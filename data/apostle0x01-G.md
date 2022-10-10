 ### [G-01] Don't Initialize Variables with Default Value

#### Impact
If a variable is not set/initialized, it is assumed to have the default value (0 for uint, false for bool, address(0) for address…). Explicitly initializing it with its default value is an anti-pattern and wastes gas.

#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::475 => uint256 totalFee = 0;
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
2022-10-blur/contracts/lib/EIP712.sol::77 => for (uint256 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```
### Mitigation
As an example: for `(uint8 i = 0; i < orders.length;) {` should be replaced with for `(uint8 i; i < orders.length;) {`

 ### [G-02] Cache Array Length Outside of Loop

#### Impact
An array's length should be cached to save gas in for-loops
Reading array length at each iteration of the loop takes 6 gas (3 for mload and 3 to place memory_offset) in the stack.

Caching the array length in the stack saves around 3 gas per iteration.

Here, I suggest storing the array's length in a variable before the for-loop, and use it instead:

#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::199 => for (uint8 i = 0; i < orders.length; i++) {
2022-10-blur/contracts/BlurExchange.sol::476 => for (uint8 i = 0; i < fees.length; i++) {
2022-10-blur/contracts/PolicyManager.sol::77 => for (uint256 i = 0; i < length; i++) {
2022-10-blur/contracts/lib/MerkleVerifier.sol::38 => for (uint256 i = 0; i < proof.length; i++) {
```


 ### [G-03] Use != 0 instead of > 0 for Unsigned Integer Comparison

#### Impact
!= 0 costs less gas compared to > 0 for unsigned integers in require statements with the optimizer enabled (6 gas)
Proof: While it may seem that > 0 is cheaper than !=, this is only true without the optimizer enabled and outside a require statement. If you enable the optimizer at 10k AND you're in a require statement, this will save gas. You can see this tweet for more proofs: https://twitter.com/gzeon/status/1485428085885640706

#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::557 => return size > 0;
```



 ### [G-04] Long Revert Strings

#### Impact
Strings that are more than 32 characters will require more than 1 storage slot, costing more gas. Consider reducing the message length to less than 32 characters or use error codes:

#### Findings:
```
2022-10-blur/contracts/BlurExchange.sol::482 => require(totalFee <= price, "Total amount of fees are more than the price");
2022-10-blur/contracts/ExecutionDelegate.sol::22 => require(contracts[msg.sender], "Contract is not approved to make transfers");
```

### [G-05] Increments can be unchecked

#### Impact
In Solidity 0.8+, there's a default overflow check on unsigned integers. It's possible to uncheck this in for-loops and save some gas at each iteration, but at the cost of some code readability, as this uncheck cannot be made inline.

#### Findings
```
./2022-10-blur/contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
./2022-10-blur/contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
./2022-10-blur/contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
./2022-10-blur/contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
./2022-10-blur/contracts/PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
```
The code would go from:
```
for (uint256 i; i < numIterations; i++) {  
 // ...  
}  
```
to:
```
for (uint256 i; i < numIterations;) {  
 // ...  
 unchecked { ++i; }  
}  
```
The risk of overflow is inexistant for a uint256 here.


### [G-06] Expressions for constant values such as a call to keccak256(), should use immutable 

#### Impact
In multiple contracts, there are constant state variables declared the following way:

This results in the keccak256 operation being performed whenever the variable is used, increasing gas costs relative to just storing the output hash.

- Each usage of a "constant" costs ~100gas more per access (still a little better than storing the result in storage, but not by much).
- Since these are not real constants, they can't be referenced from a real constant environment (e.g. from assembly, or from another library).

#### Findings
```
./2022-10-blur/contracts/lib/EIP712.sol:20: bytes32 constant public FEE_TYPEHASH = keccak256(
        "Fee(uint16 rate,address recipient)"
    );

./2022-10-blur/contracts/lib/EIP712.sol:23:    bytes32 constant public ORDER_TYPEHASH = keccak256(
        "Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)Fee(uint16 rate,address recipient)"
    );

./2022-10-blur/contracts/lib/EIP712.sol:26:    bytes32 constant public ORACLE_ORDER_TYPEHASH = keccak256(
        "OracleOrder(Order order,uint256 blockNumber)Fee(uint16 rate,address recipient)Order(address trader,uint8 side,address matchingPolicy,address collection,uint256 tokenId,uint256 amount,address paymentToken,uint256 price,uint256 listingTime,uint256 expirationTime,Fee[] fees,uint256 salt,bytes extraParams,uint256 nonce)"
    );

./2022-10-blur/contracts/lib/EIP712.sol:29:    bytes32 constant public ROOT_TYPEHASH = keccak256(
        "Root(bytes32 root)"
    );

./2022-10-blur/contracts/lib/EIP712.sol:33:    bytes32 constant EIP712DOMAIN_TYPEHASH = keccak256(
        "EIP712Domain(string name,string version,uint256 chainId,address verifyingContract)"
    );

```

#### Recommended Mitigation Steps
It is recommended to either:

- Keep the variables as constant and hard-code the bytes32 string into the smart contracts.
- Declare all the roles as immutable and perform the hashing assignment in the constructors.


### [G-07] ++i costs less gas compared to i++ or i += 1

#### Impact
++i costs less gas compared to i++ or i += 1 for unsigned integers. This is because the pre-increment operation is cheaper (about 5 GAS per iteration).


#### Findings
```
./2022-10-blur/contracts/BlurExchange.sol:199:        for (uint8 i = 0; i < orders.length; i++) {
./2022-10-blur/contracts/BlurExchange.sol:476:        for (uint8 i = 0; i < fees.length; i++) {
./2022-10-blur/contracts/lib/EIP712.sol:77:        for (uint256 i = 0; i < fees.length; i++) {
./2022-10-blur/contracts/lib/MerkleVerifier.sol:38:        for (uint256 i = 0; i < proof.length; i++) {
./2022-10-blur/contracts/PolicyManager.sol:77:        for (uint256 i = 0; i < length; i++) {
```