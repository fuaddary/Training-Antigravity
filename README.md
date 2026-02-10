# 📘 Hari 2: Transformasi Monolith ke Microservices

## Panduan Hands-on Step-by-Step dengan Antigravity AI

> **Konsep:** Peserta membangun aplikasi monolith sendiri menggunakan Antigravity, kemudian secara bertahap mengubahnya menjadi microservices. Setiap langkah dipandu dengan prompt yang bisa langsung di-copy-paste.

---

# 📋 Pendahuluan

## Agenda Hari Ini

| Sesi        | Topik                                                       | Durasi   |
| ----------- | ----------------------------------------------------------- | -------- |
| **1** | Pembuatan Aplikasi & Perencanaan Monolith ke Microservices | 90 menit |
| **2** | Decomposition dengan Strangler Fig Pattern                  | 90 menit |
| **3** | Taming the SQL                                              | 90 menit |
| **4** | Event-Driven Architecture & Data Decoupling                 | 90 menit |

## Persiapan

Pastikan di laptop Anda sudah terinstall:

| Tool        | Cara Cek                   | Install                       |
| ----------- | -------------------------- | ----------------------------- |
| Node.js     | `node --version` (v18+)  | https://nodejs.org            |
| VS Code     | Sudah terbuka              | https://code.visualstudio.com |
| Antigravity | Extension aktif di VS Code | Dari VS Code Marketplace      |

> ✅ **Siap mulai** jika: Node.js terinstall, VS Code terbuka, Antigravity aktif.

---

# 🎯 Sesi 1: Pengenalan Aplikasi & Perencanaan Monolith ke Microservices

## ⏱ Durasi: 90 menit

---

## Bagian A: Membuat Aplikasi Monolith Anda Sendiri (30 menit)

Pada sesi ini, Anda akan membuat sebuah aplikasi monolith dari nol menggunakan Antigravity. Aplikasi ini akan menjadi bahan latihan untuk sisa hari ini.

### Step 1: Buat Folder Project

Buka **Terminal** di VS Code (`Ctrl+`` ` atau `View → Terminal`), lalu jalankan:

```bash
mkdir my-monolith-app
cd my-monolith-app
npm init -y
```

### Step 2: Minta Antigravity Membuat Aplikasi Monolith

Buka chat Antigravity. Copy-paste prompt ini **persis seperti yang tertulis**:

```
Buatkan aplikasi monolith Node.js + Express sederhana dengan tema
sistem aplikasi BPJS.

Requirement:
1. Minimal 4 modul/fitur (contoh: users, products, orders, reports)
2. Gunakan SQLite (pakai library sql.js atau better-sqlite3)
3. Setiap modul punya file route sendiri di folder src/routes/
4. 1 file utama src/app.js yang menghubungkan semuanya
5. Generate sample data minimal 100 record per tabel
6. Tambahkan frontend sederhana di folder public/ (HTML + CSS + JS)
7. Halaman dashboard dengan statistik

Buatkan kode lengkap dan jalankan di port 3000.

PENTING: Sengaja buat kode dengan pola-pola ini untuk latihan:
- Beberapa query menggunakan N+1 pattern (query dalam loop)
- Beberapa query menggunakan string interpolation (bukan parameterized)
- Tidak ada pagination (return semua data sekaligus)
- Semua modul pakai 1 database yang sama
```

### Step 3: Install Dependencies & Jalankan

Setelah Antigravity selesai generate kode, jalankan:

```bash
npm install
npm start
```

Buka browser: **http://localhost:3000**

### Step 4: Verifikasi Aplikasi Berjalan

Pastikan Anda bisa melihat:

- ✅ Dashboard dengan statistik
- ✅ Minimal 4 halaman/modul
- ✅ Data di tabel terisi

> **Jika ada error**, copy-paste error message ke Antigravity dan minta diperbaiki:
>
> ```
> Saya mendapat error berikut saat menjalankan aplikasi:
> [paste error di sini]
> Tolong perbaiki.
> ```

---

## Bagian B: Analisis Arsitektur Monolith (20 menit)

Sekarang kita akan menganalisis aplikasi yang baru dibuat.

### Step 5: Minta Antigravity Menganalisis Arsitektur

