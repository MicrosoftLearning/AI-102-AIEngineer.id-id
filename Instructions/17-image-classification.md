---
lab:
  title: Mengklasifikasi Gambar dengan Visi Kustom
  module: Module 9 - Developing Custom Vision Solutions
ms.openlocfilehash: a8d60adee3e43ce0c613b71c7cba72067fe2b799
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 01/12/2022
ms.locfileid: "145195700"
---
# <a name="classify-images-with-custom-vision"></a>Mengklasifikasi Gambar dengan Visi Kustom

Layanan **Penglihatan Khusus** memungkinkan Anda membuat model penglihatan komputer yang dilatih berdasarkan gambar Anda sendiri. Anda dapat menggunakannya untuk melatih model *klasifikasi gambar* dan *deteksi objek*; yang kemudian dapat Anda publikasikan dan konsumsi dari aplikasi.

Dalam latihan ini, Anda akan menggunakan layanan Custom Vision untuk melatih model klasifikasi gambar yang dapat mengidentifikasi tiga kelas buah (apel, pisang, dan jeruk).

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda belum mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, ikuti langkah-langkah berikut untuk melakukannya. Jika tidak, buka folder kloning di Visual Studio Code.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="create-custom-vision-resources"></a>Buat sumber daya Custom Vision

Sebelum dapat melatih model, Anda memerlukan sumber daya Azure untuk *pelatihan* dan *prediksi*. Anda dapat membuat sumber daya **Penglihatan Khusus** untuk setiap tugas ini, atau Anda dapat membuat satu sumber daya **Cognitive Services** dan menggunakannya untuk salah satu (atau keduanya).

Dalam latihan ini, Anda akan membuat sumber daya **Penglihatan Khusus** untuk pelatihan dan prediksi sehingga Anda dapat mengelola akses dan biaya untuk beban kerja ini secara terpisah.

1. Di tab browser baru, buka portal Microsoft Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, cari *custom vision*, dan buat sumber daya **Custom Vision** dengan pengaturan berikut:
    - **Buat opsi**: Keduanya
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Wilayah**: *Pilih wilayah yang tersedia*
    - **Nama**: *Masukkan nama yang unik*
    - **Tingkat harga pelatihan**: F0
    - **Tingkat harga prediksi**: F0

    > **Catatan**: Jika Anda sudah memiliki layanan custom vision F0 di langganan, pilih **S0** untuk yang satu ini.

3. Tunggu hingga sumber daya dibuat, lalu lihat detail penyebaran dan perhatikan bahwa dua sumber daya Custom Vision telah disediakan; satu untuk pelatihan, dan satu lagi untuk prediksi (dibuktikan dengan akhiran **-Prediksi**). Anda dapat melihat ini dengan menavigasi ke grup sumber daya tempat Anda membuatnya.

> **Penting**: Setiap sumber daya memiliki *titik akhir* dan *kunci* sendiri, yang digunakan untuk mengelola akses dari kode Anda. Untuk melatih model klasifikasi gambar, kode Anda harus menggunakan sumber daya *pelatihan* (dengan titik akhir dan kuncinya); dan untuk menggunakan model terlatih untuk memprediksi kelas gambar, kode Anda harus menggunakan sumber daya *prediksi* (dengan titik akhir dan kuncinya).

## <a name="create-a-custom-vision-project"></a>Membuat proyek Visual Kustom

Untuk melatih model klasifikasi gambar, Anda perlu membuat proyek Visi Kustom berdasarkan sumber daya pelatihan Anda. Untuk melakukannya, Anda akan menggunakan portal Custom Vision.

1. Di Visual Studio Code, lihat gambar pelatihan di folder **17-image-classification/training-images** tempat Anda mengkloning repositori. Folder ini berisi sub-folder gambar apel, pisang, dan jeruk.
2. Di tab browser baru, buka portal Visi Khusus di `https://customvision.ai`. Jika diminta, masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda dan setujui ketentuan layanan.
3. Di portal Custom Vision, buat proyek baru dengan pengaturan berikut:
    - **Nama**: Klasifikasi Buah
    - **Deskripsi:** Klasifikasi gambar untuk buah
    - **Sumber daya**: *Sumber daya Custom Vision yang Anda buat sebelumnya*
    - **Jenis Proyek**: Klasifikasi
    - **Jenis Klasifikasi**: Multikelas (satu tag per gambar)
    - **Domain**: Makanan
4. Dalam proyek baru, klik **\[+\] Tambahkan gambar**, dan pilih semua file di folder **training-images/apple** yang Anda lihat sebelumnya. Kemudian unggah file gambar, tentukan tag *apel*, seperti ini:

![Unggah apel dengan tag apel](./images/upload_apples.jpg)
   
