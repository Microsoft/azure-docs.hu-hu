---
title: Adatok küldése a gyors, a lassú és az archív blob-rétegnek Azure Data Box/Azure Data Box Heavy használatával
description: Ismerteti, hogyan lehet Azure Data Box vagy Azure Data Box Heavy használatával elküldeni az adatok egy megfelelő blokk blob Storage-rétegbe, például a gyors, a hideg vagy az archív
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: how-to
ms.date: 05/24/2019
ms.author: alkohli
ms.openlocfilehash: 80c4d8a70454c007ac45f588e59c03ef45f10933
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92125064"
---
# <a name="use-azure-data-box-or-azure-data-box-heavy-to-send-data-to-appropriate-azure-storage-blob-tier"></a>Az adatküldés Azure Data Box vagy Azure Data Box Heavy használatával a megfelelő Azure Storage blob-szintjére

A Azure Data Box nagy mennyiségű adatmennyiséget helyez át az Azure-ba a védett tárolóeszköz szállításával. Kitölti az eszközt, és visszaküldi azt. A Data Boxból származó adatok feltöltése a Storage-fiókhoz társított alapértelmezett szinten történik. Ezután áthelyezheti az adatátvitelt egy másik tárolási szintjére.

Ez a cikk azt ismerteti, hogyan helyezhetők át a Data Box által feltöltött adatok a gyors, a hideg vagy az archív blob szintjére. Ez a cikk a Azure Data Box és a Azure Data Box Heavy is vonatkozik.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="choose-the-correct-storage-tier-for-your-data"></a>Válassza ki az adatok megfelelő tárolási szintjét

Az Azure Storage lehetővé teszi, hogy az adatok a leghatékonyabb módon, a gyakori, a ritka és az archív formában tárolhatók. A gyakori tárolási szint a gyakran használt adatok tárolására van optimalizálva. A gyakori tárolás nagyobb tárolási költséggel jár, mint a ritka elérésű és az archív tárolás, hanem a legalacsonyabb hozzáférési költségek.

A ritka elérésű tárolási szint a ritkán használt adatokra vonatkozik, amelyeket legalább 30 napig kell tárolni. A hideg réteg tárolási költségei alacsonyabbak, mint a gyakori tárolási rétegnél, de az adatelérési díjak magasak a gyors szinthez képest.

Az Azure archiválási szintje offline állapotban van, és a legalacsonyabb tárolási költségeket, valamint a legmagasabb hozzáférési költségeket kínálja. Ez a szint az archiválási tárolóban maradó, legalább 180 napos adatmennyiséget jelenti. Az egyes szintek és a díjszabási modell részleteiért lásd [a tárolási rétegek összehasonlítását](../storage/blobs/storage-blob-storage-tiers.md).

A rendszer feltölti a Data Box vagy Data Box Heavy adatait a Storage-fiókhoz társított tárolási szintjére. A Storage-fiók létrehozásakor megadhatja a hozzáférési szintet gyors vagy Hidegként. A számítási feladat és a Cost hozzáférési mintája alapján áthelyezheti ezeket az értékeket az alapértelmezett csomagból egy másik tárolási szintjére.

Az objektum tárolási adatait csak blob Storage vagy általános célú v2 (GPv2) fiókokban lehet elősegíteni. General Purpose v1 (GPv1) fiókok nem támogatják a rétegezést. Az adatokhoz megfelelő tárolási szint kiválasztásához tekintse át az Azure Blob Storage-ban részletesen ismertetett szempontokat [: prémium, gyakori, ritka elérésű és archív tárolási szint](../storage/blobs/storage-blob-storage-tiers.md).

## <a name="set-a-default-blob-tier"></a>Alapértelmezett blob-rétegek beállítása

Az alapértelmezett blob-szintet akkor kell megadni, ha a Storage-fiókot a Azure Portal hozza létre. Miután kiválasztotta a GPv2 vagy blob Storage-ként a tárolási típust, megadhatja a hozzáférési szintek attribútumát. Alapértelmezés szerint a gyors szint van kiválasztva.

