# The GARP protocol
## What is GARP?
GARP is a protocol providing a bridge between the gaming world and crypto currency.
Aim is to hide the complexity of crypto to gamers and game developers but at the same time to
open the worlds to each other.

## Why?
So why is crypto relevant in the gaming context? Well it is a different way to allow moneytization,
instead of cluttering Your game with trackable cookies and advertising GARP allows You a clean way 
to get a kickback for game play. The kickback is going to be in crypto currency.

Why is that better then local points, gold or gems? Try to convert them to USD.
Crypto currency You can convert to USD or EUR or bitcoin.

## Shall I build my game on GARP only?
We suggest no. Make a GARP version of Your game. We have developed templates for Construct 3
and Javascript, so it should be fairly easy to convert Your game to crypto currency.
The Crypto is an emerging market, see this as an additional possibility to generate revenue
for Your game. Currently we support Ether-1 and Atheios which are at writing at around
0.04 USD and 0.001 USD, but remember also bitcoin was at that level. So beside the kickback,
You could see this as an interesting investment (note that this is no financial advise).

## Now to the details
We have designed a game resolution protocol (GARP) on top of websocket. This 
protocol has multiple purposes:

* Game validation
* User validation
* Game opening and closing
* Provision of game stats and ladder

The endpoint of that protocol is wss.garp.io. From there you might be directed to other domains
from a load distribution perspective.

## The details
The communication is done via json and can be done in any language. We have developed supporting 
modules in order to simplify the integration. 

Here we focus though on the protocol itself.

A word of warning: we are in a very early stage of API development, so there will 
be significant changes over time.

Currently we are in version 0.2.0. We are using the following definitions:

* Alpha stage: version below 0.5.0:  
Compatibility breaking changes can happen, though we try to minimize it
* Beta stage version: below 1.0.0:  
    Smaller changes potential compatibility breaking changes are prevented.
* Release versions like 1.x.x, 2.x.x etc:  
    In this stage we keep compatibility. New releases are published well ahead and 
    test pools are provided for early integration
    
There will be a live server which will contain the production environment which
started with wss.garp.io.

Then there will be a staging server which will contain the next version of the
framework. Once the version is deemed to be production, we will switch that version 
over. The staging server will be called wss-test.garp.io.

The next version of the SW will be called v0.3.0 and is under construction.

* [GARP V0.1.4](/en/latest/garp_v014/)    
* [GARP V0.1.5](/en/latest/garp_v015/)    
* [GARP V0.2.0](/en/latest/garp_v020/)    

