# Hypersign testnet guide

![hyper](https://user-images.githubusercontent.com/44331529/192764421-3ebd6f3a-ee8f-44ef-8764-59eb0df7e63e.png)

[WEBSITE](https://hypersign.id/#home)
=

[EXPLORER 1](http://explorer.stavr.tech/HyperSign/staking) \
[EXPLORER 2](https://explorer.theamsolutions.info/hypersign-test/staking)
=

- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Testnet   |   8| 16GB | 160GB    |


# 1) Auto_install script
```python
wget -O hyper https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Hypersign/hyper && chmod +x hyper && ./hyper
```

# 2) Manual installation

### Preparing the server

```python
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential bsdmainutils git make ncdu gcc git jq chrony liblz4-tool -y
```

## GO 1.21.3
```python
ver="1.21.3"
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz"
sudo rm -rf /usr/local/go
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz"
rm "go$ver.linux-amd64.tar.gz"
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile
go version
```

# Build 14.12.23
```python
cd $HOME
git clone https://github.com/hypersign-protocol/hid-node.git
cd $HOME/hid-node
git checkout v0.2.0
make install
```

*******🟢UPDATE🟢******* 00.00.00
```python
SOOON
```

`hid-noded version --long | grep -e commit -e version`
- version v0.2.0
- commit: 583b9fd

```python
hid-noded init STAVR_guide --chain-id prajna-1
hid-noded config chain-id prajna-1
```    

## Create/recover wallet
```python
hid-noded keys add <walletname>
    OR
hid-noded keys add <walletname> --recover
```

## Download Genesis 

```python
curl -s https://raw.githubusercontent.com/hypersign-protocol/networks/master/testnet/prajna/final_genesis.json > ~/.hid-node/config/genesis.json
```
`sha256sum $HOME/.hid-node/config/genesis.json`
+ 40ef72dad62987429ab813dac1d5d2bd0f1b680eaedce07fdea0972beece6d81

## Set up the minimum gas price and Peers/Seeds/MaxPeers
```python
sed -i -e "s/^minimum-gas-prices *=.*/minimum-gas-prices = \"0uhid\"/" $HOME/.hid-node/config/app.toml
external_address=$(wget -qO- eth0.me) 
sed -i.bak -e "s/^external_address *=.*/external_address = \"$external_address:26656\"/" $HOME/.hid-node/config/config.toml
peers="636977c240470721ae1096dcc405e538a7c27d56@148.251.43.226:24656,378cd59b57643a878f8fd66c6853587d4c1aac34@38.46.220.58:16656,daf233bd26ccc63ffcf47202d72b25089ec5c6b2@142.132.135.125:16656,7b13f540715f8117de13341f1a5dd40957d8ace9@167.235.21.165:46656,09e9fbe80bcbbcf888dedf928d70cc12918d7f48@81.31.197.120:26656,de7fbd323435ae32e7f819c98ed394c7bd41c3b6@65.109.69.163:26356,ef4b86eeaabf8d1ade4278adf728f1fbd65ec833@141.95.103.138:26656,e47821bd8ade6f9df9ceaf9ac899f6f3393b49f1@65.108.134.215:26656,69e7ff3d6bc66e3f1e5f1d0794643be4ace556fc@81.0.247.152:36656,8486f27b121dd25c802fe28f8b0fd6ba2accdba0@45.151.122.3:26656,5e4fc955b23ab00f6a07cb6d56e89aafac0c85ff@167.86.85.122:47056,775e5830fb90a285d9cd1a2431c855dc693ed0ab@37.27.15.94:26656,77234414b2b057fa7201883316ea69490eb55a70@213.133.100.172:27161,45a6a6e2790583bf4dee1a3b1e4dd24fb89911b3@65.109.104.118:61056,43c8782bf72967173e98c8b13943c7f18a4a0202@5.181.190.76:26657,ca3b6037f7375ceaf1f3bf59de2d63a3cf64720e@162.55.27.107:26656,5cc2d1faff16fed70ee332fe9a87081788fd64e7@178.154.222.128:26656,dbb3c0e236843d2f02d3a9a65d25fee8576831db@144.76.97.251:26677,f24522f9153e4abeabb3f9a214801d0a21b5d71a@[2a01:4f9:4a:2864::2]:11456,b2ec1e52264efdd89183b595ddc877f2e023453c@62.171.184.126:31656,4ada7263e263023214a15343e277116985c8b528@5.161.99.136:26656,06c434618db29a55eb59b3be0bf3870b89aa6a92@185.249.227.6:26656,7ebd20716a06edfad4ebc969f49b9bdad05141c6@37.27.48.156:24356,d879996c98e624918088d10e8ddf552fcfc5cd19@65.109.237.80:26656,feda90ce3d07b384fde0f29677d60f21c5090c33@51.89.195.66:17656,a78f329d7854582c8b8224ff0c84f7a80d8eac20@185.249.227.7:26656,525e4aa4e6a10f000211bdff2a0e824b89ec8098@148.251.177.108:10956,f8d6f2cf1247b37337001e031084126da387f696@136.243.55.237:43566,4f854558dda4c7b0ef0e0f0262d1417e47007e5f@66.45.246.166:26656,a52d3842a60d893c667e42a393325c45e2ab477c@95.217.207.236:26656,b0fee95a6b8c04a1b9d0656fce7b72dc810e8530@162.55.103.44:26656,6b6796923c0675265866be182fe1c5400fe16283@42.115.180.135:26656,8860943e224d635aa94bfb2fc6ed3a5a3ff42971@190.2.146.152:40656,e7c8f8e8daac4eee629ea08dd528b25c05b195d1@65.108.238.61:56656,c5cbace43cfa0043fc8d85b3bd128b76e0872bfb@88.99.177.78:36656,7a569f0b43d98b0ee8a7ddb6aa91c57027f58e4a@23.88.55.152:37656,023c889e84b753709cc63fe21849e47f5b365c55@65.21.132.27:29156,cd447352bc9ddf8d14483fd5959481e0041eda65@178.18.254.211:26656,a6fae68113f3246d960653574f68902103002318@167.235.102.45:10656,cccc44f39832eaa9ae345fa92e47b553517765aa@176.9.121.109:41056,c8ff7a6594818fbd708917933e7b1599b9d67629@65.109.39.223:26656,ec855c80ec23d1fe41fc741db869581609cc8b52@89.179.33.97:26656,177de381a63a179f2b88516e95cae5cae261f116@173.212.198.50:26656,85ebee239df7b78d4565a5c28c2412ded3e2f72c@95.217.110.39:26156,3a3c85dd6911370bef69785989ff2545b9fe78cb@77.83.92.178:26656,0c6758a3f4554bbc67da73993bbb697764c5c534@38.242.142.227:41056,50ce7a4b00a363326e4e9f355ca82873744b4517@212.118.42.10:26656,c2c4b3990e555a1c838ea3cdfe15426fd0c8f6a6@154.12.228.93:26656,a34190ba699058d753f7bde2fa8c42b07b563ffe@135.181.246.250:3340,76533e599a58f47c9aeca805294af8d7cf7a4c7f@195.201.110.169:23456,071ec8aa9e9f96189878e45419703250c9648bb2@20.0.225.98:26656"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.hid-node/config/config.toml
seeds=""
sed -i.bak -e "s/^seeds =.*/seeds = \"$seeds\"/" $HOME/.hid-node/config/config.toml
sed -i 's/max_num_inbound_peers =.*/max_num_inbound_peers = 50/g' $HOME/.hid-node/config/config.toml
sed -i 's/max_num_outbound_peers =.*/max_num_outbound_peers = 50/g' $HOME/.hid-node/config/config.toml
```
### Pruning (optional)
```python
pruning="custom" &&
pruning_keep_recent="1000" &&
pruning_keep_every="0" &&
pruning_interval="10" &&
sed -i -e "s/^pruning *=.*/pruning = \"$pruning\"/" ~/.hid-node/config/app.toml &&
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"$pruning_keep_recent\"/" ~/.hid-node/config/app.toml &&
sed -i -e "s/^pruning-keep-every *=.*/pruning-keep-every = \"$pruning_keep_every\"/" ~/.hid-node/config/app.toml &&
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"$pruning_interval\"/" ~/.hid-node/config/app.toml
```
### Indexer (optional) 
```python
indexer="null" &&
sed -i -e "s/^indexer *=.*/indexer = \"$indexer\"/" $HOME/.hid-node/config/config.toml
```

## Download addrbook
```python
wget -O $HOME/.hid-node/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Hypersign/addrbook.json"
```

# StateSync
```python
SNAP_RPC=https://hid.rpc.t.stavr.tech:443
peers="3845ba311cee9c82469ec2f7b1e5cf8afbd9a434@hid.peer.stavr.tech:11056"
sed -i.bak -e "s/^persistent_peers *=.*/persistent_peers = \"$peers\"/" $HOME/.hid-node/config/config.toml
LATEST_HEIGHT=$(curl -s $SNAP_RPC/block | jq -r .result.block.header.height); \
BLOCK_HEIGHT=$((LATEST_HEIGHT - 100)); \
TRUST_HASH=$(curl -s "$SNAP_RPC/block?height=$BLOCK_HEIGHT" | jq -r .result.block_id.hash)

echo $LATEST_HEIGHT $BLOCK_HEIGHT $TRUST_HASH

sed -i.bak -E "s|^(enable[[:space:]]+=[[:space:]]+).*$|\1true| ; \
s|^(rpc_servers[[:space:]]+=[[:space:]]+).*$|\1\"$SNAP_RPC,$SNAP_RPC\"| ; \
s|^(trust_height[[:space:]]+=[[:space:]]+).*$|\1$BLOCK_HEIGHT| ; \
s|^(trust_hash[[:space:]]+=[[:space:]]+).*$|\1\"$TRUST_HASH\"| ; \
s|^(seeds[[:space:]]+=[[:space:]]+).*$|\1\"\"|" $HOME/.hid-node/config/config.toml
hid-noded tendermint unsafe-reset-all --home $HOME/.hid-node --keep-addr-book
systemctl restart hid-noded && journalctl -u hid-noded -f -o cat
```

# SnapShot (~0.4 GB) updated every 5 hours
```python
cd $HOME
apt install lz4
sudo systemctl stop hid-noded
cp $HOME/.hid-node/data/priv_validator_state.json $HOME/.hid-node/priv_validator_state.json.backup
rm -rf $HOME/.hid-node/data
curl -o - -L http://hid.snapshot.stavr.tech:1023/hid/hid-snap.tar.lz4 | lz4 -c -d - | tar -x -C $HOME/.hid-node --strip-components 2
mv $HOME/.hid-node/priv_validator_state.json.backup $HOME/.hid-node/data/priv_validator_state.json
wget -O $HOME/.hid-node/config/addrbook.json "https://raw.githubusercontent.com/obajay/nodes-Guides/main/Projects/Hypersign/addrbook.json"
sudo systemctl restart hid-noded && journalctl -u hid-noded -f -o cat
```

# Create a service file
```python
sudo tee /etc/systemd/system/hid-noded.service > /dev/null <<EOF
[Unit]
Description=hypersign
After=network-online.target

[Service]
User=$USER
ExecStart=$(which hid-noded) start
Restart=on-failure
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```

## Start
```python
sudo systemctl daemon-reload
sudo systemctl enable hid-noded
sudo systemctl restart hid-noded && sudo journalctl -u hid-noded -f -o cat
```

### Create validator
```python
hid-noded tx staking create-validator \
--amount=10000000uhid \
--pubkey=$(hid-noded tendermint show-validator) \
--moniker=STAVRguide \
--chain-id=prajna-1 \
--commission-rate="0.10" \
--commission-max-rate="0.20" \
--commission-max-change-rate="0.2" \
--min-self-delegation="1" \
--from=STAVR1 \
--identity="" \
--details="" \
--website="" -y
```

[🧩Services and Tools🧩](https://github.com/obajay/StateSync-snapshots/tree/main/Projects/Hypersign)
=

## Delete node
```python
sudo systemctl stop hid-noded
sudo systemctl disable hid-noded
rm /etc/systemd/system/hid-noded.service
sudo systemctl daemon-reload
cd $HOME
rm -rf hid-node
rm -rf .hid-node
rm -rf $(which hid-noded)
```

