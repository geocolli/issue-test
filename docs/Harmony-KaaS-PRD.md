# Harmony: Harmony KaaS PRD

# Overview

Enterprise customers want to run their apps in public cloud as well as in their own data centers in same fashion. Main use case for customers is that apps can be moved from on-prem to public cloud and vice versa. Also, the tools / services to manage these applications can be the same for various use cases – monitoring, CI/CD, deployment. Ideal scenario is having same model at multiple different levels ranging from infrastructure to PaaS service, but even if this scenario realizes partially for some use cases, customers will be able to garner tremendous value. 

In order to achieve that unified model, a common layer somewhere in that entire stack of infrastructure, Container virtualization layer, Orchestration, app platform is necessary. Technologically the common layer should be as high as possible so that underlying complexity can be masked, but when we look at market paradigm – current market at application layer is too fragmented, so it’s best to use common layer that’s pervasive in the industry. Hence, in scope of this service, Kubernetes is chosen as a common layer. 

# Scope

What specific part of problem are we solving? 

Customers want to use Kubernetes for containers, but there are many challenges in running containerized apps. Few challenges are but not limited to – 

1.  Multiple open source solutions bundling, configuration and support
2.  Provisioning of infrastructure (compute, storage, networking) for Kubernetes
3.  Configuration of Kubernetes with underlying infra services – compute, storage, network
4.  Lifecycle Management of Kubernetes

Scope is to address above challenges in Harmony KaaS, and as solution gets traction, we can expand further to other challenges.

What’s not in scope?

*   Building a PaaS solution that targets application builds, CI/CD as well as automated development and testing processes
*   Native CI/CD, monitoring, alerting capabilities in the platform – too many companies have already created compelling solutions in these area
*   **Configuration management for middleware and hosts – we believe in golden image ideology vs modify as you go plus we don’t need to play in configuration management space with the likes of Chef, Ansible, Puppet etc. (need clarification)**
*   Actual infrastructure - hypervisor layer, hardware layer is not in scope. The solution will just make calls to infrastructure layer to create necessary infra. for K8s cluster.

# Functional Requirements

There are three areas that Kubernetes-as-a-Service is targeting – 

1.  Setup orchestration and other tools (registry, IDM) for containers
2.  Make consumption of containers and K8s easy for different needs
3.  Manage container consumption, orchestration, and other tools

Notes –

1.  Some requirements have open areas where decisions need to be made based on partner solutions, customer usage etc. Those areas have been called out separately as “need more research”
2.  Also, Harmony K8s has been called as Harmony KaaS (Kubernetes-as-a-service) in some areas of this document.
3.  Each requirement is structured as priority, ranking, description, user flow, acceptance criteria, and addendum. Addendum section includes any open questions / helpful links / areas to explore etc.

## 1\. Setup orchestration and other tools (lb, registry, IDM) for containers

### Deploy multi-master K8s cluster on top of VMware HX platform

#### Priority: P0

#### Ranking:

#### Description

CX Admin needs to create a new K8s cluster with multiple masters and nodes. This cluster can be deployed on a slice of infrastructure in vCenter. The infrastructure can be defined as a combination of compute nodes (vSphere cluster), networks (VxLANs or CIDR blocks), and storage disks (data stores, NFS details). 

#### Business Driver 

Many customers use K8s as a container orchestration platform, but it’s cumbersome to get K8s deployed on different type of machines. That’s true about service provider customers as well as enterprise customers. 

#### User Flow

1.  CX admin defines a provider (infrastructure block) on which K8s cluster can be created. This provider includes compute details – vSphere cluster, resource pool with DRS enabled. (Note: no need to tweak HA+DRS policies) 
2.  CX admins will define options for node VMs for K8s cluster. These options should include – 
    1.  CPU, memory size (above certain size – vCPU-1, 2gb vRAM to vCPU-8, 52 GB of RAM) 
    2.  OVA can be hardened Ubuntu image from GKE (custom OVAs are not supported in v1) (manual configuration: customer uploads the template to data store that should be used for VMs)
    3.  storage (disk size, type – new/existing, SCSI controller – same shared storage as your template VM – need more research: Do we need non-linked clone) (number of PDs per VM - 16, max size of PD in VM - 64 TB - need to confirm with VMware/HX scale numbers)
    4.  No. of network adapters can be default=1 (to be used with Contiv VxLAN)
    5.  Size of master nodes will be pre-set as per public cloud standards - 
        1.  1-5 nodes: n1-standard-1
        2.  6-10 nodes: n1-standard-2
        3.  11-100 nodes: n1-standard-4
        4.  101-250 nodes: n1-standard-8
        5.  251-500 nodes: n1-standard-16
        6.  more than 500 nodes: n1-standard-32
