# SPARROW - step by step auction resolution

### Background
A first account of the auction mechanism in SPARROW is available in the [README.md](https://github.com/WICG/sparrow/blob/master/README.md). We try to further illustrate in this document how the multiple actors interact.

### Auction step 1: bid request call
![SPARROW step 1](https://user-images.githubusercontent.com/64090118/95061840-6f136780-06fc-11eb-9dc6-b64f6e8ed6b4.png)

In this diagram, the call to the ad server is intended to work exactly as of today. 
The new call to the browser (to get IG information) is a SPARROW addition. In this call, an SSP in charge of the auction is defined, and a shared opportunity id is given between the two calls, to be able to reconcile bids in the future.

### Auction step 2: Request sent to DSPs and Gatekeepers
![SPARROW step 2](https://user-images.githubusercontent.com/64090118/95061860-75a1df00-06fc-11eb-988d-c07a506edfaf.png)
As in the previous diagram, the new aspect is the browser/gatekeeper. As third party cookies disappeared, the "standard" bid request only contains contextual information. 

### Auction step 3: DSPs / Gatekeepers answer with bids and auctions are performed
![SPARROW step 3](https://user-images.githubusercontent.com/64090118/95061858-75094880-06fc-11eb-9ad6-d6fec73019e4.png)
DSPs respond with a bid as they do today; then gatekeepers all send their bids to the SSP defined in step 1. 
Questions remain on what information is sent along with the gatekeeper bids. 
The less disrupting option would be for the gatekeepers to send along with the bid the advertiser for which it is doing the ad. This would allow SSPs to handle ad quality.
Another option would be for SSPs to communicate ad quality guidelines to gatekeepers in advance so that it can handle ad quality on its side before transferring bids to the SSP.
Gatekeepers ads can only be rendered in a fence frame whilst standard bids have no such restrictions. 
URL for rendering gatekeepers ads are anonymized and auctions are run by the SSP, as of today. 

### Auction step 4: Winning bid is transferred and the ad is rendered
![SPARROW step 4](https://user-images.githubusercontent.com/64090118/95061862-763a7580-06fc-11eb-874d-791dfb833392.png)
The ad, chosen by the ad server, is rendered. For SPARROW ads, view, click and rendering information is sent directly to the gatekeeper who will use it to generate reporting.

### Reporting: 
![SPARROW reporting](https://user-images.githubusercontent.com/64090118/95061861-763a7580-06fc-11eb-9ecf-405cf937f7e2.png)
The reporting is sent to the various actors following the rules defined [here](https://github.com/WICG/sparrow/blob/master/Reporting_in_SPARROW.md)