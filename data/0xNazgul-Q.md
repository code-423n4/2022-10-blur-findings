## [NAZ-L1] Value Range Validity for Setters
**Severity** Low
**Context**: [`BlurExchange.sol#L246`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L246)

**Description**:
These functions doesn't have any checks to ensure that the variables being set is within some kind of value range.

**Recommendation**:
Each variable input parameter updated should have it's own value range checks to ensure their validity.


## [NAZ-L2] Missing Equivalence Checks in Setters
**Severity**: Low
**Context**: [`ExecutionDelegate.sol#L36`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36), [`ExecutionDelegate.sol#L45`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L45), [`BlurExchange.sol#L215`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L215), [`BlurExchange.sol#L224`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L224), [`BlurExchange.sol#L233`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L233), [`BlurExchange.sol#L242`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L242)

**Description**:
Setter functions are missing checks to validate if the new value being set is the same as the current value already set in the contract. Such checks will showcase mismatches between on-chain and off-chain states.

**Recommendation**:
This may hinder detecting discrepancies between on-chain and off-chain states leading to flawed assumptions of on-chain state and protocol behavior.


## [NAZ-L3] Missing Zero-address Validation
**Severity**: Low
**Context**: [`ExecutionDelegate.sol#L36`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36), [`ExecutionDelegate.sol#L45`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L45)

**Description**:
Lack of zero-address validation on address parameters may lead to transaction reverts, waste gas, require resubmission of transactions and may even force contract redeployments in certain cases within the protocol.

**Recommendation**:
Consider adding explicit zero-address validation on input parameters of address type.


## [NAZ-L4] Lack of Event Emission For Critical Functions
**Severity**: Low
**Context**: [`ExecutionDelegate.sol#L36`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36), [`ExecutionDelegate.sol#L45`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L45)

**Description**:
Several functions update critical parameters that are missing event emission. These should be performed to ensure tracking of changes of such critical parameters.

**Recommendation**:
Consider adding events to functions that change critical parameters.


## [NAZ-L5] Missing Events In Initialize Functions
**Severity**: Low
**Context**: [`BlurExchange.sol#L95`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95)

**Description**:
None of the initialize functions emit emit init-specific events. They all however have the initializer modifier (from Initializable) so that they can be called only once. Off-chain monitoring of calls to these critical functions is not possible.

**Recommendation**:
It is recommended to emit events in your initialization functions.


## [NAZ-N1] Visibility Should Be Before Modifiers
**Severity**: Informational
**Context**: [`ExecutionDelegate.sol#L36`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L36), [`ExecutionDelegate.sol#L45`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L45), [`ExecutionDelegate.sol#L75`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L75), [`ExecutionDelegate.sol#L90`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L90), [`ExecutionDelegate.sol#L106`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L106), [`ExecutionDelegate.sol#L121`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L121)

**Description**:
Visibility keyword should be before the list of modifiers as per best practice.

**Recommendation**:
Consider moving the visibility keyword before the list of modifiers.


## [NAZ-N2] Function && Variable Naming Convention
**Severity** Informational
**Context**: [`BlurExchange.sol#L57-L58`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L57-L58), [`MerkleVerifier.sol#L17`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17), [`MerkleVerifier.sol#L33`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L33), [`MerkleVerifier.sol#L17`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17) 

**Description**:
The linked variables do not conform to the standard naming convention of Solidity whereby functions and variable names(local and state) utilize the `mixedCase` format unless variables are declared as `constant` in which case they utilize the `UPPER_CASE_WITH_UNDERSCORES` format. Private variables and functions should lead with an `_underscore`.

