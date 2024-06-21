# QA Report

## [L-01] `VoteWeighting::voteForNomineeWeightsBatch` & `VoteWeighting::getNextAllowedVotingTimes` susecptible to DOS attacks
Both these functions takes the `accounts` as a param and loops over it until `accounts.length` A malicious user could pass in very large arrays to exhaust the gas limit, potentially causing a denial-of-service (DoS) attack. If the transaction fails due to exceeding the gas limit, block space is wasted on a failed transaction, reducing the number of successful transactions that can be included in that block. Multiple attempts by the attacker can also congest the network, delaying other transactions.

https://github.com/code-423n4/2024-05-olas/blob/main/governance/contracts/VoteWeighting.sol#L563-L576
```solidity
 function voteForNomineeWeightsBatch(
        bytes32[] memory accounts,
        uint256[] memory chainIds,
        uint256[] memory weights
    ) external {
        if (accounts.length != chainIds.length || accounts.length != weights.length) {
            revert WrongArrayLength(accounts.length, weights.length);
        }

        // Traverse all accounts and weights
@>      for (uint256 i = 0; i < accounts.length; ++i) {
            voteForNomineeWeights(accounts[i], chainIds[i], weights[i]);
        }
    }
```

https://github.com/code-423n4/2024-05-olas/blob/main/governance/contracts/VoteWeighting.sol#L779-L806
```solidity
function getNextAllowedVotingTimes(
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
@>       for (uint256 i = 0; i < accounts.length; ++i) {
            // Get the nominee struct and hash
            Nominee memory nominee = Nominee(accounts[i], chainIds[i]);
            bytes32 nomineeHash = keccak256(abi.encode(nominee));

            // Check for nominee existence
            if (mapNomineeIds[nomineeHash] == 0) {
                revert NomineeDoesNotExist(accounts[i], chainIds[i]);
            }

            // Calculate next allowed voting times
            nextAllowedVotingTimes[i] = lastUserVote[voters[i]][nomineeHash] + WEIGHT_VOTE_DELAY;
        }
    }
```

## Recommended Mitigation:
To prevent such attacks, you can set a reasonable limit on the size of the arrays processed by these functions and revert if the length exceeds the limit.

## [L-02] Critical Address Changes Should Use Two-Step Procedure
`owner` has critical privileges in the protocol. `changeOwner` from `VoteWeighting`, `StakingFactory` and `Tokenomics` allows the owner to transfer ownership in a one-step process. Especially in functions like `setL2TargetDispenser` in `DefaultDepositPRocessorL1` where the owner role is Revoked making the contract ownerless after one critical step. Where the protocol becomes the victim of a Clipboard Replacement Attack, where the owner copies the address that ownership is supposed to be transferred to, but malware replaces the address on the clipboard with a different, attacker-controlled address.

https://github.com/code-423n4/2024-05-olas/blob/main/governance/contracts/VoteWeighting.sol#L368-L381
```solidity
   function changeOwner(address newOwner) external {
        // Check for the contract ownership
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }

        // Check for the zero address
        if (newOwner == address(0)) {
            revert ZeroAddress();
        }

@>      owner = newOwner;
        emit OwnerUpdated(newOwner);
    }
```

## Recommended Mitigation:
Step 1: Propose a New Owner
```solidity
// Step 1: Propose a new owner
function proposeNewOwner(address _newOwner) external onlyAuthorized {
    newOwner = _newOwner;
    emit NewOwnerProposed(_newOwner);
}
```
Step 2: Accept Ownership
```solidity
// Step 2: New owner accepts the ownership
function acceptOwnership() external {
    require(msg.sender == newOwner, "Not the proposed owner");
    emit OwnershipTransferred(owner, newOwner);
    owner = newOwner;
    newOwner = address(0);
}
```

## [L-03] Inconsistency in comments and implementation in `OLAS::inflationRemainder`
As per the comment `After 10 years, adjust supplyCap according to the yearly inflation % set in maxMintCapFraction`. The `supplyCap` should be adjusted after the 10-year period has passed. But it is adjusted after 9 full years have passed, prematurely adjusting the `supplyCap`.

