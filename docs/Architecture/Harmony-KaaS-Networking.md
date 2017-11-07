# Harmony : Harmony KaaS Networking

# Harmony KaaS v1.0 Networking Plan Summary

*  Current priority is for harmony K8S deployment on UCS Hyperflex (HX) private cloud running vSphere virtualization.
*   Similar design with appropriate changes will be used for VMware, Vaquero and OpenStack use cases.
*   Physical + Container networking plan (current POR direction):
    *   Standard L2-L3 physical networking (non-ACI) + Contiv networking
    *   ACI physical networking + Contiv-ACI
*   Load balancer plan (current POR direction):
    *   L7 N-S load balancing: kubernetes ingress w/ NGINX
    *   L4 N-S load balancing: IPVS, ACI PBR
    *   Other solutions under investigation

# Overall HX topology view

The following diagram represents an overall view of a Hyperflex deployment running KaaS. This shows a collection of 3 separate kubernetes clusters of which the first is the k8s stack running the KaaS control plane and the 2nd and 3rd are examples of tenant kubernetes clusters provisioned on demand via the KaaS control plane services. All 3 of these clusters run on vCenter VMs within the Hyperflex deployment. For the KaaS control plane cluster, we will only use 3 VMs that will each function as both master and worker/ node from a kubernetes cluster perspective. In the tenant k8s clusters, we will always separate master node VMs from worked node VMs (these are represented as the M and N nodes respectively in the diagram). In the initial release, one vCenter port group will need to be pre-provisioned per kubernetes cluster as shown for the control plane cluster and Blus tenant k8s cluster in the diagram. In the v2 release (or possibly a stretch goal for the v1 release), we will also support moere than 1 portgroup per tenant cluster which can then be utilized to have separate underlay networks for the control plane versus the data plane for the tenant clusters. All these port groups will also terminate into an L3 gateway which will be either a physical L3 router (in an HA pair configuration) or by a distributed L3 gateway within the interconnect TOR switching fabric itself. Note that within each of these 3 example clusters will run a separate contiv topology and this shall be described in the following sections of this document. For now, we are just describing the overall view at a Hyperflex cluser level.

Note: To install the control plane kubernetes and KaaS services, no external networking reachability will be necessary (i.e. air-gapped deployment). These VMs and the kubernetes services within, shall be launched from an OVA deployed through the vCenter provisioning tool of this Hyperflex/ VMWare cluster. Reachability to the public internet will be required to install the tenant kubernetes. A future version of the solution shall also allow for an air-gapped deploy option for the tenant kubernetes clusters.

![](../images/192170421/204832950.png?height=250)

The next diagram illustrates the IP addressing and endpoints exposed by the different kubernetes clusters within an overall hyperflex cluster. In general, we will use private IP addressing for all the containers, pods and service IPs within each kubernetes cluster. The details of the pod/ contriner IP address allocation are discussed in the contiv networking section. Hence only the minimal number of endpoints that need external reachability will have externally routable IP addresses. For the KaaS control plan cluster, only a single endpoint will be exposed and this will be for the KaaS/ WFM api service itself as shown. This endpoint will be a virtual IP with HA and L7 load balancing mnaged via a combination of keepalived and kubernetes ingress+nginx as shown. (additional details on opeation of nginx with kubernetes ingress are described in the nginx section of this document below). For the tenant k8s clusters, 1 vip for the tenant kubernetes api itself and 1 or more vips for the exposed services that we will offer for applications running on the worker nodes of that tenant cluster. The HA for the tenant k8s api server shall be addressed using simply keepalived whereas for the application endpoint(s) it shall be a combination of keepalived + k8s ingress-nginx.

![](../images/192170421/204734560.png?height=250)

In the next few sections, we focus on the networking design and requirements specific to a single kubernetes cluster (control plane or tenant) within the overall HX cluster.

# Harmony KaaS 1.0: K8S container networking + physical networking

Options:

1.  Deploy any container platform with Contiv independently on the Nexus/ACI (overlay VXLAN, non-overlay VLAN, IP) (currently available)
2.  Contiv integration w Cisco ACI (currently available)
3.  ACI Network plugin for K8S and OpenShift with Native Security policy support (next release)

## Contiv + non-ACI Option

*   <span style="color: rgb(0,51,102);">GA v1.0: </span><span style="color: rgb(0,51,102);">Only a single mode of Contiv to be supported viz. L3 VXLAN mode over the top of underlay L2 network</span>

*   <span style="color: rgb(0,51,102);">Post GA: Additional Contiv non-ACI modes may also be supported such as L3 BGP and L2 VLAN</span>
*   <span style="color: rgb(0,51,102);">In case of HX deployments</span>

    *   <span style="color: rgb(0,51,102);">Underlay network is a VMWare port group + external gateway router</span>

    *   <span style="color: rgb(0,51,102);">Underlay implemented on a non-ACI physical fabric</span>

    *   <span style="color: rgb(0,51,102);">Underlay can also be implemented on an ACI fabric providing standard L2/ L3 to overlay Contiv networks</span>

