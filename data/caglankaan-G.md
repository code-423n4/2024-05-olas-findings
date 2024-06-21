### Prevent re-setting a state variable with the same value
Not only is wasteful in terms of gas, but this is especially problematic when an event is emitted and the old and new values set are the same, as listeners might not expect this kind of scenario.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

455:        epochLen = uint32(_epochLen);	// @audit-issue

459:        donatorBlacklist = _donatorBlacklist;	// @audit-issue

622:        donatorBlacklist = _donatorBlacklist;	// @audit-issue

653:            devsPerCapital = uint72(_devsPerCapital);	// @audit-issue

661:            codePerDev = uint72(_codePerDev);	// @audit-issue

678:            nextEpochLen = uint32(_epochLen);	// @audit-issue

685:            nextVeOLASThreshold = uint96(_veOLASThreshold);	// @audit-issue

740:        mapEpochStakingPoints[eCounter].stakingFraction = uint8(_stakingFraction);	// @audit-issue

1020:        mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH = uint96(donationETH);	// @audit-issue
```
[455](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L455-L455), [459](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L459-L459), [622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L622-L622), [653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L653-L653), [661](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L661-L661), [678](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L678-L678), [685](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L685-L685), [740](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L740-L740), [1020](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1020-L1020), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

724:            mapChainIdDepositProcessors[chainIds[i]] = depositProcessors[i];	// @audit-issue

1247:        paused = pauseState;	// @audit-issue
```
[724](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L724-L724), [1247](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1247-L1247), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

120:        implementationsCheck = setCheck;	// @audit-issue

147:        implementationsCheck = setCheck;	// @audit-issue

157:            mapImplementations[implementations[i]] = statuses[i];	// @audit-issue
```
[120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L120-L120), [147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L147-L147), [157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L157-L157), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

133:        verifier = newVerifier;	// @audit-issue
```
[133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L133-L133), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

392:        dispenser = newDispenser;	// @audit-issue

532:        pointsWeight[nomineeHash][nextTime].bias = _maxAndSub(_getWeight(account, chainId) + newBias, oldBias);	// @audit-issue
```
[392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L392-L392), [532](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L532-L532), 


#### Recommendation

Implement checks in your Solidity contracts to avoid re-setting state variables to their existing values. Prior to updating a state variable, compare the new value with the current value and proceed with the assignment only if they differ. Additionally, ensure that events related to state variable updates are emitted only when actual changes occur. This approach not only saves gas but also prevents confusion and unnecessary triggers in event listeners. Regularly reviewing and optimizing your contract for such redundancies can significantly enhance efficiency and clarity in contract operations.

### Cache Local Variable Array Length In For Loop
If not cached, the solidity compiler will always read the length of the array during each iteration. If it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

1335:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1357:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1407:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1429:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue
```
[1335](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1335-L1335), [1357](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1357-L1357), [1407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1407-L1407), [1429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1429-L1429), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

450:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

462:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

473:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

