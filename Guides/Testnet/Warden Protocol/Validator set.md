# ⚙️ Create Validator
```
cd $HOME
```
## Create validator.json file
### Format:
```
{
        "pubkey": {"@type":"/cosmos.crypto.ed25519.PubKey","key":"oWg2ISpLF405Jcm2vXV+2v4fnjodh6aafuIdeoW+rUw="},
        "amount": "1000000award",
        "moniker": "YourValidator",
        "identity": "optional identity signature (ex. UPort or Keybase)",
        "website": "validator's (optional) website",
        "security": "validator's (optional) security contact email",
        "details": "Build and Running",
        "commission-rate": "0.1",
        "commission-max-rate": "0.2",
        "commission-max-change-rate": "0.01",
        "min-self-delegation": "1"
}
```
### Show Pubkey
```
wardend tendermint show-validator
```
### 📝 Register
```
wardend tx staking create-validator validator.json --from $WALLET --chain-id chiado_10010-1 --gas auto --gas-adjustment 1.6 --fees 250000000000000award
```
