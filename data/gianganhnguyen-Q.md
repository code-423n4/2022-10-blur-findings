# 1. [L-01] Uncheck `transfer` in *contracts/BlurExchange.sol*

Boolean return value for `transfer` is not checked in file contracts/BlurExchange.sol line 508:

      payable(to).transfer(amount); // I recommend:  require(payable(to).transfer(amount), "!transfer");