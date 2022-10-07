# Gas Optimization Report

This report describes opportunity for gas optimization in the order of appearance in the code. It is not an exhaustive list of extreme gas optimization, but a series of quick wins that do not hinder code clarity (in some cases even enhance it), while having a perceptible net gain.

### 1. Using Pre-increment (++i) instead of Post-increment (i++) or i+=1

In for loops, unchecked pre-increment (++i) costs lesser gas than post-increment (i++) or i+=1 (5 gas for every iteration)

i++ increments i and returns the initial value of i. Which means that for post-increment, the compiler has to create a separate temporary variable to store the initial value of i(which was not incremented).

    uint i;  // i = 0(default value)
    i++;  // separate temporary variable stores the value 0, i value becomes 1 

    uint i;
    ++i;   // no separate temporary variable created for pre-increment


**Relevant Code:**

1. BlurExchange.sol[#L198-202](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L198-L202)  :

       function cancelOrders(Order[] calldata orders) external {
             for (uint8 i = 0; i < orders.length;i++) {
                 cancelOrder(orders[i]);
             }
          } 

Can be replaced with :

    function cancelOrders(Order[] calldata orders) external {
        for (uint8 i = 0; i < orders.length;++i) {
            cancelOrder(orders[i]);
        }
    }

2. BlurExchange.sol[#L476-480](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L476-L480) :

    for (uint8 i = 0; i < fees.length; i++) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
    }

Can be replaced with:

    for (uint8 i = 0; i < fees.length;++i) {
            uint256 fee = (price * fees[i].rate) / INVERSE_BASIS_POINT;
            _transferTo(paymentToken, from, fees[i].recipient, fee);
            totalFee += fee;
    }

3. PolicyManager.sol[#L77-79](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77-L79) :

    for (uint256 i = 0; i < length; i++) {
        whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
    }

Can be replaced with:

    for (uint256 i = 0; i < length;) {
        whitelistedPolicies[i] = _whitelistedPolicies.at(cursor + i);
        unchecked {
            ++i;
        }
    }

The use of unchecked block saves gas as there would be no checks for overflow for each iteration.
The unchecked block can be used in this case as the length of the whitelistedPolicies array will not exceed 2^256 - 1 (as the total number of addresses doesn't exceed 2^256 - 1) , hence there will not be any overflows.

### 2. A function that should be declared as view

A function that does not change any state should be declared as `view` to save gas.

**Relevant Code:**
1. BlurExchange.sol[#L53](https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L53) :

Replace `function _authorizeUpgrade(address) internal override onlyOwner {}` with `function _authorizeUpgrade(address) internal view override onlyOwner {}`