```
@workspace Analisis arsitektur project ini secara menyeluruh.

Jawab pertanyaan ini:
1. Arsitektur apa yang digunakan? (monolith/microservices/lainnya)
2. Ada berapa modul/fitur? Sebutkan masing-masing.
3. Bagaimana semua modul terhubung? Apakah mereka saling bergantung?
4. Tabel database apa saja yang ada? Apa relasinya?
5. Apa saja kelemahan dari arsitektur ini jika harus melayani 
   jutaan pengguna?

Buatkan diagram arsitektur saat ini.
```

**Perhatikan jawaban Antigravity dan catat:**

- Berapa modul yang saling bergantung?
- Tabel mana yang punya foreign key ke tabel lain?
- Fungsi mana yang mengakses lebih dari satu tabel?

### Step 6: Identifikasi Masalah Scalability

```
@workspace Jika aplikasi ini harus melayani 1 juta pengguna
secara bersamaan, apa yang akan terjadi?

Analisis dari 5 sudut pandang:
1. Scalability — bisakah tiap modul di-scale terpisah?
2. Reliability — jika satu modul crash, apa yang terjadi?
3. Development — jika 5 tim bekerja bersamaan, apa masalahnya?
4. Deployment — bagaimana proses deploy?
5. Database — apa risiko 1 database untuk semua modul?

Berikan skenario nyata untuk setiap masalah.
```

**Catat jawaban dalam tabel seperti ini:**

| Aspek       | Masalah | Contoh |
| ----------- | ------- | ------ |
| Scalability | ?       | ?      |
| Reliability | ?       | ?      |
| Development | ?       | ?      |
| Deployment  | ?       | ?      |
| Database    | ?       | ?      |

---

## Bagian C: Perencanaan Microservices (40 menit)

### Step 7: Identifikasi Bounded Contexts

```
@workspace Lakukan analisis Domain-Driven Design pada project ini.

Identifikasi bounded contexts:
1. Apa domain bisnis utama di aplikasi ini?
2. Data (tabel) mana yang dimiliki setiap domain?
3. Operasi apa yang dilakukan setiap domain?
4. Domain mana yang bisa berdiri sendiri (independent)?
5. Domain mana yang bergantung pada domain lain?

Format jawabannya sebagai tabel:
| Domain | Tabel | Operasi | Dependency |
```

### Step 8: Tentukan Service Boundaries

```
@workspace Berdasarkan bounded contexts yang sudah diidentifikasi,
tentukan SERVICE BOUNDARIES untuk migrasi ke microservices.

Untuk setiap service yang diusulkan:
1. Nama service
2. Tanggung jawab (1-2 kalimat)
3. Tabel database yang dimiliki
4. API endpoints yang disediakan
5. Service lain yang dibutuhkan

Buat juga urutan migrasi:
- Mana yang dipindah PERTAMA? (paling independen, risiko paling rendah)
- Mana yang dipindah TERAKHIR? (paling banyak dependency)
```

### Step 9: Desain Target Architecture

```
Berdasarkan analisis di atas, buatkan diagram arsitektur TARGET
setelah dikonversi ke microservices.

Tunjukkan:
1. Service-service yang ada (masing-masing dengan database sendiri)
2. API Gateway di depan semua service
3. Bagaimana service berkomunikasi (REST / Event)
4. Message broker (jika diperlukan)

Bandingkan dengan arsitektur monolith saat ini:
- Gambar arsitektur SEBELUM (monolith)
- Gambar arsitektur SESUDAH (microservices)
```

### Step 10: Buat Migration Roadmap

```
Buatkan roadmap migrasi dari monolith ke microservices
dalam 4 fase.

Untuk setiap fase:
1. Service mana yang dimigrasikan?
2. Kenapa urutan ini?
3. Apa risiko utama?
4. Bagaimana cara rollback jika gagal?
5. Estimasi effort (minggu)

Format sebagai timeline yang jelas.
```

---

## ✅ Checkpoint Sesi 1

Anda seharusnya sudah memiliki:

- [X] Aplikasi monolith berjalan di localhost:3000
- [X] Diagram arsitektur monolith saat ini
- [X] Daftar bounded contexts / domain bisnis
- [X] Rencana service boundaries
- [X] Diagram arsitektur target (microservices)
- [X] Migration roadmap 4 fase

> **Istirahat 15 menit** ☕

---

