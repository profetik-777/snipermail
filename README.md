# Snipermail (msmtp + Gmail)

Snipermail is a **send-only mail relay setup wizard** for Linux and macOS. It uses [`msmtp`](https://marlam.de/msmtp/) under the hood to let your self-hosted apps send notification emails via Gmail with minimal setup. WARNING: Made with chatgpt. Use at your own risk. 

---

## 🚀 Features
- Interactive **text-based setup wizard (TUI)**
- Automatically creates a secure `~/.config/msmtp/config`
- Uses **Gmail App Passwords** for secure SMTP authentication
- Optionally maps local users to your Gmail address
- Sends a **test email** after setup
- Logs activity to `~/.config/msmtp/msmtp.log`

---

## 🧰 Prerequisites
- A POSIX system (Linux or macOS)
- Python 3.7+
- `msmtp` installed on your system
- A Gmail account with:
  - **2-Step Verification** enabled
  - An **App Password** generated for “Mail → Other (Custom name)”

---

## ⚙️ Installation
### 1. Save the Script
Save the file as `snipermail.py` and make it executable:

```bash
chmod +x snipermail.py
```

### 2. Install Dependencies
If `msmtp` isn’t installed, use one of the following:

```bash
# Debian/Ubuntu
sudo apt update && sudo apt install -y msmtp msmtp-mta mailutils

# Fedora/RHEL
sudo dnf install -y msmtp msmtp-mta mailx

# Arch Linux
sudo pacman -S --noconfirm msmtp msmtp-mta mailx

# openSUSE
sudo zypper install -y msmtp msmtp-mta mailx

# macOS (Homebrew)
brew install msmtp s-nail
```

---

## 🧑‍💻 Running the Script
Run the setup wizard with:

```bash
./snipermail.py
```

or (if not executable):

```bash
python3 snipermail.py
```

You’ll be guided through:
1. Gmail address entry
2. Display name (optional)
3. Gmail App Password input
4. Configuration generation
5. Sending a test message

---

## 📁 Files Created
| File | Description |
|------|--------------|
| `~/.config/msmtp/config` | Main msmtp configuration file |
| `~/.config/msmtp/app_password.txt` | Stores Gmail App Password (chmod 600) |
| `~/.aliases` | Maps local usernames to Gmail address |
| `~/.config/msmtp/msmtp.log` | Log file for sent mail |

---

## 🔍 Verifying Setup
After setup, you can test sending an email manually:

```bash
echo "Test body" | msmtp -a gmail your.name@gmail.com
```

If everything is working, you’ll receive the test message in your Gmail inbox.

Check logs if something fails:

```bash
cat ~/.config/msmtp/msmtp.log
```

---

## 🧩 Using in Self-Hosted Apps
Most apps can send mail either by SMTP or by using a **sendmail path**.

### Option 1: Sendmail Path
Set your app’s “sendmail path” to:

```
/usr/sbin/sendmail
```

Make sure `msmtp` is linked to that path:

```bash
sudo ln -sf $(command -v msmtp) /usr/sbin/sendmail
```

### Option 2: Direct SMTP
If your app uses SMTP directly, configure it as follows:

```
SMTP Host: smtp.gmail.com
SMTP Port: 587
SMTP Username: your.name@gmail.com
SMTP Password: <your Gmail App Password>
Encryption: STARTTLS
From Address: your.name@gmail.com
```

---

## 🪄 Troubleshooting
- **“msmtp: command not found”** → Install it with your distro’s package manager.
- **“Authentication failed”** → Ensure you created an App Password (not your normal Gmail password).
- **No email received** → Check `~/.config/msmtp/msmtp.log` for SMTP errors.
- **TLS errors** → Confirm the `tls_trust_file` path matches your system’s certificate bundle.

---

## 🧱 Removing the Setup
To remove everything:

```bash
rm -rf ~/.config/msmtp ~/.aliases
sudo rm /usr/sbin/sendmail 2>/dev/null || true
```

---

## 🧠 Why Snipermail Instead of Mailgun?
| Option | Pros | Cons |
|--------|------|------|
| **Snipermail (msmtp + Gmail)** | Free, private, simple to set up; no domain required | Limited by Gmail daily send limits (~500/day) |
| **Mailgun** | Scalable, domain-verified, supports webhooks | Requires domain setup; free tier capped (~100/day) |

---

## ✅ Summary
**Snipermail** = 5-minute setup to turn your local Linux box into a send-only Gmail relay. No extra daemons, no DNS setup, and easy integration with any self-hosted app.

To rerun setup anytime:
```bash
python3 snipermail.py
```

Then test:
```bash
echo "Hello world from Snipermail" | msmtp -a gmail your.name@gmail.com
```
