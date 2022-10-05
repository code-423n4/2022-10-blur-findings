1. https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L2
Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0`

* 1a) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L2
Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0`

* 1b) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L3
Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0`

* 1c) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC1155.sol#L2
Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0`

* 1d) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/matchingPolicies/StandardPolicyERC721.sol#L2
Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0`

* 1e) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/ExecutionDelegate.sol#L2
Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0`

* 1f) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L2
Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0` 

* 1g) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L2
Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0` 

* 1h) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L2
Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0` 

* 1i) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/OrderStructs.sol#L2
Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0` 

2. https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L10
`boolean` values use more gas than `uint's` so use `uint256's` instead of `boolean` values to depict `true` and `false` states. This will save gas in the long run as the Change to a modified solidity pragma, instead of hardcoded to 0.8.13, e.g. `^0.8.0` is ran more.


3. https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77
use `++i` instead of `i++` to save gas

* 3a) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77
use `++i` instead of `i++` to save gas

* 3b) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L199
use `++i` instead of `i++` to save gas

* 3c) https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38
use `++i` instead of `i++` to save gas

4. https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57-L59
change `public` to `external` to save gas