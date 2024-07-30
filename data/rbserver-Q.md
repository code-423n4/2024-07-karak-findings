## QA REPORT

|      | Issue                                                                                                                                                                                                                                                                                       |
| ---- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [01] | Protocol can fail to support fee-on-transfer tokens                                                                                                                                                                                                                                         |
| [02] | Protocol can fail to support tokens that revert on transfer to zero address                                                                                                                                                                                                                 |
| [03] | Address collision for vault can be used for DOS or stealing stakers' deposits                                                                                                                                                                                                               |
| [04] | Allowing DSSes that do not implement `ERC165.supportsInterface` function to be registered is problematic                                                                                                                                                                                    |
| [05] | Allowing operator to stake its vault to multiple DSSes can cause some DSSes' slashings of such vault to fail                                                                                                                                                                                |
| [06] | First depositor attack is possible                                                                                                                                                                                                                                                          |
| [07] | `maxDeposit`, `maxMint`, `maxRedeem`, and `maxWithdraw` functions do not return 0 when they should                                                                                                                                                                                          |
| [08] | `deposit(uint256 assets, address to)`, `deposit(uint256 assets, address to, uint256 minSharesOut)`, `mint(uint256 shares, address to)`, and `startRedeem(uint256 shares, address beneficiary)` functions do not allow staker's approved spender to call these functions on behalf of staker |
| [09] | `previewWithdraw` and `previewRedeem` functions do not revert though they should                                                                                                                                                                                                            |
| [10] | Pausing `deposit(uint256 assets, address to, uint256 minSharesOut)` function causes stakers to lose slippage protection when depositing into vault                                                                                                                                          |
| [11] | `Vault` contract's withdrawal functions can be paused while its deposit functions are unpaused                                                                                                                                                                                              |
| [12] | `Vault.mint` and `Vault.finishRedeem` functions do not have slippage controls                                                                                                                                                                                                               |
| [13] | Vault deployed for token that does not yet exist but will be deployed can cause stakers to lose their deposits                                                                                                                                                                              |
| [14] | DSS's slashing request can be canceled after `SLASHING_VETO_WINDOW` is passed                                                                                                                                                                                                               |
| [15] | Operator can collude with DSS for stealing stakers' deposits                                                                                                                                                                                                                                |

## [01] Protocol can fail to support fee-on-transfer tokens

### Description
According to https://github.com/code-423n4/2024-07-karak?tab=readme-ov-file#erc20-token-behaviors-in-scope, fee-on-transfer tokens are in scope and should be supported. Calling the following `slashAssets` function further calls the `handleSlashing` function below that executes `SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount)` and then `SafeTransferLib.safeTransfer(address(token), address(0), amount)`. When `token` is a fee-on-transfer token, after executing `SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount)`, the `SlashingHandler` contract's balance of such token would be less than `amount` because such token's transfer fee is deducted, which then causes the execution of `SafeTransferLib.safeTransfer(address(token), address(0), amount)` to revert. As a result, the DSS fails to slash the operator's vault when the vault's underlying token is a fee-on-transfer token, and the protocol fails to support such fee-on-transfer token.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L193-L205
```solidity
    function slashAssets(uint256 totalAssetsToSlash, address slashingHandler)
        external
        onlyCore
        returns (uint256 transferAmount)
    {
        transferAmount = Math.min(totalAssets(), totalAssetsToSlash);

        // Approve to the handler and then call the handler which will draw the funds
        SafeTransferLib.safeApproveWithRetry(asset(), slashingHandler, transferAmount);
        ISlashingHandler(slashingHandler).handleSlashing(IERC20(asset()), transferAmount);
        ...
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/SlashingHandler.sol#L52-L59
```solidity
    function handleSlashing(IERC20 token, uint256 amount) external {
        if (amount == 0) revert ZeroAmount();
        if (!_config().supportedAssets[token]) revert UnsupportedAsset();

        SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount);
        // Below is where custom logic for each asset lives
        SafeTransferLib.safeTransfer(address(token), address(0), amount);
    }
