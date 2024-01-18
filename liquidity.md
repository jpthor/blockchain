# Overview

The protocol has 4 user-types:
- Vault Owners
- Traders (L1 swaps, arbs, Perps)
- Borrowers

# Pools
Pools provide pricing function, as well as allowing TVL to accumulate.

## Assets
The network starts with a USDC, ETH and BTC pool. There are two derivative assets: USB and BASE. 
BASE is the base asset (like RUNE). USB is the stablecoin. 

[USDC : BASE]
[ETH : BASE]
[BTC : BASE]

There is no need to hold the BASE asset. It cannot be directly swapped to. It simply is the pricing function between pools. 

## Add/Remove Liquidity
All liquidity adds are into the Savers function, which mints synths. The protocol immediately mints the exact equivalent in BASE and deposits into the other side as PoL. 
The reverse ocurrs on remove-liquidity. Since BASE cannot be bought directly, all Savers can add, PoL is matched, and then all Savers can leave again with PoL destroyed. 



# Vaults
Anyone who deposits L1 assets into vaults are given Vault Units which is equal to their share of the network TVL at the time. 

## Nodes
Nodes compete on bonded Vault Units to enter the validator set. Node competition drives up TVL. 

## Savers
Savers do not compete to enter the validator set. They simply earn a small % of network fees than nodes (some discount, like -33%). Saver adoption drives up TVL.



# Swaps
Swaps (streaming by default) are between L1s. Swaps ensure revenue to the protocol. 

## Trade Assets
Users can wrap representative assets to trade quickly between pools. Instead of settling to L1, the asset is settled to the native chain. 
Trade assets incur a small -ve interest fee to encourage quick useage and to pay for their security (and prevent accumulation). 
The -ve interest rate is coupled to the amount of trade assets to vault assets. 

## Order Books
Trade assets can be used for buy-sell limit orders to improve the arb function. 

## Dex Agg
Supported

## Memoless
Supported

# Security

## Economics
Nodes should own most of the TVL themselves, so if they steal, they just steal their own money. 
Savers rely on nodes not stealing. Large Savers should just become Nodes to ensure no collusion. 
Vault Units cannot be used for bond until after 30 days. This ensures that all Nodes put up large amounts of capital at risk in the network first. 

###############

All the above features are essentially risk-free. There's no speculative asset. Participants compete for revenue-share. 
Every participant can come and go with exactly their contributed capital (minus fees plus revenue)

The below features introduce speculation and risk, but if handled correctly, can supercharge TVL growth and adoption. 

##############

# Derivatives
Derivative assets amplify the protocol and provide speculation. 
It provides price action on BASE, which causes more TVL to be accumulated, which makes Node TVL share rapidly increase. 
As lending is adopted (people take out loans, mint stablecoins, or trade on the perps), TVL should increase. 

## Stablecoin
A mint-burn stablecoin function is provided, same as TOR. It is transferable. It buys and burns BASE. 

## Borrowing
Users can deposit L1, which buys and burns BASE. The can borrow against their L1 at 200% CR. Their debt is denominated in USB and streamed to USDC. 
They pay a small interest rate to ensure revenue to the protocol. 
They do not need to be liquidated or have an expiry. 

## Perpetuals
A classic Bitmex style Perps exchange is provided. The PnL asset is USB.
It has liquidations and daily funding rate.  

# Risk
All derivative mint-burn and Perp trading accumulates fees (as well as liquidations). 
In theory this should double the capital efficiency of the network because much more "swaps" across pools will happen, and increase revenue. 
As long as no nodes leave with the TVL, the entire thing can unwind safely back to 0 again. This is because there is no way to directly buy and sell BASE (and profit on the speculation outside of the network).

The only issue is that the speculation on BASE Asset will bring in more TVL and nodes can unbond and leave. This is where the value leakage comes from. 
If they do, the system will in theory be insolvent, as although the derivative asset holders will be able to mint back to BASE and leave, some Savers (or Nodes) will be left holding the bag. 

## Solution
To counteract this problem, two things can be done:
1) Nodes are paid their fee-revenue as USB, which accumulates and is auto-swapped to their L1 address (like affiliate-collector module).
This means their revenue is realised always, and is tracked separately to their principle.

2) When Nodes leave, if their claim on the Vault Assets (by redeeming their Vault Units) is higher than their original deposit, they forego 50% of the gain in principle.
This means they leave behind 50% of the gain in principle (due to BASE speculation) to staying nodes. 

3) All Derivative Asset swap fees (and liquidations) should burn BASE forever. This means those staying in have some mechanism to speculate on future adoption.

## End Result
There is no external speculation on BASE, so protocol risk is entirely contained to the network. Nodes secure their own TVL (plus some extra from Savers and Traders). 
Nodes compete for revenue share of the network. 
Derivative assets provide features and speculation, which should increase adoption. 

To prevent system insolvency (the last nodes holding the bag), staying nodes are granted mechnisms to invest in the future of the protocol. 
This should work as long as the network can process swaps and charge fees. 









