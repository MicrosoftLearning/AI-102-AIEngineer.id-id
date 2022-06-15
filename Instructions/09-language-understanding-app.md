---
lab:
  title: Membuat Aplikasi LUIS
  module: Module 5 - Creating Language Understanding Solutions
ms.openlocfilehash: d8a32a2b6404e81d8a5d69cef874fad209de1bfc
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 11/16/2021
ms.locfileid: "145195708"
---
# <a name="create-a-language-understanding-app"></a>Membuat Aplikasi LUIS

Layanan Pemahaman Bahasa memungkinkan Anda menentukan aplikasi yang merangkum model bahasa yang dapat digunakan aplikasi untuk menafsirkan input bahasa alami dari pengguna, memprediksi *niat* pengguna (apa yang ingin mereka capai), dan mengidentifikasi *entitas* tempat niat harus diterapkan.

Misalnya, aplikasi pemahaman bahasa untuk aplikasi jam mungkin diharapkan memproses input seperti:

*Jam berapa di London?*

Jenis input ini adalah contoh dari *ucapan* (sesuatu yang mungkin dikatakan atau diketik pengguna), dengan *niat* yang diinginkan adalah untuk mendapatkan waktu di lokasi tertentu (*entitas*); dalam hal ini, London.

> **Catatan**: Tugas aplikasi pemahaman bahasa adalah untuk memprediksi niat pengguna, dan mengidentifikasi entitas apa pun yang menjadi tujuan penerapan niat tersebut. <u>Bukan</u> tugasnya untuk benar-benar melakukan tindakan yang diperlukan untuk memenuhi niat tersebut. Misalnya, aplikasi jam dapat menggunakan aplikasi bahasa untuk membedakan bahwa pengguna ingin mengetahui waktu di London; tetapi aplikasi klien itu sendiri kemudian harus menerapkan logika untuk menentukan waktu yang tepat dan menyajikannya kepada pengguna.

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda belum mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, ikuti langkah-langkah berikut untuk melakukannya. Jika tidak, buka folder kloning di Visual Studio Code.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="create-language-understanding-resources"></a>Membuat sumber daya Pemahaman Bahasa

Untuk menggunakan layanan Pemahaman Bahasa, Anda memerlukan dua jenis sumber daya:

- Sumber daya *penulisan*: digunakan untuk mendefinisikan, melatih, dan menguji aplikasi pemahaman bahasa. Hal ini harus merupakan sumber daya **Pemahaman Bahasa - Penulisan** di langganan Azure Anda.
- Sumber daya *prediksi*: digunakan untuk menerbitkan aplikasi pemahaman bahasa Anda dan menangani permintaan dari aplikasi klien yang menggunakannya. Hal ini dapat berupa sumber daya **Pemahaman Bahasa** atau **Cognitive Services** di langganan Azure Anda.

     > **Penting**: Sumber daya penulisan harus dibuat dalam salah satu dari tiga *wilayah* (Eropa, Australia, atau AS). Aplikasi Pemahaman Bahasa yang dibuat di sumber daya penulisan Eropa atau Australia hanya dapat diterapkan ke sumber daya prediksi di Eropa atau Australia; model yang dibuat di sumber daya penulisan AS dapat diterapkan ke sumber daya prediksi di lokasi Azure mana pun selain Eropa dan Australia. Lihat [dokumentasi wilayah penulisan dan penerbitan](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions) untuk detail tentang pencocokan lokasi penulisan dan prediksi.

Jika Anda belum memiliki sumber daya penulisan dan prediksi Pemahaman Bahasa:

1. Buka portal Microsoft Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, cari *pemahaman bahasa*, dan buat sumber daya **Pengertian Bahasa** dengan pengaturan berikut.

    *Pastikan Anda memilih **Pemahaman Bahasa**, <u>bukan</u> Pemahaman Bahasa (Azure Cognitive Services)*

    - **Buat opsi**: Keduanya
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Nama**: *Masukkan nama yang unik*
    - **Lokasi penulisan**: *Pilih lokasi preferensi Anda*
    - **Tingkat harga penulisan**: F0
    - **Lokasi prediksi**: *Sama dengan lokasi penulisan Anda*
    - **Tingkat harga prediksi**: F0
