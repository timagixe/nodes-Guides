# Ojo Devnet guide

![ojo](https://user-images.githubusercontent.com/44331529/223457575-2b0b703b-372a-4e33-b725-d703edaf2bf7.png)


[WebSite](https://ojo.network/)\
[GitHub](https://github.com/ojo-network)
=
[EXPLORER](https://explorer.stavr.tech/ojo-devnet/staking)
=

- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Devnet    |   4|  8GB | 150GB    |


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

# Build 06.03.23
```python
cd $HOME
git clone https://github.com/ojo-network/ojo
cd ojo
git checkout v0.1.2
make install
```
*******🟢UPDATE🟢******* 00.00.23
```python
SOOON
```

`ojod version --long`
- version: v2.0.0
- commit: ad5a2377134aa13d7d76575b95613cf8ed12d1e4

```python
ojod init STAVRguide --chain-id ojo-devnet
ojod config chain-id ojo-devnet
```    

## Create/recover wallet
```python
ojod keys add <walletname>
  OR
ojod keys add <walletname> --recover
```

## Download Genesis
```python
wget -O $HOME/.ojo/config/genesis.json "soon"

```
`sha256sum $HOME/.ojo/config/genesis.json`
+ 6037d1c1a89110c024fc18143eafe33fee19671b9427a4d4ac9c701f7a3c9309

## Set up the minimum gas price and Peers/Seeds/Filter peers/MaxPeers
```python
sed -i.bak -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0.0ualthea\"/;" ~/.ojo/config/app.toml
sed -i -e "s/^filter_peers *=.*/filter_peers = \"true\"/" $HOME/.ojo/config/config.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.ojo/config/config.toml
peers=""
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.ojo/config/config.toml
seeds="9aa8a73ea9364aa3cf7806d4dd25b6aed88d8152@ojo-devnet.seed.mzonder.com:11556"
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.althea/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.ojo/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.ojo/config/config.toml

```
### Pruning (optional)
```python
pruning="custom"
pruning_keep_recent="100"
pruning_keep_every="0"
pruning_interval="10"
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" $HOME/.ojo/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" $HOME/.ojo/config/app.toml
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" $HOME/.ojo/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" $HOME/.ojo/config/app.toml
```
### Indexer (optional) 
```bash
indexer="null" && \
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.ojo/config/config.toml
```

## Download addrbook
```python
wget -O $HOME/.ojo/config/addrbook.json "SOOON"
```

# Create a service file
```python
sudo tee /etc/systemd/system/ojod.service > /dev/null <<EOF
[Unit]
Description=ojo
After=network-online.target

[Service]
User=$USER
ExecStart=$(which ojod) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
# StateSync Ojo Testnet
```python
SNAP_RPC=https://rpc-ojo-devnet.mzonder.com:443
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 500)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.ojo/config/config.toml
ojod tendermint unsafe-reset-all --home /root/.ojo
systemctl restart ojod && journalctl -u ojod -f -o cat
```
# SnapShot Testnet (~0.2GB) updated every 5 hours  
```python
SOON
```

## Start
```python
sudo systemctl daemon-reload
sudo systemctl enable ojod
sudo systemctl restart ojod && sudo journalctl -u ojod -f -o cat
```

### Create validator
```python
ojod tx staking create-validator \
  --amount 1000000uojo \
  --from STAVR1 \
  --commission-max-change-rate "0.1" \
  --commission-max-rate "0.2" \
  --commission-rate "0.1" \
  --min-self-delegation "1" \
  --pubkey  $(ojod tendermint show-validator) \
  --moniker "STAVRguide" \
  --chain-id ojo-devnet \
  --identity="" \
  --details="" \
  --website="" -y
```

## Delete node
```python
sudo systemctl stop ojod && \
sudo systemctl disable ojod && \
rm /etc/systemd/system/ojod.service && \
sudo systemctl daemon-reload && \
cd $HOME && \
rm -rf ojo && \
rm -rf .ojo && \
rm -rf $(which ojod)
```
#
### Sync Info
```python
ojod status 2>&1 | jq .SyncInfo
```
### NodeINfo
```python
ojod status 2>&1 | jq .NodeInfo
```
### Check node logs
```python
sudo journalctl -u ojod -f -o cat
```
### Check Balance
```python
ojod query bank balances ojo...addressjkl1yjgn7z09ua9vms259j
```