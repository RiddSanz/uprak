# Instalasi Proxmox VE dari Nol (Lengkap & Praktis)

Panduan ini ditujukan untuk **user yang sering lupa klik apa** 😄, jadi setiap tahap akan dijelaskan **apa yang harus dipilih / ditekan**.

Target akhir:

* Proxmox VE terinstal dengan benar
* Mode installer: **Terminal UI**
* Boot parameter: **nomodeset**
* Hostname: **pve2.smkbisa.net**
* DNS diarahkan ke **localhost**

---

## 1. Persiapan Awal

### Yang dibutuhkan

* ISO **Proxmox VE** (disarankan versi terbaru)
* Flashdisk bootable (Rufus / Ventoy / BalenaEtcher)
* Server / PC (bare metal)

> ⚠️ Semua data di disk akan **TERHAPUS**.

---

## 2. Boot dari Flashdisk Proxmox

1. Nyalakan server
2. Masuk **Boot Menu** (biasanya tekan `F12`, `F11`, `ESC`, atau `DEL`)
3. Pilih **USB Flashdisk**

Jika benar, akan muncul **menu Proxmox Installer**.

---

## 3. Memilih Mode Installer (Terminal UI)

Di layar awal Proxmox:

Pilihan yang muncul biasanya:

* Install Proxmox VE (Graphical)
* **Install Proxmox VE (Terminal UI)**
* Advanced Options

### Yang harus dipencet:

➡️ Gunakan **panah bawah (↓)**
➡️ Sorot: **Install Proxmox VE (Terminal UI)**
➡️ Tekan **ENTER**

> Mode Terminal UI lebih stabil untuk VGA lama / server lawas.

---

## 4. Menambahkan `nomodeset`

Jika layar sering **hitam / freeze / glitch**, gunakan `nomodeset`.

### Cara menambahkannya:

1. Saat menu installer **belum ENTER**
2. Tekan tombol:

   * `e`
3. Akan muncul baris boot parameter
4. Cari baris yang mengandung:

   ```
   quiet
   ```
5. Tambahkan di belakangnya:

   ```
   nomodeset
   ```

Contoh:

```
quiet nomodeset
```

6. Tekan:

* `CTRL + X` **atau** `F10` (tergantung layar)

Installer akan lanjut berjalan.

---

## 5. Accept License Agreement

Layar lisensi muncul.

### Yang harus dipencet:

➡️ Tekan **ENTER** pada tombol **<OK>**

---

## 6. Memilih Disk untuk Instalasi

Daftar disk akan tampil (contoh: `sda`, `nvme0n1`).

### Default (disarankan untuk pemula):

* Gunakan **Entire Disk**

### Yang harus dipencet:

➡️ Panah untuk memilih disk
➡️ Tekan **ENTER**
➡️ Pilih **<Next>** → ENTER

> ⚠️ Pastikan tidak salah pilih disk.

---

## 7. Pilih Lokasi, Timezone, Keyboard

Biasanya otomatis:

* Country: Indonesia
* Timezone: Asia/Jakarta
* Keyboard: US

### Yang harus dipencet:

➡️ Cek saja
➡️ Tekan **<Next>** → ENTER

---

## 8. Set Password & Email

Isi:

* **Password root** → ketik → ENTER
* **Confirm password** → ketik → ENTER
* **Email** → opsional (boleh kosong di lab)

➡️ Pilih **<Next>** → ENTER

---

## 9. Konfigurasi Network (PENTING)

Layar Network Configuration akan muncul.

### 9.1 Interface

Pilih interface yang terhubung ke Mikrotik (misal `eno1`, `eth0`).

➡️ Panah → pilih interface

---

### 9.2 Hostname

Isi hostname **WAJIB FQDN**.

Isi:

```
pve2.smkbisa.net
```

➡️ ENTER

---

### 9.3 IP Address

Contoh (sesuaikan jaringanmu):

```
IP Address : 192.168.x.x
Netmask    : 255.255.255.0
Gateway    : 192.168.x.1
```

➡️ Isi manual
➡️ ENTER tiap kolom

---

### 9.4 DNS Server (KE LOCALHOST)

Isi DNS:

```
127.0.0.1
```

> Proxmox akan menggunakan DNS internal (`pveproxy`).

➡️ ENTER

---

### 9.5 Ringkasan

Jika sudah benar:
➡️ Pilih **<Next>** → ENTER

---

## 10. Konfirmasi & Install

Ringkasan instalasi tampil.

### Yang harus dipencet:

➡️ Pilih **<Install>**
➡️ ENTER

⏳ Tunggu 5–10 menit.

---

## 11. Reboot Setelah Instalasi

Jika muncul pesan instalasi selesai:

➡️ Tekan **ENTER**
➡️ Cabut flashdisk
➡️ Server reboot otomatis

---

## 12. Login Pertama Proxmox

### 12.1 Akses via Browser

Dari client:

```
https://IP-PROXMOX:8006
```

Contoh:

```
https://192.168.x.x:8006
```

> Abaikan SSL warning → Advanced → Proceed

---

### 12.2 Login

* User: `root`
* Password: (yang dibuat tadi)
* Realm: `Linux PAM`

➡️ Klik **Login**

---

## 13. Validasi Hostname & DNS

Login ke shell Proxmox:

```
hostname
```

Harus tampil:

```
pve2.smkbisa.net
```

Cek DNS:

```
cat /etc/resolv.conf
```

Output minimal:

```
nameserver 127.0.0.1
```

---

## 14. Checklist Akhir ✅

* [x] Proxmox bisa boot normal
* [x] Web UI bisa diakses
* [x] Hostname FQDN benar
* [x] DNS ke localhost
* [x] Siap konfigurasi VM / CT / forwarding Mikrotik

---

Kalau mau, tahap berikutnya kita bisa lanjut ke:

* Konfigurasi **vmbr0** (bridge)
* Optimasi Proxmox untuk lab sekolah
* Integrasi full dengan Mikrotik NAT & firewall

Tinggal bilang lanjut ke mana 👌
