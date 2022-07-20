---
lab:
  title: Membuat Bot dengan Bot Framework Composer
  module: Module 7 - Conversational AI and the Azure Bot Service
ms.openlocfilehash: f25609df8d9abc29e691bd83d0470561c9e3e4b0
ms.sourcegitcommit: b934aa694b86756d8b297a384cc6b707f0536e57
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 12/08/2021
ms.locfileid: "145195656"
---
# <a name="create-a-bot-with-bot-framework-composer"></a>Membuat Bot dengan Bot Framework Composer

Bot Framework Composer adalah perancang grafis yang memungkinkan Anda membuat bot percakapan canggih dengan cepat dan mudah tanpa menulis kode. Composer adalah alat sumber terbuka yang menyajikan kanvas visual untuk membangun bot.

## <a name="prepare-to-develop-a-bot"></a>Bersiaplah untuk mengembangkan bot

Mari kita mulai dengan menyiapkan layanan dan alat yang Anda butuhkan untuk mengembangkan bot.

### <a name="get-an-openweather-api-key"></a>Mendapatkan kunci API OpenWeather

Dalam latihan ini, Anda akan membuat bot yang menggunakan layanan OpenWeather untuk mengambil kondisi cuaca kota yang dimasukkan oleh pengguna. Anda akan memerlukan kunci API agar layanan berfungsi.

1. Di browser web, buka situs OpenWeather di `https://openweathermap.org/price`.
2. Minta kunci API gratis, dan buat akun OpenWeather (jika Anda belum memilikinya).
3. Setelah mendaftar, lihat halaman **kunci API** untuk melihat kunci API Anda.

### <a name="update-bot-framework-composer"></a>Perbarui Bot Framework Composer

Anda akan menggunakan Bot Framework Composer untuk membuat bot Anda. Alat ini diperbarui secara berkala, jadi pastikan Anda menginstal versi terbaru.

> **Catatan**: Pembaruan dapat mencakup perubahan pada antarmuka pengguna yang memengaruhi instruksi dalam latihan ini.

1. Buka **Bot Framework Composer**, dan jika Anda tidak otomatis diminta untuk menginstal pembaruan, gunakan opsi **Periksa pembaruan** pada menu **Bantuan** untuk memeriksa pembaruan.
2. Jika pembaruan tersedia, pilih opsi untuk menginstalnya saat aplikasi ditutup. Kemudian tutup Bot Framework Composer dan instal pembaruan untuk pengguna yang saat ini masuk, hidupkan ulang Bot Framework Composer setelah penginstalan selesai. Penginstalan mungkin memerlukan waktu beberapa menit.
3. Pastikan versi Bot Framework Composer adalah **2.0.0** atau yang lebih baru.

## <a name="create-a-bot"></a>Buat bot

Sekarang Anda siap menggunakan Bot Framework Composer untuk membuat bot.

### <a name="create-a-bot-and-customize-the-welcome-dialog-flow"></a>Membuat bot dan menyesuaikan alur dialog "selamat datang"

1. Jalankan Bot Framework Composer jika belum terbuka.
2. Pada layar **Beranda**, pilih **Baru**. Kemudian buat bot kosong baru; beri nama **WeatherBot** dan simpan di folder lokal.
3. Tutup panel **Memulai** jika terbuka, lalu di panel navigasi di sebelah kiri, pilih **Salam** untuk membuka kanvas pembuatan dan menampilkan aktivitas *ConversationUpdate* yang dipanggil saat pengguna pertama kali bergabung dalam percakapan dengan bot. Aktivitas ini terdiri dari alur tindakan.
4. Di panel properti di sebelah kanan, edit judul **Salam** dengan memilih kata **Salam** di bagian atas panel properti di sebelah kanan dan ubah menjadi **WelcomeUsers**.
5. Di kanvas pembuatan, pilih tindakan **Kirim respons**. Kemudian, di panel properti, ubah teks default dari *Hai ke bot Anda* menjadi `Hi! I'm WeatherBot.`
6. Di kanvas penulisan, pilih simbol **+** terakhir (tepat di atas lingkaran yang menandai <u>akhir</u> alur dialog), dan tambahkan tindakan **Ajukan pertanyaan** baru untuk respons **Teks**.

    Tindakan baru membuat dua node dalam alur dialog. Node pertama mendefinisikan prompt untuk bot untuk mengajukan pertanyaan kepada pengguna, dan node kedua mewakili respons yang akan diterima dari pengguna. Di panel properti, node ini memiliki tab **Respons bot** dan **Input pengguna** yang sesuai.

