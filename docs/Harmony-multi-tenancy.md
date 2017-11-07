# Harmony: Harmony multi-tenancy

Design choices:

1.  We will allow for the creation of multiple clusters within a project (since this is akin to allowing members of a project create multiple VMs).
2.  Definition of CaaS Admin role:
    1.  I can launch, edit, suspend, reboot and delete instances
    2.  I can launch, edit, suspend reboot, and decommission baremetal hosts
    3.  I can do CRUD operations on CX Clusters.

        Note: 
        1.  Two CaaS Admins CA1 and CA2 can view and delete clusters created by each other. But they cannot “access” each other’s clusters (without being given access information to these clusters explicitly/out-of-band)
        2.  This “CaaS Admin” role maps to the default “member” role built-in to OpenStack so they would get capabilties (a) and (b) by definition.

3.  Definition of CaaS User role:
    In those projects I have been authorized for,
    1.  I can view/get instances 
    2.  I can view/get baremetal hosts 
    3.  I can view/get CX clusters
    4.  I can do CRUD operations on pods (and any other job type) in K8S.

        Note: 

*   *   *   CaaS user role could be implemented through a custom role in OpenStack (defined in Nova's policy.json file) for VM and BM access and via CX code.

The table describes how authn and authz could be implemented for different APIs:

![](images/189869447/189963653.png?height=400)

Questions:

1.  How will we provide a CaaS user the credentials/config file they need to access the K8S cluster?
2.  Does the CaaS user have to be an OpenStack user to be able to get these privileges?
3.  What will the capabilities of these custom roles be with respect to volumes, networks, routers, Floating IPs, images and project quotas? 

Related links:

*   Roles that will be defined in an upcoming release of Metacloud:  <span class="confluence-jim-macro jira-issue resolved" data-jira-key="CIR-1591">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10807&avatarType=issuetype)CIR-1591](https://metacloud.jira.com/browse/CIR-1591) - Cloud Admin/Domain Admin/Project Admin