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
ms.openlocfilehash: 6c65f28c040b15aaa2ec8f3425209351e4b60486
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524469"
---
# <a name="custom-vision-encryption-of-data-at-rest"></a>Custom Vision inaktív adatok titkosítása

Az Azure Custom Vision automatikusan titkosítja az adatait, amikor a felhőben megmaradt. Custom Vision titkosítás védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében.

[!INCLUDE [cognitive-services-about-encryption](../includes/cognitive-services-about-encryption.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok csak az 2020. május 11. után létrehozott erőforrások. Ahhoz, hogy a CMK a Custom Vision használatával használhassa, létre kell hoznia egy új Custom Vision-erőforrást. Az erőforrás létrehozása után a Azure Key Vault használatával beállíthatja a felügyelt identitást.

## <a name="regional-availability"></a>Régiónkénti rendelkezésre állás

Az ügyfél által felügyelt kulcsok jelenleg ezekben a régiókban érhetők el:

* USA déli középső régiója
* USA 2. nyugati régiója
* USA keleti régiója
* USA-beli államigazgatás – Virginia

[!INCLUDE [cognitive-services-cmk](../includes/configure-customer-managed-keys.md)]

## <a name="next-steps"></a>Következő lépések

* A CMK támogató szolgáltatások teljes listájáért lásd: [ügyfél által felügyelt kulcsok Cognitive Services](../encryption/cognitive-services-encryption-keys-portal.md)
* [Mi az Azure Key Vault](../../key-vault/general/overview.md)?
* [Cognitive Services Customer-Managed kulcs kérésének űrlapja](https://aka.ms/cogsvc-cmk)