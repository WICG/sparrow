
# Interests groups in SPARROW (WIP) 

In this page we will explain how interest groups would work within the SPARROW framework. It is very similar as in TURTLEDOVE, with some extension, notably cross domain interest groups.

Please note that all changes here could also be applied to TURTLEDOVE directly.

We will also try to link different interest groups with advertising use cases. e.g:

-   [Serving relevant ads](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#serving-relevant-ads)
-   [Lookalike Targeting](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#lookalike-targeting)
-   [Retargeting](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#retargeting)
-   [Collaborative ads](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#collaborative-ads)

  

# General rules about interest groups

## In TURTLEDOVE

Interest groups are cohorts of users. They have some characteristics, stored by the browser somewhere accessible by the advertiser defining the interest group, and the user can be added to these interest groups. The Information about the user belonging to a given interest group is stored inside the user browser.

#### Interest group characteristics

In TURTLEDOVE, interest groups are defined as a group of users (cohorts) with the following parameters:

-   An owner domain: the domain who is allowed to add a user to the interest group (e.g. 'www.wereallylikeshoes.com')
-   A name (e.g. 'athletic-shoes')
-   A list of 'reader' domains: "The list of `reader` domains indicates the ad networks that WeReallyLikeShoes uses to run their interest-group-targeted ads".

#### For a user added to an interest group:

In TURTLEDOVE, the user can be added to an interest group for a given time, chosen by the owner.

It is our understanding that when doing so, the current web page the user is browsing should be added to the IG information so that the user may understand why he was added to a specific interest group.

In TURTLEDOVE, there is no "READ" abilities, meaning that the domain owner cannot retrieve the list of a user in an IG, nor can check whether a given user is part of an IG. It is possible to remove the user from an interest group, but without knowing if the user actually belonged to this group.

In an update to TURTLEDOVE, Chrome stated that an IG with owner 'www.wereallylikeshoes.com' can actually be used to target the user for another domain  (having the ad redirecting to a shoe brand for instance). It is not 100% clear if an IG should have a specific target domain or if it is let to the ad networks to decide how they are using the interest groups. In the future, we will assume that an interest group can be used to target any domain.

An interest group can only be used if the number of users in an interest group is greater than a given threshold. This is to avoid risks of fingerprinting infringing on user privacy.

## In SPARROW:

In SPARROW, there are two types of interest groups:

-   single domain interest groups, close to those defined in TURTLEDOVE
-   Meta interest groups: A combination of interest groups using unions, intersections or exclusions.

### Single-domain interest group:

#### Interest group characteristics:

In SPARROW, an interest group is similar as in TURTLEDOVE, but the "owner" of the interest group is split in two. Interest groups are defined as follow:

-   Assigning domain: domain with the right to assign the user to an interest group (e.g. 'www.wereallylikeshoes.com'), and to change characteristics of the IG.
-   IG builders: domains with the right to use this interest group to do meta interest groups.
-   Name with interest group tree (Cf. section about Interest group tree)
-   Readers as in TURTLEDOVE.
-   Should_send_request: True or False, in order to activate/deactivate interest groups.
-   Number of users: computed by the browser, this is for the advertiser to know if the group has enough users to be called.

#### For a user added to an interest group:

In SPARROW, as in TURTLEDOVE, a user can be added to an interest group by the assigning domain, with a shared expiration time. The assignation is done when the user in on the assigning domain, by the domain.

In SPARROW, it is even more important than in TURTLEDOVE to store the assigning domain when the user is assigned to an interest group as a user can then be assigned to meta interest groups.

Should_send_request is for cases where the interest group is not for immediate use. E.g creating an IG of user going on the website during sales period to retarget them at the next sales period.

In Sparrow, we ask for the assigning domains to have reading capabilities to the user interest groups they have themselves set when the user is on their domain. This is for practical reasons, as they have ways to track this via a first-party stable identity anyway. This would just avoid useless complexity in handling interest groups.

### Meta Interest group:

Meta interest group are defined by IG builders. IG builders can be two domains willing to share data, DSPs or anybody.

A meta interest group is an interest group defined by union and/or intersections of  other  interest groups. it has the following characteristics:

-   IG builder: the people doing the aggregation
-   Assigning domain for normal IG: the list of the assigning domain for all single domain IG used for this meta IG creation.
-   list of interest groups (single-domain or meta) used for the meta IG creation.
-   Readers as in TURTLEDOVE.
-   Should_send_request: True or False, in order to activate/deactivate interest groups.
-   Interest group formula: The formula of intersection/union of IG used to define the meta IG.

In our example, let us say we want to do an awareness campaign for a shoe brand named shoebrand. We want to target all user interested in shoes. Let us consider a DSP is IG builder for both:

-   [RunningShoeReviews.com](http://runningshoereviews.com/) a publisher writing articles about shoes.
-   www.wereallylikeshoes.com.

This shoe-brand is interested by user interested in sport shoes, whatever their origin. The DSP then creates a meta-interest group which is the union of interest groups from both [RunningShoeReviews.com](http://runningshoereviews.com/) and www.wereallylikeshoes.com. (we could also think of people really interested in sport shoes and ask for the intersection instead).

The browser then checks if the interest groups is big enough to ensure user privacy, and if yes, allows the DSP to run ads for shoebrand.

Note that only aggregated interest group information have been used by the DSP, and no information at all on a specific user was used aside from the browser, who is holding all interest group information.

There is no read information for meta interest groups, to avoid possible fingerprinting. For these groups, it is the browser who is in charge of adding the user to the meta interest group using information from existing groups.

Creation of a meta interest group can be done any time, and the assignation of user is done by the browser (it does not need to be instantaneous).

Should the user ask for the reason for this ad, he should be provided with all assigning domain, interest groups, and those involved in the meta interest group creation.

## Motivation for the changes:

  

In a cookieless world, Interest groups are the new way of defining audiences. Without meta-interest groups, some advertising use cases are not supported. We will use Facebook use cases here to explain how to define appropriate audiences for  some of these advertising use case.

### [Retargeting](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#retargeting):

Retargetting is the most straightforward use case. Interests groups can be defined using available first party data to create segmentation, e.g:

-   Visitor, when the user comes on any page  
    
-   Abandoned basket: added when the user add an item to its basket but does not buy it
-   Buyer when the user pays.

More complex / more refined interest groups are obviously possible, but retargetting is likely to be mostly handled via single-domain interest groups.

### [Collaborative ads](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#collaborative-ads)

Collaborative ads are often the collaboration between a brand and big marketplace (it can also be two seller of complementary products e.g an airline and hotels). In our example let us say that shoebrand want to do ads in collaboration with www.wereallylikeshoes.com targetting users who saw any sport shoes items on [www.wereallylikeshoes.com.](http://www.wereallylikeshoes.com./) The ads would be about shoebrand and a click will redirect on an item from shoebrand sold by [www.wereallylikeshoes.com](http://www.wereallylikeshoes.com./).

In this use case, single-domain interest groups are also sufficient. [www.wereallylikeshoes.com](http://www.wereallylikeshoes.com./) creates an interest groups 'shoes-for-shoebrand' and add to it all users looking at sport shoes on its website.

Then shoebrand used the interest group with [www.wereallylikeshoes.com](http://www.wereallylikeshoes.com./) to serve ads.

  

### [Serving relevant ads](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#serving-relevant-ads)

Let us consider that [www.wereallylikeshoes.com](http://www.wereallylikeshoes.com/) is a small advertiser selling shoes in the state of NY. He is interested in running an adverting campaign to get new customer. He is interested in users with the following characteristics: they are interested in shoes and they live in the state of NY.

Let us consider two websites, [RunningShoeReviews.com](http://runningshoereviews.com/), NYlocalnewspaper, and a DSP who is IG builder for both NYlocalnewspaper and [RunningShoeReviews.com](http://runningshoereviews.com/) interest groups.

DSP will use meta interest group to define an appropriate audience for [www.wereallylikeshoes.com](http://www.wereallylikeshoes.com/). He will indeed use both IG from [RunningShoeReviews.com](http://runningshoereviews.com/), indicating interest in shoes, and from NYlocalnewspaper, indicating that the user is likely to live in the state of NY.

The new interest group, '[wereallylikeshoes](http://www.wereallylikeshoes.com/)-audience' is going to be defined as the intersection of those two interest groups. The browser will then check if the size of the group is sufficient for targeting users. It is possible to do union of multiple interest groups indicating location, and union of multiple shoes related interest groups and then intersect these two unions to increase the size of the audience.

This kind of audience definition would be impossible without meta-interest groups. Note again that all these operation are done without any user level information used by anybody except the browser.

### [Lookalike Targeting](https://github.com/w3c/web-advertising/blob/master/support_for_advertising_use_cases.md#lookalike-targeting)

Defining similar audiences is often done using multiple website information, and most often includes multiple websites. One way of creating a lookalike audience would be to create a retargeting campaign, spot performing publishers and creates IG on these publisher to create a lookalike audiences. Meta interest groups allows for complex audience creation, without risks for the user privacy.

  

## Setting interest groups:

Setting interest groups for retargetting or co-marketing purposes is not trivial but not too complex: The advertiser has all information he needs to assign users to an interest group. He knows his action, and, even more important, thanks to his first party data, he can compute probability of buying a product, the number of users in each interest groups etc..

For meta interest groups, or any interest groups targetted at domains different from the assigning domains, setting interest group is way harder. Indeed, the only way to check an interest group is relevant is to launch a campaign, and see if it is performant.

Today a lot of advertisers, when creating audiences look at metrics like organic sales to know if there is some relevance for the computed audience. It is also important when creating meta interest group to have feedback about size of the created interest group. Indeed, size of the audience is an extremely important part of advertising and the browser is the sole player able to compute it.

This is why we ask for the aggregating measurement API to be available for all interest groups managers. The measurement API would provide the following information:

-   The size of the interest group, both for single domain and meta interest groups, (potentially with some noise).
-   Standard  **organic**  advertising metrics on a targeted domain(with the appropriate noise to preserve privacy) including:
    -   Number of visits,
    -   Number of sales,
    -   etc...

Thanks to the aggregating reporting API, it will be possible to create smart audiences without any infringement on user privacy, and to cover many advertising use cases that cannot be covered with single domain interest groups. It also still allows for transparency and control via the user, who can understand why he was targeted by a specific ad.

## Interest group tree:

This is a technical proposal to ease the scalability and clarity of interest groups. It introduces the notion of priority between interest groups, and hierarchical interest groups.

##### Motivation:

As in TURTLEDOVE, the interest group can only be used the number of users is above a threshold. Let us consider that www.wereallylikeshoes.com  has 4 interest groups he would like to target:

-   two types of shoes: sport shoes and other shoes
-   Buyers and non-buyers for both of the previous groups.

Let us consider two different strategies, one with Trees and one without. Without trees, www.wereallylikeshoes.com would use 7 different interest groups

-   'Shoes' to assign user as www.wereallylikeshoes.com user.
-   'Sport-shoes' and 'other-shoes' to target users depending on the type of shoes
-   'Sport-shoes-buyer', 'Sport-shoes-non-buyer', etc.. to use the most information.

  

www.wereallylikeshoes.com would need all these groups as if he only kept the 4 smallest, It is possible that none reach the sufficient number of users, or only one of them. The advertiser will always want to have the finest possible interest group, but still over the number limit.

This strategy means that even if the 4 lowest granularity interest groups are big enough to be targetted, three bids will be need for each user at least. This numbers grows with the scale of the advertiser and is likely to be unsustainable over the long run (especially if the user belong to both 'Sport-shoes-non-buyer' and 'other-shoes-buyer' for instance) .

#### Interest group tree specification:

With the interest group as tree, the user would be added to the appropriate interest group, and the tree structure is sent to the browser. The browser will then only send request for the most specific interest group with enough user to protect user privacy.

In our example, let us consider that 'Sport-shoes-buyer' and 'Sport-shoes-non-buyer', have enough user to be used, and that only 'other-shoes' is big enough (therefore 'other-shoes-buyer' and 'other-shoes-non-buyer' are too small).

A user would be added only to the most specific group (e.g 'other-shoes-buyer' or 'Sport-shoes-buyer'). When a request is send, it would work as follow:

-   If the user belongs to 'Sport-shoes-buyer' he would only get requests for the 'Sport-shoes-buyer' interest group
-   If the user belongs to 'other-shoes-buyer', he would get a request for 'other-shoes' only, as 'other-shoes-buyer' is too small.
-   If the 'other-shoes-buyer' becomes big enough for a request to be sent, but 'other-shoes-non-buyer' is still not, the browser would send two requests, one for 'other-shoes-buyer' and one for 'other-shoes', in order to protect user privacy.
-   As soon as interest groups are big enough, the browser will send requests for the right interest groups.

Interest group tree therefore allow for more scalability, more transparency for the user (if the user sees 1 000 interest groups instead of only one, it will be though for him to understand what is going on).
