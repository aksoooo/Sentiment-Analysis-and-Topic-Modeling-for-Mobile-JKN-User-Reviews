# 🏥 Analisis Sentimen & Pemodelan Topik — Aplikasi Mobile JKN

Final Project mata kuliah **Web Mining** — Kelompok 3

Proyek ini menganalisis ulasan pengguna aplikasi **Mobile JKN** (BPJS Kesehatan) di Google Play Store menggunakan pendekatan *Natural Language Processing*, mencakup pelabelan sentimen berbasis LLM, perbandingan empat model Transformer untuk klasifikasi sentimen, serta pemodelan topik (*topic modelling*) untuk mengidentifikasi isu-isu utama yang dibicarakan pengguna.

## 👥 Tim

- Fariz Naufal Gustoro
- Surya Winaldi Yakin
- Alif Ramdhani Izharulhaq
- Mohammad Aqsha Dwinovryan

## 📋 Ringkasan Proyek

Data dikumpulkan langsung dari Google Play Store (20.000 ulasan), dilabeli sentimennya secara semi-otomatis menggunakan pendekatan **LLM-Assisted Annotation**, lalu dibersihkan dan dianalisis melalui dua jalur:

1. **Topic Modelling** — mengidentifikasi topik-topik utama yang dibicarakan pengguna menggunakan BERTopic (embedding IndoBERT + UMAP + HDBSCAN).
2. **Sentiment Modelling** — membandingkan performa empat model Transformer (IndoBERT, IndoBERTweet, XLM-RoBERTa, mBERT) untuk klasifikasi sentimen tiga kelas (Positif/Negatif/Netral).

Kedua hasil tersebut kemudian diintegrasikan untuk melihat distribusi sentimen per topik, sebagai dasar penyusunan rekomendasi pengembangan produk.

> Sesuai arahan dosen pembimbing, fokus perbandingan model sentimen adalah **antar model Transformer**, bukan Transformer vs Machine Learning klasik — karena perbandingan yang terakhir dinilai kurang memberi kontribusi baru secara akademis.

## 📁 Struktur Repository

```
.
├── README.md
└── Mobile JKN Sentiment Analysis & Topic Modelling.ipynb   # Notebook utama (satu pipeline end-to-end)
```

## 🗂️ Dataset

- **Sumber**: ulasan publik aplikasi Mobile JKN (`app.bpjs.mobile`) di Google Play Store, diambil menggunakan `google-play-scraper`.
- **Jumlah data mentah**: 20.000 ulasan (Desember 2025 – Mei 2026).
- **Setelah preprocessing**: 19.306 ulasan bersih.
- **Distribusi sentimen**: Positif 54,3% · Negatif 37,6% · Netral 8,0% (ditangani dengan Focal Loss karena tidak seimbang).
- **Kualitas anotasi**: Cohen's Kappa = 0.8394 (*Almost Perfect Agreement*) antara label LLM dan anotasi manusia pada sampel 1.000 data.

## 🤖 Model Sentimen yang Dibandingkan

| Model | Accuracy | Macro-F1 | Precision | Recall |
|---|---|---|---|---|
| **IndoBERT** 🥇 | 0.8667 | **0.8134** | 0.7981 | 0.8724 |
| XLM-RoBERTa | 0.8150 | 0.7446 | 0.7421 | 0.7904 |
| mBERT | 0.7971 | 0.7299 | 0.7360 | 0.7829 |
| IndoBERTweet | 0.7799 | 0.7216 | 0.7339 | 0.7876 |

**IndoBERT** terpilih sebagai model terbaik dan digunakan untuk inferensi sentimen pada tahap integrasi akhir.

## 🧩 Topic Modelling

- **Metode**: BERTopic dengan sentence embedding IndoBERT (`firqaaa/indo-sentence-bert-base`), reduksi dimensi UMAP, dan clustering HDBSCAN.
- **Hasil**: 52 topik teridentifikasi dari 19.306 ulasan, dengan ±26% ulasan tergolong outlier.
- **Topik dengan volume terbesar**: OTP & Verifikasi SMS (1.759 ulasan), diikuti ulasan apresiatif umum (membantu/bermanfaat), serta topik seputar rujukan rumah sakit.

## 🚀 Cara Menjalankan

1. Buka `Mobile JKN Sentiment Analysis & Topic Modelling.ipynb` di Google Colab (disarankan) atau Jupyter dengan GPU aktif (T4/A100).
2. Jalankan sel-sel secara berurutan dari atas ke bawah, dimulai dari **Setup Lingkungan Kerja**.
3. Jika Anda sudah memiliki data hasil scraping/anotasi, lewati proses scraping dan langsung muat data pada bagian **"Memuat Data Hasil Anotasi LLM"**.
4. Bagian II (Modelling Sentimen) memerlukan GPU dan waktu komputasi yang cukup besar (training 4 model Transformer × 5 epoch).

### Requirements

Seluruh dependensi diinstal otomatis di sel pertama notebook, meliputi:

```
google-play-scraper, transformers>=4.41, datasets, torch, scikit-learn,
pandas, numpy, matplotlib, seaborn, bertopic, umap-learn, hdbscan,
PySastrawi, langdetect, wordcloud, plotly, accelerate, evaluate, openpyxl
```

## 📚 Referensi

1. Devlin, J., Chang, M. W., Lee, K., & Toutanova, K. (2019). BERT: Pre-training of Deep Bidirectional Transformers for Language Understanding. *NAACL-HLT*.
2. Wilie, B., et al. (2020). IndoNLU: Benchmark and Resources for Evaluating Indonesian Natural Language Understanding. *AACL-IJCNLP*.
3. Koto, F., et al. (2021). IndoLEM and IndoBERT: A Benchmark Dataset and Pre-trained Language Model for Indonesian NLP. *COLING*.
4. Conneau, A., et al. (2020). Unsupervised Cross-lingual Representation Learning at Scale. *ACL*.
5. Grootendorst, M. (2022). BERTopic: Neural topic modeling with a class-based TF-IDF procedure. *arXiv*.
6. Gilardi, F., Alizadeh, M., & Kubli, M. (2023). ChatGPT outperforms crowd workers for text-annotation tasks. *PNAS*.
7. Pang, B., & Lee, L. (2008). Opinion Mining and Sentiment Analysis. *Foundations and Trends in Information Retrieval*.
8. Lin, T.-Y., et al. (2017). Focal Loss for Dense Object Detection. *ICCV*.

---

*Disusun untuk Final Project Web Mining — Kelompok 3, Fakultas Matematika dan Ilmu Pengetahuan Alam, Universitas Indonesia.*
