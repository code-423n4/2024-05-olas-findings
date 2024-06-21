## Medium Issues


*Total <b>18</b> instances over <b>4</b> issues:*

|ID|Issue|Instances|
|-|:-|:-:|
| [M-1](#M-1) | No way to retrieve ETH from the contract | 7 |
| [M-2](#M-2) | Return values of `approve()` not checked | 7 |
| [M-3](#M-3) | Return values of `transfer()`/`transferFrom()` not checked | 3 |
| [M-4](#M-4) | `transfer`/`transferFrom` may never return `true` for some tokens | 1 |

## Low Issues


*Total <b>494</b> instances over <b>32</b> issues:*

|ID|Issue|Instances|
|-|:-|:-:|
| [L-1](#L-1) | Missing zero address check in functions with address parameters | 54 |
| [L-2](#L-2) | Use increase/decrease allowance instead of `approve()`/`safeApprove()` | 7 |
| [L-3](#L-3) | Array is `push()`ed but not `pop()`ed | 6 |
| [L-4](#L-4) | Division by zero not prevented | 5 |
| [L-5](#L-5) | Consider implementing two-step procedure for updating protocol addresses | 13 |
| [L-6](#L-6) | Constructor / initialization function lacks parameter validation | 12 |
| [L-7](#L-7) | Code does not follow the best practice of check-effects-interaction | 47 |
| [L-8](#L-8) | External function calls within loops | 28 |
| [L-9](#L-9) | Functions calling contracts/addresses with transfer hooks are missing reentrancy guards | 8 |
| [L-10](#L-10) | Large approvals may not work with some `ERC20` tokens | 7 |
| [L-11](#L-11) | Loss of precision in divisions | 13 |
| [L-12](#L-12) | Missing checks for state variable assignments | 74 |
| [L-13](#L-13) | Missing limits when setting min/max amounts | 2 |
| [L-14](#L-14) | Missing zero address check in constructor | 12 |
| [L-15](#L-15) | Consider some checks for `address(0)` when setting address state variables | 55 |
| [L-16](#L-16) | Numbers downcast to `address`es may result in collisions | 6 |
| [L-17](#L-17) | `receive()`/`fallback()` function does not authorize requests | 3 |
| [L-18](#L-18) | Solidity version 0.8.20 or above may not work on other chains due to PUSH0 | 28 |
| [L-19](#L-19) | Some functions do not work correctly with fee-on-transfer tokens | 4 |
| [L-20](#L-20) | Some tokens may revert when large transfers are made | 4 |
| [L-21](#L-21) | Some tokens may revert when zero value transfers are made | 4 |
| [L-22](#L-22) | Unsafe downcast | 65 |
| [L-23](#L-23) | Unsafe addition/multiplication in `unchecked` block | 1 |
| [L-24](#L-24) | Consider using `ERC721A` instead of `ERC721` | 1 |
| [L-25](#L-25) | Using zero as a parameter | 9 |
| [L-26](#L-26) | Missing zero address check in initializer | 1 |
| [L-27](#L-27) | `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()` | 2 |
| [L-28](#L-28) | Initializers could be front-run | 2 |
| [L-29](#L-29) | For loops in public or external functions should be avoided due to high gas costs and possible DOS | 6 |
| [L-30](#L-30) | Function with two (or more) array parameters missing a length check | 3 |
| [L-31](#L-31) | The call `abi.encodeWithSelector` is not type safe | 6 |
| [L-32](#L-32) | int/uint passed into abi.encodePacked without casting to a string | 6 |
=

## Medium Issues

<a name="M-1"></a> 
### [M-1] No way to retrieve ETH from the contract
The following contracts contain at least one `payable` function, yet the function does not utilise forwarded ETH, and the contract is missing functionality to withdraw ETH from the contract. This means that funds may become trapped in the contract indefinitely. Consider adding a withdraw/sweep function to contracts that are capable of receiving ether.

*There are <b>7</b> instances of this issue:*
```solidity
File: registries/contracts/staking/StakingProxy.sol

/// @audit Contract can receive Ether but there is lack of functionality to claim the Ethers from the contract
21: contract StakingProxy {

```
*Github:* [[21](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingProxy.sol#L21)]


```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

/// @audit Contract can receive Ether but there is lack of functionality to claim the Ethers from the contract
70: contract ArbitrumDepositProcessorL1 is DefaultDepositProcessorL1 {

```
*Github:* [[70](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L70)]


```solidity
File: tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

/// @audit Contract can receive Ether but there is lack of functionality to claim the Ethers from the contract
23: contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 {

```
*Github:* [[23](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L23)]


```solidity
File: tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

/// @audit Contract can receive Ether but there is lack of functionality to claim the Ethers from the contract
22: abstract contract DefaultDepositProcessorL1 is IBridgeErrors {

```
*Github:* [[22](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L22)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

/// @audit Contract can receive Ether but there is lack of functionality to claim the Ethers from the contract
59: contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 {

```
*Github:* [[59](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L59)]


```solidity
File: tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

/// @audit Contract can receive Ether but there is lack of functionality to claim the Ethers from the contract
37: contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 {

```
*Github:* [[37](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L37)]


```solidity
File: tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

/// @audit Contract can receive Ether but there is lack of functionality to claim the Ethers from the contract
50: contract WormholeTargetDispenserL2 is DefaultTargetDispenserL2, TokenReceiver {

```
*Github:* [[50](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L50)]



<a name="M-2"></a> 
### [M-2] Return values of `approve()` not checked
Not all IERC20 implementations `revert()` when there's a failure in `approve()`. The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually approving anything

*There are <b>7</b> instances of this issue:*
```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

174:             IToken(olas).approve(l1ERC20Gateway, transferAmount);

```
*Github:* [[174](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L174)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

191:                 IToken(olas).approve(target, amount);

290:             IToken(olas).approve(target, amount);

```
*Github:* [[191](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L191), [290](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L290)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

118:             IToken(olas).approve(target, amount);

```
*Github:* [[118](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L118)]


```solidity
File: tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

65:             IToken(olas).approve(l1TokenRelayer, transferAmount);

```
*Github:* [[65](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L65)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

111:             IToken(olas).approve(l1TokenRelayer, transferAmount);

```
*Github:* [[111](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L111)]


```solidity
File: tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

68:             IToken(olas).approve(predicate, transferAmount);

```
*Github:* [[68](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L68)]



<a name="M-3"></a> 
### [M-3] Return values of `transfer()`/`transferFrom()` not checked
Not all ERC20 implementations `revert()` when there's a failure in `transfer()` or `transferFrom()`. The function signature has a boolean return value and they indicate errors that way instead. By not checking the return value, operations that should have marked as failed, may potentially go through without actually transfer anything.

*There are <b>3</b> instances of this issue:*
```solidity
File: tokenomics/contracts/Dispenser.sol

420:             IToken(olas).transfer(depositProcessor, transferAmount);

456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]);

```
*Github:* [[420](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L420), [456](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L456)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

112:                 IToken(olas).transfer(timelock, refundAmount);

```
*Github:* [[112](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L112)]



<a name="M-4"></a> 
### [M-4] `transfer`/`transferFrom` may never return `true` for some tokens
Some tokens do not return a bool (e.g. USDT, OMG) on transfer. As there are some conditions that rely on this `bool` value, these tokens may cause issues, as the return value of the following conditions will always be `false`.

*There is one instance of this issue:*
```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

443:             bool success = IToken(olas).transfer(newL2TargetDispenser, amount);

```
*Github:* [[443](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L443)]




## Low Issues

<a name="L-1"></a> 
### [L-1] Missing zero address check in functions with address parameters
Adding a zero address check for each address type parameter can prevent errors.

<details>
<summary>
There are <b>54</b> instances (click to show):
</summary>

```solidity
File: governance/contracts/VoteWeighting.sol

/// @audit missing zero check for `account`
36:     function lockedEnd(address account) external view returns (uint256 unlockTime);

/// @audit missing zero check for `account`
41:     function getLastUserPoint(address account) external view returns (PointVoting memory pv);

```
*Github:* [[36](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L36), [41](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L41)]


```solidity
File: registries/contracts/staking/StakingBase.sol

/// @audit missing zero check for `multisig`
11:     function getMultisigNonces(address multisig) external view returns (uint256[] memory nonces);

/// @audit missing zero check for `from`
/// @audit missing zero check for `to`
72:     function safeTransferFrom(address from, address to, uint256 id) external;

/// @audit missing zero check for `to`
390:     function _withdraw(address to, uint256 amount) internal virtual;

/// @audit missing zero check for `multisig`
398:     function _checkRatioPass(
             address multisig,
             uint256[] memory lastNonces,
             uint256 ts
         ) internal view returns (bool ratioPass, uint256[] memory currentNonces)
         {

```
*Github:* [[11](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L11), [72](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L72), [390](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L390), [398](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L398)]


```solidity
File: registries/contracts/staking/StakingFactory.sol

/// @audit missing zero check for `implementation`
18:     function verifyImplementation(address implementation) external view returns (bool);

/// @audit missing zero check for `instance`
/// @audit missing zero check for `implementation`
24:     function verifyInstance(address instance, address implementation) external view returns (bool);

/// @audit missing zero check for ``
28:     function getEmissionsAmountLimit(address) external view returns (uint256);

/// @audit missing zero check for `implementation`
161:     function getProxyAddress(address implementation) external view returns (address) {

/// @audit missing zero check for `instance`
249:     function setInstanceStatus(address instance, bool isEnabled) external {

/// @audit missing zero check for `instance`
267:     function verifyInstance(address instance) public view returns (bool) {

```
*Github:* [[18](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L18), [24](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L24), [28](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L28), [161](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L161), [249](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L249), [267](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L267)]


```solidity
File: registries/contracts/staking/StakingNativeToken.sol

/// @audit missing zero check for `to`
28:     function _withdraw(address to, uint256 amount) internal override {

```
*Github:* [[28](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingNativeToken.sol#L28)]


```solidity
File: registries/contracts/staking/StakingToken.sol

/// @audit missing zero check for `to`
104:     function _withdraw(address to, uint256 amount) internal override {

```
*Github:* [[104](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingToken.sol#L104)]


```solidity
File: registries/contracts/staking/StakingVerifier.sol

/// @audit missing zero check for `implementation`
166:     function verifyImplementation(address implementation) external view returns (bool){

/// @audit missing zero check for `implementation`
179:     function verifyInstance(address instance, address implementation) external view returns (bool) {

/// @audit missing zero check for ``
255:     function getEmissionsAmountLimit(address) external view returns (uint256) {

```
*Github:* [[166](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L166), [179](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L179), [255](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L255)]


```solidity
File: registries/contracts/utils/SafeTransferLib.sol

/// @audit missing zero check for `token`
/// @audit missing zero check for `from`
/// @audit missing zero check for `to`
22:     function safeTransferFrom(address token, address from, address to, uint256 amount) internal {

/// @audit missing zero check for `token`
/// @audit missing zero check for `to`
64:     function safeTransfer(address token, address to, uint256 amount) internal {

```
*Github:* [[22](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/utils/SafeTransferLib.sol#L22), [64](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/utils/SafeTransferLib.sol#L64)]


```solidity
File: tokenomics/contracts/Dispenser.sol

/// @audit missing zero check for `target`
11:     function sendMessage(address target, uint256 stakingIncentive, bytes memory bridgePayload,

/// @audit missing zero check for `account`
48:     function balanceOf(address account) external view returns (uint256);

/// @audit missing zero check for `to`
54:     function transfer(address to, uint256 amount) external returns (bool);

/// @audit missing zero check for `account`
115:     function accountOwnerIncentives(address account, uint256[] memory unitTypes, uint256[] memory unitIds) external

/// @audit missing zero check for `account`
151:     function withdrawToAccount(address account, uint256 accountRewards, uint256 accountTopUps) external

```
*Github:* [[11](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L11), [48](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L48), [54](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L54), [115](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L115), [151](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L151)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

/// @audit missing zero check for `account`
61:     function getVotes(address account) external view returns (uint256);

/// @audit missing zero check for `_donatorBlacklist`
409:     function initializeTokenomics(
             address _olas,
             address _treasury,
             address _depository,
             address _dispenser,
             address _ve,
             uint256 _epochLen,
             address _componentRegistry,
             address _agentRegistry,
             address _serviceRegistry,
             address _donatorBlacklist
         ) external {

/// @audit missing zero check for `account`
1308:     function accountOwnerIncentives(
              address account,
              uint256[] memory unitTypes,
              uint256[] memory unitIds
          ) external returns (uint256 reward, uint256 topUp) {

/// @audit missing zero check for `account`
1385:     function getOwnerIncentives(
              address account,
              uint256[] memory unitTypes,
              uint256[] memory unitIds
          ) external view returns (uint256 reward, uint256 topUp) {

```
*Github:* [[61](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L61), [409](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L409), [1308](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1308), [1385](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1385)]


```solidity
File: tokenomics/contracts/interfaces/IDonatorBlacklist.sol

/// @audit missing zero check for `account`
9:     function isDonatorBlacklisted(address account) external view returns (bool status);

```
*Github:* [[9](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L9)]


```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

/// @audit missing zero check for `_l1Token`
/// @audit missing zero check for `_refundTo`
/// @audit missing zero check for `_to`
26:     function outboundTransferCustomRefund(

/// @audit missing zero check for `to`
/// @audit missing zero check for `excessFeeRefundAddress`
/// @audit missing zero check for `callValueRefundAddress`
48:     function createRetryableTicket(

```
*Github:* [[26](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L26), [48](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L48)]


```solidity
File: tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

/// @audit missing zero check for `destination`
16:     function sendTxToL1(address destination, bytes calldata data) external payable returns (uint256);

```
*Github:* [[16](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L16)]


```solidity
File: tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

/// @audit missing zero check for `spender`
15:     function approve(address spender, uint256 amount) external returns (bool);

/// @audit missing zero check for `l1Relayer`
/// @audit missing zero check for `l2Dispenser`
107:     function _receiveMessage(address l1Relayer, address l2Dispenser, bytes memory data) internal virtual {

/// @audit missing zero check for `target`
132:     function sendMessage(
             address target,
             uint256 stakingIncentive,
             bytes memory bridgePayload,
             uint256 transferAmount
         ) external virtual payable {

```
*Github:* [[15](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L15), [107](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L107), [132](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L132)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

/// @audit missing zero check for `instance`
18:     function verifyInstanceAndGetEmissionsAmount(address instance) external view returns (uint256 amount);

/// @audit missing zero check for `account`
26:     function balanceOf(address account) external view returns (uint256);

/// @audit missing zero check for `spender`
32:     function approve(address spender, uint256 amount) external returns (bool);

/// @audit missing zero check for `to`
38:     function transfer(address to, uint256 amount) external returns (bool);

/// @audit missing zero check for `messageRelayer`
/// @audit missing zero check for `sourceProcessor`
224:     function _receiveMessage(
             address messageRelayer,
             address sourceProcessor,
             bytes memory data
         ) internal virtual {

```
*Github:* [[18](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L18), [26](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L26), [32](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L32), [38](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L38), [224](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L224)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

/// @audit missing zero check for `spender`
9:     function approve(address spender, uint256 amount) external returns (bool);

/// @audit missing zero check for `to`
15:     function transfer(address to, uint256 amount) external returns (bool);

/// @audit missing zero check for `instance`
28:     function verifyInstanceAndGetEmissionsAmount(address instance) external view returns (uint256 amount);

/// @audit missing zero check for `target`
130:     function sendMessage(
             address target,
             uint256 stakingIncentive,
             bytes memory,
             uint256
         ) external {

```
*Github:* [[9](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L9), [15](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L15), [28](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L28), [130](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L130)]


```solidity
File: tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

/// @audit missing zero check for `target`
15:     function requireToPassMessage(address target, bytes memory data, uint256 maxGasLimit) external returns (bytes32);

/// @audit missing zero check for `token`
/// @audit missing zero check for `receiver`
21:     function relayTokensAndCall(address token, address receiver, uint256 amount, bytes memory payload) external;

```
*Github:* [[15](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L15), [21](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L21)]


```solidity
File: tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

/// @audit missing zero check for `target`
15:     function requireToPassMessage(address target, bytes memory data, uint256 maxGasLimit) external returns (bytes32);

/// @audit missing zero check for ``
99:     function onTokenBridged(address, uint256, bytes calldata data) external {

```
*Github:* [[15](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L15), [99](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L99)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

/// @audit missing zero check for `_l1Token`
/// @audit missing zero check for `_l2Token`
/// @audit missing zero check for `_to`
20:     function depositERC20To(

/// @audit missing zero check for `_target`
39:     function sendMessage(

```
*Github:* [[20](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L20), [39](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L39)]


```solidity
File: tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

/// @audit missing zero check for `_target`
17:     function sendMessage(

```
*Github:* [[17](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L17)]


```solidity
File: tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

/// @audit missing zero check for `user`
/// @audit missing zero check for `rootToken`
15:     function depositFor(address user, address rootToken, bytes calldata depositData) external;

```
*Github:* [[15](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L15)]


```solidity
File: tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

/// @audit missing zero check for `sender`
52:     function _processMessageFromRoot(uint256, address sender, bytes memory data) internal override {

```
*Github:* [[52](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L52)]


```solidity
File: tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

/// @audit missing zero check for `targetAddress`
/// @audit missing zero check for `refundAddress`
35:     function sendPayloadToEvm(

```
*Github:* [[35](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L35)]


</details>


<a name="L-2"></a> 
### [L-2] Use increase/decrease allowance instead of `approve()`/`safeApprove()`
Changing an allowance with approve() brings the risk that someone may use both the old and the new allowance by unfortunate transaction ordering. Refer to [ERC20 API: An Attack Vector on the Approve/TransferFrom Methods](https://docs.google.com/document/d/1YLPtQxZu1UAvO9cZ1O2RPXBbT0mooh4DYKjA_jp-RLM). It is recommended to use the `increaseAllowance()`/`decreaseAllowance()` to avoid this problem.

*There are <b>7</b> instances of this issue:*
```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

174:             IToken(olas).approve(l1ERC20Gateway, transferAmount);

```
*Github:* [[174](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L174)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

191:                 IToken(olas).approve(target, amount);

290:             IToken(olas).approve(target, amount);

```
*Github:* [[191](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L191), [290](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L290)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

118:             IToken(olas).approve(target, amount);

```
*Github:* [[118](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L118)]


```solidity
File: tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

65:             IToken(olas).approve(l1TokenRelayer, transferAmount);

```
*Github:* [[65](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L65)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

111:             IToken(olas).approve(l1TokenRelayer, transferAmount);

```
*Github:* [[111](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L111)]


```solidity
File: tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

68:             IToken(olas).approve(predicate, transferAmount);

```
*Github:* [[68](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L68)]



<a name="L-3"></a> 
### [L-3] Array is `push()`ed but not `pop()`ed
There is no limit specified on the amount of gas used, so the recipient can use up all of the remaining gas (`gasleft()`), causing it to revert. Therefore, when calling an external contract, it is necessary to specify a limited amount of gas to forward.

*There are <b>6</b> instances of this issue:*
```solidity
File: governance/contracts/VoteWeighting.sol

216:         setNominees.push(Nominee(0, 0));

218:         setRemovedNominees.push(Nominee(0, 0));

307:         setNominees.push(nominee);

616:         setRemovedNominees.push(nominee);

```
*Github:* [[216](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L216), [218](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L218), [307](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L307), [616](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L616)]


```solidity
File: registries/contracts/staking/StakingBase.sol

338:             agentIds.push(agentId);

794:         setServiceIds.push(serviceId);

```
*Github:* [[338](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L338), [794](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L794)]



<a name="L-4"></a> 
### [L-4] Division by zero not prevented
The divisions below take an input parameter that has no zero-value checks, which can cause the functions reverting if zero is passed.

*There are <b>5</b> instances of this issue:*
```solidity
File: registries/contracts/staking/StakingActivityChecker.sol

/// @audit `ts`
58:             uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts;

```
*Github:* [[58](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingActivityChecker.sol#L58)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

/// @audit `ONE_YEAR`
1126:         uint256 numYears = (block.timestamp - timeLaunch) / ONE_YEAR;

/// @audit `ONE_YEAR`
1136:             curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;

/// @audit `ONE_YEAR`
1243:         numYears = (block.timestamp + curEpochLen - timeLaunch) / ONE_YEAR;

/// @audit `ONE_YEAR`
1252:             curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;

```
*Github:* [[1126](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1126), [1136](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1136), [1243](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1243), [1252](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1252)]



<a name="L-5"></a> 
### [L-5] Consider implementing two-step procedure for updating protocol addresses
A copy-paste error or a typo may end up bricking protocol functionality, or sending tokens to an address with no known private key. Consider implementing a two-step procedure for updating protocol addresses, where the recipient is set as pending, and must "accept" the assignment by making an affirmative call. A straight forward way of doing this would be to have the target contracts implement [EIP-165](https://eips.ethereum.org/EIPS/eip-165), and to have the "set" functions ensure that the recipient is of the right interface type.

<details>
<summary>
There are <b>13</b> instances (click to show):
</summary>

```solidity
File: governance/contracts/VoteWeighting.sol

/// @audit `owner`
368:     function changeOwner(address newOwner) external {

/// @audit `dispenser`
386:     function changeDispenser(address newDispenser) external {

```
*Github:* [[368](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L368), [386](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L386)]


```solidity
File: registries/contracts/staking/StakingFactory.sol

/// @audit `owner`
110:     function changeOwner(address newOwner) external {

/// @audit `verifier`
127:     function changeVerifier(address newVerifier) external {

```
*Github:* [[110](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L110), [127](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L127)]


```solidity
File: registries/contracts/staking/StakingVerifier.sol

/// @audit `owner`
96:     function changeOwner(address newOwner) external {

```
*Github:* [[96](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L96)]


```solidity
File: tokenomics/contracts/Dispenser.sol

/// @audit `owner`
636:     function changeOwner(address newOwner) external {

/// @audit `tokenomics`
/// @audit `treasury`
/// @audit `voteWeighting`
655:     function changeManagers(address _tokenomics, address _treasury, address _voteWeighting) external {

```
*Github:* [[636](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L636), [655](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L655)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

/// @audit `owner`
546:     function changeOwner(address newOwner) external {

/// @audit `treasury`
/// @audit `depository`
/// @audit `dispenser`
565:     function changeManagers(address _treasury, address _depository, address _dispenser) external {

/// @audit `componentRegistry`
/// @audit `agentRegistry`
/// @audit `serviceRegistry`
592:     function changeRegistries(address _componentRegistry, address _agentRegistry, address _serviceRegistry) external {

/// @audit `donatorBlacklist`
616:     function changeDonatorBlacklist(address _donatorBlacklist) external {

```
*Github:* [[546](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L546), [565](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L565), [592](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L592), [616](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L616)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

/// @audit `owner`
247:     function changeOwner(address newOwner) external {

/// @audit `owner`
412:     function migrate(address newL2TargetDispenser) external {

```
*Github:* [[247](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L247), [412](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L412)]


</details>


<a name="L-6"></a> 
### [L-6] Constructor / initialization function lacks parameter validation
Constructors and initialization functions play a critical role in contracts by setting important initial states when the contract is first deployed before the system starts. The parameters passed to the constructor and initialization functions directly affect the behavior of the contract / protocol. If incorrect parameters are provided, the system may fail to run, behave abnormally, be unstable, or lack security. Therefore, it's crucial to carefully check each parameter in the constructor and initialization functions. If an exception is found, the transaction should be rolled back.

<details>
<summary>
There are <b>12</b> instances (click to show):
</summary>

```solidity
File: registries/contracts/staking/StakingFactory.sol

/// @audit `_verifier` not validated
103:     constructor(address _verifier) {

```
*Github:* [[103](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L103)]


```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

/// @audit `_olas` not validated
/// @audit `_l1Dispenser` not validated
/// @audit `_l1TokenRelayer` not validated
/// @audit `_l1MessageRelayer` not validated
/// @audit `_l2TargetChainId` not validated
89:     constructor(

```
*Github:* [[89](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L89)]


```solidity
File: tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

/// @audit `_olas` not validated
/// @audit `_proxyFactory` not validated
/// @audit `_l2MessageRelayer` not validated
/// @audit `_l1SourceChainId` not validated
36:     constructor(

```
*Github:* [[36](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L36)]


```solidity
File: tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

/// @audit `_olas` not validated
59:     constructor(

```
*Github:* [[59](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L59)]


```solidity
File: tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

/// @audit `_olas` not validated
/// @audit `_l1Dispenser` not validated
/// @audit `_l1TokenRelayer` not validated
/// @audit `_l1MessageRelayer` not validated
/// @audit `_l2TargetChainId` not validated
42:     constructor(

```
*Github:* [[42](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L42)]


```solidity
File: tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

/// @audit `_olas` not validated
/// @audit `_proxyFactory` not validated
/// @audit `_l2MessageRelayer` not validated
/// @audit `_l1DepositProcessor` not validated
/// @audit `_l1SourceChainId` not validated
39:     constructor(

```
*Github:* [[39](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L39)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

/// @audit `_olas` not validated
/// @audit `_l1Dispenser` not validated
/// @audit `_l1TokenRelayer` not validated
/// @audit `_l1MessageRelayer` not validated
/// @audit `_l2TargetChainId` not validated
76:     constructor(

```
*Github:* [[76](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L76)]


```solidity
File: tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

/// @audit `_olas` not validated
/// @audit `_proxyFactory` not validated
/// @audit `_l2MessageRelayer` not validated
/// @audit `_l1DepositProcessor` not validated
/// @audit `_l1SourceChainId` not validated
47:     constructor(

```
*Github:* [[47](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L47)]


```solidity
File: tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

/// @audit `_olas` not validated
/// @audit `_l1Dispenser` not validated
/// @audit `_l1TokenRelayer` not validated
/// @audit `_l1MessageRelayer` not validated
/// @audit `_l2TargetChainId` not validated
36:     constructor(

```
*Github:* [[36](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L36)]


```solidity
File: tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

/// @audit `_olas` not validated
/// @audit `_proxyFactory` not validated
/// @audit `_l1DepositProcessor` not validated
/// @audit `_l1SourceChainId` not validated
20:     constructor(

```
*Github:* [[20](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L20)]


```solidity
File: tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

/// @audit `_olas` not validated
/// @audit `_l1Dispenser` not validated
/// @audit `_l1TokenRelayer` not validated
/// @audit `_l1MessageRelayer` not validated
/// @audit `_l2TargetChainId` not validated
28:     constructor(

```
*Github:* [[28](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L28)]


```solidity
File: tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

/// @audit `_olas` not validated
/// @audit `_proxyFactory` not validated
/// @audit `_l2MessageRelayer` not validated
/// @audit `_l1DepositProcessor` not validated
/// @audit `_l1SourceChainId` not validated
64:     constructor(

```
*Github:* [[64](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L64)]


</details>


<a name="L-7"></a> 
### [L-7] Code does not follow the best practice of check-effects-interaction
Code should follow the best-practice of [check-effects-interaction](https://blockchain-academy.hs-mittweida.de/courses/solidity-coding-beginners-to-intermediate/lessons/solidity-11-coding-patterns/topic/checks-effects-interactions/), where state variables are updated before any external calls are made. Doing so prevents a large class of reentrancy bugs.

<details>
<summary>
There are <b>47</b> instances (click to show):
</summary>

```solidity
File: governance/contracts/VoteWeighting.sol

/// @audit `.getLastUserPoint(...)` is called on line 483
/// @audit `.lockedEnd(...)` is called on line 488
524:         voteUserPower[msg.sender] = powerUsed;

/// @audit `.getLastUserPoint(...)` is called on line 483
/// @audit `.lockedEnd(...)` is called on line 488
533:         pointsSum[nextTime].bias = _maxAndSub(_getSum() + newBias, oldBias);

/// @audit `.getLastUserPoint(...)` is called on line 483
/// @audit `.lockedEnd(...)` is called on line 488
537:             pointsSum[nextTime].slope = _maxAndSub(pointsSum[nextTime].slope + newSlope.slope, oldSlope.slope);

/// @audit `.getLastUserPoint(...)` is called on line 483
/// @audit `.lockedEnd(...)` is called on line 488
540:             pointsSum[nextTime].slope += newSlope.slope;

/// @audit `.getLastUserPoint(...)` is called on line 483
/// @audit `.lockedEnd(...)` is called on line 488
544:             changesWeight[nomineeHash][oldSlope.end] -= oldSlope.slope;

/// @audit `.getLastUserPoint(...)` is called on line 483
/// @audit `.lockedEnd(...)` is called on line 488
545:             changesSum[oldSlope.end] -= oldSlope.slope;

/// @audit `.getLastUserPoint(...)` is called on line 483
/// @audit `.lockedEnd(...)` is called on line 488
548:         changesWeight[nomineeHash][newSlope.end] += newSlope.slope;

/// @audit `.getLastUserPoint(...)` is called on line 483
/// @audit `.lockedEnd(...)` is called on line 488
549:         changesSum[newSlope.end] += newSlope.slope;

/// @audit `.getLastUserPoint(...)` is called on line 483
/// @audit `.lockedEnd(...)` is called on line 488
551:         voteUserSlopes[msg.sender][nomineeHash] = newSlope;

/// @audit `.getLastUserPoint(...)` is called on line 483
/// @audit `.lockedEnd(...)` is called on line 488
554:         lastUserVote[msg.sender][nomineeHash] = block.timestamp;

```
*Github:* [[524](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L524), [533](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L533), [537](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L537), [540](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L540), [544](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L544), [545](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L545), [548](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L548), [549](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L549), [551](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L551), [554](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L554)]


```solidity
File: registries/contracts/staking/StakingFactory.sol

/// @audit `.verifyImplementation(...)` is called on line 195
/// @audit `.call(...)` is called on line 216
/// @audit `.verifyInstance(...)` is called on line 231
237:         mapInstanceParams[instance] = instanceParams;

/// @audit `.verifyImplementation(...)` is called on line 195
/// @audit `.call(...)` is called on line 216
/// @audit `.verifyInstance(...)` is called on line 231
239:         nonce = localNonce + 1;

/// @audit `.verifyImplementation(...)` is called on line 195
/// @audit `.call(...)` is called on line 216
/// @audit `.verifyInstance(...)` is called on line 231
243:         _locked = 1;

```
*Github:* [[237](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L237), [239](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L239), [243](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L243)]


```solidity
File: tokenomics/contracts/Dispenser.sol

/// @audit `.getBridgingDecimals(...)` is called on line 596
606:                 mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;

/// @audit `.getBridgingDecimals(...)` is called on line 596
619:                         withheldAmount -= transferAmounts[i];

/// @audit `.getBridgingDecimals(...)` is called on line 596
623:                         withheldAmount = 0;

/// @audit `.getBridgingDecimals(...)` is called on line 596
625:                     mapChainIdWithheldAmounts[chainIds[i]] = withheldAmount;

/// @audit `.paused(...)` is called on line 741
745:         mapLastClaimedStakingEpochs[nomineeHash] = ITokenomics(tokenomics).epochCounter();

/// @audit `.epochCounter(...)` is called on line 762
/// @audit `.getEpochEndTime(...)` is called on line 766
/// @audit `.epochLen(...)` is called on line 769
779:         mapRemovedNomineeEpochs[nomineeHash] = eCounter;

/// @audit `.paused(...)` is called on line 802
/// @audit `.accountOwnerIncentives(...)` is called on line 807
/// @audit `.balanceOf(...)` is called on line 815
/// @audit `.withdrawToAccount(...)` is called on line 818
/// @audit `.balanceOf(...)` is called on line 822
836:         _locked = 1;

/// @audit `.paused(...)` is called on line 984
/// @audit `.getBridgingDecimals(...)` is called on line 990
997:         mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;

/// @audit `.paused(...)` is called on line 984
/// @audit `.getBridgingDecimals(...)` is called on line 990
/// @audit `.refundFromStaking(...)` is called on line 1001
1016:                     withheldAmount -= transferAmount;

/// @audit `.paused(...)` is called on line 984
/// @audit `.getBridgingDecimals(...)` is called on line 990
/// @audit `.refundFromStaking(...)` is called on line 1001
1021:                     withheldAmount = 0;

/// @audit `.paused(...)` is called on line 984
/// @audit `.getBridgingDecimals(...)` is called on line 990
/// @audit `.refundFromStaking(...)` is called on line 1001
1023:                 mapChainIdWithheldAmounts[chainId] = withheldAmount;

/// @audit `.paused(...)` is called on line 984
/// @audit `.getBridgingDecimals(...)` is called on line 990
/// @audit `.refundFromStaking(...)` is called on line 1001
/// @audit `.balanceOf(...)` is called on line 1028
/// @audit `.withdrawToAccount(...)` is called on line 1031
/// @audit `.balanceOf(...)` is called on line 1034
1046:         _locked = 1;

/// @audit `.paused(...)` is called on line 1081
/// @audit `.refundFromStaking(...)` is called on line 1099
/// @audit `.balanceOf(...)` is called on line 1106
/// @audit `.withdrawToAccount(...)` is called on line 1109
/// @audit `.balanceOf(...)` is called on line 1112
1125:         _locked = 1;

/// @audit `.mapEpochStakingPoints(...)` is called on line 1148
/// @audit `.getEpochEndTime(...)` is called on line 1151
/// @audit `.nomineeRelativeWeight(...)` is called on line 1154
/// @audit `.refundFromStaking(...)` is called on line 1162
1167:         _locked = 1;

/// @audit `.getBridgingDecimals(...)` is called on line 1217
1234:         mapChainIdWithheldAmounts[chainId] = withheldAmount;

```
*Github:* [[606](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L606), [619](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L619), [623](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L623), [625](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L625), [745](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L745), [779](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L779), [836](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L836), [997](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L997), [1016](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1016), [1021](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1021), [1023](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1023), [1046](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1046), [1125](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1125), [1167](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1167), [1234](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1234)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

/// @audit `.timeLaunch(...)` is called on line 462
474:         inflationPerSecond = uint96(_inflationPerSecond);

/// @audit `.timeLaunch(...)` is called on line 462
475:         timeLaunch = uint32(_timeLaunch);

/// @audit `.timeLaunch(...)` is called on line 462
481:         epochCounter = 1;

/// @audit `.timeLaunch(...)` is called on line 462
485:         devsPerCapital = 1e18;

/// @audit `.timeLaunch(...)` is called on line 462
499:         codePerDev = 1e18;

/// @audit `.timeLaunch(...)` is called on line 462
510:         maxBond = uint96(_maxBond);

/// @audit `.timeLaunch(...)` is called on line 462
511:         effectiveBond = uint96(_maxBond);

/// @audit `.ownerOf(...)` is called on line 910
/// @audit `.getVotes(...)` is called on line 911
/// @audit `.getVotes(...)` is called on line 912
963:                         mapNewUnits[unitType][serviceUnitIds[j]] = true;

/// @audit `.ownerOf(...)` is called on line 910
/// @audit `.getVotes(...)` is called on line 911
/// @audit `.getVotes(...)` is called on line 912
/// @audit `.ownerOf(...)` is called on line 967
969:                             mapNewOwners[unitOwner] = true;

/// @audit `.isDonatorBlacklisted(...)` is called on line 1003
/// @audit `.exists(...)` is called on line 1012
1026:         lastDonationBlockNumber = uint32(block.number);

/// @audit `.rebalanceTreasury(...)` is called on line 1285
1290:             epochCounter = uint32(eCounter + 1);

```
*Github:* [[474](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L474), [475](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L475), [481](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L481), [485](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L485), [499](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L499), [510](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L510), [511](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L511), [963](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L963), [969](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L969), [1026](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1026), [1290](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1290)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

/// @audit `.call(...)` is called on line 161
/// @audit `.balanceOf(...)` is called on line 189
/// @audit `.approve(...)` is called on line 191
/// @audit `.deposit(...)` is called on line 192
199:                 stakingQueueingNonces[queueHash] = true;

/// @audit `.call(...)` is called on line 161
/// @audit `.balanceOf(...)` is called on line 189
/// @audit `.approve(...)` is called on line 191
/// @audit `.deposit(...)` is called on line 192
205:         stakingBatchNonce = batchNonce + 1;

/// @audit `.call(...)` is called on line 161
/// @audit `.balanceOf(...)` is called on line 189
/// @audit `.approve(...)` is called on line 191
/// @audit `.deposit(...)` is called on line 192
209:             withheldAmount += localWithheldAmount;

/// @audit `.call(...)` is called on line 161
/// @audit `.balanceOf(...)` is called on line 189
/// @audit `.approve(...)` is called on line 191
/// @audit `.deposit(...)` is called on line 192
212:         _locked = 1;

/// @audit `.balanceOf(...)` is called on line 287
/// @audit `.approve(...)` is called on line 290
/// @audit `.deposit(...)` is called on line 291
296:             stakingQueueingNonces[queueHash] = false;

/// @audit `.balanceOf(...)` is called on line 287
/// @audit `.approve(...)` is called on line 290
/// @audit `.deposit(...)` is called on line 291
302:         _locked = 1;

/// @audit `.balanceOf(...)` is called on line 440
/// @audit `.transfer(...)` is called on line 443
450:         owner = address(0);

```
*Github:* [[199](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L199), [205](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L205), [209](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L209), [212](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L212), [296](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L296), [302](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L302), [450](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L450)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

/// @audit `.verifyInstanceAndGetEmissionsAmount(...)` is called on line 99
/// @audit `.transfer(...)` is called on line 112
/// @audit `.approve(...)` is called on line 118
/// @audit `.deposit(...)` is called on line 119
124:         _locked = 1;

```
*Github:* [[124](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L124)]


</details>


<a name="L-8"></a> 
### [L-8] External function calls within loops
Calling external functions within loops can easily result in insufficient gas. This greatly increases the likelihood of transaction failures, DOS attacks, and other unexpected actions. It is recommended to limit the number of loops within loops that call external functions, and to limit the gas line for each external call.

<details>
<summary>
There are <b>28</b> instances (click to show):
</summary>

```solidity
File: registries/contracts/staking/StakingToken.sol

/// @audit `.getAgentBond(...)`
93:             uint256 bond = IServiceTokenUtility(serviceRegistryTokenUtility).getAgentBond(serviceId, serviceAgentIds[i]);

```
*Github:* [[93](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingToken.sol#L93)]


```solidity
File: tokenomics/contracts/Dispenser.sol

/// @audit `.transfer(...)`
456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]);

/// @audit `.getBridgingDecimals(...)`
596:             uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();

/// @audit `.mapEpochStakingPoints(...)`
884:                 ITokenomics(tokenomics).mapEpochStakingPoints(j);

/// @audit `.getEpochEndTime(...)`
886:             uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);

/// @audit `.nomineeRelativeWeight(...)`
893:                 IVoteWeighting(voteWeighting).nomineeRelativeWeight(stakingTarget, chainId, endTime);

/// @audit `.mapEpochStakingPoints(...)`
1148:             ITokenomics.StakingPoint memory stakingPoint = ITokenomics(tokenomics).mapEpochStakingPoints(j);

/// @audit `.getEpochEndTime(...)`
1151:             uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);

/// @audit `.nomineeRelativeWeight(...)`
1154:             (uint256 stakingWeight, ) = IVoteWeighting(voteWeighting).nomineeRelativeWeight(retainer,

```
*Github:* [[456](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L456), [596](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L596), [884](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L884), [886](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L886), [893](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L893), [1148](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1148), [1151](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1151), [1154](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1154)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

/// @audit `.ownerOf(...)`
910:                 address serviceOwner = IToken(serviceRegistry).ownerOf(serviceIds[i]);

/// @audit `.getVotes(...)`
911:                 topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||

/// @audit `.getVotes(...)`
912:                     IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;

/// @audit `.ownerOf(...)`
967:                         address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);

/// @audit `.ownerOf(...)`
967:                         address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);

/// @audit `.ownerOf(...)`
967:                         address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);

/// @audit `.exists(...)`
1012:             if (!IServiceRegistry(serviceRegistry).exists(serviceIds[i])) {

/// @audit `.totalSupply(...)`
1330:             registriesSupply[i] = IToken(registries[i]).totalSupply();

/// @audit `.ownerOf(...)`
1348:             address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);

/// @audit `.totalSupply(...)`
1402:             registriesSupply[i] = IToken(registries[i]).totalSupply();

/// @audit `.ownerOf(...)`
1420:             address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);

```
*Github:* [[910](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L910), [911](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L911), [912](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L912), [967](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L967), [967](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L967), [967](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L967), [1012](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1012), [1330](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1330), [1348](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1348), [1402](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1402), [1420](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1420)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

/// @audit `.call(...)`
161:             (bool success, bytes memory returnData) = stakingFactory.call(verifyData);

/// @audit `.balanceOf(...)`
189:             if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {

/// @audit `.approve(...)`
191:                 IToken(olas).approve(target, amount);

/// @audit `.deposit(...)`
192:                 IStaking(target).deposit(amount);

```
*Github:* [[161](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161), [189](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L189), [191](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L191), [192](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L192)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

/// @audit `.verifyInstanceAndGetEmissionsAmount(...)`
99:             uint256 limitAmount = IStakingFactory(stakingFactory).verifyInstanceAndGetEmissionsAmount(target);

/// @audit `.transfer(...)`
112:                 IToken(olas).transfer(timelock, refundAmount);

/// @audit `.approve(...)`
118:             IToken(olas).approve(target, amount);

/// @audit `.deposit(...)`
119:             IStaking(target).deposit(amount);

```
*Github:* [[99](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L99), [112](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L112), [118](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L118), [119](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L119)]


</details>


<a name="L-9"></a> 
### [L-9] Functions calling contracts/addresses with transfer hooks are missing reentrancy guards
While adherence to the check-effects-interaction pattern is commendable, the absence of a reentrancy guard in functions, especially where transfer hooks might be present, can expose the protocol users to risks of read-only reentrancies. Such reentrancy vulnerabilities can be exploited to execute malicious actions even without altering the contract state. Without a reentrancy guard, the only potential mitigation would be to blocklist the entire protocol - an extreme and disruptive measure. Therefore, incorporating a reentrancy guard into these functions is vital to bolster security, as it helps protect against both traditional reentrancy attacks and read-only reentrancies, ensuring robust and safe protocol operations.

*There are <b>8</b> instances of this issue:*
```solidity
File: registries/contracts/staking/StakingBase.sol

719:     function stake(uint256 serviceId) external {
             // Check if there available rewards
             if (availableRewards == 0) {
                 revert NoRewardsAvailable();
             }
     
             // Check if the evicted service has not yet unstaked
             ServiceInfo storage sInfo = mapServiceInfo[serviceId];
             // tsStart being greater than zero means that the service was not yet unstaked: still staking or evicted
             if (sInfo.tsStart > 0) {
                 revert ServiceNotUnstaked(serviceId);
             }
     
             // Check for the maximum number of staking services
             uint256 numStakingServices = setServiceIds.length;
             if (numStakingServices == maxNumServices) {
                 revert MaxNumServicesReached(maxNumServices);
             }
     
             // Check the service conditions for staking
             IService.Service memory service = IService(serviceRegistry).getService(serviceId);
     
             // Check the number of agent instances
             if (numAgentInstances != service.maxNumAgentInstances) {
                 revert WrongServiceConfiguration(serviceId);
             }
     
             // Check the configuration hash, if applicable
             if (configHash != 0 && configHash != service.configHash) {
                 revert WrongServiceConfiguration(serviceId);
             }
             // Check the threshold, if applicable
             if (threshold > 0 && threshold != service.threshold) {
                 revert WrongServiceConfiguration(serviceId);
             }
             // The service must be deployed
             if (service.state != IService.ServiceState.Deployed) {
                 revert WrongServiceState(uint256(service.state), serviceId);
             }
     
             // Check that the multisig address corresponds to the authorized multisig proxy bytecode hash
             bytes32 multisigProxyHash = keccak256(service.multisig.code);
             if (proxyHash != multisigProxyHash) {
                 revert UnauthorizedMultisig(service.multisig);
             }
     
             // Check the agent Ids requirement, if applicable
             uint256 size = agentIds.length;
             if (size > 0) {
                 uint256 numAgents = service.agentIds.length;
     
                 if (size != numAgents) {
                     revert WrongServiceConfiguration(serviceId);
                 }
                 for (uint256 i = 0; i < numAgents; ++i) {
                     // Check that the agent Ids
                     if (agentIds[i] != service.agentIds[i]) {
                         revert WrongAgentId(agentIds[i]);
                     }
                 }
             }
     
             // Check service staking deposit and token, if applicable
             _checkTokenStakingDeposit(serviceId, service.securityDeposit, service.agentIds);
     
             // ServiceInfo struct will be an empty one since otherwise the safeTransferFrom above would fail
             sInfo.multisig = service.multisig;
             sInfo.owner = msg.sender;
             // This function might revert if it's incorrectly implemented, however this is not a protocol's responsibility
             // It is safe to revert in this place
             uint256[] memory nonces = IActivityChecker(activityChecker).getMultisigNonces(service.multisig);
             sInfo.nonces = nonces;
             sInfo.tsStart = block.timestamp;
     
             // Add the service Id to the set of staked services
             setServiceIds.push(serviceId);
     
             // Transfer the service for staking
             IService(serviceRegistry).safeTransferFrom(msg.sender, address(this), serviceId);

805:     function unstake(uint256 serviceId) external returns (uint256 reward) {
             ServiceInfo storage sInfo = mapServiceInfo[serviceId];
             // Check for the service ownership
             if (msg.sender != sInfo.owner) {
                 revert OwnerOnly(msg.sender, sInfo.owner);
             }
     
             // Get the staking start time
             // Note that if the service info exists, the service is staked or evicted, and thus start time is always valid
             uint256 tsStart = sInfo.tsStart;
     
             // Check that the service has staked long enough, or if there are no rewards left
             uint256 ts = block.timestamp - tsStart;
             if (ts <= minStakingDuration && availableRewards > 0) {
                 revert NotEnoughTimeStaked(serviceId, ts, minStakingDuration);
             }
     
             // Call the checkpoint
             (uint256[] memory serviceIds, , , , uint256[] memory evictServiceIds) = checkpoint();
     
             // If the checkpoint was not successful, the serviceIds set is not returned and needs to be allocated
             if (serviceIds.length == 0) {
                 serviceIds = getServiceIds();
                 evictServiceIds = new uint256[](serviceIds.length);
             }
     
             // Get the service index in the set of services
             // The index must always exist as the service is currently staked, otherwise it has no record in the map
             uint256 idx;
             bool inSet;
             for (; idx < serviceIds.length; ++idx) {
                 // Service is still in a global staking set if it is found in the services set,
                 // and is not present in the evicted set
                 if (evictServiceIds[idx] == serviceId) {
                     break;
                 } else if (serviceIds[idx] == serviceId) {
                     inSet = true;
                     break;
                 }
             }
     
             // Get the unstaked service data
             reward = sInfo.reward;
             uint256[] memory nonces = sInfo.nonces;
             address multisig = sInfo.multisig;
     
             // Clear all the data about the unstaked service
             // Delete the service info struct
             delete mapServiceInfo[serviceId];
     
             // Update the set of staked service Ids
             // If the index was not found, the service was evicted and is not part of staked services set
             if (inSet) {
                 setServiceIds[idx] = setServiceIds[setServiceIds.length - 1];
                 setServiceIds.pop();
             }
     
             // Transfer the service back to the owner
             // Note that the reentrancy is not possible due to the ServiceInfo struct being deleted
             IService(serviceRegistry).safeTransferFrom(address(this), msg.sender, serviceId);

```
*Github:* [[719](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L719), [805](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L805)]


```solidity
File: registries/contracts/staking/StakingToken.sol

115:     function deposit(uint256 amount) external {
             // Add to the contract and available rewards balances
             uint256 newBalance = balance + amount;
             uint256 newAvailableRewards = availableRewards + amount;
     
             // Record the new actual balance and available rewards
             balance = newBalance;
             availableRewards = newAvailableRewards;
     
             // Add to the overall balance
             SafeTransferLib.safeTransferFrom(stakingToken, msg.sender, address(this), amount);

```
*Github:* [[115](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingToken.sol#L115)]


```solidity
File: tokenomics/contracts/Dispenser.sol

789:     function claimOwnerIncentives(
             uint256[] memory unitTypes,
             uint256[] memory unitIds
         ) external returns (uint256 reward, uint256 topUp) {
             // Reentrancy guard
             if (_locked > 1) {
                 revert ReentrancyGuard();
             }
             _locked = 2;
     
             // Check for the paused state
             Pause currentPause = paused;
             if (currentPause == Pause.DevIncentivesPaused || currentPause == Pause.AllPaused ||
                 ITreasury(treasury).paused() == 2) {
                 revert Paused();
             }
     
             // Calculate incentives
             (reward, topUp) = ITokenomics(tokenomics).accountOwnerIncentives(msg.sender, unitTypes, unitIds);
     
             bool success;
             // Request treasury to transfer funds to msg.sender if reward > 0 or topUp > 0
             if ((reward + topUp) > 0) {
                 // Get the current OLAS balance
                 uint256 olasBalance;
                 if (topUp > 0) {
                     olasBalance = IToken(olas).balanceOf(msg.sender);
                 }
     
                 success = ITreasury(treasury).withdrawToAccount(msg.sender, reward, topUp);

953:     function claimStakingIncentives(
             uint256 numClaimedEpochs,
             uint256 chainId,
             bytes32 stakingTarget,
             bytes memory bridgePayload
         ) external payable {
             // Reentrancy guard
             if (_locked > 1) {
                 revert ReentrancyGuard();
             }
             _locked = 2;
     
             // Check for zero chain Id
             if (chainId == 0) {
                 revert ZeroValue();
             }
     
             // Check for zero target address
             if (stakingTarget == 0) {
                 revert ZeroAddress();
             }
     
             // Check the number of claimed epochs
             uint256 localMaxNumClaimingEpochs = maxNumClaimingEpochs;
             if (numClaimedEpochs > localMaxNumClaimingEpochs) {
                 revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);
             }
     
             // Check for the paused state
             Pause currentPause = paused;
             if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
                 ITreasury(treasury).paused() == 2) {
                 revert Paused();
             }
     
             // Get deposit processor bridging decimals corresponding to a chain Id
             address depositProcessor = mapChainIdDepositProcessors[chainId];
             uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();
     
             // Get the staking incentive to send as a deposit with, and the amount to return back to staking inflation
             (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =
                 calculateStakingIncentives(numClaimedEpochs, chainId, stakingTarget, bridgingDecimals);
     
             // Write last claimed epoch counter to start claiming from the next time
             mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;
     
             // Refund returned amount back to tokenomics inflation
             if (returnAmount > 0) {
                 ITokenomics(tokenomics).refundFromStaking(returnAmount);
             }
     
             uint256 transferAmount;
     
             // Check if staking incentive is deposited
             if (stakingIncentive > 0) {
                 transferAmount = stakingIncentive;
                 // Account for possible withheld OLAS amounts
                 // Note: in case of normalized staking incentives with bridging decimals, this is correctly managed
                 // as normalized amounts are returned from another side
                 uint256 withheldAmount = mapChainIdWithheldAmounts[chainId];
                 if (withheldAmount > 0) {
                     // If withheld amount is enough to cover all the staking incentives, the transfer of OLAS is not needed
                     if (withheldAmount >= transferAmount) {
                         withheldAmount -= transferAmount;
                         transferAmount = 0;
                     } else {
                         // Otherwise, reduce the transfer of tokens for the OLAS withheld amount
                         transferAmount -= withheldAmount;
                         withheldAmount = 0;
                     }
                     mapChainIdWithheldAmounts[chainId] = withheldAmount;
                 }
     
                 // Check if minting is needed as the actual OLAS transfer is required
                 if (transferAmount > 0) {
                     uint256 olasBalance = IToken(olas).balanceOf(address(this));
     
                     // Mint tokens to self in order to distribute to the staking deposit processor
                     ITreasury(treasury).withdrawToAccount(address(this), 0, transferAmount);

1058:     function claimStakingIncentivesBatch(
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
      
              // Check the number of claimed epochs
              uint256 localMaxNumClaimingEpochs = maxNumClaimingEpochs;
              if (numClaimedEpochs > localMaxNumClaimingEpochs) {
                  revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);
              }
      
              Pause currentPause = paused;
              if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
                  ITreasury(treasury).paused() == 2) {
                  revert Paused();
              }
      
              // Total staking incentives
              // 0: Staking incentive across all the targets to send as a deposit
              // 1: Actual OLAS transfer amount across all the targets
              // 2: Staking incentive to return back to staking inflation
              uint256[] memory totalAmounts;
              // Arrays of staking and transfer amounts
              uint256[][] memory stakingIncentives;
              uint256[] memory transferAmounts;
      
              (totalAmounts, stakingIncentives, transferAmounts) = _calculateStakingIncentivesBatch(numClaimedEpochs, chainIds,

```
*Github:* [[789](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L789), [953](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L953), [1058](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1058)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

377:     function drain() external returns (uint256 amount) {
             // Reentrancy guard
             if (_locked > 1) {
                 revert ReentrancyGuard();
             }
             _locked = 2;
     
             // Check for the owner address
             if (msg.sender != owner) {
                 revert OwnerOnly(msg.sender, owner);
             }
     
             // Drain the slashed funds
             amount = address(this).balance;
             if (amount == 0) {
                 revert ZeroValue();
             }
     
             // Send funds to the owner
             (bool success, ) = msg.sender.call{value: amount}("");
             if (!success) {
                 revert TransferFailed(address(0), address(this), msg.sender, amount);

412:     function migrate(address newL2TargetDispenser) external {
             // Reentrancy guard
             if (_locked > 1) {
                 revert ReentrancyGuard();
             }
             _locked = 2;
     
             // Check for the owner address
             if (msg.sender != owner) {
                 revert OwnerOnly(msg.sender, owner);
             }
     
             // Check that the contract is paused
             if (paused == 1) {
                 revert Unpaused();
             }
     
             // Check that the migration address is a contract
             if (newL2TargetDispenser.code.length == 0) {
                 revert WrongAccount(newL2TargetDispenser);
             }
     
             // Check that the new address is not the current one
             if (newL2TargetDispenser == address(this)) {
                 revert WrongAccount(address(this));
             }
     
             // Get OLAS token amount
             uint256 amount = IToken(olas).balanceOf(address(this));
             // Transfer amount to the new L2 target dispenser
             if (amount > 0) {
                 bool success = IToken(olas).transfer(newL2TargetDispenser, amount);
                 if (!success) {
                     revert TransferFailed(olas, address(this), newL2TargetDispenser, amount);

```
*Github:* [[377](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L377), [412](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L412)]



<a name="L-10"></a> 
### [L-10] Large approvals may not work with some `ERC20` tokens
Not all `IERC20` implementations are totally compliant, and some (e.g `UNI`, `COMP`) may fail if the valued passed to `approve` is larger than `uint96`. If the approval amount is `type(uint256).max`, which may cause issues with systems that expect the value passed to approve to be reflected in the allowances mapping.

*There are <b>7</b> instances of this issue:*
```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

174:             IToken(olas).approve(l1ERC20Gateway, transferAmount);

```
*Github:* [[174](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L174)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

191:                 IToken(olas).approve(target, amount);

290:             IToken(olas).approve(target, amount);

```
*Github:* [[191](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L191), [290](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L290)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

118:             IToken(olas).approve(target, amount);

```
*Github:* [[118](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L118)]


```solidity
File: tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

65:             IToken(olas).approve(l1TokenRelayer, transferAmount);

```
*Github:* [[65](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L65)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

111:             IToken(olas).approve(l1TokenRelayer, transferAmount);

```
*Github:* [[111](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L111)]


```solidity
File: tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

68:             IToken(olas).approve(predicate, transferAmount);

```
*Github:* [[68](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L68)]



<a name="L-11"></a> 
### [L-11] Loss of precision in divisions
Division by large numbers may result in the result being zero, due to solidity not supporting fractions. Consider requiring a minimum amount for the numerator to ensure that it is always larger than the denominator.

<details>
<summary>
There are <b>13</b> instances (click to show):
</summary>

```solidity
File: governance/contracts/VoteWeighting.sol

309:         uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;

489:         uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;

605:         uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;

```
*Github:* [[309](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L309), [489](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L489), [605](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L605)]


```solidity
File: registries/contracts/staking/StakingActivityChecker.sol

58:             uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts;

```
*Github:* [[58](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingActivityChecker.sol#L58)]


```solidity
File: registries/contracts/staking/StakingBase.sol

622:                     updatedReward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards;

633:                 updatedReward = (eligibleServiceRewards[0] * lastAvailableRewards) / totalRewards;

904:                     reward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards;

```
*Github:* [[622](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L622), [633](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L633), [904](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L904)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

1126:         uint256 numYears = (block.timestamp - timeLaunch) / ONE_YEAR;

1134:             inflationPerEpoch = (yearEndTime - prevEpochTime) * curInflationPerSecond;

1138:             inflationPerEpoch += (block.timestamp - yearEndTime) * curInflationPerSecond;

1243:         numYears = (block.timestamp + curEpochLen - timeLaunch) / ONE_YEAR;

1250:             inflationPerEpoch = (yearEndTime - block.timestamp) * curInflationPerSecond;

1254:             inflationPerEpoch += (block.timestamp + curEpochLen - yearEndTime) * curInflationPerSecond;

```
*Github:* [[1126](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1126), [1134](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1134), [1138](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1138), [1243](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1243), [1250](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1250), [1254](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1254)]


</details>


<a name="L-12"></a> 
### [L-12] Missing checks for state variable assignments
There are some missing checks in these functions, and this could lead to unexpected scenarios. Consider always adding a sanity check for state variables.

<details>
<summary>
There are <b>74</b> instances (click to show):
</summary>

```solidity
File: governance/contracts/VoteWeighting.sol

/// @audit `_ve`
213:         ve = _ve;

/// @audit `newOwner`
379:         owner = newOwner;

/// @audit `newDispenser`
392:         dispenser = newDispenser;

```
*Github:* [[213](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L213), [379](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L379), [392](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L392)]


```solidity
File: registries/contracts/staking/StakingActivityChecker.sol

/// @audit `_livenessRatio`
30:         livenessRatio = _livenessRatio;

```
*Github:* [[30](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingActivityChecker.sol#L30)]


```solidity
File: registries/contracts/staking/StakingFactory.sol

/// @audit `_verifier`
105:         verifier = _verifier;

/// @audit `newOwner`
121:         owner = newOwner;

/// @audit `newVerifier`
133:         verifier = newVerifier;

```
*Github:* [[105](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L105), [121](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L121), [133](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L133)]


```solidity
File: registries/contracts/staking/StakingToken.sol

/// @audit `_stakingToken`
67:         stakingToken = _stakingToken;

/// @audit `_serviceRegistryTokenUtility`
68:         serviceRegistryTokenUtility = _serviceRegistryTokenUtility;

```
*Github:* [[67](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingToken.sol#L67), [68](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingToken.sol#L68)]


```solidity
File: registries/contracts/staking/StakingVerifier.sol

/// @audit `_olas`
87:         olas = _olas;

/// @audit `_rewardsPerSecondLimit`
88:         rewardsPerSecondLimit = _rewardsPerSecondLimit;

/// @audit `_timeForEmissionsLimit`
89:         timeForEmissionsLimit = _timeForEmissionsLimit;

/// @audit `_numServicesLimit`
90:         numServicesLimit = _numServicesLimit;

/// @audit `newOwner`
107:         owner = newOwner;

/// @audit `setCheck`
120:         implementationsCheck = setCheck;

/// @audit `setCheck`
147:         implementationsCheck = setCheck;

/// @audit `_rewardsPerSecondLimit`
244:         rewardsPerSecondLimit = _rewardsPerSecondLimit;

/// @audit `_timeForEmissionsLimit`
245:         timeForEmissionsLimit = _timeForEmissionsLimit;

/// @audit `_numServicesLimit`
246:         numServicesLimit = _numServicesLimit;

```
*Github:* [[87](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L87), [88](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L88), [89](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L89), [90](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L90), [107](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L107), [120](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L120), [147](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L147), [244](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L244), [245](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L245), [246](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L246)]


```solidity
File: tokenomics/contracts/Dispenser.sol

/// @audit `_olas`
340:         olas = _olas;

/// @audit `_tokenomics`
341:         tokenomics = _tokenomics;

/// @audit `_treasury`
342:         treasury = _treasury;

/// @audit `_voteWeighting`
343:         voteWeighting = _voteWeighting;

/// @audit `_retainer`
345:         retainer = _retainer;

/// @audit `_maxNumClaimingEpochs`
347:         maxNumClaimingEpochs = _maxNumClaimingEpochs;

/// @audit `_maxNumStakingTargets`
348:         maxNumStakingTargets = _maxNumStakingTargets;

/// @audit `newOwner`
647:         owner = newOwner;

/// @audit `_tokenomics`
663:             tokenomics = _tokenomics;

/// @audit `_treasury`
669:             treasury = _treasury;

/// @audit `_voteWeighting`
675:             voteWeighting = _voteWeighting;

/// @audit `_maxNumClaimingEpochs`
694:         maxNumClaimingEpochs = _maxNumClaimingEpochs;

/// @audit `_maxNumStakingTargets`
695:         maxNumStakingTargets = _maxNumStakingTargets;

/// @audit `pauseState`
1247:         paused = pauseState;

```
*Github:* [[340](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L340), [341](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L341), [342](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L342), [343](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L343), [345](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L345), [347](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L347), [348](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L348), [647](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L647), [663](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L663), [669](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L669), [675](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L675), [694](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L694), [695](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L695), [1247](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L1247)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

/// @audit `_olas`
450:         olas = _olas;

/// @audit `_treasury`
451:         treasury = _treasury;

/// @audit `_depository`
452:         depository = _depository;

/// @audit `_dispenser`
453:         dispenser = _dispenser;

/// @audit `_ve`
454:         ve = _ve;

/// @audit `_componentRegistry`
456:         componentRegistry = _componentRegistry;

/// @audit `_agentRegistry`
457:         agentRegistry = _agentRegistry;

/// @audit `_serviceRegistry`
458:         serviceRegistry = _serviceRegistry;

/// @audit `_donatorBlacklist`
459:         donatorBlacklist = _donatorBlacklist;

/// @audit `newOwner`
557:         owner = newOwner;

/// @audit `_treasury`
573:             treasury = _treasury;

/// @audit `_depository`
578:             depository = _depository;

/// @audit `_dispenser`
583:             dispenser = _dispenser;

/// @audit `_componentRegistry`
600:             componentRegistry = _componentRegistry;

/// @audit `_agentRegistry`
604:             agentRegistry = _agentRegistry;

/// @audit `_serviceRegistry`
608:             serviceRegistry = _serviceRegistry;

/// @audit `_donatorBlacklist`
622:         donatorBlacklist = _donatorBlacklist;

```
*Github:* [[450](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L450), [451](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L451), [452](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L452), [453](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L453), [454](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L454), [456](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L456), [457](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L457), [458](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L458), [459](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L459), [557](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L557), [573](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L573), [578](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L578), [583](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L583), [600](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L600), [604](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L604), [608](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L608), [622](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L622)]


```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

/// @audit `_l1ERC20Gateway`
106:         l1ERC20Gateway = _l1ERC20Gateway;

/// @audit `_outbox`
107:         outbox = _outbox;

/// @audit `_bridge`
108:         bridge = _bridge;

```
*Github:* [[106](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L106), [107](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L107), [108](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L108)]


```solidity
File: tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

/// @audit `_olas`
81:         olas = _olas;

/// @audit `_l1Dispenser`
82:         l1Dispenser = _l1Dispenser;

/// @audit `_l1TokenRelayer`
83:         l1TokenRelayer = _l1TokenRelayer;

/// @audit `_l1MessageRelayer`
84:         l1MessageRelayer = _l1MessageRelayer;

/// @audit `_l2TargetChainId`
85:         l2TargetChainId = _l2TargetChainId;

/// @audit `l2Dispenser`
196:         l2TargetDispenser = l2Dispenser;

```
*Github:* [[81](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L81), [82](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L82), [83](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L83), [84](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L84), [85](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L85), [196](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L196)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

/// @audit `_olas`
125:         olas = _olas;

/// @audit `_stakingFactory`
126:         stakingFactory = _stakingFactory;

/// @audit `_l2MessageRelayer`
127:         l2MessageRelayer = _l2MessageRelayer;

/// @audit `_l1DepositProcessor`
128:         l1DepositProcessor = _l1DepositProcessor;

/// @audit `_l1SourceChainId`
129:         l1SourceChainId = _l1SourceChainId;

/// @audit `newOwner`
258:         owner = newOwner;

```
*Github:* [[125](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L125), [126](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L126), [127](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L127), [128](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L128), [129](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L129), [258](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L258)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

/// @audit `_olas`
76:         olas = _olas;

/// @audit `_dispenser`
77:         dispenser = _dispenser;

/// @audit `_stakingFactory`
78:         stakingFactory = _stakingFactory;

/// @audit `_timelock`
79:         timelock = _timelock;

```
*Github:* [[76](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L76), [77](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L77), [78](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L78), [79](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L79)]


```solidity
File: tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

/// @audit `_l2TokenRelayer`
54:         l2TokenRelayer = _l2TokenRelayer;

```
*Github:* [[54](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L54)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

/// @audit `_olasL2`
91:         olasL2 = _olasL2;

```
*Github:* [[91](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L91)]


```solidity
File: tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

/// @audit `_predicate`
53:         predicate = _predicate;

```
*Github:* [[53](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L53)]


```solidity
File: tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

/// @audit `_wormholeTargetChainId`
55:         wormholeTargetChainId = _wormholeTargetChainId;

```
*Github:* [[55](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L55)]


```solidity
File: tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

/// @audit `_l1SourceChainId`
86:         l1SourceChainId = _l1SourceChainId;

```
*Github:* [[86](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L86)]


</details>


<a name="L-13"></a> 
### [L-13] Missing limits when setting min/max amounts
There are some missing limits in these functions, and this could lead to unexpected scenarios. Consider adding a min/max limit for the following values, when appropriate.

*There are <b>2</b> instances of this issue:*
```solidity
File: registries/contracts/staking/StakingVerifier.sol

/// @audit `_rewardsPerSecondLimit`
/// @audit `_timeForEmissionsLimit`
/// @audit `_numServicesLimit`
229:     function changeStakingLimits(
             uint256 _rewardsPerSecondLimit,
             uint256 _timeForEmissionsLimit,
             uint256 _numServicesLimit
         ) external {

```
*Github:* [[229](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L229)]


```solidity
File: tokenomics/contracts/Dispenser.sol

/// @audit `_maxNumClaimingEpochs`
/// @audit `_maxNumStakingTargets`
683:     function changeStakingParams(uint256 _maxNumClaimingEpochs, uint256 _maxNumStakingTargets) external {

```
*Github:* [[683](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L683)]



<a name="L-14"></a> 
### [L-14] Missing zero address check in constructor
Constructors often take address parameters to initialize important components of a contract, such as owner or linked contracts. However, without a checking, there's a risk that an address parameter could be mistakenly set to the zero address (0x0). This could be due to an error or oversight during contract deployment. A zero address in a crucial role can cause serious issues, as it cannot perform actions like a normal address, and any funds sent to it will be irretrievable. It's therefore crucial to include a zero address check in constructors to prevent such potential problems. If a zero address is detected, the constructor should revert the transaction.

<details>
<summary>
There are <b>12</b> instances (click to show):
</summary>

```solidity
File: registries/contracts/staking/StakingFactory.sol

/// @audit missing zero check for `_verifier`
103:     constructor(address _verifier) {

```
*Github:* [[103](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L103)]


```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

/// @audit missing zero check for `_olas`
/// @audit missing zero check for `_l1Dispenser`
/// @audit missing zero check for `_l1TokenRelayer`
/// @audit missing zero check for `_l1MessageRelayer`
89:     constructor(
            address _olas,
            address _l1Dispenser,
            address _l1TokenRelayer,
            address _l1MessageRelayer,
            uint256 _l2TargetChainId,
            address _l1ERC20Gateway,
            address _outbox,
            address _bridge
        )
            DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
        {

```
*Github:* [[89](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L89)]


```solidity
File: tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

/// @audit missing zero check for `_olas`
/// @audit missing zero check for `_proxyFactory`
/// @audit missing zero check for `_l2MessageRelayer`
36:     constructor(
            address _olas,
            address _proxyFactory,
            address _l2MessageRelayer,
            address _l1DepositProcessor,
            uint256 _l1SourceChainId
        )
            DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
        {

```
*Github:* [[36](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L36)]


```solidity
File: tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

/// @audit missing zero check for `_olas`
59:     constructor(
            address _olas,
            address _l1Dispenser,
            address _l1TokenRelayer,
            address _l1MessageRelayer,
            uint256 _l2TargetChainId
        ) {

```
*Github:* [[59](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L59)]


```solidity
File: tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

/// @audit missing zero check for `_olas`
/// @audit missing zero check for `_l1Dispenser`
/// @audit missing zero check for `_l1TokenRelayer`
/// @audit missing zero check for `_l1MessageRelayer`
42:     constructor(
            address _olas,
            address _l1Dispenser,
            address _l1TokenRelayer,
            address _l1MessageRelayer,
            uint256 _l2TargetChainId
        ) DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId) {}

```
*Github:* [[42](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L42)]


```solidity
File: tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

/// @audit missing zero check for `_olas`
/// @audit missing zero check for `_proxyFactory`
/// @audit missing zero check for `_l2MessageRelayer`
/// @audit missing zero check for `_l1DepositProcessor`
39:     constructor(
            address _olas,
            address _proxyFactory,
            address _l2MessageRelayer,
            address _l1DepositProcessor,
            uint256 _l1SourceChainId,
            address _l2TokenRelayer
        )
            DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
        {

```
*Github:* [[39](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L39)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

/// @audit missing zero check for `_olas`
/// @audit missing zero check for `_l1Dispenser`
/// @audit missing zero check for `_l1TokenRelayer`
/// @audit missing zero check for `_l1MessageRelayer`
76:     constructor(
            address _olas,
            address _l1Dispenser,
            address _l1TokenRelayer,
            address _l1MessageRelayer,
            uint256 _l2TargetChainId,
            address _olasL2
        )
            DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
        {

```
*Github:* [[76](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L76)]


```solidity
File: tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

/// @audit missing zero check for `_olas`
/// @audit missing zero check for `_proxyFactory`
/// @audit missing zero check for `_l2MessageRelayer`
/// @audit missing zero check for `_l1DepositProcessor`
47:     constructor(
            address _olas,
            address _proxyFactory,
            address _l2MessageRelayer,
            address _l1DepositProcessor,
            uint256 _l1SourceChainId
        ) DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId) {}

```
*Github:* [[47](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L47)]


```solidity
File: tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

/// @audit missing zero check for `_olas`
/// @audit missing zero check for `_l1Dispenser`
/// @audit missing zero check for `_l1TokenRelayer`
/// @audit missing zero check for `_l1MessageRelayer`
36:     constructor(
            address _olas,
            address _l1Dispenser,
            address _l1TokenRelayer,
            address _l1MessageRelayer,
            uint256 _l2TargetChainId,
            address _checkpointManager,
            address _predicate
        )
            DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
            FxBaseRootTunnel(_checkpointManager, _l1MessageRelayer)
        {

```
*Github:* [[36](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L36)]


```solidity
File: tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

/// @audit missing zero check for `_olas`
/// @audit missing zero check for `_proxyFactory`
/// @audit missing zero check for `_l1DepositProcessor`
20:     constructor(
            address _olas,
            address _proxyFactory,
            address _l2MessageRelayer,
            address _l1DepositProcessor,
            uint256 _l1SourceChainId
        )
            DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
            FxBaseChildTunnel(_l2MessageRelayer)
        {}

```
*Github:* [[20](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L20)]


```solidity
File: tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

/// @audit missing zero check for `_olas`
/// @audit missing zero check for `_l1Dispenser`
/// @audit missing zero check for `_l1TokenRelayer`
/// @audit missing zero check for `_l1MessageRelayer`
28:     constructor(
            address _olas,
            address _l1Dispenser,
            address _l1TokenRelayer,
            address _l1MessageRelayer,
            uint256 _l2TargetChainId,
            address _wormholeCore,
            uint256 _wormholeTargetChainId
        )
            DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)
            TokenBase(_l1MessageRelayer, _l1TokenRelayer, _wormholeCore)
        {

```
*Github:* [[28](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L28)]


```solidity
File: tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

/// @audit missing zero check for `_olas`
/// @audit missing zero check for `_proxyFactory`
/// @audit missing zero check for `_l2MessageRelayer`
/// @audit missing zero check for `_l1DepositProcessor`
64:     constructor(
            address _olas,
            address _proxyFactory,
            address _l2MessageRelayer,
            address _l1DepositProcessor,
            uint256 _l1SourceChainId,
            address _wormholeCore,
            address _l2TokenRelayer
        )
            DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
            TokenBase(_l2MessageRelayer, _l2TokenRelayer, _wormholeCore)
        {

```
*Github:* [[64](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L64)]


</details>


<a name="L-15"></a> 
### [L-15] Consider some checks for `address(0)` when setting address state variables
Check for zero-address to avoid the risk of setting `address(0)` for state variables after an update.

<details>
<summary>
There are <b>55</b> instances (click to show):
</summary>

```solidity
File: governance/contracts/VoteWeighting.sol

/// @audit `_ve`
213:         ve = _ve;

/// @audit `newOwner`
379:         owner = newOwner;

/// @audit `newDispenser`
392:         dispenser = newDispenser;

```
*Github:* [[213](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L213), [379](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L379), [392](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L392)]


```solidity
File: registries/contracts/staking/StakingFactory.sol

/// @audit `_verifier`
105:         verifier = _verifier;

/// @audit `newOwner`
121:         owner = newOwner;

/// @audit `newVerifier`
133:         verifier = newVerifier;

```
*Github:* [[105](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L105), [121](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L121), [133](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L133)]


```solidity
File: registries/contracts/staking/StakingToken.sol

/// @audit `_stakingToken`
67:         stakingToken = _stakingToken;

/// @audit `_serviceRegistryTokenUtility`
68:         serviceRegistryTokenUtility = _serviceRegistryTokenUtility;

```
*Github:* [[67](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingToken.sol#L67), [68](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingToken.sol#L68)]


```solidity
File: registries/contracts/staking/StakingVerifier.sol

/// @audit `_olas`
87:         olas = _olas;

/// @audit `newOwner`
107:         owner = newOwner;

```
*Github:* [[87](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L87), [107](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L107)]


```solidity
File: tokenomics/contracts/Dispenser.sol

/// @audit `_olas`
340:         olas = _olas;

/// @audit `_tokenomics`
341:         tokenomics = _tokenomics;

/// @audit `_treasury`
342:         treasury = _treasury;

/// @audit `_voteWeighting`
343:         voteWeighting = _voteWeighting;

/// @audit `newOwner`
647:         owner = newOwner;

/// @audit `_tokenomics`
663:             tokenomics = _tokenomics;

/// @audit `_treasury`
669:             treasury = _treasury;

/// @audit `_voteWeighting`
675:             voteWeighting = _voteWeighting;

```
*Github:* [[340](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L340), [341](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L341), [342](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L342), [343](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L343), [647](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L647), [663](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L663), [669](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L669), [675](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L675)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

/// @audit `_olas`
450:         olas = _olas;

/// @audit `_treasury`
451:         treasury = _treasury;

/// @audit `_depository`
452:         depository = _depository;

/// @audit `_dispenser`
453:         dispenser = _dispenser;

/// @audit `_ve`
454:         ve = _ve;

/// @audit `_componentRegistry`
456:         componentRegistry = _componentRegistry;

/// @audit `_agentRegistry`
457:         agentRegistry = _agentRegistry;

/// @audit `_serviceRegistry`
458:         serviceRegistry = _serviceRegistry;

/// @audit `_donatorBlacklist`
459:         donatorBlacklist = _donatorBlacklist;

/// @audit `newOwner`
557:         owner = newOwner;

/// @audit `_treasury`
573:             treasury = _treasury;

/// @audit `_depository`
578:             depository = _depository;

/// @audit `_dispenser`
583:             dispenser = _dispenser;

/// @audit `_componentRegistry`
600:             componentRegistry = _componentRegistry;

/// @audit `_agentRegistry`
604:             agentRegistry = _agentRegistry;

/// @audit `_serviceRegistry`
608:             serviceRegistry = _serviceRegistry;

/// @audit `_donatorBlacklist`
622:         donatorBlacklist = _donatorBlacklist;

```
*Github:* [[450](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L450), [451](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L451), [452](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L452), [453](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L453), [454](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L454), [456](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L456), [457](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L457), [458](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L458), [459](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L459), [557](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L557), [573](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L573), [578](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L578), [583](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L583), [600](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L600), [604](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L604), [608](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L608), [622](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L622)]


```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

/// @audit `_l1ERC20Gateway`
106:         l1ERC20Gateway = _l1ERC20Gateway;

/// @audit `_outbox`
107:         outbox = _outbox;

/// @audit `_bridge`
108:         bridge = _bridge;

```
*Github:* [[106](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L106), [107](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L107), [108](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L108)]


```solidity
File: tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

/// @audit `_olas`
81:         olas = _olas;

/// @audit `_l1Dispenser`
82:         l1Dispenser = _l1Dispenser;

/// @audit `_l1TokenRelayer`
83:         l1TokenRelayer = _l1TokenRelayer;

/// @audit `_l1MessageRelayer`
84:         l1MessageRelayer = _l1MessageRelayer;

/// @audit `l2Dispenser`
196:         l2TargetDispenser = l2Dispenser;

```
*Github:* [[81](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L81), [82](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L82), [83](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L83), [84](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L84), [196](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L196)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

/// @audit `_olas`
125:         olas = _olas;

/// @audit `_stakingFactory`
126:         stakingFactory = _stakingFactory;

/// @audit `_l2MessageRelayer`
127:         l2MessageRelayer = _l2MessageRelayer;

/// @audit `_l1DepositProcessor`
128:         l1DepositProcessor = _l1DepositProcessor;

/// @audit `newOwner`
258:         owner = newOwner;

```
*Github:* [[125](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L125), [126](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L126), [127](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L127), [128](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L128), [258](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L258)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

/// @audit `_olas`
76:         olas = _olas;

/// @audit `_dispenser`
77:         dispenser = _dispenser;

/// @audit `_stakingFactory`
78:         stakingFactory = _stakingFactory;

/// @audit `_timelock`
79:         timelock = _timelock;

```
*Github:* [[76](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L76), [77](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L77), [78](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L78), [79](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L79)]


```solidity
File: tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

/// @audit `_l2TokenRelayer`
54:         l2TokenRelayer = _l2TokenRelayer;

```
*Github:* [[54](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L54)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

/// @audit `_olasL2`
91:         olasL2 = _olasL2;

```
*Github:* [[91](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L91)]


```solidity
File: tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

/// @audit `_predicate`
53:         predicate = _predicate;

```
*Github:* [[53](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L53)]


</details>


<a name="L-16"></a> 
### [L-16] Numbers downcast to `address`es may result in collisions
If a number is downcast to an `address` the upper bytes are truncated, which may mean that more than one value will map to the `address`.

*There are <b>6</b> instances of this issue:*
```solidity
File: registries/contracts/staking/StakingFactory.sol

156:         return address(uint160(uint256(hash)));

```
*Github:* [[156](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L156)]


```solidity
File: tokenomics/contracts/Dispenser.sol

424:             address stakingTargetEVM = address(uint160(uint256(stakingTarget)));

486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));

```
*Github:* [[424](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L424), [486](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L486)]


```solidity
File: tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

48:             l1AliasedDepositProcessor = address(uint160(_l1DepositProcessor) + offset);

```
*Github:* [[48](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L48)]


```solidity
File: tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

125:         address l2Dispenser = address(uint160(uint256(sourceAddress)));

```
*Github:* [[125](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L125)]


```solidity
File: tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

164:         address processor = address(uint160(uint256(sourceProcessor)));

```
*Github:* [[164](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L164)]



<a name="L-17"></a> 
### [L-17] `receive()`/`fallback()` function does not authorize requests
Having no access control on the function (e.g. `require(msg.sender == address(weth))`) means that someone may send Ether to the contract, and have no way to get anything back out, which is a loss of funds. If the concern is having to spend a small amount of gas to check the sender against an immutable address, the code should at least have a function to rescue mistakenly-sent Ether.

*There are <b>3</b> instances of this issue:*
```solidity
File: registries/contracts/staking/StakingNativeToken.sol

39:     receive() external payable {

```
*Github:* [[39](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingNativeToken.sol#L39)]


```solidity
File: registries/contracts/staking/StakingProxy.sol

40:     fallback() external payable {

```
*Github:* [[40](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingProxy.sol#L40)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

458:     receive() external payable {

```
*Github:* [[458](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L458)]



<a name="L-18"></a> 
### [L-18] Solidity version 0.8.20 or above may not work on other chains due to PUSH0
Solidity version 0.8.20 or above uses the new [Shanghai EVM](https://blog.soliditylang.org/2023/05/10/solidity-0.8.20-release-announcement/#important-note) which introduces the PUSH0 opcode. This op code may not yet be implemented on all evm-chains or Layer2s, so deployment on these chains will fail. Consider using an earlier solidity version.

<details>
<summary>
There are <b>28</b> instances (click to show):
</summary>

```solidity
File: governance/contracts/VoteWeighting.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L2)]


```solidity
File: registries/contracts/staking/StakingActivityChecker.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingActivityChecker.sol#L2)]


```solidity
File: registries/contracts/staking/StakingBase.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L2)]


```solidity
File: registries/contracts/staking/StakingFactory.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L2)]


```solidity
File: registries/contracts/staking/StakingNativeToken.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingNativeToken.sol#L2)]


```solidity
File: registries/contracts/staking/StakingProxy.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingProxy.sol#L2)]


```solidity
File: registries/contracts/staking/StakingToken.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingToken.sol#L2)]


```solidity
File: registries/contracts/staking/StakingVerifier.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L2)]


```solidity
File: registries/contracts/utils/SafeTransferLib.sol

2: pragma solidity ^0.8.23;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/utils/SafeTransferLib.sol#L2)]


```solidity
File: tokenomics/contracts/Dispenser.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L2)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L2)]


```solidity
File: tokenomics/contracts/TokenomicsConstants.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/TokenomicsConstants.sol#L2)]


```solidity
File: tokenomics/contracts/interfaces/IBridgeErrors.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/interfaces/IBridgeErrors.sol#L2)]


```solidity
File: tokenomics/contracts/interfaces/IDonatorBlacklist.sol

2: pragma solidity ^0.8.18;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L2)]


```solidity
File: tokenomics/contracts/interfaces/IErrorsTokenomics.sol

2: pragma solidity ^0.8.18;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L2)]


```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L2)]


```solidity
File: tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L2)]


```solidity
File: tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L2)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L2)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L2)]


```solidity
File: tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L2)]


```solidity
File: tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L2)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L2)]


```solidity
File: tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L2)]


```solidity
File: tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L2)]


```solidity
File: tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L2)]


```solidity
File: tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L2)]


```solidity
File: tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

2: pragma solidity ^0.8.25;

```
*Github:* [[2](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L2)]


</details>


<a name="L-19"></a> 
### [L-19] Some functions do not work correctly with fee-on-transfer tokens
Some tokens take a transfer fee (e.g. `STA`, `PAXG`), some do not currently charge a fee but may do so in the future (e.g. `USDT`, `USDC`).

Should a fee-on-transfer token be added as an asset and deposited, it could be abused, as the accounting is wrong. In the current implementation the following function calls do not work well with fee-on-transfer tokens as the amount variable is the pre-fee amount, including the fee, whereas the final balance do not include the fee anymore.

*There are <b>4</b> instances of this issue:*
```solidity
File: tokenomics/contracts/Dispenser.sol

420:             IToken(olas).transfer(depositProcessor, transferAmount);

456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]);

```
*Github:* [[420](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L420), [456](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L456)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

443:             bool success = IToken(olas).transfer(newL2TargetDispenser, amount);

```
*Github:* [[443](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L443)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

112:                 IToken(olas).transfer(timelock, refundAmount);

```
*Github:* [[112](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L112)]



<a name="L-20"></a> 
### [L-20] Some tokens may revert when large transfers are made
Tokens such as COMP or UNI will revert when an address' balance reaches `type(uint96).max`. Ensure that the calls below can be broken up into smaller batches if necessary.

*There are <b>4</b> instances of this issue:*
```solidity
File: tokenomics/contracts/Dispenser.sol

420:             IToken(olas).transfer(depositProcessor, transferAmount);

456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]);

```
*Github:* [[420](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L420), [456](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L456)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

443:             bool success = IToken(olas).transfer(newL2TargetDispenser, amount);

```
*Github:* [[443](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L443)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

112:                 IToken(olas).transfer(timelock, refundAmount);

```
*Github:* [[112](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L112)]



<a name="L-21"></a> 
### [L-21] Some tokens may revert when zero value transfers are made
Despite the fact that [EIP-20 states](https://github.com/ethereum/EIPs/blob/7500ac4fc1bbdfaf684e7ef851f798f6b667b2fe/EIPS/eip-20.md?plain=1#L116) that zero-value transfers must be accepted, some tokens, such as LEND, will revert if this is attempted, which may cause transactions that involve other tokens (such as batch operations) to fully revert. Consider skipping the transfer if the amount is zero, which will also save gas.

*There are <b>4</b> instances of this issue:*
```solidity
File: tokenomics/contracts/Dispenser.sol

420:             IToken(olas).transfer(depositProcessor, transferAmount);

456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]);

```
*Github:* [[420](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L420), [456](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L456)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

443:             bool success = IToken(olas).transfer(newL2TargetDispenser, amount);

```
*Github:* [[443](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L443)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

112:                 IToken(olas).transfer(timelock, refundAmount);

```
*Github:* [[112](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L112)]



<a name="L-22"></a> 
### [L-22] Unsafe downcast
When a type is downcast to a smaller type, the higher order bits are truncated, effectively applying a modulo to the original value. Without any other checks, this wrapping will lead to unexpected behavior and bugs.

<details>
<summary>
There are <b>65</b> instances (click to show):
</summary>

```solidity
File: registries/contracts/staking/StakingFactory.sol

/// @audit uint256 -> uint160
156:         return address(uint160(uint256(hash)));

```
*Github:* [[156](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L156)]


```solidity
File: tokenomics/contracts/Dispenser.sol

/// @audit uint256 -> uint160
424:             address stakingTargetEVM = address(uint160(uint256(stakingTarget)));

/// @audit uint256 -> uint160
486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));

```
*Github:* [[424](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L424), [486](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L486)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

/// @audit uint256 -> uint32
440:         if (uint32(_epochLen) < MIN_EPOCH_LENGTH) {

/// @audit uint256 -> uint32
445:         if (uint32(_epochLen) > MAX_EPOCH_LENGTH) {

/// @audit uint256 -> uint32
455:         epochLen = uint32(_epochLen);

/// @audit uint256 -> uint32
469:         uint256 zeroYearSecondsLeft = uint32(_timeLaunch + ONE_YEAR - block.timestamp);

/// @audit uint256 -> uint96
474:         inflationPerSecond = uint96(_inflationPerSecond);

/// @audit uint256 -> uint32
475:         timeLaunch = uint32(_timeLaunch);

/// @audit uint256 -> uint32
478:         mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);

/// @audit uint256 -> uint8
503:         tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);

/// @audit uint256 -> uint96
510:         maxBond = uint96(_maxBond);

/// @audit uint256 -> uint96
511:         effectiveBond = uint96(_maxBond);

/// @audit uint256 -> uint72
652:         if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {

/// @audit uint256 -> uint72
653:             devsPerCapital = uint72(_devsPerCapital);

/// @audit uint256 -> uint72
660:         if (uint72(_codePerDev) > MIN_PARAM_VALUE) {

/// @audit uint256 -> uint72
661:             codePerDev = uint72(_codePerDev);

/// @audit uint256 -> uint64
671:             epsilonRate = uint64(_epsilonRate);

/// @audit uint256 -> uint32
677:         if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {

/// @audit uint256 -> uint32
677:         if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {

/// @audit uint256 -> uint32
678:             nextEpochLen = uint32(_epochLen);

/// @audit uint256 -> uint96
684:         if (uint96(_veOLASThreshold) > 0) {

/// @audit uint256 -> uint96
685:             nextVeOLASThreshold = uint96(_veOLASThreshold);

/// @audit uint256 -> uint8
732:         tp.unitPoints[0].rewardUnitFraction = uint8(_rewardComponentFraction);

/// @audit uint256 -> uint8
733:         tp.unitPoints[1].rewardUnitFraction = uint8(_rewardAgentFraction);

/// @audit uint256 -> uint8
735:         tp.epochPoint.rewardTreasuryFraction = uint8(100 - _rewardComponentFraction - _rewardAgentFraction);

/// @audit uint256 -> uint8
737:         tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);

/// @audit uint256 -> uint8
738:         tp.unitPoints[0].topUpUnitFraction = uint8(_topUpComponentFraction);

/// @audit uint256 -> uint8
739:         tp.unitPoints[1].topUpUnitFraction = uint8(_topUpAgentFraction);

/// @audit uint256 -> uint8
740:         mapEpochStakingPoints[eCounter].stakingFraction = uint8(_stakingFraction);

/// @audit uint256 -> uint96
774:         stakingPoint.maxStakingIncentive = uint96(_maxStakingIncentive);

/// @audit uint256 -> uint16
775:         stakingPoint.minStakingWeight = uint16(_minStakingWeight);

/// @audit uint256 -> uint96
799:             effectiveBond = uint96(eBond);

/// @audit uint256 -> uint96
820:         effectiveBond = uint96(eBond);

/// @audit uint256 -> uint96
839:         mapEpochStakingPoints[eCounter].stakingIncentive = uint96(stakingIncentive);

/// @audit uint256 -> uint96
857:             mapUnitIncentives[unitType][unitId].reward = uint96(totalIncentives);

/// @audit uint256 -> uint96
873:             mapUnitIncentives[unitType][unitId].topUp = uint96(totalIncentives);

/// @audit uint256 -> uint96
928:                     uint96 amount = uint96(amounts[i] / numServiceUnits);

/// @audit uint256 -> uint32
935:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);

/// @audit uint256 -> uint32
943:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);

/// @audit uint256 -> uint96
1020:         mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH = uint96(donationETH);

/// @audit uint256 -> uint32
1026:         lastDonationBlockNumber = uint32(block.number);

/// @audit uint256 -> uint96
1140:             inflationPerSecond = uint96(curInflationPerSecond);

/// @audit uint256 -> uint8
1141:             currentYear = uint8(numYears);

/// @audit uint256 -> uint96
1151:         tp.epochPoint.totalTopUpsOLAS = uint96(inflationPerEpoch);

/// @audit uint256 -> uint96
1167:             effectiveBond = uint96(incentives[4]);

/// @audit uint256 -> uint32
1178:                 epochLen = uint32(curEpochLen);

/// @audit uint256 -> uint32
1220:         tp.epochPoint.endTime = uint32(block.timestamp);

/// @audit uint256 -> uint96
1238:         mapEpochStakingPoints[eCounter].stakingIncentive = uint96(incentives[8]);

/// @audit uint256 -> uint96
1258:             maxBond = uint96(curMaxBond);

/// @audit uint256 -> uint96
1265:             maxBond = uint96(curMaxBond);

/// @audit uint256 -> uint96
1271:         effectiveBond = uint96(curMaxBond);

/// @audit uint256 -> uint64
1277:             nextEpochPoint.epochPoint.idf = uint64(idf);

/// @audit uint256 -> uint32
1290:             epochCounter = uint32(eCounter + 1);

```
*Github:* [[440](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L440), [445](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L445), [455](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L455), [469](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L469), [474](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L474), [475](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L475), [478](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L478), [503](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L503), [510](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L510), [511](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L511), [652](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L652), [653](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L653), [660](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L660), [661](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L661), [671](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L671), [677](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L677), [677](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L677), [678](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L678), [684](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L684), [685](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L685), [732](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L732), [733](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L733), [735](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L735), [737](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L737), [738](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L738), [739](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L739), [740](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L740), [774](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L774), [775](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L775), [799](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L799), [820](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L820), [839](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L839), [857](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L857), [873](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L873), [928](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L928), [935](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L935), [943](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L943), [1020](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1020), [1026](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1026), [1140](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1140), [1141](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1141), [1151](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1151), [1167](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1167), [1178](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1178), [1220](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1220), [1238](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1238), [1258](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1258), [1265](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1265), [1271](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1271), [1277](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1277), [1290](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1290)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

/// @audit uint256 -> uint32
115:                 uint32(TOKEN_GAS_LIMIT), "");

/// @audit uint256 -> uint32
140:         IBridge(l1MessageRelayer).sendMessage{value: cost}(l2TargetDispenser, data, uint32(gasLimitMessage));

```
*Github:* [[115](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L115), [140](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L140)]


```solidity
File: tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

/// @audit uint256 -> uint32
89:         IBridge(l2MessageRelayer).sendMessage{value: cost}(l1DepositProcessor, data, uint32(gasLimitMessage));

```
*Github:* [[89](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L89)]


```solidity
File: tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

/// @audit uint256 -> uint16
96:         sequence = sendTokenWithPayloadToEvm(uint16(wormholeTargetChainId), l2TargetDispenser, data, 0,

/// @audit uint256 -> uint16
97:             gasLimitMessage, olas, transferAmount, uint16(l2TargetChainId), refundAccount);

/// @audit uint256 -> uint160
125:         address l2Dispenser = address(uint160(uint256(sourceAddress)));

/// @audit uint256 -> uint16
133:         setRegisteredSender(uint16(wormholeTargetChainId), bytes32(uint256(uint160(l2Dispenser))));

```
*Github:* [[96](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L96), [97](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L97), [125](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L125), [133](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L133)]


```solidity
File: tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

/// @audit uint256 -> uint16
112:         (uint256 cost, ) = IBridge(l2MessageRelayer).quoteEVMDeliveryPrice(uint16(l1SourceChainId), 0, gasLimitMessage);

/// @audit uint256 -> uint16
120:         uint64 sequence = IBridge(l2MessageRelayer).sendPayloadToEvm{value: cost}(uint16(l1SourceChainId),

/// @audit uint256 -> uint16
121:             l1DepositProcessor, abi.encode(amount), 0, gasLimitMessage, uint16(l1SourceChainId), refundAccount);

/// @audit uint256 -> uint160
164:         address processor = address(uint160(uint256(sourceProcessor)));

```
*Github:* [[112](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L112), [120](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L120), [121](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L121), [164](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L164)]


</details>


<a name="L-23"></a> 
### [L-23] Unsafe addition/multiplication in `unchecked` block
These additions/multiplications may silently overflow because they're in unchecked blocks with no preceding value checks, which may lead to unexpected results.

*There is one instance of this issue:*
```solidity
File: tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

48:             l1AliasedDepositProcessor = address(uint160(_l1DepositProcessor) + offset);

```
*Github:* [[48](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L48)]



<a name="L-24"></a> 
### [L-24] Consider using `ERC721A` instead of `ERC721`
[ERC721A](https://www.erc721a.org/) is an implementation of IERC721 with significant gas savings for minting multiple NFTs in a single transaction.

*There is one instance of this issue:*
```solidity
File: registries/contracts/staking/StakingBase.sol

168: abstract contract StakingBase is ERC721TokenReceiver {

```
*Github:* [[168](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingBase.sol#L168)]



<a name="L-25"></a> 
### [L-25] Using zero as a parameter
Taking `0` as a valid argument in Solidity without checks can lead to severe security issues. A historical example is the infamous `0x0` address bug where numerous tokens were lost. This happens because 0 can be interpreted as an uninitialized `address`, leading to transfers to the 0x0 address, effectively burning tokens. Moreover, `0` as a denominator in division operations would cause a runtime exception. It's also often indicative of a logical error in the caller's code. It's important to always validate input and handle edge cases like `0` appropriately. Use `require()` statements to enforce conditions and provide clear error messages to facilitate debugging and safer code.

*There are <b>9</b> instances of this issue:*
```solidity
File: governance/contracts/VoteWeighting.sol

216:         setNominees.push(Nominee(0, 0));

218:         setRemovedNominees.push(Nominee(0, 0));

```
*Github:* [[216](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L216), [218](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L218)]


```solidity
File: registries/contracts/staking/StakingNativeToken.sol

35:             revert TransferFailed(address(0), address(this), to, amount);

```
*Github:* [[35](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingNativeToken.sol#L35)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

473:         uint256 _inflationPerSecond = getInflationForYear(0) / zeroYearSecondsLeft;

```
*Github:* [[473](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L473)]


```solidity
File: tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

398:             revert TransferFailed(address(0), address(this), msg.sender, amount);

461:             revert TransferFailed(address(0), msg.sender, address(this), msg.value);

```
*Github:* [[398](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L398), [461](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L461)]


```solidity
File: tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

91:         emit MessagePosted(0, msg.sender, l1DepositProcessor, amount);

```
*Github:* [[91](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L91)]


```solidity
File: tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

41:         emit MessagePosted(0, msg.sender, l1DepositProcessor, amount);

```
*Github:* [[41](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L41)]


```solidity
File: tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

96:         sequence = sendTokenWithPayloadToEvm(uint16(wormholeTargetChainId), l2TargetDispenser, data, 0,

```
*Github:* [[96](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L96)]



<a name="L-26"></a> 
### [L-26] Missing zero address check in initializer

*There is one instance of this issue:*
```solidity
File: tokenomics/contracts/Tokenomics.sol

/// @audit missing zero check for `_donatorBlacklist`
409:     function initializeTokenomics(
             address _olas,
             address _treasury,
             address _depository,
             address _dispenser,
             address _ve,
             uint256 _epochLen,
             address _componentRegistry,
             address _agentRegistry,
             address _serviceRegistry,
             address _donatorBlacklist
         ) external {

```
*Github:* [[409](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L409)]



<a name="L-27"></a> 
### [L-27] `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).
If all arguments are strings and or bytes, `bytes.concat()` should be used instead

*There are <b>2</b> instances of this issue:*
```solidity
File: registries/contracts/staking/StakingFactory.sol

143:         bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));

201:         bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));

```
*Github:* [[143](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L143), [201](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L201)]



<a name="L-28"></a> 
### [L-28] Initializers could be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment

*There are <b>2</b> instances of this issue:*
```solidity
File: registries/contracts/staking/StakingNativeToken.sol

20:     function initialize(StakingParams memory _stakingParams) external {

```
*Github:* [[20](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingNativeToken.sol#L20)]


```solidity
File: registries/contracts/staking/StakingToken.sol

54:     function initialize(

```
*Github:* [[54](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingToken.sol#L54)]



<a name="L-29"></a> 
### [L-29] For loops in public or external functions should be avoided due to high gas costs and possible DOS
In Solidity, for loops can potentially cause Denial of Service (DoS) attacks if not handled carefully. DoS attacks can occur when an attacker intentionally exploits the gas cost of a function, causing it to run out of gas or making it too expensive for other users to call. Below are some scenarios where for loops can lead to DoS attacks: Nested for loops can become exceptionally gas expensive and should be used sparingly.

*There are <b>6</b> instances of this issue:*
```solidity
File: governance/contracts/VoteWeighting.sol

563:     function voteForNomineeWeightsBatch(
             bytes32[] memory accounts,
             uint256[] memory chainIds,
             uint256[] memory weights
         ) external {
             if (accounts.length != chainIds.length || accounts.length != weights.length) {
                 revert WrongArrayLength(accounts.length, weights.length);
             }
     
             // Traverse all accounts and weights
             for (uint256 i = 0; i < accounts.length; ++i) {

779:     function getNextAllowedVotingTimes(
             bytes32[] memory accounts,
             uint256[] memory chainIds,
             address[] memory voters
         ) external view returns (uint256[] memory nextAllowedVotingTimes) {
             // Check array lengths
             if (accounts.length != chainIds.length || accounts.length != voters.length) {
                 revert WrongArrayLength(accounts.length, chainIds.length);
             }
     
             // Allocate the times array
             nextAllowedVotingTimes = new uint256[](accounts.length);
     
             // Traverse nominees and get next available voting times
             for (uint256 i = 0; i < accounts.length; ++i) {

```
*Github:* [[563](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L563), [779](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/governance/contracts/VoteWeighting.sol#L779)]


```solidity
File: registries/contracts/staking/StakingVerifier.sol

131:     function setImplementationsStatuses(
             address[] memory implementations,
             bool[] memory statuses,
             bool setCheck
         ) external {
             // Check the contract ownership
             if (owner != msg.sender) {
                 revert OwnerOnly(owner, msg.sender);
             }
     
             // Check for the array length and that they are not empty
             if (implementations.length == 0 || implementations.length != statuses.length) {
                 revert WrongArrayLength(implementations.length, statuses.length);
             }
     
             // Set the implementations address check requirement
             implementationsCheck = setCheck;
     
             // Set implementations whitelisting status
             for (uint256 i = 0; i < implementations.length; ++i) {

```
*Github:* [[131](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingVerifier.sol#L131)]


```solidity
File: tokenomics/contracts/Dispenser.sol

705:     function setDepositProcessorChainIds(address[] memory depositProcessors, uint256[] memory chainIds) external {
             // Check for the ownership
             if (msg.sender != owner) {
                 revert OwnerOnly(msg.sender, owner);
             }
     
             // Check for array length correctness
             if (depositProcessors.length == 0 || depositProcessors.length != chainIds.length) {
                 revert WrongArrayLength(depositProcessors.length, chainIds.length);
             }
     
             // Link L1 and L2 bridge mediators, set L2 chain Ids
             for (uint256 i = 0; i < chainIds.length; ++i) {

```
*Github:* [[705](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L705)]


```solidity
File: tokenomics/contracts/Tokenomics.sol

1308:     function accountOwnerIncentives(
              address account,
              uint256[] memory unitTypes,
              uint256[] memory unitIds
          ) external returns (uint256 reward, uint256 topUp) {
              // Check for the dispenser access
              if (dispenser != msg.sender) {
                  revert ManagerOnly(msg.sender, dispenser);
              }
      
              // Check array lengths
              if (unitTypes.length != unitIds.length) {
                  revert WrongArrayLength(unitTypes.length, unitIds.length);
              }
      
              // Component / agent registry addresses
              address[] memory registries = new address[](2);
              (registries[0], registries[1]) = (componentRegistry, agentRegistry);
      
              // Component / agent total supply
              uint256[] memory registriesSupply = new uint256[](2);
              for (uint256 i = 0; i < 2; ++i) {
                  registriesSupply[i] = IToken(registries[i]).totalSupply();
              }
      
              // Check the input data
              uint256[] memory lastIds = new uint256[](2);
              for (uint256 i = 0; i < unitIds.length; ++i) {
                  // Check for the unit type to be component / agent only
                  if (unitTypes[i] > 1) {
                      revert Overflow(unitTypes[i], 1);
                  }
      
                  // Check that the unit Ids are in ascending order, not repeating, and no bigger than registries total supply
                  if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
                      revert WrongUnitId(unitIds[i], unitTypes[i]);
                  }
                  lastIds[unitTypes[i]] = unitIds[i];
      
                  // Check the component / agent Id ownership
                  address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
                  if (unitOwner != account) {
                      revert OwnerOnly(unitOwner, account);
                  }
              }
      
              // Get the current epoch counter
              uint256 curEpoch = epochCounter;
      
              for (uint256 i = 0; i < unitIds.length; ++i) {

1385:     function getOwnerIncentives(
              address account,
              uint256[] memory unitTypes,
              uint256[] memory unitIds
          ) external view returns (uint256 reward, uint256 topUp) {
              // Check array lengths
              if (unitTypes.length != unitIds.length) {
                  revert WrongArrayLength(unitTypes.length, unitIds.length);
              }
      
              // Component / agent registry addresses
              address[] memory registries = new address[](2);
              (registries[0], registries[1]) = (componentRegistry, agentRegistry);
      
              // Component / agent total supply
              uint256[] memory registriesSupply = new uint256[](2);
              for (uint256 i = 0; i < 2; ++i) {
                  registriesSupply[i] = IToken(registries[i]).totalSupply();
              }
      
              // Check the input data
              uint256[] memory lastIds = new uint256[](2);
              for (uint256 i = 0; i < unitIds.length; ++i) {
                  // Check for the unit type to be component / agent only
                  if (unitTypes[i] > 1) {
                      revert Overflow(unitTypes[i], 1);
                  }
      
                  // Check that the unit Ids are in ascending order, not repeating, and no bigger than registries total supply
                  if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
                      revert WrongUnitId(unitIds[i], unitTypes[i]);
                  }
                  lastIds[unitTypes[i]] = unitIds[i];
      
                  // Check the component / agent Id ownership
                  address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
                  if (unitOwner != account) {
                      revert OwnerOnly(unitOwner, account);
                  }
              }
      
              // Get the current epoch counter
              uint256 curEpoch = epochCounter;
      
              for (uint256 i = 0; i < unitIds.length; ++i) {

```
*Github:* [[1308](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1308), [1385](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Tokenomics.sol#L1385)]



<a name="L-30"></a> 
### [L-30] Function with two (or more) array parameters missing a length check
In Solidity, if two array parameters are used within a function and one of their lengths is used as the for-loop range, it's essential to have a length check. If the arrays are not the same length, you could experience out-of-bounds errors or unintended behavior. This could happen if the function tries to access an index that doesn't exist in the shorter array.

Resolution: Always validate that the lengths of both arrays are the same before entering the loop. Add a require statement at the start of the function to check that both arrays are of equal length. This helps maintain the integrity of the function and prevents potential errors due to differing array lengths. This requirement ensures the function fails early if the arrays don't match, rather than failing unpredictably or silently during execution.

*There are <b>3</b> instances of this issue:*
```solidity
File: tokenomics/contracts/Dispenser.sol

441:     function _distributeStakingIncentivesBatch(
             uint256[] memory chainIds,
             bytes32[][] memory stakingTargets,
             uint256[][] memory stakingIncentives,
             bytes[] memory bridgePayloads,
             uint256[] memory transferAmounts,
             uint256[] memory valueAmounts
         ) internal {
             // Traverse all staking targets
             for (uint256 i = 0; i < chainIds.length; ++i) {

573:     function _calculateStakingIncentivesBatch(
             uint256 numClaimedEpochs,
             uint256[] memory chainIds,
             bytes32[][] memory stakingTargets
         ) internal returns (
             uint256[] memory totalAmounts,
             uint256[][] memory stakingIncentives,
             uint256[] memory transferAmounts
         ) {
             // Total staking incentives
             // 0: Staking incentive across all the targets to send as a deposit
             // 1: Actual OLAS transfer amount across all the targets
             // 2: Staking incentive to return back to staking inflation
             totalAmounts = new uint256[](3);
     
             // Allocate the array of staking and transfer amounts
             stakingIncentives = new uint256[][](chainIds.length);
             transferAmounts = new uint256[](chainIds.length);
     
             // Traverse all chains
             for (uint256 i = 0; i < chainIds.length; ++i) {

```
*Github:* [[441](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L441), [573](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/Dispenser.sol#L573)]


```solidity
File: tokenomics/contracts/staking/EthereumDepositProcessor.sol

86:     function _deposit(address[] memory targets, uint256[] memory stakingIncentives) internal {
            // Reentrancy guard
            if (_locked > 1) {
                revert ReentrancyGuard();
            }
            _locked = 2;
    
            // Traverse all the targets
            for (uint256 i = 0; i < targets.length; ++i) {

```
*Github:* [[86](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L86)]



<a name="L-31"></a> 
### [L-31] The call `abi.encodeWithSelector` is not type safe
In Solidity, `abi.encodeWithSelector` is a function used for encoding data along with a function selector, but it is not type-safe. This means it does not enforce type checking at compile time, potentially leading to errors if arguments do not match the expected types. Starting from version 0.8.13, Solidity introduced `abi.encodeCall`, which offers a safer alternative. `abi.encodeCall` ensures type safety by performing a full type check, aligning the types of the arguments with the function signature. This reduces the risk of bugs caused by typographical errors or mismatched types. Using `abi.encodeCall` enhances the reliability and security of the code by ensuring that the encoded data strictly conforms to the specified types, making it a preferable choice in Solidity versions 0.8.13 and above.

*There are <b>6</b> instances of this issue:*
```solidity
File: tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

187:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));

```
*Github:* [[187](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L187)]


```solidity
File: tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

55:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));

```
*Github:* [[55](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L55)]


```solidity
File: tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

73:             bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));

```
*Github:* [[73](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L73)]


```solidity
File: tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

77:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));

```
*Github:* [[77](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L77)]


```solidity
File: tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

136:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));

```
*Github:* [[136](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L136)]


```solidity
File: tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

85:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));

```
*Github:* [[85](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L85)]



<a name="L-32"></a> 
### [L-32] int/uint passed into abi.encodePacked without casting to a string
Not casting an integer as a string before passing it into abi.encode can result in unintended behaviour. lets say '1' being encoded as '1' it will be encoded as char(1) which is the 'start of heading' control character or id of 60 would be encoded as '<'. This is may not be intended. To rectify this, simply cast the Id as a string with string(id) or ideally use solmate's libString library (toString)

*There are <b>6</b> instances of this issue:*
```solidity
File: registries/contracts/staking/StakingFactory.sol

143:         bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));

143:         bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));

146:         bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,

201:         bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));

201:         bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));

203:         bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,

```
*Github:* [[143](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L143), [143](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L143), [146](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L146), [201](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L201), [201](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L201), [203](https://github.com/code-423n4/2024-05-olas/blob/e2a8bc31d2769bfb578a06cc64919ad369a82c08/registries/contracts/staking/StakingFactory.sol#L203)]



