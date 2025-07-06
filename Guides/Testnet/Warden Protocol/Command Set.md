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
