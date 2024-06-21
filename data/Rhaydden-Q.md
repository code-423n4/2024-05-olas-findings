## QA report for Olas
<details>
<summary>NOTE TO JUDGE/SPONSOR</summary>

---

To maintain clarity and brevity, we have selectively shortened the provided code snippets to highlight the most pertinent parts. Some sections may be clustered, and certain references are accessible through links due to the project's scope.

---

Note to the judge: To avoid spamming the judging repo with issues that may still end up as QA, we have included some potentially subjective findings in this report that could be considered either _`medium or low severity`_. If the judge deems it appropriate, we would appreciate these findings being upgraded accordingly.

---

</details>

## Table of Contents




| Issue ID | Description |
| -------- | ----------- |
| [QA-01](#qa-01-verifyinstanceandgetemissionsamount-returns-the-emissions-amount-even-if-the-verifyinstance-function-returns-false) | `verifyInstanceAndGetEmissionsAmount` returns the emissions amount even if the `verifyInstance` function returns false |
| [QA-02](#qa-02-staking-incentives-can-be-manipulated-by-temporarily-increasing-veolas-power-at-epoch-end) | Staking Incentives Can Be Manipulated by Temporarily Increasing veOLAS Power at Epoch End |
| [QA-03](#qa-03-checkpoint-can-be-directly-called-on-implementation-contract) | `Checkpoint()` can be directly called on implementation contract |
| [QA-04](#qa-04-all-pending-donations-at-the-last-epoch-may-be-lost) | All pending donations at the last epoch may be lost |
| [QA-05](#qa-05-getlastidf-could-potentially-return-an-idf-that-is-outdated) | `getLastIDF` could potentially return an IDF that is outdated |
| [QA-06](#qa-06-single-step-transfer-process-for-owner-and-dispenser-roles-in-voteweighting-sol-can-lead-to-loss-of-control-if-an-incorrect-address-is-provided) | Single-step transfer process for `owner` and `dispenser` roles in `VoteWeighting.sol` can lead to loss of control if an incorrect address is provided |
| [QA-07](#qa-07-incentive-fractions-sum-less-than-100-not-checked) | Incentive Fractions Sum Less Than 100 Not Checked |
| [QA-08](#qa-08-incorrect-eviction-logic-in-evict-function) | Incorrect Eviction Logic in `_evict` Function |
| [QA-09](#qa-09-incorrect-staking-incentive-distribution-when-using-withheld-olas-amounts) | Incorrect Staking Incentive Distribution When Using Withheld OLAS Amounts |
| [QA-10](#qa-10-use-constants-for-previously-defined-values-in-tokenomicsconstants-sol) | Use constants for previously defined values in `TokenomicsConstants.sol` |
| [QA-11](#qa-11-loss-of-voting-power-upon-nominee-removal-in-voteweighting) | Loss of Voting Power Upon Nominee Removal in `VoteWeighting` |
| [QA-12](#qa-12-unchecked-return-value-from-proxy-initialization) | Unchecked Return Value from Proxy Initialization |
| [QA-13](#qa-13-bypassable-voting-delay-in-multi-nominee-voting-system) | Bypassable Voting Delay in Multi-Nominee Voting System |
| [QA-14](#qa-14-incompatibility-with-solana-due-to-the-use-of-block-chainid-in-retainerhash-calculation) | Incompatibility with Solana due to the use of `block.chainid` in `retainerHash` calculation |
| [QA-15](#qa-15-tokenomics-contract-should-be-paused-blocked-if-more-than-one-year-1-days-pass-since-last-checkpoint-call) | `Tokenomics` contract should be paused/blocked if more than `ONE_YEAR - 1 days` pass since last `checkpoint()` call |
| [QA-16](#qa-16-dos-could-occur-as-a-result-of-frequent-donations) | DOS could occur as a result of Frequent Donations |
| [QA-17](#qa-17-potential-precision-loss-in-syncwithheldamountmaintenance-function-due-to-normalization) | Potential Precision Loss in `syncWithheldAmountMaintenance` Function Due to Normalization |
| [QA-18](#qa-18-reentrancy-guard-not-reset-on-internal-function-revert-in-claimstakingincentivesbatch) | Reentrancy Guard Not Reset on Internal Function Revert in `claimStakingIncentivesBatch` |
| [QA-19](#qa-19-no-check-on-veolasthreshold-value-when-implementation-is-initialized) | No Check on `veOLASThreshold` Value When Implementation is Initialized |
| [QA-20](#qa-20-incorrect-logic-in-unstaking-condition) | Incorrect Logic in Unstaking Condition |
| [QA-21](#qa-21-inverse-discount-factor-idf-can-be-manipulated-and-made-sure-its-always-at-epsilonrate) | Inverse Discount Factor (`IDF`) can be manipulated and made sure it's always at `epsilonRate` |
| [QA-22](#qa-22-dangerous-custom-upgrade-functionality) | Dangerous custom upgrade functionality |
| [QA-23](#qa-23-potential-misallocation-of-inflationperepoch) | Potential misallocation of `InflationPerEpoch` |
| [QA-24](#qa-24-service-units-owners-can-be-hurt-if-donors-donate-small-amounts-that-round-down-to-zero) | Service units owners can be hurt if donors donate small amounts that round down to zero |
| [QA-25](#qa-25-donations-made-after-epoch-end-affect-rewards-of-the-previous-epoch) | Donations Made After Epoch End Affect Rewards of the Previous Epoch |
| [QA-26](#qa-26-checkpointnomineeandgetclaimedepochcounters-function-may-allow-claiming-incentives-for-the-ongoing-epoch-if-the-nominee-was-never-removed) | `_checkpointNomineeAndGetClaimedEpochCounters` function may allow claiming incentives for the ongoing epoch if the nominee was never removed |
| [QA-27](#qa-27-duplicate-nominee-addition) | Duplicate Nominee Addition |
| [QA-28](#qa-28-demarcate-changeregistries-into-separate-functions) | Demarcate `changeRegistries()` into separate functions |
| [QA-29](#qa-29-arbitrary-manipulation-of-withheld-amounts-by-contract-owner) | Arbitrary Manipulation of Withheld Amounts by Contract Owner |




## [QA-01] `verifyInstanceAndGetEmissionsAmount` returns the emissions amount even if the `verifyInstance` function returns false

### Impact
The `verifyInstanceAndGetEmissionsAmount` function returns the emissions amount even if the `verifyInstance` function returns `false`. This means that if the verification fails, the function may return a non-zero emissions amount, which is incorrect and could lead to unexpected behavior in the contract or any external contracts relying on this function.

## Proof of Concept
[Look at the `verifyInstanceAndGetEmissionsAmount` function:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/registries/contracts/staking/StakingFactory.sol#L294-L314)

```solidity
function verifyInstanceAndGetEmissionsAmount(address instance) external view returns (uint256 amount) {
    // Verify the proxy instance
    bool success = verifyInstance(instance);

    if (success) {
        // Get the proxy instance emissions amount
        amount = IStaking(instance).emissionsAmount();

        // If there is a verifier, adjust the amount
        address localVerifier = verifier;
        if (localVerifier != address(0)) {
            // Get the max possible emissions amount
            uint256 maxEmissions = IStakingVerifier(localVerifier).getEmissionsAmountLimit(instance);
            // Limit excessive emissions amount
            if (amount > maxEmissions) {
                amount = maxEmissions;
            }
        }
    }
}
```

If the `verifyInstance` function returns `false`, the `amount` variable is not set to 0, and the function returns whatever value `amount` had before the function call.

### Recommended Mitigation Steps:
Add an `else` clause that sets the `amount` to 0 when the verification fails:

```diff
function verifyInstanceAndGetEmissionsAmount(address instance) external view returns (uint256 amount) {
    // Verify the proxy instance
    bool success = verifyInstance(instance);

+    if (success) {
        // ... (existing code)
+    } else {
+        amount = 0;
    }
}
```

## [QA-02] Staking Incentives Can Be Manipulated by Temporarily Increasing veOLAS Power at Epoch End

### Impact
The `Dispenser.sol` contract's staking incentives distribution mechanism can be exploited by users who temporarily increase their veOLAS power just before the end of an epoch. This allows them to claim a disproportionately large share of the staking incentives for that epoch, despite only holding a high stake for a very short time. This undermines the fairness of the incentive distribution and can lead to significant rewards being claimed by users who do not maintain their stake for the intended duration.

### Proof of Concept
The vulnerability lies in the `calculateStakingIncentives` function, where staking incentives are calculated based on the nominee's veOLAS power at the end of each epoch:
https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Dispenser.sol#L886-L893

```solidity
uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);

// Get the staking weight for each epoch and the total weight
// Epoch endTime is used to get the weights info, since otherwise there is a risk of having votes
// accounted for from the next epoch
// totalWeightSum is the overall veOLAS power (bias) across all the voting nominees
(uint256 stakingWeight, uint256 totalWeightSum) =
    IVoteWeighting(voteWeighting).nomineeRelativeWeight(stakingTarget, chainId, endTime);

stakingIncentive = (availableStakingAmount * stakingWeight) / 1e18;
```

The issue is specifically in the calculation of the `stakingIncentive` variable. The calculation is based on the nominee's veOLAS power at the end of each epoch (`endTime`), which is obtained from the [`ITokenomics(tokenomics).getEpochEndTime(j)` function call](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1479-L1482). This `endTime` is used to fetch the nominee's relative weight (`stakingWeight`) and the total weight (`totalWeightSum`) for that epoch. The `stakingIncentive` is then calculated as the product of the available staking amount (`availableStakingAmount`) and the nominee's relative weight (`stakingWeight`), divided by the total weight (`totalWeightSum`), and then multiplied by `1e18` to convert it to the same scale as the weights.

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Dispenser.sol#L916

```solidity
stakingIncentive = (availableStakingAmount * stakingWeight) / 1e18;
```


### Scenario
1. Alice, who controls a staking target, sees that an epoch is about to end.
2. Just before the epoch end time, Alice acquires a large amount of veOLAS power, making her target's `stakingWeight` very high.
3. At the exact epoch end time, Alice's target has a high `stakingWeight` relative to the `totalWeightSum`, so it gets allocated a large `stakingIncentive`.
4. Immediately after the epoch end time, Alice reduces her target's veOLAS power, as it's no longer needed for that epoch.
5. Later, Alice calls `claimStakingIncentives` or `claimStakingIncentivesBatch` to claim the large `stakingIncentive` her target was allocated, despite only having a high stake for a moment.

### Recommended Mitigation Steps
It's difficult to propose a solution for this exploit without major changes in the contract's architecture. Perhaps, we can consider implementing a mechanism that prorates rewards based on the actual time staked within each epoch. This involves tracking the exact timestamps of staking and unstaking actions and calculating rewards proportionally. 














## [QA-03] `Checkpoint()` can be directly called on implementation contract 

### Impact
The checkpoint() function in the tokenomics contract implements a check to prevent it from being called on the implementation contract directly:

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1080-L1089

```solidity
function checkpoint() external returns (bool) {
        // Get the implementation address that was written to the proxy contract
        address implementation;
        assembly {
            implementation := sload(PROXY_TOKENOMICS)
        }
        // Check if there is any address in the PROXY_TOKENOMICS address slot
        if (implementation == address(0)) {
            revert DelegatecallOnly();
        }
```
Albeit, this check can be bypassed by initializing the implementation and then calling changeTokenomicsImplementation().

>Although the impacts of this bypass or not immediately clear, it is generally considered bad practice for implementation contracts to be initializable since it can cause unexpected behavior and potential security vulnerabilities. Moreover, the dev team of the protocol may have put this check in place with the intention of relying on it for further development. If this assumption is violated, it could lead to vulnerabilities in future contracts.

### Proof of Concept
`checkpoint()` may be called on the implementation contract by following these steps:

- Call `initializeTokenomics` on the implementation contract, becoming its owner.
- Call `changeTokenomicsImplementation` with any non-zero argument.
- Call `checkpoint`.

### Recommended Mitigation Steps
It is recommended to replicate this check in `initializeTokenomics`. This would make sure that the implementation contract cannot be initialized.



## [QA-04] All pending donations at the last epoch may be lost

### Impact
In the accountOwnerIncentives function of Tokenomics.sol pending rewards are only finalized if `lastEpoch < curEpoch`.
Now, if we look at the `checkpoint` function, the current epoch cannot be updated `if (diffNumSeconds < curEpochLen || diffNumSeconds > MAX_EPOCH_LENGTH)`.

Now, `MAX_EPOCH_LENGTH` is defined as `MAX_EPOCH_LENGTH = ONE_YEAR - 1 days` in [TokenomicsConstants.sol](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/TokenomicsConstants.sol#L19)
So therefore, in the edge case that checkpoint has not been called for `1 year - 1 days`, all of the donations from the last epoch will be lost as the if check in [`accountOwnerIncentives`](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1363), responsible for finalizing pending donations, will always return false.

>As the chances of this happening are low but the impact would be high, due to the loss of funds. This could be considered bottomline medium/low

### Proof of Concept
Here we can see that [_finalizeIncentivesForUnitId](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1363-L1367) will only get called if the epoch has been updated:

```solidity
if (lastEpoch > 0 && lastEpoch < curEpoch) {
                _finalizeIncentivesForUnitId(lastEpoch, unitTypes[i], unitIds[i]);
                // Change the last epoch number
                mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch = 0;
            }
```


### Recommended Mitigation Steps
Consider implementing a recovery mechanism in [`accountOwnerIncentives`](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1363) that'll check if the difference of seconds since the last epoch is more than `1 year - 1 days`. In that case, a call to `_finalizeIncentivesForUnitId` should also be made.



## [QA-05] `getLastIDF` could potentially return an IDF that is outdated

### Proof of Concept
[`getLastIDF` function](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1472-L1474) is meant to return the inverse discount factor with the multiple of the latest epoch, and this value is used for calculations in several parts of the protocol.

Now, the value of IDF is supposed to be changed each epoch via `checkpoint` function based on the updated values of treasury rewards and the number of new owners (via [`_calculateIDF`](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1033)), and this update is done when the time passed since last epoch end time is greater than the specified epoch length as seen here:

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1096-L1104

```solidity
// New point can be calculated only if we passed the number of blocks equal to the epoch length
        uint256 prevEpochTime = mapEpochTokenomics[epochCounter - 1].epochPoint.endTime;
        uint256 diffNumSeconds = block.timestamp - prevEpochTime;
        uint256 curEpochLen = epochLen;
        // Check if the time passed since the last epoch end time is bigger than the specified epoch length,
        // but not bigger than almost a year in seconds
        if (diffNumSeconds < curEpochLen || diffNumSeconds > MAX_EPOCH_LENGTH) {
            return false;
        }
```

So the protocol could potentially use an outdated IDF value if the time for the update is passed and the checkpoint hasn't been called to update the tokenomics variables which will result in the use of an outdated IDF value when calculating users payout when they deposit their LP share in a product to create bonds.


### Recommended Mitigation Steps
Update `getLastIDF` to check if a new IDF is to be used. Something like this:

```diff
 function getLastIDF() external view returns (uint256) {
 uint256 prevEpochTime = mapEpochTokenomics[epochCounter - 1].epochPoint.endTime;
+       uint256 diffNumSeconds = block.timestamp - prevEpochTime;
+       uint256 curEpochLen = epochLen;
+       if (diffNumSeconds >= curEpochLen) {
+          checkpoint();
+       }

        return mapEpochTokenomics[epochCounter].epochPoint.idf;
    }
```


## [QA-06] Single-step transfer process for `owner` and `dispenser` roles in `VoteWeighting.sol` can lead to loss of control if an incorrect address is provided.

### Impact
The current implementation of the `changeOwner` and `changeDispenser` functions in the `VoteWeighting.sol` contract allows for an immediate transfer of ownership and dispenser roles without requiring confirmation from the new owner or dispenser. This can arguably be considered risky if an incorrect address is provided, as the roles could be lost for sometime or forever. Losing the owner or dispenser role can result in the inability to perform critical functions, potentially disrupting the functionality of the protocol.

### Proof of Concept
https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/governance/contracts/VoteWeighting.sol#L368-L381

`changeOwner` Function: 
```solidity
/// @dev Changes the owner address.
/// @param newOwner Address of a new owner.
function changeOwner(address newOwner) external {
    // Check for the contract ownership
    if (msg.sender != owner) {
        revert OwnerOnly(msg.sender, owner);
    }

    // Check for the zero address
    if (newOwner == address(0)) {
        revert ZeroAddress();
    }

    owner = newOwner;
    emit OwnerUpdated(newOwner);
}
```

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/governance/contracts/VoteWeighting.sol#L386-L394
`changeDispenser` Function
```solidity
/// @dev Changes the dispenser contract address.
/// @notice Dispenser can be set to a zero address if the contract needs to serve a general purpose.
/// @param newDispenser New dispenser contract address.
function changeDispenser(address newDispenser) external {
    // Check for the contract ownership
    if (msg.sender != owner) {
        revert OwnerOnly(msg.sender, owner);
    }

    dispenser = newDispenser;
    emit DispenserUpdated(newDispenser);
}
```

### Recommended Mitigation Steps:
Implement a two-step transfer pattern for both the owner and dispenser roles to ensure that the new owner and dispenser explicitly accept their roles. This reduces the risk of losing control due to an incorrect address.





## [QA-07] Incentive Fractions Sum Less Than 100 Not Checked

### Impact
If the sum of the incentive fractions is less than 100, the tokenomics contract would not be using the full potential of top-ups. This could lead to inefficiencies and suboptimal distribution of incentives.

### Proof of Concept
The current implementation only checks if the sum of the incentive fractions is greater than 100, but does not check if it is less than 100:

[Here is the concerned part:}(https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L722-L725)

```solidity
uint256 sumTopUpFractions = _maxBondFraction + _topUpComponentFraction + _topUpAgentFraction + _stakingFraction;
if (sumTopUpFractions > 100) {
    revert WrongAmount(sumTopUpFractions, 100);
}
```

### Recommended Mitigation Steps
Add a check to ensure that the sum total of the incentive fractions is not less than 100







## [QA-08] Incorrect Eviction Logic in `_evict` Function

## Impact
The logic in the `_evict` function may lead to incorrect eviction of services from the staking system. Services that should be evicted based on their inactivity duration may not be properly removed from the `setServiceIds` array, leading to inconsistencies in the staking state and potential issues with future staking operations.

## Proof of Concept
[Let's take a look at the `_evict` function:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/registries/contracts/staking/StakingBase.sol#L463-L473)

```solidity
// Evict services from the global set of staked services
for (uint256 i = numEvictServices; i > 0; --i) {
    // Decrease the number of services
    totalNumServices--;
    // Get the evicted service index
    uint256 idx = serviceIndexes[i - 1];
    // Assign last service Id to the index that points to the evicted service Id
    setServiceIds[idx] = setServiceIds[totalNumServices];
    // Pop the last element
    setServiceIds.pop();
}
```

The issue occurs when assigning the last service ID to the index of the evicted service ID. The line `setServiceIds[idx] = setServiceIds[totalNumServices];` overwrites the service ID at index `idx` with the last service ID in the `setServiceIds` array. However, this approach does not consider the case where the evicted service ID is not the last service ID in the array.

As a result, if the evicted service ID is not the last service ID, the eviction process will incorrectly remove a different service ID from the `setServiceIds` array, leading to inconsistencies in the staking state.

## Recommended Mitigation Steps:
I can't really think of a more accurate way of handling this than directly removing the evicted service ID from its current position in the `setServiceIds` array without replacing it with another service ID. Probably by shifting all subsequent elements down by one position to fill the gap left by the removed element, followed by reducing the size of the array by one.  Something like this(just an example):

```solidity
// Correctly evict services from the global set of staked services
for (uint256 i = numEvictServices; i > 0; --i) {
    // Get the evicted service index
    uint256 idx = serviceIndexes[i - 1];
    
    // Shift all elements after the evicted service down by one position
    for (uint256 j = idx; j < totalNumServices - 1; ++j) {
        setServiceIds[j] = setServiceIds[j + 1];
    }
    
    // Decrease the number of services and pop the last element
    totalNumServices--;
    setServiceIds.pop();
}
```




## [QA-09] Incorrect Staking Incentive Distribution When Using Withheld OLAS Amounts

### Impact

The `claimStakingIncentives` and `claimStakingIncentivesBatch` functions incorrectly handle withheld OLAS amounts when distributing staking incentives. This can lead to:

1. Users receiving more OLAS tokens in their staking targets than they should, resulting in an unfair distribution of incentives.
2. The contract sending out more OLAS tokens than intended, potentially exhausting its token supply faster and preventing some users from claiming their rightful incentives.

Example:
- Alice is due 100 OLAS as staking incentive.
- The contract has 50 OLAS withheld from a previous transaction.
- The contract should send Alice 50 OLAS (from its balance) + 50 OLAS (from withheld), totaling 100 OLAS.
- Instead, it sends Alice 100 OLAS (from its balance) + 50 OLAS (from withheld), totaling 150 OLAS.
- Bob, who was due 200 OLAS next, might only get 150 OLAS because the contract sent too much to Alice.

>The severity depends on withheld amount frequency and size. If many users like Alice claim, the contract's OLAS supply could deplete rapidly, leading to a halt in staking incentive claims until more tokens are added.

### Proof of Concept

This issue is related to the handling of withheld OLAS amounts in the `claimStakingIncentives` and `claimStakingIncentivesBatch` functions.

In both functions, there's a mechanism to account for withheld OLAS amounts from previous transactions. The idea is that if some OLAS tokens were withheld (not transferred) in a previous transaction, they can be used to cover the current staking incentive, reducing or eliminating the need for a new OLAS transfer.

[Now let's take a look at this:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Dispenser.sol#L1007-L1042)

In `claimStakingIncentives`, `transferAmount` is correctly reduced when using withheld OLAS, but `stakingIncentive` isn't:

```solidity
// Alice claims 100 OLAS
uint256 stakingIncentive = 100;
uint256 transferAmount = 100;
uint256 withheldAmount = mapChainIdWithheldAmounts[chainId]; // 50 OLAS

if (withheldAmount > 0) {
    // Correctly adjusts transferAmount to 50 OLAS
    transferAmount -= withheldAmount;
    withheldAmount = 0;
    mapChainIdWithheldAmounts[chainId] = withheldAmount;
}

// Incorrect: Sends 100 OLAS (not 50) from contract + 50 from withheld
_distributeStakingIncentives(chainId, stakingTarget, stakingIncentive, bridgePayload, transferAmount);
```

The contract sends Alice 150 OLAS instead of 100 OLAS, as `_distributeStakingIncentives` receives an unadjusted `stakingIncentive` value.

### Recommended Mitigation Steps
The `stakingIncentive` parameter in the `_distributeStakingIncentives` call should be adjusted to match the `transferAmount` when withheld amounts are used. The same issue and fix would apply to the `claimStakingIncentivesBatch` function as well."







## [QA-10] Use constants for previously defined values in `TokenomicsConstants.sol`

Since `maxMintCapFraction` is used only once and is previously defined: the inflation is 2% per year as defined, it should be declared as a constant variable.

_Here are the instances:_
- https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/TokenomicsConstants.sol#L54-L55

```solidity
  // After that the inflation is 2% per year as defined by the OLAS contract
            uint256 maxMintCapFraction = 2;
```

- https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/TokenomicsConstants.sol#L91-L92

```solidity
// After that the inflation is 2% per year as defined by the OLAS contract
            uint256 maxMintCapFraction = 2;
```



## [QA-11] Loss of Voting Power Upon Nominee Removal in `VoteWeighting`

### Impact

The `VoteWeighting` contract allows for the removal of nominees, which can lead to a loss of voting power for users who have voted for the removed nominee. This issue affects the integrity of the voting process, as users' votes are not properly handled or redistributed upon a nominee's removal. Additionally, it somehat introduces a potential for manipulation by the contract owner, who can unilaterally remove nominees, thereby influencing the outcome of the voting process.

### Proof of Concept

The `removeNominee` function sets the weight of a removed nominee to zero without providing a mechanism for redistributing or compensating the voting power of users who voted for that nominee. Here's the relevant part of the code:

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/governance/contracts/VoteWeighting.sol#L602-L612

```solidity
// Set nominee weight to zero
uint256 oldWeight = _getWeight(account, chainId);
uint256 oldSum = _getSum();
uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;
pointsWeight[nomineeHash][nextTime].bias = 0;
timeWeight[nomineeHash] = nextTime;

// Account for the sum weight change
uint256 newSum = oldSum - oldWeight;
pointsSum[nextTime].bias = newSum;
timeSum = nextTime;
```

Additionally, the `revokeRemovedNomineeVotingPower` function allows users to revoke their voting power from a removed nominee but does not specify how this power is reallocated:

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/governance/contracts/VoteWeighting.sol#L663-L668

```solidity
// Update the voting power
uint256 powerUsed = voteUserPower[msg.sender];
powerUsed = powerUsed - oldSlope.power;
voteUserPower[msg.sender] = powerUsed;
delete voteUserSlopes[msg.sender][nomineeHash];
```

### Recommended Mitigation Steps

- Consider re-implementing vote distribution. Modify the `removeNominee` function to redistribute the votes of the removed nominee among remaining nominees according to a predefined logic or allow users to manually reallocate their votes.

- Ensure that users who voted for a removed nominee do not lose their voting power. This could involve refunding their voting power or providing an option to reallocate it to another nominee.







## [QA-12] Unchecked Return Value from Proxy Initialization

### Impact
If the initialization of the proxy instance fails for a reason other than reverting, it will go unnoticed. This could lead to the proxy instance being created in an inconsistent state, potentially causing an issue.

### Proof of Concept

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/registries/contracts/staking/StakingFactory.sol#L215-L228

```solidity
// Initialize the proxy instance
(bool success, bytes memory returnData) = instance.call(initPayload);
// Process unsuccessful call
if (!success) {
    // Get the revert message bytes
    if (returnData.length > 0) {
        assembly {
            let returnDataSize := mload(returnData)
            revert(add(32, returnData), returnDataSize)
        }
    } else {
        revert InitializationFailed(instance);
    }
}
```

The `success` variable is decoded from the return data but never checked before proceeding. If `instance.call(initPayload)` returns `false` but does not revert, the code will continue executing as if the initialization was successful.

### Recommended Mitigation Steps
Add a require statement to check the `success` variable immediately after the `instance.call(initPayload)` line:

```diff
    // Initialize the proxy instance
    (bool success, bytes memory returnData) = instance.call(initPayload);
+  require(success, "Proxy initialization failed");
```




## [QA-13] Bypassable Voting Delay in Multi-Nominee Voting System

### Impact

The current implementation allows users to bypass the intended voting delay by casting votes for different nominees within the same delay period. This could lead to rapid changes in the voting power distribution undermining the fairness of the voting process. Additionally, it could encourage frequent voting activity, increasing gas costs and could congest the protocol.
### Scenario

Consider a scenario where the `WEIGHT_VOTE_DELAY` is set to 10 days([Actually it is set to 864000 seconds which is 10 days](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/governance/contracts/VoteWeighting.sol#L148)). A user can vote for Nominee A on Day 1, wait until Day 2, and then vote for Nominee B. This sequence of actions effectively bypasses the intended 10-day delay between votes, as the delay is enforced per nominee rather than globally for the user.

### Proof of Concept

This stems from this part of the `voteForNomineeWeights` function: 

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/governance/contracts/VoteWeighting.sol#L501-L505

```solidity
// Check for the last voting time
uint256 nextAllowedVotingTime = lastUserVote[msg.sender][nomineeHash] + WEIGHT_VOTE_DELAY;
if (nextAllowedVotingTime > block.timestamp) {
    revert VoteTooOften(msg.sender, block.timestamp, nextAllowedVotingTime);
}
```

This calculates the `nextAllowedVotingTime` based on the last vote made by the user for a specific nominee (`nomineeHash`). Since the delay is applied per nominee, a user can vote for different nominees within the same delay period without triggering the `VoteTooOften` reversion.

### Recommended Mitigation Steps

The protocol should consider enforcing a global voting delay for each user, regardless of the nominee they are voting for. This could be achieved by maintaining a single `lastUserVote` timestamp for each user, rather than for each nominee.



## [QA-14] Incompatibility with Solana due to the use of `block.chainid` in `retainerHash` calculation

### Impact
According to the [README](https://github.com/code-423n4/2024-05-olas#general-questions), 

|Question|Answer|
|-|-|
|Chains the protocol will be deployed on|	Ethereum, Arbitrum, Base, Optimism, Polygon, OtherGnosis, Celo, Solana|


But the current implementation of the `retainerHash` calculation using `block.chainid` in `Dispenser.sol` contract is incompatible with Solana. This will cause the protocol to fail when deployed on Solana, as Solana is not an EVM-compatible chain and does not support the `block.chainid` opcode. This issue affects the ability to generate unique identifiers for the retainer on Solana, potentially leading to incorrect or duplicate hashes.

### Proof of Concept
[The relevant code snippet from `Dispenser.sol`:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Dispenser.sol#L346)
```solidity
retainerHash = keccak256(abi.encode(IVoteWeighting.Nominee(retainer, block.chainid)));
```

The `Nominee` struct:
```solidity
struct Nominee {
    bytes32 account;
    uint256 chainId;
}
```

### Recommended Mitigation Steps
To ensure compatibility wITH Solana, implement chain-specific logic for the `retainerHash` calculation. For EVM-compatible chains, continue using `block.chainid`. For Solana, use an alternative mechanism to generate a unique identifier, possibly involving off-chain coordination or Solana-specific features.



## [QA-15] `Tokenomics` contract should be paused/blocked if more than `0NE_YEAR - 1 days` pass since last `checkpoint()` call

### Impact
A key limitation of this protocol is that it requires the `tokenomics.sol::checkpoint()` function to be called within a year - 1 days from the end of the previous epoch (i.e., `block.timestamp - prevEpochTime > ONE_YEAR - 1 days`). If this condition is not met, the protocol will be unable to distribute rewards or top-ups, thereby halting its main operations.


### Proof of Concept
https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1102-L1104

```solidity
  if (diffNumSeconds < curEpochLen || diffNumSeconds > MAX_EPOCH_LENGTH) {
            return false;
        }
```

If `diffNumSeconds` exceeds `MAX_EPOCH_LENGTH` which is defined as `MAX_EPOCH_LENGTH = ONE_YEAR - 1 days` in [TokenomicsConstants.sol](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/TokenomicsConstants.sol#L19), the `checkpoint()` function becomes unusable. This means new epochs can't be initiated, and the distribution of rewards and top-ups is halted. Donations made after this point would essentially be locked without any corresponding distribution of rewards, leading to donor funds being frozen without any return.

### Recommended Mitigation Steps
Consider pausing the protocol donations in case `diffNumSeconds > MAX_EPOCH_LENGTH`




## [QA-16] DOS could occur as a result of Frequent Donations

### Impact
User's might be unable to withdraw pending rewards.


### Proof of Concept
If a donation is made before the `checkpoint()` call in the same block, the checkpoint call will revert. This is done in order to prevent the possibility of flash loan attacks.

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1091-L1094

```solidity
 // Check the last donation block number to avoid the possibility of a flash loan attack
        if (lastDonationBlockNumber == block.number) {
            revert SameBlockNumberViolation();
        }
```

Albeit, this scenario might arise for the following reasons:

- High activity and numerous donations
- Attacker griefs

Grieving by an attacker can be done for some time with minimal loss if the `rewardTreasuryFraction` is set to 0 by donating to their own addresses. Although permanent locking is not typically cost-effective for the attacker, a special case scenario, such as when the current `epochLength` is nearly 1 year, could incentivize an attacker to delay the checkpoint call beyond 1 year, after which the checkpoint call will consistently fail.


### Recommended Mitigation Steps
If we can make the deposits of service donations pauseable, it'll somewhat guarantee protection.

## [QA-17] Potential Precision Loss in `syncWithheldAmountMaintenance` Function Due to Normalization

### Impact
The normalization process for the `amount` parameter in the `syncWithheldAmountMaintenance` function can lead to a loss of precision. This can result in a smaller `amount` being stored in `mapChainIdWithheldAmounts` than intended, which might not accurately reflect the withheld amount that needs to be synced. 

### Proof of Concept
[Here's the concerned part:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Dispenser.sol#L1219-L1225)

```solidity
if (bridgingDecimals < 18) {
    uint256 normalizedAmount = amount / (10 ** (18 - bridgingDecimals));
    normalizedAmount *= 10 ** (18 - bridgingDecimals);
    // Downsize staking incentive to a specified number of bridging decimals
    amount = normalizedAmount;
}
```

##### Example Scenario
Suppose `amount` is `1234567890123456789` and `bridgingDecimals` is `6`. The normalization process would be:

1. `normalizedAmount = amount / (10 ** (18 - 6)) = 1234567890123456789 / 10**12 = 1234567`
2. `normalizedAmount *= 10 ** (18 - 6) = 1234567 * 10**12 = 1234567000000000000`

The final `amount` after normalization is `1234567000000000000`, which is less than the original `1234567890123456789`. 

### Recommended Mitigation Steps:
Try to ensure that the `amount` is correctly rounded to the nearest value that can be represented with the specified `bridgingDecimals` before performing the normalization. This can be achieved by using a more precise method for normalization or adding a check to ensure that the `amount` is already in the correct format.








## [QA-18] Reentrancy Guard Not Reset on Internal Function Revert in `claimStakingIncentivesBatch`

### Impact
If any internal function call within `claimStakingIncentivesBatch` reverts, the `_locked` state variable will not be reset to `1`, leaving the contract in a locked state. This will prevent any further calls to the function until the contract is manually reset, potentially disrupting the normal operation of the contract.

### Proof of Concept
The `_locked` state variable is set to `2` at the beginning of the function to prevent reentrancy attacks. However, if any of the internal function calls revert, the `_locked` state variable will not be reset to `1`.

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Dispenser.sol#L1125

```solidity
function claimStakingIncentivesBatch(
    uint256 numClaimedEpochs,
    uint256[] memory chainIds,
    bytes32[][] memory stakingTargets,
    bytes[] memory bridgePayloads,
    uint256[] memory valueAmounts
) external payable {
    // Reentrancy guard
    if (_locked > 1) {
        revert ReentrancyGuard();
    }
    _locked = 2;

    _checkOrderAndValues(chainIds, stakingTargets, bridgePayloads, valueAmounts);

    // ... (rest of the function)

    _locked = 1;
}
```

### Recommended Mitigation Steps:
Use a `try/catch` block or a `require` statement to ensure that the `_locked` state variable is reset to `1` even if an internal function call fails. This will prevent the contract from being stuck in a locked state. For example, wrap the internal function calls in a `try/catch` block and reset `_locked` to `1` in the `catch` block.





## [QA-19]  No Check on `veOLASThreshold` Value When Implementation is Initialized


### Impact
The `veOLASThreshold` variable represents the minimum veOLAS voting power required for a service donor or service owner to be eligible for top-ups. This threshold is crucial as it determines the eligibility for rewards based on the locked OLAS tokens over time. However, when the Tokenomics implementation is initialized, `veOLASThreshold` is assigned a value of `10_000e18` without verifying if it exceeds the total supply of OLAS at the time of deployment. This oversight can result in service owners being unable to receive top-ups, as neither the donor nor the service owner would meet this threshold, especially in the early stages.

### Proof of Concept
https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L4371. **Initialization Issue:**

   ```solidity
   veOLASThreshold = 10_000e18;
   ```
   When the Tokenomics contract is initialized, `veOLASThreshold` is set to `10_000e18` without checking if this value is greater than the total supply of OLAS tokens. If the total supply is less than `10_000e18`, no service owner or donor can meet this threshold, making them ineligible for top-ups.

3. **Threshold Adjustment Issue:**
https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L683-L688
   ```solidity
   // Adjust veOLAS threshold for the next epoch
   if (uint96(_veOLASThreshold) > 0) {
       nextVeOLASThreshold = uint96(_veOLASThreshold);
   } else {
       _veOLASThreshold = veOLASThreshold;
   }
   ```
   When the contract owner changes the `veOLASThreshold` via the `Tokenomics.changeTokenomicsParameters` function, there is no check to ensure the new threshold does not exceed the total supply of OLAS tokens. This can again result in an unrealistic threshold that no service owner or donor can meet.

4. **Eligibility Check:**
  
https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L911-L913

```solidity
 topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||
                    IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;
            }
```

   The eligibility for top-ups is determined by comparing the voting power of the service owner and donor against `veOLASThreshold`. If `veOLASThreshold` is set too high, neither party can meet the requirement, making them ineligible for rewards.

### Recommended Mitigation Steps
Implement a check to ensure that the new value of `veOLASThreshold` does not exceed the total supply of OLAS tokens before assigning it. This check should be added both during the initialization of the Tokenomics contract and when the `veOLASThreshold` is changed via the `changeTokenomicsParameters` function. This will ensure that the threshold remains realistic and achievable, allowing service owners and donors to be eligible for top-ups.





## [QA-20] Incorrect Logic in Unstaking Condition

### Impact
The incorrect logic in the `unstake` function's condition may prevent services from unstaking even when they have met the minimum staking duration requirement. This can lead to services being locked in the staking contract unintentionally.


### Proof of Concept
In the `unstake` function, there is a check to ensure that the service has staked for a minimum required duration (`minStakingDuration`) or if there are no rewards left (`availableRewards > 0`). However, the condition seems to be incorrect. 

[Here's the affected part:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/registries/contracts/staking/StakingBase.sol#L816-L820)

```solidity
// Check that the service has staked long enough, or if there are no rewards left
uint256 ts = block.timestamp - tsStart;
if (ts <= minStakingDuration && availableRewards > 0) {
    revert NotEnoughTimeStaked(serviceId, ts, minStakingDuration);
}
```

The issue is with the logical operator used in the condition. Currently, it uses the `&&` operator, which means that the `NotEnoughTimeStaked` error will be thrown only if both conditions are true: `ts <= minStakingDuration` and `availableRewards > 0`.

However, according to the inline comment which says that `// Check that the service has staked long enough, or if there are no rewards left`,  I believe the intended behavior is to allow unstaking if either the service has staked for the minimum required duration (`ts > minStakingDuration`) `OR` if there are no rewards left (`availableRewards == 0`).




### Recommended Mitigation Steps:
Update the condition in the `unstake` function to use the `||` (logical OR) operator instead of `&&` (logical AND):

```diff
   // Check that the service has staked long enough, or if there are no rewards left
    uint256 ts = block.timestamp - tsStart;
-   if (ts <= minStakingDuration && availableRewards > 0) {
+   if (ts <= minStakingDuration || availableRewards > 0) {
      revert NotEnoughTimeStaked(serviceId, ts, minStakingDuration);
}
```



## [QA-21] Inverse Discount Factor (`IDF`) can be manipulated and made sure it's always at `epsilonRate`

### Impact
The `IDF` calculation can be manipulated by artificially inflating the `numNewOwners` value. This can be done by creating multiple new accounts and making small donations from these accounts, which would increase the `numNewOwners` count and potentially keep the `IDF` at the `epsilonRate`. This manipulation can undermine the intended economic incentives and fairness of the protocol.

### Proof of Concept
When calling `checkPoint()`, it calculates the new `IDF`. 
[The `IDF` calculation scales with `numNewOwners` like this:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1046-L1050)

```solidity
UD60x18 fpDevsPerCapital = UD60x18.wrap(devsPerCapital);
fp = fp.mul(fpDevsPerCapital);
UD60x18 fpNumNewOwners = convert(numNewOwners);
fp = fp.add(fpNumNewOwners);
fp = fp.mul(codeUnits);
```

[`numNewOwners` is registered when a donation is made like this:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L962-L971)

```solidity
if (!mapNewUnits[unitType][serviceUnitIds[j]]) {
    mapNewUnits[unitType][serviceUnitIds[j]] = true;
    mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;
    // Check if the owner has introduced component / agent for the first time
    // This is done together with the new unit check, otherwise it could be just a new unit owner
    address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);
    if (!mapNewOwners[unitOwner]) {
        mapNewOwners[unitOwner] = true;
        mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;
    }
}
```

Creating a new account and having it create a new service is easy. Therefore, anyone with bonds can simply create services with new accounts, then make the smallest possible donation to these services and thus increase `numNewOwners`. As they are the owners of the accounts, they'll get their donation back later as well.

### Recommended Mitigation Steps
Implement a minimum donation threshold that must be met for a new owner to be counted and a verification mechanism to ensure that new owners are legitimate and not just created for the purpose of manipulation. 

Alternatively, consider not using `IDF` at all, or just accept that it is a possibility that it can always be the maximum `epsilonRate`.










## [QA-22] Dangerous custom upgrade functionality

### Impact
Protocol can be broken as a result of unsafe upgrade.

### Proof of Concept
The current custom upgrade in tokenomics contract does not follow UUPS(Universal Upgradeable Proxy Standard) standard.

Take a look:

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L522-L542

```solidity
/// @dev Changes the tokenomics implementation contract address.
    /// @notice Make sure the implementation contract has a function to change the implementation.
    /// @param implementation Tokenomics implementation contract address.
    /// #if_succeeds {:msg "new implementation"} implementation == tokenomicsImplementation();
    function changeTokenomicsImplementation(address implementation) external {
        // Check for the contract ownership
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }

        // Check for the zero address
        if (implementation == address(0)) {
            revert ZeroAddress();
        }

        // Store the implementation address under the designated storage slot
        assembly {
            sstore(PROXY_TOKENOMICS, implementation)
        }
        emit TokenomicsImplementationUpdated(implementation);
    }
```

One may argue that this is a bad idea because:

- Owner is set by solidity compiler (slot 0, not compliant with EIP1967).   This could cause the update to break the owner.
- You missed `initializeTokenomics` inside `changeTokenomicsImplementation` that you need to update storage variables with the new variables in case you add then.


### Recommended Mitigation Steps
​ Implement a complete UUPS Standard including EIP1967.


## [QA-23] Potential misallocation of `InflationPerEpoch`

### Impact
In the [`changeIncentiveFractions`](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L721-L726) function, `sumTopUpFractions` which is the sum of `_maxBondFraction`, `_topUpComponentFraction`, `_stakingFraction` and `_topUpAgentFraction` is checked to ensure it does not exceed 100%. Albeit, this check should be stricter to ensure the sum exactly equals 100%.

This could cause a situation where a part of the inflation intended for distribution (either for bonding or top-ups) is left unallocated or misallocated.
This issue can disrupt the tokenomics balance, affecting the incentives for participants and potentially leading to underutilization of the inflationary budget. With time, this could result in significant economic imbalances within the ecosystem of Olas.


### Proof of Concept

Look at [`changeIncentiveFractions`](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L721-L726) function:

```solidity
 // Same check for top-up fractions
        uint256 sumTopUpFractions = _maxBondFraction + _topUpComponentFraction + _topUpAgentFraction +
            _stakingFraction;
        if (sumTopUpFractions > 100) {
            revert WrongAmount(sumTopUpFractions, 100);
        }

        // All the adjustments will be accounted for in the next epoch
        uint256 eCounter = epochCounter + 1;
        TokenomicsPoint storage tp = mapEpochTokenomics[eCounter];
        // 0 stands for components and 1 for agents
        tp.unitPoints[0].rewardUnitFraction = uint8(_rewardComponentFraction);
        tp.unitPoints[1].rewardUnitFraction = uint8(_rewardAgentFraction);
        // Rewards are always distributed in full: the leftovers will be allocated to treasury
        tp.epochPoint.rewardTreasuryFraction = uint8(100 - _rewardComponentFraction - _rewardAgentFraction);

        tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);
        tp.unitPoints[0].topUpUnitFraction = uint8(_topUpComponentFraction);
        tp.unitPoints[1].topUpUnitFraction = uint8(_topUpAgentFraction);
        mapEpochStakingPoints[eCounter].stakingFraction = uint8(_stakingFraction);

        // Set the flag that incentive fractions are requested to be updated (2nd bit is set to one)
        tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x02;
        emit IncentiveFractionsUpdateRequested(eCounter, _rewardComponentFraction, _rewardAgentFraction,
            _maxBondFraction, _topUpComponentFraction, _topUpAgentFraction, _stakingFraction);
    }
```

As you can see in this function rewards are checked to not be > 100%. Albeit, for top-ups there is a risk that the sum of maxBondFraction + topUpAgent + topUpComponent + _stakingFraction is < 100 if there is an error on the arguments provided to the function, and this error could lead to bad accounting of the supply for the whole protocol.

In the [`checkpoint`](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1080-L1297) function, these fractions are used to calculate how the inflationary budget is distributed:

```solidity
 function checkpoint() external returns (bool) {

// maxBond allocation
 incentives[4] = (inflationPerEpoch * tp.epochPoint.maxBondFraction) / 100;

// topup component allocation
 incentives[5] = (inflationPerEpoch * tp.unitPoints[0].topUpUnitFraction) / 100;

// topup agentFraction allocation
 incentives[6] = (inflationPerEpoch * tp.unitPoints[1].topUpUnitFraction) / 100;


// topup stakingFraction allocation
   incentives[8] = incentives[7] + (inflationPerEpoch * mapEpochStakingPoints[eCounter].stakingFraction) / 100;
```

Now, if the sum of `_maxBondFraction`, `_topUpComponentFraction`, `_topUpAgentFraction` and `stakingFraction` is less than 100%, it means that a part of `inflationPerEpoch` will not be allocated, leading to unutilized inflation funds which will cause bad accounting/minting of Olas' tokens.

### Recommended Mitigation Steps
[Change this to:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L721-L726)

```diff
 // Same check for top-up fractions
        uint256 sumTopUpFractions = _maxBondFraction + _topUpComponentFraction + _topUpAgentFraction +
            _stakingFraction;
-        if (sumTopUpFractions > 100) {
+       if (sumTopUpFractions != 100) {
            revert WrongAmount(sumTopUpFractions, 100);
        }
```


## [QA-24] Service units owners can be hurt if donors donate small amounts that round down to zero

### Impact

Potential loss for the service owner as he will be receiving less topups and rewards.

### Proof of Concept
Firstly, let's take a look at this part:

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L925-L944

```solidity
 // Record amounts data only if at least one incentive unit fraction is not zero
                if (incentiveFlags[unitType] || incentiveFlags[unitType + 2]) {
                    // The amount has to be adjusted for the number of units in the service
                    uint96 amount = uint96(amounts[i] / numServiceUnits);
                    // Accumulate amounts for each unit Id
                    for (uint256 j = 0; j < numServiceUnits; ++j) {
                        // Get the last epoch number the incentives were accumulated for
                        uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch;
                        // Check if there were no donations in previous epochs and set the current epoch
                        if (lastEpoch == 0) {
                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
                        } else if (lastEpoch < curEpoch) {
                            // Finalize unit rewards and top-ups if there were pending ones from the previous epoch
                            // Pending incentives are getting finalized during the next epoch the component / agent
                            // receives donations. If this is not the case before claiming incentives, the finalization
                            // happens in the accountOwnerIncentives() where the incentives are issued
                            _finalizeIncentivesForUnitId(lastEpoch, unitType, serviceUnitIds[j]);
                            // Change the last epoch number
                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
                        }
```

Now considering the fact that Olas allows anyone to deposit ETH donations for services via `depositServiceDonationsETH` in the treasury contract(OOS) as per the docs, which makes a subtle call to [`trackServiceDonations`](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L884) to track these donations and distribute it to the service owners.

However, in `trackServiceDonations` the donation is distributed to the service owners, and these donations are split into ETH rewards and top-ups rewards (OLAS tokens). The said top-up reward is granted to the service owner only if the owner or the donor has a vested OLAS (`veOLAS`) with a voting power > `veOLASThreshold`; otherwise the service owner will not be eligible to receive these top-up rewards.

The amount of the donation allocated for a service unit owner is then [calculated based on the number of deployed units of that service as:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L928)

```solidity
   uint96 amount = uint96(amounts[i] / numServiceUnits);
```
[then the unit service rewards are checked to be finalized or not based on the last recorded epoch of that unit:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L932-L944)

```solidity
uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch;
                        // Check if there were no donations in previous epochs and set the current epoch
                        if (lastEpoch == 0) {
                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
                        } else if (lastEpoch < curEpoch) {
                            // Finalize unit rewards and top-ups if there were pending ones from the previous epoch
                            // Pending incentives are getting finalized during the next epoch the component / agent
                            // receives donations. If this is not the case before claiming incentives, the finalization
                            // happens in the accountOwnerIncentives() where the incentives are issued
                            _finalizeIncentivesForUnitId(lastEpoch, unitType, serviceUnitIds[j]);
                            // Change the last epoch number
                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
                        }
```

where `_finalizeIncentivesForUnitId` function finalizes epoch incentives by converting the pending rewards and pending top-ups into rewards and top-ups based on the current epoch `rewardUnitFraction` and `topUpUnitFraction`.

Evidently, if the `lastEpoch == curEpoch`, the pending rewards will not be finalized until the lastEpoch becomes less(`<`) than `curEpoch`, which means user's pending rewards will be stuck until the next epoch.

Being aware of this and in tandem with the fact that the calculated `amount = uint96(amounts[i] / numServiceUnits)` might round down to `0` if the allocated amount for that service unit is very small or if the number of units in a service is very large; then not checking the amount before updating the epoch of the unit will hurt the service owner if this amount is zero; as he will be unable to finalize his pending rewards until the next epoch (where next epoch might have rewards fractions less than the current epoch fractions. This will result in a loss for the service owner as he will be receiving less rewards and top-ups).

Additionally, this can be exploited by any malicious donor by intentionally sending a dust amount for a specific service unit to prevent finalizing rewards of that unit in the current epoch.

For this scenario to happen, the malicious donor can be a unit service owner where he deposits a service donation for his unit with a big amount and a donation to the victim unit with a dust amount.

### Recommended Mitigation Steps
Check that the calculated donation amount for a unit is > 0 before updating epoch time of that unit `(mapUnitIncentives[unitType][serviceUnitId].lastEpoch)`

## [QA-25] Donations Made After Epoch End Affect Rewards of the Previous Epoch

### Impact
Donations made after the end of the current epoch but before the `checkpoint` function is called to start the next epoch are counted towards the current epoch. This results in the treasury contract receiving rewards for these late donations based on the `rewardsTreasuryFraction` of the current epoch instead of the new one.

### Proof of Concept
The protocol allows anyone to deposit ETH donations for services via the `Treasury.depositServiceDonationsETH` function(OOS). These donations are stored in `mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH`, representing the total service balance of the current epoch.

In the `Tokenomics.checkpoint` function, [the treasury rewards (incentives) are calculated as `totalDonationsETH * rewardsTreasuryFraction / 100`](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1115-L1116) and sent to the treasury. The `rewardsTreasuryFraction` is a parameter of the current epoch, not the new one.

However, if the time for the current epoch ends and the `checkpoint` function hasn't been called to start the next epoch, update the Tokenomics variables, and send treasury rewards, any donations made will still be added to the current epoch. This results in the treasury contract getting its rewards for these late donations based on the `rewardsTreasuryFraction` of the current epoch.

[Look at this part of the code:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L1284-L1294)


```solidity
// Treasury contract rebalances ETH funds depending on the treasury rewards
if (incentives[1] == 0 || ITreasury(treasury).rebalanceTreasury(incentives[1])) {
    // Emit settled epoch written to the last economics point
    emit EpochSettled(eCounter, incentives[1], accountRewards, accountTopUps);
    // Start new epoch
    epochCounter = uint32(eCounter + 1);
} else {
    // If the treasury rebalance was not executed correctly, the new epoch does not start
    revert TreasuryRebalanceFailed(eCounter);
}
```
As seen above, the `rebalanceTreasury` function is called with the `incentives[1]` parameter, which is calculated based on the donations of the current epoch. If donations are made after the epoch ends but before the `checkpoint` function is called, they are still counted towards the current epoch, affecting the rewards.

### Recommended Mitigation Steps
Add a mechanism to allocate any donations made after the end of the current epoch (but before updating to the next epoch) to the next epoch's total donations. 

## [QA-26] `_checkpointNomineeAndGetClaimedEpochCounters` function may allow claiming incentives for the ongoing epoch if the nominee was never removed

### Impact
The current logic in the `_checkpointNomineeAndGetClaimedEpochCounters` function may allow claiming incentives for the ongoing epoch if the nominee was never removed. This could possibly cause an incorrect incentive distribution.

### Proof of Concept
The issue lies in the handling of the `epochAfterRemoved` variable and the condition that checks if the nominee was removed:

https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Dispenser.sol#L383-L385

```solidity
uint256 epochAfterRemoved = mapRemovedNomineeEpochs[nomineeHash] + 1;
if (epochAfterRemoved > 1 && firstClaimedEpoch >= epochAfterRemoved) {
    revert Overflow(firstClaimedEpoch, epochAfterRemoved - 1);
}
```

##### Explanation:

1. **Initialization of `epochAfterRemoved`**:
   - `epochAfterRemoved` is calculated as `mapRemovedNomineeEpochs[nomineeHash] + 1`.
   - If `mapRemovedNomineeEpochs[nomineeHash]` is `0` (indicating the nominee was never removed), `epochAfterRemoved` will be `1`.

2. **Condition Check**:
   - The condition `epochAfterRemoved > 1` is used to check if the nominee was removed.
   - If `epochAfterRemoved` is `1`, it means the nominee was never removed, and the condition `epochAfterRemoved > 1` will be `false`.

3. **Logic Issue**:
   - If `epochAfterRemoved` is `1`, the condition `epochAfterRemoved > 1` will be `false`, and the subsequent check `firstClaimedEpoch >= epochAfterRemoved` will not be executed.
   - This means that if `firstClaimedEpoch` is `1` (indicating the first epoch), the function will not revert, even though it should not claim in the ongoing epoch.

### Recommended Mitigation Steps:
Consider adjusting the condition to handle the case where `epochAfterRemoved` is `1` correctly. Change the condition to check if `epochAfterRemoved` is greater than or equal to `1` and adjust the logic accordingly:

```diff
uint256 epochAfterRemoved = mapRemovedNomineeEpochs[nomineeHash] + 1;
- if (epochAfterRemoved > 1 && firstClaimedEpoch >= epochAfterRemoved) {
+ if (epochAfterRemoved >= 1 && firstClaimedEpoch >= epochAfterRemoved) {
    revert Overflow(firstClaimedEpoch, epochAfterRemoved - 1);
}
```




## [QA-27] Duplicate Nominee Addition

### Impact
The protocol allows adding duplicate nominees, which can lead to redundant entries in the `setNominees` array and potentially affect the contract's functionality.

### Proof of Concept
https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/governance/contracts/VoteWeighting.sol#L324-L364
```solidity
function addNomineeEVM(address account, uint256 chainId) external {
    // ...
    // Missing check for existing nominee
    _addNominee(nominee);
}

function addNomineeNonEVM(bytes32 account, uint256 chainId) external {
    // ...
    // Missing check for existing nominee
    _addNominee(nominee);
}
```

### Recommended Mitigation Steps:
Add a check in the `addNomineeEVM` and `addNomineeNonEVM` functions to ensure that the nominee doesn't already exist before adding it to the `setNominees` array.












## [QA-28]  Demarcate `changeRegistries()` into separate functions

[The `changeRegistries` function](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Tokenomics.sol#L592-L611) is responsible for updating three distinct registries: `componentRegistry`, `agentRegistry`, and `serviceRegistry`. The specific registry to be updated is determined based on the value assigned to `address(0)`. For instance, to modify solely the `componentRegistry` address, one must assign `address(0)` to both `_agentRegistry` and `_componentRegistry`. Albeit, this approach can potentially confuse users due to its non-intuitive nature.

### Recommended Mitigation Steps
Consider refactoring this function into three separate, clearly defined functions: `changeComponentRegistry()`, `changeAgentRegistry()`, and `changeServiceRegistry()`.




## [QA-29] Arbitrary Manipulation of Withheld Amounts by Contract Owner

### Impact
The `syncWithheldAmountMaintenance` function allows the contract owner to arbitrarily increase the withheld amount for any L2 chain ID without proper validation or checks. This could allow the owner manipulate the withheld amounts and potentially causing issues in the token balances and distribution. An attacker with ownership access could exploit this.

### Proof of Concept

[Look at this:](https://github.com/code-423n4/2024-05-olas/blob/9b812d9d8cade3ecbd0043ae2fbd70a9d969b901/tokenomics/contracts/Dispenser.sol#L1174-L1192)

```solidity
function syncWithheldAmountMaintenance(uint256 chainId, uint256 amount) external {
    // Check the contract ownership
    if (msg.sender != owner) {
        revert OwnerOnly(msg.sender, owner);
    }

    // ...

    // Add to the withheld amount
    mapChainIdWithheldAmounts[chainId] = withheldAmount;

    emit WithheldAmountSynced(chainId, amount, withheldAmount);
}
```

The function allows the owner to specify any `chainId` and `amount` to be added to the withheld amount without proper validation or verification of the legitimacy of the provided `amount`.

### Recommended Mitigation Steps
Implement additional validation and checks in the `syncWithheldAmountMaintenance` function to ensure the provided `amount` is within acceptable limits and aligns with the expected withheld amounts based on the L2 chain's activity and balances.