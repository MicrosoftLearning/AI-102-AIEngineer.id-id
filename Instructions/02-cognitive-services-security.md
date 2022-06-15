---
lab:
  title: Mengelola Keamanan Cognitive Services
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: 9c8de44265ffa0846b6860fd7d416bb3be547ed9
ms.sourcegitcommit: 5ffc20f6a590fe643c2b695b8dc04589411be36e
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 05/31/2022
ms.locfileid: "145951182"
---
# <a name="manage-cognitive-services-security"></a>Mengelola Keamanan Cognitive Services

Keamanan adalah pertimbangan penting untuk aplikasi apa pun, dan sebagai pengembang Anda harus memastikan bahwa akses ke sumber daya seperti layanan kognitif dibatasi hanya untuk mereka yang membutuhkannya.

Akses ke layanan kognitif biasanya dikontrol melalui kunci autentikasi, yang dihasilkan saat Anda pertama kali membuat sumber daya layanan kognitif.

## <a name="clone-the-repository-for-this-course"></a>Kloning repositori untuk kursus ini

Jika Anda telah mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, buka di Visual Studio Code; jika tidak, ikuti langkah-langkah ini untuk mengkloningnya sekarang.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana pun).
3. Ketika repositori telah dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="provision-a-cognitive-services-resource"></a>Menyediakan sumber daya Cognitive Services

Jika Anda belum memilikinya dalam langganan, Anda harus menyediakan sumber daya **Cognitive Services**.

1. Buka portal Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, cari *Cognitive Services*, dan buat sumber daya **Cognitive Services** dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Wilayah**: *Pilih wilayah yang tersedia*
    - **Nama**: *Masukkan nama unik*
    - **Tingkat harga**: Standar S0
3. Pilih kotak centang yang diperlukan dan buat sumber daya.
4. Tunggu hingga penyebaran selesai, lalu lihat detail penyebaran.

## <a name="manage-authentication-keys"></a>Mengelola kunci autentikasi

Saat Anda membuat sumber daya layanan kognitif, dua kunci autentikasi dibuat. Anda dapat mengelola ini di portal Azure atau dengan menggunakan antarmuka baris perintah Azure (CLI).

1. Di portal Azure, buka sumber daya layanan kognitif Anda dan lihat laman **Kunci dan Titik Akhir**. Halaman ini berisi informasi yang Anda perlukan untuk terhubung ke sumber daya Anda dan menggunakannya dari aplikasi yang Anda kembangkan. Khususnya:
    - *Titik akhir* HTTP tempat aplikasi klien dapat mengirim permintaan.
    - Dua *kunci* yang dapat digunakan untuk autentikasi (aplikasi klien dapat menggunakan salah satu kunci. Praktik yang umum adalah menggunakan satu untuk pengembangan, dan satu lagi untuk produksi. Anda dapat dengan mudah membuat ulang kunci pengembangan setelah pengembang menyelesaikan pekerjaan mereka untuk mencegah akses lanjutan).
    - *Lokasi* tempat sumber daya dihosting. Ini diperlukan untuk permintaan ke beberapa (tetapi tidak semua) API.
2. Di Visual Studio Code, klik kanan folder **02-cognitive-security** dan buka terminal terintegrasi. Kemudian masukkan perintah berikut untuk masuk ke langganan Azure Anda dengan menggunakan Azure CLI.

    ```
    az login
    ```

    Tab browser web akan terbuka dan meminta Anda untuk masuk ke Azure. Lakukan, lalu tutup tab browser dan kembali ke Visual Studio Code.

    > **Tips**: Jika Anda memiliki banyak langganan, Anda harus memastikan bahwa Anda bekerja di langganan yang berisi sumber daya layanan kognitif Anda.  Gunakan perintah ini untuk menentukan langganan Anda saat ini - ID uniknya adalah nilai **id** di JSON yang dikembalikan.

    > **Peringatan**: Jika Anda mendapatkan kegagalan verifikasi sertifikat `az login`, coba tunggu beberapa menit dan coba lagi.
    >
    > ```
    > az account show
    > ```
    >
    > Jika Anda perlu mengubah langganan, jalankan perintah ini, ubah *&lt;Your_Subscription_Id&gt;* ke ID langganan yang benar.
    >
    > ```
    > az account set --subscription <Your_Subscription_Id>
    > ```
    >
    > Atau, Anda dapat secara eksplisit menentukan ID langganan sebagai parameter *--subscription* di setiap perintah Azure CLI setelahnya.

