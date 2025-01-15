# Langkah-langkah Konfigurasi Materi MTCRE

## Kofigurasi LB-ECMP
### TOPOLOGI LB ECMP
![Topologi](../MTCRE/topologi-lb.jpg)
### Bagian Pertama: Konfigurasi IP Address
#### ISP-A
Konfigurasi DHCP CLient
**Note**: Sesuaikan dengan interface yang terpasang pada link.
```bash
ip dhcp-client pr
ip dhcp-client set numbers=0 interfaces=ether1
```
Verifikasi
```bash
ip dhcp-client pr
ip address pr
ip dns pr
ip route pr
ping google.com
```
Konfigurasi IP Address
```bash
ip address pr
ip address add address=10.10.10.1/30
```

#### ISP-B
Konfigurasi DHCP CLient
**Note**: Sesuaikan dengan interface yang terpasang pada link.
```bash
ip dhcp-client pr
ip dhcp-client set numbers=0 interfaces=ether1
```
Verifikasi
```bash
ip dhcp-client pr
ip address pr
ip dns pr
ip route pr
ping google.com
```
Konfigurasi IP Address
```bash
ip address pr
ip address add address=11.11.11.1/30
```

#### Router Kantor
Konfigurasi IP Address
**Note**: XX adalah Nomor Absen
```bash
ip address pr
ip address add address=10.10.10.2/30
ip address add address=11.11.11.2/30
ip address add address=192.168.XX.1/24
```
Uji Konektifitas
```bash
ping 10.10.10.1
ping 11.11.11.1
```

#### PC1
Konfigurasi IP Address dan DNS
```bash
ip 192.168.XX.1/24 192.168.XX.1
ip dns 10.10.10.1 11.11.11.1
```
Uji Konektifitas
```bash
ping 192.168.XX.1
```

### Bagian Kedua: Konfiruasi NAT dan Allow Remote Request
#### ISP-A
Konfigurasi NAT
**Note**: `out-interface` disesuaikan dengan link yang terpasang
```bash
ip firewall nat add chain=srcnat out-interface=ether1 action=masquerade
ip firewall nat print
```
Konfigurasi Allow remote Request
```bash
ip dns set allow-remote-requests=yes
ip dns pr
```
#### ISP-B
Konfigurasi NAT
**Note**: `out-interface` disesuaikan dengan link yang terpasang
```bash
ip firewall nat add chain=srcnat out-interface=ether1 action=masquerade
ip firewall nat print
```
Konfigurasi Allow remote Request
```bash
ip dns set allow-remote-requests=yes
ip dns pr
```
#### Router Kantor
**Note**: `out-interface` disesuaikan dengan link yang terpasang
```bash
ip firewall nat add chain=srcnat out-interface=ether1 action=masquerade
ip firewall nat add chain=srcnat out-interface=ether2 action=masquerade
ip firewall nat print
```

### Bagian Ketiga: Konfigurasi LB ECMP dan DNS
#### Router Kantor
KOnfigurasi Load Balancing ECMP
```bash
ip route add dst-address=0.0.0.0/0 gateway=10.10.10.1,11.11.11.1
ip route pr
```
KOnfigurasi DNS Router Kantor
```bash
ip dns set servers=10.10.10.1,11.11.11.1
ip dns pr
```

### Bagian Keempat: Verifikasi CLient
#### PC1
Uji Konektifitas 
```bash
ping google.com
trace google.com
```

[def]: ../topologi-lb.jpg