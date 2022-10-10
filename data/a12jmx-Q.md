1.

It is best practice and also unnecessary to initialize variables in for loops as they get set to 0 by default in:

Contract: PolicyManager.sol

	line 77
	
Recommendation:

	for (uint256 i; i < length; i++) {
	
Contract: EIP712.sol

	line 77
	
Recommendation:

	for (uint256 i; i < fees.length; i++) {
	
Contract: BlurExchange.sol

	line 199
	line 476
	
Recommendation:

	for (uint8 i; i < orders.length; i++) {
	for (uint8 i; i < fees.length; i++) {
	
Contract: MerkleVerifier.sol
	
	line 38
	
Recommendation:

	for (uint256 i; i < proof.length; i++) {	
	
2.

It is best practice and also unnecessary to initialize variables as they get set to 0 by default.

Contract: BlurExchange.sol
	
	line 475
	
Recommendation:

	uint256 totalFee;
	
3.

Grammer issues in comments in:

Contract: ERC1967Proxy.sol

	line 19

	“initializating” should be “initializing”
	
Contract: ExecutionDelegate.sol

	line 51 & 59

	the hyphen in “on-behalf” is unnecessary and should be “on behalf”
	
Contract: BlurExchange.sol

	line 387

	"musted" should be "had to" 
	
	line 484

	consider rephrasing to “  /* Amount that seller will receive. */ “ for better clarity