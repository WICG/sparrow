
# SPARROW - ensuring Gatekeepers privacy compliance: a first proposal
### Background

In SPARROW, Gatekeepers are trusted entities in charge of running ad placement auctions, part of the ad delivery, and providing reporting to advertisers and publishers.

The Gatekeepers may have access to data derived from user information, such as the user cohorts, but must not disclose any data to other parties such as advertisers, publishers or other technology providers that could compromise the privacy of the end user. PII should not be sent to gatekeeper, but even if they are sent, all reporting proposals ensure they never leave the gatekeeper.

This is why this new position requires a comprehensive policy in order for such a trust to be given. On this page, we explain how a Gatekeeper could be certified in order to earn this trust.

Gatekeepers would be certified by an existing certification authority with audit capabilities, following a Code of Conduct defined by the W3C Improving Web Advertising Business Group participants and other interested parties.

### Certification and audit

Some companies are specialized in providing certifications. To name a few: National Institute of Standards and Technology (NIST), Financial Industry Regulation Authority (FINRA), Entertainment Software Ratings Board (ESRB), the Merchant Risk Council (MRC), Cloud Security Alliance (CSA). Such a company, independent from any actor in the online advertising industry would be ideal to provide this service as:

-   They have no vested financial interest in media buying or selling or in data derived from online advertising.
-   They have a proven track record on certification and audit
-   Their business entirely relies on them being trustworthy.

These entities would be in charge of certifying candidate Gatekeepers before the start of their activity and auditing current Gatekeepers regularly. Certification and audit would be based on a Code of Conduct.

The cost of initial certification and audits will be supported by Gatekeepers

Browsers would send Interest Group-based bid requests only to the certified entities. Should a certified Gatekeeper go rogue and lose its certification, the browser will stop sending such bid requests, making them irrelevant. The need for potential intermediate sanctions and rule around such sanctions will need to be discussed when defining the code of conduct.

### Code of Conduct:

The Code of Conduct is the "playbook" that defines both rule of operation and general guidelines for Gatekeepers. It would include:

-   Rules about how to handle requests (e.g, the Gatekeeper cannot tie multiple interest groups information together)
-   Rules about data treatment and processing:
    -   How long can Gatekeepers keep data?
    -   How should Gatekeepers transfer data? -This will be dependent on reporting proposals currently being discussed-
-   Formal interdiction of trying to use its data to try to fingerprint users somehow.

Initial definition and management of evolution for this Code of Conduct could be done at the W3C, or in a Trade Association (e.g. IAB), or in any forum with the agreement of browsers' vendors and W3C Improving Web Advertising Business Group participants.

This code of conduct might include some technical requirement (e.g open code for more transparency), or organisational requirement (e.g a DSP/SSP cannot be their own gatekeeper) if the people participating in the code definition think it is necessary.

## Conclusion

We think that this proposal solves a lot of issues raised by the existence of a trusted entity:

-   What does trusted means? It means being compliant with an agreed-upon Code of Conduct.
-   Who will certify and audit? Established companies specialized in this kind of certification, with no financial interest in the online advertising ecosystem and whose survival relies on the trust people give them.
-   How to deal with a rogue gatekeeper? The Code of Conduct should plan for a set of sanctions, including stop ending Interest Group-based bid requests.
-   Who will pay for this? The advertiser would pay the Gatekeeper as it will act in its stead, and the Gatekeeper will pay for the certification and regular auditing by the certification companies.

We are open to any suggestions and improvements to this proposal.

## Illustrative flow chart
![Gatekeeper certification](https://user-images.githubusercontent.com/64090118/92242143-a4d7ed00-eebf-11ea-93d3-aac6617ec195.png)
