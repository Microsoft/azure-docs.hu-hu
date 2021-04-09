---
title: Módosított és törölt Blobok
titleSuffix: Azure Cognitive Search
description: Miután egy kezdeti keresési index létrehozta az Azure Blob Storage-ból való importálást, a későbbi indexelés csak azokat a blobokat tudja felvenni, amelyek módosítva vagy törölve lettek. Ez a cikk a részleteket ismerteti.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 79d5583f8c9e562a0d21a91c210aa6259472661d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "100383534"
---
# <a name="change-and-deletion-detection-in-blob-indexing-azure-cognitive-search"></a>Módosítási és törlési észlelés a blob-indexelésben (Azure Cognitive Search)

A kezdeti keresési index létrehozása után előfordulhat, hogy a következő indexelő feladatokkal csak új és módosított dokumentumokat szeretne felvenni. Az Azure Blob Storage-ból származó keresési tartalmak esetén a változások észlelése automatikusan történik, ha az indexelést az ütemterv használatával végzi. Alapértelmezés szerint a szolgáltatás csak a módosított blobokat indexeli, ahogy azt a blob `LastModified` időbélyege határozza meg. A keresési indexek által támogatott egyéb adatforrásokkal ellentétben a Blobok mindig rendelkeznek időbélyeg-val, így nem kell manuálisan beállítani a változás-észlelési szabályzatot.

Bár a változás észlelése egy adott, a törlés észlelése nem. Ha a törölt dokumentumokat szeretné felderíteni, ügyeljen arra, hogy a "Soft Delete" módszert használja. Ha törli a blobokat, a megfelelő dokumentumokat nem távolítja el a rendszer a keresési indexből.

A Soft delete módszer megvalósításának kétféleképpen valósítható meg:

+ Natív blob Soft Delete (előzetes verzió), lásd a következőt
+ [Soft delete egyéni metaadatok használatával](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>Natív blob – Soft Delete (előzetes verzió)

Ennél a törlési észlelési módszernél a Cognitive Search az Azure Blob Storage [natív blob-törlési](../storage/blobs/soft-delete-blob-overview.md) funkciójával függ, hogy megállapítsa, hogy a Blobok átkerültek-e a törölt állapotba. Ha ebben az állapotban blobokat észlel, a keresési indexelő ezt az információt használja a megfelelő dokumentum az indexből való eltávolításához.

> [!IMPORTANT]
> A natív Blobok Soft delete támogatása előzetes verzióban érhető el. Az előzetes verziójú funkciók szolgáltatói szerződés nélkül érhetők el, és éles számítási feladatokhoz nem ajánlott. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A [REST API 2020-06-30-es verziójának előzetes verziója](./search-api-preview.md) biztosítja ezt a funkciót. Jelenleg nincs portál vagy .NET SDK-támogatás.

### <a name="prerequisites"></a>Előfeltételek

+ [A Blobok Soft delete engedélyezése](../storage/blobs/soft-delete-blob-enable.md).
+ A bloboknak Azure Blob Storage-tárolóban kell lenniük. A Cognitive Search natív blob törlési szabályzata nem támogatott a Azure Data Lake Storage Gen2 Blobok esetén.
+ Az indexben lévő dokumentumokhoz tartozó dokumentum kulcsait blob-vagy blob-metaadatokként kell leképezni.
+ A `api-version=2020-06-30-Preview` Soft delete támogatásának konfigurálásához a preview REST API () t kell használnia.

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Törlési észlelés konfigurálása natív, helyreállítható törlés használatával

1. A blob Storage-ban a Soft delete engedélyezésekor állítsa be az adatmegőrzési szabályzatot olyan értékre, amely sokkal nagyobb, mint az indexelő intervallumának ütemezett értéke. Így ha probléma merül fel az indexelő futtatásakor, vagy ha nagy számú dokumentumot szeretne indexelni, akkor elég sok idő van arra, hogy az indexelő feldolgozza a puha törölt blobokat. Az Azure Cognitive Search-indexek csak akkor törölnek egy dokumentumot az indexből, ha a blobot feldolgozza, miközben a rendszer helyreállított állapotban van.

1. A Cognitive Searchban állítson be egy natív blobos törlési észlelési házirendet az adatforráson. Erre mutat példát az alábbi ábra. Mivel ez a funkció előzetes verzióban érhető el, az előzetes verziójú REST API kell használnia.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [Az indexelő futtatása](/rest/api/searchservice/run-indexer) vagy az indexelő beállítása [ütemezett](search-howto-schedule-indexers.md)futtatásra. Ha az indexelő egy olyan blobot futtat és dolgoz fel, amely helyreállítható törlési állapottal rendelkezik, a rendszer eltávolítja a megfelelő keresési dokumentumot az indexből.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Nem törölt Blobok újraindexelése (natív törlési szabályzatok használatával)

Ha a blob Storage-ban helyreállít egy törölt blobot, az indexelő nem mindig fogja újraindexelni. Ennek az az oka, hogy az indexelő a blob `LastModified` időbélyeg használatával határozza meg, hogy szükséges-e az indexelés. Ha egy nem törölt blob törölve lett, az `LastModified` időbélyeg nem frissül, így ha az indexelő már feldolgozta a blobokat a legutóbbi `LastModified` időbélyegekkel, akkor nem fogja újraindexelni a nem törölt blobot. 

A nem törölt Blobok újraindexelésének biztosításához frissítenie kell a blob `LastModified` időbélyegét. Ezt úgy teheti meg, ha átmenti a blob metaadatait. Nem kell módosítania a metaadatokat, de a metaadatok újramentése a blob `LastModified` időbélyegét fogja frissíteni, hogy az indexelő tudja felvenni.

## <a name="soft-delete-using-custom-metadata"></a>Soft delete egyéni metaadatok használatával

Ez a metódus egy blob metaadatait használja annak megállapítására, hogy el kell-e távolítani egy keresési dokumentumot az indexből. Ennek a módszernek két külön műveletre van szüksége, és törölni kell a keresési dokumentumot az indexből, majd az Azure Storage-ban a blob törlését.

A blob Storage-ban és a Cognitive Searchban egyaránt lépéseket kell követni, de nincs más szolgáltatás függőségei. Ezt a képességet az általánosan elérhető API-k támogatják.

1. Adjon hozzá egy egyéni metaadat-érték párokat a blobhoz, hogy jelezze az Azure Cognitive Search, hogy logikailag törölve van.

1. Állítsa be az adatforrásra vonatkozó, a törlési oszlop észlelésére szolgáló házirendet. Az alábbi szabályzat például egy olyan blobot tekint, amelyet törölni kell, ha a metaadatok tulajdonsága a következő `IsDeleted` értékkel rendelkezik `true` :

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Miután az indexelő feldolgozta a blobot, és törölte a dokumentumot az indexből, törölheti a blobot az Azure Blob Storage-ban.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Nem törölt Blobok újraindexelése (egyéni metaadatok használatával)

Miután egy indexelő feldolgozza a törölt blobot, és eltávolítja a megfelelő keresési dokumentumot az indexből, nem fogja újra felkeresni a blobot, ha később visszaállítja, ha a blob `LastModified` timestamp régebbi, mint az utolsó indexelő futtatása.

Ha szeretné átindexelni a dokumentumot, módosítsa a `"softDeleteMarkerValue" : "false"` blobot, és futtassa újra az indexelő.

## <a name="next-steps"></a>Következő lépések

+ [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
+ [BLOB-indexelő konfigurálása](search-howto-indexing-azure-blob-storage.md)
+ [A blob indexelésének áttekintése](search-blob-storage-integration.md)