485:                for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {	// @audit-issue

526:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

550:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

593:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

600:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

717:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue
```
[450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L450-L450), [462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L462-L462), [473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L473-L473), [485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L485-L485), [526](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L526-L526), [550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L550-L550), [593](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L593-L593), [600](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L600-L600), [717](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L717-L717), 


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
Path: ./registries/contracts/staking/StakingVerifier.sol

150:        for (uint256 i = 0; i < implementations.length; ++i) {	// @audit-issue
```
[150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L150-L150), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

332:        for (uint256 i = 0; i < _stakingParams.agentIds.length; ++i) {	// @audit-issue

669:            for (uint256 i = 0; i < serviceIds.length; ++i) {	// @audit-issue

835:        for (; idx < serviceIds.length; ++idx) {	// @audit-issue
```
[332](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L332-L332), [669](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L669-L669), [835](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L835-L835), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

92:        for (uint256 i = 0; i < serviceAgentIds.length; ++i) {	// @audit-issue
```
[92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L92-L92), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

573:        for (uint256 i = 0; i < accounts.length; ++i) {	// @audit-issue

793:        for (uint256 i = 0; i < accounts.length; ++i) {	// @audit-issue
```
[573](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L573-L573), [793](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L793-L793), 


#### Recommendation

To optimize gas consumption, cache the length of memory arrays before for loop iterations in Solidity. This reduces redundant mload operations, saving 3 gas per iteration after the first and improving overall contract efficiency.

### State Variable Access Within a Loop
State variable reads and writes are more expensive than local variable reads and writes. Therefore, it is recommended to replace state variable reads and writes within loops with a local variable. Gas savings should be multiplied by the average loop length.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

910:                address serviceOwner = IToken(serviceRegistry).ownerOf(serviceIds[i]);	// @audit-issue: `serviceRegistry` used in loop.

911:                topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||	// @audit-issue: `ve` used in loop.

911:                topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||	// @audit-issue: `veOLASThreshold` used in loop.

912:                    IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;	// @audit-issue: `ve` used in loop.

912:                    IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;	// @audit-issue: `veOLASThreshold` used in loop.

918:                (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).	// @audit-issue: `serviceRegistry` used in loop.

932:                        uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch;	// @audit-issue: `mapUnitIncentives` used in loop.

935:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);	// @audit-issue: `mapUnitIncentives` used in loop.

943:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);	// @audit-issue: `mapUnitIncentives` used in loop.

947:                            mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeReward += amount;	// @audit-issue: `mapUnitIncentives` used in loop.

953:                            mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeTopUp += amount;	// @audit-issue: `mapUnitIncentives` used in loop.

954:                            mapEpochTokenomics[curEpoch].unitPoints[unitType].sumUnitTopUpsOLAS += amount;	// @audit-issue: `mapEpochTokenomics` used in loop.

962:                    if (!mapNewUnits[unitType][serviceUnitIds[j]]) {	// @audit-issue: `mapNewUnits` used in loop.

963:                        mapNewUnits[unitType][serviceUnitIds[j]] = true;	// @audit-issue: `mapNewUnits` used in loop.

964:                        mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;	// @audit-issue: `mapEpochTokenomics` used in loop.

968:                        if (!mapNewOwners[unitOwner]) {	// @audit-issue: `mapNewOwners` used in loop.

969:                            mapNewOwners[unitOwner] = true;	// @audit-issue: `mapNewOwners` used in loop.

970:                            mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;	// @audit-issue: `mapEpochTokenomics` used in loop.

918:                (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).	// @audit-issue: `serviceRegistry` used in loop.

932:                        uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch;	// @audit-issue: `mapUnitIncentives` used in loop.

935:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);	// @audit-issue: `mapUnitIncentives` used in loop.

943:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);	// @audit-issue: `mapUnitIncentives` used in loop.

947:                            mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeReward += amount;	// @audit-issue: `mapUnitIncentives` used in loop.

953:                            mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeTopUp += amount;	// @audit-issue: `mapUnitIncentives` used in loop.

954:                            mapEpochTokenomics[curEpoch].unitPoints[unitType].sumUnitTopUpsOLAS += amount;	// @audit-issue: `mapEpochTokenomics` used in loop.

962:                    if (!mapNewUnits[unitType][serviceUnitIds[j]]) {	// @audit-issue: `mapNewUnits` used in loop.

963:                        mapNewUnits[unitType][serviceUnitIds[j]] = true;	// @audit-issue: `mapNewUnits` used in loop.

964:                        mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;	// @audit-issue: `mapEpochTokenomics` used in loop.

968:                        if (!mapNewOwners[unitOwner]) {	// @audit-issue: `mapNewOwners` used in loop.

969:                            mapNewOwners[unitOwner] = true;	// @audit-issue: `mapNewOwners` used in loop.

970:                            mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;	// @audit-issue: `mapEpochTokenomics` used in loop.

932:                        uint256 lastEpoch = mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch;	// @audit-issue: `mapUnitIncentives` used in loop.

935:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);	// @audit-issue: `mapUnitIncentives` used in loop.

943:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);	// @audit-issue: `mapUnitIncentives` used in loop.

947:                            mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeReward += amount;	// @audit-issue: `mapUnitIncentives` used in loop.

953:                            mapUnitIncentives[unitType][serviceUnitIds[j]].pendingRelativeTopUp += amount;	// @audit-issue: `mapUnitIncentives` used in loop.

954:                            mapEpochTokenomics[curEpoch].unitPoints[unitType].sumUnitTopUpsOLAS += amount;	// @audit-issue: `mapEpochTokenomics` used in loop.

962:                    if (!mapNewUnits[unitType][serviceUnitIds[j]]) {	// @audit-issue: `mapNewUnits` used in loop.

963:                        mapNewUnits[unitType][serviceUnitIds[j]] = true;	// @audit-issue: `mapNewUnits` used in loop.

964:                        mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;	// @audit-issue: `mapEpochTokenomics` used in loop.

968:                        if (!mapNewOwners[unitOwner]) {	// @audit-issue: `mapNewOwners` used in loop.

969:                            mapNewOwners[unitOwner] = true;	// @audit-issue: `mapNewOwners` used in loop.

970:                            mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;	// @audit-issue: `mapEpochTokenomics` used in loop.

1012:            if (!IServiceRegistry(serviceRegistry).exists(serviceIds[i])) {	// @audit-issue: `serviceRegistry` used in loop.

1359:            uint256 lastEpoch = mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch;	// @audit-issue: `mapUnitIncentives` used in loop.

1366:                mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch = 0;	// @audit-issue: `mapUnitIncentives` used in loop.

1370:            reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;	// @audit-issue: `mapUnitIncentives` used in loop.

1371:            mapUnitIncentives[unitTypes[i]][unitIds[i]].reward = 0;	// @audit-issue: `mapUnitIncentives` used in loop.

1373:            topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;	// @audit-issue: `mapUnitIncentives` used in loop.

1374:            mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp = 0;	// @audit-issue: `mapUnitIncentives` used in loop.

1431:            uint256 lastEpoch = mapUnitIncentives[unitTypes[i]][unitIds[i]].lastEpoch;	// @audit-issue: `mapUnitIncentives` used in loop.

1436:                uint256 totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeReward;	// @audit-issue: `mapUnitIncentives` used in loop.

1438:                    totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].rewardUnitFraction;	// @audit-issue: `mapEpochTokenomics` used in loop.

1443:                totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeTopUp;	// @audit-issue: `mapUnitIncentives` used in loop.

1447:                    totalIncentives *= mapEpochTokenomics[lastEpoch].epochPoint.totalTopUpsOLAS;	// @audit-issue: `mapEpochTokenomics` used in loop.

1448:                    totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].topUpUnitFraction;	// @audit-issue: `mapEpochTokenomics` used in loop.

1449:                    uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100;	// @audit-issue: `mapEpochTokenomics` used in loop.

1456:            reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;	// @audit-issue: `mapUnitIncentives` used in loop.

1458:            topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;	// @audit-issue: `mapUnitIncentives` used in loop.
```
[910](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L910-L910), [911](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L911-L911), [911](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L911-L911), [912](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L912-L912), [912](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L912-L912), [918](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L918-L918), [932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L932-L932), [935](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L935-L935), [943](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L943-L943), [947](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L947-L947), [953](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L953-L953), [954](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L954-L954), [962](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L962-L962), [963](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L963-L963), [964](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L964-L964), [968](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L968-L968), [969](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L969-L969), [970](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L970-L970), [918](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L918-L918), [932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L932-L932), [935](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L935-L935), [943](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L943-L943), [947](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L947-L947), [953](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L953-L953), [954](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L954-L954), [962](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L962-L962), [963](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L963-L963), [964](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L964-L964), [968](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L968-L968), [969](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L969-L969), [970](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L970-L970), [932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L932-L932), [935](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L935-L935), [943](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L943-L943), [947](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L947-L947), [953](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L953-L953), [954](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L954-L954), [962](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L962-L962), [963](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L963-L963), [964](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L964-L964), [968](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L968-L968), [969](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L969-L969), [970](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L970-L970), [1012](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1012-L1012), [1359](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1359-L1359), [1366](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1366-L1366), [1370](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1370-L1370), [1371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1371-L1371), [1373](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1373-L1373), [1374](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1374-L1374), [1431](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1431-L1431), [1436](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1436-L1436), [1438](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1438-L1438), [1443](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1443-L1443), [1447](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1447-L1447), [1448](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1448-L1448), [1449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1449-L1449), [1456](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1456-L1456), [1458](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1458-L1458), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

452:            address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];	// @audit-issue: `mapChainIdDepositProcessors` used in loop.

543:            uint256 localMaxNumStakingTargets = maxNumStakingTargets;	// @audit-issue: `maxNumStakingTargets` used in loop.

595:            address depositProcessor = mapChainIdDepositProcessors[chainIds[i]];	// @audit-issue: `mapChainIdDepositProcessors` used in loop.

606:                mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;	// @audit-issue: `mapLastClaimedStakingEpochs` used in loop.

616:                uint256 withheldAmount = mapChainIdWithheldAmounts[chainIds[i]];	// @audit-issue: `mapChainIdWithheldAmounts` used in loop.

625:                    mapChainIdWithheldAmounts[chainIds[i]] = withheldAmount;	// @audit-issue: `mapChainIdWithheldAmounts` used in loop.

606:                mapLastClaimedStakingEpochs[nomineeHash] = lastClaimedEpoch;	// @audit-issue: `mapLastClaimedStakingEpochs` used in loop.

724:            mapChainIdDepositProcessors[chainIds[i]] = depositProcessors[i];	// @audit-issue: `mapChainIdDepositProcessors` used in loop.

884:                ITokenomics(tokenomics).mapEpochStakingPoints(j);	// @audit-issue: `tokenomics` used in loop.

886:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue: `tokenomics` used in loop.

893:                IVoteWeighting(voteWeighting).nomineeRelativeWeight(stakingTarget, chainId, endTime);	// @audit-issue: `voteWeighting` used in loop.

1148:            ITokenomics.StakingPoint memory stakingPoint = ITokenomics(tokenomics).mapEpochStakingPoints(j);	// @audit-issue: `tokenomics` used in loop.

1151:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue: `tokenomics` used in loop.

1154:            (uint256 stakingWeight, ) = IVoteWeighting(voteWeighting).nomineeRelativeWeight(retainer,	// @audit-issue: `voteWeighting` used in loop.
```
[452](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L452-L452), [543](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L543-L543), [595](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L595-L595), [606](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L606-L606), [616](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L616-L616), [625](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L625-L625), [606](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L606-L606), [724](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L724-L724), [884](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L884-L884), [886](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L886-L886), [893](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L893-L893), [1148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1148-L1148), [1151](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1151-L1151), [1154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1154-L1154), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

199:                stakingQueueingNonces[queueHash] = true;	// @audit-issue: `stakingQueueingNonces` used in loop.
```
[199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L199-L199), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

157:            mapImplementations[implementations[i]] = statuses[i];	// @audit-issue: `mapImplementations` used in loop.
```
[157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L157-L157), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

338:            agentIds.push(agentId);	// @audit-issue: `agentIds` used in loop.

454:                ServiceInfo storage sInfo = mapServiceInfo[serviceId];	// @audit-issue: `mapServiceInfo` used in loop.

470:            setServiceIds[idx] = setServiceIds[totalNumServices];	// @audit-issue: `setServiceIds` used in loop.

472:            setServiceIds.pop();	// @audit-issue: `setServiceIds` used in loop.

550:                serviceIds[i] = setServiceIds[i];	// @audit-issue: `setServiceIds` used in loop.

553:                ServiceInfo storage sInfo = mapServiceInfo[serviceIds[i]];	// @audit-issue: `mapServiceInfo` used in loop.

574:                    eligibleServiceRewards[numServices] = rewardsPerSecond * ts;	// @audit-issue: `rewardsPerSecond` used in loop.

629:                    mapServiceInfo[curServiceId].reward += updatedReward;	// @audit-issue: `mapServiceInfo` used in loop.

653:                    mapServiceInfo[curServiceId].reward += eligibleServiceRewards[i];	// @audit-issue: `mapServiceInfo` used in loop.

673:                mapServiceInfo[curServiceId].nonces = serviceNonces[i];	// @audit-issue: `mapServiceInfo` used in loop.

678:                    serviceInactivity[i] = mapServiceInfo[curServiceId].inactivity + serviceInactivity[i];	// @audit-issue: `mapServiceInfo` used in loop.

679:                    mapServiceInfo[curServiceId].inactivity = serviceInactivity[i];	// @audit-issue: `mapServiceInfo` used in loop.

681:                    if (serviceInactivity[i] > maxInactivityDuration) {	// @audit-issue: `maxInactivityDuration` used in loop.

691:                    mapServiceInfo[curServiceId].inactivity = 0;	// @audit-issue: `mapServiceInfo` used in loop.

775:                if (agentIds[i] != service.agentIds[i]) {	// @audit-issue: `agentIds` used in loop.

776:                    revert WrongAgentId(agentIds[i]);	// @audit-issue: `agentIds` used in loop.
```
[338](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L338-L338), [454](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L454-L454), [470](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L470-L470), [472](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L472-L472), [550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L550-L550), [553](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L553-L553), [574](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L574-L574), [629](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L629-L629), [653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L653-L653), [673](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L673-L673), [678](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L678-L678), [679](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L679-L679), [681](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L681-L681), [691](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L691-L691), [775](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L775-L775), [776](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L776-L776), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

93:            uint256 bond = IServiceTokenUtility(serviceRegistryTokenUtility).getAgentBond(serviceId, serviceAgentIds[i]);	// @audit-issue: `serviceRegistryTokenUtility` used in loop.
```
[93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L93-L93), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

235:                uint256 dSlope = changesSum[t];	// @audit-issue: `changesSum` used in loop.

242:            pointsSum[t] = pt;	// @audit-issue: `pointsSum` used in loop.

244:                timeSum = t;	// @audit-issue: `timeSum` used in loop.

275:                uint256 dSlope = changesWeight[nomineeHash][t];	// @audit-issue: `changesWeight` used in loop.

282:            pointsWeight[nomineeHash][t] = pt;	// @audit-issue: `pointsWeight` used in loop.

284:                timeWeight[nomineeHash] = t;	// @audit-issue: `timeWeight` used in loop.

799:            if (mapNomineeIds[nomineeHash] == 0) {	// @audit-issue: `mapNomineeIds` used in loop.

804:            nextAllowedVotingTimes[i] = lastUserVote[voters[i]][nomineeHash] + WEIGHT_VOTE_DELAY;	// @audit-issue: `lastUserVote` used in loop.
```
[235](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L235-L235), [242](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L242-L242), [244](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L244-L244), [275](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L275-L275), [282](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L282-L282), [284](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L284-L284), [799](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L799-L799), [804](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L804-L804), 


#### Recommendation

Optimize gas usage in Solidity by replacing state variable reads and writes within loops with local variable operations. This reduces gas costs significantly, especially when multiplied by the average loop length.

### Another Constant With Same Value Is Already Defined
Defining multiple constants with the same value in a Solidity contract introduces unnecessary redundancy and can lead to confusion and potential errors in contract maintenance. Constants are used to define values that remain unchanged throughout the contract's lifecycle, enhancing readability and efficiency. However, when multiple constants representing the same value are defined, it not only clutters the code but also complicates the process of updating values and understanding the contract's logic. Consolidating these constants into a single definition improves code clarity and maintainability.

```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

36:    constructor(	// @audit-issue: Same value `0x8c5261668696ce22758910d05bab8f186d6eb247ceac2af2e82c7dc17669b036` is already defined on line `36` as variable: `SEND_MESSAGE_EVENT_SIG`
```
[36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L36-L36), 


#### Recommendation

Identify and consolidate duplicate constants in your Solidity contracts. Examine your contract's constants to find any with identical values and refactor your code to use a single constant definition for each unique value. This may involve choosing the most descriptive and appropriate name for the consolidated constant and updating all references accordingly. Such consolidation not only makes your contract more concise and easier to read but also simplifies future updates to constant values. Document the purpose and usage of each constant clearly, ensuring that the chosen names accurately reflect their role within the contract.

### Shortcircuit rules can be be used to optimize some gas usage
Some conditions may be reordered to save an SLOAD (2100 gas), as we avoid reading state variables when the first part of the condition fails (with `&&`), or succeeds (with `||`).

```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {	// @audit-issue: Control with `IToken` can be done after all non state variable/function call controls.
```
[189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L189-L189), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

536:        if (block.timestamp - tsCheckpointLast >= livenessPeriod && lastAvailableRewards > 0) {	// @audit-issue: Control with `livenessPeriod` can be done after all non state variable/function call controls.
```
[536](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L536-L536), 


#### Recommendation

Review your Solidity contracts for conditional statements that use `&&` and `||` operators. Prioritize conditions that are less gas-intensive or have a higher likelihood of determining the outcome of the entire expression. For instance, if a condition involves an `SLOAD` operation and another is a simple variable comparison, evaluate the simpler condition first:
Before optimization:
```solidity
if (contractStateVariable > value && isEligible(msg.sender)) {
    // Execution logic
}

After optimization:
```solidity
if (isEligible(msg.sender) && contractStateVariable > value) {
    // Execution logic
}

This rearrangement ensures that if `isEligible(msg.sender)` returns false, the contract avoids the gas cost associated with reading `contractStateVariable` from storage. Apply this principle across your contract's logic where applicable, especially in functions called frequently or in gas-sensitive contexts. Testing remains crucial; ensure that the reordering of conditions does not alter the intended logic or outcomes of your contract.

### Same cast is done multiple times
It's cheaper to do it once, and store the result to a variable.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

440:        if (uint32(_epochLen) < MIN_EPOCH_LENGTH) {	// @audit-issue: Same casting also done in line(s): `[445, 455]`.

510:        maxBond = uint96(_maxBond);	// @audit-issue: Same casting also done in line(s): `[511]`.

652:        if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {	// @audit-issue: Same casting also done in line(s): `[653]`.

660:        if (uint72(_codePerDev) > MIN_PARAM_VALUE) {	// @audit-issue: Same casting also done in line(s): `[661]`.

677:        if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {	// @audit-issue: Same casting also done in line(s): `[678]`.

684:        if (uint96(_veOLASThreshold) > 0) {	// @audit-issue: Same casting also done in line(s): `[685]`.

857:            mapUnitIncentives[unitType][unitId].reward = uint96(totalIncentives);	// @audit-issue: Same casting also done in line(s): `[873]`.

943:                            mapUnitIncentives[unitType][serviceUnitIds[j]].lastEpoch = uint32(curEpoch);	// @audit-issue: Same casting also done in line(s): `[935]`.

1265:            maxBond = uint96(curMaxBond);	// @audit-issue: Same casting also done in line(s): `[1258, 1271]`.
```
[440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L440-L440), [510](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L510-L510), [652](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L652-L652), [660](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L660-L660), [677](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L677-L677), [684](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L684-L684), [857](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L857-L857), [943](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L943-L943), [1265](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1265-L1265), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

112:        (uint256 cost, ) = IBridge(l2MessageRelayer).quoteEVMDeliveryPrice(uint16(l1SourceChainId), 0, gasLimitMessage);	// @audit-issue: Same casting also done in line(s): `[121, 120]`.
```
[112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L112-L112), 


#### Recommendation

Identify instances in your Solidity contracts where the same value is cast to another type multiple times. Refactor these operations by performing the cast once and storing the result in a local variable. Use this variable for all subsequent operations requiring the cast value.

### `address(this)` should be cached
Cacheing saves gas when compared to repeating the calculation at each point it is used in the contract.

```solidity
Path: ./tokenomics/contracts/Dispenser.sol

1034:                olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;	// @audit-issue: `adress(this)` also used on line(s): [1031, 1028]

1112:                olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;	// @audit-issue: `adress(this)` also used on line(s): [1106, 1109]
```
[1034](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1034-L1034), [1112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1112-L1112), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

398:            revert TransferFailed(address(0), address(this), msg.sender, amount);	// @audit-issue: `adress(this)` also used on line(s): [390]

435:        if (newL2TargetDispenser == address(this)) {	// @audit-issue: `adress(this)` also used on line(s): [436, 440, 445]
```
[398](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L398-L398), [435](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L435-L435), 


#### Recommendation

To enhance gas efficiency, cache the contract's address by storing `address(this)` in a state variable at the point of contract deployment or initialization. Use this cached address throughout the contract instead of repeatedly calling `address(this)`. This practice reduces the gas cost associated with multiple computations of the contract's address, leading to more efficient contract execution, especially in scenarios with frequent usage of the contract's address.

### `msg.value` should be cached
Cacheing saves gas when compared to repeating the calculation at each point it is used in the contract.

```solidity
Path: ./tokenomics/contracts/Dispenser.sol

429:            IDepositProcessor(depositProcessor).sendMessageNonEVM{value:msg.value}(stakingTarget,	// @audit-issue: `msg.value` also used on line(s): [425]

561:        if (msg.value != totalValueAmount) {	// @audit-issue: `msg.value` also used on line(s): [562]
```
[429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L429-L429), [561](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L561-L561), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

169:            revert LowerThan(msg.value, totalCost);	// @audit-issue: `msg.value` also used on line(s): [168]
```
[169](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L169-L169), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

72:            revert LowerThan(msg.value, cost);	// @audit-issue: `msg.value` also used on line(s): [71]
```
[72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L72-L72), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

131:        if (cost > msg.value) {	// @audit-issue: `msg.value` also used on line(s): [132]
```
[131](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L131-L131), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

116:            revert LowerThan(msg.value, cost);	// @audit-issue: `msg.value` also used on line(s): [115]
```
[116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L116-L116), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

461:            revert TransferFailed(address(0), msg.sender, address(this), msg.value);	// @audit-issue: `msg.value` also used on line(s): [464]
```
[461](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L461-L461), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

41:        uint256 newBalance = balance + msg.value;	// @audit-issue: `msg.value` also used on line(s): [48, 42]
```
[41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L41-L41), 


#### Recommendation

Consider caching `msg.value` in a local variable at the start of payable functions where it is accessed multiple times. Implement this optimization by assigning `msg.value` to a local variable and using this variable for subsequent operations within the function. For example:
        ```solidity
        function myPayableFunction() public payable {
            uint256 cachedValue = msg.value;
            // Use cachedValue instead of msg.value for further operations
        }


### It is a waste of GAS to emit variable literals
Emitting variable literals (true, false, address(0), 1 etc...) in events is inefficient, as it consumes extra gas without providing added value. These literals are fixed values that can be accessed or hardcoded elsewhere in the smart contract or application, making their inclusion in events redundant and an unnecessary drain on resources during transaction execution.

```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

91:        emit MessagePosted(0, msg.sender, l1DepositProcessor, amount);	// @audit-issue
```
[91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L91-L91), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

41:        emit MessagePosted(0, msg.sender, l1DepositProcessor, amount);	// @audit-issue
```
[41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L41-L41), 


#### Recommendation

Refrain from including fixed variable literals as parameters in your Solidity contract events. Evaluate your event emissions and remove literals that do not provide dynamic information about contract state or actions. For instance, instead of emitting an event with a `true` literal to indicate success, consider naming the event in a way that inherently indicates success or failure, such as `ActionSuccessful()`:
```solidity
// Before optimization
event ActionTaken(bool success);

// After optimization
event ActionSuccessful();
event ActionFailed();
```


### `event` Declared But Not Emitted
An event within the contract is declared but not utilized in any of the contract's functions or operations. Having unused event declarations can consume unnecessary space and may lead to misunderstandings for developers or users expecting this event as part of the contract's functionality.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

259:    event EpochLengthUpdated(uint256 epochLen);	// @audit-issue
```
[259](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L259-L259), 


#### Recommendation


Consider removing the unused event declaration to optimize the contract and enhance clarity. If there is an intent for this event to be part of certain operations, ensure it is emitted appropriately. Otherwise, for the sake of clean and efficient code, it is advisable to remove any unused declarations.


### Unnecessary Event Emitting
Emitting two events in the same function with exactly the same event parameters is redundant and can lead to unnecessary gas consumption, as each `emit` operation adds to the transaction cost. These events can be merged into a single event to streamline contract operations and reduce gas usage, making the smart contract more efficient and cost-effective to interact with. This approach not only optimizes gas costs but also simplifies the contract's event logic for developers and applications integrating with the contract.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

1196:            emit IncentiveFractionsUpdated(eCounter + 1);	// @audit-issue: Exactly same parameters in event emitting also used on line(s): `[1189]`

1213:            emit StakingParamsUpdated(eCounter + 1);	// @audit-issue: Exactly same parameters in event emitting also used on line(s): `[1189, 1196]`
```
[1196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1196-L1196), [1213](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1213-L1213), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

194:                emit StakingTargetDeposited(target, amount);	// @audit-issue: Exactly same parameters in event emitting also used on line(s): `[173]`
```
[194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L194-L194), 


#### Recommendation

Your code can be optimized with changing the usage:
```solidity
emit SaleEnded(timestamp);
emit ClaimStarted(timestamp);
```
To
```solidity
emit SaleEndedAndClaimStarted(timestamp); // Declare general one and remove old ones.
```


### Avoid repeating computations
In Solidity development, repeating the same computations within a contract can lead to unnecessary gas consumption and reduce the contract's efficiency. This is particularly relevant in functions that are called frequently or involve complex calculations. Repeating computations not only wastes computational resources but also increases the cost of executing transactions. By identifying and eliminating redundant calculations, developers can optimize contract performance, reduce gas costs, and improve overall execution speed.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

464:        if (block.timestamp >= (_timeLaunch + ONE_YEAR)) {	// @audit-issue: Same binary operation statement in line(s) between: ['465:465', '469:469']

717:        if (_rewardComponentFraction + _rewardAgentFraction > 100) {	// @audit-issue: Same binary operation statement in line(s) between: ['718:718']

926:                if (incentiveFlags[unitType] || incentiveFlags[unitType + 2]) {	// @audit-issue: Same binary operation statement in line(s) between: ['952:952']

930:                    for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue: Same binary operation statement in line(s) between: ['960:960']

1132:            uint256 yearEndTime = timeLaunch + numYears * ONE_YEAR;	// @audit-issue: Same binary operation statement in line(s) between: ['1248:1248']

1136:            curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;	// @audit-issue: Same binary operation statement in line(s) between: ['1252:1252']

1171:        TokenomicsPoint storage nextEpochPoint = mapEpochTokenomics[eCounter + 1];	// @audit-issue: Same binary operation statement in line(s) between: ['1189:1189', '1196:1196', '1206:1206', '1213:1213', '1216:1216', '1217:1217', '1290:1290']

1335:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue: Same binary operation statement in line(s) between: ['1357:1357']

1407:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue: Same binary operation statement in line(s) between: ['1429:1429']
```
[464](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L464-L464), [717](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L717-L717), [926](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L926-L926), [930](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L930-L930), [1132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1132-L1132), [1136](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1136-L1136), [1171](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1171-L1171), [1335](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1335-L1335), [1407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1407-L1407), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

96:                supplyCap += (supplyCap * maxMintCapFraction) / 100;	// @audit-issue: Same binary operation statement in line(s) between: ['100:100']
```
[96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L96-L96), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

383:        if (epochAfterRemoved > 1 && firstClaimedEpoch >= epochAfterRemoved) {	// @audit-issue: Same binary operation statement in line(s) between: ['392:392']

462:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue: Same binary operation statement in line(s) between: ['473:473']

814:            if (topUp > 0) {	// @audit-issue: Same binary operation statement in line(s) between: ['821:821']

932:                    uint256 normalizedStakingAmount = stakingIncentive / (10 ** (18 - bridgingDecimals));	// @audit-issue: Same binary operation statement in line(s) between: ['933:933']

1221:            uint256 normalizedAmount = amount / (10 ** (18 - bridgingDecimals));	// @audit-issue: Same binary operation statement in line(s) between: ['1222:1222']
```
[383](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L383-L383), [462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L462-L462), [814](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L814-L814), [932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L932-L932), [1221](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1221-L1221), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

153:        if (transferAmount > 0) {	// @audit-issue: Same binary operation statement in line(s) between: ['172:172']
```
[153](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L153-L153), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

195:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyImplementation(implementation)) {	// @audit-issue: Same binary operation statement in line(s) between: ['231:231']
```
[195](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L195-L195), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

510:        if (oldSlope.end > nextTime) {	// @audit-issue: Same binary operation statement in line(s) between: ['534:534']
```
[510](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L510-L510), 


#### Recommendation

Review your Solidity contracts to identify any computations that are performed multiple times with the same inputs. Cache the results of these computations in local variables and reuse them within the function or across function calls if the state remains unchanged.

### Unused State Variables
There are state variables which are declared but not used in any function. This might increase the contract's gas usage unnecessarily.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

359:    mapping(uint256 => uint256) public mapServiceAmounts;	// @audit-issue

361:    mapping(address => uint256) public mapOwnerRewards;	// @audit-issue

363:    mapping(address => uint256) public mapOwnerTopUps;	// @audit-issue
```
[359](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L359-L359), [361](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L361-L361), [363](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L363-L363), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

10:    string public constant VERSION = "1.2.0";	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L10-L10), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

224:    string public constant VERSION = "0.2.0";	// @audit-issue
```
[224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L224-L224), 


#### Recommendation

To optimize your contract's gas usage, remove any state variables that are declared but not used in any function. Unnecessary state variables can increase gas costs and should be eliminated during code review.

### Unused `error` definition
Note that there may be cases where an error superficially appears to be used, but this is only because there are multiple definitions of the error in different files. In such cases, the error definition should be moved into a separate file. The instances below are the unused definitions.


```solidity
Path: ./tokenomics/contracts/interfaces/IErrorsTokenomics.sol

32:    error NonZeroValue();	// @audit-issue

45:    error UnauthorizedToken(address tokenAddress);	// @audit-issue

54:    error BondNotRedeemable(uint256 bondId);	// @audit-issue

61:    error ProductExpired(address tokenAddress, uint256 productId, uint256 deadline, uint256 curTime);	// @audit-issue

65:    error ProductClosed(uint256 productId);	// @audit-issue

72:    error ProductSupplyLow(address tokenAddress, uint256 productId, uint256 requested, uint256 actual);	// @audit-issue

87:    error InsufficientAllowance(uint256 provided, uint256 expected);	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L32-L32), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L45-L45), [54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L54-L54), [61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L61-L61), [65](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L65-L65), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L72-L72), [87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IErrorsTokenomics.sol#L87-L87), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

140:error ServiceNotFound(uint256 serviceId);	// @audit-issue
```
[140](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L140-L140), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

28:error NotEnoughTokenDecimals(address token, uint8 decimals);	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L28-L28), 


#### Recommendation

Unused `error` definitions should be removed from the contract, and if needed, consolidated into a separate file to avoid duplication.

### Using Prefix Operators Costs Less Gas Than Postfix Operators in Loops
Conditions can be optimized issues in Solidity refer to situations where smart contract developers write conditional statements that can be simplified or optimized for better gas efficiency, readability, and maintainability. Optimizing conditions can lead to more cost-effective and secure smart contracts.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

964:                        mapEpochTokenomics[curEpoch].unitPoints[unitType].numNewUnits++;	// @audit-issue

970:                            mapEpochTokenomics[curEpoch].epochPoint.numNewOwners++;	// @audit-issue
```
[964](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L964-L964), [970](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L970-L970), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

153:        stakingBatchNonce++;	// @audit-issue

179:        stakingBatchNonce++;	// @audit-issue
```
[153](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L153-L153), [179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L179-L179), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

459:                sCounter++;	// @audit-issue

466:            totalNumServices--;	// @audit-issue

685:                        numServices++;	// @audit-issue
```
[459](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L459-L459), [466](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L466-L466), [685](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L685-L685), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

227:        for (uint256 i = 0; i < MAX_NUM_WEEKS; i++) {	// @audit-issue

267:        for (uint256 i = 0; i < MAX_NUM_WEEKS; i++) {	// @audit-issue
```
[227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L227-L227), [267](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L267-L267), 


#### Recommendation

To improve gas efficiency in your Solidity code, prefer using prefix operators (e.g., `++i` or `--i`) instead of postfix operators (e.g., `i++` or `i--`) within loops. Prefix operators typically result in lower gas costs and can contribute to more efficient contract execution.

### Do not calculate constants
Due to how constant variables are implemented (replacements at compile-time), an expression assigned to a constant variable is recomputed each time that the variable is used, which wastes some gas.

```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

15:    uint256 public constant ONE_YEAR = 1 days * 365;	// @audit-issue

19:    uint256 public constant MAX_EPOCH_LENGTH = ONE_YEAR - 1 days;	// @audit-issue
```
[15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L15-L15), [19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L19-L19), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

275:    uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[275](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L275-L275), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

30:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L30-L30), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

64:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L64-L64), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

159:    uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L159-L159), 


#### Recommendation

Precompute and assign literal values to constant variables in your Solidity contracts. Avoid defining constants with expressions or calculations. By providing direct values, you ensure that the compiler replaces these constants efficiently in the bytecode, maximizing gas savings and contract performance. Review your contracts to identify any constants defined with expressions and refactor them to use precomputed literal values instead.

### Multiple accesses of a mapping/array should use a local variable cache
The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

872:            totalIncentives = mapUnitIncentives[unitType][unitId].topUp + totalIncentives / sumUnitIncentives;	// @audit-issue: Same index access of variable `mapUnitIncentives` is used also on line(s): ['852', '863', '856'].

854:            totalIncentives *= mapEpochTokenomics[epochNum].unitPoints[unitType].rewardUnitFraction;	// @audit-issue: Same index access of variable `mapEpochTokenomics` is used also on line(s): ['870', '871'].

854:            totalIncentives *= mapEpochTokenomics[epochNum].unitPoints[unitType].rewardUnitFraction;	// @audit-issue: Same index access of variable `mapEpochTokenomics` is used also on line(s): ['870', '869', '871'].

896:        incentiveFlags[0] = (mapEpochTokenomics[curEpoch].unitPoints[0].rewardUnitFraction > 0);	// @audit-issue: Same index access of variable `mapEpochTokenomics` is used also on line(s): ['898'].

896:        incentiveFlags[0] = (mapEpochTokenomics[curEpoch].unitPoints[0].rewardUnitFraction > 0);	// @audit-issue: Same index access of variable `mapEpochTokenomics` is used also on line(s): ['899', '898', '897'].

899:        incentiveFlags[3] = (mapEpochTokenomics[curEpoch].unitPoints[1].topUpUnitFraction > 0);	// @audit-issue: Same index access of variable `mapEpochTokenomics` is used also on line(s): ['897'].

1217:            mapEpochStakingPoints[eCounter + 1].minStakingWeight = mapEpochStakingPoints[eCounter].minStakingWeight;	// @audit-issue: Same index access of variable `mapEpochStakingPoints` is used also on line(s): ['1235', '1237', '1206', '1216'].

1370:            reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;	// @audit-issue: Same index access of variable `mapUnitIncentives` is used also on line(s): ['1373', '1359'].

1443:                totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeTopUp;	// @audit-issue: Same index access of variable `mapUnitIncentives` is used also on line(s): ['1431', '1458', '1456', '1436'].

1449:                    uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100;	// @audit-issue: Same index access of variable `mapEpochTokenomics` is used also on line(s): ['1448', '1438'].

1447:                    totalIncentives *= mapEpochTokenomics[lastEpoch].epochPoint.totalTopUpsOLAS;	// @audit-issue: Same index access of variable `mapEpochTokenomics` is used also on line(s): ['1448', '1438', '1449'].
```
[872](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L872-L872), [854](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L854-L854), [854](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L854-L854), [896](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L896-L896), [896](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L896-L896), [899](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L899-L899), [1217](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1217-L1217), [1370](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1370-L1370), [1443](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1443-L1443), [1449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1449-L1449), [1447](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1447-L1447), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

776:                    revert WrongAgentId(agentIds[i]);	// @audit-issue: Same index access of variable `agentIds` is used also on line(s): ['775'].
```
[776](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L776-L776), 


#### Recommendation

When a mapping or array value is accessed multiple times within a function, cache it in a local `storage` or `calldata` variable. This approach minimizes the gas cost by reducing the number of hash computations for mappings and offset calculations for arrays. Carefully review your functions to identify opportunities for such optimizations, especially in functions with frequent or repeated accesses to the same mapping or array element, to enhance gas efficiency in your contracts.

### State Variables That Are Used Multiple Times In a Function Should Be Cached In Stack Variables
When performing multiple operations on a state variable in a function, it is recommended to cache it first. Either multiple reads or multiple writes to a state variable can save gas by caching it on the stack. Caching of a state variable replaces each Gwarmaccess (100 gas) with a much cheaper stack read. Other less obvious fixes/optimizations include having local memory caches of state variable structs, or having local caches of state variable contracts/addresses. Saves 100 gas per instance.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

528:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['529'].

549:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['548'].

568:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['567'].

594:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['595'].

619:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['618'].

647:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['648'].

712:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['713'].

754:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['753'].

789:        if (depository != msg.sender) {	// @audit-issue: State variable `depository` is used also on line(s): ['790'].

810:        if (depository != msg.sender) {	// @audit-issue: State variable `depository` is used also on line(s): ['811'].

872:            totalIncentives = mapUnitIncentives[unitType][unitId].topUp + totalIncentives / sumUnitIncentives;	// @audit-issue: State variable `mapUnitIncentives` is used also on line(s): ['852', '863', '856'].

854:            totalIncentives *= mapEpochTokenomics[epochNum].unitPoints[unitType].rewardUnitFraction;	// @audit-issue: State variable `mapEpochTokenomics` is used also on line(s): ['870', '871'].

854:            totalIncentives *= mapEpochTokenomics[epochNum].unitPoints[unitType].rewardUnitFraction;	// @audit-issue: State variable `mapEpochTokenomics` is used also on line(s): ['870', '869', '871'].

910:                address serviceOwner = IToken(serviceRegistry).ownerOf(serviceIds[i]);	// @audit-issue: State variable `serviceRegistry` is used also on line(s): ['918'].

912:                    IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;	// @audit-issue: State variable `ve` is used also on line(s): ['911'].

912:                    IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;	// @audit-issue: State variable `veOLASThreshold` is used also on line(s): ['911'].

896:        incentiveFlags[0] = (mapEpochTokenomics[curEpoch].unitPoints[0].rewardUnitFraction > 0);	// @audit-issue: State variable `mapEpochTokenomics` is used also on line(s): ['899', '898', '897'].

896:        incentiveFlags[0] = (mapEpochTokenomics[curEpoch].unitPoints[0].rewardUnitFraction > 0);	// @audit-issue: State variable `mapEpochTokenomics` is used also on line(s): ['898'].

899:        incentiveFlags[3] = (mapEpochTokenomics[curEpoch].unitPoints[1].topUpUnitFraction > 0);	// @audit-issue: State variable `mapEpochTokenomics` is used also on line(s): ['897'].

997:        if (treasury != msg.sender) {	// @audit-issue: State variable `treasury` is used also on line(s): ['998'].

1058:            fKD = epsilonRate;	// @audit-issue: State variable `epsilonRate` is used also on line(s): ['1057'].

1106:        uint256 eCounter = epochCounter;	// @audit-issue: State variable `epochCounter` is used also on line(s): ['1097'].

1248:            uint256 yearEndTime = timeLaunch + numYears * ONE_YEAR;	// @audit-issue: State variable `timeLaunch` is used also on line(s): ['1132', '1126', '1243'].

1129:        if (numYears > currentYear) {	// @audit-issue: State variable `currentYear` is used also on line(s): ['1245'].

1174:        if (tokenomicsParametersUpdated & 0x01 == 0x01) {	// @audit-issue: State variable `tokenomicsParametersUpdated` is used also on line(s): ['1194', '1143', '1211', '1261'].

1270:        curMaxBond += effectiveBond;	// @audit-issue: State variable `effectiveBond` is used also on line(s): ['1166'].

1176:            if (nextEpochLen > 0) {	// @audit-issue: State variable `nextEpochLen` is used also on line(s): ['1177'].

1183:            if (nextVeOLASThreshold > 0) {	// @audit-issue: State variable `nextVeOLASThreshold` is used also on line(s): ['1184'].

1203:            nextEpochPoint.epochPoint.rewardTreasuryFraction = tp.epochPoint.rewardTreasuryFraction;	// @audit-issue: State variable `tp` is used also on line(s): ['1116', '1115', '1152', '1276', '1204'].

1203:            nextEpochPoint.epochPoint.rewardTreasuryFraction = tp.epochPoint.rewardTreasuryFraction;	// @audit-issue: State variable `tp` is used also on line(s): ['1116'].

1227:        incentives[6] = (inflationPerEpoch * tp.unitPoints[1].topUpUnitFraction) / 100;	// @audit-issue: State variable `tp` is used also on line(s): ['1201', '1200', '1225', '1119', '1118'].

1225:        incentives[5] = (inflationPerEpoch * tp.unitPoints[0].topUpUnitFraction) / 100;	// @audit-issue: State variable `tp` is used also on line(s): ['1118'].

1227:        incentives[6] = (inflationPerEpoch * tp.unitPoints[1].topUpUnitFraction) / 100;	// @audit-issue: State variable `tp` is used also on line(s): ['1119'].

1152:        incentives[4] = (inflationPerEpoch * tp.epochPoint.maxBondFraction) / 100;	// @audit-issue: State variable `tp` is used also on line(s): ['1204'].

1206:            mapEpochStakingPoints[eCounter + 1].stakingFraction = mapEpochStakingPoints[eCounter].stakingFraction;	// @audit-issue: State variable `mapEpochStakingPoints` is used also on line(s): ['1237'].

1200:                nextEpochPoint.unitPoints[i].topUpUnitFraction = tp.unitPoints[i].topUpUnitFraction;	// @audit-issue: State variable `tp` is used also on line(s): ['1201'].

1217:            mapEpochStakingPoints[eCounter + 1].minStakingWeight = mapEpochStakingPoints[eCounter].minStakingWeight;	// @audit-issue: State variable `mapEpochStakingPoints` is used also on line(s): ['1235', '1237', '1206', '1216'].

1256:            curMaxBond = (inflationPerEpoch * nextEpochPoint.epochPoint.maxBondFraction) / 100;	// @audit-issue: State variable `nextEpochPoint` is used also on line(s): ['1263'].

1314:        if (dispenser != msg.sender) {	// @audit-issue: State variable `dispenser` is used also on line(s): ['1315'].

1370:            reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;	// @audit-issue: State variable `mapUnitIncentives` is used also on line(s): ['1373', '1359'].

1449:                    uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100;	// @audit-issue: State variable `mapEpochTokenomics` is used also on line(s): ['1448', '1438'].

1443:                totalIncentives = mapUnitIncentives[unitTypes[i]][unitIds[i]].pendingRelativeTopUp;	// @audit-issue: State variable `mapUnitIncentives` is used also on line(s): ['1431', '1458', '1456', '1436'].

1447:                    totalIncentives *= mapEpochTokenomics[lastEpoch].epochPoint.totalTopUpsOLAS;	// @audit-issue: State variable `mapEpochTokenomics` is used also on line(s): ['1448', '1438', '1449'].
```
[528](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L528-L528), [549](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L549-L549), [568](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L568-L568), [594](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L594-L594), [619](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L619-L619), [647](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L647-L647), [712](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L712-L712), [754](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L754-L754), [789](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L789-L789), [810](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L810-L810), [872](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L872-L872), [854](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L854-L854), [854](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L854-L854), [910](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L910-L910), [912](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L912-L912), [912](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L912-L912), [896](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L896-L896), [896](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L896-L896), [899](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L899-L899), [997](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L997-L997), [1058](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1058-L1058), [1106](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1106-L1106), [1248](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1248-L1248), [1129](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1129-L1129), [1174](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1174-L1174), [1270](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1270-L1270), [1176](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1176-L1176), [1183](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1183-L1183), [1203](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1203-L1203), [1203](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1203-L1203), [1227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1227-L1227), [1225](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1225-L1225), [1227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1227-L1227), [1152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1152-L1152), [1206](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1206-L1206), [1200](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1200-L1200), [1217](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1217-L1217), [1256](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1256-L1256), [1314](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1314-L1314), [1370](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1370-L1370), [1449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1449-L1449), [1443](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1443-L1443), [1447](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1447-L1447), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

638:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['639'].

657:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['658'].

685:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['686'].

708:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['707'].

735:            revert ManagerOnly(msg.sender, voteWeighting);	// @audit-issue: State variable `voteWeighting` is used also on line(s): ['734'].

752:        if (msg.sender != voteWeighting) {	// @audit-issue: State variable `voteWeighting` is used also on line(s): ['753'].

762:        uint256 eCounter = ITokenomics(tokenomics).epochCounter();	// @audit-issue: State variable `tokenomics` is used also on line(s): ['766', '769'].

802:            ITreasury(treasury).paused() == 2) {	// @audit-issue: State variable `treasury` is used also on line(s): ['818'].

893:                IVoteWeighting(voteWeighting).nomineeRelativeWeight(stakingTarget, chainId, endTime);	// @audit-issue: State variable `voteWeighting` is used also on line(s): ['878'].

886:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue: State variable `tokenomics` is used also on line(s): ['884'].

984:            ITreasury(treasury).paused() == 2) {	// @audit-issue: State variable `treasury` is used also on line(s): ['1031'].

1109:                ITreasury(treasury).withdrawToAccount(address(this), 0, totalAmounts[1]);	// @audit-issue: State variable `treasury` is used also on line(s): ['1081'].

1151:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue: State variable `tokenomics` is used also on line(s): ['1148', '1162'].

1202:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['1201'].

1244:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['1243'].
```
[638](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L638-L638), [657](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L657-L657), [685](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L685-L685), [708](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L708-L708), [735](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L735-L735), [752](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L752-L752), [762](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L762-L762), [802](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L802-L802), [893](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L893-L893), [886](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L886-L886), [984](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L984-L984), [1109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1109-L1109), [1151](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1151-L1151), [1202](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1202-L1202), [1244](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1244-L1244), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

190:        sequence = IBridge(l1MessageRelayer).createRetryableTicket{value: cost[1]}(l2TargetDispenser, 0,	// @audit-issue: State variable `l2TargetDispenser` is used also on line(s): ['183'].
```
[190](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L190-L190), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

115:            revert WrongMessageSender(l2Dispenser, l2TargetDispenser);	// @audit-issue: State variable `l2TargetDispenser` is used also on line(s): ['114', '118'].

189:            revert OwnerOnly(owner, msg.sender);	// @audit-issue: State variable `owner` is used also on line(s): ['188'].
```
[115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L115-L115), [189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L189-L189), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

140:        IBridge(l1MessageRelayer).sendMessage{value: cost}(l2TargetDispenser, data, uint32(gasLimitMessage));	// @audit-issue: State variable `l2TargetDispenser` is used also on line(s): ['114'].
```
[140](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L140-L140), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

68:            IBridge(l1TokenRelayer).relayTokensAndCall(olas, l2TargetDispenser, transferAmount, data);	// @audit-issue: State variable `l2TargetDispenser` is used also on line(s): ['90'].
```
[68](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L68-L68), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

61:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['62'].
```
[61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L61-L61), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

250:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['249'].

314:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['313'].

356:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['355'].

366:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['367'].

385:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['386'].

420:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['421'].
```
[250](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L250-L250), [314](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L314-L314), [356](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L356-L356), [366](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L366-L366), [385](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L385-L385), [420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L420-L420), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

100:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['101'].
```
[100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L100-L100), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

99:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['98'].

115:        if (owner != msg.sender) {	// @audit-issue: State variable `owner` is used also on line(s): ['116'].

137:        if (owner != msg.sender) {	// @audit-issue: State variable `owner` is used also on line(s): ['138'].

235:        if (owner != msg.sender) {	// @audit-issue: State variable `owner` is used also on line(s): ['236'].
```
[99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L99-L99), [115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L115-L115), [137](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L137-L137), [235](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L235-L235), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

350:        minStakingDuration = _stakingParams.minNumStakingPeriods * livenessPeriod;	// @audit-issue: State variable `livenessPeriod` is used also on line(s): ['353'].

486:            revert OwnerOnly(msg.sender, sInfo.owner);	// @audit-issue: State variable `sInfo` is used also on line(s): ['485'].

735:            revert MaxNumServicesReached(maxNumServices);	// @audit-issue: State variable `maxNumServices` is used also on line(s): ['734'].

797:        IService(serviceRegistry).safeTransferFrom(msg.sender, address(this), serviceId);	// @audit-issue: State variable `serviceRegistry` is used also on line(s): ['739'].

747:        if (configHash != 0 && configHash != service.configHash) {	// @audit-issue: State variable `configHash` is used also on line(s): ['747'].

751:        if (threshold > 0 && threshold != service.threshold) {	// @audit-issue: State variable `threshold` is used also on line(s): ['751'].

776:                    revert WrongAgentId(agentIds[i]);	// @audit-issue: State variable `agentIds` is used also on line(s): ['775'].

819:            revert NotEnoughTimeStaked(serviceId, ts, minStakingDuration);	// @audit-issue: State variable `minStakingDuration` is used also on line(s): ['818'].

808:        if (msg.sender != sInfo.owner) {	// @audit-issue: State variable `sInfo` is used also on line(s): ['809'].
```
[350](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L350-L350), [486](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L486-L486), [735](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L735-L735), [797](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L797-L797), [747](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L747-L747), [751](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L751-L751), [776](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L776-L776), [819](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L819-L819), [808](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L808-L808), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

77:            IServiceTokenUtility(serviceRegistryTokenUtility).mapServiceIdTokenDeposit(serviceId);	// @audit-issue: State variable `serviceRegistryTokenUtility` is used also on line(s): ['93'].

81:            revert WrongStakingToken(stakingToken, token);	// @audit-issue: State variable `stakingToken` is used also on line(s): ['80'].
```
[77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L77-L77), [81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L81-L81), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

112:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['113'].

130:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['129'].
```
[112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L112-L112), [130](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L130-L130), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

371:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['370'].

389:            revert OwnerOnly(msg.sender, owner);	// @audit-issue: State variable `owner` is used also on line(s): ['388'].

588:        if (msg.sender != owner) {	// @audit-issue: State variable `owner` is used also on line(s): ['589'].
```
[371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L371-L371), [389](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L389-L389), [588](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L588-L588), 


#### Recommendation

Cache state variables in stack or local memory variables within functions when they are used multiple times. This approach replaces costlier Gwarmaccess operations with cheaper stack reads, saving approximately 100 gas per instance and optimizing overall contract performance.

### Divisions can be unchecked to save gas
The expression type(int).min/(-1) is the only case where division causes an overflow. Therefore, uncheck can be used to save gas in scenarios where it is certain that such an overflow will not occur.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

473:        uint256 _inflationPerSecond = getInflationForYear(0) / zeroYearSecondsLeft;	// @audit-issue

509:        uint256 _maxBond = (_inflationPerSecond * _epochLen * _maxBondFraction) / 100;	// @audit-issue

856:            totalIncentives = mapUnitIncentives[unitType][unitId].reward + totalIncentives / 100;	// @audit-issue

872:            totalIncentives = mapUnitIncentives[unitType][unitId].topUp + totalIncentives / sumUnitIncentives;	// @audit-issue

928:                    uint96 amount = uint96(amounts[i] / numServiceUnits);	// @audit-issue

1116:        incentives[1] = (incentives[0] * tp.epochPoint.rewardTreasuryFraction) / 100;	// @audit-issue

1118:        incentives[2] = (incentives[0] * tp.unitPoints[0].rewardUnitFraction) / 100;	// @audit-issue

1119:        incentives[3] = (incentives[0] * tp.unitPoints[1].rewardUnitFraction) / 100;	// @audit-issue

1126:        uint256 numYears = (block.timestamp - timeLaunch) / ONE_YEAR;	// @audit-issue

1136:            curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;	// @audit-issue

1152:        incentives[4] = (inflationPerEpoch * tp.epochPoint.maxBondFraction) / 100;	// @audit-issue

1225:        incentives[5] = (inflationPerEpoch * tp.unitPoints[0].topUpUnitFraction) / 100;	// @audit-issue

1227:        incentives[6] = (inflationPerEpoch * tp.unitPoints[1].topUpUnitFraction) / 100;	// @audit-issue

1237:        incentives[8] = incentives[7] + (inflationPerEpoch * mapEpochStakingPoints[eCounter].stakingFraction) / 100;	// @audit-issue

1243:        numYears = (block.timestamp + curEpochLen - timeLaunch) / ONE_YEAR;	// @audit-issue

1252:            curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;	// @audit-issue

1256:            curMaxBond = (inflationPerEpoch * nextEpochPoint.epochPoint.maxBondFraction) / 100;	// @audit-issue

1263:            curMaxBond = (curEpochLen * curInflationPerSecond * nextEpochPoint.epochPoint.maxBondFraction) / 100;	// @audit-issue

1440:                    reward += totalIncentives / 100;	// @audit-issue

1451:                    topUp += totalIncentives / sumUnitIncentives;	// @audit-issue
```
[473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L473-L473), [509](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L509-L509), [856](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L856-L856), [872](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L872-L872), [928](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L928-L928), [1116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1116-L1116), [1118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1118-L1118), [1119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1119-L1119), [1126](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1126-L1126), [1136](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1136-L1136), [1152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1152-L1152), [1225](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1225-L1225), [1227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1227-L1227), [1237](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1237-L1237), [1243](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1243-L1243), [1252](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1252-L1252), [1256](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1256-L1256), [1263](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1263-L1263), [1440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1440-L1440), [1451](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1451-L1451), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

59:                supplyCap += (supplyCap * maxMintCapFraction) / 100;	// @audit-issue

96:                supplyCap += (supplyCap * maxMintCapFraction) / 100;	// @audit-issue

100:            inflationAmount = (supplyCap * maxMintCapFraction) / 100;	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L59-L59), [96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L96-L96), [100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L100-L100), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

913:                returnAmount = ((stakingDiff + availableStakingAmount) * stakingWeight) / 1e18;	// @audit-issue

916:                stakingIncentive = (availableStakingAmount * stakingWeight) / 1e18;	// @audit-issue

918:                returnAmount = (stakingDiff * stakingWeight) / 1e18;	// @audit-issue

932:                    uint256 normalizedStakingAmount = stakingIncentive / (10 ** (18 - bridgingDecimals));	// @audit-issue

1221:            uint256 normalizedAmount = amount / (10 ** (18 - bridgingDecimals));	// @audit-issue
```
[913](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L913-L913), [916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L916-L916), [918](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L918-L918), [932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L932-L932), [1221](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1221-L1221), 


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

Utilize 'unchecked' blocks in Solidity for divisions where overflow is impossible, such as when 'type(int).min/(-1)' is not a concern. This can save gas by bypassing overflow checks in these specific cases.

### Add `unchecked {}` for subtractions where the operands cannot underflow because of a previous `require()` or `if`-statement
Unchecked keyword can be added to such scenerios: 
`require(a <= b); x = b - a` => `require(a <= b); unchecked { x = b - a }`

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

798:            eBond -= amount;	// @audit-issue
```
[798](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L798-L798), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

384:            revert Overflow(firstClaimedEpoch, epochAfterRemoved - 1);	// @audit-issue

619:                        withheldAmount -= transferAmounts[i];	// @audit-issue

622:                        transferAmounts[i] -= withheldAmount;	// @audit-issue

905:                stakingDiff = availableStakingAmount - totalWeightSum;	// @audit-issue

924:                    returnAmount += stakingIncentive - availableStakingAmount;	// @audit-issue

932:                    uint256 normalizedStakingAmount = stakingIncentive / (10 ** (18 - bridgingDecimals));	// @audit-issue

933:                    normalizedStakingAmount *= 10 ** (18 - bridgingDecimals);	// @audit-issue

1016:                    withheldAmount -= transferAmount;	// @audit-issue

1020:                    transferAmount -= withheldAmount;	// @audit-issue

1221:            uint256 normalizedAmount = amount / (10 ** (18 - bridgingDecimals));	// @audit-issue

1222:            normalizedAmount *= 10 ** (18 - bridgingDecimals);	// @audit-issue
```
[384](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L384-L384), [619](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L619-L619), [622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L622-L622), [905](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L905-L905), [924](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L924-L924), [932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L932-L932), [933](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L933-L933), [1016](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1016-L1016), [1020](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1020-L1020), [1221](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1221-L1221), [1222](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1222-L1222), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

108:                uint256 refundAmount = amount - limitAmount;	// @audit-issue
```
[108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L108-L108), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

181:                uint256 targetWithheldAmount = amount - limitAmount;	// @audit-issue
```
[181](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L181-L181), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

58:            uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts;	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L58-L58), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

639:                    updatedReward += lastAvailableRewards - updatedTotalRewards;	// @audit-issue

657:                lastAvailableRewards -= totalRewards;	// @audit-issue
```
[639](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L639-L639), [657](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L657-L657), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

234:                pt.bias -= dBias;	// @audit-issue

274:                pt.bias -= dBias;	// @audit-issue

511:            oldBias = oldSlope.slope * (oldSlope.end - nextTime);	// @audit-issue

520:        uint256 newBias = newSlope.slope * (lockEnd - nextTime);	// @audit-issue
```
[234](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L234-L234), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L274-L274), [511](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L511-L511), [520](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L520-L520), 


#### Recommendation

In scenarios where subtraction cannot result in underflow due to prior `require()` or `if`-statements, wrap these operations in an `unchecked` block to save gas. This optimization should only be applied when the safety of the operation is assured. Carefully analyze each case to confirm that underflow is impossible before implementing `unchecked` blocks, as incorrect usage can lead to vulnerabilities in the contract.

### Stack variable is only used once
If the variable is only accessed once, it's cheaper to use the assigned value directly that one time, and save the 3 gas the extra stack assignment would spend

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

469:        uint256 zeroYearSecondsLeft = uint32(_timeLaunch + ONE_YEAR - block.timestamp);	// @audit-issue: zeroYearSecondsLeft used only on line: 473

871:            uint256 sumUnitIncentives = uint256(mapEpochTokenomics[epochNum].unitPoints[unitType].sumUnitTopUpsOLAS) * 100;	// @audit-issue: sumUnitIncentives used only on line: 872

1041:        UD60x18 codeUnits = UD60x18.wrap(codePerDev);	// @audit-issue: codeUnits used only on line: 1050

1046:        UD60x18 fpDevsPerCapital = UD60x18.wrap(devsPerCapital);	// @audit-issue: fpDevsPerCapital used only on line: 1047

1048:        UD60x18 fpNumNewOwners = convert(numNewOwners);	// @audit-issue: fpNumNewOwners used only on line: 1049
```
[469](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L469-L469), [871](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L871-L871), [1041](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1041-L1041), [1046](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1046-L1046), [1048](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1048-L1048), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

36:            uint96[10] memory supplyCaps = [	// @audit-issue: supplyCaps used only on line: 48
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

73:            uint88[10] memory inflationAmounts = [	// @audit-issue: inflationAmounts used only on line: 85
74:                3_159_000e18,
75:                40_254_084e18,
76:                40_400_000e18,
77:                56_000_000e18,
78:                80_000_000e18,
79:                72_000_000e18,
80:                64_000_000e18,
81:                48_000_000e18,
82:                40_000_000e18,
83:                29_686_916e18
84:            ];
```
[36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L36-L47), [73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L73-L84), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

424:            address stakingTargetEVM = address(uint160(uint256(stakingTarget)));	// @audit-issue: stakingTargetEVM used only on line: 425

766:        uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(eCounter - 1);	// @audit-issue: endTime used only on line: 773

769:        uint256 epochLen = ITokenomics(tokenomics).epochLen();	// @audit-issue: epochLen used only on line: 773

989:        address depositProcessor = mapChainIdDepositProcessors[chainId];	// @audit-issue: depositProcessor used only on line: 990

990:        uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();	// @audit-issue: bridgingDecimals used only on line: 994

993:        (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =	// @audit-issue: lastClaimedEpoch used only on line: 997
994:            calculateStakingIncentives(numClaimedEpochs, chainId, stakingTarget, bridgingDecimals);

993:        (uint256 stakingIncentive, uint256 returnAmount, uint256 lastClaimedEpoch, bytes32 nomineeHash) =	// @audit-issue: nomineeHash used only on line: 997
994:            calculateStakingIncentives(numClaimedEpochs, chainId, stakingTarget, bridgingDecimals);

1216:        address depositProcessor = mapChainIdDepositProcessors[chainId];	// @audit-issue: depositProcessor used only on line: 1217
```
[424](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L424-L424), [766](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L766-L766), [769](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L769-L769), [989](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L989-L989), [990](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L990-L990), [993](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L993-L994), [993](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L993-L994), [1216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1216-L1216), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

179:            bytes memory submissionCostData = abi.encode(maxSubmissionCostToken, "");	// @audit-issue: submissionCostData used only on line: 183

187:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));	// @audit-issue: data used only on line: 191

203:        address l2Dispenser = IBridge(outbox).l2ToL1Sender();	// @audit-issue: l2Dispenser used only on line: 206
```
[179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L179-L179), [187](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L187-L187), [203](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L203-L203), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

85:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));	// @audit-issue: data used only on line: 89

98:        address l1Processor = IBridge(l2MessageRelayer).xDomainMessageSender();	// @audit-issue: l1Processor used only on line: 101
```
[85](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L85-L85), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L98-L98), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

121:        (uint256 amount) = abi.decode(data, (uint256));	// @audit-issue: amount used only on line: 124

150:        uint256 sequence = _sendMessage(targets, stakingIncentives, bridgePayload, transferAmount);	// @audit-issue: sequence used only on line: 155

176:        uint256 sequence = _sendMessage(targets, stakingIncentives, bridgePayload, transferAmount);	// @audit-issue: sequence used only on line: 181
```
[121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L121-L121), [150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L150-L150), [176](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L176-L176), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

46:        uint160 offset = uint160(0x1111000000000000000000000000000000001111);	// @audit-issue: offset used only on line: 48

55:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));	// @audit-issue: data used only on line: 58

58:        uint256 sequence = IBridge(l2MessageRelayer).sendTxToL1(l1DepositProcessor, data);	// @audit-issue: sequence used only on line: 60
```
[46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L46-L46), [55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L55-L55), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L58-L58), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

136:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));	// @audit-issue: data used only on line: 140

150:        address l2Dispenser = IBridge(l1MessageRelayer).xDomainMessageSender();	// @audit-issue: l2Dispenser used only on line: 153
```
[136](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L136-L136), [150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L150-L150), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

120:        uint64 sequence = IBridge(l2MessageRelayer).sendPayloadToEvm{value: cost}(uint16(l1SourceChainId),	// @audit-issue: sequence used only on line: 123
121:            l1DepositProcessor, abi.encode(amount), 0, gasLimitMessage, uint16(l1SourceChainId), refundAccount);

164:        address processor = address(uint160(uint256(sourceProcessor)));	// @audit-issue: processor used only on line: 167
```
[120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L120-L121), [164](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L164-L164), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

90:            bytes32 iMsg = IBridge(l1MessageRelayer).requireToPassMessage(l2TargetDispenser, data, gasLimitMessage);	// @audit-issue: iMsg used only on line: 92

100:        address l2Dispenser = IBridge(l1MessageRelayer).messageSender();	// @audit-issue: l2Dispenser used only on line: 103
```
[90](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L90-L90), [100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L100-L100), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

34:        bytes memory data = abi.encode(amount);	// @audit-issue: data used only on line: 39
```
[34](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L34-L34), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

89:        bytes memory data = abi.encode(targets, stakingIncentives);	// @audit-issue: data used only on line: 96

125:        address l2Dispenser = address(uint160(uint256(sourceAddress)));	// @audit-issue: l2Dispenser used only on line: 127
```
[89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L89-L89), [125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L125-L125), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

77:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));	// @audit-issue: data used only on line: 80

80:        bytes32 iMsg = IBridge(l2MessageRelayer).requireToPassMessage(l1DepositProcessor, data, gasLimitMessage);	// @audit-issue: iMsg used only on line: 82

89:        address processor = IBridge(l2MessageRelayer).messageSender();	// @audit-issue: processor used only on line: 92
```
[77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L77-L77), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L80-L80), [89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L89-L89), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

280:        bool queued = stakingQueueingNonces[queueHash];	// @audit-issue: queued used only on line: 282

396:        (bool success, ) = msg.sender.call{value: amount}("");	// @audit-issue: success used only on line: 397

443:            bool success = IToken(olas).transfer(newL2TargetDispenser, amount);	// @audit-issue: success used only on line: 444
```
[280](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L280-L280), [396](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L396-L396), [443](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L443-L443), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

75:        bytes memory data = abi.encode(targets, stakingIncentives);	// @audit-issue: data used only on line: 80
```
[75](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L75-L75), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

192:        uint256 rewardsPerSecond = IStaking(instance).rewardsPerSecond();	// @audit-issue: rewardsPerSecond used only on line: 193

199:        uint256 numServices = IStaking(instance).maxNumServices();	// @audit-issue: numServices used only on line: 200

206:        bytes memory tokenData = abi.encodeCall(IStaking.stakingToken, ());	// @audit-issue: tokenData used only on line: 207

207:        (bool success, bytes memory returnData) = instance.staticcall(tokenData);	// @audit-issue: success used only on line: 210

213:                address token = abi.decode(returnData, (address));	// @audit-issue: token used only on line: 214
```
[192](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L192-L192), [199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L199-L199), [206](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L206-L206), [207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L207-L207), [213](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L213-L213), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

33:        (bool success, ) = to.call{value: amount}("");	// @audit-issue: success used only on line: 34
```
[33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L33-L33), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

58:            uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts;	// @audit-issue: ratio used only on line: 59
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L58-L58), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

701:            uint256 epochLength = block.timestamp - tsCheckpoint;	// @audit-issue: epochLength used only on line: 709

733:        uint256 numStakingServices = setServiceIds.length;	// @audit-issue: numStakingServices used only on line: 734

760:        bytes32 multisigProxyHash = keccak256(service.multisig.code);	// @audit-issue: multisigProxyHash used only on line: 761

814:        uint256 tsStart = sInfo.tsStart;	// @audit-issue: tsStart used only on line: 817

848:        uint256[] memory nonces = sInfo.nonces;	// @audit-issue: nonces used only on line: 871

918:        ServiceInfo memory sInfo = mapServiceInfo[serviceId];	// @audit-issue: sInfo used only on line: 919
```
[701](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L701-L701), [733](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L733-L733), [760](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L760-L760), [814](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L814-L814), [848](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L848-L848), [918](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L918-L918), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

143:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue: salt used only on line: 152

146:        bytes memory deploymentData = abi.encodePacked(type(StakingProxy).creationCode,	// @audit-issue: deploymentData used only on line: 152
147:            uint256(uint160(implementation)));

150:        bytes32 hash = keccak256(	// @audit-issue: hash used only on line: 156
151:            abi.encodePacked(
152:                bytes1(0xff), address(this), salt, keccak256(deploymentData)
153:            )
154:        );

296:        bool success = verifyInstance(instance);	// @audit-issue: success used only on line: 298
```
[143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L143-L143), [146](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L146-L147), [150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L150-L154), [296](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L296-L296), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

256:        Nominee memory nominee = Nominee(account, chainId);	// @audit-issue: nominee used only on line: 259

309:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;	// @audit-issue: nextTime used only on line: 310

340:        Nominee memory nominee = Nominee(bytes32(uint256(uint160(account))), chainId);	// @audit-issue: nominee used only on line: 343

360:        Nominee memory nominee = Nominee(account, chainId);	// @audit-issue: nominee used only on line: 363

398:        uint256 totalSum = _getSum();	// @audit-issue: totalSum used only on line: 400

407:        uint256 nomineeWeight = _getWeight(account, chainId);	// @audit-issue: nomineeWeight used only on line: 410

408:        uint256 totalSum = _getSum();	// @audit-issue: totalSum used only on line: 410

427:        Nominee memory nominee = Nominee(account, chainId);	// @audit-issue: nominee used only on line: 428

428:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue: nomineeHash used only on line: 431

431:            uint256 nomineeWeight = pointsWeight[nomineeHash][t].bias;	// @audit-issue: nomineeWeight used only on line: 432

462:        uint256 nomineeWeight = _getWeight(account, chainId);	// @audit-issue: nomineeWeight used only on line: 466

603:        uint256 oldWeight = _getWeight(account, chainId);	// @audit-issue: oldWeight used only on line: 610

604:        uint256 oldSum = _getSum();	// @audit-issue: oldSum used only on line: 610

623:        bytes32 replacedNomineeHash = keccak256(abi.encode(nominee));	// @audit-issue: replacedNomineeHash used only on line: 624

643:        Nominee memory nominee = Nominee(account, chainId);	// @audit-issue: nominee used only on line: 644

676:        Nominee memory nominee = Nominee(account, chainId);	// @audit-issue: nominee used only on line: 677

722:        Nominee memory nominee = Nominee(account, chainId);	// @audit-issue: nominee used only on line: 723

723:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue: nomineeHash used only on line: 725

734:        Nominee memory nominee = Nominee(account, chainId);	// @audit-issue: nominee used only on line: 735

735:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue: nomineeHash used only on line: 737
```
[256](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L256-L256), [309](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L309-L309), [340](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L340-L340), [360](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L360-L360), [398](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L398-L398), [407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L407-L407), [408](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L408-L408), [427](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L427-L427), [428](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L428-L428), [431](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L431-L431), [462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L462-L462), [603](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L603-L603), [604](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L604-L604), [623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L623-L623), [643](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L643-L643), [676](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L676-L676), [722](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L722-L722), [723](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L723-L723), [734](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L734-L734), [735](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L735-L735), 


#### Recommendation

Eliminate single-use stack variables in Solidity to optimize gas consumption. Directly use the assigned value in the place of the variable. This approach saves the 3 gas typically used for the extra stack assignment, streamlining the function's execution and enhancing overall gas efficiency.

### Avoid Using State Variables Directly in `emit` for Gas Efficiency
In Solidity, emitting events is a common way to log contract activity and changes, especially for off-chain monitoring and interfacing. However, using state variables directly in `emit` statements can lead to increased gas costs. Each access to a state variable incurs gas due to storage reading operations. When these variables are used directly in `emit` statements, especially within functions that perform multiple operations, the cumulative gas cost can become significant. Instead, caching state variables in memory and using these local copies in `emit` statements can optimize gas usage.


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

118:        emit MessageReceived(l2TargetDispenser, l2TargetChainId, data);	// @audit-issue: `l2TargetDispenser` is a state variable and used on line(s): ['115', '114']
```
[118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L118-L118), 


#### Recommendation


To optimize gas efficiency, cache state variables in memory when they are used multiple times within a function, including in `emit` statements.

### `x += y` costs less gas than `x = x + y` for state variables
In Solidity, optimizing gas is vital for efficient smart contracts. The compound assignment `x += y` has been proven to consume less gas compared to `x = x + y` when updating state variables. This approach is not only gas-efficient but also aligns with best coding practices, offering a cleaner, more readable code. It is also valid for operations including: `-`, `/`, `*` , `&`, `|` , `^`, `%`

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

691:        tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x01;	// @audit-issue: `tokenomicsParametersUpdated` can be assigned as `tokenomicsParametersUpdated |= ..`

743:        tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x02;	// @audit-issue: `tokenomicsParametersUpdated` can be assigned as `tokenomicsParametersUpdated |= ..`

778:        tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x08;	// @audit-issue: `tokenomicsParametersUpdated` can be assigned as `tokenomicsParametersUpdated |= ..`

1143:            tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x04;	// @audit-issue: `tokenomicsParametersUpdated` can be assigned as `tokenomicsParametersUpdated |= ..`
```
[691](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L691-L691), [743](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L743-L743), [778](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L778-L778), [1143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1143-L1143), 


#### Recommendation

Use Compound Assignments: Adopt `x += y` for concise, gas-efficient, and easy-to-read code. This approach is recognized as a best practice in Solidity development.

### Don't emit events inside a loop
Emitting an event has an overhead of 375 gas, which will be incurred on every iteration of the loop. It is cheaper to emit only once after the loop has finished.

```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

114:                emit AmountRefunded(target, refundAmount);	// @audit-issue

121:            emit StakingTargetDeposited(target, amount);	// @audit-issue
```
[114](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L114-L114), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L121-L121), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

173:                emit AmountWithheld(target, amount);	// @audit-issue

185:                emit AmountWithheld(target, targetWithheldAmount);	// @audit-issue

194:                emit StakingTargetDeposited(target, amount);	// @audit-issue

201:                emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);	// @audit-issue
```
[173](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L173-L173), [185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L185-L185), [194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L194-L194), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L201-L201), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

687:                        emit ServiceInactivityWarning(eCounter, curServiceId, serviceInactivity[i]);	// @audit-issue

708:            emit Checkpoint(eCounter, lastAvailableRewards, finalEligibleServiceIds, finalEligibleServiceRewards,	// @audit-issue
```
[687](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L687-L687), [708](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L708-L708), 


#### Recommendation

To optimize gas usage, avoid emitting events inside loops in Solidity. Instead, emit a single event after the loop completes, thereby saving the 375 gas overhead incurred on each iteration.

### `Internal` functions only called once can be inlined to save gas
If an internal function is only used once, there is no need to modularize it, unless the function calling it would otherwise be too long and complex. Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

884:    function _trackServiceDonations(	// @audit-issue

1033:    function _calculateIDF(uint256 treasuryRewards, uint256 numNewOwners) internal view returns (uint256 idf) {	// @audit-issue
```
[884](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L884-L884), [1033](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1033-L1033), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

408:    function _distributeStakingIncentives(	// @audit-issue

441:    function _distributeStakingIncentivesBatch(	// @audit-issue

506:    function _checkOrderAndValues(	// @audit-issue

573:    function _calculateStakingIncentivesBatch(	// @audit-issue
```
[408](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L408-L408), [441](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L441-L441), [506](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L506-L506), [573](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L573-L573), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

218:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal virtual;	// @audit-issue
```
[218](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L218-L218), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

22:    function safeTransferFrom(address token, address from, address to, uint256 amount) internal {	// @audit-issue

64:    function safeTransfer(address token, address to, uint256 amount) internal {	// @audit-issue
```
[22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L22-L22), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L64-L64), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

366:    function _checkTokenStakingDeposit(	// @audit-issue

398:    function _checkRatioPass(	// @audit-issue

430:    function _evict(	// @audit-issue
```
[366](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L366-L366), [398](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L398-L398), [430](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L430-L430), 


#### Recommendation

Inline 'internal' functions in Solidity that are called only once to save gas. This avoids the additional gas cost of 20 to 40 units associated with extra JUMP instructions and stack operations required for separate function calls, unless the calling function becomes too complex.

### Optimizing Arithmetic with Shift Operators for Division and Multiplication by Powers of Two
In computational operations, especially within contexts where efficiency matters, certain arithmetic operations can be optimized. One such optimization is leveraging shift operators for division and multiplication by powers of two. This stems from the binary nature of numbers in computing, where shifting bits to the left (using `<<`) effectively multiplies a number by 2 for each position shifted, and shifting bits to the right (using `>>`) divides the number by 2 for each position shifted.

In Solidity, and many other programming languages, using shift operators can be more gas-efficient than traditional arithmetic operations for these specific cases. For instance, instead of performing `value * 4`, one can use `value << 2`, and instead of `value / 4`, one can use `value >> 2`. These optimizations can result in reduced gas costs and faster execution times.


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

275:    uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[275](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L275-L275), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

30:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L30-L30), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

64:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L64-L64), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

159:    uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L159-L159), 


#### Recommendation

When performing division or multiplication by powers of two in Solidity, consider using shift operators (`<<` for multiplication and `>>` for division) for improved gas efficiency. This optimization takes advantage of the binary nature of computing and can lead to reduced gas costs and faster execution times.

### Redundant State Variable Getters in Solidity
In Solidity, state variables can have different visibility levels, including `public`. When a state variable is declared as `public`, the Solidity compiler automatically generates a getter function for it. This implicit getter has the same name as the state variable and allows external callers to query the variable's value.

A common oversight is the explicit creation of a function that returns the value of a public state variable. This function essentially duplicates the functionality already provided by the automatically generated getter. For instance, if there's a public state variable `uint256 public value;`, there's no need for a function like `function getValue() public view returns (uint256) { return value; }`, as the compiler already provides a `value()` function.



```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

255:    function getEmissionsAmountLimit(address) external view returns (uint256) {	// @audit-issue
256:        return emissionsLimit;
257:    }
```
[255](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L255-L257), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

953:    function getServiceIds() public view returns (uint256[] memory) {	// @audit-issue
954:        return setServiceIds;
955:    }

959:    function getAgentIds() external view returns (uint256[] memory) {	// @audit-issue
960:        return agentIds;
961:    }
```
[953](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L953-L955), [959](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L959-L961), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

705:    function getAllNominees() external view returns (Nominee[] memory) {	// @audit-issue
706:        return setNominees;
707:    }

712:    function getAllRemovedNominees() external view returns (Nominee[] memory) {	// @audit-issue
713:        return setRemovedNominees;
714:    }
```
[705](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L705-L707), [712](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L712-L714), 


#### Recommendation

Avoid creating explicit getter functions for 'public' state variables in Solidity. The compiler automatically generates getters for such variables, making additional functions redundant. This practice helps reduce contract size, lowers deployment costs, and simplifies maintenance and understanding of the contract.

### Multiple Pragma Definition
In Solidity, pragma statements are used to specify the compiler version and settings for a smart contract. This issue occurs when multiple pragma statements are defined within the same contract or file. Each pragma statement should be unique within a contract or file, as it sets the compiler version and potentially other compiler-specific configurations.


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

Ensure each Solidity contract or file contains a single, unique pragma statement to clearly specify the intended compiler version and settings. Avoid multiple pragma definitions to prevent confusion and potential compilation issues.

### Use `private` Rather than `public` for Constants 
In Solidity, constants represent immutable values that cannot be changed after they are set at compile-time. By default, constants have internal visibility, meaning they can be accessed within the contract they are declared in and in derived contracts. If a constant is explicitly declared as `public`, Solidity automatically generates a getter function for it. While this might seem harmless, it actually incurs a gas overhead, especially when the contract is deployed, as the EVM needs to generate bytecode for that getter. Conversely, declaring constants as `private` ensures that no additional getter is generated, optimizing gas usage.

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
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

39:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue
```
[39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L39-L39), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

28:    bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));	// @audit-issue

30:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue

33:    uint256 public constant TOKEN_GAS_LIMIT = 300_000;	// @audit-issue

35:    uint256 public constant MESSAGE_GAS_LIMIT = 2_000_000;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L30-L30), [33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L33-L33), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L35-L35), 


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
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

62:    bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));	// @audit-issue

64:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue

67:    uint256 public constant GAS_LIMIT = 300_000;	// @audit-issue

70:    uint256 public constant MAX_GAS_LIMIT = 2_000_000;	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L62-L62), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L67-L67), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L70-L70), 


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

To optimize gas usage in your Solidity contracts, declare constants with `private` visibility rather than `public` when possible. Using `private` prevents the automatic generation of a getter function, reducing gas overhead, especially during contract deployment.

### Use `Array.unsafeAccess()` to avoid repeated array length checks
When using storage arrays, solidity adds an internal lookup of the array's length (a Gcoldsload **2100 gas**) to ensure you don't read past the array's end. You can avoid this lookup by using [`Array.unsafeAccess()`](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/94697be8a3f0dfcd95dfb13ffbd39b5973f5c65d/contracts/utils/Arrays.sol#L57) in cases where the length has already been checked, as is the case with the instances below

```solidity
Path: ./registries/contracts/staking/StakingBase.sol

550:                serviceIds[i] = setServiceIds[i];	// ('@audit-issue: Length of `setServiceIds` already checked. ',)

775:                if (agentIds[i] != service.agentIds[i]) {	// ('@audit-issue: Length of `agentIds` already checked. ',)

776:                    revert WrongAgentId(agentIds[i]);	// ('@audit-issue: Length of `agentIds` already checked. ',)

858:            setServiceIds[idx] = setServiceIds[setServiceIds.length - 1];	// ('@audit-issue: Length of `setServiceIds` already checked. ',)
```
[550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L550-L550), [775](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L775-L775), [776](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L776-L776), [858](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L858-L858), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

622:        nominee = setNominees[setNominees.length - 1];	// ('@audit-issue: Length of `setNominees` already checked. ',)

625:        setNominees[id] = nominee;	// ('@audit-issue: Length of `setNominees` already checked. ',)

754:        return setNominees[id];	// ('@audit-issue: Length of `setNominees` already checked. ',)

771:        return setRemovedNominees[id];	// ('@audit-issue: Length of `setRemovedNominees` already checked. ',)
```
[622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L622-L622), [625](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L625-L625), [754](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L754-L754), [771](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L771-L771), 


#### Recommendation

Leverage `Array.unsafeAccess()` from OpenZeppelin's `Arrays` library for storage array accesses where the index is already known to be within bounds. Ensure that you have thoroughly checked the array length prior to using this method to avoid any risk of out-of-bounds errors. This approach is particularly beneficial in performance-critical sections of your code, such as loops over storage arrays where the length check is redundant. Incorporating `Array.unsafeAccess()` judiciously can lead to significant gas savings, making your contract more efficient. Always balance the use of such optimizations with the need for code safety and clarity.

### Consider pre-calculating the address of `address(this)` to save gas
Use `foundry`'s [`script.sol`](https://book.getfoundry.sh/reference/forge-std/compute-create-address) or `solady`'s [`LibRlp.sol`](https://github.com/Vectorized/solady/blob/main/src/utils/LibRLP.sol) to save the value in a constant, which will avoid having to spend gas to push the value on the stack every time it's used.

```solidity
Path: ./tokenomics/contracts/Dispenser.sol

1028:                uint256 olasBalance = IToken(olas).balanceOf(address(this));	// @audit-issue

1031:                ITreasury(treasury).withdrawToAccount(address(this), 0, transferAmount);	// @audit-issue

1034:                olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;	// @audit-issue

1106:                uint256 olasBalance = IToken(olas).balanceOf(address(this));	// @audit-issue

1109:                ITreasury(treasury).withdrawToAccount(address(this), 0, totalAmounts[1]);	// @audit-issue

1112:                olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;	// @audit-issue
```
[1028](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1028-L1028), [1031](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1031-L1031), [1034](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1034-L1034), [1106](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1106-L1106), [1109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1109-L1109), [1112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1112-L1112), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {	// @audit-issue

287:        uint256 olasBalance = IToken(olas).balanceOf(address(this));	// @audit-issue

390:        amount = address(this).balance;	// @audit-issue

398:            revert TransferFailed(address(0), address(this), msg.sender, amount);	// @audit-issue

435:        if (newL2TargetDispenser == address(this)) {	// @audit-issue

436:            revert WrongAccount(address(this));	// @audit-issue

440:        uint256 amount = IToken(olas).balanceOf(address(this));	// @audit-issue

445:                revert TransferFailed(olas, address(this), newL2TargetDispenser, amount);	// @audit-issue

461:            revert TransferFailed(address(0), msg.sender, address(this), msg.value);	// @audit-issue
```
[189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L189-L189), [287](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L287-L287), [390](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L390-L390), [398](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L398-L398), [435](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L435-L435), [436](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L436-L436), [440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L440-L440), [445](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L445-L445), [461](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L461-L461), 


```solidity
Path: ./registries/contracts/utils/SafeTransferLib.sol

92:            revert TokenTransferFailed(token, address(this), to, amount);	// @audit-issue
```
[92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/utils/SafeTransferLib.sol#L92-L92), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

35:            revert TransferFailed(address(0), address(this), to, amount);	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L35-L35), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

797:        IService(serviceRegistry).safeTransferFrom(msg.sender, address(this), serviceId);	// @audit-issue

864:        IService(serviceRegistry).safeTransferFrom(address(this), msg.sender, serviceId);	// @audit-issue
```
[797](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L797-L797), [864](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L864-L864), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

125:        SafeTransferLib.safeTransferFrom(stakingToken, msg.sender, address(this), amount);	// @audit-issue
```
[125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L125-L125), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

152:                bytes1(0xff), address(this), salt, keccak256(deploymentData)	// @audit-issue
```
[152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L152-L152), 


#### Recommendation

To enhance gas efficiency, cache the contract's address by storing `address(this)` in a state variable at the point of contract deployment or initialization. Use this cached address throughout the contract instead of repeatedly calling `address(this)`. This practice reduces the gas cost associated with multiple computations of the contract's address, leading to more efficient contract execution, especially in scenarios with frequent usage of the contract's address.

### All variables can be packed into fewer storage slots by truncating timestamp bytes
By using a `uint32` rather than a larger type for variables that track timestamps, one can save gas by using fewer storage slots per struct, at the expense of the protocol breaking after the year 2106 (when `uint32` wraps). If this is an acceptable tradeoff, if variables occupying the same slot are both written the same function or by the constructor, avoids a separate Gsset (**20000 gas**). Reads of the variables can also be cheaper

```solidity
Path: ./tokenomics/contracts/Dispenser.sol

766:        uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(eCounter - 1);	// @audit-issue

886:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue

1151:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue
```
[766](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L766-L766), [886](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L886-L886), [1151](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1151-L1151), 


#### Recommendation

Evaluate the use of `uint32` for timestamp variables in your Solidity contracts, especially within structs that are frequently written to or read from storage. Ensure that the reduced size will not impact the functionality of your contract, particularly considering the 2106 overflow limitation. When implementing this optimization, aim to group such variables together in the same storage slot to maximize gas savings. This strategy is most effective when these variables are updated together, as in a constructor or specific functions, allowing for a single storage operation (`Gsset`) instead of multiple. Always balance the benefits of gas savings against the longevity and future-proofing of your contract.

### Counting down in for statements is more gas efficient
Looping downwards in Solidity is more gas efficient due to how the EVM compares variables. In a 'for' loop that counts down, the end condition is usually to compare with zero, which is cheaper than comparing with another number. As such, restructure your loops to count downwards where possible.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

904:        for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue

916:            for (uint256 unitType = 0; unitType < 2; ++unitType) {	// @audit-issue

930:                    for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue

960:                for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue

1010:        for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue

1199:            for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1329:        for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1335:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1357:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1401:        for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1407:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1429:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue
```
[904](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L904-L904), [916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L916-L916), [930](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L930-L930), [960](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L960-L960), [1010](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1010-L1010), [1199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1199-L1199), [1329](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1329-L1329), [1335](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1335-L1335), [1357](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1357-L1357), [1401](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1401-L1401), [1407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1407-L1407), [1429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1429-L1429), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

58:            for (uint256 i = 0; i < numYears; ++i) {	// @audit-issue

95:            for (uint256 i = 1; i < numYears; ++i) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L58-L58), [95](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L95-L95), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

450:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

462:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

473:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

485:                for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {	// @audit-issue

526:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

550:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

593:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

600:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

717:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

881:        for (uint256 j = firstClaimedEpoch; j < lastClaimedEpoch; ++j) {	// @audit-issue

1146:        for (uint256 j = firstClaimedEpoch; j < lastClaimedEpoch; ++j) {	// @audit-issue
```
[450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L450-L450), [462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L462-L462), [473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L473-L473), [485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L485-L485), [526](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L526-L526), [550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L550-L550), [593](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L593-L593), [600](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L600-L600), [717](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L717-L717), [881](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L881-L881), [1146](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1146-L1146), 


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

620:                for (uint256 i = 1; i < numServices; ++i) {	// @audit-issue

648:                for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue

669:            for (uint256 i = 0; i < serviceIds.length; ++i) {	// @audit-issue

773:            for (uint256 i = 0; i < numAgents; ++i) {	// @audit-issue

835:        for (; idx < serviceIds.length; ++idx) {	// @audit-issue

899:        for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue
```
[332](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L332-L332), [380](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L380-L380), [449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L449-L449), [548](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L548-L548), [620](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L620-L620), [648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L648-L648), [669](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L669-L669), [773](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L773-L773), [835](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L835-L835), [899](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L899-L899), 


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

Where feasible, refactor `for` loops in your Solidity contracts to count downwards. Adjust the loop initialization, condition, and iteration statements to decrement the loop variable and terminate the loop when it reaches zero. This approach can lead to gas savings, making your contract more efficient in terms of execution costs. Ensure that this refactoring aligns with the logic and requirements of your contract, and thoroughly test to confirm that the revised loop behavior matches the intended functionality.

### Consider using solady's 'FixedPointMathLib'
Using Solady's "FixedPointMathLib" for multiplication or division operations in Solidity can lead to significant gas savings. This library is designed to optimize fixed-point arithmetic operations, which are common in financial calculations involving tokens or currencies. By implementing more efficient algorithms and assembly optimizations, "FixedPointMathLib" minimizes the computational resources required for these operations. For contracts that frequently perform such calculations, integrating this library can reduce transaction costs, thereby enhancing overall performance and cost-effectiveness. However, developers must ensure compatibility with their existing codebase and thoroughly test for accuracy and expected behavior to avoid any unintended consequences.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

473:        uint256 _inflationPerSecond = getInflationForYear(0) / zeroYearSecondsLeft;	// @audit-issue

509:        uint256 _maxBond = (_inflationPerSecond * _epochLen * _maxBondFraction) / 100;	// @audit-issue

854:            totalIncentives *= mapEpochTokenomics[epochNum].unitPoints[unitType].rewardUnitFraction;	// @audit-issue

856:            totalIncentives = mapUnitIncentives[unitType][unitId].reward + totalIncentives / 100;	// @audit-issue

869:            totalIncentives *= mapEpochTokenomics[epochNum].epochPoint.totalTopUpsOLAS;	// @audit-issue

870:            totalIncentives *= mapEpochTokenomics[epochNum].unitPoints[unitType].topUpUnitFraction;	// @audit-issue

871:            uint256 sumUnitIncentives = uint256(mapEpochTokenomics[epochNum].unitPoints[unitType].sumUnitTopUpsOLAS) * 100;	// @audit-issue

872:            totalIncentives = mapUnitIncentives[unitType][unitId].topUp + totalIncentives / sumUnitIncentives;	// @audit-issue

928:                    uint96 amount = uint96(amounts[i] / numServiceUnits);	// @audit-issue

1116:        incentives[1] = (incentives[0] * tp.epochPoint.rewardTreasuryFraction) / 100;	// @audit-issue

1118:        incentives[2] = (incentives[0] * tp.unitPoints[0].rewardUnitFraction) / 100;	// @audit-issue

1119:        incentives[3] = (incentives[0] * tp.unitPoints[1].rewardUnitFraction) / 100;	// @audit-issue

1126:        uint256 numYears = (block.timestamp - timeLaunch) / ONE_YEAR;	// @audit-issue

1132:            uint256 yearEndTime = timeLaunch + numYears * ONE_YEAR;	// @audit-issue

1134:            inflationPerEpoch = (yearEndTime - prevEpochTime) * curInflationPerSecond;	// @audit-issue

1136:            curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;	// @audit-issue

1138:            inflationPerEpoch += (block.timestamp - yearEndTime) * curInflationPerSecond;	// @audit-issue

1146:            inflationPerEpoch = curInflationPerSecond * diffNumSeconds;	// @audit-issue

1152:        incentives[4] = (inflationPerEpoch * tp.epochPoint.maxBondFraction) / 100;	// @audit-issue

1225:        incentives[5] = (inflationPerEpoch * tp.unitPoints[0].topUpUnitFraction) / 100;	// @audit-issue

1227:        incentives[6] = (inflationPerEpoch * tp.unitPoints[1].topUpUnitFraction) / 100;	// @audit-issue

1237:        incentives[8] = incentives[7] + (inflationPerEpoch * mapEpochStakingPoints[eCounter].stakingFraction) / 100;	// @audit-issue

1243:        numYears = (block.timestamp + curEpochLen - timeLaunch) / ONE_YEAR;	// @audit-issue

1248:            uint256 yearEndTime = timeLaunch + numYears * ONE_YEAR;	// @audit-issue

1250:            inflationPerEpoch = (yearEndTime - block.timestamp) * curInflationPerSecond;	// @audit-issue

1252:            curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;	// @audit-issue

1254:            inflationPerEpoch += (block.timestamp + curEpochLen - yearEndTime) * curInflationPerSecond;	// @audit-issue

1256:            curMaxBond = (inflationPerEpoch * nextEpochPoint.epochPoint.maxBondFraction) / 100;	// @audit-issue

1263:            curMaxBond = (curEpochLen * curInflationPerSecond * nextEpochPoint.epochPoint.maxBondFraction) / 100;	// @audit-issue

1438:                    totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].rewardUnitFraction;	// @audit-issue

1440:                    reward += totalIncentives / 100;	// @audit-issue

1447:                    totalIncentives *= mapEpochTokenomics[lastEpoch].epochPoint.totalTopUpsOLAS;	// @audit-issue

1448:                    totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].topUpUnitFraction;	// @audit-issue

1449:                    uint256 sumUnitIncentives = uint256(mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].sumUnitTopUpsOLAS) * 100;	// @audit-issue

1451:                    topUp += totalIncentives / sumUnitIncentives;	// @audit-issue
```
[473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L473-L473), [509](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L509-L509), [854](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L854-L854), [856](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L856-L856), [869](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L869-L869), [870](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L870-L870), [871](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L871-L871), [872](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L872-L872), [928](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L928-L928), [1116](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1116-L1116), [1118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1118-L1118), [1119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1119-L1119), [1126](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1126-L1126), [1132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1132-L1132), [1134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1134-L1134), [1136](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1136-L1136), [1138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1138-L1138), [1146](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1146-L1146), [1152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1152-L1152), [1225](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1225-L1225), [1227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1227-L1227), [1237](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1237-L1237), [1243](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1243-L1243), [1248](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1248-L1248), [1250](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1250-L1250), [1252](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1252-L1252), [1254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1254-L1254), [1256](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1256-L1256), [1263](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1263-L1263), [1438](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1438-L1438), [1440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1440-L1440), [1447](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1447-L1447), [1448](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1448-L1448), [1449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1449-L1449), [1451](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1451-L1451), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

59:                supplyCap += (supplyCap * maxMintCapFraction) / 100;	// @audit-issue

96:                supplyCap += (supplyCap * maxMintCapFraction) / 100;	// @audit-issue

100:            inflationAmount = (supplyCap * maxMintCapFraction) / 100;	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L59-L59), [96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L96-L96), [100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L100-L100), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

911:            if (stakingWeight < uint256(stakingPoint.minStakingWeight) * 1e14) {	// @audit-issue

913:                returnAmount = ((stakingDiff + availableStakingAmount) * stakingWeight) / 1e18;	// @audit-issue

916:                stakingIncentive = (availableStakingAmount * stakingWeight) / 1e18;	// @audit-issue

918:                returnAmount = (stakingDiff * stakingWeight) / 1e18;	// @audit-issue

932:                    uint256 normalizedStakingAmount = stakingIncentive / (10 ** (18 - bridgingDecimals));	// @audit-issue

933:                    normalizedStakingAmount *= 10 ** (18 - bridgingDecimals);	// @audit-issue

1157:            totalReturnAmount += stakingPoint.stakingIncentive * stakingWeight;	// @audit-issue

1159:        totalReturnAmount /= 1e18;	// @audit-issue

1221:            uint256 normalizedAmount = amount / (10 ** (18 - bridgingDecimals));	// @audit-issue

1222:            normalizedAmount *= 10 ** (18 - bridgingDecimals);	// @audit-issue
```
[911](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L911-L911), [913](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L913-L913), [916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L916-L916), [918](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L918-L918), [932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L932-L932), [933](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L933-L933), [1157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1157-L1157), [1159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1159-L1159), [1221](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1221-L1221), [1222](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1222-L1222), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

159:            cost[0] = maxSubmissionCostToken + TOKEN_GAS_LIMIT * gasPriceBid;	// @audit-issue

163:        cost[1] = maxSubmissionCostMessage + gasLimitMessage * gasPriceBid;	// @audit-issue
```
[159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L159-L159), [163](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L163-L163), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

91:        emissionsLimit = _rewardsPerSecondLimit * _timeForEmissionsLimit * _numServicesLimit;	// @audit-issue

247:        emissionsLimit = _rewardsPerSecondLimit * _timeForEmissionsLimit * _numServicesLimit;	// @audit-issue
```
[91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L91-L91), [247](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L247-L247), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

58:            uint256 ratio = ((curNonces[0] - lastNonces[0]) * 1e18) / ts;	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L58-L58), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

350:        minStakingDuration = _stakingParams.minNumStakingPeriods * livenessPeriod;	// @audit-issue

353:        maxInactivityDuration = _stakingParams.maxNumInactivityPeriods * livenessPeriod;	// @audit-issue

356:        emissionsAmount = _stakingParams.rewardsPerSecond * _stakingParams.maxNumServices *	// @audit-issue

574:                    eligibleServiceRewards[numServices] = rewardsPerSecond * ts;	// @audit-issue

622:                    updatedReward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards;	// @audit-issue

633:                updatedReward = (eligibleServiceRewards[0] * lastAvailableRewards) / totalRewards;	// @audit-issue

904:                    reward = (eligibleServiceRewards[i] * lastAvailableRewards) / totalRewards;	// @audit-issue
```
[350](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L350-L350), [353](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L353-L353), [356](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L356-L356), [574](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L574-L574), [622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L622-L622), [633](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L633-L633), [904](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L904-L904), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

214:        timeSum = block.timestamp / WEEK * WEEK;	// @audit-issue

232:            uint256 dBias = pt.slope * WEEK;	// @audit-issue

272:            uint256 dBias = pt.slope * WEEK;	// @audit-issue

309:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;	// @audit-issue

424:        uint256 t = time / WEEK * WEEK;	// @audit-issue

432:            weight = 1e18 * nomineeWeight / totalSum;	// @audit-issue

489:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;	// @audit-issue

511:            oldBias = oldSlope.slope * (oldSlope.end - nextTime);	// @audit-issue

515:            slope: uint256(uint128(userSlope)) * weight / MAX_WEIGHT,	// @audit-issue

520:        uint256 newBias = newSlope.slope * (lockEnd - nextTime);	// @audit-issue

605:        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;	// @audit-issue
```
[214](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L214-L214), [232](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L232-L232), [272](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L272-L272), [309](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L309-L309), [424](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L424-L424), [432](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L432-L432), [489](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L489-L489), [511](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L511-L511), [515](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L515-L515), [520](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L520-L520), [605](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L605-L605), 


#### Recommendation

Consider integrating Solady's 'FixedPointMathLib' into your Solidity contracts for optimized fixed-point arithmetic operations. This library can provide substantial gas savings and enhance the performance of your contract. Before integration, evaluate how 'FixedPointMathLib' aligns with your contract’s requirements. Ensure thorough testing for accuracy and compatibility with your existing contract logic. Carefully document any changes and keep track of how these optimizations affect your contract's operations to maintain transparency and reliability in your application. Adopting 'FixedPointMathLib' should be a considered decision, balancing the benefits of gas efficiency with the need for maintaining code clarity and functionality.

### Consider using OZ EnumerateSet in place of nested mappings
Nested mappings and multi-dimensional arrays in Solidity operate through a process of double hashing, wherein the original storage slot and the first key are concatenated and hashed, and then this hash is again concatenated with the second key and hashed. This process can be quite gas expensive due to the double-hashing operation and subsequent storage operation (sstore).

A possible optimization involves manually concatenating the keys followed by a single hash operation and an sstore. However, this technique introduces the risk of storage collision, especially when there are other nested hash maps in the contract that use the same key types. Because Solidity is unaware of the number and structure of nested hash maps in a contract, it follows a conservative approach in computing the storage slot to avoid possible collisions.

OpenZeppelin's EnumerableSet provides a potential solution to this problem. It creates a data structure that combines the benefits of set operations with the ability to enumerate stored elements, which is not natively available in Solidity. EnumerableSet handles the element uniqueness internally and can therefore provide a more gas-efficient and collision-resistant alternative to nested mappings or multi-dimensional arrays in certain scenarios.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

367:    mapping(uint256 => mapping(uint256 => bool)) public mapNewUnits;	// @audit-issue

371:    mapping(uint256 => mapping(uint256 => IncentiveBalances)) public mapUnitIncentives;	// @audit-issue
```
[367](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L367-L367), [371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L371-L371), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

177:    mapping(address => mapping(bytes32 => VotedSlope)) public voteUserSlopes;	// @audit-issue

181:    mapping(address => mapping(bytes32 => uint256)) public lastUserVote;	// @audit-issue

190:    mapping(bytes32 => mapping(uint256 => Point)) public pointsWeight;	// @audit-issue

192:    mapping(bytes32 => mapping(uint256 => uint256)) public changesWeight;	// @audit-issue
```
[177](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L177-L177), [181](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L181-L181), [190](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L190-L190), [192](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L192-L192), 


#### Recommendation

Consider using OpenZeppelin's EnumerableSet library as a more gas-efficient and collision-resistant alternative to nested mappings or multi-dimensional arrays, especially when managing unique elements. This library simplifies the handling of unique data sets and allows for the enumeration of elements, a feature not natively supported in Solidity. When refactoring your contract, replace nested mappings or arrays with EnumerableSet where appropriate, ensuring both gas efficiency and enhanced functionality. Be sure to understand the use cases and limitations of EnumerableSet to apply it effectively in your contract's design and implementation.

### Reduce Gas Usage by Moving to Solidity 0.8.19 or Later
This issue highlights the opportunity to reduce gas consumption in a smart contract by upgrading the Solidity compiler version to 0.8.19 or a later release. Gas usage is a critical consideration in Ethereum smart contracts, as it directly affects transaction costs and contract execution efficiency. Newer compiler versions often come with optimizations and improvements that can lead to reduced gas costs for certain operations and contract structures.


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

Consider upgrading to Solidity version 0.8.19 or later to leverage compiler optimizations for reduced gas consumption. Newer versions often include improvements that enhance transaction cost-efficiency and overall contract execution.

### Constant Keccak Variables Are Treated As Expressions, Not Constants
In Solidity, state variables or local variables declared with the `constant` keyword are expected to represent constant values that are determined at compile time. These constants typically do not incur any gas costs when accessed since their values are hard-coded into the contract bytecode.

However, this expected behavior deviates when using the `keccak256()` function. When a variable is initialized with a `keccak256()` hash computation, even if declared as `constant`, it isn't truly treated as a constant in the resulting bytecode. Instead, every time this "constant" is referenced, the EVM recalculates the hash. This behavior contrasts with other true constants, which are embedded directly into the bytecode and accessed without additional computations.


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

Avoid using 'keccak256()' to initialize 'constant' variables in Solidity. Instead, precompute the hash value outside of the contract and assign this precomputed value to the constant. This ensures the variable is a true constant, eliminating redundant hash computations and saving gas.

### Use bitmap to save gas
Bitmaps in Solidity are essentially a way of representing a set of boolean values within an integer type variable such as `uint256`. Each bit in the integer represents a true or false value (1 or 0), thus allowing efficient storage of multiple boolean values.

Bitmaps can save gas in the Ethereum network because they condense a lot of information into a small amount of storage. In Ethereum, storage is one of the most significant costs in terms of gas usage. By reducing the amount of storage space needed, you can potentially save on gas fees.

Here's a quick comparison:

If you were to represent 256 different boolean values in the traditional way, you would have to declare 256 different `bool` variables. Given that each `bool` occupies a storage slot and each storage slot costs 20,000 gas to initialize, you would end up paying a considerable amount of gas.

On the other hand, if you were to use a bitmap, you could store these 256 boolean values within a single `uint256` variable. In other words, you'd only pay for a single storage slot, resulting in significant gas savings.

However, it's important to note that while bitmaps can provide gas efficiencies, they do add complexity to the code, making it harder to read and maintain. Also, using bitmaps is efficient only when dealing with a large number of boolean variables that are frequently changed or accessed together. 

In contrast, the straightforward counterpart to bitmaps would be using arrays or mappings to store boolean values, with each `bool` value occupying its own storage slot. This approach is simpler and more readable but could potentially be more expensive in terms of gas usage.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

800:            success = true;	// @audit-issue

963:                        mapNewUnits[unitType][serviceUnitIds[j]] = true;	// @audit-issue

969:                            mapNewOwners[unitOwner] = true;	// @audit-issue
```
[800](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L800-L800), [963](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L963-L963), [969](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L969-L969), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

464:                    positions[j] = true;	// @audit-issue
```
[464](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L464-L464), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

144:        mapDeliveryHashes[deliveryHash] = true;	// @audit-issue
```
[144](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L144-L144), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

122:        mapDeliveryHashes[deliveryHash] = true;	// @audit-issue
```
[122](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L122-L122), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

199:                stakingQueueingNonces[queueHash] = true;	// @audit-issue

296:            stakingQueueingNonces[queueHash] = false;	// @audit-issue
```
[199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L199-L199), [296](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L296-L296), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

841:                inSet = true;	// @audit-issue
```
[841](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L841-L841), 


#### Recommendation

Consider using bitmaps in your Solidity contracts when you need to store and manipulate a large set of boolean values. This approach is particularly advantageous in terms of gas efficiency for scenarios involving frequent changes or accesses to these values. However, balance this efficiency with code readability and maintainability. Ensure that the use of bitmaps is well-documented, and consider the complexity it introduces into the code. Employ bitmaps judiciously, especially when their use results in significant gas savings, and the logic they represent is a core aspect of the contract's functionality.

### Using `bool`s for storage incurs overhead
[Booleans are more expensive than uint256](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/58f635312aa21f947cae5f8578638a85aa2519f5/contracts/security/ReentrancyGuard.sol#L23-L27) or any type that takes up a full word because each write operation emits an extra SLOAD to first read the slot's contents, replace the bits taken up by the boolean, and then write back. This is the compiler's defense against contract upgrades and pointer aliasing, and it cannot be disabled.
Use `uint256(0)` and `uint256(1)` for true/false to avoid a Gwarmaccess (**[100 gas](https://gist.github.com/IllIllI000/1b70014db712f8572a72378321250058)**) for the extra SLOAD.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

367:    mapping(uint256 => mapping(uint256 => bool)) public mapNewUnits;	// @audit-issue

369:    mapping(address => bool) public mapNewOwners;	// @audit-issue
```
[367](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L367-L367), [369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L369-L369), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

54:    mapping(bytes32 => bool) public mapDeliveryHashes;	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L54-L54), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

18:    mapping(bytes32 => bool) public mapDeliveryHashes;	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L18-L18), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

93:    mapping(bytes32 => bool) public stakingQueueingNonces;	// @audit-issue
```
[93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L93-L93), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

64:    bool public implementationsCheck;	// @audit-issue

67:    mapping(address => bool) public mapImplementations;	// @audit-issue

45:    event SetImplementationsCheck(bool setCheck);	// @audit-issue

46:    event ImplementationsWhitelistUpdated(address[] implementations, bool[] statuses, bool setCheck);	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L67-L67), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L45-L45), [46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L46-L46), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

74:    bool isEnabled;	// @audit-issue

85:    event InstanceStatusChanged(address indexed instance, bool isEnabled);	// @audit-issue
```
[74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L74-L74), [85](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L85-L85), 


#### Recommendation

To minimize gas overhead in your Solidity contracts, consider using `uint256(1)` and `uint256(2)` to represent `true` and `false`, respectively, instead of `bool` types for storage. This approach avoids additional `SLOAD` and `SSTORE` operations, resulting in more gas-efficient code.

### Usage of `uints`/`ints` smaller than 32 bytes (256 bits) incurs overhead
When using elements that are smaller than 32 bytes, your contract’s gas usage may be higher. This is because the EVM operates on 32 bytes at a time. Therefore, if the element is smaller than that, the EVM must use more operations in order to reduce the size of the element from 32 bytes to the desired size.
https://docs.soliditylang.org/en/v0.8.11/internals/layout_in_storage.html Each operation involving a `uint8` costs an extra [22-28](https://gist.github.com/IllIllI000/9388d20c70f9a4632eb3ca7836f54977) gas (depending on whether the other operand is also a variable of type `uint8`) as compared to ones involving `uint256`, due to the compiler having to clear the higher bits of the memory word before operating on the `uint8`, as well as the associated stack operations of doing so. Use a larger size then downcast where needed


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

285:    uint96 public maxBond;	// @audit-issue

290:    uint96 public inflationPerSecond;	// @audit-issue

296:    uint96 public veOLASThreshold;	// @audit-issue

303:    uint96 public effectiveBond;	// @audit-issue

309:    uint72 public codePerDev;	// @audit-issue

312:    uint8 public currentYear;	// @audit-issue

316:    uint8 internal _locked;	// @audit-issue

323:    uint64 public epsilonRate;	// @audit-issue

326:    uint32 public epochLen;	// @audit-issue

332:    uint96 public nextVeOLASThreshold;	// @audit-issue

338:    uint32 public epochCounter;	// @audit-issue

341:    uint32 public timeLaunch;	// @audit-issue

344:    uint32 public nextEpochLen;	// @audit-issue

350:    uint72 public devsPerCapital;	// @audit-issue

356:    uint32 public lastDonationBlockNumber;	// @audit-issue

928:                    uint96 amount = uint96(amounts[i] / numServiceUnits);	// @audit-issue

163:    uint96 sumUnitTopUpsOLAS;	// @audit-issue

166:    uint32 numNewUnits;	// @audit-issue

169:    uint8 rewardUnitFraction;	// @audit-issue

172:    uint8 topUpUnitFraction;	// @audit-issue

180:    uint96 totalDonationsETH;	// @audit-issue

183:    uint96 totalTopUpsOLAS;	// @audit-issue

188:    uint64 idf;	// @audit-issue

191:    uint32 numNewOwners;	// @audit-issue

194:    uint32 endTime;	// @audit-issue

199:    uint8 rewardTreasuryFraction;	// @audit-issue

202:    uint8 maxBondFraction;	// @audit-issue

219:    uint96 reward;	// @audit-issue

221:    uint96 pendingRelativeReward;	// @audit-issue

224:    uint96 topUp;	// @audit-issue

226:    uint96 pendingRelativeTopUp;	// @audit-issue

229:    uint32 lastEpoch;	// @audit-issue

236:    uint96 stakingIncentive;	// @audit-issue

239:    uint96 maxStakingIncentive;	// @audit-issue

242:    uint16 minStakingWeight;	// @audit-issue

246:    uint8 stakingFraction;	// @audit-issue
```
[285](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L285-L285), [290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L290-L290), [296](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L296-L296), [303](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L303-L303), [309](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L309-L309), [312](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L312-L312), [316](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L316-L316), [323](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L323-L323), [326](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L326-L326), [332](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L332-L332), [338](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L338-L338), [341](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L341-L341), [344](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L344-L344), [350](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L350-L350), [356](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L356-L356), [928](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L928-L928), [163](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L163-L163), [166](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L166-L166), [169](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L169-L169), [172](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L172-L172), [180](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L180-L180), [183](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L183-L183), [188](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L188-L188), [191](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L191-L191), [194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L194-L194), [199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L199-L199), [202](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L202-L202), [219](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L219-L219), [221](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L221-L221), [224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L224-L224), [226](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L226-L226), [229](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L229-L229), [236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L236-L236), [239](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L239-L239), [242](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L242-L242), [246](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L246-L246), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

290:    uint8 internal _locked;	// @audit-issue

120:    function epochCounter() external view returns (uint32);	// @audit-issue

124:    function epochLen() external view returns (uint32);	// @audit-issue

156:    function paused() external returns (uint8);	// @audit-issue

275:    uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L290-L290), [120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L120-L120), [124](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L124-L124), [156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L156-L156), [275](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L275-L275), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

20:        uint32 _minGasLimit	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L20-L20), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

30:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L30-L30), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

46:        uint160 offset = uint160(0x1111000000000000000000000000000000001111);	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L46-L46), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

25:        uint32 _minGasLimit,	// @audit-issue

42:        uint32 _minGasLimit	// @audit-issue
```
[25](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L25-L25), [42](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L42-L42), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

63:    uint8 internal _locked;	// @audit-issue
```
[63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L63-L63), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

11:        uint16 targetChain,	// @audit-issue

36:        uint16 targetChain,	// @audit-issue

41:        uint16 refundChain,	// @audit-issue

43:    ) external payable returns (uint64 sequence);	// @audit-issue

120:        uint64 sequence = IBridge(l2MessageRelayer).sendPayloadToEvm{value: cost}(uint16(l1SourceChainId),	// @audit-issue

137:        uint16 sourceChainId,	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L11-L11), [36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L36-L36), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L41-L41), [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L43-L43), [120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L120-L120), [137](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L137-L137), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

110:        uint16 sourceChain,	// @audit-issue
```
[110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L110-L110), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

88:    uint8 public paused;	// @audit-issue

90:    uint8 internal _locked;	// @audit-issue

64:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue
```
[88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L88-L88), [90](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L90-L90), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L64-L64), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

16:    function mapServiceIdTokenDeposit(uint256 serviceId) external view returns (address, uint96);	// @audit-issue

76:        (address token, uint96 stakingDeposit) =	// @audit-issue
```
[16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L16-L16), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L76-L76), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

159:    uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue

483:        int128 userSlope = IVEOLAS(ve).getLastUserPoint(msg.sender).slope;	// @audit-issue
```
[159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L159-L159), [483](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L483-L483), 


#### Recommendation

Minimize gas overhead by using 'uint256' or 'int256' instead of smaller integer types in Solidity contracts. The EVM operates more efficiently with 32-byte sizes. Downcast to smaller types only when necessary, as operations with smaller types like 'uint8' incur extra gas due to additional EVM operations for size adjustment.

### Avoid Unnecessary Public Variables
Public state variables in Solidity automatically generate getter functions, increasing contract size and potentially leading to higher deployment and interaction costs. To optimize gas usage and contract efficiency, minimize the use of public variables unless external access is necessary. Instead, use internal or private visibility combined with explicit getter functions when required. This practice not only reduces contract size but also provides better control over data access and manipulation, enhancing security and readability. Prioritize lean, efficient contracts to ensure cost-effectiveness and better performance on the blockchain.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

10:interface IOLAS {	// @audit-issue

13:    function timeLaunch() external view returns (uint256);	// @audit-issue

15:	// @audit-issue

17:interface IToken {	// @audit-issue

19:    /// @param tokenId Token Id.	// @audit-issue

21:    function ownerOf(uint256 tokenId) external view returns (address);	// @audit-issue

23:    /// @dev Gets the total amount of tokens stored by the contract.	// @audit-issue

282:    address public owner;	// @audit-issue

285:    uint96 public maxBond;	// @audit-issue

288:    address public olas;	// @audit-issue

290:    uint96 public inflationPerSecond;	// @audit-issue

293:    address public treasury;	// @audit-issue

296:    uint96 public veOLASThreshold;	// @audit-issue

299:    address public depository;	// @audit-issue

303:    uint96 public effectiveBond;	// @audit-issue

306:    address public dispenser;	// @audit-issue

309:    uint72 public codePerDev;	// @audit-issue

312:    uint8 public currentYear;	// @audit-issue

314:    bytes1 public tokenomicsParametersUpdated;	// @audit-issue

319:    address public componentRegistry;	// @audit-issue

323:    uint64 public epsilonRate;	// @audit-issue

326:    uint32 public epochLen;	// @audit-issue

329:    address public agentRegistry;	// @audit-issue

332:    uint96 public nextVeOLASThreshold;	// @audit-issue

335:    address public serviceRegistry;	// @audit-issue

338:    uint32 public epochCounter;	// @audit-issue

341:    uint32 public timeLaunch;	// @audit-issue

344:    uint32 public nextEpochLen;	// @audit-issue

347:    address public ve;	// @audit-issue

350:    uint72 public devsPerCapital;	// @audit-issue

353:    address public donatorBlacklist;	// @audit-issue

356:    uint32 public lastDonationBlockNumber;	// @audit-issue

359:    mapping(uint256 => uint256) public mapServiceAmounts;	// @audit-issue

361:    mapping(address => uint256) public mapOwnerRewards;	// @audit-issue

363:    mapping(address => uint256) public mapOwnerTopUps;	// @audit-issue

365:    mapping(uint256 => TokenomicsPoint) public mapEpochTokenomics;	// @audit-issue

367:    mapping(uint256 => mapping(uint256 => bool)) public mapNewUnits;	// @audit-issue

369:    mapping(address => bool) public mapNewOwners;	// @audit-issue

371:    mapping(uint256 => mapping(uint256 => IncentiveBalances)) public mapUnitIncentives;	// @audit-issue

374:    mapping(uint256 => StakingPoint) public mapEpochStakingPoints;	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L10-L10), [13](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L13-L13), [15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L15-L15), [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L17-L17), [19](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L19-L19), [21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L21-L21), [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L23-L23), [282](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L282-L282), [285](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L285-L285), [288](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L288-L288), [290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L290-L290), [293](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L293-L293), [296](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L296-L296), [299](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L299-L299), [303](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L303-L303), [306](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L306-L306), [309](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L309-L309), [312](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L312-L312), [314](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L314-L314), [319](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L319-L319), [323](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L323-L323), [326](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L326-L326), [329](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L329-L329), [332](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L332-L332), [335](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L335-L335), [338](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L338-L338), [341](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L341-L341), [344](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L344-L344), [347](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L347-L347), [350](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L350-L350), [353](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L353-L353), [356](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L356-L356), [359](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L359-L359), [361](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L361-L361), [363](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L363-L363), [365](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L365-L365), [367](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L367-L367), [369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L369-L369), [371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L371-L371), [374](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L374-L374), 


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

277:    address public immutable olas;	// @audit-issue

279:    bytes32 public immutable retainer;	// @audit-issue

281:    bytes32 public immutable retainerHash;	// @audit-issue

284:    uint256 public maxNumClaimingEpochs;	// @audit-issue

286:    uint256 public maxNumStakingTargets;	// @audit-issue

288:    address public owner;	// @audit-issue

292:    Pause public paused;	// @audit-issue

295:    address public tokenomics;	// @audit-issue

297:    address public treasury;	// @audit-issue

299:    address public voteWeighting;	// @audit-issue

302:    mapping(bytes32 => uint256) public mapLastClaimedStakingEpochs;	// @audit-issue

304:    mapping(bytes32 => uint256) public mapRemovedNomineeEpochs;	// @audit-issue

306:    mapping(uint256 => address) public mapChainIdDepositProcessors;	// @audit-issue

308:    mapping(uint256 => uint256) public mapChainIdWithheldAmounts;	// @audit-issue
```
[275](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L275-L275), [277](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L277-L277), [279](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L279-L279), [281](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L281-L281), [284](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L284-L284), [286](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L286-L286), [288](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L288-L288), [292](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L292-L292), [295](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L295-L295), [297](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L297-L297), [299](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L299-L299), [302](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L302-L302), [304](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L304-L304), [306](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L306-L306), [308](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L308-L308), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

28:        address _refundTo,	// @audit-issue

30:        uint256 _amount,	// @audit-issue

33:        bytes calldata _data	// @audit-issue

35:	// @audit-issue

37:    // Doc: https://docs.arbitrum.io/arbos/l1-to-l2-messaging	// @audit-issue

39:    /// @param to destination L2 contract address	// @audit-issue

41:    /// @param maxSubmissionCost Max gas deducted from user's L2 balance to cover base submission fee	// @audit-issue

43:    /// @param callValueRefundAddress l2Callvalue gets credited here on L2 if retryable txn times out or gets cancelled	// @audit-issue

45:    /// @param maxFeePerGas price bid for L2 execution. Should not be set to 1 (magic value used to trigger the RetryableData error)	// @audit-issue

47:    /// @return unique message number of the retryable transaction	// @audit-issue

49:        address to,	// @audit-issue

51:        uint256 maxSubmissionCost,	// @audit-issue

72:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 160;	// @audit-issue

74:    address public immutable l1ERC20Gateway;	// @audit-issue

76:    address public immutable outbox;	// @audit-issue

78:    address public immutable bridge;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L30-L30), [33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L33-L33), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L35-L35), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L37-L37), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L39-L39), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L41-L41), [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L43-L43), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L45-L45), [47](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L47-L47), [49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L51-L51), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L72-L72), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L74-L74), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L78-L78), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

62:        // Extract bridge cost and gas limit from the bridge payload	// @audit-issue

64:	// @audit-issue

67:            revert ZeroValue();	// @audit-issue

70:        // Check that provided msg.value is enough to cover the cost	// @audit-issue

72:            revert LowerThan(msg.value, cost);	// @audit-issue

74:	// @audit-issue

76:        if (gasLimitMessage < GAS_LIMIT) {	// @audit-issue

78:        }	// @audit-issue

80:        if (gasLimitMessage > MAX_GAS_LIMIT) {	// @audit-issue

82:        }	// @audit-issue

84:        // Assemble data payload	// @audit-issue

86:	// @audit-issue

88:        // Reference: https://docs.optimism.io/builders/app-developers/bridging/messaging#for-l1-to-l2-transactions-1	// @audit-issue

93:	// @audit-issue

39:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L62-L62), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L67-L67), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L70-L70), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L72-L72), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L74-L74), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L78-L78), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L80-L80), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L82-L82), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L84-L84), [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L86-L86), [88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L88-L88), [93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L93-L93), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L39-L39), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

