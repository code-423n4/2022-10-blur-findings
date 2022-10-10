Blur Exchange contest

BlurExchange.sol

Gas optimization:
Line 198-200

function cancelOrders(Order[] calldata orders) external {  
	for (uint8 i = 0; i < orders.length; i++) {  
		 cancelOrder(orders[i]);

In solidity 0.8.0 safe math is included, which means it will automatically check for over/underflow. To avoid unnecessary gas use, the use of unchecked is preferred. Rewrite the loop.

Proof of Concept

https://ethereum.stackexchange.com/questions/113221/what-is-the-purpose-of-unchecked-in-solidity

BlurExchange.sol
Line 476 - 480
for (uint8 i = 0; i < fees.length; i++) { 
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;             			     _transferTo(paymentToken, from, fees[i].recipient, fee); 
            totalFee += fee; 
 }

Same issue as over, no use of unchecked. Which means the safe math will check for over-/underflow and waste gas. 

PolicyManager.sol

Line 77-79

for (uint256 i = 0; i < length; i++) {
             whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
         } 

Same issue as above

For more measures on saving gas, I would recommend for the developer team to structure all the arithmetics under unchecked. In sted of having the inbuilt safe math check for under-/overflow. The team can make sure that all of the arithmetics doesn’t over-/underflow.   This step will collectively save the project a lot of gas in the present and future. 

Proof of Concept:

Scroll down to Checked or Unchecked Arithmetic

https://docs.soliditylang.org/en/v0.8.16/control-structures.html#checked-or-unchecked-arithmetic
