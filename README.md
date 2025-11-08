# 💻 devbox-bootstrap

**Terraform + cloud-init + Bash system for provisioning reproducible development servers anywhere.**

`devbox-bootstrap` lets you spin up disposable or persistent cloud dev boxes with everything you need preconfigured — from basic tools and Docker to Node, Go, Rust, and LSPs — all selected interactively when you SSH in.

---

## ✨ Features

- ⚙️ **Infrastructure-as-code provisioning**
  - Uses Terraform for any provider (Hetzner, DigitalOcean, Vultr, AWS, etc.)
  - Quickly spin up or destroy dev servers with one command.

- 💬 **Interactive setup menu**
  - Choose tools, languages, and LSPs via a clean terminal UI (powered by `whiptail`).
  - Supports multi-select menus and “Select All”.

- 🧩 **Modular and extensible**
  - Add or remove installer modules easily — each tool or LSP lives in its own `.sh` file.

- 🔐 **Secure by default**
  - No secrets in user-data; integrates with Bitwarden CLI for secret retrieval.

- 🧱 **Reproducible and disposable**
  - Recreate identical environments anytime with the same Terraform + Bash combo.

---

## 📂 Repository Structure

```
devbox-bootstrap/
├─ terraform/                  # Infrastructure-as-code for provisioning
│  ├─ main.tf
│  ├─ variables.tf
│  ├─ outputs.tf
│  └─ cloud-init.yaml
└─ dev-bootstrap/              # Interactive setup scripts
   ├─ install.sh               # Main interactive menu
   └─ modules/
      ├─ tools.sh              # Multi-select tools installer
      ├─ lsps.sh               # Multi-select LSP installer
      ├─ secrets_bitwarden.sh  # Optional Bitwarden integration
      └─ utils.sh              # Shared helper functions
```

---

## 🚀 Quick Start

### 1️⃣ Set up environment variables
```bash
export TF_VAR_hcloud_token="hc_XXXXXXXXXXXXXXXX"
export TF_VAR_ssh_pubkey="$(cat ~/.ssh/id_ed25519.pub)"
```

---

### 2️⃣ Deploy the server
```bash
cd terraform
terraform init
terraform apply -auto-approve
```

When done, Terraform prints your new server’s IP.

---

### 3️⃣ Connect via SSH
```bash
ssh dev@<SERVER_IP>
```

---

### 4️⃣ Run the interactive installer
```bash
sudo /opt/dev-bootstrap/install.sh
```

You’ll get a menu like:

```
[✔] Install common tools
[ ] Install Docker
[ ] Install Go
[ ] Install Node (fnm)
[ ] Install Rust
[ ] Install Python
[ ] Install Neovim
```

Use arrow keys and spacebar to pick your setup, then press Enter to install.

---

### 5️⃣ Tear down when finished
```bash
cd terraform
terraform destroy -auto-approve
```

---

## 🧰 Customization

### Adding new tools
Add to `modules/tools.sh`:
```bash
install_htop(){ ensure_root; apt-get install -y htop; }
INSTALLERS[htop]=install_htop
LABELS[htop]="Htop (process viewer)"
```

### Adding new LSPs
In `modules/lsps.sh`, define a new installer and register it in the arrays just like the tools.

---

## 🔐 Optional: Bitwarden Secrets

If you need to pull API keys or JSON configs:
```bash
bw login --method 0
export BW_SESSION="$(bw unlock --raw)"
bw get notes "Opencode Auth JSON" > ~/.config/opencode/auth.json
```

---

## 🧠 Tips

- Works great with GitHub Actions for automated spin-up / teardown.  
- Add `TAILSCALE_AUTHKEY` to integrate private SSH networking.  
- To update scripts on an existing server:
  ```bash
  cd /opt/dev-bootstrap
  git pull
  sudo chmod +x install.sh
  ```

---

## 🧩 Example Workflow

1. Run GitHub Action → creates a Hetzner or DigitalOcean dev box  
2. Server clones `devbox-bootstrap` repo and prepares environment  
3. SSH in → `sudo /opt/dev-bootstrap/install.sh`  
4. Select tools → environment ready in minutes  
5. Destroy instance when done

---

## 🏗️ Requirements

- Terraform ≥ 1.6  
- Git ≥ 2.40  
- Ubuntu 22.04+ (tested)  
- Access to a supported cloud provider API token (e.g. Hetzner)

---

## 📜 License

MIT License © 2025 — You are free to copy, modify, and reuse.

---

## 🧩 Maintainers

Built and maintained by developers who love fast, reproducible cloud environments.
