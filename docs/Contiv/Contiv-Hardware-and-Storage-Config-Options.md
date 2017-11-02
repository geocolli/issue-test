# Harmony : Contiv Hardware and Storage Config Options

The goal of this page is to identify options that need to be supported for the Contiv effort and the timeline for supporting each config option.

**Assumptions:**

*   Identify any environmental assumptions

**Storage and Networking Configuration Options:**

1.  Provide, in priority order, the configurations that are required for Contiv engineering/support/verification

**Hardware Needs:**

Numbers in the table below represent nodes. Please note any that we already have available.

**Fabric requirements (copy from Harmony ACI BOM):**



Env Type | Contiv | Vcenter (future - next quarter)
--- | --- | ---
| Development env | <ul><li>5 - Additional servers in proxprod1 - <span class="confluence-jim-macro jira-issue" data-jira-key="DEVINFRA-1056">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-1056](https://metacloud.jira.com/browse/DEVINFRA-1056) - Contiv development - nodes for proxmox (Reserved for Contiv team)<li>3 Additional servers for ACI mode (with Intel 710 cards) - <span class="confluence-jim-macro jira-issue" data-jira-key="DEVINFRA-1057">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-1057](https://metacloud.jira.com/browse/DEVINFRA-1057) - <span class="summary">Contiv - additional machines connected for ACI
| CI/Integration env | 2 - specific for CI (with Intel 710 cards) <span class="confluence-jim-macro jira-issue" data-jira-key="DEVINFRA-1055">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-1055](https://metacloud.jira.com/browse/DEVINFRA-1055) - Contiv CI - 2 additional C220s 
| E2E test env | ?? Kahou to confirm if the existing testbed is enough | 3
| TME/Product env | vcenter env of John Day's. In SJC01 lab? | |
| Performance env | <ul><li>2 N9Ks<li>2 ASR1k<li>5 C220s (with Intel 710 cards)<li>Adjacent to Spirent<li><span class="confluence-jim-macro jira-issue" data-jira-key="DEVINFRA-1054">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10800&avatarType=issuetype)DEVINFRA-1054](https://metacloud.jira.com/browse/DEVINFRA-1054) - <span class="summary">Contiv performance test environment</span> <span class="aui-lozenge aui-lozenge-subtle aui-lozenge-complete jira-macro-single-issue-export-pdf">On Hold</span> |


*   ACI fabric is required, but can be shared. Non-ACI usage can bypass the fabric
*   All servers are C220 metacloud compute host unless otherwise noted
*   Current CI is running against AWS and Vcenter
*   Nic Bartos is working on Vcenter. Will be needed in a couple of months
*   Openshift development - will use the same dev servers as above
*   Can we repurpose one of the current EH environments for the performance env
*   Kahou to confirm with Raphael the routable subnet options in proxprod1


##Attachments:
* [IMG_6630.jpg](../images/201588978/201588982.jpg)
* [IMG_6631.JPG](../images/201588978/201588985.jpg)