# 🎯 Sesi 2: Decomposition dengan Strangler Fig Pattern

## ⏱ Durasi: 90 menit

---

## Bagian A: Memahami Strangler Fig Pattern (20 menit)

### Step 1: Pelajari Pattern

```
Jelaskan Strangler Fig Pattern untuk pemula total.

1. Dari mana nama "Strangler Fig" berasal? (berikan analogi pohon)
2. Bagaimana cara kerjanya step-by-step?
3. Kenapa ini lebih aman daripada "big bang rewrite"
   (tulis ulang seluruh aplikasi dari nol)?
4. Perusahaan besar mana yang pernah menggunakannya?

Gunakan diagram untuk menjelaskan 3 fase:
- Fase 1: Monolith melayani semua traffic
- Fase 2: Service baru dibuat, traffic dipindah bertahap
- Fase 3: Monolith mengecil, digantikan service baru

Jelaskan dengan bahasa sederhana.
```

**Prinsip utama yang harus dipahami:**

```
🌳 Analogi Strangler Fig:
   Pohon strangler fig tumbuh MENGELILINGI pohon inang.
   Perlahan-lahan, pohon baru menggantikan pohon lama.
   Pohon lama akhirnya mati, pohon baru sudah berdiri kokoh.

💻 Dalam software:
   Service baru dibuat MENGELILINGI monolith.
   Traffic dipindahkan BERTAHAP ke service baru.
   Monolith MENGECIL seiring service baru bertambah.
   Akhirnya monolith MATI, microservices sudah berjalan.

✅ Keuntungan:
   - Tidak perlu stop monolith yang lagi jalan
   - Bisa rollback kapan saja
   - Risiko lebih rendah
   - Tim bisa belajar sambil jalan
```

---

## Bagian B: Memilih Service Pertama (15 menit)

### Step 2: Analisis Kandidat

```
@workspace Saya ingin memulai migrasi dengan Strangler Fig Pattern.

Analisis setiap bounded context di aplikasi ini dan 
rekomendasikan mana yang harus dimigrasikan PERTAMA.

Kriteria penilaian (skor 1-5):
1. Independensi — semakin sedikit dependency, semakin baik
2. Kemudahan — semakin mudah dipisahkan, semakin baik 
3. Risiko — semakin rendah risiko jika gagal, semakin baik
4. Pembelajaran — semakin banyak yang dipelajari, semakin baik

Buat tabel skor untuk semua bounded context.
Rekomendasikan mana yang terbaik dan jelaskan kenapa.
```

**Catat hasilnya. Biasanya yang paling cocok adalah modul yang:**

- Paling sedikit dependency ke modul lain
- Paling sedikit tabel yang di-share
- Paling rendah risiko bisnis jika ada masalah

---

## Bagian C: Membuat Service Pertama (35 menit)

### Step 3: Generate Service Baru

Ganti `[NAMA_SERVICE]` dan `[DESKRIPSI]` sesuai rekomendasi di Step 2:

```
@workspace Saya ingin memisahkan modul [NAMA_SERVICE] menjadi 
microservice yang berdiri sendiri.

Buatkan kode lengkap untuk service baru ini:

1. Project structure di folder baru: [nama]-service/
2. Express.js server sendiri di port 3001
3. Database SENDIRI (SQLite terpisah, bukan database monolith)
4. Semua API endpoints yang dibutuhkan
5. Migrasi data: copy data yang relevan dari database monolith

Kode harus production-ready:
- Parameterized queries (bukan string interpolation!)
- Error handling yang proper
- Pagination support (default 20 per halaman)
- Health check endpoint: GET /health
- CORS enabled

Tunjukkan perbedaan kualitas kode dibanding monolith lama.
```

### Step 4: Jalankan Service Baru

```bash
cd [nama]-service
npm install
npm start
```

Verifikasi: buka `http://localhost:3001/health` — harus response `{ status: "ok" }`

### Step 5: Bandingkan Kode Lama vs Baru

```
@workspace Bandingkan kode monolith lama (di src/routes/) 
dengan kode service baru (di [nama]-service/).

Tunjukkan perbedaan dalam format SEBELUM vs SESUDAH:
1. Query safety (string interpolation vs parameterized)
2. Error handling
3. Pagination
4. Code structure
5. Database independence

Untuk setiap perbedaan, jelaskan kenapa yang baru lebih baik.
```

