# Analisis-Big-Data
Memahami bagaimana alur pemahaman dari analisi big data

Modul 1 ini intinya:

* Jalankan Linux di Windows (WSL2)
* Install Docker
* Jalankan container
* Simulasi object storage seperti cloud

---
# 🎯  **3.4 –Instalasi dan Setup Docker di WSL**.
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

# 🟡 BAGIAN 10 — ALUR DATA

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



# 🎯 ** 3.4 – Konfigurasi Storage Layer (MinIO)**.

> “Sekarang kita membangun Storage Layer.
> Artinya kita menyiapkan tempat penyimpanan data seperti Amazon S3, tapi berjalan di komputer kita sendiri.”

---

# 🟢 LANGKAH 1 — BUAT FOLDER PROJECT

Masih di dalam WSL (Ubuntu).

Ketik:

```bash
mkdir -p ~/bigdata-lab-1
cd ~/bigdata-lab-1
```

📌 Jelaskan:

* `mkdir` = membuat folder
* `-p` = buat jika belum ada
* `~` = home directory Linux

Sekarang kita punya folder kerja khusus praktikum.

---

# 🟢 LANGKAH 2 — BUAT STRUKTUR FOLDER

Masih di folder itu, buat struktur:

```bash
mkdir data raw-data
```

Strukturnya sekarang:

```
bigdata-lab-1/
|-- data/
|-- raw-data/
```

📌 Jelaskan:

* `data/` → akan jadi penyimpanan MinIO (persistent storage)
* `raw-data/` → tempat file mentah sebelum diupload

Tekankan:

> Folder data ini bukan cuma folder biasa. Nanti akan dipetakan ke container.

---

# 🟢 LANGKAH 3 — BUAT FILE docker-compose.yml

Ketik:

```bash
nano docker-compose.yml
```

Lalu isi dengan:

```yaml
version: '3.8'

services:
  minio:
    image: minio/minio:latest
    container_name: bigdata-minio
    ports:
      - "9000:9000"
      - "9001:9001"
    environment:
      MINIO_ROOT_USER: admin
      MINIO_ROOT_PASSWORD: admin123
    volumes:
      - ./data:/data
    command: server /data --console-address ":9001"
```

Simpan:
CTRL + O → Enter
Keluar:
CTRL + X

---

# 🧠 Cara Jelaskan Isi docker-compose.yml

### 🔹 image

`minio/minio:latest`
→ Kita menarik image resmi MinIO.

### 🔹 ports

* 9000 = S3 API
* 9001 = Web Console

Jelaskan:

> Port kiri = port di komputer kita
> Port kanan = port di dalam container

---

### 🔹 environment

```
MINIO_ROOT_USER
MINIO_ROOT_PASSWORD
```

Ini username dan password login.

---

### 🔹 volumes

```
./data:/data
```

Ini penting.

Artinya:

* Folder data di laptop
* Dipetakan ke folder /data di dalam container

Jelaskan:

> Kalau container mati, data tetap aman karena disimpan di folder luar container.

Ini konsep **persistent storage**.

---

# 🟢 LANGKAH 4 — JALANKAN MINIO

Sekarang jalankan:

```bash
docker compose up -d
```

Penjelasan:

* `up` = jalankan service
* `-d` = background mode

Cek apakah aktif:

```bash
docker ps
```

Pastikan ada container:
`bigdata-minio`

---

# 🟢 LANGKAH 5 — AKSES MINIO

Buka browser:

```
http://localhost:9001
```

Login:

* Username: admin
* Password: admin123

Kalau berhasil → Storage Layer sudah aktif.

---

# 🟢 LANGKAH 6 — BUAT BUCKET MEDALLION

Setelah login:

Klik **Create Bucket**

Buat 3 bucket:

* bronze
* silver
* gold

Sekarang struktur logical:

```
MinIO
|-- bronze/
|-- silver/
|-- gold/
```

📌 Jelaskan:

> Ini bukan folder biasa. Ini zona dalam Data Lake.

---

# 🧠 KONSEP 

## 1️⃣ Kita Baru Saja Membangun Storage Layer

Belum ada processing.

Belum ada Python.

Belum ada transformasi.

Ini murni layer penyimpanan.

---

## 2️⃣ Ini Simulasi Amazon S3

MinIO = object storage S3-compatible.

Artinya nanti compute akan mengakses lewat API, bukan folder biasa.

---

