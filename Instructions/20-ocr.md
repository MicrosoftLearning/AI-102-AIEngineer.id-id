---
lab:
  title: Membaca Teks di Gambar
  module: Module 11 - Reading Text in Images and Documents
ms.openlocfilehash: 1199e4e4f44a98fc5f900fa1ad021384b56f0c2b
ms.sourcegitcommit: e242452e8125a2622093980048f1e2cacb8b893d
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 05/25/2022
ms.locfileid: "145757490"
---
# <a name="read-text-in-images"></a>Membaca Teks di Gambar

Pengenalan karakter optik (OCR) adalah bagian dari visi komputer yang berhubungan dengan membaca teks dalam gambar dan dokumen. Layanan **Computer Vision** menyediakan dua API untuk membaca teks, yang akan Anda jelajahi dalam latihan ini.

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda belum melakukannya, Anda harus mengkloning repositori kode untuk kursus ini:

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

## <a name="prepare-to-use-the-computer-vision-sdk"></a>Bersiaplah untuk menggunakan Computer Vision SDK

Dalam latihan ini, Anda akan menyelesaikan aplikasi klien yang diimplementasikan sebagian yang menggunakan Computer Vision SDK untuk membaca teks.

> **Catatan**: Anda dapat memilih untuk menggunakan SDK baik untuk **C#** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, jelajahi folder **20-ocr** dan perluas folder **C-Sharp** atau **Python** tergantung pada preferensi bahasa Anda.
2. Klik kanan folder **read-text** dan buka terminal terintegrasi. Kemudian instal paket Computer Vision SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-computervision==0.7.0
```

3. Lihat konten folder **read-text**, dan perhatikan bahwa folder tersebut berisi file untuk pengaturan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk mencerminkan **titik akhir** dan **kunci** autentikasi untuk sumber daya layanan kognitif Anda. Simpan perubahan Anda.
4. Perhatikan bahwa folder **read-text** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: read-text.py

    Buka file kode dan di bagian atas, di bawah referensi namespace yang ada, temukan komentar **Impor namespace**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk mengimpor ruang nama yang Anda perlukan untuk menggunakan Computer Vision SDK:

**C#**

```C#
// import namespaces
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision;
using Microsoft.Azure.CognitiveServices.Vision.ComputerVision.Models;
```

**Python**

```Python
# import namespaces
from azure.cognitiveservices.vision.computervision import ComputerVisionClient
from azure.cognitiveservices.vision.computervision.models import OperationStatusCodes
from msrest.authentication import CognitiveServicesCredentials
```

5. Dalam file kode untuk aplikasi klien Anda, dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat pengaturan konfigurasi telah disediakan. Kemudian temukan komentar **Autentikasi klien Computer Vision**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk membuat dan mengautentikasi objek klien Computer Vision:

**C#**

```C#
// Authenticate Computer Vision client
ApiKeyServiceClientCredentials credentials = new ApiKeyServiceClientCredentials(cogSvcKey);
cvClient = new ComputerVisionClient(credentials)
{
    Endpoint = cogSvcEndpoint
};
```

**Python**

```Python
# Authenticate Computer Vision client
credential = CognitiveServicesCredentials(cog_key) 
cv_client = ComputerVisionClient(cog_endpoint, credential)
```
    
## <a name="use-the-ocr-api"></a>Menggunakan API OCR

**OCR** API adalah API pengenalan karakter optik yang dioptimalkan untuk membaca teks cetak dalam jumlah kecil hingga sedang dalam *.jpg*, *.png*, *. format gambar gif*, dan *.bmp*. Ini mendukung berbagai bahasa dan selain membaca teks dalam gambar dapat menentukan orientasi setiap wilayah teks dan mengembalikan informasi tentang sudut rotasi teks dalam kaitannya dengan gambar

1. Dalam file kode untuk aplikasi Anda, dalam fungsi **Utama**, periksa kode yang berjalan jika pengguna memilih opsi menu **1**. Kode ini memanggil fungsi **GetTextOcr**, meneruskan jalur ke file gambar.
2. Dalam folder **read-text/images**, buka **Lincoln.jpg** untuk melihat gambar yang akan diproses oleh kode Anda.
3. Kembali ke file kode, temukan fungsi **GetTextOcr**, dan di bawah kode yang ada yang mencetak pesan ke konsol, tambahkan kode berikut:

**C#**

```C#
// Use OCR API to read text in image
using (var imageData = File.OpenRead(imageFile))
{    
    var ocrResults = await cvClient.RecognizePrintedTextInStreamAsync(detectOrientation:false, image:imageData);

    // Prepare image for drawing
    Image image = Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Magenta, 3);

    foreach(var region in ocrResults.Regions)
    {
        foreach(var line in region.Lines)
        {
            // Show the position of the line of text
            int[] dims = line.BoundingBox.Split(",").Select(int.Parse).ToArray();
            Rectangle rect = new Rectangle(dims[0], dims[1], dims[2], dims[3]);
            graphics.DrawRectangle(pen, rect);

            // Read the words in the line of text
            string lineText = "";
            foreach(var word in line.Words)
            {
                lineText += word.Text + " ";
            }
            Console.WriteLine(lineText.Trim());
        }
    }

    // Save the image with the text locations highlighted
    String output_file = "ocr_results.jpg";
    image.Save(output_file);
    Console.WriteLine("Results saved in " + output_file);
}
```

**Python**

```Python
# Use OCR API to read text in image
with open(image_file, mode="rb") as image_data:
    ocr_results = cv_client.recognize_printed_text_in_stream(image_data)

