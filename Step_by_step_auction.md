
# SPARROW - step by step auction resolution
### Background

We tried to describe as well as possible the full auction mechanism is the SPARROW readme document, but we think graphs illustrating how multiple actors interact would be a great help for understanding the SPARROW flow.
Hence this new document.

### Auction step 1 : bid request call
![SPARROW step 1](https://user-images.githubusercontent.com/64090118/95061840-6f136780-06fc-11eb-9dc6-b64f6e8ed6b4.png)


In this diagram, the call to the ad server is intended to work exactly as today. 
The new call toward the browser (to get IG information) is the SPARROW addition. In this call, a SSP in charge of the auction is defined, and a shared opportunity id is given between the two calls, in order to be able to reconcile bids in the future.

### Auction step 2: Request sent to DSPs and Gatekeepers
![SPARROW step 2](https://user-images.githubusercontent.com/64090118/95061860-75a1df00-06fc-11eb-988d-c07a506edfaf.png)
As in the previous diagram, the new part is the browser/gatekeeper. As third party cookie disappeared, the "standard" bid request only contains contextual information. 

### Auction step 3: DSPs / Gatekeepers answer with bids and auctions are performed
![SPARROW step 3](https://user-images.githubusercontent.com/64090118/95061858-75094880-06fc-11eb-9ad6-d6fec73019e4.png)
DSPs answer bid as today. The new flow is that gatekeepers all send their bids to the SSP defined in step 1. 
Questions remain on what information are sent along with Gatekeeper bids. 
The less disrupting proposition would be for Gatekeepers to send along with the bid the advertiser for which it is doing the ad. This would allow for ad Quality to be still handled at the SSP level.

Another way would be for SSP to communicate Ad quality guidelines to Gatekeepers in advance so that it can itself handle ad quality before transferring bids to the SSP.
Gatekeepers' ads can only be rendered in a fence frame whilst standard bids have no such restrictions. 
URL for rendering Gatekeepers ads are annonymized and auctions are run by the SSP, as today. 

### Auction step 4: Winning bid are transferred and ad is rendered
![SPARROW step 4](https://user-images.githubusercontent.com/64090118/95061862-763a7580-06fc-11eb-874d-791dfb833392.png)
The ad chosen by the ad server is rendered. For SPARROW ads, click, view and rendering information is sent directly to Gatekeepers who will use it to generate reporting proposals.

### Reporting: 
![SPARROW reporting](https://user-images.githubusercontent.com/64090118/95061861-763a7580-06fc-11eb-9ecf-405cf937f7e2.png)
The reporting is sent to actors following the rules defined [here](https://github.com/WICG/sparrow/blob/master/Reporting_in_SPARROW.md) 
