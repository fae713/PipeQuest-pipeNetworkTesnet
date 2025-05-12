# Pipe Network Testnet on Local PC using Ubuntu 24.04 and VS Code - For Whitelisted DevNet Node Runners

This guide provides detailed instructions on how to set up and configure the POP Cache Node on Linux systems using the provided binaries.

* You must be whitelisted and receive email with instructions to qualify for node's rewards.
* If you are a new user, Signup [here](https://airtable.com/apph9N7T0WlrPqnyc/pagSLmmUFNFbnKVZh/form) and wait until you receive an email with invite code.

## System Requirements

* Linux (Ubuntu 24.04 Only, lower version of ubuntu is not supported) - Download from Microsoft Store
* Other Ubuntu version won't work because pop requires: /lib/x86_64-linux-gnu/libc.so.6: version `GLIBC_2.39'
* VS Code - Download from the official website with your system's version, e.g., x64 or x32
* 16GB RAM (configurable), more is better for higher rewards
* 4 cores minimum (recommended)
* SSD storage with 70GB+ available space
* Internet connectivity available 24/7 - Keep your PC active as long as you can in a day!

## Keep DevNet Node

Don't delete the DevNet Node, just ignore it.

## Node Setup

### 1. Install Software

* Start up your Ubuntu 24.04
* Set it up by creating a username and password. Ensure you can remember the password.
* After successfully setting up Ubuntu, start up VS Code.
* Once running, hit "Ctrl+J" to bring up the terminal. At the top right, click the dropdown menu near '+' and select Ubuntu 24.04.
* Now all our work will be done in the VS Code terminal that's connected to Ubuntu.
* Next, do

```
cd ~
```

You're set!

Dependencies

```
sudo apt update
```

```
sudo apt install -y libssl-dev ca-certificates
```

### 2. Download Pipe Binaries

1. Visit: [Download](https://download.pipe.network/) file (use invite code from email) and download the one for your system.
2. Once Downloaded, open the file path.
3. The file path should be like this: /mnt/c/Users/DELL/Downloads/pop-v0.3.0-linux-x64.tar.gz
4. Switch to a superuser

```
su -
```

5. Create directories, run the codes one by one

```
sudo mkdir -p /opt/popcache
sudo mkdir -p /opt/popcache/logs
```

6. Change directory into where we'll be working

```
cd /opt/popcache
```

7. Then do the following commands below in your Ubuntu one after the other.

Note: Copy the full code including the '.' for this command

```
mv /mnt/c/Users/DELL/Downloads/pop-v0.3.0-linux-x64.tar.gz .
```

```
sudo tar -xzf pop-v0.3.0-linux-*.tar.gz
```

```
chmod +x /opt/popcache/pop
```

8. Setup Config File

```
nano config.json
```

```json
{
  "pop_name": "your-pop-name",
  "pop_location": "Your Location, Country",
  "invite_code": "Enter your Invite Code",
  "server": {
    "host": "0.0.0.0",
    "port": 443,
    "http_port": 80,
    "workers": 0
  },
  "cache_config": {
    "memory_cache_size_mb": 4096,
    "disk_cache_path": "./cache",
    "disk_cache_size_gb": 100,
    "default_ttl_seconds": 86400,
    "respect_origin_headers": true,
    "max_cacheable_size_mb": 1024
  },
  "api_endpoints": {
    "base_url": "https://dataplane.pipenetwork.com"
  },
  "identity_config": {
    "node_name": "your-node-name",
    "name": "Your Name",
    "email": "your.email@example.com",
    "website": "https://your-website.com",
    "discord": "your_discord_username",
    "telegram": "your_telegram_handle",
    "solana_pubkey": "YOUR_SOLANA_WALLET_ADDRESS_FOR_REWARDS"
  }
}
```

When you're done filling the correct details, do this to save: Ctrl+O, hit enter, then Ctrl+X
Setup Configuration according to you

* `pop-location`: your location --> Command to Check --> `realpath --relative-to=/usr/share/zoneinfo /etc/localtime`
* `website`: Use Github or X

### 3. Create System Configuration

* Copy the below command whole and run in your terminal

```
sudo bash -c 'cat > /etc/sysctl.d/99-popcache.conf << EOL
net.ipv4.ip_local_port_range = 1024 65535
net.core.somaxconn = 65535
net.ipv4.tcp_low_latency = 1
net.ipv4.tcp_fastopen = 3
net.ipv4.tcp_slow_start_after_idle = 0
net.ipv4.tcp_window_scaling = 1
net.ipv4.tcp_wmem = 4096 65536 16777216
net.ipv4.tcp_rmem = 4096 87380 16777216
net.core.wmem_max = 16777216
net.core.rmem_max = 16777216
EOL'
sudo sysctl -p /etc/sysctl.d/99-popcache.conf
```

```
sudo bash -c 'cat > /etc/security/limits.d/popcache.conf << EOL
*    hard nofile 65535
*    soft nofile 65535
EOL'
```

4. Create systemd file

```
sudo bash -c 'cat > /etc/systemd/system/popcache.service << EOL
[Unit]
Description=POP Cache Node
After=network.target

[Service]
Type=simple
User=root
Group=root
WorkingDirectory=/opt/popcache
ExecStart=/opt/popcache/pop
Restart=always
RestartSec=5
LimitNOFILE=65535
StandardOutput=append:/opt/popcache/logs/stdout.log
StandardError=append:/opt/popcache/logs/stderr.log
Environment=POP_CONFIG_PATH=/opt/popcache/config.json

[Install]
WantedBy=multi-user.target
EOL'
```

```
sudo systemctl daemon-reload
```

```
sudo systemctl enable popcache
```

```
sudo systemctl start popcache
```

### 5. Check Health

**1. Check Status**

```
sudo systemctl status popcache
```

**2. Check Logs**

```
sudo journalctl -u popcache
```

### Optional: Restart or Stop Node

```
# Stop
sudo systemctl stop popcache

# Restart
sudo systemctl daemon-reload
sudo systemctl enable popcache
sudo systemctl restart popcache
```

---

Done

* Twitter - [https://x.com/faenfts](https://x.com/faenfts)