## 3️⃣ Separation of Storage and Compute

Storage berdiri sendiri.

Nanti compute akan berjalan di container berbeda.

---


# 🎯 Ringkasan Alur 3.4

1. Buat folder project
2. Buat folder data & raw-data
3. Buat docker-compose.yml
4. Jalankan docker compose
5. Cek container aktif
6. Akses MinIO
7. Buat bucket bronze/silver/gold

---

# 🟢 ALUR 3.5 — SIMULASI INGESTION KE BRONZE

---

# 🔹 LANGKAH 1 — Pastikan Masih di WSL

Kita masih di terminal Ubuntu (WSL) yang tadi.

Masuk ke folder project:

```bash
cd ~/bigdata-lab-1
```

Kalau folder `raw-data` belum ada:

```bash
mkdir -p raw-data
cd raw-data
```

📌 Jelaskan:

> Folder raw-data ini hanya staging area sebelum diupload ke storage.

---

# 🔹 LANGKAH 2 — Buat File sample.csv

Di dalam `raw-data`:

```bash
nano sample.csv
```

Isi:

```csv
id,name,age,city
1,Andi,21,Bandar Lampung
2,Budi,23,Jakarta
3,Siti,,Surabaya
4,Rina,22,Bandung
```

Simpan (CTRL+O → Enter → CTRL+X).

📌 Jelaskan:

* Data ini sengaja ada missing value (umur Siti kosong).
* Ini untuk simulasi bahwa Bronze belum dibersihkan.

Tekankan:

> Bronze menerima data apa adanya.

---

# 🔹 LANGKAH 3 — Upload ke Bronze

Sekarang kita pindah ke browser.

Buka:

```
http://localhost:9001
```

Login → masuk bucket `bronze`.

Klik **Upload** → pilih `sample.csv`.

Selesai.

---

# 🧠 Alternatif (Opsional – CLI)

Kalau mau lebih advanced:

Masuk container:

```bash
docker exec -it bigdata-minio bash
```

Tapi untuk pemula, Web Console lebih mudah.


---

# 🔹 LANGKAH 5 — (Opsional) Simulasi Versi Berbasis Waktu
Buat folder partisi waktu di Bronze:

Contoh di UI:

```
bronze/2025/07/
```

Upload sebagai:

```
bronze/2025/07/sample_20250701.csv
```

# 🔹 LANGKAH 6 — Verifikasi Ingestion

Pastikan file ada di Bronze.

Cek lewat:

* Web Console
* Atau nanti via Python (S3 API)

Kalau file terlihat → ingestion selesai.

---

Ini bukan sekadar upload file.

Ini simulasi:

Batch ingestion ke Data Lake.

Bedakan ini dengan:

* Database insert
* File system biasa

Di sini kita sedang membangun fondasi Data Lake.

---
# 🎯 Transisi ke Tahap Berikutnya
> “Sekarang kita sudah punya data mentah di Bronze.
> Tahap berikutnya adalah Compute Layer membaca Bronze dan memprosesnya ke Silver.”

---

# 🟢 ALUR 3.6 — MENAMBAHKAN CONTAINER COMPUTE

---

# 🔹 LANGKAH 1 — Kembali ke Folder Project

Masih di WSL.

```bash
cd ~/bigdata-lab-1
```

Pastikan kamu melihat:

```
docker-compose.yml
data/
raw-data/
```

---

# 🔹 LANGKAH 2 — Edit docker-compose.yml

Buka file:

```bash
nano docker-compose.yml
```

Tambahkan service baru di bawah `minio`:

```yaml
compute:
  image: python:3.11-slim
  container_name: bigdata-compute
  volumes:
    - ./app:/app
  working_dir: /app
  depends_on:
    - minio
  tty: true
```

Strukturnya sekarang:

```
services:
  minio:
    ...
  compute:
    ...
```

Simpan dan keluar.

---

## 🧠 Service Compute

maknanya:

* `image: python` → ini engine pemrosesan
* `depends_on: minio` → compute baru jalan setelah storage ada
* `volumes ./app:/app` → kode kita di luar container, tapi dijalankan di dalam container

Tekankan:

> Ini adalah simulasi ETL engine seperti Spark atau Dataproc.

---

# 🔹 LANGKAH 3 — Buat Folder Aplikasi Compute

Masih di folder project:

```bash
mkdir app
cd app
```

Sekarang kita berada di:

