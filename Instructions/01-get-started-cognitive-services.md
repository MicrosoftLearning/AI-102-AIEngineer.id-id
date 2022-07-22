---
lab:
  title: Memulai Cognitive Services
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: a05256a78dee051041320aa3556a43add5596ce9
ms.sourcegitcommit: 5ffc20f6a590fe643c2b695b8dc04589411be36e
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 05/31/2022
ms.locfileid: "145951190"
---
# <a name="get-started-with-cognitive-services"></a>Memulai Cognitive Services

Dalam latihan ini, Anda akan memulai Layanan Kognitif dengan membuat sumber daya **Cognitive Services** di langganan Azure dan menggunakannya dari aplikasi klien. Tujuan dari latihan ini bukan untuk mendapatkan keahlian dalam layanan tertentu, melainkan untuk menjadi terbiasa dengan pola umum untuk penyediaan dan bekerja dengan cognitive services sebagai pengembang.

## <a name="clone-the-repository-for-this-course"></a>Kloning repositori untuk kursus ini

Jika Anda belum mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, ikuti langkah-langkah berikut untuk melakukannya. Jika tidak, buka folder kloning di Visual Studio Code.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (folder mana pun tidak masalah).
3. Ketika repositori telah dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan melakukan debug, pilih **Tidak Sekarang**.

## <a name="provision-a-cognitive-services-resource"></a>Menyediakan sumber daya Cognitive Services

Azure Cognitive Services adalah layanan berbasis cloud yang merangkum kemampuan kecerdasan buatan yang dapat Anda masukkan ke dalam aplikasi Anda. Anda dapat menyediakan sumber daya layanan kognitif individu untuk API tertentu (misalnya, **Bahasa** atau **Computer Vision**), atau Anda dapat menyediakan sumber daya **Azure Cognitive Services** umum yang menyediakan akses ke beberapa API layanan kognitif melalui satu titik akhir dan kunci. Dalam hal ini, Anda akan menggunakan satu sumber daya **Cognitive Services**.

1. Buka portal Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, cari *Cognitive Services*, dan buat sumber daya **Cognitive Services** dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Wilayah**: *Pilih wilayah yang tersedia*
    - **Nama**: *Masukkan nama unik*
    - **Tingkat harga**: Standar S0
3. Pilih kotak centang yang diperlukan dan buat sumber daya.
4. Tunggu hingga penyebaran selesai, lalu lihat detail penyebaran.
5. Buka sumber daya dan lihat halaman **Kunci dan Titik Akhir**. Halaman ini berisi informasi yang Anda perlukan untuk terhubung ke sumber daya Anda dan menggunakannya dari aplikasi yang Anda kembangkan. Khususnya:
    - *Titik akhir* HTTP tempat aplikasi klien dapat mengirim permintaan.
    - Dua *kunci* yang dapat digunakan untuk autentikasi (aplikasi klien dapat menggunakan salah satu kunci untuk mengautentikasi).
    - *Lokasi* tempat sumber daya dihosting. Ini diperlukan untuk permintaan ke beberapa (tetapi tidak semua) API.

## <a name="use-a-rest-interface"></a>Gunakan Antarmuka REST

API layanan kognitif berbasis REST, sehingga Anda dapat menggunakannya dengan mengirimkan permintaan JSON melalui HTTP. Dalam contoh ini, Anda akan menjelajahi aplikasi konsol yang menggunakan **Bahasa** REST API untuk melakukan deteksi bahasa; tetapi prinsip dasarnya sama untuk semua API yang didukung oleh sumber daya Azure Cognitive Services.

