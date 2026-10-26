# 🚀 Production-Grade Server Monitoring & Alerting System

Dokumentasi lengkap perancangan sistem pemantauan server (*Server Monitoring System*) mandiri berbasis Linux (Ubuntu/WSL) menggunakan ekosistem *Open-Source* standar industri (`roadmap.sh` framework). Sistem ini mencakup pengumpulan metrik, visualisasi grafis, manajemen aturan, hingga pengiriman pengingat otomatis langsung ke Telegram.

---

## 🏗️ Arsitektur & Tech Stack
* **Operating System:** Linux (Ubuntu / WSL)
* **Metrics Collector:** Prometheus & Node Exporter
* **Visualization:** Grafana (Dashboard ID: `1860`)
* **Alerting Engine:** Prometheus Alertmanager
* **Notification Channel:** Telegram Bot API (`MorazBotAlert`)

---

## 🛠️ Langkah-Langkah Implementasi Singkat

1. **Node Exporter & Prometheus Setup:**
   * Node Exporter berjalan di port `9100` untuk mengekspos metrik hardware.
   * Prometheus berjalan di port `9090` untuk *scraping* berkala menggunakan konfigurasi `/etc/prometheus/prometheus.yml`.
2. **Alert Rules Integration:**
   * Mengatur file `alert_rules.yml` untuk memantau status `up == 0` (Instance Down).
3. **Alertmanager & Telegram Webhook:**
   * Mengonfigurasi `/etc/alertmanager/alertmanager.yml` dengan *Bot Token* dan *Chat ID* untuk mengirim format pesan HTML interaktif ke Telegram.

---

## 📊 Hasil Pengujian
Sistem berhasil mendeteksi gangguan secara *real-time* saat layanan dimatikan secara simulasi (`sudo systemctl stop node_exporter`), dan bot Telegram secara otomatis mengirimkan pesan *critical alert* berformat HTML dengan akurat.
EOF

# Perintah untuk push ulang ke GitHub
git add README.md
git commit -m "docs: add full and comprehensive server monitoring documentation"
git push origin main --force
