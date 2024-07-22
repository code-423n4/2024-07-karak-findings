
# [G-01] `Core::unregisterOperatorFromDSS` checks twice for `isOperatorRegisteredToDSS`
The `unregisterOperatorFromDSS` function calls both `checkIfOperatorIsRegInRegDSS` and `unregisterOperatorFromDSS`. In both these functions, the `isOperatorRegisteredToDSS` is called, which is unnecessary and more gas costly.
Here is the code for these three functions:

<details>

```javascript
function unregisterOperatorFromDSS(
        IDSS dss,
        bytes memory unregistrationHookData
    )
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_CORE_UNREGISTER_FROM_DSS)
    {
        address operator = msg.sender;
        CoreLib.Storage storage self = _self();
@>      self.checkIfOperatorIsRegInRegDSS(operator, dss);
@>      self.unregisterOperatorFromDSS(dss, operator, unregistrationHookData); //f

        emit UnregisteredOperatorToDSS(operator, address(dss));
    }
```

```javascript
    function checkIfOperatorIsRegInRegDSS(
        CoreLib.Storage storage self,
        address operator,
        IDSS dss
    ) internal view {
        if (!self.isDSSRegistered(dss)) revert DSSNotRegistered();
@>      if (!isOperatorRegisteredToDSS(self, operator, dss)) {
@>          revert OperatorNotValidatingForDSS();
@>      }
    }
```

```javascript
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
@>      if (!isOperatorRegisteredToDSS(self, operator, dss))
@>          revert OperatorNotValidatingForDSS();

        self.operatorState[operator].dssMap.remove(address(dss));
        HookLib.callHookIfInterfaceImplemented({
            dss: dss,
            data: abi.encodeWithSelector(
                dss.unregistrationHook.selector,
                operator,
                unregistrationHookData
            ),
            interfaceId: dss.unregistrationHook.selector,
            ignoreFailure: true, // So it can't prevent the operator from unregistering
            hookCallGasLimit: self.hookCallGasLimit,
            supportsInterfaceGasLimit: self.supportsInterfaceGasLimit,
            hookGasBuffer: self.hookGasBuffer
        });
    }
```
</details>

## Impact
If you remove the `checkIfOperatorIsRegInRegDSS` and instead use `isDSSRegistered` only, this will save the user `1,757 gas`.

## Proof of Concept
This can be tested by altering the `unregisterOperatorFromDSS` like this and then running the `existing test_unregister_operator_from_dss` test.
**Edited unregister function:** `[PASS] test_unregister_operator_from_dss()` (gas: 221078)
**Original unregister function:** `[PASS] test_unregister_operator_from_dss()` (gas: 222835)
```diff
    function unregisterOperatorFromDSS(
        IDSS dss,
        bytes memory unregistrationHookData
    )
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_CORE_UNREGISTER_FROM_DSS)
    {
        address operator = msg.sender;
        CoreLib.Storage storage self = _self();
-       self.checkIfOperatorIsRegInRegDSS(operator, dss);
+       if (!self.isDSSRegistered(dss)) revert DSSNotRegistered();
        self.unregisterOperatorFromDSS(dss, operator, unregistrationHookData); //f

        emit UnregisteredOperatorToDSS(operator, address(dss));
    }
```
## Tools Used
Manual Review

## Recommended Mitigation Steps
Use the version of the unregistered function suggested in the proof of concept.


# [G-02] Extra checks in `Core::requestUpdateVaultStakeInDSS`
Since a Dss can only be registered and cannot be unregistered anywhere in the contract, it's unnecessary to check if the DSS is registered and if the operator is registered to that DSS. Because if the operator is registered to a DSS, it already is checked in the `registerOperatorToDSS` function, meaning that the DSS is registered in the contract beforehand. This check will cost a lot of gas every time an operator wants to stake or unstake to a DSS.

```javascript
function requestUpdateVaultStakeInDSS(
        Operator.StakeUpdateRequest memory vaultStakeUpdateRequest
    )
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_CORE_REQUEST_STAKE_UPDATE)
        returns (Operator.QueuedStakeUpdate memory updatedStake)
    {

        address operator = msg.sender;
        CoreLib.Storage storage self = _self();
@>      self.checkIfOperatorIsRegInRegDSS(
@>          operator,
@>          vaultStakeUpdateRequest.dss
        );
        updatedStake = self.requestUpdateVaultStakeInDSS(
            vaultStakeUpdateRequest,
            self.nonce++,
            operator
        );
        emit RequestedStakeUpdate(updatedStake);
    }
```
## Impact
This will cost operators `1,634` more gas every time they want to stake or unstake.

## Proof of Concept
Update the `requestUpdateVaultStakeInDSS` as such:
```diff
function requestUpdateVaultStakeInDSS(
        Operator.StakeUpdateRequest memory vaultStakeUpdateRequest
    )
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_CORE_REQUEST_STAKE_UPDATE)
        returns (Operator.QueuedStakeUpdate memory updatedStake)
    {
        address operator = msg.sender;
        CoreLib.Storage storage self = _self();
+       if (
+           !self.isOperatorRegisteredToDSS(
+              operator,
+               vaultStakeUpdateRequest.dss
+         )
+       ) {
+           revert OperatorNotValidatingForDSS();
+       }
-       self.checkIfOperatorIsRegInRegDSS(
-           operator,
-           vaultStakeUpdateRequest.dss
-       );
        updatedStake = self.requestUpdateVaultStakeInDSS(
            vaultStakeUpdateRequest,
            self.nonce++,
            operator
        );
        emit RequestedStakeUpdate(updatedStake);
    }
```
And then run the `test_request_stake_update_pending_noFuzz` which is the edited version of the existing test with removed fuzz:
```javascript
function test_request_stake_update_pending_noFuzz() public {
        uint256 time = block.timestamp;
        deposit_into_vaults();

        address requestedVault = address(vaults[0]);
        Operator.StakeUpdateRequest memory stakeUpdate = Operator
            .StakeUpdateRequest({
                vault: requestedVault,
                dss: dss,
                toStake: true
            });

        vm.startPrank(operator);
        core.requestUpdateVaultStakeInDSS(stakeUpdate);

        vm.warp(time);
        stakeUpdate = Operator.StakeUpdateRequest({
            vault: requestedVault,
            dss: dss,
            toStake: false
        });
        vm.expectRevert(PendingStakeUpdateRequest.selector);
        core.requestUpdateVaultStakeInDSS(stakeUpdate);
    }
```
The results will be as such:

**Edited Request Update function:** `[PASS] test_request_stake_update_pending_noFuzz()` (gas: 1,245,035)
**Original Request Update function:** `[PASS] test_request_stake_update_pending_noFuzz()` (gas: 1,247,669)

## Tools Used
Manual review
## Recommended Mitigation Steps
Use the fixed version in the proof of concept.
