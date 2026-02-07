# ☁️ Cloud Workstation - GitHub Actions

![Linux](https://img.shields.io/badge/Linux-FCC624?style=for-the-badge&logo=linux&logoColor=black)
![Windows](https://img.shields.io/badge/Windows-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![macOS](https://img.shields.io/badge/macOS-000000?style=for-the-badge&logo=apple&logoColor=white)

Run a full cloud-based development workstation directly from GitHub Actions - with automatic project backup to GitHub!

## 🚀 Features

| Feature | Description |
|---------|-------------|
| 🖥️ **Full Desktop** | XFCE4 (Linux), RDP (Windows), Screen Sharing (macOS) |
| ☁️ **Cloudflare Tunnel** | Secure remote access without port forwarding |
| 🔄 **Auto Backup v2.0** | Real-time + periodic scanning for project backup |
| 📥 **Auto Restore** | Previous projects automatically restored on startup |
| ⏱️ **6 Hour Sessions** | Maximum allowed by GitHub Actions |
| 💻 **Dev Tools** | VS Code, Python, Node.js, Git pre-installed |

## 📋 Available Workflows

| Workflow | Runner | Remote Access | Port |
|----------|--------|---------------|------|
| `cloud-workstation-linux.yml` | Ubuntu | VNC | 5902 |
| `cloud-workstation-windows.yml` | Windows Server | RDP | 3389 |
| `cloud-workstation-macos.yml` | macOS | VNC | 5902 |

## 🔧 Setup Instructions

### Step 1: Add Secrets (Recommended)

Go to your repository **Settings** → **Secrets and variables** → **Actions** → **New repository secret**

| Secret | Required | Description |
|--------|----------|-------------|
| `GH_TOKEN` | **Yes** | Personal Access Token with `repo` scope (for backup) |
| `VNC_PASSWORD` | Optional | Custom VNC password (default: `P@ssw0rd123!`) |
| `RDP_PASSWORD` | Optional | Custom RDP password for Windows (default: `P@ssw0rd123!`) |

#### Creating GH_TOKEN:
1. Go to GitHub → Settings → Developer Settings → Personal Access Tokens → **Tokens (classic)**
2. Click **Generate new token (classic)**
3. Select scopes: `repo` (full control)
4. Copy token and add as `GH_TOKEN` secret

### Step 2: Run Workflow

1. Go to **Actions** tab
2. Select desired workflow (Linux/Windows/macOS)
3. Click **Run workflow**
4. Wait for initialization (~3-5 minutes)

### Step 3: Connect

#### For Linux/macOS (VNC):
```bash
# Step 1: Run on your LOCAL machine
cloudflared access tcp --hostname <HOSTNAME_FROM_LOGS> --url 127.0.0.1:5902

# Step 2: Open VNC Viewer and connect to:
# Server: localhost:5902
# Password: P@ssw0rd123! (or your VNC_PASSWORD secret)
```

#### For Windows (RDP):
```bash
# Step 1: Run on your LOCAL machine
cloudflared access tcp --hostname <HOSTNAME_FROM_LOGS> --url 127.0.0.1:3389

# Step 2: Open Remote Desktop and connect to:
# Server: localhost:3389
# Username: runneradmin
# Password: P@ssw0rd123! (or your RDP_PASSWORD secret)
```

## 🔄 Backup System

### How It Works

```
┌─────────────────────────────────────────────────────────────┐
│             BACKUP WATCHER v2.0 ARCHITECTURE                │
├─────────────────────────────────────────────────────────────┤
│                                                             │
│  ~/Desktop/workspace/     ──────►  inotifywait/fswatch     │
│  ├── project-1/                    (real-time monitor)      │
│  ├── project-2/                           │                 │
│  └── project-3/                           ▼                 │
│                                    Periodic Scan            │
│                                    (every 5 min)            │
│                                           │                 │
│                                           ▼                 │
│                                    GitHub API               │
│                                    (create repo)            │
│                                           │                 │
│                                           ▼                 │
└─────────────────────────────────────► github.com           │
                                        ├── backup-files/     │
                                        │   └── README.md     │
                                        ├── project-1/        │
                                        ├── project-2/        │
                                        └── project-3/        │
```

### Backup Features

| Feature | Description |
|---------|-------------|
| 🔍 **Initial Scan** | Backs up all existing projects on workflow start |
| 👁️ **Real-time Detection** | Uses inotifywait (Linux) / fswatch (macOS) |
| 🔄 **Periodic Scan** | Catches any missed projects every 5 minutes |
| 📋 **Tracked File** | Avoids duplicate backups |
| 🔒 **Private Repos** | All backups are private by default |
| 📂 **Index Repository** | `backup-files` repo tracks all backed up projects |

### Auto Restore

When you start a new workflow session:
1. Workflow checks for `backup-files` repository
2. Reads the project list from README.md
3. Clones all projects to `~/Desktop/workspace/`
4. You continue where you left off! 🎉

## 📁 Directory Structure

```
📂 your-username/
├── 📂 cloud-workstation/          # This repository
│   └── 📂 .github/workflows/
│       ├── cloud-workstation-linux.yml
│       ├── cloud-workstation-windows.yml
│       └── cloud-workstation-macos.yml
│
├── 📂 backup-files/               # Auto-created (private)
│   └── README.md                  # Project index
│
├── 📂 project-1/                  # Auto-backed up (private)
├── 📂 project-2/                  # Auto-backed up (private)
└── 📂 project-3/                  # Auto-backed up (private)
```

## ⚙️ Pre-installed Software

### Linux (Ubuntu)
- XFCE4 Desktop
- VS Code
- Antigravity IDE
- Python 3 + pip
- Node.js LTS + npm
- Git
- Firefox

### Windows
- VS Code
- Python 3 + pip
- Node.js LTS + npm
- Git

### macOS
- VS Code
- Python 3 + pip
- Node.js LTS + npm
- Git

## ⚠️ Limitations

| Limitation | Details |
|------------|---------|
| ⏱️ **Session Duration** | Maximum 6 hours (GitHub Actions limit) |
| 💾 **Storage** | ~14GB available space |
| 🔄 **Concurrency** | Subject to your GitHub plan limits |
| 📊 **Usage** | Counts against GitHub Actions minutes |

## 🛠️ Troubleshooting

### VNC Connection Failed
1. Check workflow logs for Cloudflare hostname
2. Ensure cloudflared is running locally
3. Verify VNC password is correct

### Backup Not Working
1. Ensure `GH_TOKEN` secret is set with `repo` scope
2. Check `~/backup-watcher.log` for errors
3. Wait at least 15 seconds after creating folder

### Cloudflare Tunnel Not Connecting
1. Wait 30+ seconds for tunnel initialization
2. Check workflow logs for tunnel URL
3. Ensure no firewall blocking cloudflared

## 📄 License

MIT License - Feel free to modify and use!

---

**Made with ❤️ for cloud development**
