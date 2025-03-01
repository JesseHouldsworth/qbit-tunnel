# qbit-tunnel 🚀  
A **Kubernetes deployment** for **qBittorrent**, secured with **PIA VPN** and optional **Tailscale** access. This setup ensures all BitTorrent traffic is forced through the VPN, while still allowing secure access via Tailscale.

## 🎯 Features
- ✅ **qBittorrent** – Popular BitTorrent client
- ✅ **PIA VPN** – Secure traffic with Private Internet Access
- ✅ **Kill Switch & Firewall** – Blocks non-VPN traffic
- ✅ **Tailscale (Optional)** – Private remote access
- ✅ **Persistent Storage** – Keeps configurations and downloads
- ✅ **Kubernetes Deployment** – Easy to manage and scale

## 🛠 Installation

### 1️⃣ Clone the Repository
git clone https://github.com/YOUR_GITHUB_USERNAME/qbittorrent-vpn-k8s.git  
cd qbittorrent-vpn-k8s

### 2️⃣ Create Kubernetes Namespace
kubectl create namespace vpn

### 3️⃣ Create Secrets for PIA VPN & Tailscale
kubectl create secret generic pia-vpn-credentials -n vpn --from-literal=USER='your_pia_username' --from-literal=PASS='your_pia_password'  
kubectl create secret generic tailscale-auth -n vpn --from-literal=TAILSCALE_AUTH_KEY='your_tailscale_auth_key'

### 4️⃣ Deploy qBittorrent + PIA VPN
kubectl apply -f qbittorrent.yaml

### 5️⃣ Verify Deployment
kubectl get pods -n vpn

## 🌐 Accessing qBittorrent Web UI
1. **Via LAN**:  
   http://<kubernetes_node_ip>:8080  
2. **Via Tailscale (Recommended)**:  
   http://<tailscale_device_ip>:8080  

_Default credentials (change after login)_:  
• **Username**: admin  
• **Password**: adminadmin  

## ⚙️ Configuration

### Environment Variables
| Variable                 | Default Value      | Description                                   |
|--------------------------|--------------------|-----------------------------------------------|
| LOC                      | us_california      | PIA server location                           |
| PORT_FORWARDING          | 1                  | Enables PIA port forwarding                  |
| KILLSWITCH               | 1                  | Blocks non-VPN traffic                       |
| FIREWALL                 | 1                  | Adds firewall rules to prevent leaks         |
| ALLOWED_INBOUND_PORTS    | 8080,6881          | Ports for WebUI & BitTorrent traffic         |

### Persistent Storage
| Volume Name     | Path (Inside Container)      | Description                                   |
|-----------------|------------------------------|-----------------------------------------------|
| config          | /config                      | Stores qBittorrent settings                  |
| downloads       | /downloads                   | Storage for downloaded torrents              |
| tailscale-state | /var/lib/tailscale           | Persists Tailscale state                     |

## 🛠 Troubleshooting

### Check Pod Logs
kubectl logs -n vpn -l app=qbittorrent-vpn

### Verify VPN Connectivity
kubectl exec -n vpn -it $(kubectl get pod -n vpn -l app=qbittorrent-vpn -o jsonpath="{.items[0].metadata.name}") -- curl -s https://ifconfig.me

• If the output matches PIA’s IP, VPN is working.  
• If it shows your real IP, traffic is leaking.

### Sonarr/Radarr Not Importing Downloads?
• Ensure Remote Path Mapping is set correctly in Sonarr/Radarr:  
  Remote Path: /downloads  
  Local Path: /mnt/external-drive/downloads  
• Check Sonarr logs under “System → Logs”

### Restart qBittorrent
kubectl delete pod -n vpn $(kubectl get pod -n vpn -l app=qbittorrent-vpn -o jsonpath="{.items[0].metadata.name}")

## 📜 License
This project is licensed under the **MIT License**.

### ⭐ Enjoy secure torrenting with qBittorrent-VPN-K8s! ⭐
