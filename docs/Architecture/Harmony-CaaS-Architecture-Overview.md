# Harmony : Harmony CaaS Architecture Overview

# Problem Thesis

Customers need a container-as-a-service (CaaS) with various day-1 to day-N management of containers. There are two personas for CaaS. A CaaS admin and end-user who directly runs on containers on container clusters. Our primary buyer is the CaaS admin (who's possibly the IT admin) and their requirements are

*   Cluster management: Deploy and do life cycle management of different types of container clusters (e.g. Kubernetes, OpenShift and Docker Swarm)
*   Storage management: Management of persistent container volumes on a persistent storage (e.g NFS on Netapp filer)
*   Network management: Management of container networks
*   Image management: Manage the images for containers. This includes managing the storage backend for the images
*   Monitoring: monitor the workloads running in containers and provide a way to analyze the data. Customers are ok with some simple built-in monitoring/analytics along with the requirement to integrate with "external to Harmony" systems like AppDynamics or Splunk

For end-user, the primary requirement is to keep the workflows the same as native Docker, Kubernetes and Openshift workflows. Note that the management extends beyond installation and configuration of container clusters. We need to provide day-N management and operations to make it easy for the CaaS admin to manage and operate a CaaS for his/her end-users.

# Architecture

Logically, a CaaS domain contains compute nodes (bare-metal or VM) to form a _container cluster_, as well as additional nodes that might be needed to support the container stack in the domain (for storage etc.) This domain is deployed and managed by CaaS control plane also known as CX for short.

CX service itself runs as a k8s service on top of a _control_ K8S cluster. E.g., in Harmony environment, this cluster is provided by Sparta alongside osCore, RCM and other components. It exposes APIs that allows authorized users to create and manage a CaaS domain.

Each domain runs a full container stack, such as K8s. CaaS provides an integrated container stack that is managed as a service, including aspects such as k8s control plane scaling and upgrades. Depending on the environment, these container platforms may rely on ancillary components/services, such as monitoring, identity management, load balancers, and storage.

Currently, we plan to support only k8s as a container stack for deployment in a CaaS domain. All other stacks (OpenShift, DDC) are not in scope. From a support perspective, an evaluation of vendors to use for vanilla k8s is done and available. [Comparison between UCP, OpenShift, K8s, Kismatic, Canonical K8s & Tectonic](https://metacloud.jira.com/wiki/download/attachments/186665332/orchestratorsComparison.pdf?version=1&modificationDate=1493431265916&cacheVersion=1&api=v2).

For Harmony integration of CaaS - also known as Harmony KaaS over Harmony IaaS, we are unifying the CX admin with the CX user persona. This implies the CX user can self service create a K8s cluster on any project that they have write access to.

For Hyperflex integration of CaaS, we are as yet undecided whether CX users will have self service cluster creation capabilities and if yes whether we will manage the authorizations for a user (AD group) to one of more vSphere clusters. The 3 alternatives being currently considered will be described in further detail in the authz section.

## High-Level Design

CX is deployed as set of a scalable Kubernetes services. 

The individual components of CX service are shown in the following figure (items on roadmap but not currently available are shown in boxes with dashed outlines).

![](../images/192169971/192169993.png?width=549)

*   wfm - workflow manager. Exposes REST API endpoint and provides fine-grained APIs as well as some APIs that execute workflows (which are combination of multiple APIs). WFM is the primary API entry point for the UI and CLI(in the future). When WFM is setup as more than one pods, the service needs to be Load Balanced using an external mechanism. This is external to the CX architecture and will be covered in the deployment section.
*   sm - state manager. Owner of all the state in CX. It's the only service that can talk to a SQL data store (e.g., a mysql database), owns the state and owns the schema. sm also handles authentication and authorization logic.
*   sddc - software defined data center for containers. Provides abstraction for an IaaS backend (e.g., Openstack and vSphere), runtime/orchestration (k8s, Docker) abstraction and OS (Linux, Windows)

CX service consumes underlying infrastructure components, such as VMs or Bare metal machines, networks, storage (NFS/SAN) etc via an infrastructure provider, such as OpenStack and vSphere, to setup and manage container clusters, as shown below:

![](../images/192169971/192169990.png?height=250)

## API Specification

TODO: Currently available from [https://cto-github.cisco.com/Harmony/cx/blob/master/doc/api.md](https://cto-github.cisco.com/Harmony/cx/blob/master/doc/api.md)

CRUD on container clusters

CRUD on cluster nodes

CRUD on local users

Add authorization for user/AD group to clusters

## CX Deployment

CX is assembled as a Kubernetes deployment and is deployed on a k8s cluster, such as the control k8s cluster in Harmony environment. The deployment is defined as a helm chart, and can be installed using the helm client directly on the k8s cluster.

<pre>helm install cx</pre>

In practice, there are additional deployment steps such as how to leverage an external SQL store and configure LB to access the CX service that are environment specific. For example, in Harmony environment, percona is used as external data store, and HAProxy is used for providing access to CX service. More details are available in the environment specific sections.

## K8S in Container Clusters

### Deployment

*   Kubespray, multi-master
*   Exact config - TBD
*   Compute - configured with environment specific cloud provider to allow for faster compute convergence for k8s api servers
*   Storage - environment specific storage driver to provide PVs
*   Networking - Contiv with VXLAN mode for now. Ingress resource setup with NGINX for L7 LB. L4 LB TBD (IPVS?)

### Life-cycle Management

# Harmony Environment Specifics

## Interaction with other components and end users

CX will be installed in an on prem availability zone by RCM which pulls down a job queue from HCP. CX life cycle management (upgrade, backups?, security, etc) will be managed by RCM. CX will expose a UI using which the end user will perform cluster management operations. A CaaS domain will expose a platform specific local UI (k8s dashboard) for the consumers of the cluster platform stack. CX will handle lifecycle management for the cluster platform stack.

![](../images/192169971/192169996.png?width=700)

## Deployment

CX service is deployed on k8s control cluster.

TBD: DevArch review of whole process. Things to cover:

1.  Ansible updates
2.  Secret provisioning (service account etc)
3.  Percona configuration
4.  LB/HA configuration

# Vaquero Environment Specifics

TBD

# HX Environment Specifics

HyperFlex provides a converged infrastructure platform ([https://cisco.jiveon.com/docs/DOC-1820520](https://cisco.jiveon.com/docs/DOC-1820520)).

![](../images/192169971/192169987.png?height=250)

HyperFlex runs a controller VM on each esxi host that manager the distributed storage. HyperFlex is managed using a vCenter instance (for the vSphere scenario).

## Deployment

CX will continue to be deployed as Kubernetes Services (wfm, sm & corc). CX will be provided as a 3 VM virtual appliance with a Kubernetes cluster running in a 3 master/worker configuration.

CX services will be externally exposed using a HAProxy or a VIP for the nodePort service for WFM.

CX packaging will continue to use a helm chart. 

## Authentication

CX will continue to allow local users or AD users.

## Authorization

There are 3 primary scenarios for authorization.

1.  All CX users are AD users and will be authorized to specific infra resources by a CX admin who creates **a cluster and authorizes a local user/AD group access to the cluster**. CX admin uses a service account to authorize against the backend.![](../images/192169971/192169978.png?height=250)
2.  All CX users are AD users and will be authorized to specific infra resources by a CX admin who creates **a resource group containing a vSphere cluster/resource pool and authorizes a local user/AD group access to it**. CX users can self service create the K8s clusters under those resources.
3.  All CX users are vSphere users and can create cluster and use them just as the CX admins can. They are authorized to vSphere resources using SSO.![](../images/192169971/192169975.png?height=250)

## Logging and Monitoring(user workloads)

CX will start a fluentd pod for monitoring the logs from a cluster. CX will allow the option to start a prometheus server on the K8s Cluster. 

The logs and time series data need to be routed to a customer manager ELK stack/Splunk instance/Prometheus server. 

![](../images/192169971/192169984.png?height=250)

## Load Balancing for Container Apps

Note: This wiki page has some high level info Harmony networking & load balancing. Please refer to the following wiki page [Harmony KaaS Networking](Harmony-KaaS-Networking_192170421.html) for the latest design on Harmony networking and load balancing.

![](../images/192169971/192169981.png?height=250)

For MVP we plan to provide L7 load balancing using NGINX.

## Kubernetes Cluster Life Cycle Operations

CX will support the Kubernetes lifecycle operations supported by Kubespray (only a subset will be available for MVP)

*   Add Node
*   Cordon/Drain Node
*   Delete Node
*   Upgrade K8s Version

Additional Life Cycle Operations are

*   Kubernetes Certificate Roll over
*   Node Operating System Upgrade

## Limits & Scale

Refer to the PRD

# Test Plan

### Functional Test Cases

#### Installation

1.  Install CX from HyperFlex HX Connect UI ( (out of scope for demo)
2.  Import CX ova with required information ( (out of scope for demo)
3.  Import Ubuntu ova to create the cluster
4.  Login to CX UI
5.  Uninstall CX appliance from HyperFlex UI  (out of scope for demo)

#### Cluster Management

1.  Add/modify/delete vSphere credentials for the service account which will be used provision vSphere resources.
2.  Perform CRUD operations on Kubernetes Clusters
3.  Get the Kubeconfig for a Kubernetes Cluster (default non-admin access)
4.  Get the Kubernetes dashboard for a Kubernetes Cluster

#### Node Management

1.  Add a node
2.  Cordon/Drain a node
3.  Delete a node
4.  Upgrade a node OS (out of scope for demo)
5.  Upgrade K8S on a node (out of scope for demo)

#### Authentication and Authorization

There are two user personas - CX Admin and CX User.

Login as a CX admin user and perform the following operations:

1.  Manage LDAP server info
2.  Manage vSphere credential information
3.  Create, delete and scale tenant Kubernetes clusters
4.  Check the cluster status for created Kubernetes clusters
5.  Access the Kubernetes dashboard UI or cli KUBECONFIG for the Kubernetes clusters
6.  Authorize LDAP groups or CX local users to access the Kubernetes clusters

Login as a CX user and perform the following operations:

1.  See the list of Kubernetes clusters authorized to the users
2.  Check the cluster status for the Kubernetes clusters
3.  Access the Kubernetes dashboard UI or cli KUBECONFIG for the Kubernetes clusters
4.  Create and deploy applications on the Kubernetes cluster

#### Logging and Monitoring (out of scope for demo)

1.  Setup a Prometheus Server on the K8s cluster
2.  Check time series data and plots are accessible
3.  To test the fluentd log collection, setup a test ELK stack and point the fluentd pods to the ELK stack

#### Upgrade CX(out of scope for demo)

1.  Upgrade CX and ensure that any DB changes are auto migrated.

#### Upgrade K8S Cluster(out of scope for demo)

1.  Upgrade the K8s Cluster
2.  Ensure workloads are not disrupted

#### Application Management

1.  Deploy a Kubernetes application (guestbook?)
2.  Scale up/scale down
3.  Restart pods
4.  Check connectivity across pods
5.  Check persistent storage across pod restarts
6.  Upgrade the application

#### Network Connectivity

1.  Pod to Pod access works
2.  Services can be accessed by name
3.  Pod to external network access works
4.  Host to pod access works

#### Application Load Balancing (ingress only for demo)

1.  Create a service with multiple pods
2.  Expose the Service using the ingress controller
3.  Check external access to the service
4.  Delete a node on which one of the pods is running and check access to remaining pods

### High Availability testing

#### CX High Availability (out of scope for demo)

1.  Power off a VM running the CX control plane. CX should continue to be accessible.
2.  Power off all the VMs running the CX control plane. CX will not be accessible, but the Kubernetes clusters are not affected.
3.  Power off and restart all the VMs running the CX control plane. The cluster should be restarted at the same state as before.

#### Kubernetes High Availability

1.  Power off one of the master VMs running a K8s cluster. The cluster should remain accessible
2.  Power off a worker VM in a K8s cluster. Workloads should automatically be rescheduled to other available nodes.

### Scale Test Cases

This is the target scale test limits.

*Pods consuming Persistent Volumes

**Total Pods including Ephemeral/Stateless, Stateful & Persistent


| **Max Scale** | **Demo/Alpha** | **GA** | **GA+** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Persistent Volumes/Pod* | 2 | 2 | 2 |
| Pod*/VM | 8 | 8 | 8 |
| VM/HX Node | 8 | 16 | 32 |
| HX Node/HX Cluster | 8 | 16 | 32 |
| Persistent Volumes/HX Cluster | 1024 | 2048 | 4096 |
| Persistent Volumes/VM | 16 | 32 | 64 |
| Persistent Volumes/HX Node | 64 | 128 | 128 |
| Pods*/HX Cluster | 512 | 2048 | 4096 |
| Pods*/K8S Cluster | 512 | 1024 | 2048 |
| HX Cluster/Harmony K8S | 1 | 1 | TBD |
| Total** Pod/K8S Cluster (min) | 0 | 0 | 0 |
| Total** VM/K8S Cluster (min) | 4 (3 masters for HA + 1 worker) | 4 (3 masters for HA + 1 worker) | 4 (3 masters for HA + 1 worker) |
| Total** Pod/K8S Cluster (max) | 10000 | 15000 | 30000 |
| Total** VM/K8S Cluster (max) | 64 | 512 | 1024 |
| Max K8S Cluster/HX Cluster (assuming minimum 4 VMs per cluster) | 16 | 128 | 256 |