# Prepare image for drawing
fig = plt.figure(figsize=(7, 7))
img = Image.open(image_file)
draw = ImageDraw.Draw(img)

# Process the text line by line
for region in ocr_results.regions:
    for line in region.lines:

        # Show the position of the line of text
        l,t,w,h = list(map(int, line.bounding_box.split(',')))
        draw.rectangle(((l,t), (l+w, t+h)), outline='magenta', width=5)

        # Read the words in the line of text
        line_text = ''
        for word in line.words:
            line_text += word.text + ' '
        print(line_text.rstrip())

# Save the image with the text locations highlighted
plt.axis('off')
plt.imshow(img)
outputfile = 'ocr_results.jpg'
fig.savefig(outputfile)
print('Results saved in', outputfile)
```

4. Periksa kode yang Anda tambahkan ke fungsi **GetTextOcr**. Ini mendeteksi wilayah teks yang dicetak dari file gambar, dan untuk setiap wilayah itu mengekstrak garis teks dan menyoroti posisi di sana pada gambar. Kemudian mengekstrak kata-kata di setiap baris dan mencetaknya.
5. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **baca-teks**, dan masukkan perintah berikut untuk menjalankan program:

**C#**

```
dotnet run
```

*Keluaran C# mungkin menampilkan peringatan tentang fungsi asinkron yang sekarang menggunakan operator **menunggu**. Anda dapat mengabaikan ini.*

**Python**

```
python read-text.py
```

6. Saat diminta, masukkan **1** dan amati hasilnya, yaitu teks yang diekstrak dari gambar.
7. Lihat file **ocr_results.jpg** yang dibuat dalam folder yang sama dengan file kode Anda untuk melihat baris teks beranotasi pada gambar.

## <a name="use-the-read-api"></a>Menggunakan API Baca

API **Baca** menggunakan model pengenalan teks yang lebih baru daripada API OCR, dan berkinerja lebih baik untuk gambar yang lebih besar yang berisi banyak teks. Ini juga mendukung ekstraksi teks dari file *.pdf*, dan dapat mengenali teks cetak dan teks tulisan tangan dalam berbagai bahasa.

**Baca** API menggunakan model operasi asinkron, yang mengirimkan permintaan untuk memulai pengenalan teks; dan ID operasi yang dikembalikan dari permintaan selanjutnya dapat digunakan untuk memeriksa kemajuan dan mengambil hasil.

1. Dalam file kode untuk aplikasi Anda, dalam fungsi **Utama**, periksa kode yang berjalan jika pengguna memilih opsi menu **2**. Kode ini memanggil fungsi **GetTextRead**, meneruskan jalur ke file dokumen PDF.
2. Dalam folder **read-text/images**, klik kanan **Rome.pdf** dan pilih **Reveal in File Explorer**. Kemudian di File Explorer, buka file PDF untuk melihatnya.
3. Kembali ke file kode di Visual Studio Code, temukan fungsi **GetTextRead**, dan di bawah kode yang ada yang mencetak pesan ke konsol, tambahkan kode berikut:

**C#**

```C#
// Use Read API to read text in image
using (var imageData = File.OpenRead(imageFile))
{    
    var readOp = await cvClient.ReadInStreamAsync(imageData);

    // Get the async operation ID so we can check for the results
    string operationLocation = readOp.OperationLocation;
    string operationId = operationLocation.Substring(operationLocation.Length - 36);

    // Wait for the asynchronous operation to complete
    ReadOperationResult results;
    do
    {
        Thread.Sleep(1000);
        results = await cvClient.GetReadResultAsync(Guid.Parse(operationId));
    }
    while ((results.Status == OperationStatusCodes.Running ||
            results.Status == OperationStatusCodes.NotStarted));

    // If the operation was successfuly, process the text line by line
    if (results.Status == OperationStatusCodes.Succeeded)
    {
        var textUrlFileResults = results.AnalyzeResult.ReadResults;
        foreach (ReadResult page in textUrlFileResults)
        {
            foreach (Line line in page.Lines)
            {
                Console.WriteLine(line.Text);
            }
        }
    }
}  
```

**Python**

```Python
# Use Read API to read text in image
with open(image_file, mode="rb") as image_data:
    read_op = cv_client.read_in_stream(image_data, raw=True)

    # Get the async operation ID so we can check for the results
    operation_location = read_op.headers["Operation-Location"]
    operation_id = operation_location.split("/")[-1]

    # Wait for the asynchronous operation to complete
    while True:
        read_results = cv_client.get_read_result(operation_id)
        if read_results.status not in [OperationStatusCodes.running, OperationStatusCodes.not_started]:
            break
        time.sleep(1)

    # If the operation was successfuly, process the text line by line
    if read_results.status == OperationStatusCodes.succeeded:
        for page in read_results.analyze_result.read_results:
            for line in page.lines:
                print(line.text)
