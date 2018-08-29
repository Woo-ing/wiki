# Transaction Process Diagram

When a transaction is submitted, it is necessary to check the chain in the transaction. Transactions that are submitted externally or have been packaged into the block are somewhat different when doing validation.

## New Transaction Process \(from network, rpc\)

Transactions submitted through an RPC or other node broadcast.

* Api SendRawTransaction Verification below steps when exist fail, then return err
* check whether fromAddr and toAddr is valid \(tx proto verification\)
* check len of Payload <= MaxDataPayLoadLength \(tx proto verification\)
* 0 < gasPrice  <= TransactionMaxGasPrice and 0 < gasLimit <= TransactionMaxGas \(tx proto verification\)
* check Alg is SECP256K1 \(tx proto verification\)
* chainID Equals, Hash Equals, Sign verify??; fail and drop;
* check nonceOfTx > nonceOfFrom 
* check Contract status is ExecutionSuccess if type of tx is TxPayloadCallType, check toAddr is equal to fromAddr if type of tx is TxPayloadDeployType
* Transaction pool Verification
* gasPrice >= minGasPriceOfTxPool & 0 < gasLimit <= maxGasLimitOfTxPool??; fail and drop;
* chainID Equals, Hash Equals, Sign verify??; fail and drop;

## Transaction in Block Process

The transaction has been packaged into the block, and the transaction is verified after receiving the block.

* Packed
* Nonce Verification: nonceOfFrom +1 == nonceOfTx ??;  nonceOfTx < nonceOfFrom +1 fail and drop, nonceOfTx > nonceOfFrom +1 fail and giveback to tx pool;
* check balance >= gasLimit \* gasPrice ??; fail and drop;
* check gasLimit >= txBaseGas\(MinGasCountPerTransaction + dataLen\*GasCountPerByte\) ??; fail and drop;
* check payload is valid ??; fail and submit; gasConsumed is txBaseGas  \( all txs passed the step tx will be on chain\)
* check gasLimit >= txBaseGas + payloasBaseGas\(TxPayloadBaseGasCount\[payloadType\]\) ??;fail and submit; gasConsumed is txGasLimit
* check balance >= gasLimit \* gasPrice + value ??;fail and submit; gasConsumed is txBaseGas + payloadsBaseGas
* transfer value from SubBalance and to AddBalance ??;fail and submit; gasConsumed is txBaseGas + payloadsBaseGas
* check gasLimit >= txBaseGas + payloadsBaseGas + gasExecution ??;fail and submit; gasConsumed is txGasLimit
* success submit gasConsumed is txBaseGas + payloadsBaseGas + gasExecution
* Verify 
* check whether fromAddr and toAddr is valid \(tx proto verification\) ??; fail and submit; 
* check len of Payload <= MaxDataPayLoadLength \(tx proto verification\) ??; fail and submit; 
* 0 < gasPrice  <= TransactionMaxGasPrice and 0 < gasLimit <= TransactionMaxGas \(tx proto verification\)
* check Alg is SECP256K1 \(tx proto verification\) ??; fail and submit; 
* chainID Equals, Hash Equals, Sign verify??; fail and drop;
* Next steps like Transaction Packed in Block Process.

