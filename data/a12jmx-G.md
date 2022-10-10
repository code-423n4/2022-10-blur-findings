Changing i++ to ++i in the for loop of:

Contract: PolicyManager.sol

	line 77

	will save around 5 gas per iteration.
	
Recommendation:

	for (uint256 i = 0; i < length; ++i) {
	
Contract: EIP712.sol
	
	line 77
	
Recommendation:

	for (uint256 i = 0; i < fees.length; ++i) {
	
Contract: BlurExchange.sol

	line 199
	line 476
	
Recommendation:

	for (uint8 i = 0; i < orders.length; ++i) {
	for (uint8 i = 0; i < fees.length; ++i) {
	
Contract: MerkleVerifier.sol
	
	line 38
	
Recommendation:

	for (uint256 i = 0; i < proof.length; ++i) {