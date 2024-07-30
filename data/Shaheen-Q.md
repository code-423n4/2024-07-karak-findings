| No | Issue |
| :-----: | :-----: |
| L-01 |  Setting `maxSlashablePercentageWad` to 1, will not set it to 0% slashing, it can be manipulative for the stakers |
| L-02 | `requestSlashing` & `finalizeSlashing`'s Hooks Calls ignore failures |
| L-03 | `NativeVault.finishWithdrawal` is susceptible to Cross-Reentrancy Attacks because it doesn't follow the CEI |
| L-04 | EIP-4788 Non-Compliant function call |
| L-05 | Access Control Missing (maybe) |

## Low Findings:

### L-01:  Setting `maxSlashablePercentageWad` to 1, will not set it to 0% slashing, it can be manipulative for the stakers
As per the dev comments of [`registerDSS()`](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L259), the protocol allows DSS to set max slashing percentage to 0% by setting it to 1. But in the later code it doesn't enforce anywhere that when 1 is inputted it means DSS allows 0% slashing. Which means a DSS can market itself as a 0% Slashing DSS but when he will get the chance, he will gonna slash users assets. Just a note, 1% of 100ETH is 1ETH, that's why it can be manipulative for the stakers

```solidity
    /// @dev If you want to have 0% slashing, set `maxSlashablePercentageWad` to `1` since 0 is used as a default flag for not set
    /// If a operator is set, it will have a non-zero slashable percentage. Hence we can just check for that to see if a DSS is registered
    /// @param maxSlashablePercentageWad The max slashable percent, in the form of wad, that the DSS can slash per cooldown period
    function registerDSS(uint256 maxSlashablePercentageWad) external {
        IDSS dss = IDSS(msg.sender);
        if (!address(dss).isSmartContract()) revert NotSmartContract();
        _self().setDSSMaxSlashablePercentageWad(dss, maxSlashablePercentageWad);
        emit DSSRegistered(msg.sender, maxSlashablePercentageWad);
    }
```

#### Mitigation
Enfore that when DSS passes 1 in the `registerDSS` function, it actually sets to 0%.

### L-02: `requestSlashing` & `finalizeSlashing`'s Hooks Calls ignore failures
Both `requestSlashing` & `finalizeSlashing`'s Hooks Calls `callHookIfInterfaceImplemented` ignore failures:
```solidity
        HookLib.callHookIfInterfaceImplemented({
            dss: dss,
            data: abi.encodeWithSelector(
                dss.requestSlashingHook.selector, slashingMetadata.operator, slashingMetadata.slashPercentagesWad
            ),
            interfaceId: dss.requestSlashingHook.selector,
            ignoreFailure: true, ///@audit-issue L here the failure shouldn't be ignored becaause this func will be invoked by the dss itself, maybe the call is imp for dss, if a call fails, he can simple call the slash again
            hookCallGasLimit: self.hookCallGasLimit,
            supportsInterfaceGasLimit: self.supportsInterfaceGasLimit,
            hookGasBuffer: self.hookGasBuffer
        });
```
The failure shouldn't be ignored here, becaause these functions (esp requestSlashing) will be invoked by the DSS itself, maybe the call is important for DSS, if a call fails, he can simple call the slash again.
#### Mitigation
```diff
        HookLib.callHookIfInterfaceImplemented({
            dss: dss,
            data: abi.encodeWithSelector(dss.finishSlashingHook.selector, queuedSlashing.operator),
            interfaceId: dss.finishSlashingHook.selector,
-           ignoreFailure: true,
+           ignoreFailure: false,
            hookCallGasLimit: self.hookCallGasLimit,
            supportsInterfaceGasLimit: self.supportsInterfaceGasLimit,
            hookGasBuffer: self.hookGasBuffer
        });
```

