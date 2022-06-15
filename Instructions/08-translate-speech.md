---
lab:
  title: Menerjemahkan Azure Cognitive Service untuk Ucapan
  module: Module 4 - Building Speech-Enabled Applications
ms.openlocfilehash: 45c8d0d31bee5901247b22917d8dbad8c587a0bd
ms.sourcegitcommit: acbffd6019fe2f1a6ea70870cf7411025c156ef8
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 01/12/2022
ms.locfileid: "145195692"
---
# <a name="translate-speech"></a>Menerjemahkan Azure Cognitive Service untuk Ucapan

Layanan **Speech** menyertakan API **Speech translation** yang dapat Anda gunakan untuk menerjemahkan bahasa lisan. Misalnya, Anda ingin mengembangkan aplikasi penerjemah yang dapat digunakan orang saat bepergian di tempat-tempat di mana mereka tidak berbicara bahasa lokal. Mereka akan dapat mengucapkan frasa seperti "Di mana stasiunnya?" atau "Saya perlu mencari apotek" dalam bahasa mereka sendiri, dan memintanya menerjemahkannya ke bahasa lokal.

**Catatan**: Latihan ini mengharuskan Anda menggunakan komputer dengan speaker/headphone. Untuk pengalaman terbaik, mikrofon juga diperlukan. Beberapa lingkungan virtual yang dihosting mungkin dapat menangkap audio dari mikrofon lokal Anda, tetapi jika ini tidak berhasil (atau Anda tidak memiliki mikrofon sama sekali), Anda dapat menggunakan file audio yang disediakan untuk input ucapan. Ikuti petunjuknya dengan cermat, karena Anda harus memilih opsi yang berbeda tergantung pada apakah Anda menggunakan mikrofon atau file audio.

## <a name="clone-the-repository-for-this-course"></a>Kloning repositori untuk kursus ini

Jika Anda telah mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, buka di Visual Studio Code; jika tidak, ikuti langkah-langkah ini untuk mengkloningnya sekarang.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana pun).
3. Ketika repositori telah dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="provision-a-cognitive-services-resource"></a>Menyediakan sumber daya Cognitive Services

Jika Anda belum memiliki langganan, Anda harus menyediakan sumber daya **Cognitive Services**.

1. Buka portal Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, cari *Cognitive Services*, dan buat sumber daya **Cognitive Services** dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Wilayah**: *Pilih wilayah yang tersedia*
    - **Nama**: *Masukkan nama yang unik*
    - **Tingkat harga**: Standar S0
3. Pilih kotak centang yang diperlukan dan buat sumber daya.
4. Tunggu hingga penyebaran selesai, lalu lihat detail penyebaran.
5. Saat sumber daya telah diterapkan, buka dan lihat halaman **Kunci dan Titik Akhir**. Anda akan memerlukan salah satu kunci dan lokasi di mana layanan disediakan dari halaman ini pada prosedur selanjutnya.

## <a name="prepare-to-use-the-speech-translation-service"></a>Bersiaplah untuk menggunakan layanan Penerjemahan Ucapan

Dalam latihan ini, Anda akan menyelesaikan aplikasi klien yang diimplementasikan sebagian yang menggunakan Azure Cognitive Service untuk Ucapan SDK untuk mengenali, menerjemahkan, dan mensintesis ucapan.

> **Catatan**: Anda dapat memilih untuk menggunakan SDK baik untuk **C#** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, jelajahi folder **08-speech-translation** dan luaskan **C-Sharp** atau **Python** folder tergantung pada preferensi bahasa Anda.
2. Klik kanan folder **translator** dan buka terminal terintegrasi. Kemudian pasang paket Azure Cognitive Service untuk Ucapan SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.19.0
    ```
    
    **Python**
    
    ```
    pip install azure-cognitiveservices-speech==1.19.0
    ```

3. Lihat konten folder **translator**, dan perhatikan bahwa folder tersebut berisi file untuk setelan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk menyertakan **kunci** autentikasi untuk sumber daya layanan kognitif Anda, dan **lokasi** tempat penerapannya. Simpan perubahan Anda.
4. Perhatikan bahwa folder **translator** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: translator.py

    Buka file kode dan di bagian atas, di bawah referensi namespace yang ada, temukan komentar **Impor namespaces**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk mengimpor namespace yang Anda perlukan untuk menggunakan Azure Cognitive Service untuk Ucapan SDK:

    **C#**
    
    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Translation;
    ```
    
    **Python**
    
    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. Dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat kunci dan wilayah layanan kognitif dari file konfigurasi telah disediakan. Anda harus menggunakan variabel ini untuk membuat **SpeechTranslationConfig** untuk sumber daya layanan kognitif Anda, yang akan Anda gunakan untuk menerjemahkan masukan lisan. Tambahkan kode berikut di bawah komentar **Konfigurasikan terjemahan**:

    **C#**
    
    ```C#
    // Configure translation
    translationConfig = SpeechTranslationConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    translationConfig.SpeechRecognitionLanguage = "en-US";
    translationConfig.AddTargetLanguage("fr");
    translationConfig.AddTargetLanguage("es");
    translationConfig.AddTargetLanguage("hi");
    Console.WriteLine("Ready to translate from " + translationConfig.SpeechRecognitionLanguage);
    ```
    
    **Python**
    
    ```Python
    # Configure translation
    translation_config = speech_sdk.translation.SpeechTranslationConfig(cog_key, cog_region)
    translation_config.speech_recognition_language = 'en-US'
    translation_config.add_target_language('fr')
    translation_config.add_target_language('es')
    translation_config.add_target_language('hi')
    print('Ready to translate from',translation_config.speech_recognition_language)
    ```

