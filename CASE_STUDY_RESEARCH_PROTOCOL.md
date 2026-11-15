# Protokol riset — tiga studi kasus Digital Fringe Processing

**Sistem Fotonika · S1 semester 6 (pengayaan riset)**  
**Berkas runnable:** `notebooks/05_studi_kasus_engineering.ipynb`  
**Data:** seluruh contoh saat ini **sintetis**, bukan pengukuran laboratorium.

## A. Interferometrik — defleksi pelat tipis akibat tekanan

**Masalah engineering.** Pengukuran non-kontak deformasi pelat reflektif pada skala ratusan nanometer. Bagaimana mendapatkan peta lendutan kuantitatif ketika phase step piezoelektrik tidak tepat?

**Hipotesis yang bisa dibantah.** Dengan rentang fase yang sama dan langkah fase sebenarnya diketahui dari proses kalibrasi, estimator least-squares memberikan bias lebih kecil daripada formula quadrature yang mengasumsikan langkah tepat 90°, terutama jika terdapat phase-step error sistematik.

**Model mekanika dan optika.** Pelat elastis isotropik, tipis, jari-jari `a`, tebal `t`, tepi dijepit, tekanan seragam `q`, lendutan kecil:

\[
D=\frac{Et^3}{12(1-\nu^2)},\qquad w(r)=\frac{q(a^2-r^2)^2}{64D}.
\]

Interferometri refleksi normal: `φ(r) = 4π w(r)/λ0`. Kamera: `Ik=A+Bcos(φ+δk)+ηk`. Indeks bias medium 1; pengukuran relatif terhadap permukaan luar pelat yang dianggap datar.

**Parameter sintetis yang dinyatakan sebelum simulasi:** `E=70 GPa`, `ν=0.33`, `t=1 mm`, `a=20 mm`, `λ0=532 nm`, target lendutan pusat `270 nm`. Beban `q` dihitung dari model pelat, bukan nilai pengukuran. Phase-step perturbations: `(0, 0.09, -0.06, 0.13)` rad ditambahkan ke langkah nominal. Noise aditif terkontrol.

**Baseline algoritma:** 4-step nominal vs least-squares dengan **phase step diketahui (oracle calibration)**. Metode kedua bukan klaim estimasi phase step mandiri.

**Eksperimen lanjutan:** variasi `δ`, contrast loss, drift antarcitra, alokasi eksposur. Metrik: RMSE [nm], signed bias [nm], P95 error, failure rate, efek ROI tepi, serta perubahan akurasi bila kalibrasi phase step salah.

**Kriteria kegagalan model:** w tidak lagi kecil dibanding t, permukaan bukan reflektor ideal, gradien fase terlalu tinggi, speckle, vibrasi selama empat frame, atau indeks bias berubah.

## B. Digital grid/moiré — in-plane displacement dan regangan

**Masalah engineering.** Pengukuran regangan full-field pada permukaan yang dipasangi kisi terkalibrasi. Bagaimana mengetahui perpindahan sejati bila pergeseran melampaui satu interval fase terbungkus?

**Model:** `Ir=A+Bcos(2πx/p+δ)`; `Id=A+Bcos(2π[x−ux]/p+δ)`. Perbedaan fase `Δφ=−2π ux/p`, displacement `ux=−pΔφ/(2π)`. Regangan kecil `εxx=∂ux/∂x`. Jika displacement dinyatakan dalam pixel, diperlukan skala objek mm/pixel agar konversi ke mm benar; strain memerlukan skala referensi yang konsisten.

**Identifiability:** satu pitch hanya memberi `ux modulo p`; menghilangkan rerata displacement bukan solusi untuk mengetahui translasi absolut. Untuk dua pitch `p1=18 px`, `p2=23 px`, synthetic pitch = `82.8 px`. Perkiraan coarse dari beda fase dipakai menentukan integer fringe order; phase pitch halus memperbaiki ketelitian.

**Hipotesis:** dua pitch dapat memperluas rentang unambiguous tetapi memberi varians lebih besar pada estimasi coarse. Menggunakan integer order dari coarse untuk mengoreksi estimasi fine memperbaiki RMSE bila integer order benar.

**Model gangguan:** noise, kisi tidak sinusoidal, rotasi permukaan, perubahan orientasi pitch, kehilangan kontras lokal. **Metrik:** RMSE displacement [px/mm], persentase integer order benar, RMSE strain [µε], resolusi spasial setelah smoothing, dan evaluasi false unwrap.

