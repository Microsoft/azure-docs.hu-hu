---
title: A gyorsítótár és a növekményes bővítés konfigurálása (előzetes verzió)
titleSuffix: Azure Cognitive Search
description: A kibővített tartalom gyorsítótárazásának és megőrzésének engedélyezése a kognitív készségkészlet lévő felügyelt feldolgozáshoz. Ez a szolgáltatás jelenleg nyilvános előzetes verzióban érhető el.
author: vkurpad
manager: eladz
ms.author: vikurpad
ms.service: cognitive-search
ms.devlang: rest-api
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: a1b317b651b0e17c07eb17dbdb8a7c6657d39564
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "90971610"
---
# <a name="how-to-configure-caching-for-incremental-enrichment-in-azure-cognitive-search"></a>A növekményes dúsítás gyorsítótárazásának konfigurálása az Azure-ban Cognitive Search

> [!IMPORTANT] 
> A növekményes gazdagodás jelenleg nyilvános előzetes verzióban érhető el. Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 
> A [REST API előzetes verziójának verziója](search-api-preview.md) biztosítja ezt a szolgáltatást. Jelenleg nem érhető el portál vagy .NET SDK-támogatás.

Ebből a cikkből megtudhatja, hogyan adhat hozzá gyorsítótárazást egy alkoholtartalom-növelési folyamathoz, hogy fokozatosan módosítsa a lépéseket anélkül, hogy minden alkalommal újra kellene építenie. Alapértelmezés szerint a készségkészlet állapota állapot nélküli, és az összeállítás bármely részének megváltoztatásához az indexelő teljes újrafuttatására van szükség. A növekményes bővítéssel az indexelő meg tudja határozni, hogy a készségkészlet vagy az indexelő definíciójában észlelt változások alapján a dokumentum fájának mely részeit kell frissíteni. A meglévő feldolgozott kimenet megmarad, és bárhol felhasználható. 

A gyorsítótárazott tartalom az Azure Storage-ba kerül, az Ön által megadott fiókadatok használatával. Az `ms-az-search-indexercache-<alpha-numerc-string>` Indexelő futtatásakor létrejön a nevű tároló. Ezt a keresési szolgáltatás által kezelt belső összetevőnek kell tekinteni, és nem módosítható.

Ha még nem ismeri az indexelő beállítását, kezdje az [Indexelő áttekintésével](search-indexer-overview.md) , majd folytassa a [szakértelmével](cognitive-search-working-with-skillsets.md) , hogy megismerje a dúsítási folyamatokat. A főbb fogalmakkal kapcsolatos további információkért lásd: [növekményes gazdagodás](cognitive-search-incremental-indexing-conceptual.md).

## <a name="enable-caching-on-an-existing-indexer"></a>Gyorsítótárazás engedélyezése meglévő indexelő eszközön

Ha van olyan meglévő indexelő, amely már rendelkezik készségkészlet, kövesse az ebben a szakaszban található lépéseket a gyorsítótárazás hozzáadásához. Egyszeri műveletként a növekményes feldolgozás érvénybe léptetéséhez alaphelyzetbe kell állítania és újra kell futtatnia az indexelő.

> [!TIP]
> A megvalósíthatósági vizsgálat során ezen a [portálon](cognitive-search-quickstart-blob.md) keresztül elvégezheti a szükséges objektumok létrehozását, majd a Poster vagy a portál használatával is elvégezheti a frissítését. Előfordulhat, hogy számlázható Cognitive Services-erőforrást szeretne csatolni. Az indexelő többszöri futtatása az összes lépés elvégzése előtt kimeríti a napi ingyenes kiosztást.

### <a name="step-1-get-the-indexer-definition"></a>1. lépés: az indexelő definíciójának beolvasása

Kezdje egy érvényes, meglévő indexelő, amely a következő összetevőket tartalmazta: adatforrás, készségkészlet, index. Az indexelő futtatható kell lennie. 

Egy API-ügyfél használatával hozza létre az indexelő kérelmét az indexelő aktuális konfigurációjának [lekéréséhez](/rest/api/searchservice/get-indexer) . Ha az előnézeti API-verziót használja az indexelő beolvasása lehetőségre, a `cache` rendszer hozzáadja a null értékű tulajdonságot a definícióhoz.