6. Anda akan menggunakan **SpeechTranslationConfig** untuk menerjemahkan ucapan menjadi teks, namun Anda juga akan menggunakan **SpeechConfig** untuk mensintesis terjemahan menjadi ucapan. Tambahkan kode berikut di bawah komentar **Konfigurasikan ucapan**:

    **C#**
    
    ```C#
    // Configure speech
    speechConfig = SpeechConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    ```
    
    **Python**
    
    ```Python
    # Configure speech
    speech_config = speech_sdk.SpeechConfig(cog_key, cog_region)
    ```

7. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **penerjemah**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

8. Jika Anda menggunakan C#, Anda dapat mengabaikan peringatan apa pun tentang menggunakan operator **tunggu** dalam metode asinkron - kami akan memperbaikinya nanti. Kode harus menampilkan pesan bahwa kode siap diterjemahkan dari en-US. Tekan ENTER untuk mengakhiri program.

## <a name="implement-speech-translation"></a>Menerapkan terjemahan ucapan

Sekarang setelah Anda memiliki **SpeechTranslationConfig** untuk layanan ucapan di sumber daya layanan kognitif, Anda dapat menggunakan API **Terjemahan ucapan** untuk mengenali dan menerjemahkan ucapan.

### <a name="if-you-have-a-working-microphone"></a>Jika Anda memiliki mikrofon yang berfungsi

1. Dalam fungsi **Utama** untuk program Anda, perhatikan bahwa kode menggunakan fungsi **Terjemahkan** untuk menerjemahkan masukan lisan.
2. Dalam fungsi **Terjemahkan**, di bawah komentar **Terjemahkan ucapan**, tambahkan kode berikut untuk membuat klien **TranslationRecognizer** yang dapat digunakan untuk mengenali dan menerjemahkan ucapan menggunakan default mikrofon sistem untuk input.

    **C#**
    
    ```C#
    // Translate speech
    using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using TranslationRecognizer translator = new TranslationRecognizer(translationConfig, audioConfig);
    Console.WriteLine("Speak now...");
    TranslationRecognitionResult result = await translator.RecognizeOnceAsync();
    Console.WriteLine($"Translating '{result.Text}'");
    translation = result.Translations[targetLanguage];
    Console.OutputEncoding = Encoding.UTF8;
    Console.WriteLine(translation);
    ```
    
    **Python**
    
    ```Python
    # Translate speech
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config)
    print("Speak now...")
    result = translator.recognize_once_async().get()
    print('Translating "{}"'.format(result.text))
    translation = result.translations[targetLanguage]
    print(translation)
    ```

    > **Catatan**: Kode di aplikasi Anda menerjemahkan input ke ketiga bahasa dalam satu panggilan. Hanya terjemahan untuk bahasa tertentu yang ditampilkan, tetapi Anda dapat mengambil terjemahan mana pun dengan menetapkan kode bahasa target di kumpulan hasil **terjemahan**.

3. Sekarang lanjutkan ke bagian **Jalankan program** di bawah.

### <a name="alternatively-use-audio-input-from-a-file"></a>Atau, gunakan input audio dari file

1. Di jendela terminal, masukkan perintah berikut untuk memasang pustaka yang dapat Anda gunakan untuk memutar file audio:

    **C#**

    ```
    dotnet add package System.Windows.Extensions --version 4.6.0 
    ```

    **Python**

    ```
    pip install playsound==1.2.2
    ```

2. Dalam file kode untuk program Anda, di bawah impor namespace yang ada, tambahkan kode berikut untuk mengimpor pustaka yang baru saja Anda pasang:

    **C#**

    ```C#
    using System.Media;
    ```

    **Python**

    ```Python
    from playsound import playsound
    ```

