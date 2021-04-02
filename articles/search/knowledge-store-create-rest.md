---
title: Knowledge Store létrehozása REST használatával
titleSuffix: Azure Cognitive Search
description: Az REST API és a Poster használatával hozzon létre egy Azure Cognitive Search Knowledge Store-t, amely egy mesterséges intelligencia-bővítési folyamatból származó dúsítást tart fenn.
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/18/2020
ms.openlocfilehash: 6af9b8f97d622ae10cfdbcaa8ca50abb42ec7332
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94889054"
---
# <a name="create-a-knowledge-store-using-rest-and-postman"></a>Knowledge Store létrehozása REST és Poster használatával

A Knowledge Store egy Azure Cognitive Search-bővítési folyamatból származó kimenetet tartalmaz későbbi elemzésekhez vagy más alsóbb rétegbeli feldolgozáshoz. A mesterséges intelligenciával rendelkező folyamatok képfájlokat vagy strukturálatlan szövegfájlokat fogadnak el, az Azure Cognitive Search használatával indexelik őket, és a mesterséges intelligenciát Cognitive Services (például a képelemzést és a természetes nyelvi feldolgozást) használják, majd az eredményeket az Azure Storage-ban tárolt tudásbázisba mentik. A Azure Portalban Power BI vagy Storage Explorer eszközökkel is elvégezheti a Tudásbázis megismerését.

Ebben a cikkben a REST API felület használatával végezheti el, indexelheti és alkalmazhatja a mesterséges intelligenciát a szállodai felülvizsgálatok készletén. A szállodai értékelések importálása az Azure Blob Storage-ba történik. Az eredményeket a rendszer az Azure Table Storage szolgáltatásban található Tudásbázisban menti.

Miután létrehozta a tudásbázist, megtudhatja, hogyan érheti el a [Storage Explorer](knowledge-store-view-storage-explorer.md) vagy [Power bi](knowledge-store-connect-power-bi.md)használatával a Knowledge Store-t.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

