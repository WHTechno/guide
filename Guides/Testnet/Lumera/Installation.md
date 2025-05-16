
# Lumera Testnet Node Installation Guide

## 🔧 Prerequisites
Update system and install dependencies:
```
sudo apt update && sudo apt upgrade -y
sudo apt-get install git curl build-essential make jq gcc snapd chrony lz4 tmux unzip bc -y
```
## 📦 Install Go 1.21.5
```
rm -rf $HOME/go
sudo rm -rf /usr/local/go
cd $HOME
curl -L https://dl.google.com/go/go1.21.5.linux-amd64.tar.gz | sudo tar -C /usr/local -zxvf -

cat <<'EOF' >>$HOME/.profile
export GOROOT=/usr/local/go
export GOPATH=$HOME/go
export GO111MODULE=on
export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin
EOF

source $HOME/.profile
go version
```
## ⚙️ Install Lumera Node
```
cd $HOME
wget https://github.com/LumeraProtocol/lumera/releases/download/v1.0.1/lumera_v1.0.1_linux_amd64.tar.gz
tar -xvf lumera_v1.0.1_linux_amd64.tar.gz
rm lumera_v1.0.1_linux_amd64.tar.gz install.sh
sudo mv libwasmvm.x86_64.so /usr/lib/
chmod +x lumerad
mv lumerad $HOME/go/bin/
lumerad version
```
## 🚀 Initialize Node
```
lumerad init <YourNodeName> --chain-id=lumera-testnet-1
```
## 🌐 Download Genesis and Addrbook
```
curl -Ls https://ss-t.lumera.nodestake.org/genesis.json > $HOME/.lumera/config/genesis.json
curl -Ls https://ss-t.lumera.nodestake.org/addrbook.json > $HOME/.lumera/config/addrbook.json
```
## ⚡ Configure Node
Seeds  
```
seeds="10a50e7a88561b22a8d1f6f0fb0b8e54412229ab@seeds.lumera.io:26656"
sed -i -e "s|^seeds *=.*|seeds = \"$seeds\"|" $HOME/.lumerad/config/config.toml
```
Pruning  
```
sed -i \
  -e 's|^pruning *=.*|pruning = "custom"|' \
  -e 's|^pruning-keep-recent *=.*|pruning-keep-recent = "100"|' \
  -e 's|^pruning-keep-every *=.*|pruning-keep-every = "0"|' \
  -e 's|^pruning-interval *=.*|pruning-interval = "19"|' \
  $HOME/.lumerad/config/app.toml
```
## 🛠️ Create Systemd Service
```
sudo tee /etc/systemd/system/lumerad.service > /dev/null <<EOF
[Unit]
Description=Lumera Daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which lumerad) start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl daemon-reload
sudo systemctl enable lumerad
```
## 📥 (Optional) Download Snapshot
```
SNAP_NAME=$(curl -s https://ss-t.lumera.nodestake.org/ | egrep -o ">20.*\.tar.lz4" | tr -d ">")
curl -o - -L https://ss-t.lumera.nodestake.org/${SNAP_NAME} | lz4 -c -d - | tar -x -C $HOME/.lumera
```
## ▶️ Start Node
```
sudo systemctl restart lumerad
journalctl -u lumerad -f -o cat
```
## 🔍 Useful Commands
```
# Check node status
lumerad status

# Check sync status
lumerad status 2>&1 | jq .SyncInfo

# Check logs
journalctl -u lumerad -f -o cat

# Stop node
sudo systemctl stop lumerad

# Restart node
sudo systemctl restart lumerad
```
