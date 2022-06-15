---
lab:
  title: Membuat aplikasi klien LUIS Percakapan (Pratinjau)
ms.openlocfilehash: 486a6716a189156739e9107824e561f7bb8fa95c
ms.sourcegitcommit: 2c92ea1491cac72a4765755cf2bc6d8b5f657a46
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 04/09/2022
ms.locfileid: "145195718"
---
# <a name="create-a-language-service-client-application"></a>membuat Aplikasi Klien Layanan Bahasa

Fitur LUIS Percakapan dari Azure Cognitive Service untuk Bahasa memungkinkan Anda menentukan model bahasa percakapan yang dapat digunakan aplikasi klien untuk menafsirkan masukan bahasa alami dari pengguna, memprediksi *niat* pengguna (apa yang ingin mereka capai), dan mengidentifikasi *entitas* yang harus diterapkan maksud tersebut. Anda dapat membuat aplikasi klien yang menggunakan model pemahaman bahasa percakapan secara langsung melalui antarmuka REST, atau dengan menggunakan kit pengembangan perangkat lunak (SDK) khusus bahasa.

## <a name="clone-the-repository-for-this-course"></a>Kloning repositori untuk kursus ini

Jika Anda telah mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, buka di Visual Studio Code; jika tidak, ikuti langkah-langkah ini untuk mengkloningnya sekarang.

1. Mulai Visual Studio Code.

2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana pun).

3. Ketika repositori telah dikloning, buka folder di Visual Studio Code.

4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="create-language-service-resources"></a>Membuat sumber daya layanan Bahasa

Jika Anda sudah memiliki sumber daya layanan Bahasa di langganan Azure, Anda dapat menggunakannya dalam latihan ini. Jika tidak, ikuti petunjuk ini untuk membuatnya.

1. Buka portal Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.

2. Pilih tombol **&#65291;Buat sumber daya**, cari *layanan bahasa*, dan buat sumber daya **Layanan bahasa** dengan pengaturan berikut:

    - **Fitur Default**: Semua
    - **Fitur kustom**: tidak ada
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Wilayah**: *westus2*
    - **Nama**: *Masukkan nama yang unik*
    - **Tingkat harga**: *S*
    - **Istilah hukum**: *pilih kotak centang untuk mengonfirmasi*
    - **Pemberitahuan AI yang Bertanggung Jawab**: *pilih kotak centang untuk mengonfirmasi*

3. Tunggu hingga sumber daya dibuat. Anda dapat melihat sumber daya Anda dengan menavigasi ke grup sumber daya tempat Anda membuatnya.

## <a name="import-train-and-publish-a-conversational-language-understanding-model"></a>Impor, latih, dan publikasikan model pemahaman bahasa percakapan

Jika Anda sudah memiliki proyek **Jam** dari lab atau latihan sebelumnya, Anda dapat menggunakannya dalam latihan ini. Jika tidak, ikuti petunjuk ini untuk membuatnya.

1. Di tab browser baru, buka Studio Bahasa - portal Pratinjau di `https://language.cognitive.azure.com`.

2. Masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda. Jika ini adalah pertama kalinya Anda masuk ke portal Layanan Bahasa, Anda mungkin perlu memberikan beberapa izin kepada aplikasi untuk mengakses detail akun Anda. Kemudian selesaikan langkah-langkah *Selamat Datang* dengan memilih langganan Azure dan sumber daya penulisan yang baru saja Anda buat.

3. Buka halaman **LUIS Percakapan**.

3. Di samping **&#65291;Buat proyek baru**, lihat daftar dropdown dan pilih **Impor**. Klik **Pilih File** lalu telusur ke subfolder **10b-clu-client-(preview)** dalam folder proyek yang berisi file lab untuk latihan ini. Pilih **Clock.json**, klik **Buka**, lalu klik **Selesai**.

4. Jika panel dengan tips untuk membuat aplikasi layanan Bahasa yang efektif ditampilkan, tutup panel ini.