*   <span style="color: rgb(0,51,102);">All paths supported</span>

    *   <span style="color: rgb(0,51,102);">Container to container (including kubernetes services)</span>

    *   <span style="color: rgb(0,51,102);">Container to VM/ host nodes</span>

    *   <span style="color: rgb(0,51,102);">Container to public internet</span>

    *   <span style="color: rgb(0,51,102);">Public internet to container </span>

## Single k8s tenant cluster: logical view

Refer to the picture below ... logically, there are 3 categories of kubernetes cluster nodes (master nodes, compute/ worker nodes and edge/ infra nodes)

Edge/ infra nodes are basically worker nodes on which one may also schedule edge or infra applications such as a container load balancer. For simplicity, initially we assume that all worker nodes are also edge nodes.

The picture also shows that there is a single L2 underlay network at the VM level and intra-cluster container traffic will run in a VXLAN overlay Contiv network on top of this L2 underlay. Host management plane traffic as well as kubernetes control plane traffic will also run on this same L2 network initially. Traffic sent by a pod to a destination that is not also a kubernetes pod, will use vlan encapsulation of the L2 underlay network as shown. Additional data path details are explained in following pictures.

![](../images/192170421/192197352.png?height=250)

The figure below shows the internal view of the implementation of Contiv and its components when using the L3 VXLAN mode as poanned for Harmony K8S GA release.

Pod A1 in this example has a container/ pod IP address of 20.1.1.4 and is connected via a veth pair to the Contiv OVS switch's contivVxLanBridge instance. When this pod sends traffic to another pod within the same kubernetes cluster but on another node of the cluster, the OVS will perform a flow match and find that a vxlan tunnel is needed to reach the destination pod IP. Note that population of flows and forwarding information in OVS is performed in the control plane by the Contiv agent on the node since contiv maintains global knowledge of where every single pod resides within the cluster, what its IP address is and VXLAN tunnel endpoint associated with it. This traffic then leaves the node as a vxlan encapsulated packet via the host IP stack of the node. Alternately if the pod needs to send traffic to a destination IP address that is on the host network subnet itself, it will end up matching an OVS flow that sends the packet internally via the contivH1 internal virtual interface from where it gets forwarded by the regular host IP routing stack. Finally, if the destination IP is none of these, it is understood to belong to the public internet and in that case an OVS flow directs it to the contivh0 interface from where it goes through the host IP routing stack, gets Source NAT'ed to the hosts own IP address and goes out SNAT'ed to the public internet destination (via the external physical L3 gateway assigned to the HX cluster as a whole). 

![](../images/192170421/192197341.png?height=250)

The following picture shows these data paths again but this time from the external multi-node perspective.

![](../images/192170421/192197323.png?height=250)

Here VM-X and VM-Y are two kubernetes cluster nodes running within UCS servers A an B respectively (there is an ESXi hypervisor and corresponding VMWare Distributed Switch per server for Virtualization and VM networking). The traffic shown via the red dashed line represents pod to pod or pod to kubernetes service traffic within the cluster. It uses VXLAN encapsulation which then runs over the L2 VLAN to which VM-X's eth0 interface is connected in order to go over to VM-Y and eventually pod2\. The traffic in the blue dashed line represents pod to VM or pod to external IP traffic and it gets SNAT'ed via the host IP stack as noted earlier and eventually uses the VM's L2 network to get to another VM or to the L3 gateway router to reach a public internet destination address.

## Contiv-ACI Solution

The earlier solution was the non-ACI Contiv solution. We now describe solution 2 which will be used when the Harmony solution runs over an ACI fabric. In this case we will use the Contiv-ACI kubernetes plugin developed by the INSBU team. This is a different plugin from the non-ACI Contiv plugin described earlier although both plugins are marketed under the Contiv name and the code will reside (eventually) within the same github repo.

The overall workflow is briefly described below. In short, there is a 1-time pre-configuration of the ACI fabric to get the HX cluster working for host management traffic as well as setting up VM networking support to allow L2 networks/ VMWare port groups to be created within the VMWare cluster. Note: a separate L2 network is setup for the KaaS control plane kubernetes stack and a second one is setup for the tenant facing kubernetes stack which is primarily being discussed here. Next, on ACI, a VMM domain needs to be configured by the ACI admin to mark the VMs and port group VLAN to be used for the tenant k8s stack's underlay. Harmony K8S installer is then run to install the tenant k8s cluster as well as a contiv-ACI/ opflex container needed to connect the opflex agent running on the cluster VMs with the APIC control plane. Once the harmony k8s stack is installed, standard kubernetes networking policies can be configured within the standard kubernetes api and the contiv-ACI plugin will get notified of these, will configure filter rules within its OVS forwarding tabels to implement these policy rules and also notify the APIC controller  when appropriate.

<More details to be added on the contiv-ACI operation and workflows as the contiv-ACI solution gets completed and fully delivered to the Harmony K8S team, this is a summary of the workflow until then> 

