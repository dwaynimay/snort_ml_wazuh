# konfigurasi ssh

```bash
sudo apt update
```

```bash
sudo apt install -y openssh-server
```

```bash
sudo systemctl enable ssh
```

```bash
sudo systemctl start ssh
```

```bash
sudo systemctl status ssh
```

```bash
ip a
```

### menggunakan ssh

```bash
ssh user@ip_address
```

### kalo fingerprint bermaslah

```bash
ssh-keygen -R ip_address
```

# konfigurasi docker

### Prasyarat

```bash
sudo apt-get update
```

```bash
sudo apt-get install -y ca-certificates curl gnupg lsb-release
```

### Tambah repo resmi Docker

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

```bash
curl -fsSL https://download.docker.com/linux/$(. /etc/os-release; echo "$ID")/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
  https://download.docker.com/linux/$(. /etc/os-release; echo "$ID") \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

### Install Docker Engine + CLI + containerd + Compose v2 plugin

```bash
sudo apt-get update
```

```bash
sudo apt-get install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

### Enable & start

```bash
sudo systemctl enable --now docker
```

### jalan tanpa sudo

```bash
sudo usermod -aG docker $USER
```

```bash
newgrp docker
```

```bash
docker run --rm hello-world
docker compose version
```

# konfigurasi snort

```bash
cd ~/snort_ml_wazuh/snort
```

```bash
docker compose build
```

```bash
docker compose up -d
```

```bash
docker exec -it snort3 snort -V
```

# konfigurasi wazuh

### tingkatkan limit VMA

```bash
cd ~/snort_ml_wazuh
```

```bash
sudo sysctl -w vm.max_map_count=262144
```

```bash
git clone https://github.com/wazuh/wazuh-docker.git -b v4.14.0
```

```bash
cd wazuh-docker/single-node
```

```bash
docker compose -f generate-indexer-certs.yml run --rm generator
```

### sertifikat akan muncul di: ./config/wazuh_indexer_ssl_certs/

```bash
docker compose up -d
```

### cek status

```bash
docker compose ps
docker compose logs -f
```

### cek dashboard

https://ip_address

### username password default

| Username | Password       |
| -------- | -------------- |
| admin    | SecretPassword |

#### lihat storage yang terpakai

```bash
df -h
```

#### lihat memori yang terpakai

```bash
free -h
```

| Kondisi                         | Filesystem | Size | Used | Avail | Use% | Mounted on     |
| ------------------------------- | ---------- | ---- | ---- | ----- | ---- | -------------- |
| **Setelah instal SSH & Docker** | tmpfs      | 776M | 1.2M | 775M  | 1%   | /run           |
|                                 | /dev/sda2  | 69G  | 3.7G | 62G   | 6%   | /              |
|                                 | tmpfs      | 3.8G | 0    | 3.8G  | 0%   | /dev/shm       |
|                                 | tmpfs      | 5.0M | 0    | 5.0M  | 0%   | /run/lock      |
|                                 | tmpfs      | 776M | 4.0K | 776M  | 1%   | /run/user/1000 |
| **Setelah instal Wazuh**        | tmpfs      | 776M | 1.4M | 775M  | 1%   | /run           |
|                                 | /dev/sda2  | 69G  | 18G  | 48G   | 27%  | /              |
|                                 | tmpfs      | 3.8G | 0    | 3.8G  | 0%   | /dev/shm       |
|                                 | tmpfs      | 5.0M | 0    | 5.0M  | 0%   | /run/lock      |
|                                 | tmpfs      | 776M | 4.0K | 776M  | 1%   | /run/user/1000 |

topologi
Router (DHCP LAN)
├─ Attacker (laptop) 192.168.1.50
└─ VM Lab (bridged) 192.168.1.10 <-- menjalankan Docker: - Snort (network_mode: host, NFQUEUE) - DVWA (target uji) -> dipublish: host:8081 -> container:80 - Wazuh (manager) -> mount /opt/snort/log

## belom dicoba

### ganti eth0 dengan nama NIC bridged kamu

sudo iptables -I INPUT -i eth0 -p tcp --dport 8081 -j NFQUEUE --queue-num 0
