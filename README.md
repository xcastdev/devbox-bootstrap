# ⚙️ infra-bootstrap

**Reusable Terraform + cloud-init + Bash system for quickly provisioning and configuring servers on any cloud provider.**

This repository lets you spin up a reproducible server environment for development, testing, or lightweight service hosting.  
Once the server is provisioned, you can SSH in and use an **interactive installer** to select tools, languages, and LSPs to install — all modular and easy to extend.

---

## ✨ Features

- 🧱 **Terraform-based provisioning**  
  Works with Hetzner Cloud, DigitalOcean, Vultr, AWS, and other providers.

- ⚙️ **cloud-init automation**  
  Installs core packages, firewall rules, users, and clones your setup repo.

- 💬 **Interactive post-install script**  
  Multi-select menus (via `whiptail`) to install:
  - Common tools (curl, git, build-essential, etc.)
  - Docker
  - Node (via fnm)
  - Go, Rust, Python, Neovim
  - LSPs (gopls, pyright, rust-analyzer, tsserver, bashls)
  - Bitwarden CLI (optional auth fetch)

- 🧩 **Modular design**  
  Each feature (tools, LSPs, secrets, utils) is a small shell module you can add or remove freely.

- 🔐 **No secrets in user-data**  
  Secure by design — secrets are managed via Bitwarden or manually after SSH.

---

## 📂 Repository Structure

```
infra-bootstrap/
├─ terraform/                  # Infrastructure-as-code for the server
│  ├─ main.tf                  # Terraform resources
│  ├─ variables.tf             # Input variables (size, location, token, etc.)
│  ├─ outputs.tf               # Server IP output
│  └─ cloud-init.yaml          # Bootstraps base OS + clones setup repo
└─ dev-bootstrap/              # Interactive post-provision setup
   ├─ install.sh               # Main interactive menu
   └─ modules/
      ├─ tools.sh              # Multi-select tools installer
      ├─ lsps.sh               # Multi-select LSP installer
      ├─ secrets_bitwarden.sh  # Bitwarden CLI helpers (optional)
      └─ utils.sh              # Helper functions
```

---

## 🚀 Quick Start

### 1. Set environment variables (or use `.tfvars`)
```bash
export TF_VAR_hcloud_token="hc_XXXXXXXXXXXXXXXX"
export TF_VAR_ssh_pubkey="$(cat ~/.ssh/id_ed25519.pub)"
```

> Works with Hetzner by default.  
> To switch to another provider, replace the `provider` and `resource` sections in `terraform/main.tf`.

---

### 2. Deploy the server
```bash
cd terraform
terraform init
terraform apply -auto-approve
```

When provisioning completes, Terraform will output the public IP.

---

### 3. SSH into the server
```bash
ssh dev@<SERVER_IP>
```

---

### 4. Run the interactive installer
```bash
sudo /opt/dev-bootstrap/install.sh
```

You’ll see menus like:
```
[✔] Install common tools
[ ] Install Docker
[ ] Install Go
[ ] Install Node (fnm)
[ ] Install Rust
[ ] Install Python
[ ] Install Neovim
```

Select what you need and watch it install automatically.

---

### 5. (Optional) Destroy the server
```bash
cd terraform
terraform destroy -auto-approve
```

---

## 🧰 Customization

### Adding a new tool
1. Add a new function in `modules/tools.sh`:
   ```bash
   install_htop(){ ensure_root; apt-get install -y htop; }
   ```
2. Register it:
   ```bash
   INSTALLERS[htop]=install_htop
   LABELS[htop]="Htop (process viewer)"
   ```

### Adding a new LSP
1. Add a function in `modules/lsps.sh`
2. Register it in the `LSP_INSTALLERS` array

---

## 🔐 Secrets Management (optional)

If you use Bitwarden CLI for your API keys or credentials:
```bash
bw login --method 0
export BW_SESSION="$(bw unlock --raw)"
bw get notes "Opencode Auth JSON" > ~/.config/opencode/auth.json
```

These helper functions are included in `modules/secrets_bitwarden.sh`.

---

## 🧠 Tips

- Use GitHub Actions or any CI runner to automatically run Terraform and spin up ephemeral dev servers.
- Works perfectly with Tailscale or Cloudflare Zero Trust SSH for private access.
- To keep scripts updated, just `git pull` on the server or re-run the provisioning.

---

## 🧑‍💻 Example Workflow (Fully Automated)
1. Run GitHub Action → provisions a new Hetzner instance  
2. Cloud-init installs base tools and clones this repo  
3. SSH → `sudo /opt/dev-bootstrap/install.sh`  
4. Select tools → done in minutes  
5. Destroy when finished

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

Built and maintained by developers who like clean, reproducible server environments.
