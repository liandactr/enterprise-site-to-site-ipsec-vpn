# enterprise-site-to-site-ipsec-vpn

## Deskripsi Project

Project ini merupakan simulasi jaringan dan keamanan menggunakan Cisco Packet Tracer dengan implementasi VPN Site-to-Site IPsec dan routing dinamis OSPF.

Topologi menghubungkan jaringan kantor pusat (Head Office/HQ) dengan dua kantor cabang, yaitu Branch 1 (BR1) dan Branch 2 (BR2), melalui dua router ISP. OSPF digunakan untuk mendistribusikan informasi routing antarrouter, sedangkan IPsec VPN digunakan untuk mengamankan komunikasi antara jaringan LAN kantor pusat dan kantor cabang.

Project ini dibuat untuk mempraktikkan konfigurasi routing dinamis, VPN IPsec, pengalamatan IP, verifikasi tabel routing, serta pengujian konektivitas antarjaringan.

## Tujuan Project

Tujuan dari project ini adalah:

- Merancang topologi jaringan multi-site menggunakan Cisco Packet Tracer.
- Mengonfigurasi routing dinamis OSPF pada Area 0.
- Mengimplementasikan VPN IPsec Site-to-Site antara HQ dan BR1.
- Mengimplementasikan VPN IPsec Site-to-Site antara HQ dan BR2.
- Memahami penggunaan ISAKMP/IKE dan IPsec transform set.
- Mengonfigurasi ACL untuk menentukan trafik yang dilindungi VPN.
- Memverifikasi adjacency OSPF dan tabel routing.
- Memeriksa status koneksi VPN menggunakan perintah verifikasi IPsec.
- Menguji konektivitas antar-PC menggunakan `ping`.
- Melakukan troubleshooting routing dan konektivitas jaringan.

## Teknologi yang Digunakan

| Teknologi | Fungsi |
|---|---|
| Cisco Packet Tracer | Simulasi dan pengujian jaringan |
| Cisco 2911 Router | Routing dan terminasi VPN |
| Cisco Catalyst 2960 | Menghubungkan perangkat dalam jaringan LAN |
| IPv4 | Pengalamatan jaringan |
| OSPF | Routing dinamis antarrouter |
| OSPF Area 0 | Area backbone untuk pertukaran informasi routing |
| IPsec VPN | Mengamankan komunikasi antarjaringan |
| ISAKMP/IKE | Negosiasi parameter keamanan VPN |
| AES | Enkripsi data VPN |
| ESP-SHA-HMAC | Integritas dan autentikasi paket IPsec |
| Crypto Map | Menghubungkan peer VPN dengan kebijakan IPsec |
| Access Control List (ACL) | Menentukan trafik yang dilindungi VPN |

## Topologi Jaringan
![Topologi Jaringan VPN Site-to-Site](topology.png)

Topologi terdiri dari lima router Cisco 2911, switch Cisco Catalyst 2960, serta PC pada jaringan kantor pusat dan kedua kantor cabang.

### Perangkat dan Perannya

- **R-HQ** — router kantor pusat yang menghubungkan LAN HQ dengan dua jalur ISP dan menjadi endpoint VPN.
- **R-ISP1** — router ISP yang menghubungkan HQ, BR1, dan ISP2.
- **R-ISP2** — router ISP yang menghubungkan HQ, BR2, dan ISP1.
- **R-BR1** — router kantor cabang pertama yang menjadi endpoint VPN dengan HQ.
- **R-BR2** — router kantor cabang kedua yang menjadi endpoint VPN dengan HQ.
- **Switch Cisco 2960** — menghubungkan PC dengan router melalui jaringan LAN.
- **PC HQ** — perangkat untuk menguji konektivitas dari kantor pusat.
- **PC BR1** — perangkat untuk menguji konektivitas kantor cabang pertama.
- **PC BR2** — perangkat untuk menguji konektivitas kantor cabang kedua.

### Alur Koneksi

- PC HQ → Switch HQ → R-HQ.
- R-HQ → R-ISP1 → R-BR1.
- R-HQ → R-ISP2 → R-BR2.
- R-ISP1 ↔ R-ISP2 melalui jaringan WAN.
- VPN IPsec HQ ↔ BR1.
- VPN IPsec HQ ↔ BR2.

OSPF digunakan untuk mempelajari rute jaringan melalui topologi WAN, sedangkan VPN IPsec melindungi trafik antara LAN HQ dan LAN masing-masing cabang.

## Skema Alamat IP

### Tabel Pengalamatan IP LAN

