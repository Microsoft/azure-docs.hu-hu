---
title: 'Oktatóanyag: Python és AI az Azure-blobokon'
titleSuffix: Azure Cognitive Search
description: A Jupyter Python notebook és az Azure Cognitive Search REST API-k használatával a blob Storage-ban található tartalommal kapcsolatos szöveg-kinyerési és természetes nyelvi feldolgozás példája.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.devlang: python
ms.topic: tutorial
ms.date: 02/26/2020
ms.openlocfilehash: 743c6fa6545342f2a7655059016637fc54158b64
ms.sourcegitcommit: 1f738a94b16f61e5dad0b29c98a6d355f724a2c7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2020
ms.locfileid: "78164015"
---
# <a name="tutorial-use-python-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Oktatóanyag: kereshető tartalom előállítása az Azure-blobokból a Python és a AI használatával

Ha strukturálatlan szöveget vagy rendszerképeket használ az Azure Blob Storage-ban, egy [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítési folyamat kinyerheti az adatokat, és létrehozhat olyan új tartalmakat, amelyek hasznosak a teljes szöveges kereséshez és az adatbányászati forgatókönyvekhez. Bár a folyamatok feldolgozhatják a lemezképeket, ez a Python-oktatóanyag a szövegre, a nyelvfelismerés és a természetes nyelvi feldolgozás alkalmazására koncentrál, hogy új mezőket hozzon létre, amelyeket a lekérdezések, a dimenziók és a szűrők használhatnak.

Ebben az oktatóanyagban a Python és a [Rest](https://docs.microsoft.com/rest/api/searchservice/) használatával hajtsa végre a következő feladatokat:

> [!div class="checklist"]
> * Az Azure Blob Storage-ban teljes dokumentumokkal (strukturálatlan szöveggel), például PDF-, HTML-, DOCX-és PPTX-verziókkal kezdheti meg a használatot.
> * Definiáljon egy olyan folyamatot, amely kibontja a szöveget, észleli a nyelvet, felismeri az entitásokat, és észleli a legfontosabb kifejezéseket.
> * Definiáljon egy indexet a kimenet (nyers tartalom, valamint a folyamat által generált név-érték párok) tárolására.
> * A folyamat végrehajtásával megkezdheti az átalakításokat és az elemzést, valamint az index létrehozását és betöltését.
> * A teljes szöveges kereséssel és a részletes lekérdezési szintaxissal megismerheti az eredményeket.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt nyisson meg egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Anaconda 3,7](https://www.anaconda.com/distribution/#download-section)
+ [Meglévő keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást. Az ingyenes keresési szolgáltatás három indexre, három indexelő elemre és három adatforrásra korlátozza a szolgáltatást. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a szolgáltatásban az új erőforrások elfogadására szolgáló helyiséggel.

## <a name="download-files"></a>Fájlok letöltése

1. Nyissa meg ezt a [OneDrive mappát](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) , és a bal felső sarokban kattintson a **Letöltés** elemre a fájlok számítógépre másolásához. 

1. Kattintson a jobb gombbal a zip-fájlra, és válassza az **összes kibontása**lehetőséget. A különböző típusok 14 fájlból állnak. Ehhez a gyakorlathoz a 7-et fogja használni.

## <a name="1---create-services"></a>1 – szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Searcht használja az indexeléshez és a lekérdezésekhez, Cognitive Services a mesterséges intelligencia-bővítéshez és az Azure Blob Storage-hoz, hogy megadja az adatforrásokat. Ez az oktatóanyag napi 20 tranzakció ingyenes kiosztása alatt marad Cognitive Serviceson, így az egyetlen szükséges szolgáltatás a keresés és a tárolás.

Ha lehetséges, hozzon létre mindkettőt ugyanabban a régióban és erőforráscsoporthoz a közelség és a kezelhetőség érdekében. A gyakorlatban az Azure Storage-fiók bármely régióban lehet.

### <a name="start-with-azure-storage"></a>Első lépések az Azure Storage-ban

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/) , és kattintson az **+ erőforrás létrehozása**elemre.

1. Keressen rá a *Storage-fiókra* , és válassza ki a Microsoft Storage-fiók ajánlatát.

   ![Storage-fiók létrehozása](media/cognitive-search-tutorial-blob/storage-account.png "Storage-fiók létrehozása")

1. Az alapok lapon a következő elemek szükségesek. Minden más esetében fogadja el az alapértelmezett értékeket.

   + **Erőforráscsoport**. Válasszon ki egy meglévőt, vagy hozzon létre egy újat, de ugyanazt a csoportot használja az összes szolgáltatáshoz, hogy együtt lehessen kezelni őket.

   + A **Storage-fiók neve**. Ha úgy gondolja, hogy több erőforrása is van ugyanazzal a típussal, használja a nevet típus és régió szerint egyértelműsítse, például *blobstoragewestus*. 

   + **Hely**. Ha lehetséges, válassza ki ugyanazt a helyet, amelyet az Azure Cognitive Search és Cognitive Services használ. Egyetlen hely érvényteleníti A sávszélességgel kapcsolatos díjakat.

   + **Fiók típusa**. Válassza ki az alapértelmezett *StorageV2 (általános célú v2)* .

1. A szolgáltatás létrehozásához kattintson a **felülvizsgálat + létrehozás** lehetőségre.

1. A létrehozás után kattintson **az erőforrás** megnyitása lehetőségre az Áttekintés lap megnyitásához.

1. Kattintson a **Blobok** szolgáltatás elemre.

1. Kattintson a **+ tároló** elemre egy tároló létrehozásához, és nevezze el a *fogaskerék-Search-demo kifejezést*.

1. Válassza a *fogaskerék-keresés-bemutató* lehetőséget, majd kattintson a **feltöltés** gombra, és nyissa meg azt a mappát, ahová a letöltött fájlokat mentette. Válassza ki az összes nem képfájlt. 7 fájlnak kell lennie. A feltöltéshez kattintson **az OK** gombra.

   ![Minta fájlok feltöltése](media/cognitive-search-tutorial-blob/sample-files.png "Minta fájlok feltöltése")

1. Mielőtt elkezdené az Azure Storage-t, szerezzen be egy kapcsolatok karakterláncot, hogy az Azure Cognitive Searchban is létrehozhat egy kapcsolatokat. 

   1. Lépjen vissza a Storage-fiók áttekintés lapjára (az általunk használt *blobstragewestus* példaként használták). 
   
   1. A bal oldali navigációs panelen válassza a **hozzáférési kulcsok** lehetőséget, és másolja a kapcsolati karakterláncok egyikét. 

   A kapcsolódási karakterlánc az alábbi példához hasonló URL-cím:

      ```http
      DefaultEndpointsProtocol=https;AccountName=cogsrchdemostorage;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Mentse a kapcsolódási karakterláncot a Jegyzettömbbe. Később szüksége lesz rá az adatforrás-kapcsolatok beállításakor.

### <a name="cognitive-services"></a>Cognitive Services

A mesterséges intelligenciát Cognitive Services támogatja, beleértve a természetes nyelv és a képfeldolgozás Text Analytics és Computer Vision. Ha a cél egy tényleges prototípus vagy projekt teljesítése volt, akkor Cognitive Services (ugyanabban a régióban, mint az Azure Cognitive Search), hogy az indexelési műveletekhez csatolható legyen.

Ennél a gyakorlatnál azonban kihagyhatja az erőforrások kiosztását, mivel az Azure Cognitive Search képes csatlakozni a háttérben a Cognitive Serviceshoz, és az indexelő futtatásakor 20 ingyenes tranzakciót biztosít. Mivel ez az oktatóanyag 7 tranzakciót használ, az ingyenes kiosztás elegendő. Nagyobb projektek esetében tervezze meg Cognitive Services kiépítés az utólagos elszámolású S0 szinten. További információ: [Cognitive Services csatolása](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A harmadik összetevő az Azure Cognitive Search, amelyet [a portálon lehet létrehozni](search-create-service-portal.md). A bemutató elvégzéséhez használhatja az ingyenes szintet. 

Ahogy az Azure Blob Storage-hoz, szánjon egy kis időt a hozzáférési kulcs gyűjtésére. Tovább, amikor megkezdi a kérelmek strukturálását, meg kell adnia az egyes kérések hitelesítéséhez használt Endpoint és admin API-kulcsot.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Rendszergazdai API-kulcs és URL-cím beszerzése az Azure Cognitive Search

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon szerezze be a keresési szolgáltatás nevét. A szolgáltatás nevét a végpont URL-címének áttekintésével ellenőrizheti. Ha a végpont URL-címe `https://mydemo.search.windows.net`volt, a szolgáltatás neve `mydemo`.

2. A **beállítások** > **kulcsok**területen kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

   ![A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése](media/search-get-started-nodejs/service-name-and-keys.png)

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kérelem fejlécében. Egy érvényes kulcs a kérést küldő alkalmazás és az azt kezelő szolgáltatás közötti megbízhatósági kapcsolatot hoz létre a kérelmek alapján.

## <a name="2---start-a-notebook"></a>2 – jegyzetfüzet indítása

Hozza létre a jegyzetfüzetet az alábbi utasítások alapján, vagy töltsön le egy befejezett jegyzetfüzetet az [Azure-Search-Python-Samples](https://github.com/Azure-Samples/azure-search-python-samples/tree/master/Tutorial-AI-Enrichment)adattárból.

A anaconda Navigator használatával indítsa el Jupyter Notebook és hozzon létre egy új Python 3 jegyzetfüzetet.

A jegyzetfüzetben futtassa ezt a szkriptet a JSON használatához használt könyvtárak betöltéséhez és a HTTP-kérések összeállításához.

```python
import json
import requests
from pprint import pprint
```

Ugyanebben a jegyzetfüzetben adja meg az adatforrás, az index, az indexelő és a készségkészlet nevét. Futtassa ezt a szkriptet az oktatóanyag nevének beállításához.

```python
# Define the names for the data source, skillset, index and indexer
datasource_name = "cogsrch-py-datasource"
skillset_name = "cogsrch-py-skillset"
index_name = "cogsrch-py-index"
indexer_name = "cogsrch-py-indexer"
```

A következő parancsfájlban cserélje le a keresési szolgáltatás (a-SEARCH-SERVICE-NAME) és a felügyeleti API-kulcs (a-ADMIN-API-kulcs) helyőrzőit, majd futtassa a keresési szolgáltatás végpontjának beállításához.

```python
# Setup the endpoint
endpoint = 'https://<YOUR-SEARCH-SERVICE-NAME>.search.windows.net/'
headers = {'Content-Type': 'application/json',
           'api-key': '<YOUR-ADMIN-API-KEY>'}
params = {
    'api-version': '2019-05-06'
}
```

## <a name="3---create-the-pipeline"></a>3 – a folyamat létrehozása

Az Azure Cognitive Searchban az AI-feldolgozás az indexelés (vagy az adatfeldolgozás) során történik. Az útmutató ezen része négy objektumot hoz létre: adatforrás, index definíció, készségkészlet, indexelő. 

### <a name="step-1-create-a-data-source"></a>1\. lépés: Adatforrás létrehozása

Az [adatforrás-objektumok](https://docs.microsoft.com/rest/api/searchservice/create-data-source) biztosítják a kapcsolódási karakterláncot a fájlokat tartalmazó blob-tárolóhoz.

A következő parancsfájlban cserélje le a helyőrzőt a-BLOB-RESOURCE-replace-STRING értékre az előző lépésben létrehozott blobhoz tartozó kapcsolatok karakterláncával. Cserélje le a tároló helyőrző szövegét. Ezután futtassa a parancsfájlt egy `cogsrch-py-datasource`nevű adatforrás létrehozásához.

```python
# Create a data source
datasourceConnectionString = "<YOUR-BLOB-RESOURCE-CONNECTION-STRING>"
datasource_payload = {
    "name": datasource_name,
    "description": "Demo files to demonstrate cognitive search capabilities.",
    "type": "azureblob",
    "credentials": {
        "connectionString": datasourceConnectionString
    },
    "container": {
        "name": "<YOUR-BLOB-CONTAINER-NAME>"
    }
}
r = requests.put(endpoint + "/datasources/" + datasource_name,
                 data=json.dumps(datasource_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelemnek 201-as állapotkódot kell visszaadnia.

A Azure Portal keresési szolgáltatás irányítópultján ellenőrizze, hogy az **adatforrások** listában megjelenik-e az cogsrch-adatforrás. A lap frissítéséhez kattintson a **frissítés** gombra.

![Adatforrások csempéje a portálon](./media/cognitive-search-tutorial-blob-python/py-data-source-tile.png "Adatforrások csempéje a portálon")

### <a name="step-2-create-a-skillset"></a>2\. lépés: készségkészlet létrehozása

Ebben a lépésben az adataira vonatkozó dúsítási lépések készletét fogja meghatározni. Minden bővítési lépés egy *képesség*, a bővítési lépések készlete pedig a *képességcsoport*. Ez az oktatóanyag [beépített kognitív képességeket](cognitive-search-predefined-skills.md) használ a készségkészlet:

+ [Entitások felismerése](cognitive-search-skill-entity-recognition.md) a blob-tároló tartalmából származó szervezetek nevének kinyeréséhez.

+ [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

+ A [szöveg felosztásával](cognitive-search-skill-textsplit.md) a nagyméretű tartalmakat kisebb darabokra bontja, mielőtt meghívná a kulcskifejezések kinyerését. A kulcskifejezések kinyerése legfeljebb 50 000 karakter méterű bemeneteket fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

+ A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket. 

Futtassa a következő szkriptet egy `cogsrch-py-skillset`nevű készségkészlet létrehozásához.

```python
# Create a skillset
skillset_payload = {
    "name": skillset_name,
    "description":
    "Extract entities, detect language and extract key-phrases",
    "skills":
    [
        {
            "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
            "categories": ["Organization"],
            "defaultLanguageCode": "en",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "organizations", "targetName": "organizations"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "inputs": [
                {
                    "name": "text", "source": "/document/content"
                }
            ],
            "outputs": [
                {
                    "name": "languageCode",
                    "targetName": "languageCode"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
            "textSplitMode": "pages",
            "maximumPageLength": 4000,
            "inputs": [
                {
                    "name": "text",
                    "source": "/document/content"
                },
                {
                    "name": "languageCode",
                    "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "textItems",
                    "targetName": "pages"
                }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/pages/*",
            "inputs": [
                {
                    "name": "text", "source": "/document/pages/*"
                },
                {
                    "name": "languageCode", "source": "/document/languageCode"
                }
            ],
            "outputs": [
                {
                    "name": "keyPhrases",
                    "targetName": "keyPhrases"
                }
            ]
        }
    ]
}

r = requests.put(endpoint + "/skillsets/" + skillset_name,
                 data=json.dumps(skillset_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelemnek 201-as állapotkódot kell visszaadnia.

Az egyes lapokon a kinyerési képességre vonatkozó kifejezést alkalmazzuk. Ha a környezetet úgy állítja be, hogy `"document/pages/*"`, akkor ezt a dúsítást a dokumentum/oldal tömb minden egyes tagja számára futtatja (a dokumentum minden oldalához).

Minden képesség a dokumentum tartalmán fut le. A feldolgozás során az Azure Cognitive Search kihasználja az egyes dokumentumokat a különböző fájlformátumokból származó tartalmak olvasásához. A forrásfájlban található szöveg egy `content` mezőbe kerül, amely minden dokumentumhoz egy. Ezért `"/document/content"`ként állítsa be a bemenetet.

Alább a képességcsoport grafikai ábrázolása látható.

![Készségkészlet megismerése](media/cognitive-search-tutorial-blob/skillset.png "Készségkészlet megismerése")

A kimenetek leképezhetők egy indexre, amely bemenetként használható egy alsóbb rétegbeli képességhez vagy mindkettőhöz, akárcsak a nyelvi kóddal. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához.

A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>3\. lépés: index létrehozása

Ebben a szakaszban megadhatja az index sémát úgy, hogy megadja a kereshető indexbe felvenni kívánt mezőket, és beállítja az egyes mezők keresési attribútumait. A mezők típussal is rendelkeznek, emellett olyan attribútumokat tartalmazhatnak, amelyek meghatározzák a mező használatának módját (kereshető, rendezhető stb.). Az indexben szereplő mezőneveknek nem kell pontosan megegyezniük a forrásban szereplő mezők nevével. Egy későbbi lépésben mezőleképezéseket fog hozzáadni egy indexelőhöz a forrás-cél mezőkhöz való csatlakozás céljából. Ebben a lépésben a keresőalkalmazásra vonatkozó mezőelnevezési konvenciók használatával határozza meg az indexet.

A gyakorlat során az alábbi mezőket és mezőtípusokat használjuk:

| mezőnevek: | id         | content   | languageCode | keyPhrases         | organizations     |
|--------------|----------|-------|----------|--------------------|-------------------|
| mezőtípusok: | Edm.String|Edm.String| Edm.String| List<Edm.String>  | List<Edm.String>  |

Futtassa ezt a szkriptet a `cogsrch-py-index`nevű index létrehozásához.

```python
# Create an index
index_payload = {
    "name": index_name,
    "fields": [
        {
            "name": "id",
            "type": "Edm.String",
            "key": "true",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false",
            "sortable": "true"
        },
        {
            "name": "content",
            "type": "Edm.String",
            "sortable": "false",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "languageCode",
            "type": "Edm.String",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "keyPhrases",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "filterable": "false",
            "facetable": "false"
        },
        {
            "name": "organizations",
            "type": "Collection(Edm.String)",
            "searchable": "true",
            "sortable": "false",
            "filterable": "false",
            "facetable": "false"
        }
    ]
}

r = requests.put(endpoint + "/indexes/" + index_name,
                 data=json.dumps(index_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelemnek 201-as állapotkódot kell visszaadnia.

Az index definiálásával kapcsolatos további tudnivalókért tekintse meg az [index létrehozása (Azure Cognitive Search REST API)](https://docs.microsoft.com/rest/api/searchservice/create-index)című témakört.

### <a name="step-4-create-and-run-an-indexer"></a>4\. lépés: indexelő létrehozása és futtatása

Az [Indexelő](https://docs.microsoft.com/rest/api/searchservice/create-indexer) vezeti a folyamatot. Az eddig létrehozott három összetevő (az adatforrás, a készségkészlet, az index) egy indexelő bemenete. Az indexelő létrehozása az Azure Cognitive Searchon az az esemény, amely a teljes folyamatot mozgásba helyezi. 

Ha ezeket az objektumokat egy indexelő alkalmazásban szeretné összekapcsolni, meg kell adnia a mezők leképezéseit.

+ A fieldMappings a rendszer az adatforrásból a készségkészlet, a leképezési forrás mezőinek feldolgozásával dolgozza fel az indexben lévő mezőkbe. Ha a mezők nevei és típusai mindkét végén azonosak, nincs szükség leképezésre.

+ A outputFieldMappings a készségkészlet után dolgozzák fel, hivatkozva a sourceFieldNames, amelyek nem léteznek a dokumentum repedésének vagy dúsításának létrehozásakor. A targetFieldName egy index mezője.

A bemenetek kimenetekhez való csatlakoztatása mellett mező-hozzárendelések is használhatók az adatstruktúrák leállításához. További információ: [a dúsított mezők leképezése kereshető indexbe](cognitive-search-output-field-mapping.md).

Futtassa ezt a parancsfájlt egy `cogsrch-py-indexer`nevű indexelő létrehozásához.

```python
# Create an indexer
indexer_payload = {
    "name": indexer_name,
    "dataSourceName": datasource_name,
    "targetIndexName": index_name,
    "skillsetName": skillset_name,
    "fieldMappings": [
        {
            "sourceFieldName": "metadata_storage_path",
            "targetFieldName": "id",
            "mappingFunction":
            {"name": "base64Encode"}
        },
        {
            "sourceFieldName": "content",
            "targetFieldName": "content"
        }
    ],
    "outputFieldMappings":
    [
        {
            "sourceFieldName": "/document/organizations",
            "targetFieldName": "organizations"
        },
        {
            "sourceFieldName": "/document/pages/*/keyPhrases/*",
            "targetFieldName": "keyPhrases"
        },
        {
            "sourceFieldName": "/document/languageCode",
            "targetFieldName": "languageCode"
        }
    ],
    "parameters":
    {
        "maxFailedItems": -1,
        "maxFailedItemsPerBatch": -1,
        "configuration":
        {
            "dataToExtract": "contentAndMetadata",
            "imageAction": "generateNormalizedImages"
        }
    }
}

r = requests.put(endpoint + "/indexers/" + indexer_name,
                 data=json.dumps(indexer_payload), headers=headers, params=params)
print(r.status_code)
```

A kérelemnek a 201-as állapotkódot kell visszaadnia, azonban a feldolgozás több percet is igénybe vehet. Bár az adathalmaz kicsi, az analitikai képességek, például a képelemzés, a számítási igényű, és időt vesz igénybe.

Az indexelő [állapotának figyelésével](#check-indexer-status) megállapíthatja, hogy mikor fut vagy fejeződött be az indexelő.

> [!TIP]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma merül fel az adatok elérésekor, a bemenetek és kimenetek leképezése vagy a műveletek sorrendjével, akkor a jelen szakaszban fog megjelenni. Ha újra szeretné futtatni a folyamatot a kód vagy a parancsfájl módosításával, akkor először törölnie kell az objektumokat. További információk: [Alaphelyzetbe állítás és ismételt futtatás](#reset).

#### <a name="about-the-request-body"></a>A kérelem törzse

A szkript -1 értéket állít be a `"maxFailedItems"` paraméterhez, amely utasítja az indexelőmotort, hogy hagyja figyelmen kívül az adatimportálás közben felmerülő hibákat. Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Figyelje meg a `"dataToExtract":"contentAndMetadata"` utasítást a konfigurációs paraméterekben. Ez az utasítás azt jelzi, hogy az indexelő Kinyeri a tartalmat különböző fájlformátumokból és az egyes fájlokhoz kapcsolódó metaadatokat.

Tartalom kinyerésekor az `imageAction` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A `"imageAction":"generateNormalizedImages"` konfiguráció az OCR-képesség és a szöveg egyesítési képességeivel együtt azt jelzi, hogy az indexelő Kinyeri a szöveget a képekből (például a "Leállítás" szót egy forgalom leállításakor), és beágyazza azt a Content (tartalom) mező részeként. Ez a viselkedés a dokumentumokban beágyazott képekre (például egy PDF-fájlra) és az adatforrásban található rendszerképekre is vonatkozik (például egy JPG-fájlra).

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 – indexelés figyelése

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ha szeretné megtudni, hogy az indexelő feldolgozása befejeződött-e, futtassa a következő szkriptet.

```python
# Get indexer status
r = requests.get(endpoint + "/indexers/" + indexer_name +
                 "/status", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

A válaszban figyelje az "állapot" és a "Befejezés" érték "lastResult" értékét. Rendszeresen futtassa a parancsfájlt az állapot vizsgálatához. Az indexelő befejeződése után az állapot a "sikeres" értékre lesz állítva, a "Befejezés" értéket adja meg, és a válasz tartalmazni fog minden olyan hibát és figyelmeztetést, amely a dúsítás során történt.

![Az indexelő létrejött](./media/cognitive-search-tutorial-blob-python/py-indexer-is-created.png "Az indexelő létrejött")

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Számos figyelmeztetés jóindulatú. Ha például olyan JPEG-fájlt indexel, amely nem rendelkezik szöveggel, akkor a figyelmeztetés megjelenik a képernyőképen.

![Példa indexelő figyelmeztetésre](./media/cognitive-search-tutorial-blob-python/py-indexer-warning-example.png "Példa indexelő figyelmeztetésre")

## <a name="5---search"></a>5 – keresés

Az indexelés befejezését követően futtasson olyan lekérdezéseket, amelyek egyéni mezők tartalmát adják vissza. Alapértelmezés szerint az Azure Cognitive Search a legfontosabb 50 eredményeket adja vissza. A mintaadatok mérete kicsi, ezért az alapértelmezett beállítás megfelelő. Azonban nagyobb méretű adatkészletek esetén előfordulhat, hogy a lekérdezési sztringben meg kell adnia a megfelelő paramétereket, hogy a rendszer több eredményt adjon vissza. Útmutatásért lásd: [How to Page results in Azure Cognitive Search](search-pagination-page-layout.md).

Ellenőrzési lépésként szerezze be az index definícióját, amely az összes mezőt megjeleníti.

```python
# Query the service for the index definition
r = requests.get(endpoint + "/indexes/" + index_name,
                 headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Az eredményeknek az alábbi példához hasonlóan kell kinéznie. A képernyőkép csak a válasz egy részét jeleníti meg.

![Lekérdezési index az összes mezőhöz](./media/cognitive-search-tutorial-blob-python/py-query-index-for-fields.png "Az összes mező indexének lekérdezése")

A kimenet az egyes mezők nevét, típusát és attribútumait tartalmazó indexséma.

Indítson egy másodlagos lekérdezést a `"*"` kifejezésre egy mező, például az `organizations` összes tartalmának lekéréséhez.

```python
# Query the index to return the contents of organizations
r = requests.get(endpoint + "/indexes/" + index_name +
                 "/docs?&search=*&$select=organizations", headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Az eredményeknek az alábbi példához hasonlóan kell kinéznie. A képernyőkép csak a válasz egy részét jeleníti meg.

![A szervezetek tartalmának lekérdezési indexe](./media/cognitive-search-tutorial-blob-python/py-query-index-for-organizations.png "Az index lekérdezése a szervezetek tartalmának visszaküldéséhez")

Ismételje meg a műveletet további mezők esetén: tartalom, languageCode, kifejezés és szervezet ebben a gyakorlatban. Egyszerre több mezőt is lekérhet a vesszővel elválasztott listát használó `$select` megadásával.

A lekérdezési sztring összetettségétől és hosszától függően használhatja a GET vagy a POST metódust. További információkért lásd: [Lekérdezés a REST API-val](https://docs.microsoft.com/rest/api/searchservice/search-documents).

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai szakaszában érdemes törölni az objektumokat az Azure Cognitive Searchból, és lehetővé teheti a kód újraépítését. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

A dokumentumok újbóli indexelése az új definíciókkal:

1. Törölje az indexelő, az index és a készségkészlet.
2. Objektumok definícióinak módosítása
3. Hozzon létre újra objektumokat a szolgáltatásban. Az indexelő újbóli létrehozása futtatja a folyamatot. 

A portál használatával törölhet indexeket, indexelő és szakértelmével. Az indexelő törlésekor lehetősége van arra is, hogy az indexet, az készségkészlet és az adatforrást szelektíven törölje egyszerre.

![Keresési objektumok törlése](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Keresési objektumok törlése a portálon")

Parancsfájl használatával is törölheti őket. A következő szkript törli az általunk létrehozott készségkészlet. Az index, az indexelő és az adatforrás törlésére vonatkozó kérést egyszerűen módosíthatja.

```python
# delete the skillset
r = requests.delete(endpoint + "/skillsets/" + skillset_name,
                    headers=headers, params=params)
pprint(json.dumps(r.json(), indent=1))
```

Sikeres törlés esetén a rendszer a 204-es állapotkódot adja vissza.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag a bővített indexelőfolyamat a komponensek (adatforrás, képességcsoport, index és indexelő) létrehozásával való összeállításának alapvető lépéseit ismerteti.

A [beépített képességek](cognitive-search-predefined-skills.md) , valamint a készségkészlet-definíciók és a képességek összevonása a bemeneteken és kimeneteken keresztül történik. Azt is megtanulta, hogy az indexelő definíciójában szereplő `outputFieldMappings` szükséges a folyamatból származó, az Azure Cognitive Search szolgáltatásban kereshető indexként való útválasztáshoz.

Végezetül megtanulta, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszer további iterációkat. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. Ebben a kiadásban szerepel egy belső szerkezetek (a rendszer által létrehozott bővített dokumentumok) megjelenítésére szolgáló mechanizmus. Azt is megtanulta, hogyan ellenőrizhető az indexelő állapota, és milyen objektumokat kell törölni a folyamat újrafuttatása előtt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Az oktatóanyag elvégzésének leggyorsabb módja az Azure Cognitive Search Service és az Azure Blob servicet tartalmazó erőforráscsoport törlése. Feltételezve, hogy mindkét szolgáltatást ugyanabba a csoportba helyezi, törölje az erőforráscsoportot az összes benne lévő szolgáltatás végleges törléséhez, beleértve a szolgáltatásokat és az oktatóanyaghoz létrehozott tárolt tartalmakat is. A portálon az erőforráscsoport neve az egyes szolgáltatások Áttekintés lapján szerepel.

## <a name="next-steps"></a>Következő lépések

Egyéni képességekkel testre szabhatja vagy kibővítheti a folyamatot. Egyéni képességek létrehozása és egy képességcsoporthoz adása révén saját kezűleg írt szöveg- vagy képelemzést használhat.

> [!div class="nextstepaction"]
> [Példa: egyéni képesség létrehozása AI-bővítéshez](cognitive-search-create-custom-skill-example.md)
