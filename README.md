# 🌍 GEOPORTAL DESA NGUWET - Spatial Intelligence System

[![MIT License](https://img.shields.io/badge/License-MIT-green.svg)](https://choosealicense.com/licenses/mit/)
[![Python 3.10](https://img.shields.io/badge/Python-3.10-blue?logo=python&logoColor=white)](https://www.python.org/)
[![Flask](https://img.shields.io/badge/Backend-Flask-000000?logo=flask&logoColor=white)](https://flask.palletsprojects.com/)
[![MapLibre GL JS](https://img.shields.io/badge/Frontend-MapLibre_GL_JS-396CB2?style=flat&logo=maplibre&logoColor=white)](https://maplibre.org/)
[![Hugging Face](https://img.shields.io/badge/Compute-Hugging_Face-FFD21E?logo=huggingface&logoColor=black)](https://huggingface.co/)
[![GitHub Pages](https://img.shields.io/badge/Hosting-GitHub_Pages-222222?logo=github&logoColor=white)](https://pages.github.com/)

**Geoportal Desa Nguwet** adalah platform WebGIS interaktif yang dirancang sebagai instrumen pendukung keputusan (*Decision Support System*) untuk tata ruang desa. Sistem ini mengintegrasikan data vektor dan raster multisektoral, dilengkapi mesin analisis spasial otomatis di sisi server, serta asisten cerdas berbasis *Generative AI*.

Dikembangkan oleh **Kelompok 1 - Infrastruktur Data Spasial**, Magister Teknik Geomatika, Universitas Gadjah Mada.

---

## ✨ Fitur Utama

### 🗺️ **Visualisasi Peta 3D Interaktif**
- **LoD 1 Building Model**: Visualisasi bangunan 3D berdasarkan atribut ketinggian (*Z_NDSM*).
- **Multi-Source Layer**: Integrasi Ortofoto resolusi tinggi, DTM (*Digital Terrain Model*), dan data vektor (Jalan, Sungai, Persil).
- **Smart Interaction**: Popup informasi muncul otomatis saat kursor diarahkan (*Hover*) dan diklik.

### 📊 **Toolbox Analisis Spasial (Server-Side)**
1.  **Analisis Skor Lokasi**: Algoritma cerdas untuk menilai nilai strategis lahan berdasarkan aksesibilitas jalan dan kepadatan lingkungan.
2.  **Status Pemanfaatan Lahan**: Perhitungan otomatis rasio keterbangunan (*Building Coverage Ratio*) pada setiap bidang tanah.
3.  **Cek Sempadan Sungai**: Deteksi pelanggaran bangunan di zona sempadan sungai (Buffer 15m) sesuai **Permen PUPR No. 28/2015**.

### 🤖 **Asisten Cerdas AI (Chatbot)**
- **Powered by Gemini 2.5 Flash**: Menjawab pertanyaan *natural language* seputar data desa.
- **Context-Aware**: AI dibekali data statistik *real-time* dari database spasial desa (Jumlah bangunan, luas lahan, dll).
- **Edukatif**: Mampu menjelaskan regulasi tata ruang kepada warga awam.

### 🛡️ **Manajemen Layer Non-Destruktif**
- **Layer Terpisah**: Hasil analisis (misal: bangunan melanggar) muncul sebagai layer baru di atas data asli.
- **Toggle Kontrol**: Pengguna dapat menyalakan/mematikan layer hasil analisis tanpa menghilangkan data dasar.

---

## 🏗️ Arsitektur Sistem

Sistem ini menggunakan pendekatan **Hybrid Cloud** untuk efisiensi biaya dan performa tinggi:

```mermaid
flowchart TD
    %% --- STYLE DEFINITIONS ---
    classDef actor fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef frontend fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef storage fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef backend fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    classDef ai fill:#fce4ec,stroke:#c2185b,stroke-width:2px;

    %% --- NODES ---
    User([Pengguna]):::actor

    subgraph Client_Side ["Frontend (GitHub Pages)"]
        UI["MapLibre GL JS<br>(Visualisasi Peta & UI)"]:::frontend
    end

    subgraph Cloud_Services ["Cloud Infrastructure"]
        direction TB
        Tiles[("Raster Tiles Storage<br>(GitHub Repo)")]:::storage
        
        subgraph Backend_Server ["Backend (Hugging Face)"]
            API["Flask API Handler"]:::backend
            GeoEngine["GeoPandas Engine<br>(Analisis Spasial)"]:::backend
            VectorDB[("GeoJSON Database")]:::storage
        end
    end

    Gemini[["Google Gemini AI"]]:::ai

    %% --- DATA FLOW ---
    User ==>|"1. Interaksi / Klik"| UI
    UI -.->|"2. Request Gambar Peta (Tiles)"| Tiles
    UI ==>|"3. Request API (Analisis/Chat)"| API

    API -->|"Proses Geometri"| GeoEngine
    GeoEngine <-->|"Baca Data Vektor"| VectorDB

    API -->|"4. Kirim Konteks Data"| Gemini



## 🛠️ Technology Stack

**Frontend**
* **Core:** HTML5, CSS3, JavaScript (ES6+)
* **Mapping Engine:** MapLibre GL JS (WebGL Rendering)
* **Hosting:** GitHub Pages (Static Hosting & Tile Server)

**Backend & AI**
* **API:** Python 3.10 dengan Flask Framework
* **Spatial Processing:** GeoPandas, Shapely, OSMnx, Rtree
* **Artificial Intelligence:** Google Generative AI SDK (Gemini 2.5 Flash)
* **Compute:** Hugging Face Spaces (Docker Container)

---

## 📂 Struktur Direktori

```text
geoportal-nguwet/
├── 📄 index.html          # Halaman Utama (Frontend Interaktif)
├── 📄 app.py              # Logika Server Backend (Flask & GIS Engine)
├── 📄 requirements.txt    # Daftar Pustaka Python (Dependencies)
├── 📄 Dockerfile          # Konfigurasi Container untuk Hugging Face
├── 📂 data/
│   ├── 📂 vectors/        # Database GeoJSON (Bangunan, Persil, Sungai)
│   └── 📂 tiles/          # Peta Dasar Raster (XYZ format - Hosted di GitHub)
│       ├── 📂 ortho/      # Tiles Foto Udara
│       └── 📂 dtm/        # Tiles Digital Terrain Model
└── 📂 logo/               # Aset Gambar & Logo Stakeholder
```

---

## 🚀 Panduan Instalasi & Deployment

### 1. Backend (Hugging Face Spaces)
1. Buat **Space** baru di Hugging Face dengan SDK **Docker**.
2. Upload file `Dockerfile`, `requirements.txt`, `app.py`, dan folder `data/vectors`.
3. Set **Environment Variable** `GEMINI_API_KEY` di pengaturan Space.
4. Ambil URL API dari menu "Embed this space".

### 2. Frontend (GitHub Pages)
1. **Clone** repository ini.
2. Edit file `index.html` bagian konfigurasi API:
   ```javascript
   const API_URL = '[https://link-space-anda.hf.space](https://link-space-anda.hf.space)';
   const TILE_URL = '[https://username.github.io/repo/data/tiles](https://username.github.io/repo/data/tiles)';
   ```
3. Upload folder `data/tiles` (Raster) dan folder `logo`.
4. Aktifkan **GitHub Pages** pada branch `main`.

---

## 🤝 Stakeholder Sumber Data

Data yang digunakan dalam sistem ini bersumber dari kolaborasi instansi:

* **Badan Pertanahan Nasional (BPN):** Peta Bidang Tanah & Ortofoto.
* **Badan Informasi Geospasial (BIG):** DEM & Batas Administrasi.
* **PUPR:** Data Bangunan & Regulasi Sempadan.
* **Pemerintah Desa Nguwet:** Data Validasi Lapangan.

---

## 👥 Tim Pengembang

**Kelompok 1 - Infrastruktur Data Spasial**

* **Mohammad Zulfi Rahadi Putra        24/541055/PTK/15932 

Caecilia Alvinny Atulolon			   24/551614/PTK/16405 

Dhega Wasi Wihikan 				       24/552330/PTK/16455 

Muhammad Rouf Indhra Dewa Sambodo      24/552974/PTK/16494 

Catharina Putri Kusuma Wardhani        25/569734/PTK/16948 **

* Magister Teknik Geomatika
* Departemen Teknik Geodesi
* Universitas Gadjah Mada

---

## 📄 Lisensi

Proyek ini didistribusikan di bawah lisensi **MIT License**.
Silakan gunakan, modifikasi, dan distribusikan kembali untuk keperluan pendidikan atau pengembangan lebih lanjut.
    Gemini -->|"5. Respon Bahasa Natural"| API

    API ==>|"6. Kirim Hasil JSON"| UI
