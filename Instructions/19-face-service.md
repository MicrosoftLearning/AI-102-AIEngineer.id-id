---
lab:
  title: Deteksi dan Analisis Wajah
  module: Module 10 - Detecting, Analyzing, and Recognizing Faces
ms.openlocfilehash: 4f7f366d7f2fb221d4fcb89fdb3a4b21c88b915f
ms.sourcegitcommit: 3de3c160e5b2eae51308cacbd0bcd3dbdbe337a5
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 07/19/2022
ms.locfileid: "147375791"
---
# <a name="detect-and-analyze-faces"></a>Deteksi dan Analisis Wajah

Kemampuan untuk mendeteksi dan menganalisis wajah manusia adalah kemampuan inti AI. Dalam latihan ini, Anda akan menjelajahi dua Azure Cognitive Services yang dapat Anda gunakan untuk bekerja dengan wajah dalam gambar: layanan **Computer Vision**, dan layanan **Face**.

> **Catatan**: Mulai 21 Juni 2022, kemampuan layanan kognitif yang mengembalikan informasi identitas pribadi dibatasi untuk pelanggan yang telah diberikan [akses terbatas](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-limited-access). Selain itu, kemampuan yang menyimpulkan keadaan emosional tidak lagi tersedia. Pembatasan ini dapat memengaruhi latihan lab ini. Kami berupaya mengatasi hal ini, tetapi sementara itu Anda mungkin mengalami beberapa kesalahan saat mengikuti langkah-langkah di bawah ini; yang kami minta maaf. Untuk detail selengkapnya tentang perubahan yang telah dilakukan Microsoft, dan mengapa - lihat [Investasi dan perlindungan AI yang bertanggung jawab untuk pengenalan wajah](https://azure.microsoft.com/blog/responsible-ai-investments-and-safeguards-for-facial-recognition/).

## <a name="clone-the-repository-for-this-course"></a>Mengkloning repositori untuk kursus ini

Jika Anda belum melakukannya, Anda harus mengkloning repositori kode untuk kursus ini:

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana pun).
3. Ketika repositori telah dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="provision-a-cognitive-services-resource"></a>Menyediakan sumber daya Cognitive Services

Jika Anda belum memilikinya dalam langganan, Anda harus menyediakan sumber daya **Cognitive Services**.

1. Buka portal Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, jelajahi *layanan kognitif*, dan buat sumber daya **Cognitive Services** dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Wilayah**: *Pilih wilayah yang tersedia*
    - **Nama**: *Masukkan nama unik*
    - **Tingkat harga**: Standar S0
3. Pilih kotak centang yang diperlukan dan buat sumber daya.
4. Tunggu hingga penyebaran selesai, lalu lihat detail penyebaran.
5. Saat sumber daya telah diterapkan, buka dan lihat halaman **Kunci dan Titik Akhir**. Anda akan memerlukan titik akhir dan salah satu kunci dari halaman ini dalam prosedur selanjutnya.

## <a name="prepare-to-use-the-computer-vision-sdk"></a>Bersiaplah untuk menggunakan Computer Vision SDK

Dalam latihan ini, Anda akan menyelesaikan aplikasi klien yang diimplementasikan sebagian yang menggunakan Computer Vision SDK untuk menganalisis wajah dalam sebuah gambar.

> **Catatan**: Anda dapat memilih untuk menggunakan SDK baik untuk **C#** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, ramban ke folder **19-face** dan luaskan folder **C-Sharp** atau **Python** tergantung pada preferensi bahasa Anda.
2. Klik kanan folder **computer-vision** dan buka terminal terintegrasi. Kemudian instal paket Computer Vision SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**

    ```
    dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-vision-computervision==0.7.0
    ```
    
3. Lihat konten folder **computer-vision**, dan perhatikan bahwa folder tersebut berisi file untuk setelan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

4. Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk mencerminkan **titik akhir** dan **kunci** autentikasi untuk sumber daya layanan kognitif Anda. Simpan perubahan Anda.

5. Perhatikan bahwa folder **computer-vision** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: detect-faces.py

