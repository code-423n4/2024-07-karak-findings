# QA Report

Issues that are not included in Automated Findings.

## Summary

### Low Issues

Total **71 instances** over **12 issues**:

|ID|Issue|Instances|
|:--:|:---|:--:|
| [[L-01]](#l-01-array-length-is-not-checked-before-access-its-index) | Array length is not checked before access its index | 10 |
| [[L-02]](#l-02-variables-shadowing-other-definitions) | Variables shadowing other definitions | 6 |
| [[L-03]](#l-03-missing-zero-address-check-in-initializer) | Missing zero address check in initializer | 13 |
| [[L-04]](#l-04-written-only-contract-variables) | Written only contract variables | 1 |
| [[L-05]](#l-05-use-abiencodecall-instead-of-abiencodewithsignatureabiencodewithselector) | Use `abi.encodeCall()` instead of `abi.encodeWithSignature()`/`abi.encodeWithSelector()` | 8 |
| [[L-06]](#l-06-constructor--initialization-function-lacks-parameter-validation) | Constructor / initialization function lacks parameter validation | 9 |
| [[L-07]](#l-07-unsafe-solidity-low-level-call-can-cause-gas-grief-attack) | Unsafe solidity low-level call can cause gas grief attack | 1 |
| [[L-08]](#l-08-functions-calling-contractsaddresses-with-transfer-hooks-should-be-protected-by-reentrancy-guard) | Functions calling contracts/addresses with transfer hooks should be protected by reentrancy guard | 2 |
| [[L-09]](#l-09-critical-functions-should-be-controlled-by-time-locks) | Critical functions should be controlled by time locks | 8 |
| [[L-10]](#l-10-missing-contract-existence-checks-before-low-level-calls) | Missing contract existence checks before low-level calls | 1 |
| [[L-11]](#l-11-tokens-may-be-minted-to-the-zero-address) | Tokens may be minted to the zero address | 3 |
| [[L-12]](#l-12-code-does-not-follow-the-best-practice-of-check-effects-interaction) | Code does not follow the best practice of check-effects-interaction | 9 |

### Non Critical Issues

Total **449 instances** over **50 issues**:

|ID|Issue|Instances|
|:--:|:---|:--:|
| [[N-01]](#n-01-import-declarations-should-import-specific-identifiers-rather-than-the-whole-file) | Import declarations should import specific identifiers, rather than the whole file | 40 |
| [[N-02]](#n-02-visibility-of-state-variables-is-not-explicitly-defined) | Visibility of state variables is not explicitly defined | 1 |
| [[N-03]](#n-03-names-of-privateinternal-functions-should-be-prefixed-with-an-underscore) | Names of `private`/`internal` functions should be prefixed with an underscore | 49 |
| [[N-04]](#n-04-names-of-privateinternal-state-variables-should-be-prefixed-with-an-underscore) | Names of `private`/`internal` state variables should be prefixed with an underscore | 8 |
| [[N-05]](#n-05-the-nonreentrant-modifier-should-occur-before-all-other-modifiers) | The `nonReentrant` `modifier` should occur before all other modifiers | 11 |
| [[N-06]](#n-06-redundant-inheritance-specifier) | Redundant inheritance specifier | 3 |
| [[N-07]](#n-07-use-of-override-is-unnecessary) | Use of `override` is unnecessary | 13 |
| [[N-08]](#n-08-assembly-blocks-should-have-extensive-comments) | Assembly blocks should have extensive comments | 14 |
| [[N-09]](#n-09-consider-splitting-complex-checks-into-multiple-steps) | Consider splitting complex checks into multiple steps | 1 |
| [[N-10]](#n-10-complex-casting) | Complex casting | 1 |
| [[N-11]](#n-11-consider-adding-a-blockdeny-list) | Consider adding a block/deny-list | 2 |
| [[N-12]](#n-12-constantsimmutables-redefined-elsewhere) | Constants/Immutables redefined elsewhere | 9 |
| [[N-13]](#n-13-contract-name-does-not-match-its-filename) | Contract name does not match its filename | 3 |
| [[N-14]](#n-14-duplicate-imports) | Duplicate imports | 1 |
| [[N-15]](#n-15-consider-emitting-an-event-at-the-end-of-the-constructor) | Consider emitting an event at the end of the constructor | 7 |
| [[N-16]](#n-16-events-are-emitted-without-the-sender-information) | Events are emitted without the sender information | 11 |
| [[N-17]](#n-17-inconsistent-floating-version-pragma) | Inconsistent floating version pragma | 3 |
| [[N-18]](#n-18-function-state-mutability-can-be-restricted-to-pure) | Function state mutability can be restricted to pure | 21 |
| [[N-19]](#n-19-imports-could-be-organized-more-systematically) | Imports could be organized more systematically | 16 |
| [[N-20]](#n-20-contracts-containing-only-utility-functions-should-be-made-into-libraries) | Contracts containing only utility functions should be made into libraries | 6 |
| [[N-21]](#n-21-functions-should-be-named-in-mixedcase-style) | Functions should be named in mixedCase style | 24 |
| [[N-22]](#n-22-named-imports-of-parent-contracts-are-missing) | Named imports of parent contracts are missing | 1 |
| [[N-23]](#n-23-constants-should-be-put-on-the-left-side-of-comparisons) | Constants should be put on the left side of comparisons | 46 |
| [[N-24]](#n-24-else-block-not-required) | `else`-block not required | 1 |
| [[N-25]](#n-25-large-multiples-of-ten-should-use-scientific-notation) | Large multiples of ten should use scientific notation | 2 |
| [[N-26]](#n-26-todos-left-in-the-code) | `TODO`s left in the code | 1 |
| [[N-27]](#n-27-typos) | Typos | 4 |
| [[N-28]](#n-28-consider-bounding-input-array-length) | Consider bounding input array length | 4 |
| [[N-29]](#n-29-unnecessary-casting) | Unnecessary casting | 5 |
| [[N-30]](#n-30-unused-contract-variables) | Unused contract variables | 4 |
| [[N-31]](#n-31-unused-modifiers) | Unused modifiers | 1 |
| [[N-32]](#n-32-unused-named-return) | Unused named return | 4 |
| [[N-33]](#n-33-consider-using-delete-rather-than-assigning-zero-to-clear-values) | Consider using `delete` rather than assigning zero to clear values | 1 |
| [[N-34]](#n-34-use-the-latest-solidity-version) | Use the latest Solidity version | 3 |
| [[N-35]](#n-35-consider-using-named-function-arguments) | Consider using named function arguments | 2 |
| [[N-36]](#n-36-named-returns-are-recommended) | Named returns are recommended | 72 |
| [[N-37]](#n-37-use-a-struct-to-encapsulate-multiple-function-parameters) | Use a struct to encapsulate multiple function parameters | 3 |
| [[N-38]](#n-38-returning-a-struct-instead-of-a-bunch-of-variables-is-better) | Returning a struct instead of a bunch of variables is better | 1 |
| [[N-39]](#n-39-contract-variables-should-have-comments) | Contract variables should have comments | 1 |
| [[N-40]](#n-40-missing-event-when-a-state-variables-is-set-in-constructor) | Missing event when a state variables is set in constructor | 1 |
| [[N-41]](#n-41-empty-bytes-check-is-missing) | Empty bytes check is missing | 6 |
| [[N-42]](#n-42-dont-define-functions-with-the-same-name-in-a-contract) | Don't define functions with the same name in a contract | 3 |
| [[N-43]](#n-43-missing-event-for-critical-changes) | Missing event for critical changes | 1 |
| [[N-44]](#n-44-non-assembly-method-available) | Non-assembly method available | 2 |
| [[N-45]](#n-45-consider-adding-emergency-stop-functionality) | Consider adding emergency-stop functionality | 7 |
| [[N-46]](#n-46-avoid-the-use-of-sensitive-terms) | Avoid the use of sensitive terms | 1 |
| [[N-47]](#n-47-use-the-modern-upgradeable-contract-paradigm) | Use the Modern Upgradeable Contract Paradigm | 1 |
| [[N-48]](#n-48-large-or-complicated-code-bases-should-implement-invariant-tests) | Large or complicated code bases should implement invariant tests | 1 |
| [[N-49]](#n-49-the-default-value-is-manually-set-when-it-is-declared) | The default value is manually set when it is declared | 19 |
| [[N-50]](#n-50-contracts-should-have-all-publicexternal-functions-exposed-by-interfaces) | Contracts should have all `public`/`external` functions exposed by `interface`s | 7 |

## Low Issues

### [L-01] Array length is not checked before access its index

Accessing the elements of the array without checking or ensuring the validity of the access index in advance. It may result in an unexpected out-of-bounds error, or may result in missing elements when trying to traverse the entire array.

There are 10 instances:

- *BeaconProofsLib.sol* ( [138-138](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L138-L138), [142-142](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L142-L142), [146-146](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L146-L146), [150-150](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L150-L150) ):

```solidity
138:         return uint256(fromLittleEndianUint64(validatorFields[BALANCE_IDX])) * 1 gwei;

142:         return validatorFields[PUBKEY_IDX];

146:         return fromLittleEndianUint64(validatorFields[EXIT_EPOCH_IDX]);

150:         return validatorFields[WITHDRAWAL_CREDENTIALS_IDX];
```

- *CommonUtils.sol* ( [17-17](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L17-L17), [17-17](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L17-L17), [30-30](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L30-L30), [31-31](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L31-L31), [31-31](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L31-L31), [32-32](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L32-L32) ):

```solidity
17:             if (arr[i] <= arr[pivot]) {

17:             if (arr[i] <= arr[pivot]) {

30:         address temp = arr[left];

31:         arr[left] = arr[right];

31:         arr[left] = arr[right];

32:         arr[right] = temp;
```

### [L-02] Variables shadowing other definitions

A variable declaration shadowing any other existing definition is error-prone. It can cause confusion for developers, potentially introduce bugs, and reduce the readability and maintainability.

There are 6 instances:

- *NativeVault.sol* ( [529-529](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L529-L529), [571-571](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L571-L571), [581-581](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L581-L581) ):

```solidity
/// @audit Shadows `function owner()`
529:     modifier nodeExists(address owner) {

/// @audit Shadows `function owner()`
571:     function withdraw(uint256 assets, address to, address owner) public pure override returns (uint256 shares) {

/// @audit Shadows `function owner()`
581:     function redeem(uint256 shares, address to, address owner) public pure override returns (uint256 assets) {
```

- *SlashingHandler.sol* ( [33-33](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L33-L33) ):

```solidity
/// @audit Shadows `function owner()`
33:     function initialize(address owner, IERC20[] calldata _supportedAssets) external initializer {
```

- *Vault.sol* ( [331-331](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L331-L331), [348-348](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L348-L348) ):

```solidity
/// @audit Shadows `function owner()`
331:     function withdraw(uint256 assets, address to, address owner)

/// @audit Shadows `function owner()`
348:     function redeem(uint256 shares, address to, address owner)
```

### [L-03] Missing zero address check in initializer

Consider adding a zero address check for each address type parameter in initializer.

<details>
<summary>There are 13 instances (click to show):</summary>

- *Core.sol* ( [53-60](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L53-L60) ):

```solidity
/// @audit `_vaultImpl not checked`
/// @audit `_manager not checked`
/// @audit `_vetoCommittee not checked`
53:     function initialize(
54:         address _vaultImpl,
55:         address _manager,
56:         address _vetoCommittee,
57:         uint32 _hookCallGasLimit,
58:         uint32 _supportsInterfaceGasLimit,
59:         uint32 _hookGasBuffer
60:     ) external initializer {
```

- *NativeNode.sol* ( [28-28](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L28-L28) ):

```solidity
/// @audit `_owner not checked`
/// @audit `_nodeOwner not checked`
28:     function initialize(address _owner, address _nodeOwner) external initializer {
```

- *NativeVault.sol* ( [46-53](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L46-L53) ):

```solidity
/// @audit `_owner not checked`
/// @audit `_operator not checked`
46:     function initialize(
47:         address _owner,
48:         address _operator,
49:         address _depositToken,
50:         string memory _name,
51:         string memory _symbol,
52:         bytes memory _extraData
53:     ) external initializer {
```

- *SlashStore.sol* ( [20-20](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashStore.sol#L20-L20) ):

```solidity
/// @audit `_owner not checked`
20:     function initialize(address _owner) external initializer {
```

- *SlashingHandler.sol* ( [33-33](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L33-L33) ):

```solidity
/// @audit `owner not checked`
/// @audit `_supportedAssets not checked`
33:     function initialize(address owner, IERC20[] calldata _supportedAssets) external initializer {
```

- *Vault.sol* ( [51-58](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L51-L58) ):

```solidity
/// @audit `_owner not checked`
/// @audit `_operator not checked`
/// @audit `_depositToken not checked`
51:     function initialize(
52:         address _owner,
53:         address _operator,
54:         address _depositToken,
55:         string memory _name,
56:         string memory _symbol,
57:         bytes memory _extraData
58:     ) external initializer {
```

</details>

### [L-04] Written only contract variables

Write only contract variables are only written in the contract and are never read and cannot be accessed through an interface.
It is recommended to check if there is a bug causing the missing readings, or if some `view` interfaces should be provided. If not, consider removing them to improve code clarity, avoid confusion, and save gas.

There is 1 instance:

- *NativeNode.sol* ( [32-32](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L32-L32) ):

```solidity
32:         nodeOwner = _nodeOwner;
```

### [L-05] Use `abi.encodeCall()` instead of `abi.encodeWithSignature()`/`abi.encodeWithSelector()`

Function `abi.encodeCall()` provides [type-safe encode utility](https://github.com/OpenZeppelin/openzeppelin-contracts/issues/3693) comparing with `abi.encodeWithSignature()`/`abi.encodeWithSelector()`.

There are 8 instances:

- *HookLib.sol* ( [93-93](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L93-L93) ):

```solidity
93:             abi.encodeWithSelector(IERC165.supportsInterface.selector, interfaceId),
```

- *Operator.sol* ( [80-80](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L80-L80), [126-126](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L126-L126), [164-164](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L164-L164), [196-196](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L196-L196) ):

```solidity
80:             data: abi.encodeWithSelector(dss.requestUpdateStakeHook.selector, operator, requestStakeUpdate),

126:             data: abi.encodeWithSelector(dss.finishUpdateStakeHook.selector, msg.sender),

164:             data: abi.encodeWithSelector(dss.registrationHook.selector, operator, registrationHookData),

196:             data: abi.encodeWithSelector(dss.unregistrationHook.selector, operator, unregistrationHookData),
```

- *SlasherLib.sol* ( [115-115](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L115-L115), [144-144](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L144-L144), [161-161](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L161-L161) ):

```solidity
115:             data: abi.encodeWithSelector(

144:             data: abi.encodeWithSelector(dss.finishSlashingHook.selector, queuedSlashing.operator),

161:             data: abi.encodeWithSelector(dss.cancelSlashingHook.selector, queuedSlashing.operator),
```

### [L-06] Constructor / initialization function lacks parameter validation

Constructors and initialization functions play a critical role in contracts by setting important initial states when the contract is first deployed before the system starts. The parameters passed to the constructor and initialization functions directly affect the behavior of the contract / protocol. If incorrect parameters are provided, the system may fail to run, behave abnormally, be unstable, or lack security. Therefore, it's crucial to carefully check each parameter in the constructor and initialization functions. If an exception is found, the transaction should be rolled back.

<details>
<summary>There are 9 instances (click to show):</summary>

- *Core.sol* ( [53-60](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L53-L60) ):

```solidity
/// @audit `_hookCallGasLimit`
/// @audit `_supportsInterfaceGasLimit`
/// @audit `_hookGasBuffer`
53:     function initialize(
54:         address _vaultImpl,
55:         address _manager,
56:         address _vetoCommittee,
57:         uint32 _hookCallGasLimit,
58:         uint32 _supportsInterfaceGasLimit,
59:         uint32 _hookGasBuffer
60:     ) external initializer {
```

- *NativeVault.sol* ( [46-53](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L46-L53) ):

```solidity
/// @audit `_name`
/// @audit `_symbol`
/// @audit `_extraData`
46:     function initialize(
47:         address _owner,
48:         address _operator,
49:         address _depositToken,
50:         string memory _name,
51:         string memory _symbol,
52:         bytes memory _extraData
53:     ) external initializer {
```

- *Vault.sol* ( [51-58](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L51-L58) ):

```solidity
/// @audit `_name`
/// @audit `_symbol`
/// @audit `_extraData`
51:     function initialize(
52:         address _owner,
53:         address _operator,
54:         address _depositToken,
55:         string memory _name,
56:         string memory _symbol,
57:         bytes memory _extraData
58:     ) external initializer {
```

</details>

### [L-07] Unsafe solidity low-level call can cause gas grief attack

Using the low-level calls of a solidity address can leave the contract open to gas grief attacks. These attacks occur when the called contract returns a large amount of data.
So when calling an external contract, it is necessary to check the length of the return data before reading/copying it (using `returndatasize()`).

There is 1 instance:

- *NativeVault.sol* ( [416-416](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L416-L416) ):

```solidity
416:         (bool success, bytes memory result) = Constants.BEACON_ROOTS_ADDRESS.staticcall(abi.encode(timestamp));
```

### [L-08] Functions calling contracts/addresses with transfer hooks should be protected by reentrancy guard

Even if the function follows the best practice of check-effects-interaction, not using a reentrancy guard when there may be transfer hooks opens the users of this protocol up to [read-only reentrancy vulnerability](https://chainsecurity.com/curve-lp-oracle-manipulation-post-mortem/) with no way to protect them except by block-listing the entire protocol.

There are 2 instances:

- *SlashingHandler.sol* ( [56-56](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L56-L56), [58-58](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L58-L58) ):

```solidity
56:         SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount);

58:         SafeTransferLib.safeTransfer(address(token), address(0), amount);
```

### [L-09] Critical functions should be controlled by time locks

It is a good practice to give time for users to react and adjust to critical changes. A timelock provides more guarantees and reduces the level of trust required, thus decreasing risk for users. It also indicates that the project is legitimate (less risk of a malicious owner making a sandwich attack on a user).

<details>
<summary>There are 8 instances (click to show):</summary>

- *Core.sol* ( [173-173](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L173-L173), [183-183](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L183-L183), [197-197](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L197-L197) ):

```solidity
173:     function changeStandardImplementation(address newVaultImpl) external onlyOwner {

183:     function changeImplementationForVault(address vault, address newVaultImpl) external onlyOwner {

197:     function pauseVault(IKarakBaseVault vault, uint256 map) external onlyRolesOrOwner(Constants.MANAGER_ROLE) {
```

- *NativeNode.sol* ( [51-51](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L51-L51) ):

```solidity
51:     function pause(uint256 map) external onlyOwner {
```

- *NativeVault.sol* ( [83-87](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L83-L87), [334-334](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L334-L334) ):

```solidity
83:     function changeNodeImplementation(address newNodeImplementation)
84:         external
85:         onlyOwnerOrRoles(Constants.MANAGER_ROLE)
86:         whenFunctionNotPaused(Constants.PAUSE_NATIVEVAULT_NODE_IMPLEMENTATION)
87:     {

334:     function pauseNode(INativeNode node, uint256 map) external onlyRolesOrOwner(Constants.MANAGER_ROLE) {
```

- *SlashingHandler.sol* ( [43-43](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L43-L43) ):

```solidity
43:     function addSlashableToken(IERC20 token) external onlyOwner {
```

- *Vault.sol* ( [125-129](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L125-L129) ):

```solidity
125:     function startRedeem(uint256 shares, address beneficiary)
126:         external
127:         whenFunctionNotPaused(Constants.PAUSE_VAULT_START_REDEEM)
128:         nonReentrant
129:         returns (bytes32 withdrawalKey)
```

</details>

### [L-10] Missing contract existence checks before low-level calls

Low-level calls return success if there is no code present at the specified address. In addition to the zero-address checks, add a check to verify that `<address>.code.length > 0`.

There is 1 instance:

- *NativeVault.sol* ( [416-416](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L416-L416) ):

```solidity
416:         (bool success, bytes memory result) = Constants.BEACON_ROOTS_ADDRESS.staticcall(abi.encode(timestamp));
```

### [L-11] Tokens may be minted to the zero address

Neither the listed functions, nor `_mint()` prevent minting to `address(0x0)`

There are 3 instances:

- *NativeVault.sol* ( [501-501](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L501-L501), [562-562](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L562-L562) ):

```solidity
501:         _mint(_of, shares);

562:     function mint(uint256 shares, address to) public pure override returns (uint256 assets) {
```

- *Vault.sol* ( [110-115](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L110-L115) ):

```solidity
110:     function mint(uint256 shares, address to)
111:         public
112:         override(ERC4626, IVault)
113:         whenFunctionNotPaused(Constants.PAUSE_VAULT_MINT)
114:         nonReentrant
115:         returns (uint256 assets)
```

### [L-12] Code does not follow the best practice of check-effects-interaction

Code should follow the best-practice of [check-effects-interaction](https://blockchain-academy.hs-mittweida.de/courses/solidity-coding-beginners-to-intermediate/lessons/solidity-11-coding-patterns/topic/checks-effects-interactions/), where state variables are updated before any external calls are made. Doing so prevents a large class of reentrancy bugs.

<details>
<summary>There are 9 instances (click to show):</summary>

- *NativeVault.sol* ( [103-103](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L103-L103), [104-104](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L104-L104), [194-194](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L194-L194), [195-200](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L195-L200), [283-283](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L283-L283), [285-285](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L285-L285), [439-439](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L439-L439), [444-444](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L444-L444), [470-470](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L470-L470) ):

```solidity
/// @audit `deployNode()` is called on line 102, triggering an external call - `initialize()`
103:         self.ownerToNode[msg.sender].nodeAddress = newNodeAddr;

/// @audit `deployNode()` is called on line 102, triggering an external call - `initialize()`
104:         self.nodeToOwner[newNodeAddr] = msg.sender;

/// @audit `_getParentBlockRoot()` is called on line 191, triggering an external call - `staticcall()`
194:         for (uint256 i = 0; i < validatorFieldsProofs.length; i++) {

/// @audit `_getParentBlockRoot()` is called on line 191, triggering an external call - `staticcall()`
195:             totalRestakedWei += self.validateWithdrawalCredentials(
196:                 nodeOwner,
197:                 beaconStateRootProof.timestamp,
198:                 beaconStateRootProof.beaconStateRoot,
199:                 validatorFieldsProofs[i]
200:             );

/// @audit `withdraw()` is called on line 282
283:         node.withdrawableCreditedNodeETH -= startedWithdrawal.assets;

/// @audit `withdraw()` is called on line 282
285:         delete self.withdrawalMap[withdrawalKey];

/// @audit `withdraw()` is called on line 436
439:         node.totalRestakedETH -= slashedWithdrawable;

/// @audit `withdraw()` is called on line 436
444:             node.withdrawableCreditedNodeETH = node.nodeAddress.balance;

/// @audit `_transferToSlashStore()` is called on line 456, triggering an external call - `withdraw()`
470:         node.currentSnapshotTimestamp = uint64(block.timestamp);
```

</details>

## Non Critical Issues

### [N-01] Import declarations should import specific identifiers, rather than the whole file

Using import declarations of the form `import {<identifier_name>} from "some/file.sol"` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation (but does not save any gas).

<details>
<summary>There are 40 instances (click to show):</summary>

- *Core.sol* ( [22](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L22), [23](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L23), [24](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L24) ):

```solidity
22: import "./interfaces/Errors.sol";

23: import "./interfaces/Events.sol";

24: import "./interfaces/Constants.sol";
```

- *NativeNode.sol* ( [13](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L13), [14](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L14), [15](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L15) ):

```solidity
13: import "./interfaces/Errors.sol";

14: import "./interfaces/Events.sol";

15: import "./interfaces/Constants.sol";
```

- *NativeVault.sol* ( [21](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L21), [22](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L22), [23](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L23) ):

```solidity
21: import "./interfaces/Errors.sol";

22: import "./interfaces/Events.sol";

23: import "./interfaces/Constants.sol";
```

- *Querier.sol* ( [6](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L6), [7](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L7) ):

```solidity
6: import "./entities/CoreStorageSlots.sol";

7: import "./Core.sol";
```

- *SlashStore.sol* ( [10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashStore.sol#L10) ):

```solidity
10: import "./interfaces/Events.sol";
```

- *SlashingHandler.sol* ( [9](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L9), [10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L10) ):

```solidity
9: import "./interfaces/ISlashingHandler.sol";

10: import "./interfaces/Errors.sol";
```

- *Vault.sol* ( [23](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L23), [24](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L24) ):

```solidity
23: import "./interfaces/Errors.sol";

24: import "./interfaces/Events.sol";
```

- *BeaconProofsLib.sol* ( [4](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L4) ):

```solidity
4: import "../interfaces/Errors.sol";
```

- *CoreLib.sol* ( [12](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L12), [13](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L13), [14](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L14) ):

```solidity
12: import "../interfaces/Constants.sol";

13: import "../interfaces/Errors.sol";

14: import "../interfaces/Events.sol";
```

- *HookLib.sol* ( [5](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L5), [6](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L6), [7](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L7) ):

```solidity
5: import "@openzeppelin/contracts/interfaces/IERC165.sol";

6: import "../interfaces/Errors.sol";

7: import "../interfaces/Events.sol";
```

- *NativeVaultLib.sol* ( [6](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L6), [7](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L7), [8](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L8), [9](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L9), [10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L10), [12](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L12) ):

```solidity
6: import "./VaultLib.sol";

7: import "../interfaces/Errors.sol";

8: import "../interfaces/Events.sol";

9: import "../interfaces/Constants.sol";

10: import "../interfaces/INativeNode.sol";

12: import "../entities/BeaconProofsLib.sol";
```

- *Operator.sol* ( [10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L10), [11](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L11), [12](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L12) ):

```solidity
10: import "../interfaces/Errors.sol";

11: import "../interfaces/Constants.sol";

12: import "../interfaces/IDSS.sol";
```

- *SlasherLib.sol* ( [5](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L5), [12](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L12), [13](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L13), [14](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L14), [15](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L15), [16](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L16) ):

```solidity
5: import "@openzeppelin/contracts/utils/structs/EnumerableMap.sol";

12: import "../interfaces/Errors.sol";

13: import "../interfaces/Constants.sol";

14: import "../interfaces/IDSS.sol";

15: import "../interfaces/IKarakBaseVault.sol";

16: import "../interfaces/Events.sol";
```

- *VaultLib.sol* ( [5](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/VaultLib.sol#L5), [6](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/VaultLib.sol#L6) ):

```solidity
5: import "./Withdraw.sol";

6: import "../interfaces/Errors.sol";
```

</details>

### [N-02] Visibility of state variables is not explicitly defined

To avoid misunderstandings and unexpected state accesses, it is recommended to explicitly define the visibility of each state variable.

There is 1 instance:

- *NativeNode.sol* ( [18-18](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L18-L18) ):

```solidity
18:     address nodeOwner;
```

### [N-03] Names of `private`/`internal` functions should be prefixed with an underscore

It is recommended by the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#underscore-prefix-for-non-external-functions-and-variables).

<details>
<summary>There are 49 instances (click to show):</summary>

- *BeaconProofsLib.sol* ( [54-54](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L54-L54), [61-64](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L61-L64), [73-78](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L73-L78), [97-97](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L97-L97), [114-117](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L114-L117), [137-137](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L137-L137), [145-145](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L145-L145), [149-149](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L149-L149) ):

```solidity
54:     function fromLittleEndianUint64(bytes32 lenum) internal pure returns (uint64 n) {

61:     function validateBeaconStateRootProof(bytes32 beaconBlockRoot, BeaconStateRootProof calldata beaconStateRootProof)
62:         internal
63:         view
64:     {

73:     function validateValidatorProof(
74:         uint40 validatorIndex,
75:         bytes32[] calldata validatorFields,
76:         bytes calldata validatorProof,
77:         bytes32 beaconStateRoot
78:     ) internal view {

97:     function validateBalanceContainer(bytes32 beaconBlockRoot, BalanceContainer calldata proof) internal view {

114:     function validateBalance(bytes32 balanceRoot, uint40 validatorIndex, BalanceProof calldata proof)
115:         internal
116:         view
117:         returns (uint256 validatorBalanceWei)

137:     function getEffectiveBalanceWei(bytes32[] memory validatorFields) internal pure returns (uint256) {

145:     function getExitEpoch(bytes32[] memory validatorFields) internal pure returns (uint64) {

149:     function getWithdrawalCredentials(bytes32[] memory validatorFields) internal pure returns (bytes32) {
```

- *CoreLib.sol* ( [40-47](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L40-L47), [56-61](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L56-L61), [89-97](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L89-L97), [149-149](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L149-L149), [153-153](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L153-L153), [157-157](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L157-L157), [161-161](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L161-L161) ):

```solidity
40:     function init(
41:         Storage storage self,
42:         address _vaultImpl,
43:         address _vetoCommittee,
44:         uint32 _hookCallGasLimit,
45:         uint32 _supportsInterfaceGasLimit,
46:         uint32 _hookGasBuffer
47:     ) internal {

56:     function updateGasValues(
57:         Storage storage self,
58:         uint32 _hookCallGasLimit,
59:         uint32 _supportsInterfaceGasLimit,
60:         uint32 _hookGasBuffer
61:     ) internal {

89:     function createVault(
90:         Storage storage self,
91:         address operator,
92:         address depositToken,
93:         string memory name,
94:         string memory symbol,
95:         bytes memory extraData,
96:         address implementation
97:     ) internal returns (IKarakBaseVault) {

149:     function cloneVault(bytes32 salt) internal returns (IKarakBaseVault) {

153:     function allowlistVaultImpl(Storage storage self, address implementation) internal {

157:     function isVaultImplAllowlisted(Storage storage self, address implementation) internal view returns (bool) {

161:     function isDSSRegistered(Storage storage self, IDSS dss) internal view returns (bool) {
```

- *CoreStorageSlots.sol* ( [15-15](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreStorageSlots.sol#L15-L15), [19-19](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreStorageSlots.sol#L19-L19), [23-23](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreStorageSlots.sol#L23-L23) ):

```solidity
15:     function operatorStateMappingSlot() internal pure returns (bytes32) {

19:     function operatorStateSlot(address operator) internal pure returns (bytes32) {

23:     function pendingStakeUpdateMappingSlot(address operator) internal pure returns (bytes32) {
```

- *HookLib.sol* ( [16-18](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L16-L18), [48-54](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L48-L54), [78-86](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L78-L86) ):

```solidity
16:     function performLowLevelCallAndLimitReturnData(address target, bytes memory data, uint256 gasLimit)
17:         internal
18:         returns (bool success, bytes32 returnVal)

48:     function callHook(
49:         address target,
50:         bytes memory data,
51:         bool ignoreFailure,
52:         uint32 hookCallGasLimit,
53:         uint32 hookGasBuffer
54:     ) internal returns (bool) {

78:     function callHookIfInterfaceImplemented(
79:         IERC165 dss,
80:         bytes memory data,
81:         bytes4 interfaceId,
82:         bool ignoreFailure,
83:         uint32 hookCallGasLimit,
84:         uint32 supportsInterfaceGasLimit,
85:         uint32 hookGasBuffer
86:     ) internal returns (bool) {
```

- *MerkleProofs.sol* ( [29-32](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L29-L32), [48-51](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L48-L51), [85-88](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L85-L88), [103-106](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L103-L106), [141-141](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L141-L141) ):

```solidity
29:     function verifyInclusionKeccak(bytes memory proof, bytes32 root, bytes32 leaf, uint256 index)
30:         internal
31:         pure
32:         returns (bool)

48:     function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index)
49:         internal
50:         pure
51:         returns (bytes32)

85:     function verifyInclusionSha256(bytes memory proof, bytes32 root, bytes32 leaf, uint256 index)
86:         internal
87:         view
88:         returns (bool)

103:     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index)
104:         internal
105:         view
106:         returns (bytes32)

141:     function merkleizeSha256(bytes32[] memory leaves) internal pure returns (bytes32) {
```

- *NativeVaultLib.sol* ( [93-95](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L93-L95), [106-106](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L106-L106), [110-116](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L110-L116), [146-152](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L146-L152) ):

```solidity
93:     function deployNode(Storage storage self, VaultLib.Config storage config, address owner)
94:         internal
95:         returns (address)

106:     function calculateWithdrawKey(address nodeOwner, uint256 nodeOwnerNonce) internal pure returns (bytes32) {

110:     function validateSnapshotProof(
111:         Storage storage self,
112:         address nodeOwner,
113:         ValidatorDetails memory validatorDetails,
114:         bytes32 balanceRoot,
115:         BeaconProofs.BalanceProof calldata proof
116:     ) internal returns (int256 balanceDeltaWei) {

146:     function validateWithdrawalCredentials(
147:         Storage storage self,
148:         address nodeOwner,
149:         uint64 updateTimestamp,
150:         bytes32 beaconStateRoot,
151:         BeaconProofs.ValidatorFieldsProof calldata validatorFieldsProof
152:     ) internal returns (uint256) {
```

- *Operator.sol* ( [39-39](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L39-L39), [43-43](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L43-L43), [49-49](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L49-L49), [53-56](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L53-L56), [89-92](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L89-L92), [103-103](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L103-L103), [135-138](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L135-L138), [143-143](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L143-L143), [209-212](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L209-L212), [217-217](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L217-L217) ):

```solidity
39:     function getVaults(State storage operatorState) internal view returns (address[] memory) {

43:     function addVault(State storage operatorState, IKarakBaseVault vault) internal {

49:     function calculateRoot(QueuedStakeUpdate memory newStake) internal pure returns (bytes32) {

53:     function validateStakeUpdateRequest(State storage operatorState, StakeUpdateRequest memory stakeUpdate)
54:         internal
55:         view
56:     {

89:     function validateQueuedStakeUpdate(State storage operatorState, QueuedStakeUpdate memory queuedStakeUpdate)
90:         internal
91:         view
92:     {

103:     function updateVaultStakeInDSS(State storage operatorState, address vault, IDSS dss, bool toStake) internal {

135:     function isOperatorRegisteredToDSS(CoreLib.Storage storage self, address operator, IDSS dss)
136:         internal
137:         view
138:         returns (bool)

143:     function checkIfOperatorIsRegInRegDSS(CoreLib.Storage storage self, address operator, IDSS dss) internal view {

209:     function getDSSsOperatorIsRegisteredTo(CoreLib.Storage storage self, address operator)
210:         internal
211:         view
212:         returns (address[] memory dssAddresses)

217:     function isVaultStakeToDSS(State storage operatorState, IDSS dss, address vault) internal view returns (bool) {
```

- *SlasherLib.sol* ( [38-38](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L38-L38), [42-46](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L42-L46), [59-62](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L59-L62), [79-82](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L79-L82) ):

```solidity
38:     function calculateRoot(QueuedSlashing memory queuedSlashing) internal pure returns (bytes32 root) {

42:     function validateVaultsAndSlashPercentages(
43:         CoreLib.Storage storage self,
44:         SlashRequest memory slashingRequest,
45:         IDSS dss
46:     ) internal view {

59:     function validateRequestSlashingParams(CoreLib.Storage storage self, SlashRequest memory slashingRequest, IDSS dss)
60:         internal
61:         view
62:     {

79:     function fetchEarmarkedStakes(SlashRequest memory slashingMetadata)
80:         internal
81:         view
82:         returns (uint256[] memory earmarkedStakes)
```

- *VaultLib.sol* ( [24-27](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/VaultLib.sol#L24-L27) ):

```solidity
24:     function validateQueuedWithdrawal(State storage self, bytes32 withdrawalKey)
25:         internal
26:         view
27:         returns (WithdrawLib.QueuedWithdrawal memory qdWithdrawal)
```

- *Withdraw.sol* ( [12-12](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Withdraw.sol#L12-L12) ):

```solidity
12:     function calculateWithdrawKey(address staker, uint256 stakerNonce) internal pure returns (bytes32) {
```

- *CommonUtils.sol* ( [7-7](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L7-L7), [12-12](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L12-L12), [29-29](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L29-L29) ):

```solidity
7:     function sortArr(address[] memory arr) private pure {

12:     function sort(address[] memory arr, uint256 left, uint256 right) private pure {

29:     function swap(address[] memory arr, uint256 left, uint256 right) private pure {
```

</details>

### [N-04] Names of `private`/`internal` state variables should be prefixed with an underscore

It is recommended by the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.20/style-guide.html#underscore-prefix-for-non-external-functions-and-variables).

<details>
<summary>There are 8 instances (click to show):</summary>

- *Core.sol* ( [39-39](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L39-L39) ):

```solidity
39:     bytes32 internal constant STORAGE_SLOT = 0x13c729cff436dc8ac22d145f2c778f6a709d225083f39538cc5e2674f2f10700;
```

- *NativeNode.sol* ( [18-18](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L18-L18) ):

```solidity
18:     address nodeOwner;
```

- *NativeVault.sol* ( [29-29](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L29-L29), [32-32](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L32-L32) ):

```solidity
29:     bytes32 internal constant STATE_SLOT = 0x0e977c4f52771ae90b9a885786536a06e14de7815be95b6ed56cdea86f6fc300;

32:     bytes32 internal constant CONFIG_SLOT = 0xb6497276931248fe2cc1dc985a2850cccba81036959c83b89ec93582a1e00900;
```

- *SlashingHandler.sol* ( [20-20](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L20-L20) ):

```solidity
20:     bytes32 internal constant CONFIG_SLOT = 0x661dfff6e6cdad10b44554a6ab58129a188fa46a74caae866b07c414cb424500;
```

- *Vault.sol* ( [35-35](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L35-L35), [37-37](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L37-L37) ):

```solidity
35:     bytes32 internal constant STATE_SLOT = 0x5d654853f9da5c5c659891e7f7fc564033f2724663c32c175f373318f8e1e700;

37:     bytes32 internal constant CONFIG_SLOT = 0x22a8eb0cbcfbbbc874f794ecd9efdfeeecb09fe60d66cf9327db2eac8a1ff000;
```

- *Pauser.sol* ( [16-16](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L16-L16) ):

```solidity
16:     bytes32 private constant PAUSER_STORAGE_SLOT = 0x271441cddf42198c20456f920f5dac04f245854c82f280f2e59e7095958d0b00;
```

</details>

### [N-05] The `nonReentrant` `modifier` should occur before all other modifiers

This is a best-practice to protect against reentrancy in other modifiers

<details>
<summary>There are 11 instances (click to show):</summary>

- *Core.sol* ( [97-100](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L97-L100), [161-164](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L161-L164), [220-223](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L220-L223), [235-238](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L235-L238) ):

```solidity
97:     function registerOperatorToDSS(IDSS dss, bytes memory registrationHookData)
98:         external
99:         whenFunctionNotPaused(Constants.PAUSE_CORE_REGISTER_TO_DSS)
100:         nonReentrant

161:     function deployVaults(VaultLib.Config[] calldata vaultConfigs, address vaultImpl)
162:         external
163:         whenFunctionNotPaused(Constants.PAUSE_CORE_DEPLOY_VAULTS)
164:         nonReentrant

220:     function requestSlashing(SlasherLib.SlashRequest calldata slashingRequest)
221:         external
222:         whenFunctionNotPaused(Constants.PAUSE_CORE_REQUEST_SLASHING)
223:         nonReentrant

235:     function cancelSlashing(SlasherLib.QueuedSlashing memory queuedSlashing)
236:         external
237:         whenFunctionNotPaused(Constants.PAUSE_CORE_CANCEL_SLASHING)
238:         nonReentrant
```

- *NativeNode.sol* ( [39-42](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L39-L42) ):

```solidity
39:     function withdraw(address to, uint256 weiAmount)
40:         external
41:         onlyOwner
42:         nonReentrant
```

- *NativeVault.sol* ( [299-302](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L299-L302) ):

```solidity
299:     function slashAssets(uint256 totalAssetsToSlash, address slashingHandler)
300:         external
301:         onlyOwner
302:         nonReentrant
```

- *Vault.sol* ( [78-82](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L78-L82), [110-114](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L110-L114), [125-128](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L125-L128), [331-335](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L331-L335), [348-352](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L348-L352) ):

```solidity
78:     function deposit(uint256 assets, address to)
79:         public
80:         override(ERC4626, IVault)
81:         whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT)
82:         nonReentrant

110:     function mint(uint256 shares, address to)
111:         public
112:         override(ERC4626, IVault)
113:         whenFunctionNotPaused(Constants.PAUSE_VAULT_MINT)
114:         nonReentrant

125:     function startRedeem(uint256 shares, address beneficiary)
126:         external
127:         whenFunctionNotPaused(Constants.PAUSE_VAULT_START_REDEEM)
128:         nonReentrant

331:     function withdraw(uint256 assets, address to, address owner)
332:         public
333:         override
334:         whenFunctionNotPaused(Constants.PAUSE_VAULT_WITHDRAW)
335:         nonReentrant

348:     function redeem(uint256 shares, address to, address owner)
349:         public
350:         override
351:         whenFunctionNotPaused(Constants.PAUSE_VAULT_REDEEM)
352:         nonReentrant
```

</details>

### [N-06] Redundant inheritance specifier

The contracts below already extend the specified contract, so there is no need to list it in the inheritance list again.

There are 3 instances:

- *Core.sol* ( [26-26](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L26-L26) ):

```solidity
/// @audit Pauser already extends Initializable
26: contract Core is IBeacon, ICore, OwnableRoles, Initializable, ReentrancyGuard, Pauser, ExtSloads {
```

- *Vault.sol* ( [29-29](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L29-L29) ):

```solidity
/// @audit Pauser already extends Initializable
29: contract Vault is ERC4626, Initializable, Ownable, Pauser, ReentrancyGuard, ExtSloads, IVault {
```

- *Pauser.sol* ( [10-10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L10-L10) ):

```solidity
/// @audit ContextUpgradeable already extends Initializable
10: contract Pauser is Initializable, ContextUpgradeable {
```

### [N-07] Use of `override` is unnecessary

[Starting from Solidity 0.8.8](https://docs.soliditylang.org/en/v0.8.20/contracts.html#function-overriding), the override keyword is not required when overriding an interface function, except for the case where the function is defined in multiple bases.

<details>
<summary>There are 13 instances (click to show):</summary>

- *Core.sol* ( [308-308](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L308-L308) ):

```solidity
308:     function implementation() external view override returns (address) {
```

- *NativeVault.sol* ( [536-536](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L536-L536), [544-544](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L544-L544), [553-553](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L553-L553), [562-562](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L562-L562), [571-571](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L571-L571), [581-581](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L581-L581), [591-591](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L591-L591), [599-599](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L599-L599), [627-627](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L627-L627) ):

```solidity
536:     function transfer(address to, uint256 amount) public pure override returns (bool) {

544:     function transferFrom(address from, address to, uint256 amount) public pure override returns (bool) {

553:     function deposit(uint256 assets, address to) public pure override returns (uint256 shares) {

562:     function mint(uint256 shares, address to) public pure override returns (uint256 assets) {

571:     function withdraw(uint256 assets, address to, address owner) public pure override returns (uint256 shares) {

581:     function redeem(uint256 shares, address to, address owner) public pure override returns (uint256 assets) {

591:     function previewDeposit(uint256 assets) public pure override returns (uint256 shares) {

599:     function previewRedeem(uint256 shares) public pure override returns (uint256 assets) {

627:     function implementation() external view override returns (address) {
```

- *Vault.sol* ( [316-316](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L316-L316), [331-336](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L331-L336), [348-353](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L348-L353) ):

```solidity
316:     function _underlyingDecimals() internal view override returns (uint8) {

331:     function withdraw(uint256 assets, address to, address owner)
332:         public
333:         override
334:         whenFunctionNotPaused(Constants.PAUSE_VAULT_WITHDRAW)
335:         nonReentrant
336:         returns (uint256 shares)

348:     function redeem(uint256 shares, address to, address owner)
349:         public
350:         override
351:         whenFunctionNotPaused(Constants.PAUSE_VAULT_REDEEM)
352:         nonReentrant
353:         returns (uint256 assets)
```

</details>

### [N-08] Assembly blocks should have extensive comments

Assembly blocks take a lot more time to audit than normal Solidity code, and often have gotchas and side-effects that the Solidity versions of the same code do not. Consider adding more comments explaining what is being done in every step of the assembly code, and describe why assembly is being used instead of Solidity.

<details>
<summary>There are 14 instances (click to show):</summary>

- *Core.sol* ( [386-388](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L386-L388) ):

```solidity
386:         assembly {
387:             $.slot := STORAGE_SLOT
388:         }
```

- *NativeVault.sol* ( [404-406](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L404-L406), [410-412](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L410-L412) ):

```solidity
404:         assembly {
405:             $.slot := STATE_SLOT
406:         }

410:         assembly {
411:             $.slot := CONFIG_SLOT
412:         }
```

- *SlashingHandler.sol* ( [62-64](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L62-L64) ):

```solidity
62:         assembly {
63:             $.slot := CONFIG_SLOT
64:         }
```

- *Vault.sol* ( [298-300](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L298-L300), [304-306](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L304-L306), [310-313](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L310-L313) ):

```solidity
298:         assembly {
299:             $.slot := STATE_SLOT
300:         }

304:         assembly {
305:             $.slot := CONFIG_SLOT
306:         }

310:         assembly {
311:             $.slot := STATE_SLOT
312:             $$.slot := CONFIG_SLOT
313:         }
```

- *MerkleProofs.sol* ( [58-63](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L58-L63), [66-71](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L66-L71), [116-121](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L116-L121), [124-129](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L124-L129) ):

```solidity
58:                 assembly {
59:                     mstore(0x00, computedHash)
60:                     mstore(0x20, mload(add(proof, i)))
61:                     computedHash := keccak256(0x00, 0x40)
62:                     index := div(index, 2)
63:                 }

66:                 assembly {
67:                     mstore(0x00, mload(add(proof, i)))
68:                     mstore(0x20, computedHash)
69:                     computedHash := keccak256(0x00, 0x40)
70:                     index := div(index, 2)
71:                 }

116:                 assembly {
117:                     mstore(0x00, mload(computedHash))
118:                     mstore(0x20, mload(add(proof, i)))
119:                     if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) { revert(0, 0) }
120:                     index := div(index, 2)
121:                 }

124:                 assembly {
125:                     mstore(0x00, mload(add(proof, i)))
126:                     mstore(0x20, mload(computedHash))
127:                     if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) { revert(0, 0) }
128:                     index := div(index, 2)
129:                 }
```

- *Pauser.sol* ( [19-21](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L19-L21) ):

```solidity
19:         assembly {
20:             $.slot := PAUSER_STORAGE_SLOT
21:         }
```

- *CommonUtils.sol* ( [50-52](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L50-L52) ):

```solidity
50:         assembly {
51:             size := extcodesize(addr)
52:         }
```

- *ExtSloads.sol* ( [17-19](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/ExtSloads.sol#L17-L19) ):

```solidity
17:             assembly ("memory-safe") {
18:                 mstore(add(res, mul(i, 32)), sload(slot))
19:             }
```

</details>

### [N-09] Consider splitting complex checks into multiple steps

Assign the expression's parts to intermediate local variables, and check against those instead.

There is 1 instance:

- *NativeVault.sol* ( [62-62](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L62-L62) ):

```solidity
62:         if (manager == address(0) || slashStore == address(0) || nodeImplementation == address(0)) revert ZeroAddress();
```

### [N-10] Complex casting

Consider whether the number of casts is really necessary, or whether using a different type would be more appropriate. Alternatively, add comments to explain in detail why the casts are necessary, and any implicit reasons why the cast does not introduce an overflow.

There is 1 instance:

- *BeaconProofsLib.sol* ( [55-55](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L55-L55) ):

```solidity
55:         n = uint64(uint256(lenum >> 192));
```

### [N-11] Consider adding a block/deny-list

Doing so will significantly increase centralization, but will help to prevent hackers from using stolen tokens

There are 2 instances:

- *SlashingHandler.sol* ( [16](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L16) ):

```solidity
16: contract SlashingHandler is Initializable, Ownable, ISlashingHandler {
```

- *Vault.sol* ( [29](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L29) ):

```solidity
29: contract Vault is ERC4626, Initializable, Ownable, Pauser, ReentrancyGuard, ExtSloads, IVault {
```

### [N-12] Constants/Immutables redefined elsewhere

Consider defining in only one contract so that values cannot become out of sync when only one location is updated. A [cheap way](https://medium.com/coinmonks/gas-cost-of-solidity-library-functions-dbe0cedd4678) to store constants/immutables in a single location is to create an `internal constant` in a `library`. If the variable is a local cache of another contract's value, consider making the cache variable internal or private, which will require external users to query the contract with the source of truth, so that callers don't get out of sync.

<details>
<summary>There are 9 instances (click to show):</summary>

- *Core.sol* ( [36-36](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L36-L36) ):

```solidity
/// @audit Seen in src/Vault.sol#32
36:     string public constant VERSION = "2.0.0";
```

- *NativeVault.sol* ( [29-29](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L29-L29), [32-32](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L32-L32) ):

```solidity
/// @audit Seen in src/Vault.sol#35
29:     bytes32 internal constant STATE_SLOT = 0x0e977c4f52771ae90b9a885786536a06e14de7815be95b6ed56cdea86f6fc300;

/// @audit Seen in src/Vault.sol#37
32:     bytes32 internal constant CONFIG_SLOT = 0xb6497276931248fe2cc1dc985a2850cccba81036959c83b89ec93582a1e00900;
```

- *Querier.sol* ( [10-10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L10-L10) ):

```solidity
/// @audit Seen in src/SlashingHandler.sol#17
10:     string public constant VERSION = "2.0.0";
```

- *SlashingHandler.sol* ( [17-17](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L17-L17), [20-20](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L20-L20) ):

```solidity
/// @audit Seen in src/Core.sol#36
17:     string public constant VERSION = "2.0.0";

/// @audit Seen in src/Vault.sol#37
20:     bytes32 internal constant CONFIG_SLOT = 0x661dfff6e6cdad10b44554a6ab58129a188fa46a74caae866b07c414cb424500;
```

- *Vault.sol* ( [32-32](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L32-L32), [35-35](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L35-L35), [37-37](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L37-L37) ):

```solidity
/// @audit Seen in src/Core.sol#36
32:     string public constant VERSION = "2.0.0";

/// @audit Seen in src/NativeVault.sol#29
35:     bytes32 internal constant STATE_SLOT = 0x5d654853f9da5c5c659891e7f7fc564033f2724663c32c175f373318f8e1e700;

/// @audit Seen in src/NativeVault.sol#32
37:     bytes32 internal constant CONFIG_SLOT = 0x22a8eb0cbcfbbbc874f794ecd9efdfeeecb09fe60d66cf9327db2eac8a1ff000;
```

</details>

### [N-13] Contract name does not match its filename

According to the [Solidity Style Guide](https://docs.soliditylang.org/en/latest/style-guide.html#contract-and-library-names), contract and library names should also match their filenames.

There are 3 instances:

- *BeaconProofsLib.sol* ( [7](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L7) ):

```solidity
/// @audit Not match filename `BeaconProofsLib.sol`
7: library BeaconProofs {
```

- *MerkleProofs.sol* ( [20](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L20) ):

```solidity
/// @audit Not match filename `MerkleProofs.sol`
20: library Merkle {
```

- *Withdraw.sol* ( [4](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Withdraw.sol#L4) ):

```solidity
/// @audit Not match filename `Withdraw.sol`
4: library WithdrawLib {
```

### [N-14] Duplicate imports

Duplicate imports are redundant and should be avoided.

There is 1 instance:

- *Core.sol* ( [24-24](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L24-L24) ):

```solidity
/// @audit Duplicate import `Constants` on line 18 
24: import "./interfaces/Constants.sol";
```

### [N-15] Consider emitting an event at the end of the constructor

This will allow users to easily exactly pinpoint when and by whom a contract was constructed.

<details>
<summary>There are 7 instances (click to show):</summary>

- *Core.sol* ( [42-42](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L42-L42) ):

```solidity
42:     constructor() {
```

- *NativeNode.sol* ( [21-21](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L21-L21) ):

```solidity
21:     constructor() {
```

- *NativeVault.sol* ( [35-35](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L35-L35) ):

```solidity
35:     constructor() {
```

- *Querier.sol* ( [14-14](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L14-L14) ):

```solidity
14:     constructor(address coreAddress) {
```

- *SlashStore.sol* ( [14-14](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashStore.sol#L14-L14) ):

```solidity
14:     constructor() {
```

- *SlashingHandler.sol* ( [26-26](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L26-L26) ):

```solidity
26:     constructor() {
```

- *Vault.sol* ( [40-40](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L40-L40) ):

```solidity
40:     constructor() {
```

</details>

### [N-16] Events are emitted without the sender information

When an action is triggered based on a user's action, not being able to filter based on who triggered the action makes event processing a lot more cumbersome. Including the `msg.sender` the events of these types of action will make events much more useful to end users, especially when `msg.sender` is not `tx.origin`.

<details>
<summary>There are 11 instances (click to show):</summary>

- *Core.sol* ( [106-106](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L106-L106), [123-123](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L123-L123), [140-140](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L140-L140), [152-152](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L152-L152), [230-230](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L230-L230) ):

```solidity
106:         emit RegisteredOperatorToDSS(operator, address(dss));

123:         emit UnregisteredOperatorToDSS(operator, address(dss));

140:         emit RequestedStakeUpdate(updatedStake);

152:         emit FinishedStakeUpdate(queuedStake);

230:         emit RequestedSlashing(address(dss), slashingRequest);
```

- *NativeVault.sol* ( [255-255](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L255-L255), [287-293](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L287-L293) ):

```solidity
255:         emit StartedWithdraw(nodeOwner, _config().operator, withdrawalKey, weiAmount, to);

287:         emit FinishedWithdraw(
288:             startedWithdrawal.nodeOwner,
289:             startedWithdrawal.to,
290:             _config().operator,
291:             startedWithdrawal.assets,
292:             withdrawalKey
293:         );
```

- *Vault.sol* ( [148-148](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L148-L148), [180-187](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L180-L187), [204-204](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L204-L204) ):

```solidity
148:         emit StartedRedeem(staker, config.operator, shares, withdrawalKey, assets);

180:         emit FinishedRedeem(
181:             startedWithdrawal.staker,
182:             startedWithdrawal.beneficiary,
183:             config.operator,
184:             startedWithdrawal.shares,
185:             redeemableAssets,
186:             withdrawalKey
187:         );

204:         emit Slashed(transferAmount);
```

- *CoreLib.sol* ( [144-144](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L144-L144) ):

```solidity
144:             emit DeployedVault(operator, address(vault), vaultConfigs[i].asset);
```

</details>

### [N-17] Inconsistent floating version pragma

Source files are using different floating version syntax, this is prone to compilation errors, and is not conducive to the code reliability and maintainability.

There are 3 instances:

- *NativeVault.sol* ( [2](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L2) ):

```solidity
2: pragma solidity ^0.8.25;
```

- *MerkleProofs.sol* ( [4](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L4) ):

```solidity
4: pragma solidity ^0.8.0;
```

- *Pauser.sol* ( [3](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L3) ):

```solidity
3: pragma solidity ^0.8.21;
```

### [N-18] Function state mutability can be restricted to pure

Function state mutability can be restricted to pure

<details>
<summary>There are 21 instances (click to show):</summary>

- *NativeVault.sol* ( [355-355](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L355-L355), [359-359](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L359-L359), [363-366](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L363-L366), [371-371](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L371-L371), [607-607](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L607-L607), [611-611](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L611-L611), [615-615](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L615-L615), [619-619](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L619-L619), [623-623](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L623-L623), [627-627](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L627-L627) ):

```solidity
355:     function getNextWithdrawNonce(address nodeOwner) public view returns (uint256) {

359:     function isWithdrawalPending(address nodeOwner, uint256 withdrawNonce) public view returns (bool) {

363:     function getQueuedWithdrawal(address nodeOwner, uint256 withdrawNonce)
364:         public
365:         view
366:         returns (NativeVaultLib.QueuedWithdrawal memory)

371:     function getNodeOwner(address node) external view returns (address) {

607:     function totalAssets() public view override(ERC4626, IKarakBaseVault) returns (uint256) {

611:     function decimals() public view override(ERC4626, IKarakBaseVault) returns (uint8) {

615:     function asset() public view override(ERC4626, IKarakBaseVault) returns (address) {

619:     function name() public view override(ERC20, IKarakBaseVault) returns (string memory) {

623:     function symbol() public view override(ERC20, IKarakBaseVault) returns (string memory) {

627:     function implementation() external view override returns (address) {
```

- *Vault.sol* ( [222-222](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L222-L222), [227-227](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L227-L227), [232-232](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L232-L232), [243-243](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L243-L243), [250-250](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L250-L250), [258-261](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L258-L261), [277-277](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L277-L277), [316-316](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L316-L316) ):

```solidity
222:     function name() public view override(ERC20, IKarakBaseVault) returns (string memory) {

227:     function symbol() public view override(ERC20, IKarakBaseVault) returns (string memory) {

232:     function asset() public view override(ERC4626, IKarakBaseVault) returns (address) {

243:     function getNextWithdrawNonce(address staker) public view returns (uint256) {

250:     function isWithdrawalPending(address staker, uint256 _withdrawNonce) public view returns (bool) {

258:     function getQueuedWithdrawal(address staker, uint256 _withdrawNonce)
259:         public
260:         view
261:         returns (WithdrawLib.QueuedWithdrawal memory)

277:     function decimals() public view override(ERC4626, IKarakBaseVault) returns (uint8) {

316:     function _underlyingDecimals() internal view override returns (uint8) {
```

- *Pauser.sol* ( [54-54](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L54-L54), [58-58](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L58-L58), [63-63](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L63-L63) ):

```solidity
54:     function paused() public view virtual returns (bool) {

58:     function paused(uint8 index) public view virtual returns (bool) {

63:     function pausedMap() public view virtual returns (uint256) {
```

</details>

### [N-19] Imports could be organized more systematically

The contract's interface should be imported first, followed by each of the interfaces it uses, followed by all other files. The examples below do not follow this layout.

<details>
<summary>There are 16 instances (click to show):</summary>

- *Core.sol* ( [7-7](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L7-L7), [17-17](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L17-L17), [19-19](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L19-L19) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
7: import {IBeacon} from "@openzeppelin/contracts/proxy/beacon/IBeacon.sol";

/// @audit Out of order with the prev import️ ⬆
17: import {ICore, SlasherLib} from "./interfaces/ICore.sol";

/// @audit Out of order with the prev import️ ⬆
19: import {IKarakBaseVault} from "./interfaces/IKarakBaseVault.sol";
```

- *NativeNode.sol* ( [11-11](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L11-L11) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
11: import {INativeNode} from "./interfaces/INativeNode.sol";
```

- *NativeVault.sol* ( [10-10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L10-L10), [15-15](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L15-L15), [19-19](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L19-L19) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
10: import {IBeacon} from "@openzeppelin/contracts/proxy/beacon/IBeacon.sol";

/// @audit Out of order with the prev import️ ⬆
15: import {INativeNode} from "./interfaces/INativeNode.sol";

/// @audit Out of order with the prev import️ ⬆
19: import {IKarakBaseVault} from "./interfaces/IKarakBaseVault.sol";
```

- *SlashingHandler.sol* ( [7-7](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L7-L7) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
7: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";
```

- *Vault.sol* ( [10-10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L10-L10), [20-20](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L20-L20) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
10: import {IERC20} from "@openzeppelin/contracts/token/ERC20/IERC20.sol";

/// @audit Out of order with the prev import️ ⬆
20: import {IVault} from "./interfaces/IVault.sol";
```

- *CoreLib.sol* ( [10-10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L10-L10) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
10: import {IKarakBaseVault} from "../interfaces/IKarakBaseVault.sol";
```

- *HookLib.sol* ( [5-5](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L5-L5) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
5: import "@openzeppelin/contracts/interfaces/IERC165.sol";
```

- *NativeVaultLib.sol* ( [10-10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L10-L10) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
10: import "../interfaces/INativeNode.sol";
```

- *Operator.sol* ( [7-7](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L7-L7), [12-12](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L12-L12) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
7: import {IKarakBaseVault} from "../interfaces/IKarakBaseVault.sol";

/// @audit Out of order with the prev import️ ⬆
12: import "../interfaces/IDSS.sol";
```

- *SlasherLib.sol* ( [14-14](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L14-L14) ):

```solidity
/// @audit Out of order with the prev import️ ⬆
14: import "../interfaces/IDSS.sol";
```

</details>

### [N-20] Contracts containing only utility functions should be made into libraries

Consider using a `library` instead of a `contract` to provide utility functions.

<details>
<summary>There are 6 instances (click to show):</summary>

- *Core.sol* ( [26](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L26) ):

```solidity
26: contract Core is IBeacon, ICore, OwnableRoles, Initializable, ReentrancyGuard, Pauser, ExtSloads {
```

- *NativeVault.sol* ( [25](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L25) ):

```solidity
25: contract NativeVault is ERC4626, IBeacon, Pauser, INativeVault, OwnableRoles, ReentrancyGuard {
```

- *SlashingHandler.sol* ( [16](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L16) ):

```solidity
16: contract SlashingHandler is Initializable, Ownable, ISlashingHandler {
```

- *Vault.sol* ( [29](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L29) ):

```solidity
29: contract Vault is ERC4626, Initializable, Ownable, Pauser, ReentrancyGuard, ExtSloads, IVault {
```

- *Pauser.sol* ( [10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L10) ):

```solidity
10: contract Pauser is Initializable, ContextUpgradeable {
```

- *ExtSloads.sol* ( [4](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/ExtSloads.sol#L4) ):

```solidity
4: abstract contract ExtSloads {
```

</details>

### [N-21] Functions should be named in mixedCase style

As the [Solidity Style Guide](https://docs.soliditylang.org/en/v0.8.21/style-guide.html#function-names) suggests: functions should be named in mixedCase style.

<details>
<summary>There are 24 instances (click to show):</summary>

- *Core.sol* ( [97](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L97), [113](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L113), [130](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L130), [146](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L146), [262](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L262), [302](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L302), [339](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L339), [365](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L365) ):

```solidity
97:     function registerOperatorToDSS(IDSS dss, bytes memory registrationHookData)

113:     function unregisterOperatorFromDSS(IDSS dss, bytes memory unregistrationHookData)

130:     function requestUpdateVaultStakeInDSS(Operator.StakeUpdateRequest memory vaultStakeUpdateRequest)

146:     function finalizeUpdateVaultStakeInDSS(Operator.QueuedStakeUpdate memory queuedStake)

262:     function registerDSS(uint256 maxSlashablePercentageWad) external {

302:     function isOperatorRegisteredToDSS(address operator, IDSS dss) public view returns (bool) {

339:     function fetchVaultsStakedInDSS(address operator, IDSS dss) external view returns (address[] memory vaults) {

365:     function isDSSRegistered(IDSS dss) public view returns (bool) {
```

- *CoreLib.sol* ( [161](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L161) ):

```solidity
161:     function isDSSRegistered(Storage storage self, IDSS dss) internal view returns (bool) {
```

- *Operator.sol* ( [61](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L61), [103](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L103), [111](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L111), [135](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L135), [143](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L143), [150](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L150), [173](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L173), [181](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L181), [209](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L209), [217](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L217), [224](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L224) ):

```solidity
61:     function requestUpdateVaultStakeInDSS(

103:     function updateVaultStakeInDSS(State storage operatorState, address vault, IDSS dss, bool toStake) internal {

111:     function validateAndUpdateVaultStakeInDSS(CoreLib.Storage storage self, QueuedStakeUpdate memory queuedStakeUpdate)

135:     function isOperatorRegisteredToDSS(CoreLib.Storage storage self, address operator, IDSS dss)

143:     function checkIfOperatorIsRegInRegDSS(CoreLib.Storage storage self, address operator, IDSS dss) internal view {

150:     function registerOperatorToDSS(

173:     function getVaultsStakedToDSS(State storage operatorState, IDSS dss)

181:     function unregisterOperatorFromDSS(

209:     function getDSSsOperatorIsRegisteredTo(CoreLib.Storage storage self, address operator)

217:     function isVaultStakeToDSS(State storage operatorState, IDSS dss, address vault) internal view returns (bool) {

224:     function getDSSCountVaultStakedTo(CoreLib.Storage storage self, IKarakBaseVault vault)
```

- *Pauser.sol* ( [36](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L36), [40](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L40) ):

```solidity
36:     function __Pauser_init() internal onlyInitializing {

40:     function __Pauser_init_unchained() internal onlyInitializing {
```

- *SlasherLib.sol* ( [170](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L170), [174](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L174) ):

```solidity
170:     function getDSSMaxSlashablePercentageWad(CoreLib.Storage storage self, IDSS dss) public view returns (uint256) {

174:     function setDSSMaxSlashablePercentageWad(
```

</details>

### [N-22] Named imports of parent contracts are missing

Consider adding named imports for all parent contracts.

There is 1 instance:

- *SlashingHandler.sol* ( [16-16](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L16-L16) ):

```solidity
/// @audit ISlashingHandler
16: contract SlashingHandler is Initializable, Ownable, ISlashingHandler {
```

### [N-23] Constants should be put on the left side of comparisons

Putting constants on the left side of comparison statements is a best practice known as [Yoda conditions](https://en.wikipedia.org/wiki/Yoda_conditions).
Although solidity's static typing system prevents accidental assignments within conditionals, adopting this practice can improve code readability and consistency, especially when working across multiple languages.

<details>
<summary>There are 46 instances (click to show):</summary>

- *Core.sol* ( [174](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L174), [188](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L188), [212](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L212), [289](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L289) ):

```solidity
/// @audit put `address(0)` on the left
174:         if (newVaultImpl == address(0)) revert ZeroAddress();

/// @audit put `address(0)` on the left
188:         if (self.vaultToImplMap[vault] == address(0)) revert VaultNotAChildVault();

/// @audit put `address(0)` on the left
212:         if (vaultImpl == address(0)) revert ZeroAddress();

/// @audit put `address(0)` on the left
289:         return _self().assetSlashingHandlers[asset] != address(0);
```

- *NativeVault.sol* ( [62](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L62), [62](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L62), [62](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L62), [88](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L88), [140](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L140), [271](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L271), [451](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L451), [461](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L461), [481](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L481), [530](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L530) ):

```solidity
/// @audit put `address(0)` on the left
62:         if (manager == address(0) || slashStore == address(0) || nodeImplementation == address(0)) revert ZeroAddress();

/// @audit put `address(0)` on the left
62:         if (manager == address(0) || slashStore == address(0) || nodeImplementation == address(0)) revert ZeroAddress();

/// @audit put `address(0)` on the left
62:         if (manager == address(0) || slashStore == address(0) || nodeImplementation == address(0)) revert ZeroAddress();

/// @audit put `address(0)` on the left
88:         if (newNodeImplementation == address(0)) revert ZeroAddress();

/// @audit put `0` on the left
140:         if (node.currentSnapshotTimestamp == 0) revert NoActiveSnapshot();

/// @audit put `0` on the left
271:         if (startedWithdrawal.start == 0) revert WithdrawalNotFound();

/// @audit put `0` on the left
451:         if (node.currentSnapshotTimestamp != 0) revert PendingIncompleteSnapshot();

/// @audit put `0` on the left
461:         if (revertIfNoBalanceChange && nodeBalanceWei == 0) revert NoBalanceUpdateToSnapshot();

/// @audit put `0` on the left
481:         if (snapshot.remainingProofs == 0) {

/// @audit put `address(0)` on the left
530:         if (_state().ownerToNode[owner].nodeAddress == address(0)) revert NotNodeOwner();
```

- *Querier.sol* ( [35](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L35), [40](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L40) ):

```solidity
/// @audit put `bytes32(0)` on the left
35:             if (results[i] != bytes32(0)) count++;

/// @audit put `bytes32(0)` on the left
40:             if (results[i] != bytes32(0)) vaults[latestIndex++] = stakedVaults[i];
```

- *SlashingHandler.sol* ( [53](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L53) ):

```solidity
/// @audit put `0` on the left
53:         if (amount == 0) revert ZeroAmount();
```

- *Vault.sol* ( [85](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L85), [100](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L100), [117](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L117), [131](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L131), [132](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L132) ):

```solidity
/// @audit put `0` on the left
85:         if (assets == 0) revert ZeroAmount();

/// @audit put `0` on the left
100:         if (assets == 0) revert ZeroAmount();

/// @audit put `0` on the left
117:         if (shares == 0) revert ZeroShares();

/// @audit put `0` on the left
131:         if (shares == 0) revert ZeroShares();

/// @audit put `address(0)` on the left
132:         if (beneficiary == address(0)) revert ZeroAddress();
```

- *BeaconProofsLib.sol* ( [79](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L79) ):

```solidity
/// @audit put `NUM_FIELDS` on the left
79:         if (validatorFields.length != NUM_FIELDS) revert InvalidValidatorFieldsLength();
```

- *CoreLib.sol* ( [48](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L48), [48](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L48), [72](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L72), [72](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L72), [81](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L81), [85](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L85), [127](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L127), [162](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L162) ):

```solidity
/// @audit put `address(0)` on the left
48:         if (_vaultImpl == address(0) || _vetoCommittee == address(0)) {

/// @audit put `address(0)` on the left
48:         if (_vaultImpl == address(0) || _vetoCommittee == address(0)) {

/// @audit put `address(0)` on the left
72:             if (slashingHandlers[i] == address(0) || assets[i] == address(0)) revert ZeroAddress();

/// @audit put `address(0)` on the left
72:             if (slashingHandlers[i] == address(0) || assets[i] == address(0)) revert ZeroAddress();

/// @audit put `address(0)` on the left
81:         if (!(implementation == address(0) || isVaultImplAllowlisted(self, implementation))) {

/// @audit put `address(0)` on the left
85:             if (self.assetSlashingHandlers[vaultConfigs[i].asset] == address(0)) revert AssetNotAllowlisted();

/// @audit put `address(0)` on the left
127:         if (implementation == address(0)) {

/// @audit put `0` on the left
162:         return self.dssMaxSlashablePercentageWad[dss] != 0;
```

- *HookLib.sol* ( [97](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L97) ):

```solidity
/// @audit put `bytes32(0)` on the left
97:         if (!success || result == bytes32(0)) {
```

- *MerkleProofs.sol* ( [109](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L109), [153](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L153) ):

```solidity
/// @audit put `0` on the left
109:             proof.length != 0 && proof.length % 32 == 0,

/// @audit put `0` on the left
153:         while (numNodesInLayer != 0) {
```

- *NativeVaultLib.sol* ( [127](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L127) ):

```solidity
/// @audit put `0` on the left
127:         if (newBalanceWei == 0) {
```

- *Operator.sol* ( [44](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L44), [57](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L57), [190](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L190) ):

```solidity
/// @audit put `IKarakBaseVault(address(0))` on the left
44:         if (vault == IKarakBaseVault(address(0))) revert ZeroAddress();

/// @audit put `bytes32(0)` on the left
57:         if (operatorState.pendingStakeUpdates[stakeUpdate.vault] != bytes32(0)) revert PendingStakeUpdateRequest();

/// @audit put `0` on the left
190:         if (vaults.length != 0) revert AllVaultsNotUnstakedFromDSS();
```

- *Pauser.sol* ( [55](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L55) ):

```solidity
/// @audit put `0` on the left
55:         return (_getPauserStorage()._paused != 0);
```

- *SlasherLib.sol* ( [54](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L54), [74](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L74), [180](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L180), [181](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L181) ):

```solidity
/// @audit put `0` on the left
54:             if (slashingRequest.slashPercentagesWad[i] == 0) revert ZeroSlashPercentageWad();

/// @audit put `0` on the left
74:         if (slashingRequest.vaults.length == 0) revert EmptyArray();

/// @audit put `0` on the left
180:         if (currentSlashablePercentageWad != 0) revert DSSAlreadyRegistered();

/// @audit put `0` on the left
181:         if (dssMaxSlashablePercentageWad == 0) revert ZeroSlashPercentageWad();
```

- *VaultLib.sol* ( [31](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/VaultLib.sol#L31) ):

```solidity
/// @audit put `0` on the left
31:         if (qdWithdrawal.start == 0) {
```

- *CommonUtils.sol* ( [8](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L8), [41](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L41) ):

```solidity
/// @audit put `0` on the left
8:         if (arr.length == 0) return;

/// @audit put `0` on the left
41:         if (arr.length == 0) return false;
```

</details>

### [N-24] `else`-block not required

One level of nesting can be removed by not having an `else` block when the `if`-block always jumps at the end. For example:
```solidity
if (condition) {
    body1...
    return x;
} else {
    body2...
}
```
can be changed to:
```solidity
if (condition) {
    body1...
    return x;
}
body2...
```

There is 1 instance:

- *NativeVault.sol* ( [418-420](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L418-L420) ):

```solidity
418:         if (success && result.length > 0) {
419:             return abi.decode(result, (bytes32));
420:         } else {
```

### [N-25] Large multiples of ten should use scientific notation

Use a scientific notation rather than decimal literals (e.g. `1e6` instead of `1000000`), for better code readability.

There are 2 instances:

- *MerkleProofs.sol* ( [119-119](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L119-L119), [127-127](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L127-L127) ):

```solidity
/// @audit 2000 -> 2e3
119:                     if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) { revert(0, 0) }

/// @audit 2000 -> 2e3
127:                     if iszero(staticcall(sub(gas(), 2000), 2, 0x00, 0x40, computedHash, 0x20)) { revert(0, 0) }
```

### [N-26] `TODO`s left in the code

TODOs may signal that a feature is missing or not ready for audit, consider resolving the issue and removing the TODO comment

There is 1 instance:

- *NativeVault.sol* ( [235](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L235) ):

```solidity
235:         // TODO: make more recent snapshot compulsory
```

### [N-27] Typos

All typos should be corrected.

There are 4 instances:

- *Core.sol* ( [293](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L293) ):

```solidity
/// @audit implementaion
293:     /// @param vaultImpl address of the implementaion
```

- *NativeVault.sol* ( [454](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L454) ):

```solidity
/// @audit theoritically
454:         // This allows all the ETH that was theoritically slashed in `slashAssets` function call can be moved to a slash store,
```

- *Vault.sol* ( [50](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L50), [124](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L124) ):

```solidity
/// @audit implemetations
50:     /// @param _extraData Serialized bytes of extra data that different implemetations can use for their own purposes

/// @audit withdrawl
124:     /// @return withdrawalKey The ID of the withdrawl. This is variable is shared across everyone's withdrawal request in the vault
```

### [N-28] Consider bounding input array length

The functions below take in an unbounded array, and make function calls for entries in the array. While the function will revert if it eventually runs out of gas, it may be a nicer user experience to require() that the length of the array is below some reasonable maximum, so that the user doesn't have to use up a full transaction's gas only to see that the transaction reverts.

There are 4 instances:

- *NativeVault.sol* ( [144](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L144), [194](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L194) ):

```solidity
144:         for (uint256 i = 0; i < balanceProofs.length; i++) {

194:         for (uint256 i = 0; i < validatorFieldsProofs.length; i++) {
```

- *SlashingHandler.sol* ( [36](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L36) ):

```solidity
36:         for (uint256 i = 0; i < _supportedAssets.length; i++) {
```

- *ExtSloads.sol* ( [14](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/ExtSloads.sol#L14) ):

```solidity
14:         for (uint256 i; i < nSlots;) {
```

### [N-29] Unnecessary casting

Unnecessary castings can be removed.

There are 5 instances:

- *Vault.sol* ( [62-62](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L62-L62) ):

```solidity
/// @audit address(_depositToken)
62:         (bool decimalsSuccess, uint8 result) = _tryGetAssetDecimals(address(_depositToken));
```

- *CoreLib.sol* ( [104-104](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L104-L104), [150-150](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L150-L150) ):

```solidity
/// @audit address(expectedNewVaultAddr)
104:         self.vaultToImplMap[address(expectedNewVaultAddr)] = implementation;

/// @audit address(LibClone.deployDeterministicERC1967BeaconProxy(address(this), salt))
150:         return IKarakBaseVault(address(LibClone.deployDeterministicERC1967BeaconProxy(address(this), salt)));
```

- *NativeVaultLib.sol* ( [99-99](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L99-L99), [164-164](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L164-L164) ):

```solidity
/// @audit address(LibClone.deployDeterministicERC1967BeaconProxy(address(this), salt))
99:         INativeNode newNode = INativeNode(address(LibClone.deployDeterministicERC1967BeaconProxy(address(this), salt)));

/// @audit address(self.ownerToNode[nodeOwner].nodeAddress)
164:                 != bytes32(abi.encodePacked(bytes1(uint8(1)), bytes11(0), address(self.ownerToNode[nodeOwner].nodeAddress)))
```

### [N-30] Unused contract variables

The following state variables are defined but not used.
It is recommended to check the code for logical omissions that cause them not to be used. If it's determined that they are not needed anywhere, it's best to remove them from the codebase to improve code clarity and minimize confusion.

There are 4 instances:

- *Core.sol* ( [36-36](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L36-L36) ):

```solidity
36:     string public constant VERSION = "2.0.0";
```

- *Querier.sol* ( [10-10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L10-L10) ):

```solidity
10:     string public constant VERSION = "2.0.0";
```

- *SlashingHandler.sol* ( [17-17](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L17-L17) ):

```solidity
17:     string public constant VERSION = "2.0.0";
```

- *Vault.sol* ( [32-32](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L32-L32) ):

```solidity
32:     string public constant VERSION = "2.0.0";
```

### [N-31] Unused modifiers

The following `modifier`s are not used. It is recommended to check the code for logical omissions that cause them not to be used. If it's determined that they are not needed anywhere, it's best to remove them from the codebase to improve code clarity and minimize confusion.

There is 1 instance:

- *Pauser.sol* ( [44](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L44) ):

```solidity
44:     modifier whenNotPaused() {
```

### [N-32] Unused named return

Declaring named returns, but not using them, is confusing to the reader. Consider either completely removing them (by declaring just the type without a name), or remove the return statement and do a variable assignment. This would improve the readability of the code, and it may also help reduce regressions during future code refactors.

<details>
<summary>There are 4 instances (click to show):</summary>

- *Core.sol* ( [331-331](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L331-L331) ):

```solidity
/// @audit vaults
331:     function getOperatorVaults(address operator) external view returns (address[] memory vaults) {
```

- *NativeVault.sol* ( [299-304](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L299-L304) ):

```solidity
/// @audit transferAmount
299:     function slashAssets(uint256 totalAssetsToSlash, address slashingHandler)
300:         external
301:         onlyOwner
302:         nonReentrant
303:         whenFunctionNotPaused(Constants.PAUSE_NATIVEVAULT_SLASHER)
304:         returns (uint256 transferAmount)
```

- *Vault.sol* ( [78-83](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L78-L83) ):

```solidity
/// @audit shares
78:     function deposit(uint256 assets, address to)
79:         public
80:         override(ERC4626, IVault)
81:         whenFunctionNotPaused(Constants.PAUSE_VAULT_DEPOSIT)
82:         nonReentrant
83:         returns (uint256 shares)
```

- *BeaconProofsLib.sol* ( [114-117](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L114-L117) ):

```solidity
/// @audit validatorBalanceWei
114:     function validateBalance(bytes32 balanceRoot, uint40 validatorIndex, BalanceProof calldata proof)
115:         internal
116:         view
117:         returns (uint256 validatorBalanceWei)
```

</details>

### [N-33] Consider using `delete` rather than assigning zero to clear values

The `delete` keyword more closely matches the semantics of what is being done, and draws more attention to the changing of state, which may lead to a more thorough audit of its associated logic.

There is 1 instance:

- *Pauser.sol* ( [41-41](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L41-L41) ):

```solidity
41:         _getPauserStorage()._paused = 0;
```

### [N-34] Use the latest Solidity version

Upgrading to the [latest solidity version](https://github.com/ethereum/solc-js/tags) (0.8.19 for L2s) can optimize gas usage, take advantage of new features and improve overall contract efficiency. Where possible, based on compatibility requirements, it is recommended to use newer/latest solidity version to take advantage of the latest optimizations and features.

There are 3 instances:

- *MerkleProofs.sol* ( [4](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L4) ):

```solidity
4: pragma solidity ^0.8.0;
```

- *Pauser.sol* ( [3](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L3) ):

```solidity
3: pragma solidity ^0.8.21;
```

- *CommonUtils.sol* ( [2](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L2) ):

```solidity
2: pragma solidity ^0.8.21;
```

### [N-35] Consider using named function arguments

When calling functions with multiple arguments, consider using [named function parameters](https://docs.soliditylang.org/en/latest/control-structures.html#function-calls-with-named-parameters), rather than positional ones.

There are 2 instances:

- *Vault.sol* ( [146-146](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L146-L146) ):

```solidity
146:         this.transferFrom(msg.sender, address(this), shares);
```

- *CoreLib.sol* ( [107-107](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L107-L107) ):

```solidity
107:         vault.initialize(address(this), operator, depositToken, name, symbol, extraData);
```

### [N-36] Named returns are recommended

Using named returns makes the code more self-documenting, makes it easier to fill out NatSpec, and in some cases can save gas. The cases below are where there currently is at most one return statement, which is ideal for named returns.

<details>
<summary>There are 72 instances (click to show):</summary>

- *Core.sol* ( [288-288](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L288-L288), [294-294](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L294-L294), [302-302](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L302-L302), [308-308](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L308-L308), [318-318](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L318-L318), [365-365](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L365-L365) ):

```solidity
288:     function isAssetAllowlisted(address asset) public view returns (bool) {

294:     function isVaultImplAllowListed(address vaultImpl) public view returns (bool) {

302:     function isOperatorRegisteredToDSS(address operator, IDSS dss) public view returns (bool) {

308:     function implementation() external view override returns (address) {

318:     function implementation(address vault) public view returns (address) {

365:     function isDSSRegistered(IDSS dss) public view returns (bool) {
```

- *NativeVault.sol* ( [95-99](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L95-L99), [346-346](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L346-L346), [351-351](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L351-L351), [355-355](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L355-L355), [359-359](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L359-L359), [363-366](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L363-L366), [371-371](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L371-L371), [375-379](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L375-L379), [384-384](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L384-L384), [388-388](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L388-L388), [392-396](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L392-L396), [415-415](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L415-L415), [536-536](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L536-L536), [544-544](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L544-L544), [607-607](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L607-L607), [611-611](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L611-L611), [615-615](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L615-L615), [619-619](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L619-L619), [623-623](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L623-L623), [627-627](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L627-L627), [631-631](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L631-L631) ):

```solidity
95:     function createNode()
96:         external
97:         nonReentrant
98:         whenFunctionNotPaused(Constants.PAUSE_NATIVEVAULT_CREATE_NODE)
99:         returns (address)

346:     function withdrawableWei(address nodeOwner) public view nodeExists(nodeOwner) returns (uint256) {

351:     function activeValidatorCount(address nodeOwner) public view nodeExists(nodeOwner) returns (uint256) {

355:     function getNextWithdrawNonce(address nodeOwner) public view returns (uint256) {

359:     function isWithdrawalPending(address nodeOwner, uint256 withdrawNonce) public view returns (bool) {

363:     function getQueuedWithdrawal(address nodeOwner, uint256 withdrawNonce)
364:         public
365:         view
366:         returns (NativeVaultLib.QueuedWithdrawal memory)

371:     function getNodeOwner(address node) external view returns (address) {

375:     function getValidatorDetails(bytes32 pubKey, address nodeOwner)
376:         external
377:         view
378:         nodeExists(nodeOwner)
379:         returns (NativeVaultLib.ValidatorDetails memory)

384:     function lastSnapshotTimestamp(address nodeOwner) external view nodeExists(nodeOwner) returns (uint64) {

388:     function currentSnapshotTimestamp(address nodeOwner) external view nodeExists(nodeOwner) returns (uint64) {

392:     function currentSnapshot(address nodeOwner)
393:         external
394:         view
395:         nodeExists(nodeOwner)
396:         returns (NativeVaultLib.Snapshot memory)

415:     function _getParentBlockRoot(uint64 timestamp) internal view returns (bytes32) {

536:     function transfer(address to, uint256 amount) public pure override returns (bool) {

544:     function transferFrom(address from, address to, uint256 amount) public pure override returns (bool) {

607:     function totalAssets() public view override(ERC4626, IKarakBaseVault) returns (uint256) {

611:     function decimals() public view override(ERC4626, IKarakBaseVault) returns (uint8) {

615:     function asset() public view override(ERC4626, IKarakBaseVault) returns (address) {

619:     function name() public view override(ERC20, IKarakBaseVault) returns (string memory) {

623:     function symbol() public view override(ERC20, IKarakBaseVault) returns (string memory) {

627:     function implementation() external view override returns (address) {

631:     function vaultConfig() public pure returns (VaultLib.Config memory) {
```

- *Vault.sol* ( [222-222](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L222-L222), [227-227](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L227-L227), [232-232](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L232-L232), [237-237](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L237-L237), [243-243](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L243-L243), [250-250](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L250-L250), [258-261](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L258-L261), [267-267](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L267-L267), [272-272](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L272-L272), [277-277](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L277-L277), [316-316](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L316-L316) ):

```solidity
222:     function name() public view override(ERC20, IKarakBaseVault) returns (string memory) {

227:     function symbol() public view override(ERC20, IKarakBaseVault) returns (string memory) {

232:     function asset() public view override(ERC4626, IKarakBaseVault) returns (address) {

237:     function vaultConfig() public pure returns (VaultLib.Config memory) {

243:     function getNextWithdrawNonce(address staker) public view returns (uint256) {

250:     function isWithdrawalPending(address staker, uint256 _withdrawNonce) public view returns (bool) {

258:     function getQueuedWithdrawal(address staker, uint256 _withdrawNonce)
259:         public
260:         view
261:         returns (WithdrawLib.QueuedWithdrawal memory)

267:     function totalAssets() public view override(ERC4626, IKarakBaseVault) returns (uint256) {

272:     function owner() public view override(Ownable, IVault) returns (address) {

277:     function decimals() public view override(ERC4626, IKarakBaseVault) returns (uint8) {

316:     function _underlyingDecimals() internal view override returns (uint8) {
```

- *BeaconProofsLib.sol* ( [137-137](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L137-L137), [141-141](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L141-L141), [145-145](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L145-L145), [149-149](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L149-L149) ):

```solidity
137:     function getEffectiveBalanceWei(bytes32[] memory validatorFields) internal pure returns (uint256) {

141:     function getPubkeyHash(bytes32[] calldata validatorFields) external pure returns (bytes32) {

145:     function getExitEpoch(bytes32[] memory validatorFields) internal pure returns (uint64) {

149:     function getWithdrawalCredentials(bytes32[] memory validatorFields) internal pure returns (bytes32) {
```

- *CoreLib.sol* ( [89-97](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L89-L97), [118-123](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L118-L123), [149-149](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L149-L149), [157-157](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L157-L157), [161-161](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L161-L161) ):

```solidity
89:     function createVault(
90:         Storage storage self,
91:         address operator,
92:         address depositToken,
93:         string memory name,
94:         string memory symbol,
95:         bytes memory extraData,
96:         address implementation
97:     ) internal returns (IKarakBaseVault) {

118:     function deployVaults(
119:         Storage storage self,
120:         address operator,
121:         address implementation,
122:         VaultLib.Config[] calldata vaultConfigs
123:     ) external returns (IKarakBaseVault[] memory) {

149:     function cloneVault(bytes32 salt) internal returns (IKarakBaseVault) {

157:     function isVaultImplAllowlisted(Storage storage self, address implementation) internal view returns (bool) {

161:     function isDSSRegistered(Storage storage self, IDSS dss) internal view returns (bool) {
```

- *CoreStorageSlots.sol* ( [15-15](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreStorageSlots.sol#L15-L15), [19-19](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreStorageSlots.sol#L19-L19), [23-23](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreStorageSlots.sol#L23-L23), [27-27](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreStorageSlots.sol#L27-L27) ):

```solidity
15:     function operatorStateMappingSlot() internal pure returns (bytes32) {

19:     function operatorStateSlot(address operator) internal pure returns (bytes32) {

23:     function pendingStakeUpdateMappingSlot(address operator) internal pure returns (bytes32) {

27:     function vaultPendingStakeUpdateSlot(address operator, address vault) public pure returns (bytes32) {
```

- *HookLib.sol* ( [48-54](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L48-L54), [78-86](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L78-L86) ):

```solidity
48:     function callHook(
49:         address target,
50:         bytes memory data,
51:         bool ignoreFailure,
52:         uint32 hookCallGasLimit,
53:         uint32 hookGasBuffer
54:     ) internal returns (bool) {

78:     function callHookIfInterfaceImplemented(
79:         IERC165 dss,
80:         bytes memory data,
81:         bytes4 interfaceId,
82:         bool ignoreFailure,
83:         uint32 hookCallGasLimit,
84:         uint32 supportsInterfaceGasLimit,
85:         uint32 hookGasBuffer
86:     ) internal returns (bool) {
```

- *MerkleProofs.sol* ( [29-32](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L29-L32), [48-51](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L48-L51), [85-88](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L85-L88), [103-106](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L103-L106), [141-141](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L141-L141) ):

```solidity
29:     function verifyInclusionKeccak(bytes memory proof, bytes32 root, bytes32 leaf, uint256 index)
30:         internal
31:         pure
32:         returns (bool)

48:     function processInclusionProofKeccak(bytes memory proof, bytes32 leaf, uint256 index)
49:         internal
50:         pure
51:         returns (bytes32)

85:     function verifyInclusionSha256(bytes memory proof, bytes32 root, bytes32 leaf, uint256 index)
86:         internal
87:         view
88:         returns (bool)

103:     function processInclusionProofSha256(bytes memory proof, bytes32 leaf, uint256 index)
104:         internal
105:         view
106:         returns (bytes32)

141:     function merkleizeSha256(bytes32[] memory leaves) internal pure returns (bytes32) {
```

- *NativeVaultLib.sol* ( [93-95](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L93-L95), [106-106](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L106-L106), [146-152](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/NativeVaultLib.sol#L146-L152) ):

```solidity
93:     function deployNode(Storage storage self, VaultLib.Config storage config, address owner)
94:         internal
95:         returns (address)

106:     function calculateWithdrawKey(address nodeOwner, uint256 nodeOwnerNonce) internal pure returns (bytes32) {

146:     function validateWithdrawalCredentials(
147:         Storage storage self,
148:         address nodeOwner,
149:         uint64 updateTimestamp,
150:         bytes32 beaconStateRoot,
151:         BeaconProofs.ValidatorFieldsProof calldata validatorFieldsProof
152:     ) internal returns (uint256) {
```

- *Operator.sol* ( [39-39](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L39-L39), [49-49](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L49-L49), [135-138](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L135-L138), [217-217](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L217-L217) ):

```solidity
39:     function getVaults(State storage operatorState) internal view returns (address[] memory) {

49:     function calculateRoot(QueuedStakeUpdate memory newStake) internal pure returns (bytes32) {

135:     function isOperatorRegisteredToDSS(CoreLib.Storage storage self, address operator, IDSS dss)
136:         internal
137:         view
138:         returns (bool)

217:     function isVaultStakeToDSS(State storage operatorState, IDSS dss, address vault) internal view returns (bool) {
```

- *Pauser.sol* ( [54-54](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L54-L54), [58-58](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L58-L58), [63-63](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L63-L63) ):

```solidity
54:     function paused() public view virtual returns (bool) {

58:     function paused(uint8 index) public view virtual returns (bool) {

63:     function pausedMap() public view virtual returns (uint256) {
```

- *SlasherLib.sol* ( [170-170](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L170-L170) ):

```solidity
170:     function getDSSMaxSlashablePercentageWad(CoreLib.Storage storage self, IDSS dss) public view returns (uint256) {
```

- *Withdraw.sol* ( [12-12](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Withdraw.sol#L12-L12) ):

```solidity
12:     function calculateWithdrawKey(address staker, uint256 stakerNonce) internal pure returns (bytes32) {
```

- *CommonUtils.sol* ( [39-39](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L39-L39), [48-48](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L48-L48) ):

```solidity
39:     function hasDuplicates(address[] memory arr) external pure returns (bool) {

48:     function isSmartContract(address addr) external view returns (bool) {
```

</details>

### [N-37] Use a struct to encapsulate multiple function parameters

If a function has too many parameters, replacing them with a struct can improve code readability and maintainability, increase reusability, and reduce the likelihood of errors when passing the parameters.

<details>
<summary>There are 3 instances (click to show):</summary>

- *Core.sol* ( [53-60](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L53-L60) ):

```solidity
53:     function initialize(
54:         address _vaultImpl,
55:         address _manager,
56:         address _vetoCommittee,
57:         uint32 _hookCallGasLimit,
58:         uint32 _supportsInterfaceGasLimit,
59:         uint32 _hookGasBuffer
60:     ) external initializer {
```

- *NativeVault.sol* ( [46-53](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L46-L53) ):

```solidity
46:     function initialize(
47:         address _owner,
48:         address _operator,
49:         address _depositToken,
50:         string memory _name,
51:         string memory _symbol,
52:         bytes memory _extraData
53:     ) external initializer {
```

- *Vault.sol* ( [51-58](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L51-L58) ):

```solidity
51:     function initialize(
52:         address _owner,
53:         address _operator,
54:         address _depositToken,
55:         string memory _name,
56:         string memory _symbol,
57:         bytes memory _extraData
58:     ) external initializer {
```

</details>

### [N-38] Returning a struct instead of a bunch of variables is better

If a function returns [too many variables](https://docs.soliditylang.org/en/v0.8.21/contracts.html#returning-multiple-values), replacing them with a struct can improve code readability, maintainability and reusability.

There is 1 instance:

- *Vault.sol* ( [309-309](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L309-L309) ):

```solidity
309:     function _storage() internal pure returns (VaultLib.State storage $, VaultLib.Config storage $$) {
```

### [N-39] Contract variables should have comments

Consider adding some comments on non-public contract variables to explain what they are supposed to do. This will help for future code reviews.

There is 1 instance:

- *NativeNode.sol* ( [18-18](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L18-L18) ):

```solidity
18:     address nodeOwner;
```

### [N-40] Missing event when a state variables is set in constructor

The initial states set in a constructor are important and should be recorded in the event.

There is 1 instance:

- *Querier.sol* ( [15](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L15) ):

```solidity
15:         core = ICore(coreAddress);
```

### [N-41] Empty bytes check is missing

Passing empty bytes to a function can cause unexpected behavior, such as certain operations failing, producing incorrect results, or wasting gas. It is recommended to check that all byte parameters are not empty.

<details>
<summary>There are 6 instances (click to show):</summary>

- *Core.sol* ( [97-101](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L97-L101), [113-117](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L113-L117) ):

```solidity
/// @audit registrationHookData
97:     function registerOperatorToDSS(IDSS dss, bytes memory registrationHookData)
98:         external
99:         whenFunctionNotPaused(Constants.PAUSE_CORE_REGISTER_TO_DSS)
100:         nonReentrant
101:     {

/// @audit unregistrationHookData
113:     function unregisterOperatorFromDSS(IDSS dss, bytes memory unregistrationHookData)
114:         external
115:         nonReentrant
116:         whenFunctionNotPaused(Constants.PAUSE_CORE_UNREGISTER_FROM_DSS)
117:     {
```

- *NativeVault.sol* ( [46-53](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L46-L53) ):

```solidity
/// @audit _extraData
46:     function initialize(
47:         address _owner,
48:         address _operator,
49:         address _depositToken,
50:         string memory _name,
51:         string memory _symbol,
52:         bytes memory _extraData
53:     ) external initializer {
```

- *Vault.sol* ( [51-58](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L51-L58) ):

```solidity
/// @audit _extraData
51:     function initialize(
52:         address _owner,
53:         address _operator,
54:         address _depositToken,
55:         string memory _name,
56:         string memory _symbol,
57:         bytes memory _extraData
58:     ) external initializer {
```

- *Operator.sol* ( [150-155](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L150-L155), [181-186](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L181-L186) ):

```solidity
/// @audit registrationHookData
150:     function registerOperatorToDSS(
151:         CoreLib.Storage storage self,
152:         IDSS dss,
153:         address operator,
154:         bytes memory registrationHookData
155:     ) external {

/// @audit unregistrationHookData
181:     function unregisterOperatorFromDSS(
182:         CoreLib.Storage storage self,
183:         IDSS dss,
184:         address operator,
185:         bytes memory unregistrationHookData
186:     ) external {
```

</details>

### [N-42] Don't define functions with the same name in a contract

In Solidity, while function overriding allows for functions with the same name to coexist, it is advisable to avoid this practice to enhance code readability and maintainability. Having multiple functions with the same name, even with different parameters or in inherited contracts, can cause confusion and increase the likelihood of errors during development, testing, and debugging. Using distinct and descriptive function names not only clarifies the purpose and behavior of each function, but also helps prevent unintended function calls or incorrect overriding. By adopting a clear and consistent naming convention, developers can create more comprehensible and maintainable smart contracts.

There are 3 instances:

- *Core.sol* ( [318](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L318) ):

```solidity
/// @audit Different function with same name found on line 308
318:     function implementation(address vault) public view returns (address) {
```

- *Vault.sol* ( [94](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L94) ):

```solidity
/// @audit Different function with same name found on line 78
94:     function deposit(uint256 assets, address to, uint256 minSharesOut)
```

- *Pauser.sol* ( [58](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L58) ):

```solidity
/// @audit Different function with same name found on line 54
58:     function paused(uint8 index) public view virtual returns (bool) {
```

### [N-43] Missing event for critical changes

Events should be emitted when critical changes are made to the contracts.

There is 1 instance:

- *SlashingHandler.sol* ( [43-45](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L43-L45) ):

```solidity
43:     function addSlashableToken(IERC20 token) external onlyOwner {
44:         _config().supportedAssets[token] = true;
45:     }
```

### [N-44] Non-assembly method available

There are some automated tools that will flag a project as having higher complexity if there is inline-assembly, so it's best to avoid using it where it's not necessary. In addition, most assembly methods can be replaced by non-assembly methods, for example:
- `assembly{ g := gas() }` => `uint256 g = gasleft()`
- `assembly{ id := chainid() }` => `uint256 id = block.chainid`
- `assembly { r := mulmod(a, b, d) }` => `uint256 m = mulmod(x, y, k)`
- `assembly { size := extcodesize() }` => `uint256 size = address(a).code.length`
- etc.

There are 2 instances:

- *HookLib.sol* ( [27-36](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/HookLib.sol#L27-L36) ):

```solidity
27:             success :=
28:                 call(
29:                     gasLimit, // gas available to the inner call
30:                     target, // address of contract being called
31:                     0, // ETH (denominated in WEI) being transferred in this call
32:                     add(data, 0x20), // Pointer to actual data (i.e. 32 bytes offset from `data`)
33:                     mload(data), // Size of actual data (i.e. the value stored in the first 32 bytes at `data`)
34:                     returnData, // Free pointer as a buffer for the inner call to write the return value
35:                     32 // 32 bytes size limit for the return value
36:                 )
```

- *CommonUtils.sol* ( [51-51](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L51-L51) ):

```solidity
51:             size := extcodesize(addr)
```

### [N-45] Consider adding emergency-stop functionality

Adding a way to quickly halt protocol functionality in an emergency, rather than having to pause individual contracts one-by-one, will make in-progress hack mitigation faster and much less stressful.

<details>
<summary>There are 7 instances (click to show):</summary>

- *Core.sol* ( [26-26](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L26-L26) ):

```solidity
26: contract Core is IBeacon, ICore, OwnableRoles, Initializable, ReentrancyGuard, Pauser, ExtSloads {
```

- *NativeNode.sol* ( [17-17](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeNode.sol#L17-L17) ):

```solidity
17: contract NativeNode is Pauser, Ownable, INativeNode, ReentrancyGuard {
```

- *NativeVault.sol* ( [25-25](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L25-L25) ):

```solidity
25: contract NativeVault is ERC4626, IBeacon, Pauser, INativeVault, OwnableRoles, ReentrancyGuard {
```

- *SlashStore.sol* ( [12-12](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashStore.sol#L12-L12) ):

```solidity
12: contract SlashStore is Initializable, Ownable, ReentrancyGuard {
```

- *SlashingHandler.sol* ( [16-16](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L16-L16) ):

```solidity
16: contract SlashingHandler is Initializable, Ownable, ISlashingHandler {
```

- *Vault.sol* ( [29-29](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L29-L29) ):

```solidity
29: contract Vault is ERC4626, Initializable, Ownable, Pauser, ReentrancyGuard, ExtSloads, IVault {
```

- *Pauser.sol* ( [10-10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L10-L10) ):

```solidity
10: contract Pauser is Initializable, ContextUpgradeable {
```

</details>

### [N-46] Avoid the use of sensitive terms

Use [alternative variants](https://www.zdnet.com/article/mysql-drops-master-slave-and-blacklist-whitelist-terminology/), e.g. allowlist/denylist instead of whitelist/blacklist.

There is 1 instance:

- *Pauser.sol* ( [8](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L8) ):

```solidity
8: /// @dev Pauser contract that modifies https://github.com/OpenZeppelin/openzeppelin-contracts-upgradeable/blob/master/contracts/utils/PausableUpgradeable.sol
```

### [N-47] Use the Modern Upgradeable Contract Paradigm

Modern smart contract development often employs upgradeable contract structures, utilizing proxy patterns like OpenZeppelin’s Upgradeable Contracts. This paradigm separates logic and state, allowing developers to amend and enhance the contract's functionality without altering its state or the deployed contract address. Transitioning to this approach enhances long-term maintainability.
Resolution: Adopt a well-established proxy pattern for upgradeability, ensuring proper initialization and employing transparent proxies to mitigate potential risks. Embrace comprehensive testing and audit practices, particularly when updating contract logic, to ensure state consistency and security are preserved across upgrades. This ensures your contract remains robust and adaptable to future requirements.

There is 1 instance:

- *Querier.sol* ( [9](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L9) ):

```solidity
9: contract Querier {
```

### [N-48] Large or complicated code bases should implement invariant tests

This includes: large code bases, or code with lots of inline-assembly, complicated math, or complicated interactions between multiple contracts.
Invariant fuzzers such as Echidna require the test writer to come up with invariants which should not be violated under any circumstances, and the fuzzer tests various inputs and function calls to ensure that the invariants always hold.
Even code with 100% code coverage can still have bugs due to the order of the operations a user performs, and invariant fuzzers may help significantly.

There is 1 instance:

- Global finding

### [N-49] The default value is manually set when it is declared

In instances where a new variable is defined, there is no need to set it to it's default value.

<details>
<summary>There are 19 instances (click to show):</summary>

- *NativeVault.sol* ( [144-144](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L144-L144), [194-194](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L194-L194) ):

```solidity
144:         for (uint256 i = 0; i < balanceProofs.length; i++) {

194:         for (uint256 i = 0; i < validatorFieldsProofs.length; i++) {
```

- *Querier.sol* ( [29-29](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L29-L29), [33-33](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L33-L33), [34-34](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L34-L34), [38-38](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L38-L38), [39-39](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L39-L39) ):

```solidity
29:         for (uint256 i = 0; i < stakedVaults.length; i++) {

33:         uint256 count = 0;

34:         for (uint256 i = 0; i < results.length; i++) {

38:         uint256 latestIndex = 0;

39:         for (uint256 i = 0; i < results.length; i++) {
```

- *SlashingHandler.sol* ( [36-36](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L36-L36) ):

```solidity
36:         for (uint256 i = 0; i < _supportedAssets.length; i++) {
```

- *BeaconProofsLib.sol* ( [14-14](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/BeaconProofsLib.sol#L14-L14) ):

```solidity
14:     uint256 internal constant PUBKEY_IDX = 0;
```

- *CoreLib.sol* ( [71-71](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L71-L71), [84-84](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L84-L84), [132-132](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/CoreLib.sol#L132-L132) ):

```solidity
71:         for (uint256 i = 0; i < assets.length; i++) {

84:         for (uint256 i = 0; i < vaultConfigs.length; i++) {

132:         for (uint256 i = 0; i < vaultConfigs.length; i++) {
```

- *MerkleProofs.sol* ( [147-147](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L147-L147), [155-155](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/MerkleProofs.sol#L155-L155) ):

```solidity
147:         for (uint256 i = 0; i < numNodesInLayer; i++) {

155:             for (uint256 i = 0; i < numNodesInLayer; i++) {
```

- *Operator.sol* ( [232-232](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Operator.sol#L232-L232) ):

```solidity
232:         for (uint256 i = 0; i < dssAddresses.length; i++) {
```

- *SlasherLib.sol* ( [50-50](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L50-L50), [85-85](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L85-L85), [134-134](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L134-L134) ):

```solidity
50:         for (uint256 i = 0; i < slashingRequest.vaults.length; i++) {

85:         for (uint256 i = 0; i < slashingMetadata.vaults.length; ++i) {

134:         for (uint256 i = 0; i < queuedSlashing.vaults.length; i++) {
```

- *CommonUtils.sol* ( [42-42](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/utils/CommonUtils.sol#L42-L42) ):

```solidity
42:         for (uint256 i = 0; i < arr.length - 1; i++) {
```

</details>

### [N-50] Contracts should have all `public`/`external` functions exposed by `interface`s

All `external`/`public` functions should extend an `interface`. This is useful to ensure that the whole API is extracted and can be more easily integrated by other projects.

<details>
<summary>There are 7 instances (click to show):</summary>

- *Core.sol* ( [26](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L26) ):

```solidity
26: contract Core is IBeacon, ICore, OwnableRoles, Initializable, ReentrancyGuard, Pauser, ExtSloads {
```

- *NativeVault.sol* ( [25](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L25) ):

```solidity
25: contract NativeVault is ERC4626, IBeacon, Pauser, INativeVault, OwnableRoles, ReentrancyGuard {
```

- *Querier.sol* ( [9](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Querier.sol#L9) ):

```solidity
9: contract Querier {
```

- *SlashStore.sol* ( [12](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashStore.sol#L12) ):

```solidity
12: contract SlashStore is Initializable, Ownable, ReentrancyGuard {
```

- *SlashingHandler.sol* ( [16](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L16) ):

```solidity
16: contract SlashingHandler is Initializable, Ownable, ISlashingHandler {
```

- *Vault.sol* ( [29](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L29) ):

```solidity
29: contract Vault is ERC4626, Initializable, Ownable, Pauser, ReentrancyGuard, ExtSloads, IVault {
```

- *Pauser.sol* ( [10](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/Pauser.sol#L10) ):

```solidity
10: contract Pauser is Initializable, ContextUpgradeable {
```

</details>