> [!TIP]
> Ehhez a cikkhez a [Poster Desktop alkalmazást](https://www.getpostman.com/) ajánljuk. A cikk [forráskódja](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/knowledge-store) tartalmaz egy Poster-gyűjteményt, amely tartalmazza az összes kérelmet. 

## <a name="create-services-and-load-data"></a>Szolgáltatások létrehozása és az adatterhelés

Ez a rövid útmutató az Azure Cognitive Search, az Azure Blob Storage és az [azure Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) használatát használja az AI-hoz. 

Mivel a számítási feladatok olyan kicsik, Cognitive Services a háttérben, hogy naponta legfeljebb 20 tranzakció szabad feldolgozását biztosítsa. Mivel az adatkészlet olyan kicsi, hogy kihagy egy Cognitive Services erőforrás létrehozását vagy csatolását.

1. [HotelReviews_Free.csvletöltése ](https://knowledgestoredemo.blob.core.windows.net/hotel-reviews/HotelReviews_Free.csv?sp=r&st=2019-11-04T01:23:53Z&se=2025-11-04T16:00:00Z&spr=https&sv=2019-02-02&sr=b&sig=siQgWOnI%2FDamhwOgxmj11qwBqqtKMaztQKFNqWx00AY%3D). Ezek az adatok egy CSV-fájlban (Kaggle.com-ből származó) tárolt adatok, amelyek egy adott szállodával kapcsolatban 19 darab ügyfél-visszajelzést tartalmaznak. 

1. [Hozzon létre egy Azure Storage-fiókot](../storage/common/storage-account-create.md?tabs=azure-portal) , vagy [keressen egy meglévő fiókot](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Storage%2storageAccounts/) a jelenlegi előfizetése alatt. Az Azure Storage-t fogja használni mind az importálandó nyers tartalomhoz, mind a Tudásbázis végeredményéhez.

   Válassza ki a **StorageV2 (általános célú v2)** fiók típusát.

1. Nyissa meg a blob Services lapjait, és hozzon létre egy " *Hotel-Reviews*" nevű tárolót.

1. Kattintson a **Feltöltés** gombra.

    ![Adatok feltöltése](media/knowledge-store-create-portal/upload-command-bar.png "A szállodai értékelések feltöltése")

1. Válassza ki az első lépésben letöltött **HotelReviews-Free.csv** fájlt.

    ![Az Azure Blob-tároló létrehozása](media/knowledge-store-create-portal/hotel-reviews-blob-container.png "Az Azure Blob-tároló létrehozása")

1. Ezzel az erőforrással majdnem elkészült, de mielőtt elhagyja ezeket a lapokat, a bal oldali navigációs ablaktáblán található hivatkozást használva nyissa meg a **hozzáférési kulcsok** lapot. A blob Storage-ból származó adatok lekérésére szolgáló kapcsolódási karakterlánc beolvasása. A kapcsolódási karakterlánc az alábbi példához hasonlóan néz ki: `DefaultEndpointsProtocol=https;AccountName=<YOUR-ACCOUNT-NAME>;AccountKey=<YOUR-ACCOUNT-KEY>;EndpointSuffix=core.windows.net`

1. Továbbra is a portálon váltson az Azure Cognitive Searchra. [Hozzon létre egy új szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices). Ehhez a gyakorlathoz használhatja az ingyenes szolgáltatást.

## <a name="configure-postman"></a>Postman konfigurálása

A Poster telepítése és beállítása.

### <a name="download-and-install-postman"></a>Poster letöltése és telepítése

1. Töltse le a [Poster-gyűjtemény forráskódját](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json).
1. A **fájl** importálása lehetőség kiválasztásával  >   importálhatja a forráskódot Poster-ba.
1. Válassza a **gyűjtemények** fület, majd kattintson a **...** (három pont) gombra.
1. Válassza a **Szerkesztés** elemet. 
   
   ![Navigációt bemutató Poster-alkalmazás](media/knowledge-store-create-rest/postman-edit-menu.png "Ugrás a Poster szerkesztési menüjére")
1. A **Szerkesztés** párbeszédpanelen válassza a **változók** lapot. 

A **változók** lapon olyan értékeket adhat hozzá a Poster-feladatokhoz, amelyek minden alkalommal felvesznek egy adott változót a kettős zárójelek között. A Poster például a `{{admin-key}}` megadott aktuális értékkel helyettesíti a szimbólumot `admin-key` . A Poster lehetővé teszi a helyettesítést az URL-címek, a fejlécek, a kérés törzse és így tovább. 

Az érték beszerzéséhez `admin-key` nyissa meg az Azure Cognitive Search szolgáltatást, és válassza a **kulcsok** fület, és módosítsa a `search-service-name` `storage-account-name` [szolgáltatások létrehozása](#create-services-and-load-data)területen kiválasztott értékeket. Állítsa be a `storage-connection-string` Storage-fiók **hozzáférési kulcsainak** lapján található érték használatával. A többi értéknél meghagyhatja az alapértelmezett értékeket.

![Poster-alkalmazás változók lapja](media/knowledge-store-create-rest/postman-variables-window.png "A Poster változói ablaka")


| Változó    | Honnan szerezhető be? |
|-------------|-----------------|
| `admin-key` | Az Azure Cognitive Search szolgáltatás **Keys (kulcsok** ) lapján.  |
| `api-version` | **2020-06-30**-ig marad. |
| `datasource-name` | Távozás a **Hotel-Reviews-DS**. | 
| `indexer-name` | Távozás a **Hotel-Reviews-IXR**. | 
| `index-name` | Hagyja a **Hotel-Reviews-IX**. | 
| `search-service-name` | Az Azure Cognitive Search szolgáltatás neve. Az URL-cím: `https://{{search-service-name}}.search.windows.net` . | 
| `skillset-name` | Távozás a **Hotel-Reviews-SS**. | 
| `storage-account-name` | Az Azure Storage-fiók neve. | 
| `storage-connection-string` | A Storage-fiók **hozzáférési kulcsok** lapján válassza a **key1**  >  **kapcsolati karakterlánc** lehetőséget. | 
| `storage-container-name` | Távozás a **Hotel-Reviews-** ben. | 

### <a name="review-the-request-collection-in-postman"></a>Tekintse át a Poster kérelem-gyűjteményét

A Tudásbázis létrehozásakor négy HTTP-kérelmet kell kiadnia: 

- **Az index létrehozásának kérése**: ez az index az Azure Cognitive Search által használt és visszaadott adatok tárolására szolgál.
- **Az adatforrás létrehozásához szükséges post kérelem**: ez az adatforrás összekapcsolja az Azure Cognitive Search viselkedését az adat-és Tudásbázis Storage-fiókjával. 
- **Kérelem létrehozása a készségkészlet létrehozásához**: a készségkészlet meghatározza az adataira alkalmazott dúsításokat és a Tudásbázis struktúráját.
- **Put-kérelem az indexelő létrehozásához**: az indexelő beolvassa az adatokat, alkalmazza a készségkészlet, és az eredményeket tárolja. Ezt a kérést utoljára kell futtatnia.

A [forráskód](https://github.com/Azure-Samples/azure-search-postman-samples/blob/master/knowledge-store/KnowledgeStore.postman_collection.json) tartalmaz egy Poster-gyűjteményt, amelyben a négy kérelem szerepel. A kérések kiadásához a Poster lapon válassza ki a kérelemhez tartozó fület. Ezután adjon hozzá `api-key` és `Content-Type` kérjen fejléceket. Állítsa be a értékét a következőre: `api-key` `{{admin-key}}` . Állítsa be az értéket a következőre: `Content-type` `application/json` . 

![A Poster felületének fejléceit bemutató képernyőfelvétel](media/knowledge-store-create-rest/postman-headers-ui.png)

> [!Note]
> `api-key`Minden kérelemben be kell állítania és fejléceket kell beállítania `Content-type` . Ha a Poster egy változót is felismer, a változó narancssárga szövegben jelenik meg, az `{{admin-key}}` előző képernyőképen látható módon. Ha a változó hibásan van írva, vörös szövegben jelenik meg.
>

## <a name="create-an-azure-cognitive-search-index"></a>Egy Azure Cognitive Search-index létrehozása

Hozzon létre egy Azure Cognitive Search indexet, amely azokat az adattípusokat jelöli, amelyeknek érdeklik a keresés, a szűrés és a fejlesztések alkalmazása. Hozza létre az indexet egy PUT-kérelemnek a következőre való kiállításával `https://{{search-service-name}}.search.windows.net/indexes/{{index-name}}?api-version={{api-version}}` . A Poster lecseréli a kapcsos zárójelek közé foglalt szimbólumokat (például `{{search-service-name}}` , `{{index-name}}` , és `{{api-version}}` ). [](#configure-postman) Ha más eszközt használ a REST-parancsok kiadásához, ezeket a változókat saját kezűleg kell helyettesítenie.

Állítsa be az Azure Cognitive Search index struktúráját a kérelem törzsében. A Poster-ben a és a `api-key` `Content-type` fejlécek beállítása után lépjen a kérelem **törzs** ablaktáblájába. A következő JSON-t kell látnia. Ha nem, válassza a **nyers**  >  **JSON (alkalmazás/JSON)** lehetőséget, majd illessze be a következő kódot törzsként:

```JSON
{
    "name": "{{index-name}}",
    "fields": [
        { "name": "address", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "categories", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "city", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "country", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "latitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "longitude", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "name", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false },
        { "name": "postalCode", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "province", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_date", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_dateAdded", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_rating", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_text", "type": "Edm.String", "filterable": false,  "sortable": false, "facetable": false },
        { "name": "reviews_title", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "reviews_username", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "AzureSearch_DocumentKey", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false, "key": true },
        { "name": "metadata_storage_content_type", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_size", "type": "Edm.Int64", "searchable": false, "filterable": false, "sortable": false, "facetable": false},
        { "name": "metadata_storage_last_modified", "type": "Edm.DateTimeOffset", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_name", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "metadata_storage_path", "type": "Edm.String", "searchable": false, "filterable": false, "sortable": false, "facetable": false },
        { "name": "Sentiment", "type": "Collection(Edm.Double)", "searchable": false, "filterable": true, "retrievable": true, "sortable": false, "facetable": true },
        { "name": "Language", "type": "Edm.String", "filterable": true, "sortable": false, "facetable": true },
        { "name": "Keyphrases", "type": "Collection(Edm.String)", "filterable": true, "sortable": false, "facetable": true }
    ]
}

```

Az index definíciója olyan adatok kombinációja, amelyeket szeretne bemutatni a felhasználónak (a szálloda neve, a tartalom áttekintése, a dátum), a keresési metaadatok és az AI-javító adatok (hangulat, kifejezés és nyelv).

Válassza a **Küldés** lehetőséget a Put kérelem kiválasztásához. Ekkor meg kell jelennie az állapotnak `201 - Created` . Ha más állapotot lát, a **törzs** ablaktáblán keressen egy olyan JSON-választ, amely egy hibaüzenetet tartalmaz. 

## <a name="create-the-datasource"></a>Az adatforrás létrehozása

Következő lépésként kapcsolja össze az Azure Cognitive Search a blob Storage-ban tárolt a szállodai adatkapcsolattal. Az adatforrás létrehozásához küldjön egy POST-kérelmet a következőnek: `https://{{search-service-name}}.search.windows.net/datasources?api-version={{api-version}}` . A `api-key` és a `Content-Type` fejléceket a korábban tárgyalt módon kell beállítani. 

A Poster területen lépjen az **adatforrás létrehozása** kérelemre, majd a **törzs** ablaktáblára. Az alábbi kódot kell látnia:

```json
{
  "name" : "{{datasource-name}}",
  "description" : "Demo files to demonstrate knowledge store capabilities.",
  "type" : "azureblob",
  "credentials" : { "connectionString" : "{{storage-connection-string}}" },
  "container" : { "name" : "{{storage-container-name}}" }
}
```

Kattintson a **Küldés** gombra a post kérelem kiadásához. 

## <a name="create-the-skillset"></a>A készségkészlet létrehozása 

A következő lépés a készségkészlet megadása, amely meghatározza az alkalmazandó fejlesztéseket és az eredményeket tároló tudásbázist. A Poster területen válassza a **Létrehozás a készségkészlet** lapot. A kérelem elküld egy PUT-t `https://{{search-service-name}}.search.windows.net/skillsets/{{skillset-name}}?api-version={{api-version}}` . Állítsa be a `api-key` és a `Content-type` fejléceket a korábbi módon. 

Két nagyméretű legfelső szintű objektum létezik: `skills` és `knowledgeStore` . Az objektumon belüli összes objektum `skills` egy dúsítási szolgáltatás. Minden alkoholtartalom-növelési szolgáltatás `inputs` és `outputs` . A `LanguageDetectionSkill` kimenete: `targetName` `Language` . Ennek a csomópontnak az értékét a más képességek többsége használja bemenetként. A forrás: `document/Language` . Egy csomópont kimenetének egy másikhoz való bemenetének lehetősége még nyilvánvalóbb a-ben `ShaperSkill` , amely meghatározza, hogy az adatok hogyan áramlanak be a Knowledge Store tábláiba.

Az `knowledge_store` objektum a Poster változón keresztül kapcsolódik a Storage-fiókhoz `{{storage-connection-string}}` . `knowledge_store` a a tudásbázisban található bővített dokumentum és táblák és oszlopok közötti leképezések készletét tartalmazza. 

A készségkészlet létrehozásához kattintson a Poster **Send (Küldés** ) gombjára a kérelem elvégzéséhez:

```json
{
    "name": "{{skillset-name}}",
    "description": "Skillset to detect language, extract key phrases, and detect sentiment",
    "skills": [ 
        {
            "@odata.type": "#Microsoft.Skills.Text.SplitSkill", 
            "context": "/document/reviews_text", "textSplitMode": "pages", "maximumPageLength": 5000,
            "inputs": [ 
                { "name": "text", "source": "/document/reviews_text" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "textItems", "targetName": "pages" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.SentimentSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode", "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "score", "targetName": "Sentiment" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
            "context": "/document",
            "inputs": [
                { "name": "text", "source": "/document/reviews_text" }
            ],
            "outputs": [
                { "name": "languageCode", "targetName": "Language" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
            "context": "/document/reviews_text/pages/*",
            "inputs": [
                { "name": "text",  "source": "/document/reviews_text/pages/*" },
                { "name": "languageCode",  "source": "/document/Language" }
            ],
            "outputs": [
                { "name": "keyPhrases" , "targetName": "Keyphrases" }
            ]
        },
        {
            "@odata.type": "#Microsoft.Skills.Util.ShaperSkill",
            "context": "/document",
            "inputs": [
                { "name": "name",  "source": "/document/name" },
                { "name": "reviews_date",  "source": "/document/reviews_date" },
                { "name": "reviews_rating",  "source": "/document/reviews_rating" },
                { "name": "reviews_text",  "source": "/document/reviews_text" },
                { "name": "reviews_title",  "source": "/document/reviews_title" },
                { "name": "AzureSearch_DocumentKey",  "source": "/document/AzureSearch_DocumentKey" },
                { 
                    "name": "pages",
                    "sourceContext": "/document/reviews_text/pages/*",
                    "inputs": [
                        { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment" },
                        { "name": "LanguageCode", "source": "/document/Language" },
                        { "name": "Page", "source": "/document/reviews_text/pages/*" },
                        { 
                            "name": "keyphrase", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                            "inputs": [
                                { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                            ]
                        }
                    ]
                }
            ],
            "outputs": [
                { "name": "output" , "targetName": "tableprojection" }
            ]
        }
    ],
    "knowledgeStore": {
        "storageConnectionString": "{{storage-connection-string}}",
        "projections": [
            {
                "tables": [
                    { "tableName": "hotelReviewsDocument", "generatedKeyName": "Documentid", "source": "/document/tableprojection" },
                    { "tableName": "hotelReviewsPages", "generatedKeyName": "Pagesid", "source": "/document/tableprojection/pages/*" },
                    { "tableName": "hotelReviewsKeyPhrases", "generatedKeyName": "KeyPhrasesid", "source": "/document/tableprojection/pages/*/keyphrase/*" },
                    { "tableName": "hotelReviewsSentiment", "generatedKeyName": "Sentimentid", "source": "/document/tableprojection/pages/*/sentiment/*" }
                ],
                "objects": []
            },
            {
                "tables": [
                    { 
                        "tableName": "hotelReviewsInlineDocument", "generatedKeyName": "Documentid", "sourceContext": "/document",
                        "inputs": [
                            { "name": "name", "source": "/document/name"},
                            { "name": "reviews_date", "source": "/document/reviews_date"},
                            { "name": "reviews_rating", "source": "/document/reviews_rating"},
                            { "name": "reviews_text", "source": "/document/reviews_text"},
                            { "name": "reviews_title", "source": "/document/reviews_title"},
                            { "name": "AzureSearch_DocumentKey", "source": "/document/AzureSearch_DocumentKey" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlinePages", "generatedKeyName": "Pagesid", "sourceContext": "/document/reviews_text/pages/*",
                        "inputs": [
                            { "name": "SentimentScore", "source": "/document/reviews_text/pages/*/Sentiment"},
                            { "name": "LanguageCode", "source": "/document/Language"},
                            { "name": "Page", "source": "/document/reviews_text/pages/*" }
                        ]
                    },
                    { 
                        "tableName": "hotelReviewsInlineKeyPhrases", "generatedKeyName": "kpidv2", "sourceContext": "/document/reviews_text/pages/*/Keyphrases/*",
                        "inputs": [
                            { "name": "Keyphrases", "source": "/document/reviews_text/pages/*/Keyphrases/*" }
                        ]
                    }
                ],
                "objects": []
            }
        ]
    }
}
```

## <a name="create-the-indexer"></a>Az indexelő létrehozása

Az utolsó lépés az indexelő létrehozása. Az indexelő beolvassa az adatokat, és aktiválja a készségkészlet. A Poster területen válassza az **Indexelő kérés létrehozása** lehetőséget, majd tekintse át a törzset. Az indexelő definíciója több, már létrehozott erőforrásra hivatkozik: az adatforrás, az index és a készségkészlet. 

Az `parameters/configuration` objektum azt szabályozza, hogy az indexelő Hogyan nyelje le az adatot. Ebben az esetben a bemeneti adatok egy olyan dokumentumban vannak, amely tartalmaz egy fejlécet és egy vesszővel tagolt értéket. A dokumentum kulcsa a dokumentum egyedi azonosítója. A kódolás előtt a dokumentum kulcsa a forrásdokumentum URL-címe. Végül a készségkészlet kimeneti értékei, például a Nyelvkód, a hangulat és a legfontosabb kifejezések a dokumentumban lévő helyükre vannak leképezve. Bár egyetlen érték van a esetében `Language` , `Sentiment` a rendszer a tömb minden elemére alkalmazza `pages` . `Keyphrases` egy olyan tömb, amely a tömb egyes elemeire is vonatkozik `pages` .

Miután beállította a `api-key` és a `Content-type` fejléceket, és ellenőrizze, hogy a kérelem törzse hasonló-e a következő forráskódhoz, válassza a **Küldés** postán lehetőséget. A Poster egy PUT-kérelmet küld a következőnek: `https://{{search-service-name}}.search.windows.net/indexers/{{indexer-name}}?api-version={{api-version}}` . Az Azure Cognitive Search létrehozza és futtatja az indexelő. 

```json
{
    "name": "{{indexer-name}}",
    "dataSourceName": "{{datasource-name}}",
    "skillsetName": "{{skillset-name}}",
    "targetIndexName": "{{index-name}}",
    "parameters": {
        "configuration": {
            "dataToExtract": "contentAndMetadata",
            "parsingMode": "delimitedText",
            "firstLineContainsHeaders": true,
            "delimitedTextDelimiter": ","
        }
    },
    "fieldMappings": [
        {
            "sourceFieldName": "AzureSearch_DocumentKey",
            "targetFieldName": "AzureSearch_DocumentKey",
            "mappingFunction": { "name": "base64Encode" }
        }
    ],
    "outputFieldMappings": [
        { "sourceFieldName": "/document/reviews_text/pages/*/Keyphrases/*", "targetFieldName": "Keyphrases" },
        { "sourceFieldName": "/document/Language", "targetFieldName": "Language" },
        { "sourceFieldName": "/document/reviews_text/pages/*/Sentiment", "targetFieldName": "Sentiment" }
    ]
}
```

## <a name="run-the-indexer"></a>Az indexelő futtatása 

A Azure Portal nyissa meg az Azure Cognitive Search szolgáltatás **Áttekintés** lapját. Válassza ki az **Indexelő** fület, majd válassza a **Hotels-Reviews-IXR** lehetőséget. Ha az indexelő még nem fut, válassza a **Futtatás** lehetőséget. Előfordulhat, hogy az indexelési feladat bizonyos, a nyelvi felismeréssel kapcsolatos figyelmeztetéseket eredményezhet. Az adatelemzés tartalmaz néhány olyan nyelvet, amely a kognitív képességek által még nem támogatott nyelveken íródott. 

## <a name="next-steps"></a>Következő lépések

Most, hogy a Cognitive Services és az eredmények egy tudásbázisba való kivetítésével bővíti az adatait, a Storage Explorer vagy a Power BI használatával megismerheti a dúsított adatkészletet.

Ha szeretné megtudni, hogyan derítheti fel ezt a tudásbázist Storage Explorer használatával, tekintse meg ezt a bemutatót:

> [!div class="nextstepaction"]
> [Megtekintés a Storage Explorerrel](knowledge-store-view-storage-explorer.md)

A következő útmutatóból megtudhatja, hogyan csatlakoztatható a Power BI a Knowledge Store-hoz:

> [!div class="nextstepaction"]
> [Kapcsolódás Power BI-jal](knowledge-store-connect-power-bi.md)

Ha szeretné megismételni ezt a gyakorlatot, vagy próbáljon meg egy másik AI-bővítési bemutatót használni, törölje a **Hotel-Reviews-idxr** indexelő. Az indexelő törlése visszaállítja az ingyenes napi tranzakció számlálóját nullára.