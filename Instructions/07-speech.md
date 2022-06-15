---
lab:
  title: Mengenali dan mensintesis ucapan
  module: Module 4 - Building Speech-Enabled Applications
ms.openlocfilehash: 4f65f068cab76299f838153d32a2d4e8979a1253
ms.sourcegitcommit: 3374bf6b03869daf624f3916bc34510fcbe580e0
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 02/18/2022
ms.locfileid: "145195639"
---
# <a name="recognize-and-synthesize-speech"></a>Mengenali dan mensintesis ucapan

Layanan **Ucapan** adalah layanan kognitif Azure yang menyediakan fungsionalitas terkait ucapan, termasuk:

- API *ucapan ke teks* yang memungkinkan Anda menerapkan pengenalan ucapan (mengonversi kata-kata lisan yang dapat didengar menjadi teks).
- API *teks ke ucapan* yang memungkinkan Anda menerapkan sintesis ucapan (mengonversi teks menjadi ucapan yang dapat didengar).

Dalam latihan ini, Anda akan menggunakan kedua API ini untuk mengimplementasikan aplikasi jam yang berbicara.

**Catatan**: Latihan ini mengharuskan Anda menggunakan komputer dengan speaker/headphone. Untuk pengalaman terbaik, mikrofon juga diperlukan. Beberapa lingkungan virtual yang dihosting mungkin dapat menangkap audio dari mikrofon lokal Anda, tetapi jika ini tidak berhasil (atau Anda tidak memiliki mikrofon sama sekali), Anda dapat menggunakan file audio yang disediakan untuk input ucapan. Ikuti petunjuknya dengan cermat, karena Anda harus memilih opsi yang berbeda tergantung pada apakah Anda menggunakan mikrofon atau file audio.

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda belum mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, ikuti langkah-langkah berikut untuk melakukannya. Jika tidak, buka folder kloning di Visual Studio Code.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

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
5. Saat sumber daya telah diterapkan, buka dan lihat halaman **Kunci dan Titik Akhir**. Anda akan memerlukan salah satu kunci dan lokasi di mana layanan disediakan dari halaman ini pada prosedur selanjutnya.

## <a name="prepare-to-use-the-speech-service"></a>Bersiap untuk menggunakan layanan Ucapan

Dalam latihan ini, Anda akan menyelesaikan aplikasi klien yang diimplementasikan sebagian yang menggunakan Speech SDK untuk mengenali dan mensintesis ucapan.

**Catatan**: Anda dapat memilih untuk menggunakan SDK baik untuk **C#** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, ramban ke folder **07-speech** dan luaskan folder **C-Sharp** atau **Python** tergantung pada preferensi bahasa Anda.
2. Klik kanan folder **speaking-clock** dan buka terminal terintegrasi. Kemudian pasang paket Speech SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.19.0
    ```
    
    **Python**
    
    ```
    pip install azure-cognitiveservices-speech==1.19.0
    ```

3. Lihat konten folder **speaking-clock**, dan perhatikan bahwa folder tersebut berisi file untuk setelan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk menyertakan **kunci** autentikasi untuk sumber daya layanan kognitif Anda, dan **lokasi** tempat penerapannya. Simpan perubahan Anda.
4. Perhatikan bahwa folder **speaking-clock** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: speaking-clock.py

    Buka file kode dan di bagian atas, di bawah referensi namespace yang ada, temukan komentar **Impor namespace**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk mengimpor namespace yang Anda perlukan untuk menggunakan Speech SDK:

    **C#**
    
    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    ```
    
    **Python**
    
    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. Dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat kunci dan wilayah layanan kognitif dari file konfigurasi telah disediakan. Anda harus menggunakan variabel ini untuk membuat **SpeechConfig** untuk sumber daya layanan kognitif Anda. Tambahkan kode berikut di bawah komentar **Konfigurasikan layanan ucapan**:

    **C#**
    
    ```C#
    // Configure speech service
    speechConfig = SpeechConfig.FromSubscription(cogSvcKey, cogSvcRegion);
    Console.WriteLine("Ready to use speech service in " + speechConfig.Region);
    
    // Configure voice
    speechConfig.SpeechSynthesisVoiceName = "en-US-AriaNeural";
    ```
    
    **Python**
    
    ```Python
    # Configure speech service
    speech_config = speech_sdk.SpeechConfig(cog_key, cog_region)
    print('Ready to use speech service in:', speech_config.region)
    ```

6. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **speaking-clock**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

7. Jika Anda menggunakan C#, Anda dapat mengabaikan peringatan apa pun tentang menggunakan operator **tunggu** dalam metode asinkron - kami akan memperbaikinya nanti. Kode harus menampilkan wilayah sumber daya layanan ucapan yang akan digunakan aplikasi.

## <a name="recognize-speech"></a>Mengenali ucapan

Sekarang setelah Anda memiliki **SpeechConfig** untuk layanan ucapan di sumber daya layanan kognitif, Anda dapat menggunakan API **Speech-to-text** untuk mengenali ucapan dan menyalinnya ke teks.

