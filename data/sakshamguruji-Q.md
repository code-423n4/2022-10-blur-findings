## Outdated Version Of Openzepplin  Used
#### Description
The codebase uses outdated version of openzepplin contracts , consider using the latest version as the previous versions might have unpatched bugs .
Visit the `package.json` to view the version used  , hover above the version and it would tell the newest version of the Openzepplin's contracts.

### Remediation
Update the openzepplin's contracts to the newest version 

## Public functions not called by the contract itself should be renamed to external
#### Instances
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L17
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95

#### Remediaton
Declare these functions as external instead

## Use Scientific Notation Rather ( e.g. 1e4) Than Exponential ( e.g. 10**4 )
#### Description
Scientific notation should be used for better code readability
Instances of this issue :
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L59

## Events Are Missing Indexed Fields
#### Description
Field(s) in an event should be indexed so that events can be tracked off-chain and if there are 3 or more fields in an event then it should have 
3 indexed fields.
Instances of this issue :
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L76-L91
#### Remediation
Make the fields indexed and make 3 fields indexed in the event `OrdersMatched`

## Lack Of Comments In The Initialize Function
#### Description
In the initialize function here https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L95-L101 there is no explanation in the comments in what does the arguments signify and neither any comments where these variables are declared i.e. https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L63-L67
More Instances:
No natspec comments in the following setter functions , detailed comments should be there depicting the function of each setter function
https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L215-L249

## incrementNonce Function can be considered to change to internal 
#### Description
The msg.sender has to manually call the incrementNonce function to increment the nonce  , it should be rather incremented automatically after execution of an order.
#### Remediation
Change the function here https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L207 to internal and consider calling the function on msg.sender in the `execute` function .

## Use Of Unsafe Transfer
## Description
Use of an unsafe version of transfer is being used here https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L508
Here the return value of the transfer is not checked and the codeflow would continue even id the transfer returned a false.
The seller might not receive his fees on transfer due to a revert but the function would work just fine. 

## Remediation
Use SafeERC's safeTransfer instead to deal with non ERC20 compliant tokens . 

## Use isContract rather than using assembly
#### Description
The function here https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L548 uses assembly to determine whether the address `what` is a contract or not which increases code complexity and reduces code readability.

#### Remediation
Use isContract function provided by OpenZepplin instead https://github.com/OpenZeppelin/openzeppelin-contracts/blob/master/contracts/utils/Address.sol

 
