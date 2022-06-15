---
lab:
  title: Buat Penyimpanan Pengetahuan dengan Azure Cognitive Search
  module: Module 12 - Creating a Knowledge Mining Solution
ms.openlocfilehash: 8d7008ee592d4a76f24ca26a4b192c7dd5cbe9ea
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 11/16/2021
ms.locfileid: "145195635"
---
# <a name="create-a-knowledge-store-with-azure-cognitive-search"></a>Buat Penyimpanan Pengetahuan dengan Azure Cognitive Search

Azure Cognitive Search menggunakan alur pengayaan keterampilan kognitif untuk mengekstrak bidang yang dihasilkan AI dari dokumen dan memasukkannya ke dalam indeks pencarian. Sementara indeks mungkin dianggap sebagai output utama dari proses pengindeksan, data yang diperkaya yang dikandungnya mungkin juga berguna dengan cara lain. Contohnya:

- Karena indeks pada dasarnya adalah kumpulan objek JSON, masing-masing mewakili baris yang diindeks, mungkin berguna untuk mengekspor objek sebagai file JSON untuk integrasi ke dalam proses orkestrasi data menggunakan alat seperti Azure Data Factory.
- Anda mungkin ingin menormalkan baris indeks ke dalam skema tabel relasional untuk analisis dan pelaporan dengan alat seperti Microsoft Power BI.
- Setelah mengekstrak gambar yang disematkan dari dokumen selama proses pengindeksan, Anda mungkin ingin menyimpan gambar tersebut sebagai file.

Dalam latihan ini, Anda akan menerapkan penyimpanan pengetahuan untuk *Margie's Travel*, biro perjalanan fiktif yang menggunakan informasi dalam brosur dan ulasan hotel untuk membantu pelanggan merencanakan perjalanan.

## <a name="clone-the-repository-for-this-course"></a>Kloning repositori untuk kursus ini

Jika Anda telah mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, buka di Visual Studio Code; jika tidak, ikuti langkah-langkah ini untuk mengkloningnya sekarang.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana pun).
3. Ketika repositori telah dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="create-azure-resources"></a>Membuat sumber daya Azure

> **Catatan**: Jika sebelumnya Anda telah menyelesaikan latihan **[Membuat solusi Azure Cognitive Search](22-azure-search.md)** , dan masih memiliki sumber daya Azure ini dalam langganan, Anda dapat melewatkan bagian ini dan mulai dari **Membuat solusi pencarian**. Jika tidak, ikuti langkah-langkah di bawah ini untuk menyediakan sumber daya Azure yang diperlukan.

1. Di browser web, buka portal Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Lihat **Grup sumber daya** di langganan Anda.
3. Jika Anda menggunakan langganan terbatas ketika grup sumber daya telah disediakan untuk Anda, pilih grup sumber daya untuk melihat propertinya. Jika tidak, buat grup sumber daya baru dengan nama pilihan Anda, dan buka grup tersebut setelah dibuat.
4. Pada halaman **Ringkasan** untuk grup sumber daya Anda, catat **ID Langganan** dan **Lokasi**. Anda akan memerlukan nilai-nilai ini, bersama dengan nama grup sumber daya di langkah selanjutnya.
5. Di Visual Studio Code, luaskan folder **24-knowledge-store** dan pilih **setup.cmd**. Anda akan menggunakan skrip batch ini untuk menjalankan perintah antarmuka baris perintah (CLI) Azure yang diperlukan untuk membuat sumber daya Azure yang Anda butuhkan.
6. Klik kanan folder **24-knowledge-store** dan pilih **Buka di Terminal Terintegrasi**.
7. Di panel terminal, masukkan perintah berikut untuk membuat koneksi yang diautentikasi ke langganan Azure Anda.

    ```
    az login --output none
    ```

8. Saat diminta, masuk ke langganan Azure Anda. Kemudian kembali ke Visual Studio Code dan tunggu proses masuk selesai.
9. Jalankan perintah berikut untuk membuat daftar lokasi Azure.

    ```
    az account list-locations -o table
    ```

10. Pada output, temukan nilai **Name** yang sesuai dengan lokasi grup sumber daya Anda (misalnya, untuk *East US* nama yang sesuai adalah *eastus*).
11. Dalam skrip **setup.cmd**, ubah deklarasi variabel **subscription_id**, **resource_group**, dan **location** dengan nilai yang sesuai untuk ID langganan Anda, nama grup sumber daya, dan nama lokasi. Kemudian simpan perubahan Anda.
12. Di terminal untuk folder **24-knowledge-store**, masukkan perintah berikut untuk menjalankan skrip:

    ```
    setup
    ```
    > **Catatan**: Modul CLI Penelusuran sedang dalam pratinjau, dan mungkin macet di *- Running ..* proses. Jika proses ini berjalan selama lebih dari 2 menit, tekan CTRL+C untuk membatalkan operasi yang sudah berjalan lama, lalu pilih **N** saat ditanya apakah Anda ingin menghentikan skrip. Operasi ini kemudian akan selesai dengan sukses.
    >
    > Jika skrip gagal, pastikan Anda menyimpannya dengan nama variabel yang benar dan coba lagi.