### <a name="if-you-have-a-working-microphone"></a>Jika Anda memiliki mikrofon yang berfungsi

1. Dalam fungsi **Utama** untuk program Anda, perhatikan bahwa kode menggunakan fungsi **TranscribeCommand** untuk menerima masukan lisan.
2. Dalam fungsi **TranscribeCommand**, di bawah komentar **Konfigurasikan pengenalan ucapan**, tambahkan kode yang sesuai di bawah ini untuk membuat klien **SpeechRecognizer** yang dapat digunakan untuk mengenali dan mentranskripsikan ucapan menggunakan mikrofon sistem default:

    **C#**
    
    ```C#
    // Configure speech recognition
    using AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    Console.WriteLine("Speak now...");
    ```
    
    **Python**
    
    ```Python
    # Configure speech recognition
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    print('Speak now...')
    ```

3. Sekarang lewati ke bagian **Tambahkan kode untuk memproses perintah yang ditranskripsikan** di bawah ini.

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

3. Dalam fungsi **Utama**, perhatikan bahwa kode menggunakan fungsi **TranscribeCommand** untuk menerima masukan lisan. Kemudian dalam fungsi **TranscribeCommand**, di bawah komentar **Konfigurasikan pengenalan ucapan**, tambahkan kode yang sesuai di bawah ini untuk membuat klien **SpeechRecognizer** yang dapat digunakan untuk mengenali dan mentranskripsikan ucapan dari file audio:

    **C#**

    ```C#
    // Configure speech recognition
    string audioFile = "time.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    using AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    using SpeechRecognizer speechRecognizer = new SpeechRecognizer(speechConfig, audioConfig);
    ```

    **Python**

    ```Python
    # Configure speech recognition
    audioFile = 'time.wav'
    playsound(audioFile)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    speech_recognizer = speech_sdk.SpeechRecognizer(speech_config, audio_config)
    ```

### <a name="add-code-to-process-the-transcribed-command"></a>Menambahkan kode untuk memproses perintah yang ditranskripsikan

1. Dalam fungsi **TranscribeCommand**, di bawah **input ucapan Proses** komentar, tambahkan kode berikut untuk mendengarkan input lisan, berhati-hatilah untuk tidak mengganti kode di akhir fungsi yang mengembalikan perintah:

    **C#**
    
    ```C#
    // Process speech input
    SpeechRecognitionResult speech = await speechRecognizer.RecognizeOnceAsync();
    if (speech.Reason == ResultReason.RecognizedSpeech)
    {
        command = speech.Text;
        Console.WriteLine(command);
    }
    else
    {
        Console.WriteLine(speech.Reason);
        if (speech.Reason == ResultReason.Canceled)
        {
            var cancellation = CancellationDetails.FromResult(speech);
            Console.WriteLine(cancellation.Reason);
            Console.WriteLine(cancellation.ErrorDetails);
        }
    }
    ```

    **Python**
    
    ```Python
    # Process speech input
    speech = speech_recognizer.recognize_once_async().get()
    if speech.reason == speech_sdk.ResultReason.RecognizedSpeech:
        command = speech.text
        print(command)
    else:
        print(speech.reason)
        if speech.reason == speech_sdk.ResultReason.Canceled:
            cancellation = speech.cancellation_details
            print(cancellation.reason)
            print(cancellation.error_details)
    ```

2. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **speaking-clock**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. Jika menggunakan mikrofon, bicaralah dengan jelas dan ucapkan "jam berapa sekarang?". Program harus mentranskripsikan input lisan Anda dan menampilkan waktu (berdasarkan waktu lokal komputer tempat kode berjalan, yang mungkin bukan waktu yang benar di mana Anda berada).

    SpeechRecognizer memberi Anda waktu sekitar 5 detik untuk berbicara. Jika tidak mendeteksi adanya input lisan, itu menghasilkan hasil "Tidak ada kecocokan".

    Jika SpeechRecognizer mengalami kesalahan, itu menghasilkan hasil dari "Dibatalkan". Kode dalam aplikasi kemudian akan menampilkan pesan kesalahan. Penyebab yang paling mungkin adalah kunci atau wilayah yang salah dalam file konfigurasi.

## <a name="synthesize-speech"></a>Mensintesis ucapan

Aplikasi jam berbicara Anda menerima masukan lisan, tetapi tidak benar-benar berbicara! Mari kita perbaiki dengan menambahkan kode untuk mensintesis ucapan.

1. Dalam fungsi **Utama** untuk program Anda, perhatikan bahwa kode menggunakan fungsi **TellTime** untuk memberi tahu pengguna waktu saat ini.
2. Dalam fungsi **TellTime**, di bawah komentar **Konfigurasikan sintesis ucapan**, tambahkan kode berikut untuk membuat klien **SpeechSynthesizer** yang dapat digunakan untuk menghasilkan output lisan:

    **C#**
    
    ```C#
    // Configure speech synthesis
    speechConfig.SpeechSynthesisVoiceName = "en-GB-RyanNeural";
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    ```
    
    **Python**
    
    ```Python
    # Configure speech synthesis
    speech_config.speech_synthesis_voice_name = "en-GB-RyanNeural"
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    ```
    
    > **Catatan**: *Konfigurasi audio default menggunakan perangkat audio sistem default untuk output, jadi Anda tidak perlu menyediakan **AudioConfig** secara eksplisit. Jika Anda perlu mengarahkan output audio ke file, Anda dapat menggunakan **AudioConfig** dengan filepath untuk melakukannya.*

