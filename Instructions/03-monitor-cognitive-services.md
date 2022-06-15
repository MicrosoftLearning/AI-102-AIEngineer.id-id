---
lab:
  title: Pantau Cognitive Services
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: e0e0042421a4f7150fc3b95cef80887c81a78f3a
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 01/12/2022
ms.locfileid: "145195640"
---
# <a name="monitor-cognitive-services"></a>Pantau Cognitive Services

Azure Cognitive Services dapat menjadi bagian penting dari infrastruktur aplikasi secara keseluruhan. Sangat penting untuk dapat memantau aktivitas dan mendapatkan peringatan tentang masalah yang mungkin memerlukan perhatian.

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda sudah mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, buka di Visual Studio Code; jika belum, ikuti langkah-langkah ini untuk mengkloningnya sekarang.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="provision-a-cognitive-services-resource"></a>Menyediakan sumber daya Cognitive Services

Jika Anda belum memilikinya dalam langganan, Anda harus menyediakan sumber daya **Cognitive Services**.

1. Buka portal Microsoft Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, jelajahi *layanan kognitif*, dan buat sumber daya **Cognitive Services** dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Wilayah**: *Pilih wilayah yang tersedia*
    - **Nama**: *Masukkan nama yang unik*
    - **Tingkat harga**: Standar S0
3. Pilih kotak centang yang diperlukan dan buat sumber daya.
4. Tunggu hingga penyebaran selesai, lalu lihat detail penyebaran.
5. Saat sumber daya telah diterapkan, buka dan lihat halaman **Kunci dan Titik Akhir**. Catat URI titik akhir - Anda akan membutuhkannya nanti.

## <a name="configure-an-alert"></a>Konfigurasikan peringatan

Mari mulai memantau dengan menentukan aturan peringatan sehingga Anda dapat mendeteksi aktivitas di sumber daya layanan kognitif Anda.

1. Di portal Microsoft Azure, buka sumber daya layanan kognitif Anda dan lihat halaman **Peringatan** (di bagian **Pemantauan**).
2. Pilih **+ Aturan peringatan baru**
3. Di halaman **Buat aturan peringatan**, di bawah **Cakupan**, verifikasi bahwa sumber daya layanan kognitif Anda terdaftar.
4. Di bawah **Kondisi**, klik **Tambahkan Kondisi**, dan lihat panel **Pilih sinyal** yang muncul di sebelah kanan, tempat Anda dapat memilih jenis sinyal untuk dipantau.
5. Dalam daftar **jenis sinyal**, pilih **Log Aktivitas**, lalu dalam daftar yang difilter, pilih **Kunci Daftar**.
6. Tinjau aktivitas selama 6 jam terakhir.
7. Pilih tab **Tindakan**. Perhatikan bahwa Anda dapat menentukan *grup tindakan*. Ini memungkinkan Anda untuk mengonfigurasi tindakan otomatis saat peringatan diaktifkan - misalnya, mengirim pemberitahuan email. Kami tidak akan melakukannya dalam latihan ini; tetapi dapat berguna untuk melakukan ini di lingkungan produksi.
8. Di tab **Detail**, atur **Nama aturan peringatan** ke **Peringatan Daftar Kunci**.
9. Pilih **Tinjau + buat**. 
10. Tinjau konfigurasi untuk peringatan. Pilih **Buat** dan tunggu aturan peringatan dibuat.
11. Di Visual Studio Code, klik kanan folder **03-monitor** dan buka terminal terintegrasi. Kemudian masukkan perintah berikut untuk masuk ke langganan Azure Anda dengan menggunakan Azure CLI.

    ```
    az login
    ```

    Tab browser web akan terbuka dan meminta Anda untuk masuk ke Azure. Lakukan, lalu tutup tab browser dan kembali ke Visual Studio Code.

    > **Tips**: Jika Anda memiliki banyak langganan, Anda harus memastikan bahwa Anda bekerja di langganan yang berisi sumber daya layanan kognitif Anda.  Gunakan perintah ini untuk menentukan langganan Anda saat ini.
    >
    > ```
    > az account show
    > ```
    >
    > Jika Anda perlu mengubah langganan, jalankan perintah ini, ubah *&lt;subscriptionName&gt;* menjadi nama langganan yang benar.
    >
    > ```
    > az account set --subscription <subscriptionName>
    > ```

