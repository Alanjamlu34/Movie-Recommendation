# **Latar Belakang:**
Rekomendasi film adalah salah satu aplikasi populer dalam sistem rekomendasi, yang membantu pengguna menemukan film yang sesuai dengan preferensi mereka dari jutaan pilihan yang tersedia. Dengan semakin banyaknya konten film yang diproduksi setiap hari, sistem rekomendasi yang efektif menjadi sangat penting untuk meningkatkan pengalaman pengguna dan engagement pada platform streaming.
Proyek ini penting karena dapat memberikan rekomendasi film yang lebih relevan kepada pengguna berdasarkan genre dan atribut film yang telah mereka sukai sebelumnya, sehingga mengurangi waktu pencarian film dan meningkatkan kepuasan pengguna. Analisis Content-Based Filtering pada data rating film dengan fitur movieId, title, dan genres menjadi fokus utama karena dapat memberikan rekomendasi yang personal tanpa bergantung pada preferensi pengguna lain.
Beberapa penelitian menunjukkan bahwa Content-Based Filtering mampu memberikan rekomendasi yang akurat terutama jika data fitur film lengkap dan representatif (Schafer et al., 2007; Lops et al., 2011). Dataset film yang umum digunakan adalah [Movies & Ratings for Recommendation System](https://www.kaggle.com/datasets/nicoletacilibiu/movies-and-ratings-for-recommendation-system) dari kaggle, yang menyediakan atribut seperti movieId, title, genres dan data lainnya yang sangat membantu dalam mengukur kesamaan antar film.

# **Business Understanding**
## Problem Statements:
- Pengguna platform film sering merasa kesulitan memilih film yang sesuai dengan preferensi mereka karena banyaknya pilihan yang tersedia.
- Rekomendasi yang diberikan belum personal dan kurang relevan dengan selera individu pengguna.

## Goals:
- Mengembangkan sistem rekomendasi film yang dapat memberikan rekomendasi personal berbasis fitur film yang telah disukai pengguna.
- Meningkatkan tingkat kepuasan pengguna terhadap rekomendasi yang diberikan oleh sistem.
- Meningkatkan engagement dan durasi penggunaan platform melalui rekomendasi yang relevan.

## Solution Approach
**Pendekatan 1: Content-Based Filtering**
Menggunakan atribut film seperti movieId, title, dan genres, sistem akan membangun profil pengguna berdasarkan film yang telah diberi rating tinggi. Rekomendasi diberikan dengan mencari film lain yang memiliki genre atau atribut serupa dengan film yang sudah disukai pengguna tersebut. Contohnya, jika pengguna sering memberi rating tinggi pada film bergenre "Action" dan "Adventure," sistem akan merekomendasikan film-film dengan genre yang sama atau mirip.

- **Kelebihan:**
Tidak memerlukan data pengguna lain (tidak bergantung pada data kolaborasi).
Rekomendasi yang personal dan spesifik berdasarkan preferensi pengguna.

- **Kekurangan:**
Sulit merekomendasikan film baru yang belum pernah diberi rating oleh pengguna (cold start item problem).
Terbatas pada fitur yang tersedia (genre saja mungkin kurang representatif).

**Pendekatan 2: Collaborative Filtering**
Sistem merekomendasikan film berdasarkan kesamaan preferensi antar pengguna. Film yang disukai oleh pengguna dengan profil serupa akan direkomendasikan kepada pengguna target, tanpa harus bergantung pada atribut film.

- **Kelebihan:**
Dapat menemukan hubungan tersembunyi antar film yang tidak langsung terlihat dari genre saja.
Lebih fleksibel dalam memberikan rekomendasi yang variatif.

- **Kekurangan:**
Memerlukan data rating pengguna dalam jumlah besar.
Rentan terhadap masalah cold start untuk pengguna baru yang belum memberi rating.

# Data Preparation
Pada tahap ini, dilakukan beberapa proses persiapan data untuk memastikan kualitas data yang akan digunakan dalam analisis Content-Based Filtering sistem rekomendasi film. Dataset yang digunakan berasal dari dua file CSV yaitu movie.csv dan rating.csv yang diunduh dari sumber Kaggle dengan path sebagai berikut:
`path = kagglehub.dataset_download("nicoletacilibiu/movies-and-ratings-for-recommendation-system")`
- Dataset yang digunakan
movie.csv: Memiliki kolom movieId (integer), title (object/string), dan genres (object/string).
rating.csv: Memiliki kolom userId (integer), movieId (integer), rating (float), dan timestamp (integer).
- Langkah-langkah Data Preparation
  - Memuat dataset;
      Kedua file CSV movie.csv dan rating.csv dimuat ke dalam DataFrame pandas untuk memudahkan analisis.
  - Memeriksa tipe data dan struktur dataset;
      Tipe data setiap kolom dicek untuk memastikan kesesuaian dengan tipe data yang diharapkan (numerik untuk movieId, userId, dan rating; string untuk title dan genres).
  - Analisis statistik deskriptif awal;
      Menggunakan .describe() untuk melihat ringkasan statistik data numerik dan memahami distribusi data movieId dan rating.

      Contoh hasil analisis:
      - Pada movie.csv, terdapat 9.742 film.
      - Pada rating.csv, terdapat 100.836 data rating dengan nilai rating dari 0.5 hingga 5.0.

  - Mengurutkan data rating berdasarkan nilai rating;
      Data rating diurutkan berdasarkan kolom rating dari yang terendah ke tertinggi untuk memudahkan analisis distribusi nilai rating.
  - Membersihkan data jika ada missing values atau data tidak valid;
      - Memastikan tidak ada nilai kosong (missing) di kolom penting seperti movieId, title, dan genres di dataset film.
      - Memastikan rating hanya berisi nilai yang valid sesuai rentang 0.5 hingga 5.0;
  - Pengolahan kolom genre;
      Kolom genres yang berupa string dengan genre yang dipisahkan tanda | dapat diolah lebih lanjut untuk analisis similarity antar film, misalnya dengan transformasi menjadi format list atau menggunakan teknik vectorisasi (TF-IDF, CountVectorizer).
- Alasan Tahapan Data Preparation
    - Memuat dan memeriksa tipe data memastikan data dapat diproses tanpa error dan sesuai dengan metode analisis yang akan digunakan.
    - Analisis statistik deskriptif membantu mengenali distribusi data dan outlier sehingga dapat mengantisipasi masalah saat membangun model rekomendasi.
    - Pengurutan rating berguna untuk visualisasi dan memahami preferensi rating pengguna secara umum.
    - Pembersihan data memastikan data bebas dari nilai kosong atau tidak valid, yang dapat mengganggu proses pembelajaran model.
    - Pengolahan kolom genres krusial karena genre menjadi fitur utama dalam content-based filtering, sehingga perlu diolah menjadi representasi yang dapat dihitung kemiripannya antar film.
# Modeling and Result
  - Sistem Rekomendasi: Content-Based Filtering
    Untuk menyelesaikan permasalahan rekomendasi film secara personal tanpa bergantung pada preferensi pengguna lain, kami membangun Content-Based Recommender System. Pendekatan ini merekomendasikan film kepada pengguna berdasarkan kemiripan konten dari film yang disukai sebelumnya, khususnya atribut genres.
  - Proses Modeling
    - Fitur yang digunakan
      - Kolom genres dari movie.csv digunakan sebagai dasar untuk menghitung kemiripan antar film.
      - Genre diubah dari string menjadi representasi numerik menggunakan teknik TF-IDF Vectorization. Ini memungkinkan sistem untuk mengenali bobot pentingnya genre tertentu dalam film.
    - Perhitungan Kemiripan
      - Digunakan Cosine Similarity untuk mengukur tingkat kemiripan antar film berdasarkan vektor genre.
      - Semakin tinggi nilai cosine similarity, semakin mirip genre film tersebut dengan film referensi.
    - Pembuatan Fungsi Rekomendasi;
          Fungsi movie_recommendations() dibuat untuk menerima input judul film, kemudian mengembalikan daftar film lain yang memiliki genre paling mirip.
  - Top-N Recommendation (N = 10)
    Sebagai contoh, diberikan input:
    `movie_recommendations('Toy Story (1995)')`
    Output sistem adalah daftar 10 film teratas dengan genre yang paling mirip dengan *Toy Story (1995)*, yang memiliki _genre: Adventure|Animation|Children|Comedy|Fantasy_.

    | No | Title                                          | Genres                                                  |
    |:--:|------------------------------------------------|---------------------------------------------------------|
    | 1  | Adventures of Rocky and Bullwinkle, The (2000) | Adventure \| Animation \| Children \| Comedy \| Fantasy |
    | 2  | Antz (1998)                                    | Adventure \| Animation \| Children \| Comedy \| Fantasy |
    | 3  | Emperor's New Groove, The (2000)               | Adventure \| Animation \| Children \| Comedy \| Fantasy |
    | 4  | Shrek the Third (2007)                         | Adventure \| Animation \| Children \| Comedy \| Fantasy |
    | 5  | Moana (2016)                                   | Adventure \| Animation \| Children \| Comedy \| Fantasy |
    | 6  | Turbo (2013)                                   | Adventure \| Animation \| Children \| Comedy \| Fantasy |
    | 7  | The Good Dinosaur (2015)                       | Adventure \| Animation \| Children \| Comedy \| Fantasy |
    | 8  | Monsters, Inc. (2001)                          | Adventure \| Animation \| Children \| Comedy \| Fantasy |
    | 9  | Wild, The (2006)                               | Adventure \| Animation \| Children \| Comedy \| Fantasy |
    | 10 | Atlantis: The Lost Empire (2001)               | Adventure \| Animation \| Children \| Comedy \| Fantasy |
  - Hasil
    - Sistem berhasil memberikan 10 rekomendasi teratas yang sangat relevan dengan film input.
    - Semua hasil memiliki genre yang sama persis dengan Toy Story (1995), yang membuktikan bahwa sistem mampu mengidentifikasi kesamaan konten dengan baik.
    - Rekomendasi ini cocok untuk pengguna yang menyukai film animasi petualangan keluarga dengan unsur fantasi dan komedi.

