---
lab:
  title: Mengekstrak Data dari Formulir
  module: Module 11 - Reading Text in Images and Documents
ms.openlocfilehash: 540fdc49b9efcf335d43cdd7a6db405c255cd058
ms.sourcegitcommit: de1f38bbe53ec209b42cd89516813773e2f3479b
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 05/17/2022
ms.locfileid: "145195636"
---
# <a name="extract-data-from-forms"></a>Mengekstrak Data dari Formulir 

Misalkan perusahaan saat ini mengharuskan karyawan untuk membeli lembar pesanan secara manual dan memasukkan data ke dalam database. Mereka ingin Anda menggunakan layanan AI untuk meningkatkan proses entri data. Anda memutuskan untuk membangun model pembelajaran mesin yang akan membaca formulir dan menghasilkan data terstruktur yang dapat digunakan untuk memperbarui database secara otomatis.

**Form Recognizer** adalah layanan kognitif yang memungkinkan pengguna membuat perangkat lunak pemrosesan data otomatis. Perangkat lunak ini dapat mengekstrak teks, pasangan kunci/nilai, dan tabel dari dokumen formulir menggunakan pengenalan karakter optik (OCR). Form Recognizer memiliki model bawaan untuk mengenali faktur, tanda terima, dan kartu nama. Layanan ini juga menyediakan kemampuan untuk melatih model kustom. Dalam latihan ini, kita akan fokus membangun model kustom.

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda belum melakukannya, Anda harus mengkloning repositori kode untuk kursus ini:

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="create-a-form-recognizer-resource"></a>Membuat sumber daya Form Recognizer

Untuk menggunakan layanan Pengenal Formulir, Anda memerlukan sumber Form Recognizer atau Cognitive Services di langganan Azure Anda. Anda akan menggunakan portal Microsoft Azure untuk membuat sumber daya.

1.  Buka portal Microsoft Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.

2. Pilih tombol **&#65291;Buat sumber daya**, cari *Form Recognizer*, dan buat sumber daya **Form Recognizer** dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Wilayah**: *Pilih wilayah yang tersedia*
    - **Nama**: *Masukkan nama yang unik*
    - **Tingkatan harga**: F0

    > **Catatan**: Jika Anda sudah memiliki layanan form recognizer F0 dalam langganan Anda, pilih **S0** untuk yang ini.

3. Saat sumber daya telah diterapkan, buka dan lihat halaman **Kunci dan Titik Akhir**. Anda akan memerlukan **titik akhir** dan salah satu **kunci** dari halaman ini untuk mengelola akses dari kode Anda nanti. 

## <a name="gather-documents-for-training"></a>Mengumpulkan dokumen untuk pelatihan

![Gambar faktur.](../21-custom-form/sample-forms/Form_1.jpg)  

Anda akan menggunakan formulir sampel dari folder **21-custom-form/sample-forms** dalam repositori ini, yang berisi semua file yang Anda perlukan untuk melatih dan menguji model.

1. Di Visual Studio Code, di folder **21-custom-form**, perluas folder **sample-forms**. Perhatikan ada file yang berakhiran **.json** dan **.jpg** di folder.

    Anda akan menggunakan file **.jpg** untuk melatih model Anda.  

    File **.json** telah dibuat untuk Anda dan berisi informasi label. File akan diunggah ke dalam kontainer penyimpanan blob Anda bersama formulir. 