3. Tunggu agar sumber daya dibuat, dan perhatikan bahwa dua sumber daya Pemahaman Bahasa disediakan; satu untuk penulisan, dan lainnya untuk prediksi. Anda dapat melihat keduanya dengan menavigasi ke grup sumber daya tempat Anda membuatnya. Jika Anda memilih **Buka sumber daya**, sumber daya *penulisan* akan terbuka.

## <a name="create-a-language-understanding-app"></a>Membuat aplikasi LUIS

Sekarang setelah Anda membuat sumber daya penulisan, Anda dapat menggunakannya untuk membuat aplikasi Pemahaman Bahasa.

1. Di tab browser baru, buka portal Pemahaman Bahasa di `https://www.luis.ai`.
2. Masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda. Jika ini adalah pertama kalinya Anda masuk ke portal LUIS, Anda mungkin perlu memberikan beberapa izin kepada aplikasi untuk mengakses detail akun Anda. Kemudian selesaikan langkah-langkah *Selamat Datang* dengan memilih langganan Azure dan sumber daya pembuatan yang baru saja Anda buat.

    > **Catatan**: Jika akun Anda dikaitkan dengan beberapa langganan di direktori yang berbeda, Anda mungkin perlu beralih ke direktori yang berisi langganan tempat Anda menyediakan sumber daya Pemahaman Bahasa.

3. Pada halaman **Aplikasi Percakapan**, pastikan langganan Anda dan sumber daya Penulisan Pemahaman Bahasa dipilih. Lalu buat aplikasi baru untuk percakapan dengan pengaturan berikut:
    - **Nama**: Jam
    - **Budaya**: Bahasa Inggris (*jika opsi ini tidak tersedia, biarkan kosong*)
    - **Deskripsi:** Jam bahasa alami
    - **Sumber prediksi**: *Sumber daya prediksi LUIS Anda*

    Jika aplikasi **Jam** Anda tidak terbuka secara otomatis, buka aplikasi jam tersebut.
    
    Jika panel disertai tips untuk membuat aplikasi LUIS yang efektif muncul, tutup panel ini.

## <a name="create-intents"></a>Membuat niat

Hal pertama yang akan kita lakukan di aplikasi baru adalah mendefinisikan beberapa niat.

1. Pada halaman **Niat**, pilih **&#65291; Buat** untuk membuat niat baru bernama **GetTime**.
2. Dalam niat **GetTime**, tambahkan ucapan berikut sebagai contoh input pengguna:

    *jam berapa?*

    *jam berapa sekarang?*

3. Setelah menambahkan ucapan-ucapan ini, kembali ke halaman **Niat** dan tambahkan niat baru lainnya bernama **GetDay** dengan ucapan-ucapan berikut:

    *hari apa sekarang?*

    *sekarang hari apa?*

4. Setelah Anda menambahkan ucapan ini, kembali ke halaman **Niat** dan tambahkan niat baru lainnya bernama **GetDate** dengan ucapan berikut:

    *tanggal berapa ini?*

    *tanggal berapa sekarang?*

5. Setelah Anda menambahkan ucapan ini, kembali ke halaman **Niat** dan pilih Niat **Tidak Ada**. Niat disediakan sebagai fallback untuk input yang tidak memetakan ke salah satu niat yang telah Anda tetapkan dalam model bahasa Anda.
6. Tambahkan ucapan berikut ke niat **Tidak Ada**:

    *halo*

    *selamat tinggal*

## <a name="train-and-test-the-app"></a>Melatih dan menguji aplikasi

Sekarang setelah Anda menambahkan beberapa niat, mari latih aplikasi dan lihat apakah aplikasi tersebut dapat memprediksinya dengan benar dari input pengguna.

1. Di kanan atas portal, pilih **Latih** untuk melatih aplikasi.
2. Saat aplikasi dilatih, pilih **Uji** untuk menampilkan panel Pengujian, lalu masukkan ucapan uji berikut:

    *jam berapa sekarang?*

    Tinjau hasil yang dikembalikan, dengan mencatat bahwa hasil tersebut mencakup niat yang diprediksi (yang seharusnya **GetTime**) dan skor keyakinan yang menunjukkan probabilitas yang dihitung model untuk niat yang diprediksi.

