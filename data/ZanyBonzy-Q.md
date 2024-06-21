## 1. SafeTransferLib.sol doesn't mask addresses for `safeTransfer` and `safeTransferFrom`

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/contracts/utils/SafeTransferLib.sol#L32-L35

### Impact
The lack of address masking in `safeTransfer` and `safeTransferFrom` in SafeTransferLib.sol will cause that tokens like FRAX, UNI, CRV, COMP cannot be used as stakingToken as their transfer will fail, dossing deposits as there will be no way to deposit rewards for users to claim.

When StakingToken.sol is initialized with any of these tokens, every attempt to deposit funds as rewards for staking will fail, hence there willl be no rewards available to claim. This is bad as the protocol aims to work with all sorts of ERC20 tokens.  

```solidity
    /// @dev Deposits funds for staking.
    /// @param amount Token amount to deposit.
    function deposit(uint256 amount) external {
        // Add to the contract and available rewards balances
        uint256 newBalance = balance + amount;
        uint256 newAvailableRewards = availableRewards + amount;

        // Record the new actual balance and available rewards
        balance = newBalance;
        availableRewards = newAvailableRewards;

        // Add to the overall balance
        SafeTransferLib.safeTransferFrom(stakingToken, msg.sender, address(this), amount);

        emit Deposit(msg.sender, amount, newBalance, newAvailableRewards);
    }
```

### Recommended Mitigation Steps
Consider masking input addresses in the transfer functions as is done in solmate's [implementation](https://github.com/transmissions11/solmate/blob/4b47a19038b798b4a33d9749d25e570443520647/src/utils/SafeTransferLib.sol).
 
***
 
## 2. Potential mismatch between nominees in VoteWeighting.sol and in Dispenser.sol

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L314

### Impact

The `_addNominee` function allows for adding nominees to the list along with the chain id. The function also checks if the dispenser address is available and if it is,proceeds to add the same nominee to the dispenser. If it's not available, the function still goes through. This can lead to a potential mismatch in the nominees list in the VoteWeighting contract and the Dispenser contract. This can also lead to potential inability to access rewards through a nominee as any nominee added by users this time will not have its `mapLastClaimedStakingEpochs` updated to the last epoch counter. Important to note that the same nominees once added/removed cannot be readded so those nominees are locked out of getting rewards.

On the ironic sides of things, if a nominee gets added when there's no dispenser, then gets removed when there is one, the `mapRemovedNomineeEpochs` mapping is [updated](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L779) for such nominee. The same goes for the other way round if a nominee is added when there's a dispenser and [removed](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L631) when there's no dispenser.

```solidity
        if (localDispenser != address(0)) {
            IDispenser(localDispenser).addNominee(nomineeHash);
        }
```

### Recommended Mitigation Steps

Consider reevaluating if this is intended behaviour, or just reverting if there's no local dispenser address.
 
***
 
## 3. A paused Dispenser or Treasury could temporarily DOS nominee addition

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L742

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L315

### Impact

Piggybacking off of the previous issue, if its intended that nominees in VoteWeighting.sol doesn't have to match those in Dispenser.sol (tbh, I don't see any reason) or just in general, then there's no need for `addNominee` to revert when the treasury or dispenser is paused. Considering also that the dispenser's default state upon deployment is [paused](https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L327), users stand the risk of being temporarily unable to add nominees for as long as either the dispenser or treasury is paused.

### Recommended Mitigation Steps

A potential fix is to query the paused status of the dispenser and treasury and skipping adding nominee to dispenser or just removing the paused status check in the `addNominee` function in Dispenser.sol
 
***
 
## 4. Change in dispenser could disrupt a number of protocol calculations and operations.

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L392


### Impact

The owner has the ability to call the `changeDispenser` function to set a new dispenser address. Doing this however risk breaking a lot of voteweighting and dispenser related functionalities. 

```solidity
    function changeDispenser(address newDispenser) external {
        // Check for the contract ownership
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }

        dispenser = newDispenser;
        emit DispenserUpdated(newDispenser);
    }
```

Changing the dispenser will lead to a mismatch in the nominees in the vote weighting contract, the old dispenser and the new dispenser. A newly deployed dispenser's default state is paused, hence adding and removing nominees will be temporarily dossed. Rewards for previously added nominees will no longer be avaliable in the new dispenser as their `mapLastClaimedStakingEpochs` has not been written. As nominees cannot be readded, there will be no way for the old nominees of the old dispenser to make claims in the new dispenser.

### Recommended Mitigation Steps

