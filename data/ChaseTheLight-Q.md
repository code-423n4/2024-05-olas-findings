## LightChaser-V3

### Generated for: Code4rena : Olas

### Generated on: 2024-05-30

## Total findings: 58

### Total Low findings: 32

### Total Gas findings: 9

### Total NonCritical findings: 17

### Note: 

Although NC and Gas findings are not in scope for reports as per the new QA rules, I still included some of the more interesting ones as I feel they may be useful to the sponsor. :) 

# Summary for Low findings

| Number | Details | Instances |
|----------|----------|----------|
| [Low-1] | Potential division by zero should have zero checks in place  | 2 |
| [Low-2] | Division operations should always be performed after multiplication operations  | 4 |
| [Low-3] | Function with two array parameter missing a length check  | 1 |
| [Low-4] | Code does not follow the best practice of check-effects-interaction  | 4 |
| [Low-5] | Incorrect comparison against a max value  | 1 |
| [Low-6] | Public or external initialize functions should be protected with the initializer modifier | 2 |
| [Low-7] | Sending tokens in a for loop | 3 |
| [Low-8] | Bridge function must check for native token | 4 |
| [Low-9] | Contract contains payable functions but no withdraw/sweep function | 7 |
| [Low-10] | Calculations using non internal accounting such as balanceOf or totalSupply can introduce potential donation attack vectors | 7 |
| [Low-11] | Solidity version 0.8.20 won't work on all chains due to PUSH0 | 3 |
| [Low-12] | Using block.number is not fully L2 compatible | 2 |
| [Low-13] | Int casting block.timestamp can reduce the lifespan of a contract | 2 |
| [Low-14] | The call abi.encodeWithSelector is not type safe | 6 |
| [Low-15] | Loss of precision | 2 |
| [Low-16] | Missing zero address check in constructor | 2 |
| [Low-17] | Off-by-one timestamp error | 2 |
| [Low-18] | Using zero as a parameter | 5 |
| [Low-19] | Constant decimal values | 11 |
| [Low-20] | Arrays can grow in size without a way to shrink them | 1 |
| [Low-21] | Revert on Transfer to the Zero Address | 1 |
| [Low-22] | Return values not checked for approve() | 6 |
| [Low-23] | No access control on receive/payable fallback | 1 |
| [Low-24] | External call recipient may consume all transaction gas | 2 |
| [Low-25] | Unbounded state array which is iterated upon | 1 |
| [Low-26] | SafeTransferLib does not ensure that the token contract exists | 1 |
| [Low-27] | Don't assume specific ETH balance | 1 |
| [Low-28] | Prefer skip over revert model in iteration | 6 |
| [Low-29] | Missing contract-existence checks before low-level calls | 3 |
| [Low-30] | Numbers downcast to addresses may result in collisions | 6 |
| [Low-31] | Use of abi.encodePacked with dynamic types inside keccak256 | 1 |
| [Low-32] | Inconsistent checks of address params against address(0) | 1 |
# Summary for NonCritical findings

| Number | Details | Instances |
|----------|----------|----------|
| [NonCritical-1] | Greater than comparisons made on state uints that can be set to max  | 2 |
| [NonCritical-2] | Constant array index within iteration | 3 |
| [NonCritical-3] | safeApprove()/approve() may revert if the current approval is not zero | 7 |
| [NonCritical-4] | Addition/multiplication in unchecked block is unsafe | 1 |
| [NonCritical-5] | Consider the case where `totalSupply` is zero | 2 |
| [NonCritical-6] | Floating pragma should be avoided | 3 |
| [NonCritical-7] | Functions with array parameters should have length checks in place | 8 |
| [NonCritical-8] | Events may be emitted out of order due to code not follow the best practice of check-effects-interaction | 4 |
| [NonCritical-9] | Missing events in sensitive functions | 3 |
| [NonCritical-10] | Avoid mutating function parameters | 5 |
| [NonCritical-11] | A event should be emitted if a non immutable state variable is set in a constructor | 7 |
| [NonCritical-12] | Immutable and constant integer state variables should not be casted | 5 |
| [NonCritical-13] | Use transfer libraries instead of low level calls | 2 |
| [NonCritical-14] | int/uint passed into abi.encodePacked without casting to a string. | 1 |
| [NonCritical-15] | For loop iterates on arrays not indexed | 16 |
| [NonCritical-16] | Avoid arithmetic directly within array indices | 8 |
| [NonCritical-17] | ERC777 tokens can introduce reentrancy risks | 1 |
# Summary for Gas findings

| Number | Details | Instances | Gas |
|----------|----------|----------|----------|
| [Gas-1] | Multiple accesses of the same mapping/array key/index should be cached  | 2 | 168 |
| [Gas-2] | Shortcircuit rules can be be used to optimize some gas usage  | 2 | 84000 |
| [Gas-3] | Setter emit which emits new and old value can be more efficient | 1 | 800 |
| [Gas-4] | Public functions not used internally can be marked as external to save gas | 1 | 0.0 |
| [Gas-5] | Redundant state variable getters | 1 | 0.0 |
| [Gas-6] | Identical Deployments Should be Replaced with Clone | 1 | 0.0 |
| [Gas-7] | Where a value is casted more than once, consider caching the result to save gas | 3 | 0.0 |
| [Gas-8] | State variable written in a loop | 3 | 104292 |
| [Gas-9] | Public functions not called internally | 1 | 0.0 |



## [Low-1] Potential division by zero should have zero checks in place 

### Resolution 
Implement a zero address check for found instances

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[893](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingBase.sol#L893-L904)']
```solidity
893:     function calculateStakingLastReward(uint256 serviceId) public view returns (uint256 reward) { // <= FOUND
894:         
895:         (uint256 lastAvailableRewards, uint256 numServices, uint256 totalRewards, uint256[] memory eligibleServiceIds,
896:             uint256[] memory eligibleServiceRewards, , , ) = _calculateStakingRewards();
897: 
898:         
899:         for (uint256 i = 0; i < numServices; ++i) {
900:             
901:             if (eligibleServiceIds[i] == serviceId) {
902:                 
903:                 if (totalRewards > lastAvailableRewards) {
904:                     reward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards; // <= FOUND
905:                 } else {
906:                     reward = eligibleServiceRewards[i];
907:                 }
908:                 break;
909:             }
910:         }
911:     }
```
['[408](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L408-L472)']
```solidity
408:     function initializeTokenomics(
409:         address _olas,
410:         address _treasury,
411:         address _depository,
412:         address _dispenser,
413:         address _ve,
414:         uint256 _epochLen,
415:         address _componentRegistry,
416:         address _agentRegistry,
417:         address _serviceRegistry,
418:         address _donatorBlacklist
419:     ) external {
420:         
421:         if (owner != address(0)) {
422:             revert AlreadyInitialized();
423:         }
424: 
425:         
426:         if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||
427:             _ve == address(0) || _componentRegistry == address(0) || _agentRegistry == address(0) ||
428:             _serviceRegistry == address(0)) {
429:             revert ZeroAddress();
430:         }
431: 
432:         
433:         owner = msg.sender;
434:         _locked = 1;
435:         epsilonRate = 1e17;
436:         veOLASThreshold = 10_000e18;
437: 
438:         
439:         if (uint32(_epochLen) < MIN_EPOCH_LENGTH) {
440:             revert LowerThan(_epochLen, MIN_EPOCH_LENGTH);
441:         }
442: 
443:         
444:         if (uint32(_epochLen) > MAX_EPOCH_LENGTH) {
445:             revert Overflow(_epochLen, MAX_EPOCH_LENGTH);
446:         }
447: 
448:         
449:         olas = _olas;
450:         treasury = _treasury;
451:         depository = _depository;
452:         dispenser = _dispenser;
453:         ve = _ve;
454:         epochLen = uint32(_epochLen);
455:         componentRegistry = _componentRegistry;
456:         agentRegistry = _agentRegistry;
457:         serviceRegistry = _serviceRegistry;
458:         donatorBlacklist = _donatorBlacklist;
459: 
460:         
461:         uint256 _timeLaunch = IOLAS(_olas).timeLaunch();
462:         
463:         if (block.timestamp >= (_timeLaunch + ONE_YEAR)) {
464:             revert Overflow(_timeLaunch + ONE_YEAR, block.timestamp);
465:         }
466:         
467:         
468:         uint256 zeroYearSecondsLeft = uint32(_timeLaunch + ONE_YEAR - block.timestamp);
469:         
470:         
471:         
472:         uint256 _inflationPerSecond = getInflationForYear(0) / zeroYearSecondsLeft; // <= FOUND
473:         inflationPerSecond = uint96(_inflationPerSecond);
474:         timeLaunch = uint32(_timeLaunch);
475: 
476:         
477:         mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);
478: 
479:         
480:         epochCounter = 1;
481:         TokenomicsPoint storage tp = mapEpochTokenomics[1];
482: 
483:         
484:         devsPerCapital = 1e18;
485:         tp.epochPoint.idf = 1e18;
486: 
487:         
488:         
489:         tp.unitPoints[0].rewardUnitFraction = 83;
490:         tp.unitPoints[1].rewardUnitFraction = 17;
491:         
492: 
493:         
494:         
495:         
496:         
497:         
498:         codePerDev = 1e18;
499: 
500:         
501:         uint256 _maxBondFraction = 50;
502:         tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);
503:         tp.unitPoints[0].topUpUnitFraction = 41;
504:         tp.unitPoints[1].topUpUnitFraction = 9;
505: 
506:         
507:         
508:         uint256 _maxBond = (_inflationPerSecond * _epochLen * _maxBondFraction) / 100;
509:         maxBond = uint96(_maxBond);
510:         effectiveBond = uint96(_maxBond);
511:     }
```


</details>

## [Low-2] Division operations should always be performed after multiplication operations 

### Resolution 
Perform multiplication operations first

Num of instances: 4

### Findings 


<details><summary>Click to show findings</summary>

['[292](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L292-L309)']
```solidity
292:     function _addNominee(Nominee memory nominee) internal { // <= FOUND
293:         
294:         bytes32 nomineeHash = keccak256(abi.encode(nominee));
295:         if (mapNomineeIds[nomineeHash] > 0) {
296:             revert NomineeAlreadyExists(nominee.account, nominee.chainId);
297:         }
298: 
299:         
300:         if (mapRemovedNominees[nomineeHash] > 0) {
301:             revert NomineeRemoved(nominee.account, nominee.chainId);
302:         }
303: 
304:         uint256 id = setNominees.length;
305:         mapNomineeIds[nomineeHash] = id;
306:         
307:         setNominees.push(nominee);
308: 
309:         uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK; // <= FOUND
310:         timeWeight[nomineeHash] = nextTime;
311: 
312:         
313:         address localDispenser = dispenser;
314:         if (localDispenser != address(0)) {
315:             IDispenser(localDispenser).addNominee(nomineeHash);
316:         }
317: 
318:         emit AddNominee(nominee.account, nominee.chainId, id);
319:     }
```
['[419](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L419-L424)']
```solidity
419:     function _nomineeRelativeWeight(
420:         bytes32 account,
421:         uint256 chainId,
422:         uint256 time
423:     ) internal view returns (uint256 weight, uint256 totalSum) {
424:         uint256 t = time / WEEK * WEEK; // <= FOUND
425:         totalSum = pointsSum[t].bias;
426: 
427:         Nominee memory nominee = Nominee(account, chainId);
428:         bytes32 nomineeHash = keccak256(abi.encode(nominee));
429: 
430:         if (totalSum > 0) {
431:             uint256 nomineeWeight = pointsWeight[nomineeHash][t].bias;
432:             weight = 1e18 * nomineeWeight / totalSum;
433:         }
434:     }
```
['[473](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L473-L489)']
```solidity
473:     function voteForNomineeWeights(bytes32 account, uint256 chainId, uint256 weight) public { // <= FOUND
474:         
475:         bytes32 nomineeHash = keccak256(abi.encode(Nominee(account, chainId)));
476: 
477:         
478:         if (mapRemovedNominees[nomineeHash] > 0) {
479:             revert NomineeRemoved(account, chainId);
480:         }
481: 
482:         
483:         int128 userSlope = IVEOLAS(ve).getLastUserPoint(msg.sender).slope;
484:         if (userSlope < 0) {
485:             revert NegativeSlope(msg.sender, userSlope);
486:         }
487: 
488:         uint256 lockEnd = IVEOLAS(ve).lockedEnd(msg.sender);
489:         uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK; // <= FOUND
490: 
491:         
492:         if (nextTime >= lockEnd) {
493:             revert LockExpired(msg.sender, lockEnd, nextTime);
494:         }
495: 
496:         
497:         if (weight > MAX_WEIGHT) {
498:             revert Overflow(weight, MAX_WEIGHT);
499:         }
500: 
501:         
502:         uint256 nextAllowedVotingTime = lastUserVote[msg.sender][nomineeHash] + WEIGHT_VOTE_DELAY;
503:         if (nextAllowedVotingTime > block.timestamp) {
504:             revert VoteTooOften(msg.sender, block.timestamp, nextAllowedVotingTime);
505:         }
506: 
507:         
508:         VotedSlope memory oldSlope = voteUserSlopes[msg.sender][nomineeHash];
509:         uint256 oldBias;
510:         if (oldSlope.end > nextTime) {
511:             oldBias = oldSlope.slope * (oldSlope.end - nextTime);
512:         }
513: 
514:         VotedSlope memory newSlope = VotedSlope({
515:             slope: uint256(uint128(userSlope)) * weight / MAX_WEIGHT,
516:             end: lockEnd,
517:             power: weight
518:         });
519: 
520:         uint256 newBias = newSlope.slope * (lockEnd - nextTime);
521: 
522:         uint256 powerUsed = voteUserPower[msg.sender];
523:         powerUsed = powerUsed + newSlope.power - oldSlope.power;
524:         voteUserPower[msg.sender] = powerUsed;
525:         if (powerUsed > MAX_WEIGHT) {
526:             revert Overflow(powerUsed, MAX_WEIGHT);
527:         }
528: 
529:         
530:         
531:         
532:         pointsWeight[nomineeHash][nextTime].bias = _maxAndSub(_getWeight(account, chainId) + newBias, oldBias);
533:         pointsSum[nextTime].bias = _maxAndSub(_getSum() + newBias, oldBias);
534:         if (oldSlope.end > nextTime) {
535:             pointsWeight[nomineeHash][nextTime].slope =
536:                 _maxAndSub(pointsWeight[nomineeHash][nextTime].slope + newSlope.slope, oldSlope.slope);
537:             pointsSum[nextTime].slope = _maxAndSub(pointsSum[nextTime].slope + newSlope.slope, oldSlope.slope);
538:         } else {
539:             pointsWeight[nomineeHash][nextTime].slope += newSlope.slope;
540:             pointsSum[nextTime].slope += newSlope.slope;
541:         }
542:         if (oldSlope.end > block.timestamp) {
543:             
544:             changesWeight[nomineeHash][oldSlope.end] -= oldSlope.slope;
545:             changesSum[oldSlope.end] -= oldSlope.slope;
546:         }
547:         
548:         changesWeight[nomineeHash][newSlope.end] += newSlope.slope;
549:         changesSum[newSlope.end] += newSlope.slope;
550: 
551:         voteUserSlopes[msg.sender][nomineeHash] = newSlope;
552: 
553:         
554:         lastUserVote[msg.sender][nomineeHash] = block.timestamp;
555: 
556:         emit VoteForNominee(msg.sender, account, chainId, weight);
557:     }
```
['[586](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L586-L605)']
```solidity
586:     function removeNominee(bytes32 account, uint256 chainId) external { // <= FOUND
587:         
588:         if (msg.sender != owner) {
589:             revert OwnerOnly(owner, msg.sender);
590:         }
591: 
592:         
593:         Nominee memory nominee = Nominee(account, chainId);
594:         bytes32 nomineeHash = keccak256(abi.encode(nominee));
595: 
596:         
597:         uint256 id = mapNomineeIds[nomineeHash];
598:         if (id == 0) {
599:             revert NomineeDoesNotExist(account, chainId);
600:         }
601: 
602:         
603:         uint256 oldWeight = _getWeight(account, chainId);
604:         uint256 oldSum = _getSum();
605:         uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK; // <= FOUND
606:         pointsWeight[nomineeHash][nextTime].bias = 0;
607:         timeWeight[nomineeHash] = nextTime;
608: 
609:         
610:         uint256 newSum = oldSum - oldWeight;
611:         pointsSum[nextTime].bias = newSum;
612:         timeSum = nextTime;
613: 
614:         
615:         mapRemovedNominees[nomineeHash] = setRemovedNominees.length;
616:         setRemovedNominees.push(nominee);
617: 
618:         
619:         mapNomineeIds[nomineeHash] = 0;
620: 
621:         
622:         nominee = setNominees[setNominees.length - 1];
623:         bytes32 replacedNomineeHash = keccak256(abi.encode(nominee));
624:         mapNomineeIds[replacedNomineeHash] = id;
625:         setNominees[id] = nominee;
626:         
627:         setNominees.pop();
628: 
629:         
630:         address localDispenser = dispenser;
631:         if (localDispenser != address(0)) {
632:             IDispenser(localDispenser).removeNominee(nomineeHash);
633:         }
634: 
635:         emit RemoveNominee(account, chainId, newSum);
636:     }
```


</details>

## [Low-3] Function with two array parameter missing a length check 

### Resolution 
In Solidity, if two array parameters are used within a function and one of their lengths is used as the for-loop range, it's essential to have a length check. If the arrays are not the same length, you could experience out-of-bounds errors or unintended behavior. This could happen if the function tries to access an index that doesn't exist in the shorter array.

Resolution: Always validate that the lengths of both arrays are the same before entering the loop. Add a require statement at the start of the function to check that both arrays are of equal length. This helps maintain the integrity of the function and prevents potential errors due to differing array lengths. This requirement ensures the function fails early if the arrays don't match, rather than failing unpredictably or silently during execution.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[86](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L86-L94)']
```solidity
86:     function _deposit(address[] memory targets, uint256[] memory stakingIncentives) internal {
87:         
88:         if (_locked > 1) {
89:             revert ReentrancyGuard();
90:         }
91:         _locked = 2;
92: 
93:         
94:         for (uint256 i = 0; i < targets.length; ++i) { // <= FOUND
95:             address target = targets[i];
96:             uint256 amount = stakingIncentives[i];
97: 
98:             
99:             uint256 limitAmount = IStakingFactory(stakingFactory).verifyInstanceAndGetEmissionsAmount(target);
100: 
101:             
102:             if (limitAmount == 0) {
103:                 revert TargetEmissionsZero(target);
104:             }
105: 
106:             
107:             if (amount > limitAmount) {
108:                 uint256 refundAmount = amount - limitAmount;
109:                 amount = limitAmount;
110: 
111:                 
112:                 IToken(olas).transfer(timelock, refundAmount);
113: 
114:                 emit AmountRefunded(target, refundAmount);
115:             }
116: 
117:             
118:             IToken(olas).approve(target, amount);
119:             IStaking(target).deposit(amount);
120: 
121:             emit StakingTargetDeposited(target, amount);
122:         }
123: 
124:         _locked = 1;
125:     }
```


</details>

## [Low-4] Code does not follow the best practice of check-effects-interaction 

### Resolution 
The "check-effects-interaction" pattern is a best practice in smart contract development, emphasizing the order of operations in functions to prevent reentrancy attacks. Violations arise when a function interacts with external contracts before settling internal state changes or checks. This misordering can expose the contract to potential threats. To adhere to this pattern, first ensure all conditions or checks are satisfied, then update any internal states, and only after these steps, interact with external contracts or addresses. Rearranging operations to this recommended sequence bolsters contract security and aligns with established best practices in the Ethereum community.

Num of instances: 4

### Findings 


<details><summary>Click to show findings</summary>

['[294](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingFactory.sol#L294-L300)']
```solidity
294:     function verifyInstanceAndGetEmissionsAmount(address instance) external view returns (uint256 amount) { // <= FOUND
295:         
296:         bool success = verifyInstance(instance);
297: 
298:         if (success) {
299:             
300:             amount = IStaking(instance).emissionsAmount(); // <= FOUND
301: 
302:             
303:             address localVerifier = verifier;
304:             if (localVerifier != address(0)) {
305:                 
306:                 uint256 maxEmissions = IStakingVerifier(localVerifier).getEmissionsAmountLimit(instance); // <= FOUND
307:                 
308:                 if (amount > maxEmissions) {
309:                     amount = maxEmissions;
310:                 }
311:             }
312:         }
313:     }
```
['[989](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L989-L1002)']
```solidity
989:     function trackServiceDonations(
990:         address donator,
991:         uint256[] memory serviceIds,
992:         uint256[] memory amounts,
993:         uint256 donationETH
994:     ) external {
995:         
996:         if (treasury != msg.sender) {
997:             revert ManagerOnly(msg.sender, treasury);
998:         }
999: 
1000:         
1001:         address bList = donatorBlacklist;
1002:         if (bList != address(0) && IDonatorBlacklist(bList).isDonatorBlacklisted(donator)) { // <= FOUND
1003:             revert DonatorBlacklisted(donator);
1004:         }
1005: 
1006:         
1007:         uint256 numServices = serviceIds.length;
1008:         
1009:         for (uint256 i = 0; i < numServices; ++i) {
1010:             
1011:             if (!IServiceRegistry(serviceRegistry).exists(serviceIds[i])) {
1012:                 revert ServiceDoesNotExist(serviceIds[i]);
1013:             }
1014:         }
1015:         
1016:         uint256 curEpoch = epochCounter;
1017:         
1018:         donationETH += mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH;
1019:         mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH = uint96(donationETH);
1020: 
1021:         
1022:         _trackServiceDonations(donator, serviceIds, amounts, curEpoch);
1023: 
1024:         
1025:         lastDonationBlockNumber = uint32(block.number);
1026:     }
```
['[573](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L573-L596)']
```solidity
573:     function _calculateStakingIncentivesBatch(
574:         uint256 numClaimedEpochs,
575:         uint256[] memory chainIds,
576:         bytes32[][] memory stakingTargets
577:     ) internal returns (
578:         uint256[] memory totalAmounts,
579:         uint256[][] memory stakingIncentives,
580:         uint256[] memory transferAmounts
581:     ) {
582:         
583:         
584:         
585:         
586:         totalAmounts = new uint256[](3);
587: 
588:         
589:         stakingIncentives = new uint256[][](chainIds.length);
590:         transferAmounts = new uint256[](chainIds.length);
591: 
592:         
593:         for (uint256 i = 0; i < chainIds.length; ++i) {
594:             
595:             address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];
596:             uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals(); // <= FOUND
597: 
598:             stakingIncentives[i] = new uint256[](stakingTargets[i].length);
599:             
600:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {
601:                 
602:                 (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =
603:                     calculateStakingIncentives(numClaimedEpochs, chainIds[i], stakingTargets[i][j], bridgingDecimals);
604: 
605:                 
606:                 mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;
607: 
608:                 stakingIncentives[i][j] = stakingIncentive;
609:                 transferAmounts[i] += stakingIncentive;
610:                 totalAmounts[0] += stakingIncentive;
611:                 totalAmounts[2] += returnAmount;
612:             }
613: 
614:             
615:             if (transferAmounts[i] > 0) {
616:                 uint256 withheldAmount = mapChainIdWithheldAmounts[chainIds[i]];
617:                 if (withheldAmount > 0) {
618:                     if (withheldAmount >= transferAmounts[i]) {
619:                         withheldAmount -= transferAmounts[i];
620:                         transferAmounts[i] = 0;
621:                     } else {
622:                         transferAmounts[i] -= withheldAmount;
623:                         withheldAmount = 0;
624:                     }
625:                     mapChainIdWithheldAmounts[chainIds[i]] = withheldAmount;
626:                 }
627:             }
628: 
629:             
630:             totalAmounts[1] += transferAmounts[i];
631:         }
632:     }
```
['[1199](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L1199-L1217)']
```solidity
1199:     function syncWithheldAmountMaintenance(uint256 chainId, uint256 amount) external { // <= FOUND
1200:         
1201:         if (msg.sender != owner) {
1202:             revert OwnerOnly(msg.sender, owner);
1203:         }
1204: 
1205:         
1206:         if (chainId == 0 || amount == 0) {
1207:             revert ZeroValue();
1208:         }
1209: 
1210:         
1211:         if (chainId == block.chainid) {
1212:             revert WrongChainId(chainId);
1213:         }
1214: 
1215:         
1216:         address depositProcessor = mapChainIdDepositProcessors[chainId];
1217:         uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals(); // <= FOUND
1218: 
1219:         
1220:         if (bridgingDecimals < 18) {
1221:             uint256 normalizedAmount = amount / (10 ** (18 - bridgingDecimals));
1222:             normalizedAmount *= 10 ** (18 - bridgingDecimals);
1223:             
1224:             amount = normalizedAmount;
1225:         }
1226: 
1227:         
1228:         uint256 withheldAmount = mapChainIdWithheldAmounts[chainId] + amount;
1229:         if (withheldAmount > type(uint96).max) {
1230:             revert Overflow(withheldAmount, type(uint96).max);
1231:         }
1232: 
1233:         
1234:         mapChainIdWithheldAmounts[chainId] = withheldAmount;
1235: 
1236:         emit WithheldAmountSynced(chainId, amount, withheldAmount);
1237:     }
```


</details>

## [Low-5] Incorrect comparison against a max value 

### Resolution 
Comparing a value using `>= MAX_VALUE` is conceptually incorrect when `MAX_VALUE` is defined as the upper limit that a variable can take. According to the definition of a maximum value, the condition should only revert if the variable is greater than `MAX_VALUE`, not equal to it. Using `>=` can introduce unintended behavior, as the condition will trigger a revert even when the variable reaches its legitimate upper bound. This can lead to bugs and vulnerabilities, as well as hamper the contract's intended functionality. The resolution is to strictly use `>` when making comparisons against a defined `MAX_VALUE` to align with its conceptual definition and to prevent unintended reverts or vulnerabilities.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[774](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L774-L774)']
```solidity
774:         if (block.timestamp >= maxAllowedTime) { // <= FOUND
```


</details>

## [Low-6] Public or external initialize functions should be protected with the initializer modifier