3. Coba uji ucapan berikut:

    *beri tahu saya waktunya*

    Sekali lagi, tinjau niat dan skor keyakinan yang diprediksi.

4. Coba uji ucapan berikut:

    *ada apa hari ini?*

    Semoga model memprediksi niat **GetDay**.

5. Terakhir, uji ucapan percobaan ini:

    *hi*

    Ini akan mengembalikan niat **Tidak Ada**.

6. Tutup panel Pengujian.

## <a name="add-entities"></a>Tambahkan entitas

Sejauh ini Anda telah mendefinisikan beberapa ucapan sederhana yang memetakan niat. Sebagian besar aplikasi nyata menyertakan ucapan yang lebih kompleks dari mana entitas data tertentu harus diekstraksi untuk mendapatkan lebih banyak konteks untuk niat tersebut.

### <a name="add-a-machine-learned-entity"></a>Tambahkan entitas *machine learned*

Jenis entitas yang paling umum adalah entitas *machine learned*, di mana aplikasi belajar mengidentifikasi nilai entitas berdasarkan contoh.

1. Pada halaman **Entitas**, pilih **&#65291; Buat** untuk membuat entitas baru.
2. Di kotak dialog **Buat entitas**, buat entitas **Machine learned** bernama **Location**.
3. Setelah entitas **Location** dibuat, kembali ke halaman **Niat** dan pilih niat **GetTime**.
4. Masukkan contoh ucapan baru berikut:

    *jam berapa sekarang di London?*

5. Ketika ucapan telah ditambahkan, pilih kata ***london** _, dan dalam daftar dropdown yang muncul, pilih _ *Location** untuk menunjukkan bahwa "london" adalah contoh dari sebuah lokasi.
6. Tambahkan contoh ucapan lainnya:

    *jam berapa sekarang di New York?*

7. Ketika ucapan telah ditambahkan, pilih kata ***new york** _, dan petakan ke entitas _ *Location**.

### <a name="add-a-list-entity"></a>Tambahkan entitas *list*

Dalam beberapa kasus, nilai yang valid untuk suatu entitas dapat dibatasi pada daftar istilah dan sinonim tertentu; yang dapat membantu aplikasi mengidentifikasi contoh entitas dalam ucapan.

1. Pada halaman **Entitas**, pilih **&#65291; Buat** untuk membuat entitas baru.
2. Di kotak dialog **Buat entitas**, buat entitas **List** bernama **Weekday**.
3. Tambahkan **Nilai yang dinormalisasi** dan **sinonim** berikut:

    | Nilai yang dinormalisasi | sinonim|
    |-------------------|---------|
    | minggu | min |
    | senin | sen |
    | selasa | sel |
    | rabu | rab |
    | kamis | kam |
    | jumat | jum |
    | sabtu | sab |

3. Setelah entitas **Weekday** dibuat, kembali ke halaman **Niat** dan pilih niat **GetDate**.
4. Masukkan contoh ucapan baru berikut:

    *hari Sabtu tanggal berapa?*

5. Ketika ucapan telah ditambahkan, pastikan bahwa **sabtu** telah dipetakan secara otomatis ke entitas **Weekday**. Jika tidak, pilih kata **_saturday_ *_, dan di daftar dropdown yang muncul, pilih _* Weekday**.
6. Tambahkan contoh ucapan lainnya:

    *tanggal berapa hari Jumat?*

7. Ketika ucapan telah ditambahkan, pastikan **jumat** dipetakan ke entitas **Weekday**.

### <a name="add-a-regex-entity"></a>Tambahkan entitas *Regex*

Terkadang, entitas memiliki format tertentu, seperti nomor seri, kode formulir, atau tanggal. Anda dapat menentukan regex (*regex*) yang menjelaskan format yang diharapkan untuk membantu aplikasi Anda mengidentifikasi nilai entitas yang cocok.

