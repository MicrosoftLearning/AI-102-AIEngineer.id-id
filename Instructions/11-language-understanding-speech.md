---
lab:
  title: Menggunakan Layanan Speech dan LUIS
  module: Module 5 - Creating Language Understanding Solutions
ms.openlocfilehash: 31b88612bce38780f828859f8e2b166dbe8df34d
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 11/16/2021
ms.locfileid: "145195706"
---
# <a name="use-the-speech-and-language-understanding-services"></a>Menggunakan Layanan Speech dan LUIS

Anda dapat mengintegrasikan layanan Ucapan dengan layanan LUIS untuk membuat aplikasi yang dapat dengan cerdas menentukan niat pengguna dari input lisan.

> **Catatan**: Latihan ini berfungsi paling baik jika Anda memiliki mikrofon. Beberapa lingkungan virtual yang dihosting mungkin dapat menangkap audio dari mikrofon lokal Anda, tetapi jika ini tidak berhasil (atau Anda tidak memiliki mikrofon sama sekali), Anda dapat menggunakan file audio yang disediakan untuk input ucapan. Ikuti petunjuknya dengan cermat, karena Anda harus memilih opsi yang berbeda tergantung pada apakah Anda menggunakan mikrofon atau file audio.

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda sudah mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, buka di Visual Studio Code; jika belum, ikuti langkah-langkah ini untuk mengkloningnya sekarang.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="create-language-understanding-resources"></a>Membuat sumber daya Pemahaman Bahasa

Jika Anda sudah memiliki sumber daya penulisan dan prediksi LUIS di langganan Azure, Anda dapat menggunakannya dalam latihan ini. Jika tidak, ikuti petunjuk ini untuk membuatnya.

1. Buka portal Microsoft Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, jelajahi *pemahaman bahasa*, dan buat sumber daya **LUIS** dengan pengaturan berikut:
    - **Buat opsi**: Keduanya
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Nama**: *Masukkan nama yang unik*
    - **Lokasi penulisan**: *Pilih lokasi preferensi Anda*
    - **Tingkat harga penulisan**: F0
    - **Lokasi prediksi**: *Pilih <u>lokasi yang sama</u> sebagai lokasi penulisan Anda*
    - **Tingkat harga prediksi**: F0 (*Jika F0 tidak tersedia, pilih S0*)

3. Tunggu agar sumber daya dibuat, dan perhatikan bahwa dua sumber daya Pemahaman Bahasa disediakan; satu untuk penulisan, dan lainnya untuk prediksi. Anda dapat melihat keduanya dengan menavigasi ke grup sumber daya tempat Anda membuatnya.

## <a name="prepare-a-language-understanding-app"></a>Menyiapkan aplikasi LUIS

Jika Anda sudah memiliki aplikasi **Clock** dari latihan sebelumnya, buka di portal LUIS di `https://www.luis.ai`. Jika tidak, ikuti petunjuk ini untuk membuatnya.

1. Di tab browser baru, buka portal Pemahaman Bahasa di `https://www.luis.ai`.
2. Masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda. Jika ini adalah pertama kalinya Anda masuk ke portal LUIS, Anda mungkin perlu memberikan beberapa izin kepada aplikasi untuk mengakses detail akun Anda. Kemudian selesaikan langkah-langkah *Selamat Datang* dengan memilih langganan Azure dan sumber daya pembuatan yang baru saja Anda buat.
3. Buka halaman **Aplikasi Percakapan**, di samping **&#65291;Aplikasi baru**, lihat daftar drop-down dan pilih **Impor Sebagai LU**.
Jelajahi subfolder **11-luis-speech** dalam folder proyek yang berisi file lab untuk latihan ini, dan pilih **Clock.lu**. Kemudian tentukan nama unik untuk aplikasi jam.
4. Jika panel disertai tips untuk membuat aplikasi LUIS yang efektif muncul, tutup panel ini.

## <a name="train-and-publish-the-app-with-speech-priming"></a>Melatih dan menerbitkan aplikasi dengan *Speech Priming*