```
bigdata-lab-1/app
```

---

# 🔹 LANGKAH 4 — Buat requirements.txt

```bash
nano requirements.txt
```

Isi:

```
boto3
pandas
pyarrow
```

Simpan.

Jelaskan:

* boto3 → koneksi S3
* pandas → transformasi data
* pyarrow → simpan ke Parquet

---

# 🔹 LANGKAH 5 — Buat transform.py

```bash
nano transform.py
```

Isi sesuai modul.
Perhatikan bagian ini:

```python
endpoint_url='http://minio:9000'
```

Bukan localhost.

Kenapa?

Karena compute dan minio berada dalam jaringan Docker yang sama.

Nama service = hostname internal.

Ini konsep networking container.

---

# 🔹 LANGKAH 6 — Restart Docker Compose

Kembali ke root project:

```bash
cd ..
```

Lalu jalankan ulang:

```bash
docker compose up -d
```

Sekarang ada dua container:

* bigdata-minio
* bigdata-compute

Cek:

```bash
docker ps
```

---

# 🔹 LANGKAH 7 — Masuk ke Container Compute

```bash
docker exec -it bigdata-compute bash
```

Sekarang kamu berada di dalam container compute.

Tampilannya akan seperti:

```
root@container-id:/app#
```

---

# 🔹 LANGKAH 8 — Install Dependency

Di dalam container:

```bash
pip install -r requirements.txt
```

Tunggu sampai selesai.

---

# 🔹 LANGKAH 9 — Simulasi Transformasi

Pastikan:
File sample.csv sudah ada di bucket bronze.

Jalankan:

```bash
python transform.py
```

Kalau berhasil muncul:

```
Transformasi selesai: Bronze ke Silver
```

---

# 🔹 LANGKAH 10 — Verifikasi Silver

Keluar container:

```bash
exit
```

Buka browser → MinIO → bucket silver.

Harus muncul:

```
sample_clean.parquet
```

---

# 🧠 Apa yang Sebenarnya Terjadi Secara Arsitektural?

Alur internalnya:

1. Compute membaca Bronze via S3 API
2. Data dibersihkan (dropna)
3. Ditambahkan kolom processed_at
4. Disimpan sebagai Parquet ke Silver
5. Bronze tidak disentuh

Itu separation of concern.

---

# 🎯 Gambaran Besar Sekarang

Kalau kamu jelaskan dari awal sampai sini, sistemnya sudah seperti ini:

```
User → Upload raw data → Bronze (MinIO)
Compute container → Read Bronze → Transform → Silver
```

Kamu sudah berhasil mensimulasikan:

S3 + ETL Engine

---

# 🔥 Hal Penting 
Ini bukan sekadar Python script.

Ini arsitektur:

* Storage layer berdiri sendiri
* Compute layer berdiri sendiri
* Keduanya terhubung lewat API

Itu pola Big Data modern.

---

# 🟢 ALUR 3.7 — MEMBANGUN GOLD LAYER

---

# 🔹 LANGKAH 1 — Masuk ke Folder app

Masih di WSL:

```bash id="mbp4xz"
cd ~/bigdata-lab-1/app
```

---

# 🔹 LANGKAH 2 — Buat File aggregate.py

```bash id="a2fph6"
nano aggregate.py
```

Isi dengan kode dari modul (agregasi rata-rata umur per kota).

Simpan.
---

## 🔍 Perhatikan Bagian Penting Ini

### 🔹 Membaca Silver

```python
Bucket='silver'
Key='sample_clean.parquet'
```

Artinya:
Compute tidak baca file lokal.
Compute baca object storage via API.

Itu penting secara arsitektur.

---

### 🔹 Agregasi

```python
groupby('city')
```

Ini mengubah cleaned data menjadi summarized data.

Silver = record-level
Gold = summary-level

---

### 🔹 Simpan ke Gold

```python
Bucket='gold'
Key='city_summary.parquet'
```

Bronze tetap utuh
Silver tetap utuh
Gold adalah turunan

Ini konsep lineage.

---

# 🔹 LANGKAH 3 — Masuk ke Container Compute

Kembali ke root project:

```bash id="2bsk8y"
cd ..
```

Masuk container:

```bash id="n1kwye"
docker exec -it bigdata-compute bash
```

---

# 🔹 LANGKAH 4 — Jalankan Agregasi

Di dalam container:

```bash id="3u7yzk"
python aggregate.py
```

