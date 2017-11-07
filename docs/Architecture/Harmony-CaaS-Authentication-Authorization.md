# Harmony : Harmony CaaS Authentication/Authorization


1. **Problem Thesis:**
            The Harmony platform will have different personas access and use it's VMaaS, Baremetal-aaS and Container-aaS offerings. Each of these personas will have different privileges and capabilities. These privileges and capabilities for different personas need to be implemented through various authentication and authorization mechanisms.

2.  **Product Requirement:**
            (same as above)

3.  **Diagram of Proposed Solution:**
![](../images/190224710/190256135.png?height=400)

1.  **Detailed Description of the Proposed API:**
                [ API endpoints for all resources, actions, and versions ]
2.  Proposed solution is described in this power point presentation: [CaaS AuthN AuthZ Presentation](attachments/190224710/190682013.pptx)
  1.  **Components:**
  2.  **Name: **CaaS
  3.  **Detailed Description of the Proposed API: **

These two new APIs will be implemented: 

1. [https://harmany-caas-uri/1/system/authenticate](https://harmany-caas-uri/1/system/authenticate): Will be invoked by each K8S API call to authenticate the call
2.  [https://harmany-caas-uri/1/system/auth](https://harmany-caas-uri/1/system/authenticate)orize: Will be invoked by each K8S API call to authorize the call
3.  **Sub-components:**
   1.  V1 requires the addition of an authorization module for CaaS APIs
   2.  V1 requires the addition of an authentication and authorization webhook service for K8S APIs
   3.  V2 requires the addition of a custom role in the Openstack deployment**Dependencies:**
   4.  **Name:** OpenStack Custom role for "CaaS User"
   5. **Alternatives: **Are there alternatives?
   6. The alternative is to stick with V1 which does not need the definition of a custom role in Openstack.
   7. If so, why are we using this instead of the alternatives.
   8. We are not using the alternative because the "product" needs these different personas.
     1. Documentation
     2. Support
     3. Deployment
     4. **Spike is complete?**
     5. **When would you like the work to start? ** As soon as possible

## Example of CaaS roles:

![](../images/190224710/190681795.png?height=400)