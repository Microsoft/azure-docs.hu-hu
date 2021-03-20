---
title: BLOB-indexelő konfigurálása
titleSuffix: Azure Cognitive Search
description: Állítson be egy Azure Blob indexelő, amely automatizálja a Blobok tartalmának indexelését a teljes szöveges keresési műveletekhez az Azure Cognitive Searchban.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.custom: contperf-fy21q3
ms.openlocfilehash: 74813fabec4d5fe43cd158bb4aa359c2a3b0188a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988717"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>BLOB-indexelés konfigurálása a Cognitive Searchban

Ebből a cikkből megtudhatja, hogyan konfigurálhat blob-indexet szöveges dokumentumok indexeléséhez (például PDF-fájlok, Microsoft Office dokumentumok és egyebek) az Azure Cognitive Searchban. Ha nem ismeri az indexelő fogalmait, kezdje az [Azure Cognitive Search indexelő](search-indexer-overview.md) szolgáltatásával, és [hozzon létre egy keresési indexet](search-howto-create-indexers.md) a blob-indexelés előtt.

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>Támogatott dokumentumformátumok

Az Azure Cognitive Search blob indexelő a következő dokumentum-formátumokból tud szöveget kinyerni:

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>Adatforrás-definíciók

A blob indexelő és bármely más indexelő közötti különbség az indexelő számára hozzárendelt adatforrás-definíció. Az adatforrás minden olyan tulajdonságot beágyaz, amely megadja az indexelni kívánt tartalom típusát, a kapcsolódást és a helyét.

A blob-adatforrás definíciója az alábbi példához hasonlóan néz ki:

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