| Lokasi | Network Address | Gateway |
|---|---|---|
| HQ | `192.168.10.0/24` | `192.168.10.1` |
| BR1 | `192.168.20.0/24` | `192.168.20.1` |
| BR2 | `192.168.30.0/24` | `192.168.30.1` |

### Tabel Pengalamatan IP WAN

| Koneksi | Network Address | IP Perangkat |
|---|---|---|
| HQ – ISP1 | `10.0.12.0/30` | HQ: `10.0.12.1`, ISP1: `10.0.12.2` |
| HQ – ISP2 | `10.0.13.0/30` | HQ: `10.0.13.1`, ISP2: `10.0.13.2` |
| ISP1 – BR1 | `10.0.14.0/30` | ISP1: `10.0.14.1`, BR1: `10.0.14.2` |
| ISP2 – BR2 | `10.0.25.0/30` | ISP2: `10.0.25.1`, BR2: `10.0.25.2` |
| ISP1 – ISP2 | `10.0.35.0/30` | ISP1: `10.0.35.1`, ISP2: `10.0.35.2` |

### Tabel IP PC

| Perangkat | IP Address | Default Gateway |
|---|---|---|
| PC HQ | `192.168.10.10` | `192.168.10.1` |
| PC BR1 | `192.168.20.10` | `192.168.20.1` |
| PC BR2 | `192.168.30.10` | `192.168.30.1` |

### Pembagian Jaringan

| Network Address | Fungsi |
|---|---|
| `192.168.10.0/24` | Jaringan LAN kantor pusat |
| `192.168.20.0/24` | Jaringan LAN kantor cabang 1 |
| `192.168.30.0/24` | Jaringan LAN kantor cabang 2 |
| `10.0.12.0/30` | Link HQ–ISP1 |
| `10.0.13.0/30` | Link HQ–ISP2 |
| `10.0.14.0/30` | Link ISP1–BR1 |
| `10.0.25.0/30` | Link ISP2–BR2 |
| `10.0.35.0/30` | Link antar-ISP |

## Konfigurasi Routing OSPF

OSPF dikonfigurasi menggunakan Process ID 1 pada Area 0. Setiap router mengiklankan jaringan LAN atau WAN yang terhubung langsung sesuai topologi.

### Konfigurasi OSPF pada R-HQ

```cisco
router ospf 1
 network 192.168.10.0 0.0.0.255 area 0
 network 10.0.12.0 0.0.0.3 area 0
 network 10.0.13.0 0.0.0.3 area 0
```

### Jaringan yang Diiklankan

- **R-HQ:** `192.168.10.0/24`, `10.0.12.0/30`, `10.0.13.0/30`
- **R-ISP1:** `10.0.12.0/30`, `10.0.14.0/30`, `10.0.35.0/30`
- **R-ISP2:** `10.0.13.0/30`, `10.0.25.0/30`, `10.0.35.0/30`
- **R-BR1:** `192.168.20.0/24`, `10.0.14.0/30`
- **R-BR2:** `192.168.30.0/24`, `10.0.25.0/30`

### Verifikasi OSPF

Perintah yang digunakan:

```cisco
show ip ospf neighbor
show ip ospf database
show ip route
show ip protocols
```

Perintah tersebut digunakan untuk memeriksa adjacency OSPF, database link-state, jaringan yang dipelajari, dan konfigurasi protokol routing.

## Konfigurasi VPN IPsec Site-to-Site

VPN IPsec dikonfigurasi untuk menghubungkan kantor pusat dengan kedua kantor cabang.

### VPN HQ–BR1

Peer VPN:

- R-HQ: `10.0.12.1` sebagai interface WAN lokal dan peer remote `10.0.14.2`.
- R-BR1: `10.0.14.2` sebagai interface WAN lokal dan peer remote `10.0.12.1`.
- Jaringan lokal HQ: `192.168.10.0/24`.
- Jaringan lokal BR1: `192.168.20.0/24`.

### VPN HQ–BR2

Peer VPN:

- R-HQ menggunakan peer remote `10.0.25.2`.
- R-BR2 menggunakan peer remote `10.0.13.1`.
- Jaringan lokal HQ: `192.168.10.0/24`.
- Jaringan lokal BR2: `192.168.30.0/24`.

### Parameter Keamanan VPN

| Parameter | Konfigurasi |
|---|---|
| Autentikasi | Pre-shared key |
| Enkripsi ISAKMP | AES |
| Diffie-Hellman Group | 2 |
| IPsec encryption | ESP-AES |
| IPsec integrity | ESP-SHA-HMAC |
| Identifikasi trafik | Crypto ACL |
| Pengaitan kebijakan VPN | Crypto map |