13. Saat skrip selesai, tinjau output yang ditampilkan dan catat informasi berikut tentang sumber daya Azure Anda (Anda akan memerlukan nilai ini nanti):
    - Nama akun penyimpanan
    - String koneksi penyimpanan
    - Akun Cognitive Services
    - Kunci Cognitive Services
    - Titik akhir layanan pencarian
    - Kunci admin layanan pencarian
    - Kunci kueri layanan pencarian

14. Di portal Azure, segarkan grup sumber daya dan pastikan grup tersebut berisi akun Azure Storage, sumber daya Azure Cognitive Services, dan sumber daya Azure Cognitive Search.

## <a name="create-a-search-solution"></a>Membuat solusi pencarian

Sekarang setelah Anda memiliki sumber daya Azure yang diperlukan, Anda dapat membuat solusi pencarian yang terdiri dari komponen berikut:

- **Sumber data** yang mereferensikan dokumen di kontainer penyimpanan Azure Anda.
- **Kumpulan keterampilan** yang menentukan alur pengayaan keterampilan untuk mengekstrak bidang yang dihasilkan AI dari dokumen. Kumpulan keterampilan juga menentukan *proyeksi* yang akan dihasilkan di *penyimpanan pengetahuan* Anda.
- **Indeks** yang menentukan kumpulan rekaman dokumen yang dapat dicari.
- **Pengindeks** yang mengekstrak dokumen dari sumber data, menerapkan kumpulan keterampilan, dan mengisi indeks. Proses pengindeksan juga mempertahankan proyeksi yang ditentukan dalam kumpulan keterampilan di penyimpanan pengetahuan.

Dalam latihan ini, Anda akan menggunakan antarmuka REST Azure Cognitive Search untuk membuat komponen ini dengan mengirimkan permintaan JSON.

### <a name="prepare-json-for-rest-operations"></a>Menyiapkan JSON untuk operasi REST

Anda akan menggunakan antarmuka REST untuk mengirimkan definisi JSON untuk komponen Azure Cognitive Search Anda.

1. Di Visual Studio Code, di folder **24-knowledge-store**, luaskan folder **create-search** dan pilih **data_source.json**. File ini berisi definisi JSON untuk sumber data bernama **margies-knowledge-data**.
2. Ganti tempat penampung **YOUR_CONNECTION_STRING** dengan string koneksi untuk akun Azure storage Anda, yang akan terlihat seperti berikut:

    ```
    DefaultEndpointsProtocol=https;AccountName=ai102str123;AccountKey=12345abcdefg...==;EndpointSuffix=core.windows.net
    ```

    *Anda dapat menemukan string koneksi di halaman **Kunci akses** untuk akun penyimpanan Anda di portal Azure.*

3. Simpan dan tutup file JSON yang diperbarui.
4. Dalam folder **create-search**, buka **skillset.json**. File ini berisi definisi JSON untuk kumpulan keterampilan bernama **margies-knowledge-skillset**.
5. Di bagian atas definisi kumpulan kemampuan, di elemen **cognitiveServices**, ganti tempat penampung **YOUR_COGNITIVE_SERVICES_KEY** dengan salah satu kunci untuk sumber daya cognitive service Anda.

    *Anda dapat menemukan kunci di halaman **Kunci dan Titik Akhir** untuk sumber daya cognitive service Anda di portal Azure.*