---

## Bagian D: Implementasi Strangler Pattern (20 menit)

### Step 6: Buat Proxy di Monolith

```
@workspace Sekarang implementasikan Strangler Fig Pattern.

Yang perlu dilakukan di monolith (app.js):
1. Untuk request ke modul [NAMA_SERVICE]:
   - Coba forward ke service baru di port 3001
   - Jika service baru TERSEDIA → gunakan response dari sana
   - Jika service baru DOWN → fallback ke handler lama (monolith)
2. Untuk request ke modul LAINNYA:
   - Tetap dilayani oleh monolith seperti biasa

Ini inti dari Strangler Fig:
- Service baru "membungkus" fitur lama
- Traffic bisa dipindah bertahap
- Rollback mudah (service baru mati → otomatis pakai yang lama)

Buatkan kode proxy/forwarding yang dibutuhkan.
Sertakan environment variable untuk toggle on/off.
```

### Step 7: Test Strangler Pattern

```
Buatkan test scenario untuk memastikan Strangler Fig berjalan:

1. Test normal: service baru hidup → request diteruskan ke service baru
2. Test fallback: service baru mati → request dilayani monolith
3. Test toggle: environment variable bisa on/off forwarding

Berikan curl commands untuk setiap test scenario.
```

Lakukan test:

```bash
# Test 1: Service baru hidup (pastikan port 3001 aktif)
curl http://localhost:3000/api/[nama-service]
# Seharusnya response dari service baru

# Test 2: Matikan service baru, test lagi
# (Ctrl+C di terminal service baru)
curl http://localhost:3000/api/[nama-service]
# Seharusnya fallback ke monolith
```

---

## ✅ Checkpoint Sesi 2

Anda seharusnya sudah memiliki:

- [X] Memahami Strangler Fig Pattern dan 3 fasanya
- [X] Memilih service pertama untuk dimigrasikan
- [X] Service baru berjalan di port 3001
- [X] Strangler proxy di monolith (forward + fallback)
- [X] Test scenario berhasil

> **Istirahat 15 menit** ☕

---

# 🎯 Sesi 3: Taming the SQL

## ⏱ Durasi: 90 menit

---

## Bagian A: Menemukan N+1 Query Problem (25 menit)

### Step 1: Apa itu N+1 Query?

```
Jelaskan N+1 Query Problem dengan analogi kehidupan sehari-hari.

1. Apa itu N+1 Query? Kenapa namanya "N+1"?
2. Analogi: bayangkan Anda di restoran dan mau tahu harga
   semua menu. Apa bedanya tanya satu-satu vs minta daftar harga?
3. Impact terhadap performa — berapa kali lipat lebih lambat?
4. Kapan biasanya N+1 muncul? (kode pattern apa yang menyebabkannya?)

Tunjukkan contoh kode N+1 vs kode yang benar.
```

**Analogi yang mudah diingat:**

```
❌ N+1 QUERY (cara lambat):
   Anda di kelas, mau tahu alamat semua 30 murid.
   Langkah 1: Minta daftar 30 nama murid    → 1 query
   Langkah 2: Tanya alamat murid pertama     → 1 query
   Langkah 3: Tanya alamat murid kedua       → 1 query
   ...
   Langkah 31: Tanya alamat murid ke-30      → 1 query
   TOTAL: 31 queries! 😱

✅ CARA BENAR (JOIN):
   Langkah 1: Minta daftar nama + alamat sekaligus → 1 query
   TOTAL: 1 query! 🚀
```

### Step 2: Temukan N+1 di Project Anda

```
@workspace Scan seluruh project untuk menemukan SEMUA 
N+1 Query Problem.

N+1 terjadi ketika:
- Ada query di dalam loop/forEach/map
- Setiap iterasi melakukan query terpisah ke database
- Biasanya untuk mengambil data relasi (foreign key)

Untuk setiap temuan:
1. File dan fungsi mana?
2. Berapa total query yang dijalankan?
3. Tunjukkan baris kode yang bermasalah
4. Seberapa parah dampaknya? (hitung: 1 + N x jumlah query per loop)

Urutkan dari yang paling parah.
```

### Step 3: Fix N+1 dengan JOIN

