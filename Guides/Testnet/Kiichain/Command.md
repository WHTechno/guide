# 🔑 Key Management

## ➕ Add New Key
```
kiichaind keys add wallet
```
## ♻️ Recover Existing Key
```
kiichaind keys add wallet --recover
```
## 📋 List All Keys
```
kiichaind keys list
```
## ❌ Delete Key
```
kiichaind keys delete wallet
```
## 📤 Export Key to a File
```
kiichaiind keys export wallet
```
## 💰 Query Wallet Balance
```
kiichaind q bank balances $(lumerad keys show wallet -a)
```
## 🆕 Create New Validator
```
kiichaind tx staking create-validator <(cat <<EOF
{
  "pubkey": $(lumerad comet show-validator),
  "amount": "1000000ulume",
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
--chain-id oro_1336-1 \
--from wallet \
--gas-adjustment 1.3 \
--gas auto \
--gas-prices 0.01akii \
-y
```
## ✏️ Edit Existing Validator
```
kiichaind tx staking edit-validator \
--new-moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id oro_1336-1 \
--commission-rate 0.1 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.01akii \
-y
```
## 🔓 Unjail Validator
```
kiichaind tx slashing unjail --from wallet --chain-id oro_1336-1 --fees 500akii  -y
```
## 🕵️‍♂️ Jail Reason
```
kiichaind query slashing signing-info $(kiichaind comet show-validator)
```
## ✅ List All Active Validators
```
kiichaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
## 🚫 List All Inactive Validators
```
kiichaind q staking validators -oj --limit=3000 | jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' | jq -r '(.tokens|tonumber/pow(10; 6)|floor|tostring) + " \t " + .description.moniker' | sort -gr | nl
```
## 🔍 View Validator Details
```
kiichaind q staking validator $(kiichaind keys show wallet --bech val -a)
```
