# Light-node

{% embed url="https://docs.celestia.org/nodes/light-node" %}
Official guide documentation
{% endembed %}

## Hardware requirements[​](https://docs.celestia.org/nodes/light-node#hardware-requirements) <a href="#hardware-requirements" id="hardware-requirements"></a>

* Memory: **500 MB RAM (minimum)**
* CPU: **Single Core**
* Disk: **50 GB SSD Storage**
* Bandwidth: **56 Kbps for Download/56 Kbps for Upload**

### Dependencies install

```sh
sudo apt update && sudo apt upgrade -y
sudo apt install curl tar wget clang pkg-config libssl-dev jq build-essential git make ncdu -y
```

### Go install

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

### Initialize the light node[​](https://docs.celestia.org/nodes/light-node#initialize-the-light-node) <a href="#initialize-the-light-node" id="initialize-the-light-node"></a>

```sh
celestia light init
```

### Run node in the background using SystemD

```sh
sudo tee <<EOF >/dev/null /etc/systemd/system/celestia-lightd.service
[Unit]
Description=celestia-lightd Light Node
After=network-online.target
 
[Service]
User=root
ExecStart=/usr/local/bin/celestia light start --core.ip https://rpc-celestia.theamsolutions.info --p2p.network celestia
Restart=on-failure
RestartSec=3
LimitNOFILE=4096
 
[Install]
WantedBy=multi-user.target
EOF

# Enable and start celestia-lightd daemon
sudo systemctl enable celestia-lightd
sudo systemctl start celestia-lightd

# Check daemon logs
sudo journalctl -u celestia-lightd.service -f

```
