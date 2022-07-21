---
lab:
  title: Menerjemahkan teks.
  module: Module 3 - Getting Started with Natural Language Processing
ms.openlocfilehash: 4ca4394ce5d9456abeabbdded1ee219f271f284e
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 11/16/2021
ms.locfileid: "145195646"
---
# <a name="translate-text"></a>Menerjemahkan teks.

Layanan **Penerjemah** adalah layanan kognitif yang memungkinkan Anda menerjemahkan teks antar bahasa.

Sebagai contoh, anggaplah sebuah biro perjalanan ingin memeriksa ulasan hotel yang telah dikirimkan ke situs web perusahaan, menstandarkan bahasa Inggris sebagai bahasa yang digunakan untuk analisis. Dengan menggunakan layanan Penerjemah, mereka dapat menentukan bahasa penulisan setiap ulasan, dan jika belum berbahasa Inggris, terjemahkan dari bahasa sumber apa pun yang ditulis ke dalam bahasa Inggris.

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
5. Saat sumber daya telah diterapkan, buka dan lihat halaman **Kunci dan Titik Akhir**. Anda akan memerlukan salah satu kunci dan lokasi di mana layanan disediakan dari halaman ini pada prosedur selanjutnya.

## <a name="prepare-to-use-the-translator-service"></a>Bersiaplah untuk menggunakan layanan Penerjemah

Dalam latihan ini, Anda akan menyelesaikan aplikasi klien yang diimplementasikan sebagian yang menggunakan Penerjemah REST API untuk menerjemahkan ulasan hotel.

> **Catatan**: Anda dapat memilih untuk menggunakan API dari **C#** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, jelajahi folder **06-translate-text** dan perluas folder **C-Sharp** atau **Python** tergantung pada preferensi bahasa Anda.
2. Lihat konten folder **text-translation**, dan perhatikan bahwa folder tersebut berisi file untuk pengaturan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk menyertakan **kunci** autentikasi untuk sumber daya layanan kognitif Anda, dan **lokasi** tempat penyebarannya (<u>bukan</u> titik akhir) - Anda harus menyalin keduanya dari halaman **kunci dan Titik akhir** untuk sumber daya layanan kognitif Anda. Simpan perubahan Anda.
3. Perhatikan bahwa folder **text-translation** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: text-translation.py

    Buka file kode dan periksa kode yang dikandungnya.

4. Dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat kunci dan wilayah layanan kognitif dari file konfigurasi telah disediakan. Titik akhir untuk layanan terjemahan juga ditentukan dalam kode Anda.
5. Klik kanan folder **text-translation**, buka terminal terintegrasi, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-translation.py
    ```

6. Amati output saat kode harus berjalan tanpa kesalahan, menampilkan konten setiap file teks ulasan dalam folder **ulasan**. Aplikasi saat ini tidak menggunakan layanan Penerjemah. Kami akan memperbaikinya di prosedur selanjutnya.

## <a name="detect-language"></a>Mendeteksi bahasa

Layanan Penerjemah dapat secara otomatis mendeteksi bahasa sumber teks yang akan diterjemahkan, tetapi juga memungkinkan Anda untuk secara eksplisit mendeteksi bahasa teks yang ditulis.

1. Dalam file kode Anda, temukan fungsi **GetLanguage**, yang saat ini mengembalikan "en" untuk semua nilai teks.
2. Dalam fungsi **GetLanguage**, di bawah komentar **Gunakan fungsi pendeteksi Penerjemah**, tambahkan kode berikut untuk menggunakan API REST Penerjemah untuk mendeteksi bahasa teks yang ditentukan, berhati-hatilah untuk tidak mengganti kode di akhir fungsi yang mengembalikan bahasa:

**C#**

```C#
// Use the Translator detect function
object[] body = new object[] { new { Text = text } };
var requestBody = JsonConvert.SerializeObject(body);
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Build the request
        string path = "/detect?api-version=3.0";
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(translatorEndpoint + path);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", cogSvcKey);
        request.Headers.Add("Ocp-Apim-Subscription-Region", cogSvcRegion);

        // Send the request and get response
        HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
        // Read response as a string
        string responseContent = await response.Content.ReadAsStringAsync();

        // Parse JSON array and get language
        JArray jsonResponse = JArray.Parse(responseContent);
        language = (string)jsonResponse[0]["language"]; 
    }
}
```

**Python**

```Python
# Use the Translator detect function
path = '/detect'
url = translator_endpoint + path