5. Di sebelah kiri portal Language Studio, pilih **Melatih model** untuk melatih aplikasi. Klik **Mulai tugas pelatihan**, beri nama model **Jam** dan pastikan evaluasi dengan pelatihan diaktifkan. Pelatihan mungkin memerlukan waktu beberapa menit untuk diselesaikan.

    > **Catatan**: Karena nama model **Jam** dikodekan dalam kode klien jam (digunakan nanti di lab), gunakan huruf besar dan ejaan nama persis seperti yang dijelaskan. 

6. Di sebelah kiri portal Language Studio, pilih **Terapkan model** dan gunakan **Tambahkan penerapan** untuk membuat penerapan untuk model Jam yang diberi nama **produksi**.

    > **Catatan**: Karena nama penerapan **produksi** dikodekan secara keras dalam kode klien jam (digunakan nanti di lab), gunakan huruf besar dan ejaan nama persis seperti yang dijelaskan. 

7. Setelah penerapan selesai, pilih penerapan **produksi**, lalu klik **Dapatkan URL prediksi**. Aplikasi klien memerlukan URL titik akhir untuk menggunakan model yang Anda terapkan.

8. Di sebelah kiri portal Language Studio, pilih **Setelan Proyek** dan catat **Kunci utama**. Aplikasi klien memerlukan kunci utama untuk menggunakan model yang Anda terapkan.

9. Aplikasi klien memerlukan informasi dari URL prediksi dan kunci layanan Bahasa untuk terhubung ke model yang Anda terapkan dan diautentikasi.

## <a name="prepare-to-use-the-language-service-sdk"></a>Bersiaplah untuk menggunakan SDK layanan Bahasa

Dalam latihan ini, Anda akan menyelesaikan aplikasi klien yang diimplementasikan sebagian yang menggunakan model Jam (model LUIS Percakapan yang diterbitkan) untuk memprediksi niat dari input pengguna dan merespons dengan tepat.

> **Catatan**: Anda dapat memilih untuk menggunakan SDK baik untuk **.NET** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, telusuri ke folder **10b-clu-client-(preview)** dan luaskan **C-Sharp** atau **Python** folder tergantung pada preferensi bahasa Anda.

2. Klik kanan folder **clock-client**, lalu pilih **Buka di Terminal Terpadu**. Kemudian pasang paket SDK Layanan Bahasa Percakapan dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**

    ```
    dotnet add package Azure.AI.Language.Conversations --version 1.0.0-beta.2
    ```

    **Python**

    ```
    pip install azure-ai-language-conversations --pre
    python -m pip install python-dotenv
    python -m pip install python-dateutil
    ```

