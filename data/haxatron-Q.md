### 1. Enforce a minimum gas limit for cross-chain messages

Sending cross-chain staking data and tokens are permissionless and anyone can execute them. However, the contracts do not enforce a minimum gas limit for the cross-chain transactions, only a maximum. Therefore user can set a low gas limit to fail the cross-chain transaction.

Although the cross-chain messages are replayable for all of the bridges, recommended to have enforce minimum gas limit to remove the hassle of the DAO having to replay them.

### 2. Migrating the `DefaultDepositProcessorL2` can cause undrained native token to be locked.

Calling `migrate()` before `drain()` will cause `locked = 2` and the undrained native token cannot be drained.

```solidity
    /// @dev Drains contract native funds.
    /// @notice For cross-bridge leftovers and incorrectly sent funds.
    /// @return amount Drained amount to the owner address.
    function drain() external returns (uint256 amount) {
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
        }

        emit Drain(msg.sender, amount);

        _locked = 1;
    }

    /// @dev Migrates funds to a new specified L2 target dispenser contract address.
    /// @notice The contract must be paused to prevent other interactions.
    ///         The owner is be zeroed, the contract becomes paused and in the reentrancy state for good.
    ///         No further write interaction with the contract is going to be possible.
    ///         If the withheld amount is nonzero, it is regulated by the DAO directly on the L1 side.
    ///         If there are outstanding queued requests, they are processed by the DAO directly on the L2 side.
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

### 3. `syncWithheldAmount` needs to normalize according to bridging decimals.

The `withheldAmount` received from L2 -> L1 transaction can be unnormalized according to the bridging decimals. 

For example for the Wormhole bridge, if the `limitAmount` for a target on the L2 is not a multiple of 1e10, the `withheldAmount` sent from L2 -> L1 will also not be multiple of 1e10, so it should be normalized here:
```
    function syncWithheldAmount(uint256 chainId, uint256 amount) external {
        address depositProcessor = mapChainIdDepositProcessors[chainId];

        // Check L1 deposit processor address
        if (msg.sender != depositProcessor) {
            revert DepositProcessorOnly(msg.sender, depositProcessor);
        }

        // The overall amount is bound by the OLAS projected maximum amount for years to come
        uint256 withheldAmount = mapChainIdWithheldAmounts[chainId] + amount;
        if (withheldAmount > type(uint96).max) {
            revert Overflow(withheldAmount, type(uint96).max);
        }

        // Update the withheld amount
        mapChainIdWithheldAmounts[chainId] = withheldAmount;

        emit WithheldAmountSynced(chainId, amount, withheldAmount);
    }
```

