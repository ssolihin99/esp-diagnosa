# ESP Diagnosa — Aplikasi Web

Aplikasi standalone untuk mendiagnosa kondisi ESP dari data parameter, berbasis katalog fault-signature. Berjalan sepenuhnya di browser (client-side), tanpa backend.

## Isi
- `index.html` — aplikasi (UI + engine diagnosa)
- `signatures.json` — **sumber kebenaran**: katalog signature, aturan skoring, ambang tren, alias kolom, narasi & rekomendasi

Untuk mengubah logika diagnosa (menambah case, menyetel ambang, memperbaiki narasi), **cukup edit `signatures.json`** — kode aplikasi tidak perlu disentuh.

## Cara menjalankan

### Opsi A — server lokal (disarankan)
Aplikasi membaca `signatures.json` via `fetch`, yang butuh konteks http:
```bash
cd esp-app
python -m http.server 8000
# buka http://localhost:8000
```

### Opsi B — GitHub Pages / Netlify
Unggah kedua file ke repo, aktifkan Pages. Langsung jalan.

### Opsi C — buka langsung (file://)
Karena `fetch` diblokir di `file://`, aplikasi akan menampilkan tombol **"muat signatures.json manual"** — pilih file JSON-nya, lalu app berfungsi normal.

## Cara pakai
1. **Unggah CSV** — lepas file CSV berisi kolom parameter (nama bebas: `PIP`, `Intake Pressure (psi) (Raw)`, `Amps`, dll — dikenali otomatis). App menurunkan tren, menormalisasi terhadap frekuensi bila perlu, mencocokkan ke signature, dan menerapkan temporal gating.
2. **Input Tren Manual** — untuk data tidak lengkap: pilih arah tren tiap parameter (naik tajam … turun tajam), karakter sinyal Amps/Pi (spikey/eratic/smooth), dan skala waktu tren.

## Fitur engine
- Peta alias kolom → parameter kanonik (buang embel-embel satuan dalam kurung)
- Penurunan tren (baseline vs akhir) + klasifikasi 7 level
- **Kompensasi frekuensi** (affinity law): `dP/Hz²`, `Amps/Hz` saat Freq berubah
- Deteksi karakter sinyal (spikey/eratic/smooth) di resolusi asli
- Deteksi TRIP (RA→0) & sensor beku/flat
- Skoring berbobot + **temporal gating** (mendadak vs gradual)
- Grafik tren (dinormalisasi min–max)

## Sinkronisasi dengan skill
`signatures.json` mencerminkan logika di skill `esp-diagnosa`. Saat skill diupdate, sinkronkan perubahannya ke file JSON ini (atau sebaliknya) agar keduanya konsisten.
