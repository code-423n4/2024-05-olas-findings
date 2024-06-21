# QA Report

## Summary

### Low Issues

Total **257 instances** over **16 issues**:

|ID|Issue|Instances|
|:--:|:---|:--:|
| [[L-01]](#l-01-array-length-is-not-checked-before-access-its-index) | Array length is not checked before access its index | 19 |
| [[L-02]](#l-02-a-year-is-not-always-365-days) | A year is not always 365 days | 1 |
| [[L-03]](#l-03-variables-shadowing-other-definitions) | Variables shadowing other definitions | 2 |
| [[L-04]](#l-04-array-is-pushed-but-not-poped) | Array is `push()`ed but not `pop()`ed | 3 |
| [[L-05]](#l-05-state-variables-not-limited-to-reasonable-values) | State variables not limited to reasonable values | 5 |
| [[L-06]](#l-06-written-only-contract-variables) | Written only contract variables | 1 |
| [[L-07]](#l-07-downcasting-other-types-to-an-address-can-cause-collisions) | Downcasting other types to an address can cause collisions | 6 |
| [[L-08]](#l-08-use-abiencodecall-instead-of-abiencodewithsignatureabiencodewithselector) | Use `abi.encodeCall()` instead of `abi.encodeWithSignature()`/`abi.encodeWithSelector()` | 6 |
| [[L-09]](#l-09-constructor--initialization-function-lacks-parameter-validation) | Constructor / initialization function lacks parameter validation | 52 |
| [[L-10]](#l-10-unsafe-solidity-low-level-call-can-cause-gas-grief-attack) | Unsafe solidity low-level call can cause gas grief attack | 6 |
| [[L-11]](#l-11-functions-calling-contractsaddresses-with-transfer-hooks-should-be-protected-by-reentrancy-guard) | Functions calling contracts/addresses with transfer hooks should be protected by reentrancy guard | 8 |
| [[L-12]](#l-12-critical-functions-should-be-controlled-by-time-locks) | Critical functions should be controlled by time locks | 21 |
| [[L-13]](#l-13-missing-contract-existence-checks-before-low-level-calls) | Missing contract existence checks before low-level calls | 5 |
| [[L-14]](#l-14-duplicate-data-may-be-pushed-to-the-array) | Duplicate data may be pushed to the array | 1 |
| [[L-15]](#l-15-sending-tokens-within-loops) | Sending tokens within loops | 2 |
| [[L-16]](#l-16-code-does-not-follow-the-best-practice-of-check-effects-interaction) | Code does not follow the best practice of check-effects-interaction | 119 |

### Non Critical Issues

Total **725 instances** over **52 issues**:

|ID|Issue|Instances|
|:--:|:---|:--:|
| [[N-01]](#n-01-import-declarations-should-import-specific-identifiers-rather-than-the-whole-file) | Import declarations should import specific identifiers, rather than the whole file | 1 |
| [[N-02]](#n-02-names-of-privateinternal-functions-should-be-prefixed-with-an-underscore) | Names of `private`/`internal` functions should be prefixed with an underscore | 3 |
| [[N-03]](#n-03-use-of-override-is-unnecessary) | Use of `override` is unnecessary | 21 |
| [[N-04]](#n-04-unused-structs) | Unused `struct`s | 1 |
| [[N-05]](#n-05-add-inline-comments-for-unnamed-parameters) | Add inline comments for unnamed parameters | 12 |
| [[N-06]](#n-06-consider-providing-a-ranged-getter-for-array-state-variables) | Consider providing a ranged getter for array state variables | 4 |
| [[N-07]](#n-07-assembly-blocks-should-have-extensive-comments) | Assembly blocks should have extensive comments | 8 |
| [[N-08]](#n-08-consider-splitting-complex-checks-into-multiple-steps) | Consider splitting complex checks into multiple steps | 10 |
| [[N-09]](#n-09-complex-casting) | Complex casting | 3 |
| [[N-10]](#n-10-consider-adding-a-blockdeny-list) | Consider adding a block/deny-list | 5 |
| [[N-11]](#n-11-constantsimmutables-redefined-elsewhere) | Constants/Immutables redefined elsewhere | 17 |
| [[N-12]](#n-12-convert-simple-if-statements-to-ternary-expressions) | Convert simple `if`-statements to ternary expressions | 1 |
| [[N-13]](#n-13-contracts-should-each-be-defined-in-separate-files) | Contracts should each be defined in separate files | 19 |
| [[N-14]](#n-14-contract-timekeeping-will-break-earlier-than-the-ethereum-network-itself-will-stop-working) | Contract timekeeping will break earlier than the Ethereum network itself will stop working | 3 |
| [[N-15]](#n-15-consider-emitting-an-event-at-the-end-of-the-constructor) | Consider emitting an event at the end of the constructor | 20 |
| [[N-16]](#n-16-empty-function-body-without-comments) | Empty function body without comments | 4 |
| [[N-17]](#n-17-events-are-emitted-without-the-sender-information) | Events are emitted without the sender information | 51 |
| [[N-18]](#n-18-inconsistent-floating-version-pragma) | Inconsistent floating version pragma | 4 |
| [[N-19]](#n-19-imports-could-be-organized-more-systematically) | Imports could be organized more systematically | 5 |
| [[N-20]](#n-20-invalid-natspec-comment-style) | Invalid NatSpec comment style | 1 |
| [[N-21]](#n-21-safe-contracts-should-be-upgraded-to-a-newer-version) | safe-contracts should be upgraded to a newer version | 2 |
| [[N-22]](#n-22-expressions-for-constant-values-should-use-immutable-rather-than-constant) | Expressions for constant values should use `immutable` rather than `constant` | 6 |
| [[N-23]](#n-23-contracts-containing-only-utility-functions-should-be-made-into-libraries) | Contracts containing only utility functions should be made into libraries | 1 |
| [[N-24]](#n-24-functions-should-be-named-in-mixedcase-style) | Functions should be named in mixedCase style | 2 |
| [[N-25]](#n-25-variable-names-for-immutables-should-use-upper_case_with_underscores) | Variable names for `immutable`s should use UPPER_CASE_WITH_UNDERSCORES | 28 |
| [[N-26]](#n-26-named-imports-of-parent-contracts-are-missing) | Named imports of parent contracts are missing | 1 |
| [[N-27]](#n-27-constants-should-be-put-on-the-left-side-of-comparisons) | Constants should be put on the left side of comparisons | 165 |
| [[N-28]](#n-28-else-block-not-required) | `else`-block not required | 3 |
| [[N-29]](#n-29-large-multiples-of-ten-should-use-scientific-notation) | Large multiples of ten should use scientific notation | 7 |
| [[N-30]](#n-30-high-cyclomatic-complexity) | High cyclomatic complexity | 4 |
| [[N-31]](#n-31-typos) | Typos | 1 |
| [[N-32]](#n-32-consider-bounding-input-array-length) | Consider bounding input array length | 28 |
| [[N-33]](#n-33-unused-contract-variables) | Unused contract variables | 5 |
| [[N-34]](#n-34-use-delete-instead-of-assigning-values-to-false) | Use delete instead of assigning values to `false` | 1 |
| [[N-35]](#n-35-consider-using-delete-rather-than-assigning-zero-to-clear-values) | Consider using `delete` rather than assigning zero to clear values | 27 |
| [[N-36]](#n-36-use-the-latest-solidity-version) | Use the latest Solidity version | 1 |
| [[N-37]](#n-37-use-transfer-libraries-instead-of-low-level-calls-to-transfer-native-tokens) | Use transfer libraries instead of low level calls to transfer native tokens | 2 |
| [[N-38]](#n-38-use-a-struct-to-encapsulate-multiple-function-parameters) | Use a struct to encapsulate multiple function parameters | 10 |
| [[N-39]](#n-39-returning-a-struct-instead-of-a-bunch-of-variables-is-better) | Returning a struct instead of a bunch of variables is better | 12 |
| [[N-40]](#n-40-missing-event-when-a-state-variables-is-set-in-constructor) | Missing event when a state variables is set in constructor | 22 |
| [[N-41]](#n-41-empty-bytes-check-is-missing) | Empty bytes check is missing | 13 |
| [[N-42]](#n-42-assembly-block-creates-dirty-bits) | Assembly block creates dirty bits | 2 |
| [[N-43]](#n-43-multiple-mappings-with-same-keys-can-be-combined-into-a-single-struct-mapping-for-readability) | Multiple mappings with same keys can be combined into a single struct mapping for readability | 22 |
| [[N-44]](#n-44-missing-event-for-critical-changes) | Missing event for critical changes | 2 |
| [[N-45]](#n-45-non-assembly-method-available) | Non-assembly method available | 1 |
| [[N-46]](#n-46-consider-adding-emergency-stop-functionality) | Consider adding emergency-stop functionality | 20 |
| [[N-47]](#n-47-avoid-the-use-of-sensitive-terms) | Avoid the use of sensitive terms | 61 |
| [[N-48]](#n-48-missing-checks-for-uint-state-variable-assignments) | Missing checks for uint state variable assignments | 5 |
| [[N-49]](#n-49-use-the-modern-upgradeable-contract-paradigm) | Use the Modern Upgradeable Contract Paradigm | 18 |
| [[N-50]](#n-50-large-or-complicated-code-bases-should-implement-invariant-tests) | Large or complicated code bases should implement invariant tests | 2 |
| [[N-51]](#n-51-the-default-value-is-manually-set-when-it-is-declared) | The default value is manually set when it is declared | 39 |
| [[N-52]](#n-52-contracts-should-have-all-publicexternal-functions-exposed-by-interfaces) | Contracts should have all `public`/`external` functions exposed by `interface`s | 19 |

## Low Issues

### [L-01] Array length is not checked before access its index

Accessing the elements of the array without checking or ensuring the validity of the access index in advance. It may result in an unexpected out-of-bounds error, or may result in missing elements when trying to traverse the entire array.

<details>
<summary>There are 19 instances (click to show):</summary>

- *StakingActivityChecker.sol* ( [57-57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol#L57-L57), [57-57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol#L57-L57), [58-58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol#L58-L58), [58-58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol#L58-L58) ):

```solidity
57:         if (ts > 0 && curNonces[0] > lastNonces[0]) {

57:         if (ts > 0 && curNonces[0] > lastNonces[0]) {

58:             uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts;

58:             uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts;
```

- *StakingBase.sol* ( [457-457](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L457-L457) ):

```solidity
457:                 inactivity[sCounter] = serviceInactivity[i];
```

- *Dispenser.sol* ( [455-455](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L455-L455), [456-456](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L456-L456), [463-463](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L463-L463), [463-463](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L463-L463), [476-476](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L476-L476), [476-476](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L476-L476), [490-490](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L490-L490), [491-491](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L491-L491), [491-491](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L491-L491), [494-494](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L494-L494), [495-495](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L495-L495), [495-495](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L495-L495) ):

```solidity
455:             if (transferAmounts[i] > 0) {

456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]);

463:                 if (stakingIncentives[i][j] > 0) {

463:                 if (stakingIncentives[i][j] > 0) {

476:                     updatedStakingAmounts[numPos] = stakingIncentives[i][j];

476:                     updatedStakingAmounts[numPos] = stakingIncentives[i][j];

490:                 IDepositProcessor(depositProcessor).sendMessageBatch{value:valueAmounts[i]}(stakingTargetsEVM,

491:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);

491:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);

494:                 IDepositProcessor(depositProcessor).sendMessageBatchNonEVM{value:valueAmounts[i]}(updatedStakingTargets,

495:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);

495:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);
```

- *Tokenomics.sol* ( [928-928](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L928-L928) ):

```solidity
928:                     uint96 amount = uint96(amounts[i] / numServiceUnits);
```

- *EthereumDepositProcessor.sol* ( [96-96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L96-L96) ):

```solidity
96:             uint256 amount = stakingIncentives[i];
```

</details>

### [L-02] A year is not always 365 days

A year is not always 365 days or any fixed days or seconds. On leap years, the number of days is 366, so calculations during those years will return the wrong value.

There is 1 instance:

- *TokenomicsConstants.sol* ( [15-15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/TokenomicsConstants.sol#L15-L15) ):

```solidity
15:     uint256 public constant ONE_YEAR = 1 days * 365;
```

### [L-03] Variables shadowing other definitions

A variable declaration shadowing any other existing definition is error-prone. It can cause confusion for developers, potentially introduce bugs, and reduce the readability and maintainability.

There are 2 instances:

- *StakingVerifier.sol* ( [192-192](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L192-L192) ):

```solidity
/// @audit Shadows `function rewardsPerSecond()`
192:         uint256 rewardsPerSecond = IStaking(instance).rewardsPerSecond();
```

- *Dispenser.sol* ( [769-769](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L769-L769) ):

```solidity
/// @audit Shadows `function epochLen()`
769:         uint256 epochLen = ITokenomics(tokenomics).epochLen();
```

### [L-04] Array is `push()`ed but not `pop()`ed

Array entries are added but are never removed. Consider whether this should be the case, or whether there should be a maximum, or whether old entries should be removed. Cases where there are specific potential problems will be flagged separately under a different issue.

There are 3 instances:

- *VoteWeighting.sol* ( [218-218](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L218-L218), [616-616](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L616-L616) ):

```solidity
218:         setRemovedNominees.push(Nominee(0, 0));

616:         setRemovedNominees.push(nominee);
```

- *StakingBase.sol* ( [338-338](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L338-L338) ):

```solidity
338:             agentIds.push(agentId);
```

### [L-05] State variables not limited to reasonable values

Consider adding appropriate minimum/maximum value checks to ensure that the following state variables can never be used to excessively harm users, including via griefing.

There are 5 instances:

- *StakingVerifier.sol* ( [244-244](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L244-L244), [245-245](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L245-L245), [246-246](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L246-L246) ):

```solidity
244:         rewardsPerSecondLimit = _rewardsPerSecondLimit;

245:         timeForEmissionsLimit = _timeForEmissionsLimit;

246:         numServicesLimit = _numServicesLimit;
```

- *Dispenser.sol* ( [694-694](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L694-L694), [695-695](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L695-L695) ):

```solidity
694:         maxNumClaimingEpochs = _maxNumClaimingEpochs;

695:         maxNumStakingTargets = _maxNumStakingTargets;
```

### [L-06] Written only contract variables

Write only contract variables are only written in the contract and are never read and cannot be accessed through an interface.
It is recommended to check if there is a bug causing the missing readings, or if some `view` interfaces should be provided. If not, consider removing them to improve code clarity, avoid confusion, and save gas.

There is 1 instance:

- *Tokenomics.sol* ( [435-435](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L435-L435) ):

```solidity
435:         _locked = 1;
```

### [L-07] Downcasting other types to an address can cause collisions

Downcasting other types to an address will truncates the upper bytes, which means that multiple values can be mapped to an address, i.e. address collisions can occur.

<details>
<summary>There are 6 instances (click to show):</summary>

- *StakingFactory.sol* ( [156-156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L156-L156) ):

```solidity
156:         return address(uint160(uint256(hash)));
```

- *Dispenser.sol* ( [424-424](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L424-L424), [486-486](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L486-L486) ):

```solidity
424:             address stakingTargetEVM = address(uint160(uint256(stakingTarget)));

486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));
```

- *ArbitrumTargetDispenserL2.sol* ( [48-48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L48-L48) ):

```solidity
48:             l1AliasedDepositProcessor = address(uint160(_l1DepositProcessor) + offset);
```

- *WormholeDepositProcessorL1.sol* ( [125-125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L125-L125) ):

```solidity
125:         address l2Dispenser = address(uint160(uint256(sourceAddress)));
```

- *WormholeTargetDispenserL2.sol* ( [164-164](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L164-L164) ):

```solidity
164:         address processor = address(uint160(uint256(sourceProcessor)));
```

</details>

### [L-08] Use `abi.encodeCall()` instead of `abi.encodeWithSignature()`/`abi.encodeWithSelector()`

Function `abi.encodeCall()` provides [type-safe encode utility](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3693) comparing with `abi.encodeWithSignature()`/`abi.encodeWithSelector()`.

<details>
<summary>There are 6 instances (click to show):</summary>

- *ArbitrumDepositProcessorL1.sol* ( [187-187](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L187-L187) ):

```solidity
187:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));
```

- *ArbitrumTargetDispenserL2.sol* ( [55-55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L55-L55) ):

```solidity
55:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));
```

- *GnosisDepositProcessorL1.sol* ( [73-73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L73-L73) ):

```solidity
73:             bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));
```

- *GnosisTargetDispenserL2.sol* ( [77-77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L77-L77) ):

```solidity
77:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));
```

- *OptimismDepositProcessorL1.sol* ( [136-136](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L136-L136) ):

```solidity
136:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));
```

- *OptimismTargetDispenserL2.sol* ( [85-85](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L85-L85) ):

```solidity
85:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));
```

</details>

### [L-09] Constructor / initialization function lacks parameter validation

Constructors and initialization functions play a critical role in contracts by setting important initial states when the contract is first deployed before the system starts. The parameters passed to the constructor and initialization functions directly affect the behavior of the contract / protocol. If incorrect parameters are provided, the system may fail to run, behave abnormally, be unstable, or lack security. Therefore, it's crucial to carefully check each parameter in the constructor and initialization functions. If an exception is found, the transaction should be rolled back.

<details>
<summary>There are 52 instances (click to show):</summary>

- *StakingNativeToken.sol* ( [20-20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingNativeToken.sol#L20-L20) ):

```solidity
/// @audit `_stakingParams`
20:     function initialize(StakingParams memory _stakingParams) external {
```

- *StakingToken.sol* ( [54-59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L54-L59) ):

```solidity
/// @audit `_stakingParams`
54:     function initialize(
55:         StakingParams memory _stakingParams,
56:         address _serviceRegistryTokenUtility,
57:         address _stakingToken
58:     ) external
59:     {
```

- *ArbitrumDepositProcessorL1.sol* ( [89-100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L89-L100) ):

```solidity
/// @audit `_olas`
/// @audit `_l1Dispenser`
/// @audit `_l1TokenRelayer`
/// @audit `_l1MessageRelayer`
/// @audit `_l2TargetChainId`
89:     constructor(
90:         address _olas,
91:         address _l1Dispenser,
92:         address _l1TokenRelayer,
93:         address _l1MessageRelayer,
94:         uint256 _l2TargetChainId,
95:         address _l1ERC20Gateway,
96:         address _outbox,
97:         address _bridge
98:     )
99:         DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
100:     {
```

- *ArbitrumTargetDispenserL2.sol* ( [36-44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L36-L44) ):

```solidity
/// @audit `_olas`
/// @audit `_proxyFactory`
/// @audit `_l2MessageRelayer`
/// @audit `_l1DepositProcessor`
/// @audit `_l1SourceChainId`
36:     constructor(
37:         address _olas,
38:         address _proxyFactory,
39:         address _l2MessageRelayer,
40:         address _l1DepositProcessor,
41:         uint256 _l1SourceChainId
42:     )
43:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
44:     {
```

- *DefaultDepositProcessorL1.sol* ( [59-65](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L59-L65) ):

```solidity
/// @audit `_olas`
59:     constructor(
60:         address _olas,
61:         address _l1Dispenser,
62:         address _l1TokenRelayer,
63:         address _l1MessageRelayer,
64:         uint256 _l2TargetChainId
65:     ) {
```

- *GnosisDepositProcessorL1.sol* ( [42-48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L42-L48) ):

```solidity
/// @audit `_olas`
/// @audit `_l1Dispenser`
/// @audit `_l1TokenRelayer`
/// @audit `_l1MessageRelayer`
/// @audit `_l2TargetChainId`
42:     constructor(
43:         address _olas,
44:         address _l1Dispenser,
45:         address _l1TokenRelayer,
46:         address _l1MessageRelayer,
47:         uint256 _l2TargetChainId
48:     ) DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId) {}
```

- *GnosisTargetDispenserL2.sol* ( [39-48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L39-L48) ):

```solidity
/// @audit `_olas`
/// @audit `_proxyFactory`
/// @audit `_l2MessageRelayer`
/// @audit `_l1DepositProcessor`
/// @audit `_l1SourceChainId`
39:     constructor(
40:         address _olas,
41:         address _proxyFactory,
42:         address _l2MessageRelayer,
43:         address _l1DepositProcessor,
44:         uint256 _l1SourceChainId,
45:         address _l2TokenRelayer
46:     )
47:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
48:     {
```

- *OptimismDepositProcessorL1.sol* ( [76-85](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L76-L85) ):

```solidity
/// @audit `_olas`
/// @audit `_l1Dispenser`
/// @audit `_l1TokenRelayer`
/// @audit `_l1MessageRelayer`
/// @audit `_l2TargetChainId`
76:     constructor(
77:         address _olas,
78:         address _l1Dispenser,
79:         address _l1TokenRelayer,
80:         address _l1MessageRelayer,
81:         uint256 _l2TargetChainId,
82:         address _olasL2
83:     )
84:         DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
85:     {
```

- *OptimismTargetDispenserL2.sol* ( [47-53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L47-L53) ):

```solidity
/// @audit `_olas`
/// @audit `_proxyFactory`
/// @audit `_l2MessageRelayer`
/// @audit `_l1DepositProcessor`
/// @audit `_l1SourceChainId`
47:     constructor(
48:         address _olas,
49:         address _proxyFactory,
50:         address _l2MessageRelayer,
51:         address _l1DepositProcessor,
52:         uint256 _l1SourceChainId
53:     ) DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId) {}
```

- *PolygonDepositProcessorL1.sol* ( [36-47](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L36-L47) ):

```solidity
/// @audit `_olas`
/// @audit `_l1Dispenser`
/// @audit `_l1TokenRelayer`
/// @audit `_l1MessageRelayer`
/// @audit `_l2TargetChainId`
36:     constructor(
37:         address _olas,
38:         address _l1Dispenser,
39:         address _l1TokenRelayer,
40:         address _l1MessageRelayer,
41:         uint256 _l2TargetChainId,
42:         address _checkpointManager,
43:         address _predicate
44:     )
45:         DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
46:         FxBaseRootTunnel(_checkpointManager, _l1MessageRelayer)
47:     {
```

- *PolygonTargetDispenserL2.sol* ( [20-29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L20-L29) ):

```solidity
/// @audit `_olas`
/// @audit `_proxyFactory`
/// @audit `_l2MessageRelayer`
/// @audit `_l1DepositProcessor`
/// @audit `_l1SourceChainId`
20:     constructor(
21:         address _olas,
22:         address _proxyFactory,
23:         address _l2MessageRelayer,
24:         address _l1DepositProcessor,
25:         uint256 _l1SourceChainId
26:     )
27:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
28:         FxBaseChildTunnel(_l2MessageRelayer)
29:     {}
```

- *WormholeDepositProcessorL1.sol* ( [28-39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L28-L39) ):

```solidity
/// @audit `_olas`
/// @audit `_l1Dispenser`
/// @audit `_l1TokenRelayer`
/// @audit `_l1MessageRelayer`
/// @audit `_l2TargetChainId`
28:     constructor(
29:         address _olas,
30:         address _l1Dispenser,
31:         address _l1TokenRelayer,
32:         address _l1MessageRelayer,
33:         uint256 _l2TargetChainId,
34:         address _wormholeCore,
35:         uint256 _wormholeTargetChainId
36:     )
37:         DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
38:         TokenBase(_l1MessageRelayer, _l1TokenRelayer, _wormholeCore)
39:     {
```

- *WormholeTargetDispenserL2.sol* ( [64-75](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L64-L75) ):

```solidity
/// @audit `_olas`
/// @audit `_proxyFactory`
/// @audit `_l2MessageRelayer`
/// @audit `_l1DepositProcessor`
64:     constructor(
65:         address _olas,
66:         address _proxyFactory,
67:         address _l2MessageRelayer,
68:         address _l1DepositProcessor,
69:         uint256 _l1SourceChainId,
70:         address _wormholeCore,
71:         address _l2TokenRelayer
72:     )
73:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
74:         TokenBase(_l2MessageRelayer, _l2TokenRelayer, _wormholeCore)
75:     {
```

</details>

### [L-10] Unsafe solidity low-level call can cause gas grief attack

Using the low-level calls of a solidity address can leave the contract open to gas grief attacks. These attacks occur when the called contract returns a large amount of data.
So when calling an external contract, it is necessary to check the length of the return data before reading/copying it (using `returndatasize()`).

<details>
<summary>There are 6 instances (click to show):</summary>

- *StakingBase.sol* ( [407-407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L407-L407), [417-417](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L417-L417) ):

```solidity
407:         (bool success, bytes memory returnData) = activityChecker.staticcall(activityData);

417:             (success, returnData) = activityChecker.staticcall(activityData);
```

- *StakingFactory.sol* ( [216-216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L216-L216) ):

```solidity
216:         (bool success, bytes memory returnData) = instance.call(initPayload);
```

- *StakingNativeToken.sol* ( [33-33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingNativeToken.sol#L33-L33) ):

```solidity
33:         (bool success, ) = to.call{value: amount}("");
```

- *StakingVerifier.sol* ( [207-207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L207-L207) ):

```solidity
207:         (bool success, bytes memory returnData) = instance.staticcall(tokenData);
```

- *DefaultTargetDispenserL2.sol* ( [161-161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L161-L161) ):

```solidity
161:             (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
```

</details>

### [L-11] Functions calling contracts/addresses with transfer hooks should be protected by reentrancy guard

Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks opens the users of this protocol up to [read-only reentrancy vulnerability](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/) with no way to protect them except by block-listing the entire protocol.

<details>
<summary>There are 8 instances (click to show):</summary>

- *StakingBase.sol* ( [797-797](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L797-L797), [864-864](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L864-L864) ):

```solidity
797:         IService(serviceRegistry).safeTransferFrom(msg.sender, address(this), serviceId);

864:         IService(serviceRegistry).safeTransferFrom(address(this), msg.sender, serviceId);
```

- *StakingToken.sol* ( [108-108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L108-L108), [125-125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L125-L125) ):

```solidity
108:         SafeTransferLib.safeTransfer(stakingToken, to, amount);

125:         SafeTransferLib.safeTransferFrom(stakingToken, msg.sender, address(this), amount);
```

- *Dispenser.sol* ( [420-420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L420-L420), [456-456](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L456-L456) ):

```solidity
420:             IToken(olas).transfer(depositProcessor, transferAmount);

456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]);
```

- *DefaultTargetDispenserL2.sol* ( [443-443](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L443-L443) ):

```solidity
443:             bool success = IToken(olas).transfer(newL2TargetDispenser, amount);
```

- *EthereumDepositProcessor.sol* ( [112-112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L112-L112) ):

```solidity
112:                 IToken(olas).transfer(timelock, refundAmount);
```

</details>

### [L-12] Critical functions should be controlled by time locks

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).

<details>
<summary>There are 21 instances (click to show):</summary>

- *VoteWeighting.sol* ( [324-324](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L324-L324), [368-368](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L368-L368), [386-386](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L386-L386) ):

```solidity
324:     function addNomineeEVM(address account, uint256 chainId) external {

368:     function changeOwner(address newOwner) external {

386:     function changeDispenser(address newDispenser) external {
```

- *StakingFactory.sol* ( [110-110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L110-L110), [127-127](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L127-L127), [249-249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L249-L249) ):

```solidity
110:     function changeOwner(address newOwner) external {

127:     function changeVerifier(address newVerifier) external {

249:     function setInstanceStatus(address instance, bool isEnabled) external {
```

- *StakingVerifier.sol* ( [96-96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L96-L96) ):

```solidity
96:     function changeOwner(address newOwner) external {
```

- *Dispenser.sol* ( [636-636](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L636-L636), [655-655](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L655-L655) ):

```solidity
636:     function changeOwner(address newOwner) external {

655:     function changeManagers(address _tokenomics, address _treasury, address _voteWeighting) external {
```

- *Tokenomics.sol* ( [526-526](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L526-L526), [546-546](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L546-L546), [565-565](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L565-L565), [592-592](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L592-L592), [616-616](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L616-L616) ):

```solidity
526:     function changeTokenomicsImplementation(address implementation) external {

546:     function changeOwner(address newOwner) external {

565:     function changeManagers(address _treasury, address _depository, address _dispenser) external {

592:     function changeRegistries(address _componentRegistry, address _agentRegistry, address _serviceRegistry) external {

616:     function changeDonatorBlacklist(address _donatorBlacklist) external {
```

- *DefaultDepositProcessorL1.sol* ( [186-186](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L186-L186), [206-206](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L206-L206) ):

```solidity
186:     function _setL2TargetDispenser(address l2Dispenser) internal {

206:     function setL2TargetDispenser(address l2Dispenser) external virtual {
```

- *DefaultTargetDispenserL2.sol* ( [247-247](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L247-L247) ):

```solidity
247:     function changeOwner(address newOwner) external {
```

- *PolygonDepositProcessorL1.sol* ( [98-98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L98-L98), [117-117](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L117-L117) ):

```solidity
98:     function setFxChildTunnel(address l2Dispenser) public override {

117:     function setL2TargetDispenser(address l2Dispenser) external override {
```

- *PolygonTargetDispenserL2.sol* ( [59-59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L59-L59) ):

```solidity
59:     function setFxRootTunnel(address l1Processor) external override {
```

- *WormholeDepositProcessorL1.sol* ( [132-132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L132-L132) ):

```solidity
132:     function setL2TargetDispenser(address l2Dispenser) external override {
```

</details>

### [L-13] Missing contract existence checks before low-level calls

Low-level calls return success if there is no code present at the specified address. In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`.

There are 5 instances:

- *StakingBase.sol* ( [407-407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L407-L407), [417-417](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L417-L417) ):

```solidity
407:         (bool success, bytes memory returnData) = activityChecker.staticcall(activityData);

417:             (success, returnData) = activityChecker.staticcall(activityData);
```

- *StakingFactory.sol* ( [216-216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L216-L216) ):

```solidity
216:         (bool success, bytes memory returnData) = instance.call(initPayload);
```

- *StakingVerifier.sol* ( [207-207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L207-L207) ):

```solidity
207:         (bool success, bytes memory returnData) = instance.staticcall(tokenData);
```

- *DefaultTargetDispenserL2.sol* ( [161-161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L161-L161) ):

```solidity
161:             (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
```

### [L-14] Duplicate data may be pushed to the array

When data is pushed into the array, it does not overwrite or remove the existing identical elements. Duplicate data added to the state array can cause problems such as operations being repeated incorrectly, assets being assigned repeatedly or weights being calculated repeatedly.

There is 1 instance:

- *StakingBase.sol* ( [794-794](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L794-L794) ):

```solidity
/// @audit stake()
794:         setServiceIds.push(serviceId);
```

### [L-15] Sending tokens within loops

Performing token transfers in a loop is not recommended due to the "Fail-Silently" issue. If one transfer fails, the entire transaction fails, which can be problematic when dealing with multiple transfers. This can prevent subsequent recipients from receiving their transfers. Additionally, it can be exploited by malicious contracts to disrupt transfers. To mitigate this, the "withdraw pattern" is recommended, where each recipient triggers their own payment, separating transfer operations and reducing the chance of interference.

<details>
<summary>There are 2 instances (click to show):</summary>

- *Dispenser.sol* ( [450-497](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L450-L497) ):

```solidity
450:         for (uint256 i = 0; i < chainIds.length; ++i) {
451:             // Get the deposit processor contract address
452:             address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];
453: 
454:             // Transfer corresponding OLAS amounts to deposit processors
455:             if (transferAmounts[i] > 0) {
456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]);
457:             }
458: 
459:             // Find zero staking incentives
460:             uint256 numActualTargets;
461:             bool[] memory positions = new bool[](stakingTargets[i].length);
462:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {
463:                 if (stakingIncentives[i][j] > 0) {
464:                     positions[j] = true;
465:                     ++numActualTargets;
466:                 }
467:             }
468: 
469:             // Allocate updated arrays accounting only for nonzero staking incentives
470:             bytes32[] memory updatedStakingTargets = new bytes32[](numActualTargets);
471:             uint256[] memory updatedStakingAmounts = new uint256[](numActualTargets);
472:             uint256 numPos;
473:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {
474:                 if (positions[j]) {
475:                     updatedStakingTargets[numPos] = stakingTargets[i][j];
476:                     updatedStakingAmounts[numPos] = stakingIncentives[i][j];
477:                     ++numPos;
478:                 }
479:             }
480: 
481:             // Address conversion depending on chain Ids
482:             if (chainIds[i] <= MAX_EVM_CHAIN_ID) {
483:                 // Convert to EVM addresses
484:                 address[] memory stakingTargetsEVM = new address[](updatedStakingTargets.length);
485:                 for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {
486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));
487:                 }
488: 
489:                 // Send to EVM chains
490:                 IDepositProcessor(depositProcessor).sendMessageBatch{value:valueAmounts[i]}(stakingTargetsEVM,
491:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);
492:             } else {
493:                 // Send to non-EVM chains
494:                 IDepositProcessor(depositProcessor).sendMessageBatchNonEVM{value:valueAmounts[i]}(updatedStakingTargets,
495:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);
496:             }
497:         }
```

- *EthereumDepositProcessor.sol* ( [94-122](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L94-L122) ):

```solidity
94:         for (uint256 i = 0; i < targets.length; ++i) {
95:             address target = targets[i];
96:             uint256 amount = stakingIncentives[i];
97: 
98:             // Check the target validity address and staking parameters, and get emissions amount
99:             uint256 limitAmount = IStakingFactory(stakingFactory).verifyInstanceAndGetEmissionsAmount(target);
100: 
101:             // If the limit amount is zero, something is wrong with the target
102:             if (limitAmount == 0) {
103:                 revert TargetEmissionsZero(target);
104:             }
105: 
106:             // Check the amount limit and adjust, if necessary
107:             if (amount > limitAmount) {
108:                 uint256 refundAmount = amount - limitAmount;
109:                 amount = limitAmount;
110: 
111:                 // Send refund amount to the DAO address (timelock)
112:                 IToken(olas).transfer(timelock, refundAmount);
113: 
114:                 emit AmountRefunded(target, refundAmount);
115:             }
116: 
117:             // Approve the OLAS amount for the staking target
118:             IToken(olas).approve(target, amount);
119:             IStaking(target).deposit(amount);
120: 
121:             emit StakingTargetDeposited(target, amount);
122:         }
```

</details>

### [L-16] Code does not follow the best practice of check-effects-interaction

Code should follow the best-practice of [check-effects-interaction](https://blockchain-academy.hs-mittweida.de/courses/solidity-coding-beginners-to-intermediate/lessons/solidity-11-coding-patterns/topic/checks-effects-interactions/), where state variables are updated before any external calls are made. Doing so prevents a large class of reentrancy bugs.

<details>
<summary>There are 119 instances (click to show):</summary>

- *StakingBase.sol* ( [413-413](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L413-L413), [416-416](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L416-L416), [417-417](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L417-L417), [421-421](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L421-L421), [574-574](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L574-L574), [575-575](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L575-L575), [576-576](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L576-L576), [577-577](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L577-L577), [579-579](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L579-L579), [608-608](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L608-L608), [613-613](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L613-L613), [614-614](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L614-L614), [620-620](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L620-L620), [622-622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L622-L622), [624-624](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L624-L624), [626-626](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L626-L626), [627-627](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L627-L627), [628-628](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L628-L628), [629-629](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L629-L629), [633-633](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L633-L633), [634-634](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L634-L634), [635-635](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L635-L635), [636-636](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L636-L636), [639-639](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L639-L639), [641-641](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L641-L641), [643-643](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L643-L643), [645-645](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L645-L645), [648-648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L648-L648), [650-650](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L650-L650), [651-651](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L651-L651), [652-652](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L652-L652), [653-653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L653-L653), [657-657](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L657-L657), [661-661](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L661-L661), [667-667](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L667-L667), [669-669](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L669-L669), [671-671](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L671-L671), [673-673](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L673-L673), [678-678](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L678-L678), [679-679](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L679-L679), [683-683](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L683-L683), [685-685](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L685-L685), [691-691](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L691-L691), [703-703](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L703-L703), [706-706](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L706-L706), [899-899](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L899-L899), [904-904](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L904-L904), [906-906](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L906-L906) ):

```solidity
/// @audit `staticcall()` is called on line 407
413:             currentNonces = abi.decode(returnData, (uint256[]));

/// @audit `staticcall()` is called on line 407
416:             activityData = abi.encodeCall(IActivityChecker.isRatioPass, (currentNonces, lastNonces, ts));

/// @audit `staticcall()` is called on line 407
417:             (success, returnData) = activityChecker.staticcall(activityData);

/// @audit `staticcall()` is called on line 407
421:                 ratioPass = abi.decode(returnData, (bool));

/// @audit `_checkRatioPass()` is called on line 569, triggering an external call - `staticcall()`
574:                     eligibleServiceRewards[numServices] = rewardsPerSecond * ts;

/// @audit `_checkRatioPass()` is called on line 569, triggering an external call - `staticcall()`
575:                     totalRewards += eligibleServiceRewards[numServices];

/// @audit `_checkRatioPass()` is called on line 569, triggering an external call - `staticcall()`
576:                     eligibleServiceIds[numServices] = serviceIds[i];

/// @audit `_checkRatioPass()` is called on line 569, triggering an external call - `staticcall()`
577:                     ++numServices;

/// @audit `_checkRatioPass()` is called on line 569, triggering an external call - `staticcall()`
579:                     serviceInactivity[i] = ts;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
608:         evictServiceIds = new uint256[](serviceIds.length);

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
613:             finalEligibleServiceIds = new uint256[](numServices);

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
614:             finalEligibleServiceRewards = new uint256[](numServices);

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
620:                 for (uint256 i = 1; i < numServices; ++i) {

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
622:                     updatedReward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
624:                     updatedTotalRewards += updatedReward;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
626:                     curServiceId = eligibleServiceIds[i];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
627:                     finalEligibleServiceIds[i] = eligibleServiceIds[i];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
628:                     finalEligibleServiceRewards[i] = updatedReward;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
629:                     mapServiceInfo[curServiceId].reward += updatedReward;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
633:                 updatedReward = (eligibleServiceRewards[0] * lastAvailableRewards) / totalRewards;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
634:                 updatedTotalRewards += updatedReward;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
635:                 curServiceId = eligibleServiceIds[0];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
636:                 finalEligibleServiceIds[0] = eligibleServiceIds[0];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
639:                     updatedReward += lastAvailableRewards - updatedTotalRewards;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
641:                 finalEligibleServiceRewards[0] = updatedReward;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
643:                 mapServiceInfo[curServiceId].reward += updatedReward;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
645:                 lastAvailableRewards = 0;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
648:                 for (uint256 i = 0; i < numServices; ++i) {

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
650:                     curServiceId = eligibleServiceIds[i];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
651:                     finalEligibleServiceIds[i] = eligibleServiceIds[i];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
652:                     finalEligibleServiceRewards[i] = eligibleServiceRewards[i];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
653:                     mapServiceInfo[curServiceId].reward += eligibleServiceRewards[i];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
657:                 lastAvailableRewards -= totalRewards;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
661:             availableRewards = lastAvailableRewards;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
667:             numServices = 0;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
669:             for (uint256 i = 0; i < serviceIds.length; ++i) {

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
671:                 curServiceId = serviceIds[i];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
673:                 mapServiceInfo[curServiceId].nonces = serviceNonces[i];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
678:                     serviceInactivity[i] = mapServiceInfo[curServiceId].inactivity + serviceInactivity[i];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
679:                     mapServiceInfo[curServiceId].inactivity = serviceInactivity[i];

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
683:                         evictServiceIds[i] = curServiceId;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
685:                         numServices++;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
691:                     mapServiceInfo[curServiceId].inactivity = 0;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
703:             tsCheckpoint = block.timestamp;

/// @audit `_calculateStakingRewards()` is called on line 603, triggering an external call - `staticcall()`
706:             epochCounter = eCounter + 1;

/// @audit `_calculateStakingRewards()` is called on line 896, triggering an external call - `staticcall()`
899:         for (uint256 i = 0; i < numServices; ++i) {

/// @audit `_calculateStakingRewards()` is called on line 896, triggering an external call - `staticcall()`
904:                     reward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards;

/// @audit `_calculateStakingRewards()` is called on line 896, triggering an external call - `staticcall()`
906:                     reward = eligibleServiceRewards[i];
```

- *StakingFactory.sol* ( [237-237](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L237-L237), [239-239](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L239-L239), [243-243](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L243-L243) ):

```solidity
/// @audit `call()` is called on line 216
237:         mapInstanceParams[instance] = instanceParams;

/// @audit `call()` is called on line 216
239:         nonce = localNonce + 1;

/// @audit `call()` is called on line 216
243:         _locked = 1;
```

- *Dispenser.sol* ( [462-462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L462-L462), [464-464](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L464-L464), [465-465](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L465-L465), [473-473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L473-L473), [475-475](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L475-L475), [476-476](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L476-L476), [477-477](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L477-L477), [485-485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L485-L485), [486-486](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L486-L486), [606-606](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L606-L606), [608-608](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L608-L608), [609-609](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L609-L609), [610-610](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L610-L610), [611-611](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L611-L611), [619-619](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L619-L619), [620-620](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L620-L620), [622-622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L622-L622), [623-623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L623-L623), [625-625](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L625-L625), [630-630](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L630-L630), [745-745](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L745-L745), [815-815](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L815-L815), [818-818](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L818-L818), [822-822](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L822-L822), [836-836](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L836-L836), [881-881](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L881-L881), [905-905](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L905-L905), [906-906](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L906-L906), [913-913](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L913-L913), [916-916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L916-L916), [918-918](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L918-L918), [921-921](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L921-L921), [924-924](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L924-L924), [926-926](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L926-L926), [933-933](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L933-L933), [936-936](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L936-L936), [938-938](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L938-L938), [941-941](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L941-L941), [944-944](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L944-L944), [997-997](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L997-L997), [1008-1008](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1008-L1008), [1016-1016](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1016-L1016), [1017-1017](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1017-L1017), [1020-1020](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1020-L1020), [1021-1021](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1021-L1021), [1023-1023](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1023-L1023), [1034-1034](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1034-L1034), [1046-1046](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1046-L1046), [1112-1112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1112-L1112), [1125-1125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1125-L1125), [1167-1167](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1167-L1167) ):

```solidity
/// @audit `transfer()` is called on line 456
462:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {

/// @audit `transfer()` is called on line 456
464:                     positions[j] = true;

/// @audit `transfer()` is called on line 456
465:                     ++numActualTargets;

/// @audit `transfer()` is called on line 456
473:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {

/// @audit `transfer()` is called on line 456
475:                     updatedStakingTargets[numPos] = stakingTargets[i][j];

/// @audit `transfer()` is called on line 456
476:                     updatedStakingAmounts[numPos] = stakingIncentives[i][j];

/// @audit `transfer()` is called on line 456
477:                     ++numPos;

/// @audit `transfer()` is called on line 456
485:                 for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {

/// @audit `transfer()` is called on line 456
486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));

/// @audit `getBridgingDecimals()` is called on line 596
606:                 mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;

/// @audit `getBridgingDecimals()` is called on line 596
608:                 stakingIncentives[i][j] = stakingIncentive;

/// @audit `getBridgingDecimals()` is called on line 596
609:                 transferAmounts[i] += stakingIncentive;

/// @audit `getBridgingDecimals()` is called on line 596
610:                 totalAmounts[0] += stakingIncentive;

/// @audit `getBridgingDecimals()` is called on line 596
611:                 totalAmounts[2] += returnAmount;

/// @audit `getBridgingDecimals()` is called on line 596
619:                         withheldAmount -= transferAmounts[i];

/// @audit `getBridgingDecimals()` is called on line 596
620:                         transferAmounts[i] = 0;

/// @audit `getBridgingDecimals()` is called on line 596
622:                         transferAmounts[i] -= withheldAmount;

/// @audit `getBridgingDecimals()` is called on line 596
623:                         withheldAmount = 0;

/// @audit `getBridgingDecimals()` is called on line 596
625:                     mapChainIdWithheldAmounts[chainIds[i]] = withheldAmount;

/// @audit `getBridgingDecimals()` is called on line 596
630:             totalAmounts[1] += transferAmounts[i];

/// @audit `epochCounter()` is called on line 745
745:         mapLastClaimedStakingEpochs[nomineeHash] = ITokenomics(tokenomics).epochCounter();

/// @audit `accountOwnerIncentives()` is called on line 807
815:                 olasBalance = IToken(olas).balanceOf(msg.sender);

/// @audit `accountOwnerIncentives()` is called on line 807
818:             success = ITreasury(treasury).withdrawToAccount(msg.sender, reward, topUp);

/// @audit `accountOwnerIncentives()` is called on line 807
822:                 olasBalance = IToken(olas).balanceOf(msg.sender) - olasBalance;

/// @audit `accountOwnerIncentives()` is called on line 807
836:         _locked = 1;

/// @audit `checkpointNominee()` is called on line 878
881:         for (uint256 j = firstClaimedEpoch; j < lastClaimedEpoch; ++j) {

/// @audit `checkpointNominee()` is called on line 878
905:                 stakingDiff = availableStakingAmount - totalWeightSum;

/// @audit `checkpointNominee()` is called on line 878
906:                 availableStakingAmount = totalWeightSum;

/// @audit `checkpointNominee()` is called on line 878
913:                 returnAmount = ((stakingDiff + availableStakingAmount) * stakingWeight) / 1e18;

/// @audit `checkpointNominee()` is called on line 878
916:                 stakingIncentive = (availableStakingAmount * stakingWeight) / 1e18;

/// @audit `checkpointNominee()` is called on line 878
918:                 returnAmount = (stakingDiff * stakingWeight) / 1e18;

/// @audit `checkpointNominee()` is called on line 878
921:                 availableStakingAmount = stakingPoint.maxStakingAmount;

/// @audit `checkpointNominee()` is called on line 878
924:                     returnAmount += stakingIncentive - availableStakingAmount;

/// @audit `checkpointNominee()` is called on line 878
926:                     stakingIncentive = availableStakingAmount;

/// @audit `checkpointNominee()` is called on line 878
933:                     normalizedStakingAmount *= 10 ** (18 - bridgingDecimals);

/// @audit `checkpointNominee()` is called on line 878
936:                     returnAmount += stakingIncentive - normalizedStakingAmount;

/// @audit `checkpointNominee()` is called on line 878
938:                     stakingIncentive = normalizedStakingAmount;

/// @audit `checkpointNominee()` is called on line 878
941:                 totalStakingIncentive += stakingIncentive;

/// @audit `checkpointNominee()` is called on line 878
944:             totalReturnAmount += returnAmount;

/// @audit `getBridgingDecimals()` is called on line 990
997:         mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;

/// @audit `getBridgingDecimals()` is called on line 990
1008:             transferAmount = stakingIncentive;

/// @audit `getBridgingDecimals()` is called on line 990
1016:                     withheldAmount -= transferAmount;

/// @audit `getBridgingDecimals()` is called on line 990
1017:                     transferAmount = 0;

/// @audit `getBridgingDecimals()` is called on line 990
1020:                     transferAmount -= withheldAmount;

/// @audit `getBridgingDecimals()` is called on line 990
1021:                     withheldAmount = 0;

/// @audit `getBridgingDecimals()` is called on line 990
1023:                 mapChainIdWithheldAmounts[chainId] = withheldAmount;

/// @audit `getBridgingDecimals()` is called on line 990
1034:                 olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;

/// @audit `getBridgingDecimals()` is called on line 990
1046:         _locked = 1;

/// @audit `_calculateStakingIncentivesBatch()` is called on line 1094, triggering an external call - `getBridgingDecimals()`
1112:                 olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;

/// @audit `_calculateStakingIncentivesBatch()` is called on line 1094, triggering an external call - `getBridgingDecimals()`
1125:         _locked = 1;

/// @audit `_checkpointNomineeAndGetClaimedEpochCounters()` is called on line 1138, triggering an external call - `epochCounter()`
1167:         _locked = 1;
```

- *Tokenomics.sol* ( [1290-1290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1290-L1290) ):

```solidity
/// @audit `rebalanceTreasury()` is called on line 1285
1290:             epochCounter = uint32(eCounter + 1);
```

- *DefaultTargetDispenserL2.sol* ( [166-166](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L166-L166), [172-172](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L172-L172), [182-182](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L182-L182), [183-183](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L183-L183), [199-199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L199-L199), [205-205](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L205-L205), [209-209](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L209-L209), [212-212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L212-L212), [296-296](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L296-L296), [302-302](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L302-L302), [403-403](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L403-L403), [450-450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L450-L450) ):

```solidity
/// @audit `call()` is called on line 161
166:                 limitAmount = abi.decode(returnData, (uint256));

/// @audit `call()` is called on line 161
172:                 localWithheldAmount += amount;

/// @audit `call()` is called on line 161
182:                 localWithheldAmount += targetWithheldAmount;

/// @audit `call()` is called on line 161
183:                 amount = limitAmount;

/// @audit `call()` is called on line 161
199:                 stakingQueueingNonces[queueHash] = true;

/// @audit `call()` is called on line 161
205:         stakingBatchNonce = batchNonce + 1;

/// @audit `call()` is called on line 161
209:             withheldAmount += localWithheldAmount;

/// @audit `call()` is called on line 161
212:         _locked = 1;

/// @audit `balanceOf()` is called on line 287
296:             stakingQueueingNonces[queueHash] = false;

/// @audit `balanceOf()` is called on line 287
302:         _locked = 1;

/// @audit `call()` is called on line 396
403:         _locked = 1;

/// @audit `balanceOf()` is called on line 440
450:         owner = address(0);
```

- *EthereumDepositProcessor.sol* ( [124-124](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L124-L124) ):

```solidity
/// @audit `verifyInstanceAndGetEmissionsAmount()` is called on line 99
124:         _locked = 1;
```

- *GnosisDepositProcessorL1.sol* ( [92-92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L92-L92) ):

```solidity
/// @audit `requireToPassMessage()` is called on line 90
92:             sequence = uint256(iMsg);
```

- *OptimismDepositProcessorL1.sol* ( [143-143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L143-L143) ):

```solidity
/// @audit `sendMessage()` is called on line 140
143:         sequence = stakingBatchNonce;
```

- *PolygonDepositProcessorL1.sol* ( [83-83](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L83-L83) ):

```solidity
/// @audit `_sendMessageToChild()` is called on line 80, triggering an external call - `sendMessageToChild()`
83:         sequence = stakingBatchNonce;
```

</details>

## Non Critical Issues

### [N-01] Import declarations should import specific identifiers, rather than the whole file

Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation (but does not save any gas).

There is 1 instance:

- *GnosisTargetDispenserL2.sol* ( [4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L4) ):

```solidity
4: import "./DefaultTargetDispenserL2.sol";
```

### [N-02] Names of `private`/`internal` functions should be prefixed with an underscore

It is recommended by the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#underscore-prefix-for-non-external-functions-and-variables).

There are 3 instances:

- *SafeTransferLib.sol* ( [22-22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/utils/SafeTransferLib.sol#L22-L22), [64-64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/utils/SafeTransferLib.sol#L64-L64) ):

```solidity
22:     function safeTransferFrom(address token, address from, address to, uint256 amount) internal {

64:     function safeTransfer(address token, address to, uint256 amount) internal {
```

- *WormholeTargetDispenserL2.sol* ( [133-139](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L133-L139) ):

```solidity
133:     function receivePayloadAndTokens(
134:         bytes memory data,
135:         TokenReceived[] memory receivedTokens,
136:         bytes32 sourceProcessor,
137:         uint16 sourceChainId,
138:         bytes32 deliveryHash
139:     ) internal override {
```

### [N-03] Use of `override` is unnecessary

[Starting from Solidity 0.8.8](https://docs.soliditylang.org/en/v0.8.20/contracts.html#function-overriding), the override keyword is not required when overriding an interface function, except for the case where the function is defined in multiple bases.

<details>
<summary>There are 21 instances (click to show):</summary>

- *StakingNativeToken.sol* ( [28-28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingNativeToken.sol#L28-L28) ):

```solidity
28:     function _withdraw(address to, uint256 amount) internal override {
```

- *StakingToken.sol* ( [74-74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L74-L74), [104-104](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L104-L104) ):

```solidity
74:     function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {

104:     function _withdraw(address to, uint256 amount) internal override {
```

- *ArbitrumDepositProcessorL1.sol* ( [119-124](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L119-L124) ):

```solidity
119:     function _sendMessage(
120:         address[] memory targets,
121:         uint256[] memory stakingIncentives,
122:         bytes memory bridgePayload,
123:         uint256 transferAmount
124:     ) internal override returns (uint256 sequence) {
```

- *ArbitrumTargetDispenserL2.sol* ( [53-53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L53-L53) ):

```solidity
53:     function _sendMessage(uint256 amount, bytes memory) internal override {
```

- *GnosisDepositProcessorL1.sol* ( [51-56](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L51-L56) ):

```solidity
51:     function _sendMessage(
52:         address[] memory targets,
53:         uint256[] memory stakingIncentives,
54:         bytes memory bridgePayload,
55:         uint256 transferAmount
56:     ) internal override returns (uint256 sequence) {
```

- *GnosisTargetDispenserL2.sol* ( [58-58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L58-L58) ):

```solidity
58:     function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {
```

- *OptimismDepositProcessorL1.sol* ( [95-100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L95-L100) ):

```solidity
95:     function _sendMessage(
96:         address[] memory targets,
97:         uint256[] memory stakingIncentives,
98:         bytes memory bridgePayload,
99:         uint256 transferAmount
100:     ) internal override returns (uint256 sequence) {
```

- *OptimismTargetDispenserL2.sol* ( [56-56](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L56-L56) ):

```solidity
56:     function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {
```

- *PolygonDepositProcessorL1.sol* ( [57-62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L57-L62), [91-91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L91-L91), [98-98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L98-L98), [117-117](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L117-L117) ):

```solidity
57:     function _sendMessage(
58:         address[] memory targets,
59:         uint256[] memory stakingIncentives,
60:         bytes memory,
61:         uint256 transferAmount
62:     ) internal override returns (uint256 sequence) {

91:     function _processMessageFromChild(bytes memory data) internal override {

98:     function setFxChildTunnel(address l2Dispenser) public override {

117:     function setL2TargetDispenser(address l2Dispenser) external override {
```

- *PolygonTargetDispenserL2.sol* ( [32-32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L32-L32), [52-52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L52-L52), [59-59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L59-L59) ):

```solidity
32:     function _sendMessage(uint256 amount, bytes memory) internal override {

52:     function _processMessageFromRoot(uint256, address sender, bytes memory data) internal override {

59:     function setFxRootTunnel(address l1Processor) external override {
```

- *WormholeDepositProcessorL1.sol* ( [59-64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L59-L64), [132-132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L132-L132), [138-138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L138-L138) ):

```solidity
59:     function _sendMessage(
60:         address[] memory targets,
61:         uint256[] memory stakingIncentives,
62:         bytes memory bridgePayload,
63:         uint256 transferAmount
64:     ) internal override returns (uint256 sequence) {

132:     function setL2TargetDispenser(address l2Dispenser) external override {

138:     function getBridgingDecimals() external pure override returns (uint256) {
```

- *WormholeTargetDispenserL2.sol* ( [89-89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L89-L89), [133-139](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L133-L139) ):

```solidity
89:     function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {

133:     function receivePayloadAndTokens(
134:         bytes memory data,
135:         TokenReceived[] memory receivedTokens,
136:         bytes32 sourceProcessor,
137:         uint16 sourceChainId,
138:         bytes32 deliveryHash
139:     ) internal override {
```

</details>

### [N-04] Unused `struct`s

Note that there may be cases where a struct superficially appears to be used, but this is only because there are multiple definitions of the struct in different files. In such cases, the struct definition should be moved into a separate file. The instances below are the unused definitions.

There is 1 instance:

- *Dispenser.sol* ( [162](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L162) ):

```solidity
162:     struct Nominee {
```

### [N-05] Add inline comments for unnamed parameters

`function func(address a, address)` -> `function func(address a, address /* b */)`

<details>
<summary>There are 12 instances (click to show):</summary>

- *StakingBase.sol* ( [366-370](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L366-L370) ):

```solidity
366:     function _checkTokenStakingDeposit(
367:         uint256 serviceId,
368:         uint256 stakingDeposit,
369:         uint32[] memory
370:     ) internal view virtual {
```

- *StakingFactory.sol* ( [28-28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L28-L28) ):

```solidity
28:     function getEmissionsAmountLimit(address) external view returns (uint256);
```

- *StakingToken.sol* ( [74-74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L74-L74) ):

```solidity
74:     function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {
```

- *StakingVerifier.sol* ( [255-255](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L255-L255) ):

```solidity
255:     function getEmissionsAmountLimit(address) external view returns (uint256) {
```

- *ArbitrumTargetDispenserL2.sol* ( [53-53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L53-L53) ):

```solidity
53:     function _sendMessage(uint256 amount, bytes memory) internal override {
```

- *EthereumDepositProcessor.sol* ( [130-135](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L130-L135), [155-160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L155-L160) ):

```solidity
130:     function sendMessage(
131:         address target,
132:         uint256 stakingIncentive,
133:         bytes memory,
134:         uint256
135:     ) external {

155:     function sendMessageBatch(
156:         address[] memory targets,
157:         uint256[] memory stakingIncentives,
158:         bytes memory,
159:         uint256
160:     ) external {
```

- *GnosisTargetDispenserL2.sol* ( [99-99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L99-L99) ):

```solidity
99:     function onTokenBridged(address, uint256, bytes calldata data) external {
```

- *PolygonDepositProcessorL1.sol* ( [57-62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L57-L62) ):

```solidity
57:     function _sendMessage(
58:         address[] memory targets,
59:         uint256[] memory stakingIncentives,
60:         bytes memory,
61:         uint256 transferAmount
62:     ) internal override returns (uint256 sequence) {
```

- *PolygonTargetDispenserL2.sol* ( [32-32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L32-L32), [52-52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L52-L52) ):

```solidity
32:     function _sendMessage(uint256 amount, bytes memory) internal override {

52:     function _processMessageFromRoot(uint256, address sender, bytes memory data) internal override {
```

- *WormholeDepositProcessorL1.sol* ( [106-112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L106-L112) ):

```solidity
106:     function receiveWormholeMessages(
107:         bytes memory data,
108:         bytes[] memory,
109:         bytes32 sourceAddress,
110:         uint16 sourceChain,
111:         bytes32 deliveryHash
112:     ) external {
```

</details>

### [N-06] Consider providing a ranged getter for array state variables

While the compiler automatically provides a getter for accessing single elements within a public state variable array, it doesn't provide a way to fetch the whole array, or subsets thereof. Consider adding a function to allow the fetching of slices of the array, especially if the contract doesn't already have multicall functionality.

There are 4 instances:

- *VoteWeighting.sol* ( [168-168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L168-L168), [170-170](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L170-L170) ):

```solidity
168:     Nominee[] public setNominees;

170:     Nominee[] public setRemovedNominees;
```

- *StakingBase.sol* ( [270-270](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L270-L270), [274-274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L274-L274) ):

```solidity
270:     uint256[] public agentIds;

274:     uint256[] public setServiceIds;
```

### [N-07] Assembly blocks should have extensive comments

Assembly blocks take a lot more time to audit than normal Solidity code, and often have gotchas and side-effects that the Solidity versions of the same code do not. Consider adding more comments explaining what is being done in every step of the assembly code, and describe why assembly is being used instead of Solidity.

<details>
<summary>There are 8 instances (click to show):</summary>

- *StakingFactory.sol* ( [207-209](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L207-L209), [221-224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L221-L224) ):

```solidity
207:         assembly {
208:             instance := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt)
209:         }

221:                 assembly {
222:                     let returnDataSize := mload(returnData)
223:                     revert(add(32, returnData), returnDataSize)
224:                 }
```

- *StakingProxy.sol* ( [34-36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingProxy.sol#L34-L36), [41-50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingProxy.sol#L41-L50), [55-57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingProxy.sol#L55-L57) ):

```solidity
34:         assembly {
35:             sstore(SERVICE_STAKING_PROXY, implementation)
36:         }

41:         assembly {
42:             let implementation := sload(SERVICE_STAKING_PROXY)
43:             calldatacopy(0, 0, calldatasize())
44:             let success := delegatecall(gas(), implementation, 0, calldatasize(), 0, 0)
45:             returndatacopy(0, 0, returndatasize())
46:             if eq(success, 0) {
47:                 revert(0, returndatasize())
48:             }
49:             return(0, returndatasize())
50:         }

55:         assembly {
56:             implementation := sload(SERVICE_STAKING_PROXY)
57:         }
```

- *Tokenomics.sol* ( [517-519](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L517-L519), [538-540](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L538-L540), [1083-1085](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1083-L1085) ):

```solidity
517:         assembly {
518:             implementation := sload(PROXY_TOKENOMICS)
519:         }

538:         assembly {
539:             sstore(PROXY_TOKENOMICS, implementation)
540:         }

1083:         assembly {
1084:             implementation := sload(PROXY_TOKENOMICS)
1085:         }
```

</details>

### [N-08] Consider splitting complex checks into multiple steps

Assign the expression's parts to intermediate local variables, and check against those instead.

<details>
<summary>There are 10 instances (click to show):</summary>

- *StakingBase.sol* ( [411-411](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L411-L411) ):

```solidity
411:         if (success && returnData.length > 63 && (returnData.length % 32 == 0)) {
```

- *StakingVerifier.sol* ( [82-82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L82-L82), [240-240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L240-L240) ):

```solidity
82:         if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {

240:         if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {
```

- *Dispenser.sol* ( [740-741](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L740-L741), [801-802](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L801-L802), [983-984](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L983-L984), [1080-1081](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1080-L1081) ):

```solidity
740:         if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
741:             ITreasury(treasury).paused() == 2) {

801:         if (currentPause == Pause.DevIncentivesPaused || currentPause == Pause.AllPaused ||
802:             ITreasury(treasury).paused() == 2) {

983:         if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
984:             ITreasury(treasury).paused() == 2) {

1080:         if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
1081:             ITreasury(treasury).paused() == 2) {
```

- *ArbitrumDepositProcessorL1.sol* ( [102-102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L102-L102), [141-141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L141-L141) ):

```solidity
102:         if (_l1ERC20Gateway == address(0) || _outbox == address(0) || _bridge == address(0)) {

141:         if (gasPriceBid < 2 || gasLimitMessage < 2 || maxSubmissionCostMessage == 0) {
```

- *DefaultDepositProcessorL1.sol* ( [67-67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L67-L67) ):

```solidity
67:         if (_l1Dispenser == address(0) || _l1TokenRelayer == address(0) || _l1MessageRelayer == address(0)) {
```

</details>

### [N-09] Complex casting

Consider whether the number of casts is really necessary, or whether using a different type would be more appropriate. Alternatively, add comments to explain in detail why the casts are necessary, and any implicit reasons why the cast does not introduce an overflow.

There are 3 instances:

- *VoteWeighting.sol* ( [515-515](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L515-L515) ):

```solidity
515:             slope: uint256(uint128(userSlope)) * weight / MAX_WEIGHT,
```

- *StakingFactory.sol* ( [147-147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L147-L147), [204-204](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L204-L204) ):

```solidity
147:             uint256(uint160(implementation)));

204:             uint256(uint160(implementation)));
```

### [N-10] Consider adding a block/deny-list

Doing so will significantly increase centralization, but will help to prevent hackers from using stolen tokens

There are 5 instances:

- *StakingBase.sol* ( [168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L168) ):

```solidity
168: abstract contract StakingBase is ERC721TokenReceiver {
```

- *StakingToken.sol* ( [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L44) ):

```solidity
44: contract StakingToken is StakingBase {
```

- *Dispenser.sol* ( [253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L253) ):

```solidity
253: contract Dispenser {
```

- *DefaultTargetDispenserL2.sol* ( [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L45) ):

```solidity
45: abstract contract DefaultTargetDispenserL2 is IBridgeErrors {
```

- *EthereumDepositProcessor.sol* ( [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L50) ):

```solidity
50: contract EthereumDepositProcessor {
```

### [N-11] Constants/Immutables redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants/immutables in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

<details>
<summary>There are 17 instances (click to show):</summary>

- *Dispenser.sol* ( [277-277](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L277-L277) ):

```solidity
/// @audit Seen in ./contracts/staking/EthereumDepositProcessor.sol#55
277:     address public immutable olas;
```

- *ArbitrumDepositProcessorL1.sol* ( [72-72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L72-L72) ):

```solidity
/// @audit Seen in ./contracts/staking/GnosisDepositProcessorL1.sol#34
72:     uint256 public constant BRIDGE_PAYLOAD_LENGTH = 160;
```

- *DefaultDepositProcessorL1.sol* ( [28-28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L28-L28), [30-30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L30-L30), [37-37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L37-L37) ):

```solidity
/// @audit Seen in ./contracts/staking/DefaultTargetDispenserL2.sol#62
28:     bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));

/// @audit Seen in ./contracts/staking/DefaultTargetDispenserL2.sol#64
30:     uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;

/// @audit Seen in ./contracts/staking/DefaultTargetDispenserL2.sol#72
37:     address public immutable olas;
```

- *DefaultTargetDispenserL2.sol* ( [62-62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L62-L62), [64-64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L64-L64), [72-72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L72-L72), [74-74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L74-L74) ):

```solidity
/// @audit Seen in ./contracts/staking/DefaultDepositProcessorL1.sol#28
62:     bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));

/// @audit Seen in ./contracts/staking/DefaultDepositProcessorL1.sol#30
64:     uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;

/// @audit Seen in ./contracts/staking/DefaultDepositProcessorL1.sol#37
72:     address public immutable olas;

/// @audit Seen in ./contracts/staking/EthereumDepositProcessor.sol#59
74:     address public immutable stakingFactory;
```

- *EthereumDepositProcessor.sol* ( [55-55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L55-L55), [59-59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L59-L59) ):

```solidity
/// @audit Seen in ./contracts/staking/DefaultTargetDispenserL2.sol#72
55:     address public immutable olas;

/// @audit Seen in ./contracts/staking/DefaultTargetDispenserL2.sol#74
59:     address public immutable stakingFactory;
```

- *GnosisDepositProcessorL1.sol* ( [34-34](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L34-L34) ):

```solidity
/// @audit Seen in ./contracts/staking/ArbitrumDepositProcessorL1.sol#72
34:     uint256 public constant BRIDGE_PAYLOAD_LENGTH = 32;
```

- *GnosisTargetDispenserL2.sol* ( [28-28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L28-L28) ):

```solidity
/// @audit Seen in ./contracts/staking/GnosisDepositProcessorL1.sol#34
28:     uint256 public constant BRIDGE_PAYLOAD_LENGTH = 32;
```

- *OptimismDepositProcessorL1.sol* ( [61-61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L61-L61) ):

```solidity
/// @audit Seen in ./contracts/staking/GnosisTargetDispenserL2.sol#28
61:     uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;
```

- *OptimismTargetDispenserL2.sol* ( [39-39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L39-L39) ):

```solidity
/// @audit Seen in ./contracts/staking/OptimismDepositProcessorL1.sol#61
39:     uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;
```

- *WormholeDepositProcessorL1.sol* ( [13-13](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L13-L13) ):

```solidity
/// @audit Seen in ./contracts/staking/OptimismTargetDispenserL2.sol#39
13:     uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;
```

- *WormholeTargetDispenserL2.sol* ( [52-52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L52-L52) ):

```solidity
/// @audit Seen in ./contracts/staking/WormholeDepositProcessorL1.sol#13
52:     uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;
```

</details>

### [N-12] Convert simple `if`-statements to ternary expressions

Converting some if statements to ternaries (such as `z = (a < b) ? x : y`) can make the code more concise and easier to read.

There is 1 instance:

- *StakingBase.sol* ( [903-907](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L903-L907) ):

```solidity
903:                 if (totalRewards > lastAvailableRewards) {
904:                     reward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards;
905:                 } else {
906:                     reward = eligibleServiceRewards[i];
907:                 }
```

### [N-13] Contracts should each be defined in separate files

Keeping each contract in a separate file makes it easier to work with multiple people, makes the code easier to maintain, and is a common practice on most projects. The following files each contains more than one contract/library/interface.

<details>
<summary>There are 19 instances (click to show):</summary>

- [*VoteWeighting.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol)

- [*StakingActivityChecker.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol)

- [*StakingBase.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol)

- [*StakingFactory.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol)

- [*StakingToken.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol)

- [*StakingVerifier.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol)

- [*Dispenser.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol)

- [*Tokenomics.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol)

- [*ArbitrumDepositProcessorL1.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol)

- [*ArbitrumTargetDispenserL2.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol)

- [*DefaultDepositProcessorL1.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol)

- [*DefaultTargetDispenserL2.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol)

- [*EthereumDepositProcessor.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol)

- [*GnosisDepositProcessorL1.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol)

- [*GnosisTargetDispenserL2.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol)

- [*OptimismDepositProcessorL1.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol)

- [*OptimismTargetDispenserL2.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol)

- [*PolygonDepositProcessorL1.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol)

- [*WormholeTargetDispenserL2.sol*](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol)

</details>

### [N-14] Contract timekeeping will break earlier than the Ethereum network itself will stop working

When a timestamp is downcast from `uint256` to `uint32`, the value will wrap in the year 2106, and the contracts will break. Other downcasts will have different endpoints. Consider whether your contract is intended to live past the size of the type being used.

There are 3 instances:

- *Tokenomics.sol* ( [469-469](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L469-L469), [478-478](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L478-L478), [1220-1220](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1220-L1220) ):

```solidity
469:         uint256 zeroYearSecondsLeft = uint32(_timeLaunch + ONE_YEAR - block.timestamp);

478:         mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);

1220:         tp.epochPoint.endTime = uint32(block.timestamp);
```

### [N-15] Consider emitting an event at the end of the constructor

This will allow users to easily exactly pinpoint when and by whom a contract was constructed.

<details>
<summary>There are 20 instances (click to show):</summary>

- *VoteWeighting.sol* ( [205-205](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L205-L205) ):

```solidity
205:     constructor(address _ve) {
```

- *StakingActivityChecker.sol* ( [24-24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol#L24-L24) ):

```solidity
24:     constructor(uint256 _livenessRatio) {
```

- *StakingFactory.sol* ( [103-103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L103-L103) ):

```solidity
103:     constructor(address _verifier) {
```

- *StakingProxy.sol* ( [27-27](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingProxy.sol#L27-L27) ):

```solidity
27:     constructor(address implementation) {
```

- *StakingVerifier.sol* ( [74-75](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L74-L75) ):

```solidity
74:     constructor(address _olas, uint256 _rewardsPerSecondLimit, uint256 _timeForEmissionsLimit,
75:         uint256 _numServicesLimit) {
```

- *Dispenser.sol* ( [315-323](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L315-L323) ):

```solidity
315:     constructor(
316:         address _olas,
317:         address _tokenomics,
318:         address _treasury,
319:         address _voteWeighting,
320:         bytes32 _retainer,
321:         uint256 _maxNumClaimingEpochs,
322:         uint256 _maxNumStakingTargets
323:     ) {
```

- *Tokenomics.sol* ( [377-379](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L377-L379) ):

```solidity
377:     constructor()
378:         TokenomicsConstants()
379:     {}
```

- *ArbitrumDepositProcessorL1.sol* ( [89-100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L89-L100) ):

```solidity
89:     constructor(
90:         address _olas,
91:         address _l1Dispenser,
92:         address _l1TokenRelayer,
93:         address _l1MessageRelayer,
94:         uint256 _l2TargetChainId,
95:         address _l1ERC20Gateway,
96:         address _outbox,
97:         address _bridge
98:     )
99:         DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
100:     {
```

- *ArbitrumTargetDispenserL2.sol* ( [36-44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L36-L44) ):

```solidity
36:     constructor(
37:         address _olas,
38:         address _proxyFactory,
39:         address _l2MessageRelayer,
40:         address _l1DepositProcessor,
41:         uint256 _l1SourceChainId
42:     )
43:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
44:     {
```

- *DefaultDepositProcessorL1.sol* ( [59-65](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L59-L65) ):

```solidity
59:     constructor(
60:         address _olas,
61:         address _l1Dispenser,
62:         address _l1TokenRelayer,
63:         address _l1MessageRelayer,
64:         uint256 _l2TargetChainId
65:     ) {
```

- *DefaultTargetDispenserL2.sol* ( [101-107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L101-L107) ):

```solidity
101:     constructor(
102:         address _olas,
103:         address _stakingFactory,
104:         address _l2MessageRelayer,
105:         address _l1DepositProcessor,
106:         uint256 _l1SourceChainId
107:     ) {
```

- *EthereumDepositProcessor.sol* ( [70-70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L70-L70) ):

```solidity
70:     constructor(address _olas, address _dispenser, address _stakingFactory, address _timelock) {
```

- *GnosisDepositProcessorL1.sol* ( [42-48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L42-L48) ):

```solidity
42:     constructor(
43:         address _olas,
44:         address _l1Dispenser,
45:         address _l1TokenRelayer,
46:         address _l1MessageRelayer,
47:         uint256 _l2TargetChainId
48:     ) DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId) {}
```

- *GnosisTargetDispenserL2.sol* ( [39-48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L39-L48) ):

```solidity
39:     constructor(
40:         address _olas,
41:         address _proxyFactory,
42:         address _l2MessageRelayer,
43:         address _l1DepositProcessor,
44:         uint256 _l1SourceChainId,
45:         address _l2TokenRelayer
46:     )
47:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
48:     {
```

- *OptimismDepositProcessorL1.sol* ( [76-85](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L76-L85) ):

```solidity
76:     constructor(
77:         address _olas,
78:         address _l1Dispenser,
79:         address _l1TokenRelayer,
80:         address _l1MessageRelayer,
81:         uint256 _l2TargetChainId,
82:         address _olasL2
83:     )
84:         DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
85:     {
```

- *OptimismTargetDispenserL2.sol* ( [47-53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L47-L53) ):

```solidity
47:     constructor(
48:         address _olas,
49:         address _proxyFactory,
50:         address _l2MessageRelayer,
51:         address _l1DepositProcessor,
52:         uint256 _l1SourceChainId
53:     ) DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId) {}
```

- *PolygonDepositProcessorL1.sol* ( [36-47](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L36-L47) ):

```solidity
36:     constructor(
37:         address _olas,
38:         address _l1Dispenser,
39:         address _l1TokenRelayer,
40:         address _l1MessageRelayer,
41:         uint256 _l2TargetChainId,
42:         address _checkpointManager,
43:         address _predicate
44:     )
45:         DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
46:         FxBaseRootTunnel(_checkpointManager, _l1MessageRelayer)
47:     {
```

- *PolygonTargetDispenserL2.sol* ( [20-29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L20-L29) ):

```solidity
20:     constructor(
21:         address _olas,
22:         address _proxyFactory,
23:         address _l2MessageRelayer,
24:         address _l1DepositProcessor,
25:         uint256 _l1SourceChainId
26:     )
27:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
28:         FxBaseChildTunnel(_l2MessageRelayer)
29:     {}
```

- *WormholeDepositProcessorL1.sol* ( [28-39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L28-L39) ):

```solidity
28:     constructor(
29:         address _olas,
30:         address _l1Dispenser,
31:         address _l1TokenRelayer,
32:         address _l1MessageRelayer,
33:         uint256 _l2TargetChainId,
34:         address _wormholeCore,
35:         uint256 _wormholeTargetChainId
36:     )
37:         DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
38:         TokenBase(_l1MessageRelayer, _l1TokenRelayer, _wormholeCore)
39:     {
```

- *WormholeTargetDispenserL2.sol* ( [64-75](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L64-L75) ):

```solidity
64:     constructor(
65:         address _olas,
66:         address _proxyFactory,
67:         address _l2MessageRelayer,
68:         address _l1DepositProcessor,
69:         uint256 _l1SourceChainId,
70:         address _wormholeCore,
71:         address _l2TokenRelayer
72:     )
73:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
74:         TokenBase(_l2MessageRelayer, _l2TokenRelayer, _wormholeCore)
75:     {
```

</details>

### [N-16] Empty function body without comments

Empty function body in solidity is not recommended, consider adding some comments to the body.

<details>
<summary>There are 4 instances (click to show):</summary>

- *Tokenomics.sol* ( [377-379](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L377-L379) ):

```solidity
377:     constructor()
378:         TokenomicsConstants()
379:     {}
```

- *GnosisDepositProcessorL1.sol* ( [42-48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L42-L48) ):

```solidity
42:     constructor(
43:         address _olas,
44:         address _l1Dispenser,
45:         address _l1TokenRelayer,
46:         address _l1MessageRelayer,
47:         uint256 _l2TargetChainId
48:     ) DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId) {}
```

- *OptimismTargetDispenserL2.sol* ( [47-53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L47-L53) ):

```solidity
47:     constructor(
48:         address _olas,
49:         address _proxyFactory,
50:         address _l2MessageRelayer,
51:         address _l1DepositProcessor,
52:         uint256 _l1SourceChainId
53:     ) DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId) {}
```

- *PolygonTargetDispenserL2.sol* ( [20-29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L20-L29) ):

```solidity
20:     constructor(
21:         address _olas,
22:         address _proxyFactory,
23:         address _l2MessageRelayer,
24:         address _l1DepositProcessor,
25:         uint256 _l1SourceChainId
26:     )
27:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
28:         FxBaseChildTunnel(_l2MessageRelayer)
29:     {}
```

</details>

### [N-17] Events are emitted without the sender information

When an action is triggered based on a user's action, not being able to filter based on who triggered the action makes event processing a lot more cumbersome. Including the `msg.sender` the events of these types of action will make events much more useful to end users, especially when `msg.sender` is not `tx.origin`.

<details>
<summary>There are 51 instances (click to show):</summary>

- *VoteWeighting.sol* ( [380-380](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L380-L380), [393-393](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L393-L393), [635-635](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L635-L635) ):

```solidity
380:         emit OwnerUpdated(newOwner);

393:         emit DispenserUpdated(newDispenser);

635:         emit RemoveNominee(account, chainId, newSum);
```

- *StakingBase.sol* ( [687-687](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L687-L687), [708-709](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L708-L709) ):

```solidity
687:                         emit ServiceInactivityWarning(eCounter, curServiceId, serviceInactivity[i]);

708:             emit Checkpoint(eCounter, lastAvailableRewards, finalEligibleServiceIds, finalEligibleServiceRewards,
709:                 epochLength);
```

- *StakingFactory.sol* ( [122-122](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L122-L122), [134-134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L134-L134), [261-261](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L261-L261) ):

```solidity
122:         emit OwnerUpdated(newOwner);

134:         emit VerifierUpdated(newVerifier);

261:         emit InstanceStatusChanged(instance, isEnabled);
```

- *StakingVerifier.sol* ( [108-108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L108-L108), [121-121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L121-L121), [160-160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L160-L160), [249-249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L249-L249) ):

```solidity
108:         emit OwnerUpdated(newOwner);

121:         emit SetImplementationsCheck(setCheck);

160:         emit ImplementationsWhitelistUpdated(implementations, statuses, setCheck);

249:         emit StakingLimitsUpdated(_rewardsPerSecondLimit, _timeForEmissionsLimit, _numServicesLimit, emissionsLimit);
```

- *Dispenser.sol* ( [648-648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L648-L648), [664-664](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L664-L664), [670-670](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L670-L670), [676-676](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L676-L676), [697-697](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L697-L697), [727-727](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L727-L727), [1191-1191](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1191-L1191), [1236-1236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1236-L1236), [1249-1249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1249-L1249) ):

```solidity
648:         emit OwnerUpdated(newOwner);

664:             emit TokenomicsUpdated(_tokenomics);

670:             emit TreasuryUpdated(_treasury);

676:             emit VoteWeightingUpdated(_voteWeighting);

697:         emit StakingParamsUpdated(_maxNumClaimingEpochs, _maxNumStakingTargets);

727:         emit SetDepositProcessorChainIds(depositProcessors, chainIds);

1191:         emit WithheldAmountSynced(chainId, amount, withheldAmount);

1236:         emit WithheldAmountSynced(chainId, amount, withheldAmount);

1249:         emit PauseDispenser(pauseState);
```

- *Tokenomics.sol* ( [541-541](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L541-L541), [558-558](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L558-L558), [574-574](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L574-L574), [579-579](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L579-L579), [584-584](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L584-L584), [601-601](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L601-L601), [605-605](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L605-L605), [609-609](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L609-L609), [623-623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L623-L623), [692-693](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L692-L693), [744-745](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L744-L745), [779-779](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L779-L779), [801-801](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L801-L801), [821-821](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L821-L821), [840-840](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L840-L840), [1189-1189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1189-L1189), [1196-1196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1196-L1196), [1213-1213](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1213-L1213), [1278-1278](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1278-L1278), [1287-1288](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1287-L1288) ):

```solidity
541:         emit TokenomicsImplementationUpdated(implementation);

558:         emit OwnerUpdated(newOwner);

574:             emit TreasuryUpdated(_treasury);

579:             emit DepositoryUpdated(_depository);

584:             emit DispenserUpdated(_dispenser);

601:             emit ComponentRegistryUpdated(_componentRegistry);

605:             emit AgentRegistryUpdated(_agentRegistry);

609:             emit ServiceRegistryUpdated(_serviceRegistry);

623:         emit DonatorBlacklistUpdated(_donatorBlacklist);

692:         emit TokenomicsParametersUpdateRequested(epochCounter + 1, _devsPerCapital, _codePerDev, _epsilonRate, _epochLen,
693:             _veOLASThreshold);

744:         emit IncentiveFractionsUpdateRequested(eCounter, _rewardComponentFraction, _rewardAgentFraction,
745:             _maxBondFraction, _topUpComponentFraction, _topUpAgentFraction, _stakingFraction);

779:         emit StakingParamsUpdateRequested(eCounter, _maxStakingIncentive, _minStakingWeight);

801:             emit EffectiveBondUpdated(epochCounter, eBond);

821:         emit EffectiveBondUpdated(epochCounter, eBond);

840:         emit StakingRefunded(eCounter, amount);

1189:             emit TokenomicsParametersUpdated(eCounter + 1);

1196:             emit IncentiveFractionsUpdated(eCounter + 1);

1213:             emit StakingParamsUpdated(eCounter + 1);

1278:             emit IDFUpdated(idf);

1287:             emit EpochSettled(eCounter, incentives[1], accountRewards, accountTopUps, curMaxBond, incentives[7],
1288:                 incentives[8]);
```

- *DefaultDepositProcessorL1.sol* ( [118-118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L118-L118), [155-155](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L155-L155), [181-181](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L181-L181), [201-201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L201-L201) ):

```solidity
118:         emit MessageReceived(l2TargetDispenser, l2TargetChainId, data);

155:         emit MessagePosted(sequence, targets, stakingIncentives, transferAmount);

181:         emit MessagePosted(sequence, targets, stakingIncentives, transferAmount);

201:         emit L2TargetDispenserUpdated(l2Dispenser);
```

- *DefaultTargetDispenserL2.sol* ( [259-259](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L259-L259), [293-293](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L293-L293), [360-360](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L360-L360), [371-371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L371-L371) ):

```solidity
259:         emit OwnerUpdated(newOwner);

293:             emit StakingTargetDeposited(target, amount);

360:         emit TargetDispenserPaused();

371:         emit TargetDispenserUnpaused();
```

- *PolygonDepositProcessorL1.sol* ( [112-112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L112-L112) ):

```solidity
112:         emit FxChildTunnelUpdated(l2Dispenser);
```

- *PolygonTargetDispenserL2.sol* ( [73-73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L73-L73) ):

```solidity
73:         emit FxRootTunnelUpdated(l1Processor);
```

</details>

### [N-18] Inconsistent floating version pragma

Source files are using different floating version syntax, this is prone to compilation errors, and is not conducive to the code reliability and maintainability.

There are 4 instances:

- *VoteWeighting.sol* ( [2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L2) ):

```solidity
2: pragma solidity ^0.8.25;
```

- *SafeTransferLib.sol* ( [2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/utils/SafeTransferLib.sol#L2) ):

```solidity
2: pragma solidity ^0.8.23;
```

- *IDonatorBlacklist.sol* ( [2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/interfaces/IDonatorBlacklist.sol#L2) ):

```solidity
2: pragma solidity ^0.8.18;
```

- *DefaultDepositProcessorL1.sol* ( [2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L2) ):

```solidity
2: pragma solidity ^0.8.25;
```

### [N-19] Imports could be organized more systematically

The contract's interface should be imported first, followed by each of the interfaces it uses, followed by all other files. The examples below do not follow this layout.

<details>
<summary>There are 5 instances (click to show):</summary>

- *Tokenomics.sol* ( [6-6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L6-L6) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
6: import {IDonatorBlacklist} from "./interfaces/IDonatorBlacklist.sol";
```

- *ArbitrumDepositProcessorL1.sol* ( [4-4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L4-L4) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
4: import {DefaultDepositProcessorL1, IToken} from "./DefaultDepositProcessorL1.sol";
```

- *GnosisDepositProcessorL1.sol* ( [4-4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L4-L4) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
4: import {DefaultDepositProcessorL1, IToken} from "./DefaultDepositProcessorL1.sol";
```

- *OptimismDepositProcessorL1.sol* ( [4-4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L4-L4) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
4: import {DefaultDepositProcessorL1, IToken} from "./DefaultDepositProcessorL1.sol";
```

- *PolygonDepositProcessorL1.sol* ( [4-4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L4-L4) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
4: import {DefaultDepositProcessorL1, IToken} from "./DefaultDepositProcessorL1.sol";
```

</details>

### [N-20] Invalid NatSpec comment style

NatSpec comment in solidity should use `///` or `/* ... */` syntax.

There is 1 instance:

- *IBridgeErrors.sol* ( [88-89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/interfaces/IBridgeErrors.sol#L88-L89) ):

```solidity
88: 
89:     // @dev Reentrancy guard.
```

### [N-21] safe-contracts should be upgraded to a newer version

These contracts import contracts from `@gnosis.pm/safe-contracts`, but they are not using [the latest version](https://github.com/safe-global/safe-contracts/releases). Using the latest version ensures contract security with fixes for known vulnerabilities, access to the latest feature updates, and enhanced community support and engagement.

The imported version is `^1.3.0`.
The imported version is `^1.3.0`.

There are 2 instances:

- Global finding

- Global finding

### [N-22] Expressions for constant values should use `immutable` rather than `constant`

While it doesn't save any gas because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constants` should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the constructor.

There are 6 instances:

- *VoteWeighting.sol* ( [159-159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L159-L159) ):

```solidity
159:     uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;
```

- *Dispenser.sol* ( [275-275](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L275-L275) ):

```solidity
275:     uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;
```

- *DefaultDepositProcessorL1.sol* ( [28-28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L28-L28), [30-30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L30-L30) ):

```solidity
28:     bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));

30:     uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;
```

- *DefaultTargetDispenserL2.sol* ( [62-62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L62-L62), [64-64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L64-L64) ):

```solidity
62:     bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));

64:     uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;
```

### [N-23] Contracts containing only utility functions should be made into libraries

Consider using a `library` instead of a `contract` to provide utility functions.

There is 1 instance:

- *TokenomicsConstants.sol* ( [8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/TokenomicsConstants.sol#L8) ):

```solidity
8: abstract contract TokenomicsConstants {
```

### [N-24] Functions should be named in mixedCase style

As the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.21/style-guide.html#function-names) suggests: functions should be named in mixedCase style.

There are 2 instances:

- *Tokenomics.sol* ( [1033](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1033), [1472](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1472) ):

```solidity
1033:     function _calculateIDF(uint256 treasuryRewards, uint256 numNewOwners) internal view returns (uint256 idf) {

1472:     function getLastIDF() external view returns (uint256) {
```

### [N-25] Variable names for `immutable`s should use UPPER_CASE_WITH_UNDERSCORES

For `immutable` variable names, each word should use all capital letters, with underscores separating each word (UPPER_CASE_WITH_UNDERSCORES)

<details>
<summary>There are 28 instances (click to show):</summary>

- *VoteWeighting.sol* ( [161-161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L161-L161) ):

```solidity
161:     address public immutable ve;
```

- *StakingActivityChecker.sol* ( [20-20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol#L20-L20) ):

```solidity
20:     uint256 public immutable livenessRatio;
```

- *StakingVerifier.sol* ( [51-51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L51-L51) ):

```solidity
51:     address public immutable olas;
```

- *Dispenser.sol* ( [277-277](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L277-L277), [279-279](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L279-L279), [281-281](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L281-L281) ):

```solidity
277:     address public immutable olas;

279:     bytes32 public immutable retainer;

281:     bytes32 public immutable retainerHash;
```

- *ArbitrumDepositProcessorL1.sol* ( [74-74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L74-L74), [76-76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L76-L76), [78-78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L78-L78) ):

```solidity
74:     address public immutable l1ERC20Gateway;

76:     address public immutable outbox;

78:     address public immutable bridge;
```

- *ArbitrumTargetDispenserL2.sol* ( [25-25](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L25-L25) ):

```solidity
25:     address public immutable l1AliasedDepositProcessor;
```

- *DefaultDepositProcessorL1.sol* ( [37-37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L37-L37), [39-39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L39-L39), [41-41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L41-L41), [43-43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L43-L43), [45-45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L45-L45) ):

```solidity
37:     address public immutable olas;

39:     address public immutable l1Dispenser;

41:     address public immutable l1TokenRelayer;

43:     address public immutable l1MessageRelayer;

45:     uint256 public immutable l2TargetChainId;
```

- *DefaultTargetDispenserL2.sol* ( [72-72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L72-L72), [74-74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L74-L74), [76-76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L76-L76), [78-78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L78-L78), [80-80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L80-L80) ):

```solidity
72:     address public immutable olas;

74:     address public immutable stakingFactory;

76:     address public immutable l2MessageRelayer;

78:     address public immutable l1DepositProcessor;

80:     uint256 public immutable l1SourceChainId;
```

- *EthereumDepositProcessor.sol* ( [55-55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L55-L55), [57-57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L57-L57), [59-59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L59-L59), [61-61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L61-L61) ):

```solidity
55:     address public immutable olas;

57:     address public immutable dispenser;

59:     address public immutable stakingFactory;

61:     address public immutable timelock;
```

- *GnosisTargetDispenserL2.sol* ( [30-30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L30-L30) ):

```solidity
30:     address public immutable l2TokenRelayer;
```

- *OptimismDepositProcessorL1.sol* ( [63-63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L63-L63) ):

```solidity
63:     address public immutable olasL2;
```

- *PolygonDepositProcessorL1.sol* ( [26-26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L26-L26) ):

```solidity
26:     address public immutable predicate;
```

- *WormholeDepositProcessorL1.sol* ( [15-15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L15-L15) ):

```solidity
15:     uint256 public immutable wormholeTargetChainId;
```

</details>

### [N-26] Named imports of parent contracts are missing

Consider adding named imports for all parent contracts.

There is 1 instance:

- *GnosisTargetDispenserL2.sol* ( [26-26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L26-L26) ):

```solidity
/// @audit DefaultTargetDispenserL2
26: contract GnosisTargetDispenserL2 is DefaultTargetDispenserL2 {
```

### [N-27] Constants should be put on the left side of comparisons

Putting constants on the left side of comparison statements is a best practice known as [Yoda conditions](https://en.wikipedia.org/wiki/Yoda_conditions).
Although solidity's static typing system prevents accidental assignments within conditionals, adopting this practice can improve code readability and consistency, especially when working across multiple languages.

<details>
<summary>There are 165 instances (click to show):</summary>

- *VoteWeighting.sol* ( [207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L207), [260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L260), [260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L260), [314](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L314), [326](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L326), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L331), [351](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L351), [375](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L375), [598](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L598), [631](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L631), [647](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L647), [653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L653), [748](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L748), [765](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L765), [799](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L799) ):

```solidity
/// @audit put `address(0)` on the left
207:         if (_ve == address(0)) {

/// @audit put `0` on the left
260:         if (mapRemovedNominees[nomineeHash] == 0 && mapNomineeIds[nomineeHash] == 0) {

/// @audit put `0` on the left
260:         if (mapRemovedNominees[nomineeHash] == 0 && mapNomineeIds[nomineeHash] == 0) {

/// @audit put `address(0)` on the left
314:         if (localDispenser != address(0)) {

/// @audit put `address(0)` on the left
326:         if (account == address(0)) {

/// @audit put `0` on the left
331:         if (chainId == 0) {

/// @audit put `bytes32(0)` on the left
351:         if (account == bytes32(0)) {

/// @audit put `address(0)` on the left
375:         if (newOwner == address(0)) {

/// @audit put `0` on the left
598:         if (id == 0) {

/// @audit put `address(0)` on the left
631:         if (localDispenser != address(0)) {

/// @audit put `0` on the left
647:         if (mapRemovedNominees[nomineeHash] == 0) {

/// @audit put `0` on the left
653:         if (oldSlope.power == 0) {

/// @audit put `0` on the left
748:         if (id == 0) {

/// @audit put `0` on the left
765:         if (id == 0) {

/// @audit put `0` on the left
799:             if (mapNomineeIds[nomineeHash] == 0) {
```

- *StakingActivityChecker.sol* ( [26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol#L26) ):

```solidity
/// @audit put `0` on the left
26:         if (_livenessRatio == 0) {
```

- *StakingBase.sol* ( [282](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L282), [287](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L287), [287](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L287), [288](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L288), [288](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L288), [289](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L289), [289](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L289), [290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L290), [290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L290), [305](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L305), [305](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L305), [310](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L310), [342](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L342), [420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L420), [498](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L498), [721](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L721), [747](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L747), [826](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L826) ):

```solidity
/// @audit put `address(0)` on the left
282:         if (serviceRegistry != address(0)) {

/// @audit put `0` on the left
287:         if (_stakingParams.metadataHash == 0 || _stakingParams.maxNumServices == 0 ||

/// @audit put `0` on the left
287:         if (_stakingParams.metadataHash == 0 || _stakingParams.maxNumServices == 0 ||

/// @audit put `0` on the left
288:             _stakingParams.rewardsPerSecond == 0 || _stakingParams.livenessPeriod == 0 ||

/// @audit put `0` on the left
288:             _stakingParams.rewardsPerSecond == 0 || _stakingParams.livenessPeriod == 0 ||

/// @audit put `0` on the left
289:             _stakingParams.numAgentInstances == 0 || _stakingParams.timeForEmissions == 0 ||

/// @audit put `0` on the left
289:             _stakingParams.numAgentInstances == 0 || _stakingParams.timeForEmissions == 0 ||

/// @audit put `0` on the left
290:             _stakingParams.minNumStakingPeriods == 0 || _stakingParams.maxNumInactivityPeriods == 0) {

/// @audit put `0` on the left
290:             _stakingParams.minNumStakingPeriods == 0 || _stakingParams.maxNumInactivityPeriods == 0) {

/// @audit put `address(0)` on the left
305:         if (_stakingParams.serviceRegistry == address(0) || _stakingParams.activityChecker == address(0)) {

/// @audit put `address(0)` on the left
305:         if (_stakingParams.serviceRegistry == address(0) || _stakingParams.activityChecker == address(0)) {

/// @audit put `0` on the left
310:         if (_stakingParams.activityChecker.code.length == 0) {

/// @audit put `0` on the left
342:         if (_stakingParams.proxyHash == 0) {

/// @audit put `32` on the left
420:             if (success && returnData.length == 32) {

/// @audit put `0` on the left
498:         if (reward == 0) {

/// @audit put `0` on the left
721:         if (availableRewards == 0) {

/// @audit put `0` on the left
747:         if (configHash != 0 && configHash != service.configHash) {

/// @audit put `0` on the left
826:         if (serviceIds.length == 0) {
```

- *StakingFactory.sol* ( [117](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L117), [179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L179), [184](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L184), [195](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L195), [211](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L211), [231](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L231), [273](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L273), [284](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L284), [304](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L304) ):

```solidity
/// @audit put `address(0)` on the left
117:         if (newOwner == address(0)) {

/// @audit put `address(0)` on the left
179:         if (implementation == address(0)) {

/// @audit put `0` on the left
184:         if (implementation.code.length == 0) {

/// @audit put `address(0)` on the left
195:         if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyImplementation(implementation)) {

/// @audit put `address(0)` on the left
211:         if (instance == address(0)) {

/// @audit put `address(0)` on the left
231:         if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyInstance(instance, implementation)) {

/// @audit put `address(0)` on the left
273:         if (implementation == address(0)) {

/// @audit put `address(0)` on the left
284:         if (localVerifier != address(0)) {

/// @audit put `address(0)` on the left
304:             if (localVerifier != address(0)) {
```

- *StakingProxy.sol* ( [29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingProxy.sol#L29) ):

```solidity
/// @audit put `address(0)` on the left
29:         if (implementation == address(0)) {
```

- *StakingToken.sol* ( [63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L63), [63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L63) ):

```solidity
/// @audit put `address(0)` on the left
63:         if (_stakingToken == address(0) || _serviceRegistryTokenUtility == address(0)) {

/// @audit put `address(0)` on the left
63:         if (_stakingToken == address(0) || _serviceRegistryTokenUtility == address(0)) {
```

- *StakingVerifier.sol* ( [77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L77), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L82), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L82), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L82), [103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L103), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L142), [152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L152), [186](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L186), [212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L212), [240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L240), [240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L240), [240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L240) ):

```solidity
/// @audit put `address(0)` on the left
77:         if (_olas == address(0)) {

/// @audit put `0` on the left
82:         if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {

/// @audit put `0` on the left
82:         if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {

/// @audit put `0` on the left
82:         if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {

/// @audit put `address(0)` on the left
103:         if (newOwner == address(0)) {

/// @audit put `0` on the left
142:         if (implementations.length == 0 || implementations.length != statuses.length) {

/// @audit put `address(0)` on the left
152:             if (implementations[i] == address(0)) {

/// @audit put `0` on the left
186:         if (instance.code.length == 0) {

/// @audit put `32` on the left
212:             if (returnData.length == 32) {

/// @audit put `0` on the left
240:         if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {

/// @audit put `0` on the left
240:         if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {

/// @audit put `0` on the left
240:         if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {
```

- *Dispenser.sol* ( [330](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L330), [330](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L330), [330](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L330), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L331), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L331), [336](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L336), [336](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L336), [369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L369), [423](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L423), [482](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L482), [535](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L535), [643](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L643), [662](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L662), [668](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L668), [674](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L674), [690](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L690), [690](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L690), [712](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L712), [719](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L719), [861](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L861), [866](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L866), [966](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L966), [971](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L971), [1206](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1206), [1206](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1206) ):

```solidity
/// @audit put `address(0)` on the left
330:         if (_olas == address(0) || _tokenomics == address(0) || _treasury == address(0) ||

/// @audit put `address(0)` on the left
330:         if (_olas == address(0) || _tokenomics == address(0) || _treasury == address(0) ||

/// @audit put `address(0)` on the left
330:         if (_olas == address(0) || _tokenomics == address(0) || _treasury == address(0) ||

/// @audit put `0` on the left
331:             _voteWeighting == address(0) || _retainer == 0) {

/// @audit put `address(0)` on the left
331:             _voteWeighting == address(0) || _retainer == 0) {

/// @audit put `0` on the left
336:         if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {

/// @audit put `0` on the left
336:         if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {

/// @audit put `0` on the left
369:         if (firstClaimedEpoch == 0) {

/// @audit put `MAX_EVM_CHAIN_ID` on the left
423:         if (chainId <= MAX_EVM_CHAIN_ID) {

/// @audit put `MAX_EVM_CHAIN_ID` on the left
482:             if (chainIds[i] <= MAX_EVM_CHAIN_ID) {

/// @audit put `0` on the left
535:             if (stakingTargets[i].length == 0) {

/// @audit put `address(0)` on the left
643:         if (newOwner == address(0)) {

/// @audit put `address(0)` on the left
662:         if (_tokenomics != address(0)) {

/// @audit put `address(0)` on the left
668:         if (_treasury != address(0)) {

/// @audit put `address(0)` on the left
674:         if (_voteWeighting != address(0)) {

/// @audit put `0` on the left
690:         if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {

/// @audit put `0` on the left
690:         if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {

/// @audit put `0` on the left
712:         if (depositProcessors.length == 0 || depositProcessors.length != chainIds.length) {

/// @audit put `0` on the left
719:             if (chainIds[i] == 0) {

/// @audit put `0` on the left
861:         if (chainId == 0) {

/// @audit put `0` on the left
866:         if (stakingTarget == 0) {

/// @audit put `0` on the left
966:         if (chainId == 0) {

/// @audit put `0` on the left
971:         if (stakingTarget == 0) {

/// @audit put `0` on the left
1206:         if (chainId == 0 || amount == 0) {

/// @audit put `0` on the left
1206:         if (chainId == 0 || amount == 0) {
```

- *Tokenomics.sol* ( [422](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L422), [427](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L427), [427](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L427), [427](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L427), [427](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L427), [428](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L428), [428](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L428), [428](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L428), [429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L429), [533](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L533), [553](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L553), [572](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L572), [577](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L577), [582](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L582), [599](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L599), [603](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L603), [607](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L607), [670](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L670), [758](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L758), [758](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L758), [922](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L922), [934](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L934), [1003](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1003), [1087](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1087), [1285](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1285) ):

```solidity
/// @audit put `address(0)` on the left
422:         if (owner != address(0)) {

/// @audit put `address(0)` on the left
427:         if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||

/// @audit put `address(0)` on the left
427:         if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||

/// @audit put `address(0)` on the left
427:         if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||

/// @audit put `address(0)` on the left
427:         if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||

/// @audit put `address(0)` on the left
428:             _ve == address(0) || _componentRegistry == address(0) || _agentRegistry == address(0) ||

/// @audit put `address(0)` on the left
428:             _ve == address(0) || _componentRegistry == address(0) || _agentRegistry == address(0) ||

/// @audit put `address(0)` on the left
428:             _ve == address(0) || _componentRegistry == address(0) || _agentRegistry == address(0) ||

/// @audit put `address(0)` on the left
429:             _serviceRegistry == address(0)) {

/// @audit put `address(0)` on the left
533:         if (implementation == address(0)) {

/// @audit put `address(0)` on the left
553:         if (newOwner == address(0)) {

/// @audit put `address(0)` on the left
572:         if (_treasury != address(0)) {

/// @audit put `address(0)` on the left
577:         if (_depository != address(0)) {

/// @audit put `address(0)` on the left
582:         if (_dispenser != address(0)) {

/// @audit put `address(0)` on the left
599:         if (_componentRegistry != address(0)) {

/// @audit put `address(0)` on the left
603:         if (_agentRegistry != address(0)) {

/// @audit put `address(0)` on the left
607:         if (_serviceRegistry != address(0)) {

/// @audit put `17e18` on the left
670:         if (_epsilonRate > 0 && _epsilonRate <= 17e18) {

/// @audit put `0` on the left
758:         if (_maxStakingIncentive == 0 || _minStakingWeight == 0) {

/// @audit put `0` on the left
758:         if (_maxStakingIncentive == 0 || _minStakingWeight == 0) {

/// @audit put `0` on the left
922:                 if (numServiceUnits == 0) {

/// @audit put `0` on the left
934:                         if (lastEpoch == 0) {

/// @audit put `address(0)` on the left
1003:         if (bList != address(0) && IDonatorBlacklist(bList).isDonatorBlacklisted(donator)) {

/// @audit put `address(0)` on the left
1087:         if (implementation == address(0)) {

/// @audit put `0` on the left
1285:         if (incentives[1] == 0 || ITreasury(treasury).rebalanceTreasury(incentives[1])) {
```

- *ArbitrumDepositProcessorL1.sol* ( [102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L102), [102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L102), [102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L102), [126](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L126), [135](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L135), [141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L141), [154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L154) ):

```solidity
/// @audit put `address(0)` on the left
102:         if (_l1ERC20Gateway == address(0) || _outbox == address(0) || _bridge == address(0)) {

/// @audit put `address(0)` on the left
102:         if (_l1ERC20Gateway == address(0) || _outbox == address(0) || _bridge == address(0)) {

/// @audit put `address(0)` on the left
102:         if (_l1ERC20Gateway == address(0) || _outbox == address(0) || _bridge == address(0)) {

/// @audit put `BRIDGE_PAYLOAD_LENGTH` on the left
126:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {

/// @audit put `address(0)` on the left
135:         if (refundAccount == address(0)) {

/// @audit put `0` on the left
141:         if (gasPriceBid < 2 || gasLimitMessage < 2 || maxSubmissionCostMessage == 0) {

/// @audit put `0` on the left
154:             if (maxSubmissionCostToken == 0) {
```

- *DefaultDepositProcessorL1.sol* ( [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L67), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L67), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L67), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L72), [193](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L193) ):

```solidity
/// @audit put `address(0)` on the left
67:         if (_l1Dispenser == address(0) || _l1TokenRelayer == address(0) || _l1MessageRelayer == address(0)) {

/// @audit put `address(0)` on the left
67:         if (_l1Dispenser == address(0) || _l1TokenRelayer == address(0) || _l1MessageRelayer == address(0)) {

/// @audit put `address(0)` on the left
67:         if (_l1Dispenser == address(0) || _l1TokenRelayer == address(0) || _l1MessageRelayer == address(0)) {

/// @audit put `0` on the left
72:         if (_l2TargetChainId == 0) {

/// @audit put `address(0)` on the left
193:         if (l2Dispenser == address(0)) {
```

- *DefaultTargetDispenserL2.sol* ( [109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L109), [109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L109), [109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L109), [110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L110), [115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L115), [165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L165), [170](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L170), [189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L189), [254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L254), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L274), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L331), [337](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L337), [391](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L391), [425](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L425), [430](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L430), [460](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L460) ):

```solidity
/// @audit put `address(0)` on the left
109:         if (_olas == address(0) || _stakingFactory == address(0) || _l2MessageRelayer == address(0)

/// @audit put `address(0)` on the left
109:         if (_olas == address(0) || _stakingFactory == address(0) || _l2MessageRelayer == address(0)

/// @audit put `address(0)` on the left
109:         if (_olas == address(0) || _stakingFactory == address(0) || _l2MessageRelayer == address(0)

/// @audit put `address(0)` on the left
110:             || _l1DepositProcessor == address(0)) {

/// @audit put `0` on the left
115:         if (_l1SourceChainId == 0) {

/// @audit put `32` on the left
165:             if (success && returnData.length == 32) {

/// @audit put `0` on the left
170:             if (limitAmount == 0) {

/// @audit put `1` on the left
189:             if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {

/// @audit put `address(0)` on the left
254:         if (newOwner == address(0)) {

/// @audit put `2` on the left
274:         if (paused == 2) {

/// @audit put `2` on the left
331:         if (paused == 2) {

/// @audit put `0` on the left
337:         if (amount == 0) {

/// @audit put `0` on the left
391:         if (amount == 0) {

/// @audit put `1` on the left
425:         if (paused == 1) {

/// @audit put `0` on the left
430:         if (newL2TargetDispenser.code.length == 0) {

/// @audit put `address(0)` on the left
460:         if (owner == address(0)) {
```

- *EthereumDepositProcessor.sol* ( [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L72), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L72), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L72), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L72), [102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L102) ):

```solidity
/// @audit put `address(0)` on the left
72:         if (_olas == address(0) || _dispenser == address(0) || _stakingFactory == address(0) || _timelock == address(0)) {

/// @audit put `address(0)` on the left
72:         if (_olas == address(0) || _dispenser == address(0) || _stakingFactory == address(0) || _timelock == address(0)) {

/// @audit put `address(0)` on the left
72:         if (_olas == address(0) || _dispenser == address(0) || _stakingFactory == address(0) || _timelock == address(0)) {

/// @audit put `address(0)` on the left
72:         if (_olas == address(0) || _dispenser == address(0) || _stakingFactory == address(0) || _timelock == address(0)) {

/// @audit put `0` on the left
102:             if (limitAmount == 0) {
```

- *GnosisDepositProcessorL1.sol* ( [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L58), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L80) ):

```solidity
/// @audit put `BRIDGE_PAYLOAD_LENGTH` on the left
58:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {

/// @audit put `0` on the left
80:             if (gasLimitMessage == 0) {
```

- *GnosisTargetDispenserL2.sol* ( [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L50), [60](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L60) ):

```solidity
/// @audit put `address(0)` on the left
50:         if (_l2TokenRelayer == address(0)) {

/// @audit put `BRIDGE_PAYLOAD_LENGTH` on the left
60:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {
```

- *OptimismDepositProcessorL1.sol* ( [87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L87), [102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L102), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L121), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L121) ):

```solidity
/// @audit put `address(0)` on the left
87:         if (_olasL2 == address(0)) {

/// @audit put `BRIDGE_PAYLOAD_LENGTH` on the left
102:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {

/// @audit put `0` on the left
121:         if (cost == 0 || gasLimitMessage == 0) {

/// @audit put `0` on the left
121:         if (cost == 0 || gasLimitMessage == 0) {
```

- *OptimismTargetDispenserL2.sol* ( [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L58), [66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L66) ):

```solidity
/// @audit put `BRIDGE_PAYLOAD_LENGTH` on the left
58:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {

/// @audit put `0` on the left
66:         if (cost == 0) {
```

- *PolygonDepositProcessorL1.sol* ( [49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L49), [49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L49), [105](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L105) ):

```solidity
/// @audit put `address(0)` on the left
49:         if (_checkpointManager == address(0) || _predicate == address(0)) {

/// @audit put `address(0)` on the left
49:         if (_checkpointManager == address(0) || _predicate == address(0)) {

/// @audit put `address(0)` on the left
105:         if (l2Dispenser == address(0)) {
```

- *PolygonTargetDispenserL2.sol* ( [66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L66) ):

```solidity
/// @audit put `address(0)` on the left
66:         if (l1Processor == address(0)) {
```

- *WormholeDepositProcessorL1.sol* ( [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L41), [46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L46), [66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L66), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L74), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L84) ):

```solidity
/// @audit put `address(0)` on the left
41:         if (_wormholeCore == address(0)) {

/// @audit put `0` on the left
46:         if (_wormholeTargetChainId == 0) {

/// @audit put `BRIDGE_PAYLOAD_LENGTH` on the left
66:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {

/// @audit put `0` on the left
74:         if (gasLimitMessage == 0) {

/// @audit put `address(0)` on the left
84:         if (refundAccount == address(0)) {
```

- *WormholeTargetDispenserL2.sol* ( [77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L77), [77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L77), [91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L91), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L98), [154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L154) ):

```solidity
/// @audit put `address(0)` on the left
77:         if (_wormholeCore == address(0) || _l2TokenRelayer == address(0)) {

/// @audit put `address(0)` on the left
77:         if (_wormholeCore == address(0) || _l2TokenRelayer == address(0)) {

/// @audit put `BRIDGE_PAYLOAD_LENGTH` on the left
91:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {

/// @audit put `address(0)` on the left
98:         if (refundAccount == address(0)) {

/// @audit put `1` on the left
154:         if (receivedTokens.length != 1) {
```

</details>

### [N-28] `else`-block not required

One level of nesting can be removed by not having an `else` block when the `if`-block always jumps at the end. For example:
```solidity
if (condition) {
    body1...
    return x;
} else {
    body2...
}
```
can be changed to:
```solidity
if (condition) {
    body1...
    return x;
}
body2...
```

There are 3 instances:

- *VoteWeighting.sol* ( [748-750](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L748-L750), [765-767](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L765-L767) ):

```solidity
748:         if (id == 0) {
749:             revert ZeroValue();
750:         } else if (id > totalNumNominees) {

765:         if (id == 0) {
766:             revert ZeroValue();
767:         } else if (id > totalNumRemovedNominees) {
```

- *StakingBase.sol* ( [838-840](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L838-L840) ):

```solidity
838:             if (evictServiceIds[idx] == serviceId) {
839:                 break;
840:             } else if (serviceIds[idx] == serviceId) {
```

### [N-29] Large multiples of ten should use scientific notation

Use a scientific notation rather than decimal literals (e.g. `1e6` instead of `1000000`), for better code readability.

<details>
<summary>There are 7 instances (click to show):</summary>

- *VoteWeighting.sol* ( [148-148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L148-L148), [157-157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L157-L157) ):

```solidity
/// @audit 864_000 -> 864e3
148:     uint256 public constant WEIGHT_VOTE_DELAY = 864_000;

/// @audit 10_000 -> 1e4
157:     uint256 public constant MAX_WEIGHT = 10_000;
```

- *TokenomicsConstants.sol* ( [23-23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/TokenomicsConstants.sol#L23-L23) ):

```solidity
/// @audit 10_000 -> 1e4
23:     uint256 public constant MAX_STAKING_WEIGHT = 10_000;
```

- *DefaultDepositProcessorL1.sol* ( [33-33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L33-L33), [35-35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L35-L35) ):

```solidity
/// @audit 300_000 -> 3e5
33:     uint256 public constant TOKEN_GAS_LIMIT = 300_000;

/// @audit 2_000_000 -> 2e6
35:     uint256 public constant MESSAGE_GAS_LIMIT = 2_000_000;
```

- *DefaultTargetDispenserL2.sol* ( [67-67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L67-L67), [70-70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L70-L70) ):

```solidity
/// @audit 300_000 -> 3e5
67:     uint256 public constant GAS_LIMIT = 300_000;

/// @audit 2_000_000 -> 2e6
70:     uint256 public constant MAX_GAS_LIMIT = 2_000_000;
```

</details>

### [N-30] High cyclomatic complexity

Consider breaking down these blocks into more manageable units, by splitting things into utility functions, by reducing nesting, and by using early returns.

<details>
<summary>There are 4 instances (click to show):</summary>

- *StakingBase.sol* ( [278-361](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L278-L361), [805-872](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L805-L872) ):

```solidity
278:     function _initialize(
279:         StakingParams memory _stakingParams
280:     ) internal {
281:         // Double initialization check
282:         if (serviceRegistry != address(0)) {
283:             revert AlreadyInitialized();
284:         }
285:         
286:         // Initial checks
287:         if (_stakingParams.metadataHash == 0 || _stakingParams.maxNumServices == 0 ||
288:             _stakingParams.rewardsPerSecond == 0 || _stakingParams.livenessPeriod == 0 ||
289:             _stakingParams.numAgentInstances == 0 || _stakingParams.timeForEmissions == 0 ||
290:             _stakingParams.minNumStakingPeriods == 0 || _stakingParams.maxNumInactivityPeriods == 0) {
291:             revert ZeroValue();
292:         }
293: 
294:         // Check that the min number of staking periods is not smaller than the max number of inactivity periods
295:         // This check is necessary to avoid an attack scenario when the service is going to stake and unstake without
296:         // any meaningful activity and just occupy the staking slot
297:         if (_stakingParams.minNumStakingPeriods < _stakingParams.maxNumInactivityPeriods) {
298:             revert LowerThan(_stakingParams.minNumStakingPeriods, _stakingParams.maxNumInactivityPeriods);
299:         }
300: 
301:         // Check the rest of parameters
302:         if (_stakingParams.minStakingDeposit < 2) {
...... OMITTED ......
337:             agentId = _stakingParams.agentIds[i];
338:             agentIds.push(agentId);
339:         }
340: 
341:         // Check for the multisig proxy bytecode hash value
342:         if (_stakingParams.proxyHash == 0) {
343:             revert ZeroValue();
344:         }
345: 
346:         // Record provided multisig proxy bytecode hash
347:         proxyHash = _stakingParams.proxyHash;
348: 
349:         // Calculate min staking duration
350:         minStakingDuration = _stakingParams.minNumStakingPeriods * livenessPeriod;
351: 
352:         // Calculate max allowed inactivity duration
353:         maxInactivityDuration = _stakingParams.maxNumInactivityPeriods * livenessPeriod;
354: 
355:         // Calculate emissions amount
356:         emissionsAmount = _stakingParams.rewardsPerSecond * _stakingParams.maxNumServices *
357:             _stakingParams.timeForEmissions;
358: 
359:         // Set the checkpoint timestamp to be the deployment one
360:         tsCheckpoint = block.timestamp;
361:     }

805:     function unstake(uint256 serviceId) external returns (uint256 reward) {
806:         ServiceInfo storage sInfo = mapServiceInfo[serviceId];
807:         // Check for the service ownership
808:         if (msg.sender != sInfo.owner) {
809:             revert OwnerOnly(msg.sender, sInfo.owner);
810:         }
811: 
812:         // Get the staking start time
813:         // Note that if the service info exists, the service is staked or evicted, and thus start time is always valid
814:         uint256 tsStart = sInfo.tsStart;
815: 
816:         // Check that the service has staked long enough, or if there are no rewards left
817:         uint256 ts = block.timestamp - tsStart;
818:         if (ts <= minStakingDuration && availableRewards > 0) {
819:             revert NotEnoughTimeStaked(serviceId, ts, minStakingDuration);
820:         }
821: 
822:         // Call the checkpoint
823:         (uint256[] memory serviceIds, , , , uint256[] memory evictServiceIds) = checkpoint();
824: 
825:         // If the checkpoint was not successful, the serviceIds set is not returned and needs to be allocated
826:         if (serviceIds.length == 0) {
827:             serviceIds = getServiceIds();
828:             evictServiceIds = new uint256[](serviceIds.length);
829:         }
...... OMITTED ......
848:         uint256[] memory nonces = sInfo.nonces;
849:         address multisig = sInfo.multisig;
850: 
851:         // Clear all the data about the unstaked service
852:         // Delete the service info struct
853:         delete mapServiceInfo[serviceId];
854: 
855:         // Update the set of staked service Ids
856:         // If the index was not found, the service was evicted and is not part of staked services set
857:         if (inSet) {
858:             setServiceIds[idx] = setServiceIds[setServiceIds.length - 1];
859:             setServiceIds.pop();
860:         }
861: 
862:         // Transfer the service back to the owner
863:         // Note that the reentrancy is not possible due to the ServiceInfo struct being deleted
864:         IService(serviceRegistry).safeTransferFrom(address(this), msg.sender, serviceId);
865: 
866:         // Transfer accumulated rewards to the service multisig
867:         if (reward > 0) {
868:             _withdraw(multisig, reward);
869:         }
870: 
871:         emit ServiceUnstaked(epochCounter, serviceId, msg.sender, multisig, nonces, reward);
872:     }
```

- *Dispenser.sol* ( [441-498](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L441-L498) ):

```solidity
441:     function _distributeStakingIncentivesBatch(
442:         uint256[] memory chainIds,
443:         bytes32[][] memory stakingTargets,
444:         uint256[][] memory stakingIncentives,
445:         bytes[] memory bridgePayloads,
446:         uint256[] memory transferAmounts,
447:         uint256[] memory valueAmounts
448:     ) internal {
449:         // Traverse all staking targets
450:         for (uint256 i = 0; i < chainIds.length; ++i) {
451:             // Get the deposit processor contract address
452:             address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];
453: 
454:             // Transfer corresponding OLAS amounts to deposit processors
455:             if (transferAmounts[i] > 0) {
456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]);
457:             }
458: 
459:             // Find zero staking incentives
460:             uint256 numActualTargets;
461:             bool[] memory positions = new bool[](stakingTargets[i].length);
462:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {
463:                 if (stakingIncentives[i][j] > 0) {
464:                     positions[j] = true;
465:                     ++numActualTargets;
...... OMITTED ......
474:                 if (positions[j]) {
475:                     updatedStakingTargets[numPos] = stakingTargets[i][j];
476:                     updatedStakingAmounts[numPos] = stakingIncentives[i][j];
477:                     ++numPos;
478:                 }
479:             }
480: 
481:             // Address conversion depending on chain Ids
482:             if (chainIds[i] <= MAX_EVM_CHAIN_ID) {
483:                 // Convert to EVM addresses
484:                 address[] memory stakingTargetsEVM = new address[](updatedStakingTargets.length);
485:                 for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {
486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));
487:                 }
488: 
489:                 // Send to EVM chains
490:                 IDepositProcessor(depositProcessor).sendMessageBatch{value:valueAmounts[i]}(stakingTargetsEVM,
491:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);
492:             } else {
493:                 // Send to non-EVM chains
494:                 IDepositProcessor(depositProcessor).sendMessageBatchNonEVM{value:valueAmounts[i]}(updatedStakingTargets,
495:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);
496:             }
497:         }
498:     }
```

- *Tokenomics.sol* ( [1308-1376](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1308-L1376) ):

```solidity
1308:     function accountOwnerIncentives(
1309:         address account,
1310:         uint256[] memory unitTypes,
1311:         uint256[] memory unitIds
1312:     ) external returns (uint256 reward, uint256 topUp) {
1313:         // Check for the dispenser access
1314:         if (dispenser != msg.sender) {
1315:             revert ManagerOnly(msg.sender, dispenser);
1316:         }
1317: 
1318:         // Check array lengths
1319:         if (unitTypes.length != unitIds.length) {
1320:             revert WrongArrayLength(unitTypes.length, unitIds.length);
1321:         }
1322: 
1323:         // Component / agent registry addresses
1324:         address[] memory registries = new address[](2);
1325:         (registries[0], registries[1]) = (componentRegistry, agentRegistry);
1326: 
1327:         // Component / agent total supply
1328:         uint256[] memory registriesSupply = new uint256[](2);
1329:         for (uint256 i = 0; i < 2; ++i) {
1330:             registriesSupply[i] = IToken(registries[i]).totalSupply();
1331:         }
1332: 
...... OMITTED ......
1352:         }
1353: 
1354:         // Get the current epoch counter
1355:         uint256 curEpoch = epochCounter;
1356: 
1357:         for (uint256 i = 0; i < unitIds.length; ++i) {
1358:             // Get the last epoch number the incentives were accumulated for
1359:             uint256 lastEpoch = mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch;
1360:             // Finalize unit rewards and top-ups if there were pending ones from the previous epoch
1361:             // The finalization is needed when the trackServiceDonations() function did not take care of it
1362:             // since between last epoch the donations were received and this current epoch there were no more donations
1363:             if (lastEpoch > 0 && lastEpoch < curEpoch) {
1364:                 _finalizeIncentivesForUnitId(lastEpoch, unitTypes[i], unitIds[i]);
1365:                 // Change the last epoch number
1366:                 mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch = 0;
1367:             }
1368: 
1369:             // Accumulate total rewards and clear their balances
1370:             reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;
1371:             mapUnitIncentives[unitTypes[i]][unitIds[i]].reward = 0;
1372:             // Accumulate total top-ups and clear their balances
1373:             topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;
1374:             mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp = 0;
1375:         }
1376:     }
```

</details>

### [N-31] Typos

All typos should be corrected.

There is 1 instance:

- *ArbitrumDepositProcessorL1.sol* ( [167](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L167) ):

```solidity
/// @audit fot
167:         // Check fot msg.value to cover the total cost
```

### [N-32] Consider bounding input array length

The functions below take in an unbounded array, and make function calls for entries in the array. While the function will revert if it eventually runs out of gas, it may be a nicer user experience to require() that the length of the array is below some reasonable maximum, so that the user doesn't have to use up a full transaction's gas only to see that the transaction reverts.

<details>
<summary>There are 28 instances (click to show):</summary>

- *VoteWeighting.sol* ( [573](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L573), [793](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L793) ):

```solidity
573:         for (uint256 i = 0; i < accounts.length; ++i) {

793:         for (uint256 i = 0; i < accounts.length; ++i) {
```

- *StakingBase.sol* ( [380](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L380), [449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L449), [464](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L464) ):

```solidity
380:         for (uint256 i = 0; i < numAgentIds; ++i) {

449:         for (uint256 i = 0; i < totalNumServices; ++i) {

464:         for (uint256 i = numEvictServices; i > 0; --i) {
```

- *StakingToken.sol* ( [92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L92) ):

```solidity
92:         for (uint256 i = 0; i < serviceAgentIds.length; ++i) {
```

- *StakingVerifier.sol* ( [150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L150) ):

```solidity
150:         for (uint256 i = 0; i < implementations.length; ++i) {
```

- *Dispenser.sol* ( [450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L450), [462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L462), [473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L473), [485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L485), [526](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L526), [550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L550), [593](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L593), [600](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L600), [717](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L717) ):

```solidity
450:         for (uint256 i = 0; i < chainIds.length; ++i) {

462:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {

473:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {

485:                 for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {

526:         for (uint256 i = 0; i < chainIds.length; ++i) {

550:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {

593:         for (uint256 i = 0; i < chainIds.length; ++i) {

600:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {

717:         for (uint256 i = 0; i < chainIds.length; ++i) {
```

- *Tokenomics.sol* ( [904](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L904), [916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L916), [930](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L930), [960](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L960), [1010](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1010), [1329](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1329), [1335](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1335), [1357](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1357), [1401](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1401), [1407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1407), [1429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1429) ):

```solidity
904:         for (uint256 i = 0; i < numServices; ++i) {

916:             for (uint256 unitType = 0; unitType < 2; ++unitType) {

930:                     for (uint256 j = 0; j < numServiceUnits; ++j) {

960:                 for (uint256 j = 0; j < numServiceUnits; ++j) {

1010:         for (uint256 i = 0; i < numServices; ++i) {

1329:         for (uint256 i = 0; i < 2; ++i) {

1335:         for (uint256 i = 0; i < unitIds.length; ++i) {

1357:         for (uint256 i = 0; i < unitIds.length; ++i) {

1401:         for (uint256 i = 0; i < 2; ++i) {

1407:         for (uint256 i = 0; i < unitIds.length; ++i) {

1429:         for (uint256 i = 0; i < unitIds.length; ++i) {
```

- *EthereumDepositProcessor.sol* ( [94](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L94) ):

```solidity
94:         for (uint256 i = 0; i < targets.length; ++i) {
```

</details>

### [N-33] Unused contract variables

The following state variables are defined but not used.
It is recommended to check the code for logical omissions that cause them not to be used. If it's determined that they are not needed anywhere, it's best to remove them from the codebase to improve code clarity and minimize confusion.

There are 5 instances:

- *StakingBase.sol* ( [224-224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L224-L224) ):

```solidity
224:     string public constant VERSION = "0.2.0";
```

- *Tokenomics.sol* ( [359-359](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L359-L359), [361-361](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L361-L361), [363-363](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L363-L363) ):

```solidity
359:     mapping(uint256 => uint256) public mapServiceAmounts;

361:     mapping(address => uint256) public mapOwnerRewards;

363:     mapping(address => uint256) public mapOwnerTopUps;
```

- *TokenomicsConstants.sol* ( [10-10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/TokenomicsConstants.sol#L10-L10) ):

```solidity
10:     string public constant VERSION = "1.2.0";
```

### [N-34] Use delete instead of assigning values to `false`

The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

There is 1 instance:

- *DefaultTargetDispenserL2.sol* ( [296-296](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L296-L296) ):

```solidity
296:             stakingQueueingNonces[queueHash] = false;
```

### [N-35] Consider using `delete` rather than assigning zero to clear values

The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

<details>
<summary>There are 27 instances (click to show):</summary>

- *VoteWeighting.sol* ( [238-238](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L238-L238), [239-239](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L239-L239), [278-278](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L278-L278), [279-279](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L279-L279), [606-606](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L606-L606), [619-619](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L619-L619) ):

```solidity
238:                 pt.bias = 0;

239:                 pt.slope = 0;

278:                 pt.bias = 0;

279:                 pt.slope = 0;

606:         pointsWeight[nomineeHash][nextTime].bias = 0;

619:         mapNomineeIds[nomineeHash] = 0;
```

- *StakingBase.sol* ( [503-503](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L503-L503), [645-645](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L645-L645), [667-667](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L667-L667), [691-691](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L691-L691) ):

```solidity
503:         sInfo.reward = 0;

645:                 lastAvailableRewards = 0;

667:             numServices = 0;

691:                     mapServiceInfo[curServiceId].inactivity = 0;
```

- *Dispenser.sol* ( [620-620](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L620-L620), [623-623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L623-L623), [1017-1017](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1017-L1017), [1021-1021](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L1021-L1021) ):

```solidity
620:                         transferAmounts[i] = 0;

623:                         withheldAmount = 0;

1017:                     transferAmount = 0;

1021:                     withheldAmount = 0;
```

- *Tokenomics.sol* ( [859-859](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L859-L859), [875-875](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L875-L875), [1034-1034](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1034-L1034), [1179-1179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1179-L1179), [1185-1185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1185-L1185), [1260-1260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1260-L1260), [1267-1267](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1267-L1267), [1366-1366](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1366-L1366), [1371-1371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1371-L1371), [1374-1374](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1374-L1374) ):

```solidity
859:             mapUnitIncentives[unitType][unitId].pendingRelativeReward = 0;

875:             mapUnitIncentives[unitType][unitId].pendingRelativeTopUp = 0;

1034:         idf = 0;

1179:                 nextEpochLen = 0;

1185:                 nextVeOLASThreshold = 0;

1260:             tokenomicsParametersUpdated = 0;

1267:             tokenomicsParametersUpdated = 0;

1366:                 mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch = 0;

1371:             mapUnitIncentives[unitTypes[i]][unitIds[i]].reward = 0;

1374:             mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp = 0;
```

- *DefaultDepositProcessorL1.sol* ( [199-199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L199-L199) ):

```solidity
199:         owner = address(0);
```

- *DefaultTargetDispenserL2.sol* ( [342-342](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L342-L342), [450-450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L450-L450) ):

```solidity
342:         withheldAmount = 0;

450:         owner = address(0);
```

</details>

### [N-36] Use the latest Solidity version

Upgrading to the [latest solidity version](https://github.com/ethereum/solc-js/tags) (0.8.19 for L2s) can optimize gas usage, take advantage of new features and improve overall contract efficiency. Where possible, based on compatibility requirements, it is recommended to use newer/latest solidity version to take advantage of the latest optimizations and features.

There is 1 instance:

- *SafeTransferLib.sol* ( [2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/utils/SafeTransferLib.sol#L2) ):

```solidity
2: pragma solidity ^0.8.23;
```

### [N-37] Use transfer libraries instead of low level calls to transfer native tokens

Consider using [`SafeTransferLib.safeTransferETH`](https://github.com/transmissions11/solmate/blob/fadb2e2778adbf01c80275bfb99e5c14969d964b/src/utils/SafeTransferLib.sol#L15) or [`Address.sendValue`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/9e3f4d60c581010c4a3979480e07cc7752f124cc/contracts/utils/Address.sol#L41) for clearer semantic meaning instead of using a low level `call`.

There are 2 instances:

- *StakingNativeToken.sol* ( [33-33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingNativeToken.sol#L33-L33) ):

```solidity
33:         (bool success, ) = to.call{value: amount}("");
```

- *DefaultTargetDispenserL2.sol* ( [396-396](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L396-L396) ):

```solidity
396:         (bool success, ) = msg.sender.call{value: amount}("");
```

### [N-38] Use a struct to encapsulate multiple function parameters

If a function has too many parameters, replacing them with a struct can improve code readability and maintainability, increase reusability, and reduce the likelihood of errors when passing the parameters.

<details>
<summary>There are 10 instances (click to show):</summary>

- *ArbitrumDepositProcessorL1.sol* ( [26-34](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L26-L34), [48-57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L48-L57), [89-100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L89-L100) ):

```solidity
26:     function outboundTransferCustomRefund(
27:         address _l1Token,
28:         address _refundTo,
29:         address _to,
30:         uint256 _amount,
31:         uint256 _maxGas,
32:         uint256 _gasPriceBid,
33:         bytes calldata _data
34:     ) external payable returns (bytes memory res);

48:     function createRetryableTicket(
49:         address to,
50:         uint256 l2CallValue,
51:         uint256 maxSubmissionCost,
52:         address excessFeeRefundAddress,
53:         address callValueRefundAddress,
54:         uint256 gasLimit,
55:         uint256 maxFeePerGas,
56:         bytes calldata data
57:     ) external payable returns (uint256);

89:     constructor(
90:         address _olas,
91:         address _l1Dispenser,
92:         address _l1TokenRelayer,
93:         address _l1MessageRelayer,
94:         uint256 _l2TargetChainId,
95:         address _l1ERC20Gateway,
96:         address _outbox,
97:         address _bridge
98:     )
99:         DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
100:     {
```

- *ArbitrumTargetDispenserL2.sol* ( [36-44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L36-L44) ):

```solidity
36:     constructor(
37:         address _olas,
38:         address _proxyFactory,
39:         address _l2MessageRelayer,
40:         address _l1DepositProcessor,
41:         uint256 _l1SourceChainId
42:     )
43:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
44:     {
```

- *DefaultDepositProcessorL1.sol* ( [59-65](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L59-L65) ):

```solidity
59:     constructor(
60:         address _olas,
61:         address _l1Dispenser,
62:         address _l1TokenRelayer,
63:         address _l1MessageRelayer,
64:         uint256 _l2TargetChainId
65:     ) {
```

- *DefaultTargetDispenserL2.sol* ( [101-107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L101-L107) ):

```solidity
101:     constructor(
102:         address _olas,
103:         address _stakingFactory,
104:         address _l2MessageRelayer,
105:         address _l1DepositProcessor,
106:         uint256 _l1SourceChainId
107:     ) {
```

- *GnosisDepositProcessorL1.sol* ( [42-48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L42-L48) ):

```solidity
42:     constructor(
43:         address _olas,
44:         address _l1Dispenser,
45:         address _l1TokenRelayer,
46:         address _l1MessageRelayer,
47:         uint256 _l2TargetChainId
48:     ) DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId) {}
```

- *GnosisTargetDispenserL2.sol* ( [39-48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L39-L48) ):

```solidity
39:     constructor(
40:         address _olas,
41:         address _proxyFactory,
42:         address _l2MessageRelayer,
43:         address _l1DepositProcessor,
44:         uint256 _l1SourceChainId,
45:         address _l2TokenRelayer
46:     )
47:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
48:     {
```

- *OptimismDepositProcessorL1.sol* ( [20-27](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L20-L27), [76-85](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L76-L85) ):

```solidity
20:     function depositERC20To(
21:         address _l1Token,
22:         address _l2Token,
23:         address _to,
24:         uint256 _amount,
25:         uint32 _minGasLimit,
26:         bytes calldata _extraData
27:     ) external;

76:     constructor(
77:         address _olas,
78:         address _l1Dispenser,
79:         address _l1TokenRelayer,
80:         address _l1MessageRelayer,
81:         uint256 _l2TargetChainId,
82:         address _olasL2
83:     )
84:         DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
85:     {
```

</details>

### [N-39] Returning a struct instead of a bunch of variables is better

If a function returns [too many variables](https://docs.soliditylang.org/en/v0.8.21/contracts.html#returning-multiple-values), replacing them with a struct can improve code readability, maintainability and reusability.

<details>
<summary>There are 12 instances (click to show):</summary>

- *VoteWeighting.sol* ( [419-423](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L419-L423), [442-446](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L442-L446), [457-461](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L457-L461) ):

```solidity
419:     function _nomineeRelativeWeight(
420:         bytes32 account,
421:         uint256 chainId,
422:         uint256 time
423:     ) internal view returns (uint256 weight, uint256 totalSum) {

442:     function nomineeRelativeWeight(
443:         bytes32 account,
444:         uint256 chainId,
445:         uint256 time
446:     ) external view returns (uint256 relativeWeight, uint256 totalSum) {

457:     function nomineeRelativeWeightWrite(
458:         bytes32 account,
459:         uint256 chainId,
460:         uint256 time
461:     ) external returns (uint256 relativeWeight, uint256 totalSum) {
```

- *StakingBase.sol* ( [83-84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L83-L84), [398-402](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L398-L402) ):

```solidity
83:     function getAgentParams(uint256 serviceId) external view
84:         returns (uint256 numAgentIds, AgentParams[] memory agentParams);

398:     function _checkRatioPass(
399:         address multisig,
400:         uint256[] memory lastNonces,
401:         uint256 ts
402:     ) internal view returns (bool ratioPass, uint256[] memory currentNonces)
```

- *Dispenser.sol* ( [115-116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L115-L116), [185-185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L185-L185), [356-359](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L356-L359) ):

```solidity
115:     function accountOwnerIncentives(address account, uint256[] memory unitTypes, uint256[] memory unitIds) external
116:         returns (uint256 reward, uint256 topUp);

185:     function nomineeRelativeWeight(bytes32 account, uint256 chainId, uint256 time) external view returns (uint256, uint256);

356:     function _checkpointNomineeAndGetClaimedEpochCounters(
357:         bytes32 nomineeHash,
358:         uint256 numClaimedEpochs
359:     ) internal view returns (uint256 firstClaimedEpoch, uint256 lastClaimedEpoch) {
```

- *Tokenomics.sol* ( [53-54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L53-L54), [1308-1312](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1308-L1312), [1385-1389](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1385-L1389) ):

```solidity
53:     function getUnitIdsOfService(UnitType unitType, uint256 serviceId) external view
54:     returns (uint256 numUnitIds, uint32[] memory unitIds);

1308:     function accountOwnerIncentives(
1309:         address account,
1310:         uint256[] memory unitTypes,
1311:         uint256[] memory unitIds
1312:     ) external returns (uint256 reward, uint256 topUp) {

1385:     function getOwnerIncentives(
1386:         address account,
1387:         uint256[] memory unitTypes,
1388:         uint256[] memory unitIds
1389:     ) external view returns (uint256 reward, uint256 topUp) {
```

- *WormholeTargetDispenserL2.sol* ( [10-14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L10-L14) ):

```solidity
10:     function quoteEVMDeliveryPrice(
11:         uint16 targetChain,
12:         uint256 receiverValue,
13:         uint256 gasLimit
14:     ) external returns (uint256 nativePriceQuote, uint256 targetChainRefundPerGasUnused);
```

</details>

### [N-40] Missing event when a state variables is set in constructor

The initial states set in a constructor are important and should be recorded in the event.

<details>
<summary>There are 22 instances (click to show):</summary>

- *VoteWeighting.sol* ( [212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L212), [214](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L214) ):

```solidity
212:         owner = msg.sender;

214:         timeSum = block.timestamp / WEEK * WEEK;
```

- *StakingFactory.sol* ( [104](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L104), [105](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L105) ):

```solidity
104:         owner = msg.sender;

105:         verifier = _verifier;
```

- *StakingVerifier.sol* ( [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L86), [88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L88), [89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L89), [90](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L90), [91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L91) ):

```solidity
86:         owner = msg.sender;

88:         rewardsPerSecondLimit = _rewardsPerSecondLimit;

89:         timeForEmissionsLimit = _timeForEmissionsLimit;

90:         numServicesLimit = _numServicesLimit;

91:         emissionsLimit = _rewardsPerSecondLimit * _timeForEmissionsLimit * _numServicesLimit;
```

- *Dispenser.sol* ( [324](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L324), [325](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L325), [327](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L327), [341](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L341), [342](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L342), [343](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L343), [347](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L347), [348](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L348) ):

```solidity
324:         owner = msg.sender;

325:         _locked = 1;

327:         paused = Pause.StakingIncentivesPaused;

341:         tokenomics = _tokenomics;

342:         treasury = _treasury;

343:         voteWeighting = _voteWeighting;

347:         maxNumClaimingEpochs = _maxNumClaimingEpochs;

348:         maxNumStakingTargets = _maxNumStakingTargets;
```

- *DefaultDepositProcessorL1.sol* ( [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L86) ):

```solidity
86:         owner = msg.sender;
```

- *DefaultTargetDispenserL2.sol* ( [132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L132), [133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L133), [134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L134) ):

```solidity
132:         owner = msg.sender;

133:         paused = 1;

134:         _locked = 1;
```

- *EthereumDepositProcessor.sol* ( [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L80) ):

```solidity
80:         _locked = 1;
```

</details>

### [N-41] Empty bytes check is missing

Passing empty bytes to a function can cause unexpected behavior, such as certain operations failing, producing incorrect results, or wasting gas. It is recommended to check that all byte parameters are not empty.

<details>
<summary>There are 13 instances (click to show):</summary>

- *Dispenser.sol* ( [953-958](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L953-L958) ):

```solidity
/// @audit bridgePayload
953:     function claimStakingIncentives(
954:         uint256 numClaimedEpochs,
955:         uint256 chainId,
956:         bytes32 stakingTarget,
957:         bytes memory bridgePayload
958:     ) external payable {
```

- *ArbitrumDepositProcessorL1.sol* ( [196-196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L196-L196) ):

```solidity
/// @audit data
196:     function receiveMessage(bytes memory data) external {
```

- *ArbitrumTargetDispenserL2.sol* ( [66-66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L66-L66) ):

```solidity
/// @audit data
66:     function receiveMessage(bytes memory data) external payable {
```

- *DefaultDepositProcessorL1.sol* ( [132-137](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L132-L137), [164-169](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultDepositProcessorL1.sol#L164-L169) ):

```solidity
/// @audit bridgePayload
132:     function sendMessage(
133:         address target,
134:         uint256 stakingIncentive,
135:         bytes memory bridgePayload,
136:         uint256 transferAmount
137:     ) external virtual payable {

/// @audit bridgePayload
164:     function sendMessageBatch(
165:         address[] memory targets,
166:         uint256[] memory stakingIncentives,
167:         bytes memory bridgePayload,
168:         uint256 transferAmount
169:     ) external virtual payable {
```

- *DefaultTargetDispenserL2.sol* ( [311-311](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L311-L311), [323-323](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L323-L323) ):

```solidity
/// @audit data
311:     function processDataMaintenance(bytes memory data) external {

/// @audit bridgePayload
323:     function syncWithheldTokens(bytes memory bridgePayload) external payable {
```

- *GnosisDepositProcessorL1.sol* ( [98-98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L98-L98) ):

```solidity
/// @audit data
98:     function receiveMessage(bytes memory data) external {
```

- *GnosisTargetDispenserL2.sol* ( [87-87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L87-L87), [99-99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L99-L99) ):

```solidity
/// @audit data
87:     function receiveMessage(bytes memory data) external {

/// @audit data
99:     function onTokenBridged(address, uint256, bytes calldata data) external {
```

- *OptimismDepositProcessorL1.sol* ( [148-148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L148-L148) ):

```solidity
/// @audit data
148:     function receiveMessage(bytes memory data) external payable {
```

- *OptimismTargetDispenserL2.sol* ( [96-96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L96-L96) ):

```solidity
/// @audit data
96:     function receiveMessage(bytes memory data) external payable {
```

- *WormholeDepositProcessorL1.sol* ( [106-112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L106-L112) ):

```solidity
/// @audit data
106:     function receiveWormholeMessages(
107:         bytes memory data,
108:         bytes[] memory,
109:         bytes32 sourceAddress,
110:         uint16 sourceChain,
111:         bytes32 deliveryHash
112:     ) external {
```

</details>

### [N-42] Assembly block creates dirty bits

Writing data to the free memory pointer without later updating the free memory pointer will cause there to be dirty bits at that memory location. Not updating the free memory pointer will make it [harder](https://docs.soliditylang.org/en/latest/ir-breaking-changes.html#cleanup) for the optimizer to reason about whether the memory needs to be cleaned before use, which will lead to worse optimizations. Update the free memory pointer and annotate the block (`assembly ("memory-safe") { ... }`) to avoid this issue.

<details>
<summary>There are 2 instances (click to show):</summary>

- *SafeTransferLib.sol* ( [26-48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/utils/SafeTransferLib.sol#L26-L48), [68-89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/utils/SafeTransferLib.sol#L68-L89) ):

```solidity
26:         assembly {
27:         // We'll write our calldata to this slot below, but restore it later.
28:             let memPointer := mload(0x40)
29: 
30:         // Write the abi-encoded calldata into memory, beginning with the function selector.
31:             mstore(0, 0x23b872dd00000000000000000000000000000000000000000000000000000000)
32:             mstore(4, from) // Append the "from" argument.
33:             mstore(36, to) // Append the "to" argument.
34:             mstore(68, amount) // Append the "amount" argument.
35: 
36:             success := and(
37:             // Set success to whether the call reverted, if not we check it either
38:             // returned exactly 1 (can't just be non-zero data), or had no return data.
39:                 or(and(eq(mload(0), 1), gt(returndatasize(), 31)), iszero(returndatasize())),
40:             // We use 100 because that's the total length of our calldata (4 + 32 * 3)
41:             // Counterintuitively, this call() must be positioned after the or() in the
42:             // surrounding and() because and() evaluates its arguments from right to left.
43:                 call(gas(), token, 0, 0, 100, 0, 32)
44:             )
45: 
46:             mstore(0x60, 0) // Restore the zero slot to zero.
47:             mstore(0x40, memPointer) // Restore the memPointer.
48:         }

68:         assembly {
69:         // We'll write our calldata to this slot below, but restore it later.
70:             let memPointer := mload(0x40)
71: 
72:         // Write the abi-encoded calldata into memory, beginning with the function selector.
73:             mstore(0, 0xa9059cbb00000000000000000000000000000000000000000000000000000000)
74:             mstore(4, to) // Append the "to" argument.
75:             mstore(36, amount) // Append the "amount" argument.
76: 
77:             success := and(
78:             // Set success to whether the call reverted, if not we check it either
79:             // returned exactly 1 (can't just be non-zero data), or had no return data.
80:                 or(and(eq(mload(0), 1), gt(returndatasize(), 31)), iszero(returndatasize())),
81:             // We use 68 because that's the total length of our calldata (4 + 32 * 2)
82:             // Counterintuitively, this call() must be positioned after the or() in the
83:             // surrounding and() because and() evaluates its arguments from right to left.
84:                 call(gas(), token, 0, 0, 68, 0, 32)
85:             )
86: 
87:             mstore(0x60, 0) // Restore the zero slot to zero.
88:             mstore(0x40, memPointer) // Restore the memPointer.
89:         }
```

</details>

### [N-43] Multiple mappings with same keys can be combined into a single struct mapping for readability

Well-organized data structures make code reviews easier, which may lead to fewer bugs. Consider combining related mappings into mappings to structs, so it's clear what data is related.

<details>
<summary>There are 22 instances (click to show):</summary>

- *VoteWeighting.sol* ( [172-172](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L172-L172), [174-174](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L174-L174), [177-177](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L177-L177), [179-179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L179-L179), [181-181](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L181-L181), [190-190](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L190-L190), [192-192](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L192-L192), [194-194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L194-L194), [197-197](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L197-L197), [199-199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L199-L199) ):

```solidity
172:     mapping(bytes32 => uint256) public mapNomineeIds;

174:     mapping(bytes32 => uint256) public mapRemovedNominees;

177:     mapping(address => mapping(bytes32 => VotedSlope)) public voteUserSlopes;

179:     mapping(address => uint256) public voteUserPower;

181:     mapping(address => mapping(bytes32 => uint256)) public lastUserVote;

190:     mapping(bytes32 => mapping(uint256 => Point)) public pointsWeight;

192:     mapping(bytes32 => mapping(uint256 => uint256)) public changesWeight;

194:     mapping(bytes32 => uint256) public timeWeight;

197:     mapping(uint256 => Point) public pointsSum;

199:     mapping(uint256 => uint256) public changesSum;
```

- *Dispenser.sol* ( [302-302](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L302-L302), [304-304](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L304-L304), [306-306](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L306-L306), [308-308](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L308-L308) ):

```solidity
302:     mapping(bytes32 => uint256) public mapLastClaimedStakingEpochs;

304:     mapping(bytes32 => uint256) public mapRemovedNomineeEpochs;

306:     mapping(uint256 => address) public mapChainIdDepositProcessors;

308:     mapping(uint256 => uint256) public mapChainIdWithheldAmounts;
```

- *Tokenomics.sol* ( [359-359](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L359-L359), [361-361](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L361-L361), [363-363](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L363-L363), [365-365](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L365-L365), [367-367](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L367-L367), [369-369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L369-L369), [371-371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L371-L371), [374-374](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L374-L374) ):

```solidity
359:     mapping(uint256 => uint256) public mapServiceAmounts;

361:     mapping(address => uint256) public mapOwnerRewards;

363:     mapping(address => uint256) public mapOwnerTopUps;

365:     mapping(uint256 => TokenomicsPoint) public mapEpochTokenomics;

367:     mapping(uint256 => mapping(uint256 => bool)) public mapNewUnits;

369:     mapping(address => bool) public mapNewOwners;

371:     mapping(uint256 => mapping(uint256 => IncentiveBalances)) public mapUnitIncentives;

374:     mapping(uint256 => StakingPoint) public mapEpochStakingPoints;
```

</details>

### [N-44] Missing event for critical changes

Events should be emitted when critical changes are made to the contracts.

There are 2 instances:

- *Dispenser.sol* ( [732-746](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L732-L746), [750-750](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L750-L750) ):

```solidity
732:     function addNominee(bytes32 nomineeHash) external {
733:         // Check for the contract ownership
734:         if (msg.sender != voteWeighting) {
735:             revert ManagerOnly(msg.sender, voteWeighting);
736:         }
737: 
738:         // Check for the paused state
739:         Pause currentPause = paused;
740:         if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
741:             ITreasury(treasury).paused() == 2) {
742:             revert Paused();
743:         }
744: 
745:         mapLastClaimedStakingEpochs[nomineeHash] = ITokenomics(tokenomics).epochCounter();
746:     }

750:     function removeNominee(bytes32 nomineeHash) external {
```

### [N-45] Non-assembly method available

There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary. In addition, most assembly methods can be replaced by non-assembly methods, for example:
- `assembly{ g := gas() }` => `uint256 g = gasleft()`
- `assembly{ id := chainid() }` => `uint256 id = block.chainid`
- `assembly { r := mulmod(a, b, d) }` => `uint256 m = mulmod(x, y, k)`
- `assembly { size := extcodesize() }` => `uint256 size = address(a).code.length`
- etc.

There is 1 instance:

- *StakingFactory.sol* ( [208-208](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L208-L208) ):

```solidity
208:             instance := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt)
```

### [N-46] Consider adding emergency-stop functionality

Adding a way to quickly halt protocol functionality in an emergency, rather than having to pause individual contracts one-by-one, will make in-progress hack mitigation faster and much less stressful.

<details>
<summary>There are 20 instances (click to show):</summary>

- *VoteWeighting.sol* ( [132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L132) ):

```solidity
132: contract VoteWeighting {
```

- *StakingActivityChecker.sol* ( [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol#L18) ):

```solidity
18: contract StakingActivityChecker {
```

- *StakingFactory.sol* ( [81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L81) ):

```solidity
81: contract StakingFactory {
```

- *StakingNativeToken.sol* ( [17-17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingNativeToken.sol#L17-L17) ):

```solidity
17: contract StakingNativeToken is StakingBase {
```

- *StakingProxy.sol* ( [21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingProxy.sol#L21) ):

```solidity
21: contract StakingProxy {
```

- *StakingToken.sol* ( [44-44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L44-L44) ):

```solidity
44: contract StakingToken is StakingBase {
```

- *StakingVerifier.sol* ( [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L43) ):

```solidity
43: contract StakingVerifier {
```

- *Dispenser.sol* ( [253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L253) ):

```solidity
253: contract Dispenser {
```

- *Tokenomics.sol* ( [254-254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L254-L254) ):

```solidity
254: contract Tokenomics is TokenomicsConstants {
```

- *ArbitrumDepositProcessorL1.sol* ( [70-70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L70-L70) ):

```solidity
70: contract ArbitrumDepositProcessorL1 is DefaultDepositProcessorL1 {
```

- *ArbitrumTargetDispenserL2.sol* ( [23-23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L23-L23) ):

```solidity
23: contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 {
```

- *EthereumDepositProcessor.sol* ( [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L50) ):

```solidity
50: contract EthereumDepositProcessor {
```

- *GnosisDepositProcessorL1.sol* ( [32-32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L32-L32) ):

```solidity
32: contract GnosisDepositProcessorL1 is DefaultDepositProcessorL1 {
```

- *GnosisTargetDispenserL2.sol* ( [26-26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L26-L26) ):

```solidity
26: contract GnosisTargetDispenserL2 is DefaultTargetDispenserL2 {
```

- *OptimismDepositProcessorL1.sol* ( [59-59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L59-L59) ):

```solidity
59: contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 {
```

- *OptimismTargetDispenserL2.sol* ( [37-37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L37-L37) ):

```solidity
37: contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 {
```

- *PolygonDepositProcessorL1.sol* ( [22-22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L22-L22) ):

```solidity
22: contract PolygonDepositProcessorL1 is DefaultDepositProcessorL1, FxBaseRootTunnel {
```

- *PolygonTargetDispenserL2.sol* ( [11-11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L11-L11) ):

```solidity
11: contract PolygonTargetDispenserL2 is DefaultTargetDispenserL2, FxBaseChildTunnel {
```

- *WormholeDepositProcessorL1.sol* ( [11-11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L11-L11) ):

```solidity
11: contract WormholeDepositProcessorL1 is DefaultDepositProcessorL1, TokenSender {
```

- *WormholeTargetDispenserL2.sol* ( [50-50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L50-L50) ):

```solidity
50: contract WormholeTargetDispenserL2 is DefaultTargetDispenserL2, TokenReceiver {
```

</details>

### [N-47] Avoid the use of sensitive terms

Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist.

<details>
<summary>There are 61 instances (click to show):</summary>

- *VoteWeighting.sol* ( [128](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L128), [147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L147), [154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L154) ):

```solidity
128: /// @notice Inspired by https://github.com/curvefi/curve-dao-contracts/blob/master/contracts/GaugeController.vy

147:     // For explanation about the delay consult the official audit report: https://github.com/trailofbits/publications/blob/master/reviews/CurveDAO.pdf

154:     // For gas concerns regarding checkpoint calculations, see the internal audit and the official audit report: https://github.com/trailofbits/publications/blob/master/reviews/CurveDAO.pdf
```

- *StakingBase.sol* ( [111](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L111) ):

```solidity
111: /// @dev Multisig is not whitelisted.
```

- *StakingVerifier.sol* ( [46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L46), [63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L63), [66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L66), [111](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L111), [112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L112), [124](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L124), [125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L125), [127](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L127), [129](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L129), [130](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L130), [149](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L149), [156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L156), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L160), [167](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L167), [180](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L180) ):

```solidity
46:     event ImplementationsWhitelistUpdated(address[] implementations, bool[] statuses, bool setCheck);

63:     // Flag to check for the implementation address whitelisting status

66:     // Mapping implementation address => whitelisting status

111:     /// @dev Controls the necessity of checking implementation whitelisting statuses.

112:     /// @param setCheck True if the whitelisting check is needed, and false otherwise.

124:     /// @dev Controls implementations whitelisting statuses.

125:     /// @notice Implementation is considered whitelisted if the global status is set to true.

127:     ///         This is the owner responsibility how to manage the whitelisting logic.

129:     /// @param statuses Set of whitelisting statuses.

130:     /// @param setCheck True if the whitelisting check is needed, and false otherwise.

149:         // Set implementations whitelisting status

156:             // Set the operator whitelisting status

160:         emit ImplementationsWhitelistUpdated(implementations, statuses, setCheck);

167:         // Check the operator whitelisting status, if the whitelisting check is set

180:         // If the implementations check is true, and the implementation is not whitelisted, the verification is failed
```

- *Tokenomics.sol* ( [6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L6), [108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L108), [110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L110), [276](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L276), [352](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L352), [353](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L353), [392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L392), [404](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L404), [419](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L419), [459](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L459), [613](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L613), [614](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L614), [615](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L615), [616](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L616), [622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L622), [623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L623), [1001](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1001), [1002](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1002), [1003](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1003), [1004](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1004) ):

```solidity
6: import {IDonatorBlacklist} from "./interfaces/IDonatorBlacklist.sol";

108: /// @dev The donator address is blacklisted.

110: error DonatorBlacklisted(address account);

276:     event DonatorBlacklistUpdated(address indexed blacklist);

352:     // Blacklist contract address

353:     address public donatorBlacklist;

392:     /// @param _donatorBlacklist DonatorBlacklist address.

404:     /// #if_succeeds {:msg "donatorBlacklist assignment"} donatorBlacklist == _donatorBlacklist;

419:         address _donatorBlacklist

459:         donatorBlacklist = _donatorBlacklist;

613:     /// @dev Changes donator blacklist contract address.

614:     /// @notice DonatorBlacklist contract can be disabled by setting its address to zero.

615:     /// @param _donatorBlacklist DonatorBlacklist contract address.

616:     function changeDonatorBlacklist(address _donatorBlacklist) external {

622:         donatorBlacklist = _donatorBlacklist;

623:         emit DonatorBlacklistUpdated(_donatorBlacklist);

1001:         // Check if the donator blacklist is enabled, and the status of the donator address

1002:         address bList = donatorBlacklist;

1003:         if (bList != address(0) && IDonatorBlacklist(bList).isDonatorBlacklisted(donator)) {

1004:             revert DonatorBlacklisted(donator);
```

- *IDonatorBlacklist.sol* ( [4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/interfaces/IDonatorBlacklist.sol#L4), [5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/interfaces/IDonatorBlacklist.sol#L5), [6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/interfaces/IDonatorBlacklist.sol#L6), [8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/interfaces/IDonatorBlacklist.sol#L8), [9](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/interfaces/IDonatorBlacklist.sol#L9) ):

```solidity
4: /// @dev DonatorBlacklist interface.

5: interface IDonatorBlacklist {

6:     /// @dev Gets account blacklisting status.

8:     /// @return status Blacklisting status.

9:     function isDonatorBlacklisted(address account) external view returns (bool status);
```

- *IErrorsTokenomics.sol* ( [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/interfaces/IErrorsTokenomics.sol#L43), [114](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/interfaces/IErrorsTokenomics.sol#L114), [116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/interfaces/IErrorsTokenomics.sol#L116) ):

```solidity
43:     /// @dev Token is disabled or not whitelisted.

114:     /// @dev The donator address is blacklisted.

116:     error DonatorBlacklisted(address account);
```

- *GnosisTargetDispenserL2.sol* ( [96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L96) ):

```solidity
96:     // Source: https://github.com/omni/omnibridge/blob/master/contracts/interfaces/IERC20Receiver.sol
```

- *PolygonDepositProcessorL1.sol* ( [8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L8) ):

```solidity
8:     // Source: https://github.com/maticnetwork/pos-portal/blob/master/flat/RootChainManager.sol#L2173
```

</details>

### [N-48] Missing checks for uint state variable assignments

Consider whether reasonable bounds checks for variables would be useful.

There are 5 instances:

- *StakingVerifier.sol* ( [244-244](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L244-L244), [245-245](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L245-L245), [246-246](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L246-L246) ):

```solidity
244:         rewardsPerSecondLimit = _rewardsPerSecondLimit;

245:         timeForEmissionsLimit = _timeForEmissionsLimit;

246:         numServicesLimit = _numServicesLimit;
```

- *Dispenser.sol* ( [694-694](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L694-L694), [695-695](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L695-L695) ):

```solidity
694:         maxNumClaimingEpochs = _maxNumClaimingEpochs;

695:         maxNumStakingTargets = _maxNumStakingTargets;
```

### [N-49] Use the Modern Upgradeable Contract Paradigm

Modern smart contract development often employs upgradeable contract structures, utilizing proxy patterns like OpenZeppelin’s Upgradeable Contracts. This paradigm separates logic and state, allowing developers to amend and enhance the contract's functionality without altering its state or the deployed contract address. Transitioning to this approach enhances long-term maintainability.
Resolution: Adopt a well-established proxy pattern for upgradeability, ensuring proper initialization and employing transparent proxies to mitigate potential risks. Embrace comprehensive testing and audit practices, particularly when updating contract logic, to ensure state consistency and security are preserved across upgrades. This ensures your contract remains robust and adaptable to future requirements.

<details>
<summary>There are 18 instances (click to show):</summary>

- *VoteWeighting.sol* ( [132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L132) ):

```solidity
132: contract VoteWeighting {
```

- *StakingActivityChecker.sol* ( [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol#L18) ):

```solidity
18: contract StakingActivityChecker {
```

- *StakingNativeToken.sol* ( [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingNativeToken.sol#L17) ):

```solidity
17: contract StakingNativeToken is StakingBase {
```

- *StakingToken.sol* ( [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L44) ):

```solidity
44: contract StakingToken is StakingBase {
```

- *StakingVerifier.sol* ( [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L43) ):

```solidity
43: contract StakingVerifier {
```

- *Dispenser.sol* ( [253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L253) ):

```solidity
253: contract Dispenser {
```

- *Tokenomics.sol* ( [254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L254) ):

```solidity
254: contract Tokenomics is TokenomicsConstants {
```

- *ArbitrumDepositProcessorL1.sol* ( [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L70) ):

```solidity
70: contract ArbitrumDepositProcessorL1 is DefaultDepositProcessorL1 {
```

- *ArbitrumTargetDispenserL2.sol* ( [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L23) ):

```solidity
23: contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 {
```

- *EthereumDepositProcessor.sol* ( [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L50) ):

```solidity
50: contract EthereumDepositProcessor {
```

- *GnosisDepositProcessorL1.sol* ( [32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L32) ):

```solidity
32: contract GnosisDepositProcessorL1 is DefaultDepositProcessorL1 {
```

- *GnosisTargetDispenserL2.sol* ( [26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L26) ):

```solidity
26: contract GnosisTargetDispenserL2 is DefaultTargetDispenserL2 {
```

- *OptimismDepositProcessorL1.sol* ( [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L59) ):

```solidity
59: contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 {
```

- *OptimismTargetDispenserL2.sol* ( [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L37) ):

```solidity
37: contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 {
```

- *PolygonDepositProcessorL1.sol* ( [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L22) ):

```solidity
22: contract PolygonDepositProcessorL1 is DefaultDepositProcessorL1, FxBaseRootTunnel {
```

- *PolygonTargetDispenserL2.sol* ( [11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L11) ):

```solidity
11: contract PolygonTargetDispenserL2 is DefaultTargetDispenserL2, FxBaseChildTunnel {
```

- *WormholeDepositProcessorL1.sol* ( [11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L11) ):

```solidity
11: contract WormholeDepositProcessorL1 is DefaultDepositProcessorL1, TokenSender {
```

- *WormholeTargetDispenserL2.sol* ( [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeTargetDispenserL2.sol#L50) ):

```solidity
50: contract WormholeTargetDispenserL2 is DefaultTargetDispenserL2, TokenReceiver {
```

</details>

### [N-50] Large or complicated code bases should implement invariant tests

This includes: large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts.
Invariant fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold.
Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and invariant fuzzers may help significantly.

There are 2 instances:

- Global finding

- Global finding

### [N-51] The default value is manually set when it is declared

In instances where a new variable is defined, there is no need to set it to it's default value.

<details>
<summary>There are 39 instances (click to show):</summary>

- *VoteWeighting.sol* ( [227-227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L227-L227), [267-267](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L267-L267), [573-573](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L573-L573), [793-793](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L793-L793) ):

```solidity
227:         for (uint256 i = 0; i < MAX_NUM_WEEKS; i++) {

267:         for (uint256 i = 0; i < MAX_NUM_WEEKS; i++) {

573:         for (uint256 i = 0; i < accounts.length; ++i) {

793:         for (uint256 i = 0; i < accounts.length; ++i) {
```

- *StakingBase.sol* ( [332-332](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L332-L332), [380-380](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L380-L380), [449-449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L449-L449), [548-548](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L548-L548), [648-648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L648-L648), [669-669](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L669-L669), [773-773](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L773-L773), [899-899](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingBase.sol#L899-L899) ):

```solidity
332:         for (uint256 i = 0; i < _stakingParams.agentIds.length; ++i) {

380:         for (uint256 i = 0; i < numAgentIds; ++i) {

449:         for (uint256 i = 0; i < totalNumServices; ++i) {

548:             for (uint256 i = 0; i < size; ++i) {

648:                 for (uint256 i = 0; i < numServices; ++i) {

669:             for (uint256 i = 0; i < serviceIds.length; ++i) {

773:             for (uint256 i = 0; i < numAgents; ++i) {

899:         for (uint256 i = 0; i < numServices; ++i) {
```

- *StakingToken.sol* ( [92-92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L92-L92) ):

```solidity
92:         for (uint256 i = 0; i < serviceAgentIds.length; ++i) {
```

- *StakingVerifier.sol* ( [150-150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L150-L150) ):

```solidity
150:         for (uint256 i = 0; i < implementations.length; ++i) {
```

- *Dispenser.sol* ( [450-450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L450-L450), [462-462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L462-L462), [473-473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L473-L473), [485-485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L485-L485), [526-526](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L526-L526), [550-550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L550-L550), [593-593](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L593-L593), [600-600](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L600-L600), [717-717](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L717-L717) ):

```solidity
450:         for (uint256 i = 0; i < chainIds.length; ++i) {

462:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {

473:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {

485:                 for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {

526:         for (uint256 i = 0; i < chainIds.length; ++i) {

550:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {

593:         for (uint256 i = 0; i < chainIds.length; ++i) {

600:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {

717:         for (uint256 i = 0; i < chainIds.length; ++i) {
```

- *Tokenomics.sol* ( [904-904](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L904-L904), [916-916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L916-L916), [930-930](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L930-L930), [960-960](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L960-L960), [1010-1010](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1010-L1010), [1199-1199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1199-L1199), [1329-1329](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1329-L1329), [1335-1335](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1335-L1335), [1357-1357](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1357-L1357), [1401-1401](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1401-L1401), [1407-1407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1407-L1407), [1429-1429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L1429-L1429) ):

```solidity
904:         for (uint256 i = 0; i < numServices; ++i) {

916:             for (uint256 unitType = 0; unitType < 2; ++unitType) {

930:                     for (uint256 j = 0; j < numServiceUnits; ++j) {

960:                 for (uint256 j = 0; j < numServiceUnits; ++j) {

1010:         for (uint256 i = 0; i < numServices; ++i) {

1199:             for (uint256 i = 0; i < 2; ++i) {

1329:         for (uint256 i = 0; i < 2; ++i) {

1335:         for (uint256 i = 0; i < unitIds.length; ++i) {

1357:         for (uint256 i = 0; i < unitIds.length; ++i) {

1401:         for (uint256 i = 0; i < 2; ++i) {

1407:         for (uint256 i = 0; i < unitIds.length; ++i) {

1429:         for (uint256 i = 0; i < unitIds.length; ++i) {
```

- *TokenomicsConstants.sol* ( [58-58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/TokenomicsConstants.sol#L58-L58) ):

```solidity
58:             for (uint256 i = 0; i < numYears; ++i) {
```

- *DefaultTargetDispenserL2.sol* ( [150-150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L150-L150), [154-154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/DefaultTargetDispenserL2.sol#L154-L154) ):

```solidity
150:         uint256 localWithheldAmount = 0;

154:         for (uint256 i = 0; i < targets.length; ++i) {
```

- *EthereumDepositProcessor.sol* ( [94-94](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L94-L94) ):

```solidity
94:         for (uint256 i = 0; i < targets.length; ++i) {
```

</details>

### [N-52] Contracts should have all `public`/`external` functions exposed by `interface`s

All `external`/`public` functions should extend an `interface`. This is useful to ensure that the whole API is extracted and can be more easily integrated by other projects.

<details>
<summary>There are 19 instances (click to show):</summary>

- *VoteWeighting.sol* ( [132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/../governance/contracts/VoteWeighting.sol#L132) ):

```solidity
132: contract VoteWeighting {
```

- *StakingActivityChecker.sol* ( [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingActivityChecker.sol#L18) ):

```solidity
18: contract StakingActivityChecker {
```

- *StakingFactory.sol* ( [81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingFactory.sol#L81) ):

```solidity
81: contract StakingFactory {
```

- *StakingNativeToken.sol* ( [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingNativeToken.sol#L17) ):

```solidity
17: contract StakingNativeToken is StakingBase {
```

- *StakingProxy.sol* ( [21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingProxy.sol#L21) ):

```solidity
21: contract StakingProxy {
```

- *StakingToken.sol* ( [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingToken.sol#L44) ):

```solidity
44: contract StakingToken is StakingBase {
```

- *StakingVerifier.sol* ( [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/./contracts/staking/StakingVerifier.sol#L43) ):

```solidity
43: contract StakingVerifier {
```

- *Dispenser.sol* ( [253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Dispenser.sol#L253) ):

```solidity
253: contract Dispenser {
```

- *Tokenomics.sol* ( [254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/Tokenomics.sol#L254) ):

```solidity
254: contract Tokenomics is TokenomicsConstants {
```

- *ArbitrumDepositProcessorL1.sol* ( [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumDepositProcessorL1.sol#L70) ):

```solidity
70: contract ArbitrumDepositProcessorL1 is DefaultDepositProcessorL1 {
```

- *ArbitrumTargetDispenserL2.sol* ( [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/ArbitrumTargetDispenserL2.sol#L23) ):

```solidity
23: contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 {
```

- *EthereumDepositProcessor.sol* ( [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/EthereumDepositProcessor.sol#L50) ):

```solidity
50: contract EthereumDepositProcessor {
```

- *GnosisDepositProcessorL1.sol* ( [32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisDepositProcessorL1.sol#L32) ):

```solidity
32: contract GnosisDepositProcessorL1 is DefaultDepositProcessorL1 {
```

- *GnosisTargetDispenserL2.sol* ( [26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/GnosisTargetDispenserL2.sol#L26) ):

```solidity
26: contract GnosisTargetDispenserL2 is DefaultTargetDispenserL2 {
```

- *OptimismDepositProcessorL1.sol* ( [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismDepositProcessorL1.sol#L59) ):

```solidity
59: contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 {
```

- *OptimismTargetDispenserL2.sol* ( [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/OptimismTargetDispenserL2.sol#L37) ):

```solidity
37: contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 {
```

- *PolygonDepositProcessorL1.sol* ( [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonDepositProcessorL1.sol#L22) ):

```solidity
22: contract PolygonDepositProcessorL1 is DefaultDepositProcessorL1, FxBaseRootTunnel {
```

- *PolygonTargetDispenserL2.sol* ( [11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/PolygonTargetDispenserL2.sol#L11) ):

```solidity
11: contract PolygonTargetDispenserL2 is DefaultTargetDispenserL2, FxBaseChildTunnel {
```

- *WormholeDepositProcessorL1.sol* ( [11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/./contracts/staking/WormholeDepositProcessorL1.sol#L11) ):

```solidity
11: contract WormholeDepositProcessorL1 is DefaultDepositProcessorL1, TokenSender {
```

</details>
