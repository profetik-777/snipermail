# snipermail
Tui tool - This tool helps you configure a **send-only mail relay** on Linux or macOS using [`msmtp`](https://marlam.de/msmtp/). It’s perfect for self‑hosted apps that need to send notification emails (e.g., Nextcloud, Jellyfin, Portainer) but don’t require inbound mail handling. 


If everything is working, you’ll receive the test message in your Gmail inbox.


Check logs if something fails:


```bash
cat ~/.config/msmtp/msmtp.log
```


---


## 🧩 Using in Self‑Hosted Apps
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


## 🧠 Why msmtp Instead of Mailgun?
| Option | Pros | Cons |
|--------|------|------|
| **msmtp + Gmail** | Free, private, simple to set up; no domain required | Limited by Gmail daily send limits (~500/day) |
| **Mailgun** | Scalable, domain‑verified, supports webhooks | Requires domain setup; free tier capped (~100/day) |


---


## ✅ Summary
**notify_mail_setup.py** = 5‑minute setup to turn your local Linux box into a send‑only Gmail relay. No extra daemons, no DNS setup, and easy integration with any self‑hosted app.


To rerun setup anytime:
```bash
python3 notify_mail_setup.py
```


Then test:
```bash
echo "Hello world from msmtp" | msmtp -a gmail your.name@gmail.com
```