3. Dalam fungsi **Utama** untuk program Anda, perhatikan bahwa kode menggunakan fungsi **Terjemahkan** untuk menerjemahkan masukan lisan. Kemudian dalam fungsi **Terjemahkan**, di bawah komentar **Terjemahkan ucapan**, tambahkan kode berikut untuk membuat klien **TranslationRecognizer** yang dapat digunakan untuk mengenali dan menerjemahkan ucapan dari mengajukan.

    **C#**
    
    ```C#
    // Translate speech
    string audioFile = "station.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    using TranslationRecognizer translator = new TranslationRecognizer(translationConfig, audioConfig);
    Console.WriteLine("Getting speech from file...");
    TranslationRecognitionResult result = await translator.RecognizeOnceAsync();
    Console.WriteLine($"Translating '{result.Text}'");
    translation = result.Translations[targetLanguage];
    Console.OutputEncoding = Encoding.UTF8;
    Console.WriteLine(translation);
    ```
    
    **Python**
    
    ```Python
    # Translate speech
    audioFile = 'station.wav'
    playsound(audioFile)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    translator = speech_sdk.translation.TranslationRecognizer(translation_config, audio_config)
    print("Getting speech from file...")
    result = translator.recognize_once_async().get()
    print('Translating "{}"'.format(result.text))
    translation = result.translations[targetLanguage]
    print(translation)
    ```

    > **Catatan**: Kode di aplikasi Anda menerjemahkan input ke ketiga bahasa dalam satu panggilan. Hanya terjemahan untuk bahasa tertentu yang ditampilkan, tetapi Anda dapat mengambil terjemahan mana pun dengan menetapkan kode bahasa target di kumpulan hasil **terjemahan**.

### <a name="run-the-program"></a>Menjalankan program

1. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **penerjemah**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

2. Saat diminta, masukkan kode bahasa yang valid (*fr*, *es*, atau *hi*), lalu, jika menggunakan mikrofon, ucapkan dengan jelas dan ucapkan "di mana Stasiun?" atau frasa lain yang mungkin Anda gunakan saat bepergian ke luar negeri. Program ini harus mentranskripsikan input lisan Anda dan menerjemahkannya ke bahasa yang Anda tentukan (Prancis, Spanyol, atau Hindi). Ulangi proses ini, mencoba setiap bahasa yang didukung oleh aplikasi. Setelah selesai, tekan ENTER untuk mengakhiri program.

    > **Catatan**: TranslationRecognizer memberi Anda waktu sekitar 5 detik untuk berbicara. Jika tidak mendeteksi adanya input lisan, itu menghasilkan hasil "Tidak ada kecocokan".
    >
    > Terjemahan ke bahasa Hindi mungkin tidak selalu ditampilkan dengan benar di jendela Konsol karena masalah pengkodean karakter.

## <a name="synthesize-the-translation-to-speech"></a>Sintesiskan terjemahan menjadi ucapan

Sejauh ini, aplikasi Anda menerjemahkan masukan lisan ke teks; yang mungkin cukup jika Anda perlu meminta bantuan seseorang saat bepergian. Namun, akan lebih baik jika terjemahan diucapkan dengan lantang dengan suara yang sesuai.

1. Dalam fungsi **Terjemahkan**, di bawah komentar **Sintesis terjemahan**, tambahkan kode berikut untuk menggunakan klien **SpeechSynthesizer** guna menyintesis terjemahan sebagai ucapan melalui speaker default:

    **C#**
    
    ```C#
    // Synthesize translation
    var voices = new Dictionary<string, string>
                    {
                        ["fr"] = "fr-FR-HenriNeural",
                        ["es"] = "es-ES-ElviraNeural",
                        ["hi"] = "hi-IN-MadhurNeural"
                    };
    speechConfig.SpeechSynthesisVoiceName = voices[targetLanguage];
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(translation);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```
    
    **Python**
    
    ```Python
    # Synthesize translation
    voices = {
            "fr": "fr-FR-HenriNeural",
            "es": "es-ES-ElviraNeural",
            "hi": "hi-IN-MadhurNeural"
    }
    speech_config.speech_synthesis_voice_name = voices.get(targetLanguage)
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    speak = speech_synthesizer.speak_text_async(translation).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

2. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **penerjemah**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python translator.py
    ```

3. Saat diminta, masukkan kode bahasa yang valid (*fr*, *es*, atau *hi*), lalu ucapkan dengan jelas ke mikrofon dan ucapkan frasa yang mungkin Anda gunakan saat jalan-jalan keluar negeri. Program harus menyalin masukan lisan Anda dan merespons dengan terjemahan lisan. Ulangi proses ini, mencoba setiap bahasa yang didukung oleh aplikasi. Setelah selesai, tekan ENTER untuk mengakhiri program.

    > **Catatan** *Dalam contoh ini, Anda telah menggunakan **SpeechTranslationConfig** untuk menerjemahkan ucapan ke teks, lalu menggunakan **SpeechConfig** untuk menyintesis terjemahan sebagai ucapan. Anda sebenarnya dapat menggunakan **SpeechTranslationConfig** untuk mensintesis terjemahan secara langsung, tetapi ini hanya berfungsi saat menerjemahkan ke satu bahasa, dan menghasilkan aliran audio yang biasanya disimpan sebagai file daripada dikirim langsung ke pembicara.*

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang menggunakan API **Terjemahan Azure Cognitive Service untuk Ucapan**, lihat [Dokumentasi terjemahan Azure Cognitive Service untuk Ucapan](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-translation).
