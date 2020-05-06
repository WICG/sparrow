# SPARROW
SPARROW, Secure Private Advertising Remotely Run On Webserver, is an enhancement of TURTLEDOVE proposal aiming at providing more value for the ad ecosystem, more control and transparency, and safeguarding the user experience, whilst keeping all the privacy guarantees.
SPARROW also provides the opportunity to extend advertising use cases coverage.

## Motivation

SPARROW keeps TURTLEDOVE objectives, i.e:

-   People who like ads that remind them of sites they're interested in can choose to keep seeing those sorts of ads.
-   People who don't like these types of ads can choose to avoid seeing them.
-   People who wonder "how the ad knew" what they were interested in can get a clear, accurate answer.
-   People who wish to sever their association with any interest group with which they are associated can do so and can expect to stop seeing ads targeting the group.
-   Advertisers cannot learn the browsing habits of specific people, even those who have joined multiple interest groups.
-   Web sites cannot learn the interest groups of the people who visit them.

While adding the following ones:

-   Advertisers can retain **campaign control and performance** in so far as this does not infringe user privacy.
-   Appropriate **control** over ad safety, brand safety and  **transparency**  in billing is provided to both advertisers and publishers.
-   SPARROW brings a better support to more advertising use cases, not only retargeting.
-   **User experience**  while browsing the web is preserved.

We believe that these objectives are key to a healthy ecosystem, ensuring value for advertisers and revenue for publishers. In more details, the SPARROW proposal supplements TURTLEDOVE on the following items:

## Campaign control and Performance for Advertisers

-   **budget management**: ability to start / stop / increase / decrease the spend on a campaign with low latency. Common advertiser use cases include campaign management at an hourly and currency unit granularity.
-   **Attribution**: ability to define the attribution method that serves their needs best.
-   **Performance**: ability to measure and optimise for advertising KPIs such as ROAS, click-through rates, conversion rates, cost per action, etc...
-   **AB testing**: ability to measure performance, such as marketing lift.
-   **Fraud prevention**: ability to detect and blacklist suspicious supply (fraud is often very specific to a publisher, localized in time, and comes with specific patterns.)

## Advertising beyond re-marketing:

As such, it seems that TURTLEDOVE focuses mainly on re-targeting ("reminder ads"). Many adjacent use cases (Similar/Look-Alike & Commerce Audiences, Co-marketing) will be challenging within TURTLEDOVE framework. However, adding a few features can bring us closer to cover these use cases with no impact on user privacy.

