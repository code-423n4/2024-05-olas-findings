### `block.number` means different things on different L2s
On Optimism, `block.number` is the L2 block number, but on Arbitrum, it's the L1 block number, and `ArbSys(address(100)).arbBlockNumber()` must be used. Furthermore, L2 block numbers often occur much more frequently than L1 block numbers (any may even occur on a per-transaction basis), so using block numbers for timing results in inconsistencies, especially when voting is involved across multiple chains. As of version 4.9, OpenZeppelin has [modified](https://blog.openzeppelin.com/introducing-openzeppelin-contracts-v4.9#governor) their governor code to use a clock rather than block numbers, to avoid these sorts of issues, but this still requires that the project [implement](https://docs.openzeppelin.com/contracts/4.x/governance#token_2) a [clock](https://eips.ethereum.org/EIPS/eip-6372) for each L2.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

1026:        lastDonationBlockNumber = uint32(block.number);	// @audit-issue

1092:        if (lastDonationBlockNumber == block.number) {	// @audit-issue
```
[1026](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1026-L1026), [1092](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1092-L1092), 


#### Recommendation

Adopt a consistent timekeeping mechanism across L1 and L2 solutions when using `block.number` or similar timing mechanisms in your Solidity contracts. Consider using a standard clock or timestamp-based system, especially for functionalities like governance that require consistent timing across chains. For contracts on specific L2 solutions, ensure you're using the correct method to obtain block numbers consistent with the intended behavior. Keep abreast of standards and best practices, such as those suggested by OpenZeppelin, and implement appropriate solutions like EIP-6372 to provide a consistent and reliable timing mechanism across different blockchain environments.
### Consider implementing two-step procedure for updating protocol addresses
Implementing a two-step procedure for updating protocol addresses adds an extra layer of security. In such a system, the first step initiates the change, and the second step, after a predefined delay, confirms and finalizes it. This delay allows stakeholders or monitoring tools to observe and react to unintended or malicious changes. If an unauthorized change is detected, corrective actions can be taken before the change is finalized. To achieve this, introduce a "proposed address" state variable and a "delay period". Upon an update request, set the "proposed address". After the delay, if not contested, the main protocol address can be updated.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

450:        olas = _olas;	// @audit-issue

451:        treasury = _treasury;	// @audit-issue

452:        depository = _depository;	// @audit-issue

453:        dispenser = _dispenser;	// @audit-issue

454:        ve = _ve;	// @audit-issue

456:        componentRegistry = _componentRegistry;	// @audit-issue

457:        agentRegistry = _agentRegistry;	// @audit-issue

458:        serviceRegistry = _serviceRegistry;	// @audit-issue

459:        donatorBlacklist = _donatorBlacklist;	// @audit-issue

557:        owner = newOwner;	// @audit-issue

573:            treasury = _treasury;	// @audit-issue

578:            depository = _depository;	// @audit-issue

583:            dispenser = _dispenser;	// @audit-issue

600:            componentRegistry = _componentRegistry;	// @audit-issue

604:            agentRegistry = _agentRegistry;	// @audit-issue

608:            serviceRegistry = _serviceRegistry;	// @audit-issue

622:        donatorBlacklist = _donatorBlacklist;	// @audit-issue
```
[450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L450-L450), [451](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L451-L451), [452](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L452-L452), [453](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L453-L453), [454](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L454-L454), [456](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L456-L456), [457](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L457-L457), [458](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L458-L458), [459](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L459-L459), [557](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L557-L557), [573](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L573-L573), [578](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L578-L578), [583](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L583-L583), [600](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L600-L600), [604](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L604-L604), [608](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L608-L608), [622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L622-L622), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

647:        owner = newOwner;	// @audit-issue

663:            tokenomics = _tokenomics;	// @audit-issue

669:            treasury = _treasury;	// @audit-issue

675:            voteWeighting = _voteWeighting;	// @audit-issue
```
[647](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L647-L647), [663](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L663-L663), [669](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L669-L669), [675](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L675-L675), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

196:        l2TargetDispenser = l2Dispenser;	// @audit-issue
```
[196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L196-L196), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

71:        fxRootTunnel = l1Processor;	// @audit-issue
```
[71](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L71-L71), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

258:        owner = newOwner;	// @audit-issue
```
[258](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L258-L258), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

110:        fxChildTunnel = l2Dispenser;	// @audit-issue
```
[110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L110-L110), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

107:        owner = newOwner;	// @audit-issue
```
[107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L107-L107), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

121:        owner = newOwner;	// @audit-issue

133:        verifier = newVerifier;	// @audit-issue
```
[121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L121-L121), [133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L133-L133), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

379:        owner = newOwner;	// @audit-issue

392:        dispenser = newDispenser;	// @audit-issue
```
[379](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L379-L379), [392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L392-L392), 


#### Recommendation

Introduce two state variables in your contract: one to hold the proposed new address (`address public proposedAddress`) and another to timestamp the proposal (`uint public addressChangeInitiated`). Implement two functions: one to propose a new address, recording the current timestamp, and another to finalize the address change after the delay period has elapsed.

### Centralization risk for privileged functions
Contracts with privileged functions need owner/admin to be trusted not to perform malicious updates or drain funds. This may also cause a single point failure.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

526:    function changeTokenomicsImplementation(address implementation) external {
527:        // Check for the contract ownership
528:        if (msg.sender != owner) {	// @audit-issue
529:            revert OwnerOnly(msg.sender, owner);
530:        }

546:    function changeOwner(address newOwner) external {
547:        // Check for the contract ownership
548:        if (msg.sender != owner) {	// @audit-issue
549:            revert OwnerOnly(msg.sender, owner);
550:        }

565:    function changeManagers(address _treasury, address _depository, address _dispenser) external {
566:        // Check for the contract ownership
567:        if (msg.sender != owner) {	// @audit-issue
568:            revert OwnerOnly(msg.sender, owner);
569:        }

592:    function changeRegistries(address _componentRegistry, address _agentRegistry, address _serviceRegistry) external {
593:        // Check for the contract ownership
594:        if (msg.sender != owner) {	// @audit-issue
595:            revert OwnerOnly(msg.sender, owner);
596:        }

616:    function changeDonatorBlacklist(address _donatorBlacklist) external {
617:        // Check for the contract ownership
618:        if (msg.sender != owner) {	// @audit-issue
619:            revert OwnerOnly(msg.sender, owner);
620:        }

639:    function changeTokenomicsParameters(
640:        uint256 _devsPerCapital,
641:        uint256 _codePerDev,
642:        uint256 _epsilonRate,
643:        uint256 _epochLen,
644:        uint256 _veOLASThreshold
645:    ) external {
646:        // Check for the contract ownership
647:        if (msg.sender != owner) {	// @audit-issue
648:            revert OwnerOnly(msg.sender, owner);
649:        }

703:    function changeIncentiveFractions(
704:        uint256 _rewardComponentFraction,
705:        uint256 _rewardAgentFraction,
706:        uint256 _maxBondFraction,
707:        uint256 _topUpComponentFraction,
708:        uint256 _topUpAgentFraction,
709:        uint256 _stakingFraction
710:    ) external {
711:        // Check for the contract ownership
712:        if (msg.sender != owner) {	// @audit-issue
713:            revert OwnerOnly(msg.sender, owner);
714:        }

751:    function changeStakingParams(uint256 _maxStakingIncentive, uint256 _minStakingWeight) external {
752:        // Check for the contract ownership
753:        if (msg.sender != owner) {	// @audit-issue
754:            revert OwnerOnly(msg.sender, owner);
755:        }
```
[528](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L526-L530), [548](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L546-L550), [567](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L565-L569), [594](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L592-L596), [618](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L616-L620), [647](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L639-L649), [712](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L703-L714), [753](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L751-L755), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

636:    function changeOwner(address newOwner) external {
637:        // Check for the contract ownership
638:        if (msg.sender != owner) {	// @audit-issue
639:            revert OwnerOnly(msg.sender, owner);
640:        }

655:    function changeManagers(address _tokenomics, address _treasury, address _voteWeighting) external {
656:        // Check for the contract ownership
657:        if (msg.sender != owner) {	// @audit-issue
658:            revert OwnerOnly(msg.sender, owner);
659:        }

683:    function changeStakingParams(uint256 _maxNumClaimingEpochs, uint256 _maxNumStakingTargets) external {
684:        // Check for the contract ownership
685:        if (msg.sender != owner) {	// @audit-issue
686:            revert OwnerOnly(msg.sender, owner);
687:        }

705:    function setDepositProcessorChainIds(address[] memory depositProcessors, uint256[] memory chainIds) external {
706:        // Check for the ownership
707:        if (msg.sender != owner) {	// @audit-issue
708:            revert OwnerOnly(msg.sender, owner);
709:        }

1199:    function syncWithheldAmountMaintenance(uint256 chainId, uint256 amount) external {
1200:        // Check the contract ownership
1201:        if (msg.sender != owner) {	// @audit-issue
1202:            revert OwnerOnly(msg.sender, owner);
1203:        }

1241:    function setPauseState(Pause pauseState) external {
1242:        // Check the contract ownership
1243:        if (msg.sender != owner) {	// @audit-issue
1244:            revert OwnerOnly(msg.sender, owner);
1245:        }
```
[638](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L636-L640), [657](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L655-L659), [685](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L683-L687), [707](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L705-L709), [1201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1199-L1203), [1243](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1241-L1245), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

186:    function _setL2TargetDispenser(address l2Dispenser) internal {
187:        // Check the contract ownership
188:        if (msg.sender != owner) {	// @audit-issue
189:            revert OwnerOnly(owner, msg.sender);
190:        }
```
[188](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L186-L190), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

59:    function setFxRootTunnel(address l1Processor) external override {
60:        // Check for the ownership
61:        if (msg.sender != owner) {	// @audit-issue
62:            revert OwnerOnly(msg.sender, owner);
63:        }
```
[61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L59-L63), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

247:    function changeOwner(address newOwner) external {
248:        // Check for the contract ownership
249:        if (msg.sender != owner) {	// @audit-issue
250:            revert OwnerOnly(msg.sender, owner);
251:        }

311:    function processDataMaintenance(bytes memory data) external {
312:        // Check for the contract ownership
313:        if (msg.sender != owner) {	// @audit-issue
314:            revert OwnerOnly(msg.sender, owner);
315:        }

353:    function pause() external {
354:        // Check for the contract ownership
355:        if (msg.sender != owner) {	// @audit-issue
356:            revert OwnerOnly(msg.sender, owner);
357:        }

364:    function unpause() external {
365:        // Check for the contract ownership
366:        if (msg.sender != owner) {	// @audit-issue
367:            revert OwnerOnly(msg.sender, owner);
368:        }

377:    function drain() external returns (uint256 amount) {
378:        // Reentrancy guard
379:        if (_locked > 1) {
380:            revert ReentrancyGuard();
381:        }
382:        _locked = 2;
383:
384:        // Check for the owner address
385:        if (msg.sender != owner) {	// @audit-issue
386:            revert OwnerOnly(msg.sender, owner);
387:        }

412:    function migrate(address newL2TargetDispenser) external {
413:        // Reentrancy guard
414:        if (_locked > 1) {
415:            revert ReentrancyGuard();
416:        }
417:        _locked = 2;
418:
419:        // Check for the owner address
420:        if (msg.sender != owner) {	// @audit-issue
421:            revert OwnerOnly(msg.sender, owner);
422:        }
```
[249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L247-L251), [313](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L311-L315), [355](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L353-L357), [366](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L364-L368), [385](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L377-L387), [420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L412-L422), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

98:    function setFxChildTunnel(address l2Dispenser) public override {
99:        // Check for the ownership
100:        if (msg.sender != owner) {	// @audit-issue
101:            revert OwnerOnly(msg.sender, owner);
102:        }
```
[100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L98-L102), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

96:    function changeOwner(address newOwner) external {
97:        // Check for the ownership
98:        if (msg.sender != owner) {	// @audit-issue
99:            revert OwnerOnly(msg.sender, owner);
100:        }

113:    function setImplementationsCheck(bool setCheck) external {
114:        // Check the contract ownership
115:        if (owner != msg.sender) {	// @audit-issue
116:            revert OwnerOnly(owner, msg.sender);
117:        }

131:    function setImplementationsStatuses(
132:        address[] memory implementations,
133:        bool[] memory statuses,
134:        bool setCheck
135:    ) external {
136:        // Check the contract ownership
137:        if (owner != msg.sender) {	// @audit-issue
138:            revert OwnerOnly(owner, msg.sender);
139:        }

229:    function changeStakingLimits(
230:        uint256 _rewardsPerSecondLimit,
231:        uint256 _timeForEmissionsLimit,
232:        uint256 _numServicesLimit
233:    ) external {
234:        // Check the contract ownership
235:        if (owner != msg.sender) {	// @audit-issue
236:            revert OwnerOnly(owner, msg.sender);
237:        }
```
[98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L96-L100), [115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L113-L117), [137](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L131-L139), [235](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L229-L237), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

110:    function changeOwner(address newOwner) external {
111:        // Check for the ownership
112:        if (msg.sender != owner) {	// @audit-issue
113:            revert OwnerOnly(msg.sender, owner);
114:        }

127:    function changeVerifier(address newVerifier) external {
128:        // Check for the ownership
129:        if (msg.sender != owner) {	// @audit-issue
130:            revert OwnerOnly(msg.sender, owner);
131:        }
```
[112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L110-L114), [129](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L127-L131), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

368:    function changeOwner(address newOwner) external {
369:        // Check for the contract ownership
370:        if (msg.sender != owner) {	// @audit-issue
371:            revert OwnerOnly(msg.sender, owner);
372:        }

386:    function changeDispenser(address newDispenser) external {
387:        // Check for the contract ownership
388:        if (msg.sender != owner) {	// @audit-issue
389:            revert OwnerOnly(msg.sender, owner);
390:        }

586:    function removeNominee(bytes32 account, uint256 chainId) external {
587:        // Check for the contract ownership
588:        if (msg.sender != owner) {	// @audit-issue
589:            revert OwnerOnly(owner, msg.sender);
590:        }
```
[370](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L368-L372), [388](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L386-L390), [588](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L586-L590), 


#### Recommendation

To mitigate centralization risks associated with privileged functions, consider implementing a multi-signature or decentralized governance mechanism. Instead of relying solely on a single owner/administrator, distribute control and decision-making authority among multiple parties or stakeholders. This approach enhances security, reduces the risk of malicious actions by a single entity, and prevents single points of failure. Explore governance solutions and smart contract frameworks that support decentralized control and decision-making to enhance the trustworthiness and resilience of your contract.

### Code does not follow the best practice of check-effects-interactions pattern
Code should follow the best-practice of [CEI](https://blockchain-academy.hs-mittweida.de/courses/solidity-coding-beginners-to-intermediate/lessons/solidity-11-coding-patterns/topic/checks-effects-interactions/), where state variables are updated before any external calls are made. Doing so prevents a large class of reentrancy bugs.

```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162:
163:            uint256 limitAmount;
164:            // If the function call was successful, check the return value
165:            if (success && returnData.length == 32) {
166:                limitAmount = abi.decode(returnData, (uint256));
167:            }
168:
169:            // If the limit amount is zero, withhold OLAS amount and continue
170:            if (limitAmount == 0) {
171:                // Withhold OLAS for further usage
172:                localWithheldAmount += amount;
173:                emit AmountWithheld(target, amount);
174:
175:                // Proceed to the next target
176:                continue;
177:            }
178:
179:            // Check the amount limit and adjust, if necessary
180:            if (amount > limitAmount) {
181:                uint256 targetWithheldAmount = amount - limitAmount;
182:                localWithheldAmount += targetWithheldAmount;
183:                amount = limitAmount;
184:
185:                emit AmountWithheld(target, targetWithheldAmount);
186:            }
187:
188:            // Check the OLAS balance and the contract being unpaused
189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {
190:                // Approve and transfer OLAS to the service staking target
191:                IToken(olas).approve(target, amount);
192:                IStaking(target).deposit(amount);
193:
194:                emit StakingTargetDeposited(target, amount);
195:            } else {
196:                // Hash of target + amount + batchNonce
197:                bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
198:                // Queue the hash for further redeem
199:                stakingQueueingNonces[queueHash] = true;	// @audit-issue

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162:
163:            uint256 limitAmount;
164:            // If the function call was successful, check the return value
165:            if (success && returnData.length == 32) {
166:                limitAmount = abi.decode(returnData, (uint256));
167:            }
168:
169:            // If the limit amount is zero, withhold OLAS amount and continue
170:            if (limitAmount == 0) {
171:                // Withhold OLAS for further usage
172:                localWithheldAmount += amount;
173:                emit AmountWithheld(target, amount);
174:
175:                // Proceed to the next target
176:                continue;
177:            }
178:
179:            // Check the amount limit and adjust, if necessary
180:            if (amount > limitAmount) {
181:                uint256 targetWithheldAmount = amount - limitAmount;
182:                localWithheldAmount += targetWithheldAmount;
183:                amount = limitAmount;
184:
185:                emit AmountWithheld(target, targetWithheldAmount);
186:            }
187:
188:            // Check the OLAS balance and the contract being unpaused
189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {
190:                // Approve and transfer OLAS to the service staking target
191:                IToken(olas).approve(target, amount);
192:                IStaking(target).deposit(amount);
193:
194:                emit StakingTargetDeposited(target, amount);
195:            } else {
196:                // Hash of target + amount + batchNonce
197:                bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
198:                // Queue the hash for further redeem
199:                stakingQueueingNonces[queueHash] = true;
200:
201:                emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);
202:            }
203:        }
204:        // Increase the staking batch nonce
205:        stakingBatchNonce = batchNonce + 1;	// @audit-issue

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162:
163:            uint256 limitAmount;
164:            // If the function call was successful, check the return value
165:            if (success && returnData.length == 32) {
166:                limitAmount = abi.decode(returnData, (uint256));
167:            }
168:
169:            // If the limit amount is zero, withhold OLAS amount and continue
170:            if (limitAmount == 0) {
171:                // Withhold OLAS for further usage
172:                localWithheldAmount += amount;
173:                emit AmountWithheld(target, amount);
174:
175:                // Proceed to the next target
176:                continue;
177:            }
178:
179:            // Check the amount limit and adjust, if necessary
180:            if (amount > limitAmount) {
181:                uint256 targetWithheldAmount = amount - limitAmount;
182:                localWithheldAmount += targetWithheldAmount;
183:                amount = limitAmount;
184:
185:                emit AmountWithheld(target, targetWithheldAmount);
186:            }
187:
188:            // Check the OLAS balance and the contract being unpaused
189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {
190:                // Approve and transfer OLAS to the service staking target
191:                IToken(olas).approve(target, amount);
192:                IStaking(target).deposit(amount);
193:
194:                emit StakingTargetDeposited(target, amount);
195:            } else {
196:                // Hash of target + amount + batchNonce
197:                bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
198:                // Queue the hash for further redeem
199:                stakingQueueingNonces[queueHash] = true;
200:
201:                emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);
202:            }
203:        }
204:        // Increase the staking batch nonce
205:        stakingBatchNonce = batchNonce + 1;
206:
207:        // Adjust withheld amount, if at least one target has not passed the validity check
208:        if (localWithheldAmount > 0) {
209:            withheldAmount += localWithheldAmount;	// @audit-issue

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162:
163:            uint256 limitAmount;
164:            // If the function call was successful, check the return value
165:            if (success && returnData.length == 32) {
166:                limitAmount = abi.decode(returnData, (uint256));
167:            }
168:
169:            // If the limit amount is zero, withhold OLAS amount and continue
170:            if (limitAmount == 0) {
171:                // Withhold OLAS for further usage
172:                localWithheldAmount += amount;
173:                emit AmountWithheld(target, amount);
174:
175:                // Proceed to the next target
176:                continue;
177:            }
178:
179:            // Check the amount limit and adjust, if necessary
180:            if (amount > limitAmount) {
181:                uint256 targetWithheldAmount = amount - limitAmount;
182:                localWithheldAmount += targetWithheldAmount;
183:                amount = limitAmount;
184:
185:                emit AmountWithheld(target, targetWithheldAmount);
186:            }
187:
188:            // Check the OLAS balance and the contract being unpaused
189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {
190:                // Approve and transfer OLAS to the service staking target
191:                IToken(olas).approve(target, amount);
192:                IStaking(target).deposit(amount);
193:
194:                emit StakingTargetDeposited(target, amount);
195:            } else {
196:                // Hash of target + amount + batchNonce
197:                bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
198:                // Queue the hash for further redeem
199:                stakingQueueingNonces[queueHash] = true;
200:
201:                emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);
202:            }
203:        }
204:        // Increase the staking batch nonce
205:        stakingBatchNonce = batchNonce + 1;
206:
207:        // Adjust withheld amount, if at least one target has not passed the validity check
208:        if (localWithheldAmount > 0) {
209:            withheldAmount += localWithheldAmount;
210:        }
211:
212:        _locked = 1;	// @audit-issue

396:        (bool success, ) = msg.sender.call{value: amount}("");
397:        if (!success) {
398:            revert TransferFailed(address(0), address(this), msg.sender, amount);
399:        }
400:
401:        emit Drain(msg.sender, amount);
402:
403:        _locked = 1;	// @audit-issue
```
[199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L199), [205](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L205), [209](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L209), [212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L212), [403](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L396-L403), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

216:        (bool success, bytes memory returnData) = instance.call(initPayload);
217:        // Process unsuccessful call
218:        if (!success) {
219:            // Get the revert message bytes
220:            if (returnData.length > 0) {
221:                assembly {
222:                    let returnDataSize := mload(returnData)
223:                    revert(add(32, returnData), returnDataSize)
224:                }
225:            } else {
226:                revert InitializationFailed(instance);
227:            }
228:        }
229:
230:        // Check that the created proxy instance does not violate defined limits
231:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyInstance(instance, implementation)) {
232:            revert UnverifiedProxy(instance);
233:        }
234:
235:        // Record instance params
236:        InstanceParams memory instanceParams = InstanceParams(implementation, msg.sender, true);
237:        mapInstanceParams[instance] = instanceParams;	// @audit-issue

216:        (bool success, bytes memory returnData) = instance.call(initPayload);
217:        // Process unsuccessful call
218:        if (!success) {
219:            // Get the revert message bytes
220:            if (returnData.length > 0) {
221:                assembly {
222:                    let returnDataSize := mload(returnData)
223:                    revert(add(32, returnData), returnDataSize)
224:                }
225:            } else {
226:                revert InitializationFailed(instance);
227:            }
228:        }
229:
230:        // Check that the created proxy instance does not violate defined limits
231:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyInstance(instance, implementation)) {
232:            revert UnverifiedProxy(instance);
233:        }
234:
235:        // Record instance params
236:        InstanceParams memory instanceParams = InstanceParams(implementation, msg.sender, true);
237:        mapInstanceParams[instance] = instanceParams;
238:        // Increase the nonce
239:        nonce = localNonce + 1;	// @audit-issue

216:        (bool success, bytes memory returnData) = instance.call(initPayload);
217:        // Process unsuccessful call
218:        if (!success) {
219:            // Get the revert message bytes
220:            if (returnData.length > 0) {
221:                assembly {
222:                    let returnDataSize := mload(returnData)
223:                    revert(add(32, returnData), returnDataSize)
224:                }
225:            } else {
226:                revert InitializationFailed(instance);
227:            }
228:        }
229:
230:        // Check that the created proxy instance does not violate defined limits
231:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyInstance(instance, implementation)) {
232:            revert UnverifiedProxy(instance);
233:        }
234:
235:        // Record instance params
236:        InstanceParams memory instanceParams = InstanceParams(implementation, msg.sender, true);
237:        mapInstanceParams[instance] = instanceParams;
238:        // Increase the nonce
239:        nonce = localNonce + 1;
240:
241:        emit InstanceCreated(msg.sender, instance, implementation);
242:
243:        _locked = 1;	// @audit-issue
```
[237](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L216-L237), [239](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L216-L239), [243](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L216-L243), 


#### Recommendation

To enhance the security of your smart contract and prevent reentrancy attacks, it's crucial to adhere to the check-effects-interactions pattern. Restructure your contract's functions to follow this pattern by first performing condition checks (checks), then updating the contract's state (effects), and finally, executing any external calls or interactions (interactions). This sequential approach reduces the risk of reentrancy vulnerabilities by ensuring that state changes are isolated from external interactions and condition checks are made before any state modifications. Review and refactor your contract code to align with this best practice for improved security.

### Missing Reentrancy Modifier
A reentrancy attack is a common vulnerability in smart contracts, particularly when a contract makes an external call to another untrusted contract and then continues to execute code afterwards. If the called contract is malicious, it can call back into the original contract in a way that causes the original function to run again, potentially leading to effects like multiple withdrawals and other unintended actions.

The absence of reentrancy guards, such as the `nonReentrant` modifier provided by OpenZeppelin's ReentrancyGuard contract, makes a function susceptible to reentrancy attacks. This modifier prevents a function from being called again until it has finished executing.

```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

396:        (bool success, ) = msg.sender.call{value: amount}("");	// @audit-issue
```
[396](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L396-L396), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

216:        (bool success, bytes memory returnData) = instance.call(initPayload);	// @audit-issue
```
[216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L216-L216), 


#### Recommendation

Consider adding `nonReentrant` modifier to given functions.

### Missing checks for `address(0)` in constructor/initializers
In Solidity, the Ethereum address `0x0000000000000000000000000000000000000000` is known as the "zero address". This address has significance because it's the default value for uninitialized address variables and is often used to represent an invalid or non-existent address. The "
Missing zero address control" issue arises when a Solidity smart contract does not properly check or prevent interactions with the zero address, leading to unintended behavior.
For instance, a contract might allow tokens to be sent to the zero address without any checks, which essentially burns those tokens as they become irretrievable. While sometimes this is intentional, without proper control or checks, accidental transfers could occur.    
        

```solidity
Path: ./tokenomics/contracts/Dispenser.sol

341:        tokenomics = _tokenomics;	// @audit-issue

342:        treasury = _treasury;	// @audit-issue

343:        voteWeighting = _voteWeighting;	// @audit-issue
```
[341](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L341-L341), [342](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L342-L342), [343](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L343-L343), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

107:        outbox = _outbox;	// @audit-issue

108:        bridge = _bridge;	// @audit-issue
```
[107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L107-L107), [108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L108-L108), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

81:        olas = _olas;	// @audit-issue

83:        l1TokenRelayer = _l1TokenRelayer;	// @audit-issue

84:        l1MessageRelayer = _l1MessageRelayer;	// @audit-issue
```
[81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L81-L81), [83](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L83-L83), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L84-L84), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

77:        dispenser = _dispenser;	// @audit-issue

78:        stakingFactory = _stakingFactory;	// @audit-issue

79:        timelock = _timelock;	// @audit-issue
```
[77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L77-L77), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L78-L78), [79](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L79-L79), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

126:        stakingFactory = _stakingFactory;	// @audit-issue

127:        l2MessageRelayer = _l2MessageRelayer;	// @audit-issue

128:        l1DepositProcessor = _l1DepositProcessor;	// @audit-issue
```
[126](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L126-L126), [127](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L127-L127), [128](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L128-L128), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

53:        predicate = _predicate;	// @audit-issue
```
[53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L53-L53), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

67:        stakingToken = _stakingToken;	// @audit-issue
```
[67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L67-L67), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

105:        verifier = _verifier;	// @audit-issue
```
[105](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L105-L105), 


#### Recommendation

It is strongly recommended to implement checks to prevent the zero address from being set during the initialization of contracts. This can be achieved by adding require statements that ensure address parameters are not the zero address. 

### Missing checks for `address(0)`  when updating state variables
In Solidity, the Ethereum address `0x0000000000000000000000000000000000000000` is known as the "zero address". This address has significance because it's the default value for uninitialized address variables and is often used to represent an invalid or non-existent address. The "
Missing zero address control" issue arises when a Solidity smart contract does not properly check or prevent interactions with the zero address, leading to unintended behavior.
For instance, a contract might allow tokens to be sent to the zero address without any checks, which essentially burns those tokens as they become irretrievable. While sometimes this is intentional, without proper control or checks, accidental transfers could occur.    
        

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

451:        treasury = _treasury;	// @audit-issue

452:        depository = _depository;	// @audit-issue

453:        dispenser = _dispenser;	// @audit-issue

454:        ve = _ve;	// @audit-issue

456:        componentRegistry = _componentRegistry;	// @audit-issue

457:        agentRegistry = _agentRegistry;	// @audit-issue

458:        serviceRegistry = _serviceRegistry;	// @audit-issue

459:        donatorBlacklist = _donatorBlacklist;	// @audit-issue

622:        donatorBlacklist = _donatorBlacklist;	// @audit-issue
```
[451](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L451-L451), [452](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L452-L452), [453](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L453-L453), [454](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L454-L454), [456](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L456-L456), [457](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L457-L457), [458](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L458-L458), [459](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L459-L459), [622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L622-L622), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

207:        _setL2TargetDispenser(l2Dispenser);	// @audit-issue
```
[207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L207-L207), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

134:        _setL2TargetDispenser(l2Dispenser);	// @audit-issue
```
[134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L134-L134), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

118:        setFxChildTunnel(l2Dispenser);	// @audit-issue

119:        _setL2TargetDispenser(l2Dispenser);	// @audit-issue
```
[118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L118-L118), [119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L119-L119), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

133:        verifier = newVerifier;	// @audit-issue
```
[133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L133-L133), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

392:        dispenser = newDispenser;	// @audit-issue
```
[392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L392-L392), 


#### Recommendation

It is strongly recommended to implement checks to prevent the zero address from being set during the initialization of contracts. This can be achieved by adding require statements that ensure address parameters are not the zero address. 

### The `constructor`/`initialize` function lacks parameter validation.
Constructors and initialization functions play a critical role in contracts by setting important initial states when the contract is first deployed before the system starts. The parameters passed to the constructor and initialization functions directly affect the behavior of the contract / protocol. If incorrect parameters are provided, the system may fail to run, behave abnormally, be unstable, or lack security. Therefore, it's crucial to carefully check each parameter in the constructor and initialization functions. If an exception is found, the transaction should be rolled back.

```solidity
Path: ./tokenomics/contracts/Dispenser.sol

348:        maxNumStakingTargets = _maxNumStakingTargets;	// @audit-issue
```
[348](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L348-L348), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

89:        timeForEmissionsLimit = _timeForEmissionsLimit;	// @audit-issue

90:        numServicesLimit = _numServicesLimit;	// @audit-issue

91:        emissionsLimit = _rewardsPerSecondLimit * _timeForEmissionsLimit * _numServicesLimit;	// @audit-issue
```
[89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L89-L89), [90](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L90-L90), [91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L91-L91), 


#### Recommendation

Incorporate comprehensive parameter validation in your contract's constructor and initialization functions. This should include checks for value ranges, address validity, and any other condition that defines a parameter as valid. Use `require` statements for validation, providing clear error messages for each condition. If any validation fails, the `require` statement will revert the transaction, preventing the contract from being deployed or initialized with invalid parameters.
Example:
```solidity
contract MyContract {
    address public owner;
    uint256 public someValue;

    constructor(address _owner, uint256 _someValue) {
        require(_owner != address(0), "Owner cannot be the zero address");
        require(_someValue > 0 && _someValue < 100, "SomeValue must be between 1 and 99");

        owner = _owner;
        someValue = _someValue;
    }
}

// For contracts using the proxy pattern and requiring initialization functions:
function initialize(address _owner, uint256 _someValue) public {
    require(_owner != address(0), "Owner cannot be the zero address");
    require(_someValue > 0 && _someValue < 100, "SomeValue must be between 1 and 99");

    owner = _owner;
    someValue = _someValue;
}
```


### Division before multiplication
When dealing with arithmetic operations, it's crucial to prioritize multiplication before division. This practice helps to reduce the risk of rounding errors and increases the precision of the calculations. By rearranging your mathematical operations to perform multiplications first, you can enhance the accuracy and efficiency of your contract's computational processes.

```solidity
Path: ./governance/contracts/VoteWeighting.sol

214:        timeSum = block.timestamp / WEEK * WEEK;	// @audit-issue

309:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;	// @audit-issue

424:        uint256 t = time / WEEK * WEEK;	// @audit-issue

489:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;	// @audit-issue

605:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;	// @audit-issue
```
[214](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L214-L214), [309](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L309-L309), [424](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L424-L424), [489](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L489-L489), [605](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L605-L605), 


#### Recommendation

Review your smart contract code to identify instances where division precedes multiplication. Refactor these calculations by rearranging the order, ensuring multiplication is performed first. This adjustment can significantly improve the precision of your results, especially in environments where every decimal point matters, like financial or token-related contracts.

### Gas griefing/theft is possible on an unsafe external call
A low-level call will copy any amount of bytes to local memory. When bytes are copied from returndata to memory, the memory expansion cost is paid.

This means that when using a standard solidity call, the callee can 'returnbomb' the caller, imposing an arbitrary gas cost.

Because this gas is paid by the caller and in the caller's context, it can cause the caller to run out of gas and halt execution.

Consider replacing all unsafe `call` with `excessivelySafeCall` from this [repository](https://github.com/nomad-xyz/ExcessivelySafeCall).


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

33:        (bool success, ) = to.call{value: amount}("");	// @audit-issue
```
[33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L33-L33), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

216:        (bool success, bytes memory returnData) = instance.call(initPayload);	// @audit-issue
```
[216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L216-L216), 


#### Recommendation

Mitigate the risk of gas griefing by replacing direct low-level calls (`call`, `delegatecall`, etc.) with `excessivelySafeCall`, a utility that limits the amount of gas used for copying return data, thereby preventing return bombing. The `excessivelySafeCall` function, available in certain security-focused repositories such as [nomad-xyz/ExcessivelySafeCall](https://github.com/nomad-xyz/ExcessivelySafeCall), implements additional checks to ensure that the callee cannot impose arbitrary gas costs on the caller. To incorporate this into your contract:

1. Review your contract for external calls to untrusted contracts.
2. Import or implement an `excessivelySafeCall` utility in your contract.
3. Replace unsafe external calls with `excessivelySafeCall`, specifying limits on return data size as appropriate for your application.

Example usage:
```solidity
// Assuming excessivelySafeCall is available in your contract
(bool success, ) = target.excessivelySafeCall(gasLimit, callData, maxReturnSize);
require(success, "External call failed");

This approach not only protects against gas griefing attacks but also enhances the overall security posture of your contract by ensuring that external interactions do not lead to unexpected execution halts or excessive gas consumption. Always perform thorough testing after integrating `excessivelySafeCall` to ensure compatibility and intended functionality.

### Unsafe downcast may overflow
When a type is downcast to a smaller type, the higher order bits are discarded, resulting in the application of a modulo operation to the original value.

If the downcasted value is large enough, this may result in an overflow that will not revert.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

775:        stakingPoint.minStakingWeight = uint16(_minStakingWeight);	// @audit-issue: Variable `_minStakingWeight` is type `uint256` and it is downcasted to `uint16`
```
[775](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L775-L775), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

96:        sequence = sendTokenWithPayloadToEvm(uint16(wormholeTargetChainId), l2TargetDispenser, data, 0,	// @audit-issue: Variable `wormholeTargetChainId` is type `uint256` and it is downcasted to `uint16`

133:        setRegisteredSender(uint16(wormholeTargetChainId), bytes32(uint256(uint160(l2Dispenser))));	// @audit-issue: Variable `wormholeTargetChainId` is type `uint256` and it is downcasted to `uint16`
```
[96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L96-L96), [133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L133-L133), 


#### Recommendation

When downcasting numbers to `addresses` in Solidity, be cautious of potential collisions. Downcasting truncates the upper bytes of the number, which can lead to multiple values mapping to the same `address`. To avoid collisions, consider using a more robust mapping strategy or additional checks to ensure unique mappings.

### Avoid Double `casting`
Consider refactoring the following code, as double casting may introduce unexpected truncations and/or rounding issues.

Furthermore, double type casting can make the code less readable and harder to maintain, increasing the likelihood of errors and misunderstandings during development and debugging.


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

424:            address stakingTargetEVM = address(uint160(uint256(stakingTarget)));	// @audit-issue

486:                    stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));	// @audit-issue
```
[424](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L424-L424), [486](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L486-L486), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

164:        address processor = address(uint160(uint256(sourceProcessor)));	// @audit-issue
```
[164](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L164-L164), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

125:        address l2Dispenser = address(uint160(uint256(sourceAddress)));	// @audit-issue

133:        setRegisteredSender(uint16(wormholeTargetChainId), bytes32(uint256(uint160(l2Dispenser))));	// @audit-issue
```
[125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L125-L125), [133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L133-L133), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

147:            uint256(uint160(implementation)));	// @audit-issue

156:        return address(uint160(uint256(hash)));	// @audit-issue

204:            uint256(uint160(implementation)));	// @audit-issue
```
[147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L147-L147), [156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L156-L156), [204](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L204-L204), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

340:        Nominee memory nominee = Nominee(bytes32(uint256(uint160(account))), chainId);	// @audit-issue

515:            slope: uint256(uint128(userSlope)) * weight / MAX_WEIGHT,	// @audit-issue
```
[340](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L340-L340), [515](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L515-L515), 


#### Recommendation

To enhance code readability, maintainability, and avoid potential truncation or rounding issues, refactor code that involves double type casting. Minimize the use of double casting to improve code quality and reduce the risk of errors during development and debugging.

### Int casting `block.timestamp` can reduce the lifespan of a contract
In Solidity, `block.timestamp` returns the Unix timestamp of the current block, which is a continuously increasing value. Casting `block.timestamp` to a smaller integer type, like `uint32`, can limit the maximum value it can hold. As time progresses and the Unix timestamp exceeds this maximum value, the casted value will overflow, leading to incorrect and potentially harmful behavior in the contract. This issue can significantly reduce the functional lifespan of a contract, as it becomes unreliable and potentially insecure once the timestamp exceeds the capacity of the casted integer type.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

478:        mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);	// @audit-issue

1220:        tp.epochPoint.endTime = uint32(block.timestamp);	// @audit-issue
```
[478](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L478-L478), [1220](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1220-L1220), 


#### Recommendation

Avoid casting `block.timestamp` to smaller integer types in your Solidity contracts. Use `uint256` to store timestamp values to accommodate the increasing nature of Unix timestamps and ensure the contract remains functional in the long term. Regularly review and update your contracts to remove any unnecessary casting of `block.timestamp` that could lead to overflows and reduce the contract's lifespan. By doing so, you maintain the contract's reliability and integrity well into the future.

### Consider using OpenZeppelin’s `SafeCast` library to prevent unexpected overflows when casting from various type int/uint values
In Solidity, when casting from `int` to `uint` or vice versa, there is a risk of unexpected overflow or underflow, especially when dealing with large values. To mitigate this risk and ensure safe type conversions, you can consider using OpenZeppelin’s `SafeCast` library. This library provides functions that check for overflow/underflow conditions before performing the cast, helping you prevent potential issues related to type conversion in your smart contracts.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

440:        if (uint32(_epochLen) < MIN_EPOCH_LENGTH) {	// @audit-issue

445:        if (uint32(_epochLen) > MAX_EPOCH_LENGTH) {	// @audit-issue

455:        epochLen = uint32(_epochLen);	// @audit-issue

469:        uint256 zeroYearSecondsLeft = uint32(_timeLaunch + ONE_YEAR - block.timestamp);	// @audit-issue

474:        inflationPerSecond = uint96(_inflationPerSecond);	// @audit-issue

475:        timeLaunch = uint32(_timeLaunch);	// @audit-issue

478:        mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);	// @audit-issue

503:        tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);	// @audit-issue

510:        maxBond = uint96(_maxBond);	// @audit-issue

511:        effectiveBond = uint96(_maxBond);	// @audit-issue

652:        if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {	// @audit-issue

653:            devsPerCapital = uint72(_devsPerCapital);	// @audit-issue

660:        if (uint72(_codePerDev) > MIN_PARAM_VALUE) {	// @audit-issue

661:            codePerDev = uint72(_codePerDev);	// @audit-issue

671:            epsilonRate = uint64(_epsilonRate);	// @audit-issue

677:        if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {	// @audit-issue

678:            nextEpochLen = uint32(_epochLen);	// @audit-issue

684:        if (uint96(_veOLASThreshold) > 0) {	// @audit-issue

685:            nextVeOLASThreshold = uint96(_veOLASThreshold);	// @audit-issue

732:        tp.unitPoints[0].rewardUnitFraction = uint8(_rewardComponentFraction);	// @audit-issue

733:        tp.unitPoints[1].rewardUnitFraction = uint8(_rewardAgentFraction);	// @audit-issue

735:        tp.epochPoint.rewardTreasuryFraction = uint8(100 - _rewardComponentFraction - _rewardAgentFraction);	// @audit-issue

737:        tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);	// @audit-issue

738:        tp.unitPoints[0].topUpUnitFraction = uint8(_topUpComponentFraction);	// @audit-issue

739:        tp.unitPoints[1].topUpUnitFraction = uint8(_topUpAgentFraction);	// @audit-issue

740:        mapEpochStakingPoints[eCounter].stakingFraction = uint8(_stakingFraction);	// @audit-issue

774:        stakingPoint.maxStakingIncentive = uint96(_maxStakingIncentive);	// @audit-issue

775:        stakingPoint.minStakingWeight = uint16(_minStakingWeight);	// @audit-issue

799:            effectiveBond = uint96(eBond);	// @audit-issue

820:        effectiveBond = uint96(eBond);	// @audit-issue

839:        mapEpochStakingPoints[eCounter].stakingIncentive = uint96(stakingIncentive);	// @audit-issue

857:            mapUnitIncentives[unitType][unitId].reward = uint96(totalIncentives);	// @audit-issue

871:            uint256 sumUnitIncentives = uint256(mapEpochTokenomics[epochNum].unitPoints[unitType].sumUnitTopUpsOLAS) * 100;	// @audit-issue

873:            mapUnitIncentives[unitType][unitId].topUp = uint96(totalIncentives);	// @audit-issue

928:                    uint96 amount = uint96(amounts[i] / numServiceUnits);	// @audit-issue

935:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);	// @audit-issue

943:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);	// @audit-issue

1020:        mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH = uint96(donationETH);	// @audit-issue

1026:        lastDonationBlockNumber = uint32(block.number);	// @audit-issue

1140:            inflationPerSecond = uint96(curInflationPerSecond);	// @audit-issue

1141:            currentYear = uint8(numYears);	// @audit-issue

1151:        tp.epochPoint.totalTopUpsOLAS = uint96(inflationPerEpoch);	// @audit-issue

1167:            effectiveBond = uint96(incentives[4]);	// @audit-issue

1178:                epochLen = uint32(curEpochLen);	// @audit-issue

1220:        tp.epochPoint.endTime = uint32(block.timestamp);	// @audit-issue

1238:        mapEpochStakingPoints[eCounter].stakingIncentive = uint96(incentives[8]);	// @audit-issue

1258:            maxBond = uint96(curMaxBond);	// @audit-issue

1265:            maxBond = uint96(curMaxBond);	// @audit-issue

1271:        effectiveBond = uint96(curMaxBond);	// @audit-issue

1277:            nextEpochPoint.epochPoint.idf = uint64(idf);	// @audit-issue

1290:            epochCounter = uint32(eCounter + 1);	// @audit-issue

1449:                    uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100;	// @audit-issue
```
[440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L440-L440), [445](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L445-L445), [455](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L455-L455), [469](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L469-L469), [474](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L474-L474), [475](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L475-L475), [478](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L478-L478), [503](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L503-L503), [510](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L510-L510), [511](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L511-L511), [652](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L652-L652), [653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L653-L653), [660](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L660-L660), [661](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L661-L661), [671](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L671-L671), [677](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L677-L677), [678](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L678-L678), [684](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L684-L684), [685](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L685-L685), [732](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L732-L732), [733](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L733-L733), [735](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L735-L735), [737](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L737-L737), [738](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L738-L738), [739](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L739-L739), [740](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L740-L740), [774](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L774-L774), [775](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L775-L775), [799](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L799-L799), [820](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L820-L820), [839](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L839-L839), [857](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L857-L857), [871](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L871-L871), [873](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L873-L873), [928](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L928-L928), [935](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L935-L935), [943](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L943-L943), [1020](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1020-L1020), [1026](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1026-L1026), [1140](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1140-L1140), [1141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1141-L1141), [1151](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1151-L1151), [1167](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1167-L1167), [1178](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1178-L1178), [1220](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1220-L1220), [1238](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1238-L1238), [1258](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1258-L1258), [1265](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1265-L1265), [1271](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1271-L1271), [1277](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1277-L1277), [1290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1290-L1290), [1449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1449-L1449), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

424:            address stakingTargetEVM = address(uint160(uint256(stakingTarget)));	// @audit-issue

486:                    stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));	// @audit-issue

553:                if (uint256(lastTarget) >= uint256(stakingTargets[i][j])) {	// @audit-issue

911:            if (stakingWeight < uint256(stakingPoint.minStakingWeight) * 1e14) {	// @audit-issue
```
[424](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L424-L424), [486](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L486-L486), [553](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L553-L553), [911](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L911-L911), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

89:        IBridge(l2MessageRelayer).sendMessage{value: cost}(l1DepositProcessor, data, uint32(gasLimitMessage));	// @audit-issue
```
[89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L89-L89), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

46:        uint160 offset = uint160(0x1111000000000000000000000000000000001111);	// @audit-issue

48:            l1AliasedDepositProcessor = address(uint160(_l1DepositProcessor) + offset);	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L46-L46), [48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L48-L48), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

115:                uint32(TOKEN_GAS_LIMIT), "");	// @audit-issue

140:        IBridge(l1MessageRelayer).sendMessage{value: cost}(l2TargetDispenser, data, uint32(gasLimitMessage));	// @audit-issue
```
[115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L115-L115), [140](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L140-L140), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

112:        (uint256 cost, ) = IBridge(l2MessageRelayer).quoteEVMDeliveryPrice(uint16(l1SourceChainId), 0, gasLimitMessage);	// @audit-issue

120:        uint64 sequence = IBridge(l2MessageRelayer).sendPayloadToEvm{value: cost}(uint16(l1SourceChainId),	// @audit-issue

121:            l1DepositProcessor, abi.encode(amount), 0, gasLimitMessage, uint16(l1SourceChainId), refundAccount);	// @audit-issue

164:        address processor = address(uint160(uint256(sourceProcessor)));	// @audit-issue
```
[112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L112-L112), [120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L120-L120), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L121-L121), [164](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L164-L164), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

92:            sequence = uint256(iMsg);	// @audit-issue
```
[92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L92-L92), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

96:        sequence = sendTokenWithPayloadToEvm(uint16(wormholeTargetChainId), l2TargetDispenser, data, 0,	// @audit-issue

97:            gasLimitMessage, olas, transferAmount, uint16(l2TargetChainId), refundAccount);	// @audit-issue

125:        address l2Dispenser = address(uint160(uint256(sourceAddress)));	// @audit-issue

133:        setRegisteredSender(uint16(wormholeTargetChainId), bytes32(uint256(uint160(l2Dispenser))));	// @audit-issue
```
[96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L96-L96), [97](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L97-L97), [125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L125-L125), [133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L133-L133), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

82:        emit MessagePosted(uint256(iMsg), msg.sender, l1DepositProcessor, amount);	// @audit-issue
```
[82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L82-L82), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

756:            revert WrongServiceState(uint256(service.state), serviceId);	// @audit-issue
```
[756](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L756-L756), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

147:            uint256(uint160(implementation)));	// @audit-issue

156:        return address(uint160(uint256(hash)));	// @audit-issue

204:            uint256(uint160(implementation)));	// @audit-issue
```
[147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L147-L147), [156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L156-L156), [204](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L204-L204), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

340:        Nominee memory nominee = Nominee(bytes32(uint256(uint160(account))), chainId);	// @audit-issue

515:            slope: uint256(uint128(userSlope)) * weight / MAX_WEIGHT,	// @audit-issue
```
[340](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L340-L340), [515](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L515-L515), 


#### Recommendation

To enhance the safety and reliability of your Solidity smart contracts, it is advisable to utilize OpenZeppelin’s `SafeCast` library when casting between `int` and `uint` types. Incorporating this library into your codebase will help prevent unexpected overflows and underflows during type conversion, reducing the risk of vulnerabilities and ensuring secure contract execution.

### Numbers downcast to `addresses` may result in collisions
If a number is downcast to an `address` the upper bytes are truncated, which may mean that more than one value will map to the `address`


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

424:            address stakingTargetEVM = address(uint160(uint256(stakingTarget)));	// @audit-issue

486:                    stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));	// @audit-issue
```
[424](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L424-L424), [486](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L486-L486), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

164:        address processor = address(uint160(uint256(sourceProcessor)));	// @audit-issue
```
[164](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L164-L164), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

125:        address l2Dispenser = address(uint160(uint256(sourceAddress)));	// @audit-issue
```
[125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L125-L125), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

156:        return address(uint160(uint256(hash)));	// @audit-issue
```
[156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L156-L156), 


#### Recommendation

When downcasting numbers to `addresses` in Solidity, be cautious of potential collisions. Downcasting truncates the upper bytes of the number, which can lead to multiple values mapping to the same `address`. To avoid collisions, consider using a more robust mapping strategy or additional checks to ensure unique mappings.

### Initializers can be front-run
Initializers could be front-run, allowing an attacker to either set their own values, take ownership of the contract, and in the best case forcing a re-deployment.


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

20:    function initialize(StakingParams memory _stakingParams) external {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L20-L20), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

54:    function initialize(	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L54-L54), 


#### Recommendation

To mitigate the risk of initializers being front-run, ensure that they are only callable by a trusted entity, such as the deployer, or through a secure, multi-step initialization process. One approach is to use a constructor for initial setup, which is inherently protected against front-running. If a separate initializer is necessary, consider using a modifier that restricts the initializer function to be called only once by a predefined address, typically the deployer's address. Additionally, implement robust checks within the initializer to validate inputs and reject suspicious or unauthorized initialization attempts.

### State variables not limited to reasonable values
Consider adding appropriate minimum/maximum value checks to ensure that the following state variables can never be used to excessively harm users, including via griefing.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

455:        epochLen = uint32(_epochLen);	// @audit-issue

653:            devsPerCapital = uint72(_devsPerCapital);	// @audit-issue

661:            codePerDev = uint72(_codePerDev);	// @audit-issue

678:            nextEpochLen = uint32(_epochLen);	// @audit-issue

685:            nextVeOLASThreshold = uint96(_veOLASThreshold);	// @audit-issue

740:        mapEpochStakingPoints[eCounter].stakingFraction = uint8(_stakingFraction);	// @audit-issue

1020:        mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH = uint96(donationETH);	// @audit-issue
```
[455](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L455-L455), [653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L653-L653), [661](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L661-L661), [678](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L678-L678), [685](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L685-L685), [740](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L740-L740), [1020](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1020-L1020), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

532:        pointsWeight[nomineeHash][nextTime].bias = _maxAndSub(_getWeight(account, chainId) + newBias, oldBias);	// @audit-issue
```
[532](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L532-L532), 


#### Recommendation


Implement validation checks for state variables to enforce minimum and maximum value limits. This can be achieved by adding modifiers or require statements in Solidity functions that modify these state variables. Ensure that these limits are reasonable and reflect the intended use of the contract. Additionally, consider implementing a mechanism to update these limits through a governance process or a trusted role, if applicable, to maintain flexibility and adaptability of the contract over time.

### Missing contract existence checks before low-level calls
Low-level calls return success if there is no code present at the specified address. In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`.

```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);	// @audit-issue
```
[161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L161), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

33:        (bool success, ) = to.call{value: amount}("");	// @audit-issue
```
[33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L33-L33), 


#### Recommendation

To enhance the security and reliability of your Solidity smart contracts, always include contract existence checks before making low-level calls. In addition to verifying that the address is not the zero address, also confirm that `<address>.code.length > 0`. These checks help ensure that the target address corresponds to a valid and functioning contract, reducing the risk of unexpected behavior and vulnerabilities in your contract interactions.

### External calls in an unbounded loop can result in a DoS
Consider limiting the number of iterations in loops that make external calls, as just a single one of them failing will result in a revert.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

910:                address serviceOwner = IToken(serviceRegistry).ownerOf(serviceIds[i]);	// @audit-issue

911:                topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||	// @audit-issue

912:                    IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;	// @audit-issue

918:                (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).	// @audit-issue
919:                    getUnitIdsOfService(IServiceRegistry.UnitType(unitType), serviceIds[i]);

919:                    getUnitIdsOfService(IServiceRegistry.UnitType(unitType), serviceIds[i]);	// @audit-issue

967:                        address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);	// @audit-issue

1012:            if (!IServiceRegistry(serviceRegistry).exists(serviceIds[i])) {	// @audit-issue

1330:            registriesSupply[i] = IToken(registries[i]).totalSupply();	// @audit-issue

1348:            address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);	// @audit-issue

1402:            registriesSupply[i] = IToken(registries[i]).totalSupply();	// @audit-issue

1420:            address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);	// @audit-issue

1449:                    uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100;	// @audit-issue
```
[910](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L910-L910), [911](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L911-L911), [912](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L912-L912), [918](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L918-L919), [919](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L919-L919), [967](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L967-L967), [1012](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1012-L1012), [1330](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1330-L1330), [1348](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1348-L1348), [1402](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1402-L1402), [1420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1420-L1420), [1449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1449-L1449), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

456:                IToken(olas).transfer(depositProcessor, transferAmounts[i]);	// @audit-issue

461:            bool[] memory positions = new bool[](stakingTargets[i].length);	// @audit-issue

484:                address[] memory stakingTargetsEVM = new address[](updatedStakingTargets.length);	// @audit-issue

490:                IDepositProcessor(depositProcessor).sendMessageBatch{value:valueAmounts[i]}(stakingTargetsEVM,	// @audit-issue

494:                IDepositProcessor(depositProcessor).sendMessageBatchNonEVM{value:valueAmounts[i]}(updatedStakingTargets,	// @audit-issue

545:                revert Overflow(stakingTargets[i].length, localMaxNumStakingTargets);	// @audit-issue

596:            uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();	// @audit-issue

598:            stakingIncentives[i] = new uint256[](stakingTargets[i].length);	// @audit-issue

884:                ITokenomics(tokenomics).mapEpochStakingPoints(j);	// @audit-issue

886:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue

893:                IVoteWeighting(voteWeighting).nomineeRelativeWeight(stakingTarget, chainId, endTime);	// @audit-issue

911:            if (stakingWeight < uint256(stakingPoint.minStakingWeight) * 1e14) {	// @audit-issue

1148:            ITokenomics.StakingPoint memory stakingPoint = ITokenomics(tokenomics).mapEpochStakingPoints(j);	// @audit-issue

1151:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue

1154:            (uint256 stakingWeight, ) = IVoteWeighting(voteWeighting).nomineeRelativeWeight(retainer,	// @audit-issue

1155:                block.chainid, endTime);	// @audit-issue
```
[456](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L456-L456), [461](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L461-L461), [484](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L484-L484), [490](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L490-L490), [494](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L494-L494), [545](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L545-L545), [596](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L596-L596), [598](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L598-L598), [884](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L884-L884), [886](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L886-L886), [893](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L893-L893), [911](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L911-L911), [1148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1148-L1148), [1151](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1151-L1151), [1154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1154-L1154), [1155](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1155-L1155), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

99:            uint256 limitAmount = IStakingFactory(stakingFactory).verifyInstanceAndGetEmissionsAmount(target);	// @audit-issue

112:                IToken(olas).transfer(timelock, refundAmount);	// @audit-issue

118:            IToken(olas).approve(target, amount);	// @audit-issue

119:            IStaking(target).deposit(amount);	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L99-L99), [112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L112-L112), [118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L118-L118), [119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L119-L119), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

160:            bytes memory verifyData = abi.encodeCall(IStakingFactory.verifyInstanceAndGetEmissionsAmount, target);	// @audit-issue

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);	// @audit-issue

166:                limitAmount = abi.decode(returnData, (uint256));	// @audit-issue

189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {	// @audit-issue

191:                IToken(olas).approve(target, amount);	// @audit-issue

192:                IStaking(target).deposit(amount);	// @audit-issue
```
[160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L160-L160), [161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L161), [166](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L166-L166), [189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L189-L189), [191](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L191-L191), [192](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L192-L192), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

335:                revert WrongAgentId(_stakingParams.agentIds[i]);	// @audit-issue

382:                revert LowerThan(agentParams[i].bond, minDeposit);	// @audit-issue

569:                (ratioPass, serviceNonces[i]) = _checkRatioPass(sInfo.multisig, sInfo.nonces, ts);	// @audit-issue
```
[335](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L335-L335), [382](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L382-L382), [569](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L569-L569), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

93:            uint256 bond = IServiceTokenUtility(serviceRegistryTokenUtility).getAgentBond(serviceId, serviceAgentIds[i]);	// @audit-issue
```
[93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L93-L93), 


#### Recommendation

To mitigate the risk of Denial-of-Service (DoS) attacks in your Solidity code, it's important to limit the number of iterations in loops that involve external calls. A single failed external call in an unbounded loop can lead to a revert, causing disruptions in contract execution. Consider implementing safeguards, such as setting a maximum loop iteration count or employing strategies like batch processing, to reduce the impact of potential external call failures.

### Solidity version 0.8.20 might not work on all chains due to `PUSH0`
The Solidity version 0.8.20 employs the recently introduced PUSH0 opcode in the Shanghai EVM. This opcode might not be universally supported across all blockchain networks and Layer 2 solutions. Thus, as a result, it might be not possible to deploy solution with version 0.8.20 >= on some blockchains.

```solidity
Path: ./tokenomics/contracts/interfaces/IDonatorBlacklist.sol

2:pragma solidity ^0.8.18;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/interfaces/IErrorsTokenomics.sol

2:pragma solidity ^0.8.18;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L2-L2), 


#### Recommendation

The Solidity version 0.8.20 employs the recently introduced PUSH0 opcode in the Shanghai EVM. This opcode might not be universally supported across all blockchain networks and Layer 2 solutions. Thus, as a result, it might be not possible to deploy solution with version 0.8.20 >= on some blockchains.

It is recommended to verify whether solution can be deployed on particular blockchain with the Solidity version 0.8.20 >=. Whenever such deployment is not possible due to lack of PUSH0 opcode support and lowering the Solidity version is a must, it is strongly advised to review all feature changes and bugfixes in [Solidity releases](https://soliditylang.org/blog/category/releases/). Some changes may have impact on current implementation and may impose a necessity of maintaining another version of solution.

### Contracts are designed to receive ETH but do not implement function for withdrawal
The following contracts can receive ETH but do not provide a function for withdrawal. This means that any ETH sent to these contracts will be permanently stuck, unable to be retrieved by the contract owner or any other party. Additionally, this issue can also apply to baseTokens, resulting in locked tokens and potential loss of funds.


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

132:    function sendMessage(	// @audit-issue
```
[132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L132-L132), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

66:    function receiveMessage(bytes memory data) external payable {	// @audit-issue
```
[66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L66-L66), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

40:    fallback() external payable {	// @audit-issue
```
[40](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L40-L40), 


#### Recommendation

To prevent ETH and token lock-up and potential loss of funds, ensure that contracts designed to receive ETH or tokens implement a function for withdrawal. This function should allow contract owners and users to retrieve their funds when needed. Failure to provide a withdrawal mechanism can lead to locked assets and permanent loss, posing a significant risk to contract users and owners.

### `abi.encodePacked()` should not be used with dynamic types when passing the result to a hash function such as `keccak256()`
Use `abi.encode()` instead which will pad items to 32 bytes, which will [prevent hash collisions](https://docs.soliditylang.org/en/v0.8.13/abi-spec.html#non-standard-packed-mode) (e.g. `abi.encodePacked(0x123,0x456)` => `0x123456` => `abi.encodePacked(0x1,0x23456)`, but `abi.encode(0x123,0x456)` => `0x0...1230...456`). "Unless there is a compelling reason, `abi.encode` should be preferred". If there is only one argument to `abi.encodePacked()` it can often be cast to `bytes()` or `bytes32()` [instead](https://ethereum.stackexchange.com/questions/30912/how-to-compare-strings-in-solidity#answer-82739).
If all arguments are strings and or bytes, `bytes.concat()` should be used instead

```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

143:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue

201:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue
```
[143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L143-L143), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L201-L201), 


#### Recommendation

To ensure the integrity of hash functions like `keccak256()` in Solidity, prefer using `abi.encode()` instead of `abi.encodePacked()` when dealing with dynamic types. `abi.encode()` pads items to 32 bytes, preventing hash collisions and enhancing security. Only use `abi.encodePacked()` when there is a compelling reason. If there's only one argument to `abi.encodePacked()`, consider casting it to `bytes()` or `bytes32()`. If all arguments are strings or bytes, use `bytes.concat()` for better clarity and reliability.

### Excessive Gas Consumption Due to Nested Loops in Solidity
In the Solidity smart contract, there are instances where for-loops are nested within other for-loops. Nested loops in Solidity can lead to significantly increased gas consumption, especially when operating on large data sets. This is because the complexity of nested loops is multiplicative; a double nested loop results in \(O(n^2)\) complexity, and a triple nested loop leads to \(O(n^3)\) complexity. In the context of Ethereum, where each operation consumes a certain amount of gas, such complexity can make transactions prohibitively expensive and may even hit the block gas limit, causing transactions to fail.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

916:            for (uint256 unitType = 0; unitType < 2; ++unitType) {	// @audit-issue: This for is under another for loop on line 904

930:                    for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue: This for is under another for loop on line 904

960:                for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue: This for is under another for loop on line 904

930:                    for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue: This for is under another for loop on line 916

960:                for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue: This for is under another for loop on line 916
```
[916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L916-L916), [930](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L930-L930), [960](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L960-L960), [930](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L930-L930), [960](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L960-L960), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

462:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue: This for is under another for loop on line 450

473:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue: This for is under another for loop on line 450

485:                for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {	// @audit-issue: This for is under another for loop on line 450

550:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue: This for is under another for loop on line 526

600:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue: This for is under another for loop on line 593
```
[462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L462-L462), [473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L473-L473), [485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L485-L485), [550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L550-L550), [600](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L600-L600), 


#### Recommendation


1. **Optimize Data Structures**: Where possible, use more efficient data structures that can reduce the need for nested iterations. This might involve restructuring how data is stored and accessed.
2. **Limit Loop Operations**: Implement checks or mechanisms to limit the number of iterations in each loop. For instance, setting a hard cap on array sizes or breaking out of loops early when a certain condition is met.


### Critical functions should have a timelock
Critical functions, especially those affecting protocol parameters or user funds, are potential points of failure or exploitation. To mitigate risks, incorporating a timelock on such functions can be beneficial. A timelock requires a waiting period between the time an action is initiated and when it's executed, giving stakeholders time to react, potentially vetoing malicious or erroneous changes. To implement, integrate a smart contract like OpenZeppelin's `TimelockController` or build a custom mechanism. This ensures governance decisions or administrative changes are transparent and allows for community or multi-signature interventions, enhancing protocol security and trustworthiness.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

616:    function changeDonatorBlacklist(address _donatorBlacklist) external {
617:        // Check for the contract ownership
618:        if (msg.sender != owner) {
619:            revert OwnerOnly(msg.sender, owner);
620:        }
621:
622:        donatorBlacklist = _donatorBlacklist;	// @audit-issue

639:    function changeTokenomicsParameters(
640:        uint256 _devsPerCapital,
641:        uint256 _codePerDev,
642:        uint256 _epsilonRate,
643:        uint256 _epochLen,
644:        uint256 _veOLASThreshold
645:    ) external {
646:        // Check for the contract ownership
647:        if (msg.sender != owner) {
648:            revert OwnerOnly(msg.sender, owner);
649:        }
650:
651:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
652:        if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {
653:            devsPerCapital = uint72(_devsPerCapital);	// @audit-issue

639:    function changeTokenomicsParameters(
640:        uint256 _devsPerCapital,
641:        uint256 _codePerDev,
642:        uint256 _epsilonRate,
643:        uint256 _epochLen,
644:        uint256 _veOLASThreshold
645:    ) external {
646:        // Check for the contract ownership
647:        if (msg.sender != owner) {
648:            revert OwnerOnly(msg.sender, owner);
649:        }
650:
651:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
652:        if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {
653:            devsPerCapital = uint72(_devsPerCapital);
654:        } else {
655:            // This is done in order not to pass incorrect parameters into the event
656:            _devsPerCapital = devsPerCapital;
657:        }
658:
659:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
660:        if (uint72(_codePerDev) > MIN_PARAM_VALUE) {
661:            codePerDev = uint72(_codePerDev);	// @audit-issue

639:    function changeTokenomicsParameters(
640:        uint256 _devsPerCapital,
641:        uint256 _codePerDev,
642:        uint256 _epsilonRate,
643:        uint256 _epochLen,
644:        uint256 _veOLASThreshold
645:    ) external {
646:        // Check for the contract ownership
647:        if (msg.sender != owner) {
648:            revert OwnerOnly(msg.sender, owner);
649:        }
650:
651:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
652:        if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {
653:            devsPerCapital = uint72(_devsPerCapital);
654:        } else {
655:            // This is done in order not to pass incorrect parameters into the event
656:            _devsPerCapital = devsPerCapital;
657:        }
658:
659:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
660:        if (uint72(_codePerDev) > MIN_PARAM_VALUE) {
661:            codePerDev = uint72(_codePerDev);
662:        } else {
663:            // This is done in order not to pass incorrect parameters into the event
664:            _codePerDev = codePerDev;
665:        }
666:
667:        // Check the epsilonRate value for idf to fit in its size
668:        // 2^64 - 1 < 18.5e18, idf is equal at most 1 + epsilonRate < 18e18, which fits in the variable size
669:        // epsilonRate is the part of the IDF calculation and thus its change will be accounted for in the next epoch
670:        if (_epsilonRate > 0 && _epsilonRate <= 17e18) {
671:            epsilonRate = uint64(_epsilonRate);
672:        } else {
673:            _epsilonRate = epsilonRate;
674:        }
675:
676:        // Check for the epochLen value to change
677:        if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {
678:            nextEpochLen = uint32(_epochLen);	// @audit-issue

639:    function changeTokenomicsParameters(
640:        uint256 _devsPerCapital,
641:        uint256 _codePerDev,
642:        uint256 _epsilonRate,
643:        uint256 _epochLen,
644:        uint256 _veOLASThreshold
645:    ) external {
646:        // Check for the contract ownership
647:        if (msg.sender != owner) {
648:            revert OwnerOnly(msg.sender, owner);
649:        }
650:
651:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
652:        if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {
653:            devsPerCapital = uint72(_devsPerCapital);
654:        } else {
655:            // This is done in order not to pass incorrect parameters into the event
656:            _devsPerCapital = devsPerCapital;
657:        }
658:
659:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
660:        if (uint72(_codePerDev) > MIN_PARAM_VALUE) {
661:            codePerDev = uint72(_codePerDev);
662:        } else {
663:            // This is done in order not to pass incorrect parameters into the event
664:            _codePerDev = codePerDev;
665:        }
666:
667:        // Check the epsilonRate value for idf to fit in its size
668:        // 2^64 - 1 < 18.5e18, idf is equal at most 1 + epsilonRate < 18e18, which fits in the variable size
669:        // epsilonRate is the part of the IDF calculation and thus its change will be accounted for in the next epoch
670:        if (_epsilonRate > 0 && _epsilonRate <= 17e18) {
671:            epsilonRate = uint64(_epsilonRate);
672:        } else {
673:            _epsilonRate = epsilonRate;
674:        }
675:
676:        // Check for the epochLen value to change
677:        if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {
678:            nextEpochLen = uint32(_epochLen);
679:        } else {
680:            _epochLen = epochLen;
681:        }
682:
683:        // Adjust veOLAS threshold for the next epoch
684:        if (uint96(_veOLASThreshold) > 0) {
685:            nextVeOLASThreshold = uint96(_veOLASThreshold);	// @audit-issue

703:    function changeIncentiveFractions(
704:        uint256 _rewardComponentFraction,
705:        uint256 _rewardAgentFraction,
706:        uint256 _maxBondFraction,
707:        uint256 _topUpComponentFraction,
708:        uint256 _topUpAgentFraction,
709:        uint256 _stakingFraction
710:    ) external {
711:        // Check for the contract ownership
712:        if (msg.sender != owner) {
713:            revert OwnerOnly(msg.sender, owner);
714:        }
715:
716:        // Check that the sum of fractions is 100%
717:        if (_rewardComponentFraction + _rewardAgentFraction > 100) {
718:            revert WrongAmount(_rewardComponentFraction + _rewardAgentFraction, 100);
719:        }
720:
721:        // Same check for top-up fractions
722:        uint256 sumTopUpFractions = _maxBondFraction + _topUpComponentFraction + _topUpAgentFraction +
723:            _stakingFraction;
724:        if (sumTopUpFractions > 100) {
725:            revert WrongAmount(sumTopUpFractions, 100);
726:        }
727:
728:        // All the adjustments will be accounted for in the next epoch
729:        uint256 eCounter = epochCounter + 1;
730:        TokenomicsPoint storage tp = mapEpochTokenomics[eCounter];
731:        // 0 stands for components and 1 for agents
732:        tp.unitPoints[0].rewardUnitFraction = uint8(_rewardComponentFraction);
733:        tp.unitPoints[1].rewardUnitFraction = uint8(_rewardAgentFraction);
734:        // Rewards are always distributed in full: the leftovers will be allocated to treasury
735:        tp.epochPoint.rewardTreasuryFraction = uint8(100 - _rewardComponentFraction - _rewardAgentFraction);
736:
737:        tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);
738:        tp.unitPoints[0].topUpUnitFraction = uint8(_topUpComponentFraction);
739:        tp.unitPoints[1].topUpUnitFraction = uint8(_topUpAgentFraction);
740:        mapEpochStakingPoints[eCounter].stakingFraction = uint8(_stakingFraction);	// @audit-issue
```
[622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L616-L622), [653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L639-L653), [661](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L639-L661), [678](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L639-L678), [685](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L639-L685), [740](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L703-L740), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

705:    function setDepositProcessorChainIds(address[] memory depositProcessors, uint256[] memory chainIds) external {
706:        // Check for the ownership
707:        if (msg.sender != owner) {
708:            revert OwnerOnly(msg.sender, owner);
709:        }
710:
711:        // Check for array length correctness
712:        if (depositProcessors.length == 0 || depositProcessors.length != chainIds.length) {
713:            revert WrongArrayLength(depositProcessors.length, chainIds.length);
714:        }
715:
716:        // Link L1 and L2 bridge mediators, set L2 chain Ids
717:        for (uint256 i = 0; i < chainIds.length; ++i) {
718:            // Check supported chain Ids on L2
719:            if (chainIds[i] == 0) {
720:                revert ZeroValue();
721:            }
722:
723:            // Note: depositProcessors[i] might be zero if there is a need to stop processing a specific L2 chain Id
724:            mapChainIdDepositProcessors[chainIds[i]] = depositProcessors[i];	// @audit-issue

1241:    function setPauseState(Pause pauseState) external {
1242:        // Check the contract ownership
1243:        if (msg.sender != owner) {
1244:            revert OwnerOnly(msg.sender, owner);
1245:        }
1246:
1247:        paused = pauseState;	// @audit-issue
```
[724](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L705-L724), [1247](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1241-L1247), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

113:    function setImplementationsCheck(bool setCheck) external {
114:        // Check the contract ownership
115:        if (owner != msg.sender) {
116:            revert OwnerOnly(owner, msg.sender);
117:        }
118:
119:        // Set the implementations check requirement
120:        implementationsCheck = setCheck;	// @audit-issue

131:    function setImplementationsStatuses(
132:        address[] memory implementations,
133:        bool[] memory statuses,
134:        bool setCheck
135:    ) external {
136:        // Check the contract ownership
137:        if (owner != msg.sender) {
138:            revert OwnerOnly(owner, msg.sender);
139:        }
140:
141:        // Check for the array length and that they are not empty
142:        if (implementations.length == 0 || implementations.length != statuses.length) {
143:            revert WrongArrayLength(implementations.length, statuses.length);
144:        }
145:
146:        // Set the implementations address check requirement
147:        implementationsCheck = setCheck;	// @audit-issue

131:    function setImplementationsStatuses(
132:        address[] memory implementations,
133:        bool[] memory statuses,
134:        bool setCheck
135:    ) external {
136:        // Check the contract ownership
137:        if (owner != msg.sender) {
138:            revert OwnerOnly(owner, msg.sender);
139:        }
140:
141:        // Check for the array length and that they are not empty
142:        if (implementations.length == 0 || implementations.length != statuses.length) {
143:            revert WrongArrayLength(implementations.length, statuses.length);
144:        }
145:
146:        // Set the implementations address check requirement
147:        implementationsCheck = setCheck;
148:
149:        // Set implementations whitelisting status
150:        for (uint256 i = 0; i < implementations.length; ++i) {
151:            // Check for the zero address
152:            if (implementations[i] == address(0)) {
153:                revert ZeroAddress();
154:            }
155:            
156:            // Set the operator whitelisting status
157:            mapImplementations[implementations[i]] = statuses[i];	// @audit-issue
```
[120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L113-L120), [147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L131-L147), [157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L131-L157), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

127:    function changeVerifier(address newVerifier) external {
128:        // Check for the ownership
129:        if (msg.sender != owner) {
130:            revert OwnerOnly(msg.sender, owner);
131:        }
132:
133:        verifier = newVerifier;	// @audit-issue
```
[133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L127-L133), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

386:    function changeDispenser(address newDispenser) external {
387:        // Check for the contract ownership
388:        if (msg.sender != owner) {
389:            revert OwnerOnly(msg.sender, owner);
390:        }
391:
392:        dispenser = newDispenser;	// @audit-issue
```
[392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L386-L392), 


#### Recommendation

Integrate a timelock mechanism into your Solidity contracts for critical functions, especially those controlling protocol parameters or managing user funds. Consider using established solutions like OpenZeppelin's `TimelockController` for robustness and reliability. Alternatively, develop a custom timelock mechanism tailored to your specific requirements. Ensure that the timelock duration is appropriate for your contract's use case and stakeholder needs, providing sufficient time for review and intervention. Clearly document and communicate the presence and workings of the timelock mechanism to stakeholders to maintain transparency and trust in your contract's operations.

### Consider bounding input array length
If the number of for loop iterations is unbounded, then it may lead to the transaction to run out of gas. While the function will revert if it eventually runs out of gas, it may be a nicer user experience to require() that the length of the array is below some reasonable maximum, so that the user doesn't have to use up a full transaction's gas only to see that the transaction reverts.

```solidity
Path: ./tokenomics/contracts/Dispenser.sol

485:                for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {	// @audit-issue

600:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue
```
[485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L485-L485), [600](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L600-L600), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

94:        for (uint256 i = 0; i < targets.length; ++i) {	// @audit-issue
```
[94](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L94-L94), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

154:        for (uint256 i = 0; i < targets.length; ++i) {	// @audit-issue
```
[154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L154-L154), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

332:        for (uint256 i = 0; i < _stakingParams.agentIds.length; ++i) {	// @audit-issue
```
[332](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L332-L332), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

92:        for (uint256 i = 0; i < serviceAgentIds.length; ++i) {	// @audit-issue
```
[92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L92-L92), 


#### Recommendation

Implement a check at the beginning of your Solidity functions to enforce a maximum length on input arrays. Use a `require()` statement to validate that the length of any input array does not exceed a predetermined limit, which should be chosen based on the function's complexity and typical gas usage. This ensures that the function will not attempt to process more data than it can handle within reasonable gas limits, thereby preventing out-of-gas errors and improving the overall user experience. Clearly document this behavior and the rationale behind the chosen array size limit to inform users and developers interacting with your contract.

### Don't use assembly for create2
Using assembly for create2 is error-prone and harder to read than higher-level Solidity. With the evolution of the Solidity language, a more abstracted and clear syntax for salted contract creation, which leverages the create2 opcode, has been introduced. Instead of manually managing assembly code, developers are encouraged to use the modern syntax, ensuring better readability, maintainability, and reduced chances of mistakes.

```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

208:            instance := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt)	// @audit-issue
```
[208](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L208-L208), 


#### Recommendation

It is recommended to not use `create` in assembly.

### Possible loss of precision
Division by large numbers may result in precision loss due to rounding down, or even the result being erroneously equal to zero. Consider adding checks on the numerator to ensure precision loss is handled appropriately.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

473:        uint256 _inflationPerSecond = getInflationForYear(0) / zeroYearSecondsLeft;	// @audit-issue

872:            totalIncentives = mapUnitIncentives[unitType][unitId].topUp + totalIncentives / sumUnitIncentives;	// @audit-issue

928:                    uint96 amount = uint96(amounts[i] / numServiceUnits);	// @audit-issue

1126:        uint256 numYears = (block.timestamp - timeLaunch) / ONE_YEAR;	// @audit-issue

1136:            curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;	// @audit-issue

1243:        numYears = (block.timestamp + curEpochLen - timeLaunch) / ONE_YEAR;	// @audit-issue

1252:            curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;	// @audit-issue

1451:                    topUp += totalIncentives / sumUnitIncentives;	// @audit-issue
```
[473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L473-L473), [872](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L872-L872), [928](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L928-L928), [1126](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1126-L1126), [1136](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1136-L1136), [1243](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1243-L1243), [1252](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1252-L1252), [1451](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1451-L1451), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

932:                    uint256 normalizedStakingAmount = stakingIncentive / (10 ** (18 - bridgingDecimals));	// @audit-issue

1221:            uint256 normalizedAmount = amount / (10 ** (18 - bridgingDecimals));	// @audit-issue
```
[932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L932-L932), [1221](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1221-L1221), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

58:            uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts;	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L58-L58), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

622:                    updatedReward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards;	// @audit-issue

633:                updatedReward = (eligibleServiceRewards[0] * lastAvailableRewards) / totalRewards;	// @audit-issue

904:                    reward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards;	// @audit-issue
```
[622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L622-L622), [633](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L633-L633), [904](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L904-L904), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

214:        timeSum = block.timestamp / WEEK * WEEK;	// @audit-issue

309:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;	// @audit-issue

424:        uint256 t = time / WEEK * WEEK;	// @audit-issue

432:            weight = 1e18 * nomineeWeight / totalSum;	// @audit-issue

489:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;	// @audit-issue

515:            slope: uint256(uint128(userSlope)) * weight / MAX_WEIGHT,	// @audit-issue

605:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;	// @audit-issue
```
[214](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L214-L214), [309](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L309-L309), [424](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L424-L424), [432](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L432-L432), [489](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L489-L489), [515](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L515-L515), [605](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L605-L605), 


#### Recommendation

Incorporate strategies in your Solidity contracts to mitigate precision loss in division operations. This can include:
1. Performing checks on the numerator and denominator to ensure they are within a reasonable range to avoid significant rounding errors.
2. Considering the use of fixed-point arithmetic libraries or scaling factors to handle divisions with higher precision.
3. Clearly documenting any inherent limitations of your division logic and providing guidelines for inputs to minimize unexpected behavior.
Always thoroughly test division operations under various scenarios to ensure that the outcomes are consistent with your contract's intended logic and accuracy requirements.

### A year is not always 365 days
On leap years, the number of days is 366, so calculations during those years will return the wrong value

```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

15:    uint256 public constant ONE_YEAR = 1 days * 365;	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L15-L15), 


#### Recommendation

Incorporate leap year recognition into your smart contract's time-based calculations to ensure accuracy across all years. Utilize a function that calculates the exact number of days in a given year by checking for leap years. For example, a year is a leap year if it is divisible by 4 but not by 100, unless it is also divisible by 400.
### Floating Pragma
A "floating pragma" in Solidity refers to the practice of using a pragma statement that does not specify a fixed compiler version but instead allows the contract to be compiled with any compatible compiler version. This issue arises when pragma statements like `pragma solidity ^0.8.0;` are used without a specific version number, allowing the contract to be compiled with the latest available compiler version. This can lead to various compatibility and stability issues.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/interfaces/IDonatorBlacklist.sol

2:pragma solidity ^0.8.18;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/interfaces/IBridgeErrors.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/interfaces/IErrorsTokenomics.sol

2:pragma solidity ^0.8.18;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L2-L2), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

2:pragma solidity ^0.8.23;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L2-L2), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L2-L2), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L2-L2), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L2-L2), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L2-L2), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L2-L2), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L2-L2), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L2-L2), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

2:pragma solidity ^0.8.25;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L2-L2), 


#### Recommendation

Consider locking the pragma version whenever possible and avoid using a floating pragma in the final deployment. [Consider known bugs](https://github.com/ethereum/solidity/releases) for the compiler version that is chosen.

### Unneeded initializations of integer variable to `0`.
In Solidity, it is common practice to initialize variables with default values when declaring them. However, initializing integer variables to `0` when they are not subsequently used in the code can lead to unnecessary gas consumption and code clutter. This issue points out instances where such initializations are present but serve no functional purpose.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

930:                    for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue

960:                for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue

916:            for (uint256 unitType = 0; unitType < 2; ++unitType) {	// @audit-issue

904:        for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue

1010:        for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue

1199:            for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1329:        for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1335:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1357:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1401:        for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1407:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1429:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue
```
[930](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L930-L930), [960](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L960-L960), [916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L916-L916), [904](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L904-L904), [1010](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1010-L1010), [1199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1199-L1199), [1329](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1329-L1329), [1335](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1335-L1335), [1357](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1357-L1357), [1401](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1401-L1401), [1407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1407-L1407), [1429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1429-L1429), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

58:            for (uint256 i = 0; i < numYears; ++i) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L58-L58), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

462:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

473:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

485:                for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {	// @audit-issue

450:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

550:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

526:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

600:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

593:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

717:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue
```
[462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L462-L462), [473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L473-L473), [485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L485-L485), [450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L450-L450), [550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L550-L550), [526](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L526-L526), [600](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L600-L600), [593](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L593-L593), [717](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L717-L717), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

94:        for (uint256 i = 0; i < targets.length; ++i) {	// @audit-issue
```
[94](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L94-L94), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

150:        uint256 localWithheldAmount = 0;	// @audit-issue

154:        for (uint256 i = 0; i < targets.length; ++i) {	// @audit-issue
```
[150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L150-L150), [154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L154-L154), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

150:        for (uint256 i = 0; i < implementations.length; ++i) {	// @audit-issue
```
[150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L150-L150), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

332:        for (uint256 i = 0; i < _stakingParams.agentIds.length; ++i) {	// @audit-issue

380:        for (uint256 i = 0; i < numAgentIds; ++i) {	// @audit-issue

449:        for (uint256 i = 0; i < totalNumServices; ++i) {	// @audit-issue

548:            for (uint256 i = 0; i < size; ++i) {	// @audit-issue

648:                for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue

669:            for (uint256 i = 0; i < serviceIds.length; ++i) {	// @audit-issue

773:            for (uint256 i = 0; i < numAgents; ++i) {	// @audit-issue

899:        for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue
```
[332](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L332-L332), [380](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L380-L380), [449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L449-L449), [548](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L548-L548), [648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L648-L648), [669](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L669-L669), [773](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L773-L773), [899](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L899-L899), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

92:        for (uint256 i = 0; i < serviceAgentIds.length; ++i) {	// @audit-issue
```
[92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L92-L92), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

227:        for (uint256 i = 0; i < MAX_NUM_WEEKS; i++) {	// @audit-issue

267:        for (uint256 i = 0; i < MAX_NUM_WEEKS; i++) {	// @audit-issue

573:        for (uint256 i = 0; i < accounts.length; ++i) {	// @audit-issue

793:        for (uint256 i = 0; i < accounts.length; ++i) {	// @audit-issue
```
[227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L227-L227), [267](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L267-L267), [573](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L573-L573), [793](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L793-L793), 


#### Recommendation


It is recommended not to initialize integer variables to `0` to save some Gas.


### Use transfer libraries instead of low level calls
Consider using `SafeTransferLib.safeTransferETH` or `Address.sendValue` for clearer semantic meaning instead of using a low level call.

```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);	// @audit-issue

396:        (bool success, ) = msg.sender.call{value: amount}("");	// @audit-issue
```
[161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L161), [396](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L396-L396), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

33:        (bool success, ) = to.call{value: amount}("");	// @audit-issue
```
[33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L33-L33), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

216:        (bool success, bytes memory returnData) = instance.call(initPayload);	// @audit-issue
```
[216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L216-L216), 


#### Recommendation

To improve code readability and safety, consider using higher-level transfer libraries like `SafeTransferLib.safeTransferETH` or `Address.sendValue` instead of low-level calls for handling Ether transfers. These libraries provide clearer semantic meaning and help prevent common pitfalls associated with low-level calls.

### Unused arguments should be removed or implemented
In Solidity, functions often have arguments that are intended for specific operations or logic within the function. However, sometimes these arguments remain unused in the function body, either due to changes during development or oversight. Unused arguments in functions can lead to confusion, making the code less readable and potentially misleading for other developers who might use or audit the contract. Moreover, they can create a false impression of the function's purpose and behavior. It's crucial to either implement these arguments in the function's logic as originally intended or remove them to ensure clarity and efficiency of the code.

```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

53:    function _sendMessage(uint256 amount, bytes memory) internal override {	// @audit-issue: None
```
[53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L53-L53), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

134:        uint256	// @audit-issue: None

159:        uint256	// @audit-issue: None
```
[134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L134-L134), [159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L159-L159), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

32:    function _sendMessage(uint256 amount, bytes memory) internal override {	// @audit-issue: None

52:    function _processMessageFromRoot(uint256, address sender, bytes memory data) internal override {	// @audit-issue: None
```
[32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L32-L32), [52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L52-L52), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

108:        bytes[] memory,	// @audit-issue: None
```
[108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L108-L108), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

99:    function onTokenBridged(address, uint256, bytes calldata data) external {	// @audit-issue: None
```
[99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L99-L99), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

60:        bytes memory,	// @audit-issue: None
```
[60](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L60-L60), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

255:    function getEmissionsAmountLimit(address) external view returns (uint256) {	// @audit-issue: None
```
[255](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L255-L255), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

369:        uint32[] memory	// @audit-issue: None
```
[369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L369-L369), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

74:    function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {	// @audit-issue: None
```
[74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L74-L74), 


#### Recommendation

Eliminate unused arguments in Solidity function definitions to enhance code clarity and efficiency. If an argument is currently not utilized in the function's logic, assess its potential future utility. If it serves no purpose, removing it simplifies the function signature and aligns the code more closely with its actual operation, contributing to a cleaner and more understandable contract structure.

### Unused import
The identifier is imported but never used within the file.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

7:import {IErrorsTokenomics} from "./interfaces/IErrorsTokenomics.sol";	// @audit-issue: IErrorsTokenomics not used in the contract.
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L7-L7), 


#### Recommendation

Regularly review your Solidity code to remove unused imports. This practice declutters the codebase, making it easier to understand and maintain. In addition, consider using tools or IDE features that can automatically detect and highlight unused imports for cleanup. Keeping imports limited to only what is necessary helps maintain a clear understanding of the contract's dependencies and reduces potential confusion for developers working on or reviewing the code.

### Excessive Authorization in Contract Functions
A contract where a high percentage of functions require authorization (e.g., restricted to the contract owner or specific roles) may indicate over-centralization or excessive control. This could limit the contract's flexibility, reduce trust among users, and potentially create bottleneck points that could be exploited or become failure points. While some level of control is necessary for administrative purposes, overly restrictive access can detract from the decentralized nature of blockchain applications and concentrate too much power in the hands of a few.

```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

11:contract PolygonTargetDispenserL2 is DefaultTargetDispenserL2, FxBaseChildTunnel {	// @audit-issue: %100.0 amount of external/public and non-view/non-pure functions are required authorization to call.
	List of total functions: `setFxRootTunnel`
	List of functions that require authorization: `setFxRootTunnel`
	List of functions that doesn't require authorization: None
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

45:abstract contract DefaultTargetDispenserL2 is IBridgeErrors {	// @audit-issue: %75.0 amount of external/public and non-view/non-pure functions are required authorization to call.
	List of total functions: `migrate`, `redeem`, `unpause`, `syncWithheldTokens`, `pause`, `processDataMaintenance`, `drain`, `changeOwner`
	List of functions that require authorization: `migrate`, `changeOwner`, `pause`, `processDataMaintenance`, `drain`, `unpause`
	List of functions that doesn't require authorization: `syncWithheldTokens`, `redeem`
```
[45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L45-L45), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

43:contract StakingVerifier {	// @audit-issue: %100.0 amount of external/public and non-view/non-pure functions are required authorization to call.
	List of total functions: `setImplementationsStatuses`, `changeStakingLimits`, `changeOwner`, `setImplementationsCheck`
	List of functions that require authorization: `setImplementationsStatuses`, `changeStakingLimits`, `changeOwner`, `setImplementationsCheck`
	List of functions that doesn't require authorization: None
```
[43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L43-L43), 


#### Recommendation

Make contract more decentralized.

### `public` functions not called by the contract should be declared `external` instead
In Solidity, function visibility is an important aspect that determines how and where a function can be called from. Two commonly used visibilities are `public` and `external`. A `public` function can be called both from other functions inside the same contract and from outside transactions, while an `external` function can only be called from outside the contract.
A potential pitfall in smart contract development is the misuse of the `public` keyword for functions that are only meant to be accessed externally. When a function is not used internally within a contract and is only intended for external calls, it should be labeled as `external` rather than `public`. Using `public` unnecessarily can introduce potential vulnerabilities and also make the contract consume more gas than required. This is because `public` functions have to add additional code to handle both internal and external calls, while `external` functions can be more optimized since they only handle external calls.


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

33:    function getSupplyCapForYear(uint256 numYears) public pure returns (uint256 supplyCap) {	// @audit-issue
```
[33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L33-L33), 


#### Recommendation

To optimize gas usage and improve code clarity, declare functions that are not called internally within the contract and are intended for external access as `external` rather than `public`. This ensures that these functions are only callable externally, reducing unnecessary gas consumption and potential security risks.

### Functions contain the same code
The functions below have the same implementation as is seen in other files. The functions should be refactored into functions of a common base contract.

```solidity
Path: ./tokenomics/contracts/Dispenser.sol

636:    function changeOwner(address newOwner) external {	// @audit-issue: Seen on line 546 of ./tokenomics/contracts/Tokenomics.sol
```
[636](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L636-L636), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

173:    function getBridgingDecimals() external pure returns (uint256) {	// @audit-issue: Seen on line 212 of ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol
```
[173](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L173-L173), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

247:    function changeOwner(address newOwner) external {	// @audit-issue: Seen on line 546 of ./tokenomics/contracts/Tokenomics.sol
```
[247](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L247-L247), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

96:    function changeOwner(address newOwner) external {	// @audit-issue: Seen on line 546 of ./tokenomics/contracts/Tokenomics.sol
```
[96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L96-L96), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

110:    function changeOwner(address newOwner) external {	// @audit-issue: Seen on line 546 of ./tokenomics/contracts/Tokenomics.sol
```
[110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L110-L110), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

368:    function changeOwner(address newOwner) external {	// @audit-issue: Seen on line 546 of ./tokenomics/contracts/Tokenomics.sol
```
[368](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L368-L368), 


#### Recommendation

Identify and consolidate duplicated code blocks in Solidity contracts into reusable modifiers or functions. This approach streamlines the contract by eliminating redundancy, thereby improving clarity, maintainability, and potentially reducing gas costs. For common checks, consider using modifiers for concise and consistent enforcement of conditions. For reusable logic, encapsulate it in functions to avoid code duplication and simplify future updates or bug fixes.

### `if`-statement can be converted to a ternary
The code can be made more compact while also increasing readability by converting the following `if`-statements to ternaries (e.g. `foo += (x > y) ? a : b`)

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

652:        if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {	// @audit-issue
653:            devsPerCapital = uint72(_devsPerCapital);
654:        } else {
655:            // This is done in order not to pass incorrect parameters into the event
656:            _devsPerCapital = devsPerCapital;
657:        }

660:        if (uint72(_codePerDev) > MIN_PARAM_VALUE) {	// @audit-issue
661:            codePerDev = uint72(_codePerDev);
662:        } else {
663:            // This is done in order not to pass incorrect parameters into the event
664:            _codePerDev = codePerDev;
665:        }

670:        if (_epsilonRate > 0 && _epsilonRate <= 17e18) {	// @audit-issue
671:            epsilonRate = uint64(_epsilonRate);
672:        } else {
673:            _epsilonRate = epsilonRate;
674:        }

677:        if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {	// @audit-issue
678:            nextEpochLen = uint32(_epochLen);
679:        } else {
680:            _epochLen = epochLen;
681:        }

684:        if (uint96(_veOLASThreshold) > 0) {	// @audit-issue
685:            nextVeOLASThreshold = uint96(_veOLASThreshold);
686:        } else {
687:            _veOLASThreshold = veOLASThreshold;
688:        }

1057:        if (fKD > epsilonRate) {	// @audit-issue
1058:            fKD = epsilonRate;
1059:        }

1102:        if (diffNumSeconds < curEpochLen || diffNumSeconds > MAX_EPOCH_LENGTH) {	// @audit-issue
1103:            return false;
1104:        }
```
[652](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L652-L657), [660](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L660-L665), [670](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L670-L674), [677](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L677-L681), [684](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L684-L688), [1057](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1057-L1059), [1102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1102-L1104), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

392:        if (epochAfterRemoved > 1 && lastClaimedEpoch > epochAfterRemoved) {	// @audit-issue
393:            lastClaimedEpoch = epochAfterRemoved;
394:        }

397:        if (lastClaimedEpoch > eCounter) {	// @audit-issue
398:            lastClaimedEpoch = eCounter;
399:        }

419:        if (transferAmount > 0) {	// @audit-issue
420:            IToken(olas).transfer(depositProcessor, transferAmount);
421:        }

455:            if (transferAmounts[i] > 0) {	// @audit-issue
456:                IToken(olas).transfer(depositProcessor, transferAmounts[i]);
457:            }

1000:        if (returnAmount > 0) {	// @audit-issue
1001:            ITokenomics(tokenomics).refundFromStaking(returnAmount);
1002:        }

1098:        if (totalAmounts[2] > 0) {	// @audit-issue
1099:            ITokenomics(tokenomics).refundFromStaking(totalAmounts[2]);
1100:        }

1161:        if (totalReturnAmount > 0) {	// @audit-issue
1162:            ITokenomics(tokenomics).refundFromStaking(totalReturnAmount);
1163:        }
```
[392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L392-L394), [397](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L397-L399), [419](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L419-L421), [455](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L455-L457), [1000](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1000-L1002), [1098](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1098-L1100), [1161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1161-L1163), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

135:        if (refundAccount == address(0)) {	// @audit-issue
136:            refundAccount = msg.sender;
137:        }
```
[135](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L135-L137), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

76:        if (gasLimitMessage < GAS_LIMIT) {	// @audit-issue
77:            gasLimitMessage = GAS_LIMIT;
78:        }

80:        if (gasLimitMessage > MAX_GAS_LIMIT) {	// @audit-issue
81:            gasLimitMessage = MAX_GAS_LIMIT;
82:        }
```
[76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L76-L78), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L80-L82), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

98:        if (refundAccount == address(0)) {	// @audit-issue
99:            refundAccount = msg.sender;
100:        }

103:        if (gasLimitMessage < GAS_LIMIT) {	// @audit-issue
104:            gasLimitMessage = GAS_LIMIT;
105:        }

107:        if (gasLimitMessage > MAX_GAS_LIMIT) {	// @audit-issue
108:            gasLimitMessage = MAX_GAS_LIMIT;
109:        }
```
[98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L98-L100), [103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L103-L105), [107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L107-L109), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

84:        if (refundAccount == address(0)) {	// @audit-issue
85:            refundAccount = msg.sender;
86:        }
```
[84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L84-L86), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

68:        if (gasLimitMessage < GAS_LIMIT) {	// @audit-issue
69:            gasLimitMessage = GAS_LIMIT;
70:        }

72:        if (gasLimitMessage > MAX_GAS_LIMIT) {	// @audit-issue
73:            gasLimitMessage = MAX_GAS_LIMIT;
74:        }
```
[68](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L68-L70), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L72-L74), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

165:            if (success && returnData.length == 32) {	// @audit-issue
166:                limitAmount = abi.decode(returnData, (uint256));
167:            }

208:        if (localWithheldAmount > 0) {	// @audit-issue
209:            withheldAmount += localWithheldAmount;
210:        }
```
[165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L165-L167), [208](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L208-L210), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

168:        if (implementationsCheck) {	// @audit-issue
169:            return mapImplementations[implementation];
170:        }

181:        if (implementationsCheck && !mapImplementations[implementation]) {	// @audit-issue
182:            return false;
183:        }

186:        if (instance.code.length == 0) {	// @audit-issue
187:            return false;
188:        }

193:        if (rewardsPerSecond > rewardsPerSecondLimit) {	// @audit-issue
194:            return false;
195:        }

200:        if (numServices > numServicesLimit) {	// @audit-issue
201:            return false;
202:        }
```
[168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L168-L170), [181](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L181-L183), [186](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L186-L188), [193](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L193-L195), [200](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L200-L202), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

490:        if (execCheckPoint) {	// @audit-issue
491:            checkpoint();
492:        }

867:        if (reward > 0) {	// @audit-issue
868:            _withdraw(multisig, reward);
869:        }

932:        } else if (sInfo.tsStart > 0) {	// @audit-issue
933:            stakingState = StakingState.Staked;
934:        }
```
[490](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L490-L492), [867](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L867-L869), [932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L932-L934), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

273:        if (implementation == address(0)) {	// @audit-issue
274:            return false;
275:        }

278:        if (!instanceParams.isEnabled) {	// @audit-issue
279:            return false;
280:        }

284:        if (localVerifier != address(0)) {	// @audit-issue
285:            return IStakingVerifier(localVerifier).verifyInstance(instance, implementation);
286:        }
```
[273](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L273-L275), [278](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L278-L280), [284](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L284-L286), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

314:        if (localDispenser != address(0)) {	// @audit-issue
315:            IDispenser(localDispenser).addNominee(nomineeHash);
316:        }

510:        if (oldSlope.end > nextTime) {	// @audit-issue
511:            oldBias = oldSlope.slope * (oldSlope.end - nextTime);
512:        }

631:        if (localDispenser != address(0)) {	// @audit-issue
632:            IDispenser(localDispenser).removeNominee(nomineeHash);
633:        }
```
[314](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L314-L316), [510](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L510-L512), [631](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L631-L633), 


#### Recommendation

Consider using single line if statements as ternary.

### Custom error has no error details
Consider adding parameters to the error to indicate which user or values caused the failure

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

75:error ZeroAddress();	// @audit-issue

87:error ZeroValue();	// @audit-issue

113:error AlreadyInitialized();	// @audit-issue

116:error DelegatecallOnly();	// @audit-issue

119:error SameBlockNumberViolation();	// @audit-issue
```
[75](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L75-L75), [87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L87-L87), [113](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L113-L113), [116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L116-L116), [119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L119-L119), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

199:error ZeroAddress();	// @audit-issue

207:error ZeroValue();	// @audit-issue

225:error ReentrancyGuard();	// @audit-issue

228:error Paused();	// @audit-issue
```
[199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L199-L199), [207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L207-L207), [225](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L225-L225), [228](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L228-L228), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

32:error ZeroAddress();	// @audit-issue

35:error ReentrancyGuard();	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L32-L32), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L35-L35), 


```solidity
Path: ./tokenomics/contracts/interfaces/IBridgeErrors.sol

16:    error ZeroAddress();	// @audit-issue

19:    error ZeroValue();	// @audit-issue

84:    error Paused();	// @audit-issue

87:    error Unpaused();	// @audit-issue

90:    error ReentrancyGuard();	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L16-L16), [19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L19-L19), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L84-L84), [87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L87-L87), [90](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L90-L90), 


```solidity
Path: ./tokenomics/contracts/interfaces/IErrorsTokenomics.sol

17:    error ZeroAddress();	// @audit-issue

29:    error ZeroValue();	// @audit-issue

32:    error NonZeroValue();	// @audit-issue

103:    error ReentrancyGuard();	// @audit-issue

119:    error AlreadyInitialized();	// @audit-issue

122:    error DelegatecallOnly();	// @audit-issue

125:    error Paused();	// @audit-issue

128:    error SameBlockNumberViolation();	// @audit-issue
```
[17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L17-L17), [29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L29-L29), [32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L32-L32), [103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L103-L103), [119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L119-L119), [122](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L122-L122), [125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L125-L125), [128](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L128-L128), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

20:error ZeroAddress();	// @audit-issue

23:error ZeroValue();	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L20-L20), [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L23-L23), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

12:error ZeroValue();	// @audit-issue
```
[12](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L12-L12), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

93:error ZeroAddress();	// @audit-issue

96:error ZeroValue();	// @audit-issue

116:error AlreadyInitialized();	// @audit-issue

119:error NoRewardsAvailable();	// @audit-issue
```
[93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L93-L93), [96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L96-L96), [116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L116-L116), [119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L119-L119), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

8:error ZeroTokenAddress();	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L8-L8), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

5:error ZeroImplementationAddress();	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L5-L5), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

37:error ZeroAddress();	// @audit-issue

65:error ReentrancyGuard();	// @audit-issue
```
[37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L37-L37), [65](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L65-L65), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

50:error ZeroAddress();	// @audit-issue

53:error ZeroValue();	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L50-L50), [53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L53-L53), 


#### Recommendation

When defining custom errors, consider adding parameters or error details that provide information about the specific conditions or inputs that caused the error. Including error details can make debugging and troubleshooting easier by providing context on the cause of the failure.

### Consider moving `msg.sender` checks to `modifier`s
If some functions are only allowed to be called by some specific users, consider using a modifier instead of checking with a require statement, especially if this check is done in multiple functions.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

528:        if (msg.sender != owner) {
529:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

548:        if (msg.sender != owner) {
549:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

567:        if (msg.sender != owner) {
568:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

594:        if (msg.sender != owner) {
595:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

618:        if (msg.sender != owner) {
619:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

647:        if (msg.sender != owner) {
648:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

712:        if (msg.sender != owner) {
713:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

753:        if (msg.sender != owner) {
754:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

789:        if (depository != msg.sender) {
790:            revert ManagerOnly(msg.sender, depository);	// @audit-issue

810:        if (depository != msg.sender) {
811:            revert ManagerOnly(msg.sender, depository);	// @audit-issue

828:        if (dispenser != msg.sender) {
829:            revert ManagerOnly(msg.sender, depository);	// @audit-issue

997:        if (treasury != msg.sender) {
998:            revert ManagerOnly(msg.sender, treasury);	// @audit-issue

1314:        if (dispenser != msg.sender) {
1315:            revert ManagerOnly(msg.sender, dispenser);	// @audit-issue
```
[529](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L528-L529), [549](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L548-L549), [568](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L567-L568), [595](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L594-L595), [619](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L618-L619), [648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L647-L648), [713](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L712-L713), [754](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L753-L754), [790](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L789-L790), [811](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L810-L811), [829](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L828-L829), [998](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L997-L998), [1315](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1314-L1315), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

638:        if (msg.sender != owner) {
639:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

657:        if (msg.sender != owner) {
658:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

685:        if (msg.sender != owner) {
686:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

707:        if (msg.sender != owner) {
708:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

734:        if (msg.sender != voteWeighting) {
735:            revert ManagerOnly(msg.sender, voteWeighting);	// @audit-issue

752:        if (msg.sender != voteWeighting) {
753:            revert ManagerOnly(msg.sender, voteWeighting);	// @audit-issue

830:        if (!success) {
831:            revert ClaimIncentivesFailed(msg.sender, reward, topUp);	// @audit-issue

1178:        if (msg.sender != depositProcessor) {
1179:            revert DepositProcessorOnly(msg.sender, depositProcessor);	// @audit-issue

1201:        if (msg.sender != owner) {
1202:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

1243:        if (msg.sender != owner) {
1244:            revert OwnerOnly(msg.sender, owner);	// @audit-issue
```
[639](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L638-L639), [658](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L657-L658), [686](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L685-L686), [708](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L707-L708), [735](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L734-L735), [753](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L752-L753), [831](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L830-L831), [1179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1178-L1179), [1202](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1201-L1202), [1244](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1243-L1244), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

198:        if (msg.sender != bridge) {
199:            revert TargetRelayerOnly(msg.sender, bridge);	// @audit-issue
```
[199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L198-L199), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

109:        if (l1Relayer != l1MessageRelayer) {
110:            revert TargetRelayerOnly(msg.sender, l1MessageRelayer);	// @audit-issue

139:        if (msg.sender != l1Dispenser) {
140:            revert ManagerOnly(l1Dispenser, msg.sender);	// @audit-issue

171:        if (msg.sender != l1Dispenser) {
172:            revert ManagerOnly(l1Dispenser, msg.sender);	// @audit-issue

188:        if (msg.sender != owner) {
189:            revert OwnerOnly(owner, msg.sender);	// @audit-issue
```
[110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L109-L110), [140](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L139-L140), [172](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L171-L172), [189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L188-L189), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

68:        if (msg.sender != l1AliasedDepositProcessor) {
69:            revert WrongMessageSender(msg.sender, l1AliasedDepositProcessor);	// @audit-issue
```
[69](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L68-L69), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

137:        if (msg.sender != dispenser) {
138:            revert ManagerOnly(dispenser, msg.sender);	// @audit-issue

162:        if (msg.sender != dispenser) {
163:            revert ManagerOnly(dispenser, msg.sender);	// @audit-issue
```
[138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L137-L138), [163](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L162-L163), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

61:        if (msg.sender != owner) {
62:            revert OwnerOnly(msg.sender, owner);	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L61-L62), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

101:        if (msg.sender != l2TokenRelayer) {
102:            revert TargetRelayerOnly(msg.sender, l2TokenRelayer);	// @audit-issue
```
[102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L101-L102), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

249:        if (msg.sender != owner) {
250:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

313:        if (msg.sender != owner) {
314:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

355:        if (msg.sender != owner) {
356:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

366:        if (msg.sender != owner) {
367:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

385:        if (msg.sender != owner) {
386:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

397:        if (!success) {
398:            revert TransferFailed(address(0), address(this), msg.sender, amount);	// @audit-issue

420:        if (msg.sender != owner) {
421:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

460:        if (owner == address(0)) {
461:            revert TransferFailed(address(0), msg.sender, address(this), msg.value);	// @audit-issue
```
[250](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L249-L250), [314](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L313-L314), [356](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L355-L356), [367](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L366-L367), [386](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L385-L386), [398](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L397-L398), [421](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L420-L421), [461](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L460-L461), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

100:        if (msg.sender != owner) {
101:            revert OwnerOnly(msg.sender, owner);	// @audit-issue
```
[101](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L100-L101), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

98:        if (msg.sender != owner) {
99:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

115:        if (owner != msg.sender) {
116:            revert OwnerOnly(owner, msg.sender);	// @audit-issue

137:        if (owner != msg.sender) {
138:            revert OwnerOnly(owner, msg.sender);	// @audit-issue

235:        if (owner != msg.sender) {
236:            revert OwnerOnly(owner, msg.sender);	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L98-L99), [116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L115-L116), [138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L137-L138), [236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L235-L236), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

485:        if (msg.sender != sInfo.owner) {
486:            revert OwnerOnly(msg.sender, sInfo.owner);	// @audit-issue

808:        if (msg.sender != sInfo.owner) {
809:            revert OwnerOnly(msg.sender, sInfo.owner);	// @audit-issue
```
[486](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L485-L486), [809](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L808-L809), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

112:        if (msg.sender != owner) {
113:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

129:        if (msg.sender != owner) {
130:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

255:        if (msg.sender != deployer) {
256:            revert OwnerOnly(msg.sender, deployer);	// @audit-issue
```
[113](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L112-L113), [130](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L129-L130), [256](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L255-L256), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

370:        if (msg.sender != owner) {
371:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

388:        if (msg.sender != owner) {
389:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

484:        if (userSlope < 0) {
485:            revert NegativeSlope(msg.sender, userSlope);	// @audit-issue

492:        if (nextTime >= lockEnd) {
493:            revert LockExpired(msg.sender, lockEnd, nextTime);	// @audit-issue

503:        if (nextAllowedVotingTime > block.timestamp) {
504:            revert VoteTooOften(msg.sender, block.timestamp, nextAllowedVotingTime);	// @audit-issue

588:        if (msg.sender != owner) {
589:            revert OwnerOnly(owner, msg.sender);	// @audit-issue
```
[371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L370-L371), [389](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L388-L389), [485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L484-L485), [493](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L492-L493), [504](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L503-L504), [589](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L588-L589), 


#### Recommendation

Consider refactoring your code by moving `msg.sender` checks to modifiers when certain functions are only allowed to be called by specific users. This approach can enhance code readability, reduce redundancy, and make it easier to maintain access control logic.

### Constants in comparisons should appear on the left side
Doing so will prevent [typo bugs](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html)

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

758:        if (_maxStakingIncentive == 0 || _minStakingWeight == 0) {	// @audit-issue

922:                if (numServiceUnits == 0) {	// @audit-issue

934:                        if (lastEpoch == 0) {	// @audit-issue

1174:        if (tokenomicsParametersUpdated & 0x01 == 0x01) {	// @audit-issue

1194:        if (tokenomicsParametersUpdated & 0x02 == 0x02) {	// @audit-issue

1211:        if (tokenomicsParametersUpdated & 0x08 == 0x08) {	// @audit-issue

1285:        if (incentives[1] == 0 || ITreasury(treasury).rebalanceTreasury(incentives[1])) {	// @audit-issue
```
[758](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L758-L758), [922](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L922-L922), [934](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L934-L934), [1174](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1174-L1174), [1194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1194-L1194), [1211](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1211-L1211), [1285](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1285-L1285), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

331:            _voteWeighting == address(0) || _retainer == 0) {	// @audit-issue

336:        if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {	// @audit-issue

369:        if (firstClaimedEpoch == 0) {	// @audit-issue

535:            if (stakingTargets[i].length == 0) {	// @audit-issue

690:        if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {	// @audit-issue

712:        if (depositProcessors.length == 0 || depositProcessors.length != chainIds.length) {	// @audit-issue

719:            if (chainIds[i] == 0) {	// @audit-issue

741:            ITreasury(treasury).paused() == 2) {	// @audit-issue

802:            ITreasury(treasury).paused() == 2) {	// @audit-issue

861:        if (chainId == 0) {	// @audit-issue

866:        if (stakingTarget == 0) {	// @audit-issue

966:        if (chainId == 0) {	// @audit-issue

971:        if (stakingTarget == 0) {	// @audit-issue

984:            ITreasury(treasury).paused() == 2) {	// @audit-issue

1081:            ITreasury(treasury).paused() == 2) {	// @audit-issue

1206:        if (chainId == 0 || amount == 0) {	// @audit-issue
```
[331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L331-L331), [336](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L336-L336), [369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L369-L369), [535](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L535-L535), [690](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L690-L690), [712](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L712-L712), [719](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L719-L719), [741](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L741-L741), [802](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L802-L802), [861](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L861-L861), [866](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L866-L866), [966](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L966-L966), [971](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L971-L971), [984](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L984-L984), [1081](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1081-L1081), [1206](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1206-L1206), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

141:        if (gasPriceBid < 2 || gasLimitMessage < 2 || maxSubmissionCostMessage == 0) {	// @audit-issue

154:            if (maxSubmissionCostToken == 0) {	// @audit-issue
```
[141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L141-L141), [154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L154-L154), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

66:        if (cost == 0) {	// @audit-issue
```
[66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L66-L66), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

72:        if (_l2TargetChainId == 0) {	// @audit-issue
```
[72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L72-L72), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

121:        if (cost == 0 || gasLimitMessage == 0) {	// @audit-issue
```
[121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L121-L121), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

102:            if (limitAmount == 0) {	// @audit-issue
```
[102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L102-L102), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

154:        if (receivedTokens.length != 1) {	// @audit-issue
```
[154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L154-L154), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

80:            if (gasLimitMessage == 0) {	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L80-L80), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

46:        if (_wormholeTargetChainId == 0) {	// @audit-issue

74:        if (gasLimitMessage == 0) {	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L46-L46), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L74-L74), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

115:        if (_l1SourceChainId == 0) {	// @audit-issue

165:            if (success && returnData.length == 32) {	// @audit-issue

170:            if (limitAmount == 0) {	// @audit-issue

189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {	// @audit-issue

274:        if (paused == 2) {	// @audit-issue

331:        if (paused == 2) {	// @audit-issue

337:        if (amount == 0) {	// @audit-issue

391:        if (amount == 0) {	// @audit-issue

425:        if (paused == 1) {	// @audit-issue

430:        if (newL2TargetDispenser.code.length == 0) {	// @audit-issue
```
[115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L115-L115), [165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L165-L165), [170](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L170-L170), [189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L189-L189), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L274-L274), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L331-L331), [337](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L337-L337), [391](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L391-L391), [425](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L425-L425), [430](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L430-L430), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

82:        if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {	// @audit-issue

142:        if (implementations.length == 0 || implementations.length != statuses.length) {	// @audit-issue

186:        if (instance.code.length == 0) {	// @audit-issue

212:            if (returnData.length == 32) {	// @audit-issue

240:        if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {	// @audit-issue
```
[82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L82-L82), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L142-L142), [186](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L186-L186), [212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L212-L212), [240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L240-L240), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

26:        if (_livenessRatio == 0) {	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L26-L26), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

287:        if (_stakingParams.metadataHash == 0 || _stakingParams.maxNumServices == 0 ||	// @audit-issue

288:            _stakingParams.rewardsPerSecond == 0 || _stakingParams.livenessPeriod == 0 ||	// @audit-issue

289:            _stakingParams.numAgentInstances == 0 || _stakingParams.timeForEmissions == 0 ||	// @audit-issue

290:            _stakingParams.minNumStakingPeriods == 0 || _stakingParams.maxNumInactivityPeriods == 0) {	// @audit-issue

310:        if (_stakingParams.activityChecker.code.length == 0) {	// @audit-issue

342:        if (_stakingParams.proxyHash == 0) {	// @audit-issue

411:        if (success && returnData.length > 63 && (returnData.length % 32 == 0)) {	// @audit-issue

420:            if (success && returnData.length == 32) {	// @audit-issue

498:        if (reward == 0) {	// @audit-issue

721:        if (availableRewards == 0) {	// @audit-issue

747:        if (configHash != 0 && configHash != service.configHash) {	// @audit-issue

826:        if (serviceIds.length == 0) {	// @audit-issue
```
[287](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L287-L287), [288](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L288-L288), [289](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L289-L289), [290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L290-L290), [310](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L310-L310), [342](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L342-L342), [411](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L411-L411), [420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L420-L420), [498](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L498-L498), [721](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L721-L721), [747](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L747-L747), [826](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L826-L826), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

184:        if (implementation.code.length == 0) {	// @audit-issue
```
[184](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L184-L184), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

260:        if (mapRemovedNominees[nomineeHash] == 0 && mapNomineeIds[nomineeHash] == 0) {	// @audit-issue

331:        if (chainId == 0) {	// @audit-issue

598:        if (id == 0) {	// @audit-issue

647:        if (mapRemovedNominees[nomineeHash] == 0) {	// @audit-issue

653:        if (oldSlope.power == 0) {	// @audit-issue

748:        if (id == 0) {	// @audit-issue

765:        if (id == 0) {	// @audit-issue

799:            if (mapNomineeIds[nomineeHash] == 0) {	// @audit-issue
```
[260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L260-L260), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L331-L331), [598](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L598-L598), [647](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L647-L647), [653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L653-L653), [748](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L748-L748), [765](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L765-L765), [799](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L799-L799), 


#### Recommendation

To prevent typo bugs and improve code readability, it's advisable to place constants on the left side of comparisons. This coding practice helps catch accidental assignment (=) instead of comparison (==) and enhances code quality.

### Convert duplicated codes to modifier/functions
Duplicated code in Solidity contracts, especially when repeated across multiple functions, can lead to inefficiencies and increased maintenance challenges. It not only bloats the contract but also makes it harder to implement changes consistently across the codebase. By identifying common patterns or checks that are repeated and abstracting them into modifiers or separate functions, the code can be made more concise, readable, and maintainable. This practice not only reduces the overall bytecode size, potentially lowering deployment and execution costs, but also enhances the contract's reliability by ensuring consistency in how these common checks or operations are executed.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

1391:        if (unitTypes.length != unitIds.length) {	// @audit-issue: Exactly same copy pasted functionality between lines: `{'1319->1355'}`
1392:            revert WrongArrayLength(unitTypes.length, unitIds.length);
1393:        }
1394:
1395:        // Component / agent registry addresses
1396:        address[] memory registries = new address[](2);
1397:        (registries[0], registries[1]) = (componentRegistry, agentRegistry);
1398:
1399:        // Component / agent total supply
1400:        uint256[] memory registriesSupply = new uint256[](2);
1401:        for (uint256 i = 0; i < 2; ++i) {
1402:            registriesSupply[i] = IToken(registries[i]).totalSupply();
1403:        }
1404:
1405:        // Check the input data
1406:        uint256[] memory lastIds = new uint256[](2);
1407:        for (uint256 i = 0; i < unitIds.length; ++i) {
1408:            // Check for the unit type to be component / agent only
1409:            if (unitTypes[i] > 1) {
1410:                revert Overflow(unitTypes[i], 1);
1411:            }
1412:
1413:            // Check that the unit Ids are in ascending order, not repeating, and no bigger than registries total supply
1414:            if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
1415:                revert WrongUnitId(unitIds[i], unitTypes[i]);
1416:            }
1417:            lastIds[unitTypes[i]] = unitIds[i];
1418:
1419:            // Check the component / agent Id ownership
1420:            address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
1421:            if (unitOwner != account) {
1422:                revert OwnerOnly(unitOwner, account);
1423:            }
1424:        }
1425:
1426:        // Get the current epoch counter
1427:        uint256 curEpoch = epochCounter;

1329:        for (uint256 i = 0; i < 2; ++i) {	// @audit-issue: Same for statement in line(s): ['1401']

1335:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue: Same for statement in line(s): ['1407']

517:        assembly {	// @audit-issue: Same inline assembly statement in line(s): ['1083']
```
[1391](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1391-L1427), [1329](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1329-L1329), [1335](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1335-L1335), [517](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L517-L517), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

1228:        uint256 withheldAmount = mapChainIdWithheldAmounts[chainId] + amount;	// @audit-issue: Exactly same copy pasted functionality between lines: `{'1183->1191'}`
1229:        if (withheldAmount > type(uint96).max) {
1230:            revert Overflow(withheldAmount, type(uint96).max);
1231:        }
1232:
1233:        // Add to the withheld amount
1234:        mapChainIdWithheldAmounts[chainId] = withheldAmount;
1235:
1236:        emit WithheldAmountSynced(chainId, amount, withheldAmount);
```
[1228](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1228-L1236), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

176:        uint256 sequence = _sendMessage(targets, stakingIncentives, bridgePayload, transferAmount);	// @audit-issue: Exactly same copy pasted functionality between lines: `{'150->155'}`
177:
178:        // Increase the staking batch nonce
179:        stakingBatchNonce++;
180:
181:        emit MessagePosted(sequence, targets, stakingIncentives, transferAmount);
```
[176](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L176-L181), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

414:        if (_locked > 1) {	// @audit-issue: Exactly same copy pasted functionality between lines: `{'379->387'}`
415:            revert ReentrancyGuard();
416:        }
417:        _locked = 2;
418:
419:        // Check for the owner address
420:        if (msg.sender != owner) {
421:            revert OwnerOnly(msg.sender, owner);
422:        }
```
[414](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L414-L422), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

244:        rewardsPerSecondLimit = _rewardsPerSecondLimit;	// @audit-issue: Exactly same copy pasted functionality between lines: `{'88->91'}`
245:        timeForEmissionsLimit = _timeForEmissionsLimit;
246:        numServicesLimit = _numServicesLimit;
247:        emissionsLimit = _rewardsPerSecondLimit * _timeForEmissionsLimit * _numServicesLimit;
```
[244](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L244-L247), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

228:            if (t > block.timestamp) {	// @audit-issue: Same if statement in line(s): ['268']

542:        if (oldSlope.end > block.timestamp) {	// @audit-issue: Same if statement in line(s): ['658']
```
[228](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L228-L228), [542](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L542-L542), 


#### Recommendation

Identify and consolidate duplicated code blocks in Solidity contracts into reusable modifiers or functions. This approach streamlines the contract by eliminating redundancy, thereby improving clarity, maintainability, and potentially reducing gas costs. For common checks, consider using modifiers for concise and consistent enforcement of conditions. For reusable logic, encapsulate it in functions to avoid code duplication and simplify future updates or bug fixes.

### Style guide: Function ordering does not follow the Solidity style guide
According to the Solidity [style guide](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions), functions should be laid out in the following order :`constructor()`, `receive()`, `fallback()`, `external`, `public`, `internal`, `private`, but the cases below do not follow this pattern

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

990:    function trackServiceDonations(	// @audit-issue: trackServiceDonations should come before than _finalizeIncentivesForUnitId
```
[990](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L990-L990), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

636:    function changeOwner(address newOwner) external {	// @audit-issue: changeOwner should come before than _calculateStakingIncentivesBatch
```
[636](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L636-L636), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

196:    function receiveMessage(bytes memory data) external {	// @audit-issue
```
[196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L196-L196), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

96:    function receiveMessage(bytes memory data) external payable {	// @audit-issue
```
[96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L96-L96), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

132:    function sendMessage(	// @audit-issue
```
[132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L132-L132), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

66:    function receiveMessage(bytes memory data) external payable {	// @audit-issue
```
[66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L66-L66), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

148:    function receiveMessage(bytes memory data) external payable {	// @audit-issue
```
[148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L148-L148), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

130:    function sendMessage(	// @audit-issue
```
[130](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L130-L130), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

98:    function receiveMessage(bytes memory data) external {	// @audit-issue
```
[98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L98-L98), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

59:    function setFxRootTunnel(address l1Processor) external override {	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

106:    function receiveWormholeMessages(	// @audit-issue
```
[106](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L106-L106), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

87:    function receiveMessage(bytes memory data) external {	// @audit-issue
```
[87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L87-L87), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

247:    function changeOwner(address newOwner) external {	// @audit-issue
```
[247](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L247-L247), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

98:    function setFxChildTunnel(address l2Dispenser) public override {	// @audit-issue
```
[98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L98-L98), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

39:    receive() external payable {	// @audit-issue
```
[39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L39-L39), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

591:    function checkpoint() public returns (	// @audit-issue: checkpoint should come before than _claim
```
[591](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L591-L591), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

115:    function deposit(uint256 amount) external {	// @audit-issue
```
[115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L115-L115), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

161:    function getProxyAddress(address implementation) external view returns (address) {	// @audit-issue
```
[161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L161-L161), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

324:    function addNomineeEVM(address account, uint256 chainId) external {	// @audit-issue
```
[324](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L324-L324), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/latest/style-guide.html).

### Style guide: Contract does not follow the Solidity style guide's suggested layout ordering
The [style guide](https://docs.soliditylang.org/en/latest/style-guide.html#order-of-layout) says that, within a contract, the ordering should be 1) Type declarations, 2) State variables, 3) Events, 4) Errors, 5) Modifiers, and 6) Functions, but the contract(s) below do not follow this ordering


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

254:contract Tokenomics is TokenomicsConstants {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> owner
	2 -> maxBond
	3 -> olas
	4 -> inflationPerSecond
	5 -> treasury
	6 -> veOLASThreshold
	7 -> depository
	8 -> effectiveBond
	9 -> dispenser
	10 -> codePerDev
	11 -> currentYear
	12 -> tokenomicsParametersUpdated
	13 -> _locked
	14 -> componentRegistry
	15 -> epsilonRate
	16 -> epochLen
	17 -> agentRegistry
	18 -> nextVeOLASThreshold
	19 -> serviceRegistry
	20 -> epochCounter
	21 -> timeLaunch
	22 -> nextEpochLen
	23 -> ve
	24 -> devsPerCapital
	25 -> donatorBlacklist
	26 -> lastDonationBlockNumber
	27 -> mapServiceAmounts
	28 -> mapOwnerRewards
	29 -> mapOwnerTopUps
	30 -> mapEpochTokenomics
	31 -> mapNewUnits
	32 -> mapNewOwners
	33 -> mapUnitIncentives
	34 -> mapEpochStakingPoints
	35 -> OwnerUpdated
	36 -> TreasuryUpdated
	37 -> DepositoryUpdated
	38 -> DispenserUpdated
	39 -> EpochLengthUpdated
	40 -> EffectiveBondUpdated
	41 -> StakingRefunded
	42 -> IDFUpdated
	43 -> TokenomicsParametersUpdateRequested
	44 -> TokenomicsParametersUpdated
	45 -> IncentiveFractionsUpdateRequested
	46 -> StakingParamsUpdateRequested
	47 -> IncentiveFractionsUpdated
	48 -> StakingParamsUpdated
	49 -> ComponentRegistryUpdated
	50 -> AgentRegistryUpdated
	51 -> ServiceRegistryUpdated
	52 -> DonatorBlacklistUpdated
	53 -> EpochSettled
	54 -> TokenomicsImplementationUpdated
	55 -> constructor
	56 -> initializeTokenomics
	57 -> tokenomicsImplementation
	58 -> changeTokenomicsImplementation
	59 -> changeOwner
	60 -> changeManagers
	61 -> changeRegistries
	62 -> changeDonatorBlacklist
	63 -> changeTokenomicsParameters
	64 -> changeIncentiveFractions
	65 -> changeStakingParams
	66 -> reserveAmountForBondProgram
	67 -> refundFromBondProgram
	68 -> refundFromStaking
	69 -> _finalizeIncentivesForUnitId
	70 -> _trackServiceDonations
	71 -> trackServiceDonations
	72 -> _calculateIDF
	73 -> checkpoint
	74 -> accountOwnerIncentives
	75 -> getOwnerIncentives
	76 -> getUnitPoint
	77 -> getLastIDF
	78 -> getEpochEndTime

```
[254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L254-L254), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

253:contract Dispenser {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> MAX_EVM_CHAIN_ID
	2 -> olas
	3 -> retainer
	4 -> retainerHash
	5 -> maxNumClaimingEpochs
	6 -> maxNumStakingTargets
	7 -> owner
	8 -> _locked
	9 -> paused
	10 -> tokenomics
	11 -> treasury
	12 -> voteWeighting
	13 -> mapLastClaimedStakingEpochs
	14 -> mapRemovedNomineeEpochs
	15 -> mapChainIdDepositProcessors
	16 -> mapChainIdWithheldAmounts
	17 -> OwnerUpdated
	18 -> TokenomicsUpdated
	19 -> TreasuryUpdated
	20 -> VoteWeightingUpdated
	21 -> StakingParamsUpdated
	22 -> IncentivesClaimed
	23 -> StakingIncentivesClaimed
	24 -> Retained
	25 -> SetDepositProcessorChainIds
	26 -> WithheldAmountSynced
	27 -> PauseDispenser
	28 -> constructor
	29 -> _checkpointNomineeAndGetClaimedEpochCounters
	30 -> _distributeStakingIncentives
	31 -> _distributeStakingIncentivesBatch
	32 -> _checkOrderAndValues
	33 -> _calculateStakingIncentivesBatch
	34 -> changeOwner
	35 -> changeManagers
	36 -> changeStakingParams
	37 -> setDepositProcessorChainIds
	38 -> addNominee
	39 -> removeNominee
	40 -> claimOwnerIncentives
	41 -> calculateStakingIncentives
	42 -> claimStakingIncentives
	43 -> claimStakingIncentivesBatch
	44 -> retain
	45 -> syncWithheldAmount
	46 -> syncWithheldAmountMaintenance
	47 -> setPauseState

```
[253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L253-L253), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

22:abstract contract DefaultDepositProcessorL1 is IBridgeErrors {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> RECEIVE_MESSAGE
	2 -> MAX_CHAIN_ID
	3 -> TOKEN_GAS_LIMIT
	4 -> MESSAGE_GAS_LIMIT
	5 -> olas
	6 -> l1Dispenser
	7 -> l1TokenRelayer
	8 -> l1MessageRelayer
	9 -> l2TargetChainId
	10 -> l2TargetDispenser
	11 -> owner
	12 -> stakingBatchNonce
	13 -> MessagePosted
	14 -> MessageReceived
	15 -> L2TargetDispenserUpdated
	16 -> constructor
	17 -> _sendMessage
	18 -> _receiveMessage
	19 -> sendMessage
	20 -> sendMessageBatch
	21 -> _setL2TargetDispenser
	22 -> setL2TargetDispenser
	23 -> getBridgingDecimals

```
[22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

50:contract EthereumDepositProcessor {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> olas
	2 -> dispenser
	3 -> stakingFactory
	4 -> timelock
	5 -> _locked
	6 -> AmountRefunded
	7 -> StakingTargetDeposited
	8 -> constructor
	9 -> _deposit
	10 -> sendMessage
	11 -> sendMessageBatch
	12 -> getBridgingDecimals

```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

45:abstract contract DefaultTargetDispenserL2 is IBridgeErrors {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> RECEIVE_MESSAGE
	2 -> MAX_CHAIN_ID
	3 -> GAS_LIMIT
	4 -> MAX_GAS_LIMIT
	5 -> olas
	6 -> stakingFactory
	7 -> l2MessageRelayer
	8 -> l1DepositProcessor
	9 -> l1SourceChainId
	10 -> withheldAmount
	11 -> stakingBatchNonce
	12 -> owner
	13 -> paused
	14 -> _locked
	15 -> stakingQueueingNonces
	16 -> OwnerUpdated
	17 -> FundsReceived
	18 -> StakingTargetDeposited
	19 -> AmountWithheld
	20 -> StakingRequestQueued
	21 -> MessagePosted
	22 -> MessageReceived
	23 -> WithheldAmountSynced
	24 -> Drain
	25 -> TargetDispenserPaused
	26 -> TargetDispenserUnpaused
	27 -> Migrated
	28 -> constructor
	29 -> _processData
	30 -> _sendMessage
	31 -> _receiveMessage
	32 -> changeOwner
	33 -> redeem
	34 -> processDataMaintenance
	35 -> syncWithheldTokens
	36 -> pause
	37 -> unpause
	38 -> drain
	39 -> migrate
	40 -> receive

```
[45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L45-L45), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

22:contract PolygonDepositProcessorL1 is DefaultDepositProcessorL1, FxBaseRootTunnel {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> predicate
	2 -> FxChildTunnelUpdated
	3 -> constructor
	4 -> _sendMessage
	5 -> _processMessageFromChild
	6 -> setFxChildTunnel
	7 -> setL2TargetDispenser

```
[22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

43:contract StakingVerifier {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> olas
	2 -> rewardsPerSecondLimit
	3 -> timeForEmissionsLimit
	4 -> numServicesLimit
	5 -> emissionsLimit
	6 -> owner
	7 -> implementationsCheck
	8 -> mapImplementations
	9 -> OwnerUpdated
	10 -> SetImplementationsCheck
	11 -> ImplementationsWhitelistUpdated
	12 -> StakingLimitsUpdated
	13 -> constructor
	14 -> changeOwner
	15 -> setImplementationsCheck
	16 -> setImplementationsStatuses
	17 -> verifyImplementation
	18 -> verifyInstance
	19 -> changeStakingLimits
	20 -> getEmissionsAmountLimit

```
[43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L43-L43), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

168:abstract contract StakingBase is ERC721TokenReceiver {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> VERSION
	2 -> metadataHash
	3 -> maxNumServices
	4 -> rewardsPerSecond
	5 -> minStakingDeposit
	6 -> maxNumInactivityPeriods
	7 -> livenessPeriod
	8 -> timeForEmissions
	9 -> numAgentInstances
	10 -> threshold
	11 -> configHash
	12 -> proxyHash
	13 -> serviceRegistry
	14 -> activityChecker
	15 -> minStakingDuration
	16 -> maxInactivityDuration
	17 -> epochCounter
	18 -> balance
	19 -> availableRewards
	20 -> emissionsAmount
	21 -> tsCheckpoint
	22 -> agentIds
	23 -> mapServiceInfo
	24 -> setServiceIds
	25 -> ServiceStaked
	26 -> Checkpoint
	27 -> ServiceUnstaked
	28 -> RewardClaimed
	29 -> ServiceInactivityWarning
	30 -> ServicesEvicted
	31 -> Deposit
	32 -> Withdraw
	33 -> _initialize
	34 -> _checkTokenStakingDeposit
	35 -> _withdraw
	36 -> _checkRatioPass
	37 -> _evict
	38 -> _claim
	39 -> _calculateStakingRewards
	40 -> checkpoint
	41 -> stake
	42 -> unstake
	43 -> claim
	44 -> checkpointAndClaim
	45 -> calculateStakingLastReward
	46 -> calculateStakingReward
	47 -> getStakingState
	48 -> getNextRewardCheckpointTimestamp
	49 -> getServiceInfo
	50 -> getServiceIds
	51 -> getAgentIds

```
[168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L168-L168), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

81:contract StakingFactory {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> SELECTOR_DATA_LENGTH
	2 -> nonce
	3 -> owner
	4 -> verifier
	5 -> _locked
	6 -> mapInstanceParams
	7 -> OwnerUpdated
	8 -> VerifierUpdated
	9 -> InstanceCreated
	10 -> InstanceStatusChanged
	11 -> constructor
	12 -> changeOwner
	13 -> changeVerifier
	14 -> getProxyAddressWithNonce
	15 -> getProxyAddress
	16 -> createStakingInstance
	17 -> setInstanceStatus
	18 -> verifyInstance
	19 -> verifyInstanceAndGetEmissionsAmount

```
[81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L81-L81), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

132:contract VoteWeighting {	// @audit-issue : Order layout of this contract is not correct. It should be like this: 
	1 -> WEEK
	2 -> WEIGHT_VOTE_DELAY
	3 -> MAX_NUM_WEEKS
	4 -> MAX_WEIGHT
	5 -> MAX_EVM_CHAIN_ID
	6 -> ve
	7 -> owner
	8 -> dispenser
	9 -> setNominees
	10 -> setRemovedNominees
	11 -> mapNomineeIds
	12 -> mapRemovedNominees
	13 -> voteUserSlopes
	14 -> voteUserPower
	15 -> lastUserVote
	16 -> pointsWeight
	17 -> changesWeight
	18 -> timeWeight
	19 -> pointsSum
	20 -> changesSum
	21 -> timeSum
	22 -> OwnerUpdated
	23 -> DispenserUpdated
	24 -> Checkpoint
	25 -> CheckpointNominee
	26 -> NomineeRelativeWeightWrite
	27 -> VoteForNominee
	28 -> AddNominee
	29 -> RemoveNominee
	30 -> constructor
	31 -> _getSum
	32 -> _getWeight
	33 -> _addNominee
	34 -> addNomineeEVM
	35 -> addNomineeNonEVM
	36 -> changeOwner
	37 -> changeDispenser
	38 -> checkpoint
	39 -> checkpointNominee
	40 -> _nomineeRelativeWeight
	41 -> nomineeRelativeWeight
	42 -> nomineeRelativeWeightWrite
	43 -> voteForNomineeWeights
	44 -> voteForNomineeWeightsBatch
	45 -> _maxAndSub
	46 -> removeNominee
	47 -> revokeRemovedNomineeVotingPower
	48 -> getNomineeWeight
	49 -> getWeightsSum
	50 -> getNumNominees
	51 -> getNumRemovedNominees
	52 -> getAllNominees
	53 -> getAllRemovedNominees
	54 -> getNomineeId
	55 -> getRemovedNomineeId
	56 -> getNominee
	57 -> getRemovedNominee
	58 -> getNextAllowedVotingTimes

```
[132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L132-L132), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### Duplicated `require()`/`revert()` checks should be refactored to a modifier or function
In Solidity contracts, it's common to encounter duplicated `require()` or `revert()` statements across multiple functions. These statements are crucial for validating conditions and ensuring contract integrity. However, repeated checks can lead to code redundancy, making the contract larger and more difficult to maintain. This redundancy can be streamlined by encapsulating the repeated logic in a modifier or a dedicated function. By doing so, the code becomes more concise, easier to audit, and more gas-efficient. Furthermore, centralizing the validation logic in a single location makes the codebase more adaptable to changes and reduces the risk of inconsistencies or errors in future updates.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

528:        if (msg.sender != owner) {	// @audit-issue: Same if statement in line(s): ['548', '567', '594', '618', '647', '712', '753']

789:        if (depository != msg.sender) {	// @audit-issue: Same if statement in line(s): ['810']

1319:        if (unitTypes.length != unitIds.length) {	// @audit-issue: Same if statement in line(s): ['1391']

1337:            if (unitTypes[i] > 1) {	// @audit-issue: Same if statement in line(s): ['1409']

1342:            if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {	// @audit-issue: Same if statement in line(s): ['1414']

1349:            if (unitOwner != account) {	// @audit-issue: Same if statement in line(s): ['1421']
```
[528](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L528-L528), [789](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L789-L789), [1319](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1319-L1319), [1337](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1337-L1337), [1342](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1342-L1342), [1349](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1349-L1349), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

336:        if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {	// @audit-issue: Same if statement in line(s): ['690']

638:        if (msg.sender != owner) {	// @audit-issue: Same if statement in line(s): ['657', '685', '707', '1201', '1243']

734:        if (msg.sender != voteWeighting) {	// @audit-issue: Same if statement in line(s): ['752']

740:        if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||	// @audit-issue: Same if statement in line(s): ['983', '1080']

794:        if (_locked > 1) {	// @audit-issue: Same if statement in line(s): ['960', '1066', '1131']

861:        if (chainId == 0) {	// @audit-issue: Same if statement in line(s): ['966']

866:        if (stakingTarget == 0) {	// @audit-issue: Same if statement in line(s): ['971']

977:        if (numClaimedEpochs > localMaxNumClaimingEpochs) {	// @audit-issue: Same if statement in line(s): ['1075']

1184:        if (withheldAmount > type(uint96).max) {	// @audit-issue: Same if statement in line(s): ['1229']
```
[336](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L336-L336), [638](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L638-L638), [734](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L734-L734), [740](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L740-L740), [794](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L794-L794), [861](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L861-L861), [866](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L866-L866), [977](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L977-L977), [1184](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1184-L1184), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

139:        if (msg.sender != l1Dispenser) {	// @audit-issue: Same if statement in line(s): ['171']
```
[139](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L139-L139), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

137:        if (msg.sender != dispenser) {	// @audit-issue: Same if statement in line(s): ['162']
```
[137](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L137-L137), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

141:        if (_locked > 1) {	// @audit-issue: Same if statement in line(s): ['268', '325', '379', '414']

249:        if (msg.sender != owner) {	// @audit-issue: Same if statement in line(s): ['313', '355', '366', '385', '420']

274:        if (paused == 2) {	// @audit-issue: Same if statement in line(s): ['331']

337:        if (amount == 0) {	// @audit-issue: Same if statement in line(s): ['391']
```
[141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L141-L141), [249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L249-L249), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L274-L274), [337](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L337-L337), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

82:        if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {	// @audit-issue: Same if statement in line(s): ['240']

115:        if (owner != msg.sender) {	// @audit-issue: Same if statement in line(s): ['137', '235']
```
[82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L82-L82), [115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L115-L115), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

485:        if (msg.sender != sInfo.owner) {	// @audit-issue: Same if statement in line(s): ['808']
```
[485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L485-L485), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

112:        if (msg.sender != owner) {	// @audit-issue: Same if statement in line(s): ['129']
```
[112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L112-L112), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

370:        if (msg.sender != owner) {	// @audit-issue: Same if statement in line(s): ['388']
```
[370](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L370-L370), 


#### Recommendation

Consolidate repeated `require()` or `revert()` checks in Solidity by creating a modifier or a separate function. Apply this modifier to all functions requiring the same validation, or call the function at the beginning of each relevant function. This refactoring enhances contract efficiency, maintainability, and consistency, while potentially reducing gas costs associated with deploying and executing redundant code.

### Adding a return statement when the function defines a named return variable, is redundant
Once the return variable has been assigned (or has its default value), there is no need to explicitly return it at the end of the function, since it's returned automatically.

```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

33:    function getSupplyCapForYear(uint256 numYears) public pure returns (uint256 supplyCap) {
34:        // For the first 10 years the supply caps are pre-defined
35:        if (numYears < 10) {
36:            uint96[10] memory supplyCaps = [
37:                529_659_000e18,
38:                569_913_084e18,
39:                610_313_084e18,
40:                666_313_084e18,
41:                746_313_084e18,
42:                818_313_084e18,
43:                882_313_084e18,
44:                930_313_084e18,
45:                970_313_084e18,
46:                1_000_000_000e18
47:            ];
48:            supplyCap = supplyCaps[numYears];
49:        } else {
50:            // Number of years after ten years have passed (including ongoing ones)
51:            numYears -= 9;
52:            // Max cap for the first 10 years
53:            supplyCap = 1_000_000_000e18;
54:            // After that the inflation is 2% per year as defined by the OLAS contract
55:            uint256 maxMintCapFraction = 2;
56:
57:            // Get the supply cap until the current year
58:            for (uint256 i = 0; i < numYears; ++i) {
59:                supplyCap += (supplyCap * maxMintCapFraction) / 100;
60:            }
61:            // Return the difference between last two caps (inflation for the current year)
62:            return supplyCap;	// @audit-issue
63:        }
64:    }
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L33-L64), 


#### Recommendation

When a function defines a named return variable and assigns a value to it, there is no need to add an explicit return statement at the end of the function. The named return variable will be automatically returned with its assigned value. Removing the redundant return statement can lead to cleaner and more concise code, improving readability and reducing the risk of introducing unnecessary errors.

### Consider using `delete` rather than assigning values to `false`
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

296:            stakingQueueingNonces[queueHash] = false;	// @audit-issue
```
[296](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L296-L296), 


#### Recommendation

Prefer using the `delete` keyword to reset state variables to their initial values instead of assigning `false` or other values, as it makes the intent clearer and helps with auditing.

### Consider using `delete` rather than assigning zero to clear values
The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

859:            mapUnitIncentives[unitType][unitId].pendingRelativeReward = 0;	// @audit-issue

875:            mapUnitIncentives[unitType][unitId].pendingRelativeTopUp = 0;	// @audit-issue

1034:        idf = 0;	// @audit-issue

1179:                nextEpochLen = 0;	// @audit-issue

1185:                nextVeOLASThreshold = 0;	// @audit-issue

1260:            tokenomicsParametersUpdated = 0;	// @audit-issue

1267:            tokenomicsParametersUpdated = 0;	// @audit-issue

1366:                mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch = 0;	// @audit-issue

1371:            mapUnitIncentives[unitTypes[i]][unitIds[i]].reward = 0;	// @audit-issue

1374:            mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp = 0;	// @audit-issue
```
[859](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L859-L859), [875](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L875-L875), [1034](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1034-L1034), [1179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1179-L1179), [1185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1185-L1185), [1260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1260-L1260), [1267](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1267-L1267), [1366](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1366-L1366), [1371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1371-L1371), [1374](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1374-L1374), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

620:                        transferAmounts[i] = 0;	// @audit-issue

623:                        withheldAmount = 0;	// @audit-issue

1017:                    transferAmount = 0;	// @audit-issue

1021:                    withheldAmount = 0;	// @audit-issue
```
[620](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L620-L620), [623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L623-L623), [1017](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1017-L1017), [1021](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1021-L1021), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

342:        withheldAmount = 0;	// @audit-issue
```
[342](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L342-L342), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

503:        sInfo.reward = 0;	// @audit-issue

645:                lastAvailableRewards = 0;	// @audit-issue

667:            numServices = 0;	// @audit-issue

691:                    mapServiceInfo[curServiceId].inactivity = 0;	// @audit-issue
```
[503](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L503-L503), [645](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L645-L645), [667](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L667-L667), [691](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L691-L691), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

238:                pt.bias = 0;	// @audit-issue

239:                pt.slope = 0;	// @audit-issue

278:                pt.bias = 0;	// @audit-issue

279:                pt.slope = 0;	// @audit-issue

606:        pointsWeight[nomineeHash][nextTime].bias = 0;	// @audit-issue

619:        mapNomineeIds[nomineeHash] = 0;	// @audit-issue
```
[238](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L238-L238), [239](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L239-L239), [278](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L278-L278), [279](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L279-L279), [606](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L606-L606), [619](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L619-L619), 


#### Recommendation

When you need to clear or reset values in storage variables, consider using the `delete` keyword instead of manually assigning zero or other values. Using `delete` provides a more explicit and efficient way to clear storage variables. For example, instead of `myVariable = 0;`, you can use `delete myVariable;` to clear the value.

### Array is `push()`ed but not `pop()`ed
Array entries are added but are never removed. Consider whether this should be the case, or whether there should be a maximum, or whether old entries should be removed. Cases where there are specific potential problems will be flagged separately under a different issue.

```solidity
Path: ./registries/contracts/staking/StakingBase.sol

338:            agentIds.push(agentId);	// @audit-issue
```
[338](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L338-L338), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

616:        setRemovedNominees.push(nominee);	// @audit-issue
```
[616](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L616-L616), 


#### Recommendation

When working with arrays in your Solidity code, carefully evaluate whether the addition of array entries using `push()` should be balanced with corresponding removal using methods like `pop()`, `splice()`, or other appropriate array manipulation techniques. This evaluation is crucial to manage the array's size and prevent unnecessary storage growth, which can impact gas costs and contract efficiency.

### Too long functions should be refactored
Functions with too many lines are difficult to understand. It is recommended to refactor complex functions into multiple shorter and easier to understand functions.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

409:    function initializeTokenomics(	// @audit-issue 103 lines

639:    function changeTokenomicsParameters(	// @audit-issue 55 lines

884:    function _trackServiceDonations(	// @audit-issue 92 lines

1080:    function checkpoint() external returns (bool) {	// @audit-issue 217 lines

1308:    function accountOwnerIncentives(	// @audit-issue 68 lines

1385:    function getOwnerIncentives(	// @audit-issue 75 lines
```
[409](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L409-L409), [639](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L639-L639), [884](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L884-L884), [1080](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1080-L1080), [1308](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1308-L1308), [1385](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1385-L1385), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

441:    function _distributeStakingIncentivesBatch(	// @audit-issue 57 lines

506:    function _checkOrderAndValues(	// @audit-issue 58 lines

573:    function _calculateStakingIncentivesBatch(	// @audit-issue 59 lines

849:    function calculateStakingIncentives(	// @audit-issue 97 lines

953:    function claimStakingIncentives(	// @audit-issue 94 lines

1058:    function claimStakingIncentivesBatch(	// @audit-issue 68 lines
```
[441](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L441-L441), [506](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L506-L506), [573](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L573-L573), [849](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L849-L849), [953](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L953-L953), [1058](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1058-L1058), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

119:    function _sendMessage(	// @audit-issue 73 lines
```
[119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L119-L119), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

139:    function _processData(bytes memory data) internal {	// @audit-issue 74 lines
```
[139](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L139-L139), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

278:    function _initialize(	// @audit-issue 83 lines

522:    function _calculateStakingRewards() internal view returns (	// @audit-issue 61 lines

591:    function checkpoint() public returns (	// @audit-issue 122 lines

719:    function stake(uint256 serviceId) external {	// @audit-issue 81 lines

805:    function unstake(uint256 serviceId) external returns (uint256 reward) {	// @audit-issue 67 lines
```
[278](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L278-L278), [522](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L522-L522), [591](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L591-L591), [719](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L719-L719), [805](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L805-L805), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

168:    function createStakingInstance(	// @audit-issue 76 lines
```
[168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L168-L168), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

473:    function voteForNomineeWeights(bytes32 account, uint256 chainId, uint256 weight) public {	// @audit-issue 84 lines

586:    function removeNominee(bytes32 account, uint256 chainId) external {	// @audit-issue 50 lines
```
[473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L473-L473), [586](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L586-L586), 


#### Recommendation

To address this issue, refactor long and complex functions into multiple shorter and more manageable functions. This will improve code readability and maintainability, making it easier to understand and maintain your smart contract.

### Expressions for `constant` values should use `immutable` rather than `constant`
While it does not save gas for some simple binary expressions because the compiler knows that developers often make this mistake, it's still best to use the right tool for the task at hand. There is a difference between `constant` variables and `immutable` variables, and they should each be used in their appropriate contexts. `constant`s should be used for literal values written into the code, and `immutable` variables should be used for expressions, or values calculated in, or passed into the `constructor`.

```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

28:    bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L28-L28), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

62:    bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L62-L62), 


#### Recommendation

To improve code readability and adhere to best practices, consider using `immutable` variables instead of `constant` for expressions or values calculated in, or passed into the constructor. While the compiler may handle this issue for simple binary expressions, it's essential to use the right tool for the task at hand. `constant` should be reserved for literal values written directly into the code, while `immutable` is better suited for dynamic values and expressions.

### Events should use parameters to convey information
For example, rather than using `event Paused()` and `event Unpaused()`, use `event PauseState(address indexed whoChangedIt, bool wasPaused, bool isNowPaused)`


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

57:    event TargetDispenserPaused();	// @audit-issue

58:    event TargetDispenserUnpaused();	// @audit-issue
```
[57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L57-L57), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L58-L58), 


#### Recommendation

To provide more informative event logs, consider using parameters in your events to convey relevant information about the event. Instead of using separate events like `event Paused()` and `event Unpaused()`, use a single event with parameters, such as `event PauseState(address indexed whoChangedIt, bool wasPaused, bool isNowPaused)`. This approach allows you to include context and details in your event logs, making it easier for users and applications to understand and interpret the events.

### Lines are too long
Usually lines in source code are limited to [80](https://softwareengineering.stackexchange.com/questions/148677/why-is-80-characters-the-standard-limit-for-code-width) characters. Today's screens are much larger so it's reasonable to stretch this in some cases. The solidity style guide recommends a maximumum line length of [120 characters](https://docs.soliditylang.org/en/v0.8.17/style-guide.html#maximum-line-length), so the lines below should be split when they reach that length.        self.impact_details = 


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

300:    // effectiveBond = sum(MaxBond(e)) - sum(BondingProgram) over all epochs: accumulates leftovers from previous epochs	// @audit-issue: Length of the line is: 121

308:    // We assume this number will not be practically bigger than 4,722 of its integer-part (with 18 digits of fractional-part)	// @audit-issue: Length of the line is: 127

349:    // We assume this number will not be practically bigger than 4,722 of its integer-part (with 18 digits of fractional-part)	// @audit-issue: Length of the line is: 127

382:    /// @notice Tokenomics contract must be initialized no later than one year from the launch of the OLAS token contract.	// @audit-issue: Length of the line is: 123

396:    /// #if_succeeds {:msg "treasury must not be a zero address"} old(_treasury) != address(0) ==> treasury == _treasury;	// @audit-issue: Length of the line is: 122

397:    /// #if_succeeds {:msg "depository must not be a zero address"} old(_depository) != address(0) ==> depository == _depository;	// @audit-issue: Length of the line is: 130

398:    /// #if_succeeds {:msg "dispenser must not be a zero address"} old(_dispenser) != address(0) ==> dispenser == _dispenser;	// @audit-issue: Length of the line is: 126

400:    /// #if_succeeds {:msg "epochLen"} old(_epochLen > MIN_EPOCH_LENGTH && _epochLen <= type(uint32).max) ==> epochLen == _epochLen;	// @audit-issue: Length of the line is: 133

401:    /// #if_succeeds {:msg "componentRegistry must not be a zero address"} old(_componentRegistry) != address(0) ==> componentRegistry == _componentRegistry;	// @audit-issue: Length of the line is: 158

402:    /// #if_succeeds {:msg "agentRegistry must not be a zero address"} old(_agentRegistry) != address(0) ==> agentRegistry == _agentRegistry;	// @audit-issue: Length of the line is: 142

403:    /// #if_succeeds {:msg "serviceRegistry must not be a zero address"} old(_serviceRegistry) != address(0) ==> serviceRegistry == _serviceRegistry;	// @audit-issue: Length of the line is: 150

405:    /// #if_succeeds {:msg "inflationPerSecond must not be zero"} inflationPerSecond > 0 && inflationPerSecond <= getInflationForYear(0);	// @audit-issue: Length of the line is: 138

407:    /// #if_succeeds {:msg "maxBond"} old(_epochLen > MIN_EPOCH_LENGTH && _epochLen <= type(uint32).max && inflationPerSecond > 0 && inflationPerSecond <= getInflationForYear(0))	// @audit-issue: Length of the line is: 179

468:        // This value is necessary since it is different from a precise one year time, as the OLAS contract started earlier	// @audit-issue: Length of the line is: 124

470:        // Calculating initial inflation per second: (mintable OLAS from getInflationForYear(0)) / (seconds left in a year)	// @audit-issue: Length of the line is: 124

471:        // Note that we lose precision here dividing by the number of seconds right away, but to avoid complex calculations	// @audit-issue: Length of the line is: 124

472:        // later we consider it less error-prone and sacrifice at most 6 insignificant digits (or 1e-12) of OLAS per year	// @audit-issue: Length of the line is: 122

496:        // E.g. if we have 2 profitable components and 2 profitable agents, this means there are (2 x 2.0 + 2 x 1.0) / 3 = 2	// @audit-issue: Length of the line is: 125

498:        // We assume that during one epoch the developer can contribute with one piece of code (1 component or 2 agents)	// @audit-issue: Length of the line is: 121

633:    /// ==> mapEpochTokenomics[epochCounter - 1].epochPoint.endTime > mapEpochTokenomics[epochCounter - 2].epochPoint.endTime;	// @audit-issue: Length of the line is: 127

634:    /// #if_succeeds {:msg "epochLen"} old(_epochLen > MIN_EPOCH_LENGTH && _epochLen <= ONE_YEAR && epochLen != _epochLen) ==> nextEpochLen == _epochLen;	// @audit-issue: Length of the line is: 154

635:    /// #if_succeeds {:msg "devsPerCapital"} _devsPerCapital > MIN_PARAM_VALUE && _devsPerCapital <= type(uint72).max ==> devsPerCapital == _devsPerCapital;	// @audit-issue: Length of the line is: 157

636:    /// #if_succeeds {:msg "codePerDev"} _codePerDev > MIN_PARAM_VALUE && _codePerDev <= type(uint72).max ==> codePerDev == _codePerDev;	// @audit-issue: Length of the line is: 137

638:    /// #if_succeeds {:msg "veOLASThreshold"} _veOLASThreshold > 0 && _veOLASThreshold <= type(uint96).max ==> nextVeOLASThreshold == _veOLASThreshold;	// @audit-issue: Length of the line is: 152

651:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch	// @audit-issue: Length of the line is: 121

659:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch	// @audit-issue: Length of the line is: 121

692:        emit TokenomicsParametersUpdateRequested(epochCounter + 1, _devsPerCapital, _codePerDev, _epsilonRate, _epochLen,	// @audit-issue: Length of the line is: 122

702:    /// #if_succeeds {:msg "maxBond"} mapEpochTokenomics[epochCounter + 1].epochPoint.maxBondFraction == _maxBondFraction;	// @audit-issue: Length of the line is: 123

786:    /// #if_succeeds {:msg "effectiveBond"} old(effectiveBond) > amount ==> effectiveBond == old(effectiveBond) - amount;	// @audit-issue: Length of the line is: 122

807:    /// #if_succeeds {:msg "effectiveBond"} old(effectiveBond + amount) <= type(uint96).max ==> effectiveBond == old(effectiveBond) + amount;	// @audit-issue: Length of the line is: 142

850:        // Note that if the rewardUnitFraction is set to zero at the end of epoch, the whole pending reward will be zero	// @audit-issue: Length of the line is: 121

871:            uint256 sumUnitIncentives = uint256(mapEpochTokenomics[epochNum].unitPoints[unitType].sumUnitTopUpsOLAS) * 100;	// @audit-issue: Length of the line is: 124

979:    /// @notice This function is only called by the treasury where the validity of arrays and values has been performed.	// @audit-issue: Length of the line is: 121

985:    /// #if_succeeds {:msg "totalDonationsETH can only increase"} old(mapEpochTokenomics[epochCounter].epochPoint.totalDonationsETH) + donationETH <= type(uint96).max	// @audit-issue: Length of the line is: 167

986:    /// ==> mapEpochTokenomics[epochCounter].epochPoint.totalDonationsETH == old(mapEpochTokenomics[epochCounter].epochPoint.totalDonationsETH) + donationETH;	// @audit-issue: Length of the line is: 159

987:    /// #if_succeeds {:msg "sumUnitTopUpsOLAS for components can only increase"} mapEpochTokenomics[epochCounter].unitPoints[0].sumUnitTopUpsOLAS >= old(mapEpochTokenomics[epochCounter].unitPoints[0].sumUnitTopUpsOLAS);	// @audit-issue: Length of the line is: 220

988:    /// #if_succeeds {:msg "sumUnitTopUpsOLAS for agents can only increase"} mapEpochTokenomics[epochCounter].unitPoints[1].sumUnitTopUpsOLAS >= old(mapEpochTokenomics[epochCounter].unitPoints[1].sumUnitTopUpsOLAS);	// @audit-issue: Length of the line is: 216

989:    /// #if_succeeds {:msg "numNewOwners can only increase"} mapEpochTokenomics[epochCounter].epochPoint.numNewOwners >= old(mapEpochTokenomics[epochCounter].epochPoint.numNewOwners);	// @audit-issue: Length of the line is: 184

1066:    ///         not valid not to call a checkpoint for longer than a year. Thus, the function will return false otherwise.	// @audit-issue: Length of the line is: 123

1070:    /// #if_succeeds {:msg "two events will never happen at the same time"} $result == true && (block.timestamp - timeLaunch) / ONE_YEAR > old(currentYear) ==> currentYear == old(currentYear) + 1;	// @audit-issue: Length of the line is: 197

1071:    /// #if_succeeds {:msg "previous epoch endTime must never be zero"} mapEpochTokenomics[epochCounter - 1].epochPoint.endTime > 0;	// @audit-issue: Length of the line is: 133

1072:    /// #if_succeeds {:msg "when the year is the same, the adjusted maxBond (incentives[4]) will never be lower than the epoch maxBond"}	// @audit-issue: Length of the line is: 137

1074:    /// ==> old((inflationPerSecond * (block.timestamp - mapEpochTokenomics[epochCounter - 1].epochPoint.endTime) * mapEpochTokenomics[epochCounter].epochPoint.maxBondFraction) / 100) >= old(maxBond);	// @audit-issue: Length of the line is: 201

1075:    /// #if_succeeds {:msg "idf check"} $result == true ==> mapEpochTokenomics[epochCounter].epochPoint.idf >= 1e18 && mapEpochTokenomics[epochCounter].epochPoint.idf <= 18e18;	// @audit-issue: Length of the line is: 177

1079:    /// ==> mapEpochTokenomics[epochCounter].unitPoints[0].rewardUnitFraction + mapEpochTokenomics[epochCounter].unitPoints[1].rewardUnitFraction + mapEpochTokenomics[epochCounter].epochPoint.rewardTreasuryFraction == 100;	// @audit-issue: Length of the line is: 223

1121:        // The actual inflation per epoch considering that it is settled not in the exact epochLen time, but a bit later	// @audit-issue: Length of the line is: 121

1142:            // Set the tokenomics parameters flag such that the maxBond is correctly updated below (3rd bit is set to one)	// @audit-issue: Length of the line is: 123

1163:        // This has to be always true, or incentives[4] == curMaxBond if the epoch is settled exactly at the epochLen time	// @audit-issue: Length of the line is: 123

1192:        // Update incentive fractions for the next epoch if they were requested by the changeIncentiveFractions() function	// @audit-issue: Length of the line is: 123

1216:            mapEpochStakingPoints[eCounter + 1].maxStakingIncentive = mapEpochStakingPoints[eCounter].maxStakingIncentive;	// @audit-issue: Length of the line is: 123

1229:        // we still record the amount of OLAS allocated for component / agent owner top-ups from the inflation schedule.	// @audit-issue: Length of the line is: 121

1241:        // Note that this computation happens before the epoch that is triggered in the next epoch (the code above) when	// @audit-issue: Length of the line is: 121

1301:    /// @notice If not all `unitIds` belonging to `account` were provided, they will be untouched and keep accumulating.	// @audit-issue: Length of the line is: 121

1341:            // Check that the unit Ids are in ascending order, not repeating, and no bigger than registries total supply	// @audit-issue: Length of the line is: 121

1379:    /// @notice `account` must be the owner of components / agents they are passing, otherwise the function will revert.	// @audit-issue: Length of the line is: 121

1413:            // Check that the unit Ids are in ascending order, not repeating, and no bigger than registries total supply	// @audit-issue: Length of the line is: 121

1449:                    uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100;	// @audit-issue: Length of the line is: 137
```
[300](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L300-L300), [308](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L308-L308), [349](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L349-L349), [382](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L382-L382), [396](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L396-L396), [397](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L397-L397), [398](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L398-L398), [400](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L400-L400), [401](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L401-L401), [402](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L402-L402), [403](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L403-L403), [405](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L405-L405), [407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L407-L407), [468](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L468-L468), [470](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L470-L470), [471](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L471-L471), [472](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L472-L472), [496](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L496-L496), [498](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L498-L498), [633](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L633-L633), [634](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L634-L634), [635](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L635-L635), [636](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L636-L636), [638](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L638-L638), [651](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L651-L651), [659](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L659-L659), [692](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L692-L692), [702](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L702-L702), [786](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L786-L786), [807](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L807-L807), [850](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L850-L850), [871](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L871-L871), [979](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L979-L979), [985](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L985-L985), [986](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L986-L986), [987](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L987-L987), [988](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L988-L988), [989](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L989-L989), [1066](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1066-L1066), [1070](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1070-L1070), [1071](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1071-L1071), [1072](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1072-L1072), [1074](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1074-L1074), [1075](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1075-L1075), [1079](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1079-L1079), [1121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1121-L1121), [1142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1142-L1142), [1163](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1163-L1163), [1192](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1192-L1192), [1216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1216-L1216), [1229](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1229-L1229), [1241](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1241-L1241), [1301](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1301-L1301), [1341](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1341-L1341), [1379](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1379-L1379), [1413](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1413-L1413), [1449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1449-L1449), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

70:        // For the first 10 years the inflation caps are pre-defined as differences between next year cap and current year one	// @audit-issue: Length of the line is: 127
```
[70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L70-L70), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

63:        // Even if the ETH inflation rate is 5% per year, it would take 130+ years to reach 2^96 - 1 of ETH total supply	// @audit-issue: Length of the line is: 121

71:        // The IDF depends on the epsilonRate value, idf = 1 + epsilonRate, and epsilonRate is bound by 17 with 18 decimals	// @audit-issue: Length of the line is: 124

77:        // 2^32 - 1 gives 136+ years counted in seconds starting from the year 1970, which is safe until the year of 2106	// @audit-issue: Length of the line is: 122

108:    /// @notice If not all `unitIds` belonging to `account` were provided, they will be untouched and keep accumulating.	// @audit-issue: Length of the line is: 121

185:    function nomineeRelativeWeight(bytes32 account, uint256 chainId, uint256 time) external view returns (uint256, uint256);	// @audit-issue: Length of the line is: 125

494:                IDepositProcessor(depositProcessor).sendMessageBatchNonEVM{value:valueAmounts[i]}(updatedStakingTargets,	// @audit-issue: Length of the line is: 121

504:    /// @param bridgePayloads Set of bridge payloads (if) necessary for a specific bridge relayer depending on chain Id.	// @audit-issue: Length of the line is: 121

601:                // Get the staking incentive to send as a deposit with, and the amount to return back to staking inflation	// @audit-issue: Length of the line is: 123

783:    /// @notice `msg.sender` must be the owner of components / agents they are passing, otherwise the function will revert.	// @audit-issue: Length of the line is: 124

784:    /// @notice If not all `unitIds` belonging to `msg.sender` were provided, they will be untouched and keep accumulating.	// @audit-issue: Length of the line is: 124

1050:    /// @notice Mind the gas spending depending on the max number of numChains * numTargetsPerChain * numEpochs to claim.	// @audit-issue: Length of the line is: 122

1056:    /// @param bridgePayloads Set of bridge payloads (if) necessary for a specific bridge relayer depending on chain Id.	// @audit-issue: Length of the line is: 121

1094:        (totalAmounts, stakingIncentives, transferAmounts) = _calculateStakingIncentivesBatch(numClaimedEpochs, chainIds,	// @audit-issue: Length of the line is: 122

1119:            _distributeStakingIncentivesBatch(chainIds, stakingTargets, stakingIncentives, bridgePayloads, transferAmounts,	// @audit-issue: Length of the line is: 124
```
[63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L63-L63), [71](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L71-L71), [77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L77-L77), [108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L108-L108), [185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L185-L185), [494](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L494-L494), [504](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L504-L504), [601](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L601-L601), [783](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L783-L783), [784](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L784-L784), [1050](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1050-L1050), [1056](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1056-L1056), [1094](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1094-L1094), [1119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1119-L1119), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

7:    // Source: https://github.com/OffchainLabs/token-bridge-contracts/blob/b3894ecc8b6185b2d505c71c9a7851725f53df15/contracts/tokenbridge/ethereum/gateway/L1ArbitrumGateway.sol#L238	// @audit-issue: Length of the line is: 182

19:    /// @param _to Account to be credited with the tokens in the L2 (can be the user's L2 account or a contract), not subject to L2 aliasing	// @audit-issue: Length of the line is: 141

20:    ///            This account, or its L2 alias if it have code in L1, will also be able to cancel the retryable ticket and receive callvalue refund	// @audit-issue: Length of the line is: 150

36:    // Source: https://github.com/OffchainLabs/nitro-contracts/blob/67127e2c2fd0943d9d87a05915d77b1f220906aa/src/bridge/Inbox.sol#L432	// @audit-issue: Length of the line is: 135

44:    /// @param gasLimit Max gas deducted from user's L2 balance to cover L2 execution. Should not be set to 1 (magic value used to trigger the RetryableData error)	// @audit-issue: Length of the line is: 164

45:    /// @param maxFeePerGas price bid for L2 execution. Should not be set to 1 (magic value used to trigger the RetryableData error)	// @audit-issue: Length of the line is: 133

59:    // Source: https://github.com/OffchainLabs/nitro-contracts/blob/67127e2c2fd0943d9d87a05915d77b1f220906aa/src/bridge/Outbox.sol#L78	// @audit-issue: Length of the line is: 135

62:    /// @dev the l2ToL1Sender behaves as the tx.origin, the msg.sender should be validated to protect against reentrancies	// @audit-issue: Length of the line is: 123

66:/// @title ArbitrumDepositProcessorL1 - Smart contract for sending tokens and data via Arbitrum bridge from L1 to L2 and processing data received from L2.	// @audit-issue: Length of the line is: 155

118:    ///         - maxSubmissionCostMessage: Max gas deducted from user's L2 balance to cover message base submission fee.	// @audit-issue: Length of the line is: 122

132:            uint256 maxSubmissionCostMessage) = abi.decode(bridgePayload, (address, uint256, uint256, uint256, uint256));	// @audit-issue: Length of the line is: 122
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L7-L7), [19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L19-L19), [20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L20-L20), [36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L36-L36), [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L44-L44), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L45-L45), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L59-L59), [62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L62-L62), [66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L66-L66), [118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L118-L118), [132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L132-L132), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

7:    // Source: https://github.com/ethereum-optimism/optimism/blob/65ec61dde94ffa93342728d324fecf474d228e1f/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L259	// @audit-issue: Length of the line is: 184

23:    // Source: https://github.com/ethereum-optimism/optimism/blob/65ec61dde94ffa93342728d324fecf474d228e1f/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L422	// @audit-issue: Length of the line is: 184

33:/// @title OptimismTargetDispenserL2 - Smart contract for processing tokens and data received on Optimism L2, and data sent back to L1.	// @audit-issue: Length of the line is: 136
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L7-L7), [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L23-L23), [33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L33-L33), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

18:/// @title DefaultDepositProcessorL1 - Smart contract for sending tokens and data via arbitrary bridge from L1 to L2 and processing data received from L2.	// @audit-issue: Length of the line is: 155

23:    event MessagePosted(uint256 indexed sequence, address[] targets, uint256[] stakingIncentives, uint256 transferAmount);	// @audit-issue: Length of the line is: 123
```
[18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L18-L18), [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L23-L23), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

8:    // Source for the possible utility contract: https://github.com/OffchainLabs/token-bridge-contracts/blob/b3894ecc8b6185b2d505c71c9a7851725f53df15/contracts/tokenbridge/arbitrum/L2ArbitrumMessenger.sol#L30	// @audit-issue: Length of the line is: 209

19:/// @title ArbitrumTargetDispenserL2 - Smart contract for processing tokens and data received on Arbitrum L2, and data sent back to L1.	// @audit-issue: Length of the line is: 136

30:    ///         Source: https://github.com/OffchainLabs/token-bridge-contracts/blob/b3894ecc8b6185b2d505c71c9a7851725f53df15/contracts/tokenbridge/libraries/AddressAliasHelper.sol#L21-L32	// @audit-issue: Length of the line is: 188
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L8-L8), [19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L19-L19), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L30-L30), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

7:    // Source: https://github.com/ethereum-optimism/optimism/blob/65ec61dde94ffa93342728d324fecf474d228e1f/packages/contracts-bedrock/contracts/L1/L1StandardBridge.sol#L188	// @audit-issue: Length of the line is: 173

29:    // Source: https://github.com/ethereum-optimism/optimism/blob/65ec61dde94ffa93342728d324fecf474d228e1f/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L259	// @audit-issue: Length of the line is: 184

45:    // Source: https://github.com/ethereum-optimism/optimism/blob/65ec61dde94ffa93342728d324fecf474d228e1f/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L422	// @audit-issue: Length of the line is: 184

55:/// @title OptimismDepositProcessorL1 - Smart contract for sending tokens and data via Optimism bridge from L1 to L2 and processing data received from L2.	// @audit-issue: Length of the line is: 155

110:            // Source: https://github.com/maticnetwork/pos-portal/blob/5fbd35ba9cdc8a07bf32d81d6d1f4ce745feabd6/flat/RootChainManager.sol#L2218	// @audit-issue: Length of the line is: 144
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L7-L7), [29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L29-L29), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L45-L45), [55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L55-L55), [110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L110-L110), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

72:        if (_olas == address(0) || _dispenser == address(0) || _stakingFactory == address(0) || _timelock == address(0)) {	// @audit-issue: Length of the line is: 123

171:    /// @notice This function is implemented for the compatibility purposes only, as no cross-bridge is happening on L1.	// @audit-issue: Length of the line is: 121
```
[72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L72-L72), [171](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L171-L171), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

8:    // Source: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/b9e129e65d34827d92fceeed8c87d3ecdfc801d0/src/interfaces/IWormholeRelayer.sol#L442	// @audit-issue: Length of the line is: 163

16:    // Source: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/b9e129e65d34827d92fceeed8c87d3ecdfc801d0/src/interfaces/IWormholeRelayer.sol#L122	// @audit-issue: Length of the line is: 163

24:    /// This function must be called with `msg.value` equal to `quoteEVMDeliveryPrice(targetChain, receiverValue, gasLimit)`	// @audit-issue: Length of the line is: 125

29:    /// @param receiverValue msg.value that delivery provider should pass in for call to `targetAddress` (in targetChain currency units)	// @audit-issue: Length of the line is: 137

30:    /// @param gasLimit gas limit with which to call `targetAddress`. Any units of gas unused will be refunded according to the	// @audit-issue: Length of the line is: 128

46:/// @title WormholeTargetDispenserL2 - Smart contract for processing tokens and data received via Wormhole on L2, and data sent back to L1.	// @audit-issue: Length of the line is: 140

112:        (uint256 cost, ) = IBridge(l2MessageRelayer).quoteEVMDeliveryPrice(uint16(l1SourceChainId), 0, gasLimitMessage);	// @audit-issue: Length of the line is: 121

152:        // Source code: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/b9e129e65d34827d92fceeed8c87d3ecdfc801d0/src/TokenBase.sol#L187	// @audit-issue: Length of the line is: 154
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L8-L8), [16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L16-L16), [24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L24-L24), [29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L29-L29), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L30-L30), [46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L46-L46), [112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L112-L112), [152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L152-L152), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

8:    // Source: https://github.com/omni/tokenbridge-contracts/blob/908a48107919d4ab127f9af07d44d47eac91547e/contracts/upgradeable_contracts/arbitrary_message/MessageDelivery.sol#L22	// @audit-issue: Length of the line is: 181

18:    // Source: https://github.com/omni/omnibridge/blob/c814f686487c50462b132b9691fd77cc2de237d3/contracts/upgradeable_contracts/components/common/TokensRelayer.sol#L80	// @audit-issue: Length of the line is: 168

23:    // Source: https://github.com/omni/omnibridge/blob/c814f686487c50462b132b9691fd77cc2de237d3/contracts/interfaces/IAMB.sol#L14	// @audit-issue: Length of the line is: 130

28:/// @title GnosisDepositProcessorL1 - Smart contract for sending tokens and data via Gnosis bridge from L1 to L2 and processing data received from L2.	// @audit-issue: Length of the line is: 151

76:            // Source: https://docs.gnosischain.com/bridges/Token%20Bridge/amb-bridge#how-to-check-if-amb-is-down-not-relaying-message	// @audit-issue: Length of the line is: 135
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L8-L8), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L18-L18), [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L23-L23), [28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L28-L28), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L76-L76), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

7:/// @title PolygonTargetDispenserL2 - Smart contract for processing tokens and data received on Polygon L2, and data sent back to L1.	// @audit-issue: Length of the line is: 134

36:        // Source: https://github.com/0xPolygon/fx-portal/blob/731959279a77b0779f8a1eccdaea710e0babee19/contracts/tunnel/FxBaseChildTunnel.sol#L50	// @audit-issue: Length of the line is: 147

37:        // Doc: https://docs.polygon.technology/pos/how-to/bridging/l1-l2-communication/state-transfer/#child-tunnel-contract	// @audit-issue: Length of the line is: 126

44:    // Source: https://github.com/0xPolygon/fx-portal/blob/731959279a77b0779f8a1eccdaea710e0babee19/contracts/tunnel/FxBaseChildTunnel.sol#L63	// @audit-issue: Length of the line is: 143

45:    // Doc: https://docs.polygon.technology/pos/how-to/bridging/l1-l2-communication/state-transfer/#child-tunnel-contract	// @audit-issue: Length of the line is: 122
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L7-L7), [36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L36-L36), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L37-L37), [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L44-L44), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L45-L45), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

7:/// @title WormholeDepositProcessorL1 - Smart contract for sending tokens and data via Wormhole bridge from L1 to L2 and processing data received from L2.	// @audit-issue: Length of the line is: 155

91:        // Source: https://github.com/wormhole-foundation/wormhole-solidity-sdk/blob/b9e129e65d34827d92fceeed8c87d3ecdfc801d0/src/TokenBase.sol#L125	// @audit-issue: Length of the line is: 149

92:        // Additional token source: https://github.com/wormhole-foundation/wormhole/blob/b18a7e61eb9316d620c888e01319152b9c8790f4/ethereum/contracts/bridge/Bridge.sol#L203	// @audit-issue: Length of the line is: 172
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L7-L7), [91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L91-L91), [92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L92-L92), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

8:    // Source: https://github.com/omni/tokenbridge-contracts/blob/908a48107919d4ab127f9af07d44d47eac91547e/contracts/upgradeable_contracts/arbitrary_message/MessageDelivery.sol#L22	// @audit-issue: Length of the line is: 181

17:    // Source: https://github.com/omni/omnibridge/blob/c814f686487c50462b132b9691fd77cc2de237d3/contracts/interfaces/IAMB.sol#L14	// @audit-issue: Length of the line is: 130

22:/// @title GnosisTargetDispenserL2 - Smart contract for processing tokens and data received on Gnosis L2, and data sent back to L1.	// @audit-issue: Length of the line is: 132

95:    // Source: https://github.com/omni/omnibridge/blob/c814f686487c50462b132b9691fd77cc2de237d3/contracts/upgradeable_contracts/BasicOmnibridge.sol#L464	// @audit-issue: Length of the line is: 153
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L8-L8), [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L17-L17), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L22-L22), [95](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L95-L95), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

41:/// @title DefaultTargetDispenserL2 - Smart contract for processing tokens and data received on L2, and data sent back to L1.	// @audit-issue: Length of the line is: 126

305:    /// @dev Processes the data manually provided by the DAO in order to restore the data that was not delivered from L1.	// @audit-issue: Length of the line is: 122

307:    ///         - Both token and message delivery fails: re-send OLAS to the contract (separate vote), call this function;	// @audit-issue: Length of the line is: 123
```
[41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L41-L41), [305](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L305-L305), [307](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L307-L307), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

11:    /// @dev This mechanism supports arbitrary tokens as long as its predicate has been registered and the token is mapped	// @audit-issue: Length of the line is: 123

18:/// @title PolygonDepositProcessorL1 - Smart contract for sending tokens and data via Polygon bridge from L1 to L2 and processing data received from L2.	// @audit-issue: Length of the line is: 153

67:            // Source: https://github.com/maticnetwork/pos-portal/blob/5fbd35ba9cdc8a07bf32d81d6d1f4ce745feabd6/flat/RootChainManager.sol#L2218	// @audit-issue: Length of the line is: 144

77:        // Source: https://github.com/0xPolygon/fx-portal/blob/731959279a77b0779f8a1eccdaea710e0babee19/contracts/FxRoot.sol#L29	// @audit-issue: Length of the line is: 129

78:        // Doc: https://docs.polygon.technology/pos/how-to/bridging/l1-l2-communication/state-transfer/#root-tunnel-contract	// @audit-issue: Length of the line is: 125

86:    // Source: https://github.com/0xPolygon/fx-portal/blob/731959279a77b0779f8a1eccdaea710e0babee19/contracts/tunnel/FxBaseRootTunnel.sol#L175	// @audit-issue: Length of the line is: 143

87:    // Doc: https://docs.polygon.technology/pos/how-to/bridging/l1-l2-communication/state-transfer/#root-tunnel-contract	// @audit-issue: Length of the line is: 121
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L11-L11), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L18-L18), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L67-L67), [77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L77-L77), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L78-L78), [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L86-L86), [87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L87-L87), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

13:/// @title StakingNativeToken - Smart contract for staking a service with the service having a native network token as the deposit	// @audit-issue: Length of the line is: 131
```
[13](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L13-L13), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

373:        // The staking deposit derived from a security deposit value must be greater or equal to the minimum defined one	// @audit-issue: Length of the line is: 121

379:        (uint256 numAgentIds, IService.AgentParams[] memory agentParams) = IService(serviceRegistry).getAgentParams(serviceId);	// @audit-issue: Length of the line is: 128

716:    /// @notice Each service must be staked for a minimum of maxInactivityDuration time, or until the funds are not zero.	// @audit-issue: Length of the line is: 122
```
[373](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L373-L373), [379](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L379-L379), [716](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L716-L716), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

40:/// @title StakingToken - Smart contract for staking a service by its owner when the service has an ERC20 token as the deposit	// @audit-issue: Length of the line is: 127

74:    function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {	// @audit-issue: Length of the line is: 125

93:            uint256 bond = IServiceTokenUtility(serviceRegistryTokenUtility).getAgentBond(serviceId, serviceAgentIds[i]);	// @audit-issue: Length of the line is: 122
```
[40](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L40-L40), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L74-L74), [93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L93-L93), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

22:    // Code position in storage is keccak256("SERVICE_STAKING_PROXY") = "0x9e5e169c1098011e4e5940a3ec1797686b2a8294a9b77a4c676b121bdc0ebb5e"	// @audit-issue: Length of the line is: 141
```
[22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L22-L22), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

147:    // For explanation about the delay consult the official audit report: https://github.com/trailofbits/publications/blob/master/reviews/CurveDAO.pdf	// @audit-issue: Length of the line is: 151

154:    // For gas concerns regarding checkpoint calculations, see the internal audit and the official audit report: https://github.com/trailofbits/publications/blob/master/reviews/CurveDAO.pdf	// @audit-issue: Length of the line is: 190

221:    /// @dev Fill sum of nominee weights for the same type week-over-week for missed checkins and return the sum for the future week.	// @audit-issue: Length of the line is: 134

413:    /// @dev Gets Nominee relative weight (not more than 1.0) normalized to 1e18 (e.g. 1.0 == 1e18) and a sum of weights.	// @audit-issue: Length of the line is: 122

436:    /// @dev Gets Nominee relative weight (not more than 1.0) normalized to 1e18 (e.g. 1.0 == 1e18) and a sum of weights.	// @audit-issue: Length of the line is: 122
```
[147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L147-L147), [154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L154-L154), [221](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L221-L221), [413](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L413-L413), [436](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L436-L436), 


#### Recommendation

To adhere to coding standards and enhance code readability, consider splitting long lines of code when they approach the recommended maximum line length. In Solidity, a common guideline is to limit lines to a maximum of 120 characters. Splitting long lines can improve code maintainability and make it easier to understand.

### Avoid the use of sensitive terms
Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

6:import {IDonatorBlacklist} from "./interfaces/IDonatorBlacklist.sol";	// @audit-issue: Replace `blacklist` with `denylist`

108:/// @dev The donator address is blacklisted.	// @audit-issue: Replace `blacklist` with `denylist`

110:error DonatorBlacklisted(address account);	// @audit-issue: Replace `blacklist` with `denylist`

276:    event DonatorBlacklistUpdated(address indexed blacklist);	// @audit-issue: Replace `blacklist` with `denylist`

352:    // Blacklist contract address	// @audit-issue: Replace `blacklist` with `denylist`

353:    address public donatorBlacklist;	// @audit-issue: Replace `blacklist` with `denylist`

392:    /// @param _donatorBlacklist DonatorBlacklist address.	// @audit-issue: Replace `blacklist` with `denylist`

404:    /// #if_succeeds {:msg "donatorBlacklist assignment"} donatorBlacklist == _donatorBlacklist;	// @audit-issue: Replace `blacklist` with `denylist`

419:        address _donatorBlacklist	// @audit-issue: Replace `blacklist` with `denylist`

459:        donatorBlacklist = _donatorBlacklist;	// @audit-issue: Replace `blacklist` with `denylist`

613:    /// @dev Changes donator blacklist contract address.	// @audit-issue: Replace `blacklist` with `denylist`

614:    /// @notice DonatorBlacklist contract can be disabled by setting its address to zero.	// @audit-issue: Replace `blacklist` with `denylist`

615:    /// @param _donatorBlacklist DonatorBlacklist contract address.	// @audit-issue: Replace `blacklist` with `denylist`

616:    function changeDonatorBlacklist(address _donatorBlacklist) external {	// @audit-issue: Replace `blacklist` with `denylist`

622:        donatorBlacklist = _donatorBlacklist;	// @audit-issue: Replace `blacklist` with `denylist`

623:        emit DonatorBlacklistUpdated(_donatorBlacklist);	// @audit-issue: Replace `blacklist` with `denylist`

1001:        // Check if the donator blacklist is enabled, and the status of the donator address	// @audit-issue: Replace `blacklist` with `denylist`

1002:        address bList = donatorBlacklist;	// @audit-issue: Replace `blacklist` with `denylist`

1003:        if (bList != address(0) && IDonatorBlacklist(bList).isDonatorBlacklisted(donator)) {	// @audit-issue: Replace `blacklist` with `denylist`

1004:            revert DonatorBlacklisted(donator);	// @audit-issue: Replace `blacklist` with `denylist`
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L6-L6), [108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L108-L108), [110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L110-L110), [276](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L276-L276), [352](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L352-L352), [353](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L353-L353), [392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L392-L392), [404](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L404-L404), [419](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L419-L419), [459](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L459-L459), [613](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L613-L613), [614](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L614-L614), [615](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L615-L615), [616](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L616-L616), [622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L622-L622), [623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L623-L623), [1001](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1001-L1001), [1002](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1002-L1002), [1003](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1003-L1003), [1004](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1004-L1004), 


```solidity
Path: ./tokenomics/contracts/interfaces/IDonatorBlacklist.sol

4:/// @dev DonatorBlacklist interface.	// @audit-issue: Replace `blacklist` with `denylist`

5:interface IDonatorBlacklist {	// @audit-issue: Replace `blacklist` with `denylist`

6:    /// @dev Gets account blacklisting status.	// @audit-issue: Replace `blacklist` with `denylist`

8:    /// @return status Blacklisting status.	// @audit-issue: Replace `blacklist` with `denylist`

9:    function isDonatorBlacklisted(address account) external view returns (bool status);	// @audit-issue: Replace `blacklist` with `denylist`
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L4-L4), [5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L5-L5), [6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L6-L6), [8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L8-L8), [9](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L9-L9), 


```solidity
Path: ./tokenomics/contracts/interfaces/IErrorsTokenomics.sol

43:    /// @dev Token is disabled or not whitelisted.	// @audit-issue: Replace `whitelist` with `allowlist`

114:    /// @dev The donator address is blacklisted.	// @audit-issue: Replace `blacklist` with `denylist`

116:    error DonatorBlacklisted(address account);	// @audit-issue: Replace `blacklist` with `denylist`
```
[43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L43-L43), [114](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L114-L114), [116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L116-L116), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

46:    event ImplementationsWhitelistUpdated(address[] implementations, bool[] statuses, bool setCheck);	// @audit-issue: Replace `whitelist` with `allowlist`

63:    // Flag to check for the implementation address whitelisting status	// @audit-issue: Replace `whitelist` with `allowlist`

66:    // Mapping implementation address => whitelisting status	// @audit-issue: Replace `whitelist` with `allowlist`

111:    /// @dev Controls the necessity of checking implementation whitelisting statuses.	// @audit-issue: Replace `whitelist` with `allowlist`

112:    /// @param setCheck True if the whitelisting check is needed, and false otherwise.	// @audit-issue: Replace `whitelist` with `allowlist`

124:    /// @dev Controls implementations whitelisting statuses.	// @audit-issue: Replace `whitelist` with `allowlist`

125:    /// @notice Implementation is considered whitelisted if the global status is set to true.	// @audit-issue: Replace `whitelist` with `allowlist`

127:    ///         This is the owner responsibility how to manage the whitelisting logic.	// @audit-issue: Replace `whitelist` with `allowlist`

129:    /// @param statuses Set of whitelisting statuses.	// @audit-issue: Replace `whitelist` with `allowlist`

130:    /// @param setCheck True if the whitelisting check is needed, and false otherwise.	// @audit-issue: Replace `whitelist` with `allowlist`

149:        // Set implementations whitelisting status	// @audit-issue: Replace `whitelist` with `allowlist`

156:            // Set the operator whitelisting status	// @audit-issue: Replace `whitelist` with `allowlist`

160:        emit ImplementationsWhitelistUpdated(implementations, statuses, setCheck);	// @audit-issue: Replace `whitelist` with `allowlist`

167:        // Check the operator whitelisting status, if the whitelisting check is set	// @audit-issue: Replace `whitelist` with `allowlist`

180:        // If the implementations check is true, and the implementation is not whitelisted, the verification is failed	// @audit-issue: Replace `whitelist` with `allowlist`
```
[46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L46-L46), [63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L63-L63), [66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L66-L66), [111](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L111-L111), [112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L112-L112), [124](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L124-L124), [125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L125-L125), [127](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L127-L127), [129](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L129-L129), [130](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L130-L130), [149](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L149-L149), [156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L156-L156), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L160-L160), [167](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L167-L167), [180](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L180-L180), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

111:/// @dev Multisig is not whitelisted.	// @audit-issue: Replace `whitelist` with `allowlist`
```
[111](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L111-L111), 


#### Recommendation

To promote inclusive language and avoid insensitive terminology, consider using alternative terms such as 'allowlist' instead of 'whitelist' and 'denylist' instead of 'blacklist.' These alternatives help create a more welcoming and inclusive environment in code and documentation.

### Dependence on external protocols
External protocols should be monitored as such dependencies may introduce vulnerabilities if a vulnerability is found /introduced in the external protocol

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

4:import {convert, UD60x18} from "@prb/math/src/UD60x18.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L4-L4), 


#### Recommendation

Regularly monitor and review the external protocols your Solidity contracts depend on for any updates or identified vulnerabilities. Consider implementing fallback mechanisms or contingency plans in your contracts to handle potential failures or compromises in these external protocols. Additionally, thoroughly audit and test the integration points with external protocols to ensure they adhere to your contract's security standards. Where possible, design your contract architecture to minimize reliance on external protocols or allow for upgradability in response to changes in these dependencies. Stay informed about developments in the protocols you depend on and actively participate in their community for early awareness of potential issues.

### Bug in Deduplication of Verbatim Blocks
The current Solidity version has the [Bug in Deduplication of Verbatim Blocks](https://soliditylang.org/blog/2023/11/08/verbatim-invalid-deduplication-bug/) issue.

```solidity
Path: ./tokenomics/contracts/interfaces/IDonatorBlacklist.sol

2:pragma solidity ^0.8.18;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L2-L2), 


```solidity
Path: ./tokenomics/contracts/interfaces/IErrorsTokenomics.sol

2:pragma solidity ^0.8.18;	// @audit-issue
```
[2](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L2-L2), 


#### Recommendation

It is recommended to follow the fix provided in the [link](https://soliditylang.org/blog/2023/11/08/verbatim-invalid-deduplication-bug/).

### Use `bytes.concat()` on `bytes` instead of `abi.encodePacked()` for clearer semantic meaning
Starting with version 0.8.4, Solidity has the `bytes.concat()` function, which allows one to concatenate a list of bytes/strings, without extra padding. Using this function rather than `abi.encodePacked()` makes the intended operation more clear, leading to less reviewer confusion.

```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

143:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue

146:        bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,	// @audit-issue
147:            uint256(uint160(implementation)));

151:            abi.encodePacked(	// @audit-issue
152:                bytes1(0xff), address(this), salt, keccak256(deploymentData)
153:            )

201:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue

203:        bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,	// @audit-issue
204:            uint256(uint160(implementation)));
```
[143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L143-L143), [146](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L146-L147), [151](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L151-L153), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L201-L201), [203](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L203-L204), 


#### Recommendation

Consider using `bytes.concat()` instead of `abi.encodePacked()` for concatenating `bytes` for clearer semantic meaning, especially in Solidity versions 0.8.4 and later.

### Use scientific notation (e.g. `1e18`) rather than exponentiation (e.g. `10**18`)
While the compiler knows to optimize away the exponentiation, it's still better coding practice to use idioms that do not require compiler optimization, if they exist


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

23:    uint256 public constant MAX_STAKING_WEIGHT = 10_000;	// @audit-issue: This number `10_000` can be written as `1e4`
```
[23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L23-L23), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

157:    uint256 public constant MAX_WEIGHT = 10_000;	// @audit-issue: This number `10_000` can be written as `1e4`
```
[157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L157-L157), 


#### Recommendation

Consider using scientific notation (e.g., `1e18`) instead of exponentiation (e.g., `10**18`) for better coding practice. While the compiler can optimize exponentiation, using scientific notation makes the code more readable and relies on standard idioms.

### Style Guide: Surround top level declarations in Solidity source with two blank lines.
1- Surround top level declarations in Solidity source with two blank lines.
2- Within a contract surround function declarations with a single blank line.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

374:    mapping(uint256 => StakingPoint) public mapEpochStakingPoints;
375:
376:    /// @dev Tokenomics constructor.	// @audit-issue: There should be single blank line between function declarations.
377:    constructor()

379:    {}
380:
381:    /// @dev Tokenomics initializer.
382:    /// @notice Tokenomics contract must be initialized no later than one year from the launch of the OLAS token contract.
383:    /// @param _olas OLAS token address.
384:    /// @param _treasury Treasury address.
385:    /// @param _depository Depository address.
386:    /// @param _dispenser Dispenser address.
387:    /// @param _ve Voting Escrow address.
388:    /// @param _epochLen Epoch length.
389:    /// @param _componentRegistry Component registry address.
390:    /// @param _agentRegistry Agent registry address.
391:    /// @param _serviceRegistry Service registry address.
392:    /// @param _donatorBlacklist DonatorBlacklist address.
393:    /// #if_succeeds {:msg "ep is correct endTime"} mapEpochTokenomics[0].epochPoint.endTime > 0;
394:    /// #if_succeeds {:msg "maxBond eq effectiveBond form start"} effectiveBond == maxBond;
395:    /// #if_succeeds {:msg "olas must not be a zero address"} old(_olas) != address(0) ==> olas == _olas;
396:    /// #if_succeeds {:msg "treasury must not be a zero address"} old(_treasury) != address(0) ==> treasury == _treasury;
397:    /// #if_succeeds {:msg "depository must not be a zero address"} old(_depository) != address(0) ==> depository == _depository;
398:    /// #if_succeeds {:msg "dispenser must not be a zero address"} old(_dispenser) != address(0) ==> dispenser == _dispenser;
399:    /// #if_succeeds {:msg "vaOLAS must not be a zero address"} old(_ve) != address(0) ==> ve == _ve;
400:    /// #if_succeeds {:msg "epochLen"} old(_epochLen > MIN_EPOCH_LENGTH && _epochLen <= type(uint32).max) ==> epochLen == _epochLen;
401:    /// #if_succeeds {:msg "componentRegistry must not be a zero address"} old(_componentRegistry) != address(0) ==> componentRegistry == _componentRegistry;
402:    /// #if_succeeds {:msg "agentRegistry must not be a zero address"} old(_agentRegistry) != address(0) ==> agentRegistry == _agentRegistry;
403:    /// #if_succeeds {:msg "serviceRegistry must not be a zero address"} old(_serviceRegistry) != address(0) ==> serviceRegistry == _serviceRegistry;
404:    /// #if_succeeds {:msg "donatorBlacklist assignment"} donatorBlacklist == _donatorBlacklist;
405:    /// #if_succeeds {:msg "inflationPerSecond must not be zero"} inflationPerSecond > 0 && inflationPerSecond <= getInflationForYear(0);
406:    /// #if_succeeds {:msg "Zero epoch point end time must be non-zero"} mapEpochTokenomics[0].epochPoint.endTime > 0;
407:    /// #if_succeeds {:msg "maxBond"} old(_epochLen > MIN_EPOCH_LENGTH && _epochLen <= type(uint32).max && inflationPerSecond > 0 && inflationPerSecond <= getInflationForYear(0))
408:    /// ==> maxBond == (inflationPerSecond * _epochLen * mapEpochTokenomics[1].epochPoint.maxBondFraction) / 100;	// @audit-issue: There should be single blank line between function declarations.
409:    function initializeTokenomics(

512:    }
513:
514:    /// @dev Gets the tokenomics implementation contract address.
515:    /// @return implementation Tokenomics implementation contract address.	// @audit-issue: There should be single blank line between function declarations.
516:    function tokenomicsImplementation() external view returns (address implementation) {

520:    }
521:
522:    /// @dev Changes the tokenomics implementation contract address.
523:    /// @notice Make sure the implementation contract has a function to change the implementation.
524:    /// @param implementation Tokenomics implementation contract address.
525:    /// #if_succeeds {:msg "new implementation"} implementation == tokenomicsImplementation();	// @audit-issue: There should be single blank line between function declarations.
526:    function changeTokenomicsImplementation(address implementation) external {

542:    }
543:
544:    /// @dev Changes the owner address.
545:    /// @param newOwner Address of a new owner.	// @audit-issue: There should be single blank line between function declarations.
546:    function changeOwner(address newOwner) external {

559:    }
560:
561:    /// @dev Changes various managing contract addresses.
562:    /// @param _treasury Treasury address.
563:    /// @param _depository Depository address.
564:    /// @param _dispenser Dispenser address.	// @audit-issue: There should be single blank line between function declarations.
565:    function changeManagers(address _treasury, address _depository, address _dispenser) external {

586:    }
587:
588:    /// @dev Changes registries contract addresses.
589:    /// @param _componentRegistry Component registry address.
590:    /// @param _agentRegistry Agent registry address.
591:    /// @param _serviceRegistry Service registry address.	// @audit-issue: There should be single blank line between function declarations.
592:    function changeRegistries(address _componentRegistry, address _agentRegistry, address _serviceRegistry) external {

611:    }
612:
613:    /// @dev Changes donator blacklist contract address.
614:    /// @notice DonatorBlacklist contract can be disabled by setting its address to zero.
615:    /// @param _donatorBlacklist DonatorBlacklist contract address.	// @audit-issue: There should be single blank line between function declarations.
616:    function changeDonatorBlacklist(address _donatorBlacklist) external {

624:    }
625:
626:    /// @dev Changes tokenomics parameters.
627:    /// @notice Parameter values are not updated for those that are passed as zero or out of defined bounds.
628:    /// @param _devsPerCapital Number of valuable devs can be paid per units of capital per epoch.
629:    /// @param _codePerDev Number of units of useful code that can be built by a developer during one epoch.
630:    /// @param _epsilonRate Epsilon rate that contributes to the interest rate value.
631:    /// @param _epochLen New epoch length.
632:    /// #if_succeeds {:msg "ep is correct endTime"} epochCounter > 1
633:    /// ==> mapEpochTokenomics[epochCounter - 1].epochPoint.endTime > mapEpochTokenomics[epochCounter - 2].epochPoint.endTime;
634:    /// #if_succeeds {:msg "epochLen"} old(_epochLen > MIN_EPOCH_LENGTH && _epochLen <= ONE_YEAR && epochLen != _epochLen) ==> nextEpochLen == _epochLen;
635:    /// #if_succeeds {:msg "devsPerCapital"} _devsPerCapital > MIN_PARAM_VALUE && _devsPerCapital <= type(uint72).max ==> devsPerCapital == _devsPerCapital;
636:    /// #if_succeeds {:msg "codePerDev"} _codePerDev > MIN_PARAM_VALUE && _codePerDev <= type(uint72).max ==> codePerDev == _codePerDev;
637:    /// #if_succeeds {:msg "epsilonRate"} _epsilonRate > 0 && _epsilonRate < 17e18 ==> epsilonRate == _epsilonRate;
638:    /// #if_succeeds {:msg "veOLASThreshold"} _veOLASThreshold > 0 && _veOLASThreshold <= type(uint96).max ==> nextVeOLASThreshold == _veOLASThreshold;	// @audit-issue: There should be single blank line between function declarations.
639:    function changeTokenomicsParameters(

694:    }
695:
696:    /// @dev Sets incentive parameter fractions.
697:    /// @param _rewardComponentFraction Fraction for component owner rewards funded by ETH donations.
698:    /// @param _rewardAgentFraction Fraction for agent owner rewards funded by ETH donations.
699:    /// @param _maxBondFraction Fraction for the maxBond that depends on the OLAS inflation.
700:    /// @param _topUpComponentFraction Fraction for component owners OLAS top-up.
701:    /// @param _topUpAgentFraction Fraction for agent owners OLAS top-up.
702:    /// #if_succeeds {:msg "maxBond"} mapEpochTokenomics[epochCounter + 1].epochPoint.maxBondFraction == _maxBondFraction;	// @audit-issue: There should be single blank line between function declarations.
703:    function changeIncentiveFractions(

746:    }
747:
748:    /// @dev Sets staking parameters by the DAO.
749:    /// @param _maxStakingIncentive Max allowed staking incentive threshold.
750:    /// @param _minStakingWeight Min staking weight threshold bound by 10_000.	// @audit-issue: There should be single blank line between function declarations.
751:    function changeStakingParams(uint256 _maxStakingIncentive, uint256 _minStakingWeight) external {

780:    }
781:
782:    /// @dev Reserves OLAS amount from the effective bond to be minted during a bond program.
783:    /// @notice Programs exceeding the limit of the effective bond are not allowed.
784:    /// @param amount Requested amount for the bond program.
785:    /// @return success True if effective bond threshold is not reached.
786:    /// #if_succeeds {:msg "effectiveBond"} old(effectiveBond) > amount ==> effectiveBond == old(effectiveBond) - amount;	// @audit-issue: There should be single blank line between function declarations.
787:    function reserveAmountForBondProgram(uint256 amount) external returns (bool success) {

803:    }
804:
805:    /// @dev Refunds unused bond program amount when the program is closed.
806:    /// @param amount Amount to be refunded from the closed bond program.
807:    /// #if_succeeds {:msg "effectiveBond"} old(effectiveBond + amount) <= type(uint96).max ==> effectiveBond == old(effectiveBond) + amount;	// @audit-issue: There should be single blank line between function declarations.
808:    function refundFromBondProgram(uint256 amount) external {

822:    }
823:
824:    /// @dev Records amount returned back from staking to the inflation.
825:    /// @param amount OLAS amount returned from staking.	// @audit-issue: There should be single blank line between function declarations.
826:    function refundFromStaking(uint256 amount) external {

841:    }
842:
843:    /// @dev Finalizes epoch incentives for a specified component / agent Id.
844:    /// @param epochNum Epoch number to finalize incentives for.
845:    /// @param unitType Unit type (component / agent).
846:    /// @param unitId Unit Id.	// @audit-issue: There should be single blank line between function declarations.
847:    function _finalizeIncentivesForUnitId(uint256 epochNum, uint256 unitType, uint256 unitId) internal {

877:    }
878:
879:    /// @dev Records service donations into corresponding data structures.
880:    /// @param donator Donator account address.
881:    /// @param serviceIds Set of service Ids.
882:    /// @param amounts Correspondent set of ETH amounts provided by services.
883:    /// @param curEpoch Current epoch number.	// @audit-issue: There should be single blank line between function declarations.
884:    function _trackServiceDonations(

976:    }
977:
978:    /// @dev Tracks the deposited ETH service donations during the current epoch.
979:    /// @notice This function is only called by the treasury where the validity of arrays and values has been performed.
980:    /// @notice Donating to services must not be followed by the checkpoint in the same block.
981:    /// @param donator Donator account address.
982:    /// @param serviceIds Set of service Ids.
983:    /// @param amounts Correspondent set of ETH amounts provided by services.
984:    /// @param donationETH Overall service donation amount in ETH.
985:    /// #if_succeeds {:msg "totalDonationsETH can only increase"} old(mapEpochTokenomics[epochCounter].epochPoint.totalDonationsETH) + donationETH <= type(uint96).max
986:    /// ==> mapEpochTokenomics[epochCounter].epochPoint.totalDonationsETH == old(mapEpochTokenomics[epochCounter].epochPoint.totalDonationsETH) + donationETH;
987:    /// #if_succeeds {:msg "sumUnitTopUpsOLAS for components can only increase"} mapEpochTokenomics[epochCounter].unitPoints[0].sumUnitTopUpsOLAS >= old(mapEpochTokenomics[epochCounter].unitPoints[0].sumUnitTopUpsOLAS);
988:    /// #if_succeeds {:msg "sumUnitTopUpsOLAS for agents can only increase"} mapEpochTokenomics[epochCounter].unitPoints[1].sumUnitTopUpsOLAS >= old(mapEpochTokenomics[epochCounter].unitPoints[1].sumUnitTopUpsOLAS);
989:    /// #if_succeeds {:msg "numNewOwners can only increase"} mapEpochTokenomics[epochCounter].epochPoint.numNewOwners >= old(mapEpochTokenomics[epochCounter].epochPoint.numNewOwners);	// @audit-issue: There should be single blank line between function declarations.
990:    function trackServiceDonations(

1027:    }
1028:
1029:    /// @dev Gets the inverse discount factor value.
1030:    /// @param treasuryRewards Treasury rewards.
1031:    /// @param numNewOwners Number of new owners of components / agents registered during the epoch.
1032:    /// @return idf IDF value.	// @audit-issue: There should be single blank line between function declarations.
1033:    function _calculateIDF(uint256 treasuryRewards, uint256 numNewOwners) internal view returns (uint256 idf) {

1062:    }
1063:
1064:    /// @dev Record global data with a new checkpoint.
1065:    /// @notice Note that even though a specific epoch can last longer than the epochLen, it is practically
1066:    ///         not valid not to call a checkpoint for longer than a year. Thus, the function will return false otherwise.
1067:    /// @notice Checkpoint must not be called in the same block with the service donation.
1068:    /// @return True if the function execution is successful.
1069:    /// #if_succeeds {:msg "epochCounter can only increase"} $result == true ==> epochCounter == old(epochCounter) + 1;
1070:    /// #if_succeeds {:msg "two events will never happen at the same time"} $result == true && (block.timestamp - timeLaunch) / ONE_YEAR > old(currentYear) ==> currentYear == old(currentYear) + 1;
1071:    /// #if_succeeds {:msg "previous epoch endTime must never be zero"} mapEpochTokenomics[epochCounter - 1].epochPoint.endTime > 0;
1072:    /// #if_succeeds {:msg "when the year is the same, the adjusted maxBond (incentives[4]) will never be lower than the epoch maxBond"}
1073:    ///$result == true && (block.timestamp - timeLaunch) / ONE_YEAR == old(currentYear)
1074:    /// ==> old((inflationPerSecond * (block.timestamp - mapEpochTokenomics[epochCounter - 1].epochPoint.endTime) * mapEpochTokenomics[epochCounter].epochPoint.maxBondFraction) / 100) >= old(maxBond);
1075:    /// #if_succeeds {:msg "idf check"} $result == true ==> mapEpochTokenomics[epochCounter].epochPoint.idf >= 1e18 && mapEpochTokenomics[epochCounter].epochPoint.idf <= 18e18;
1076:    /// #if_succeeds {:msg "devsPerCapital check"} $result == true ==> devsPerCapital > MIN_PARAM_VALUE;
1077:    /// #if_succeeds {:msg "codePerDev check"} $result == true ==> codePerDev > MIN_PARAM_VALUE;
1078:    /// #if_succeeds {:msg "sum of reward fractions must result in 100"} $result == true
1079:    /// ==> mapEpochTokenomics[epochCounter].unitPoints[0].rewardUnitFraction + mapEpochTokenomics[epochCounter].unitPoints[1].rewardUnitFraction + mapEpochTokenomics[epochCounter].epochPoint.rewardTreasuryFraction == 100;	// @audit-issue: There should be single blank line between function declarations.
1080:    function checkpoint() external returns (bool) {

1297:    }
1298:
1299:    /// @dev Gets component / agent owner incentives and clears the balances.
1300:    /// @notice `account` must be the owner of components / agents Ids, otherwise the function will revert.
1301:    /// @notice If not all `unitIds` belonging to `account` were provided, they will be untouched and keep accumulating.
1302:    /// @notice Component and agent Ids must be provided in the ascending order and must not repeat.
1303:    /// @param account Account address.
1304:    /// @param unitTypes Set of unit types (component / agent).
1305:    /// @param unitIds Set of corresponding unit Ids where account is the owner.
1306:    /// @return reward Reward amount.
1307:    /// @return topUp Top-up amount.	// @audit-issue: There should be single blank line between function declarations.
1308:    function accountOwnerIncentives(

1376:    }
1377:
1378:    /// @dev Gets the component / agent owner incentives.
1379:    /// @notice `account` must be the owner of components / agents they are passing, otherwise the function will revert.
1380:    /// @param account Account address.
1381:    /// @param unitTypes Set of unit types (component / agent).
1382:    /// @param unitIds Set of corresponding unit Ids where account is the owner.
1383:    /// @return reward Reward amount.
1384:    /// @return topUp Top-up amount.	// @audit-issue: There should be single blank line between function declarations.
1385:    function getOwnerIncentives(

1460:    }
1461:
1462:    /// @dev Gets component / agent point of a specified epoch number and a unit type.
1463:    /// @param epoch Epoch number.
1464:    /// @param unitType Component (0) or agent (1).
1465:    /// @return Unit point.	// @audit-issue: There should be single blank line between function declarations.
1466:    function getUnitPoint(uint256 epoch, uint256 unitType) external view returns (UnitPoint memory) {

1468:    }
1469:
1470:    /// @dev Gets inverse discount factor with the multiple of 1e18 of the last epoch.
1471:    /// @return Discount factor with the multiple of 1e18.	// @audit-issue: There should be single blank line between function declarations.
1472:    function getLastIDF() external view returns (uint256) {

1474:    }
1475:
1476:    /// @dev Gets epoch end time.
1477:    /// @param epoch Epoch number.
1478:    /// @return Epoch end time.	// @audit-issue: There should be single blank line between function declarations.
1479:    function getEpochEndTime(uint256 epoch) external view returns (uint256) {
```
[376](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L374-L377), [408](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L379-L409), [515](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L512-L516), [525](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L520-L526), [545](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L542-L546), [564](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L559-L565), [591](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L586-L592), [615](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L611-L616), [638](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L624-L639), [702](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L694-L703), [750](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L746-L751), [786](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L780-L787), [807](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L803-L808), [825](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L822-L826), [846](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L841-L847), [883](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L877-L884), [989](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L976-L990), [1032](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1027-L1033), [1079](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1062-L1080), [1307](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1297-L1308), [1384](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1376-L1385), [1465](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1460-L1466), [1471](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1468-L1472), [1478](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1474-L1479), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

23:    uint256 public constant MAX_STAKING_WEIGHT = 10_000;
24:
25:    /// @dev Gets an inflation cap for a specific year.
26:    /// @param numYears Number of years passed from the launch date.
27:    /// @return supplyCap Supply cap.
28:    /// supplyCap = 1e27 * (1.02)^(x-9) for x >= 10
29:    /// if_succeeds {:msg "correct supplyCap"} (numYears >= 10) ==> (supplyCap > 1e27);  
30:    /// There is a bug in scribble tools, a broken instrumented version is as follows:
31:    /// function getSupplyCapForYear(uint256 numYears) public returns (uint256 supplyCap)
32:    /// And the test is waiting for a view / pure function, which would be correct	// @audit-issue: There should be single blank line between function declarations.
33:    function getSupplyCapForYear(uint256 numYears) public pure returns (uint256 supplyCap) {

64:    }
65:
66:    /// @dev Gets an inflation amount for a specific year.
67:    /// @param numYears Number of years passed from the launch date.
68:    /// @return inflationAmount Inflation limit amount.	// @audit-issue: There should be single blank line between function declarations.
69:    function getInflationForYear(uint256 numYears) public pure returns (uint256 inflationAmount) {
```
[32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L23-L33), [68](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L64-L69), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

308:    mapping(uint256 => uint256) public mapChainIdWithheldAmounts;
309:
310:    /// @dev Dispenser constructor.
311:    /// @param _olas OLAS token address.
312:    /// @param _tokenomics Tokenomics address.
313:    /// @param _treasury Treasury address.
314:    /// @param _voteWeighting Vote Weighting address.	// @audit-issue: There should be single blank line between function declarations.
315:    constructor(

349:    }
350:
351:    /// @dev Checkpoints specified staking target (nominee in Vote Weighting) and gets claimed epoch counters.
352:    /// @param nomineeHash Hash of a Nominee(stakingTarget, chainId).
353:    /// @param numClaimedEpochs Specified number of claimed epochs.
354:    /// @return firstClaimedEpoch First claimed epoch number.
355:    /// @return lastClaimedEpoch Last claimed epoch number (not included in claiming).	// @audit-issue: There should be single blank line between function declarations.
356:    function _checkpointNomineeAndGetClaimedEpochCounters(

400:    }
401:
402:    /// @dev Distributes staking incentives to a corresponding staking target.
403:    /// @param chainId Chain Id.
404:    /// @param stakingTarget Staking target corresponding to the chain Id.
405:    /// @param stakingIncentive Corresponding staking incentive.
406:    /// @param bridgePayload Bridge payload necessary (if required) for a specific bridge relayer.
407:    /// @param transferAmount Actual OLAS amount to be transferred.	// @audit-issue: There should be single blank line between function declarations.
408:    function _distributeStakingIncentives(

432:    }
433:
434:    /// @dev Distributes staking incentives to corresponding staking targets.
435:    /// @param chainIds Set of chain Ids.
436:    /// @param stakingTargets Set of staking target addresses corresponding to each chain Id.
437:    /// @param stakingIncentives Corresponding set of staking incentives.
438:    /// @param bridgePayloads Bridge payloads (if) necessary for a specific bridge relayer depending on chain Id.
439:    /// @param transferAmounts Set of actual total OLAS amounts across all the targets to be transferred.
440:    /// @param valueAmounts Set of value amounts required to provide to some of the bridges.	// @audit-issue: There should be single blank line between function declarations.
441:    function _distributeStakingIncentivesBatch(

498:    }
499:
500:    /// @dev Checks strict ascending order of chain Ids and staking targets to ensure the absence of duplicates.
501:    /// @param chainIds Set of chain Ids.
502:    /// @notice The function is not view deliberately such that all the reverts are executed correctly.
503:    /// @param stakingTargets Set of staking target addresses corresponding to each chain Id.
504:    /// @param bridgePayloads Set of bridge payloads (if) necessary for a specific bridge relayer depending on chain Id.
505:    /// @param valueAmounts Set of value amounts required to provide to some of the bridges.	// @audit-issue: There should be single blank line between function declarations.
506:    function _checkOrderAndValues(

564:    }
565:
566:    /// @dev Calculates staking incentives for sets of staking targets corresponding to a set of chain Ids.
567:    /// @param numClaimedEpochs Specified number of claimed epochs.
568:    /// @param chainIds Set of chain Ids.
569:    /// @param stakingTargets Set of staking target addresses corresponding to each chain Id.
570:    /// @return totalAmounts Total calculated amounts: staking, transfer, return.
571:    /// @return stakingIncentives Sets of staking incentives.
572:    /// @return transferAmounts Set of transfer amounts.	// @audit-issue: There should be single blank line between function declarations.
573:    function _calculateStakingIncentivesBatch(

632:    }
633:
634:    /// @dev Changes the owner address.
635:    /// @param newOwner Address of a new owner.	// @audit-issue: There should be single blank line between function declarations.
636:    function changeOwner(address newOwner) external {

649:    }
650:
651:    /// @dev Changes various managing contract addresses.
652:    /// @param _tokenomics Tokenomics address.
653:    /// @param _treasury Treasury address.
654:    /// @param _voteWeighting Vote Weighting address.	// @audit-issue: There should be single blank line between function declarations.
655:    function changeManagers(address _tokenomics, address _treasury, address _voteWeighting) external {

678:    }
679:
680:    /// @dev Changes staking params by the DAO.
681:    /// @param _maxNumClaimingEpochs Maximum number of epochs to claim staking incentives for.
682:    /// @param _maxNumStakingTargets Maximum number of staking targets available to claim for on a single chain Id.	// @audit-issue: There should be single blank line between function declarations.
683:    function changeStakingParams(uint256 _maxNumClaimingEpochs, uint256 _maxNumStakingTargets) external {

698:    }
699:
700:    /// @dev Sets deposit processor contracts addresses and L2 chain Ids.
701:    /// @notice It is the contract owner responsibility to set correct L1 deposit processor contracts
702:    ///         and corresponding supported L2 chain Ids.
703:    /// @param depositProcessors Set of deposit processor contract addresses on L1.
704:    /// @param chainIds Set of corresponding L2 chain Ids.	// @audit-issue: There should be single blank line between function declarations.
705:    function setDepositProcessorChainIds(address[] memory depositProcessors, uint256[] memory chainIds) external {

728:    }
729:
730:    /// @dev Records nominee starting epoch number.
731:    /// @param nomineeHash Nominee hash.	// @audit-issue: There should be single blank line between function declarations.
732:    function addNominee(bytes32 nomineeHash) external {

746:    }
747:
748:    /// @dev Records nominee removal epoch number.
749:    /// @param nomineeHash Nominee hash.	// @audit-issue: There should be single blank line between function declarations.
750:    function removeNominee(bytes32 nomineeHash) external {

780:    }
781:
782:    /// @dev Claims incentives for the owner of components / agents.
783:    /// @notice `msg.sender` must be the owner of components / agents they are passing, otherwise the function will revert.
784:    /// @notice If not all `unitIds` belonging to `msg.sender` were provided, they will be untouched and keep accumulating.
785:    /// @param unitTypes Set of unit types (component / agent).
786:    /// @param unitIds Set of corresponding unit Ids where account is the owner.
787:    /// @return reward Reward amount in ETH.
788:    /// @return topUp Top-up amount in OLAS.	// @audit-issue: There should be single blank line between function declarations.
789:    function claimOwnerIncentives(

837:    }
838:
839:    /// @dev Calculates staking incentives for a specific staking target.
840:    /// @notice Call this function via staticcall in order not to write in the nominee checkpoint map.
841:    /// @param numClaimedEpochs Specified number of claimed epochs.
842:    /// @param chainId Chain Id.
843:    /// @param stakingTarget Staking target corresponding to the chain Id.
844:    /// @param bridgingDecimals Number of supported token decimals able to be transferred across the bridge.
845:    /// @return totalStakingIncentive Total staking incentive across all the claimed epochs.
846:    /// @return totalReturnAmount Total return amount across all the claimed epochs.
847:    /// @return lastClaimedEpoch Last claimed epoch number (not included in claiming).
848:    /// @return nomineeHash Hash of a Nominee(stakingTarget, chainId).	// @audit-issue: There should be single blank line between function declarations.
849:    function calculateStakingIncentives(

946:    }
947:
948:    /// @dev Claims staking incentives for a specific staking target.
949:    /// @param numClaimedEpochs Specified number of claimed epochs.
950:    /// @param chainId Chain Id.
951:    /// @param stakingTarget Staking target corresponding to the chain Id.
952:    /// @param bridgePayload Bridge payload necessary (if required) for a specific bridge relayer.	// @audit-issue: There should be single blank line between function declarations.
953:    function claimStakingIncentives(

1047:    }
1048:
1049:    /// @dev Claims staking incentives for sets staking targets corresponding to a set of chain Ids.
1050:    /// @notice Mind the gas spending depending on the max number of numChains * numTargetsPerChain * numEpochs to claim.
1051:    ///         Also note that in order to avoid duplicates, there is a requirement for a strict ascending order
1052:    ///         of chain Ids and stakingTargets.
1053:    /// @param numClaimedEpochs Specified number of claimed epochs.
1054:    /// @param chainIds Set of chain Ids.
1055:    /// @param stakingTargets Set of staking target addresses corresponding to each chain Id.
1056:    /// @param bridgePayloads Set of bridge payloads (if) necessary for a specific bridge relayer depending on chain Id.
1057:    /// @param valueAmounts Set of value amounts required to provide to some of the bridges.	// @audit-issue: There should be single blank line between function declarations.
1058:    function claimStakingIncentivesBatch(

1126:    }
1127:
1128:    /// @dev Retains staking incentives according to the retainer address to return it back to the staking inflation.	// @audit-issue: There should be single blank line between function declarations.
1129:    function retain() external {

1168:    }
1169:
1170:    /// @dev Syncs the withheld amount according to the data received from L2.
1171:    /// @notice Only a corresponding chain Id deposit processor is able to communicate the withheld amount data.
1172:    /// @param chainId L2 chain Id the withheld amount data is communicated from.
1173:    /// @param amount Withheld OLAS token amount.	// @audit-issue: There should be single blank line between function declarations.
1174:    function syncWithheldAmount(uint256 chainId, uint256 amount) external {

1192:    }
1193:
1194:    /// @dev Syncs the withheld amount manually by the DAO in order to restore the data that was not delivered from L2.
1195:    /// @notice The possible bridge failure scenario that requires to act via the DAO vote includes:
1196:    ///         - Message from L2 to L1 fails: need to call this function.
1197:    /// @param chainId L2 chain Id.
1198:    /// @param amount Withheld amount that was not delivered from L2.	// @audit-issue: There should be single blank line between function declarations.
1199:    function syncWithheldAmountMaintenance(uint256 chainId, uint256 amount) external {

1237:    }
1238:
1239:    /// @dev Sets the pause state.
1240:    /// @param pauseState Pause state.	// @audit-issue: There should be single blank line between function declarations.
1241:    function setPauseState(Pause pauseState) external {
```
[314](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L308-L315), [355](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L349-L356), [407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L400-L408), [440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L432-L441), [505](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L498-L506), [572](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L564-L573), [635](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L632-L636), [654](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L649-L655), [682](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L678-L683), [704](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L698-L705), [731](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L728-L732), [749](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L746-L750), [788](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L780-L789), [848](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L837-L849), [952](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L946-L953), [1057](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1047-L1058), [1128](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1126-L1129), [1173](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1168-L1174), [1198](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1192-L1199), [1240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1237-L1241), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

78:    address public immutable bridge;
79:
80:    /// @dev ArbitrumDepositProcessorL1 constructor.
81:    /// @param _olas OLAS token address.
82:    /// @param _l1Dispenser L1 tokenomics dispenser address.
83:    /// @param _l1TokenRelayer L1 token relayer router bridging contract address (L1ERC20GatewayRouter).
84:    /// @param _l1MessageRelayer L1 message relayer bridging contract address (Inbox).
85:    /// @param _l2TargetChainId L2 target chain Id.
86:    /// @param _l1ERC20Gateway Actual L1 token relayer bridging contract address.
87:    /// @param _outbox L1 Outbox relayer contract address.
88:    /// @param _bridge L1 Bridge repalyer contract address that finalizes the call from L2 to L1	// @audit-issue: There should be single blank line between function declarations.
89:    constructor(

109:    }
110:
111:    /// @inheritdoc DefaultDepositProcessorL1
112:    /// @notice bridgePayload is composed of the following parameters:
113:    ///         - refundAccount: address of a refund account for the excess of funds paid for the message transaction.
114:    ///                          Note if refundAccount is zero address, it is defaulted to the msg.sender;
115:    ///         - gasPriceBid: gas price bid of a sending L1 chain;
116:    ///         - maxSubmissionCostToken: Max gas deducted from user's L2 balance to cover token base submission fee;
117:    ///         - gasLimitMessage: Max gas deducted from user's L2 balance to cover L2 message execution
118:    ///         - maxSubmissionCostMessage: Max gas deducted from user's L2 balance to cover message base submission fee.	// @audit-issue: There should be single blank line between function declarations.
119:    function _sendMessage(

192:    }
193:
194:    /// @dev Process message received from L2.
195:    /// @param data Bytes message data sent from L2.	// @audit-issue: There should be single blank line between function declarations.
196:    function receiveMessage(bytes memory data) external {
```
[88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L78-L89), [118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L109-L119), [195](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L192-L196), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

39:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;
40:
41:    /// @dev OptimismTargetDispenserL2 constructor.
42:    /// @param _olas OLAS token address.
43:    /// @param _proxyFactory Service staking proxy factory address.
44:    /// @param _l2MessageRelayer L2 message relayer bridging contract address (L2CrossDomainMessengerProxy).
45:    /// @param _l1DepositProcessor L1 deposit processor address.
46:    /// @param _l1SourceChainId L1 source chain Id.	// @audit-issue: There should be single blank line between function declarations.
47:    constructor(

53:    ) DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId) {}
54:
55:    /// @inheritdoc DefaultTargetDispenserL2	// @audit-issue: There should be single blank line between function declarations.
56:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {

92:    }
93:
94:    /// @dev Processes a message received from L1 deposit processor contract.
95:    /// @param data Bytes message data sent from L1.	// @audit-issue: There should be single blank line between function declarations.
96:    function receiveMessage(bytes memory data) external payable {
```
[46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L39-L47), [55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L53-L56), [95](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L92-L96), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

8:}
9:	// @audit-issue: There should be at least two blank lines between top level declarations.
10:interface IToken {

51:    uint256 public stakingBatchNonce;
52:
53:    /// @dev DefaultDepositProcessorL1 constructor.
54:    /// @param _olas OLAS token address on L1.
55:    /// @param _l1Dispenser L1 tokenomics dispenser address.
56:    /// @param _l1TokenRelayer L1 token relayer bridging contract address.
57:    /// @param _l1MessageRelayer L1 message relayer bridging contract address.
58:    /// @param _l2TargetChainId L2 target chain Id.	// @audit-issue: There should be single blank line between function declarations.
59:    constructor(

101:    ) internal virtual returns (uint256 sequence);
102:
103:    /// @dev Receives a message on L1 sent from L2 target dispenser side to sync withheld OLAS amount on L2.
104:    /// @param l1Relayer L1 source relayer.
105:    /// @param l2Dispenser L2 target dispenser that originated the message.
106:    /// @param data Message data payload sent from L2.	// @audit-issue: There should be single blank line between function declarations.
107:    function _receiveMessage(address l1Relayer, address l2Dispenser, bytes memory data) internal virtual {

125:    }
126:
127:    /// @dev Sends a single message to the L2 side via a corresponding bridge.
128:    /// @param target Staking target addresses.
129:    /// @param stakingIncentive Corresponding staking incentive.
130:    /// @param bridgePayload Bridge payload necessary (if required) for a specific bridge relayer.
131:    /// @param transferAmount Actual OLAS amount to be transferred.	// @audit-issue: There should be single blank line between function declarations.
132:    function sendMessage(

156:    }
157:
158:
159:    /// @dev Sends a batch message to the L2 side via a corresponding bridge.
160:    /// @param targets Set of staking target addresses.
161:    /// @param stakingIncentives Corresponding set of staking incentives.
162:    /// @param bridgePayload Bridge payload necessary (if required) for a specific bridge relayer.
163:    /// @param transferAmount Actual total OLAS amount across all the targets to be transferred.	// @audit-issue: There should be single blank line between function declarations.
164:    function sendMessageBatch(

182:    }
183:
184:    /// @dev Sets L2 target dispenser address and zero-s the owner.
185:    /// @param l2Dispenser L2 target dispenser address.	// @audit-issue: There should be single blank line between function declarations.
186:    function _setL2TargetDispenser(address l2Dispenser) internal {

202:    }
203:
204:    /// @dev Sets L2 target dispenser address.
205:    /// @param l2Dispenser L2 target dispenser address.	// @audit-issue: There should be single blank line between function declarations.
206:    function setL2TargetDispenser(address l2Dispenser) external virtual {

208:    }
209:
210:    /// @dev Gets the maximum number of token decimals able to be transferred across the bridge.
211:    /// @return Number of supported decimals.	// @audit-issue: There should be single blank line between function declarations.
212:    function getBridgingDecimals() external pure virtual returns (uint256) {
```
[9](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L8-L10), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L51-L59), [106](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L101-L107), [131](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L125-L132), [163](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L156-L164), [185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L182-L186), [205](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L202-L206), [211](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L208-L212), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

25:    address public immutable l1AliasedDepositProcessor;
26:
27:    /// @dev ArbitrumTargetDispenserL2 constructor.
28:    /// @notice _l1AliasedDepositProcessor must be correctly aliased from the address on L1.
29:    ///         Reference: https://docs.arbitrum.io/arbos/l1-to-l2-messaging#address-aliasing
30:    ///         Source: https://github.com/OffchainLabs/token-bridge-contracts/blob/b3894ecc8b6185b2d505c71c9a7851725f53df15/contracts/tokenbridge/libraries/AddressAliasHelper.sol#L21-L32
31:    /// @param _olas OLAS token address.
32:    /// @param _proxyFactory Service staking proxy factory address.
33:    /// @param _l2MessageRelayer L2 message relayer bridging contract address (ArbSys).
34:    /// @param _l1DepositProcessor L1 deposit processor address (NOT aliased).
35:    /// @param _l1SourceChainId L1 source chain Id.	// @audit-issue: There should be single blank line between function declarations.
36:    constructor(

50:    }
51:
52:    /// @inheritdoc DefaultTargetDispenserL2	// @audit-issue: There should be single blank line between function declarations.
53:    function _sendMessage(uint256 amount, bytes memory) internal override {

61:    }
62:
63:    /// @dev Processes a message received from L1 deposit processor contract.
64:    /// @notice msg.sender is an aliased L1 l1DepositProcessor address.
65:    /// @param data Bytes message data sent from L1.	// @audit-issue: There should be single blank line between function declarations.
66:    function receiveMessage(bytes memory data) external payable {
```
[35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L25-L36), [52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L50-L53), [65](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L61-L66), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

63:    address public immutable olasL2;
64:
65:    // https://docs.optimism.io/chain/addresses
66:    // _l1TokenRelayer is L1StandardBridgeProxy
67:    // _l1MessageRelayer is L1CrossDomainMessengerProxy
68:
69:    /// @dev OptimismDepositProcessorL1 constructor.
70:    /// @param _olas OLAS token address on L1.
71:    /// @param _l1Dispenser L1 tokenomics dispenser address.
72:    /// @param _l1TokenRelayer L1 token relayer bridging contract address (L1StandardBridgeProxy).
73:    /// @param _l1MessageRelayer L1 message relayer bridging contract address (L1CrossDomainMessengerProxy).
74:    /// @param _l2TargetChainId L2 target chain Id.
75:    /// @param _olasL2 OLAS token address on L2.	// @audit-issue: There should be single blank line between function declarations.
76:    constructor(

92:    }
93:
94:    /// @inheritdoc DefaultDepositProcessorL1	// @audit-issue: There should be single blank line between function declarations.
95:    function _sendMessage(

144:    }
145:
146:    /// @dev Process message received from L2.
147:    /// @param data Bytes message data sent from L2.	// @audit-issue: There should be single blank line between function declarations.
148:    function receiveMessage(bytes memory data) external payable {
```
[75](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L63-L76), [94](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L92-L95), [147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L144-L148), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

16:}
17:	// @audit-issue: There should be at least two blank lines between top level declarations.
18:interface IStaking {

22:}
23:	// @audit-issue: There should be at least two blank lines between top level declarations.
24:interface IStakingFactory {

63:    uint8 internal _locked;
64:
65:    /// @dev EthereumDepositProcessor constructor.
66:    /// @param _olas OLAS token address.
67:    /// @param _dispenser Tokenomics dispenser address.
68:    /// @param _stakingFactory Service staking proxy factory address.
69:    /// @param _timelock DAO timelock address.	// @audit-issue: There should be single blank line between function declarations.
70:    constructor(address _olas, address _dispenser, address _stakingFactory, address _timelock) {

81:    }
82:
83:    /// @dev Deposits staking incentives for corresponding targets.
84:    /// @param targets Set of staking target addresses.
85:    /// @param stakingIncentives Corresponding set of staking incentives.	// @audit-issue: There should be single blank line between function declarations.
86:    function _deposit(address[] memory targets, uint256[] memory stakingIncentives) internal {

125:    }
126:
127:    /// @dev Deposits a single staking incentive for a corresponding target.
128:    /// @param target Staking target addresses.
129:    /// @param stakingIncentive Corresponding staking incentive.	// @audit-issue: There should be single blank line between function declarations.
130:    function sendMessage(

149:    }
150:
151:
152:    /// @dev Deposits a batch of staking incentives for corresponding targets.
153:    /// @param targets Set of staking target addresses.
154:    /// @param stakingIncentives Corresponding set of staking incentives.	// @audit-issue: There should be single blank line between function declarations.
155:    function sendMessageBatch(

168:    }
169:
170:    /// @dev Gets the maximum number of token decimals able to be transferred across the bridge.
171:    /// @notice This function is implemented for the compatibility purposes only, as no cross-bridge is happening on L1.
172:    /// @return Number of supported decimals.	// @audit-issue: There should be single blank line between function declarations.
173:    function getBridgingDecimals() external pure returns (uint256) {
```
[17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L16-L18), [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L22-L24), [69](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L63-L70), [85](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L81-L86), [129](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L125-L130), [154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L149-L155), [172](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L168-L173), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

54:    mapping(bytes32 => bool) public mapDeliveryHashes;
55:
56:    /// @dev WormholeTargetDispenserL2 constructor.
57:    /// @param _olas OLAS token address.
58:    /// @param _proxyFactory Service staking proxy factory address.
59:    /// @param _l2MessageRelayer L2 message relayer bridging contract address (Relayer).
60:    /// @param _l1DepositProcessor L1 deposit processor address.
61:    /// @param _l1SourceChainId L1 wormhole standard source chain Id.
62:    /// @param _wormholeCore L2 Wormhole Core contract address.
63:    /// @param _l2TokenRelayer L2 token relayer bridging contract address (Token Bridge).	// @audit-issue: There should be single blank line between function declarations.
64:    constructor(

124:    }
125:    
126:    /// @dev Processes a message received from L2 Wormhole Relayer contract.
127:    /// @notice The sender must be the deposit processor address.
128:    /// @param data Bytes message sent from L2 Wormhole Relayer contract.
129:    /// @param receivedTokens Tokens received on L2.
130:    /// @param sourceProcessor The (wormhole format) address on the sending chain which requested this delivery.
131:    /// @param sourceChainId The wormhole chain Id where this delivery was requested.
132:    /// @param deliveryHash The VAA hash of the deliveryVAA.	// @audit-issue: There should be single blank line between function declarations.
133:    function receivePayloadAndTokens(
```
[63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L54-L64), [132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L124-L133), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

34:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 32;
35:
36:    /// @dev GnosisDepositProcessorL1 constructor.
37:    /// @param _olas OLAS token address.
38:    /// @param _l1Dispenser L1 tokenomics dispenser address.
39:    /// @param _l1TokenRelayer L1 token relayer bridging contract address (OmniBridge).
40:    /// @param _l1MessageRelayer L1 message relayer bridging contract address (AMB Proxy Foreign).
41:    /// @param _l2TargetChainId L2 target chain Id.	// @audit-issue: There should be single blank line between function declarations.
42:    constructor(

48:    ) DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId) {}
49:
50:    /// @inheritdoc DefaultDepositProcessorL1	// @audit-issue: There should be single blank line between function declarations.
51:    function _sendMessage(

94:    }
95:
96:    /// @dev Process message received from L2.
97:    /// @param data Bytes message data sent from L2.	// @audit-issue: There should be single blank line between function declarations.
98:    function receiveMessage(bytes memory data) external {
```
[41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L34-L42), [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L48-L51), [97](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L94-L98), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

12:    event FxRootTunnelUpdated(address indexed fxRootTunnel);
13:
14:    /// @dev PolygonTargetDispenserL2 constructor.
15:    /// @param _olas OLAS token address.
16:    /// @param _proxyFactory Service staking proxy factory address.
17:    /// @param _l2MessageRelayer L2 message relayer bridging contract address (fxChild).
18:    /// @param _l1DepositProcessor L1 deposit processor address.
19:    /// @param _l1SourceChainId L1 source chain Id.	// @audit-issue: There should be single blank line between function declarations.
20:    constructor(

29:    {}
30:
31:    /// @inheritdoc DefaultTargetDispenserL2	// @audit-issue: There should be single blank line between function declarations.
32:    function _sendMessage(uint256 amount, bytes memory) internal override {

42:    }
43:
44:    // Source: https://github.com/0xPolygon/fx-portal/blob/731959279a77b0779f8a1eccdaea710e0babee19/contracts/tunnel/FxBaseChildTunnel.sol#L63
45:    // Doc: https://docs.polygon.technology/pos/how-to/bridging/l1-l2-communication/state-transfer/#child-tunnel-contract
46:    /// @dev Processes message received from L1 Root Tunnel.
47:    /// @notice Function needs to be implemented to handle message as per requirement.
48:    ///      This is called by onStateReceive function.
49:    ///      Since it is called via a system call, any event will not be emitted during its execution.
50:    /// @param sender Root message sender.
51:    /// @param data Bytes message that was sent from L1 Root Tunnel.	// @audit-issue: There should be single blank line between function declarations.
52:    function _processMessageFromRoot(uint256, address sender, bytes memory data) internal override {

55:    }
56:
57:    /// @dev Set l1DepositProcessor, aka fxRootTunnel.
58:    /// @param l1Processor L1 deposit processor address.	// @audit-issue: There should be single blank line between function declarations.
59:    function setFxRootTunnel(address l1Processor) external override {
```
[19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L12-L20), [31](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L29-L32), [51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L42-L52), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L55-L59), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

18:    mapping(bytes32 => bool) public mapDeliveryHashes;
19:
20:    /// @dev WormholeDepositProcessorL1 constructor.
21:    /// @param _olas OLAS token address.
22:    /// @param _l1Dispenser L1 tokenomics dispenser address.
23:    /// @param _l1TokenRelayer L1 token relayer bridging contract address (TokenBridge).
24:    /// @param _l1MessageRelayer L1 message relayer bridging contract address (Relayer).
25:    /// @param _l2TargetChainId L2 target chain Id.
26:    /// @param _wormholeCore L1 Wormhole Core contract address.
27:    /// @param _wormholeTargetChainId L2 wormhole standard target chain Id.	// @audit-issue: There should be single blank line between function declarations.
28:    constructor(

56:    }
57:
58:    /// @inheritdoc DefaultDepositProcessorL1	// @audit-issue: There should be single blank line between function declarations.
59:    function _sendMessage(

98:    }
99:
100:    /// @dev Processes a message received from L2 via the L1 Wormhole Relayer contract.
101:    /// @notice The sender must be the L2 target dispenser address.
102:    /// @param data Bytes data message sent from L2.
103:    /// @param sourceAddress The (wormhole format) address on the sending chain which requested this delivery.
104:    /// @param sourceChain The wormhole chain Id where this delivery was requested.
105:    /// @param deliveryHash The VAA hash of the deliveryVAA.	// @audit-issue: There should be single blank line between function declarations.
106:    function receiveWormholeMessages(

128:    }
129:
130:    /// @dev Sets L2 target dispenser address.
131:    /// @param l2Dispenser L2 target dispenser address.	// @audit-issue: There should be single blank line between function declarations.
132:    function setL2TargetDispenser(address l2Dispenser) external override {

135:    }
136:
137:    /// @inheritdoc DefaultDepositProcessorL1	// @audit-issue: There should be single blank line between function declarations.
138:    function getBridgingDecimals() external pure override returns (uint256) {
```
[27](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L18-L28), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L56-L59), [105](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L98-L106), [131](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L128-L132), [137](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L135-L138), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

30:    address public immutable l2TokenRelayer;
31:
32:    /// @dev GnosisTargetDispenserL2 constructor.
33:    /// @param _olas OLAS token address.
34:    /// @param _proxyFactory Service staking proxy factory address.
35:    /// @param _l2MessageRelayer L2 message relayer bridging contract address (AMBHomeProxy).
36:    /// @param _l1DepositProcessor L1 deposit processor address.
37:    /// @param _l1SourceChainId L1 source chain Id.
38:    /// @param _l2TokenRelayer L2 token relayer address (HomeOmniBridgeProxy).	// @audit-issue: There should be single blank line between function declarations.
39:    constructor(

55:    }
56:
57:    /// @inheritdoc DefaultTargetDispenserL2	// @audit-issue: There should be single blank line between function declarations.
58:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {

83:    }
84:
85:    /// @dev Processes a message received from the AMB Contract Proxy (Home) contract.
86:    /// @param data Bytes message data sent from the AMB Contract Proxy (Home) contract.	// @audit-issue: There should be single blank line between function declarations.
87:    function receiveMessage(bytes memory data) external {

93:    }
94:
95:    // Source: https://github.com/omni/omnibridge/blob/c814f686487c50462b132b9691fd77cc2de237d3/contracts/upgradeable_contracts/BasicOmnibridge.sol#L464
96:    // Source: https://github.com/omni/omnibridge/blob/master/contracts/interfaces/IERC20Receiver.sol
97:    /// @dev Processes the data received together with the token transfer from L1.
98:    /// @param data Bytes message data sent from L1.	// @audit-issue: There should be single blank line between function declarations.
99:    function onTokenBridged(address, uint256, bytes calldata data) external {
```
[38](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L30-L39), [57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L55-L58), [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L83-L87), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L93-L99), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

93:    mapping(bytes32 => bool) public stakingQueueingNonces;
94:
95:    /// @dev DefaultTargetDispenserL2 constructor.
96:    /// @param _olas OLAS token address on L2.
97:    /// @param _stakingFactory Service staking proxy factory address.
98:    /// @param _l2MessageRelayer L2 message relayer bridging contract address.
99:    /// @param _l1DepositProcessor L1 deposit processor address.
100:    /// @param _l1SourceChainId L1 source chain Id.	// @audit-issue: There should be single blank line between function declarations.
101:    constructor(

135:    }
136:
137:    /// @dev Processes the data received from L1.
138:    /// @param data Bytes message data sent from L1.	// @audit-issue: There should be single blank line between function declarations.
139:    function _processData(bytes memory data) internal {

218:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal virtual;
219:
220:    /// @dev Receives a message from L1.
221:    /// @param messageRelayer L2 bridge message relayer address.
222:    /// @param sourceProcessor L1 deposit processor address.
223:    /// @param data Bytes message data sent from L1.	// @audit-issue: There should be single blank line between function declarations.
224:    function _receiveMessage(

243:    }
244:
245:    /// @dev Changes the owner address.
246:    /// @param newOwner Address of a new owner.	// @audit-issue: There should be single blank line between function declarations.
247:    function changeOwner(address newOwner) external {

260:    }
261:
262:    /// @dev Redeems queued staking incentive.
263:    /// @param target Staking target address.
264:    /// @param amount Staking incentive amount.
265:    /// @param batchNonce Batch nonce.	// @audit-issue: There should be single blank line between function declarations.
266:    function redeem(address target, uint256 amount, uint256 batchNonce) external {

303:    }
304:
305:    /// @dev Processes the data manually provided by the DAO in order to restore the data that was not delivered from L1.
306:    /// @notice Here are possible bridge failure scenarios and the way to act via the DAO vote:
307:    ///         - Both token and message delivery fails: re-send OLAS to the contract (separate vote), call this function;
308:    ///         - Token transfer succeeds, message fails: call this function;
309:    ///         - Token transfer fails, message succeeds: re-send OLAS to the contract (separate vote).
310:    /// @param data Bytes message data that was not delivered from L1.	// @audit-issue: There should be single blank line between function declarations.
311:    function processDataMaintenance(bytes memory data) external {

319:    }
320:
321:    /// @dev Syncs withheld token amount with L1.
322:    /// @param bridgePayload Payload data for the bridge relayer.	// @audit-issue: There should be single blank line between function declarations.
323:    function syncWithheldTokens(bytes memory bridgePayload) external payable {

350:    }
351:
352:    /// @dev Pause the contract.	// @audit-issue: There should be single blank line between function declarations.
353:    function pause() external {

361:    }
362:
363:    /// @dev Unpause the contract	// @audit-issue: There should be single blank line between function declarations.
364:    function unpause() external {

372:    }
373:
374:    /// @dev Drains contract native funds.
375:    /// @notice For cross-bridge leftovers and incorrectly sent funds.
376:    /// @return amount Drained amount to the owner address.	// @audit-issue: There should be single blank line between function declarations.
377:    function drain() external returns (uint256 amount) {

404:    }
405:
406:    /// @dev Migrates funds to a new specified L2 target dispenser contract address.
407:    /// @notice The contract must be paused to prevent other interactions.
408:    ///         The owner is be zeroed, the contract becomes paused and in the reentrancy state for good.
409:    ///         No further write interaction with the contract is going to be possible.
410:    ///         If the withheld amount is nonzero, it is regulated by the DAO directly on the L1 side.
411:    ///         If there are outstanding queued requests, they are processed by the DAO directly on the L2 side.	// @audit-issue: There should be single blank line between function declarations.
412:    function migrate(address newL2TargetDispenser) external {

455:    }
456:
457:    /// @dev Receives native network token.	// @audit-issue: There should be single blank line between function declarations.
458:    receive() external payable {
```
[100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L93-L101), [138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L135-L139), [223](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L218-L224), [246](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L243-L247), [265](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L260-L266), [310](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L303-L311), [322](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L319-L323), [352](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L350-L353), [363](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L361-L364), [376](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L372-L377), [411](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L404-L412), [457](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L455-L458), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

26:    address public immutable predicate;
27:
28:    /// @dev PolygonDepositProcessorL1 constructor.
29:    /// @param _olas OLAS token address on L1.
30:    /// @param _l1Dispenser L1 tokenomics dispenser address.
31:    /// @param _l1TokenRelayer L1 token relayer bridging contract address (RootChainManagerProxy).
32:    /// @param _l1MessageRelayer L1 message relayer bridging contract address (fxRoot).
33:    /// @param _l2TargetChainId L2 target chain Id.
34:    /// @param _checkpointManager Checkpoint manager contract for verifying L2 to L1 data (RootChainManagerProxy).
35:    /// @param _predicate ERC20 predicate contract to lock tokens on L1 before sending to L2.	// @audit-issue: There should be single blank line between function declarations.
36:    constructor(

54:    }
55:
56:    /// @inheritdoc DefaultDepositProcessorL1	// @audit-issue: There should be single blank line between function declarations.
57:    function _sendMessage(

84:    }
85:
86:    // Source: https://github.com/0xPolygon/fx-portal/blob/731959279a77b0779f8a1eccdaea710e0babee19/contracts/tunnel/FxBaseRootTunnel.sol#L175
87:    // Doc: https://docs.polygon.technology/pos/how-to/bridging/l1-l2-communication/state-transfer/#root-tunnel-contract
88:    /// @dev Process message received from the L2 Child Tunnel. This is called by receiveMessage function.
89:    /// @notice All the bridge relayer and sender verifications are performed in a parent receiveMessage() function.
90:    /// @param data Bytes message data sent from L2.	// @audit-issue: There should be single blank line between function declarations.
91:    function _processMessageFromChild(bytes memory data) internal override {

94:    }
95:
96:    /// @dev Sets l2TargetDispenser, aka fxChildTunnel.
97:    /// @param l2Dispenser L2 target dispenser address.	// @audit-issue: There should be single blank line between function declarations.
98:    function setFxChildTunnel(address l2Dispenser) public override {

113:    }
114:
115:    /// @dev Sets L2 target dispenser address.
116:    /// @param l2Dispenser L2 target dispenser address.	// @audit-issue: There should be single blank line between function declarations.
117:    function setL2TargetDispenser(address l2Dispenser) external override {
```
[35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L26-L36), [56](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L54-L57), [90](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L84-L91), [97](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L94-L98), [116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L113-L117), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

53:    }
54:
55:    /// @dev Safe token transfer implementation.
56:    /// @notice The implementation is fully copied from the audited MIT-licensed solmate code repository:
57:    ///         https://github.com/transmissions11/solmate/blob/v7/src/utils/SafeTransferLib.sol
58:    ///         The original library imports the `ERC20` abstract token contract, and thus embeds all that contract
59:    ///         related code that is not needed. In this version, `ERC20` is swapped with the `address` representation.
60:    ///         Also, the final `require` statement is modified with this contract own `revert` statement.
61:    /// @param token Token address.
62:    /// @param to Address to transfer tokens to.
63:    /// @param amount Token amount.	// @audit-issue: There should be single blank line between function declarations.
64:    function safeTransfer(address token, address to, uint256 amount) internal {
```
[63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L53-L64), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

67:    mapping(address => bool) public mapImplementations;
68:
69:    /// @dev StakingVerifier constructor.
70:    /// @param _olas OLAS token address.
71:    /// @param _rewardsPerSecondLimit Rewards per second limit.
72:    /// @param _timeForEmissionsLimit Time for emissions limit.
73:    /// @param _numServicesLimit Limit for the number of services.	// @audit-issue: There should be single blank line between function declarations.
74:    constructor(address _olas, uint256 _rewardsPerSecondLimit, uint256 _timeForEmissionsLimit,

92:    }
93:
94:    /// @dev Changes the owner address.
95:    /// @param newOwner Address of a new owner.	// @audit-issue: There should be single blank line between function declarations.
96:    function changeOwner(address newOwner) external {

109:    }
110:
111:    /// @dev Controls the necessity of checking implementation whitelisting statuses.
112:    /// @param setCheck True if the whitelisting check is needed, and false otherwise.	// @audit-issue: There should be single blank line between function declarations.
113:    function setImplementationsCheck(bool setCheck) external {

122:    }
123:
124:    /// @dev Controls implementations whitelisting statuses.
125:    /// @notice Implementation is considered whitelisted if the global status is set to true.
126:    /// @notice Implementations check could be set to false even though (some) implementations are set to true.
127:    ///         This is the owner responsibility how to manage the whitelisting logic.
128:    /// @param implementations Set of implementation addresses.
129:    /// @param statuses Set of whitelisting statuses.
130:    /// @param setCheck True if the whitelisting check is needed, and false otherwise.	// @audit-issue: There should be single blank line between function declarations.
131:    function setImplementationsStatuses(

161:    }
162:
163:    /// @dev Verifies a service staking implementation contract.
164:    /// @param implementation Service staking implementation contract address.
165:    /// @return True, if verification is successful.	// @audit-issue: There should be single blank line between function declarations.
166:    function verifyImplementation(address implementation) external view returns (bool){

173:    }
174:
175:    /// @dev Verifies a service staking proxy instance.
176:    /// @param instance Service staking proxy instance.
177:    /// @param implementation Service staking implementation.
178:    /// @return True, if verification is successful.	// @audit-issue: There should be single blank line between function declarations.
179:    function verifyInstance(address instance, address implementation) external view returns (bool) {

223:    }
224:
225:    /// @dev Changes staking parameter limits.
226:    /// @param _rewardsPerSecondLimit Rewards per second limit.
227:    /// @param _timeForEmissionsLimit Time for emissions limit.
228:    /// @param _numServicesLimit Limit for the number of services.	// @audit-issue: There should be single blank line between function declarations.
229:    function changeStakingLimits(

250:    }
251:
252:    /// @dev Gets emissions amount limit for a specific staking proxy instance.
253:    /// @notice The address field is reserved for the proxy instance, if needed in the next verifier version.
254:    /// @return Emissions amount limit.	// @audit-issue: There should be single blank line between function declarations.
255:    function getEmissionsAmountLimit(address) external view returns (uint256) {
```
[73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L67-L74), [95](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L92-L96), [112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L109-L113), [130](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L122-L131), [165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L161-L166), [178](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L173-L179), [228](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L223-L229), [254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L250-L255), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

22:    }
23:
24:    /// @dev Withdraws the reward amount to a service owner.
25:    /// @notice The balance is always greater or equal the amount, as follows from the Base contract logic.
26:    /// @param to Address to.
27:    /// @param amount Amount to withdraw.	// @audit-issue: There should be single blank line between function declarations.
28:    function _withdraw(address to, uint256 amount) internal override {
```
[27](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L22-L28), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

20:    uint256 public immutable livenessRatio;
21:
22:    /// @dev StakingNativeToken initialization.
23:    /// @param _livenessRatio Liveness ratio in the format of 1e18.	// @audit-issue: There should be single blank line between function declarations.
24:    constructor(uint256 _livenessRatio) {

31:    }
32:
33:    /// @dev Gets service multisig nonces.
34:    /// @param multisig Service multisig address.
35:    /// @return nonces Set of a single service multisig nonce.	// @audit-issue: There should be single blank line between function declarations.
36:    function getMultisigNonces(address multisig) external view virtual returns (uint256[] memory nonces) {

39:    }
40:
41:    /// @dev Checks if the service multisig liveness ratio passes the defined liveness threshold.
42:    /// @notice The formula for calculating the ratio is the following:
43:    ///         currentNonce - service multisig nonce at time now (block.timestamp);
44:    ///         lastNonce - service multisig nonce at the previous checkpoint or staking time (tsStart);
45:    ///         ratio = (currentNonce - lastNonce) / (block.timestamp - tsStart).
46:    /// @param curNonces Current service multisig set of a single nonce.
47:    /// @param lastNonces Last service multisig set of a single nonce.
48:    /// @param ts Time difference between current and last timestamps.
49:    /// @return ratioPass True, if the liveness ratio passes the check.	// @audit-issue: There should be single blank line between function declarations.
50:    function isRatioPass(
```
[23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L20-L24), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L31-L36), [49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L39-L50), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

274:    uint256[] public setServiceIds;
275:
276:    /// @dev StakingBase initialization.
277:    /// @param _stakingParams Service staking parameters.	// @audit-issue: There should be single blank line between function declarations.
278:    function _initialize(

361:    }
362:
363:    /// @dev Checks token / ETH staking deposit.
364:    /// @param serviceId Service Id.
365:    /// @param stakingDeposit Staking deposit.	// @audit-issue: There should be single blank line between function declarations.
366:    function _checkTokenStakingDeposit(

390:    function _withdraw(address to, uint256 amount) internal virtual;
391:
392:    /// @dev Checks the ratio pass based on external activity checker implementation.
393:    /// @param multisig Multisig address.
394:    /// @param lastNonces Last checked service multisig nonces.
395:    /// @param ts Time difference between current and last timestamps.
396:    /// @return ratioPass True, if the defined nonce ratio passes the check.
397:    /// @return currentNonces Current multisig nonces.	// @audit-issue: There should be single blank line between function declarations.
398:    function _checkRatioPass(

424:    }
425:
426:    /// @dev Evicts services due to their extended inactivity.
427:    /// @param evictServiceIds Service Ids to be evicted.
428:    /// @param serviceInactivity Corresponding service inactivity records.
429:    /// @param numEvictServices Number of services to evict.	// @audit-issue: There should be single blank line between function declarations.
430:    function _evict(

476:    }
477:
478:    /// @dev Claims rewards for the service.
479:    /// @param serviceId Service Id.
480:    /// @param execCheckPoint Checkpoint execution flag.
481:    /// @return reward Staking reward.	// @audit-issue: There should be single blank line between function declarations.
482:    function _claim(uint256 serviceId, bool execCheckPoint) internal returns (uint256 reward) {

511:    }
512:
513:    /// @dev Calculates staking rewards for all services at current timestamp.
514:    /// @param lastAvailableRewards Available amount of rewards.
515:    /// @param numServices Number of services eligible for the reward that passed the liveness check.
516:    /// @param totalRewards Total calculated rewards.
517:    /// @param eligibleServiceIds Service Ids eligible for rewards.
518:    /// @param eligibleServiceRewards Corresponding rewards for eligible service Ids.
519:    /// @param serviceIds All the staking service Ids.
520:    /// @param serviceNonces Current service nonces.
521:    /// @param serviceInactivity Service inactivity records.	// @audit-issue: There should be single blank line between function declarations.
522:    function _calculateStakingRewards() internal view returns (

583:    }
584:
585:    /// @dev Checkpoint to allocate rewards up until a current time.
586:    /// @return All staking service Ids (including evicted ones within a current epoch).
587:    /// @return All staking updated nonces (including evicted ones within a current epoch).
588:    /// @return Set of reward-eligible service Ids.
589:    /// @return Corresponding set of reward-eligible service rewards.
590:    /// @return evictServiceIds Evicted service Ids.	// @audit-issue: There should be single blank line between function declarations.
591:    function checkpoint() public returns (

713:    }
714:
715:    /// @dev Stakes the service.
716:    /// @notice Each service must be staked for a minimum of maxInactivityDuration time, or until the funds are not zero.
717:    ///         maxInactivityDuration = maxNumInactivityPeriods * livenessPeriod
718:    /// @param serviceId Service Id.	// @audit-issue: There should be single blank line between function declarations.
719:    function stake(uint256 serviceId) external {

800:    }
801:
802:    /// @dev Unstakes the service.
803:    /// @param serviceId Service Id.
804:    /// @return reward Staking reward.	// @audit-issue: There should be single blank line between function declarations.
805:    function unstake(uint256 serviceId) external returns (uint256 reward) {

872:    }
873:
874:    /// @dev Claims rewards for the service without an additional checkpoint call.
875:    /// @param serviceId Service Id.
876:    /// @return Staking reward.	// @audit-issue: There should be single blank line between function declarations.
877:    function claim(uint256 serviceId) external returns (uint256) {

879:    }
880:
881:    /// @dev Checkpoints and claims rewards for the service.
882:    /// @param serviceId Service Id.
883:    /// @return Staking reward.	// @audit-issue: There should be single blank line between function declarations.
884:    function checkpointAndClaim(uint256 serviceId) external returns (uint256) {

886:    }
887:
888:    /// @dev Calculates service staking reward during the last checkpoint period.
889:    /// @notice Call this function if `calculateStakingLastReward()` returns a nonzero value in order to checkpoint
890:    ///         and get the full reward.
891:    /// @param serviceId Service Id.
892:    /// @return reward Service reward.	// @audit-issue: There should be single blank line between function declarations.
893:    function calculateStakingLastReward(uint256 serviceId) public view returns (uint256 reward) {

911:    }
912:
913:    /// @dev Calculates overall service staking reward at current timestamp.
914:    /// @param serviceId Service Id.
915:    /// @return reward Service reward.	// @audit-issue: There should be single blank line between function declarations.
916:    function calculateStakingReward(uint256 serviceId) external view returns (uint256 reward) {

923:    }
924:
925:    /// @dev Gets the service staking state.
926:    /// @param serviceId.
927:    /// @return stakingState Staking state of the service.	// @audit-issue: There should be single blank line between function declarations.
928:    function getStakingState(uint256 serviceId) external view returns (StakingState stakingState) {

935:    }
936:
937:    /// @dev Gets the next reward checkpoint timestamp.
938:    /// @return tsNext Next reward checkpoint timestamp.	// @audit-issue: There should be single blank line between function declarations.
939:    function getNextRewardCheckpointTimestamp() external view returns (uint256 tsNext) {

942:    }
943:
944:    /// @dev Gets staked service info.
945:    /// @param serviceId Service Id.
946:    /// @return sInfo Struct object with the corresponding service info.	// @audit-issue: There should be single blank line between function declarations.
947:    function getServiceInfo(uint256 serviceId) external view returns (ServiceInfo memory sInfo) {

949:    }
950:
951:    /// @dev Gets staked service Ids.
952:    /// @return Staked service Ids.	// @audit-issue: There should be single blank line between function declarations.
953:    function getServiceIds() public view returns (uint256[] memory) {

955:    }
956:
957:    /// @dev Gets canonical agent Ids from the service configuration.
958:    /// @return Agent Ids.	// @audit-issue: There should be single blank line between function declarations.
959:    function getAgentIds() external view returns (uint256[] memory) {
```
[277](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L274-L278), [365](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L361-L366), [397](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L390-L398), [429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L424-L430), [481](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L476-L482), [521](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L511-L522), [590](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L583-L591), [718](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L713-L719), [804](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L800-L805), [876](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L872-L877), [883](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L879-L884), [892](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L886-L893), [915](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L911-L916), [927](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L923-L928), [938](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L935-L939), [946](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L942-L947), [952](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L949-L953), [958](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L955-L959), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

48:    address public stakingToken;
49:
50:    /// @dev StakingToken initialization.
51:    /// @param _stakingParams Service staking parameters.
52:    /// @param _serviceRegistryTokenUtility ServiceRegistryTokenUtility contract address.
53:    /// @param _stakingToken Address of a service staking token.	// @audit-issue: There should be single blank line between function declarations.
54:    function initialize(

69:    }
70:
71:    /// @dev Checks token staking deposit.
72:    /// @param serviceId Service Id.
73:    /// @param serviceAgentIds Service agent Ids.	// @audit-issue: There should be single blank line between function declarations.
74:    function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {

98:    }
99:
100:    /// @dev Withdraws the reward amount to a service owner.
101:    /// @notice The balance is always greater or equal the amount, as follows from the Base contract logic.
102:    /// @param to Address to.
103:    /// @param amount Amount to withdraw.	// @audit-issue: There should be single blank line between function declarations.
104:    function _withdraw(address to, uint256 amount) internal override {

111:    }
112:
113:    /// @dev Deposits funds for staking.
114:    /// @param amount Token amount to deposit.	// @audit-issue: There should be single blank line between function declarations.
115:    function deposit(uint256 amount) external {
```
[53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L48-L54), [73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L69-L74), [103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L98-L104), [114](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L111-L115), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

23:    bytes32 public constant SERVICE_STAKING_PROXY = 0x9e5e169c1098011e4e5940a3ec1797686b2a8294a9b77a4c676b121bdc0ebb5e;
24:
25:    /// @dev StakingProxy constructor.
26:    /// @param implementation Service staking implementation address.	// @audit-issue: There should be single blank line between function declarations.
27:    constructor(address implementation) {

37:    }
38:
39:    /// @dev Delegatecall to all the incoming data.	// @audit-issue: There should be single blank line between function declarations.
40:    fallback() external payable {

51:    }
52:
53:    /// @dev Gets the implementation address.	// @audit-issue: There should be single blank line between function declarations.
54:    function getImplementation() external view returns (address implementation) {
```
[26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L23-L27), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L37-L40), [53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L51-L54), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

99:    mapping(address => InstanceParams) public mapInstanceParams;
100:
101:    /// @dev StakingFactory constructor.
102:    /// @param _verifier Verifier contract address (can be zero).	// @audit-issue: There should be single blank line between function declarations.
103:    constructor(address _verifier) {

106:    }
107:
108:    /// @dev Changes the owner address.
109:    /// @param newOwner Address of a new owner.	// @audit-issue: There should be single blank line between function declarations.
110:    function changeOwner(address newOwner) external {

123:    }
124:
125:    /// @dev Changes the verifier address.
126:    /// @param newVerifier Address of a new verifier.	// @audit-issue: There should be single blank line between function declarations.
127:    function changeVerifier(address newVerifier) external {

135:    }
136:
137:    /// @dev Calculates a new proxy address based on the deployment data and provided nonce.
138:    /// @notice New address = first 20 bytes of keccak256(0xff + address(this) + s + keccak256(deploymentData)).
139:    /// @param implementation Implementation contract address.
140:    /// @param localNonce Nonce.	// @audit-issue: There should be single blank line between function declarations.
141:    function getProxyAddressWithNonce(address implementation, uint256 localNonce) public view returns (address) {

157:    }
158:
159:    /// @dev Calculates a new proxy address based on the deployment data and utilizing a current contract nonce.
160:    /// @param implementation Implementation contract address.	// @audit-issue: There should be single blank line between function declarations.
161:    function getProxyAddress(address implementation) external view returns (address) {

163:    }
164:
165:    /// @dev Creates a service staking contract instance.
166:    /// @param implementation Service staking blanc implementation address.
167:    /// @param initPayload Initialization payload.	// @audit-issue: There should be single blank line between function declarations.
168:    function createStakingInstance(

244:    }
245:
246:    /// @dev Sets the instance status flag.
247:    /// @param instance Proxy instance address.
248:    /// @param isEnabled Activity flag.	// @audit-issue: There should be single blank line between function declarations.
249:    function setInstanceStatus(address instance, bool isEnabled) external {

262:    }
263:    
264:    /// @dev Verifies a service staking contract instance.
265:    /// @param instance Service staking proxy instance.
266:    /// @return True, if verification is successful.	// @audit-issue: There should be single blank line between function declarations.
267:    function verifyInstance(address instance) public view returns (bool) {

289:    }
290:
291:    /// @dev Verifies staking proxy instance and gets emissions amount.
292:    /// @param instance Staking proxy instance.
293:    /// @return amount Emissions amount.	// @audit-issue: There should be single blank line between function declarations.
294:    function verifyInstanceAndGetEmissionsAmount(address instance) external view returns (uint256 amount) {
```
[102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L99-L103), [109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L106-L110), [126](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L123-L127), [140](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L135-L141), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L157-L161), [167](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L163-L168), [248](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L244-L249), [266](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L262-L267), [293](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L289-L294), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

201:    uint256 public timeSum;
202:
203:    /// @dev Contract constructor.
204:    /// @param _ve Voting Escrow contract address.	// @audit-issue: There should be single blank line between function declarations.
205:    constructor(address _ve) {

219:    }
220:
221:    /// @dev Fill sum of nominee weights for the same type week-over-week for missed checkins and return the sum for the future week.
222:    /// @return Sum of nominee weights.	// @audit-issue: There should be single blank line between function declarations.
223:    function _getSum() internal returns (uint256) {

248:    }
249:
250:    /// @dev Fill historic nominee weights week-over-week for missed checkins and return the total for the future week.
251:    /// @param account Nominee account address in bytes32 form.
252:    /// @param chainId Nominee chain Id.
253:    /// @return Nominee weight.	// @audit-issue: There should be single blank line between function declarations.
254:    function _getWeight(bytes32 account, uint256 chainId) internal returns (uint256) {

288:    }
289:
290:    /// @dev Add nominee address along with the chain Id.
291:    /// @param nominee Nominee account address and chainId.	// @audit-issue: There should be single blank line between function declarations.
292:    function _addNominee(Nominee memory nominee) internal {

319:    }
320:
321:    /// @dev Add EVM nominee address along with the chain Id.
322:    /// @param account Address of the nominee.
323:    /// @param chainId Chain Id.	// @audit-issue: There should be single blank line between function declarations.
324:    function addNomineeEVM(address account, uint256 chainId) external {

344:    }
345:
346:    /// @dev Add Non-EVM nominee address along with the chain Id.
347:    /// @param account Address of the nominee in byte32 standard.
348:    /// @param chainId Chain Id.	// @audit-issue: There should be single blank line between function declarations.
349:    function addNomineeNonEVM(bytes32 account, uint256 chainId) external {

364:    }
365:
366:    /// @dev Changes the owner address.
367:    /// @param newOwner Address of a new owner.	// @audit-issue: There should be single blank line between function declarations.
368:    function changeOwner(address newOwner) external {

381:    }
382:
383:    /// @dev Changes the dispenser contract address.
384:    /// @notice Dispenser can be set to a zero address if the contract needs to serve a general purpose.
385:    /// @param newDispenser New dispenser contract address.	// @audit-issue: There should be single blank line between function declarations.
386:    function changeDispenser(address newDispenser) external {

394:    }
395:
396:    /// @dev Checkpoints to fill data common for all nominees.	// @audit-issue: There should be single blank line between function declarations.
397:    function checkpoint() external {

401:    }
402:
403:    /// @dev Checkpoints to fill data for both a specific nominee and common for all nominees.
404:    /// @param account Address of the nominee.
405:    /// @param chainId Chain Id.	// @audit-issue: There should be single blank line between function declarations.
406:    function checkpointNominee(bytes32 account, uint256 chainId) external {

411:    }
412:
413:    /// @dev Gets Nominee relative weight (not more than 1.0) normalized to 1e18 (e.g. 1.0 == 1e18) and a sum of weights.
414:    /// @param account Address of the nominee in byte32 standard.
415:    /// @param chainId Chain Id.
416:    /// @param time Timestamp in the past or present.
417:    /// @return weight Value of relative weight normalized to 1e18.
418:    /// @return totalSum Sum of nominee weights.	// @audit-issue: There should be single blank line between function declarations.
419:    function _nomineeRelativeWeight(

434:    }
435:
436:    /// @dev Gets Nominee relative weight (not more than 1.0) normalized to 1e18 (e.g. 1.0 == 1e18) and a sum of weights.
437:    /// @param account Address of the nominee in bytes32 form.
438:    /// @param chainId Chain Id.
439:    /// @param time Relative weight at the specified timestamp in the past or present.
440:    /// @return relativeWeight Value of nominee relative weight normalized to 1e18.
441:    /// @return totalSum Sum of nominee weights.	// @audit-issue: There should be single blank line between function declarations.
442:    function nomineeRelativeWeight(

448:    }
449:
450:    /// @dev Checkpoints and gets nominee weight normalized to 1e18, and the total sum of all the nominee weights.
451:    /// @notice Nothing is recorded if the values are already filled.
452:    /// @param account Address of the nominee in bytes32 form.
453:    /// @param chainId Chain Id.
454:    /// @param time Relative weight at the specified timestamp in the past or present.
455:    /// @return relativeWeight Value of nominee relative weight normalized to 1e18.
456:    /// @return totalSum Sum of nominee weights.	// @audit-issue: There should be single blank line between function declarations.
457:    function nomineeRelativeWeightWrite(

467:    }
468:
469:    /// @dev Allocates voting power for changing pool weights.
470:    /// @param account Address of the nominee the `msg.sender` votes for in bytes32 form.
471:    /// @param chainId Chain Id.
472:    /// @param weight Weight for a nominee in bps (units of 0.01%). Minimal is 0.01%. Ignored if 0.	// @audit-issue: There should be single blank line between function declarations.
473:    function voteForNomineeWeights(bytes32 account, uint256 chainId, uint256 weight) public {

557:    }
558:
559:    /// @dev Allocates voting power for changing pool weights in a batch set.
560:    /// @param accounts Set of nominee addresses in bytes32 form the `msg.sender` votes for.
561:    /// @param chainIds Set of corresponding chain Ids.
562:    /// @param weights Weights for a nominees in bps (units of 0.01%). Minimal is 0.01%. Ignored if 0.	// @audit-issue: There should be single blank line between function declarations.
563:    function voteForNomineeWeightsBatch(

580:    }
581:
582:    /// @dev Removes nominee from the contract and zeros its weight.
583:    /// @notice The last nominee in the set of nominees is going to change its Id at the end of this function call.
584:    /// @param account Address of the nominee in bytes32 form.
585:    /// @param chainId Chain Id.	// @audit-issue: There should be single blank line between function declarations.
586:    function removeNominee(bytes32 account, uint256 chainId) external {

636:    }
637:
638:    /// @dev Revokes user voting power from a removed nominee.
639:    /// @param account Address of the removed nominee in bytes32 form.
640:    /// @param chainId Chain Id.	// @audit-issue: There should be single blank line between function declarations.
641:    function revokeRemovedNomineeVotingPower(bytes32 account, uint256 chainId) external {

668:    }
669:
670:    /// @dev Get current nominee weight.
671:    /// @param account Address of the nominee in bytes32 form.
672:    /// @param chainId Chain Id.
673:    /// @return Nominee weight.	// @audit-issue: There should be single blank line between function declarations.
674:    function getNomineeWeight(bytes32 account, uint256 chainId) external view returns (uint256) {

680:    }
681:    
682:    /// @dev Get sum of nominee weights.
683:    /// @return Sum of nominee weights.	// @audit-issue: There should be single blank line between function declarations.
684:    function getWeightsSum() external view returns (uint256) {

686:    }
687:
688:    /// @dev Get the total number of nominees.
689:    /// @notice The zero-th default nominee Id with id == 0 does not count.
690:    /// @return Total number of nominees.	// @audit-issue: There should be single blank line between function declarations.
691:    function getNumNominees() external view returns (uint256) {

693:    }
694:
695:    /// @dev Get the total number of removed nominees.
696:    /// @notice The zero-th default nominee Id with id == 0 does not count.
697:    /// @return Total number of removed nominees.	// @audit-issue: There should be single blank line between function declarations.
698:    function getNumRemovedNominees() external view returns (uint256) {

700:    }
701:
702:    /// @dev Gets a full set of nominees.
703:    /// @notice The returned set includes the zero-th empty nominee instance.
704:    /// @return Set of all the nominees in the contract.	// @audit-issue: There should be single blank line between function declarations.
705:    function getAllNominees() external view returns (Nominee[] memory) {

707:    }
708:
709:    /// @dev Gets a full set of removed nominees.
710:    /// @notice The returned set includes the zero-th empty nominee instance.
711:    /// @return Set of all the removed nominees in the contract.	// @audit-issue: There should be single blank line between function declarations.
712:    function getAllRemovedNominees() external view returns (Nominee[] memory) {

714:    }
715:
716:    /// @dev Gets the nominee Id in the global nominees set.
717:    /// @param account Nominee address in bytes32 form.
718:    /// @param chainId Chain Id.
719:    /// @return Nominee Id in the global set of Nominee struct values.	// @audit-issue: There should be single blank line between function declarations.
720:    function getNomineeId(bytes32 account, uint256 chainId) external view returns (uint256) {

726:    }
727:
728:    /// @dev Gets the removed nominee Id in the global removed nominees set.
729:    /// @param account Nominee address in bytes32 form.
730:    /// @param chainId Chain Id.
731:    /// @return Removed nominee Id in the global set of Nominee struct values.	// @audit-issue: There should be single blank line between function declarations.
732:    function getRemovedNomineeId(bytes32 account, uint256 chainId) external view returns (uint256) {

738:    }
739:
740:    /// @dev Gets the nominee address and its corresponding chain Id.
741:    /// @notice The zero-th default nominee Id with id == 0 does not count.
742:    /// @param id Nominee Id in the global set of Nominee struct values.
743:    /// @return Nominee address in bytes32 form and chain Id.	// @audit-issue: There should be single blank line between function declarations.
744:    function getNominee(uint256 id) external view returns (Nominee memory) {

755:    }
756:
757:    /// @dev Gets the removed nominee address and its corresponding chain Id.
758:    /// @notice The zero-th default removed nominee Id with id == 0 does not count.
759:    /// @param id Removed nominee Id in the global set of Nominee struct values.
760:    /// @return Removed nominee address in bytes32 form and chain Id.	// @audit-issue: There should be single blank line between function declarations.
761:    function getRemovedNominee(uint256 id) external view returns (Nominee memory) {

772:    }
773:
774:    /// @dev Gets next allowed voting time for selected nominees and voters.
775:    /// @notice The function does not check for repeated nominees and voters.
776:    /// @param accounts Set of nominee account addresses.
777:    /// @param chainIds Corresponding set of chain Ids.
778:    /// @param voters Corresponding set of voters for specified nominees.	// @audit-issue: There should be single blank line between function declarations.
779:    function getNextAllowedVotingTimes(
```
[204](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L201-L205), [222](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L219-L223), [253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L248-L254), [291](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L288-L292), [323](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L319-L324), [348](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L344-L349), [367](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L364-L368), [385](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L381-L386), [396](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L394-L397), [405](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L401-L406), [418](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L411-L419), [441](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L434-L442), [456](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L448-L457), [472](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L467-L473), [562](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L557-L563), [585](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L580-L586), [640](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L636-L641), [673](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L668-L674), [683](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L680-L684), [690](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L686-L691), [697](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L693-L698), [704](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L700-L705), [711](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L707-L712), [719](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L714-L720), [731](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L726-L732), [743](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L738-L744), [760](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L755-L761), [778](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L772-L779), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/latest/style-guide.html#blank-lines).

### `constants` should be defined rather than using magic numbers
Even [assembly](https://github.com/code-423n4/2022-05-opensea-seaport/blob/9d7ce4d08bf3c3010304a0476a785c70c0e90ae7/contracts/lib/TokenTransferrer.sol#L35-L39) can benefit from using readable constants instead of hex/numeric literals

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

670:        if (_epsilonRate > 0 && _epsilonRate <= 17e18) {	// @audit-issue

691:        tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x01;	// @audit-issue

717:        if (_rewardComponentFraction + _rewardAgentFraction > 100) {	// @audit-issue

724:        if (sumTopUpFractions > 100) {	// @audit-issue

735:        tp.epochPoint.rewardTreasuryFraction = uint8(100 - _rewardComponentFraction - _rewardAgentFraction);	// @audit-issue

743:        tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x02;	// @audit-issue

778:        tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x08;	// @audit-issue

856:            totalIncentives = mapUnitIncentives[unitType][unitId].reward + totalIncentives / 100;	// @audit-issue

909:            if (incentiveFlags[2] || incentiveFlags[3]) {	// @audit-issue

926:                if (incentiveFlags[unitType] || incentiveFlags[unitType + 2]) {	// @audit-issue

952:                        if (topUpEligible && incentiveFlags[unitType + 2]) {	// @audit-issue

916:            for (uint256 unitType = 0; unitType < 2; ++unitType) {	// @audit-issue

1061:        idf = 1e18 + fKD;	// @audit-issue

1116:        incentives[1] = (incentives[0] * tp.epochPoint.rewardTreasuryFraction) / 100;	// @audit-issue

1118:        incentives[2] = (incentives[0] * tp.unitPoints[0].rewardUnitFraction) / 100;	// @audit-issue

1119:        incentives[3] = (incentives[0] * tp.unitPoints[1].rewardUnitFraction) / 100;	// @audit-issue

1143:            tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x04;	// @audit-issue

1152:        incentives[4] = (inflationPerEpoch * tp.epochPoint.maxBondFraction) / 100;	// @audit-issue

1164:        if (incentives[4] > curMaxBond) {	// @audit-issue

1166:            incentives[4] = effectiveBond + incentives[4] - curMaxBond;	// @audit-issue

1174:        if (tokenomicsParametersUpdated & 0x01 == 0x01) {	// @audit-issue

1194:        if (tokenomicsParametersUpdated & 0x02 == 0x02) {	// @audit-issue

1199:            for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1211:        if (tokenomicsParametersUpdated & 0x08 == 0x08) {	// @audit-issue

1225:        incentives[5] = (inflationPerEpoch * tp.unitPoints[0].topUpUnitFraction) / 100;	// @audit-issue

1227:        incentives[6] = (inflationPerEpoch * tp.unitPoints[1].topUpUnitFraction) / 100;	// @audit-issue

1237:        incentives[8] = incentives[7] + (inflationPerEpoch * mapEpochStakingPoints[eCounter].stakingFraction) / 100;	// @audit-issue

1256:            curMaxBond = (inflationPerEpoch * nextEpochPoint.epochPoint.maxBondFraction) / 100;	// @audit-issue

1263:            curMaxBond = (curEpochLen * curInflationPerSecond * nextEpochPoint.epochPoint.maxBondFraction) / 100;	// @audit-issue

1329:        for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1401:        for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1440:                    reward += totalIncentives / 100;	// @audit-issue
```
[670](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L670-L670), [691](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L691-L691), [717](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L717-L717), [724](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L724-L724), [735](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L735-L735), [743](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L743-L743), [778](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L778-L778), [856](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L856-L856), [909](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L909-L909), [926](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L926-L926), [952](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L952-L952), [916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L916-L916), [1061](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1061-L1061), [1116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1116-L1116), [1118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1118-L1118), [1119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1119-L1119), [1143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1143-L1143), [1152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1152-L1152), [1164](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1164-L1164), [1166](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1166-L1166), [1174](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1174-L1174), [1194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1194-L1194), [1199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1199-L1199), [1211](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1211-L1211), [1225](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1225-L1225), [1227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1227-L1227), [1237](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1237-L1237), [1256](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1256-L1256), [1263](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1263-L1263), [1329](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1329-L1329), [1401](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1401-L1401), [1440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1440-L1440), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

35:        if (numYears < 10) {	// @audit-issue

51:            numYears -= 9;	// @audit-issue

59:                supplyCap += (supplyCap * maxMintCapFraction) / 100;	// @audit-issue

71:        if (numYears < 10) {	// @audit-issue

88:            numYears -= 9;	// @audit-issue

96:                supplyCap += (supplyCap * maxMintCapFraction) / 100;	// @audit-issue

100:            inflationAmount = (supplyCap * maxMintCapFraction) / 100;	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L35-L35), [51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L51-L51), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L59-L59), [71](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L71-L71), [88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L88-L88), [96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L96-L96), [100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L100-L100), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

611:                totalAmounts[2] += returnAmount;	// @audit-issue

741:            ITreasury(treasury).paused() == 2) {	// @audit-issue

802:            ITreasury(treasury).paused() == 2) {	// @audit-issue

911:            if (stakingWeight < uint256(stakingPoint.minStakingWeight) * 1e14) {	// @audit-issue

913:                returnAmount = ((stakingDiff + availableStakingAmount) * stakingWeight) / 1e18;	// @audit-issue

916:                stakingIncentive = (availableStakingAmount * stakingWeight) / 1e18;	// @audit-issue

918:                returnAmount = (stakingDiff * stakingWeight) / 1e18;	// @audit-issue

931:                if (bridgingDecimals < 18) {	// @audit-issue

933:                    normalizedStakingAmount *= 10 ** (18 - bridgingDecimals);	// @audit-issue

984:            ITreasury(treasury).paused() == 2) {	// @audit-issue

1081:            ITreasury(treasury).paused() == 2) {	// @audit-issue

1098:        if (totalAmounts[2] > 0) {	// @audit-issue

1159:        totalReturnAmount /= 1e18;	// @audit-issue

1220:        if (bridgingDecimals < 18) {	// @audit-issue

1222:            normalizedAmount *= 10 ** (18 - bridgingDecimals);	// @audit-issue
```
[611](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L611-L611), [741](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L741-L741), [802](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L802-L802), [911](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L911-L911), [913](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L913-L913), [916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L916-L916), [918](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L918-L918), [931](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L931-L931), [933](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L933-L933), [984](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L984-L984), [1081](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1081-L1081), [1098](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1098-L1098), [1159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1159-L1159), [1220](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1220-L1220), [1222](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1222-L1222), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

141:        if (gasPriceBid < 2 || gasLimitMessage < 2 || maxSubmissionCostMessage == 0) {	// @audit-issue
```
[141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L141-L141), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

165:            if (success && returnData.length == 32) {	// @audit-issue

274:        if (paused == 2) {	// @audit-issue

331:        if (paused == 2) {	// @audit-issue
```
[165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L165-L165), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L274-L274), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L331-L331), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

212:            if (returnData.length == 32) {	// @audit-issue
```
[212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L212-L212), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

302:        if (_stakingParams.minStakingDeposit < 2) {	// @audit-issue

411:        if (success && returnData.length > 63 && (returnData.length % 32 == 0)) {	// @audit-issue

420:            if (success && returnData.length == 32) {	// @audit-issue
```
[302](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L302-L302), [411](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L411-L411), [420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L420-L420), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

432:            weight = 1e18 * nomineeWeight / totalSum;	// @audit-issue
```
[432](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L432-L432), 


#### Recommendation

Consider defining constants with meaningful names for magic numbers and hexadecimal literals to improve code readability and maintainability.

### Constant redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

39:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue seen in: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol
```
[39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L39-L39), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

62:    bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));	// @audit-issue seen in: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

64:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue seen in: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L62-L62), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L64-L64), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

61:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue seen in: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol
```
[61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L61-L61), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

52:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue seen in: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol
```
[52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L52-L52), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

34:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 32;	// @audit-issue seen in: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol
```
[34](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L34-L34), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

13:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue seen in: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol
```
[13](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L13-L13), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

28:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 32;	// @audit-issue seen in: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L28-L28), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

224:    string public constant VERSION = "0.2.0";	// @audit-issue seen in: ./tokenomics/contracts/TokenomicsConstants.sol
```
[224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L224-L224), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

159:    uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue seen in: ./tokenomics/contracts/Dispenser.sol
```
[159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L159-L159), 


#### Recommendation

To avoid constant values being redefined in multiple locations, consider defining constants in a single contract, such as a library, using internal constants. This approach ensures that values remain consistent and reduces the risk of synchronization issues. If a variable serves as a local cache of another contract's value, make the cache variable internal or private, requiring external users to query the contract with the source of truth to prevent synchronization problems.

### Immutable redefined elsewhere
Consider defining in only one contract so that values cannot become out of sync when only one location is updated.

```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

37:    address public immutable olas;	// @audit-issue seen in: ./tokenomics/contracts/Dispenser.sol
```
[37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L37-L37), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

72:    address public immutable olas;	// @audit-issue seen in: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol
```
[72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L72-L72), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

55:    address public immutable olas;	// @audit-issue seen in: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

59:    address public immutable stakingFactory;	// @audit-issue seen in: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol
```
[55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L55-L55), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L59-L59), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

51:    address public immutable olas;	// @audit-issue seen in: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol
```
[51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L51-L51), 


#### Recommendation

To avoid immutable values being redefined in multiple locations, consider defining immutables in a single contract.

### Lack of index element validation in function
There's no validation to check whether the index element provided as an argument actually exists in the call. This omission could lead to unintended behavior if an element that does not exist in the call is passed to the function. The function should validate that the provided index element exists in the call before proceeding.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

928:                    uint96 amount = uint96(amounts[i] / numServiceUnits);	// @audit-issue

1013:                revert ServiceDoesNotExist(serviceIds[i]);	// @audit-issue

1374:            mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp = 0;	// @audit-issue

1458:            topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;	// @audit-issue
```
[928](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L928-L928), [1013](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1013-L1013), [1374](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1374-L1374), [1458](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1458-L1458), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

495:                    updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);	// @audit-issue

476:                    updatedStakingAmounts[numPos] = stakingIncentives[i][j];	// @audit-issue

550:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

556:                lastTarget = stakingTargets[i][j];	// @audit-issue

625:                    mapChainIdWithheldAmounts[chainIds[i]] = withheldAmount;	// @audit-issue

603:                    calculateStakingIncentives(numClaimedEpochs, chainIds[i], stakingTargets[i][j], bridgingDecimals);	// @audit-issue

724:            mapChainIdDepositProcessors[chainIds[i]] = depositProcessors[i];	// @audit-issue
```
[495](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L495-L495), [476](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L476-L476), [550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L550-L550), [556](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L556-L556), [625](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L625-L625), [603](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L603-L603), [724](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L724-L724), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

96:            uint256 amount = stakingIncentives[i];	// @audit-issue
```
[96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L96-L96), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

160:            revert WrongTokenAddress(receivedTokens[0].tokenAddress, olas);	// @audit-issue
```
[160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L160-L160), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

157:            mapImplementations[implementations[i]] = statuses[i];	// @audit-issue
```
[157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L157-L157), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

457:                inactivity[sCounter] = serviceInactivity[i];	// @audit-issue

470:            setServiceIds[idx] = setServiceIds[totalNumServices];	// @audit-issue

550:                serviceIds[i] = setServiceIds[i];	// @audit-issue

776:                    revert WrongAgentId(agentIds[i]);	// @audit-issue

858:            setServiceIds[idx] = setServiceIds[setServiceIds.length - 1];	// @audit-issue
```
[457](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L457-L457), [470](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L470-L470), [550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L550-L550), [776](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L776-L776), [858](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L858-L858), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

93:            uint256 bond = IServiceTokenUtility(serviceRegistryTokenUtility).getAgentBond(serviceId, serviceAgentIds[i]);	// @audit-issue
```
[93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L93-L93), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

574:            voteForNomineeWeights(accounts[i], chainIds[i], weights[i]);	// @audit-issue

622:        nominee = setNominees[setNominees.length - 1];	// @audit-issue

804:            nextAllowedVotingTimes[i] = lastUserVote[voters[i]][nomineeHash] + WEIGHT_VOTE_DELAY;	// @audit-issue
```
[574](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L574-L574), [622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L622-L622), [804](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L804-L804), 


#### Recommendation

Integrate explicit index validation checks at the beginning of functions that operate based on index elements. Use conditional statements to verify that the provided index falls within the valid range of existing elements. For array operations, ensure the index is less than the array's length. For mappings, consider additional logic to confirm the presence of a key. For example, in an array-based function:
```solidity
function getElementByIndex(uint256 index) public view returns (ElementType) {
    require(index < array.length, "Index out of bounds");
    return array[index];
}
```


### Contract should expose an `interface`
All `external`/`public` functions should extend an `interface`. This is useful to make sure that the whole API is extracted.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

254:contract Tokenomics is TokenomicsConstants {	// @audit-issue
```
[254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L254-L254), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

253:contract Dispenser {	// @audit-issue
```
[253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L253-L253), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

70:contract ArbitrumDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L70-L70), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

37:contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L37-L37), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

23:contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L23-L23), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

59:contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

50:contract EthereumDepositProcessor {	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

50:contract WormholeTargetDispenserL2 is DefaultTargetDispenserL2, TokenReceiver {	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

32:contract GnosisDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L32-L32), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

11:contract PolygonTargetDispenserL2 is DefaultTargetDispenserL2, FxBaseChildTunnel {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

11:contract WormholeDepositProcessorL1 is DefaultDepositProcessorL1, TokenSender {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

26:contract GnosisTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L26-L26), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

22:contract PolygonDepositProcessorL1 is DefaultDepositProcessorL1, FxBaseRootTunnel {	// @audit-issue
```
[22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

43:contract StakingVerifier {	// @audit-issue
```
[43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L43-L43), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

17:contract StakingNativeToken is StakingBase {	// @audit-issue
```
[17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L17-L17), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

18:contract StakingActivityChecker {	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L18-L18), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

44:contract StakingToken is StakingBase {	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L44-L44), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

21:contract StakingProxy {	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L21-L21), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

81:contract StakingFactory {	// @audit-issue
```
[81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L81-L81), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

132:contract VoteWeighting {	// @audit-issue
```
[132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L132-L132), 


#### Recommendation

Consider defining an `interface` that includes all `external`/`public` functions of the contract. Exposing a well-defined interface helps ensure that the entire API is extracted and provides a clear and standardized way for other contracts or users to interact with your contract.

### Consider using named returns
Using named returns makes the code more self-documenting, makes it easier to fill out NatSpec, and in some cases can save gas. The cases below are where there currently is at most one return statement, which is ideal for named returns.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

13:    function timeLaunch() external view returns (uint256);	// @audit-issue

21:    function ownerOf(uint256 tokenId) external view returns (address);	// @audit-issue

25:    function totalSupply() external view returns (uint256);	// @audit-issue

46:    function exists(uint256 serviceId) external view returns (bool);	// @audit-issue

61:    function getVotes(address account) external view returns (uint256);	// @audit-issue

1080:    function checkpoint() external returns (bool) {	// @audit-issue

1466:    function getUnitPoint(uint256 epoch, uint256 unitType) external view returns (UnitPoint memory) {	// @audit-issue

1472:    function getLastIDF() external view returns (uint256) {	// @audit-issue

1479:    function getEpochEndTime(uint256 epoch) external view returns (uint256) {	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L13-L13), [21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L21-L21), [25](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L25-L25), [46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L46-L46), [61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L61-L61), [1080](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1080-L1080), [1466](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1466-L1466), [1472](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1472-L1472), [1479](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1479-L1479), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

40:    function getBridgingDecimals() external pure returns (uint256);	// @audit-issue

48:    function balanceOf(address account) external view returns (uint256);	// @audit-issue

54:    function transfer(address to, uint256 amount) external returns (bool);	// @audit-issue

120:    function epochCounter() external view returns (uint32);	// @audit-issue

124:    function epochLen() external view returns (uint32);	// @audit-issue

134:    function mapEpochStakingPoints(uint256 eCounter) external view returns (StakingPoint memory);	// @audit-issue

156:    function paused() external returns (uint8);	// @audit-issue

170:    function mapNomineeIds(bytes32 nomineeHash) external returns (uint256);	// @audit-issue

185:    function nomineeRelativeWeight(bytes32 account, uint256 chainId, uint256 time) external view returns (uint256, uint256);	// @audit-issue
```
[40](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L40-L40), [48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L48-L48), [54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L54-L54), [120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L120-L120), [124](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L124-L124), [134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L134-L134), [156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L156-L156), [170](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L170-L170), [185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L185-L185), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

48:    function createRetryableTicket(
49:        address to,
50:        uint256 l2CallValue,
51:        uint256 maxSubmissionCost,
52:        address excessFeeRefundAddress,
53:        address callValueRefundAddress,
54:        uint256 gasLimit,
55:        uint256 maxFeePerGas,
56:        bytes calldata data
57:    ) external payable returns (uint256);	// @audit-issue

63:    function l2ToL1Sender() external view returns (address);	// @audit-issue
```
[57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L48-L57), [63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L63-L63), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

30:    function xDomainMessageSender() external view returns (address);	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L30-L30), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

15:    function approve(address spender, uint256 amount) external returns (bool);	// @audit-issue

212:    function getBridgingDecimals() external pure virtual returns (uint256) {	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L15-L15), [212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L212-L212), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

16:    function sendTxToL1(address destination, bytes calldata data) external payable returns (uint256);	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L16-L16), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

52:    function xDomainMessageSender() external view returns (address);	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L52-L52), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

9:    function approve(address spender, uint256 amount) external returns (bool);	// @audit-issue

15:    function transfer(address to, uint256 amount) external returns (bool);	// @audit-issue

173:    function getBridgingDecimals() external pure returns (uint256) {	// @audit-issue
```
[9](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L9-L9), [15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L15-L15), [173](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L173-L173), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

15:    function requireToPassMessage(address target, bytes memory data, uint256 maxGasLimit) external returns (bytes32);	// @audit-issue

25:    function messageSender() external returns (address);	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L15-L15), [25](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L25-L25), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

138:    function getBridgingDecimals() external pure override returns (uint256) {	// @audit-issue
```
[138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L138-L138), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

15:    function requireToPassMessage(address target, bytes memory data, uint256 maxGasLimit) external returns (bytes32);	// @audit-issue

19:    function messageSender() external returns (address);	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L15-L15), [19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L19-L19), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

26:    function balanceOf(address account) external view returns (uint256);	// @audit-issue

32:    function approve(address spender, uint256 amount) external returns (bool);	// @audit-issue

38:    function transfer(address to, uint256 amount) external returns (bool);	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L26-L26), [32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L32-L32), [38](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L38-L38), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

8:    function rewardsPerSecond() external view returns (uint256);	// @audit-issue

12:    function maxNumServices() external view returns (uint256);	// @audit-issue

16:    function stakingToken() external view returns (address);	// @audit-issue

166:    function verifyImplementation(address implementation) external view returns (bool){	// @audit-issue

179:    function verifyInstance(address instance, address implementation) external view returns (bool) {	// @audit-issue

255:    function getEmissionsAmountLimit(address) external view returns (uint256) {	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L8-L8), [12](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L12-L12), [16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L16-L16), [166](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L166-L166), [179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L179-L179), [255](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L255-L255), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

8:    function nonce() external view returns (uint256);	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L8-L8), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

591:    function checkpoint() public returns (
592:        uint256[] memory,	// @audit-issue
593:        uint256[][] memory,
594:        uint256[] memory,
595:        uint256[] memory,
596:        uint256[] memory evictServiceIds

591:    function checkpoint() public returns (
592:        uint256[] memory,
593:        uint256[][] memory,	// @audit-issue
594:        uint256[] memory,
595:        uint256[] memory,
596:        uint256[] memory evictServiceIds

591:    function checkpoint() public returns (
592:        uint256[] memory,
593:        uint256[][] memory,
594:        uint256[] memory,	// @audit-issue
595:        uint256[] memory,
596:        uint256[] memory evictServiceIds

591:    function checkpoint() public returns (
592:        uint256[] memory,
593:        uint256[][] memory,
594:        uint256[] memory,
595:        uint256[] memory,	// @audit-issue
596:        uint256[] memory evictServiceIds

877:    function claim(uint256 serviceId) external returns (uint256) {	// @audit-issue

884:    function checkpointAndClaim(uint256 serviceId) external returns (uint256) {	// @audit-issue

953:    function getServiceIds() public view returns (uint256[] memory) {	// @audit-issue

959:    function getAgentIds() external view returns (uint256[] memory) {	// @audit-issue
```
[592](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L591-L596), [593](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L591-L596), [594](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L591-L596), [595](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L591-L596), [877](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L877-L877), [884](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L884-L884), [953](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L953-L953), [959](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L959-L959), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

16:    function mapServiceIdTokenDeposit(uint256 serviceId) external view returns (address, uint96);	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L16-L16), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

10:    function emissionsAmount() external view returns (uint256);	// @audit-issue

18:    function verifyImplementation(address implementation) external view returns (bool);	// @audit-issue

24:    function verifyInstance(address instance, address implementation) external view returns (bool);	// @audit-issue

28:    function getEmissionsAmountLimit(address) external view returns (uint256);	// @audit-issue

141:    function getProxyAddressWithNonce(address implementation, uint256 localNonce) public view returns (address) {	// @audit-issue

161:    function getProxyAddress(address implementation) external view returns (address) {	// @audit-issue

267:    function verifyInstance(address instance) public view returns (bool) {	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L10-L10), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L18-L18), [24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L24-L24), [28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L28-L28), [141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L141-L141), [161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L161-L161), [267](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L267-L267), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

223:    function _getSum() internal returns (uint256) {	// @audit-issue

254:    function _getWeight(bytes32 account, uint256 chainId) internal returns (uint256) {	// @audit-issue

578:    function _maxAndSub(uint256 a, uint256 b) internal pure returns (uint256) {	// @audit-issue

674:    function getNomineeWeight(bytes32 account, uint256 chainId) external view returns (uint256) {	// @audit-issue

684:    function getWeightsSum() external view returns (uint256) {	// @audit-issue

691:    function getNumNominees() external view returns (uint256) {	// @audit-issue

698:    function getNumRemovedNominees() external view returns (uint256) {	// @audit-issue

705:    function getAllNominees() external view returns (Nominee[] memory) {	// @audit-issue

712:    function getAllRemovedNominees() external view returns (Nominee[] memory) {	// @audit-issue

720:    function getNomineeId(bytes32 account, uint256 chainId) external view returns (uint256) {	// @audit-issue

732:    function getRemovedNomineeId(bytes32 account, uint256 chainId) external view returns (uint256) {	// @audit-issue

744:    function getNominee(uint256 id) external view returns (Nominee memory) {	// @audit-issue

761:    function getRemovedNominee(uint256 id) external view returns (Nominee memory) {	// @audit-issue
```
[223](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L223-L223), [254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L254-L254), [578](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L578-L578), [674](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L674-L674), [684](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L684-L684), [691](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L691-L691), [698](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L698-L698), [705](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L705-L705), [712](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L712-L712), [720](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L720-L720), [732](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L732-L732), [744](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L744-L744), [761](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L761-L761), 


#### Recommendation

Adopt named returns in your Solidity functions, especially in cases where functions contain a single return statement. This approach enhances code readability and documentation by making the return values clear and explicit. When defining your function, specify the return types with names, and manipulate these named variables directly within your function logic. Additionally, leverage named returns to streamline your NatSpec documentation, providing clear descriptions for each return variable. Evaluate your current contracts for opportunities to refactor functions to use named returns, prioritizing those with simple return patterns for immediate benefits in gas efficiency and code clarity.

### Contract timekeeping will break earlier than the Ethereum network itself will stop working
When a timestamp is downcast from `uint256` to `uint32`, the value will wrap in the year 2106, and the contracts will break. Other downcasts will have different endpoints. Consider whether your contract is intended to live past the size of the type being used.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

469:        uint256 zeroYearSecondsLeft = uint32(_timeLaunch + ONE_YEAR - block.timestamp);	// @audit-issue

478:        mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);	// @audit-issue

1220:        tp.epochPoint.endTime = uint32(block.timestamp);	// @audit-issue
```
[469](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L469-L469), [478](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L478-L478), [1220](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1220-L1220), 


#### Recommendation

Carefully consider the choice of data types for timestamps in your Solidity contracts. If downcasting from `uint256` to smaller types like `uint32`, be aware of the overflow risks and the potential for the contract to break prematurely. For contracts intended to operate beyond these time limits, use larger integer types capable of representing dates far into the future. Additionally, document any decisions around the use of specific timestamp types and their implications for the contract's longevity and reliability. Regularly review and update your contracts to ensure that they remain robust against future changes in the Ethereum network and the broader blockchain ecosystem.

### Use `abi.encodeCall()` instead of `abi.encodeWithSignature()`/`abi.encodeWithSelector()`
Starting with version 0.8.11, abi.encodeCall() has compiler [type safety](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3693), whereas the other two functions do not.

```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

187:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));	// @audit-issue
```
[187](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L187-L187), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

85:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));	// @audit-issue
```
[85](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L85-L85), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

55:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));	// @audit-issue
```
[55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L55-L55), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

136:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));	// @audit-issue
```
[136](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L136-L136), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

73:            bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));	// @audit-issue
```
[73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L73-L73), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

77:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));	// @audit-issue
```
[77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L77-L77), 


#### Recommendation

In Solidity version 0.8.11 and later, it's recommended to use `abi.encodeCall()` for creating function call data, as it provides better type safety compared to `abi.encodeWithSignature()` or `abi.encodeWithSelector()`. This helps prevent type-related errors and ensures more reliable contract interactions.

### Missing events in initializers/deploys
As a best practice, consider emitting an event when the contract is initialized. In this way, it's easy for the user to track the exact point in time when the contract was initialized, by filtering the emitted events.

```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

20:    function initialize(StakingParams memory _stakingParams) external {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L20-L20), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

278:    function _initialize(	// @audit-issue
```
[278](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L278-L278), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

54:    function initialize(	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L54-L54), 


#### Recommendation

To provide transparency and enable users to track the initialization of the contract, consider emitting an event within the contract's initializer function. Emitting an event during initialization can help users pinpoint the exact moment the contract was initialized by filtering and monitoring the emitted events.

### Hardcoded `address` should be avoided
It's better to declare the hardcoded addresses as `immutable` state variables, as this will facilitate deployment on other chains.


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

46:        uint160 offset = uint160(0x1111000000000000000000000000000000001111);	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L46-L46), 


#### Recommendation

To enhance the flexibility and portability of your smart contract, consider declaring hardcoded addresses as `immutable` state variables. This allows for easier deployment on various chains and ensures that addresses can be updated if necessary without modifying the contract's code.

### Inefficient Array Usage
Use mappings instead of arrays for managing lists of data in order to conserve gas. Mappings are less expensive and more efficient for accessing any value without having to iterate through an array.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

886:        uint256[] memory serviceIds,	// @audit-issue

887:        uint256[] memory amounts,	// @audit-issue

891:        address[] memory registries = new address[](2);	// @audit-issue

895:        bool[] memory incentiveFlags = new bool[](4);	// @audit-issue

918:                (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).	// @audit-issue

992:        uint256[] memory serviceIds,	// @audit-issue

993:        uint256[] memory amounts,	// @audit-issue

1114:        uint256[] memory incentives = new uint256[](9);	// @audit-issue

1310:        uint256[] memory unitTypes,	// @audit-issue

1311:        uint256[] memory unitIds	// @audit-issue

1324:        address[] memory registries = new address[](2);	// @audit-issue

1328:        uint256[] memory registriesSupply = new uint256[](2);	// @audit-issue

1334:        uint256[] memory lastIds = new uint256[](2);	// @audit-issue

1387:        uint256[] memory unitTypes,	// @audit-issue

1388:        uint256[] memory unitIds	// @audit-issue

1396:        address[] memory registries = new address[](2);	// @audit-issue

1400:        uint256[] memory registriesSupply = new uint256[](2);	// @audit-issue

1406:        uint256[] memory lastIds = new uint256[](2);	// @audit-issue
```
[886](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L886-L886), [887](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L887-L887), [891](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L891-L891), [895](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L895-L895), [918](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L918-L918), [992](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L992-L992), [993](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L993-L993), [1114](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1114-L1114), [1310](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1310-L1310), [1311](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1311-L1311), [1324](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1324-L1324), [1328](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1328-L1328), [1334](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1334-L1334), [1387](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1387-L1387), [1388](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1388-L1388), [1396](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1396-L1396), [1400](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1400-L1400), [1406](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1406-L1406), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

36:            uint96[10] memory supplyCaps = [	// @audit-issue

73:            uint88[10] memory inflationAmounts = [	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L36-L36), [73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L73-L73), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

442:        uint256[] memory chainIds,	// @audit-issue

443:        bytes32[][] memory stakingTargets,	// @audit-issue

444:        uint256[][] memory stakingIncentives,	// @audit-issue

445:        bytes[] memory bridgePayloads,	// @audit-issue

446:        uint256[] memory transferAmounts,	// @audit-issue

447:        uint256[] memory valueAmounts	// @audit-issue

461:            bool[] memory positions = new bool[](stakingTargets[i].length);	// @audit-issue

470:            bytes32[] memory updatedStakingTargets = new bytes32[](numActualTargets);	// @audit-issue

471:            uint256[] memory updatedStakingAmounts = new uint256[](numActualTargets);	// @audit-issue

484:                address[] memory stakingTargetsEVM = new address[](updatedStakingTargets.length);	// @audit-issue

507:        uint256[] memory chainIds,	// @audit-issue

508:        bytes32[][] memory stakingTargets,	// @audit-issue

509:        bytes[] memory bridgePayloads,	// @audit-issue

510:        uint256[] memory valueAmounts	// @audit-issue

575:        uint256[] memory chainIds,	// @audit-issue

576:        bytes32[][] memory stakingTargets	// @audit-issue

705:    function setDepositProcessorChainIds(address[] memory depositProcessors, uint256[] memory chainIds) external {	// @audit-issue

790:        uint256[] memory unitTypes,	// @audit-issue

791:        uint256[] memory unitIds	// @audit-issue

1060:        uint256[] memory chainIds,	// @audit-issue

1061:        bytes32[][] memory stakingTargets,	// @audit-issue

1062:        bytes[] memory bridgePayloads,	// @audit-issue

1063:        uint256[] memory valueAmounts	// @audit-issue

1089:        uint256[] memory totalAmounts;	// @audit-issue

1091:        uint256[][] memory stakingIncentives;	// @audit-issue

1092:        uint256[] memory transferAmounts;	// @audit-issue
```
[442](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L442-L442), [443](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L443-L443), [444](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L444-L444), [445](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L445-L445), [446](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L446-L446), [447](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L447-L447), [461](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L461-L461), [470](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L470-L470), [471](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L471-L471), [484](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L484-L484), [507](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L507-L507), [508](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L508-L508), [509](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L509-L509), [510](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L510-L510), [575](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L575-L575), [576](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L576-L576), [705](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L705-L705), [790](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L790-L790), [791](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L791-L791), [1060](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1060-L1060), [1061](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1061-L1061), [1062](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1062-L1062), [1063](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1063-L1063), [1089](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1089-L1089), [1091](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1091-L1091), [1092](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1092-L1092), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

120:        address[] memory targets,	// @audit-issue

121:        uint256[] memory stakingIncentives,	// @audit-issue

152:        uint256[] memory cost = new uint256[](2);	// @audit-issue
```
[120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L120-L120), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L121-L121), [152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L152-L152), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

97:        address[] memory targets,	// @audit-issue

98:        uint256[] memory stakingIncentives,	// @audit-issue

144:        address[] memory targets = new address[](1);	// @audit-issue

146:        uint256[] memory stakingIncentives = new uint256[](1);	// @audit-issue

165:        address[] memory targets,	// @audit-issue

166:        uint256[] memory stakingIncentives,	// @audit-issue
```
[97](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L97-L97), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L98-L98), [144](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L144-L144), [146](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L146-L146), [165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L165-L165), [166](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L166-L166), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

96:        address[] memory targets,	// @audit-issue

97:        uint256[] memory stakingIncentives,	// @audit-issue
```
[96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L96-L96), [97](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L97-L97), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

86:    function _deposit(address[] memory targets, uint256[] memory stakingIncentives) internal {	// @audit-issue

142:        address[] memory targets = new address[](1);	// @audit-issue

144:        uint256[] memory stakingIncentives = new uint256[](1);	// @audit-issue

156:        address[] memory targets,	// @audit-issue

157:        uint256[] memory stakingIncentives,	// @audit-issue
```
[86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L86-L86), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L142-L142), [144](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L144-L144), [156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L156-L156), [157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L157-L157), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

135:        TokenReceived[] memory receivedTokens,	// @audit-issue
```
[135](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L135-L135), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

52:        address[] memory targets,	// @audit-issue

53:        uint256[] memory stakingIncentives,	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L52-L52), [53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L53-L53), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

60:        address[] memory targets,	// @audit-issue

61:        uint256[] memory stakingIncentives,	// @audit-issue

108:        bytes[] memory,	// @audit-issue
```
[60](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L60-L60), [61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L61-L61), [108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L108-L108), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

147:        (address[] memory targets, uint256[] memory amounts) = abi.decode(data, (address[], uint256[]));	// @audit-issue
```
[147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L147-L147), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

58:        address[] memory targets,	// @audit-issue

59:        uint256[] memory stakingIncentives,	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L58-L58), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L59-L59), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

132:        address[] memory implementations,	// @audit-issue

133:        bool[] memory statuses,	// @audit-issue
```
[132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L132-L132), [133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L133-L133), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

51:        uint256[] memory curNonces,	// @audit-issue

52:        uint256[] memory lastNonces,	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L51-L51), [52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L52-L52), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

270:    uint256[] public agentIds;	// @audit-issue

274:    uint256[] public setServiceIds;	// @audit-issue

369:        uint32[] memory	// @audit-issue

379:        (uint256 numAgentIds, IService.AgentParams[] memory agentParams) = IService(serviceRegistry).getAgentParams(serviceId);	// @audit-issue

400:        uint256[] memory lastNonces,	// @audit-issue

431:        uint256[] memory evictServiceIds,	// @audit-issue

432:        uint256[] memory serviceInactivity,	// @audit-issue

440:        uint256[] memory serviceIds = new uint256[](numEvictServices);	// @audit-issue

441:        address[] memory owners = new address[](numEvictServices);	// @audit-issue

442:        address[] memory multisigs = new address[](numEvictServices);	// @audit-issue

443:        uint256[] memory inactivity = new uint256[](numEvictServices);	// @audit-issue

444:        uint256[] memory serviceIndexes = new uint256[](numEvictServices);	// @audit-issue

600:        (uint256 lastAvailableRewards, uint256 numServices, uint256 totalRewards,	// @audit-issue

606:        uint256[] memory finalEligibleServiceIds;	// @audit-issue

607:        uint256[] memory finalEligibleServiceRewards;	// @audit-issue

789:        uint256[] memory nonces = IActivityChecker(activityChecker).getMultisigNonces(service.multisig);	// @audit-issue

823:        (uint256[] memory serviceIds, , , , uint256[] memory evictServiceIds) = checkpoint();	// @audit-issue

848:        uint256[] memory nonces = sInfo.nonces;	// @audit-issue

895:        (uint256 lastAvailableRewards, uint256 numServices, uint256 totalRewards, uint256[] memory eligibleServiceIds,	// @audit-issue

155:    uint256[] nonces;	// @audit-issue
```
[270](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L270-L270), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L274-L274), [369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L369-L369), [379](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L379-L379), [400](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L400-L400), [431](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L431-L431), [432](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L432-L432), [440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L440-L440), [441](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L441-L441), [442](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L442-L442), [443](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L443-L443), [444](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L444-L444), [600](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L600-L600), [606](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L606-L606), [607](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L607-L607), [789](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L789-L789), [823](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L823-L823), [848](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L848-L848), [895](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L895-L895), [155](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L155-L155), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

74:    function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {	// @audit-issue
```
[74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L74-L74), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

168:    Nominee[] public setNominees;	// @audit-issue

170:    Nominee[] public setRemovedNominees;	// @audit-issue

564:        bytes32[] memory accounts,	// @audit-issue

565:        uint256[] memory chainIds,	// @audit-issue

566:        uint256[] memory weights	// @audit-issue

780:        bytes32[] memory accounts,	// @audit-issue

781:        uint256[] memory chainIds,	// @audit-issue

782:        address[] memory voters	// @audit-issue
```
[168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L168-L168), [170](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L170-L170), [564](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L564-L564), [565](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L565-L565), [566](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L566-L566), [780](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L780-L780), [781](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L781-L781), [782](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L782-L782), 


#### Recommendation

In scenarios where data access efficiency is critical, prefer using mappings over arrays in Solidity contracts. Mappings offer more efficient and gas-effective data retrieval and updates, especially when dealing with large or frequently accessed datasets. Ensure to structure your data and choose keys thoughtfully to maximize the efficiency gains offered by mappings. While arrays might be suitable for ordered data or when the entire dataset needs to be iterated, for most other use cases, mappings are likely to be the more gas-efficient choice.

### Enum values should be used in place of constant array indexes
Consider using an `enum` instead of hardcoding an index access to make the code easier to understand.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

478:        mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);	// @audit-issue

482:        TokenomicsPoint storage tp = mapEpochTokenomics[1];	// @audit-issue

490:        tp.unitPoints[0].rewardUnitFraction = 83;	// @audit-issue

491:        tp.unitPoints[1].rewardUnitFraction = 17;	// @audit-issue

504:        tp.unitPoints[0].topUpUnitFraction = 41;	// @audit-issue

505:        tp.unitPoints[1].topUpUnitFraction = 9;	// @audit-issue

732:        tp.unitPoints[0].rewardUnitFraction = uint8(_rewardComponentFraction);	// @audit-issue

733:        tp.unitPoints[1].rewardUnitFraction = uint8(_rewardAgentFraction);	// @audit-issue

738:        tp.unitPoints[0].topUpUnitFraction = uint8(_topUpComponentFraction);	// @audit-issue

739:        tp.unitPoints[1].topUpUnitFraction = uint8(_topUpAgentFraction);	// @audit-issue

892:        (registries[0], registries[1]) = (componentRegistry, agentRegistry);	// @audit-issue

896:        incentiveFlags[0] = (mapEpochTokenomics[curEpoch].unitPoints[0].rewardUnitFraction > 0);	// @audit-issue

897:        incentiveFlags[1] = (mapEpochTokenomics[curEpoch].unitPoints[1].rewardUnitFraction > 0);	// @audit-issue

898:        incentiveFlags[2] = (mapEpochTokenomics[curEpoch].unitPoints[0].topUpUnitFraction > 0);	// @audit-issue

899:        incentiveFlags[3] = (mapEpochTokenomics[curEpoch].unitPoints[1].topUpUnitFraction > 0);	// @audit-issue

909:            if (incentiveFlags[2] || incentiveFlags[3]) {	// @audit-issue

1115:        incentives[0] = tp.epochPoint.totalDonationsETH;	// @audit-issue

1116:        incentives[1] = (incentives[0] * tp.epochPoint.rewardTreasuryFraction) / 100;	// @audit-issue

1118:        incentives[2] = (incentives[0] * tp.unitPoints[0].rewardUnitFraction) / 100;	// @audit-issue

1119:        incentives[3] = (incentives[0] * tp.unitPoints[1].rewardUnitFraction) / 100;	// @audit-issue

1152:        incentives[4] = (inflationPerEpoch * tp.epochPoint.maxBondFraction) / 100;	// @audit-issue

1164:        if (incentives[4] > curMaxBond) {	// @audit-issue

1166:            incentives[4] = effectiveBond + incentives[4] - curMaxBond;	// @audit-issue

1167:            effectiveBond = uint96(incentives[4]);	// @audit-issue

1223:        uint256 accountRewards = incentives[2] + incentives[3];	// @audit-issue

1225:        incentives[5] = (inflationPerEpoch * tp.unitPoints[0].topUpUnitFraction) / 100;	// @audit-issue

1227:        incentives[6] = (inflationPerEpoch * tp.unitPoints[1].topUpUnitFraction) / 100;	// @audit-issue

1231:        uint256 accountTopUps = incentives[5] + incentives[6];	// @audit-issue

1235:        incentives[7] = mapEpochStakingPoints[eCounter].stakingIncentive;	// @audit-issue

1237:        incentives[8] = incentives[7] + (inflationPerEpoch * mapEpochStakingPoints[eCounter].stakingFraction) / 100;	// @audit-issue

1238:        mapEpochStakingPoints[eCounter].stakingIncentive = uint96(incentives[8]);	// @audit-issue

1274:        if (incentives[0] > 0) {	// @audit-issue

1276:            uint256 idf = _calculateIDF(incentives[1], tp.epochPoint.numNewOwners);	// @audit-issue

1285:        if (incentives[1] == 0 || ITreasury(treasury).rebalanceTreasury(incentives[1])) {	// @audit-issue

1287:            emit EpochSettled(eCounter, incentives[1], accountRewards, accountTopUps, curMaxBond, incentives[7],	// @audit-issue

1288:                incentives[8]);	// @audit-issue

1325:        (registries[0], registries[1]) = (componentRegistry, agentRegistry);	// @audit-issue

1397:        (registries[0], registries[1]) = (componentRegistry, agentRegistry);	// @audit-issue
```
[478](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L478-L478), [482](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L482-L482), [490](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L490-L490), [491](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L491-L491), [504](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L504-L504), [505](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L505-L505), [732](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L732-L732), [733](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L733-L733), [738](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L738-L738), [739](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L739-L739), [892](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L892-L892), [896](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L896-L896), [897](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L897-L897), [898](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L898-L898), [899](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L899-L899), [909](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L909-L909), [1115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1115-L1115), [1116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1116-L1116), [1118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1118-L1118), [1119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1119-L1119), [1152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1152-L1152), [1164](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1164-L1164), [1166](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1166-L1166), [1167](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1167-L1167), [1223](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1223-L1223), [1225](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1225-L1225), [1227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1227-L1227), [1231](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1231-L1231), [1235](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1235-L1235), [1237](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1237-L1237), [1238](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1238-L1238), [1274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1274-L1274), [1276](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1276-L1276), [1285](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1285-L1285), [1287](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1287-L1287), [1288](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1288-L1288), [1325](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1325-L1325), [1397](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1397-L1397), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

610:                totalAmounts[0] += stakingIncentive;	// @audit-issue

611:                totalAmounts[2] += returnAmount;	// @audit-issue

630:            totalAmounts[1] += transferAmounts[i];	// @audit-issue

1098:        if (totalAmounts[2] > 0) {	// @audit-issue

1099:            ITokenomics(tokenomics).refundFromStaking(totalAmounts[2]);	// @audit-issue

1103:        if (totalAmounts[0] > 0) {	// @audit-issue

1105:            if (totalAmounts[1] > 0) {	// @audit-issue

1109:                ITreasury(treasury).withdrawToAccount(address(this), 0, totalAmounts[1]);	// @audit-issue

1113:                if (olasBalance != totalAmounts[1]) {	// @audit-issue

1114:                    revert WrongAmount(olasBalance, totalAmounts[1]);	// @audit-issue

1123:        emit StakingIncentivesClaimed(msg.sender, totalAmounts[0], totalAmounts[1], totalAmounts[2]);	// @audit-issue
```
[610](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L610-L610), [611](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L611-L611), [630](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L630-L630), [1098](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1098-L1098), [1099](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1099-L1099), [1103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1103-L1103), [1105](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1105-L1105), [1109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1109-L1109), [1113](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1113-L1113), [1114](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1114-L1114), [1123](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1123-L1123), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

159:            cost[0] = maxSubmissionCostToken + TOKEN_GAS_LIMIT * gasPriceBid;	// @audit-issue

163:        cost[1] = maxSubmissionCostMessage + gasLimitMessage * gasPriceBid;	// @audit-issue

165:        uint256 totalCost = cost[0] + cost[1];	// @audit-issue

182:            IBridge(l1TokenRelayer).outboundTransferCustomRefund{value: cost[0]}(olas, refundAccount,	// @audit-issue

190:        sequence = IBridge(l1MessageRelayer).createRetryableTicket{value: cost[1]}(l2TargetDispenser, 0,	// @audit-issue
```
[159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L159-L159), [163](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L163-L163), [165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L165-L165), [182](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L182-L182), [190](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L190-L190), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

145:        targets[0] = target;	// @audit-issue

147:        stakingIncentives[0] = stakingIncentive;	// @audit-issue
```
[145](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L145-L145), [147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L147-L147), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

143:        targets[0] = target;	// @audit-issue

145:        stakingIncentives[0] = stakingIncentive;	// @audit-issue
```
[143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L143-L143), [145](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L145-L145), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

159:        if (receivedTokens[0].tokenAddress != olas) {	// @audit-issue

160:            revert WrongTokenAddress(receivedTokens[0].tokenAddress, olas);	// @audit-issue
```
[159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L159-L159), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L160-L160), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

38:        nonces[0] = IMultisig(multisig).nonce();	// @audit-issue

57:        if (ts > 0 && curNonces[0] > lastNonces[0]) {	// @audit-issue

58:            uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts;	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L38-L38), [57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L57-L57), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L58-L58), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

633:                updatedReward = (eligibleServiceRewards[0] * lastAvailableRewards) / totalRewards;	// @audit-issue

635:                curServiceId = eligibleServiceIds[0];	// @audit-issue

636:                finalEligibleServiceIds[0] = eligibleServiceIds[0];	// @audit-issue

641:                finalEligibleServiceRewards[0] = updatedReward;	// @audit-issue
```
[633](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L633-L633), [635](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L635-L635), [636](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L636-L636), [641](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L641-L641), 


#### Recommendation

To improve code readability and maintainability, replace hardcoded array indexes with corresponding enum values. Enum values provide descriptive names for array elements, making your code more self-explanatory and reducing the risk of errors when working with arrays. This enhances the overall clarity and robustness of your smart contract code.

### Lack of specific import identifier
It is better to use `import {<identifier>} from "<file.sol>"` instead of `import "<file.sol>"` to improve readability and speed up the compilation time.

```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

4:import "./DefaultTargetDispenserL2.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L4-L4), 


#### Recommendation

To improve code clarity and avoid naming conflicts, it's recommended to use specific import identifiers when importing from other contracts or libraries. Instead of using `import "<file.sol>";`, specify the desired identifiers using `import { <identifier1>, <identifier2> } from "<file.sol>";`. This not only enhances readability but also can speed up compilation times by only importing the necessary symbols.

### Returning a struct instead of returning many variables is better
If a function returns [too many variables](https://docs.soliditylang.org/en/latest/contracts.html#returning-multiple-values), replacing them with a struct can improve code readability, maintainability and reusability.

```solidity
Path: ./tokenomics/contracts/Dispenser.sol

573:    function _calculateStakingIncentivesBatch(
574:        uint256 numClaimedEpochs,
575:        uint256[] memory chainIds,
576:        bytes32[][] memory stakingTargets
577:    ) internal returns (
578:        uint256[] memory totalAmounts,
579:        uint256[][] memory stakingIncentives,
580:        uint256[] memory transferAmounts
581:    ) {	// @audit-issue

849:    function calculateStakingIncentives(
850:        uint256 numClaimedEpochs,
851:        uint256 chainId,
852:        bytes32 stakingTarget,
853:        uint256 bridgingDecimals
854:    ) public returns (
855:        uint256 totalStakingIncentive,
856:        uint256 totalReturnAmount,
857:        uint256 lastClaimedEpoch,
858:        bytes32 nomineeHash
859:    ) {	// @audit-issue
```
[581](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L573-L581), [859](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L849-L859), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

522:    function _calculateStakingRewards() internal view returns (
523:        uint256 lastAvailableRewards,
524:        uint256 numServices,
525:        uint256 totalRewards,
526:        uint256[] memory eligibleServiceIds,
527:        uint256[] memory eligibleServiceRewards,
528:        uint256[] memory serviceIds,
529:        uint256[][] memory serviceNonces,
530:        uint256[] memory serviceInactivity
531:    )	// @audit-issue

591:    function checkpoint() public returns (
592:        uint256[] memory,
593:        uint256[][] memory,
594:        uint256[] memory,
595:        uint256[] memory,
596:        uint256[] memory evictServiceIds
597:    )	// @audit-issue
```
[531](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L522-L531), [597](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L591-L597), 


#### Recommendation

Consider returning a struct instead of multiple variables when a function returns many variables. This can enhance code readability, maintainability, and reusability, as well as make the contract interface more user-friendly.

### Consider using a `struct` rather than having many function input parameters
In Solidity, functions with a large number of input parameters can become cumbersome to manage and call. This can lead to readability issues and increased likelihood of errors, especially if the order of parameters is complex or not intuitive. To streamline this, consider consolidating multiple parameters into a single `struct`. This approach not only simplifies function signatures but also enhances code clarity. Structs allow for grouping related data together, making it easier to understand the relationship between parameters and manage them as a single entity.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

409:    function initializeTokenomics(
410:        address _olas,
411:        address _treasury,
412:        address _depository,
413:        address _dispenser,
414:        address _ve,
415:        uint256 _epochLen,
416:        address _componentRegistry,
417:        address _agentRegistry,
418:        address _serviceRegistry,
419:        address _donatorBlacklist
420:    ) external {

639:    function changeTokenomicsParameters(
640:        uint256 _devsPerCapital,
641:        uint256 _codePerDev,
642:        uint256 _epsilonRate,
643:        uint256 _epochLen,
644:        uint256 _veOLASThreshold
645:    ) external {

703:    function changeIncentiveFractions(
704:        uint256 _rewardComponentFraction,
705:        uint256 _rewardAgentFraction,
706:        uint256 _maxBondFraction,
707:        uint256 _topUpComponentFraction,
708:        uint256 _topUpAgentFraction,
709:        uint256 _stakingFraction
710:    ) external {
```
[512](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L409-L420), [694](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L639-L645), [746](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L703-L710), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

315:    constructor(
316:        address _olas,
317:        address _tokenomics,
318:        address _treasury,
319:        address _voteWeighting,
320:        bytes32 _retainer,
321:        uint256 _maxNumClaimingEpochs,
322:        uint256 _maxNumStakingTargets
323:    ) {

408:    function _distributeStakingIncentives(
409:        uint256 chainId,
410:        bytes32 stakingTarget,
411:        uint256 stakingIncentive,
412:        bytes memory bridgePayload,
413:        uint256 transferAmount
414:    ) internal {

441:    function _distributeStakingIncentivesBatch(
442:        uint256[] memory chainIds,
443:        bytes32[][] memory stakingTargets,
444:        uint256[][] memory stakingIncentives,
445:        bytes[] memory bridgePayloads,
446:        uint256[] memory transferAmounts,
447:        uint256[] memory valueAmounts
448:    ) internal {

1058:    function claimStakingIncentivesBatch(
1059:        uint256 numClaimedEpochs,
1060:        uint256[] memory chainIds,
1061:        bytes32[][] memory stakingTargets,
1062:        bytes[] memory bridgePayloads,
1063:        uint256[] memory valueAmounts
1064:    ) external payable {
```
[349](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L315-L323), [432](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L408-L414), [498](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L441-L448), [1126](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1058-L1064), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

89:    constructor(
90:        address _olas,
91:        address _l1Dispenser,
92:        address _l1TokenRelayer,
93:        address _l1MessageRelayer,
94:        uint256 _l2TargetChainId,
95:        address _l1ERC20Gateway,
96:        address _outbox,
97:        address _bridge
98:    )
```
[109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L89-L98), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

47:    constructor(
48:        address _olas,
49:        address _proxyFactory,
50:        address _l2MessageRelayer,
51:        address _l1DepositProcessor,
52:        uint256 _l1SourceChainId
53:    ) DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId) {}	// @audit-issue
```
[53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L47-L53), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

59:    constructor(
60:        address _olas,
61:        address _l1Dispenser,
62:        address _l1TokenRelayer,
63:        address _l1MessageRelayer,
64:        uint256 _l2TargetChainId
65:    ) {
```
[87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L59-L65), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

36:    constructor(
37:        address _olas,
38:        address _proxyFactory,
39:        address _l2MessageRelayer,
40:        address _l1DepositProcessor,
41:        uint256 _l1SourceChainId
42:    )
```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L36-L42), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

76:    constructor(
77:        address _olas,
78:        address _l1Dispenser,
79:        address _l1TokenRelayer,
80:        address _l1MessageRelayer,
81:        uint256 _l2TargetChainId,
82:        address _olasL2
83:    )
```
[92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L76-L83), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

64:    constructor(
65:        address _olas,
66:        address _proxyFactory,
67:        address _l2MessageRelayer,
68:        address _l1DepositProcessor,
69:        uint256 _l1SourceChainId,
70:        address _wormholeCore,
71:        address _l2TokenRelayer
72:    )

133:    function receivePayloadAndTokens(
134:        bytes memory data,
135:        TokenReceived[] memory receivedTokens,
136:        bytes32 sourceProcessor,
137:        uint16 sourceChainId,
138:        bytes32 deliveryHash
139:    ) internal override {
```
[87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L64-L72), [168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L133-L139), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

42:    constructor(
43:        address _olas,
44:        address _l1Dispenser,
45:        address _l1TokenRelayer,
46:        address _l1MessageRelayer,
47:        uint256 _l2TargetChainId
48:    ) DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId) {}	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L42-L48), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

20:    constructor(
21:        address _olas,
22:        address _proxyFactory,
23:        address _l2MessageRelayer,
24:        address _l1DepositProcessor,
25:        uint256 _l1SourceChainId
26:    )
```
[29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L20-L26), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

28:    constructor(
29:        address _olas,
30:        address _l1Dispenser,
31:        address _l1TokenRelayer,
32:        address _l1MessageRelayer,
33:        uint256 _l2TargetChainId,
34:        address _wormholeCore,
35:        uint256 _wormholeTargetChainId
36:    )

106:    function receiveWormholeMessages(
107:        bytes memory data,
108:        bytes[] memory,
109:        bytes32 sourceAddress,
110:        uint16 sourceChain,
111:        bytes32 deliveryHash
112:    ) external {
```
[56](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L28-L36), [128](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L106-L112), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

39:    constructor(
40:        address _olas,
41:        address _proxyFactory,
42:        address _l2MessageRelayer,
43:        address _l1DepositProcessor,
44:        uint256 _l1SourceChainId,
45:        address _l2TokenRelayer
46:    )
```
[55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L39-L46), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

101:    constructor(
102:        address _olas,
103:        address _stakingFactory,
104:        address _l2MessageRelayer,
105:        address _l1DepositProcessor,
106:        uint256 _l1SourceChainId
107:    ) {
```
[135](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L101-L107), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

36:    constructor(
37:        address _olas,
38:        address _l1Dispenser,
39:        address _l1TokenRelayer,
40:        address _l1MessageRelayer,
41:        uint256 _l2TargetChainId,
42:        address _checkpointManager,
43:        address _predicate
44:    )
```
[54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L36-L44), 


#### Recommendation

When faced with functions having numerous input parameters, refactor them to accept a `struct` instead. Define a `struct` that encapsulates all these parameters, thereby simplifying the function signature and improving code readability and maintainability. This method is particularly effective in complex functions or those with parameters that are logically related, making the code more intuitive and less error-prone.

### Ensure Events Emission Prior to External Calls to Prevent Out-of-Order Issues
It's essential to ensure that events follow the best practice of check-effects-interaction and are emitted before any external calls to prevent out-of-order events due to reentrancy. Emitting events post external interactions may cause them to be out of order due to reentrancy, which can be misleading or erroneous for event listeners. [Refer to the Solidity Documentation for best practices](https://solidity.readthedocs.io/en/latest/security-considerations.html#reentrancy).

```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162:
163:            uint256 limitAmount;
164:            // If the function call was successful, check the return value
165:            if (success && returnData.length == 32) {
166:                limitAmount = abi.decode(returnData, (uint256));
167:            }
168:
169:            // If the limit amount is zero, withhold OLAS amount and continue
170:            if (limitAmount == 0) {
171:                // Withhold OLAS for further usage
172:                localWithheldAmount += amount;
173:                emit AmountWithheld(target, amount);	// @audit-issue

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162:
163:            uint256 limitAmount;
164:            // If the function call was successful, check the return value
165:            if (success && returnData.length == 32) {
166:                limitAmount = abi.decode(returnData, (uint256));
167:            }
168:
169:            // If the limit amount is zero, withhold OLAS amount and continue
170:            if (limitAmount == 0) {
171:                // Withhold OLAS for further usage
172:                localWithheldAmount += amount;
173:                emit AmountWithheld(target, amount);
174:
175:                // Proceed to the next target
176:                continue;
177:            }
178:
179:            // Check the amount limit and adjust, if necessary
180:            if (amount > limitAmount) {
181:                uint256 targetWithheldAmount = amount - limitAmount;
182:                localWithheldAmount += targetWithheldAmount;
183:                amount = limitAmount;
184:
185:                emit AmountWithheld(target, targetWithheldAmount);	// @audit-issue

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162:
163:            uint256 limitAmount;
164:            // If the function call was successful, check the return value
165:            if (success && returnData.length == 32) {
166:                limitAmount = abi.decode(returnData, (uint256));
167:            }
168:
169:            // If the limit amount is zero, withhold OLAS amount and continue
170:            if (limitAmount == 0) {
171:                // Withhold OLAS for further usage
172:                localWithheldAmount += amount;
173:                emit AmountWithheld(target, amount);
174:
175:                // Proceed to the next target
176:                continue;
177:            }
178:
179:            // Check the amount limit and adjust, if necessary
180:            if (amount > limitAmount) {
181:                uint256 targetWithheldAmount = amount - limitAmount;
182:                localWithheldAmount += targetWithheldAmount;
183:                amount = limitAmount;
184:
185:                emit AmountWithheld(target, targetWithheldAmount);
186:            }
187:
188:            // Check the OLAS balance and the contract being unpaused
189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {
190:                // Approve and transfer OLAS to the service staking target
191:                IToken(olas).approve(target, amount);
192:                IStaking(target).deposit(amount);
193:
194:                emit StakingTargetDeposited(target, amount);	// @audit-issue

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162:
163:            uint256 limitAmount;
164:            // If the function call was successful, check the return value
165:            if (success && returnData.length == 32) {
166:                limitAmount = abi.decode(returnData, (uint256));
167:            }
168:
169:            // If the limit amount is zero, withhold OLAS amount and continue
170:            if (limitAmount == 0) {
171:                // Withhold OLAS for further usage
172:                localWithheldAmount += amount;
173:                emit AmountWithheld(target, amount);
174:
175:                // Proceed to the next target
176:                continue;
177:            }
178:
179:            // Check the amount limit and adjust, if necessary
180:            if (amount > limitAmount) {
181:                uint256 targetWithheldAmount = amount - limitAmount;
182:                localWithheldAmount += targetWithheldAmount;
183:                amount = limitAmount;
184:
185:                emit AmountWithheld(target, targetWithheldAmount);
186:            }
187:
188:            // Check the OLAS balance and the contract being unpaused
189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {
190:                // Approve and transfer OLAS to the service staking target
191:                IToken(olas).approve(target, amount);
192:                IStaking(target).deposit(amount);
193:
194:                emit StakingTargetDeposited(target, amount);
195:            } else {
196:                // Hash of target + amount + batchNonce
197:                bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
198:                // Queue the hash for further redeem
199:                stakingQueueingNonces[queueHash] = true;
200:
201:                emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);	// @audit-issue

396:        (bool success, ) = msg.sender.call{value: amount}("");
397:        if (!success) {
398:            revert TransferFailed(address(0), address(this), msg.sender, amount);
399:        }
400:
401:        emit Drain(msg.sender, amount);	// @audit-issue
```
[173](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L173), [185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L185), [194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L194), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L201), [401](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L396-L401), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

216:        (bool success, bytes memory returnData) = instance.call(initPayload);
217:        // Process unsuccessful call
218:        if (!success) {
219:            // Get the revert message bytes
220:            if (returnData.length > 0) {
221:                assembly {
222:                    let returnDataSize := mload(returnData)
223:                    revert(add(32, returnData), returnDataSize)
224:                }
225:            } else {
226:                revert InitializationFailed(instance);
227:            }
228:        }
229:
230:        // Check that the created proxy instance does not violate defined limits
231:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyInstance(instance, implementation)) {
232:            revert UnverifiedProxy(instance);
233:        }
234:
235:        // Record instance params
236:        InstanceParams memory instanceParams = InstanceParams(implementation, msg.sender, true);
237:        mapInstanceParams[instance] = instanceParams;
238:        // Increase the nonce
239:        nonce = localNonce + 1;
240:
241:        emit InstanceCreated(msg.sender, instance, implementation);	// @audit-issue
```
[241](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L216-L241), 


#### Recommendation

To prevent out-of-order events and ensure consistency, always emit events before making any external calls or interactions within your smart contracts. This practice adheres to the check-effects-interaction pattern and helps provide a clear and accurate event log for event listeners. Following this approach enhances the reliability and predictability of your smart contract's behavior.

### Unnecessary Use of override Keyword
In Solidity version 0.8.8 and later, the use of the override keyword becomes superfluous when a function is overriding solely from an interface and the function isn't present in multiple base contracts. Previously, the override keyword was required as an explicit indication to the compiler. However, this is no longer the case, and the extraneous use of the keyword can make the code less clean and more verbose.
Solidity documentation on [Function Overriding](https://docs.soliditylang.org/en/v0.8.20/contracts.html#function-overriding).


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

119:    function _sendMessage(
120:        address[] memory targets,
121:        uint256[] memory stakingIncentives,
122:        bytes memory bridgePayload,
123:        uint256 transferAmount
124:    ) internal override returns (uint256 sequence) {	// @audit-issue
```
[124](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L119-L124), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

56:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {	// @audit-issue
```
[56](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L56-L56), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

53:    function _sendMessage(uint256 amount, bytes memory) internal override {	// @audit-issue
```
[53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L53-L53), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

95:    function _sendMessage(
96:        address[] memory targets,
97:        uint256[] memory stakingIncentives,
98:        bytes memory bridgePayload,
99:        uint256 transferAmount
100:    ) internal override returns (uint256 sequence) {	// @audit-issue
```
[100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L95-L100), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

89:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {	// @audit-issue

133:    function receivePayloadAndTokens(
134:        bytes memory data,
135:        TokenReceived[] memory receivedTokens,
136:        bytes32 sourceProcessor,
137:        uint16 sourceChainId,
138:        bytes32 deliveryHash
139:    ) internal override {	// @audit-issue
```
[89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L89-L89), [139](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L133-L139), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

51:    function _sendMessage(
52:        address[] memory targets,
53:        uint256[] memory stakingIncentives,
54:        bytes memory bridgePayload,
55:        uint256 transferAmount
56:    ) internal override returns (uint256 sequence) {	// @audit-issue
```
[56](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L51-L56), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

32:    function _sendMessage(uint256 amount, bytes memory) internal override {	// @audit-issue

52:    function _processMessageFromRoot(uint256, address sender, bytes memory data) internal override {	// @audit-issue

59:    function setFxRootTunnel(address l1Processor) external override {	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L32-L32), [52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L52-L52), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

59:    function _sendMessage(
60:        address[] memory targets,
61:        uint256[] memory stakingIncentives,
62:        bytes memory bridgePayload,
63:        uint256 transferAmount
64:    ) internal override returns (uint256 sequence) {	// @audit-issue

132:    function setL2TargetDispenser(address l2Dispenser) external override {	// @audit-issue

138:    function getBridgingDecimals() external pure override returns (uint256) {	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L59-L64), [132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L132-L132), [138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L138-L138), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

58:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L58-L58), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

57:    function _sendMessage(
58:        address[] memory targets,
59:        uint256[] memory stakingIncentives,
60:        bytes memory,
61:        uint256 transferAmount
62:    ) internal override returns (uint256 sequence) {	// @audit-issue

91:    function _processMessageFromChild(bytes memory data) internal override {	// @audit-issue

98:    function setFxChildTunnel(address l2Dispenser) public override {	// @audit-issue

117:    function setL2TargetDispenser(address l2Dispenser) external override {	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L57-L62), [91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L91-L91), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L98-L98), [117](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L117-L117), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

28:    function _withdraw(address to, uint256 amount) internal override {	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L28-L28), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

74:    function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {	// @audit-issue

104:    function _withdraw(address to, uint256 amount) internal override {	// @audit-issue
```
[74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L74-L74), [104](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L104-L104), 


#### Recommendation

In Solidity versions 0.8.8 and later, the `override` keyword is no longer required for functions that are solely overriding from an interface and not present in multiple base contracts. Removing the unnecessary `override` keyword can make the code cleaner and less verbose.

### Use a single file for all system-wide constants
System-wide constants should be declared in a single file for better maintainability and readability. This contract seems to contain constants which could potentially be system-wide and could be better managed if they were centralized in a single location.

```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

10:    string public constant VERSION = "1.2.0";	// @audit-issue

13:    bytes32 public constant PROXY_TOKENOMICS = 0xbd5523e7c3b6a94aa0e3b24d1120addc2f95c7029e097b466b2bedc8d4b4362f;	// @audit-issue

15:    uint256 public constant ONE_YEAR = 1 days * 365;	// @audit-issue

17:    uint256 public constant MIN_EPOCH_LENGTH = 10 days;	// @audit-issue

19:    uint256 public constant MAX_EPOCH_LENGTH = ONE_YEAR - 1 days;	// @audit-issue

21:    uint256 public constant MIN_PARAM_VALUE = 1e14;	// @audit-issue

23:    uint256 public constant MAX_STAKING_WEIGHT = 10_000;	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L10-L10), [13](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L13-L13), [15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L15-L15), [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L17-L17), [19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L19-L19), [21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L21-L21), [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L23-L23), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

275:    uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[275](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L275-L275), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

72:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 160;	// @audit-issue
```
[72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L72-L72), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

28:    bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));	// @audit-issue

30:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue

33:    uint256 public constant TOKEN_GAS_LIMIT = 300_000;	// @audit-issue

35:    uint256 public constant MESSAGE_GAS_LIMIT = 2_000_000;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L30-L30), [33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L33-L33), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L35-L35), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

39:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue
```
[39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L39-L39), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

62:    bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));	// @audit-issue

64:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue

67:    uint256 public constant GAS_LIMIT = 300_000;	// @audit-issue

70:    uint256 public constant MAX_GAS_LIMIT = 2_000_000;	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L62-L62), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L67-L67), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L70-L70), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

61:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue
```
[61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L61-L61), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

52:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L52-L52), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

34:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 32;	// @audit-issue
```
[34](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L34-L34), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

13:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue
```
[13](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L13-L13), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

28:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 32;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L28-L28), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

224:    string public constant VERSION = "0.2.0";	// @audit-issue
```
[224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L224-L224), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

23:    bytes32 public constant SERVICE_STAKING_PROXY = 0x9e5e169c1098011e4e5940a3ec1797686b2a8294a9b77a4c676b121bdc0ebb5e;	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L23-L23), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

88:    uint256 public constant SELECTOR_DATA_LENGTH = 4;	// @audit-issue
```
[88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L88-L88), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

145:    uint256 public constant WEEK = 604_800;	// @audit-issue

148:    uint256 public constant WEIGHT_VOTE_DELAY = 864_000;	// @audit-issue

155:    uint256 public constant MAX_NUM_WEEKS = 53;	// @audit-issue

157:    uint256 public constant MAX_WEIGHT = 10_000;	// @audit-issue

159:    uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[145](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L145-L145), [148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L148-L148), [155](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L155-L155), [157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L157-L157), [159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L159-L159), 


#### Recommendation

Consider centralizing system-wide constants in a single file for better maintainability and readability. This practice makes it easier to manage and update constants across the contract and promotes consistency in your codebase.

### Missing events in sensitive functions
Events should be emitted when sensitive changes are made to the contracts, but some functions lack them.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

409:    function initializeTokenomics(	// @audit-issue

990:    function trackServiceDonations(	// @audit-issue

1308:    function accountOwnerIncentives(	// @audit-issue
```
[409](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L409-L409), [990](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L990-L990), [1308](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1308-L1308), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

732:    function addNominee(bytes32 nomineeHash) external {	// @audit-issue

750:    function removeNominee(bytes32 nomineeHash) external {	// @audit-issue
```
[732](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L732-L732), [750](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L750-L750), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

106:    function receiveWormholeMessages(	// @audit-issue
```
[106](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L106-L106), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

641:    function revokeRemovedNomineeVotingPower(bytes32 account, uint256 chainId) external {	// @audit-issue
```
[641](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L641-L641), 


#### Recommendation

To enhance transparency and auditability, ensure that events are emitted when sensitive changes are made to the contracts. Review and update functions that lack event emissions, especially in cases where sensitive operations or state changes occur, to provide a clear record of such actions.

### Include sender information in events
When an action is triggered based on a user's action, not being able to filter based on who triggered the action makes event processing a lot more cumbersome. Including the `msg.sender` the events of these types of action will make events much more useful to end users, especially when `msg.sender` is not `tx.origin`.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

541:        emit TokenomicsImplementationUpdated(implementation);	// @audit-issue

558:        emit OwnerUpdated(newOwner);	// @audit-issue

574:            emit TreasuryUpdated(_treasury);	// @audit-issue

579:            emit DepositoryUpdated(_depository);	// @audit-issue

584:            emit DispenserUpdated(_dispenser);	// @audit-issue

601:            emit ComponentRegistryUpdated(_componentRegistry);	// @audit-issue

605:            emit AgentRegistryUpdated(_agentRegistry);	// @audit-issue

609:            emit ServiceRegistryUpdated(_serviceRegistry);	// @audit-issue

623:        emit DonatorBlacklistUpdated(_donatorBlacklist);	// @audit-issue

692:        emit TokenomicsParametersUpdateRequested(epochCounter + 1, _devsPerCapital, _codePerDev, _epsilonRate, _epochLen,	// @audit-issue

744:        emit IncentiveFractionsUpdateRequested(eCounter, _rewardComponentFraction, _rewardAgentFraction,	// @audit-issue

779:        emit StakingParamsUpdateRequested(eCounter, _maxStakingIncentive, _minStakingWeight);	// @audit-issue

801:            emit EffectiveBondUpdated(epochCounter, eBond);	// @audit-issue

821:        emit EffectiveBondUpdated(epochCounter, eBond);	// @audit-issue

840:        emit StakingRefunded(eCounter, amount);	// @audit-issue

1189:            emit TokenomicsParametersUpdated(eCounter + 1);	// @audit-issue

1196:            emit IncentiveFractionsUpdated(eCounter + 1);	// @audit-issue

1213:            emit StakingParamsUpdated(eCounter + 1);	// @audit-issue

1278:            emit IDFUpdated(idf);	// @audit-issue

1287:            emit EpochSettled(eCounter, incentives[1], accountRewards, accountTopUps, curMaxBond, incentives[7],	// @audit-issue
```
[541](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L541-L541), [558](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L558-L558), [574](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L574-L574), [579](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L579-L579), [584](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L584-L584), [601](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L601-L601), [605](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L605-L605), [609](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L609-L609), [623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L623-L623), [692](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L692-L692), [744](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L744-L744), [779](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L779-L779), [801](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L801-L801), [821](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L821-L821), [840](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L840-L840), [1189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1189-L1189), [1196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1196-L1196), [1213](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1213-L1213), [1278](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1278-L1278), [1287](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1287-L1287), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

648:        emit OwnerUpdated(newOwner);	// @audit-issue

664:            emit TokenomicsUpdated(_tokenomics);	// @audit-issue

670:            emit TreasuryUpdated(_treasury);	// @audit-issue

676:            emit VoteWeightingUpdated(_voteWeighting);	// @audit-issue

697:        emit StakingParamsUpdated(_maxNumClaimingEpochs, _maxNumStakingTargets);	// @audit-issue

727:        emit SetDepositProcessorChainIds(depositProcessors, chainIds);	// @audit-issue

1191:        emit WithheldAmountSynced(chainId, amount, withheldAmount);	// @audit-issue

1236:        emit WithheldAmountSynced(chainId, amount, withheldAmount);	// @audit-issue

1249:        emit PauseDispenser(pauseState);	// @audit-issue
```
[648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L648-L648), [664](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L664-L664), [670](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L670-L670), [676](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L676-L676), [697](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L697-L697), [727](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L727-L727), [1191](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1191-L1191), [1236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1236-L1236), [1249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1249-L1249), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

118:        emit MessageReceived(l2TargetDispenser, l2TargetChainId, data);	// @audit-issue

155:        emit MessagePosted(sequence, targets, stakingIncentives, transferAmount);	// @audit-issue

181:        emit MessagePosted(sequence, targets, stakingIncentives, transferAmount);	// @audit-issue

201:        emit L2TargetDispenserUpdated(l2Dispenser);	// @audit-issue
```
[118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L118-L118), [155](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L155-L155), [181](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L181-L181), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L201-L201), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

114:                emit AmountRefunded(target, refundAmount);	// @audit-issue

121:            emit StakingTargetDeposited(target, amount);	// @audit-issue
```
[114](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L114-L114), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L121-L121), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

73:        emit FxRootTunnelUpdated(l1Processor);	// @audit-issue
```
[73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L73-L73), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

173:                emit AmountWithheld(target, amount);	// @audit-issue

185:                emit AmountWithheld(target, targetWithheldAmount);	// @audit-issue

194:                emit StakingTargetDeposited(target, amount);	// @audit-issue

201:                emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);	// @audit-issue

239:        emit MessageReceived(l1DepositProcessor, l1SourceChainId, data);	// @audit-issue

259:        emit OwnerUpdated(newOwner);	// @audit-issue

293:            emit StakingTargetDeposited(target, amount);	// @audit-issue

360:        emit TargetDispenserPaused();	// @audit-issue

371:        emit TargetDispenserUnpaused();	// @audit-issue
```
[173](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L173-L173), [185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L185-L185), [194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L194-L194), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L201-L201), [239](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L239-L239), [259](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L259-L259), [293](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L293-L293), [360](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L360-L360), [371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L371-L371), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

112:        emit FxChildTunnelUpdated(l2Dispenser);	// @audit-issue
```
[112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L112-L112), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

108:        emit OwnerUpdated(newOwner);	// @audit-issue

121:        emit SetImplementationsCheck(setCheck);	// @audit-issue

160:        emit ImplementationsWhitelistUpdated(implementations, statuses, setCheck);	// @audit-issue

249:        emit StakingLimitsUpdated(_rewardsPerSecondLimit, _timeForEmissionsLimit, _numServicesLimit, emissionsLimit);	// @audit-issue
```
[108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L108-L108), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L121-L121), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L160-L160), [249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L249-L249), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

475:        emit ServicesEvicted(epochCounter, serviceIds, owners, multisigs, inactivity);	// @audit-issue

687:                        emit ServiceInactivityWarning(eCounter, curServiceId, serviceInactivity[i]);	// @audit-issue

708:            emit Checkpoint(eCounter, lastAvailableRewards, finalEligibleServiceIds, finalEligibleServiceRewards,	// @audit-issue
```
[475](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L475-L475), [687](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L687-L687), [708](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L708-L708), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

110:        emit Withdraw(to, amount);	// @audit-issue
```
[110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L110-L110), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

122:        emit OwnerUpdated(newOwner);	// @audit-issue

134:        emit VerifierUpdated(newVerifier);	// @audit-issue

261:        emit InstanceStatusChanged(instance, isEnabled);	// @audit-issue
```
[122](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L122-L122), [134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L134-L134), [261](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L261-L261), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

318:        emit AddNominee(nominee.account, nominee.chainId, id);	// @audit-issue

380:        emit OwnerUpdated(newOwner);	// @audit-issue

393:        emit DispenserUpdated(newDispenser);	// @audit-issue

635:        emit RemoveNominee(account, chainId, newSum);	// @audit-issue
```
[318](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L318-L318), [380](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L380-L380), [393](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L393-L393), [635](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L635-L635), 


#### Recommendation

To improve the usability and analysis of your smart contract events, consider including the `msg.sender` address as part of the event data. This enables easier filtering and identification of the sender's actions within your contract, providing valuable insights for users and external tools.

### Interfaces should be defined in separate files from their usage
This issue arises when the interfaces are defined in the same files where they are used. They should be separated into different files for better readability and reusability.

In Solidity, interfaces are used to interact with contract abstractions. They define the functions and events that other contracts can call or listen to. While it is possible to define an interface within the same file where it is used, it is considered a good practice to separate the interface definition into its own file.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

10:interface IOLAS {	// @audit-issue used in lines: [462]

17:interface IToken {	// @audit-issue used in lines: [910, 967, 1330, 1348, 1402, 1420]

29:interface ITreasury {	// @audit-issue used in lines: [1285]

37:interface IServiceRegistry {	// @audit-issue used in lines: [918, 919, 1012]

58:interface IVotingEscrow {	// @audit-issue used in lines: [911, 912]
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L10-L10), [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L17-L17), [29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L29-L29), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L37-L37), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L58-L58), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

5:interface IDepositProcessor {	// @audit-issue used in lines: [425, 429, 490, 494, 596, 990, 1217]

44:interface IToken {	// @audit-issue used in lines: [420, 456, 815, 822, 1028, 1034, 1106, 1112]

58:interface ITokenomics {	// @audit-issue used in lines: [361, 745, 762, 766, 769, 807, 884, 886, 1001, 1099, 1148, 1151, 1162]

142:interface ITreasury {	// @audit-issue used in lines: [741, 802, 818, 984, 1031, 1081, 1109]

160:interface IVoteWeighting {	// @audit-issue used in lines: [346, 871, 878, 893, 1154]
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L5-L5), [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L44-L44), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L58-L58), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L142-L142), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L160-L160), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue used in lines: [182, 190, 203]
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue used in lines: [89, 98]
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

6:interface IDispenser {	// @audit-issue used in lines: [124]
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue used in lines: [58]
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue used in lines: [114, 140, 150]
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

4:interface IToken {	// @audit-issue used in lines: [112, 118]

18:interface IStaking {	// @audit-issue used in lines: [119]

24:interface IStakingFactory {	// @audit-issue used in lines: [99]
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L4-L4), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L18-L18), [24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L24-L24), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

7:interface IBridge {	// @audit-issue used in lines: [112, 120]
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L7-L7), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue used in lines: [68, 90, 100]
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue used in lines: [80, 89]
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

7:interface IStaking {	// @audit-issue used in lines: [192, 291]

14:interface IStakingFactory {	// @audit-issue used in lines: [160]

22:interface IToken {	// @audit-issue used in lines: [189, 191, 287, 290, 440, 443]
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L14-L14), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L22-L22), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

7:interface IBridge {	// @audit-issue used in lines: [71]
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L7-L7), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

5:interface IStaking {	// @audit-issue used in lines: [192, 199, 206]
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L5-L5), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

5:interface IMultisig {	// @audit-issue used in lines: [38]
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L5-L5), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

7:interface IActivityChecker {	// @audit-issue used in lines: [406, 416, 789]

30:interface IService {	// @audit-issue used in lines: [379, 739, 755, 797, 864]
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L7-L7), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L30-L30), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

11:interface IServiceTokenUtility {	// @audit-issue used in lines: [77, 93]
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L11-L11), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

7:interface IStaking {	// @audit-issue used in lines: [300]

14:interface IStakingVerifier {	// @audit-issue used in lines: [195, 231, 285, 306]
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L14-L14), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

5:interface IDispenser {	// @audit-issue used in lines: [315, 632]

16:interface IVEOLAS {	// @audit-issue used in lines: [483, 488]
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L5-L5), [16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L16-L16), 


#### Recommendation

To improve code organization and maintainability, it's recommended to define interfaces in separate files from their usage. This separation of concerns makes your codebase more modular and easier to understand. Create dedicated files for interface definitions, and then import and use these interfaces in the contracts that require them.

### Control structures do not follow the Solidity Style Guide
Refer to the [Solidity style guide - Control Structures](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#control-structures).

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

377:    constructor()	// @audit-issue
```
[377](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L377-L377), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

89:    constructor(
90:        address _olas,
91:        address _l1Dispenser,
92:        address _l1TokenRelayer,
93:        address _l1MessageRelayer,
94:        uint256 _l2TargetChainId,
95:        address _l1ERC20Gateway,
96:        address _outbox,
97:        address _bridge
98:    )	// @audit-issue
```
[98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L89-L98), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

36:    constructor(
37:        address _olas,
38:        address _proxyFactory,
39:        address _l2MessageRelayer,
40:        address _l1DepositProcessor,
41:        uint256 _l1SourceChainId
42:    )	// @audit-issue
```
[42](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L36-L42), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

76:    constructor(
77:        address _olas,
78:        address _l1Dispenser,
79:        address _l1TokenRelayer,
80:        address _l1MessageRelayer,
81:        uint256 _l2TargetChainId,
82:        address _olasL2
83:    )	// @audit-issue
```
[83](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L76-L83), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

64:    constructor(
65:        address _olas,
66:        address _proxyFactory,
67:        address _l2MessageRelayer,
68:        address _l1DepositProcessor,
69:        uint256 _l1SourceChainId,
70:        address _wormholeCore,
71:        address _l2TokenRelayer
72:    )	// @audit-issue
```
[72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L64-L72), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

20:    constructor(
21:        address _olas,
22:        address _proxyFactory,
23:        address _l2MessageRelayer,
24:        address _l1DepositProcessor,
25:        uint256 _l1SourceChainId
26:    )	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L20-L26), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

28:    constructor(
29:        address _olas,
30:        address _l1Dispenser,
31:        address _l1TokenRelayer,
32:        address _l1MessageRelayer,
33:        uint256 _l2TargetChainId,
34:        address _wormholeCore,
35:        uint256 _wormholeTargetChainId
36:    )	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L28-L36), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

39:    constructor(
40:        address _olas,
41:        address _proxyFactory,
42:        address _l2MessageRelayer,
43:        address _l1DepositProcessor,
44:        uint256 _l1SourceChainId,
45:        address _l2TokenRelayer
46:    )	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L39-L46), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

36:    constructor(
37:        address _olas,
38:        address _l1Dispenser,
39:        address _l1TokenRelayer,
40:        address _l1MessageRelayer,
41:        uint256 _l2TargetChainId,
42:        address _checkpointManager,
43:        address _predicate
44:    )	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L36-L44), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

398:    function _checkRatioPass(
399:        address multisig,
400:        uint256[] memory lastNonces,
401:        uint256 ts
402:    ) internal view returns (bool ratioPass, uint256[] memory currentNonces)	// @audit-issue

522:    function _calculateStakingRewards() internal view returns (
523:        uint256 lastAvailableRewards,
524:        uint256 numServices,
525:        uint256 totalRewards,
526:        uint256[] memory eligibleServiceIds,
527:        uint256[] memory eligibleServiceRewards,
528:        uint256[] memory serviceIds,
529:        uint256[][] memory serviceNonces,
530:        uint256[] memory serviceInactivity
531:    )	// @audit-issue

591:    function checkpoint() public returns (
592:        uint256[] memory,
593:        uint256[][] memory,
594:        uint256[] memory,
595:        uint256[] memory,
596:        uint256[] memory evictServiceIds
597:    )	// @audit-issue
```
[402](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L398-L402), [531](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L522-L531), [597](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L591-L597), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

54:    function initialize(
55:        StakingParams memory _stakingParams,
56:        address _serviceRegistryTokenUtility,
57:        address _stakingToken
58:    ) external	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L54-L58), 


#### Recommendation

Adhere to the Solidity style guide regarding control structures by avoiding the definition of multiple functions with identical names in a contract. Unique and descriptive function names improve code clarity and prevent potential confusion or errors. Consult [Solidity style guide - Control Structures](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#control-structures) for best practices.

### Add inline comments for unnamed variables
In Solidity, it's not uncommon to encounter functions with unnamed parameters, especially when certain arguments are not used within the function body. While this is syntactically valid, it can lead to confusion and a lack of clarity about the function's intent and design. For better readability and maintainability, it's beneficial to include inline comments for these unnamed parameters. This practice provides context to other developers or auditors, clarifying the purpose or the reason for the exclusion of these parameters. For instance, transforming `function foo(address x, address)` to `function foo(address x, address /* unused */)`` or `function foo(address x, address /* y */)``. This small change can significantly enhance the understandability of the contract.

```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

53:    function _sendMessage(uint256 amount, bytes memory) internal override {	// @audit-issue: Need inline comments for unnamed parameter.
```
[53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L53-L53), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

130:    function sendMessage(
131:        address target,
132:        uint256 stakingIncentive,
133:        bytes memory,	// @audit-issue: Need inline comments for unnamed parameter.
134:        uint256
135:    ) external {

130:    function sendMessage(
131:        address target,
132:        uint256 stakingIncentive,
133:        bytes memory,
134:        uint256	// @audit-issue: Need inline comments for unnamed parameter.
135:    ) external {

155:    function sendMessageBatch(
156:        address[] memory targets,
157:        uint256[] memory stakingIncentives,
158:        bytes memory,	// @audit-issue: Need inline comments for unnamed parameter.
159:        uint256
160:    ) external {

155:    function sendMessageBatch(
156:        address[] memory targets,
157:        uint256[] memory stakingIncentives,
158:        bytes memory,
159:        uint256	// @audit-issue: Need inline comments for unnamed parameter.
160:    ) external {
```
[133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L130-L135), [134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L130-L135), [158](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L155-L160), [159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L155-L160), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

32:    function _sendMessage(uint256 amount, bytes memory) internal override {	// @audit-issue: Need inline comments for unnamed parameter.

52:    function _processMessageFromRoot(uint256, address sender, bytes memory data) internal override {	// @audit-issue: Need inline comments for unnamed parameter.
```
[32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L32-L32), [52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L52-L52), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

106:    function receiveWormholeMessages(
107:        bytes memory data,
108:        bytes[] memory,	// @audit-issue: Need inline comments for unnamed parameter.
109:        bytes32 sourceAddress,
110:        uint16 sourceChain,
111:        bytes32 deliveryHash
112:    ) external {
```
[108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L106-L112), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

99:    function onTokenBridged(address, uint256, bytes calldata data) external {	// @audit-issue: Need inline comments for unnamed parameter.
```
[99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L99-L99), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

57:    function _sendMessage(
58:        address[] memory targets,
59:        uint256[] memory stakingIncentives,
60:        bytes memory,	// @audit-issue: Need inline comments for unnamed parameter.
61:        uint256 transferAmount
62:    ) internal override returns (uint256 sequence) {
```
[60](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L57-L62), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

255:    function getEmissionsAmountLimit(address) external view returns (uint256) {	// @audit-issue: Need inline comments for unnamed parameter.
```
[255](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L255-L255), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

366:    function _checkTokenStakingDeposit(
367:        uint256 serviceId,
368:        uint256 stakingDeposit,
369:        uint32[] memory	// @audit-issue: Need inline comments for unnamed parameter.
370:    ) internal view virtual {
```
[369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L366-L370), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

74:    function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {	// @audit-issue: Need inline comments for unnamed parameter.
```
[74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L74-L74), 


#### Recommendation

Incorporate inline comments for unnamed parameters in Solidity function definitions. This enhances readability and provides clarity on the function's design. For example, change `function foo(address x, address)` to `function foo(address x, address /* unused */)` or `function foo(address x, address /* y */)` to clearly indicate the purpose or the intentional omission of these parameters.

### Consider making contracts `Upgradeable`
This allows for bugs to be fixed in production, at the expense of significantly increasing centralization.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

254:contract Tokenomics is TokenomicsConstants {	// @audit-issue
```
[254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L254-L254), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

253:contract Dispenser {	// @audit-issue
```
[253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L253-L253), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

70:contract ArbitrumDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L70-L70), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

37:contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L37-L37), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

23:contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L23-L23), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

59:contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

50:contract EthereumDepositProcessor {	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

50:contract WormholeTargetDispenserL2 is DefaultTargetDispenserL2, TokenReceiver {	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

32:contract GnosisDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L32-L32), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

11:contract PolygonTargetDispenserL2 is DefaultTargetDispenserL2, FxBaseChildTunnel {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

11:contract WormholeDepositProcessorL1 is DefaultDepositProcessorL1, TokenSender {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

26:contract GnosisTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L26-L26), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

22:contract PolygonDepositProcessorL1 is DefaultDepositProcessorL1, FxBaseRootTunnel {	// @audit-issue
```
[22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

43:contract StakingVerifier {	// @audit-issue
```
[43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L43-L43), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

17:contract StakingNativeToken is StakingBase {	// @audit-issue
```
[17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L17-L17), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

18:contract StakingActivityChecker {	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L18-L18), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

44:contract StakingToken is StakingBase {	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L44-L44), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

21:contract StakingProxy {	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L21-L21), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

81:contract StakingFactory {	// @audit-issue
```
[81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L81-L81), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

132:contract VoteWeighting {	// @audit-issue
```
[132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L132-L132), 


#### Recommendation

Assess the need for upgradeability in your Solidity contracts based on the project's requirements and lifecycle. If chosen, implement a well-known proxy pattern ensuring rigorous security and governance mechanisms are in place. Be aware of the increased centralization and plan accordingly to mitigate potential risks, such as through decentralized governance models or multi-sig control for upgrade decisions.

### Events that mark critical parameter changes should contain both the old and the new value
This should especially be done if the new value is not required to be different from the old value

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

558:        emit OwnerUpdated(newOwner);	// @audit-issue

574:            emit TreasuryUpdated(_treasury);	// @audit-issue

579:            emit DepositoryUpdated(_depository);	// @audit-issue

584:            emit DispenserUpdated(_dispenser);	// @audit-issue

601:            emit ComponentRegistryUpdated(_componentRegistry);	// @audit-issue

605:            emit AgentRegistryUpdated(_agentRegistry);	// @audit-issue

609:            emit ServiceRegistryUpdated(_serviceRegistry);	// @audit-issue

623:        emit DonatorBlacklistUpdated(_donatorBlacklist);	// @audit-issue

692:        emit TokenomicsParametersUpdateRequested(epochCounter + 1, _devsPerCapital, _codePerDev, _epsilonRate, _epochLen,	// @audit-issue

1189:            emit TokenomicsParametersUpdated(eCounter + 1);	// @audit-issue

1196:            emit IncentiveFractionsUpdated(eCounter + 1);	// @audit-issue

1213:            emit StakingParamsUpdated(eCounter + 1);	// @audit-issue
```
[558](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L558-L558), [574](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L574-L574), [579](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L579-L579), [584](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L584-L584), [601](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L601-L601), [605](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L605-L605), [609](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L609-L609), [623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L623-L623), [692](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L692-L692), [1189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1189-L1189), [1196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1196-L1196), [1213](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1213-L1213), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

648:        emit OwnerUpdated(newOwner);	// @audit-issue

664:            emit TokenomicsUpdated(_tokenomics);	// @audit-issue

670:            emit TreasuryUpdated(_treasury);	// @audit-issue

676:            emit VoteWeightingUpdated(_voteWeighting);	// @audit-issue

727:        emit SetDepositProcessorChainIds(depositProcessors, chainIds);	// @audit-issue

1191:        emit WithheldAmountSynced(chainId, amount, withheldAmount);	// @audit-issue

1236:        emit WithheldAmountSynced(chainId, amount, withheldAmount);	// @audit-issue

1249:        emit PauseDispenser(pauseState);	// @audit-issue
```
[648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L648-L648), [664](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L664-L664), [670](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L670-L670), [676](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L676-L676), [727](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L727-L727), [1191](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1191-L1191), [1236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1236-L1236), [1249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1249-L1249), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

73:        emit FxRootTunnelUpdated(l1Processor);	// @audit-issue
```
[73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L73-L73), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

259:        emit OwnerUpdated(newOwner);	// @audit-issue
```
[259](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L259-L259), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

112:        emit FxChildTunnelUpdated(l2Dispenser);	// @audit-issue
```
[112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L112-L112), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

108:        emit OwnerUpdated(newOwner);	// @audit-issue

121:        emit SetImplementationsCheck(setCheck);	// @audit-issue
```
[108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L108-L108), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L121-L121), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

687:                        emit ServiceInactivityWarning(eCounter, curServiceId, serviceInactivity[i]);	// @audit-issue

708:            emit Checkpoint(eCounter, lastAvailableRewards, finalEligibleServiceIds, finalEligibleServiceRewards,	// @audit-issue
```
[687](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L687-L687), [708](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L708-L708), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

122:        emit OwnerUpdated(newOwner);	// @audit-issue

134:        emit VerifierUpdated(newVerifier);	// @audit-issue
```
[122](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L122-L122), [134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L134-L134), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

380:        emit OwnerUpdated(newOwner);	// @audit-issue

393:        emit DispenserUpdated(newDispenser);	// @audit-issue

635:        emit RemoveNominee(account, chainId, newSum);	// @audit-issue
```
[380](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L380-L380), [393](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L393-L393), [635](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L635-L635), 


#### Recommendation

To enhance transparency and auditability, ensure that events are emitted when sensitive changes are made to the contracts. Review and update functions that lack event emissions, especially in cases where sensitive operations or state changes occur, to provide a clear record of such actions.

### Consider using descriptive `constants` when passing zero as a function argument
Passing zero as a function argument can sometimes result in a security issue (e.g. passing zero as the slippage parameter). Consider using a `constant` variable with a descriptive name, so it's clear that the argument is intentionally being used, and for the right reasons.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

473:        uint256 _inflationPerSecond = getInflationForYear(0) / zeroYearSecondsLeft;	// @audit-issue
```
[473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L473-L473), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

1031:                ITreasury(treasury).withdrawToAccount(address(this), 0, transferAmount);	// @audit-issue

1109:                ITreasury(treasury).withdrawToAccount(address(this), 0, totalAmounts[1]);	// @audit-issue
```
[1031](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1031-L1031), [1109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1109-L1109), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

91:        emit MessagePosted(0, msg.sender, l1DepositProcessor, amount);	// @audit-issue
```
[91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L91-L91), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

112:        (uint256 cost, ) = IBridge(l2MessageRelayer).quoteEVMDeliveryPrice(uint16(l1SourceChainId), 0, gasLimitMessage);	// @audit-issue
```
[112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L112-L112), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

41:        emit MessagePosted(0, msg.sender, l1DepositProcessor, amount);	// @audit-issue
```
[41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L41-L41), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

96:        sequence = sendTokenWithPayloadToEvm(uint16(wormholeTargetChainId), l2TargetDispenser, data, 0,	// @audit-issue
97:            gasLimitMessage, olas, transferAmount, uint16(l2TargetChainId), refundAccount);
```
[96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L96-L97), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

216:        setNominees.push(Nominee(0, 0));	// @audit-issue

218:        setRemovedNominees.push(Nominee(0, 0));	// @audit-issue
```
[216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L216-L216), [218](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L218-L218), 


#### Recommendation

Replace direct usage of zero as a function argument with a well-named `constant`. For example, use something like `uint256 constant NO_SLIPPAGE = 0;` when zero is intended to signify 'no slippage'. This approach enhances code readability, reduces ambiguity, and helps ensure that the function is used correctly and for its intended purpose.

### Non-`external`/`public` function names should begin with an underscore
According to the Solidity Style Guide, Non-external/public function names should begin with an [underscore](https://docs.soliditylang.org/en/latest/style-guide.html#underscore-prefix-for-non-external-functions-and-variables)


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

22:    function safeTransferFrom(address token, address from, address to, uint256 amount) internal {	// @audit-issue

64:    function safeTransfer(address token, address to, uint256 amount) internal {	// @audit-issue
```
[22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L22-L22), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L64-L64), 


#### Recommendation

To adhere to the Solidity Style Guide, consider prefixing the names of non-`external`/`public` functions with an underscore (_). This naming convention enhances code readability and helps distinguish the visibility of functions.

### Variable names for `immutable`s should use CONSTANT_CASE
For `immutable` variable names, each word should use all capital letters, with underscores separating each word (CONSTANT_CASE)

```solidity
Path: ./tokenomics/contracts/Dispenser.sol

277:    address public immutable olas;	// @audit-issue name should be: OLAS

279:    bytes32 public immutable retainer;	// @audit-issue name should be: RETAINER

281:    bytes32 public immutable retainerHash;	// @audit-issue name should be: RETAINER_HASH
```
[277](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L277-L277), [279](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L279-L279), [281](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L281-L281), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

74:    address public immutable l1ERC20Gateway;	// @audit-issue name should be: L_1ERC20G_ATEWAY

76:    address public immutable outbox;	// @audit-issue name should be: OUTBOX

78:    address public immutable bridge;	// @audit-issue name should be: BRIDGE
```
[74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L74-L74), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L78-L78), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

37:    address public immutable olas;	// @audit-issue name should be: OLAS

39:    address public immutable l1Dispenser;	// @audit-issue name should be: L_1D_ISPENSER

41:    address public immutable l1TokenRelayer;	// @audit-issue name should be: L_1T_OKEN_RELAYER

43:    address public immutable l1MessageRelayer;	// @audit-issue name should be: L_1M_ESSAGE_RELAYER

45:    uint256 public immutable l2TargetChainId;	// @audit-issue name should be: L_2T_ARGET_CHAIN_ID
```
[37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L37-L37), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L39-L39), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L41-L41), [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L43-L43), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L45-L45), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

25:    address public immutable l1AliasedDepositProcessor;	// @audit-issue name should be: L_1A_LIASED_DEPOSIT_PROCESSOR
```
[25](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L25-L25), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

63:    address public immutable olasL2;	// @audit-issue name should be: OLAS_L_2
```
[63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L63-L63), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

55:    address public immutable olas;	// @audit-issue name should be: OLAS

57:    address public immutable dispenser;	// @audit-issue name should be: DISPENSER

59:    address public immutable stakingFactory;	// @audit-issue name should be: STAKING_FACTORY

61:    address public immutable timelock;	// @audit-issue name should be: TIMELOCK
```
[55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L55-L55), [57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L57-L57), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L59-L59), [61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L61-L61), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

15:    uint256 public immutable wormholeTargetChainId;	// @audit-issue name should be: WORMHOLE_TARGET_CHAIN_ID
```
[15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L15-L15), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

30:    address public immutable l2TokenRelayer;	// @audit-issue name should be: L_2T_OKEN_RELAYER
```
[30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L30-L30), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

72:    address public immutable olas;	// @audit-issue name should be: OLAS

74:    address public immutable stakingFactory;	// @audit-issue name should be: STAKING_FACTORY

76:    address public immutable l2MessageRelayer;	// @audit-issue name should be: L_2M_ESSAGE_RELAYER

78:    address public immutable l1DepositProcessor;	// @audit-issue name should be: L_1D_EPOSIT_PROCESSOR

80:    uint256 public immutable l1SourceChainId;	// @audit-issue name should be: L_1S_OURCE_CHAIN_ID
```
[72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L72-L72), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L74-L74), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L78-L78), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L80-L80), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

26:    address public immutable predicate;	// @audit-issue name should be: PREDICATE
```
[26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L26-L26), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

51:    address public immutable olas;	// @audit-issue name should be: OLAS
```
[51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L51-L51), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

20:    uint256 public immutable livenessRatio;	// @audit-issue name should be: LIVENESS_RATIO
```
[20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L20-L20), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

161:    address public immutable ve;	// @audit-issue name should be: VE
```
[161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L161-L161), 


#### Recommendation

When naming `immutable` variables, follow the CONSTANT_CASE convention, which means using all capital letters with underscores to separate words. This naming convention improves code readability and aligns with best practices for naming constants.

### Employ Explicit Casting to Bytes or Bytes32 for Enhanced Code Clarity and Meaning
Smart contracts are complex entities, and clarity in their operations is fundamental to ensure that they function as intended. Casting a single argument instead of utilizing 'abi.encodePacked()' improves the transparency of the operation. It elucidates the intent of the code, reducing ambiguity and making it easier for auditors and developers to understand the code’s purpose. Such practices promote readability and maintainability, thus reducing the likelihood of errors and misunderstandings. Therefore, it's recommended to employ explicit casts for single arguments where possible, to increase the contract's comprehensibility and ensure a smoother review process.

```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

143:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue

146:        bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,	// @audit-issue
147:            uint256(uint160(implementation)));

151:            abi.encodePacked(	// @audit-issue
152:                bytes1(0xff), address(this), salt, keccak256(deploymentData)
153:            )

201:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue

203:        bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,	// @audit-issue
204:            uint256(uint160(implementation)));
```
[143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L143-L143), [146](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L146-L147), [151](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L151-L153), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L201-L201), [203](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L203-L204), 


#### Recommendation

Consider using `bytes.concat()` instead of `abi.encodePacked()` for concatenating `bytes` for clearer semantic meaning, especially in Solidity versions 0.8.4 and later.

### Revert statements within external and public functions can be used to perform DOS attacks
In Solidity, `revert` statements are used to undo changes and throw an exception when certain conditions are not met. However, in public and external functions, improper use of `revert` can be exploited for Denial of Service (DoS) attacks. An attacker can intentionally trigger these `revert' conditions, causing legitimate transactions to consistently fail. For example, if a function relies on specific conditions from user input or contract state, an attacker could manipulate these to continually force `revert`s, blocking the function's execution. Therefore, it's crucial to design contract logic to handle exceptions properly and avoid scenarios where `revert` can be predictably triggered by malicious actors. This includes careful input validation and considering alternative design patterns that are less susceptible to such abuses.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

423:            revert AlreadyInitialized();	// @audit-issue

430:            revert ZeroAddress();	// @audit-issue

441:            revert LowerThan(_epochLen, MIN_EPOCH_LENGTH);	// @audit-issue

446:            revert Overflow(_epochLen, MAX_EPOCH_LENGTH);	// @audit-issue

465:            revert Overflow(_timeLaunch + ONE_YEAR, block.timestamp);	// @audit-issue

529:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

534:            revert ZeroAddress();	// @audit-issue

549:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

554:            revert ZeroAddress();	// @audit-issue

568:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

595:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

619:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

648:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

713:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

718:            revert WrongAmount(_rewardComponentFraction + _rewardAgentFraction, 100);	// @audit-issue

725:            revert WrongAmount(sumTopUpFractions, 100);	// @audit-issue

754:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

759:            revert ZeroValue();	// @audit-issue

764:            revert Overflow(_maxStakingIncentive, type(uint96).max);	// @audit-issue

768:            revert Overflow(_minStakingWeight, MAX_STAKING_WEIGHT);	// @audit-issue

790:            revert ManagerOnly(msg.sender, depository);	// @audit-issue

811:            revert ManagerOnly(msg.sender, depository);	// @audit-issue

818:            revert Overflow(eBond, type(uint96).max);	// @audit-issue

829:            revert ManagerOnly(msg.sender, depository);	// @audit-issue

836:            revert Overflow(stakingIncentive, type(uint96).max);	// @audit-issue

998:            revert ManagerOnly(msg.sender, treasury);	// @audit-issue

1004:            revert DonatorBlacklisted(donator);	// @audit-issue

1013:                revert ServiceDoesNotExist(serviceIds[i]);	// @audit-issue

1088:            revert DelegatecallOnly();	// @audit-issue

1093:            revert SameBlockNumberViolation();	// @audit-issue

1293:            revert TreasuryRebalanceFailed(eCounter);	// @audit-issue

1315:            revert ManagerOnly(msg.sender, dispenser);	// @audit-issue

1320:            revert WrongArrayLength(unitTypes.length, unitIds.length);	// @audit-issue

1338:                revert Overflow(unitTypes[i], 1);	// @audit-issue

1343:                revert WrongUnitId(unitIds[i], unitTypes[i]);	// @audit-issue

1350:                revert OwnerOnly(unitOwner, account);	// @audit-issue

1392:            revert WrongArrayLength(unitTypes.length, unitIds.length);	// @audit-issue

1410:                revert Overflow(unitTypes[i], 1);	// @audit-issue

1415:                revert WrongUnitId(unitIds[i], unitTypes[i]);	// @audit-issue

1422:                revert OwnerOnly(unitOwner, account);	// @audit-issue
```
[423](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L423-L423), [430](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L430-L430), [441](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L441-L441), [446](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L446-L446), [465](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L465-L465), [529](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L529-L529), [534](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L534-L534), [549](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L549-L549), [554](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L554-L554), [568](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L568-L568), [595](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L595-L595), [619](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L619-L619), [648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L648-L648), [713](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L713-L713), [718](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L718-L718), [725](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L725-L725), [754](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L754-L754), [759](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L759-L759), [764](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L764-L764), [768](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L768-L768), [790](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L790-L790), [811](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L811-L811), [818](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L818-L818), [829](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L829-L829), [836](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L836-L836), [998](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L998-L998), [1004](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1004-L1004), [1013](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1013-L1013), [1088](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1088-L1088), [1093](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1093-L1093), [1293](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1293-L1293), [1315](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1315-L1315), [1320](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1320-L1320), [1338](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1338-L1338), [1343](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1343-L1343), [1350](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1350-L1350), [1392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1392-L1392), [1410](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1410-L1410), [1415](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1415-L1415), [1422](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1422-L1422), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

639:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

644:            revert ZeroAddress();	// @audit-issue

658:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

686:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

691:            revert ZeroValue();	// @audit-issue

708:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

713:            revert WrongArrayLength(depositProcessors.length, chainIds.length);	// @audit-issue

720:                revert ZeroValue();	// @audit-issue

735:            revert ManagerOnly(msg.sender, voteWeighting);	// @audit-issue

742:            revert Paused();	// @audit-issue

753:            revert ManagerOnly(msg.sender, voteWeighting);	// @audit-issue

758:            revert WrongAccount(retainer);	// @audit-issue

775:            revert Overflow(block.timestamp, maxAllowedTime);	// @audit-issue

795:            revert ReentrancyGuard();	// @audit-issue

803:            revert Paused();	// @audit-issue

824:                    revert WrongAmount(olasBalance, topUp);	// @audit-issue

831:            revert ClaimIncentivesFailed(msg.sender, reward, topUp);	// @audit-issue

862:            revert ZeroValue();	// @audit-issue

867:            revert ZeroAddress();	// @audit-issue

961:            revert ReentrancyGuard();	// @audit-issue

967:            revert ZeroValue();	// @audit-issue

972:            revert ZeroAddress();	// @audit-issue

978:            revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);	// @audit-issue

985:            revert Paused();	// @audit-issue

1036:                    revert WrongAmount(olasBalance, transferAmount);	// @audit-issue

1067:            revert ReentrancyGuard();	// @audit-issue

1076:            revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);	// @audit-issue

1082:            revert Paused();	// @audit-issue

1114:                    revert WrongAmount(olasBalance, totalAmounts[1]);	// @audit-issue

1132:            revert ReentrancyGuard();	// @audit-issue

1179:            revert DepositProcessorOnly(msg.sender, depositProcessor);	// @audit-issue

1185:            revert Overflow(withheldAmount, type(uint96).max);	// @audit-issue

1202:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

1207:            revert ZeroValue();	// @audit-issue

1212:            revert WrongChainId(chainId);	// @audit-issue

1230:            revert Overflow(withheldAmount, type(uint96).max);	// @audit-issue

1244:            revert OwnerOnly(msg.sender, owner);	// @audit-issue
```
[639](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L639-L639), [644](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L644-L644), [658](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L658-L658), [686](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L686-L686), [691](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L691-L691), [708](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L708-L708), [713](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L713-L713), [720](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L720-L720), [735](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L735-L735), [742](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L742-L742), [753](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L753-L753), [758](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L758-L758), [775](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L775-L775), [795](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L795-L795), [803](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L803-L803), [824](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L824-L824), [831](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L831-L831), [862](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L862-L862), [867](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L867-L867), [961](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L961-L961), [967](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L967-L967), [972](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L972-L972), [978](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L978-L978), [985](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L985-L985), [1036](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1036-L1036), [1067](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1067-L1067), [1076](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1076-L1076), [1082](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1082-L1082), [1114](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1114-L1114), [1132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1132-L1132), [1179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1179-L1179), [1185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1185-L1185), [1202](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1202-L1202), [1207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1207-L1207), [1212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1212-L1212), [1230](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1230-L1230), [1244](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1244-L1244), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

199:            revert TargetRelayerOnly(msg.sender, bridge);	// @audit-issue
```
[199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L199-L199), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

140:            revert ManagerOnly(l1Dispenser, msg.sender);	// @audit-issue

172:            revert ManagerOnly(l1Dispenser, msg.sender);	// @audit-issue
```
[140](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L140-L140), [172](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L172-L172), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

69:            revert WrongMessageSender(msg.sender, l1AliasedDepositProcessor);	// @audit-issue
```
[69](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L69-L69), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

138:            revert ManagerOnly(dispenser, msg.sender);	// @audit-issue

163:            revert ManagerOnly(dispenser, msg.sender);	// @audit-issue
```
[138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L138-L138), [163](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L163-L163), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

62:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

67:            revert ZeroAddress();	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L62-L62), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L67-L67), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

115:            revert WrongChainId(sourceChain, wormholeTargetChainId);	// @audit-issue

120:            revert AlreadyDelivered(deliveryHash);	// @audit-issue
```
[115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L115-L115), [120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L120-L120), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

102:            revert TargetRelayerOnly(msg.sender, l2TokenRelayer);	// @audit-issue
```
[102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L102-L102), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

250:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

255:            revert ZeroAddress();	// @audit-issue

269:            revert ReentrancyGuard();	// @audit-issue

275:            revert Paused();	// @audit-issue

283:            revert TargetAmountNotQueued(target, amount, batchNonce);	// @audit-issue

299:            revert InsufficientBalance(olasBalance, amount);	// @audit-issue

314:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

326:            revert ReentrancyGuard();	// @audit-issue

332:            revert Paused();	// @audit-issue

338:            revert ZeroValue();	// @audit-issue

356:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

367:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

380:            revert ReentrancyGuard();	// @audit-issue

386:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

392:            revert ZeroValue();	// @audit-issue

398:            revert TransferFailed(address(0), address(this), msg.sender, amount);	// @audit-issue

415:            revert ReentrancyGuard();	// @audit-issue

421:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

426:            revert Unpaused();	// @audit-issue

431:            revert WrongAccount(newL2TargetDispenser);	// @audit-issue

436:            revert WrongAccount(address(this));	// @audit-issue

445:                revert TransferFailed(olas, address(this), newL2TargetDispenser, amount);	// @audit-issue

461:            revert TransferFailed(address(0), msg.sender, address(this), msg.value);	// @audit-issue
```
[250](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L250-L250), [255](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L255-L255), [269](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L269-L269), [275](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L275-L275), [283](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L283-L283), [299](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L299-L299), [314](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L314-L314), [326](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L326-L326), [332](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L332-L332), [338](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L338-L338), [356](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L356-L356), [367](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L367-L367), [380](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L380-L380), [386](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L386-L386), [392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L392-L392), [398](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L398-L398), [415](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L415-L415), [421](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L421-L421), [426](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L426-L426), [431](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L431-L431), [436](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L436-L436), [445](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L445-L445), [461](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L461-L461), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

101:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

106:            revert ZeroAddress();	// @audit-issue
```
[101](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L101-L101), [106](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L106-L106), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

99:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

104:            revert ZeroAddress();	// @audit-issue

116:            revert OwnerOnly(owner, msg.sender);	// @audit-issue

138:            revert OwnerOnly(owner, msg.sender);	// @audit-issue

143:            revert WrongArrayLength(implementations.length, statuses.length);	// @audit-issue

153:                revert ZeroAddress();	// @audit-issue

236:            revert OwnerOnly(owner, msg.sender);	// @audit-issue

241:            revert ZeroValue();	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L99-L99), [104](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L104-L104), [116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L116-L116), [138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L138-L138), [143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L143-L143), [153](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L153-L153), [236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L236-L236), [241](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L241-L241), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

722:            revert NoRewardsAvailable();	// @audit-issue

729:            revert ServiceNotUnstaked(serviceId);	// @audit-issue

735:            revert MaxNumServicesReached(maxNumServices);	// @audit-issue

743:            revert WrongServiceConfiguration(serviceId);	// @audit-issue

748:            revert WrongServiceConfiguration(serviceId);	// @audit-issue

752:            revert WrongServiceConfiguration(serviceId);	// @audit-issue

756:            revert WrongServiceState(uint256(service.state), serviceId);	// @audit-issue

762:            revert UnauthorizedMultisig(service.multisig);	// @audit-issue

771:                revert WrongServiceConfiguration(serviceId);	// @audit-issue

776:                    revert WrongAgentId(agentIds[i]);	// @audit-issue

809:            revert OwnerOnly(msg.sender, sInfo.owner);	// @audit-issue

819:            revert NotEnoughTimeStaked(serviceId, ts, minStakingDuration);	// @audit-issue
```
[722](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L722-L722), [729](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L729-L729), [735](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L735-L735), [743](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L743-L743), [748](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L748-L748), [752](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L752-L752), [756](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L756-L756), [762](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L762-L762), [771](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L771-L771), [776](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L776-L776), [809](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L809-L809), [819](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L819-L819), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

64:            revert ZeroTokenAddress();	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L64-L64), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

113:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

118:            revert ZeroAddress();	// @audit-issue

130:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

174:            revert ReentrancyGuard();	// @audit-issue

180:            revert ZeroAddress();	// @audit-issue

185:            revert ContractOnly(implementation);	// @audit-issue

190:            revert IncorrectDataLength(initPayload.length, SELECTOR_DATA_LENGTH);	// @audit-issue

196:            revert UnverifiedImplementation(implementation);	// @audit-issue

212:            revert ProxyCreationFailed(implementation);	// @audit-issue

226:                revert InitializationFailed(instance);	// @audit-issue

232:            revert UnverifiedProxy(instance);	// @audit-issue

256:            revert OwnerOnly(msg.sender, deployer);	// @audit-issue
```
[113](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L113-L113), [118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L118-L118), [130](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L130-L130), [174](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L174-L174), [180](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L180-L180), [185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L185-L185), [190](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L190-L190), [196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L196-L196), [212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L212-L212), [226](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L226-L226), [232](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L232-L232), [256](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L256-L256), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

327:            revert ZeroAddress();	// @audit-issue

332:            revert ZeroValue();	// @audit-issue

337:            revert Overflow(chainId, MAX_EVM_CHAIN_ID);	// @audit-issue

352:            revert ZeroAddress();	// @audit-issue

357:            revert Underflow(chainId, MAX_EVM_CHAIN_ID + 1);	// @audit-issue

371:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

376:            revert ZeroAddress();	// @audit-issue

389:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

479:            revert NomineeRemoved(account, chainId);	// @audit-issue

485:            revert NegativeSlope(msg.sender, userSlope);	// @audit-issue

493:            revert LockExpired(msg.sender, lockEnd, nextTime);	// @audit-issue

498:            revert Overflow(weight, MAX_WEIGHT);	// @audit-issue

504:            revert VoteTooOften(msg.sender, block.timestamp, nextAllowedVotingTime);	// @audit-issue

526:            revert Overflow(powerUsed, MAX_WEIGHT);	// @audit-issue

569:            revert WrongArrayLength(accounts.length, weights.length);	// @audit-issue

589:            revert OwnerOnly(owner, msg.sender);	// @audit-issue

599:            revert NomineeDoesNotExist(account, chainId);	// @audit-issue

648:            revert NomineeNotRemoved(account, chainId);	// @audit-issue

654:            revert ZeroValue();	// @audit-issue

749:            revert ZeroValue();	// @audit-issue

751:            revert Overflow(id, totalNumNominees);	// @audit-issue

766:            revert ZeroValue();	// @audit-issue

768:            revert Overflow(id, totalNumRemovedNominees);	// @audit-issue

786:            revert WrongArrayLength(accounts.length, chainIds.length);	// @audit-issue

800:                revert NomineeDoesNotExist(accounts[i], chainIds[i]);	// @audit-issue
```
[327](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L327-L327), [332](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L332-L332), [337](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L337-L337), [352](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L352-L352), [357](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L357-L357), [371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L371-L371), [376](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L376-L376), [389](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L389-L389), [479](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L479-L479), [485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L485-L485), [493](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L493-L493), [498](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L498-L498), [504](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L504-L504), [526](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L526-L526), [569](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L569-L569), [589](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L589-L589), [599](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L599-L599), [648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L648-L648), [654](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L654-L654), [749](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L749-L749), [751](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L751-L751), [766](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L766-L766), [768](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L768-L768), [786](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L786-L786), [800](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L800-L800), 


#### Recommendation

Design your Solidity contract's public and external functions with care to mitigate the risk of DoS attacks via `revert` statements. Implement robust input validation to ensure inputs are within expected bounds and conditions. Consider alternative logic or design patterns that reduce the reliance on `revert` for critical operations, particularly those that can be influenced externally. Evaluate the use of modifiers, try-catch blocks, or state checks that allow for safer handling of conditions and exceptions. Ensure that your contract's critical functionality remains accessible and resilient against potential abuse of `revert` behavior by malicious actors.

### Use `string.concat()` on strings instead of `abi.encodePacked()` for clearer semantic meaning
From Solidity 0.8.12 onwards, developers can utilize `string.concat()` to concatenate strings without additional padding. Opting for `string.concat()` over `abi.encodePacked()` offers clearer semantic interpretation of the code's intent, enhancing readability. This shift minimizes ambiguity, reducing the potential for misinterpretation by reviewers or future developers. Thus, for string concatenation tasks, it's recommended to transition to `string.concat()` for transparent, straightforward code that communicates its purpose distinctly.

```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

143:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue

146:        bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,	// @audit-issue
147:            uint256(uint160(implementation)));

151:            abi.encodePacked(	// @audit-issue
152:                bytes1(0xff), address(this), salt, keccak256(deploymentData)
153:            )

201:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue

203:        bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,	// @audit-issue
204:            uint256(uint160(implementation)));
```
[143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L143-L143), [146](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L146-L147), [151](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L151-L153), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L201-L201), [203](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L203-L204), 


#### Recommendation

Consider using `bytes.concat()` instead of `abi.encodePacked()` for concatenating `bytes` for clearer semantic meaning, especially in Solidity versions 0.8.4 and later.

### Consider adding emergency-stop functionality
In the event of a security breach or any unforeseen emergency, swiftly suspending all protocol operations becomes crucial. Having a mechanism in place to halt all functions collectively, instead of pausing individual contracts separately, substantially enhances the efficiency of mitigating ongoing attacks or vulnerabilities. This not only quickens the response time to potential threats but also reduces operational stress during these critical periods. Therefore, consider integrating a 'circuit breaker' or 'emergency stop' function into the smart contract system architecture. Such a feature would provide the capability to suspend the entire protocol instantly, which could prove invaluable during a time-sensitive crisis management situation.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

254:contract Tokenomics is TokenomicsConstants {	// @audit-issue
```
[254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L254-L254), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

253:contract Dispenser {	// @audit-issue
```
[253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L253-L253), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

70:contract ArbitrumDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L70-L70), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

37:contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L37-L37), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

23:contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L23-L23), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

59:contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

50:contract EthereumDepositProcessor {	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

50:contract WormholeTargetDispenserL2 is DefaultTargetDispenserL2, TokenReceiver {	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

32:contract GnosisDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L32-L32), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

11:contract PolygonTargetDispenserL2 is DefaultTargetDispenserL2, FxBaseChildTunnel {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

11:contract WormholeDepositProcessorL1 is DefaultDepositProcessorL1, TokenSender {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

26:contract GnosisTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L26-L26), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

22:contract PolygonDepositProcessorL1 is DefaultDepositProcessorL1, FxBaseRootTunnel {	// @audit-issue
```
[22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

16:library SafeTransferLib {	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L16-L16), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

43:contract StakingVerifier {	// @audit-issue
```
[43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L43-L43), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

17:contract StakingNativeToken is StakingBase {	// @audit-issue
```
[17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L17-L17), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

18:contract StakingActivityChecker {	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L18-L18), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

44:contract StakingToken is StakingBase {	// @audit-issue
```
[44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L44-L44), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

21:contract StakingProxy {	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L21-L21), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

81:contract StakingFactory {	// @audit-issue
```
[81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L81-L81), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

132:contract VoteWeighting {	// @audit-issue
```
[132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L132-L132), 


#### Recommendation

Implement an emergency-stop feature in your Solidity contract system to enhance security and crisis response capabilities. This can be achieved through a 'circuit breaker' pattern, where a central switch or set of conditions can instantly suspend critical operations across the contract ecosystem. Ensure that this mechanism is accessible to authorized parties only, such as contract administrators or a decentralized governance system. Design the emergency-stop functionality to be transparent and auditable, with clear conditions and processes for activation and deactivation. Regularly test and audit this feature to ensure its reliability and effectiveness in potential emergency situations.

### Assembly block creates dirty bits
Manipulating data directly at the free memory pointer location without subsequently adjusting the pointer can lead to unwanted data remnants, or "dirty bits", in that memory spot. This can cause challenges for the Solidity optimizer, making it difficult to determine if memory cleaning is required before reuse, potentially resulting in less efficient optimization. To mitigate this issue, it's advised to always update the free memory pointer following any data write operation. Furthermore, using the `assembly ("memory-safe") { ... }` annotation will clearly indicate to the optimizer the sections of your code that are memory-safe, improving code efficiency and reducing the potential for errors.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

517:        assembly {	// @audit-issue
518:            implementation := sload(PROXY_TOKENOMICS)
519:        }

538:        assembly {	// @audit-issue
539:            sstore(PROXY_TOKENOMICS, implementation)
540:        }

1083:        assembly {	// @audit-issue
1084:            implementation := sload(PROXY_TOKENOMICS)
1085:        }
```
[517](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L517-L519), [538](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L538-L540), [1083](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1083-L1085), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

26:        assembly {	// @audit-issue
27:        // We'll write our calldata to this slot below, but restore it later.
28:            let memPointer := mload(0x40)
29:
30:        // Write the abi-encoded calldata into memory, beginning with the function selector.
31:            mstore(0, 0x23b872dd00000000000000000000000000000000000000000000000000000000)
32:            mstore(4, from) // Append the "from" argument.
33:            mstore(36, to) // Append the "to" argument.
34:            mstore(68, amount) // Append the "amount" argument.
35:
36:            success := and(
37:            // Set success to whether the call reverted, if not we check it either
38:            // returned exactly 1 (can't just be non-zero data), or had no return data.
39:                or(and(eq(mload(0), 1), gt(returndatasize(), 31)), iszero(returndatasize())),
40:            // We use 100 because that's the total length of our calldata (4 + 32 * 3)
41:            // Counterintuitively, this call() must be positioned after the or() in the
42:            // surrounding and() because and() evaluates its arguments from right to left.
43:                call(gas(), token, 0, 0, 100, 0, 32)
44:            )
45:
46:            mstore(0x60, 0) // Restore the zero slot to zero.
47:            mstore(0x40, memPointer) // Restore the memPointer.
48:        }

68:        assembly {	// @audit-issue
69:        // We'll write our calldata to this slot below, but restore it later.
70:            let memPointer := mload(0x40)
71:
72:        // Write the abi-encoded calldata into memory, beginning with the function selector.
73:            mstore(0, 0xa9059cbb00000000000000000000000000000000000000000000000000000000)
74:            mstore(4, to) // Append the "to" argument.
75:            mstore(36, amount) // Append the "amount" argument.
76:
77:            success := and(
78:            // Set success to whether the call reverted, if not we check it either
79:            // returned exactly 1 (can't just be non-zero data), or had no return data.
80:                or(and(eq(mload(0), 1), gt(returndatasize(), 31)), iszero(returndatasize())),
81:            // We use 68 because that's the total length of our calldata (4 + 32 * 2)
82:            // Counterintuitively, this call() must be positioned after the or() in the
83:            // surrounding and() because and() evaluates its arguments from right to left.
84:                call(gas(), token, 0, 0, 68, 0, 32)
85:            )
86:
87:            mstore(0x60, 0) // Restore the zero slot to zero.
88:            mstore(0x40, memPointer) // Restore the memPointer.
89:        }
```
[26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L26-L48), [68](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L68-L89), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

34:        assembly {	// @audit-issue
35:            sstore(SERVICE_STAKING_PROXY, implementation)
36:        }

41:        assembly {	// @audit-issue
42:            let implementation := sload(SERVICE_STAKING_PROXY)
43:            calldatacopy(0, 0, calldatasize())
44:            let success := delegatecall(gas(), implementation, 0, calldatasize(), 0, 0)
45:            returndatacopy(0, 0, returndatasize())
46:            if eq(success, 0) {
47:                revert(0, returndatasize())
48:            }
49:            return(0, returndatasize())
50:        }

55:        assembly {	// @audit-issue
56:            implementation := sload(SERVICE_STAKING_PROXY)
57:        }
```
[34](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L34-L36), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L41-L50), [55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L55-L57), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

207:        assembly {	// @audit-issue
208:            instance := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt)
209:        }

221:                assembly {	// @audit-issue
222:                    let returnDataSize := mload(returnData)
223:                    revert(add(32, returnData), returnDataSize)
224:                }
```
[207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L207-L209), [221](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L221-L224), 


#### Recommendation

Always update the free memory pointer after writing data in Solidity assembly blocks to ensure memory cleanliness. This practice prevents the creation of 'dirty bits' and aids the Solidity optimizer in performing efficient memory management. Additionally, consider using the `assembly ("memory-safe") { ... }` annotation for sections of your code that are memory-safe. This annotation clearly communicates to the optimizer which parts of your assembly code are handling memory correctly, leading to better optimization and reduced risk of memory-related errors. Regularly review and audit your assembly blocks for proper memory handling to maintain the efficiency and reliability of your Solidity contracts.

### Cyclomatic complexity in functions
Cyclomatic complexity is a software metric used to measure the complexity of a program. It quantifies the number of linearly independent paths through a program's source code, giving an idea of how complex the control flow is. High cyclomatic complexity may indicate a higher risk of defects and can make the code harder to understand, test, and maintain. It often suggests that a function or method is trying to do too much, and a refactor might be needed. By breaking down complex functions into smaller, more focused pieces, you can improve readability, ease of testing, and overall maintainability.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

639:    function changeTokenomicsParameters(	// @audit-issue
640:        uint256 _devsPerCapital,
641:        uint256 _codePerDev,
642:        uint256 _epsilonRate,
643:        uint256 _epochLen,
644:        uint256 _veOLASThreshold
645:    ) external {
646:        // Check for the contract ownership
647:        if (msg.sender != owner) {
648:            revert OwnerOnly(msg.sender, owner);
649:        }
650:
651:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
652:        if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {
653:            devsPerCapital = uint72(_devsPerCapital);
654:        } else {
655:            // This is done in order not to pass incorrect parameters into the event
656:            _devsPerCapital = devsPerCapital;
657:        }
658:
659:        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
660:        if (uint72(_codePerDev) > MIN_PARAM_VALUE) {
661:            codePerDev = uint72(_codePerDev);
662:        } else {
663:            // This is done in order not to pass incorrect parameters into the event
664:            _codePerDev = codePerDev;
665:        }
666:
667:        // Check the epsilonRate value for idf to fit in its size
668:        // 2^64 - 1 < 18.5e18, idf is equal at most 1 + epsilonRate < 18e18, which fits in the variable size
669:        // epsilonRate is the part of the IDF calculation and thus its change will be accounted for in the next epoch
670:        if (_epsilonRate > 0 && _epsilonRate <= 17e18) {
671:            epsilonRate = uint64(_epsilonRate);
672:        } else {
673:            _epsilonRate = epsilonRate;
674:        }
675:
676:        // Check for the epochLen value to change
677:        if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {
678:            nextEpochLen = uint32(_epochLen);
679:        } else {
680:            _epochLen = epochLen;
681:        }
682:
683:        // Adjust veOLAS threshold for the next epoch
684:        if (uint96(_veOLASThreshold) > 0) {
685:            nextVeOLASThreshold = uint96(_veOLASThreshold);
686:        } else {
687:            _veOLASThreshold = veOLASThreshold;
688:        }
689:
690:        // Set the flag that tokenomics parameters are requested to be updated (1st bit is set to one)
691:        tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x01;
692:        emit TokenomicsParametersUpdateRequested(epochCounter + 1, _devsPerCapital, _codePerDev, _epsilonRate, _epochLen,
693:            _veOLASThreshold);
694:    }

884:    function _trackServiceDonations(	// @audit-issue
885:        address donator,
886:        uint256[] memory serviceIds,
887:        uint256[] memory amounts,
888:        uint256 curEpoch
889:    ) internal {
890:        // Component / agent registry addresses
891:        address[] memory registries = new address[](2);
892:        (registries[0], registries[1]) = (componentRegistry, agentRegistry);
893:
894:        // Check all the unit fractions and identify those that need accounting of incentives
895:        bool[] memory incentiveFlags = new bool[](4);
896:        incentiveFlags[0] = (mapEpochTokenomics[curEpoch].unitPoints[0].rewardUnitFraction > 0);
897:        incentiveFlags[1] = (mapEpochTokenomics[curEpoch].unitPoints[1].rewardUnitFraction > 0);
898:        incentiveFlags[2] = (mapEpochTokenomics[curEpoch].unitPoints[0].topUpUnitFraction > 0);
899:        incentiveFlags[3] = (mapEpochTokenomics[curEpoch].unitPoints[1].topUpUnitFraction > 0);
900:
901:        // Get the number of services
902:        uint256 numServices = serviceIds.length;
903:        // Loop over service Ids to calculate their partial contributions
904:        for (uint256 i = 0; i < numServices; ++i) {
905:            // Check if the service owner or donator stakes enough OLAS for its components / agents to get a top-up
906:            // If both component and agent owner top-up fractions are zero, there is no need to call external contract
907:            // functions to check each service owner veOLAS balance
908:            bool topUpEligible;
909:            if (incentiveFlags[2] || incentiveFlags[3]) {
910:                address serviceOwner = IToken(serviceRegistry).ownerOf(serviceIds[i]);
911:                topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||
912:                    IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;
913:            }
914:
915:            // Loop over component and agent Ids
916:            for (uint256 unitType = 0; unitType < 2; ++unitType) {
917:                // Get the number and set of units in the service
918:                (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).
919:                    getUnitIdsOfService(IServiceRegistry.UnitType(unitType), serviceIds[i]);
920:                // Service has to be deployed at least once to be able to receive donations,
921:                // otherwise its components and agents are undefined
922:                if (numServiceUnits == 0) {
923:                    revert ServiceNeverDeployed(serviceIds[i]);
924:                }
925:                // Record amounts data only if at least one incentive unit fraction is not zero
926:                if (incentiveFlags[unitType] || incentiveFlags[unitType + 2]) {
927:                    // The amount has to be adjusted for the number of units in the service
928:                    uint96 amount = uint96(amounts[i] / numServiceUnits);
929:                    // Accumulate amounts for each unit Id
930:                    for (uint256 j = 0; j < numServiceUnits; ++j) {
931:                        // Get the last epoch number the incentives were accumulated for
932:                        uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch;
933:                        // Check if there were no donations in previous epochs and set the current epoch
934:                        if (lastEpoch == 0) {
935:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
936:                        } else if (lastEpoch < curEpoch) {
937:                            // Finalize unit rewards and top-ups if there were pending ones from the previous epoch
938:                            // Pending incentives are getting finalized during the next epoch the component / agent
939:                            // receives donations. If this is not the case before claiming incentives, the finalization
940:                            // happens in the accountOwnerIncentives() where the incentives are issued
941:                            _finalizeIncentivesForUnitId(lastEpoch, unitType, serviceUnitIds[j]);
942:                            // Change the last epoch number
943:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
944:                        }
945:                        // Sum the relative amounts for the corresponding components / agents
946:                        if (incentiveFlags[unitType]) {
947:                            mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeReward += amount;
948:                        }
949:                        // If eligible, add relative top-up weights in the form of donation amounts.
950:                        // These weights will represent the fraction of top-ups for each component / agent relative
951:                        // to the overall amount of top-ups that must be allocated
952:                        if (topUpEligible && incentiveFlags[unitType + 2]) {
953:                            mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeTopUp += amount;
954:                            mapEpochTokenomics[curEpoch].unitPoints[unitType].sumUnitTopUpsOLAS += amount;
955:                        }
956:                    }
957:                }
958:
959:                // Record new units and new unit owners
960:                for (uint256 j = 0; j < numServiceUnits; ++j) {
961:                    // Check if the component / agent is used for the first time
962:                    if (!mapNewUnits[unitType][serviceUnitIds[j]]) {
963:                        mapNewUnits[unitType][serviceUnitIds[j]] = true;
964:                        mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;
965:                        // Check if the owner has introduced component / agent for the first time
966:                        // This is done together with the new unit check, otherwise it could be just a new unit owner
967:                        address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);
968:                        if (!mapNewOwners[unitOwner]) {
969:                            mapNewOwners[unitOwner] = true;
970:                            mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;
971:                        }
972:                    }
973:                }
974:            }
975:        }
976:    }

1080:    function checkpoint() external returns (bool) {	// @audit-issue
1081:        // Get the implementation address that was written to the proxy contract
1082:        address implementation;
1083:        assembly {
1084:            implementation := sload(PROXY_TOKENOMICS)
1085:        }
1086:        // Check if there is any address in the PROXY_TOKENOMICS address slot
1087:        if (implementation == address(0)) {
1088:            revert DelegatecallOnly();
1089:        }
1090:
1091:        // Check the last donation block number to avoid the possibility of a flash loan attack
1092:        if (lastDonationBlockNumber == block.number) {
1093:            revert SameBlockNumberViolation();
1094:        }
1095:
1096:        // New point can be calculated only if we passed the number of blocks equal to the epoch length
1097:        uint256 prevEpochTime = mapEpochTokenomics[epochCounter - 1].epochPoint.endTime;
1098:        uint256 diffNumSeconds = block.timestamp - prevEpochTime;
1099:        uint256 curEpochLen = epochLen;
1100:        // Check if the time passed since the last epoch end time is bigger than the specified epoch length,
1101:        // but not bigger than almost a year in seconds
1102:        if (diffNumSeconds < curEpochLen || diffNumSeconds > MAX_EPOCH_LENGTH) {
1103:            return false;
1104:        }
1105:
1106:        uint256 eCounter = epochCounter;
1107:        TokenomicsPoint storage tp = mapEpochTokenomics[eCounter];
1108:
1109:        // 0: total incentives funded with donations in ETH, that are split between:
1110:        // 1: treasuryRewards, 2: componentRewards, 3: agentRewards
1111:        // OLAS inflation is split between:
1112:        // 4: maxBond, 5: component ownerTopUps, 6: agent ownerTopUps
1113:        // 7: returned staking incentive from previous epochs, 8: staking incentive
1114:        uint256[] memory incentives = new uint256[](9);
1115:        incentives[0] = tp.epochPoint.totalDonationsETH;
1116:        incentives[1] = (incentives[0] * tp.epochPoint.rewardTreasuryFraction) / 100;
1117:        // 0 stands for components and 1 for agents
1118:        incentives[2] = (incentives[0] * tp.unitPoints[0].rewardUnitFraction) / 100;
1119:        incentives[3] = (incentives[0] * tp.unitPoints[1].rewardUnitFraction) / 100;
1120:
1121:        // The actual inflation per epoch considering that it is settled not in the exact epochLen time, but a bit later
1122:        uint256 inflationPerEpoch;
1123:        // Record the current inflation per second
1124:        uint256 curInflationPerSecond = inflationPerSecond;
1125:        // Current year
1126:        uint256 numYears = (block.timestamp - timeLaunch) / ONE_YEAR;
1127:        // Amounts for the yearly inflation change from year to year, so if the year changes in the middle
1128:        // of the epoch, it is necessary to adjust epoch inflation numbers to account for the year change
1129:        if (numYears > currentYear) {
1130:            // Calculate remainder of inflation for the passing year
1131:            // End of the year timestamp
1132:            uint256 yearEndTime = timeLaunch + numYears * ONE_YEAR;
1133:            // Initial inflation per epoch during the end of the year minus previous epoch timestamp
1134:            inflationPerEpoch = (yearEndTime - prevEpochTime) * curInflationPerSecond;
1135:            // Recalculate the inflation per second based on the new inflation for the current year
1136:            curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;
1137:            // Add the remainder of inflation amount for this epoch based on a new inflation per second ratio
1138:            inflationPerEpoch += (block.timestamp - yearEndTime) * curInflationPerSecond;
1139:            // Updating state variables
1140:            inflationPerSecond = uint96(curInflationPerSecond);
1141:            currentYear = uint8(numYears);
1142:            // Set the tokenomics parameters flag such that the maxBond is correctly updated below (3rd bit is set to one)
1143:            tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x04;
1144:        } else {
1145:            // Inflation per epoch is equal to the inflation per second multiplied by the actual time of the epoch
1146:            inflationPerEpoch = curInflationPerSecond * diffNumSeconds;
1147:        }
1148:
1149:        // Bonding and top-ups in OLAS are recalculated based on the inflation schedule per epoch
1150:        // Actual maxBond of the epoch
1151:        tp.epochPoint.totalTopUpsOLAS = uint96(inflationPerEpoch);
1152:        incentives[4] = (inflationPerEpoch * tp.epochPoint.maxBondFraction) / 100;
1153:
1154:        // Get the maxBond that was credited to effectiveBond during this settled epoch
1155:        // If the year changes, the maxBond for the next epoch is updated in the condition below and will be used
1156:        // later when the effectiveBond is updated for the next epoch
1157:        uint256 curMaxBond = maxBond;
1158:
1159:        // Effective bond accumulates bonding leftovers from previous epochs (with the last max bond value set)
1160:        // It is given the value of the maxBond for the next epoch as a credit
1161:        // The difference between recalculated max bond per epoch and maxBond value must be reflected in effectiveBond,
1162:        // since the epoch checkpoint delay was not accounted for initially
1163:        // This has to be always true, or incentives[4] == curMaxBond if the epoch is settled exactly at the epochLen time
1164:        if (incentives[4] > curMaxBond) {
1165:            // Adjust the effectiveBond
1166:            incentives[4] = effectiveBond + incentives[4] - curMaxBond;
1167:            effectiveBond = uint96(incentives[4]);
1168:        }
1169:
1170:        // Get the tokenomics point of the next epoch
1171:        TokenomicsPoint storage nextEpochPoint = mapEpochTokenomics[eCounter + 1];
1172:        // Update parameters for the next epoch, if changes were requested by the changeTokenomicsParameters() function
1173:        // Check if the second bit is set to one
1174:        if (tokenomicsParametersUpdated & 0x01 == 0x01) {
1175:            // Update epoch length and set the next value back to zero
1176:            if (nextEpochLen > 0) {
1177:                curEpochLen = nextEpochLen;
1178:                epochLen = uint32(curEpochLen);
1179:                nextEpochLen = 0;
1180:            }
1181:
1182:            // Update veOLAS threshold and set the next value back to zero
1183:            if (nextVeOLASThreshold > 0) {
1184:                veOLASThreshold = nextVeOLASThreshold;
1185:                nextVeOLASThreshold = 0;
1186:            }
1187:
1188:            // Confirm the change of tokenomics parameters
1189:            emit TokenomicsParametersUpdated(eCounter + 1);
1190:        }
1191:
1192:        // Update incentive fractions for the next epoch if they were requested by the changeIncentiveFractions() function
1193:        // Check if the second bit is set to one
1194:        if (tokenomicsParametersUpdated & 0x02 == 0x02) {
1195:            // Confirm the change of incentive fractions
1196:            emit IncentiveFractionsUpdated(eCounter + 1);
1197:        } else {
1198:            // Copy current tokenomics point into the next one such that it has necessary tokenomics parameters
1199:            for (uint256 i = 0; i < 2; ++i) {
1200:                nextEpochPoint.unitPoints[i].topUpUnitFraction = tp.unitPoints[i].topUpUnitFraction;
1201:                nextEpochPoint.unitPoints[i].rewardUnitFraction = tp.unitPoints[i].rewardUnitFraction;
1202:            }
1203:            nextEpochPoint.epochPoint.rewardTreasuryFraction = tp.epochPoint.rewardTreasuryFraction;
1204:            nextEpochPoint.epochPoint.maxBondFraction = tp.epochPoint.maxBondFraction;
1205:            // Copy service staking fraction
1206:            mapEpochStakingPoints[eCounter + 1].stakingFraction = mapEpochStakingPoints[eCounter].stakingFraction;
1207:        }
1208:
1209:        // Update service staking parameters if they were requested by the changeStakingParams() function
1210:        // Check if the forth bit is set to one
1211:        if (tokenomicsParametersUpdated & 0x08 == 0x08) {
1212:            // Confirm the change of service staking parameters
1213:            emit StakingParamsUpdated(eCounter + 1);
1214:        } else {
1215:            // Copy current service staking parameters into the next epoch
1216:            mapEpochStakingPoints[eCounter + 1].maxStakingIncentive = mapEpochStakingPoints[eCounter].maxStakingIncentive;
1217:            mapEpochStakingPoints[eCounter + 1].minStakingWeight = mapEpochStakingPoints[eCounter].minStakingWeight;
1218:        }
1219:        // Record settled epoch timestamp
1220:        tp.epochPoint.endTime = uint32(block.timestamp);
1221:
1222:        // Cumulative incentives
1223:        uint256 accountRewards = incentives[2] + incentives[3];
1224:        // Owner top-ups: epoch incentives for component owners funded with the inflation
1225:        incentives[5] = (inflationPerEpoch * tp.unitPoints[0].topUpUnitFraction) / 100;
1226:        // Owner top-ups: epoch incentives for agent owners funded with the inflation
1227:        incentives[6] = (inflationPerEpoch * tp.unitPoints[1].topUpUnitFraction) / 100;
1228:        // Even if there was no single donating service owner that had a sufficient veOLAS balance,
1229:        // we still record the amount of OLAS allocated for component / agent owner top-ups from the inflation schedule.
1230:        // This amount will appear in the EpochSettled event, and thus can be tracked historically
1231:        uint256 accountTopUps = incentives[5] + incentives[6];
1232:
1233:        // Service staking funding
1234:        // Refunded amount during the epoch
1235:        incentives[7] = mapEpochStakingPoints[eCounter].stakingIncentive;
1236:        // Adding service staking top-ups amount based on a current epoch inflation
1237:        incentives[8] = incentives[7] + (inflationPerEpoch * mapEpochStakingPoints[eCounter].stakingFraction) / 100;
1238:        mapEpochStakingPoints[eCounter].stakingIncentive = uint96(incentives[8]);
1239:
1240:        // Adjust max bond value if the next epoch is going to be the year change epoch
1241:        // Note that this computation happens before the epoch that is triggered in the next epoch (the code above) when
1242:        // the actual year changes
1243:        numYears = (block.timestamp + curEpochLen - timeLaunch) / ONE_YEAR;
1244:        // Account for the year change to adjust the max bond
1245:        if (numYears > currentYear) {
1246:            // Calculate the inflation remainder for the passing year
1247:            // End of the year timestamp
1248:            uint256 yearEndTime = timeLaunch + numYears * ONE_YEAR;
1249:            // Calculate the inflation per epoch value until the end of the year
1250:            inflationPerEpoch = (yearEndTime - block.timestamp) * curInflationPerSecond;
1251:            // Recalculate the inflation per second based on the new inflation for the current year
1252:            curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;
1253:            // Add the remainder of the inflation for the next epoch based on a new inflation per second ratio
1254:            inflationPerEpoch += (block.timestamp + curEpochLen - yearEndTime) * curInflationPerSecond;
1255:            // Calculate the max bond value
1256:            curMaxBond = (inflationPerEpoch * nextEpochPoint.epochPoint.maxBondFraction) / 100;
1257:            // Update state maxBond value
1258:            maxBond = uint96(curMaxBond);
1259:            // Reset the tokenomics parameters update flag
1260:            tokenomicsParametersUpdated = 0;
1261:        } else if (tokenomicsParametersUpdated > 0) {
1262:            // Since tokenomics parameters have been updated, maxBond has to be recalculated
1263:            curMaxBond = (curEpochLen * curInflationPerSecond * nextEpochPoint.epochPoint.maxBondFraction) / 100;
1264:            // Update state maxBond value
1265:            maxBond = uint96(curMaxBond);
1266:            // Reset the tokenomics parameters update flag
1267:            tokenomicsParametersUpdated = 0;
1268:        }
1269:        // Update effectiveBond with the current or updated maxBond value
1270:        curMaxBond += effectiveBond;
1271:        effectiveBond = uint96(curMaxBond);
1272:
1273:        // Update the IDF value for the next epoch or assign a default one if there are no ETH donations
1274:        if (incentives[0] > 0) {
1275:            // Calculate IDF based on the incoming donations
1276:            uint256 idf = _calculateIDF(incentives[1], tp.epochPoint.numNewOwners);
1277:            nextEpochPoint.epochPoint.idf = uint64(idf);
1278:            emit IDFUpdated(idf);
1279:        } else {
1280:            // Assign a default IDF value
1281:            nextEpochPoint.epochPoint.idf = 1e18;
1282:        }
1283:
1284:        // Treasury contract rebalances ETH funds depending on the treasury rewards
1285:        if (incentives[1] == 0 || ITreasury(treasury).rebalanceTreasury(incentives[1])) {
1286:            // Emit settled epoch written to the last economics point
1287:            emit EpochSettled(eCounter, incentives[1], accountRewards, accountTopUps, curMaxBond, incentives[7],
1288:                incentives[8]);
1289:            // Start new epoch
1290:            epochCounter = uint32(eCounter + 1);
1291:        } else {
1292:            // If the treasury rebalance was not executed correctly, the new epoch does not start
1293:            revert TreasuryRebalanceFailed(eCounter);
1294:        }
1295:
1296:        return true;
1297:    }

1308:    function accountOwnerIncentives(	// @audit-issue
1309:        address account,
1310:        uint256[] memory unitTypes,
1311:        uint256[] memory unitIds
1312:    ) external returns (uint256 reward, uint256 topUp) {
1313:        // Check for the dispenser access
1314:        if (dispenser != msg.sender) {
1315:            revert ManagerOnly(msg.sender, dispenser);
1316:        }
1317:
1318:        // Check array lengths
1319:        if (unitTypes.length != unitIds.length) {
1320:            revert WrongArrayLength(unitTypes.length, unitIds.length);
1321:        }
1322:
1323:        // Component / agent registry addresses
1324:        address[] memory registries = new address[](2);
1325:        (registries[0], registries[1]) = (componentRegistry, agentRegistry);
1326:
1327:        // Component / agent total supply
1328:        uint256[] memory registriesSupply = new uint256[](2);
1329:        for (uint256 i = 0; i < 2; ++i) {
1330:            registriesSupply[i] = IToken(registries[i]).totalSupply();
1331:        }
1332:
1333:        // Check the input data
1334:        uint256[] memory lastIds = new uint256[](2);
1335:        for (uint256 i = 0; i < unitIds.length; ++i) {
1336:            // Check for the unit type to be component / agent only
1337:            if (unitTypes[i] > 1) {
1338:                revert Overflow(unitTypes[i], 1);
1339:            }
1340:
1341:            // Check that the unit Ids are in ascending order, not repeating, and no bigger than registries total supply
1342:            if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
1343:                revert WrongUnitId(unitIds[i], unitTypes[i]);
1344:            }
1345:            lastIds[unitTypes[i]] = unitIds[i];
1346:
1347:            // Check the component / agent Id ownership
1348:            address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
1349:            if (unitOwner != account) {
1350:                revert OwnerOnly(unitOwner, account);
1351:            }
1352:        }
1353:
1354:        // Get the current epoch counter
1355:        uint256 curEpoch = epochCounter;
1356:
1357:        for (uint256 i = 0; i < unitIds.length; ++i) {
1358:            // Get the last epoch number the incentives were accumulated for
1359:            uint256 lastEpoch = mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch;
1360:            // Finalize unit rewards and top-ups if there were pending ones from the previous epoch
1361:            // The finalization is needed when the trackServiceDonations() function did not take care of it
1362:            // since between last epoch the donations were received and this current epoch there were no more donations
1363:            if (lastEpoch > 0 && lastEpoch < curEpoch) {
1364:                _finalizeIncentivesForUnitId(lastEpoch, unitTypes[i], unitIds[i]);
1365:                // Change the last epoch number
1366:                mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch = 0;
1367:            }
1368:
1369:            // Accumulate total rewards and clear their balances
1370:            reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;
1371:            mapUnitIncentives[unitTypes[i]][unitIds[i]].reward = 0;
1372:            // Accumulate total top-ups and clear their balances
1373:            topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;
1374:            mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp = 0;
1375:        }
1376:    }

1385:    function getOwnerIncentives(	// @audit-issue
1386:        address account,
1387:        uint256[] memory unitTypes,
1388:        uint256[] memory unitIds
1389:    ) external view returns (uint256 reward, uint256 topUp) {
1390:        // Check array lengths
1391:        if (unitTypes.length != unitIds.length) {
1392:            revert WrongArrayLength(unitTypes.length, unitIds.length);
1393:        }
1394:
1395:        // Component / agent registry addresses
1396:        address[] memory registries = new address[](2);
1397:        (registries[0], registries[1]) = (componentRegistry, agentRegistry);
1398:
1399:        // Component / agent total supply
1400:        uint256[] memory registriesSupply = new uint256[](2);
1401:        for (uint256 i = 0; i < 2; ++i) {
1402:            registriesSupply[i] = IToken(registries[i]).totalSupply();
1403:        }
1404:
1405:        // Check the input data
1406:        uint256[] memory lastIds = new uint256[](2);
1407:        for (uint256 i = 0; i < unitIds.length; ++i) {
1408:            // Check for the unit type to be component / agent only
1409:            if (unitTypes[i] > 1) {
1410:                revert Overflow(unitTypes[i], 1);
1411:            }
1412:
1413:            // Check that the unit Ids are in ascending order, not repeating, and no bigger than registries total supply
1414:            if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
1415:                revert WrongUnitId(unitIds[i], unitTypes[i]);
1416:            }
1417:            lastIds[unitTypes[i]] = unitIds[i];
1418:
1419:            // Check the component / agent Id ownership
1420:            address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
1421:            if (unitOwner != account) {
1422:                revert OwnerOnly(unitOwner, account);
1423:            }
1424:        }
1425:
1426:        // Get the current epoch counter
1427:        uint256 curEpoch = epochCounter;
1428:
1429:        for (uint256 i = 0; i < unitIds.length; ++i) {
1430:            // Get the last epoch number the incentives were accumulated for
1431:            uint256 lastEpoch = mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch;
1432:            // Calculate rewards and top-ups if there were pending ones from the previous epoch
1433:            if (lastEpoch > 0 && lastEpoch < curEpoch) {
1434:                // Get the overall amount of unit rewards for the component's last epoch
1435:                // reward = (pendingRelativeReward * rewardUnitFraction) / 100
1436:                uint256 totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeReward;
1437:                if (totalIncentives > 0) {
1438:                    totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].rewardUnitFraction;
1439:                    // Accumulate to the final reward for the last epoch
1440:                    reward += totalIncentives / 100;
1441:                }
1442:                // Add the final top-up for the last epoch
1443:                totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeTopUp;
1444:                if (totalIncentives > 0) {
1445:                    // Summation of all the unit top-ups and total amount of top-ups per epoch
1446:                    // topUp = (pendingRelativeTopUp * totalTopUpsOLAS * topUpUnitFraction) / (100 * sumUnitTopUpsOLAS)
1447:                    totalIncentives *= mapEpochTokenomics[lastEpoch].epochPoint.totalTopUpsOLAS;
1448:                    totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].topUpUnitFraction;
1449:                    uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100;
1450:                    // Accumulate to the final top-up for the last epoch
1451:                    topUp += totalIncentives / sumUnitIncentives;
1452:                }
1453:            }
1454:
1455:            // Accumulate total rewards to finalized ones
1456:            reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;
1457:            // Accumulate total top-ups to finalized ones
1458:            topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;
1459:        }
1460:    }
```
[639](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L639-L694), [884](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L884-L976), [1080](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1080-L1297), [1308](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1308-L1376), [1385](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1385-L1460), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

506:    function _checkOrderAndValues(	// @audit-issue
507:        uint256[] memory chainIds,
508:        bytes32[][] memory stakingTargets,
509:        bytes[] memory bridgePayloads,
510:        uint256[] memory valueAmounts
511:    ) internal {
512:        // Check array sizes
513:        if (chainIds.length != stakingTargets.length) {
514:            revert WrongArrayLength(chainIds.length, stakingTargets.length);
515:        }
516:        if (chainIds.length != bridgePayloads.length) {
517:            revert WrongArrayLength(chainIds.length, bridgePayloads.length);
518:        }
519:        if (chainIds.length != valueAmounts.length) {
520:            revert WrongArrayLength(chainIds.length, valueAmounts.length);
521:        }
522:
523:        uint256 lastChainId;
524:        uint256 totalValueAmount;
525:        // Traverse all chains
526:        for (uint256 i = 0; i < chainIds.length; ++i) {
527:            // Check that chain Ids are strictly in ascending non-repeatable order
528:            // Also protects from the initial chainId == 0
529:            if (lastChainId >= chainIds[i]) {
530:                revert WrongChainId(chainIds[i]);
531:            }
532:            lastChainId = chainIds[i];
533:
534:            // Staking targets must not be an empty array
535:            if (stakingTargets[i].length == 0) {
536:                revert ZeroValue();
537:            }
538:
539:            // Add to the total value amount
540:            totalValueAmount += valueAmounts[i];
541:
542:            // Check for the maximum number of staking targets
543:            uint256 localMaxNumStakingTargets = maxNumStakingTargets;
544:            if (stakingTargets[i].length > localMaxNumStakingTargets) {
545:                revert Overflow(stakingTargets[i].length, localMaxNumStakingTargets);
546:            }
547:
548:            bytes32 lastTarget;
549:            // Traverse all staking targets
550:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {
551:                // Enforce ascending non-repeatable order of targets
552:                // Also protects from the initial stakingTargets[i][j] == 0
553:                if (uint256(lastTarget) >= uint256(stakingTargets[i][j])) {
554:                    revert WrongAccount(stakingTargets[i][j]);
555:                }
556:                lastTarget = stakingTargets[i][j];
557:            }
558:        }
559:
560:        // Check if the total transferred amount corresponds to the sum of value amounts
561:        if (msg.value != totalValueAmount) {
562:            revert WrongAmount(msg.value, totalValueAmount);
563:        }
564:    }

789:    function claimOwnerIncentives(	// @audit-issue
790:        uint256[] memory unitTypes,
791:        uint256[] memory unitIds
792:    ) external returns (uint256 reward, uint256 topUp) {
793:        // Reentrancy guard
794:        if (_locked > 1) {
795:            revert ReentrancyGuard();
796:        }
797:        _locked = 2;
798:
799:        // Check for the paused state
800:        Pause currentPause = paused;
801:        if (currentPause == Pause.DevIncentivesPaused || currentPause == Pause.AllPaused ||
802:            ITreasury(treasury).paused() == 2) {
803:            revert Paused();
804:        }
805:
806:        // Calculate incentives
807:        (reward, topUp) = ITokenomics(tokenomics).accountOwnerIncentives(msg.sender, unitTypes, unitIds);
808:
809:        bool success;
810:        // Request treasury to transfer funds to msg.sender if reward > 0 or topUp > 0
811:        if ((reward + topUp) > 0) {
812:            // Get the current OLAS balance
813:            uint256 olasBalance;
814:            if (topUp > 0) {
815:                olasBalance = IToken(olas).balanceOf(msg.sender);
816:            }
817:
818:            success = ITreasury(treasury).withdrawToAccount(msg.sender, reward, topUp);
819:
820:            // Check the balance after the OLAS mint, if applicable
821:            if (topUp > 0){
822:                olasBalance = IToken(olas).balanceOf(msg.sender) - olasBalance;
823:                if (olasBalance != topUp) {
824:                    revert WrongAmount(olasBalance, topUp);
825:                }
826:            }
827:        }
828:
829:        // Check if the claim is successful and has at least one non-zero incentive.
830:        if (!success) {
831:            revert ClaimIncentivesFailed(msg.sender, reward, topUp);
832:        }
833:
834:        emit IncentivesClaimed(msg.sender, reward, topUp);
835:
836:        _locked = 1;
837:    }

849:    function calculateStakingIncentives(	// @audit-issue
850:        uint256 numClaimedEpochs,
851:        uint256 chainId,
852:        bytes32 stakingTarget,
853:        uint256 bridgingDecimals
854:    ) public returns (
855:        uint256 totalStakingIncentive,
856:        uint256 totalReturnAmount,
857:        uint256 lastClaimedEpoch,
858:        bytes32 nomineeHash
859:    ) {
860:        // Check for the correct chain Id
861:        if (chainId == 0) {
862:            revert ZeroValue();
863:        }
864:
865:        // Check for the zero address
866:        if (stakingTarget == 0) {
867:            revert ZeroAddress();
868:        }
869:
870:        // Get the staking target nominee hash
871:        nomineeHash = keccak256(abi.encode(IVoteWeighting.Nominee(stakingTarget, chainId)));
872:
873:        uint256 firstClaimedEpoch;
874:        (firstClaimedEpoch, lastClaimedEpoch) =
875:            _checkpointNomineeAndGetClaimedEpochCounters(nomineeHash, numClaimedEpochs);
876:
877:        // Checkpoint staking target nominee in the Vote Weighting contract
878:        IVoteWeighting(voteWeighting).checkpointNominee(stakingTarget, chainId);
879:
880:        // Traverse all the claimed epochs
881:        for (uint256 j = firstClaimedEpoch; j < lastClaimedEpoch; ++j) {
882:            // Get service staking info
883:            ITokenomics.StakingPoint memory stakingPoint =
884:                ITokenomics(tokenomics).mapEpochStakingPoints(j);
885:
886:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);
887:
888:            // Get the staking weight for each epoch and the total weight
889:            // Epoch endTime is used to get the weights info, since otherwise there is a risk of having votes
890:            // accounted for from the next epoch
891:            // totalWeightSum is the overall veOLAS power (bias) across all the voting nominees
892:            (uint256 stakingWeight, uint256 totalWeightSum) =
893:                IVoteWeighting(voteWeighting).nomineeRelativeWeight(stakingTarget, chainId, endTime);
894:
895:            uint256 stakingIncentive;
896:            uint256 returnAmount;
897:
898:            // Adjust the inflation by the maximum amount of veOLAS voted for service staking contracts
899:            // If veOLAS power is lower, it reflects the maximum amount of OLAS allocated for staking
900:            // such that all the inflation is not distributed for a minimal veOLAS power
901:            uint256 availableStakingAmount = stakingPoint.stakingIncentive;
902:
903:            uint256 stakingDiff;
904:            if (availableStakingAmount > totalWeightSum) {
905:                stakingDiff = availableStakingAmount - totalWeightSum;
906:                availableStakingAmount = totalWeightSum;
907:            }
908:
909:            // Compare the staking weight
910:            // 100% = 1e18, in order to compare with minStakingWeight we need to bring it from the range of 0 .. 10_000
911:            if (stakingWeight < uint256(stakingPoint.minStakingWeight) * 1e14) {
912:                // If vote weighting staking weight is lower than the defined threshold - return the staking incentive
913:                returnAmount = ((stakingDiff + availableStakingAmount) * stakingWeight) / 1e18;
914:            } else {
915:                // Otherwise, allocate staking incentive to corresponding contracts
916:                stakingIncentive = (availableStakingAmount * stakingWeight) / 1e18;
917:                // Calculate initial return amount, if stakingDiff > 0
918:                returnAmount = (stakingDiff * stakingWeight) / 1e18;
919:
920:                // availableStakingAmount is not used anymore and can serve as a local maxStakingAmount
921:                availableStakingAmount = stakingPoint.maxStakingAmount;
922:                if (stakingIncentive > availableStakingAmount) {
923:                    // Adjust the return amount
924:                    returnAmount += stakingIncentive - availableStakingAmount;
925:                    // Adjust the staking incentive
926:                    stakingIncentive = availableStakingAmount;
927:                }
928:
929:                // Normalize staking incentive if there is a bridge decimals limiting condition
930:                // Note: only OLAS decimals must be considered
931:                if (bridgingDecimals < 18) {
932:                    uint256 normalizedStakingAmount = stakingIncentive / (10 ** (18 - bridgingDecimals));
933:                    normalizedStakingAmount *= 10 ** (18 - bridgingDecimals);
934:                    // Update return amounts
935:                    // stakingIncentive is always bigger or equal than normalizedStakingAmount
936:                    returnAmount += stakingIncentive - normalizedStakingAmount;
937:                    // Downsize staking incentive to a specified number of bridging decimals
938:                    stakingIncentive = normalizedStakingAmount;
939:                }
940:                // Adjust total staking incentive amount
941:                totalStakingIncentive += stakingIncentive;
942:            }
943:            // Adjust total return amount
944:            totalReturnAmount += returnAmount;
945:        }
946:    }

953:    function claimStakingIncentives(	// @audit-issue
954:        uint256 numClaimedEpochs,
955:        uint256 chainId,
956:        bytes32 stakingTarget,
957:        bytes memory bridgePayload
958:    ) external payable {
959:        // Reentrancy guard
960:        if (_locked > 1) {
961:            revert ReentrancyGuard();
962:        }
963:        _locked = 2;
964:
965:        // Check for zero chain Id
966:        if (chainId == 0) {
967:            revert ZeroValue();
968:        }
969:
970:        // Check for zero target address
971:        if (stakingTarget == 0) {
972:            revert ZeroAddress();
973:        }
974:
975:        // Check the number of claimed epochs
976:        uint256 localMaxNumClaimingEpochs = maxNumClaimingEpochs;
977:        if (numClaimedEpochs > localMaxNumClaimingEpochs) {
978:            revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);
979:        }
980:
981:        // Check for the paused state
982:        Pause currentPause = paused;
983:        if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
984:            ITreasury(treasury).paused() == 2) {
985:            revert Paused();
986:        }
987:
988:        // Get deposit processor bridging decimals corresponding to a chain Id
989:        address depositProcessor = mapChainIdDepositProcessors[chainId];
990:        uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();
991:
992:        // Get the staking incentive to send as a deposit with, and the amount to return back to staking inflation
993:        (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =
994:            calculateStakingIncentives(numClaimedEpochs, chainId, stakingTarget, bridgingDecimals);
995:
996:        // Write last claimed epoch counter to start claiming from the next time
997:        mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;
998:
999:        // Refund returned amount back to tokenomics inflation
1000:        if (returnAmount > 0) {
1001:            ITokenomics(tokenomics).refundFromStaking(returnAmount);
1002:        }
1003:
1004:        uint256 transferAmount;
1005:
1006:        // Check if staking incentive is deposited
1007:        if (stakingIncentive > 0) {
1008:            transferAmount = stakingIncentive;
1009:            // Account for possible withheld OLAS amounts
1010:            // Note: in case of normalized staking incentives with bridging decimals, this is correctly managed
1011:            // as normalized amounts are returned from another side
1012:            uint256 withheldAmount = mapChainIdWithheldAmounts[chainId];
1013:            if (withheldAmount > 0) {
1014:                // If withheld amount is enough to cover all the staking incentives, the transfer of OLAS is not needed
1015:                if (withheldAmount >= transferAmount) {
1016:                    withheldAmount -= transferAmount;
1017:                    transferAmount = 0;
1018:                } else {
1019:                    // Otherwise, reduce the transfer of tokens for the OLAS withheld amount
1020:                    transferAmount -= withheldAmount;
1021:                    withheldAmount = 0;
1022:                }
1023:                mapChainIdWithheldAmounts[chainId] = withheldAmount;
1024:            }
1025:
1026:            // Check if minting is needed as the actual OLAS transfer is required
1027:            if (transferAmount > 0) {
1028:                uint256 olasBalance = IToken(olas).balanceOf(address(this));
1029:
1030:                // Mint tokens to self in order to distribute to the staking deposit processor
1031:                ITreasury(treasury).withdrawToAccount(address(this), 0, transferAmount);
1032:
1033:                // Check the balance after the mint
1034:                olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;
1035:                if (olasBalance != transferAmount) {
1036:                    revert WrongAmount(olasBalance, transferAmount);
1037:                }
1038:            }
1039:
1040:            // Dispense to a service staking target
1041:            _distributeStakingIncentives(chainId, stakingTarget, stakingIncentive, bridgePayload, transferAmount);
1042:        }
1043:
1044:        emit StakingIncentivesClaimed(msg.sender, stakingIncentive, transferAmount, returnAmount);
1045:
1046:        _locked = 1;
1047:    }

1058:    function claimStakingIncentivesBatch(	// @audit-issue
1059:        uint256 numClaimedEpochs,
1060:        uint256[] memory chainIds,
1061:        bytes32[][] memory stakingTargets,
1062:        bytes[] memory bridgePayloads,
1063:        uint256[] memory valueAmounts
1064:    ) external payable {
1065:        // Reentrancy guard
1066:        if (_locked > 1) {
1067:            revert ReentrancyGuard();
1068:        }
1069:        _locked = 2;
1070:
1071:        _checkOrderAndValues(chainIds, stakingTargets, bridgePayloads, valueAmounts);
1072:
1073:        // Check the number of claimed epochs
1074:        uint256 localMaxNumClaimingEpochs = maxNumClaimingEpochs;
1075:        if (numClaimedEpochs > localMaxNumClaimingEpochs) {
1076:            revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);
1077:        }
1078:
1079:        Pause currentPause = paused;
1080:        if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
1081:            ITreasury(treasury).paused() == 2) {
1082:            revert Paused();
1083:        }
1084:
1085:        // Total staking incentives
1086:        // 0: Staking incentive across all the targets to send as a deposit
1087:        // 1: Actual OLAS transfer amount across all the targets
1088:        // 2: Staking incentive to return back to staking inflation
1089:        uint256[] memory totalAmounts;
1090:        // Arrays of staking and transfer amounts
1091:        uint256[][] memory stakingIncentives;
1092:        uint256[] memory transferAmounts;
1093:
1094:        (totalAmounts, stakingIncentives, transferAmounts) = _calculateStakingIncentivesBatch(numClaimedEpochs, chainIds,
1095:            stakingTargets);
1096:
1097:        // Refund returned amount back to tokenomics inflation
1098:        if (totalAmounts[2] > 0) {
1099:            ITokenomics(tokenomics).refundFromStaking(totalAmounts[2]);
1100:        }
1101:
1102:        // Check if staking incentive is deposited
1103:        if (totalAmounts[0] > 0) {
1104:            // Check if minting is needed as the actual OLAS transfer is required
1105:            if (totalAmounts[1] > 0) {
1106:                uint256 olasBalance = IToken(olas).balanceOf(address(this));
1107:
1108:                // Mint tokens to self in order to distribute to staking deposit processors
1109:                ITreasury(treasury).withdrawToAccount(address(this), 0, totalAmounts[1]);
1110:
1111:                // Check the balance after the mint
1112:                olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;
1113:                if (olasBalance != totalAmounts[1]) {
1114:                    revert WrongAmount(olasBalance, totalAmounts[1]);
1115:                }
1116:            }
1117:
1118:            // Dispense all the service staking targets, if the total staking incentive is not equal to zero
1119:            _distributeStakingIncentivesBatch(chainIds, stakingTargets, stakingIncentives, bridgePayloads, transferAmounts,
1120:                valueAmounts);
1121:        }
1122:
1123:        emit StakingIncentivesClaimed(msg.sender, totalAmounts[0], totalAmounts[1], totalAmounts[2]);
1124:
1125:        _locked = 1;
1126:    }
```
[506](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L506-L564), [789](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L789-L837), [849](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L849-L946), [953](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L953-L1047), [1058](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1058-L1126), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

119:    function _sendMessage(	// @audit-issue
120:        address[] memory targets,
121:        uint256[] memory stakingIncentives,
122:        bytes memory bridgePayload,
123:        uint256 transferAmount
124:    ) internal override returns (uint256 sequence) {
125:        // Check for the bridge payload length
126:        if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {
127:            revert IncorrectDataLength(BRIDGE_PAYLOAD_LENGTH, bridgePayload.length);
128:        }
129:
130:        // Decode the staking contract supplemental payload required for bridging tokens
131:        (address refundAccount, uint256 gasPriceBid, uint256 maxSubmissionCostToken, uint256 gasLimitMessage,
132:            uint256 maxSubmissionCostMessage) = abi.decode(bridgePayload, (address, uint256, uint256, uint256, uint256));
133:
134:        // If refundAccount is zero, default to msg.sender
135:        if (refundAccount == address(0)) {
136:            refundAccount = msg.sender;
137:        }
138:
139:        // Check for the tx param limits
140:        // See the function description for the magic values of 1
141:        if (gasPriceBid < 2 || gasLimitMessage < 2 || maxSubmissionCostMessage == 0) {
142:            revert ZeroValue();
143:        }
144:
145:        // Check for the max message gas limit
146:        if (gasLimitMessage > MESSAGE_GAS_LIMIT) {
147:            revert Overflow(gasLimitMessage, MESSAGE_GAS_LIMIT);
148:        }
149:
150:        // Calculate token and message transfer cost
151:        // Reference: https://docs.arbitrum.io/arbos/l1-to-l2-messaging#submission
152:        uint256[] memory cost = new uint256[](2);
153:        if (transferAmount > 0) {
154:            if (maxSubmissionCostToken == 0) {
155:                revert ZeroValue();
156:            }
157:
158:            // Calculate token transfer gas cost
159:            cost[0] = maxSubmissionCostToken + TOKEN_GAS_LIMIT * gasPriceBid;
160:        }
161:
162:        // Calculate cost for the message transfer
163:        cost[1] = maxSubmissionCostMessage + gasLimitMessage * gasPriceBid;
164:        // Get the total cost
165:        uint256 totalCost = cost[0] + cost[1];
166:
167:        // Check fot msg.value to cover the total cost
168:        if (totalCost > msg.value) {
169:            revert LowerThan(msg.value, totalCost);
170:        }
171:
172:        if (transferAmount > 0) {
173:            // Approve tokens for the bridge contract
174:            IToken(olas).approve(l1ERC20Gateway, transferAmount);
175:
176:            // Construct the data for IBridge consisting of 2 pieces:
177:            // uint256 maxSubmissionCost: Max gas deducted from user's L2 balance to cover base submission fee
178:            // bytes memory extraData: empty data
179:            bytes memory submissionCostData = abi.encode(maxSubmissionCostToken, "");
180:
181:            // Transfer OLAS to the staking dispenser contract across the bridge
182:            IBridge(l1TokenRelayer).outboundTransferCustomRefund{value: cost[0]}(olas, refundAccount,
183:                l2TargetDispenser, transferAmount, TOKEN_GAS_LIMIT, gasPriceBid, submissionCostData);
184:        }
185:
186:        // Assemble message data payload
187:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));
188:
189:        // Send a message to the staking dispenser contract on L2 to reflect the transferred OLAS amount
190:        sequence = IBridge(l1MessageRelayer).createRetryableTicket{value: cost[1]}(l2TargetDispenser, 0,
191:            maxSubmissionCostMessage, refundAccount, refundAccount, gasLimitMessage, gasPriceBid, data);
192:    }
```
[119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L119-L192), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

139:    function _processData(bytes memory data) internal {	// @audit-issue
140:        // Reentrancy guard
141:        if (_locked > 1) {
142:            revert ReentrancyGuard();
143:        }
144:        _locked = 2;
145:
146:        // Decode received data
147:        (address[] memory targets, uint256[] memory amounts) = abi.decode(data, (address[], uint256[]));
148:
149:        uint256 batchNonce = stakingBatchNonce;
150:        uint256 localWithheldAmount = 0;
151:        uint256 localPaused = paused;
152:
153:        // Traverse all the targets
154:        for (uint256 i = 0; i < targets.length; ++i) {
155:            address target = targets[i];
156:            uint256 amount = amounts[i];
157:
158:            // Check the target validity address and staking parameters, and get emissions amount
159:            // This is a low level call since it must never revert
160:            bytes memory verifyData = abi.encodeCall(IStakingFactory.verifyInstanceAndGetEmissionsAmount, target);
161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162:
163:            uint256 limitAmount;
164:            // If the function call was successful, check the return value
165:            if (success && returnData.length == 32) {
166:                limitAmount = abi.decode(returnData, (uint256));
167:            }
168:
169:            // If the limit amount is zero, withhold OLAS amount and continue
170:            if (limitAmount == 0) {
171:                // Withhold OLAS for further usage
172:                localWithheldAmount += amount;
173:                emit AmountWithheld(target, amount);
174:
175:                // Proceed to the next target
176:                continue;
177:            }
178:
179:            // Check the amount limit and adjust, if necessary
180:            if (amount > limitAmount) {
181:                uint256 targetWithheldAmount = amount - limitAmount;
182:                localWithheldAmount += targetWithheldAmount;
183:                amount = limitAmount;
184:
185:                emit AmountWithheld(target, targetWithheldAmount);
186:            }
187:
188:            // Check the OLAS balance and the contract being unpaused
189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {
190:                // Approve and transfer OLAS to the service staking target
191:                IToken(olas).approve(target, amount);
192:                IStaking(target).deposit(amount);
193:
194:                emit StakingTargetDeposited(target, amount);
195:            } else {
196:                // Hash of target + amount + batchNonce
197:                bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
198:                // Queue the hash for further redeem
199:                stakingQueueingNonces[queueHash] = true;
200:
201:                emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);
202:            }
203:        }
204:        // Increase the staking batch nonce
205:        stakingBatchNonce = batchNonce + 1;
206:
207:        // Adjust withheld amount, if at least one target has not passed the validity check
208:        if (localWithheldAmount > 0) {
209:            withheldAmount += localWithheldAmount;
210:        }
211:
212:        _locked = 1;
213:    }

412:    function migrate(address newL2TargetDispenser) external {	// @audit-issue
413:        // Reentrancy guard
414:        if (_locked > 1) {
415:            revert ReentrancyGuard();
416:        }
417:        _locked = 2;
418:
419:        // Check for the owner address
420:        if (msg.sender != owner) {
421:            revert OwnerOnly(msg.sender, owner);
422:        }
423:
424:        // Check that the contract is paused
425:        if (paused == 1) {
426:            revert Unpaused();
427:        }
428:
429:        // Check that the migration address is a contract
430:        if (newL2TargetDispenser.code.length == 0) {
431:            revert WrongAccount(newL2TargetDispenser);
432:        }
433:
434:        // Check that the new address is not the current one
435:        if (newL2TargetDispenser == address(this)) {
436:            revert WrongAccount(address(this));
437:        }
438:
439:        // Get OLAS token amount
440:        uint256 amount = IToken(olas).balanceOf(address(this));
441:        // Transfer amount to the new L2 target dispenser
442:        if (amount > 0) {
443:            bool success = IToken(olas).transfer(newL2TargetDispenser, amount);
444:            if (!success) {
445:                revert TransferFailed(olas, address(this), newL2TargetDispenser, amount);
446:            }
447:        }
448:
449:        // Zero the owner
450:        owner = address(0);
451:
452:        emit Migrated(msg.sender, newL2TargetDispenser, amount);
453:
454:        // _locked is now set to 2 for good
455:    }
```
[139](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L139-L213), [412](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L412-L455), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

179:    function verifyInstance(address instance, address implementation) external view returns (bool) {	// @audit-issue
180:        // If the implementations check is true, and the implementation is not whitelisted, the verification is failed
181:        if (implementationsCheck && !mapImplementations[implementation]) {
182:            return false;
183:        }
184:
185:        // Check that instance is the contract when it is not checked against the implementation
186:        if (instance.code.length == 0) {
187:            return false;
188:        }
189:
190:        // Check for the staking parameters
191:        // This is a must have parameter for all staking contracts
192:        uint256 rewardsPerSecond = IStaking(instance).rewardsPerSecond();
193:        if (rewardsPerSecond > rewardsPerSecondLimit) {
194:            return false;
195:        }
196:
197:        // Check for the number of services
198:        // This is a must have parameter for all staking contracts
199:        uint256 numServices = IStaking(instance).maxNumServices();
200:        if (numServices > numServicesLimit) {
201:            return false;
202:        }
203:
204:        // Check staking token
205:        // This is an optional check since there could be staking contracts with native tokens
206:        bytes memory tokenData = abi.encodeCall(IStaking.stakingToken, ());
207:        (bool success, bytes memory returnData) = instance.staticcall(tokenData);
208:
209:        // Check the returnData is the call was successful
210:        if (success) {
211:            // The returned size must be 32 to fit one address
212:            if (returnData.length == 32) {
213:                address token = abi.decode(returnData, (address));
214:                if (token != olas) {
215:                    return false;
216:                }
217:            } else {
218:                return false;
219:            }
220:        }
221:
222:        return true;
223:    }
```
[179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L179-L223), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

278:    function _initialize(	// @audit-issue
279:        StakingParams memory _stakingParams
280:    ) internal {
281:        // Double initialization check
282:        if (serviceRegistry != address(0)) {
283:            revert AlreadyInitialized();
284:        }
285:        
286:        // Initial checks
287:        if (_stakingParams.metadataHash == 0 || _stakingParams.maxNumServices == 0 ||
288:            _stakingParams.rewardsPerSecond == 0 || _stakingParams.livenessPeriod == 0 ||
289:            _stakingParams.numAgentInstances == 0 || _stakingParams.timeForEmissions == 0 ||
290:            _stakingParams.minNumStakingPeriods == 0 || _stakingParams.maxNumInactivityPeriods == 0) {
291:            revert ZeroValue();
292:        }
293:
294:        // Check that the min number of staking periods is not smaller than the max number of inactivity periods
295:        // This check is necessary to avoid an attack scenario when the service is going to stake and unstake without
296:        // any meaningful activity and just occupy the staking slot
297:        if (_stakingParams.minNumStakingPeriods < _stakingParams.maxNumInactivityPeriods) {
298:            revert LowerThan(_stakingParams.minNumStakingPeriods, _stakingParams.maxNumInactivityPeriods);
299:        }
300:
301:        // Check the rest of parameters
302:        if (_stakingParams.minStakingDeposit < 2) {
303:            revert LowerThan(_stakingParams.minStakingDeposit, 2);
304:        }
305:        if (_stakingParams.serviceRegistry == address(0) || _stakingParams.activityChecker == address(0)) {
306:            revert ZeroAddress();
307:        }
308:
309:        // Check for the Activity Checker to be the contract
310:        if (_stakingParams.activityChecker.code.length == 0) {
311:            revert ContractOnly(_stakingParams.activityChecker);
312:        }
313:
314:        // Assign all the required parameters
315:        metadataHash = _stakingParams.metadataHash;
316:        maxNumServices = _stakingParams.maxNumServices;
317:        rewardsPerSecond = _stakingParams.rewardsPerSecond;
318:        minStakingDeposit = _stakingParams.minStakingDeposit;
319:        maxNumInactivityPeriods = _stakingParams.maxNumInactivityPeriods;
320:        livenessPeriod = _stakingParams.livenessPeriod;
321:        timeForEmissions = _stakingParams.timeForEmissions;
322:        numAgentInstances = _stakingParams.numAgentInstances;
323:        serviceRegistry = _stakingParams.serviceRegistry;
324:        activityChecker = _stakingParams.activityChecker;
325:
326:        // Assign optional parameters
327:        threshold = _stakingParams.threshold;
328:        configHash = _stakingParams.configHash;
329:
330:        // Assign agent Ids, if applicable
331:        uint256 agentId;
332:        for (uint256 i = 0; i < _stakingParams.agentIds.length; ++i) {
333:            // Agent Ids must be unique and in ascending order
334:            if (_stakingParams.agentIds[i] <= agentId) {
335:                revert WrongAgentId(_stakingParams.agentIds[i]);
336:            }
337:            agentId = _stakingParams.agentIds[i];
338:            agentIds.push(agentId);
339:        }
340:
341:        // Check for the multisig proxy bytecode hash value
342:        if (_stakingParams.proxyHash == 0) {
343:            revert ZeroValue();
344:        }
345:
346:        // Record provided multisig proxy bytecode hash
347:        proxyHash = _stakingParams.proxyHash;
348:
349:        // Calculate min staking duration
350:        minStakingDuration = _stakingParams.minNumStakingPeriods * livenessPeriod;
351:
352:        // Calculate max allowed inactivity duration
353:        maxInactivityDuration = _stakingParams.maxNumInactivityPeriods * livenessPeriod;
354:
355:        // Calculate emissions amount
356:        emissionsAmount = _stakingParams.rewardsPerSecond * _stakingParams.maxNumServices *
357:            _stakingParams.timeForEmissions;
358:
359:        // Set the checkpoint timestamp to be the deployment one
360:        tsCheckpoint = block.timestamp;
361:    }

591:    function checkpoint() public returns (	// @audit-issue
592:        uint256[] memory,
593:        uint256[][] memory,
594:        uint256[] memory,
595:        uint256[] memory,
596:        uint256[] memory evictServiceIds
597:    )
598:    {
599:        // Calculate staking rewards
600:        (uint256 lastAvailableRewards, uint256 numServices, uint256 totalRewards,
601:            uint256[] memory eligibleServiceIds, uint256[] memory eligibleServiceRewards,
602:            uint256[] memory serviceIds, uint256[][] memory serviceNonces,
603:            uint256[] memory serviceInactivity) = _calculateStakingRewards();
604:
605:        // Get arrays for eligible service Ids and rewards of exact size
606:        uint256[] memory finalEligibleServiceIds;
607:        uint256[] memory finalEligibleServiceRewards;
608:        evictServiceIds = new uint256[](serviceIds.length);
609:        uint256 curServiceId;
610:
611:        // If there are eligible services, proceed with staking calculation and update rewards
612:        if (numServices > 0) {
613:            finalEligibleServiceIds = new uint256[](numServices);
614:            finalEligibleServiceRewards = new uint256[](numServices);
615:            // If total allocated rewards are not enough, adjust the reward value
616:            if (totalRewards > lastAvailableRewards) {
617:                // Traverse all the eligible services and adjust their rewards proportional to leftovers
618:                uint256 updatedReward;
619:                uint256 updatedTotalRewards;
620:                for (uint256 i = 1; i < numServices; ++i) {
621:                    // Calculate the updated reward
622:                    updatedReward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards;
623:                    // Add to the total updated reward
624:                    updatedTotalRewards += updatedReward;
625:                    // Add reward to the overall service reward
626:                    curServiceId = eligibleServiceIds[i];
627:                    finalEligibleServiceIds[i] = eligibleServiceIds[i];
628:                    finalEligibleServiceRewards[i] = updatedReward;
629:                    mapServiceInfo[curServiceId].reward += updatedReward;
630:                }
631:
632:                // Process the first service in the set
633:                updatedReward = (eligibleServiceRewards[0] * lastAvailableRewards) / totalRewards;
634:                updatedTotalRewards += updatedReward;
635:                curServiceId = eligibleServiceIds[0];
636:                finalEligibleServiceIds[0] = eligibleServiceIds[0];
637:                // If the reward adjustment happened to have small leftovers, add it to the first service
638:                if (lastAvailableRewards > updatedTotalRewards) {
639:                    updatedReward += lastAvailableRewards - updatedTotalRewards;
640:                }
641:                finalEligibleServiceRewards[0] = updatedReward;
642:                // Add reward to the overall service reward
643:                mapServiceInfo[curServiceId].reward += updatedReward;
644:                // Set available rewards to zero
645:                lastAvailableRewards = 0;
646:            } else {
647:                // Traverse all the eligible services and add to their rewards
648:                for (uint256 i = 0; i < numServices; ++i) {
649:                    // Add reward to the service overall reward
650:                    curServiceId = eligibleServiceIds[i];
651:                    finalEligibleServiceIds[i] = eligibleServiceIds[i];
652:                    finalEligibleServiceRewards[i] = eligibleServiceRewards[i];
653:                    mapServiceInfo[curServiceId].reward += eligibleServiceRewards[i];
654:                }
655:
656:                // Adjust available rewards
657:                lastAvailableRewards -= totalRewards;
658:            }
659:
660:            // Update the storage value of available rewards
661:            availableRewards = lastAvailableRewards;
662:        }
663:
664:        // If service Ids are returned, then the checkpoint takes place
665:        if (serviceIds.length > 0) {
666:            uint256 eCounter = epochCounter;
667:            numServices = 0;
668:            // Record service inactivities and updated current service nonces
669:            for (uint256 i = 0; i < serviceIds.length; ++i) {
670:                // Get the current service Id
671:                curServiceId = serviceIds[i];
672:                // Record service nonces
673:                mapServiceInfo[curServiceId].nonces = serviceNonces[i];
674:
675:                // Increase service inactivity if it is greater than zero
676:                if (serviceInactivity[i] > 0) {
677:                    // Get the overall continuous service inactivity
678:                    serviceInactivity[i] = mapServiceInfo[curServiceId].inactivity + serviceInactivity[i];
679:                    mapServiceInfo[curServiceId].inactivity = serviceInactivity[i];
680:                    // Check for the maximum allowed inactivity time
681:                    if (serviceInactivity[i] > maxInactivityDuration) {
682:                        // Evict a service if it has been inactive for more than a maximum allowed inactivity time
683:                        evictServiceIds[i] = curServiceId;
684:                        // Increase number of evicted services
685:                        numServices++;
686:                    } else {
687:                        emit ServiceInactivityWarning(eCounter, curServiceId, serviceInactivity[i]);
688:                    }
689:                } else {
690:                    // Otherwise, set it back to zero
691:                    mapServiceInfo[curServiceId].inactivity = 0;
692:                }
693:            }
694:
695:            // Evict inactive services
696:            if (numServices > 0) {
697:                _evict(evictServiceIds, serviceInactivity, numServices);
698:            }
699:
700:            // Record the actual epoch length
701:            uint256 epochLength = block.timestamp - tsCheckpoint;
702:            // Record the current timestamp such that next calculations start from this point of time
703:            tsCheckpoint = block.timestamp;
704:
705:            // Increase the epoch counter
706:            epochCounter = eCounter + 1;
707:
708:            emit Checkpoint(eCounter, lastAvailableRewards, finalEligibleServiceIds, finalEligibleServiceRewards,
709:                epochLength);
710:        }
711:
712:        return (serviceIds, serviceNonces, finalEligibleServiceIds, finalEligibleServiceRewards, evictServiceIds);
713:    }

719:    function stake(uint256 serviceId) external {	// @audit-issue
720:        // Check if there available rewards
721:        if (availableRewards == 0) {
722:            revert NoRewardsAvailable();
723:        }
724:
725:        // Check if the evicted service has not yet unstaked
726:        ServiceInfo storage sInfo = mapServiceInfo[serviceId];
727:        // tsStart being greater than zero means that the service was not yet unstaked: still staking or evicted
728:        if (sInfo.tsStart > 0) {
729:            revert ServiceNotUnstaked(serviceId);
730:        }
731:
732:        // Check for the maximum number of staking services
733:        uint256 numStakingServices = setServiceIds.length;
734:        if (numStakingServices == maxNumServices) {
735:            revert MaxNumServicesReached(maxNumServices);
736:        }
737:
738:        // Check the service conditions for staking
739:        IService.Service memory service = IService(serviceRegistry).getService(serviceId);
740:
741:        // Check the number of agent instances
742:        if (numAgentInstances != service.maxNumAgentInstances) {
743:            revert WrongServiceConfiguration(serviceId);
744:        }
745:
746:        // Check the configuration hash, if applicable
747:        if (configHash != 0 && configHash != service.configHash) {
748:            revert WrongServiceConfiguration(serviceId);
749:        }
750:        // Check the threshold, if applicable
751:        if (threshold > 0 && threshold != service.threshold) {
752:            revert WrongServiceConfiguration(serviceId);
753:        }
754:        // The service must be deployed
755:        if (service.state != IService.ServiceState.Deployed) {
756:            revert WrongServiceState(uint256(service.state), serviceId);
757:        }
758:
759:        // Check that the multisig address corresponds to the authorized multisig proxy bytecode hash
760:        bytes32 multisigProxyHash = keccak256(service.multisig.code);
761:        if (proxyHash != multisigProxyHash) {
762:            revert UnauthorizedMultisig(service.multisig);
763:        }
764:
765:        // Check the agent Ids requirement, if applicable
766:        uint256 size = agentIds.length;
767:        if (size > 0) {
768:            uint256 numAgents = service.agentIds.length;
769:
770:            if (size != numAgents) {
771:                revert WrongServiceConfiguration(serviceId);
772:            }
773:            for (uint256 i = 0; i < numAgents; ++i) {
774:                // Check that the agent Ids
775:                if (agentIds[i] != service.agentIds[i]) {
776:                    revert WrongAgentId(agentIds[i]);
777:                }
778:            }
779:        }
780:
781:        // Check service staking deposit and token, if applicable
782:        _checkTokenStakingDeposit(serviceId, service.securityDeposit, service.agentIds);
783:
784:        // ServiceInfo struct will be an empty one since otherwise the safeTransferFrom above would fail
785:        sInfo.multisig = service.multisig;
786:        sInfo.owner = msg.sender;
787:        // This function might revert if it's incorrectly implemented, however this is not a protocol's responsibility
788:        // It is safe to revert in this place
789:        uint256[] memory nonces = IActivityChecker(activityChecker).getMultisigNonces(service.multisig);
790:        sInfo.nonces = nonces;
791:        sInfo.tsStart = block.timestamp;
792:
793:        // Add the service Id to the set of staked services
794:        setServiceIds.push(serviceId);
795:
796:        // Transfer the service for staking
797:        IService(serviceRegistry).safeTransferFrom(msg.sender, address(this), serviceId);
798:
799:        emit ServiceStaked(epochCounter, serviceId, msg.sender, service.multisig, nonces);
800:    }

805:    function unstake(uint256 serviceId) external returns (uint256 reward) {	// @audit-issue
806:        ServiceInfo storage sInfo = mapServiceInfo[serviceId];
807:        // Check for the service ownership
808:        if (msg.sender != sInfo.owner) {
809:            revert OwnerOnly(msg.sender, sInfo.owner);
810:        }
811:
812:        // Get the staking start time
813:        // Note that if the service info exists, the service is staked or evicted, and thus start time is always valid
814:        uint256 tsStart = sInfo.tsStart;
815:
816:        // Check that the service has staked long enough, or if there are no rewards left
817:        uint256 ts = block.timestamp - tsStart;
818:        if (ts <= minStakingDuration && availableRewards > 0) {
819:            revert NotEnoughTimeStaked(serviceId, ts, minStakingDuration);
820:        }
821:
822:        // Call the checkpoint
823:        (uint256[] memory serviceIds, , , , uint256[] memory evictServiceIds) = checkpoint();
824:
825:        // If the checkpoint was not successful, the serviceIds set is not returned and needs to be allocated
826:        if (serviceIds.length == 0) {
827:            serviceIds = getServiceIds();
828:            evictServiceIds = new uint256[](serviceIds.length);
829:        }
830:
831:        // Get the service index in the set of services
832:        // The index must always exist as the service is currently staked, otherwise it has no record in the map
833:        uint256 idx;
834:        bool inSet;
835:        for (; idx < serviceIds.length; ++idx) {
836:            // Service is still in a global staking set if it is found in the services set,
837:            // and is not present in the evicted set
838:            if (evictServiceIds[idx] == serviceId) {
839:                break;
840:            } else if (serviceIds[idx] == serviceId) {
841:                inSet = true;
842:                break;
843:            }
844:        }
845:
846:        // Get the unstaked service data
847:        reward = sInfo.reward;
848:        uint256[] memory nonces = sInfo.nonces;
849:        address multisig = sInfo.multisig;
850:
851:        // Clear all the data about the unstaked service
852:        // Delete the service info struct
853:        delete mapServiceInfo[serviceId];
854:
855:        // Update the set of staked service Ids
856:        // If the index was not found, the service was evicted and is not part of staked services set
857:        if (inSet) {
858:            setServiceIds[idx] = setServiceIds[setServiceIds.length - 1];
859:            setServiceIds.pop();
860:        }
861:
862:        // Transfer the service back to the owner
863:        // Note that the reentrancy is not possible due to the ServiceInfo struct being deleted
864:        IService(serviceRegistry).safeTransferFrom(address(this), msg.sender, serviceId);
865:
866:        // Transfer accumulated rewards to the service multisig
867:        if (reward > 0) {
868:            _withdraw(multisig, reward);
869:        }
870:
871:        emit ServiceUnstaked(epochCounter, serviceId, msg.sender, multisig, nonces, reward);
872:    }
```
[278](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L278-L361), [591](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L591-L713), [719](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L719-L800), [805](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L805-L872), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

168:    function createStakingInstance(	// @audit-issue
169:        address implementation,
170:        bytes memory initPayload
171:    ) external returns (address payable instance) {
172:        // Reentrancy guard
173:        if (_locked > 1) {
174:            revert ReentrancyGuard();
175:        }
176:        _locked = 2;
177:
178:        // Check for the zero implementation address
179:        if (implementation == address(0)) {
180:            revert ZeroAddress();
181:        }
182:
183:        // Check that the implementation is the contract
184:        if (implementation.code.length == 0) {
185:            revert ContractOnly(implementation);
186:        }
187:
188:        // The payload length must be at least of the a function selector size
189:        if (initPayload.length < SELECTOR_DATA_LENGTH) {
190:            revert IncorrectDataLength(initPayload.length, SELECTOR_DATA_LENGTH);
191:        }
192:
193:        // Provide additional checks, if needed
194:        address localVerifier = verifier;
195:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyImplementation(implementation)) {
196:            revert UnverifiedImplementation(implementation);
197:        }
198:
199:        uint256 localNonce = nonce;
200:        // Get salt based on chain Id and nonce values
201:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));
202:        // Get the deployment data based on the proxy bytecode and the implementation address
203:        bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,
204:            uint256(uint160(implementation)));
205:
206:        // solhint-disable-next-line no-inline-assembly
207:        assembly {
208:            instance := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt)
209:        }
210:        // Check that the proxy creation was successful
211:        if (instance == address(0)) {
212:            revert ProxyCreationFailed(implementation);
213:        }
214:
215:        // Initialize the proxy instance
216:        (bool success, bytes memory returnData) = instance.call(initPayload);
217:        // Process unsuccessful call
218:        if (!success) {
219:            // Get the revert message bytes
220:            if (returnData.length > 0) {
221:                assembly {
222:                    let returnDataSize := mload(returnData)
223:                    revert(add(32, returnData), returnDataSize)
224:                }
225:            } else {
226:                revert InitializationFailed(instance);
227:            }
228:        }
229:
230:        // Check that the created proxy instance does not violate defined limits
231:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyInstance(instance, implementation)) {
232:            revert UnverifiedProxy(instance);
233:        }
234:
235:        // Record instance params
236:        InstanceParams memory instanceParams = InstanceParams(implementation, msg.sender, true);
237:        mapInstanceParams[instance] = instanceParams;
238:        // Increase the nonce
239:        nonce = localNonce + 1;
240:
241:        emit InstanceCreated(msg.sender, instance, implementation);
242:
243:        _locked = 1;
244:    }
```
[168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L168-L244), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

473:    function voteForNomineeWeights(bytes32 account, uint256 chainId, uint256 weight) public {	// @audit-issue
474:        // Get the nominee hash
475:        bytes32 nomineeHash = keccak256(abi.encode(Nominee(account, chainId)));
476:
477:        // Check for the previously removed nominee
478:        if (mapRemovedNominees[nomineeHash] > 0) {
479:            revert NomineeRemoved(account, chainId);
480:        }
481:
482:        // Get user veOLAS slope and check its value
483:        int128 userSlope = IVEOLAS(ve).getLastUserPoint(msg.sender).slope;
484:        if (userSlope < 0) {
485:            revert NegativeSlope(msg.sender, userSlope);
486:        }
487:
488:        uint256 lockEnd = IVEOLAS(ve).lockedEnd(msg.sender);
489:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;
490:
491:        // Check for the lock end expiration
492:        if (nextTime >= lockEnd) {
493:            revert LockExpired(msg.sender, lockEnd, nextTime);
494:        }
495:
496:        // Check for the weight number
497:        if (weight > MAX_WEIGHT) {
498:            revert Overflow(weight, MAX_WEIGHT);
499:        }
500:
501:        // Check for the last voting time
502:        uint256 nextAllowedVotingTime = lastUserVote[msg.sender][nomineeHash] + WEIGHT_VOTE_DELAY;
503:        if (nextAllowedVotingTime > block.timestamp) {
504:            revert VoteTooOften(msg.sender, block.timestamp, nextAllowedVotingTime);
505:        }
506:
507:        // Prepare old and new slopes and biases
508:        VotedSlope memory oldSlope = voteUserSlopes[msg.sender][nomineeHash];
509:        uint256 oldBias;
510:        if (oldSlope.end > nextTime) {
511:            oldBias = oldSlope.slope * (oldSlope.end - nextTime);
512:        }
513:
514:        VotedSlope memory newSlope = VotedSlope({
515:            slope: uint256(uint128(userSlope)) * weight / MAX_WEIGHT,
516:            end: lockEnd,
517:            power: weight
518:        });
519:
520:        uint256 newBias = newSlope.slope * (lockEnd - nextTime);
521:
522:        uint256 powerUsed = voteUserPower[msg.sender];
523:        powerUsed = powerUsed + newSlope.power - oldSlope.power;
524:        voteUserPower[msg.sender] = powerUsed;
525:        if (powerUsed > MAX_WEIGHT) {
526:            revert Overflow(powerUsed, MAX_WEIGHT);
527:        }
528:
529:        // Remove old and schedule new slope changes
530:        // Remove slope changes for old slopes
531:        // Schedule recording of initial slope for nextTime
532:        pointsWeight[nomineeHash][nextTime].bias = _maxAndSub(_getWeight(account, chainId) + newBias, oldBias);
533:        pointsSum[nextTime].bias = _maxAndSub(_getSum() + newBias, oldBias);
534:        if (oldSlope.end > nextTime) {
535:            pointsWeight[nomineeHash][nextTime].slope =
536:                _maxAndSub(pointsWeight[nomineeHash][nextTime].slope + newSlope.slope, oldSlope.slope);
537:            pointsSum[nextTime].slope = _maxAndSub(pointsSum[nextTime].slope + newSlope.slope, oldSlope.slope);
538:        } else {
539:            pointsWeight[nomineeHash][nextTime].slope += newSlope.slope;
540:            pointsSum[nextTime].slope += newSlope.slope;
541:        }
542:        if (oldSlope.end > block.timestamp) {
543:            // Cancel old slope changes if they still didn't happen
544:            changesWeight[nomineeHash][oldSlope.end] -= oldSlope.slope;
545:            changesSum[oldSlope.end] -= oldSlope.slope;
546:        }
547:        // Add slope changes for new slopes
548:        changesWeight[nomineeHash][newSlope.end] += newSlope.slope;
549:        changesSum[newSlope.end] += newSlope.slope;
550:
551:        voteUserSlopes[msg.sender][nomineeHash] = newSlope;
552:
553:        // Record last action time
554:        lastUserVote[msg.sender][nomineeHash] = block.timestamp;
555:
556:        emit VoteForNominee(msg.sender, account, chainId, weight);
557:    }
```
[473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L473-L557), 


#### Recommendation

Regularly analyze your Solidity contracts for functions with high cyclomatic complexity. Consider refactoring these functions into smaller, more focused units. This could involve breaking down complex functions into multiple simpler functions, reducing the number of conditional branches, or simplifying logic where possible. Such refactoring improves the readability and testability of your code and reduces the likelihood of defects. Additionally, simpler functions are easier to audit and maintain, enhancing the overall security and robustness of your contract. Employ tools or metrics to periodically assess the complexity of your functions as part of your development and maintenance processes.

### Consider only defining one library/interface/contract per sol file
Combining multiple libraries, interfaces, or contracts in a single .sol file can lead to clutter, reduced readability, and versioning issues. Resolution: Adopt the best practice of defining only one library, interface, or contract per Solidity file. This modular approach enhances clarity, simplifies unit testing, and streamlines code review. Furthermore, segregating components makes version management easier, as updates to one component won't necessitate changes to a file housing multiple unrelated components. Structured file management can further assist in avoiding naming collisions and ensure smoother integration into larger systems or DApps.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

4:import {convert, UD60x18} from "@prb/math/src/UD60x18.sol";	// @audit-issue
5:import {TokenomicsConstants} from "./TokenomicsConstants.sol";
6:import {IDonatorBlacklist} from "./interfaces/IDonatorBlacklist.sol";
7:import {IErrorsTokenomics} from "./interfaces/IErrorsTokenomics.sol";
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L4-L7), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

4:/// @title TokenomicsConstants - Smart contract with tokenomics constants	// @audit-issue
5:/// @author Aleksandr Kuperman - <aleksandr.kuperman@valory.xyz>
6:/// @author Andrey Lebedev - <andrey.lebedev@valory.xyz>
7:/// @author Mariapia Moscatiello - <mariapia.moscatiello@valory.xyz>
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L4-L7), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

4:// Deposit Processor interface	// @audit-issue
5:interface IDepositProcessor {
6:    /// @dev Sends a single message to the L2 side via a corresponding bridge.
7:    /// @param target Staking target addresses.
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L4-L7), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

4:import {DefaultDepositProcessorL1, IToken} from "./DefaultDepositProcessorL1.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

4:import {DefaultTargetDispenserL2} from "./DefaultTargetDispenserL2.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

4:import {IBridgeErrors} from "../interfaces/IBridgeErrors.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

4:import {DefaultTargetDispenserL2} from "./DefaultTargetDispenserL2.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

4:import {DefaultDepositProcessorL1, IToken} from "./DefaultDepositProcessorL1.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

4:interface IToken {	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

4:import {DefaultTargetDispenserL2} from "./DefaultTargetDispenserL2.sol";	// @audit-issue
5:import {TokenBase, TokenReceiver} from "wormhole-solidity-sdk/TokenBase.sol";
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L4-L5), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

4:import {DefaultDepositProcessorL1, IToken} from "./DefaultDepositProcessorL1.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

4:import {DefaultTargetDispenserL2} from "./DefaultTargetDispenserL2.sol";	// @audit-issue
5:import {FxBaseChildTunnel} from "../../lib/fx-portal/contracts/tunnel/FxBaseChildTunnel.sol";
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L4-L5), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

4:import {DefaultDepositProcessorL1} from "./DefaultDepositProcessorL1.sol";	// @audit-issue
5:import {TokenBase, TokenSender} from "wormhole-solidity-sdk/TokenBase.sol";
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L4-L5), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

4:import "./DefaultTargetDispenserL2.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

4:import {IBridgeErrors} from "../interfaces/IBridgeErrors.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

4:import {DefaultDepositProcessorL1, IToken} from "./DefaultDepositProcessorL1.sol";	// @audit-issue
5:import {FxBaseRootTunnel} from "../../lib/fx-portal/contracts/tunnel/FxBaseRootTunnel.sol";
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L4-L5), 


```solidity
Path: ./tokenomics/contracts/interfaces/IDonatorBlacklist.sol

4:/// @dev DonatorBlacklist interface.	// @audit-issue
5:interface IDonatorBlacklist {
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L4-L5), 


```solidity
Path: ./tokenomics/contracts/interfaces/IBridgeErrors.sol

4:interface IBridgeErrors {	// @audit-issue
5:    /// @dev Only `manager` has a privilege, but the `sender` was provided.
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L4-L5), 


```solidity
Path: ./tokenomics/contracts/interfaces/IErrorsTokenomics.sol

4:/// @dev Errors.	// @audit-issue
5:interface IErrorsTokenomics {
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L4-L5), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

4:/// @dev Failure of a token transfer.	// @audit-issue
5:/// @param token Address of a token.
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L4-L5), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

4:// Staking instance interface	// @audit-issue
5:interface IStaking {
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L4-L5), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

4:import {StakingBase} from "./StakingBase.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L4-L4), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

4:// Multisig interface	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L4-L4), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

4:import {ERC721TokenReceiver} from "../../lib/solmate/src/tokens/ERC721.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L4-L4), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

4:import {StakingBase} from "./StakingBase.sol";	// @audit-issue
5:import {SafeTransferLib} from "../utils/SafeTransferLib.sol";
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L4-L5), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

4:/// @dev Zero implementation address.	// @audit-issue
5:error ZeroImplementationAddress();
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L4-L5), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

4:import {StakingProxy} from "./StakingProxy.sol";	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L4-L4), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

4:// Dispenser interface	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L4-L4), 


#### Recommendation

Adopt a modular file structure in your Solidity projects by defining only one library, interface, or contract per file. This approach significantly enhances the clarity and readability of your code, making it easier to manage, test, and review. It also simplifies version control, as updates to individual components are isolated to their respective files, reducing the risk of unintended side effects. Organize your project directory to reflect this structure, with a clear naming convention that matches file names to their contained contracts, libraries, or interfaces. This organization not only prevents naming collisions but also facilitates smoother integration into larger systems or decentralized applications (DApps).

### Reduce deployment costs by tweaking contracts' metadata
When solidity generates the bytecode for the smart contract to be deployed, it appends metadata about the compilation at the end of the bytecode.
By default, the solidity compiler appends metadata at the end of the “actual” initcode, which gets stored to the blockchain when the constructor finishes executing.
Consider tweaking the metadata to avoid this unnecessary allocation. A full guide can be found [here](https://www.rareskills.io/post/solidity-metadata).
```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1), 


#### Recommendation

See [this](https://www.rareskills.io/post/solidity-metadata) link, at its bottom, for full details

### All verbatim blocks are considered identical by deduplicator and can incorrectly be unified
The Solidity Team reported a bug on October 24, 2023, affecting Yul code using the verbatim builtin, specifically in the Block Deduplicator optimizer step. This bug, present since Solidity version 0.8.5, caused incorrect deduplication of verbatim assembly items surrounded by identical opcodes, considering them identical regardless of their data. The bug was confined to pure Yul compilation with optimization enabled and was unlikely to be exploited as an attack vector. The conditions triggering the bug were very specific, and its occurrence was deemed to have a low likelihood. The bug was rated with an overall low score due to these factors.
```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1), 


#### Recommendation

Review and assess any Solidity contracts, especially those involving Yul code, that may be impacted by this deduplication bug. If your contracts rely on the Block Deduplicator optimizer and use verbatim blocks in a way that could be affected by this issue, consider updating your Solidity version to one where this bug is fixed, or adjust your contract to avoid this specific scenario. Stay informed about updates from the Solidity Team regarding this and similar issues, and regularly update your Solidity compiler to the latest version to benefit from bug fixes and optimizations. Given the specific and limited nature of this bug, its impact may be minimal, but caution is advised for contracts with complex assembly code or those heavily reliant on optimizer behaviors.

### Consider adding formal verification proofs
Formal verification is the act of proving or disproving the correctness of intended algorithms underlying a system with respect to a certain formal specification/property/invariant, using formal methods of mathematics.
```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1), 


#### Recommendation

Consider integrating formal verification into your Solidity contract development process. This can be done by defining formal specifications and properties that your contract should adhere to and using mathematical methods to verify these aspects. Tools and platforms like Certora Prover, Scribble, or OpenZeppelin's test environment can assist in this process. Formal verification should complement traditional testing and auditing methods, offering an additional layer of security assurance. Keep in mind that formal verification requires a thorough understanding of mathematical logic and contract specifications, so it may necessitate additional resources or expertise. Nevertheless, the investment in formal verification can significantly enhance the trustworthiness and robustness of your smart contracts.

### Contracts should have full test coverage
While 100% code coverage does not guarantee that there are no bugs, it often will catch easy-to-find bugs, and will ensure that there are fewer regressions when the code invariably has to be modified. Furthermore, in order to get full coverage, code authors will often have to re-organize their code so that it is more modular, so that each component can be tested separately, which reduces interdependencies between modules and layers, and makes for code that is easier to reason about and audit.
```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1), 


#### Recommendation

Consider writing test cases.

### Large or complicated code bases should implement invariant tests
This includes: large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts. Invariant fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold. Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and invariant fuzzers may help significantly.
```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1), 


#### Recommendation

Consider writing invariant test cases.

### Consider adding formal verification proofs
Consider using formal verification to mathematically prove that your code does what is intended, and does not have any edge cases with unexpected behavior. The solidity compiler itself has this functionality [built in](https://docs.soliditylang.org/en/latest/smtchecker.html#smtchecker-and-formal-verification)
```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1), 


#### Recommendation

Consider using formal verification.

### NatSpec: Whitespace in Expressions
See the [Whitespace in Expressions](https://docs.soliditylang.org/en/latest/style-guide.html#whitespace-in-expressions) section of the Solidity Style Guide.


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

883:            ITokenomics.StakingPoint memory stakingPoint =	// @audit-issue: Variable declaration should be like `uint256 x = 3`
```
[883](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L883-L883), 


#### Recommendation

Review and align your Solidity code with the whitespace guidelines provided in the Solidity Style Guide, particularly for expressions. Ensure consistent spacing around operators and commas, and adopt a uniform style for indentation and line breaks in complex expressions. This alignment not only enhances the readability of your code but also promotes a standardized coding style that aligns with community best practices. Utilize linters or automated code formatting tools, where possible, to enforce these whitespace guidelines consistently across your codebase.

### Assembly blocks should have extensive comments
Assembly blocks take a lot more time to audit than normal Solidity code, and often have gotchas and side-effects that the Solidity versions of the same code do not. Consider adding more comments explaining what is being done in every step of the assembly code, and describe why assembly is being used instead of Solidity.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

517:        assembly {	// @audit-issue
518:            implementation := sload(PROXY_TOKENOMICS)
519:        }

538:        assembly {	// @audit-issue
539:            sstore(PROXY_TOKENOMICS, implementation)
540:        }

1083:        assembly {	// @audit-issue
1084:            implementation := sload(PROXY_TOKENOMICS)
1085:        }
```
[517](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L517-L519), [538](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L538-L540), [1083](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1083-L1085), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

34:        assembly {	// @audit-issue
35:            sstore(SERVICE_STAKING_PROXY, implementation)
36:        }

41:        assembly {	// @audit-issue
42:            let implementation := sload(SERVICE_STAKING_PROXY)
43:            calldatacopy(0, 0, calldatasize())
44:            let success := delegatecall(gas(), implementation, 0, calldatasize(), 0, 0)
45:            returndatacopy(0, 0, returndatasize())
46:            if eq(success, 0) {
47:                revert(0, returndatasize())
48:            }
49:            return(0, returndatasize())
50:        }

55:        assembly {	// @audit-issue
56:            implementation := sload(SERVICE_STAKING_PROXY)
57:        }
```
[34](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L34-L36), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L41-L50), [55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L55-L57), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

207:        assembly {	// @audit-issue
208:            instance := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt)
209:        }

221:                assembly {	// @audit-issue
222:                    let returnDataSize := mload(returnData)
223:                    revert(add(32, returnData), returnDataSize)
224:                }
```
[207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L207-L209), [221](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L221-L224), 


#### Recommendation

Thoroughly comment assembly blocks in Solidity to aid in understanding and auditing. Explain each step of the assembly code and justify the use of assembly over Solidity, highlighting any side effects or nuances not present in high-level code.

### NatSpec: Contracts should be named using CapWords style
According to the Solidity [style guide](https://docs.soliditylang.org/en/latest/style-guide.html#contract-and-library-names) Contract names should be in CapWords style (CamelCase)

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

10:interface IOLAS {	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L10-L10), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

16:interface IVEOLAS {	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L16-L16), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### Invalid NatSpec comment style
NatSpec must begin with `///`, or use the `/** ... */` syntax. The compiler doesn't count `//` or `/* ... */` as NatSpec

```solidity
Path: ./tokenomics/contracts/interfaces/IBridgeErrors.sol

89:    // @dev Reentrancy guard.	// @audit-issue
```
[89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L89-L89), 


#### Recommendation

Ensure that all NatSpec comments in your Solidity contracts adhere to the correct syntax: use `///` for single-line comments and `/** ... */` for multi-line comments. Review and update your existing comments to align with these guidelines. Avoid using regular comment syntax `//` or `/* ... */` for NatSpec documentation, as these will not be recognized by the compiler for generating automated documentation. Correctly formatted NatSpec comments improve the readability of your code and are essential for generating clear and useful documentation for users and developers interacting with your contract interfaces.

### NatSpec: Contract declarations should have `@author` tag
In the world of decentralized code, giving credit is key. NatSpec's `@author` tag acknowledges the minds behind the code. It appears this Solidity contract omits the `@author` directive in its NatSpec annotations. Properly attributing code to its contributors not only recognizes effort but also aids in establishing trust and credibility. [Dive Deeper into NatSpec Guidelines](https://docs.soliditylang.org/en/develop/natspec-format.html)

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

10:interface IOLAS {	// @audit-issue missing `@author` tag

17:interface IToken {	// @audit-issue missing `@author` tag

29:interface ITreasury {	// @audit-issue missing `@author` tag

37:interface IServiceRegistry {	// @audit-issue missing `@author` tag

58:interface IVotingEscrow {	// @audit-issue missing `@author` tag
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L10-L10), [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L17-L17), [29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L29-L29), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L37-L37), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L58-L58), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

5:interface IDepositProcessor {	// @audit-issue missing `@author` tag

44:interface IToken {	// @audit-issue missing `@author` tag

58:interface ITokenomics {	// @audit-issue missing `@author` tag

142:interface ITreasury {	// @audit-issue missing `@author` tag

160:interface IVoteWeighting {	// @audit-issue missing `@author` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L5-L5), [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L44-L44), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L58-L58), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L142-L142), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L160-L160), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@author` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@author` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

6:interface IDispenser {	// @audit-issue missing `@author` tag

10:interface IToken {	// @audit-issue missing `@author` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L6-L6), [10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L10-L10), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@author` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@author` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

4:interface IToken {	// @audit-issue missing `@author` tag

18:interface IStaking {	// @audit-issue missing `@author` tag

24:interface IStakingFactory {	// @audit-issue missing `@author` tag
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L4-L4), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L18-L18), [24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L24-L24), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

7:interface IBridge {	// @audit-issue missing `@author` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L7-L7), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@author` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@author` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

7:interface IStaking {	// @audit-issue missing `@author` tag

14:interface IStakingFactory {	// @audit-issue missing `@author` tag

22:interface IToken {	// @audit-issue missing `@author` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L14-L14), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L22-L22), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

7:interface IBridge {	// @audit-issue missing `@author` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L7-L7), 


```solidity
Path: ./tokenomics/contracts/interfaces/IDonatorBlacklist.sol

5:interface IDonatorBlacklist {	// @audit-issue missing `@author` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L5-L5), 


```solidity
Path: ./tokenomics/contracts/interfaces/IBridgeErrors.sol

4:interface IBridgeErrors {	// @audit-issue missing `@author` tag
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/interfaces/IErrorsTokenomics.sol

5:interface IErrorsTokenomics {	// @audit-issue missing `@author` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L5-L5), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

16:library SafeTransferLib {	// @audit-issue missing `@author` tag
```
[16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L16-L16), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

5:interface IStaking {	// @audit-issue missing `@author` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L5-L5), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

5:interface IMultisig {	// @audit-issue missing `@author` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L5-L5), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

7:interface IActivityChecker {	// @audit-issue missing `@author` tag

30:interface IService {	// @audit-issue missing `@author` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L7-L7), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L30-L30), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

11:interface IServiceTokenUtility {	// @audit-issue missing `@author` tag
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L11-L11), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

7:interface IStaking {	// @audit-issue missing `@author` tag

14:interface IStakingVerifier {	// @audit-issue missing `@author` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L14-L14), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

5:interface IDispenser {	// @audit-issue missing `@author` tag

16:interface IVEOLAS {	// @audit-issue missing `@author` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L5-L5), [16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L16-L16), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### NatSpec: Contract declarations should have `@dev` tag
NatSpec comments are a critical part of Solidity's documentation system, designed to help developers and others understand the behavior and purpose of a contract. The `@dev` tag, in particular, provides context and insight into the contract's development considerations. A missing `@dev` comment can lead to misunderstandings about the contract, making it harder for others to contribute to or use the contract effectively. Therefore, it's highly recommended to include `@dev` comments in the documentation to enhance code readability and maintainability. [Dive Deeper into NatSpec Guidelines](https://docs.soliditylang.org/en/develop/natspec-format.html)

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

10:interface IOLAS {	// @audit-issue missing `@dev` tag

17:interface IToken {	// @audit-issue missing `@dev` tag

29:interface ITreasury {	// @audit-issue missing `@dev` tag

37:interface IServiceRegistry {	// @audit-issue missing `@dev` tag

58:interface IVotingEscrow {	// @audit-issue missing `@dev` tag
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L10-L10), [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L17-L17), [29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L29-L29), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L37-L37), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L58-L58), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

8:abstract contract TokenomicsConstants {	// @audit-issue missing `@dev` tag
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L8-L8), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

5:interface IDepositProcessor {	// @audit-issue missing `@dev` tag

44:interface IToken {	// @audit-issue missing `@dev` tag

58:interface ITokenomics {	// @audit-issue missing `@dev` tag

142:interface ITreasury {	// @audit-issue missing `@dev` tag

160:interface IVoteWeighting {	// @audit-issue missing `@dev` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L5-L5), [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L44-L44), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L58-L58), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L142-L142), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L160-L160), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@dev` tag

70:contract ArbitrumDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue missing `@dev` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L6-L6), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L70-L70), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@dev` tag

37:contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue missing `@dev` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L6-L6), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L37-L37), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

6:interface IDispenser {	// @audit-issue missing `@dev` tag

10:interface IToken {	// @audit-issue missing `@dev` tag

22:abstract contract DefaultDepositProcessorL1 is IBridgeErrors {	// @audit-issue missing `@dev` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L6-L6), [10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L10-L10), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@dev` tag

23:contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue missing `@dev` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L6-L6), [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L23-L23), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@dev` tag

59:contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue missing `@dev` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L6-L6), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

4:interface IToken {	// @audit-issue missing `@dev` tag

18:interface IStaking {	// @audit-issue missing `@dev` tag

24:interface IStakingFactory {	// @audit-issue missing `@dev` tag
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L4-L4), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L18-L18), [24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L24-L24), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

7:interface IBridge {	// @audit-issue missing `@dev` tag

50:contract WormholeTargetDispenserL2 is DefaultTargetDispenserL2, TokenReceiver {	// @audit-issue missing `@dev` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L7-L7), [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@dev` tag

32:contract GnosisDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue missing `@dev` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L6-L6), [32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L32-L32), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

11:contract PolygonTargetDispenserL2 is DefaultTargetDispenserL2, FxBaseChildTunnel {	// @audit-issue missing `@dev` tag
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

11:contract WormholeDepositProcessorL1 is DefaultDepositProcessorL1, TokenSender {	// @audit-issue missing `@dev` tag
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@dev` tag

26:contract GnosisTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue missing `@dev` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L6-L6), [26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L26-L26), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

7:interface IStaking {	// @audit-issue missing `@dev` tag

14:interface IStakingFactory {	// @audit-issue missing `@dev` tag

22:interface IToken {	// @audit-issue missing `@dev` tag

45:abstract contract DefaultTargetDispenserL2 is IBridgeErrors {	// @audit-issue missing `@dev` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L14-L14), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L22-L22), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L45-L45), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

7:interface IBridge {	// @audit-issue missing `@dev` tag

22:contract PolygonDepositProcessorL1 is DefaultDepositProcessorL1, FxBaseRootTunnel {	// @audit-issue missing `@dev` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L7-L7), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./tokenomics/contracts/interfaces/IBridgeErrors.sol

4:interface IBridgeErrors {	// @audit-issue missing `@dev` tag
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L4-L4), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

5:interface IStaking {	// @audit-issue missing `@dev` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L5-L5), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

5:interface IMultisig {	// @audit-issue missing `@dev` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L5-L5), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

7:interface IActivityChecker {	// @audit-issue missing `@dev` tag

30:interface IService {	// @audit-issue missing `@dev` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L7-L7), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L30-L30), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

7:interface IStaking {	// @audit-issue missing `@dev` tag

14:interface IStakingVerifier {	// @audit-issue missing `@dev` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L14-L14), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

5:interface IDispenser {	// @audit-issue missing `@dev` tag

16:interface IVEOLAS {	// @audit-issue missing `@dev` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L5-L5), [16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L16-L16), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### NatSpec: Contract declarations should have `@notice` tag
The `@notice` is used to explain to users what the contract does. The compiler interprets `///` or `/**` comments [as this tag](https://docs.soliditylang.org/en/latest/natspec-format.html#tags) if one wasn't explicitly provided.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

10:interface IOLAS {	// @audit-issue missing `@notice` tag

17:interface IToken {	// @audit-issue missing `@notice` tag

29:interface ITreasury {	// @audit-issue missing `@notice` tag

37:interface IServiceRegistry {	// @audit-issue missing `@notice` tag

58:interface IVotingEscrow {	// @audit-issue missing `@notice` tag

254:contract Tokenomics is TokenomicsConstants {	// @audit-issue missing `@notice` tag
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L10-L10), [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L17-L17), [29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L29-L29), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L37-L37), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L58-L58), [254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L254-L254), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

8:abstract contract TokenomicsConstants {	// @audit-issue missing `@notice` tag
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L8-L8), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

5:interface IDepositProcessor {	// @audit-issue missing `@notice` tag

44:interface IToken {	// @audit-issue missing `@notice` tag

58:interface ITokenomics {	// @audit-issue missing `@notice` tag

142:interface ITreasury {	// @audit-issue missing `@notice` tag

160:interface IVoteWeighting {	// @audit-issue missing `@notice` tag

253:contract Dispenser {	// @audit-issue missing `@notice` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L5-L5), [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L44-L44), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L58-L58), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L142-L142), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L160-L160), [253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L253-L253), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@notice` tag

70:contract ArbitrumDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue missing `@notice` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L6-L6), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L70-L70), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@notice` tag

37:contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue missing `@notice` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L6-L6), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L37-L37), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

6:interface IDispenser {	// @audit-issue missing `@notice` tag

10:interface IToken {	// @audit-issue missing `@notice` tag

22:abstract contract DefaultDepositProcessorL1 is IBridgeErrors {	// @audit-issue missing `@notice` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L6-L6), [10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L10-L10), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@notice` tag

23:contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue missing `@notice` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L6-L6), [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L23-L23), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@notice` tag

59:contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue missing `@notice` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L6-L6), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

4:interface IToken {	// @audit-issue missing `@notice` tag

18:interface IStaking {	// @audit-issue missing `@notice` tag

24:interface IStakingFactory {	// @audit-issue missing `@notice` tag

50:contract EthereumDepositProcessor {	// @audit-issue missing `@notice` tag
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L4-L4), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L18-L18), [24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L24-L24), [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

7:interface IBridge {	// @audit-issue missing `@notice` tag

50:contract WormholeTargetDispenserL2 is DefaultTargetDispenserL2, TokenReceiver {	// @audit-issue missing `@notice` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L7-L7), [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@notice` tag

32:contract GnosisDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue missing `@notice` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L6-L6), [32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L32-L32), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

11:contract PolygonTargetDispenserL2 is DefaultTargetDispenserL2, FxBaseChildTunnel {	// @audit-issue missing `@notice` tag
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

11:contract WormholeDepositProcessorL1 is DefaultDepositProcessorL1, TokenSender {	// @audit-issue missing `@notice` tag
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L11-L11), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@notice` tag

26:contract GnosisTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue missing `@notice` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L6-L6), [26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L26-L26), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

7:interface IStaking {	// @audit-issue missing `@notice` tag

14:interface IStakingFactory {	// @audit-issue missing `@notice` tag

22:interface IToken {	// @audit-issue missing `@notice` tag

45:abstract contract DefaultTargetDispenserL2 is IBridgeErrors {	// @audit-issue missing `@notice` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L14-L14), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L22-L22), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L45-L45), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

7:interface IBridge {	// @audit-issue missing `@notice` tag

22:contract PolygonDepositProcessorL1 is DefaultDepositProcessorL1, FxBaseRootTunnel {	// @audit-issue missing `@notice` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L7-L7), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./tokenomics/contracts/interfaces/IDonatorBlacklist.sol

5:interface IDonatorBlacklist {	// @audit-issue missing `@notice` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L5-L5), 


```solidity
Path: ./tokenomics/contracts/interfaces/IBridgeErrors.sol

4:interface IBridgeErrors {	// @audit-issue missing `@notice` tag
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/interfaces/IErrorsTokenomics.sol

5:interface IErrorsTokenomics {	// @audit-issue missing `@notice` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L5-L5), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

16:library SafeTransferLib {	// @audit-issue missing `@notice` tag
```
[16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L16-L16), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

5:interface IStaking {	// @audit-issue missing `@notice` tag

43:contract StakingVerifier {	// @audit-issue missing `@notice` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L5-L5), [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L43-L43), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

17:contract StakingNativeToken is StakingBase {	// @audit-issue missing `@notice` tag
```
[17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L17-L17), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

5:interface IMultisig {	// @audit-issue missing `@notice` tag

18:contract StakingActivityChecker {	// @audit-issue missing `@notice` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L5-L5), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L18-L18), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

7:interface IActivityChecker {	// @audit-issue missing `@notice` tag

30:interface IService {	// @audit-issue missing `@notice` tag

168:abstract contract StakingBase is ERC721TokenReceiver {	// @audit-issue missing `@notice` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L7-L7), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L30-L30), [168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L168-L168), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

11:interface IServiceTokenUtility {	// @audit-issue missing `@notice` tag

44:contract StakingToken is StakingBase {	// @audit-issue missing `@notice` tag
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L11-L11), [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L44-L44), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

21:contract StakingProxy {	// @audit-issue missing `@notice` tag
```
[21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L21-L21), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

7:interface IStaking {	// @audit-issue missing `@notice` tag

14:interface IStakingVerifier {	// @audit-issue missing `@notice` tag

81:contract StakingFactory {	// @audit-issue missing `@notice` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L14-L14), [81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L81-L81), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

5:interface IDispenser {	// @audit-issue missing `@notice` tag

16:interface IVEOLAS {	// @audit-issue missing `@notice` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L5-L5), [16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L16-L16), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### NatSpec: Contract declarations should have `@title` tag
The `@title` is used to explain to users what the contract does. The compiler interprets `///` or `/**` comments [as this tag](https://docs.soliditylang.org/en/latest/natspec-format.html#tags) if one wasn't explicitly provided.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

10:interface IOLAS {	// @audit-issue missing `@title` tag

17:interface IToken {	// @audit-issue missing `@title` tag

29:interface ITreasury {	// @audit-issue missing `@title` tag

37:interface IServiceRegistry {	// @audit-issue missing `@title` tag

58:interface IVotingEscrow {	// @audit-issue missing `@title` tag
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L10-L10), [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L17-L17), [29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L29-L29), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L37-L37), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L58-L58), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

5:interface IDepositProcessor {	// @audit-issue missing `@title` tag

44:interface IToken {	// @audit-issue missing `@title` tag

58:interface ITokenomics {	// @audit-issue missing `@title` tag

142:interface ITreasury {	// @audit-issue missing `@title` tag

160:interface IVoteWeighting {	// @audit-issue missing `@title` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L5-L5), [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L44-L44), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L58-L58), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L142-L142), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L160-L160), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@title` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@title` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

6:interface IDispenser {	// @audit-issue missing `@title` tag

10:interface IToken {	// @audit-issue missing `@title` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L6-L6), [10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L10-L10), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@title` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@title` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

4:interface IToken {	// @audit-issue missing `@title` tag

18:interface IStaking {	// @audit-issue missing `@title` tag

24:interface IStakingFactory {	// @audit-issue missing `@title` tag
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L4-L4), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L18-L18), [24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L24-L24), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

7:interface IBridge {	// @audit-issue missing `@title` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L7-L7), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue missing `@title` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue missing `@title` tag
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L6-L6), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

7:interface IStaking {	// @audit-issue missing `@title` tag

14:interface IStakingFactory {	// @audit-issue missing `@title` tag

22:interface IToken {	// @audit-issue missing `@title` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L14-L14), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L22-L22), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

7:interface IBridge {	// @audit-issue missing `@title` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L7-L7), 


```solidity
Path: ./tokenomics/contracts/interfaces/IDonatorBlacklist.sol

5:interface IDonatorBlacklist {	// @audit-issue missing `@title` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L5-L5), 


```solidity
Path: ./tokenomics/contracts/interfaces/IBridgeErrors.sol

4:interface IBridgeErrors {	// @audit-issue missing `@title` tag
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IBridgeErrors.sol#L4-L4), 


```solidity
Path: ./tokenomics/contracts/interfaces/IErrorsTokenomics.sol

5:interface IErrorsTokenomics {	// @audit-issue missing `@title` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L5-L5), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

16:library SafeTransferLib {	// @audit-issue missing `@title` tag
```
[16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L16-L16), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

5:interface IStaking {	// @audit-issue missing `@title` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L5-L5), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

5:interface IMultisig {	// @audit-issue missing `@title` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L5-L5), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

7:interface IActivityChecker {	// @audit-issue missing `@title` tag

30:interface IService {	// @audit-issue missing `@title` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L7-L7), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L30-L30), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

11:interface IServiceTokenUtility {	// @audit-issue missing `@title` tag
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L11-L11), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

7:interface IStaking {	// @audit-issue missing `@title` tag

14:interface IStakingVerifier {	// @audit-issue missing `@title` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L14-L14), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

5:interface IDispenser {	// @audit-issue missing `@title` tag

16:interface IVEOLAS {	// @audit-issue missing `@title` tag
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L5-L5), [16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L16-L16), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### NatSpec: Use `@inheritdoc` for overriden functions.
Natural Specification (NatSpec) comments are crucial for understanding the role of function arguments in your Solidity code. Including @param tags will not only improve your code's readability but also its maintainability by clearly defining each argument's purpose. [Dive Deeper into NatSpec Guidelines](https://docs.soliditylang.org/en/develop/natspec-format.html)

```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

89:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {	// @audit-issue missing `@inheritdoc` tag

133:    function receivePayloadAndTokens(	// @audit-issue missing `@inheritdoc` tag
```
[89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L89-L89), [133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L133-L133), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

52:    function _processMessageFromRoot(uint256, address sender, bytes memory data) internal override {	// @audit-issue missing `@inheritdoc` tag

59:    function setFxRootTunnel(address l1Processor) external override {	// @audit-issue missing `@inheritdoc` tag
```
[52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L52-L52), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

132:    function setL2TargetDispenser(address l2Dispenser) external override {	// @audit-issue missing `@inheritdoc` tag
```
[132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L132-L132), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

91:    function _processMessageFromChild(bytes memory data) internal override {	// @audit-issue missing `@inheritdoc` tag

98:    function setFxChildTunnel(address l2Dispenser) public override {	// @audit-issue missing `@inheritdoc` tag

117:    function setL2TargetDispenser(address l2Dispenser) external override {	// @audit-issue missing `@inheritdoc` tag
```
[91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L91-L91), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L98-L98), [117](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L117-L117), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

28:    function _withdraw(address to, uint256 amount) internal override {	// @audit-issue missing `@inheritdoc` tag
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L28-L28), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

74:    function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {	// @audit-issue missing `@inheritdoc` tag

104:    function _withdraw(address to, uint256 amount) internal override {	// @audit-issue missing `@inheritdoc` tag
```
[74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L74-L74), [104](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L104-L104), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### NatSpec: Function `@return` tag is missing
Natural Specification (NatSpec) comments are crucial for understanding the role of function arguments in your Solidity code. Including `@return` tag will not only improve your code's readability but also its maintainability by clearly defining each argument's purpose. [Dive Deeper into NatSpec Guidelines](https://docs.soliditylang.org/en/develop/natspec-format.html)

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

61:    function getVotes(address account) external view returns (uint256);	// @audit-issue missing `@return` tag
```
[61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L61-L61), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

63:    function l2ToL1Sender() external view returns (address);	// @audit-issue missing `@return` tag

119:    function _sendMessage(	// @audit-issue missing `@return` tag
```
[63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L63-L63), [119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L119-L119), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

95:    function _sendMessage(	// @audit-issue missing `@return` tag
```
[95](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L95-L95), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

10:    function quoteEVMDeliveryPrice(	// @audit-issue missing `@return` tag
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L10-L10), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

25:    function messageSender() external returns (address);	// @audit-issue missing `@return` tag

51:    function _sendMessage(	// @audit-issue missing `@return` tag
```
[25](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L25-L25), [51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L51-L51), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

59:    function _sendMessage(	// @audit-issue missing `@return` tag

138:    function getBridgingDecimals() external pure override returns (uint256) {	// @audit-issue missing `@return` tag
```
[59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L59-L59), [138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L138-L138), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

19:    function messageSender() external returns (address);	// @audit-issue missing `@return` tag
```
[19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L19-L19), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

57:    function _sendMessage(	// @audit-issue missing `@return` tag
```
[57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L57-L57), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

522:    function _calculateStakingRewards() internal view returns (	// @audit-issue missing `@return` tag
```
[522](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L522-L522), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

54:    function getImplementation() external view returns (address implementation) {	// @audit-issue missing `@return` tag
```
[54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L54-L54), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

141:    function getProxyAddressWithNonce(address implementation, uint256 localNonce) public view returns (address) {	// @audit-issue missing `@return` tag

161:    function getProxyAddress(address implementation) external view returns (address) {	// @audit-issue missing `@return` tag

168:    function createStakingInstance(	// @audit-issue missing `@return` tag
```
[141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L141-L141), [161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L161-L161), [168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L168-L168), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

578:    function _maxAndSub(uint256 a, uint256 b) internal pure returns (uint256) {	// @audit-issue missing `@return` tag

779:    function getNextAllowedVotingTimes(	// @audit-issue missing `@return` tag
```
[578](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L578-L578), [779](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L779-L779), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).

### NatSpec: Function declarations should have `@notice` tag
The `@notice` tag in NatSpec comments is used to provide important explanations to end users about what a function does. It appears that this contract's function declarations are missing `@notice` tags in their NatSpec annotations.

The absence of `@notice` tags reduces the contract's transparency and could lead to misunderstandings about a function's purpose and behavior.  [Dive Deeper into NatSpec Guidelines](https://docs.soliditylang.org/en/develop/natspec-format.html)

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

13:    function timeLaunch() external view returns (uint256);	// @audit-issue missing `@notice` tag

21:    function ownerOf(uint256 tokenId) external view returns (address);	// @audit-issue missing `@notice` tag

25:    function totalSupply() external view returns (uint256);	// @audit-issue missing `@notice` tag

33:    function rebalanceTreasury(uint256 treasuryRewards) external returns (bool success);	// @audit-issue missing `@notice` tag

46:    function exists(uint256 serviceId) external view returns (bool);	// @audit-issue missing `@notice` tag

61:    function getVotes(address account) external view returns (uint256);	// @audit-issue missing `@notice` tag

377:    constructor()	// @audit-issue missing `@notice` tag

516:    function tokenomicsImplementation() external view returns (address implementation) {	// @audit-issue missing `@notice` tag

546:    function changeOwner(address newOwner) external {	// @audit-issue missing `@notice` tag

565:    function changeManagers(address _treasury, address _depository, address _dispenser) external {	// @audit-issue missing `@notice` tag

592:    function changeRegistries(address _componentRegistry, address _agentRegistry, address _serviceRegistry) external {	// @audit-issue missing `@notice` tag

703:    function changeIncentiveFractions(	// @audit-issue missing `@notice` tag

751:    function changeStakingParams(uint256 _maxStakingIncentive, uint256 _minStakingWeight) external {	// @audit-issue missing `@notice` tag

808:    function refundFromBondProgram(uint256 amount) external {	// @audit-issue missing `@notice` tag

826:    function refundFromStaking(uint256 amount) external {	// @audit-issue missing `@notice` tag

847:    function _finalizeIncentivesForUnitId(uint256 epochNum, uint256 unitType, uint256 unitId) internal {	// @audit-issue missing `@notice` tag

884:    function _trackServiceDonations(	// @audit-issue missing `@notice` tag

1033:    function _calculateIDF(uint256 treasuryRewards, uint256 numNewOwners) internal view returns (uint256 idf) {	// @audit-issue missing `@notice` tag

1466:    function getUnitPoint(uint256 epoch, uint256 unitType) external view returns (UnitPoint memory) {	// @audit-issue missing `@notice` tag

1472:    function getLastIDF() external view returns (uint256) {	// @audit-issue missing `@notice` tag

1479:    function getEpochEndTime(uint256 epoch) external view returns (uint256) {	// @audit-issue missing `@notice` tag
```
[13](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L13-L13), [21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L21-L21), [25](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L25-L25), [33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L33-L33), [46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L46-L46), [61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L61-L61), [377](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L377-L377), [516](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L516-L516), [546](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L546-L546), [565](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L565-L565), [592](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L592-L592), [703](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L703-L703), [751](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L751-L751), [808](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L808-L808), [826](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L826-L826), [847](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L847-L847), [884](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L884-L884), [1033](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1033-L1033), [1466](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1466-L1466), [1472](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1472-L1472), [1479](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1479-L1479), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

33:    function getSupplyCapForYear(uint256 numYears) public pure returns (uint256 supplyCap) {	// @audit-issue missing `@notice` tag

69:    function getInflationForYear(uint256 numYears) public pure returns (uint256 inflationAmount) {	// @audit-issue missing `@notice` tag
```
[33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L33-L33), [69](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L69-L69), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

11:    function sendMessage(address target, uint256 stakingIncentive, bytes memory bridgePayload,	// @audit-issue missing `@notice` tag

19:    function sendMessageBatch(address[] memory targets, uint256[] memory stakingIncentives, bytes memory bridgePayload,	// @audit-issue missing `@notice` tag

27:    function sendMessageNonEVM(bytes32 target, uint256 stakingIncentive, bytes memory bridgePayload,	// @audit-issue missing `@notice` tag

35:    function sendMessageBatchNonEVM(bytes32[] memory targets, uint256[] memory stakingIncentives,	// @audit-issue missing `@notice` tag

40:    function getBridgingDecimals() external pure returns (uint256);	// @audit-issue missing `@notice` tag

48:    function balanceOf(address account) external view returns (uint256);	// @audit-issue missing `@notice` tag

54:    function transfer(address to, uint256 amount) external returns (bool);	// @audit-issue missing `@notice` tag

120:    function epochCounter() external view returns (uint32);	// @audit-issue missing `@notice` tag

124:    function epochLen() external view returns (uint32);	// @audit-issue missing `@notice` tag

129:    function getEpochEndTime(uint256 epoch) external view returns (uint256 endTime);	// @audit-issue missing `@notice` tag

134:    function mapEpochStakingPoints(uint256 eCounter) external view returns (StakingPoint memory);	// @audit-issue missing `@notice` tag

138:    function refundFromStaking(uint256 amount) external;	// @audit-issue missing `@notice` tag

156:    function paused() external returns (uint8);	// @audit-issue missing `@notice` tag

170:    function mapNomineeIds(bytes32 nomineeHash) external returns (uint256);	// @audit-issue missing `@notice` tag

175:    function checkpointNominee(bytes32 account, uint256 chainId) external;	// @audit-issue missing `@notice` tag

185:    function nomineeRelativeWeight(bytes32 account, uint256 chainId, uint256 time) external view returns (uint256, uint256);	// @audit-issue missing `@notice` tag

315:    constructor(	// @audit-issue missing `@notice` tag

356:    function _checkpointNomineeAndGetClaimedEpochCounters(	// @audit-issue missing `@notice` tag

408:    function _distributeStakingIncentives(	// @audit-issue missing `@notice` tag

441:    function _distributeStakingIncentivesBatch(	// @audit-issue missing `@notice` tag

573:    function _calculateStakingIncentivesBatch(	// @audit-issue missing `@notice` tag

636:    function changeOwner(address newOwner) external {	// @audit-issue missing `@notice` tag

655:    function changeManagers(address _tokenomics, address _treasury, address _voteWeighting) external {	// @audit-issue missing `@notice` tag

683:    function changeStakingParams(uint256 _maxNumClaimingEpochs, uint256 _maxNumStakingTargets) external {	// @audit-issue missing `@notice` tag

732:    function addNominee(bytes32 nomineeHash) external {	// @audit-issue missing `@notice` tag

750:    function removeNominee(bytes32 nomineeHash) external {	// @audit-issue missing `@notice` tag

953:    function claimStakingIncentives(	// @audit-issue missing `@notice` tag

1129:    function retain() external {	// @audit-issue missing `@notice` tag

1241:    function setPauseState(Pause pauseState) external {	// @audit-issue missing `@notice` tag
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L11-L11), [19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L19-L19), [27](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L27-L27), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L35-L35), [40](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L40-L40), [48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L48-L48), [54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L54-L54), [120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L120-L120), [124](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L124-L124), [129](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L129-L129), [134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L134-L134), [138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L138-L138), [156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L156-L156), [170](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L170-L170), [175](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L175-L175), [185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L185-L185), [315](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L315-L315), [356](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L356-L356), [408](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L408-L408), [441](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L441-L441), [573](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L573-L573), [636](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L636-L636), [655](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L655-L655), [683](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L683-L683), [732](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L732-L732), [750](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L750-L750), [953](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L953-L953), [1129](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1129-L1129), [1241](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1241-L1241), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

48:    function createRetryableTicket(	// @audit-issue missing `@notice` tag

89:    constructor(	// @audit-issue missing `@notice` tag

196:    function receiveMessage(bytes memory data) external {	// @audit-issue missing `@notice` tag
```
[48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L48-L48), [89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L89-L89), [196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L196-L196), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

47:    constructor(	// @audit-issue missing `@notice` tag

56:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {	// @audit-issue missing `@notice` tag

96:    function receiveMessage(bytes memory data) external payable {	// @audit-issue missing `@notice` tag
```
[47](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L47-L47), [56](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L56-L56), [96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L96-L96), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

7:    function syncWithheldAmount(uint256 chainId, uint256 amount) external;	// @audit-issue missing `@notice` tag

15:    function approve(address spender, uint256 amount) external returns (bool);	// @audit-issue missing `@notice` tag

59:    constructor(	// @audit-issue missing `@notice` tag

107:    function _receiveMessage(address l1Relayer, address l2Dispenser, bytes memory data) internal virtual {	// @audit-issue missing `@notice` tag

132:    function sendMessage(	// @audit-issue missing `@notice` tag

164:    function sendMessageBatch(	// @audit-issue missing `@notice` tag

186:    function _setL2TargetDispenser(address l2Dispenser) internal {	// @audit-issue missing `@notice` tag

206:    function setL2TargetDispenser(address l2Dispenser) external virtual {	// @audit-issue missing `@notice` tag

212:    function getBridgingDecimals() external pure virtual returns (uint256) {	// @audit-issue missing `@notice` tag
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L7-L7), [15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L15-L15), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L59-L59), [107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L107-L107), [132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L132-L132), [164](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L164-L164), [186](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L186-L186), [206](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L206-L206), [212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L212-L212), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

53:    function _sendMessage(uint256 amount, bytes memory) internal override {	// @audit-issue missing `@notice` tag
```
[53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L53-L53), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

76:    constructor(	// @audit-issue missing `@notice` tag

95:    function _sendMessage(	// @audit-issue missing `@notice` tag

148:    function receiveMessage(bytes memory data) external payable {	// @audit-issue missing `@notice` tag
```
[76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L76-L76), [95](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L95-L95), [148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L148-L148), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

9:    function approve(address spender, uint256 amount) external returns (bool);	// @audit-issue missing `@notice` tag

15:    function transfer(address to, uint256 amount) external returns (bool);	// @audit-issue missing `@notice` tag

21:    function deposit(uint256 amount) external;	// @audit-issue missing `@notice` tag

28:    function verifyInstanceAndGetEmissionsAmount(address instance) external view returns (uint256 amount);	// @audit-issue missing `@notice` tag

70:    constructor(address _olas, address _dispenser, address _stakingFactory, address _timelock) {	// @audit-issue missing `@notice` tag

86:    function _deposit(address[] memory targets, uint256[] memory stakingIncentives) internal {	// @audit-issue missing `@notice` tag

130:    function sendMessage(	// @audit-issue missing `@notice` tag

155:    function sendMessageBatch(	// @audit-issue missing `@notice` tag
```
[9](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L9-L9), [15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L15-L15), [21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L21-L21), [28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L28-L28), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L70-L70), [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L86-L86), [130](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L130-L130), [155](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L155-L155), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

10:    function quoteEVMDeliveryPrice(	// @audit-issue missing `@notice` tag

64:    constructor(	// @audit-issue missing `@notice` tag

89:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {	// @audit-issue missing `@notice` tag
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L10-L10), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L64-L64), [89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L89-L89), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

15:    function requireToPassMessage(address target, bytes memory data, uint256 maxGasLimit) external returns (bytes32);	// @audit-issue missing `@notice` tag

21:    function relayTokensAndCall(address token, address receiver, uint256 amount, bytes memory payload) external;	// @audit-issue missing `@notice` tag

25:    function messageSender() external returns (address);	// @audit-issue missing `@notice` tag

42:    constructor(	// @audit-issue missing `@notice` tag

51:    function _sendMessage(	// @audit-issue missing `@notice` tag

98:    function receiveMessage(bytes memory data) external {	// @audit-issue missing `@notice` tag
```
[15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L15-L15), [21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L21-L21), [25](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L25-L25), [42](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L42-L42), [51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L51-L51), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L98-L98), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

20:    constructor(	// @audit-issue missing `@notice` tag

32:    function _sendMessage(uint256 amount, bytes memory) internal override {	// @audit-issue missing `@notice` tag

59:    function setFxRootTunnel(address l1Processor) external override {	// @audit-issue missing `@notice` tag
```
[20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L20-L20), [32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L32-L32), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

28:    constructor(	// @audit-issue missing `@notice` tag

59:    function _sendMessage(	// @audit-issue missing `@notice` tag

132:    function setL2TargetDispenser(address l2Dispenser) external override {	// @audit-issue missing `@notice` tag

138:    function getBridgingDecimals() external pure override returns (uint256) {	// @audit-issue missing `@notice` tag
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L28-L28), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L59-L59), [132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L132-L132), [138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L138-L138), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

15:    function requireToPassMessage(address target, bytes memory data, uint256 maxGasLimit) external returns (bytes32);	// @audit-issue missing `@notice` tag

19:    function messageSender() external returns (address);	// @audit-issue missing `@notice` tag

39:    constructor(	// @audit-issue missing `@notice` tag

58:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {	// @audit-issue missing `@notice` tag

87:    function receiveMessage(bytes memory data) external {	// @audit-issue missing `@notice` tag

99:    function onTokenBridged(address, uint256, bytes calldata data) external {	// @audit-issue missing `@notice` tag
```
[15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L15-L15), [19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L19-L19), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L39-L39), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L58-L58), [87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L87-L87), [99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L99-L99), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

10:    function deposit(uint256 amount) external;	// @audit-issue missing `@notice` tag

18:    function verifyInstanceAndGetEmissionsAmount(address instance) external view returns (uint256 amount);	// @audit-issue missing `@notice` tag

26:    function balanceOf(address account) external view returns (uint256);	// @audit-issue missing `@notice` tag

32:    function approve(address spender, uint256 amount) external returns (bool);	// @audit-issue missing `@notice` tag

38:    function transfer(address to, uint256 amount) external returns (bool);	// @audit-issue missing `@notice` tag

101:    constructor(	// @audit-issue missing `@notice` tag

139:    function _processData(bytes memory data) internal {	// @audit-issue missing `@notice` tag

218:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal virtual;	// @audit-issue missing `@notice` tag

224:    function _receiveMessage(	// @audit-issue missing `@notice` tag

247:    function changeOwner(address newOwner) external {	// @audit-issue missing `@notice` tag

266:    function redeem(address target, uint256 amount, uint256 batchNonce) external {	// @audit-issue missing `@notice` tag

323:    function syncWithheldTokens(bytes memory bridgePayload) external payable {	// @audit-issue missing `@notice` tag

353:    function pause() external {	// @audit-issue missing `@notice` tag

364:    function unpause() external {	// @audit-issue missing `@notice` tag

458:    receive() external payable {	// @audit-issue missing `@notice` tag
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L10-L10), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L18-L18), [26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L26-L26), [32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L32-L32), [38](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L38-L38), [101](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L101-L101), [139](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L139-L139), [218](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L218-L218), [224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L224-L224), [247](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L247-L247), [266](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L266-L266), [323](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L323-L323), [353](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L353-L353), [364](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L364-L364), [458](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L458-L458), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

36:    constructor(	// @audit-issue missing `@notice` tag

57:    function _sendMessage(	// @audit-issue missing `@notice` tag

98:    function setFxChildTunnel(address l2Dispenser) public override {	// @audit-issue missing `@notice` tag

117:    function setL2TargetDispenser(address l2Dispenser) external override {	// @audit-issue missing `@notice` tag
```
[36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L36-L36), [57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L57-L57), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L98-L98), [117](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L117-L117), 


```solidity
Path: ./tokenomics/contracts/interfaces/IDonatorBlacklist.sol

9:    function isDonatorBlacklisted(address account) external view returns (bool status);	// @audit-issue missing `@notice` tag
```
[9](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L9-L9), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

22:    function safeTransferFrom(address token, address from, address to, uint256 amount) internal {	// @audit-issue missing `@notice` tag
```
[22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L22-L22), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

8:    function rewardsPerSecond() external view returns (uint256);	// @audit-issue missing `@notice` tag

12:    function maxNumServices() external view returns (uint256);	// @audit-issue missing `@notice` tag

16:    function stakingToken() external view returns (address);	// @audit-issue missing `@notice` tag

74:    constructor(address _olas, uint256 _rewardsPerSecondLimit, uint256 _timeForEmissionsLimit,	// @audit-issue missing `@notice` tag

96:    function changeOwner(address newOwner) external {	// @audit-issue missing `@notice` tag

113:    function setImplementationsCheck(bool setCheck) external {	// @audit-issue missing `@notice` tag

166:    function verifyImplementation(address implementation) external view returns (bool){	// @audit-issue missing `@notice` tag

179:    function verifyInstance(address instance, address implementation) external view returns (bool) {	// @audit-issue missing `@notice` tag

229:    function changeStakingLimits(	// @audit-issue missing `@notice` tag
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L8-L8), [12](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L12-L12), [16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L16-L16), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L74-L74), [96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L96-L96), [113](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L113-L113), [166](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L166-L166), [179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L179-L179), [229](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L229-L229), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

20:    function initialize(StakingParams memory _stakingParams) external {	// @audit-issue missing `@notice` tag

39:    receive() external payable {	// @audit-issue missing `@notice` tag
```
[20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L20-L20), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L39-L39), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

8:    function nonce() external view returns (uint256);	// @audit-issue missing `@notice` tag

24:    constructor(uint256 _livenessRatio) {	// @audit-issue missing `@notice` tag

36:    function getMultisigNonces(address multisig) external view virtual returns (uint256[] memory nonces) {	// @audit-issue missing `@notice` tag
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L8-L8), [24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L24-L24), [36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L36-L36), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

11:    function getMultisigNonces(address multisig) external view returns (uint256[] memory nonces);	// @audit-issue missing `@notice` tag

72:    function safeTransferFrom(address from, address to, uint256 id) external;	// @audit-issue missing `@notice` tag

77:    function getService(uint256 serviceId) external view returns (Service memory service);	// @audit-issue missing `@notice` tag

83:    function getAgentParams(uint256 serviceId) external view	// @audit-issue missing `@notice` tag

278:    function _initialize(	// @audit-issue missing `@notice` tag

366:    function _checkTokenStakingDeposit(	// @audit-issue missing `@notice` tag

390:    function _withdraw(address to, uint256 amount) internal virtual;	// @audit-issue missing `@notice` tag

398:    function _checkRatioPass(	// @audit-issue missing `@notice` tag

430:    function _evict(	// @audit-issue missing `@notice` tag

482:    function _claim(uint256 serviceId, bool execCheckPoint) internal returns (uint256 reward) {	// @audit-issue missing `@notice` tag

522:    function _calculateStakingRewards() internal view returns (	// @audit-issue missing `@notice` tag

591:    function checkpoint() public returns (	// @audit-issue missing `@notice` tag

805:    function unstake(uint256 serviceId) external returns (uint256 reward) {	// @audit-issue missing `@notice` tag

877:    function claim(uint256 serviceId) external returns (uint256) {	// @audit-issue missing `@notice` tag

884:    function checkpointAndClaim(uint256 serviceId) external returns (uint256) {	// @audit-issue missing `@notice` tag

916:    function calculateStakingReward(uint256 serviceId) external view returns (uint256 reward) {	// @audit-issue missing `@notice` tag

928:    function getStakingState(uint256 serviceId) external view returns (StakingState stakingState) {	// @audit-issue missing `@notice` tag

939:    function getNextRewardCheckpointTimestamp() external view returns (uint256 tsNext) {	// @audit-issue missing `@notice` tag

947:    function getServiceInfo(uint256 serviceId) external view returns (ServiceInfo memory sInfo) {	// @audit-issue missing `@notice` tag

953:    function getServiceIds() public view returns (uint256[] memory) {	// @audit-issue missing `@notice` tag

959:    function getAgentIds() external view returns (uint256[] memory) {	// @audit-issue missing `@notice` tag
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L11-L11), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L72-L72), [77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L77-L77), [83](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L83-L83), [278](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L278-L278), [366](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L366-L366), [390](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L390-L390), [398](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L398-L398), [430](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L430-L430), [482](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L482-L482), [522](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L522-L522), [591](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L591-L591), [805](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L805-L805), [877](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L877-L877), [884](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L884-L884), [916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L916-L916), [928](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L928-L928), [939](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L939-L939), [947](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L947-L947), [953](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L953-L953), [959](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L959-L959), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

16:    function mapServiceIdTokenDeposit(uint256 serviceId) external view returns (address, uint96);	// @audit-issue missing `@notice` tag

22:    function getAgentBond(uint256 serviceId, uint256 agentId) external view returns (uint256 bond);	// @audit-issue missing `@notice` tag

54:    function initialize(	// @audit-issue missing `@notice` tag

74:    function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {	// @audit-issue missing `@notice` tag

115:    function deposit(uint256 amount) external {	// @audit-issue missing `@notice` tag
```
[16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L16-L16), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L22-L22), [54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L54-L54), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L74-L74), [115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L115-L115), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

27:    constructor(address implementation) {	// @audit-issue missing `@notice` tag

40:    fallback() external payable {	// @audit-issue missing `@notice` tag

54:    function getImplementation() external view returns (address implementation) {	// @audit-issue missing `@notice` tag
```
[27](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L27-L27), [40](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L40-L40), [54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L54-L54), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

10:    function emissionsAmount() external view returns (uint256);	// @audit-issue missing `@notice` tag

18:    function verifyImplementation(address implementation) external view returns (bool);	// @audit-issue missing `@notice` tag

24:    function verifyInstance(address instance, address implementation) external view returns (bool);	// @audit-issue missing `@notice` tag

28:    function getEmissionsAmountLimit(address) external view returns (uint256);	// @audit-issue missing `@notice` tag

103:    constructor(address _verifier) {	// @audit-issue missing `@notice` tag

110:    function changeOwner(address newOwner) external {	// @audit-issue missing `@notice` tag

127:    function changeVerifier(address newVerifier) external {	// @audit-issue missing `@notice` tag

161:    function getProxyAddress(address implementation) external view returns (address) {	// @audit-issue missing `@notice` tag

168:    function createStakingInstance(	// @audit-issue missing `@notice` tag

249:    function setInstanceStatus(address instance, bool isEnabled) external {	// @audit-issue missing `@notice` tag

267:    function verifyInstance(address instance) public view returns (bool) {	// @audit-issue missing `@notice` tag

294:    function verifyInstanceAndGetEmissionsAmount(address instance) external view returns (uint256 amount) {	// @audit-issue missing `@notice` tag
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L10-L10), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L18-L18), [24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L24-L24), [28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L28-L28), [103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L103-L103), [110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L110-L110), [127](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L127-L127), [161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L161-L161), [168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L168-L168), [249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L249-L249), [267](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L267-L267), [294](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L294-L294), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

8:    function addNominee(bytes32 nomineeHash) external;	// @audit-issue missing `@notice` tag

12:    function removeNominee(bytes32 nomineeHash) external;	// @audit-issue missing `@notice` tag

36:    function lockedEnd(address account) external view returns (uint256 unlockTime);	// @audit-issue missing `@notice` tag

41:    function getLastUserPoint(address account) external view returns (PointVoting memory pv);	// @audit-issue missing `@notice` tag

205:    constructor(address _ve) {	// @audit-issue missing `@notice` tag

223:    function _getSum() internal returns (uint256) {	// @audit-issue missing `@notice` tag

254:    function _getWeight(bytes32 account, uint256 chainId) internal returns (uint256) {	// @audit-issue missing `@notice` tag

292:    function _addNominee(Nominee memory nominee) internal {	// @audit-issue missing `@notice` tag

324:    function addNomineeEVM(address account, uint256 chainId) external {	// @audit-issue missing `@notice` tag

349:    function addNomineeNonEVM(bytes32 account, uint256 chainId) external {	// @audit-issue missing `@notice` tag

368:    function changeOwner(address newOwner) external {	// @audit-issue missing `@notice` tag

397:    function checkpoint() external {	// @audit-issue missing `@notice` tag

406:    function checkpointNominee(bytes32 account, uint256 chainId) external {	// @audit-issue missing `@notice` tag

419:    function _nomineeRelativeWeight(	// @audit-issue missing `@notice` tag

442:    function nomineeRelativeWeight(	// @audit-issue missing `@notice` tag

473:    function voteForNomineeWeights(bytes32 account, uint256 chainId, uint256 weight) public {	// @audit-issue missing `@notice` tag

563:    function voteForNomineeWeightsBatch(	// @audit-issue missing `@notice` tag

578:    function _maxAndSub(uint256 a, uint256 b) internal pure returns (uint256) {	// @audit-issue missing `@notice` tag

641:    function revokeRemovedNomineeVotingPower(bytes32 account, uint256 chainId) external {	// @audit-issue missing `@notice` tag

674:    function getNomineeWeight(bytes32 account, uint256 chainId) external view returns (uint256) {	// @audit-issue missing `@notice` tag

684:    function getWeightsSum() external view returns (uint256) {	// @audit-issue missing `@notice` tag

720:    function getNomineeId(bytes32 account, uint256 chainId) external view returns (uint256) {	// @audit-issue missing `@notice` tag

732:    function getRemovedNomineeId(bytes32 account, uint256 chainId) external view returns (uint256) {	// @audit-issue missing `@notice` tag
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L8-L8), [12](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L12-L12), [36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L36-L36), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L41-L41), [205](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L205-L205), [223](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L223-L223), [254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L254-L254), [292](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L292-L292), [324](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L324-L324), [349](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L349-L349), [368](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L368-L368), [397](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L397-L397), [406](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L406-L406), [419](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L419-L419), [442](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L442-L442), [473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L473-L473), [563](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L563-L563), [578](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L578-L578), [641](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L641-L641), [674](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L674-L674), [684](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L684-L684), [720](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L720-L720), [732](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L732-L732), 


#### Recommendation

Follow the official [Solidity guidelines](https://docs.soliditylang.org/en/v0.8.17/style-guide.html).