# Build the request
params = {
    'api-version': '3.0'
}

headers = {
'Ocp-Apim-Subscription-Key': cog_key,
'Ocp-Apim-Subscription-Region': cog_region,
'Content-type': 'application/json'
}

body = [{
    'text': text
}]

# Send the request and get response
request = requests.post(url, params=params, headers=headers, json=body)
response = request.json()

# Parse JSON array and get language
language = response[0]["language"]
```

3. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **text-translation**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-translation.py
    ```

4. Amati hasilnya, perhatikan bahwa kali ini bahasa untuk setiap ulasan diidentifikasi.

## <a name="translate-text"></a>Menerjemahkan teks

Sekarang setelah aplikasi Anda dapat menentukan bahasa yang digunakan untuk menulis ulasan, Anda dapat menggunakan layanan Penerjemah untuk menerjemahkan ulasan non-Inggris ke dalam bahasa Inggris.

1. Dalam file kode Anda, temukan fungsi **Terjemahkan**, yang saat ini mengembalikan dan mengosongkan string untuk semua nilai teks.
2. Dalam fungsi **Terjemahkan**, di bawah komentar **Gunakan fungsi terjemahan Penerjemah**, tambahkan kode berikut untuk menggunakan API REST Penerjemah untuk menerjemahkan teks yang ditentukan dari bahasa sumbernya ke dalam bahasa Inggris, berhati-hatilah tidak mengganti kode di akhir fungsi yang mengembalikan terjemahan:

**C#**

```C#
// Use the Translator translate function
object[] body = new object[] { new { Text = text } };
var requestBody = JsonConvert.SerializeObject(body);
using (var client = new HttpClient())
{
    using (var request = new HttpRequestMessage())
    {
        // Build the request
        string path = "/translate?api-version=3.0&from=" + sourceLanguage + "&to=en" ;
        request.Method = HttpMethod.Post;
        request.RequestUri = new Uri(translatorEndpoint + path);
        request.Content = new StringContent(requestBody, Encoding.UTF8, "application/json");
        request.Headers.Add("Ocp-Apim-Subscription-Key", cogSvcKey);
        request.Headers.Add("Ocp-Apim-Subscription-Region", cogSvcRegion);

        // Send the request and get response
        HttpResponseMessage response = await client.SendAsync(request).ConfigureAwait(false);
        // Read response as a string
        string responseContent = await response.Content.ReadAsStringAsync();

        // Parse JSON array and get translation
        JArray jsonResponse = JArray.Parse(responseContent);
        translation = (string)jsonResponse[0]["translations"][0]["text"];  
    }
}
```

**Python**

```Python
# Use the Translator translate function
path = '/translate'
url = translator_endpoint + path

# Build the request
params = {
    'api-version': '3.0',
    'from': source_language,
    'to': ['en']
}

headers = {
    'Ocp-Apim-Subscription-Key': cog_key,
    'Ocp-Apim-Subscription-Region': cog_region,
    'Content-type': 'application/json'
}

body = [{
    'text': text
}]

# Send the request and get response
request = requests.post(url, params=params, headers=headers, json=body)
response = request.json()

# Parse JSON array and get translation
translation = response[0]["translations"][0]["text"]
```

3. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **text-translation**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**
    
    ```
    dotnet run
    ```
    
    **Python**
    
    ```
    python text-translation.py
    ```

4. Amati hasilnya, perhatikan bahwa ulasan non-Inggris diterjemahkan ke dalam bahasa Inggris.

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang menggunakan layanan **Penerjemah**, lihat [dokumentasi Penerjemah](https://docs.microsoft.com/azure/cognitive-services/translator/).