6. Buka file kode dan di bagian atas, di bawah referensi namespace yang ada, temukan komentar **Impor namespace**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk mengimpor namespace yang Anda perlukan untuk menggunakan Computer Vision SDK:

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
    from azure.cognitiveservices.vision.computervision.models import VisualFeatureTypes
    from msrest.authentication import CognitiveServicesCredentials
    ```

## <a name="view-the-image-you-will-analyze"></a>Melihat gambar yang akan Anda analisis

Dalam latihan ini, Anda akan menggunakan layanan Computer Vision untuk menganalisis gambar orang.

1. Di Visual Studio Code, perluas folder **computer-vision** dan folder **gambar** yang ada di dalamnya.
2. Pilih gambar **people.jpg** untuk melihatnya.

## <a name="detect-faces-in-an-image"></a>Mendeteksi wajah dalam gambar

Sekarang Anda siap menggunakan SDK untuk memanggil layanan Computer Vision dan mendeteksi wajah dalam gambar.

1. Dalam file kode untuk aplikasi klien Anda (**Program.cs** atau **detect-faces.py**), dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat pengaturan konfigurasi telah disediakan. Kemudian temukan komentar **Autentikasi klien Computer Vision**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk membuat dan mengautentikasi objek klien Computer Vision:

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

2. Dalam fungsi **Utama**, di bawah kode yang baru saja Anda tambahkan, perhatikan bahwa kode menentukan jalur ke file gambar dan kemudian meneruskan jalur gambar ke fungsi bernama **AnalyzeFaces**. Fungsi ini belum sepenuhnya diimplementasikan.

3. Dalam fungsi **AnalyzeFaces**, di bawah komentar **Tentukan fitur yang akan diambil (wajah)** , tambahkan kode berikut:

    **C#**

    ```C#
    // Specify features to be retrieved (faces)
    List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>()
    {
        VisualFeatureTypes.Faces
    };
    ```

    **Python**

    ```Python
    # Specify features to be retrieved (faces)
    features = [VisualFeatureTypes.faces]
    ```

4. Dalam fungsi **AnalyzeFaces**, di bawah komentar **Dapatkan analisis gambar**, tambahkan kode berikut:

**C#**

```C
// Get image analysis
using (var imageData = File.OpenRead(imageFile))
{    
    var analysis = await cvClient.AnalyzeImageInStreamAsync(imageData, features);

    // Get faces
    if (analysis.Faces.Count > 0)
    {
        Console.WriteLine($"{analysis.Faces.Count} faces detected.");

        // Prepare image for drawing
        Image image = Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen pen = new Pen(Color.LightGreen, 3);
        Font font = new Font("Arial", 3);
        SolidBrush brush = new SolidBrush(Color.LightGreen);

        // Draw and annotate each face
        foreach (var face in analysis.Faces)
        {
            var r = face.FaceRectangle;
            Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
            graphics.DrawRectangle(pen, rect);
            string annotation = $"Person at approximately {r.Left}, {r.Top}";
            graphics.DrawString(annotation,font,brush,r.Left, r.Top);
        }

        // Save annotated image
        String output_file = "detected_faces.jpg";
        image.Save(output_file);
        Console.WriteLine(" Results saved in " + output_file);   
    }
}        
```

**Python**

```Python
# Get image analysis
with open(image_file, mode="rb") as image_data:
    analysis = cv_client.analyze_image_in_stream(image_data , features)

    # Get faces
    if analysis.faces:
        print(len(analysis.faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'

        # Draw and annotate each face
        for face in analysis.faces:
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline=color, width=5)
            annotation = 'Person at approximately {}, {}'.format(r.left, r.top)
            plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)

        # Save annotated image
        plt.imshow(image)
        outputfile = 'detected_faces.jpg'
        fig.savefig(outputfile)

        print('Results saved in', outputfile)
```

5. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **computer-vision**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python detect-faces.py
    ```

6. Amati output, yang harus menunjukkan jumlah wajah yang terdeteksi.
7. Lihat file **detected_faces.jpg** yang dihasilkan di folder yang sama dengan file kode Anda untuk melihat wajah yang dianotasi. Dalam hal ini, kode Anda telah menggunakan atribut wajah untuk melabeli lokasi di kiri atas kotak, dan koordinat kotak pembatas untuk menggambar persegi panjang di sekitar setiap wajah.

## <a name="prepare-to-use-the-face-sdk"></a>Bersiap untuk menggunakan Face SDK

Meskipun layanan **Computer Vision** menawarkan deteksi wajah dasar (bersama dengan banyak kemampuan analisis gambar lainnya), layanan **Face** menyediakan fungsionalitas yang lebih komprehensif untuk analisis dan pengenalan wajah.

