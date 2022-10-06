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


---

## G-02 use != 0 instead of >0 for unsigned int comparisons to save gas

**Description:**

In the _exist function there is a use of an unsigned comparison using `>`. 

see @audit tag in snippet of BlurExchange.sol

```
function _exists(address what) internal view returns (bool) {
    uint256 size;
    assembly {
      size := extcodesize(what)
    }
    return size > 0; // @audit - use != 0 instead of >0 for unsigned int comparisons
  }

```

**Remediation:**

replace > 0 with != 0 for unsigned int comparisons to save gas