-   Interest groups defined on a given advertiser should be usable for advertising redirecting outside of the original advertiser domain:
    
    -   E.g. an interest group defined on [WeReallyLikeShoes.com](http://wereallylikeshoes.com/) should be available to do branding for <shoebrand> (and thus redirecting to shoebrand.com), with [WeReallyLikeShoes.com](http://wereallylikeshoes.com/) blessing.
        
-   It should be possible to create new interest groups by combining existing, single-domain interest groups (via union intersection, NOT operations). This would still give the same privacy protection for users whilst allowing for important advertising use cases to be supported, and provide a flexible framework to create complex but aggregated audiences. These "shared interest groups" could be used for lookalike targeting, relevant targeting and retargeting for very small actors.
    

A separate page on interest groups mechanism in TURTLEDOVE and SPARROW has been written and can be found [here](https://github.com/BasileLeparmentier/SPARROW/blob/master/Interest_groups_audiences_new_building_blocks.md).

## Control and transparency

-   Ad safety for publishers: ability for a publisher to enforce a policy on the type of ads that can be displayed on its properties. E.g.
    -   "I don't want ads about tobacco, alcohol or containing nudity on my properties."
    -   "As an online news company, I don't want ads for other newspapers on my properties."
-   Brand safety for advertisers: ability for an advertiser to enforce a policy regarding websites its ads are displayed on. E.g.
    -   "As a luxury company, I don't want my ads to be displayed on sports websites."
    -   "As an airline company, I don't want my ads to be displayed on news pages related to airline accidents."
-   Transparency and trust in billing data, which must be auditable and produced by an accountable party.

Please note that ad safety and brand safety rules are to be handled at bid time, but there is also an "a posteriori" audit component to it. Advertisers and publishers must be able to verify the rules were enforced.

## User experience while browsing the web

-   User network, compute, and storage device resources should not be used to pre-fetch ad bundles (containing pictures, videos, sounds) or compute bids, especially in mobile environments where these resources are scarce and data plans limited.

# Proposal

SPARROW proposal builds on TURTLEDOVE and introduces a third party: the Gatekeeper.

The Gatekeeper is an internet-based service responsible to run interest group auctions and to generate ad web bundles, instead of the browser in the TURTLEDOVE proposal.

Since it receives real-time interest group auction requests with page contextual data, it must not be an advertiser or ad tech company, nor share any data with those actors.

In this proposal:

-   The advertiser is any company selling goods or services on a website.
-   The publisher is any company getting revenues from displaying ads on its website.
-   The ad network is any company responsible for monetizing the publisher ads inventory.
-   The DSP (Demand Side Platform) is any company charged with running ads for a given advertiser.

## Example flow

This section, based on TURTLEDOVE API example flow, describes one way this might work end-to-end. The example here is the long-term goal.

At some point in time, [WeReallyLikeShoes.com](http://wereallylikeshoes.com/) sent to the Gatekeeper a bidding model and ad web bundles (containing all elements required to render an ad). The bidding model takes as input an interest group and page contextual data. It does not need any interaction with the advertiser to be executed.

Suppose I am a regular reader of  [myLocalNewspaper.com](http://mylocalnewspaper.com/), which gets their ads from  [first-ad-network.com](http://first-ad-network.com/).

I visit  [WeReallyLikeShoes.com](http://wereallylikeshoes.com/)  and spend some time looking at running shoes. The advertiser web page decides to add me to an ad interest group for a month.

At some later point, I visit  [myLocalNewspaper.com](http://mylocalnewspaper.com/). An ad network script on the page requests ads from  [first-ad-network.com](http://first-ad-network.com/).

At that point, the ad network calls regular DSPs for contextual bids and the browser calls the Gatekeeper with interest groups and page contextual data. The Gatekeeper uses the previously provided bidding model to compute a bid.

Resulting bids are returned to the ad network which selects the winner.

If a contextual bid wins, the ad is rendered as it is today. Let's consider that an interest-based bid wins the auction.

The ad server notifies the Gatekeeper. The Gatekeeper generates a web bundle with all elements required to render the ad and transfers this bundle to the ad network.

The ad network sends back the web bundle to the browser, which renders the ad in an opaque iframe.

![SPARROW sequence diagram](https://user-images.githubusercontent.com/64090118/79886212-b29d4200-83f8-11ea-8421-a4ca329d54cf.png)

## AB testing

In order to provide advertisers with AB testing capabilities, we advocate for the browser to pass a "bucket_id" - defined locally at the user level - in the request sent to the gatekeeper. The advertiser could use the "buckets" in order do define AB test populations and take population split into account in the models sent to the Gatekeeper. Each user would be assigned for a certain time in one of the N buckets. The number  _N_  of different buckets and the frequency  _f_  at which the buckets would be known in advance by the advertiser, but remain to be defined.

## Reporting

The Gatekeeper notifies the advertiser, with a variable delay  around one minute, of the display, including the winning interest group, the ad data (campaign, product, layout), the bid value, and the publisher it was displayed on. This delay further protects the user identity from fingerprinting while keeping the ability to measure and control advertiser budgets.

Click reporting is provided to the advertiser via an impression ID in the landing URL, similarly to what is done today.

## Further considerations

All user interest groups can be sent in one request to the Gatekeeper. In order to avoid cross-interest groups targeting (this could be used to do fingerprinting and infringe upon user privacy), the Gatekeeper will ensure that bids for each interest group are computed independently.

There can be several gatekeepers competing. Which gatekeepers to contact can be defined at the ad network level.

As an incremental path of adoption, the following steps could be done first:

1.  The browser allows the creation of interest groups and sends the requests to the advertiser as they do today, without Gatekeeper.
2.  The Gatekeeper is in charge of bidding only, the ad web bundle for rendering is still provided directly by the advertiser.

# Enhancements w.r.t. TURTLEDOVE proposal

## Campaign control and Performance

Control of the campaign budget:

-   Having near real-time reporting provides near real-time update of campaign spend to advertisers.
-   Advertisers can act upon their campaign spend by notifying the Gatekeeper, which can take into account new orders without delay - whereas there does not seem to be any practical way to update all in-browser stored js bidding scripts in TURTLEDOVE.

Performance:

-   Models executed by the Gatekeeper can be more complex than those that could fit in a js bidding script.
-   The Gatekeeper ensures that models provided by advertisers are not disclosed to third parties, whereas in-browser models could be reversed-engineered.
-   Having reporting data at the display level, with the interest group and publisher information, allows for the advertisers to learn better ML models, providing more value, higher bidding and eventually more revenue for the publishers.
-   Display level reporting puts attribution back in the advertiser' hands. It gives him the flexibility he needs to define his own attribution method.
-   The bucket_id received by the gatekeeper at bidding time would allow the advertisers to run simple full-fledged AB tests to measure incrementality and test any kind of improvements. Starting/Stopping/Updating an AB test would only require an instantaneous update by the gatekeepers.

Fraud prevention:

-   Having reporting data at the display level provides the required information to investigate fraud attempts.
-   Having near real-time reporting enables the advertiser to act quickly against these.

## Control and transparency

Ad safety for publishers:

-   The ad network can filter out interest-based bid responses that are not compliant with the publisher policy.
-   The ad network knows which ads were displayed on each web site and can provide reporting to publishers.
-   Neither the ad network nor the publisher knows the interest groups corresponding to the ads displayed.

Brand safety for advertisers:

-   The advertiser can provide advanced brand safety rules to be enforced by the Gatekeeper.
-   Advertisers know which ad was displayed on which websites thanks to reporting.

Transparency and trust in billing data:

-   The Gatekeeper is accountable for fair computation of interest-based bids - whereas relying on bids produced locally on a browser could be problematic from an audit point of view.

## User experience while browsing the web

The Gatekeeper, not the browser, is in charge of resource-intensive tasks: bid computation or ad bundle pre-fetch. This will result in less impact on device resources such as memory or battery life, and less network usage.

## Additional benefits

-   SPARROW proposal does not affect the way contextual bids are managed as of today, which should ease its adoption.
-   SPARROW makes reporting possible even with a very low volume of ads per publisher for any given campaign, which happens quite often.
-   In TURTLEDOVE, the contextual bid is the only way to leverage contextual signal in the interest-based bidding script. By definition, contextual bid requests are not tied to any interest group and cannot be sent only when an IG is present, otherwise contextual request would reveal a form of interest group information. This requires either to place  _many_  contextual bids (called for all opportunities for ALL users, no matter if they belong to an IG or not) or to be blind to any contextual information when computing the interest-based bid. In SPARROW, the request contains both contextual and interest group information and is only sent if the interest group is present, greatly reducing the number of bid requests.

# Privacy considerations

TURTLEDOVE privacy objectives are:

-   Advertisers cannot learn the browsing habits of any specific people, even ones who have joined multiple interest groups.
-   Web sites cannot learn the interest groups of the people who visit them.

As in TURTLEDOVE, advertisers do not take part in interest-based auctions and ad rendering is done in an opaque iframe. Advertisers only receive display level reporting, but cannot link this data to individual users - the delay in reporting prevents timing attacks.

The Gatekeeper receives interests group x publisher data, but cannot link this data to individual users since it has no user-level information.

# Reporting considerations

We believe that the reporting mechanism exposed in this proposal would both fit the advertisers and publishers needs, and ensure user privacy. Therefore, it could replace, more simply and efficiently, other reporting mechanisms proposed in Google Privacy Sandbox.

# Regarding the Gatekeeper

## An independent player

The gatekeeper is a key element for ensuring user privacy, hiding interest groups from publishers and transferring privacy-safe data to advertisers. Gatekeepers must remain independent from other parties in the ad tech ecosystem. In particular, DSPs cannot run as Gatekeepers for their own ad services.

This independence could be ensured by a legally binding agreement and appropriate audit procedures. An industry consortium, or regulators, could ensure that gatekeepers fulfil their duties and could certify new Gatekeepers. Ultimately, in case of contractual breach, browser vendors would be the ones blacklisting Gatekeepers since interest-based display opportunities are sent out by browsers to Gatekeepers.

## Business model

Gatekeepers provide a service to advertisers, running their models to compute bids, and should be paid by advertisers. Answering bid requests is very hardware intensive but is rather straightforward in term of software. Cloud providers should, therefore, be interested in the opportunity. SSP’s could also try to provide this service, independently from their existing services. There could be multiple gatekeepers.

*Basile Leparmentier* b.leparmentier@criteo.com  
*Lionel Basdevant* l.basdevant@criteo.com  
*Paul Marcilhacy* p.marcilhacy@criteo.com