10. Sekarang Anda dapat menggunakan perintah berikut untuk mendapatkan daftar kunci layanan kognitif, mengganti *&lt;resourceName&gt;* dengan nama sumber daya layanan kognitif Anda, dan *&lt;resourceGroup&gt;*  dengan nama grup sumber daya tempat Anda membuatnya.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

Perintah mengembalikan daftar kunci untuk sumber daya layanan kognitif Anda.

11. Beralih kembali ke browser yang berisi portal Microsoft Azure, dan segarkan **Halaman peringatan** Anda. Anda akan melihat peringatan **Sev 4** yang tercantum dalam tabel (jika tidak, tunggu hingga lima menit dan segarkan kembali).
12. Pilih peringatan untuk melihat detailnya.

## <a name="visualize-a-metric"></a>Visualisasikan metrik

Selain menentukan peringatan, Anda dapat melihat metrik untuk sumber daya layanan kognitif Anda untuk memantau pemanfaatannya.

1. Di portal Azure, di halaman sumber daya layanan kognitif Anda, pilih **Metrik** (di bagian **Pemantauan**).
2. Jika tidak ada bagan yang ada, pilih **+ Bagan baru**. Kemudian dalam daftar **Metrik**, tinjau kemungkinan metrik yang dapat Anda visualisasikan dan pilih **Total Panggilan**.
3. Dalam daftar **Agregasi**, pilih **Hitung**.  Ini akan memungkinkan Anda untuk memantau total panggilan ke sumber daya Layanan Kognitif Anda; yang berguna dalam menentukan berapa banyak layanan yang digunakan selama periode waktu tertentu.
4. Untuk menghasilkan beberapa permintaan ke layanan kognitif Anda, Anda akan menggunakan **curl** - alat baris perintah untuk permintaan HTTP. Dalam Visual Studio Code, dalam folder **03-monitor**, buka **rest-test.cmd** dan edit perintah **curl** yang ada di dalamnya (ditampilkan di bawah), menggantikan *&lt;yourEndpoint&gt;* dan *&lt;yourKey&gt;* dengan URI titik akhir dan kunci **Key1** Anda untuk menggunakan Text Analytics API di sumber daya layanan kognitif Anda.

    ```
    curl -X POST "<yourEndpoint>/text/analytics/v3.1/languages?" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <yourKey>" --data-ascii "{'documents':           [{'id':1,'text':'hello'}]}"
    ```

5. Simpan perubahan Anda, lalu di terminal terintegrasi untuk folder **03-monitor**, jalankan perintah berikut:

    ```
    rest-test
    ```

Perintah mengembalikan dokumen JSON yang berisi informasi tentang bahasa yang terdeteksi dalam data input (yang seharusnya bahasa Inggris).

6. Jalankan kembali perintah **rest-test** beberapa kali untuk menghasilkan beberapa aktivitas panggilan (Anda dapat menggunakan tombol **^** untuk menelusuri perintah sebelumnya).
7. Kembali ke halaman **Metrik** di portal Microsoft Azure dan segarkan bagan jumlah **Total Panggilan**. Mungkin perlu beberapa menit agar panggilan yang Anda lakukan menggunakan *curl* terlihat di bagan - terus segarkan bagan hingga diperbarui untuk menyertakannya.

## <a name="more-information"></a>Informasi selengkapnya

Salah satu opsi untuk memantau layanan kognitif adalah menggunakan *pembuatan log diagnostik*. Setelah diaktifkan, pembuatan log diagnostik menangkap informasi yang kaya tentang penggunaan sumber daya layanan kognitif Anda, dan dapat menjadi alat pemantauan dan penelusuran kesalahan yang berguna. Diperlukan waktu lebih dari satu jam setelah menyiapkan pembuatan log diagnostik untuk menghasilkan informasi apa pun, itulah sebabnya kami belum menjelajahinya dalam latihan ini; tetapi Anda dapat mempelajarinya lebih lanjut di [dokumentasi Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/diagnostic-logging).
