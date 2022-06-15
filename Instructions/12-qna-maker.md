---
lab:
  title: Membuat Solusi Jawaban atas Pertanyaan
  module: Module 6 - Building a QnA Solution
ms.openlocfilehash: 0a71dc2c0185c51d8ccf390afd780dd914366be0
ms.sourcegitcommit: 45e075a4b45a914d378900b4c00451a530d813de
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 05/26/2022
ms.locfileid: "145892512"
---
# <a name="create-a-question-answering-solution"></a>Membuat Solusi Jawaban atas Pertanyaan

Salah satu skenario percakapan yang paling umum adalah memberikan dukungan melalui basis pengetahuan tentang pertanyaan yang sering diajukan (FAQ). Banyak organisasi menerbitkan FAQ sebagai dokumen atau halaman web, yang berfungsi dengan baik untuk kumpulan pertanyaan dan jawaban yang kecil, tetapi dokumen besar dapat sulit dan memakan waktu untuk dicari.

Layanan **Bahasa** mencakup kemampuan *jawaban pertanyaan* yang memungkinkan Anda membuat basis pengetahuan dari pasangan pertanyaan dan jawaban yang dapat ditanyakan menggunakan masukan bahasa alami, dan paling sering digunakan sebagai sumber daya yang dapat digunakan bot untuk mencari jawaban atas pertanyaan yang diajukan oleh pengguna.

> **Catatan**: Kemampuan jawaban atas pertanyaan dalam layanan Bahasa adalah versi terbaru dari layanan QnA Maker - yang masih tersedia sebagai layanan terpisah.

## <a name="clone-the-repository-for-this-course"></a>Kloning repositori untuk kursus ini

Jika Anda belum mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, ikuti langkah-langkah berikut untuk melakukannya. Jika tidak, buka folder kloning di Visual Studio Code.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana pun).
3. Ketika repositori telah dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="create-a-language-resource"></a>Membuat sumber daya Bahasa

Untuk membuat dan menghosting basis pengetahuan untuk menjawab pertanyaan, Anda memerlukan sumber daya **Layanan bahasa** di langganan Azure Anda.

1. Buka portal Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, cari *Bahasa*, dan buat sumber daya **Layanan bahasa**.
3. Klik **Pilih** pada blok **Penjawab pertanyaan kustom**. Kemudian klik **Lanjutkan untuk membuat sumber daya**. Anda harus memasukkan pengaturan berikut:
    
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Region**: *Pilih lokasi yang tersedia*
    - **Nama**: *Masukkan nama unik*
    - **Pricing tier**: Standar S
    - **Lokasi Penelusuran Azure**\*: *Pilih lokasi di kawasan global yang sama dengan sumber daya Bahasa Anda*.
    - **Tingkat harga Penelusuran Azure**: Gratis (F) (*Jika tingkat ini tidak tersedia, pilih Dasar (B)* )
    - **Persyaratan Hukum**: _Setuju_ 
    - **Pemberitahuan AI yang Bertanggung Jawab**: _Setuju_
    
    \*Jawaban atas Pertanyaan Kustom menggunakan Azure Search untuk mengindeks dan mengkueri Dasar Pengetahuan pertanyaan dan jawaban.

4. Tunggu hingga penyebaran selesai, lalu lihat detail penyebaran.

## <a name="create-a-question-answering-project"></a>Membuat Solusi Jawaban atas Pertanyaan

