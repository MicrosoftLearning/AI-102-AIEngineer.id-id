---
lab:
  title: Analisis Gambar dengan Computer Vision
  module: Module 8 - Getting Started with Computer Vision
ms.openlocfilehash: 5b7f15550844e4bc5efbdb3b8ee00d71760f18c9
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 11/16/2021
ms.locfileid: "145195633"
---
# <a name="analyze-images-with-computer-vision"></a>Analisis Gambar dengan Computer Vision

Visi komputer adalah kemampuan kecerdasan buatan yang memungkinkan sistem perangkat lunak untuk menafsirkan input visual dengan menganalisis gambar. Di Microsoft Azure, layanan kognitif **Computer Vision** menyediakan model pra-bangun untuk tugas visi komputer umum, termasuk analisis gambar untuk menyarankan keterangan dan tag, deteksi objek umum, tengara, selebriti, merek, dan keberadaan dari konten dewasa. Anda juga dapat menggunakan layanan Computer Vision untuk menganalisis warna dan format gambar, dan untuk menghasilkan gambar mini yang "dipangkas cerdas".

## <a name="clone-the-repository-for-this-course"></a>Kloning repositori untuk kursus ini

Jika Anda belum mengkloning repositori kode **AI-102-AIEngineer** ke lingkungan tempat Anda bekerja di lab ini, ikuti langkah-langkah berikut untuk melakukannya. Jika tidak, buka folder kloning di Visual Studio Code.

1. Mulai Visual Studio Code.
2. Buka palet (SHIFT+CTRL+P) dan jalankan **Git: Perintah klon** untuk mengkloning repositori `https://github.com/MicrosoftLearning/AI-102-AIEngineer` ke folder lokal (tidak masalah folder mana pun).
3. Ketika repositori telah dikloning, buka folder di Visual Studio Code.
4. Tunggu sementara file tambahan diinstal untuk mendukung proyek kode C# di repositori.

    > **Catatan**: Jika Anda diminta untuk menambahkan aset yang diperlukan untuk membangun dan men-debug, pilih **Tidak Sekarang**.

## <a name="provision-a-cognitive-services-resource"></a>Menyediakan sumber daya Cognitive Services

Jika Anda belum memilikinya dalam langganan, Anda harus menyediakan sumber daya **Cognitive Services**.

1. Buka portal Azure di `https://portal.azure.com`, dan masuk menggunakan akun Microsoft yang terkait dengan langganan Azure Anda.
2. Pilih tombol **&#65291;Buat sumber daya**, cari *Cognitive Services*, dan buat sumber daya **Cognitive Services** dengan pengaturan berikut:
    - **Langganan**: *Langganan Azure Anda*
    - **Grup sumber daya**: *Pilih atau buat grup sumber daya (jika Anda menggunakan langganan terbatas, Anda mungkin tidak memiliki izin untuk membuat grup sumber daya baru - gunakan yang disediakan)*
    - **Wilayah**: *Pilih wilayah yang tersedia*
    - **Nama**: *Masukkan nama yang unik*
    - **Tingkat harga**: Standar S0
3. Pilih kotak centang yang diperlukan dan buat sumber daya.
4. Tunggu hingga penyebaran selesai, lalu lihat detail penyebaran.
5. Saat sumber daya telah diterapkan, buka dan lihat halaman **Kunci dan Titik Akhir**. Anda akan memerlukan titik akhir dan salah satu kunci dari halaman ini dalam prosedur selanjutnya.

## <a name="prepare-to-use-the-computer-vision-sdk"></a>Bersiaplah untuk menggunakan Computer Vision SDK

Dalam latihan ini, Anda akan menyelesaikan aplikasi klien yang diimplementasikan sebagian yang menggunakan Computer Vision SDK untuk menganalisis gambar.

> **Catatan**: Anda dapat memilih untuk menggunakan SDK baik untuk **C#** atau **Python**. Dalam langkah-langkah di bawah ini, lakukan tindakan yang sesuai untuk bahasa pilihan Anda.

1. Di Visual Studio Code, di panel **Explorer**, telusuri ke folder **15-computer-vision** dan luaskan folder **C-Sharp** atau **Python** tergantung pada preferensi bahasa Anda.
2. Klik kanan folder **image-analysis** dan buka terminal terintegrasi. Kemudian pasang paket Computer Vision SDK dengan menjalankan perintah yang sesuai untuk preferensi bahasa Anda:

**C#**

```
dotnet add package Microsoft.Azure.CognitiveServices.Vision.ComputerVision --version 6.0.0
```

**Python**

