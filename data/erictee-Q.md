### [L-01] ```require()``` should be used instead of ```assert()```


#### Impact
Prior to solidity version 0.8.0, hitting an assert consumes the remainder of the transaction’s available gas rather than returning it, as ```require()```/```revert()``` do. ```assert()``` should be avoided even past solidity version 0.8.0 as its [documentation](https://docs.soliditylang.org/en/v0.8.14/control-structures.html#panic-via-assert-and-error-via-require) states that “The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix”.


#### Findings:
```
contracts/lib/ERC1967Proxy.sol:L22        assert(_IMPLEMENTATION_SLOT == bytes32(uint256(keccak256("eip1967.proxy.implementation")) - 1));

```
### [L-02] Use of ```ecrecover``` is susceptible to signature malleability


#### Impact
The ```ecrecover``` function is used to recover the address from the signature. The built-in EVM precompile ecrecover is susceptible to signature malleability which could lead to replay attacks (references: https://swcregistry.io/docs/SWC-117, https://swcregistry.io/docs/SWC-121 and https://medium.com/cryptronics/signature-replay-vulnerabilities-in-smart-contracts-3b6f7596df57).

Consider using OpenZeppelin’s ECDSA library (which prevents this malleability) instead of the built-in function.


#### Findings:
```
contracts/BlurExchange.sol:L408        return ecrecover(digest, v, r, s);

```
### [L-03] ```require()```/```revert()``` statements should have descriptive strings.


#### Impact
Consider adding descriptive strings in ```require()```/```revert()```.


#### Findings:
```
contracts/BlurExchange.sol:L134        require(sell.order.side == Side.Sell);

contracts/BlurExchange.sol:L183        require(msg.sender == order.trader);

contracts/BlurExchange.sol:L452            require(msg.value == price);

```

### [L-04] zero-address checks are missing


#### Impact
Zero-address checks are a best practice for input validation of critical address parameters. Accidental use of zero-addresses may result in exceptions, burn fees/tokens, or force redeployment of contracts.

#### Findings:
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L113-L116

