<h1 align="center"> How to edit validator profile picture </h1>

```console
# you can edit your validator by replacing placeholders

initiad tx mstaking edit-validator \
--moniker "YOUR_MONIKER_NAME" \
--identity "YOUR_KEYBASE_ID" \
--details "YOUR_DETAILS" \
--website "YOUR_WEBSITE_URL" \
--chain-id initiation-1 \
--commission-rate 0.05 \
--from $WALLET \
--gas-adjustment 1.4 \
--gas auto \
--gas-prices 0.15uinit \
-y
```
- to change your validator profile picture you have to create a KEYBASE_ID with instructions in this link : https://medium.com/@AVIAONE/how-to-display-logo-and-moniker-on-blockchain-explorer-with-keybase-aviaone-1a89c4e9c11
- you can leave the moniker unchanged
