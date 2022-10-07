# Gas Optimization

Github Link : https://github.com/code-423n4/2022-10-blur/blob/main/contracts/BlurExchange.sol#L199

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

# Gas Optimization

Github Link : https://github.com/code-423n4/2022-10-blur/blob/main/contracts/lib/MerkleVerifier.sol#L38

Line number 38

    for (uint256 i = 0; i < proof.length; i++)

Using a helper function instead of directly incrementing i in the loop

    function unsafe_inc(uint x) private pure returns (uint) {
        unchecked {    return x+1;    }
    }

    for (uint256 i = 0; i < proof.length; i = unsafe_inc(i))

# Gas Optimization

Github Link : https://github.com/code-423n4/2022-10-blur/blob/main/contracts/PolicyManager.sol#L77

Line number 77

    for (uint256 i = 0; i < length; i++)

Using a helper function instead of directly incrementing i in the loop

    function unsafe_inc(uint x) private pure returns (uint) {
        unchecked {    return x+1;    }
    }

    for (uint256 i = 0; i < length; i = unsafe_inc(i))