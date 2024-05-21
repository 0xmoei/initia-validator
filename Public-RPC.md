```console
# Fetch your IP and RPC port (It will print your RPC url)
RPC="http://$(wget -qO- eth0.me)$(grep -A 3 "\[rpc\]" $HOME/.initia/config/config.toml | egrep -o ":[0-9]+")" && echo $RPC

# Check if it's responding
curl $RPC/status
```
❗ If you are getting Connection refused , you need to make it accessible to the Internet:

```console
# Edit config
sed -i '/\[rpc\]/,/\[/{s/^laddr = "tcp:\/\/127\.0\.0\.1:/laddr = "tcp:\/\/0.0.0.0:/}' $HOME/.initia/config/config.toml

# Restart your node
sudo systemctl restart initiad

# Ensure logs are good
sudo journalctl -u initiad -f -o cat --no-hostname

# Check if RPC is responding once again
curl $RPC/status

# To output your PUBLIC RPC URL again
echo $RPC
```
> P.S. You can also paste this URL into your browser and ensure it works :)
> Submit the form
https://forms.gle/HqLFePaka2NLmzY98

❗ If you are still getting Connection refused and you have ufw firewall you need to open the port

```console
# Fetch RPC port. Make sure output is not empty
PORT=$(grep -A 3 "\[rpc\]" $HOME/.initia/config/config.toml | egrep -o ":[0-9]+") && \
PORT=${PORT#:} && \
echo $PORT

# Add the rule
sudo ufw allow $PORT

# Reload ufw
sudo ufw reload
```
