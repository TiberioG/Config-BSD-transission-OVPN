# Config-BSD-transission-OVPN
Collection of my configs for a working Freenas jail with transmission and OpenVPN

## Create Freenas Transmission Plugin

* Use GUI to create, use VNET and set static IP, remember to select `allow_tun = 1,`
* Full configuration of the iocage jail is in [config.json](/config.json)
* Add storage, jail path `/media` , Freenas path `/mnt/disk/Downloads`

## Install OpenVPN in jail

```
jls
jexec <Transmission_jail_id>
pkg update
pkg upgrade
pkg install bash openvpn wget nano
```

### Edit /etc/rc.conf

```
transmission_download_dir="/media"
openvpn_enable="YES"
openvpn_configfile="/usr/local/etc/openvpn/openvpn.conf"
firewall_enable="YES"
firewall_script="/usr/local/etc/ipfw.rules"
```

### Get OpenVPN config
* I'm using NordVPN so connect to https://nordvpn.com/servers/tools/ and copy the link of the UDP configuration
* create file for username and password

```
mkdir /usr/local/etc/openvpn
cd /usr/local/etc/openvpn
wget <config_link_from_provider>
cp <name_downloaded_conf> openvpn.conf
touch nordvpnauth.txt
vi nordvpnauth.txt
```


* edit /usr/local/etc/openvpn/[openvpn.conf](/usr/local/etc/openvpn/openvpn.conf)

```
-dev tun
+dev tun2

+redirect-gateway

-auth-user-pass
+auth-user-pass /usr/local/etc/openvpn/nordvpnauth.txt
```

* fix problem with tunnel interface, add the following line in /usr/local/etc/rc.d/[openvpn](/usr/local/etc/rc.d/openvpn) in order to force manual creation of tun interface at openvpn service start

```
ifconfig tun2 create
```

## Useful ip getter script /usr/local/sbin/[myip](/usr/local/sbin/myip)
```
cd /usr/local/sbin
touch myip
echo "#/bin/csh" >>myip
echo "wget http://ipinfo.io/IP -qO -" >>myip
chmod +x myip
```