https://github.com/code-423n4/2024-05-olas/blob/main/governance/contracts/OLAS.sol#L98-L114
```solidity
   function inflationRemainder() public view returns (uint256 remainder) {
        uint256 _totalSupply = totalSupply;
        // Current year
        uint256 numYears = (block.timestamp - timeLaunch) / oneYear;
        // Calculate maximum mint amount to date
        uint256 supplyCap = tenYearSupplyCap;
@>      // After 10 years, adjust supplyCap according to the yearly inflation % set in maxMintCapFraction
        if (numYears > 9) {
            // Number of years after ten years have passed (including ongoing ones)
            numYears -= 9;
            for (uint256 i = 0; i < numYears; ++i) {
                supplyCap += (supplyCap * maxMintCapFraction) / 100;
            }
        }
        // Check for the requested mint overflow
        remainder = supplyCap - _totalSupply;
    }
```
## Recommended Mitigation:
Modify the logic to ensure that `supplyCap` adjustments occur only after a full 10-year period has elapsed. (or) Revise the comment to accurately reflect the current behavior of the function.

## [L-04] Check for overflow before downcasting in `Tokenomics::_finalizeIncentivesForUnitId`

This function casts `totalIncentives` uint256 to uint96 before assigning to `mapUnitIncentives[unitType][unitId].reward` and `mapUnitIncentives[unitType][unitId].topUp`. Ensure that `totalIncentives` does not exceed type(uint96).max to prevent truncation or overflow issues. Like checking in`refundFromBondProgram` and `refundFromStaking` for `eBond` and `stakingIncentive` respectively.

https://github.com/code-423n4/2024-05-olas/blob/main/tokenomics/contracts/Tokenomics.sol#L847-L877
```solidity
function _finalizeIncentivesForUnitId(uint256 epochNum, uint256 unitType, uint256 unitId) internal {
        // Gets the overall amount of unit rewards for the unit's last epoch
        // The pendingRelativeReward can be zero if the rewardUnitFraction was zero in the first place
        // Note that if the rewardUnitFraction is set to zero at the end of epoch, the whole pending reward will be zero
        // reward = (pendingRelativeReward * rewardUnitFraction) / 100
        uint256 totalIncentives = mapUnitIncentives[unitType][unitId].pendingRelativeReward;
        if (totalIncentives > 0) {
            totalIncentives *= mapEpochTokenomics[epochNum].unitPoints[unitType].rewardUnitFraction;
            // Add to the final reward for the last epoch
            totalIncentives = mapUnitIncentives[unitType][unitId].reward + totalIncentives / 100;
@>          mapUnitIncentives[unitType][unitId].reward = uint96(totalIncentives);
            // Setting pending reward to zero
            mapUnitIncentives[unitType][unitId].pendingRelativeReward = 0;
        }

        // Add to the final top-up for the last epoch
        totalIncentives = mapUnitIncentives[unitType][unitId].pendingRelativeTopUp;
        // The pendingRelativeTopUp can be zero if the service owner did not stake enough veOLAS
        // The topUpUnitFraction was checked before and if it were zero, pendingRelativeTopUp would be zero as well
        if (totalIncentives > 0) {
            // Summation of all the unit top-ups and total amount of top-ups per epoch
            // topUp = (pendingRelativeTopUp * totalTopUpsOLAS * topUpUnitFraction) / (100 * sumUnitTopUpsOLAS)
            totalIncentives *= mapEpochTokenomics[epochNum].epochPoint.totalTopUpsOLAS;
            totalIncentives *= mapEpochTokenomics[epochNum].unitPoints[unitType].topUpUnitFraction;
            uint256 sumUnitIncentives = uint256(mapEpochTokenomics[epochNum].unitPoints[unitType].sumUnitTopUpsOLAS) * 100;
            totalIncentives = mapUnitIncentives[unitType][unitId].topUp + totalIncentives / sumUnitIncentives;
@>          mapUnitIncentives[unitType][unitId].topUp = uint96(totalIncentives);
            // Setting pending top-up to zero
            mapUnitIncentives[unitType][unitId].pendingRelativeTopUp = 0;
        }
    }
```
## Recommended Mitigation:
Add a check before the assignments
```solidity
if (totalIncentives > type(uint96).max) {
  revert Overflow(totalIncentives, type(uint96).max);
}
```

