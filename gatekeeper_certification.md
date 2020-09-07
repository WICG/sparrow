
# SPARROW - ensuring Gatekeepers privacy compliance: a first proposal
### Background

In SPARROW, Gatekeepers are trusted entities by people (their user agents), publishers and marketers in charge of running ad placement auctions, part of the ad delivery, and providing reporting to advertisers and publishers. These reports can be used to compare sell-side billing logs against the Gatekeeper information. 

The Gatekeepers may have access to data derived from personal user information, such as the user cohorts, but may only disclose this information in a privacy-preserving manner. For example, the Gatekeeper must not disclose any directly-identifiable data to other parties such as advertisers, publishers or other technology providers. The directly-identifiable information (which used to be called “PII”) should not be sent to Gatekeeper, in the first place, but even if such information is sent, all reporting proposals ensure this information never leaves the Gatekeeper.

This is why this new position requires a comprehensive web-wide policy in order for such a trust to be given to Gatekeepers. This is in order to preserve end user ability to visit ad-funded publishers. On this page, we explain how a Gatekeeper could be certified in order to earn this trust.

Gatekeepers would be certified by an existing certification authority with audit capabilities, following a Code of Conduct defined by the appropriate policy body. As the W3C Improving Web Advertising Business Group is a business group that does not have authority to propose standards, the policy work about appropriate and inappropriate Gatekeeper behavior should happen in a dedicated group with the relevant legal, privacy and technical participants to represent the various regional privacy regulations. That being said, this document can highlight how the technical aspects of enhancing trust in digital advertising transactions. 


### Certification and audit

Some companies are specialized in providing certifications. To name a few: National Institute of Standards and Technology (NIST), Financial Industry Regulation Authority (FINRA), Entertainment Software Ratings Board (ESRB), the Merchant Risk Council (MRC), Cloud Security Alliance (CSA). Such a company, independent from any actor in the online advertising industry would be ideal to provide this certification service (including future audits) as:

-   They have no vested financial interest in media buying or selling or in data derived from online advertising.
-   They have a proven track record on certification and audit
-   Their business entirely relies on them being trustworthy.

These entities would be in charge of certifying candidate Gatekeepers before the start of their activity and auditing current Gatekeepers regularly. Certification and audit would be based on a Code of Conduct.

The cost of initial certification and audits will be supported by Gatekeepers

Browsers would send interest group information along with bid requests, and subsequent ad rendering information only to the certified entities. Should a certified Gatekeeper go rogue and lose its certification, the browser will stop sending such bid transactions or ad rendering information, making them irrelevant. Browsers would have an API asking to certifying entities which Gatekeepers are certified, and therefore trusted, in order to allow for new Gatekeeper to be added and rogue Gatekeeper to be sanctionned. The need for potential intermediate sanctions and rules around such sanctions can be enforced against Gatekeepers and needs further discussion when defining the code of conduct. 

### Code of Conduct:

The Code of Conduct is the "playbook" that defines both rule of operation and general guidelines for Gatekeepers. It would include:

-   Rules about how to handle requests (e.g. the Gatekeeper cannot tie multiple interest groups information together when computing the auction bid)
-   Rules about data treatment and processing:
    -   How long can Gatekeepers keep data?
    -   How should Gatekeepers transfer data? -This will be dependent on reporting proposals currently being discussed-
-   Formal interdiction, for the Gatekeeper, of trying to use its data to try to fingerprint users somehow.

Initial definition and management of evolution for this Code of Conduct could be done by the appropriate policy body. This body should include W3C members, browsers, Trade Associations (e.g. IAB), publishers, privacy advocates etc. and be as wide as possible.

This code of conduct should include some technical requirement (e.g open code for more transparency), or organisational requirement (e.g., a market actor involved in buying or selling digital advertising cannot be their own Gatekeeper).

## Conclusion

We think that this proposal is a first step to address a lot of issues raised by the existence of a trusted entity:

-   What does trusted means? It means being compliant with an agreed-upon Code of Conduct.
-   Who will certify and audit? Established companies specialized in this kind of certification, with no financial interest in the online advertising ecosystem and whose survival relies on the trust people give them.
-   How to deal with a rogue gatekeeper? The Code of Conduct should plan for a set of sanctions, including stop sending Interest Group-based bid requests.
-   Who will pay for this?
    -	As described above, Gatekeepers must pay for certification. Gatekeepers are for-profit enterprises.
    -	The cost of operating a Gatekeeper business to process the petabytes of transactions will not be negligible. 
    -	The Gatekeeper fees will be paid directly by marketers, however as these incremental fees will impact their return-on-ad-spend, the Gatekeeper costs will also be indirectly borne by publishers.
    -	Accordingly, we need sufficient market competition by multiple Gatekeepers to ensure rates they charge are kept to a minimum.


We are open to any suggestions and improvements to this proposal.

## Illustrative flow chart
![Gatekeeper certification](https://user-images.githubusercontent.com/64090118/92409802-2a6acf80-f142-11ea-85b6-09388e172d34.png)
