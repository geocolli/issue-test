# Harmony: KaaS CI review

*   Review current CI requirements with Galactic team
*   Discuss this as it relates to the KaaS release milestones
*   Make sure that all needed items are in the backlog and appropriately prioritized
*   Main target is HX3 cluster - this is the first env that is going to be built on actual HX gear
    *   HX1 an HX2 are blocked on drives
*   Needed CI environments
    *   ACI
    *   Non-ACI
    *   commits should be run against both ACI and non-ACI concurrently
*   Anticipated CI needs
    *   1 long running test
    *   Install tests on every merge (1-2 hours E2E)
        *   3 controller nodes, (3 master nodes, 2 worker nodes per tenant cluster)
    *   Upgrade tests nightly
    *   Release needs - on tag
*   Time to live for assets

    *   Needs to be implemented for Docker registry. CI team hasn't defined.
*   Artifacts
    *   <u>OVA for install (nothing in place with CI for this at the moment) - will live in a static file repo</u>
    *   <u>K8s binaries</u>
    *   OS Image
    *   Debian packages
    *   Docker images
    *   helm charts for updates
*   Information needed from Pradnesh on Import Compliance
*   **<u>CCO process information needed</u>**
*   Ubuntu is a hard requirement for KaaS
*   Integration with HX and ACI teams - may require additional infrastructure (needs more review)
*   ACI BOM - [Harmony Kubernetes on HyperFlex BOM](https://metacloud.jira.com/wiki/spaces/PR/pages/194773022/Harmony+Kubernetes+on+HyperFlex+BOM)
    *   To have an alpha of the ACI driver by 9/29, GA by end of November
*   Vaquero - need to be able to run control plane and at least 2 tenant clusters (11 boxes?)
    *   Can we do Vaquero inside of VMware?
    *   Vaquero with ACI cannot be tested in this manner and will require hardware (11 nodes)
*   Need to review devinfra queue and make sure there are tickets for each env discussed (Blake)