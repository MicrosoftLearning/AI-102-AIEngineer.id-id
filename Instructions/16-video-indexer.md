---
lab:
  title: Menganalisis Video dengan Video Analyzer
  module: Module 8 - Getting Started with Computer Vision
ms.openlocfilehash: 50223cdfeb0a22933858d595d9329f8b8dcd873d
ms.sourcegitcommit: e20d9099aaecdefa62a763dae24833b97e3d9f6d
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 07/11/2022
ms.locfileid: "147052854"
---
# <a name="analyze-video-with-video-analyzer"></a>Menganalisis Video dengan Video Analyzer

Sebagian besar data yang dibuat dan digunakan saat ini dalam format video. **Video Analyzer for Media** adalah layanan yang didukung AI yang dapat Anda gunakan untuk mengindeks video dan mengekstrak wawasan video.

> **Catatan**: Mulai 21 Juni 2022, kemampuan layanan kognitif yang mengembalikan informasi identitas pribadi dibatasi untuk pelanggan yang telah diberikan [akses terbatas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-limited-access). Tanpa mendapatkan persetujuan akses terbatas, mengenali orang dan selebriti dengan Video Analyzer untuk lab ini tidak tersedia. Untuk detail selengkapnya tentang perubahan yang telah dilakukan Microsoft, dan mengapa - lihat [Investasi dan perlindungan AI yang bertanggung jawab untuk pengenalan wajah](https://azure.microsoft.com/blog/responsible-ai-investments-and-safeguards-for-facial-recognition/).

## <a name="clone-the-repository-for-this-course"></a>Kloning repositori untuk kursus ini

Jika Anda telah mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, buka di Visual Studio Code; jika tidak, ikuti langkah-langkah ini untuk mengkloningnya sekarang.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana pun).
3. Ketika repositori telah dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="upload-a-video-to-video-analyzer"></a>Mengunggah video ke Video Analyzer

Pertama, Anda harus masuk ke portal Video Analyzer dan mengunggah video.

> **Tips**: Jika halaman Video Analyzer dimuat dengan lambat di lingkungan lab yang dihosting, gunakan browser yang diinstal secara lokal. Anda dapat beralih kembali ke VM yang dihosting untuk tugas selanjutnya.

1. Di browser Anda, buka portal Video Analyzer di `https://www.videoindexer.ai`.
2. Jika Anda sudah memiliki akun Video Analyzer, masuk. Jika belum, daftar untuk mendapatkan akun gratis dan masuk menggunakan akun Microsoft Anda (atau jenis akun valid lainnya). Jika Anda mengalami kesulitan masuk, coba buka sesi browser privat.
3. Di Video Analyzer, pilih opsi **Unggah**. Kemudian pilih opsi untuk **memasukkan URL file** dan masukkan `https://aka.ms/responsible-ai-video`. Ubah nama default menjadi **AI yang Bertanggung Jawab**, tinjau pengaturan default, centang kotak guna memverifikasi kepatuhan terhadap kebijakan Microsoft untuk pengenalan wajah, dan unggah file.
4. Setelah file diunggah, tunggu beberapa menit sementara Video Analyzer secara otomatis mengindeksnya.

> **Catatan**: Dalam latihan ini, kami menggunakan video ini untuk menjelajahi fungsionalitas Video Analyzer; tetapi Anda harus meluangkan waktu untuk menontonnya hingga selesai setelah menyelesaikan latihan karena berisi informasi dan panduan yang berguna untuk mengembangkan aplikasi yang mendukung AI secara bertanggung jawab! 

## <a name="review-video-insights"></a>Meninjau wawasan video

Proses pengindeksan mengekstrak wawasan dari video, yang dapat Anda lihat di portal.

1. Di portal Video Analyzer, saat video diindeks, pilih untuk melihatnya. Anda akan melihat pemutar video di samping panel yang menunjukkan wawasan yang diambil dari video.

![Video Analyzer dengan pemutar video dan panel Wawasan](./images/video-indexer-insights.png)

2. Saat video diputar, pilih tab **Garis Waktu** untuk melihat transkrip audio video.

![Video Analyzer dengan pemutar video dan panel Garis Waktu yang menampilkan transkrip video.](./images/video-indexer-transcript.png)

