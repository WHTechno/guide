# 💻 Recommended Hardware
6 Cores, 16GB RAM, 400GB of storage (NVME), 100 Mb/s

---

## 🛠️ Install dependencies, if needed
```
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```
## 🚀 Install Go, if needed
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
## ⚙️ Set environment variables
```
echo "export MONIKER="WHTech"" >> $HOME/.bash_profile
echo "export STORY_CHAIN_ID="aeneid"" >> $HOME/.bash_profile
echo "export STORY_PORT="52"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
Note : Change Moniker whatever you want  
## 📥 Download binaries
```
cd $HOME
rm -rf story-geth
git clone https://github.com/piplabs/story-geth.git
cd story-geth
git checkout v1.0.2
make geth
mv build/bin/geth  $HOME/go/bin/
[ ! -d "$HOME/.story/story" ] && mkdir -p "$HOME/.story/story"
[ ! -d "$HOME/.story/geth" ] && mkdir -p "$HOME/.story/geth"
```
## 📦 Install Story client
```
cd $HOME
rm -rf story
git clone https://github.com/piplabs/story
cd story
git checkout v1.2.0
go build -o story ./client 
mkdir -p $HOME/go/bin/
mv $HOME/story/story $HOME/go/bin/
```
## 🔧 Initialize story app
```
story init --moniker $MONIKER --network $STORY_CHAIN_ID
```
## 🌐 Set seeds and peers
```
SEEDS="46b7995b0b77515380000b7601e6fc21f783e16f@story-testnet-seed.itrocket.net:52656"
PEERS="01f8a2148a94f0267af919d2eab78452c90d9864@story-testnet-peer.itrocket.net:52656,521f141a1ea0e9f23029428b41d3d3cc41862feb@34.76.242.140:26656,eb99dd98a2236fed658134ef9e78b86b58cb16ed@213.239.198.181:52656,84f26098dc3736c501de372c85bb8794a1bd50e5@65.109.69.188:26656,f86435bc0377a9b4dc64b6794f9e6fca3c8dad7a@65.109.71.80:52656,9d34ab3819aa8baa75589f99138318acfa0045f5@95.217.119.251:30900,32a52b2fd7a512c1a4803ebe0ba25ca53fad434f@88.99.216.51:26656,311cd3903e25ab85e5a26c44510fbc747ab61760@152.53.87.97:36656,db6791a8e35dee076de75aebae3c89df8bba3374@65.109.50.22:56656,9308260b6cb4ca1faa9f3025bac0bc2636c4b020@185.232.68.94:26656,36b4dc63121ecde21a7e1f884768d456fc059e0e@152.53.240.138:62656,40bafe5bf4c0c8913be7299b97b93fd8daecb138@188.68.38.127:20656"
sed -i -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*seeds *=.*/seeds = \"$SEEDS\"/}" \
       -e "/^\[p2p\]/,/^\[/{s/^[[:space:]]*persistent_peers *=.*/persistent_peers = \"$PEERS\"/}" $HOME/.story/story/config/config.toml
```
## 🔌 Set custom ports in story.toml file
```
sed -i.bak -e "s%:1317%:${STORY_PORT}317%g;
s%:8551%:${STORY_PORT}551%g" $HOME/.story/story/config/story.toml
```
## 🔌 Set custom ports in config.toml file
```
sed -i.bak -e "s%:26658%:${STORY_PORT}658%g;
s%:26657%:${STORY_PORT}657%g;
s%:26656%:${STORY_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${STORY_PORT}656\"%;
s%:26660%:${STORY_PORT}660%g" $HOME/.story/story/config/config.toml
```
## 📈 Enable Prometheus and disable indexing
```
sed -i -e "s/prometheus = false/prometheus = true/" $HOME/.story/story/config/config.toml
sed -i -e "s/^indexer *=.*/indexer = \"null\"/" $HOME/.story/story/config/config.toml
```
## ⚙️ Create geth service file
```
sudo tee /etc/systemd/system/story-geth.service > /dev/null <<EOF
[Unit]
Description=Story Geth daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$HOME/go/bin/geth --aeneid --syncmode full --http --http.api eth,net,web3,engine --http.vhosts '*' --http.addr 0.0.0.0 --http.port ${STORY_PORT}545 --authrpc.port ${STORY_PORT}551 --ws --ws.api eth,web3,net,txpool --ws.addr 0.0.0.0 --ws.port ${STORY_PORT}546
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
## ⚙️ Create story service file
```
sudo tee /etc/systemd/system/story.service > /dev/null <<EOF
[Unit]
Description=Story Service
After=network.target

[Service]
User=$USER
WorkingDirectory=$HOME/.story/story
ExecStart=$(which story) run

Restart=on-failure
RestartSec=5
LimitNOFILE=65535
[Install]
WantedBy=multi-user.target
EOF
```
## 📦 Download snapshots
```
# backup priv_validator_state.json
cp $HOME/.story/story/data/priv_validator_state.json $HOME/.story/story/priv_validator_state.json.backup

# remove old data and unpack Story snapshot
rm -rf $HOME/.story/story/data
curl https://server-3.itrocket.net/testnet/story/story_2025-05-16_4443603_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/story

# restore priv_validator_state.json
mv $HOME/.story/story/priv_validator_state.json.backup $HOME/.story/story/data/priv_validator_state.json

# delete geth data and unpack Geth snapshot
rm -rf $HOME/.story/geth/aeneid/geth/chaindata
mkdir -p $HOME/.story/geth/aeneid/geth
curl https://server-3.itrocket.net/testnet/story/geth_story_2025-05-16_4443603_snap.tar.lz4 | lz4 -dc - | tar -xf - -C $HOME/.story/geth/aeneid/geth
```
## ▶️ Enable and start geth & story services
```
sudo systemctl daemon-reload
sudo systemctl enable story story-geth
sudo systemctl restart story-geth && sleep 5 && sudo systemctl restart story
```
## 📝 Check logs
```
journalctl -u story -u story-geth -f
```
