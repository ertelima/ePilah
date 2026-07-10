# E-Pilah RT 005 — Taman Buaran Indah IV

Sistem pelaporan pilah sampah warga berbasis web untuk RT 005, RW 013, Kelurahan Klender, Kec. Duren Sawit, Jakarta Timur.

## 🏗️ Arsitektur (Mobile-First Smartphone App)

Aplikasi didesain sebagai **mobile app** dengan phone frame wrapper:
- Max-width 430px, centered di desktop
- Bottom tab bar (iOS/Android style) sebagai navigasi utama
- Status bar + app header
- Touch-optimized (min 44px tap targets)
- Animasi transisi antar halaman

```
apps/
├── index.html          ← Entry point (phone frame + 5 tab)
├── css/
│   └── style.css       ← Mobile-first styles (Material + iOS hybrid)
├── js/
│   ├── main.js         ← Bootstrap + event wiring + setor form handler
│   ├── config.js       ← Konstanta: Firebase config, multiplier poin, item game
│   ├── utils.js        ← DOM helpers, HTML sanitizer, toast, modal
│   ├── data.js         ← Central data store + localStorage persistence + CSV parser
│   ├── firebase.js     ← Firebase init, realtime listeners, cloud write
│   ├── auth.js         ← Login / Register / Logout flows
│   ├── ui.js           ← Rendering: dashboard, leaderboard, activity feed, parallax
│   ├── game.js         ← Game "Pilah Cepat!" engine
│   └── activity.js     ← Offline simulation feed
└── README.md
```

## 🚀 Cara Menjalankan

### 1. Static server (direkomendasikan)
Karena aplikasi menggunakan ES modules (`import`/`export`), file harus disajikan melalui HTTP server, bukan dibuka langsung dari filesystem (`file://`).

```bash
# Python 3
python -m http.server 8080

# Node.js (npx)
npx serve .

# VS Code Live Server extension
```

Lalu buka `http://localhost:8080`

### 2. CSV Data Warga
Letakkan file `Data_Warga_Terurut.csv` di folder yang sama dengan `index.html`. Format kolom:

```
Blok;No_Rumah;Nama_Penghuni;Poin;Berat
Blok A;01;Hendra Wijaya;420;35.5
Blok B;03;Dewi Lestari;640;58.0
```

Atau upload manual melalui tombol **Unggah Manual CSV** di Dashboard.

Format delimiter: `,` atau `;` (auto-deteksi).

## 🔥 Firebase Setup (Production)

1. Buka `js/config.js`
2. Ganti `FIREBASE_CONFIG` dengan kredensial nyata dari Firebase Console
3. Aktifkan **Authentication** (Email/Password + Anonymous)
4. Aktifkan **Firestore Database**
5. Atur **Firestore Security Rules**:

```
rules_version = '2';
service cloud.firestore {
  match /databases/{database}/documents {
    match /artifacts/{appId}/public/data/{docId} {
      allow read: if true;
      allow write: if request.auth != null;
    }
    match /artifacts/{appId}/users/{userId}/{document=**} {
      allow read, write: if request.auth != null && request.auth.uid == userId;
    }
  }
}
```

## 🔒 Keamanan

- **HTML Sanitizer**: Semua data dari CSV, Firestore, dan input user di-escape via `escapeHTML()` sebelum rendering DOM
- **TextContent**: Data teks murni menggunakan `textContent`, bukan `innerHTML`
- **Safe Icons**: Hanya ikon dari whitelist `SAFE_ICONS` yang dirender sebagai HTML
- **Firebase Security Rules**: Harus diatur untuk membatasi akses write hanya ke user terautentikasi

## 📱 Fitur

| Fitur | Deskripsi |
|---|---|
| Dashboard | Statistik RT + jadwal angkut + feed aktivitas real-time |
| Panduan Pilah | Search engine + 4 kategori sampah dengan instruksi |
| Setor Sampah | Form pengiriman laporan + estimasi poin |
| Leaderboard | Podium Top 3 + tabel peringkat per blok |
| Portal Warga | Login/Register/Logout + dashboard pribadi terverifikasi |
| Game Edukasi | Mini-game pilah cepat 30 detik + klaim bonus poin |
| Offline Mode | localStorage persistence + simulasi aktivitas otomatis |

## 🎯 Poin Multiplier

| Kategori | Poin |
|---|---|
| Plastik Bersih | 15 / kg |
| Kertas/Kardus | 10 / kg |
| Logam/Kaleng | 20 / kg |
| Minyak Jelantah | 25 / liter |
| Organik Kompos | 5 / kg |

## 📅 Jadwal Angkut

| Hari | Kategori |
|---|---|
| Senin pagi | Organik (Basah) |
| Rabu pagi | Anorganik (Kering) |
| Jumat pagi | Residu & B3 |
| Minggu 07:00 | Kerja Bakti — Kumpul Akbar Daur Ulang |

## 🛠️ Tech Stack

- **UI**: Tailwind CSS (CDN) + FontAwesome 6.4
- **Font**: Inter (Google Fonts)
- **Backend**: Firebase (Firestore + Auth)
- **No build step**: ES Modules native, bisa langsung di-serve

## 📝 Lisensi

Proyek komunitas RT 005 Taman Buaran Indah IV.