```
@workspace Perbaiki SEMUA N+1 query yang ditemukan.

Untuk setiap N+1:
1. Tunjukkan KODE SEBELUM — yang bermasalah, dengan komentar
   "query #1", "query #N" untuk menunjukkan berapa query dijalankan
2. Tunjukkan KODE SESUDAH — yang optimal, menggunakan SQL JOIN
3. Jelaskan SQL JOIN yang digunakan (INNER JOIN / LEFT JOIN / kenapa)
4. Hitung: berapa query sebelum vs sesudah?

Pastikan hasilnya sama persis (data yang dikembalikan identik).
```

**Contoh perbaikan umum:**

```javascript
// ❌ SEBELUM: N+1 (misalnya 101 queries untuk 100 orders)
app.get('/api/orders', (req, res) => {
  const orders = db.exec('SELECT * FROM orders');     // 1 query
  const result = orders.map(order => {
    // Query di dalam loop! Ini N+1!
    const customer = db.exec(                          // N queries
      `SELECT nama FROM customers WHERE id = ${order.customer_id}`
    );
    return { ...order, customer_name: customer[0].nama };
  });
  res.json(result);
});

// ✅ SESUDAH: 1 query dengan JOIN
app.get('/api/orders', (req, res) => {
  const result = db.exec(`
    SELECT o.*, c.nama as customer_name
    FROM orders o
    LEFT JOIN customers c ON o.customer_id = c.id
    ORDER BY o.created_at DESC
  `);
  res.json(result);
});
```

---

## Bagian B: Fixing Slow Queries (20 menit)

### Step 4: Temukan Correlated Subqueries

```
@workspace Temukan semua SLOW QUERY di project ini.

Cari pattern yang lambat:
1. Correlated subquery (SELECT di dalam SELECT)
2. Query tanpa WHERE clause pada tabel besar
3. LIKE tanpa index
4. ORDER BY pada kolom tanpa index
5. Aggregasi tanpa GROUP BY yang efisien

Untuk setiap temuan:
- Tunjukkan query yang bermasalah
- Jelaskan KENAPA lambat
- Berikan query yang sudah dioptimasi
- Estimasi improvement (berapa kali lebih cepat)
```

### Step 5: Optimasi dengan JOIN dan GROUP BY

```
@workspace Untuk setiap slow query yang ditemukan,
tunjukkan cara optimasi step by step.

Tunjukkan transformasi query:
1. Query asli (lambat)
2. Langkah 1: Ubah subquery → JOIN
3. Langkah 2: Tambahkan GROUP BY jika perlu
4. Langkah 3: Pastikan hasilnya sama
5. Query akhir (cepat)

Jelaskan setiap langkah dengan bahasa sederhana.
Kenapa JOIN lebih cepat dari subquery?
```

---

## Bagian C: Pagination & Indexing (25 menit)

### Step 6: Implementasi Pagination

```
@workspace Semua endpoint saat ini mengembalikan SEMUA data.
Ini masalah besar jika ada ratusan ribu record.

Buatkan pagination yang bisa dipakai di SEMUA endpoint:

1. Buat helper function untuk pagination
2. Parameter: ?page=1&limit=20
3. Response format:
   {
     data: [...],
     pagination: {
       page: 1,
       limit: 20,
       total: 1000,
       totalPages: 50,
       hasNext: true,
       hasPrev: false
     }
   }
4. Default: page=1, limit=20

Tunjukkan cara implementasi di satu endpoint sebagai contoh,
lalu jelaskan cara menerapkannya di endpoint lainnya.
Jelaskan bagaimana LIMIT dan OFFSET bekerja di SQL.
```

### Step 7: Buat Database Indexes

```
@workspace Analisis semua query di project ini dan 
rekomendasikan database indexes.

Untuk setiap index yang direkomendasikan:
1. Tabel dan kolom mana?
2. Kenapa index ini dibutuhkan?
3. Query mana yang akan lebih cepat?
4. SQL CREATE INDEX statement

Jelaskan juga:
- Apa itu database index? (analogi: indeks buku)
- Kapan index membantu vs kapan tidak?
- Apakah terlalu banyak index itu berbahaya? Kenapa?

Buatkan script SQL lengkap untuk semua indexes.
```

---

## Bagian D: SQL Injection Prevention (20 menit)