3.  Provider also includes details for VM network– (pre-created VLAN, pre-created port groups and network profiles)
4.  Provider also includes details on storage – (vSphere data stores – multiple vSphere data stores need to be supported. Also, datastore for VM template and container usage can be different
5.  CX admin specifies how many K8s masters and how many K8s nodes are needed (etcd cluster default configuration is provided – default configuration is (need more research))
6.  6. <span class="Apple-tab-span"> CX admin asks Harmony K8s to deploy K8s cluster as per specifications above

#### Acceptance Criteria 

1.  Latest qualified version of K8s should be used when K8s cluster is created. Also, one previous version to latest qualified version should be supported. (n and n-1)
2.  CX Admin should be able to specify number of K8s masters and nodes that K8s cluster should hold
3.  Validation of customer inputs is done before actual deployment starts
4.  Provider configuration should also be available as configuration file – YAML (ideally) or JSON. 
5.  Customer should be able to have multiple providers
6.  Customer should be able to use same infrastructure provider for multiple clusters
7.  Pre-check to see if enough resources are available on provider as well as configurations for specified cluster parameters should be done. 
8.  Kubernetes cluster should be created with standard configuration flags (need to provide standard configuration flags information based on GKE)

#### Addendum

1.  Max. size of a persistent volume in a VM
2.  Number of network interfaces needed by HX VM
3.  Need to define ‘etcd’ cluster default configuration
4.  Need to look at standard configuration flags for K8s cluster
5.  Need to check how good is vSphere cloud provider for compute and see which K8s use cases from below will be feasible – 

1.  Status of compute nodes (alive/dead, resources being used)
2.  Which storage and network configurations are present on machines during K8s cluster lifecycle
3.  Details: [https://github.com/kubernetes/community/blob/master/contributors/design-proposals/cloud-provider-refactoring.md](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/cloud-provider-refactoring.md)

## Setup networking solution (Contiv) to be used with K8s

#### Priority: P0

#### Ranking: 

#### Description

Multiple networking services are needed to run applications successfully on K8s cluster. These networking services include overlay network with IPAM (pod level), load balancer (covered in separate requirement), and security policies. In addition, many applications require pods to communicate directly with VMs or bare metal nodes outside of K8s cluster. Cisco’s Contiv provides a compelling solution for these use cases – so Contiv configuration for K8s cluster should be automated.  There is Contiv-ACI Kubernetes driver that can be used when customer wants to run Kubernetes, and have Cisco ACI deployed in the environment. If Cisco ACI is available, customer can use Cisco ACI to connect VMs at underlay network level, and Contiv-ACI driver can be used to provide container connectivity. 

Customers also have option of connecting VMs through other mechanism besides Cisco ACI - it’s customer’s choice what networking solution to use for connecting VMs.

#### Business Driver 

Container network solution is essential for successfully running container based apps on K8s. There are multiple competing solutions in the market Calico, Flannel, Weave etc. Cisco’s solution - Contiv should be integrated completely with K8s service so that our platform can offer compelling solution at container networking layer.   

#### User Flow

1.  CX admin specifies network specifics (CIDR block, gateway, DNS) for the creation of underlay network for the cluster. New underlay network is created for each new K8s cluster.
2.  CX Admin specifies overlay network CIDR block for pod communication for particular cluster (Contiv-ACI driver or Contiv CNI driver will be used for this purpose. Also, Contiv gets deployed when Harmony K8s cluster is deployed)
3.  CX Admin specifies layer 3 constructs for communication between K8s pods and bare metal / VM nodes outside of K8s cluster 

1.  CX user creates security policies using Contiv for container workloads on K8s cluster

#### Acceptance Criteria

1.  Contiv deployment and necessary configuration is done by the service when K8s cluster is created 
2.  CX admin can specify Contiv configuration for overlay networks and security policies on as needed basi
3.  Contiv can be upgraded independently of Harmony KaaS service (p2 requirement
4.  Pod to pod communication as well as pod to external VMs/BMs communication is possibl
5.  New underlay network gets created each time when new K8s cluster gets created

#### Addendum 

1. <span class="Apple-tab-span"> Contiv will support only VxLAN mode for pod to external VM/BM communication – there is about 5-10% penalty of using VxLAN compared to direct vLAN mode. Traffic from pods is sent to external VM/BM using NAT and host network

## Setup external load balancer to be used with K8s

#### Priority: P0

#### Ranking: 

#### Description

Customers need an external load balancer to expose internal services from K8s cluster to the outside world. This external load balancer needs to be configured with K8s so it’s ready for consumption. Load balancing services need to be provided at TCP/IP layer as well as at HTTP layer. Load balancing at L3 and L7 needs to happen for east-west traffic within Kubernetes cluster as well for north-south ingress traffic. Customers are going to use Kubernetes native mechanisms - services / discovery etc. for east-west load balancing.

Node port mechanism is limited due to high range of ports and scalability issues, so we need to support load balancer service type for applications. Also, ingress controller is picking up steam so we need to support that in addition to service type ‘load balancer’. 

There are couple of more use cases where load balancer can be used 

1.  we need to give HA to K8s API server
2.  CX control plane also needs to be load balanced

So, we should see if our chosen LB solution can cover all these use cases or we may need to look at alternative mechanisms. 

Note – we are going to use Nginx for L7 load balancing, and IPVS for L4 load balancing. 

#### Business Driver 

Many customers want to use external load balancer for ingress communication. These customers want external load balancer to be part of curated stack that we are offering as K8s as a service. Plus, they also need to give HA to K8s API server. 

#### User Flow 

1.  CX admin configures Contiv desired LB
2.  CX admin creates pool of public IPs that can be used with different load balancers
3.  CX user creates a service with type ‘load balancer’
4.  System automatically assigns the public IP to the load balancer VIP and port 
5.  CX user can see public IP of the load balancer and can access app on that public IP

#### Acceptance Criteria

1.  CX user doesn’t need to do any load balancer configuration except to define service type as ‘load balancer’ in service configuration yaml file
2.  LB solution needs to be bundled with Harmony K8s service so that CX admin doesn’t need to worry about the install and configuration of LB service
3.  Customers need to have HA for K8s API server auto-configured for them.
4.  Customer should be able to see LB VIP information at K8s cluster level. (p2 requirement)
5.  Both ‘service type: load balancer’ and ‘ingress resource’ types should be supported
6.  L3/L4 as well L7 load balancing for east-west and north-south traffic should be supported

#### Addendum 

1.  <span style="color: rgb(0,0,0);">Ingress controller
    1.  <span style="color: rgb(0,0,0);">[<span style="text-decoration: underline;">https://kubernetes.io/docs/concepts/services-networking/ingress/#single-service-ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/#single-service-ingress)
    2.  <span style="text-decoration: underline;">[https://www.nginx.com/blog/nginx-plus-ingress-controller-kubernetes-load-balancing/](https://www.nginx.com/blog/nginx-plus-ingress-controller-kubernetes-load-balancing/)
2.  IPVS – [https://www.youtube.com/watch?v=KJ-A8LYriGI](https://www.youtube.com/watch?v=KJ-A8LYriGI)
3.  SPs use IPVS, HAProxy as load balancers. Also, Istio is gaining steam is the ecosystem - [<span style="color: rgb(4,51,255);text-decoration: underline;">http://blog.kubernetes.io/2017/05/managing-microservices-with-istio-service-mesh.html](http://blog.kubernetes.io/2017/05/managing-microservices-with-istio-service-mesh.html)
    IVP (SP video team) is planning to use Nginx as load balancer and ingress controller. We need to finalize on what load balancers are offered to SPs 
4.  We need to check if we need to provide support for LB at cloud provider level to create LBaaS

## Setup storage solution to be used with K8s cluster

#### Priority: P0

#### Ranking: 

#### Description

Customer needs to use persistent storage with K8s app deployments. In enterprise environment, customers usually have multiple storage back ends that they need to use for storage – NFS, storage arrays etc. We plan to use HyperFlex as the vSphere storage provider. i.e. K8s will use vSphere datastores with the backend storage being provided by HyperFlex. In order to achieve that, HyperFlex vSphere storage driver for K8s will be used.  

#### Business Driver 

Even though micro-services are all rage, still applications need to store data somewhere, and that’s where persistent volumes can be used. 

#### User Flow

1.  Harmony K8s service does necessary configuration of HyperFlex vSphere storage provider in Kubernetes 
2.  CX user requests a persistent volume with size and type in pod spec when CX user deploys an app in K8s
3.  K8s makes a call to HyperFlex vSphere driver to create a volume for the CX user. Note – this volume needs to be attributed to the same CX user
4.  App gets deployed with the persistent volume requested in K8s
5.  When a cluster is deleted, data may still persist – Harmony K8s service won’t delete any volumes or clean up any data in data stores being used. Note – need to check what happens for dynamic provisioning on cleanup side. 
6.  Note – data HA – so if node with datastore goes down, you make sure that other nodes have access to that datastore. 

#### Acceptance Criteria

1.  New dynamic storage provisioning model should be supported
2.  Storage driver from HX platform should be supported
3.  Post-storage provisioning plugin model is supported

#### Addendum 

1.  <span style="color: rgb(0,0,0);">Persistent volumes primer on K8s - [<span style="text-decoration: underline;">https://kubernetes.io/docs/concepts/storage/persistent-volumes/](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
2.  Need to analyze limitations of vSphere driver and see how that impacts requirements
3.  Note - When HyperFlex cluster is setup, HyperFlex storage diver will be configured for K8s clusters that will be created later.

## Setup HA and security (certs) for K8s masters

#### Priority: P1

#### Ranking: 

#### Description

Customers need to provide HA to K8s API servers when they are running enterprise or service providers workloads on K8s. Also, as per data center policies, all the communication between nodes needs to be secure – this is usually achieved by the usage of certificates. CX admins need to replaces these certificates in certain intervals to maintain compliance in data center. Life of a CX admin can be made easier if the workflow to request and replace certificates can be automated.

#### Business Driver 

Providing HA to masters is a necessity when an enterprise customer runs a K8s cluster. Also, if we can allow customers to replace security certificates, that automation can provide additional value to them.

#### User Flow

1.  If CX admin is creating multiple masters K8s cluster, he is given an option to choose whether he wants HA for cluster or not. 
2.  If CX admin chooses HA for the cluster, load balancer (needs to decided HAProxy or Nginx or something else) is deployed and it’s used to provide HA to K8s master and API server (need more research - if alternate mechanisms should be used instead of LB)
3.  If CX admin adds more K8s masters to load balanced (masters) K8s cluster, all additional masters get added to load balancing pool automatically
4.  CX admin specifies CA server for requesting certificates for K8s master or CX admin can choose self-signed certificates. If left empty, CX admin has to either setup certificates by himself or communication between K8s nodes is unsecured 
5.  CX admin can go to a K8s cluster and indicate that now there is time to replace certificates, and all master / node certificates will be refreshed automatically. 

#### Acceptance Criteria

1.  CX admin should be able to use a load balancer (or alternate mechanism) for HA without much configuration on his end
2.  CX admin should be able to validate CA server URL / credentials when specifying it in the solution
3.  Security certificates functionality should be optional
4.  CX admin should be able to scale in / scale out load balanced K8s cluster

#### Addendum

1.  Open question – which LB should we use to give HA to K8s masters?
2.  Note – v1 will have self-signed certs, and it is customer’s responsibility to work with CA
3.  Need to look at alternative mechanisms than LB for HA of K8s API servers, and see if those solutions are acceptable

## Configure K8s to work with pre-deployed registry solution

#### Priority: P2

#### Ranking: 

#### Description

There are two levels to this requirement of using a registry with Harmony K8s service. 

Level 1 – KaaS helps customer to set up a registry and does necessary configuration so that images can be pulled from / pushed to that registry from K8s

Level 2 – In addition to level 1, there is also common authN and authZ between registry and KaaS service that goes across users, roles, and projects. 

Current scope of the requirement is just level 1\. We may tackle level 2 of registry usage in future versions of KaaS service

Single registry is used per Harmony K8s service. We need to choose one registry solution to offer as part of bundle (more research needed)

#### Business Driver 

Most of competing solutions in the market offer automation of registry setup. Some services even offer registry as part of stack e.g. Docker – DTR, CoreOS - Quay

#### User Flow

1.  CX Admin has option to add registry to an AZ if container service is enabled for that particular AZ
2.  CX Admin provides configuration parameters related with registry setup (url, IP etc.) (more research needed)
3.  CX Admin sets up users, roles, projects in registry (out of scope for KaaS – this is level 2 requirement)
4.  When user is deploying pods in K8s cluster, he specifies image in pod spec with image access secrets (username / password or TLS based authentication)

#### Acceptance Criteria

1.  CX Admin should be able to use other registry solutions with KaaS service if they desire
2.  CX Admin should be able to use different images and repositories with registry 
3.  CX Admin should be able to perform CRUD operations on registry solution

#### Addendum

1.  Workaround for the customer if we don’t build this functionality is – manual creation of secret for external registry URL and username/password. Including that secret with image information to access that image directly from registry solution. 

# 2) Make consumption of containers and KaaS easy for different apps / needs

## AuthN and AuthZ for container service

#### Priority: P0

#### Ranking: 

#### Description

We need to think about authentication and authorization of K8s at two levels – a) Harmony K8s service b) Kubernetes clusters (customer clusters)

Harmony K8s service: 

Authentication - Harmony K8s service users should come from LDAP backend – AD. So, anybody on-boarded to Harmony K8s service is able to perform different operations as per the role 

Authorization – Harmony K8s should support two roles at container service level - 1) CX Admin 2) CX user. CX admin is responsible for creating and managing K8s clusters, and doing user management. CX user just comes to CX service to know which clusters are available to him / her.

Kubernetes clusters: 

Authentication – CX user / CX admin should come to Harmony K8s service and can download token for particular cluster to access the cluster. CX admin should be able to perform CRUD operations on users for K8s cluster(s). K8s cluster users are same users as Harmony K8s users. 

<span style="color: rgb(0,0,0);">Authorization - Authorization / roles in K8s cluster itself will be provided through native RBAC model in Kubernetes. No sync of RBAC model in scope at least for v1 use cases of the track. Idea is that people will sync just AD groups - so users will be the same

#### Business Driver

Any service cannot be used in enterprise world without authentication features. So, authentication is a no brainer. 

At least basic authorization is needed in most of enterprise systems. If we look at our competition as container service, most of competitors offer pretty mature RBAC models on authorization side. 

#### User Flow (need clarity / work in progress)

1.  First CX admin can be added during the installation, and that CX admin can onboard other CX users / admins
2.  CX admin adds multiple service accounts 
3.  CX admin creates a K8s cluster with all the configuration and adds new CX users to that cluster
4.  CX admin adds another CX admin to the cluster who can add/ delete CX users or add more CX users to the cluster 
5.  CX admin / user can also login to created K8s cluster with same credentials – any user that’s entitle for particular K8s cluster, sees an option to download a token to access K8s cluster. 

#### Acceptance Criteria (need clarity / work in progress)

1.  CX admin should be able to map multiple AD groups to the service. 
2.  CX user can be part of multiple K8s clusters
3.  K8s cluster can have multiple CX Admins
4.  CX Admin can add another user as CX Admin to the cluster, and new CX Admin can perform CRUD operations on users

#### Addendum

1.  How user gets access to K8s cluster via token/certificate – need to define UX flow with mockups / wireframes

## Setup communication between container pods and external VMs / BMs

#### Priority: P2

#### Ranking: 

#### Description

Many apps are containerized partially. Lot of enterprise companies shy away from containerizing databases or RabbitMQ servers, so app is in hybrid deployment format where part of app is in containers and part of app is deployed on VMs / BMs. Similarly, in SP market, some services run on containers whereas some services run on bare metal nodes. 

Contiv is our offering for L3 connectivity between containers and outside VMs / BMs. It will be used with container service for communication between containers and non-containers. 

#### Business Driver 

It’s necessary to provide pod to outside VMs / BMs communication due to hybrid nature of app deployments on VMs and containers. 

#### User Flow

< Standard Contiv Flow> (need to define)

#### Acceptance Criteria

1.  Manual steps in the process – e.g. creating vxLAN, BGP configuration on host need to be automated as much as possible
2.  Necessary validation should be provided to verify customer inputs and throw error / warnings about particular input
3.  VM L3 connectively should also be offered in Contiv as many enterprise apps run VMs for non-container services

#### Addendum

1.  <span style="color: rgb(0,0,0);">L3 connectivity steps in Contiv - [<span style="text-decoration: underline;">http://contiv.github.io/documents/networking/bgp.html](http://contiv.github.io/documents/networking/bgp.html)
2.  Note – we can support container to non-container communication through Contiv VxLAN model, it is not support through VLAN mode. 

## CLI / API / UI for Harmony K8s service

#### Priority: P0 (API/UI), P1 (CLI)

#### Ranking: 

#### Description

Harmony K8s UI/CLI/API will be used for K8s cluster creation and lifecycle management. Native K8s UI/CLI/API will be used for actual workload deployments. This requirement pertains to CLI / API / UI for Harmony K8s service, and not for native K8s CLI/UI/API. 

Harmony K8s service needs to have a UI that can be deployed and consumed independently. We should be launch this UI from HX UI, and UI should have same look n feel as HX UI. 

#### Business Driver

Customers need consumption mechanisms – CLI / API / UI for the functionality that Harmony K8s will offer

#### Acceptance Criteria

1.  Harmony K8s service should have same look n feel as rest of HX platform UI 
2.  API of Harmony K8s service should cover CX Admin interactions as well as CX user interactions in the API completely 
3.  Harmony K8s UI can be launched from HX UI. 
4.  Kubernetes UI (Kitematic) can be launched from Harmony K8s UI. 

#### Addendum

1.  Open Questions - Does HX platform have CLI? Does Harmony K8s service need to have CLI? 
2.  Manage container consumption, orchestration solution, and other tools

## Add / remove K8s nodes, K8s masters (‘etcd’ nodes)

#### Priority: P0

#### Ranking: 

#### Description

Enterprise customers always want to change cluster size of their K8s clusters as workloads increase or decrease. This change in sizing can happen at multiple levels – K8s masters, etcd servers, and K8s nodes

a) Add / remove K8s nodes

Ideally, cluster admin should be able to specify range for K8s cluster – e.g. 3 to 7 nodes, and cluster should auto-scale based on scheduling pipeline. We can also do this requirement in phases where first phase is manual add/remove of K8s nodes and second phase is actual auto-scaling. 

Manual removal of K8s nodes should be possible where customer chooses exact node to be removed, where first node is marked as not available for scheduling pods, and once workloads are moved completely then node is removed from K8s cluster and VM is deleted. 

b) Add / remove K8s masters and ‘etcd’ cluster nodes

(note - p2 requirement as just adding K8s masters is alpha level functionality in K8s itself)

Ideally as cluster size grows, you can just bump up the size of K8s masters, but our ‘etcd’ cluster is part of K8s masters. So, we need to give an option of adding K8s masters. 

When cluster size is changed, KaaS service should do necessary configuration so that K8s cluster can function flawlessly.  

#### Business Driver

Most of enterprise customers need to change their cluster size frequently.  

#### User Flow

1.  CX admin chooses scale out operation for specific K8s cluster
2.  CX admin specifies number of additional master servers and nodes that need to be added. 
3.  CX admin request is validated with quota available for container deployment and if request is processed successfully, scale out operation is performed
4.  CX admin chooses scale in operation for specific K8s cluster
5.  CX admin specifies new size of the cluster in terms of number of masters, etcd servers, and nodes
6.  CX admin can also choose specific nodes that need to be removed from the K8s cluster, and those nodes are removed from the cluster. 

#### Acceptance Criteria

1.  Scale out / scale in operation configuration is required to be validated
2.  Added masters / nodes should be configured to work flawlessly in the K8s cluster (cert setup etc.)
3.  Removed masters / nodes from cluster should have options where node is just removed from the cluster or where node is removed from the cluster and also underlying VM is nuked. (these two stages are needed so that customers can troubleshoot particular node)

#### Addendum

1.  Need to investigate etcd server management more
2.  Need to investigate cluster auto-scaling – current requirement is manual scale out requirement where nodes are being added manually, but in future version we should look at automatically growing or shrinking Kubernetes cluster. 

## Monitoring and logging solution controllers

Note – Bare minimum requirement here is giving reference designs for Prometheus and ELK stack. 

#### Priority: P2

#### Ranking: 

#### Description

We will choose most popular / technologically advanced monitoring solution and logging solution, and create configurations and data collectors for Harmony K8s service initially. We need to consider a few factors when choosing right solutions – 1) technology 2) market share 3) vendor / competition 4) support model 5) our business direction etc.

