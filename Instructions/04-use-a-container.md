---
lab:
  title: Menggunakan Kontainer Cognitive Services
  module: Module 2 - Developing AI Apps with Cognitive Services
ms.openlocfilehash: 244ab1ef3754e668d64996dece9711682651691d
ms.sourcegitcommit: 29a684646784fe4f7370343b6c005728a953770d
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 05/04/2022
ms.locfileid: "145195647"
---
# <a name="use-a-cognitive-services-container"></a>Menggunakan Kontainer Cognitive Services

Menggunakan layanan kognitif yang dihosting di Azure memungkinkan pengembang aplikasi untuk fokus pada infrastruktur kode mereka sambil memanfaatkan layanan skalabel yang dikelola oleh Microsoft. Namun, dalam banyak skenario, organisasi memerlukan kontrol lebih besar atas infrastruktur layanan mereka dan data yang diteruskan antar layanan.

Banyak dari layanan kognitif API dapat dikemas dan disebarkan dalam sebuah *kontainer*, memungkinkan organisasi untuk meng-host layanan kognitif dalam infrastruktur mereka sendiri; misalnya di server Docker lokal, Azure Container Instances, atau kluster Azure Kubernetes Service. Layanan kognitif kemas perlu berkomunikasi dengan akun layanan kognitif berbasis Azure untuk mendukung penagihan; tetapi data aplikasi tidak diteruskan ke layanan back-end, dan organisasi memiliki kontrol lebih besar atas konfigurasi penyebaran kontainer mereka, memungkinkan solusi kustom untuk autentikasi, skalabilitas, dan pertimbangan lainnya.

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda sudah mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, buka di Visual Studio Code; jika belum, ikuti langkah-langkah ini untuk mengkloningnya sekarang.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan melakukan debug, pilih **Tidak Sekarang**.

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
5. Saat sumber daya telah diterapkan, buka dan lihat halaman **Kunci dan Titik Akhir**. Anda akan memerlukan titik akhir dan salah satu kunci dari halaman ini dalam prosedur selanjutnya.

## <a name="deploy-and-run-a-text-analytics-container"></a>Terapkan dan jalankan kontainer Analisis Teks