### Resolution 
The initializer modifiers ensures two key aspects. A) Only an authorised account can initialize B) initialization can only be done once. Consider using such a modifier and in instances where initialization can be done more than once, the function name should be changed to reflect that.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[20](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingNativeToken.sol#L20-L21)']
```solidity
20:     function initialize(StakingParams memory _stakingParams) external { // <= FOUND
21:         _initialize(_stakingParams); // <= FOUND
22:     }
```
['[54](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingToken.sol#L54-L60)']
```solidity
54:     function initialize( // <= FOUND
55:         StakingParams memory _stakingParams,
56:         address _serviceRegistryTokenUtility,
57:         address _stakingToken
58:     ) external
59:     {
60:         _initialize(_stakingParams); // <= FOUND
61: 
62:         
63:         if (_stakingToken == address(0) || _serviceRegistryTokenUtility == address(0)) {
64:             revert ZeroTokenAddress();
65:         }
66: 
67:         stakingToken = _stakingToken;
68:         serviceRegistryTokenUtility = _serviceRegistryTokenUtility;
69:     }
```


</details>

## [Low-7] Sending tokens in a for loop

### Resolution 
Performing token transfers in a loop in a Solidity contract is generally not recommended due to various reasons. One of these reasons is the "Fail-Silently" issue.

In a Solidity loop, if one transfer operation fails, it causes the entire transaction to fail. This issue can be particularly troublesome when you're dealing with multiple transfers in one transaction. For instance, if you're looping through an array of recipients to distribute dividends or rewards, a single failed transfer will prevent all the subsequent recipients from receiving their transfers. This could be due to the recipient contract throwing an exception or due to other issues like a gas limit being exceeded.

Moreover, such a design could also inadvertently lead to a situation where a malicious contract intentionally causes a failure when receiving Ether to prevent other participants from getting their rightful transfers. This could open up avenues for griefing attacks in the system.

Resolution: To mitigate this problem, it's typically recommended to follow the "withdraw pattern" in your contracts instead of pushing payments. In this model, each recipient would be responsible for triggering their own payment. This separates each transfer operation, so a failure in one doesn't impact the others. Additionally, it greatly reduces the chance of malicious interference as the control over fund withdrawal lies with the intended recipient and not with an external loop operation.

Num of instances: 3

### Findings 


<details><summary>Click to show findings</summary>

['[450](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L450-L456)']
```solidity
450:        for (uint256 i = 0; i < chainIds.length; ++i) {
451:             
452:             address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];
453: 
454:             
455:             if (transferAmounts[i] > 0) {
456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]); // <= FOUND
457:             }
458: 
459:             
460:             uint256 numActualTargets;
461:             bool[] memory positions = new bool[](stakingTargets[i].length);
462:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {
463:                 if (stakingIncentives[i][j] > 0) {
464:                     positions[j] = true;
465:                     ++numActualTargets;
466:                 }
467:             }
468: 
469:             
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
481:             
482:             if (chainIds[i] <= MAX_EVM_CHAIN_ID) {
483:                 
484:                 address[] memory stakingTargetsEVM = new address[](updatedStakingTargets.length);
485:                 for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {
486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));
487:                 }
488: 
489:                 
490:                 IDepositProcessor(depositProcessor).sendMessageBatch{value:valueAmounts[i]}(stakingTargetsEVM,
491:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);
492:             } else {
493:                 
494:                 IDepositProcessor(depositProcessor).sendMessageBatchNonEVM{value:valueAmounts[i]}(updatedStakingTargets,
495:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);
496:             }
497:         }
```
['[94](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L94-L112)']
```solidity
94:         for (uint256 i = 0; i < targets.length; ++i) {
95:             address target = targets[i];
96:             uint256 amount = stakingIncentives[i];
97: 
98:             
99:             uint256 limitAmount = IStakingFactory(stakingFactory).verifyInstanceAndGetEmissionsAmount(target);
100: 
101:             
102:             if (limitAmount == 0) {
103:                 revert TargetEmissionsZero(target);
104:             }
105: 
106:             
107:             if (amount > limitAmount) {
108:                 uint256 refundAmount = amount - limitAmount;
109:                 amount = limitAmount;
110: 
111:                 
112:                 IToken(olas).transfer(timelock, refundAmount); // <= FOUND
113: 
114:                 emit AmountRefunded(target, refundAmount);
115:             }
116: 
117:             
118:             IToken(olas).approve(target, amount);
119:             IStaking(target).deposit(amount);
120: 
121:             emit StakingTargetDeposited(target, amount);
122:         }
```
['[450](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L450-L456)']
```solidity
450:         for (uint256 i = 0; i < chainIds.length; ++i) {
451:             
452:             address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];
453: 
454:             
455:             if (transferAmounts[i] > 0) {
456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]); // <= FOUND
457:             }
458: 
459:             
460:             uint256 numActualTargets;
461:             bool[] memory positions = new bool[](stakingTargets[i].length);
462:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {
463:                 if (stakingIncentives[i][j] > 0) {
464:                     positions[j] = true;
465:                     ++numActualTargets;
466:                 }
467:             }
468: 
469:             
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
481:             
482:             if (chainIds[i] <= MAX_EVM_CHAIN_ID) {
483:                 
484:                 address[] memory stakingTargetsEVM = new address[](updatedStakingTargets.length);
485:                 for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {
486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));
487:                 }
488: 
489:                 
490:                 IDepositProcessor(depositProcessor).sendMessageBatch{value:valueAmounts[i]}(stakingTargetsEVM,
491:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);
492:             } else {
493:                 
494:                 IDepositProcessor(depositProcessor).sendMessageBatchNonEVM{value:valueAmounts[i]}(updatedStakingTargets,
495:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]);
496:             }
497:         }
```


</details>

## [Low-8] Bridge function must check for native token

### Resolution 
Functions that bridge ERC20 tokens often do not interact with Ether (ETH), the native cryptocurrency of Ethereum. In these functions, `msg.value` represents the amount of Ether sent in the transaction. 

When a function is marked as `payable`, it indicates that the function is allowed to receive Ether. However, if these functions are not designed to handle Ether, and someone unintentionally or intentionally sends Ether to these functions, the Ether could be locked in the contract forever, leading to a loss of funds. This is because if the contract does not have a specific function to withdraw or refund this Ether, there's no way to retrieve it.

To mitigate this issue, these functions should explicitly require that `msg.value == 0`. This statement ensures that no Ether is being sent in the transaction. If `msg.value` is not equal to zero, the transaction will revert, thereby preventing accidental loss of Ether. 

In conclusion, requiring `msg.value == 0` in functions that bridge ERC20 tokens ensures that only token transactions are processed, and prevents inadvertent loss of Ether, thereby increasing the security of the contract.

Num of instances: 4

### Findings 


<details><summary>Click to show findings</summary>

['[132](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L132-L132)']
```solidity
132:     function sendMessage(
133:         address target,
134:         uint256 stakingIncentive,
135:         bytes memory bridgePayload,
136:         uint256 transferAmount
137:     ) external virtual payable {
138:         
139:         if (msg.sender != l1Dispenser) {
140:             revert ManagerOnly(l1Dispenser, msg.sender);
141:         }
142: 
143:         
144:         address[] memory targets = new address[](1);
145:         targets[0] = target;
146:         uint256[] memory stakingIncentives = new uint256[](1);
147:         stakingIncentives[0] = stakingIncentive;
148: 
149:         
150:         uint256 sequence = _sendMessage(targets, stakingIncentives, bridgePayload, transferAmount);
151: 
152:         
153:         stakingBatchNonce++;
154: 
155:         emit MessagePosted(sequence, targets, stakingIncentives, transferAmount);
156:     }
```
['[164](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L164-L164)']
```solidity
164:     function sendMessageBatch(
165:         address[] memory targets,
166:         uint256[] memory stakingIncentives,
167:         bytes memory bridgePayload,
168:         uint256 transferAmount
169:     ) external virtual payable {
170:         
171:         if (msg.sender != l1Dispenser) {
172:             revert ManagerOnly(l1Dispenser, msg.sender);
173:         }
174: 
175:         
176:         uint256 sequence = _sendMessage(targets, stakingIncentives, bridgePayload, transferAmount);
177: 
178:         
179:         stakingBatchNonce++;
180: 
181:         emit MessagePosted(sequence, targets, stakingIncentives, transferAmount);
182:     }
```
['[323](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L323-L323)']
```solidity
323:     function syncWithheldTokens(bytes memory bridgePayload) external payable {
324:         
325:         if (_locked > 1) {
326:             revert ReentrancyGuard();
327:         }
328:         _locked = 2;
329: 
330:         
331:         if (paused == 2) {
332:             revert Paused();
333:         }
334: 
335:         
336:         uint256 amount = withheldAmount;
337:         if (amount == 0) {
338:             revert ZeroValue();
339:         }
340: 
341:         
342:         withheldAmount = 0;
343: 
344:         
345:         _sendMessage(amount, bridgePayload);
346: 
347:         emit WithheldAmountSynced(msg.sender, amount);
348: 
349:         _locked = 1;
350:     }
```
['[953](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L953-L953)']
```solidity
953:     function claimStakingIncentives(
954:         uint256 numClaimedEpochs,
955:         uint256 chainId,
956:         bytes32 stakingTarget,
957:         bytes memory bridgePayload
958:     ) external payable {
959:         
960:         if (_locked > 1) {
961:             revert ReentrancyGuard();
962:         }
963:         _locked = 2;
964: 
965:         
966:         if (chainId == 0) {
967:             revert ZeroValue();
968:         }
969: 
970:         
971:         if (stakingTarget == 0) {
972:             revert ZeroAddress();
973:         }
974: 
975:         
976:         uint256 localMaxNumClaimingEpochs = maxNumClaimingEpochs;
977:         if (numClaimedEpochs > localMaxNumClaimingEpochs) {
978:             revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);
979:         }
980: 
981:         
982:         Pause currentPause = paused;
983:         if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
984:             ITreasury(treasury).paused() == 2) {
985:             revert Paused();
986:         }
987: 
988:         
989:         address depositProcessor = mapChainIdDepositProcessors[chainId];
990:         uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();
991: 
992:         
993:         (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =
994:             calculateStakingIncentives(numClaimedEpochs, chainId, stakingTarget, bridgingDecimals);
995: 
996:         
997:         mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;
998: 
999:         
1000:         if (returnAmount > 0) {
1001:             ITokenomics(tokenomics).refundFromStaking(returnAmount);
1002:         }
1003: 
1004:         uint256 transferAmount;
1005: 
1006:         
1007:         if (stakingIncentive > 0) {
1008:             transferAmount = stakingIncentive;
1009:             
1010:             
1011:             
1012:             uint256 withheldAmount = mapChainIdWithheldAmounts[chainId];
1013:             if (withheldAmount > 0) {
1014:                 
1015:                 if (withheldAmount >= transferAmount) {
1016:                     withheldAmount -= transferAmount;
1017:                     transferAmount = 0;
1018:                 } else {
1019:                     
1020:                     transferAmount -= withheldAmount;
1021:                     withheldAmount = 0;
1022:                 }
1023:                 mapChainIdWithheldAmounts[chainId] = withheldAmount;
1024:             }
1025: 
1026:             
1027:             if (transferAmount > 0) {
1028:                 uint256 olasBalance = IToken(olas).balanceOf(address(this));
1029: 
1030:                 
1031:                 ITreasury(treasury).withdrawToAccount(address(this), 0, transferAmount);
1032: 
1033:                 
1034:                 olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;
1035:                 if (olasBalance != transferAmount) {
1036:                     revert WrongAmount(olasBalance, transferAmount);
1037:                 }
1038:             }
1039: 
1040:             
1041:             _distributeStakingIncentives(chainId, stakingTarget, stakingIncentive, bridgePayload, transferAmount);
1042:         }
1043: 
1044:         emit StakingIncentivesClaimed(msg.sender, stakingIncentive, transferAmount, returnAmount);
1045: 
1046:         _locked = 1;
1047:     }
```


</details>

## [Low-9] Contract contains payable functions but no withdraw/sweep function

### Resolution 
In smart contract development, particularly for Ethereum, having payable functions without a corresponding withdraw or sweep function can lead to potential issues. Payable functions allow the contract to receive Ether, but without a mechanism to withdraw these funds, the Ether can become locked within the contract indefinitely. This situation might be intentional in some cases (like a burn function), but generally, it’s a design oversight. A withdraw or sweep function is necessary to transfer Ether out of the contract to a specific address, typically the owner's or a designated recipient. Without this, the contract lacks flexibility in managing its funds, potentially leading to lost or inaccessible Ether.

Num of instances: 7

### Findings 


<details><summary>Click to show findings</summary>

['[81](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingFactory.sol#L81-L81)']
```solidity
81: contract StakingFactory 
```
['[17](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingNativeToken.sol#L17-L17)']
```solidity
17: contract StakingNativeToken is StakingBase 
```
['[20](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingProxy.sol#L20-L20)']
```solidity
20: contract StakingProxy 
```
['[23](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L23-L23)']
```solidity
23: contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 
```
['[59](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L59-L59)']
```solidity
59: contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 
```
['[37](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L37-L37)']
```solidity
37: contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 
```
['[253](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L253-L253)']
```solidity
253: contract Dispenser 
```


</details>

## [Low-10] Calculations using non internal accounting such as balanceOf or totalSupply can introduce potential donation attack vectors

### Resolution 
Calculations using non-internal accounting, such as balanceOf or totalSupply, can introduce potential donation attack vectors. For instance, consider a scenario where a reward calculation uses totalSupply during a deposit. An attacker could exploit this by donating a large amount of tokens to the vault before the user tries to redeem their withdrawal. This manipulation causes the totalSupply to change significantly, altering the reward calculations and potentially leading to unexpected or unfair outcomes for users.

Let's say a smart contract calculates user rewards based on their share of the total token supply:

```solidity
uint256 reward = (userDeposit * totalRewards) / totalSupply;
```

If an attacker donates a large number of tokens to the vault before users withdraw, totalSupply increases, thereby diluting each user's share of the rewards. This discrepancy between expected and actual rewards can undermine user trust and contract integrity.

The risks associated with this attack include reward manipulation, where attackers can alter totalSupply to reduce the share of honest users, and economic attacks, where significant token donations destabilize the contract's economic assumptions, leading to unfair advantages or financial losses. Additionally, users may find their rewards significantly lower than expected, causing confusion and dissatisfaction.

To prevent such vulnerabilities, it's essential to use internal variables to track deposits, withdrawals, and rewards instead of relying on balanceOf or totalSupply. This approach isolates contract logic from external token movements. Implementing a snapshot mechanism to capture totalSupply at specific points in time ensures consistent reward calculations. Introducing limits on the number of tokens that can be deposited or donated in a single transaction can also prevent significant manipulation. 

Num of instances: 7

### Findings 


<details><summary>Click to show findings</summary>

['[266](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L266-L287)']
```solidity
266:     function redeem(address target, uint256 amount, uint256 batchNonce) external { // <= FOUND
267:         
268:         if (_locked > 1) {
269:             revert ReentrancyGuard();
270:         }
271:         _locked = 2;
272: 
273:         
274:         if (paused == 2) {
275:             revert Paused();
276:         }
277: 
278:         
279:         bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
280:         bool queued = stakingQueueingNonces[queueHash];
281:         
282:         if (!queued) {
283:             revert TargetAmountNotQueued(target, amount, batchNonce);
284:         }
285: 
286:         
287:         uint256 olasBalance = IToken(olas).balanceOf(address(this)); // <= FOUND
288:         if (olasBalance >= amount) {
289:             
290:             IToken(olas).approve(target, amount);
291:             IStaking(target).deposit(amount);
292: 
293:             emit StakingTargetDeposited(target, amount);
294: 
295:             
296:             stakingQueueingNonces[queueHash] = false;
297:         } else {
298:             
299:             revert InsufficientBalance(olasBalance, amount);
300:         }
301: 
302:         _locked = 1;
303:     }
```
['[412](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L412-L440)']
```solidity
412:     function migrate(address newL2TargetDispenser) external { // <= FOUND
413:         
414:         if (_locked > 1) {
415:             revert ReentrancyGuard();
416:         }
417:         _locked = 2;
418: 
419:         
420:         if (msg.sender != owner) {
421:             revert OwnerOnly(msg.sender, owner);
422:         }
423: 
424:         
425:         if (paused == 1) {
426:             revert Unpaused();
427:         }
428: 
429:         
430:         if (newL2TargetDispenser.code.length == 0) {
431:             revert WrongAccount(newL2TargetDispenser);
432:         }
433: 
434:         
435:         if (newL2TargetDispenser == address(this)) {
436:             revert WrongAccount(address(this));
437:         }
438: 
439:         
440:         uint256 amount = IToken(olas).balanceOf(address(this)); // <= FOUND
441:         
442:         if (amount > 0) {
443:             bool success = IToken(olas).transfer(newL2TargetDispenser, amount);
444:             if (!success) {
445:                 revert TransferFailed(olas, address(this), newL2TargetDispenser, amount);
446:             }
447:         }
448: 
449:         
450:         owner = address(0);
451: 
452:         emit Migrated(msg.sender, newL2TargetDispenser, amount);
453: 
454:         
455:     }
```
['[1307](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1307-L1329)']
```solidity
1307:     function accountOwnerIncentives(
1308:         address account,
1309:         uint256[] memory unitTypes,
1310:         uint256[] memory unitIds
1311:     ) external returns (uint256 reward, uint256 topUp) {
1312:         
1313:         if (dispenser != msg.sender) {
1314:             revert ManagerOnly(msg.sender, dispenser);
1315:         }
1316: 
1317:         
1318:         if (unitTypes.length != unitIds.length) {
1319:             revert WrongArrayLength(unitTypes.length, unitIds.length);
1320:         }
1321: 
1322:         
1323:         address[] memory registries = new address[](2);
1324:         (registries[0], registries[1]) = (componentRegistry, agentRegistry);
1325: 
1326:         
1327:         uint256[] memory registriesSupply = new uint256[](2);
1328:         for (uint256 i = 0; i < 2; ++i) {
1329:             registriesSupply[i] = IToken(registries[i]).totalSupply(); // <= FOUND
1330:         }
1331: 
1332:         
1333:         uint256[] memory lastIds = new uint256[](2);
1334:         for (uint256 i = 0; i < unitIds.length; ++i) {
1335:             
1336:             if (unitTypes[i] > 1) {
1337:                 revert Overflow(unitTypes[i], 1);
1338:             }
1339: 
1340:             
1341:             if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
1342:                 revert WrongUnitId(unitIds[i], unitTypes[i]);
1343:             }
1344:             lastIds[unitTypes[i]] = unitIds[i];
1345: 
1346:             
1347:             address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
1348:             if (unitOwner != account) {
1349:                 revert OwnerOnly(unitOwner, account);
1350:             }
1351:         }
1352: 
1353:         
1354:         uint256 curEpoch = epochCounter;
1355: 
1356:         for (uint256 i = 0; i < unitIds.length; ++i) {
1357:             
1358:             uint256 lastEpoch = mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch;
1359:             
1360:             
1361:             
1362:             if (lastEpoch > 0 && lastEpoch < curEpoch) {
1363:                 _finalizeIncentivesForUnitId(lastEpoch, unitTypes[i], unitIds[i]);
1364:                 
1365:                 mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch = 0;
1366:             }
1367: 
1368:             
1369:             reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;
1370:             mapUnitIncentives[unitTypes[i]][unitIds[i]].reward = 0;
1371:             
1372:             topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;
1373:             mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp = 0;
1374:         }
1375:     }
```
['[1384](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1384-L1401)']
```solidity
1384:     function getOwnerIncentives(
1385:         address account,
1386:         uint256[] memory unitTypes,
1387:         uint256[] memory unitIds
1388:     ) external view returns (uint256 reward, uint256 topUp) {
1389:         
1390:         if (unitTypes.length != unitIds.length) {
1391:             revert WrongArrayLength(unitTypes.length, unitIds.length);
1392:         }
1393: 
1394:         
1395:         address[] memory registries = new address[](2);
1396:         (registries[0], registries[1]) = (componentRegistry, agentRegistry);
1397: 
1398:         
1399:         uint256[] memory registriesSupply = new uint256[](2);
1400:         for (uint256 i = 0; i < 2; ++i) {
1401:             registriesSupply[i] = IToken(registries[i]).totalSupply(); // <= FOUND
1402:         }
1403: 
1404:         
1405:         uint256[] memory lastIds = new uint256[](2);
1406:         for (uint256 i = 0; i < unitIds.length; ++i) {
1407:             
1408:             if (unitTypes[i] > 1) {
1409:                 revert Overflow(unitTypes[i], 1);
1410:             }
1411: 
1412:             
1413:             if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
1414:                 revert WrongUnitId(unitIds[i], unitTypes[i]);
1415:             }
1416:             lastIds[unitTypes[i]] = unitIds[i];
1417: 
1418:             
1419:             address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
1420:             if (unitOwner != account) {
1421:                 revert OwnerOnly(unitOwner, account);
1422:             }
1423:         }
1424: 
1425:         
1426:         uint256 curEpoch = epochCounter;
1427: 
1428:         for (uint256 i = 0; i < unitIds.length; ++i) {
1429:             
1430:             uint256 lastEpoch = mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch;
1431:             
1432:             if (lastEpoch > 0 && lastEpoch < curEpoch) {
1433:                 
1434:                 
1435:                 uint256 totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeReward;
1436:                 if (totalIncentives > 0) {
1437:                     totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].rewardUnitFraction;
1438:                     
1439:                     reward += totalIncentives / 100;
1440:                 }
1441:                 
1442:                 totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeTopUp;
1443:                 if (totalIncentives > 0) {
1444:                     
1445:                     
1446:                     totalIncentives *= mapEpochTokenomics[lastEpoch].epochPoint.totalTopUpsOLAS;
1447:                     totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].topUpUnitFraction;
1448:                     uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100;
1449:                     
1450:                     topUp += totalIncentives / sumUnitIncentives;
1451:                 }
1452:             }
1453: 
1454:             
1455:             reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;
1456:             
1457:             topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;
1458:         }
1459:     }
```
['[789](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L789-L822)']
```solidity
789:     function claimOwnerIncentives(
790:         uint256[] memory unitTypes,
791:         uint256[] memory unitIds
792:     ) external returns (uint256 reward, uint256 topUp) {
793:         
794:         if (_locked > 1) {
795:             revert ReentrancyGuard();
796:         }
797:         _locked = 2;
798: 
799:         
800:         Pause currentPause = paused;
801:         if (currentPause == Pause.DevIncentivesPaused || currentPause == Pause.AllPaused ||
802:             ITreasury(treasury).paused() == 2) {
803:             revert Paused();
804:         }
805: 
806:         
807:         (reward, topUp) = ITokenomics(tokenomics).accountOwnerIncentives(msg.sender, unitTypes, unitIds);
808: 
809:         bool success;
810:         
811:         if ((reward + topUp) > 0) {
812:             
813:             uint256 olasBalance;
814:             if (topUp > 0) {
815:                 olasBalance = IToken(olas).balanceOf(msg.sender); // <= FOUND
816:             }
817: 
818:             success = ITreasury(treasury).withdrawToAccount(msg.sender, reward, topUp);
819: 
820:             
821:             if (topUp > 0){
822:                 olasBalance = IToken(olas).balanceOf(msg.sender) - olasBalance; // <= FOUND
823:                 if (olasBalance != topUp) {
824:                     revert WrongAmount(olasBalance, topUp);
825:                 }
826:             }
827:         }
828: 
829:         
830:         if (!success) {
831:             revert ClaimIncentivesFailed(msg.sender, reward, topUp);
832:         }
833: 
834:         emit IncentivesClaimed(msg.sender, reward, topUp);
835: 
836:         _locked = 1;
837:     }
```
['[953](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L953-L1034)']
```solidity
953:     function claimStakingIncentives(
954:         uint256 numClaimedEpochs,
955:         uint256 chainId,
956:         bytes32 stakingTarget,
957:         bytes memory bridgePayload
958:     ) external payable {
959:         
960:         if (_locked > 1) {
961:             revert ReentrancyGuard();
962:         }
963:         _locked = 2;
964: 
965:         
966:         if (chainId == 0) {
967:             revert ZeroValue();
968:         }
969: 
970:         
971:         if (stakingTarget == 0) {
972:             revert ZeroAddress();
973:         }
974: 
975:         
976:         uint256 localMaxNumClaimingEpochs = maxNumClaimingEpochs;
977:         if (numClaimedEpochs > localMaxNumClaimingEpochs) {
978:             revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);
979:         }
980: 
981:         
982:         Pause currentPause = paused;
983:         if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
984:             ITreasury(treasury).paused() == 2) {
985:             revert Paused();
986:         }
987: 
988:         
989:         address depositProcessor = mapChainIdDepositProcessors[chainId];
990:         uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();
991: 
992:         
993:         (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =
994:             calculateStakingIncentives(numClaimedEpochs, chainId, stakingTarget, bridgingDecimals);
995: 
996:         
997:         mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;
998: 
999:         
1000:         if (returnAmount > 0) {
1001:             ITokenomics(tokenomics).refundFromStaking(returnAmount);
1002:         }
1003: 
1004:         uint256 transferAmount;
1005: 
1006:         
1007:         if (stakingIncentive > 0) {
1008:             transferAmount = stakingIncentive;
1009:             
1010:             
1011:             
1012:             uint256 withheldAmount = mapChainIdWithheldAmounts[chainId];
1013:             if (withheldAmount > 0) {
1014:                 
1015:                 if (withheldAmount >= transferAmount) {
1016:                     withheldAmount -= transferAmount;
1017:                     transferAmount = 0;
1018:                 } else {
1019:                     
1020:                     transferAmount -= withheldAmount;
1021:                     withheldAmount = 0;
1022:                 }
1023:                 mapChainIdWithheldAmounts[chainId] = withheldAmount;
1024:             }
1025: 
1026:             
1027:             if (transferAmount > 0) {
1028:                 uint256 olasBalance = IToken(olas).balanceOf(address(this)); // <= FOUND
1029: 
1030:                 
1031:                 ITreasury(treasury).withdrawToAccount(address(this), 0, transferAmount);
1032: 
1033:                 
1034:                 olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance; // <= FOUND
1035:                 if (olasBalance != transferAmount) {
1036:                     revert WrongAmount(olasBalance, transferAmount);
1037:                 }
1038:             }
1039: 
1040:             
1041:             _distributeStakingIncentives(chainId, stakingTarget, stakingIncentive, bridgePayload, transferAmount);
1042:         }
1043: 
1044:         emit StakingIncentivesClaimed(msg.sender, stakingIncentive, transferAmount, returnAmount);
1045: 
1046:         _locked = 1;
1047:     }
```
['[1058](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L1058-L1112)']
```solidity
1058:     function claimStakingIncentivesBatch(
1059:         uint256 numClaimedEpochs,
1060:         uint256[] memory chainIds,
1061:         bytes32[][] memory stakingTargets,
1062:         bytes[] memory bridgePayloads,
1063:         uint256[] memory valueAmounts
1064:     ) external payable {
1065:         
1066:         if (_locked > 1) {
1067:             revert ReentrancyGuard();
1068:         }
1069:         _locked = 2;
1070: 
1071:         _checkOrderAndValues(chainIds, stakingTargets, bridgePayloads, valueAmounts);
1072: 
1073:         
1074:         uint256 localMaxNumClaimingEpochs = maxNumClaimingEpochs;
1075:         if (numClaimedEpochs > localMaxNumClaimingEpochs) {
1076:             revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);
1077:         }
1078: 
1079:         Pause currentPause = paused;
1080:         if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
1081:             ITreasury(treasury).paused() == 2) {
1082:             revert Paused();
1083:         }
1084: 
1085:         
1086:         
1087:         
1088:         
1089:         uint256[] memory totalAmounts;
1090:         
1091:         uint256[][] memory stakingIncentives;
1092:         uint256[] memory transferAmounts;
1093: 
1094:         (totalAmounts, stakingIncentives, transferAmounts) = _calculateStakingIncentivesBatch(numClaimedEpochs, chainIds,
1095:             stakingTargets);
1096: 
1097:         
1098:         if (totalAmounts[2] > 0) {
1099:             ITokenomics(tokenomics).refundFromStaking(totalAmounts[2]);
1100:         }
1101: 
1102:         
1103:         if (totalAmounts[0] > 0) {
1104:             
1105:             if (totalAmounts[1] > 0) {
1106:                 uint256 olasBalance = IToken(olas).balanceOf(address(this)); // <= FOUND
1107: 
1108:                 
1109:                 ITreasury(treasury).withdrawToAccount(address(this), 0, totalAmounts[1]);
1110: 
1111:                 
1112:                 olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance; // <= FOUND
1113:                 if (olasBalance != totalAmounts[1]) {
1114:                     revert WrongAmount(olasBalance, totalAmounts[1]);
1115:                 }
1116:             }
1117: 
1118:             
1119:             _distributeStakingIncentivesBatch(chainIds, stakingTargets, stakingIncentives, bridgePayloads, transferAmounts,
1120:                 valueAmounts);
1121:         }
1122: 
1123:         emit StakingIncentivesClaimed(msg.sender, totalAmounts[0], totalAmounts[1], totalAmounts[2]);
1124: 
1125:         _locked = 1;
1126:     }
```


</details>

## [Low-11] Solidity version 0.8.20 won't work on all chains due to PUSH0

### Resolution 
Solidity version 0.8.20 uses the new Shanghai EVM which introduces the PUSH0 opcode, this may not be implemented on all chains and L2 thus reducing the portability and compatability of the code. Consider using a earlier solidity version.

Num of instances: 3

### Findings 


<details><summary>Click to show findings</summary>

['[2](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L2-L2)']
```solidity
2: pragma solidity ^0.8.25; // <= FOUND
```
['[2](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/utils/SafeTransferLib.sol#L2-L2)']
```solidity
2: pragma solidity ^0.8.23; // <= FOUND
```
['[2](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L2-L2)']
```solidity
2: pragma solidity ^0.8.18; // <= FOUND
```


</details>

## [Low-12] Using block.number is not fully L2 compatible

### Resolution 
Using block.number  can break compatibility with some L2s such as Optimism whos time between blocks differ from Ethereum and isn't constant. Consider using block.timestamp to prevent compatibility issues

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[1025](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1025-L1026)']
```solidity
1025:         
1026:         lastDonationBlockNumber = uint32(block.number); // <= FOUND
```
['[1091](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1091-L1092)']
```solidity
1091:         
1092:         if (lastDonationBlockNumber == block.number) { // <= FOUND
```


</details>

## [Low-13] Int casting block.timestamp can reduce the lifespan of a contract

### Resolution 
Casting `block.timestamp` (a Unix epoch time in seconds) to an integer type with a smaller size in Solidity can potentially reduce the lifespan of a smart contract. The `block.timestamp` returns a value that increases over time and can eventually exceed the maximum value storable in smaller integer types. For example, casting `block.timestamp` to an `int32` could lead to an overflow by the year 2038 (the year when Unix time exceeds the maximum value storable in a 32-bit integer). This overflow would result in incorrect timestamp values, potentially disrupting the contract's logic or rendering it unusable. Therefore, it's important to use an integer type that can accommodate the range of expected timestamp values throughout the contract's intended lifespan.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[477](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L477-L478)']
```solidity
477:         
478:         mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp); // <= FOUND
```
['[1219](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1219-L1220)']
```solidity
1219:         
1220:         tp.epochPoint.endTime = uint32(block.timestamp); // <= FOUND
```


