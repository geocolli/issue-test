<div id="page">

<div id="main" class="aui-page-panel">

<div id="main-header">

<div id="breadcrumb-section">

1.  <span>[Harmony](index.html)</span>
2.  <span>[Harmony Kubernetes](Harmony-Kubernetes_186665332.html)</span>
3.  <span>[General](General_195854993.html)</span>
4.  <span>[Harmony CaaS Architecture](Harmony-CaaS-Architecture_188590493.html)</span>

</div>

# <span id="title-text">Harmony : Detailed Architecture Notes</span>

</div>

<div id="content" class="view">

<div class="page-metadata">Created by <span class="author">Himanshu Raj</span>, last modified by <span class="editor">Sanjeev Rampal</span> on Sep 20, 2017</div>

<div id="main-content" class="wiki-content group">

Logically, a CaaS domain contains compute nodes (bare-metal or VM) to form a _cluster_, as well as additional nodes that might be needed to support the container stack in the domain (for storage etc.) This domain is deployed and managed by CaaS Deployment and Management (DM) service also known as CX for short.

Following figure shows a high level architecture of these components.

<div class="ap-container" id="ap-com.gliffy.integration.confluence__gliffy3162565495060792504"><script class="ap-iframe-body-script">(function(){ var data = { "addon_key":"com.gliffy.integration.confluence", "uniqueKey":"com.gliffy.integration.confluence__gliffy3162565495060792504", "key":"gliffy", "cp":"/wiki", "uid":"blaksmit", "ukey":"ff808081520b687501522393a03a007c", "general":"", "w":"", "h":"", "url":"https://confluence-connect.gliffy.net/macro/render?space=HARMONY&container=187132587&pageId=&name=CaaS+-+HLA&attachmentId=att187150918&imageAttachmentId=att187150922&border=&chrome=&html5=&size=&ceoId=&contentPropertiesId=&spaceKey=&version=&migration=&previewPageId=187132587&tz=America%2FChicago&loc=en-GB&user_id=blaksmit&user_key=ff808081520b687501522393a03a007c&xdm_e=https%3A%2F%2Fmetacloud.jira.com&xdm_c=channel-com.gliffy.integration.confluence__gliffy3162565495060792504&cp=%2Fwiki&xdm_deprecated_addon_key_do_not_use=com.gliffy.integration.confluence&lic=active&cv=1.3.303&jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJmZjgwODA4MTUyMGI2ODc1MDE1MjIzOTNhMDNhMDA3YyIsInFzaCI6ImFkNWUwNWJhNzc5MTU2NGM4MjIyY2RlNzRiYjRlODYzZDAxN2E4NDVjNzBkOTI0ZmI0NjA4NjI3YzhhZWYyMWYiLCJpc3MiOiJDb25mbHVlbmNlOjM0MDg1NTYwMzciLCJjb250ZXh0Ijp7InVzZXIiOnsidXNlcktleSI6ImZmODA4MDgxNTIwYjY4NzUwMTUyMjM5M2EwM2EwMDdjIiwidXNlcm5hbWUiOiJibGFrc21pdCIsImRpc3BsYXlOYW1lIjoiQmxha2UgU21pdGgifX0sImV4cCI6MTUwOTQ2MTk1NiwiaWF0IjoxNTA5NDYxNzc2fQ.7QBHKeUd2o3Vqg-_f1hc94FEtP2ZYmotY70KAdgNSZA", "contentClassifier":"content", "productCtx":"{\"page.id\":\"187132587\",\"macro.hash\":\"98f2ea33-e309-4c97-b6fd-65ff24a03d44\",\"space.key\":\"HARMONY\",\"user.id\":\"blaksmit\",\"page.type\":\"page\",\"content.version\":\"10\",\"page.title\":\"Detailed Architecture Notes\",\"macro.body\":\"\",\": = | RAW | = :\":\"imageAttachmentId=att187150922|baseUrl=https:\\/\\/metacloud.jira.com\\/wiki|name=CaaS - HLA|diagramAttachmentId=att187150918|containerId=187132587\",\"space.id\":\"186269314\",\"macro.truncated\":\"false\",\"content.type\":\"page\",\"output.type\":\"html_export\",\"page.version\":\"10\",\"imageAttachmentId\":\"att187150922\",\"user.key\":\"ff808081520b687501522393a03a007c\",\"baseUrl\":\"https:\\/\\/metacloud.jira.com\\/wiki\",\"content.id\":\"187132587\",\"name\":\"CaaS - HLA\",\"macro.id\":\"98f2ea33-e309-4c97-b6fd-65ff24a03d44\",\"diagramAttachmentId\":\"att187150918\",\"containerId\":\"187132587\"}", "timeZone":"America/Chicago", "origin":"https://confluence-connect.gliffy.net", "hostOrigin":"https://metacloud.jira.com" }; if(window.AP && window.AP.subCreate) { window._AP.appendConnectAddon(data); } else { require(['ac/create'], function(create){ create.appendConnectAddon(data); }); } }());</script></div>