7. Di panel properti, pada tab **Respons bot**, tambahkan respons dengan teks `What's your name?`. Kemudian, pada tab **Input pengguna**, atur nilai **Properti** ke `user.name` untuk menentukan variabel yang dapat Anda akses nanti dalam percakapan bot.
8. Kembali ke kanvas pembuatan, pilih simbol **+** di bawah tindakan **User input(Text)** yang baru saja Anda tambahkan, dan tambahkan tindakan **Kirim respons**.
9. Pilih tindakan **Kirim respons** yang baru ditambahkan dan di panel properti, atur nilai teks ke `Hello ${user.name}, nice to meet you!`.

    Alur aktivitas yang telah selesai akan terlihat seperti ini:

    ![Alur dialog menyambut pengguna dan menanyakan nama mereka](./images/welcomeUsers.png)

### <a name="test-the-bot"></a>Menguji bot

Bot dasar Anda sudah selesai jadi sekarang mari kita uji.

1. Pilih **Mulai Bot** di sudut kanan atas Composer, dan tunggu saat bot Anda dikompilasi dan dimulai. Hal ini mungkin berlangsung selama beberapa menit.

    - Jika pesan Windows Firewall ditampilkan, aktifkan akses untuk semua jaringan.

2. Di panel **Pengelola runtime bot lokal**, pilih **Buka Web Chat**.
3. Di panel obrolan web **WeatherBot**, setelah jeda singkat, Anda akan melihat pesan selamat datang dan permintaan untuk memasukkan nama depan Anda.  Masukkan nama depan Anda dan tekan **Enter**.
4. Bot akan merespons dengan **Halo *nama_anda*, senang bertemu dengan Anda!** .
5. Tutup panel web chat.
6. Di kanan atas Composer, di samping **&#8635; Mulai ulang bot**, klik **<u>=</u>** untuk membuka panel **Pengelola runtime bot lokal**, dan gunakan ikon ⏹ untuk menghentikan bot.

## <a name="add-a-dialog-to-get-the-weather"></a>Menambahkan dialog untuk mendapatkan prakiraan cuaca

Sekarang Anda memiliki bot yang berfungsi, Anda dapat memperluas kemampuannya dengan menambahkan dialog untuk interaksi tertentu. Dalam hal ini, Anda akan menambahkan dialog yang dipicu saat pengguna menyebutkan "cuaca".

### <a name="add-a-dialog"></a>Menambahkan dialog

Pertama, Anda perlu menentukan alur dialog yang akan digunakan untuk menangani pertanyaan tentang cuaca.

1. Di Composer, di panel navigasi, tahan mouse di atas node tingkat atas (**WeatherBot**) dan di menu **...** , pilih **+ Tambahkan dialog**, seperti yang ditunjukkan di sini:

    ![Menambahkan menu Dialog](./images/add-dialog.png)

    Kemudian buat dialog baru bernama **GetWeather** dengan deskripsi **Mendapatkan kondisi cuaca terkini untuk kode pos yang disediakan**.
2. Di panel navigasi, pilih node **BeginDialog** untuk dialog **GetWeather** baru. Kemudian pada kanvas pembuatan, gunakan simbol **+** untuk menambahkan tindakan **Ajukan pertanyaan** untuk respons **Teks**.
3. Di panel properti, pada tab **Respons bot**, tambahkan respons `Enter your city.`
4. Pada tab **Input pengguna**, atur bidang **Properti** ke `dialog.city`, dan atur bidang **Format output** ke ekspresi `=trim(this.value)` untuk menghapus spasi yang berlebihan sekitar nilai yang diberikan pengguna.

    Alur aktivitas sejauh ini akan terlihat seperti ini:

    ![Alur dialog dengan satu tindakan "Kirim respons"](./images/getWeather-dialog-1.png)

    Sejauh ini, dialog meminta pengguna untuk memasukkan kota. Sekarang Anda harus menerapkan logika untuk mengambil informasi cuaca untuk kota yang dimasukkan.

