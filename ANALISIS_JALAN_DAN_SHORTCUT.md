# Analisis: Jalan & Shortcut (Simpang 3–5)

## Struktur project saat ini

### Grid
- **`n = 4`** → grid 4×4 **manzana** (blok).
- Variabel utama di `rellenarGrilla()`:
  - `anchoCalle` = 3.0 (lebar jalan)
  - `largoCalle` = 20.0 (panjang jalan)
  - `manzana` = 20.0 (sisi satu blok)

### Jalan (calle)
- **Factory**: `factory.createCalle(anchoCalle, largoCalle)`  
  → persegi panjang (lebar × panjang).
- **Posisi**:
  - Jalan **horizontal** (sejajar X): `translate(x, 0.1, z)` tanpa rotate.
  - Jalan **vertikal** (sejajar Z): `translate(x, 0.1, z)` lalu `rotate(Math.PI/2, 0, 1, 0)`.
- Saat ini ada **24 jalan**: calle1–calle12 (horizontal), calle13–calle24 (vertikal).

### Persimpangan (esquina)
- **Factory**: `factory.createEsquina(anchoCalle)`  
  → bentuk **bujur sangkar** (sama dengan calle persegi), dipakai di tengah pertemuan jalan.
- Shader memakai tekstur `esquina` / `cruce` untuk tampilan persimpangan.
- Saat ini ada **9 esquina** di setiap pertemuan 4 jalan (simpang 4).

### Koordinat grid (ringkas)
- Sumbu **X** (posisi horizontal):  
  `0`, `manzana`, `manzana+anchoCalle`, `2*manzana+anchoCalle`, `2*manzana+2*anchoCalle`, `3*manzana+2*anchoCalle`, `3*manzana+3*anchoCalle`.
- Sumbu **Z** (posisi vertikal): pola yang sama.
- Esquina diletakkan di titik temu, mis. `(manzana, manzana)`, `(2*manzana+anchoCalle, 2*manzana+anchoCalle)`, dll.

---

## Bisakah menambah jalan? **Ya.**

Langkah:
1. **Deklarasi variabel** (di bagian variabel global, bersama calle1–calle24):
   ```js
   var calle25 = null;
   var calle26 = null;
   // ...
   ```
2. **Buat jalan** di `rellenarCalles()` (atau fungsi baru):
   ```js
   calle25 = factory.createCalle(anchoCalle, largoCalle);
   calle25.translate(posX, 0.1, posZ);
   // Jika jalan vertikal (sejajar Z):
   calle25.rotate(Math.PI/2, 0, 1, 0);
   escena.add(calle25);
   ```
3. **Posisi (posX, posZ)** pilih sesuai grid, contoh:
   - Horizontal: `posZ` = 0, `manzana+anchoCalle`, `2*manzana+2*anchoCalle`, atau `3*manzana+3*anchoCalle`.
   - Vertikal: `posX` = 0, `manzana+anchoCalle`, `2*manzana+2*anchoCalle`, atau `3*manzana+3*anchoCalle`.

---

## Shortcut & simpang 3 / 4 / 5

- **Simpang 4** sudah ada: setiap esquina saat ini adalah pertemuan 4 jalan (2 horizontal + 2 vertikal).
- **Simpang 3 (T-junction)**:
  - Secara logika: satu titik temu dengan **3** jalan (satu jalan buntu atau satu lengan tidak dipakai).
  - Implementasi termudah: tambah **1 jalan shortcut** (mis. diagonal atau potongan) yang bertemu di satu titik; di titik itu tetap pakai **1 esquina** (`createEsquina(anchoCalle)`). Visual tetap persegi, tapi jumlah “lengan” jalan yang menempel bisa 3 (mis. satu ujung jalan shortcut tidak ke mana-mana).
- **Simpang 5**:
  - Satu titik temu dengan **5** jalan.
  - Implementasi: satu **esquina** di tengah + **5 ruas jalan** (4 dari grid + 1 shortcut, atau 3 grid + 2 shortcut). Shortcut bisa diagonal atau potongan jalan tambahan.

Jadi **bisa**: shortcut = jalan tambahan; simpang 3/5 = kombinasi jumlah jalan yang bertemu di satu esquina (tetap pakai `createEsquina` yang sama).

---

## Contoh konsep untuk 1 shortcut (simpang 3)

- Pilih satu esquina yang mau jadi “simpang 3” (satu jalan tidak dipakai), atau tambah **1 jalan shortcut** yang hanya menyambung dua titik.
- Contoh shortcut **diagonal** (menghubungkan dua esquina):
  - Jalan baru: `createCalle(anchoCalle, largoCalle)` lalu di-rotate **45°** (`Math.PI/4`) dan di-translate ke titik tengah antara dua esquina.
  - Di ujung-ujung shortcut bisa ditambah **esquina** jika bertemu jalan lain, atau dibiarkan tanpa esquina (ujung jalan).

Implementasi rinci (angka dan posisi) bisa disesuaikan di `index.html` di fungsi `rellenarCalles()` dan fungsi baru `rellenarShortcuts()` (lihat contoh di bawah).

---

## Ringkasan

| Yang ingin dilakukan | Bisa? | Cara |
|----------------------|-------|------|
| Menambah jalan biasa | Ya | `createCalle` + `translate` (+ `rotate` jika vertikal) + `escena.add`. Tambah variabel calle25, ... |
| Shortcut (jalan potong) | Ya | Jalan tambahan dengan posisi/rotasi yang menyambung dua titik (bisa diagonal dengan rotate 45°). |
| Simpang 3 | Ya | Satu esquina + 3 ruas jalan (atau 2 jalan + 1 shortcut) bertemu di satu titik. |
| Simpang 4 | Sudah ada | Esquina di setiap pertemuan 4 jalan di grid. |
| Simpang 5 | Ya | Satu esquina + 5 ruas jalan (4 grid + 1 shortcut, atau 3 + 2 shortcut). |

Semua tetap memakai:
- `factory.createCalle(anchoCalle, largoCalle)` untuk jalan.
- `factory.createEsquina(anchoCalle)` untuk persimpangan (bentuk persegi yang sama; shader membedakan calle vs esquina lewat type/id).
