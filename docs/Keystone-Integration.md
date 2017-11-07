# Harmony: Keystone Integration


**Keystone has following concepts:**

User: Scoped within a domain. User name is unique within a domain.

Project: User can be assigned to multiple projects. User has a default project. Project maps to OSCore domain. Quotas are defined on project. Each CX cluster (K8s, OpenShift) will be created in a project. There can be multiple CX clusters within a project

Domain: User, Group, Project are contained in a domain.

Role: There are two roles by default: member and admin. User can have different roles in different projects. Admin role is required to create a project. Admin role lets user do any operation in OpenStack across projects/domains. User is usually assigned a member role for a project which lets the user do any operation within a project.

Keystone can be configured with LDAP or  an IDP (using SAML or OIDC). Keystone can also act as a SAML IDP.

**CX Authorization Concepts:**

Principal: This is a user or ldap group and associated role (devops or sysadmin).

Authorization claims: These are key value pairs associated with a principal. For example: key could be a cluster id and value could be role sysadmin and that will give a user sysadmin privileges to the given cluster.

We plan to use keystone for authentication and authorization and don't plan to use existing authorization objects in CX.

Which objects in CX will we like to have authorization for? We have removed pools and apps. We only have clusters.

**The user authentication/authorization flow:**

![](images/188753253/188825160.png?height=400)

1.  User logs in through LUI using keystone credentials.
2.  LUI calls CX APIs using the token returned by keystone auth APIs.
3.  CX checks if the token is valid using Keystone (in authenticate filter in CORC)
4.  If token is valid, CX checks if Principal (uniquely identified by domain, username and project) exists. If it doesn't it creates a Principal using role, domain, project information in keystone token (Keystone has two roles: member and admin which we can map to devops and sysadmin in CaaS).  We create a table for Keystone user storing domain, username, project and principal id and use existing authorization infrastructure to store principal and authorization claims.
5.  Alternative to step 4 could be that we delegate authorization to keystone as well. We get projects using the keystone token and use that information to filter and return cluster information. We are proceeding with this approach as opposed to step 4.

**CX Cluster Admin and CX Cluster User authentication/authorization flow:**

![](images/188753253/189863851.png?width=900)

1.  User logs in through LUI using keystone credentials.
2.  LUI calls K8s APIs using the keystone token in Authorization header as Bearer <keystone_token>
3.  K8s API server is configured to use Webhook token based authentication. It makes REST call to Webhook authentication service with token information.
4.  Webhook authentication service validates token with keystone and returns user information including project id and role in the JSON response to API server.
5.  K8s API server is configured to use Webhook based authorization. It makes REST call to Webhook authorization service running in CX with the user and requested resource and operation information. It should also be able to pass in cluster id information to Webhook service.  We need to figure out how we can save cluster id information with this K8s cluster and pass it to webhook. One approach: API server uses a kubeconfig style file for saving configuration to talk to Webhook service. That file has a field in which one could save cluster id information. 
6.  Webhook authorization service will first check if the k8s cluster belongs to the project for which token is scoped. Hence this service needs to be part of CX. Second it will check if the role is allowed requested operation on the given resource and returns yes or no in the JSON response to API server. In keystone, user is assigned a role for a project. So if a user has ClusterUser role on a project, then it will have that role for all K8s clusters created within that project. OpenStack services use oslo policy library to check if role is allowed a requested operation. It reads in a policy.json and checks access for the given token. It is written in python.

Based on feedback/discussion, we will use K8s RBAC for authorization as opposed to using policy.json file like in other OpenStack services. Modified flow is as below:

![](images/188753253/190013616.png?width=900)