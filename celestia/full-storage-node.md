# Full-Storage-Node

{% embed url="https://docs.celestia.org/nodes/full-storage-node#setting-up-a-celestia-full-storage-node" %}
Official guide documentation
{% endembed %}

## Hardware requirements[​](https://docs.celestia.org/nodes/light-node#hardware-requirements) <a href="#hardware-requirements" id="hardware-requirements"></a>

* Memory: **4 GB RAM (minimum)**
* CPU: **6 Cores**
* Disk: **10 TB SSD Storage**
* Bandwidth: 1 Gbps for Download/1 Gbps for Upload

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

## Celestia-node install <a href="#install-celestia-node" id="install-celestia-node"></a>

```sh
LATEST_RELEASE=$(curl -s https://api.github.com/repos/celestiaorg/celestia-node/releases/latest | jq -r '.tag_name')
echo "The latest Celestia Node release is: $LATEST_RELEASE"

cd $HOME 
rm -rf celestia-node 
git clone https://github.com/celestiaorg/celestia-node.git 
cd celestia-node/ 
git checkout $LATEST_RELEASE

# Standard build
make build

# Install the binary
make install

# Build the cel-key utility
make cel-key

celestia version
```

## Initialize the light node[​](https://docs.celestia.org/nodes/light-node#initialize-the-light-node) <a href="#initialize-the-light-node" id="initialize-the-light-node"></a>

```sh
celestia full init
```

## Keys and wallets <a href="#initialize-the-light-node" id="initialize-the-light-node"></a>

```sh
./cel-key add <key-name> --keyring-backend test --node.type full \
  --p2p.network celestia
```

## Run node in the background using SystemD

```sh
sudo tee <<EOF >/dev/null /etc/systemd/system/celestia-full.service
[Unit]
Description=celestia-fulld Full Storage Node
After=network-online.target
 
[Service]
User=root
ExecStart=/usr/local/bin/celestia full start
Restart=on-failure
RestartSec=3
LimitNOFILE=1400000
 
[Install]
WantedBy=multi-user.target
EOF

# Enable and start celestia-full daemon
sudo systemctl enable celestia-full
sudo systemctl start celestia-full

# Check daemon logs
sudo journalctl -u celestia-full.service -f
```