1. Pada halaman **Entitas**, pilih **&#65291; Buat** untuk membuat entitas baru.
2. Di kotak dialog **Buat entitas**, buat entitas **Regex** bernama **Date** dengan regex berikut:

    ```
    [0-9]{2}/[0-9]{2}/[0-9]{4}
    ```

    > **Catatan**: Ini adalah regex sederhana yang memeriksa dua digit diikuti dengan "/", dua digit lainnya, "/", dan empat digit - misalnya *11/01/2020*. Ini memungkinkan tanggal yang tidak valid, seperti *56/00/9999*; namun penting untuk diingat bahwa regex entitas digunakan untuk mengidentifikasi entri data yang *dimaksudkan* sebagai tanggal - bukan untuk memvalidasi nilai tanggal.

3. Setelah entitas **Date** dibuat, kembali ke halaman **Niat** dan pilih niat **GetDay**.
4. Masukkan contoh ucapan baru berikut:

    *tanggal 01/01/1901 hari apa?*

5. Ketika ucapan telah ditambahkan, pastikan bahwa **01/01/1901** telah dipetakan secara otomatis ke entitas **Date**. Jika tidak, pilih **_01/01/1901_ *_, dan di daftar dropdown yang muncul, pilih _* Date**.
6. Tambahkan contoh ucapan lainnya:

    *tanggal 12/12/2099 hari apa?*

7. Ketika ucapan telah ditambahkan, pastikan **12/12/2099** dipetakan ke entitas **Date**.

### <a name="retrain-the-app"></a>Melatih ulang aplikasi

Sekarang setelah Anda memodifikasi model bahasa ini, Anda perlu melatih ulang dan menguji ulang aplikasi.

1. Di kanan atas portal, pilih **Latih** untuk melatih ulang aplikasi.
2. Saat aplikasi dilatih, pilih **Uji** untuk menampilkan panel Pengujian, lalu masukkan ucapan uji berikut:

    *jam berapa di Edinburgh?*

3. Tinjau hasil yang dikembalikan, yang diharapkan dapat memprediksi niat **GetTime**. Kemudian pilih **Periksa** dan di panel pemeriksaan tambahan yang ditampilkan, periksa bagian **Entitas ML**. Model seharusnya memperkirakan bahwa "edinburgh" adalah turunan dari entitas **Location**.
4. Coba uji ucapan berikut:

    *tanggal berapa hari Jumat?*

    *tanggal berapa hari Kamis?*

    *pada tanggal 01/01/2020 hari apa?*

5. Setelah Anda selesai menguji, tutup panel inspeksi, tetapi biarkan panel pengujian terbuka.

## <a name="perform-batch-testing"></a>Lakukan pengujian batch

Anda dapat menggunakan panel pengujian untuk menguji ucapan individu secara interaktif, tetapi untuk model bahasa yang lebih kompleks biasanya lebih efisien untuk melakukan *pengujian batch*.

1. Di Visual Studio Code, buka file **batch-test.json** di folder **09-luis-app**. File ini terdiri dari dokumen JSON yang berisi beberapa kasus pengujian untuk model bahasa jam yang Anda buat.
2. Di portal Pemahaman Bahasa, di panel Pengujian, pilih **Panel pengujian batch**. Kemudian pilih **&#65291; Impor** dan impor file **batch-test.json**, beri nama **clock-test**.
3. Di panel pengujian Batch, jalankan pengujian **clock-test**.
4. Setelah pengujian selesai, pilih **Lihat hasil**.
5. Pada halaman hasil, lihat matriks kebingungan yang mewakili hasil prediksi. Matriks ini menunjukkan prediksi positif benar, positif palsu, negatif benar, dan negatif palsu untuk niat atau entitas yang dipilih dalam daftar di sebelah kanan.

    ![Matriks kebingungan untuk pengujian batch pemahaman bahasa](./images/luis-confusion-matrix.jpg)

    > **Catatan**: Setiap ucapan diberi skor sebagai *positif* atau *negatif* untuk setiap niat - jadi misalnya "jam berapa sekarang?" harus diberi skor sebagai *positif* untuk niat **GetTime**, dan *negatif* untuk niat **GetDate**. Titik-titik pada matriks kebingungan menunjukkan ucapan mana yang diprediksi dengan benar (*true*) dan salah (*false*) sebagai *positif* dan *negatif* untuk niat yang dipilih.

