# BAND Mainnet guide

![band](https://github.com/obajay/nodes-Guides/assets/44331529/b42abd97-f3d7-42e0-ba0f-f4ddeb44f5cb)


[WebSite](https://www.bandprotocol.com/)\
[GitHub](https://github.com/bandprotocol/chain)
=
[EXPLORER](https://explorer.stavr.tech/Band-Mainnet/staking)
=

- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Mainnet   |   8| 16GB | 250GB    |


# 1) Auto_install script
```python
SOOON
```

# 2) Manual installation

### Preparing the server
```python
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

## GO 1.19
```python
ver="1.19" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

# Build 14.09.23
```python
cd $HOME
git clone https://github.com/bandprotocol/chain band
cd band
git checkout v2.5.4
make install


```
*******🟢UPDATE🟢******* 00.00.23
```python
SOOON
```

`bandd version --long | head`
- version: 2.5.4
- commit: e6548bbf4793829bb8e711e5ed89ba4afc710ded

```python
bandd init STAVRguide --chain-id laozi-mainnet
bandd config chain-id laozi-mainnet
```    

## Create/recover wallet
```python
bandd keys add <walletname>
  OR
bandd keys add <walletname> --recover
```

## Download Genesis
```python
wget -O genesis.json https://snapshots.polkachu.com/genesis/band/genesis.json --inet4-only
mv genesis.json ~/.band/config

```
`sha256sum $HOME/.band/config/genesis.json`
+ c776e2a5fac6ce3214845db52da6beef95fada49717eac55b0b81ad3767db7de

## Set up the minimum gas price and Peers/Seeds/Filter peers/MaxPeers
```python
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uband\"/;" ~/.band/config/app.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.band/config/config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.band/config/config.toml
seeds="ade4d8bc8cbe014af6ebdf3cb7b1e9ad36f412c0@seeds.polkachu.com:22956"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.band/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.band/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.band/config/config.toml

```
### Pruning (optional)
```python
pruning="custom"
pruning_keep_recent="1000"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.band/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.band/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.band/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.band/config/app.toml
```
### Indexer (optional) 
```bash
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.band/config/config.toml
```

## Download addrbook
```python
wget -O $HOME/.band/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/BandProtocol/addrbook.json"
```

# Create a service file
```python
sudo tee /etc/systemd/system/bandd.service > /dev/null <<EOF
[Unit]
Description=band
After=network-online.target

[Service]
User=$USER
ExecStart=$(which bandd) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# StateSync Band Mainnet
```python
SOON
```
# SnapShot Mainnet (~0.2GB) updated every 5 hours  
```python
SOON
```

## Start
```python
sudo systemctl daemon-reload
sudo systemctl enable bandd
sudo systemctl restart bandd && sudo journalctl -u bandd -f -o cat
```

### Create validator
```python
bandd tx staking create-validator \
  --amount=1000000uband \
  --pubkey=$(bandd tendermint show-validator) \
  --moniker="STAVR_Guide" \
  --identity="" \
  --details="" \
  --website="" \
  --chain-id="laozi-mainnet" \
  --commission-rate="0.10" \
  --commission-max-rate="0.20" \
  --commission-max-change-rate="0.2" \
  --min-self-delegation="1" \
  --from=YourWallet -y
```

## Delete node
```python
sudo systemctl stop bandd
sudo systemctl disable bandd
rm /etc/systemd/system/bandd.service
sudo systemctl daemon-reload
cd $HOME
rm -rf band
rm -rf .band
rm -rf $(which bandd)
```
#
### Sync Info
```python
bandd status 2>&1 | jq .SyncInfo
```
### NodeINfo
```python
bandd status 2>&1 | jq .NodeInfo
```
### Check node logs
```python
sudo journalctl -u bandd -f -o cat
```
### Check Balance
```python
bandd query bank balances bandd...addressjkl1yjgn7z09ua9vms259j
```