### Verifikasi VPN

Perintah yang digunakan:

```cisco
show crypto isakmp sa
show crypto ipsec sa
```

`show crypto isakmp sa` digunakan untuk memeriksa status negosiasi IKE/ISAKMP, sedangkan `show crypto ipsec sa` digunakan untuk memeriksa parameter IPsec dan penghitung paket terenkripsi maupun terdekripsi.

## Kebijakan Keamanan Jaringan

Kebijakan VPN menentukan trafik LAN yang akan dilindungi IPsec.

| Sumber | Tujuan | Kebijakan |
|---|---|---|
| LAN HQ (`192.168.10.0/24`) | LAN BR1 (`192.168.20.0/24`) | Dilindungi VPN IPsec |
| LAN BR1 (`192.168.20.0/24`) | LAN HQ (`192.168.10.0/24`) | Dilindungi VPN IPsec |
| LAN HQ (`192.168.10.0/24`) | LAN BR2 (`192.168.30.0/24`) | Dilindungi VPN IPsec |
| LAN BR2 (`192.168.30.0/24`) | LAN HQ (`192.168.10.0/24`) | Dilindungi VPN IPsec |

ACL crypto digunakan untuk menentukan trafik yang cocok dengan kebijakan IPsec. Trafik lain tidak otomatis menjadi bagian dari VPN hanya karena OSPF memiliki rute menuju jaringan tersebut.

## Konfigurasi Access Control List (ACL)

ACL crypto pada R-HQ menentukan trafik LAN yang perlu dilindungi oleh masing-masing tunnel.

### ACL HQ–BR1

```cisco
access-list 110 permit ip 192.168.10.0 0.0.0.255 192.168.20.0 0.0.0.255
```

ACL tersebut mencocokkan trafik IP dari LAN HQ menuju LAN BR1.

### ACL HQ–BR2

```cisco
access-list 120 permit ip 192.168.10.0 0.0.0.255 192.168.30.0 0.0.0.255
```

ACL tersebut mencocokkan trafik IP dari LAN HQ menuju LAN BR2.

Pada sisi cabang, ACL crypto harus mencocokkan pasangan jaringan yang sama dengan arah sumber dan tujuan terbalik.

## Pengujian Konektivitas

Pengujian dilakukan menggunakan perintah `ping` dari PC kantor pusat ke PC masing-masing cabang.

### PC HQ ke PC BR1

Perintah:

```text
ping 192.168.20.10
```

Hasil pengujian:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

**Status: Berhasil.**

PC HQ berhasil menjangkau PC BR1 tanpa kehilangan paket.

### PC HQ ke PC BR2

Perintah:

```text
ping 192.168.30.10
```

Hasil pengujian:

```text
Packets: Sent = 4, Received = 4, Lost = 0 (0% loss)
```

**Status: Berhasil.**

PC HQ berhasil menjangkau PC BR2 tanpa kehilangan paket.

### Ringkasan Pengujian

| Sumber | Tujuan | Hasil |
|---|---|---|
| PC HQ (`192.168.10.10`) | PC BR1 (`192.168.20.10`) | Berhasil, 4/4 paket diterima |
| PC HQ (`192.168.10.10`) | PC BR2 (`192.168.30.10`) | Berhasil, 4/4 paket diterima |

Pengujian ini mengonfirmasi konektivitas dari kantor pusat menuju kedua kantor cabang. Pengujian komunikasi antarcabang secara langsung perlu dilakukan secara terpisah apabila ingin memastikan jalur tersebut juga berfungsi.

## Verifikasi Konfigurasi

### Tabel Routing R-HQ

```cisco
show ip route
```

Hasil pemeriksaan menunjukkan rute OSPF menuju kedua LAN cabang:

```text
O 192.168.20.0/24 [110/3] via 10.0.12.2
O 192.168.30.0/24 [110/3] via 10.0.13.2
```

Rute tersebut menunjukkan bahwa jaringan BR1 dipelajari melalui ISP1 dan jaringan BR2 dipelajari melalui ISP2.

### Verifikasi Neighbor OSPF

```cisco
show ip ospf neighbor
```

Digunakan untuk memeriksa status adjacency OSPF antarrouter.

### Verifikasi Database OSPF

```cisco
show ip ospf database router
```

Digunakan untuk memeriksa Router LSA yang memuat informasi jaringan dan hubungan antarrouter.

### Verifikasi Status VPN

```cisco
show crypto isakmp sa
show crypto ipsec sa
```

Digunakan untuk memeriksa status negosiasi VPN serta penghitung paket IPsec.

