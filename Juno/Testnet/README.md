# Juno Testnet guide

![juno](https://user-images.githubusercontent.com/44331529/222464046-d33e1e1f-ac42-4f9c-8c34-7f978772d5ed.png)

[WebSite](https://www.junonetwork.io/)\
[GitHub](https://github.com/CosmosContracts)
=
[EXPLORER 1](https://explorer.stavr.tech/juno-testnet/staking) \
[EXPLORER 2](https://explorer.stavr.tech/juno-testnet/staking)
=

- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Testnet   |  8 | 16GB | 200GB    |


# 1) Auto_install script
```python
wget -O juno-t https://raw.githubusercontent.com/obajay/nodes-Guides/main/Juno/Testnet/juno-t && chmod +x juno-t && ./juno-t
```

# 2) Manual installation

### Preparing the server
```python
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

## GO 1.19.5
```python
ver="1.19.5" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

# Build 09.02.23
```python
cd $HOME
git clone https://github.com/CosmosContracts/juno juno
cd juno
git checkout v13.0.0-beta.2
make install
```
*******🟢UPDATE🟢******* 00.00.23
```python
SOOON
```

`junod version --long`
- version: v13.0.0-beta.2
- commit: 6b69a3d2a9ef8bfca4bf9cb33fe53bf609aa63b6

```python
junod init STAVRguide --chain-id uni-6
junod config chain-id uni-6
```    

## Create/recover wallet
```python
junod keys add <walletname>
  OR
junod keys add <walletname> --recover
```

## Download Genesis
```python
wget -O $HOME/.juno/config/genesis.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Juno/Testnet/genesis.json"

```
`sha256sum $HOME/.juno/config/genesis.json`
+ 4c90e8bfce9b7fab824b923cbb7bdddf276f1040f3651fdb5304a4289147ea90

## Set up the minimum gas price and Peers/Seeds/Filter peers/MaxPeers
```python
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0ujunox\"/;" ~/.juno/config/app.toml
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.juno/config/config.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.juno/config/config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.juno/config/config.toml
seeds="ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@testnet-seeds.polkachu.com:12656"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.juno/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.juno/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.juno/config/config.toml

```
### Pruning (optional)
```python
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.juno/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.juno/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.juno/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.juno/config/app.toml
```
### Indexer (optional) 
```bash
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.juno/config/config.toml
```

## Download addrbook
```python
wget -O $HOME/.juno/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Juno/Testnet/addrbook.json"
```

# Create a service file
```python
sudo tee /etc/systemd/system/junod.service > /dev/null <<EOF
[Unit]
Description=juno
After=network-online.target

[Service]
User=$USER
ExecStart=$(which junod) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# StateSync Juno Mainnet
```python
soon
```
# SnapShot Mainnet (~0.2GB) updated every 5 hours  
```python
soon
```

## Start
```python
sudo systemctl daemon-reload
sudo systemctl enable junod
sudo systemctl restart junod && sudo journalctl -u junod -f -o cat
```

### Create validator
```python
junod tx staking create-validator \
--commission-rate 0.1 \
--commission-max-rate 1 \
--commission-max-change-rate 1 \
--min-self-delegation "1" \
--amount 1000000ujunox \
--pubkey $(junod tendermint show-validator) \
--from <wallet> \
--moniker="STAVRguide" \
--chain-id juno-1 \
--gas 350000 \
--identity="" \
--website="" \
--details="" -y
```

## Delete node
```python
sudo systemctl stop junod && \
sudo systemctl disable junod && \
rm /etc/systemd/system/junod.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf juno && \
rm -rf .juno && \
rm -rf $(which junod)
```
#
### Sync Info
```python
junod status 2>&1 | jq .SyncInfo
```
### NodeINfo
```python
junod status 2>&1 | jq .NodeInfo
```
### Check node logs
```python
sudo journalctl -u junod -f -o cat
```
### Check Balance
```python
junod query bank balances juno...addressjkl1yjgn7z09ua9vms259j
```
