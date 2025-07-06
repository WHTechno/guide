# 🔍 Useful Commands
## Check node status
```
wardend status
```
## Check sync status
```
wardend status 2>&1 | jq .SyncInfo
```
## Check logs
```
journalctl -u wardend -f -o cat
```
## Stop node
```
sudo systemctl stop wardend
```
## Restart node
```
sudo systemctl restart wardend
```
## Sync Status
```
wardend status 2>&1 | jq 
```
## Create wallet
```
wardend keys add $WALLET
```
## Recover Wallet
```
wardend keys add $WALLET --recover
```
## Check Balance
```
wardend query bank balances $WALLET_ADDRESS 
```
## Validator Details
```
wardend q staking validator $(wardend keys show $WALLET --bech val -a) 
```
## Jailing Info
```
wardend q slashing signing-info $(wardend tendermint show-validator) 
```
## Unjail 
```
wardend tx slashing unjail --from $WALLET --chain-id chiado_10010-1 --gas auto --gas-adjustment 1.6 --fees 250000000000000award -y 
```
## Vote
```
wardend tx gov vote 1 yes --from $WALLET --chain-id chiado_10010-1  --gas auto --gas-adjustment 1.6 --fees 250000000000000award -y 
```
