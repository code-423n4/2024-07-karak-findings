
## 1. Solidity version ^0.8.23 won't work on all chains due to MCOPY 

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/foundry.toml#L12-L12
### Impact

Solidity version 0.8.23 introduces the MCOPY opcode, this may not be implemented on all chains and L2 thus reducing the portability and compatibility of the code. The protocol is expected to be deloyed on Ethereum,Arbitrum,Base,BSC,Optimism,Blast, Mantle, K2 and as a result, deployment may fail on these chains.

```solidity
solc = "0.8.25"
```
### Recommended Mitigation Steps
Consider using a earlier solidity version.

***


## 2. Veto Committee can cancel slashing after vetodelay period `cancelSlashing`

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L153

### Impact

The `cancelSlashing` function can only be called by the veto committee and should occur within a `SLASHING_VETO_WINDOW` withing which the committee analyzes and can cancel the slashing request. 

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
The `cancelSlashing` function however doesn't enforce this time period, and as a result, even after the slashing veto window is passed, and has not been slashed, a malicious/compromised veto committee within that time period can easily cancel the slashing of vaults even if they should be slashed. 

### Recommended Mitigation Steps
Consider checking if the veto window has passed, and reverting if it has.

***


## 3. Functions to get tokens out of the protocol should not be pausable

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L43

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L232

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L265

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L127

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L160

### Impact

Ideally, functions that help users get their funds out of the protocol should not be pausable. This is because, during emergency events, the users need to have the safety and possibility of rescuing their funds during these situations. Also, if the owner gets malicious, he can pause the contracts and renounce ownership, potentially locking the tokens indefnitely.

### Recommended Mitigation Steps

Remove the whenNot`paused modifiers from the functions in the highlighted links above.
***


## 4. SafeTransferLib version in use may revert for certain token types

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L56-L58

### Impact
The contracts uses solady version 0.0.201 to handle token transfers during slashing. The version in use doesn't mask the addresses in the `safeTransfer` and `safeTransferFrom` functions will cause that tokens like FRAX, UNI, CRV, COMP to sometimes fail during their transfers, casuing slashing them to fail. The issues is reported [here](https://github.com/transmissions11/solmate/issues?q=is%3Aissue+346+is%3Aclosed) for solmate's own implementation (which is more or less the same as solady's)

```solidity
		"solady": "^v0.0.201"
```
```solidity
            let m := mload(0x40) // Cache the free memory pointer.
            mstore(0x60, amount) // Store the `amount` argument.
            mstore(0x40, to) // Store the `to` argument.
            mstore(0x2c, shl(96, from)) // Store the `from` argument.
            mstore(0x0c, 0x23b872dd000000000000000000000000) // `transferFrom(address,address,uint256)`.
```

Since the protocol will be working with various token types, these tokens may fail during their transfers and as a result may be unslashable.

```solidity
    function handleSlashing(IERC20 token, uint256 amount) external {
        if (amount == 0) revert ZeroAmount();
        if (!_config().supportedAssets[token]) revert UnsupportedAsset();

        SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount);
        // Below is where custom logic for each asset lives
        SafeTransferLib.safeTransfer(address(token), address(0), amount);
    }
```

### Recommended Mitigation Steps
Recommendation is to use a different implementation as the issue has not been fixed on solady. Using openzepellin's implementation is recommended.
***


## 5. Protocol plans to deploy on blast but doesn't perform the needed configurations.

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/main/README.md#scoping-q--a
### Impact

The contracts are to be deployed on blast, an EVM compatible layer 2 chain known for claiming yields and gas fees.
> Chains the protocol will be deployed on	... Blast ...

Blast redirects sequencer or gas fees to the dapps that induced them, allowing smart contract developers to have an additional source of revenue. Since these contracts are to be used for by lots of users, it can be expected that functions inside the contracts will be spending a lot of gas. This can be beneficial to the developers as they can have additional revenue. However, the contracts implementation are not configured to take advantage of blast's features on claiming gas fees. This can be a lost of opportunity to earn potential income from the usage of the contracts.

### Recommended Mitigation Steps

The contracts should implement the procedure from [Blast Docs](https://docs.blast.io/building/guides/gas-fees) on how to properly claim gas fees.

***


## 6. Beacon proofs validation will be impossible on certain chains

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L416

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/interfaces/Constants.sol#L29

### Impact

The BEACON_ROOTS_ADDRESS is hardcoded to 0x000F3df6D732807Ef1319fB7B8bB8522d0Beac02, which is the expected address according to the EIP. It is used when validating beacon proofs.
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
However, this has not been deployed on certain chains, and on some other chains, the address belongs to an EOA. This will lead to issues with validation on these chains.

On karak - https://explorer.karak.network/address/0x000F3df6D732807Ef1319fB7B8bB8522d0Beac02;
On mantle https://mantlescan.info/address/0x000F3df6D732807Ef1319fB7B8bB8522d0Beac02;
On bsc https://bscscan.com/address/0x000F3df6D732807Ef1319fB7B8bB8522d0Beac02
On arbitrum https://arbiscan.io/address/0x000F3df6D732807Ef1319fB7B8bB8522d0Beac02   

### Recommended Mitigation Steps
Consider deloying this first before deploying on those chains.

***


## 7. Redundant `!isOperatorRegisteredToDSS` check in `unregisterOperatorFromDSS` in Operator.sol can be removed

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L120

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L121


### Impact

To unregister an operator from DSS, two major steps are performed, 
1. Checking if the operator is registered in a registered DSS, 2. Actually unregistering the operator from the DSS
```solidity
    function unregisterOperatorFromDSS(IDSS dss, bytes memory unregistrationHookData)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_CORE_UNREGISTER_FROM_DSS)
    {
        address operator = msg.sender;
        CoreLib.Storage storage self = _self();
        self.checkIfOperatorIsRegInRegDSS(operator, dss);
        self.unregisterOperatorFromDSS(dss, operator, unregistrationHookData);

        emit UnregisteredOperatorToDSS(operator, address(dss));
    }
