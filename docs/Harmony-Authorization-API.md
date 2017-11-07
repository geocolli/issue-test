# Harmony : Harmony Authorization API


Add design notes here:

First cut:

-       Create CaaSAuthorization table:

*   UUID
*   ClusterID
*   UserID
*   Role

-       AddCaaSAuthz

*   /authz/
*   Create entry in table.

-       UpdateCaaSAuthz

*   /authz/clusterID/username
*   Update entry in table

-       DeleteCaaSAuthz

*   /authz/clusterID/username
*   Delete entry where ClusterID=X and User=username