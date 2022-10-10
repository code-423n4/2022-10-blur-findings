## increment assignment (~113 gas optimization).
### contracts/BlurExchange.sol-`incrementNonce()`:

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L207
```diff
- nonces[msg.sender] += 1;
+ nonces[msg.sender] = nonces[msg.sender] + 1;
```

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L478
```diff
- totalFee += fee;
+ totalFee = totalFee + fee;
```

**Before**:

![before gas costs](https://ik.imagekit.io/1winv85cn8g/C4/2022-10-blur/before.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665365581813)


**After**:

![after gas costs](https://ik.imagekit.io/1winv85cn8g/C4/2022-10-blur/Screen_Shot_2022-10-10_at_09.22.57_cRqRUVsss.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665365421732)

## self increment (~882 in total)

### 1. contracts/BlurExchange.sol - `execute()`: ~5

change `i++` to `++i`.

https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L198
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/BlurExchange.sol#L475

**Before**
![gas costs before](https://ik.imagekit.io/1winv85cn8g/C4/2022-10-blur/Screen_Shot_2022-10-10_at_10.15.41_hsRZcyen-.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665368413590)

**After**
![gas cost after](https://ik.imagekit.io/1winv85cn8g/C4/2022-10-blur/Screen_Shot_2022-10-10_at_10.16.33_HapHLw5rk.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665368413277)

### 2. contracts/PolicyManager.sol: ~420
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/PolicyManager.sol#L77

**Before**
![gas costs before](https://ik.imagekit.io/1winv85cn8g/C4/2022-10-blur/Screen_Shot_2022-10-10_at_10.29.22_042ZV4aOJ.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665369192331)

**After**
![gas costs after](https://ik.imagekit.io/1winv85cn8g/C4/2022-10-blur/Screen_Shot_2022-10-10_at_10.30.07_l00YNfS-a.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665369196121)

### 3. contracts/lib/EIP712.sol: ~25
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/EIP712.sol#L77

**Before**
![gas costs before](https://ik.imagekit.io/1winv85cn8g/C4/2022-10-blur/Screen_Shot_2022-10-10_at_10.38.42_Ew4j7oRQt.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665369624687)

**After**
![gas costs after](https://ik.imagekit.io/1winv85cn8g/C4/2022-10-blur/Screen_Shot_2022-10-10_at_10.39.18_4XLXsrJm9.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665369624379)

### 4. contracts/lib/MerkleVerifier.sol: ~432
https://github.com/code-423n4/2022-10-blur/blob/2fdaa6e13b544c8c11d1c022a575f16c3a72e3bf/contracts/lib/MerkleVerifier.sol#L38

**Before**
![gas costs before](https://ik.imagekit.io/1winv85cn8g/C4/2022-10-blur/Screen_Shot_2022-10-10_at_10.46.59_tCO3OEAmT.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665370197065)

**After**
![gas costs after](https://ik.imagekit.io/1winv85cn8g/C4/2022-10-blur/Screen_Shot_2022-10-10_at_10.47.49_6kvdvueMH.png?ik-sdk-version=javascript-1.4.3&updatedAt=1665370196988)