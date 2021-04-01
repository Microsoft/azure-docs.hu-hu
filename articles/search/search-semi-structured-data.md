---
title: 'Oktatóanyag: részben strukturált adathalmazok indexelése JSON-blobokban'
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan indexelheti és keresheti meg a félig strukturált Azure JSON-blobokat az Azure Cognitive Search REST API-k és a Poster használatával.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/25/2021
ms.openlocfilehash: a7a010e3c60d6b96947597878fcd870e9845b2b3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746137"
---
# <a name="tutorial-index-json-blobs-from-azure-storage-using-rest"></a>Oktatóanyag: JSON-Blobok indexelése az Azure Storage-ból REST használatával

Az Azure Cognitive Search képes indexelni az Azure Blob Storage-ban található JSON-dokumentumokat és-tömböket olyan [Indexelő](search-indexer-overview.md) használatával, amely képes a részben strukturált információk beolvasására. A részben strukturált adatok címkéket és jelölőket tartalmaznak, amelyek a tartalmakat választják el az adatokon belül. Feldarabolja a strukturálatlan adatmennyiségek közötti különbséget, amelyeknek teljes mértékben indexelve kell lenniük, és az olyan, az adatmodellbe (például egy olyan kapcsolati adatbázis-sémához) tartozó, formálisan strukturált adat, amely egy mező alapján indexelhető.

Ez az oktatóanyag a Poster és a [Search REST API](/rest/api/searchservice/) -k használatával hajtja végre a következő feladatokat:

