<p align="center">
<img src="https://github.com/user-attachments/assets/17c844bd-9ad8-480c-8732-84460bb0a31e" width="500" />
<h1 align="center">WiFi Security & Router Diagnostics v2.0.0</h1>
<h3 align="center"> 
 Versi Terbaru dari Perangkat Lunak Pembobol Kata Sandi WiFi </h3>
   
</p>

WiFi Security & Router Diagnostics adalah skrip Python lintas platform yang mengambil profil WiFi yang tersimpan dan detailnya termasuk kata sandi, jenis otentikasi, enkripsi, dan pita frekuensi radio pada Windows, Linux, dan macOS. Skrip ini menggunakan alat-alat asli platform (`netsh` pada Windows, NetworkManager pada Linux, dan Keychain pada macOS) untuk mengakses dan menampilkan informasi ini. Skrip ini menyertakan menu interaktif untuk memfilter, mencari, dan mengekspor hasil, serta persetujuan pengguna melalui Syarat dan Ketentuan sebelum data apa pun diambil.

## About
[![made-with-python](https://img.shields.io/badge/Made%20with-Python-1f425f.svg)](https://www.python.org/)
[![GitHub license](https://img.shields.io/github/license/Naereen/StrapDown.js.svg)](https://github.com/shopeebjm/Image-Background-Remover/blob/main/LICENSE)

- **Developer:** shopeebjm
- **Application creation date:** 18 June 2024
- **Last updated:** 17 Maret 2026

## Fitur
- Dukungan lintas platform: Windows, Linux, dan macOS.
- Mengambil semua profil WiFi yang tersimpan di perangkat.
- Menampilkan nama WiFi, kata sandi, jenis otentikasi, algoritma enkripsi, dan pita frekuensi radio.
- Output terminal berwarna — hijau untuk profil dengan kata sandi, merah untuk profil tanpa kata sandi.
- Menu interaktif dengan opsi untuk menelusuri, mencari, memfilter, dan mengekspor data.
- Cari profil berdasarkan kata kunci nama.
- Filter untuk hanya menampilkan profil yang memiliki kata sandi tersimpan.
- Ekspor hasil ke CSV, TXT, atau JSON (nama file dengan stempel waktu otomatis).
- Pindai ulang profil tanpa memulai ulang program.
- Akan terinstal secara otomatis coloramajika belum ada.

## Persyaratan

- Python 3.x
- `colorama` (terinstall otomatis saat pertama kali dijalankan)
- **Windows:** tidak ada persyaratan tambahan
- **Linux:** NetworkManager (`/etc/NetworkManager/system-connections/`) — memerlukan akses root untuk kata sandi
- **macOS:** `networksetup` and `security` CLI tools (bawaan)

## Installation

1. Kloning Repositori Ini Atau Unduh Secara Langsung .
   ```sh
   git clone https://github.com/shopeebjm/WiFi-Password-Cracker
   cd WiFi-Password-Cracker
   ```
2. Pastikan Anda Sudah Menginstall Python 3.x. Anda Dapat Mengunduhnya Dari [python.org](https://www.python.org/).

## Penggunaan

1. Buka Terminal Atau Command Prompt
2. Arahkan Ke Deriktori Tempat File `main.py` Tersebut Berada.
3. Jalankan scriptnya:
   ```sh
   python main.py
   ```
   > **Linux/macOS:** Jika Profile Tidak Ditemukan,Jalankan Dengan Hak Akses Administrator :
   > ```sh
   > sudo python main.py
   > ```
4. Baca Dan Setujui Syarat Dan Ketentuan Dengan Mengetik `Y`.
5. Gunakan Menu Interaktif Untuk Bernavigasi :

   | Pilihan | Keterangan |
   |--------|-------------|
   | `[1]` Tampilkan Semua Profil | Tampilkan Semua Profil WiFi Yang Dipindai |
   | `[2]` Cari Berdasarkan Nama | Saring Profil Berdasarkan Kata Kunci |
   | `[3]` Hanya Tampilkan Dengan Kata Sandi | Tampilkan Hanya Profil Yang Memiliki Kata Sandi Yang Tersimpan |
   | `[4]` Ekspor Hasil | Simpan Tampilan Saat Ini Ke CSV, TXT, or JSON |
   | `[5]` Pindai Ulang | Perbarui Daftar Profil Dari Sistem |
   | `[0]` Keluar | Keluar Dari program |

## Penjelasan Kode

### Pengecekan Ketergantungan

```python
def check_dependencies():
    try:
        import colorama
    except ImportError:
        subprocess.check_call([sys.executable, "-m", "pip", "install", "colorama"])
```

Secara Otomatis `colorama` Jika Tidak Ditemukan,Jika Tidak `pip install` Perlu Manual Sebelum Menjalankan Script.

### Deteksi Platform

```python
OS = platform.system()  # "Windows", "Linux", "Darwin"
```

Mendeteksi sistem operasi saat ini dan mengarahkan semua fungsi pengambilan profil sesuai dengan sistem tersebut.

## Parser Khusus Platform

#### Windows : `get_profiles_windows()` / `get_profile_details_windows()`

Digunakan `netsh wlan show profiles` Untuk Menampilkan Semua Profil Dan  `netsh wlan show profile <name> key=clear` Mengambil Kata Sandi, Otentikasi, Sandi, Dan Pita Frekuensi Radio Untuk Setiap Profil

#### Linux : `get_profiles_linux()` / `get_profile_details_linux()`

Membaca `.nmconnection` file Dari `/etc/NetworkManager/system-connections/`. Mengekstrak `psk` (KataSandi), `key-mgmt` (Otorisasi), `pairwise` (enkripsi), dan `band` Bidang. Menampilkan `[Permission Denied]` Catatan Jika Akses Root Diperlukan.

#### macOS : `get_profiles_macos()` / `get_profile_details_macos()`

Uses `networksetup -listpreferredwirelessnetworks en0` to list profiles and `security find-generic-password -wa <name>` to retrieve passwords from the system Keychain.

## Pemindai Terpadu

```python
def scan_all_profiles():
```

Memanggil Parser Khsusus Platform Yang Sesuai Dan Mengembalikan Daftar Kamus Profil Terpadu Dengan Kunci: `name`, `password`, `auth`, `encryption`, `band`.

## Menampilkan

```python
def print_table(profiles):
```

Menampilkan Hasil Sebagai Tabel Yang Diformat Dan Diberi Warna.Baris Ditampilkan Dengan Warna **hijau** Jika Kata Sandi Ditemukan,Dan **merah** Jika Tidak Ada Kata Sandi Yang Tersimpan .

### Filter / Pencarian

```python
def filter_profiles(profiles, keyword=None, with_password_only=False):
```

Memfilter Daftar Profil Berdasarkan Kata Kunci Nama (Tidak Peka Huruf Besar/Kecil). Dan/atau berdasarkan Keberadaan Kata Sandi,Digunakan Oleh Opsi Menu`[2]` and `[3]`.

### Ekspor

```python
def export_profiles(profiles, fmt):
```

Mengekspor Tampilan Saat Ini Kedalam Sebuah File,Nama File Di Buat Secara Otomatis Dengan Stempel Waktu,Misalnya `fisard_wifi_export_20260315_143022.csv`. Mendukung Tiga Format:

- **CSV** — Kompatibel Dengan spreadsheet, Satu Baris Per Profil
- **TXT** — Mudah Dibaca Manusia,Satu Blok Per Profil
- **JSON** — Data Terstruktur, array Objek Profil

### Menu Interaktif

```python
def show_menu():
```

Menampilkan menu bergaya kotak menggunakan karakter batas Unicode. Mengembalikan pilihan pengguna sebagai string untuk diproses oleh loop utama.

## Disclaimer

Skrip ini ditujukan hanya untuk tujuan pendidikan dan penggunaan pribadi. Akses tanpa izin ke jaringan atau perangkat yang bukan milik Anda adalah ilegal dan tidak etis. Gunakan skrip ini secara bertanggung jawab dan hanya pada perangkat yang Anda miliki atau yang Anda miliki izin eksplisit untuk mengaksesnya.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.

-----------------------------------------------------------------------------------------