1. Di bagian atas portal LUIS, pilih **Latih** untuk melatih aplikasi jika belum dilatih.
2. Di kanan atas portal Pemahaman Bahasa, pilih **Terbitkan**. Kemudian pilih **slot Produksi** dan ubah pengaturan untuk mengaktifkan **Speech Priming** (ini akan menghasilkan performa yang lebih baik untuk pengenalan ucapan).
3. Setelah penerbitan selesai, di bagian atas portal Pemahaman Bahasa, pilih **Kelola**.
4. Pada halaman **Pengaturan**, perhatikan **ID Aplikasi**. Aplikasi klien memerlukan ini untuk menggunakan aplikasi Anda.
5. Pada halaman **Sumber Daya Azure**, di bawah **Sumber daya prediksi**, jika tidak ada sumber daya prediksi yang tercantum, tambahkan sumber daya prediksi di langganan Azure Anda.
6. Perhatikan **Kunci Utama**, **Kunci Sekunder**, dan **Lokasi** (<u>bukan</u> titik akhir!) untuk sumber daya prediksi. Aplikasi klien Speech SDK memerlukan lokasi dan salah satu kunci untuk terhubung ke sumber daya prediksi dan diautentikasi.

## <a name="configure-a-client-application-for-language-understanding"></a>Konfigurasikan aplikasi klien untuk LUIS

Dalam latihan ini, Anda akan membuat aplikasi klien yang menerima input lisan dan menggunakan aplikasi LUIS Anda untuk memprediksi niat pengguna.

> **Catatan**: Anda dapat memilih untuk menggunakan SDK baik untuk **C#** atau **Python** dalam latihan ini. Dalam langkah-langkah berikut, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, ramban ke folder **11-luis-speech** dan luaskan folder **C-Sharp** atau **Python** tergantung pada preferensi bahasa Anda.
2. Lihat konten folder **speaking-clock-client**, dan perhatikan bahwa folder tersebut berisi file untuk setelan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk menyertakan **ID Aplikasi** untuk aplikasi LUIS Anda, dan **Lokasi** (<u>bukan</u> titik akhir lengkap - untuk misalnya, *eastus*) dan salah satu **Kunci** untuk sumber daya prediksinya (dari laman **Kelola** untuk aplikasi Anda di portal LUIS).

## <a name="install-sdk-packages"></a>Menginstal Paket SDK

Untuk menggunakan Speech SDK dengan layanan LUIS, Anda perlu menginstal paket Speech SDK untuk bahasa pemrograman Anda.

1. Di Visual Studio, klik kanan folder **speaking-clock-client** dan buka terminal terintegrasi. Kemudian instal paket LUIS SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**

    ```
    dotnet add package Microsoft.CognitiveServices.Speech --version 1.14.0
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-speech==1.14.0
    ```

2. Selain itu, jika sistem Anda <u>tidak</u> memiliki mikrofon yang berfungsi, Anda harus menggunakan file audio untuk memberikan input lisan untuk aplikasi Anda. Dalam hal ini, gunakan perintah berikut untuk menginstal paket tambahan sehingga program Anda dapat memutar file audio (Anda dapat melewati ini jika Anda berniat menggunakan mikrofon):

    **C#**

    ```
    dotnet add package System.Windows.Extensions --version 4.6.0 
    ```

    **Python**

    ```
    pip install playsound==1.2.2
    ```

3. Perhatikan bahwa folder **speaking-clock-client** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: speaking-clock-client.py

4. Buka file kode dan di bagian atas, di bawah referensi namespace yang ada, temukan komentar **Impor namespace**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk mengimpor namespace yang Anda perlukan untuk menggunakan Speech SDK:

    **C#**

    ```C#
    // Import namespaces
    using Microsoft.CognitiveServices.Speech;
    using Microsoft.CognitiveServices.Speech.Audio;
    using Microsoft.CognitiveServices.Speech.Intent;
    ```

    **Python**

    ```Python
    # Import namespaces
    import azure.cognitiveservices.speech as speech_sdk
    ```

5. Selain itu, jika sistem Anda <u>tidak</u> memiliki mikrofon yang berfungsi, di bawah impor namespace yang ada, tambahkan kode berikut untuk mengimpor pustaka yang akan Anda gunakan untuk memutar file audio:

    **C#**

    ```C#
    using System.Media;
    ```

    **Python**

    ```Python
    from playsound import playsound
    ```