## [L-05] Natspec docs missing a param for `Tokenomics::changeIncentiveFractions`
The `changeIncentiveFractions` function lacks Natspec documentation for the `_stakingFraction` parameter

https://github.com/code-423n4/2024-05-olas/blob/main/tokenomics/contracts/Tokenomics.sol#L696-L710
```solidity
    /// @dev Sets incentive parameter fractions.
    /// @param _rewardComponentFraction Fraction for component owner rewards funded by ETH donations.
    /// @param _rewardAgentFraction Fraction for agent owner rewards funded by ETH donations.
    /// @param _maxBondFraction Fraction for the maxBond that depends on the OLAS inflation.
    /// @param _topUpComponentFraction Fraction for component owners OLAS top-up.
    /// @param _topUpAgentFraction Fraction for agent owners OLAS top-up.
    /// #if_succeeds {:msg "maxBond"} mapEpochTokenomics[epochCounter + 1].epochPoint.maxBondFraction == _maxBondFraction;
    function changeIncentiveFractions(
        uint256 _rewardComponentFraction,
        uint256 _rewardAgentFraction,
        uint256 _maxBondFraction,
        uint256 _topUpComponentFraction,
        uint256 _topUpAgentFraction,
        uint256 _stakingFraction
    ) external {
      ...
    }
```
## [L-06] Natspec docs inconsistency in `IBridgeErrors`
The comment format inconsistency in the `ReentrancyGuard` error declaration within `IBridgeErrors.sol`. The comment uses `//` instead of `/// @dev`, which diverges from standard documentation practices across the project.

https://github.com/code-423n4/2024-05-olas/blob/main/tokenomics/contracts/interfaces/IBridgeErrors.sol#L89-L90
```solidity
    // @dev Reentrancy guard.
    error ReentrancyGuard();
```

## [L-07] Event Emission for `_withdraw` in `StakingNativeToken`
In the `StakingNativeToken` contract, the `_withdraw` function does not emit a `Withdraw` event upon successful withdrawal of tokens. But in the `StakingToken` contract, where the `Withdraw` event is emitted upon successful token withdrawals.

https://github.com/code-423n4/2024-05-olas/blob/main/registries/contracts/staking/StakingNativeToken.sol#L28-L37
```solidity
    function _withdraw(address to, uint256 amount) internal override {
        // Update the contract balance
        balance -= amount;

        // Transfer the amount
        (bool success, ) = to.call{value: amount}("");
        if (!success) {
            revert TransferFailed(address(0), address(this), to, amount);
        }
    }
```

## [L-08] Can improve consistently & readability in `Tokenomics::changeTokenomicsParameters` and `Tokenomics::_finalizeIncentivesForUnitId`
We can improve the readability of the `changeTokenomicsParameters` function, by removing redundant casting operations for variables (`_devsPerCapital`, `_codePerDev`, `_epochLen`, `_veOLASThreshold`). These redundant casts do not provide additional type safety or clarity but add complexity and reduce readability, and also save a bit in gas. Similarly for `mapEpochTokenomics[epochNum].unitPoints[unitType]` in `_finalizeIncentivesForUnitId` can also be considered.

