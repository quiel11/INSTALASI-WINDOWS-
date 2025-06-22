# INSTALASI-WINDOWS-

# 🖥️ Panduan Instalasi Windows 10 dengan Docker

Panduan ini akan membantu Anda menjalankan Windows 10 dalam container Docker menggunakan image dockurr/windows.

---

## 📋 Persyaratan Sistem
- Docker dan Docker Compose terinstall
- Akses sudo/root
- Sistem mendukung virtualisasi (KVM)
- RAM minimal 16GB (disarankan)

---

## 🚀 Langkah-langkah Instalasi

### **Tahap 1: Membuat Direktori Data Docker**
```bash
sudo mkdir -p /tmp/docker-data
```
> 📁 Membuat direktori untuk menyimpan data Docker di `/tmp/docker-data`

---

### **Tahap 2: Konfigurasi Docker Daemon**
```bash
sudo nano /etc/docker/daemon.json
```

**Isi file `daemon.json`:**
```json
{
  "data-root": "/tmp/docker-data"
}
```
> ⚙️ Mengonfigurasi Docker untuk menggunakan direktori data custom

---

### **Tahap 3: Membuat File Docker Compose**
```bash
sudo nano windows10.yml
```

**Isi file `windows10.yml`:**
```yaml
version: '3.9'

services:
  windows:
    image: dockurr/windows
    container_name: windows
    environment:
      VERSION: "10"  
      USERNAME: ${WINDOWS_USERNAME:-admin}
      PASSWORD: ${WINDOWS_PASSWORD:}
      RAM_SIZE: "12G"  
      CPU_CORES: "4"
      DISK_SIZE: "32G"
      DISK2_SIZE: "16G"
    cap_add:
      - NET_ADMIN
    ports:
      - "8006:8006"    # Web interface
      - "3389:3389/tcp" # RDP TCP
      - "3389:3389/udp" # RDP UDP
    volumes:
      - /tmp/docker-data:/mnt/disco1
      - windows-data:/mnt/windows-data
    devices:
      - "/dev/kvm:/dev/kvm"
      - "/dev/net/tun:/dev/net/tun"
    stop_grace_period: 2m
    restart: unless-stopped  
    shm_size: "2g" 
    tty: true 
    stdin_open: true 

volumes:
  windows-data:
```

#### 📊 **Penjelasan Konfigurasi:**
- **RAM_SIZE**: Alokasi RAM untuk Windows (12GB)
- **CPU_CORES**: Jumlah core CPU yang dialokasikan (4 core)
- **DISK_SIZE**: Ukuran disk utama (32GB)
- **Port 8006**: Web interface untuk akses browser
- **Port 3389**: Remote Desktop Protocol (RDP)

---

### **Tahap 4: Konfigurasi Environment Variables**
```bash
sudo nano .env
```

**Isi file `.env`:**
```env
WINDOWS_USERNAME=isi_username_kalian
WINDOWS_PASSWORD=isi_password_kalian
```
> 🔐 **Penting:** Ganti dengan username dan password yang diinginkan untuk Windows

---

### **Tahap 5: Menjalankan Container**
```bash
docker-compose -f windows10.yml up
```
> ▶️ Untuk menjalankan di background, tambahkan flag `-d`

---

## ⚡ Tips dan Catatan

- **Proses Download**: Pertama kali menjalankan akan download image Windows (~4-6GB)
- **Waktu Boot**: Windows membutuhkan beberapa menit untuk boot pertama kali
- **Performance**: Pastikan sistem memiliki resource yang cukup
- **Firewall**: Pastikan port 8006 dan 3389 tidak diblokir

---

## 🛑 Menghentikan Container
```bash
docker-compose -f windows10.yml down
```

## 🔄 Restart Container
```bash
docker-compose -f windows10.yml restart
```

---

## ⚠️ Troubleshooting
- Jika gagal start, periksa apakah virtualisasi aktif: `lscpu | grep Virtualization`
- Pastikan user memiliki akses ke `/dev/kvm`
- Cek log container: `docker logs windows`
