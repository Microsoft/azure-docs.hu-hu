---
title: Keresés az Azure Blob Storage-tartalmakon
titleSuffix: Azure Cognitive Search
description: További információ az Azure-Blobok szövegének kinyeréséről, valamint teljes szöveges kereshetőség az Azure Cognitive Search indexében.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/23/2020
ms.openlocfilehash: f61bf635cc61a2153a7bb016ef4b4711d7ba7391
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91355295"
---
# <a name="search-over-azure-blob-storage-content"></a>Keresés az Azure Blob Storage-tartalmakon

Az Azure Blob Storage-ban tárolt különböző tartalomtípusok közötti keresés nehéz megoldás lehet. Ebben a cikkben áttekintheti a tartalom és metaadatok a blobokból való kinyeréséhez és az Azure Cognitive Search keresési indexbe való küldéséhez szükséges alapszintű munkafolyamatot. Az eredményül kapott index a teljes szöveges keresés használatával kérdezhető le.

> [!NOTE]
> Már ismeri a munkafolyamatot és az összeállítást? A [blob indexelő konfigurálása](search-howto-indexing-azure-blob-storage.md) a következő lépés.

## <a name="what-it-means-to-add-full-text-search-to-blob-data"></a>Mit jelent a teljes szöveges keresés hozzáadása a blob-adathoz

Az Azure Cognitive Search egy olyan keresési szolgáltatás, amely támogatja az indexelést és a lekérdezési feladatokat a felhőben üzemeltetett távoli kereshető tartalmat tartalmazó, felhasználó által definiált indexeken keresztül. A kereshető tartalmak a lekérdezési motorral való közös elhelyezéséhez szükséges a teljesítmény, a sebességű felhasználók által várt eredmények pedig a keresési lekérdezésekből származnak.

A Cognitive Search az indexelési rétegben integrálva van az Azure Blob Storage szolgáltatással, így a blob tartalmát olyan keresési dokumentumokként importálhatja, amelyek az ingyenes szöveges lekérdezéseket *és a* szűrési kifejezéseket támogató lekérdezési struktúrákba vannak indexelve. Mivel a blob tartalma egy keresési indexbe van indexelve, az Azure Cognitive Search lekérdezési funkcióinak teljes skáláját használhatja a blob tartalmában található információk megkereséséhez.

A bemenetek a Blobok egyetlen tárolóban, az Azure Blob Storage-ban. A Blobok szinte bármilyen típusú szöveges adattal rendelkezhetnek. Ha a Blobok képeket tartalmaznak, hozzáadhat AI-bővítést [a blob indexeléshez](search-blob-ai-integration.md) a képekből származó szövegek létrehozásához és kinyeréséhez.

A kimenet mindig egy Azure Cognitive Search index, amely a gyors szöveges kereséshez, a lekérésekhez és a feltáráshoz használható az ügyfélalkalmazások számára. A között az indexelő folyamat architektúrája. A folyamat az *Indexelő* szolgáltatáson alapul, amelyet a cikk további részében ismertetett.

Miután létrehozta és feltölti az indexet, a blob-tárolótól függetlenül létezik, de újból újrafuttathatja az indexelési műveleteket, hogy a módosított dokumentumok alapján frissítse az indexet. Az egyes Blobok időbélyeg-információi a változás észlelésére szolgálnak. Az ütemezett végrehajtást vagy igény szerinti indexelést a frissítési mechanizmusként is választhatja.

## <a name="required-resources"></a>Szükséges erőforrások

Az Azure Cognitive Search és az Azure Blob Storage szolgáltatásra is szüksége van. A blob Storage-ban olyan tárolóra van szükség, amely a forrás tartalmát biztosítja.

A Storage-fiók portálján közvetlenül is elindíthatja a lapot. A bal oldali navigációs oldal **blob Service** területén kattintson az **Azure Cognitive Search hozzáadása** elemre egy új szolgáltatás létrehozásához, vagy válasszon ki egy meglévőt. 

Miután hozzáadta az Azure-Cognitive Search a Storage-fiókjához, a szabványos folyamat használatával indexelheti a Blobok adatait. Javasoljuk, hogy az **adatok importálása** varázslót az Azure Cognitive Search egyszerű kezdeti bevezetésre használja, vagy hívja meg a REST API-kat egy olyan eszközzel, mint például a Poster. Ez az oktatóanyag végigvezeti azon lépéseken, amelyekkel meghívja a REST API a Poster [szolgáltatásban: index és keresés részben strukturált adatfájlok (JSON-Blobok) az Azure Cognitive Searchban](search-semi-structured-data.md). 

## <a name="use-a-blob-indexer"></a>BLOB-indexelő használata

Az *Indexelő* egy adatforrást támogató alszolgáltatás Cognitive Searchban, amely belső logikával rendelkezik a mintavételi adatokhoz, a metaadatok beolvasásához, az adatok lekéréséhez és az adatok natív formátumokból való szerializálásához a következő importáláshoz. 