6. Dengan memilih niat **GetDate**, pilih salah satu titik pada matriks kebingungan untuk melihat detail prediksi - termasuk ucapan dan skor keyakinan untuk prediksi. Kemudian pilih niat **GetDay**, **GetTime** dan **None** dan lihat hasil prediksinya. Aplikasi seharusnya berhasil memprediksi niat dengan benar.

    > **Catatan**: Antarmuka pengguna mungkin tidak menghapus titik yang dipilih sebelumnya.

7. Pilih entitas **Location** dan lihat hasil prediksi dalam matriks kebingungan. Secara khusus, perhatikan prediksi yang *negatif palsu* - ini adalah kasus di mana aplikasi gagal mendeteksi lokasi yang ditentukan dalam ucapan, yang menunjukkan bahwa Anda mungkin perlu menambahkan lebih banyak ucapan sampel ke niat dan melatih ulang model.
8. Tutup panel pengujian Batch.

## <a name="publish-the-app"></a>Terbitkan aplikasi

Dalam proyek nyata, Anda akan secara berulang memperbaiki niat dan entitas, melatih ulang, dan menguji ulang hingga Anda puas dengan performa prediktif. Kemudian, Anda dapat menerbitkan aplikasi untuk digunakan oleh aplikasi klien.

1. Di kanan atas portal Pemahaman Bahasa, pilih **Terbitkan**.
2. Pilih **Slot produksi**, dan terbitkan aplikasi.
3. Setelah penerbitan selesai, di bagian atas portal Pemahaman Bahasa, pilih **Kelola**.
4. Pada halaman **Pengaturan**, perhatikan **ID Aplikasi**. Aplikasi klien memerlukan ini untuk menggunakan aplikasi Anda.
5. Pada halaman **Sumber Daya Azure**, perhatikan **Kunci Primer**, **Kunci Sekunder**, dan **URL Titik Akhir** untuk sumber daya prediksi tempat aplikasi dapat digunakan. Aplikasi klien memerlukan titik akhir dan salah satu kunci untuk terhubung ke sumber daya prediksi dan diautentikasi.
6. Di Visual Studio Code, dalam folder **09-luis-app**, pilih file batch **GetIntent.cmd** dan lihat kode yang ada di dalamnya. Skrip baris perintah ini menggunakan cURL untuk memanggil REST API Pemahaman Bahasa aplikasi dan titik akhir prediksi yang ditentukan.
7. Ganti nilai tempat penampung dalam skrip dengan **ID Aplikasi**, **URL Titik Akhir**, dan **Kunci Primer** atau **Kunci Sekunder** untuk aplikasi Pemahaman Bahasa Anda; lalu simpan file yang diperbarui.
8. Klik kanan folder **09-luis-app** dan buka terminal terintegrasi. Kemudian masukkan perintah berikut (pastikan untuk menyertakan tanda kutip!):

    ```
    GetIntent "What's the time?"
    ```

9. Tinjau respons JSON yang dikembalikan oleh aplikasi Anda, yang seharusnya menunjukkan niat penilaian teratas yang diprediksi untuk input Anda (yang seharusnya **GetTime**).
10. Coba perintah berikut:

    ```
    GetIntent "What's today's date?"
    ```

11. Periksa respons dan verifikasi bahwa respons tersebut memprediksi niat **GetDate**.
12. Coba perintah berikut:

    ```
    GetIntent "What time is it in Sydney?"
    ```

13. Periksa respons dan verifikasi bahwa respons tersebut menyertakan entitas **Location**.

14. Coba perintah berikut dan periksa responsnya:

    ```
    GetIntent "What time is it in Glasgow?"
    ```

    ```
    GetIntent "What's the time in Nairobi?"
    ```

    ```
    GetIntent "What's the UK time?"
    ```
15. Coba beberapa variasi lagi - tujuannya adalah untuk menghasilkan setidaknya beberapa respons yang memprediksi niat **GetTime** dengan benar, tetapi gagal mendeteksi entitas **Location**.

    Biarkan terminal tetap terbuka. Anda akan kembali ke sana nanti.

## <a name="apply-active-learning"></a>Terapkan *pembelajaran aktif*