Monitoring solution of choice – Prometheus

Logging solution of choice – ELK 

[only exporter pieces will be covered]

#### Business Driver

When customers are running containerized apps, they need to monitor container usage – resource usage, construct (pods, services) scale, and also create alerts for threshold reach, rogue behavior, failures etc. 

All of micro-services and containers are running pieces of application independently. In order to troubleshoot application, all of these logs need to be aggregated in one place, and also logs need to be forwarded to log analytics solution.

#### User Flow

1.  Harmony KaaS auto-configures some of popular solutions to be used with created K8s clusters
2.  Customer can also create some manual configurations for additional toolset that might not be most popular with Harmony

#### Acceptance Criteria

1.  Prometheus, ELK stack needs to be supported
2.  Data about CX control plane health and logs needs to be forwarded to monitoring / logging solutions
3.  Data about K8s masters / nodes (metrics based on GKE) needs to be forwarded to monitoring / logging solutions
4.  Actual K8s objects data (pods, services etc.) will be forwarded to monitoring / logging solutions by the customer
5.  Reference configurations should be made available for popular tools in the documentation.

Addendum

1.  Give sample configurations to customers for popular tools
2.  Note – CX service in the platform will be monitored through HX platform – need to explore what are possible monitoring solutions available in HX platform

## Install / upgrade / uninstall / management of Harmony K8s control plane

