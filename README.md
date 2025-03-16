# Password Vault

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
