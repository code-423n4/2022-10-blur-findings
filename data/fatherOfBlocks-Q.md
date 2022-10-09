**ReentrancyGuarded**
- L2 - It is recommended to use the latest version of Solidity since you will get bug fixes and new features, here you can check: https://github.com/ethereum/solidity/releases.


**EIP712**
- L20-35 - EXPRESSIONS FOR CONSTANT VALUES SUCH AS A CALL TO KECCAK256(), SHOULD USE IMMUTABLE RATHER THAN CONSTANT
While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.


**BlurExchange**
- L134/183/452 - It is important when performing a validation with a require, to launch a message, because if the user or application that uses the method reverts, it can recognize the exception.

- L8 - The ERC20 contract is imported but it is not used in any part of the contract.

- L291/297/305 - It is confusing that the input is called order, since within the struct Order there is a parameter called order, causing the variable order.order to exist within the function, this is extremely confusing. The solution to this is to give the input the name input and not order.

- 354/357/384/386 - after the if, it is validated with an else if that the signatureVersion has its second value. But SignatureVersion only has two values, so instead of an else if, just one else could be used.

- L506/508 - An attempt is made to transfer from the contract to "to" ether, but it is not validated if the contract has sufficient balance. It should be validated in order to know how to handle it in case there is no ether.

- L496/506/509/513 - One way to implement it differently is by requesting a bool, instead of address paymentToken and depending on whether it is true or false, make a transfer of ETH or WETH. This would allow that there is no option to reverse since the two possibilities will always be correct, instead of paymentToken, the input for example could be called isEthTransfer.

- L531/537/539 - The AssetType enum only has two values, therefore the else if of line 539 is not necessary, an else would be enough to assume that it is ERC1155.