## <a name="create-an-intentrecognizer"></a>Membuat *IntentRecognizer*

Kelas **IntentRecognizer** menyediakan objek klien yang dapat Anda gunakan untuk mendapatkan prediksi LUIS dari input lisan.

1. Dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat ID Aplikasi, wilayah prediksi, dan kunci dari file konfigurasi telah disediakan. Kemudian temukan komentar **Konfigurasikan layanan ucapan dan dapatkan pengenal niat**, dan tambahkan kode berikut tergantung pada apakah Anda akan menggunakan mikrofon atau file audio untuk input ucapan:

    ### <a name="if-you-have-a-working-microphone"></a>**Jika Anda memiliki mikrofon yang berfungsi:**

    **C#**

    ```C#
    // Configure speech service and get intent recognizer
    SpeechConfig speechConfig = SpeechConfig.FromSubscription(predictionKey, predictionRegion);
    AudioConfig audioConfig = AudioConfig.FromDefaultMicrophoneInput();
    IntentRecognizer recognizer = new IntentRecognizer(speechConfig, audioConfig);
    ```

    **Python**

    ```Python
    # Configure speech service and get intent recognizer
    speech_config = speech_sdk.SpeechConfig(subscription=lu_prediction_key, region=lu_prediction_region)
    audio_config = speech_sdk.AudioConfig(use_default_microphone=True)
    recognizer = speech_sdk.intent.IntentRecognizer(speech_config, audio_config)
    ```

    ### <a name="if-you-need-to-use-an-audio-file"></a>**Jika Anda perlu menggunakan file audio:**

    **C#**

    ```C#
    // Configure speech service and get intent recognizer
    string audioFile = "time-in-london.wav";
    SoundPlayer wavPlayer = new SoundPlayer(audioFile);
    wavPlayer.Play();
    System.Threading.Thread.Sleep(2000);
    SpeechConfig speechConfig = SpeechConfig.FromSubscription(predictionKey, predictionRegion);
    AudioConfig audioConfig = AudioConfig.FromWavFileInput(audioFile);
    IntentRecognizer recognizer = new IntentRecognizer(speechConfig, audioConfig);
    ```

    **Python**

    ```Python
    # Configure speech service and get intent recognizer
    audioFile = 'time-in-london.wav'
    playsound(audioFile)
    speech_config = speech_sdk.SpeechConfig(subscription=lu_prediction_key, region=lu_prediction_region)
    audio_config = speech_sdk.AudioConfig(filename=audioFile)
    recognizer = speech_sdk.intent.IntentRecognizer(speech_config, audio_config)
    ```

## <a name="get-a-predicted-intent-from-spoken-input"></a>Mendapatkan niat yang diprediksi dari input lisan

Sekarang Anda siap untuk menerapkan kode yang menggunakan Speech SDK untuk mendapatkan niat yang diprediksi dari input lisan.

1. Dalam fungsi **Utama**, tepat di bawah kode yang baru saja Anda tambahkan, temukan komentar **Dapatkan model dari AppID dan tambahkan niat yang ingin kami gunakan** dan tambahkan kode berikut untuk mendapatkan model LUIS Anda (berdasarkan ID Aplikasinya) dan tentukan niat yang ingin kami identifikasi oleh pengenal.

    **C#**

    ```C#
    // Get the model from the AppID and add the intents we want to use
    var model = LanguageUnderstandingModel.FromAppId(luAppId);
    recognizer.AddIntent(model, "GetTime", "time");
    recognizer.AddIntent(model, "GetDate", "date");
    recognizer.AddIntent(model, "GetDay", "day");
    recognizer.AddIntent(model, "None", "none");
    ```

    *Perhatikan bahwa Anda dapat menentukan ID berbasis string untuk setiap niat*

    **Python**

    ```Python
    # Get the model from the AppID and add the intents we want to use
    model = speech_sdk.intent.LanguageUnderstandingModel(app_id=lu_app_id)
    intents = [
        (model, "GetTime"),
        (model, "GetDate"),
        (model, "GetDay"),
        (model, "None")
    ]
    recognizer.add_intents(intents)
    ```

