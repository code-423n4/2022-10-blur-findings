# MISSING CHECKS FOR ADDRESS(0X0) WHEN ASSIGNING VALUES TO ADDRESS STATE VARIABLES

Checking addresses against zero-address during initialization or during setting is a security best-practice. However, such checks are missing in address variable initializations/changes

Impact: Allowing zero-addresses will lead to contract reverts and force redeployments if there are no setters for such address variables.

BlurExchange.sol
```
    function initialize(
        uint chainId,
        address _weth,
        IExecutionDelegate _executionDelegate,
        IPolicyManager _policyManager,
        address _oracle,
        uint _blockRange
    ) public initializer {
        __Ownable_init();
        isOpen = 1;

        DOMAIN_SEPARATOR = _hashDomain(EIP712Domain({
            name              : name,
            version           : version,
            chainId           : chainId,
            verifyingContract : address(this)
        }));

        weth = _weth;
        executionDelegate = _executionDelegate;
        policyManager = _policyManager;
        oracle = _oracle;
        blockRange = _blockRange;
    }
```

the 4 address data, `_weth, _executionDelegate, _policyManager, and _oracle` are not being check against zero address

# UPGRADE OPEN ZEPPELIN CONTRACT DEPENDENCY

An outdated OZ version is used (which has known vulnerabilities, see https://github.com/OpenZeppelin/openzeppelin-contracts/security/advisories).

Current BlurExchange use 
```
    "@openzeppelin/contracts": "4.4.1",
    "@openzeppelin/contracts-upgradeable": "^4.6.0",
```

# NO TRANSFER OWNERSHIP PATTERN

BlurExchange use OpenZeppelin Ownable contract. Ownable contract is a standard way of owning a contract. But there is a potential issue within the Ownable contract, it allows for the transfer of ownership without validating that the address is a valid address in control of some expected recipient. If this function is used incorrectly, mistype, or any unexpected input, the admin user might be lost and potentially locked up for future usage.

Consider implementing a transfer-accept ownership pattern or two-step process in those contracts when transfering ownership. This allow an owner to accept the transfer insuring that the account is controlled by a valid user.