# Harmony : Harmony CaaS Role-capability separation

Harmony Roles

*   Harmony role: “Openstack-member”
    *   Has CaaS Capability: “CX-admin” to some clusters. "Some clusters" includes all clusters that this user created as well as all clusters that they were given "CX-admin" capability for.
    *   CRUD of VMs, BMs, Clusters {say c1, c3}

*   Harmony role: “Openstack-ro-member”
    *   Has capability: “CX-user” to some clusters. Some clusters includes those clusters that this user was explicitly given this capability. by some user who has CX-admin capability to that cluster.
    *   Read-only access to VMs, BMs, Clusters

CaaS Capabilties:

*   CX-admin to a cluster C: 
    *   This capability is obtained by either creating a cluster or by being given this role by another user who has this role for a cluster.