2. Di bawah komentar **Proses input ucapan**, tambahkan kode berikut, yang menggunakan pengenal untuk secara asinkron memanggil layanan LUIS dengan input lisan, dan mengambil respons. Jika respons menyertakan niat yang diprediksi, kueri lisan, niat yang diprediksi, dan respons JSON lengkap ditampilkan. Jika tidak, kode menangani respons berdasarkan alasan yang dikembalikan.

**C#**

```C
// Process speech input
string intent = "";
var result = await recognizer.RecognizeOnceAsync().ConfigureAwait(false);
if (result.Reason == ResultReason.RecognizedIntent)
{
    // Intent was identified
    intent = result.IntentId;
    Console.WriteLine($"Query: {result.Text}");
    Console.WriteLine($"Intent Id: {intent}.");
    string jsonResponse = result.Properties.GetProperty(PropertyId.LanguageUnderstandingServiceResponse_JsonResult);
    Console.WriteLine($"JSON Response:\n{jsonResponse}\n");
    
    // Get the first entity (if any)

    // Apply the appropriate action
    
}
else if (result.Reason == ResultReason.RecognizedSpeech)
{
    // Speech was recognized, but no intent was identified.
    intent = result.Text;
    Console.Write($"I don't know what {intent} means.");
}
else if (result.Reason == ResultReason.NoMatch)
{
    // Speech wasn't recognized
    Console.WriteLine($"Sorry. I didn't understand that.");
}
else if (result.Reason == ResultReason.Canceled)
{
    // Something went wrong
    var cancellation = CancellationDetails.FromResult(result);
    Console.WriteLine($"CANCELED: Reason={cancellation.Reason}");
    if (cancellation.Reason == CancellationReason.Error)
    {
        Console.WriteLine($"CANCELED: ErrorCode={cancellation.ErrorCode}");
        Console.WriteLine($"CANCELED: ErrorDetails={cancellation.ErrorDetails}");
    }
}
```

**Python**

```Python
# Process speech input
intent = ''
result = recognizer.recognize_once_async().get()
if result.reason == speech_sdk.ResultReason.RecognizedIntent:
    intent = result.intent_id
    print("Query: {}".format(result.text))
    print("Intent: {}".format(intent))
    json_response = json.loads(result.intent_json)
    print("JSON Response:\n{}\n".format(json.dumps(json_response, indent=2)))
    
    # Get the first entity (if any)
    
    # Apply the appropriate action
    
elif result.reason == speech_sdk.ResultReason.RecognizedSpeech:
    # Speech was recognized, but no intent was identified.
    intent = result.text
    print("I don't know what {} means.".format(intent))
elif result.reason == speech_sdk.ResultReason.NoMatch:
    # Speech wasn't recognized
    print("Sorry. I didn't understand that.")
elif result.reason == speech_sdk.ResultReason.Canceled:
    # Something went wrong
    print("Intent recognition canceled: {}".format(result.cancellation_details.reason))
    if result.cancellation_details.reason == speech_sdk.CancellationReason.Error:
        print("Error details: {}".format(result.cancellation_details.error_details))
```

Kode yang Anda tambahkan sejauh ini mengidentifikasi *niat*, tetapi beberapa niat dapat mereferensikan *entitas*, jadi Anda harus menambahkan kode untuk mengekstrak informasi entitas dari JSON yang dikembalikan oleh layanan.

3. Dalam kode yang baru saja Anda tambahkan, temukan komentar **Dapatkan entitas pertama (jika ada)** dan tambahkan kode berikut di bawahnya:

**C#**

```C
// Get the first entity (if any)
JObject jsonResults = JObject.Parse(jsonResponse);
string entityType = "";
string entityValue = "";
if (jsonResults["entities"].HasValues)
{
    JArray entities = new JArray(jsonResults["entities"][0]);
    entityType = entities[0]["type"].ToString();
    entityValue = entities[0]["entity"].ToString();
    Console.WriteLine(entityType + ": " + entityValue);
}
```

**Python**

```Python
# Get the first entity (if any)
entity_type = ''
entity_value = ''
if len(json_response["entities"]) > 0:
    entity_type = json_response["entities"][0]["type"]
    entity_value = json_response["entities"][0]["entity"]
    print(entity_type + ': ' + entity_value)
```
    
