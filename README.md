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
| Nur Fathiah Hutriani| 123430021 | fathiahutriani-spec | Melakukan analisis hasil segmentasi, mengevaluasi peta kesalahan (false positive dan false negative), serta menyusun README. |

## 1. Pertanyaan

Augmentasi apa yang membantu U-Net menemukan polip pada citra kolonoskopi, dan augmentasi apa yang tidak? Kenapa?

## 2. Data & pengaturan

<!-- Isi dari Bagian 2 notebook. Pastikan CEPAT = False untuk hasil akhir. -->

- Dataset: Kvasir-SEG (1000 citra kolonoskopi + mask polip), diperkecil ke 128 × 128 piksel.
- Data uji: 200 citra (tetap). Data latih: 200 citra per ulangan.
- Model: U-Net mini (±0,5 juta parameter), 40 epoch, 3 ulangan per skenario.
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

| Skenario | Dice Ulangan 1 | Dice Ulangan 2 | Dice Ulangan 3 | IoU Ulangan 1 | IoU Ulangan 2 | IoU Ulangan 3 |
|---|---|---|---|---|---|---|
| Tanpa augmentasi | 0.591 | 0.637 | 0.596 | 0.471 | 0.525 | 0.470 |
| Flip | 0.609 | 0.689 | 0.578 | 0.506 | 0.578 | 0.451 |
| Kecerahan & kontras | 0.521 | 0.607 | 0.591 | 0.417 | 0.495 | 0.481 |

| Skenario | Dice (rata-rata ± sb) | IoU (rata-rata ± sb) | Dice tiap ulangan |
|---|---|---|---|
| Tanpa augmentasi | 0.608 ± 0.021 | 0.489 ± 0.026 | 0.591, 0.637, 0.596 |
| Flip | 0.625 ± 0.047 | 0.512 ± 0.052 | 0.609, 0.689, 0.578 |
| Kecerahan & kontras | 0.573 ± 0.038 | 0.464 ± 0.034 | 0.521, 0.607, 0.591 |

![Perbandingan Dice](hasil/perbandingan.png)
https://colab.research.google.com/drive/1w1V2An1FVl76gLMSBKYOQXX3Zs7agU-a#scrollTo=14dd4f45&fullscreenOutput=true

## 5. Analisis peta kesalahan

![Peta kesalahan](hasil/peta_kesalahan.png)

<!-- Hijau = benar, merah = false positive, biru = false negative.
     Pertanyaan pemandu: Polip seperti apa yang sering terlewat (kecil? datar? warna mirip dinding usus?)
     Apa yang sering dikira polip padahal bukan? Apakah augmentasi memperbaiki kasus sulit? -->
https://colab.research.google.com/drive/1w1V2An1FVl76gLMSBKYOQXX3Zs7agU-a#scrollTo=0b460587&fullscreenOutput=true

## 6. Pembahasan & kesimpulan

<!-- Bandingkan hasil dengan hipotesis di Bagian 3. Hipotesis yang ternyata salah tetap bernilai,
     asalkan dijelaskan kenapa. Perbedaan kecil (lebih kecil dari simpangan baku) belum tentu berarti. -->

Penggunaan 40 epoch bertujuan memberikan kesempatan kepada model U-Net untuk mempelajari pola dan karakteristik polip secara lebih optimal. Peningkatan jumlah epoch diharapkan dapat membantu model menghasilkan segmentasi yang lebih baik dibandingkan pengujian sebelumnya. Pada skenario augmentasi Flip, transformasi horizontal dan vertikal digunakan untuk memperkaya variasi orientasi citra kolonoskopi. Teknik ini diharapkan membantu model mengenali polip meskipun mengalami perubahan orientasi. Pada skenario Kecerahan dan Kontras, parameter augmentasi diturunkan menjadi 0,1 untuk mengurangi perubahan intensitas visual yang terlalu besar. Pengaturan tersebut bertujuan mempertahankan karakteristik warna dan tekstur polip sekaligus memberikan variasi pencahayaan pada data pelatihan.

Hasil akhir dari ketiga skenario kemudian dibandingkan berdasarkan nilai Dice dan IoU. Skenario dengan nilai Dice dan IoU lebih tinggi menunjukkan kesesuaian hasil segmentasi yang lebih baik terhadap anotasi dokter. Selain nilai rata-rata, simpangan baku juga diperhatikan untuk mengetahui konsistensi hasil pengulangan eksperimen. Melalui pengujian ini, dapat diketahui apakah peningkatan jumlah epoch dan penyesuaian parameter augmentasi mampu memperbaiki performa segmentasi polip pada citra kolonoskopi.

## 7. Pembagian kerja

<!-- Siapa mengerjakan apa. Penilaian proses kerja dilihat dari riwayat commit tiap anggota. -->

| Nama | NIM | Username GitHub | Kontribusi utama |
|---|---|---|---|
| Azaria Fadilah | 123430065 | Azafadilah | Menyiapkan lingkungan eksperimen, menjalankan model U-Net, melakukan pengujian, dan mengumpulkan hasil eksperimen. |
| Jihan Nailah Efrani | 123430005 | jihannaila03-png | Merancang skenario augmentasi citra, menentukan hipotesis, menganalisis pengaruh augmentasi, dan mengerjakan README. |
| Nur Fathiah Hutriani | 123430021 | fathiahutriani-spec | Menganalisis hasil segmentasi, mengevaluasi peta kesalahan (false positive dan false negative), serta menyusun README. |

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
