# Harmony : Harmony KaaS Design Document


# Objective

Customers need a Kubernetes-as-a-service (KaaS) platform with various day-1 to day-N management of containers. We need a set of bare metal or virtual machine nodes to deploy Kubernetes clusters. HyperFlex provides the IaaS platform to provision compute nodes as well as other objects required to support the Kubernetes cluster(for storage etc.) 

There are two personas for the KaaS platform. A KaaS admin who manages the Kubernetes clusters and KaaS user who directly runs containers on Kubernetes clusters.

Our primary customer is the KaaS admin (who's possibly the IT admin) and their requirements are:

1.  **Cluster management:** Deploy and do life cycle management of Kubernetes clusters
2.  **Storage management:** Management of persistent container volumes on a persistent storage
3.  **Network management:** Management of container networks
4.  **Image management:** Manage the images for containers. This includes managing the storage backend for the images
5.  **Monitoring:** Monitor the workloads running in containers and provide a way to analyze the data. 

For end-user, the primary requirement is to keep the workflows the same as native Kubernetes workflows. Note that the management extends beyond installation and configuration of container clusters. We need to provide day-N management and operations to make it easy for the KaaS admin to manage and operate a Kubernetes-as-a-service platform for his/her end-users.

# Basic Assumptions

*   KubeSpray will provide the infrastructure for managing deployment of the Kubernetes cluster
*   vSphere Cloud Provider will be used to manage the node lifecycle
*   KaaS deployments will have external Internet access for the download of artifacts during installation and upgrade

# Detailed Design

Logically, a KaaS domain contains compute nodes (bare-metal or VM) to form a container cluster, as well as additional nodes that might be needed to support the container stack in the domain (for storage etc.) This domain is deployed and managed by KaaS Control Plane also known as KCP (or CX) for short.

KCP service itself runs as a set of scalable Kubernetes services on top of a control K8S cluster.

The individual components of KCP service are shown in the following figure.

![](../images/195559566/195526802.png?height=250)

*   **WFM** - workflow manager. Exposes REST API endpoint and provides fine-grained APIs as well as some APIs that execute workflows (which are combination of multiple APIs). WFM is the primary API entry point for the UI and CLI(in the future). When WFM is setup as more than one pods, the service needs to be Load Balanced using an external mechanism. For phase I, NGINX will be used for this purpose.
*   **SM** - state manager. Owner of all the state in KCP. It's the only service that can talk to a SQL data store (e.g., a mysql database), owns the state and owns the schema. SM also handles the authentication and authorization logic.
*   **SDDC** - software defined data center for containers. Provides abstraction for an IaaS backend (e.g., Openstack and vSphere), runtime/orchestration (k8s, Docker) abstraction and OS (Linux, Windows).

KCP service consumes underlying infrastructure components, such as VMs or bare metal machines, networks, storage etc via an infrastructure provider, such as OpenStack and vSphere, to setup and manage container clusters, as shown below:

![](../images/195559566/195494101.png?height=250)

# KCP Deployment

KCP will be deployed as Kubernetes Services (wfm, sm & corc) on a control cluster. The control cluster will be deployed as a Virtual Appliance on HyperFlex (on a vCenter cluster of minimum 3 nodes for HA). KCP will be provided as a 3 VM virtual appliance with a K8s cluster running in a 3 master/worker configuration. KCP will be packaged as a helm chart to allow deployment on a smaller or larger K8s cluster as required and also to allow easy customization. Load Balancing and HA of the KCP API and UI will be provided using an ingress controller (for phase I).

KCP deployment will be launched using HX Connect at the end of the HyperFlex bring up.

# KCP Authentication

KCP will allow local users or AD users.

Current implementation allows the KCP admin to configure an LDAP server that will be used for authenticating AD users and getting AD group information for authorization. KCP will redirect login requests to the AD server and generate a JWT token with the authorization claims.

# KCP Authorization

There are 3 primary scenarios for authorization. The final design is TBD. Current implementation assumes option 1 below:

1.  All KCP users are AD users and will be authorized to specific infra resources by a KCP admin who creates a cluster and authorizes a local user/AD group access to the KCP cluster. KCP admin uses a service account to authorize against the backend.
2.  All KCP users are AD users and will be authorized to specific infra resources by a KCP admin who creates a resource group containing a vSphere cluster/resource pool and authorizes a local user/AD group access to it. KCP users can create the K8s clusters under those resources in a self-service manner
3.  All KCP users are vSphere users and can create cluster and use them just as the KCP admins can. They are authorized to vSphere resources using SSO.

![](../images/195559566/195592345.png?height=250)

# Tenant Kubernetes Cluster Deployment

Kubernetes clusters will be created in a multi-master configuration. The following are the design choices for Phase I:

*   **<span class="inline-comment-marker" data-ref="33434b0b-392d-4507-978a-310a70e47f60">Provisioning** <span class="inline-comment-marker" data-ref="33434b0b-392d-4507-978a-310a70e47f60">– VMs will be provisioned as clones (linked clones where possible) from the Ubuntu 16.04 VM template that is imported during KCP installation or later. Provisioning will be done using govmomi API. User will have an option to select the Datastore, Network and vSphere Cluster or resource pool.
*   **<span class="inline-comment-marker" data-ref="33434b0b-392d-4507-978a-310a70e47f60">Deployment** <span class="inline-comment-marker" data-ref="33434b0b-392d-4507-978a-310a70e47f60">- Kubespray will be used to deploy the Kubernetes clusters as a multi-master HA cluster. The master nodes will be separate from the worker nodes and there will be 3 master nodes. Exact configuration options are TBD.
*   **Networking** - Contiv will be setup in VXLAN mode for Phase I and will support pod to pod, pod to external network and host to pod access.
*   **Cloud Provider** - vSphere cloud provider will be configured to allow for faster compute convergence for k8s api servers.
*   **Logging and Monitoring** - KCP will use fluentd for monitoring the logs from a cluster which can be redirected to an ELK stack. KCP will allow the option to start a prometheus server on the K8s Cluster. 
*   **Load Balancing** - For MVP we plan to provide L7 load balancing only using NGINX.
*   **Authentication and Authorization** - KCP will provide client cert based authentication for the K8s clusters in Phase I. AD integration will be done at a later phase.

# Kubernetes Cluster Life Cycle Operations

KCP will support the following Kubernetes lifecycle operations.

*   Add Node
*   Cordon/Drain Node
*   Delete Node
*   Upgrade K8s Version

The following additional Life Cycle Operations will be supported at a later phase.

*   Kubernetes Certificate updates
*   Node Operating System Upgrade

# KaaS Installation and Upgrade Workflows

A KaaS deployment consists of the following components:

1.  HX Server Hardware (beyond scope of this document)
2.  Networking Hardware (beyond scope of this document)
3.  Control Plane
4.  Tenant Clusters

The control plane consists of 3 VMs and is further broken into the following components:

1.  Operating System. This will be Ubuntu 16.04.
2.  Platform. This consists of all of the “bare metal” service required to support the KCP application.
3.  Platform Kubernetes Cluster. A cluster that is used to host the KCP application.
4.  KCP Application. This consists of the UI, WFM, SM and SDDC components which all run on the Platform Kubernetes Cluster.

Tenant clusters consist of the following: 

1.  3 x VMs running the tenant Kubernetes Control Plane
2.  1 or more VMs running the tenant Kubernetes Worker Nodes.

All KaaS product installs and updates will be done using images (e.g. OVA) for v1.0 of the product.

There will be two types of images making up a release of the product: 

1.  image for the KaaS control plane nodes: including Operating System, k8s and supporting services e.g. percona, and the KaaS application itself
2.  image for all Tenant cluster nodes (K8s master and worker nodes): including Operating System, k8s and supporting drivers e.g. for HX, contiv-aci

## Installing KaaS

Installing KaaS the first time onto an existing HX cluster will involve starting the KaaS Control plane OVA and specifying environment specific information, e.g. which vsphere storage to use, which ips to use for the control plane nodes. 

Once the KaaS Control plane VMs, and attached persistent storage disks, have started the KaaS UI will be available to customers to begin deploying Tenant clusters. To create a Tenant cluster the second image will be used to provision the nodes making up the cluster. Once those nodes are available, K8s running on the Tenant cluster will be available for deployment of customer workloads.

The product upgrade scenarios are more complex and are described in detail below. The workflow is broken into two sections,

1.  first upgrading the KaaS control plane nodes (including Operating System, k8s and supporting services e.g. percona, and the KaaS application itself) and
2.  secondly upgrading the Tenant cluster nodes (including Operating System, k8s and supporting drivers e.g. for HX, contiv-aci)

## Upgrading the KaaS control plane

*   Customer will see in the KaaS UI that a new version of KaaS is available
*   Customer downloads the new KaaS control plan OVA from Cisco (i.e. CCW)
*   New OVA is imported into vsphere on HX cluster
*   Power off the three VMs which make up the KaaS control plane
    *   Obviously at this point the KaaS product is offline and customers can't spin up new Tenant clusters or manage existing clusters however existing deployed Tenant clusters will not be impacted and will continue to operate as normal
    *   Note: <span class="inline-comment-marker" data-ref="05e3945b-55c2-4b83-97d3-2b2acce67b01">The long term goal is to do rolling upgrades of the KaaS control plane, preventing any user impacting downtime

*   Take a snapshot of the VMs (and attached persistent storage disks)
    *   These will only be used in the event of needing to roll back an upgrade

*   Detach Persistent storage disks from the stopped KaaS control plane VMs
*   Attach Persistent storage disks to the VMs in the new OVA
*   Power on the new OVA
    *   This will effectively do a brand new setup of KaaS on the new VMs from the new OVA
    *   The new VMs will need to reuse the ip's from the old VMs so as to ensure external users can continue to access the KaaS UI

*   At this point the new version of the KaaS control plane will be up and running and we can proceed to the second and final step of the KaaS upgrade

### Upgrading the Tenant clusters

*   Customer downloads the new Tenant cluster OVA from Cisco (i.e. CCW)
*   New OVA is imported into vsphere on HX cluster
*   The Tenant upgrade  process will be triggered through the KaaS UI and will be orchestrated by KaaS itself
*   For each Tenant cluster the following workflow will be used:
    *   For each VM in a Tenant cluster the following workflow will be used:
        *   Power on a new VM using the new Tenant cluster OVA
        *   Add the new VM to the existing K8s cluster
        *   (For K8s worker nodes only) Drain workloads from the node
        *   Power off the old VM

*   Note: we still need to determine how to best handle the persistent storage of etcd data on the K8s master nodes in the Tenant cluster. One option maybe to run etcd itself as a pod inside K8s (similar to self-hosted K8s) and then use K8s Persistent Volumes which will be provided by HX.
*   Note: we need to verify how tolerant K8s is when running two versions at once, e.g. new Master version, but old Worker version, since that will be the situation using the workflow above.

# Test Plan

### Functional Test Cases

#### Installation

1.  Install KCP from HyperFlex HX Connect UI ( (out of scope for demo)
2.  Import KCP ova with required information ( (out of scope for demo)
3.  Import Ubuntu ova to create the cluster
4.  Login to KCP UI
5.  Uninstall KCP appliance from HyperFlex UI  (out of scope for demo)
6.  Add/modify/delete vSphere credentials for the service account which will be used provision vSphere resources.
7.  Perform CRUD operations on Kubernetes Clusters
8.  Get the Kubeconfig for a Kubernetes Cluster (default non-admin access)
9.  Get the Kubernetes dashboard for a Kubernetes Cluster
10.  Add a node
11.  Cordon/Drain a node
12.  Delete a node
13.  Upgrade a node OS (out of scope for demo)
14.  Upgrade K8S on a node (out of scope for demo)

#### Cluster Management

#### Node Management

#### Authentication and Authorization

There are two user personas - KCP Admin and KCP User.

Login as a KCP admin user and perform the following operations:

1.  Manage LDAP server info
2.  Manage vSphere credential information
3.  Create, delete and scale tenant Kubernetes clusters
4.  Check the cluster status for created Kubernetes clusters
5.  Access the Kubernetes dashboard UI or cli KUBECONFIG for the Kubernetes clusters
6.  Authorize LDAP groups or KCP local users to access the Kubernetes clusters

Login as a KCP user and perform the following operations:

1.  See the list of Kubernetes clusters authorized to the users
2.  Check the cluster status for the Kubernetes clusters
3.  Access the Kubernetes dashboard UI or cli KUBECONFIG for the Kubernetes clusters
4.  Create and deploy applications on the Kubernetes cluster

#### Logging and Monitoring (out of scope for demo)

1.  Setup a Prometheus Server on the K8s cluster
2.  Check time series data and plots are accessible
3.  To test the fluentd log collection, setup a test ELK stack and point the fluentd pods to the ELK stack
4.  Upgrade KCP and ensure that any DB changes are auto migrated.
5.  Upgrade the K8s Cluster
6.  Ensure workloads are not disrupted
7.  Deploy a Kubernetes application (guestbook?)
8.  Scale up/scale down
9.  Restart pods
10.  Check connectivity across pods
11.  Check persistent storage across pod restarts
12.  Upgrade the application
13.  Pod to Pod access works
14.  Services can be accessed by name
15.  Pod to external network access works
16.  Host to pod access works
17.  Create a service with multiple pods
18.  Expose the Service using the ingress controller
19.  Check external access to the service
20.  Delete a node on which one of the pods is running and check access to remaining pods

#### Application Management

#### Network Connectivity

#### Application Load Balancing (ingress only for MVP)

### High Availability testing

#### KCP High Availability

1.  Power off a VM running the KCP control plane. KCP should continue to be accessible.
2.  Power off all the VMs running the KCP control plane. KCP will not be accessible, but the Kubernetes clusters are not affected.
3.  Power off and restart all the VMs running the KCP control plane. The cluster should be restarted at the same state as before.

#### Kubernetes High Availability

1.  Power off one of the master VMs running a K8s cluster. The cluster should remain accessible
2.  Power off a worker VM in a K8s cluster. Workloads should automatically be rescheduled to other available nodes.

### Scale Test Cases

TBD

# Documentation / Cisco Validated Design (CVD) Plans

CVD will document the workflow for the following tasks.

1.  HyperFlex bring up/configuration
2.  KCP installation
3.  K8s Cluster Configuration
4.  Day-n tasks for the HyperFlex admin, KCP admin and KCP users

CVD will document reference architecture

1.  Hardware/software configurations
2.  Scale limits
3.  Deployment topology
4.  Monitoring & Logging deployment
5.  Registry configuration
6.  Sizing recommendations for multiple workload sizes (number of masters etc)
