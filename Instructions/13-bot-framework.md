---
lab:
  title: Membuat Bot dengan Bot Framework SDK
  module: Module 7 - Conversational AI and the Azure Bot Service
ms.openlocfilehash: 7f2d78bec2ee9fab7bf5fad9ec9ef8ebc76b7fba
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 11/16/2021
ms.locfileid: "145195686"
---
# <a name="create-a-bot-with-the-bot-framework-sdk"></a>Membuat Bot dengan Bot Framework SDK

*Bot* adalah agen perangkat lunak yang dapat berpartisipasi dalam dialog percakapan dengan pengguna manusia. Microsoft Bot Framework menyediakan platform komprehensif untuk membangun bot yang dapat dikirimkan sebagai layanan cloud melalui Azure Bot Service.

Dalam latihan ini, Anda akan menggunakan Microsoft Bot Framework SDK untuk membuat dan menerapkan bot.

## <a name="before-you-start"></a>Sebelum Anda memulai

Mari kita mulai dengan mempersiapkan lingkungan untuk pengembangan bot.

### <a name="update-the-bot-framework-emulator"></a>Perbarui Emulator Kerangka Bot

Anda akan menggunakan Kerangka kerja Bot SDK untuk membuat bot Anda, dan Bot Framework Emulator untuk mengujinya. Emulator Kerangka Kerja Bot diperbarui secara berkala, jadi pastikan Anda telah memasang versi terbaru.

> **Catatan**: Pembaruan dapat mencakup perubahan pada antarmuka pengguna yang memengaruhi instruksi dalam latihan ini.

1. Mulai **Bot Framework Emulator**, dan jika Anda diminta untuk memasang pembaruan, lakukan untuk pengguna yang saat ini masuk. Jika Anda tidak diminta secara otomatis, gunakan opsi **Periksa pembaruan** pada menu **Bantuan** untuk memeriksa pembaruan.
2. Setelah memasang pembaruan yang tersedia, tutup Bot Framework Emulator hingga Anda membutuhkannya lagi nanti.

### <a name="clone-the-repository-for-this-course"></a>Kloning repositori untuk kursus ini

Jika Anda belum mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, ikuti langkah-langkah berikut untuk melakukannya. Jika tidak, buka folder kloning di Visual Studio Code.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana pun).
3. Ketika repositori telah dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="create-a-bot"></a>Buat bot

Anda dapat menggunakan Bot Framework SDK untuk membuat bot berdasarkan template, lalu menyesuaikan kode untuk memenuhi kebutuhan spesifik Anda.

> **Catatan**: Dalam latihan ini, Anda dapat memilih untuk menggunakan **C#** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, ramban ke folder **13-bot-framework** dan luaskan **C-Sharp** atau **Python** folder tergantung pada preferensi bahasa Anda.
2. Klik kanan folder untuk bahasa pilihan Anda dan buka terminal terintegrasi.
3. Di terminal, jalankan perintah berikut untuk memasang template bot dan paket yang Anda butuhkan:

**C#**

```C#
dotnet new -i Microsoft.Bot.Framework.CSharp.EchoBot
dotnet new -i Microsoft.Bot.Framework.CSharp.CoreBot
dotnet new -i Microsoft.Bot.Framework.CSharp.EmptyBot
```

**Python**

```Python
pip install botbuilder-core
pip install asyncio
pip install aiohttp
pip install cookiecutter==1.7.0
```

4. Setelah template dan paket terpasang, jalankan perintah berikut untuk membuat bot berdasarkan template *EchoBot*:

**C#**

```C#
dotnet new echobot -n TimeBot
```

**Python**

```Python
cookiecutter https://github.com/microsoft/botbuilder-python/releases/download/Templates/echo.zip
```

Jika Anda menggunakan Python, saat diminta oleh cookiecutter, masukkan detail berikut:
- **bot_name**: TimeBot
- **bot_description**: Bot untuk zaman kita
    
5. Di panel terminal, masukkan perintah berikut untuk mengubah direktori saat ini ke folder **TimeBot** yang mencantumkan file kode yang telah dibuat untuk bot Anda:

    ```Code
    cd TimeBot
    dir
    ```

## <a name="test-the-bot-in-the-bot-framework-emulator"></a>Uji bot di Emulator Kerangka Bot

Anda telah membuat bot berdasarkan kerangka *EchoBot*. Sekarang Anda dapat menjalankannya secara lokal dan mengujinya dengan menggunakan Bot Framework Emulator (yang harus dipasang pada sistem Anda).

1. Di panel terminal, pastikan bahwa direktori saat ini adalah folder **TimeBot** yang berisi file kode bot Anda, lalu masukkan perintah berikut untuk memulai bot Anda berjalan secara lokal.

**C#**

```C#
dotnet run
```

**Python**

```Python
python app.py
```
    
Saat bot dimulai, perhatikan titik akhir saat bot berjalan ditampilkan. Ini harus mirip dengan **http://localhost:3978** .

2. Mulai Emulator Kerangka Bot, dan buka bot Anda dengan menentukan titik akhir dengan menambahkan jalur **/api/messages**, seperti ini:

    `http://localhost:3978/api/messages`

