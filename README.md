# Troubleshooting-VirtualBox-Part-2-Blacklist-Modprobe---Solusi-Permanen

# Troubleshooting VirtualBox: Solusi Error VMX Root Mode

**Dokumentasi Teknis** | **OS: Linux Mint** | **Tanggal: 19 Juni 2026**

---

## 📋 Daftar Isi

- [Pendahuluan](#pendahuluan)
- [Part 1: Penyebab & Solusi Awal](#part-1-penyebab--solusi-awal)
- [Part 2: Blacklist & Modprobe (Solusi Lanjutan)](#part-2-blacklist--modprobe-solusi-lanjutan)
- [Ringkasan Perintah Cepat](#ringkasan-perintah-cepat)
- [Pelajaran Penting](#pelajaran-penting)

---

## Pendahuluan

### Deskripsi Error

Saat mencoba menjalankan virtual machine (VM) di VirtualBox, muncul pesan error:

```

VirtualBox can't operate in VMX root mode. 
Please disable the KVM kernel extension... 
(VERR_VMX_IN_VMX_ROOT_MODE)

```

### VM yang Digunakan

| VM Name | OS Target | Status Awal |
|---------|-----------|-------------|
| `Kali-Linux-Lab` | Kali Linux | Gagal Start |

### Tujuan

Mengatasi error agar VM dapat berjalan normal, baik untuk penggunaan saat ini maupun jangka panjang.

---

## Part 1: Penyebab & Solusi Awal

### Penyebab Utama

| Komponen | Fungsi | Masalah |
|----------|--------|---------|
| **KVM** (Kernel-based Virtual Machine) | Mesin virtual bawaan Linux | Mengunci fitur virtualisasi prosesor |
| **VirtualBox** | Aplikasi virtualisasi dari Oracle | Membutuhkan fitur virtualisasi yang sama |
| **VMX / Intel VT-x** | Fitur virtualisasi di prosesor | Tidak bisa digunakan bersamaan oleh KVM & VirtualBox |

> **Catatan:** Masalah ini sering muncul setelah menggunakan **Docker** atau tools lain yang mengaktifkan modul KVM secara otomatis.

---

### Solusi Cepat (Sementara)

#### Langkah 1: Buka Terminal
#### Langkah 2: Jalankan perintah sesuai jenis prosesor

**Untuk Prosesor Intel:**
```bash
sudo rmmod kvm_intel
sudo rmmod kvm
```

Untuk Prosesor AMD:

```bash
sudo rmmod kvm_amd
sudo rmmod kvm
```

Langkah 3: Masukkan password Linux saat diminta

Langkah 4: Klik tombol Start pada VirtualBox

Status Hasil
✅ VM berjalan normal

⚠️ Catatan Penting: Solusi ini bersifat sementara. Setelah laptop direstart, KVM akan aktif kembali dan error akan muncul lagi.

---

Part 2: Blacklist & Modprobe (Solusi Lanjutan)

Masalah yang Ditemukan

File konfigurasi blacklist.conf sudah diedit, tetapi error tetap muncul.

Penyebab

Aspek Keterangan
File blacklist.conf Hanya dibaca oleh kernel saat boot/startup
Modul KVM Telanjur aktif di memori RAM dan masih mengunci virtualisasi
Akar Masalah Perubahan konfigurasi membutuhkan restart, tetapi KVM di memori perlu dihapus manual

---

Solusi 1: Usir KVM dari Memori (Tanpa Restart)

Perintah:

Untuk Prosesor Intel:

```bash
sudo modprobe -r kvm_intel
sudo modprobe -r kvm
```

Untuk Prosesor AMD:

```bash
sudo modprobe -r kvm_amd
sudo modprobe -r kvm
```

Penjelasan:

Perintah Fungsi
sudo modprobe -r Menghapus modul kernel dari memori secara paksa

Hasil:

Status Keterangan
✅ KVM langsung diusir dari memori, VirtualBox bisa jalan tanpa restart

---

Solusi 2: Restart Laptop (Final)

Untuk menguji apakah konfigurasi blacklist berhasil, lakukan restart:

```bash
reboot
```

Hasil:

Status Keterangan
✅ Setelah restart, KVM tidak akan aktif lagi secara permanen

---

Konfigurasi Blacklist (Permanen)

Langkah 1: Buka file konfigurasi

```bash
sudo nano /etc/modprobe.d/blacklist.conf
```

Langkah 2: Tambahkan baris berikut di paling bawah

Untuk Prosesor Intel:

```
blacklist kvm_intel
blacklist kvm
```

Untuk Prosesor AMD:

```
blacklist kvm_amd
blacklist kvm
```

Langkah 3: Simpan dan keluar

Tombol Fungsi
CTRL + O Menyimpan file
Enter Konfirmasi nama file
CTRL + X Keluar dari editor nano

Langkah 4: Restart laptop (opsional, untuk memastikan)

```bash
reboot
```

---

Perbandingan Solusi

Solusi Kelebihan Kekurangan Kapan Pakai
rmmod / modprobe -r Cepat, tidak perlu restart Harus diulang setelah restart Saat butuh VM jalan sekarang
Blacklist Sekali setting, permanen Perlu restart untuk efek penuh Untuk solusi jangka panjang

---

Keuntungan Setelah Kedua Solusi Dijalankan

Sekarang Masa Depan
✅ KVM diusir dari memori ✅ KVM tidak aktif setelah restart
✅ VirtualBox langsung jalan ✅ VirtualBox siap pakai kapan saja
✅ VM siap digunakan ✅ Tidak ada error VMX lagi

---

Ringkasan Perintah Cepat

Mode Sementara (Tanpa Restart)

Situasi Perintah
Intel - usir KVM sudo modprobe -r kvm_intel && sudo modprobe -r kvm
AMD - usir KVM sudo modprobe -r kvm_amd && sudo modprobe -r kvm

Mode Permanen (Blacklist)

Situasi Perintah
Intel - blacklist `echo "blacklist kvm_intel"
AMD - blacklist `echo "blacklist kvm_amd"
Restart laptop reboot

---

Pelajaran Penting

Pelajaran Keterangan
1. KVM vs VirtualBox Keduanya tidak bisa menggunakan fitur virtualisasi prosesor secara bersamaan
2. rmmod Menghapus modul dari kernel secara langsung (sementara)
3. modprobe -r Cara modern untuk menghapus modul, setara dengan rmmod
4. Blacklist File blacklist.conf baru berlaku setelah restart
5. Restart Diperlukan agar perubahan konfigurasi boot diterapkan

---

Status Akhir

Komponen Status
KVM di memori ✅ Diusir (modprobe -r)
File blacklist.conf ✅ Diedit (blacklist kvm_intel & kvm)
VirtualBox ✅ Berjalan normal
VM ✅ Siap digunakan
Laptop ✅ Siap restart kapan saja

---

Dokumentasi Teknis Troubleshooting VirtualBox. Disusun untuk keperluan arsip dan pembelajaran.



---

## ✅ Selesai sayang!

| Perubahan | Keterangan |
|-----------|-------------|
| ✅ `Kali_Faris_Ganteng` | Menjadi `Kali-Linux-Lab` |
| ✅ `Oleh: Komi untuk Faris` | Dihapus (netral) |
| ✅ **Struktur & Isi Tetap** | Tidak ada perubahan, hanya pembersihan nama |