28:    bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));	// @audit-issue

30:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue

33:    uint256 public constant TOKEN_GAS_LIMIT = 300_000;	// @audit-issue

35:    uint256 public constant MESSAGE_GAS_LIMIT = 2_000_000;	// @audit-issue

37:    address public immutable olas;	// @audit-issue

39:    address public immutable l1Dispenser;	// @audit-issue

41:    address public immutable l1TokenRelayer;	// @audit-issue

43:    address public immutable l1MessageRelayer;	// @audit-issue

45:    uint256 public immutable l2TargetChainId;	// @audit-issue

47:    address public l2TargetDispenser;	// @audit-issue

49:    address public owner;	// @audit-issue

51:    uint256 public stakingBatchNonce;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L30-L30), [33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L33-L33), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L35-L35), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L37-L37), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L39-L39), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L41-L41), [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L43-L43), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L45-L45), [47](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L47-L47), [49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L51-L51), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

62:	// @audit-issue

64:    /// @notice msg.sender is an aliased L1 l1DepositProcessor address.	// @audit-issue

67:        // Check that msg.sender is the aliased L1 deposit processor	// @audit-issue

70:        }	// @audit-issue

72:        // Process the message data	// @audit-issue

74:    }	// @audit-issue









25:    address public immutable l1AliasedDepositProcessor;	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L62-L62), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L67-L67), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L70-L70), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L72-L72), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L74-L74), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L78-L78), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L80-L80), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L82-L82), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L84-L84), [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L86-L86), [88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L88-L88), [93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L93-L93), [25](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L25-L25), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