6. Pada kanvas pembuatan, tepat di bawah tindakan **Input pengguna** untuk entri kota, pilih simbol **+** untuk menambahkan tindakan baru.
7. Dari daftar tindakan, pilih **Akses sumber daya eksternal** lalu **Kirim permintaan HTTP**.
8. Atur properti untuk **permintaan HTTP** sebagai berikut, ganti **YOUR_API_KEY** dengan kunci API [OpenWeather](https://openweathermap.org/price) Anda:
    - **Metode HTTP**: GET
    - **Url**: `http://api.openweathermap.org/data/2.5/weather?units=metric&q=${dialog.city}&appid=YOUR_API_KEY`
    - **Properti hasil**: `dialog.api_response`

    Hasilnya dapat mencakup salah satu dari empat properti berikut dari respons HTTP:

    - **statusCode**. Diakses melalui **dialog.api_response.statusCode**.
    - **reasonPhrase**. Diakses melalui **dialog.api_response.reasonPhrase**.
    - **content**. Diakses melalui **dialog.api_response.content**.
    - **headers**. Diakses melalui **dialog.api_response.headers**.

    Selain itu, jika jenis responsnya adalah JSON, respons tersebut akan menjadi objek yang diserialisasi yang tersedia melalui properti **dialog.api_response.content**. Untuk informasi selengkapnya tentang API OpenWeather dan respons yang dikembalikannya, lihat [dokumentasi API OpenWeather](https://openweathermap.org/current).

    Sekarang Anda perlu menambahkan logika ke alur dialog yang menangani respons, yang mungkin menunjukkan keberhasilan atau kegagalan permintaan HTTP.

9. Pada kanvas pembuatan, di bawah tindakan **Kirim Permintaan HTTP** yang Anda buat, tambahkan tindakan **Buat kondisi** > **Branch: if/else**. Tindakan ini menentukan cabang dalam alur dialog dengan jalur **Benar** dan **Salah**.
10. Di **Properti** tindakan cabang, atur bidang **Kondisi** untuk menulis ekspresi berikut:

    ```
    =dialog.api_response.statusCode == 200
    ```

11. Jika panggilan berhasil, Anda perlu menyimpan respons dalam sebuah variabel. Pada kanvas pembuatan, di cabang **True**, tambahkan tindakan **Kelola properti** > **Tetapkan properti**. Kemudian di panel properti, tambahkan penetapan properti berikut:

    | Properti | Nilai |
    | -- | -- |
    | `dialog.weather` | `=dialog.api_response.content.weather[0].description` |
    | `dialog.temp` | `=round(dialog.api_response.content.main.temp)` |
    | `dialog.icon` | `=dialog.api_response.content.weather[0].icon` |

12. Masih di cabang **Benar**, tambahkan tindakan **Kirim respons** di bawah tindakan **Tetapkan properti** dan atur teksnya ke:

    ```
    The weather in ${dialog.city} is ${dialog.weather} and the temperature is ${dialog.temp}&deg;.
    ```

    ***Catatan**: Pesan ini menggunakan properti **dialog.city**, **dialog.weather**, dan **dialog.temp** yang Anda tetapkan di tindakan sebelumnya. Nanti, Anda juga akan menggunakan properti **dialog.icon**.*

13. Anda juga perlu memperhitungkan respons dari layanan cuaca yang bukan 200, jadi di cabang **False**, tambahkan tindakan **Kirim respons** dan atur teksnya ke `I got an error: ${dialog.api_response.content.message}.`

    Alur dialog sekarang akan terlihat seperti ini:

    ![Alur dialog dengan cabang untuk hasil respons HTTP](./images/getWeather-dialog-2.png)

### <a name="add-a-trigger-for-the-dialog"></a>Menambahkan pemicu untuk dialog

Sekarang Anda memerlukan beberapa cara agar dialog baru dimulai dari dialog sambutan yang ada.

1. Di panel navigasi, pilih dialog **WeatherBot** yang berisi **WelcomeUsers** (ini berada di bawah node bot tingkat atas dengan nama yang sama).

    ![Alur kerja WeatherBot yang dipilih](./images/select-workflow.png)

2. Di panel properti untuk dialog **WeatherBot** yang dipilih, di bagian **Pemahaman Bahasa**, atur **Jenis pengenal** ke **Pengenal regex**.

    > Jenis pengenal default menggunakan layanan Pemahaman Bahasa untuk menghasilkan niat pengguna menggunakan model pemahaman bahasa alami. Kami menggunakan pengenal regex untuk menyederhanakan latihan ini. Di aplikasi nyata, Anda harus mempertimbangkan untuk menggunakan Pemahaman Bahasa guna memungkinkan pengenalan niat yang lebih canggih.

3. Di menu **...** untuk dialog **WeatherBot**, pilih **Tambahkan Pemicu baru**.

    ![Tambahkan menu Pemicu](./images/add-trigger.png)

    Kemudian buat pemicu dengan pengaturan berikut:

    - **Apa jenis pemicu ini?** : Niat diakui
    - **Apa nama pemicu ini (RegEx)** : `WeatherRequested`
    - **Masukkan pola regex**: `weather`

    > Teks yang dimasukkan dalam kotak teks pola regex adalah pola regex sederhana yang akan menyebabkan bot mencari kata *cuaca* di setiap pesan masuk.  Jika ada "cuaca", pesan menjadi **niat yang dikenali** dan pemicunya dimulai.

4. Sekarang setelah pemicu dibuat, Anda perlu mengonfigurasi tindakan untuk pemicu tersebut. Di kanvas pembuatan pemicu, pilih simbol **+** di bawah node pemicu **WeatherRequested** baru Anda. Kemudian dalam daftar tindakan, pilih **Manajemen Dialog** dan pilih **Mulai dialog baru**.
5. Dengan memilih tindakan **Mulai dialog baru**, di panel properti, pilih dialog **GetWeather** dari daftar dropdown **Nama dialog** untuk memulai  **Dialog GetWeather** yang Anda tentukan sebelumnya saat pemicu **WeatherRequested** dikenali.

    Alur aktivitas **WeatherRequested** akan terlihat seperti ini:

    ![Pemicu regex memulai dialog GetWeather](./images/weather-regex.png)

6. Mulai ulang bot dan buka panel obrolan web. Kemudian mulai ulang percakapan, dan setelah memasukkan nama Anda, masukkan `What is the weather like?`. Kemudian, saat diminta, masukkan kota, seperti `Seattle`. Bot akan menghubungi layanan dan akan merespons dengan pernyataan laporan cuaca kecil.
7. Setelah Anda selesai menguji, tutup panel web chat dan hentikan bot.

## <a name="handle-interruptions"></a>Menangani gangguan

Bot yang dirancang dengan baik harus memungkinkan pengguna untuk mengubah alur percakapan, misalnya dengan membatalkan permintaan.

1. Di Bot Composer, di panel navigasi, gunakan menu **...** untuk dialog **WeatherBot** guna menambahkan pemicu baru (selain **WelcomeUsers** yang ada dan pemicu **WeatherRequested**). Pemicu baru harus memiliki pengaturan berikut:

    - **Apa jenis pemicu ini?** : Niat diakui
    - **Apa nama pemicu ini (RegEx)** : `CancelRequest`
    - **Masukkan pola regex**: `cancel`

    > Teks yang dimasukkan dalam kotak teks pola regex adalah pola regex sederhana yang akan menyebabkan bot mencari kata *batal* di setiap pesan masuk.

2. Di kanvas pembuatan untuk pemicu, tambahkan tindakan **Kirim respons**, dan atur respons teksnya ke `OK. Whenever you're ready, you can ask me about the weather.`
3. Di bawah tindakan **Kirim respons**, tambahkan tindakan baru untuk mengakhiri dialog dengan memilih **Manajemen dialog** dan **Akhiri dialog ini**.

    Alur dialog **CancelRequest** akan terlihat seperti ini:

    ![Pemicu CancelRequest dengan Kirim respons serta Hentikan tindakan dialog ini](./images/cancel-dialog.png)

    Sekarang setelah Anda memiliki pemicu untuk merespons permintaan pembatalan pengguna, Anda harus mengizinkan interupsi pada aliran dialog tempat pengguna mungkin ingin membuat permintaan seperti itu - seperti ketika dimintai kode pos setelah meminta informasi cuaca.

4. Di panel navigasi, pilih **BeginDialog** di bawah dialog **GetWeather**.
5. Pilih tindakan **Prompt for text** yang meminta pengguna untuk memasukkan kota mereka.
6. Di properti untuk tindakan, pada tab **Lainnya**, luaskan **Konfigurasi Prompt** dan atur properti **Izinkan Interupsi** ke **TRUE**.
7. Mulai ulang bot dan buka panel web chat. Mulai ulang percakapan, dan setelah memasukkan nama Anda, masukkan `What is the weather like?`. Kemudian, saat diminta, masukkan `cancel`, dan konfirmasikan bahwa permintaan dibatalkan.
8. Setelah membatalkan permintaan, masukkan `What's the weather like?` dan perhatikan bahwa pemicu yang sesuai memulai instans baru dari dialog **GetWeather**, yang meminta Anda sekali lagi untuk memasukkan kota.
9. Setelah Anda selesai menguji, tutup panel web chat dan hentikan bot.

## <a name="enhance-the-user-experience"></a>Meningkatkan pengalaman pengguna

Interaksi dengan bot cuaca sejauh ini melalui teks.  Pengguna memasukkan teks untuk niat mereka dan bot merespons dengan teks. Meskipun teks sering kali merupakan cara yang cocok untuk berkomunikasi, Anda dapat meningkatkan pengalaman melalui bentuk elemen antarmuka pengguna lainnya.  Misalnya, Anda dapat menggunakan tombol untuk memulai tindakan yang disarankan, atau menampilkan *kartu* untuk menyajikan informasi secara visual.

### <a name="add-a-button"></a>Menambahkan tombol

1. Di Bot Framework Composer, di panel navigasi, di bawah tindakan **GetWeather**, pilih **BeginDialog**.
2. Di kanvas pembuatan, pilih tindakan **Permintaan untuk teks** yang berisi perintah untuk kota.
3. Di panel properti, pilih **Tampilkan kode**, dan ganti kode yang ada dengan kode berikut.

```
[Activity    
    Text = Enter your city.
    SuggestedActions = Cancel
]
```

Aktivitas ini akan meminta pengguna untuk memasukkan kota mereka seperti sebelumnya, tetapi juga menampilkan tombol **Batal**.

### <a name="add-a-card"></a>Menambahkan kartu

1. Dalam dialog **GetWeather**, di jalur **True** setelah memeriksa respons dari layanan cuaca HTTP, pilih tindakan **Kirim respons** yang menampilkan laporan cuaca.
2. Di panel properti, pilih **Tampilkan kode** dan ganti kode yang ada dengan kode berikut.

```
[ThumbnailCard
    title = Weather for ${dialog.city}
    text = ${dialog.weather} (${dialog.temp}&deg;)
    image = http://openweathermap.org/img/w/${dialog.icon}.png
]
```

Template ini akan menggunakan variabel yang sama seperti sebelumnya untuk kondisi cuaca tetapi juga menambahkan judul ke kartu yang akan ditampilkan, bersama dengan gambar untuk kondisi cuaca.

### <a name="test-the-new-user-interface"></a>Menguji antarmuka pengguna baru

1. Mulai ulang bot dan buka panel web chat. Mulai ulang percakapan, dan setelah memasukkan nama Anda, masukkan `What is the weather like?`. Kemudian, saat diminta, klik tombol **Batal** untuk membatalkan permintaan.
2. Setelah membatalkan, masukkan `Tell me about the weather` dan ketika diminta, masukkan kota, seperti `London`. Bot akan menghubungi layanan dan akan merespons dengan kartu yang menunjukkan kondisi cuaca.
3. Setelah Anda selesai menguji, tutup emulator dan hentikan bot.

## <a name="more-information"></a>Informasi selengkapnya

Untuk mempelajari selengkapnya tentang Bot Framework Composer, lihat [dokumentasi Bot Framework Composer](https://docs.microsoft.com/composer/introduction).
