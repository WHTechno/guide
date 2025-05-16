# ⚙️ Cosmovisor Setup

---

## 📥 Install Go, if needed

```
cd $HOME
VER="1.22.5"
wget "https://golang.org/dl/go$VER.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$VER.linux-amd64.tar.gz"
rm "go$VER.linux-amd64.tar.gz"
[ ! -f ~/.bash_profile ] && touch ~/.bash_profile
echo "export PATH=$PATH:/usr/local/go/bin:~/go/bin" >> ~/.bash_profile
source $HOME/.bash_profile
[ ! -d ~/go/bin ] && mkdir -p ~/go/bin
```
## 🚀 Install and init Cosmovisor
```
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@latest
echo "export DAEMON_NAME=\"story\"" >> $HOME/.bash_profile
echo "export DAEMON_HOME=\"$HOME/.story/story\"" >> $HOME/.bash_profile
source $HOME/.bash_profile
cosmovisor init $(which story)
```
## 📂 Create directory and download current version of story
```
mkdir -p $HOME/.story/story/cosmovisor/upgrades/v1.2.0/bin
wget -O $HOME/.story/story/cosmovisor/upgrades/v1.2.0/bin/story https://github.com/piplabs/story/releases/download/v1.2.0/story-linux-amd64
chmod +x $HOME/.story/story/cosmovisor/upgrades/v1.2.0/bin/story
```
## 🔧 Update systemd service file for Cosmovisor
```
sudo tee /etc/systemd/system/story.service > /dev/null << EOF
[Unit]
Description=story node service
After=network-online.target

[Service]
User=$USER
Environment="DAEMON_NAME=story"
Environment="DAEMON_HOME=$HOME/.story/story"
Environment="DAEMON_RESTART_AFTER_UPGRADE=true"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="DAEMON_DATA_BACKUP_DIR=$HOME/.story/story/data"
ExecStart=$(which cosmovisor) run run
Restart=on-failure
RestartSec=10
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## ▶️ Enable and start Story node with Cosmovisor
```
sudo systemctl daemon-reload
sudo systemctl enable story
sudo systemctl restart story && sudo journalctl -u story -f
```
## Cosmovisor finnish setup 🎉
