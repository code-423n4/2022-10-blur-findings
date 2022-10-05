# > 0 is less efficient than != 0 for unsigned integers
The gas cost can be reduced by using != 0 instead of > 0 in the following location:

BlurExchange.sol, Line 557:

		return size > 0;

# i++ is less efficient than ++i
The gas cost can be reduced by using ++i or i += 1 instead of i++ in the following locations:

MerkleVerifier.sol, Line 38:

		for (uint256 i = 0; i < proof.length; i++) {

EIP712.sol, Line 77:

		for (uint256 i = 0; i < fees.length; i++) {

PolicyManager.sol, Line 77:

		for (uint256 i = 0; i < length; i++) {

BlurExchange.sol, Line 199:

		for (uint8 i = 0; i < orders.length; i++) {

BlurExchange.sol, Line 476:

		for (uint8 i = 0; i < fees.length; i++) {

# Initialising variables to 0 uses unecessary gas
Uninitialsed variables default to 0x0. Hence, assigning them 0 uses gas unecessarily.

MerkleVerifier.sol, Line 38:

		for (uint256 i = 0; i < proof.length; i++) {

EIP712.sol, Line 77:

		for (uint256 i = 0; i < fees.length; i++) {

PolicyManager.sol, Line 77:

		for (uint256 i = 0; i < length; i++) {

BlurExchange.sol, Line 199:

		for (uint8 i = 0; i < orders.length; i++) {

BlurExchange.sol, Line 475:

		uint256 totalFee = 0;

BlurExchange.sol, Line 476:

		for (uint8 i = 0; i < fees.length; i++) {