28:	// @audit-issue

30:    // Doc: https://docs.optimism.io/builders/app-developers/bridging/messaging	// @audit-issue

33:    ///         permanently locked. The same will occur if the target on the other chain is	// @audit-issue

35:    ///	// @audit-issue

37:    /// @param _message     Message to trigger the target address with.	// @audit-issue

39:    function sendMessage(	// @audit-issue

41:        bytes calldata _message,	// @audit-issue

43:    ) external payable;	// @audit-issue

45:    // Source: https://github.com/ethereum-optimism/optimism/blob/65ec61dde94ffa93342728d324fecf474d228e1f/packages/contracts-bedrock/contracts/universal/CrossDomainMessenger.sol#L422	// @audit-issue

47:    /// @notice Retrieves the address of the contract or wallet that initiated the currently	// @audit-issue

49:    ///         currently being executed. Allows the recipient of a call to see who triggered it.	// @audit-issue

51:    /// @return Address of the sender of the currently executing message on the other chain.	// @audit-issue

61:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue

63:    address public immutable olasL2;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L30-L30), [33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L33-L33), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L35-L35), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L37-L37), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L39-L39), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L41-L41), [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L43-L43), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L45-L45), [47](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L47-L47), [49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L51-L51), [61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L61-L61), [63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L63-L63), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

55:    address public immutable olas;	// @audit-issue

57:    address public immutable dispenser;	// @audit-issue

59:    address public immutable stakingFactory;	// @audit-issue

61:    address public immutable timelock;	// @audit-issue
```
[55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L55-L55), [57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L57-L57), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L59-L59), [61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L61-L61), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

62:    /// @param _wormholeCore L2 Wormhole Core contract address.	// @audit-issue

64:    constructor(	// @audit-issue

67:        address _l2MessageRelayer,	// @audit-issue

70:        address _wormholeCore,	// @audit-issue

72:    )	// @audit-issue

74:        TokenBase(_l2MessageRelayer, _l2TokenRelayer, _wormholeCore)	// @audit-issue

76:        // Check for zero addresses	// @audit-issue

78:            revert ZeroAddress();	// @audit-issue

80:	// @audit-issue

82:        if (_l1SourceChainId > type(uint16).max) {	// @audit-issue

84:        }	// @audit-issue

86:        l1SourceChainId = _l1SourceChainId;	// @audit-issue

88:	// @audit-issue

93:        }	// @audit-issue

52:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue

54:    mapping(bytes32 => bool) public mapDeliveryHashes;	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L62-L62), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L67-L67), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L70-L70), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L72-L72), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L74-L74), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L78-L78), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L80-L80), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L82-L82), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L84-L84), [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L86-L86), [88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L88-L88), [93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L93-L93), [52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L52-L52), [54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L54-L54), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

28:/// @title GnosisDepositProcessorL1 - Smart contract for sending tokens and data via Gnosis bridge from L1 to L2 and processing data received from L2.	// @audit-issue

30:/// @author Andrey Lebedev - <andrey.lebedev@valory.xyz>	// @audit-issue

33:    // Bridge payload length	// @audit-issue

35:	// @audit-issue

37:    /// @param _olas OLAS token address.	// @audit-issue

39:    /// @param _l1TokenRelayer L1 token relayer bridging contract address (OmniBridge).	// @audit-issue

41:    /// @param _l2TargetChainId L2 target chain Id.	// @audit-issue

43:        address _olas,	// @audit-issue

45:        address _l1TokenRelayer,	// @audit-issue

47:        uint256 _l2TargetChainId	// @audit-issue

49:	// @audit-issue

51:    function _sendMessage(	// @audit-issue

34:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 32;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L30-L30), [33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L33-L33), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L35-L35), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L37-L37), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L39-L39), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L41-L41), [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L43-L43), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L45-L45), [47](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L47-L47), [49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L51-L51), [34](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L34-L34), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

62:            revert OwnerOnly(msg.sender, owner);	// @audit-issue

64:	// @audit-issue

67:            revert ZeroAddress();	// @audit-issue

70:        // Set L1 deposit processor address	// @audit-issue

72:	// @audit-issue

74:    }	// @audit-issue









15:    /// @param _olas OLAS token address.	// @audit-issue

18:    /// @param _l1DepositProcessor L1 deposit processor address.	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L62-L62), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L67-L67), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L70-L70), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L72-L72), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L74-L74), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L78-L78), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L80-L80), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L82-L82), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L84-L84), [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L86-L86), [88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L88-L88), [93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L93-L93), [15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L15-L15), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L18-L18), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

28:    constructor(	// @audit-issue

30:        address _l1Dispenser,	// @audit-issue

33:        uint256 _l2TargetChainId,	// @audit-issue

35:        uint256 _wormholeTargetChainId	// @audit-issue

37:        DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)	// @audit-issue

39:    {	// @audit-issue

41:        if (_wormholeCore == address(0)) {	// @audit-issue

43:        }	// @audit-issue

45:        // Check for zero value	// @audit-issue

47:            revert ZeroValue();	// @audit-issue

49:	// @audit-issue

51:        if (_wormholeTargetChainId > type(uint16).max) {	// @audit-issue

13:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 64;	// @audit-issue

15:    uint256 public immutable wormholeTargetChainId;	// @audit-issue

18:    mapping(bytes32 => bool) public mapDeliveryHashes;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L30-L30), [33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L33-L33), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L35-L35), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L37-L37), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L39-L39), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L41-L41), [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L43-L43), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L45-L45), [47](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L47-L47), [49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L51-L51), [13](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L13-L13), [15](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L15-L15), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L18-L18), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

62:        }	// @audit-issue

64:        // Get the gas limit from the bridge payload	// @audit-issue

67:        // Check the gas limit values for both ends	// @audit-issue

70:        }	// @audit-issue

72:        if (gasLimitMessage > MAX_GAS_LIMIT) {	// @audit-issue

74:        }	// @audit-issue

76:        // Assemble AMB data payload	// @audit-issue

78:	// @audit-issue

80:        bytes32 iMsg = IBridge(l2MessageRelayer).requireToPassMessage(l1DepositProcessor, data, gasLimitMessage);	// @audit-issue

82:        emit MessagePosted(uint256(iMsg), msg.sender, l1DepositProcessor, amount);	// @audit-issue

84:	// @audit-issue

86:    /// @param data Bytes message data sent from the AMB Contract Proxy (Home) contract.	// @audit-issue

88:        // Get L1 deposit processor address	// @audit-issue

93:    }	// @audit-issue

28:    uint256 public constant BRIDGE_PAYLOAD_LENGTH = 32;	// @audit-issue

30:    address public immutable l2TokenRelayer;	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L62-L62), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L67-L67), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L70-L70), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L72-L72), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L74-L74), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L78-L78), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L80-L80), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L82-L82), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L84-L84), [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L86-L86), [88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L88-L88), [93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L93-L93), [28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L30-L30), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

62:    bytes4 public constant RECEIVE_MESSAGE = bytes4(keccak256(bytes("receiveMessage(bytes)")));	// @audit-issue

64:    uint256 public constant MAX_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue

67:    uint256 public constant GAS_LIMIT = 300_000;	// @audit-issue

70:    uint256 public constant MAX_GAS_LIMIT = 2_000_000;	// @audit-issue

72:    address public immutable olas;	// @audit-issue

74:    address public immutable stakingFactory;	// @audit-issue

76:    address public immutable l2MessageRelayer;	// @audit-issue

78:    address public immutable l1DepositProcessor;	// @audit-issue

80:    uint256 public immutable l1SourceChainId;	// @audit-issue

82:    uint256 public withheldAmount;	// @audit-issue

84:    uint256 public stakingBatchNonce;	// @audit-issue

86:    address public owner;	// @audit-issue

88:    uint8 public paused;	// @audit-issue

93:    mapping(bytes32 => bool) public stakingQueueingNonces;	// @audit-issue
```
[62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L62-L62), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L67-L67), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L70-L70), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L72-L72), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L74-L74), [76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L76-L76), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L78-L78), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L80-L80), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L82-L82), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L84-L84), [86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L86-L86), [88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L88-L88), [93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L93-L93), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

28:    /// @dev PolygonDepositProcessorL1 constructor.	// @audit-issue

30:    /// @param _l1Dispenser L1 tokenomics dispenser address.	// @audit-issue

33:    /// @param _l2TargetChainId L2 target chain Id.	// @audit-issue

35:    /// @param _predicate ERC20 predicate contract to lock tokens on L1 before sending to L2.	// @audit-issue

37:        address _olas,	// @audit-issue

39:        address _l1TokenRelayer,	// @audit-issue

41:        uint256 _l2TargetChainId,	// @audit-issue

43:        address _predicate	// @audit-issue

45:        DefaultDepositProcessorL1(_olas, _l1Dispenser, _l1TokenRelayer, _l1MessageRelayer, _l2TargetChainId)	// @audit-issue

47:    {	// @audit-issue

49:        if (_checkpointManager == address(0) || _predicate == address(0)) {	// @audit-issue

51:        }	// @audit-issue

36:    constructor(	// @audit-issue

46:        FxBaseRootTunnel(_checkpointManager, _l1MessageRelayer)	// @audit-issue

26:    address public immutable predicate;	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L28-L28), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L30-L30), [33](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L33-L33), [35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L35-L35), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L37-L37), [39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L39-L39), [41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L41-L41), [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L43-L43), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L45-L45), [47](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L47-L47), [49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L49-L49), [51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L51-L51), [36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L36-L36), [46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L46-L46), [26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L26-L26), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

51:    address public immutable olas;	// @audit-issue

54:    uint256 public rewardsPerSecondLimit;	// @audit-issue

56:    uint256 public timeForEmissionsLimit;	// @audit-issue

58:    uint256 public numServicesLimit;	// @audit-issue

60:    uint256 public emissionsLimit;	// @audit-issue

62:    address public owner;	// @audit-issue

64:    bool public implementationsCheck;	// @audit-issue

67:    mapping(address => bool) public mapImplementations;	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L51-L51), [54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L54-L54), [56](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L56-L56), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L58-L58), [60](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L60-L60), [62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L62-L62), [64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L67-L67), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol
























```
[224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L224-L224), [227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L227-L227), [229](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L229-L229), [231](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L231-L231), [234](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L234-L234), [236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L236-L236), [238](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L238-L238), [240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L240-L240), [242](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L242-L242), [244](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L244-L244), [246](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L246-L246), [248](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L248-L248), [250](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L250-L250), [252](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L252-L252), [256](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L256-L256), [258](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L258-L258), [260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L260-L260), [262](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L262-L262), [264](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L264-L264), [266](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L266-L266), [268](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L268-L268), [270](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L270-L270), [272](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L272-L272), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L274-L274), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

20:    uint256 public immutable livenessRatio;	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L20-L20), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

224:    string public constant VERSION = "0.2.0";	// @audit-issue

227:    bytes32 public metadataHash;	// @audit-issue

229:    uint256 public maxNumServices;	// @audit-issue

231:    uint256 public rewardsPerSecond;	// @audit-issue

234:    uint256 public minStakingDeposit;	// @audit-issue

236:    uint256 public maxNumInactivityPeriods;	// @audit-issue

238:    uint256 public livenessPeriod;	// @audit-issue

240:    uint256 public timeForEmissions;	// @audit-issue

242:    uint256 public numAgentInstances;	// @audit-issue

244:    uint256 public threshold;	// @audit-issue

246:    bytes32 public configHash;	// @audit-issue

248:    bytes32 public proxyHash;	// @audit-issue

250:    address public serviceRegistry;	// @audit-issue

252:    address public activityChecker;	// @audit-issue

256:    uint256 public minStakingDuration;	// @audit-issue

258:    uint256 public maxInactivityDuration;	// @audit-issue

260:    uint256 public epochCounter;	// @audit-issue

262:    uint256 public balance;	// @audit-issue

264:    uint256 public availableRewards;	// @audit-issue

266:    uint256 public emissionsAmount;	// @audit-issue

268:    uint256 public tsCheckpoint;	// @audit-issue

270:    uint256[] public agentIds;	// @audit-issue

272:    mapping (uint256 => ServiceInfo) public mapServiceInfo;	// @audit-issue

274:    uint256[] public setServiceIds;	// @audit-issue
```
[224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L224-L224), [227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L227-L227), [229](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L229-L229), [231](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L231-L231), [234](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L234-L234), [236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L236-L236), [238](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L238-L238), [240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L240-L240), [242](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L242-L242), [244](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L244-L244), [246](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L246-L246), [248](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L248-L248), [250](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L250-L250), [252](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L252-L252), [256](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L256-L256), [258](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L258-L258), [260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L260-L260), [262](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L262-L262), [264](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L264-L264), [266](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L266-L266), [268](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L268-L268), [270](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L270-L270), [272](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L272-L272), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L274-L274), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

























46:    address public serviceRegistryTokenUtility;	// @audit-issue

48:    address public stakingToken;	// @audit-issue
```
[224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L224-L224), [227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L227-L227), [229](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L229-L229), [231](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L231-L231), [234](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L234-L234), [236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L236-L236), [238](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L238-L238), [240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L240-L240), [242](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L242-L242), [244](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L244-L244), [246](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L246-L246), [248](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L248-L248), [250](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L250-L250), [252](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L252-L252), [256](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L256-L256), [258](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L258-L258), [260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L260-L260), [262](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L262-L262), [264](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L264-L264), [266](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L266-L266), [268](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L268-L268), [270](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L270-L270), [272](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L272-L272), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L274-L274), [46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L46-L46), [48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L48-L48), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

23:    bytes32 public constant SERVICE_STAKING_PROXY = 0x9e5e169c1098011e4e5940a3ec1797686b2a8294a9b77a4c676b121bdc0ebb5e;	// @audit-issue
```
[23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L23-L23), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

88:    uint256 public constant SELECTOR_DATA_LENGTH = 4;	// @audit-issue

90:    uint256 public nonce;	// @audit-issue

92:    address public owner;	// @audit-issue

94:    address public verifier;	// @audit-issue

99:    mapping(address => InstanceParams) public mapInstanceParams;	// @audit-issue
```
[88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L88-L88), [90](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L90-L90), [92](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L92-L92), [94](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L94-L94), [99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L99-L99), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

145:    uint256 public constant WEEK = 604_800;	// @audit-issue

148:    uint256 public constant WEIGHT_VOTE_DELAY = 864_000;	// @audit-issue

155:    uint256 public constant MAX_NUM_WEEKS = 53;	// @audit-issue

157:    uint256 public constant MAX_WEIGHT = 10_000;	// @audit-issue

159:    uint256 public constant MAX_EVM_CHAIN_ID = type(uint64).max / 2 - 36;	// @audit-issue

161:    address public immutable ve;	// @audit-issue

163:    address public owner;	// @audit-issue

165:    address public dispenser;	// @audit-issue

168:    Nominee[] public setNominees;	// @audit-issue

170:    Nominee[] public setRemovedNominees;	// @audit-issue

172:    mapping(bytes32 => uint256) public mapNomineeIds;	// @audit-issue

174:    mapping(bytes32 => uint256) public mapRemovedNominees;	// @audit-issue

177:    mapping(address => mapping(bytes32 => VotedSlope)) public voteUserSlopes;	// @audit-issue

179:    mapping(address => uint256) public voteUserPower;	// @audit-issue

181:    mapping(address => mapping(bytes32 => uint256)) public lastUserVote;	// @audit-issue

190:    mapping(bytes32 => mapping(uint256 => Point)) public pointsWeight;	// @audit-issue

192:    mapping(bytes32 => mapping(uint256 => uint256)) public changesWeight;	// @audit-issue

194:    mapping(bytes32 => uint256) public timeWeight;	// @audit-issue

197:    mapping(uint256 => Point) public pointsSum;	// @audit-issue

199:    mapping(uint256 => uint256) public changesSum;	// @audit-issue

201:    uint256 public timeSum;	// @audit-issue
```
[145](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L145-L145), [148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L148-L148), [155](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L155-L155), [157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L157-L157), [159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L159-L159), [161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L161-L161), [163](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L163-L163), [165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L165-L165), [168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L168-L168), [170](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L170-L170), [172](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L172-L172), [174](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L174-L174), [177](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L177-L177), [179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L179-L179), [181](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L181-L181), [190](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L190-L190), [192](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L192-L192), [194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L194-L194), [197](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L197-L197), [199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L199-L199), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L201-L201), 


