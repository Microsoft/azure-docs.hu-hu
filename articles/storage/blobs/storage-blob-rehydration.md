---
title: BLOB-adatok rehidratálása az archív szintről
description: Távolítsa el a blobokat az archív tárolóból, hogy hozzáférhessen a blob adataihoz. Archivált blob másolása online szintre.
services: storage
author: twooley
ms.author: twooley
ms.date: 03/11/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: hux
ms.openlocfilehash: aaea21dca5304a7a75b24bd7f974712db38d1815
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276773"
---
# <a name="rehydrate-blob-data-from-the-archive-tier"></a>BLOB-adatok rehidratálása az archív szintről

Míg a blob az archív hozzáférési szinten található, offline állapotba kerül, és nem olvasható és nem módosítható. A blob metaadatai online és elérhető állapotban maradnak, és lehetővé teszik a blob és a hozzá tartozó tulajdonságok listázását. A Blobok beolvasása és módosítása csak online szinteken érhető el, például a gyakori vagy a ritka elérésű. Az archív hozzáférési szinten tárolt adatok lekérdezésére és elérésére két lehetőség áll rendelkezésre.

1. [Archivált Blobok rehidratálása online szintre](#rehydrate-an-archived-blob-to-an-online-tier) – az archív blobot gyors vagy ritka állapotba állíthatja, ha megváltoztatja a rétegét a [blob-réteg beállítása](/rest/api/storageservices/set-blob-tier) művelettel.
2. [Archivált blob másolása online szintre](#copy-an-archived-blob-to-an-online-tier) – hozzon létre egy archív blob új másolatát a [blob másolása](/rest/api/storageservices/copy-blob) művelet használatával. Adjon meg egy másik blob-nevet és egy gyors vagy ritka elérésű célként megadott szintet.

 A rétegekkel kapcsolatos további információkért lásd [: Azure Blob Storage: gyakori, ritka elérésű és archív hozzáférési szintek](storage-blob-storage-tiers.md).

## <a name="rehydrate-an-archived-blob-to-an-online-tier"></a>Archivált Blobok rehidratálása online szintre

[!INCLUDE [storage-blob-rehydration](../../../includes/storage-blob-rehydrate-include.md)]

### <a name="lifecycle-management"></a>Életciklus-kezelés

A Blobok kiszáradása nem változik az `Last-Modified` ideje. Az [életciklus-kezelési](storage-lifecycle-management-concepts.md) funkció használatával létrehozhat egy olyan forgatókönyvet, amelyben a blob rehidratált, majd az életciklus-kezelési házirend visszahelyezi a blobot az archívumba, mert az `Last-Modified` idő meghaladja a házirendhez beállított küszöbértéket. Ennek elkerüléséhez használja az *[archivált blob másolása online szintű](#copy-an-archived-blob-to-an-online-tier)* metódusba lehetőséget. A másolási módszer a blob új példányát egy frissített `Last-Modified` időponttal hozza létre, és nem indítja el az életciklus-kezelési házirendet.

## <a name="monitor-rehydration-progress"></a>A rehidratálás folyamatának figyelése

A rehidratálás során a blob beolvasása művelettel ellenőrizze az **Archive status** attribútumot, és erősítse meg, hogy a szint módosítása befejeződött-e. Az állapot a célszinttől függően „rehydrate-pending-to-hot” (rehidratálás-folyamatban-a-gyakoriba) vagy „rehydrate-pending-to-cool” (rehidratálás-folyamatban-a-ritkába) lehet. Befejezésekor a rendszer eltávolítja az archiválási állapot tulajdonságot, és a hozzáférési réteg blob tulajdonsága az új gyakori vagy ritka **elérésű** szintet tükrözi.

## <a name="copy-an-archived-blob-to-an-online-tier"></a>Archivált blob másolása online szintre

Ha nem szeretné kiszáradni az archív blobot, dönthet úgy, hogy [másolási blob](/rest/api/storageservices/copy-blob) műveletet hajt végre. Az eredeti blob változatlan marad az archívumban, miközben új blob jön létre az online gyakori vagy ritka elérésű szinten, hogy működjön. A **blob másolása** műveletben az opcionális *x-MS-rehidratált-priority* tulajdonságot standard vagy magas értékre is állíthatja, hogy megadja, melyik prioritást kívánja létrehozni a blob-másolat létrehozásához.

A Blobok archívumból való másolása a kiválasztott rehidratálás prioritástól függően órákig elvégezhető. A háttérben a **blob másolása** művelet beolvassa az archív forrás blobját, hogy létrehozzon egy új online blobot a kiválasztott célhelyen. Előfordulhat, hogy az új blob látható a Blobok listázásakor, de az adatok nem érhetők el, amíg a forrás archív blobból való olvasás be nem fejeződik, és az adatok bekerülnek az új online cél blobba. Az új blob független másolat, és minden módosítás vagy törlés nem befolyásolja a forrás archív blobot.

> [!IMPORTANT]
> Ne törölje a forrás blobot, amíg a másolat sikeresen el nem fejeződik a célhelyen. Ha a forrás blob törölve lesz, akkor előfordulhat, hogy a cél blobja nem fejeződik be, és üres. A másolási művelet állapotának meghatározásához ellenőrizze az *x-MS-Copy-status állapotot* .

Az archív Blobok csak ugyanazon a Storage-fiókon belüli online célhelyekre másolhatók. Az archív Blobok másik archív blobba való másolása nem támogatott. A következő táblázat a **másolási Blobok** műveletének képességeit mutatja be.

|                                           | **Forró réteg forrása**   | **Hűvös réteg forrása** | **Archiválási szint forrása**    |
| ----------------------------------------- | --------------------- | -------------------- | ------------------- |
| **Gyors elérési szint célhelye**                  | Támogatott             | Támogatott            | Ugyanazon a fiókon belül támogatott; függőben lévő rehidratálás               |
| **Lassú elérési szint rendeltetése**                 | Támogatott             | Támogatott            | Ugyanazon a fiókon belül támogatott; függőben lévő rehidratálás               |
| **Archiválási szint célhelye**              | Támogatott             | Támogatott            | Nem támogatott         |

## <a name="pricing-and-billing"></a>Árak és számlázás

Az olvasási műveletek és az adatok lekérése során a rendszer felszámítja, hogy az archivált Blobok a gyakori vagy a ritka elérésű szintekre kerülnek. A magas prioritású használata magasabb működési és Adatlekérdezési költségeket biztosít a normál prioritáshoz képest. A magas prioritású rehidratálás a számlán külön tételként jelenik meg. Ha a magas prioritású kérelem egy pár gigabájt archiválási blobjának visszaadására irányul, 5 órát vesz igénybe, nem számítunk fel díjat a magas prioritású beolvasási arányért. A normál lekérési díjak azonban továbbra is érvényben maradnak, mivel a rehidratálás elsőbbséget élvez más kérelmekkel szemben.

A Blobok archívumból gyors vagy ritka elérésű szintekre való másolása olvasási műveletként és adatlekérdezésként történik. Az új blob másolatának létrehozásakor a rendszer írási műveletet számít fel. A korai törlési díjak nem érvényesek az online blobra másoláskor, mert a forrás blobja változatlan marad az archiválási szinten. Ha be van jelölve, a magas prioritású beolvasási díjak érvényesek.

Az archiválási szinten lévő blobokat legalább 180 napig kell tárolni. Az archivált Blobok törlésére vagy újraszárítására az 180 nap előtt a korai törlési díjat kell fizetni.

> [!NOTE]
> A blokk-blobok és az adattisztítások díjszabásával kapcsolatos további információkért lásd: az [Azure Storage díjszabása](https://azure.microsoft.com/pricing/details/storage/blobs/). A kimenő adatátviteli díjakkal kapcsolatos további információkért tekintse meg az [adatátviteli díjszabás részleteit](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="quickstart-scenarios"></a>Rövid útmutatóul szolgáló forgatókönyvek

### <a name="rehydrate-an-archive-blob-to-an-online-tier"></a>Archív blob rehidratálása online szintre
# <a name="portal"></a>[Portál](#tab/azure-portal)
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. A Azure Portal keresse meg és válassza ki az **összes erőforrás** elemet.

1. Válassza ki a tárfiókot.

1. Válassza ki a tárolót, majd válassza ki a blobot.

1. A **blob tulajdonságainál** válassza a lehetőség **módosítása** lehetőséget.

1. Válassza ki **a** gyakori **vagy ritka** elérésű hozzáférési szintet. 

1. Válassza a **standard** vagy a **magas** rehidratálás prioritást.

1. Kattintson a **Save (Mentés** ) gombra a lap alján.

![A Storage-fiók szintjeinek módosítása a ](media/storage-tiers/blob-access-tier.png)
 ![ rehidratálás állapotának ellenőrzését](media/storage-tiers/rehydrate-status.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Az alábbi PowerShell-parancsfájl segítségével módosíthatja az archív Blobok blob-szintjét. A `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a Storage-fiók nevével. A `$containerName` változót a tároló nevével kell inicializálni. A `$blobName` változót inicializálni kell a blob nevével. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$containerName = ""
$blobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Select the blob from a container
$blob = Get-AzStorageBlob -Container $containerName -Blob $blobName -Context $ctx

#Change the blob’s access tier to Hot using Standard priority rehydrate
$blob.ICloudBlob.SetStandardBlobTier("Hot", "Standard")
```
---

### <a name="copy-an-archive-blob-to-a-new-blob-with-an-online-tier"></a>Archív blob másolása egy új blobba egy online szinttel
Az alábbi PowerShell-szkripttel másolhatja az archív blobokat egy új blobba ugyanazon a Storage-fiókon belül. A `$rgName` változót inicializálni kell az erőforráscsoport nevével. A `$accountName` változót inicializálni kell a Storage-fiók nevével. A `$srcContainerName` és a `$destContainerName` változókat a tároló nevével kell inicializálni. A `$srcBlobName` és `$destBlobName` változókat a blob nevével kell inicializálni. 
```powershell
#Initialize the following with your resource group, storage account, container, and blob names
$rgName = ""
$accountName = ""
$srcContainerName = ""
$destContainerName = ""
$srcBlobName = ""
$destBlobName = ""

#Select the storage account and get the context
$storageAccount =Get-AzStorageAccount -ResourceGroupName $rgName -Name $accountName
$ctx = $storageAccount.Context

#Copy source blob to a new destination blob with access tier hot using standard rehydrate priority
Start-AzStorageBlobCopy -SrcContainer $srcContainerName -SrcBlob $srcBlobName -DestContainer $destContainerName -DestBlob $destBlobName -StandardBlobTier Hot -RehydratePriority Standard -Context $ctx
```

## <a name="next-steps"></a>Következő lépések

* [Tudnivalók a Blob Storage szintjeiről](storage-blob-storage-tiers.md)
* [Gyors, ritka és archív díjszabás a blob Storage-ban és a GPv2-fiókok régiónként](https://azure.microsoft.com/pricing/details/storage/)
* [Az Azure Blob Storage-életciklus felügyelete](storage-lifecycle-management-concepts.md)
* [Az adatátviteli díjszabás megtekintése](https://azure.microsoft.com/pricing/details/data-transfers/)