A `"credentials"` tulajdonság lehet egy kapcsolatok karakterlánca, ahogy az a fenti példában is látható, vagy a következő szakaszban ismertetett alternatív megközelítések egyike. A `"container"` tulajdonság megadja a tartalom helyét az Azure Storage-ban, és a `"query"` tárolóban található almappa megadására szolgál. További információ az adatforrás-definíciókkal kapcsolatban: [create adatforrás (REST)](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>

## <a name="credentials"></a>Hitelesítő adatok

A blob-tároló hitelesítő adatait az alábbi módszerek egyikével adhatja meg:

**Felügyelt identitás-kapcsolatok karakterlánca**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

Ehhez a kapcsolódási karakterlánchoz nem szükséges a fiók kulcsa, de a [felügyelt identitás használatával kell megadnia egy Azure Storage-fiókhoz való kapcsolódás beállításának](search-howto-managed-identities-storage.md)utasításait.

**Teljes hozzáférésű Storage-fiók kapcsolati sztringje**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

A kapcsolati karakterláncot a Azure Portal a Storage-fiók panel > beállítások > kulcsok (klasszikus Storage-fiókok esetében) vagy a beállítások > hozzáférési kulcsok (Azure Resource Manager Storage-fiókok esetében) lehetőségre kattintva érheti el.

A **Storage-fiók közös hozzáférésű aláírása** (SAS) kapcsolati karakterlánca:`{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

Az SAS-nek szerepelnie kell a listán, és olvasási engedéllyel kell rendelkeznie a tárolók és objektumok számára (ebben az esetben Blobok).

**Tároló közös hozzáférésének aláírása**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

Az SAS-nek a tárolóban szerepelnie kell a listához és az olvasáshoz szükséges engedélyekkel. További információ a Storage közös hozzáférésű aláírásáról: a [közös hozzáférésű aláírások használata](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Ha SAS hitelesítő adatokat használ, az adatforráshoz tartozó hitelesítő adatokat rendszeresen frissítenie kell megújított aláírásokkal a lejárat megakadályozása érdekében. Ha az SAS hitelesítő adatai lejárnak, az indexelő a következőhöz hasonló hibaüzenettel fog meghiúsulni: "a kapcsolódási karakterláncban megadott hitelesítő adatok érvénytelenek vagy elévültek".  

## <a name="index-definitions"></a>Index-definíciók

Az index meghatározza a dokumentumok, attribútumok és más, a keresési élményt formáló szerkezetek mezőit. Az alábbi példa egy egyszerű indexet hoz létre a [create index (REST API)](/rest/api/searchservice/create-index)használatával. 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

Az index-definíciók a gyűjtemény egy mezőjét igénylik a `"fields"` dokumentum kulcsaként. Az index definícióinak tartalmazniuk kell a tartalom és a metaadatok mezőit is.

A **`content`** blobokból kinyert szöveg tárolására szolgáló mező. A mező definíciója a fentiekhez hasonlóan fog kinézni. Ezt a nevet nem kell használnia, de ezzel lehetővé teszi az implicit mezők leképezésének előnyeit. A blob-indexelő elküldheti a blob tartalmát az index Content EDM. String mezőjébe, nincs szükség mező-hozzárendelésre.

Hozzáadhat mezőket is az indexben használni kívánt blob-metaadatokhoz. Az indexelő képes beolvasni az egyéni metaadat-tulajdonságokat, a [szabványos metaadat](#indexing-blob-metadata) -tulajdonságokat és a [tartalom-specifikus metaadatok](search-blob-metadata-properties.md) tulajdonságait. Az indexekről további információt az [index létrehozása](search-what-is-an-index.md)című témakörben talál.

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>A dokumentumok kulcsainak és a mezők hozzárendelésének meghatározása

A keresési indexekben a dokumentum kulcsa egyedileg azonosítja az egyes dokumentumokat. A kiválasztott mezőnek típusúnak kell lennie `Edm.String` . BLOB-tartalom esetén a dokumentum kulcsának legjobb jelöltje a blob metaadat-tulajdonságai.

+ **`metadata_storage_name`** – Ez a tulajdonság egy jelölt, de csak akkor, ha a nevek egyediek az összes olyan tárolóban és mappában, amelyet indexel. A blob helyétől függetlenül a végeredmény az, hogy a dokumentum kulcsának (neve) egyedinek kell lennie a keresési indexben, miután az összes tartalom indexelve lett. 

  Egy másik lehetséges probléma a tároló nevével kapcsolatban, hogy olyan karaktereket tartalmazhat, amelyek érvénytelenek a dokumentum kulcsaihoz, például kötőjelekhez. Az érvénytelen karaktereket a `base64Encode` [mező-hozzárendelési függvény](search-indexer-field-mappings.md#base64EncodeFunction)használatával kezelheti. Ha ezt teszi, ne felejtse el a dokumentum kulcsainak kódolását is, ha olyan API-hívásokba kerül, mint például a [keresési dokumentum (REST)](/rest/api/searchservice/lookup-document). A .NET-ben használhatja a [UrlTokenEncode metódust](/dotnet/api/system.web.httpserverutility.urltokenencode) a karakterek kódolására.

+ **`metadata_storage_path`** – a teljes elérési út használata biztosítja az egyediséget, de az elérési út határozottan olyan karaktereket tartalmaz, `/` amelyek [érvénytelenek a dokumentum kulcsaiban](/rest/api/searchservice/naming-rules). A fentieknek megfelelően használhatja a `base64Encode` [függvényt](search-indexer-field-mappings.md#base64EncodeFunction) a karakterek kódolásához.

+ A harmadik lehetőség egy egyéni metaadat-tulajdonság hozzáadása a blobokhoz. Ez a beállítás megköveteli, hogy a blob feltöltési folyamata hozzáadja a metaadatokat az összes blobhoz. Mivel a kulcs egy kötelező tulajdonság, az értéket hiányzó Blobok nem lesznek indexelve.

> [!IMPORTANT]
> Ha nincs explicit leképezés a kulcs mezőhöz az indexben, az Azure Cognitive Search automatikusan a kulcsként `metadata_storage_path` és az alap-64 kódolással kódolja a kulcs értékeit (a fenti második lehetőség).
>

#### <a name="example"></a>Példa

Az alábbi példa `metadata_storage_name` a dokumentum kulcsát mutatja be. Tegyük fel, hogy az indexnek van egy nevű kulcs mezője `key` és egy nevű mező a `fileSize` dokumentum méretének tárolásához. A [mező-hozzárendelések](search-indexer-field-mappings.md) az indexelő definíciójában a mezők társításait határozzák meg, és `metadata_storage_name` a [ `base64Encode` mező leképezése funkcióval](search-indexer-field-mappings.md#base64EncodeFunction) a nem támogatott karakterek is kezelhetők.

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>Kódolt mező kereshetővé tétele

Időnként, ha egy mező `metadata_storage_path` (például a kulcs) kódolt verzióját kell használnia, de a mezőnek a keresési indexben is kereshetőnek (kódolás nélkül) kell lennie. Mindkét használati eset támogatásához leképezheti `metadata_storage_path` a két mezőt, egyet a kulcshoz (kódolva), egy másodikat pedig egy olyan Path mezőhöz, amelyet feltételezhető, hogy "kereshető"ként van megadva az index sémában. Az alábbi példa két mező-hozzárendelést mutat be a következőhöz: `metadata_storage_path` .

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>Tartalom és metaadatok indexelése

A Blobok tartalmakat és metaadatokat tartalmaznak. Megadhatja, hogy a Blobok mely részei legyenek indexelve a `dataToExtract` konfigurációs paraméter használatával. A következő értékeket veheti fel:

+ `contentAndMetadata` -Megadja, hogy a blobból kinyert összes metaadat és szöveges tartalom indexelve legyen. Ez az alapértelmezett érték.

+ `storageMetadata` -azt adja meg, hogy csak a [szabványos blob-tulajdonságok és a felhasználó által megadott metaadatok](../storage/blobs/storage-blob-container-properties-metadata.md) legyenek indexelve.

+ `allMetadata` -azt adja meg, hogy a rendszer kinyeri a szabványos blob-tulajdonságokat és a [talált tartalomtípusok metaadatait](search-blob-metadata-properties.md) a blob tartalmából és indexelve.

Ha például csak a tárolási metaadatokat szeretné indexelni, használja a következőt:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>BLOB tartalmának indexelése

A strukturált tartalommal (például JSON vagy CSV) rendelkező Blobok alapértelmezés szerint egyetlen darab szövegként vannak indexelve. Ha azonban a JSON-vagy CSV-dokumentumok belső struktúrával (határolójelekkel) rendelkeznek, az elemzési módokat hozzárendelheti az egyes sorokhoz vagy elemekhez tartozó egyéni keresési dokumentumok létrehozásához. További információ: JSON- [Blobok indexelése](search-howto-index-json-blobs.md) és [CSV-Blobok indexelése](search-howto-index-csv-blobs.md).

Összetett vagy beágyazott dokumentum (például ZIP-archívum, beágyazott Outlook-e-mail-mellékleteket tartalmazó Word-dokumentum vagy a). A mellékleteket tartalmazó MSG-fájl is egyetlen dokumentumként van indexelve. Például az összes kép a mellékletekből kinyerve. A rendszer a normalized_images mezőben adja vissza az MSG-fájlt.

A dokumentum szöveges tartalma kinyerve egy nevű karakterlánc-mezőbe `content` .

  > [!NOTE]
  > Az Azure Cognitive Search korlátozza, hogy mennyi szöveg legyen kibontva a díjszabási szintjétől függően. A jelenlegi [szolgáltatási korlátok](search-limits-quotas-capacity.md#indexer-limits) az ingyenes szint, a 64 000 az alapszintű, a 4 000 000 a standard, a 8 000 000 a standard S2 esetében, valamint az 16 000 000 standard S3 esetén 32 000 karakter. A rendszer figyelmeztetést tartalmaz az indexelő állapotának a csonkolt dokumentumokra adott válaszában.  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>BLOB metaadatainak indexelése

Az indexelő is indexelik a blob metaadatait. Először is minden felhasználó által megadott metaadat-tulajdonságot lehet szó szerint kibontani. Az értékek fogadásához meg kell adnia a (z) típusú keresési indexben lévő mezőt `Edm.String` , amelynek a neve megegyezik a blob metaadat-kulcsával. Ha például egy blob tartalmaz egy `Sensitivity` értékkel rendelkező metaadat `High` -kulcsot, akkor meg kell adnia egy nevű mezőt a `Sensitivity` keresési indexben, és az értékkel lesz feltöltve `High` .

Másodszor, a szabványos blob metaadat-tulajdonságok kivonhatók az alább felsorolt mezőkbe. A blob-indexelő automatikusan létrehoz belső mező-hozzárendeléseket a blob metaadatainak tulajdonságaihoz. Továbbra is hozzá kell adnia az index definícióját használni kívánt mezőket, de kihagyhatja a mezők hozzárendelésének létrehozását az indexelő.

  + **metadata_storage_name** ( `Edm.String` ) – a blob fájlneve. Ha például egy blob/My-Container/My-Folder/subfolder/resume.pdf, a mező értéke `resume.pdf` .

  + **metadata_storage_path** ( `Edm.String` ) – a blob teljes URI-ja, beleértve a Storage-fiókot is. Például: `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** ( `Edm.String` ) – a blob feltöltéséhez használt kód által megadott tartalomtípus. Például: `application/octet-stream`.

  + **metadata_storage_last_modified** ( `Edm.DateTimeOffset` ) – a blob utolsó módosításának időbélyege. Az Azure Cognitive Search ezt az időbélyeget használja a módosított Blobok azonosításához, hogy ne legyenek újraindexelve a kezdeti indexelés után.

  + **metadata_storage_size** ( `Edm.Int64` ) – blob mérete bájtban.

  + **metadata_storage_content_md5** ( `Edm.String` ) – a blob tartalmának MD5-kivonata, ha elérhető.

  + **metadata_storage_sas_token** ( `Edm.String` ) – egy ideiglenes sas-token, amelyet az [egyéni képességek](cognitive-search-custom-skill-interface.md) használhatnak a blobhoz való hozzáféréshez. Ezt a jogkivonatot nem szabad a későbbi használat céljából tárolni, mivel az lejáró lehet.

Végül az indexelő Blobok dokumentum-formátumára jellemző metaadat-tulajdonságok is megadhatók az index sémában. A tartalom-specifikus metaadatokkal kapcsolatos további információkért lásd a [tartalom metaadatainak tulajdonságai](search-blob-metadata-properties.md)című témakört.

Fontos kimutatni, hogy nem kell mezőket meghatároznia a keresési index fenti tulajdonságaihoz – csak rögzítse az alkalmazáshoz szükséges tulajdonságokat.

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>Az indexelt Blobok szabályozása

Megadhatja, hogy mely Blobok indexelve legyenek, és melyeket a rendszer kihagyja, a blob fájltípusa alapján, vagy maguk a Blobok tulajdonságainak beállításával, így az indexelő kihagyhatja őket.

### <a name="include-specific-file-extensions"></a>Adott fájlkiterjesztések belefoglalása

A használatával az `indexedFileNameExtensions` indexhez tartozó fájlkiterjesztés vesszővel tagolt listáját adhatja meg (vezető ponttal). Például csak a érték indexeléséhez. PDF és. DOCX Blobok:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>Adott fájlkiterjesztések kizárása

Ezzel a paranccsal `excludedFileNameExtensions` vesszővel tagolt listát adhat meg a kihagyni kívánt kiterjesztésekről (a vezető ponttal együtt). Például az összes blob indexeléséhez, kivéve a következővel:. PNG és. JPEG-bővítmények:

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

Ha mindkettő `indexedFileNameExtensions` és a `excludedFileNameExtensions` paraméterek is szerepelnek, az indexelő először a következőt keresi: `indexedFileNameExtensions` `excludedFileNameExtensions` . Ha ugyanaz a fájlkiterjesztés mindkét listán szerepel, a rendszer kizárja az indexelésből.

### <a name="add-skip-metadata-the-blob"></a>A "Skip" metaadatok hozzáadása a blobhoz

Az indexelő konfigurációs paraméterei a tárolóban vagy a mappában található összes blobra érvényesek. Néha azt is meg szeretné határozni, hogy az *egyes Blobok* hogyan legyenek indexelve. Ezt úgy teheti meg, hogy hozzáadja a következő metaadat-tulajdonságokat és-értékeket a blob Storage-ban lévő blobokhoz. Ha az indexelő felveszi ezt a tulajdonságot, a rendszer kihagyja a blobot vagy annak tartalmát az indexelési futtatásban.

| Tulajdonság neve | Tulajdonság értéke | Magyarázat |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |Arra utasítja a blob indexelő, hogy teljesen kihagyja a blobot. Sem a metaadatok, sem a tartalom kibontása nem történt meg. Ez akkor hasznos, ha egy adott blob többször meghiúsul, és megszakítja az indexelési folyamatot. |
| `AzureSearch_SkipContent` |`"true"` |Ez egyenértékű a `"dataToExtract" : "allMetadata"` [fent](#PartsOfBlobToIndex) ismertetett beállítással egy adott blobon. |

## <a name="index-large-datasets"></a>Nagyméretű adathalmazok indexelése

Az indexelési Blobok időigényes folyamat lehet. Abban az esetben, ha több millió blobot tartalmaz az indexeléshez, felgyorsíthatja az indexelést, ha particionálja az adatait, és több indexelő használatával [dolgozza fel az adatait párhuzamosan](search-howto-large-index.md#parallel-indexing). Ezt a következőképpen állíthatja be:

+ Az adatai particionálása több blob-tárolóba vagy virtuális mappába

+ Állítson be több adatforrást, egy tárolót vagy egy mappát. Ha egy blob mappára szeretne mutatni, használja a következő `query` paramétert:

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ Hozzon létre egy megfelelő indexelő az egyes adatforrásokhoz. Az összes indexelő ugyanarra a cél keresési indexre kell mutatnia.  

+ A szolgáltatás egy keresési egysége egy adott időpontban képes futtatni egy indexelő szolgáltatást. Több indexelő létrehozása a fent leírtak szerint csak akkor hasznos, ha ténylegesen párhuzamosan futnak.

  Több indexelő párhuzamos futtatásához bővítse a keresési szolgáltatást a megfelelő számú partíció és replika létrehozásával. Ha például a Search szolgáltatás 6 keresési egységgel rendelkezik (például 2 partíciós x 3 replika), akkor 6 indexelő is futhat egyszerre, ami az indexelési teljesítmény hat szorzott növekedését eredményezi. A méretezéssel és a kapacitás megtervezésével kapcsolatos további tudnivalókért tekintse meg [Az Azure Cognitive Search szolgáltatás kapacitásának módosítását](search-capacity-planning.md)ismertető témakört.

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>Hibák kezelése

Az indexelés során leggyakrabban előforduló hibák közé tartoznak a nem támogatott tartalomtípusok, a hiányzó tartalom vagy a túlméretezett Blobok.

Alapértelmezés szerint a blob-indexelő azonnal leáll, ha nem támogatott tartalomtípusú blobot (például egy képet) észlel. A `excludedFileNameExtensions` paraméter használatával kihagyhat bizonyos tartalomtípusokat. Előfordulhat azonban, hogy az indexelést a rendszer akkor is folytatja, ha hibák történnek, majd az egyes dokumentumokat később hibakereséssel végzi. Az indexelő hibáival kapcsolatos további információkért lásd: [gyakori indexelő problémák](search-indexer-troubleshooting.md) és [Indexelő hibák és figyelmeztetések](cognitive-search-common-errors-warnings.md)hibaelhárítása.

### <a name="respond-to-errors"></a>Válaszadás a hibákra

Négy indexelő tulajdonság van, amelyek az indexelő válaszait vezérlik a hibák bekövetkezésekor. Az alábbi példák bemutatják, hogyan állíthatja be ezeket a tulajdonságokat az indexelő definíciójában. Ha már létezik indexelő, ezeket a tulajdonságokat a portál definíciójának szerkesztésével adhatja hozzá.

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` és `"maxFailedItemsPerBatch"`

Az indexelés folytatása, ha a hibák bármilyen feldolgozási ponton történnek, vagy a Blobok elemzése közben vagy a dokumentumok indexbe való felvételekor. Állítsa be ezeket a tulajdonságokat az elfogadható hibák számára. A érték `-1` lehetővé teszi a feldolgozást, függetlenül attól, hogy hány hiba fordul elő. Ellenkező esetben az érték pozitív egész szám.

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` és `"failOnUnprocessableDocument"` 

Egyes Blobok esetében az Azure Cognitive Search nem tudja meghatározni a tartalomtípust, vagy nem tud feldolgozni egy egyébként támogatott tartalomtípusú dokumentumot. A hiba feltételeinek figyelmen kívül hagyásához állítsa be a következő konfigurációs paramétereket `false` :

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>A Relax indexelő megkötései

Megadhatja a [blob-konfiguráció tulajdonságait](/rest/api/searchservice/create-indexer#blob-configuration-parameters) is, amelyek hatékonyan határozzák meg, hogy van-e hiba a feltételben. A következő tulajdonság kihasználhatja a korlátozásokat, és letilthatja az egyébként előforduló hibákat.

+ `"indexStorageMetadataOnlyForOversizedDocuments"` olyan blob-tartalom tárolási metaadatainak indexeléséhez, amely túl nagy a feldolgozáshoz. A túlméretezett Blobok alapértelmezés szerint hibákként vannak kezelve. A Blobok méretére vonatkozó korlátozásokat lásd: [szolgáltatási korlátok](search-limits-quotas-capacity.md).

## <a name="see-also"></a>Lásd még

+ [Indexelők az Azure Cognitive Searchben](search-indexer-overview.md)
+ [Indexelő létrehozása](search-howto-create-indexers.md)
+ [Mesterséges intelligencia Blobok áttekintése](search-blob-ai-integration.md)
+ [A Blobok közötti keresés áttekintése](search-blob-storage-integration.md)