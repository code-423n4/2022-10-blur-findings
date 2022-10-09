## [L-1] owner can renounce Ownership

The Openzeppelin’s Ownable contract implements renounceOwnership. This can represent a certain risk if the ownership is renounced for any other reason than by design. Renouncing ownership will leave the contract without an owner, thereby removing any functionality that is only available to the owner.

***File : PolicyManager.sol***

PolicyManager.sol#L13

***File : BlurExchange.sol***

BlurExchange.sol#L30

***File : ExecutionDelegate.sol***

ExecutionDelegate.sol#L16




## [L-2] Consider two phases Ownership transfer

Admin calls Ownable.transferOwnership function to transfers the ownership to the new address directly. As such, there is a risk that the ownership is transferred to an invalid address, thus causing the contract to be without a owner.

***File : PolicyManager.sol***

PolicyManager.sol#L13

***File : BlurExchange.sol***

BlurExchange.sol#L30

***File : ExecutionDelegate.sol***

ExecutionDelegate.sol#L16




## [L-3] Using the transferFrom function of an ERC721 contract may freeze the user's NFT

When using the transferFrom function of an ERC721 contract to send an NFT, if the receiving address is a smart contract and does not support ERC721, the NFT can be frozen in the contract.

***File : ExecutionDelegate.sol***

ExecutionDelegate.sol#L78




## [N-1] Constants should be defined rather than using magic numbers.

Even assembly can benefit from using readable constants instead of hex/numeric literals.

***File : BlurExchange.sol***

BlurExchange.sol#L407




## [N-2] Use a more recent version of solidity.

Use a solidity version of at least 0.8.15 to correct bugs reported in previous version. One of these bugs resulted in a .push() operation on a bytes array in storage appending bytes that are not properly zero-initialized. This change significantly decreases the bytecode size (which impacts the deployment cost) 




## [N-3] Expressions for constant values such as a call to KECCAK256() should use immutable rather than constant.

While it doesn’t save any gas because the compiler knows that developers often make this mistake, it’s still best to use the right tool for the task at hand. There is a difference between constant variables and immutable variables, and they should each be used in their appropriate contexts. constants should be used for literal values written into the code, and immutable variables should be used for expressions, or values calculated in, or passed into the constructor.

***File : EIP712.sol***

EIP712.sol#L20
EIP712.sol#L23
EIP712.sol#L26
EIP712.sol#L29
EIP712.sol#L33




## [N-4] Lines are too long

Usually lines in source code are limited to 80 characters. Today’s screens are much larger so it’s reasonable to stretch this in some cases. Since the files will most likely reside in GitHub, and GitHub starts using a scroll bar in all cases when the length is over 164 characters, the lines below should be split when they reach that length

***File : EIP712.sol***

EIP712.sol#L24
EIP712.sol#L27




## [N-5] Large multiples of ten should use scientific notation

Use (e.g. 1e4) rather than decimal literals (e.g. 10000), for better code readability

***File : BlurExchange.sol***

BlurExchange.sol#L59




## [N-6] Use of block.timestamp

Block timestamps have historically been used for a variety of applications, such as entropy for random numbers (see the Entropy Illusion for further details), locking funds for periods of time, and various state-changing conditional statements that are time-dependent. Miners have the ability to adjust timestamps slightly, which can prove to be dangerous if block timestamps are used incorrectly in smart contracts.

***File : BlurExchange.sol***

BlurExchange.sol#L283
BlurExchange.sol#L283




## [N-7] Require() should be used instead of assert().

The assert function creates an error of type Panic(uint256). … Properly functioning code should never create a Panic, not even on invalid external input. If this happens, then there is a bug in your contract which you should fix.

***File : ERC1967Proxy.sol***

ERC1967Proxy.sol#L22
