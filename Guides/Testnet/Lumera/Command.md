# 🔑 Key Management

## ➕ Add New Key
```
lumerad keys add wallet
```
## ♻️ Recover Existing Key
```
lumerad keys add wallet --recover
```
## 📋 List All Keys
```
lumerad keys list
```
## ❌ Delete Key
```
lumerad keys delete wallet
```
## 📤 Export Key to a File
```
lumerad keys export wallet
```
## 💰 Query Wallet Balance
```
lumerad q bank balances $(lumerad keys show wallet -a)
```
## 🆕 Create New Validator
```
lumerad tx staking create-validator <(cat <<EOF
{
  "pubkey": $(lumerad comet show-validator),
  "amount": "1000000ulume",
  "moniker": "YOUR_MONIKER_NAME",
  "identity": "YOUR_KEYBASE_ID",
  "website": "YOUR_WEBSITE_URL",
  "security": "YOUR_SECURITY_EMAIL",
  "details": "YOUR_DETAILS",
  "commission-rate": "0.05",
  "commission-max-rate": "0.20",
  "commission-max-change-rate": "0.05",
  "min-self-delegation": "1"
}
EOF
) \
--chain-id lumera-testnet-1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.025ulume \
-y
```
## ✏️ Edit Existing Validator
```
lumerad tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id lumera-testnet-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.025ulume \
-y
```
## 🔓 Unjail Validator
```
lumerad tx slashing unjail --from wallet --chain-id lumera-testnet-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.025ulume -y
```
## 🕵️‍♂️ Jail Reason
```
lumerad query slashing signing-info $(lumerad comet show-validator)
```
## ✅ List All Active Validators
```
lumerad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
## 🚫 List All Inactive Validators
```
lumerad q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
## 🔍 View Validator Details
```
lumerad q staking validator $(lumerad keys show wallet --bech val -a)
```