```
    
4. Periksa kode yang Anda tambahkan ke fungsi **GetTextRead**. Ini mengajukan permintaan untuk operasi baca, dan kemudian berulang kali memeriksa status hingga operasi selesai. Jika berhasil, kode memproses hasil dengan mengulangi setiap halaman, dan kemudian melalui setiap baris.
5. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **baca-teks**, dan masukkan perintah berikut untuk menjalankan program:

**C#**

```
dotnet run
```

**Python**

```
python read-text.py
```

6. Saat diminta, masukkan **2** dan amati hasilnya, yaitu teks yang diekstrak dari dokumen.

## <a name="read-handwritten-text"></a>Membaca teks tulisan tangan

Selain teks tercetak, API **Baca** dapat mengekstrak teks tulisan tangan dalam bahasa Inggris..

1. Dalam file kode untuk aplikasi Anda, dalam fungsi **Utama**, periksa kode yang berjalan jika pengguna memilih opsi menu **3**. Kode ini memanggil fungsi **GetTextRead**, meneruskan jalur ke file gambar.
2. Dalam folder **read-text/images**, buka **Note.jpg** untuk melihat gambar yang akan diproses oleh kode Anda.
3. Di terminal terintegrasi untuk folder **baca-teks**, dan masukkan perintah berikut untuk menjalankan program:

**C#**

```
dotnet run
```

**Python**

```
python read-text.py
```

4. Saat diminta, masukkan **3** dan amati hasilnya, yaitu teks yang diekstrak dari dokumen.

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang menggunakan layanan **Computer Vision** untuk membaca teks, lihat [dokumentasi Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-recognizing-text).
