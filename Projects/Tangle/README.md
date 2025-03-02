# Tangle Testnet guide

![tangl](https://github.com/obajay/nodes-Guides/assets/44331529/78b39cb8-33de-4b55-ba66-852527e5a29d)


[WebSite](https://www.tangle.tools/)\
[GitHub](https://github.com/webb-tools/tangle/blob/d12e316d7c7f64fdcc76842e03944a43221285d8/chainspecs/testnet/tangle-standalone.json)
=
[EXPLORER](https://explorer.tangle.tools/?ref=blog.webb.tools) \
[TELEMETRY](https://telemetry.polkadot.io/#list/0xea63e6ac7da8699520af7fb540470d63e48eccb33f7273d2e21a935685bf1320) \
[CHAIN](https://polkadot.js.org/apps/?rpc=wss%253A%252F%252Frpc.tangle.tools&ref=blog.webb.tools#/staking)
=

- **Minimum hardware requirements**:

| Node Type |CPU | RAM  | Storage  | 
|-----------|----|------|----------|
| Testnet   |   4|  8GB | 150GB    |

### Preparing the server
```python
sudo apt update && sudo apt upgrade -y
apt install curl iptables build-essential git wget jq make gcc nano tmux htop nvme-cli pkg-config libssl-dev libleveldb-dev tar clang bsdmainutils ncdu unzip libleveldb-dev -y
```

# Build 04.10.23
```python
cd $HOME
mkdir -p $HOME/.tangle && cd $HOME/.tangle
wget -O tangle https://github.com/webb-tools/tangle/releases/download/v5.0.0/tangle-standalone-linux-amd64
chmod 744 tangle
mv tangle /usr/bin/
```

`tangle --version`
- 0.5.0-e892e17-x86_64-linux-gnu

## Download json
```python
wget -O $HOME/.tangle/tangle-standalone.json "https://raw.githubusercontent.com/webb-tools/tangle/main/chainspecs/testnet/tangle-standalone.json"
chmod 744 ~/.tangle/tangle-standalone.json

```
`sha256sum ~/.tangle/tangle-standalone.json`
+ 17f4e994b5724242a2299bffeb12c7945ab569577c8f730686e0497e7ce160e4


# Create a service file
```python
yourname=<name>
```
```python
tee /etc/systemd/system/tangle.service > /dev/null << EOF
[Unit]
Description=Tangle Validator Node
After=network-online.target
StartLimitIntervalSec=0
[Service]
User=$USER
Restart=always
RestartSec=3
LimitNOFILE=65535
ExecStart=/usr/bin/tangle \
  --base-path $HOME/.tangle/data/ \
  --name '$yourname' \
  --chain $HOME/.tangle/tangle-standalone.json \
  --node-key-file "$HOME/.tangle/node-key" \
  --port 30333 \
  --rpc-port 9933 \
  --prometheus-port 9615 \
  --auto-insert-keys \
  --validator \
  --telemetry-url "wss://telemetry.polkadot.io/submit 0" \
  --no-mdns
[Install]
WantedBy=multi-user.target
EOF
```

## Start
```python
systemctl daemon-reload
systemctl enable tangle
systemctl restart tangle && journalctl -u tangle -f -o cat
```

- After launch, we wait for our node to synchronize. You can track our condition using telemetry

[TELEMETRY](https://telemetry.polkadot.io/#list/0xea63e6ac7da8699520af7fb540470d63e48eccb33f7273d2e21a935685bf1320)
=

- After the node has synchronized, we pull out the key from our node by entering the command
```python
curl -H "Content-Type: application/json" -d '{"id":1, "jsonrpc":"2.0", "method": "author_rotateKeys", "params":[]}' http://localhost:9933
```

## BACKUP
🟢 save the located keys in `$HOME/.tangle/node-key` and `$HOME/.tangle/data/chains/tangle-standalone-testnet/keystore/`

## Creating a validator
- Go to the [website](https://polkadot.js.org/apps/?rpc=wss%253A%252F%252Frpc.tangle.tools&ref=blog.webb.tools#/staking) and first create a wallet
- We create a validator. To do this, select `Network - Staking - Accounts - Validator`

`logs`
```python
journalctl -u tangle -f -o cat
```
`restart`
```
systemctl restart tangle && journalctl -u tangle -f -o cat
```
`delete node`
```
systemctl stop tangle &&
systemctl disable tangle &&
rm /etc/systemd/system/tangle.service &&
systemctl daemon-reload &&
cd
rm -r .tangle
```