3. Lihat konten folder **clock-client**, dan perhatikan bahwa folder tersebut berisi file untuk pengaturan konfigurasi:

    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang ada di dalamnya untuk menyertakan **URL Titik Akhir** dan **Kunci utama** untuk sumber daya Bahasa Anda. Anda dapat menemukan nilai yang diperlukan di portal Microsoft Azure atau Studio Bahasa sebagai berikut:

    - Portal Microsoft Azure: Buka sumber daya Bahasa Anda. Di bawah **Pengelolaan Sumber Daya**, pilih **Kunci dan Titik Akhir**. Salin nilai **KEY 1** dan **Titik akhir** ke file setelan konfigurasi Anda.
    - Studio Bahasa: Buka proyek **Jam** Anda. Titik akhir layanan Bahasa dapat ditemukan di halaman **Sebarkan model** di bawah **Dapatkan URL prediksi**, dan **Kunci primer** dapat ditemukan di halaman **pengaturan Proyek**. Bagian titik akhir layanan Bahasa dari URL Prediksi diakhiri dengan **.cognitiveservices.azure.com/** . Misalnya: `https://ai102-langserv.cognitiveservices.azure.com/`.

4. Perhatikan bahwa folder **clock-client** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: clock-client.py

    Buka file kode, dan di bagian atas, di bawah referensi namespace yang ada, temukan komentar **Impor namespace**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk mengimpor namespace yang Anda perlukan untuk menggunakan SDK layanan Bahasa:

    **C#**

    ```C#
    // Import namespaces
    using Azure;
    using Azure.AI.Language.Conversations;
    ```

    **Python**

    ```Python
    # Import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.language.conversations import ConversationAnalysisClient
    from azure.ai.language.conversations.models import ConversationAnalysisOptions
    ```

## <a name="get-a-prediction-from-the-conversational-language-model"></a>Dapatkan prediksi dari model Bahasa Percakapan

Sekarang Anda siap untuk menerapkan kode yang menggunakan SDK untuk mendapatkan prediksi dari model Bahasa Percakapan Anda.

1. Dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat kunci dan wilayah layanan kognitif dari file konfigurasi telah disediakan. Kemudian temukan komentar **Buat klien untuk model layanan Bahasa** dan tambahkan kode berikut untuk membuat klien prediksi untuk aplikasi Layanan Bahasa Anda:

    **C#**

    ```C#
    // Create a client for the Language service model
    Uri lsEndpoint = new Uri(predictionEndpoint);
    AzureKeyCredential lsCredential = new AzureKeyCredential(predictionKey);

    ConversationAnalysisClient lsClient = new ConversationAnalysisClient(lsEndpoint, lsCredential);
    ```

    **Python**

    ```Python
    # Create a client for the Language service model
    client = ConversationAnalysisClient(
        ls_prediction_endpoint, AzureKeyCredential(ls_prediction_key))
    ```

2. Perhatikan bahwa kode dalam fungsi **Utama** meminta masukan pengguna hingga pengguna memasukkan "keluar". Dalam perulangan ini, temukan komentar **Panggil model layanan Bahasa untuk mendapatkan niat dan entitas** dan tambahkan kode berikut:

    **C#**

    ```C#
    // Call the Language service model to get intent and entities
    var lsProject = "Clock";
    var lsSlot = "production";

    ConversationsProject conversationsProject = new ConversationsProject(lsProject, lsSlot);
    Response<AnalyzeConversationResult> response = await lsClient.AnalyzeConversationAsync(userText, conversationsProject);

    ConversationPrediction conversationPrediction = response.Value.Prediction as ConversationPrediction;

    Console.WriteLine(JsonConvert.SerializeObject(conversationPrediction, Formatting.Indented));
    Console.WriteLine("--------------------\n");
    Console.WriteLine(userText);
    var topIntent = "";
    if (conversationPrediction.Intents[0].Confidence > 0.5)
    {
        topIntent = conversationPrediction.TopIntent;
    }

    var entities = conversationPrediction.Entities;
    ```

    **Python**

    ```Python
    # Call the Language service model to get intent and entities
    convInput = ConversationAnalysisOptions(
        query = userText
        )
    
    cls_project = 'Clock'
    deployment_slot = 'production'

    with client:
        result = client.analyze_conversations(
            convInput, 
            project_name=cls_project, 
            deployment_name=deployment_slot
            )

    # list the prediction results
    top_intent = result.prediction.top_intent
    entities = result.prediction.entities

    print("view top intent:")
    print("\ttop intent: {}".format(result.prediction.top_intent))
    print("\tcategory: {}".format(result.prediction.intents[0].category))
    print("\tconfidence score: {}\n".format(result.prediction.intents[0].confidence_score))

    print("view entities:")
    for entity in entities:
        print("\tcategory: {}".format(entity.category))
        print("\ttext: {}".format(entity.text))
        print("\tconfidence score: {}".format(entity.confidence_score))

    print("query: {}".format(result.query))
    ```

    Panggilan ke model layanan Bahasa mengembalikan prediksi/hasil, yang mencakup niat teratas (kemungkinan besar) serta entitas apa pun yang terdeteksi dalam ucapan input. Aplikasi klien Anda sekarang harus menggunakan prediksi tersebut untuk menentukan dan melakukan tindakan yang sesuai.

3. Temukan komentar **Terapkan tindakan yang sesuai**, dan tambahkan kode berikut, yang memeriksa niat yang didukung oleh aplikasi (**GetTime**, **GetDate**, dan **GetDay**) dan tentukan apakah entitas yang relevan telah terdeteksi, sebelum memanggil fungsi yang ada untuk menghasilkan respons yang sesuai.

    **C#**

    ```C#
    // Apply the appropriate action
    switch (topIntent)
    {
        case "GetTime":
            var location = "local";
            // Check for entities
            if (entities.Count > 0)
            {
                // Check for a location entity
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Location")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        location = entity.Text;
                    }
                    
                }

            }

            // Get the time for the specified location
            var getTimeTask = Task.Run(() => GetTime(location));
            string timeResponse = await getTimeTask;
            Console.WriteLine(timeResponse);
            break;

        case "GetDay":
            var date = DateTime.Today.ToShortDateString();
            // Check for entities
            if (entities.Count > 0)
            {
                // Check for a Date entity
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Date")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        date = entity.Text;
                    }
                }
            }
            // Get the day for the specified date
            var getDayTask = Task.Run(() => GetDay(date));
            string dayResponse = await getDayTask;
            Console.WriteLine(dayResponse);
            break;

        case "GetDate":
            var day = DateTime.Today.DayOfWeek.ToString();
            // Check for entities
            if (entities.Count > 0)
            {
                // Check for a Weekday entity
                foreach (ConversationEntity entity in conversationPrediction.Entities)
                {
                    if (entity.Category == "Weekday")
                    {
                        //Console.WriteLine($"Location Confidence: {entity.Confidence}");
                        day = entity.Text;
                    }
                }
                
            }
            // Get the date for the specified day
            var getDateTask = Task.Run(() => GetDate(day));
            string dateResponse = await getDateTask;
            Console.WriteLine(dateResponse);
            break;

        default:
            // Some other intent (for example, "None") was predicted
            Console.WriteLine("Try asking me for the time, the day, or the date.");
            break;
    }
    ```

    **Python**

    ```Python
    # Apply the appropriate action
    if top_intent == 'GetTime':
        location = 'local'
        # Check for entities
        if len(entities) > 0:
            # Check for a location entity
            for entity in entities:
                if 'Location' == entity.category:
                    # ML entities are strings, get the first one
                    location = entity.text
        # Get the time for the specified location
        print(GetTime(location))

    elif top_intent == 'GetDay':
        date_string = date.today().strftime("%m/%d/%Y")
        # Check for entities
        if len(entities) > 0:
            # Check for a Date entity
            for entity in entities:
                if 'Date' == entity.category:
                    # Regex entities are strings, get the first one
                    date_string = entity.text
        # Get the day for the specified date
        print(GetDay(date_string))

    elif top_intent == 'GetDate':
        day = 'today'
        # Check for entities
        if len(entities) > 0:
            # Check for a Weekday entity
            for entity in entities:
                if 'Weekday' == entity.category:
                # List entities are lists
                    day = entity.text
        # Get the date for the specified day
        print(GetDate(day))

    else:
        # Some other intent (for example, "None") was predicted
        print('Try asking me for the time, the day, or the date.')
    ```

4. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **clock-client**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python clock-client.py
    ```

5. Saat diminta, masukkan ucapan untuk menguji aplikasi. Misalnya, coba:

    *Hello*
    
    *Jam berapa sekarang?*

    *Jam berapa di London?*

    *Tanggal berapa?*

    *Tanggal berapa hari Minggu?*

    *Sekarang hari apa?*

    *Hari apa 01/01/2025?*

> **Catatan**: Logika dalam aplikasi ini sengaja dibuat sederhana, dan memiliki sejumlah keterbatasan. Misalnya, saat mendapatkan waktu, hanya kumpulan kota terbatas yang didukung dan waktu musim panas diabaikan. Tujuannya adalah untuk melihat contoh pola tipikal untuk menggunakan Layanan Bahasa di mana aplikasi Anda harus:
>
>   1. Menyambungkan ke titik akhir prediksi.
>   2. Mengirim ucapan untuk mendapatkan prediksi.
>   3. Menerapkan logika untuk merespons dengan tepat niat dan entitas yang diprediksi.

6. Setelah Anda selesai menguji, masukkan *keluar*.

## <a name="more-information"></a>Informasi selengkapnya

Untuk mempelajari lebih lanjut tentang membuat klien Layanan Bahasa, lihat [dokumentasi pengembang](https://docs.microsoft.com/azure/cognitive-services/luis/developer-reference-resource)