3. Di kanan atas portal, pilih simbol **Tampilan** (yang terlihat mirip dengan &#128455;), dan di daftar wawasan, selain **Transkrip**, pilih **OCR** dan **Pembicara**.

![Menu tampilan Video Analyzer dengan Transkrip, OCR, dan Pembicara dipilih](./images/video-indexer-view-menu.png)

4. Perhatikan bahwa panel **Garis Waktu** sekarang menyertakan:
    - Transkrip narasi audio.
    - Teks terlihat dalam video.
    - Indikasi pembicara yang muncul dalam video. Beberapa orang terkenal secara otomatis dikenali dengan nama, yang lain ditunjukkan dengan nomor (misalnya *Pembicara #1*).
5. Beralih kembali ke panel **Wawasan** dan lihat tampilan wawasan di sana. Dukungan tersebut termasuk:
    - Masing-masing orang yang muncul dalam video.
    - Topik yang dibahas dalam video.
    - Label untuk objek yang muncul di video.
    - Entitas bernama, seperti orang dan merek yang muncul dalam video.
    - Adegan kunci.
6. Dengan panel **Wawasan** terlihat, pilih simbol **Tampilan** lagi, dan dalam daftar wawasan, tambahkan **Kata Kunci** dan **Sentimen** ke panel.

    Wawasan yang ditemukan dapat membantu Anda menentukan tema utama dalam video. Misalnya, **topik** untuk video ini menunjukkan bahwa video ini jelas tentang teknologi, tanggung jawab sosial, dan etika.

## <a name="search-for-insights"></a>Cari wawasan

Anda dapat menggunakan Video Analyzer untuk mencari video guna mendapatkan wawasan.

1. Di panel **Wawasan**, di kotak **Pencarian**, masukkan *Lebah*. Anda mungkin perlu menggulir ke bawah di panel Wawasan untuk melihat hasil semua jenis wawasan.
2. Amati bahwa satu *label* yang cocok ditemukan, dengan lokasinya dalam video yang ditunjukkan di bawahnya.
3. Pilih awal bagian tempat keberadaan lebah ditunjukkan, dan lihat video pada saat itu (Anda mungkin perlu menjeda video dan memilih dengan hati-hati - lebah hanya muncul sebentar!)
4. Kosongkan kotak **Pencarian** guna menampilkan semua wawasan untuk video tersebut.

![Hasil pencarian Video Analyzer untuk Lebah](./images/video-indexer-search.png)

## <a name="use-video-analyzer-widgets"></a>Menggunakan widget Video Analyzer

Portal Video Analyzer adalah antarmuka yang berguna untuk mengelola proyek pengindeksan video. Namun, ada kalanya Anda ingin membuat video dan wawasannya tersedia bagi orang-orang yang tidak memiliki akses ke akun Video Analyzer Anda. Video Analyzer menyediakan widget yang dapat Anda sematkan di halaman web untuk tujuan ini.

1. Di Visual Studio Code, di folder **16-video-indexer**, buka **analyze-video.html**. Ini adalah halaman HTML dasar tempat Anda akan menambahkan widget **Pemutar** dan **Wawasan** Video Analyzer. Perhatikan referensi ke skrip **vb.widgets.mediator.js** di header - skrip ini memungkinkan beberapa widget Video Analyzer pada halaman untuk berinteraksi satu sama lain.
2. Di portal Video Analyzer, kembali ke halaman **File media** dan buka video **AI yang Bertanggung Jawab**.
3. Di bagian pemutar video, pilih **&lt;/&gt; Sematkan** guna melihat kode iframe HTML untuk menyematkan widget.
4. Di kotak dialog **Bagikan dan Sematkan**, pilih widget **Pemutar**, atur ukuran video ke 560 x 315, lalu salin kode semat ke clipboard.
5. Di Visual Studio Code, di file **analyze-video.html**, tempel kode yang disalin di bagian komentar **&lt;-- Widget pemutar ada di sini -- &gt;** .
6. Kembali ke kotak dialog **Bagikan dan Sematkan**, pilih widget **Wawasan**, lalu salin kode semat ke clipboard. Kemudian tutup kotak dialog **Bagikan dan Sematkan**, alihkan kembali ke Visual Studio Code, dan tempel kode yang disalin di bagian komentar **&lt;-- Widget Wawasan ada di sini -- &gt;** .
7. Simpan file. Kemudian di panel **Explorer**, klik kanan **analyze-video.html** dan pilih **Ungkap di File Explorer**.
8. Di File Explorer, buka **analyze-video.html** di browser Anda untuk melihat halaman web.
9. Bereksperimenlah dengan widget, menggunakan widget **Wawasan** untuk mencari wawasan dan membukanya di video.

![Widget Video Analyzer di halaman web](./images/video-indexer-widgets.png)

## <a name="use-the-video-analyzer-rest-api"></a>Menggunakan REST API Video Analyzer

Video Analyzer menyediakan REST API yang dapat Anda gunakan untuk mengunggah dan mengelola video di akun Anda.

### <a name="get-your-api-details"></a>Mendapatkan detail API Anda

Untuk menggunakan API Video Analyzer, Anda memerlukan beberapa informasi untuk mengautentikasi permintaan:

1. Di portal Video Analyzer, luaskan menu (≡) dan pilih halaman **Pengaturan akun**.
2. Catat **ID Akun** di halaman ini - Anda akan membutuhkannya nanti.
3. Buka tab browser baru dan buka portal pengembang Video Analyzer di `https://api-portal.videoindexer.ai`, masuk menggunakan kredensial akun Video Analyzer Anda.
4. Pada halaman **Profil**, lihat **Langganan** yang terkait dengan profil Anda.
5. Pada halaman dengan langganan Anda, perhatikan bahwa Anda telah diberi dua kunci (primer dan sekunder) untuk setiap langganan. Kemudian pilih **Tampilkan** salah satu tombol untuk melihatnya. Anda akan membutuhkan kunci ini segera.

### <a name="use-the-rest-api"></a>Menggunakan REST API

Sekarang setelah Anda memiliki ID akun dan kunci API, Anda dapat menggunakan REST API untuk bekerja dengan video di akun Anda. Dalam prosedur ini, Anda akan menggunakan skrip PowerShell untuk melakukan panggilan REST; tetapi prinsip yang sama berlaku dengan utilitas HTTP seperti cURL atau Postman, atau bahasa pemrograman apa pun yang mampu mengirim dan menerima JSON melalui HTTP.

Semua interaksi dengan REST API Video Analyzer mengikuti pola yang sama:

- Permintaan awal ke metode **AccessToken** dengan kunci API di header digunakan untuk mendapatkan token akses.
- Permintaan berikutnya menggunakan token akses untuk mengautentikasi saat memanggil metode REST agar berfungsi dengan video.

1. Di Visual Studio Code, di folder **16-video-indexer**, buka **get-videos.ps1**.
2. Dalam skrip PowerShell, ganti tempat penampung **YOUR_ACCOUNT_ID** dan **YOUR_API_KEY** dengan ID akun dan nilai kunci API yang Anda identifikasi sebelumnya.
3. Perhatikan bahwa *lokasi* untuk akun gratis adalah "percobaan". Jika Anda sudah membuat akun Video Analyzer yang tidak dibatasi (dengan sumber daya Azure terkait), Anda dapat mengubahnya ke lokasi tempat sumber daya Azure Anda disediakan (misalnya "eastus").
4. Tinjau kode dalam skrip, perhatikan bahwa memanggil dua metode REST: satu untuk mendapatkan token akses, dan satu lagi untuk membuat daftar video di akun Anda.
5. Simpan perubahan Anda, lalu di kanan atas panel skrip, gunakan tombol **&#9655;** untuk menjalankan skrip.
6. Lihat respons JSON dari layanan REST, yang seharusnya berisi detail video **AI yang Bertanggung Jawab** yang Anda indeks sebelumnya.

## <a name="more-information"></a>Informasi selengkapnya

Pengenalan orang dan selebriti masih tersedia, tetapi mengikuti [Standar AI yang Bertanggung Jawab](https://aka.ms/aah91ff) yang dibatasi di balik kebijakan Akses Terbatas. Fitur-fitur ini termasuk identifikasi wajah dan pengenalan selebriti. Untuk mempelajari selengkapnya dan mengajukan permohonan akses, lihat [Akses Terbatas untuk Cognitive Services](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-limited-access).

Untuk informasi selengkapnya tentang **Video Analyzer**, lihat [dokumentasi Video Analyzer](https://docs.microsoft.com/azure/azure-video-analyzer/video-analyzer-for-media-docs/).
