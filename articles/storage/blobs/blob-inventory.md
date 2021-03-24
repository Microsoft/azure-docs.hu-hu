---
title: Az Azure Storage-leltár használata a blob-adatkezeléshez (előzetes verzió)
description: Az Azure Storage Inventory egy olyan eszköz, amely a Storage-fiókban lévő összes blob-adatai áttekintését segíti.
services: storage
author: mhopkins-msft
ms.service: storage
ms.date: 03/05/2021
ms.topic: conceptual
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: blobs
ms.custom: references_regions
ms.openlocfilehash: 8310de465a6416102a7ce4e614ead7029e6be87a
ms.sourcegitcommit: ac035293291c3d2962cee270b33fca3628432fac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2021
ms.locfileid: "104950926"
---
# <a name="use-azure-storage-blob-inventory-to-manage-blob-data-preview"></a>A blob-adatkészletek kezelése az Azure Storage blob Inventory szolgáltatással (előzetes verzió)

Az Azure Storage blob Inventory funkció áttekintést nyújt a blob-adatairól a Storage-fiókon belül. A leltár jelentés használatával megismerheti a teljes adatmennyiséget, az életkort, a titkosítási állapotot és így tovább. A jelentés áttekintést nyújt az üzleti és megfelelőségi követelményekkel kapcsolatos adatairól. Ha engedélyezve van, a leltári jelentés naponta automatikusan létrejön.

## <a name="availability"></a>Rendelkezésre állás

A blob Inventory az általános célú 2-es verzió (GPv2) és a prémium szintű blokk blob Storage-fiókok esetében is támogatott. Ez a funkció a [hierarchikus névtér](data-lake-storage-namespace.md) funkció engedélyezésével vagy anélkül támogatott.

