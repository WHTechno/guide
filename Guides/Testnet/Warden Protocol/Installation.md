# Warden Protocol Testnet Node Installation Guide

## 🔧 Prerequisites
```
sudo apt update && sudo apt upgrade -y
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc wget -y
```
## 📦 Install Go 1.23.1
```
cd $HOME
VER="1.23.1"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"

[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```
```
go version
```
## ⚙️ Set Environment Variables
```
echo "export WALLET=\"wallet\"" >> $HOME/.bash_profile
echo "export MONIKER=\"test\"" >> $HOME/.bash_profile
echo "export WARDEN_CHAIN_ID=\"chiado_10010-1\"" >> $HOME/.bash_profile
echo "export WARDEN_PORT=\"18\"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
## 🚀 Install Binary
```
cd $HOME
rm -rf bin
mkdir bin && cd bin
wget -O wardend https://github.com/warden-protocol/wardenprotocol/releases/download/v0.6.3/wardend-0.6.3-linux-amd64
chmod +x wardend
mv $HOME/bin/wardend $HOME/go/bin
```
```
wardend version
```
## 🌐 Initialize Node and Download Configs
```
wardend init $MONIKER --chain-id $WARDEN_CHAIN_ID
```
```
wget -O $HOME/.warden/config/genesis.json https://server-2.itrocket.net/testnet/warden/genesis.json
wget -O $HOME/.warden/config/addrbook.json https://server-2.itrocket.net/testnet/warden/addrbook.json
```
## ⚡ Configure Node
### Set peers and seeds:
```
SEEDS="8288657cb2ba075f600911685670517d18f54f3b@warden-testnet-seed.itrocket.net:18656"
PEERS="b14f35c07c1b2e58c4a1c1727c89a5933739eeea@warden-testnet-peer.itrocket.net:18656,de9e8c44039e240ff31cbf976a0d4d673d4e4734@188.165.213.192:26656"
sed -i -e "s/^seeds =.*/seeds = \"$SEEDS\"/" \
       -e "s/^persistent_peers =.*/persistent_peers = \"$PEERS\"/" $HOME/.warden/config/config.toml
```
### Set custom ports:
```
sed -i.bak -e "s%:26658%:${WARDEN_PORT}658%g;
s%:26657%:${WARDEN_PORT}657%g;
s%:26656%:${WARDEN_PORT}656%g;
s%^external_address =.*%external_address = \"$(wget -qO- eth0.me):${WARDEN_PORT}656\"%" $HOME/.warden/config/config.toml
```
### Set Minimum Gas:
```
sed -i 's|^minimum-gas-prices =.*|minimum-gas-prices = "25000000award"|' $HOME/.warden/config/app.toml
sed -i -e "s/^pruning =.*/pruning = \"custom\"/" \
       -e "s/^pruning-keep-recent =.*/pruning-keep-recent = \"100\"/" \
       -e "s/^pruning-interval =.*/pruning-interval = \"19\"/" $HOME/.warden/config/app.toml
```
## 🛠️ Create Systemd Service
```
sudo tee /etc/systemd/system/wardend.service > /dev/null <<EOF
[Unit]
Description=Warden node
After=network-online.target

[Service]
User=$USER
WorkingDirectory=$HOME/.warden
ExecStart=$(which wardend) start --home $HOME/.warden
Restart=on-failure
RestartSec=5
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## 📥 (Optional) Download Snapshot
```
wardend tendermint unsafe-reset-all --home $HOME/.warden
if curl -s --head https://server-2.itrocket.net/testnet/warden/warden_2025-07-06_3782850_snap.tar.lz4 | head -n 1 | grep "200" > /dev/null; then
  curl -L https://server-2.itrocket.net/testnet/warden/warden_2025-07-06_3782850_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.warden
else
  echo "No snapshot available, syncing from genesis"
fi
```
## ▶️ Start Node
```
sudo systemctl daemon-reload
sudo systemctl enable wardend
sudo systemctl start wardend
journalctl -u wardend -f -o cat
```
