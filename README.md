# 📘 Konfigurasi Port Forwarding Internal MikroTik

Dokumen ini menjelaskan **alur konfigurasi Port Forwarding (dst-nat) pada MikroTik** dalam **jaringan internal**, tanpa menggunakan IP public.

Topologi yang digunakan:

```
Server Sekolah
      |
   eth1 (uplink)
   MikroTik
   |        |
eth2      eth3
Proxmox   Client
```

---

## 🧭 Peran Interface

| Interface | Fungsi                             |
| --------- | ---------------------------------- |
| eth1      | Koneksi ke server sekolah (uplink) |
| eth2      | Jaringan Proxmox / server          |
| eth3      | Jaringan client                    |

---

## 1️⃣ IP → Addresses

### 📍 IP Router untuk Proxmox

```
Address   : 192.168.21.1/29
Interface : eth2
```

Berfungsi sebagai **gateway** untuk:

* Proxmox host
* VM / Container

---

### 📍 IP Router untuk Client

```
Address   : 192.168.100.1/24
Interface : eth3
```

Berfungsi sebagai **gateway** untuk seluruh client.

---

## 2️⃣ IP → Routes

Pastikan terdapat **default route** ke server sekolah:

```
Dst. Address : 0.0.0.0/0
Gateway      : IP-server-sekolah
```

Artinya semua trafik yang tidak dikenal akan diteruskan ke server sekolah.

---

## 3️⃣ IP → DNS

```
Servers : DNS sekolah / DNS internal
Allow Remote Requests : yes
```

Digunakan agar:

* Client dapat melakukan resolve domain
* Server dapat melakukan update internal

---

## 4️⃣ Firewall → NAT (dstnat / Port Forwarding)

Digunakan untuk mengalihkan trafik dari jaringan atas ke server Proxmox.

### 📍 IP → Firewall → NAT → Add

#### 🔹 Tab General

```
Chain        : dstnat
Protocol     : tcp
In Interface : eth1
Dst Port     : 9090
```

#### 🔹 Tab Action

```
Action       : dst-nat
To Addresses : 192.168.21.2
To Ports     : 80
```

Makna konfigurasi:

* Akses ke MikroTik pada port `9090`
* Akan dialihkan ke Proxmox/VM port `80`

---

## 5️⃣ Firewall → NAT (srcnat / Masquerade)

Digunakan agar jalur balasan paket kembali dengan benar.

### 📍 IP → Firewall → NAT → Add

#### 🔹 General

```
Chain         : srcnat
Out Interface : eth1
```

#### 🔹 Action

```
Action : masquerade
```

---

## 6️⃣ Firewall → Filter Rules

### 🔹 Allow Forward ke Proxmox

Digunakan agar trafik tidak terblok oleh firewall.

#### 📍 IP → Firewall → Filter → Add

**General**

```
Chain        : forward
Protocol     : tcp
Dst Port     : 80
In Interface : eth1
Out Interface: eth2
```

**Action**

```
Action : accept
```

⚠️ Letakkan rule ini **di atas rule drop**.

---

## 7️⃣ Firewall → Connections (Verifikasi)

Saat konfigurasi berhasil dan ada koneksi masuk, akan terlihat:

```
tcp  dst-nat  9090 → 192.168.21.2:80
```

Ini menandakan **port forwarding internal berjalan dengan benar**.

---

## 8️⃣ Konfigurasi Jaringan Proxmox / VM

Contoh konfigurasi:

```
IP Address : 192.168.21.2
Netmask    : 255.255.255.248
Gateway    : 192.168.21.1
DNS        : DNS sekolah
```

Pastikan:

* Service berjalan di port `80`
* Firewall OS tidak memblok koneksi

---

## 9️⃣ Alur Paket (Ringkas)

1. Request datang dari server sekolah ke MikroTik port `9090`
2. Paket masuk melalui `eth1`
3. Rule `dstnat` mengalihkan ke `192.168.21.2:80`
4. Proxmox membalas ke MikroTik
5. MikroTik meneruskan kembali ke server sekolah

---

## 🔁 Ringkasan Jalur Konfigurasi

```
IP → Addresses
IP → Routes
IP → DNS
IP → Firewall → NAT (dstnat)
IP → Firewall → NAT (srcnat)
IP → Firewall → Filter Rules
IP → Firewall → Connections
```

---

## 🧠 Catatan Penting

* Konfigurasi ini **tidak menggunakan IP public**
* NAT dapat berjalan sepenuhnya di jaringan internal
* MikroTik bertindak sebagai **pengarah trafik**, bukan tujuan akhir

---

📌 Dokumen ini cocok digunakan sebagai:

* README GitHub
* Laporan praktikum jaringan
* Dokumentasi internal lab sekolah

---

✍️ Author: **Raden Mas Parid**
