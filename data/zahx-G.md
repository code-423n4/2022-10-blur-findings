# Gas Optimization

Github Link : https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol

Line number 199

    function cancelOrders(Order[] calldata orders) external {
        for (uint8 i = 0; i < orders.length; i++) {
            cancelOrder(orders[i]);
        }
    }

199 - for (uint8 i = 0; i < orders.length; i++)

Using a helper function instead of directly incrementing i in the loop

    function unsafe_inc(uint x) private pure returns (uint) {
        unchecked {    return x+1;    }
    }

    function cancelOrders(Order[] calldata orders) external {
        for (uint8 i = 0; i < orders.length; i = unsafe_inc(i)) {
            cancelOrder(orders[i]);
        }
    }