Banyak API layanan kognitif yang umum digunakan tersedia dalam gambar kontainer. Untuk daftar lengkapnya, lihat [dokumentasi layanan kognitif](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-container-support#container-availability-in-azure-cognitive-services). Dalam latihan ini, Anda akan menggunakan gambar penampung untuk API *pendeteksian bahasa* Analisis Teks; tetapi prinsipnya sama untuk semua gambar yang tersedia.

1. Di portal Microsoft Azure, pada halaman **Beranda**, pilih tombol **&#65291;Buat sumber daya**, jelajahi *instans kontainer*, dan buat sumber daya **Instans Kontainer** dengan pengaturan berikut:

    - **Dasar-dasar**:
        - **Langganan**: *Langganan Azure Anda*
        - **Grup sumber daya**: *Pilih grup sumber daya yang berisi sumber daya layanan kognitif Anda*
        - **Nama penampung**: *Masukkan nama yang unik*
        - **Wilayah**: *Pilih wilayah yang tersedia*
        - **Sumber gambar**: Registri Lainnya
        - **Jenis gambar**: Publik
        - **Gambar**: `mcr.microsoft.com/azure-cognitive-services/textanalytics/language:1.1.013570001-amd64`
        - **Jenis OS**: Linux
        - **Size**: 1 vcpu, memori 4 GB
    - **Jaringan**:
        - **Jenis jaringan**: Publik
        - **Label nama DNS**: *Masukkan nama unik untuk titik akhir penampung*
        - **Pelabuhan**: *Ubah port TCP dari 80 menjadi **5000***
    - **Lanjutan**:
        - **Kebijakan mulai ulang**: Pada kegagalan
        - **Variabel lingkungan**:

            | Tandai sebagai aman | Kunci | Nilai |
            | -------------- | --- | ----- |
            | Ya | `ApiKey` | *Salah satu kunci untuk sumber daya layanan kognitif Anda* |
            | Ya | `Billing` | *URI titik akhir untuk sumber daya layanan kognitif Anda* |
            | Tidak | `Eula` | `accept` |

        - **Penggantian perintah**: [ ]
    - **Tag**:
        - *Jangan tambahkan tag apa pun*

2. Tunggu hingga penyebaran selesai, lalu buka sumber daya yang disebarkan.
3. Amati properti berikut dari sumber daya instans penampung Anda di halaman **Ringkasan**:
    - **Status**: Ini seharusnya *Berjalan*.
    - **Alamat IP**: Ini adalah alamat IP publik yang dapat Anda gunakan untuk mengakses instans kontainer Anda.
    - **FQDN**: Ini adalah *nama domain yang sepenuhnya memenuhi syarat* dari sumber daya instans kontainer, Anda dapat menggunakan ini untuk mengakses instans kontainer alih-alih alamat IP.

    > **Catatan**: Dalam latihan ini, Anda telah menerapkan gambar kontainer layanan kognitif untuk terjemahan teks ke sumber daya Azure Container Instances (ACI). Anda dapat menggunakan pendekatan serupa untuk menerapkannya ke host *[Docker](https://www.docker.com/products/docker-desktop)* di komputer atau jaringan Anda sendiri dengan menjalankan perintah berikut (pada satu baris) untuk menerapkan kontainer deteksi bahasa ke instans Docker lokal, menggantikan *&lt;yourEndpoint&gt;* dan *&lt;yourKey&gt;* dengan URI titik akhir Anda dan salah satu kunci untuk sumber daya layanan kognitif Anda.
    >
    > ```
    > docker run --rm -it -p 5000:5000 --memory 4g --cpus 1 mcr.microsoft.com/azure-cognitive-services/textanalytics/language Eula=accept Billing=<yourEndpoint> ApiKey=<yourKey>
    > ```
    >
    > Perintah akan mencari gambar di mesin lokal Anda, dan jika tidak menemukannya di sana, perintah akan menariknya dari registri gambar *mcr.microsoft.com* dan menyebarkannya ke instans Docker Anda. Saat penyebaran selesai, penampung akan mulai dan mendengarkan permintaan masuk pada port 5000.

## <a name="use-the-container"></a>Gunakan kontainer

1. Di Visual Studio Code, di folder **04-containers**, buka **rest-test.cmd** dan edit perintah **curl** yang ada di dalamnya (ditampilkan di bawah), menggantikan *&lt;your_ACI_IP_address_or_FQDN&gt;* dengan alamat IP atau FQDN untuk penampung Anda.

    ```
    curl -X POST "http://<your_ACI_IP_address_or_FQDN>:5000/text/analytics/v3.0/languages?" -H "Content-Type: application/json" --data-ascii "{'documents':[{'id':1,'text':'Hello world.'},{'id':2,'text':'Salut tout le monde.'}]}"
    ```

2. Simpan perubahan Anda ke skrip. Perhatikan bahwa Anda tidak perlu menentukan titik akhir atau kunci layanan kognitif - permintaan diproses oleh layanan kemas. Kontainer pada gilirannya berkomunikasi secara berkala dengan layanan di Azure untuk melaporkan penggunaan untuk penagihan, tetapi tidak mengirim data permintaan.
3. Klik kanan folder **04-containers** dan buka terminal terintegrasi. Kemudian masukkan perintah berikut untuk menjalankan skrip:

    ```
    rest-test
    ```

4. Verifikasi bahwa perintah mengembalikan dokumen JSON yang berisi informasi tentang bahasa yang terdeteksi dalam dua dokumen input (yang harus bahasa Inggris dan Prancis).

## <a name="clean-up"></a>Pembersihan

Jika Anda telah selesai bereksperimen dengan instans kontainer Anda, Anda harus menghapusnya.

1. Di portal Microsoft Azure, buka grup sumber daya tempat Anda membuat sumber daya untuk latihan ini.
2. Pilih sumber daya instans kontainer dan hapus.

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang penampung layanan kognitif, lihat [dokumentasi penampung Cognitive Services](https://docs.microsoft.com/azure/cognitive-services/containers/).
