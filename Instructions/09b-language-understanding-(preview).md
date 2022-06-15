---
lab:
  title: Membuat model pemahaman bahasa dengan layanan Bahasa (pratinjau)
ms.openlocfilehash: 92d556e50c8f5c827e16f1e5ad301d3b59c1ad6e
ms.sourcegitcommit: cb2edc850cec8390a81212d9b8f6a279d2f42b4d
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 04/04/2022
ms.locfileid: "145195716"
---
# <a name="create-a-language-understanding-model-with-the-language-service-preview"></a>Membuat model pemahaman bahasa dengan layanan Bahasa (pratinjau)

> **Catatan**: Fitur pemahaman bahasa percakapan dari layanan Bahasa saat ini dalam pratinjau, dan dapat berubah. Dalam beberapa kasus, pelatihan model mungkin gagal - jika ini terjadi, coba lagi.  

Layanan Bahasa memungkinkan Anda menentukan model *pemahaman bahasa percakapan* yang dapat digunakan aplikasi untuk menafsirkan input bahasa alami dari pengguna, memprediksi *niat* pengguna (apa yang ingin mereka capai), dan mengidentifikasi *entitas* apa pun yang harus diterapkan niat tersebut.

Misalnya, model bahasa percakapan untuk aplikasi jam mungkin diharapkan memproses input seperti:

*Jam berapa di London?*

Jenis input ini adalah contoh dari *ucapan* (sesuatu yang mungkin dikatakan atau diketik pengguna), dengan *niat* yang diinginkan adalah untuk mendapatkan waktu di lokasi tertentu (*entitas*); dalam hal ini, London.

> **Catatan**: Tugas model bahasa percakapan adalah memprediksi maksud pengguna dan mengidentifikasi entitas apa pun yang menjadi tujuan penerapan niat tersebut. <u>Bukan</u> pekerjaan model bahasa percakapan untuk benar-benar melakukan tindakan yang diperlukan untuk memenuhi niat. Misalnya, aplikasi jam dapat menggunakan model bahasa percakapan untuk membedakan bahwa pengguna ingin mengetahui waktu di London; tetapi aplikasi klien itu sendiri kemudian harus menerapkan logika untuk menentukan waktu yang tepat dan menyajikannya kepada pengguna.

## <a name="create-a-language-service-resource"></a>Buat sumber daya Layanan bahasa

Untuk membuat model bahasa percakapan, Anda memerlukan sumber daya **Layanan bahasa** di wilayah yang didukung. Pada saat penulisan, hanya wilayah US Barat 2 dan Eropa Barat yang didukung.

1. Buka portal Microsoft Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, cari *bahasa*, dan buat sumber daya **Layanan bahasa** dengan pengaturan berikut.

    - **Fitur**: Gunakan fitur default, yang mencakup pemahaman bahasa percakapan (pratinjau) 
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Region**: US Barat 2 atau Eropa Barat
    - **Nama**: *Masukkan nama yang unik*
    - **Tingkat harga**: Pilih tingkat Standar (S) (Pengertian Bahasa Percakapan saat ini tidak didukung pada tingkat Gratis).
    - **Persyaratan Hukum**: _Setuju_ 
    - **Pemberitahuan AI yang Bertanggung Jawab**: _Setuju_
3. Tunggu hingga penyebaran selesai, lalu lihat detail penyebaran.

## <a name="create-a-conversational-language-understanding-project"></a>Membuat proyek pemahaman bahasa percakapan

Sekarang setelah Anda membuat sumber penulisan, Anda dapat menggunakannya untuk membuat proyek pemahaman bahasa percakapan.

1. Di tab browser baru, buka portal Language Studio di `https://language.cognitive.azure.com/` dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.

2. Jika diminta untuk memilih sumber daya Bahasa, pilih pengaturan berikut:

    - **Direktori Azure**: Direktori Azure yang berisi langganan Anda.
    - **Langganan Azure**: Langganan Azure Anda.
    - **Sumber daya bahasa**: Sumber daya bahasa yang Anda buat sebelumnya.