```

### Recommended Mitigation
The `handleSlashing` function can be updated to transfer the actual token amount received by the `SlashingHandler` contract through the `SafeTransferLib.safeTransferFrom` function call when calling the `SafeTransferLib.safeTransfer` function.

## [02] Protocol can fail to support tokens that revert on transfer to zero address

### Description
Based on https://github.com/code-423n4/2024-07-karak?tab=readme-ov-file#erc20-token-behaviors-in-scope, tokens that revert on transfer to the zero address are in scope and should be supported. When the following `slashAssets` function is called, the `handleSlashing` function below is further called. However, when `token` is a token that reverts on transfer to the zero address, calling the `handleSlashing` function reverts because executing `SafeTransferLib.safeTransfer(address(token), address(0), amount)` reverts. Thus, the DSS fails to slash the operator's vault when the vault's underlying token is a token that reverts on transfer to the zero address, and the protocol fails to support such token that reverts on transfer to the zero address.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L193-L205
```solidity
    function slashAssets(uint256 totalAssetsToSlash, address slashingHandler)
        external
        onlyCore
        returns (uint256 transferAmount)
    {
        transferAmount = Math.min(totalAssets(), totalAssetsToSlash);

        // Approve to the handler and then call the handler which will draw the funds
        SafeTransferLib.safeApproveWithRetry(asset(), slashingHandler, transferAmount);
        ISlashingHandler(slashingHandler).handleSlashing(IERC20(asset()), transferAmount);
        ...
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/SlashingHandler.sol#L52-L59
```solidity
    function handleSlashing(IERC20 token, uint256 amount) external {
        if (amount == 0) revert ZeroAmount();
        if (!_config().supportedAssets[token]) revert UnsupportedAsset();

        SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount);
        // Below is where custom logic for each asset lives
        SafeTransferLib.safeTransfer(address(token), address(0), amount);
    }
```

### Recommended Mitigation
The `handleSlashing` function can be updated to transfer the relevant token amount to a dead address, which is not `address(0)`, that is not controlled by anyone when calling the `SafeTransferLib.safeTransfer` function.

## [03] Address collision for vault can be used for DOS or stealing stakers' deposits

### Description
When the following `deployVaults` function is called, the `createVault` function below is also called. In the `createVault` function, `bytes32 salt = keccak256(abi.encodePacked(operator, depositToken, self.vaultNonce++))` is executed, and such salt is used to call the `cloneVault` function below to deploy the vault. When an operator's `createVault` transaction is in the mempool, `operator`, `depositToken`, and `self.vaultNonce` can be known, and a malicious actor can use these information to create the same salt and frontrun the operator's `createVault` transaction by calling the `LibClone.deployDeterministicERC1967BeaconProxy` function with such salt and the `Core` contract's address. Since the malicious actor's transaction deterministically deploys a contract to the address where the operator's vault intends to be deployed to, the operator's `createVault` transaction reverts after the frontrunning, which prevents `vault.initialize(address(this), operator, depositToken, name, symbol, extraData)` and `self.operatorState[operator].addVault(vault)` from being executed. Afterwards, the malicious actor can initialize his deployed contract to make himself the owner. If stakers believe that the malicious actor's deployed contract is the operator's vault due to that the deployed contract's address is the intended address of the operator's vault, they can deposit into such contract. However, the malicious actor, as the owner of his deployed contract, can call his deployed contract's `slashAssets` function to transfer the stakers' deposits out from such contract. As a result, the stakers lose their deposits.

Alternatively, a malicious operator can brute-force many values of  `self.vaultNonce` that are not yet used in the `Core` contract or `depositToken` and compute the potential vault addresses deterministically. Then, the operator can brute-force many values of salt to compute addresses of contracts, which can be controlled by him, based on `CREATE2`. When an address collision is found between the potential vault and the controlled contract after brute-forcing sufficient number of times, the operator can deploy the controlled contract to the collided address deterministically, call the controlled contract to allow the operator to spend all of such contract's `depositToken` balance, and call the controlled contract's `selfdestruct` function to destroy itself in which all of these operations of the operator are executed in one transaction. Afterwards, the operator deploys the vault using the `self.vaultNonce` corresponding to the collided address when such `self.vaultNonce` is reached in the `Core` contract if `self.vaultNonce` was brute-forced, which might require frontrunning if necessary, or the `depositToken` corresponding to the collided address if `depositToken` was brute-forced. After innocent stakers made enough deposits of the `depositToken` into the vault, the malicious operator can spend the vault's `depositToken` balance to transfer them to himself. Thus, the stakers also lose their deposits.

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/CoreLib.sol#L118-L147
```solidity
    function deployVaults(
        Storage storage self,
        address operator,
        address implementation,
        VaultLib.Config[] calldata vaultConfigs
    ) external returns (IKarakBaseVault[] memory) {
        validateVaultConfigs(self, vaultConfigs, implementation);
        IKarakBaseVault[] memory vaults = new IKarakBaseVault[](vaultConfigs.length);

        if (implementation == address(0)) {
            // Allows us to change all the standard vaults to a new implementation
            implementation = Constants.DEFAULT_VAULT_IMPLEMENTATION_FLAG;
        }

        for (uint256 i = 0; i < vaultConfigs.length; i++) {
            IKarakBaseVault vault = createVault(
                self,
                operator,
                vaultConfigs[i].asset,
                vaultConfigs[i].name,
                vaultConfigs[i].symbol,
                vaultConfigs[i].extraData,
                implementation
            );
            vaults[i] = vault;
            self.operatorState[operator].addVault(vault);
            emit DeployedVault(operator, address(vault), vaultConfigs[i].asset);
        }
        return vaults;
    }
```

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/CoreLib.sol#L89-L116
```solidity
    function createVault(
        Storage storage self,
        address operator,
        address depositToken,
        string memory name,
        string memory symbol,
        bytes memory extraData,
        address implementation
    ) internal returns (IKarakBaseVault) {
        // Use Create2 to determine the address before hand
        bytes32 salt = keccak256(abi.encodePacked(operator, depositToken, self.vaultNonce++));

        address expectedNewVaultAddr =
            LibClone.predictDeterministicAddressERC1967BeaconProxy(address(this), salt, address(this));

        self.vaultToImplMap[address(expectedNewVaultAddr)] = implementation;

        IKarakBaseVault vault = cloneVault(salt);
        vault.initialize(address(this), operator, depositToken, name, symbol, extraData);

        // Extra protection to ensure the vault was created with the correct address
        if (expectedNewVaultAddr != address(vault)) {
            revert VaultCreationFailedAddrMismatch(expectedNewVaultAddr, address(vault));
        }
        ...
    }
```

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/CoreLib.sol#L149-L151
```solidity
    function cloneVault(bytes32 salt) internal returns (IKarakBaseVault) {
        return IKarakBaseVault(address(LibClone.deployDeterministicERC1967BeaconProxy(address(this), salt)));
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L193-L205
```solidity
    function slashAssets(uint256 totalAssetsToSlash, address slashingHandler)
        external
        onlyCore
        returns (uint256 transferAmount)
    {
        transferAmount = Math.min(totalAssets(), totalAssetsToSlash);

        // Approve to the handler and then call the handler which will draw the funds
        SafeTransferLib.safeApproveWithRetry(asset(), slashingHandler, transferAmount);
        ISlashingHandler(slashingHandler).handleSlashing(IERC20(asset()), transferAmount);
        ...
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L322-L325
```solidity
    modifier onlyCore() {
        _checkOwner();
        _;
    }
```

### Recommended Mitigation
The `createVault` function can be updated to further encode `block.timestamp` and `block.number` in addition to `operator`, `depositToken`, and `self.vaultNonce` for computing the salt for deploying a vault.

## [04] Allowing DSSes that do not implement `ERC165.supportsInterface` function to be registered is problematic

### Description
https://github.com/code-423n4/2024-07-karak?tab=readme-ov-file#eip-compliance-checklist states that `DSS contract should comply with ERC-165`. Therefore, the protocol requires that each DSS is ERC-165 compliant.

According to https://eips.ethereum.org/EIPS/eip-165#how-a-contract-will-publish-the-interfaces-it-implements, `A contract that is compliant with ERC-165 shall implement the following interface`:

```solidity
interface ERC165 {
    /// @notice Query if a contract implements an interface
    /// @param interfaceID The interface identifier, as specified in ERC-165
    /// @dev Interface identification is specified in ERC-165. This function
    ///  uses less than 30,000 gas.
    /// @return `true` if the contract implements `interfaceID` and
    ///  `interfaceID` is not 0xffffffff, `false` otherwise
    function supportsInterface(bytes4 interfaceID) external view returns (bool);
}
```

Thus, each DSS needs to implement the `ERC165.supportsInterface` function to comply with ERC-165. However, calling the following `Core.registerDSS` function does not check if the DSS implements the `ERC165.supportsInterface` function or not so a DSS that does not implement the `ERC165.supportsInterface` function and hence is not ERC-165 compliant can also be registered.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Core.sol#L262-L267
```solidity
    function registerDSS(uint256 maxSlashablePercentageWad) external {
        IDSS dss = IDSS(msg.sender);
        if (!address(dss).isSmartContract()) revert NotSmartContract();
        _self().setDSSMaxSlashablePercentageWad(dss, maxSlashablePercentageWad);
        emit DSSRegistered(msg.sender, maxSlashablePercentageWad);
    }
```

For a DSS that does not implement the `ERC165.supportsInterface` function, calling the following `callHookIfInterfaceImplemented` function returns `false` without further calling the DSS's corresponding hook even if such DSS does implement such hook. In this situation, although the DSS expects the relevant hooks to be called, all of these hooks fail to be called; for instance, such DSS's `registrationHook` function cannot be called at all so it fails to only accept its chosen operators even though https://github.com/code-423n4/2024-07-karak?tab=readme-ov-file#dss-distributed-secure-services states that `DSS would be able to choose which operator it would like to accept`.

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/HookLib.sol#L78-L103
```solidity
    function callHookIfInterfaceImplemented(
        IERC165 dss,
        bytes memory data,
        bytes4 interfaceId,
        bool ignoreFailure,
        uint32 hookCallGasLimit,
        uint32 supportsInterfaceGasLimit,
        uint32 hookGasBuffer
    ) internal returns (bool) {
        if (gasleft() < (supportsInterfaceGasLimit * 64 / 63 + hookGasBuffer)) {
            revert NotEnoughGas();
        }

        (bool success, bytes32 result) = performLowLevelCallAndLimitReturnData(
            address(dss),
            abi.encodeWithSelector(IERC165.supportsInterface.selector, interfaceId),
            supportsInterfaceGasLimit
        );

        if (!success || result == bytes32(0)) {
            // Either call failed or interface isn't implemented
            emit InterfaceNotSupported();
            return false;
        }
        return callHook(address(dss), data, ignoreFailure, hookCallGasLimit, hookGasBuffer);
    }
```

### Recommended Mitigation
The `Core.registerDSS` function can be updated to only allow DSSes that do implement the `ERC165.supportsInterface` function and comply with ERC-165 to be registered.

## [05] Allowing operator to stake its vault to multiple DSSes can cause some DSSes' slashings of such vault to fail

### Description
The protocol allows an operator to overleverage by staking its vault to multiple DSSes. When a malicious behavior occurs, all of these DSSes can request to slash the operator's vault by calling the following `requestSlashing` function that further calls the `fetchEarmarkedStakes` function below. Because the sum of these DSSes' allowed slashing percentages can be more than 100%, the sum of the token amounts to be slashed by these DSSes calculated by the `fetchEarmarkedStakes` function can be more than the vault's underlying token balance. This incentivizes the DSSes to frontrun each other when finalizing the slashings. The DSSes, which successfully frontrun the others, can slash their allowed slashing percentages of the vault's underlying token balance; however, the DSSes, which get frontran by the others, can fail to slash any of the vault's underlying tokens because calling the `slashAssets` function below executes `transferAmount = Math.min(totalAssets(), totalAssetsToSlash)` but the vault's `totalAssets()` is already 0 after the other DSSes' frontrunnings have slashed all of the vault's underlying token balance. Although the DSSes, which fail to slash the vault, can jail the operator afterwards but that is after the fact, and these DSSes have already suffered the damage caused by the operator's misbehavior without being compensated at all.

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/SlasherLib.sol#L94-L124
```solidity
    function requestSlashing(
        CoreLib.Storage storage self,
        IDSS dss,
        SlashRequest memory slashingMetadata,
        uint256 nonce
    ) external returns (QueuedSlashing memory queuedSlashing) {
        validateRequestSlashingParams(self, slashingMetadata, dss);
        uint256[] memory earmarkedStakes = fetchEarmarkedStakes(slashingMetadata);
        queuedSlashing = QueuedSlashing({
            dss: dss,
            timestamp: uint96(block.timestamp),
            operator: slashingMetadata.operator,
            vaults: slashingMetadata.vaults,
            earmarkedStakes: earmarkedStakes,
            nonce: nonce
        });
        self.slashingRequests[calculateRoot(queuedSlashing)] = true;
        self.operatorState[slashingMetadata.operator].nextSlashableTimestamp[dss] =
            block.timestamp + Constants.SLASHING_COOLDOWN;
        ...
    }
```

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/SlasherLib.sol#L79-L92
```solidity
    function fetchEarmarkedStakes(SlashRequest memory slashingMetadata)
        internal
        view
        returns (uint256[] memory earmarkedStakes)
    {
        earmarkedStakes = new uint256[](slashingMetadata.vaults.length);
        for (uint256 i = 0; i < slashingMetadata.vaults.length; ++i) {
            earmarkedStakes[i] = Math.mulDiv(
                slashingMetadata.slashPercentagesWad[i],
                IKarakBaseVault(slashingMetadata.vaults[i]).totalAssets(),
                Constants.MAX_SLASHING_PERCENT_WAD
            );
        }
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L193-L205
```solidity
    function slashAssets(uint256 totalAssetsToSlash, address slashingHandler)
        external
        onlyCore
        returns (uint256 transferAmount)
    {
        transferAmount = Math.min(totalAssets(), totalAssetsToSlash);

        // Approve to the handler and then call the handler which will draw the funds
        SafeTransferLib.safeApproveWithRetry(asset(), slashingHandler, transferAmount);
        ISlashingHandler(slashingHandler).handleSlashing(IERC20(asset()), transferAmount);
        ...
    }
```

### Recommended Mitigation
Each time the operator attempts to stake its vault to a DSS, the total slashing percentage against the vault after adding the new DSS's allowed slashing percentage must not exceed 100%; otherwise, such staking function call of the operator should revert.

## [06] First depositor attack is possible

### Description
After a vault is deployed, an attacker can call the following `Vault.deposit(uint256 assets, address to)` function to deposit 1 wei of the vault's underlying token. Afterwards, a staker also calls the `Vault.deposit(uint256 assets, address to)` function to deposit some of the underlying token. While the staker's `Vault.deposit` transaction is in the mempool, the attacker frontruns such transaction by sending a very large amount of the underlying token directly to the vault, which increases the vault's `totalAssets()` by a lot. After the frontrunning, when the staker's `Vault.deposit` transaction calls `solady`'s `ERC4626.deposit`, `ERC4626.previewDeposit`, and `ERC4626.convertToShares` functions, executing `FixedPointMathLib.fullMulDiv(assets, totalSupply() + 1, _inc(totalAssets()))` can return 0 because `totalAssets()` is higher than `assets * (totalSupply() + 1)`. As a result, the staker fails to mint any shares even though he deposits some of the underlying token already, and the attacker can redeem his share for the deposits he and the staker made. This means that the attacker is able to steal the staker's deposit.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L78-L87
```solidity
    function deposit(uint256 assets, address to)
        public
        override(ERC4626, IVault)
        whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT)
        nonReentrant
        returns (uint256 shares)
    {
        if (assets == 0) revert ZeroAmount();
        return super.deposit(assets, to);
    }
```

https://github.com/vectorized/solady/blob/main/src/tokens/ERC4626.sol#L376-L380
```solidity
    function deposit(uint256 assets, address to) public virtual returns (uint256 shares) {
        if (assets > maxDeposit(to)) _revert(0xb3c61a83); // `DepositMoreThanMax()`.
        shares = previewDeposit(assets);
        _deposit(msg.sender, to, assets, shares);
    }
```

https://github.com/Vectorized/solady/blob/main/src/tokens/ERC4626.sol#L220-L222
```solidity
    function previewDeposit(uint256 assets) public view virtual returns (uint256 shares) {
        shares = convertToShares(assets);
    }
```

https://github.com/Vectorized/solady/blob/main/src/tokens/ERC4626.sol#L167-L179
```solidity
    function convertToShares(uint256 assets) public view virtual returns (uint256 shares) {
        if (!_useVirtualShares()) {
            uint256 supply = totalSupply();
            return _eitherIsZero(assets, supply)
                ? _initialConvertToShares(assets)
                : FixedPointMathLib.fullMulDiv(assets, supply, totalAssets());
        }
        uint256 o = _decimalsOffset();
        if (o == uint256(0)) {
            return FixedPointMathLib.fullMulDiv(assets, totalSupply() + 1, _inc(totalAssets()));
        }
        return FixedPointMathLib.fullMulDiv(assets, totalSupply() + 10 ** o, _inc(totalAssets()));
    }
```

### Recommended Mitigation
Certain amount of shares can be minted to `address(0)` during the deployment of the vault, and the first depositor can be required to mint a minimal amount of shares.

## [07] `maxDeposit`, `maxMint`, `maxRedeem`, and `maxWithdraw` functions do not return 0 when they should

### Description
The following `pauseVault` function of the `Core` contract can be called to pause the `Vault` contract's `deposit`, `mint`, `startRedeem`, and `finishRedeem` functions. Also, the `Vault` contract's `withdraw` and `redeem` functions below always revert. Since the `Vault` contract inherits `solady`'s `ERC4626` contract, calling the `Vault` contract's `maxDeposit`, `maxMint`, `maxRedeem`, and `maxWithdraw` functions would not return 0 even when the respective `deposit`, `mint`, `startRedeem`, and `finishRedeem` functions are paused and `withdraw` and `redeem` functions always revert.

As specified in https://eips.ethereum.org/EIPS/eip-4626:
- `maxDeposit` `MUST factor in both global and user-specific limits, like if deposits are entirely disabled (even temporarily) it MUST return 0`;
- `maxMint` `MUST factor in both global and user-specific limits, like if mints are entirely disabled (even temporarily) it MUST return 0`;
- `maxRedeem` `MUST factor in both global and user-specific limits, like if redemption is entirely disabled (even temporarily) it MUST return 0`;
- `maxWithdraw` `MUST factor in both global and user-specific limits, like if withdrawals are entirely disabled (even temporarily) it MUST return 0`.

Hence, these `maxDeposit`, `maxMint`, `maxRedeem`, and `maxWithdraw` functions should return 0 when the respective `deposit`, `mint`, `startRedeem`, and `finishRedeem` functions are paused and `withdraw` and `redeem` functions always revert but that is not the case currently. As a result, these `maxDeposit`, `maxMint`, `maxRedeem`, and `maxWithdraw` functions are not compliant with the EIP-4626 standard.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Core.sol#L197-L199
```solidity
    function pauseVault(IKarakBaseVault vault, uint256 map) external onlyRolesOrOwner(Constants.MANAGER_ROLE) {
        vault.pause(map);
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L331-L362
```solidity
    function withdraw(uint256 assets, address to, address owner)
        public
        override
        whenFunctionNotPaused(Constants.PAUSE_VAULT_WITHDRAW)
        nonReentrant
        returns (uint256 shares)
    {
        // To suppress warnings
        owner = owner;
        assets = assets;
        to = to;
        shares = shares;

        revert NotImplemented();
    }
    ...
    function redeem(uint256 shares, address to, address owner)
        public
        override
        whenFunctionNotPaused(Constants.PAUSE_VAULT_REDEEM)
        nonReentrant
        returns (uint256 assets)
    {
        // To suppress warnings
        owner = owner;
        to = to;
        shares = shares;
        assets = assets;

        revert NotImplemented();
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L5-L29
```solidity
import {ERC4626, ERC20} from "solady/src/tokens/ERC4626.sol";
...
contract Vault is ERC4626, Initializable, Ownable, Pauser, ReentrancyGuard, ExtSloads, IVault {
```

https://github.com/vectorized/solady/blob/main/src/tokens/ERC4626.sol#L326-L359
```solidity
    function maxDeposit(address to) public view virtual returns (uint256 maxAssets) {
        to = to; // Silence unused variable warning.
        maxAssets = type(uint256).max;
    }

    ...
    function maxMint(address to) public view virtual returns (uint256 maxShares) {
        to = to; // Silence unused variable warning.
        maxShares = type(uint256).max;
    }

    ...
    function maxWithdraw(address owner) public view virtual returns (uint256 maxAssets) {
        maxAssets = convertToAssets(balanceOf(owner));
    }

    ...
    function maxRedeem(address owner) public view virtual returns (uint256 maxShares) {
        maxShares = balanceOf(owner);
    }
```

### Recommended Mitigation
The `Vault` contract can be updated to add the `maxDeposit`, `maxMint`, `maxRedeem`, and `maxWithdraw` functions to override the corresponding functions in `solady`'s `ERC4626` contract in which these `maxDeposit`, `maxMint`, `maxRedeem`, and `maxWithdraw` functions would return 0 when the respective `deposit`, `mint`, `startRedeem`, and `finishRedeem` functions are paused and `withdraw` and `redeem` functions always revert.

## [08] `deposit(uint256 assets, address to)`, `deposit(uint256 assets, address to, uint256 minSharesOut)`, `mint(uint256 shares, address to)`, and `startRedeem(uint256 shares, address beneficiary)` functions do not allow staker's approved spender to call these functions on behalf of staker

### Description
The `Vault` contract's `deposit(uint256 assets, address to)`, `deposit(uint256 assets, address to, uint256 minSharesOut)`, `mint(uint256 shares, address to)`, and `startRedeem(uint256 shares, address beneficiary)` functions all require `msg.sender` to be the actual staker, and the staker's approved spender cannot call these functions on behalf of the staker.

According to https://eips.ethereum.org/EIPS/eip-4626:
- `deposit` `MUST support EIP-20 ``approve`` / ``transferFrom`` on ``asset`` as a deposit flow`;
- `mint` `MUST support EIP-20 ``approve`` / ``transferFrom`` on ``asset`` as a mint flow`;
- `redeem` `MUST support a redeem flow where the shares are burned from ``owner`` directly where ``msg.sender`` has EIP-20 approval over the shares of ``owner``.

Since the `Vault` contract's `deposit(uint256 assets, address to)`, `deposit(uint256 assets, address to, uint256 minSharesOut)`, `mint(uint256 shares, address to)`, and `startRedeem(uint256 shares, address beneficiary)` functions do not support the staker's approved spender to call these functions on behalf of the staker, these functions are not EIP-4626 compliant.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L78-L87
```solidity
    function deposit(uint256 assets, address to)
        public
        override(ERC4626, IVault)
        whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT)
        nonReentrant
        returns (uint256 shares)
    {
        if (assets == 0) revert ZeroAmount();
        return super.deposit(assets, to);
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L94-L103
```solidity
    function deposit(uint256 assets, address to, uint256 minSharesOut)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT_SLIPPAGE)
        returns (uint256 shares)
    {
        if (assets == 0) revert ZeroAmount();
        shares = super.deposit(assets, to);
        if (shares < minSharesOut) revert NotEnoughShares();
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L110-L119
```solidity
    function mint(uint256 shares, address to)
        public
        override(ERC4626, IVault)
        whenFunctionNotPaused(Constants.PAUSE_VAULT_MINT)
        nonReentrant
        returns (uint256 assets)
    {
        if (shares == 0) revert ZeroShares();
        assets = super.mint(shares, to);
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L125-L149
```solidity
    function startRedeem(uint256 shares, address beneficiary)
        external
        whenFunctionNotPaused(Constants.PAUSE_VAULT_START_REDEEM)
        nonReentrant
        returns (bytes32 withdrawalKey)
    {
        if (shares == 0) revert ZeroShares();
        if (beneficiary == address(0)) revert ZeroAddress();

        (VaultLib.State storage state, VaultLib.Config storage config) = _storage();
        address staker = msg.sender;

        uint256 assets = convertToAssets(shares);

        withdrawalKey = WithdrawLib.calculateWithdrawKey(staker, state.stakerToWithdrawNonce[staker]++);

        state.withdrawalMap[withdrawalKey].staker = staker;
        state.withdrawalMap[withdrawalKey].start = uint96(block.timestamp);
        state.withdrawalMap[withdrawalKey].shares = shares;
        state.withdrawalMap[withdrawalKey].beneficiary = beneficiary;

        this.transferFrom(msg.sender, address(this), shares);

        emit StartedRedeem(staker, config.operator, shares, withdrawalKey, assets);
    }
```

### Recommended Mitigation
The `Vault` contract's `deposit(uint256 assets, address to)`, `deposit(uint256 assets, address to, uint256 minSharesOut)`, `mint(uint256 shares, address to)`, and `startRedeem(uint256 shares, address beneficiary)` functions can be updated to allow the staker's approved spender to call these functions on behalf of the staker.

## [09] `previewWithdraw` and `previewRedeem` functions do not revert though they should

### Description
According to https://eips.ethereum.org/EIPS/eip-4626:
- `previewWithdraw` `MUST return as close to and no fewer than the exact amount of Vault shares that would be burned in a ``withdraw`` call in the same transaction` and `MAY revert due to other conditions that would also cause ``withdraw`` to revert`;
- `previewRedeem` `MUST return as close to and no more than the exact amount of assets that would be withdrawn in a ``redeem`` call in the same transaction` and `MAY revert due to other conditions that would also cause ``redeem`` to revert`.

Since the `Vault` contract's following `withdraw` and `redeem` functions always revert, no `assets` can be withdrawn and no `share` can be redeemed through such `withdraw` and `redeem` functions. Yet, because the `Vault` contract inherits `solady`'s `ERC4626` contract, the `previewWithdraw` and `previewRedeem` functions below for the `Vault` contract can still return positive values. Hence, these `previewWithdraw` and `previewRedeem` functions are not compliant with the EIP-4626 standard.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L331-L362
```solidity
    function withdraw(uint256 assets, address to, address owner)
        public
        override
        whenFunctionNotPaused(Constants.PAUSE_VAULT_WITHDRAW)
        nonReentrant
        returns (uint256 shares)
    {
        // To suppress warnings
        owner = owner;
        assets = assets;
        to = to;
        shares = shares;

        revert NotImplemented();
    }
    ...
    function redeem(uint256 shares, address to, address owner)
        public
        override
        whenFunctionNotPaused(Constants.PAUSE_VAULT_REDEEM)
        nonReentrant
        returns (uint256 assets)
    {
        // To suppress warnings
        owner = owner;
        to = to;
        shares = shares;
        assets = assets;

        revert NotImplemented();
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L5-L29
```solidity
import {ERC4626, ERC20} from "solady/src/tokens/ERC4626.sol";
...
contract Vault is ERC4626, Initializable, Ownable, Pauser, ReentrancyGuard, ExtSloads, IVault {
```

https://github.com/Vectorized/solady/blob/1f43cc8005cc3b3c8361dd7dbdd2cdeaf0f99e66/src/tokens/ERC4626.sol#L266-L278
```solidity
    function previewWithdraw(uint256 assets) public view virtual returns (uint256 shares) {
        if (!_useVirtualShares()) {
            uint256 supply = totalSupply();
            return _eitherIsZero(assets, supply)
                ? _initialConvertToShares(assets)
                : FixedPointMathLib.fullMulDivUp(assets, supply, totalAssets());
        }
        uint256 o = _decimalsOffset();
        if (o == uint256(0)) {
            return FixedPointMathLib.fullMulDivUp(assets, totalSupply() + 1, _inc(totalAssets()));
        }
        return FixedPointMathLib.fullMulDivUp(assets, totalSupply() + 10 ** o, _inc(totalAssets()));
    }
```

https://github.com/Vectorized/solady/blob/1f43cc8005cc3b3c8361dd7dbdd2cdeaf0f99e66/src/tokens/ERC4626.sol#L294-L296
```solidity
    function previewRedeem(uint256 shares) public view virtual returns (uint256 assets) {
        assets = convertToAssets(shares);
    }
```

https://github.com/Vectorized/solady/blob/1f43cc8005cc3b3c8361dd7dbdd2cdeaf0f99e66/src/tokens/ERC4626.sol#L192-L204
```solidity
    function convertToAssets(uint256 shares) public view virtual returns (uint256 assets) {
        if (!_useVirtualShares()) {
            uint256 supply = totalSupply();
            return supply == uint256(0)
                ? _initialConvertToAssets(shares)
                : FixedPointMathLib.fullMulDiv(shares, totalAssets(), supply);
        }
        uint256 o = _decimalsOffset();
        if (o == uint256(0)) {
            return FixedPointMathLib.fullMulDiv(shares, totalAssets() + 1, _inc(totalSupply()));
        }
        return FixedPointMathLib.fullMulDiv(shares, totalAssets() + 1, totalSupply() + 10 ** o);
    }
```

### Recommended Mitigation
The `Vault` contract can be updated to add the `previewWithdraw` and `previewRedeem` functions, which override the corresponding functions in `solady`'s `ERC4626` contract, that would revert.

## [10] Pausing `deposit(uint256 assets, address to, uint256 minSharesOut)` function causes stakers to lose slippage protection when depositing into vault

### Description
It is possible that the following `pauseVault` function of the `Core` contract is called to pause the `Vault` contract's `deposit(uint256 assets, address to, uint256 minSharesOut)` function without pausing the `Vault` contract's `deposit(uint256 assets, address to)` function; for example, when `_getPauserStorage()._paused` is set to 2, `paused(1)` equals `true` but `paused(0)` equals `false`. When this happens, the slippage protection is missing when stakers deposit into the vault. Without the slippage control, the number of shares minted to stakers can be lower than what they should be when malicious frontrunnings occur before stakers' deposits are executed.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Core.sol#L197-L199
```solidity
    function pauseVault(IKarakBaseVault vault, uint256 map) external onlyRolesOrOwner(Constants.MANAGER_ROLE) {
        vault.pause(map);
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L209-L211
```solidity
    function pause(uint256 map) external onlyCore {
        _pause(map);
    }
```

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/Pauser.sol#L67-L72
```solidity
    function _pause(uint256 map) internal virtual {
        PauserStorage storage self = _getPauserStorage();
        if ((self._paused & map) != self._paused) revert AttemptedUnpauseWhilePausing();
        self._paused = map;
        emit Paused(_msgSender(), map);
    }
```

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/Pauser.sol#L49-L52
```solidity
    modifier whenFunctionNotPaused(uint8 index) {
        if (paused(index)) revert EnforcedPauseFunction(index);
        _;
    }
```

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/Pauser.sol#L58-L61
```solidity
    function paused(uint8 index) public view virtual returns (bool) {
        uint256 mask = 1 << index;
        return ((_getPauserStorage()._paused & mask) != 0);
    }
```

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/interfaces/Constants.sol#L34-L35
```solidity
    uint8 internal constant PAUSE_VAULT_DEPOSIT = 0;
    uint8 internal constant PAUSE_VAULT_DEPOSIT_SLIPPAGE = 1;
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L94-L103
```solidity
    function deposit(uint256 assets, address to, uint256 minSharesOut)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT_SLIPPAGE)
        returns (uint256 shares)
    {
        if (assets == 0) revert ZeroAmount();
        shares = super.deposit(assets, to);
        if (shares < minSharesOut) revert NotEnoughShares();
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L78-L87
```solidity
    function deposit(uint256 assets, address to)
        public
        override(ERC4626, IVault)
        whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT)
        nonReentrant
        returns (uint256 shares)
    {
        if (assets == 0) revert ZeroAmount();
        return super.deposit(assets, to);
    }
```

### Recommended Mitigation
The `Core.pauseVault` or `Vault.pause` function can be updated to only allow the `Vault` contract's `deposit(uint256 assets, address to, uint256 minSharesOut)`, `deposit(uint256 assets, address to)`, and `mint(uint256 shares, address to)` functions to be paused or unpaused all together instead of allowing each of them to be paused or unpaused individually.

## [11] `Vault` contract's withdrawal functions can be paused while its deposit functions are unpaused
### Description
The `Core.pauseVault` function can be called to pause the `Vault.startRedeem` function and/or `Vault.finishRedeem` function while the `Vault` contract's other functions like `deposit` and `mint` are not paused. When this happens, the stakers can still deposit into the vault but cannot withdraw from the vault. If this situation persists, the stakers' deposited funds can be locked in the vault for an undetermined duration that is not controlled by the stakers even though they should have the right to withdraw their deposits especially when no slashing occurs against the vault.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Core.sol#L197-L199
```solidity
    function pauseVault(IKarakBaseVault vault, uint256 map) external onlyRolesOrOwner(Constants.MANAGER_ROLE) {
        vault.pause(map);
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L125-L149
```solidity
    function startRedeem(uint256 shares, address beneficiary)
        external
        whenFunctionNotPaused(Constants.PAUSE_VAULT_START_REDEEM)
        nonReentrant
        returns (bytes32 withdrawalKey)
    {
        if (shares == 0) revert ZeroShares();
        if (beneficiary == address(0)) revert ZeroAddress();

        (VaultLib.State storage state, VaultLib.Config storage config) = _storage();
        address staker = msg.sender;

        uint256 assets = convertToAssets(shares);

        withdrawalKey = WithdrawLib.calculateWithdrawKey(staker, state.stakerToWithdrawNonce[staker]++);

        state.withdrawalMap[withdrawalKey].staker = staker;
        state.withdrawalMap[withdrawalKey].start = uint96(block.timestamp);
        state.withdrawalMap[withdrawalKey].shares = shares;
        state.withdrawalMap[withdrawalKey].beneficiary = beneficiary;

        this.transferFrom(msg.sender, address(this), shares);
        ...
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L157-L188
```solidity
    function finishRedeem(bytes32 withdrawalKey)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_VAULT_FINISH_REDEEM)
    {
        (VaultLib.State storage state, VaultLib.Config storage config) = _storage();

        WithdrawLib.QueuedWithdrawal memory startedWithdrawal = state.validateQueuedWithdrawal(withdrawalKey);

        uint256 shares = startedWithdrawal.shares;
        if (shares > maxRedeem(address(this))) revert RedeemMoreThanMax();
        uint256 redeemableAssets = convertToAssets(shares);

        delete state.withdrawalMap[withdrawalKey];

        _withdraw({
            by: address(this),
            to: startedWithdrawal.beneficiary,
            owner: address(this),
            assets: redeemableAssets,
            shares: shares
        });
        ...
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L94-L103
```solidity
    function deposit(uint256 assets, address to, uint256 minSharesOut)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT_SLIPPAGE)
        returns (uint256 shares)
    {
        if (assets == 0) revert ZeroAmount();
        shares = super.deposit(assets, to);
        if (shares < minSharesOut) revert NotEnoughShares();
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L110-L119
```solidity
    function mint(uint256 shares, address to)
        public
        override(ERC4626, IVault)
        whenFunctionNotPaused(Constants.PAUSE_VAULT_MINT)
        nonReentrant
        returns (uint256 assets)
    {
        if (shares == 0) revert ZeroShares();
        assets = super.mint(shares, to);
    }
```

### Recommended Mitigation
The `Vault` contract can be updated to only allow its `startRedeem` and `finishRedeem` functions to be paused and unpaused all together instead of being paused or unpaused individually. Moreover, when the `Vault` contract's withdrawal functions like `startRedeem` and `finishRedeem` are paused, its deposit functions like `deposit` and `mint` can be required to be paused as well; in contrast, when the `Vault` contract's deposit functions are paused, its withdrawal functions do not need to be paused.

## [12] `Vault.mint` and `Vault.finishRedeem` functions do not have slippage controls

### Description
Unlike the `Vault.deposit(uint256 assets, address to, uint256 minSharesOut)` function that includes the `minSharesOut` input for slippage control, the `Vault.mint` and `Vault.finishRedeem` functions do not have similar inputs for slippage controls. When the staker is an EOA, the `previewMint` and `Vault.mint` functions need to be called in separate transactions, and the `previewRedeem` and `Vault.finishRedeem` functions need to be called in separate transactions. When malicious frontrunnings that manipulate the vault's exchange rates occur, calling the `Vault.mint` function can cause the staker to send more underlying tokens than expected to the vault, and calling the `Vault.finishRedeem` function can cause the staker to receive less underlying tokens than expected from the vault.

Moreover, according to https://eips.ethereum.org/EIPS/eip-4626#security-considerations, `If implementors intend to support EOA account access directly, they should consider adding an additional function call for ``deposit``/``mint``/``withdraw``/``redeem`` with the means to accommodate slippage loss or unexpected deposit/withdrawal limits, since they have no other means to revert the transaction if the exact output amount is not achieved`. Hence, having no slippage controls in the `Vault.mint` and `Vault.finishRedeem` functions is also EIP-4626 non-compliant.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L94-L103
```solidity
    function deposit(uint256 assets, address to, uint256 minSharesOut)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT_SLIPPAGE)
        returns (uint256 shares)
    {
        if (assets == 0) revert ZeroAmount();
        shares = super.deposit(assets, to);
        if (shares < minSharesOut) revert NotEnoughShares();
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L110-L119
```solidity
    function mint(uint256 shares, address to)
        public
        override(ERC4626, IVault)
        whenFunctionNotPaused(Constants.PAUSE_VAULT_MINT)
        nonReentrant
        returns (uint256 assets)
    {
        if (shares == 0) revert ZeroShares();
        assets = super.mint(shares, to);
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L157-L188
```solidity
    function finishRedeem(bytes32 withdrawalKey)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_VAULT_FINISH_REDEEM)
    {
        (VaultLib.State storage state, VaultLib.Config storage config) = _storage();

        WithdrawLib.QueuedWithdrawal memory startedWithdrawal = state.validateQueuedWithdrawal(withdrawalKey);

        uint256 shares = startedWithdrawal.shares;
        if (shares > maxRedeem(address(this))) revert RedeemMoreThanMax();
        uint256 redeemableAssets = convertToAssets(shares);

        delete state.withdrawalMap[withdrawalKey];

        _withdraw({
            by: address(this),
            to: startedWithdrawal.beneficiary,
            owner: address(this),
            assets: redeemableAssets,
            shares: shares
        });
        ...
    }
```

### Recommended Mitigation
The `Vault.mint` and `Vault.finishRedeem` functions can be updated to include inputs for slippage controls.

## [13] Vault deployed for token that does not yet exist but will be deployed can cause stakers to lose their deposits

### Description
When a token that exists on one chain but not yet on the other chain, such token's protocol can deterministically deploy such token to the same address on the other chain in the future. For such token that does not yet exist but is expected to exist on a chain, the following `allowlistAssets` function can be called to allowlist it on the corresponding chain. Afterwards, the operator can deploy a vault for such token. Although such token does not exist yet, stakers can call the vault's deposit functions like `deposit` below to mint shares without sending any token amount because `solady`'s `SafeTransferLib` used by the `Vault` contract does not check whether the token has code or not. These deposits can mess up the vault's accounting. For example, a malicious staker can mint a very large number of shares that is somewhat close to `type(uint256).max` while does not send any token amount to the vault when the token does not exist. After the token is deployed and exists on the chain, innocent stakers would mint shares by sending token amounts to the vault. When the vault's `totalSupply()` becomes very close to `type(uint256).max`, the malicious staker can mint the difference between `type(uint256).max` and the existing `totalSupply()` by sending a small token amount. When the vault's `totalSupply()` equals `type(uint256).max`, calling the vault's `startRedeem` function that further calls `solady`'s `ERC4626.convertToAssets` function can revert since executing `FixedPointMathLib.fullMulDiv(shares, totalAssets() + 1, _inc(totalSupply()))` would divide by 0 and revert. As a result, the innocent stakers are DOS'ed from calling the vault's `startRedeem` function and lose their deposits.

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/CoreLib.sol#L67-L75
```solidity
    function allowlistAssets(Storage storage self, address[] memory assets, address[] memory slashingHandlers)
        external
    {
        if (assets.length != slashingHandlers.length) revert LengthsDontMatch();
        for (uint256 i = 0; i < assets.length; i++) {
            if (slashingHandlers[i] == address(0) || assets[i] == address(0)) revert ZeroAddress();
            self.assetSlashingHandlers[assets[i]] = slashingHandlers[i];
        }
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L94-L103
```solidity
    function deposit(uint256 assets, address to, uint256 minSharesOut)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT_SLIPPAGE)
        returns (uint256 shares)
    {
        if (assets == 0) revert ZeroAmount();
        shares = super.deposit(assets, to);
        if (shares < minSharesOut) revert NotEnoughShares();
    }
```

https://github.com/Vectorized/solady/blob/a95f6714868cfe5d590145f936d0661bddff40d2/src/utils/SafeTransferLib.sol#L11-L13
```solidity
/// - For ERC20s, this implementation won't check that a token has code,
///   responsibility is delegated to the caller.
library SafeTransferLib {
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L125-L149
```solidity
    function startRedeem(uint256 shares, address beneficiary)
        external
        whenFunctionNotPaused(Constants.PAUSE_VAULT_START_REDEEM)
        nonReentrant
        returns (bytes32 withdrawalKey)
    {
        if (shares == 0) revert ZeroShares();
        if (beneficiary == address(0)) revert ZeroAddress();

        (VaultLib.State storage state, VaultLib.Config storage config) = _storage();
        address staker = msg.sender;

        uint256 assets = convertToAssets(shares);

        ...
    }
```

https://github.com/Vectorized/solady/blob/main/src/tokens/ERC4626.sol#L192-L204
```solidity
    function convertToAssets(uint256 shares) public view virtual returns (uint256 assets) {
        ...
        uint256 o = _decimalsOffset();
        if (o == uint256(0)) {
            return FixedPointMathLib.fullMulDiv(shares, totalAssets() + 1, _inc(totalSupply()));
        }
        ...
    }
```

https://github.com/Vectorized/solady/blob/a95f6714868cfe5d590145f936d0661bddff40d2/src/tokens/ERC4626.sol#L310-L314
```solidity
    function _inc(uint256 x) private pure returns (uint256) {
        unchecked {
            return x + 1;
        }
    }
```

### Recommended Mitigation
The `CoreLib.allowlistAssets` function can be updated to revert if the underlying token does not exist on the corresponding chain even though it will be deployed and exist on such chain in the future.

## [14] DSS's slashing request can be canceled after `SLASHING_VETO_WINDOW` is passed

### Description
Although the `SlasherLib.finalizeSlashing` function requires that the slashing can only be finalized when the `SLASHING_VETO_WINDOW` is passed after the slashing is requested, the `SlasherLib.cancelSlashing` function does not require the slashing veto committee to cancel the slashing request during the `SLASHING_VETO_WINDOW`. After the `SLASHING_VETO_WINDOW` is passed, the DSS should be allowed to finalize the slashing request but the slashing veto committee can always intentionally or unintentionally frontrun the DSS's `finalizeSlashing` transaction. As a result, the DSS can fail to slash the operator's vault when it should be allowed.

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Core.sol#L235-L243
```solidity
    function cancelSlashing(SlasherLib.QueuedSlashing memory queuedSlashing)
        external
        whenFunctionNotPaused(Constants.PAUSE_CORE_CANCEL_SLASHING)
        nonReentrant
        onlyRoles(Constants.VETO_COMMITTEE_ROLE)
    {
        _self().cancelSlashing(queuedSlashing);
        emit CancelledSlashing(msg.sender, queuedSlashing);
    }
```

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/SlasherLib.sol#L153-L168
```solidity
    function cancelSlashing(CoreLib.Storage storage self, QueuedSlashing memory queuedSlashing) external {
        bytes32 slashRoot = calculateRoot(queuedSlashing);
        if (!self.slashingRequests[slashRoot]) revert InvalidSlashingParams();
        delete self.slashingRequests[slashRoot];
        IDSS dss = queuedSlashing.dss;

        HookLib.callHookIfInterfaceImplemented({
            dss: dss,
            data: abi.encodeWithSelector(dss.cancelSlashingHook.selector, queuedSlashing.operator),
            interfaceId: dss.cancelSlashingHook.selector,
            ignoreFailure: true,
            hookCallGasLimit: self.hookCallGasLimit,
            supportsInterfaceGasLimit: self.supportsInterfaceGasLimit,
            hookGasBuffer: self.hookGasBuffer
        });
    }
```

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/SlasherLib.sol#L126-L151
```solidity
    function finalizeSlashing(CoreLib.Storage storage self, QueuedSlashing memory queuedSlashing) external {
        bytes32 slashRoot = calculateRoot(queuedSlashing);
        if (!self.slashingRequests[slashRoot]) revert InvalidSlashingParams();
        if (queuedSlashing.timestamp + Constants.SLASHING_VETO_WINDOW > block.timestamp) {
            revert MinSlashingDelayNotPassed();
        }
        delete self.slashingRequests[slashRoot];


        for (uint256 i = 0; i < queuedSlashing.vaults.length; i++) {
            IKarakBaseVault(queuedSlashing.vaults[i]).slashAssets(
                queuedSlashing.earmarkedStakes[i],
                self.assetSlashingHandlers[IKarakBaseVault(queuedSlashing.vaults[i]).asset()]
            );
        }
        IDSS dss = queuedSlashing.dss;

        HookLib.callHookIfInterfaceImplemented({
            dss: dss,
            data: abi.encodeWithSelector(dss.finishSlashingHook.selector, queuedSlashing.operator),
            interfaceId: dss.finishSlashingHook.selector,
            ignoreFailure: true,
            hookCallGasLimit: self.hookCallGasLimit,
            supportsInterfaceGasLimit: self.supportsInterfaceGasLimit,
            hookGasBuffer: self.hookGasBuffer
        });
    }
```

### Recommended Mitigation
The `SlasherLib.cancelSlashing` function can be updated to revert if `queuedSlashing.timestamp + Constants.SLASHING_VETO_WINDOW <= block.timestamp` is true.

## [15] Operator can collude with DSS for stealing stakers' deposits

### Description
It is possible that an operator colludes with a DSS. Similar to the normal workflow, the operator would register with the DSS, deploy an vault, and stake such vault to such DSS. After stakers make enough deposits into the operator's vault, the operator would intentionally misbehave, and the DSS would request to slash such vault. Because the operator's misbehavior is indeed malicious, the slashing veto committee would not cancel the DSS's slashing request. After the `SLASHING_VETO_WINDOW` is passed after the DSS requests such slashing, the following `finalizeSlashing` function can be called to slash the DSS's slashing percentage of the vault's underlying token balance in which some of the slashed token amount can then be transferred to the operator. As a result, some or all of the stakers' deposits are stolen by the operator and DSS.

https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/SlasherLib.sol#L126-L151
```solidity
    function finalizeSlashing(CoreLib.Storage storage self, QueuedSlashing memory queuedSlashing) external {
        bytes32 slashRoot = calculateRoot(queuedSlashing);
        if (!self.slashingRequests[slashRoot]) revert InvalidSlashingParams();
        if (queuedSlashing.timestamp + Constants.SLASHING_VETO_WINDOW > block.timestamp) {
            revert MinSlashingDelayNotPassed();
        }
        delete self.slashingRequests[slashRoot];

        for (uint256 i = 0; i < queuedSlashing.vaults.length; i++) {
            IKarakBaseVault(queuedSlashing.vaults[i]).slashAssets(
                queuedSlashing.earmarkedStakes[i],
                self.assetSlashingHandlers[IKarakBaseVault(queuedSlashing.vaults[i]).asset()]
            );
        }
        IDSS dss = queuedSlashing.dss;
        ...
    }
```

https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L193-L205
```solidity
    function slashAssets(uint256 totalAssetsToSlash, address slashingHandler)
        external
        onlyCore
        returns (uint256 transferAmount)
    {
        transferAmount = Math.min(totalAssets(), totalAssetsToSlash);

        // Approve to the handler and then call the handler which will draw the funds
        SafeTransferLib.safeApproveWithRetry(asset(), slashingHandler, transferAmount);
        ISlashingHandler(slashingHandler).handleSlashing(IERC20(asset()), transferAmount);
        ...
    }
```

### Recommended Mitigation
When the number of times that the operator's vaults are slashed reach certain threshold or the total slashed token amount of the operator's vaults reach certain threshold, the operator can be banned from deploying new vaults, the operator's registered DSSes can be disallowed from further slashing the operator's vaults, and these vaults' deposit and mint functions can be permanently paused while these vaults' existing stakers can be allowed to withdraw from such vaults without any delay.