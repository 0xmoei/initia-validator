![image copy 2](https://github.com/0xmoei/initia-validator/assets/90371338/5c8fb423-e310-45d4-ab27-8f29710b39ef)

# Official Links
 * [Moei Twitter](https://twitter.com/0xMoei)
 * [Initia discord](https://discord.gg/initia)


# Validator Program
- There are 5 phases in this program and we are at Pre-Phase right now
- First task of the Pre-Phase is Setting up a Validator node
- You must also complete other tasks to get points
- Validator Program Info: https://initia-xyz.notion.site/The-Initiation-Validator-Tasks-6d88ab0034644473907435662f9285b3
![img](https://github.com/0xmoei/initia-validator/assets/90371338/dbfff272-2b2f-4433-91ea-0e6c5ad46fa1)


# Setting up a Validator
## System Requirement
![image2](https://github.com/0xmoei/initia-validator/assets/90371338/f7aa09a4-8dfb-4c6c-b028-9d90da0ec163)

## Install Dependecies
```shell
sudo apt update && sudo apt upgrade -y
sudo apt install curl git wget htop tmux build-essential jq make lz4 gcc unzip -y
```

## Install GO
```shell
cd $HOME && \
ver="1.22.0" && \
wget "https://golang.org/dl/go$ver.linux-amd64.tar.gz" && \
sudo rm -rf /usr/local/go && \
sudo tar -C /usr/local -xzf "go$ver.linux-amd64.tar.gz" && \
rm "go$ver.linux-amd64.tar.gz" && \
echo "export PATH=$PATH:/usr/local/go/bin:$HOME/go/bin" >> $HOME/.bash_profile && \
source $HOME/.bash_profile && \
go version
```

## Install Initia
```shell
git clone https://github.com/initia-labs/initia
cd initia
git checkout v0.2.14
make build
```
```shell
mkdir -p $HOME/.initia/cosmovisor/genesis/bin
mv /root/initia/build/initiad $HOME/.initia/cosmovisor/genesis/bin/
```

## Link Genesis
```shell
sudo ln -s $HOME/.initia/cosmovisor/genesis $HOME/.initia/cosmovisor/current -f
sudo ln -s $HOME/.initia/cosmovisor/current/bin/initiad /usr/local/bin/initiad -f
```

## Install Cosmovisor
```shell
go install cosmossdk.io/tools/cosmovisor/cmd/cosmovisor@v1.5.0
```

## Create service file
```shell
sudo tee /etc/systemd/system/initiad.service > /dev/null << EOF
[Unit]
Description=initia node service
After=network-online.target

[Service]
User=$USER
ExecStart=$(which cosmovisor) run start
Restart=on-failure
RestartSec=10
LimitNOFILE=65535
Environment="DAEMON_HOME=$HOME/.initia"
Environment="DAEMON_NAME=initiad"
Environment="UNSAFE_SKIP_BACKUP=true"
Environment="PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/snap/bin:$HOME/.initia/cosmovisor/current/bin"

[Install]
WantedBy=multi-user.target
EOF
```
```shell
sudo systemctl daemon-reload
sudo systemctl enable initiad.service
```

## Set Variables
- Replace NODE_NAME
```shell
echo 'export MONIKER="NODE_NAME"' >> ~/.bash_profile
echo 'export WALLET="wallet"' >> ~/.bash_profile
source $HOME/.bash_profile
```

## Initiate Node
```shell
initiad config set client chain-id initiation-1
initiad config set client node tcp://localhost:15657
initiad config set client keyring-backend test
```
```shell
initiad init $MONIKER --chain-id initiation-1
```

## Genesis
```shell
rm ~/.initia/config/genesis.json
curl -Ls https://raw.githubusercontent.com/molla202/pokemon/main/genesis.json > $HOME/.initia/config/genesis.json
curl -Ls https://raw.githubusercontent.com/Core-Node-Team/Testnet-TR/main/Initia/addrbook.json > $HOME/.initia/config/addrbook.json
```

## Set Ports
```shell
echo "export N_PORT="15"" >> $HOME/.bash_profile
source $HOME/.bash_profile
```
 You can replace "15" if you have port conflicts or leave it alone ;)
 
## Add ports to app.toml & config.toml files
```shell
sed -i.bak -e "s%:1317%:${N_PORT}317%g;
s%:8080%:${N_PORT}080%g;
s%:9090%:${N_PORT}090%g;
s%:9091%:${N_PORT}091%g;
s%:8545%:${N_PORT}545%g;
s%:8546%:${N_PORT}546%g;
s%:6065%:${N_PORT}065%g" $HOME/.initia/config/app.toml
```
```shell
sed -i.bak -e "s%:26658%:${N_PORT}658%g;
s%:26657%:${N_PORT}657%g;
s%:6060%:${N_PORT}060%g;
s%:26656%:${N_PORT}656%g;
s%^external_address = \"\"%external_address = \"$(wget -qO- eth0.me):${N_PORT}656\"%;
s%:26660%:${N_PORT}660%g" $HOME/.initia/config/config.toml
```

## Seed
```shell
PEERS="a3660a8b7a0d88b12506787b26952930f1774fc2@65.21.69.53:48656,e3ac92ce5b790c76ce07c5fa3b257d83a517f2f6@178.18.251.146:30656,2692225700832eb9b46c7b3fc6e4dea2ec044a78@34.126.156.141:26656,2a574706e4a1eba0e5e46733c232849778faf93b@84.247.137.184:53456,40d3f977d97d3c02bd5835070cc139f289e774da@168.119.10.134:26313,1f6633bc18eb06b6c0cab97d72c585a6d7a207bc@65.109.59.22:25756,4a988797d8d8473888640b76d7d238b86ce84a2c@23.158.24.168:26656,e3679e68616b2cd66908c460d0371ac3ed7795aa@176.34.17.102:26656,d2a8a00cd5c4431deb899bc39a057b8d8695be9e@138.201.37.195:53456,329227cf8632240914511faa9b43050a34aa863e@43.131.13.84:26656,517c8e70f2a20b8a3179a30fe6eb3ad80c407c07@37.60.231.212:26656,07632ab562028c3394ee8e78823069bfc8de7b4c@37.27.52.25:19656,028999a1696b45863ff84df12ebf2aebc5d40c2d@37.27.48.77:26656,3c44f7dbb473fee6d6e5471f22fa8d8095bd3969@185.219.142.137:53456,8db320e665dbe123af20c4a5c667a17dc146f4d0@51.75.144.149:26656,c424044f3249e73c050a7b45eb6561b52d0db456@158.220.124.183:53456,767fdcfdb0998209834b929c59a2b57d474cc496@207.148.114.112:26656,edcc2c7098c42ee348e50ac2242ff897f51405e9@65.109.34.205:36656,140c332230ac19f118e5882deaf00906a1dba467@185.219.142.119:53456,4eb031b59bd0210481390eefc656c916d47e7872@37.60.248.151:53456,ff9dbc6bb53227ef94dc75ab1ddcaeb2404e1b0b@178.170.47.171:26656,ffb9874da3e0ead65ad62ac2b569122f085c0774@149.28.134.228:26656" && \
SEEDS="2eaa272622d1ba6796100ab39f58c75d458b9dbc@34.142.181.82:26656,c28827cb96c14c905b127b92065a3fb4cd77d7f6@testnet-seeds.whispernode.com:25756" && \
sed -i -e "s/^seeds *=.*/seeds = \"$SEEDS\"/; s/^persistent_peers *=.*/persistent_peers = \"$PEERS\"/" $HOME/.initia/config/config.toml
```

## Set Gas
```shell
sed -i -e "s|^minimum-gas-prices *=.*|minimum-gas-prices = \"0.15uinit,0.01uusdc\"|" $HOME/.initia/config/app.toml
```

## Prunning
```shell
sed -i -e "s/^pruning *=.*/pruning = \"custom\"/" $HOME/.initia/config/app.toml
sed -i -e "s/^pruning-keep-recent *=.*/pruning-keep-recent = \"100\"/" $HOME/.initia/config/app.toml
sed -i -e "s/^pruning-interval *=.*/pruning-interval = \"50\"/" $HOME/.initia/config/app.toml
```

## Download Snapshot of a synced node files
```shell
sudo systemctl stop initiad
```
```shell
initiad tendermint unsafe-reset-all --home $HOME/.initia --keep-addr-book
```
```shell
wget https://rpc-initia-testnet.trusted-point.com/latest_snapshot.tar.lz4
```
```shell
lz4 -d -c ./latest_snapshot.tar.lz4 | tar -xf - -C $HOME/.initia
```

## Start Node
```shell
sudo systemctl daemon-reload && \
sudo systemctl enable initiad && \
sudo systemctl restart initiad
```

## Check Logs
```shell
sudo journalctl -u initiad -f -o cat
```
- Ctrl + C to exit

## Sync Check
- True = Node is NOT synced
- False = Node is synced
```shell
initiad status | jq
```
!Only Continue the steps if you are getting FALSE !

## Create/Recover Wallet
- Create Wallet
```shell
initiad keys add $WALLET
```
- Recover Wallet
```shell
initiad keys add wallet --recover
```

## Get Faucet
- Get Faucet for your node wallet
https://faucet.testnet.initia.xyz

## Check Balance
```shell
initiad q bank balances $(initiad keys show $WALLET -a)
```
- You can import your seed phrase to initia wallet extension to send tokens or check balance too

## Create Validator
```shell
initiad tx mstaking create-validator \
  --amount=5000000uinit \
  --pubkey=$(initiad tendermint show-validator) \
  --moniker=$MONIKER \
  --chain-id=initiation-1 \
  --commission-rate=0.05 \
  --commission-max-rate=0.10 \
  --commission-max-change-rate=0.01 \
  --from=$WALLET \
  --identity="" \
  --website="https://github.com/0xMoei" \
  --details="0xMoei Validator" \
  --node=http://localhost:15657 \
  --gas-adjustment 1.4 \
  --gas auto \
  --gas-prices 0.15uinit \
  -y
```
- Save the txhash

## Delegate Tokens to your Validator
```shell
initiad tx mstaking delegate $(initiad keys show $WALLET --bech val -a)  1000000uinit --from $WALLET --gas-adjustment 1.4 --gas auto --gas-prices 0.15uinit --node=http://localhost:15657 -y
```

# Useful Commands

## Check inActive Validators (search your node name)
```shell
initiad q mstaking validators -o json --limit=1000 \
| jq '.validators[] | select(.status=="BOND_STATUS_UNBONDED")' \
| jq -r '.voting_power + " - " + .description.moniker' \
| sort -gr | nl
```

## Check Active Valdiators
```shell
initiad q mstaking validators -o json --limit=1000 \
| jq '.validators[] | select(.status=="BOND_STATUS_BONDED")' \
| jq -r '.voting_power + " - " + .description.moniker' \
| sort -gr | nl
```

## Validator Status
```shell
initiad q mstaking validator $(initiad keys show $WALLET --bech val -a)
```
### Search your validator in inactive set
https://scan.testnet.initia.xyz/initiation-1/validators

## Check Logs
```shell
sudo journalctl -u initiad -f -o cat
```

## Restart Node
```shell
sudo systemctl restart initiad
```

## Stop Node
```shell
sudo systemctl stop initiad
```

## Unjail Validator
```shell
initiad tx slashing unjail --from $WALLET --fees=0.025uinit -y
```

## Edit Validator
```shell
initiad tx mstaking edit-validator \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id initiation-1 \
--commission-rate 0.05 \
--from wallet \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.15uinit \
-y
```

## Delete Node Files
```shell
sudo systemctl stop initiad.service
sudo systemctl disable initiad.service
sudo rm /etc/systemd/system/initiad.service
rm -rf $HOME/.initia $HOME/initia
```

# WELL DONE! YOU JUST RAN YOUR VALIDATOR NODE ON INITIA TESTNET!