```
pip install azure-cognitiveservices-vision-computervision==0.7.0
```
    
3. Lihat konten folder **image-analysis**, dan perhatikan bahwa folder tersebut berisi file untuk pengaturan konfigurasi:
    - **C#** : appsettings.json
    - **Python**: .env

    Buka file konfigurasi dan perbarui nilai konfigurasi yang dikandungnya untuk mencerminkan **titik akhir** dan **kunci** autentikasi untuk sumber daya cognitive services Anda. Simpan perubahan Anda.
4. Perhatikan bahwa folder **image-analysis** berisi file kode untuk aplikasi klien:

    - **C#** : Program.cs
    - **Python**: image-analysis.py

    Buka file kode dan di bagian atas, di bawah referensi namespace yang ada, temukan komentar **Impor namespaces**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk mengimpor namespace yang Anda perlukan untuk menggunakan Computer Vision SDK:

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
    
## <a name="view-the-images-you-will-analyze"></a>Lihat gambar yang akan Anda analisis

Dalam latihan ini, Anda akan menggunakan layanan Computer Vision untuk menganalisis banyak gambar.

1. Di Visual Studio Code, luaskan folder **image-analysis** dan folder **gambar** yang ada di dalamnya.
2. Pilih masing-masing file gambar secara bergantian untuk dilihat kemudian di Visual Studio Code.

## <a name="analyze-an-image-to-suggest-a-caption"></a>Analisis gambar untuk menyarankan keterangan

Sekarang Anda siap menggunakan SDK untuk memanggil layanan Computer Vision dan menganalisis gambar.

1. Dalam file kode untuk aplikasi klien Anda (**Program.cs** atau **image-analysis.py**), dalam fungsi **Utama**, perhatikan bahwa kode untuk memuat pengaturan konfigurasi telah disediakan. Kemudian temukan komentar **Autentikasi klien Computer Vision**. Kemudian, di bawah komentar ini, tambahkan kode khusus bahasa berikut untuk membuat dan mengautentikasi objek klien Computer Vision:

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

2. Dalam fungsi **Utama**, di bawah kode yang baru saja Anda tambahkan, perhatikan bahwa kode menentukan jalur ke file gambar dan kemudian meneruskan jalur gambar ke dua fungsi lain (**AnalyzeImage** dan **GetThumbnail**). Fungsi-fungsi ini belum sepenuhnya dilaksanakan.

3. Dalam fungsi **AnalyzeImage**, di bawah komentar **Tentukan fitur yang akan diambil**, tambahkan kode berikut:

**C#**

```C#
// Specify features to be retrieved
List<VisualFeatureTypes?> features = new List<VisualFeatureTypes?>()
{
    VisualFeatureTypes.Description,
    VisualFeatureTypes.Tags,
    VisualFeatureTypes.Categories,
    VisualFeatureTypes.Brands,
    VisualFeatureTypes.Objects,
    VisualFeatureTypes.Adult
};
```

**Python**

```Python
# Specify features to be retrieved
features = [VisualFeatureTypes.description,
            VisualFeatureTypes.tags,
            VisualFeatureTypes.categories,
            VisualFeatureTypes.brands,
            VisualFeatureTypes.objects,
            VisualFeatureTypes.adult]
```
    
4. Dalam fungsi **AnalyzeImage**, di bawah komentar **Dapatkan analisis gambar**, tambahkan kode berikut (termasuk komentar yang menunjukkan di mana Anda akan menambahkan lebih banyak kode nanti.):

**C#**

```C
// Get image analysis
using (var imageData = File.OpenRead(imageFile))
{    
    var analysis = await cvClient.AnalyzeImageInStreamAsync(imageData, features);

    // get image captions
    foreach (var caption in analysis.Description.Captions)
    {
        Console.WriteLine($"Description: {caption.Text} (confidence: {caption.Confidence.ToString("P")})");
    }

    // Get image tags


    // Get image categories


    // Get brands in the image


    // Get objects in the image


    // Get moderation ratings
    

}            
```

**Python**

```Python
# Get image analysis
with open(image_file, mode="rb") as image_data:
    analysis = cv_client.analyze_image_in_stream(image_data , features)

# Get image description
for caption in analysis.description.captions:
    print("Description: '{}' (confidence: {:.2f}%)".format(caption.text, caption.confidence * 100))

# Get image tags


# Get image categories 


# Get brands in the image


# Get objects in the image


# Get moderation ratings

```
    
5. Simpan perubahan Anda dan kembali ke terminal terintegrasi untuk folder **image-analysis**, dan masukkan perintah berikut untuk menjalankan program dengan argumen **images/street.jpg**:

**C#**

```
dotnet run images/street.jpg
```

**Python**

```
python image-analysis.py images/street.jpg
```
    
6. Amati output, yang harus menyertakan keterangan yang disarankan untuk gambar **street.jpg**.
7. Jalankan program lagi, kali ini dengan argumen **images/building.jpg** untuk melihat keterangan yang dihasilkan untuk gambar **building.jpg**.
8. Ulangi langkah sebelumnya untuk membuat keterangan untuk file **images/person.jpg**.

## <a name="get-suggested-tags-for-an-image"></a>Dapatkan tag yang disarankan untuk sebuah gambar

Terkadang berguna untuk mengidentifikasi *tag* relevan yang memberikan petunjuk tentang konten gambar.

1. Dalam fungsi **AnalyzeImage**, di bawah komentar **Dapatkan tag gambar**, tambahkan kode berikut:

**C#**

```C
// Get image tags
if (analysis.Tags.Count > 0)
{
    Console.WriteLine("Tags:");
    foreach (var tag in analysis.Tags)
    {
        Console.WriteLine($" -{tag.Name} (confidence: {tag.Confidence.ToString("P")})");
    }
}
```

**Python**

```Python
# Get image tags
if (len(analysis.tags) > 0):
    print("Tags: ")
    for tag in analysis.tags:
        print(" -'{}' (confidence: {:.2f}%)".format(tag.name, tag.confidence * 100))
```

2. Simpan perubahan Anda dan jalankan program sekali untuk setiap file gambar dalam folder **gambar**, perhatikan bahwa selain keterangan gambar, daftar tag yang disarankan akan ditampilkan.

## <a name="get-image-categories"></a>Mendapatkan kategori gambar

Layanan Computer Vision dapat menyarankan *kategori* untuk gambar, dan dalam setiap kategori dapat mengidentifikasi bangunan terkenal atau selebritas.

1. Dalam fungsi **AnalyzeImage**, di bawah komentar **Dapatkan kategori gambar (termasuk selebriti dan landmark)** , tambahkan kode berikut:

**C#**

```C
// Get image categories (including celebrities and landmarks)
List<LandmarksModel> landmarks = new List<LandmarksModel> {};
List<CelebritiesModel> celebrities = new List<CelebritiesModel> {};
Console.WriteLine("Categories:");
foreach (var category in analysis.Categories)
{
    // Print the category
    Console.WriteLine($" -{category.Name} (confidence: {category.Score.ToString("P")})");

    // Get landmarks in this category
    if (category.Detail?.Landmarks != null)
    {
        foreach (LandmarksModel landmark in category.Detail.Landmarks)
        {
            if (!landmarks.Any(item => item.Name == landmark.Name))
            {
                landmarks.Add(landmark);
            }
        }
    }

    // Get celebrities in this category
    if (category.Detail?.Celebrities != null)
    {
        foreach (CelebritiesModel celebrity in category.Detail.Celebrities)
        {
            if (!celebrities.Any(item => item.Name == celebrity.Name))
            {
                celebrities.Add(celebrity);
            }
        }
    }
}

// If there were landmarks, list them
if (landmarks.Count > 0)
{
    Console.WriteLine("Landmarks:");
    foreach(LandmarksModel landmark in landmarks)
    {
        Console.WriteLine($" -{landmark.Name} (confidence: {landmark.Confidence.ToString("P")})");
    }
}

// If there were celebrities, list them
if (celebrities.Count > 0)
{
    Console.WriteLine("Celebrities:");
    foreach(CelebritiesModel celebrity in celebrities)
    {
        Console.WriteLine($" -{celebrity.Name} (confidence: {celebrity.Confidence.ToString("P")})");
    }
}
```

**Python**

```Python
# Get image categories (including celebrities and landmarks)
if (len(analysis.categories) > 0):
    print("Categories:")
    landmarks = []
    celebrities = []
    for category in analysis.categories:
        # Print the category
        print(" -'{}' (confidence: {:.2f}%)".format(category.name, category.score * 100))
        if category.detail:
            # Get landmarks in this category
            if category.detail.landmarks:
                for landmark in category.detail.landmarks:
                    if landmark not in landmarks:
                        landmarks.append(landmark)

            # Get celebrities in this category
            if category.detail.celebrities:
                for celebrity in category.detail.celebrities:
                    if celebrity not in celebrities:
                        celebrities.append(celebrity)

    # If there were landmarks, list them
    if len(landmarks) > 0:
        print("Landmarks:")
        for landmark in landmarks:
            print(" -'{}' (confidence: {:.2f}%)".format(landmark.name, landmark.confidence * 100))

    # If there were celebrities, list them
    if len(celebrities) > 0:
        print("Celebrities:")
        for celebrity in celebrities:
            print(" -'{}' (confidence: {:.2f}%)".format(celebrity.name, celebrity.confidence * 100))

```
    
