### Use sudo without typing a password

```bash
sudo visudo

# User privilege specification
username ALL = (ALL) NOPASSWD:ALL

cat /etc/sudoers
```
