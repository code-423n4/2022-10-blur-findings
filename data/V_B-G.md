### 1. internal function instead of public in library

`MerkleVerifier` declare functions as `public`. This makes a call to the library function be compiled to `delegatecall` instruction, while the functions with internal modifiers are simply inlined into the code. Thus, changing one word in contracts can replace calling an expensive opcode by simply executing logic directly. 

For example with `MerkleVerifier` it will save almost 3k gas per function call.

### 2. immutable variables

It is cheaper to use `weth` variable as `immutable`. It saves one storage read and make the code clearer.


