# Token Transfer

Karura supports different types of tokens than Kusama, and allows various ways to transfer tokens. This guide will walk through tokens available on Karura, tools can be used for transfers, how to send transfer transactions, monitor and track these transactions. 

## Token Types

* **Native Network Token**
  * KAR
* **Native Protocol Tokens**
  * LKSM: tokenized staked KSM from the Liquid Staking protocol
  * kUSD: multi-collateralized stablecoin
* **Foreign Tokens**
  * KSM: crossed to Karura from Kusama Relay Chain
  * Tokens originated from other parachains
  * Tokens crossed from other blockchains such as ETH, renBTC or Compound CASH
* **ERC20 Tokens**
  * Token issued by ERC20 contracts deployed in Karura EVM

## Tools

* JS/TS SDK: [https://github.com/AcalaNetwork/acala.js](https://github.com/AcalaNetwork/acala.js)
* Blockchain explorer: [http://karura.subscan.io](http://karura.subscan.io)
* api-sidecar: [https://github.com/paritytech/substrate-api-sidecar](https://github.com/paritytech/substrate-api-sidecar)
* txwrapper: [https://github.com/AcalaNetwork/txwrapper](https://github.com/AcalaNetwork/txwrapper)
* SubQuery: [https://github.com/AcalaNetwork/acala-subql](https://github.com/AcalaNetwork/acala-subql)

## Send Tokens

### Transactions

#### currencies.transfer

* [https://karura.subscan.io/extrinsic?module=Currencies&call=transfer](https://karura.subscan.io/extrinsic?module=Currencies&call=transfer)
* This can be used to send any supported tokens in the network

#### currencies.transferNativeCurrency

* [https://karura.subscan.io/extrinsic?module=Currencies&call=transfer\_native\_currency](https://karura.subscan.io/extrinsic?module=Currencies&call=transfer_native_currency)
* This can be used to send native token \(KAR\). It has slightly cheaper transaction fees compare to currencies.transfer

#### balances.transfer

* [https://karura.subscan.io/extrinsic?module=Balances&call=transfer](https://karura.subscan.io/extrinsic?module=Balances&call=transfer)
* Same as `currencies.transferNativeCurrency`
* Compatible with Polkadot / Kusama and most other Substrate-based chains.

## Receive Tokens

There are multiple ways to detect incoming balance transfers:

* Monitor events
* Subscribe storage changes
* Monitor transactions

### Monitor Events

Monitoring events is a recommended way to track incoming balance transfers. It can handle **ALL** types of transfer transactions including the one that is not initiated by a transaction directly \(e.g. delayed proxy\).

#### balances.transfer

* [https://karura.subscan.io/event?module=Balances&event=Transfer](https://karura.subscan.io/event?module=Balances&event=Transfer)
* Emitted when a native token transfer happened.

#### currencies.transfer

* [https://karura.subscan.io/event?module=Currencies&event=Transferred](https://karura.subscan.io/event?module=Currencies&event=Transferred)
* Emitted when a token transfer happened.
* NOTE: This is not emitted when balances.transfer is used to make a transfer.

#### currencies.deposit

* [https://karura.subscan.io/event?module=Currencies&event=Deposited](https://karura.subscan.io/event?module=Currencies&event=Deposited)
* Emitted when a token is minted to an account. This could happen when it is a cross-chain transfer or it is a transaction minting stablecoins.

### Storage changes RPC

* [state\_subscribeStorage](https://polkadot.js.org/docs/substrate/rpc#subscribestoragekeys-vecstoragekey-storagechangeset)
  * Subscribe to a list of account balances. However, it does not guarantee subscription delivery due to connection errors or blockchain reorg.

### Monitor Tranactions

It is possible to fetch transactions in every block, check for transfer transactions, and check if the transfer transaction is successful. However, this may likely yield false-negative results i.e. deposit received but failed to recognize, due to the various ways for transfer.

Refer to Send Tokens section for direct transfer transactions. In additional, to sending transfer transactions individually, there are common utility methods to batch send transfer transactions:

#### utility.batch

* [https://karura.subscan.io/extrinsic?module=Utility&call=batch](https://karura.subscan.io/extrinsic?module=Utility&call=batch)
* This can be used to send batch transaction
* NOTE: batched transactions will always emit success events. 
  * `utility.BatchCompleted` event indicates that all transactions are successful
  * `utility.BatchInterrupted` event indicates which transaction failed. Transactions before the failed transaction are executed successfully and will not be reverted.

#### utility.batchAll

* [https://karura.subscan.io/extrinsic?module=Utility&call=batch\_all](https://karura.subscan.io/extrinsic?module=Utility&call=batch_all)
* This is similar to utility.batch but will revert all transactions upon failed transaction.
