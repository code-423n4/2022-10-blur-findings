in BlurExchange.sol 

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L57-L58

string is most expensive than bytes32 

 string public constant name = "Blur Exchange";
 string public constant version = "1.0";

if we make deploy in remix with string type

we have a cost of gas: 202910
transaction cost         :176443 gas

if we make deploy in remix with bytes32 type

we have a cost of gas: 158936
transaction cost         :138205 gas

clearly is mas cheaper : 202910 -158936=43974 

****************************************************************************************************************
in StandardPolicyERC721  and  StandardPolicyERC1155 

the logic of two functions is similar  we can save ourselves a function therefore we save gas

if we make this way  

function canMatchMaker(Order calldata Ask, Order calldata Bid,bool orderType)
        external
        pure
        returns (bool,uint256,uint256,uint256,AssetType)
    { if(orderType){
        return (
            (Ask.side != Bid.side) &&
            (Ask.paymentToken == Bid.paymentToken) &&
            (Ask.collection == Bid.collection) &&
            (Ask.tokenId == Bid.tokenId) &&
            (Ask.matchingPolicy == Bid.matchingPolicy) &&
            (Ask.price == Bid.price),
            Ask.price,
            Ask.tokenId,
            1,
            AssetType.ERC1155
        );
    }else
    {
       return (
            (Bid.side != Ask.side) &&
            (Bid.paymentToken == Ask.paymentToken) &&
            (Bid.collection == Ask.collection) &&
            (Bid.tokenId == Ask.tokenId) &&
            (Bid.matchingPolicy == Ask.matchingPolicy) &&
            (Bid.price == Ask.price),
            Bid.price,
            Bid.tokenId,
            1,
            AssetType.ERC1155
        );
    }
    }

if ordertype is true is ASK , is false bif

if we make deploy in remix with two functions 
  have a cost: 486795

if we make deploy in remix with   one  function 

have a cost 485595

clearly is mas cheaper : 486795 -485595=1200

this way is applicable for  StandardPolicyERC721 

therefore in blurExchange.sol in _canMatchOrders  it would be like this

if (sell.listingTime <= buy.listingTime) {
            /* Seller is maker. */
            require(policyManager.isPolicyWhitelisted(sell.matchingPolicy), "Policy is not whitelisted");
            (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(sell.matchingPolicy). canMatchMaker(sell, buy,1);
        } else {
            /* Buyer is maker. */
            require(policyManager.isPolicyWhitelisted(buy.matchingPolicy), "Policy is not whitelisted");
            (canMatch, price, tokenId, amount, assetType) = IMatchingPolicy(buy.matchingPolicy). canMatchMaker(buy, sell,0);
        }



