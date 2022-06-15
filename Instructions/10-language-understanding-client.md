---
lab:
  title: Membuat Aplikasi Klien LUIS
  module: Module 5 - Creating Language Understanding Solutions
ms.openlocfilehash: 36f75e47910707c959495140be43c4196649d471
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 11/16/2021
ms.locfileid: "145195645"
---
# <a name="create-a-language-understanding-client-application"></a>Membuat Aplikasi Klien LUIS

Layanan Pemahaman Bahasa memungkinkan Anda menentukan aplikasi yang merangkum model bahasa yang dapat digunakan aplikasi untuk menafsirkan input bahasa alami dari pengguna, memprediksi *niat* pengguna (apa yang ingin mereka capai), dan mengidentifikasi *entitas* tempat niat harus diterapkan. Anda dapat membuat aplikasi klien yang menggunakan aplikasi LUIS secara langsung melalui antarmuka REST, atau dengan menggunakan kit pengembangan perangkat lunak (SDK) khusus bahasa.

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda sudah mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, buka di Visual Studio Code; jika belum, ikuti langkah-langkah ini untuk mengkloningnya sekarang.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana).
3. Setelah repositori dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan guna membangun dan men-debug, pilih **Tidak Sekarang**.

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

## <a name="import-train-and-publish-a-language-understanding-app"></a>Impor, latih, dan publikasikan aplikasi LUIS

Jika Anda sudah memiliki aplikasi **Jam** dari latihan sebelumnya, Anda dapat menggunakannya dalam latihan ini. Jika tidak, ikuti petunjuk ini untuk membuatnya.

1. Di tab browser baru, buka portal Pemahaman Bahasa di `https://www.luis.ai`.
2. Masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda. Jika ini adalah pertama kalinya Anda masuk ke portal LUIS, Anda mungkin perlu memberikan beberapa izin kepada aplikasi untuk mengakses detail akun Anda. Kemudian selesaikan langkah-langkah *Selamat Datang* dengan memilih langganan Azure dan sumber daya pembuatan yang baru saja Anda buat.
3. Buka halaman **Aplikasi Percakapan**, di samping **&#65291;Aplikasi baru**, lihat daftar drop-down dan pilih **Impor Sebagai LU**.
Jelajahi sub-folder **10-luis-client** dalam folder proyek yang berisi file lab untuk latihan ini, dan pilih **Clock.lu**. Kemudian tentukan nama unik untuk aplikasi jam.
4. Jika panel disertai tips untuk membuat aplikasi LUIS yang efektif muncul, tutup panel ini.
5. Di bagian atas portal LUIS, pilih **Latih** untuk melatih aplikasi.
6. Di kanan atas portal LUIS, pilih **Publikasikan** dan publikasikan aplikasi ke **slot Produksi**.
7. Setelah penerbitan selesai, di bagian atas portal Pemahaman Bahasa, pilih **Kelola**.
8. Pada halaman **Pengaturan**, perhatikan **ID Aplikasi**. Aplikasi klien memerlukan ini untuk menggunakan aplikasi Anda.
9. Pada halaman **Sumber Daya Azure**, di bawah **Sumber daya prediksi**, jika tidak ada sumber daya prediksi yang tercantum, tambahkan sumber daya prediksi di langganan Azure Anda.
10. Catat **Kunci Utama**, **Kunci Sekunder**, dan **URL Titik Akhir** untuk sumber daya prediksi. Aplikasi klien memerlukan titik akhir dan salah satu kunci untuk terhubung ke sumber daya prediksi dan diautentikasi.

## <a name="prepare-to-use-the-language-understanding-sdk"></a>Bersiaplah untuk menggunakan SDK LUIS

Dalam latihan ini, Anda akan menyelesaikan aplikasi klien yang diimplementasikan sebagian yang menggunakan aplikasi LUIS jam untuk memprediksi maksud dari input pengguna dan merespons dengan tepat.

