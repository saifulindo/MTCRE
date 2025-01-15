## langkah-langkah Konfigurasi Materi MTCRE

### Kofigurasi LB-ECMP
#### ISP-A
```terminal
ip dhcp-client pr
ip dhcp-client set numbers=0 interfaces=ether1
ip address add address=10.10.10.2/30
```