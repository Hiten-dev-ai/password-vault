# Password Vault

A secure desktop application for managing passwords locally with strong encryption. This password manager provides a clean terminal-based interface for organizing and securely storing your credentials.

![Password Vault](screenshots/password_vault_main.png)

## Table of Contents
- [Features](#features)
- [Security Architecture](#security-architecture)
- [Installation](#installation)
- [Usage Guide](#usage-guide)
  - [First Time Setup](#first-time-setup)
  - [Managing Services](#managing-services)
  - [Managing Records](#managing-records)
  - [Changing Master Password](#changing-master-password)
- [Technical Details](#technical-details)
- [Security Best Practices](#security-best-practices)
- [Screenshots](#screenshots)
- [Compatibility](#compatibility)
- [License](#license)
- [Privacy Policy](#privacy-policy)
- [Questions & Support](#questions--support)

## Features

- **Encrypted Local Database**: All data is stored in an encrypted SQLite database
- **Service-Based Organization**: Group credentials by service (websites, applications, etc.)
- **Multiple Account Management**: Store multiple accounts per service
- **Strong Encryption**: Uses PBKDF2 with SHA-256 for key derivation
- **Visual Progress Indicators**: Includes loading animations and progress bars
- **Clean Terminal UI**: Easy-to-navigate text-based interface
- **Completely Offline**: No internet connection required or used

## Security Architecture

Password Vault uses a layered security approach:

1. **Master Password Protection**:
   - The master password is never stored directly
   - A key derivation function (PBKDF2) with SHA-256 creates a secure hash
   - 100,000 iterations provide resistance against brute force attacks

2. **Database Encryption**:
   - SQLite database is secured with Fernet symmetric encryption
   - Each record is individually encrypted within the database
   - Fixed salt is used for consistent key derivation

3. **Memory Safety**:
   - Sensitive data is only held in memory temporarily
   - Secure input handling for password entry

## Installation

### Download the Executable
1. Go to the [Releases](https://github.com/yourusername/password-vault/releases) page
2. Download the latest `PasswordVault.exe`
3. Run directly - no installation required

### Verifying File Integrity
To ensure you've downloaded an unmodified version of the executable, verify the SHA-256 hash:

```bash
# Windows (PowerShell)
Get-FileHash -Algorithm SHA256 .\PasswordVault.exe

# Linux/macOS
shasum -a 256 PasswordVault.exe
```

Compare the output with the hash provided in the release notes.

## Usage Guide

### First Time Setup

When you first run Password Vault, you'll be prompted to create a master password:

```
   ___    ___    ____   ____  _      __  ____    ___    ___        _   __   ___   __  __   __  ______
  / _ \  / _ |  / __/  / __/ | | /| / / / __ \  / _ \  / _ \      | | / /  / _ | / / / /  / / /_  __/
 / ___/ / __ | _\ \   _\ \   | |/ |/ / / /_/ / / , _/ / // /      | |/ /  / __ |/ /_/ /  / /__ / /   
/_/    /_/ |_|/___/  /___/   |__/|__/  \____/ /_/|_| /____/       |___/  /_/ |_|\____/  /____//_/   

CREATE DATABASE
SET DATABASE PASSWORD (MIN 8 CHARACTERS): ********
CONFIRM PASSWORD: ********
```

Important notes:
- Choose a strong, memorable password
- Minimum 8 characters required 
- This password cannot be recovered if forgotten
- Your database will be stored at `~/vault.db`

### Managing Services

Services are categories for your passwords (websites, applications, etc.):

```
  __  __     _     _  _     _      ___   ___     ___   ___   ___  __   __  ___    ___   ___   ___ 
 |  \/  |   /_\   | \| |   /_\    / __| | __|   / __| | __| | _ \ \ \ / / |_ _|  / __| | __| / __|
 | |\/| |  / _ \  | .` |  / _ \  | (_ | | _|    \__ \ | _|  |   /  \ V /   | |  | (__  | _|  \__ \
 |_|  |_| /_/ \_\ |_|\_| /_/ \_\  \___| |___|   |___/ |___| |_|_\   \_/   |___|  \___| |___| |___/

[1]LIST ALL SERVICES
[2]ADD SERVICE
[3]MODIFY SERVICE
[4]DELETE SERVICE
[5]RETURN TO MAIN MENU
```

Example workflow:
1. Select option `2` to add a service
2. Enter service name (e.g., "GMAIL" or "BANKING")
3. Confirm the addition

### Managing Records

Records are the actual username/password pairs:

```
  __  __     _     _  _     _      ___   ___     ___   ___    ___    ___    ___   ___    ___ 
 |  \/  |   /_\   | \| |   /_\    / __| | __|   | _ \ | __|  / __|  / _ \  | _ \ |   \  / __|
 | |\/| |  / _ \  | .` |  / _ \  | (_ | | _|    |   / | _|  | (__  | (_) | |   / | |) | \__ \
 |_|  |_| /_/ \_\ |_|\_| /_/ \_\  \___| |___|   |_|_\ |___|  \___|  \___/  |_|_\ |___/  |___/

[1]LIST RECORDS
[2]ADD RECORD
[3]MODIFY RECORD
[4]DELETE RECORD
[5]RETURN TO MAIN MENU
```

Adding a new record:
1. Select option `2`
2. Choose a service (must be created first)
3. Enter username and password
4. Confirm the details

Viewing records example output:
```
#GMAIL
RECORD [1]
USERNAME : myemail@gmail.com
PASSWORD : MySecureP@ssw0rd

#BANKING
RECORD [1]
USERNAME : user123
PASSWORD : BankingP@55
```

### Changing Master Password

To update your master password:

1. From the main menu, select option `3`
2. Enter your current password
3. Enter and confirm your new password

```
   ___ _  _   _   _  _  ___ ___   ___  _   ___ _____      _____  ___ ___  
  / __| || | /_\ | \| |/ __| __| | _ \/_\ / __/ __\ \    / / _ \| _ \   \ 
 | (__| __ |/ _ \| .` | (_ | _|  |  _/ _ \\__ \__ \\ \/\/ / (_) |   / |) |
  \___|_||_/_/ \_\_|\_|\___|___| |_|/_/ \_\___/___/ \_/\_/ \___/|_|_\___/ 

ENTER CURRENT PASSWORD: ********
ENTER NEW PASSWORD: ********
CONFIRM NEW PASSWORD: ********
```

## Technical Details

### Database Structure

The application uses an SQLite database with the following schema:

```sql
-- Services table stores categories
CREATE TABLE services (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    name TEXT UNIQUE
)

-- Records table stores encrypted credentials
CREATE TABLE records (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    service_id INTEGER,
    username TEXT,
    password TEXT,
    FOREIGN KEY(service_id) REFERENCES services(id)
)

-- Vault table stores master password hash
CREATE TABLE vault (
    id INTEGER PRIMARY KEY AUTOINCREMENT,
    service TEXT,
    pass_hash TEXT
)
```

### Encryption Implementation

Password Vault uses the following cryptographic components:

```python
# Key derivation from master password
def derive_key(password, salt):
    kdf = PBKDF2HMAC(
        algorithm=hashes.SHA256(),
        length=32,
        salt=salt,
        iterations=100000,
        backend=default_backend()
    )
    return kdf.derive(password.encode())
```

## Security Best Practices

When using Password Vault:

1. **Choose a Strong Master Password**
   - Use a combination of uppercase, lowercase, numbers, and special characters
   - Minimum recommended length is 12+ characters
   - Consider using a passphrase (multiple random words)

2. **Regular Backups**
   - Create periodic backups of your `vault.db` file
   - Store backups securely on separate media

3. **Regular Password Updates**
   - Change critical passwords periodically
   - Use the vault to generate and store complex passwords

4. **Physical Security**
   - Lock your computer when away
   - Limit physical access to your device

## Screenshots

![Main Menu](screenshots/main_menu.png)
![Services Management](screenshots/services_management.png)
![Records Management](screenshots/records_management.png)
![Adding a Password](screenshots/add_password.png)

## Compatibility

- **Operating System**: Windows 10/11 (64-bit)
- **Requirements**: No additional dependencies needed to run the executable

## License

[MIT License](LICENSE)

## Privacy Policy

This application:
- Does not collect any user data
- Does not require or use internet connection
- Stores all information locally on your device in encrypted format
- Does not include any tracking or analytics
- Does not communicate with external servers

## Questions & Support

For issues, questions, or suggestions:
- [Open an issue](https://github.com/yourusername/password-vault/issues) on this repository
- When reporting issues, please include:
  - Operating system and version
  - Steps to reproduce the problem
  - Any error messages (without sharing sensitive data)

---

*Note: This password manager is designed for personal use. While it implements strong security practices, no software can guarantee absolute security. Always maintain backups and practice good security hygiene.*# Password Vault

A secure desktop application for managing passwords locally with encryption.

## Features

- Encrypted local database for password storage
- Service-based organization of credentials
- Manage multiple accounts per service
- Strong encryption with PBKDF2 key derivation
- Clean terminal UI with visual feedback

## Installation

### Option 1: Download the executable
1. Go to the [Releases](https://github.com/yourusername/password-vault/releases) page
2. Download the latest `PasswordVault.exe`
3. Run the executable directly - no installation required

### Option 2: Run from source
1. Clone this repository
2. Install required dependencies: `pip install -r requirements.txt`
3. Run `python database_locker.py`

## Requirements (for building from source)
- Python 3.6+
- cryptography
- sqlite3 (included with Python)

## Usage

1. When first run, you'll be prompted to create a master password
2. Navigate the menus using the numbered options
3. Add services first, then add credentials to those services
4. Your data is stored in an encrypted SQLite database in your home directory

## Security Notes

- All data is stored locally in an encrypted SQLite database
- Master password is hashed using PBKDF2 with SHA-256
- Database is encrypted using Fernet symmetric encryption
- No data is transmitted over the internet

## Building from Source

```bash
# Install dependencies
pip install -r requirements.txt pyinstaller

# Build executable
pyinstaller --onefile --noconsole database_locker.py
```

## License

[MIT License](LICENSE)