Untuk membuat basis pengetahuan untuk menjawab pertanyaan di sumber daya Bahasa Anda, Anda bisa menggunakan portal Language Studio untuk membuat proyek penjawab pertanyaan. Dalam hal ini, Anda akan membuat basis pengetahuan yang berisi pertanyaan dan jawaban tentang [Microsoft Learn](https://docs.microsoft.com/learn).

1. Di tab browser baru, buka portal *Language Studio* di `https://language.azure.com` dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Jika diminta untuk memilih sumber daya Bahasa, pilih pengaturan berikut:
    - **Direktori Azure**: Direktori Azure yang berisi langganan Anda.
    - **Langganan Azure**: Langganan Azure Anda.
    - **Sumber daya bahasa**: Sumber daya bahasa yang Anda buat sebelumnya.
3. Jika Anda <u>tidak</u> diminta untuk memilih sumber daya bahasa, itu mungkin karena Anda memiliki beberapa sumber daya Bahasa dalam langganan Anda; dalam hal ini:
    1. Pada bilah di bagian atas halaman, klik tombol **Pengaturan (&#9881;)**.
    2. Pada halaman **Pengaturan**, lihat tab **Sumber Daya**.
    3. Pilih sumber daya bahasa yang baru saja Anda buat, dan klik **Ganti sumber daya**.
    4. Di bagian atas halaman, klik **Language Studio** untuk kembali ke beranda Language Studio.
3. Di bagian atas portal Language Studio, di menu **Buat baru**, pilih **Jawaban atas pertanyaan kustom**.
4. Dalam wizard ***Buat proyek**, pada halaman **Pilih pengaturan bahasa**, pilih opsi untuk mengatur bahasa untuk semua proyek dalam sumber daya ini, dan pilih **Bahasa Inggris** sebagai bahasa. Lalu, klik **Berikutnya**.
5. Pada halaman **Masukkan informasi dasar**, masukkan detail berikut, lalu klik **Berikutnya**:
    - **Nama** LearnFAQ
    - **Deskripsi:** FAQ untuk Microsoft Learn
    - **Jawaban default ketika tidak ada jawaban yang dikembalikan**: Maaf, saya tidak mengerti pertanyaannya
6. Pada halaman **Tinjau dan selesaikan**, klik **Buat proyek**.

## <a name="add-a-sources-to-the-knowledge-base"></a>Tambahkan sumber ke basis pengetahuan

Anda dapat membuat basis pengetahuan dari awal, tetapi biasanya dimulai dengan mengimpor pertanyaan dan jawaban dari halaman FAQ atau dokumen yang ada. Dalam hal ini, Anda akan mengimpor data dari halaman web FAQ yang ada untuk dipelajari Microsoft, dan Anda juga akan mengimpor beberapa pertanyaan dan jawaban "obrolan" yang telah ditentukan sebelumnya untuk mendukung pertukaran percakapan umum.

1. Pada halaman **Kelola sumber** untuk proyek jawaban atas pertanyaan Anda, di daftar **&#9547; Tambahkan sumber**, pilih **URL**. Kemudian dalam kotak dialog **Tambahkan URL**, klik **&#9547; Tambahkan url** dan tambahkan URL berikut, lalu klik **Tambahkan semua** untuk menambahkannya ke Basis Pengetahuan:
    - **Nama**: `Learn FAQ Page`
    - **URL**: `https://docs.microsoft.com/en-us/learn/support/faq`
2. Pada halaman **Kelola sumber** untuk proyek jawaban atas pertanyaan Anda, di daftar **&#9547; Tambahkan sumber**, pilih **Chitchat**. Di kotak dialog **Tambahkan chit chat**, pilih **Ramah** dan klik **Tambahkan obrolan ringan**.

## <a name="edit-the-knowledge-base"></a>Mengedit Pangkalan Pengetahuan

Basis pengetahuan Anda telah diisi dengan pasangan pertanyaan dan jawaban dari FAQ Microsoft Learn, dilengkapi dengan satu set pasangan tanya jawab percakapan *obrolan*. Anda dapat memperluas basis pengetahuan dengan menambahkan pasangan pertanyaan dan jawaban tambahan.

1. Dalam proyek **LearnFAQ** Anda di Language Studio, pilih halaman **Edit basis pengetahuan** untuk melihat pasangan tanya jawab yang ada (jika beberapa tips ditampilkan, baca dan klik **Mengerti**  untuk menutupnya, atau klik **Lewati semua**)
2. Di basis pengetahuan, pilih **&#65291; Tambahkan pasangan pertanyaan**.
3. Di kotak **Pertanyaan**, ketik `What is Microsoft certification?` dan tekan **Enter****.
4. Pilih **&#65291; Tambahkan frasa alternatif** dan ketik `How can I demonstrate my Microsoft technology skills?` dan tekan **Enter**.
5. Di kotak **Jawab**, ketik `The Microsoft Certified Professional program enables you to validate and prove your skills with Microsoft technologies.` Lalu tekan **Kirim** untuk menambahkan pertanyaan (termasuk frasa alternatif) dan jawaban ke basis pengetahuan.

    Dalam beberapa kasus, masuk akal untuk memungkinkan pengguna menindaklanjuti jawaban untuk membuat percakapan *multi giliran* yang memungkinkan pengguna menyempurnakan pertanyaan secara berulang untuk mendapatkan jawaban yang mereka butuhkan.

6. Di bawah jawaban yang Anda masukkan untuk pertanyaan sertifikasi, pilih **&#65291; Tambahkan petunjuk tindak lanjut**.
7. Di kotak dialog **Perintah Tindak lanjut**, masukkan pengaturan berikut, lalu klik **Tambahkan perintah**:
    - **Teks yang ditampilkan dalam perintah kepada pengguna**: `Learn more about certification`.
    - Pilih **Buat tautan ke pasangan baru**, dan masukkan teks ini: `You can learn more about certification on the [Microsoft certification page](https://docs.microsoft.com/learn/certifications/).`
    - **Tampilkan hanya dalam alur kontekstual**: Dipilih. *Opsi ini memastikan bahwa jawabannya hanya dikembalikan dalam konteks pertanyaan lanjutan dari pertanyaan sertifikasi asli.*

## <a name="train-and-test-the-knowledge-base"></a>Melatih dan menguji pangkalan pengetahuan

Sekarang setelah Anda memiliki basis pengetahuan, Anda dapat mengujinya di Language Studio.

1. Di kanan atas halaman, klik **Simpan perubahan**.
2. Setelah perubahan disimpan, klik **Uji** untuk membuka panel uji.
3. Di panel uji, di bagian atas, *batalkan pilihan* opsi untuk menampilkan jawaban singkat. Kemudian di bagian bawah masukkan pesan `Hello`. Tanggapan yang sesuai harus dikembalikan.
4. Di panel uji, masukkan pesan `What is Microsoft Learn?` di bagian bawah. Respons yang tepat dari FAQ akan muncul.
5. Masukkan pesan `Thanks!` Respons obrolan yang sesuai harus dikembalikan.
6. Masukkan pesan `Tell me about certification`. Jawaban yang Anda buat harus dikembalikan bersama dengan tautan petunjuk tindak lanjut.
7. Pilih tautan tindak lanjut **Pelajari lebih lanjut tentang sertifikasi**. Jawaban tindak lanjut dengan tautan ke halaman sertifikasi harus dikembalikan.
8. Setelah selesai menguji basis pengetahuan, tutup panel pengujian.

## <a name="deploy-and-test-the-knowledge-base"></a>Menyebarkan dan menguji basis pengetahuan

Basis pengetahuan menyediakan layanan back-end yang dapat digunakan aplikasi klien untuk menjawab pertanyaan. Sekarang Anda siap untuk mempublikasikan basis pengetahuan Anda dan mengakses antarmuka REST dari klien.

1. Dalam proyek **LearnFAQ** di Language Studio, pilih halaman **Sebarkan basis pengetahuan**.
2. Di bagian atas halaman, klik **Terapkan**. Kemudian klik **Terapkan** untuk mengonfirmasi bahwa Anda ingin menerapkan basis pengetahuan.
3. Saat penerapan selesai, klik **Dapatkan URL prediksi** untuk melihat titik akhir REST untuk basis pengetahuan Anda, dan salin ke clipboard (tetapi jangan tutup kotak dialog dulu).
4. Di Visual Studio Code, dalam folder **12-qna**, buka **ask-question.cmd**. Skrip ini menggunakan *Curl* untuk memanggil antarmuka REST dari titik akhir jawaban atas pertanyaan.
5. Dalam skrip, ganti *YOUR_PREDICTION_ENDPOINT* dengan titik akhir prediksi yang Anda salin (memastikannya diapit dalam tanda kutip).
6. Kembali ke browser dan dalam kotak dialog **Dapatkan URL prediksi**, perhatikan bahwa permintaan sampel menyertakan nilai untuk parameter **Ocp-Apim-Subscription-Key**, yang terlihat mirip dengan *ab12c345de678fg9hijk01lmno2pqrs34*. Ini adalah kunci autorisasi untuk sumber daya Anda. Salin ke clipboard, lalu klik **Mengerti** untuk menutup kotak dialog.
7. Kembali ke Visual Studio Code, dan dalam skrip **ask-question.cmd**, ganti *YOUR_KEY* dengan kunci yang Anda salin (pastikan diapit tanda kutip).
8. Perhatikan bahwa perintah Curl dalam skrip mengirimkan parameter **pertanyaan** dengan nilai **Apa itu Jalur Pembelajaran?** .
9. Verifikasi bahwa seluruh skrip terlihat mirip dengan kode berikut, lalu simpan file.

    ```
    @echo off
    SETLOCAL ENABLEDELAYEDEXPANSION

    rem Set variables
    set prediction_url="https://some-name.cognitiveservices.azure.com/language/......."
    set key="ab12c345de678fg9hijk01lmno2pqrs34"

    curl -X POST !prediction_url! -H "Ocp-Apim-Subscription-Key: !key!" -H "Content-Type: application/json" -d "{'question': 'What is a learning Path?' }"
    ```

10. Di Visual Studio Code di panel Explorer, klik kanan skrip **ask-question.cmd** dan pilih **Buka di Terminal Terintegrasi**.
11. Di panel terminal, masukkan perintah `ask-question.cmd` untuk menjalankan skrip dan menampilkan respons JSON yang dikembalikan oleh layanan, yang harus berisi jawaban yang sesuai untuk pertanyaan *Apa itu jalur pembelajaran?* .

## <a name="create-a-bot-for-the-knowledge-base"></a>Membuat bot untuk Pangkalan Pengetahuan

Paling umum, aplikasi klien yang digunakan untuk mengambil jawaban dari Basis Pengetahuan adalah bot.

1. Kembali ke Language Studio di browser, dan di halaman **Sebarkan Basis Pengetahuan**, pilih **Buat Bot**. Ini akan membuka portal Azure di tab browser baru sehingga Anda dapat membuat Bot Aplikasi Web di langganan Azure Anda (jika diminta, masuk).
2. Di portal Azure, buat Bot Aplikasi Web dengan pengaturan berikut (sebagian besar akan diisi sebelumnya untuk Anda):

    *Jika beberapa nilai tidak ada, refresh browser Anda.*  

  - **Handle bot**: *Nama unik untuk bot Anda*
  - **Langganan**: *Langganan Azure Anda*
  - **Grup sumber daya**: *Grup sumber daya yang berisi sumber daya Bahasa Anda*
  - **Lokasi**: *Lokasi yang sama dengan layanan Analisis Teks Anda*.
  - **Tingkatan harga**: F0
  - **Nama aplikasi**: *Sama seperti **Handle bot** dengan *.azurewebsites.net* yang ditambahkan secara otomatis
  - **Bahasa SDK**: *Pilih C# atau Node.js*
  - **Kunci Autentikasi QnA**: *Kunci ini akan diatur secara otomatis ke kunci autentikasi untuk Pangkalan Pengetahuan QnA Anda*
  - **Paket/lokasi layanan aplikasi**: *Ini dapat diatur secara otomatis ke rencana dan lokasi yang sesuai jika ada. Jika tidak, buat rencana baru*
  - **Application Insights**: Nonaktif
  - **ID dan kata sandi Aplikasi Microsoft**: Membuat ID dan kata sandi Aplikasi secara otomatis.
3. Tunggu bot Anda dibuat. Kemudian klik **Buka sumber daya** (atau sebagai alternatif, di beranda, klik **Grup sumber daya**, buka grup sumber daya tempat Anda membuat bot aplikasi web, dan klik aplikasi tersebut.)
4. Di panel untuk bot Anda, lihat halaman **Uji di Web Chat**, dan tunggu hingga bot menampilkan pesan **Halo dan selamat datang!** (Mungkin diperlukan beberapa detik untuk menginisialisasi).
5. Gunakan antarmuka obrolan uji untuk memastikan bahwa bot Anda menjawab pertanyaan dari Pangkalan Pengetahuan Anda seperti yang diharapkan. Misalnya, coba kirimkan `What is Microsoft certification?`.

## <a name="more-information"></a>Informasi selengkapnya

Untuk mempelajari selengkapnya tentang menjawab pertanyaan di layanan Bahasa, lihat [dokumentasi layanan Bahasa](https://docs.microsoft.com/en-us/azure/cognitive-services/language-service/question-answering/overview).
