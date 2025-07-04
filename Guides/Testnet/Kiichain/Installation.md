
# Kiichain Testnet Node Installation Guide

## 🔧 Prerequisites
Update system and install dependencies:
```
sudo apt update
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```
## 📦 Install Go 1.23.5
```
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl https://dl.google.com/go/go1.23.5.linux-amd64.tar.gz | sudo tar -C/usr/local -zxvf -
cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF
source $HOME/.profile
go version
```
## ⚙️ Install Kiichain Node
```
cd $HOME
rm -rf kiichain
git clone https://github.com/kiichain/kiichain.git
cd kiichain
git checkout v2.0.0
make install
kiichaind version
```
## 🚀 Initialize Node
```
kiichaind init <YourNodeName> --chain-id=oro_1336-1
```
## 🌐 Download Genesis and Addrbook
```
curl -Ls https://ss-t.kiichain.nodestake.org/genesis.json > $HOME/.kiichain/config/genesis.json 
curl -Ls https://ss-t.kiichain.nodestake.org/addrbook.json > $HOME/.kiichain/config/addrbook.json
```
## ⚡ Configure Node
Set persistent peers  
```
PEERS="5b6aa55124c0fd28e47d7da091a69973964a9fe1@uno.sentry.testnet.v3.kiivalidator.com:26656,5e6b283c8879e8d1b0866bda20949f9886aff967@dos.sentry.testnet.v3.kiivalidator.com:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.kiichain/config/config.toml
```
Set minimum gas price
```
sed -i 's|^minimum-gas-prices =.*|minimum-gas-prices = "1000000000akii"|' $HOME/.kiichain/config/app.toml
```
## 🛠️ Create Systemd Service
```
sudo tee /etc/systemd/system/kiichaind.service <<'EOF' > /dev/null
[Unit]
Description=Kiichain Node
After=network-online.target

[Service]
User=$USER
ExecStart=$(which kiichaind) start --home $HOME/.kiichain
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

```
## 📥 (Optional) Download Snapshot
```
SNAP_NAME=$(curl -s https://ss-t.kiichain.nodestake.org/ | egrep -o ">20.*\.tar.lz4" | tr -d ">")
curl -o - -L https://ss-t.kiichain.nodestake.org/${SNAP_NAME}  | lz4 -c -d - | tar -x -C $HOME/.kiichain
```
## ▶️ Start Node
```
sudo systemctl daemon-reload
sudo systemctl enable kiichaind
sudo systemctl start kiichaind
```
## 🔍 Useful Commands
```
# Check node status
kiichaind status

# Check sync status
kiichaind status 2>&1 | jq .SyncInfo

# Check logs
journalctl -u kiichaind -f -o cat

# Stop node
sudo systemctl stop kiichaind

# Restart node
sudo systemctl restart kiichaind
```

