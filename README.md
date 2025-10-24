# Jarkom-Modul-2-2025-K-43
| Nama | NRP |
|------------|--------------|
| Mey Rosalina     | 5027241004    |
| Mochamad Fadhil Saifullah   | 5027231068       |

<img width="798" height="697" alt="image" src="https://github.com/user-attachments/assets/dd466ff4-ade6-48de-9c41-e64e757b0932" />

soal 1-3 

Konfigurasi Jaringan

Eonwe (Router)
```
auto eth0
iface eth0 inet dhcp

auto eth1
iface eth1 inet static
    address 10.85.1.1
    netmask 255.255.255.0

auto eth2
iface eth2 inet static
    address 10.85.2.1
    netmask 255.255.255.0

auto eth3
iface eth3 inet static
    address 10.85.3.1
    netmask 255.255.255.0

iptables -t nat -A POSTROUTING -o eth0 -j MASQUERADE -s 10.85.0.0/16
```

Earendil (Klien Barat)
```
auto eth0
iface eth0 inet static
	address 10.85.1.2
	netmask 255.255.255.0
	gateway 10.85.1.1
        dns-nameserver 192.168.122.1
```

Elwing (Klien Barat)
```
auto eth0
iface eth0 inet static
	address 10.85.1.3
	netmask 255.255.255.0
	gateway 10.85.1.1
        dns-nameserver 192.168.122.1
```

Cirdan (Klien Timur)
```
auto eth0
iface eth0 inet static
	address 10.85.2.2
	netmask 255.255.255.0
	gateway 10.85.2.1
        dns-nameserver 192.168.122.1
```

Elrond (Klien Timur)
```
auto eth0
iface eth0 inet static
	address 10.85.2.3
	netmask 255.255.255.0
	gateway 10.85.2.1
        dns-nameserver 192.168.122.1
```

Maglor (Klien Timur)
```
auto eth0
iface eth0 inet static
	address 10.85.2.3
	netmask 255.255.255.0
	gateway 10.85.2.1
        dns-nameserver 192.168.122.1
```

Sirion
```
auto eth0
iface eth0 inet static
	address 10.85.3.2
	netmask 255.255.255.0
	gateway 10.85.3.1
        dns-nameserver 192.168.122.1
```

Tirion (NS1)
```
auto eth0
iface eth0 inet static
	address 10.85.3.3
	netmask 255.255.255.0
	gateway 10.85.3.1
        dns-nameserver 192.168.122.1
```

Valmar (NS2)
```
auto eth0
iface eth0 inet static
	address 10.85.3.4
	netmask 255.255.255.0
	gateway 10.85.3.1
        dns-nameserver 192.168.122.1
```

Lindon (Web Server Statis)
```
auto eth0
iface eth0 inet static
	address 10.85.3.5
	netmask 255.255.255.0
	gateway 10.85.3.1
        dns-nameserver 192.168.122.1
```

Vingilot (Web Server Dinamis)
```
auto eth0
iface eth0 inet static
	address 10.85.3.6
	netmask 255.255.255.0
	gateway 10.85.3.1
        dns-nameserver 192.168.122.1
```

Konfigurasi GNS

4. 

SOA-nya `ns1.k43.com`  
NS-nya `ns1.k43.com` dan `ns2.k43.com`  
A record `ns1.k43.com` dan `ns2.k43.com` menunjuk ke DNS server masing-masing  
A record `k43.com` menunjuk ke **Sirion**

```bash
$TTL            3h

@               IN      SOA     n1.k43.com.     admin.k43.net. (
                        1       ; Serial
                        3h      ; Refresh
                        1h      ; Retry
                        1w      ; Expire
                        1h      ; Negative caching TTL
                        )

@               IN      NS      ns1.k43.com.
@               IN      NS      ns2.k43.com.

ns1             IN      A       192.168.122.1 ; Tirion
ns2             IN      A       192.168.122.1 ; Valmar
@               IN      A       192.168.122.1 ; Sirion

zone "k43.com" {
        type master;
        notify yes;
        allow-transfer { 192.168.122.1; }; // <---
        also-notify { 192.168.122.1; };    // <---
        file "/etc/bind/ns1.k43.com";
};

options {
        directory "/var/cache/bind";
        listen-on port 53 { localhost; 192.240.0.0/16; };
        allow-query { localhost; 192.240.0.0/16; };
        forwarders { 192.168.122.1; };  // <---
};

zone "k43.com" {
        type slave;
        masters { 192.168.122.1; }; // IP Tirion/master
        file "/var/cache/bind/ns2.k58.com";
};

nameserver 192.168.122.1  # ns1/master
nameserver 192.168.122.1  # ns2/slave
nameserver 192.168.122.1

