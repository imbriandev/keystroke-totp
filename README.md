# TOTP Authenticator for Keystroke

Generate and copy time-based one-time passwords (TOTP / 2FA) in the [Keystroke](https://github.com/evindor/keystroke) command palette on Linux Omarchy.

Ported and enhanced from [raycast-totp](https://github.com/imbriandev/raycast-totp) for macOS.

## Features

- **Live TOTP Codes**: Computes RFC 6238 codes (SHA-1, SHA-256, SHA-512; 6 to 8 digits; custom periods) completely in pure JavaScript inside the shell without external runtime lag.
- **Real-Time Countdown**: Countdown accessory and preview pane update live every second while the palette is open.
- **Copy & Paste**:
  - `Enter`: Copies the current code to clipboard.
  - `Ctrl+Enter`: Pastes the code directly into the active window.
- **Quick OTP**: Paste a Base32 secret or `otpauth://totp/...` URI to instantly view and copy a one-time code without saving it.
- **Fuzzy Search**: Filter accounts by name or issuer directly from root or inside the `totp` screen.
- **Raycast Backup Compatibility**: Full support for importing and exporting backups, including Raycast's AES-256-GCM encrypted format.

## Turn it on

Extensions in Keystroke start switched off. To enable:
1. Open Keystroke palette (`Super+Space` or configured key).
2. Type `ext` or open **Extensions → TOTP**.
3. Confirm **Enabled** (or via **Keystroke Settings → TOTP → Enabled**).

## Usage

| Query | Action |
| --- | --- |
| `totp` | List all saved accounts with live codes and countdown timers |
| `totp github` | Filter accounts by name or issuer |
| `totp add JBSWY3DPEHPK3PXP GitHub` | Add account from Base32 secret with name |
| `totp add otpauth://totp/GitHub:user?secret=...` | Add account from otpauth URI |
| `totp quick JBSWY3DPEHPK3PXP` | Quick OTP: generate one-time code without saving |
| `totp JBSWY3DPEHPK3PXP` | Auto-detects Base32 secret and offers Quick OTP |
| `otpauth://totp/...` | Auto-detects pasted URI and offers Quick OTP |

Inside an account row:
- `↵ Enter`: Copy OTP code to clipboard.
- `Ctrl+↵`: Paste OTP code into previous window.

## Settings

Available under **Keystroke Settings → TOTP**:

- **Notification on copy**: Show desktop notification when an OTP code is copied (default: `true`).
- **Auto-backup directory**: Folder to save encrypted `totp-backup.json` after changes (e.g. `~/Downloads` or `~/Backups`).
- **Auto-backup passphrase**: Passphrase for AES-256-GCM encryption. Both directory and passphrase must be set to enable encrypted auto-backups.
- **Default digits**: Number of digits for raw secrets (default: `6`).
- **Default period**: Validity interval in seconds (default: `30`).

## CLI Utility (`bin/totp-cli`)

For terminal automation and Raycast backup migration:

```sh
# List accounts
./bin/totp-cli list

# Add account
./bin/totp-cli add "otpauth://totp/GitHub:alice?secret=JBSWY3DP"

# Export encrypted backup (100% compatible with raycast-totp)
./bin/totp-cli export my-backup.json --passphrase "my-secure-password"

# Import encrypted backup from raycast-totp
./bin/totp-cli import totp-backup-mac.json --passphrase "my-secure-password"
```

## Security & Storage

- Accounts are stored locally in `~/.local/state/keystroke/totp/accounts.json` with standard user-only file permissions.
- Pure JavaScript cryptographic operations (SHA-1, SHA-256, SHA-512 HMAC) run entirely offline. No telemetry, no cloud sync, no tracking.

## Repository Layout

```
├── extension.json      Metadata, commands, and schema for Keystroke
├── Service.qml         QML provider and reactive countdown timer
├── core/
│   └── TOTP.js         Pure JavaScript TOTP/HOTP crypto and row generation
├── tests/
│   └── tst_totp.qml    Unit tests (RFC 6238 vectors, parsing, storage)
├── assets/
│   ├── icon.svg        Vector key icon for palette
│   └── totp-icon.png   Brand icon from raycast-totp
├── bin/
│   └── totp-cli        CLI management and backup migration tool
└── README.md
```
