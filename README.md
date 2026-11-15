# Digital Fringe Processing — Computational Optical Metrology

**Bahan ajar Sistem Fotonika · Teknik Fisika · Iwan Cony Setiadi**

Seri **5 notebook Jupyter** dari model medan optik sampai validasi algoritma dan studi kasus engineering. Dirancang untuk S1 semester 6 dengan jalur eksplorasi setingkat kegiatan riset. **Seluruh data yang diberikan bersifat sintetis dan reproducible (random seeds tetap)**. Belum ada klaim validasi instrumen nyata maupun dataset pengukuran.

## Susunan notebook

| Notebook | Inti materi |
| --- | --- |
| [01 — Fisika fringe & sampling](notebooks/01_fisika_fringe_dan_sampling.ipynb) | Medan kompleks, interferensi, kamera, shot/read noise, Nyquist |
| [02 — Fourier phase retrieval](notebooks/02_fourier_phase_retrieval.ipynb) | Spektrum 2D, sideband filtering, Takeda, bias/noise/filter trade-off |
| [03 — Phase-shifting & unwrapping](notebooks/03_phase_shifting_dan_unwrapping.ipynb) | Least-squares PSI, 3/4/8/12-step, langkah fase meleset, unwrap 2D |
| [04 — Kalibrasi & ketidakpastian](notebooks/04_kalibrasi_rekonstruksi_ketidakpastian.ipynb) | Fase-ke-tinggi, moiré-ke-strain, kalibrasi, uncertainty propagation |
| [05 — Studi kasus engineering](notebooks/05_studi_kasus_engineering.ipynb) | Interferometri pelat mekanis, dual-pitch grid/moiré, gamma & baseline errors pada fringe projection; pembandingan algoritma dan error budget |

**Pengayaan riset Notebook 05:** lihat [Protokol studi kasus dan rubrik penelitian](CASE_STUDY_RESEARCH_PROTOCOL.md). Contoh A2, B2 dan C2 menguji kegagalan model yang tidak muncul dalam simulasi ideal.

## Prasyarat

- Python 3.10+, aljabar kompleks, gelombang optik, kalkulus diferensial, transformasi Fourier.
- Tidak memerlukan dataset eksternal atau akun API. Notebook mandiri; tidak perlu clone modul Python khusus.
- Dependensi utama: `numpy`, `scipy`, `matplotlib`, `scikit-image`, `pandas`, `ipykernel`, `nbformat`, `nbclient` (untuk verifikasi notebook).

## Menjalankan secara lokal

```bash
python -m venv .venv
# Windows: .venv\Scripts\activate
# Linux / macOS: source .venv/bin/activate
python -m pip install -r requirements.txt
jupyter lab
```

## Membuka di Google Colab

1. Unggah folder `notebooks/` bersama file lainnya ke repo GitHub.
2. Buka Google Colab → **File → Open notebook → GitHub**.
3. Tempel alamat repo, pilih notebook, kemudian jalankan **Runtime → Run all**.

Semua notebook menggunakan library publik yang lazim tersedia di Colab. Jika lingkungan Colab belum menyediakan dependensi yang sesuai, jalankan sel instalasi sesuai `requirements.txt` secara manual. Belum diuji langsung di runtime Colab; eksekusi lokal dilaporkan di bawah.

## Kriteria ilmiah (wajib untuk tugas mahasiswa)

1. Tulis **measurand**, geometri, asumsi, dan konvensi fase/tanda.
2. Pertahankan label unit (rad, siklus/pixel, nm, µm, mm, microstrain).
3. Tetapkan **ground truth**, ROI dan metrik sebelum melakukan tuning parameter.
4. Jangan menyebut satu simulasi sebagai *validasi eksperimen* atau *akurasi alat*.
5. Untuk benchmarking, variasikan lebih dari satu noise level dan random seed; laporkan gagal unwrap.
6. Jelaskan mengapa rumus fase–tinggi interferometri refleksi tidak berlaku pada fringe projection.
7. Simpan raw images, metadata akuisisi dan kalibrasi jika kelak memakai dataset eksperimen.

## Validasi

Jalankan:

```bash
python tests/validate_notebooks.py
```

Skrip memvalidasi struktur `.ipynb` dan menjalankan semua cell termasuk pernyataan `assert`. Tidak memerlukan internet. Laporan tersedia di konsol. Berkas di GitHub tetap **clean (outputs dihapus)** untuk menghindari repository berukuran besar dan untuk memastikan hasil dapat direproduksi.

## Referensi utama

- Takeda, Ina, Kobayashi (1982), *Fourier-transform method of fringe-pattern analysis*. https://doi.org/10.1364/JOSA.72.000156
- Ghiglia & Pritt (1998), *Two-Dimensional Phase Unwrapping*. ISBN 9780471249351.
- NIST TN 1297, uncertainty propagation: https://www.nist.gov/pml/nist-technical-note-1297/nist-tn-1297-appendix-law-propagation-uncertainty
- *Calibration of fringe projection profilometry: A comparative review* (2021): https://doi.org/10.1016/j.optlaseng.2021.106622
- *Modeling the measurement precision of Fringe Projection Profilometry* (2023): https://www.nature.com/articles/s41377-023-01294-0

## Lisensi dan penggunaan

Kode asli dan teks ajar paket ini: MIT License, lihat `LICENSE`. Referensi publikasi ilmiah mengikuti hak cipta masing-masing sumber. Gambar yang muncul saat eksekusi diproduksi oleh kode sintetis di notebook.

## Audit akademik revisi 9/10 (16 November 2026)

- Audit terinci: [AUDIT_AKADEMIK_9_PER_NOTEBOOK.md](AUDIT_AKADEMIK_9_PER_NOTEBOOK.md). Skor editorial setelah revisi (untuk **bahan ajar komputasional berbasis riset**, bukan validasi instrumen): Notebook 01 **9,0**, 02 **9,2**, 03 **9,2**, 04 **9,3**, 05 **9,2**.
- Lima HTML dengan grafik hasil eksekusi tersedia pada folder [`html/`](html/), sehingga dapat dilihat tanpa Python.
- Tambahan uji independen `python tests/test_research_properties.py` mencakup tujuh invarian fisika/numerik. Seluruh notebook dan uji telah lulus pada runtime lokal; belum diuji di Google Colab atau pada alat fisik.