5. Ulangi langkah sebelumnya untuk mengunggah gambar di folder **pisang** dengan tag *pisang*, dan gambar di folder **jeruk** dengan tag *jeruk*.
6. Jelajahi gambar yang telah Anda unggah di proyek Custom Vision - harus ada 15 gambar dari setiap kelas, seperti ini:

![Gambar buah yang diberi tag - 15 apel, 15 pisang, dan 15 jeruk](./images/fruit.jpg)
    
7. Dalam proyek Custom Vision, di atas gambar, klik **Latih** untuk melatih model klasifikasi menggunakan gambar yang diberi tag. Pilih opsi **Pelatihan Cepat**, lalu tunggu hingga perulangan pelatihan selesai (hal ini mungkin memerlukan waktu sekitar satu menit).
8. Ketika perulangan model telah dilatih, tinjau metrik performa *Presisi*, *Pengenalan*, dan *AP* - hal ini mengukur akurasi prediksi model klasifikasi, dan semua harus tinggi.

> **Catatan**: Metrik kinerja didasarkan pada ambang probabilitas 50% untuk setiap prediksi (dengan kata lain, jika model menghitung probabilitas 50% atau lebih tinggi bahwa suatu gambar berasal dari kelas tertentu, maka kelas tersebut diprediksi). Anda dapat menyesuaikan ini di kiri atas halaman.

## <a name="test-the-model"></a>Menguji model

Sekarang setelah Anda melatih modelnya, Anda dapat mengujinya.

1. Di atas metrik performa, klik **Uji Cepat**.
2. Di kotak **URL Gambar**, ketik `https://aka.ms/apple-image` dan klik &#10132;
3. Lihat prediksi yang ditampilkan oleh model Anda - skor peluang untuk *apel* harus yang tertinggi, seperti ini:

![Gambar dengan prediksi kelas apel](./images/test-apple.jpg)

4. Tutup jendela **Uji Cepat**.

## <a name="view-the-project-settings"></a>Lihat pengaturan proyek

Proyek yang Anda buat telah diberi pengidentifikasi unik, yang perlu Anda tentukan dalam kode apa pun yang berinteraksi dengannya.

