- Use a more recent version of Solidity.
	- Use latest 0.8.17 for security. [release note](https://blog.soliditylang.org/2022/09/08/solidity-0.8.17-release-announcement/)
	- Instances: files within the entire scope.

- Variable naming is strange.
	- https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L548
	- Use a more intuitive and formal name.

- Unsafe transfer methods (`transferERC721Unsafe` and `transferERC1155Unsafe`) in `ExecutionDelegate.sol` are not used.