```http
GET https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Másolja az indexelő definícióját a válaszból.

### <a name="step-2-modify-the-cache-property-in-the-indexer-definition"></a>2. lépés: a cache tulajdonság módosítása az indexelő definíciójában

Alapértelmezés szerint a `cache` tulajdonság null értékű. API-ügyfél használata a gyorsítótár konfigurációjának beállításához (a portál nem támogatja ezt a részecske-frissítést). 

Módosítsa a gyorsítótár objektumot úgy, hogy az tartalmazza a következő szükséges és választható tulajdonságokat: 

+ A `storageConnectionString` kötelező, és egy Azure Storage-beli kapcsolódási karakterláncra kell beállítani. 
+ A `enableReprocessing` logikai tulajdonság nem kötelező ( `true` alapértelmezés szerint), és azt jelzi, hogy a növekményes alkoholtartalom engedélyezve van. Ha szükséges, beállíthatja a `false` növekményes feldolgozás felfüggesztését, miközben más erőforrás-igényes műveletek, például az új dokumentumok indexelése folyamatban van, majd visszafordítható `true` később.

```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
}
```

### <a name="step-3-reset-the-indexer"></a>3. lépés: az indexelő alaphelyzetbe állítása

A meglévő indexelő növekményes bővítésének beállításakor az indexelő alaphelyzetbe állítása szükséges, hogy minden dokumentum konzisztens állapotban legyen. Ehhez a feladathoz használhatja a portált vagy egy API-ügyfelet, valamint az [indexelő REST API alaphelyzetbe állítása](/rest/api/searchservice/reset-indexer) lehetőséget.

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/reset?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

### <a name="step-4-save-the-updated-definition"></a>4. lépés: a frissített definíció mentése

[Frissítse az indexelő](/rest/api/searchservice/preview-api/update-indexer) egy Put-kérelemmel, a kérelem törzsének tartalmaznia kell a gyorsítótár tulajdonsággal rendelkező frissített indexelő definíciót. Ha 400 kap, ellenőrizze az indexelő definícióját, és győződjön meg arról, hogy teljesülnek az összes követelmény (adatforrás, készségkészlet, index).

```http
PUT https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
{
    "name" : "<YOUR-INDEXER-NAME>",
    ...
    "cache": {
        "storageConnectionString": "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    }
}
```

Ha most egy másik GET-kérelmet ad ki az indexelő számára, a szolgáltatás válasza tartalmazni fog egy `ID` tulajdonságot a gyorsítótár objektumban. Az alfanumerikus karakterláncot az indexelő által feldolgozott összes dokumentum gyorsítótárazott eredményeit és köztes állapotát tartalmazó tároló neve fűzi hozzá. A rendszer a gyorsítótár egyedi elnevezésére használja az azonosítót a blob Storage-ban.

```http
    "cache": {
        "ID": "<ALPHA-NUMERIC STRING>",
        "enableReprocessing": true,
        "storageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-STORAGE-KEY>;EndpointSuffix=core.windows.net"
    }
```

### <a name="step-5-run-the-indexer"></a>5. lépés: az indexelő futtatása

Az indexelő futtatásához használhatja a portált vagy az API-t. A portálon az indexelő listából válassza ki az indexelő elemet, majd kattintson a **Futtatás** gombra. A portál használatának egyik előnye, hogy nyomon követheti az indexelő állapotát, megjegyezheti a feladatok időtartamát és a feldolgozott dokumentumok számát. A portál oldalai néhány percenként frissülnek.

Azt is megteheti, hogy [a REST használatával futtatja az indexelő](/rest/api/searchservice/run-indexer):

```http
POST https://[YOUR-SEARCH-SERVICE].search.windows.net/indexers/[YOUR-INDEXER-NAME]/run?api-version=2020-06-30-Preview
Content-Type: application/json
api-key: [YOUR-ADMIN-KEY]
```

Az indexelő futtatása után megkeresheti a gyorsítótárat az Azure Blob Storage-ban. A tároló neve a következő formátumban van: `ms-az-search-indexercache-<YOUR-CACHE-ID>`

> [!NOTE]
> Az indexelő alaphelyzetbe állítása és újrafuttatása a teljes újraépítést eredményezi, hogy a tartalom gyorsítótárazható legyen. Minden kognitív gazdagodás minden dokumentumon újra le lesz futtatva.
>

### <a name="step-6-modify-a-skillset-and-confirm-incremental-enrichment"></a>6. lépés: a készségkészlet módosítása és a növekményes dúsítás megerősítése

A készségkészlet módosításához használhatja a portált vagy az API-t. Ha például szöveges fordítást használ, a `en` `es` -ból vagy más nyelvről történő egyszerű beágyazott változás elegendő a növekményes alkoholtartalom-növelés teszteléséhez.

Futtassa újra az indexelő. Csak a dúsított dokumentum fájának részei frissülnek. Ha a [portál](cognitive-search-quickstart-blob.md) rövid útmutatóját használta, és a szöveg fordítási képességeit "es" értékre módosította, akkor láthatja, hogy az eredeti 14 helyett csak 8 dokumentum frissül. A fordítási folyamat által nem érintett képfájlokat a rendszer újból felhasználja a gyorsítótárból.

## <a name="enable-caching-on-new-indexers"></a>Gyorsítótárazás engedélyezése új indexelő eszközökön

Egy új indexelő növekményes bővítésének beállításához mindössze annyit kell tennie, hogy a `cache` [create Indexer (2020-06-30 – Preview)](/rest/api/searchservice/preview-api/create-indexer)hívásakor az indexelő definíciójában szereplő tulajdonságot tartalmazza. 


```json
{
    "name": "<YOUR-INDEXER-NAME>",
    "targetIndexName": "<YOUR-INDEX-NAME>",
    "dataSourceName": "<YOUR-DATASOURCE-NAME>",
    "skillsetName": "<YOUR-SKILLSET-NAME>",
    "cache" : {
        "storageConnectionString" : "<YOUR-STORAGE-ACCOUNT-CONNECTION-STRING>",
        "enableReprocessing": true
    },
    "fieldMappings" : [],
    "outputFieldMappings": [],
    "parameters": []
    }
}
```

## <a name="checking-for-cached-output"></a>Gyorsítótárazott kimenet ellenőrzése

A gyorsítótárat az indexelő hozza létre, használja és kezeli, és a tartalma nem szerepel az emberi olvasásra alkalmas formátumban. A legjobb módszer annak megállapítására, hogy a gyorsítótár használatban van-e az indexelő futtatásával, valamint a végrehajtás ideje és a dokumentum darabszáma előtti és utáni metrikák összehasonlításával. 

Tegyük fel például, hogy egy készségkészlet, amely a beolvasott dokumentumok képelemzésével és optikai karakterfelismerésével (OCR) kezdődik, majd az eredményül kapott szöveg alsóbb rétegbeli elemzését. Ha módosítja egy alárendelt szövegbeli képességet, az indexelő lekérheti az összes korábban feldolgozott képet és OCR-tartalmat a gyorsítótárból, valamint a módosítások által jelzett szöveggel kapcsolatos módosításokat és feldolgozást. Várhatóan kevesebb dokumentum jelenik meg a dokumentumok száma (például 8/8, az eredeti futtatásban 14/14), a rövidebb végrehajtási idő, valamint a számlán kevesebb díj is.

## <a name="working-with-the-cache"></a>A gyorsítótár használata

A gyorsítótár működésének befejezése után az indexelő megtekinti a gyorsítótárat az [Indexelő futtatásakor](/rest/api/searchservice/run-indexer) , hogy megtudja, a meglévő kimenet mely részeit lehet használni. 

A következő táblázat összefoglalja, hogy a különböző API-k hogyan kapcsolódnak a gyorsítótárhoz:

| API           | Gyorsítótár hatása     |
|---------------|------------------|
| [Indexelő létrehozása (2020-06-30 – előzetes verzió)](/rest/api/searchservice/preview-api/create-indexer) | Létrehoz és futtat egy indexelő az első használatnál, beleértve a gyorsítótár létrehozását is, ha az indexelő definíciója megadja azt. |
| [Indexelő futtatása](/rest/api/searchservice/run-indexer) | Igény szerinti dúsítási folyamatot hajt végre. Ez az API a gyorsítótárból olvassa be, ha létezik, vagy létrehoz egy gyorsítótárat, ha egy frissített indexelő definícióhoz adta a gyorsítótárazást. Ha olyan indexelő futtat, amelyen engedélyezve van a gyorsítótárazás, az indexelő kihagyja a lépéseket, ha a gyorsítótárazott kimenet használható. Használhatja az API általánosan elérhető vagy előzetes verzió API-verzióját.|
| [Indexelő alaphelyzetbe állítása](/rest/api/searchservice/reset-indexer)| A növekményes indexelési információk indexelő adatainak törlése. A következő indexelő futtatása (igény szerinti vagy ütemezett) teljes újrafeldolgozást végez, beleértve az összes ismeret ismételt futtatását és a gyorsítótár újjáépítését. Ez a funkció az indexelő törlésével és újbóli létrehozásával egyenértékű. Használhatja az API általánosan elérhető vagy előzetes verzió API-verzióját.|
| [Képességek alaphelyzetbe állítása](/rest/api/searchservice/preview-api/reset-skills) | Meghatározza, hogy mely szaktudást futtassa újra a következő indexelő futtatásakor, még akkor is, ha nem módosította a szükséges képességeket. A gyorsítótár ennek megfelelően frissül. Az olyan kimenetek, mint például a Knowledge Store vagy a Search index, a gyorsítótárban található újrafelhasználható adatok használatával, valamint a frissített szaktudással együtt frissülnek. |

A gyorsítótárral kapcsolatos további információkért lásd: [gyorsítótár-kezelés](cognitive-search-incremental-indexing-conceptual.md#cache-management).

## <a name="next-steps"></a>Következő lépések

A növekményes alkoholtartalom a szakértelmével tartalmazó indexelő esetében alkalmazható. A következő lépésként tekintse meg a készségkészlet dokumentációját, és Ismerje meg a fogalmakat és az összetételt. 

Emellett ha engedélyezte a gyorsítótárat, tudnia kell a gyorsítótárban lévő paramétereket és API-kat, beleértve az egyes viselkedések felülbírálását vagy kényszerítését. További információkért tekintse meg a következő hivatkozásokat.

+ [Készségkészlet-fogalmak és-összeállítás](cognitive-search-working-with-skillsets.md)
+ [Készségkészlet létrehozása](cognitive-search-defining-skillset.md)
+ [A növekményes gazdagodás és a gyorsítótárazás bemutatása](cognitive-search-incremental-indexing-conceptual.md)