6. Di akhir kumpulan keterampilan di kumpulan keterampilan Anda, temukan keterampilan **Microsoft.Skills.Util.ShaperSkill** bernama **define-projection**. Keterampilan ini mendefinisikan struktur JSON untuk data yang diperkaya yang akan digunakan untuk proyeksi bahwa alur akan bertahan di penyimpanan pengetahuan untuk setiap dokumen yang diproses oleh pengindeks.
7. Di bagian bawah file kumpulan keterampilan, perhatikan bahwa kumpulan keterampilan juga menyertakan definisi **knowledgeStore**, yang menyertakan string koneksi untuk akun Azure Storage tempat penyimpanan pengetahuan akan dibuat, dan kumpulan **proyeksi**. Kumpulan keterampilan ini mencakup tiga *grup proyeksi*:
    - Grup yang berisi proyeksi *object* berdasarkan output **knowledge_projection** dari keterampilan pembentuk di kumpulan keterampilan.
    - Grup yang berisi proyeksi *file* berdasarkan kumpulan data gambar **normalized_images** yang diekstrak dari dokumen.
    - Grup yang berisi proyeksi *table* berikut:
        - **KeyPhrases**: Berisi kolom kunci yang dibuat secara otomatis dan kolom **keyPhrase** yang dipetakan ke output koleksi **knowledge_projection/key_phrases/** dari keterampilan pembentuk.
        - **Locations**: Berisi kolom kunci yang dibuat secara otomatis dan kolom **location** yang dipetakan ke output koleksi **knowledge_projection/key_phrases/** dari keterampilan pembentuk.
        - **ImageTags**: Berisi kolom kunci yang dibuat secara otomatis dan kolom **tag** yang dipetakan ke output koleksi **knowledge_projection/image_tags/** dari keterampilan pembentuk.
        - **Docs**: Berisi kolom kunci yang dibuat secara otomatis dan semua nilai output **knowledge_projection** dari keterampilan pembentuk yang belum ditetapkan ke tabel.
8. Ganti tempat penampung **YOUR_CONNECTION_STRING** untuk nilai **storageConnectionString** dengan string koneksi untuk akun penyimpanan Anda.
9. Simpan dan tutup file JSON yang diperbarui.
10. Dalam folder **create-search**, buka **index.json**. File ini berisi definisi JSON untuk indeks bernama **margies-knowledge-index**.
11. Tinjau JSON untuk indeks, lalu tutup file tanpa membuat perubahan apa pun.
12. Dalam folder **create-search**, buka **indexer.json**. File ini berisi definisi JSON untuk pengindeks bernama **margies-knowledge-indexer**.
13. Tinjau JSON untuk pengindeks, lalu tutup file tanpa membuat perubahan apa pun.

### <a name="submit-rest-requests"></a>Mengirimkan permintaan REST

Sekarang setelah Anda menyiapkan objek JSON yang menentukan komponen solusi pencarian, Anda dapat mengirimkan dokumen JSON ke antarmuka REST untuk membuatnya.

1. Dalam folder **create-search**, buka **create-search.cmd**. Skrip batch ini menggunakan utilitas cURL untuk mengirimkan definisi JSON ke antarmuka REST untuk sumber daya Azure Cognitive Search Anda.
2. Ganti tempat penampung variabel **YOUR_SEARCH_URL** dan **YOUR_ADMIN_KEY** dengan **URL** dan salah satu **kunci admin** untuk sumber daya Azure Cognitive Search Anda.

    *Anda dapat menemukan nilai ini di halaman **Ringkasan** dan **Kunci** untuk sumber daya Azure Cognitive Search di portal Azure.*

3. Simpan file batch yang diperbarui.
4. Klik kanan folder **create-search** dan pilih **Buka di Terminal Terpadu**.
5. Di panel terminal untuk folder **create-search**, masukkan perintah berikut, jalankan skrip batch.

    ```
    create-search
    ```

6. Saat skrip selesai, di portal Azure pada halaman untuk sumber daya Azure Cognitive Search, pilih halaman **Pengindeks** dan tunggu hingga proses pengindeksan selesai.

    *Anda dapat memilih **Refresh** untuk melacak kemajuan operasi pengindeksan. Mungkin perlu satu menit atau lebih untuk menyelesaikannya.*

    > **Tips**: Jika skrip gagal, periksa tempat penampung yang Anda tambahkan di file **data_source.json** dan **skillset.json** serta file **create-search.cmd**. Setelah memperbaiki kesalahan, Anda mungkin perlu menggunakan antarmuka pengguna portal Azure untuk menghapus komponen apa pun yang dibuat di sumber pencarian Anda sebelum menjalankan kembali skrip.

## <a name="view-the-knowledge-store"></a>Menampilkan penyimpanan pengetahuan

Setelah Anda menjalankan pengindeks yang menggunakan kumpulan keterampilan untuk membuat penyimpanan pengetahuan, data yang diperkaya yang diekstraksi oleh proses pengindeksan akan dipertahankan dalam proyeksi penyimpanan pengetahuan.

### <a name="view-object-projections"></a>Menampilkan proyeksi objek

Proyeksi *objek* yang ditentukan dalam kumpulan kemampuan Margie's Travel terdiri dari file JSON untuk setiap dokumen yang diindeks. File ini disimpan dalam kontainer blob di akun Azure Storage yang ditentukan dalam definisi kumpulan kemampuan.

1. Di portal Azure, lihat akun Azure Storage yang Anda buat sebelumnya.
2. Pilih tab **Penjelajah penyimpanan** (di panel sebelah kiri) untuk melihat akun penyimpanan di antarmuka penjelajah penyimpanan di portal Azure.
2. Luaskan **BLOB CONTAINERS** untuk melihat kontainer di akun penyimpanan. Selain kontainer **margies** tempat data sumber disimpan, harus ada dua kontainer baru: **margies-images** dan **margies-knowledge**. Kedua kontainer tersebut dibuat oleh proses pengindeksan.
3. Pilih kontainer **margies-knowledge**. Kontainer ini harus berisi folder untuk setiap dokumen yang diindeks.
4. Buka salah satu folder, lalu unduh dan buka file **knowledge-projection.json** yang ada di dalamnya. Setiap file JSON berisi representasi dari dokumen yang diindeks, termasuk data yang diperkaya yang diekstraksi oleh kumpulan keterampilan seperti yang ditunjukkan di sini.

```
{
    "file_id":"abcd1234....",
    "file_name":"Margies Travel Company Info.pdf",
    "url":"https://store....blob.core.windows.net/margies/...pdf",
    "language":"en",
    "sentiment":0.83164644241333008,
    "key_phrases":[
        "Margie’s Travel",
        "Margie's Travel",
        "best travel experts",
        "world-leading travel agency",
        "international reach"
        ],
    "locations":[
        "Dubai",
        "Las Vegas",
        "London",
        "New York",
        "San Francisco"
        ],
    "image_tags":[
        "outdoor",
        "tree",
        "plant",
        "palm"
        ]
}
```

Kemampuan untuk membuat proyeksi *object* seperti ini memungkinkan Anda menghasilkan objek data yang diperkaya yang dapat dimasukkan ke dalam solusi analisis data perusahaan - misalnya dengan memasukkan file JSON ke dalam alur Azure Data Factory untuk pemrosesan atau pemuatan lebih lanjut ke dalam gudang data.

### <a name="view-file-projections"></a>Menampilkan proyeksi file

Proyeksi *file* yang ditentukan dalam kumpulan keterampilan membuat file JPEG untuk setiap gambar yang diekstraksi dari dokumen selama proses pengindeksan.

1. Di antarmuka penjelajah penyimpanan di portal Azure, pilih kontainer blob **margies-images**. Kontainer ini berisi folder untuk setiap dokumen yang berisi gambar.
2. Buka salah satu folder dan lihat isinya - setiap folder berisi setidaknya satu file \*.jpg.
3. Buka salah satu file gambar untuk memverifikasi bahwa file tersebut berisi gambar yang diekstrak dari dokumen.

Kemampuan untuk menghasilkan proyeksi *file* seperti ini membuat pengindeksan menjadi cara yang efisien untuk mengekstrak gambar yang disematkan dari sejumlah besar dokumen.

### <a name="view-table-projections"></a>Menampilkan proyeksi tabel

Proyeksi *tabel* yang ditentukan dalam kumpulan keterampilan membentuk skema relasional dari data yang diperkaya.

1. Di antarmuka penjelajah penyimpanan di portal Azure, luaskan **TABLES**.
2. Pilih tabel **Docs** untuk melihat kolomnya. Kolom tersebut menyertakan beberapa kolom tabel Azure Storage standar - untuk menyembunyikannya, ubah **Opsi Kolom** untuk hanya memilih kolom berikut:
    - **document_id** (kolom kunci dibuat secara otomatis oleh proses pengindeksan)
    - **file_id** (URL file yang disandikan)
    - **file_name** (nama file yang diambil dari metadata dokumen)
    - **language** (bahasa yang digunakan untuk menulis dokumen)
    - **sentiment** skor sentimen yang dihitung untuk dokumen tersebut.
    - **url** URL untuk blob dokumen di Azure storage.
3. Lihat tabel lain yang dibuat oleh proses pengindeksan:
    - **ImageTags** (berisi satu baris untuk setiap tag gambar individual dengan **document_id** untuk dokumen tempat tag tersebut muncul).
    - **KeyPhrases** (berisi satu baris untuk setiap frasa kunci individual dengan **document_id** untuk dokumen tempat frasa tersebut muncul).
    - **Locations** (berisi satu baris untuk setiap lokasi individual dengan **document_id** untuk dokumen tempat lokasi muncul).

Kemampuan untuk membuat proyeksi *table* memungkinkan Anda membangun solusi analitis dan pelaporan yang mengkueri skema relasional seperti misalnya menggunakan Microsoft Power BI. Kolom kunci yang dibuat secara otomatis dapat digunakan untuk menggabungkan tabel dalam kueri - misalnya untuk mengembalikan semua lokasi yang disebutkan dalam dokumen tertentu.

## <a name="more-information"></a>Informasi selengkapnya

Untuk mempelajari selengkapnya tentang membuat penyimpanan pengetahuan dengan Azure Cognitive Search, baca [dokumentasi Azure Cognitive Search](https://docs.microsoft.com/azure/search/knowledge-store-concept-intro).
