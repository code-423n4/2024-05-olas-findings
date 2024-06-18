### [L-01] Missing check for `msg.value` in `WormholeDepositProcessorL1._sendMessage()`

The `_sendMessage` function in `WormholeDepositProcessorL1` does not verify that the `msg.value` sent with the transaction is sufficient to cover the cost of the Wormhole message fee and the quoted delivery price. This allows users to pay for the cost of the message with any leftover ETH in the `WormholeDepositProcessorL1` contract.

Add a check to ensure that `msg.value` is equal to the sum of `wormhole.messageFee() + wormholeRelayer.quoteEVMDeliveryPrice()` as per the Wormhole [docs](https://docs.wormhole.com/wormhole/quick-start/tutorials/hello-token#implement-sending-function).

### [L-02] `msg.value` must match the quoted cost exactly in `WormholeTargetDispenser.L2_sendMessage()`

In the `_sendMessage()` function of the `WormholeTargetDispenserL2` contract, the `msg.value` provided must match the quoted cost exactly. If the `msg.value` is not equal to the cost returned by `quoteEVMDeliveryPrice()`, the transaction will [revert](https://github.com/wormhole-foundation/wormhole/blob/33b2fbe72a3067f1e84160de93aef0ea66abdca4/relayer/ethereum/contracts/relayer/wormholeRelayer/WormholeRelayerBase.sol#L88). 

Modify the [condition on L115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L115) to check for strict equality.

### [L-03] Excess ETH sent when sending staking incentives to Arbitrum will remain in `ArbitrumDepositProcessorL1`

The `msg.value` sent to `ArbitrumDepositProcessorL1.sendMessageBatch()` from the dispenser is only checked to be at least as large as `cost[0] + cost[1]`:

```solidity
File: ArbitrumDepositProcessorL1.sol
164:         // Get the total cost
165:         uint256 totalCost = cost[0] + cost[1];
166: 
167:         // Check fot msg.value to cover the total cost
168:         if (totalCost > msg.value) {
169:             revert LowerThan(msg.value, totalCost);
170:         }
```

However, excess funds are not returned to the user and will remain in the `ArbitrumDepositProcessorL1`. It would be more sensible to check for strict equality.

This is also the case in the `_sendMessage()` functions of the Wormhole target dispenser on L2 as well as in the Optimism contracts on both sides, although those contracts should not accept any ETH in the first place as reported in a separate finding.

### [L-04] Allowing users to set the gas limit may cause failed messages, accidentally or intentionally

Allowing users to define the gas limit for messages sent via the L1 deposit processors for Arbitrum, Optimism and Wormhole may lead to undelivered messages on the other end that must be retried with a higher gas limit.

While all of these bridges implement functionality to retry a message with a higher gas limit if it fails to be delivered, if would be preferable not to allow users to directly specify the `gasLimitMessage`. Instead, have the deposit processors calculate the required gas based on the number of `targets` and `stakingIncentives` being processed. A small buffer can be added to account for any minor differences in gas costs.

Additionally, note that Arbitrum messages must be successfully delivered [within 7 days](https://docs.arbitrum.io/how-arbitrum-works/arbos/l1-l2-messaging#manual-redemption).

It is also worth pointing out that, while Wormhole seems to implement [functionality](https://github.com/wormhole-foundation/wormhole/blob/33b2fbe72a3067f1e84160de93aef0ea66abdca4/relayer/ethereum/contracts/relayer/wormholeRelayer/WormholeRelayerDelivery.sol#L217-L223) to override the original gas limit in a message, this feature is not well documented and the following excerpt can be found in its [documentation](https://docs.wormhole.com/wormhole/reference/blockchain-environments/evm/relayer#delivery-statuses):
> There are only three causes for a delivery failure:
> - the target contract does not implement the `IWormholeReceiver` interface 
> - the target contract threw an exception or reverted during execution of `receiveWormholeMessages`
> - the target contract exceeded the specified `gasLimit` while executing `receiveWormholeMessages`
> 
> All three of these scenarios should generally be avoidable by the integrator, and thus it is up to integrator to resolve them.

Hence it would be highly recommended to strictly prevent sending messages with an insufficient gas limit unless the replay functionality is properly understood and can be relied on.

 In the case of the Gnosis AMB, this has a more severe impact as reported in a separate finding.

### [L-05] Variable `gasLimitMessage` is not necessary for messages sent from L2

Messages sent from L2, on the other hand, are always verified to be sent with a gas limit `>= GAS_LIMIT`, which is set to `300_000`. This value is sufficient to cover the gas consumed by the `receiveMessage()` function on the L1 deposit processor, which has a bounded and generally constant gas cost. The only exception would be edge cases such as warm storage slots, which could decrease the gas cost slightly.

Given the predictable gas consumption, it is not really necessary to provide a variable `gasLimitMessage` for L2 to L1 messages. The `GAS_LIMIT` constant or a more accurate gas limit can be used directly.

### [L-06] All messages must be processed before an L2 dispenser can be migrated

Messages sent via the L1 deposit processors must all be processed before an L2 dispenser can be migrated, or the migration will lead to lost in-flight incentives.

There may be pending messages for a few reasons:
1. The `gasLimitMessage` provided by the user was too low, causing the message to fail to be delivered on L2. The message would need to be retried with a higher gas limit.
2. There was a period of downtime or congestion on the bridge, causing a timeout on certain messages.
3. The `receiveMessage()` function on the L2 dispenser reverted for some unexpected reason, requiring a fix and replay of messages.

Until all messages are successfully processed on L2, the staking incentives will have been claimed on L1 but not yet delivered on L2. Migrating the L2 dispenser in this state would result in the loss of any incentives that had not yet been delivered.

### [L-07] `GnosisTargetDispenserL2` and `GnosisDepositProcessorL1` may be vulnerable to replay attacks

These contracts send messages to each other directly via the Arbitrary Messaging Bridge.

According to [Gnosis Chain's Security Considerations for Receiving a Call](https://docs.gnosischain.com/bridges/About%20Token%20Bridges/amb-bridge#security-considerations-for-receiving-a-call) via the AMB, it's the receivers responsibility to take measures against replay attacks (or accidental message replay) by the AMB:

> **Replay Attack**
> `transactionHash()` allows for checking of a hash of the transaction that invoked the `requireToPassMessage()` call. The invoking contract (in some cases, the mediator contract) is responsible for providing a _unique sequence_ (can be a nonce) as part of the `_data` param in the `requireToPassMessage()` function call

This documentation seems to be outdated as the `transactionHash` [no longer serves](https://github.com/gnosischain/tokenbridge-contracts/blob/e672562825fbb99f202bb2cc40edff0c6a28f294/contracts/upgradeable_contracts/arbitrary_message/MessageProcessor.sol#L125) to uniquely identify a message, and replay protection seems to be implemented on both the [Home](https://github.com/gnosischain/tokenbridge-contracts/blob/e672562825fbb99f202bb2cc40edff0c6a28f294/contracts/upgradeable_contracts/arbitrary_message/BasicHomeAMB.sol#L37) and [Foreign](https://github.com/gnosischain/tokenbridge-contracts/blob/e672562825fbb99f202bb2cc40edff0c6a28f294/contracts/upgradeable_contracts/arbitrary_message/BasicForeignAMB.sol#L110) ends of the bridge (also in the [live](https://gnosisscan.io/address/0x525127c1f5670cc102b26905dccf8245c05c164f#code) [contracts](https://etherscan.io/address/0x82b67a43b69914e611710c62e629dabb2f7ac6ab#code)).

Nevertheless, it is advisable to verify that the functionality of the AMB is correctly understood and to consult with the upstream maintainers regarding this section of the documentation.

Alternatively, implement replay protection to be safe.

### [L-08] Empty arrays of staking targets and incentives should not be sent to deposit processors

In the `_distributeStakingIncentivesBatch()` function, after filtering out staking targets with zero incentives, the resulting `updatedStakingTargets` and `updatedStakingAmounts` arrays may end up being empty. For example, this could happen if all staking incentives for a particular chain ID were zero after being normalized.

However, the code still proceeds to send these empty arrays to the deposit processor contract via the `sendMessageBatch()` or `sendMessageBatchNonEVM()` functions. Sending empty arrays is wasteful in terms of gas costs, and may lead to unnecessary processing on the L2 side.

Consider adding a check to skip sending messages to a chain if the `updatedStakingTargets` array is empty.

### [L-09] No logic to explicitly support upgradeable tokens

As per the README, the protocol intends to support upgradeable tokens. However, there exists no logic to explicitly support such tokens in any way that would differ from non-upgradeable ones.

A change to the token semantics could break the staking contracts if they rely on past beheaviour (e.g. a token introducing rebasing logic), let alone a malicious upgrade.

As per the `weird-erc20` [docs](https://github.com/d-xo/weird-erc20#upgradable-tokens):

> Developers integrating with upgradable tokens should consider introducing logic that will freeze interactions with the token in question if an upgrade is detected. (e.g. the [`TUSD` adapter](https://github.com/makerdao/dss-deploy/blob/7394f6555daf5747686a1b29b2f46c6b2c64b061/src/join.sol#L321) used by MakerDAO).

### [L-10] Staking factory salt does not include `msg.sender` which may lead to unintended proxy ownership in case of a reorg

The `StakingFactory` contract deploys new proxy instances of the `StakingBase` implementation using `create2()` in the `createStakingInstance()` function. The salt used for `create2()` is based on `block.chainid` and a `nonce` value. However, it does not include `msg.sender`.

In the case of a reorg, a user's transaction to stake a service may end up depositing to a proxy contract deployed at the same address but by a different user, with potentially very different staking parameters.

Consider including `msg.sender` in the salt used for `create2()` to ensure each user's deployed proxy is unique to them, even in the case of a reorg.