# 🚀 Upgrade to `v1.0.1`

## ⚠️ Manual Upgrade

> **Upgrade height: `1283200`**  
> ⏳ **Do not upgrade before this block height is reached!**  
> 🔍 [Check current block height here](https://testnet.cosmotracker.com/lumera/block/1283200)

### 🛠️ Manual Upgrade

```
cd "$HOME" && rm -rf bin && mkdir bin && cd bin && \
wget https://github.com/LumeraProtocol/lumera/releases/download/v1.0.1/lumera_v1.0.1_linux_amd64.tar.gz && \
tar -xzvf lumera_v1.0.1_linux_amd64.tar.gz && \
chmod +x lumerad && \
sudo mv libwasmvm.x86_64.so /usr/lib/ && \
sudo ldconfig && \
sudo mv lumerad $(which lumerad) && \
rm lumera_v1.0.1_linux_amd64.tar.gz && \
sudo service lumerad restart && \
sudo journalctl -fu lumerad -o cat
```
### 🛠️Cosmovisor upgrade
```
cd "$HOME" && rm -rf bin && mkdir bin && cd bin && \
wget https://github.com/LumeraProtocol/lumera/releases/download/v1.0.1/lumera_v1.0.1_linux_amd64.tar.gz && \
tar -xzvf lumera_v1.0.1_linux_amd64.tar.gz && \
chmod +x lumerad && \
sudo mv libwasmvm.x86_64.so /usr/lib/ && \
sudo ldconfig && \
mkdir -p $HOME/.lumera/cosmovisor/upgrades/v1.0.0/bin && \
mv lumerad $HOME/.lumera/cosmovisor/upgrades/v1.0.0/bin/ && \
rm lumera_v1.0.1_linux_amd64.tar.gz
```
