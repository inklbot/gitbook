# Validator-Node

{% embed url="https://docs.celestia.org/nodes/consensus-node#run-the-validator-node" %}
Official guide documentation
{% endembed %}

## Hardware requirements[​](https://docs.celestia.org/nodes/light-node#hardware-requirements) <a href="#hardware-requirements" id="hardware-requirements"></a>

* Memory: **8 GB RAM (minimum)**
* CPU: **6 Cores**
* Disk: **500 GB SSD Storage**
* Bandwidth: **1 Gbps for Download/1 Gbps for Upload**

## Keys and wallets <a href="#initialize-the-light-node" id="initialize-the-light-node"></a>

```sh
export WALLET=set_your_wallet_name
echo $WALLET

celestia-appd keys add $WALLET

WALLET_ADDRESS=$(celestia-appd keys show $WALLET -a)
echo $WALLET_ADDRESS
echo "export WALLET_ADDRESS="$WALLET_ADDRESS >> $HOME/.bash_profile

VALOPER_ADDRESS=$(celestia-appd keys show $WALLET --bech val -a)
echo $VALOPER_ADDRESS
echo "export VALOPER_ADDRESS="$VALOPER_ADDRESS >> $HOME/.bash_profile

source $HOME/.bash_profile
```

## Create validator and delegate

```sh
celestia-appd tx staking create-validator \
    --amount 1000000utia \
    --from $WALLET \
    --commission-rate 0.1 \
    --commission-max-rate 0.2 \
    --commission-max-change-rate 0.01 \
    --min-self-delegation 1 \
    --pubkey $(celestia-appd tendermint show-validator) \
    --moniker "your_moniker" \
    --identity "your_identity" \
    --chain-id celestia \
    --gas auto --gas-adjustment 1.5 --gas-prices 0.005utia \
    -y

celestia-appd tx staking delegate celestiavaloper1p2and9ehh9rgqmefzwhzq7yhkrkd5wdddx8mgv 1000000utia --from inklbot \
    --chain-id celestia \
    --gas auto --gas-adjustment 1.5 --gas-prices 0.005utia \
    -y
```
