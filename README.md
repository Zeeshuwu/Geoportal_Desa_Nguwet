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
    Gemini -->|"5. Respon Bahasa Natural"| API

    API ==>|"6. Kirim Hasil JSON"| UI
