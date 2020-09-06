# token-bind-tool
Tool to bind BEP2 tokens and BEP20 tokens

## Compile

Compile token bind tool:
```shell script
make build
```

## Preparation for binding tokens

1. Connect ledger to your computer and open Binance Chain App
2. Import bep2 token owner key:

    3.1 From ledger
    ```shell script
    bnbcli keys add bep2TokenOwner --ledger --index 0
    ```
    3.2 From mnemonic
    ```shell script
    bnbcli keys add bep2TokenOwner --recover
    ```
3. Generate temp account:
    ```shell script
    ./build/token-bind-tool initKey --network-type mainnet
    ```
    Example response:
    ```text
    Temp account: 0xde9Aa1d632b48d881B50528FC524C88474Ec8809, Explorer url: :  https://bscscan.com/address/0xde9Aa1d632b48d881B50528FC524C88474Ec8809
    ```
   
4. Transfer 1 BNB to the temp account.
   
   4.1 Cross chain transfer
   ```shell script
    bnbcli bridge transfer-out --expire-time `expr $(date +%s) + 3600` \
    --chain-id Binance-Chain-Tigris --from {keyName} --node http://dataseed4.binance.org:80 \
    --to {temp account address} --amount 100000000:BNB
    ```
   Example command:
   ```shell script
   bnbcli bridge transfer-out --expire-time `expr $(date +%s) + 3600` \
   --chain-id Binance-Chain-Tigris --from bep2TokenOwner --node http://dataseed4.binance.org:80 \
   --to 0xde9Aa1d632b48d881B50528FC524C88474Ec8809 --amount 200000000:BNB
   ```
   
   4.2 You can also transfer BNB from other Binance Smart Chain account with Metamask.

5. Prepare BEP20 contract code

    You can refer to [BEP20 Template](https://github.com/binance-chain/bsc-genesis-contract/blob/master/contracts/bep20_template/BEP20Token.template) and modify it according to your own requirements. Compile your contract with [Remix](https://remix.ethereum.org) and get contract byte code:
    ![img](pictures/compile.png)
    
6. Edit `script/contract.json`

    ```json
    {
      "contract_data": ""
    }
    ```
    Fill contract byte code to `contract_data`

## Bind BEP2 token with BEP20 token

1. Suppose you have already issued a BEP2 token, and you want to deploy a BEP20 token and bind it with existing BEP2 token:
```shell script
./script/bind.sh {network type} {bep2TokenOwnerKeyName} {password} {peggyAmount} {bep2 token symbol} {token owner} {path to bnbcli or tbnbcli}
```

Example command:
```shell script
./script/bind.sh testnet lhy "12345678" 0 ABC-520 0xaa25Aa7a19f9c426E07dee59b12f944f4d9f1DD3 $HOME/go/bin/tbnbcli
```

2. Suppose you have already issued BEP2 token, deployed BEP20 contract and sent bind transaction, now you just want to approve bind from your Ledger device:
```shell script
./build/token-bind-tool approveBindFromLedger --bep2-symbol {bep2 symbol} --bep20-contract-addr {bep20 contract address} --ledger-account-index {ledger key index} --peggy-amount {peggy amount}
```