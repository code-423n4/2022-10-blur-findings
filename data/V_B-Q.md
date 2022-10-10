### 1. immutable variables

It is reasonable to use `weth` variable as `immutable`. This will reduce gas consumption and make the code clearer.

### 2. events in open/close functions

`Opened` and `Closed` events should not be emmited in `open` and `close` functions in `BlurExchange` contract in case when the status of the contract was not changed (so value of the variable `isOpen` did not changed). It should be done in the same way it is done in `cancelOrder` function.

### 3. NonceIncremented event should have indexed parameter

`trader` parameter of `NonceIncremented` event should be `indexed`, as it has been done in all other events.

### 4. chainId constructor parameter

It is reasonable to calculate chainId using data known at the time of the call, instead of passing `chainId` as a constructor parameter.