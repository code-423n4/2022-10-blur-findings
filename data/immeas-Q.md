### 1. state changes done after transfers, doesn't follow checks effect interactions

https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L164-L165


## Impact
It's an anti-pattern doing state changes after interactions. Receiving contracts could possibly rely on checking if order is executed or not.

## Proof of Concept
https://fravoll.github.io/solidity-patterns/checks_effects_interactions.html

## Tools Used
vscode

## Recommended Mitigation Steps
Do the state changes before doing transfers