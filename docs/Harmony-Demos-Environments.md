# Harmony: Demos/Environments

| Date | Presenter | Title | Recording |
| --- | --- | --- | ---|
| 5/5/2017 | Yuva Shankar | OpenShift on OpenStack | [**Play recording**](https://cisco.webex.com/ciscosales/lsr.php?RCID=b62e1c6533ea471985ba2cd5199048b3) (1 hr 39 min 10 sec)  <br />Recording password: YwQ8vJkq


*   Cluster Setup

1) CX admin installs Harmony K8s (optional)

2) CX admin creates a three node K8s cluster with single / multi master (not sure - if we can do multi-master)

3) CX admin configures storage backend for persistent storage - VMware vSphere driver or Harmony Cinder backend

4) CX admin creates overlay network in Contiv for K8s cluster (install / config of Contiv is already done on pods)

(Setup HA for K8s masters using LB and security certs is out of scope as functionality isn't probably there yet)

*   K8s Consume

1) Pre-configured AuthN (Keystone) with local users and AuthZ of native K8s. (AuthZ of Harmony K8s is out of scope - need to confirm)

2) Pre-configure HAProxy or Nginx manually in the pods

3) AuthN with Harmony K8s and log into K8s cluster

4) Deploy sample app (guestbook app?) with LB and persistent storage

5) Show K8s dashboard for cluster resources

(UI and AuthZ of Harmony K8s is out of scope as work needs to be done)

*   K8s lifecycle management

1) Increase app replicas and show cluster resources are running short (bonus goal)

2) Scale out cluster to five nodes by adding nodes (not sure - if we can add master)

3) Show K8s app deployment in Prometheus and logs in ELK stack

(Upgrade and delete K8s clusters is out scope as work needs to be done)