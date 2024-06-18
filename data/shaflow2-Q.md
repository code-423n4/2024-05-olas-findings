# [L-1]VerifyInstance function did not check timeForEmissionsLimit
## impact
The verifyInstance function of the StackingVerifier contract did not check the timeForEmissionsLimit field, which may result in the instances exceeding the emissionsLimit
github:[https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/contracts/staking/StakingVerifier.sol#L179](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/contracts/staking/StakingVerifier.sol#L179)
```solidity
  function verifyInstance(address instance, address implementation) external view returns (bool) {
        // If the implementations check is true, and the implementation is not whitelisted, the verification is failed
        if (implementationsCheck && !mapImplementations[implementation]) {
            return false;
        }

        // Check that instance is the contract when it is not checked against the implementation
        if (instance.code.length == 0) {
            return false;
        }

        // Check for the staking parameters
        // This is a must have parameter for all staking contracts
        uint256 rewardsPerSecond = IStaking(instance).rewardsPerSecond();
        if (rewardsPerSecond > rewardsPerSecondLimit) {
            return false;
        }

        // Check for the number of services
        // This is a must have parameter for all staking contracts
        uint256 numServices = IStaking(instance).maxNumServices();
        if (numServices > numServicesLimit) {
            return false;
        }
        ...
 }

```
## Recommended Mitigation Steps
Check the timeForEmissionsLimit field of instance
```solidity
        uint256 timeForEmissions = IStaking(instance).timeForEmissions()
        if(timeForEmissions > timeForEmissionsLimit ){
            return false;
        }
```

# [L-2] The voteForNomineWeights function does not check if a nominee has been added

## impact
The voteForNomineWeights function only checks whether the nodeeHash is located in the mapRemovedNominees map when checking it, but does not check whether it is located in the mapNomineIds map.
github:[https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L473](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L473)
```solidity
function voteForNomineeWeights(bytes32 account, uint256 chainId, uint256 weight) public {
        // Get the nominee hash
        bytes32 nomineeHash = keccak256(abi.encode(Nominee(account, chainId)));

        // Check for the previously removed nominee
        if (mapRemovedNominees[nomineeHash] > 0) {
            revert NomineeRemoved(account, chainId);
        }
```
This may result in voting for accounts that do not exist in mapNomineIds

## Recommended Mitigation Steps
Check if the nominee is in mapNomineIds before voting
```solidity
function voteForNomineeWeights(bytes32 account, uint256 chainId, uint256 weight) public {
        // Get the nominee hash
        bytes32 nomineeHash = keccak256(abi.encode(Nominee(account, chainId)));

+        if (mapNomineeIds[nomineeHash] == 0) {
+            revert();
+        }

        // Check for the previously removed nominee
        if (mapRemovedNominees[nomineeHash] > 0) {
            revert NomineeRemoved(account, chainId);
        }
```

# [L-3] When calling getWeight function, the update status of timeWeight should be checked

## impact
_GetWeight is used to iteratively calculate the weight of a nominee,It should be noted that the loop can last up to 53 times, and after more than 53 times, the loop will automatically exit and return the current calculation result.
github:[https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L267C1-L286C10](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L267C1-L286C10)
```solidity
        for (uint256 i = 0; i < MAX_NUM_WEEKS; i++) {
            if (t > block.timestamp) {
                break;
            }
            t += WEEK;
            uint256 dBias = pt.slope * WEEK;
            if (pt.bias > dBias) {
                pt.bias -= dBias;
                uint256 dSlope = changesWeight[nomineeHash][t];
                pt.slope -= dSlope;
            } else {
                pt.bias = 0;
                pt.slope = 0;
            }

            pointsWeight[nomineeHash][t] = pt;
            if (t > block.timestamp) {
                timeWeight[nomineeHash] = t;
            }
        }
```
In the case of a large number of nominees, it is possible for a nominee to be ignored and not updated for more than 53 weeks. However, in some sumWeight and sumPoints calculations involving calls, there is no check whether the weights of the nominees have been updated to the latest time, which may affect the calculation
## Recommended Mitigation Steps
It is recommended to follow the following format when calling the getWeight function
```solidity
        uint nextTime = (block.timestamp + WEEK) / WEEK;
        uint weight;
        while(timeWeight[nomineeHash] != nextTime) {
                weight = _getWeight(account, chainId);
        }
```

# [L-4] Failure to impose any restrictions on adding nominees may result in malicious individuals frequently adding meaningless nominees
## impact
The addNomineEVM and addNomineNonEVM of the VoteWeight contract do not impose any restrictions on the outside world, which may lead to malicious individuals frequently adding meaningless nominees, increasing the system's cost of identifying and processing correct nominees.Causing setNominees array growth.
github:[https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L321C1-L364C6](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L321C1-L364C6)
```solidity
    /// @dev Add EVM nominee address along with the chain Id.
    /// @param account Address of the nominee.
    /// @param chainId Chain Id.
    function addNomineeEVM(address account, uint256 chainId) external {
        // Check for the zero address
        if (account == address(0)) {
            revert ZeroAddress();
        }

        // Check for zero chain Id
        if (chainId == 0) {
            revert ZeroValue();
        }

        // Check for the chain Id overflow
        if (chainId > MAX_EVM_CHAIN_ID) {
            revert Overflow(chainId, MAX_EVM_CHAIN_ID);
        }

        Nominee memory nominee = Nominee(bytes32(uint256(uint160(account))), chainId);

        // Record nominee instance
        _addNominee(nominee);
    }

    /// @dev Add Non-EVM nominee address along with the chain Id.
    /// @param account Address of the nominee in byte32 standard.
    /// @param chainId Chain Id.
    function addNomineeNonEVM(bytes32 account, uint256 chainId) external {
        // Check for the zero address
        if (account == bytes32(0)) {
            revert ZeroAddress();
        }

        // Check for the chain Id underflow
        if (MAX_EVM_CHAIN_ID >= chainId) {
            revert Underflow(chainId, MAX_EVM_CHAIN_ID + 1);
        }

        Nominee memory nominee = Nominee(account, chainId);

        // Record nominee instance
        _addNominee(nominee);
    }
```
## Recommended Mitigation Steps
Perform some compliance checks for adding nominees

# [L-5] Repeated storage write
## impact
In the removeNominee function, timeWeight[nodeeHash] has been repeatedly written. Normally, timeWeight[nodeeHash] will be updated to the latest in the _getWeight function
github:[https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L607](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L607)
```solidity
        uint256 oldWeight = _getWeight(account, chainId);
        uint256 oldSum = _getSum();
        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;
        pointsWeight[nomineeHash][nextTime].bias = 0;
        timeWeight[nomineeHash] = nextTime;
```
## Recommended Mitigation Steps
Delete unnecessary writes to reduce gas consumption
```solidity
        uint256 oldWeight = _getWeight(account, chainId);
        uint256 oldSum = _getSum();
        uint256 nextTime = (block.timestamp + WEEK) / WEEK * WEEK;
        pointsWeight[nomineeHash][nextTime].bias = 0;
-        timeWeight[nomineeHash] = nextTime;
```
# [L-6] Check the status of the paused variable before calling pause() and unpause()
## impact
When calling the pause and unpause functions, the status of the paused variable was not checked
github:[https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L352](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L352)
```solidity
    /// @dev Pause the contract.
    function pause() external {
        // Check for the contract ownership
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }

        paused = 2;
        emit TargetDispenserPaused();
    }

    /// @dev Unpause the contract
    function unpause() external {
        // Check for the contract ownership
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }

        paused = 1;
        emit TargetDispenserUnpaused();
    }
```
This may result in events with no change in status being emitted
## Recommended Mitigation Steps
```solidity
    /// @dev Pause the contract.
    function pause() external {
        // Check for the contract ownership
+        require(paused ==1);
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }

        paused = 2;
        emit TargetDispenserPaused();
    }

    /// @dev Unpause the contract
    function unpause() external {
        // Check for the contract ownership
+        require(paused ==2);
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }

        paused = 1;
        emit TargetDispenserUnpaused();
    }
```

# [L-7] Important state change triggering events include parameters before and after the change
## impact
For changes to some important parameters, it is recommended to use new and old values to trigger events, rather than just including new values
For example, the transfer of the owner variable.
```solidity
    function changeOwner(address newOwner) external {
        // Check for the ownership
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
## Recommended Mitigation Steps
```solidity
    function changeOwner(address newOwner) external {
        address oldOwner = owner;
        // Check for the ownership
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }

        // Check for the zero address
        if (newOwner == address(0)) {
            revert ZeroAddress();
        }

        owner = newOwner;
        emit OwnerUpdated(oldOwner, newOwner);
    }
```

# [L-8]Suggest using a two-step approach to change key variables
## impact
For changes to the contract owner, it is recommended to use pending accept two-step transfer to ensure that in case of spelling errors or other situations, the owner is transferred to an uncontrolled address, thereby losing control of the protocol
github:[https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/contracts/staking/StakingFactory.sol#L110](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/contracts/staking/StakingFactory.sol#L110)
```solidity
    function changeOwner(address newOwner) external {
        // Check for the ownership
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
## Recommended Mitigation Steps
```solidity
    function setPendingOwner(address newOwner) external {
        // Check for the ownership
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }
        pendingOwner = newOwner;
    }

   function acceptOwner() external {
        // Check for the ownership
        if (msg.sender != pendingOwner) {
            revert();
        }
        owner = pendingOwner;
    }
```