Kalau berhasil akan muncul:

```
Transformasi selesai: Silver ke Gold
```

---

# 🔹 LANGKAH 5 — Verifikasi Gold

Keluar container:

```bash id="6mbk4u"
exit
```

Buka browser → MinIO → bucket `gold`.

Harus ada:

```
city_summary.parquet
```

Kalau file muncul → pipeline lengkap berhasil.

---

# 🧭 SEKARANG ALUR END-TO-END SUDAH LENGKAP

Kalau kamu jelaskan dari awal sampai sini, urutannya:

1. Install WSL & Docker
2. Jalankan MinIO (Storage Layer)
3. Buat bucket bronze/silver/gold
4. Upload raw data ke Bronze
5. Compute transform Bronze → Silver
6. Compute agregasi Silver → Gold

Itu sudah full Medallion Architecture.

---

# 🧠 MAKNA ARSITEKTURAL 


Tekankan ini:

## 🔹 Bronze

Raw data
Tidak diubah
Sumber kebenaran

## 🔹 Silver

Cleaned
Terstruktur
Siap analitik

## 🔹 Gold

Diringkas
Ringan
Siap dashboard

---

# 🔥 Kenapa Gold Lebih Kecil dan Lebih Cepat?

Karena:

* Sudah di-aggregate
* Jumlah baris lebih sedikit
* Tidak perlu transformasi ulang

Itu sebabnya BI tool membaca Gold, bukan Bronze.

---

# 🎯 Gambaran Arsitektur Final

Secara konseptual sistemnya sekarang:

```
User → Bronze (Raw)
Compute → Silver (Clean)
Compute → Gold (Aggregated)
```

Storage tetap MinIO.
Compute tetap container Python.
Komunikasi lewat S3 API.

Itu pola Big Data modern.

---
# 🎯 Tujuan Latihan Ini

> “Sekarang kita tidak lagi pakai sample kecil.
> Kita gunakan dataset publik nyata dan menerapkan arsitektur yang sama.”

Artinya:

* Arsitektur tetap
* Dataset berubah
* Skala lebih realistis

---

# 🔁 ALUR LENGKAP LATIHAN (End-to-End)

---

# 🟢 LANGKAH 1 — Upload ke Bronze

Tetap sama seperti sebelumnya.

Buka MinIO → bucket bronze → upload file:

```
happiness_raw.csv
```

Struktur:

```
bronze/
└── happiness_raw.csv
```

📌 Jelaskan ke praktikan:

Bronze = data mentah
Tidak boleh diubah
Kalau salah → upload versi baru

---

# 🟡 LANGKAH 2 — Transformasi ke Silver

Masuk ke folder:

```bash
cd ~/bigdata-lab-1/app
```

Edit atau buat transform.py untuk dataset ini.

---

## 🔹 Apa yang Dilakukan di Silver?

Script harus:

1. Membaca CSV dari Bronze
2. Standarisasi kolom (lowercase, strip)
3. Drop missing value
4. Tambah processed_at
5. Simpan sebagai Parquet ke Silver

Contoh inti transformasi:

```python
df.columns = df.columns.str.lower().str.strip()
df_clean = df.dropna()
df_clean['processed_at'] = pd.Timestamp.now()
```

Output:

```
silver/
└── happiness_clean.parquet
```


# 🟠 LANGKAH 3 — Agregasi ke Gold

Buat file:

```bash
nano aggregate.py
```

Agregasi:

```python
df_gold = (
    df.groupby('country')
      .agg(
          avg_happiness=('life_ladder','mean'),
          avg_gdp=('gdp_per_capita','mean')
      )
      .reset_index()
)
```

Simpan ke:

```
gold/
└── country_summary.parquet
```

---

# 🧠 Apa yang Terjadi Secara Konseptual?

Bronze:
Semua data mentah lintas tahun

Silver:
Data bersih lintas tahun

Gold:
Ringkasan per negara

Jumlah baris Gold jauh lebih kecil daripada Bronze.

Itu tujuan Gold.

---

# 🎯 Penjelasan Arsitektural yang Harus Kamu Tekankan

Pipeline sekarang:

```
Bronze (raw multi-year data)
   ↓
Silver (clean dataset)
   ↓
Gold (aggregated country-level summary)
```

Storage tetap MinIO
Compute tetap container Python
Komunikasi tetap S3 API

Tidak ada perubahan arsitektur.

---

