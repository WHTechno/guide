# 🔑 Key Management

## ➕ Add New Key
```
axoned keys add wallet
```
## ♻️ Recover Existing Key
```
axoned keys add wallet --recover
```
## 📋 List All Keys
```
axoned keys list
```
## ❌ Delete Key
```
axoned keys delete wallet
```
## 📤 Export Key to a File
```
axoned keys export wallet
```
## 💰 Query Wallet Balance
```
axoned q bank balances $(lumerad keys show wallet -a)
```
## 🆕 Create New Validator
```
axoned tx staking create-validator <(cat <<EOF
{
  "pubkey": $(axoned comet show-validator),
  "amount": "1000000uaxone",
  "moniker": "YOUR_MONIKER_NAME",
  "identity": "YOUR_KEYBASE_ID",
  "website": "YOUR_WEBSITE_URL",
  "security": "YOUR_SECURITY_EMAIL",
  "details": "YOUR_DETAILS",
  "commission-rate": "0.1",
  "commission-max-rate": "0.20",
  "commission-max-change-rate": "0.05",
  "min-self-delegation": "1"
}
EOF
) \
--chain-id axone-1 \
--from wallet \
--gas-adjustment 1.5 \
--gas auto \
--gas-prices 0.025uaxone \
-y
```
## ✏️ Edit Existing Validator
```
axoned tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id axone-1 \
--commission-rate 0.1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.025uaxone \
-y
```
## 🔓 Unjail Validator
```
axoned tx slashing unjail --from wallet --chain-id axone-1 --gas-adjustment 1.4 --gas auto --gas-prices 0.025uaxone -y
```
## 🕵️‍♂️ Jail Reason
```
axoned query slashing signing-info $(lumerad comet show-validator)
```
## ✅ List All Active Validators
```
axoned q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
## 🚫 List All Inactive Validators
```
axoned q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
## 🔍 View Validator Details
```
axoned q staking validator $(lumerad keys show wallet --bech val -a)
```