2. Simpan perubahan Anda dan jalankan program satu kali untuk setiap file gambar dalam folder **gambar**, dengan mengamati bahwa selain keterangan gambar dan tag, daftar kategori yang disarankan ditampilkan bersama dengan landmark atau selebritas yang dikenali (khususnya pada gambar **building.jpg** dan **person.jpg**).

## <a name="get-brands-in-an-image"></a>Mendapatkan merek dalam gambar

Beberapa merek dapat dikenali secara visual dari logo, bahkan ketika nama merek tidak ditampilkan. Layanan Computer Vision dilatih untuk mengidentifikasi ribuan merek terkenal.

1. Dalam fungsi **AnalyzeImage**, di bawah komentar **Dapatkan merek dalam gambar**, tambahkan kode berikut:

**C#**

```C
// Get brands in the image
if (analysis.Brands.Count > 0)
{
    Console.WriteLine("Brands:");
    foreach (var brand in analysis.Brands)
    {
        Console.WriteLine($" -{brand.Name} (confidence: {brand.Confidence.ToString("P")})");
    }
}
```

**Python**

```Python
# Get brands in the image
if (len(analysis.brands) > 0):
    print("Brands: ")
    for brand in analysis.brands:
        print(" -'{}' (confidence: {:.2f}%)".format(brand.name, brand.confidence * 100))
```
    
2. Simpan perubahan Anda dan jalankan program sekali untuk setiap file gambar di folder **gambar**, mengamati merek apa pun yang diidentifikasi (khususnya, dalam gambar **person.jpg** ).

## <a name="detect-and-locate-objects-in-an-image"></a>Mendeteksi dan menemukan objek dalam gambar

*Deteksi objek* adalah bentuk visi komputer tertentu di mana objek individual dalam gambar diidentifikasi dan lokasinya ditunjukkan oleh kotak pembatas..

1. Dalam fungsi **AnalyzeImage**, di bawah komentar **Dapatkan objek dalam gambar**, tambahkan kode berikut:

**C#**

```C
// Get objects in the image
if (analysis.Objects.Count > 0)
{
    Console.WriteLine("Objects in image:");

    // Prepare image for drawing
    Image image = Image.FromFile(imageFile);
    Graphics graphics = Graphics.FromImage(image);
    Pen pen = new Pen(Color.Cyan, 3);
    Font font = new Font("Arial", 16);
    SolidBrush brush = new SolidBrush(Color.Black);

    foreach (var detectedObject in analysis.Objects)
    {
        // Print object name
        Console.WriteLine($" -{detectedObject.ObjectProperty} (confidence: {detectedObject.Confidence.ToString("P")})");

        // Draw object bounding box
        var r = detectedObject.Rectangle;
        Rectangle rect = new Rectangle(r.X, r.Y, r.W, r.H);
        graphics.DrawRectangle(pen, rect);
        graphics.DrawString(detectedObject.ObjectProperty,font,brush,r.X, r.Y);

    }
    // Save annotated image
    String output_file = "objects.jpg";
    image.Save(output_file);
    Console.WriteLine("  Results saved in " + output_file);   
}
```

**Python**

```Python
# Get objects in the image
if len(analysis.objects) > 0:
    print("Objects in image:")

    # Prepare image for drawing
    fig = plt.figure(figsize=(8, 8))
    plt.axis('off')
    image = Image.open(image_file)
    draw = ImageDraw.Draw(image)
    color = 'cyan'
    for detected_object in analysis.objects:
        # Print object name
        print(" -{} (confidence: {:.2f}%)".format(detected_object.object_property, detected_object.confidence * 100))
        
        # Draw object bounding box
        r = detected_object.rectangle
        bounding_box = ((r.x, r.y), (r.x + r.w, r.y + r.h))
        draw.rectangle(bounding_box, outline=color, width=3)
        plt.annotate(detected_object.object_property,(r.x, r.y), backgroundcolor=color)
    # Save annotated image
    plt.imshow(image)
    outputfile = 'objects.jpg'
    fig.savefig(outputfile)
    print('  Results saved in', outputfile)
```
    
