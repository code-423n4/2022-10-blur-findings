## 1.Disable implementation contract

The BlurExchange contract does not disable initializers in the constructor, [as recommended ](https://github.com/OpenZeppelin/openzeppelin-contracts/blob/release-v4.6/contracts/proxy/utils/Initializable.sol#L42)by the
imported Initializable contract. This means that anyone can call the initializer on the
implementation contract to set the contract variables and assign the roles. To reduce the
potential attack surface, consider calling _disableInitializers in the constructor.



2.Writing to optimize
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L388

```solidity
        if (signatureVersion == SignatureVersion.Single) {
            (v, r, s) = abi.decode(extraSignature, (uint8, bytes32, bytes32));
        } else if (signatureVersion == SignatureVersion.Bulk) {
            /* If the signature was a bulk listing the merkle path musted be unpacked before the oracle signature. */
            - (bytes32[] memory merklePath, uint8 _v, bytes32 _r, bytes32 _s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
            - v = _v; r = _r; s = _s;
            + (bytes32[] memory merklePath, uint8 v, bytes32 r, bytes32 s) = abi.decode(extraSignature, (bytes32[], uint8, bytes32, bytes32));
        }
```

