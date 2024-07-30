## Title: Wrong parameter used in validateAndUpdateVaultStakeInDSS function

## **Relevant GitHub Links:**

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L111

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L146

## **Vulnerability Details:**

The [validateAndUpdateVaultStakeInDSS](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L111) function passes msg.sender as data for the callHookIfInterfaceImplemented function; however, it should be the operator. Since anyone can call the [finalizeUpdateVaultStakeInDSS](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L146) function, which in turn calls validateAndUpdateVaultStakeInDSS, there is a possibility that msg.sender will not be the operator.

```solidity
    function validateAndUpdateVaultStakeInDSS(CoreLib.Storage storage self, QueuedStakeUpdate memory queuedStakeUpdate)
        external
    {
        ...
        IDSS dss = queuedStakeUpdate.updateRequest.dss;
        HookLib.callHookIfInterfaceImplemented({
            dss: dss,
            data: abi.encodeWithSelector(dss.finishUpdateStakeHook.selector, msg.sender),
            ...
        });
    }
```

This issue was pointed out in the previous Code4rena audit [here](https://code4rena.com/reports/2024-06-karak-pro-league#h-03-validateandupdatevaultstakeindss-forwards-incorrect-operator-address-to-finishupdatestakehook) and marked as fixed; however, the issue remains unresolved.

## **Impact:**

Since the msg.sender may not be the operator, the DSS contract will record the incorrect address which can lead to discrepancies and further problems depending on the functionality.

## **Recommendation**

Modify the function to use the operator address instead. This ensures that regardless of who calls the function, the operator address will be used.

```solidity
    function validateAndUpdateVaultStakeInDSS(CoreLib.Storage storage self, QueuedStakeUpdate memory queuedStakeUpdate)
        external
    {
        ...
        IDSS dss = queuedStakeUpdate.updateRequest.dss;
        HookLib.callHookIfInterfaceImplemented({
            dss: dss,
            data: abi.encodeWithSelector(dss.finishUpdateStakeHook.selector, queuedStakeUpdate.operator), //change here
            ...
        });
    }
```

## Title: Handling Slashing for Tokens that Revert on Transfer to Zero Address

## **Relevant GitHub Links:**

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L52

## **Vulnerability Details:**

The protocol documentation states that all possible ERC20 tokens can be used within the protocol ([here](https://code4rena.com/audits/2024-07-karak-restaking#top:~:text=ERC20%20used%20by%20the%20protocol)), including tokens that revert on transfer to the zero address ([here](https://code4rena.com/audits/2024-07-karak-restaking#top:~:text=Revert%20on%20transfer%20to%20the%20zero%20address)). However, the current system does not support such tokens. The [handleSlashing](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L52) function in the SlashingHandler contract slashes the specified amount of the token by sending it to the zero address, as shown below. Therefore, these tokens will always revert when a slashing is being finalized.

```solidity
    function handleSlashing(IERC20 token, uint256 amount) external {
        if (amount == 0) revert ZeroAmount();
        if (!_config().supportedAssets[token]) revert UnsupportedAsset();

        SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount);
        // Below is where custom logic for each asset lives
        SafeTransferLib.safeTransfer(address(token), address(0), amount);
    }
```

## **Impact:**

DSS requests to slash operator vaults will always revert when such tokens are being used. Additionally, since requests can be made for multiple vaults at once, this revert will cause all slashes in that request to fail. This vulnerability could be exploited by operators who know they cannot be slashed.

## **Recommendation:**

Modify the function to allow slashing such tokens, such as transferring to a different designated empty address that does not revert.

## Title: validateExpiredSnapshot can be called before expiry

## **Relevant GitHub Links:**

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L210

## **Vulnerability Details:**

The [validateExpiredSnapshot](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L210) function allows anyone to start a new snapshot if the last snapshot has expired. This is to ensure that users' nodes are updated if the user does not start a snapshot for their NativeNode for over the expiry time. The problem is that the validateExpiredSnapshot function can be called even if a snapshot isn’t expired, since new nodes will have default values. The node.lastSnapshotTimestamp + Constants.SNAPSHOT_EXPIRY > block.timestamp check will always pass.

```solidity
    function validateExpiredSnapshot(address nodeOwner)
        external
        nonReentrant
        nodeExists(nodeOwner)
        whenFunctionNotPaused(Constants.PAUSE_NATIVEVAULT_VALIDATE_EXPIRED_SNAPSHOT)
    {
        NativeVaultLib.NativeNode storage node = _state().ownerToNode[nodeOwner];
        // Only check if the last snapshot has expired or not
        if (node.lastSnapshotTimestamp + Constants.SNAPSHOT_EXPIRY > block.timestamp) {
            revert SnapshotNotExpired();
        }

        _startSnapshot(node, false, nodeOwner);
    }
```

## **Impact:**

Users can start another user's snapshot prematurely, forcing the user to finish the snapshot before starting a new one. This also contradicts the function's intended purpose and description.

## **Recommendation:**

Add an additional check to ensure that the `validateExpiredSnapshot` function only triggers when the snapshot has truly expired.

## Title: Incorrect Rounding Direction in Mint Function

## **Relevant GitHub Links:**

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L110

## **Vulnerability Details:**

The [mint](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L110) function in the vault calls super.mint in the ERC4626 contract, which in turn calls previewMint. This function determines the number of assets a user needs to provide for the specified shares. The issue is that the function rounds down in favour of the user when it should be rounding up, contrary to what is specified in the [EIP-4626](https://eips.ethereum.org/EIPS/eip-4626#security-considerations:~:text=If%20(1)%20it%E2%80%99s%20calculating%20the%20amount%20of%20shares%20a%20user%20has%20to%20supply%20to%20receive%20a%20given%20amount%20of%20the%20underlying%20tokens%20or%20(2)%20it%E2%80%99s%20calculating%20the%20amount%20of%20underlying%20tokens%20a%20user%20has%20to%20provide%20to%20receive%20a%20certain%20amount%20of%20shares%2C%20it%20should%20round%20up.).

```solidity
    function previewMint(uint256 shares) public view virtual returns (uint256 assets) {
        if (!_useVirtualShares()) {
            uint256 supply = totalSupply();
            return supply == 0
                ? _initialConvertToAssets(shares)
                : FixedPointMathLib.fullMulDivUp(shares, totalAssets(), supply);
        }
        uint256 o = _decimalsOffset();
        if (o == 0) {
            return FixedPointMathLib.fullMulDivUp(shares, totalAssets() + 1, _inc(totalSupply()));
        }
        return FixedPointMathLib.fullMulDivUp(shares, totalAssets() + 1, totalSupply() + 10 ** o);
    }
```

## **Impact:**

Rounding down instead of up results in users providing fewer assets for a given number of shares, which can lead to potential losses for other users. This also deviates from the EIP-4626 standard.

## **Recommendation:**

Modify the mint function to ensure it rounds up, as specified in the EIP-4626 standard, to accurately calculate the number of assets a user must provide for the desired shares.

## Title: Potential Revert in finalizeSlashing Function Due to Paused Vaults

## **Relevant GitHub Links:**

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L299

## **Vulnerability Details:**

The [slashAssets](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L299) function in the NativeVault contract can be paused by the Owner, which is the core contract. A potential issue arises when slash requests involve multiple vaults. If one of the vaults is paused, it will cause the entire transaction to revert, blocking the execution of slashes for all vaults involved in the request.

```solidity
    function slashAssets(uint256 totalAssetsToSlash, address slashingHandler)
        external
        onlyOwner
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_NATIVEVAULT_SLASHER)
        returns (uint256 transferAmount)
    {
        NativeVaultLib.Storage storage self = _state();

        if (slashingHandler != self.slashStore) revert NotSlashStore();

        // avoid negative totalAssets if slashing amount is greater than totalAssets
        if (totalAssetsToSlash > self.totalAssets) {
            totalAssetsToSlash = self.totalAssets;
        }

        self.totalAssets -= totalAssetsToSlash;
        emit Slashed(totalAssetsToSlash);
        return totalAssetsToSlash;
    }
```

## **Impact:**

If a slash request includes multiple vaults and one of those vaults is paused, the entire request will revert. This will prevent the execution of legitimate slashes on other vaults.

## **Recommendation:**

To mitigate this issue, consider using a `try/catch` block around the slashing logic. This would allow the contract to handle any errors that occur when attempting to slash a paused vault without reverting the entire transaction.