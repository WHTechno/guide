
# Axone Mainnet Node Installation Guide

## 🔧 Prerequisites
Update system and install dependencies:
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
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
## 📌Set Environment Variables
```
MONIKER=Your-Moniker
echo "export MONIKER=$MONIKER" >> $HOME/.bash_profile
echo "export AXONE_CHAIN_ID=axone-1" >> $HOME/.bash_profile
echo "export AXONE_PORT=11" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
## ⚙️ Install Axone Node
```
cd $HOME
git clone https://github.com/axone-protocol/axoned
cd axoned
git checkout v12.0.0
make install
axoned version
```
## 🚀 Initialize Node
```
kiichaind init <YourNodeName> --chain-id=axone-1
```
## 🌐 Download Genesis and Addrbook
```
curl -Ls https://ss.lumera.nodestake.org/genesis.json > $HOME/.lumera/config/genesis.json 
curl -Ls https://ss.lumera.nodestake.org/addrbook.json > $HOME/.lumera/config/addrbook.json
```
## ⚡ Configure Node
Set persistent peers  
```
peers="88a89303f7efed5310d2333fc40940aaacac7d3d@217.160.102.31:26656,ea1d3b5b70ac85d553a645561fbfd95577afee4c@148.113.153.62:26656,b356ae3dbfc97a21a89db0d58fdf00c7158d4d85@142.132.131.184:26656,9b372f7335ae09b38080c4d09106821757f8f7e2@65.21.32.216:26656,46f9edbbce02f0e6cf8aac82f65fe7aedecf3abd@51.159.96.236:36656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.axoned/config/config.toml
```
Set minimum gas price
```
ssed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "17"|' \
  $HOME/.axoned/config/app.toml

sed -i -E 's|^minimum-gas-prices\s*=.*|minimum-gas-prices = "0.001uaxone"|' $HOME/.axoned/config/app.toml
```
## 🛠️ Create Systemd Service
```
sudo tee /etc/systemd/system/axoned.service > /dev/null <<EOF
[Unit]
Description=axoned Service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which axoned) start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
sudo systemctl daemon-reload
sudo systemctl enable axoned
```
## 📥 (Optional) Download Snapshot
```
SNAP_NAME=$(curl -s https://ss.lumera.nodestake.org/ | egrep -o ">20.*\.tar.lz4" | tr -d ">")
curl -o - -L https://ss.lumera.nodestake.org/${SNAP_NAME}  | lz4 -c -d - | tar -x -C $HOME/.lumera
```
## ▶️ Start Node
```
sudo systemctl daemon-reload
sudo systemctl enable axoned
sudo systemctl start axoned
```
## 🔍 Useful Commands
```
# Check node status
axoned status

# Check sync status
axoned status 2>&1 | jq .SyncInfo

# Check logs
journalctl -u axoned -f -o cat

# Stop node
sudo systemctl stop axoned

# Restart node
sudo systemctl restart axoned
```
