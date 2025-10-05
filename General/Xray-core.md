# 🛰️ Xray Proxy Setup Guide for Ubuntu

This guide will help you install and configure **Xray-core** on Ubuntu to use a VLESS proxy (like `xhttp` type).
It works on Ubuntu 20.04, 22.04, and 24.04.

---

## ⚙️ Step 1 — Install Xray-core

Open your terminal and run the following commands:

```bash
sudo apt update
sudo apt install -y curl

# Download and install Xray-core (official method)
bash <(curl -L https://github.com/XTLS/Xray-install/raw/main/install-release.sh) install
```

✅ This will:

* Install the latest **Xray-core** binary at `/usr/local/bin/xray`
* Create the config folder `/usr/local/etc/xray/`
* Add a **systemd service** called `xray.service`

---

## 🧾 Step 2 — Add Your Configuration

Edit the default config file:

```bash
sudo nano /usr/local/etc/xray/config.json
```

Paste your configuration below (replace with your own values if needed):

```json
{
  "log": {
    "loglevel": "info"
  },
  "inbounds": [
    {
      "tag": "socks-in",
      "port": 10808,
      "listen": "127.0.0.1",
      "protocol": "socks",
      "settings": {
        "udp": true
      }
    },
    {
      "tag": "http-in",
      "port": 8080,
      "listen": "127.0.0.1",
      "protocol": "http",
      "settings": {}
    }
  ],
  // This is an example proxy:
  "outbounds": [
    {
      "tag": "proxy",
      "protocol": "vless",
      "settings": {
        "vnext": [
          {
            "address": "YOUR_IP_OR_DOMAIN_HERE",
            "port": 11111,
            "users": [
              {
                "id": "PUT_YOUR_ID_HERE",
                "encryption": "none"
              }
            ]
          }
        ]
      },
      "streamSettings": {
        "network": "xhttp",
        "xhttpSettings": {
          "path": "/",
          "mode": "auto"
        }
      }
    },
    {
      "protocol": "freedom",
      "tag": "direct",
      "settings": {}
    },
    {
      "protocol": "blackhole",
      "tag": "blocked",
      "settings": {}
    }
  ],
  "routing": {
    "domainStrategy": "AsIs",
    "rules": [
      {
        "type": "field",
        "ip": ["geoip:private"],
        "outboundTag": "direct"
      }
    ]
  }
}
```

Save the file:

* Press **Ctrl + O**, then **Enter**
* Press **Ctrl + X** to exit

---

## 🚀 Step 3 — Enable and Start Xray

Run these commands:

```bash
sudo systemctl daemon-reload
sudo systemctl enable xray
sudo systemctl start xray
```

Check if Xray is running correctly:

```bash
systemctl status xray
```

If you want to see detailed logs:

```bash
journalctl -u xray -e
```

---

## 🌐 Step 4 — Configure Ubuntu to Use the Proxy

Xray opens two local proxy ports:

* **SOCKS5 proxy:** `127.0.0.1:10808`
* **HTTP proxy:** `127.0.0.1:8080`

You can choose one of the following methods to make Ubuntu use them.

---

### 🧭 Option A — System-wide via GUI

1. Open **Settings → Network → Network Proxy**
2. Select **Manual**
3. Fill in these values:

   * HTTP Proxy → `127.0.0.1`, Port `8080`
   * HTTPS Proxy → `127.0.0.1`, Port `8080`
   * SOCKS Host → `127.0.0.1`, Port `10808`
4. Click **Apply system-wide**

Now all apps (including browsers) will use your proxy.

---

### 💻 Option B — Terminal Only (temporary)

For command-line tools like `curl`, `wget`, or `apt`, you can set temporary environment variables:

```bash
export http_proxy="http://127.0.0.1:8080"
export https_proxy="http://127.0.0.1:8080"
export all_proxy="socks5://127.0.0.1:10808"
```

To test:

```bash
curl -I https://ipinfo.io
```

You should see a different IP if the proxy is working.

To remove these settings, just close the terminal or run:

```bash
unset http_proxy https_proxy all_proxy
```

---

### 🔁 Option C — Make Proxy Permanent in Terminal

Add these lines at the end of your `~/.bashrc` (or `~/.zshrc`):

```bash
export http_proxy="http://127.0.0.1:8080"
export https_proxy="http://127.0.0.1:8080"
export all_proxy="socks5://127.0.0.1:10808"
```

Then apply it:

```bash
source ~/.bashrc
```

---

## ✅ Step 5 — Test Your Proxy

Open your browser and visit:
👉 [https://whatismyipaddress.com](https://whatismyipaddress.com)

If everything is correct, you should see your **proxy IP address**, not your real one.

---

## 🧩 Optional Commands

Restart Xray service:

```bash
sudo systemctl restart xray
```

Stop Xray:

```bash
sudo systemctl stop xray
```

Check logs:

```bash
journalctl -u xray -e
```

---

## 🎉 Done!

You now have a working Xray proxy setup on Ubuntu.
This setup works for **VLESS**, **xhttp**, and other Xray-supported protocols.