Anda dapat meningkatkan aplikasi Pemahaman Bahasa berdasarkan ucapan historis yang dikirimkan ke titik akhir. Praktik ini disebut *pembelajaran aktif*.

Di prosedur sebelumnya, Anda menggunakan cURL untuk mengirimkan permintaan ke titik akhir aplikasi Anda. Permintaan ini mencakup opsi untuk mencatat kueri, yang memungkinkan aplikasi melacaknya untuk digunakan dalam pembelajaran aktif.

1. Di portal Pemahaman Bahasa, Pilih **Buat** dan lihat halaman **Tinjau ucapan titik akhir**. Halaman ini mencantumkan ucapan yang dicatat yang ditandai oleh layanan untuk ditinjau.
2. Untuk ucapan apa pun yang maksud dan entitas lokasi barunya (yang tidak disertakan dalam ucapan pelatihan asli) diprediksi dengan benar, pilih **&#10003;** untuk mengonfirmasi entitas, lalu gunakan ikon **&#10514;** untuk menambahkan ucapan ke niat sebagai contoh pelatihan.
3. Temukan contoh ucapan di mana niat **GetTime** diidentifikasi dengan benar, tetapi entitas **Location** <u>tidak</u> diidentifikasi; dan pilih nama lokasi dan petakan ke entitas **location**. Kemudian gunakan ikon **&#10514;** untuk menambahkan ucapan ke niat sebagai contoh pelatihan.
4. Buka halaman **Niat** dan buka niat **GetTime** untuk mengonfirmasi bahwa ucapan yang disarankan telah ditambahkan.
5. Di bagian atas portal Pemahaman Bahasa, pilih **Latih** untuk melatih ulang aplikasi.
6. Di kanan atas portal Pemahaman Bahasa, pilih **Terbitkan** dan terbitkan ulang aplikasi ke **slot Produksi**.
7. Kembali ke terminal untuk folder **09-luis-app**, dan gunakan perintah **GetIntent** untuk mengirimkan ucapan yang Anda tambahkan dan koreksi selama pembelajaran aktif.
8. Verifikasi bahwa hasilnya sekarang menyertakan entitas **Location**. Kemudian coba ucapan lain yang menggunakan frasa yang sama tetapi menentukan lokasi yang berbeda (misalnya, *Berlin*).

## <a name="export-the-app"></a>Mengekspor aplikasi

Anda dapat menggunakan portal Pemahaman Bahasa untuk mengembangkan dan menguji aplikasi bahasa Anda, tetapi dalam proses pengembangan perangkat lunak untuk DevOps, Anda harus mempertahankan definisi aplikasi yang dikontrol sumber yang dapat disertakan dalam alur integrasi dan pengiriman berkelanjutan (CI/CD). Meskipun Anda *dapat* menggunakan SDK atau REST API Pemahaman Bahasa dalam skrip kode untuk membuat dan melatih aplikasi, cara yang lebih sederhana adalah menggunakan portal untuk membuat aplikasi, dan mengekspornya dalam bentuk file *.lu*  yang dapat diimpor dan dilatih ulang dalam contoh Pemahaman Bahasa lain. Pendekatan ini memungkinkan Anda memanfaatkan manfaat produktivitas portal sambil mempertahankan portabilitas dan reproduktifitas aplikasi.

1. Di portal Pemahaman Bahasa, pilih **Kelola**.
2. Pada halaman **Versi**, pilih versi aplikasi saat ini (seharusnya hanya ada satu).
3. Dalam daftar dropdown **Ekspor**, pilih **Ekspor sebagai LU**. Kemudian, saat diminta oleh browser Anda, simpan file dalam folder **09-luis-app**.
4. Di Visual Studio Code, buka file **.lu** yang baru saja Anda ekspor dan unduh (jika Anda diminta untuk mencari ekstensi di marketplace yang dapat membacanya, abaikan perintah tersebut). Perhatikan bahwa format LU dapat dibaca manusia, menjadikannya cara yang efektif untuk mendokumentasikan definisi aplikasi Pemahaman Bahasa Anda di lingkungan pengembangan tim.

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang menggunakan layanan **Pemahaman Bahasa**, lihat [dokumentasi Pemahaman Bahasa](https://docs.microsoft.com/azure/cognitive-services/luis/).