Az Azure Storage-beli Blobok indexelése az [azure Cognitive Search blob Storage indexelő](search-howto-indexing-azure-blob-storage.md)használatával történik. Az indexelő az **adatimportálás** varázsló, a REST API vagy a .net SDK használatával hívható meg. A kódban ezt az indexelő kell használnia a típus megadásával, valamint az Azure Storage-fiókkal és a blob-tárolóval együtt tartalmazó kapcsolatok adatainak biztosításával. A blobokat egy virtuális könyvtár létrehozásával, amelyet aztán paraméterként adhat át, vagy szűrheti a fájltípusok kiterjesztését.

Az indexelő a "dokumentum repedése", a Blobok megnyitása a tartalom vizsgálatához. Az adatforráshoz való csatlakozás után ez a folyamat első lépése. A Blobok esetében ez az a hely, ahol a rendszer PDF-fájlokat, Office-dokumentumokat és más típusú tartalmakat észlel. A szöveg kibontásával nem számítunk fel díjat. Ha a Blobok képtartalmat tartalmaznak, a rendszer figyelmen kívül hagyja a képeket, hacsak nem [ad hozzá mesterséges intelligenciát](search-blob-ai-integration.md). A normál indexelés csak szöveges tartalomra vonatkozik.

A blob indexelő konfigurációs paramétereket tartalmaz, és támogatja a változások követését, ha az alapul szolgáló adatok elegendő információt biztosítanak. További információ az [Azure Cognitive Search blob Storage indexelő](search-howto-indexing-azure-blob-storage.md)alapfunkcióival kapcsolatban.

### <a name="supported-content-types"></a>Támogatott tartalomtípusok

A blob Indexer tárolón való futtatásával a következő tartalomtípusokból származó szövegeket és metaadatokat lehet kinyerni egyetlen lekérdezéssel:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

### <a name="indexing-blob-metadata"></a>BLOB metaadatainak indexelése

Egy gyakori forgatókönyv, amely megkönnyíti a tetszőleges tartalomtípusú Blobok rendezését, az egyéni metaadatok és a Rendszertulajdonságok indexelése az egyes blobokhoz. Így az összes blobra vonatkozó információ indexelve van, tekintet nélkül a keresési szolgáltatásban tárolt indexbe. Az új index használatával folytathatja az összes blob Storage-tartalom rendezését, szűrését és aspektusát.

> [!NOTE]
> A blob-index címkéi natív módon vannak indexelve a blob Storage szolgáltatásban, és elérhetők a lekérdezéshez. Ha a Blobok kulcs/érték attribútumai indexelést és szűrési képességeket igényelnek, a blob-index címkéit a metaadatok helyett ki kell használni.
>
> További információ a blob indexről: az [Azure Blob Storage adatainak kezelése és keresése blob indextel](../storage/blobs/storage-manage-find-blobs.md).

### <a name="indexing-json-blobs"></a>JSON-Blobok indexelése

Az indexelő úgy konfigurálható, hogy kinyerje a JSON-t tartalmazó blobokban található strukturált tartalmat. Az indexelő képes beolvasni a JSON-blobokat, és elemezni a strukturált tartalmat a keresési dokumentum megfelelő mezőiben. Az indexelő olyan blobokat is tartalmazhat, amelyek JSON-objektumok tömbjét tartalmazzák, és az egyes elemeket egy külön keresési dokumentumra képezik le. Az elemzési mód beállítható úgy, hogy az az indexelő által létrehozott JSON-objektum típusát is befolyásolja.

## <a name="search-blob-content-in-a-search-index"></a>A blob tartalmának keresése keresési indexben 

Az indexelő kimenete egy keresési index, amely az interaktív feltáráshoz használatos az ügyfélalkalmazások ingyenes szöveges és szűrt lekérdezéseit használva. A tartalom kezdeti feltárásához és ellenőrzéséhez javasoljuk, hogy a portálon kezdjen a [keresési tallózóval](search-explorer.md) a dokumentum-struktúra vizsgálatához. Használhatja az [egyszerű lekérdezési szintaxist](query-simple-syntax.md), a [teljes lekérdezési szintaxist](query-lucene-syntax.md)és a [szűrési kifejezés szintaxisát](query-odata-filter-orderby-syntax.md) a keresési Explorerben.

A több állandó megoldás a lekérdezési bemenetek összegyűjtése és a válasz megjelenítése keresési eredményekként egy ügyfélalkalmazás számára. A következő C#-oktatóanyag azt ismerteti, hogyan lehet keresési alkalmazást [létrehozni: hozza létre első alkalmazását az Azure Cognitive Searchban](tutorial-csharp-create-first-app.md).

## <a name="next-steps"></a>Következő lépések

+ [Blobok feltöltése, letöltése és listázása a Azure Portal (Azure Blob Storage)](../storage/blobs/storage-quickstart-blobs-portal.md)
+ [BLOB-indexelő beállítása (Azure Cognitive Search)](search-howto-indexing-azure-blob-storage.md)