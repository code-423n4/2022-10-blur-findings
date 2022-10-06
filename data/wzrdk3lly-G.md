## G-01 Use ++i instead of i++ or +=1 to save gas

**Description:**

There are five instances of post increment operations i++ and one instance of +=1 found throughout the code. 



see @audit tag in snippet of MerkleVerifier.sol

```
for (uint256 i = 0; i < proof.length; i++) {  // @audit - use ++i instead of i++
      bytes32 proofElement = proof[i];
      computedHash = _hashPair(computedHash, proofElement);
    }

```

**Remediation:**

replace instances of i++ or i += 1 with ++i. You save ~5 gas when performing pre increment operations. 