#### Priority: P0

#### Ranking: 

#### Description

Harmony K8s needs to be installed / upgraded / uninstalled independently for customer. 

Install: 

Harmony K8s will be deployed as OVA, and this OVA can be deployed from HX UI or installer. Also, Harmony K8s control plane needs to be deployed in VMs on separate nodes due to ‘etcd’ cluster requirements, so we need to deploy control plane VMs with anti-affinity. 

Configure:

Necessary configuration of Harmony K8s control plane will be done. HA for Harmony K8s control plane will be provided through load balancing solution.  HA/DRS/backup etc. for Harmony control plane VMs will be provided through vSphere policies and mechanisms. 

Monitoring:

Customer should be able to monitor health of Harmony K8s control plane. We are going to bundle Prometheus with Fluentd for control plane monitoring. 

Upgrade: 

Upgrade of control plane K8s will be done in a same way as customer’s K8s clusters (please refer above). Upgrade of actual bits of Harmony K8s service inside K8s cluster will be done using blue/green upgrade methodology. OS upgrade will be done in blue/green deployment manner also using LB. 

Uninstall: 

We directly destroy VMs on which Harmony K8s control plane is deployed. 

#### Business Driver 

Any piece of software needs to have an easier and automated way to install, configure or remove it. 

#### User Flow