*   <span>ACI fabric 1-time pre-configured</span>
*   L2 underlay network pre-created in ACI for k8s compute nodes
    *   In case of Hyperflex, underlay is a vSphere port group
    *   Underlay pre-configured to provide L2 trunk VLANs
*   L3out routing pre-created in ACI
*   Harmony KaaS installs K8S cluster + ACI/ opflex container
*   Once K8S cluster is up, devops admin interacts only with Kubernetes CLI/ API.
*   <span style="color: rgb(103,103,103);">K8S traffic runs as VLAN or VXLAN traffic on top of </span><span style="color: rgb(103,103,103);">pre-created ACI L2 underlay network </span>

## Physical Fabric Topology

![](../images/192170421/192170860.png?height=250)

## BOM/Hardware

*   ACI 3.0 fabric hardware and software
    *   Note: ACI 3.0 does not yet support Kubernetes on VMWare VMs
    *   Initial testing for Kubernetes + VMWare + ACI will use engineering builds
    *   ACI 3.1 will be first official ACI release to support Kubernetes plugin over VMWare VMs
*   Nexus 9300 EX/ FX leaf switches
*   Nexus 9300/ 9500 spine switches per ACI 3.0 requirements
*   Exact hardware model details documented in testbed infra builds section

# Harmony KaaS 1.0: K8S Load Balancer

We now switch to another aspect of Harmony networking viz. container load balancing service.  There is a wide range of container load balancing options that can be used with kubernetes, some of these are listed here but we do not go into deails of each one in this document. Our focus here will be on the last two namely an L7 load balancer using Kubernetes Ingress feature combined with NGINX and an L4 load balancer using IPVS. Other flavors of load balancers are also needed for Harmony k8s (including ACI PBR for L4 load balancing incase of ACI and istio/ envoy based L7+L4 load balancing in the near future). However for now we restrict this wiki page to the scenarios of immediate top priority starting with the L7 load balancer via Kubernetes Ingress and NGINX. 

## K8S Stack: Container load balancing options

*   Multiple options - (Pros/ Cons discussed separately
    *   Separate independent load balancer
        *   Containerized or appliance
    *   Kubernetes Node Port
    *   KubeProxy
    *   Openshift Router (with HAProxy/ F5 ...)
    *   Kubernetes Service type LoadBalancer L4**
        *   **IPVS**
        *   **ACI PBR**
    *   **Kubernetes Ingress (L7/ L4)**
        *   **With NGINX**
        *   **With istio/ Envoy/...**


The following picture shows how the data path will work when doing L7 load balancing via Kubernetes INgress + NGINX. Here,the cluster admin runs a shared load balancer service  that is then available to all k8s users. The load balancer service is also instantiated via NGINX running inside a kubernetes container itself. Applications run yaml manifests that include Kubernetes Ingress objects which the underlying cluster maps to the already pre-configured NGINX load balancin service. In the example below, two containerized application services (tea and coffee) share a common external virtual host name (cafe.example.com) but require http traffic sent to two different URL paths within this host to the two respective services. This is performed by Kubernetes cluster automatically mapping the URL paths specified within the Kubernetes Ingress objects to NGINX configuration rules that implement the required load balancing. Further more, the backends of these L7 endpoints are themselves kubernetes services running multiple replicas (for exampel 3 replicas of the tea and coffee services erspectively in this example) and NGINX+Ingress will automatically track the health of these backends and keep the load balancer configuration rules updated to only send traffic to healthy endpoints. There is more detail on Kubernetes ingress operations than summarized here. The reader is referred to the Kubernetes.io web site documentation for more information.

## K8S Ingress with NGINX

![](../images/192170421/192171104.png?height=250)

### HA Topology

In order to implement High Availability for the above K8S Ingress + NGINX load balancer solution, the initial GA plan for Harmony k8s will be to use keepalived to implement a active-standby virtual IP address that represents the front end of the load balancer (and is separately pre-programmed into DNS to map the exposed L7 URLs to reach this Virtual IP address).

![](../images/192170421/192198749.png?height=250)

### Other Design Details

*   HA for VIP
    *   Separately provisioned Keepalived initially
    *   Integrated via extensions
*   Load balancing/ HA for tenant Kubernetes API endpoint
    *   In GA/ v1.0 release there will be no load balancing needed for the kubernetes API 
    *   “Load Balancer As a service”:
    *   NGINX in an isolated services namespace
    *   Provided via Kubernetes Ingress objects as a service to tenants
*   DNS interface:
    *   Out of band
    *   Auto external DNS optimization
*   Extensions via NGINX annotations


### Harmony KaaS Control Plane load balancer

Finally, we show here that the same load balancing solution that is planned for the tenant k8s stack can also be used for the control plane kubernetes stack as well. Currently this is not yet part of the confirmed plan for the control plane kubernetes stack but is under active consideration and this document will be updated once the plan gets confirmed. (note the picture contains openstack components that only apply when Harmony K8S is run in a Metacloud + Harmony environment. 

![](../images/192170421/192171173.png?height=250)
