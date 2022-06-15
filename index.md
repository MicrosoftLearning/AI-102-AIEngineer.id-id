---
title: Petunjuk Host Online
permalink: index.html
layout: home
ms.openlocfilehash: ba3d2a5154e5e9b08e750f7d9ced8b62048dd66d
ms.sourcegitcommit: d6da3bcb25d1cff0edacd759e75b7608a4694f03
ms.translationtype: HT
ms.contentlocale: id-ID
ms.lasthandoff: 11/16/2021
ms.locfileid: "145195668"
---
# <a name="content-directory"></a>Direktori Konten

Repositori ini berisi latihan lab langsung untuk kursus Microsoft [AI-102 Merancang dan Menerapkan Solusi Microsoft Azure AI](https://docs.microsoft.com/learn/certifications/courses/ai-102t00) dan [modul mandiri yang setara di Microsoft Learn](https://aka.ms/AzureLearn_AIEngineer). Latihan ini didesain untuk melengkapi materi pembelajaran dan memungkinkan Anda mempraktikkan teknologi yang dijelaskan.

Untuk menyelesaikan latihan ini, Anda memerlukan langganan Microsoft Azure. Jika instruktur Anda belum menyediakannya, Anda dapat mendaftar untuk coba gratis di [https://azure.microsoft.com](https://azure.microsoft.com).

## <a name="labs"></a>Lab

{% assign labs = site.pages | where_exp:"page", "page.url contains '/Instructions'" %}
| Modul | Laboratorium |
| --- | --- | 
{% for activity in labs  %}| {{ activity.lab.module }} | [{{ activity.lab.title }}{% if activity.lab.type %} - {{ activity.lab.type }}{% endif %}]({{ site.github.url }}{{ activity.url }}) |
{% endfor %}

