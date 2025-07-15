
eth0 : connected to public site
eth1 : connected to internal site

1.  net.ipv4.ip_forward=1

````
# sysctl -w net.ipv4.ip_forward=1
````

Create a file in /etc/sysctl.d/

````
# echo net.ipv4.ip_forward=1 > /etc/sysctl.d/myrouter.conf
````

2. Assign zones to NICs

````
# nmcli conn mod eth0 connection.zone external
# nmcli conn mod eth1 connection.zone internal

# nmcli show con show eth0 | grep tion.zone
connection.zone: external
# nmcli show con show eth1 | grep tion.zone
connection.zone: internal
````

3. Stop firewalld
````
# systemctl disable --now firewalld
````

4. Enable nftables 
````
# systemctl enable --now nftables
````

5. Confirm to be able to configure with command 'nft' 

````
# nft add table ip filter
# nft add chain ip filter FORWARD "{ type filter hook forward priority 0; policy accept; }"
# nft add rule ip filter FORWARD iifname "eth1" oifname "eth0" counter accept
# nft add rule ip filter FORWARD iifname "eth0" oifname "eth1" ct state related,established counter accept

# nft add table ip nat
# nft add chain ip nat POSTROUTING "{ type nat hook postrouting priority 100; policy accept; }"
# nft add rule ip nat POSTROUTING oifname "eth0" counter masquerade

# nft list ruleset 
````


6. Put a file ruleset-myrouter.nft  in /etc/nftables/

````
#!/usr/sbin/nft -f 
## to be located to /etc/nftables/
## and add include ".nft_filePATH" to /etc/sysconfig/nftables.conf (RHELs dist.) 
nft add table ip filter
nft add chain ip filter FORWARD { type filter hook forward priority 0; policy accept; }
nft add rule ip filter FORWARD iifname "eth1" oifname "eth0" counter accept
nft add rule ip filter FORWARD iifname "eth0" oifname "eth1" ct state related,established counter accept

nft add table ip nat
nft add chain ip nat POSTROUTING { type nat hook postrouting priority 100; policy accept; }
nft add rule ip nat POSTROUTING oifname "eth0" counter masquerade
````

7. Edit /etc/sysconfig/nftables.conf

add a line to /etc/sysconfig/nftables.conf (RHELs dist.) :
include ".nft_filePATH" 

8. Restart nftables
````
# systemctl restart nftables
````
9. Confirm ruleset again 
````
# nft list ruleset 
````
==================

firewalld ONLY (MORE simple)

1.  net.ipv4.ip_forward=1

````
# sysctl -w net.ipv4.ip_forward=1
````

Create a file in /etc/sysctl.d/

````
# echo net.ipv4.ip_forward=1 > /etc/sysctl.d/myrouter.conf
````

2. Enable firewalld

````
# systemctl enable --now firewalld
````

3. Assign zones to NICs

````
# nmcli conn mod eth0 connection.zone external
# nmcli conn mod eth1 connection.zone trusted

# nmcli show con show eth0 | grep tion.zone
connection.zone: external
# nmcli show con show eth1 | grep tion.zone
connection.zone: trusted
````

====

Configuration for dnsmasq 
 
!!! dnsmasq host as router and dhcp server 192.168.24.1 on eth1

/etc/dnsmasq.conf

````
interface=eth1
dhcp-range=192.168.24.190,192.168.24.210,255.255.255.0,12h
dhcp-option=option:router,192.168.24.1
````
