---
lab:
  title: Mengaktifkan Penyedia Sumber Daya
  module: Setup
ms.openlocfilehash: 2cd48d0d9f67b9bab3e64452bf6199e1f438492a
ms.sourcegitcommit: e06fbeaa3bc15e4dbe99f72216dfeeb27f58babd
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 02/15/2022
ms.locfileid: "145195693"
---
# <a name="enable-resource-providers"></a>Mengaktifkan Penyedia Sumber Daya

Ada beberapa penyedia sumber daya yang harus terdaftar di langganan Azure Anda. Ikuti langkah-langkah ini untuk memastikan bahwa mereka terdaftar.

1. Masuk ke portal Microsoft Azure di `https://portal.azure.com` menggunakan kredensial Microsoft yang terkait dengan langganan Anda.
2. Pada halaman **Beranda**, pilih **Langganan** (atau perluas menu **&#8801;** , pilih **Semua Layanan**, dan pada kategori **Umum**, pilih **Langganan**).
3. Pilih langganan Azure Anda (jika Anda memiliki beberapa langganan, pilih yang Anda buat dengan menukarkan Azure Pass Anda).
4. Di bilah untuk langganan Anda, di panel sebelah kiri, di bagian **Pengaturan**, pilih **Penyedia sumber daya**.
5. Dalam daftar penyedia sumber daya, pastikan penyedia berikut terdaftar (jika tidak, pilih dan klik **daftar**):
    - Microsoft.BotService
    - Microsoft.Web
    - Microsoft.ManagedIdentity
    - Microsoft.Search
    - Microsoft.Storage
    - Microsoft.CognitiveServices
    - Microsoft.AlertsManagement
    - microsoft.insights
    - Microsoft.KeyVault
    - Microsoft.ContainerInstance
