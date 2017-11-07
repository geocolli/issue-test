# <span id="title-text">Harmony : KaaS Planning Agenda</span>

### Agenda Overview
*   Review completed work from last week
*   Review CIR epics for Priority
*   Review timelines and progress
*   Identify Risks and Cross-team dependencies
*   Review AIs and next steps
*   Review Parking lot items (if any)


### Agenda Details
*   Review completed work from last week
*   Review [CIR epic board](https://metacloud.jira.com/secure/RapidBoard.jspa?rapidView=430) for priority and current focus - [https://metacloud.jira.com/secure/RapidBoard.jspa?rapidView=430&quickFilter=2364](https://metacloud.jira.com/secure/RapidBoard.jspa?rapidView=430&projectKey=CIR)

    *   Review the board to ensure correct epic status and ownership

        *   [https://metacloud.jira.com/issues/?filter=35613](https://metacloud.jira.com/issues/?filter=35613) - Harmony V1 epics - review them
            *   **Backlog review later in week**
        * Need HX timeline on testing and delivery (Review of test plan document and approval). Cross-validation testing of components - Jive test plan.
            *   Alex/Charles/Kahou to work with Suresh, Mandeep
            *   **Provide document link of testplan - David in Jive
                **
            *   **Research spike needed (Blake) - investigate HX code**
            *   **Discuss QA options (within team, external)**
                *   **Solution testing team for Candy**
                *   Other details captured in Jive test plan
        *   Tenant cluster monitoring/logging requirements. Pradnesh to provide and update the backlog. Sync with eng leads to make sure this meets SP requirements.
            *   **Need a review of the Pradnesh's SP requirements analysis with the SP team and product (use Friday SP sync)**
    *   Pradnesh requesting info on upgrade/update
        *   Research spike in progress for control plane upgrade **(OPENSTACK-3958) - Pradnesh to review spec**
        *   Docs in progress - [Cluster operator](https://metacloud.jira.com/wiki/spaces/~vijkatam/pages/199065671/Cluster+operator) docs **(Pradnesh to review)**
    *   Still need to work through the release review/approval process and finalize
*   Review Timelines and Epics for each release
    *   <time datetime="2017-10-30" class="date-past">30 Oct 2017</time> Partner Kaas Hyperflex demo
    *   <time datetime="2017-11-13" class="date-future">13 Nov 2017</time> KaaS 0.3.0 release (tentative) - 2 week cadence begins
        *   Sample app required - <span class="confluence-jim-macro jira-issue" data-jira-key="SOLAR-2472">[![](https://metacloud.jira.com/secure/viewavatar?size=xsmall&avatarId=10803&avatarType=issuetype)SOLAR-2472](https://metacloud.jira.com/browse/SOLAR-2472) - <span class="summary">Sample app – We need to decide on sample app that can display persistent volumes, ingress, and external connectivity (Contiv)</span> <span class="aui-lozenge aui-lozenge-subtle aui-lozenge-complete jira-macro-single-issue-export-pdf">Open</span></span> 
    *   <time datetime="2017-11-15" class="date-future">15 Nov 2017</time> Cloud SEVT demo
    *   <time datetime="2017-11-27" class="date-future">27 Nov 2017</time> KaaS Kubecon demo release
    *   <time datetime="2017-12-06" class="date-future">06 Dec 2017</time> Kubecon demo (external)
    *   <time datetime="2018-01-29" class="date-future">29 Jan 2018</time> KaaS Hyperflex EA/LA Release (EAP target)
    *   <time datetime="2018-03-19" class="date-future">19 Mar 2018</time> KaaS Hyperflex GA
    *   Review [Release and Planning Calendar](https://metacloud.jira.com/wiki/spaces/MH/pages/170721784/Release+and+Planning+Calendar)
*   Identify risks

    *   Hardware and lab setup

        *   The first ACI environment should be ready for install this week.
        *   All tickets related to required hardware are called out on this page - [KaaS Hardware and Storage Config Options](KaaS-Hardware-and-Storage-Config-Options_192167930.html)
        *   Sanjeev to review testbed information for HX1 and provide further details on networking as needed - [HX1 Testbed Information](https://metacloud.jira.com/wiki/spaces/DEVINFRA/pages/191929436/HX1+Testbed+Information)
        *   **Is M5 hardware a requirement for GA V1?**
*   Identify cross-team dependencies

    *   ACI team dependencies (need to document and list these) - not in scope for October demo
    *   HX team
*   Planning output and next steps