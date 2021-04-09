---
title: 'Oktatóanyag: REST és AI over Azure-Blobok'
titleSuffix: Azure Cognitive Search
description: A blob Storage-ban a Poster és az Azure Cognitive Search REST API-k használatával bemutatunk egy példát a szöveg kinyerésére és a természetes nyelvi feldolgozásra.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 11/17/2020
ms.openlocfilehash: 21f0d141567f17c470732088c6a93a2ae7ed3c67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94738050"
---
# <a name="tutorial-use-rest-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Oktatóanyag: az Azure-Blobok kereshető tartalmának létrehozásához használja a REST és a AI használatát

Ha strukturálatlan szöveget vagy rendszerképeket használ az Azure Blob Storage-ban, egy [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítési folyamat kinyeri az adatokat, és új tartalmat hoz létre olyan blobokból, amelyek teljes szöveges keresési vagy adatbányászati helyzetekben hasznosak. Bár a folyamatok feldolgozhatják a lemezképeket, ez a REST-oktatóanyag a szövegre, a nyelvfelismerés és a természetes nyelvi feldolgozás alkalmazására koncentrál, és új mezőket hoz létre, amelyeket használhat a lekérdezésekben, a dimenziókban és a szűrőkben.

Ez az oktatóanyag a Poster és a [Search REST API](/rest/api/searchservice/) -k használatával hajtja végre a következő feladatokat:

> [!div class="checklist"]
> * Szolgáltatások és Poster-gyűjtemények beállítása.
> * Hozzon létre egy olyan dúsítási folyamatot, amely szöveget, észleli a nyelvet, felismeri az entitásokat, és észleli a legfontosabb kifejezéseket.
> * Hozzon létre egy indexet a kimenet tárolásához (nyers tartalom, valamint folyamat által generált név-érték párok).
> * A folyamat végrehajtása átalakítások és elemzések elvégzéséhez, valamint az index betöltéséhez.
> * A teljes szöveges kereséssel és a részletes lekérdezési szintaxissal megismerheti az eredményeket.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt nyisson meg egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag a C# és az Azure Cognitive Search REST API-k használatával hozza létre az adatforrást, az indexet, az indexelő és a készségkészlet. Az Azure Blob Storage-ban a teljes dokumentumokkal (strukturálatlan szöveggel), például a PDF, a HTML, a DOCX és a PPTX használatával, majd egy készségkészlet keresztül futtathatja őket, hogy kinyerje az entitásokat, a kulcsfontosságú kifejezéseket és az egyéb szöveget a tartalmi fájlokban.

Ez a készségkészlet Cognitive Services API-k alapján beépített képességeket használ. A folyamat lépései közé tartozik a nyelvfelismerés szöveg, a fő kifejezés kibontása és az entitások felismerése (szervezetek). A rendszer új adatokat tárol a lekérdezésekben, aspektusokban és szűrőkben használható új mezőkben.

## <a name="prerequisites"></a>Előfeltételek

+ [Azure Storage](https://azure.microsoft.com/services/storage/)
+ [Postman asztali alkalmazás](https://www.getpostman.com/)
+ [Meglévő keresési szolgáltatás](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) [létrehozása](search-create-service-portal.md) vagy keresése 

> [!Note]
> Ehhez az oktatóanyaghoz használhatja az ingyenes szolgáltatást. Az ingyenes keresési szolgáltatás három indexre, három indexelő elemre és három adatforrásra korlátozza a szolgáltatást. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a szolgáltatásban az új erőforrások elfogadására szolgáló helyiséggel.

## <a name="download-files"></a>Fájlok letöltése

1. Nyissa meg ezt a [OneDrive mappát](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) , és a bal felső sarokban kattintson a **Letöltés** elemre a fájlok számítógépre másolásához. 

1. Kattintson a jobb gombbal a zip-fájlra, és válassza az **összes kibontása** lehetőséget. A különböző típusok 14 fájlból állnak. Ehhez a gyakorlathoz a 7-et fogja használni.

Igény szerint a forráskódot, a Poster-gyűjteményt is letöltheti az oktatóanyaghoz. A forráskód a következő címen érhető el: [https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial](https://github.com/Azure-Samples/azure-search-postman-samples/tree/master/Tutorial) .

## <a name="1---create-services"></a>1 – szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Searcht használja az indexeléshez és a lekérdezésekhez, Cognitive Services a mesterséges intelligencia-bővítéshez és az Azure Blob Storage-hoz, hogy megadja az adatforrásokat. Ez az oktatóanyag napi 20 tranzakció ingyenes kiosztása alatt marad Cognitive Serviceson, így az egyetlen szükséges szolgáltatás a keresés és a tárolás.

Ha lehetséges, hozzon létre mindkettőt ugyanabban a régióban és erőforráscsoporthoz a közelség és a kezelhetőség érdekében. A gyakorlatban az Azure Storage-fiók bármely régióban lehet.

### <a name="start-with-azure-storage"></a>Első lépések az Azure Storage-ban

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/) , és kattintson az **+ erőforrás létrehozása** elemre.

1. Keressen rá a *Storage-fiókra* , és válassza ki a Microsoft Storage-fiók ajánlatát.

   ![Storage-fiók létrehozása](media/cognitive-search-tutorial-blob/storage-account.png "Storage-fiók létrehozása")

1. Az alapok lapon a következő elemek szükségesek. Minden más esetében fogadja el az alapértelmezett értékeket.

   + **Erőforráscsoport**. Válasszon ki egy meglévőt, vagy hozzon létre egy újat, de ugyanazt a csoportot használja az összes szolgáltatáshoz, hogy együtt lehessen kezelni őket.

   + **Tárfiók neve**. Ha úgy gondolja, hogy több erőforrása is van ugyanazzal a típussal, használja a nevet típus és régió szerint egyértelműsítse, például *blobstoragewestus*. 

   + **Hely**. Ha lehetséges, válassza ki ugyanazt a helyet, amelyet az Azure Cognitive Search és Cognitive Services használ. Egyetlen hely érvényteleníti A sávszélességgel kapcsolatos díjakat.

   + **Fiók típusa**. Válassza ki az alapértelmezett *StorageV2 (általános célú v2)*.

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

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>A felügyeleti API-kulcs és az Azure-beli URL-cím másolása Cognitive Search

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **áttekintése** lapon szerezze be a keresési szolgáltatás nevét. A szolgáltatás nevét a végpont URL-címének áttekintésével ellenőrizheti. Ha a végpont URL-címe volt `https://mydemo.search.windows.net` , a szolgáltatás neve a következő lesz: `mydemo` .

2. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

   ![A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése](media/search-get-started-javascript/service-name-and-keys.png)

Minden kérelemhez API-kulcs szükséges a szolgáltatásnak küldött összes kérelem fejlécében. Egy érvényes kulcs a kérést küldő alkalmazás és az azt kezelő szolgáltatás közötti megbízhatósági kapcsolatot hoz létre a kérelmek alapján.

## <a name="2---set-up-postman"></a>2 – Poster beállítása

Indítsa el a Postmant, és hozzon létre egy HTTP-kérelmet. Ha nem ismeri ezt az eszközt, tekintse meg az [Azure Cognitive Search REST API-k megismerése](search-get-started-rest.md)című témakört.

Az oktatóanyagban használt metódusok a **post**, a **put** és a **Get**. A következő módszerekkel hozhat létre négy API-hívást a keresési szolgáltatáshoz: adatforrás, készségkészlet, index és indexelő létrehozása.

A fejlécekben a "Content-Type" értéket állítsa be, `application/json` és állítsa `api-key` az Azure Cognitive Search szolgáltatás felügyeleti API-kulcsára. Miután beállította a fejléceket, használhatja azokat minden kérelemhez ebben a gyakorlatban.

  ![Poster-kérelem URL-címe és fejléce](media/search-get-started-rest/postman-url.png "Poster-kérelem URL-címe és fejléce")

## <a name="3---create-the-pipeline"></a>3 – a folyamat létrehozása

Az Azure Cognitive Searchban a dúsítás az indexelés során (vagy az adatfeldolgozás során történik). Az útmutató ezen része négy objektumot hoz létre: adatforrás, index definíció, készségkészlet, indexelő. 

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

Az [adatforrás-objektumok](/rest/api/searchservice/create-data-source) biztosítják a kapcsolódási karakterláncot a fájlokat tartalmazó blob-tárolóhoz.

1. A **post** és a következő URL-cím helyett használja a-Service-Name kifejezést a szolgáltatás tényleges nevével.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/datasources?api-version=2020-06-30
   ```

1. A kérelem **törzsében** másolja a következő JSON-definíciót, `connectionString` és cserélje le a-t a Storage-fiókja tényleges kapcsolatára. 

   Ne felejtse el szerkeszteni a tároló nevét is. Egy korábbi lépésben a tároló neveként a "fogaskerék-keresés – bemutató" kifejezést javasoltuk.

    ```json
    {
      "name" : "cog-search-demo-ds",
      "description" : "Demo files to demonstrate cognitive search capabilities.",
      "type" : "azureblob",
      "credentials" :
      { "connectionString" :
        "DefaultEndpointsProtocol=https;AccountName=<YOUR-STORAGE-ACCOUNT>;AccountKey=<YOUR-ACCOUNT-KEY>;"
      },
      "container" : { "name" : "<YOUR-BLOB-CONTAINER-NAME>" }
    }
    ```
1. Küldje el a kérést. Meg kell jelennie a 201-es állapotkód megerősítő sikerességének. 

Ha a 403-as vagy 404-es hibát kapja, ellenőrizze a kérés szerkezetét: az `api-version=2020-06-30` legyen a végpont, az `api-key` szerepeljen a fejlécben a `Content-Type` kifejezés után, az értékének pedig érvényesnek kell lennie egy keresési szolgáltatáshoz. Előfordulhat, hogy a JSON-dokumentumot egy online JSON-érvényesítő használatával szeretné futtatni, hogy ellenőrizze a szintaxis helyességét. 

### <a name="step-2-create-a-skillset"></a>2. lépés: készségkészlet létrehozása

A [készségkészlet objektum](/rest/api/searchservice/create-skillset) a tartalomra alkalmazott dúsítási lépések halmaza. 

1. Használja a **put** és a következő URL-címet, és cserélje le a-Service-Name kifejezést a szolgáltatás tényleges nevére.

    ```http
    https://[YOUR-SERVICE-NAME].search.windows.net/skillsets/cog-search-demo-sd?api-version=2020-06-30
    ```

1. A kérelem **törzsében** másolja az alábbi JSON-definíciót. Ez a készségkészlet a következő beépített készségekből áll.

   | Ügyességi                 | Leírás    |
   |-----------------------|----------------|
   | [Entitások felismerése](cognitive-search-skill-entity-recognition.md) | Kibontja a személyek, szervezetek és helyszínek nevét a blob-tároló tartalmából. |
   | [Nyelvfelismerés](cognitive-search-skill-language-detection.md) | Észleli a tartalom nyelvét. |
   | [Szöveg felosztása](cognitive-search-skill-textsplit.md)  | Megszakítja a nagyméretű tartalmakat kisebb adattömbökbe, mielőtt meghívja a Key kifejezés kinyerési készségét. A kulcskifejezések kinyerése legfeljebb 50 000 karakter méterű bemeneteket fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba. |
   | [Kulcsszókeresés](cognitive-search-skill-keyphrases.md) | Lekéri a legfontosabb mondatokat. |

   Minden képesség a dokumentum tartalmán fut le. A feldolgozás során az Azure Cognitive Search kihasználja az egyes dokumentumokat a különböző fájlformátumokból származó tartalmak olvasásához. A forrásfájlban talált szöveg a létrehozott ```content``` mezőbe kerül, amelyből dokumentumonként egy jön létre. A bemenet így lesz ```"/document/content"``` .

   A legfontosabb mondatok kinyeréséhez, mivel a Text Splitter skill használatával nagyobb méretű fájlokat kell megtörni a lapokra, a kulcs kifejezés kinyerési képességének ```"document/pages/*"``` (a dokumentum minden oldalához) kontextusa a helyett ```"/document/content"``` .

    ```json
    {
      "description": "Extract entities, detect language and extract key-phrases",
      "skills":
      [
        {
          "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
          "categories": [ "Person", "Organization", "Location" ],
          "defaultLanguageCode": "en",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "persons", "targetName": "persons" },
            { "name": "organizations", "targetName": "organizations" },
            { "name": "locations", "targetName": "locations" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.LanguageDetectionSkill",
          "inputs": [
            { "name": "text", "source": "/document/content" }
          ],
          "outputs": [
            { "name": "languageCode", "targetName": "languageCode" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.SplitSkill",
          "textSplitMode" : "pages",
          "maximumPageLength": 4000,
          "inputs": [
            { "name": "text", "source": "/document/content" },
            { "name": "languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "textItems", "targetName": "pages" }
          ]
        },
        {
          "@odata.type": "#Microsoft.Skills.Text.KeyPhraseExtractionSkill",
          "context": "/document/pages/*",
          "inputs": [
            { "name": "text", "source": "/document/pages/*" },
            { "name":"languageCode", "source": "/document/languageCode" }
          ],
          "outputs": [
            { "name": "keyPhrases", "targetName": "keyPhrases" }
          ]
        }
      ]
    }
    ```
    Alább a képességcsoport grafikai ábrázolása látható. 

    ![Készségkészlet megismerése](media/cognitive-search-tutorial-blob/skillset.png "Készségkészlet megismerése")

1. Küldje el a kérést. A Poster-nek a 201-es állapotkódot kell visszaadnia. 

> [!NOTE]
> A kimenetek hozzárendelhetők egy indexhez, bemenetként használhatók egy alsóbb rétegbeli képességhez, vagy a fentiek mindegyike lehetséges, akárcsak a nyelvkód esetében. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához. A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

### <a name="step-3-create-an-index"></a>3. lépés: index létrehozása

Az [index](/rest/api/searchservice/create-index) biztosítja azt a sémát, amely a tartalom fizikai kifejezésének létrehozásához használható invertált indexekben és más szerkezetekben az Azure Cognitive Searchban. Az index legnagyobb összetevője a mezők gyűjteménye, ahol az adattípus és az attribútumok határozzák meg az Azure Cognitive Search tartalmát és viselkedését.

1. Használja az **put** és a következő URL-címet, és cserélje le a-Service-Name kifejezést a szolgáltatás tényleges nevére, és nevezze el az indexet.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx?api-version=2020-06-30
   ```

1. A kérelem **törzsében** másolja a következő JSON-definíciót. A `content` mező maga tárolja a dokumentumot. További mezők a `languageCode` , a `keyPhrases` és `organizations` a készségkészlet által létrehozott új információk (mezők és értékek) megjelenítéséhez.

    ```json
    {
      "fields": [
        {
          "name": "id",
          "type": "Edm.String",
          "key": true,
          "searchable": true,
          "filterable": false,
          "facetable": false,
          "sortable": true
        },
        {
          "name": "metadata_storage_name",
          "type": "Edm.String",
          "searchable": false,
          "filterable": false,
          "facetable": false,
          "sortable": false
        },
        {
          "name": "content",
          "type": "Edm.String",
          "sortable": false,
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "languageCode",
          "type": "Edm.String",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "keyPhrases",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "filterable": false,
          "facetable": false
        },
        {
          "name": "persons",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "organizations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        },
        {
          "name": "locations",
          "type": "Collection(Edm.String)",
          "searchable": true,
          "sortable": false,
          "filterable": true,
          "facetable": true
        }
      ]
    }
    ```

1. Küldje el a kérést. A Poster-nek a 201-es állapotkódot kell visszaadnia. 

### <a name="step-4-create-and-run-an-indexer"></a>4. lépés: indexelő létrehozása és futtatása

Az [Indexelő](/rest/api/searchservice/create-indexer) vezeti a folyamatot. Az eddig létrehozott három összetevő (az adatforrás, a készségkészlet, az index) egy indexelő bemenete. Az indexelő létrehozása az Azure Cognitive Searchon az az esemény, amely a teljes folyamatot mozgásba helyezi. 

1. Használja az **put** és a következő URL-címet, és cserélje le a-Service-Name kifejezést a szolgáltatás tényleges nevére, hogy az indexelő nevet adja.

   ```http
   https://[servicename].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
   ```

1. A kérelem **törzsében** másolja az alábbi JSON-definíciót. Figyelje meg a mező-hozzárendelési elemeket; Ezek a leképezések azért fontosak, mert definiálják az adatfolyamot. 

   A `fieldMappings` feldolgozása a készségkészlet előtt történik, és az adatforrásból az indexben lévő mezőkbe küld tartalmat. A mező-hozzárendelések használatával meglévő, nem módosított tartalmakat küldhet az indexbe. Ha a mezők nevei és típusai mindkét végén azonosak, nincs szükség leképezésre.

   A a `outputFieldMappings` szaktudás által létrehozott mezőkre vonatkoznak, amelyek feldolgozása a készségkészlet futtatása után történik. A `sourceFieldNames` alkalmazásban való hivatkozás `outputFieldMappings` nem létezik, amíg a dokumentum repedése vagy a dúsítása létre nem hozza őket. A az index `targetFieldName` sémában definiált index mezője.

    ```json
    {
      "name":"cog-search-demo-idxr",
      "dataSourceName" : "cog-search-demo-ds",
      "targetIndexName" : "cog-search-demo-idx",
      "skillsetName" : "cog-search-demo-ss",
      "fieldMappings" : [
        {
          "sourceFieldName" : "metadata_storage_path",
          "targetFieldName" : "id",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "metadata_storage_name",
          "targetFieldName" : "metadata_storage_name",
          "mappingFunction" :
            { "name" : "base64Encode" }
        },
        {
          "sourceFieldName" : "content",
          "targetFieldName" : "content"
        }
      ],
      "outputFieldMappings" :
      [
        {
          "sourceFieldName" : "/document/persons",
          "targetFieldName" : "persons"
        },
        {
          "sourceFieldName" : "/document/organizations",
          "targetFieldName" : "organizations"
        },
        {
          "sourceFieldName" : "/document/locations",
          "targetFieldName" : "locations"
        },
        {
          "sourceFieldName" : "/document/pages/*/keyPhrases/*",
          "targetFieldName" : "keyPhrases"
        },
        {
          "sourceFieldName": "/document/languageCode",
          "targetFieldName": "languageCode"
        }
      ],
      "parameters":
      {
        "maxFailedItems":-1,
        "maxFailedItemsPerBatch":-1,
        "configuration":
        {
          "dataToExtract": "contentAndMetadata",
          "parsingMode": "default",
          "firstLineContainsHeaders": false,
          "delimitedTextDelimiter": ","
        }
      }
    }
    ```

1. Küldje el a kérést. A Poster-nek a 201-as állapotkódot kell visszaadnia, amely megerősíti a sikeres feldolgozást. 

   A lépés utasításainak végrehajtása több percig is eltarthat. Annak ellenére, hogy az adatkészlet kis méretű, az analitikai képességek számítási igénye nagy. 

> [!NOTE]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma lép fel az adatok elérésével, a bemenetek és kimenetek leképezésével vagy a műveletek sorrendjével kapcsolatban, az ebben a szakaszban jelenik meg. Ha kód- vagy szkriptmódosításokkal szeretné újra futtatni a folyamatot, lehetséges, hogy először el kell távolítania az objektumokat. További információk: [Alaphelyzetbe állítás és ismételt futtatás](#reset).

#### <a name="about-indexer-parameters"></a>Az indexelő paramétereinek ismertetése

A szkript -1 értéket állít be a ```"maxFailedItems"``` paraméterhez, amely utasítja az indexelőmotort, hogy hagyja figyelmen kívül az adatimportálás közben felmerülő hibákat. Ez azért fogadható el, mert a bemutató adatforrásának van ilyen kevés dokumentuma. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Az ```"dataToExtract":"contentAndMetadata"``` utasítás azt jelzi, hogy az indexelő automatikusan Kinyeri a tartalmat a különböző fájlformátumokból, valamint az egyes fájlokhoz kapcsolódó metaadatokat. 

Tartalom kinyerésekor az ```imageAction``` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A ```"imageAction":"generateNormalizedImages"``` konfiguráció az OCR-képesség és a szöveg egyesítése képességgel együtt azt jelzi, hogy az indexelő Kinyeri a szöveget a képekből (például a "Leállítás" szót egy forgalom leállításakor), és beágyazza azt a tartalom mező részévé. Ez a működés mind a dokumentumokban beágyazott képekre (például egy PDF-fájlban található képre), mind az adatforrásban talált képekre (például egy JPG-fájlra) vonatkozik.

## <a name="4---monitor-indexing"></a>4 – indexelés figyelése

Az indexelés és a dúsítás a Create Indexer-kérelem elküldésekor azonnal megkezdődik. Az indexelés eltarthat egy ideig, attól függően, hogy milyen kognitív ismereteket határoz meg. Ha kíváncsi rá, hogy az indexelő fut-e még, küldje el a következő kérést az indexelő állapotának ellenőrzéséhez.

1. Használja a **Get** és a következő URL-címet, és cserélje le a-Service-Name kifejezést a szolgáltatás tényleges nevére, hogy az indexelő nevet adja.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr/status?api-version=2020-06-30
   ```

1. Tekintse át a választ, és Ismerje meg, hogy az indexelő fut-e, vagy hogy megtekintse a hiba-és figyelmeztetési információkat.  

Ha az ingyenes szintet használja, a következő üzenet várható: "" nem tudott kinyerni tartalmat vagy metaadatokat a dokumentumból. Csonkolt kinyert szöveg a következő karakterekre: "32768". Ez az üzenet akkor jelenik meg, ha az ingyenes szinten lévő blob-indexelés[32K korláttal](search-limits-quotas-capacity.md#indexer-limits)rendelkezik. Ez az üzenet nem jelenik meg ennél a magasabb szintű adatkészletnél. 

> [!NOTE]
> A figyelmeztetések gyakran előfordulnak bizonyos helyzetekben, és nem mindig jeleznek problémát. Ha például egy blob-tároló képfájlokat tartalmaz, és a folyamat nem kezeli a lemezképeket, a rendszer figyelmeztetést küld arról, hogy a képek feldolgozása nem történt meg.

## <a name="5---search"></a>5 – keresés

Most, hogy új mezőket és információkat hozott létre, futtasson néhány lekérdezést a kognitív keresés értékének megismeréséhez, mivel az egy tipikus keresési forgatókönyvhöz kapcsolódik.

Ne felejtse el, hogy a blob tartalmával kezdtük el, ahol a teljes dokumentum egyetlen mezőbe van csomagolva `content` . Kereshet ebben a mezőben, és megkeresheti az egyezéseket a lekérdezésekben.

1. Használja a **Get** és a következő URL-címet, cserélje le a-Service-Name kifejezést a szolgáltatás tényleges nevére, és keressen rá egy kifejezés vagy kifejezés példányaira, adja vissza a `content` mezőt és a megfelelő dokumentumok számát.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=content&api-version=2020-06-30
   ```
   
   A lekérdezés eredménye visszaküldi a dokumentum tartalmát, ami ugyanazt az eredményt kapja, ha a blob indexelő a kognitív keresési folyamat nélkül használta. Ez a mező kereshető, de nem használható, ha dimenziókat, szűrőket vagy automatikus kiegészítést szeretne használni.

   ![Tartalom mező kimenete](media/cognitive-search-tutorial-blob/content-output.png "Tartalom mező kimenete")
   
1. A második lekérdezés esetében a folyamat által létrehozott új mezőket (személyek, szervezetek, helyszínek, languageCode) kell visszaadnia. A rövidítések kimaradnak, de érdemes megadnia, ha meg szeretné jeleníteni ezeket az értékeket.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$count=true&$select=metadata_storage_name,persons,organizations,locations,languageCode&api-version=2020-06-30
   ```
   A $select utasítás mezői a Cognitive Services természetes nyelvi feldolgozási képességeiből létrehozott új információkat tartalmazzák. Ahogy várható, némi zaj van a dokumentumok eredményei és variációja között, de sok esetben az analitikai modellek pontos eredményeket hoznak létre.

   Az alábbi ábrán a a Microsoftnál a VEZÉRIGAZGATÓi szerepkört feltételezve, a következő képen láthatók a nagy-és nagybetűk.

   ![Folyamat kimenete](media/cognitive-search-tutorial-blob/pipeline-output.png "Folyamat kimenete")

1. Ha szeretné megtekinteni, hogyan használhatja ki ezeket a mezőket, adjon hozzá egy dimenziós paramétert a megfelelő dokumentumok hely szerinti összesítésének visszaadásához.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&facet=locations&api-version=2020-06-30
   ``` 

   Ebben a példában az egyes helyekhez 2 vagy 3 egyezés van.

   ![Dimenzió kimenete](media/cognitive-search-tutorial-blob/facet-output.png "Dimenzió kimenete")
   

1. Ebben a végső példában egy szűrőt alkalmaz a szervezetek gyűjteményére, és két egyezést ad vissza a NASDAQ alapján történő szűrési feltételekhez.

   ```http
   https://[YOUR-SERVICE-NAME].search.windows.net/indexes/cog-search-demo-idx/docs?search=*&$filter=organizations/any(organizations: organizations eq 'NASDAQ')&$select=metadata_storage_name,organizations&$count=true&api-version=2020-06-30
   ```

Ezek a lekérdezések néhány módszert mutatnak be, amelyekkel a lekérdezési szintaxissal és szűrőkkel dolgozhat a kognitív keresés által létrehozott új mezőkben. További példákat a példák [a keresési dokumentumok REST API](/rest/api/searchservice/search-documents#bkmk_examples), az [egyszerű szintaxisú lekérdezési példák](search-query-simple-examples.md)és a [teljes Lucene lekérdezési](search-query-lucene-examples.md)példák című részben talál.

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszaiban a tervezési iteráció legalkalmasabb megközelítése az objektumok törlése az Azure Cognitive Search és a kód újraépítésének engedélyezése. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

A portál használatával törölhet indexeket, indexelő fájlokat, adatforrásokat és szakértelmével. Az indexelő törlésekor lehetősége van arra is, hogy az indexet, az készségkészlet és az adatforrást szelektíven törölje egyszerre.

![Keresési objektumok törlése](./media/cognitive-search-tutorial-blob-python/py-delete-indexer-delete-all.png "Keresési objektumok törlése a portálon")

Vagy használja a **delete (Törlés** ) lehetőséget, és adja meg az egyes objektumok URL-címét A következő parancs törli az indexelő.

```http
DELETE https://[YOUR-SERVICE-NAME].search.windows.net/indexers/cog-search-demo-idxr?api-version=2020-06-30
```

Sikeres törlés esetén a rendszer a 204-es állapotkódot adja vissza.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag a bővített indexelőfolyamat a komponensek (adatforrás, képességcsoport, index és indexelő) létrehozásával való összeállításának alapvető lépéseit ismerteti.

A [beépített készségek](cognitive-search-predefined-skills.md) a készségkészlet-definícióval és a képességek láncolásával együtt a bemeneteken és kimeneteken keresztül is elérhetők. Azt is megtanulta, hogy `outputFieldMappings` az indexelő definíciója szerint a folyamatból származó, az Azure Cognitive Search szolgáltatásban kereshető indexre való átirányításhoz szükséges.

Végül megismerte, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszert a későbbi futtatásokhoz. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, a projekt végén érdemes lehet eltávolítani a már nem szükséges erőforrásokat. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a minden erőforrás vagy erőforráscsoport hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a mesterséges intelligencia-bővítési folyamat összes objektumát, ismerkedjen meg közelebbről a készségkészlet-definíciókkal és az egyéni ismeretekkel.

> [!div class="nextstepaction"]
> [Készségkészlet létrehozása](cognitive-search-defining-skillset.md)
