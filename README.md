# Segmentasi Polip pada Citra Kolonoskopi dengan U-Net

<!--
  TEMPLATE LAPORAN — Tugas P10 Kecerdasan Buatan (BM25-41204), Teknik Biomedis ITERA.
  Petunjuk lengkap: https://mctm.web.id/ai-bm/tugas

  Cara memakai:
  - Ganti semua teks yang diawali "✍️" dengan tulisan kelompokmu, lalu hapus tanda ✍️-nya.
  - Blok komentar seperti ini adalah petunjuk; tidak tampil di GitHub. Boleh dihapus.
  - Gambar diambil dari folder hasil/ yang dibuat otomatis oleh notebook.
  - Jangan hapus judul-judul bagian (##).
-->

**Tugas P10 — Kecerdasan Buatan (BM25-41204), Teknik Biomedis ITERA**

## Anggota kelompok

| Nama | NIM | Username GitHub | Kontribusi utama |
|---|---|---|---|
| Azaria Fadilah | 123430065 | Azafadilah | Menyiapkan lingkungan eksperimen, menjalankan model U-Net, serta melakukan pengujian dan pengumpulan hasil eksperimen. |
| Jihan Nailah Efrani | 123430005 | jihannaila03-png | Merancang skenario augmentasi citra, menentukan hipotesis, serta menganalisis pengaruh augmentasi dan mengerjakan README. |
| Nur Fathiah| 123430021 | fathiahutriani-spec | Melakukan analisis hasil segmentasi, mengevaluasi peta kesalahan (false positive dan false negative), serta menyusun README. |

## 1. Pertanyaan

Augmentasi apa yang membantu U-Net menemukan polip pada citra kolonoskopi, dan augmentasi apa yang tidak? Kenapa?

## 2. Data & pengaturan

<!-- Isi dari Bagian 2 notebook. Pastikan CEPAT = False untuk hasil akhir. -->

- Dataset: Kvasir-SEG (1000 citra kolonoskopi + mask polip), diperkecil ke 128 × 128 piksel.
- Data uji: 200 citra (tetap). Data latih: 200 citra per ulangan.
- Model: U-Net mini (±0,5 juta parameter), 10 epoch, 3 ulangan per skenario.
- Dijalankan di: Google Colab T4.

![Contoh data](hasil/contoh_data.png)

## 3. Skenario augmentasi & hipotesis

<!-- Tulis bagian ini SEBELUM menjalankan eksperimen. Commit dulu, baru jalankan.
     Alasan klinis = kenapa variasi ini mungkin (atau tidak mungkin) terjadi pada kolonoskopi sungguhan. -->

| Skenario | Augmentasi (kode) | Alasan klinis | Hipotesis: Dice naik/turun/sama? |
|---|---|---|---|
| Tanpa augmentasi | — | Pembanding | — |
| Flip | `v2.Compose([v2.RandomHorizontalFlip(), v2.RandomVerticalFlip()])` | Orientasi kamera kolonoskopi dapat berubah. Flip digunakan untuk melatih model mengenali polip pada orientasi berbeda. | Naik, dibandingkan tanpa augmentasi, karena model diharapkan tidak bergantung pada orientasi polip. |
| Kecerahan & kontras |  `v2.ColorJitter(brightness=0.3, contrast=0.3)` | Tampilan pencahayaan citra dapat bervariasi. Augmentasi ini meniru variasi terang dan kontras. | Naik dibandingkan tanpa augmentasi, karena model diharapkan lebih tahan terhadap variasi pencahayaan. |

Contoh augmentasi yang dicoba:

![Contoh augmentasi](hasil/augmentasi_flip.png)
<!-- Ganti nama file sesuai skenario kalian (lihat isi folder hasil/). -->

## 4. Hasil

<!-- Tempel isi hasil/ringkasan.md di sini. -->

| Skenario | Dice (rata-rata ± sb) | IoU (rata-rata ± sb) | Dice tiap ulangan |
|---|---|---|---|
| Tanpa augmentasi | 0.473 ± 0.024 | 0.350 ± 0.025 | 0.445, 0.504, 0.470 |
| Flip | 0.462 ± 0.039 | 0.344 ± 0.035 | 0.449, 0.515, 0.422 |
| Kecerahan & kontras | 0.378 ± 0.045 | 0.273 ± 0.036 | 0.364, 0.331, 0.438 |

![Perbandingan Dice](hasil/perbandingan.png)
 

## 5. Analisis peta kesalahan

![Peta kesalahan](hasil/peta_kesalahan.png)

<!-- Hijau = benar, merah = false positive, biru = false negative.
     Pertanyaan pemandu: Polip seperti apa yang sering terlewat (kecil? datar? warna mirip dinding usus?)
     Apa yang sering dikira polip padahal bukan? Apakah augmentasi memperbaiki kasus sulit? -->

https://colab.research.google.com/drive/1MLGlXVLll_pw2kg1nJGMdbspriJ0bM0a#scrollTo=0b460587&fullscreenOutput=true

## 6. Pembahasan & kesimpulan

<!-- Bandingkan hasil dengan hipotesis di Bagian 3. Hipotesis yang ternyata salah tetap bernilai,
     asalkan dijelaskan kenapa. Perbedaan kecil (lebih kecil dari simpangan baku) belum tentu berarti. -->

✍️

## 7. Pembagian kerja

<!-- Siapa mengerjakan apa. Penilaian proses kerja dilihat dari riwayat commit tiap anggota. -->

✍️

## 8. Penggunaan AI

<!-- Wajib diisi, walau tidak memakai AI (tulis "Tidak memakai AI").
     Contoh: "Claude Code (CLI) untuk menulis skenario augmentasi dan memperbaiki error di Bagian 4.
     Draf Bagian 6 ditulis sendiri, lalu dirapikan bahasanya dengan bantuan AI." -->

✍️

## Cara menjalankan ulang

```bash
uv sync
uv run jupyter lab segmentasi_polip.ipynb
```

Atau buka notebook di Google Colab (Runtime → Change runtime type → T4 GPU).

## Referensi

- Jha, D., dkk. (2020). Kvasir-SEG: A Segmented Polyp Dataset. *MultiMedia Modeling (MMM 2020)*, 451–462.
- Ronneberger, O., Fischer, P., & Brox, T. (2015). U-Net: Convolutional Networks for Biomedical Image Segmentation. *MICCAI*. arXiv:1505.04597.
- ✍️ tambahkan referensi lain yang kalian pakai
