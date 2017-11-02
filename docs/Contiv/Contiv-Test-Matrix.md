# Harmony : Contiv Test Matrix

This document contains the test matrices for all Contiv end-to-end (functional) tests. System testing efforts (i.e. unit testing) are outside the scope of this document.


## Platform Versions Supported

Docker versions supported: 

* **Legacy 1.12.6**

* **Native Swarm**

K8s versions supported:

* **1.4.x**

* **1.5.x**

* **1.6.x**

* **1.7.x (future)**

OS versions supported:

* **centos 7.x**

* **rhel 7.x**

* **ubuntu (TBD)**

# Contiv Basic Functional Tests


| # | Test Description | Testing Procedure | Verification points | Automated? | CI? | Supported Platform/Distro | Supported Encap |
| --- | --- | --- | --- | --- | --- | --- | --- |
| 1 | Network creation and usage [<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-101](https://metacloud.jira.com/browse/CNTV-101) | <ol><li>Create a contiv network with a specific subnet and gateway<li>Run contiv network list to ensure the network is created correctly.<li>Boot a container with the created network.<li>Ensure the container get the corrected leased IP | <ol><li>Ensure network creation<li>Ensure network is created correctly | <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-135" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-135](https://metacloud.jira.com/browse/CNTV-135) | |  <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 2 | Delete contiv network <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-102" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-102](https://metacloud.jira.com/browse/CNTV-102) | <ol><li>Create a contiv network<li>Boot a container/pod with that network<li>Delete the container/pod | <ol><li>Ensure that we can delete the contiv network<li>Ensure all infrastructure (OVS, etc.) is cleaned up | <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-135" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-135](https://metacloud.jira.com/browse/CNTV-135) |  | <ol><li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 3 | Delete contiv network (negative test) <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-103" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-103](https://metacloud.jira.com/browse/CNTV-103) | <ol><li>Create a contiv network.<li>Boot a container/pod with that network<li>Delete the contiv network | <ol><li>Ensure that we cannot delete the contiv network | <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-135" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-135](https://metacloud.jira.com/browse/CNTV-135)  | | <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 4 | Multi node networking <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-109" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-109](https://metacloud.jira.com/browse/CNTV-109)  | <ol><li>Create a contiv network<li>Boot two containers/pods. Each container/pod should be on different nodes | 1.  Ensure container/pods can ping each other | <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-136" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-136](https://metacloud.jira.com/browse/CNTV-136) | |  <ol><li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 5 | Single node networking <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-110" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-110](https://metacloud.jira.com/browse/CNTV-110) | <ol><li>Create a contiv network<li>Boot two containers/pods which reside on the same node | 1.  Ensure container/pods can ping each other | <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-136" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-136](https://metacloud.jira.com/browse/CNTV-136) |  | <ol><li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift |  <li>VLAN<li>VXLAN |
| 6 | Multi-tenancy networking <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-111" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-111](https://metacloud.jira.com/browse/CNTV-111) | <ol><li>Create two tenants<li>Create network under each tenant<li>Boot 2 containers from each network | <ol><li>Ensure container under the same network can ping each other<li>Ensure container under different networks cannot ping each other | <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-136" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-136](https://metacloud.jira.com/browse/CNTV-136)|  |  <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 8 | Connect pods to external network <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-112" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-112](https://metacloud.jira.com/browse/CNTV-112) | <ol><li>Create a contiv network with gateway<li>Create a container/pod under that network<li>ping 8.8.8.8 | <ol><li>Ensure ping 8.8.8.8 |  |  | <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 9 | Connect pods to external network (negative test) <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-113" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-113](https://metacloud.jira.com/browse/CNTV-113) |  <ol><li>Create a contiv network without gateway<li>Create a container/pod under that network<li>ping 8.8.8.8 | <ol><li>Ensure we cannot ping 8.8.8.8 | |  | <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 10 | Network create (multi-tenant) <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-114" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-114](https://metacloud.jira.com/browse/CNTV-114) | <ol><li>Create two tenant<li>For each tenant, create a network with same subnet and same gateway<li>Boot container under each network (in different tenant) | <ol><li>Ensure each container is able to boot<li>Ensure each container get leased IP | | | <ol><li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift  | <li>VLAN<li>VXLAN |

# Contiv Isolation Policy Tests

| # | Test Description | Testing Procedure | Verification Points | Automated? | CI? | Supported Platform/Distro | Supported Encap |
| --- | --- | --- | ---| --- | --- | --- | --- |
| 1 | EPGs under same network (default policy) <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-118" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-118](https://metacloud.jira.com/browse/CNTV-118) | <li>Create a contiv network<li>Create two EPGs under that network<li>Boot one container under those two EPGs each.<li>Ping from one container to another container | <ol><li>Ensure containers cannot ping each other | | | <li>Docker Legacy<li>Docker v2plugin<li>K8S <li>OpenShift | <li>VLAN<li>VXLAN |
| 2 | EPGs under same network (deny ICMP) <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-119" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-119](https://metacloud.jira.com/browse/CNTV-119) | <li>Create a contiv network<li>Create two EPGs under that network<li>Boot one container under those two EPGs each.<li>Create deny ICMP policy in one of the EPGs<li>Ping from one container to another container | <ol><li>Ensure containers cannot ping each other | | |<li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 3 | EPGs under same network (deny TCP) <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-120" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-120](https://metacloud.jira.com/browse/CNTV-120) | <li>Create a contiv network<li>Create two EPGs under that network<li>Boot one container under those two EPGs each.<li>Create deny TCP port 22 policy in one of the EPGs<li>ssh from one container to another container | 1.  Ensure containers cannot SSH | | |<li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 5 | EPGs under same network (allow ICMP) <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-121" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-121](https://metacloud.jira.com/browse/CNTV-121) | <li>*   Create a contiv network<li>Create two EPGs under that network<li>Boot one container under those two EPGs each.<li>Create allow ICMP policy in both of the EPGs<li>Ping from one container to another container | 1.  Ensure containers can ping each other | | | <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 6 | EPGs under same network (allow TCP) <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-122" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-122](https://metacloud.jira.com/browse/CNTV-122) | <li>Create a contiv network<li>Create two EPGs under that network<li>Boot one container under those two EPGs each.<li>Create allow TCP port 22 policy in both of the EPGs<li>ssh from one container to another container | 1.  Ensure containers can SSH | | |<li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |


# Contiv Network Policy Tests

| # | Test Description | Testing Procedure | Verification Points | Automated? | CI? | Supported Platform/Distro | Supported Encap |
| --- | --- | --- | ---| --- | --- | --- | --- |
| 1 | Bandwidth Limit <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-134" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-134](https://metacloud.jira.com/browse/CNTV-134) | <li>Create a contiv network<li>Create a network policy restricting bandwidth<li>Boot two containers/pods which reside on the same node | 1.  Ensure that one container cannot send more than the prescribed amount of traffic to the other container | | | <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |


# Contiv Resilience Tests

| # | Test Description | Testing Procedure | Verification Points | Automated? | CI? | Supported Platform/Distro | Supported Encap |
| --- | --- | --- | ---| --- | --- | --- | --- |
| 1 | Single node failure, no alternate node | <ol><li>Create a contiv network<li>Boot a container<li>Reboot the node on which the container is running | Expected behavior? | | | <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 2 | Single node failure, alternate node | <ol><li>Create a contiv network<li>Boot a container <li>Reboot the node on which the container is running | Expected behavior? | | | <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 3 | Single master failure, no alternate master | <li>Create a contiv network<li>Boot a container<li>Reboot the master node | Expected behavior? | | |<li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 4 | Single master failure, alternate master | <li>Create a contiv network<li>Boot a container<li>Reboot a master node | Expected behavior? | | |<li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |


# K8S specific test cases

| # | Test Description | Testing Procedure | Verification Points | Automated? | CI? | Supported Platform/Distro | Supported Encap |
| --- | --- | --- | ---| --- | --- | --- | --- |
| 1 | Load balancer |
| 2 | Ingress |
| 3 | Service |

# ACI-GW Tests

Note that the ACI group is taking over Contiv K8s and OpenShift ACI testing.

The existing ACI GW environment and testing that has been done (against Contiv 1.1.3) is documented here: [Existing Contiv ACI Test Setup](https://metacloud.jira.com/wiki/spaces/~amccormi/pages/200999425/Existing+Contiv+ACI+Test+Setup)

| # | Test Description | Testing Procedure | Verification Points | Automated? | CI? | Supported Platform/Distro | Supported Encap |
| --- | --- | --- | ---| --- | --- | --- | --- |
| 1 | EPGs under same network (default policy) <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-123" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-123](https://metacloud.jira.com/browse/CNTV-123) | <li>Create a contiv network<li>Create two EPGs under that network<li>Boot one container under those two EPGs each.<li>Ping from one container to another container | 1.  Ensure containers cannot ping each other | | | <li>Docker Legacy<li>Docker v2plugin | <li>VLAN<li>VXLAN |
| 2 | EPGs under same network (deny ICMP) <span style="color: rgb(51,51,51);"><span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-130" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-130](https://metacloud.jira.com/browse/CNTV-130) | <li>Create a contiv network<li>Create two EPGs under that network<li>Boot one container under those two EPGs each<li>Create deny ICMP ACI policy in one of the EPGs<li>Ping from one container to another container | 1.  Ensure containers cannot ping each other |
| 3 | EPGs under same network (deny TCP) <span style="color: rgb(51,51,51);"><span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-131" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-131](https://metacloud.jira.com/browse/CNTV-131) | <li>Create a contiv network<li>Create two EPGs under that network<li>Boot one container under those two EPGs each<li>Create deny TCP port 22 ACI policy in one of the EPGs<li>ssh from one container to another container | 1.  Ensure containers cannot SSH |
| 4 | EPGs under same network (allow ICMP) <span style="color: rgb(51,51,51);"><span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-132" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-132](https://metacloud.jira.com/browse/CNTV-132) | <li>Create a contiv network<li>Create two EPGs under that network<li>Boot one container under those two EPGs each.<li>Create allow ICMP ACI policy in both of the EPGs<li>Ping from one container to another container | 1.  Ensure containers can ping each other |
| 5 | EPGs under same network (allow TCP) <span style="color: rgb(51,51,51);"><span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-133" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-133](https://metacloud.jira.com/browse/CNTV-133) | <li>Create a contiv network<li>Create two EPGs under that network<li>Boot one container under those two EPGs each.<li>Create allow TCP port 22 ACI policy in both of the EPGs<li>ssh from one container to another container | 1.  Ensure containers can SSH |

# New K8S policy model

TBD

# Operations

| # | Test Description | Testing Procedure | Verification Points | Automated? | CI? | Supported Platform/Distro | Supported Encap |
| --- | --- | --- | ---| --- | --- | --- | --- |
| 1 | Netplugin version upgrade | <ol><li>Create some contiv networks<li>Create couple container under those networks<li>Ping between container <li>Upgrade from one netplugin version to a newer netplugin version | 1.  Ensure container networking shouldn't be interrupted | | | <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift | <li>VLAN<li>VXLAN |
| 2 | Adding new cluster node | <ol><li>Deploy a container platform (Docker swarm/K8S)<li>Create couple contiv networks<li>Create couple containers under those networks<li>Ensure network connectivity<li>Add an additional node to that cluster<li>Create containers under that node | 1.  Ensure new containers in the new node has network connectivity | | |  <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift |


# Installer

| # | Test Description | Testing Procedure | Verification Points | Automated? | CI? | Supported Platform/Distro | Supported Encap |
| --- | --- | --- | ---| --- | --- | --- | --- |
| 1 | Vagrant installer | 1.  Install all the vagrant related installer | 1.  Ensure all the vagrant related installer can bring up a dev cluster | | |  <li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift |
| 2 | Installer with end to end system | 1.  Run installer against end to end systems (e.g. vcenter, proxmox) | 1.  Ensure install script works without any aid of vagrant/Makefile | | |<li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift<li>Kubespray |


# Scalability

| # | Test Description | Testing Procedure | Verification Points | Automated? | CI? | Supported Platform/Distro | Supported Encap |
| --- | --- | --- | ---| --- | --- | --- | --- |
| 1 | High scale cluster node deployment <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-115" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-115](https://metacloud.jira.com/browse/CNTV-115) | | | | |<li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift |
| 2 | High scale container per node deployment <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-116" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-116](https://metacloud.jira.com/browse/CNTV-116) | | | | |<li>Docker Legacy<li>Docker v2plugin<li>K8S<li>OpenShift |

# Performance

| # | Test Description | Testing Procedure | Verification Points | Automated? | CI? | Supported Platform/Distro | Supported Encap |
| --- | --- | --- | ---| --- | --- | --- | --- |
| 1 | Performance test <span class="confluence-jim-macro jira-issue" data-jira-key="CNTV-117" data-client-id="SINGLE_f547c63a-0b4f-3c32-855b-9ad40099bb5a_197165084_ff808081520b687501522393a03a007c">[<span class="aui-icon aui-icon-wait issue-placeholder"></span>CNTV-117](https://metacloud.jira.com/browse/CNTV-117) |


# Customer specific use cases

CVD: 


## Attachments:
[DEE-Contiv-TestCases.xlsx](../attachments/197165084/197886106.xlsx)