**Perbandingan terarah:** grid phase analysis, windowed Fourier demodulation, dan normalized cross-correlation/DIC (sebagai metode berbeda; jangan menyamakan secara konseptual). Uji bias–variance tradeoff saat kernel smoothing diperbesar; perubahan strain lokal dapat tertutupi smoothing.

## C. Fringe projection profilometry — 3D calibrated structured light

**Masalah engineering.** Rekonstruksi topografi objek dengan satu proyektor dan satu kamera. Fase fringe mengkodekan korespondensi piksel, bukan perubahan optical path sepanjang arah normal interferometer.

**Model sederhana:** sumbu optik paralel, pinhole, rectified, baseline `b`, focal length efektif `f` dalam pixel, referensi `Z0`, periode proyeksi `P` pixel. `d=fb/Z`, `Δφ = −2π(fb/P)(1/Z−1/Z0)`, `d=d0−PΔφ/(2π)`, `Z=fb/d`, `h=Z0−Z`.

**Parameter sintetis:** `Z0=600 mm`, `b=75 mm`, `f=1200 pixel`, `P=24 pixel`, puncak tinggi sekitar `31 mm`. Data generated with phase-shifted patterns and camera noise.

**Hipotesis:** (i) nonlinearitas radiometrik (contoh `I^γ`, `γ=2.2`) dapat menimbulkan galat fase sistematis; (ii) baseline salah kalibrasi 3% menyebabkan bias tinggi yang tidak hilang dengan menambah langkah fase.

**Algoritma pembanding:** LS 4-step vs 8-step dengan model noise pelatihan terkontrol, dan pengujian noise-free untuk mengisolasi gamma. Tambahkan calibration ablation: baseline, f, projector period, reference plane. Untuk eksperimen sungguhan perlu kamera–proyektor calibrated intrinsics/extrinsics, lens distortion, gamma characterization, synchronisation, independent gauge blocks dan pemetaan validitas/occlusion.

**Metrik:** height RMSE [mm], signed bias [mm], P95 absolute error [mm], error vs distance, failure/invalid pixel rate, calibration parameter sensitivity.

## Prinsip verifikasi umum

1. **Pre-registration ringkas:** rumuskan hipotesis, ROI, input noise, level gangguan, fixed seeds, dan acceptance criteria sebelum benchmarking.
2. **Minimal tugas:** 3 level gangguan × 10 seed × 3 kondisi (random-only, systematic-only, combined), kemudian sajikan median, IQR, bias, RMSE, P95 dan failure fraction. Jumlah ini sesuai latihan kelas; untuk publikasi pertimbangkan power analysis dan dataset independen.
3. **Split data:** tuning parameter di dataset pengembangan, laporkan hasil akhir pada test set baru; jangan tuning dengan ground truth test.
4. **Ablation:** satu faktor diubah setiap eksperimen, kemudian baru kombinasi interaksi; nyatakan bila model gangguan hanyalah pendekatan fenomenologis.
5. **Uncertainty:** NIST GUM `u_c²≈J Σ Jᵀ`, perhatikan kovarians dan parameter yang sama dipakai untuk kalibrasi serta rekonstruksi.
6. **Eksperimen nyata:** data mentah, frame referensi, dark/flat frames, gain/exposure/trigger, geometri, standar kalibrasi, ROI, titik nol, firmware, tanggal, random seeds, versi library, checksum file; pisahkan dari simulasi.
7. **Yang dilarang:** menyebut galat sintetis sebagai resolusi atau ketidakpastian instrumen; menyamakan RMSE antar measurand berbeda; mengklaim kelayakan publikasi sebelum validasi independen.

## Rubrik mini-proyek (100 poin)

| Dimensi | Bobot |
|---|---:|
| Mekanisme fisika, geometri dan asumsi | 25 |
| Derivasi, identifiability dan unit | 20 |
| Implementasi algoritma & reproducibility | 20 |
| Validasi, error budget dan failure analysis | 25 |
| Interpretasi engineering dan keterbatasan | 10 |

## Literatur pegangan

- Grédiac, Sur, Blaysat (2016), *The Grid Method for In-plane Displacement and Strain Measurement*. DOI `10.1111/str.12182`.
- *Calibration of fringe projection profilometry: A comparative review* (2021). DOI `10.1016/j.optlaseng.2021.106622`.
- Xing & Guo (2018), *Correction of projector nonlinearity in multi-frequency phase-shifting fringe projection profilometry*. DOI `10.1364/OE.26.016277`.
- NIST Technical Note 1297, Appendix A, *Law of Propagation of Uncertainty*: https://www.nist.gov/pml/nist-technical-note-1297/nist-tn-1297-appendix-law-propagation-uncertainty.
