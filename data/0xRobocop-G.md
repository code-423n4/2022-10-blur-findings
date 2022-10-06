The state variables can be re-arranged to use less storage slots, for example the variable at [LoC 33](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L33), [LoC 63](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L63), [LoC 67](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L67) on the BlurExchange.sol contract uses 3 storage slots. But they can be arranged to use 1, the variable at LoC 33 "isOpen" can be converted from uint256 to uint32, and the variable at LoC 67 "blockRange" can be converted to uint64, and the variable at LoC 63 ''weth" it stays the same.  

It is important for this to work not only to change the type of the variables, but to be careful on using all the 256 bits for each slot.

For example:

uint32 public isOpen;
uint64 public blockRange;
address public weth;
IExecutionDelegate public executionDelegate;
IPolicyManager public policyManager;
address public oracle;

string public constant name = "Blur Exchange";
string public constant version = "1.0";
uint256 public constant INVERSE_BASIS_POINT = 10000;

mapping(bytes32 => bool) public cancelledOrFilled;
mapping(address => uint256) public nonces;