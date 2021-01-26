
# Certifying SPARROW: Trust but verify



### Background:

This document aims at explaining in depth the SPARROW gatekeeper's role, what needs to be certified, and ideas on how to do this. In order to do so, it refers to the  [gatekeeper certification document](https://github.com/WICG/sparrow/blob/master/gatekeeper_certification.md), along with the  [current SPARROW specification](https://github.com/WICG/sparrow). Many flavors of gatekeepers have been proposed/discussed in the last months; in this document, we focus on the SPARROW gatekeeper, and this one only.

In  [the gatekeeper certification](https://github.com/WICG/sparrow/blob/master/gatekeeper_certification.md), the rules are set up by a codification committee, and the audit / certification is performed by an independent certification entity that uses the predefined code of conduct.

We remind the reader that having a trusted server acting as a Gatekeeper brings many advantages, including but not restricted to:

-   No additional strain on the user's device and data plan.
-   Reactivity and capacity for complex optimization (campaign control, AB testing, etc.).
-   Fraud and IVT Robustness.
-   Billing auditing and reconciliation.
-   Less disruptive compared to TURTLEDOVE for advertisers/ DSPs and publishers.
-   Allows for competition between providers (important because of the associated costs).

All this makes the certification of such a helper server a worthwhile pursuit.

## Gatekeeper's design:

In SPARROW, the gatekeeper has three main roles:

-   Answering to bid request for DSPs, by generating a bid, and delivering the ad bundle.
-   Depending on design choices, Gatekeeper also ensures compliance with publishers' Ad Quality guidelines.
-   Building privacy-preserving reporting for advertisers/publishers.

The exact trust model will depend on some choices regarding the design (for instance, the trust model will change depending on the reporting proposals). This document is based on the current status of the  proposal. The current design of the gatekeeper will be described first and then the part about certification and trust (but verify) gatekeepers.

### Answering the auction:

The gatekeeper's main role is to answer a bid request sent by the browser, which includes interest groups and contextual information. The design we envision is as follow:

The gatekeeper design can be split into two main modules:

-   Generic gatekeeper code,  strictly executing explicit functions as stated in the code of conduct.
-   DSP APIs, that can run arbitrary code, but with the following strict constraints:
    -   No network access outside of Gatekeepers.
    -   Constrained Input / outputs.
        -   Inputs: The inputs are the bid request, with only one interest group and contextual information.
        -   Outputs: The DSP API return, a bid, information for rendering the ad (who will be rendered in fenced frame) and metadata (including, for instance, product id inside the banner).
-   A similar API could be introduced for SSP to handle ad quality.
![Gatekeeper auction design](https://user-images.githubusercontent.com/64090118/101050371-a3809400-3584-11eb-8ca1-d75420040567.png)

The detailed role of each component is explained below, by order of occurrence in the bid answering schema:

-   **Generic gatekeeper code**: Receiving the bid request including the list of IGs the user belongs to, along with the contextual information.
    -   Design:
        -   Here the Gatekeeper splits the request, and creates one per IG, in order to forbid the DSPs to use multiple interest groups together (as this could allow micro-targeting).
        -   Then it sends these split requests to the DSP's API  for them to be able to compute a bid for each interest group.
        -   Absolutely no PII is needed, and using it would be very complex for DSPs as the reporting would automatically filter them.
    -   Audit and Certification:
        -   This code is quite simple and could therefore quite easily be audited.
        -   In order to avoid the request to be intercepted before reaching the gatekeeper, encryption could be used.

-   **Specific DSP code: computing the bid of a specific request**
    -   Design: This code is arbitrarily complex and updated very regularly. Privacy protection will be ensured by controlling inputs and outputs, and network usage. Controls are the following:
        -   This code runs without any way to contact external services, nor has any network access.
        -   The input is a request with one interest group information and contextual information with it.
        -   The output consists of:
            -   A bid with constraints in terms of granularity (e.g. only 1,000 possible values). This will impact performance, so tradeoffs need to be assessed about the possible bid space.
            -   Rendering information for the gatekeeper to be able to render the ad if the bid is picked as the winner.
            -   The interest group along with the  targeted  domain.
            -   Other metadata for reporting purposes (please note that those metadata will then be processed following the reporting proposals). They include, for instance, information about the banner (product shown, video length, etc...).
    -   Audit and certification:
        -   Here, the role of audit and certifications will be to ensure the code indeed run in isolation, and that the input is as specified. This arbitrary code is therefore not part of the audit, only the checks on input output, and measure taken to ensure the code do run in total isolation. Subsequent code from the Gatekeeper will ensure that the output does respect the code of conduct.

-   **Generic gatekeeper code**: Treatment of the DSPs code outputs
    -   Design:
        -   The code here checks that DSPs code output is indeed respecting the code of conduct. This includes for instance checking that the bid value is one of the allowed bid values.
        -   It also creates an anonymized URL that can be rendered only into a fenced frame, used to render the ad, without any information that can be inferred from it by other actors.
        -   Depending on the design the gatekeeper handles both an internal auction and ensure ad quality guidelines:
            -   Ad quality guidelines can be enforced by using SSPs APIs as they can be for DSPs. They would run arbitrary code, with a limit on inputs (e.g they would get the targeted advertiser, and potentially even ad content (e.g. an image of products)) and on outputs where they would return a binary answer (blocked or unblocked).
            -   After handling ad quality, an internal auction is run by the gatekeeper depending on the number of bids the codification committee decides to send to the SSPs.
                -   Case 1 (1 external bid per DSP): an internal auction should be run among all the DSP's bids (but not among multiple DSPs), and only the highest validated bid should be sent to the SSP handling the external auction.
                -   Case 2 (only one bid is sent out): the highest validated bid among all DPSs hosted by the gatekeeper should be sent out to the SSP.
        -   The bid will be sent along with information deemed acceptable to transfer for ad quality by the codification committee, after assessing trade-offs in terms of privacy / efficiency / trust.
        -   The full output is sent for reporting purposes. It is then treated by the gatekeeper following the reporting proposal. The codification committee will choose if only winning bids are subjects to reporting or all bids.
    -   Audit and certification:
        -   Besides the ad quality, all steps here are quite straightforward in terms of code and auditing.
        -   Ad quality could be handled as for DSP logic, with an isolated Ad quality API ensuring compliance, with as input, targeted domain, and maybe even asset for pre-screening.
        -   Reporting is left for the next section.

### Reporting:

In SPARROW the Gatekeeper is also in charge of the reporting. A detailed proposal of the reporting can be found  [here](https://github.com/WICG/sparrow/blob/master/Reporting_in_SPARROW.md) . This section holds whatever the reporting proposal is chosen, as long as the reporting is still done by the gatekeeper.

**Design:**

The reporting sources for the gatekeeper reports are twofold:

-   The output of the DSP API described above: the metadata and the bid request. It is important to note that if a request was done for a user with multiple interest groups, they should not be logged together, but as multiple separated requests.
-   Click, view and other ad interaction notifications that are sent from the fenced frame rendering the ad to the gatekeeper.

The gatekeeper then joins the two sources as a basis for all reporting capabilities.

It then sends reporting regularly to Advertiser/DSP and SSPs/Publisher according to modalities described in the reporting proposal (and will be chosen definitely by the codification committee).

  

**Audit & certification:**

The auditing/certification part is trickier for reporting than the auction mechanism. It is quite straightforward to check that the report the gatekeeper is "officially" sending  complies with the code of conduct requirements. It is trickier to verify that it does not send other reports without respecting the privacy guarantees.

We also want to highlight the design constraints minimizing the risk of the gatekeeper going rogue or even the gatekeeper being attacked:

-   The gatekeeper does not receive any PII and does not even access to the meaning of interest groups.
    -   This means that for the information of gatekeeper to be used for fingerprinting, or leaking information on users, not only collusion is needed, but on top of that, the publisher request must be unique so that an interest group of a user can be linked to a unique user on the publisher side.
    -   Even if this occurs, it is not sufficient to tie this user with the exact user on the advertiser side (as cohorts, to be active, need enough users).

However, a rogue gatekeeper repeatedly leaking data can still transfer information on the users, hence the need for additional safety mechanisms and audit functionality.

  

In order to reduce the risk of leakage we are proposing the following design mitigation which will need to be audited:

-   Data should be stored only for a very specific timeframe (e.g. not more than a week).
-   Once the data has been transferred to the stakeholders, only transformed/aggregated data should be kept by the gatekeeper. That makes cheating more difficult as it means that it needs to be done before the report is sent to stakeholders, but still allows for ex-post contention resolution.
-   Technical means should be pursued:
	-   Any technical limitation that could trace each read of the database in order to be able to audit that it is not read for malicious purposes would be useful.
-   We ask for ideas and contribution on how both technical and design limitation could be put in place to strengthen the trust model for reporting.
There are many design limitations that could make it easier to trust but verify the gatekeeper, and we encourage people to propose sensible ways of making it possible.
    
## Technical mechanisms & future improvements

We are aware that there are technical means that can be used to help with trusting the gatekeeper. Two of the most discussed are:

- Software guard extension: a technique that allows to ensure exactly which code is run by issuing a signing hash. It could help audit the Gatekeeper "Generic code".
 
- Multi party computation were one need only to trust one of the helper server to be confident that the system is protected.

We think both avenues should be pursued, however, we are under the impression that the two technologies are not mature enough to be used for Gatekeepers as of today.
Indeed, setting up those trusted servers would already be quite hard, and infrastructure cost are already huge issue in the online advertising industry. Introducing two un mature technology into a revolution of huge scale for the industry would be to combine two revolution into one.
We are proposing a pragmatic two step revolutions. Let us set up Gatekeepers as "standard" server side components, whose trustworthiness will be defined using audit and rules that have been relied upon  in many industries / over many years.

We should also try as much as possible to help technologies like multi server computation / SGX mature, and should the audit model fail, or should the browser want even more robust guarantees, we may then updates the gatekeepers towards these new technologies to further ensure that servers can be trusted.

Certified Gatekeeper may be only a temporary solutions (and become permanent should they prove effective) to both allow for the open web continued monetization and to protect user's privacy, until a better solution is found.

## Business model & sanctions:

### Gatekeeper business model:

Gatekeepers are very hardware intensive endeavors, as both reporting and answering to bid requests using DSPs logic are both extremely costly. This means that it is very complex to have anything but a for-profit organization running those gatekeepers. It also means that there needs to be competition between at least two gatekeepers to avoid having a monopoly situation where the gatekeeper would demand an unreasonable toll.

This is why we are proposing this set up with a code of conduct defined by the codification committee and used by the certification entity to allow for competition and trust.

The gatekeeper provides services to two entities, who will be charged for it:

-   DSP/Advertisers, whose intelligence runs on the gatekeeper, and who receive extensive reports. They should be the first source of income for the gatekeeper.
-   SSPs, who delegate some ad quality checks to the gatekeeper. Depending on the final design, they will likely make a financial contribution to the gatekeepers too.

### Certification entity:

We propose these certifications are to be paid by Gatekeepers.

This is standard in many industries using certifications, for instance when requiring license fees, auditing, etc...

### Sanctions:

This is a very important part of the proposal.

Should  a gatekeeper fail to comply with the code of conduct, sanctions will be pronounced by the certification entity, up to de-certification (temporarily to permanent, depending on the gravity).
This would have the following consequences:  A non-certified gatekeeper would not receive bid requests and is therefore irrelevant. This means they would receive no revenue and thus the sanctions act as a strong deterrence against breaching the code of conduct..

Indeed, gatekeepers are costly to set up and operate. This means that a business model of "get certify, cheat, repeat" is not going to be viable economically, far from it. It is in Gatekeeper's interest to comply because the cost of non-compliance will strongly exceed the benefits.

## Conclusion:

SPARROW's Gatekeeper was designed with the idea to give them as little information as possible, only as much as is needed. Gatekeepers do not get PII from publishers, nor from advertisers (only IGs). They still get information, that used in conjunction with publisher and advertiser data can reveal user information. But even if the gatekeeper is breached, without collusion with at least the publisher, it is not possible to learn anything about specific users.

The huge pros of having the possibility to rely on a server-side component, along with the trust model we are proposing here for SPARROW's Gatekeeper are strong arguments in favor of building such a gatekeeper to protect the users' privacy, and propose a workable revenue model for the freely available content those same users value so much.
