# QA Report
## [N-01] Replace inline assembly with `account.code.length`

`<address>.code.length` can be used in Solidity >= 0.8.0 to access an account's code size and check if it is a contract without inline assembly.

_There is 1 instance of this issue:_

[BlurExchange#_exists](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L548-L558)

```
File: contracts/BlurExchange.sol

548     function _exists(address what)
549         internal
550         view
551         returns (bool)
552     {
553         uint size;
554         assembly {
555             size := extcodesize(what)
556         }
557         return size > 0;
558:    }
```

Sugestion:
```
    function _exists(address what)
        internal
        view
        returns (bool)
    {
        return what.code.length != 0;
    }
```




