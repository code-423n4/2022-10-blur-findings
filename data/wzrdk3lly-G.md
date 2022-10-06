## G-01 Use != 0 instead of > 0 for unsigned int comparisons to save gas

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

---
## G-02 Use bytes32 instead of string when possible 

**Description:**

use "bytes32" instead of "string" to save gas. Fitting data into fixed-size 32 byte words is cheaper than using arbitrary-length types such as strings. 

see @audit tag in snippet of BlurExchange.sol

```
// @audit - use "bytes32" instead of "string" to save gas for name and version 
string public constant name = "Blur Exchange"; 
string public constant version = "1.0";

```

**Remediation:**

Replace string with bytes32