1. Di Visual Studio Code, di panel **Explorer**, ramban ke folder **19-face** dan luaskan folder **C-Sharp** atau **Python** tergantung pada preferensi bahasa Anda.
2. Klik kanan folder **face-api** dan buka terminal terintegrasi. Kemudian instal paket Face SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

    **C#**

    ```
    dotnet add package Microsoft.Azure.CognitiveServices.Vision.Face --version 2.6.0-preview.1
    ```

    **Python**

    ```
    pip install azure-cognitiveservices-vision-face==0.4.1
    ```
    
3. Lihat konten folder **face-api**, dan perhatikan bahwa folder tersebut berisi file untuk setelan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

4. Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk mencerminkan **titik akhir** dan **kunci** autentikasi untuk sumber daya layanan kognitif Anda. Simpan perubahan Anda.

5. Perhatikan bahwa folder **face-api** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: analyze-faces.py

6. Buka file kode dan di bagian atas, di bawah referensi namespace yang ada, temukan komentar **Impor namespace**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk mengimpor namespace yang Anda perlukan untuk menggunakan Computer Vision SDK:

    **C#**

    ```C#
    // Import namespaces
    using Microsoft.Azure.CognitiveServices.Vision.Face;
    using Microsoft.Azure.CognitiveServices.Vision.Face.Models;
    ```

    **Python**

    ```Python
    # Import namespaces
    from azure.cognitiveservices.vision.face import FaceClient
    from azure.cognitiveservices.vision.face.models import FaceAttributeType
    from msrest.authentication import CognitiveServicesCredentials
    ```

7. Dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat pengaturan konfigurasi telah disediakan. Kemudian temukan komentar **Autentikasi klien Wajah**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk membuat dan mengautentikasi objek **FaceClient** :

    **C#**

    ```C#
    // Authenticate Face client
    ApiKeyServiceClientCredentials credentials = new ApiKeyServiceClientCredentials(cogSvcKey);
    faceClient = new FaceClient(credentials)
    {
        Endpoint = cogSvcEndpoint
    };
    ```

    **Python**

    ```Python
    # Authenticate Face client
    credentials = CognitiveServicesCredentials(cog_key)
    face_client = FaceClient(cog_endpoint, credentials)
    ```

8. Dalam fungsi **Utama**, di bawah kode yang baru saja Anda tambahkan, perhatikan bahwa kode menampilkan menu yang memungkinkan Anda memanggil fungsi dalam kode Anda untuk menjelajahi kemampuan layanan Face. Anda akan menerapkan fungsi-fungsi ini di sisa latihan ini.

## <a name="detect-and-analyze-faces"></a>Deteksi dan analisis wajah

Salah satu kemampuan paling mendasar dari layanan Face adalah mendeteksi wajah dalam gambar, dan menentukan atributnya, seperti pose kepala, samar-samar, keberadaan tontonan, dan sebagainya.

1. Dalam file kode untuk aplikasi Anda, dalam fungsi **Utama**, periksa kode yang berjalan jika pengguna memilih opsi menu **1**. Kode ini memanggil fungsi **DetectFaces**, meneruskan jalur ke file gambar.
2. Temukan fungsi **DetectFaces** dalam file kode, dan di bawah komentar **Tentukan fitur wajah yang akan diambil**, tambahkan kode berikut:

    **C#**

    ```C#
    // Specify facial features to be retrieved
    List<FaceAttributeType?> features = new List<FaceAttributeType?>
    {
        FaceAttributeType.Occlusion,
        FaceAttributeType.Blur,
        FaceAttributeType.Glasses
    };
    ```

    **Python**

    ```Python
    # Specify facial features to be retrieved
    features = [FaceAttributeType.occlusion,
                FaceAttributeType.blur,
                FaceAttributeType.glasses]
    ```

3. Dalam fungsi **DetectFaces**, di bawah kode yang baru saja Anda tambahkan, temukan komentar **Dapatkan wajah** dan tambahkan kode berikut:

**C#**