Changing dispenser should be done very carefully, with consideration for the nominees, before porting. 
 
***
 
## 5. Blocklisting tokens can leave rewards locked and unclaimable if multisig gets blocklisted

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/contracts/staking/StakingBase.sol#L508

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/registries/contracts/staking/StakingToken.sol#L108

### Impact

The `_claim` function allows the owner to claim his tokens. The reward tokens are transferred to the services's multisig. SInce all sorts of ERC20 tokens are to be used, tokens that offer a blocklist can be used and will render the rewards unclaimable if the multisig is ever changed. 
```solidity
    function _claim(uint256 serviceId, bool execCheckPoint) internal returns (uint256 reward) {
        ServiceInfo storage sInfo = mapServiceInfo[serviceId];
        // Check for the service ownership
        if (msg.sender != sInfo.owner) {
            revert OwnerOnly(msg.sender, sInfo.owner);
        }

        // Call the checkpoint, if required
        if (execCheckPoint) {
            checkpoint();
        }

        // Get the claimed service data
        reward = sInfo.reward;

        // Check for the zero reward
        if (reward == 0) {
            revert ZeroValue();
        }

        // Zero the reward field
        sInfo.reward = 0;

        // Transfer accumulated rewards to the service multisig
        // Note that the reentrancy is not possible since the reward is set to zero
        address multisig = sInfo.multisig;
        _withdraw(multisig, reward);

        emit RewardClaimed(epochCounter, serviceId, msg.sender, multisig, sInfo.nonces, reward);
    }
```
### Recommended Mitigation Steps
Consider introducing a way for owners to change multisig addresses in their service struct.

***
 
## 6. Functions to claim rewards should not be pausable

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L803

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L985

### Impact

In real time scenarios, the functionality to pause claims can serve as a double edge sword and can be maliciously used to stop users from being able to claim their rewards. Hence a general rule of thumb to ensure safety is to make sure functions that help get tokens out of a protocol i.e claim, withdraw, redeem etc are not pausable.

```solidity
    function claimOwnerIncentives(
        uint256[] memory unitTypes,
        uint256[] memory unitIds
    ) external returns (uint256 reward, uint256 topUp) {
    ......
        // Check for the paused state
        Pause currentPause = paused;
        if (currentPause == Pause.DevIncentivesPaused || currentPause == Pause.AllPaused ||
            ITreasury(treasury).paused() == 2) {
            revert Paused();
        }
        ......
    }
```

```solidity
    function claimStakingIncentives(
        uint256 numClaimedEpochs,
        uint256 chainId,
        bytes32 stakingTarget,
        bytes memory bridgePayload
    ) external payable {
    .....

        // Check for the paused state
        Pause currentPause = paused;
        if (currentPause == Pause.StakingIncentivesPaused || currentPause == Pause.AllPaused ||
            ITreasury(treasury).paused() == 2) {
            revert Paused();
        }
    }
```


***
 
## 7. `syncWithheldAmount` doesn't normalize the amount to sync based on bridging decimals

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L1174

### Impact

`syncWithheldAmount` is missing the downsizing of staking incentive to the specified number of bridging decimals which can lead to potential overinflation of the witheld amount for the chainid to be synced.

### Recommended Mitigation Steps

Introduce the normalizer check for bridging decimals.
```solidity
        if (bridgingDecimals < 18) {
            uint256 normalizedAmount = amount / (10 ** (18 - bridgingDecimals));
            normalizedAmount *= 10 ** (18 - bridgingDecimals);
            // Downsize staking incentive to a specified number of bridging decimals
            amount = normalizedAmount;
        }
```
 
***
 
## 8. Protocol is currently missing the `sendMessageNonEVM` and `sendMessageBatchNonEVM` method, so sending message to non evm chains will be impossible.

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L429

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L494

### Impact

If chainid is > MAX_EVM_CHAIN_ID, when distributing staking incentives, the `sendMessageNonEVM` function is queried. This function currently doesn't exist in any of the protocol's contracts (except for a few interfaces). 

```solidity
        if (chainId <= MAX_EVM_CHAIN_ID) {
            address stakingTargetEVM = address(uint160(uint256(stakingTarget)));
            IDepositProcessor(depositProcessor).sendMessage{value:msg.value}(stakingTargetEVM, stakingIncentive,
                bridgePayload, transferAmount);
        } else {
            // Send to non-EVM
            IDepositProcessor(depositProcessor).sendMessageNonEVM{value:msg.value}(stakingTarget,
                stakingIncentive, bridgePayload, transferAmount);
        }
```