A rétegek nem adhatók meg, ha új fiókot próbál létrehozni Data Box vagy Data Box Heavy rendezése során. A fiók létrehozása után módosíthatja a fiókot a portálon az alapértelmezett hozzáférési szintek beállításához.

Azt is megteheti, hogy először létrehoz egy Storage-fiókot a megadott hozzáférési szintű attribútummal. Data Box vagy Data Box Heavy rendelés létrehozásakor válassza ki a meglévő Storage-fiókot. A Storage-fiók létrehozása során az alapértelmezett blob-rétegek beállításával kapcsolatos további információkért nyissa meg a [Storage-fiók létrehozása Azure Portalban](../storage/common/storage-account-create.md?tabs=portal)című témakört.

## <a name="move-data-to-a-non-default-tier"></a>Az adatáthelyezés nem alapértelmezett rétegbe

Ha Data Box eszközről származó adatok feltöltése az alapértelmezett rétegbe történik, előfordulhat, hogy át szeretné helyezni az adatok egy nem alapértelmezett szintjére. Ezeket az értékeket kétféleképpen helyezheti át egy nem alapértelmezett rétegbe.

- Az **Azure Blob Storage életciklus-kezelője** – házirend-alapú megközelítéssel automatikusan elvégezheti az adatréteget, vagy lejárhat az életciklus végén. További információért látogasson el [Az Azure Blob Storage életciklusának kezelésére](../storage/blobs/storage-lifecycle-management-concepts.md).
- **Parancsfájlok** – parancsfájlokkal elválasztott megközelítést használhat Azure PowerShellon keresztül a blob szintű rétegek engedélyezéséhez. A művelet meghívásával `SetBlobTier` beállíthatja a szintet a blobon.

## <a name="use-azure-powershell-to-set-the-blob-tier"></a>A blob-rétegek beállításához használja a Azure PowerShell

A következő lépések azt írják le, hogyan állíthatja be a blob-rétegek archiválását Azure PowerShell parancsfájl használatával.

1. Nyisson meg egy rendszergazda jogú Windows PowerShell-munkamenetet. Győződjön meg arról, hogy a PowerShell 5,0-es vagy újabb verziója fut. Típus:

   `$PSVersionTable.PSVersion`     

2. Jelentkezzen be a Azure PowerShellba. 

   `Login-AzAccount`  

3. Adja meg a Storage-fiók, a hozzáférési kulcs, a tároló és a tárolási környezet változóit.

    ```powershell
    $StorageAccountName = "<enter account name>"
    $StorageAccountKey = "<enter account key>"
    $ContainerName = "<enter container name>"
    $ctx = New-AzStorageContext -StorageAccountName $StorageAccountName -StorageAccountKey $StorageAccountKey
    ```

4. A tároló összes blobjának beolvasása.

    `$blobs = Get-AzStorageBlob -Container "<enter container name>" -Context $ctx`
 
5. Állítsa be a tárolóban lévő Blobok szintjét az archív értékre.

    ```powershell
    Foreach ($blob in $blobs) {
    $blob.ICloudBlob.SetStandardBlobTier("Archive")
    }
    ```

    Alább látható egy minta kimenet:

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
   > Ha azt szeretné, hogy az adatok betöltés közben is archiválva legyenek, állítsa a fiók alapértelmezett rétegét Gyorsra. Ha az alapértelmezett szint nem megfelelő, akkor a rendszer egy 30 napos korai törlési szankciót alkalmaz, ha az adatok azonnal archiválásra kerülnek.

## <a name="next-steps"></a>Következő lépések

-  Ismerje meg, hogyan lehet kezelni a [közös adatfeldolgozási forgatókönyveket életciklus-szabályzatokkal](../storage/blobs/storage-lifecycle-management-concepts.md#examples)