</details>

## [Low-14] The call abi.encodeWithSelector is not type safe

### Resolution 
In Solidity, `abi.encodeWithSelector` is a function used for encoding data along with a function selector, but it is not type-safe. This means it does not enforce type checking at compile time, potentially leading to errors if arguments do not match the expected types. Starting from version 0.8.13, Solidity introduced `abi.encodeCall`, which offers a safer alternative. `abi.encodeCall` ensures type safety by performing a full type check, aligning the types of the arguments with the function signature. This reduces the risk of bugs caused by typographical errors or mismatched types. Using `abi.encodeCall` enhances the reliability and security of the code by ensuring that the encoded data strictly conforms to the specified types, making it a preferable choice in Solidity versions 0.8.13 and above.

Num of instances: 6

### Findings 


<details><summary>Click to show findings</summary>

['[73](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L73-L74)']
```solidity
73:         
74:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives)); // <= FOUND
```
['[85](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L85-L86)']
```solidity
85:         
86:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount)); // <= FOUND
```
['[73](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L73-L74)']
```solidity
73:             
74:             bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives)); // <= FOUND
```
['[73](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L73-L74)']
```solidity
73:         
74:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives)); // <= FOUND
```
['[85](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L85-L86)']
```solidity
85:         
86:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount)); // <= FOUND
```
['[73](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L73-L74)']
```solidity
73:             
74:             bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives)); // <= FOUND
```


</details>

## [Low-15] Loss of precision

### Resolution 
Dividing by large numbers in Solidity can cause a loss of precision due to the language's inherent integer division behavior. Solidity does not support floating-point arithmetic, and as a result, division between integers yields an integer result, truncating any fractional part. When dividing by a large number, the resulting value may become significantly smaller, leading to a loss of precision, as the fractional part is discarded.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[893](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingBase.sol#L893-L904)']
```solidity
893:     function calculateStakingLastReward(uint256 serviceId) public view returns (uint256 reward) { // <= FOUND
894:         
895:         (uint256 lastAvailableRewards, uint256 numServices, uint256 totalRewards, uint256[] memory eligibleServiceIds,
896:             uint256[] memory eligibleServiceRewards, , , ) = _calculateStakingRewards();
897: 
898:         
899:         for (uint256 i = 0; i < numServices; ++i) {
900:             
901:             if (eligibleServiceIds[i] == serviceId) {
902:                 
903:                 if (totalRewards > lastAvailableRewards) {
904:                     reward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards; // <= FOUND
905:                 } else {
906:                     reward = eligibleServiceRewards[i];
907:                 }
908:                 break;
909:             }
910:         }
911:     }
```
['[408](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L408-L472)']
```solidity
408:     function initializeTokenomics(
409:         address _olas,
410:         address _treasury,
411:         address _depository,
412:         address _dispenser,
413:         address _ve,
414:         uint256 _epochLen,
415:         address _componentRegistry,
416:         address _agentRegistry,
417:         address _serviceRegistry,
418:         address _donatorBlacklist
419:     ) external {
420:         
421:         if (owner != address(0)) {
422:             revert AlreadyInitialized();
423:         }
424: 
425:         
426:         if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||
427:             _ve == address(0) || _componentRegistry == address(0) || _agentRegistry == address(0) ||
428:             _serviceRegistry == address(0)) {
429:             revert ZeroAddress();
430:         }
431: 
432:         
433:         owner = msg.sender;
434:         _locked = 1;
435:         epsilonRate = 1e17;
436:         veOLASThreshold = 10_000e18;
437: 
438:         
439:         if (uint32(_epochLen) < MIN_EPOCH_LENGTH) {
440:             revert LowerThan(_epochLen, MIN_EPOCH_LENGTH);
441:         }
442: 
443:         
444:         if (uint32(_epochLen) > MAX_EPOCH_LENGTH) {
445:             revert Overflow(_epochLen, MAX_EPOCH_LENGTH);
446:         }
447: 
448:         
449:         olas = _olas;
450:         treasury = _treasury;
451:         depository = _depository;
452:         dispenser = _dispenser;
453:         ve = _ve;
454:         epochLen = uint32(_epochLen);
455:         componentRegistry = _componentRegistry;
456:         agentRegistry = _agentRegistry;
457:         serviceRegistry = _serviceRegistry;
458:         donatorBlacklist = _donatorBlacklist;
459: 
460:         
461:         uint256 _timeLaunch = IOLAS(_olas).timeLaunch();
462:         
463:         if (block.timestamp >= (_timeLaunch + ONE_YEAR)) {
464:             revert Overflow(_timeLaunch + ONE_YEAR, block.timestamp);
465:         }
466:         
467:         
468:         uint256 zeroYearSecondsLeft = uint32(_timeLaunch + ONE_YEAR - block.timestamp);
469:         
470:         
471:         
472:         uint256 _inflationPerSecond = getInflationForYear(0) / zeroYearSecondsLeft; // <= FOUND
473:         inflationPerSecond = uint96(_inflationPerSecond);
474:         timeLaunch = uint32(_timeLaunch);
475: 
476:         
477:         mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);
478: 
479:         
480:         epochCounter = 1;
481:         TokenomicsPoint storage tp = mapEpochTokenomics[1];
482: 
483:         
484:         devsPerCapital = 1e18;
485:         tp.epochPoint.idf = 1e18;
486: 
487:         
488:         
489:         tp.unitPoints[0].rewardUnitFraction = 83;
490:         tp.unitPoints[1].rewardUnitFraction = 17;
491:         
492: 
493:         
494:         
495:         
496:         
497:         
498:         codePerDev = 1e18;
499: 
500:         
501:         uint256 _maxBondFraction = 50;
502:         tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);
503:         tp.unitPoints[0].topUpUnitFraction = 41;
504:         tp.unitPoints[1].topUpUnitFraction = 9;
505: 
506:         
507:         
508:         uint256 _maxBond = (_inflationPerSecond * _epochLen * _maxBondFraction) / 100;
509:         maxBond = uint96(_maxBond);
510:         effectiveBond = uint96(_maxBond);
511:     }
```


</details>

## [Low-16] Missing zero address check in constructor

### Resolution 
In Solidity, constructors often take address parameters to initialize important components of a contract, such as owner or linked contracts. However, without a check, there's a risk that an address parameter could be mistakenly set to the zero address (0x0). This could occur due to a mistake or oversight during contract deployment. A zero address in a crucial role can cause serious issues, as it cannot perform actions like a normal address, and any funds sent to it are irretrievable. Therefore, it's crucial to include a zero address check in constructors to prevent such potential problems. If a zero address is detected, the constructor should revert the transaction.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[103](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingFactory.sol#L103-L103)']
```solidity
103:     constructor(address _verifier) { // <= FOUND
104:         owner = msg.sender;
105:         verifier = _verifier;
106:     }
```
['[36](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L36-L40)']
```solidity
36:     constructor(
37:         address _olas, // <= FOUND
38:         address _proxyFactory, // <= FOUND
39:         address _l2MessageRelayer, // <= FOUND
40:         address _l1DepositProcessor, // <= FOUND
41:         uint256 _l1SourceChainId
42:     )
43:         DefaultTargetDispenserL2(_olas, _proxyFactory, _l2MessageRelayer, _l1DepositProcessor, _l1SourceChainId)
44:     {
45:         
46:         uint160 offset = uint160(0x1111000000000000000000000000000000001111);
47:         unchecked {
48:             l1AliasedDepositProcessor = address(uint160(_l1DepositProcessor) + offset);
49:         }
50:     }
```


</details>

## [Low-17] Off-by-one timestamp error

### Resolution 
In Solidity, using `>=` or `<=` to compare against `block.timestamp` (alias `now`) may introduce off-by-one errors due to the fact that `block.timestamp` is only updated once per block and its value remains constant throughout the block's execution. If an operation happens at the exact second when `block.timestamp` changes, it could result in unexpected behavior. To avoid this, it's safer to use strict inequality operators (`>` or `<`). For instance, if a condition should only be met after a certain time, use `block.timestamp > time` rather than `block.timestamp >= time`. This way, potential off-by-one errors due to the exact timing of block mining are mitigated, leading to safer, more predictable contract behavior.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[408](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L408-L463)']
```solidity
408:     function initializeTokenomics(
409:         address _olas,
410:         address _treasury,
411:         address _depository,
412:         address _dispenser,
413:         address _ve,
414:         uint256 _epochLen,
415:         address _componentRegistry,
416:         address _agentRegistry,
417:         address _serviceRegistry,
418:         address _donatorBlacklist
419:     ) external {
420:         
421:         if (owner != address(0)) {
422:             revert AlreadyInitialized();
423:         }
424: 
425:         
426:         if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||
427:             _ve == address(0) || _componentRegistry == address(0) || _agentRegistry == address(0) ||
428:             _serviceRegistry == address(0)) {
429:             revert ZeroAddress();
430:         }
431: 
432:         
433:         owner = msg.sender;
434:         _locked = 1;
435:         epsilonRate = 1e17;
436:         veOLASThreshold = 10_000e18;
437: 
438:         
439:         if (uint32(_epochLen) < MIN_EPOCH_LENGTH) {
440:             revert LowerThan(_epochLen, MIN_EPOCH_LENGTH);
441:         }
442: 
443:         
444:         if (uint32(_epochLen) > MAX_EPOCH_LENGTH) {
445:             revert Overflow(_epochLen, MAX_EPOCH_LENGTH);
446:         }
447: 
448:         
449:         olas = _olas;
450:         treasury = _treasury;
451:         depository = _depository;
452:         dispenser = _dispenser;
453:         ve = _ve;
454:         epochLen = uint32(_epochLen);
455:         componentRegistry = _componentRegistry;
456:         agentRegistry = _agentRegistry;
457:         serviceRegistry = _serviceRegistry;
458:         donatorBlacklist = _donatorBlacklist;
459: 
460:         
461:         uint256 _timeLaunch = IOLAS(_olas).timeLaunch();
462:         
463:         if (block.timestamp >= (_timeLaunch + ONE_YEAR)) { // <= FOUND
464:             revert Overflow(_timeLaunch + ONE_YEAR, block.timestamp);
465:         }
466:         
467:         
468:         uint256 zeroYearSecondsLeft = uint32(_timeLaunch + ONE_YEAR - block.timestamp);
469:         
470:         
471:         
472:         uint256 _inflationPerSecond = getInflationForYear(0) / zeroYearSecondsLeft;
473:         inflationPerSecond = uint96(_inflationPerSecond);
474:         timeLaunch = uint32(_timeLaunch);
475: 
476:         
477:         mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);
478: 
479:         
480:         epochCounter = 1;
481:         TokenomicsPoint storage tp = mapEpochTokenomics[1];
482: 
483:         
484:         devsPerCapital = 1e18;
485:         tp.epochPoint.idf = 1e18;
486: 
487:         
488:         
489:         tp.unitPoints[0].rewardUnitFraction = 83;
490:         tp.unitPoints[1].rewardUnitFraction = 17;
491:         
492: 
493:         
494:         
495:         
496:         
497:         
498:         codePerDev = 1e18;
499: 
500:         
501:         uint256 _maxBondFraction = 50;
502:         tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);
503:         tp.unitPoints[0].topUpUnitFraction = 41;
504:         tp.unitPoints[1].topUpUnitFraction = 9;
505: 
506:         
507:         
508:         uint256 _maxBond = (_inflationPerSecond * _epochLen * _maxBondFraction) / 100;
509:         maxBond = uint96(_maxBond);
510:         effectiveBond = uint96(_maxBond);
511:     }
```
['[750](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L750-L774)']
```solidity
750:     function removeNominee(bytes32 nomineeHash) external { // <= FOUND
751:         
752:         if (msg.sender != voteWeighting) {
753:             revert ManagerOnly(msg.sender, voteWeighting);
754:         }
755: 
756:         
757:         if (retainerHash == nomineeHash) {
758:             revert WrongAccount(retainer);
759:         }
760: 
761:         
762:         uint256 eCounter = ITokenomics(tokenomics).epochCounter();
763: 
764:         
765:         
766:         uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(eCounter - 1);
767: 
768:         
769:         uint256 epochLen = ITokenomics(tokenomics).epochLen();
770: 
771:         
772:         
773:         uint256 maxAllowedTime = endTime + epochLen - 1 weeks;
774:         if (block.timestamp >= maxAllowedTime) { // <= FOUND
775:             revert Overflow(block.timestamp, maxAllowedTime);
776:         }
777: 
778:         
779:         mapRemovedNomineeEpochs[nomineeHash] = eCounter;
780:     }
```


</details>

## [Low-18] Using zero as a parameter

### Resolution 
Taking 0 as a valid argument in Solidity without checks can lead to severe security issues. A historical example is the infamous 0x0 address bug where numerous tokens were lost. This happens because '0' can be interpreted as an uninitialized address, leading to transfers to the '0x0' address, effectively burning tokens. Moreover, 0 as a denominator in division operations would cause a runtime exception. It's also often indicative of a logical error in the caller's code. It's important to always validate input and handle edge cases like 0 appropriately. Use `require()` statements to enforce conditions and provide clear error messages to facilitate debugging and safer code.

Num of instances: 5

### Findings 


<details><summary>Click to show findings</summary>

['[119](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L119-L190)']
```solidity
119:     function _sendMessage(
120:         address[] memory targets,
121:         uint256[] memory stakingIncentives,
122:         bytes memory bridgePayload,
123:         uint256 transferAmount
124:     ) internal override returns (uint256 sequence) {
125:         
126:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {
127:             revert IncorrectDataLength(BRIDGE_PAYLOAD_LENGTH, bridgePayload.length);
128:         }
129: 
130:         
131:         (address refundAccount, uint256 gasPriceBid, uint256 maxSubmissionCostToken, uint256 gasLimitMessage,
132:             uint256 maxSubmissionCostMessage) = abi.decode(bridgePayload, (address, uint256, uint256, uint256, uint256));
133: 
134:         
135:         if (refundAccount == address(0)) {
136:             refundAccount = msg.sender;
137:         }
138: 
139:         
140:         
141:         if (gasPriceBid < 2 || gasLimitMessage < 2 || maxSubmissionCostMessage == 0) {
142:             revert ZeroValue();
143:         }
144: 
145:         
146:         if (gasLimitMessage > MESSAGE_GAS_LIMIT) {
147:             revert Overflow(gasLimitMessage, MESSAGE_GAS_LIMIT);
148:         }
149: 
150:         
151:         
152:         uint256[] memory cost = new uint256[](2);
153:         if (transferAmount > 0) {
154:             if (maxSubmissionCostToken == 0) {
155:                 revert ZeroValue();
156:             }
157: 
158:             
159:             cost[0] = maxSubmissionCostToken + TOKEN_GAS_LIMIT * gasPriceBid;
160:         }
161: 
162:         
163:         cost[1] = maxSubmissionCostMessage + gasLimitMessage * gasPriceBid;
164:         
165:         uint256 totalCost = cost[0] + cost[1];
166: 
167:         
168:         if (totalCost > msg.value) {
169:             revert LowerThan(msg.value, totalCost);
170:         }
171: 
172:         if (transferAmount > 0) {
173:             
174:             IToken(olas).approve(l1ERC20Gateway, transferAmount);
175: 
176:             
177:             
178:             
179:             bytes memory submissionCostData = abi.encode(maxSubmissionCostToken, "");
180: 
181:             
182:             IBridge(l1TokenRelayer).outboundTransferCustomRefund{value: cost[0]}(olas, refundAccount,
183:                 l2TargetDispenser, transferAmount, TOKEN_GAS_LIMIT, gasPriceBid, submissionCostData);
184:         }
185: 
186:         
187:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));
188: 
189:         
190:         sequence = IBridge(l1MessageRelayer).createRetryableTicket{value: cost[1]}(l2TargetDispenser, 0, // <= FOUND
191:             maxSubmissionCostMessage, refundAccount, refundAccount, gasLimitMessage, gasPriceBid, data);
192:     }
```
['[59](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L59-L59)']
```solidity
59:     function _sendMessage(
60:         address[] memory targets,
61:         uint256[] memory stakingIncentives,
62:         bytes memory bridgePayload,
63:         uint256 transferAmount
64:     ) internal override returns (uint256 sequence) {
65:         
66:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {
67:             revert IncorrectDataLength(BRIDGE_PAYLOAD_LENGTH, bridgePayload.length);
68:         }
69: 
70:         
71:         (address refundAccount, uint256 gasLimitMessage) = abi.decode(bridgePayload, (address, uint256));
72: 
73:         
74:         if (gasLimitMessage == 0) {
75:             revert ZeroValue();
76:         }
77: 
78:         
79:         if (gasLimitMessage > MESSAGE_GAS_LIMIT) {
80:             revert Overflow(gasLimitMessage, MESSAGE_GAS_LIMIT);
81:         }
82: 
83:         
84:         if (refundAccount == address(0)) {
85:             refundAccount = msg.sender;
86:         }
87: 
88:         
89:         bytes memory data = abi.encode(targets, stakingIncentives);
90: 
91:         
92:         
93:         
94:         
95:         
96:         sequence = sendTokenWithPayloadToEvm(uint16(wormholeTargetChainId), l2TargetDispenser, data, 0,
97:             gasLimitMessage, olas, transferAmount, uint16(l2TargetChainId), refundAccount);
98:     }
```
['[89](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L89-L121)']
```solidity
89:     function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override { // <= FOUND
90:         
91:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {
92:             revert IncorrectDataLength(BRIDGE_PAYLOAD_LENGTH, bridgePayload.length);
93:         }
94: 
95:         
96:         (address refundAccount, uint256 gasLimitMessage) = abi.decode(bridgePayload, (address, uint256));
97:         
98:         if (refundAccount == address(0)) {
99:             refundAccount = msg.sender;
100:         }
101: 
102:         
103:         if (gasLimitMessage < GAS_LIMIT) {
104:             gasLimitMessage = GAS_LIMIT;
105:         }
106: 
107:         if (gasLimitMessage > MAX_GAS_LIMIT) {
108:             gasLimitMessage = MAX_GAS_LIMIT;
109:         }
110: 
111:         
112:         (uint256 cost, ) = IBridge(l2MessageRelayer).quoteEVMDeliveryPrice(uint16(l1SourceChainId), 0, gasLimitMessage); // <= FOUND
113: 
114:         
115:         if (cost > msg.value) {
116:             revert LowerThan(msg.value, cost);
117:         }
118: 
119:         
120:         uint64 sequence = IBridge(l2MessageRelayer).sendPayloadToEvm{value: cost}(uint16(l1SourceChainId),
121:             l1DepositProcessor, abi.encode(amount), 0, gasLimitMessage, uint16(l1SourceChainId), refundAccount); // <= FOUND
122: 
123:         emit MessagePosted(sequence, msg.sender, l1DepositProcessor, amount);
124:     }
```
['[953](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L953-L1031)']
```solidity
953:     function claimStakingIncentives(
954:         uint256 numClaimedEpochs,
955:         uint256 chainId,
956:         bytes32 stakingTarget,
957:         bytes memory bridgePayload
958:     ) external payable {
959:         
960:         if (_locked > 1) {
961:             revert ReentrancyGuard();
962:         }
963:         _locked = 2;
964: 
965:         
966:         if (chainId == 0) {
967:             revert ZeroValue();
968:         }
969: 
970:         
971:         if (stakingTarget == 0) {
972:             revert ZeroAddress();
973:         }
974: 
975:         
976:         uint256 localMaxNumClaimingEpochs = maxNumClaimingEpochs;
977:         if (numClaimedEpochs > localMaxNumClaimingEpochs) {
978:             revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);
979:         }
980: 
981:         
982:         Pause currentPause = paused;
983:         if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
984:             ITreasury(treasury).paused() == 2) {
985:             revert Paused();
986:         }
987: 
988:         
989:         address depositProcessor = mapChainIdDepositProcessors[chainId];
990:         uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();
991: 
992:         
993:         (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =
994:             calculateStakingIncentives(numClaimedEpochs, chainId, stakingTarget, bridgingDecimals);
995: 
996:         
997:         mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;
998: 
999:         
1000:         if (returnAmount > 0) {
1001:             ITokenomics(tokenomics).refundFromStaking(returnAmount);
1002:         }
1003: 
1004:         uint256 transferAmount;
1005: 
1006:         
1007:         if (stakingIncentive > 0) {
1008:             transferAmount = stakingIncentive;
1009:             
1010:             
1011:             
1012:             uint256 withheldAmount = mapChainIdWithheldAmounts[chainId];
1013:             if (withheldAmount > 0) {
1014:                 
1015:                 if (withheldAmount >= transferAmount) {
1016:                     withheldAmount -= transferAmount;
1017:                     transferAmount = 0;
1018:                 } else {
1019:                     
1020:                     transferAmount -= withheldAmount;
1021:                     withheldAmount = 0;
1022:                 }
1023:                 mapChainIdWithheldAmounts[chainId] = withheldAmount;
1024:             }
1025: 
1026:             
1027:             if (transferAmount > 0) {
1028:                 uint256 olasBalance = IToken(olas).balanceOf(address(this));
1029: 
1030:                 
1031:                 ITreasury(treasury).withdrawToAccount(address(this), 0, transferAmount); // <= FOUND
1032: 
1033:                 
1034:                 olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;
1035:                 if (olasBalance != transferAmount) {
1036:                     revert WrongAmount(olasBalance, transferAmount);
1037:                 }
1038:             }
1039: 
1040:             
1041:             _distributeStakingIncentives(chainId, stakingTarget, stakingIncentive, bridgePayload, transferAmount);
1042:         }
1043: 
1044:         emit StakingIncentivesClaimed(msg.sender, stakingIncentive, transferAmount, returnAmount);
1045: 
1046:         _locked = 1;
1047:     }
```
['[1058](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L1058-L1109)']
```solidity
1058:     function claimStakingIncentivesBatch(
1059:         uint256 numClaimedEpochs,
1060:         uint256[] memory chainIds,
1061:         bytes32[][] memory stakingTargets,
1062:         bytes[] memory bridgePayloads,
1063:         uint256[] memory valueAmounts
1064:     ) external payable {
1065:         
1066:         if (_locked > 1) {
1067:             revert ReentrancyGuard();
1068:         }
1069:         _locked = 2;
1070: 
1071:         _checkOrderAndValues(chainIds, stakingTargets, bridgePayloads, valueAmounts);
1072: 
1073:         
1074:         uint256 localMaxNumClaimingEpochs = maxNumClaimingEpochs;
1075:         if (numClaimedEpochs > localMaxNumClaimingEpochs) {
1076:             revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);
1077:         }
1078: 
1079:         Pause currentPause = paused;
1080:         if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
1081:             ITreasury(treasury).paused() == 2) {
1082:             revert Paused();
1083:         }
1084: 
1085:         
1086:         
1087:         
1088:         
1089:         uint256[] memory totalAmounts;
1090:         
1091:         uint256[][] memory stakingIncentives;
1092:         uint256[] memory transferAmounts;
1093: 
1094:         (totalAmounts, stakingIncentives, transferAmounts) = _calculateStakingIncentivesBatch(numClaimedEpochs, chainIds,
1095:             stakingTargets);
1096: 
1097:         
1098:         if (totalAmounts[2] > 0) {
1099:             ITokenomics(tokenomics).refundFromStaking(totalAmounts[2]);
1100:         }
1101: 
1102:         
1103:         if (totalAmounts[0] > 0) {
1104:             
1105:             if (totalAmounts[1] > 0) {
1106:                 uint256 olasBalance = IToken(olas).balanceOf(address(this));
1107: 
1108:                 
1109:                 ITreasury(treasury).withdrawToAccount(address(this), 0, totalAmounts[1]); // <= FOUND
1110: 
1111:                 
1112:                 olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;
1113:                 if (olasBalance != totalAmounts[1]) {
1114:                     revert WrongAmount(olasBalance, totalAmounts[1]);
1115:                 }
1116:             }
1117: 
1118:             
1119:             _distributeStakingIncentivesBatch(chainIds, stakingTargets, stakingIncentives, bridgePayloads, transferAmounts,
1120:                 valueAmounts);
1121:         }
1122: 
1123:         emit StakingIncentivesClaimed(msg.sender, totalAmounts[0], totalAmounts[1], totalAmounts[2]);
1124: 
1125:         _locked = 1;
1126:     }
```


</details>

## [Low-19] Constant decimal values

### Resolution 
The use of fixed decimal values such as 1e18 or 1e8 in Solidity contracts can lead to inaccuracies, bugs, and vulnerabilities, particularly when interacting with tokens having different decimal configurations. Not all ERC20 tokens follow the standard 18 decimal places, and assumptions about decimal places can lead to miscalculations.

Resolution: Always retrieve and use the `decimals()` function from the token contract itself when performing calculations involving token amounts. This ensures that your contract correctly handles tokens with any number of decimal places, mitigating the risk of numerical errors or under/overflows that could jeopardize contract integrity and user funds.

Num of instances: 11

### Findings 


<details><summary>Click to show findings</summary>

['[432](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L432-L432)']
```solidity
432:             weight = 1e18 * nomineeWeight / totalSum; // <= FOUND
```
['[58](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingActivityChecker.sol#L58-L58)']
```solidity
58:             uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts; // <= FOUND
```
['[484](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L484-L485)']
```solidity
484:         
485:         devsPerCapital = 1e18; // <= FOUND
```
['[485](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L485-L485)']
```solidity
485:         tp.epochPoint.idf = 1e18; // <= FOUND
```
['[498](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L498-L505)']
```solidity
498:         
499: 
500:         
501:         
502:         
503:         
504:         
505:         codePerDev = 1e18; // <= FOUND
```
['[1060](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1060-L1061)']
```solidity
1060:         
1061:         idf = 1e18 + fKD; // <= FOUND
```
['[1280](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1280-L1281)']
```solidity
1280:             
1281:             nextEpochPoint.epochPoint.idf = 1e18; // <= FOUND
```
['[913](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L913-L914)']
```solidity
913:                 
914:                 returnAmount = ((stakingDiff + availableStakingAmount) * stakingWeight) / 1e18; // <= FOUND
```
['[916](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L916-L917)']
```solidity
916:                 
917:                 stakingIncentive = (availableStakingAmount * stakingWeight) / 1e18; // <= FOUND
```
['[918](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L918-L919)']
```solidity
918:                 
919:                 returnAmount = (stakingDiff * stakingWeight) / 1e18; // <= FOUND
```
['[1159](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L1159-L1159)']
```solidity
1159:         totalReturnAmount /= 1e18; // <= FOUND
```


</details>

## [Low-20] Arrays can grow in size without a way to shrink them

### Resolution 
It's a good practice to maintain control over the size of array state variables in Solidity, especially if they are dynamically updated. If a contract includes a mechanism to push items into an array, it should ideally also provide a mechanism to remove items. This is because Solidity arrays don't automatically shrink when items are deleted - their length needs to be manually adjusted.

Ignoring this can lead to bloated and inefficient contracts. For instance, iterating over a large array can cause your contract to hit the block gas limit. Additionally, if entries are only marked for deletion but never actually removed, you may end up dealing with stale or irrelevant data, which can cause logical errors.

Therefore, implementing a method to 'pop' items from arrays helps manage contract's state, improve efficiency and prevent potential issues related to gas limits or stale data. Always ensure to handle potential underflow conditions when popping elements from the array.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[170](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L170-L170)']
```solidity
170: Nominee[] public setRemovedNominees; // <= FOUND
```