3. Sekarang Anda dapat menggunakan perintah berikut untuk mendapatkan daftar kunci layanan kognitif, mengganti *&lt;resourceName&gt;* dengan nama sumber daya layanan kognitif Anda, dan *&lt;resourceGroup&gt;*  dengan nama grup sumber daya tempat Anda membuatnya.

    ```
    az cognitiveservices account keys list --name <resourceName> --resource-group <resourceGroup>
    ```

Perintah mengembalikan daftar kunci sumber daya layanan kognitif Anda - ada dua kunci, bernama **key1** dan **key2**.

4. Untuk menguji layanan kognitif Anda, Anda dapat menggunakan **curl** - alat baris perintah untuk permintaan HTTP. Di folder **02-cognitive-security**, buka **rest-test.cmd** dan edit perintah **curl** yang ada di dalamnya (ditampilkan di bawah), menggantikan *&lt;yourEndpoint&gt;* dan *&lt;yourKey&gt;* dengan URI titik akhir dan kunci **Key1** Anda untuk menggunakan Text Analytics API di sumber daya layanan kognitif Anda.

    ```
    curl -X POST "<yourEndpoint>/text/analytics/v3.0/languages?" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <yourKey>" --data-ascii "{'documents':[{'id':1,'text':'hello'}]}"
    ```

5. Simpan perubahan Anda, lalu di terminal terintegrasi untuk folder **02-cognitive-security**, jalankan perintah berikut:

    ```
    rest-test
    ```

Perintah mengembalikan dokumen JSON yang berisi informasi tentang bahasa yang terdeteksi dalam data input (yang seharusnya bahasa Inggris).

6. Jika kunci disusupi, atau pengembang yang memilikinya tidak lagi memerlukan akses, Anda dapat membuat ulang di portal atau dengan menggunakan Azure CLI. Jalankan perintah berikut untuk membuat ulang kunci **key1** Anda (menggantikan *&lt;resourceName&gt;* dan *&lt;resourceGroup&gt;* untuk sumber daya Anda).

    ```
    az cognitiveservices account keys regenerate --name <resourceName> --resource-group <resourceGroup> --key-name key1
    ```

Daftar kunci untuk sumber daya layanan kognitif Anda dikembalikan - perhatikan bahwa **key1** telah berubah sejak terakhir kali Anda mengambilnya.

7. Jalankan kembali perintah **rest-test** dengan kunci lama (Anda dapat menggunakan kunci **^** untuk menelusuri perintah sebelumnya), dan pastikan bahwa itu sekarang gagal.
8. Edit perintah *curl* di **rest-test.cmd** dengan mengganti kunci dengan nilai **key1** yang baru, dan simpan perubahannya. Kemudian jalankan kembali perintah **rest-test** dan pastikan bahwa itu berhasil.

