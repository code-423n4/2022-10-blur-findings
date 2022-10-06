## G-01 use != 0 instead of >0 for unsigned int comparisons to save gas

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

