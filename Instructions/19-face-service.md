---
lab:
  title: Deteksi, Analisa, dan Kenali Wajah
  module: Module 10 - Detecting, Analyzing, and Recognizing Faces
ms.openlocfilehash: 29b0544e4f31f6e85eeba5cd8fb42951ca1334a9
ms.sourcegitcommit: 7191e53bc33cda92e710d957dde4478ee2496660
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 07/09/2022
ms.locfileid: "147041657"
---
# <a name="detect-analyze-and-recognize-faces"></a>Deteksi, Analisa, dan Kenali Wajah

Kemampuan untuk mendeteksi, menganalisis, dan mengenali wajah manusia adalah kemampuan AI inti. Dalam latihan ini, Anda akan menjelajahi dua Azure Cognitive Services yang dapat Anda gunakan untuk bekerja dengan wajah dalam gambar: layanan **Computer Vision**, dan layanan **Face**.

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
            string annotation = $"Person at approximately {face.Left}, {face.Top}";
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
    var detected_faces = await faceClient.Face.DetectWithStreamAsync(imageData, returnFaceAttributes: features);

    if (detected_faces.Count > 0)
    {
        Console.WriteLine($"{detected_faces.Count} faces detected.");

        // Prepare image for drawing
        Image image = Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen pen = new Pen(Color.LightGreen, 3);
        Font font = new Font("Arial", 4);
        SolidBrush brush = new SolidBrush(Color.Black);

        // Draw and annotate each face
        foreach (var face in detected_faces)
        {
            // Get face properties
            Console.WriteLine($"\nFace ID: {face.FaceId}");
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
                                                            return_face_attributes=features)

    if len(detected_faces) > 0:
        print(len(detected_faces), 'faces detected.')

        # Prepare image for drawing
        fig = plt.figure(figsize=(8, 6))
        plt.axis('off')
        image = Image.open(image_file)
        draw = ImageDraw.Draw(image)
        color = 'lightgreen'

        # Draw and annotate each face
        for face in detected_faces:

            # Get face properties
            print('\nFace ID: {}'.format(face.face_id))
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

## <a name="compare-faces"></a>Membandingkan wajah

Tugas umumnya adalah membandingkan wajah, dan menemukan wajah yang serupa. Dalam skenario ini, Anda tidak perlu *mengidentifikasi* orang dalam gambar, cukup tentukan apakah beberapa gambar menunjukkan orang yang *sama* (atau setidaknya seseorang yang terlihat mirip). 

1. Dalam file kode untuk aplikasi Anda, dalam fungsi **Utama**, periksa kode yang berjalan jika pengguna memilih opsi menu **2**. Kode ini memanggil fungsi **CompareFaces**, meneruskan jalur ke dua file gambar (**person1.jpg** dan **people.jpg**).
2. Temukan fungsi **CompareFaces** dalam file kode, dan di bawah kode yang ada yang mencetak pesan ke konsol, tambahkan kode berikut:

**C#**

```C
// Determine if the face in image 1 is also in image 2
DetectedFace image_i_face;
using (var image1Data = File.OpenRead(image1))
{    
    // Get the first face in image 1
    var image1_faces = await faceClient.Face.DetectWithStreamAsync(image1Data);
    if (image1_faces.Count > 0)
    {
        image_i_face = image1_faces[0];
        Image img1 = Image.FromFile(image1);
        Graphics graphics = Graphics.FromImage(img1);
        Pen pen = new Pen(Color.LightGreen, 3);
        var r = image_i_face.FaceRectangle;
        Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
        graphics.DrawRectangle(pen, rect);
        String output_file = "face_to_match.jpg";
        img1.Save(output_file);
        Console.WriteLine(" Results saved in " + output_file); 

        //Get all the faces in image 2
        using (var image2Data = File.OpenRead(image2))
        {    
            var image2Faces = await faceClient.Face.DetectWithStreamAsync(image2Data);

            // Get faces
            if (image2Faces.Count > 0)
            {

                var image2FaceIds = image2Faces.Select(f => f.FaceId).ToList<Guid?>();
                var similarFaces = await faceClient.Face.FindSimilarAsync((Guid)image_i_face.FaceId,faceIds:image2FaceIds);
                var similarFaceIds = similarFaces.Select(f => f.FaceId).ToList<Guid?>();

                // Prepare image for drawing
                Image img2 = Image.FromFile(image2);
                Graphics graphics2 = Graphics.FromImage(img2);
                Pen pen2 = new Pen(Color.LightGreen, 3);
                Font font2 = new Font("Arial", 4);
                SolidBrush brush2 = new SolidBrush(Color.Black);

                // Draw and annotate each face
                foreach (var face in image2Faces)
                {
                    if (similarFaceIds.Contains(face.FaceId))
                    {
                        // Draw and annotate face
                        var r2 = face.FaceRectangle;
                        Rectangle rect2 = new Rectangle(r2.Left, r2.Top, r2.Width, r2.Height);
                        graphics2.DrawRectangle(pen2, rect2);
                        string annotation = "Match!";
                        graphics2.DrawString(annotation,font2,brush2,r2.Left, r2.Top);
                    }
                }

                // Save annotated image
                String output_file2 = "matched_faces.jpg";
                img2.Save(output_file2);
                Console.WriteLine(" Results saved in " + output_file2);   
            }
        }

    }
}
```

**Python**

```Python
# Determine if the face in image 1 is also in image 2
with open(image_1, mode="rb") as image_data:
    # Get the first face in image 1
    image_1_faces = face_client.face.detect_with_stream(image=image_data)
    image_1_face = image_1_faces[0]

    # Highlight the face in the image
    fig = plt.figure(figsize=(8, 6))
    plt.axis('off')
    image = Image.open(image_1)
    draw = ImageDraw.Draw(image)
    color = 'lightgreen'
    r = image_1_face.face_rectangle
    bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
    draw = ImageDraw.Draw(image)
    draw.rectangle(bounding_box, outline=color, width=5)
    plt.imshow(image)
    outputfile = 'face_to_match.jpg'
    fig.savefig(outputfile)

# Get all the faces in image 2
with open(image_2, mode="rb") as image_data:
    image_2_faces = face_client.face.detect_with_stream(image=image_data)
    image_2_face_ids = list(map(lambda face: face.face_id, image_2_faces))

    # Find faces in image 2 that are similar to the one in image 1
    similar_faces = face_client.face.find_similar(face_id=image_1_face.face_id, face_ids=image_2_face_ids)
    similar_face_ids = list(map(lambda face: face.face_id, similar_faces))

    # Prepare image for drawing
    fig = plt.figure(figsize=(8, 6))
    plt.axis('off')
    image = Image.open(image_2)
    draw = ImageDraw.Draw(image)

    # Draw and annotate matching faces
    for face in image_2_faces:
        if face.face_id in similar_face_ids:
            r = face.face_rectangle
            bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
            draw = ImageDraw.Draw(image)
            draw.rectangle(bounding_box, outline='lightgreen', width=10)
            plt.annotate('Match!',(r.left, r.top + r.height + 15), backgroundcolor='white')

    # Save annotated image
    plt.imshow(image)
    outputfile = 'matched_faces.jpg'
    fig.savefig(outputfile)
```

3. Periksa kode yang Anda tambahkan ke fungsi **CompareFaces**. Ini menemukan wajah pertama dalam gambar 1 dan menganotasinya dalam file gambar baru bernama **face_to_match.jpg**. Kemudian ia menemukan semua wajah di gambar 2, dan menggunakan ID wajah mereka untuk menemukan wajah yang mirip dengan gambar 1. Yang serupa diannotasikan dan disimpan dalam gambar baru bernama **matched_faces.jpg**.
4. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **face-api**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    *Keluaran C# mungkin menampilkan peringatan tentang fungsi asinkron yang sekarang menggunakan operator **menunggu**. Anda dapat mengabaikan ini.*

    **Python**

    ```
    python analyze-faces.py
    ```
    
5. Saat diminta, masukkan **2** dan amati output. Kemudian lihat file **face_to_match.jpg** dan **matched_faces.jpg** yang dibuat dalam folder yang sama dengan file kode Anda untuk melihat wajah yang cocok.
6. Edit kode di fungsi **Utama** untuk opsi menu **2** untuk membandingkan **person2.jpg** dengan **people.jpg** lalu jalankan kembali program dan lihat hasilnya.

## <a name="train-a-facial-recognition-model"></a>Melatih model pengenalan wajah

Mungkin ada skenario di mana Anda perlu mempertahankan model orang tertentu yang wajahnya dapat dikenali oleh aplikasi AI. Misalnya, untuk memfasilitasi sistem keamanan biometrik yang menggunakan pengenalan wajah untuk memverifikasi identitas karyawan di gedung yang aman.

1. Dalam file kode untuk aplikasi Anda, dalam fungsi **Utama**, periksa kode yang berjalan jika pengguna memilih opsi menu **3**. Kode ini memanggil fungsi **TrainModel**, meneruskan nama dan ID untuk **PersonGroup** baru yang akan terdaftar di sumber daya layanan kognitif Anda, dan daftar nama karyawan.
2. Di folder **face-api/images**, amati bahwa ada folder dengan nama yang sama dengan karyawan. Setiap folder berisi gambar mutliple karyawan bernama.
3. Temukan fungsi **TrainModel** dalam file kode, dan di bawah kode yang ada yang mencetak pesan ke konsol, tambahkan kode berikut:

**C#**

```C
// Delete group if it already exists
var groups = await faceClient.PersonGroup.ListAsync();
foreach(var group in groups)
{
    if (group.PersonGroupId == groupId)
    {
        await faceClient.PersonGroup.DeleteAsync(groupId);
    }
}

// Create the group
await faceClient.PersonGroup.CreateAsync(groupId, groupName);
Console.WriteLine("Group created!");

// Add each person to the group
Console.Write("Adding people to the group...");
foreach(var personName in imageFolders)
{
    // Add the person
    var person = await faceClient.PersonGroupPerson.CreateAsync(groupId, personName);

    // Add multiple photo's of the person
    string[] images = Directory.GetFiles("images/" + personName);
    foreach(var image in images)
    {
        using (var imageData = File.OpenRead(image))
        { 
            await faceClient.PersonGroupPerson.AddFaceFromStreamAsync(groupId, person.PersonId, imageData);
        }
    }

}

    // Train the model
Console.WriteLine("Training model...");
await faceClient.PersonGroup.TrainAsync(groupId);

// Get the list of people in the group
Console.WriteLine("Facial recognition model trained with the following people:");
var people = await faceClient.PersonGroupPerson.ListAsync(groupId);
foreach(var person in people)
{
    Console.WriteLine($"-{person.Name}");
}
```

**Python**

```Python
# Delete group if it already exists
groups = face_client.person_group.list()
for group in groups:
    if group.person_group_id == group_id:
        face_client.person_group.delete(group_id)

# Create the group
face_client.person_group.create(group_id, group_name)
print ('Group created!')

# Add each person to the group
print('Adding people to the group...')
for person_name in image_folders:
    # Add the person
    person = face_client.person_group_person.create(group_id, person_name)

    # Add multiple photo's of the person
    folder = os.path.join('images', person_name)
    person_pics = os.listdir(folder)
    for pic in person_pics:
        img_path = os.path.join(folder, pic)
        img_stream = open(img_path, "rb")
        face_client.person_group_person.add_face_from_stream(group_id, person.person_id, img_stream)

# Train the model
print('Training model...')
face_client.person_group.train(group_id)

# Get the list of people in the group
print('Facial recognition model trained with the following people:')
people = face_client.person_group_person.list(group_id)
for person in people:
    print('-', person.name)
```

4. Periksa kode yang Anda tambahkan ke fungsi **TrainModel**. Ini melakukan tugas-tugas berikut:
    - Mendapatkan daftar **PersonGroup** yang terdaftar dalam layanan, dan menghapus yang ditentukan jika ada.
    - Membuat grup dengan ID dan nama yang ditentukan.
    - Menambahkan seseorang ke grup untuk setiap nama yang ditentukan, dan menambahkan beberapa gambar setiap orang.
    - Melatih model pengenalan wajah berdasarkan orang bernama dalam grup dan gambar wajah mereka.
    - Mengambil daftar orang bernama dalam grup dan menampilkannya.
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

6. Ketika diminta, masukkan **3** dan amati output, mencatat bahwa **PersonGroup** yang dibuat menyertakan dua orang.

## <a name="recognize-faces"></a>Kenali wajah

Sekarang setelah Anda mendefinisikan **PeopleGroup** dan melatih model pengenalan wajah, Anda dapat menggunakannya untuk mengenali individu bernama dalam gambar.

1. Dalam file kode untuk aplikasi Anda, dalam fungsi **Utama**, periksa kode yang berjalan jika pengguna memilih opsi menu **4**. Kode ini memanggil fungsi **RecognizeFaces**, meneruskan jalur ke file gambar (**people.jpg**) dan ID **PeopleGroup** yang akan digunakan untuk identifikasi wajah.
2. Temukan fungsi **RecognizeFaces** dalam file kode, dan di bawah kode yang ada yang mencetak pesan ke konsol, tambahkan kode berikut:

**C#**

```C
// Detect faces in the image
using (var imageData = File.OpenRead(imageFile))
{    
    var detectedFaces = await faceClient.Face.DetectWithStreamAsync(imageData);

    // Get faces
    if (detectedFaces.Count > 0)
    {
        
        // Get a list of face IDs
        var faceIds = detectedFaces.Select(f => f.FaceId).ToList<Guid?>();

        // Identify the faces in the people group
        var recognizedFaces = await faceClient.Face.IdentifyAsync(faceIds, groupId);

        // Get names for recognized faces
        var faceNames = new Dictionary<Guid?, string>();
        if (recognizedFaces.Count> 0)
        {
            foreach(var face in recognizedFaces)
            {
                var person = await faceClient.PersonGroupPerson.GetAsync(groupId, face.Candidates[0].PersonId);
                Console.WriteLine($"-{person.Name}");
                faceNames.Add(face.FaceId, person.Name);

            }
        }

        
        // Annotate faces in image
        Image image = Image.FromFile(imageFile);
        Graphics graphics = Graphics.FromImage(image);
        Pen penYes = new Pen(Color.LightGreen, 3);
        Pen penNo = new Pen(Color.Magenta, 3);
        Font font = new Font("Arial", 4);
        SolidBrush brush = new SolidBrush(Color.Cyan);
        foreach (var face in detectedFaces)
        {
            var r = face.FaceRectangle;
            Rectangle rect = new Rectangle(r.Left, r.Top, r.Width, r.Height);
            if (faceNames.ContainsKey(face.FaceId))
            {
                // If the face is recognized, annotate in green with the name
                graphics.DrawRectangle(penYes, rect);
                string personName = faceNames[face.FaceId];
                graphics.DrawString(personName,font,brush,r.Left, r.Top);
            }
            else
            {
                // Otherwise, just annotate the unrecognized face in magenta
                graphics.DrawRectangle(penNo, rect);
            }
        }

        // Save annotated image
        String output_file = "recognized_faces.jpg";
        image.Save(output_file);
        Console.WriteLine("Results saved in " + output_file);   
    }
}
```

**Python**

```Python
# Detect faces in the image
with open(image_file, mode="rb") as image_data:

    # Get faces
    detected_faces = face_client.face.detect_with_stream(image=image_data)

    # Get a list of face IDs
    face_ids = list(map(lambda face: face.face_id, detected_faces))

    # Identify the faces in the people group
    recognized_faces = face_client.face.identify(face_ids, group_id)

    # Get names for recognized faces
    face_names = {}
    if len(recognized_faces) > 0:
        print(len(recognized_faces), 'faces recognized.')
        for face in recognized_faces:
            person_name = face_client.person_group_person.get(group_id, face.candidates[0].person_id).name
            print('-', person_name)
            face_names[face.face_id] = person_name

    # Annotate faces in image
    fig = plt.figure(figsize=(8, 6))
    plt.axis('off')
    image = Image.open(image_file)
    draw = ImageDraw.Draw(image)
    for face in detected_faces:
        r = face.face_rectangle
        bounding_box = ((r.left, r.top), (r.left + r.width, r.top + r.height))
        draw = ImageDraw.Draw(image)
        if face.face_id in face_names:
            # If the face is recognized, annotate in green with the name
            draw.rectangle(bounding_box, outline='lightgreen', width=3)
            plt.annotate(face_names[face.face_id],
                        (r.left, r.top + r.height + 15), backgroundcolor='white')
        else:
            # Otherwise, just annotate the unrecognized face in magenta
            draw.rectangle(bounding_box, outline='magenta', width=3)

    # Save annotated image
    plt.imshow(image)
    outputfile = 'recognized_faces.jpg'
    fig.savefig(outputfile)

    print('\nResults saved in', outputfile)
```
    
3. Periksa kode yang Anda tambahkan ke fungsi **RecognizeFaces**. Ini menemukan wajah dalam gambar dan membuat daftar ID mereka. Kemudian menggunakan grup orang untuk mencoba mengidentifikasi wajah dalam daftar ID wajah. Wajah yang dikenali diberi anotasi dengan nama orang yang diidentifikasi, dan hasilnya disimpan dalam **recognized_faces.jpg**.
4. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **face-api**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    *Keluaran C# mungkin menampilkan peringatan tentang fungsi asinkron yang sekarang menggunakan operator **menunggu**. Anda dapat mengabaikan ini.*

    **Python**

    ```
    python analyze-faces.py
    ```

5. Saat diminta, masukkan **4** dan amati hasilnya. Kemudian lihat file **recognized_faces.jpg** yang dihasilkan di folder yang sama dengan file kode Anda untuk melihat orang yang diidentifikasi.
6. Edit kode di fungsi **Utama** untuk opsi menu **4** untuk mengenali wajah di **people2.jpg** lalu jalankan kembali program dan lihat hasilnya. Orang yang sama harus dikenali.

## <a name="verify-a-face"></a>Memverifikasi wajah

Pengenalan wajah sering digunakan untuk verifikasi identitas. Dengan layanan Face, Anda dapat memverifikasi wajah dalam gambar dengan membandingkannya dengan wajah lain, atau dari orang yang terdaftar di **PersonGroup**.

1. Dalam file kode untuk aplikasi Anda, dalam fungsi **Utama**, periksa kode yang berjalan jika pengguna memilih opsi menu **5**. Kode ini memanggil fungsi **VerifyFace**, meneruskan jalur ke file gambar (**person1.jpg**), nama, dan ID **PeopleGroup** yang akan digunakan untuk identifikasi wajah.
2. Temukan fungsi **VerifyFace** dalam file kode, dan di bawah komentar **Dapatkan ID orang tersebut dari grup orang** (di atas kode yang mencetak hasilnya) tambahkan kode berikut:

**C#**

```C
// Get the ID of the person from the people group
var people = await faceClient.PersonGroupPerson.ListAsync(groupId);
foreach(var person in people)
{
    if (person.Name == personName)
    {
        Guid personId = person.PersonId;

        // Get the first face in the image
        using (var imageData = File.OpenRead(personImage))
        {    
            var faces = await faceClient.Face.DetectWithStreamAsync(imageData);
            if (faces.Count > 0)
            {
                Guid faceId = (Guid)faces[0].FaceId;

                //We have a face and an ID. Do they match?
                var verification = await faceClient.Face.VerifyFaceToPersonAsync(faceId, personId, groupId);
                if (verification.IsIdentical)
                {
                    result = "Verified";
                }
            }
        }
    }
}
```

**Python**

```Python
# Get the ID of the person from the people group
people = face_client.person_group_person.list(group_id)
for person in people:
    if person.name == person_name:
        person_id = person.person_id

        # Get the first face in the image
        with open(person_image, mode="rb") as image_data:
            faces = face_client.face.detect_with_stream(image=image_data)
            if len(faces) > 0:
                face_id = faces[0].face_id

                # We have a face and an ID. Do they match?
                verification = face_client.face.verify_face_to_person(face_id, person_id, group_id)
                if verification.is_identical:
                    result = 'Verified'
```

3. Periksa kode yang Anda tambahkan ke fungsi **VerifyFace**. Ini mencari ID orang dalam grup dengan nama yang ditentukan. Jika orang tersebut ada, kode mendapatkan ID wajah pertama dalam gambar. Akhirnya, jika ada wajah dalam gambar, kode memverifikasinya terhadap ID orang yang ditentukan.
4. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **face-api**, dan masukkan perintah berikut untuk menjalankan program:

    **C#**

    ```
    dotnet run
    ```

    **Python**

    ```
    python analyze-faces.py
    ```

5. Saat diminta, masukkan **5** dan amati hasilnya.
6. Edit kode di fungsi **Utama** untuk opsi menu **5** untuk bereksperimen dengan kombinasi nama yang berbeda dan gambar **person1.jpg** dan **person2.jpg**.

## <a name="more-information"></a>Informasi selengkapnya

Untuk informasi selengkapnya tentang menggunakan layanan **Computer Vision** untuk deteksi wajah, lihat [dokumentasi Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/concept-detecting-faces).

Untuk mempelajari lebih lanjut tentang layanan **Wajah**, lihat [dokumentasi Wajah](https://docs.microsoft.com/azure/cognitive-services/face/).