3. Setelah percakapan dibuka di panel **Obrolan langsung**, tunggu pesan *Halo dan selamat datang!* .
4. Masukkan pesan seperti *Halo* dan lihat respons dari bot, yang seharusnya menggemakan kembali pesan yang Anda masukkan.
5. Tutup Bot Framework Emulator dan kembali ke Visual Studio Code, lalu di jendela terminal, masukkan **CTRL+C** untuk menghentikan bot.

## <a name="modify-the-bot-code"></a>Ubah kode bot

Anda telah membuat bot yang menggemakan input pengguna kembali kepada mereka. Ini tidak terlalu berguna, tetapi berfungsi untuk menggambarkan alur dasar dialog percakapan. Percakapan dengan bot terdiri dari rangkaian *aktivitas*, di mana teks, grafik, atau *kartu* antarmuka pengguna digunakan untuk bertukar informasi. Bot memulai percakapan dengan salam, yang merupakan hasil dari aktivitas *pembaruan percakapan* yang dipicu saat pengguna memulai sesi obrolan dengan bot. Kemudian percakapan terdiri dari rangkaian aktivitas lebih lanjut di mana pengguna dan bot secara bergiliran mengirim *pesan*.

1. Di Visual Studio Code, buka file kode berikut untuk bot Anda:
    - **C#** : TimeBot/Bots/EchoBot.cs
    - **Python**: TimeBot/bot.py

    Perhatikan bahwa kode dalam file ini terdiri dari fungsi *pengatur aktivitas*; satu untuk aktivitas pembaruan percakapan *Ditambahkan Anggota* (saat seseorang bergabung dengan sesi obrolan) dan satu lagi untuk aktivitas *Pesan* (saat pesan diterima). Percakapan didasarkan pada konsep *putaran*, di mana setiap giliran mewakili interaksi di mana bot menerima, memproses, dan merespons suatu aktivitas. *Konteks giliran* digunakan untuk melacak informasi tentang aktivitas yang sedang diproses di giliran saat ini.

2. Di bagian atas file kode, tambahkan pernyataan impor namespace berikut:

**C#**

```C#
using System;
```

**Python**

```Python
from datetime import datetime
```

3. Ubah fungsi pengendali aktivitas untuk aktivitas *Pesan* agar sesuai dengan kode berikut:

**C#**

```C#
protected override async Task OnMessageActivityAsync(ITurnContext<IMessageActivity> turnContext, CancellationToken cancellationToken)
{
    string inputMessage = turnContext.Activity.Text;
    string responseMessage = "Ask me what the time is.";
    if (inputMessage.ToLower().StartsWith("what") && inputMessage.ToLower().Contains("time"))
    {
        var now = DateTime.Now;
        responseMessage = "The time is " + now.Hour.ToString() + ":" + now.Minute.ToString("D2");
    }
    await turnContext.SendActivityAsync(MessageFactory.Text(responseMessage, responseMessage), cancellationToken);
}
```

**Python**

```Python
async def on_message_activity(self, turn_context: TurnContext):
    input_message = turn_context.activity.text
    response_message = 'Ask me what the time is.'
    if (input_message.lower().startswith('what') and 'time' in input_message.lower()):
        now = datetime.now()
        response_message = 'The time is {}:{:02d}.'.format(now.hour,now.minute)
    await turn_context.send_activity(response_message)
```
    
4. Simpan perubahan Anda, lalu di panel terminal, pastikan bahwa direktori saat ini adalah folder **TimeBot** yang berisi file kode bot Anda, lalu masukkan perintah berikut untuk memulai bot Anda berjalan secara lokal.

**C#**

```C#
dotnet run
```

**Python**

```Python
python app.py
```

Seperti sebelumnya, saat bot dimulai, perhatikan titik akhir saat bot berjalan ditampilkan.

5. Mulai Emulator Kerangka Bot, dan buka bot Anda dengan menentukan titik akhir dengan menambahkan jalur **/api/messages**, seperti ini:

    `http://localhost:3978/api/messages`

6. Setelah percakapan dibuka di panel **Obrolan langsung**, tunggu pesan *Halo dan selamat datang!* .
7. Masukkan pesan seperti *Halo* dan lihat tanggapan dari bot, yang seharusnya *menayakan jam berapa sekarang*.
8. Masukkan *Jam berapa?* dan lihat tanggapannya.

    Bot sekarang menanggapi pertanyaan "Jam berapa?" dengan menampilkan waktu lokal di mana bot berjalan. Untuk kueri lainnya, ini meminta pengguna untuk menanyakan jam berapa sekarang. Ini adalah bot yang sangat terbatas, yang dapat ditingkatkan melalui integrasi dengan layanan LUIS dan kode kustom tambahan, tetapi ini berfungsi sebagai contoh kerja bagaimana Anda dapat membangun solusi dengan Bot Framework SDK dengan memperluas bot yang dibuat dari template.

9. Tutup Bot Framework Emulator dan kembali ke Visual Studio Code, lalu di jendela terminal, masukkan **CTRL+C** untuk menghentikan bot.

## <a name="more-information"></a>Informasi selengkapnya

Untuk mempelajari lebih lanjut tentang Kerangka Bot, lihat [dokumentasi Kerangka Kerja Bot](https://docs.microsoft.com/azure/bot-service/index-bf-sdk).
