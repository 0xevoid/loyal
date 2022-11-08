<p style="font-size:14px" align="right">
<a href="https://m.do.co/c/2cea00d4f9bble" target="_blank">Deploy your VPS using our referral link to get 100$ free bonus for 60 days <img src="https://user-images.githubusercontent.com/50621007/183284313-adf81164-6db4-4284-9ea0-bcb841936350.png" width="30"/></a>/
</p>

<p align="center">
  <img height="150" height="auto" src="https://user-images.githubusercontent.com/38981255/200131380-6f87a774-fde7-43ce-b2df-9f1eb3ab3158.JPG">
</p>

# Tutorial Loyal Testnet Validator

Dokumen Loyal Official :
> [Node Loyal](https://docs.joinloyal.io/validators/run-a-loyal-validator)

Explorer Loyal :
> [Explorer Loyal](https://ping-pub.joinloyal.io/loyal/staking")
## HARDWARE 

| Component | Minimum Requirements  |
| ------------ | ------------ |
| CPU  | Intel Core i7-8700 Hexa-Core  |
| RAM |  4 GB  |
| Storage  | 80GB NVMe SSD |

## SOFTWARE 

| Component | Minimum Requirements |
| ------------ | ------------ |
| OS |  Ubuntu 18.04 | 


## Set up your Loyal fullnode(automatic)
You can setup your Loyal fullnode in few minutes by using automated script below. It will prompt you to input your validator node name!
```
wget -O loyal.sh https://raw.githubusercontent.com/0xevoid/loyal/main/loyal.sh && chmod +x loyal.sh && ./loyal.sh
```

### Post Installation Steps

You have to make sure your validator syncs the blocks. You can use the following command to check the sync status.

```
loyald status 2>&1 | jq .SyncInfo
```

### Create wallet
To create new wallet you can use command below. Don’t forget to save the mnemonic
```
loyald keys add $LYL_WALLET
```
To recover your wallet using seed phrase (OPTIONAL) 
```
loyald keys add $LYL_WALLET --recover
```
### To get current list of wallets:
```
loyald keys list
```
##### Save wallet info
Add wallet and valoper address into variables
```
LYL_WALLET_ADDRESS=$(loyald keys show $LYL_WALLET -a)
LYL_VALOPER_ADDRESS=$(loyald keys show $LYL_WALLET --bech val -a)
echo 'export LYL_WALLET_ADDRESS='${LYL_WALLET_ADDRESS} >> $HOME/.bash_profile
echo 'export LYL_VALOPER_ADDRESS='${LYL_VALOPER_ADDRESS} >> $HOME/.bash_profile
source $HOME/.bash_profile
```
### Claim Faucet
Before creating a validator, make sure you have at least 1 LYL
> [Discord](https://discord.gg/djZvwNKx")
> Or DM Dev

To check your wallet balance:
```
loyald query bank balances $LYL_WALLET_ADDRESS
```
> If your wallet does not show any balance than probably your node is still syncing. Please wait until it finish to synchronize and then continue.

### Create validator
Before creating validator please make sure that you have at least 1 nibi (1 nibi is equal to 1000000 unibi) and your node is synchronized

```
loyald tx staking create-validator \
  --amount 8000000ulyl \
  --from $LYL_WALLET \
  --min-self-delegation "1" \
  --commission-max-rate "0.2" \
  --commission-max-change-rate "0.07" \
  --commission-rate "0.07" \
  --pubkey  $(loyald tendermint show-validator) \
  --moniker  \
  --chain-id LOYAL-1 \
  --gas-prices 1ulyl
```

### Commands you may need

- Service Management Check Logs:

```
journalctl -fu loyald -o cat
```

- Starting service:

```
systemctl start loyald
```

- Stop service:

```
systemctl stop loyald
```
- Restart Service:
```
systemctl restart loyald
```
- Sync Information:
```
loyald status 2>&1 | jq .SyncInfo
```
- Validator Information:
```
loyald status 2>&1 | jq .ValidatorInfo
```
- Node information:
```
loyald status 2>&1 | jq .NodeInfo
```
- Show Node ID:
```
loyald tendermint show-node-id
```
 - **Other**
### Wallet List:
```
loyald keys list
```
### Recover wallet using Mnemonic:
```
loyald keys add $LYL_WALLET --recover
```
### Dellet Wallet:
```
loyald keys delete $LYL_WALLET
```
### Check Wallet Balance:
```
loyald query bank balances $LYL_WALLET_ADDRESS
```
### Transfer Wallet Balance to Other Wallets:
```
loyald tx bank send $LYL_WALLET_ADDRESS <TO_WALLET_ADDRESS> 10000000ulyl
```
### VOTE
```
loyald tx gov vote 1 yes --from $LYL_WALLET --chain-id LOYAL-1
```
### Stakes, Delegates, and Awards

### Delegation Process:
```
loyald tx staking delegate $LYL_VALOPER_ADDRESS 10000000ulyl --from=$LYL_WALLET --chain-id LOYAL-1 --gas=auto --fees 250ulyl
```
### Retransfer part of the validator to another validator:
```
loyald tx staking redelegate <srcValidatorAddress> <destValidatorAddress> 10000000ulyl --from=$LYL_WALLET --chain-id LOYAL-1 --gas=auto --fees 250ulyl
```
### Withdraw all Rewards:
```
loyald tx distribution withdraw-all-rewards --from=$LYL_WALLET --chain-id LOYAL-1 --gas=auto --fees 250ulyl
```
### Withdraw rewards with commissions:
```
loyald tx distribution withdraw-rewards $LYL_VALOPER_ADDRESS --from $LYL_WALLET --commission --chain-id LOYAL-1
```

### UNJAIL:
```
nibid tx slashing unjail \
  --from $LYL_WALLET \
  --broadcast-mode block \
  --chain-id LOYAL-1 \
  --fees=250ulyl
  --gas-prices 1ulyl
```
### To Remove Node Completely:
```
sudo systemctl stop loyald
sudo systemctl disable loyald
sudo rm /etc/systemd/system/loyald* -rf
sudo rm $(which loyald) -rf
sudo rm $HOME/.loyal* -rf
sudo rm $HOME/loyal* -rf
sed -i '/LYL_/d' ~/.bash_profile
```
