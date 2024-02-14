# Full-Consensus-Node

{% embed url="https://docs.celestia.org/nodes/consensus-node#setting-up-a-celestia-full-consensus-node" %}
Official guide documentation
{% endembed %}

## Hardware requirements[​](https://docs.celestia.org/nodes/light-node#hardware-requirements) <a href="#hardware-requirements" id="hardware-requirements"></a>

* Memory: **8 GB RAM**
* CPU: **Quad-Core**
* Disk: **250 GB SSD Storage**
* Bandwidth: **1 Gbps for Download/1 Gbps for Upload**

## Dependencies install

```sh
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential git make ncdu -y
```

## Go install

```sh
ver="1.21.1" 
cd $HOME 
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" 
sudo rm -rf /usr/local/go 
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" 
rm "go$ver.linux-amd64.tar.gz" 

echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile
source $HOME/.bash_profile

go version
```

## Celestia-appd install <a href="#install-celestia-node" id="install-celestia-node"></a>

```sh
LATEST_RELEASE=$(curl -s https://api.github.com/repos/celestiaorg/celestia-app/releases/latest | jq -r '.tag_name')
echo "The latest Celestia Node release is: $LATEST_RELEASE"

cd $HOME
rm -rf celestia-app
git clone https://github.com/celestiaorg/celestia-app.git
cd celestia-app
git checkout tags/$LATEST_RELEASE -b $LATEST_RELEASE
make install

celestia-appd version
```

## Initialize the Full consensus node[​](https://docs.celestia.org/nodes/light-node#initialize-the-light-node) <a href="#initialize-the-light-node" id="initialize-the-light-node"></a>

```sh
celestia-appd init "set_node_name" --chain-id celestia
```

## Copy the `genesis.json` file <a href="#initialize-the-light-node" id="initialize-the-light-node"></a>

```sh
wget -O $HOME/.celestia-app/config/genesis.json https://raw.githubusercontent.com/celestiaorg/networks/master/celestia/genesis.json
```

## Set seeds and peers <a href="#initialize-the-light-node" id="initialize-the-light-node"></a>

```sh
SEEDS="e6116822e1a5e283d8a85d3ec38f4d232274eaf3@consensus-full-seed-1.celestia-bootstrap.net:26656,cf7ac8b19ff56a9d47c75551bd4864883d1e24b5@consensus-full-seed-2.celestia-bootstrap.net:26656"
PEERS="1b92a439555a19ee6edea85b184abb6873757dae@51.89.173.96:26656,5bc86308e2b3730ce611abf622c8be752780f934@5.199.172.53:26656,87af580078c80c630625db2360b3dc19483d29cd@185.182.194.163:26656,a99cebabe607bffba1d81878450fa5a18fbc5c33@192.168.1.36:26656"
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.celestia-app/config/config.toml
```

## Run node in the background using SystemD

```sh
sudo tee /etc/systemd/system/celestia-appd.service > /dev/null <<EOF
[Unit]
Description=celestia-appd Daemon
After=network-online.target

[Service]
User=$USER
ExecStart=$(which celestia-appd) start
Restart=always
RestartSec=3
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF

# Enable and start celestia-full-consesnsus-node daemon
sudo systemctl daemon-reload
sudo systemctl enable celestia-appd
sudo systemctl restart celestia-appd

# Check daemon logs
journalctl -u celestia-appd -f
```