## Dokumentasi Pengujian

### 1. Topologi Jaringan

![Topologi Jaringan](topology.png)

### 2. Verifikasi Routing OSPF

![Verifikasi Routing OSPF](ospf-routing.png)

### 3. Status VPN IPsec

![Verifikasi VPN IPsec](ipsec-status.png)

### 4. Pengujian Konektivitas HQ ke BR1

![Pengujian Ping BR1](ping-br1.png)

### 5. Pengujian Konektivitas HQ ke BR2

![Pengujian Ping BR2](ping-br2.png)


## Troubleshooting

### Rute LAN BR1 Tidak Muncul di Tabel Routing

**Gejala:** Rute `192.168.20.0/24` tidak terlihat pada tabel routing R-HQ.

**Pemeriksaan:**

- Memeriksa `show ip route`.
- Memeriksa `show ip ospf database router`.
- Memastikan R-BR1 mengiklankan LAN `192.168.20.0/24`.
- Memastikan adjacency OSPF berjalan.

**Hasil:** R-HQ akhirnya menampilkan rute OSPF menuju `192.168.20.0/24` melalui `10.0.12.2` dengan metric 3.

### Verifikasi Routing BR2

**Gejala:** Perlu memastikan jaringan BR2 dapat dijangkau melalui jalur routing yang sesuai.

**Pemeriksaan:** Tabel routing R-HQ diperiksa untuk melihat rute `192.168.30.0/24`.

**Hasil:** Rute menuju BR2 tersedia melalui `10.0.13.2` dengan metric 3.

### Verifikasi Konektivitas VPN

**Pemeriksaan:**

- Memeriksa status ISAKMP/IKE.
- Memeriksa parameter IPsec.
- Memeriksa penghitung paket terenkripsi dan terdekripsi.
- Menguji konektivitas antar-PC.

**Hasil:** Ping dari PC HQ menuju PC BR1 dan PC BR2 berhasil dengan packet loss 0%.

## Ringkasan Keterampilan

Project ini mendemonstrasikan keterampilan dasar jaringan dan keamanan berikut:

- Perancangan topologi multi-site.
- Pengalamatan IPv4 dan subnetting.
- Konfigurasi routing dinamis OSPF.
- Pemahaman OSPF Area 0 dan Router LSA.
- Implementasi VPN IPsec Site-to-Site.
- Konfigurasi ISAKMP/IKE dan transform set IPsec.
- Penggunaan crypto map dan crypto ACL.
- Verifikasi tabel routing dan adjacency OSPF.
- Verifikasi status dan penghitung paket IPsec.
- Pengujian konektivitas menggunakan `ping`.
- Troubleshooting routing dan VPN.
- Dokumentasi hasil simulasi jaringan.

## Struktur File Project

```text
Cisco_Site_to_Site_VPN/
├── README.md
├── Cisco_Site_to_Site_VPN.pkt
├── topology.png
├── ospf-routing.png
├── ipsec-status.png
├── ping-br1.png
└── ping-br2.png
```

File `.pkt` berisi simulasi jaringan Cisco Packet Tracer. File PNG digunakan untuk mendokumentasikan topologi, routing OSPF, status VPN, dan hasil pengujian konektivitas.

## Status Project

**Status: Konfigurasi inti selesai dan pengujian konektivitas HQ ke kedua cabang berhasil.**

Fitur yang telah dikonfigurasi dan diuji:

- Routing OSPF pada Area 0.
- Pertukaran informasi routing antarrouter.
- Rute OSPF menuju LAN BR1 dan BR2.
- VPN IPsec antara HQ dan BR1.
- VPN IPsec antara HQ dan BR2.
- Pengujian ping dari PC HQ menuju PC BR1.
- Pengujian ping dari PC HQ menuju PC BR2.

## Kesimpulan

Project ini menunjukkan penerapan routing dinamis OSPF dan VPN IPsec Site-to-Site untuk menghubungkan kantor pusat dengan dua kantor cabang dalam simulasi Cisco Packet Tracer.

Hasil pengujian menunjukkan bahwa R-HQ telah mempelajari rute menuju kedua LAN cabang melalui OSPF. Pengujian ping dari PC HQ menuju PC BR1 dan PC BR2 berhasil dengan tingkat kehilangan paket sebesar 0%.

Dengan demikian, konfigurasi inti dan konektivitas HQ ke kedua cabang telah berhasil diuji. Simulasi ini digunakan sebagai dokumentasi pembelajaran jaringan komputer, routing dinamis, dan keamanan komunikasi menggunakan VPN IPsec.
