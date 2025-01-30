# Pipe Network Devnet-2 Node Setup Guide
Pipe Network DevNet 2 will serve as a proving ground for the testnet. After 40 days of stability, it will transition to the testnet

By running a Node, Users will earn Reputation and Scores during this phase to prove their contributions to the network.
* You must be whitelisted and receive email with instructions to qualify for node's rewards.
* If you are a new user, Signup [here](https://docs.google.com/forms/d/e/1FAIpQLScbxN1qlstpbyU55K5I1UPufzfwshcv7uRJG6aLZQDk52ma0w/viewform) and wait until you receive an email.

## System Requirements
* Linux
* Minimum 4GB RAM (configurable), more the better for higher rewards
* At least 100GB free disk space (configurable). 200-500GB is a sweet spot
* Internet connectivity available 24/7

#

## Stop Old Node
if you have running a node previously, you need to do these steps first.

### 1. Backup `.permissionless`
`.permissionless` is created in your `root` directory containing your node keys. we need it to install new node

### 2. Stop old node
```bash
sudo systemctl stop dcdnd.service
sudo systemctl disable dcdnd.service
sudo rm /etc/systemd/system/dcdnd.service
sudo systemctl daemon-reload
```

#

## Node Setup
### 1. Create directories
```
mkdir -p /root/pipe
mkdir -p /root/pipe/download_cache/
```
```
cd /root/pipe
```
### 2. Download Pipe binaries
* Replace `LINK` with **Download PoP Node** url received in your email
```
wget -O pop "LINK"
```

![image](https://github.com/user-attachments/assets/16c72001-41af-4393-a41d-ef893c3d4c1e)


### 3. Make pop executable
```
chmod +x pop
```

### 4. Create systemd file
We create a systemd file to run the node by entering the following command.
* `--ram 4`: Replace `4` with your favorite Ram you want to allocate. (e.g., `4` for 8GB).
* `--max-disk 200`: Replace `200` with the hard disk you want to allocate. (e.g., `200` for 200GB).
* `--pubKey SOLADDRESS`: Replace `SOLADDRESS` with your Solana Public Address. (You can use your old node generated Sol address)
* Refer to System Requirements step for choosing better specifications
```
sudo tee /etc/systemd/system/pipe.service > /dev/null << EOF
[Unit]
Description=Pipe Node Service
After=network.target
Wants=network-online.target

[Service]
User=root
Group=root
WorkingDirectory=/root/pipenetwork
ExecStart=/root/pipenetwork/pop \
    --ram 4 \
    --max-disk 200 \
    --cache-dir /root/pipenetwork/download_cache \
    --pubKey SOLADDRESS \
    --signup-by-referral-route 24e2c57a307e69ba
Restart=always
RestartSec=5
LimitNOFILE=65536
LimitNPROC=4096
StandardOutput=journal
StandardError=journal
SyslogIdentifier=dcdn-node

[Install]
WantedBy=multi-user.target
EOF
```

### 5. Start systemd
```
sudo systemctl daemon-reload
sudo systemctl enable pipe
sudo systemctl start pipe
```

### 6. Check health
**1. Check status**
```
sudo systemctl status pipe
```
**2. Check logs**
```
journalctl -u pipe -f
```
No problem if you get the following error in the picture, it will be fine after a few hours

![image](https://github.com/user-attachments/assets/699c3095-2833-4ee7-bdfb-a7c4134cc05e)

Final logs for a healthy node should be like the picture below:

![image](https://github.com/user-attachments/assets/ad117b92-4755-4bb1-a957-c3c0d7313cd3)


### 7. Check Node's Reputation and Scores
```
cd $HOME && cd pipe
./pop --status
```
You'll get following details representing your node's reputation
* Uptime Score (40%): Based on node's uptime over past 7 days
* Egress Score (30%): Based on data served in past 24h
* Historical Score (30%): Based on consistent reporting

### 8. Backup Files
Recommened to backup `node_info.json` in `/root/pipe`. It is linked to the IP address that registered the PoP node. It is no recoverable if lost. 
* `node_info.json`: Node configuration
* `download_cache`: Cached content
* You will not be able to create a new node and new `node_info.json` with the same IP

### 9. Generate Referral Code
You can share your referral to other people to signup with it when running the PoP Node and earn bonus points.
```
cd $HOME && cd pipe
./pop --gen-referral-route
```