***
 
## 9. Inconsistencies in solana's network's chain id might cause issues with specialized evm and non evm functions.

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L27

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L35

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L429

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L494

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L356

### Impact

The contracts are to be deployed on mostly evm chains with solana being the only non-evm chain to be deployed on. The issue is that while trying to get solana's chain id has been tough, different sources range from as low 101, 102, [900](https://docs.expand.network/important-ids/chain-id) to [1399811149](https://www.covalenthq.com/docs/networks/solana/). Even neon, the EVM wrapper for solana has a chain id of [245022926](https://www.alchemy.com/overviews/solana-evm). All of these are less than `MAX_EVM_CHAIN_ID`.

The point is that when certain functions are called, functions like `_distributeStakingIncentives`, `addNomineeNonEVM`, the expected behaviour will not be fulfilled.
Calling `addNomineeNonEVM` for instance while passing in solana chain id will revert since the known chain ids are lesser than `MAX_EVM_CHAIN_ID` while not being EVM chains.

```solidity
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
The same can be observed in the `_distributeStakingIncentives` and `_distributeStakingIncentivesBatch` functions in which the `sendMessage` function will be directly queried if solana's chainid is entered into the function. This might have unexpected consequencies depending on the integration of the `sendMessageNonEVM` function.

```solidity
    function _distributeStakingIncentives(
        uint256 chainId,
        bytes32 stakingTarget,
        uint256 stakingIncentive,
        bytes memory bridgePayload,
        uint256 transferAmount
    ) internal {
        // Get the deposit processor contract address
        address depositProcessor = mapChainIdDepositProcessors[chainId];

        // Transfer corresponding OLAS amounts to the deposit processor
        if (transferAmount > 0) {
            IToken(olas).transfer(depositProcessor, transferAmount);
        }

        if (chainId <= MAX_EVM_CHAIN_ID) {
            address stakingTargetEVM = address(uint160(uint256(stakingTarget)));
            IDepositProcessor(depositProcessor).sendMessage{value:msg.value}(stakingTargetEVM, stakingIncentive,
                bridgePayload, transferAmount);
        } else {
            // Send to non-EVM
            IDepositProcessor(depositProcessor).sendMessageNonEVM{value:msg.value}(stakingTarget,
                stakingIncentive, bridgePayload, transferAmount);
        }
```

 
***
 
## 10. `sendMessage` and `sendMessageBatch` in EthereumDepositProcessor.sol should probabaly be marked payable

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L425

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L490

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L130

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/staking/EthereumDepositProcessor.sol#L155

### Impact

When `_distributeStakingIncentives` is called it calls `sendMessage` function passing in msg.value sent to the depositProcessor. 
```solidity
    function _distributeStakingIncentives(
        uint256 chainId,
        bytes32 stakingTarget,
        uint256 stakingIncentive,
        bytes memory bridgePayload,
        uint256 transferAmount
    ) internal {
    .....

        if (chainId <= MAX_EVM_CHAIN_ID) {
            address stakingTargetEVM = address(uint160(uint256(stakingTarget)));
            IDepositProcessor(depositProcessor).sendMessage{value:msg.value}(stakingTargetEVM, stakingIncentive,
                bridgePayload, transferAmount);
        } else {
    .....
    }
```

If it is `EthereumDepositProcessor`, the call is likely to fail if ETH is also sent as the `sendMessage` function in the contract is not marked payable, nor does the contract have any payable `receive` or `fallback` function.
```solidity
    function sendMessage(
        address target,
        uint256 stakingIncentive,
        bytes memory,
        uint256
    ) external {
        // Check for the dispenser contract to be the msg.sender
        if (msg.sender != dispenser) {
            revert ManagerOnly(dispenser, msg.sender);
        }
    .....
}
```

The same can be observed in `sendMessageBatch` which is called in `_distributeStakingIncentivesBatch` function.

### Recommended Mitigation Steps

Consider marking these functions payable.

***
 
## 11. Non inclusive comparison for MIN_PARAM_VALUE and `_devsPerCapital`/`_codePerDev` params

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Tokenomics.sol#L485

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Tokenomics.sol#L499

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Tokenomics.sol#L652

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Tokenomics.sol#L660

### Impact

When changing `_devsPerCapital` and `_codePerDev`, a comparison is made against the `MIN_PARAM_VALUE`. The comparison is done using a strict greater than check, which means that attempts to set these values to the `MIN_PARAM_VALUE` will be ignored. 

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
        if (uint72(_devsPerCapital) > MIN_PARAM_VALUE) {
            devsPerCapital = uint72(_devsPerCapital);
        } else {
            // This is done in order not to pass incorrect parameters into the event
            _devsPerCapital = devsPerCapital;
        }

        // devsPerCapital is the part of the IDF calculation and thus its change will be accounted for in the next epoch
        if (uint72(_codePerDev) > MIN_PARAM_VALUE) {
            codePerDev = uint72(_codePerDev);
        } else {
            // This is done in order not to pass incorrect parameters into the event
            _codePerDev = codePerDev;
        }
```

### Recommended Mitigation Steps
Consider using >= operator instead.
 
***
 
## 12. Redundant check for success/failure during migration in DefaultTargetDispenserL2.sol

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L444

### Impact

During migration, olas tokens are migrated to the the new L2TargetDispenser. This is done by calling the `transfer` function on OLAS contract, which by all metrics is a standard token. It returns true on success and reverts on failure. So there's no need to check for returned value making the check redundant.

```solidity
        if (amount > 0) {
            bool success = IToken(olas).transfer(newL2TargetDispenser, amount);
            if (!success) {
                revert TransferFailed(olas, address(this), newL2TargetDispenser, amount);
            }
        }
```

### Recommended Mitigation Steps

Consider removing the check.
 
***
 
## 13. Consider calling the `drain` function during migration

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L412

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/staking/DefaultTargetDispenserL2.sol#L377

### Impact

If `migrate` is called before calling the `drain` function, there will be no way to drain excess native funds from the contact as the `migrate` function deletes the owner making the contract ownerless and by extension the `drain` function inaccessible.

The `migrate` function zeros out the owner address.
```solidity
    function migrate(address newL2TargetDispenser) external {
    .....
        // Zero the owner
        owner = address(0);

        emit Migrated(msg.sender, newL2TargetDispenser, amount);

        // _locked is now set to 2 for good
    }
```

And the drain function is only callable by the owner.
```solidity
    function drain() external returns (uint256 amount) {
.....
        // Check for the owner address
        if (msg.sender != owner) {
            revert OwnerOnly(msg.sender, owner);
        }

.....

        emit Drain(msg.sender, amount);

        _locked = 1;
    }
```

### Recommended Mitigation Steps

A good improvement to cover for an admin error like this is to include the `drain` function in the `migrate` function. This might involve refactoring the `drain` function to return rather than revert if the contract has no eth. This will help prevent unnecessary fund loss.
For instance:

```solidity
    function migrate(address newL2TargetDispenser) external {
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

+       drain();
        // Get OLAS token amount
        uint256 amount = IToken(olas).balanceOf(address(this));
        // Transfer amount to the new L2 target dispenser
        if (amount > 0) {
            bool success = IToken(olas).transfer(newL2TargetDispenser, amount);
            if (!success) {
                revert TransferFailed(olas, address(this), newL2TargetDispenser, amount);
            }
        }

        // Zero the owner
        owner = address(0);

        emit Migrated(msg.sender, newL2TargetDispenser, amount);

        // _locked is now set to 2 for good
    }
```
***
 
## 14. `_sendMessage` in GnosisDepositProcessorL1.sol can better improved to check for MessageDelivery.sol's lower gas limit

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/staking/GnosisDepositProcessorL1.sol#L80

### Impact

`_sendMessage` in GnosisDepositProcessorL1.sol sends message to L2. It decodes the bridgePayload to check the `gaslimitMessage` and reverts if its equal to zero. This check can be better improved by ensuring that gaslimit >= 100 instead. This is because the required minimum gas when interacting with message delivery on gnosis is [100](https://github.com/omni/tokenbridge-contracts/blob/908a48107919d4ab127f9af07d44d47eac91547e/contracts/upgradeable_contracts/arbitrary_message/MessageDelivery.sol#L14)

```solidity
        } else {
            // Assemble AMB data payload
            bytes memory data = abi.encodeWithSelector(RECEIVE_MESSAGE, abi.encode(targets, stakingIncentives));

            // In the current configuration, maxGasPerTx is set to 4000000 on Ethereum and 2000000 on Gnosis Chain.
            // Source: https://docs.gnosischain.com/bridges/Token%20Bridge/amb-bridge#how-to-check-if-amb-is-down-not-relaying-message
            uint256 gasLimitMessage = abi.decode(bridgePayload, (uint256));

            // Check for zero value
            if (gasLimitMessage == 0) {
                revert ZeroValue();
            }

            // Check for the max gas limit
            if (gasLimitMessage > MESSAGE_GAS_LIMIT) {
                revert Overflow(gasLimitMessage, MESSAGE_GAS_LIMIT);
            }

            // Send message to L2
            bytes32 iMsg = IBridge(l1MessageRelayer).requireToPassMessage(l2TargetDispenser, data, gasLimitMessage);

            sequence = uint256(iMsg);
        }
```
The way the function is structured is that when the `requireToPassMessage` function is called in MessageDelivery.sol. 

```solidity
    function requireToPassMessage(address _contract, bytes memory _data, uint256 _gas) public returns (bytes32) {
        return _sendMessage(_contract, _data, _gas, SEND_TO_ORACLE_DRIVEN_LANE);
    }
```

```solidity
    function _sendMessage(address _contract, bytes memory _data, uint256 _gas, uint256 _dataType)
        internal
        returns (bytes32)
    {
        // it is not allowed to pass messages while other messages are processed
        // if other is not explicitly configured
        require(messageId() == bytes32(0) || allowReentrantRequests());
        require(_gas >= MIN_GAS_PER_CALL && _gas <= maxGasPerTx());
    .....
    }
```

As can be seen from above, gaslimit is not >= 100, the whole function reverts. This check can be introduced much earlier rather than only checking for 0.

### Recommended Mitigation Steps
Consider refactoring the check 

```solidity
            if (gasLimitMessage < 100) {
                revert ZeroValue();
            }
```
 
***
 
## 15. `voteForNomineeWeights` should call `_getSum` function.

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L473


### Impact

`voteForNomineeWeights` is missing a call to `_getSum` which causes that the sum of nominee weights is not updated after voting. This doesn't entirely match curve's implementation as intended which calls `_get_total` (which in itself updates the sum of nominee weights.)

### Recommended Mitigation Steps

Call the `_getSum` function after adding slope changes

```solidity
        changesWeight[nomineeHash][newSlope.end] += newSlope.slope;
        changesSum[newSlope.end] += newSlope.slope;

        _getSum();

        voteUserSlopes[msg.sender][nomineeHash] = newSlope;

        // Record last action time
        lastUserVote[msg.sender][nomineeHash] = block.timestamp;
```

***


## 16. Removing a nominee will result in a user's votes being unusable for up to 10 days 

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L586

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L148

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/governance/contracts/VoteWeighting.sol#L502

### Impact

Within the VoteWeighting contracts, users can vote for the nominee of their choice. They allocate a power of their voting power. In order to not spam-change their votes, users have a `WEIGHT_VOTE_DELAY` of 10 days. Meaning they cannot change their vote towards a nominee for 10 days. Now, when the owner calls the `removeNominee` functio, allowing him to remove a nominee. The users can can remove their votes from said nominee. However, in order to do so, the user has to call `voteForNomineeWeights`, but will not be able to do so due to the check for `nextAllowedVotingTime > block.timestamp)`, the call will revert causing the user to notm be able to vote for about 10 days.

***

# 17. `retainerHash` should not be encoded in constructor in case of a hard fork

Links to affected code *

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L281

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L346

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L757

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L1138

https://github.com/code-423n4/2024-05-olas/blob/3ce502ec8b475885b90668e617f3983cea3ae29f/tokenomics/contracts/Dispenser.sol#L1154

### Impact

The protocol aims to deploy on multiple different chains, so the risk and possibility of a hardfork is higher. Under normal circumstances, this should not be much of a problem, but Dispenser.sol constructs the `retainerHash` in the constructor using the chainId. When a hard fork occurs, the new chain id will be different from the one used to previously construct the `retainerHash`, which will lead to the `retainerHash` now being incorrect and can cause issues with the `retain` function.

As can be seen from the contract, the `retainerHash` is declared and set as immutable.

```solidity
    // Retainer hash of a Nominee struct composed of retainer address with block.chainid
    bytes32 public immutable retainerHash;
```

And in the constructor, the `retainer` address is declared, and the `retainerHash` is calculated by encoding the retainer and the current chain id.
```solidity
    constructor(
        address _olas,
        address _tokenomics,
        address _treasury,
        address _voteWeighting,
        bytes32 _retainer,
        uint256 _maxNumClaimingEpochs,
        uint256 _maxNumStakingTargets
    ) {
    .....
        retainer = _retainer;
        retainerHash = keccak256(abi.encode(IVoteWeighting.Nominee(retainer, block.chainid)));
    .....
    }
```

### Recommended Mitigation Steps

Do not declare retainerHash in the constructor.