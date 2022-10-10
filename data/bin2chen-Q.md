1:
_transferTo() Usage of deprecated transfer can revert.
The original transfer used to send eth uses a fixed stipend 2300 gas. This was used to prevent reentrancy. However this limit your protocol to interact with others contracts that need more than that to process the transaction.
```
    function _transferTo(
        address paymentToken,
        address from,
        address to,
        uint256 amount
    ) internal {
        if (amount == 0) {
            return;
        }

        if (paymentToken == address(0)) {
            /* Transfer funds in ETH. */
-          payable(to).transfer(amount);
+          (bool success, ) = payable(address(this)).call{value:amount}("");
+          require(success, "Transfer failed.");
        } else if (paymentToken == weth) {
            /* Transfer funds in WETH. */
            executionDelegate.transferERC20(weth, from, to, amount);
        } else {
            revert("Invalid payment token");
        }
    }
```

 2:
  BlurExchange.sol initialize() chainId is passed through the parameter, there is a risk of cross-chain, it is recommended to use block.chainid
 ```
     function initialize(
---     uint chainId,
        address _weth,
        IExecutionDelegate _executionDelegate,
        IPolicyManager _policyManager,
        address _oracle,
        uint _blockRange
    ) public initializer {
        __Ownable_init();
        isOpen = 1;

        DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({
            name              : name,
            version           : version,
-           chainId           : chainId,
+           chainId           : block.chainid,
            verifyingContract : address(this) 
        }));
 ```


 3:
 BlurExchange.sol is a Upgradeable contract, it is recommended that the subcontract EIP712.sol add gap, to facilitate the subsequent possible upgrade
 ```
 contract EIP712 {
..
     bytes32 DOMAIN_SEPARATOR;
..

+   uint256[49] private __gap;         
 }
 ```

 4:
 execute() suggest to compliance “Checks Effects Interactions”

 ```
     function execute(Input calldata sell, Input calldata buy)
        external
        payable
        reentrancyGuard
        whenOpen
    {

....
+       /* Mark orders as filled. */
+       cancelledOrFilled[sellHash] = true;
+       cancelledOrFilled[buyHash] = true;

        _executeFundsTransfer(
            sell.order.trader,
            buy.order.trader,
            sell.order.paymentToken,
            sell.order.fees,
            price
        );
        _executeTokenTransfer(
            sell.order.collection,
            sell.order.trader,
            buy.order.trader,
            tokenId,
            amount,
            assetType
        );

-       /* Mark orders as filled. */
-       cancelledOrFilled[sellHash] = true;
-       cancelledOrFilled[buyHash] = true;

 ```

5:
oracle in initialize() does not require not to be 0, the subsequent can be set, so it is recommended that _validateOracleAuthorization() verification to avoid address(0)
Because if oracle is 0, then _validateOracleAuthorization() signature verification will be invalid, can be arbitrary signature

```
     function _validateOracleAuthorization(
        bytes32 orderHash,
        SignatureVersion signatureVersion,
        bytes calldata extraSignature,
        uint256 blockNumber
    ) internal view returns (bool) {
+       require(oracle != address(0));    

        bytes32 oracleHash = _hashToSignOracle(orderHash, blockNumber);
```

6:
_transferFees() suggest to add the judgment recipient ! = address(0), to avoid losing funds
```
    function _transferFees(
        Fee[] calldata fees,
        address paymentToken,
        address from,
        uint256 price
    ) internal returns (uint256) {
        uint256 totalFee = 0;
        for (uint8 i = 0; i < fees.length; i++) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
+           require(fees[i].recipient != address(0));
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
        }
```



7:
ExecutionDelegate.sol transferERC20() does not detect whether the transfer is successful, part of the token transfer failure is not revert.
Although now only WETH used, but ExecutionDelegate.sol as a tool contract, it is suggest to add check, to avoid the subsequent  more paymentToken 

```
    function transferERC20(address token, address from, address to, uint256 amount)
        approvedContract
        external
        returns (bool)
    {
         require(revokedApproval[from] == false, "User has revoked approval");
_        return IERC20(token).transferFrom(from, to, amount);
+        bool result = IERC20(token).transferFrom(from, to, amount);
+        require(result);
+        return result;
    }
```