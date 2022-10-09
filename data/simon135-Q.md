## don’t use a static chainid because your vulnerable to replay attacks
If etherum forks that will be problem and can cause issues with static `chainId`
```
DOMAIN_SEPARATOR = _hashDomain(
EIP712Domain({
name: name,
version: version,
chainId: chainId,
verifyingContract: address(this)
})
);
```
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L37

## owner can remove what every  policys and cause users txs to revert
if an owner calls remove policys in the a block right before a users tx is exuecting it will cause the users to revert.
make timelock and wait some time for owner to remove policys
```
   function removePolicy(address policy) external override onlyOwner {
        require(_whitelistedPolicies.contains(policy), "Not whitelisted");
        _whitelistedPolicies.remove(policy);

        emit PolicyRemoved(policy);
    }
```
https://github.com/code-423n4/2022-10-blur/blob/d1c22a94ed08b08fe3f7d5c96e973d80d3dc0e54/contracts/PolicyManager.sol#L37
