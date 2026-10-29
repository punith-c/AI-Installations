# 🤖 Local Autonomous Pentesting Setup: Strix + Ollama
### Fully Free, Air-Gapped, and Privacy-Focused Security Assessments

This guide provides the complete setup for **Strix**, an open-source autonomous AI penetration testing agent framework. By routing it through **Ollama** running locally on your machine, you can run security scans with **zero cost, zero tokens used, and complete data privacy**.

Given your **12 GB RAM allocation**, this setup uses an optimized **3-Billion Parameter (3B)** model to ensure powerful code analysis without freezing your system.

---

## 🛠️ Step 1: System Dependencies & Docker Privileges
Strix executes its offensive security tools (like scanners and payload builders) inside isolated Docker containers to protect your host OS. We need to install system utilities and give your user account permission to use Docker without needing `sudo`.

```bash
# Update system package repositories
sudo apt update

# Install pipx (isolated Python environment manager), jq, and curl
sudo apt install -y pipx jq curl

# Add pipx binaries to your system environment paths
pipx ensurepath

# Create the docker user group (if it doesn't already exist)
sudo groupadd docker

# Add your current user to the docker group to bypass sudo restrictions
sudo usermod -aG docker \$USER

# Apply the new group permissions to this terminal session immediately
newgrp docker
```

---

## 📦 Step 2: Install Strix Framework & Ollama Daemon
Next, we download the Strix core execution files and install the local AI runtime engine.

```bash
# Install the Strix engine cleanly inside an isolated pipx environment
pipx install strix-agent

# Download and execute the official Ollama local AI installation script
curl -fsSL https://ollama.com | sh

# Pull the lightweight, high-performance coding model optimized for 12GB RAM
ollama pull qwen2.5-coder:3b
```

---

## 🌐 Step 3: Configure Network Bridging & AI Paths
Because Strix runs inside a Docker sandbox, it cannot find Ollama at `localhost` or `127.0.0.1` (which would point inside the container itself). We must configure Ollama to accept incoming connections via the virtual network gateway bridge (`172.17.0.1`) and permanently save these configurations.

```bash
# Create an override folder for the Ollama system service
sudo mkdir -p /etc/systemd/system/ollama.service.d/

# Force Ollama to bind globally (0.0.0.0) so Docker containers can communicate with it
echo -e "[Service]\nEnvironment=\"OLLAMA_HOST=0.0.0.0\"" | sudo tee /etc/systemd/system/ollama.service.d/override.conf

# Reload the system service manager and restart Ollama to apply the network changes
sudo systemctl daemon-reload
sudo systemctl restart ollama

# Save the local AI endpoint paths to your bash profile so they load automatically
echo 'export STRIX_LLM="ollama/qwen2.5-coder:3b"' >> ~/.bashrc
echo 'export LLM_API_BASE="http://172.17.0.1:11434"' >> ~/.bashrc
echo 'export LLM_API_KEY="ollama"' >> ~/.bashrc

# Reload your terminal configuration profile to apply the variables right now
source ~/.bashrc
```

---

## 🚀 Step 4: Verification & Execution

### 1. Run Sanity Checks
Ensure your environment variables are configured correctly and the network link is open:
```bash
# Check if Docker permissions are working natively
docker info > /dev/null && echo "Docker Link: SUCCESS"

# Check if Ollama is accessible through the virtual bridge network gateway
curl -s http://172.17.0 > /dev/null && echo "AI Bridge Link: SUCCESS"
```

### 2. Run Your Scan
Create an isolated workspace directory to store target evidence and launch the autonomous agent team:
```bash
# Create and navigate to your clean security workspace
mkdir -p ~/SecurityWork/strix_workspace && cd ~/SecurityWork/strix_workspace

# Clear any corrupted cache files from previous connection attempts
rm -f ~/.strix/cli-config.json 2>/dev/null

# Initiate the scan loop (Replace with your explicitly authorized target URL or directory)
strix run --target https://abcd.in
```

### 3. Open the Visual Dashboard
Strix runs quietly in the background. To view a real-time terminal user interface showing what the sub-agents are thinking, what code they are executing, and what vulnerabilities they are uncovering, open a **second terminal window** and run:
```bash
cd ~/SecurityWork/strix_workspace
strix view
```

*(Note: If your system begins lagging due to the 12 GB RAM limitation, you can immediately cancel the active reasoning loop by pressing `Ctrl + C` in the main scanning window.)*