```

The first step, using the `checkIfOperatorIsRegInRegDSS` function checks if `!isOperatorRegisteredToDSS` upon which it reverts.

```solidity
    function checkIfOperatorIsRegInRegDSS(CoreLib.Storage storage self, address operator, IDSS dss) internal view {
        if (!self.isDSSRegistered(dss)) revert DSSNotRegistered();
        if (!isOperatorRegisteredToDSS(self, operator, dss)) { //@here
            revert OperatorNotValidatingForDSS();
        }
    }
```

The next step, in `unregisterOperatorFromDSS` also repeats this check, which is now no longer needed.
```solidity
    function unregisterOperatorFromDSS(
        CoreLib.Storage storage self,
        IDSS dss,
        address operator,
        bytes memory unregistrationHookData
    ) external {
        State storage operatorState = self.operatorState[operator];
        // Checks if all operator delegations are zero
        address[] memory vaults = getVaultsStakedToDSS(operatorState, dss);
        if (vaults.length != 0) revert AllVaultsNotUnstakedFromDSS();
        if (!isOperatorRegisteredToDSS(self, operator, dss)) revert OperatorNotValidatingForDSS(); //@here

        self.operatorState[operator].dssMap.remove(address(dss));
        HookLib.callHookIfInterfaceImplemented({
            dss: dss,
            data: abi.encodeWithSelector(dss.unregistrationHook.selector, operator, unregistrationHookData),
            interfaceId: dss.unregistrationHook.selector,
            ignoreFailure: true, // So it can't prevent the operator from unregistering
            hookCallGasLimit: self.hookCallGasLimit,
            supportsInterfaceGasLimit: self.supportsInterfaceGasLimit,
            hookGasBuffer: self.hookGasBuffer
        });
    }
```

It can be safely removed in `unregisterOperatorFromDSS` in Operator.sol because the function is only called once, in the `unregisterOperatorFromDSS` in Core.sol, which, as has been established, already performs the check. So theres no need to repeat it.

### Recommended Mitigation Steps
```diff
    function unregisterOperatorFromDSS(
        CoreLib.Storage storage self,
        IDSS dss,
        address operator,
        bytes memory unregistrationHookData
    ) external {
        State storage operatorState = self.operatorState[operator];
        // Checks if all operator delegations are zero
        address[] memory vaults = getVaultsStakedToDSS(operatorState, dss);
        if (vaults.length != 0) revert AllVaultsNotUnstakedFromDSS();
-        if (!isOperatorRegisteredToDSS(self, operator, dss)) revert OperatorNotValidatingForDSS(); //@here, not needed.

        self.operatorState[operator].dssMap.remove(address(dss));
        HookLib.callHookIfInterfaceImplemented({
            dss: dss,
            data: abi.encodeWithSelector(dss.unregistrationHook.selector, operator, unregistrationHookData),
            interfaceId: dss.unregistrationHook.selector,
            ignoreFailure: true, // So it can't prevent the operator from unregistering
            hookCallGasLimit: self.hookCallGasLimit,
            supportsInterfaceGasLimit: self.supportsInterfaceGasLimit,
            hookGasBuffer: self.hookGasBuffer
        });
    }
