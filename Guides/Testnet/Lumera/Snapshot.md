# 🔧 Lumera Node Snapshot Update Guide

## 📦 Update & Install Tools

First, update your system and install the necessary tools:
```
sudo apt update && sudo apt install aria2 lz4 -y
```
## 🛑 Stop Service & Backup Validator State
```
sudo service lumerad stop
cp $HOME/.lumera/data/priv_validator_state.json $HOME/.lumera/priv_validator_state.json.backup
lumerad tendermint unsafe-reset-all --home $HOME/.lumera --keep-addr-book
```
## 📥 Download & Extract Snapshot
```
curl -L https://snapshots.stakerhouse.com/testnet/lumera/lumera_1296603.tar.lz4 | tar -Ilz4 -xf - -C $HOME/.lumera
mv $HOME/.lumera/priv_validator_state.json.backup $HOME/.lumera/data/priv_validator_state.json
```
## 🚀 Start Service & Monitor Logs
```
sudo service lumerad start && sudo journalctl -fu lumerad -o cat
```
