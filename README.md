# Laporan Proyek Machine Learning - Michael Eko
---

## Domain Proyek
---

### Latar Belakang
Karena internet tumbuh secara eksponensial, demikian pula ukuran dan kompleksitas banyak halaman web. Bagi  pengguna situs web ini, menemukan informasi yang mereka cari menjadi semakin sulit dan memakan waktu. Untuk membantu pengguna menemukan informasi yang sesuai dengan minat mereka, sebuah situs web dapat disesuaikan. Sistem rekomendasi dapat meningkatkan situs  untuk pengguna individu dengan menambahkan hyperlink secara dinamis. Sistem rekomendasi merupakan model aplikasi dari berdasarkan keadaan dan keinginan pengguna. Oleh karena itu, sistem rekomendasi membutuhkan model rekomendasi yang sesuai agar apa yang direkomendasikan sesuai dengan keinginan pengguna dan memudahkan pengguna dalam mengambil keputusan yang tepat untuk menentukan item yang akan dipilih.

Tren membaca buku belakangan ini mengalami perubahan yang sangat signifikan terutama pada masa pandemi. Karena banyak orang yang ingin menghabiskan waktu untuk menambah wawasan. Konsumen saat ini cenderung mengakses dan membeli buku secara online daripada pergi ke toko buku, yang jelas menimbulakn masalah pertumbuhan data yang terlalu cepat di internet yang menyebabkan terlalu banyak informasi. Hal ini dapat menyulitkan seseorang untuk mendapatkan informasi buku secara cepat dan pada saat dibutuhkan. Untuk itu diperlukan sistem rekomendasi yang dapat membantu seseorang menemukan informasi sebuah buku yang sesuai dengan kebutuhannya.

## Business Understanding
---

### Problem Statement
Berdasarkan pada latar belakang di atas, permasalahan yang dapat diselesaikan pada proyek ini adalah bagaimana membangun suatu sistem rekomendasi buku menggunakan metode *content based filtering* guna untuk mempermudah pengguna memperoleh rekomendasi.

### Goals
Tujuan proyek ini dibuat adalah untuk mempermudah pengguna dalam menemukan buku yang diinginkan.

### Solution Statement
Solusi yang dapat dilakukan agar goals terpenuhi adalah membuat model sistem rekomendasi untuk memberikan beberapa rekomendasi buku dengan menggunakan pendekatan *content based filtering*.

## Data Understanding
---