https://github.com/code-423n4/2024-05-olas/blob/main/tokenomics/contracts/Tokenomics.sol#L639-L694
```solidity
function changeTokenomicsParameters(
        uint256 _devsPerCapital,
        uint256 _codePerDev,
        uint256 _epsilonRate,
        uint256 _epochLen,
        uint256 _veOLASThreshold
    ) external {
        // Check for the contract ownership
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }

        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
@>      if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {
            devsPerCapital = uint72(_devsPerCapital);
        } else {
            // This is done in order not to pass incorrect parameters into the event
            _devsPerCapital = devsPerCapital;
        }

        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
@>      if (uint72(_codePerDev) > MIN_PARAM_VALUE) {
            codePerDev = uint72(_codePerDev);
        } else {
            // This is done in order not to pass incorrect parameters into the event
            _codePerDev = codePerDev;
        }

        // Check the epsilonRate value for idf to fit in its size
        // 2^64 - 1 < 18.5e18, idf is equal at most 1 + epsilonRate < 18e18, which fits in the variable size
        // epsilonRate is the part of the IDF calculation and thus its change will be accounted for in the next epoch
        if (_epsilonRate > 0 && _epsilonRate <= 17e18) {
            epsilonRate = uint64(_epsilonRate);
        } else {
            _epsilonRate = epsilonRate;
        }

        // Check for the epochLen value to change
@>      if (uint32(_epochLen) >= MIN_EPOCH_LENGTH && uint32(_epochLen) <= MAX_EPOCH_LENGTH) {
            nextEpochLen = uint32(_epochLen);
        } else {
            _epochLen = epochLen;
        }

        // Adjust veOLAS threshold for the next epoch
@>      if (uint96(_veOLASThreshold) > 0) {
            nextVeOLASThreshold = uint96(_veOLASThreshold);
        } else {
            _veOLASThreshold = veOLASThreshold;
        }

        // Set the flag that tokenomics parameters are requested to be updated (1st bit is set to one)
        tokenomicsParametersUpdated = tokenomicsParametersUpdated | 0x01;
        emit TokenomicsParametersUpdateRequested(epochCounter + 1, _devsPerCapital, _codePerDev, _epsilonRate, _epochLen,
            _veOLASThreshold);
    }
```

## [L-09] Use Safe Transfer Method Instead of `call` in `StakingNativeToken`
The `_withdraw` function in `StakingNativeToken` uses `call` to transfer tokens to `to`. This approach forwards all remaining gas to the recipient contract, where the `transfer` provides a fixed gas stipend of 2300 gas units to the recipient’s receive or fallback function in order to prevent any reentrance attacks. Where `StakingToken` uses `safeTransfer` from `SafeTransferLib` for its `_withdraw`.

https://github.com/code-423n4/2024-05-olas/blob/main/registries/contracts/staking/StakingNativeToken.sol#L28-L37
```solidity
    function _withdraw(address to, uint256 amount) internal override {
        // Update the contract balance
        balance -= amount;

        // Transfer the amount
@>      (bool success, ) = to.call{value: amount}("");
        if (!success) {
            revert TransferFailed(address(0), address(this), to, amount);
        }
    })
```

## [L-10] Missing `_donatorBlacklist` Address Check in `Tokenomics::initializeTokenomics`
Here, the input param `_donatorBlacklist` is assigned to the state variable `donatorBlacklist` without any checks against `msg.sender`. According to the comment `#if_succeeds {:msg "donatorBlacklist assignment"} donatorBlacklist == _donatorBlacklist;` compared to other params' comment, which could potentially allow `_donatorBlacklist` to be zero address, but we can check that `_donatorBlacklist` is not `msg.sender`.

 ## [L-11] Old version of OpenZeppelin Contracts used
Using old versions of 3rd-party libraries in the build process can introduce vulnerabilities to the protocol code.
The latest version is 5.0.2, version used in project is 4.8.3 & ^4.2.0.

### Risks
- Older versions of OpenZeppelin contracts might not have fixes for known security vulnerabilities.
- Older versions might contain features or functionality that have been deprecated in recent versions.
- Newer versions often come with new features, optimizations, and improvements that are not available in older versions.

 ## [L-12] Redundant Owner Check Implementation
Throughout the codebase, there is redundant implementation of owner verification using the statement `if (msg.sender != owner) { revert OwnerOnly(msg.sender, owner); }`. This pattern is repeated across multiple functions. To enhance code readability, maintainability, and reduce duplication, consider implementing a modifier for owner-only functions. Or can also use [Ownable.sol](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/access/Ownable.sol) from openzeppelin-contracts.

 ## [L-13] Order of functions
Ordering helps readers identify which functions they can call and to find the constructor and fallback definitions easier. But there are contracts in the project that do not comply with this.
The solidity [documentation](https://docs.soliditylang.org/en/v0.8.16/style-guide.html#order-of-functions) recommends the following order for functions:
- constructor
- receive function (if exists)
- fallback function (if exists)
- external
- public
- internal
- private