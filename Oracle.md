### Install Oracle
```
cd $HOME
rm -rf slinky
git clone https://github.com/skip-mev/slinky.git
cd slinky
git checkout v0.4.3
```
```
make build
```
```
mv build/slinky /usr/local/bin/
```
### Create Oracle Service file
```
sudo tee /etc/systemd/system/slinkyd.service > /dev/null <<EOF
[Unit]
Description=Initia Slinky Oracle
After=network-online.target

[Service]
User=$USER
ExecStart=$(which slinky) --oracle-config-path $HOME/slinky/config/core/oracle.json --market-map-endpoint 127.0.0.1:15090
Restart=on-failure
RestartSec=30
LimitNOFILE=65535

[Install]
WantedBy=multi-user.target
EOF
```
### Start Oracle systemD
```
sudo systemctl daemon-reload
sudo systemctl enable slinkyd
sudo systemctl restart slinkyd
```
### Check Oracle Logs
```
journalctl -fu slinkyd --no-hostname
```
### Config app.toml
```
nano /root/.initia/config/app.toml
```
- Scroll down to the end of the file (Search for "oracle" by pressing CTRL+W), and replace the placeholders with the following codes as shown in the below images.
```
enabled = "true"
oracle_address = "127.0.0.1:8080"
client_timeout = "500ms"
```
![img](https://github.com/0xmoei/initia-validator/assets/90371338/a0d264ad-2f40-4ebc-bc9f-0920246a83b8)

![img](https://github.com/0xmoei/initia-validator/assets/90371338/d3e03408-011b-4580-9ddd-050c54bcfabd)

### Restart Initia SystemD
```
sudo systemctl daemon-reload
sudo systemctl restart initiad
sudo systemctl restart slinkyd
sudo journalctl -u initiad -f --no-hostname -o cat
```