3. Dalam fungsi **TellTime**, di bawah **komentar Sintesis output lisan**, tambahkan kode berikut untuk menghasilkan output lisan, berhati-hatilah untuk tidak mengganti kode di akhir fungsi yang mencetak respons:

    **C#**
    
    ```C#
    // Synthesize spoken output
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakTextAsync(responseText);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```
    
    **Python**
    
    ```Python
    # Synthesize spoken output
    speak = speech_synthesizer.speak_text_async(response_text).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

4. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **speaking-clock**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

5. Ketika diminta, bicaralah dengan jelas ke mikrofon dan ucapkan "jam berapa sekarang?". Program harus berbicara, memberi tahu Anda waktu.

## <a name="use-a-different-voice"></a>Gunakan suara yang berbeda

Aplikasi jam berbicara Anda menggunakan suara default, yang dapat Anda ubah. Layanan Ucapan mendukung berbagai suara *standar* serta suara *saraf* yang lebih mirip manusia. Anda juga dapat membuat suara *kustom*.

> **Catatan**: Untuk daftar suara saraf dan standar, lihat [Dukungan bahasa dan suara](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech) dalam dokumentasi layanan Ucapan.

1. Dalam fungsi **TellTime**, di bawah komentar **Konfigurasikan sintesis ucapan**, ubah kode sebagai berikut untuk menentukan suara alternatif sebelum membuat klien **SpeechSynthesizer** :

   **C#**

    ```C#
    // Configure speech synthesis
    speechConfig.SpeechSynthesisVoiceName = "en-GB-LibbyNeural"; // change this
    using SpeechSynthesizer speechSynthesizer = new SpeechSynthesizer(speechConfig);
    ```
    
    **Python**
    
    ```Python
    # Configure speech synthesis
    speech_config.speech_synthesis_voice_name = 'en-GB-LibbyNeural' # change this
    speech_synthesizer = speech_sdk.SpeechSynthesizer(speech_config)
    ```

2. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **speaking-clock**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. Ketika diminta, bicaralah dengan jelas ke mikrofon dan ucapkan "jam berapa sekarang?". Program harus berbicara dalam suara yang ditentukan, memberi tahu Anda waktu.

## <a name="use-speech-synthesis-markup-language"></a>Menggunakan Speech Synthesis Markup Language

Speech Synthesis Markup Language (SSML) memungkinkan Anda menyesuaikan cara ucapan Anda disintesis menggunakan format berbasis XML.

1. Dalam fungsi **TellTime**, ganti semua kode saat ini di bawah **komentar Sintesis output lisan** dengan kode berikut (biarkan kode di bawah komentar **Cetak respons**):

   **C#**

    ```C#
    // Synthesize spoken output
    string responseSsml = $@"
        <speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'>
            <voice name='en-GB-LibbyNeural'>
                {responseText}
                <break strength='weak'/>
                Time to end this lab!
            </voice>
        </speak>";
    SpeechSynthesisResult speak = await speechSynthesizer.SpeakSsmlAsync(responseSsml);
    if (speak.Reason != ResultReason.SynthesizingAudioCompleted)
    {
        Console.WriteLine(speak.Reason);
    }
    ```

    **Python**
    
    ```Python
    # Synthesize spoken output
    responseSsml = " \
        <speak version='1.0' xmlns='http://www.w3.org/2001/10/synthesis' xml:lang='en-US'> \
            <voice name='en-GB-LibbyNeural'> \
                {} \
                <break strength='weak'/> \
                Time to end this lab! \
            </voice> \
        </speak>".format(response_text)
    speak = speech_synthesizer.speak_ssml_async(responseSsml).get()
    if speak.reason != speech_sdk.ResultReason.SynthesizingAudioCompleted:
        print(speak.reason)
    ```

2. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **speaking-clock**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python speaking-clock.py
    ```

3. Ketika diminta, bicaralah dengan jelas ke mikrofon dan ucapkan "jam berapa sekarang?". Program harus berbicara dalam suara yang ditentukan dalam SSML (mengesampingkan suara yang ditentukan dalam SpeechConfig), memberi tahu Anda waktu, dan kemudian setelah jeda memberi tahu Anda saatnya untuk mengakhiri lab ini - yang mana itu!

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang menggunakan API **Ucapan ke teks** dan **Teks ke ucapan**, lihat [dokumentasi Ucapan ke teks](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-speech-to-text) dan [dokumentasi Teks ke ucapan](https://docs.microsoft.com/azure/cognitive-services/speech-service/index-text-to-speech).
