---
sponsor: "Karak"
slug: "2024-07-karak"
date: "2024-10-07"
title: "Karak Restaking"
findings: "https://github.com/code-423n4/2024-07-karak-findings/issues"
contest: 404
---

# Overview

## About C4

Code4rena (C4) is an open organization consisting of security researchers, auditors, developers, and individuals with domain expertise in smart contracts.

A C4 audit is an event in which community participants, referred to as Wardens, review, audit, or analyze smart contract logic in exchange for a bounty provided by sponsoring projects.

During the audit outlined in this document, C4 conducted an analysis of the Karak Restaking smart contract system written in Solidity. The audit took place between July 16—July 30 2024.

Following the C4 audit, 3 wardens, ([KupiaSec](https://code4rena.com/@KupiaSec), [0xCiphky](https://code4rena.com/@0xCiphky) and [sl1](https://code4rena.com/@sl1)) reviewed the mitigations for all identified issues; the [mitigation review report](#mitigation-review) is appended below the audit report.

## Wardens

26 Wardens contributed reports to Karak Restaking:

  1. [KupiaSec](https://code4rena.com/@KupiaSec)
  2. [0xCiphky](https://code4rena.com/@0xCiphky)
  3. [20centclub](https://code4rena.com/@20centclub) ([tpiliposian](https://code4rena.com/@tpiliposian), [Skylice](https://code4rena.com/@Skylice), and [M3talDrag0n](https://code4rena.com/@M3talDrag0n))
  4. [sl1](https://code4rena.com/@sl1)
  5. [rbserver](https://code4rena.com/@rbserver)
  6. [givn](https://code4rena.com/@givn)
  7. [lanrebayode77](https://code4rena.com/@lanrebayode77)
  8. [zanderbyte](https://code4rena.com/@zanderbyte)
  9. [Takarez](https://code4rena.com/@Takarez)
  10. [anonymousjoe](https://code4rena.com/@anonymousjoe)
  11. [MrValioBg](https://code4rena.com/@MrValioBg)
  12. [Shaheen](https://code4rena.com/@Shaheen)
  13. [ZanyBonzy](https://code4rena.com/@ZanyBonzy)
  14. [ayden](https://code4rena.com/@ayden)
  15. [trachev](https://code4rena.com/@trachev)
  16. [Rhaydden](https://code4rena.com/@Rhaydden)
  17. [Fulum](https://code4rena.com/@Fulum)
  18. [iam\_emptyset](https://code4rena.com/@iam_emptyset)
  19. [peanuts](https://code4rena.com/@peanuts)
  20. [0xlemon](https://code4rena.com/@0xlemon)
  21. [c0pp3rscr3w3r](https://code4rena.com/@c0pp3rscr3w3r)
  22. [gesha17](https://code4rena.com/@gesha17)
  23. [falconhoof](https://code4rena.com/@falconhoof)
  24. [4rdiii](https://code4rena.com/@4rdiii)

This audit was judged by [MiloTruck](https://code4rena.com/@MiloTruck).

Final report assembled by [liveactionllama](https://twitter.com/liveactionllama) and [thebrittfactor](https://twitter.com/brittfactorC4).

# Summary

The C4 analysis yielded an aggregated total of 9 unique vulnerabilities. Of these vulnerabilities, 4 received a risk rating in the category of HIGH severity and 5 received a risk rating in the category of MEDIUM severity.

Additionally, C4 analysis included 15 reports detailing issues with a risk rating of LOW severity or non-critical.

All of the issues presented here are linked back to their original finding.

# Scope

The code under review can be found within the [C4 Karak Restaking repository](https://github.com/code-423n4/2024-07-karak), and is composed of 20 smart contracts written in the Solidity programming language and includes 2,997 lines of Solidity code.

# Severity Criteria

C4 assesses the severity of disclosed vulnerabilities based on three primary risk categories: high, medium, and low/non-critical.

High-level considerations for vulnerabilities span the following key areas when conducting assessments:

- Malicious Input Handling
- Escalation of privileges
- Arithmetic
- Gas use

For more information regarding the severity criteria referenced throughout the submission review process, please refer to the documentation provided on [the C4 website](https://code4rena.com), specifically our section on [Severity Categorization](https://docs.code4rena.com/awarding/judging-criteria/severity-categorization).

# High Risk Findings (4)
## [[H-01] Slashing `NativeVault` will lead to locked ETH for the users](https://github.com/code-423n4/2024-07-karak-findings/issues/102)
*Submitted by [zanderbyte](https://github.com/code-423n4/2024-07-karak-findings/issues/102), also found by [anonymousjoe](https://github.com/code-423n4/2024-07-karak-findings/issues/104), [lanrebayode77](https://github.com/code-423n4/2024-07-karak-findings/issues/99), [sl1](https://github.com/code-423n4/2024-07-karak-findings/issues/98), and [KupiaSec](https://github.com/code-423n4/2024-07-karak-findings/issues/29)*

<https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L238><br>
<https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L277><br>
<https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L348><br>
<https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L512>

The Karak protocol includes a slashing mechanism that allows the contract owner to penalize stakers by reducing their staked assets in the event of malicious activity by the operator. If a user with a staked balance of 32 ETH is subject to a 3 ETH slashing, they should ideally be able to withdraw the remaining 29 ETH. However, due to a flaw in the implementation, when the user attempts to fully withdraw their ETH, they are only able to withdraw less than the actual remaining amount, with some ETH becoming permanently locked in the protocol.

Specifically, if all share tokens are burned during the withdrawal process, the user cannot access the remaining locked ETH. This results in users receiving fewer ETH than they are entitled to, with the excess ETH becoming inaccessible and permanently locked within the protocol.

### Proof of Concept

Consider the following simplified scenario, where Alice want to restake her 32 ETH into Karak protocol, using 1 validator and no rewards acumulated for simplicity. In this example, although Alice initially had 32 ETH staked and should be able to withdraw 29 ETH (after a 3 ETH slashing), she ends up receiving only 26 ETH. The remaining 3 ETH becomes permanently locked in the contract.
The steps she need to perform are the following:

1.  Staking and Initial Setup:
    *   Alice call `createNode` to point her withdrawal credentials to it
    *   She then calls `validateWithdrawalCredentials` followed by `startSnapshot` and `validateSnapshotProofs`.
    *After these actions, the state will look like this:\
    `totalAssets = 32e18`\
    `totalSupply = 32e18`\
    `totalRestakedETH = 32e18` (in her Node struct)*
2.  Slashing:
    *   A slashing event occurs for 3 ETH, redusing the `totalAssets` to 29e18.
3.  Withdraw from Beacon Chain:
    *   Alice decides to withdraw all her ETH from the Beacon Chain, resulting in her node balance being 32 ETH.
    *   To withdraw her ETH from the node, she performs the following operations: `startSnapshot`, `validateSnapshotProofs`, `startWithdraw`, and `finishWithdraw`.
4.  Starting Snapshot
    *   When `startSnapshot` is executed, `_transferToSlashStore` is called, slashing 3 ETH from her node and transferring them to `SlashStore`. This reduces `totalRestakedETH` to `29e18` in her Node struct and saves the new snapshot with `nodeBalanceWei = 29e18`.
5.  Validate snapshot proofs
    *   Alice calls `validateSnapshotProofs` to validate her balance proofs:

```solidity
            int256 balanceDeltaWei = self.validateSnapshotProof(
                nodeOwner, validatorDetails, balanceContainer.containerRoot, balanceProofs[i]
            );
```

*   `balanceDeltaWei = -32e18` (the difference between `newBalanceWei` of 0 and `prevBalanceWei` of 32e18)
*   `snapshot.remainigProofs = 0` since Alice has only one validator
*   After validation `_updateSnapshot` finalize snapshot due to no remaining active validators, where the balance of the user will be decreased due to the following calculation:

```solidity
int256 totalDeltaWei = int256(snapshot.nodeBalanceWei) + snapshot.balanceDeltaWei;
```

*   This results in `29e18 - 32e18 = -3e18`, reducing Alice's shares by 3e18. The state after execution will be:\
    `node.totalRestakedETH: 26e18`\
    `node.withdrawableCreditedNodeETH: 29e18`\
    `balanceOf(alice) = 28689655172413793104`\
    `totalAssets = 26e18`\
    `totalSupply = 28689655172413793104`

6.  Starting Withdrawal
    *   Alice calls `startWithdraw`. There is a check to prevent a user from withdrawing more than they actually can, but in Alice's situation, it will be less than what she should withdraw:

```solidity
if (weiAmount > withdrawableWei(msg.sender) - self.nodeOwnerToWithdrawAmount[msg.sender]) {
            revert WithdrawMoreThanMax();
        }
```

*   `withdrawableWei(msg.sender)` will return the minimum between:

```solidity
function withdrawableWei(address nodeOwner) public view nodeExists(nodeOwner) returns (uint256) {
        return
            Math.min(convertToAssets(balanceOf(nodeOwner)), _state().ownerToNode[nodeOwner].withdrawableCreditedNodeETH);
    }
```

*   `convertToAssets` is calculated as:

`(shares * (totalAssets + 1)) / (totalSupply + 1)` which is:\
`(28689655172413793104 * (26e18 + 1)) / (28689655172413793104 + 1) = 26e18` while\
`_state().ownerToNode[nodeOwner].withdrawableCreditedNodeETH` is 29e18 (the actual withdrawable wei after slashing)

7.  Finishing Withdrawal:
    *   `finishWithdrawal` sends the maximum withdrawableWei (26e18) to Alice, burning all her tokens and causing 3 ETH to remain stuck in the node.

### Recommended Mitigation Steps

Due to the complexity and the current limitations in testing the implementation of the slashing mechanism, a precise fix is difficult to pinpoint. In the example provided above, the problem occurs at step 5 where `_decreaseBalance` is called again reducing the `totalAssets` by another `3e18` (the slashed amount). If `totalAssets` are not decreased again and stays `29e18` in the next step , `withdrawableWei(msg.sender)` would correctly return the minimum between `28999999999999999999 and 29000000000000000000`.

However, to address the root cause of the issue, a better mechanism for handling slashing should be implemented and tested.

**[karan-andalusia (Karak) confirmed via duplicate issue \#29](https://github.com/code-423n4/2024-07-karak-findings/issues/29#event-13767346027)**

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/102#issuecomment-2299777427):**
 > Great find!
> 
> The crux of this issue is that `nodeBalanceWei` is calculated _after_ `_transferToSlashStore()` is called, so `node.nodeAddress.balance` will have already decreased before the unattributed balance can be added to `totalRestakedETH`.
> 
> This causes a loss of funds for the node owner as he does not receive shares for the unattributed balance that was lost, as such, I believe high severity is appropriate.

**[Karak mitigated](https://github.com/code-423n4/2024-09-karak-mitigation?tab=readme-ov-file#scope):**
> This mitigation only burns the ETH that has already been credited to the user consequently avoiding this scenario.

**Status:** Mitigation confirmed. Full details in reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/9), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/23) and [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/21).

***

## [[H-02] The operator can create a `NativeVault` that can be silently unslashable](https://github.com/code-423n4/2024-07-karak-findings/issues/55)
*Submitted by [Fulum](https://github.com/code-423n4/2024-07-karak-findings/issues/55), also found by [lanrebayode77](https://github.com/code-423n4/2024-07-karak-findings/issues/87), Takarez ([1](https://github.com/code-423n4/2024-07-karak-findings/issues/85), [2](https://github.com/code-423n4/2024-07-karak-findings/issues/84)), ayden ([1](https://github.com/code-423n4/2024-07-karak-findings/issues/82), [2](https://github.com/code-423n4/2024-07-karak-findings/issues/81)), trachev ([1](https://github.com/code-423n4/2024-07-karak-findings/issues/78), [2](https://github.com/code-423n4/2024-07-karak-findings/issues/77)), [20centclub](https://github.com/code-423n4/2024-07-karak-findings/issues/75), [sl1](https://github.com/code-423n4/2024-07-karak-findings/issues/73), [givn](https://github.com/code-423n4/2024-07-karak-findings/issues/48), and [0xCiphky](https://github.com/code-423n4/2024-07-karak-findings/issues/5)*

An operator can create a `NativeVault` which always reverts when `slashAssets()` is called and make the vault unslashable.

### Proof of Concept

When an operator want to create a `NativeVault`, he call the `deployVaults()` function on the `Core` contract with a custom config struct to give informations for the deployment. The `extraData` params is used to give the `manager`, `slahsStore` and `nodeImplementation` addresses on the `initialize()` function of the `NativeVault`.

When an asset is allowed on the protocol, a `slashingHandler` address is associated with the asset address on the `assetSlashingHandlers` mapping.

`NativeVault` is used to make native restaking with the ETH of Beacon Chain validators of users. When a slashing is executed, the address of `assetSlashingHandlers` mapping is inserted as a params in [the call](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L134-L138) `slashAssets()` :

```solidity
    for (uint256 i = 0; i < queuedSlashing.vaults.length; i++) {
        IKarakBaseVault(queuedSlashing.vaults[i]).slashAssets(
            queuedSlashing.earmarkedStakes[i],
            self.assetSlashingHandlers[IKarakBaseVault(queuedSlashing.vaults[i]).asset()]
        );
    }
```

Then the function make this [verification](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L308) with the input address of `slashingHandler`:

```solidity
    if (slashingHandler != self.slashStore) revert NotSlashStore();
```

If the operator, during the initialization, insert a `slashStore` address which is different from the `assetSlashingHandlers` address for ETH, the call on the `NativeVault` will always revert and make the vault slash impossible.

**Proof of Concept**

You can add this PoC to the `./test/nativeRestaking/nativeVault.t.sol`:

```solidity

    function test_createUnslashableVault() public {
        //Setup
        vm.warp(1718549030);
        NativeVault unslashableVault;
        address badSlashStore = address(666);
        DSSContract dss = new DSSContract();

        vm.startPrank(address(dss));
        core.registerDSS(100000000000000000000);
        vm.stopPrank();       
        // Setup NativeNode implementation
        address nativeNodeImpl = address(new NativeNode());

        // Deploy Vaults
        VaultLib.Config[] memory vaultConfigs = new VaultLib.Config[](1);
        vaultConfigs[0] = VaultLib.Config({
            asset: Constants.DEAD_BEEF,
            decimals: 18,
            operator: operator,
            name: "NativeTestVault",
            symbol: "NTV",
            extraData: abi.encode(address(manager), badSlashStore, address(nativeNodeImpl))
        });

        vm.startPrank(operator);
        IDSS dssInterface = IDSS(address(dss));
        core.registerOperatorToDSS(dssInterface, bytes(""));
        IKarakBaseVault[] memory vaults = core.deployVaults(vaultConfigs, address(0));
        unslashableVault = NativeVault(address(vaults[0]));
        
        //Register vault staked for dss
        Operator.StakeUpdateRequest memory stakeRequest = Operator.StakeUpdateRequest({
            vault: address(unslashableVault),
            dss: dssInterface,
            toStake: true
        });
        Operator.QueuedStakeUpdate memory queuedStake = core.requestUpdateVaultStakeInDSS(stakeRequest);
        vm.warp(1718549030 + 10 days);
        core.finalizeUpdateVaultStakeInDSS(queuedStake);
        vm.stopPrank();
        
        vm.startPrank(address(dss));
        //Slash request 
        uint96[] memory slashPercentagesWad = new uint96[](1);
        slashPercentagesWad[0] = 10000000000000000000;
        address[] memory operatorVaults = new address[](1);
        operatorVaults[0] = address(unslashableVault);
        
        SlasherLib.SlashRequest memory slashingReq = SlasherLib.SlashRequest({
            operator: operator,
            slashPercentagesWad: slashPercentagesWad,
            vaults: operatorVaults
        });

        //Request and execute the slashing but revert
        SlasherLib.QueuedSlashing memory queuedSlashing = core.requestSlashing(slashingReq);
        vm.warp(1718549030 + 14 days);
        core.finalizeSlashing(queuedSlashing);
        
        vm.stopPrank();
    }
```

Add also this contract for the dss on the same file :

```solidity
    contract DSSContract {
        uint256 variable;
        constructor() {
            variable = 1;
        }
        function returnVariable() external view returns(uint256 _variable) {
            _variable = variable;
        }
    }
```

And execute the command: `forge test --mt test_createUnslashableVault -vvvvv`.

### Recommended Mitigation Steps

You can add a verified `slashingHandler` for ETH on the `Core` contract and verify the operator use the correct `slashStore` params on the `initialize()` function in the `NativeVault`, or on the `deployVaults()` for `NativeVault`.

**[dewpe (Karak) disputed and commented](https://github.com/code-423n4/2024-07-karak-findings/issues/55#issuecomment-2267609461):**
 > We think it should be re-classified to `med` because it's ultimately the DSS' responsibility to figure out which operators and vaults to take in via the `registrationHook.selector`.

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/55#issuecomment-2282714847):**
 > The crux of this issue and its duplicates is that for native vaults, `vaultConfig.extraData` has no input validation in when calling `deployVaults()`. This means the operator can set `manager`, `slashStore` and `nodeImplementation` to anything.
> 
> This issue has demonstrated how an operator can create an unslashable vault by creating it with `slashStore` as a different address than the whitelisted slashing handler for ETH. Slashing is core functionality of the protocol and being unable to do so would threaten the integrity of operators (eg. operator can act malicious, causing loss of funds, at no risk). 
> 
> https://github.com/code-423n4/2024-07-karak-findings/issues/85 describes how the setting the manager will allow the operator to call restricted functions.
> 
> As such, I believe high severity is appropriate.
> 
> > We think it should be re-classified to med because it's ultimately the DSS' responsibility to figure out which operators and vaults to take in via the `registrationHook.selector`.
> 
> Given that the DSS implementation isn't in-scope and wardens did not know what `registrationHook` could/couldn't do during the audit, I don't think it is fair to downgrade the issue based on this.

**[Karak mitigated](https://github.com/code-423n4/2024-09-karak-mitigation?tab=readme-ov-file#scope):**
> This mitigation removes the SlashStore altogether and the NativeVault itself burns the slashed ETH.

**Status:** Unmitigated. Full details in reports from [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/12) and [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/8), and also included in the [Mitigation Review](#mitigation-review) section below.

***

## [[H-03] A `DoS` on snapshots due to a rounding error in calculations](https://github.com/code-423n4/2024-07-karak-findings/issues/36)
*Submitted by [KupiaSec](https://github.com/code-423n4/2024-07-karak-findings/issues/36)*

Creating a snapshot may be impossible due to a rounding error in the calculations.

### Proof of Concept

When initiating a snapshot, some assets are transferred to the slash store within the `_transferToSlashStore()` function. The transfer amount is calculated at `L430`. However, if `convertToAssets(balanceOf(nodeOwner))` exceeds `node.totalRestakedETH` (which could occur due to a rounding error in the calculations), the transaction will revert due to an integer underflow.

```solidity
    function _transferToSlashStore(address nodeOwner) internal {
        ...

430     uint256 slashedAssets = node.totalRestakedETH - convertToAssets(balanceOf(nodeOwner));
        ...
```

Consider the following scenario:

**Note:** The exchange rate formula is `(totalAssets + 1) / (totalSupply + 1)` as defined in [ERC4626](https://github.com/vectorized/solady/blob/main/src/tokens/ERC4626.sol#L201).

1.  Current state of the `NativeVault`:
    *   `totalAssets` = 3e18
    *   `totalSupply` = 5e18
    *   Bob's share = (5e18 - 4) / 2
    *   Bob's assets = (5e18 - 4) / 2 &ast; (3e18 + 1) / (5e18 + 1) = 1.5e18 - 2
    *   `totalRestakedETH` for Bob's node = 1.5e18 - 2
2.  An increase in totalAssets by 3:
    *   Corresponding share = 3 &ast; (5e18 + 1) / (3e18 + 1) = 4
    As a result:
    *   `totalAssets` = 3e18 + 3
    *   `totalSupply` = 5e18 + 4
    *   Bob's assets = (5e18 - 4) / 2 &ast; (3e18 + 4) / (5e18 + 5) = 1.5e18 - 1
    *   `totalRestakedETH` for Bob's node = 1.5e18 - 2

In this situation, Bob's snapshot will revert because `convertToAssets(balanceOf(nodeOwner))` is greater than `node.totalRestakedETH`.

This issue arises from calculation rounding errors. As seen in the scenario above, the issue can arise even with a single increase in totalAssets, and the likelihood of this problem grows as the number of participants increases.

Additionally, attackers could exploit this vulnerability to execute a `DoS` attack on others' snapshots by transfering DUST ethers to the nodeAddress and performing the second step of the above scenario.

### Recommended Mitigation Steps

The `_transferToSlashStore()` function should be fixed as follows:

```diff
-       uint256 slashedAssets = node.totalRestakedETH - convertToAssets(balanceOf(nodeOwner));

+       uint256 slashedAssets;
+       if(node.totalRestakedETH > convertToAssets(balanceOf(nodeOwner))) {
+           slashedAssets = node.totalRestakedETH - convertToAssets(balanceOf(nodeOwner));
+       }
```

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/36#issuecomment-2282812331):**
 > The PoC provided has the following step:
> 
> > Current state of the NativeVault:
> >
> > - totalAssets = 3e18
> > - totalSupply = 5e18
> 
> How would a native vault reach such a state? 32 ETH is required to activate a validator, and the minimum balance for a validator is 16 ETH. This means a native vault with at least one active validator in it would have `totalAssets` no smaller than `16e18`.
> 
> The issue hasn't described how it is possible for the native vault to reach a state where `totalAssets = 3e18` and `totalSupply = 5e18`, and it is not immediately obvious when looking at the code.
>  
> > An increase in totalAssets by 3:
> > - Corresponding share = 3 * (5e18 + 1) / (3e18 + 1) = 4
> 
> `totalAssets` is only increased in `_increaseBalance()`, which is only called in `validateWithdrawalCredentials()` and `_updateSnapshot()`.
> 
> `validateWithdrawalCredentials()` is used to add new validators, so it is impossible that `totalAssets` increases by only 3 when it is called. Note that validator balances are in gwei on the beacon chain. Also, the calculation for assets in `_updateSnapshot()` is complex - you can't simply state that `totalAssets` will increase by 3 without describing how.
> 
> As such, I believe this issue has not demonstrated sufficient proof of how `convertToAssets(balanceOf(nodeOwner))` can become larger than `node.totalRestakedETH`.
> 
> If the warden genuinely feels that the underflow can occur, I encourage them to provide a coded PoC that interacts with `NativeVault`.

**[karan-andalusia (Karak) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/36#issuecomment-2288634004):**
 > Fixed this with:
> ```
> uint256 slashedAssets;
> // Account for rounding errors which might make convertToAssets() > totalRestakedETH
> if (node.totalRestakedETH > convertToAssets(balanceOf(nodeOwner))) {
>     // slashed ETH = total restaked ETH (node + beacon) - share price equivalent ETH
>     slashedAssets = node.totalRestakedETH - convertToAssets(balanceOf(nodeOwner));
> }
> ```

**[KupiaSec (warden) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/36#issuecomment-2292936584):**
 > Hi, @MiloTruck!
> 
> Thank you for your encouragement! I have made a coded PoC along with a detailed description.
> 
> **The primary reason for this issue**<br>
> 
> Since a nativeVault is an ERC4626, all rounding in calculations is managed for the benefit of the protocol. Consequently, if there is no slashing, the asset amount per share can only increase due to rounding. As the frequency of deposits and withdrawals rises, this effect may become more pronounced.
> 
> This could lead to overflows at [L430](https://github.com/code-423n4/2024-07-karak/blob/main/src/NativeVault.sol#L430), because `convertToAssets(balanceOf(nodeOwner))` may increase due to the aforementioned reasons.
> 
> https://github.com/code-423n4/2024-07-karak/blob/main/src/NativeVault.sol#L425-L446
> ```solidity
>     function _transferToSlashStore(address nodeOwner) internal {
>         NativeVaultLib.Storage storage self = _state();
>         NativeVaultLib.NativeNode storage node = self.ownerToNode[nodeOwner];
> 
>         // slashed ETH = total restaked ETH (node + beacon) - share price equivalent ETH
> @>      uint256 slashedAssets = node.totalRestakedETH - convertToAssets(balanceOf(nodeOwner));
>             [...]
>     }
> ```
> 
> **Note**<br>
> 
> Before detailing the coded PoC, I want to clarify that the totalAssets can increase through donations to nativeNode. The donated amount can be any arbitrary number (the original report uses 3), which may not necessarily be a multiple of GEWI.
> (Donations are feasible because the NativeNode inherits from the Ownable contract, which includes the payable function `cancelOwnershipHandover()`, allowing anyone to call it. Even in the absence of the payable function cancelOwnershipHandover(), there are alternative methods available.)
> 
> **Scenario:**
> 
> 1. Bob has a validator with 32 ETH.
>     - `totalAssets` = 32e18
>     - `totalSupply` = 32e18
>     - Bob's share = 32e18
>     - Bob's assets = 32e18
>     - `totalRestakedETH` for Bob's node = 32e18
> 2. The native vault is slashed by 2 ETH.
>     - `totalAssets` = 30e18
>     - `totalSupply` = 32e18
>     - Bob's share = 32e18
>     - Bob's assets = 30e18
>     - `totalRestakedETH` for Bob's node = 30e18
> 3. An attacker creates a node and sends 15 wei to it.
>     - Corresponding share = 15 * (32e18 + 1) / (30e18 + 1) = 15
>     
>     As a result:
>     - `totalAssets` = 30e18 + 15
>     - `totalSupply` = 32e18 + 15
> 4. The attacker creates another node and sends 15 wei to it.
>     - Corresponding share = 15 * (32e18 + 16) / (30e18 + 16) = 15
>     
>     As a result:
>     - `totalAssets` = 30e18 + 30
>     - `totalSupply` = 32e18 + 30
>     - Bob's assets = 32e18 * (30e18 + 31) / (32e18 + 31) = 30e18 + 1
>     - `totalRestakedETH` for Bob's node = 30e18
> 
> In this situation, Bob's snapshot will revert because `convertToAssets(balanceOf(nodeOwner))` is greater than `node.totalRestakedETH`.
> 
> **Coded PoC**
> 
> In this PoC, I only utilize Bob's native node, not his validator, which does not result in any numerical differences.
> 
> **Before testing**, insert the following code snippet into `NativeVault.sol`, as there is no view function to retrieve the value of `totalRestakedETH` for nodes.
> 
> <details>
> 
> ```solidity
> NativeVault.sol
> 
>     function getNodeTotalRestakedETH(address owner) external view returns (uint256 totalRestakedETH) {
>         return _state().ownerToNode[owner].totalRestakedETH;
>     }
> ```
> 
> ```solidity
> NativeVault.t.sol
> 
>     function test_rounding_error() public {
> 
>         ////////////
>         // STEP 1 //
>         ////////////
> 
>         // Consider Bob's address to be address(this)
>         // Create bob's node
>         address bobNode = nativeVault.createNode();
>         assertEq(nativeVault.getNodeOwner(bobNode), address(this));
> 
>         // Bob's node has 32 ETH (the same effect as if Bob had a validator.)
>         vm.deal(bobNode, 32 ether);
>         assertEq(bobNode.balance, 32 ether);
> 
>         // Need to store the parent root before starting the snapshot
>         vm.store(Constants.BEACON_ROOTS_ADDRESS, timestamp_idx(block.timestamp), bytes32(uint256(block.timestamp)));
>         vm.store(Constants.BEACON_ROOTS_ADDRESS, root_idx(block.timestamp), bytes32(0x00));
>         // start snapshot
>         nativeVault.startSnapshot(false);
> 
>         assertEq(nativeVault.getNodeTotalRestakedETH(address(this)), 32 ether);
>         assertEq(nativeVault.convertToAssets(nativeVault.balanceOf(address(this))), 32 ether);
>         assertEq(nativeVault.totalAssets(), 32 ether);
> 
>         ////////////
>         // STEP 2 //
>         ////////////
> 
>         vm.prank(address(core));
>         nativeVault.slashAssets(2 ether, address(slashStore));
>         // slashing succesfull
>         assertEq(nativeVault.totalAssets(), 30 ether);
>         
>         vm.prank(address(this));
>         // During a call to startSnapshot, slashed ether will be swept and totalRestakedETH decremented
>         nativeVault.startSnapshot(false);
>         
>         assertEq(nativeVault.getNodeTotalRestakedETH(address(this)), 30 ether);
>         assertEq(nativeVault.convertToAssets(nativeVault.balanceOf(address(this))), 30 ether);
>         assertEq(nativeVault.totalAssets(), 30 ether);
> 
>         ////////////
>         // STEP 3 //
>         ////////////
> 
>         // Create the attacker's node
>         address attacker = address(2);
>         vm.prank(attacker);
>         address attackerNode = nativeVault.createNode();
>         assertEq(nativeVault.getNodeOwner(attackerNode), attacker);
> 
>         // The attacker sends 15 wei to the node
>         vm.deal(attackerNode, 15 wei);
>         assertEq(attackerNode.balance, 15 wei);
> 
>         vm.prank(attacker);
>         nativeVault.startSnapshot(false);
>         
>         assertEq(nativeVault.getNodeTotalRestakedETH(address(this)), 30 ether);
>         assertEq(nativeVault.convertToAssets(nativeVault.balanceOf(address(this))), 30 ether);
>         assertEq(nativeVault.totalAssets(), 30 ether + 15 wei);
> 
>         ////////////
>         // STEP 4 //
>         ////////////
> 
>         // Create the attacker's another node
>         address attacker3 = address(3);
>         vm.prank(attacker3);
>         address attackerNode3 = nativeVault.createNode();
>         assertEq(nativeVault.getNodeOwner(attackerNode3), attacker3);
> 
>         // The attacker sends 15 wei to the node
>         vm.deal(attackerNode3, 15 wei);
>         assertEq(attackerNode3.balance, 15 wei);
> 
>         vm.prank(attacker3);
>         nativeVault.startSnapshot(false);
>         
>         // The following illustrates the difference between the two values, which will revert Bob's snapshot
>         assertEq(nativeVault.getNodeTotalRestakedETH(address(this)), 30 ether);
>         assertEq(nativeVault.convertToAssets(nativeVault.balanceOf(address(this))), 30 ether + 1 wei);
> 
>         // Bob's snapshot will revert
>         vm.prank(address(this));
>         vm.expectRevert();
>         nativeVault.startSnapshot(false);
>     }
> ```
> 
> </details>

**[MiloTruck (judge) increased severity to High and commented](https://github.com/code-423n4/2024-07-karak-findings/issues/36#issuecomment-2299640443):**
 > Thanks for providing the detailed example and the coded PoC, this is a good find!
> 
> `_startSnapshot()` is core functionality of the native vault, and if it is permanently DOSed, the user [might not be able to withdraw](https://github.com/code-423n4/2024-07-karak/blob/main/src/NativeVault.sol#L243-L245). As such, I believe this warrants high severity.
> 
> > totalRestakedEth for Bob will only decrease when shares are being burnt, which happens during update! so it remains 32e18!
> 
> We assume `startSnapshot()` is called once in step 2 before the attack is performed.

**[Karak mitigated](https://github.com/code-423n4/2024-09-karak-mitigation?tab=readme-ov-file#scope):**
> This mitigation introduces a check for the rounding error.

**Status:** Mitigation confirmed. Full details in reports from [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/13), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/25) and [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/7).

***

## [[H-04] Violation of Invariant Allowing DSSs to Slash Unregistered Operators](https://github.com/code-423n4/2024-07-karak-findings/issues/4)
*Submitted by [0xCiphky](https://github.com/code-423n4/2024-07-karak-findings/issues/4), also found by [20centclub](https://github.com/code-423n4/2024-07-karak-findings/issues/103) and [KupiaSec](https://github.com/code-423n4/2024-07-karak-findings/issues/42)*

Operators select DSSs to allocate their funds to, and for each DSS, they can choose a list of vaults to stake. The selected vaults are fully staked to the chosen DSS.

To unstake, the operator must adhere to a `MIN_STAKE_UPDATE_DELAY` of 9 days, which prevents front-running a slashing event. An operator can fully unregister from a DSS once all the vaults are unstaked. The DSS has the ability to slash any malicious behaviour that occurred before the withdrawal initiation for up to 7 days.

The problem is, the current implementation breaks one of the main invariants (confirmed by sponsors).

* [**Only DSSs an operator is registered with can slash said operator**](https://code4rena.com/audits/2024-07-karak-restaking#top:\~:text=Only%20DSSs%20an%20operator%20is%20registered%20with%20can%20slash%20said%20operator)

Consider the following scenario:

* Operator calls the [requestUpdateVaultStakeInDSS](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L130) function to unstake Vault 1 from DSS, initiating the 9-day `MIN_STAKE_UPDATE_DELAY`.
* After 8 days, the DSS calls the [requestSlashing](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L220) function on Operator’s Vault 1, starting the 2-day `SLASHING_VETO_WINDOW`.
* After 1 day, the operator calls the [finalizeUnstaking](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L248C14-L248C30) function to finalize unstaking Vault 1 from DSS and the [unregisterOperatorFromDSS](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L113C14-L113C39) function to unregister from DSS.
* After another day, the DSS (or anyone) calls the [finalizeSlashing](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L248C14-L248C30) function to finalize the slash on the operator, who is no longer registered with the DSS.

### Proof Of Concept

```solidity
    function test_slash_unregistered_operator() public {
        // register operator to dss, deploy vaults, stake vaults to dss
        stake_vaults_to_dss();
        // check if operator is registered to dss
        assertEq(true, core.isOperatorRegisteredToDSS(operator, dss));
        // unstake vaults from dss
        address[] memory operatorVaults = core.fetchVaultsStakedInDSS(operator, dss);
        Operator.StakeUpdateRequest memory stakeUpdate =
            Operator.StakeUpdateRequest({vault: operatorVaults[0], dss: dss, toStake: false});
        Operator.StakeUpdateRequest memory stakeUpdate2 =
            Operator.StakeUpdateRequest({vault: operatorVaults[1], dss: dss, toStake: false});
        vm.startPrank(operator);
        Operator.QueuedStakeUpdate memory queuedStakeUpdate = core.requestUpdateVaultStakeInDSS(stakeUpdate);
        Operator.QueuedStakeUpdate memory queuedStakeUpdate2 = core.requestUpdateVaultStakeInDSS(stakeUpdate2);
        vm.stopPrank();
        skip(8 days);
        // dss request slashing
        uint96[] memory slashPercentagesWad = new uint96[](2);
        slashPercentagesWad[0] = uint96(10e18);
        slashPercentagesWad[1] = uint96(10e18);
        SlasherLib.SlashRequest memory slashingReq = SlasherLib.SlashRequest({
            operator: operator,
            slashPercentagesWad: slashPercentagesWad,
            vaults: operatorVaults
        });
        vm.startPrank(address(dss));
        SlasherLib.QueuedSlashing memory queuedSlashing = core.requestSlashing(slashingReq);
        vm.stopPrank();
        skip(1 days);
        vm.startPrank(operator);
        // finalize unstake and unregister operator from dss
        core.finalizeUpdateVaultStakeInDSS(queuedStakeUpdate);
        core.finalizeUpdateVaultStakeInDSS(queuedStakeUpdate2);
        core.unregisterOperatorFromDSS(dss, "");
        vm.stopPrank();
        skip(1 days);
        // check if operator is unregistered from dss
        assertEq(false, core.isOperatorRegisteredToDSS(operator, dss));
        // finalize slashing on unregistered operator
        core.finalizeSlashing(queuedSlashing);
    }
```

### Impact

The current implementation allows a DSS to finalize a slash on an operator even after the operator has unregistered from the DSS. This breaks the intended invariant that only DSSs an operator is registered with can slash that operator.

Additionally, new users depositing into the DSS may be unaware of the pending slash because the operator is no longer registered with the DSS, leading to a potential loss on deposits.

### Tools Used

*   Manual analysis
*   Foundry

### Recommendation

Consider implementing a check to prevent operators from unregistering from a DSS that has pending slashing requests on them. This will ensure that all slashing actions are finalized before an operator can fully unregister, thereby maintaining the intended invariant/design.

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/4#issuecomment-2282771207):**
 > This issue demonstrates how if `requestSlashing()` was called in the last 2 days of a vault unstake request (ie. during `SLASHING_VETO_WINDOW`), `finalizeSlashing()` can be called on a vault even after its operator has unregistered the DSS with `unregisterOperatorFromDSS()`.
> 
> Given that this breaks one of the main invariants stated in the README, I believe high severity is appropriate.

**[devdks25 (Karak) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/4#issuecomment-2284442197):**
 > Fixed.

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/4#issuecomment-2295195355):**
 > There have been comments that this should be downgraded as the veto committee has the ability to stop it, but I disagree. Following this reasoning, all issues related to slashing can be downgraded to QA since the veto committee can simply stop all slashings-related bugs.
> 
> It's not clear when the veto committee will overturn a slashing and for what reasons, as such, we cannot assume this will be stopped by the committee. I think a fair assumption to make is the committee will overturn any slashings that are outright malicious, which isn't the case here.
> 
> This remains as high.

**[Karak mitigated](https://github.com/code-423n4/2024-09-karak-mitigation?tab=readme-ov-file#scope):**
> This mitigation validates the operator, vaults status in the finalizing slashing.

**Status:** Mitigation confirmed. Full details in reports from [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/14) and [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/6).

***

 
# Medium Risk Findings (5)
## [[M-01] Changing the `slashingHandler` for `NativeVaults` will DoS slashing](https://github.com/code-423n4/2024-07-karak-findings/issues/49)
*Submitted by [givn](https://github.com/code-423n4/2024-07-karak-findings/issues/49), also found by [sl1](https://github.com/code-423n4/2024-07-karak-findings/issues/68)*

Relevant code: [NativeVault::slashAssets](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/NativeVault.sol#L308)

### Description

`Core`'s state contains a mapping between assets and slashingHandlers - `assetSlashingHandlers`. Assets that are assigned a slashing handler are considered whitelisted and vaults containing this asset can be deployed.

When a slash is performed on a vault, the following function is called:

```js
function slashAssets(
	uint256 slashPercentageWad, address slashingHandler
) external returns (uint256 transferAmount);
```

Inside `NativeVault`'s implementation of the function, the following check is performed:

```js
if (slashingHandler != self.slashStore) revert NotSlashStore();
```

This check can be problematic in the case where some number of native vaults have been deployed and the `assetSlashingHandlers` mapping gets updated with a new `slashHandler`. Then, the `slashAssets` function will always revert.

### Root Cause

Unsynchronized state update causes DoS of slashing functionality inside `NativeVault`.

### Impact

If the `slashingHandler` for restaking gets changed at some point it will cause DoS of the slashing functionality for `NativeVaults`. It will be blocked until contracts get updated with new implementation or old slashing handler is restored.

> The issue could be exacerbated if some new `NativeVaults` get deployed with the new slash handler. Then, reverting to the old slash handler will break the newly deployed `NativeVaults`, making update implementation the only option to fix this issue. But this is also troublesome because only normal `Vaults` can have they're implementation updated for all deployments with one call. `NativeVaults` would have to be updated one by one.

### Proof of Concept

Let's suppose the following scenario:

1.  SlashHandler `A` is set for key `Constants.DEAD_BEEF` in `assetSlashingHandlers`.
2.  5 `NativeVaults` get deployed with this slash handler.
3.  SlashHandler `B` is set for `Constants.DEAD_BEEF`.
4.  These 5 `NativeVaults` would not be slashable until some measures are taken - either `A` restored or the vault's implementations are updated.

Both approaches to fix this issue once it has occurred have their drawbacks:

* If after **step 3**, more `NativeVaults` are created updating the allowlist will always cause some group of native vaults to be **unslashable**.
* Changing the `NativeVault` implementations can only be done only 1 by 1, since the default vault implementation is for normal `Vault`.

### Suggested Mitigation

There are multiple ways to prevent this issue:

* Function only callable by `Manager` to change `slashStore`
* Function in `Core` that is callable by anyone which changes the underlying `slashStore` of a `NativeVault` if there is a difference. Allowing users to unDoS themselves.
* Remove the check causing the revert in `slashAssets` and allow `NativeVaults` to have different `slashHandlers` if this is acceptable.

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/49#issuecomment-2282720677):**
 > While technically valid, will need to confirm with the sponsor if slashing handlers are meant to be changed or if `allowlistAssets()` is just meant to set the slashing handler once.

**[karan-andalusia (Karak) confirmed](https://github.com/code-423n4/2024-07-karak-findings/issues/49#event-14458579112)**

***

## [[M-02] A snapshot may face a permanent DoS if both a slashing event occurs in the `NativeVault` and the staker's validator is penalized](https://github.com/code-423n4/2024-07-karak-findings/issues/31)
*Submitted by [KupiaSec](https://github.com/code-423n4/2024-07-karak-findings/issues/31)*

<https://github.com/code-423n4/2024-07-karak/blob/main/src/NativeVault.sol#L126-L162><br>
<https://github.com/code-423n4/2024-07-karak/blob/main/src/NativeVault.sol#L476-L495><br>
<https://github.com/code-423n4/2024-07-karak/blob/main/src/NativeVault.sol#L517-L525><br>
<https://github.com/code-423n4/2024-07-karak/blob/main/src/NativeVault.sol#L507-L515>

### Impact

The staker can still receive rewards even if their validators are empty.

### Proof of Concept

Let's consider the following scenario:

1.  Alice has a validator whose balance is 32 ETH.

2.  A slashing event occurs in the NativeVault, resulting in Alice being slashed by 2 ETH, reducing her withdrawable assets to 30 ETH.

3.  Subsequently, Alice's validator loses all its funds (this can happen within the 7-day snapshot period, even within just 1 hour). A snapshot should then be taken to reduce Alice's assets by 32 ETH, which requires calling the `validateSnapshotProofs()` function for Alice's validator.

    ```solidity
        function validateSnapshotProofs(
            address nodeOwner,
            BeaconProofs.BalanceProof[] calldata balanceProofs,
            BeaconProofs.BalanceContainer calldata balanceContainer
        )
            external
            nonReentrant
            nodeExists(nodeOwner)
            whenFunctionNotPaused(Constants.PAUSE_NATIVEVAULT_VALIDATE_SNAPSHOT)
        {
            NativeVaultLib.Storage storage self = _state();
            NativeVaultLib.NativeNode storage node = self.ownerToNode[nodeOwner];
            NativeVaultLib.Snapshot memory snapshot = node.currentSnapshot;

            if (node.currentSnapshotTimestamp == 0) revert NoActiveSnapshot();

            BeaconProofs.validateBalanceContainer(snapshot.parentBeaconBlockRoot, balanceContainer);

            for (uint256 i = 0; i < balanceProofs.length; i++) {
                NativeVaultLib.ValidatorDetails memory validatorDetails =
                    node.validatorPubkeyHashToDetails[balanceProofs[i].pubkeyHash];

                if (validatorDetails.status != NativeVaultLib.ValidatorStatus.ACTIVE) revert InactiveValidator();
                if (validatorDetails.lastBalanceUpdateTimestamp >= node.currentSnapshotTimestamp) {
                    revert ValidatorAlreadyProved();
                }

    153         int256 balanceDeltaWei = self.validateSnapshotProof(
                    nodeOwner, validatorDetails, balanceContainer.containerRoot, balanceProofs[i]
                );

                snapshot.remainingProofs--;
    158         snapshot.balanceDeltaWei += balanceDeltaWei;
            }

    161     _updateSnapshot(node, snapshot, nodeOwner);
        }
    ```

    In this call, `snapshot.balanceDeltaWei` is set to -32 ETH, and the `_updateSnapshot()` function is invoked at `L161`.

4.  In the `_updateSnapshot()` function, the `_updateBalance()` function is invoked at `L490`, with `totalDeltaWei = -32 ETH`, as noted at `L482`.
    ```solidity
        function _updateSnapshot(
            NativeVaultLib.NativeNode storage node,
            NativeVaultLib.Snapshot memory snapshot,
            address nodeOwner
        ) internal {
            if (snapshot.remainingProofs == 0) {
    482         int256 totalDeltaWei = int256(snapshot.nodeBalanceWei) + snapshot.balanceDeltaWei;

                node.withdrawableCreditedNodeETH += snapshot.nodeBalanceWei;

                node.lastSnapshotTimestamp = node.currentSnapshotTimestamp;
                delete node.currentSnapshotTimestamp;
                delete node.currentSnapshot;

    490         _updateBalance(nodeOwner, totalDeltaWei);
                emit SnapshotFinished(nodeOwner, node.nodeAddress, node.lastSnapshotTimestamp, totalDeltaWei);
            } else {
                node.currentSnapshot = snapshot;
            }
        }
    ```

5.  Within the `_updateBalance()` function, the `_decreaseBalance()` function is called at `L521` with a decrease amount of 32 ETH.
    ```solidity
        function _updateBalance(address _of, int256 assets) internal {
            if (assets > 0) {
                _increaseBalance(_of, uint256(assets));
            } else if (assets < 0) {
    521         _decreaseBalance(_of, uint256(-assets));
            } else {
                return;
            }
        }
    ```

6.  In the `_decreaseBalance()` function, shares are burned at `L511`.
    ```solidity
        function _decreaseBalance(address _of, uint256 assets) internal {
            NativeVaultLib.Storage storage self = _state();
            uint256 shares = previewWithdraw(assets);
            _beforeWithdraw(assets, shares);
    511     _burn(_of, shares);
            self.totalAssets -= assets;
            self.ownerToNode[_of].totalRestakedETH -= assets;
            emit DecreasedBalance(self.ownerToNode[_of].totalRestakedETH);
        }
    ```

The problem arises in step 6. Here, the shares being burned correspond to 32 ETH, while Alice's shares only amount to 30 ETH. This discrepancy causes the burn amount to exceed the existing share amount, leading to a reversal of the transaction. As a result, the snapshot for Alice will be permanently `DoS`ed, and her share amount will remain unchanged. Consequently, Alice can still receive rewards even though her validator is empty. In addition, nobody can call `validateExpiredSnapshot()` for Alice.

A malicious staker could intentionally orchestrate this scenario by creating a validator and engaging in malicious behavior. If the malicious action fails, they would lose all 32 ETH but could still receive rewards.

### Recommended Mitigation Steps

The `_decreaseBalance()` function should be enhanced to properly handle cases where the burning amount exceeds the existing share amount.

For example:

```diff
    function _decreaseBalance(address _of, uint256 assets) internal {
        NativeVaultLib.Storage storage self = _state();
-       uint256 shares = convertToShares(assets);
+       uint256 shares = Math.min(convertToShares(assets), balanceOf(nodeOwner));
        _beforeWithdraw(assets, shares);
        _burn(_of, shares);
        self.totalAssets -= assets;
        self.ownerToNode[_of].totalRestakedETH -= assets;
        emit DecreasedBalance(self.ownerToNode[_of].totalRestakedETH);
    }
```

**[MiloTruck (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2024-07-karak-findings/issues/31#issuecomment-2269752165):**
 > > Subsequently, Alice's validator loses all its funds (this can happen within the 7-day snapshot period, even within just 1 hour). A snapshot should then be taken to reduce Alice's assets by 32 ETH
> 
> A validator getting slashed for its entire effective balance is only possible due to the correlation penalty, which is _extremely_ unlikely. [Historically, most slashing penalties have been around 1 ETH](https://consensys.io/blog/understanding-slashing-in-ethereum-staking-its-importance-and-consequences).
> 
> The following conditions must be met for this issue to occur:
> 
> - Karak slashes an operator for X amount, followed by beacon chain slashing for Y amount.
> - X +Y must exceed the node owner's total restaked balance.
> 
> I believe this is extremely unlikely to occur, and as such, medium severity is appropriate.

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/31#issuecomment-2298934407):**
 > According to the [C4 severity categorization](https://docs.code4rena.com/awarding/judging-criteria/severity-categorization), the criteria for medium severity is:
> 
> > 2 — Med: Assets not at direct risk, but the function of the protocol or its availability could be impacted, or leak value with a hypothetical attack path with stated assumptions, but external requirements.
> 
> As stated in my initial comment, this requires both Karak and beacon chain slashing to occur. The upper limit of how much the DSS can slash an account for is 100%:
> 
> ```solidity
>     uint256 public constant HUNDRED_PERCENT_WAD = 100e18;
>     uint256 public constant MAX_SLASHING_PERCENT_WAD = HUNDRED_PERCENT_WAD;
> ```
> 
> Therefore, it is possible for the sum of Karak and beacon chain slashing to exceed 100%, however unlikely. I view this as "with stated assumptions, but external requirements".
> 
> Regarding impact, `validateSnapshotProofs()` and `validateExpiredSnapshot()` will permanently be DOSed for the user - they can't stake into the vault in the future even if they would like to. This fulfills "the function of the protocol or its availability could be impacted".
> 
> As such, this remains as medium.

**[karan-andalusia (Karak) confirmed](https://github.com/code-423n4/2024-07-karak-findings/issues/31#event-14458620503)**

**[Karak mitigated](https://github.com/code-423n4/2024-09-karak-mitigation?tab=readme-ov-file#scope):**
> This mitigation accounts for the decrease in balance of the users shares before burning.

**Status:** Mitigation confirmed. Full details in reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/2), sl1 ([1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/15), [2](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/16)) and [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/27).

***

## [[M-03] When malicious behavior occurs and DSS requests slashing against vault during 2 day period after `SLASHING_WINDOW` of 7 days is passed after staker initiates a withdrawal, token amount to be slashed is calculated to be higher than what it should be](https://github.com/code-423n4/2024-07-karak-findings/issues/17)
*Submitted by [rbserver](https://github.com/code-423n4/2024-07-karak-findings/issues/17), also found by [MrValioBg](https://github.com/code-423n4/2024-07-karak-findings/issues/66) and [Shaheen](https://github.com/code-423n4/2024-07-karak-findings/issues/24)*

### Impact

According to <https://github.com/code-423n4/2024-07-karak?tab=readme-ov-file#stakers>, `Stakers can initiate a withdrawal, subject to a ``MIN_WITHDRAW_DELAY`` of 9 days`, and `DSS can slash any malicious behavior occurring before the withdrawal initiation for up to 7 days`. Since `MIN_WITHDRAW_DELAY` equals `SLASHING_WINDOW + SLASHING_VETO_WINDOW`, the staker's withdrawal should be safe without being associated with any malicious behavior and hence not slashable when the DSS does not request any slashing against the corresponding vault within the `SLASHING_WINDOW` of 7 days after such withdrawal is initiated.

<https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/interfaces/Constants.sol#L13-L16>

```solidity
    uint256 public constant SLASHING_WINDOW = 7 days;
    uint256 public constant SLASHING_VETO_WINDOW = 2 days;
    uint256 public constant MIN_STAKE_UPDATE_DELAY = SLASHING_WINDOW + SLASHING_VETO_WINDOW;
    uint256 public constant MIN_WITHDRAWAL_DELAY = SLASHING_WINDOW + SLASHING_VETO_WINDOW;
```

During the 2 day period after the `SLASHING_WINDOW` of 7 days is passed after the staker's withdrawal is initiated, such as when just couple minutes are passed after such `SLASHING_WINDOW` is reached, the staker's withdrawal cannot be finished but a malicious behavior can occur and the DSS can make a slashing request against the corresponding vault; in this situation, the staker's withdrawal mentioned previously should not be subject to such slashing though. At that time, when the DSS calls the `Core.requestSlashing` function, which further calls the `SlasherLib.requestSlashing` and `SlasherLib.fetchEarmarkedStakes` functions, the token amount to be slashed is calculated as the DSS's allowed slashing percentage of the vault's `totalAssets()`, which includes the token amount corresponding to the staker's withdrawal. Because the staker's withdrawal should not be slashable, the token amount to be slashed actually should be the DSS's allowed slashing percentage of a value that equals the vault's `totalAssets()` minus the token amount corresponding to the staker's withdrawal. Thus, the DSS can unfairly slash more underlying token amount from the vault than it should be allowed.

<https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/SlasherLib.sol#L94-L124>

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
        ...
    }
```

<https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/SlasherLib.sol#L79-L92>

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

Moreover, when the `MIN_WITHDRAW_DELAY` of 9 days is passed after the staker's withdrawal is initiated, the staker can call the `Vault.finishRedeem` function for finishing his withdrawal request. Since `SLASHING_VETO_WINDOW` is 2 days, the DSS can call the `Core.finalizeSlashing` function for finalizing its slashing request just after the `MIN_WITHDRAW_DELAY` of 9 days is passed after the initiation of the staker's withdrawal if the DSS's slashing request was made when just couple minutes were passed after the `SLASHING_WINDOW` for such withdrawal of the staker was reached. Because both the staker's `Vault.finishRedeem` transaction and the DSS's `Core.finalizeSlashing` transaction are sent at the similar time, a malicious miner can place and execute the DSS's `Core.finalizeSlashing` transaction before the staker's `Vault.finishRedeem` transaction. In this case, the staker's withdrawal can be unfairly slashed by the DSS even though it should not be slashed.

<https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Vault.sol#L157-L188>

```solidity
    function finishRedeem(bytes32 withdrawalKey)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_VAULT_FINISH_REDEEM)
    {
        (VaultLib.State storage state, VaultLib.Config storage config) = _storage();

        WithdrawLib.QueuedWithdrawal memory startedWithdrawal = state.validateQueuedWithdrawal(withdrawalKey);
        ...
    }
```

<https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/VaultLib.sol#L24-L38>

```solidity
    function validateQueuedWithdrawal(State storage self, bytes32 withdrawalKey)
        internal
        view
        returns (WithdrawLib.QueuedWithdrawal memory qdWithdrawal)
    {
        qdWithdrawal = self.withdrawalMap[withdrawalKey];
        ...
        if (qdWithdrawal.start + Constants.MIN_WITHDRAWAL_DELAY > block.timestamp) {
            revert MinWithdrawDelayNotPassed();
        }
    }
```

<https://github.com/code-423n4/2024-07-karak/blob/53eb78ebda718d752023db4faff4ab1567327db4/src/Core.sol#L248-L256>

```solidity
    function finalizeSlashing(SlasherLib.QueuedSlashing memory queuedSlashing)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_CORE_FINALIZE_SLASHING)
    {
        _self().finalizeSlashing(queuedSlashing);
        ...
    }
```

<https://github.com/code-423n4/2024-07-karak/blob/d19a4de35bcaf31ccec8bccd36e2d26594d05aad/src/entities/SlasherLib.sol#L126-L151>

```solidity
    function finalizeSlashing(CoreLib.Storage storage self, QueuedSlashing memory queuedSlashing) external {
        ...
        if (queuedSlashing.timestamp + Constants.SLASHING_VETO_WINDOW > block.timestamp) {
            revert MinSlashingDelayNotPassed();
        }
        ...
    }
```

### Proof of Concept

The following steps can occur for the first described scenario where the DSS unfairly slashes more underlying token amount from the vault than it should be allowed.

1.  The vault holds `100` underlying tokens and has only one staker who owns `100` shares.
2.  On Day 0, the staker initiates his withdrawal for redeeming `50` shares.
3.  During the `SLASHING_WINDOW` of 7 days between Day 0 and Day 7, no malicious behavior occurs, and the DSS does not request any slashing against the vault.
    *   Therefore, the staker's withdrawal is safe and should not be slashable, and the staker should receive `50 * 100 / 100 = 50` underlying tokens for his withdrawal.
4.  On Day 8, a malicious behavior occurs, and the DSS with a 50% allowed slashing percentage requests to slash the vault.
    *   The token amount to be slashed by the DSS is calculated to be `50% * 100 = 50` underlying tokens.
    *   However, because the staker's withdrawal initiated on Day 0 should not be subject to such slashing, the token amount to be slashed by the DSS should equal `50% * (100 - 50) = 25` underlying tokens.
5.  On Day 9, the `MIN_WITHDRAW_DELAY` of 9 days is passed after the initiation of the staker's withdrawal so the staker finishes his withdrawal and does receive `50 * 100 / 100 = 50` underlying tokens from the vault.
6.  On Day 10, the DSS finalizes the slashing and slashes `50` underlying tokens calculated on Day 8, which is more than `25` underlying tokens that it should slash, from the vault. Therefore, the DSS unfairly slashes more underlying tokens from the vault than it should be allowed.

The following steps can occur for the second described scenario where the staker's withdrawal can be unfairly slashed by the DSS even though it should not be slashed.

1.  The vault holds `150` underlying tokens and has two stakers in which Staker A owns `100` shares and Staker B owns `50` shares.
2.  On Day 0, Staker A initiates his withdrawal for redeeming `100` shares.
3.  During the `SLASHING_WINDOW` of 7 days between Day 0 and Day 7, no malicious behavior occurs, and the DSS does not request any slashing against the vault.
    *   Therefore, Staker A's withdrawal is safe and should not be slashable, and Staker A should receive `100 * 150 / (100 + 50) = 100` underlying tokens for his withdrawal.
4.  A malicious behavior occurs when 3 minutes are passed after Day 7 starts, and the DSS with a 50% allowed slashing percentage requests to slash the vault when 5 minutes are passed after Day 7 starts.
    *   The token amount to be slashed by the DSS is calculated to be `50% * 150 = 75` underlying tokens.
    *   However, because Staker A's withdrawal initiated on Day 0 should not be subject to such slashing, the token amount to be slashed by the DSS should equal `50% * (150 - 100) = 25` underlying tokens.
5.  On Day 9, the `MIN_WITHDRAW_DELAY` of 9 days is passed after the initiation of Staker A's withdrawal so the staker calls the `Vault.finishRedeem` function for finishing his withdrawal.
6.  When 5 minutes are passed after Day 9 starts, the DSS calls the `Core.finalizeSlashing` function for finalizing the slashing.
7.  Both Staker A's `Vault.finishRedeem` transaction and the DSS's `Core.finalizeSlashing` transaction are sent at the similar time, a malicious miner places and executes the DSS's `Core.finalizeSlashing` transaction before Staker A's `Vault.finishRedeem` transaction.
8.  When the DSS's `Core.finalizeSlashing` transaction is executed, the DSS slashes `75` underlying tokens calculated in Step 4, which is more than `25` underlying tokens that it should slash, from the vault.
9.  When Staker A's `Vault.finishRedeem` transaction is executed, Staker A receives `100 * (150 - 75) / (100 + 50) = 50` underlying tokens, which is less than `100` underlying tokens that it should receive for his withdrawal, from the vault. Therefore, Staker A's withdrawal is unfairly slashed by the DSS even though it should not be slashed.

### Recommended Mitigation Steps

One way to mitigate this issue is to update the `Vault.finishRedeem` function to allow the staker's withdrawal to be finished after the `SLASHING_WINDOW` of 7 days is passed after such withdrawal is initiated if the DSS does not request any slashing against the corresponding vault within such `SLASHING_WINDOW` and only enforce the `MIN_WITHDRAW_DELAY` of 9 days on the withdrawal if the DSS has requested to slash the corresponding vault within such `SLASHING_WINDOW`.

**[devdks25 (Karak) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/17#issuecomment-2262922222):**
 > Ideally the protocol doesn't provide any financial advantage (to any individual) for slashing any operator. So if a staker provides a quite low priority gas fees then it might stay in the mempool for long enough to be slashed for activity which it wasn't responsible for.
> So imo the staker's should provide enough priority gas fees for `finishRedeem` to prevent the aforementioned. cc: @dewpe

**[dewpe (Karak) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/17#issuecomment-2267637914):**
 > Would re-rate to a non-issue.
> 
> From a technical standpoint, the user can withdraw on the exact second that the 9th day hits but they could delay it forever if they really wanted. It's ultimately up to them to finish it in a timely manner. On the frontend we can state "hey a slashing has started so withdraw your funds right when the 9th day hits". In normal operations, a DSS would be written so that it slashes users from only the active operator set and that code can be reviewed by stakers and operators before depositing and delegating. If you let them out at 7 days if there isn't a slashing request then you may run into some timing games if there is a slashing request occurs then.
> 
> From a philosophical standpoint, DSS contracts would be audited and agreeing to allocate assets to them or to an operator that allocates to them means you agree to whatever slashing conditions the DSS implements. In theory, the DSS can slash you for 100% right when you register if it wanted.

**[devdks25 (Karak) disputed](https://github.com/code-423n4/2024-07-karak-findings/issues/17#event-13767280482)**

**[MiloTruck (judge) decreased severity to Medium and commented](https://github.com/code-423n4/2024-07-karak-findings/issues/17#issuecomment-2282776011):**
 > Agree that the second scenario is unrealistic - it is entirely the staker's responsibility to ensure they withdraw their funds on time. If the staker calls `finishRedeem()` with a priority fee so low that his transaction remains in the mempool for an extended period of time, it is considered a user mistake.
> 
> However, the warden does make a valid point that calculating the amount of assets to slash based on `totalAssets()` will include assets queued for withdrawal.
> 
> @dewpe Isn't it an issue if the DSS ends up slashing a higher percentage of the remaining assets? For example:
> 
> - Vault has 100 tokens.
> - User requests a withdrawal of 50 tokens.
> - DSS calls `requestSlashing()` to slash 50% of assets, which is calculated as 50 tokens.
> - User withdraws 50 tokens.
> - `finalizeSlashing()` slashes the remaining 50 tokens, leaving 0 tokens in the vault.
> 
> Shouldn't the calculation in `requestSlashing()` exclude assets in pending withdrawals that have passed `SLASHING_WINDOW`? Although in practice this isn't easy to implement.

**[devdks25 (Karak) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/17#issuecomment-2284891262):**
 > @MiloTruck, the example seems apt and to mitigate it we are thinking of computing `earmarkedStakes` during `finalizeSlashing`, this way all the stakers that are staked will only be slashed.

**[devdks25 (Karak) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/17#issuecomment-2301385817):**
 > Fixed.

**[Karak mitigated](https://github.com/code-423n4/2024-09-karak-mitigation?tab=readme-ov-file#scope):**
> This mitigation computes the slashing amount in finalize slashing.

**Status:** Mitigation confirmed. Full details in reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/3), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/28) and [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/17).

***

## [[M-04] Delayed slashing window and lack of transparency for pending slashes could lead to loss of funds](https://github.com/code-423n4/2024-07-karak-findings/issues/15)
*Submitted by [0xCiphky](https://github.com/code-423n4/2024-07-karak-findings/issues/15), also found by [Takarez](https://github.com/code-423n4/2024-07-karak-findings/issues/105), [lanrebayode77](https://github.com/code-423n4/2024-07-karak-findings/issues/88), and [KupiaSec](https://github.com/code-423n4/2024-07-karak-findings/issues/35)*

Stakers can provide liquidity by choosing an operator and vault to deposit their assets with. In return, they receive shares based on their deposited amount relative to the total assets in the vault.

DSSs have the right to slash vaults if it feels that an operator has failed to perform its tasks adequately. DSSs are subjected to a delay of 2 days (represented by `VETO_WINDOW`) before a slashing can be finalized. This allows the slashing committee to veto a slashing event if it feels that the slashing was unfair.

The [handleSlashing](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L52) function transfers the slashed assets from the vault, effectively reducing the total assets in the vault, which slashes each shareholder relative to the number of shares they own.

```solidity
    function handleSlashing(IERC20 token, uint256 amount) external {
        if (amount == 0) revert ZeroAmount();
        if (!_config().supportedAssets[token]) revert UnsupportedAsset();

        SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount);
        // Below is where custom logic for each asset lives
        SafeTransferLib.safeTransfer(address(token), address(0), amount);
    }
```

The problem with the current system is that the two-day veto window introduces a time gap between when a slash is confirmed and when it is finalized. The slash request records the amount that should be slashed depending on the slash percentage and total assets in the [fetchEarmarkedStakes](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/entities/SlasherLib.sol#L79) function. Therefore, it is fair to assume that the shareholders at that timestamp should be slashed. However, currently, users who deposit between the slash request and when it is executed will also be slashed and lose value.

Furthermore, the protocol currently lacks any getter methods to warn users of pending slashes for vaults, meaning users could unknowingly deposit into such vaults and lose value in a short period.

In the worst-case scenario, if a vault is fully slashed and the total assets become zero, since the total supply will still be non-zero and previous users will still own shares, any new deposits will instantly lose value, and some funds will be incorrectly allocated to previous users.

### Impact

The current implementation can lead to unfair slashing of users who deposit between the slashing request and its finalization. Users unknowingly depositing into vaults with pending slashes will lose value. With no getter methods available, there is no way for users to identify such vaults and avoid potential losses.

### Recommendation

*   Implement getter methods to allow users to check for pending slashes on vaults before making deposits.
*   If a vault is fully slashed, consider disabling deposits to avoid users from losing value. If disabling deposits is not feasible, make sure users are warned of a slashing event or that the vault is empty with shares still in it, which could cause losses.

**[devdks25 (Karak) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/15#issuecomment-2262965087):**
 > We plan on running indexers for operator related metrics.<br>
> > If a vault is fully slashed, consider disabling deposits to avoid users from losing value. If disabling deposits is not feasible, make sure users are warned of a slashing event or that the vault is empty with shares still in it, which could cause losses.
> 
> Ideally no staker should deposit in such vault, but still the vault's deposit can be paused.<br>
> cc: @dewpe 

**[dewpe (Karak) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/15#issuecomment-2267647006):**
 > Would reclassify to a non-issue.
> 
> This is more of a frontend and indexer issue instead of a contract/protocol issue. Operators are already performing due diligence prior to delegating to an DSS. Part of their DD would be if there is any pending slashings. The frontend would make it obvious that there are pending slashings that could affect a users share price. 
> 
> We could add helper functions on the querier to help protocols building on top have the same observability.

**[dewpe (Karak) disputed](https://github.com/code-423n4/2024-07-karak-findings/issues/15#issuecomment-2267647006)**

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/15#issuecomment-2282760819):**
 > The warden has demonstrated how stakers that deposit while a slash is ongoing will lose funds.
> 
> Although it is technically the responsibility of stakers to ensure the vault's current state does not cause them to lose funds when depositing, it isn't apparent to the regular user that an ongoing slash will cause a loss of funds for new deposits. I also believe it isn't documented anywhere that users must check if a slash is ongoing before depositing into vaults prior to the audit. 
> 
> As such, I am inclined to award this as medium severity.

**[devdks25 (Karak) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/15#issuecomment-2284908881):**
 > To mitigate this the vault deposits will be paused by the core during a queued slashing event and will be unpaused post `finalizeSlashing/cancelSlashing`.<br>
> Additionally a `deposit_OVERRIDE_SLASH_PROTECTION` method will be exposed to prevent griefing.

**[Karak mitigated](https://github.com/code-423n4/2024-09-karak-mitigation?tab=readme-ov-file#scope):**
> This mitigation exposes a getter to determine if a vault's queued for slashing.

**Status:** Mitigation confirmed. Full details in reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/4), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/29) and [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/18).

***

## [[M-05] Slashings will always fail in some cases](https://github.com/code-423n4/2024-07-karak-findings/issues/7)
*Submitted by [0xCiphky](https://github.com/code-423n4/2024-07-karak-findings/issues/7)*

The [requestSlashing](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Core.sol#L220) function allows a slashing to be requested for a given operator’s deployed vaults staked to the DSS.  The slashing request must pass the `SLASHING_VETO_WINDOW` (2 days) before it can be confirmed, allowing the veto committee to cancel any unfair queued slashing.

This time gap can create situations where the requested slashed amount is no longer possible, as the contract might have had previous withdrawals or been slashed by other DSS’s in that time, reducing its overall balance. The [slashAssets](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/Vault.sol#L193) function in the vault contract handles this by taking the minimum of the requested slashed amount and the contract balance.

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

        emit Slashed(transferAmount);
    }
```

However, if the total assets in the contract are zero, the transferAmount will be zero. When this zero value is passed to the [handleSlashing](https://github.com/code-423n4/2024-07-karak/blob/f5e52fdcb4c20c4318d532a9f08f7876e9afb321/src/SlashingHandler.sol#L52) function, it will revert due to a check that ensures the amount is not zero.

*   Since DSS can slash 100% of a vault and vaults can be staked to multiple DSS, it is possible that a vault could be slashed before this slashing request, leaving its total assets as zero.
*   Another scenario is if there were pending withdrawals that were completed before the slashing, resulting in the total assets being zero after the withdrawals.

```solidity

    function handleSlashing(IERC20 token, uint256 amount) external {
        if (amount == 0) revert ZeroAmount();
        if (!_config().supportedAssets[token]) revert UnsupportedAsset();

        SafeTransferLib.safeTransferFrom(address(token), msg.sender, address(this), amount);
        // Below is where custom logic for each asset lives
        SafeTransferLib.safeTransfer(address(token), address(0), amount);
    }
```

As a result, if this slashing reverts and since a slash request can include multiple slashes for different vaults, the entire transaction will revert, blocking other vault slashes as well.

*   Consider the following scenario:
    *   A DSS requests  a slash for all 32 vaults of one of its registered operators.
    *   One of these vaults has its total assets reduced to zero, either due to another DSS slash or because pending withdrawals were completed.
    *   After the two-day veto period, the finalizeSlashing function is called but fails because the slash function of the vault with zero assets reverts.
        *   As a result, the other 31 vaults cannot be slashed either.
*   Additional Complications:
    *   The operator must create another slash request excluding the problematic vault and go through the process again, including another two-day veto period.
        *   This could affect the slash amount if other users complete withdrawals during this time.
    *   The pending request must also be canceled by the veto; otherwise, if the zero-asset vault becomes non-zero, it could lead to an incorrect double slashing as anyone can finalize a slash request.

### Impact

If the slashAssets function encounters this scenario while attempting to slash assets, it will cause the entire transaction to revert. This will block other slashing requests within the same transaction and lead to additional logical issues as described above.

### Proof Of Concept

```solidity
    function test_slash_fails() public {
        // register operator to dss, deploy vaults, stake vaults to dss
        stake_vaults_to_dss();
        // check if operator is registered to dss
        assertEq(true, core.isOperatorRegisteredToDSS(operator, dss));
        // request full withdraw from vault 1
        vault1.approve(address(vault1), 1000);
        IVault(address(vault1)).startRedeem(1000, address(this));
        skip(8 days);
        // dss request slashing
        address[] memory operatorVaults = core.fetchVaultsStakedInDSS(operator, dss);
        uint96[] memory slashPercentagesWad = new uint96[](2);
        slashPercentagesWad[0] = uint96(10e18);
        slashPercentagesWad[1] = uint96(10e18);
        SlasherLib.SlashRequest memory slashingReq = SlasherLib.SlashRequest({
            operator: operator,
            slashPercentagesWad: slashPercentagesWad,
            vaults: operatorVaults
        });
        vm.startPrank(address(dss));
        SlasherLib.QueuedSlashing memory queuedSlashing = core.requestSlashing(slashingReq);
        vm.stopPrank();
        skip(1 days);
        // finalize withdraw from vault 1
        bytes32 expectedWithdrawalKey = keccak256(abi.encode(address(this), 0));
        IVault(address(vault1)).finishRedeem(expectedWithdrawalKey);
        // check vault 1 total assets
        assertEq(vault1.totalAssets(), 0);
        skip(1 days);
        // finalize slashing reverts blocking all slashing
        vm.expectRevert(ZeroAmount.selector);
        core.finalizeSlashing(queuedSlashing);
    }
```

### Tools Used

*   Manual analysis
*   Foundry

### Recommendation

The slashAssets function in the vault contract should only approve and call handleSlashing if transferAmount is greater than zero. This way, the function will not revert, and other slashes will go through.

```

    function slashAssets(uint256 totalAssetsToSlash, address slashingHandler)
        external
        onlyCore
        returns (uint256 transferAmount)
    {
        transferAmount = Math.min(totalAssets(), totalAssetsToSlash);
        if (transferAmount > 0) { // Add here
            // Approve to the handler and then call the handler which will draw the funds
            SafeTransferLib.safeApproveWithRetry(asset(), slashingHandler, transferAmount);
            ISlashingHandler(slashingHandler).handleSlashing(IERC20(asset()), transferAmount);
        }
        emit Slashed(transferAmount);
    }
```

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/7#issuecomment-2282775691):**
 > The warden has demonstrated how if a vault's `totalAssets` decreases to `0` while a slash is pending, it will be impossible to `finalizeSlashing()` for that slash request. 
> 
> As such, I believe medium severity is appropriate.

**[karan-andalusia (Karak) confirmed](https://github.com/code-423n4/2024-07-karak-findings/issues/7#event-14473984535)**

**[Karak mitigated](https://github.com/code-423n4/2024-09-karak-mitigation?tab=readme-ov-file#scope):**
> This mitigation skips the slashing incase of 0 slashing amount.

**Status:** Mitigation confirmed. Full details in reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/5), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/30) and [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/19).

***

# Low Risk and Non-Critical Issues

For this audit, 15 reports were submitted by wardens detailing low risk and non-critical issues. The [report highlighted below](https://github.com/code-423n4/2024-07-karak-findings/issues/22) by **rbserver** received the top score from the judge.

*The following wardens also submitted reports: [ZanyBonzy](https://github.com/code-423n4/2024-07-karak-findings/issues/96), [Rhaydden](https://github.com/code-423n4/2024-07-karak-findings/issues/97), [Shaheen](https://github.com/code-423n4/2024-07-karak-findings/issues/45), [0xCiphky](https://github.com/code-423n4/2024-07-karak-findings/issues/14), [iam\_emptyset](https://github.com/code-423n4/2024-07-karak-findings/issues/94), [peanuts](https://github.com/code-423n4/2024-07-karak-findings/issues/89), [anonymousjoe](https://github.com/code-423n4/2024-07-karak-findings/issues/74), [0xlemon](https://github.com/code-423n4/2024-07-karak-findings/issues/70), [c0pp3rscr3w3r](https://github.com/code-423n4/2024-07-karak-findings/issues/61), [lanrebayode77](https://github.com/code-423n4/2024-07-karak-findings/issues/59), [KupiaSec](https://github.com/code-423n4/2024-07-karak-findings/issues/39), [gesha17](https://github.com/code-423n4/2024-07-karak-findings/issues/27), [falconhoof](https://github.com/code-423n4/2024-07-karak-findings/issues/10), and [4rdiii](https://github.com/code-423n4/2024-07-karak-findings/issues/3).*

## [01] Allowing DSSes that do not implement `ERC165.supportsInterface` function to be registered is problematic

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

## [02] Allowing operator to stake its vault to multiple DSSes can cause some DSSes' slashings of such vault to fail

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

## [03] First depositor attack is possible

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

## [04] `maxDeposit`, `maxMint`, `maxRedeem`, and `maxWithdraw` functions do not return 0 when they should

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

## [05] `deposit(uint256 assets, address to)`, `deposit(uint256 assets, address to, uint256 minSharesOut)`, `mint(uint256 shares, address to)`, and `startRedeem(uint256 shares, address beneficiary)` functions do not allow staker's approved spender to call these functions on behalf of staker

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

## [06] `previewWithdraw` and `previewRedeem` functions do not revert though they should

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

## [07] DSS's slashing request can be canceled after `SLASHING_VETO_WINDOW` is passed

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

## [08] Address collision for vault can be used for DOS or stealing stakers' deposits

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

## [09] Pausing `deposit(uint256 assets, address to, uint256 minSharesOut)` function causes stakers to lose slippage protection when depositing into vault

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

## [10] `Vault` contract's withdrawal functions can be paused while its deposit functions are unpaused

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

## [11] `Vault.mint` and `Vault.finishRedeem` functions do not have slippage controls

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

## [12] Vault deployed for token that does not yet exist but will be deployed can cause stakers to lose their deposits

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

## [13] Operator can collude with DSS for stealing stakers' deposits

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

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-07-karak-findings/issues/22#issuecomment-2303002295):**
 > **[01] Allowing DSSes that do not implement `ERC165.supportsInterface` function to be registered is problematic**<br>	 
> Low.
> 
> **[02] Allowing operator to stake its vault to multiple DSSes can cause some DSSes' slashings of such vault to fail**<br>
> Low.
> 
> **[03] First depositor attack is possible**<br>
> Low, but I don't agree with the recommendation.
> 
> **[04] `maxDeposit`, `maxMint`, `maxRedeem`, and `maxWithdraw` functions do not return 0 when they should**<br>
> Low, EIP-compliance issue.
> 
> **[05] `deposit(uint256 assets, address to)`, `deposit(uint256 assets, address to, uint256 minSharesOut)`, `mint(uint256 shares, address to)`, and `startRedeem(uint256 shares, address beneficiary)` functions do not allow staker's approved spender to call these functions on behalf of staker**<br>
> Low, EIP-compliance issue.
> 
> **[06] `previewWithdraw` and `previewRedeem` functions do not revert though they should**<br>
> Low, EIP-compliance issue.
> 
> **[07] DSS's slashing request can be canceled after `SLASHING_VETO_WINDOW` is passed**<br>
> Low.
> 
> **[08] Address collision for vault can be used for DOS or stealing stakers' deposits**<br>
> Informational.
> 
> **[09] Pausing `deposit(uint256 assets, address to, uint256 minSharesOut)` function causes stakers to lose slippage protection when depositing into vault**<br>
> Informational.
> 
> **[10] Vault contract's withdrawal functions can be paused while its deposit functions are unpaused**<br>
> Informational.
> 
> **[11] `Vault.mint` and `Vault.finishRedeem` functions do not have slippage controls**<br>
> Informational.
> 
> **[12] Vault deployed for token that does not yet exist but will be deployed can cause stakers to lose their deposits**<br>
> Informational, assumes the admin will intentionally whitelist a token that hasn't yet been deployed.
> 
> **[13] Operator can collude with DSS for stealing stakers' deposits**<br>
> Informational, kind of speculative.

***

# [Mitigation Review](#mitigation-review)

## Introduction

Following the C4 audit, 3 wardens ([KupiaSec](https://code4rena.com/@KupiaSec), [0xCiphky](https://code4rena.com/@0xCiphky) and [sl1](https://code4rena.com/@sl1)) reviewed all mitigations in a private repo provided by the sponsor. The guidelines for the review can be found in the [C4 Karak Mitigation Review repository](https://github.com/code-423n4/2024-09-karak-mitigation) and the outcomes are detailed below.

After completion of the mitigation review, the sponsor team transferred their v2 contracts to a [publicly accessible repo](https://github.com/karak-network/v2-contracts).

## Overview of Changes

**[Summary from the Sponsor](https://github.com/code-423n4/2024-09-karak-mitigation?tab=readme-ov-file#overview-of-changes):**

> Karak Restaking is a protocol that allows users to restake their assets by directly depositing them into the vaults of operators. Operators can then register with Distributed Secure Services (DSS) to provide economic security. Operators perform tasks for the DSS in exchange for rewards, and the DSS has the ability to slash the funds that operators have delegated.

## Mitigation Review Scope

| Mitigation of | Purpose | 
| ------------- | ----------- |
| H-01 | This mitigation only burns the ETH that has already been credited to the user consequently avoiding this scenario |
| H-02 | This mitigation removes the SlashStore altogether and the NativeVault itself burns the slashed ETH |
| H-03 | This mitigation introduces a check for the rounding error |
| H-04 | This mitigation validates the operator, vaults status in the finalizing slashing |
| M-02| This mitigation accounts for the decrease in balance of the users shares before burning |
| M-03 | This mitigation computes the slashing amount in finalize slashing |
| M-04 | This mitigation exposes a getter to determine if a vault's queued for slashing |
| M-05 | This mitigation skips the slashing incase of `0` slashing amount |

### Additional scope to be reviewed

The following items were additional changes that were in scope for this review, but not directly tied to findings from the original C4 audit.

| Reference ID | Purpose | 
| ------------- | ----------- |
| ADD-01 | Critical fix |
| ADD-02 | Critical fix |

## Out of Scope
- [M-01: Changing the slashingHandler for NativeVaults will DoS slashing](https://github.com/code-423n4/2024-07-karak-findings/issues/49)

## Mitigation Review Summary

**The wardens confirmed the mitigations for all in-scope findings except for H-02, which was unmitigated. They also surfaced 2 new Medium severity issues. The table below provides details regarding the status of each in-scope vulnerability from the original audit, followed by full details on the new issues and any in-scope vulnerabilities that were not fully mitigated.**

| Original Issue | Status | Full Details |
| ----------- | ------------- | ----------- |
| H-01 | 🟢 Mitigation Confirmed | Reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/9), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/23) and [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/21) |
| H-02 | 🔴 Unmitigated | Reports from [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/12) and [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/8) - details also shared below |
| H-03 | 🟢 Mitigation Confirmed | Reports from [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/13), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/25) and [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/7) |
| H-04 | 🟢 Mitigation Confirmed | Reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/6) and [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/14)|
| M-02 | 🟢 Mitigation Confirmed | Reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/2), sl1 ([1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/15), [2](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/16)) and [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/27) |
| M-03 | 🟢 Mitigation Confirmed | Reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/3), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/28), [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/17) |
| M-04 | 🟢 Mitigation Confirmed | Reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/4), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/29), [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/18) |
| M-05 | 🟢 Mitigation Confirmed | Reports from [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/5), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/30), [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/19) |
| ADD-01 | 🟢 Mitigation Confirmed | Reports from [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/22), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/31), [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/10) |
| ADD-02 | 🟢 Mitigation Confirmed | Reports from [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/20), [KupiaSec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/32), [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/11) |

## [H-02 Unmitigated](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/12)
*Submitted by [sl1](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/12) and also [0xCiphky](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/8).*

### Original Issue:

https://github.com/code-423n4/2024-07-karak-findings/issues/55

### Comments

Before mitigation, when deploying a new NativeVault, the operator could set `manager`, `slashStore` and `nodeImplementation` to anything. This happened because `vaultConfig.extraData` lacked input validation when calling `deployVaults()`. The impacts of the original issue were the ability of operator to create a NativeVault that can be silently unslashable and escalate his rights, obtaining a privileged role that will allow him to steal user's funds. 

### Mitigation

The mitigation removes the `slashStore` variable altogether and `NativeVault` burns the slashed ETH directly by itself. The current mitigation does not address all of the impacts of the original issue. As currently operator can still escalate his rights and steal user's funds.

### Proof of Concept

As can be seen, when `deployVaults()` is called with `vaultConfigs` passed by an operator, the variables is passed to `createVault()` unvalidated.

`CoreLib.sol#L133-L141`

```solidity
IKarakBaseVault vault = createVault(
    self,
    operator,
    vaultConfigs[i].asset,
    vaultConfigs[i].name,
    vaultConfigs[i].symbol,
    vaultConfigs[i].extraData,
    implementation
);
```

After cloning a vault, `createVault` initializes it with the configuration passed by the operator.

`CoreLib.sol#L107`

```solidity
 vault.initialize(address(this), operator, depositToken, name, symbol, extraData);
```

When `initialize()` is called, the `extraData` parameter is decoded into `manager` and `nodeImplementation` which are only validated to not be `address(0)`.

`NativeVault.sol#L59-L62`

```solidity
(address manager, address nodeImplementation) = abi.decode(
    _extraData,
    (address, address)
);
if (manager == address(0) || nodeImplementation == address(0))
    revert ZeroAddress();
_grantRoles(manager, Constants.MANAGER_ROLE);
```

This allows an operator to set himself as a manager and call restricted functions,  such as `changeNodeImplementation()` to change the implementation to a malicious one, which would allow him to steal staker's funds.

### Recommended Mitigation Steps

Preset the NativeNode's implementation and only allow the owner of the NativeVault to change it.

**[karan-andalusia (Karak) commented](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/12#issuecomment-2364290045):**
> We are aware of this and actually this was by design. Since the karak protocol was designed to be modular and NativeVault is owned by Core it didn't seem ideal to change core to adapt to NativeVault - i.e., add a method to change node implementation in Core. This similar situation could arise with any third party developer trying to bring in their vaults where Core should not be changed for integration purposes. And the transparency of the system allows the staker to view the manager of the native vault before they stake their validators to it so they go ahead with it only if the manager seems to be trust worthy. 

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/12#issuecomment-2368628048):**
> > And the transparency of the system allows the staker to view the manager of the native vault before they stake their validators to it so they go ahead with it only if the manager seems to be trust worthy.
>
> I'll leave this as unmitigated for awarding purposes since I believe this trust assumption was made known prior to the audit.
> 
> It's worth nothing the bug is effectively mitigated if we assume regular users should be checking if the vault's manager is trustworthy though.

***

## [Pending slashes not addressed when `finalizeSlashing` reverts](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/6)
*Submitted by 0xCiphky*

**Severity: Medium**

### Original Issue

https://github.com/code-423n4/2024-07-karak-findings/issues/4

### Comments

The original implementation does not account for cases where an operator unregisters from a vault before a pending slash is finalized. This oversight allows slashing to occur on unregistered operators, violating the protocol's invariant that only registered operators should be subject to slashing.

### Mitigation

The mitigation addresses the issue by incorporating the `checkIfOperatorIsRegInRegDSS` function into the `finalizeSlashing` process. This ensures that the function checks whether the operator is still registered in the relevant DSS before finalizing the slash.

```solidity
    function finalizeSlashing(SlasherLib.QueuedSlashing memory queuedSlashing)
        external
        nonReentrant
        whenFunctionNotPaused(Constants.PAUSE_CORE_FINALIZE_SLASHING)
    {
        CoreLib.Storage storage self = _self();
        self.checkIfOperatorIsRegInRegDSS(queuedSlashing.operator, queuedSlashing.dss);
        self.finalizeSlashing(queuedSlashing);

        emit FinalizedSlashing(msg.sender, queuedSlashing);
    }
```

### New issue

The current mitigation introduces a new issue. When a slash is requested, the count of queued slashes for that vault is incremented. However, if the `finalizeSlashing` function reverts due to an operator being unregistered, the queued slash remains unaddressed, leading users to believe there are pending slashes when calling `isVaultQueuedForSlashing.` This situation may act as a deterrent for new deposits, as users would assume a pending slash.

```solidity
    /// @notice checks whether a slashing in requested on the given vault.
    /// @param vault address of the vault.
    /// @return boolean where true indicating vault is queued for slashing.
    function isVaultQueuedForSlashing(address vault) public view returns (bool) {
        uint256 count = _self().getSlashingsQueuedForVault(vault);
        return count != 0;
    }
```

Furthermore, since pending slashes have no deadline on them, if the operator re-registers in the future, the slash could incorrectly be finalized by anyone.

### Recommended additional mitigation

To address this, the `finalizeSlashing` function could be modified to call the internal `cancelSlashing` function when the operator is no longer registered. This adjustment treats such cases similarly to cancelled slashes, preventing invalid slashes from lingering indefinitely and ensuring the queued slashes count reflects the true state of the vault.

**[MiloTruck (judge) commented](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/6#issuecomment-2357303957):**
> > Furthermore, since pending slashes have no deadline on them, if the operator re-registers in the future, the slash could incorrectly be finalized by anyone.
>
> Agree that this edge case is an issue - pending slashes should always be resolved, regardless of whether the operator/vault is slashed or not.
> 
> Additionally, this is case of a mitigation causing a separate H/M issue and should be submitted as a new finding. H-04 _has_ been mitigated.

**[devdks25 (Karak) commented](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/6#issuecomment-2359637378):**
> Consider this scenario:
> 1. Slash is requested on operator by DSS.
> 2. `SLASHING_VETO_WINDOW` is passed, now `finalizeSlashing` can be called anytime.
> 3. Let's after 20 days `finalizeSlashing` is called and operator is slashed.
> 
> So the point being operator is held responsible and slashed for the malicious activity done by it, no matter whenever the `finalizeSlashing` occurs.
>
> So even if the operator unregisters during the `SLASHING_VETO_WINDOW`, and then registers again (before `finalizeSlashing` is called), those vaults are still slashable (as `VETO_COMMITTEE` didn't veto the slashing hence the slashing in legit, and the reason that operator didn't get slashed is probably a lousy DSS).

**[0xCiphky (warden) commented](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/6#issuecomment-2360016110):**
> The pending slash could still cause issues in this scenario, particularly with the `isVaultQueuedForSlashing` function:
>
> - Typically, users looking to deposit into a vault will wait for any pending slashes to be resolved first.
> - If a slash stays pending indefinitely, it’ll inadvertently harm the vault, as users will be deterred from depositing funds, even if the operator is maintaining all it’s current tasks effectively.
>    - In some cases, this could be worse than a regular slash, as the vault might have a pending slash on record for longer than the typical slashing request-execution time (worst case for good). This will deter users from depositing into the vault, potentially resulting in greater losses than if the slash had been finalized.

**[devdks25 (Karak) commented](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/6#issuecomment-2362122792):** 
> So if a malicious operator got away with slashing, it can still finalize the pending slashing:
> 1. Post unstaking all the vaults from the DSS (prerequisite for unregistering from DSS) and before unregistering from the DSS, just call the `finalizeSlashing`. Vaults won't be slashed as they'll be skipped because they are unstaked from the DSS.
> 2. Post unregistering from the DSS, register again and repeat the above.
> 
> `Typically, users looking to deposit into a vault will wait for any pending slashes to be resolved first.`
>
> In an event where a slashing is completed/pending for an operator, the credibility of that operator decreases. So users must be more inclined towards withdrawing their stakes from this operator rather than waiting for the pending slashing to go through.

**[0xCiphky (warden) commented](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/6#issuecomment-2362900678):**
> That’s a fair point. I don’t think this could be exploited effectively, as operators stop earning rewards once they request to unstake. So, there’s little incentive to remain in that in between state.
>
> I think the area of concern is that the situation could also occur unintentionally.
> 
> If a slash is requested just before the operator unstakes and unregisters unknowingly they would end up with a pending slash indefinetely. In this case, while the slash request may be justified, it wasn’t necessarily bypassed with malicious intent, and the operator has no way to directly resolve it.
>
>> Typically, users looking to deposit into a vault will wait for any pending slashes to be resolved first. In an event where a slashing is completed/pending for an operator, the credibility of that operator decreases. So users must be more inclined towards withdrawing their stakes from this operator rather than waiting for the pending slashing to go through.
> 
> I wouldn’t say this is always the case. A reputable, long-standing operator could experience a technical issue that results in a slash, and new users might still choose to deposit with them in the future.

**[devdks25 (Karak) commented](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/6#issuecomment-2364121202):** 
> The intent of this was to describe a way for operator to complete a pending slashing(without it's vaults getting slashed), if an operator has managed to unstake all the vaults/unregistered from DSS.
>
>> If a slash is requested just before the operator unstakes and unregisters unknowingly they would end up with a pending slash indefinetely.
> 
> Such a slashing would be considered invalid and should be vetoed as the slashing should be requested within the [`SLASHING_WINDOW(7 days)`](https://github.com/code-423n4/2024-07-karak#:~:text=a%20slashing%20event.-,DSS%20can%20slash%20any%20malicious%20behavior%20occurring%20before%20the%20withdrawal%20initiation%20for%20up%20to%207%20days.,-An%20operator%20can) period post the malicious activity is recorded, and the operator to be slashed has to be staked during the malicious event. So if the operator initiates an unstaking immediately post the malicious event, we'll be still left with `2 days` as the `MIN_STAKE_UPDATE_DELAY(9 days)`, so an immediate slashing request followed by unstaking shouldn't arise.
> 
> 1. Post unstaking all the vaults from the DSS (prerequisite for unregistering from DSS) and before unregistering from the DSS, just call the `finalizeSlashing`. Vaults won't be slashed as they'll be skipped because they are unstaked from the DSS.
> 2. Post unregistering from the DSS, register again and repeat the above.
>
> If a justified slashing was requested and the operator managed to escape it (due to lousy DSS), then a pending slashing would enforce the operator to complete the slashing (using either of the above 2 methods based on it's registration status). And it's better than cancelling the slashing, as this way it'll be logged as a completed slashing rather than an invalid one.

**[0xCiphky (warden) commented](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/6#issuecomment-2367263798):**
> Makes sense. So the most likely valid scenario would be if a request is made just before the 7-day period ends. Once the 9-day unstaking period is over, either the operator or anyone else can call unstake before being slashed. A user who has staked in that vault might be incentivized to call unstake before their funds are slashed.
>
> - In this situation, if the finalize slashing is called before the unregister, the pending slash is resolved as you mentioned earlier. However, if the user unregisters first, the slash remains pending. Both cases are effectively the same, but one results in a significantly harsher punishment.
>
>> If a justified slashing was requested and the operator managed to escape it (due to lousy DSS), then a pending slashing would enforce the operator to complete the slashing (using either of the above 2 methods based on it's registration status). And it's better than cancelling the slashing, as this way it'll be logged as a completed slashing rather than an invalid one.
> 
> Since anyone can call unstake on behalf of the operator, we can't classify the operator as malicious. Should we expect the operator to know to finalize the slash after the unstake to resolve it before unregistering? What’s the purpose of having two completely different outcomes when the decision often comes down to chance as operators would be unaware of the two outcomes?

***

## [Stakers in the NativeVault could be unfairly slashed](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/16)
*Submitted by sl1, also found by [Kupiasec](https://github.com/code-423n4/2024-09-karak-mitigation-findings/issues/27)*

**Severity: Medium**

### Bug description

> Note: Even though the root cause of the issue was not discovered in the previous audit, the presented scenario is the same as in [M-02](https://github.com/code-423n4/2024-07-karak-findings/issues/31).

Consider a scenario where both Alice and Bob each have 32 ETH restaked into the NativeVault. NativeVault's `totalAssets` equals 64 ETH. A slashing event occurs in the vault, resulting in the NativeVault being slashed by 2 ETH, reducing `totalAssets` to 62 ETH.

`NativeVault.sol#L312`

```solidity
self.totalAssets -= totalAssetsToSlash;
```

Now 32 shares of both users amount to `32 * 62 / 64 = 31 ETH`, meaning that both Alice and Bob lost 1 ETH due to the slashing. After a slashing event has occurred in the NativeVault, Alice's validator looses all of its funds. Snapshot is started for Alice to reduce her assets by 32 ETH. `validateSnapshotProofs()` will calculate `balanceDeltaWei` as -32, subsequently calling `_updateSnapshot()` function.

`NativeVault.sol#L151-L159`

```solidity
    int256 balanceDeltaWei = self.validateSnapshotProof(
        nodeOwner,
        validatorDetails,
        balanceContainer.containerRoot,
        balanceProofs[i]
    );
    snapshot.remainingProofs--;
    snapshot.balanceDeltaWei += balanceDeltaWei;
}
_updateSnapshot(node, snapshot, nodeOwner);
```

`_updateSnapshot()` calls `_updateBalance()`, where `_decreaseBalance()` function is invoked. `_decreaseBalance()` will burn all of Alice's shares and reduce `totalAssets` of the NativeVault by 32 ETH.

`NativeVault.sol#L511-L515`

```solidity
uint256 shares = Math.min(convertToShares(assets), balanceOf(_of));
_beforeWithdraw(assets, shares);
_burn(_of, shares);
self.totalAssets -= assets;
self.ownerToNode[_of].totalRestakedETH -= assets;
```

After transaction has been completed, `totalAssets` are reduced to 30 ETH, leaving Bob only being able to withdraw 30 ETH, even though he should be able to withdraw 31.

### Impact

Loss of funds for a user.

### Recommended Mitigation

`_decreaseBalance()` function should calculate the amount of assets to slash based on  the amount of shares of a user.

```diff
function _decreaseBalance(address _of, uint256 assets) internal {
    NativeVaultLib.Storage storage self = _state();
    uint256 shares = Math.min(convertToShares(assets), balanceOf(_of));
+   uint256 assetsToSlash = Math.min(assets, convertToAssets(balanceOf(_of)));
-   _beforeWithdraw(assets, shares);
+   _beforeWithdraw(assetsToSlash, shares);
    _burn(_of, shares);
-   self.totalAssets -= assets;
+   self.totalAssets -= assetsToSlash;
    self.ownerToNode[_of].totalRestakedETH -= assets;
    emit DecreasedBalance(self.ownerToNode[_of].totalRestakedETH);
}
```

***

# Disclosures

C4 is an open organization governed by participants in the community.

C4 audits incentivize the discovery of exploits, vulnerabilities, and bugs in smart contracts. Security researchers are rewarded at an increasing rate for finding higher-risk issues. Audit submissions are judged by a knowledgeable security researcher and solidity developer and disclosed to sponsoring developers. C4 does not conduct formal verification regarding the provided code but instead provides final verification.

C4 does not provide any guarantee or warranty regarding the security of this project. All smart contract software should be used at the sole risk and responsibility of users.