2. Kembali ke portal Microsoft Azure di [https://portal.azure.com](https://portal.azure.com).

3. Lihat **Grup sumber daya** tempat Anda membuat sumber daya Form Recognizer sebelumnya.

4. Pada halaman **Ringkasan** untuk grup sumber daya Anda, catat **ID Langganan** dan **Lokasi**. Anda akan memerlukan nilai-nilai ini, bersama dengan nama **grup sumber daya** Anda dalam langkah-langkah berikutnya.

![Contoh halaman grup sumber daya.](./images/resource_group_variables.png)

5. Di Visual Studio Code, di panel Explorer, klik kanan folder **21-custom-form** dan pilih **Buka di Terminal Terintegrasi**.

6. Di panel terminal, masukkan perintah berikut untuk membuat koneksi yang diautentikasi ke langganan Azure Anda.
    
```
az login --output none
```

7. Saat diminta, masuk ke langganan Azure Anda. Kemudian kembali ke Visual Studio Code dan tunggu proses masuk selesai.

8. Jalankan perintah berikut untuk membuat daftar lokasi Azure.

```
az account list-locations -o table
```

9. Pada output, temukan nilai **Name** yang sesuai dengan lokasi grup sumber daya Anda (misalnya, untuk *East US* nama yang sesuai adalah *eastus*).

    > **Penting**: Rekam nilai **Nama** dan gunakan di Langkah 12.

10. Di panel Explorer, di folder **21-custom-form**, pilih **setup.cmd**. Anda akan menggunakan skrip batch ini untuk menjalankan perintah antarmuka baris perintah (CLI) Azure yang diperlukan untuk membuat sumber daya Azure lain yang Anda butuhkan.

11. Dalam skrip **setup.cmd**, tinjau perintah **rem**. Komentar ini menguraikan program yang akan dijalankan skrip. Program ini akan: 
    - Buat akun penyimpanan di grup sumber daya Azure Anda
    - Upload file dari folder _sampleforms_ lokal Anda ke kontainer yang disebut _sampleforms_ di akun penyimpanan
    - Cetak URI Tanda Tangan Akses Bersama

12. Ubah deklarasi variabel **subscription_id**, **resource_group**, dan **lokasi** dengan nilai yang sesuai untuk langganan, grup sumber daya, dan nama lokasi tempat Anda menyebarkan sumber daya Form Recognizer. Kemudian **simpan** perubahan Anda.

    Biarkan variabel **expiry_date** seperti untuk latihan. Variabel ini digunakan saat membuat URI Tanda Tangan Akses Bersama (SAS). Dalam praktiknya, Anda akan ingin menetapkan tanggal kedaluwarsa yang sesuai untuk SAS Anda. Anda dapat mempelajari lebih lanjut tentang SAS [di sini](https://docs.microsoft.com/azure/storage/common/storage-sas-overview#how-a-shared-access-signature-works).  

13. Di terminal untuk folder **21-custom-form**, masukkan perintah berikut untuk menjalankan skrip:

```
setup
```

14. Saat skrip selesai, tinjau output yang ditampilkan dan catat URI SAS sumber daya Azure Anda.

> **Penting**: Sebelum melanjutkan, tempelkan SAS URI di suatu tempat Anda akan dapat mengambilnya lagi nanti (misalnya, dalam file teks baru di Visual Studio Code).

15. Di portal Microsoft Azure, refresh grup sumber daya dan verifikasi bahwa grup tersebut berisi akun Azure Storage yang baru saja dibuat. Buka akun penyimpanan dan di panel di sebelah kiri, pilih **Storage Browser (pratinjau)** . Kemudian di Storage Browser, perluas **KONTAINER BLOB** dan pilih kontainer **sampleforms** untuk memverifikasi bahwa file telah diunggah dari folder **21-custom-form/sample-forms** lokal Anda.

## <a name="train-a-model-using-the-form-recognizer-sdk"></a>Melatih model menggunakan SDK Form Recognizer

Sekarang Anda akan melatih model menggunakan file **.jpg** dan **.json**.

1. Di Visual Studio Code, di folder **21-custom-form/sample-forms**, buka **fields.json** dan tinjau dokumen JSON yang dikandungnya. File ini menentukan bidang yang akan Anda latih model untuk mengekstrak dari formulir.
2. Buka **Form_1.jpg.labels.json** dan tinjau JSON yang ada di dalamnya. File ini mengidentifikasi lokasi dan nilai untuk bidang bernama dalam dokumen pelatihan **Form_1.jpg**.
3. Buka **Form_1.jpg.ocr.json** dan tinjau JSON yang ada di dalamnya. File ini berisi representasi JSOn dari tata letak teks **Form_1.jpg**, termasuk lokasi semua area teks yang ditemukan dalam formulir.

    *File informasi bidang telah disediakan untuk Anda dalam latihan ini. Untuk proyek Anda sendiri, Anda dapat membuat file ini menggunakan [Form Recognizer Studio](https://formrecognizer.appliedai.azure.com/studio). Saat Anda menggunakan alat ini, file informasi bidang Anda secara otomatis dibuat dan disimpan di akun penyimpanan Anda yang terhubung.*

4. Di Visual Studio Code, di folder **21-custom-form**, luaskan folder **C-Sharp** atau **Python** bergantung pada preferensi bahasa Anda.
5. Klik kanan folder **train-model** dan buka terminal terintegrasi.

6. Instal paket Form Recognizer dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

**C#**

```
dotnet add package Azure.AI.FormRecognizer --version 3.0.0 
```

**Python**

```
pip install azure-ai-formrecognizer==3.0.0
```

7. Lihat konten folder **train-model**, dan perhatikan bahwa folder tersebut berisi file untuk setelan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

8. Edit file konfigurasi, memodifikasi pengaturan untuk mencerminkan:
    - **Titik akhir** untuk sumber daya Form Recognizer Anda.
    - **Kunci** untuk sumber daya Form Recognizer Anda.
    - **URI SAS** untuk kontainer blob Anda.

9. Perhatikan bahwa folder **train-model** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: train-model.py

    Buka file kode dan tinjau kode yang ada di dalamnya, perhatikan detail berikut:
    - Ruang nama dari paket yang Anda instal diimpor
    - Fungsi **Utama** mengambil setelan konfigurasi, dan menggunakan kunci dan titik akhir untuk membuat **Klien** yang diautentikasi.
    - Kode ini menggunakan klien pelatihan untuk melatih model menggunakan gambar dalam kontainer penyimpanan blob Anda, yang diakses menggunakan URI SAS yang Anda buat.

10. Di folder **train-model**, buka file kode untuk aplikasi pelatihan:

    - **C#** : Program.cs
    - **Python**: train-model.py

11. Kembalikan terminal terintegrasi untuk folder **train-model**, dan masukkan perintah berikut untuk menjalankan program:

**C#**

```
dotnet run
```

**Python**

```
python train-model.py
```

12. Tunggu hingga program berakhir, lalu tinjau output model.
13. Tuliskan ID Model di output terminal. Anda akan membutuhkannya untuk bagian lab berikutnya. 

## <a name="test-your-custom-form-recognizer-model"></a>Menguji model Form Recognizer kustom Anda 

1. Di folder **21-custom-form**, di subfolder untuk bahasa pilihan Anda (**C-Sharp** atau **Python**), perluas folder **test-model**.

2. Klik kanan folder **test-model** dan pilih **buka terminal terintegrasi**.

3. Di terminal untuk folder **model pengujian**, instal paket Form Recognizer dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

**C#**

```
dotnet add package Azure.AI.FormRecognizer --version 3.0.0 
```

**Python**

```
pip install azure-ai-formrecognizer==3.0.0
```

*Ini tidak benar-benar diperlukan jika Anda sebelumnya menggunakan pip untuk menginstal paket ke lingkungan Python; tetapi tidak membahayakan untuk memastikannya terpasang!*

4. Di terminal yang sama untuk folder **model pengujian**, instal pustaka Tabulate. Ini akan memberikan output Anda dalam tabel:

**C#**

```
Install-Package Tabulate.NET -Version 1.0.5
```

**Python**

```
pip install tabulate
```

5. Dalam folder **test-model**, edit file konfigurasi (**appsettings.json** atau **.env**, bergantung pada preferensi bahasa Anda) untuk menambahkan nilai berikut:
    - Titik akhir Form Recognizer Anda.
    - Kunci Form Recognizer Anda.
    - ID Model yang dihasilkan saat Anda melatih model (Anda dapat menemukannya dengan mengalihkan terminal kembali ke konsol **cmd** untuk folder **train-model**). **Simpan** perubahan Anda.

6. Di folder **test-model**, buka file kode untuk aplikasi klien Anda (*Program.cs* untuk C#, *test-model.py* untuk Python) dan tinjau kode yang dikandungnya, perhatikan detail berikut:
    - Ruang nama dari paket yang Anda instal diimpor
    - Fungsi **Utama** mengambil setelan konfigurasi, dan menggunakan kunci dan titik akhir untuk membuat **Klien** yang diautentikasi.
    - Klien kemudian digunakan untuk mengekstrak bidang formulir dan nilai dari gambar **test1.jpg**.
    

7. Kembalikan terminal terintegrasi untuk folder **test-model**, dan masukkan perintah berikut untuk menjalankan program:

**C#**

```
dotnet run
```

**Python**

```
python test-model.py
```
    
8. Lihat output dan amati bagaimana output untuk model menyediakan nama bidang seperti "CompanyPhoneNumber" dan "DatedAs".   

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang layanan Form Recognizer, lihat [dokumentasi Form Recognizer](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/).
