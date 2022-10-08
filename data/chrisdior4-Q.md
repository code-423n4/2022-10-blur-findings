## PUBLIC FUNCTIONS NOT CALLED BY THE CONTRACT SHOULD BE DECLARED EXTERNAL INSTEAD

  ### file: BlurExchange.sol
==================================  

# function `cancelOrder` must be called by the trader of the order, therefore it is better to be declared as external   

1.function cancelOrder(Order calldata order) public {  

1.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L181  

==================================  
## call() should be used instead of transfer() on an address payable

  ### file: BlurExchange.sol

1.payable(to).transfer(amount);

1.https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L508


