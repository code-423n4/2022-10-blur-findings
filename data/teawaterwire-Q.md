Couldn't find anything substantial, just mentioning two very small things here:

1. in `_validateSignatures` it's weird to be using the variable name `order` for a type `Input` (leading to confusing `order.order.trader` etc) 

2. there are no checks to prevent a user to trade with themselves (buy and sell side being the same address)