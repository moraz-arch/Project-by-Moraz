# 🚀 Production-Grade Server Monitoring & Alerting System

Dokumentasi komprehensif perancangan dan implementasi sistem pemantauan server (*Server Monitoring System*) mandiri berbasis Linux (Ubuntu / WSL) menggunakan standar industri (`roadmap.sh` framework). Proyek ini dibangun secara bertahap mulai dari custom Bash script, penerapan infrastruktur *open-source* enterprise (`Prometheus`, `Node Exporter`, `Grafana`, `Alertmanager`), hingga otomatisasi pengiriman peringatan instan (*real-time alerting*) ke Telegram Bot.

---

## 🏗️ 1. Arsitektur & Tech Stack
* **Operating System:** Linux (Ubuntu / WSL)
* **Initial Tooling:** Custom Bash Scripting (`df`, `free`, `top`)
* **Metrics Collector:** Prometheus & Node Exporter
* **Visualization:** Grafana (Dashboard ID: `1860`)
* **Alerting Engine:** Prometheus Alertmanager
* **Notification Gateway:** Telegram Bot API (`MorazBotAlert`)

---

## 🛠️ 2. Tahapan Implementasi Lengkap

### Langkah A: Pembuatan Custom Bash Script untuk Resource Monitoring
    Sebelum menggunakan ekosistem enterprise, sistem dibangun menggunakan skrip Bash untuk memantau penggunaan sumber daya secara langsung melalui terminal.
1. Buat file skrip:
   ```bash
   nano monitor.sh
   
2. Masukkan logika pengecekan resource:
   #!/bin/bash
   echo "=== SYSTEM RESOURCE MONITOR ==="
   echo "Date: $(date)"
   echo "---------------------------------"
   echo "[Disk Usage]:"
   df -h / | awk 'NR==2 {print "Used: " $5 " (" $3 " / " $2 ")"}'
   echo ""
   echo "[Memory Usage]:"
   free -m | awk 'NR==2 {printf "Used: %.2f%% (%sMB / %sMB)\n", $3*100/$2, $3, $2}'
   echo "================================-"
   
4. Berikan izin eksekusi dan jalankan:
   chmod +x monitor.sh
   ./monitor.sh

### Langkah B: Instalasi Node Exporter & Prometheus
    Untuk skala produksi, metrik server dieksport menggunakan Prometheus dan Node Exporter
    
    1. Setup Node Exporter (Port 9100):
     Unduh dan pasang Node Exporter agar metrik OS terekspos

     wget [https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz]                                                 (https://github.com/prometheus/node_exporter/releases/download/v1.5.0/node_exporter-1.5.0.linux-amd64.tar.gz)
     tar xvf node_exporter-*.tar.gz
     sudo cp node_exporter-*.linux-amd64/node_exporter /usr/local/bin/

    2. Setup Prometheus (Port 9090):
    Unduh dan konfigurasi Prometheus untuk melakukan scraping data secara berkala

   wget [https://github.com/prometheus/prometheus/releases/download/v2.42.0/prometheus-2.42.0.linux-amd64.tar.gz]                                                     (https://github.com/prometheus/prometheus/releases/download/v2.42.0/prometheus-2.42.0.linux-amd64.tar.gz)
   tar xvf prometheus-*.tar.gz
   sudo cp prometheus-*.linux-amd64/prometheus /usr/local/bin/
   sudo cp prometheus-*.linux-amd64/promtool /usr/local/bin/

3. Konfigurasi Utama (/etc/prometheus/prometheus.yml):

   global:
     scrape_interval: 15s

   alerting:
      alertmanagers:
        - static_configs:
            - targets: ['localhost:9093']

   rule_files:
     - 'alert_rules.yml'

   scrape_configs:
     - job_name: 'prometheus'
       static_configs:
         - targets: ['localhost:9090']

   - job_name: 'node_exporter'
     static_configs:
       - targets: ['localhost:9100']

### Langkah C: Manajemen Systemd Service & Keamanan (Non-Root Users)
    Demi keamanan sistem, seluruh layanan dijalankan di bawah service user khusus (bukan sebagai root).
