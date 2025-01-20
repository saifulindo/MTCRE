# Langkah-langkah Konfigurasi Materi MTCRE

## Konfigurasi LB-ECMP
### TOPOLOGI LB ECMP
![Topologi](https://github.com/saifulindo/MTCRE/raw/master/topologi-lb.jpg)
### Bagian Pertama: Konfigurasi IP Address
#### ISP-A
Konfigurasi DHCP Client
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
Konfigurasi DHCP Client
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
Uji Konektivitas
```bash
ping 10.10.10.1
ping 11.11.11.1
```

#### PC1
Konfigurasi IP Address dan DNS
```bash
ip 192.168.XX.2/24 192.168.XX.1
ip dns 10.10.10.1 11.11.11.1
```
Uji Konektivitas
```bash
ping 192.168.XX.1
```

### Bagian Kedua: Konfigurasi NAT dan Allow Remote Request
#### ISP-A
Konfigurasi NAT
**Note**: `out-interface` disesuaikan dengan link yang terpasang
```bash
ip firewall nat add chain=srcnat out-interface=ether1 action=masquerade
ip firewall nat print
```
Konfigurasi Allow Remote Request
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
Konfigurasi Allow Remote Request
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
Konfigurasi Load Balancing ECMP
```bash
ip route add dst-address=0.0.0.0/0 gateway=10.10.10.1,11.11.11.1
ip route pr
```
Konfigurasi DNS Router Kantor
```bash
ip dns set servers=10.10.10.1,11.11.11.1
ip dns pr
```

### Bagian Keempat: Verifikasi Client
#### PC1
Uji Konektivitas 
```bash
ping google.com
trace google.com
```
---

## Konfigurasi Fail Over
### Bagian Pertama: Disable Konfigurasi LB ECMP
#### R-Kantor
Lakukan disable LB ECMP pada Router Kantor
```bash
ip route disable number=0
ip route pr
Flags: X - disabled, A - active, D - dynamic, C - connect, S - static, r - rip, b - bgp, o - ospf, m - mme,
B - blackhole, U - unreachable, P - prohibit
 #      DST-ADDRESS        PREF-SRC        GATEWAY            DISTANCE
 0 X S  0.0.0.0/0                          10.10.10.1                1
                                           11.11.11.1
 1 ADC  10.10.10.0/30      10.10.10.2      ether1                    0
 2 ADC  11.11.11.0/30      11.11.11.2      ether2                    0
 3 ADC  192.168.10.0/24    192.168.10.1    ether3                    0
```
Tambahkan routing ke big-zero secara terpisah
```bash
ip route add dst-address=0.0.0.0/0 gateway=10.10.10.1
ip route add dst-address=0.0.0.0/0 gateway=11.11.11.1
ip route pr
Flags: X - disabled, A - active, D - dynamic, C - connect, S - static, r - rip, b - bgp, o - ospf, m - mme,
B - blackhole, U - unreachable, P - prohibit
 #      DST-ADDRESS        PREF-SRC        GATEWAY            DISTANCE
 0 X S  0.0.0.0/0                          10.10.10.1                1
                                           11.11.11.1
 1 A S  0.0.0.0/0                          11.11.11.1                1
 2   S  0.0.0.0/0                          10.10.10.1                1
 3 ADC  10.10.10.0/30      10.10.10.2      ether1                    0
 4 ADC  11.11.11.0/30      11.11.11.2      ether2                    0
 5 ADC  192.168.10.0/24    192.168.10.1    ether3                    0
```
Atur distance yang menuju ke ISP-B menjadi 2
```bash
ip route set number=2 distance=2
ip route pr
```
### Bagian Kedua: Pengujian File Over
#### R-Kantor
```bash
interface disable number=0
ip route pr
```

[def]: ../topologi-lb.jpg