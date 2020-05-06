
# Interest groups: Audiences new building blocks

-   [Introduction](#introduction)
-   [Interest Groups in TURTLEDOVE](#interest-groups-in-turtledove)
-   [Extension proposal for interest groups](#extension-proposal-for-interest-groups)
    -   [Single-domain interest groups](#single-domain-interest-groups)
    -   [Meta Interest group](#meta-interest-group)
-   [Motivation for the changes](#motivation-for-the-changes)
    -   [Retargeting](#retargeting)
    -   [Serving relevant ads or Awareness](#serving-relevant-ads-or-awareness)
    -   [Lookalike Targeting](#lookalike-targeting)
-   [Setting interest groups](#setting-interest-groups)
-   [Interest group tree](#interest-group-tree)
    -   [Motivation](#motivation)
    -   [Without Trees](#without-trees)
    -   [With Trees](#with-trees)


# Introduction

With this page, we would like to share some thinking about how interest groups mechanisms could be extended in order to better support some advertising use cases, while still preserving user privacy.

These are for the time being only rough ideas, and we welcome feedback from the community -advertisers, publishers, ad networks and browser vendors- to further build on these ideas.

In our [SPARROW](https://github.com/BasileLeparmentier/SPARROW) proposal, we expect this proposal or a similar one to be implemented, but this new proposal on interest groups could work for any cohort based mechanism.

# Interest Groups in TURTLEDOVE

This paragraph is a short reminder of interest groups key elements in TURTLEDOVE (link  [here](https://github.com/michaelkleber/turtledove)).

Interest groups are cohorts of users managed by the browser. They have some properties stored by the browser. We expect those properties to be accessible to the owner domain by some mechanism, not currently described.

The user can be added to these interest groups by the owner domain. The list of interest group a user has been assigned to is stored in this user's browser.

## Properties

In TURTLEDOVE, interest groups are defined as a group of users (cohort) with the following properties:

-   **Owner domain**: the domain who is allowed to add a user to the interest group (e.g. 'www.wereallylikeshoes.com')
-   **Name**  (e.g. 'athletic-shoes')
-   **List of 'reader' domains**: "The list of `reader` domains indicates the ad networks that WeReallyLikeShoes uses to run their interest-group-targeted ads".

## Adding a user to an interest group

In TURTLEDOVE, an owner domain can add a user to an interest group for a given time.

It is our understanding that whenever a user gets assigned to an interest group, the browser stores the current web page the user is on and the time of the assignement. It can be later retrieved so that the user may understand when and why he was added to a specific interest group.

In TURTLEDOVE, there are no "READ" abilities, meaning that the domain owner cannot retrieve the list of all users belonging to an interest group (IG), nor can check whether a given user is part of an interest group. It is possible to remove the user from an interest group, but without knowing if the user actually belonged to this group.

It is our understanding that in TURTLEDOVE, an interest group with owner 'www.wereallylikeshoes.com' can actually be used to target the user for another domain  (having the ad redirecting to a shoe brand for instance). It is not 100% clear if an IG should have a specific target domain or if it is let to the ad networks to decide how they are using the interest groups. In this document, we will assume that an interest group can be used to target any domain, and will ask for clarification.

An interest group can only be used if the number of users in an interest group is greater than a given threshold. This is to avoid risks of fingerprinting infringing on user privacy.

# Extension proposal for interest groups

We introduce the two following interest group types:

-   **Single domain interest groups**, close to those defined in TURTLEDOVE
-   **Meta interest groups**, a combination of interest groups using unions, intersections or exclusions.

## Single-domain interest groups

### Properties

In this new proposal, an single-domain interest group is similar to a TURTLEDOVE interest group, with three additional properties:

-   **Owner domain**
-   **Name**
-   **List of reader domains**
-   **List of builder domains**: domains with the right to use this interest group to do meta interest groups.
-   **Should_send_request**: True or False, in order to activate/deactivate interest groups.
-   **Number of users**: computed by the browser, this is for the advertiser to know if the group has enough users to be called.

### Adding a user to an interest group

In this new proposal, as in TURTLEDOVE, a user can be added to an interest group by the owner domain, with a set expiration time. The assignation is done when the user in on the owner domain, by the domain.

Here, it is even more important than in TURTLEDOVE to store the owner domain when the user is assigned to an interest group as a user can then be assigned to meta interest groups.

Should_send_request is for cases where the interest group is not for immediate use. E.g creating an IG of user going on the website during a sales period to retarget them at the next sales period. It should be possible to update interest group properties (e.g should_send_request, the list of builder domains) via a UI or API provided by the browser.

We also ask for the owner domains to have reading capabilities to the user interest groups they have themselves set when the user is on their domain. This is for practical reasons, as they have ways to track this via a first-party stable identity anyway. This would just avoid useless complexity in handling interest groups.

## Meta Interest Group

Meta interest group are defined by IG builders. A typical use case for IG builders would be DSPs or Ad Networks.

A meta interest group is an interest group defined by the union and/or intersection of  other  interest groups. it has the following properties:

-   **IG builder**: the company doing the aggregation
-   **List of owner domains used in this meta IG**: the list of the owner domain for all single domains IG used for this meta IG creation.
-   **List of interest groups**  (single-domain or meta) used for the meta IG creation.
-   **List of readers domain**
-   **Should_send_request:**  True or False, in order to activate/deactivate interest groups.
-   **Interest group formula**: The formula of intersection/union of IG used to define the meta IG.

For meta interest group, it should not be possible for owner domains to know if a given user belongs to it or not, even in case the user is on the owner website. This is to avoid possible fingerprinting. For these groups, the browser is in charge of adding the user to the meta interest group using information from existing groups.

Creation of a meta interest group can be done any time, from  a UI or API provided by the browser, and the assignation of the user is done by the browser (it does not need to be instantaneous). A meta interest group can be defined using single domain interest groups from different domains. As for single-domain interest group, the browser checks that the number of users in the newly defined interest group is above a given threshold.

Meta IG can be used as single domain IG for ad serving.

Should the user ask "Why am I seeing this ad?" for an ad linked to a meta IG, he should be provided with all owner domains, interest groups involved in the meta interest group creation.

An illustrating chart is available below in the Motivation for the changes section.

# Motivation for the changes

In a world without 3rd party cookies, interest groups are the new way of defining audiences. Without meta-interest groups, some advertising use cases are not supported. We will use cases taken from this document  [here](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md) to explain how to define appropriate audiences for  some of these advertising use-cases.

## [Retargeting](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#retargeting)

For retargeting, if single-domain interest group is sometimessufficient to create audiences, meta interest groups are needed for some specific retargeting use cases.

For "standard" retargeting, interest groups can be defined using available first-party data to create segmentation, e.g.  

-   Visitor, when the user comes on any page  
    
-   Abandoned basket: added when the user adds an item to its basket but does not buy it
-   Buyer when the user converts.

This allow to build audiences for standard retargeting, and no meta interest group is needed.

A current specific retargeting use case is called "re-engagement". The purpose is to target users that went once to the website but did not return in the last month. This could be easily done thanks to a meta interest group combining two interest groups:

-   An interest group indicating the user once went to the website, e.g wereallylikeshoes.com-users.
-   An interest group indicating if the users went to [wereallylikeshoes.com](http://wereallylikeshoes.com/) in the last month e.g: [wereallylikeshoes.com](http://wereallylikeshoes.com/)-last30days
-   Then [wereallylikeshoes.com](http://wereallylikeshoes.com/) creates a meta interest group "[wereallylikeshoes.com](http://wereallylikeshoes.com/)-moreThan30days" which is all users that are in [wereallylikeshoes.com](http://wereallylikeshoes.com/)-users but are not in [wereallylikeshoes.com](http://wereallylikeshoes.com/)-last30days. This new meta interest group is then used for targeting users.

This example illustrates how a standard use case can be better covered in the new framework.

## [Serving relevant ads or Awareness](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#serving-relevant-ads) 

Let us consider that [www.wereallylikeshoes.com](http://www.wereallylikeshoes.com/) is a small advertiser selling shoes in the state of NY. He is interested in running an adverting campaign to get new customers. He is interested in users with the following characteristics: they are interested in shoes and they live in the state of NY.

Let us consider two websites, [RunningShoes.com](http://runningshoereviews.com/), NYlocalnewspaper.com.  [www.wereallylikeshoes.com](http://www.wereallylikeshoes.com/) (it could be the DSP he is working with) is IG builder for both NYlocalnewspaper and [RunningShoes.com](http://runningshoereviews.com/) interest groups.

The following flow chart explains the workflow of the meta interest group creation.
![Meta interest group]([[https://user-images.githubusercontent.com/64090118/81148909-754ebd80-8f7d-11ea-9343-2204d37e7133.png](https://user-images.githubusercontent.com/64090118/81148909-754ebd80-8f7d-11ea-9343-2204d37e7133.png)))


This kind of audience definition would be impossible without meta-interest groups. Note that all these operation are done without any user-level information used by anybody except the browser.

As the new meta interest group is big enough (nb users > threshold) it can be used to do interest-based advertising. This example shows that the new proposal is an improvement and allows for more complex audience creation.

## [Lookalike Targeting](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#lookalike-targeting)

Defining similar audiences is often done using multiple website information, and most often includes multiple websites. One way of creating a lookalike audience would be to create a retargeting campaign, spot performing publishers and creates IG on these publishers to try to emulate a lookalike audience.

If meta-interest group allows for complex audience creation, lookalike is not fully covered, and further improvement to the proposal might be needed.

# Setting interest groups

When setting interest groups targeting the owner domain, the advertiser has a lot of information that can be used to help setting interest groups. He knows the users actions, and, even more important, thanks to his first-party data, he can compute the probability of buying a product, the number of users in each interest groups, etc...

For interest groups targeted at  **different domains**  than the owner domains, setting interest groups is much harder. Indeed, there is in the current proposals no other way to test whether an interest group is relevant other than to launch a campaign, and see if it performs.

Today a lot of advertisers, when creating audiences look at metrics like organic sales to know if there is some relevance for the computed audience. It is also important when creating meta interest groups to gather some feedback about the size of the created interest group. Indeed, the audience size is an extremely important aspect of advertising and the browser is the sole player able to compute it.

This is why we ask for an extension of the aggregated measurement API to be available for all interest groups managers (owners for single domain interest groups, IG builder for meta interest groups), in order to provide them with required information to build IG. The measurement API would provide the following information:

-   The size of the interest group, both for single domain interest groups and meta interest groups, (potentially with some noise).
-   Standard  **organic** -outside of any running campaign- advertising metrics on a targeted domain, for user in the defined IG, including:
    -   Number of visits,
    -   Number of sales,
    -   etc...

This should be done with the appropriate protections (noise...) to ensure proper user privacy.

Thanks to the aggregated reporting API, it should be possible to create smart audiences without any infringement on user privacy and to cover many advertising use-cases that cannot be covered with single domain interest groups. In addition, it still gives the user the necessary transparency and control, since he has full access to why he was targeted by a specific ad.

# Interest group tree

This is a technical proposal to ease the scalability and clarity of interest groups. It introduces the notion of priority between interest groups, and hierarchical interest groups.

## Motivation

As in TURTLEDOVE, the interest group can only be used when the number  of users is above a threshold. Let us now consider the following situation:  www.wereallylikeshoes.com  has 4 interest groups he would like to target, with the following hierarchy:

-   First, split users by the type of shoes: sport shoes and other shoes
-   Then split them between buyers and non-buyers for both of the previous groups.

This gives the following interest-groups tree, with the number of users in each interest group. note that the number of users at each level is the same: 3 500. Let us consider that the threshold is 1 000 users.

![Interest group tree]([https://user-images.githubusercontent.com/64090118/81148900-6ff17300-8f7d-11ea-851c-1ad2f4260be5.png](https://user-images.githubusercontent.com/64090118/81148900-6ff17300-8f7d-11ea-851c-1ad2f4260be5.png))
We consider two different strategies, one with trees and one without.

## Without Trees

Without trees, www.wereallylikeshoes.com would use 7 different interest groups

-   'shoes' to assign users as www.wereallylikeshoes.com users. With 3 500 users, this group is valid.
-   'sports-shoes' and 'other-shoes' to target users depending on the type of shoes they saw on the website. Both have more users than the threshold, so both can be used.
-   'sports-shoes-buyer', 'sports-shoes-non-buyer', etc. to use the most information. here, only other-shoes-non-buyer is big enough to be eligible.

www.wereallylikeshoes.com would need to use all these groups. Indeed, if he were to keep only the 4 most granular groups, he would be able to target only a third of his audience: other-shoes-non-buyers (1 200 users out of 3 500).

If  www.wereallylikeshoes.com were to keep only the top one, he would be able to retarget all his user base, but without the granularity and thus with degraded performance. The best strategy thus would be to add the all interest-groups from all three level for each user, to be as granular as possible on one hand, and to be able to target as many users as possible on the other.

This strategy means that even if the 4 high-granularity interest-groups are big enough to be targeted, three bids at least would be needed for each user. This number grows with the scale of the advertiser and is likely to be unsustainable in the long run (especially if the user belongs to both 'sports-shoes-non-buyer' and 'other-shoes-buyer').

## With Trees

With this notion of an interest-groups tree, the user would be added to the appropriate interest group, and the tree structure is sent to the browser. The browser will then only ask for a bundle and java-script bidding function (or in SPARROW, send a bid request) for the most specific interest group with enough user to protect user privacy.

In our example, the requests would work as follow:

-   If the user is either in sports-shoes-buyer or sports-shoes-non-buyer IG, the browser will send a request for sports-shoes, as it is the first level of interest group with the required number of users.
-   If the user is in other-shoes-buyer IG, the browser will send requests for other-shoes IG as the interest group is not big enough yet.
-   If the user is in other-shoes-non-buyer, the browser will send two requests for two interest groups, other-shoes-non-buyer and other-shoes, that will be answered separately. This avoids giving information (by differential reasoning) about the user being in other-shoes-buyer if there is only other-shoes IG. As soon as other-shoes-buyer is big enough, both IG will receive only one request.

Therefore, Interest-groups trees allow for more scalability, more transparency for the user (The growing number of interest-groups the user can see he was assigned to would just confuse and overwhelm the user).
