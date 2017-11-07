# Harmony : CRUD operations for NFS volumes

Create NFS volume or any other resource:


| CaaS REST API | K8S cmd | <span>K8S API</span> |
| --- | --- | --- |
| POST /1/cluster/{clusterID}/resource | kubectl create –f nfs-vol.yaml | <span>POST k8s:[]/persistentvolumes</span> |


Modify NFS volume or any other resource:

| <span>CaaS REST API</span> | <span>K8S cmd</span> | <span>K8S API</span> |
| --- | --- | --- |
| PATCH /1/cluster/{clusterID}/resource | kubectl patch –f nfs-vol.yaml | <span>PATCH k8s:[]/persistentvolumes</span> |


Replace NFS volume or any other resource:

| <span>CaaS REST API</span> | <span>K8S cmd</span> | <span>K8S API</span> |
| --- | --- | --- |
| PUT /1/cluster/{clusterID}/resource | kubectl apply –f nfs-vol.yaml | PUT k8s:[]/persistentvolumes/name |


GET all persistent volumes or resources of a specific kind

| <span>CaaS REST API</span> | <span>K8S cmd</span> | K8S API |
| --- | --- | --- |
| GET /1/cluster/{clusterID}/resource {kind: persistentvolumes} | kubectl get persistentvolumes | GET k8s:[]/persistentvolumes |


GET a specific persistent volume or any other specific resource :

| <span>CaaS REST API</span> | <span>K8S cmd</span> | <span>K8S API</span> |
| --- | --- | --- |
| GET /1/cluster/{clusterID}/resource {kind: persistentvolumes} | kubectl get persistentvolumes name | GET k8s:[]/persistentvolumes/{name} |


Delete a persistent volume or any other specific resource:

| <span>CaaS REST API</span> | <span>K8S cmd</span> | <span>K8S API</span> |
| --- | --- | --- |
| DELETE /1/cluster/{clusterID}/resource {kind: persistentvolumes, name: name} | kubectl delete persistentvolumes name | <span>DELETE k8s:[]/persistentvolumes/{name}</span> |
