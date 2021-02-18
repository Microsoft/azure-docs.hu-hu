---
title: Custom Vision inaktív adatok titkosítása
titleSuffix: Azure Cognitive Services
description: A Microsoft felügyeli a Microsoft által kezelt titkosítási kulcsokat, valamint lehetővé teszi az Cognitive Services-előfizetések kezelését a saját kulcsaival, az úgynevezett ügyfél által felügyelt kulcsokkal (CMK). Ez a cikk a Custom Vision inaktív adatok titkosítását, valamint a CMK engedélyezését és felügyeletét ismerteti.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: 822a4249b6ed054f36605d0367803da68bab090b
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652258"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision inaktív adatok titkosítása

Az Azure Custom Vision automatikusan titkosítja az adatait, amikor a felhőben megmaradt. Custom Vision titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak az 2020. május 11. után létrehozott erőforrások. Ahhoz, hogy a CMK a Custom Vision használatával használhassa, létre kell hoznia egy új Custom Vision-erőforrást. Az erőforrás létrehozása után a Azure Key Vault használatával beállíthatja a felügyelt identitást.

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Következő lépések

* A CMK támogató szolgáltatások teljes listájáért lásd: [ügyfél által felügyelt kulcsok Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Mi az Azure Key Vault](../../key-vault/general/overview.md)?
* [Cognitive Services Customer-Managed kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)