```
***


## 8. Hardcoding decimals to `_DEFAULT_UNDERLYING_DECIMALS` if `_tryGetAssetDecimals` fails is not advisable and may lead to issues with certain tokens.

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L62

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L68

https://github.com/Vectorized/solady/blob/183a5c9cc3ca982492ae5fcca9e7ed6668ddb6ac/src/tokens/ERC4626.sol#L122

### Impact

When initializing a token vault, an attempt is made to get the token's decimals, through the [`_tryGetAssetDecimals`](https://github.com/Vectorized/solady/blob/183a5c9cc3ca982492ae5fcca9e7ed6668ddb6ac/src/tokens/ERC4626.sol#L122) function. If this fails, the token decimals is set as the [`_DEFAULT_UNDERLYING_DECIMALS`](https://github.com/Vectorized/solady/blob/183a5c9cc3ca982492ae5fcca9e7ed6668ddb6ac/src/tokens/ERC4626.sol#L18) which is 18. 

```solidity
        (bool decimalsSuccess, uint8 result) = _tryGetAssetDecimals(address(_depositToken));
        VaultLib.Config storage config = _config();

        config.asset = _depositToken;
        config.name = _name;
        config.symbol = _symbol;
        config.decimals = decimalsSuccess ? result : _DEFAULT_UNDERLYING_DECIMALS;
```
For certain tokens (including standard ones), the decimals function is absent since its not a mandatory part of the [EIP20 standard](https://eips.ethereum.org/EIPS/eip-20#decimals). Handling tokens like this on etherscan is not standard as some of these tokens are set as 0 decimals, for instance, [here](https://etherscan.io/token/0x1da4858ad385cc377165a298cc2ce3fce0c5fd31). Also, if the `_tryGetAssetDecimals` function somehow fails, the `_DEFAULT_UNDERLYING_DECIMALS` might not be the actual token decimal. For instance, if the function fails for a token like USDC with 6 decimals, the `config.decimals` parameter will be set to 18 erroneously.

This will cause issues durng deposits, transfers, slashes and withdrawal from these vaults.

### Recommended Mitigation Steps

Recommend reverting instead, as a safer option.

***


## 9. `fromLittleEndianUint64` risks an unsafe cast when input is > 2^64

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L54

### Impact
The input to `fromLittleEndianUint64` in BeaconProofsLib.sol is not validated. It should check that the number to be cast is not greater than `2^64` in little endian notation. If a value greater than 2^64 is passed in, the casting will be done incorrectly;

### Recommended Mitigation Steps

Add the corresponding check to revert for casting of numbers greater than `2^64` in little endian:

```solidity

+ if(n & 0xFFFFFFFFFFFFFFFF000000000000000000000000000000000000000000000000 != n){
+ return ERROR_LITTLE_ENDIAN_UNSAFE_UINT64_CAST()
+ }
    n = uint64(uint256(lenum >> 192));
```
### Recommended Mitigation Steps

***


## 10. Can introduce a `mint` function with max assets to spend function, just like its `deposit` counterpart

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L94

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L110

### Impact

To protect from malicious frontrunning, Vault.sol has two `deposit` functions, including one that allows the user to specify the minimum amount of shares to mint. The same option can be can be introduced for the `mint` function, that allows the user to specify the maximum amount of assets they're willing to spend to mint the specified amount of share. This also helps protect from malicious frontrunning.

### Recommended Mitigation Steps

```diff
+    function mint(uint256 shares, address to, uint256 maxAssetsIn)
+        public
+        override(ERC4626, IVault)
+        whenFunctionNotPaused(Constants.PAUSE_VAULT_MINT_SLIPPAGE)
+        nonReentrant
+        returns (uint256 assets)
+    {
+        if (shares == 0) revert ZeroShares();
+        assets = super.mint(shares, to);
+        if (assets > maxAssetsIn) revert TooMuchAssets();
    }
```
***


## 11. No function to remove a assets from allowlist

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L85

### Impact

Core.sol holds an `allowlistAssets` function which allows the owner/manager to allow assets that can later be used to create vaults. However, there are no functions to remove these assets from the allowlist if the asset gets compromised, or the protocol is no longer supporting the asset. 

```solidity
    function allowlistAssets(address[] memory assets, address[] memory slashingHandlers)
        external
        onlyRolesOrOwner(Constants.MANAGER_ROLE)
    {
        _self().allowlistAssets(assets, slashingHandlers);
        emit AllowlistedAssets(assets);
    }
```
### Recommended Mitigation Steps
Recommend introducing a function to remove assets from allowlists.

***

## 12. The two `deposit` functions should use the same pause mapping

Links to affected code *

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L81

https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L97

### Impact

The two `deposit` functions, one with slippage control, the other without, have different pause mappings.
Since they both have the same functionality, it makes more sense to control them with the same pause mapping, otherwise, pausing one can lead to users bypassing through the other.

```solidity

   function deposit(uint256 assets, address to)
        public
        override(ERC4626, IVault)
        whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT)
        nonReentrant
```
```solidity
    function deposit(uint256 assets, address to, uint256 minSharesOut)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT_SLIPPAGE)
        returns (uint256 shares)
    {
```
### Recommended Mitigation Steps

Recommend using the same pause map for the two functions.