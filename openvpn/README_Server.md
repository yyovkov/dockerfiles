# TODO
* Script to Generate PKI

# Run docker container on host
```bash
$ docker run -dti --name dev_vpn -p 1194:1194/udp --cap-add=NET_ADMIN alpine sh
```

## Connect to docker container
```bash
$ docker exec -ti dev_vpn sh
```

## Create tun device
```bash
$ mkdir /dev/net
$ mknod /dev/net/tun c 10 200
$ chmod 600 /dev/net/tun
$ cat /dev/net/tun
```

## Install OpenVPN
```bash
$ apk add --no-cache openvpn easy-rsa
$ mkdir /var/log/openvpn
```

## Generate SSL Certificates
```bash
$ cp -r /usr/share/easy-rsa ~
$ cd ~/easy-rsa
$ cp vars.example vars
$ ./easyrsa init-pki
$ ./easyrsa build-ca
$ ./easyrsa gen-dh
$ ./easyrsa build-server-full root.hubata-seals.com
```

## Install SSL certificates
```bash
$ mkdir /etc/openvpn/server
$ cp pki/ca.crt /etc/openvpn/server/ca.crt
$ cp pki/dh.pem /etc/openvpn/server
$ cp pki/issued/root.hubata-seals.com.crt /etc/openvpn/server/server.crt
$ cp pki/private/root.hubata-seals.com.key /etc/openvpn/server/server.key
$ chmod 400 /etc/openvpn/server/server.key
```

## Create configuration file
```bash
tee > /etc/openvpn/server.conf << 'EOF'
port 1194
proto udp
dev tun

ca /etc/openvpn/server/ca.crt
cert /etc/openvpn/server/server.crt
key /etc/openvpn/server/server.key
dh /etc/openvpn/server/dh.pem

server 10.8.0.0 255.255.255.0
ifconfig-pool-persist /etc/openvpn/server/ipp.txt
push "route 192.168.10.0 255.255.255.0"
# client-to-client

user nobody
group nogroup

push "dhcp-option DNS 192.168.10.3"
push "dhcp-option DOMAIN head-office.hubata-seals.com"
push "comp-lzo yes"

keepalive 10 120
comp-lzo
#max-clients 5
max-clients 10
user nobody
group nobody
persist-key
persist-tun
status /var/log/openvpn/server.log
log /var/log/openvpn/server.log
#verb 1
verb 3
mute 20
# explicit-exit-notify 1
EOF
```

## Start OpenVPN
```bash
$ cd /etc/openvpn
$ openvpn --config server.conf
```

## Firewall
```bash
$ export OVPN_NATDEVICE=eth0
$ export OVPN_SERVER="10.9.0.0/24"

$ iptables -t nat -A POSTROUTING -s $OVPN_SERVER -o $OVPN_NATDEVICE -j MASQUERADE
$ iptables -t nat -A POSTROUTING -s "$DESTNET" -o $OVPN_NATDEVICE -j MASQUERADE
```

## Enable CRL support
```bash
$ openvpn --crl-verify $OPENVPN/crl.pem
```
