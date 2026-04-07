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

1. Clone this repository or download the script directly.
   ```sh
   git clone https://github.com/shabir-mp/WiFi-Password-Cracker.git
   cd WiFi-Password-Cracker
   ```
2. Ensure you have Python 3.x installed. You can download it from [python.org](https://www.python.org/).

## Usage

1. Open a terminal or command prompt.
2. Navigate to the directory where `main.py` is located.
3. Run the script:
   ```sh
   python main.py
   ```
   > **Linux/macOS:** If profiles are not found, run with elevated permissions:
   > ```sh
   > sudo python main.py
   > ```
4. Read and agree to the Terms and Conditions by typing `Y`.
5. Use the interactive menu to navigate:

   | Option | Description |
   |--------|-------------|
   | `[1]` Show All Profiles | Display all scanned WiFi profiles |
   | `[2]` Search by Name | Filter profiles by a keyword |
   | `[3]` Show Only With Password | Display only profiles that have a saved password |
   | `[4]` Export Results | Save current view to CSV, TXT, or JSON |
   | `[5]` Re-Scan | Refresh the profile list from the system |
   | `[0]` Exit | Exit the program |

## Code Explanation

### Dependency Check

```python
def check_dependencies():
    try:
        import colorama
    except ImportError:
        subprocess.check_call([sys.executable, "-m", "pip", "install", "colorama"])
```

Automatically installs `colorama` if it is not found, so no manual `pip install` is needed before running the script.

### Platform Detection

```python
OS = platform.system()  # "Windows", "Linux", "Darwin"
```

Detects the current operating system and routes all profile retrieval functions accordingly.

### Platform-Specific Parsers

#### Windows : `get_profiles_windows()` / `get_profile_details_windows()`

Uses `netsh wlan show profiles` to list all profiles and `netsh wlan show profile <name> key=clear` to retrieve password, authentication, cipher, and radio band for each profile.

#### Linux : `get_profiles_linux()` / `get_profile_details_linux()`

Reads `.nmconnection` files from `/etc/NetworkManager/system-connections/`. Extracts `psk` (password), `key-mgmt` (auth), `pairwise` (encryption), and `band` fields. Raises a `[Permission Denied]` note if root access is required.

#### macOS : `get_profiles_macos()` / `get_profile_details_macos()`

Uses `networksetup -listpreferredwirelessnetworks en0` to list profiles and `security find-generic-password -wa <name>` to retrieve passwords from the system Keychain.

### Unified Scanner

```python
def scan_all_profiles():
```

Calls the appropriate platform-specific parser and returns a unified list of profile dictionaries with the keys: `name`, `password`, `auth`, `encryption`, `band`.

### Display

```python
def print_table(profiles):
```

Prints results as a formatted, colorized table. Rows are shown in **green** if a password is found, and **red** if no password is stored.

### Filter / Search

```python
def filter_profiles(profiles, keyword=None, with_password_only=False):
```

Filters the profile list by a case-insensitive name keyword and/or by password presence. Used by menu options `[2]` and `[3]`.

### Export

```python
def export_profiles(profiles, fmt):
```

Exports the current profile view to a file. The filename is auto-generated with a timestamp, e.g. `fisard_wifi_export_20260315_143022.csv`. Supports three formats:

- **CSV** — spreadsheet-compatible, one row per profile
- **TXT** — human-readable, one block per profile
- **JSON** — structured data, array of profile objects

### Interactive Menu

```python
def show_menu():
```

Displays a box-styled menu using Unicode border characters. Returns the user's choice as a string for the main loop to process.

## Disclaimer

This script is intended for educational and personal use only. Unauthorized access to networks or devices you do not own is illegal and unethical. Use this script responsibly and only on devices you own or have explicit permission to access.

## License

This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more details.

-----------------------------------------------------------------------------------------
![Github Footer](https://github.com/shabir-mp/Kereta-Api-Indonesia-Booking-System/assets/133546000/c1833fe4-f470-494f-99e7-d583421625be)