### Step 8: Temukan dan Fix SQL Injection

```
@workspace Sebagai security auditor, scan SEMUA file
untuk SQL Injection vulnerability.

SQL Injection terjadi ketika input user dimasukkan
langsung ke query SQL menggunakan string concatenation
atau template literal TANPA escaping.

Untuk setiap temuan:
1. File dan baris berapa?
2. Contoh serangan yang bisa dilakukan (proof of concept)
3. Apa dampaknya jika berhasil? (baca data, hapus data, dll.)
4. KODE SEBELUM (vulnerable)
5. KODE SESUDAH (aman, gunakan parameterized query)
```

**Contoh umum:**

```javascript
// ❌ VULNERABLE: input langsung masuk ke query
app.get('/search', (req, res) => {
  const nama = req.query.nama;
  db.exec(`SELECT * FROM users WHERE nama LIKE '%${nama}%'`);
  // Attacker bisa kirim: nama = ' OR '1'='1
  // Query jadi: SELECT * FROM users WHERE nama LIKE '%' OR '1'='1%'
  // → Mengembalikan SEMUA data!
});

// ✅ AMAN: parameterized query
app.get('/search', (req, res) => {
  const nama = req.query.nama;
  const stmt = db.prepare('SELECT * FROM users WHERE nama LIKE ?');
  stmt.bind([`%${nama}%`]);
  // Input di-escape otomatis oleh database driver
});
```

---

## ✅ Checkpoint Sesi 3

Anda seharusnya sudah:

- [X] Menemukan dan memperbaiki semua N+1 queries
- [X] Mengoptimasi correlated subqueries dengan JOIN
- [X] Menambahkan pagination ke endpoint
- [X] Membuat database indexes
- [X] Memperbaiki SQL Injection vulnerabilities

> **Istirahat 15 menit** ☕

---

# 🎯 Sesi 4: Event-Driven Architecture & Data Decoupling

## ⏱ Durasi: 90 menit

---

## Bagian A: Masalah Data Coupling (20 menit)

### Step 1: Identifikasi Coupling

```
@workspace Analisis bagaimana data antar modul saling terkait.

Saya ingin memahami COUPLING (ketergantungan) data:
1. Tabel mana yang saling referensi via foreign key?
2. Fungsi mana yang mengakses data dari tabel milik modul lain?
3. JOIN query mana yang MENGGABUNGKAN tabel dari domain berbeda?

Buatkan diagram yang menunjukkan semua hubungan data.
Tandai mana yang akan RUSAK jika setiap service punya database sendiri.
```

### Step 2: Kenapa JOIN Antar Database Tidak Bisa

```
Jelaskan kenapa di arsitektur microservices, setiap service 
HARUS punya database sendiri, dan kita TIDAK BISA melakukan
SQL JOIN antar database service yang berbeda.

Jawab dengan bahasa sederhana:
1. Apa prinsip "Database per Service"?
2. Apa yang terjadi jika 2 service share 1 database?
3. Kalau tidak bisa JOIN, bagaimana cara mendapatkan 
   data dari service lain?
4. Apa trade-off yang harus kita terima?

Berikan contoh konkret dari aplikasi kita.
```

**Masalah yang akan muncul:**

```
Di monolith (BISA JOIN):
  SELECT orders.*, customers.nama 
  FROM orders JOIN customers ON orders.customer_id = customers.id

Di microservices (TIDAK BISA JOIN — database terpisah!):
  Order Service  → order_db   → tabel: orders
  Customer Service → customer_db → tabel: customers
  
  orders.customer_id = ???
  Butuh cara lain untuk mendapatkan nama customer!
```

---

## Bagian B: Event-Driven Architecture (25 menit)

### Step 3: Pengenalan Event-Driven

```
Jelaskan Event-Driven Architecture untuk pemula total.

Gunakan analogi yang SANGAT sederhana:
1. Apa itu "event"? 
   (analogi: pengumuman di grup WhatsApp)
2. Apa itu "publisher"? 
   (analogi: orang yang mengirim pengumuman)
3. Apa itu "subscriber"? 
   (analogi: orang yang membaca pengumuman)
4. Apa itu "message broker"? 
   (analogi: grup WhatsApp itu sendiri)

Kenapa ini lebih baik dari direct call (REST API)?

Buatkan perbandingan:
- TANPA event (direct call): apa yang terjadi?
- DENGAN event (message broker): apa yang terjadi?

Tunjukkan diagram untuk kedua pendekatan.
```