3. Jika Anda <u>tidak</u> diminta untuk memilih sumber daya bahasa, mungkin karena Anda telah menetapkan sumber daya bahasa yang berbeda; dalam hal ini:

    1. Pada bilah di bagian atas halaman, klik tombol **Pengaturan (&#9881;)**.
    2. Pada halaman **Pengaturan**, lihat tab **Sumber Daya**.
    3. Pilih sumber daya bahasa yang baru saja Anda buat, dan klik **Ganti sumber daya**.
    4. Di bagian atas halaman, klik **Language Studio** untuk kembali ke beranda Language Studio.

4. Di bagian atas portal, di menu **Buat baru**, pilih **Pemahaman bahasa percakapan**.

5. Di kotak dialog **Buat proyek**, pada halaman **Masukkan informasi dasar**, masukkan detail berikut, lalu klik **Berikutnya**:
    - **Nama**: `Clock`
    - **Deskripsi**: `Natural language clock`
    - **Bahasa utama ucapan**: Bahasa Inggris
    - **Aktifkan beberapa bahasa dalam proyek?** : *Tidak dipilih*

7. Di halaman **Tinjau dan selesaikan**, klik **Buat**.

## <a name="create-intents"></a>Membuat niat

Hal pertama yang akan kita lakukan dalam proyek baru adalah mendefinisikan beberapa niat.

> **Tips**: Saat mengerjakan proyek Anda, jika beberapa tips ditampilkan, baca dan klik **Mengerti** untuk menutupnya, atau klik **Lewati semua**.

1. Pada halaman **Buat skema**, pada tab **Niat**, pilih **&#65291; Tambahkan** untuk menambahkan niat baru bernama **GetTime**.

2. Klik niat **GetTime** baru untuk mengeditnya, tambahkan ucapan berikut sebagai contoh input pengguna:

    `what is the time?`

    `what's the time?`

    `what time is it?`

    `tell me the time`

3. Setelah Anda menambahkan ucapan ini, klik **Simpan perubahan** dan kembali ke halaman **Buat skema**.

4. Tambahkan niat baru lainnya bernama **GetDay** dengan ucapan berikut:

    `what day is it?`

    `what's the day?`

    `what is the day today?`

    `what day of the week is it?`

5. Setelah Anda menambahkan ucapan-ucapan ini dan menyimpannya, kembali ke halaman **Buat skema** dan tambahkan niat baru lainnya bernama **GetDate** dengan ucapan-ucapan berikut:

    `what date is it?`

    `what's the date?`

    `what is the date today?`

    `what's today's date?`

6. Setelah Anda menambahkan ucapan-ucapan ini, simpan dan kosongkan filter **GetDate** pada halaman ucapan sehingga Anda dapat melihat semua ucapan untuk semua niat.

## <a name="train-and-test-the-model"></a>Terapkan dan uji model tersebut

Sekarang setelah Anda menambahkan beberapa niat, mari latih model bahasa dan lihat apakah model dapat memprediksinya dengan benar dari input pengguna.

1. Di panel di sebelah kiri, pilih halaman **Latih model**, lalu pilih **Mulai pekerjaan pelatihan**.

2. Pada dialog **Mulai pekerjaan pelatihan**, pilih opsi untuk melatih model baru, beri nama **Jam** dan pastikan opsi untuk menjalankan evaluasi saat pelatihan diaktifkan. 

3. Untuk memulai proses pelatihan model Anda, klik **Latih**.

4. Saat pelatihan selesai (yang mungkin memerlukan waktu beberapa menit) **Status** pekerjaan akan berubah menjadi **Pelatihan berhasil**.

5. Pilih halaman **Lihat detail model**, lalu pilih model **Jam**. Tinjau metrik evaluasi keseluruhan dan per niat (*presisi*, *recall*, dan *skor F1*) dan *matriks kebingungan* yang dihasilkan oleh evaluasi yang dilakukan saat pelatihan (perhatikan bahwa karena sedikitnya jumlah sampel ucapan, tidak semua niat dapat disertakan dalam hasil).

    >**Catatan**: Untuk mempelajari selengkapnya tentang metrik evaluasi, lihat [dokumentasi](https://docs.microsoft.com/azure/cognitive-services/language-service/conversational-language-understanding/concepts/evaluation-metrics)

5. Pada halaman **Model penyebaran**, pilih **Tambahkan penyebaran**.

5. Pada dialog **Tambahkan penyebaran**, pilih **Buat nama penyebaran baru**, lalu masukkan **produksi**

5. Pilih model **Jam** dan klik **Kirim**. Penyebaran ini mungkin memerlukan waktu.

6. Ketika model telah disebarkan, pada halaman **Uji model**, pilih model **Jam**.

6. Pada **Uji Model: Halaman Jam**, di menu dropdown **Nama penyebaran**, pilih **produksi**.  

7. Masukkan teks berikut, lalu klik **Jalankan pengujian**:

    `what's the time now?`

    Tinjau hasil yang dikembalikan, dengan mencatat bahwa hasil tersebut mencakup niat yang diprediksi (yang seharusnya **GetTime**) dan skor keyakinan yang menunjukkan probabilitas yang dihitung model untuk niat yang diprediksi. Tab JSON menunjukkan keyakinan komparatif untuk setiap niat potensial (yang memiliki skor kepercayaan tertinggi adalah tujuan yang diprediksi)

8. Kosongkan kotak teks, lalu jalankan pengujian lain dengan teks berikut:

    `tell me the time`

    Sekali lagi, tinjau niat dan skor keyakinan yang diprediksi.

9. Coba teks berikut:

    `what's the day today?`

    Semoga model memprediksi niat **GetDay**.

## <a name="add-entities"></a>Tambahkan entitas

Sejauh ini Anda telah mendefinisikan beberapa ucapan sederhana yang memetakan niat. Sebagian besar aplikasi nyata menyertakan ucapan yang lebih kompleks dari mana entitas data tertentu harus diekstraksi untuk mendapatkan lebih banyak konteks untuk niat tersebut.

### <a name="add-a-learned-entity"></a>Menambahkan entitas *learned*

Jenis entitas yang paling umum adalah entitas *learned*, di mana model belajar mengidentifikasi nilai entitas berdasarkan contoh.

1. Di Language Studio, kembali ke halaman **Buat skema**, lalu pada tab **Entitas**, pilih **&#65291; Tambahkan** untuk menambahkan entitas baru.

2. Di kotak dialog **Tambahkan entitas**, masukkan nama entitas **Location** dan pastikan bahwa **Learned** dipilih. Kemudian klik **Tambahkan entitas**.

3. Setelah entitas **Location** dibuat, kembali ke halaman **Bangu skema** lalu pada tab **Niat**, pilih niat **GetTime**.

4. Masukkan contoh ucapan baru berikut:

    `what time is it in London?`

5. Ketika ucapan telah ditambahkan, pilih kata ***London** _, dan dalam daftar dropdown yang muncul, pilih _ *Location** untuk menunjukkan bahwa "London" adalah contoh dari sebuah lokasi.

6. Tambahkan contoh ucapan lainnya:

    `Tell me the time in Paris?`

7. Ketika ucapan telah ditambahkan, pilih kata ***Paris** _, dan petakan ke entitas _ *Location**.

8. Tambahkan contoh ucapan lainnya:

    `what's the time in New York?`

9. Ketika ucapan telah ditambahkan, pilih kata ***New York** _, dan petakan ke entitas _ *Location**.

10. Klik **Simpan perubahan** untuk menyimpan ucapan baru.

### <a name="add-a-list-entity"></a>Tambahkan entitas *list*

Dalam beberapa kasus, nilai yang valid untuk suatu entitas dapat dibatasi pada daftar istilah dan sinonim tertentu; yang dapat membantu aplikasi mengidentifikasi contoh entitas dalam ucapan.

1. Di Language Studio, kembali ke halaman **Buat skema**, lalu pada tab **Entitas**, pilih **&#65291; Tambahkan** untuk menambahkan entitas baru.

2. Di kotak dialog **Tambahkan entitas**, masukkan nama entitas **Weekday** dan pilih jenis entitas **List**. Kemudian klik **Tambahkan entitas**.

3. Pada halaman untuk entitas **Weekday**, di bagian **Daftar**, klik **&#65291; Tambahkan daftar baru**. Kemudian masukkan nilai dan sinonim berikut lalu **Simpan**:

    | Daftar kunci | sinonim|
    |-------------------|---------|
    | Hari Minggu | Min |

4. Ulangi langkah sebelumnya untuk menambahkan komponen daftar berikut:

    | Nilai | sinonim|
    |-------------------|---------|
    | Senin | Sen |
    | Selasa | Sel |
    | Rabu | Rab |
    | Kamis | Kam |
    | Jumat | Jum |
    | Sabtu | Sab |

5. Kembali ke halaman **Buat skema**, lalu pada tab **Niat**, pilih niat **GetDate**.

6. Masukkan contoh ucapan baru berikut:

    `what date was it on Saturday?`

7. Ketika ucapan telah ditambahkan, pilih kata ***Saturday** _, dan di daftar drop-down yang muncul, pilih _*Weekday**.

8. Tambahkan contoh ucapan lainnya:

    `what date will it be on Friday?`

9. Ketika ucapan telah ditambahkan, petakan **Jumat** ke entitas **Weekday**.

10. Tambahkan contoh ucapan lainnya:

    `what will the date be on Thurs?`

11. Ketika ucapan telah ditambahkan, petakan **Kamis** ke entitas **Weekday**.

12. Klik **Simpan perubahan** untuk menyimpan ucapan baru.

### <a name="add-a-prebuilt-entity"></a>Tambahkan entitas *yang dibuat sebelumnya*

Layanan Bahasa menyediakan sekumpulan entitas *prebuilt* yang biasanya digunakan dalam aplikasi percakapan.

1. Di Language Studio, kembali ke halaman **Buat skema**, lalu pada tab **Entitas**, pilih **&#65291; Tambahkan** untuk menambahkan entitas baru.

2. Di kotak dialog **Tambahkan entitas**, masukkan nama entitas **Date** dan pilih jenis entitas **Prebuilt**. Kemudian klik **Tambahkan entitas**.

3. Pada halaman untuk entitas **Date**, di bagian **Prebuilt**, klik **&#65291; Tambahkan prebuilt baru**.

4. Dalam daftar **Pilih prebuilt**, pilih **DateTime** lalu klik **Simpan**.

5. Kembali ke halaman **Buat skema**, lalu pada tab **Niat**, pilih niat **GetDay**.

6. Masukkan contoh ucapan baru berikut:

    `what day was 01/01/1901?`

7. Ketika ucapan telah ditambahkan, pilih ***01/01/1901** _, dan dalam daftar dropdown yang muncul, pilih _*Date**.

8. Tambahkan contoh ucapan lainnya:

    `what day will it be on Dec 31st 2099?`

9. Ketika ucapan telah ditambahkan, petakan **31 Desember 2099** ke entitas **Date**.

10. Klik **Simpan perubahan** untuk menyimpan ucapan baru.

### <a name="retrain-the-model"></a>Latih ulang model

Sekarang setelah Anda memodifikasi skema, Anda perlu melatih ulang dan menguji ulang model.

1. Pada halaman **Latih model**, pilih **Mulai tugas pelatihan**.

1. Pada dialog **Mulai pekerjaan pelatihan**, pilih opsi untuk menimpa model yang ada dan tentukan model **Jam**. Pastikan opsi untuk menjalankan evaluasi saat pelatihan dipilih dan klik **Latih** untuk melatih model; mengonfirmasi bahwa Anda ingin menimpa model yang ada.

2. Saat pelatihan selesai, **Status** pekerjaan akan diperbarui menjadi **Pelatihan berhasil**. 

2. Pilih halaman **Lihat detail model**, lalu pilih model **Jam**. Tinjau metrik evaluasi keseluruhan, per-entitas, dan per-niat (*presisi*, *recall*, dan *skor F1*) dan *matriks kebingungan* dihasilkan oleh evaluasi yang dilakukan saat pelatihan (perhatikan bahwa karena jumlah ucapan sampel yang sedikit, tidak semua niat dapat disertakan dalam hasil).

3. Pada halaman **Model penyebaran**, pilih **Tambahkan penyebaran**.

3. Pada dialog **Tambahkan penyebaran**, pilih **Ganti nama penyebaran yang ada**, lalu pilih **produksi**.

3. Pilih model **Jam**, lalu klik **Kirim** untuk menyebarkannya. Ini mungkin memakan waktu.

4. Saat model disebarkan, pada halaman **Uji model**, pilih model **Jam**, pilih penyebaran **produksi**, lalu uji dengan teks berikut:

    `what's the time in Edinburgh?`

5. Tinjau hasil yang dikembalikan, yang diharapkan dapat memprediksi niat **GetTime** dan entitas **Location** dengan nilai teks "Edinburgh".

6. Coba uji ucapan berikut:

    `what time is it in Tokyo?`
    
    `what date is it on Friday?`

    `what's the date on Weds?`

    `what day was 01/01/2020?`

    `what day will Mar 7th 2030 be?`

## <a name="use-the-model-from-a-client-app"></a>Menggunakan model dari aplikasi klien

Dalam proyek nyata, Anda akan secara berulang memperbaiki niat dan entitas, melatih ulang, dan menguji ulang hingga Anda puas dengan performa prediktif. Kemudian, ketika Anda telah mengujinya dan puas dengan performa prediktifnya, Anda dapat menggunakannya di aplikasi klien dengan memanggil antarmuka REST-nya. Dalam latihan ini, Anda akan menggunakan utilitas *curl* guna memanggil titik akhir REST untuk model Anda.

1. Di Language Studio, pada halaman **Menyebarkan model**, pilih model **Jam**. Kemudian klik **Dapatkan URL prediksi**.

2. Di kotak dialog **Dapatkan URL prediksi**, perhatikan bahwa URL untuk titik akhir prediksi ditampilkan bersama dengan permintaan sampel, yang terdiri dari perintah **curl** yang mengirimkan permintaan HTTP POST ke titik akhir, menentukan kunci untuk sumber daya Bahasa Anda di header dan menyertakan kueri dan bahasa dalam data permintaan.

3. Salin permintaan sampel, dan tempel ke editor teks pilihan Anda (misalnya Notepad).

4. Ganti tempat penampung berikut:
    - **YOUR_QUERY_HERE**: *Jam berapa di Sydney*
    - **QUERY_LANGUAGE_HERE**: *ID*

    Perintah harus menyerupai kode berikut:

    ```
    curl -X POST "https://some-name.cognitiveservices.azure.com/language/:analyze-conversations?projectName=Clock&deploymentName=production&api-version=2021-11-01-preview" -H "Ocp-Apim-Subscription-Key: 0ab1c23de4f56..."  -H "Apim-Request-Id: 9zy8x76wv5u43...." -H "Content-Type: application/json" -d "{\"verbose\":true,\"query\":\"What's the time in Sydney?\",\"language\":\"EN\"}"
    ```

5. Buka perintah (Windows) atau bash shell (Linux/Mac).

6. Salin dan tempel perintah curl yang diedit ke antarmuka baris perintah Anda dan jalankan.

7. Lihat JSON yang dihasilkan, yang harus menyertakan niat dan entitas yang diprediksi, seperti ini:

    ```
    {"query":"What's the time in Sydney?","prediction":{"topIntent":"GetTime","projectKind":"conversation","intents":[{"category":"GetTime","confidenceScore":0.9998859},{"category":"GetDate","confidenceScore":9.8372206E-05},{"category":"GetDay","confidenceScore":1.5763446E-05}],"entities":[{"category":"Location","text":"Sydney","offset":19,"length":6,"confidenceScore":1}]}}
    ```

8. Tinjau respons JSON yang dikembalikan oleh model Anda untuk memastikan bahwa niat penilaian teratas yang diprediksi adalah **GetTime**.

9. Ubah kueri dalam perintah curl ke `What's today's date?` lalu jalankan dan tinjau JSON yang dihasilkan.

10. Coba kueri berikut:

    `What day will Jan 1st 2050 be?`

    `What time is it in Glasgow?`

    `What date will next Monday be?`

## <a name="export-the-project"></a>Mengekspor proyek

Anda dapat menggunakan Language Studio untuk mengembangkan dan menguji model pemahaman bahasa Anda, tetapi dalam proses pengembangan perangkat lunak untuk DevOps, Anda harus mempertahankan definisi proyek yang dikontrol sumber yang dapat disertakan dalam alur integrasi pengiriman berkelanjutan (CI/CD). Meskipun Anda *dapat* menggunakan REST API Bahasa dalam skrip kode untuk membuat dan melatih model, cara yang lebih sederhana adalah menggunakan portal untuk membuat skema model, dan mengekspornya sebagai file *.json* yang dapat diimpor dan dilatih ulang dalam instans layanan Bahasa lain. Pendekatan ini memungkinkan Anda memanfaatkan manfaat produktivitas antarmuka visual Language Studio sambil mempertahankan portabilitas dan reproduktifitas model.

1. Di Language Studio, pada halaman **Proyek**, pilih proyek **Jam**. Jangan klik **Jam**, pilih ikon lingkaran untuk memilih proyek Jam.

2. Klik tombol **&#x2913; Ekspor**.

3. Simpan file **Clock.json** yang dihasilkan (di tempat yang Anda inginkan).

4. Buka file yang diunduh di editor kode favorit Anda (misalnya, Visual Studio Code) untuk meninjau definisi JSON proyek Anda.

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang menggunakan layanan **Bahasa** untuk membuat solusi pemahaman bahasa percakapan, lihat [Dokumentasi layanan bahasa](https://docs.microsoft.com/azure/cognitive-services/language-service/conversational-language-understanding/overview).
