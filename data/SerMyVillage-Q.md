https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/ReentrancyGuarded.sol#L1-L20
Contract seems to be custom coded, not recommended. Please use OpenZepplin's ReentrancyGuard contract instead to ensure security.
`import "@openzeppelin/contracts/security/ReentrancyGuard.sol";`