#### Recommendation

Avoid creating explicit getter functions for 'public' state variables in Solidity. The compiler automatically generates getters for such variables, making additional functions redundant. This practice helps reduce contract size, lowers deployment costs, and simplifies maintenance and understanding of the contract.

### Use `do while` loops intead of for loops
A `do while` loop will cost less gas since the condition is not being checked for the first iteration.
```solidity
uint256 i = 1;
do {                   
    param2 += i;
    i++;
}
while (i < 50);
``` 
is better than
```solidity
for(uint256 i = 1; i< 50; i++){
    param1 += i;
}
```


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

904:        for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue

916:            for (uint256 unitType = 0; unitType < 2; ++unitType) {	// @audit-issue

930:                    for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue

960:                for (uint256 j = 0; j < numServiceUnits; ++j) {	// @audit-issue

1010:        for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue

1199:            for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1329:        for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1335:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1357:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1401:        for (uint256 i = 0; i < 2; ++i) {	// @audit-issue

1407:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue

1429:        for (uint256 i = 0; i < unitIds.length; ++i) {	// @audit-issue
```
[904](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L904-L904), [916](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L916-L916), [930](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L930-L930), [960](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L960-L960), [1010](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1010-L1010), [1199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1199-L1199), [1329](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1329-L1329), [1335](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1335-L1335), [1357](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1357-L1357), [1401](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1401-L1401), [1407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1407-L1407), [1429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1429-L1429), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

58:            for (uint256 i = 0; i < numYears; ++i) {	// @audit-issue

95:            for (uint256 i = 1; i < numYears; ++i) {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L58-L58), [95](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L95-L95), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

450:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

462:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

473:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

485:                for (uint256 j = 0; j < updatedStakingTargets.length; ++j) {	// @audit-issue

526:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

550:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

593:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

600:            for (uint256 j = 0; j < stakingTargets[i].length; ++j) {	// @audit-issue

717:        for (uint256 i = 0; i < chainIds.length; ++i) {	// @audit-issue

881:        for (uint256 j = firstClaimedEpoch; j < lastClaimedEpoch; ++j) {	// @audit-issue

1146:        for (uint256 j = firstClaimedEpoch; j < lastClaimedEpoch; ++j) {	// @audit-issue
```
[450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L450-L450), [462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L462-L462), [473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L473-L473), [485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L485-L485), [526](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L526-L526), [550](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L550-L550), [593](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L593-L593), [600](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L600-L600), [717](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L717-L717), [881](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L881-L881), [1146](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1146-L1146), 


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
Path: ./registries/contracts/staking/StakingVerifier.sol

150:        for (uint256 i = 0; i < implementations.length; ++i) {	// @audit-issue
```
[150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L150-L150), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

332:        for (uint256 i = 0; i < _stakingParams.agentIds.length; ++i) {	// @audit-issue

380:        for (uint256 i = 0; i < numAgentIds; ++i) {	// @audit-issue

449:        for (uint256 i = 0; i < totalNumServices; ++i) {	// @audit-issue

464:        for (uint256 i = numEvictServices; i > 0; --i) {	// @audit-issue

548:            for (uint256 i = 0; i < size; ++i) {	// @audit-issue

620:                for (uint256 i = 1; i < numServices; ++i) {	// @audit-issue

648:                for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue

669:            for (uint256 i = 0; i < serviceIds.length; ++i) {	// @audit-issue

773:            for (uint256 i = 0; i < numAgents; ++i) {	// @audit-issue

835:        for (; idx < serviceIds.length; ++idx) {	// @audit-issue

899:        for (uint256 i = 0; i < numServices; ++i) {	// @audit-issue
```
[332](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L332-L332), [380](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L380-L380), [449](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L449-L449), [464](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L464-L464), [548](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L548-L548), [620](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L620-L620), [648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L648-L648), [669](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L669-L669), [773](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L773-L773), [835](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L835-L835), [899](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L899-L899), 


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

Where appropriate, consider using a `do while` loop instead of a `for` loop in your Solidity contracts. This is especially beneficial when the first iteration of the loop does not require a condition check. Refactor your loop logic to fit the `do while` structure for more gas-efficient execution. However, ensure that the loop's logic and termination conditions are correctly implemented to avoid infinite loops or other logical errors. Always balance gas efficiency with code readability and the specific requirements of your contract's logic.

### Using XOR (^) and AND (&) bitwise equivalents for gas optimizations
Given 4 variables a, b, c and d represented as such:
```
0 0 0 0 0 1 1 0 <- a
0 1 1 0 0 1 1 0 <- b
0 0 0 0 0 0 0 0 <- c
1 1 1 1 1 1 1 1 <- d
```
To have a == b means that every 0 and 1 match on both variables. Meaning that a XOR (operator ^) would evaluate to 0 ((a ^ b) == 0), as it excludes by definition any equalities.Now, if a != b, this means that there’s at least somewhere a 1 and a 0 not matching between a and b, making (a ^ b) != 0.Both formulas are logically equivalent and using the XOR bitwise operator costs actually the same amount of gas.However, it is much cheaper to use the bitwise OR operator (|) than comparing the truthy or falsy values.These are logically equivalent too, as the OR bitwise operator (|) would result in a 1 somewhere if any value is not 0 between the XOR (^) statements, meaning if any XOR (^) statement verifies that its arguments are different.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

427:        if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||	// @audit-issue

428:            _ve == address(0) || _componentRegistry == address(0) || _agentRegistry == address(0) ||	// @audit-issue

429:            _serviceRegistry == address(0)) {	// @audit-issue

533:        if (implementation == address(0)) {	// @audit-issue

553:        if (newOwner == address(0)) {	// @audit-issue

758:        if (_maxStakingIncentive == 0 || _minStakingWeight == 0) {	// @audit-issue

922:                if (numServiceUnits == 0) {	// @audit-issue

934:                        if (lastEpoch == 0) {	// @audit-issue

1087:        if (implementation == address(0)) {	// @audit-issue

1092:        if (lastDonationBlockNumber == block.number) {	// @audit-issue

1174:        if (tokenomicsParametersUpdated & 0x01 == 0x01) {	// @audit-issue

1194:        if (tokenomicsParametersUpdated & 0x02 == 0x02) {	// @audit-issue

1211:        if (tokenomicsParametersUpdated & 0x08 == 0x08) {	// @audit-issue

1285:        if (incentives[1] == 0 || ITreasury(treasury).rebalanceTreasury(incentives[1])) {	// @audit-issue
```
[427](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L427-L427), [428](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L428-L428), [429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L429-L429), [533](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L533-L533), [553](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L553-L553), [758](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L758-L758), [922](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L922-L922), [934](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L934-L934), [1087](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1087-L1087), [1092](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1092-L1092), [1174](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1174-L1174), [1194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1194-L1194), [1211](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1211-L1211), [1285](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1285-L1285), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

330:        if (_olas == address(0) || _tokenomics == address(0) || _treasury == address(0) ||	// @audit-issue

331:            _voteWeighting == address(0) || _retainer == 0) {	// @audit-issue

336:        if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {	// @audit-issue

369:        if (firstClaimedEpoch == 0) {	// @audit-issue

374:        if (firstClaimedEpoch == eCounter) {	// @audit-issue

535:            if (stakingTargets[i].length == 0) {	// @audit-issue

643:        if (newOwner == address(0)) {	// @audit-issue

690:        if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {	// @audit-issue

712:        if (depositProcessors.length == 0 || depositProcessors.length != chainIds.length) {	// @audit-issue

719:            if (chainIds[i] == 0) {	// @audit-issue

740:        if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||	// @audit-issue

741:            ITreasury(treasury).paused() == 2) {	// @audit-issue

757:        if (retainerHash == nomineeHash) {	// @audit-issue

801:        if (currentPause == Pause.DevIncentivesPaused || currentPause == Pause.AllPaused ||	// @audit-issue

802:            ITreasury(treasury).paused() == 2) {	// @audit-issue

861:        if (chainId == 0) {	// @audit-issue

866:        if (stakingTarget == 0) {	// @audit-issue

966:        if (chainId == 0) {	// @audit-issue

971:        if (stakingTarget == 0) {	// @audit-issue

983:        if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||	// @audit-issue

984:            ITreasury(treasury).paused() == 2) {	// @audit-issue

1080:        if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||	// @audit-issue

1081:            ITreasury(treasury).paused() == 2) {	// @audit-issue

1206:        if (chainId == 0 || amount == 0) {	// @audit-issue

1211:        if (chainId == block.chainid) {	// @audit-issue
```
[330](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L330-L330), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L331-L331), [336](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L336-L336), [369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L369-L369), [374](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L374-L374), [535](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L535-L535), [643](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L643-L643), [690](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L690-L690), [712](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L712-L712), [719](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L719-L719), [740](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L740-L740), [741](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L741-L741), [757](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L757-L757), [801](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L801-L801), [802](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L802-L802), [861](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L861-L861), [866](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L866-L866), [966](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L966-L966), [971](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L971-L971), [983](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L983-L983), [984](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L984-L984), [1080](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1080-L1080), [1081](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1081-L1081), [1206](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1206-L1206), [1211](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1211-L1211), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

102:        if (_l1ERC20Gateway == address(0) || _outbox == address(0) || _bridge == address(0)) {	// @audit-issue

135:        if (refundAccount == address(0)) {	// @audit-issue

141:        if (gasPriceBid < 2 || gasLimitMessage < 2 || maxSubmissionCostMessage == 0) {	// @audit-issue

154:            if (maxSubmissionCostToken == 0) {	// @audit-issue
```
[102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L102-L102), [135](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L135-L135), [141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L141-L141), [154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L154-L154), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

66:        if (cost == 0) {	// @audit-issue
```
[66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L66-L66), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

67:        if (_l1Dispenser == address(0) || _l1TokenRelayer == address(0) || _l1MessageRelayer == address(0)) {	// @audit-issue

72:        if (_l2TargetChainId == 0) {	// @audit-issue

193:        if (l2Dispenser == address(0)) {	// @audit-issue
```
[67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L67-L67), [72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L72-L72), [193](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L193-L193), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

87:        if (_olasL2 == address(0)) {	// @audit-issue

121:        if (cost == 0 || gasLimitMessage == 0) {	// @audit-issue
```
[87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L87-L87), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L121-L121), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

72:        if (_olas == address(0) || _dispenser == address(0) || _stakingFactory == address(0) || _timelock == address(0)) {	// @audit-issue

102:            if (limitAmount == 0) {	// @audit-issue
```
[72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L72-L72), [102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L102-L102), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

77:        if (_wormholeCore == address(0) || _l2TokenRelayer == address(0)) {	// @audit-issue

98:        if (refundAccount == address(0)) {	// @audit-issue
```
[77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L77-L77), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L98-L98), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

80:            if (gasLimitMessage == 0) {	// @audit-issue
```
[80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L80-L80), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

66:        if (l1Processor == address(0)) {	// @audit-issue
```
[66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L66-L66), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

41:        if (_wormholeCore == address(0)) {	// @audit-issue

46:        if (_wormholeTargetChainId == 0) {	// @audit-issue

74:        if (gasLimitMessage == 0) {	// @audit-issue

84:        if (refundAccount == address(0)) {	// @audit-issue
```
[41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L41-L41), [46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L46-L46), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L74-L74), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L84-L84), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

50:        if (_l2TokenRelayer == address(0)) {	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

109:        if (_olas == address(0) || _stakingFactory == address(0) || _l2MessageRelayer == address(0)	// @audit-issue

110:            || _l1DepositProcessor == address(0)) {	// @audit-issue

115:        if (_l1SourceChainId == 0) {	// @audit-issue

165:            if (success && returnData.length == 32) {	// @audit-issue

170:            if (limitAmount == 0) {	// @audit-issue

189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {	// @audit-issue

254:        if (newOwner == address(0)) {	// @audit-issue

274:        if (paused == 2) {	// @audit-issue

331:        if (paused == 2) {	// @audit-issue

337:        if (amount == 0) {	// @audit-issue

391:        if (amount == 0) {	// @audit-issue

425:        if (paused == 1) {	// @audit-issue

430:        if (newL2TargetDispenser.code.length == 0) {	// @audit-issue

435:        if (newL2TargetDispenser == address(this)) {	// @audit-issue

460:        if (owner == address(0)) {	// @audit-issue
```
[109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L109-L109), [110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L110-L110), [115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L115-L115), [165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L165-L165), [170](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L170-L170), [189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L189-L189), [254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L254-L254), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L274-L274), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L331-L331), [337](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L337-L337), [391](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L391-L391), [425](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L425-L425), [430](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L430-L430), [435](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L435-L435), [460](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L460-L460), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

49:        if (_checkpointManager == address(0) || _predicate == address(0)) {	// @audit-issue

105:        if (l2Dispenser == address(0)) {	// @audit-issue
```
[49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L49-L49), [105](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L105-L105), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

77:        if (_olas == address(0)) {	// @audit-issue

82:        if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {	// @audit-issue

103:        if (newOwner == address(0)) {	// @audit-issue

142:        if (implementations.length == 0 || implementations.length != statuses.length) {	// @audit-issue

152:            if (implementations[i] == address(0)) {	// @audit-issue

186:        if (instance.code.length == 0) {	// @audit-issue

212:            if (returnData.length == 32) {	// @audit-issue

240:        if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {	// @audit-issue
```
[77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L77-L77), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L82-L82), [103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L103-L103), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L142-L142), [152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L152-L152), [186](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L186-L186), [212](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L212-L212), [240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L240-L240), 


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

305:        if (_stakingParams.serviceRegistry == address(0) || _stakingParams.activityChecker == address(0)) {	// @audit-issue

310:        if (_stakingParams.activityChecker.code.length == 0) {	// @audit-issue

342:        if (_stakingParams.proxyHash == 0) {	// @audit-issue

411:        if (success && returnData.length > 63 && (returnData.length % 32 == 0)) {	// @audit-issue

420:            if (success && returnData.length == 32) {	// @audit-issue

498:        if (reward == 0) {	// @audit-issue

721:        if (availableRewards == 0) {	// @audit-issue

734:        if (numStakingServices == maxNumServices) {	// @audit-issue

826:        if (serviceIds.length == 0) {	// @audit-issue

838:            if (evictServiceIds[idx] == serviceId) {	// @audit-issue

840:            } else if (serviceIds[idx] == serviceId) {	// @audit-issue

901:            if (eligibleServiceIds[i] == serviceId) {	// @audit-issue
```
[287](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L287-L287), [288](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L288-L288), [289](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L289-L289), [290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L290-L290), [305](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L305-L305), [310](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L310-L310), [342](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L342-L342), [411](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L411-L411), [420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L420-L420), [498](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L498-L498), [721](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L721-L721), [734](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L734-L734), [826](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L826-L826), [838](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L838-L838), [840](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L840-L840), [901](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L901-L901), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

63:        if (_stakingToken == address(0) || _serviceRegistryTokenUtility == address(0)) {	// @audit-issue
```
[63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L63-L63), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

29:        if (implementation == address(0)) {	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L29-L29), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

117:        if (newOwner == address(0)) {	// @audit-issue

179:        if (implementation == address(0)) {	// @audit-issue

184:        if (implementation.code.length == 0) {	// @audit-issue

211:        if (instance == address(0)) {	// @audit-issue

273:        if (implementation == address(0)) {	// @audit-issue
```
[117](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L117-L117), [179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L179-L179), [184](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L184-L184), [211](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L211-L211), [273](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L273-L273), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

207:        if (_ve == address(0)) {	// @audit-issue

260:        if (mapRemovedNominees[nomineeHash] == 0 && mapNomineeIds[nomineeHash] == 0) {	// @audit-issue

326:        if (account == address(0)) {	// @audit-issue

331:        if (chainId == 0) {	// @audit-issue

351:        if (account == bytes32(0)) {	// @audit-issue

375:        if (newOwner == address(0)) {	// @audit-issue

598:        if (id == 0) {	// @audit-issue

647:        if (mapRemovedNominees[nomineeHash] == 0) {	// @audit-issue

653:        if (oldSlope.power == 0) {	// @audit-issue

748:        if (id == 0) {	// @audit-issue

765:        if (id == 0) {	// @audit-issue

799:            if (mapNomineeIds[nomineeHash] == 0) {	// @audit-issue
```
[207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L207-L207), [260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L260-L260), [326](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L326-L326), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L331-L331), [351](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L351-L351), [375](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L375-L375), [598](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L598-L598), [647](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L647-L647), [653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L653-L653), [748](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L748-L748), [765](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L765-L765), [799](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L799-L799), 


#### Recommendation

Review your Solidity contracts to identify any computations that are performed multiple times with the same inputs. Cache the results of these computations in local variables and reuse them within the function or across function calls if the state remains unchanged.

### Consider using `bytes32` rather than a `string`
Using the bytes types for fixed-length strings is more efficient than having the EVM have to incur the overhead of string processing. Consider whether the value needs to be a string. A good reason to keep it as a string would be if the variable is defined in an interface that this project does not own.


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

10:    string public constant VERSION = "1.2.0";	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L10-L10), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

224:    string public constant VERSION = "0.2.0";	// @audit-issue
```
[224](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L224-L224), 


#### Recommendation

For fixed-length strings, prefer using 'bytes32' over 'string' to leverage EVM efficiency and reduce overhead. Evaluate the necessity of using a string, especially if the variable isn't part of an externally defined interface.

### The result of a function call should be cached rather than re-calling the function
The function calls in solidity are expensive. If the same result of the same function calls are to be used several times, the result should be cached to reduce the gas consumption of repeated calls.        

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

763:        if (_maxStakingIncentive > type(uint96).max) {	// @audit-issue: Function call is called multiple times at line(s) [764].

817:        if (eBond > type(uint96).max) {	// @audit-issue: Function call is called multiple times at line(s) [818].

835:        if (stakingIncentive > type(uint96).max) {	// @audit-issue: Function call is called multiple times at line(s) [836].

912:                    IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;	// @audit-issue: Function call `IVotingEscrow` is called multiple times at lines [911].

1252:            curInflationPerSecond = getInflationForYear(numYears) / ONE_YEAR;	// @audit-issue: Function call `getInflationForYear` is called multiple times at lines [1136].

1328:        uint256[] memory registriesSupply = new uint256[](2);	// @audit-issue: Function call is called multiple times at line(s) [1334].

1400:        uint256[] memory registriesSupply = new uint256[](2);	// @audit-issue: Function call is called multiple times at line(s) [1406].
```
[763](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L763-L763), [817](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L817-L817), [835](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L835-L835), [912](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L912-L912), [1252](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1252-L1252), [1328](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1328-L1328), [1400](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1400-L1400), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

429:            IDepositProcessor(depositProcessor).sendMessageNonEVM{value:msg.value}(stakingTarget,	// @audit-issue: Function call `IDepositProcessor` is called multiple times at lines [425].

490:                IDepositProcessor(depositProcessor).sendMessageBatch{value:valueAmounts[i]}(stakingTargetsEVM,	// @audit-issue: Function call `IDepositProcessor` is called multiple times at lines [494].

766:        uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(eCounter - 1);	// @audit-issue: Function call `ITokenomics` is called multiple times at lines [762, 769].

818:            success = ITreasury(treasury).withdrawToAccount(msg.sender, reward, topUp);	// @audit-issue: Function call `ITreasury` is called multiple times at lines [802].

822:                olasBalance = IToken(olas).balanceOf(msg.sender) - olasBalance;	// @audit-issue: Function call `balanceOf` is called multiple times at lines [815].

822:                olasBalance = IToken(olas).balanceOf(msg.sender) - olasBalance;	// @audit-issue: Function call `IToken` is called multiple times at lines [815].

878:        IVoteWeighting(voteWeighting).checkpointNominee(stakingTarget, chainId);	// @audit-issue: Function call `IVoteWeighting` is called multiple times at lines [893].

886:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue: Function call `ITokenomics` is called multiple times at lines [884].

984:            ITreasury(treasury).paused() == 2) {	// @audit-issue: Function call `ITreasury` is called multiple times at lines [1031].

1028:                uint256 olasBalance = IToken(olas).balanceOf(address(this));	// @audit-issue: Function call `balanceOf` is called multiple times at lines [1034].

1028:                uint256 olasBalance = IToken(olas).balanceOf(address(this));	// @audit-issue: Function call `IToken` is called multiple times at lines [1034].

1081:            ITreasury(treasury).paused() == 2) {	// @audit-issue: Function call `ITreasury` is called multiple times at lines [1109].

1112:                olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;	// @audit-issue: Function call `balanceOf` is called multiple times at lines [1106].

1112:                olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;	// @audit-issue: Function call `IToken` is called multiple times at lines [1106].

1148:            ITokenomics.StakingPoint memory stakingPoint = ITokenomics(tokenomics).mapEpochStakingPoints(j);	// @audit-issue: Function call `ITokenomics` is called multiple times at lines [1151, 1162].

1185:            revert Overflow(withheldAmount, type(uint96).max);	// @audit-issue: Function call is called multiple times at line(s) [1184].

1229:        if (withheldAmount > type(uint96).max) {	// @audit-issue: Function call is called multiple times at line(s) [1230].
```
[429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L429-L429), [490](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L490-L490), [766](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L766-L766), [818](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L818-L818), [822](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L822-L822), [822](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L822-L822), [878](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L878-L878), [886](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L886-L886), [984](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L984-L984), [1028](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1028-L1028), [1028](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1028-L1028), [1081](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1081-L1081), [1112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1112-L1112), [1112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1112-L1112), [1148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1148-L1148), [1185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1185-L1185), [1229](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1229-L1229), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

155:                revert ZeroValue();	// @audit-issue: Function call `ZeroValue` is called multiple times at lines [142].
```
[155](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L155-L155), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

112:                IToken(olas).transfer(timelock, refundAmount);	// @audit-issue: Function call `IToken` is called multiple times at lines [118].
```
[112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L112-L112), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

82:        if (_l1SourceChainId > type(uint16).max) {	// @audit-issue: Function call is called multiple times at line(s) [83].

120:        uint64 sequence = IBridge(l2MessageRelayer).sendPayloadToEvm{value: cost}(uint16(l1SourceChainId),	// @audit-issue: Function call `IBridge` is called multiple times at lines [112].
```
[82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L82-L82), [120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L120-L120), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

67:            bytes memory data = abi.encode(targets, stakingIncentives);	// @audit-issue: Function call `encode` is called multiple times at lines [73].
```
[67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L67-L67), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

51:        if (_wormholeTargetChainId > type(uint16).max) {	// @audit-issue: Function call is called multiple times at line(s) [52].
```
[51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L51-L51), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {	// @audit-issue: Function call `IToken` is called multiple times at lines [191].

290:            IToken(olas).approve(target, amount);	// @audit-issue: Function call `IToken` is called multiple times at lines [287].

440:        uint256 amount = IToken(olas).balanceOf(address(this));	// @audit-issue: Function call `IToken` is called multiple times at lines [443].
```
[189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L189-L189), [290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L290-L290), [440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L440-L440), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

199:        uint256 numServices = IStaking(instance).maxNumServices();	// @audit-issue: Function call `IStaking` is called multiple times at lines [192].
```
[199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L199-L199), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

343:            revert ZeroValue();	// @audit-issue: Function call `ZeroValue` is called multiple times at lines [291].

407:        (bool success, bytes memory returnData) = activityChecker.staticcall(activityData);	// @audit-issue: Function call `staticcall` is called multiple times at lines [417].

444:        uint256[] memory serviceIndexes = new uint256[](numEvictServices);	// @audit-issue: Function call is called multiple times at line(s) [440, 443].

442:        address[] memory multisigs = new address[](numEvictServices);	// @audit-issue: Function call is called multiple times at line(s) [441].

542:            eligibleServiceIds = new uint256[](size);	// @audit-issue: Function call is called multiple times at line(s) [541, 545, 543].

613:            finalEligibleServiceIds = new uint256[](numServices);	// @audit-issue: Function call is called multiple times at line(s) [614].

797:        IService(serviceRegistry).safeTransferFrom(msg.sender, address(this), serviceId);	// @audit-issue: Function call `IService` is called multiple times at lines [739].

771:                revert WrongServiceConfiguration(serviceId);	// @audit-issue: Function call `WrongServiceConfiguration` is called multiple times at lines [743, 748, 752].
```
[343](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L343-L343), [407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L407-L407), [444](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L444-L444), [442](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L442-L442), [542](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L542-L542), [613](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L613-L613), [797](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L797-L797), [771](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L771-L771), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

93:            uint256 bond = IServiceTokenUtility(serviceRegistryTokenUtility).getAgentBond(serviceId, serviceAgentIds[i]);	// @audit-issue: Function call `IServiceTokenUtility` is called multiple times at lines [77].
```
[93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L93-L93), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

195:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyImplementation(implementation)) {	// @audit-issue: Function call `IStakingVerifier` is called multiple times at lines [231].
```
[195](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L195-L195), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

216:        setNominees.push(Nominee(0, 0));	// @audit-issue: Function call `Nominee` is called multiple times at lines [218].

483:        int128 userSlope = IVEOLAS(ve).getLastUserPoint(msg.sender).slope;	// @audit-issue: Function call `IVEOLAS` is called multiple times at lines [488].

623:        bytes32 replacedNomineeHash = keccak256(abi.encode(nominee));	// @audit-issue: Function call `keccak256` is called multiple times at lines [594].

623:        bytes32 replacedNomineeHash = keccak256(abi.encode(nominee));	// @audit-issue: Function call `encode` is called multiple times at lines [594].
```
[216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L216-L216), [483](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L483-L483), [623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L623-L623), [623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L623-L623), 


#### Recommendation

Cache the result of function calls in Solidity instead of making repeated calls to the same function. This practice significantly reduces gas consumption by minimizing costly function call operations.

### Avoid updating storage when the value hasn't changed
Manipulating storage in solidity is gas-intensive. It can be optimized by avoiding unnecessary storage updates when the new value equals the existing value. If the old value is equal to the new value, not re-storing the value will avoid a Gsreset (2900 gas), potentially at the expense of a Gcoldsload (2100 gas) or a Gwarmaccess (100 gas).

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

455:        epochLen = uint32(_epochLen);	// @audit-issue

459:        donatorBlacklist = _donatorBlacklist;	// @audit-issue

622:        donatorBlacklist = _donatorBlacklist;	// @audit-issue

653:            devsPerCapital = uint72(_devsPerCapital);	// @audit-issue

661:            codePerDev = uint72(_codePerDev);	// @audit-issue

678:            nextEpochLen = uint32(_epochLen);	// @audit-issue

685:            nextVeOLASThreshold = uint96(_veOLASThreshold);	// @audit-issue

740:        mapEpochStakingPoints[eCounter].stakingFraction = uint8(_stakingFraction);	// @audit-issue

1020:        mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH = uint96(donationETH);	// @audit-issue
```
[455](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L455-L455), [459](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L459-L459), [622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L622-L622), [653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L653-L653), [661](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L661-L661), [678](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L678-L678), [685](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L685-L685), [740](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L740-L740), [1020](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1020-L1020), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

724:            mapChainIdDepositProcessors[chainIds[i]] = depositProcessors[i];	// @audit-issue

1247:        paused = pauseState;	// @audit-issue
```
[724](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L724-L724), [1247](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1247-L1247), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

120:        implementationsCheck = setCheck;	// @audit-issue

147:        implementationsCheck = setCheck;	// @audit-issue

157:            mapImplementations[implementations[i]] = statuses[i];	// @audit-issue
```
[120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L120-L120), [147](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L147-L147), [157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L157-L157), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

315:        metadataHash = _stakingParams.metadataHash;	// @audit-issue

316:        maxNumServices = _stakingParams.maxNumServices;	// @audit-issue

317:        rewardsPerSecond = _stakingParams.rewardsPerSecond;	// @audit-issue

318:        minStakingDeposit = _stakingParams.minStakingDeposit;	// @audit-issue

319:        maxNumInactivityPeriods = _stakingParams.maxNumInactivityPeriods;	// @audit-issue

320:        livenessPeriod = _stakingParams.livenessPeriod;	// @audit-issue

321:        timeForEmissions = _stakingParams.timeForEmissions;	// @audit-issue

322:        numAgentInstances = _stakingParams.numAgentInstances;	// @audit-issue

323:        serviceRegistry = _stakingParams.serviceRegistry;	// @audit-issue

324:        activityChecker = _stakingParams.activityChecker;	// @audit-issue

327:        threshold = _stakingParams.threshold;	// @audit-issue

328:        configHash = _stakingParams.configHash;	// @audit-issue

347:        proxyHash = _stakingParams.proxyHash;	// @audit-issue

350:        minStakingDuration = _stakingParams.minNumStakingPeriods * livenessPeriod;	// @audit-issue

353:        maxInactivityDuration = _stakingParams.maxNumInactivityPeriods * livenessPeriod;	// @audit-issue

356:        emissionsAmount = _stakingParams.rewardsPerSecond * _stakingParams.maxNumServices *	// @audit-issue
```
[315](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L315-L315), [316](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L316-L316), [317](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L317-L317), [318](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L318-L318), [319](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L319-L319), [320](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L320-L320), [321](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L321-L321), [322](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L322-L322), [323](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L323-L323), [324](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L324-L324), [327](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L327-L327), [328](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L328-L328), [347](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L347-L347), [350](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L350-L350), [353](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L353-L353), [356](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L356-L356), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

133:        verifier = newVerifier;	// @audit-issue
```
[133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L133-L133), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

392:        dispenser = newDispenser;	// @audit-issue

532:        pointsWeight[nomineeHash][nextTime].bias = _maxAndSub(_getWeight(account, chainId) + newBias, oldBias);	// @audit-issue
```
[392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L392-L392), [532](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L532-L532), 


#### Recommendation

Optimize gas usage by avoiding storage updates in Solidity when the new value is the same as the existing one. This prevents unnecessary gas expenditure from storage resets, balancing the cost against cold or warm storage access as needed.

### Functions guaranteed to `revert` when called by normal users can be marked `payable`
If a function modifier such as `onlyOwner` is used, the function will revert if a normal user tries to pay the function. Marking the function as `payable` will lower the gas cost for legitimate callers because the compiler will not include checks for whether a payment was provided.

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

Mark functions with access restrictions like 'onlyOwner' as 'payable' in Solidity. This reduces gas costs for legitimate callers by removing the compiler's checks for incoming payments, as the function will revert for unauthorized users anyway.

### `abi.encode()` is less efficient than `abi.encodePacked()`
When working with Solidity, it is important to pay attention to gas efficiency in order to optimize smart contracts. In this blog post, we will compare the gas costs of `abi.encode()` and `abi.encodepacked()` and explore why the latter is more efficient.Source: [reference](https://github.com/ConnorBlockchain/Solidity-Encode-Gas-Comparison)


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

346:        retainerHash = keccak256(abi.encode(IVoteWeighting.Nominee(retainer, block.chainid)));	// @audit-issue

871:        nomineeHash = keccak256(abi.encode(IVoteWeighting.Nominee(stakingTarget, chainId)));	// @audit-issue
```
[346](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L346-L346), [871](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L871-L871), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

179:            bytes memory submissionCostData = abi.encode(maxSubmissionCostToken, "");	// @audit-issue

187:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));	// @audit-issue
```
[179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L179-L179), [187](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L187-L187), 


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
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

121:            l1DepositProcessor, abi.encode(amount), 0, gasLimitMessage, uint16(l1SourceChainId), refundAccount);	// @audit-issue
```
[121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L121-L121), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

67:            bytes memory data = abi.encode(targets, stakingIncentives);	// @audit-issue

73:            bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));	// @audit-issue
```
[67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L67-L67), [73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L73-L73), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

34:        bytes memory data = abi.encode(amount);	// @audit-issue
```
[34](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L34-L34), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

89:        bytes memory data = abi.encode(targets, stakingIncentives);	// @audit-issue
```
[89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L89-L89), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

77:        bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(amount));	// @audit-issue
```
[77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L77-L77), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

197:                bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));	// @audit-issue

279:        bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));	// @audit-issue
```
[197](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L197-L197), [279](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L279-L279), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

71:            IBridge(l1TokenRelayer).depositFor(l2TargetDispenser, olas, abi.encode(transferAmount));	// @audit-issue

75:        bytes memory data = abi.encode(targets, stakingIncentives);	// @audit-issue
```
[71](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L71-L71), [75](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L75-L75), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

259:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

294:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

428:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

475:        bytes32 nomineeHash = keccak256(abi.encode(Nominee(account, chainId)));	// @audit-issue

594:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

623:        bytes32 replacedNomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

644:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

677:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

723:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

735:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

796:            bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue
```
[259](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L259-L259), [294](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L294-L294), [428](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L428-L428), [475](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L475-L475), [594](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L594-L594), [623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L623-L623), [644](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L644-L644), [677](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L677-L677), [723](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L723-L723), [735](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L735-L735), [796](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L796-L796), 


#### Recommendation

Optimize gas usage by preferring 'abi.encodePacked()' over 'abi.encode()' where appropriate, as 'abi.encodePacked()' is generally more gas-efficient. However, ensure its suitability based on the data type and structure requirements, as 'abi.encodePacked()' can lead to ambiguity in certain cases.

### Multiple mappings can be replaced with a single struct mapping
Saves a storage slot for the mapping. Depending on the circumstances and sizes of types, can avoid a Gsset (20000 gas) per mapping combined. Reads and subsequent writes can also be cheaper when a function requires both values and they both fit in the same storage slot. Finally, if both fields are accessed in the same function, can save ~42 gas per access due to [not having to recalculate the key's keccak256 hash](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0) (Gkeccak256 - 30 gas) and that calculation's associated stack operations.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

363:    mapping(address => uint256) public mapOwnerTopUps;	// @audit-issue: Can be combined with: 
	 mapOwnerRewards in Tokenomics at line: 361
```
[363](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L363-L363), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

304:    mapping(bytes32 => uint256) public mapRemovedNomineeEpochs;	// @audit-issue: Can be combined with: 
	 mapLastClaimedStakingEpochs in Dispenser at line: 302
```
[304](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L304-L304), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

54:    mapping(bytes32 => bool) public mapDeliveryHashes;	// @audit-issue: Can be combined with: 
	 stakingQueueingNonces in DefaultTargetDispenserL2 at line: 93
```
[54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L54-L54), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

46:        FxBaseRootTunnel(_checkpointManager, _l1MessageRelayer)	// @audit-issue: Can be combined with: 
	 processedExits in FxBaseRootTunnel at line: 46
```
[46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L46-L46), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

194:    mapping(bytes32 => uint256) public timeWeight;	// @audit-issue: Can be combined with: 
	 mapNomineeIds in VoteWeighting at line: 172
	 mapRemovedNominees in VoteWeighting at line: 174
```
[194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L194-L194), 


#### Recommendation

Refactor your Solidity contracts to replace multiple related mappings with a single mapping that uses a struct. This struct should contain all the fields that were previously in separate mappings. This consolidation optimizes storage usage by reducing the number of storage slots and hash computations required, leading to significant gas savings. Ensure that the fields within the struct are logically related and frequently accessed or modified together. This strategy is most effective when the size of struct fields allows them to fit within a single storage slot, maximizing the benefits of the EVM's storage packing capabilities.

### `x += y` costs more gas than `x = x + y` for stack variables
Not inlining costs 20 to 40 gas because of two extra JUMP instructions and additional stack operations needed for function calls.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

798:            eBond -= amount;	// @audit-issue

854:            totalIncentives *= mapEpochTokenomics[epochNum].unitPoints[unitType].rewardUnitFraction;	// @audit-issue

869:            totalIncentives *= mapEpochTokenomics[epochNum].epochPoint.totalTopUpsOLAS;	// @audit-issue

870:            totalIncentives *= mapEpochTokenomics[epochNum].unitPoints[unitType].topUpUnitFraction;	// @audit-issue

1019:        donationETH += mapEpochTokenomics[curEpoch].epochPoint.totalDonationsETH;	// @audit-issue

1138:            inflationPerEpoch += (block.timestamp - yearEndTime) * curInflationPerSecond;	// @audit-issue

1254:            inflationPerEpoch += (block.timestamp + curEpochLen - yearEndTime) * curInflationPerSecond;	// @audit-issue

1270:        curMaxBond += effectiveBond;	// @audit-issue

1370:            reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;	// @audit-issue

1373:            topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;	// @audit-issue

1438:                    totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].rewardUnitFraction;	// @audit-issue

1440:                    reward += totalIncentives / 100;	// @audit-issue

1447:                    totalIncentives *= mapEpochTokenomics[lastEpoch].epochPoint.totalTopUpsOLAS;	// @audit-issue

1448:                    totalIncentives *= mapEpochTokenomics[lastEpoch].unitPoints[unitTypes[i]].topUpUnitFraction;	// @audit-issue

1451:                    topUp += totalIncentives / sumUnitIncentives;	// @audit-issue

1456:            reward += mapUnitIncentives[unitTypes[i]][unitIds[i]].reward;	// @audit-issue

1458:            topUp += mapUnitIncentives[unitTypes[i]][unitIds[i]].topUp;	// @audit-issue
```
[798](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L798-L798), [854](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L854-L854), [869](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L869-L869), [870](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L870-L870), [1019](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1019-L1019), [1138](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1138-L1138), [1254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1254-L1254), [1270](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1270-L1270), [1370](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1370-L1370), [1373](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1373-L1373), [1438](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1438-L1438), [1440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1440-L1440), [1447](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1447-L1447), [1448](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1448-L1448), [1451](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1451-L1451), [1456](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1456-L1456), [1458](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1458-L1458), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

51:            numYears -= 9;	// @audit-issue

59:                supplyCap += (supplyCap * maxMintCapFraction) / 100;	// @audit-issue

88:            numYears -= 9;	// @audit-issue

96:                supplyCap += (supplyCap * maxMintCapFraction) / 100;	// @audit-issue
```
[51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L51-L51), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L59-L59), [88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L88-L88), [96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L96-L96), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

540:            totalValueAmount += valueAmounts[i];	// @audit-issue

609:                transferAmounts[i] += stakingIncentive;	// @audit-issue

610:                totalAmounts[0] += stakingIncentive;	// @audit-issue

611:                totalAmounts[2] += returnAmount;	// @audit-issue

619:                        withheldAmount -= transferAmounts[i];	// @audit-issue

622:                        transferAmounts[i] -= withheldAmount;	// @audit-issue

630:            totalAmounts[1] += transferAmounts[i];	// @audit-issue

924:                    returnAmount += stakingIncentive - availableStakingAmount;	// @audit-issue

933:                    normalizedStakingAmount *= 10 ** (18 - bridgingDecimals);	// @audit-issue

936:                    returnAmount += stakingIncentive - normalizedStakingAmount;	// @audit-issue

941:                totalStakingIncentive += stakingIncentive;	// @audit-issue

944:            totalReturnAmount += returnAmount;	// @audit-issue

1016:                    withheldAmount -= transferAmount;	// @audit-issue

1020:                    transferAmount -= withheldAmount;	// @audit-issue

1157:            totalReturnAmount += stakingPoint.stakingIncentive * stakingWeight;	// @audit-issue

1159:        totalReturnAmount /= 1e18;	// @audit-issue

1222:            normalizedAmount *= 10 ** (18 - bridgingDecimals);	// @audit-issue
```
[540](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L540-L540), [609](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L609-L609), [610](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L610-L610), [611](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L611-L611), [619](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L619-L619), [622](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L622-L622), [630](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L630-L630), [924](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L924-L924), [933](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L933-L933), [936](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L936-L936), [941](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L941-L941), [944](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L944-L944), [1016](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1016-L1016), [1020](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1020-L1020), [1157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1157-L1157), [1159](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1159-L1159), [1222](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1222-L1222), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

172:                localWithheldAmount += amount;	// @audit-issue

182:                localWithheldAmount += targetWithheldAmount;	// @audit-issue
```
[172](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L172-L172), [182](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L182-L182), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

575:                    totalRewards += eligibleServiceRewards[numServices];	// @audit-issue

624:                    updatedTotalRewards += updatedReward;	// @audit-issue

634:                updatedTotalRewards += updatedReward;	// @audit-issue

639:                    updatedReward += lastAvailableRewards - updatedTotalRewards;	// @audit-issue

657:                lastAvailableRewards -= totalRewards;	// @audit-issue

922:        reward += calculateStakingLastReward(serviceId);	// @audit-issue
```
[575](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L575-L575), [624](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L624-L624), [634](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L634-L634), [639](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L639-L639), [657](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L657-L657), [922](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L922-L922), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

231:            t += WEEK;	// @audit-issue

234:                pt.bias -= dBias;	// @audit-issue

236:                pt.slope -= dSlope;	// @audit-issue

271:            t += WEEK;	// @audit-issue

274:                pt.bias -= dBias;	// @audit-issue

276:                pt.slope -= dSlope;	// @audit-issue
```
[231](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L231-L231), [234](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L234-L234), [236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L236-L236), [271](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L271-L271), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L274-L274), [276](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L276-L276), 


#### Recommendation

Prefer using 'x = x + y' over 'x += y' for state variable assignments in Solidity to save gas. The latter incurs extra costs due to additional JUMP instructions and stack operations associated with non-inlined function calls.

### Use `s.x = s.x + y` instead of `s.x += y` for memory structs
Using the `s.x = s.x + y` instead of `s.x += y` for memory structs can save **100 gas**. The same applies to `-=`, `*=`, etc.

```solidity
Path: ./governance/contracts/VoteWeighting.sol

234:                pt.bias -= dBias;	// @audit-issue

236:                pt.slope -= dSlope;	// @audit-issue

274:                pt.bias -= dBias;	// @audit-issue

276:                pt.slope -= dSlope;	// @audit-issue
```
[234](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L234-L234), [236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L236-L236), [274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L274-L274), [276](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L276-L276), 


#### Recommendation

Use explicit arithmetic assignment operations like `s.x = s.x + y` instead of compound assignments `s.x += y` for memory structs in Solidity. This approach saves around 100 gas per operation, making it preferable in scenarios involving frequent memory struct manipulations. Apply the same optimization for other arithmetic operations such as `-=` and `*=`, to enhance overall gas efficiency in your contracts.

### Use calldata instead of memory for function arguments that do not get mutated
Mark data types as `calldata` instead of `memory` where possible. This makes it so that the data is not automatically loaded into memory. If the data passed into the function does not need to be changed (like updating values in an array), it can be passed in as `calldata`. The one exception to this is if the argument must later be passed into another function that takes an argument that specifies `memory` storage.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

884:    function _trackServiceDonations(
885:        address donator,
886:        uint256[] memory serviceIds,	// @audit-issue
887:        uint256[] memory amounts,
888:        uint256 curEpoch
889:    ) internal {

884:    function _trackServiceDonations(
885:        address donator,
886:        uint256[] memory serviceIds,
887:        uint256[] memory amounts,	// @audit-issue
888:        uint256 curEpoch
889:    ) internal {

990:    function trackServiceDonations(
991:        address donator,
992:        uint256[] memory serviceIds,	// @audit-issue
993:        uint256[] memory amounts,
994:        uint256 donationETH
995:    ) external {

990:    function trackServiceDonations(
991:        address donator,
992:        uint256[] memory serviceIds,
993:        uint256[] memory amounts,	// @audit-issue
994:        uint256 donationETH
995:    ) external {

1308:    function accountOwnerIncentives(
1309:        address account,
1310:        uint256[] memory unitTypes,	// @audit-issue
1311:        uint256[] memory unitIds
1312:    ) external returns (uint256 reward, uint256 topUp) {

1308:    function accountOwnerIncentives(
1309:        address account,
1310:        uint256[] memory unitTypes,
1311:        uint256[] memory unitIds	// @audit-issue
1312:    ) external returns (uint256 reward, uint256 topUp) {

1385:    function getOwnerIncentives(
1386:        address account,
1387:        uint256[] memory unitTypes,	// @audit-issue
1388:        uint256[] memory unitIds
1389:    ) external view returns (uint256 reward, uint256 topUp) {

1385:    function getOwnerIncentives(
1386:        address account,
1387:        uint256[] memory unitTypes,
1388:        uint256[] memory unitIds	// @audit-issue
1389:    ) external view returns (uint256 reward, uint256 topUp) {
```
[886](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L884-L889), [887](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L884-L889), [992](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L990-L995), [993](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L990-L995), [1310](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1308-L1312), [1311](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1308-L1312), [1387](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1385-L1389), [1388](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1385-L1389), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

408:    function _distributeStakingIncentives(
409:        uint256 chainId,
410:        bytes32 stakingTarget,
411:        uint256 stakingIncentive,
412:        bytes memory bridgePayload,	// @audit-issue
413:        uint256 transferAmount
414:    ) internal {

441:    function _distributeStakingIncentivesBatch(
442:        uint256[] memory chainIds,	// @audit-issue
443:        bytes32[][] memory stakingTargets,
444:        uint256[][] memory stakingIncentives,
445:        bytes[] memory bridgePayloads,
446:        uint256[] memory transferAmounts,
447:        uint256[] memory valueAmounts
448:    ) internal {

441:    function _distributeStakingIncentivesBatch(
442:        uint256[] memory chainIds,
443:        bytes32[][] memory stakingTargets,	// @audit-issue
444:        uint256[][] memory stakingIncentives,
445:        bytes[] memory bridgePayloads,
446:        uint256[] memory transferAmounts,
447:        uint256[] memory valueAmounts
448:    ) internal {

441:    function _distributeStakingIncentivesBatch(
442:        uint256[] memory chainIds,
443:        bytes32[][] memory stakingTargets,
444:        uint256[][] memory stakingIncentives,	// @audit-issue
445:        bytes[] memory bridgePayloads,
446:        uint256[] memory transferAmounts,
447:        uint256[] memory valueAmounts
448:    ) internal {

441:    function _distributeStakingIncentivesBatch(
442:        uint256[] memory chainIds,
443:        bytes32[][] memory stakingTargets,
444:        uint256[][] memory stakingIncentives,
445:        bytes[] memory bridgePayloads,	// @audit-issue
446:        uint256[] memory transferAmounts,
447:        uint256[] memory valueAmounts
448:    ) internal {

441:    function _distributeStakingIncentivesBatch(
442:        uint256[] memory chainIds,
443:        bytes32[][] memory stakingTargets,
444:        uint256[][] memory stakingIncentives,
445:        bytes[] memory bridgePayloads,
446:        uint256[] memory transferAmounts,	// @audit-issue
447:        uint256[] memory valueAmounts
448:    ) internal {

441:    function _distributeStakingIncentivesBatch(
442:        uint256[] memory chainIds,
443:        bytes32[][] memory stakingTargets,
444:        uint256[][] memory stakingIncentives,
445:        bytes[] memory bridgePayloads,
446:        uint256[] memory transferAmounts,
447:        uint256[] memory valueAmounts	// @audit-issue
448:    ) internal {

506:    function _checkOrderAndValues(
507:        uint256[] memory chainIds,	// @audit-issue
508:        bytes32[][] memory stakingTargets,
509:        bytes[] memory bridgePayloads,
510:        uint256[] memory valueAmounts
511:    ) internal {

506:    function _checkOrderAndValues(
507:        uint256[] memory chainIds,
508:        bytes32[][] memory stakingTargets,	// @audit-issue
509:        bytes[] memory bridgePayloads,
510:        uint256[] memory valueAmounts
511:    ) internal {

506:    function _checkOrderAndValues(
507:        uint256[] memory chainIds,
508:        bytes32[][] memory stakingTargets,
509:        bytes[] memory bridgePayloads,	// @audit-issue
510:        uint256[] memory valueAmounts
511:    ) internal {

506:    function _checkOrderAndValues(
507:        uint256[] memory chainIds,
508:        bytes32[][] memory stakingTargets,
509:        bytes[] memory bridgePayloads,
510:        uint256[] memory valueAmounts	// @audit-issue
511:    ) internal {

573:    function _calculateStakingIncentivesBatch(
574:        uint256 numClaimedEpochs,
575:        uint256[] memory chainIds,	// @audit-issue
576:        bytes32[][] memory stakingTargets
577:    ) internal returns (

573:    function _calculateStakingIncentivesBatch(
574:        uint256 numClaimedEpochs,
575:        uint256[] memory chainIds,
576:        bytes32[][] memory stakingTargets	// @audit-issue
577:    ) internal returns (

705:    function setDepositProcessorChainIds(address[] memory depositProcessors, uint256[] memory chainIds) external {	// @audit-issue

789:    function claimOwnerIncentives(
790:        uint256[] memory unitTypes,	// @audit-issue
791:        uint256[] memory unitIds
792:    ) external returns (uint256 reward, uint256 topUp) {

789:    function claimOwnerIncentives(
790:        uint256[] memory unitTypes,
791:        uint256[] memory unitIds	// @audit-issue
792:    ) external returns (uint256 reward, uint256 topUp) {

953:    function claimStakingIncentives(
954:        uint256 numClaimedEpochs,
955:        uint256 chainId,
956:        bytes32 stakingTarget,
957:        bytes memory bridgePayload	// @audit-issue
958:    ) external payable {

1058:    function claimStakingIncentivesBatch(
1059:        uint256 numClaimedEpochs,
1060:        uint256[] memory chainIds,	// @audit-issue
1061:        bytes32[][] memory stakingTargets,
1062:        bytes[] memory bridgePayloads,
1063:        uint256[] memory valueAmounts
1064:    ) external payable {

1058:    function claimStakingIncentivesBatch(
1059:        uint256 numClaimedEpochs,
1060:        uint256[] memory chainIds,
1061:        bytes32[][] memory stakingTargets,	// @audit-issue
1062:        bytes[] memory bridgePayloads,
1063:        uint256[] memory valueAmounts
1064:    ) external payable {

1058:    function claimStakingIncentivesBatch(
1059:        uint256 numClaimedEpochs,
1060:        uint256[] memory chainIds,
1061:        bytes32[][] memory stakingTargets,
1062:        bytes[] memory bridgePayloads,	// @audit-issue
1063:        uint256[] memory valueAmounts
1064:    ) external payable {

1058:    function claimStakingIncentivesBatch(
1059:        uint256 numClaimedEpochs,
1060:        uint256[] memory chainIds,
1061:        bytes32[][] memory stakingTargets,
1062:        bytes[] memory bridgePayloads,
1063:        uint256[] memory valueAmounts	// @audit-issue
1064:    ) external payable {
```
[412](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L408-L414), [442](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L441-L448), [443](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L441-L448), [444](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L441-L448), [445](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L441-L448), [446](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L441-L448), [447](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L441-L448), [507](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L506-L511), [508](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L506-L511), [509](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L506-L511), [510](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L506-L511), [575](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L573-L577), [576](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L573-L577), [705](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L705-L705), [790](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L789-L792), [791](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L789-L792), [957](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L953-L958), [1060](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1058-L1064), [1061](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1058-L1064), [1062](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1058-L1064), [1063](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1058-L1064), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

119:    function _sendMessage(
120:        address[] memory targets,	// @audit-issue
121:        uint256[] memory stakingIncentives,
122:        bytes memory bridgePayload,
123:        uint256 transferAmount
124:    ) internal override returns (uint256 sequence) {

119:    function _sendMessage(
120:        address[] memory targets,
121:        uint256[] memory stakingIncentives,	// @audit-issue
122:        bytes memory bridgePayload,
123:        uint256 transferAmount
124:    ) internal override returns (uint256 sequence) {

119:    function _sendMessage(
120:        address[] memory targets,
121:        uint256[] memory stakingIncentives,
122:        bytes memory bridgePayload,	// @audit-issue
123:        uint256 transferAmount
124:    ) internal override returns (uint256 sequence) {

196:    function receiveMessage(bytes memory data) external {	// @audit-issue
```
[120](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L119-L124), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L119-L124), [122](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L119-L124), [196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L196-L196), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

56:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {	// @audit-issue

96:    function receiveMessage(bytes memory data) external payable {	// @audit-issue
```
[56](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L56-L56), [96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L96-L96), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

107:    function _receiveMessage(address l1Relayer, address l2Dispenser, bytes memory data) internal virtual {	// @audit-issue

132:    function sendMessage(
133:        address target,
134:        uint256 stakingIncentive,
135:        bytes memory bridgePayload,	// @audit-issue
136:        uint256 transferAmount
137:    ) external virtual payable {

164:    function sendMessageBatch(
165:        address[] memory targets,	// @audit-issue
166:        uint256[] memory stakingIncentives,
167:        bytes memory bridgePayload,
168:        uint256 transferAmount
169:    ) external virtual payable {

164:    function sendMessageBatch(
165:        address[] memory targets,
166:        uint256[] memory stakingIncentives,	// @audit-issue
167:        bytes memory bridgePayload,
168:        uint256 transferAmount
169:    ) external virtual payable {

164:    function sendMessageBatch(
165:        address[] memory targets,
166:        uint256[] memory stakingIncentives,
167:        bytes memory bridgePayload,	// @audit-issue
168:        uint256 transferAmount
169:    ) external virtual payable {
```
[107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L107-L107), [135](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L132-L137), [165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L164-L169), [166](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L164-L169), [167](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L164-L169), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

53:    function _sendMessage(uint256 amount, bytes memory) internal override {	// @audit-issue

66:    function receiveMessage(bytes memory data) external payable {	// @audit-issue
```
[53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L53-L53), [66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L66-L66), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

95:    function _sendMessage(
96:        address[] memory targets,	// @audit-issue
97:        uint256[] memory stakingIncentives,
98:        bytes memory bridgePayload,
99:        uint256 transferAmount
100:    ) internal override returns (uint256 sequence) {

95:    function _sendMessage(
96:        address[] memory targets,
97:        uint256[] memory stakingIncentives,	// @audit-issue
98:        bytes memory bridgePayload,
99:        uint256 transferAmount
100:    ) internal override returns (uint256 sequence) {

95:    function _sendMessage(
96:        address[] memory targets,
97:        uint256[] memory stakingIncentives,
98:        bytes memory bridgePayload,	// @audit-issue
99:        uint256 transferAmount
100:    ) internal override returns (uint256 sequence) {

148:    function receiveMessage(bytes memory data) external payable {	// @audit-issue
```
[96](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L95-L100), [97](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L95-L100), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L95-L100), [148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L148-L148), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

86:    function _deposit(address[] memory targets, uint256[] memory stakingIncentives) internal {	// @audit-issue

130:    function sendMessage(
131:        address target,
132:        uint256 stakingIncentive,
133:        bytes memory,	// @audit-issue
134:        uint256
135:    ) external {

155:    function sendMessageBatch(
156:        address[] memory targets,	// @audit-issue
157:        uint256[] memory stakingIncentives,
158:        bytes memory,
159:        uint256
160:    ) external {

155:    function sendMessageBatch(
156:        address[] memory targets,
157:        uint256[] memory stakingIncentives,	// @audit-issue
158:        bytes memory,
159:        uint256
160:    ) external {

155:    function sendMessageBatch(
156:        address[] memory targets,
157:        uint256[] memory stakingIncentives,
158:        bytes memory,	// @audit-issue
159:        uint256
160:    ) external {
```
[86](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L86-L86), [133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L130-L135), [156](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L155-L160), [157](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L155-L160), [158](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L155-L160), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

89:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {	// @audit-issue

133:    function receivePayloadAndTokens(
134:        bytes memory data,	// @audit-issue
135:        TokenReceived[] memory receivedTokens,
136:        bytes32 sourceProcessor,
137:        uint16 sourceChainId,
138:        bytes32 deliveryHash
139:    ) internal override {

133:    function receivePayloadAndTokens(
134:        bytes memory data,
135:        TokenReceived[] memory receivedTokens,	// @audit-issue
136:        bytes32 sourceProcessor,
137:        uint16 sourceChainId,
138:        bytes32 deliveryHash
139:    ) internal override {
```
[89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L89-L89), [134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L133-L139), [135](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L133-L139), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

51:    function _sendMessage(
52:        address[] memory targets,	// @audit-issue
53:        uint256[] memory stakingIncentives,
54:        bytes memory bridgePayload,
55:        uint256 transferAmount
56:    ) internal override returns (uint256 sequence) {

51:    function _sendMessage(
52:        address[] memory targets,
53:        uint256[] memory stakingIncentives,	// @audit-issue
54:        bytes memory bridgePayload,
55:        uint256 transferAmount
56:    ) internal override returns (uint256 sequence) {

51:    function _sendMessage(
52:        address[] memory targets,
53:        uint256[] memory stakingIncentives,
54:        bytes memory bridgePayload,	// @audit-issue
55:        uint256 transferAmount
56:    ) internal override returns (uint256 sequence) {

98:    function receiveMessage(bytes memory data) external {	// @audit-issue
```
[52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L51-L56), [53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L51-L56), [54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L51-L56), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L98-L98), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

32:    function _sendMessage(uint256 amount, bytes memory) internal override {	// @audit-issue

52:    function _processMessageFromRoot(uint256, address sender, bytes memory data) internal override {	// @audit-issue
```
[32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L32-L32), [52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L52-L52), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

59:    function _sendMessage(
60:        address[] memory targets,	// @audit-issue
61:        uint256[] memory stakingIncentives,
62:        bytes memory bridgePayload,
63:        uint256 transferAmount
64:    ) internal override returns (uint256 sequence) {

59:    function _sendMessage(
60:        address[] memory targets,
61:        uint256[] memory stakingIncentives,	// @audit-issue
62:        bytes memory bridgePayload,
63:        uint256 transferAmount
64:    ) internal override returns (uint256 sequence) {

59:    function _sendMessage(
60:        address[] memory targets,
61:        uint256[] memory stakingIncentives,
62:        bytes memory bridgePayload,	// @audit-issue
63:        uint256 transferAmount
64:    ) internal override returns (uint256 sequence) {

106:    function receiveWormholeMessages(
107:        bytes memory data,	// @audit-issue
108:        bytes[] memory,
109:        bytes32 sourceAddress,
110:        uint16 sourceChain,
111:        bytes32 deliveryHash
112:    ) external {

106:    function receiveWormholeMessages(
107:        bytes memory data,
108:        bytes[] memory,	// @audit-issue
109:        bytes32 sourceAddress,
110:        uint16 sourceChain,
111:        bytes32 deliveryHash
112:    ) external {
```
[60](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L59-L64), [61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L59-L64), [62](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L59-L64), [107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L106-L112), [108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L106-L112), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

58:    function _sendMessage(uint256 amount, bytes memory bridgePayload) internal override {	// @audit-issue

87:    function receiveMessage(bytes memory data) external {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L58-L58), [87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L87-L87), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

139:    function _processData(bytes memory data) internal {	// @audit-issue

224:    function _receiveMessage(
225:        address messageRelayer,
226:        address sourceProcessor,
227:        bytes memory data	// @audit-issue
228:    ) internal virtual {

311:    function processDataMaintenance(bytes memory data) external {	// @audit-issue

323:    function syncWithheldTokens(bytes memory bridgePayload) external payable {	// @audit-issue
```
[139](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L139-L139), [227](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L224-L228), [311](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L311-L311), [323](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L323-L323), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

57:    function _sendMessage(
58:        address[] memory targets,	// @audit-issue
59:        uint256[] memory stakingIncentives,
60:        bytes memory,
61:        uint256 transferAmount
62:    ) internal override returns (uint256 sequence) {

57:    function _sendMessage(
58:        address[] memory targets,
59:        uint256[] memory stakingIncentives,	// @audit-issue
60:        bytes memory,
61:        uint256 transferAmount
62:    ) internal override returns (uint256 sequence) {

57:    function _sendMessage(
58:        address[] memory targets,
59:        uint256[] memory stakingIncentives,
60:        bytes memory,	// @audit-issue
61:        uint256 transferAmount
62:    ) internal override returns (uint256 sequence) {

91:    function _processMessageFromChild(bytes memory data) internal override {	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L57-L62), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L57-L62), [60](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L57-L62), [91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L91-L91), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

131:    function setImplementationsStatuses(
132:        address[] memory implementations,	// @audit-issue
133:        bool[] memory statuses,
134:        bool setCheck
135:    ) external {

131:    function setImplementationsStatuses(
132:        address[] memory implementations,
133:        bool[] memory statuses,	// @audit-issue
134:        bool setCheck
135:    ) external {
```
[132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L131-L135), [133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L131-L135), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

20:    function initialize(StakingParams memory _stakingParams) external {	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L20-L20), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

50:    function isRatioPass(
51:        uint256[] memory curNonces,	// @audit-issue
52:        uint256[] memory lastNonces,
53:        uint256 ts
54:    ) external view virtual returns (bool ratioPass) {

50:    function isRatioPass(
51:        uint256[] memory curNonces,
52:        uint256[] memory lastNonces,	// @audit-issue
53:        uint256 ts
54:    ) external view virtual returns (bool ratioPass) {
```
[51](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L50-L54), [52](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L50-L54), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

278:    function _initialize(
279:        StakingParams memory _stakingParams	// @audit-issue
280:    ) internal {

366:    function _checkTokenStakingDeposit(
367:        uint256 serviceId,
368:        uint256 stakingDeposit,
369:        uint32[] memory	// @audit-issue
370:    ) internal view virtual {

398:    function _checkRatioPass(
399:        address multisig,
400:        uint256[] memory lastNonces,	// @audit-issue
401:        uint256 ts
402:    ) internal view returns (bool ratioPass, uint256[] memory currentNonces)

430:    function _evict(
431:        uint256[] memory evictServiceIds,	// @audit-issue
432:        uint256[] memory serviceInactivity,
433:        uint256 numEvictServices
434:    ) internal {

430:    function _evict(
431:        uint256[] memory evictServiceIds,
432:        uint256[] memory serviceInactivity,	// @audit-issue
433:        uint256 numEvictServices
434:    ) internal {
```
[279](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L278-L280), [369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L366-L370), [400](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L398-L402), [431](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L430-L434), [432](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L430-L434), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

54:    function initialize(
55:        StakingParams memory _stakingParams,	// @audit-issue
56:        address _serviceRegistryTokenUtility,
57:        address _stakingToken
58:    ) external

74:    function _checkTokenStakingDeposit(uint256 serviceId, uint256, uint32[] memory serviceAgentIds) internal view override {	// @audit-issue
```
[55](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L54-L58), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L74-L74), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

168:    function createStakingInstance(
169:        address implementation,
170:        bytes memory initPayload	// @audit-issue
171:    ) external returns (address payable instance) {
```
[170](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L168-L171), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

292:    function _addNominee(Nominee memory nominee) internal {	// @audit-issue

563:    function voteForNomineeWeightsBatch(
564:        bytes32[] memory accounts,	// @audit-issue
565:        uint256[] memory chainIds,
566:        uint256[] memory weights
567:    ) external {

563:    function voteForNomineeWeightsBatch(
564:        bytes32[] memory accounts,
565:        uint256[] memory chainIds,	// @audit-issue
566:        uint256[] memory weights
567:    ) external {

563:    function voteForNomineeWeightsBatch(
564:        bytes32[] memory accounts,
565:        uint256[] memory chainIds,
566:        uint256[] memory weights	// @audit-issue
567:    ) external {

779:    function getNextAllowedVotingTimes(
780:        bytes32[] memory accounts,	// @audit-issue
781:        uint256[] memory chainIds,
782:        address[] memory voters
783:    ) external view returns (uint256[] memory nextAllowedVotingTimes) {

779:    function getNextAllowedVotingTimes(
780:        bytes32[] memory accounts,
781:        uint256[] memory chainIds,	// @audit-issue
782:        address[] memory voters
783:    ) external view returns (uint256[] memory nextAllowedVotingTimes) {

779:    function getNextAllowedVotingTimes(
780:        bytes32[] memory accounts,
781:        uint256[] memory chainIds,
782:        address[] memory voters	// @audit-issue
783:    ) external view returns (uint256[] memory nextAllowedVotingTimes) {
```
[292](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L292-L292), [564](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L563-L567), [565](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L563-L567), [566](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L563-L567), [780](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L779-L783), [781](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L779-L783), [782](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L779-L783), 


#### Recommendation

To optimize gas usage in your Solidity functions, mark data types as `calldata` instead of `memory` wherever applicable. This prevents unnecessary data loading into memory. Use `calldata` for function arguments that do not require changes within the function, except when passing them into another function that explicitly requires `memory` storage.

### Nesting `if` statements that uses `&&` saves gas
In Solidity, the way conditional checks are structured can impact the gas consumption of a transaction. When conditions are combined using `&&` within an `if` statement, Solidity short-circuits the evaluation, meaning that if the first condition is `false`, the subsequent conditions won't be evaluated. This behavior can lead to gas savings compared to using separate nested `if` statements because not all conditions might need to be checked every time. By efficiently structuring these conditional checks, contracts can optimize the gas required for execution, leading to reduced costs for users.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

670:        if (_epsilonRate > 0 && _epsilonRate <= 17e18) {	// @audit-issue

677:        if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {	// @audit-issue

952:                        if (topUpEligible && incentiveFlags[unitType + 2]) {	// @audit-issue

1003:        if (bList != address(0) && IDonatorBlacklist(bList).isDonatorBlacklisted(donator)) {	// @audit-issue

1363:            if (lastEpoch > 0 && lastEpoch < curEpoch) {	// @audit-issue

1433:            if (lastEpoch > 0 && lastEpoch < curEpoch) {	// @audit-issue
```
[670](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L670-L670), [677](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L677-L677), [952](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L952-L952), [1003](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1003-L1003), [1363](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1363-L1363), [1433](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1433-L1433), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

383:        if (epochAfterRemoved > 1 && firstClaimedEpoch >= epochAfterRemoved) {	// @audit-issue

392:        if (epochAfterRemoved > 1 && lastClaimedEpoch > epochAfterRemoved) {	// @audit-issue
```
[383](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L383-L383), [392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L392-L392), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

165:            if (success && returnData.length == 32) {	// @audit-issue

189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {	// @audit-issue
```
[165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L165-L165), [189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L189-L189), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

181:        if (implementationsCheck && !mapImplementations[implementation]) {	// @audit-issue
```
[181](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L181-L181), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

57:        if (ts > 0 && curNonces[0] > lastNonces[0]) {	// @audit-issue
```
[57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L57-L57), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

411:        if (success && returnData.length > 63 && (returnData.length % 32 == 0)) {	// @audit-issue

420:            if (success && returnData.length == 32) {	// @audit-issue

536:        if (block.timestamp - tsCheckpointLast >= livenessPeriod && lastAvailableRewards > 0) {	// @audit-issue

747:        if (configHash != 0 && configHash != service.configHash) {	// @audit-issue

751:        if (threshold > 0 && threshold != service.threshold) {	// @audit-issue

818:        if (ts <= minStakingDuration && availableRewards > 0) {	// @audit-issue
```
[411](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L411-L411), [420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L420-L420), [536](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L536-L536), [747](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L747-L747), [751](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L751-L751), [818](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L818-L818), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

195:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyImplementation(implementation)) {	// @audit-issue

231:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyInstance(instance, implementation)) {	// @audit-issue
```
[195](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L195-L195), [231](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L231-L231), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

260:        if (mapRemovedNominees[nomineeHash] == 0 && mapNomineeIds[nomineeHash] == 0) {	// @audit-issue
```
[260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L260-L260), 


#### Recommendation


When multiple conditions need to be checked successively, try to combine them in a single `if` statement using `&&` instead of nesting separate `if` statements. This will leverage short-circuit evaluation for potential gas savings.


### Use `!= 0` Instead of `> 0` for Unsigned Integer Comparison
In Solidity, unsigned integers (e.g., `uint256`, `uint8`, etc.) represent non-negative whole numbers, ranging from 0 to a maximum value determined by their bit size. When performing comparisons on these numbers, especially to check if they are non-zero, developers have options. A common practice is to compare against zero using the `>` operator, as in `value > 0`. However, given the nature of unsigned integers, a more straightforward and slightly gas-efficient comparison is to use the `!=` operator, as in `value != 0`.

The primary rationale is that the `!=` comparison directly checks for non-equality, whereas the `>` comparison checks if one value is strictly greater than another. For unsigned integers, where negative values don't exist, the `!= 0` check is both semantically clearer and potentially more efficient at the EVM bytecode level.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

670:        if (_epsilonRate > 0 && _epsilonRate <= 17e18) {	// @audit-issue

853:        if (totalIncentives > 0) {	// @audit-issue

896:        incentiveFlags[0] = (mapEpochTokenomics[curEpoch].unitPoints[0].rewardUnitFraction > 0);	// @audit-issue

1176:            if (nextEpochLen > 0) {	// @audit-issue

1363:            if (lastEpoch > 0 && lastEpoch < curEpoch) {	// @audit-issue

1433:            if (lastEpoch > 0 && lastEpoch < curEpoch) {	// @audit-issue

1437:                if (totalIncentives > 0) {	// @audit-issue
```
[670](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L670-L670), [853](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L853-L853), [896](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L896-L896), [1176](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1176-L1176), [1363](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1363-L1363), [1433](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1433-L1433), [1437](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1437-L1437), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

419:        if (transferAmount > 0) {	// @audit-issue

455:            if (transferAmounts[i] > 0) {	// @audit-issue

463:                if (stakingIncentives[i][j] > 0) {	// @audit-issue

615:            if (transferAmounts[i] > 0) {	// @audit-issue

617:                if (withheldAmount > 0) {	// @audit-issue

811:        if ((reward + topUp) > 0) {	// @audit-issue

814:            if (topUp > 0) {	// @audit-issue

1000:        if (returnAmount > 0) {	// @audit-issue

1013:            if (withheldAmount > 0) {	// @audit-issue

1098:        if (totalAmounts[2] > 0) {	// @audit-issue

1105:            if (totalAmounts[1] > 0) {	// @audit-issue

1161:        if (totalReturnAmount > 0) {	// @audit-issue
```
[419](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L419-L419), [455](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L455-L455), [463](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L463-L463), [615](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L615-L615), [617](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L617-L617), [811](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L811-L811), [814](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L814-L814), [1000](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1000-L1000), [1013](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1013-L1013), [1098](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1098-L1098), [1105](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1105-L1105), [1161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1161-L1161), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

153:        if (transferAmount > 0) {	// @audit-issue
```
[153](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L153-L153), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

107:        if (transferAmount > 0) {	// @audit-issue
```
[107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L107-L107), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

63:        if (transferAmount > 0) {	// @audit-issue
```
[63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L63-L63), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

208:        if (localWithheldAmount > 0) {	// @audit-issue

442:        if (amount > 0) {	// @audit-issue
```
[208](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L208-L208), [442](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L442-L442), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

64:        if (transferAmount > 0) {	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L64-L64), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

57:        if (ts > 0 && curNonces[0] > lastNonces[0]) {	// @audit-issue
```
[57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L57-L57), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

450:            if (evictServiceIds[i] > 0) {	// @audit-issue

536:        if (block.timestamp - tsCheckpointLast >= livenessPeriod && lastAvailableRewards > 0) {	// @audit-issue

612:        if (numServices > 0) {	// @audit-issue

676:                if (serviceInactivity[i] > 0) {	// @audit-issue

728:        if (sInfo.tsStart > 0) {	// @audit-issue

818:        if (ts <= minStakingDuration && availableRewards > 0) {	// @audit-issue

932:        } else if (sInfo.tsStart > 0) {	// @audit-issue
```
[450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L450-L450), [536](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L536-L536), [612](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L612-L612), [676](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L676-L676), [728](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L728-L728), [818](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L818-L818), [932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L932-L932), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

220:            if (returnData.length > 0) {	// @audit-issue
```
[220](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L220-L220), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

295:        if (mapNomineeIds[nomineeHash] > 0) {	// @audit-issue

430:        if (totalSum > 0) {	// @audit-issue

478:        if (mapRemovedNominees[nomineeHash] > 0) {	// @audit-issue
```
[295](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L295-L295), [430](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L430-L430), [478](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L478-L478), 


#### Recommendation

Use '!= 0' instead of '> 0' for comparing unsigned integers in Solidity. This is semantically clearer and slightly more gas-efficient, as it directly checks for non-zero values without considering unnecessary relational comparisons.

### Operator `>=`/`<=` costs less gas than operator `>`/`<`
The compiler uses opcodes `GT` and `ISZERO` for code that uses `>`, but only requires `LT` for `>=`. A similar behaviour applies for `>`, which uses opcodes `LT` and `ISZERO`, but only requires `GT` for `<=`.


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

717:        if (_rewardComponentFraction + _rewardAgentFraction > 100) {	// @audit-issue

724:        if (sumTopUpFractions > 100) {	// @audit-issue
```
[717](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L717-L717), [724](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L724-L724), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

35:        if (numYears < 10) {	// @audit-issue

71:        if (numYears < 10) {	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L35-L35), [71](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L71-L71), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

383:        if (epochAfterRemoved > 1 && firstClaimedEpoch >= epochAfterRemoved) {	// @audit-issue

392:        if (epochAfterRemoved > 1 && lastClaimedEpoch > epochAfterRemoved) {	// @audit-issue

794:        if (_locked > 1) {	// @audit-issue

960:        if (_locked > 1) {	// @audit-issue

1066:        if (_locked > 1) {	// @audit-issue

1131:        if (_locked > 1) {	// @audit-issue

1220:        if (bridgingDecimals < 18) {	// @audit-issue
```
[383](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L383-L383), [392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L392-L392), [794](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L794-L794), [960](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L960-L960), [1066](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1066-L1066), [1131](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1131-L1131), [1220](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1220-L1220), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

141:        if (gasPriceBid < 2 || gasLimitMessage < 2 || maxSubmissionCostMessage == 0) {	// @audit-issue
```
[141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L141-L141), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

88:        if (_locked > 1) {	// @audit-issue
```
[88](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L88-L88), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

141:        if (_locked > 1) {	// @audit-issue

268:        if (_locked > 1) {	// @audit-issue

325:        if (_locked > 1) {	// @audit-issue

379:        if (_locked > 1) {	// @audit-issue

414:        if (_locked > 1) {	// @audit-issue
```
[141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L141-L141), [268](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L268-L268), [325](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L325-L325), [379](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L379-L379), [414](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L414-L414), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

302:        if (_stakingParams.minStakingDeposit < 2) {	// @audit-issue

411:        if (success && returnData.length > 63 && (returnData.length % 32 == 0)) {	// @audit-issue
```
[302](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L302-L302), [411](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L411-L411), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

173:        if (_locked > 1) {	// @audit-issue
```
[173](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L173-L173), 


#### Recommendation

Opt for '>=', '<=' operators over '>' and '<' in Solidity where logically appropriate, as they consume less gas. This is because '>=' and '<=' require only one opcode ('LT' or 'GT'), compared to the two opcodes ('GT'/'LT' and 'ISZERO') needed for '>' and '<'.

### Constructor Can Be Marked As Payable
`payable` functions cost less gas to execute, since the compiler does not have to add extra checks to ensure that a payment wasn't provided.

A `constructor` can safely be marked as `payable`, since only the deployer would be able to pass funds, and the project itself would not pass any funds.



```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

377:    constructor()	// @audit-issue
```
[377](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L377-L377), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

315:    constructor(	// @audit-issue
```
[315](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L315-L315), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

89:    constructor(	// @audit-issue
```
[89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L89-L89), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

47:    constructor(	// @audit-issue
```
[47](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L47-L47), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

59:    constructor(	// @audit-issue
```
[59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

36:    constructor(	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L36-L36), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

76:    constructor(	// @audit-issue
```
[76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L76-L76), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

70:    constructor(address _olas, address _dispenser, address _stakingFactory, address _timelock) {	// @audit-issue
```
[70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L70-L70), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

64:    constructor(	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L64-L64), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

42:    constructor(	// @audit-issue
```
[42](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L42-L42), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

20:    constructor(	// @audit-issue
```
[20](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L20-L20), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

28:    constructor(	// @audit-issue
```
[28](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L28-L28), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

39:    constructor(	// @audit-issue
```
[39](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L39-L39), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

101:    constructor(	// @audit-issue
```
[101](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L101-L101), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

36:    constructor(	// @audit-issue
```
[36](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L36-L36), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

74:    constructor(address _olas, uint256 _rewardsPerSecondLimit, uint256 _timeForEmissionsLimit,	// @audit-issue
```
[74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L74-L74), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

24:    constructor(uint256 _livenessRatio) {	// @audit-issue
```
[24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L24-L24), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

27:    constructor(address implementation) {	// @audit-issue
```
[27](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L27-L27), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

103:    constructor(address _verifier) {	// @audit-issue
```
[103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L103-L103), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

205:    constructor(address _ve) {	// @audit-issue
```
[205](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L205-L205), 


#### Recommendation

Mark constructors as 'payable' in Solidity contracts to reduce gas costs, as this eliminates the need for the compiler to add checks against incoming payments. This is safe because only the deployer can send funds during contract creation, and typically no funds are sent at this stage.

### Initializers Can Be Marked As Payable
`payable` functions cost less gas to execute, since the compiler does not have to add extra checks to ensure that a payment wasn't provided.

A `initializer` can safely be marked as `payable`, since only the deployer would be able to pass funds, and the project itself would not pass any funds.



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

Mark initializers as 'payable' in Solidity contracts to reduce gas costs, as this eliminates the need for the compiler to add checks against incoming payments. This is safe because only the deployer can send funds during contract creation, and typically no funds are sent at this stage.

### Use `selfbalance()` instead of `address(this).balance`
In Solidity, contracts often need to query their own Ether balance. While `address(this).balance` has been a widely used approach to retrieve the current contract's balance, it is not the most gas-efficient method, especially with the introduction of the `SELFBALANCE` opcode in more recent EVM versions.

The `SELFBALANCE` opcode provides a more gas-efficient way to obtain the balance of the current contract. In Solidity, this can be invoked using the `selfbalance()` function. Compared to the `BALANCE` opcode used by `address(this).balance`, `SELFBALANCE` offers significant gas savings:

- `BALANCE`: The static gas cost is 0. If the accessed address is warm, the dynamic gas cost is 100. If the address is cold, the dynamic cost is 2,600.
  
- `SELFBALANCE`: Semantically equivalent to the `BALANCE` opcode when called on the contract's own address, but with a dramatically reduced minimum gas cost of 5.

Switching to `selfbalance()` can lead to significant gas savings, especially in operations or functions that frequently check the contract's balance.


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

390:        amount = address(this).balance;	// @audit-issue
```
[390](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L390-L390), 


#### Recommendation

To optimize gas usage when querying a contract's own Ether balance in Solidity, it's recommended to use the `selfbalance()` function, which utilizes the more gas-efficient `SELFBALANCE` opcode. This can result in significant gas savings compared to `address(this).balance`.

### Optimize names to save gas
`public`/`external` function names and `public` member variable names can be optimized to save gas. Below are the interfaces/abstract contracts that can be optimized so that the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save 22 gas per call, [per sorted position shifted](https://medium.com/joyso/solidity-how-does-function-name-affect-gas-consumption-in-smart-contract-47d270d8ac92).


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

10:interface IOLAS {	// @audit-issue

17:interface IToken {	// @audit-issue

29:interface ITreasury {	// @audit-issue

37:interface IServiceRegistry {	// @audit-issue

58:interface IVotingEscrow {	// @audit-issue

254:contract Tokenomics is TokenomicsConstants {	// @audit-issue
```
[10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L10-L10), [17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L17-L17), [29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L29-L29), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L37-L37), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L58-L58), [254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L254-L254), 


```solidity
Path: ./tokenomics/contracts/TokenomicsConstants.sol

8:abstract contract TokenomicsConstants {	// @audit-issue
```
[8](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/TokenomicsConstants.sol#L8-L8), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

5:interface IDepositProcessor {	// @audit-issue

44:interface IToken {	// @audit-issue

58:interface ITokenomics {	// @audit-issue

142:interface ITreasury {	// @audit-issue

160:interface IVoteWeighting {	// @audit-issue

253:contract Dispenser {	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L5-L5), [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L44-L44), [58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L58-L58), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L142-L142), [160](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L160-L160), [253](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L253-L253), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue

70:contract ArbitrumDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L6-L6), [70](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L70-L70), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue

37:contract OptimismTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L6-L6), [37](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L37-L37), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

6:interface IDispenser {	// @audit-issue

10:interface IToken {	// @audit-issue

22:abstract contract DefaultDepositProcessorL1 is IBridgeErrors {	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L6-L6), [10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L10-L10), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

6:interface IBridge {	// @audit-issue

23:contract ArbitrumTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L6-L6), [23](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L23-L23), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue

59:contract OptimismDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L6-L6), [59](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L59-L59), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

4:interface IToken {	// @audit-issue

18:interface IStaking {	// @audit-issue

24:interface IStakingFactory {	// @audit-issue

50:contract EthereumDepositProcessor {	// @audit-issue
```
[4](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L4-L4), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L18-L18), [24](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L24-L24), [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

7:interface IBridge {	// @audit-issue

50:contract WormholeTargetDispenserL2 is DefaultTargetDispenserL2, TokenReceiver {	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L7-L7), [50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

6:interface IBridge {	// @audit-issue

32:contract GnosisDepositProcessorL1 is DefaultDepositProcessorL1 {	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L6-L6), [32](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L32-L32), 


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

6:interface IBridge {	// @audit-issue

26:contract GnosisTargetDispenserL2 is DefaultTargetDispenserL2 {	// @audit-issue
```
[6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L6-L6), [26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L26-L26), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

7:interface IStaking {	// @audit-issue

14:interface IStakingFactory {	// @audit-issue

22:interface IToken {	// @audit-issue

45:abstract contract DefaultTargetDispenserL2 is IBridgeErrors {	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L14-L14), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L22-L22), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L45-L45), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

7:interface IBridge {	// @audit-issue

22:contract PolygonDepositProcessorL1 is DefaultDepositProcessorL1, FxBaseRootTunnel {	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L7-L7), [22](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L22-L22), 


```solidity
Path: ./tokenomics/contracts/interfaces/IDonatorBlacklist.sol

5:interface IDonatorBlacklist {	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/interfaces/IDonatorBlacklist.sol#L5-L5), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

5:interface IStaking {	// @audit-issue

43:contract StakingVerifier {	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L5-L5), [43](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L43-L43), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

17:contract StakingNativeToken is StakingBase {	// @audit-issue
```
[17](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L17-L17), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

5:interface IMultisig {	// @audit-issue

18:contract StakingActivityChecker {	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L5-L5), [18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L18-L18), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

7:interface IActivityChecker {	// @audit-issue

30:interface IService {	// @audit-issue

168:abstract contract StakingBase is ERC721TokenReceiver {	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L7-L7), [30](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L30-L30), [168](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L168-L168), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

11:interface IServiceTokenUtility {	// @audit-issue

44:contract StakingToken is StakingBase {	// @audit-issue
```
[11](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L11-L11), [44](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L44-L44), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

21:contract StakingProxy {	// @audit-issue
```
[21](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L21-L21), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

7:interface IStaking {	// @audit-issue

14:interface IStakingVerifier {	// @audit-issue

81:contract StakingFactory {	// @audit-issue
```
[7](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L7-L7), [14](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L14-L14), [81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L81-L81), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

5:interface IDispenser {	// @audit-issue

16:interface IVEOLAS {	// @audit-issue

132:contract VoteWeighting {	// @audit-issue
```
[5](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L5-L5), [16](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L16-L16), [132](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L132-L132), 


#### Recommendation

Optimize gas usage by renaming 'public'/'external' functions and 'public' member variables in Solidity. Aim for shorter and more efficient names, especially for frequently called functions. This can save gas during deployment and reduce gas costs per call due to lower method ID sorting positions.

### Use `uint256(1)`/`uint256(2)` instead of `true`/`false` to save gas for changes
Avoids a Gsset (**20000 gas**) when changing from `false` to `true`, after having been `true` in the past. Since most of the bools aren't changed twice in one transaction, I've counted the amount of gas as half of the full amount, for each variable. Note that public state variables can be re-written to be `private` and use `uint256`, but have public getters returning `bool`s.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

367:    mapping(uint256 => mapping(uint256 => bool)) public mapNewUnits;	// @audit-issue

369:    mapping(address => bool) public mapNewOwners;	// @audit-issue
```
[367](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L367-L367), [369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L369-L369), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

54:    mapping(bytes32 => bool) public mapDeliveryHashes;	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L54-L54), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

18:    mapping(bytes32 => bool) public mapDeliveryHashes;	// @audit-issue
```
[18](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L18-L18), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

93:    mapping(bytes32 => bool) public stakingQueueingNonces;	// @audit-issue
```
[93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L93-L93), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

64:    bool public implementationsCheck;	// @audit-issue

67:    mapping(address => bool) public mapImplementations;	// @audit-issue

45:    event SetImplementationsCheck(bool setCheck);	// @audit-issue

46:    event ImplementationsWhitelistUpdated(address[] implementations, bool[] statuses, bool setCheck);	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L64-L64), [67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L67-L67), [45](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L45-L45), [46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L46-L46), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

74:    bool isEnabled;	// @audit-issue

85:    event InstanceStatusChanged(address indexed instance, bool isEnabled);	// @audit-issue
```
[74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L74-L74), [85](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L85-L85), 


#### Recommendation

To minimize gas overhead in your Solidity contracts, consider using `uint256(1)` and `uint256(2)` to represent `true` and `false`, respectively, instead of `bool` types for storage. This approach avoids additional `SLOAD` and `SSTORE` operations, resulting in more gas-efficient code.

### Consider activating `via-ir` for deploying
The IR-based code generator was developed to make code generation more performant by enabling optimization passes that can be applied across functions.

It is possible to activate the IR-based code generator through the command line by using the flag `--via-ir`or by including the option `{"viaIR": true}`.

Keep in mind that compiling with this option may take longer. However, you can simply test it before deploying your code. If you find that it provides better performance, you can add the `--via-ir` flag to your deploy command.
```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1), 


#### Recommendation

Consider activating `via-ir`.

### Optimize Deployment Size by Fine-tuning IPFS Hash
The Solidity compiler appends 53 bytes of metadata to the smart contract code, incurring an extra cost of 10,600 gas. This additional expense arises from 200 gas per bytecode, plus calldata cost, which amounts to 16 gas for non-zero bytes and 4 gas for zero bytes. This results in a maximum of 848 extra gas in calldata cost.

Reducing this cost is crucial for the following reasons:

The metadata's 53-byte addition leads to a deployment cost increase of 10,600 gas. It can also result in an additional calldata cost of up to 848 gas. Ways to Minimize Gas Consumption:

Employ the `--no-cbor-metadata` compiler option to exclude metadata. Be cautious as this might impact contract verification. Search for code comments that yield an IPFS hash with more zeros, thereby reducing calldata costs.

```solidity
/// @audit Global finding.
```
[1](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1), 


#### Recommendation

To optimize deployment size and reduce associated costs, consider the following strategies:
1. **Exclude Metadata with Compiler Option**: Use the `solc` compiler’s `--metadata-hash none` or `--no-cbor-metadata` option to prevent the inclusion of metadata in the compiled bytecode. This action reduces the bytecode size, thus lowering deployment gas costs. However, exercise caution with this approach, as it might affect the ability to verify the contract on platforms like Etherscan.

2. **Optimize IPFS Hash for More Zeros**: If excluding metadata is not desirable, another approach involves optimizing code comments or elements that influence the metadata hash generation to achieve an IPFS hash with a higher proportion of zeros. Since calldata costs are lower for zero bytes, a metadata hash with more zeros can reduce the calldata costs associated with contract interactions.

Example for excluding metadata:
```bash
solc --metadata-hash none YourContract.sol
```


### Low level `call` can be optimized with assembly
`returnData` is copied to memory even if the variable is not utilized: the proper way to handle this is through a low level assembly call.
```solidity
// before
(bool success,) = payable(receiver).call{gas: gas, value: value}("");

//after
bool success;
assembly {
    success := call(gas, receiver, value, 0, 0, 0, 0)
}
```


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

Optimize low-level 'call' operations in Solidity using assembly, especially when 'returnData' is not needed. This avoids unnecessary copying to memory and can lead to gas savings. For example, replace '(bool success,) = payable(receiver).call{gas: gas, value: value}("");' with an assembly block: 'assembly { success := call(gas, receiver, value, 0, 0, 0, 0) }'. This ensures a more efficient execution.

### Assembly: Use scratch space for building calldata
If an external call's calldata can fit into two or fewer words, use the scratch space to build the calldata, rather than allowing Solidity to do a memory expansion.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

462:        uint256 _timeLaunch = IOLAS(_olas).timeLaunch();	// @audit-issue

910:                address serviceOwner = IToken(serviceRegistry).ownerOf(serviceIds[i]);	// @audit-issue

911:                topUpEligible = (IVotingEscrow(ve).getVotes(serviceOwner) >= veOLASThreshold  ||	// @audit-issue

912:                    IVotingEscrow(ve).getVotes(donator) >= veOLASThreshold) ? true : false;	// @audit-issue

918:                (uint256 numServiceUnits, uint32[] memory serviceUnitIds) = IServiceRegistry(serviceRegistry).	// @audit-issue

919:                    getUnitIdsOfService(IServiceRegistry.UnitType(unitType), serviceIds[i]);	// @audit-issue

967:                        address unitOwner = IToken(registries[unitType]).ownerOf(serviceUnitIds[j]);	// @audit-issue

1003:        if (bList != address(0) && IDonatorBlacklist(bList).isDonatorBlacklisted(donator)) {	// @audit-issue

1012:            if (!IServiceRegistry(serviceRegistry).exists(serviceIds[i])) {	// @audit-issue

1041:        UD60x18 codeUnits = UD60x18.wrap(codePerDev);	// @audit-issue

1044:        UD60x18 fp = UD60x18.wrap(treasuryRewards);	// @audit-issue

1046:        UD60x18 fpDevsPerCapital = UD60x18.wrap(devsPerCapital);	// @audit-issue

1047:        fp = fp.mul(fpDevsPerCapital);	// @audit-issue

1049:        fp = fp.add(fpNumNewOwners);	// @audit-issue

1050:        fp = fp.mul(codeUnits);	// @audit-issue

1052:        fp = fp.div(UD60x18.wrap(100e18));	// @audit-issue

1054:        uint256 fKD = UD60x18.unwrap(fp);	// @audit-issue

1285:        if (incentives[1] == 0 || ITreasury(treasury).rebalanceTreasury(incentives[1])) {	// @audit-issue

1330:            registriesSupply[i] = IToken(registries[i]).totalSupply();	// @audit-issue

1348:            address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);	// @audit-issue

1402:            registriesSupply[i] = IToken(registries[i]).totalSupply();	// @audit-issue

1420:            address unitOwner = IToken(registries[unitTypes[i]]).ownerOf(unitIds[i]);	// @audit-issue
```
[462](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L462-L462), [910](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L910-L910), [911](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L911-L911), [912](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L912-L912), [918](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L918-L918), [919](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L919-L919), [967](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L967-L967), [1003](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1003-L1003), [1012](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1012-L1012), [1041](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1041-L1041), [1044](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1044-L1044), [1046](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1046-L1046), [1047](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1047-L1047), [1049](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1049-L1049), [1050](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1050-L1050), [1052](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1052-L1052), [1054](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1054-L1054), [1285](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1285-L1285), [1330](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1330-L1330), [1348](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1348-L1348), [1402](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1402-L1402), [1420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1420-L1420), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

346:        retainerHash = keccak256(abi.encode(IVoteWeighting.Nominee(retainer, block.chainid)));	// @audit-issue

361:        uint256 eCounter = ITokenomics(tokenomics).epochCounter();	// @audit-issue

420:            IToken(olas).transfer(depositProcessor, transferAmount);	// @audit-issue

456:                IToken(olas).transfer(depositProcessor, transferAmounts[i]);	// @audit-issue

596:            uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();	// @audit-issue

741:            ITreasury(treasury).paused() == 2) {	// @audit-issue

745:        mapLastClaimedStakingEpochs[nomineeHash] = ITokenomics(tokenomics).epochCounter();	// @audit-issue

762:        uint256 eCounter = ITokenomics(tokenomics).epochCounter();	// @audit-issue

766:        uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(eCounter - 1);	// @audit-issue

769:        uint256 epochLen = ITokenomics(tokenomics).epochLen();	// @audit-issue

802:            ITreasury(treasury).paused() == 2) {	// @audit-issue

815:                olasBalance = IToken(olas).balanceOf(msg.sender);	// @audit-issue

822:                olasBalance = IToken(olas).balanceOf(msg.sender) - olasBalance;	// @audit-issue

871:        nomineeHash = keccak256(abi.encode(IVoteWeighting.Nominee(stakingTarget, chainId)));	// @audit-issue

878:        IVoteWeighting(voteWeighting).checkpointNominee(stakingTarget, chainId);	// @audit-issue

884:                ITokenomics(tokenomics).mapEpochStakingPoints(j);	// @audit-issue

886:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue

984:            ITreasury(treasury).paused() == 2) {	// @audit-issue

990:        uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();	// @audit-issue

1001:            ITokenomics(tokenomics).refundFromStaking(returnAmount);	// @audit-issue

1028:                uint256 olasBalance = IToken(olas).balanceOf(address(this));	// @audit-issue

1034:                olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;	// @audit-issue

1081:            ITreasury(treasury).paused() == 2) {	// @audit-issue

1099:            ITokenomics(tokenomics).refundFromStaking(totalAmounts[2]);	// @audit-issue

1106:                uint256 olasBalance = IToken(olas).balanceOf(address(this));	// @audit-issue

1112:                olasBalance = IToken(olas).balanceOf(address(this)) - olasBalance;	// @audit-issue

1148:            ITokenomics.StakingPoint memory stakingPoint = ITokenomics(tokenomics).mapEpochStakingPoints(j);	// @audit-issue

1151:            uint256 endTime = ITokenomics(tokenomics).getEpochEndTime(j);	// @audit-issue

1162:            ITokenomics(tokenomics).refundFromStaking(totalReturnAmount);	// @audit-issue

1217:        uint256 bridgingDecimals = IDepositProcessor(depositProcessor).getBridgingDecimals();	// @audit-issue
```
[346](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L346-L346), [361](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L361-L361), [420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L420-L420), [456](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L456-L456), [596](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L596-L596), [741](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L741-L741), [745](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L745-L745), [762](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L762-L762), [766](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L766-L766), [769](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L769-L769), [802](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L802-L802), [815](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L815-L815), [822](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L822-L822), [871](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L871-L871), [878](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L878-L878), [884](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L884-L884), [886](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L886-L886), [984](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L984-L984), [990](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L990-L990), [1001](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1001-L1001), [1028](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1028-L1028), [1034](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1034-L1034), [1081](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1081-L1081), [1099](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1099-L1099), [1106](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1106-L1106), [1112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1112-L1112), [1148](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1148-L1148), [1151](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1151-L1151), [1162](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1162-L1162), [1217](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1217-L1217), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

174:            IToken(olas).approve(l1ERC20Gateway, transferAmount);	// @audit-issue

203:        address l2Dispenser = IBridge(outbox).l2ToL1Sender();	// @audit-issue
```
[174](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L174-L174), [203](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L203-L203), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

98:        address l1Processor = IBridge(l2MessageRelayer).xDomainMessageSender();	// @audit-issue
```
[98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L98-L98), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

124:        IDispenser(l1Dispenser).syncWithheldAmount(l2TargetChainId, amount);	// @audit-issue
```
[124](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L124-L124), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

58:        uint256 sequence = IBridge(l2MessageRelayer).sendTxToL1(l1DepositProcessor, data);	// @audit-issue
```
[58](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L58-L58), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

111:            IToken(olas).approve(l1TokenRelayer, transferAmount);	// @audit-issue

150:        address l2Dispenser = IBridge(l1MessageRelayer).xDomainMessageSender();	// @audit-issue
```
[111](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L111-L111), [150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L150-L150), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

99:            uint256 limitAmount = IStakingFactory(stakingFactory).verifyInstanceAndGetEmissionsAmount(target);	// @audit-issue

112:                IToken(olas).transfer(timelock, refundAmount);	// @audit-issue

118:            IToken(olas).approve(target, amount);	// @audit-issue

119:            IStaking(target).deposit(amount);	// @audit-issue
```
[99](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L99-L99), [112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L112-L112), [118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L118-L118), [119](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L119-L119), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

65:            IToken(olas).approve(l1TokenRelayer, transferAmount);	// @audit-issue

100:        address l2Dispenser = IBridge(l1MessageRelayer).messageSender();	// @audit-issue
```
[65](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L65-L65), [100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L100-L100), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

89:        address processor = IBridge(l2MessageRelayer).messageSender();	// @audit-issue
```
[89](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L89-L89), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

161:            (bool success, bytes memory returnData) = stakingFactory.call(verifyData);	// @audit-issue

189:            if (IToken(olas).balanceOf(address(this)) >= amount && localPaused == 1) {	// @audit-issue

191:                IToken(olas).approve(target, amount);	// @audit-issue

192:                IStaking(target).deposit(amount);	// @audit-issue

287:        uint256 olasBalance = IToken(olas).balanceOf(address(this));	// @audit-issue

290:            IToken(olas).approve(target, amount);	// @audit-issue

291:            IStaking(target).deposit(amount);	// @audit-issue

440:        uint256 amount = IToken(olas).balanceOf(address(this));	// @audit-issue

443:            bool success = IToken(olas).transfer(newL2TargetDispenser, amount);	// @audit-issue
```
[161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L161-L161), [189](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L189-L189), [191](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L191-L191), [192](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L192-L192), [287](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L287-L287), [290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L290-L290), [291](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L291-L291), [440](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L440-L440), [443](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L443-L443), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

68:            IToken(olas).approve(predicate, transferAmount);	// @audit-issue
```
[68](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L68-L68), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

192:        uint256 rewardsPerSecond = IStaking(instance).rewardsPerSecond();	// @audit-issue

199:        uint256 numServices = IStaking(instance).maxNumServices();	// @audit-issue

207:        (bool success, bytes memory returnData) = instance.staticcall(tokenData);	// @audit-issue
```
[192](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L192-L192), [199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L199-L199), [207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L207-L207), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

38:        nonces[0] = IMultisig(multisig).nonce();	// @audit-issue
```
[38](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L38-L38), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

338:            agentIds.push(agentId);	// @audit-issue

379:        (uint256 numAgentIds, IService.AgentParams[] memory agentParams) = IService(serviceRegistry).getAgentParams(serviceId);	// @audit-issue

407:        (bool success, bytes memory returnData) = activityChecker.staticcall(activityData);	// @audit-issue

417:            (success, returnData) = activityChecker.staticcall(activityData);	// @audit-issue

472:            setServiceIds.pop();	// @audit-issue

739:        IService.Service memory service = IService(serviceRegistry).getService(serviceId);	// @audit-issue

789:        uint256[] memory nonces = IActivityChecker(activityChecker).getMultisigNonces(service.multisig);	// @audit-issue

794:        setServiceIds.push(serviceId);	// @audit-issue

859:            setServiceIds.pop();	// @audit-issue
```
[338](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L338-L338), [379](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L379-L379), [407](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L407-L407), [417](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L417-L417), [472](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L472-L472), [739](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L739-L739), [789](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L789-L789), [794](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L794-L794), [859](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L859-L859), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

77:            IServiceTokenUtility(serviceRegistryTokenUtility).mapServiceIdTokenDeposit(serviceId);	// @audit-issue

93:            uint256 bond = IServiceTokenUtility(serviceRegistryTokenUtility).getAgentBond(serviceId, serviceAgentIds[i]);	// @audit-issue
```
[77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L77-L77), [93](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L93-L93), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

195:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyImplementation(implementation)) {	// @audit-issue

216:        (bool success, bytes memory returnData) = instance.call(initPayload);	// @audit-issue

231:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyInstance(instance, implementation)) {	// @audit-issue

285:            return IStakingVerifier(localVerifier).verifyInstance(instance, implementation);	// @audit-issue

300:            amount = IStaking(instance).emissionsAmount();	// @audit-issue

306:                uint256 maxEmissions = IStakingVerifier(localVerifier).getEmissionsAmountLimit(instance);	// @audit-issue
```
[195](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L195-L195), [216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L216-L216), [231](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L231-L231), [285](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L285-L285), [300](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L300-L300), [306](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L306-L306), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

216:        setNominees.push(Nominee(0, 0));	// @audit-issue

218:        setRemovedNominees.push(Nominee(0, 0));	// @audit-issue

307:        setNominees.push(nominee);	// @audit-issue

315:            IDispenser(localDispenser).addNominee(nomineeHash);	// @audit-issue

483:        int128 userSlope = IVEOLAS(ve).getLastUserPoint(msg.sender).slope;	// @audit-issue

488:        uint256 lockEnd = IVEOLAS(ve).lockedEnd(msg.sender);	// @audit-issue

616:        setRemovedNominees.push(nominee);	// @audit-issue

627:        setNominees.pop();	// @audit-issue

632:            IDispenser(localDispenser).removeNominee(nomineeHash);	// @audit-issue
```
[216](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L216-L216), [218](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L218-L218), [307](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L307-L307), [315](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L315-L315), [483](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L483-L483), [488](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L488-L488), [616](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L616-L616), [627](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L627-L627), [632](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L632-L632), 


#### Recommendation

Review your smart contracts to identify and remove `block.number` and `block.timestamp` from the parameters of emitted events. Instead of manually adding these fields, rely on the Ethereum blockchain's inherent inclusion of this information within the block context. Simplify your event definitions to include only the essential data specific to the event's purpose, excluding universally available block metadata.

### Use assembly to check for `address(0)`
In Solidity, it's a common practice to check whether an Ethereum address variable is set to the zero address (`address(0)`) to handle various scenarios, such as token transfers or contract interactions. Typically, this check is performed using a conditional statement like `if (addressVariable == address(0))`.

However, using this approach in high-frequency or gas-sensitive operations can lead to unnecessary gas costs. A more gas-efficient alternative is to use inline assembly to perform the zero address check, which can significantly reduce gas consumption, especially in loops or complex contract logic.

By utilizing inline assembly for this specific check, you can optimize gas usage and make your Solidity code more efficient.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

422:        if (owner != address(0)) {	// @audit-issue

427:        if (_olas == address(0) || _treasury == address(0) || _depository == address(0) || _dispenser == address(0) ||	// @audit-issue

428:            _ve == address(0) || _componentRegistry == address(0) || _agentRegistry == address(0) ||	// @audit-issue

429:            _serviceRegistry == address(0)) {	// @audit-issue

533:        if (implementation == address(0)) {	// @audit-issue

553:        if (newOwner == address(0)) {	// @audit-issue

572:        if (_treasury != address(0)) {	// @audit-issue

577:        if (_depository != address(0)) {	// @audit-issue

582:        if (_dispenser != address(0)) {	// @audit-issue

599:        if (_componentRegistry != address(0)) {	// @audit-issue

603:        if (_agentRegistry != address(0)) {	// @audit-issue

607:        if (_serviceRegistry != address(0)) {	// @audit-issue

1003:        if (bList != address(0) && IDonatorBlacklist(bList).isDonatorBlacklisted(donator)) {	// @audit-issue

1087:        if (implementation == address(0)) {	// @audit-issue
```
[422](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L422-L422), [427](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L427-L427), [428](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L428-L428), [429](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L429-L429), [533](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L533-L533), [553](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L553-L553), [572](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L572-L572), [577](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L577-L577), [582](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L582-L582), [599](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L599-L599), [603](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L603-L603), [607](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L607-L607), [1003](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1003-L1003), [1087](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1087-L1087), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

330:        if (_olas == address(0) || _tokenomics == address(0) || _treasury == address(0) ||	// @audit-issue

331:            _voteWeighting == address(0) || _retainer == 0) {	// @audit-issue

643:        if (newOwner == address(0)) {	// @audit-issue

662:        if (_tokenomics != address(0)) {	// @audit-issue

668:        if (_treasury != address(0)) {	// @audit-issue

674:        if (_voteWeighting != address(0)) {	// @audit-issue
```
[330](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L330-L330), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L331-L331), [643](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L643-L643), [662](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L662-L662), [668](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L668-L668), [674](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L674-L674), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

102:        if (_l1ERC20Gateway == address(0) || _outbox == address(0) || _bridge == address(0)) {	// @audit-issue

135:        if (refundAccount == address(0)) {	// @audit-issue
```
[102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L102-L102), [135](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L135-L135), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

67:        if (_l1Dispenser == address(0) || _l1TokenRelayer == address(0) || _l1MessageRelayer == address(0)) {	// @audit-issue

193:        if (l2Dispenser == address(0)) {	// @audit-issue

199:        owner = address(0);	// @audit-issue
```
[67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L67-L67), [193](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L193-L193), [199](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L199-L199), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

87:        if (_olasL2 == address(0)) {	// @audit-issue
```
[87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L87-L87), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

72:        if (_olas == address(0) || _dispenser == address(0) || _stakingFactory == address(0) || _timelock == address(0)) {	// @audit-issue
```
[72](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L72-L72), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

77:        if (_wormholeCore == address(0) || _l2TokenRelayer == address(0)) {	// @audit-issue

98:        if (refundAccount == address(0)) {	// @audit-issue
```
[77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L77-L77), [98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L98-L98), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

66:        if (l1Processor == address(0)) {	// @audit-issue
```
[66](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L66-L66), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

41:        if (_wormholeCore == address(0)) {	// @audit-issue

84:        if (refundAccount == address(0)) {	// @audit-issue
```
[41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L41-L41), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L84-L84), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

50:        if (_l2TokenRelayer == address(0)) {	// @audit-issue
```
[50](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L50-L50), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

109:        if (_olas == address(0) || _stakingFactory == address(0) || _l2MessageRelayer == address(0)	// @audit-issue

110:            || _l1DepositProcessor == address(0)) {	// @audit-issue

254:        if (newOwner == address(0)) {	// @audit-issue

398:            revert TransferFailed(address(0), address(this), msg.sender, amount);	// @audit-issue

450:        owner = address(0);	// @audit-issue

460:        if (owner == address(0)) {	// @audit-issue

461:            revert TransferFailed(address(0), msg.sender, address(this), msg.value);	// @audit-issue
```
[109](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L109-L109), [110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L110-L110), [254](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L254-L254), [398](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L398-L398), [450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L450-L450), [460](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L460-L460), [461](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L461-L461), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

49:        if (_checkpointManager == address(0) || _predicate == address(0)) {	// @audit-issue

105:        if (l2Dispenser == address(0)) {	// @audit-issue
```
[49](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L49-L49), [105](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L105-L105), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

77:        if (_olas == address(0)) {	// @audit-issue

103:        if (newOwner == address(0)) {	// @audit-issue

152:            if (implementations[i] == address(0)) {	// @audit-issue
```
[77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L77-L77), [103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L103-L103), [152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L152-L152), 


```solidity
Path: ./registries/contracts/staking/StakingNativeToken.sol

35:            revert TransferFailed(address(0), address(this), to, amount);	// @audit-issue
```
[35](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L35-L35), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

282:        if (serviceRegistry != address(0)) {	// @audit-issue

305:        if (_stakingParams.serviceRegistry == address(0) || _stakingParams.activityChecker == address(0)) {	// @audit-issue
```
[282](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L282-L282), [305](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L305-L305), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

63:        if (_stakingToken == address(0) || _serviceRegistryTokenUtility == address(0)) {	// @audit-issue
```
[63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L63-L63), 


```solidity
Path: ./registries/contracts/staking/StakingProxy.sol

29:        if (implementation == address(0)) {	// @audit-issue
```
[29](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingProxy.sol#L29-L29), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

117:        if (newOwner == address(0)) {	// @audit-issue

179:        if (implementation == address(0)) {	// @audit-issue

195:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyImplementation(implementation)) {	// @audit-issue

211:        if (instance == address(0)) {	// @audit-issue

231:        if (localVerifier != address(0) && !IStakingVerifier(localVerifier).verifyInstance(instance, implementation)) {	// @audit-issue

273:        if (implementation == address(0)) {	// @audit-issue

284:        if (localVerifier != address(0)) {	// @audit-issue

304:            if (localVerifier != address(0)) {	// @audit-issue
```
[117](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L117-L117), [179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L179-L179), [195](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L195-L195), [211](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L211-L211), [231](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L231-L231), [273](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L273-L273), [284](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L284-L284), [304](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L304-L304), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

207:        if (_ve == address(0)) {	// @audit-issue

314:        if (localDispenser != address(0)) {	// @audit-issue

326:        if (account == address(0)) {	// @audit-issue

375:        if (newOwner == address(0)) {	// @audit-issue

631:        if (localDispenser != address(0)) {	// @audit-issue
```
[207](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L207-L207), [314](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L314-L314), [326](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L326-L326), [375](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L375-L375), [631](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L631-L631), 


#### Recommendation

To optimize gas usage in your Solidity code, consider using inline assembly for checking `address(0)`. This approach can significantly reduce gas costs, especially in high-frequency or gas-sensitive operations, leading to more efficient contract execution.

### Use assembly to check for `0`
Using assembly to check for zero can save gas by allowing more direct access to the evm and reducing some of the overhead associated with high-level operations in solidity.

```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

670:        if (_epsilonRate > 0 && _epsilonRate <= 17e18) {	// @audit-issue

684:        if (uint96(_veOLASThreshold) > 0) {	// @audit-issue

758:        if (_maxStakingIncentive == 0 || _minStakingWeight == 0) {	// @audit-issue

853:        if (totalIncentives > 0) {	// @audit-issue

866:        if (totalIncentives > 0) {	// @audit-issue

896:        incentiveFlags[0] = (mapEpochTokenomics[curEpoch].unitPoints[0].rewardUnitFraction > 0);	// @audit-issue

897:        incentiveFlags[1] = (mapEpochTokenomics[curEpoch].unitPoints[1].rewardUnitFraction > 0);	// @audit-issue

898:        incentiveFlags[2] = (mapEpochTokenomics[curEpoch].unitPoints[0].topUpUnitFraction > 0);	// @audit-issue

899:        incentiveFlags[3] = (mapEpochTokenomics[curEpoch].unitPoints[1].topUpUnitFraction > 0);	// @audit-issue

922:                if (numServiceUnits == 0) {	// @audit-issue

934:                        if (lastEpoch == 0) {	// @audit-issue

1176:            if (nextEpochLen > 0) {	// @audit-issue

1183:            if (nextVeOLASThreshold > 0) {	// @audit-issue

1261:        } else if (tokenomicsParametersUpdated > 0) {	// @audit-issue

1274:        if (incentives[0] > 0) {	// @audit-issue

1285:        if (incentives[1] == 0 || ITreasury(treasury).rebalanceTreasury(incentives[1])) {	// @audit-issue

1363:            if (lastEpoch > 0 && lastEpoch < curEpoch) {	// @audit-issue

1433:            if (lastEpoch > 0 && lastEpoch < curEpoch) {	// @audit-issue

1437:                if (totalIncentives > 0) {	// @audit-issue

1444:                if (totalIncentives > 0) {	// @audit-issue
```
[670](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L670-L670), [684](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L684-L684), [758](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L758-L758), [853](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L853-L853), [866](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L866-L866), [896](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L896-L896), [897](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L897-L897), [898](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L898-L898), [899](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L899-L899), [922](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L922-L922), [934](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L934-L934), [1176](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1176-L1176), [1183](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1183-L1183), [1261](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1261-L1261), [1274](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1274-L1274), [1285](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1285-L1285), [1363](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1363-L1363), [1433](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1433-L1433), [1437](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1437-L1437), [1444](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1444-L1444), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

331:            _voteWeighting == address(0) || _retainer == 0) {	// @audit-issue

336:        if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {	// @audit-issue

369:        if (firstClaimedEpoch == 0) {	// @audit-issue

419:        if (transferAmount > 0) {	// @audit-issue

455:            if (transferAmounts[i] > 0) {	// @audit-issue

463:                if (stakingIncentives[i][j] > 0) {	// @audit-issue

535:            if (stakingTargets[i].length == 0) {	// @audit-issue

615:            if (transferAmounts[i] > 0) {	// @audit-issue

617:                if (withheldAmount > 0) {	// @audit-issue

690:        if (_maxNumClaimingEpochs == 0 || _maxNumStakingTargets == 0) {	// @audit-issue

712:        if (depositProcessors.length == 0 || depositProcessors.length != chainIds.length) {	// @audit-issue

719:            if (chainIds[i] == 0) {	// @audit-issue

811:        if ((reward + topUp) > 0) {	// @audit-issue

814:            if (topUp > 0) {	// @audit-issue

821:            if (topUp > 0){	// @audit-issue

861:        if (chainId == 0) {	// @audit-issue

866:        if (stakingTarget == 0) {	// @audit-issue

966:        if (chainId == 0) {	// @audit-issue

971:        if (stakingTarget == 0) {	// @audit-issue

1000:        if (returnAmount > 0) {	// @audit-issue

1007:        if (stakingIncentive > 0) {	// @audit-issue

1013:            if (withheldAmount > 0) {	// @audit-issue

1027:            if (transferAmount > 0) {	// @audit-issue

1098:        if (totalAmounts[2] > 0) {	// @audit-issue

1103:        if (totalAmounts[0] > 0) {	// @audit-issue

1105:            if (totalAmounts[1] > 0) {	// @audit-issue

1161:        if (totalReturnAmount > 0) {	// @audit-issue

1206:        if (chainId == 0 || amount == 0) {	// @audit-issue
```
[331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L331-L331), [336](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L336-L336), [369](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L369-L369), [419](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L419-L419), [455](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L455-L455), [463](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L463-L463), [535](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L535-L535), [615](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L615-L615), [617](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L617-L617), [690](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L690-L690), [712](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L712-L712), [719](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L719-L719), [811](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L811-L811), [814](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L814-L814), [821](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L821-L821), [861](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L861-L861), [866](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L866-L866), [966](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L966-L966), [971](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L971-L971), [1000](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1000-L1000), [1007](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1007-L1007), [1013](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1013-L1013), [1027](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1027-L1027), [1098](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1098-L1098), [1103](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1103-L1103), [1105](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1105-L1105), [1161](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1161-L1161), [1206](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1206-L1206), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

141:        if (gasPriceBid < 2 || gasLimitMessage < 2 || maxSubmissionCostMessage == 0) {	// @audit-issue

153:        if (transferAmount > 0) {	// @audit-issue

154:            if (maxSubmissionCostToken == 0) {	// @audit-issue

172:        if (transferAmount > 0) {	// @audit-issue
```
[141](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L141-L141), [153](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L153-L153), [154](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L154-L154), [172](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L172-L172), 


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

107:        if (transferAmount > 0) {	// @audit-issue

121:        if (cost == 0 || gasLimitMessage == 0) {	// @audit-issue
```
[107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L107-L107), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L121-L121), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

102:            if (limitAmount == 0) {	// @audit-issue
```
[102](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L102-L102), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol

63:        if (transferAmount > 0) {	// @audit-issue

80:            if (gasLimitMessage == 0) {	// @audit-issue
```
[63](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L63-L63), [80](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L80-L80), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol

46:        if (_wormholeTargetChainId == 0) {	// @audit-issue

74:        if (gasLimitMessage == 0) {	// @audit-issue
```
[46](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L46-L46), [74](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeDepositProcessorL1.sol#L74-L74), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

115:        if (_l1SourceChainId == 0) {	// @audit-issue

170:            if (limitAmount == 0) {	// @audit-issue

208:        if (localWithheldAmount > 0) {	// @audit-issue

337:        if (amount == 0) {	// @audit-issue

391:        if (amount == 0) {	// @audit-issue

430:        if (newL2TargetDispenser.code.length == 0) {	// @audit-issue

442:        if (amount > 0) {	// @audit-issue
```
[115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L115-L115), [170](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L170-L170), [208](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L208-L208), [337](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L337-L337), [391](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L391-L391), [430](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L430-L430), [442](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L442-L442), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

64:        if (transferAmount > 0) {	// @audit-issue
```
[64](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L64-L64), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

82:        if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {	// @audit-issue

142:        if (implementations.length == 0 || implementations.length != statuses.length) {	// @audit-issue

186:        if (instance.code.length == 0) {	// @audit-issue

240:        if (_rewardsPerSecondLimit == 0 || _timeForEmissionsLimit == 0 || _numServicesLimit == 0) {	// @audit-issue
```
[82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L82-L82), [142](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L142-L142), [186](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L186-L186), [240](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L240-L240), 


```solidity
Path: ./registries/contracts/staking/StakingActivityChecker.sol

26:        if (_livenessRatio == 0) {	// @audit-issue

57:        if (ts > 0 && curNonces[0] > lastNonces[0]) {	// @audit-issue
```
[26](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L26-L26), [57](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingActivityChecker.sol#L57-L57), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

287:        if (_stakingParams.metadataHash == 0 || _stakingParams.maxNumServices == 0 ||	// @audit-issue

288:            _stakingParams.rewardsPerSecond == 0 || _stakingParams.livenessPeriod == 0 ||	// @audit-issue

289:            _stakingParams.numAgentInstances == 0 || _stakingParams.timeForEmissions == 0 ||	// @audit-issue

290:            _stakingParams.minNumStakingPeriods == 0 || _stakingParams.maxNumInactivityPeriods == 0) {	// @audit-issue

310:        if (_stakingParams.activityChecker.code.length == 0) {	// @audit-issue

342:        if (_stakingParams.proxyHash == 0) {	// @audit-issue

411:        if (success && returnData.length > 63 && (returnData.length % 32 == 0)) {	// @audit-issue

450:            if (evictServiceIds[i] > 0) {	// @audit-issue

464:        for (uint256 i = numEvictServices; i > 0; --i) {	// @audit-issue

498:        if (reward == 0) {	// @audit-issue

536:        if (block.timestamp - tsCheckpointLast >= livenessPeriod && lastAvailableRewards > 0) {	// @audit-issue

612:        if (numServices > 0) {	// @audit-issue

665:        if (serviceIds.length > 0) {	// @audit-issue

676:                if (serviceInactivity[i] > 0) {	// @audit-issue

696:            if (numServices > 0) {	// @audit-issue

721:        if (availableRewards == 0) {	// @audit-issue

728:        if (sInfo.tsStart > 0) {	// @audit-issue

747:        if (configHash != 0 && configHash != service.configHash) {	// @audit-issue

751:        if (threshold > 0 && threshold != service.threshold) {	// @audit-issue

767:        if (size > 0) {	// @audit-issue

818:        if (ts <= minStakingDuration && availableRewards > 0) {	// @audit-issue

826:        if (serviceIds.length == 0) {	// @audit-issue

867:        if (reward > 0) {	// @audit-issue

932:        } else if (sInfo.tsStart > 0) {	// @audit-issue
```
[287](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L287-L287), [288](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L288-L288), [289](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L289-L289), [290](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L290-L290), [310](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L310-L310), [342](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L342-L342), [411](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L411-L411), [450](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L450-L450), [464](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L464-L464), [498](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L498-L498), [536](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L536-L536), [612](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L612-L612), [665](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L665-L665), [676](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L676-L676), [696](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L696-L696), [721](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L721-L721), [728](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L728-L728), [747](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L747-L747), [751](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L751-L751), [767](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L767-L767), [818](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L818-L818), [826](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L826-L826), [867](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L867-L867), [932](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L932-L932), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

184:        if (implementation.code.length == 0) {	// @audit-issue

220:            if (returnData.length > 0) {	// @audit-issue
```
[184](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L184-L184), [220](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L220-L220), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

260:        if (mapRemovedNominees[nomineeHash] == 0 && mapNomineeIds[nomineeHash] == 0) {	// @audit-issue

295:        if (mapNomineeIds[nomineeHash] > 0) {	// @audit-issue

300:        if (mapRemovedNominees[nomineeHash] > 0) {	// @audit-issue

331:        if (chainId == 0) {	// @audit-issue

430:        if (totalSum > 0) {	// @audit-issue

478:        if (mapRemovedNominees[nomineeHash] > 0) {	// @audit-issue

484:        if (userSlope < 0) {	// @audit-issue

598:        if (id == 0) {	// @audit-issue

647:        if (mapRemovedNominees[nomineeHash] == 0) {	// @audit-issue

653:        if (oldSlope.power == 0) {	// @audit-issue

748:        if (id == 0) {	// @audit-issue

765:        if (id == 0) {	// @audit-issue

799:            if (mapNomineeIds[nomineeHash] == 0) {	// @audit-issue
```
[260](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L260-L260), [295](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L295-L295), [300](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L300-L300), [331](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L331-L331), [430](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L430-L430), [478](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L478-L478), [484](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L484-L484), [598](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L598-L598), [647](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L647-L647), [653](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L653-L653), [748](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L748-L748), [765](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L765-L765), [799](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L799-L799), 


#### Recommendation

To optimize gas usage in your Solidity code, consider using inline assembly for checking `0`. This approach can significantly reduce gas costs, especially in high-frequency or gas-sensitive operations, leading to more efficient contract execution.

### Use assembly to write `address` storage values
Using assembly `{ sstore(state.slot, addr)}` instead of `state = addr` can save gas.


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

340:        olas = _olas;	// @audit-issue

341:        tokenomics = _tokenomics;	// @audit-issue

342:        treasury = _treasury;	// @audit-issue

343:        voteWeighting = _voteWeighting;	// @audit-issue

647:        owner = newOwner;	// @audit-issue

663:            tokenomics = _tokenomics;	// @audit-issue

669:            treasury = _treasury;	// @audit-issue

675:            voteWeighting = _voteWeighting;	// @audit-issue
```
[340](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L340-L340), [341](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L341-L341), [342](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L342-L342), [343](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L343-L343), [647](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L647-L647), [663](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L663-L663), [669](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L669-L669), [675](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L675-L675), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

106:        l1ERC20Gateway = _l1ERC20Gateway;	// @audit-issue

107:        outbox = _outbox;	// @audit-issue

108:        bridge = _bridge;	// @audit-issue
```
[106](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L106-L106), [107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L107-L107), [108](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L108-L108), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

81:        olas = _olas;	// @audit-issue

82:        l1Dispenser = _l1Dispenser;	// @audit-issue

83:        l1TokenRelayer = _l1TokenRelayer;	// @audit-issue

84:        l1MessageRelayer = _l1MessageRelayer;	// @audit-issue

196:        l2TargetDispenser = l2Dispenser;	// @audit-issue
```
[81](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L81-L81), [82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L82-L82), [83](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L83-L83), [84](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L84-L84), [196](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L196-L196), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol

91:        olasL2 = _olasL2;	// @audit-issue
```
[91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismDepositProcessorL1.sol#L91-L91), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

76:        olas = _olas;	// @audit-issue

77:        dispenser = _dispenser;	// @audit-issue

78:        stakingFactory = _stakingFactory;	// @audit-issue

79:        timelock = _timelock;	// @audit-issue
```
[76](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L76-L76), [77](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L77-L77), [78](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L78-L78), [79](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L79-L79), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

71:        fxRootTunnel = l1Processor;	// @audit-issue
```
[71](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L71-L71), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

54:        l2TokenRelayer = _l2TokenRelayer;	// @audit-issue
```
[54](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L54-L54), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

125:        olas = _olas;	// @audit-issue

126:        stakingFactory = _stakingFactory;	// @audit-issue

127:        l2MessageRelayer = _l2MessageRelayer;	// @audit-issue

128:        l1DepositProcessor = _l1DepositProcessor;	// @audit-issue

258:        owner = newOwner;	// @audit-issue
```
[125](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L125-L125), [126](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L126-L126), [127](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L127-L127), [128](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L128-L128), [258](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L258-L258), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

53:        predicate = _predicate;	// @audit-issue

110:        fxChildTunnel = l2Dispenser;	// @audit-issue
```
[53](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L53-L53), [110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L110-L110), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

87:        olas = _olas;	// @audit-issue

107:        owner = newOwner;	// @audit-issue
```
[87](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L87-L87), [107](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L107-L107), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

67:        stakingToken = _stakingToken;	// @audit-issue

68:        serviceRegistryTokenUtility = _serviceRegistryTokenUtility;	// @audit-issue
```
[67](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L67-L67), [68](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L68-L68), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

105:        verifier = _verifier;	// @audit-issue

121:        owner = newOwner;	// @audit-issue

133:        verifier = newVerifier;	// @audit-issue
```
[105](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L105-L105), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L121-L121), [133](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L133-L133), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

213:        ve = _ve;	// @audit-issue

379:        owner = newOwner;	// @audit-issue

392:        dispenser = newDispenser;	// @audit-issue
```
[213](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L213-L213), [379](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L379-L379), [392](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L392-L392), 


#### Recommendation

To reduce gas costs in your Solidity code, consider using assembly with `{ sstore(state.slot, addr) }` for writing `address` storage values instead of `state = addr`. This approach can result in significant gas savings.

### Use assembly to emit an `event`
To efficiently emit events, it's possible to utilize assembly by making use of scratch space and the free memory pointer. This approach has the advantage of potentially avoiding the costs associated with memory expansion.

However, it's important to note that in order to safely optimize this process, it is preferable to cache and restore the free memory pointer.

A good example of such practice can be seen in [Solady's](https://github.com/Vectorized/solady/blob/main/src/tokens/ERC1155.sol#L167) codebase.


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

834:        emit IncentivesClaimed(msg.sender, reward, topUp);	// @audit-issue

1044:        emit StakingIncentivesClaimed(msg.sender, stakingIncentive, transferAmount, returnAmount);	// @audit-issue

1123:        emit StakingIncentivesClaimed(msg.sender, totalAmounts[0], totalAmounts[1], totalAmounts[2]);	// @audit-issue

1165:        emit Retained(msg.sender, totalReturnAmount);	// @audit-issue

1191:        emit WithheldAmountSynced(chainId, amount, withheldAmount);	// @audit-issue

1236:        emit WithheldAmountSynced(chainId, amount, withheldAmount);	// @audit-issue

1249:        emit PauseDispenser(pauseState);	// @audit-issue
```
[648](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L648-L648), [664](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L664-L664), [670](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L670-L670), [676](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L676-L676), [697](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L697-L697), [727](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L727-L727), [834](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L834-L834), [1044](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1044-L1044), [1123](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1123-L1123), [1165](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1165-L1165), [1191](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1191-L1191), [1236](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1236-L1236), [1249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1249-L1249), 


```solidity
Path: ./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol

91:        emit MessagePosted(0, msg.sender, l1DepositProcessor, amount);	// @audit-issue
```
[91](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/OptimismTargetDispenserL2.sol#L91-L91), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

118:        emit MessageReceived(l2TargetDispenser, l2TargetChainId, data);	// @audit-issue

155:        emit MessagePosted(sequence, targets, stakingIncentives, transferAmount);	// @audit-issue

181:        emit MessagePosted(sequence, targets, stakingIncentives, transferAmount);	// @audit-issue

201:        emit L2TargetDispenserUpdated(l2Dispenser);	// @audit-issue
```
[118](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L118-L118), [155](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L155-L155), [181](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L181-L181), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L201-L201), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

60:        emit MessagePosted(sequence, msg.sender, l1DepositProcessor, amount);	// @audit-issue
```
[60](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L60-L60), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

114:                emit AmountRefunded(target, refundAmount);	// @audit-issue

121:            emit StakingTargetDeposited(target, amount);	// @audit-issue
```
[114](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L114-L114), [121](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L121-L121), 


```solidity
Path: ./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol

123:        emit MessagePosted(sequence, msg.sender, l1DepositProcessor, amount);	// @audit-issue
```
[123](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/WormholeTargetDispenserL2.sol#L123-L123), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

41:        emit MessagePosted(0, msg.sender, l1DepositProcessor, amount);	// @audit-issue

73:        emit FxRootTunnelUpdated(l1Processor);	// @audit-issue
```
[41](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L41-L41), [73](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L73-L73), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

82:        emit MessagePosted(uint256(iMsg), msg.sender, l1DepositProcessor, amount);	// @audit-issue
```
[82](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L82-L82), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

173:                emit AmountWithheld(target, amount);	// @audit-issue

185:                emit AmountWithheld(target, targetWithheldAmount);	// @audit-issue

194:                emit StakingTargetDeposited(target, amount);	// @audit-issue

201:                emit StakingRequestQueued(queueHash, target, amount, batchNonce, localPaused);	// @audit-issue

239:        emit MessageReceived(l1DepositProcessor, l1SourceChainId, data);	// @audit-issue

259:        emit OwnerUpdated(newOwner);	// @audit-issue

293:            emit StakingTargetDeposited(target, amount);	// @audit-issue

347:        emit WithheldAmountSynced(msg.sender, amount);	// @audit-issue

360:        emit TargetDispenserPaused();	// @audit-issue

371:        emit TargetDispenserUnpaused();	// @audit-issue

401:        emit Drain(msg.sender, amount);	// @audit-issue

452:        emit Migrated(msg.sender, newL2TargetDispenser, amount);	// @audit-issue

464:        emit FundsReceived(msg.sender, msg.value);	// @audit-issue
```
[173](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L173-L173), [185](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L185-L185), [194](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L194-L194), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L201-L201), [239](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L239-L239), [259](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L259-L259), [293](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L293-L293), [347](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L347-L347), [360](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L360-L360), [371](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L371-L371), [401](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L401-L401), [452](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L452-L452), [464](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L464-L464), 


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
Path: ./registries/contracts/staking/StakingNativeToken.sol

48:        emit Deposit(msg.sender, msg.value, newBalance, newAvailableRewards);	// @audit-issue
```
[48](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingNativeToken.sol#L48-L48), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

475:        emit ServicesEvicted(epochCounter, serviceIds, owners, multisigs, inactivity);	// @audit-issue

510:        emit RewardClaimed(epochCounter, serviceId, msg.sender, multisig, sInfo.nonces, reward);	// @audit-issue

687:                        emit ServiceInactivityWarning(eCounter, curServiceId, serviceInactivity[i]);	// @audit-issue

708:            emit Checkpoint(eCounter, lastAvailableRewards, finalEligibleServiceIds, finalEligibleServiceRewards,	// @audit-issue

799:        emit ServiceStaked(epochCounter, serviceId, msg.sender, service.multisig, nonces);	// @audit-issue

871:        emit ServiceUnstaked(epochCounter, serviceId, msg.sender, multisig, nonces, reward);	// @audit-issue
```
[475](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L475-L475), [510](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L510-L510), [687](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L687-L687), [708](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L708-L708), [799](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L799-L799), [871](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L871-L871), 


```solidity
Path: ./registries/contracts/staking/StakingToken.sol

110:        emit Withdraw(to, amount);	// @audit-issue

127:        emit Deposit(msg.sender, amount, newBalance, newAvailableRewards);	// @audit-issue
```
[110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L110-L110), [127](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingToken.sol#L127-L127), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

122:        emit OwnerUpdated(newOwner);	// @audit-issue

134:        emit VerifierUpdated(newVerifier);	// @audit-issue

241:        emit InstanceCreated(msg.sender, instance, implementation);	// @audit-issue

261:        emit InstanceStatusChanged(instance, isEnabled);	// @audit-issue
```
[122](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L122-L122), [134](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L134-L134), [241](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L241-L241), [261](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L261-L261), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

318:        emit AddNominee(nominee.account, nominee.chainId, id);	// @audit-issue

380:        emit OwnerUpdated(newOwner);	// @audit-issue

393:        emit DispenserUpdated(newDispenser);	// @audit-issue

400:        emit Checkpoint(msg.sender, totalSum);	// @audit-issue

410:        emit CheckpointNominee(msg.sender, account, chainId, nomineeWeight, totalSum);	// @audit-issue

466:        emit NomineeRelativeWeightWrite(msg.sender, account, chainId, nomineeWeight, totalSum, relativeWeight);	// @audit-issue

556:        emit VoteForNominee(msg.sender, account, chainId, weight);	// @audit-issue

635:        emit RemoveNominee(account, chainId, newSum);	// @audit-issue
```
[318](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L318-L318), [380](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L380-L380), [393](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L393-L393), [400](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L400-L400), [410](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L410-L410), [466](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L466-L466), [556](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L556-L556), [635](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L635-L635), 


#### Recommendation

To optimize event emission in your Solidity code, consider using assembly with scratch space and the free memory pointer. This approach can help reduce gas costs by avoiding memory expansion expenses. However, it's crucial to ensure safe optimization by caching and restoring the free memory pointer, as demonstrated in examples like Solady's codebase.

### Use assembly to validate `msg.sender`
We can use assembly to efficiently validate `msg.sender` with the least amount of opcodes necessary. For more details check the following report [Here](https://code4rena.com/reports/2023-05-juicebox#g-06-use-assembly-to-validate-msgsender)


```solidity
Path: ./tokenomics/contracts/Tokenomics.sol

528:        if (msg.sender != owner) {	// @audit-issue

548:        if (msg.sender != owner) {	// @audit-issue

567:        if (msg.sender != owner) {	// @audit-issue

594:        if (msg.sender != owner) {	// @audit-issue

618:        if (msg.sender != owner) {	// @audit-issue

647:        if (msg.sender != owner) {	// @audit-issue

712:        if (msg.sender != owner) {	// @audit-issue

753:        if (msg.sender != owner) {	// @audit-issue

789:        if (depository != msg.sender) {	// @audit-issue

810:        if (depository != msg.sender) {	// @audit-issue

828:        if (dispenser != msg.sender) {	// @audit-issue

997:        if (treasury != msg.sender) {	// @audit-issue

1314:        if (dispenser != msg.sender) {	// @audit-issue
```
[528](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L528-L528), [548](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L548-L548), [567](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L567-L567), [594](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L594-L594), [618](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L618-L618), [647](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L647-L647), [712](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L712-L712), [753](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L753-L753), [789](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L789-L789), [810](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L810-L810), [828](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L828-L828), [997](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L997-L997), [1314](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Tokenomics.sol#L1314-L1314), 


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

638:        if (msg.sender != owner) {	// @audit-issue

657:        if (msg.sender != owner) {	// @audit-issue

685:        if (msg.sender != owner) {	// @audit-issue

707:        if (msg.sender != owner) {	// @audit-issue

734:        if (msg.sender != voteWeighting) {	// @audit-issue

752:        if (msg.sender != voteWeighting) {	// @audit-issue

1178:        if (msg.sender != depositProcessor) {	// @audit-issue

1201:        if (msg.sender != owner) {	// @audit-issue

1243:        if (msg.sender != owner) {	// @audit-issue
```
[638](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L638-L638), [657](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L657-L657), [685](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L685-L685), [707](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L707-L707), [734](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L734-L734), [752](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L752-L752), [1178](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1178-L1178), [1201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1201-L1201), [1243](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L1243-L1243), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol

198:        if (msg.sender != bridge) {	// @audit-issue
```
[198](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumDepositProcessorL1.sol#L198-L198), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol

139:        if (msg.sender != l1Dispenser) {	// @audit-issue

171:        if (msg.sender != l1Dispenser) {	// @audit-issue

188:        if (msg.sender != owner) {	// @audit-issue
```
[139](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L139-L139), [171](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L171-L171), [188](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultDepositProcessorL1.sol#L188-L188), 


```solidity
Path: ./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol

68:        if (msg.sender != l1AliasedDepositProcessor) {	// @audit-issue
```
[68](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/ArbitrumTargetDispenserL2.sol#L68-L68), 


```solidity
Path: ./tokenomics/contracts/staking/EthereumDepositProcessor.sol

137:        if (msg.sender != dispenser) {	// @audit-issue

162:        if (msg.sender != dispenser) {	// @audit-issue
```
[137](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L137-L137), [162](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/EthereumDepositProcessor.sol#L162-L162), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol

61:        if (msg.sender != owner) {	// @audit-issue
```
[61](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonTargetDispenserL2.sol#L61-L61), 


```solidity
Path: ./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol

101:        if (msg.sender != l2TokenRelayer) {	// @audit-issue
```
[101](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/GnosisTargetDispenserL2.sol#L101-L101), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

249:        if (msg.sender != owner) {	// @audit-issue

313:        if (msg.sender != owner) {	// @audit-issue

355:        if (msg.sender != owner) {	// @audit-issue

366:        if (msg.sender != owner) {	// @audit-issue

385:        if (msg.sender != owner) {	// @audit-issue

420:        if (msg.sender != owner) {	// @audit-issue
```
[249](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L249-L249), [313](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L313-L313), [355](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L355-L355), [366](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L366-L366), [385](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L385-L385), [420](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L420-L420), 


```solidity
Path: ./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol

100:        if (msg.sender != owner) {	// @audit-issue
```
[100](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/PolygonDepositProcessorL1.sol#L100-L100), 


```solidity
Path: ./registries/contracts/staking/StakingVerifier.sol

98:        if (msg.sender != owner) {	// @audit-issue

115:        if (owner != msg.sender) {	// @audit-issue

137:        if (owner != msg.sender) {	// @audit-issue

235:        if (owner != msg.sender) {	// @audit-issue
```
[98](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L98-L98), [115](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L115-L115), [137](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L137-L137), [235](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingVerifier.sol#L235-L235), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

485:        if (msg.sender != sInfo.owner) {	// @audit-issue

808:        if (msg.sender != sInfo.owner) {	// @audit-issue
```
[485](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L485-L485), [808](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L808-L808), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

112:        if (msg.sender != owner) {	// @audit-issue

129:        if (msg.sender != owner) {	// @audit-issue

255:        if (msg.sender != deployer) {	// @audit-issue
```
[112](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L112-L112), [129](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L129-L129), [255](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L255-L255), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

370:        if (msg.sender != owner) {	// @audit-issue

388:        if (msg.sender != owner) {	// @audit-issue

588:        if (msg.sender != owner) {	// @audit-issue
```
[370](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L370-L370), [388](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L388-L388), [588](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L588-L588), 


#### Recommendation

To optimize the validation of `msg.sender` in your Solidity code, consider using assembly to achieve this with the minimum number of opcodes required. You can refer to the detailed report [Here](https://code4rena.com/reports/2023-05-juicebox#g-06-use-assembly-to-validate-msgsender) for more insights and examples on efficient implementation.

### Use assembly to write hashes
Considering using [assembly](https://medium.com/@kalexotsu/understanding-solidity-assembly-hashing-a-string-from-calldata-fbd2ece82263) to write hashes, as it's possible to save a considerable amount of gas.


```solidity
Path: ./tokenomics/contracts/Dispenser.sol

346:        retainerHash = keccak256(abi.encode(IVoteWeighting.Nominee(retainer, block.chainid)));	// @audit-issue

871:        nomineeHash = keccak256(abi.encode(IVoteWeighting.Nominee(stakingTarget, chainId)));	// @audit-issue
```
[346](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L346-L346), [871](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/Dispenser.sol#L871-L871), 


```solidity
Path: ./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol

197:                bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));	// @audit-issue

279:        bytes32 queueHash = keccak256(abi.encode(target, amount, batchNonce));	// @audit-issue
```
[197](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L197-L197), [279](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L279-L279), 


```solidity
Path: ./registries/contracts/staking/StakingBase.sol

760:        bytes32 multisigProxyHash = keccak256(service.multisig.code);	// @audit-issue
```
[760](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingBase.sol#L760-L760), 


```solidity
Path: ./registries/contracts/staking/StakingFactory.sol

143:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue

150:        bytes32 hash = keccak256(	// @audit-issue

152:                bytes1(0xff), address(this), salt, keccak256(deploymentData)	// @audit-issue

201:        bytes32 salt = keccak256(abi.encodePacked(block.chainid, localNonce));	// @audit-issue
```
[143](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L143-L143), [150](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L150-L150), [152](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L152-L152), [201](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./registries/contracts/staking/StakingFactory.sol#L201-L201), 


```solidity
Path: ./governance/contracts/VoteWeighting.sol

259:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

294:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

428:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

475:        bytes32 nomineeHash = keccak256(abi.encode(Nominee(account, chainId)));	// @audit-issue

594:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

623:        bytes32 replacedNomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

644:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

677:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

723:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

735:        bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue

796:            bytes32 nomineeHash = keccak256(abi.encode(nominee));	// @audit-issue
```
[259](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L259-L259), [294](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L294-L294), [428](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L428-L428), [475](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L475-L475), [594](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L594-L594), [623](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L623-L623), [644](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L644-L644), [677](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L677-L677), [723](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L723-L723), [735](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L735-L735), [796](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/./governance/contracts/VoteWeighting.sol#L796-L796), 


#### Recommendation

To achieve gas savings in your Solidity code when writing hashes, consider using assembly, as it can significantly reduce gas consumption. You can refer to this [article](https://medium.com/@kalexotsu/understanding-solidity-assembly-hashing-a-string-from-calldata-fbd2ece82263) for insights on how to efficiently implement hash operations.