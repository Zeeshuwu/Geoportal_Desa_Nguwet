🌍 Geoportal Desa Nguwet

Sistem Informasi Geospasial Cerdas untuk Analisis Tata Ruang Desa

Geoportal Desa Nguwet adalah aplikasi WebGIS interaktif yang dirancang untuk membantu perangkat desa dan masyarakat dalam memvisualisasikan data spasial, melakukan analisis tata ruang otomatis, dan mendapatkan wawasan berbasis data melalui asisten kecerdasan buatan (AI).

Proyek ini dikembangkan oleh Kelompok 1 - Infrastruktur Data Spasial, Magister Teknik Geomatika, Universitas Gadjah Mada.

✨ Fitur Utama

Peta Interaktif 3D

Visualisasi bangunan 3D (LoD 1) berdasarkan data ketinggian.

Integrasi data Ortofoto resolusi tinggi dan DTM (Digital Terrain Model).

Popup informasi interaktif untuk setiap objek bangunan dan bidang tanah.

Toolbox Analisis Spasial (Server-Side)

📊 Analisis Skor Lokasi: Menilai strategisnya lokasi berdasarkan akses jalan dan kepadatan.

🟨 Status Pemanfaatan Lahan: Menghitung rasio bangunan terhadap luas persil tanah (Building Coverage Ratio).

🌊 Cek Sempadan Sungai: Deteksi otomatis bangunan yang melanggar zona sempadan sungai (Buffer 15m) sesuai Permen PUPR No. 28/2015.

Asisten Cerdas AI (Chatbot)

Terintegrasi dengan Google Gemini 2.5 Flash.

Menjawab pertanyaan seputar statistik desa (jumlah bangunan, luas lahan) secara natural.

Memberikan konteks data real-time dari database spasial.

Manajemen Layer Non-Destruktif

Hasil analisis ditampilkan sebagai layer baru di atas peta dasar tanpa menghapus data asli.

Legenda dinamis yang muncul sesuai analisis yang aktif.

🏗️ Arsitektur Sistem

Sistem ini menggunakan pendekatan Hybrid Cloud untuk efisiensi biaya dan performa tinggi:

Frontend: Dihosting di GitHub Pages (Statis & Cepat).

Backend: Dihosting di Hugging Face Spaces (Komputasi Berat Python/GeoPandas).

Data Raster: Disimpan sebagai Tiles di GitHub untuk streaming cepat.

flowchart TD
    %% --- AKTOR UTAMA ---
    User([Pengguna]):::actor

    %% --- FRONTEND ---
    subgraph Client_Side ["Frontend (GitHub Pages)"]
        UI["MapLibre GL JS<br>(Visualisasi Peta & UI)"]:::frontend
    end

    %% --- CLOUD SERVICES ---
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
    Gemini -->|"5. Respon Bahasa Natural"| API

    API ==>|"6. Kirim Hasil JSON"| UI

    %% --- STYLING ---
    classDef actor fill:#e1f5fe,stroke:#01579b,stroke-width:2px;
    classDef frontend fill:#e8f5e9,stroke:#2e7d32,stroke-width:2px;
    classDef storage fill:#fff3e0,stroke:#ef6c00,stroke-width:2px;
    classDef backend fill:#f3e5f5,stroke:#7b1fa2,stroke-width:2px;
    classDef ai fill:#fce4ec,stroke:#c2185b,stroke-width:2px;


🛠️ Teknologi yang Digunakan

Frontend

HTML5, CSS3, JavaScript (ES6+)

MapLibre GL JS: Rendering peta vektor dan raster 3D.

Chart.js: Visualisasi data statistik.

FontAwesome: Ikon antarmuka.

Backend

Python 3.10

Flask: Framework API ringan.

GeoPandas & Shapely: Analisis geometri dan spasial.

OSMnx: Pengambilan data jaringan jalan.

Google Generative AI SDK: Integrasi LLM.

🚀 Cara Instalasi & Deployment

1. Backend (Hugging Face)

Buat Space baru di Hugging Face (SDK: Docker).

Upload file: Dockerfile, requirements.txt, app.py, dan folder data/vectors.

Set Environment Variable (jika perlu) untuk API Key Gemini.

2. Frontend (GitHub Pages)

Fork atau Clone repositori ini.

Edit file index.html:

const API_URL = '[https://link-space-huggingface-anda.hf.space](https://link-space-huggingface-anda.hf.space)';
const TILE_URL = '[https://username.github.io/nama-repo/data/tiles](https://username.github.io/nama-repo/data/tiles)';


Upload folder data/tiles (Raster Tiles) dan folder logo.

Aktifkan GitHub Pages di pengaturan repositori (Source: main / root).

📂 Struktur Direktori

geoportal-nguwet/
├── 📄 index.html          # Halaman Utama (Frontend)
├── 📄 app.py              # Logika Server (Backend)
├── 📄 requirements.txt    # Daftar Pustaka Python
├── 📄 Dockerfile          # Konfigurasi Container
├── 📂 data/
│   ├── 📂 vectors/        # File GeoJSON (Bangunan, Persil, Sungai)
│   └── 📂 tiles/          # Pecahan Gambar Peta (XYZ format)
└── 📂 logo/               # Aset Gambar & Logo Stakeholder


🤝 Stakeholder Sumber Data

Data yang digunakan dalam sistem ini bersumber dari:

Badan Pertanahan Nasional (BPN): Peta Bidang Tanah & Ortofoto.

Badan Informasi Geospasial (BIG): DEM & Batas Administrasi.

PUPR: Data Bangunan & Regulasi Sempadan.

Pemerintah Desa Nguwet: Data Validasi Lapangan.