**Analogi yang harus dipahami:**

```
TANPA EVENT (Direct Call):
  Order Service: "Hey Customer Service, kasih nama customer #123!"
  → Order HARUS TAHU alamat Customer Service
  → Jika Customer Service DOWN, Order juga GAGAL
  → Tight coupling! 😱

DENGAN EVENT (Message Broker):
  Order Service: *publish* "ORDER_CREATED: {customer_id: 123}"
  (Order tidak peduli siapa yang membaca)
  
  Customer Service: *subscribe* "Oh ada order baru, saya update datanya"
  Billing Service: *subscribe* "Oh ada order baru, saya buat invoice"
  Notification Service: *subscribe* "Oh ada order baru, saya kirim email"
  
  → Masing-masing service INDEPENDEN
  → Jika satu service DOWN, event disimpan & diproses nanti
  → Loose coupling! ✅
```

### Step 4: Design Events untuk Aplikasi Anda

```
@workspace Design sistem events untuk aplikasi ini.

Berdasarkan alur bisnis yang ada, identifikasi:
1. Event apa saja yang perlu dibuat?
2. Siapa publisher (pengirim) setiap event?
3. Siapa subscriber (penerima) setiap event?
4. Data apa yang dibawa setiap event (payload)?
5. Apa yang dilakukan subscriber ketika menerima event?

Format sebagai tabel:
| Event Name | Publisher | Subscriber | Payload | Action |
```

### Step 5: Implementasi Event Sederhana

```
@workspace Implementasikan event system sederhana 
menggunakan Node.js EventEmitter sebagai simulasi message broker.

Buatkan:
1. File src/event-bus.js — in-memory event bus
2. Publisher: saat data dibuat/diubah, publish event
3. Subscriber: service lain yang listen dan bereaksi
4. Log setiap event yang dikirim dan diterima

Contoh flow:
- User membuat order → publish "ORDER_CREATED"
- Inventory service mendengar → kurangi stok
- Notification service mendengar → kirim notifikasi

Ini simulasi sederhana. Di production, gunakan 
Kafka/RabbitMQ/Redis Streams.
```

---

## Bagian C: Data Decoupling Patterns (25 menit)

### Step 6: API Composition Pattern

```
Jelaskan API Composition Pattern.

Skenario: di monolith, kita bisa JOIN tabel orders + customers + products
dalam 1 query. Di microservices, tabel ini ada di DATABASE BERBEDA.

Bagaimana caranya menampilkan data gabungan?

Tunjukkan:
1. SEBELUM (monolith): 1 JOIN query
2. SESUDAH (microservices): API Composition

Buatkan kode contoh:
- Panggil 3 service secara parallel (Promise.all)
- Gabungkan hasilnya
- Return ke client

Jelaskan trade-off: lebih lambat tapi lebih scalable.
```

**Contoh pattern:**

```javascript
// Monolith: 1 query
const data = db.exec(`
  SELECT o.*, c.nama, p.product_name
  FROM orders o
  JOIN customers c ON o.customer_id = c.id
  JOIN products p ON o.product_id = p.id
`);

// Microservices: 3 API calls, digabungkan
app.get('/api/order-detail/:id', async (req, res) => {
  const order = await fetch('http://order-service/api/orders/' + id);
  
  // Panggil 2 service secara PARALLEL (lebih cepat!)
  const [customer, product] = await Promise.all([
    fetch('http://customer-service/api/customers/' + order.customer_id),
    fetch('http://product-service/api/products/' + order.product_id)
  ]);

  res.json({
    ...order,
    customer_name: customer.nama,
    product_name: product.name
  });
});
```

### Step 7: Eventual Consistency

```
Jelaskan Eventual Consistency untuk pemula.

Di monolith (Strong Consistency):
- Update data → langsung konsisten di mana-mana
- Semua orang lihat data yang sama SEKETIKA

Di microservices (Eventual Consistency):
- Update data → perlu waktu untuk sinkronisasi
- Untuk sesaat, data mungkin BELUM sama di semua service

Pertanyaan:
1. Kenapa kita harus menerima eventual consistency?
2. Berapa lama biasanya delay-nya? (milidetik-detik)
3. Bagaimana menangani situasi dimana data belum sinkron?
4. Apa itu Saga Pattern? Kapan digunakan?

Berikan contoh nyata:
- User melakukan pembayaran
- Berapa lama sampai status berubah di service lain?
- Apa yang terjadi jika pembayaran gagal di tengah jalan?
```

