# raspberry-pi-wifi-setup
## A tutorial for setup of wifi-connection to IIIS-Net
---
## 1. Genearate hash password.

```
echo -n yourpassword | iconv -t UTF-16LE | openssl md4
```
It will show a string which is the hashed password(`HASH_PWD`) and were use in 2.2.
> the iconv might have different optional name for UTF-16LE hash, (ex: utf16le)
> ```
> echo -n yourpassword | iconv -t utf16le | openssl md4
> ```
> check `iconv -l` to find the correct name.

---
## 2. Modify the `/etc/wpa_supplicant/wpa_suplicant.conf`

```
cd /etc/wpa_supplicant
```
### 2.1 backup the original `wpa_supplicant.conf` if existed.

```
sudo cp wpa_supplicant.conf wpa_supplicant.conf.org
```

### 2.2 Edit new `wpa_supplicant.conf`

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
    identity= "ACCOUNT"
    password=hash:HASH_PWD
    phase1="peaplabel=0"
    phase2="auth=MSCHAPV2"
    priority=1
}
```

> `ACCOUNT` is your cisco account, ex: s2310146@s.tsukuba.ac.jp  
> `HASH_PWD` is the hashed password.
---


## 3. Modify the `/etc/dhcpcd.conf`

```
cd /etc
```
### 3.1 backup the original `dhcpcd.conf` if existed.

```
sudo cp dhcpcd.conf dhcpcd.conf.org
```

### 3.2 Add below content to `dhcpcd.conf` file.

```
interface IIIS-Net
env ifwireless=1
env wpa_supplicant_driver=wext,nl80211
```

---
## 4 Reboot

```{shell}
sudo reboot
```




