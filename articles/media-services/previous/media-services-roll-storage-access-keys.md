---
title: A Media Services frissítése tárelérési kulcsok váltása után |} A Microsoft Docs
description: Ez a cikk ad útmutatást a Media Services frissítése tárelérési kulcsok váltása után.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.assetid: a892ebb0-0ea0-4fc8-b715-60347cc5c95b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/20/2019
ms.author: milanga;cenkdin;juliako
ms.openlocfilehash: c688169dc21304f234aead7196f377a3fa5fd633
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60407321"
---
# <a name="update-media-services-after-rolling-storage-access-keys"></a>A Media Services frissítése tárelérési kulcsok váltása után 

Amikor létrehoz egy új Azure Media Services (AMS)-fiókot, a rendszer is felkéri Azure Storage-fiókot, amely a médiatartalmak tárolására szolgál. A Media Services-fiók több mint egy storage-fiókok is hozzáadhat. Ez a cikk bemutatja, hogyan tárkulcsok rotálása. Azt is bemutatja, hogyan tárfiókok hozzáadása media-fiók. 

Az ebben a cikkben leírt műveletek végrehajtását, kellene használni [Azure Resource Manager API-k](/rest/api/media/operations/azure-media-services-rest-api-reference) és [Powershell](https://docs.microsoft.com/powershell/module/az.media).  További információkért lásd: [kezelése az Azure PowerShell és a Resource Manager-erőforrások](../../azure-resource-manager/manage-resource-groups-powershell.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Áttekintés

Egy új storage-fiók létrehozásakor a Azure létrehoz két 512 bites tárelérési kulcsot, amelyek segítségével hitelesíti a hozzáférést a tárfiókhoz. Tárolási kapcsolatai biztonságának megőrzése biztonságosabb, hogy rendszeresen generálja újra, és a tárelérési kulcs rotálása ajánlott. Két kulcsot (elsődleges és másodlagos) biztosított, és engedélyezze a storage-fiókba az egyik kulcs, míg más hozzáférési kulcs kapcsolatok fenntartását. Ez az eljárás "működés közbeni hozzáférési kulcsok" is nevezik.

A Media Services megadott tárkulcs függ. A keresők továbbításához vagy letöltéséhez az eszközök használt, a megadott tárelérési kulcs függenek. Az AMS-fiók létrehozásakor, függőséget vesz fel az elsődleges tárelérési kulcs alapértelmezés szerint, de felhasználóként a tárfiók-kulcsot, amely rendelkezik az AMS frissítheti. Győződjön meg arról, hogy a Media Services tudja, melyik kulcsot kell használni, kövesse a cikkben leírtak szerint.  

>[!NOTE]
> Ha több tárfiók, ezeket az eljárásokat minden tárfióknak kell elvégeznie. A sorrendet, amelyben a tárkulcsok rotálása nem meghatározott. Másodlagos kulcs első és az elsődleges kulcs, vagy fordítva fordítva elfordításával.
>
> A termelési fiók ebben a cikkben ismertetett lépések végrehajtása előtt ügyeljen arra, hogy tesztelje le azokat a üzem előtti-fiók.
>

## <a name="steps-to-rotate-storage-keys"></a>Tárkulcsok rotálása lépései 
 
 1. A tárfiók elsődleges hívóbetűjét a powershell-parancsmaggal módosíthatja vagy [Azure](https://portal.azure.com/) portálon.
 2. Hívja meg Sync-AzMediaServiceStorageKeys parancsmagot a megfelelő paraméterei media fiók tárfiókkulcsok csomópontmetrikák kényszerítése
 
    Az alábbi példa bemutatja, hogyan tárfiókokhoz kulcsok szinkronizálása.
  
         Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId
  
 3. Várjon egy órát. Ellenőrizze, hogy a streamelési forgatókönyvek dolgozik.
 4. Módosítsa a tárfiók másodlagos kulcsát a powershell-parancsmag vagy az Azure Portalon keresztül.
 5. Hívja meg az új tárfiókkulcsok csomópontmetrikák media fiók kényszerítése megfelelő paraméterei Sync-AzMediaServiceStorageKeys powershell. 
 6. Várjon egy órát. Ellenőrizze, hogy a streamelési forgatókönyvek dolgozik.
 
### <a name="a-powershell-cmdlet-example"></a>Egy powershell parancsmag-példával 

Az alábbi példa bemutatja, hogyan a storage-fiók és szinkronizálás a az AMS-fiók.

    $regionName = "West US"
    $resourceGroupName = "SkyMedia-USWest-App"
    $mediaAccountName = "sky"
    $storageAccountName = "skystorage"
    $storageAccountId = "/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Storage/storageAccounts/$storageAccountName"

    Sync-AzMediaServiceStorageKeys -ResourceGroupName $resourceGroupName -AccountName $mediaAccountName -StorageAccountId $storageAccountId

 
## <a name="steps-to-add-storage-accounts-to-your-ams-account"></a>Az AMS-fiók a storage-fiókok hozzáadásának lépéseit

A következő cikk bemutatja, hogyan tárfiókok hozzáadása az AMS-fiók: [Több tárfiók csatolása a Media Services-fiók](meda-services-managing-multiple-storage-accounts.md).

## <a name="media-services-learning-paths"></a>Media Services képzési tervek
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Visszajelzés küldése
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

### <a name="acknowledgments"></a>Visszaigazolás
Szeretnénk elfogadja az alábbi személyek, aki hozzájárult felé, hogy ez a dokumentum létrehozása: Cenk Dingiloglu, Milánó Gada, Seva Titov.
