# Harmony : Hyperflex/KaaS Integration

# Requirements

There are three primary points of integration for KaaS and HyperFlex.

1.  HyperFlex as the IaaS backend for KaaS
2.  HyperFlex as the storage provider for KaaS tenant Kubernetes cluster workloads
3.  KaaS as a HyperFlex UI plugin showing information regarding the Kubernetes clusters running on a HyperFlex cluster as shown in the picture below.

![](../images/199065930/198967538.png?width=1000)

# High Level Design (detailing the alternatives)

## Hyperflex as the IaaS backend for KaaS

KaaS has a plugin driver model for different infrastructure providers or IaaS backends.

![](../images/199065930/198901965.png?height=250)

KaaS vSphere Driver uses vSphere (vmomi) API to provision Virtual Machines on a vSphere environment.

For V1, KaaS can use the vSphere driver to consume the IaaS services on HX platform.

_SSO Note:_ KaaS uses a vSphere provider configuration, comprising the vCenter information, user and password to access the vSphere backend for provisioning.  We may investigate using an extension mode (vSphere extensions) and using a certificate based authentication to vCenter instead of having to store the vSphere password in the KaaS DB (**needs to be evaluated pre-GA, primary issue at this time is the vSphere cloud provider configuration described in the next section**).

For V2, we need to evaluate adding a Hyperflex driver to SDDC. The primary advantage of this is to make use of Hyperflex snapshot functionality as well as to have a uniform interface to multiple hypervisors like Hyper-V, KVM etc.

## Hyperflex as the storage provider for KaaS tenant Kubernetes cluster workloads

KaaS has a plugin model for different orchestrator deployment mechanisms. Current KaaS deployer for Kubernetes on vSphere uses vSphere Cloud Provider as the persistence provider for tenant Kubernetes cluster workloads.

_SSO Note:_ vSphere cloud provider requires username/password based authentication at this time.

([https://github.com/kubernetes/kubernetes/blob/master/pkg/cloudprovider/providers/vsphere/vsphere.go#L74-L101](https://github.com/kubernetes/kubernetes/blob/master/pkg/cloudprovider/providers/vsphere/vsphere.go#L74-L101))

This will be replaced by the Hyperflex storage provider.

Details TBD

## KaaS as a Hyperflex UI plugin showing information regarding the Kubernetes clusters running on a Hyperflex cluster

 We want to show the end user KaaS kubernetes cluster information along with the VM information in the HX connect UI.

There are two primary problems to be solved here.

*   Day 0 - How does Hyperflex discover KaaS?
*   Day N - How does Hyperflex get the list of cluster information corresponding to the HX cluster?

### Day 0 - How does Hyperflex discover KaaS?

We discussed two different mechanisms for Hyperflex to discover KaaS.

1.  The first one uses a registration mechanism where KaaS makes a REST API call to Hyperflex to register its information. This brings us to the question of how does KaaS discover Hyperflex and the user/credentials to use to register with Hyperflex? As Hyperflex registers itself as a vCenter extension, we can “discover” HX by querying vCenter for its extensions. We can also use the vCenter provider credentials in KaaS to authenticate to HyperFlex. KaaS can do the registration at initial setup or on demand as an admin triggered publish operation. Registration at initial setup only will not work for the case where we have multiple HX clusters, some of which may be added later (after KaaS initial setup) to the system.
2.  KaaS registers itself as a vSphere extension. HyperFlex can discover KaaS by querying vCenter. AuthN/AuthZ is same as for Day N operations.

### Day N - How does HyperFlex get the list of cluster information corresponding to the HX cluster?

We discussed the following options for authN/authZ:

1.  (authN/authZ) HX opens the KaaS login page and user explicitly logs in using AD credentials
2.  (authN) HX passes a vCenter session ID and KaaS authenticates it against vCenter.

(authZ) KaaS gets the vSphere clusters for which the vCenter session has access to and allows access to KaaS clusters on those vSphere clusters.

1.  (authN/authZ) HX uses AD authentication and passes Kerberos information to KaaS
2.  (authN) HX and KaaS register service accounts with AD and “trust” these service accounts of each other. authZ aspects need to be discussed here.

1 & 2 seem to be the simplest options at this point, even though 2 is specific to vCenter and can’t be extended to Hyper-V later.

KaaS API to get the list of KaaS cluster for HX clusters

KaaS will add a REST API for this.

1.  Login to KaaS and get a JWT token
2.  Get the list of KaaS clusters and their information

GET on <a rel="nofollow">https://<API</a> Server>/v2/clusters with query parameter HX Cluster Name.
