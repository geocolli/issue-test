# Harmony: KaaS Hardware and Storage Config Options

The goal of this page is to identify options that need to be supported for the KaaS effort and the timeline for supporting each config option.

**Assumptions:**

*   Compute: Will always be provided vSphere
*   Container Networking: Will always be provided by some variety of Contiv driver
*   Storage: Either provided by HX or whatever we find vSphere is setup to provide
*   BM/VM Networking: Either provided by ACI or whatever we find vSphere is setup to provide

**Storage and Networking Configuration Options:**

1.  No ACI, with HX:  [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10807&avatarType=issuetype)CIR-2004](https://metacloud.jira.com/browse/CIR-2004) - Deploy multi-master K8s cluster on top of VMware HX platform  In Eng Review  and <span class="confluence-jim-macro jira-issue" data-jira-key="CIR-2013">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10807&avatarType=issuetype)CIR-2013](https://metacloud.jira.com/browse/CIR-2013) - Setup storage solution to be used with K8s Hyperflex cluster Ready for Eng Review 
2.  With ACI and HX: <span class="confluence-jim-macro jira-issue" data-jira-key="CIR-2043">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10807&avatarType=issuetype)CIR-2043](https://metacloud.jira.com/browse/CIR-2043) - Contiv-ACI Driver for Harmony K8s Ready for Eng Review 
3.  Vaquero, non-ACI
4.  Vaquero with ACI
5.  ~~No ACI, no HX: <span class="confluence-jim-macro jira-issue" data-jira-key="CIR-2011">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10807&avatarType=issuetype)CIR-2011](https://metacloud.jira.com/browse/CIR-2011) - Setup networking solution (Contiv) to be used with K8s Ready for Eng Review ~~
6.  ~~With ACI, no HX:  <span class="confluence-jim-macro jira-issue" data-jira-key="CIR-2011">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10807&avatarType=issuetype)CIR-2011](https://metacloud.jira.com/browse/CIR-2011) - Setup networking solution (Contiv) to be used with K8s Ready for Eng Review  and <span class="confluence-jim-macro jira-issue" data-jira-key="CIR-2043">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10807&avatarType=issuetype)CIR-2043](https://metacloud.jira.com/browse/CIR-2043) - Contiv-ACI Driver for Harmony K8s Ready for Eng Review ~~

[David Lapsley](https://metacloud.jira.com/wiki/display/~david.lapsley) indicated that options 1 and 2 above are the highest priority based on the following timeline:

1.  <span>Hyperflex Integration (GA Q1 CY 2018)
2.  <span>Vaquero Integration (Beta Q1 2018, GA Q2 2018) - Add to current hardware dev Epic - Confirm that this is just C220s
3.  <span>VMware Integration (GA Q2 2018)
4.  <span>OpenStack Integration

**Hardware Needs:**

Numbers in the table below represent nodes. All HX clusters will be 3 node.


| Env Type | VMware | VMware, HX, No ACI | VMware, HX, ACI | Vaquero, non-ACI | Vaquero, ACI, storage options TBD |
| --- | --- | --- | --- | --- | --- |
| Development env | 8 (most dev happens here) e.g. VC1? | 3 - HX1[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-871](https://metacloud.jira.com/browse/DEVINFRA-871) - Create Hyperflex Cluster for dev - HX1 Done  | 3 - HX2 - currently 3 nodes - [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-875](https://metacloud.jira.com/browse/DEVINFRA-875) - Create Hyperflex/ACI Cluster for dev - HX8 In Progress (NAME CHANGE REQUESTED)| 4 extra compute nodes to HX1 - [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-962](https://metacloud.jira.com/browse/DEVINFRA-962) - 4 C220s for Vaquero compute nodes to HX1 Done  | 3+4+4 (KaaS + 2 deployments) 4 nodes (vmware + vaq)* - (extra compute nodes to HX2) - [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-963](https://metacloud.jira.com/browse/DEVINFRA-963) - 4 C220s for Vaquero compute nodes to HX8 Done  |
| CI/Integration env | 4 | 3 - HX3 [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-876](https://metacloud.jira.com/browse/DEVINFRA-876) - Create Hyperflex Cluster for CI - HX3 In Progress  | 3 - HX5 [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-956](https://metacloud.jira.com/browse/DEVINFRA-956) - Create Hyperflex/ACI Cluster for CI - HX5 On Hold  | 4 nodes (vmware + vaq)* (extra compute nodes to HX3) - [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-960](https://metacloud.jira.com/browse/DEVINFRA-960) - 4 C220s for Vaquero compute nodes to HX3  Done | 3+4+4 (KaaS + 2 deployments) - (extra compute nodes to HX5) - [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-964](https://metacloud.jira.com/browse/DEVINFRA-964) - 4 C220s for Vaquero compute nodes to HX5 Backlog  |
| TME/Product env | 4 - Needs to stand apart from Eng cluster and operate as prod Cust0. | 3 - HX4 [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-955](https://metacloud.jira.com/browse/DEVINFRA-955) - Create Hyperflex Cluster for TME/Product - HX4 On Hold  | 3 - HX6 [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-957](https://metacloud.jira.com/browse/DEVINFRA-957) - Create Hyperflex/ACI TME/Product Cluster - HX6 On Hold  | 4 nodes (vmware + vaq)* (extra compute nodes to HX4) - [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-961](https://metacloud.jira.com/browse/DEVINFRA-961) - 4 C220s for Vaquero compute nodes to HX4 Backlog  | 3 + 4 (KaaS + 1 deployment) (extra compute nodes to HX6) - [![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-965](https://metacloud.jira.com/browse/DEVINFRA-965) - 4 C220s for Vaquero compute nodes to HX6 Backlog  |
| Performance env | NA | Use HX team's lab | NA | NA | NA |


Performance environment to be provided by the HX team

\* Requires vaquero to support provisioning vmware nodes in the same way as baremetal