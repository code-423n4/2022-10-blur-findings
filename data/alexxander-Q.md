## EIP712.sol

`bytes32 DOMAIN_SEPARATOR` and 
```
struct EIP712Domain {
        string  name;
        string  version;
        uint256 chainId;
        address verifyingContract;
    }
```
should be declared private and immutable to conform better with the OpenZeppelin implementation. Moreover, the contract is missing a function that deals with re-building the domain separator, when the chain Id changes, to prevent replay attacks. That function is  _domainSeparatorV4() in the OpenZeppelin EIP712.sol contract found at https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/cryptography/EIP712.sol.

```
 function _domainSeparatorV4() internal view returns (bytes32) {
        if (address(this) == _CACHED_THIS && block.chainid == _CACHED_CHAIN_ID) {
            return _CACHED_DOMAIN_SEPARATOR;
        } else {
            return _buildDomainSeparator(_TYPE_HASH, _HASHED_NAME, _HASHED_VERSION);
        }
    }
```

