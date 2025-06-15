
# ModSecurity WAF with FastAPI Dashboard – PinewoodStore Example

This project demonstrates a **Dockerized deployment of ModSecurity** as a Web Application Firewall (WAF), integrated with a custom **FastAPI-based monitoring dashboard**. The example application used is **PinewoodStore**, but the setup can be adapted to protect **any other web application** by modifying configuration.

---

## 📦 Project Structure

```

modsec-docker/
├── apache-modsec/
│   ├── apache-config/
│   │   └── myapp.conf        # Apache virtual host & ModSecurity rules for PinewoodStore
│   └── Dockerfile            # Builds Apache + ModSecurity container
├── dashboard/
│   ├── main.py               # FastAPI dashboard backend
│   ├── templates/            # Jinja2 HTML templates for logs and dashboard
│   └── requirements.txt      # Python dependencies
├── docker-compose.yml        # Defines WAF and dashboard services
└── README.md                 # You are here

````

---

## 🚀 How It Works

- **ModSecurity (Apache module)** inspects incoming HTTP traffic and blocks malicious requests based on rule sets.
- The WAF acts as a **reverse proxy**, sitting in front of the target application (e.g., PinewoodStore).
- The **FastAPI dashboard** reads ModSecurity's audit log (`modsec_audit.log`) and displays:

  - ✅ Normal Traffic  
  - 🚫 Blocked Requests (rule violations like 406, 414)  
  - 🔒 Attack Attempts (403 Forbidden)

- You can **export logs** to CSV or PDF and **reset** the data via the dashboard.

---

## 🔧 Building and Running

### 1. Clone the Repository

```bash
git clone https://github.com/enochgitgamefied/Modsecurity-Dashboard-Remote-WAF-LinuxOnly.git
cd Modsecurity-Dashboard-Remote-WAF-LinuxOnly
````

### 2. Configure Apache Reverse Proxy and ModSecurity

Edit the file:

```bash
apache-modsec/apache-config/myapp.conf
```

Update the proxy target:

```apache
ProxyPass / http://<YOUR_APP_HOST>:<PORT>/
ProxyPassReverse / http://<YOUR_APP_HOST>:<PORT>/
```

Example:

```apache
ProxyPass / http://192.168.1.173:8088/
ProxyPassReverse / http://192.168.1.173:8088/
```

This tells the WAF to forward traffic to your backend application.

> 💡 Note: You can protect **remote** applications by setting the appropriate IP and port.

---

## 🌐 Accessing the WAF and Dashboard

After deployment, access the components like so:

* 🔐 **WAF-Protected Application**:
  From a browser on any client machine:

  ```
  http://{WAF-HOST-IP}:8880
  ```

  (e.g., `http://192.168.1.100:8880`)

  This URL is served through Apache + ModSecurity and routes to your backend app.

* 📊 **Dashboard Interface**:

  ```
  http://{WAF-HOST-IP}:8000
  ```

  This opens the FastAPI dashboard UI with:

  * Realtime traffic stats
  * Blocked request details
  * Export / reset functions
  * Rule creation/management console

---

## 🧠 Why Use Host Networking?

This stack uses `network_mode: host` and is designed for **Linux-only deployment**. Here's why:

* Docker bridge networking hides the real client IP (appears as `172.17.0.1`).
* Host networking allows ModSecurity to see **true client IP addresses**, which are logged and shown on the dashboard.
* This is critical for real-world monitoring, threat tracing, and forensic analysis.

> ⚠️ Not compatible with Windows/Mac due to lack of host mode support in Docker.

---

## 🔁 Switching to a Different App

To use this WAF for another backend application:

### Step 1: Update Proxy Configuration

Edit the reverse proxy section in:

```bash
apache-modsec/apache-config/myapp.conf
```

Change this:

```apache
ProxyPass / http://192.168.1.173:8088/
ProxyPassReverse / http://192.168.1.173:8088/
```

To point to your own app's IP and port.

### Step 2: Rebuild & Restart

```bash
docker-compose down
docker-compose up --build
```

> ✅ The FastAPI dashboard does **not need any changes**, as it parses generic ModSecurity logs.

---

## 🛠️ Configurable Components

* **Ruleset**: You can mount your own custom rule directory and set `Include` paths.
* **Audit Log Path**: Ensure Apache logs to `/var/log/apache2/modsec_audit.log`, which is mounted into the dashboard container.
* **Virtual Hosts**: You can create multiple `<VirtualHost>` blocks to serve different apps on different ports or domains.

---

## 📂 Logs Location

ModSecurity audit logs are written to:

```
/var/log/apache2/modsec_audit.log
```

This file is shared with the FastAPI dashboard via volume mount for real-time log analysis.

---

## 📄 Features

* ✅ Full WAF protection with Apache + ModSecurity
* 📈 Live FastAPI dashboard for log analysis
* 📁 Log export: CSV / PDF
* ✏️ Create / disable rules from the UI
* 🔄 Reset logs for fresh testing
* 🔧 Adaptable to **any backend** behind the WAF

---

## 🛑 Requirements

* ✅ **Linux OS** (host networking required)
* ✅ Docker + Docker Compose
* ✅ Python 3.9+ (for standalone dashboard use)
* ⚙️ Basic understanding of Apache reverse proxying and containerization

---

## 🧪 Example: PinewoodStore

This project uses `PinewoodStore` — a test e-commerce platform — to demonstrate:

* Detection of SQL Injection, XSS, and malformed input
* Real-time dashboard display of blocked / allowed traffic
* IP-aware attack detection thanks to host networking

---

## 📺 Instructional Video

🎥 A full tutorial covering setup, configuration, and usage of this project will be released soon on YouTube.
Stay tuned!

---

## 🖼️ UI Screenshots

### Top

<img width="1280" alt="image" src="https://github.com/user-attachments/assets/9c61f416-7af3-449b-9a73-0aa788e65cc0" />

### Bottom

<img width="1280" alt="image" src="https://github.com/user-attachments/assets/7c8f5372-70ea-4c31-84e4-4dc924d84bd6" />

### 🔐 New Feature – WAF Rule Management Console

![Modsecurity Frontend Dashboard with Rule Management Feature](https://github.com/user-attachments/assets/182dd96e-fa26-411b-81f8-6e655a5a62ed)

### ✍️ Feature Upgrade – Create Custom Rule from Dashboard

<img width="1280" alt="image" src="https://github.com/user-attachments/assets/57626234-4596-4684-919d-83e3856b2e94" />

---

## 🤝 License

MIT or your preferred open-source license.


