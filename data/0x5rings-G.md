 ## `++i` cost less gas compared to `i++`. Consider using `++{variable}` instead of `{variable}++` 
 ### Files Found: 
 There are 6 instances of this issue. 
 - File: contracts/BlurExchange.sol - line 199 
 - File: contracts/BlurExchange.sol - line 476 
 - File: contracts/PolicyManager.sol - line 77 
 
### Mitigation: 
Consider using `++i` instead of `i++` 

---- 
 ## Unused/empty `receive()/fallback()` function 
 ### Files Found: 
 There are 1 instances of this issue. 
 - File: contracts/BlurExchange.sol - line 53 
 
 ### Mitigation: 
 Either implement, emit or remove the empty callback 

 --- 
 ## Using private rather than public for constants, saves gas 
 ### Files Found: 
 There are 3 instances of this issue. 
 - File: contracts/BlurExchange.sol - line 57 
 - File: contracts/BlurExchange.sol - line 58 
 - File: contracts/BlurExchange.sol - line 59 
 
 ### Mitigation: 
 If needed, the value can be read from the verified contract source code. Savings are due to the compiler not having to create non-payable getter functions for deployment calldata. 

 --- 
 ## `x += y` costs more gas than `x = x + y` for state variables 
 ### Files Found: 
 There are 2 instances of this issue. 
 - File: contracts/BlurExchange.sol - line 208 
 - File: contracts/BlurExchange.sol - line 479 
 
 ### Mitigation: 
 Use `x = x + y` instead of `x += y` 

 --- 
 
 ## It costs more gas to initialise non-constant/non-immutable variables to zero than to let the default of zero be applied 
 ### Files Found: 
 There are 1 instances of this issue. 
 - File: contracts/PolicyManager.sol - line 77 

### Mitigation: 

uint is initialised at 0. It cost more gas to initialise variable at 0 

--- 