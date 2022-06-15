---
lab:
  title: Analisis Teks
  module: Module 3 - Getting Started with Natural Language Processing
ms.openlocfilehash: 27cd22e81d4fe8fda27e6fc960d3b3aeb8debded
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 01/12/2022
ms.locfileid: "145195712"
---
# <a name="analyze-text"></a>Analisis Teks

Layanan **Bahasa** adalah layanan kognitif yang mendukung analisis teks, termasuk deteksi bahasa, analisis sentimen, ekstraksi frasa kunci, dan pengenalan entitas.

Misalnya, agen perjalanan ingin memproses ulasan hotel yang telah dikirimkan ke situs web perusahaan. Dengan menggunakan layanan Bahasa, mereka dapat menentukan bahasa yang digunakan untuk menulis setiap ulasan, sentimen (positif, netral, atau negatif) dari ulasan, frasa kunci yang mungkin menunjukkan topik utama yang dibahas dalam ulasan, dan entitas bernama, seperti tempat, landmark, atau orang yang disebutkan dalam ulasan.

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda belum mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, ikuti langkah-langkah berikut untuk melakukannya. Jika tidak, buka folder kloning di Visual Studio Code.

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
5. Saat sumber daya telah diterapkan, buka dan lihat halaman **Kunci dan Titik Akhir**. Anda akan memerlukan titik akhir dan salah satu kunci dari halaman ini dalam prosedur selanjutnya.

## <a name="prepare-to-use-the-language-sdk-for-text-analytics"></a>Bersiaplah untuk menggunakan SDK Bahasa untuk analisis teks

Dalam latihan ini, Anda akan menyelesaikan aplikasi klien yang diimplementasikan sebagian yang menggunakan SDK analisis teks layanan Bahasa untuk menganalisis ulasan hotel.

> **Catatan**: Anda dapat memilih untuk menggunakan SDK baik untuk **C#** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, jelajahi folder **05-analyze-text** dan perluas folder **C-Sharp** atau **Python** tergantung pada preferensi bahasa Anda.
2. Klik kanan folder **text-analysis** dan buka terminal terintegrasi. Kemudian instal paket Text Analytics SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:
    
    **C#**
    
    ```
    dotnet add package Azure.AI.TextAnalytics --version 5.1.0
    ```
    
    **Python**
    
    ```
    pip install azure-ai-textanalytics==5.1.0
    ```
    
3. Lihat konten folder **text-analysis**, dan perhatikan bahwa folder tersebut berisi file untuk pengaturan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk mencerminkan **titik akhir** dan **kunci** autentikasi untuk sumber daya layanan kognitif Anda. Simpan perubahan Anda.

4. Perhatikan bahwa folder **text-analysis** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: text-analysis.py

    Buka file kode dan di bagian atas, di bawah referensi namespace yang ada, temukan komentar **Impor namespace**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk mengimpor ruang nama yang Anda perlukan untuk menggunakan SDK Analisis Teks:

    **C#**
    
    ```C#
    // import namespaces
    using Azure;
    using Azure.AI.TextAnalytics;
    ```

    **Python**

    ```Python
    # import namespaces
    from azure.core.credentials import AzureKeyCredential
    from azure.ai.textanalytics import TextAnalyticsClient
    ```

5. Dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat titik akhir dan kunci layanan kognitif dari file konfigurasi telah disediakan. Kemudian temukan komentar **Buat klien menggunakan titik akhir dan kunci**, dan tambahkan kode berikut untuk membuat klien untuk API Analisis Teks:

    **C#**

    ```C#
    // Create client using endpoint and key
    AzureKeyCredential credentials = new AzureKeyCredential(cogSvcKey);
    Uri endpoint = new Uri(cogSvcEndpoint);
    TextAnalyticsClient CogClient = new TextAnalyticsClient(endpoint, credentials);
    ```

    **Python**

    ```Python
    # Create client using endpoint and key
    credential = AzureKeyCredential(cog_key)
    cog_client = TextAnalyticsClient(endpoint=cog_endpoint, credential=credential)
    ```

6. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **text-analysis**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python text-analysis.py
    ```

6. Amati keluaran saat kode harus berjalan tanpa kesalahan, menampilkan konten setiap file teks ulasan dalam folder **ulasan**. Aplikasi berhasil membuat klien untuk Text Analytics API tetapi tidak menggunakannya. Kami akan memperbaikinya di prosedur selanjutnya.

## <a name="detect-language"></a>Mendeteksi bahasa

Sekarang setelah Anda membuat klien untuk Text Analytics API, mari kita gunakan untuk mendeteksi bahasa yang digunakan untuk menulis setiap ulasan.

1. Dalam fungsi **Utama** untuk program Anda, temukan komentar **Dapatkan bahasa**. Kemudian, di bawah komentar ini, tambahkan kode yang diperlukan untuk mendeteksi bahasa di setiap dokumen ulasan:

    **C#**
    
    ```C
    // Get language
    DetectedLanguage detectedLanguage = CogClient.DetectLanguage(text);
    Console.WriteLine($"\nLanguage: {detectedLanguage.Name}");
    ```

    **Python**
    
    ```Python
    # Get language
    detectedLanguage = cog_client.detect_language(documents=[text])[0]
    print('\nLanguage: {}'.format(detectedLanguage.primary_language.name))
    ```

    > **Catatan**: *Dalam contoh ini, setiap ulasan dianalisis satu per satu, menghasilkan panggilan terpisah ke layanan untuk setiap file. Pendekatan alternatif adalah membuat kumpulan dokumen dan meneruskannya ke layanan dalam satu panggilan. Dalam kedua pendekatan tersebut, tanggapan dari layanan terdiri dari kumpulan dokumen; itulah sebabnya dalam kode Python di atas, indeks dokumen pertama (dan satu-satunya) dalam respons ([0]) ditentukan.*

6. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **text-analysis**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python text-analysis.py
    ```

7. Amati hasilnya, perhatikan bahwa kali ini bahasa untuk setiap ulasan diidentifikasi.

## <a name="evaluate-sentiment"></a>Evaluasi sentimen

*Analisis sentimen* adalah teknik yang umum digunakan untuk mengklasifikasikan teks sebagai *positif* atau *negatif* (atau kemungkinan *netral* atau *campuran* ). Ini biasanya digunakan untuk menganalisis posting media sosial, ulasan produk, dan item lain di mana sentimen teks dapat memberikan wawasan yang berguna.

1. Dalam fungsi **Utama** untuk program Anda, temukan komentar **Dapatkan sentimen**. Kemudian, di bawah komentar ini, tambahkan kode yang diperlukan untuk mendeteksi sentimen dari setiap dokumen ulasan:

    **C#**
    
    ```C
    // Get sentiment
    DocumentSentiment sentimentAnalysis = CogClient.AnalyzeSentiment(text);
    Console.WriteLine($"\nSentiment: {sentimentAnalysis.Sentiment}");
    ```

    **Python**
    
    ```Python
    # Get sentiment
    sentimentAnalysis = cog_client.analyze_sentiment(documents=[text])[0]
    print("\nSentiment: {}".format(sentimentAnalysis.sentiment))
    ```

2. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **text-analysis**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```

    **Python**

    ```
    python text-analysis.py
    ```

3. Amati output, perhatikan bahwa sentimen ulasan terdeteksi.

## <a name="identify-key-phrases"></a>Identifikasi frasa kunci

Akan berguna untuk mengidentifikasi frase kunci dalam tubuh teks untuk membantu menentukan topik utama yang dibahas.

1. Dalam fungsi **Utama** untuk program Anda, temukan komentar **Dapatkan frasa kunci**. Kemudian, di bawah komentar ini, tambahkan kode yang diperlukan untuk mendeteksi frasa kunci di setiap dokumen ulasan:

    **C#**

    ```C
    // Get key phrases
    KeyPhraseCollection phrases = CogClient.ExtractKeyPhrases(text);
    if (phrases.Count > 0)
    {
        Console.WriteLine("\nKey Phrases:");
        foreach(string phrase in phrases)
        {
            Console.WriteLine($"\t{phrase}");
        }
    }
    ```
    
    **Python**
    
    ```Python
    # Get key phrases
    phrases = cog_client.extract_key_phrases(documents=[text])[0].key_phrases
    if len(phrases) > 0:
        print("\nKey Phrases:")
        for phrase in phrases:
            print('\t{}'.format(phrase))
    ```

2. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **text-analysis**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-analysis.py
    ```

3. Amati hasilnya, perhatikan bahwa setiap dokumen berisi frasa kunci yang memberikan beberapa wawasan tentang ulasan tersebut.

## <a name="extract-entities"></a>Mengekstrak entitas

Sering kali, dokumen atau kumpulan teks lainnya menyebutkan orang, tempat, periode waktu, atau entitas lain. API Analytics teks dapat mendeteksi beberapa kategori (dan sub-kategori) entitas dalam teks Anda.

1. Dalam fungsi **Utama** untuk program Anda, temukan komentar **Dapatkan entitas**. Kemudian, di bawah komentar ini, tambahkan kode yang diperlukan untuk mengidentifikasi entitas yang disebutkan di setiap ulasan:

    **C#**
    
    ```C
    // Get entities
    CategorizedEntityCollection entities = CogClient.RecognizeEntities(text);
    if (entities.Count > 0)
    {
        Console.WriteLine("\nEntities:");
        foreach(CategorizedEntity entity in entities)
        {
            Console.WriteLine($"\t{entity.Text} ({entity.Category})");
        }
    }
    ```

    **Python**
    
    ```Python
    # Get entities
    entities = cog_client.recognize_entities(documents=[text])[0].entities
    if len(entities) > 0:
        print("\nEntities")
        for entity in entities:
            print('\t{} ({})'.format(entity.text, entity.category))
    ```

2. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **text-analysis**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-analysis.py
    ```

3. Amati keluarannya, perhatikan entitas yang telah terdeteksi dalam teks.

## <a name="extract-linked-entities"></a>Mengekstrak entitas tertaut

Selain entitas yang dikategorikan, Text Analytics API dapat mendeteksi entitas yang diketahui memiliki tautan ke sumber data, seperti Wikipedia.

1. Dalam fungsi **Utama** untuk program Anda, temukan komentar **Dapatkan entitas tertaut**. Kemudian, di bawah komentar ini, tambahkan kode yang diperlukan untuk mengidentifikasi entitas terkait yang disebutkan di setiap ulasan:

    **C#**
    
    ```C
    // Get linked entities
    LinkedEntityCollection linkedEntities = CogClient.RecognizeLinkedEntities(text);
    if (linkedEntities.Count > 0)
    {
        Console.WriteLine("\nLinks:");
        foreach(LinkedEntity linkedEntity in linkedEntities)
        {
            Console.WriteLine($"\t{linkedEntity.Name} ({linkedEntity.Url})");
        }
    }
    ```

    **Python**
    
    ```Python
    # Get linked entities
    entities = cog_client.recognize_linked_entities(documents=[text])[0].entities
    if len(entities) > 0:
        print("\nLinks")
        for linked_entity in entities:
            print('\t{} ({})'.format(linked_entity.name, linked_entity.url))
    ```

2. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **text-analysis**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-analysis.py
    ```

3. Amati output, catat entitas terkait yang diidentifikasi.

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang menggunakan layanan **Bahasa**, lihat [dokumentasi Analisis Teks](https://docs.microsoft.com/azure/cognitive-services/language-service/).