> [!IMPORTANT]
> A blob-leltár jelenleg **előzetes** verzióban érhető el. Tekintse meg az Azure-szolgáltatásokra vonatkozó, a bétaverzióban, az előzetes verzióban, vagy más módon még nem közzétett, általánosan elérhetővé vált jogi feltételekhez tartozó [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .

### <a name="preview-regions"></a>Előnézeti régiók

A blob-leltár előzetes verziója a következő régiókban érhető el a Storage-fiókokban:

- Közép-Franciaország
- Közép-Kanada
- Kelet-Kanada
- USA keleti régiója
- USA 2. keleti régiója

### <a name="pricing-and-billing"></a>Árak és számlázás

A leltári jelentések díját az előzetes verzió időtartama alatt nem számoljuk fel. A szolgáltatás általánosan elérhetővé válik a díjszabás alapján.

## <a name="enable-inventory-reports"></a>Leltározási jelentések engedélyezése

A blob leltározási jelentéseinek engedélyezéséhez adjon hozzá egy szabályzatot a Storage-fiókjához. Házirend hozzáadása, szerkesztése vagy eltávolítása a [Azure Portal](https://portal.azure.com/)használatával.

1. Navigáljon a [Azure Portal](https://portal.azure.com/)
1. Válasszon egyet a Storage-fiókok közül
1. A **blob Service** területen válassza a **blob-leltár** elemet.
1. Győződjön meg arról, hogy a **blob-leltár engedélyezve** van
1. Válassza **a szabály hozzáadása** elemet.
1. Új szabály elnevezése
1. Adja meg a leltári jelentéshez tartozó **blob-típusokat**
1. Előtag-egyezés hozzáadása a leltári jelentés szűréséhez
1. Adja meg, hogy **szerepeljenek** -e blob-verziók, valamint **Pillanatképek szerepeljenek** a leltári jelentésben. A fiókban engedélyezni kell a verziókat és a pillanatképeket, hogy az új szabályt a megfelelő beállítással mentse.
1. Válassza a **Mentés** lehetőséget

:::image type="content" source="./media/blob-inventory/portal-blob-inventory.png" alt-text="Képernyőfelvétel: blob-leltározási szabály hozzáadása a Azure Portal használatával":::

A leltári szabályzatok olvasása vagy írása teljes. A részleges frissítések nem támogatottak.

> [!IMPORTANT]
> Ha engedélyezi a tűzfalszabályokat a tárfiókon, előfordulhat, hogy a rendszer blokkolja a készletkéréseket. A kérések blokkolását úgy oldhatja fel, ha kivételeket ad meg a megbízható Microsoft-szolgáltatásokra vonatkozóan. További információt a [tűzfalak és virtuális hálózatok konfigurálása](../common/storage-network-security.md#exceptions)című témakör kivételek című szakaszában talál.

A blob-leltár futtatása minden nap automatikusan ütemezve van. A leltár futtatása akár 24 óráig is eltarthat. Egy leltári jelentés úgy van konfigurálva, hogy leltári házirendet ad hozzá egy vagy több szabállyal.

## <a name="inventory-policy"></a>Leltári szabályzat

A leltári szabályzat egy JSON-dokumentum szabályainak gyűjteménye.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition": {. . .}
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition": {. . .}
    }]
}
```

A leltározási szabályzat JSON-fájljának megtekintéséhez válassza a Azure Portal **blob Inventory** szakaszának **kód nézet** lapját.

| Paraméter neve | Paramétertípus        | Jegyzetek | Kötelező? |
|----------------|-----------------------|-------|-----------|
| destination    | Sztring                | Az a cél tároló, amelyben a rendszer az összes leltározási fájlt létrehozza. A célként megadott tárolónak már léteznie kell. | Igen |
| engedélyezve        | Logikai               | A teljes szabályzat letiltására szolgál. Ha **igaz** értékre van állítva, akkor a szabály szintje engedélyezve mező felülbírálja ezt a paramétert. Ha le van tiltva, az összes szabály leltára le lesz tiltva. | Igen |
| szabályok          | Szabály objektumainak tömbje | Egy házirendben legalább egy szabályra van szükség. Legfeljebb 10 szabály támogatott. | Igen |

## <a name="inventory-rules"></a>Leltári szabályok

A szabályok rögzítik a leltári jelentések létrehozásához szükséges szűrési feltételeket és kimeneti paramétereket. Mindegyik szabály egy leltári jelentést hoz létre. A szabályok átfedő előtagokat is tartalmazhatnak. A szabályok definíciói alapján a Blobok több leltárban is szerepelhetnek.

A szabályzaton belüli szabályok több paraméterrel rendelkeznek:

| Paraméter neve | Paramétertípus                 | Jegyzetek | Kötelező? |
|----------------|--------------------------------|-------|-----------|
| name           | Sztring                         | A szabály neve legfeljebb 256 kis-és nagybetűket megkülönböztető alfanumerikus karaktereket tartalmazhat. A névnek egyedinek kell lennie a szabályzaton belül. | Igen |
| engedélyezve        | Logikai                        | Olyan jelző, amely lehetővé teszi egy szabály engedélyezését vagy letiltását. Az alapértelmezett érték **true (igaz**). | Igen |
| definíció     | JSON-leltározási szabály definíciója | Az egyes definíciók egy szabálykészlet-készletből állnak. | Igen |

A globális **blob-leltár engedélyezve** jelzője elsőbbséget élvez a szabályban *engedélyezett* paraméterrel szemben.

### <a name="rule-filters"></a>Szabályok szűrői

Több szűrő is elérhető a blob-leltári jelentések testreszabásához:

| Szűrő neve         | Szűrő típusa                     | Jegyzetek | Kötelező? |
|---------------------|---------------------------------|-------|-----------|
| blobTypes           | Előre definiált enumerálási értékek tömbje | Az érvényes értékek `blockBlob` `appendBlob` : és a hierarchikus névtérben engedélyezett fiókok, és, `blockBlob` `appendBlob` és `pageBlob` más fiókok esetében. | Igen |
| prefixMatch         | Legfeljebb 10 sztringből álló tömb az előtagok megfeleltetéséhez. Az előtagnak a tároló nevével kell kezdődnie, például: "container1/foo" | Ha nem határoz meg *prefixMatch* , vagy üres előtagot ad meg, a szabály a Storage-fiókban lévő összes blobra vonatkozik. | Nem |
| includeSnapshots    | Logikai                         | Meghatározza, hogy a leltár tartalmazzon-e pillanatképeket. Az alapértelmezett érték a **false**. | Nem |
| includeBlobVersions | Logikai                         | Meghatározza, hogy a leltár tartalmazzon-e blob-verziókat. Az alapértelmezett érték a **false**. | Nem |

A leltári szabályok JSON-fájljának megtekintéséhez válassza a Azure Portal **blob Inventory** szakaszának **kód nézet** lapját. A szűrők a szabály definíciójában vannak megadva.

```json
{
    "destination": "destinationContainer",
    "enabled": true,
    "rules": [
    {
        "enabled": true,
        "name": "inventoryrule1",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["blockBlob", "appendBlob", "pageBlob"],
                "prefixMatch": ["inventorycontainer1", "inventorycontainer2/abcd", "etc"]
            }
        }
    },
    {
        "enabled": true,
        "name": "inventoryrule2",
        "definition":
        {
            "filters":
            {
                "blobTypes": ["pageBlob"],
                "prefixMatch": ["inventorycontainer-disks-", "inventorycontainer4/"],
                "includeSnapshots": true,
                "includeBlobVersions": true
            }
        }
    }]
}
```

## <a name="inventory-output"></a>Leltár kimenete

Az egyes leltározások egy CSV formátumú fájlt hoznak létre a megadott leltári tárolóban. A leltár kimenetét a következő elérési út alapján hozza létre a `https://<accountName>.blob.core.windows.net/<inventory-destination-container>/YYYY/MM/DD/HH-MM-SS/` rendszer:

- a *accountName* az Azure Blob Storage-fiók neve
- *leltár – cél – a tároló* a leltári házirendben megadott cél tároló.
- Az *éééé/hh/nn/hh-mm-SS* az az idő, amikor a leltár futása megkezdődött

### <a name="inventory-files"></a>Leltári fájlok

Minden leltári Futtatás a következő fájlokat hozza létre:

- **Leltár CSV-fájlja**: vesszővel tagolt (CSV) fájl az egyes leltározási szabályokhoz. Minden fájl egyező objektumokat és metaadatokat tartalmaz. Minden CSV formátumú fájl első sora mindig a séma sora. Az alábbi képen egy Microsoft Excelben megnyitott leltár CSV-fájl látható.

   :::image type="content" source="./media/blob-inventory/csv-file-excel.png" alt-text="Képernyőfelvétel a Microsoft Excelben megnyitott leltár CSV-fájlról":::

- **Manifest file (jegyzékfájl**): a (z) azon fájl manifest.js, amely a futtatott összes szabályhoz generált leltári fájlok részleteit tartalmazza. A jegyzékfájl rögzíti a felhasználó által megadott szabálykészlet és az adott szabályhoz tartozó leltár elérési útját is.

- **Ellenőrzőösszeg-fájl**: egy manifest. ellenőrzőösszeg fájl, amely a fájl manifest.jstartalmának MD5 ellenőrzőösszegét tartalmazza. A manifest. ellenőrzőösszeg fájl generációja a leltár futtatásának befejezését jelzi.

## <a name="inventory-completed-event"></a>Leltár befejezve esemény

Fizessen elő a leltár befejezett eseményére, hogy értesítést kapjon a leltár futásának befejeződése után. Ez az esemény a jegyzékfájl ellenőrzőösszeg-fájljának létrehozásakor jön létre. A leltár befejezett esemény akkor is előfordul, ha a leltár futtatása sikertelen a Futtatás előtt. Például egy érvénytelen házirend vagy a célként megadott tároló nem jeleníti meg a hibát, elindítja az eseményt. Az eseményt a rendszer közzéteszi a blob Inventory témakörben.

Példa a következő eseményre:

```json
{
  "topic": "/subscriptions/3000151d-7a84-4120-b71c-336feab0b0f0/resourceGroups/BlobInventory/providers/Microsoft.EventGrid/topics/BlobInventoryTopic",
  "subject": "BlobDataManagement/BlobInventory",
  "eventType": "Microsoft.Storage.BlobInventoryPolicyCompleted",
  "id": "c99f7962-ef9d-403e-9522-dbe7443667fe",
  "data": {
    "scheduleDateTime": "2020-10-13T15:37:33Z",
    "accountName": "inventoryaccountname",
    "policyRunStatus": "Succeeded",
    "policyRunStatusMessage": "Inventory run succeeded, refer manifest file for inventory details.",
    "policyRunId": "b5e1d4cc-ee23-4ed5-b039-897376a84f79",
    "manifestBlobUrl": "https://inventoryaccountname.blob.core.windows.net/inventory-destination-container/2020/10/13/15-37-33/manifest.json"
  },
  "dataVersion": "1.0",
  "metadataVersion": "1",
  "eventTime": "2020-10-13T15:47:54Z"
}
```

## <a name="next-steps"></a>Következő lépések

- [A Blobok darabszámának és teljes méretének kiszámítása egy tárolóban](calculate-blob-count-size.md)
- [Az Azure Blob Storage életciklusának kezelése](storage-lifecycle-management-concepts.md)
