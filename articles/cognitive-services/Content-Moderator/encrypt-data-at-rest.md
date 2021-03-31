---
title: Content Moderator inaktív adatok titkosítása
titleSuffix: Azure Cognitive Services
description: Content Moderator inaktív adatok titkosítása.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 1401108a594e30790e842ec379724603f11d493f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "100524477"
---
# <a name="content-moderator-encryption-of-data-at-rest"></a>Content Moderator inaktív adatok titkosítása

A Content Moderator automatikusan titkosítja az adatait, ha a felhőben marad, így segítve a szervezeti biztonsági és megfelelőségi célok teljesítését.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak a E0 díjszabási szinten érhetők el. Az ügyfél által felügyelt kulcsok használatának kérelmezéséhez töltse ki és küldje el a [Content Moderator Customer-Managed Key Request (kérelem) űrlapot](https://aka.ms/cogsvc-cmk). Körülbelül 3-5 munkanapot vesz igénybe, hogy visszahallgassa a kérés állapotát. Igénytől függően előfordulhat, hogy egy várólistába helyezi, és a rendszer jóváhagyja a helyet, és elérhetővé válik. Miután jóváhagyta a CMK és a Content Moderator szolgáltatás használatát, létre kell hoznia egy új Content Moderator-erőforrást, és a E0 lehetőséget kell választania. Miután létrehozta a Content Moderator-erőforrást a E0 díjszabási szintjével, a Azure Key Vault használatával beállíthatja a felügyelt identitását.

[!INCLUDE [cognitive-services-cmk](../includes/cognitive-services-cmk-regions.md)]

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="enable-data-encryption-for-your-content-moderator-team"></a>Adattitkosítás engedélyezése a Content Moderator csapata számára

A Content Moderator felülvizsgálati csapat adattitkosításának engedélyezéséhez tekintse meg a rövid útmutató [: próbálja ki a Content Moderatort a weben](quick-start.md#create-a-review-team).  

> [!NOTE]
> Meg kell adnia egy erőforrás- _azonosítót_ a Content moderator E0 díjszabási szintjével.

## <a name="next-steps"></a>Következő lépések

* A CMK támogató szolgáltatások teljes listájáért lásd: [ügyfél által felügyelt kulcsok Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Mi az Azure Key Vault](../../key-vault/general/overview.md)?
* [Cognitive Services Customer-Managed kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)