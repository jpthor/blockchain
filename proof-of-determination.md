# Proof of Determination

## An egalitarian method to launch Proof of Stake networks. 

>We propose a novel method to a launch and scale the validator set in Proof of Stake networks, as well as offering a fair, liquid and accessible manner to distribute tokens by using a censorship resistant smart contract blockchain to seed the network. A PoS network is prepared by first deploying an election contract as well as a bridging contract that links the two chains together. The PoS network is hard-coded with a starting validator count `n`, the beginning block reward `BR`, a bond period `T`, a cycle time to add additional validator slots `c`, and the maximum number of validators `m`. Validator-elects then run the PoS network software, as well depositing a “Proof of Determination” (PoD) into the election contract. The PoD contains their PoS public key, a nonce, and the hash of their keys and nonce. Since the election contract is permissionless, any validator-elect can attempt to add themselves. The validator-elects attempt to find the lowest possible hash they can by incrementing the nonces.  

>Once the validator-elects observe the `n` number of unique public keys in the election contract, they can attempt to propose the genesis block on the PoS network. The round-robin validator order is deterministically set by the value of the PoDs in the election contract, ordered in ascending order. The genesis block reward is distributed, `BR/m` units of tokens emitted to `n` validator-elects, incrementally locked beginning at `T`, and ending at `2T`, in increments of `c` blocks. This ensures a smooth and continuous lock/unlock cycle for validators. Every period of `c`, a new validator slot is opened up, such that after a certain number of `T`, the intended capped number of validators `m` is achieved (such as 100). New validators continue to elect themselves into the election contract, with the lowest-value PoDs signed into the Validator Set.

>A portion of the block reward is emitted into a continuous liquidity pool, which is bonded to the asset linked via the bridging contract. Anyone can send assets into a linked liquidity contract to acquire PoS tokens. These assets can be used to stake in the Validator Set, and ensure high churn between active and standby validators. 

## Introduction

If a PoS network is launched with no pre-mine, and block rewards are issued to validators, then the starting validators can accumulate 100% of the tokens. If there is no permissionless way to nominate validators, then the starting validators may be a colluding cartel or a greedy founder who can dominate the network - especially in the early years of high inflation. Even if a cartel or greedy founder is not present, then massive coordination is required between genesis validators to start the network, which sets up channels of communication which may later be exploited later by cartels. If tokens are not issued to non-validators, then non-validators can never enter the validator set. Such a network is doomed. 

There are three parts to the solution in this paper. The first is that the starting validators are nominated in a permissionless way with almost no coordination. The second is that validator set growth is controlled in a predictable and permissionless way, with active competition to encourage optimal bond amounts and high validator churn. The third is that tokens are also distributed into a liquidity pool where price is set by the market and the PoS tokens can be acquired by anyone with any amount of capital. 

## Requirements

The PoS network requires a host chain to seed the network, but once it is seeded the host chain is no longer required, apart from continuing to provide asset liquidity. The components of this are the host chain, the PoS chain, and two special contracts on the host chain. These are the Election Contract and the Bridge Contract. The validators must be running nodes for both chains. 

## Election Contract

The election contract serves to allow any validator-elect to nominate themselves to become a validator. It is permissionless and not able to be censored or biased if it is deployed on a censorship-resistant blockchain such as Ethereum. The election contract has a number of variables and only two public methods.

The owner of the Election Contract must hard-code the address of the Bridge Contract in the contract constructor. 

**Variables**

```solidity
address _addressBridge;
uint256 _startValidators;   // n
uint256 _finishValidators;  // m
uint256 _periodBlocks;      // c
uint256 _blockReward;       // BR
uint256 _bondPeriod;        // T

uint256 _intValidators;
mapping _validatorElectHash(uint256 => uint256);  // mapping of validator-elect PoD
mapping _validatorElect(uint256 => uint256);      // mapping of validator-elect PoS keys
mapping _validatorOrder(uint256 => uint256);      // mapping of ordered validators
```


1) **Election Method.** Anyone can call the public election method to nominate themselves. They must insert their PoS key, a nonce and the hash of their key and nonce.
```solidity
function electMe(uint256 _posKey, uint256 _nonce, uint256 _hash) public returns(bool){

require(sha256(_posKey + _nonce) == _hash);
_validatorElectHash[msg.sender] = _hash;
_validatorElect[intValidators] = _hash

return true;
}
```

