# Overview

## Names
Alpine
Abex Protocol
Actex Protocol
Adex Protocol
Aflex Protocol
Agorix Protocol
Ajarix Protocol
Akarix Protocol
Alarix Protocol
Altonix Protocol
Alpex Protocol  <<<<<<<<<< Alp(ha), pe(rps), ex(change)
AlpineX Protocol
Amortix Protocol
Anchorix Protocol
Apex Protocol
Aquatix Protocol
Ardex Protocol
Asdex Protocol
Asterix Protocol
Atonix Protocol
Avarix Protocol
Awarix Protocol
Axona Protocol
Azadix Protocol

## Genesis
The network starts with 4 nodes with ETH and BTC synced. $100k of BTC, ETH, USDT, USDC is placed in the vaults, and 100k BASE is minted to match into each of the pools. There is no RESERVE. BASE value starts with a notional pool value of $1.00. 
Other nodes can then LP into the vaults to join and churn into the network. 

# Nodes
Node operators provide LP capital (single-sided). BASE tokens are minted to match their LP capital based on current pool price. The number of BASE minted is their LP units, which becomes their Bond Units. Thus Node Bond Value is measured in units of BASE, and can be priced in $USD at all times. There is an finite number of slots for nodes. Nodes do not need to split up their capital across multiple nodes, because they are protecting their own capital. System Income is split based on Bond Units, so large nodes earn more. 

Large nodes only get 1 signing power for block validation and TSS signing. This means all nodes have equal voting power, regardless of their Bond Value. Competition into bonding should ensure Bond Value always goes up. 
Anyone can LP and delegate their LP tokens to any node. LPs can delegate across pools, since LP tokens are universally priced no matter the pool. 

## Economic Security 
Nodes own all the TVL all the time. 2/3rds can only steal their money, plus 50% more. The best defence against a rug is just running your own node. 

## Churning
Churning should be slow, once every 30 days. Nodes can only BOND/UNBOND and redeem their LP when churning. This should give time for Stables/Savers/Borrowers/Perps/Traders to see a slow-rug and redeem their IOUs before nodes take their L1 capital. 

Nodes should be attracted to the network based on:
- Yield
- Speculation on BASE asset brings L1 capital
- Adoption of Derived Assets brings L1 capital

The nodes that stay in for the longest should make the most money.

## Swaps
Pools are XYK for simplicity. Everything is default-auto-1block-streamed (Swaps, Savers, Loans). LP add-remove doesn’t need streaming because BASE is insta-minted/burnt at the correct price. 
 
Affiliate Fees and Memoless are supported.

## Liquidity Fees
Liquidity Fees operate on a global minBP (5-10 BP), or the affiliate fee, whichever is higher. 70BP affiliate fee means more income for protocol. 

## Trade Assets
Trade Assets are used for limit orders and arb. Trade assets are kept outside the pools and attract small -ve interest depending on how much is outstanding relative the depths of the pools. 

# Derived Assets
Derived Assets allow mint/burn of BASE into the secondary asset based on the price of an underlying L1 pool. Derived Assets are IOUs.

## Savers
Savers use derived assets and earn 1/3rd the fees for each pool. Savers deposit L1 capital, which buys and burns BASE.

## Stable
The STABLE is a derived asset which uses one or more stablecoin pool prices. Stables can be exported to external EVMs by using the Router which also contains ERC-20 mint/burn. 

## Borrowing
Borrowers use derived assets. Borrowers deposit L1 capital, which buys and burns BASE into STABLE. At 200% CR, half of it is minted back and swapped back out. Borrowers should pay an interest rate to provide income to network while the loan is open. 

## Perps
Perps use derived assets. A funding rate and liquidation mechanism is required. Up to 100x leverage possible. The PERP module keeps track of the BASE value of all tokens inside PERP positions. When a trader opens a position the PERP pool increases and it decreases when they exit. Traders compete amongst themselves. The maximum the winning side can win is the value of the PERP pool (in BASE). This keeps the risk isolated to just the PERP pool and stops the protocol taking the other side of trades. A trader is liquidated when their position is evaluated to 0 by the protocol, which just deletes them. This effectively moves their capital to the winners, who can claim it. Unclaimed capital (happens when one side is liquidated, but the other side doesn’t close to claim and the price moves back) just bolsters the PERP pool, and keeps it there the next time a winner is in a winning trade. This is effectively the Insurance Fund.

# Airdrop 
To incentivise early adoption, all participants in the network earn rights to a future airdrop which will mint 10% extra the outstanding BASE supply and can be claimed. Addresses earn airdrop points which accumulate until the day the airdrop is activated. 

If the network has $1bn L1 in the pools, then BASE is worth $1bn so the airdrop is worth $100m. 

**LPs/Nodes:** LP Units * Age
`BOND:nodeAddress:airdropAddress`

**Savers:** Deposit in Base value * Age
`+:asset:airdropAddress`

**Borrowers:** Deposit in Base value * Age
`$+:collateralAsset:debtAsset:airdropAddress`

**StableMinters:** Deposit in Base value * Age
`=:X.USD:baseAddress`

**Traders:** Fees paid in Base value * Age
Their trading address

**Affiliates:** Fees collected in Base value * Age
Their affiliate address


## BASE Launch
Airdrop height is set to 0, but nodes can vote a future blockheight to enable it. Once consensus on a height is reached, the airdrop and launch goes ahead. Nodes will want to delay the airdrop long enough to maximise adoption metrics. The longer they wait the more valuable the network. 

- 10% is minted into a module
- All participants claim their share based on airdrop points
- BASE can be bought/sold in the pools

For every $1 that BASE is bought by speculators, $1 of TVL is deposited into the network. Since only 10% is circulating from the airdrop at the start, the risk of volatility is low. Nodes profit the most from speculation since they own the TVL. 

## Router
The router deposits and churns assets into vaults. The router owns ERC-20 (777) contracts that can mint new tokens which are wrapped derived assets or bond units. Thus the network can export tokens:
- Export $USX stablecoin
- Export derived BTC/ETH
- Export bond units for yield-farming

Dev deploys the ERC-20 (777) and sets the owner to the router. Only the protocol can then mint. If new router, the old router must migrate owner. To track vault privileges:

- Once `router.transferAllowance(newVault)`is called, 
- `mapping isVault(address,bool)` turns off `oldVault` and turns on `newVault`.
- Thus vaults can expand and contract, but only the latest vaults have the privileges to be owner
- Function `isOwner(isVault(msg.sender))`
- Any vault can then mint on the wrapped asset contracts

Anyone depositing wrapped assets into the router will cause them to be burnt, and minted back into the protocol. 





