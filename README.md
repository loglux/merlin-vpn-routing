# merlin-vpn-routing

This script creates **VPN Policy Routing rules** for the **OpenVPN Client** in [AsusWRT Merlin firmware](https://github.com/RMerl/asuswrt-merlin.ng). It helps fetch IP addresses that belong to domain names and saves them into the router’s policy configuration. This allows selective VPN routing based on destination IPs.

> 🚨 **Important**: This version works only with **firmware 386.3+**. For older firmware, use [release 2.07](https://github.com/loglux/merlin-vpn-routing/releases/tag/2.07).

---

## 🔧 What It Does

- Reads domain names from `domains.txt`
- Resolves them into IP addresses (with optional subnet expansion)
- Adds static entries from `static.csv`
- Generates routing rules for the selected OpenVPN client
- Saves rules to file (not NVRAM)
- Optionally restarts the VPN client

---

## 📂 Files

- `vpol.py` — core logic
- `client1.py`, `client2.py`, ... — configurations for each VPN client
- `domains.txt` — list of domain names (one per line)
- `static.csv` — static entries in format: `Description,Source IP,Destination IP,Iface`

### Example: `domains.txt`
```
domain1.com
#example.com       # commented out
domain2.com
@subnetdomain.com  # use @ to resolve subnets (/xx)
```

### Example: `static.csv`
```
ADomain,0.0.0.0,194.200.22.87/26,VPN
```

---

## 🛠️ Requirements (on router)

- Entware
- Python 3
- `bind-tools` or `bind-dig`:
  ```sh
  opkg install bind-tools
  ```
- `whois` utility:
  ```sh
  opkg install whois
  ```
- `pip`:
  ```sh
  opkg install python3-pip
  ```
- Python module `pydig`:
  ```sh
  pip install pydig
  ```
- Optionally: `git` to clone the repository

---

## 🚀 Usage

1. Clone the repo:
   ```sh
   git clone https://github.com/loglux/merlin-vpn-routing.git
   cd merlin-vpn-routing
   ```

2. Edit your client config (`client1.py`, etc):

   - `local`: local IP or subnet (e.g. `192.168.0.10`, `192.168.0.0/24`, or `0.0.0.0`)
   - `name_length`: max description length (default: 10)
   - `client`: OpenVPN client number (1–5)
   - `conf_path`: optional path to `domains.txt` and `static.csv`

3. Run the script:
   ```sh
   python3 client1.py
   ```

---

## 📅 Auto-start with VPN

To run the script automatically on VPN (re)start:

1. Create or edit `/jffs/scripts/openvpn-event`:
   ```sh
   #!/bin/sh
   python3 /path_to_script/client1.py
   ```

2. Make it executable:
   ```sh
   chmod a+rx /jffs/scripts/openvpn-event
   ```

> ⚠️ Important: comment out `rules.client_restart(client)` to prevent restart loops.

---

## 🔙 Compatibility Note (Pre-386.3 Firmware)

Old versions of AsusWRT Merlin used NVRAM for storing rules, with strict space limits. This script no longer supports that mode. To support legacy systems, use [release 2.07](https://github.com/loglux/merlin-vpn-routing/releases/tag/2.07).

---

## 📄 References

- [Policy-based Routing — AsusWRT Wiki](https://github.com/RMerl/asuswrt-merlin.ng/wiki/Policy-based-routing)
- [User Scripts — AsusWRT Wiki](https://github.com/RMerl/asuswrt-merlin.ng/wiki/User-scripts)

---

## 🙋 Contributing

Pull requests are welcome! If you have improvements, ideas, or bug fixes, feel free to fork the repo and submit a PR.