> **Tips**: Dalam latihan ini, Anda menggunakan nama lengkap parameter Azure CLI, seperti **--resource-group**.  Anda juga dapat menggunakan alternatif yang lebih pendek, seperti **-g**, untuk membuat perintah Anda tidak terlalu bertele-tele (tetapi sedikit lebih sulit untuk dipahami).  [Referensi perintah CLI Cognitive Services](https://docs.microsoft.com/cli/azure/cognitiveservices?view=azure-cli-latest) mencantumkan opsi parameter untuk setiap perintah CLI layanan kognitif.

## <a name="secure-key-access-with-azure-key-vault"></a>Akses kunci aman dengan Azure Key Vault

Anda dapat mengembangkan aplikasi yang menggunakan layanan kognitif dengan menggunakan kunci untuk autentikasi. Namun, ini berarti bahwa kode aplikasi harus dapat memperoleh kuncinya. Salah satu opsi adalah menyimpan kunci dalam variabel lingkungan atau file konfigurasi tempat aplikasi disebarkan, tetapi pendekatan ini membuat kunci rentan terhadap akses yang tidak sah. Pendekatan yang lebih baik saat mengembangkan aplikasi di Azure adalah dengan menyimpan kunci dengan aman di Azure Key Vault, dan memberikan akses ke kunci tersebut melalui *identitas terkelola* (dengan kata lain, akun pengguna yang digunakan oleh aplikasi itu sendiri).

### <a name="create-a-key-vault-and-add-a-secret"></a>Buat brankas kunci dan tambahkan rahasia

Pertama, Anda perlu membuat brankas kunci dan menambahkan *rahasia* untuk kunci layanan kognitif.

1. Buat catatan nilai **key1** untuk sumber daya layanan kognitif Anda (atau salin ke papan klip).
2. Di portal Microsoft Azure, pada halaman **Beranda**, pilih tombol **&#65291;Buat sumber daya**, cari *Key Vault*, dan buat sumber daya **Key Vault** dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Grup sumber daya yang sama dengan sumber daya layanan kognitif Anda*
    - **Nama brankas kunci**: *Masukkan nama yang unik*
    - **Region**: *Wilayah yang sama dengan sumber daya layanan kognitif Anda*
    - **Tingkat harga**: Standard
3. Tunggu hingga penerapan selesai, lalu buka sumber daya brankas kunci Anda.
4. Di panel navigasi kiri, pilih **Rahasia** (di bagian Pengaturan).
5. Pilih **+ Hasilkan/Impor** dan tambahkan rahasia baru dengan pengaturan berikut:
    - **Opsi unggah**: Manual
    - **Nama**: Cognitive-Services-Key *(penting untuk mencocokkan ini dengan tepat, karena nanti Anda akan menjalankan kode yang mengambil rahasia berdasarkan nama ini)*
    - **Nilai**: *Kunci layanan kognitif **key1** Anda*

### <a name="create-a-service-principal"></a>Membuat perwakilan layanan

Untuk mengakses rahasia di brankas kunci, aplikasi Anda harus menggunakan prinsip layanan yang memiliki akses ke rahasia tersebut. Anda akan menggunakan antarmuka baris perintah (CLI) Azure untuk membuat prinsip layanan, menemukan ID objeknya, dan memberikan akses ke rahasia di Azure Vault.

1. Kembali ke Visual Studio Code, dan di terminal terintegrasi untuk folder **02-cognitive-security**, jalankan perintah Azure CLI berikut, ganti *&lt;spName&gt;* dengan yang sesuai nama untuk identitas aplikasi (misalnya, *ai-app*). Ganti juga *&lt;subscriptionId&gt;* dan *&lt;resourceGroup&gt;* dengan nilai yang benar untuk ID langganan Anda dan grup sumber daya yang berisi layanan kognitif dan sumber daya brankas kunci:

    > **Tips**: Jika Anda tidak yakin dengan ID langganan Anda, gunakan perintah **az account show** untuk mengambil informasi langganan Anda - ID langganan adalah atribut **id** di output.

    ```
    az ad sp create-for-rbac -n "api://<spName>" --role owner --scopes subscriptions/<subscriptionId>/resourceGroups/<resourceGroup>
    ```

Output dari perintah ini mencakup informasi tentang prinsip layanan baru Anda. Ini akan terlihat mirip dengan ini:

    ```
    {
        "appId": "abcd12345efghi67890jklmn",
        "displayName": "ai-app",
        "name": "http://ai-app",
        "password": "1a2b3c4d5e6f7g8h9i0j",
        "tenant": "1234abcd5678fghi90jklm"
    }
    ```

Catat nilai **appId**, **kata sandi**, dan **penyewa** - Anda akan membutuhkannya nanti (jika Anda menutup terminal ini, Anda tidak akan dapat mengambil kata sandinya; jadi penting untuk mencatat nilainya sekarang - Anda dapat menempelkan output ke file teks baru di Visual Studio Code untuk memastikan Anda dapat menemukan nilai yang Anda butuhkan nanti!)

2. Untuk mendapatkan **ID objek** perwakilan layanan Anda, jalankan perintah Azure CLI berikut, ganti *&lt;appId&gt;* dengan nilai ID aplikasi prinsipal layanan Anda.

    ```
    az ad sp show --id <appId> --query objectId --out tsv
    ```

3. Untuk menetapkan izin bagi perwakilan layanan baru Anda untuk mengakses rahasia di Key Vault Anda, jalankan perintah Azure CLI berikut, ganti *&lt;keyVaultName&gt;* dengan nama sumber daya Azure Key Vault Anda dan  *&lt;objectId&gt;* dengan nilai ID objek perwakilan layanan Anda.

    ```
    az keyvault set-policy -n <keyVaultName> --object-id <objectId> --secret-permissions get list
    ```

### <a name="use-the-service-principal-in-an-application"></a>Menggunakan prinsip layanan dalam aplikasi

Sekarang Anda siap untuk menggunakan identitas utama layanan dalam aplikasi, sehingga dapat mengakses kunci layanan kognitif rahasia di brankas kunci Anda dan menggunakannya untuk terhubung ke sumber daya layanan kognitif Anda.

> **Catatan**: Dalam latihan ini, kami akan menyimpan kredensial utama layanan dalam konfigurasi aplikasi dan menggunakannya untuk mengautentikasi identitas **ClientSecretCredential** dalam kode aplikasi Anda. Latihan ini bagus untuk pengembangan dan pengujian, tetapi dalam aplikasi produksi nyata, administrator akan menetapkan *identitas terkelola* ke aplikasi sehingga aplikasi tersebut menggunakan identitas utama layanan untuk mengakses sumber daya, tanpa menembolok atau menyimpan sandi.

1. Di Visual Studio Code, luaskan folder **02-cognitive-security** dan folder **C-Sharp** atau **Python** bergantung pada preferensi bahasa Anda.
2. Klik kanan folder **keyvault-client** dan buka terminal terintegrasi. Kemudian instal paket yang Anda perlukan untuk menggunakan Azure Key Vault dan Text Analytics API di sumber daya layanan kognitif Anda dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**

    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.1.0
    dotnet add package Azure.Identity --version 1.5.0
    dotnet add package Azure.Security.KeyVault.Secrets --version 4.2.0-beta.3
    ```

    **Python**

    ```
    pip install azure-ai-textanalytics==5.1.0
    pip install azure-identity==1.5.0
    pip install azure-keyvault-secrets==4.2.0
    ```

3. Lihat konten folder **keyvault-client**, dan perhatikan bahwa folder tersebut berisi file untuk pengaturan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk mencerminkan pengaturan berikut:
    
    - **Titik akhir** untuk sumber daya Cognitive Services Anda
    - Nama sumber daya **Azure Key Vault** Anda
    - **Penyewa** untuk perwakilan layanan Anda
    - **appId** untuk perwakilan layanan Anda
    - **Kata sandi** untuk perwakilan layanan Anda

     Simpan perubahan Anda.
4. Perhatikan bahwa folder **keyvault-client** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: keyvault-client.py

    Buka file kode dan tinjau kode yang ada di dalamnya, perhatikan detail berikut:
    - Namespace untuk SDK yang Anda instal telah diimpor
    - Kode dalam fungsi **Utama** mengambil pengaturan konfigurasi aplikasi, lalu menggunakan kredensial utama layanan untuk mendapatkan kunci layanan kognitif dari brankas kunci.
    - Fungsi **GetLanguage** menggunakan SDK untuk membuat klien untuk layanan, dan kemudian menggunakan klien untuk mendeteksi bahasa teks yang dimasukkan.
5. Kembali ke terminal terintegrasi untuk folder **keyvault-client**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python keyvault-client.py
    ```

6. Saat diminta, masukkan beberapa teks dan tinjau bahasa yang terdeteksi oleh layanan. Misalnya, coba masukkan "Halo", "Bonjour", dan "Gracias".
7. Setelah Anda selesai menguji aplikasi, masukkan "berhenti" untuk menghentikan program.

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang mengamankan layanan kognitif, lihat [dokumentasi keamanan Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-security).