CX service runs on top of K8S cluster provided by Sparta alongside osCore, RCM and other components. It exposes APIs that allows authorized users to create and manage _CaaS domains _in a Harmony deployment.

Each domain, also referred to as a cluster, provides a collection of compute resources(VMs/Baremetal) and runs a container stack, such as OpenShift. These container platforms may rely on ancillary services provided by Harmony platform, such as monitoring, user ID management, and storage (NFS).

# Design Details

## CX API

### CRUD operations on CaaS Domain (Cluster)

1.  Create cluster

    1.  configures a set of nodes to form a cluster.
    2.  Sets up a container stack, such as kubernetes or openshift.
    3.  Requires caller to have minimum CX admin role.
2.  Delete cluster
    1.  Requires caller to have minimum CX admin role.
3.  Update cluster
    1.  Add node(s)
    2.  Remove node(s)
    3.  Node OS upgrades
    4.  Cluster stack upgrade
    5.  Requires caller to have minimum CX admin role.
4.  Get cluster URI.
    1.  Requires caller to have minimum CX user role.

See RBAC section later for more details on roles and associated capabilities.

## Networking

Note: This section has the high level problem definition. Refer to the following wiki page ([Harmony KaaS Networking](Harmony-KaaS-Networking_192170421.html)) for the latest documentation wrt Harmony networking.

Following flows of communication are expected

1.  Access to CX APIs from harmony portal

2.  Communication between nodes for K8s control plane
3.  Exposing **external access** to k8s api service endpoint

4.  Communication between CX and domain nodes for cluster management (kubeadm workflows)

5.  Communication between containers in a cluster. This communication will be policy controlled - a set of containers will be explicitly allowed to communicate with other set as per the needs of deployed application
6.  Northbound access to services running in containers in a cluster
7.  Communication between containers and hosts in a cluster

