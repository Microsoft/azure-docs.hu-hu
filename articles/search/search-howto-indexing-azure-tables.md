---
title: Keresés az Azure Table Storage-tartalomban
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan indexelheti az Azure Table Storage-ban tárolt Azure Cognitive Search indexelő szolgáltatásait.
manager: nitinme
author: mgottein
ms.author: magottei
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/11/2020
ms.openlocfilehash: 2c67cd4d071660da2ca5714623695ca434329263
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91275183"
---
# <a name="how-to-index-tables-from-azure-table-storage-with-azure-cognitive-search"></a>Táblázatok indexelése az Azure Table Storage-ból az Azure Cognitive Search

Ez a cikk bemutatja, hogyan használható az Azure Cognitive Search az Azure Table Storage-ban tárolt adatindexeléshez.

## <a name="set-up-azure-table-storage-indexing"></a>Az Azure Table Storage indexelésének beállítása

Az alábbi erőforrásokkal állíthatja be az Azure Table Storage indexelő szolgáltatását:

* [Azure Portal](https://ms.portal.azure.com)
* Azure Cognitive Search [REST API](/rest/api/searchservice/Indexer-operations)
* Azure Cognitive Search [.net SDK](/dotnet/api/overview/azure/search)

Itt mutatjuk be a folyamatot a REST API használatával. 

### <a name="step-1-create-a-datasource"></a>1. lépés: adatforrás létrehozása

Az adatforrás meghatározza, hogy mely adatokat kell indexelni, az adatok eléréséhez szükséges hitelesítő adatokat, valamint azokat a házirendeket, amelyek lehetővé teszik az Azure Cognitive Search az adatok változásainak hatékony azonosítását.

A tábla indexeléséhez az adatforrásnak a következő tulajdonságokkal kell rendelkeznie:

- a **Name** a keresési szolgáltatásban található adatforrás egyedi neve.
- a **típusnak a következőnek** kell lennie: `azuretable`
- a **hitelesítő adatok** paraméter tartalmazza a Storage-fiókhoz tartozó kapcsolatok sztringjét. A részletekért tekintse [meg a hitelesítő adatok megadása](#Credentials) szakaszt.
- a **tároló** beállítja a tábla nevét és egy opcionális lekérdezést.
    - Adja meg a tábla nevét a `name` paraméter használatával.
    - Igény szerint megadhat egy lekérdezést a paraméter használatával `query` . 

> [!IMPORTANT] 
> Ha lehetséges, használjon egy szűrőt a PartitionKey a jobb teljesítmény érdekében. Minden más lekérdezés teljes táblázatos vizsgálatot végez, ami a nagyméretű táblák gyenge teljesítményét eredményezi. Lásd a [teljesítménnyel kapcsolatos szempontok](#Performance) szakaszt.


Adatforrás létrehozása:

```http
    POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   
```

További információ a Create DataSource API-ról: [adatforrás létrehozása](/rest/api/searchservice/create-data-source).

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>A hitelesítő adatok megadásának módjai ####

A következő módszerek egyikével megadhatja a táblázat hitelesítő adatait: 

- **Felügyelt identitás kapcsolódási karakterlánca**: `ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;` ehhez a kapcsolódási karakterlánchoz nem szükséges a fiók kulcsa, de a [felügyelt identitás használatával kell végrehajtania egy Azure Storage-fiókhoz való kapcsolódás beállításának](search-howto-managed-identities-storage.md)utasításait.
- **Teljes hozzáférésű Storage-fiók kapcsolati karakterlánca**: a `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` Azure Portal kapcsolati karakterláncát a Storage- **fiók** panel  >  **Beállítások**  >  **kulcsa** (klasszikus Storage-fiókok esetében) vagy a **Beállítások**  >  **elérési kulcsa** (Azure Resource Manager Storage-fiókok esetében) lekérheti.
- **Storage-fiók közös hozzáférési aláírásának kapcsolati karakterlánca**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` a közös hozzáférésű aláírásnak szerepelnie kell a listán, és olvasási engedéllyel kell rendelkeznie a tárolók (ebben az esetben a táblák) és az objektumok (tábla sorai) számára.
-  **Táblázat megosztott hozzáférésének aláírása**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` a közös hozzáférésű aláírásnak lekérdezési (olvasási) engedélyekkel kell rendelkeznie a táblához.

További információ a Storage közös hozzáférésű aláírásáról: a [közös hozzáférésű aláírások használata](../storage/common/storage-sas-overview.md).

> [!NOTE]
> Ha közös hozzáférés-aláírási hitelesítő adatokat használ, az adatforráshoz tartozó hitelesítő adatokat rendszeresen frissíteni kell a megújított aláírásokkal, hogy megakadályozza a lejáratát. Ha a megosztott hozzáférés aláírásának hitelesítő adatai lejárnak, az indexelő a következőhöz hasonló hibaüzenetet küld: "a kapcsolati sztringben megadott hitelesítő adatok érvénytelenek vagy lejártak."  

### <a name="step-2-create-an-index"></a>2. lépés: Index létrehozása
Az index határozza meg a dokumentum, az attribútumok és a keresési élményt formáló egyéb szerkezetek mezőit.

Index létrehozása:

```http
    POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }
```

Az indexek létrehozásával kapcsolatos további információkért lásd: [create index](/rest/api/searchservice/create-index).

### <a name="step-3-create-an-indexer"></a>3. lépés: indexelő létrehozása
Az indexelő Összekapcsol egy adatforrást a cél keresési indexszel, és az Adatfrissítés automatizálására szolgáló ütemtervet biztosít. 

Az index és az adatforrás létrehozása után készen áll az indexelő létrehozására:

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Ez az indexelő két óránként fut. (Az ütemezett időköz értéke "PT2H".) Az indexelő 30 percenkénti futtatásához állítsa az intervallumot "PT30M" értékre. A legrövidebb támogatott időköz öt perc. Az ütemterv nem kötelező; Ha nincs megadva, az indexelő csak egyszer fut a létrehozásakor. Az Indexelő szolgáltatást azonban bármikor futtathatja igény szerint.   

Az indexelő API létrehozásával kapcsolatos további információkért lásd: az [Indexelő létrehozása](/rest/api/searchservice/create-indexer).

Az indexelő-ütemtervek definiálásával kapcsolatos további információkért lásd: [Az Azure Cognitive Search indexelő szolgáltatásának beosztása](search-howto-schedule-indexers.md).

## <a name="deal-with-different-field-names"></a>Más mezőnevek kezelése
Előfordulhat, hogy a meglévő index mezőinek nevei eltérnek a táblában lévő tulajdonságok neveitől. A mező-hozzárendelések segítségével leképezheti a tulajdonságok nevét a táblából a keresési indexben lévő mezőnevek alapján. További információ a mezők-hozzárendelésekről: [Az Azure Cognitive Search indexelő mező-hozzárendelések áthidalják az adatforrások és a keresési indexek közötti különbségeket](search-indexer-field-mappings.md).

## <a name="handle-document-keys"></a>Dokumentum kulcsainak kezelése
Az Azure Cognitive Search a dokumentum kulcsa egyedileg azonosít egy dokumentumot. Minden keresési indexnek pontosan egy típusú Key mezővel kell rendelkeznie `Edm.String` . Az indexhez hozzáadott minden dokumentumhoz meg kell adni a Key mezőt. (Valójában ez az egyetlen kötelező mező.)

Mivel a tábla sorainak összetett kulcsa van, az Azure Cognitive Search létrehoz egy nevű szintetikus mezőt, `Key` amely a partíciós kulcs és a sor kulcsának összefűzése. Ha például egy sor PartitionKey `PK1` és RowKey `RK1` , akkor a `Key` mező értéke `PK1RK1` .

> [!NOTE]
> Az `Key` érték olyan karaktereket tartalmazhat, amelyek érvénytelenek a dokumentum kulcsaiban, például kötőjelek. Az érvénytelen karaktereket a `base64Encode` [mező-hozzárendelési függvény](search-indexer-field-mappings.md#base64EncodeFunction)használatával kezelheti. Ha ezt teszi, ne feledje az URL szempontjából biztonságos Base64-kódolást használni a dokumentumkulcsok átadására a LookUp (Keresés) és hasonló API-hívások esetében is.
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>Növekményes indexelés és törlés észlelése
Ha úgy állítja be a tábla-indexelő, hogy az ütemterv szerint fusson, akkor csak az új vagy frissített sorokat indexeli, ahogy azt egy sor értéke határozza meg `Timestamp` . Nem kell megadnia a változás-észlelési szabályzatot. A növekményes indexelés automatikusan engedélyezve van.

Ha azt szeretné jelezni, hogy bizonyos dokumentumokat el kell távolítani az indexből, használhat Soft delete stratégiát. Egy sor törlése helyett adjon hozzá egy tulajdonságot, amely jelzi, hogy törölve lett, és állítson be egy törlési észlelési házirendet az adatforráshoz. Az alábbi házirend például azt veszi figyelembe, hogy a sor törölve lett, ha a sor tulajdonsága a következő `IsDeleted` értékkel rendelkezik `"true"` :

```http
    PUT https://[service name].search.windows.net/datasources?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   
```

<a name="Performance"></a>
## <a name="performance-considerations"></a>A teljesítménnyel kapcsolatos megfontolások

Alapértelmezés szerint az Azure Cognitive Search a következő lekérdezési szűrőt használja: `Timestamp >= HighWaterMarkValue` . Mivel az Azure-táblák nem rendelkeznek másodlagos indexszel a `Timestamp` mezőhöz, az ilyen típusú lekérdezésekhez teljes táblázatos vizsgálat szükséges, ezért a nagyméretű táblák esetében lassú.


Íme két lehetséges módszer a tábla indexelési teljesítményének javításához. Mindkét módszer a Table Partitions használatára támaszkodik: 

- Ha az adatai természetesen több partícióra is particionálva vannak, hozzon létre egy adatforrást és egy megfelelő indexelő az egyes partíciós tartományokhoz. Minden indexelő mostantól csak egy adott partíciós tartományt dolgoz fel, ami jobb lekérdezési teljesítményt eredményez. Ha az indexelni kívánt adat kis számú rögzített partícióval rendelkezik, még jobbá is: minden indexelő csak a partíciók vizsgálatát végzi el. Ha például egy olyan adatforrást szeretne létrehozni, amely a-ből származó kulcsokat tartalmazó partíciós tartományt dolgozza fel `000` `100` , a következőhöz hasonló lekérdezést használjon: 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- Ha az adatait idő szerint particionálja (például naponta vagy hetente létrehoz egy új partíciót), vegye figyelembe a következő megközelítést: 
    - Használja az űrlap lekérdezését: `(PartitionKey ge <TimeStamp>) and (other filters)` . 
    - Figyelje az indexelő állapotát az [Indexelő állapot API beolvasása](/rest/api/searchservice/get-indexer-status)szolgáltatással, és rendszeres időközönként frissítse a `<TimeStamp>` lekérdezés feltételét a legújabb sikeres magas vízjelek érték alapján. 
    - Ha ezzel a módszerrel teljes újraindexelést kell elindítania, alaphelyzetbe kell állítania az adatforrás-lekérdezést az indexelő alaphelyzetbe állítása mellett. 


## <a name="help-us-make-azure-cognitive-search-better"></a>Segítsen nekünk, hogy jobban megtegyük az Azure Cognitive Search
Ha a szolgáltatással kapcsolatos kérések vagy ötletek vannak, küldje el azokat a [UserVoice webhelyen](https://feedback.azure.com/forums/263029-azure-search/).