2. Simpan perubahan Anda dan jalankan program sekali untuk setiap file gambar di folder **gambar**, mengamati objek apa pun yang terdeteksi. Setelah setiap kali dijalankan, lihat file **objects.jpg** yang dibuat dalam folder yang sama dengan file kode Anda untuk melihat objek yang dianotasi.

## <a name="get-moderation-ratings-for-an-image"></a>Mendapatkan peringkat moderasi untuk gambar

Beberapa gambar mungkin tidak cocok untuk semua pemirsa, dan Anda mungkin perlu menerapkan beberapa moderasi untuk mengidentifikasi gambar yang bersifat dewasa atau kekerasan.

1. Dalam fungsi **AnalyzeImage**, di bawah komentar **Dapatkan peringkat moderasi**, tambahkan kode berikut:

**C#**

```C
// Get moderation ratings
string ratings = $"Ratings:\n -Adult: {analysis.Adult.IsAdultContent}\n -Racy: {analysis.Adult.IsRacyContent}\n -Gore: {analysis.Adult.IsGoryContent}";
Console.WriteLine(ratings);
```

**Python**

```Python
# Get moderation ratings
ratings = 'Ratings:\n -Adult: {}\n -Racy: {}\n -Gore: {}'.format(analysis.adult.is_adult_content,
                                                                    analysis.adult.is_racy_content,
                                                                    analysis.adult.is_gory_content)
print(ratings)
```
    
2. Simpan perubahan Anda dan jalankan program satu kali untuk setiap file gambar dalam folder **gambar**, dengan mengamati peringkat untuk setiap gambar.

> **Catatan**: Dalam tugas sebelumnya, Anda menggunakan satu metode untuk menganalisis gambar, lalu menambahkan kode secara bertahap untuk mengurai dan menampilkan hasilnya. SDK juga menyediakan metode individual untuk menyarankan keterangan, mengidentifikasi tag, mendeteksi objek, dan sebagainya - artinya Anda dapat menggunakan metode yang paling tepat untuk mengembalikan hanya informasi yang Anda butuhkan, mengurangi ukuran muatan data yang perlu dikembalikan. Lihat dokumentasi [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/cognitiveservices/client/computervision?view=azure-dotnet) atau [dokumentasi Python SDK](https://docs.microsoft.com/python/api/overview/azure/cognitiveservices/computervision?view=azure-python) untuk detail selengkapnya.

## <a name="generate-a-thumbnail-image"></a>Membuat gambar mini

Dalam beberapa kasus, Anda mungkin perlu membuat versi gambar yang lebih kecil bernama *thumbnail*, memotongnya untuk menyertakan subjek visual utama dalam dimensi gambar baru.

1. Dalam file kode Anda, temukan fungsi **GetThumbnail**; dan di bawah komentar **Buat thumbnail**, tambahkan kode berikut:

**C#**

```C
// Generate a thumbnail
using (var imageData = File.OpenRead(imageFile))
{
    // Get thumbnail data
    var thumbnailStream = await cvClient.GenerateThumbnailInStreamAsync(100, 100,imageData, true);

    // Save thumbnail image
    string thumbnailFileName = "thumbnail.png";
    using (Stream thumbnailFile = File.Create(thumbnailFileName))
    {
        thumbnailStream.CopyTo(thumbnailFile);
    }

    Console.WriteLine($"Thumbnail saved in {thumbnailFileName}");
}
```

**Python**

```Python
# Generate a thumbnail
with open(image_file, mode="rb") as image_data:
    # Get thumbnail data
    thumbnail_stream = cv_client.generate_thumbnail_in_stream(100, 100, image_data, True)

# Save thumbnail image
thumbnail_file_name = 'thumbnail.png'
with open(thumbnail_file_name, "wb") as thumbnail_file:
    for chunk in thumbnail_stream:
        thumbnail_file.write(chunk)

print('Thumbnail saved in.', thumbnail_file_name)
```
    
2. Simpan perubahan Anda dan jalankan program sekali untuk setiap file gambar di folder **gambar**, buka file **thumbnail.jpg** yang dibuat dalam folder yang sama dengan file kode Anda untuk setiap gambar.

## <a name="more-information"></a>Informasi selengkapnya

Dalam latihan ini, Anda menjelajahi beberapa kemampuan analisis dan manipulasi gambar dari layanan Computer Vision. Layanan ini juga mencakup kemampuan untuk membaca teks, mendeteksi wajah, dan tugas penglihatan komputer lainnya.

Untuk informasi selengkapnya tentang menggunakan layanan **Computer Vision**, lihat [dokumentasi Computer Vision](https://docs.microsoft.com/azure/cognitive-services/computer-vision/).
