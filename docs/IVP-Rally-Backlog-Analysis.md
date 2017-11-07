# Harmony : IVP Rally Backlog Analysis

Here is Rally URL - [https://rally1.rallydev.com/#/73760156440d/portfolioitemstreegrid](https://rally1.rallydev.com/#/73760156440d/portfolioitemstreegrid) (initiatives under I214 - rest are under rework)

Items that are not in CCP v1 - 

## I-103: Container Orchestration Engine

1.  Guest OS configuration - firewall rules, log permissions, firewall policies (CF - 11401, 11402, 11404)
2.  Design and implement HA support for K8s API LB - (CF - 9259)
3.  Make number of pods per node configurable (need to check whether it's possible in CCP - CF9440)
4.  Support for GlusterFS (Is it needed?) - (CF - 9534)
5.  Support for Hawkular (Does it need to be part of the platform) - (CF - 9544)
6.  Anything needed on local storage side? ( [https://wiki.cisco.com/display/VCBURD/Persistent+Volume+Investigation](https://wiki.cisco.com/display/VCBURD/Persistent+Volume+Investigation))
7.  Support for OpenStack and AWS (based on discussions, not needed initially - CF 9745, 9746)

## I-105: Integrations

1.  RBAC integration (not clear - exactly what it means, just noting so it doesn't fall through) (CF 11570)
2.  Make installer for IVP logging bridge (not clear) (CF - 10938)
3.  IVP bridge - requirement to have Consul in the platform? (CF 9625, 9627) (

## I-102: Compliance

1.  Any compliance testing requirements for the platform? (need more details)

## I-106: Infra services

1.  Need pagerduty and sensu event handler (CF9533)
2.  Need vault secrets storage (CF 6689)
3.  ELK with RBAC (CF 7442, 9247, 9248 etc.)

## I-154: Base Image and Guest OS

1.  Multiple host images needed - just for VMware? (need clarity - MF3445)
2.  Reduce base image footprint size - 10MBs (MF4883)

## I-155: CSDL and COSI

1.  Any additional testing requirements for the platform ( need to research
2.  Need Uchiwa, Sensu, Prometheus and Grafana in the platform (CF11685)
3.  Need Alpine Linux support (CF11499)

## I-163: Customer Engagement

1.  VMware cluster provisioning script (Need to review the script and make sure it aligns with CCP model) (CF - 9758)
2.  VMware nodes labels requirements (Need to follow similar model in CCP)
3.  IVP also uses Redis (CF10048) (not CCP requirement - need to confirm)

## I-224: Service Discovery

1.  Support for consul independently and in bridged mode

## I-214: Logging

1.  Requirements covered above, but lot more details on how Kibana, Logstash should be configured (Clustered and stand-alone modes- which mode is needed? or both?) 

## I-215: Alerting

1.  Support for Splunk 
2.  Configurations based on how alert is triggered (MF 4823, 4826)
3.  Sensu K8s plugin enhancements ( need details - CF 6676) (will be replaced by Prometheus?)

## I-216: Metrics and Integrations

1.  Need more details on what work is done here, not clear from Rally entries [https://coreos.com/operators/prometheus/docs/latest/high-availability.html](https://coreos.com/operators/prometheus/docs/latest/high-availability.html)
2.  Prometheus alert configurations, metrics de-duplication, right forwarders

## I-221: Provisioning

1.  Existing vSphere deployment (Does that mean VMs will be pre-provisioned? - MF4844)
2.  Bare metal - Ansible configuration of the node after provisioning (What type of configurations are needed - MF4843)

## I-222: Load balancing and gateways

1.  Ingress resource configurations - do we support these configurations - details in Rally entries? (MF 4845, 4846, 4876, 4877)
2.  Is haproxy support needed?

## I-104: Build and CI, I-112: VMR on IVP at Vodafone, I-223: environment (not applicable for CCP)

Additional things captured before Rally analysis through discussions - 

1.  Hardened Centos image provided by SP team – [https://metacloud.jira.com/browse/CIR-2100](https://metacloud.jira.com/browse/CIR-2100)
2.  Investigate leveraging COE platform logging and monitoring infra into Harmony K8s - [https://metacloud.jira.com/browse/HC-365](https://metacloud.jira.com/browse/HC-365)
3.  L7 loadbalancing using Ingress - [https://metacloud.jira.com/browse/CIR-2102](https://metacloud.jira.com/browse/CIR-2102)
4.  L3/L4 loadbalancing using IPVS - [https://metacloud.jira.com/browse/CIR-2103](https://metacloud.jira.com/browse/CIR-2103)
5.  Harmony K8s API to support providing inventory yaml file - [https://metacloud.jira.com/browse/CIR-2104](https://metacloud.jira.com/browse/CIR-2104) (P2)
6.  Harmony K8s to deploy K8s clusters on nodes that have been already provisioned with OS with Vaquero - [https://metacloud.jira.com/browse/CIR-2101](https://metacloud.jira.com/browse/CIR-2101) (P2)
7.  Harmony K8s + Vaquero integrated workflow - [https://metacloud.jira.com/browse/CIR-2105](https://metacloud.jira.com/browse/CIR-2105)
