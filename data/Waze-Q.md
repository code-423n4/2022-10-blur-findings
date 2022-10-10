## [L-01] Missing zero address for constructor and initializer

## Code Snipped
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ERC1967Proxy.sol#L21
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L97
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L100

## Recommendation
Checking addresses against zero-address during initialization in constructor and initialize is a security best-practice. However, such checks are missing in multiple constructors or initialize.  Allowing zero-addresses will lead to contract reverts and force redeployments if there are no setters for such address variables. So we recommend to Add zero-address checks in the constructors and initialize.

## [L-02] paymentToken not checked for zero address
## Code Snipped
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L471
## Recommendation
To ensure the paymentToken isn't zero address. wes recommend to add simple check for address(0).

## [N-01] Missing indexed field
## Code Snipped
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L86
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L90
## Recommendation
event is missing indexed fields in important parameter. we recommend indexed at important field for increase creadibility.

## [N-02] File missing natspec comment
## Code Snipped
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L213-L248
## Recommendation
the file has a natspec comment  to explain utility about function or parameter. So add natspec comment to increase readability.