Kode Anda sekarang menggunakan aplikasi LUIS untuk memprediksi niat serta entitas apa pun yang terdeteksi dalam ucapan input. Aplikasi klien Anda sekarang harus menggunakan prediksi tersebut untuk menentukan dan melakukan tindakan yang sesuai.

4. Di bawah kode yang baru saja Anda tambahkan, temukan komentar **Terapkan tindakan yang sesuai**, dan tambahkan kode berikut, yang memeriksa niat yang didukung oleh aplikasi (**GetTime**, **GetDate**, dan **GetDay**) dan menentukan apakah entitas relevan telah terdeteksi, sebelum memanggil fungsi yang ada untuk menghasilkan respons yang sesuai.

**C#**

```C#
// Apply the appropriate action
switch (intent)
{
    case "time":
        var location = "local";
        // Check for entities
        if (entityType == "Location")
        {
            location = entityValue;
        }
        // Get the time for the specified location
        var getTimeTask = Task.Run(() => GetTime(location));
        string timeResponse = await getTimeTask;
        Console.WriteLine(timeResponse);
        break;
    case "day":
        var date = DateTime.Today.ToShortDateString();
        // Check for entities
        if (entityType == "Date")
        {
            date = entityValue;
        }
        // Get the day for the specified date
        var getDayTask = Task.Run(() => GetDay(date));
        string dayResponse = await getDayTask;
        Console.WriteLine(dayResponse);
        break;
    case "date":
        var day = DateTime.Today.DayOfWeek.ToString();
        // Check for entities
        if (entityType == "Weekday")
        {
            day = entityValue;
        }

        var getDateTask = Task.Run(() => GetDate(day));
        string dateResponse = await getDateTask;
        Console.WriteLine(dateResponse);
        break;
    default:
        // Some other intent (for example, "None") was predicted
        Console.WriteLine("You said " + result.Text.ToLower());
        if (result.Text.ToLower().Replace(".", "") == "stop")
        {
            intent = result.Text;
        }
        else
        {
            Console.WriteLine("Try asking me for the time, the day, or the date.");
        }
        break;
}
```

**Python**

```Python
# Apply the appropriate action
if intent == 'GetTime':
    location = 'local'
    # Check for entities
    if entity_type == 'Location':
        location = entity_value
    # Get the time for the specified location
    print(GetTime(location))

elif intent == 'GetDay':
    date_string = date.today().strftime("%m/%d/%Y")
    # Check for entities
    if entity_type == 'Date':
        date_string = entity_value
    # Get the day for the specified date
    print(GetDay(date_string))

elif intent == 'GetDate':
    day = 'today'
    # Check for entities
    if entity_type == 'Weekday':
        # List entities are lists
        day = entity_value
    # Get the date for the specified day
    print(GetDate(day))

else:
    # Some other intent (for example, "None") was predicted
    print('You said {}'.format(result.text))
    if result.text.lower().replace('.', '') == 'stop':
        intent = result.text
    else:
        print('Try asking me for the time, the day, or the date.')
```

## <a name="run-the-client-application"></a>Jalankan aplikasi klien

1. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **speaking-clock-client**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python speaking-clock-client.py
    ```

2. Jika menggunakan mikrofon, ujaran dengan keras untuk menguji aplikasi. Misalnya, coba yang berikut ini (jalankan kembali program setiap kali):

    *Jam berapa?*
    
    *Jam berapa sekarang?*

    *Sekarang hari apa?*

    *Jam berapa di London?*

    *Tanggal berapa?*

    *Tanggal berapa hari Minggu?*

> **Catatan**: Logika dalam aplikasi memang sederhana, dan memiliki sejumlah batasan, tetapi harus mencakup tujuan pengujian kemampuan model LUIS untuk memprediksi niat dari input lisan menggunakan Speech SDK. Anda mungkin mengalami masalah saat mengenali niat **GetDay** dengan entitas tanggal tertentu karena kesulitan dalam menyeimbangkan tanggal dalam format *BB/TT/TTTT* !

## <a name="more-information"></a>Informasi selengkapnya

Untuk mempelajari selengkapnya tentang integrasi Speech dan LUIS, lihat [dokumentasi Speech](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstarts/intent-recognition).