</details>

## [Low-21] Revert on Transfer to the Zero Address

### Resolution 
Many ERC-20 and ERC-721 token contracts implement a safeguard that reverts transactions which attempt to transfer tokens to the zero address. This is because such transfers are often the result of programming errors. The OpenZeppelin library, a popular choice for implementing these standards, includes this safeguard. For token contract developers who want to avoid unintentional transfers to the zero address, it's good practice to include a condition that reverts the transaction if the recipient's address is the zero address. 

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[104](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingToken.sol#L104-L108)']
```solidity
104:     function _withdraw(address to, uint256 amount) internal override {
105:         
106:         balance -= amount;
107: 
108:         SafeTransferLib.safeTransfer(stakingToken, to, amount); // <= FOUND
109: 
110:         emit Withdraw(to, amount);
111:     }
```


</details>

## [Low-22] Return values not checked for approve()

### Resolution 
The ERC-20 token standard does not dictate that the approve function must return a value. The function signature in the ERC-20 standard is function approve(address spender, uint tokens) public returns (bool success);. However, a well-implemented ERC-20 token contract will typically have approve return a boolean value indicating whether or not the operation was successful.

It's crucial to note that not all token contracts follow this practice. Some might not return a value, or they might return a value in a non-standard way. This inconsistency among token contracts is one reason why it's important to handle token interactions carefully in your smart contracts and to check the return value of approve when possible.

Num of instances: 6

### Findings 


<details><summary>Click to show findings</summary>

['[191](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L191-L192)']
```solidity
191:                 
192:                 IToken(olas).approve(target, amount); // <= FOUND
```
['[191](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L191-L192)']
```solidity
191:             
192:             IToken(olas).approve(target, amount); // <= FOUND
```
['[174](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L174-L175)']
```solidity
174:             
175:             IToken(olas).approve(l1ERC20Gateway, transferAmount); // <= FOUND
```
['[65](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L65-L66)']
```solidity
65:             
66:             IToken(olas).approve(l1TokenRelayer, transferAmount); // <= FOUND
```
['[65](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L65-L68)']
```solidity
65:             
66:             
67:             
68:             IToken(olas).approve(l1TokenRelayer, transferAmount); // <= FOUND
```
['[68](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L68-L71)']
```solidity
68:             
69:             
70:             
71:             IToken(olas).approve(predicate, transferAmount); // <= FOUND
```


</details>

## [Low-23] No access control on receive/payable fallback

### Resolution 
Without access control on receive/payable fallback functions in a contract, anyone can send Ether (ETH) to the contract's address. If there's no way to withdraw those funds defined within the contract, any Ether sent, whether intentionally or by mistake, will be permanently stuck. This could lead to unintended loss of funds. Implementing proper access control ensures that only authorized addresses can interact with these functions. Resolution could involve adding access control mechanisms, like Ownable or specific permission requirements, or creating a withdrawal function accessible only to the contract's owner, thus preventing unintentional loss of funds.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[39](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingProxy.sol#L39-L39)']
```solidity
39:     fallback() external payable { // <= FOUND
40:         assembly {
41:             let implementation := sload(SERVICE_STAKING_PROXY)
42:             calldatacopy(0, 0, calldatasize())
43:             let success := delegatecall(gas(), implementation, 0, calldatasize(), 0, 0)
44:             returndatacopy(0, 0, returndatasize())
45:             if eq(success, 0) {
46:                 revert(0, returndatasize())
47:             }
48:             return(0, returndatasize())
49:         }
50:     }
```


</details>

## [Low-24] External call recipient may consume all transaction gas

### Resolution 
When making external calls, the called contract can intentionally or unintentionally consume all provided gas, leading to unintended transaction reversion. To mitigate this risk, it's crucial to specify a gas limit when making the call. By using `addr.call{gas: <amount>}("")`, you allocate a specific amount of gas to the external call, ensuring the parent transaction has gas left for post-call operations. This approach safeguards against malevolent contracts aiming to exhaust gas and provides greater control over transaction execution.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[33](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingNativeToken.sol#L33-L34)']
```solidity
33:         
34:         (bool success, ) = to.call{value: amount}(""); // <= FOUND
```
['[396](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L396-L397)']
```solidity
396:         
397:         (bool success, ) = msg.sender.call{value: amount}(""); // <= FOUND
```


</details>

## [Low-25] Unbounded state array which is iterated upon

### Resolution 
Reason: In Solidity, iteration over large arrays can lead to excessive gas consumption. In the worst case scenario, if the array size exceeds the block gas limit, it could make the operation unfeasible. This is a common problem for operations like token distribution, where one might iterate over an array of holders.