Dataset yang digunakan pada proyek ini diambil dari website kaggle [*Book-Crossing: User review ratings*](https://www.kaggle.com/datasets/ruchi798/bookcrossing-dataset). Pada proyek ini kita akan menggunakan 2 dataset yaitu *Book Data with Categories.csv* dan *BX-Book-Ratings.csv*. 

Dataset pertama yaitu *Book Data with Categories.csv* mempunyai total 1.031.175 data dan 18 kolom dengan informasi sebagai berikut :
* user_id : merupakan id dari user
* location : merupakan lokasi user tinggal
* age : merupakan umur dari user
* isbn : merupakan kode pengidentifikasi buku
* rating : merupakan rating yang user berikan untuk buku
* book_title : merupakan judul dari buku
* book_author : merupakan penulis dari buku
* year_of_publication : merupakan tahun publikasi buku
* publisher : merupakan penerbit buku
* img_s/img_m/img_l : merupakan cover dari buku
* summary : merupakan sinopsis dari buku
* language : merupakan bahasa terjemahan buku
* category : merupakan kategori buku
* city : merupakan kota buku tersebut dibeli
* state : merupakan provinsi buku tersebut dibeli
* country : merupakan negara buku tersebut dibeli

Dataset kedua yaitu *BX-Book-Ratings.csv* memiliki total 1.149.780 data dan 3 kolom dengan informasi sebagai berikut :
* User-ID : merupakan id dari user yang memberikan rating
* ISBN : merupakan kode pengidentifikasi buku
* Book-Rating : merupakan rating yang diberikan pada buku

### Exploratory Data Analysis
Sebelum melakukan pemrosesan data, ada baiknya untuk mengeksplor data untuk mengetahui keadaan data seperti mencari *missing value*, menghapus fitur yang tidak relevan, menghapus duplikat pada kedua dataset, *merging dataset* dan menentukan fitur yang akan digunakan.

* Menangani *missing value*
    Pada dataset pertama terdapat 3 kolom yang memiliki missing value yaitu *city, state* dan *country*. Karena kolom ini tidak relevan pada proyek ini, maka kita dapat menghapus kolom ini. Lalu pada dataset kedua tidak memiliki *missing value*.

* Menghapus fitur yang tidak relevan
    Pada tahap ini kita akan menghapus beberapa fitur yang tidak memberikan informasi terkait buku seperti, *'user_id', 'location', 'age', 'rating', 'year_of_publication', 'img_s', 'img_m', 'img_l', 'Summary', 'Language'*.

* Menghapus duplikat
    Karena banyaknya judul buku yang duplikasi pada dataset pertama dan isbn pada dataset kedua, maka kita harus menghapus data yang berduplikat agar tidak membuang waktu dan ruang komputasi.

* *Merging dataset*
    Pada tahap ini kita akan menyatukan kedua dataset agar menjadi satu yang akan disatukan pada kolom *isbn*.
    
# Data Preparation
---

### Menghapus fitur yang tidak diperlukan
Pada proyek ini kita hanya memerlukan beberapa kolom saja yang memberikan informasi penting pada buku, seperti *book_title, book_author, publisher* dan *category*, sehingga kita dapat menghapus semua kolom kecuali kolom-kolom tersebut.

### Membersihkan fitur *category*
Karena pada fitur category terdapat *noise* pada konten (terbungkus pada kurung kotak seperti pada tabel), maka kita harus membersihkan kurung kotak tersebut.

| book_title           | book_author          | publisher               | category           |
|----------------------|----------------------|-------------------------|--------------------|
| Classical Mythology  | Mark P. O. Morford   | Oxford University Press | ['Social Science'] |
| Clara Callan         | Richard Bruce Wright | HarperFlamingo Canada   | ['Actresses']      |
| Decision in Normandy | Carlo D'Este         | HarperPerennial         |      ['1940-1949'] |

*Category* yang telah dibersihkan akan kita tampung pada kolom baru dengan nama *clean_category*.

| book_title           | book_author          | publisher               | category           | clean_category |
|----------------------|----------------------|-------------------------|--------------------|----------------|
| Classical Mythology  | Mark P. O. Morford   | Oxford University Press | ['Social Science'] | Social Science |
| Clara Callan         | Richard Bruce Wright | HarperFlamingo Canada   | ['Actresses']      | Actresses      |
| Decision in Normandy | Carlo D'Este         | HarperPerennial         |      ['1940-1949'] | 1940-1949      |

### Menghapus duplikat pada *category*
Karena dataset ini memiliki total 1.031.175 data, maka hal tersebut memungkinkan bahwa terdapat *category* yang muncul berulang-ulang. Hal tersebut akan membuat model tidak berjalan baik sehingga kita harus menghapus duplikat pada fitur *category*

### Merging dataset
Karena kita akan menghitung *precision* dari sistem rekomendasi kita, maka kita memerlukan rating. Untuk memakai rating kita perlu *merging* dataset kita pada fitur *isbn*.

| book_title           | book_author          | publisher               | Category           | Book-Rating |
|----------------------|----------------------|-------------------------|--------------------|-------------|
| Classical Mythology  | Mark P. O. Morford   | Oxford University Press | ['Social Science'] | 0           |
| Clara Callan         | Richard Bruce Wright | HarperFlamingo Canada   | ['Actresses']      | 5           |
| Decision in Normandy | Carlo D'Este         | HarperPerennial         | ['1940-1949']      | 0           |

# Modeling
---

Model yang akan digunakan proyek kali ini yaitu menggunakan pendekatan *content based filtering* menggunakan TfidfVectorizer.

### *Content Based Filtering*
*Content based filtering* menggunakan informasi tentang beberapa item/data untuk merekomendasikan kepada pengguna sebagai referensi mengenai informasi yang digunakan sebelumnya. Tujuan dari *content based filtering* adalah untuk memprediksi persamaan sejumlah informasi yang didapat dari pengguna. Sebagai contoh, seorang pembaca sedang membaca buku tentang saham. Platform baca buku online secara sistem akan merekomendasikan si pengguna untuk membaca buku lain yang berhubungan dengan saham.

Dalam pembuatannya, *content based filtering* menggunakan konsep perhitungan *vectoru*, [TF-IDF](https://en.wikipedia.org/wiki/Tf%E2%80%93idf), dan [Cosine Similarity](https://en.wikipedia.org/wiki/Cosine_similarity) yang intinya dikonversikan dari data/teks menjadi berbentuk vector.

![](https://miro.medium.com/max/1400/1*BcXAhvp6xChQ85B7yYbkXA.png)

##### Kelebihan
* Tidak memerlukan data apapun terhadap pengguna
* Dapat merekomendasikan item khusus

##### Kelemahan
* Membutuhkan banyak pengetahuan suatu domain
* Membuat rekomendasi berdasarkan minat pengguna yang ada saja

##### Recommendation Result
Untuk mendapatkan rekomendasi, kita menggunakan buku yang berjudul "*The Mummies of Urumchi*" dengan kategori "*Design*". Jika sistem rekomendasi berjalan dengan baik, maka kita akan mendapatkan hasil buku dengan kategori yang sama yaitu "*Design*". Hasil rekomendasi di bawah sudah diurutkan dari Book-Rating terbesar.

| book_title                                                                                                         | book_author             | publisher                | Category | Book-Rating |
|--------------------------------------------------------------------------------------------------------------------|-------------------------|--------------------------|----------|-------------|
| Hole in the Wall                                                                                                   | Judith M. Grieshaber    | Cantz Editions           | Design   | 10          |
| Ready-To-Use Celtic Borders on Layout Grids (Dover Clip Art Series)                                                | Mallory Pearce          | Dover Publications       | Design   | 9           |
| Fairie-Ality: The Fashion Collection                                                                               | David Ellwand           | Candlewick Press (MA)    | Design   | 9           |
| Treasury of Fantastic and Mythological Creatures: 1087 Renderings from Historic Sources (Dover Pictorial Archives) | Richard Huber           | Dover Publications       | Design   | 7           |
| Type Rules!                                                                                                        | Ilene Strizver          | North Light Books        | Design   | 6           |
| Ready-To-Use School and Education Illustrations (Dover Clip-Art Series)                                            | Tom Tierney             | Dover Publications       | Design   | 0           |
| Zen Style: Balance and Simplicity for Your Home                                                                    | Jane Tidbury            | Universe Publishing (NY) | Design   | 0           |
| Art of the Book : From Medieval Manuscript to Graphic Novel (Victoria and Albert Museum Studies)                   | James Bettley           | Victoria & Albert Museum | Design   | 0           |
| Stained Glass : From its Origins to the Present                                                                    | Virginia Chieffo Raguin | Harry N Abrams           | Design   | 0           |
| Abstract and Geometric Patterns: Clip Art (North Light Clip Art)                                                   | Not Applicable (Na )    | F & W Pubns              | Design   | 0           |

Dapat kita lihat bahwa ada beberapa buku yang memiliki rating kurang bagus muncul dalam rekomendasi. Inilah salah satu kelemahan dari sistem rekomendasi dengan pendekatan *content based filtering* karena tidak memberikan rekomendasi sesuai dengan preferensi dari pengguna.

# Evaluation
---

Untuk evaluasi dari sistem rekomendasi dengan pendekatan *content based filtering* kita dapat menggunakan salah satu metric yaitu *precision@K*. Apa itu *precision*? *Precision* adalah perbandingan antara *True Positive (TP)* dengan banyaknya data yang diprediksi positif. Atau juga bisa ditulis secara matematis sebagai berikut :

*precision = TP / (TP + FP)*

dimana :
TP = *True Positive* atau positif yang sebenarnya
FP = *False Positive* atau positif yang salah dari hasil prediksi

Namun pada sistem rekomendasi kita tidak akan menggunakan *True positive* atau *False Positive* melainkan *rating* yang diberikan pada buku untuk menentukan buku yang direkomendasikan relevan atau tidak. Dengan rumus sebagai berikut :

*precision@K = (# of recommended item that relevan) / (# of recommended item)*

Lalu apa definisi relevan? Bisa kita definisikan sebagai berikut :

Relevan : *Rating* > 5
Tidak relevan : *Rating* < 5

Angka 5 tersebut merupakan nilai arbiter dimana kita mengatakan bahwa nilai yang berada diatas akan dianggap relevan. Ada banyak metode untuk memilih nilai itu, tetapi untuk proek ini, kita akan menggunakan angka 5 sebagai ambang batas, yang berarti setiap buku yang memiliki rating di atas 5, akan dianggap relevan dan di bawahnya tidak akan relevan. 
* Pertama kita memilih nilai K yaitu 10 (sesuai total hasil rekomendasi)
* Lalu kita akan menentukan relevansi *threshold* yaitu 5
* Kemudian kita akan memfilter semua buku rekomendasi sesuai *threshold*
* Terakhir hitung dengan rumus *precision@K* di atas

Dari hasil rekomendasi yang dihasilkan, didapatkan *precision* sebagai berikut :

*precision@10 = 5 / 10*
*precision@10 = 50%*

Karena terdapat 5 buku yang memiliki rating di atas *threshold* maka didapatkan 50% *precision* dari model sistem rekomendasi dengan pendekatan *content based filtering*.

# Referensi (APA7)  :
---

1.  Yusuf, M., &amp; Cherid, A. (2017). Implementasi Algoritma Cosine Similarity Dan Metode TF-IDF Berbasis PHP Untuk Menghasilkan Rekomendasi Seminar. 
2.  Budianto, T. (2015). RANCANG BANGUN MUSIC RECOMMENDER SYSTEM DENGAN METODE USER-BASED COLLABORATIVE FILTERING. 
3.  Prasetio, N. (2019, July 26). Recommendation system Dengan Python&nbsp;: Definisi (part 1). Medium. Retrieved August 25, 2022, from https://medium.com/data-folks-indonesia/recommendation-system-dengan-python-definisi-part-1-71154dc3f700 
4.  Sambhwani, D. (2020, March 28). Movie recommender system. Medium. Retrieved August 26, 2022, from https://towardsdatascience.com/movie-recommender-system-52e4651f3ae7 
5.  Setiawan, S. (2020, November 11). Membicarakan precision, recall, Dan F1-Score. Medium. Retrieved August 26, 2022, from https://stevkarta.medium.com/membicarakan-precision-recall-dan-f1-score-e96d81910354 
6.  Malaeb, M. (2020, September 2). Recall and precision at K for Recommender Systems. Medium. Retrieved August 26, 2022, from https://medium.com/@m_n_malaeb/recall-and-precision-at-k-for-recommender-systems-618483226c54#:~:text=In%20the%20computation%20of%20precision,precision%20at%20k%20to%201. 
7.  Deutschman, Z. (2022, July 22). Recommender Systems: Machine Learning Metrics and business metrics. neptune.ai. Retrieved August 26, 2022, from https://neptune.ai/blog/recommender-systems-metrics 