# raspberry-pi-4b-wifi-setup
## A tutorial for setup of wifi-connection to IIIS-Net
---
## 1. Genearate hash password.

```
echo -n yourpassword | iconv -t UTF-16LE | openssl md4 -provider legacy
```
It will show a string which is the hashed password(`HASH_PWD`) and were use in 2.2.
> the iconv might have different optional name for UTF-16LE hash, (ex: utf16le)
> ```
> echo -n yourpassword | iconv -t utf16le | openssl md4 -provider legacy
> ```
> check `iconv -l` to find the correct name.

---
## 2. Modify the `/etc/wpa_supplicant/wpa_supplicant.conf`

```
cd /etc/wpa_supplicant
```
### 2.1 backup the original `wpa_supplicant.conf` if existed.

```
sudo cp wpa_supplicant.conf wpa_supplicant.conf.org
```

### 2.2 Edit new `wpa_supplicant.conf`

- Using nano to open the wpa_supplicant.conf
```
sudo nano wpa_supplicant.conf
```

- Editing the content as follow, replacing the `USERNAME` and `HASH_PWD`.
```
ctrl_interface=DIR=/var/run/wpa_supplicant GROUP=netdev
update_config=1
country=JP

network={
    ssid="IIIS-Net"
    proto=RSN
    key_mgmt=WPA-EAP
    pairwise=CCMP
    auth_alg=OPEN
    eap=PEAP
    identity="USERNAME"
    password=hash:HASH_PWD
    phase1="peaplabel=0"
    phase2="auth=MSCHAPV2"
    priority=1
}
```

> `USERNAME` is your cisco username, ex: s1234567@s.tsukuba.ac.jp  
> `HASH_PWD` is the hashed password string from step #1.
---


## 3. Modify the `/etc/dhcpcd.conf`

```
cd /etc
```
### 3.1 Backup the original `dhcpcd.conf` if existed.

```
sudo cp dhcpcd.conf dhcpcd.conf.org
```

### 3.2 Modify `dhcpcd.conf` file.

- Using nano to open the wpa_supplicant.conf
```
sudo nano dhcpcd.conf
```

- Add below content to `dhcpcd.conf` file

```
interface IIIS-Net
env ifwireless=1
env wpa_supplicant_driver=wext,nl80211
```

---
## 4 Reboot the system

```
sudo reboot
```




