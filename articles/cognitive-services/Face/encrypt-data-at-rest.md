---
title: Az inaktív adatok arc-szolgáltatásbeli titkosítása
titleSuffix: Azure Cognitive Services
description: A Microsoft felügyeli a Microsoft által kezelt titkosítási kulcsokat, valamint lehetővé teszi az Cognitive Services-előfizetések kezelését a saját kulcsaival, az úgynevezett ügyfél által felügyelt kulcsokkal (CMK). Ez a cikk az inaktív adatok titkosítását ismerteti a Face, valamint a CMK engedélyezéséhez és kezeléséhez.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: a46253e586aaf90a85bc10e2c62ab6eb238f2ead
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100650813"
---
# <a name="face-service-encryption-of-data-at-rest"></a>Az inaktív adatok arc-szolgáltatásbeli titkosítása

A Face szolgáltatás automatikusan titkosítja az adatait, amikor a felhőben megmarad. Az arc-szolgáltatás titkosítása védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak a E0 díjszabási szinten érhetők el. Ha az ügyfél által felügyelt kulcsok használatára van szüksége, töltse ki és küldje el a [Face Service Customer-Managed a kulcs kérése űrlapot](https://aka.ms/cogsvc-cmk). Körülbelül 3-5 munkanapot vesz igénybe, hogy visszahallgassa a kérés állapotát. Igénytől függően előfordulhat, hogy egy várólistába helyezi, és a rendszer jóváhagyja a helyet, és elérhetővé válik. Miután jóváhagyta a CMK és a Face szolgáltatás használatát, létre kell hoznia egy új arc-erőforrást, és a E0 kell választania az árképzési szinten. Miután létrehozta az arc-erőforrást a E0 díjszabási szintjével, a felügyelt identitás beállításához Azure Key Vault is használhatja.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Következő lépések

* A CMK támogató szolgáltatások teljes listájáért lásd: [ügyfél által felügyelt kulcsok Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Mi az Azure Key Vault](../../key-vault/general/overview.md)?
* [Cognitive Services Customer-Managed kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)