> [!div class="checklist"]
> * Azure Cognitive Search-adatforrás konfigurálása Azure Blob-tárolóhoz
> * Kereshető tartalmat tartalmazó Azure Cognitive Search index létrehozása
> * Indexelő konfigurálása és futtatása a tároló olvasásához és a kereshető tartalom kinyeréséhez az Azure Blob Storage-ból
> * Keresés az újonnan létrehozott indexben

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Storage](../storage/common/storage-account-create.md)
+ [Postman asztali alkalmazás](https://www.getpostman.com/)
+ [Meglévő keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást. Az ingyenes keresési szolgáltatás három indexre, három indexelő elemre és három adatforrásra korlátozza a szolgáltatást. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a szolgáltatásban az új erőforrások elfogadására szolgáló helyiséggel.

## <a name="download-files"></a>Fájlok letöltése

[Clinical-trials-json.zip](https://github.com/Azure-Samples/storage-blob-integration-with-cdn-search-hdi/raw/master/clinical-trials-json.zip) az oktatóanyagban használt adatkészleteket tartalmazza. Töltse le és csomagolja ki a fájlt a saját mappájába. Az adatok a [clinicaltrials.gov](https://clinicaltrials.gov/ct2/results)-ből származnak, és a JSON formátumba konvertálódnak erre az oktatóanyagra.

## <a name="1---create-services"></a>1 – szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Searcht használja az indexeléshez és a lekérdezésekhez, valamint az Azure Blob Storage-hoz az adatkezeléshez. 

Ha lehetséges, hozzon létre mindkettőt ugyanabban a régióban és erőforráscsoporthoz a közelség és a kezelhetőség érdekében. A gyakorlatban az Azure Storage-fiók bármely régióban lehet.

### <a name="start-with-azure-storage"></a>Első lépések az Azure Storage-ban

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/) , és kattintson az **+ erőforrás létrehozása** elemre.

1. Keressen rá a *Storage-fiókra* , és válassza ki a Microsoft Storage-fiók ajánlatát.

   :::image type="content" source="media/cognitive-search-tutorial-blob/storage-account.png" alt-text="Storage-fiók létrehozása" border="false":::

1. Az alapok lapon a következő elemek szükségesek. Minden más esetében fogadja el az alapértelmezett értékeket.

   + **Erőforráscsoport**. Válasszon ki egy meglévőt, vagy hozzon létre egy újat, de ugyanazt a csoportot használja az összes szolgáltatáshoz, hogy együtt lehessen kezelni őket.

   + **Tárfiók neve**. Ha úgy gondolja, hogy több erőforrása is van ugyanazzal a típussal, használja a nevet típus és régió szerint egyértelműsítse, például *blobstoragewestus*. 

   + **Hely**. Ha lehetséges, válassza ki ugyanazt a helyet, amelyet az Azure Cognitive Search és Cognitive Services használ. Egyetlen hely érvényteleníti A sávszélességgel kapcsolatos díjakat.

   + **Fiók típusa**. Válassza ki az alapértelmezett *StorageV2 (általános célú v2)*.

1. A szolgáltatás létrehozásához kattintson a **felülvizsgálat + létrehozás** lehetőségre.

1. A létrehozás után kattintson **az erőforrás** megnyitása lehetőségre az Áttekintés lap megnyitásához.

1. Kattintson a **Blobok** szolgáltatás elemre.

1. [Hozzon létre egy BLOB-tárolót](../storage/blobs/storage-quickstart-blobs-portal.md) a mintaadatok tárolásához. Megadhatja a nyilvános hozzáférési szintet bármelyik érvényes értékéhez.

1. A tároló létrehozása után nyissa meg, majd válassza a parancssáv **feltöltés** elemét.

   :::image type="content" source="media/search-semi-structured-data/upload-command-bar.png" alt-text="Feltöltés a parancssáv" border="false":::

1. Navigáljon a minta fájlokat tartalmazó mappához. Jelölje ki az összeset, majd kattintson a **feltöltés** elemre.

   :::image type="content" source="media/search-semi-structured-data/clinicalupload.png" alt-text="Fájlok feltöltése" border="false":::

Ha befejeződött a feltöltés, a fájlok a saját almappájukban jelennek meg az adattárolóban.

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A következő erőforrás az Azure Cognitive Search, amelyet [a portálon lehet létrehozni](search-create-service-portal.md). A bemutató elvégzéséhez használhatja az ingyenes szintet. 

Ahogy az Azure Blob Storage-hoz, szánjon egy kis időt a hozzáférési kulcs gyűjtésére. Tovább, amikor megkezdi a kérelmek strukturálását, meg kell adnia az egyes kérések hitelesítéséhez használt Endpoint és admin API-kulcsot.

### <a name="get-a-key-and-url"></a>Kulcs és URL-cím lekérése

A REST-hívásokhoz minden kérésének tartalmaznia kell a szolgáltatás URL-címét és egy hozzáférési kulcsot. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   :::image type="content" source="media/search-get-started-rest/get-url-key.png" alt-text="HTTP-végpont és elérési kulcs beszerzése" border="false":::

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kéréshez. Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="2---set-up-postman"></a>2 – Poster beállítása

Indítsa el a Postmant, és hozzon létre egy HTTP-kérelmet. Ha nem ismeri ezt az eszközt, tekintse meg [a keresési index létrehozása REST API](search-get-started-rest.md)-k használatával című témakört.

Az oktatóanyag minden hívására vonatkozó kérési metódusok **post** és **Get**. A keresési szolgáltatás három API-hívást hajt végre egy adatforrás, egy index és egy indexelő létrehozásához. Az adatforrás tartalmaz egy, a tárfiókjára irányuló mutatót és a JSON-adatait. A keresési szolgáltatás az adatok betöltésekor hozza létre a kapcsolatot.

A fejlécekben a "Content-Type" értéket állítsa be, `application/json` és állítsa `api-key` az Azure Cognitive Search szolgáltatás felügyeleti API-kulcsára. Miután beállította a fejléceket, használhatja azokat minden kérelemhez ebben a gyakorlatban.

  :::image type="content" source="media/search-get-started-rest/postman-url.png" alt-text="Poster-kérelem URL-címe és fejléce" border="false":::

Az URI-k API-verziót kell megadni, és minden hívásnak egy **201**-as értéket kell visszaadnia. A JSON-tömbök használatának általánosan elérhető API-verziója a következő: `2020-06-30` .

## <a name="3---create-a-data-source"></a>3 – adatforrás létrehozása

Az [adatforrás létrehozása API](/rest/api/searchservice/create-data-source) létrehoz egy Azure Cognitive Search objektumot, amely meghatározza, hogy milyen adatindexet szeretne.

1. A hívás végpontjának beállítása a következőre: `https://[service name].search.windows.net/datasources?api-version=2020-06-30` . Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére. 

1. Másolja a következő JSON-t a kérelem törzsébe.

    ```json
    {
        "name" : "clinical-trials-json-ds",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=[storage account name];AccountKey=[storage account key];" },
        "container" : { "name" : "[blob container name]"}
    }
    ```

1. Cserélje le a kapcsolatok karakterláncát érvényes sztringre a fiókjához.

1. Cserélje le a "[blob Container name]" sort a mintaadatok számára létrehozott tárolóra. 

1. Küldje el a kérést. A válasznak így kell kinéznie:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#datasources/$entity",
        "@odata.etag": "\"0x8D505FBC3856C9E\"",
        "name": "clinical-trials-json-ds",
        "description": null,
        "type": "azureblob",
        "subtype": null,
        "credentials": {
            "connectionString": "DefaultEndpointsProtocol=https;AccountName=[mystorageaccounthere];AccountKey=[[myaccountkeyhere]]];"
        },
        "container": {
            "name": "[mycontainernamehere]",
            "query": null
        },
        "dataChangeDetectionPolicy": null,
        "dataDeletionDetectionPolicy": null
    }
    ```

## <a name="4---create-an-index"></a>4 – index létrehozása

A második hívás [index API-t hoz létre](/rest/api/searchservice/create-index), amely egy Azure Cognitive Search indexet hoz létre, amely az összes kereshető adattal tárolja. Az index határozza meg az összes paramétert és ezek attribútumait.

1. A hívás végpontjának beállítása a következőre: `https://[service name].search.windows.net/indexes?api-version=2020-06-30` . Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

1. Másolja a következő JSON-t a kérelem törzsébe.

    ```json
    {
      "name": "clinical-trials-json-index",  
      "fields": [
      {"name": "FileName", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "Description", "type": "Edm.String", "searchable": true, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MinimumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Title", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "URL", "type": "Edm.String", "searchable": false, "retrievable": false, "facetable": false, "filterable": false, "sortable": false},
      {"name": "MyURL", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "Gender", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "MaximumAge", "type": "Edm.Int32", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": true},
      {"name": "Summary", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": false, "sortable": false},
      {"name": "NCTID", "type": "Edm.String", "key": true, "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": true},
      {"name": "Phase", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Date", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": false, "filterable": false, "sortable": true},
      {"name": "OverallStatus", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "OrgStudyId", "type": "Edm.String", "searchable": true, "retrievable": true, "facetable": false, "filterable": true, "sortable": false},
      {"name": "HealthyVolunteers", "type": "Edm.String", "searchable": false, "retrievable": true, "facetable": true, "filterable": true, "sortable": false},
      {"name": "Keywords", "type": "Collection(Edm.String)", "searchable": true, "retrievable": true, "facetable": true, "filterable": false, "sortable": false},
      {"name": "metadata_storage_last_modified", "type":"Edm.DateTimeOffset", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_storage_size", "type":"Edm.String", "searchable": false, "retrievable": true, "filterable": true, "sortable": false},
      {"name": "metadata_content_type", "type":"Edm.String", "searchable": true, "retrievable": true, "filterable": true, "sortable": false}
      ]
    }
   ```

1. Küldje el a kérést. A válasznak így kell kinéznie:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexes/$entity",
        "@odata.etag": "\"0x8D505FC00EDD5FA\"",
        "name": "clinical-trials-json-index",
        "fields": [
            {
                "name": "FileName",
                "type": "Edm.String",
                "searchable": false,
                "filterable": false,
                "retrievable": true,
                "sortable": true,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            {
                "name": "Description",
                "type": "Edm.String",
                "searchable": true,
                "filterable": false,
                "retrievable": false,
                "sortable": false,
                "facetable": false,
                "key": false,
                "indexAnalyzer": null,
                "searchAnalyzer": null,
                "analyzer": null,
                "synonymMaps": []
            },
            ...
          }
    ```

## <a name="5---create-and-run-an-indexer"></a>5 – indexelő létrehozása és futtatása

Az indexelő csatlakozik az adatforráshoz, importálja az adatmennyiséget a cél keresési indexbe, és opcionálisan biztosít egy ütemtervet az Adatfrissítés automatizálásához. A REST API [Indexelő létrehozása](/rest/api/searchservice/create-indexer).

1. Állítsa be a hívás URI-JÁT a következőre: `https://[service name].search.windows.net/indexers?api-version=2020-06-30` . Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

1. Másolja a következő JSON-t a kérelem törzsébe.

    ```json
    {
      "name" : "clinical-trials-json-indexer",
      "dataSourceName" : "clinical-trials-json-ds",
      "targetIndexName" : "clinical-trials-json-index",
      "parameters" : { "configuration" : { "parsingMode" : "jsonArray" } }
    }
    ```

1. Küldje el a kérést. A kérés feldolgozása azonnal megtörténik. Ha a válasz visszatér, egy teljes szöveges kereshető indextel fog rendelkezni. A válasznak így kell kinéznie:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/$metadata#indexers/$entity",
        "@odata.etag": "\"0x8D505FDE143D164\"",
        "name": "clinical-trials-json-indexer",
        "description": null,
        "dataSourceName": "clinical-trials-json-ds",
        "targetIndexName": "clinical-trials-json-index",
        "schedule": null,
        "parameters": {
            "batchSize": null,
            "maxFailedItems": null,
            "maxFailedItemsPerBatch": null,
            "base64EncodeKeys": null,
            "configuration": {
                "parsingMode": "jsonArray"
            }
        },
        "fieldMappings": [],
        "enrichers": [],
        "disabled": null
    }
    ```

## <a name="6---search-your-json-files"></a>6 – JSON-fájlok keresése

Az első dokumentum betöltését követően megkezdheti a keresést.

1. Módosítsa a **lekérdezni** kívánt műveletet.

1. Állítsa be a hívás URI-JÁT a következőre: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&api-version=2020-06-30&$count=true` . Cserélje le a `[service name]` elemet a keresési szolgáltatás nevére.

1. Küldje el a kérést. Ez egy nem megadott teljes szöveges keresési lekérdezés, amely visszaadja az indexben beolvasható összes mezőt, valamint a dokumentumok számát. A válasznak így kell kinéznie:

    ```json
    {
        "@odata.context": "https://exampleurl.search.windows.net/indexes('clinical-trials-json-index')/$metadata#docs(*)",
        "@odata.count": 100,
        "value": [
            {
                "@search.score": 1.0,
                "FileName": "NCT00000102.txt",
                "MinimumAge": 14,
                "Title": "Congenital Adrenal Hyperplasia: Calcium Channels as Therapeutic Targets",
                "MyURL": "https://azure.storagedemos.com/clinical-trials/NCT00000102.txt",
                "Gender": "Both",
                "MaximumAge": 35,
                "Summary": "This study will test the ability of extended release nifedipine (Procardia XL), a blood pressure medication, to permit a decrease in the dose of glucocorticoid medication children take to treat congenital adrenal hyperplasia (CAH).",
                "NCTID": "NCT00000102",
                "Phase": "Phase 1/Phase 2",
                "Date": "ClinicalTrials.gov processed this data on October 25, 2016",
                "OverallStatus": "Completed",
                "OrgStudyId": "NCRR-M01RR01070-0506",
                "HealthyVolunteers": "No",
                "Keywords": [],
                "metadata_storage_last_modified": "2019-04-09T18:16:24Z",
                "metadata_storage_size": "33060",
                "metadata_content_type": null
            },
            . . . 
    ```

1. Adja hozzá a `$select` lekérdezési paramétert az eredmények kevesebb mezőre való korlátozásához: `https://[service name].search.windows.net/indexes/clinical-trials-json-index/docs?search=*&$select=Gender,metadata_storage_size&api-version=2020-06-30&$count=true` .  Ehhez a lekérdezéshez a 100-es dokumentumok egyeznek, de alapértelmezés szerint az Azure Cognitive Search csak a 50 értéket adja vissza az eredményekben.

   :::image type="content" source="media/search-semi-structured-data/lastquery.png" alt-text="Paraméteres lekérdezés" border="false":::

1. Példa összetettebb lekérdezésre `$filter=MinimumAge ge 30 and MaximumAge lt 75` , amely csak azokat az eredményeket adja vissza, amelyekben a paraméterek minimális értéke nagyobb vagy egyenlő, mint 30, a maximális érték pedig 75. Cserélje le a `$select` kifejezést a `$filter` kifejezésre.

   :::image type="content" source="media/search-semi-structured-data/metadatashort.png" alt-text="Részben strukturált keresés" border="false":::

A logikai operátorok (és, vagy, nem) és az összehasonlító operátorok (EQ, ne, gt, lt, GE, le) is használhatók. A sztring-összehasonlítások megkülönböztetik a kis- és nagybetűket. További információkat és példákat az [egyszerű lekérdezés létrehozása](search-query-simple-examples.md)című témakörben talál.

> [!NOTE]
> A `$filter` paraméter csak olyan metaadatokkal működik, amelyek szűrhetőként lettek megjelölve az index létrehozásakor.

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszaiban a tervezési iteráció legalkalmasabb megközelítése az objektumok törlése az Azure Cognitive Search és a kód újraépítésének engedélyezése. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

A portál használatával törölhet indexeket, indexelő fájlokat és adatforrásokat. Vagy használja a **delete (Törlés** ) lehetőséget, és adja meg az egyes objektumok URL-címét A következő parancs törli az indexelő.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/clinical-trials-json-indexer?api-version=2020-06-30
```

Sikeres törlés esetén a rendszer a 204-es állapotkódot adja vissza.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, a projekt végén érdemes lehet eltávolítani a már nem szükséges erőforrásokat. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a minden erőforrás vagy erőforráscsoport hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri az Azure Blob-indexelés alapjait, ismerkedjen meg részletesebben a JSON-Blobok indexelési konfigurációjával az Azure Storage-ban.

> [!div class="nextstepaction"]
> [JSON-blob indexelésének konfigurálása](search-howto-index-json-blobs.md)