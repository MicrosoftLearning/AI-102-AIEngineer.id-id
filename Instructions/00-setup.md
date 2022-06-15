---
lab:
  title: Pengaturan Lingkungan Lab
  module: Setup
ms.openlocfilehash: 57363ce9fec94cb3a1a6ef7622a10bca48a6f055
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 11/16/2021
ms.locfileid: "145195694"
---
# <a name="lab-environment-setup"></a>Pengaturan Lingkungan Lab

Latihan-latihan ini dirancang untuk diselesaikan di lingkungan lab yang dihosting. Jika Anda ingin menyelesaikannya di komputer Anda sendiri, Anda dapat melakukannya dengan menginstal perangkat lunak berikut. Anda mungkin mengalami dialog dan perilaku yang tidak terduga saat menggunakan lingkungan Anda sendiri. Karena berbagai kemungkinan konfigurasi lokal, tim kursus tidak dapat mendukung masalah yang mungkin Anda temui di lingkungan Anda sendiri.

> **Catatan**: Petunjuk di bawah ini untuk komputer Windows 10. Anda juga dapat menggunakan Linux atau MacOS. Anda mungkin perlu menyesuaikan instruksi lab untuk OS yang Anda pilih.

### <a name="base-operating-system-windows-10"></a>Sistem Operasi Dasar (Windows 10)

#### <a name="windows-10"></a>Windows 10

Instal Windows 10 dan terapkan semua pembaruan.

#### <a name="edge"></a>Azure Stack Edge

Instal [Edge (Chromium)](https://microsoft.com/edge)

### <a name="net-core-sdk"></a>.NET Core SDK

1. Unduh dan instal dari https://dotnet.microsoft.com/download (unduh .NET Core SDK - bukan hanya runtime bahasa umum)

### <a name="c-redistributable"></a>C++ Redistributable

1. Unduh dan instal Visual C++ Redistributable (x64) dari https://aka.ms/vs/16/release/vc_redist.x64.exe.

### <a name="nodejs"></a>Node.JS

1. Unduh versi LTS terbaru dari https://nodejs.org/en/download/ 
2. Instal menggunakan opsi default

### <a name="python-and-required-packages"></a>Python (dan paket yang diperlukan)

1. Unduh versi 3.8 dari https://docs.conda.io/en/latest/miniconda.html 
2. Jalankan pengaturan untuk menginstal - **Penting**: Pilih opsi untuk menambahkan Miniconda ke variabel PATH dan mendaftarkan Miniconda sebagai lingkungan Python default.
3. Setelah instalasi, buka prompt Anaconda dan masukkan perintah berikut untuk menginstal paket: 

```
pip install flask requests python-dotenv pylint matplotlib pillow
pip install --upgrade numpy
```

### <a name="azure-cli"></a>Azure CLI

1. Unduh dari https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest 
2. Instal menggunakan opsi default

### <a name="git"></a>Git

1. Unduh dan instal dari https://git-scm.com/download.html, menggunakan opsi default


### <a name="visual-studio-code-and-extensions"></a>Visual Studio Code (dan ekstensi)

1. Unduh dari https://code.visualstudio.com/Download 
2. Instal menggunakan opsi default 
3. Setelah instalasi, mulai Visual Studio Code dan pada tab **Extensions** (CTRL+SHIFT+X), cari dan instal ekstensi berikut dari Microsoft:
    - Python
    - C#
    - Azure Functions
    - PowerShell


### <a name="bot-framework-emulator"></a>Emulator Framework Bot

Ikuti petunjuk di https://github.com/Microsoft/BotFramework-Emulator/blob/master/README.md untuk mengunduh dan menginstal versi stabil terbaru dari Bot Framework Emulator untuk sistem operasi Anda.

### <a name="bot-framework-composer"></a>Komposer Framework Bot

Pasang dari https://docs.microsoft.com/en-us/composer/install-composer.
