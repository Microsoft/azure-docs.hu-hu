---
title: Használja az Azure Data Box, az Azure Data Box nehéz is küldhet adatokat a gyakori és ritka elérésű, archív blobszint |} Az adatok a Microsoft Docs
description: Ismerteti, hogyan használható az Azure Data Box- vagy Azure Data Box nehéz adatokat küldeni egy megfelelő block blob storage-rétegét például a gyakori és ritka elérésű vagy archív tárolási szint
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: article
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: ea208c395e2ef69ce8f28052351643e963cceb05
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66427870"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Azure Data Box- vagy Azure Data Box nehéz segítségével adatokat küldeni a megfelelő Azure Storage blob szintjének

Az Azure Data Box helyezi át, elküldünk Önnek egy saját fejlesztésű tárolóeszköz által nagy mennyiségű adat Azure-bA. Töltse fel az eszközt az adatokat, és küldje vissza. Az adatok Data Box egy társított a tárfiók alapértelmezett szintje van feltöltve. Ezután áthelyezheti az adatokat egy új blobtárolási réteg.

Ez a cikk bemutatja, hogyan helyezhetők az adatok Data Box által feltöltött egy gyors Elérésűre, ritka elérésű vagy archív blobszint. Ez a cikk az Azure Data Box és az Azure Data Box nehéz vonatkozik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Válassza ki a megfelelő tárolási réteget az adatok

Az Azure storage lehetővé teszi, hogy a három különböző rétegek tárolja az adatait a legköltséghatékonyabb módon – a gyakori és ritka elérésű vagy archív tárolási. Gyakori elérésű tárolási szinten gyakran használt adatok tárolására van optimalizálva. Gyakori elérésű tárolási rendelkezik, mint a ritka elérésű és archív tárolási, de a legalacsonyabb a hozzáférési költsége magasabb tárolási költségek.

Ritka elérésű tárolási szint olyan ritkán használt adatokhoz, amely minimum 30 napig kell tárolni. A ritkán használt szint tárolási költsége alacsonyabb, mint a gyakori elérésű tárolási szinten, de az adathozzáférési díjakat magas, gyakori elérésű szinthez képest.

Az Azure archív rétegét offline állapotban, és a legalacsonyabb tárolási költségeket, de a legmagasabb hozzáférési költségekkel is kínál. Ez a szint legalább 180 napos archív tárolás maradó adatokra tervezték. Az egyes szolgáltatásszintek és a díjszabási modell részletekért nyissa meg [a tárolási Rétegek összehasonlítása](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

A Data Box az adatokat, vagy a Data Box nehéz a tárfiók társított tárolási szintek van feltöltve. Storage-fiók létrehozásakor megadhatja a hozzáférési szint gyors Elérésűre vagy ritka elérésű. Függően a hozzáférési mintájában, a számítási feladatok és a költség, áthelyezheti az adatokat az alapértelmezett szint egy új blobtárolási réteg.

Csak akkor előfordulhat, hogy szolgáltatásiréteg a objektum tárolási adataihoz a Blob storage- vagy általános célú v2 (GPv2-) fiókok. General Purpose v1 (GPv1) fiókok nem támogatják a rétegezést. Válassza ki a megfelelő tárolási réteget az adatok, tekintse át a részletes leírást talál az szempontok [Azure Blob storage: Prémium szintű, gyors Elérésűre, ritka elérésű és archív tárolási szintek](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers).

## <a name="set-a-default-blob-tier"></a>Egy alapértelmezett blobszint beállítása

Az alapértelmezett blob szint van megadva, a storage-fiók létrehozásakor az Azure Portalon. Miután a GPv2- vagy Blob storage egy tárolási mód van kiválasztva, majd a hozzáférési réteg attribútum adható meg. Alapértelmezés szerint a gyakori elérésű szint van kiválasztva.

A rétegek nem lehet megadva, ha a kívánt új fiók létrehozása a Data Box vagy a Data Box nehéz záradékkal. A fiók létrehozása után módosíthatja a fiók alapértelmezett hozzáférési szint beállítása a portálon.

Azt is megteheti, hogy egy tárfiók létrehozásához először a megadott hozzáférési réteg attribútum. A Data Box vagy a Data Box nehéz sorrend létrehozásakor válassza ki a meglévő tárfiókot. További információk a storage-fiók létrehozása során az alapértelmezett blobszint beállítása, [storage-fiók létrehozása az Azure Portalon](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=portal).

## <a name="move-data-to-a-non-default-tier"></a>Adatok áthelyezése egy nem alapértelmezett szintre

Az adatok Data Box-eszköz az alapértelmezett szint a feltöltését követően érdemes helyezze át az adatokat egy nem alapértelmezett réteget. Kétféleképpen ezeket az adatokat áthelyezni egy nem alapértelmezett szintre.

- **Az Azure Blob storage életciklus-felügyelet** – a házirend-alapú megközelítést segítségével automatikusan adatok vagy a életciklus végén lejár. További információért ugorjon [kezelése az Azure Blob storage életciklus](https://docs.microsoft.com/azure/storage/common/storage-lifecycle-managment-concepts).
- **Parancsfájl-kezelési** -engedélyezéséhez a blobszintű rétegezés Azure Powershellen keresztül a parancsfájlalapú megközelítést használhat. Hívása a `SetBlobTier` műveletet a szint beállítása a blobot.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>A blob szintjének beállítása az Azure PowerShell használatával

Következő lépések bemutatják, hogyan állíthatja a blob szintjének archiválása az Azure PowerShell-parancsfájl használatával.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet. Győződjön meg arról, hogy a futó PowerShell 5.0-s vagy újabb. Típus:

   `$PSVersionTable.PSVersion`     

2. Jelentkezzen be az Azure PowerShell-lel. 

   `Login-AzAccount`  

3. A storage-fiók, hozzáférési kulcsot, tároló és a tárolási környezet változókat határozhat meg.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. Kérje le a tároló összes blobjának.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Az archív tárolóban lévő összes BLOB szintjének megadása

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Alább látható egy mintakimenet:

    ```
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.
    PS C:\WINDOWS\system32> $PSVersionTable.PSVersion

    Major  Minor  Build  Revision
    -----  -----  -----  --------
    5      1      17763  134
    PS C:\WINDOWS\system32> Login-AzAccount

    Account          : gus@contoso.com
    SubscriptionName : MySubscription
    SubscriptionId   : subscription-id
    TenantId         : tenant-id
    Environment      : AzureCloud

    PS C:\WINDOWS\system32> $StorageAccountName = "mygpv2storacct"
    PS C:\WINDOWS\system32> $StorageAccountKey = "mystorageacctkey"
    PS C:\WINDOWS\system32> $ContainerName = "test"
    PS C:\WINDOWS\system32> $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    PS C:\WINDOWS\system32> $blobs = Get-AzStorageBlob -Container "test" -Context $ctx
    PS C:\WINDOWS\system32> Foreach ($blob in $blobs) {
    >> $blob.ICloudBlob.SetStandardBlobTier("Archive")
    >> }
    PS C:\WINDOWS\system32>
    ```
   > [!TIP]
   > Ha azt szeretné, hogy az adatok archiválása a betöltési, állítsa be alapértelmezett szintje a gyakran használt adatok. Az alapértelmezett szint a ritkán használt adatok, majd van-e a 30 napos korai törlési napján belül pótdíj Ha archív azonnal áthelyezi az adatokat.

## <a name="next-steps"></a>További lépések

-  Ismerje meg, hogyan címre a [adatok rétegezési az általános forgatókönyveinek életciklus-szabályzat előírásainak](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts#examples)