1. Klik ikon *pengaturan* (&#9881;) di kanan atas halaman **Kinerja** untuk melihat pengaturan proyek.
2. Di bawah **Umum** (di sebelah kiri), perhatikan **Project Id** yang secara unik mengidentifikasi proyek ini.
3. Di sebelah kanan, di bawah **Sumber daya** perhatikan bahwa kunci dan titik akhir ditampilkan. Ini adalah detail untuk sumber daya *pelatihan* (Anda juga dapat memperoleh informasi ini dengan melihat sumber daya di portal Microsoft Azure).

## <a name="use-the-training-api"></a>Gunakan *pelatihan* API

Portal Custom Vision menyediakan antarmuka pengguna yang nyaman yang dapat Anda gunakan untuk mengunggah dan menandai gambar, dan melatih model. Namun, dalam beberapa skenario Anda mungkin ingin mengotomatiskan pelatihan model dengan menggunakan API pelatihan Visi Kustom.

> **Catatan**: Dalam latihan ini, Anda dapat memilih untuk menggunakan API dari **C#** atau **Python** SDK. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, jelajahi folder **17-image_classification** dan perluas folder **C-Sharp** atau **Python** tergantung pada preferensi bahasa Anda.
2. Klik kanan folder **train-classifier** dan buka terminal terintegrasi. Kemudian instal paket Pelatihan Custom Vision dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Training --version 2.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-customvision==3.1.0
```

3. Lihat konten folder **train-classifier**, dan perhatikan bahwa folder tersebut berisi file untuk pengaturan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk mencerminkan titik akhir dan kunci untuk sumber daya *pelatihan* Visi Kustom Anda, dan ID proyek untuk proyek klasifikasi yang Anda buat sebelumnya. Simpan perubahan Anda.
4. Perhatikan bahwa folder **train-classifier** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: train-classifier.py

    Buka file kode dan tinjau kode yang ada di dalamnya, perhatikan detail berikut:
    - Ruang nama dari paket yang Anda instal diimpor
    - Fungsi **Utama** mengambil pengaturan konfigurasi, dan menggunakan kunci dan titik akhir untuk membuat **CustomVisionTrainingClient** yang diautentikasi, yang kemudian digunakan dengan ID proyek untuk membuat **Proyek** referensi ke proyek Anda.
    - Fungsi **Upload_Images** mengambil tag yang ditentukan dalam proyek Custom Vision dan kemudian mengunggah file gambar dari folder bernama terkait ke proyek, menetapkan ID tag yang sesuai.
    - Fungsi **Train_Model** membuat iterasi pelatihan baru untuk proyek dan menunggu pelatihan selesai.
5. Kembalikan terminal terintegrasi untuk folder **train-classifier**, dan masukkan perintah berikut untuk menjalankan program:

**C#**

```
dotnet run
```

**Python**

```
python train-classifier.py
```
    
6. Tunggu hingga program berakhir. Kemudian kembali ke browser Anda dan lihat halaman **Gambar Pelatihan** untuk proyek Anda di portal Custom Vision (menyegarkan browser jika perlu).
7. Verifikasi bahwa beberapa gambar baru yang diberi tag telah ditambahkan ke proyek. Kemudian lihat halaman **Kinerja** dan verifikasi bahwa iterasi baru telah dibuat.

## <a name="publish-the-image-classification-model"></a>Terbitkan model klasifikasi gambar

Sekarang Anda siap untuk memublikasikan model terlatih Anda sehingga dapat digunakan dari aplikasi klien.

1. Di portal Visi Khusus, pada halaman **Kinerja**, klik **&#128504; Publikasikan** untuk memublikasikan model terlatih dengan pengaturan berikut:
    - **Nama model**: pengklasifikasi buah
    - **Sumber Daya Prediksi**: *Sumber daya **prediksi** yang Anda buat sebelumnya yang diakhiri dengan "-Prediction" (<u>bukan</u> sumber pelatihan)* .
2. Di kiri atas halaman **Pengaturan Proyek**, klik ikon *Galeri Proyek* (&#128065;) untuk kembali ke beranda portal Custom Vision, tempat proyek Anda sekarang terdaftar.
3. Pada beranda portal Custom Vision, di kanan atas, klik ikon *pengaturan* (&#9881;) untuk melihat pengaturan layanan Custom Vision Anda. Kemudian, di bawah **Sumber Daya**, temukan sumber daya *prediksi* Anda yang diakhiri dengan "-Prediksi" (<u>bukan</u> sumber daya pelatihan) untuk menentukan **Kunci** dan nilai **Titik Akhir** (Anda juga dapat memperoleh informasi ini dengan melihat sumber daya di portal Microsoft Azure).

## <a name="use-the-image-classifier-from-a-client-application"></a>Gunakan pengklasifikasi gambar dari aplikasi klien

Sekarang setelah Anda memublikasikan model klasifikasi gambar, Anda dapat menggunakannya dari aplikasi klien. Sekali lagi, Anda dapat memilih untuk menggunakan **C#** atau **Python**.

1. Di Visual Studio Code, di folder **17-image-classification**, di subfolder untuk bahasa pilihan Anda (**C-Sharp** atau **Python**), kanan **test-classifier** dan buka terminal terintegrasi. Kemudian masukkan perintah khusus SDK berikut untuk menginstal paket Prediksi Visi Kustom:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.CustomVision.Prediction --version 2.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-customvision==3.1.0
```

> **Catatan**: Paket Python SDK mencakup paket pelatihan dan prediksi, dan mungkin sudah diinstal.

2. Perluas folder **test-classifier** untuk melihat file yang ada di dalamnya, yang digunakan untuk menerapkan aplikasi klien pengujian untuk model klasifikasi gambar Anda.
3. Buka file konfigurasi untuk aplikasi klien Anda (*appsettings.json* untuk C# atau *.env* untuk Python) dan perbarui nilai konfigurasi yang dikandungnya untuk mencerminkan titik akhir dan kunci untuk Visi Kustom Anda *prediksi* sumber daya, ID proyek untuk proyek klasifikasi, dan nama model yang Anda terbitkan (yang seharusnya *pengklasifikasi buah*). Simpan perubahan Anda.
4. Buka file kode untuk aplikasi klien Anda (*Program.cs* untuk C#, *test-classification.py* untuk Python) dan tinjau kode yang ada di dalamnya, perhatikan detail berikut:
    - Ruang nama dari paket yang Anda instal diimpor
    - Fungsi **Utama** mengambil pengaturan konfigurasi, dan menggunakan kunci dan titik akhir untuk membuat **CustomVisionPredictionClient** yang diautentikasi.
    - Objek klien prediksi digunakan untuk memprediksi kelas untuk setiap gambar dalam folder **test-images**, yang menetapkan ID proyek dan nama model untuk setiap permintaan. Setiap prediksi menyertakan probabilitas untuk setiap kemungkinan kelas, dan hanya tag prediksi dengan probabilitas lebih besar dari 50% yang ditampilkan.
5. Kembalikan terminal terintegrasi untuk folder **test-classifier**, dan masukkan perintah khusus SDK berikut untuk menjalankan program:

**C#**

```
dotnet run
```

**Python**

```
python test-classifier.py
```

6. Lihat label (tag) dan skor probabilitas untuk setiap prediksi. Anda dapat melihat gambar dalam folder **test-images** untuk memverifikasi bahwa model telah mengklasifikasikannya dengan benar.

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi lebih lanjut tentang klasifikasi gambar dengan layanan Custom Vision, lihat [dokumentasi Custom Vision](https://docs.microsoft.com/azure/cognitive-services/custom-vision-service/).
