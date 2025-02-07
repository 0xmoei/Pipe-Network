## Update your PoP Node to 0.2.2 Version

```bash
sudo systemctl stop pipe && cd $HOME/pipe && wget -O pop "https://dl.pipecdn.app/v0.2.3/pop" && chmod +x pop && sudo systemctl daemon-reload && sudo systemctl restart pipe && journalctl -u pipe -f
```

## Check your Node status:
https://dashboard.pipenetwork.com/