Resolution: To prevent gas problems, limit the size of arrays that will be iterated over. Implement an alternative data structure, such as a linked list, which allows for partial iteration. Another solution could be paginated processing, where elements are processed in smaller batches over multiple transactions. Lastly, the use of 'state array' with a separate index-tracking array can also help manage large datasets.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[522](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingBase.sol#L522-L550)']
```solidity
522:     function _calculateStakingRewards() internal view returns (
523:         uint256 lastAvailableRewards,
524:         uint256 numServices,
525:         uint256 totalRewards,
526:         uint256[] memory eligibleServiceIds,
527:         uint256[] memory eligibleServiceRewards,
528:         uint256[] memory serviceIds,
529:         uint256[][] memory serviceNonces,
530:         uint256[] memory serviceInactivity
531:     )
532:     {
533:         
534:         uint256 tsCheckpointLast = tsCheckpoint;
535:         lastAvailableRewards = availableRewards;
536:         if (block.timestamp - tsCheckpointLast >= livenessPeriod && lastAvailableRewards > 0) {
537:             
538:             uint256 size = setServiceIds.length;
539: 
540:             
541:             serviceIds = new uint256[](size);
542:             eligibleServiceIds = new uint256[](size);
543:             eligibleServiceRewards = new uint256[](size);
544:             serviceNonces = new uint256[][](size);
545:             serviceInactivity = new uint256[](size);
546: 
547:             
548:             for (uint256 i = 0; i < size; ++i) {
549:                 
550:                 serviceIds[i] = setServiceIds[i]; // <= FOUND
551: 
552:                 
553:                 ServiceInfo storage sInfo = mapServiceInfo[serviceIds[i]];
554: 
555:                 
556:                 
557:                 uint256 serviceCheckpoint = tsCheckpointLast;
558:                 uint256 ts = sInfo.tsStart;
559:                 
560:                 if (ts > serviceCheckpoint) {
561:                     serviceCheckpoint = ts;
562:                 }
563: 
564:                 
565:                 
566:                 ts = block.timestamp - serviceCheckpoint;
567: 
568:                 bool ratioPass;
569:                 (ratioPass, serviceNonces[i]) = _checkRatioPass(sInfo.multisig, sInfo.nonces, ts);
570: 
571:                 
572:                 if (ratioPass) {
573:                     
574:                     eligibleServiceRewards[numServices] = rewardsPerSecond * ts;
575:                     totalRewards += eligibleServiceRewards[numServices];
576:                     eligibleServiceIds[numServices] = serviceIds[i];
577:                     ++numServices;
578:                 } else {
579:                     serviceInactivity[i] = ts;
580:                 }
581:             }
582:         }
583:     }
```


</details>

## [Low-26] SafeTransferLib does not ensure that the token contract exists

### Resolution 
SafeTransferLib as similarly named function as OpenZepelins SafeERC20 module however it's functions such as safeTransferFrom don't check if the contract exists which can result in silent failures when performing such operations. As such it is recommended to perform a contract existence check beforehand. 

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[5](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingToken.sol#L5-L5)']
```solidity
5: import {SafeTransferLib} from "../utils/SafeTransferLib.sol"; // <= FOUND
```


</details>

## [Low-27] Don't assume specific ETH balance

### Resolution 
ETH balances can change due to gas costs, miner reordering, or unexpected transactions. Directly comparing an expected balance with `==` or `!=` can lead to unpredictable results. Instead, use range checks or require a minimum balance. For instance, instead of requiring an exact balance, check if an account's balance is within an acceptable range or above a certain threshold. This provides flexibility and accounts for slight variations that might occur due to the dynamic nature of blockchain transactions and fees. By avoiding exact balance checks, smart contracts can avoid unintentional failures and enhance robustness.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[561](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L561-L562)']
```solidity
561:         
562:         if (msg.value != totalValueAmount) { // <= FOUND
```


</details>

## [Low-28] Prefer skip over revert model in iteration

### Resolution 
It is preferable to skip operations on an array index when a condition is not met rather than reverting the whole transaction as reverting can introduce the possiblity of malicous actors purposefully introducing array objects which fail conditional checks within for/while loops so group operations fail. As such it is recommended to simply skip such array indices over reverting unless there is a valid security or logic reason behind not doing so.

Num of instances: 6

### Findings 


<details><summary>Click to show findings</summary>

['[793](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L793-L800)']
```solidity
793:         for (uint256 i = 0; i < accounts.length; ++i) { // <= FOUND
794:             
795:             Nominee memory nominee = Nominee(accounts[i], chainIds[i]);
796:             bytes32 nomineeHash = keccak256(abi.encode(nominee));
797: 
798:             
799:             if (mapNomineeIds[nomineeHash] == 0) {
800:                 revert NomineeDoesNotExist(accounts[i], chainIds[i]); // <= FOUND
801:             }
802: 
803:             
804:             nextAllowedVotingTimes[i] = lastUserVote[voters[i]][nomineeHash] + WEIGHT_VOTE_DELAY;
805:         }
```
['[332](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingBase.sol#L332-L335)']
```solidity
332:         for (uint256 i = 0; i < _stakingParams.agentIds.length; ++i) { // <= FOUND
333:             
334:             if (_stakingParams.agentIds[i] <= agentId) {
335:                 revert WrongAgentId(_stakingParams.agentIds[i]); // <= FOUND
336:             }
337:             agentId = _stakingParams.agentIds[i];
338:             agentIds.push(agentId);
339:         }
```
['[1334](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1334-L1342)']
```solidity
1334:         for (uint256 i = 0; i < unitIds.length; ++i) { // <= FOUND
1335:             
1336:             if (unitTypes[i] > 1) {
1337:                 revert Overflow(unitTypes[i], 1); // <= FOUND
1338:             }
1339: 
1340:             
1341:             if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
1342:                 revert WrongUnitId(unitIds[i], unitTypes[i]); // <= FOUND
1343:             }
1344:             lastIds[unitTypes[i]] = unitIds[i];
1345: 
1346:             
1347:             address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
1348:             if (unitOwner != account) {
1349:                 revert OwnerOnly(unitOwner, account);
1350:             }
1351:         }
```
['[526](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L526-L554)']
```solidity
526:         for (uint256 i = 0; i < chainIds.length; ++i) { // <= FOUND
527:             
528:             
529:             if (lastChainId >= chainIds[i]) {
530:                 revert WrongChainId(chainIds[i]); // <= FOUND
531:             }
532:             lastChainId = chainIds[i];
533: 
534:             
535:             if (stakingTargets[i].length == 0) {
536:                 revert ZeroValue();
537:             }
538: 
539:             
540:             totalValueAmount += valueAmounts[i];
541: 
542:             
543:             uint256 localMaxNumStakingTargets = maxNumStakingTargets;
544:             if (stakingTargets[i].length > localMaxNumStakingTargets) {
545:                 revert Overflow(stakingTargets[i].length, localMaxNumStakingTargets); // <= FOUND
546:             }
547: 
548:             bytes32 lastTarget;
549:             
550:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
551:                 
552:                 
553:                 if (uint256(lastTarget) >= uint256(stakingTargets[i][j])) {
554:                     revert WrongAccount(stakingTargets[i][j]); // <= FOUND
555:                 }
556:                 lastTarget = stakingTargets[i][j];
557:             }
558:         }
```
['[915](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L915-L922)']
```solidity
915:             for (uint256 unitType = 0; unitType < 2; ++unitType) { // <= FOUND
916:                 
917:                 (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).
918:                     getUnitIdsOfService(IServiceRegistry.UnitType(unitType), serviceIds[i]);
919:                 
920:                 
921:                 if (numServiceUnits == 0) {
922:                     revert ServiceNeverDeployed(serviceIds[i]); // <= FOUND
923:                 }
924:                 
925:                 if (incentiveFlags[unitType] || incentiveFlags[unitType + 2]) {
926:                     
927:                     uint96 amount = uint96(amounts[i] / numServiceUnits);
928:                     
929:                     for (uint256 j = 0; j < numServiceUnits; ++j) {
930:                         
931:                         uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch;
932:                         
933:                         if (lastEpoch == 0) {
934:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
935:                         } else if (lastEpoch < curEpoch) {
936:                             
937:                             
938:                             
939:                             
940:                             _finalizeIncentivesForUnitId(lastEpoch, unitType, serviceUnitIds[j]);
941:                             
942:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
943:                         }
944:                         
945:                         if (incentiveFlags[unitType]) {
946:                             mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeReward += amount;
947:                         }
948:                         
949:                         
950:                         
951:                         if (topUpEligible && incentiveFlags[unitType + 2]) {
952:                             mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeTopUp += amount;
953:                             mapEpochTokenomics[curEpoch].unitPoints[unitType].sumUnitTopUpsOLAS += amount;
954:                         }
955:                     }
956:                 }
957: 
958:                 
959:                 for (uint256 j = 0; j < numServiceUnits; ++j) {
960:                     
961:                     if (!mapNewUnits[unitType][serviceUnitIds[j]]) {
962:                         mapNewUnits[unitType][serviceUnitIds[j]] = true;
963:                         mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;
964:                         
965:                         
966:                         address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);
967:                         if (!mapNewOwners[unitOwner]) {
968:                             mapNewOwners[unitOwner] = true;
969:                             mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;
970:                         }
971:                     }
972:                 }
973:             }
```
['[550](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L550-L554)']
```solidity
550:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
551:                 
552:                 
553:                 if (uint256(lastTarget) >= uint256(stakingTargets[i][j])) {
554:                     revert WrongAccount(stakingTargets[i][j]); // <= FOUND
555:                 }
556:                 lastTarget = stakingTargets[i][j];
557:             }
```


</details>

## [Low-29] Missing contract-existence checks before low-level calls

### Resolution 
Low-level calls in Solidity, when made to addresses without contract code, don't fail but return a successful status. This behavior can be misleading, leading to unintended consequences in dApps. Ignoring this can potentially mean acting on false positive results. To address this, apart from the conventional zero-address check, developers should verify the existence of contract code at the target address by ensuring that the code length at the specified address (`<address>.code.length`) is greater than zero. By doing so, it provides a more robust validation before executing low-level calls, safeguarding against unintentional interactions with empty addresses.

Num of instances: 3

### Findings 


<details><summary>Click to show findings</summary>

['[139](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L139-L161)']
```solidity
139:     function _processData(bytes memory data) internal {
140:         
141:         if (_locked > 1) {
142:             revert ReentrancyGuard();
143:         }
144:         _locked = 2;
145: 
146:         
147:         (address[] memory targets, uint256[] memory amounts) = abi.decode(data, (address[], uint256[]));
148: 
149:         uint256 batchNonce = stakingBatchNonce;
150:         uint256 localWithheldAmount = 0;
151:         uint256 localPaused = paused;
152: 
153:         
154:         for (uint256 i = 0; i < targets.length; ++i) {
155:             address target = targets[i];
156:             uint256 amount = amounts[i];
157: 
158:             
159:             
160:             bytes memory verifyData = abi.encodeCall(IStakingFactory.verifyInstanceAndGetEmissionsAmount, target);
161:             (bool success, bytes memory returnData) = stakingFactory.call(verifyData); // <= FOUND
162: 
163:             uint256 limitAmount;
164:             
165:             if (success && returnData.length == 32) {
166:                 limitAmount = abi.decode(returnData, (uint256));
167:             }
168: 
169:             
170:             if (limitAmount == 0) {
171:                 
172:                 localWithheldAmount += amount;
173:                 emit AmountWithheld(target, amount);
174: 
175:                 
176:                 continue;
177:             }
178: 
179:             
180:             if (amount > limitAmount) {
181:                 uint256 targetWithheldAmount = amount - limitAmount;
182:                 localWithheldAmount += targetWithheldAmount;
183:                 amount = limitAmount;
184: 
185:                 emit AmountWithheld(target, targetWithheldAmount);
186:             }
187: 
188:             
189:             if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {
190:                 
191:                 IToken(olas).approve(target, amount);
192:                 IStaking(target).deposit(amount);
193: 
194:                 emit StakingTargetDeposited(target, amount);
195:             } else {
196:                 
197:                 bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
198:                 
199:                 stakingQueueingNonces[queueHash] = true;
200: 
201:                 emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);
202:             }
203:         }
204:         
205:         stakingBatchNonce = batchNonce + 1;
206: 
207:         
208:         if (localWithheldAmount > 0) {
209:             withheldAmount += localWithheldAmount;
210:         }
211: 
212:         _locked = 1;
213:     }
```
['[28](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingNativeToken.sol#L28-L33)']
```solidity
28:     function _withdraw(address to, uint256 amount) internal override {
29:         
30:         balance -= amount;
31: 
32:         
33:         (bool success, ) = to.call{value: amount}(""); // <= FOUND
34:         if (!success) {
35:             revert TransferFailed(address(0), address(this), to, amount);
36:         }
37:     }
```
['[377](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L377-L396)']
```solidity
377:     function drain() external returns (uint256 amount) {
378:         
379:         if (_locked > 1) {
380:             revert ReentrancyGuard();
381:         }
382:         _locked = 2;
383: 
384:         
385:         if (msg.sender != owner) {
386:             revert OwnerOnly(msg.sender, owner);
387:         }
388: 
389:         
390:         amount = address(this).balance;
391:         if (amount == 0) {
392:             revert ZeroValue();
393:         }
394: 
395:         
396:         (bool success, ) = msg.sender.call{value: amount}(""); // <= FOUND
397:         if (!success) {
398:             revert TransferFailed(address(0), address(this), msg.sender, amount);
399:         }
400: 
401:         emit Drain(msg.sender, amount);
402: 
403:         _locked = 1;
404:     }
```


</details>

## [Low-30] Numbers downcast to addresses may result in collisions

### Resolution 
Downcasting numbers to addresses in blockchain contracts, particularly in Ethereum's Solidity, involves risks such as possible address collisions. A collision occurs when different inputs, when cast or hashed, generate the same output address, potentially compromising contract integrity and asset security. If an uint256, for instance, is downcast to an address (effectively an uint160) without ensuring it’s a legitimate, collision-free conversion, different uint256 inputs might yield the same address, creating vulnerabilities attackers might exploit. Implementing thorough checks and opting for secure practices, like avoiding downcasting in critical logic or utilizing mappings with original uint256 as keys, mitigates risks

Num of instances: 6

### Findings 


<details><summary>Click to show findings</summary>

['[156](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingFactory.sol#L156-L156)']
```solidity
156:         return address(uint160(uint256(hash))); // <= FOUND
```
['[48](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L48-L48)']
```solidity
48:             l1AliasedDepositProcessor = address(uint160(_l1DepositProcessor) + offset); // <= FOUND
```
['[125](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L125-L126)']
```solidity
125:         
126:         address l2Dispenser = address(uint160(uint256(sourceAddress))); // <= FOUND
```
['[164](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L164-L165)']
```solidity
164:         
165:         address processor = address(uint160(uint256(sourceProcessor))); // <= FOUND
```
['[424](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L424-L424)']
```solidity
424:             address stakingTargetEVM = address(uint160(uint256(stakingTarget))); // <= FOUND
```
['[486](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L486-L486)']
```solidity
486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j]))); // <= FOUND
```


</details>

## [Low-31] Use of abi.encodePacked with dynamic types inside keccak256

### Resolution 
Using abi.encodePacked with dynamic types for hashing functions like keccak256 can be risky due to the potential for hash collisions. This function concatenates arguments tightly, without padding, which might lead to different inputs producing the same hash. This is especially problematic with dynamic types, where the boundaries between inputs can blur. To mitigate this, use abi.encode instead. abi.encode pads its arguments to 32 bytes, creating clear distinctions between different inputs and significantly reducing the chance of hash collisions. This approach ensures more reliable and collision-resistant hashing, crucial for maintaining data integrity and security in smart contracts.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[141](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingFactory.sol#L141-L146)']
```solidity
141:     function getProxyAddressWithNonce(address implementation, uint256 localNonce) public view returns (address) { // <= FOUND
142:         
143:         bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce)); // <= FOUND
144: 
145:         
146:         bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode, // <= FOUND
147:             uint256(uint160(implementation)));
148: 
149:         
150:         bytes32 hash = keccak256(
151:             abi.encodePacked(
152:                 bytes1(0xff), address(this), salt, keccak256(deploymentData) // <= FOUND
153:             )
154:         );
155: 
156:         return address(uint160(uint256(hash)));
157:     }
```


</details>

## [Low-32] Inconsistent checks of address params against address(0)

### Resolution 
Only some address parameters are checked against address(0), to ensure consistency ensure all address parameters are checked.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[408](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L408-L418)']
```solidity
408:     function initializeTokenomics(
409:         address _olas,
410:         address _treasury,
411:         address _depository,
412:         address _dispenser,
413:         address _ve,
414:         uint256 _epochLen,
415:         address _componentRegistry,
416:         address _agentRegistry,
417:         address _serviceRegistry,
418:         address _donatorBlacklist // <= FOUND 'address _donatorBlacklist'
419:     ) external {
420:         
421:         if (owner != address(0)) {
422:             revert AlreadyInitialized();
423:         }
424: 
425:         
426:         if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||
427:             _ve == address(0) || _componentRegistry == address(0) || _agentRegistry == address(0) ||
428:             _serviceRegistry == address(0)) {
429:             revert ZeroAddress();
430:         }
431: 
432:         
433:         owner = msg.sender;
434:         _locked = 1;
435:         epsilonRate = 1e17;
436:         veOLASThreshold = 10_000e18;
437: 
438:         
439:         if (uint32(_epochLen) < MIN_EPOCH_LENGTH) {
440:             revert LowerThan(_epochLen, MIN_EPOCH_LENGTH);
441:         }
442: 
443:         
444:         if (uint32(_epochLen) > MAX_EPOCH_LENGTH) {
445:             revert Overflow(_epochLen, MAX_EPOCH_LENGTH);
446:         }
447: 
448:         
449:         olas = _olas;
450:         treasury = _treasury;
451:         depository = _depository;
452:         dispenser = _dispenser;
453:         ve = _ve;
454:         epochLen = uint32(_epochLen);
455:         componentRegistry = _componentRegistry;
456:         agentRegistry = _agentRegistry;
457:         serviceRegistry = _serviceRegistry;
458:         donatorBlacklist = _donatorBlacklist;
459: 
460:         
461:         uint256 _timeLaunch = IOLAS(_olas).timeLaunch();
462:         
463:         if (block.timestamp >= (_timeLaunch + ONE_YEAR)) {
464:             revert Overflow(_timeLaunch + ONE_YEAR, block.timestamp);
465:         }
466:         
467:         
468:         uint256 zeroYearSecondsLeft = uint32(_timeLaunch + ONE_YEAR - block.timestamp);
469:         
470:         
471:         
472:         uint256 _inflationPerSecond = getInflationForYear(0) / zeroYearSecondsLeft;
473:         inflationPerSecond = uint96(_inflationPerSecond);
474:         timeLaunch = uint32(_timeLaunch);
475: 
476:         
477:         mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);
478: 
479:         
480:         epochCounter = 1;
481:         TokenomicsPoint storage tp = mapEpochTokenomics[1];
482: 
483:         
484:         devsPerCapital = 1e18;
485:         tp.epochPoint.idf = 1e18;
486: 
487:         
488:         
489:         tp.unitPoints[0].rewardUnitFraction = 83;
490:         tp.unitPoints[1].rewardUnitFraction = 17;
491:         
492: 
493:         
494:         
495:         
496:         
497:         
498:         codePerDev = 1e18;
499: 
500:         
501:         uint256 _maxBondFraction = 50;
502:         tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);
503:         tp.unitPoints[0].topUpUnitFraction = 41;
504:         tp.unitPoints[1].topUpUnitFraction = 9;
505: 
506:         
507:         
508:         uint256 _maxBond = (_inflationPerSecond * _epochLen * _maxBondFraction) / 100;
509:         maxBond = uint96(_maxBond);
510:         effectiveBond = uint96(_maxBond);
511:     }
```


</details>

## [NonCritical-1] Greater than comparisons made on state uints that can be set to max 

### Resolution 
When state variables (uints) that can be set to their maximum value (type(uint256).max for example) are used in "greater than" comparisons, it introduces a risk of logic errors. If the state variable ever reaches this max value, any comparison expecting it to increment further will fail. This can halt or disrupt contract functionality. To avoid this, implement checks to ensure that the state variable doesn't exceed a certain threshold below the max value.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[179](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingVerifier.sol#L179-L200)']
```solidity
179:     function verifyInstance(address instance, address implementation) external view returns (bool) {
180:         
181:         if (implementationsCheck && !mapImplementations[implementation]) {
182:             return false;
183:         }
184: 
185:         
186:         if (instance.code.length == 0) {
187:             return false;
188:         }
189: 
190:         
191:         
192:         uint256 rewardsPerSecond = IStaking(instance).rewardsPerSecond();
193:         if (rewardsPerSecond > rewardsPerSecondLimit) { // <= FOUND
194:             return false;
195:         }
196: 
197:         
198:         
199:         uint256 numServices = IStaking(instance).maxNumServices();
200:         if (numServices > numServicesLimit) { // <= FOUND
201:             return false;
202:         }
203: 
204:         
205:         
206:         bytes memory tokenData = abi.encodeCall(IStaking.stakingToken, ());
207:         (bool success, bytes memory returnData) = instance.staticcall(tokenData);
208: 
209:         
210:         if (success) {
211:             
212:             if (returnData.length == 32) {
213:                 address token = abi.decode(returnData, (address));
214:                 if (token != olas) {
215:                     return false;
216:                 }
217:             } else {
218:                 return false;
219:             }
220:         }
221: 
222:         return true;
223:     }
```
['[229](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingVerifier.sol#L229-L246)']
```solidity
229:     function changeStakingLimits(
230:         uint256 _rewardsPerSecondLimit,
231:         uint256 _timeForEmissionsLimit,
232:         uint256 _numServicesLimit
233:     ) external {
234:         
235:         if (owner != msg.sender) {
236:             revert OwnerOnly(owner, msg.sender);
237:         }
238: 
239:         
240:         if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {
241:             revert ZeroValue();
242:         }
243: 
244:         rewardsPerSecondLimit = _rewardsPerSecondLimit; // <= FOUND
245:         timeForEmissionsLimit = _timeForEmissionsLimit;
246:         numServicesLimit = _numServicesLimit; // <= FOUND
247:         emissionsLimit = _rewardsPerSecondLimit * _timeForEmissionsLimit * _numServicesLimit;
248: 
249:         emit StakingLimitsUpdated(_rewardsPerSecondLimit, _timeForEmissionsLimit, _numServicesLimit, emissionsLimit);
250:     }
```


</details>

## [NonCritical-2] Constant array index within iteration

### Resolution 
Using constant array indexes within for loops is error prone as typically [i] is used instead as by using a constant index such as [0] means that only one value will be used/modified within an array, historically many bugs have been introduced through using a constant index rather than the array iteration index i.e i,j. Provided this is intentional and not a typo consider using enum values to make this distinction clear.

Num of instances: 3

### Findings 


<details><summary>Click to show findings</summary>

['[600](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L600-L611)']
```solidity
600:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) {
601:                 
602:                 (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =
603:                     calculateStakingIncentives(numClaimedEpochs, chainIds[i], stakingTargets[i][j], bridgingDecimals);
604: 
605:                 
606:                 mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;
607: 
608:                 stakingIncentives[i][j] = stakingIncentive;
609:                 transferAmounts[i] += stakingIncentive;
610:                 totalAmounts[0] += stakingIncentive; // <= FOUND
611:                 totalAmounts[2] += returnAmount; // <= FOUND
612:             }
```
['[903](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L903-L908)']
```solidity
903:        for (uint256 i = 0; i < numServices; ++i) {
904:             
905:             
906:             
907:             bool topUpEligible;
908:             if (incentiveFlags[2] || incentiveFlags[3]) { // <= FOUND
909:                 address serviceOwner = IToken(serviceRegistry).ownerOf(serviceIds[i]);
910:                 topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||
911:                     IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;
912:             }
913: 
914:             
915:             for (uint256 unitType = 0; unitType < 2; ++unitType) {
916:                 
917:                 (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).
918:                     getUnitIdsOfService(IServiceRegistry.UnitType(unitType), serviceIds[i]);
919:                 
920:                 
921:                 if (numServiceUnits == 0) {
922:                     revert ServiceNeverDeployed(serviceIds[i]);
923:                 }
924:                 
925:                 if (incentiveFlags[unitType] || incentiveFlags[unitType + 2]) {
926:                     
927:                     uint96 amount = uint96(amounts[i] / numServiceUnits);
928:                     
929:                     for (uint256 j = 0; j < numServiceUnits; ++j) {
930:                         
931:                         uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch;
932:                         
933:                         if (lastEpoch == 0) {
934:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
935:                         } else if (lastEpoch < curEpoch) {
936:                             
937:                             
938:                             
939:                             
940:                             _finalizeIncentivesForUnitId(lastEpoch, unitType, serviceUnitIds[j]);
941:                             
942:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
943:                         }
944:                         
945:                         if (incentiveFlags[unitType]) {
946:                             mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeReward += amount;
947:                         }
948:                         
949:                         
950:                         
951:                         if (topUpEligible && incentiveFlags[unitType + 2]) {
952:                             mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeTopUp += amount;
953:                             mapEpochTokenomics[curEpoch].unitPoints[unitType].sumUnitTopUpsOLAS += amount;
954:                         }
955:                     }
956:                 }
957: 
958:                 
959:                 for (uint256 j = 0; j < numServiceUnits; ++j) {
960:                     
961:                     if (!mapNewUnits[unitType][serviceUnitIds[j]]) {
962:                         mapNewUnits[unitType][serviceUnitIds[j]] = true;
963:                         mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;
964:                         
965:                         
966:                         address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);
967:                         if (!mapNewOwners[unitOwner]) {
968:                             mapNewOwners[unitOwner] = true;
969:                             mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;
970:                         }
971:                     }
972:                 }
973:             }
974:         }
```
['[903](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L903-L908)']
```solidity
903:         for (uint256 i = 0; i < numServices; ++i) {
904:             
905:             
906:             
907:             bool topUpEligible;
908:             if (incentiveFlags[2] || incentiveFlags[3]) { // <= FOUND
909:                 address serviceOwner = IToken(serviceRegistry).ownerOf(serviceIds[i]);
910:                 topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||
911:                     IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;
912:             }
913: 
914:             
915:             for (uint256 unitType = 0; unitType < 2; ++unitType) {
916:                 
917:                 (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).
918:                     getUnitIdsOfService(IServiceRegistry.UnitType(unitType), serviceIds[i]);
919:                 
920:                 
921:                 if (numServiceUnits == 0) {
922:                     revert ServiceNeverDeployed(serviceIds[i]);
923:                 }
924:                 
925:                 if (incentiveFlags[unitType] || incentiveFlags[unitType + 2]) {
926:                     
927:                     uint96 amount = uint96(amounts[i] / numServiceUnits);
928:                     
929:                     for (uint256 j = 0; j < numServiceUnits; ++j) {
930:                         
931:                         uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch;
932:                         
933:                         if (lastEpoch == 0) {
934:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
935:                         } else if (lastEpoch < curEpoch) {
936:                             
937:                             
938:                             
939:                             
940:                             _finalizeIncentivesForUnitId(lastEpoch, unitType, serviceUnitIds[j]);
941:                             
942:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);
943:                         }
944:                         
945:                         if (incentiveFlags[unitType]) {
946:                             mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeReward += amount;
947:                         }
948:                         
949:                         
950:                         
951:                         if (topUpEligible && incentiveFlags[unitType + 2]) {
952:                             mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeTopUp += amount;
953:                             mapEpochTokenomics[curEpoch].unitPoints[unitType].sumUnitTopUpsOLAS += amount;
954:                         }
955:                     }
956:                 }
957: 
958:                 
959:                 for (uint256 j = 0; j < numServiceUnits; ++j) {
960:                     
961:                     if (!mapNewUnits[unitType][serviceUnitIds[j]]) {
962:                         mapNewUnits[unitType][serviceUnitIds[j]] = true;
963:                         mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;
964:                         
965:                         
966:                         address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);
967:                         if (!mapNewOwners[unitOwner]) {
968:                             mapNewOwners[unitOwner] = true;
969:                             mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;
970:                         }
971:                     }
972:                 }
973:             }
974:         }
```


</details>

## [NonCritical-3] safeApprove()/approve() may revert if the current approval is not zero

### Resolution 
The `approve()` and `safeApprove()` functions in ERC20 token contracts are designed to set an allowance for a spender. However, there's a known issue where calling `approve()` to set a new allowance before the current one is zero can be a potential race condition. Attackers might spend the old allowance and the new one before the owner has a chance to change it. To mitigate this, many token contracts require the current allowance to be zero before setting a new one. As a resolution, always set the allowance to zero with an additional transaction before assigning a new value, or utilize patterns like `increaseAllowance()` and `decreaseAllowance()`.

Num of instances: 7

### Findings 


<details><summary>Click to show findings</summary>

['[139](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L139-L191)']
```solidity
139:     function _processData(bytes memory data) internal {
140:         
141:         if (_locked > 1) {
142:             revert ReentrancyGuard();
143:         }
144:         _locked = 2;
145: 
146:         
147:         (address[] memory targets, uint256[] memory amounts) = abi.decode(data, (address[], uint256[]));
148: 
149:         uint256 batchNonce = stakingBatchNonce;
150:         uint256 localWithheldAmount = 0;
151:         uint256 localPaused = paused;
152: 
153:         
154:         for (uint256 i = 0; i < targets.length; ++i) {
155:             address target = targets[i];
156:             uint256 amount = amounts[i];
157: 
158:             
159:             
160:             bytes memory verifyData = abi.encodeCall(IStakingFactory.verifyInstanceAndGetEmissionsAmount, target);
161:             (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162: 
163:             uint256 limitAmount;
164:             
165:             if (success && returnData.length == 32) {
166:                 limitAmount = abi.decode(returnData, (uint256));
167:             }
168: 
169:             
170:             if (limitAmount == 0) {
171:                 
172:                 localWithheldAmount += amount;
173:                 emit AmountWithheld(target, amount);
174: 
175:                 
176:                 continue;
177:             }
178: 
179:             
180:             if (amount > limitAmount) {
181:                 uint256 targetWithheldAmount = amount - limitAmount;
182:                 localWithheldAmount += targetWithheldAmount;
183:                 amount = limitAmount;
184: 
185:                 emit AmountWithheld(target, targetWithheldAmount);
186:             }
187: 
188:             
189:             if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {
190:                 
191:                 IToken(olas).approve(target, amount); // <= FOUND
192:                 IStaking(target).deposit(amount);
193: 
194:                 emit StakingTargetDeposited(target, amount);
195:             } else {
196:                 
197:                 bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
198:                 
199:                 stakingQueueingNonces[queueHash] = true;
200: 
201:                 emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);
202:             }
203:         }
204:         
205:         stakingBatchNonce = batchNonce + 1;
206: 
207:         
208:         if (localWithheldAmount > 0) {
209:             withheldAmount += localWithheldAmount;
210:         }
211: 
212:         _locked = 1;
213:     }
```
['[266](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L266-L290)']
```solidity
266:     function redeem(address target, uint256 amount, uint256 batchNonce) external {
267:         
268:         if (_locked > 1) {
269:             revert ReentrancyGuard();
270:         }
271:         _locked = 2;
272: 
273:         
274:         if (paused == 2) {
275:             revert Paused();
276:         }
277: 
278:         
279:         bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
280:         bool queued = stakingQueueingNonces[queueHash];
281:         
282:         if (!queued) {
283:             revert TargetAmountNotQueued(target, amount, batchNonce);
284:         }
285: 
286:         
287:         uint256 olasBalance = IToken(olas).balanceOf(address(this));
288:         if (olasBalance >= amount) {
289:             
290:             IToken(olas).approve(target, amount); // <= FOUND
291:             IStaking(target).deposit(amount);
292: 
293:             emit StakingTargetDeposited(target, amount);
294: 
295:             
296:             stakingQueueingNonces[queueHash] = false;
297:         } else {
298:             
299:             revert InsufficientBalance(olasBalance, amount);
300:         }
301: 
302:         _locked = 1;
303:     }
```
['[86](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L86-L118)']
```solidity
86:     function _deposit(address[] memory targets, uint256[] memory stakingIncentives) internal {
87:         
88:         if (_locked > 1) {
89:             revert ReentrancyGuard();
90:         }
91:         _locked = 2;
92: 
93:         
94:         for (uint256 i = 0; i < targets.length; ++i) {
95:             address target = targets[i];
96:             uint256 amount = stakingIncentives[i];
97: 
98:             
99:             uint256 limitAmount = IStakingFactory(stakingFactory).verifyInstanceAndGetEmissionsAmount(target);
100: 
101:             
102:             if (limitAmount == 0) {
103:                 revert TargetEmissionsZero(target);
104:             }
105: 
106:             
107:             if (amount > limitAmount) {
108:                 uint256 refundAmount = amount - limitAmount;
109:                 amount = limitAmount;
110: 
111:                 
112:                 IToken(olas).transfer(timelock, refundAmount);
113: 
114:                 emit AmountRefunded(target, refundAmount);
115:             }
116: 
117:             
118:             IToken(olas).approve(target, amount); // <= FOUND
119:             IStaking(target).deposit(amount);
120: 
121:             emit StakingTargetDeposited(target, amount);
122:         }
123: 
124:         _locked = 1;
125:     }
```
['[119](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L119-L174)']
```solidity
119:     function _sendMessage(
120:         address[] memory targets,
121:         uint256[] memory stakingIncentives,
122:         bytes memory bridgePayload,
123:         uint256 transferAmount
124:     ) internal override returns (uint256 sequence) {
125:         
126:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {
127:             revert IncorrectDataLength(BRIDGE_PAYLOAD_LENGTH, bridgePayload.length);
128:         }
129: 
130:         
131:         (address refundAccount, uint256 gasPriceBid, uint256 maxSubmissionCostToken, uint256 gasLimitMessage,
132:             uint256 maxSubmissionCostMessage) = abi.decode(bridgePayload, (address, uint256, uint256, uint256, uint256));
133: 
134:         
135:         if (refundAccount == address(0)) {
136:             refundAccount = msg.sender;
137:         }
138: 
139:         
140:         
141:         if (gasPriceBid < 2 || gasLimitMessage < 2 || maxSubmissionCostMessage == 0) {
142:             revert ZeroValue();
143:         }
144: 
145:         
146:         if (gasLimitMessage > MESSAGE_GAS_LIMIT) {
147:             revert Overflow(gasLimitMessage, MESSAGE_GAS_LIMIT);
148:         }
149: 
150:         
151:         
152:         uint256[] memory cost = new uint256[](2);
153:         if (transferAmount > 0) {
154:             if (maxSubmissionCostToken == 0) {
155:                 revert ZeroValue();
156:             }
157: 
158:             
159:             cost[0] = maxSubmissionCostToken + TOKEN_GAS_LIMIT * gasPriceBid;
160:         }
161: 
162:         
163:         cost[1] = maxSubmissionCostMessage + gasLimitMessage * gasPriceBid;
164:         
165:         uint256 totalCost = cost[0] + cost[1];
166: 
167:         
168:         if (totalCost > msg.value) {
169:             revert LowerThan(msg.value, totalCost);
170:         }
171: 
172:         if (transferAmount > 0) {
173:             
174:             IToken(olas).approve(l1ERC20Gateway, transferAmount); // <= FOUND
175: 
176:             
177:             
178:             
179:             bytes memory submissionCostData = abi.encode(maxSubmissionCostToken, "");
180: 
181:             
182:             IBridge(l1TokenRelayer).outboundTransferCustomRefund{value: cost[0]}(olas, refundAccount,
183:                 l2TargetDispenser, transferAmount, TOKEN_GAS_LIMIT, gasPriceBid, submissionCostData);
184:         }
185: 
186:         
187:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));
188: 
189:         
190:         sequence = IBridge(l1MessageRelayer).createRetryableTicket{value: cost[1]}(l2TargetDispenser, 0,
191:             maxSubmissionCostMessage, refundAccount, refundAccount, gasLimitMessage, gasPriceBid, data);
192:     }
```
['[51](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L51-L65)']
```solidity
51:     function _sendMessage(
52:         address[] memory targets,
53:         uint256[] memory stakingIncentives,
54:         bytes memory bridgePayload,
55:         uint256 transferAmount
56:     ) internal override returns (uint256 sequence) {
57:         
58:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {
59:             revert IncorrectDataLength(BRIDGE_PAYLOAD_LENGTH, bridgePayload.length);
60:         }
61: 
62:         
63:         if (transferAmount > 0) {
64:             
65:             IToken(olas).approve(l1TokenRelayer, transferAmount); // <= FOUND
66: 
67:             bytes memory data = abi.encode(targets, stakingIncentives);
68:             IBridge(l1TokenRelayer).relayTokensAndCall(olas, l2TargetDispenser, transferAmount, data);
69: 
70:             sequence = stakingBatchNonce;
71:         } else {
72:             
73:             bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));
74: 
75:             
76:             
77:             uint256 gasLimitMessage = abi.decode(bridgePayload, (uint256));
78: 
79:             
80:             if (gasLimitMessage == 0) {
81:                 revert ZeroValue();
82:             }
83: 
84:             
85:             if (gasLimitMessage > MESSAGE_GAS_LIMIT) {
86:                 revert Overflow(gasLimitMessage, MESSAGE_GAS_LIMIT);
87:             }
88: 
89:             
90:             bytes32 iMsg = IBridge(l1MessageRelayer).requireToPassMessage(l2TargetDispenser, data, gasLimitMessage);
91: 
92:             sequence = uint256(iMsg);
93:         }
94:     }
```
['[95](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L95-L111)']
```solidity
95:     function _sendMessage(
96:         address[] memory targets,
97:         uint256[] memory stakingIncentives,
98:         bytes memory bridgePayload,
99:         uint256 transferAmount
100:     ) internal override returns (uint256 sequence) {
101:         
102:         if (bridgePayload.length != BRIDGE_PAYLOAD_LENGTH) {
103:             revert IncorrectDataLength(BRIDGE_PAYLOAD_LENGTH, bridgePayload.length);
104:         }
105: 
106:         
107:         if (transferAmount > 0) {
108:             
109:             
110:             
111:             IToken(olas).approve(l1TokenRelayer, transferAmount); // <= FOUND
112: 
113:             
114:             IBridge(l1TokenRelayer).depositERC20To(olas, olasL2, l2TargetDispenser, transferAmount,
115:                 uint32(TOKEN_GAS_LIMIT), "");
116:         }
117: 
118:         
119:         (uint256 cost, uint256 gasLimitMessage) = abi.decode(bridgePayload, (uint256, uint256));
120:         
121:         if (cost == 0 || gasLimitMessage == 0) {
122:             revert ZeroValue();
123:         }
124: 
125:         
126:         if (gasLimitMessage > MESSAGE_GAS_LIMIT) {
127:             revert Overflow(gasLimitMessage, MESSAGE_GAS_LIMIT);
128:         }
129: 
130:         
131:         if (cost > msg.value) {
132:             revert LowerThan(msg.value, cost);
133:         }
134: 
135:         
136:         bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));
137: 
138:         
139:         
140:         IBridge(l1MessageRelayer).sendMessage{value: cost}(l2TargetDispenser, data, uint32(gasLimitMessage));
141: 
142:         
143:         sequence = stakingBatchNonce;
144:     }
```
['[57](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L57-L68)']
```solidity
57:     function _sendMessage(
58:         address[] memory targets,
59:         uint256[] memory stakingIncentives,
60:         bytes memory,
61:         uint256 transferAmount
62:     ) internal override returns (uint256 sequence) {
63:         
64:         if (transferAmount > 0) {
65:             
66:             
67:             
68:             IToken(olas).approve(predicate, transferAmount); // <= FOUND
69: 
70:             
71:             IBridge(l1TokenRelayer).depositFor(l2TargetDispenser, olas, abi.encode(transferAmount));
72:         }
73: 
74:         
75:         bytes memory data = abi.encode(targets, stakingIncentives);
76: 
77:         
78:         
79:         
80:         _sendMessageToChild(data);
81: 
82:         
83:         sequence = stakingBatchNonce;
84:     }
```


</details>

## [NonCritical-4] Addition/multiplication in unchecked block is unsafe

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[47](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L47-L48)']
```solidity
47:         unchecked { // <= FOUND
48:             l1AliasedDepositProcessor = address(uint160(_l1DepositProcessor) + offset); // <= FOUND
49:         }
```


</details>

## [NonCritical-5] Consider the case where `totalSupply` is zero

### Resolution 
In smart contracts, especially those dealing with tokens or financial calculations, functions that perform operations based on `totalSupply` must account for the scenario where `totalSupply` is zero. Neglecting this edge case can lead to division by zero errors, causing transactions to revert and potentially disrupting contract functionality. This is critical in functions calculating percentages, rewards, or distributions. Proper checks should be implemented to ensure `totalSupply` is greater than zero before performing division or similar operations. Handling this case ensures the contract remains robust, preventing logical errors and maintaining the integrity of its operations.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[1307](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1307-L1329)']
```solidity
1307:     function accountOwnerIncentives(
1308:         address account,
1309:         uint256[] memory unitTypes,
1310:         uint256[] memory unitIds
1311:     ) external returns (uint256 reward, uint256 topUp) {
1312:         
1313:         if (dispenser != msg.sender) {
1314:             revert ManagerOnly(msg.sender, dispenser);
1315:         }
1316: 
1317:         
1318:         if (unitTypes.length != unitIds.length) {
1319:             revert WrongArrayLength(unitTypes.length, unitIds.length);
1320:         }
1321: 
1322:         
1323:         address[] memory registries = new address[](2);
1324:         (registries[0], registries[1]) = (componentRegistry, agentRegistry);
1325: 
1326:         
1327:         uint256[] memory registriesSupply = new uint256[](2);
1328:         for (uint256 i = 0; i < 2; ++i) {
1329:             registriesSupply[i] = IToken(registries[i]).totalSupply(); // <= FOUND
1330:         }
1331: 
1332:         
1333:         uint256[] memory lastIds = new uint256[](2);
1334:         for (uint256 i = 0; i < unitIds.length; ++i) {
1335:             
1336:             if (unitTypes[i] > 1) {
1337:                 revert Overflow(unitTypes[i], 1);
1338:             }
1339: 
1340:             
1341:             if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
1342:                 revert WrongUnitId(unitIds[i], unitTypes[i]);
1343:             }
1344:             lastIds[unitTypes[i]] = unitIds[i];
1345: 
1346:             
1347:             address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
1348:             if (unitOwner != account) {
1349:                 revert OwnerOnly(unitOwner, account);
1350:             }
1351:         }
1352: 
1353:         
1354:         uint256 curEpoch = epochCounter;
1355: 
1356:         for (uint256 i = 0; i < unitIds.length; ++i) {
1357:             
1358:             uint256 lastEpoch = mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch;
1359:             
1360:             
1361:             
1362:             if (lastEpoch > 0 && lastEpoch < curEpoch) {
1363:                 _finalizeIncentivesForUnitId(lastEpoch, unitTypes[i], unitIds[i]);
1364:                 
1365:                 mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch = 0;
1366:             }
1367: 
1368:             
1369:             reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;
1370:             mapUnitIncentives[unitTypes[i]][unitIds[i]].reward = 0;
1371:             
1372:             topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;
1373:             mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp = 0;
1374:         }
1375:     }
```
['[1384](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1384-L1401)']
```solidity
1384:     function getOwnerIncentives(
1385:         address account,
1386:         uint256[] memory unitTypes,
1387:         uint256[] memory unitIds
1388:     ) external view returns (uint256 reward, uint256 topUp) {
1389:         
1390:         if (unitTypes.length != unitIds.length) {
1391:             revert WrongArrayLength(unitTypes.length, unitIds.length);
1392:         }
1393: 
1394:         
1395:         address[] memory registries = new address[](2);
1396:         (registries[0], registries[1]) = (componentRegistry, agentRegistry);
1397: 
1398:         
1399:         uint256[] memory registriesSupply = new uint256[](2);
1400:         for (uint256 i = 0; i < 2; ++i) {
1401:             registriesSupply[i] = IToken(registries[i]).totalSupply(); // <= FOUND
1402:         }
1403: 
1404:         
1405:         uint256[] memory lastIds = new uint256[](2);
1406:         for (uint256 i = 0; i < unitIds.length; ++i) {
1407:             
1408:             if (unitTypes[i] > 1) {
1409:                 revert Overflow(unitTypes[i], 1);
1410:             }
1411: 
1412:             
1413:             if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
1414:                 revert WrongUnitId(unitIds[i], unitTypes[i]);
1415:             }
1416:             lastIds[unitTypes[i]] = unitIds[i];
1417: 
1418:             
1419:             address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
1420:             if (unitOwner != account) {
1421:                 revert OwnerOnly(unitOwner, account);
1422:             }
1423:         }
1424: 
1425:         
1426:         uint256 curEpoch = epochCounter;
1427: 
1428:         for (uint256 i = 0; i < unitIds.length; ++i) {
1429:             
1430:             uint256 lastEpoch = mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch;
1431:             
1432:             if (lastEpoch > 0 && lastEpoch < curEpoch) {
1433:                 
1434:                 
1435:                 uint256 totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeReward;
1436:                 if (totalIncentives > 0) {
1437:                     totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].rewardUnitFraction;
1438:                     
1439:                     reward += totalIncentives / 100;
1440:                 }
1441:                 
1442:                 totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeTopUp;
1443:                 if (totalIncentives > 0) {
1444:                     
1445:                     
1446:                     totalIncentives *= mapEpochTokenomics[lastEpoch].epochPoint.totalTopUpsOLAS;
1447:                     totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].topUpUnitFraction;
1448:                     uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100;
1449:                     
1450:                     topUp += totalIncentives / sumUnitIncentives;
1451:                 }
1452:             }
1453: 
1454:             
1455:             reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;
1456:             
1457:             topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;
1458:         }
1459:     }
```


</details>

## [NonCritical-6] Floating pragma should be avoided

Num of instances: 3

### Findings 


<details><summary>Click to show findings</summary>

['[2](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L2-L2)']
```solidity
2: pragma solidity ^0.8.25; // <= FOUND
```
['[2](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/utils/SafeTransferLib.sol#L2-L2)']
```solidity
2: pragma solidity ^0.8.23; // <= FOUND
```
['[2](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L2-L2)']
```solidity
2: pragma solidity ^0.8.18; // <= FOUND
```


</details>

## [NonCritical-7] Functions with array parameters should have length checks in place

### Resolution 
Functions in Solidity that accept array parameters should incorporate length checks as a security measure. This is to prevent potential overflow errors, unwanted gas consumption, and manipulation attempts. Without length checks, an attacker could pass excessively large arrays as input, causing excessive computation and potentially causing the function to exceed the block gas limit, leading to a denial-of-service. Additionally, unexpected array sizes could lead to logic errors within the function. As a resolution, always validate array length at the start of functions handling array inputs, ensuring it aligns with the expectations of the function logic. This makes the code more robust and predictable.

Num of instances: 8

### Findings 


<details><summary>Click to show findings</summary>

['[366](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingBase.sol#L366-L379)']
```solidity
366:     function _checkTokenStakingDeposit(
367:         uint256 serviceId,
368:         uint256 stakingDeposit,
369:         uint32[] memory // <= FOUND
370:     ) internal view virtual {
371:         uint256 minDeposit = minStakingDeposit;
372: 
373:         
374:         if (stakingDeposit < minDeposit) {
375:             revert LowerThan(stakingDeposit, minDeposit);
376:         }
377: 
378:         
379:         (uint256 numAgentIds, IService.AgentParams[] memory agentParams) = IService(serviceRegistry).getAgentParams(serviceId); // <= FOUND
380:         for (uint256 i = 0; i < numAgentIds; ++i) {
381:             if (agentParams[i].bond < minDeposit) {
382:                 revert LowerThan(agentParams[i].bond, minDeposit);
383:             }
384:         }
385:     }
```
['[50](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingActivityChecker.sol#L50-L52)']
```solidity
50:     function isRatioPass(
51:         uint256[] memory curNonces, // <= FOUND
52:         uint256[] memory lastNonces, // <= FOUND
53:         uint256 ts
54:     ) external view virtual returns (bool ratioPass) {
55:         
56:         
57:         if (ts > 0 && curNonces[0] > lastNonces[0]) {
58:             uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts;
59:             ratioPass = (ratio >= livenessRatio);
60:         }
61:     }
```
['[164](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L164-L166)']
```solidity
164:     function sendMessageBatch(
165:         address[] memory targets, // <= FOUND
166:         uint256[] memory stakingIncentives, // <= FOUND
167:         bytes memory bridgePayload,
168:         uint256 transferAmount
169:     ) external virtual payable {
170:         
171:         if (msg.sender != l1Dispenser) {
172:             revert ManagerOnly(l1Dispenser, msg.sender);
173:         }
174: 
175:         
176:         uint256 sequence = _sendMessage(targets, stakingIncentives, bridgePayload, transferAmount);
177: 
178:         
179:         stakingBatchNonce++;
180: 
181:         emit MessagePosted(sequence, targets, stakingIncentives, transferAmount);
182:     }
```
['[155](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L155-L157)']
```solidity
155:     function sendMessageBatch(
156:         address[] memory targets, // <= FOUND
157:         uint256[] memory stakingIncentives, // <= FOUND
158:         bytes memory,
159:         uint256
160:     ) external {
161:         
162:         if (msg.sender != dispenser) {
163:             revert ManagerOnly(dispenser, msg.sender);
164:         }
165: 
166:         
167:         _deposit(targets, stakingIncentives);
168:     }
```
['[57](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L57-L59)']
```solidity
57:     function _sendMessage(
58:         address[] memory targets, // <= FOUND
59:         uint256[] memory stakingIncentives, // <= FOUND
60:         bytes memory,
61:         uint256 transferAmount
62:     ) internal override returns (uint256 sequence) {
63:         
64:         if (transferAmount > 0) {
65:             
66:             
67:             
68:             IToken(olas).approve(predicate, transferAmount);
69: 
70:             
71:             IBridge(l1TokenRelayer).depositFor(l2TargetDispenser, olas, abi.encode(transferAmount));
72:         }
73: 
74:         
75:         bytes memory data = abi.encode(targets, stakingIncentives);
76: 
77:         
78:         
79:         
80:         _sendMessageToChild(data);
81: 
82:         
83:         sequence = stakingBatchNonce;
84:     }
```
['[106](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L106-L108)']
```solidity
106:     function receiveWormholeMessages(
107:         bytes memory data,
108:         bytes[] memory, // <= FOUND
109:         bytes32 sourceAddress,
110:         uint16 sourceChain,
111:         bytes32 deliveryHash
112:     ) external {
113:         
114:         if (sourceChain != wormholeTargetChainId) {
115:             revert WrongChainId(sourceChain, wormholeTargetChainId);
116:         }
117: 
118:         
119:         if (mapDeliveryHashes[deliveryHash]) {
120:             revert AlreadyDelivered(deliveryHash);
121:         }
122:         mapDeliveryHashes[deliveryHash] = true;
123: 
124:         
125:         address l2Dispenser = address(uint160(uint256(sourceAddress)));
126: 
127:         _receiveMessage(msg.sender, l2Dispenser, data);
128:     }
```
['[789](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L789-L791)']
```solidity
789:     function claimOwnerIncentives(
790:         uint256[] memory unitTypes, // <= FOUND
791:         uint256[] memory unitIds // <= FOUND
792:     ) external returns (uint256 reward, uint256 topUp) {
793:         
794:         if (_locked > 1) {
795:             revert ReentrancyGuard();
796:         }
797:         _locked = 2;
798: 
799:         
800:         Pause currentPause = paused;
801:         if (currentPause == Pause.DevIncentivesPaused || currentPause == Pause.AllPaused ||
802:             ITreasury(treasury).paused() == 2) {
803:             revert Paused();
804:         }
805: 
806:         
807:         (reward, topUp) = ITokenomics(tokenomics).accountOwnerIncentives(msg.sender, unitTypes, unitIds);
808: 
809:         bool success;
810:         
811:         if ((reward + topUp) > 0) {
812:             
813:             uint256 olasBalance;
814:             if (topUp > 0) {
815:                 olasBalance = IToken(olas).balanceOf(msg.sender);
816:             }
817: 
818:             success = ITreasury(treasury).withdrawToAccount(msg.sender, reward, topUp);
819: 
820:             
821:             if (topUp > 0){
822:                 olasBalance = IToken(olas).balanceOf(msg.sender) - olasBalance;
823:                 if (olasBalance != topUp) {
824:                     revert WrongAmount(olasBalance, topUp);
825:                 }
826:             }
827:         }
828: 
829:         
830:         if (!success) {
831:             revert ClaimIncentivesFailed(msg.sender, reward, topUp);
832:         }
833: 
834:         emit IncentivesClaimed(msg.sender, reward, topUp);
835: 
836:         _locked = 1;
837:     }
```
['[1058](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L1058-L1092)']
```solidity
1058:     function claimStakingIncentivesBatch(
1059:         uint256 numClaimedEpochs,
1060:         uint256[] memory chainIds, // <= FOUND
1061:         bytes32[][] memory stakingTargets, // <= FOUND
1062:         bytes[] memory bridgePayloads, // <= FOUND
1063:         uint256[] memory valueAmounts // <= FOUND
1064:     ) external payable {
1065:         
1066:         if (_locked > 1) {
1067:             revert ReentrancyGuard();
1068:         }
1069:         _locked = 2;
1070: 
1071:         _checkOrderAndValues(chainIds, stakingTargets, bridgePayloads, valueAmounts);
1072: 
1073:         
1074:         uint256 localMaxNumClaimingEpochs = maxNumClaimingEpochs;
1075:         if (numClaimedEpochs > localMaxNumClaimingEpochs) {
1076:             revert Overflow(numClaimedEpochs, localMaxNumClaimingEpochs);
1077:         }
1078: 
1079:         Pause currentPause = paused;
1080:         if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
1081:             ITreasury(treasury).paused() == 2) {
1082:             revert Paused();
1083:         }
1084: 
1085:         
1086:         
1087:         
1088:         
1089:         uint256[] memory totalAmounts; // <= FOUND
1090:         
1091:         uint256[][] memory stakingIncentives; // <= FOUND
1092:         uint256[] memory transferAmounts; // <= FOUND
1093: 
1094:         (totalAmounts, stakingIncentives, transferAmounts) = _calculateStakingIncentivesBatch(numClaimedEpochs, chainIds,
1095:             stakingTargets);
1096: 
1097:         
1098:         if (totalAmounts[2] > 0) {
1099:             ITokenomics(tokenomics).refundFromStaking(totalAmounts[2]);
1100:         }
1101: 
1102:         
1103:         if (totalAmounts[0] > 0) {
1104:             
1105:             if (totalAmounts[1] > 0) {
1106:                 uint256 olasBalance = IToken(olas).balanceOf(address(this));
1107: 
1108:                 
1109:                 ITreasury(treasury).withdrawToAccount(address(this), 0, totalAmounts[1]);
1110: 
1111:                 
1112:                 olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;
1113:                 if (olasBalance != totalAmounts[1]) {
1114:                     revert WrongAmount(olasBalance, totalAmounts[1]);
1115:                 }
1116:             }
1117: 
1118:             
1119:             _distributeStakingIncentivesBatch(chainIds, stakingTargets, stakingIncentives, bridgePayloads, transferAmounts,
1120:                 valueAmounts);
1121:         }
1122: 
1123:         emit StakingIncentivesClaimed(msg.sender, totalAmounts[0], totalAmounts[1], totalAmounts[2]);
1124: 
1125:         _locked = 1;
1126:     }
```


</details>

## [NonCritical-8] Events may be emitted out of order due to code not follow the best practice of check-effects-interaction

### Resolution 
The "check-effects-interaction" pattern also impacts event ordering. When a contract doesn't adhere to this pattern, events might be emitted in a sequence that doesn't reflect the actual logical flow of operations. This can cause confusion during event tracking, potentially leading to erroneous off-chain interpretations. To rectify this, always ensure that checks are performed first, state modifications come next, and interactions with external contracts or addresses are done last. This will ensure events are emitted in a logical, consistent manner, providing a clear and accurate chronological record of on-chain actions for off-chain systems and observers.

Num of instances: 4

### Findings 


<details><summary>Click to show findings</summary>

['[412](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L412-L452)']
```solidity
412:     function migrate(address newL2TargetDispenser) external { // <= FOUND
413:         
414:         if (_locked > 1) {
415:             revert ReentrancyGuard();
416:         }
417:         _locked = 2;
418: 
419:         
420:         if (msg.sender != owner) {
421:             revert OwnerOnly(msg.sender, owner);
422:         }
423: 
424:         
425:         if (paused == 1) {
426:             revert Unpaused();
427:         }
428: 
429:         
430:         if (newL2TargetDispenser.code.length == 0) { // <= FOUND
431:             revert WrongAccount(newL2TargetDispenser);
432:         }
433: 
434:         
435:         if (newL2TargetDispenser == address(this)) {
436:             revert WrongAccount(address(this));
437:         }
438: 
439:         
440:         uint256 amount = IToken(olas).balanceOf(address(this));
441:         
442:         if (amount > 0) {
443:             bool success = IToken(olas).transfer(newL2TargetDispenser, amount);
444:             if (!success) {
445:                 revert TransferFailed(olas, address(this), newL2TargetDispenser, amount);
446:             }
447:         }
448: 
449:         
450:         owner = address(0);
451: 
452:         emit Migrated(msg.sender, newL2TargetDispenser, amount); // <= FOUND
453: 
454:         
455:     }
```
['[292](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L292-L318)']
```solidity
292:     function _addNominee(Nominee memory nominee) internal { // <= FOUND
293:         
294:         bytes32 nomineeHash = keccak256(abi.encode(nominee));
295:         if (mapNomineeIds[nomineeHash] > 0) {
296:             revert NomineeAlreadyExists(nominee.account, nominee.chainId);
297:         }
298: 
299:         
300:         if (mapRemovedNominees[nomineeHash] > 0) {
301:             revert NomineeRemoved(nominee.account, nominee.chainId);
302:         }
303: 
304:         uint256 id = setNominees.length;
305:         mapNomineeIds[nomineeHash] = id;
306:         
307:         setNominees.push(nominee);
308: 
309:         uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;
310:         timeWeight[nomineeHash] = nextTime;
311: 
312:         
313:         address localDispenser = dispenser;
314:         if (localDispenser != address(0)) {
315:             IDispenser(localDispenser).addNominee(nomineeHash); // <= FOUND
316:         }
317: 
318:         emit AddNominee(nominee.account, nominee.chainId, id); // <= FOUND
319:     }
```
['[586](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L586-L635)']
```solidity
586:     function removeNominee(bytes32 account, uint256 chainId) external { // <= FOUND
587:         
588:         if (msg.sender != owner) {
589:             revert OwnerOnly(owner, msg.sender);
590:         }
591: 
592:         
593:         Nominee memory nominee = Nominee(account, chainId);
594:         bytes32 nomineeHash = keccak256(abi.encode(nominee));
595: 
596:         
597:         uint256 id = mapNomineeIds[nomineeHash];
598:         if (id == 0) {
599:             revert NomineeDoesNotExist(account, chainId);
600:         }
601: 
602:         
603:         uint256 oldWeight = _getWeight(account, chainId);
604:         uint256 oldSum = _getSum();
605:         uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;
606:         pointsWeight[nomineeHash][nextTime].bias = 0;
607:         timeWeight[nomineeHash] = nextTime;
608: 
609:         
610:         uint256 newSum = oldSum - oldWeight;
611:         pointsSum[nextTime].bias = newSum;
612:         timeSum = nextTime;
613: 
614:         
615:         mapRemovedNominees[nomineeHash] = setRemovedNominees.length;
616:         setRemovedNominees.push(nominee);
617: 
618:         
619:         mapNomineeIds[nomineeHash] = 0;
620: 
621:         
622:         nominee = setNominees[setNominees.length - 1];
623:         bytes32 replacedNomineeHash = keccak256(abi.encode(nominee));
624:         mapNomineeIds[replacedNomineeHash] = id;
625:         setNominees[id] = nominee;
626:         
627:         setNominees.pop();
628: 
629:         
630:         address localDispenser = dispenser;
631:         if (localDispenser != address(0)) {
632:             IDispenser(localDispenser).removeNominee(nomineeHash); // <= FOUND
633:         }
634: 
635:         emit RemoveNominee(account, chainId, newSum); // <= FOUND
636:     }
```
['[1199](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L1199-L1236)']
```solidity
1199:     function syncWithheldAmountMaintenance(uint256 chainId, uint256 amount) external { // <= FOUND
1200:         
1201:         if (msg.sender != owner) {
1202:             revert OwnerOnly(msg.sender, owner);
1203:         }
1204: 
1205:         
1206:         if (chainId == 0 || amount == 0) {
1207:             revert ZeroValue();
1208:         }
1209: 
1210:         
1211:         if (chainId == block.chainid) {
1212:             revert WrongChainId(chainId);
1213:         }
1214: 
1215:         
1216:         address depositProcessor = mapChainIdDepositProcessors[chainId];
1217:         uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals(); // <= FOUND
1218: 
1219:         
1220:         if (bridgingDecimals < 18) {
1221:             uint256 normalizedAmount = amount / (10 ** (18 - bridgingDecimals));
1222:             normalizedAmount *= 10 ** (18 - bridgingDecimals);
1223:             
1224:             amount = normalizedAmount;
1225:         }
1226: 
1227:         
1228:         uint256 withheldAmount = mapChainIdWithheldAmounts[chainId] + amount;
1229:         if (withheldAmount > type(uint96).max) {
1230:             revert Overflow(withheldAmount, type(uint96).max);
1231:         }
1232: 
1233:         
1234:         mapChainIdWithheldAmounts[chainId] = withheldAmount;
1235: 
1236:         emit WithheldAmountSynced(chainId, amount, withheldAmount); // <= FOUND
1237:     }
```


</details>

## [NonCritical-9] Missing events in sensitive functions

### Resolution 
Sensitive setter functions in smart contracts often alter critical state variables. Without events emitted in these functions, external observers or dApps cannot easily track or react to these state changes. Missing events can obscure contract activity, hampering transparency and making integration more challenging. To resolve this, incorporate appropriate event emissions within these functions. Events offer an efficient way to log crucial changes, aiding in real-time tracking and post-transaction verification.

Num of instances: 3

### Findings 


<details><summary>Click to show findings</summary>

['[206](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L206-L206)']
```solidity
206:     function setL2TargetDispenser(address l2Dispenser) external virtual { // <= FOUND
207:         _setL2TargetDispenser(l2Dispenser);
208:     }
```
['[117](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L117-L117)']
```solidity
117:     function setL2TargetDispenser(address l2Dispenser) external override { // <= FOUND
118:         setFxChildTunnel(l2Dispenser);
119:         _setL2TargetDispenser(l2Dispenser);
120:     }
```
['[132](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L132-L132)']
```solidity
132:     function setL2TargetDispenser(address l2Dispenser) external override { // <= FOUND
133:         setRegisteredSender(uint16(wormholeTargetChainId), bytes32(uint256(uint160(l2Dispenser))));
134:         _setL2TargetDispenser(l2Dispenser);
135:     }
```


</details>

## [NonCritical-10] Avoid mutating function parameters

### Resolution 
Function parameters in Solidity are passed by value, meaning they are essentially local copies. Mutating them can lead to confusion and errors because the changes don't persist outside the function. By keeping function parameters immutable, you ensure clarity in code behavior, preventing unintended side-effects. If you need to modify a value based on a parameter, use a local variable inside the function, leaving the original parameter unaltered. By adhering to this practice, you maintain a clear distinction between input data and the internal processing logic, improving code readability and reducing the potential for bugs.

Num of instances: 5

### Findings 


<details><summary>Click to show findings</summary>

['[638](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L638-L686)']
```solidity
638:     function changeTokenomicsParameters(
639:         uint256 _devsPerCapital,
640:         uint256 _codePerDev,
641:         uint256 _epsilonRate,
642:         uint256 _epochLen,
643:         uint256 _veOLASThreshold
644:     ) external {
645:         
646:         if (msg.sender != owner) {
647:             revert OwnerOnly(msg.sender, owner);
648:         }
649: 
650:         
651:         if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {
652:             devsPerCapital = uint72(_devsPerCapital);
653:         } else {
654:             
655:             _devsPerCapital = devsPerCapital; // <= FOUND
656:         }
657: 
658:         
659:         if (uint72(_codePerDev) > MIN_PARAM_VALUE) {
660:             codePerDev = uint72(_codePerDev);
661:         } else {
662:             
663:             _codePerDev = codePerDev; // <= FOUND
664:         }
665: 
666:         
667:         
668:         
669:         if (_epsilonRate > 0 && _epsilonRate <= 17e18) {
670:             epsilonRate = uint64(_epsilonRate);
671:         } else {
672:             _epsilonRate = epsilonRate; // <= FOUND
673:         }
674: 
675:         
676:         if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {
677:             nextEpochLen = uint32(_epochLen);
678:         } else {
679:             _epochLen = epochLen; // <= FOUND
680:         }
681: 
682:         
683:         if (uint96(_veOLASThreshold) > 0) {
684:             nextVeOLASThreshold = uint96(_veOLASThreshold);
685:         } else {
686:             _veOLASThreshold = veOLASThreshold; // <= FOUND
687:         }
688: 
689:         
690:         tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x01;
691:         emit TokenomicsParametersUpdateRequested(epochCounter + 1, _devsPerCapital, _codePerDev, _epsilonRate, _epochLen,
692:             _veOLASThreshold);
693:     }
```
['[1199](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L1199-L1224)']
```solidity
1199:     function syncWithheldAmountMaintenance(uint256 chainId, uint256 amount) external { // <= FOUND
1200:         
1201:         if (msg.sender != owner) {
1202:             revert OwnerOnly(msg.sender, owner);
1203:         }
1204: 
1205:         
1206:         if (chainId == 0 || amount == 0) {
1207:             revert ZeroValue();
1208:         }
1209: 
1210:         
1211:         if (chainId == block.chainid) {
1212:             revert WrongChainId(chainId);
1213:         }
1214: 
1215:         
1216:         address depositProcessor = mapChainIdDepositProcessors[chainId];
1217:         uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();
1218: 
1219:         
1220:         if (bridgingDecimals < 18) {
1221:             uint256 normalizedAmount = amount / (10 ** (18 - bridgingDecimals));
1222:             normalizedAmount *= 10 ** (18 - bridgingDecimals);
1223:             
1224:             amount = normalizedAmount; // <= FOUND
1225:         }
1226: 
1227:         
1228:         uint256 withheldAmount = mapChainIdWithheldAmounts[chainId] + amount;
1229:         if (withheldAmount > type(uint96).max) {
1230:             revert Overflow(withheldAmount, type(uint96).max);
1231:         }
1232: 
1233:         
1234:         mapChainIdWithheldAmounts[chainId] = withheldAmount;
1235: 
1236:         emit WithheldAmountSynced(chainId, amount, withheldAmount);
1237:     }
```
['[989](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L989-L1018)']
```solidity
989:     function trackServiceDonations(
990:         address donator,
991:         uint256[] memory serviceIds,
992:         uint256[] memory amounts,
993:         uint256 donationETH
994:     ) external {
995:         
996:         if (treasury != msg.sender) {
997:             revert ManagerOnly(msg.sender, treasury);
998:         }
999: 
1000:         
1001:         address bList = donatorBlacklist;
1002:         if (bList != address(0) && IDonatorBlacklist(bList).isDonatorBlacklisted(donator)) {
1003:             revert DonatorBlacklisted(donator);
1004:         }
1005: 
1006:         
1007:         uint256 numServices = serviceIds.length;
1008:         
1009:         for (uint256 i = 0; i < numServices; ++i) {
1010:             
1011:             if (!IServiceRegistry(serviceRegistry).exists(serviceIds[i])) {
1012:                 revert ServiceDoesNotExist(serviceIds[i]);
1013:             }
1014:         }
1015:         
1016:         uint256 curEpoch = epochCounter;
1017:         
1018:         donationETH += mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH; // <= FOUND
1019:         mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH = uint96(donationETH);
1020: 
1021:         
1022:         _trackServiceDonations(donator, serviceIds, amounts, curEpoch);
1023: 
1024:         
1025:         lastDonationBlockNumber = uint32(block.number);
1026:     }
```
['[33](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/TokenomicsConstants.sol#L33-L51)']
```solidity
33:     function getSupplyCapForYear(uint256 numYears) public pure returns (uint256 supplyCap) { // <= FOUND
34:         
35:         if (numYears < 10) {
36:             uint96[10] memory supplyCaps = [
37:                 529_659_000e18,
38:                 569_913_084e18,
39:                 610_313_084e18,
40:                 666_313_084e18,
41:                 746_313_084e18,
42:                 818_313_084e18,
43:                 882_313_084e18,
44:                 930_313_084e18,
45:                 970_313_084e18,
46:                 1_000_000_000e18
47:             ];
48:             supplyCap = supplyCaps[numYears];
49:         } else {
50:             
51:             numYears -= 9; // <= FOUND
52:             
53:             supplyCap = 1_000_000_000e18;
54:             
55:             uint256 maxMintCapFraction = 2;
56: 
57:             
58:             for (uint256 i = 0; i < numYears; ++i) {
59:                 supplyCap += (supplyCap * maxMintCapFraction) / 100;
60:             }
61:             
62:             return supplyCap;
63:         }
64:     }
```
['[69](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/TokenomicsConstants.sol#L69-L88)']
```solidity
69:     function getInflationForYear(uint256 numYears) public pure returns (uint256 inflationAmount) { // <= FOUND
70:         
71:         if (numYears < 10) {
72:             
73:             uint88[10] memory inflationAmounts = [
74:                 3_159_000e18,
75:                 40_254_084e18,
76:                 40_400_000e18,
77:                 56_000_000e18,
78:                 80_000_000e18,
79:                 72_000_000e18,
80:                 64_000_000e18,
81:                 48_000_000e18,
82:                 40_000_000e18,
83:                 29_686_916e18
84:             ];
85:             inflationAmount = inflationAmounts[numYears];
86:         } else {
87:             
88:             numYears -= 9; // <= FOUND
89:             
90:             uint256 supplyCap = 1_000_000_000e18;
91:             
92:             uint256 maxMintCapFraction = 2;
93: 
94:             
95:             for (uint256 i = 1; i < numYears; ++i) {
96:                 supplyCap += (supplyCap * maxMintCapFraction) / 100;
97:             }
98: 
99:             
100:             inflationAmount = (supplyCap * maxMintCapFraction) / 100;
101:         }
102:     }
```


</details>

## [NonCritical-11] A event should be emitted if a non immutable state variable is set in a constructor

Num of instances: 7

### Findings 


<details><summary>Click to show findings</summary>

['[205](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L205-L213)']
```solidity
205:     constructor(address _ve) {
206:         
207:         if (_ve == address(0)) {
208:             revert ZeroAddress();
209:         }
210: 
211:         
212:         owner = msg.sender; // <= FOUND
213:         ve = _ve; // <= FOUND
214:         timeSum = block.timestamp / WEEK * WEEK; // <= FOUND
215:         
216:         setNominees.push(Nominee(0, 0));
217:         
218:         setRemovedNominees.push(Nominee(0, 0));
219:     }
```
['[103](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingFactory.sol#L103-L105)']
```solidity
103:     constructor(address _verifier) {
104:         owner = msg.sender; // <= FOUND
105:         verifier = _verifier; // <= FOUND
106:     }
```
['[74](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingVerifier.sol#L74-L87)']
```solidity
74:     constructor(address _olas, uint256 _rewardsPerSecondLimit, uint256 _timeForEmissionsLimit,
75:         uint256 _numServicesLimit) {
76:         
77:         if (_olas == address(0)) {
78:             revert ZeroAddress();
79:         }
80: 
81:         
82:         if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {
83:             revert ZeroValue();
84:         }
85: 
86:         owner = msg.sender; // <= FOUND
87:         olas = _olas; // <= FOUND
88:         rewardsPerSecondLimit = _rewardsPerSecondLimit; // <= FOUND
89:         timeForEmissionsLimit = _timeForEmissionsLimit; // <= FOUND
90:         numServicesLimit = _numServicesLimit; // <= FOUND
91:         emissionsLimit = _rewardsPerSecondLimit * _timeForEmissionsLimit * _numServicesLimit; // <= FOUND
92:     }
```
['[59](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L59-L81)']
```solidity
59:     constructor(
60:         address _olas,
61:         address _l1Dispenser,
62:         address _l1TokenRelayer,
63:         address _l1MessageRelayer,
64:         uint256 _l2TargetChainId
65:     ) {
66:         
67:         if (_l1Dispenser == address(0) || _l1TokenRelayer == address(0) || _l1MessageRelayer == address(0)) {
68:             revert ZeroAddress();
69:         }
70: 
71:         
72:         if (_l2TargetChainId == 0) {
73:             revert ZeroValue();
74:         }
75: 
76:         
77:         if (_l2TargetChainId > MAX_CHAIN_ID) {
78:             revert Overflow(_l2TargetChainId, MAX_CHAIN_ID);
79:         }
80: 
81:         olas = _olas; // <= FOUND
82:         l1Dispenser = _l1Dispenser;
83:         l1TokenRelayer = _l1TokenRelayer;
84:         l1MessageRelayer = _l1MessageRelayer;
85:         l2TargetChainId = _l2TargetChainId;
86:         owner = msg.sender; // <= FOUND
87:     }
```
['[101](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L101-L133)']
```solidity
101:     constructor(
102:         address _olas,
103:         address _stakingFactory,
104:         address _l2MessageRelayer,
105:         address _l1DepositProcessor,
106:         uint256 _l1SourceChainId
107:     ) {
108:         
109:         if (_olas == address(0) || _stakingFactory == address(0) || _l2MessageRelayer == address(0)
110:             || _l1DepositProcessor == address(0)) {
111:             revert ZeroAddress();
112:         }
113: 
114:         
115:         if (_l1SourceChainId == 0) {
116:             revert ZeroValue();
117:         }
118: 
119:         
120:         if (_l1SourceChainId > MAX_CHAIN_ID) {
121:             revert Overflow(_l1SourceChainId, MAX_CHAIN_ID);
122:         }
123: 
124:         
125:         olas = _olas; // <= FOUND
126:         stakingFactory = _stakingFactory;
127:         l2MessageRelayer = _l2MessageRelayer;
128:         l1DepositProcessor = _l1DepositProcessor;
129:         l1SourceChainId = _l1SourceChainId;
130: 
131:         
132:         owner = msg.sender; // <= FOUND
133:         paused = 1; // <= FOUND
134:         _locked = 1; // <= FOUND
135:     }
```
['[315](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L315-L327)']
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
324:         owner = msg.sender; // <= FOUND
325:         _locked = 1; // <= FOUND
326:         
327:         paused = Pause.StakingIncentivesPaused; // <= FOUND
328: 
329:         
330:         if (_olas == address(0) || _tokenomics == address(0) || _treasury == address(0) ||
331:             _voteWeighting == address(0) || _retainer == 0) {
332:             revert ZeroAddress();
333:         }
334: 
335:         
336:         if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {
337:             revert ZeroValue();
338:         }
339: 
340:         olas = _olas; // <= FOUND
341:         tokenomics = _tokenomics; // <= FOUND
342:         treasury = _treasury; // <= FOUND
343:         voteWeighting = _voteWeighting; // <= FOUND
344: 
345:         retainer = _retainer;
346:         retainerHash = keccak256(abi.encode(IVoteWeighting.Nominee(retainer, block.chainid)));
347:         maxNumClaimingEpochs = _maxNumClaimingEpochs; // <= FOUND
348:         maxNumStakingTargets = _maxNumStakingTargets; // <= FOUND
349:     }
```
['[70](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L70-L76)']
```solidity
70:     constructor(address _olas, address _dispenser, address _stakingFactory, address _timelock) {
71:         
72:         if (_olas == address(0) || _dispenser == address(0) || _stakingFactory == address(0) || _timelock == address(0)) {
73:             revert ZeroAddress();
74:         }
75: 
76:         olas = _olas; // <= FOUND
77:         dispenser = _dispenser; // <= FOUND
78:         stakingFactory = _stakingFactory;
79:         timelock = _timelock;
80:         _locked = 1; // <= FOUND
81:     }
```


</details>

## [NonCritical-12] Immutable and constant integer state variables should not be casted

### Resolution 
The definition of a constant or immutable variable is that they do not change, casting such variables can potentially push more than one 'value' to a constant, for example a uin128 constant can have its original value and that of when it's casted to uint64 (i.e it has some bytes truncated). This can create confusion and inconsistencies within the code which can inadvertently increase the attack surface of the project. It is thus advise to either change the uint byte size in the constant/immutable definition of the variable or introduce a second state variable to cover the differing casts that are expected such as having a uint128 constant and a separate uint64 constant.

Num of instances: 5

### Findings 


<details><summary>Click to show findings</summary>

['[114](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L114-L116)']
```solidity
114:             
115:             IBridge(l1TokenRelayer).depositERC20To(olas, olasL2, l2TargetDispenser, transferAmount,
116:                 uint32(TOKEN_GAS_LIMIT), ""); // <= FOUND
```
['[96](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L96-L102)']
```solidity
96:         
97:         
98:         
99:         
100:         
101:         sequence = sendTokenWithPayloadToEvm(uint16(wormholeTargetChainId), l2TargetDispenser, data, 0, // <= FOUND
102:             gasLimitMessage, olas, transferAmount, uint16(l2TargetChainId), refundAccount); // <= FOUND
```
['[133](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L133-L133)']
```solidity
133:         setRegisteredSender(uint16(wormholeTargetChainId), bytes32(uint256(uint160(l2Dispenser)))); // <= FOUND
```
['[112](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L112-L113)']
```solidity
112:         
113:         (uint256 cost, ) = IBridge(l2MessageRelayer).quoteEVMDeliveryPrice(uint16(l1SourceChainId), 0, gasLimitMessage); // <= FOUND
```
['[120](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L120-L122)']
```solidity
120:         
121:         uint64 sequence = IBridge(l2MessageRelayer).sendPayloadToEvm{value: cost}(uint16(l1SourceChainId), // <= FOUND
122:             l1DepositProcessor, abi.encode(amount), 0, gasLimitMessage, uint16(l1SourceChainId), refundAccount); // <= FOUND
```


</details>

## [NonCritical-13] Use transfer libraries instead of low level calls

### Resolution 
Using transfer libraries like OpenZeppelin's Address.sendValue is preferred over low-level calls for transferring Ether in Solidity. These libraries provide clearer, more semantically meaningful methods compared to low-level call() functions. They encapsulate best practices for error handling and gas management, enhancing the security and readability of your code. Low-level calls lack these built-in safety checks and can be more error-prone, especially when dealing with Ether transfers.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[33](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingNativeToken.sol#L33-L34)']
```solidity
33:         
34:         (bool success, ) = to.call{value: amount}(""); // <= FOUND
```
['[396](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L396-L397)']
```solidity
396:         
397:         (bool success, ) = msg.sender.call{value: amount}(""); // <= FOUND
```


</details>

## [NonCritical-14] int/uint passed into abi.encodePacked without casting to a string.

### Resolution 
Not casting an integer as a string before passing it into abi.encode can result in unintended behaviour. lets say '1' being encoded as '1' it will be encoded as char(1) which is the 'start of heading' control character or id of 60 would be encoded as '<'. This is may not be intended. To rectify this, simply cast the Id as a string with string(id) or ideally use solmate's libString library (toString)

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[141](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingFactory.sol#L141-L143)']
```solidity
141:     function getProxyAddressWithNonce(address implementation, uint256 localNonce) public view returns (address) { // <= FOUND
142:         
143:         bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce)); // <= FOUND
144: 
145:         
146:         bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,
147:             uint256(uint160(implementation)));
148: 
149:         
150:         bytes32 hash = keccak256(
151:             abi.encodePacked(
152:                 bytes1(0xff), address(this), salt, keccak256(deploymentData)
153:             )
154:         );
155: 
156:         return address(uint160(uint256(hash)));
157:     }
```


</details>

## [NonCritical-15] For loop iterates on arrays not indexed

### Resolution 
Ensuring matching input array lengths in functions is crucial to prevent logical errors and inconsistencies in smart contracts. Mismatched array lengths can lead to incomplete operations or incorrect data handling, particularly if one array's length is assumed to reflect another's. For instance, iterating over a shorter array than intended could result in unprocessed elements from a longer array, potentially causing unintended consequences in contract execution. Validating that all input arrays have intended lengths before performing operations helps safeguard against such errors, ensuring that all elements are appropriately processed and the contract behaves as expected.

Num of instances: 16

### Findings 


<details><summary>Click to show findings</summary>

['[669](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingBase.sol#L669-L687)']
```solidity
669:            for (uint256 i = 0; i < serviceIds.length; ++i) { // <= FOUND
670:                 
671:                 curServiceId = serviceIds[i];
672:                 
673:                 mapServiceInfo[curServiceId].nonces = serviceNonces[i]; // <= FOUND
674: 
675:                 
676:                 if (serviceInactivity[i] > 0) { // <= FOUND
677:                     
678:                     serviceInactivity[i] = mapServiceInfo[curServiceId].inactivity + serviceInactivity[i]; // <= FOUND
679:                     mapServiceInfo[curServiceId].inactivity = serviceInactivity[i]; // <= FOUND
680:                     
681:                     if (serviceInactivity[i] > maxInactivityDuration) { // <= FOUND
682:                         
683:                         evictServiceIds[i] = curServiceId; // <= FOUND
684:                         
685:                         numServices++;
686:                     } else {
687:                         emit ServiceInactivityWarning(eCounter, curServiceId, serviceInactivity[i]); // <= FOUND
688:                     }
689:                 } else {
690:                     
691:                     mapServiceInfo[curServiceId].inactivity = 0;
692:                 }
693:             }
```
['[669](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingBase.sol#L669-L687)']
```solidity
669:             for (uint256 i = 0; i < serviceIds.length; ++i) { // <= FOUND
670:                 
671:                 curServiceId = serviceIds[i];
672:                 
673:                 mapServiceInfo[curServiceId].nonces = serviceNonces[i]; // <= FOUND
674: 
675:                 
676:                 if (serviceInactivity[i] > 0) { // <= FOUND
677:                     
678:                     serviceInactivity[i] = mapServiceInfo[curServiceId].inactivity + serviceInactivity[i]; // <= FOUND
679:                     mapServiceInfo[curServiceId].inactivity = serviceInactivity[i]; // <= FOUND
680:                     
681:                     if (serviceInactivity[i] > maxInactivityDuration) { // <= FOUND
682:                         
683:                         evictServiceIds[i] = curServiceId; // <= FOUND
684:                         
685:                         numServices++;
686:                     } else {
687:                         emit ServiceInactivityWarning(eCounter, curServiceId, serviceInactivity[i]); // <= FOUND
688:                     }
689:                 } else {
690:                     
691:                     mapServiceInfo[curServiceId].inactivity = 0;
692:                 }
693:             }
```
['[1334](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1334-L1337)']
```solidity
1334:         for (uint256 i = 0; i < unitIds.length; ++i) { // <= FOUND
1335:             
1336:             if (unitTypes[i] > 1) { // <= FOUND
1337:                 revert Overflow(unitTypes[i], 1); // <= FOUND
1338:             }
1339: 
1340:             
1341:             if (unitIds[i] <= lastIds[unitTypes[i]] || unitIds[i] > registriesSupply[unitTypes[i]]) {
1342:                 revert WrongUnitId(unitIds[i], unitTypes[i]);
1343:             }
1344:             lastIds[unitTypes[i]] = unitIds[i];
1345: 
1346:             
1347:             address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);
1348:             if (unitOwner != account) {
1349:                 revert OwnerOnly(unitOwner, account);
1350:             }
1351:         }
```
['[1428](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1428-L1448)']
```solidity
1428:         for (uint256 i = 0; i < unitIds.length; ++i) { // <= FOUND
1429:             
1430:             uint256 lastEpoch = mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch;
1431:             
1432:             if (lastEpoch > 0 && lastEpoch < curEpoch) {
1433:                 
1434:                 
1435:                 uint256 totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeReward;
1436:                 if (totalIncentives > 0) {
1437:                     totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].rewardUnitFraction; // <= FOUND
1438:                     
1439:                     reward += totalIncentives / 100;
1440:                 }
1441:                 
1442:                 totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeTopUp;
1443:                 if (totalIncentives > 0) {
1444:                     
1445:                     
1446:                     totalIncentives *= mapEpochTokenomics[lastEpoch].epochPoint.totalTopUpsOLAS;
1447:                     totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].topUpUnitFraction; // <= FOUND
1448:                     uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100; // <= FOUND
1449:                     
1450:                     topUp += totalIncentives / sumUnitIncentives;
1451:                 }
1452:             }
1453: 
1454:             
1455:             reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;
1456:             
1457:             topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;
1458:         }
```
['[450](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L450-L473)']
```solidity
450:        for (uint256 i = 0; i < chainIds.length; ++i) { // <= FOUND
451:             
452:             address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];
453: 
454:             
455:             if (transferAmounts[i] > 0) { // <= FOUND
456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]); // <= FOUND
457:             }
458: 
459:             
460:             uint256 numActualTargets;
461:             bool[] memory positions = new bool[](stakingTargets[i].length); // <= FOUND
462:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
463:                 if (stakingIncentives[i][j] > 0) { // <= FOUND
464:                     positions[j] = true;
465:                     ++numActualTargets;
466:                 }
467:             }
468: 
469:             
470:             bytes32[] memory updatedStakingTargets = new bytes32[](numActualTargets);
471:             uint256[] memory updatedStakingAmounts = new uint256[](numActualTargets);
472:             uint256 numPos;
473:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
474:                 if (positions[j]) {
475:                     updatedStakingTargets[numPos] = stakingTargets[i][j]; // <= FOUND
476:                     updatedStakingAmounts[numPos] = stakingIncentives[i][j]; // <= FOUND
477:                     ++numPos;
478:                 }
479:             }
480: 
481:             
482:             if (chainIds[i] <= MAX_EVM_CHAIN_ID) {
483:                 
484:                 address[] memory stakingTargetsEVM = new address[](updatedStakingTargets.length);
485:                 for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {
486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));
487:                 }
488: 
489:                 
490:                 IDepositProcessor(depositProcessor).sendMessageBatch{value:valueAmounts[i]}(stakingTargetsEVM, // <= FOUND
491:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]); // <= FOUND
492:             } else {
493:                 
494:                 IDepositProcessor(depositProcessor).sendMessageBatchNonEVM{value:valueAmounts[i]}(updatedStakingTargets, // <= FOUND
495:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]); // <= FOUND
496:             }
497:         }
```
['[593](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L593-L609)']
```solidity
593:        for (uint256 i = 0; i < chainIds.length; ++i) { // <= FOUND
594:             
595:             address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];
596:             uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();
597: 
598:             stakingIncentives[i] = new uint256[](stakingTargets[i].length); // <= FOUND
599:             
600:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
601:                 
602:                 (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =
603:                     calculateStakingIncentives(numClaimedEpochs, chainIds[i], stakingTargets[i][j], bridgingDecimals);
604: 
605:                 
606:                 mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;
607: 
608:                 stakingIncentives[i][j] = stakingIncentive; // <= FOUND
609:                 transferAmounts[i] += stakingIncentive; // <= FOUND
610:                 totalAmounts[0] += stakingIncentive;
611:                 totalAmounts[2] += returnAmount;
612:             }
613: 
614:             
615:             if (transferAmounts[i] > 0) { // <= FOUND
616:                 uint256 withheldAmount = mapChainIdWithheldAmounts[chainIds[i]];
617:                 if (withheldAmount > 0) {
618:                     if (withheldAmount >= transferAmounts[i]) { // <= FOUND
619:                         withheldAmount -= transferAmounts[i]; // <= FOUND
620:                         transferAmounts[i] = 0; // <= FOUND
621:                     } else {
622:                         transferAmounts[i] -= withheldAmount; // <= FOUND
623:                         withheldAmount = 0;
624:                     }
625:                     mapChainIdWithheldAmounts[chainIds[i]] = withheldAmount;
626:                 }
627:             }
628: 
629:             
630:             totalAmounts[1] += transferAmounts[i]; // <= FOUND
631:         }
```
['[450](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L450-L473)']
```solidity
450:         for (uint256 i = 0; i < chainIds.length; ++i) { // <= FOUND
451:             
452:             address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];
453: 
454:             
455:             if (transferAmounts[i] > 0) { // <= FOUND
456:                 IToken(olas).transfer(depositProcessor, transferAmounts[i]); // <= FOUND
457:             }
458: 
459:             
460:             uint256 numActualTargets;
461:             bool[] memory positions = new bool[](stakingTargets[i].length); // <= FOUND
462:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
463:                 if (stakingIncentives[i][j] > 0) { // <= FOUND
464:                     positions[j] = true;
465:                     ++numActualTargets;
466:                 }
467:             }
468: 
469:             
470:             bytes32[] memory updatedStakingTargets = new bytes32[](numActualTargets);
471:             uint256[] memory updatedStakingAmounts = new uint256[](numActualTargets);
472:             uint256 numPos;
473:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
474:                 if (positions[j]) {
475:                     updatedStakingTargets[numPos] = stakingTargets[i][j]; // <= FOUND
476:                     updatedStakingAmounts[numPos] = stakingIncentives[i][j]; // <= FOUND
477:                     ++numPos;
478:                 }
479:             }
480: 
481:             
482:             if (chainIds[i] <= MAX_EVM_CHAIN_ID) {
483:                 
484:                 address[] memory stakingTargetsEVM = new address[](updatedStakingTargets.length);
485:                 for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {
486:                     stakingTargetsEVM[j] = address(uint160(uint256(updatedStakingTargets[j])));
487:                 }
488: 
489:                 
490:                 IDepositProcessor(depositProcessor).sendMessageBatch{value:valueAmounts[i]}(stakingTargetsEVM, // <= FOUND
491:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]); // <= FOUND
492:             } else {
493:                 
494:                 IDepositProcessor(depositProcessor).sendMessageBatchNonEVM{value:valueAmounts[i]}(updatedStakingTargets, // <= FOUND
495:                     updatedStakingAmounts, bridgePayloads[i], transferAmounts[i]); // <= FOUND
496:             }
497:         }
```
['[593](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L593-L609)']
```solidity
593:         for (uint256 i = 0; i < chainIds.length; ++i) { // <= FOUND
594:             
595:             address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];
596:             uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();
597: 
598:             stakingIncentives[i] = new uint256[](stakingTargets[i].length); // <= FOUND
599:             
600:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
601:                 
602:                 (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =
603:                     calculateStakingIncentives(numClaimedEpochs, chainIds[i], stakingTargets[i][j], bridgingDecimals);
604: 
605:                 
606:                 mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;
607: 
608:                 stakingIncentives[i][j] = stakingIncentive; // <= FOUND
609:                 transferAmounts[i] += stakingIncentive; // <= FOUND
610:                 totalAmounts[0] += stakingIncentive;
611:                 totalAmounts[2] += returnAmount;
612:             }
613: 
614:             
615:             if (transferAmounts[i] > 0) { // <= FOUND
616:                 uint256 withheldAmount = mapChainIdWithheldAmounts[chainIds[i]];
617:                 if (withheldAmount > 0) {
618:                     if (withheldAmount >= transferAmounts[i]) { // <= FOUND
619:                         withheldAmount -= transferAmounts[i]; // <= FOUND
620:                         transferAmounts[i] = 0; // <= FOUND
621:                     } else {
622:                         transferAmounts[i] -= withheldAmount; // <= FOUND
623:                         withheldAmount = 0;
624:                     }
625:                     mapChainIdWithheldAmounts[chainIds[i]] = withheldAmount;
626:                 }
627:             }
628: 
629:             
630:             totalAmounts[1] += transferAmounts[i]; // <= FOUND
631:         }
```
['[526](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L526-L550)']
```solidity
526:         for (uint256 i = 0; i < chainIds.length; ++i) { // <= FOUND
527:             
528:             
529:             if (lastChainId >= chainIds[i]) {
530:                 revert WrongChainId(chainIds[i]);
531:             }
532:             lastChainId = chainIds[i];
533: 
534:             
535:             if (stakingTargets[i].length == 0) { // <= FOUND
536:                 revert ZeroValue();
537:             }
538: 
539:             
540:             totalValueAmount += valueAmounts[i]; // <= FOUND
541: 
542:             
543:             uint256 localMaxNumStakingTargets = maxNumStakingTargets;
544:             if (stakingTargets[i].length > localMaxNumStakingTargets) { // <= FOUND
545:                 revert Overflow(stakingTargets[i].length, localMaxNumStakingTargets); // <= FOUND
546:             }
547: 
548:             bytes32 lastTarget;
549:             
550:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
551:                 
552:                 
553:                 if (uint256(lastTarget) >= uint256(stakingTargets[i][j])) { // <= FOUND
554:                     revert WrongAccount(stakingTargets[i][j]); // <= FOUND
555:                 }
556:                 lastTarget = stakingTargets[i][j]; // <= FOUND
557:             }
558:         }
```
['[793](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L793-L804)']
```solidity
793:         for (uint256 i = 0; i < accounts.length; ++i) { // <= FOUND
794:             
795:             Nominee memory nominee = Nominee(accounts[i], chainIds[i]);
796:             bytes32 nomineeHash = keccak256(abi.encode(nominee));
797: 
798:             
799:             if (mapNomineeIds[nomineeHash] == 0) {
800:                 revert NomineeDoesNotExist(accounts[i], chainIds[i]);
801:             }
802: 
803:             
804:             nextAllowedVotingTimes[i] = lastUserVote[voters[i]][nomineeHash] + WEIGHT_VOTE_DELAY; // <= FOUND
805:         }
```
['[154](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L154-L156)']
```solidity
154:         for (uint256 i = 0; i < targets.length; ++i) { // <= FOUND
155:             address target = targets[i];
156:             uint256 amount = amounts[i]; // <= FOUND
157: 
158:             
159:             
160:             bytes memory verifyData = abi.encodeCall(IStakingFactory.verifyInstanceAndGetEmissionsAmount, target);
161:             (bool success, bytes memory returnData) = stakingFactory.call(verifyData);
162: 
163:             uint256 limitAmount;
164:             
165:             if (success && returnData.length == 32) {
166:                 limitAmount = abi.decode(returnData, (uint256));
167:             }
168: 
169:             
170:             if (limitAmount == 0) {
171:                 
172:                 localWithheldAmount += amount;
173:                 emit AmountWithheld(target, amount);
174: 
175:                 
176:                 continue;
177:             }
178: 
179:             
180:             if (amount > limitAmount) {
181:                 uint256 targetWithheldAmount = amount - limitAmount;
182:                 localWithheldAmount += targetWithheldAmount;
183:                 amount = limitAmount;
184: 
185:                 emit AmountWithheld(target, targetWithheldAmount);
186:             }
187: 
188:             
189:             if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {
190:                 
191:                 IToken(olas).approve(target, amount);
192:                 IStaking(target).deposit(amount);
193: 
194:                 emit StakingTargetDeposited(target, amount);
195:             } else {
196:                 
197:                 bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));
198:                 
199:                 stakingQueueingNonces[queueHash] = true;
200: 
201:                 emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);
202:             }
203:         }
```
['[94](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L94-L96)']
```solidity
94:         for (uint256 i = 0; i < targets.length; ++i) { // <= FOUND
95:             address target = targets[i];
96:             uint256 amount = stakingIncentives[i]; // <= FOUND
97: 
98:             
99:             uint256 limitAmount = IStakingFactory(stakingFactory).verifyInstanceAndGetEmissionsAmount(target);
100: 
101:             
102:             if (limitAmount == 0) {
103:                 revert TargetEmissionsZero(target);
104:             }
105: 
106:             
107:             if (amount > limitAmount) {
108:                 uint256 refundAmount = amount - limitAmount;
109:                 amount = limitAmount;
110: 
111:                 
112:                 IToken(olas).transfer(timelock, refundAmount);
113: 
114:                 emit AmountRefunded(target, refundAmount);
115:             }
116: 
117:             
118:             IToken(olas).approve(target, amount);
119:             IStaking(target).deposit(amount);
120: 
121:             emit StakingTargetDeposited(target, amount);
122:         }
```
['[462](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L462-L462)']
```solidity
462:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
463:                 if (stakingIncentives[i][j] > 0) { // <= FOUND
464:                     positions[j] = true;
465:                     ++numActualTargets;
466:                 }
467:             }
```
['[473](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L473-L473)']
```solidity
473:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
474:                 if (positions[j]) {
475:                     updatedStakingTargets[numPos] = stakingTargets[i][j]; // <= FOUND
476:                     updatedStakingAmounts[numPos] = stakingIncentives[i][j]; // <= FOUND
477:                     ++numPos;
478:                 }
479:             }
```
['[550](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L550-L550)']
```solidity
550:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
551:                 
552:                 
553:                 if (uint256(lastTarget) >= uint256(stakingTargets[i][j])) { // <= FOUND
554:                     revert WrongAccount(stakingTargets[i][j]); // <= FOUND
555:                 }
556:                 lastTarget = stakingTargets[i][j]; // <= FOUND
557:             }
```
['[600](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L600-L609)']
```solidity
600:             for (uint256 j = 0; j < stakingTargets[i].length; ++j) { // <= FOUND
601:                 
602:                 (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =
603:                     calculateStakingIncentives(numClaimedEpochs, chainIds[i], stakingTargets[i][j], bridgingDecimals);
604: 
605:                 
606:                 mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;
607: 
608:                 stakingIncentives[i][j] = stakingIncentive; // <= FOUND
609:                 transferAmounts[i] += stakingIncentive; // <= FOUND
610:                 totalAmounts[0] += stakingIncentive;
611:                 totalAmounts[2] += returnAmount;
612:             }
```


</details>

## [NonCritical-16] Avoid arithmetic directly within array indices

### Resolution 
Avoiding arithmetic directly within array indices, like `test[i + 2]`, is recommended to prevent errors such as index out of bounds or incorrect data access. This practice enhances code readability and maintainability. Instead, calculate the index beforehand, store it in a variable, and then use that variable to access the array element. This approach reduces mistakes, especially in complex loops or when handling dynamic data, ensuring that each access is clear and verifiable. It's about keeping code clean and understandable, minimizing potential bugs.

Num of instances: 8

### Findings 


<details><summary>Click to show findings</summary>

['[951](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L951-L954)']
```solidity
951:                         
952:                         
953:                         
954:                         if (topUpEligible && incentiveFlags[unitType + 2]) { // <= FOUND
```
['[1170](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1170-L1171)']
```solidity
1170:         
1171:         TokenomicsPoint storage nextEpochPoint = mapEpochTokenomics[eCounter + 1]; // <= FOUND
```
['[1205](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1205-L1206)']
```solidity
1205:             
1206:             mapEpochStakingPoints[eCounter + 1].stakingFraction = mapEpochStakingPoints[eCounter].stakingFraction; // <= FOUND
```
['[1215](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1215-L1216)']
```solidity
1215:             
1216:             mapEpochStakingPoints[eCounter + 1].maxStakingIncentive = mapEpochStakingPoints[eCounter].maxStakingIncentive; // <= FOUND
```
['[1216](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1216-L1216)']
```solidity
1216:             mapEpochStakingPoints[eCounter + 1].minStakingWeight = mapEpochStakingPoints[eCounter].minStakingWeight; // <= FOUND
```
['[622](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L622-L623)']
```solidity
622:         
623:         nominee = setNominees[setNominees.length - 1]; // <= FOUND
```
['[468](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingBase.sol#L468-L469)']
```solidity
468:             
469:             uint256 idx = serviceIndexes[i - 1]; // <= FOUND
```
['[1096](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L1096-L1097)']
```solidity
1096:         
1097:         uint256 prevEpochTime = mapEpochTokenomics[epochCounter - 1].epochPoint.endTime; // <= FOUND
```


</details>

## [NonCritical-17] ERC777 tokens can introduce reentrancy risks

### Resolution 
ERC777 is an advanced token standard that introduces hooks, allowing operators to execute additional logic during transfers. While this feature offers greater flexibility, it also opens up the possibility of reentrancy attacks. Specifically, when tokens are sent, the receiving contract's `tokensReceived` hook gets called, and this external call can execute arbitrary code. An attacker can exploit this feature to re-enter the original function, potentially leading to double-spending or other types of financial manipulation.

To mitigate reentrancy risks with ERC777, it's crucial to adopt established security measures, such as utilizing reentrancy guards or following the check-effects-interactions pattern. Some developers opt to stick with the simpler ERC20 standard, which does not have these hooks, to minimize this risk. If you do choose to use ERC777, extreme caution and thorough auditing are advised to secure against potential reentrancy vulnerabilities.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[115](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingToken.sol#L115-L125)']
```solidity
115:     function deposit(uint256 amount) external { // <= FOUND
116:         
117:         uint256 newBalance = balance + amount;
118:         uint256 newAvailableRewards = availableRewards + amount;
119: 
120:         
121:         balance = newBalance;
122:         availableRewards = newAvailableRewards;
123: 
124:         
125:         SafeTransferLib.safeTransferFrom(stakingToken, msg.sender, address(this), amount); // <= FOUND
126: 
127:         emit Deposit(msg.sender, amount, newBalance, newAvailableRewards);
128:     }
```


</details>

## [Gas-1] Multiple accesses of the same mapping/array key/index should be cached 

### Resolution 
Caching repeated accesses to the same mapping or array key/index in smart contracts can lead to significant gas savings. In Solidity, each read operation from storage (like accessing a value in a mapping or array using a key or index) costs gas. By storing the accessed value in a local variable and reusing it within the function, you avoid multiple expensive storage read operations. This practice is particularly beneficial in loops or functions with multiple reads of the same data. Implementing this caching approach enhances efficiency and reduces transaction costs, which is crucial for optimizing smart contract performance and user experience on the blockchain.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[473](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L473-L540)']
```solidity
473:     function voteForNomineeWeights(bytes32 account, uint256 chainId, uint256 weight) public {
474:         
475:         bytes32 nomineeHash = keccak256(abi.encode(Nominee(account, chainId)));
476: 
477:         
478:         if (mapRemovedNominees[nomineeHash] > 0) { // <= FOUND
479:             revert NomineeRemoved(account, chainId);
480:         }
481: 
482:         
483:         int128 userSlope = IVEOLAS(ve).getLastUserPoint(msg.sender).slope;
484:         if (userSlope < 0) {
485:             revert NegativeSlope(msg.sender, userSlope);
486:         }
487: 
488:         uint256 lockEnd = IVEOLAS(ve).lockedEnd(msg.sender);
489:         uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;
490: 
491:         
492:         if (nextTime >= lockEnd) {
493:             revert LockExpired(msg.sender, lockEnd, nextTime);
494:         }
495: 
496:         
497:         if (weight > MAX_WEIGHT) {
498:             revert Overflow(weight, MAX_WEIGHT);
499:         }
500: 
501:         
502:         uint256 nextAllowedVotingTime = lastUserVote[msg.sender][nomineeHash] + WEIGHT_VOTE_DELAY; // <= FOUND
503:         if (nextAllowedVotingTime > block.timestamp) {
504:             revert VoteTooOften(msg.sender, block.timestamp, nextAllowedVotingTime);
505:         }
506: 
507:         
508:         VotedSlope memory oldSlope = voteUserSlopes[msg.sender][nomineeHash]; // <= FOUND
509:         uint256 oldBias;
510:         if (oldSlope.end > nextTime) {
511:             oldBias = oldSlope.slope * (oldSlope.end - nextTime);
512:         }
513: 
514:         VotedSlope memory newSlope = VotedSlope({
515:             slope: uint256(uint128(userSlope)) * weight / MAX_WEIGHT,
516:             end: lockEnd,
517:             power: weight
518:         });
519: 
520:         uint256 newBias = newSlope.slope * (lockEnd - nextTime);
521: 
522:         uint256 powerUsed = voteUserPower[msg.sender]; // <= FOUND
523:         powerUsed = powerUsed + newSlope.power - oldSlope.power;
524:         voteUserPower[msg.sender] = powerUsed; // <= FOUND
525:         if (powerUsed > MAX_WEIGHT) {
526:             revert Overflow(powerUsed, MAX_WEIGHT);
527:         }
528: 
529:         
530:         
531:         
532:         pointsWeight[nomineeHash][nextTime].bias = _maxAndSub(_getWeight(account, chainId) + newBias, oldBias); // <= FOUND
533:         pointsSum[nextTime].bias = _maxAndSub(_getSum() + newBias, oldBias); // <= FOUND
534:         if (oldSlope.end > nextTime) {
535:             pointsWeight[nomineeHash][nextTime].slope = // <= FOUND
536:                 _maxAndSub(pointsWeight[nomineeHash][nextTime].slope + newSlope.slope, oldSlope.slope); // <= FOUND
537:             pointsSum[nextTime].slope = _maxAndSub(pointsSum[nextTime].slope + newSlope.slope, oldSlope.slope); // <= FOUND
538:         } else {
539:             pointsWeight[nomineeHash][nextTime].slope += newSlope.slope; // <= FOUND
540:             pointsSum[nextTime].slope += newSlope.slope; // <= FOUND
541:         }
542:         if (oldSlope.end > block.timestamp) {
543:             
544:             changesWeight[nomineeHash][oldSlope.end] -= oldSlope.slope; // <= FOUND
545:             changesSum[oldSlope.end] -= oldSlope.slope;
546:         }
547:         
548:         changesWeight[nomineeHash][newSlope.end] += newSlope.slope; // <= FOUND
549:         changesSum[newSlope.end] += newSlope.slope;
550: 
551:         voteUserSlopes[msg.sender][nomineeHash] = newSlope; // <= FOUND
552: 
553:         
554:         lastUserVote[msg.sender][nomineeHash] = block.timestamp; // <= FOUND
555: 
556:         emit VoteForNominee(msg.sender, account, chainId, weight);
557:     }
```
['[883](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L883-L966)']
```solidity
883:     function _trackServiceDonations(
884:         address donator,
885:         uint256[] memory serviceIds,
886:         uint256[] memory amounts,
887:         uint256 curEpoch
888:     ) internal {
889:         
890:         address[] memory registries = new address[](2);
891:         (registries[0], registries[1]) = (componentRegistry, agentRegistry);
892: 
893:         
894:         bool[] memory incentiveFlags = new bool[](4);
895:         incentiveFlags[0] = (mapEpochTokenomics[curEpoch].unitPoints[0].rewardUnitFraction > 0);
896:         incentiveFlags[1] = (mapEpochTokenomics[curEpoch].unitPoints[1].rewardUnitFraction > 0);
897:         incentiveFlags[2] = (mapEpochTokenomics[curEpoch].unitPoints[0].topUpUnitFraction > 0);
898:         incentiveFlags[3] = (mapEpochTokenomics[curEpoch].unitPoints[1].topUpUnitFraction > 0);
899: 
900:         
901:         uint256 numServices = serviceIds.length;
902:         
903:         for (uint256 i = 0; i < numServices; ++i) {
904:             
905:             
906:             
907:             bool topUpEligible;
908:             if (incentiveFlags[2] || incentiveFlags[3]) {
909:                 address serviceOwner = IToken(serviceRegistry).ownerOf(serviceIds[i]);
910:                 topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||
911:                     IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;
912:             }
913: 
914:             
915:             for (uint256 unitType = 0; unitType < 2; ++unitType) {
916:                 
917:                 (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).
918:                     getUnitIdsOfService(IServiceRegistry.UnitType(unitType), serviceIds[i]);
919:                 
920:                 
921:                 if (numServiceUnits == 0) {
922:                     revert ServiceNeverDeployed(serviceIds[i]);
923:                 }
924:                 
925:                 if (incentiveFlags[unitType] || incentiveFlags[unitType + 2]) {
926:                     
927:                     uint96 amount = uint96(amounts[i] / numServiceUnits);
928:                     
929:                     for (uint256 j = 0; j < numServiceUnits; ++j) {
930:                         
931:                         uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch; // <= FOUND
932:                         
933:                         if (lastEpoch == 0) {
934:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch); // <= FOUND
935:                         } else if (lastEpoch < curEpoch) {
936:                             
937:                             
938:                             
939:                             
940:                             _finalizeIncentivesForUnitId(lastEpoch, unitType, serviceUnitIds[j]); // <= FOUND
941:                             
942:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch); // <= FOUND
943:                         }
944:                         
945:                         if (incentiveFlags[unitType]) {
946:                             mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeReward += amount; // <= FOUND
947:                         }
948:                         
949:                         
950:                         
951:                         if (topUpEligible && incentiveFlags[unitType + 2]) {
952:                             mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeTopUp += amount; // <= FOUND
953:                             mapEpochTokenomics[curEpoch].unitPoints[unitType].sumUnitTopUpsOLAS += amount;
954:                         }
955:                     }
956:                 }
957: 
958:                 
959:                 for (uint256 j = 0; j < numServiceUnits; ++j) {
960:                     
961:                     if (!mapNewUnits[unitType][serviceUnitIds[j]]) { // <= FOUND
962:                         mapNewUnits[unitType][serviceUnitIds[j]] = true; // <= FOUND
963:                         mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;
964:                         
965:                         
966:                         address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]); // <= FOUND
967:                         if (!mapNewOwners[unitOwner]) {
968:                             mapNewOwners[unitOwner] = true;
969:                             mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;
970:                         }
971:                     }
972:                 }
973:             }
974:         }
975:     }
```


</details>

## [Gas-2] Shortcircuit rules can be be used to optimize some gas usage 

### Resolution 
Reading state variables takes alot of gas to do, so if you have a conditional statement regarding a state varaible alongide others which are not related to state vars, it is advisable to have conditions which do not involve state vars be declared first in the overall conditional so unnecessary state variable lookups can be avoided.

Num of instances: 2

### Findings 


<details><summary>Click to show findings</summary>

['[189](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L189-L190)']
```solidity
189:             
190:             if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) { // <= FOUND
```
['[818](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingBase.sol#L818-L818)']
```solidity
818:         if (ts <= minStakingDuration && availableRewards > 0) { // <= FOUND
```


</details>

## [Gas-3] Setter emit which emits new and old value can be more efficient

### Resolution 
Instead of assigning the old state variable value to a temporary variable, it is far more efficient to move the emit to the start of the function and simply emit the current state variable value as previous and the parameter it will be replaced by as the new value. This can potentially save 800 gas due to one less warm read of a state variable

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[638](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L638-L691)']
```solidity
638:     function changeTokenomicsParameters(
639:         uint256 _devsPerCapital,
640:         uint256 _codePerDev,
641:         uint256 _epsilonRate,
642:         uint256 _epochLen,
643:         uint256 _veOLASThreshold
644:     ) external {
645:         
646:         if (msg.sender != owner) {
647:             revert OwnerOnly(msg.sender, owner);
648:         }
649: 
650:         
651:         if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {
652:             devsPerCapital = uint72(_devsPerCapital);
653:         } else {
654:             
655:             _devsPerCapital = devsPerCapital;
656:         }
657: 
658:         
659:         if (uint72(_codePerDev) > MIN_PARAM_VALUE) {
660:             codePerDev = uint72(_codePerDev);
661:         } else {
662:             
663:             _codePerDev = codePerDev;
664:         }
665: 
666:         
667:         
668:         
669:         if (_epsilonRate > 0 && _epsilonRate <= 17e18) {
670:             epsilonRate = uint64(_epsilonRate);
671:         } else {
672:             _epsilonRate = epsilonRate;
673:         }
674: 
675:         
676:         if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {
677:             nextEpochLen = uint32(_epochLen);
678:         } else {
679:             _epochLen = epochLen;
680:         }
681: 
682:         
683:         if (uint96(_veOLASThreshold) > 0) {
684:             nextVeOLASThreshold = uint96(_veOLASThreshold);
685:         } else {
686:             _veOLASThreshold = veOLASThreshold;
687:         }
688: 
689:         
690:         tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x01;
691:         emit TokenomicsParametersUpdateRequested(epochCounter + 1, _devsPerCapital, _codePerDev, _epsilonRate, _epochLen, // <= FOUND
692:             _veOLASThreshold);
693:     }
```


</details>

## [Gas-4] Public functions not used internally can be marked as external to save gas

### Resolution 
Public functions that aren't used internally in Solidity contracts should be made external to optimize gas usage and improve contract efficiency. External functions can only be called from outside the contract, and their arguments are directly read from the calldata, which is more gas-efficient than loading them into memory, as is the case for public functions. By using external visibility, developers can reduce gas consumption for external calls and ensure that the contract operates more cost-effectively for users. Moreover, setting the appropriate visibility level for functions also enhances code readability and maintainability, promoting a more secure and well-structured contract design.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[33](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/TokenomicsConstants.sol#L33-L33)']
```solidity
33:     function getSupplyCapForYear(uint256 numYears) public pure returns (uint256 supplyCap) 
```


</details>

## [Gas-5] Redundant state variable getters

### Resolution 
Getters for public state variables are automatically generated so there is no need to code them manually and lose gas

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[255](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingVerifier.sol#L255-L256)']
```solidity
255:     function getEmissionsAmountLimit(address) external view returns (uint256) {
256:         return emissionsLimit; // <= FOUND
257:     }
```


</details>

## [Gas-6] Identical Deployments Should be Replaced with Clone

### Resolution 
In the context of smart contracts, deploying multiple identical contracts can lead to inefficient use of gas and unnecessarily duplicate code on the blockchain. A more gas-efficient approach is to use a "clone" pattern. By deploying a master contract and then creating clones of it, only the differences between the instances are stored for each clone. This approach leverages the EIP-1167 standard, which defines a minimal proxy contract that points to the implementation contract. Clones can be far cheaper to deploy compared to full instances. So, the resolution is to replace identical deployments with clones, saving on gas and storage space.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[208](https://github.com/code-423n4/2024-05-olas/tree/main/registries/contracts/staking/StakingFactory.sol#L208-L208)']
```solidity
208:             instance := create2(0x0, add(0x20, deploymentData), mload(deploymentData), salt) // <= FOUND
209:         }
```


</details>

## [Gas-7] Where a value is casted more than once, consider caching the result to save gas

### Resolution 
Casting values multiple times in Solidity can be gas-inefficient. When a value undergoes repeated type conversions, the EVM must execute additional operations for each cast, consuming more gas than necessary. To optimize for gas efficiency, cache the result of the initial cast in a local variable and reuse it, rather than performing multiple casts. This not only conserves gas but also enhances code readability, reducing potential error points. For example, instead of repeatedly casting an `address` to `uint256`, cast once, store the result in a local variable, and reference that variable in subsequent operations.

Num of instances: 3

### Findings 


<details><summary>Click to show findings</summary>

['[408](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L408-L454)']
```solidity
408:     function initializeTokenomics(
409:         address _olas,
410:         address _treasury,
411:         address _depository,
412:         address _dispenser,
413:         address _ve,
414:         uint256 _epochLen,
415:         address _componentRegistry,
416:         address _agentRegistry,
417:         address _serviceRegistry,
418:         address _donatorBlacklist
419:     ) external {
420:         
421:         if (owner != address(0)) {
422:             revert AlreadyInitialized();
423:         }
424: 
425:         
426:         if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||
427:             _ve == address(0) || _componentRegistry == address(0) || _agentRegistry == address(0) ||
428:             _serviceRegistry == address(0)) {
429:             revert ZeroAddress();
430:         }
431: 
432:         
433:         owner = msg.sender;
434:         _locked = 1;
435:         epsilonRate = 1e17;
436:         veOLASThreshold = 10_000e18;
437: 
438:         
439:         if (uint32(_epochLen) < MIN_EPOCH_LENGTH) { // <= FOUND
440:             revert LowerThan(_epochLen, MIN_EPOCH_LENGTH);
441:         }
442: 
443:         
444:         if (uint32(_epochLen) > MAX_EPOCH_LENGTH) { // <= FOUND
445:             revert Overflow(_epochLen, MAX_EPOCH_LENGTH);
446:         }
447: 
448:         
449:         olas = _olas;
450:         treasury = _treasury;
451:         depository = _depository;
452:         dispenser = _dispenser;
453:         ve = _ve;
454:         epochLen = uint32(_epochLen); // <= FOUND
455:         componentRegistry = _componentRegistry;
456:         agentRegistry = _agentRegistry;
457:         serviceRegistry = _serviceRegistry;
458:         donatorBlacklist = _donatorBlacklist;
459: 
460:         
461:         uint256 _timeLaunch = IOLAS(_olas).timeLaunch();
462:         
463:         if (block.timestamp >= (_timeLaunch + ONE_YEAR)) {
464:             revert Overflow(_timeLaunch + ONE_YEAR, block.timestamp);
465:         }
466:         
467:         
468:         uint256 zeroYearSecondsLeft = uint32(_timeLaunch + ONE_YEAR - block.timestamp);
469:         
470:         
471:         
472:         uint256 _inflationPerSecond = getInflationForYear(0) / zeroYearSecondsLeft;
473:         inflationPerSecond = uint96(_inflationPerSecond);
474:         timeLaunch = uint32(_timeLaunch);
475: 
476:         
477:         mapEpochTokenomics[0].epochPoint.endTime = uint32(block.timestamp);
478: 
479:         
480:         epochCounter = 1;
481:         TokenomicsPoint storage tp = mapEpochTokenomics[1];
482: 
483:         
484:         devsPerCapital = 1e18;
485:         tp.epochPoint.idf = 1e18;
486: 
487:         
488:         
489:         tp.unitPoints[0].rewardUnitFraction = 83;
490:         tp.unitPoints[1].rewardUnitFraction = 17;
491:         
492: 
493:         
494:         
495:         
496:         
497:         
498:         codePerDev = 1e18;
499: 
500:         
501:         uint256 _maxBondFraction = 50;
502:         tp.epochPoint.maxBondFraction = uint8(_maxBondFraction);
503:         tp.unitPoints[0].topUpUnitFraction = 41;
504:         tp.unitPoints[1].topUpUnitFraction = 9;
505: 
506:         
507:         
508:         uint256 _maxBond = (_inflationPerSecond * _epochLen * _maxBondFraction) / 100;
509:         maxBond = uint96(_maxBond);
510:         effectiveBond = uint96(_maxBond);
511:     }
```
['[638](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L638-L677)']
```solidity
638:     function changeTokenomicsParameters(
639:         uint256 _devsPerCapital,
640:         uint256 _codePerDev,
641:         uint256 _epsilonRate,
642:         uint256 _epochLen,
643:         uint256 _veOLASThreshold
644:     ) external {
645:         
646:         if (msg.sender != owner) {
647:             revert OwnerOnly(msg.sender, owner);
648:         }
649: 
650:         
651:         if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {
652:             devsPerCapital = uint72(_devsPerCapital);
653:         } else {
654:             
655:             _devsPerCapital = devsPerCapital;
656:         }
657: 
658:         
659:         if (uint72(_codePerDev) > MIN_PARAM_VALUE) {
660:             codePerDev = uint72(_codePerDev);
661:         } else {
662:             
663:             _codePerDev = codePerDev;
664:         }
665: 
666:         
667:         
668:         
669:         if (_epsilonRate > 0 && _epsilonRate <= 17e18) {
670:             epsilonRate = uint64(_epsilonRate);
671:         } else {
672:             _epsilonRate = epsilonRate;
673:         }
674: 
675:         
676:         if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) { // <= FOUND
677:             nextEpochLen = uint32(_epochLen); // <= FOUND
678:         } else {
679:             _epochLen = epochLen;
680:         }
681: 
682:         
683:         if (uint96(_veOLASThreshold) > 0) {
684:             nextVeOLASThreshold = uint96(_veOLASThreshold);
685:         } else {
686:             _veOLASThreshold = veOLASThreshold;
687:         }
688: 
689:         
690:         tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x01;
691:         emit TokenomicsParametersUpdateRequested(epochCounter + 1, _devsPerCapital, _codePerDev, _epsilonRate, _epochLen,
692:             _veOLASThreshold);
693:     }
```
['[883](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Tokenomics.sol#L883-L942)']
```solidity
883:     function _trackServiceDonations(
884:         address donator,
885:         uint256[] memory serviceIds,
886:         uint256[] memory amounts,
887:         uint256 curEpoch
888:     ) internal {
889:         
890:         address[] memory registries = new address[](2);
891:         (registries[0], registries[1]) = (componentRegistry, agentRegistry);
892: 
893:         
894:         bool[] memory incentiveFlags = new bool[](4);
895:         incentiveFlags[0] = (mapEpochTokenomics[curEpoch].unitPoints[0].rewardUnitFraction > 0);
896:         incentiveFlags[1] = (mapEpochTokenomics[curEpoch].unitPoints[1].rewardUnitFraction > 0);
897:         incentiveFlags[2] = (mapEpochTokenomics[curEpoch].unitPoints[0].topUpUnitFraction > 0);
898:         incentiveFlags[3] = (mapEpochTokenomics[curEpoch].unitPoints[1].topUpUnitFraction > 0);
899: 
900:         
901:         uint256 numServices = serviceIds.length;
902:         
903:         for (uint256 i = 0; i < numServices; ++i) {
904:             
905:             
906:             
907:             bool topUpEligible;
908:             if (incentiveFlags[2] || incentiveFlags[3]) {
909:                 address serviceOwner = IToken(serviceRegistry).ownerOf(serviceIds[i]);
910:                 topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||
911:                     IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;
912:             }
913: 
914:             
915:             for (uint256 unitType = 0; unitType < 2; ++unitType) {
916:                 
917:                 (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).
918:                     getUnitIdsOfService(IServiceRegistry.UnitType(unitType), serviceIds[i]);
919:                 
920:                 
921:                 if (numServiceUnits == 0) {
922:                     revert ServiceNeverDeployed(serviceIds[i]);
923:                 }
924:                 
925:                 if (incentiveFlags[unitType] || incentiveFlags[unitType + 2]) {
926:                     
927:                     uint96 amount = uint96(amounts[i] / numServiceUnits);
928:                     
929:                     for (uint256 j = 0; j < numServiceUnits; ++j) {
930:                         
931:                         uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch;
932:                         
933:                         if (lastEpoch == 0) {
934:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch); // <= FOUND
935:                         } else if (lastEpoch < curEpoch) {
936:                             
937:                             
938:                             
939:                             
940:                             _finalizeIncentivesForUnitId(lastEpoch, unitType, serviceUnitIds[j]);
941:                             
942:                             mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch); // <= FOUND
943:                         }
944:                         
945:                         if (incentiveFlags[unitType]) {
946:                             mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeReward += amount;
947:                         }
948:                         
949:                         
950:                         
951:                         if (topUpEligible && incentiveFlags[unitType + 2]) {
952:                             mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeTopUp += amount;
953:                             mapEpochTokenomics[curEpoch].unitPoints[unitType].sumUnitTopUpsOLAS += amount;
954:                         }
955:                     }
956:                 }
957: 
958:                 
959:                 for (uint256 j = 0; j < numServiceUnits; ++j) {
960:                     
961:                     if (!mapNewUnits[unitType][serviceUnitIds[j]]) {
962:                         mapNewUnits[unitType][serviceUnitIds[j]] = true;
963:                         mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;
964:                         
965:                         
966:                         address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);
967:                         if (!mapNewOwners[unitOwner]) {
968:                             mapNewOwners[unitOwner] = true;
969:                             mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;
970:                         }
971:                     }
972:                 }
973:             }
974:         }
975:     }
```


</details>

## [Gas-8] State variable written in a loop

### Resolution 
To optimize gas consumption in smart contracts, especially when dealing with loops that write to state variables, a recommended approach is to use a local (memory) variable within the loop for calculations and then update the state variable only once after the loop completes. This method reduces the number of state changes, each of which costs gas, thereby making the operation more gas-efficient.

Num of instances: 3

### Findings 


<details><summary>Click to show findings</summary>

['[881](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/Dispenser.sol#L881-L941)']
```solidity
881:         for (uint256 j = firstClaimedEpoch; j < lastClaimedEpoch; ++j) {
882:             
883:             ITokenomics.StakingPoint memory stakingPoint =
884:                 ITokenomics(tokenomics).mapEpochStakingPoints(j);
885: 
886:             uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);
887: 
888:             
889:             
890:             
891:             
892:             (uint256 stakingWeight, uint256 totalWeightSum) =
893:                 IVoteWeighting(voteWeighting).nomineeRelativeWeight(stakingTarget, chainId, endTime);
894: 
895:             uint256 stakingIncentive;
896:             uint256 returnAmount;
897: 
898:             
899:             
900:             
901:             uint256 availableStakingAmount = stakingPoint.stakingIncentive;
902: 
903:             uint256 stakingDiff;
904:             if (availableStakingAmount > totalWeightSum) {
905:                 stakingDiff = availableStakingAmount - totalWeightSum;
906:                 availableStakingAmount = totalWeightSum;
907:             }
908: 
909:             
910:             
911:             if (stakingWeight < uint256(stakingPoint.minStakingWeight) * 1e14) {
912:                 
913:                 returnAmount = ((stakingDiff + availableStakingAmount) * stakingWeight) / 1e18;
914:             } else {
915:                 
916:                 stakingIncentive = (availableStakingAmount * stakingWeight) / 1e18; // <= FOUND
917:                 
918:                 returnAmount = (stakingDiff * stakingWeight) / 1e18;
919: 
920:                 
921:                 availableStakingAmount = stakingPoint.maxStakingAmount;
922:                 if (stakingIncentive > availableStakingAmount) {
923:                     
924:                     returnAmount += stakingIncentive - availableStakingAmount;
925:                     
926:                     stakingIncentive = availableStakingAmount; // <= FOUND
927:                 }
928: 
929:                 
930:                 
931:                 if (bridgingDecimals < 18) {
932:                     uint256 normalizedStakingAmount = stakingIncentive / (10 ** (18 - bridgingDecimals));
933:                     normalizedStakingAmount *= 10 ** (18 - bridgingDecimals);
934:                     
935:                     
936:                     returnAmount += stakingIncentive - normalizedStakingAmount;
937:                     
938:                     stakingIncentive = normalizedStakingAmount; // <= FOUND
939:                 }
940:                 
941:                 totalStakingIncentive += stakingIncentive; // <= FOUND
942:             }
943:             
944:             totalReturnAmount += returnAmount;
945:         }
```
['[227](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L227-L244)']
```solidity
227:        for (uint256 i = 0; i < MAX_NUM_WEEKS; i++) {
228:             if (t > block.timestamp) {
229:                 break;
230:             }
231:             t += WEEK;
232:             uint256 dBias = pt.slope * WEEK;
233:             if (pt.bias > dBias) {
234:                 pt.bias -= dBias;
235:                 uint256 dSlope = changesSum[t];
236:                 pt.slope -= dSlope;
237:             } else {
238:                 pt.bias = 0;
239:                 pt.slope = 0;
240:             }
241: 
242:             pointsSum[t] = pt;
243:             if (t > block.timestamp) {
244:                 timeSum = t; // <= FOUND
245:             }
246:         }
```
['[227](https://github.com/code-423n4/2024-05-olas/tree/main/governance/contracts/VoteWeighting.sol#L227-L244)']
```solidity
227:         for (uint256 i = 0; i < MAX_NUM_WEEKS; i++) {
228:             if (t > block.timestamp) {
229:                 break;
230:             }
231:             t += WEEK;
232:             uint256 dBias = pt.slope * WEEK;
233:             if (pt.bias > dBias) {
234:                 pt.bias -= dBias;
235:                 uint256 dSlope = changesSum[t];
236:                 pt.slope -= dSlope;
237:             } else {
238:                 pt.bias = 0;
239:                 pt.slope = 0;
240:             }
241: 
242:             pointsSum[t] = pt;
243:             if (t > block.timestamp) {
244:                 timeSum = t; // <= FOUND
245:             }
246:         }
```


</details>

## [Gas-9] Public functions not called internally

### Resolution 
Public functions that aren't used internally in Solidity contracts should be made external to optimize gas usage and improve contract efficiency. External functions can only be called from outside the contract, and their arguments are directly read from the calldata, which is more gas-efficient than loading them into memory, as is the case for public functions. By using external visibility, developers can reduce gas consumption for external calls and ensure that the contract operates more cost-effectively for users. Moreover, setting the appropriate visibility level for functions also enhances code readability and maintainability, promoting a more secure and well-structured contract design.

Num of instances: 1

### Findings 


<details><summary>Click to show findings</summary>

['[33](https://github.com/code-423n4/2024-05-olas/tree/main/tokenomics/contracts/TokenomicsConstants.sol#L33-L33)']
```solidity
33:     function getSupplyCapForYear(uint256 numYears) public pure returns (uint256 supplyCap) 
```


</details>