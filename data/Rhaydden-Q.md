# QA for Karak Restaking

| Issue ID | Description |
| -------- | ----------- |
| [QA-01](#qa-01-lack-of-validator-status-check-when-decrementing-active-validator-count-in-nativevaultlib) | Lack of validator status check when decrementing active validator count in `NativeVaultLib` |
| [QA-02](#qa-02-soladys-safetransferlib-does-not-check-for-token-contracts-existence-which-opens-up-possibility-for-a-honeypot-attack) | Solady's `SafeTransferLib` does not check for token contract's existence, which opens up possibility for a honeypot attack |
| [QA-03](#qa-03-incorrect-pauseunpause-logic-in-pauser-contract-leading-to-unintended-state-changes-which-contradicts-the-readme) | Incorrect `Pause/Unpause` logic in pauser contract leading to unintended state changes which contradicts the `ReadMe` |
| [QA-04](#qa-04-hasduplicates-function-modifies-input-array-contrary-to-intent) | `hasDuplicates` function modifies input array contrary to intent |
| [QA-05](#qa-05-ambiguous-handling-of-supports-interface-in-hooklib-may-cause-unintended-dss-hook-executions-or-omissions) | Ambiguous handling of supports interface in `HookLib` may cause unintended DSS hook executions or omissions |
| [QA-06](#qa-06-weth-compatibility-issue-on-blast-chain) | WETH compatibility issue on Blast chain |
| [QA-07](#qa-07-missing-logic-for-updating-nextslashabletimestamp-impacts-slashing-mechanism) | Missing logic for updating `nextSlashableTimestamp` impacts slashing mechanism |
| [QA-08](#qa-08-lack-of-beacon-state-root-validation-in-validatewithdrawalcredentials-function-allows-acceptance-of-outdated-or-manipulated-proofs) | Lack of beacon state root validation in `validateWithdrawalCredentials` function allows acceptance of outdated or manipulated proofs |
| [QA-09](#qa-09-logic-issue-in-sort-function-of-commonutils-library) | Logic issue in `sort` function of `CommonUtils` library |
| [QA-10](#qa-10-premature-dss-removal-in-unregisteroperatorfromdss-function-potentially-breaks-hook-functionality) | Premature DSS removal in `unregisterOperatorFromDSS` function potentially breaks hook functionality |
| [QA-11](#qa-11-lack-of-slippage-protection-in-redeem-process-exposes-users-to-potential-value-loss) | Lack of slippage protection in redeem process exposes users to potential value loss |
| [QA-12](#qa-12-initial-griefing-attack-possible-in-vault-allowing-attacker-to-lock-victims-funds) | Initial griefing attack possible in Vault, allowing attacker to lock victim's funds |
| [QA-13](#qa-13-premature-implementation-mapping-in-createvault-creates-room-for-potential-front-running-attack) | Premature implementation mapping in `createVault` creates room for potential front-running attack |
| [QA-14](#qa-14-uniform-token-slashing-mechanism-using-address0-transfer-may-fail-for--some-erc20-tokens-which-could-lead-to-dos) | Uniform token slashing mechanism using `address(0)` transfer may fail for  some ERC20 tokens which could lead to DOS |
| [QA-15](#qa-15-insufficient-funds-for-slashing-due-to-time-delay-could-render-slashing-mechanism-ineffective) | Insufficient funds for slashing due to time delay could render slashing mechanism ineffective |





## [QA-01] Lack of validator status check when decrementing active validator count in `NativeVaultLib`

#### Impact

The `validateSnapshotProof` function could cause inaccurate accounting of active validators. While the current implementation is protected by checks in the `validateWithdrawalCredentials` function, the lack of explicit status checking when decrementing the `activeValidatorCount` could potentially cause issues if the contract logic is modified in the future or if there are edge cases not accounted for in the current implementation.

#### Proof of Concept

In the [`validateSnapshotProof` function:](https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/NativeVaultLib.sol#L110-L144)

```solidity
function validateSnapshotProof(
    Storage storage self,
    address nodeOwner,
    ValidatorDetails memory validatorDetails,
    bytes32 balanceRoot,
    BeaconProofs.BalanceProof calldata proof
) internal returns (int256 balanceDeltaWei) {
    // ... (other code)

    if (newBalanceWei == 0) {
        self.ownerToNode[nodeOwner].activeValidatorCount--;
        validatorDetails.status = ValidatorStatus.WITHDRAWN;

        emit ValidatorWithdrawn(nodeOwner, nodeAddress, timestamp, validatorIndex);
    }

    // ... (other code)
}
```

The `activeValidatorCount` is decremented whenever a validator's balance becomes zero, without explicitly checking if the validator was previously in an ACTIVE state.

#### Recommended Mitigation Steps

Add an explicit check for the validator's status before decrementing the `activeValidatorCount`:

```diff
 function validateSnapshotProof(
     Storage storage self,
     address nodeOwner,
     ValidatorDetails memory validatorDetails,
     bytes32 balanceRoot,
     BeaconProofs.BalanceProof calldata proof
 ) internal returns (int256 balanceDeltaWei) {
     // ... (other code)

     if (newBalanceWei == 0) {
-        self.ownerToNode[nodeOwner].activeValidatorCount--;
+        if (validatorDetails.status == ValidatorStatus.ACTIVE) {
+            self.ownerToNode[nodeOwner].activeValidatorCount--;
+        }
         validatorDetails.status = ValidatorStatus.WITHDRAWN;

         emit ValidatorWithdrawn(nodeOwner, nodeAddress, timestamp, validatorIndex);
     }

     // ... (other code)
 }
```




## [QA-02] Solady's `SafeTransferLib` does not check for token contract's existence, which opens up possibility for a honeypot attack

#### Proof of Concept
Protocol heavily uses Solady's SafeTransferLib accross in-scope contracts, this can easily be confirmed by using this search command: https://github.com/search?q=repo%3Acode-423n4%2F2024-07-karak%20import%20%7BSafeTransferLib%7D%20from%20%22solady%2Fsrc%2Futils%2FSafeTransferLib.sol%22%3B&type=code


Kindly note that there is a subtle difference between the implementation of solady (solmate’s) SafeTransferLib and OZ’s `SafeERC20`. 
>OZ’s SafeERC20 checks if the token is a contract or not, solady’s SafeTransferLib does not. (it's based on solmate safetransferlib)
See: https://github.com/Vectorized/solady/blob/main/src/utils/SafeTransferLib.sol#L10

Note that none of the functions in this library check that a token has code at all. That responsibility is delegated to the caller.
As a result, when the token’s address has no code, the transaction will just succeed with no error.
This attack vector was made well-known by the [qBridge hack back in Jan 2022](https://www.halborn.com/blog/post/explained-the-qubit-hack-january-2022).

It’s becoming popular for protocols to deploy their token across multiple networks and when they do so, a common practice is to deploy the token contract from the same deployer address and with the same nonce so that the token address can be the same for all the networks.

A sophisticated attacker can exploit it by taking advantage of that and setting traps on multiple potential tokens to create fake deposits. For example: 1INCH is using the same token address for both Ethereum and BSC; Gelato's $GEL token is using the same token address for Ethereum, Fantom and Polygon. Also, attacker can frontrun one new token deployment and create fake deposits before it hit the chain which means that this attack vector can be exploited via deposits.

#### Impact
Considering that Protocol is integrating all types of ERC20s, A hacker could set traps for non existing tokens to steal future funds from users.

#### Recommended Mitigation Steps
Verify if the token has code before integrating or consider using OpenZeppelin's SafeERC20 library instead, which includes checks to ensure that the token contract exists.









## [QA-03] Incorrect `Pause/Unpause` logic in pauser contract leading to unintended state changes which contradicts the `ReadMe`

#### Impact
The current implementation of the `_pause` and `_unpause` functions can lead to unintended state changes. This could result in accidentally pausing or unpausing functions that were not meant to be affected, potentially disrupting the intended functionality of the contract and any contracts that inherit from it.

#### Proof of Concept
Look at both the `_pause` and `_unpause` functions: https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/Pauser.sol#L67-L79

```solidity
function _pause(uint256 map) internal virtual {
    PauserStorage storage self = _getPauserStorage();
    if ((self._paused & map) != self._paused) revert AttemptedUnpauseWhilePausing();
    self._paused = map; // Incorrect: overwrites entire pause state
    emit Paused(_msgSender(), map);
}

function _unpause(uint256 map) internal virtual {
    PauserStorage storage self = _getPauserStorage();
    if (self._paused & map != map) revert AttemptedPauseWhileUnpausing();
    self._paused = map; // Incorrect: overwrites entire pause state
    emit Unpaused(_msgSender(), map);
}
```

According to the [ReadMe](https://github.com/code-423n4/2024-07-karak?tab=readme-ov-file#attack-ideas-where-to-focus-for-bugs),
```
Attack ideas (where to focus for bugs)
Upgradability of Core and vaults.

PAUSABILITY:

- Can contracts be paused by non owner or non manger addresses.
- Each pausing map pauses only the intended functionalities.
```

In both functions as seen above, the line `self._paused = map;` incorrectly overwrites the entire pause state with the provided `map`. This means:

1. In `_pause`, it might accidentally unpause functions that were previously paused but not included in the new `map`.
2. In `_unpause`, it might accidentally pause functions that were not previously paused and not intended to be paused.
3. 
These behaviors directly contradict the protocol's focus on ensuring that "each pausing map pauses only the intended functionalities."

Also, the condition for reverting in `_unpause` is unnecessarily complex and can be simplified.




#### Recommended Mitigation Steps
Update the `_pause` and `_unpause` functions to correctly modify only the specified bits in the pause state. Also, simplify the revert condition in `_unpause`.

```diff
function _pause(uint256 map) internal virtual {
    PauserStorage storage self = _getPauserStorage();
    if ((self._paused & map) != self._paused) revert AttemptedUnpauseWhilePausing();
-   self._paused = map;
+   self._paused = self._paused | map;
    emit Paused(_msgSender(), map);
}

function _unpause(uint256 map) internal virtual {
    PauserStorage storage self = _getPauserStorage();
-   if (self._paused & map != map) revert AttemptedPauseWhileUnpausing();
+   if ((self._paused & map) != map) revert AttemptedPauseWhileUnpausing();
-   self._paused = map;
+   self._paused = self._paused & ~map;
    emit Unpaused(_msgSender(), map);
}
```

These changes will see to it that:
- `_pause` only adds new paused functions without affecting others.
- `_unpause` only removes specified paused functions without affecting others.









## [QA-04] `hasDuplicates` function modifies input array contrary to intent

#### Impact

The `hasDuplicates` function in the CommonUtils library sorts the input array, modifying its order. This behavior contradicts the function's comment, which states the intent was to keep the array unchanged. The function is also marked as `pure`, implying it doesn't modify inputs. This could cause bugs in dependent contracts that rely on the original order of the array after calling this function.

#### Proof of Concept

[In CommonUtils.sol:](https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/utils/CommonUtils.sol#L39-L47)

```solidity
/// @notice Sorts the array and checks for duplicates
/// Intent was to get the array unchanges after sorting
/// @param arr Array of addresses to check duplicates
/// @return boolean indicates whether array has duplicates or not
function hasDuplicates(address[] memory arr) external pure returns (bool) {
    sortArr(arr);
    if (arr.length == 0) return false;
    for (uint256 i = 0; i < arr.length - 1; i++) {
        if (arr[i] == arr[i + 1]) return true;
    }
    return false;
}
```

The `sortArr(arr)` call modifies the input array, contradicting the [comment](https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/utils/CommonUtils.sol#L36-L37) and potentially violating caller expectations.


```solidity
    /// Intent was to get the array unchanges after sorting

```

#### Recommended Mitigation Steps

> There are 2 options to fix this:

Option 1: If the array should remain unchanged, create a copy before sorting:

```solidity
function hasDuplicates(address[] memory arr) external pure returns (bool) {
    address[] memory sortedArr = new address[](arr.length);
    for (uint256 i = 0; i < arr.length; i++) {
        sortedArr[i] = arr[i];
    }
    sortArr(sortedArr);
    if (sortedArr.length == 0) return false;
    for (uint256 i = 0; i < sortedArr.length - 1; i++) {
        if (sortedArr[i] == sortedArr[i + 1]) return true;
    }
    return false;
}
```

Option 2: If modifying the input is acceptable, update the function name and documentation:

```solidity
/// @notice Sorts the array and checks for duplicates
/// @param arr Array of addresses to check duplicates (will be sorted)
/// @return boolean indicates whether array has duplicates or not
function hasDuplicatesAndSort(address[] memory arr) external pure returns (bool) {
    // ... existing implementation ...
}
```









## [QA-05] Ambiguous handling of supports interface in `HookLib` may cause unintended DSS hook executions or omissions

#### Impact
The current implementation fails to distinguish between intentionally unsupported interfaces and failed calls to `supportsInterface`. This can result in hooks being incorrectly skipped or executed, potentially leading to inconsistencies between the Core contract and DSS contracts. It undermines the flexibility intended for DSS contracts to selectively implement hooks based on their operational needs.

#### Proof of Concept
The `callHookIfInterfaceImplemented` function doesn't properly differentiate between unsupported interfaces and failed calls:
https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/HookLib.sol#L78-L105

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
    // ... (earlier code omitted for brevity)

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

This implementation contradicts the [documentation](https://docs.karak.network/developers/dss/hooks#core-function-hooks), which states:

>DSS contracts can selectively implement hooks based on their operational needs. This selection is managed through the supportsInterface function, which determines whether the DSS supports specific interface functions.

The current code doesn't allow for this selective implementation, as it treats both failed calls and intentionally unsupported interfaces the same way.

#### Recommended Mitigation Steps
Modify the `callHookIfInterfaceImplemented` function to distinguish between failed calls and intentionally unsupported interfaces. This can be done by separately handling the case where the call succeeds but returns false (indicating an intentionally unsupported interface) from the case where the call fails.

Additionally, consider adding a mechanism in the Core contract to respect the DSS's choice of supported interfaces, allowing for the flexibility described in the documentation.











## [QA-06] WETH compatibility issue on Blast chain


#### Proof of Concept
According to [ReadMe](https://github.com/code-423n4/2024-07-karak?tab=readme-ov-file#scoping-q--a), 

##### ERC20 token behaviors in scope

| Question                                                                                                                                                   | Answer |
| ---------------------------------------------------------------------------------------------- | ------ |
| ERC20 used by the protocol  | Any (all possible ERC20s    |

WETH is a type of ERC-20.

Also, it's explicitly stated that the protocol will be deployed on the Blast chain too. However, if we look at the `handleSlashing` function of [`vault.sol` contract,](https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/SlashingHandler.sol#L52-L59)

```solidity
function handleSlashing(IERC20 token, uint256 amount) external {
        if (amount == 0) revert ZeroAmount();
        if (!_config().supportedAssets[token]) revert UnsupportedAsset();

❌     SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount);
        // Below is where custom logic for each asset lives
        SafeTransferLib.safeTransfer(address(token), address(0), amount);
    }
```
The token transfer is done using the safeTransferFrom method. This works fine on most chains (Ethereum, Optimism, Polygon, BSC) which use the standard WETH9 contract that handles the case src == msg.sender:

WETH9.sol
```solidity
if (src != msg.sender && allowance[src][msg.sender] != uint(- 1)) {
            require(allowance[src][msg.sender] >= wad);
            allowance[src][msg.sender] -= wad;
        }
```
The problem is that the WETH implementation on Blast uses [a different contract](https://blastscan.io/address/0x4300000000000000000000000000000000000004#code), and does not have this `src == msg.sender handling`.

Also, the issue is presented in [Wrapped Arbitrum](https://arbiscan.io/address/0x8b194beae1d3e0788a1a35173978001acdfba668#code) and  [Wrapped Fantom](https://ftmscan.com/token/0x21be370d5312f44cb42ce377bc9b8a0cef1a4c83#code).


Setup a foundry environment, get an Arbitrum RPC endpoint url and run the following command: `forge test --match-test testTransferFromIssue --fork-url {YOUR_RPC}`. This will revert with a ERC20: transfer amount exceeds allowance error, because of the issue described above.

```solidity
pragma solidity ^0.8.0;

import "forge-std/Test.sol";
import "forge-std/console.sol";
import {IERC20} from "forge-std/interfaces/IERC20.sol";

contract PairTest is Test {
    using stdStorage for StdStorage;
    address alice = address(0x1);
    address bob = address(0x2);
    IERC20 public constant WETH = IERC20(0x82aF49447D8a07e3bd95BD0d56f35241523fBab1);

    function setUp() public {
    }
  
    function testTransferFromIssue() public {
      // given WETH to Alice
      stdstore
            .target(address(WETH))
            .sig(WETH.balanceOf.selector)
            .with_key(alice)
            .checked_write(10 ether);


      vm.startPrank(alice);

      //IERC20(address(WETH)).transfer(bob, 1 ether); //@audit toggle this line on and the one below off to see the allowance problem
      IERC20(address(WETH)).transferFrom(alice, bob, 1 ether);
      vm.stopPrank();
    }
}
```


#### Impact

The slashing mechanism in the Vault contract may fail or behave inconsistently when handling certain ERC20 tokens on chains with non-standard token implementations, such as Blast.


#### Recommended Mitigation Steps
Modify `handleSlashing` in `slashinghandler.sol` as follows:

```diff
-        SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount);
+       SafeTransferLib.safeTransfer(address(token), address(this), amount);
```











## [QA-07] Missing logic for updating `nextSlashableTimestamp` impacts slashing mechanism
 
#### Impact
The `nextSlashableTimestamp` mapping in the `State` struct is never updated or used, which directly violates the requirement in the `ReadMe` that:

>A DSS must wait the cooldown period before being able to slash again.

This could allow a DSS to slash operators more frequently than intended, bypassing the mandatory cooldown period. 

Additionally, this issue indirectly affects the ability to properly enforce the slashing percentage limits, as there's no mechanism to ensure that slashing requests are appropriately spaced out in time. This could lead to cases where multiple slashing events occur in rapid succession, potentially exceeding the intended maximum slashing percentage over a short period.

The absence of logic to update the `nextSlashableTimestamp` mapping in the contract prevents accurate tracking of when an operator becomes eligible for slashing again after staking or unstaking actions.

#### Proof of Concept
The `State` struct defines a mapping `nextSlashableTimestamp` intended to track when an operator can be slashed again by a `DSS`:

```solidity
mapping(IDSS dss => uint256 timestamp) nextSlashableTimestamp; // When this operator can be slashed again by a DSS
```

Albeit, the contract lacks functionality to update this mapping after stake operations (`updateVaultStakeInDSS`). As a result, once set, the `nextSlashableTimestamp` remains unchanged, regardless of subsequent stake actions:

For example, the `updateVaultStakeInDSS` function modifies staking state but doesn't update the `nextSlashableTimestamp`:
https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/Operator.sol#L102-L108

```solidity
function updateVaultStakeInDSS(State storage operatorState, address vault, IDSS dss, bool toStake) internal {
    if (toStake) {
        operatorState.vaultStakedInDssMap[dss].add(vault);
    } else {
        operatorState.vaultStakedInDssMap[dss].remove(vault);
    }
}
```

This will mean that the protocol doesn't properly track when an operator can be slashed again, potentially allowing for premature slashing or preventing timely slashing when it should be allowed.

#### Recommended Mitigation Steps
Update the `updateVaultStakeInDSS` function to set the `nextSlashableTimestamp` when a stake operation occurs. Also, add a function to check the `nextSlashableTimestamp` before allowing a slash operation.













## [QA-08] Lack of beacon state root validation in `validateWithdrawalCredentials` function allows acceptance of outdated or manipulated proofs

#### Impact
An attacker could use outdated or manipulated beacon chain state roots when validating withdrawal credentials. This could lead to the acceptance of invalid proofs, allowing manipulation of validator statuses. 

#### Proof of Concept
In NativeVaultLib.sol, `validateWithdrawalCredentials` function takes beaconStateRoot as an input parameter: https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/NativeVaultLib.sol#L146-L152

```solidity
          146:     function validateWithdrawalCredentials(
          147:         Storage storage self,
          148:         address nodeOwner,
          149:         uint64 updateTimestamp,
❌        150:         bytes32 beaconStateRoot,         
          151:         BeaconProofs.ValidatorFieldsProof calldata validatorFieldsProof
          152:     ) internal returns (uint256) {
```

This `beaconStateRoot` is then passed directly to `BeaconProofs.validateValidatorProof`: https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/NativeVaultLib.sol#L170-L175
```solidity
170:         BeaconProofs.validateValidatorProof(
171:             validatorFieldsProof.validatorProof.validatorIndex,
172:             validatorFieldsProof.validatorFields,
173:             validatorFieldsProof.validatorProof.proof,
174:             beaconStateRoot
175:         );
```


In `BeaconProofsLib.sol`, the `validateValidatorProof` function uses this `beaconStateRoot` without any additional checks: https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/BeaconProofsLib.sol#L92

```solidity
          73:     function validateValidatorProof(
          74:         uint40 validatorIndex,
          75:         bytes32[] calldata validatorFields,
          76:         bytes calldata validatorProof,
          77:         bytes32 beaconStateRoot
          78:     ) internal view {
          79:         if (validatorFields.length != NUM_FIELDS) revert InvalidValidatorFieldsLength();
          80: 
          81:         bytes32 validatorRoot = Merkle.merkleizeSha256(validatorFields);
          82: 
          83:         // Calculate the index for validator proof verification
          84:         // Shift the container index left by the sum of validator height and one, then combine with the validator index
          85:         uint256 index = (CONTAINER_IDX << (VALIDATOR_HEIGHT + 1)) | uint256(validatorIndex);
          86: 
          87:         // Validate the length of the validator proof
          88:         if (validatorProof.length != 32 * ((VALIDATOR_HEIGHT + 1) + BEACON_STATE_HEIGHT)) {
          89:             revert InvalidValidatorFieldsProofLength();
          90:         }
          91:         // Validate the inclusion of the validator root in the beacon state root
❌        92:         if (!Merkle.verifyInclusionSha256(validatorProof, beaconStateRoot, validatorRoot, index)) {
          93:             revert InvalidValidatorFieldsProofInclusion();
          94:         }
          95:     }
```


The issue here is that there's no verification that the provided `beaconStateRoot` is actually the current or recent state root of the beacon chain. This means that an attacker could potentially provide an old or manipulated `beaconStateRoot` along with a corresponding valid proof for that outdated state, which would pass the validation because the system would accept this proof as valid.

#### Recommended Mitigation Steps
Consider implementing a mechanism to verify the authenticity and recency of the `beaconStateRoot` before using it in proof validations. Maybe by implementing a time-based check to ensure the provided state root is recent, or using a light client or checkpoint sync to verify the beacon chain state. 










## [QA-09] Logic issue in `sort` function of `CommonUtils` library

#### Proof of Concept

`sort` function has a logic issue that could lead to incorrect sorting or infinite recursion in certain cases: https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/utils/CommonUtils.sol#L12-L33

```solidity
function sort(address[] memory arr, uint256 left, uint256 right) private pure {
    if (left >= right) return;
    uint256 lastUnsortedInd = left;
    uint256 pivot = right;
    for (uint256 i = left; i < right; i++) {
        if (arr[i] <= arr[pivot]) {
            if (i != lastUnsortedInd) swap(arr, i, lastUnsortedInd);
            lastUnsortedInd++;
        }
    }
    swap(arr, pivot, lastUnsortedInd);
    if (lastUnsortedInd > left) {
        sort(arr, left, lastUnsortedInd - 1);
    }
    sort(arr, lastUnsortedInd, right);
}
```

The main issue is here in the recursive calls at the end of the function:

1. The first recursive call `sort(arr, left, lastUnsortedInd - 1)` is correct, but it's unnecessarily wrapped in an `if` statement. This check is not needed because if `lastUnsortedInd == left`, the base case `if (left >= right) return;` at the beginning of the function will handle it.

2. The second recursive call `sort(arr, lastUnsortedInd, right)` can lead to infinite recursion or incorrect sorting. If the pivot element ends up being the smallest element in the current partition, `lastUnsortedInd` will be equal to `left`, and the function will keep calling itself with the same arguments.

#### Recommended Mitigation Steps

```diff
function sort(address[] memory arr, uint256 left, uint256 right) private pure {
    if (left >= right) return;
    uint256 lastUnsortedInd = left;
    uint256 pivot = right;
    for (uint256 i = left; i < right; i++) {
        if (arr[i] <= arr[pivot]) {
            if (i != lastUnsortedInd) swap(arr, i, lastUnsortedInd);
            lastUnsortedInd++;
        }
    }
    swap(arr, pivot, lastUnsortedInd);
    
-   if (lastUnsortedInd > left) {
-       sort(arr, left, lastUnsortedInd - 1);
-   }
-   sort(arr, lastUnsortedInd, right);
+   // Fix: Remove unnecessary if statement and adjust the recursive calls
+   sort(arr, left, lastUnsortedInd - 1);
+   sort(arr, lastUnsortedInd + 1, right); // Change lastUnsortedInd to lastUnsortedInd + 1
}
```

These changes will see to it that:

1. The left partition is always sorted (including when `lastUnsortedInd == left`).
2. The right partition starts from `lastUnsortedInd + 1`, avoiding potential infinite recursion and ensuring all elements are properly sorted.













## [QA-10] Premature DSS removal in `unregisterOperatorFromDSS` function potentially breaks hook functionality

#### Impact
The current implementation of the `unregisterOperatorFromDSS` function removes the DSS from the operator's `dssMap` before calling the unregistration hook. This could cause issues or failures in the hook execution, as the hook may rely on the operator still being registered to the DSS. Additionally, the hook is called with `ignoreFailure: true`, which means any critical checks or operations in the hook could be bypassed.

#### Proof of Concept
Take a look at `unregisterOperatorFromDSS` function: https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/Operator.sol#L181-L203

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
    if (!isOperatorRegisteredToDSS(self, operator, dss)) revert OperatorNotValidatingForDSS();

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

Here, the DSS is removed from the operator's `dssMap` before the unregistration hook is called. This can lead to scenarios where:

- The hook attempts to perform operations that require the operator to still be registered, but fails because the registration has already been removed.
- Critical checks or operations in the hook are bypassed due to `ignoreFailure: true`.

#### Recommended Mitigation Steps
Consider changing the order of operations and the `ignoreFailure` flag should be reconsidered.

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
    if (!isOperatorRegisteredToDSS(self, operator, dss)) revert OperatorNotValidatingForDSS();

-   self.operatorState[operator].dssMap.remove(address(dss));
    HookLib.callHookIfInterfaceImplemented({
        dss: dss,
        data: abi.encodeWithSelector(dss.unregistrationHook.selector, operator, unregistrationHookData),
        interfaceId: dss.unregistrationHook.selector,
-       ignoreFailure: true, // So it can't prevent the operator from unregistering
+       ignoreFailure: false, // Allow critical checks in the hook to prevent unregistration if necessary
        hookCallGasLimit: self.hookCallGasLimit,
        supportsInterfaceGasLimit: self.supportsInterfaceGasLimit,
        hookGasBuffer: self.hookGasBuffer
    });
+   self.operatorState[operator].dssMap.remove(address(dss));
}
```










## [QA-11] Lack of slippage protection in redeem process exposes users to potential value loss

#### Impact

The absence of slippage protection in the redeem process of the Vault contract exposes users to potential value loss. When market conditions change between initiating and finalizing a redemption, users may receive fewer assets than expected without the ability to specify a minimum acceptable amount.

#### Proof of Concept

The redeem process in the Vault contract is split into two functions: `startRedeem` and `finishRedeem`. Neither of these functions includes parameters for slippage protection:
https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/Vault.sol#L125-L188

```solidity
function startRedeem(uint256 shares, address beneficiary)
    external
    whenFunctionNotPaused(Constants.PAUSE_VAULT_START_REDEEM)
    nonReentrant
    returns (bytes32 withdrawalKey)
{
    // ... (code omitted for brevity)
}

function finishRedeem(bytes32 withdrawalKey)
    external
    nonReentrant
    whenFunctionNotPaused(Constants.PAUSE_VAULT_FINISH_REDEEM)
{
    // ... (code omitted for brevity)
    uint256 redeemableAssets = convertToAssets(shares);
    
    _withdraw({
        by: address(this),
        to: startedWithdrawal.beneficiary,
        owner: address(this),
        assets: redeemableAssets,
        shares: shares
    });
    // ... (code omitted for brevity)
}
```

In the `finishRedeem` function, the `redeemableAssets` are calculated and withdrawn without any check against a minimum expected value. This leaves users vulnerable to receiving fewer assets than they might have anticipated when they initiated the redemption.

Contrast this with the deposit function that does offer slippage protection:

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

This deposit function allows users to specify `minSharesOut`, providing protection against unexpected slippage. A similar mechanism is missing in the redeem process.

#### Recommended Mitigation Steps

Implement slippage protection in the redeem process by adding a `minAssetsOut` parameter to the `startRedeem` function. Store this value in the `WithdrawLib.QueuedWithdrawal` struct. In the `finishRedeem` function, compare the `redeemableAssets` against the stored `minAssetsOut` value and revert the transaction if the minimum is not met. This change will allow users to specify their acceptable minimum asset return, protecting them from unexpected value loss during the redemption process.












## [QA-12] Initial griefing attack possible in Vault, allowing attacker to lock victim's funds

#### Impact
An attacker can perform an initial griefing attack on the Vault contract which can result in locking a victim's funds in the contract. While this attack is not actually profitable for the attacker, it can cause significant harm to the victim and leave the vault in an abnormal state. 
>Impact - high
>Likelihood - low due to the cost incurred by the attacker.

#### Proof of Concept
The Vault contract is vulnerable to an initial griefing attack due to the lack of protection against small initial deposits followed by large transfers to inflate the exchange rate. This is the sequence how the exploits could happen:

1. The attacker makes a very small initial [deposit](https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/Vault.sol#L94-L103):

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

2. The attacker then transfers a large amount of tokens directly to the vault, inflating the exchange rate.

3. A victim attempts to deposit a significant amount, but due to the inflated exchange rate, they receive 0 shares:

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

4. The victim's funds are now locked in the contract, as they have no shares to redeem.

This attack is possible because the contract lacks checks for minimum deposit amounts and doesn't have protection against artificially inflated exchange rates during the initial deposit phase.

#### Recommended Mitigation Steps
Consider:

1. Add a minimum deposit amount check in the `deposit` function to prevent very small initial deposits.
2. Implement an initial "seed" deposit during the vault's initialization to establish a reasonable initial exchange rate.
3. Add extra checks for the first deposit to ensure it's of a reasonable size.
4. Consider implementing a maximum exchange rate increase per transaction to prevent sudden, large inflation of the exchange rate."





## [QA-13] Premature implementation mapping in `createVault` creates room for potential front-running attack

#### Impact
This could allow an attacker to potentially associate a malicious contract with a legitimate implementation address in the `vaultToImplMap`. 

#### Proof of Concept
https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/CoreLib.sol#L89-L116

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
    // ... (earlier code omitted)

    address expectedNewVaultAddr =
        LibClone.predictDeterministicAddressERC1967BeaconProxy(address(this), salt, address(this));

    self.vaultToImplMap[address(expectedNewVaultAddr)] = implementation;

    IKarakBaseVault vault = cloneVault(salt);
    vault.initialize(address(this), operator, depositToken, name, symbol, extraData);

    // ... (rest of the function)
}
```

The issue is because the `vaultToImplMap` is updated with the `implementation` address before the vault is actually created and initialized. An attacker can exploit this by:

- Monitoring the mempool for `createVault` transactions.
- Predicting the `expectedNewVaultAddr` using the same deterministic factors.
- Front-running the transaction to deploy a malicious contract at the predicted address.
- When the original `createVault` transaction executes, it will associate the attacker's malicious contract with the legitimate implementation address in `vaultToImplMap`.


#### Recommended Mitigation Steps
Update the `vaultToImplMap` only after the vault has been successfully created and initialized. Move the mapping update to after the vault creation and address verification check. This ensures that only legitimate, successfully created vaults are associated with their implementations in the mapping. 

```diff
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

-   self.vaultToImplMap[address(expectedNewVaultAddr)] = implementation;

    IKarakBaseVault vault = cloneVault(salt);
    vault.initialize(address(this), operator, depositToken, name, symbol, extraData);

    // Extra protection to ensure the vault was created with the correct address
    if (expectedNewVaultAddr != address(vault)) {
        revert VaultCreationFailedAddrMismatch(expectedNewVaultAddr, address(vault));
    }

+   self.vaultToImplMap[address(vault)] = implementation;

    emit NewVault(address(vault), implementation);
    return vault;
}
```












## [QA-14] Uniform token slashing mechanism using `address(0)` transfer may fail for  some ERC20 tokens which could lead to DOS

#### Impact
In the current slashing mechanism, which sends tokens to `address(0)`, it can lead to issues like:
 
- For tokens that don't remove themselves from circulation when sent to `address(0)`, the slashing doesn't actually reduce the token supply.
- For tokens that revert on transfers to `address(0)`, the slashing operation will fail entirely.
- Tokens with additional functionality or underlying assets may not be correctly handled by this simplistic approach.

These issues can result in the slashing mechanism failing to achieve its intended purpose causing a DOS.

#### Proof of Concept
Look at this part of the code: https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/SlashingHandler.sol#L52-L59

```solidity
function handleSlashing(IERC20 token, uint256 amount) external {
    if (amount == 0) revert ZeroAmount();
    if (!_config().supportedAssets[token]) revert UnsupportedAsset();

    SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount);
    // Below is where custom logic for each asset lives
    SafeTransferLib.safeTransfer(address(token), address(0), amount);
}
```

This function attempts to slash tokens by sending them to `address(0)`, which is not so effective for all token types:

1. Tokens that don't remove from circulation when sent to `address(0)`:
   - Basic ERC20 tokens without a burn function
   - Wrapped Ether (WETH)
   - Many governance or utility tokens

   For these tokens, sending to `address(0)` makes the tokens inaccessible but doesn't reduce the total supply.

2. Tokens that revert on transfers to `address(0)`:
   - USDT (Tether)
   - BNB (Binance Coin on Ethereum)
   - Tokens implementing OpenZeppelin's ERC20 with transfer checks
   - Some DeFi tokens like Compound's cTokens

These tokens include checks like:
   ```solidity
   require(to != address(0), "ERC20: transfer to the zero address");
   ```
   Attempting to slash these tokens will cause the transaction to revert, failing the slashing operation.

3. Complex tokens with additional functionality or underlying assets (e.g., staked tokens) may require specific handling that isn't addressed by this simple transfer.

#### Recommended Mitigation Steps
Consider creating a mapping of token addresses to slashing strategies. Now for each supported token, implement a custom slashing strategy that correctly handles its specific requirements (e.g., using a burn function if available, holding slashed tokens in the contract, or implementing token-specific logic).









## [QA-15] Insufficient funds for slashing due to time delay could render slashing mechanism ineffective

#### Impact
It can lead to failed slashing attempts and differences between expected and actual slashed amount. 

#### Proof of Concept
The problem stems from the time delay between requesting and finalizing a slashing operation.

In the [`requestSlashing` function:](https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/SlasherLib.sol#L94-L124)

```solidity
function requestSlashing(
    CoreLib.Storage storage self,
    IDSS dss,
    SlashRequest memory slashingMetadata,
    uint256 nonce
) external returns (QueuedSlashing memory queuedSlashing) {
    // ...
    uint256[] memory earmarkedStakes = fetchEarmarkedStakes(slashingMetadata);
    queuedSlashing = QueuedSlashing({
        // ...
        earmarkedStakes: earmarkedStakes,
        // ...
    });
    // ...
}
```

The `earmarkedStakes` are calculated based on the current total assets of the vault.

In the [`finalizeSlashing` function:](https://github.com/code-423n4/2024-07-karak/blob/ab18e1f6c03e118158369527baa2487b2b4616b1/src/entities/SlasherLib.sol#L126-L151)

```solidity
function finalizeSlashing(CoreLib.Storage storage self, QueuedSlashing memory queuedSlashing) external {
    // ...
    if (queuedSlashing.timestamp + Constants.SLASHING_VETO_WINDOW > block.timestamp) {
        revert MinSlashingDelayNotPassed();
    }
    // ...
    for (uint256 i = 0; i < queuedSlashing.vaults.length; i++) {
        IKarakBaseVault(queuedSlashing.vaults[i]).slashAssets(
            queuedSlashing.earmarkedStakes[i],
            self.assetSlashingHandlers[IKarakBaseVault(queuedSlashing.vaults[i]).asset()]
        );
    }
    // ...
}
```

The slashing is only executed after the `SLASHING_VETO_WINDOW` has passed. During this time window, the total assets in the vault could decrease, potentially leading to insufficient funds for slashing when `finalizeSlashing` is called.


#### Recommended Mitigation Steps
Consider implementing a locking mechanism that prevents withdrawals or transfers of the earmarked assets during the veto window. Alternatively, recalculate the slashable amount at the time of finalization based on the current total assets, ensuring that the slashed amount is always proportional to the available assets. Then, implement checks in the `finalizeSlashing` function to handle cases where the available assets are less than the originally earmarked amount, possibly by slashing a percentage of the current assets rather than a fixed amount.