```C
// Get faces
using (var imageData = File.OpenRead(imageFile))
{    
    var detected_faces = await faceClient.Face.DetectWithStreamAsync(imageData, returnFaceAttributes: features, returnFaceId: false);

    if (detected_faces.Count > 0)
    {
        Console.WriteLine($"{detected_faces.Count} faces detected.");

        // Prepare image for drawing
        Image image = Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen pen = new Pen(Color.LightGreen, 3);
        Font font = new Font("Arial", 4);
        SolidBrush brush = new SolidBrush(Color.Black);
        int faceCount=0;

        // Draw and annotate each face
        foreach (var face in detected_faces)
        {
            faceCount++;
            Console.WriteLine($"\nFace number {faceCount}");
            
            // Get face properties
            Console.WriteLine($" - Mouth Occluded: {face.FaceAttributes.Occlusion.MouthOccluded}");
            Console.WriteLine($" - Eye Occluded: {face.FaceAttributes.Occlusion.EyeOccluded}");
            Console.WriteLine($" - Blur: {face.FaceAttributes.Blur.BlurLevel}");
            Console.WriteLine($" - Glasses: {face.FaceAttributes.Glasses}");

            // Draw and annotate face
            var r = face.FaceRectangle;
            Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
            graphics.DrawRectangle(pen, rect);
            string annotation = $"Face ID: {face.FaceId}";
            graphics.DrawString(annotation,font,brush,r.Left, r.Top);
        }

        // Save annotated image
        String output_file = "detected_faces.jpg";
        image.Save(output_file);
        Console.WriteLine(" Results saved in " + output_file);   
    }
}
```

**Python**

```Python
# Get faces
with open(image_file, mode="rb") as image_data:
    detected_faces = face_client.face.detect_with_stream(image=image_data,
                                                            return_face_attributes=features,                     return_face_id=False)

    if len(detected_faces) > 0:
        print(len(detected_faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'
        face_count = 0

        # Draw and annotate each face
        for face in detected_faces:

            # Get face properties
            face_count += 1
            print('\nFace number {}'.format(face_count))

            detected_attributes = face.face_attributes.as_dict()
            if 'blur' in detected_attributes:
                print(' - Blur:')
                for blur_name in detected_attributes['blur']:
                    print('   - {}: {}'.format(blur_name, detected_attributes['blur'][blur_name]))
                    
            if 'occlusion' in detected_attributes:
                print(' - Occlusion:')
                for occlusion_name in detected_attributes['occlusion']:
                    print('   - {}: {}'.format(occlusion_name, detected_attributes['occlusion'][occlusion_name]))

            if 'glasses' in detected_attributes:
                print(' - Glasses:{}'.format(detected_attributes['glasses']))

            # Draw and annotate face
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline=color, width=5)
            annotation = 'Face ID: {}'.format(face.face_id)
            plt.annotate(annotation,(r.left, r.top), backgroundcolor=color)

        # Save annotated image
        plt.imshow(image)
        outputfile = 'detected_faces.jpg'
        fig.savefig(outputfile)

        print('\nResults saved in', outputfile)
```

4. Periksa kode yang Anda tambahkan ke fungsi **DetectFaces**. Ini menganalisis file gambar dan mendeteksi wajah apa pun yang dikandungnya, termasuk atribut untuk usia, emosi, dan keberadaan tontonan. Detail setiap wajah ditampilkan, termasuk pengidentifikasi wajah unik yang ditetapkan untuk setiap wajah; dan lokasi wajah ditunjukkan pada gambar menggunakan kotak pembatas.
5. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **face-api**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    *Keluaran C# mungkin menampilkan peringatan tentang fungsi asinkron yang sekarang menggunakan operator **menunggu**. Anda dapat mengabaikan ini.*

    **Python**

    ```
    python analyze-faces.py
    ```

6. Saat diminta, masukkan **1** dan amati output, yang harus menyertakan ID dan atribut setiap wajah yang terdeteksi.
7. Lihat file **detected_faces.jpg** yang dihasilkan di folder yang sama dengan file kode Anda untuk melihat wajah yang dianotasi.

## <a name="more-information"></a>Informasi selengkapnya

Ada beberapa fitur tambahan yang tersedia dalam layanan **Face**, tetapi mengikuti [Standar AI yang Bertanggung Jawab](https://aka.ms/aah91ff) yang dibatasi di balik kebijakan Akses Terbatas. Fitur-fitur ini termasuk mengidentifikasi, memverifikasi, dan membuat model pengenalan wajah. Untuk mempelajari selengkapnya dan mengajukan permohonan akses, lihat [Akses Terbatas untuk Cognitive Services](https://docs.microsoft.com/en-us/azure/cognitive-services/cognitive-services-limited-access).

Untuk informasi selengkapnya tentang menggunakan layanan **Computer Vision** untuk deteksi wajah, lihat [dokumentasi Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-detecting-faces).

Untuk mempelajari lebih lanjut tentang layanan **Wajah**, lihat [dokumentasi Wajah](https://docs.microsoft.com/azure/cognitive-services/face/).
