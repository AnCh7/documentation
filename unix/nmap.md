### NMAP

```bash
# quick scan of machines in the network
sudo nmap -sn 192.168.4.0/24

# scan the ports on each device and tells which ones are open
sudo nmap 192.168.4.0/24

# aggressive device scan
sudo nmap -A -T4 192.168.4.11

# https://www.wireshark.org/tools/oui-lookup.html
```