---

## Bagian D: Rencana Migrasi Data (20 menit)

### Step 8: Strategy Data Migration

```
@workspace Buatkan rencana migrasi data dari 1 database monolith 
menjadi beberapa database terpisah per service.

Jawab pertanyaan ini:
1. Bagaimana memecah data: tabel mana ke database mana?
2. Bagaimana mengatasi foreign key yang cross-service?
   (misal: orders.customer_id → padahal customers ada di service lain)
3. Bagaimana menjaga data tetap konsisten SELAMA proses migrasi?
4. Apa rollback plan jika ada masalah?

Langkah per langkah:
- Fase 1: Duplikasi data (baca dari baru, tulis ke dua-duanya)
- Fase 2: Verifikasi data sama
- Fase 3: Switch ke database baru
- Fase 4: Hapus data lama dari monolith
```

### Step 9: Ringkasan & Architecture Decision Record

```
Buatkan ringkasan SEMUA yang dipelajari hari ini 
dalam format Architecture Decision Record (ADR).

Format:
1. KONTEKS — situasi monolith saat ini, masalahnya apa
2. KEPUTUSAN — apa yang akan dilakukan (migrasi ke microservices)
3. PENDEKATAN — Strangler Fig Pattern, step by step
4. KONSEKUENSI — trade-off apa yang diterima
5. ROADMAP — timeline implementasi (4 fase)
6. NEXT STEPS — apa yang harus dilakukan setelah training

Buatkan juga checklist yang bisa dipakai tim
untuk memulai migrasi setelah training ini selesai.
```

---

## ✅ Checkpoint Sesi 4

Anda seharusnya sudah:

- [X] Memahami masalah data coupling antar service
- [X] Mengenal Event-Driven Architecture
- [X] Mengimplementasikan event bus sederhana
- [X] Memahami API Composition & Eventual Consistency
- [X] Membuat rencana migrasi data
- [X] Architecture Decision Record selesai

---

# 📚 Lampiran: Cheat Sheet Prompt Antigravity

## Buat Aplikasi

```
Buatkan aplikasi [jenis] menggunakan [tech stack].
Minimal [N] modul dengan [requirements].
Generate sample data [jumlah] records per tabel.
```

## Analisis Arsitektur

```
@workspace Analisis arsitektur project ini.
Identifikasi bounded contexts dan dependency antar modul.
Buatkan diagram dan tabel.
```

## Temukan Masalah

```
@workspace Scan project untuk [N+1 query / SQL injection / slow query].
Untuk setiap temuan, tunjukkan SEBELUM dan SESUDAH fix.
```

## Migrasi Service

```
@workspace Pisahkan modul [nama] menjadi microservice.
Buatkan kode lengkap dengan database sendiri.
Implementasi Strangler Fig Pattern.
```

## Design Pattern

```
Jelaskan [Strangler Fig / Event-Driven / CQRS / Saga] pattern.
Gunakan analogi sederhana dan diagram.
Tunjukkan contoh kode implementasi.
```

## Tips Prompting yang Efektif

| Tips               | ❌ Kurang Baik | ✅ Lebih Baik                                    |
| ------------------ | -------------- | ------------------------------------------------ |
| Spesifik           | "Fix query"    | "Fix N+1 query di fungsi GET / di file users.js" |
| Minta perbandingan | "Perbaiki"     | "Tunjukkan SEBELUM dan SESUDAH"                  |
| Gunakan @workspace | "Lihat kode"   | "@workspace Analisis semua file"                 |
| Minta penjelasan   | "Buat kode"    | "Buat kode dan jelaskan setiap bagian"           |
| Iterasi            | (berhenti)     | "Elaborasi poin #3 lebih detail"                 |
| Minta format       | "Jelaskan"     | "Buatkan tabel perbandingan"                     |
| Minta analogi      | (teknis)       | "Jelaskan dengan analogi kehidupan sehari-hari"  |
