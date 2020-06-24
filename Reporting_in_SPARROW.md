

# Reporting in SPARROW
Relevant Internet advertising is an important component in striking a balance between providing a good end user experience and allowing publishers and content creators to receive adequate compensation for the content they provide. Hence, the conjunction of user interests (as it is captured by interest groups in TURTLEDOVE)  along with publisher relevance  is a key driver of the open internet advertising ecosystem. Any weakness in leveraging both signals together would undoubtedly hurt both the publisher's revenue and the user experience, exposing them to irrelevant advertisements or worse, unsafe content.

Furthermore, advertisers want to ensure that their products are only appearing on websites that match their brand safety standards and, likewise, publishers want to ensure that all advertisements that appear on their site are suitable for their audience and their own brand image.

This is why any proposal that does not allow joint use, and reporting of, publisher information along with the interest group is, by design, flawed and will lead to extremely limited adoption from the user, advertiser and publisher.

  

This objective behind this proposal is to prevent any possible association between end user PII - some being available on publisher side - and interest groups, whilst providing the best possible support for the variety of reporting use cases as described below,  in order to preserve the most essential parts of the ad business

-   [The proposal](#the-proposal-real-time-aggregated-/-delayed-ranked-privacy-preserving-granular-report)
-   [Relation to use cases](#how-it-relates-to-some-common-use-cases-that-require-reporting-capabilities)
-   [Aggregated report](#aggregated-report)
  -  [Report on ads served](#report-on-ads-served)
   -   [Ranked-privacy preserving granular report](#ranked-privacy-preserving-granular-report)
-   [ABtest and private unique id count](#abtest-and-private-unique-id-count-for-k-anonymity-reporting-computation)
-   [Appendix](#Appendix)
    -   [Algorithm used to hide some contextual features in the ranked privacy-preserving report](#algorithm-used-to-hide-some-contextual-features-in-the-ranked-privacy-preserving-report)
    -   [Example of ranked privacy-preserving report](#example-of-ranked-privacy-preserving-report)
    -   [Privacy considerations](#privacy-considerations)


# The proposal Real-time aggregated / delayed ranked-privacy preserving granular report

In a nutshell, to handle the different use cases, we propose to rely on a combination of aggregated and  k-anonymous  granular reports (definition  [here](https://en.wikipedia.org/wiki/K-anonymity)). Please note that this is an initial proposal and we remain open to any suggestions to improve it. The proposal consists of three different reports:

-   An  **aggregated report**, with variable latency, but with the  **possibility to have real-time information**. This report should be available with differential privacy.
-   A  **delayed** **served ads report** for publishers. It informs about the creatives and origins of the ads such that the publisher can check if the served ads comply with its ads policy (i.e. publisher Ad Quality). The delay should be discussed with the main stakeholders (publisher and  industry experts on security and ad safety), in order to find the right balance between user safety and user privacy. To support privacy constraints, a sampling mechanism  could be used.
-   A  **delayed ranked privacy-preserving granular report based on k-anonymity**:
    -   This report is granular, meaning one row per display.
    -   A granular report with intentionally noisy prices and k-anonymity on variables shared by advertiser and publisher (information about the interest group is never available to the publisher, even with appropriate k-anonymity).  
        
    -   A different version of this report is available for the advertiser and the publisher.

## How it relates to some common use cases that require reporting capabilities

### Advertiser needs

| Use-case | Description & Requirements| Example of variables| Support from proposal |
|----------|--------|--------|---------------------|
| Campaign Control|Ability to stop / start change spend on a campaign. <br> Report should be near-real-time to be able to pause/start, control budget/bidding level, ... with very little latency. (e.g less than 10 minutes)  | -   Budget Remain/Spend, eCPM, cost. <br> -  Interest Group, Campaign ID|Aggregated report ||
| Invalid Traffic/Fraud Detection/Filtering|Detection of different types of fraud. Today fully granular, including PII, reports are available and used in real-time. | -   Basic metrics (displays, clicks, visits) <br> -   Contextual information <br> -   Identifiers (e.g. IP) can also be very helpful|Ranked k-anonymous granular report but limited. <br> For massive fraud events, the aggregated reporting may be leveraged in real time. <br> Fighting against IVT is best when relying on PII (IPs / uids) information. The privacy sandbox directly opposes this and this use case thus can not be supported fully. <br> The necessary delay and partial information (either because of differential privacy or K-anonymous reporting) further reduce ability to act quickly on IVT, which is a weakness of the privacy sandbox. Gatekeepers might have to become a first line of defense.| |
| Campaign Performance|Real-time control of bid levels and campaign parameters to allow for performance optimization. <br> Need as much granularity as possible to avoid bad incentive caused by asymmetric information. | - Interest Group, Creative ID, ABTest ID <br> -   Impressions, (Landed) Clicks, Visits <br> -   CTR, Non-Bounced Rate, Viewability <br> -   Cost|Aggregated report for overall level <br> ranked k-anonymous granular report for optimization.| |
| Same UserAgent, same device Attribution / Conversion Measurement|Measure and report (a few hours later) the number of ad actions (display, view, click, ...) that lead to a conversion (visit, sale, ...) on the same user agent and device.  | -   Ad action (display, view, click) <br> -   Conversion (visit, sale, ...) on the same user agent and device.|The ranked k-anonymous granular report offers display id and the click id to the advertiser (and not for the publisher) allowing further attribution to be done in a second step.| |
| X-user agent and/or X-device Attribution / Conversion measurement|Measure and report (a few hours later) the number of ad actions (display, view, click, ...) that lead to a conversion (visit, sale, ...) anywhere. | -   Ad action (display, view, click). <br> -   Conversion (visit, sale, ...) anywhere.|Not supported. <br> Gatekeepers don't have access to any x-device or x-user-agent PII.| |
| Brand Lift Measurement|Measure and report (a few hours later) the difference of behavior between exposed users and non-exposed user. <br> Need very little noise in the reporting as observed effect are already often very noisy.  | Conversion lift through **incrementality** (#conversions happened due to campaign). - Creative Id <br> - Interest group Id <br> - ABTest Id <br>- Conversions|The ranked k-anonymous granular report allows for measuring the performance on users we have done an ad for.| |
| Ex post Brand Safety|Ability to audit that served ads / publishers did indeed respect the guidelines. <br> Real-time reports with full granularity are currently used for this and any solution should be as close to it as possible.| - Contextual information <br> - Interest Group <br> - Bid price (FPA), Price paid (SPA), with noise.|Ranked k-anonymous granular report + Aggregated report.| |
| Billing|Ability to get ads placement location, data and time, and prices to be used later for publisher and advertiser billing. <br> Need a report with as exact as possible information on billing between all stakeholders. <br> It would be best if two sources of this report were available to handle potential litigation.   |  - Publisher domain <br> -  Interest Groups/campaignId <br> - day/time <br> - Budget spent/avg. Price|Aggregated report. <br> An especially low noise / noiseless aggregated report could be provided daily for the specific billing purposes. <br> Note that an exact report might be a breach of user privacy, but of low magnitude, hence the discussion on the amount of noise included in a billing report. <br> SPARROW enables two parties, one on Publisher side and one on Advertiser side, to generate that same report, thus enabling cross-data comparison and disputes resolution.| |


### Ad networks needs

| Use-case | Description & Requirements| Example of variables| Support from proposal |
|----------|--------|--------|---------------------|
|Optimization & Performance | Use historical data to train machine learning model to fit the advertisers needs. <br>  More generally, use historical data to optimize the client needs and bring performance. This performance is the reason for increased publisher's revenue. <br> Data as granular as possible is needed to perform the previously described use cases. | - Bid timestamp, Bid price/Price paid. <br>  As much contextual as possible:<br> - Publisher Url, Page Vertical, Ad Position, Keywords. <br> - Ad tracking: Impressions, (Landing) Clicks, Viewability. <br> - Conversions: Visits, Basket Events, Sales. <br> - Interest Group, Creative ID. <br>-  ABTest ID|Ranked k-anonymous granular report ||

### Publisher needs

| Use-case | Description & Requirements| Example of variables| Support from proposal |
|----------|--------|--------|---------------------|
|Ex post Ad Quality, UX & Auditing |In order to check ads and potentially permanently ban irrelevant, offending, bad UX ads, publishers need granular report and the ability to block / blame specific offending advertisers.| - Product URL, Advertiser Verticals. <br> - Creative Id. <br> - Number of displays <br> - Targeted domains|Aggregated report and Delayed served ads report. <br>  The Gatekeeper could provide a service to ensure Ad Quality _before_ the ad is displayed.||
|Ex post Ad Quality for user safety |Some ads do not only breach publisher guidelines, they are a threat to the user. Malware advertising is a well known and growing issue. <br> In order to handle malware advertising, real time and report as granular as possible are needed in order to limit the damages. Publisher need to be able to block both the creative ID and the targeted domain with the highest reactivity| - Creative IDs,Product URL. <br> - Advertisers / Targeted domains|Delayed served ads report.  <br> A report with too much delay might have massive impact on the publisher image but, worse, on the user safety (e.g [https://www.bbc.com/news/technology-35821276](https://www.bbc.com/news/technology-35821276)). <br> Gatekeepers might have to be a first line of defense. ||
|Billing |Know how much advertisers/DSP owes the publisher and be able to explain quite precisely the spend|- advertiser (domain) <br> - DSP <br> - price <br>- Creative Id|Aggregated report (potentially daily report with low noise) ||
|Invalid Traffic/Fraud Detection/Filtering |Detection of different type of fraud. <br> Today fully granular, including PII, reports are available and used in real-time.| - Basic metrics (displays, clicks, visits) <br> - Contextual information <br> -   Identifiers (e.g. IP) can also be very helpful|Same as for advertiser, but without click / visit information. <br> Please note that publishers have there own reports with all ads served and full contextual information, they are just missing the advertiser, price and labels. <br> They can therefore notice IP flooding thanks to their own reports. <br> Requirements of the privacy sandbox means that this cannot be as well supported as it is today. ||
## Aggregated report

The aim of this report type is to enable near real-time management for advertisers and publishers. It relies on differential privacy mechanism.

Variables such as, interest groups, displays, views, clicks, prices,  and creatives ids  can be exposed here in a privacy-preserving way while keeping the near real-time property which is critical for ad tech players.

A too-high privacy level applied to this report (i.e. too much noise) could endanger its usage for some use cases (i.e. billing) which would then require a specific report, too.

## Report on ads served

In order to ensure that rendered ads are compliant with the publisher guidelines about ad quality, and even more important, user safety, the publisher receives all web bundles with the click-through url domain, creative id and the advertiser name.

Although the other two types of reports are meant to offer flexibility and completeness while respecting privacy standards, they may not be enough to handle publisher ad Quality. For this use case a separate "report on ads served" is  necessary.

Discussions about delay and the scale of information available in this report should be done with security experts in order to avoid not only ad of bad quality, but also to protect the user from malware advertising.

## Ranked-privacy preserving granular report

This is a granular report, meaning that each row maps to a display and the label (click/sale) is associated with the exact display for which it occurred. This report can, for example, be used for billing on the publisher side and for fraud detection, performance or billing on the advertiser side.

The aim is to offer advertisers and publishers the flexibility to trade-off between variables and metrics to fulfil their needs while preserving the identity of each user.

To do so, the Gatekeeper provides a granular report to the advertiser with k-anonymity  on  **a subset of reported variables (that we call here 'protected variables')** with some delay. All the variables that could be used to link the protected variables (a.k.a. personal identifiable information (PII)) is obfuscated by the process described below. However, variables (non-protected, one-sided variables) like the click log (that is not available anywhere in the publisher report or data) that is of paramount importance to the advertiser is always recorded at the display level. Therefore, we are  **preventing any unique join key to appear in the reports**  to ensure privacy whilst giving as much information as possible to the advertiser and the publisher.

### Non-protected variables

Many variables are relevant for one player only, like ad labels (click, view, click id), ABTest ID (see ABTest section below), and the interest group for the advertiser. These variables should be hidden to the other players and do not need other specific attention in this report, they do not represent any risk with regard to the user privacy and can be reported as is per display.

### Protected variables

Some variables are relevant for both advertiser and publisher and present a privacy risk if exposed as is in the reports. We are taking the examples of:

-   Contextual data,
-   the price, the DSP winning the ad and the click-through domain.

As contextual data are very valuable but not all use cases require the same type of contextual data, the report allows the advertiser and the publisher to rank raw contextual data (e.g. display size) or transformed contextual data (e.g. truncated URL) by importance in order to have as much information as possible on most important data (for them) whilst forbidding colluding publisher and advertiser to join a user with an interest group.

The report relies on k-anonymity, meaning that a feature is available only if at least k records are similar (e.g for a URL to be revealed, you need at least k requests on this URL and the same higher ranked features' modalities).

This automatically makes full URLs, with potential PII encoded therein, not usable in such report. The need for providing the transformation to remove these privacy-breaching parts of the URL is thus transferred to the advertiser and publisher (and not the reporter) incentivized by the k-anonymity constraint.

The technical implementation details and some examples of this mechanism can be found in the appendix. The main point being that a ranking (provided by the advertiser or publisher) allows the reporter to know which dimension should be sacrificed in case k-anonymity is not respected for a group of reported rows.

One important point here is that for k-anonymity to work, continuous value feature like price needs to be pre-processed either by adding noise or by bucketizing them.

This version of a report allows for protecting that privacy (as there are k events, it is not possible to do the link with a particular user, and therefore the publisher can't learn the users' interest groups).

### Value proposition

This report allows for great flexibility, as it enables actors to rank the features by order of importance according to them. If one advertiser is very interested in geolocation and another does not care, they can rank this feature differently. The k-anonymity annihilates the incentive to create an individual domain per user and to hide PII within a feature, since any granular grouping would be hidden.

This also allows for and encourages the advertiser / publisher to find the right tradeoff between latency and precision (the longer the delay, the more granular the report would be).

This report avoids hiding useful information to the advertiser/publisher that cannot be used to identify users anyway.

Please note that only one of such reports can be requested per display, meaning it is not allowed to request multiple reports to include the same display with different features rank options.

# ABtest and private unique id count for K-anonymity reporting computation

AB testing, which relies on splitting users into several populations and presenting each population with different features, is at the core of many user improvement on the web. As of today, AB testing in web advertising rely mainly on uid hashing.

The proposal:

-   The browser sends at bid time, along with the IG, an ABTest_ID  between 1 and 10 000 (can be 1 000, or even 100, there is a tradeoff to consider). This ABTest_ID is semi-stable per user (For example, we could imagine that the number for each user would be reset randomly every 2 months)
-   The Gatekeeper hashes this ABTest_number differently for all stakeholders (advertisers and publishers), and transform this to an ABTest_ID_advertiser between 1 and 10. This ID can be used for AB testing, and can be transferred safely in the granular report, as it cannot be used for joining (because the ID for one user is different in all reports). This allows for doing user-level ABtesting without breaching privacy.

For the computation of the Ranked k-anonymous report, this also allows for computing the number of unique uids  without having the uids ever sent to the Gatekeeper. Indeed, the Gatekeeper uses the ABTest_number to compute the number of unique ids. This means that if k = 3 for instance, the Gatekeeper checks if there is at least 3 different ABTest_IDs corresponding to the request. It is possible that sadly two different users have the same ABTest_ID, but this is quite unlikely so the impact on the report should be very limited.

This approach means that no uids are ever sent by the browser - and if 10,000 seems like a lot, consider that with billions of uids, this would reduce the number of uids per semi-stable group to hundreds of thousands, clearly not enough to identify unique users.

For incrementality measurement, an unique id with low granularity and long semi stability (e.g 10) could be available for all stakeholders in order to be able to measure incrementality efficiently. As this number is available to everybody, it will need to be a protected variable by the ranked privacy preserving report.

# Appendix

## Algorithm used to hide some contextual features in the ranked privacy-preserving report

The idea here is to give as much information to the advertiser and the publisher without allowing them to associate a user 1st party identity on the publisher side with a user 1st party identity on the advertiser side.

The advertiser ranks protected variables (like contextual data) by the order of importance to him. He can also provide a set of transformations (truncation, bucketing, ...), and then the reporting works as follows:

-   The reporter (assumed to be the gatekeeper for Sparrow) counts the number of unique rows (this is done in an anonymous way) for each modality of the first variable.  Please note that this count is done on the publisher domain level, and includes displays that were done on  **other interest groups**  and by **other DSPs.  This means that one display may be enough for an advertiser to get contextual information, provided another display was done by another actor on similar contextual data.**:
    -   For all rows with a variable's modality (e.g. the URL domain of a given website) appearing more than a threshold k in the whole dataset, keep the actual value.
    -   For all groups with a number of unique rows lower than k, return "Hidden" instead.
        -   For this variable, should the number of "Hidden" rows in the group be lower than k, assign to "Hidden" the smallest group with size greater than k, so as to preserve privacy.
        -   "Hidden" becomes the new "value" of the variable for the rows of this group, allowing the algorithm to continue if possible and have lower-ranked protected variables not hidden.
-   The reporter groups by the n first variables he is asked to groupby, and checks if the group still has a number of rows higher than k, and then provides with the actual value or "Hidden" accordingly.
-   Note: This is only for protected variables that could be used to do the join to match a display to a specific user and therefore a specific interest group. All other variables (non-protected variables) do not pass through this procedure.

By so doing, the report is privacy-protected (as there are k events, it is not possible to do the link with a particular user, and therefore the publisher can't learn the user's interest groups).

The publisher report is produced using the same principle. It also ranks features by order of importance as they have defined them.

## Example of ranked privacy-preserving report

#### The data:

Here we have 9 displays, on 9 different users, with 4 publisher features, the publisher_UID, the Domain, the subdomain, the size, and a label. There is also a display id, not shared between the advertiser and publisher.

For this example, we take k=2.

The data is the following:

| Display id | publisher_UID | Domain | Subdomain | Size | Label |
|------------|---------------|--------|-----------|------|-------|
| 1          | uid1          | A      | A1        | 5    | 0     |
| 2          | uid2          | A      | A1        | 10   | 1     |
| 3          | uid3          | A      | A2        | 10   | 0     |
| 4          | uid4          | B      | B1        | 5    | 0     |
| 5          | uid5          | B      | B1        | 10   | 1     |
| 6          | uid6          | B      | B2        | 5    | 0     |
| 7          | uid7          | B      | B2        | 10   | 0     |
| 8          | uid8          | C      | C1        | 10   | 1     |
| 9          | uid9          | C      | C1        | 10   | 0     |

Now, we show two different reports that could have been requested by the advertiser. Note that, as explained above, the advertiser is only allowed one report, not the two of them.

##### Example 1: the order of importance of the features are the following: uid, domain, size and then subdomain.

The report would look as follow:
| Display id | publisher_UID | Domain | Size   | Subdomain | Label |
|------------|---------------|--------|--------|-----------|-------|
| 1          | Hidden        | A      | Hidden | Hidden    | 0     |
| 2          | Hidden        | A      | Hidden | Hidden    | 1     |
| 3          | Hidden        | A      | Hidden | Hidden    | 0     |
| 4          | Hidden        | B      | 5      | Hidden    | 0     |
| 5          | Hidden        | B      | 10     | Hidden    | 1     |
| 6          | Hidden        | B      | 5      | Hidden    | 0     |
| 7          | Hidden        | B      | 10     | Hidden    | 0     |
| 8          | Hidden        | C      | 10     | C1        | 1     |
| 9          | Hidden        | C      | 10     | C1        | 0     |

Uids are obviously all hidden as there are not two uids per uid. Note that this does not stop the algorithm of returning "Hidden" as a feature value when for instance the value is missing. All domains are available because there are more than two displays on each domain.

For size: all sizes of domain A are hidden. Why? Because if indeed there are two displays on domain A with size 10, as there is only one remaining display (of size 5), this means that the "Hidden" report size is lower than k, and we need to hide the size of displays done on domain A.

Then subdomain: all are hidden but for the C domain, for the same reason. Label, however, is always available. This is because the publisher does not know of the label, and therefore this does not need to be aggregated.

##### Example 2: the order is now publisher_UID, domain, Subdomain and lastly size.
| Display id | publisher_UID | Domain | Subdomain | Size   | Label |
|------------|---------------|--------|-----------|--------|-------|
| 1          | Hidden        | A      | Hidden    | Hidden | 0     |
| 2          | Hidden        | A      | Hidden    | Hidden | 1     |
| 3          | Hidden        | A      | Hidden    | Hidden | 0     |
| 4          | Hidden        | B      | B1        | Hidden | 0     |
| 5          | Hidden        | B      | B1        | Hidden | 1     |
| 6          | Hidden        | B      | B2        | Hidden | 0     |
| 7          | Hidden        | B      | B2        | Hidden | 0     |
| 8          | Hidden        | C      | C1        | 10     | 1     |
| 9          | Hidden        | C      | C1        | 10     | 0     |

For similar reasons, a lot of categorical features are put to "Hidden", but here instead of the subdomain being hidden on B, it is the size. This shows the flexibility of this report, allowing the advertiser to choose which feature is most important to them.

## Privacy considerations

While K-anonymity is not foolproof for privacy, it is a fairly common standard; however, we should be aware of several possible attacks that could risk lowering the level of privacy.

First, with the proposed reporting scheme, it may be possible to identify some user, or at least lower the effective 'k', by joining the publisher logs with several partners reports.  
This would be made possible by the fact that each actor can ask for a different set of reported variables: Each report provides some constraints on the possible assignations between the publisher side uid and the displays, and in some edge cases there could be enough constraints to fully identify a user.

Here is the simplest example of such an attack.  
Let's assume that k = 2, and that there are exactly 4 displays in the reporting period, distributed as follow;  
User 1 Display 1 Domain D1 Size S1 Partner A Interest group A1  
User 2 Display 2 Domain D1 Size S2 Partner A Interest group A2  
User 3 Display 3 Domain D2 Size S1 Partner B Interest group B1  
User 4 Display 4 Domain D2 Size S2 Partner B Interest group B2

Partner A asks for the report on domain, and partner B for the report on size.  
Those two reports are as follow:  
Report A  
Domain D1 partner A interest group A1  
Domain D1 partner A interest group A2

Report B  
Subdomain D2 size S1 partner B interest group B1  
Subdomain D2 size S2 partner B interest group B2

On top of that, the publisher is able to collect the following log:  
User 1 Display 1 Domain D1 Size S1  
User 2 Display 2 Domain D1 Size S2  
User 3 Display 3 Domain D2 Size S1  
User 4 Display 4 Domain D2 Size S2

We learn from partner A report that it made the 2 displays on domain D1. We can, therefore, deduce from publisher data that the two displays of partner B are on domain D2.  
Crossing this information with the report of partner B, we know that the display with 'size =S1' on partner B report is a display for user 3, and retrieve its interest group.

  

While this kind of attack seems quite impractical to build and requires a collusion between the publisher and some advertisers and  **a specific ad distribution (not controlled by advertiser / publisher)**, it could be mitigated by randomly removing and / or duplicating some lines from the reports. We think, however, that this is an unnecessary overhead as the effort required to just identify any given user is significantly higher than the potential value gained by such attacks.

Other possible attacks include "Homogeneity" attack if for example all users getting displays on some domain share the same interest group, or "probabilistic matching": when the same user gets some displays at different times, it might be possible to learn something on this user by comparing the reports at the time of each of those displays.

But it is worth noting that all those attacks are only made possible by comparing data collected by the publisher (with a publisher side user identifier) and data found in the partner reports, and only with some specific display distribution, that seem very hard to control, even with collusion.

Please also note that only one report is available per IG and domain. This means that for the collusion to work (assuming the user distribution allows it, which seems unlikely), all publisher would need to collude exactly the same way with all advertisers for it to be valuable at scale.

  
Therefore, even if we think that the actual privacy risks are low, we propose that access to the reports be conditioned to a legally binding agreement that those two sources of data are never crossed.

_Basile Leparmentier_  [b.leparmentier@criteo.com](mailto:b.leparmentier@criteo.com)  
_Paul Marcilhacy_  [p.marcilhacy@criteo.com](mailto:p.marcilhacy@criteo.com)  
_Jeremy Morvan_  [j.morvan@criteo.com](mailto:j.morvan@criteo.com)  
_Alexandre Gilotte_  [a.gilotte@criteo.com](mailto:a.gilotte@criteo.com)  
_Amin Mantrach_  [a.mantrach@criteo.com](mailto:a.mantrach@criteo.com)  
