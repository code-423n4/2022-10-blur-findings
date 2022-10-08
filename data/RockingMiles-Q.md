## Missing fee parameter validation


Some fee parameters of functions are not checked for invalid values. Validate the parameters:
        
        
### Code instances:

        EIP712._packFees (fees)
        BlurExchange._executeFundsTransfer (fees)
        EIP712._hashFee (fee)
        BlurExchange._transferFees (fees)



## Require with empty message

The following requires are with empty messages. 
This is very important to add a message for any require. So the user has enough information to know the reason of failure.
### Code instances:

        Solidity file: BlurExchange.sol, In line 134 with Empty Require message.
        Solidity file: BlurExchange.sol, In line 183 with Empty Require message.



## Not verified input


external / public functions parameters should be validated to make sure the address is not 0.
Otherwise if not given the right input it can mistakenly lead to loss of user funds.    

### Code instances:

        ExecutionDelegate.sol.transferERC721Unsafe collection
        ExecutionDelegate.sol.transferERC721 collection
        ExecutionDelegate.sol.transferERC20 from
        MockERC20.sol.mint to
        MockERC721.sol.mint to
        BlurExchange.sol.initialize _oracle
        ExecutionDelegate.sol.approveContract _contract
        ExecutionDelegate.sol.transferERC20 token
        ExecutionDelegate.sol.denyContract _contract
        PolicyManager.sol.addPolicy policy
        ExecutionDelegate.sol.transferERC1155 from
        ExecutionDelegate.sol.transferERC1155 to
        ExecutionDelegate.sol.transferERC20 to
        ExecutionDelegate.sol.transferERC721Unsafe to
        ExecutionDelegate.sol.transferERC721 from
        MockERC1155.sol.mint to
        ExecutionDelegate.sol.transferERC1155 collection
        PolicyManager.sol.removePolicy policy
        ExecutionDelegate.sol.transferERC721Unsafe from
        BlurExchange.sol.setOracle _oracle
        ExecutionDelegate.sol.transferERC721 to
        BlurExchange.sol.initialize _weth



## Init frontrun

Most contracts use an init pattern (instead of a constructor) to initialize contract parameters. Unless these are enforced to be atomic with contact deployment via deployment script or factory contracts, they are susceptible to front-running race conditions where an attacker/griefer can front-run (cannot access control because admin roles are not initialized) to initially with their own (malicious) parameters upon detecting (if an event is emitted) which the contract deployer has to redeploy wasting gas and risking other transactions from interacting with the attacker-initialized contract.

Many init functions do not have an explicit event emission which makes monitoring such scenarios harder. All of them have re-init checks; while many are explicit some (those in auction contracts) have implicit reinit checks in initAccessControls() which is better if converted to an explicit check in the main init function itself.
(details credit to: https://github.com/code-423n4/2021-09-sushimiso-findings/issues/64)
The vulnerable initialization functions in the codebase are: 

### Code instance:

        BlurExchange.sol, initialize, 95



## Named return issue

Users can mistakenly think that the return value is the named return, but it is actually the actualreturn statement that comes after. To know that the user needs to read the code and is confusing.
Furthermore, removing either the actual return or the named return will save gas. 

### Code instances:

        EIP712.sol, _hashToSignRoot
        BlurExchange.sol, _canMatchOrders
        ERC1967Proxy.sol, _implementation
        EIP712.sol, _hashToSign
        EIP712.sol, _hashToSignOracle



## Two Steps Verification before Transferring Ownership

The following contracts have a function that allows them an admin to change it to a different address. If the admin accidentally uses an invalid address for which they do not have the private key, then the system gets locked.
It is important to have two steps admin change where the first is announcing a pending new admin and the new address should then claim its ownership. 
A similar issue was reported in a previous contest and was assigned a severity of medium: [code-423n4/2021-06-realitycards-findings#105](https://github.com/code-423n4/2021-06-realitycards-findings/issues/105) 

### Code instances:

        IBlurExchange.sol
        BlurExchange.sol



## Assert instead require to validate user inputs


        From solidity docs: Properly functioning code should never reach a failing assert statement; if this happens there is a bug in your contract which you should fix.
        With assert the user pays the gas and with require it doesn't. The ETH network gas isn't cheap and users can see it as a scam.
        
### Code instance:

        ERC1967Proxy.sol : reachable assert in line 21



## Check transfer receiver is not 0 to avoid burned money


Transferring tokens to the zero address is usually prohibited to accidentally avoid "burning" tokens by sending them to an unrecoverable zero address.


### Code instances:

        https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L147
        https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L109
        https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L93
        https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L507
        https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L125
        https://github.com/code-423n4/2022-10-blur/tree/main/contracts/ExecutionDelegate.sol#L78



## Add a timelock

To give more trust to users: functions that set key/critical variables should be put behind a timelock.

### Code instances:

        https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L233
        https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L215
        https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L224
        https://github.com/code-423n4/2022-10-blur/tree/main/contracts/BlurExchange.sol#L242



## transfer return value of a general ERC20 is ignored

Need to use safeTransfer instead of transfer. As there are popular tokens, such as USDT that transfer/trasnferFrom method doesn’t return anything. The transfer return value has to be checked (as there are some other tokens that returns false instead revert), that means you must 
 1. Check the transfer return value
Another popular possibility is to add a whiteList.
Those are the appearances (solidity file, line number, actual line):

### Code instances:

        ExecutionDelegate.sol, 125 (transferERC20), return IERC20(token).transferFrom(from, to, amount);
        BlurExchange.sol, 508 (_transferTo), payable(to).transfer(amount);
