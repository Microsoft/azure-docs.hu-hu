---
title: Media Services v3 frissítése a működés közbeni tárterület-hozzáférési kulcsok után | Microsoft Docs
description: Ez a cikk útmutatást nyújt arról, hogyan frissítheti Media Services v3-t a működés közbeni tárterület-hozzáférési kulcsok után.
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/22/2021
ms.author: inhenkel
ms.openlocfilehash: 76ba1b848b033c5a26e81be18bef23a2b4715a7e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105572398"
---
# <a name="update-media-services-v3-after-rolling-storage-access-keys"></a>Media Services v3 frissítése a működés közbeni tárterület-hozzáférési kulcsok után

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

Új Azure Media Services-(AMS-) fiók létrehozásakor a rendszer megkéri, hogy válasszon ki egy Azure Storage-fiókot.  Több Storage-fiókot is hozzáadhat a Media Services-fiókhoz. Ez a cikk a tárolási kulcsok elforgatását mutatja be. Azt is bemutatja, hogyan adhat hozzá Storage-fiókokat egy adathordozó-fiókhoz.

A cikkben ismertetett műveletek végrehajtásához [Azure Resource Manager API-kat](/rest/api/media/operations/azure-media-services-rest-api-reference) és [PowerShellt](/powershell/module/az.media)kell használnia.  További információ: [Azure-erőforrások kezelése a PowerShell és a Resource Manager használatával](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="storage-access-key-generation"></a>Tárterület-hozzáférési kulcs létrehozása

Új Storage-fiók létrehozásakor az Azure 2 512 bites tárterület-hozzáférési kulcsokat hoz létre, amelyek a Storage-fiókhoz való hozzáférés hitelesítéséhez használhatók. A tárolási kapcsolatok biztonságosabbá tételéhez időnként a Storage-hozzáférési kulcs újralétrehozása és elforgatása. A rendszer két hozzáférési kulcsot (elsődleges és másodlagos) biztosít ahhoz, hogy a másik hozzáférési kulcs újragenerálása közben egyetlen hozzáférési kulccsal kapcsolatot tartson a Storage-fiókkal. Ezt az eljárást "működés közbeni hozzáférési kulcsoknak" is nevezzük.

Media Services a megadott tárolási kulcstól függ. Az eszközök adatfolyamként történő továbbításához vagy letöltéséhez használt lokátorok a megadott tárterület-hozzáférési kulcstól függenek. AMS-fiók létrehozásakor alapértelmezés szerint az elsődleges tárterület-hozzáférési kulcstól függ. Felhasználóként azonban frissítheti az AMS által birtokolt tárolási kulcsot. Media Services tudnia kell, hogy melyik kulcsot kell használnia a következő lépések végrehajtásával:

>[!NOTE]
> Ha több Storage-fiókkal rendelkezik, ezt az eljárást minden egyes Storage-fiókkal végrehajthatja. A tárolási kulcsok elforgatásának sorrendje nincs kijavítva. Először a másodlagos kulcsot, majd az elsődleges kulcsot is elforgathatja, vagy fordítva.
>
> Az éles fiók lépéseinek végrehajtása előtt győződjön meg arról, hogy az üzem előtti fiókon teszteli őket.
>

## <a name="steps-to-rotate-storage-keys"></a>A tárolási kulcsok elforgatásának lépései
 
 1. Módosítsa a Storage-fiók elsődleges kulcsát a PowerShell-parancsmag vagy az [Azure](https://portal.azure.com/) Portal használatával.
 2. Hívja `Sync-AzMediaServiceStorageKeys` meg a parancsmagot a megfelelő paraméterekkel, hogy kényszerítse a Media-fiókot a Storage-fiók kulcsainak kiválasztására
 
    Az alábbi példa bemutatja, hogyan szinkronizálhat kulcsokat a Storage-fiókokhoz.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Várjon egy órát. Ellenőrizze, hogy működik-e a folyamatos átviteli forgatókönyvek.
 4. Módosítsa a Storage-fiók másodlagos kulcsát a PowerShell-parancsmag vagy a Azure Portal használatával.
 5. Hívja `Sync-AzMediaServiceStorageKeys` meg a PowerShellt a megfelelő paraméterekkel, hogy kényszerítse a Media-fiókot az új Storage-fiók kulcsainak kiválasztására.
 6. Várjon egy órát. Ellenőrizze, hogy működik-e a folyamatos átviteli forgatókönyvek.
 
### <a name="a-powershell-cmdlet-example"></a>Példa PowerShell-parancsmagra

Az alábbi példa bemutatja, hogyan kérheti le a Storage-fiókot, és hogyan szinkronizálhatja azt az AMS-fiókkal.

```console
$regionName = "West US"
$resourceGroupName = "SkyMedia-USWest-App"
$mediaAccountName = "sky"
$storageAccountName = "skystorage"
$storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
```

## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Az AMS-fiókhoz tartozó Storage-fiókok hozzáadásának lépései

A következő cikk bemutatja, hogyan adhat hozzá Storage-fiókokat az AMS-fiókhoz: [több Storage-fiók csatolása egy Media Services-fiókhoz](media-services-managing-multiple-storage-accounts.md).