> **Catatan**: Anda dapat memilih untuk menggunakan SDK baik untuk **C#** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, jelajahi folder **10-luis-client** dan perluas folder **C-Sharp** atau **Python** tergantung pada preferensi bahasa Anda.
2. Klik kanan folder **clock-client** dan buka terminal terintegrasi. Kemudian instal paket LUIS SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime --version 3.0.0
```

*Selain paket **Runtime** (prediksi), ada paket **Authoring** yang dapat Anda gunakan untuk menulis kode guna membuat dan mengelola model LUIS.*

**Python**

```
pip install azure-cognitiveservices-language-luis==0.7.0
```
    
*Paket Python SDK menyertakan kelas untuk **prediksi** dan **penulisan**.*

3. Lihat konten folder **clock-client**, dan perhatikan bahwa folder tersebut berisi file untuk pengaturan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang ada di dalamnya untuk menyertakan **ID APLIKASI** untuk aplikasi LUIS Anda, dan **URL Titik Akhir** dan salah satu **Kunci** untuk sumber daya prediksinya (dari halaman **Kelola** untuk aplikasi Anda di portal LUIS).

4. Perhatikan bahwa folder **clock-client** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: clock-client.py

    Buka file kode dan di bagian atas, di bawah referensi namespace yang ada, temukan komentar **Impor namespace**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk mengimpor ruang nama yang Anda perlukan untuk menggunakan SDK prediksi LUIS:

**C#**

```C#
// Import namespaces
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime;
using Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.Models;
```

**Python**

```Python
# Import namespaces
from azure.cognitiveservices.language.luis.runtime import LUISRuntimeClient
from msrest.authentication import CognitiveServicesCredentials
```

## <a name="get-a-prediction-from-the-language-understanding-app"></a>Dapatkan prediksi dari aplikasi LUIS

Sekarang Anda siap menerapkan kode yang menggunakan SDK untuk mendapatkan prediksi dari aplikasi LUIS Anda.

1. Dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat ID Aplikasi, titik akhir prediksi, dan kunci dari file konfigurasi telah disediakan. Kemudian temukan komentar **Buat klien untuk aplikasi LU** dan tambahkan kode berikut untuk membuat klien prediksi untuk aplikasi LUIS Anda:

**C#**

```C#
// Create a client for the LU app
var credentials = new Microsoft.Azure.CognitiveServices.Language.LUIS.Runtime.ApiKeyServiceClientCredentials(predictionKey);
var luClient = new LUISRuntimeClient(credentials) { Endpoint = predictionEndpoint };
```

**Python**

```Python
# Create a client for the LU app
credentials = CognitiveServicesCredentials(lu_prediction_key)
lu_client = LUISRuntimeClient(lu_prediction_endpoint, credentials)
```

2. Perhatikan bahwa kode dalam fungsi **Utama** meminta masukan pengguna hingga pengguna memasukkan "berhenti". Dalam perulangan ini, temukan komentar **Panggil aplikasi LU untuk mendapatkan maksud dan entitas** dan tambahkan kode berikut:

**C#**

```C#
// Call the LU app to get intent and entities
var slot = "Production";
var request = new PredictionRequest { Query = userText };
PredictionResponse predictionResponse = await luClient.Prediction.GetSlotPredictionAsync(luAppId, slot, request);
Console.WriteLine(JsonConvert.SerializeObject(predictionResponse, Formatting.Indented));
Console.WriteLine("--------------------\n");
Console.WriteLine(predictionResponse.Query);
var topIntent = predictionResponse.Prediction.TopIntent;
var entities = predictionResponse.Prediction.Entities;
```

**Python**

```Python
# Call the LU app to get intent and entities
request = { "query" : userText }
slot = 'Production'
prediction_response = lu_client.prediction.get_slot_prediction(lu_app_id, slot, request)
top_intent = prediction_response.prediction.top_intent
entities = prediction_response.prediction.entities
print('Top Intent: {}'.format(top_intent))
print('Entities: {}'.format (entities))
print('-----------------\n{}'.format(prediction_response.query))
```

Panggilan ke aplikasi LUIS mengembalikan prediksi, yang menyertakan maksud teratas (kemungkinan besar) serta entitas apa pun yang terdeteksi dalam ucapan input. Aplikasi klien Anda sekarang harus menggunakan prediksi tersebut untuk menentukan dan melakukan tindakan yang sesuai.

3. Temukan komentar **Terapkan tindakan yang sesuai**, dan tambahkan kode berikut, yang memeriksa maksud yang didukung oleh aplikasi (**GetTime**, **GetDate**, dan **GetDay**) dan menentukan apakah entitas relevan telah terdeteksi, sebelum memanggil fungsi yang ada untuk menghasilkan respons yang sesuai.

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
            if (entities.ContainsKey("Location"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Location"].ToString());
                // ML entities are strings, get the first one
                location = entityJson[0].ToString();
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
            if (entities.ContainsKey("Date"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Date"].ToString());
                // Regex entities are strings, get the first one
                date = entityJson[0].ToString();
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
            if (entities.ContainsKey("Weekday"))
            {
                //Get the JSON for the entity
                var entityJson = JArray.Parse(entities["Weekday"].ToString());
                // List entities are lists
                day = entityJson[0][0].ToString();
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
        if 'Location' in entities:
            # ML entities are strings, get the first one
            location = entities['Location'][0]
    # Get the time for the specified location
    print(GetTime(location))

elif top_intent == 'GetDay':
    date_string = date.today().strftime("%m/%d/%Y")
    # Check for entities
    if len(entities) > 0:
        # Check for a Date entity
        if 'Date' in entities:
            # Regex entities are strings, get the first one
            date_string = entities['Date'][0]
    # Get the day for the specified date
    print(GetDay(date_string))

elif top_intent == 'GetDate':
    day = 'today'
    # Check for entities
    if len(entities) > 0:
        # Check for a Weekday entity
        if 'Weekday' in entities:
            # List entities are lists
            day = entities['Weekday'][0][0]
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

> **Catatan**: Logika dalam aplikasi ini sengaja dibuat sederhana, dan memiliki sejumlah keterbatasan. Misalnya, saat mendapatkan waktu, hanya kumpulan kota terbatas yang didukung dan waktu musim panas diabaikan. Tujuannya adalah untuk melihat contoh pola tipikal untuk menggunakan LUIS di mana aplikasi Anda harus:
>
>   1. Hubungkan ke titik akhir prediksi.
>   2. Kirim ucapan untuk mendapatkan prediksi.
>   3. Terapkan logika untuk merespons dengan tepat maksud dan entitas yang diprediksi.

6. Setelah Anda selesai menguji, masukkan *berhenti*.

## <a name="more-information"></a>Informasi selengkapnya

Untuk mempelajari lebih lanjut tentang membuat klien LUIS, lihat [dokumentasi pengembang](https://docs.microsoft.com/azure/cognitive-services/luis/developer-reference-resource)
