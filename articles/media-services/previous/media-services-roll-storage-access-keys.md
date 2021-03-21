---
title: Media Services frissítése a működés közbeni tárterület-hozzáférési kulcsok után | Microsoft Docs
description: Ez a cikk útmutatást nyújt arról, hogyan frissítheti Media Services a működés közbeni tárterület-hozzáférési kulcsok után.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/10/2021
ms.author: inhenkel
ms.reviewer: milanga;cenkdin
ms.openlocfilehash: 12732171f774e6ce010f722cde4a27bb298275b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103007923"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>A Media Services frissítése tárelérési kulcsok váltása után

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

Új Azure Media Services-(AMS-) fiók létrehozásakor a rendszer azt is kéri, hogy válasszon ki egy Azure Storage-fiókot, amelyet a médiatartalom tárolására használ. Több Storage-fiókot is hozzáadhat a Media Services-fiókhoz. Ez a cikk a tárolási kulcsok elforgatását mutatja be. Azt is bemutatja, hogyan adhat hozzá Storage-fiókokat egy adathordozó-fiókhoz. 

A cikkben ismertetett műveletek végrehajtásához [Azure Resource Manager API-kat](/rest/api/media/operations/azure-media-services-rest-api-reference) és [PowerShellt](/powershell/module/az.media)kell használnia.  További információ: [Azure-erőforrások kezelése a PowerShell és a Resource Manager használatával](../../azure-resource-manager/management/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

Új Storage-fiók létrehozásakor az Azure 2 512 bites tárterület-hozzáférési kulcsokat hoz létre, amelyek a Storage-fiókhoz való hozzáférés hitelesítéséhez használhatók. A tárolási kapcsolatok biztonságosabbá tételéhez ajánlott a tárterület-hozzáférési kulcs rendszeres újralétrehozása és elforgatása. A rendszer két hozzáférési kulcsot (elsődleges és másodlagos) biztosít ahhoz, hogy a másik hozzáférési kulcs újragenerálása közben egyetlen hozzáférési kulccsal kezelje a Storage-fiók kapcsolatait. Ezt az eljárást "működés közbeni hozzáférési kulcsoknak" is nevezzük.

Media Services a megadott tárolási kulcstól függ. Az eszközök adatfolyamként történő továbbításához vagy letöltéséhez használt lokátorok a megadott tárterület-hozzáférési kulcstól függenek. AMS-fiók létrehozásakor a rendszer alapértelmezés szerint az elsődleges tárterület-hozzáférési kulcstól függ, de felhasználóként frissítheti az AMS által birtokolt tárolási kulcsot. Győződjön meg arról, hogy Media Services tudnia kell, hogy melyik kulcsot kell használni a jelen cikkben ismertetett lépések végrehajtásával.  

>[!NOTE]
> Ha több Storage-fiókkal rendelkezik, ezt az eljárást minden egyes Storage-fiókkal végrehajthatja. A tárolási kulcsok elforgatásának sorrendje nincs kijavítva. Először a másodlagos kulcsot, majd az elsődleges kulcsot is elforgathatja, vagy fordítva.
>
> A cikkben ismertetett lépések végrehajtása előtt éles fiókon ellenőrizze azokat egy üzem előtti fiókon.
>

## <a name="steps-to-rotate-storage-keys"></a>A tárolási kulcsok elforgatásának lépései 
 
 1. Módosítsa a Storage-fiók elsődleges kulcsát a PowerShell-parancsmag vagy az [Azure](https://portal.azure.com/) Portal használatával.
 2. A Storage-fiók kulcsainak kiválasztásához hívja meg Sync-AzMediaServiceStorageKeys parancsmagot a megfelelő paraméterekkel.
 
    Az alábbi példa bemutatja, hogyan szinkronizálhat kulcsokat a Storage-fiókokhoz.
  
    `Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId`
  
 3. Várjon egy órát. Ellenőrizze, hogy működik-e a folyamatos átviteli forgatókönyvek.
 4. Módosítsa a Storage-fiók másodlagos kulcsát a PowerShell-parancsmag vagy a Azure Portal használatával.
 5. Az új Storage-fiók kulcsainak kiválasztásához hívja meg a Sync-AzMediaServiceStorageKeys PowerShellt a megfelelő paraméterekkel. 
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

A következő cikk bemutatja, hogyan adhat hozzá Storage-fiókokat az AMS-fiókhoz: [több Storage-fiók csatolása egy Media Services-fiókhoz](./media-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>A Media Services tanulási útvonalai
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Elismerések
Szeretnénk nyugtázni a következő személyeket, akik hozzájárultak a dokumentum létrehozásához: Cenk Dingiloglu, Milan Gottlieb, Seva Tyitov.