> **Catatan**: Dalam latihan ini, Anda dapat memilih untuk menggunakan REST API dari **C#** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, ramban ke folder **01-getting-started** dan luaskan **C-Sharp** atau **Python** folder tergantung pada preferensi bahasa Anda.
2. Lihat konten folder **rest-client**, dan perhatikan bahwa folder tersebut berisi file untuk setelan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk mencerminkan **titik akhir** dan **kunci** autentikasi untuk sumber daya cognitive services Anda. Simpan perubahan Anda.
3. Perhatikan bahwa folder **rest-client** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: rest-client.py

    Buka file kode dan tinjau kode yang ada di dalamnya, perhatikan detail berikut:
    - Berbagai ruang nama diimpor untuk mengaktifkan komunikasi HTTP
    - Kode dalam fungsi **Utama** mengambil titik akhir dan kunci untuk sumber daya cognitive services Anda - ini akan digunakan untuk mengirim permintaan REST ke layanan Analisis Teks.
    - Program menerima masukan pengguna, dan menggunakan fungsi **GetLanguage** untuk memanggil API REST deteksi bahasa Analisis Teks untuk titik akhir cognitive services Anda guna mendeteksi bahasa teks yang dimasukkan.
    - Permintaan yang dikirim ke API terdiri dari objek JSON yang berisi data masukan - dalam hal ini, kumpulan objek **dokumen**, yang masing-masing memiliki **id** dan **teks**.
    - Kunci untuk layanan Anda disertakan dalam header permintaan untuk mengautentikasi aplikasi klien Anda.
    - Respons dari layanan adalah objek JSON, yang dapat diurai oleh aplikasi klien.
4. Klik kanan folder **rest-client** dan buka terminal terintegrasi. Kemudian masukkan perintah khusus bahasa berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python rest-client.py
    ```

5. Saat diminta, masukkan beberapa teks dan tinjau bahasa yang dideteksi oleh layanan, yang dikembalikan dalam respons JSON. Misalnya, coba masukkan "Halo", "Bonjour", dan "Gracias".
6. Setelah Anda selesai menguji aplikasi, masukkan "berhenti" untuk menghentikan program.

## <a name="use-an-sdk"></a>Menggunakan SDK

Anda dapat menulis kode yang menggunakan REST API cognitive services secara langsung, tetapi ada kit pengembangan perangkat lunak (SDK) untuk banyak bahasa pemrograman populer, termasuk Microsoft C#, Python, dan Node.js. Menggunakan SDK dapat sangat menyederhanakan pengembangan aplikasi yang menggunakan cognitive services.

1. Di Visual Studio Code, di panel **Explorer**, di folder **01-getting-started**, luaskan **C-Sharp** atau **Python** folder tergantung pada preferensi bahasa Anda.
2. Klik kanan folder **sdk-client** dan buka terminal terintegrasi. Kemudian pasang paket Text Analytics SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.1.0
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.1.0
    ```

3. Lihat konten folder **sdk-client**, dan perhatikan bahwa folder tersebut berisi file untuk pengaturan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk mencerminkan **titik akhir** dan **kunci** autentikasi untuk sumber daya cognitive services Anda. Simpan perubahan Anda.
    
4. Perhatikan bahwa folder **sdk-client** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: sdk-client.py

    Buka file kode dan tinjau kode yang ada di dalamnya, perhatikan detail berikut:
    - Namespace untuk SDK yang Anda instal telah diimpor
    - Kode dalam fungsi **Utama** mengambil titik akhir dan kunci untuk sumber daya cognitive services Anda - ini akan digunakan dengan SDK untuk membuat klien untuk layanan Analisis Teks.
    - Fungsi **GetLanguage** menggunakan SDK untuk membuat klien untuk layanan, dan kemudian menggunakan klien untuk mendeteksi bahasa teks yang dimasukkan.
5. Kembali ke terminal terintegrasi untuk folder **sdk-client**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python sdk-client.py
    ```

6. Saat diminta, masukkan beberapa teks dan tinjau bahasa yang terdeteksi oleh layanan. Misalnya, coba masukkan "Selamat tinggal", "Au revoir", dan "Hasta la vista".
7. Setelah Anda selesai menguji aplikasi, masukkan "berhenti" untuk menghentikan program.

> **Catatan**: Beberapa bahasa yang memerlukan rangkaian karakter Unicode mungkin tidak dikenali dalam aplikasi konsol sederhana ini.

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang Azure Cognitive Services, lihat [dokumentasi Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/what-are-cognitive-services).
