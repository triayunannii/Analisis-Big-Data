# Analisis-Big-Data
Memahami bagaimana alur pemahaman dari analisi big data
Modul ini intinya:

* Jalankan Linux di Windows (WSL2)
* Install Docker
* Jalankan container
* Simulasi object storage seperti cloud

---

# 🔵 BAGIAN 1 — CEK DAN SIAPKAN WSL2

## 🔹 Langkah 1: Cek WSL Aktif

Buka **PowerShell atau CMD (Windows)**

Ketik:

```bash
wsl --status
```

Lalu:

```bash
wsl --list --verbose
```

Pastikan:

* Versinya **Version 2**
* Ubuntu terpasang

📌 Cara jelasin ke praktikan:

> “WSL itu membuat Windows bisa menjalankan Linux.
> Kita butuh Linux karena Docker berjalan lebih stabil di sana.”

---

# 🔵 BAGIAN 2 — MASUK KE LINUX (Ubuntu)

Di terminal Windows, ketik:

```bash
wsl
```

Sekarang kamu sudah masuk ke Ubuntu.

Tampilan akan berubah seperti:

```
username@DESKTOP:~$
```

📌 Jelaskan:

> “Sekarang kita berada di lingkungan Linux, bukan Windows lagi.”

---

# 🔵 BAGIAN 3 — UPDATE SISTEM

Jalankan:

```bash
sudo apt update && sudo apt upgrade -y
```

Tujuan:
Memastikan sistem up-to-date.

📌 Jelaskan:

> “Ini seperti update aplikasi sebelum install software baru.”

---

# 🔵 BAGIAN 4 — INSTALL DOCKER DI WSL

## 1️⃣ Install dependency

```bash
sudo apt install ca-certificates curl gnupg lsb-release -y
```

## 2️⃣ Tambahkan GPG Key Docker

```bash
sudo mkdir -p /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

## 3️⃣ Tambahkan repository Docker

```bash
echo \
"deb [arch=$(dpkg --print-architecture) \
signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$(lsb_release -cs) stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

## 4️⃣ Install Docker Engine

```bash
sudo apt update
sudo apt install docker-ce docker-ce-cli \
containerd.io docker-buildx-plugin docker-compose-plugin -y
```

📌 Cara jelasin ke pemula:

> “Kita sedang memasang mesin yang bisa menjalankan container.
> Container itu seperti mini server kecil di dalam komputer kita.”

---

# 🔵 BAGIAN 5 — AGAR DOCKER BISA JALAN TANPA SUDO

Jalankan:

```bash
sudo usermod -aG docker $USER
```

Lalu:

```bash
newgrp docker
```

📌 Jelaskan:

> “Supaya nanti kita tidak perlu ketik sudo setiap menjalankan Docker.”

---

# 🔵 BAGIAN 6 — VERIFIKASI DOCKER

Cek versi:

```bash
docker --version
```

Cek compose:

```bash
docker compose version
```

Test container:

```bash
docker run hello-world
```

Kalau muncul pesan sukses → Docker siap.

📌 Jelaskan:

> “Kalau hello-world berhasil, artinya Docker sudah benar-benar berjalan.”

---

# 🔵 BAGIAN 7 — (OPSIONAL) ATUR RESOURCE WSL

Kalau RAM kecil, perlu atur.

Buat file di Windows:

```
C:\Users\namauser\.wslconfig
```

Isi:

```
[wsl2]
memory=6GB
processors=4
```

Lalu restart WSL:

```bash
wsl --shutdown
```

📌 Jelaskan:

> “Ini supaya Docker tidak memakan seluruh RAM komputer.”

---

# 🟢 BAGIAN 8 — MENJALANKAN DOCKER COMPOSE

Masuk ke folder yang ada file `docker-compose.yml`

Contoh:

```bash
cd bigdata-lab
```

Jalankan:

```bash
docker compose up -d
```

Cek container:

```bash
docker ps
```

Pastikan:

* MinIO container aktif

📌 Jelaskan:

> “Sekarang kita menyalakan storage layer dan compute layer.”

---

# 🟢 BAGIAN 9 — AKSES MINIO

Buka browser:

```
http://localhost:9001
```

Login sesuai username & password di docker-compose.

Buat bucket:

```
bigdata-lab
```

Buat struktur:

```
bronze/
silver/
gold/
```

📌 Jelaskan:

> “Ini simulasi Data Lake sederhana.”

---

# 🟡 BAGIAN 10 — ALUR DATA (Konsep yang Harus Kamu Tekankan)

Alurnya:

1. Upload data mentah → bronze
2. Compute baca dari bronze
3. Transformasi
4. Simpan ke silver
5. Agregasi
6. Simpan ke gold

Jelaskan ini sebagai:

Storage dan compute terpisah.

---

# 🔴 BAGIAN 11 — MATIKAN CONTAINER

Setelah selesai:

```bash
docker compose down
```

📌 Jelaskan:

> “Container bisa dimatikan dan dinyalakan kembali kapan saja. Konfigurasinya tetap sama.”

---
