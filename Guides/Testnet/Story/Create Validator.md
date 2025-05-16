# 🛡️ Create Validator

---

## 🔑 View your validator key
```
story validator export
```
## 🔐 Export EVM private key
```
story validator export --export-evm-key
```
## 📋 View EVM private key and make a key backup
```
cat $HOME/.story/story/config/private_key.txt
```
📌 Use this private key to import your account into a wallet, e.g. Metamask or Phantom.
Add the Aeneid testnet to your wallet via faucet.
Then, copy your EVM address from the wallet and request $IP tokens.
Now you can see the balance and make transactions in the wallet app. 
## ⏳ Wait for your node to get fully synced
```
curl localhost:$(sed -n '/\[rpc\]/,/laddr/ { /laddr/ {s/.*://; s/".*//; p} }' $HOME/.story/story/config/config.toml)/status | jq
```
# 🏗️ Create validator


## 🔒 Locked validator
```
story validator create --stake 1024000000000000000000 --moniker $MONIKER --chain-id 1315 --unlocked=false
```
## 🔓 Unlocked validator
```
story validator create --stake 1024000000000000000000 --moniker $MONIKER --chain-id 1315 --unlocked=true
```
## 💾 Backup your validator private key
```
cat $HOME/.story/story/config/priv_validator_key.json
```
