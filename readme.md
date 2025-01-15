# Langkah-langkah Konfigurasi Materi MTCRE

## Kofigurasi LB-ECMP
### Bagian Pertama: Konfigurasi IP Address
#### ISP-A
Konfigurasi DHCP CLient
**Note**: Sesuaikan dengan interface yang terpasang pada link.
```bash
ip dhcp-client pr
ip dhcp-client set numbers=0 interfaces=ether1
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