### L-03: `NativeVault.finishWithdrawal` is susceptible to Cross-Reentrancy Attacks because it doesn't follow the CEI
[NativeVault.finishWithdrawal](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L262) function transfers Ethers to the users (makes an external call) and then it updates `withdrawableCreditedNodeETH` and deletes the `withdrawalMap[key]`, which makes it susceptible to cross-reentrancy attacks, even tho the function and its surreounding functions are protected by a nonReentranct gaurd, caution should be paid to cross-contract reentrancy attacks and Checks-Effects-Interactions pattern should be followed:
```diff
    function finishWithdrawal(bytes32 withdrawalKey)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_NATIVEVAULT_FINISH_WITHDRAWAL)
    {
        NativeVaultLib.Storage storage self = _state();
        NativeVaultLib.QueuedWithdrawal memory startedWithdrawal = self.withdrawalMap[withdrawalKey];
        NativeVaultLib.NativeNode storage node = self.ownerToNode[startedWithdrawal.nodeOwner];

        if (startedWithdrawal.start == 0) revert WithdrawalNotFound();
        if (startedWithdrawal.start + Constants.MIN_WITHDRAWAL_DELAY > block.timestamp) {
            revert MinWithdrawDelayNotPassed();
        }
        self.nodeOwnerToWithdrawAmount[startedWithdrawal.nodeOwner] -= startedWithdrawal.assets;

        uint256 withdrawableAssets = withdrawableWei(startedWithdrawal.nodeOwner);
        ///@audit-ok maxRedeem check? - maybe not imp bcz this burn shares directly
        if (startedWithdrawal.assets > withdrawableAssets) {
            startedWithdrawal.assets = withdrawableAssets;
        }
        _decreaseBalance(startedWithdrawal.nodeOwner, startedWithdrawal.assets);
-      INativeNode(node.nodeAddress).withdraw(startedWithdrawal.to, startedWithdrawal.assets);
        node.withdrawableCreditedNodeETH -= startedWithdrawal.assets;
        delete self.withdrawalMap[withdrawalKey]; ///@audit-issue L CEI miss
+      INativeNode(node.nodeAddress).withdraw(startedWithdrawal.to, startedWithdrawal.assets);
        emit FinishedWithdraw(
            startedWithdrawal.nodeOwner,
            startedWithdrawal.to,
            _config().operator,
            startedWithdrawal.assets,
            withdrawalKey
        );
    }

```
### L-04: EIP-4788 Non-Compliant function call
The `EIP-4788` [requires](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-4788.md#beacon-roots-contract) that the `get` call to the `BEACON_ROOTS_ADDRESS` strictly should be `32 bytes`. EIP says "If the input is not exactly 32 bytes, the contract must revert".
The `abi.encode(timestamp)` returns `bytes` not the required `bytes32`.
```solidity
    function _getParentBlockRoot(uint64 timestamp) internal view returns (bytes32) {
        (bool success, bytes memory result) = Constants.BEACON_ROOTS_ADDRESS.staticcall(abi.encode(timestamp));
        if (success && result.length > 0) {
            return abi.decode(result, (bytes32));
        } else {
            revert BeaconRootFetchError();
        }
    }
```
#### Mitigation
```diff
-        (bool success, bytes memory result) = Constants.BEACON_ROOTS_ADDRESS.staticcall(abi.encode(timestamp));
+        (bool success, bytes memory result) = Constants.BEACON_ROOTS_ADDRESS.staticcall(bytes32(abi.encode(timestamp)));
```
### L-05: Access Control Missing or maybe a Wrong Dev Comment
The `finalizeUpdateVaultStakeInDSS` dev comments first mentions that `Allows anyone to finish` and then in the next line `Only operator can finish`, is this a critical vulnerability, where access control is needed or just a wrong dev comment!?
```solidity
    /// @notice Allows anyone to finish the queued request for an operator to update assets delegated to a DSS
    /// @dev Only operator can finish their queued request valid only after a
    /// minimum delay of `Constants.MIN_STAKE_UPDATE_DELAY` after starting the request
    function finalizeUpdateVaultStakeInDSS(Operator.QueuedStakeUpdate memory queuedStake)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_CORE_FINALIZE_STAKE_UPDATE)
    {
        _self().validateAndUpdateVaultStakeInDSS(queuedStake);
        emit FinishedStakeUpdate(queuedStake);
    }
```
#### Mitigation
Correct the misleading dev comment & If there is a access control modifier missing then pls reward Shaheen a million dollar. Thanks!  