- L36/134/139/140/142/143/183/219/228/237/318/407/424/428/431/452/482/534 - Instead of using a require you can use ifs and an error custom, this would generate a lower cost of gas.

- L557 - It is less expensive to validate that uint != 0 than to validate uint > 0

- L482 - REQUIRE()/REVERT() STRINGS LONGER THAN 32 BYTES COST EXTRA GAS

- L267 - When we want to validate bools, it is less expensive to validate require(!bool) or require(bool), instead of require(bool == false) or require(bool == true)

- L361/362/456/459/485/486 - When a variable is created, but it is only used once, there is no point in creating the variable, MerkleVerifier._computeRoot() can be used directly.

- L199/475/476 - It is not necessary to create a variable and set a value when we want to set its default value, this is so since when creating the variable it already has that value.

- L199/476 - When the length of an array is used to go through it in the for, instead of consulting the length in each iteration, it is less expensive to create a length variable in memory.

- L199/476 - It is less expensive to run ++i, instead of i++