5-7 require a capable multi-host, policy-based, container networking fabric. We plan to use [Contiv](http://github.contiv.io) for this purpose. Contiv uses open virtual switch (OVS) for data plane on each cluster node. It supports L2 + ACI integration, L3 BGP, and VXLAN for container networking modes. Inter-container communication control is accomplished via creating separate networks for different groups of containers, and controlling routing between these networks based on policy. For MVP, we will focus on L2 and VXLAN modes only for container communication.

Following figure shows various networks in operation in a CaaS domain.

<div class="ap-container" id="ap-com.gliffy.integration.confluence__gliffy8862649964185292891"><script class="ap-iframe-body-script">(function(){ var data = { "addon_key":"com.gliffy.integration.confluence", "uniqueKey":"com.gliffy.integration.confluence__gliffy8862649964185292891", "key":"gliffy", "cp":"/wiki", "uid":"blaksmit", "ukey":"ff808081520b687501522393a03a007c", "general":"", "w":"", "h":"", "url":"https://confluence-connect.gliffy.net/macro/render?space=HARMONY&container=187132587&pageId=&name=CaaS-Networks&attachmentId=att187152544&imageAttachmentId=att187152548&border=&chrome=&html5=&size=&ceoId=&contentPropertiesId=&spaceKey=&version=3&migration=&previewPageId=187132587&tz=America%2FChicago&loc=en-GB&user_id=blaksmit&user_key=ff808081520b687501522393a03a007c&xdm_e=https%3A%2F%2Fmetacloud.jira.com&xdm_c=channel-com.gliffy.integration.confluence__gliffy8862649964185292891&cp=%2Fwiki&xdm_deprecated_addon_key_do_not_use=com.gliffy.integration.confluence&lic=active&cv=1.3.303&jwt=eyJ0eXAiOiJKV1QiLCJhbGciOiJIUzI1NiJ9.eyJzdWIiOiJmZjgwODA4MTUyMGI2ODc1MDE1MjIzOTNhMDNhMDA3YyIsInFzaCI6IjkwMTM4ZWI1NjY2M2EzYjE5YWJhYTg0NTBhNDljMDk4MzE2MWQ4ODM1M2ExODJkZjA4YTAzZjVlMWMwOWI0OGIiLCJpc3MiOiJDb25mbHVlbmNlOjM0MDg1NTYwMzciLCJjb250ZXh0Ijp7InVzZXIiOnsidXNlcktleSI6ImZmODA4MDgxNTIwYjY4NzUwMTUyMjM5M2EwM2EwMDdjIiwidXNlcm5hbWUiOiJibGFrc21pdCIsImRpc3BsYXlOYW1lIjoiQmxha2UgU21pdGgifX0sImV4cCI6MTUwOTQ2MTk1NiwiaWF0IjoxNTA5NDYxNzc2fQ.VbD-A77r4h58eaRCZVpFfTBXeUwGaSpkj_ta4GF_Efg", "contentClassifier":"content", "productCtx":"{\"page.id\":\"187132587\",\"macro.hash\":\"2b42f9eb-afaa-45b0-b570-4ed5ca754e88\",\"space.key\":\"HARMONY\",\"user.id\":\"blaksmit\",\"page.type\":\"page\",\"content.version\":\"10\",\"page.title\":\"Detailed Architecture Notes\",\"macro.body\":\"\",\": = | RAW | = :\":\"imageAttachmentId=att187152548|baseUrl=https:\\/\\/metacloud.jira.com\\/wiki|name=CaaS-Networks|diagramAttachmentId=att187152544|containerId=187132587|version=3\",\"space.id\":\"186269314\",\"macro.truncated\":\"false\",\"version\":\"3\",\"content.type\":\"page\",\"output.type\":\"html_export\",\"page.version\":\"10\",\"imageAttachmentId\":\"att187152548\",\"user.key\":\"ff808081520b687501522393a03a007c\",\"baseUrl\":\"https:\\/\\/metacloud.jira.com\\/wiki\",\"content.id\":\"187132587\",\"name\":\"CaaS-Networks\",\"macro.id\":\"2b42f9eb-afaa-45b0-b570-4ed5ca754e88\",\"diagramAttachmentId\":\"att187152544\",\"containerId\":\"187132587\"}", "timeZone":"America/Chicago", "origin":"https://confluence-connect.gliffy.net", "hostOrigin":"https://metacloud.jira.com" }; if(window.AP && window.AP.subCreate) { window._AP.appendConnectAddon(data); } else { require(['ac/create'], function(create){ create.appendConnectAddon(data); }); } }());</script></div>

ctrl network connects nodes in domain for k8s orchestration and Contiv management. data network connects nodes in _cluster_ (may not be all nodes in a domain). Harmony Network Service (HNS) will configure interfaces on each node to connect to these networks as required. For data network, the interface must be configured in **trunk** mode. This interface will be controlled by Contiv's OVS component, and will be used for container networking.

Based on container stack requirements, further networks may be required, e.g., for storage, and will be configured on nodes by CaaS DM service appropriately.

## Role-Based Access Control

User's ID is provided by an external ID provider, such as active directory. CaaS domain provides role-based access control of each cluster as a resource, as well as global, domain-level operations.

Role definitions - T.B.D.

## Container Stacks

We expect to support following container stacks for deployment in a CaaS domain

1.  Vanilla K8s - v1
2.  OpenShift - v1
3.  Docker Datacenter - Post v1

# CaaS DM Service Deployment

CaaS DM service containers are deployed on osCore control cluster running kubernetes. In particular, the deployment is a helm chart, installed as

<pre>helm install cx</pre>

# CaaS Interaction with HCP/RCM/osCore and end users

CaaS will be install in an on prem availability zone by RCM which pulls down a job queue from HCP.

CaaS control plane life cycle management (upgrade, backups?, security, etc) will be managed by RCM.

CaaS will expose a UI using which the end user will perform cluster management operations. CaaS will also expose a platform specific local UI (k8s dashboard) for the consumers of the cluster platform stack. CaaS will handle lifecycle management for the cluster platform stack.

<span class="confluence-embedded-file-wrapper confluence-embedded-manual-size">![](attachments/187132587/188290232.png?height=250)</span>

# Implementation Details

## MVP Specifics

For MVP, CaaS DM service will be deployed alongside osCore. As osCore deployment is also containerized, and is expected to be installed on the k8s control cluster using helm, it should be possible to install the helm chart for CaaS mgmt service on the same k8s control cluster.

CaaS DM service will be configured to use osCore API endpoint. It will call into osCore to:

1.  Create a tenant (project) on Domain create
2.  Allocate nodes in the tenant to add to domain
3.  Create networking resources for the domain

For an example workflow, see [Sample](https://cto-github.cisco.com/bradjone/bifrost-k8s-install/blob/domain-deploy/domain_scripts/deploy_caas.sh) script. It shows the kind of API interaction that CaaS DM will have with osCore.

# Open Items

Domain Upgrade

1.  New version of k8s on control cluster
2.  Node s/w updates, reboots

</div>

<div class="pageSection group">

<div class="pageSectionHeader">

## Attachments:

</div>

<div class="greybox" align="left">![](images/icons/bullet_blue.gif) [CaaS - HLA](attachments/187132587/187725855) (application/gliffy+json)
![](images/icons/bullet_blue.gif) [CaaS - HLA.png](attachments/187132587/187725863.png) (image/png)
![](images/icons/bullet_blue.gif) [CaaS - HLA.html](attachments/187132587/187150926.html) (text/html)
![](images/icons/bullet_blue.gif) [CaaS-Networks](attachments/187132587/187152616) (application/gliffy+json)
![](images/icons/bullet_blue.gif) [CaaS-Networks.png](attachments/187132587/187152627.png) (image/png)
![](images/icons/bullet_blue.gif) [CaaS-Networks.html](attachments/187132587/187152635.html) (text/html)
![](images/icons/bullet_blue.gif) [CaaS-Networks](attachments/187132587/187726266) (application/gliffy+json)
![](images/icons/bullet_blue.gif) [CaaS-Networks.png](attachments/187132587/187726258.png) (image/png)
![](images/icons/bullet_blue.gif) [CaaS-Networks.html](attachments/187132587/187152552.html) (text/html)
![](images/icons/bullet_blue.gif) [CaaS - HLA](attachments/187132587/190330360) (application/gliffy+json)
![](images/icons/bullet_blue.gif) [CaaS - HLA.png](attachments/187132587/190330368.png) (image/png)
![](images/icons/bullet_blue.gif) [CaaS-Networks.png](attachments/187132587/187726747.png) (image/png)
![](images/icons/bullet_blue.gif) [CaaS-Networks](attachments/187132587/187726755) (application/gliffy+json)
![](images/icons/bullet_blue.gif) [CaaS-Networks.png](attachments/187132587/187726861.png) (image/png)
![](images/icons/bullet_blue.gif) [CaaS-Networks](attachments/187132587/187726853) (application/gliffy+json)
![](images/icons/bullet_blue.gif) [CaaS-Networks](attachments/187132587/187727223) (application/gliffy+json)
![](images/icons/bullet_blue.gif) [CaaS-Networks.png](attachments/187132587/187727215.png) (image/png)
![](images/icons/bullet_blue.gif) [CaaS-Networks.png](attachments/187132587/187152548.png) (image/png)
![](images/icons/bullet_blue.gif) [CaaS-Networks](attachments/187132587/187152544) (application/gliffy+json)
![](images/icons/bullet_blue.gif) [hcprcm2.png](attachments/187132587/188289841.png) (image/png)
![](images/icons/bullet_blue.gif) [hcprcm3.png](attachments/187132587/188290232.png) (image/png)
![](images/icons/bullet_blue.gif) [CaaS - HLA](attachments/187132587/190330435) (application/gliffy+json)
![](images/icons/bullet_blue.gif) [CaaS - HLA.png](attachments/187132587/190330443.png) (image/png)
![](images/icons/bullet_blue.gif) [CaaS - HLA](attachments/187132587/187150918) (application/gliffy+json)
![](images/icons/bullet_blue.gif) [CaaS - HLA.png](attachments/187132587/187150922.png) (image/png)
</div>

</div>

</div>

</div>

</div>