**Recommendation**:
Consider naming conventions utilized by the linked statements are adjusted to reflect the correct type of declaration according to the [Solidity style guide](https://docs.soliditylang.org/en/latest/style-guide.html). 


## [NAZ-N3] Code Structure Deviates From Best-Practice
**Severity**: Informational
**Context**: [`ExecutionDelegate.sol#L21`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol#L21), [`EIP712.sol#L112`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L112), [`BlurExchange.sol#L35`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L35)

**Description**:
The best-practice layout for a contract should follow the following order: state variables, events, modifiers, constructor and functions. Function ordering helps readers identify which functions they can call and find constructor and fallback functions easier.  Functions should be grouped according to their visibility and ordered as: constructor, receive function (if exists), fallback function (if exists), external, public, internal, private. Functions should then further be ordered with view functions coming after the non-view labeled ones.

**Recommendation**:
Consider adopting recommended best-practice for code structure and layout.


## [NAZ-N4] Line Length
**Severity**: Informational
**Context**: [`EIP712.sol#L24`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L24), [`EIP712.sol#L27`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L27), [`BlurExchange.sol#L124`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L124), [`BlurExchange.sol#L205`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L205), [`BlurExchange.sol#L388`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388), [`BlurExchange.sol#L425`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L425), [`BlurExchange.sol#L429`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L429)

**Description**:
Max line length must be no more than 120 but many lines are extended past this length.

**Recommendation**:
Consider cutting down the line length below 120.


## [NAZ-N5] Missing Visibility
**Severity**: Informational
**Context**: [`ReentrancyGuarded.sol#L10 (Can be private)`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/ReentrancyGuarded.sol#L10), [`EIP712.sol#L33`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L33), [`EIP712.sol#L37`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/EIP712.sol#L37)

**Description**:
There is missing visibility on some state variables that are default to public. It's best practice to explicityl mark visibility of state variables and can save gas depending on the marking.

**Recommendation**:
Consider explicitly marking visibility of state.


## [NAZ-N6] Code Contains Empty Blocks
**Severity**: Informational
**Context**: [`BlurExchange.sol#L53`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L53)

**Description**:
It's best practice that when there is an empty block, to add a comment in the block explaining why it's empty.

**Recommendation**:
Consider adding `/* Comment on why */` to the empty blocks.


## [NAZ-N7] Unclear Revert Messages
**Severity** Informational
**Context**: [`BlurExchange.sol#L134`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L134), [`BlurExchange.sol#L183`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L183)

**Description**:
Some revert messages are unclear which can lead to confusion. Unclear revert messages may cause misunderstandings on reverted transactions.

**Recommendation**: 
Consider making revert messages more clear.


## [NAZ-N8] Use Underscores for Number Literals
**Severity**: Informational
**Context**: [`BlurExchange.sol#L59`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59)

**Description**:
There are multiple occasions where certain numbers have been hardcoded, either in variables or in the code itself. Large numbers can become hard to read.

**Recommendation**:
Consider using underscores for number literals to improve its readability.


## [NAZ-N9] Use of `ABIEncoderV2` With `0.8+`
**Severity**: Informational
**Context**: [`ExecutionDelegate.sol`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/ExecutionDelegate.sol), [`BlurExchange.sol`](https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol)

**Description**:
`ABIEncoderV2` is being stated in a solidity version `0.8+` which is not needed since `ABIEncoderV2` is activated by default `0.8+`.

**Recommendation**: 
Consider removing `pragma experimental ABIEncoderV2;`.


## [NAZ-N10] Missing or Incomplete NatSpec
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-10-blur/tree/main/contracts)

**Description**:
Some functions are missing @notice/@dev NatSpec comments for the function, @param for all/some of their parameters and @return for return values. Given that NatSpec is an important part of code documentation, this affects code comprehension, auditability and usability.

**Recommendation**:
Consider adding in full NatSpec comments for all functions to have complete code documentation for future use.


## [NAZ-N11] Older Version Pragma
**Severity**: Informational
**Context**: [`All Contracts`](https://github.com/code-423n4/2022-10-blur/tree/main/contracts)

**Description**:
Using very old versions of Solidity prevents benefits of bug fixes and newer security checks. Using the latest versions might make contracts susceptible to undiscovered compiler bugs. 

**Recommendation**:
Consider using the most recent version.