Need more research – need granular workflows for each of areas above. 

#### Acceptance Criteria

1.  IT Admin can install/uninstall/upgrade KaaS service with HX platform
2.  Control plan of Harmony K8s service should run in minimal footprint 
3.  (n) and (n-1) version should be supported for upgrade

#### Addendum

# Non-functional Requirements

## Scalability and performance of Harmony K8s Service

Current plan is to support K8s cluster up to 1000 nodes. Bare minimum, we need 3 masters and 2 nodes to have a meaningful cluster, so we should be able to support up to 200 clusters theoretically -  what we have seen typically in customer environments is that there are no more than 80 K8s clusters. 

Note - Kubernetes 1.7 scale numbers - No more than 5000 nodes - No more than 150000 total pods - No more than 300000 total containers - No more than 100 pods per node

## Version Matrix

| Area | Software | Version|  
| --- | --- | --- | --- | --- |
| OS | Ubuntu (GKE) | 16.04 | 
| Orchestration | Kubernetes | 1.7.3 and 1.6.8 (default) |
| Networking | Contiv, Contiv-ACI | 1.2, ACIv? |
| Hyper converged Infrastructure | HyperFlex | TBD |
| Hypervisor | vSphere | Same as HyperFlex |
| Load Balancer (L7) | Nginx | TBD |
| Load Balancer (L4) | IPVS | TBD |



*   We are going to support latest qualified versions of K8s – n and (n-1) (most probably 1.7.3 and 1.6.8 but it may change depending on qualification hurdles)