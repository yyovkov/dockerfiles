# Remove password from password protected key
```bash
$ openssl rsa -in Mikrotik_Buchurest.key -out NoPass.key
```

# Configuring routing on Mikrotik VPN client:
https://github.com/missinglink/mikrotik-openvpn-client
```bash
/ip firewall address-list add address=192.168.10.0/24 disabled=no list=vpn_traffic
/ip firewall mangle add disabled=no action=mark-routing chain=prerouting dst-address-list=vpn_traffic new-routing-mark=vpn_traffic passthrough=yes src-address=192.168.115.2-192.168.115.254
/ip route add disabled=no dst-address=0.0.0.0/0 type=unicast gateway=ovpn-head-office routing-mark=vpn_traffic scope=30 target-scope=10
/ip firewall nat add chain=srcnat src-address=192.168.115.0/24 out-interface=ovpn-head-office action=masquerade
```