2) **Update Signatures.** Anyone can call the public update signature method to publish the lowest hashes in the election contract. The election period must be expired and the minimum number of public keys must be present. 

```solidity
function publishSigs() public returns(bool){

require(block >= electionBlockEnd);
require(intValidators >= startValidators);

for (uint i=0, i < intValidators, i++{
_validatorOrder[i] = _getOrder(i);
}

return true;
}
```

>**todo**
>Specify the `_getOrder(i)` function to order validator-elects in accodance of their PoD

In the election period any validator-elect can choose to increase their odds of being in the starting validator set by cycling through nonces off-chain and attempting to find the lowest possible hash they can. Once the starting signatures are published the first validator in the mapping can propose the genesis block on the PoS network, which includes the address of the election contract. The other validators in the PoS network will accept this genesis block by cross-referring to the election contract. They will reject any genesis block that is not valid. 

1) **Incorrect election contract.** Validators will reject a genesis block that is referring to a null or wrong election contract. The right election contract is the one that the majority of validator-elects are using. 
2) **Incorrect Order.** Validators will reject a genesis block that is being proposed by a validator that is not in position 0 in the election contract. 
3) **Incorrect Genesis.** Validators will reject a genesis block that is attempting to distribute the wrong block reward, or wrong lock times. 

## Bridge Contract

The bridge contract allows assets on Ethereum to be moved over to the liquidity pool on the PoS network. The bridge contract can be either 1-way or 2-way.  A 1-way bridge does not allow Ether assets to be removed and is inherently safer and less complex. A 1-way bridge can also be decommissioned after the network is bootstrapped. A 2-way bridge gives liquidity to tokens on the PoS network, since it allows them to be sold for Ether via signature consensus. A 2-way bridge is more complex since it will require slashing conditions on the PoS network to prevent stealing assets. 

When the PoS network begins, a minority block reward (such as 33%), is emitted into the liquidity pool on the PoS network. A liquidity pool is a smart contract with internal logic and the ability to handle PoS assets. 

The function in the bridge contract is the purchase method. Any ether placed in the bridge contract is observed by the watching validators on the host chain. The next validator who wishes to collect the fee from the purchase transaction processes the smart contract on the PoS network and emits tokens to the user’s PoS address (nominated in the bridge transaction).

The purchase method in the bridge contract:

```solidity
function purchaseTokens(address _destAddr) public payable {

  uint256 depthEther = address(this).balance;
  emit tokensPurchasedEvent(msg.sender, msg.value, depthEther, _destAddr)

}
```

The validator on the PoS network then processes the purchase (assuming EVM-like smart contracts and solidity-like code) by reading the bridge contract events and making an appropriate call on the smart contract:

```solidity
function emitTokens(uint256 _amount, address _destAddr) onlyValidator {

  uint256 depthToken = address(this).balance;
  uint256 depthEther = _bridgeContract.balance;
  
  uint256 tokens = (_amount * depthEther) / (_amount * depthToken);
  
  require(address(this).transfer(tokens, _destAddr));

  emit tokensEmittedEvent(_tokens, _amount, _destAddr)

}
```

If the validator attempts to emit tokens improperly or with the incorrect details, then other validators can block the transaction and even slash the validator. This should be codified prior to the network launch. 


