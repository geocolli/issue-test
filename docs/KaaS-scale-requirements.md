# Harmony: KaaS scale requirements

Agenda:

*   Do we have ACI requirements?
    *   [https://metacloud.jira.com/browse/CIR-2043](https://metacloud.jira.com/browse/CIR-2043?jql=labels%20%3D%20harmony-k8s)
    *   Need to setup an ACI testbed for testing and validation
    *   Is required for GA
    *   Pradnesh to investigate a shared lab
*   Determine license needs/requirements for hyperflex
*   Do we have M5 other specific hardware needs that require ordering hardware?
*   Etc

*   Assume HX nodes are dedicated and not shared for other workloads
*   Define min/max range of RAM per VM
*   Document numbers that have been validated as part of the product solution
    *   Does the HX team have a CVD they can share as well as a certification process for their application against certain hardware configs?
*   testing to be performed against entry and performance HX units
*   KaaS control plane on VMs. These nodes don't need to be dedicated to control plane
*   Minimum cluster size is 3 nodes. 8 nodes for demo scale testing

Scale requirements:

Epic for capturing hardware guidlines/BOM:
[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10807&avatarType=issuetype)CIR-2047](https://metacloud.jira.com/browse/CIR-2047)

1. There are essentially 4 HX configs (HX220/HX240, Hybrid/All Flash)
2.  K8s HX Flex driver will support all configs and there is no difference in behavior of the driver
3. HX3.0 release will include the Flex driver in Beta- Dec 2017 and GA- Feb 2018
4. Scale limits discussed are software limits and independent of the config
5. Attached is HX 220 SKU info
6. Harmony dev could start working with HX220/Hybrid/Entry config (cheapest option)
7. Here is HX + ACI white paper based on HX220 - [https://www.cisco.com/c/en/us/products/collateral/hyperconverged-infrastructure/hyperflex-hx-series/whitepaper_c11-738327.html#_Toc471294948](https://www.cisco.com/c/en/us/products/collateral/hyperconverged-infrastructure/hyperflex-hx-series/whitepaper_c11-738327.html#_Toc471294948)

*   contiv-aci is different from contiv (only supports coupled mode)

TODO:

*   Create devinfra tickets for ACI testbed
*   Work breakdown for ACI-contiv
*   Architecture review for ACI-contiv integration with K8S

**Useful links:**

[Harmony CaaS Architecture](Harmony-CaaS-Architecture_188590493.html)