Since the liquidity pool bonds PoS tokens to Ether using the `X*Y=K` model, [(implementation)](https://github.com/thorchain/Resources/blob/master/Whitepapers/ASGARDEX/clp-implementation.md), it allows anyone to purchase emitted tokens from the pool at any price they deem is worthwhile. Placing ether in the pool causes the token price to increase as they are emitted in accordance with the `X*Y=K` model. Since the block reward is continually placed in the pool, the price will continually reset back down to fair market prices. 

If the network chooses to remove the bridge after the network is bootstrapped (`m` validators) then the liquidity block reward should be removed by the validators, and the residual balance in the contract should be destroyed. On the ethereum side, the ether will be locked forever, since it is no longer necessary. 

The Bridge can be 2-way, allowing PoS token holders to exit to Ether at any time. This gives them liquidity and incentivises them to exit their PoS tokens, further increasing distribution. Ether does not need to be pegged on the PoS chain, since there does not need to be any representation of Ether on the PoS chain. A 2-way bridge is more complex, but can be achieved since it becomes a multi-signature account that emits Ether when someone sells. 

A 2-way bridge may be difficult to remove since the Ether is spendable by the validators. If they remove the liquidity block reward and burn the residual PoS balance, then they may elect to spend equally the residual Ether. This may in fact be codified into the smart contract as a planned decomission strategy to minimise disruption. 


## Active and Standby Validators

In order to encourage sufficient validator churn as well as a resilient network, the notion of standby validators are pursued. Standby validators are elected in the same cycle as active validators, yet have a higher hash in their election. The proportion of standby validators is `1/2` the number of active validators, such that they can recover a mainnet that loses more than 33% of active validators. Once signed into the validator set, standby validators receive a block reward that is `1/2` the amount the active validators receive. 

Standby validators can become active validators at any time once they bond more tokens than the lowest bonded active validators. Since all validators start with a bond of `BR/m`, they are incentivised quickly to start bonding tokens else standby validators will quickly replace them.

## Validator Growth

The launch of the network depends on there being a sufficient number of validators present. In an ideal state, 100+ validators should be part of consensus to ensure decentralisation, but it may prove very difficult (and even encourage a starting cartel) to coordinate a large number of  psuedo-anonymous validators for genesis. As such a much lower number of validators should be used to start the network, and progressively increase it over time. This ensures the network can be launched without much coordination and the entry slots for validators stays fair and permissionless. 

The method to do this is to continue to use the election contract. After genesis, validator-elects can continue to nominate themselves into the election contract. When `publishSigs()` is called the validators with the lowest hashes are added to the `validatorSet` mapping. Once they are added they can be part of the consensus of the PoS network. Validator-elects do not need to be holding any PoS tokens to enter the set. 

The election contract specifies the cycle to add new validators, `c`, as well as the max number of validators that is required before the election contract can be discarded, `m`. Every `c` blocks (on Ethereum), a new validators is added. Every other `c` blocks a standby validator slot is also added. If `publishSigs()` is not called after `c` blocks, then the validator can not be added until it is called. The cycle repeats once it is called.

## Genesis Block 

The validator with the lowest hash that has been nominated in the `publishSigs()` method in the election contract proposes the genesis block. Genesis blocks that do not comply with the rules of the network are simply ignored. At any time in the PoS bootstrap phase the correct chain can be audited by inspecting the genesis block and the election contract. If the validator with the lowest hash fails to start the network, then any other validator can take over by submitting a lower hash in their PoD and calling `publishSigs()` again. 

The Genesis block that begins the network contains the following information:

1) **Election Contract address.** The validator refers to the correct election contract address. 
2) **Block Reward.** The validator creates and distributes the first block reward to themselves which is `(BR * n)/m` PoS tokens. 
3) **Bond Periods.** The validator nominates bond times for themselves and all other validators, such that unlock times are smooth and continuous over a period of length `T`, starting at `T` and ending at `2T`. This prevents mass unlocks at the same time. 
4) **Mandatory Bond Period.** For the first period that a validator is included in, all block rewards issued to them are auto-bonded, creating their first bond, which is then locked for the designated lock time. 

The Block Reward issuance is designed to increase each time a new validator is added to the set, incentivising validators to continually call `publishSigs()` and add new validators, given by the equation `(BR * n)/m`. The Block Reward will ultimately reach 100% of what it should be once `m` validators are on the network.

The first time a validator is signed into the set it does not have a stake. Whilst at first glance this may cause a nothing-at-stake attack, since nothing is at stake, it is clear that the correct chain can always be identified by inspecting the election contract in order to determine the correct validator set. Nothing-at-stake problems will be a factor once the network is out of bootstrap mode and has discarded the election contract. 

## Post Bootstrap Phase
Once the maximum number of active and standby validators are added, then the election contract no longer serves a purpose. Validators are churned naturally as they bond different amounts. If ever new validator slots are added, then entry can be determined by auction. 

The Bridge Contract can continue to be used indefinitely as long as the network chooses to link the two chains. If the network chooses to decomission the Bridge Contract and remove the liquidity reward, then the Bridge Contract can be decomissioned as discussed above. 

# Conclusion
We have discussed a novel distribution method that allows a PoS network to be launched in a manner that does not cause cartels, coordination or plutocracies. The network is first seeded from a host chain that provides a permissionless election forum, as well as liquidity. One the minimum number of validators have been elected, they begin the network. New validators are also added in a permissionless way, until the maximum is achieved. PoS tokens are also distributed into a liquidity pool that allows anyone to acquire them. Once the network has been bootstrapped, the election and bridge contracts can be decomissioned safely. 
