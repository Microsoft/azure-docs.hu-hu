---
title: C#-oktatóanyag az AI használatával az Azure-blobokon
titleSuffix: Azure Cognitive Search
description: A C# és az Azure Cognitive Search .NET SDK használatával a blob Storage-ban található tartalommal kapcsolatos szöveg-kinyerési és természetes nyelvi feldolgozás példája.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 01/23/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6cbfa01788f1897264b4197798aa5879cf724db3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104770954"
---
# <a name="tutorial-use-net-and-ai-to-generate-searchable-content-from-azure-blobs"></a>Oktatóanyag: a .NET és a AI használata kereshető tartalmak létrehozásához az Azure-blobokból

Ha strukturálatlan szöveget vagy rendszerképeket használ az Azure Blob Storage-ban, egy [mesterséges intelligencia](cognitive-search-concept-intro.md) -bővítési folyamat kinyerheti az adatokat, és új tartalmakat hozhat létre a teljes szöveges kereséshez vagy az adatbányászati forgatókönyvekhez. 

Az oktatóanyag során a következőket fogja elsajátítani:

> [!div class="checklist"]
> * Fejlesztési környezet beállítása
> * Az OCR, a nyelvfelismerés, valamint az entitás és a Key kifejezés felismerését használó folyamat definiálása.
> * Hajtsa végre a folyamatot az átalakítások meghívásához, valamint egy keresési index létrehozásához és betöltéséhez.
> * A teljes szöveges kereséssel és a részletes lekérdezési szintaxissal megismerheti az eredményeket.

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt nyisson meg egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag a C# nyelvet és a [ **Azure.Search.Documents** -ügyféloldali függvénytárat](/dotnet/api/overview/azure/search.documents-readme) használja az adatforrás, index, indexelő és készségkészlet létrehozásához.

Az indexelő egy, az adatforrás-objektumban megadott blob-tárolóhoz csatlakozik, és az összes indexelt tartalmat egy meglévő keresési indexbe küldi.

A készségkészlet csatolva van az indexelő számára. A Microsoft beépített képességeit használja az információk megkereséséhez és kinyeréséhez. A folyamat lépései közé tartozik az optikai karakterfelismerés (OCR) a képeken, a nyelvfelismerés a szövegben, a fő kifejezés kibontása és az entitások felismerése (szervezetek). A folyamat által létrehozott új információk az index új mezőiben tárolódnak. Az index kitöltése után használhatja a lekérdezésekben, a dimenziókban és a szűrőben található mezőket.

## <a name="prerequisites"></a>Előfeltételek

* [Visual Studio](https://visualstudio.microsoft.com/downloads/)
* [Azure.Search.Documents 11. x NuGet csomag](https://www.nuget.org/packages/Azure.Search.Documents) 
* [Azure Storage](https://azure.microsoft.com/services/storage/)
* [Azure Cognitive Search](https://azure.microsoft.com/services/search/)

> [!Note]
> Ehhez az oktatóanyaghoz az ingyenes keresési szolgáltatást használhatja. Az ingyenes keresési szolgáltatás három indexre, három indexelő elemre és három adatforrásra korlátozza a szolgáltatást. Az oktatóanyagban mindegyikből egyet hozhat majd létre. Mielőtt elkezdené, győződjön meg arról, hogy rendelkezik a szolgáltatásban az új erőforrások elfogadására szolgáló helyiséggel.

## <a name="download-sample-data"></a>Töltse le a mintaadatokat

A mintaadatok a vegyes tartalomtípusú 14 fájlból állnak, amelyeket egy későbbi lépésben fel kell tölteni az Azure Blob Storage-ba.

1. Nyissa meg ezt a [OneDrive mappát](https://1drv.ms/f/s!As7Oy81M_gVPa-LCb5lC_3hbS-4) , és a bal felső sarokban kattintson a **Letöltés** elemre a fájlok számítógépre másolásához. 

1. Kattintson a jobb gombbal a zip-fájlra, és válassza az **összes kibontása** lehetőséget. A különböző típusok 14 fájlból állnak. Ehhez a gyakorlathoz a 7-et fogja használni.

Az oktatóanyag forráskódját le is töltheti. A forráskód az **oktatóanyag-AI-dúsítás/v11** mappában található az [Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) adattárban.

## <a name="1---create-services"></a>1 – szolgáltatások létrehozása

Ez az oktatóanyag az Azure Cognitive Searcht használja az indexeléshez és a lekérdezésekhez, Cognitive Services a mesterséges intelligencia-bővítéshez és az Azure Blob Storage-hoz, hogy megadja az adatforrásokat. Ez az oktatóanyag napi 20 tranzakció ingyenes kiosztása alatt marad Cognitive Serviceson, így az egyetlen szükséges szolgáltatás a keresés és a tárolás.

Ha lehetséges, hozzon létre mindkettőt ugyanabban a régióban és erőforráscsoporthoz a közelség és a kezelhetőség érdekében. A gyakorlatban az Azure Storage-fiók bármely régióban lehet.

### <a name="start-with-azure-storage"></a>Első lépések az Azure Storage-ban

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/) , és kattintson az **+ erőforrás létrehozása** elemre.

1. Keressen rá a *Storage-fiókra* , és válassza ki a Microsoft Storage-fiók ajánlatát.

   ![Storage-fiók létrehozása](media/cognitive-search-tutorial-blob/storage-account.png "Storage-fiók létrehozása")

1. Az alapok lapon a következő elemek szükségesek. Minden más esetében fogadja el az alapértelmezett értékeket.

   * **Erőforráscsoport**. Válasszon ki egy meglévőt, vagy hozzon létre egy újat, de ugyanazt a csoportot használja az összes szolgáltatáshoz, hogy együtt lehessen kezelni őket.

   * **Tárfiók neve**. Ha úgy gondolja, hogy több erőforrása is van ugyanazzal a típussal, használja a nevet típus és régió szerint egyértelműsítse, például *blobstoragewestus*. 

   * **Hely**. Ha lehetséges, válassza ki ugyanazt a helyet, amelyet az Azure Cognitive Search és Cognitive Services használ. Egyetlen hely érvényteleníti A sávszélességgel kapcsolatos díjakat.

   * **Fiók típusa**. Válassza ki az alapértelmezett *StorageV2 (általános célú v2)*.

1. A szolgáltatás létrehozásához kattintson a **felülvizsgálat + létrehozás** lehetőségre.

1. A létrehozás után kattintson **az erőforrás** megnyitása lehetőségre az Áttekintés lap megnyitásához.

1. Kattintson a **Blobok** szolgáltatás elemre.

1. Kattintson a **+ tároló** elemre egy tároló létrehozásához, és nevezze el a *fogaskerék-Search-demo kifejezést*.

1. Válassza a *fogaskerék-keresés-bemutató* lehetőséget, majd kattintson a **feltöltés** gombra, és nyissa meg azt a mappát, ahová a letöltött fájlokat mentette. Válassza az összes tizennégy fájl lehetőséget, majd kattintson **az OK** gombra a feltöltéshez.

   ![Minta fájlok feltöltése](media/cognitive-search-quickstart-blob/sample-data.png "Minta fájlok feltöltése")

1. Mielőtt elkezdené az Azure Storage-t, szerezzen be egy kapcsolatok karakterláncot, hogy az Azure Cognitive Searchban is létrehozhat egy kapcsolatokat. 

   1. Lépjen vissza a Storage-fiók áttekintés lapjára (az általunk használt *blobstoragewestus* példaként használták). 

   1. A bal oldali navigációs panelen válassza a **hozzáférési kulcsok** lehetőséget, és másolja a kapcsolati karakterláncok egyikét. 

   A kapcsolódási karakterlánc az alábbi példához hasonló URL-cím:

      ```http
      DefaultEndpointsProtocol=https;AccountName=blobstoragewestus;AccountKey=<your account key>;EndpointSuffix=core.windows.net
      ```

1. Mentse a kapcsolódási karakterláncot a Jegyzettömbbe. Később szüksége lesz rá az adatforrás-kapcsolatok beállításakor.

### <a name="cognitive-services"></a>Cognitive Services

A mesterséges intelligenciát Cognitive Services támogatja, beleértve a természetes nyelv és a képfeldolgozás Text Analytics és Computer Vision. Ha a cél egy tényleges prototípus vagy projekt teljesítése volt, akkor Cognitive Services (ugyanabban a régióban, mint az Azure Cognitive Search), hogy az indexelési műveletekhez csatolható legyen.

Ennél a gyakorlatnál azonban kihagyhatja az erőforrások kiosztását, mivel az Azure Cognitive Search képes csatlakozni a háttérben a Cognitive Serviceshoz, és az indexelő futtatásakor 20 ingyenes tranzakciót biztosít. Mivel ez az oktatóanyag 14 tranzakciót használ, az ingyenes kiosztás elegendő. Nagyobb projektek esetében tervezze meg Cognitive Services kiépítés az utólagos elszámolású S0 szinten. További információ: [Cognitive Services csatolása](cognitive-search-attach-cognitive-services.md).

### <a name="azure-cognitive-search"></a>Azure Cognitive Search

A harmadik összetevő az Azure Cognitive Search, amelyet létrehozhat a [portálon](search-create-service-portal.md) , vagy [kereshet egy meglévő keresési szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) az előfizetésében.

A bemutató elvégzéséhez használhatja az ingyenes szintet. 

### <a name="copy-an-admin-api-key-and-url-for-azure-cognitive-search"></a>A felügyeleti API-kulcs és az Azure-beli URL-cím másolása Cognitive Search

Az Azure Cognitive Search szolgáltatással való kommunikációhoz szüksége lesz a szolgáltatás URL-címére és egy hozzáférési kulcsra. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban** másolja a szolgáltatásra vonatkozó összes jogosultsághoz tartozó rendszergazdai kulcsot. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   Kérje le a lekérdezési kulcsot is. Ajánlott a lekérdezési kérelmeket csak olvasási hozzáféréssel kibocsátani.

   ![A szolgáltatás nevének és a rendszergazda és a lekérdezési kulcsok beszerzése](media/search-get-started-javascript/service-name-and-keys.png)

Érvényes kulcs birtokában kérelmenként létesíthető megbízhatósági kapcsolat a kérést küldő alkalmazás és az azt kezelő szolgáltatás között.

## <a name="2---set-up-your-environment"></a>2 – a környezet beállítása

Először nyissa meg a Visual studiót, és hozzon létre egy új, a .NET Core-on futtatható Console app-projektet.

### <a name="install-azuresearchdocuments"></a>Azure.Search.Documents telepítése

Az [Azure Cognitive Search .net SDK](/dotnet/api/overview/azure/search) egy ügyféloldali kódtárből áll, amely lehetővé teszi az indexek, az adatforrások, az indexelő és a szakértelmével kezelését, valamint a dokumentumok feltöltését és kezelését, valamint a lekérdezések végrehajtását anélkül, hogy a http-és JSON-adatokkal kellene foglalkoznia. Ez az ügyféloldali kódtár NuGet-csomagként van kiosztva.

Ebben a projektben telepítse a `Azure.Search.Documents` és a legújabb verziójának 11-es vagy újabb verzióját `Microsoft.Extensions.Configuration` .

1. A Visual Studióban válassza az **eszközök**  >  **NuGet csomagkezelő**  >  **NuGet-csomagok kezelése a megoldáshoz lehetőséget...**

1. Tallózással keresse meg [Azure.Search.Document](https://www.nuget.org/packages/Azure.Search.Documents).

1. Válassza ki a legújabb verziót, majd kattintson a **telepítés** gombra.

1. Az előző lépések megismétlésével telepítse [Microsoft.Extensions.Configszülő](https://www.nuget.org/packages/Microsoft.Extensions.Configuration) , és [Microsoft.Extensions.Configuration.Jsbe a](https://www.nuget.org/packages/Microsoft.Extensions.Configuration.Json)következőre:.

### <a name="add-service-connection-information"></a>Szolgáltatás-összekapcsolási adatok hozzáadása

1. Kattintson a jobb gombbal a projektre a megoldáskezelő, majd válassza az  >  **új elem hozzáadása..** . lehetőséget. 

1. Nevezze el a fájlt `appsettings.json` , majd válassza a **Hozzáadás** lehetőséget. 

1. A fájl belefoglalása a kimeneti könyvtárba.
    1. Kattintson a jobb gombbal a elemre `appsettings.json` , és válassza a **Tulajdonságok** lehetőséget. 
    1. Módosítsa a másolás értékét a **kimeneti könyvtárba** a **másoláshoz, ha újabb**.

1. Másolja az alábbi JSON-t az új JSON-fájlba.

    ```json
    {
      "SearchServiceUri": "Put your search service URI here",
      "SearchServiceAdminApiKey": "Put your primary or secondary API key here",
      "SearchServiceQueryApiKey": "Put your query API key here",
      "AzureBlobConnectionString": "Put your Azure Blob connection string here",
    }
    ```

Adja hozzá a keresési szolgáltatás és a blob Storage-fiók adatait. Ne felejtse el, hogy ezt az információt az előző szakaszban jelzett szolgáltatás létesítési lépéseiből kérheti le.

A **SearchServiceUri** mezőben adja meg a teljes URL-címet.

### <a name="add-namespaces"></a>Névterek hozzáadása

A alkalmazásban `Program.cs` adja hozzá a következő névtereket.

```csharp
using Azure;
using Azure.Search.Documents.Indexes;
using Azure.Search.Documents.Indexes.Models;
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;
using System.Linq;

namespace EnrichwithAI
```

### <a name="create-a-client"></a>Ügyfél létrehozása

Hozzon létre egy példányt a `SearchIndexClient` és a `SearchIndexerClient` alatt `Main` .

```csharp
public static void Main(string[] args)
{
    // Create service client
    IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
    IConfigurationRoot configuration = builder.Build();

    string searchServiceUri = configuration["SearchServiceUri"];
    string adminApiKey = configuration["SearchServiceAdminApiKey"];
    string cognitiveServicesKey = configuration["CognitiveServicesKey"];

    SearchIndexClient indexClient = new SearchIndexClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
    SearchIndexerClient indexerClient = new SearchIndexerClient(new Uri(searchServiceUri), new AzureKeyCredential(adminApiKey));
}
```

> [!NOTE]
> Az ügyfelek csatlakoznak a keresési szolgáltatáshoz. A túl sok kapcsolat megnyitásának elkerüléséhez próbáljon meg egyetlen példányt megosztani az alkalmazásban, ha lehetséges. Az ilyen megosztás engedélyezéséhez a metódusok a szálon biztonságosak.
> 

### <a name="add-function-to-exit-the-program-during-failure"></a>Függvény hozzáadása a programból való kilépéshez a hiba során

Ez az oktatóanyag az indexelési folyamat egyes lépéseinek megismerését segíti. Ha van olyan kritikus probléma, amely megakadályozza, hogy a program létrehozza az adatforrást, a készségkészlet, az indexet vagy az indexelő, a program kiírja a hibaüzenetet, és kilép, így a probléma érthető és orvosolható lesz.

A hozzáadásával `ExitProgram` `Main` kezelheti azokat a forgatókönyveket, amelyekhez szükség van a program kilépésére.

```csharp
private static void ExitProgram(string message)
{
    Console.WriteLine("{0}", message);
    Console.WriteLine("Press any key to exit the program...");
    Console.ReadKey();
    Environment.Exit(0);
}
```

## <a name="3---create-the-pipeline"></a>3 – a folyamat létrehozása

Az Azure Cognitive Searchban az AI-feldolgozás az indexelés (vagy az adatfeldolgozás) során történik. Az útmutató ezen része négy objektumot hoz létre: adatforrás, index definíció, készségkészlet, indexelő. 

### <a name="step-1-create-a-data-source"></a>1. lépés: Adatforrás létrehozása

`SearchIndexerClient` egy [`DataSourceName`](/dotnet/api/azure.search.documents.indexes.models.searchindexer.datasourcename) objektumra beállítható tulajdonság `SearchIndexerDataSourceConnection` . Ez az objektum biztosítja az Azure Cognitive Search-adatforrások létrehozásához, listázásához, frissítéséhez vagy törléséhez szükséges összes módszert.

Hozzon létre egy új `SearchIndexerDataSourceConnection` példányt a hívásával `indexerClient.CreateOrUpdateDataSourceConnection(dataSource)` . A következő kód egy típusú adatforrást hoz létre `AzureBlob` .

```csharp
private static SearchIndexerDataSourceConnection CreateOrUpdateDataSource(SearchIndexerClient indexerClient, IConfigurationRoot configuration)
{
    SearchIndexerDataSourceConnection dataSource = new SearchIndexerDataSourceConnection(
        name: "demodata",
        type: SearchIndexerDataSourceType.AzureBlob,
        connectionString: configuration["AzureBlobConnectionString"],
        container: new SearchIndexerDataContainer("cog-search-demo"))
    {
        Description = "Demo files to demonstrate cognitive search capabilities."
    };

    // The data source does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateDataSourceConnection(dataSource);
    }
    catch (Exception ex)
    {
        Console.WriteLine("Failed to create or update the data source\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a data source");
    }

    return dataSource;
}
```

Sikeres kérelem esetén a metódus a létrehozott adatforrást fogja visszaadni. Ha probléma merül fel a kéréssel, például egy érvénytelen paraméterrel, akkor a metódus kivételt jelez.

Most adjon hozzá egy sort a-ben `Main` az `CreateOrUpdateDataSource` imént hozzáadott függvény meghívásához.

```csharp
// Create or Update the data source
Console.WriteLine("Creating or updating the data source...");
SearchIndexerDataSourceConnection dataSource = CreateOrUpdateDataSource(indexerClient, configuration);
```

Fordítsa le és futtassa a megoldást. Mivel ez az első kérés, a Azure Portal ellenőrizheti, hogy az adatforrás létrejött-e az Azure Cognitive Searchban. A keresési szolgáltatás áttekintése lapon ellenőrizze, hogy az adatforrások lista tartalmaz-e új elemeket. Lehet, hogy várnia kell néhány percet, amíg a portáloldal frissül.

  ![Adatforrások csempéje a portálon](./media/cognitive-search-tutorial-blob/data-source-tile.png "Adatforrások csempéje a portálon")

### <a name="step-2-create-a-skillset"></a>2. lépés: készségkészlet létrehozása

Ebben a szakaszban megadhatja az adataira alkalmazni kívánt gazdagító lépések készletét. Az egyes alkoholtartalom-növelési lépések a *szaktudás* és a dúsítási lépések egy *készségkészlet*. Ez az oktatóanyag [beépített kognitív képességeket](cognitive-search-predefined-skills.md) használ a készségkészlet:

* [Optikai karakterfelismerés](cognitive-search-skill-ocr.md) a nyomtatott és a kézírásos szöveg felismeréséhez a képfájlokban.

* [Szöveges egyesítéssel](cognitive-search-skill-textmerger.md) egyesítheti a mezőket egy gyűjteményből egyetlen mezőbe.

* [Nyelvfelismeréssel](cognitive-search-skill-language-detection.md) azonosítja a tartalom nyelvét.

* A kinyerési képesség és az entitás-felismerési képesség meghívása előtt a [szöveg felosztása](cognitive-search-skill-textsplit.md) a nagyméretű tartalmak kisebb adattömbökbe való bontásához. A fő kifejezés kinyerése és az entitások felismerése 50 000 karakterből álló vagy annál kevesebb bemenetet fogad el. A mintafájlok közül néhányat fel kell osztani ahhoz, hogy beleférjen a korlátozásba.

* [Entitások felismerése](cognitive-search-skill-entity-recognition.md) a blob-tároló tartalmából származó szervezetek nevének kinyeréséhez.

* A [Kulcskifejezések kinyerése](cognitive-search-skill-keyphrases.md) lehívja a leggyakoribb kulcskifejezéseket.

A kezdeti feldolgozás során az Azure Cognitive Search kihasználja az egyes dokumentumokat, hogy a tartalmakat különböző fájlformátumokból kinyerje. A forrásfájl-ből származó szöveg egy generált `content` mezőbe kerül, egy pedig minden dokumentumhoz. Ennek megfelelően állítsa be a bemenetet úgy, `"/document/content"` hogy ezt a szöveget használja. A képtartalom egy generált mezőbe kerül `normalized_images` , amely a készségkészlet van megadva `/document/normalized_images/*` .

A kimenetek hozzárendelhetők egy indexhez, bemenetként használhatók egy alsóbb rétegbeli képességhez, vagy a fentiek mindegyike lehetséges, akárcsak a nyelvkód esetében. Az indexben a nyelvkód a szűréskor lehet hasznos. A nyelvkódot bemenetként a szövegelemzési képességek használják, a szótördeléssel kapcsolatos nyelvi szabályok megadásához.

A képességcsoportok alapvető tudnivalóval kapcsolatos bővebb információkért lásd: [Képességcsoport megadása](cognitive-search-defining-skillset.md).

### <a name="ocr-skill"></a>OCR-képesség

Az **OCR** -képesség Kinyeri a szöveget a képekből. Ez a szaktudás azt feltételezi, hogy egy normalized_images mező létezik. A mező létrehozásához az oktatóanyag későbbi részében a ```"imageAction"``` konfigurációt az indexelő definíciójában kell beállítani ```"generateNormalizedImages"``` .

```csharp
private static OcrSkill CreateOcrSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("image")
    {
        Source = "/document/normalized_images/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("text")
    {
        TargetName = "text"
    });

    OcrSkill ocrSkill = new OcrSkill(inputMappings, outputMappings)
    {
        Description = "Extract text (plain and structured) from image",
        Context = "/document/normalized_images/*",
        DefaultLanguageCode = OcrSkillLanguage.En,
        ShouldDetectOrientation = true
    };

    return ocrSkill;
}
```

### <a name="merge-skill"></a>Szaktudás egyesítése

Ebben a szakaszban egy **egyesítési** képességet hozunk létre, amely egyesíti a dokumentum tartalma MEZŐT az OCR-képesség által létrehozott szöveggel.

```csharp
private static MergeSkill CreateMergeSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/content"
    });
    inputMappings.Add(new InputFieldMappingEntry("itemsToInsert")
    {
        Source = "/document/normalized_images/*/text"
    });
    inputMappings.Add(new InputFieldMappingEntry("offsets")
    {
        Source = "/document/normalized_images/*/contentOffset"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("mergedText")
    {
        TargetName = "merged_text"
    });

    MergeSkill mergeSkill = new MergeSkill(inputMappings, outputMappings)
    {
        Description = "Create merged_text which includes all the textual representation of each image inserted at the right location in the content field.",
        Context = "/document",
        InsertPreTag = " ",
        InsertPostTag = " "
    };

    return mergeSkill;
}
```

### <a name="language-detection-skill"></a>Nyelvi észlelési képesség

A **nyelvfelismerési** képesség észleli a bemeneti szöveg nyelvét, és egyetlen nyelvi kódot jelent a kérelemben elküldött összes dokumentumhoz. Az **nyelvfelismerés** skill kimenetét fogjuk használni a **szöveg felosztása** képességbe való bevitel részeként.

```csharp
private static LanguageDetectionSkill CreateLanguageDetectionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("languageCode")
    {
        TargetName = "languageCode"
    });

    LanguageDetectionSkill languageDetectionSkill = new LanguageDetectionSkill(inputMappings, outputMappings)
    {
        Description = "Detect the language used in the document",
        Context = "/document"
    };

    return languageDetectionSkill;
}
```

### <a name="text-split-skill"></a>Szöveg felosztása készség

Az alábbi **felosztott** képesség a szövegeket lapok alapján osztja szét, és az oldal hosszát 4 000 karakterre korlátozza `String.Length` . Az algoritmus megpróbálja felosztani a szöveget a nagy méretű adattömbökbe `maximumPageLength` . Ebben az esetben az algoritmus elvégzi a legjobbat, hogy a mondatot egy mondat határán megtörje, így a tömb mérete valamivel kisebb lehet `maximumPageLength` .

```csharp
private static SplitSkill CreateSplitSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/merged_text"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("textItems")
    {
        TargetName = "pages",
    });

    SplitSkill splitSkill = new SplitSkill(inputMappings, outputMappings)
    {
        Description = "Split content into pages",
        Context = "/document",
        TextSplitMode = TextSplitMode.Pages,
        MaximumPageLength = 4000,
        DefaultLanguageCode = SplitSkillLanguage.En
    };

    return splitSkill;
}
```

### <a name="entity-recognition-skill"></a>Entitás-felismerési szakértelem

Ez a `EntityRecognitionSkill` példány a kategória típusának felismerésére van beállítva `organization` . Az **entitás-felismerési** képesség is felismerheti a kategóriák típusát `person` és a `location` .

Figyelje meg, hogy a "Context" mező ```"/document/pages/*"``` csillaggal van beállítva, ami azt jelenti, hogy a dúsítási lépést minden egyes lapon meg kell hívni ```"/document/pages"``` .

```csharp
private static EntityRecognitionSkill CreateEntityRecognitionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("organizations")
    {
        TargetName = "organizations"
    });

    EntityRecognitionSkill entityRecognitionSkill = new EntityRecognitionSkill(inputMappings, outputMappings)
    {
        Description = "Recognize organizations",
        Context = "/document/pages/*",
        DefaultLanguageCode = EntityRecognitionSkillLanguage.En
    };
    entityRecognitionSkill.Categories.Add(EntityCategory.Organization);

    return entityRecognitionSkill;
}
```

### <a name="key-phrase-extraction-skill"></a>Kulcs kifejezés kinyerési készség

Az `EntityRecognitionSkill` imént létrehozott példányhoz hasonlóan a **kulcsszókeresés** skill is meghívva lesz a dokumentum minden oldalára.

```csharp
private static KeyPhraseExtractionSkill CreateKeyPhraseExtractionSkill()
{
    List<InputFieldMappingEntry> inputMappings = new List<InputFieldMappingEntry>();
    inputMappings.Add(new InputFieldMappingEntry("text")
    {
        Source = "/document/pages/*"
    });
    inputMappings.Add(new InputFieldMappingEntry("languageCode")
    {
        Source = "/document/languageCode"
    });

    List<OutputFieldMappingEntry> outputMappings = new List<OutputFieldMappingEntry>();
    outputMappings.Add(new OutputFieldMappingEntry("keyPhrases")
    {
        TargetName = "keyPhrases"
    });

    KeyPhraseExtractionSkill keyPhraseExtractionSkill = new KeyPhraseExtractionSkill(inputMappings, outputMappings)
    {
        Description = "Extract the key phrases",
        Context = "/document/pages/*",
        DefaultLanguageCode = KeyPhraseExtractionSkillLanguage.En
    };

    return keyPhraseExtractionSkill;
}
```

### <a name="build-and-create-the-skillset"></a>A készségkészlet készítése és létrehozása

`Skillset`Hozza létre az Ön által létrehozott ismeretek használatával.

```csharp
private static SearchIndexerSkillset CreateOrUpdateDemoSkillSet(SearchIndexerClient indexerClient, IList<SearchIndexerSkill> skills,string cognitiveServicesKey)
{
    SearchIndexerSkillset skillset = new SearchIndexerSkillset("demoskillset", skills)
    {
        Description = "Demo skillset",
        CognitiveServicesAccount = new CognitiveServicesAccountKey(cognitiveServicesKey)
    };

    // Create the skillset in your search service.
    // The skillset does not need to be deleted if it was already created
    // since we are using the CreateOrUpdate method
    try
    {
        indexerClient.CreateOrUpdateSkillset(skillset);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the skillset\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without a skillset");
    }

    return skillset;
}
```

Adja hozzá az alábbi sorokat a következőhöz: `Main` .

```csharp
// Create the skills
Console.WriteLine("Creating the skills...");
OcrSkill ocrSkill = CreateOcrSkill();
MergeSkill mergeSkill = CreateMergeSkill();
EntityRecognitionSkill entityRecognitionSkill = CreateEntityRecognitionSkill();
LanguageDetectionSkill languageDetectionSkill = CreateLanguageDetectionSkill();
SplitSkill splitSkill = CreateSplitSkill();
KeyPhraseExtractionSkill keyPhraseExtractionSkill = CreateKeyPhraseExtractionSkill();

// Create the skillset
Console.WriteLine("Creating or updating the skillset...");
List<SearchIndexerSkill> skills = new List<SearchIndexerSkill>();
skills.Add(ocrSkill);
skills.Add(mergeSkill);
skills.Add(languageDetectionSkill);
skills.Add(splitSkill);
skills.Add(entityRecognitionSkill);
skills.Add(keyPhraseExtractionSkill);

SearchIndexerSkillset skillset = CreateOrUpdateDemoSkillSet(indexerClient, skills, cognitiveServicesKey);
```

### <a name="step-3-create-an-index"></a>3. lépés: index létrehozása

Ebben a szakaszban egy indexsémát határoz meg a kereshető indexben szereplő mezők és az egyes mezők keresési attribútumainak megadásával. A mezők típussal is rendelkeznek, emellett olyan attribútumokat tartalmazhatnak, amelyek meghatározzák a mező használatának módját (kereshető, rendezhető stb.). Az indexben szereplő mezőneveknek nem kell pontosan megegyezniük a forrásban szereplő mezők nevével. Egy későbbi lépésben mezőleképezéseket fog hozzáadni egy indexelőhöz a forrás-cél mezőkhöz való csatlakozás céljából. Ebben a lépésben a keresőalkalmazásra vonatkozó mezőelnevezési konvenciók használatával határozza meg az indexet.

A gyakorlat során az alábbi mezőket és mezőtípusokat használjuk:

| Mezőnevek | Mezőtípusok |
| --- | --- |
| `id` | Edm.String |
| `content` | Edm.String |
| `languageCode` | Edm.String |
| `keyPhrases` | List<Edm.String> |
| `organizations` | List<Edm.String> |

#### <a name="create-demoindex-class"></a>DemoIndex osztály létrehozása

Az index mezői a Model osztály használatával vannak meghatározva. A modellosztály minden tulajdonsága olyan attribútumokkal rendelkezik, amelyek meghatározzák a vonatkozó indexmező kereséssel kapcsolatos viselkedéseit. 

Hozzáadjuk a modell osztályt egy új C#-fájlhoz. Kattintson a jobb gombbal a projektre , és válassza  >  az **új elem hozzáadása...** lehetőséget, válassza a "class" elemet, és nevezze el a fájlt `DemoIndex.cs` , majd válassza a **Hozzáadás** lehetőséget.

Ügyeljen arra, hogy a és a névterek típusait is használni kívánja `Azure.Search.Documents.Indexes` `System.Text.Json.Serialization` .

Adja hozzá az alábbi modell osztály definícióját, `DemoIndex.cs` és vegye fel azt ugyanabban a névtérbe, amelyben létre kívánja hozni az indexet.

```csharp
using Azure.Search.Documents.Indexes;
using System.Text.Json.Serialization;

namespace EnrichwithAI
{
    // The SerializePropertyNamesAsCamelCase is currently unsupported as of this writing. 
    // Replace it with JsonPropertyName
    public class DemoIndex
    {
        [SearchableField(IsSortable = true, IsKey = true)]
        [JsonPropertyName("id")]
        public string Id { get; set; }

        [SearchableField]
        [JsonPropertyName("content")]
        public string Content { get; set; }

        [SearchableField]
        [JsonPropertyName("languageCode")]
        public string LanguageCode { get; set; }

        [SearchableField]
        [JsonPropertyName("keyPhrases")]
        public string[] KeyPhrases { get; set; }

        [SearchableField]
        [JsonPropertyName("organizations")]
        public string[] Organizations { get; set; }
    }
}
```

Most, hogy meghatározta a modell osztályt, a vissza lehetőséggel `Program.cs` könnyedén létrehozhat egy index-definíciót. Az index neve a következő lesz: `demoindex` . Ha már létezik ilyen nevű index, a rendszer törli.

```csharp
private static SearchIndex CreateDemoIndex(SearchIndexClient indexClient)
{
    FieldBuilder builder = new FieldBuilder();
    var index = new SearchIndex("demoindex")
    {
        Fields = builder.Build(typeof(DemoIndex))
    };

    try
    {
        indexClient.GetIndex(index.Name);
        indexClient.DeleteIndex(index.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified index not exist, 404 will be thrown.
    }

    try
    {
        indexClient.CreateIndex(index);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the index\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without an index");
    }

    return index;
}
```

A tesztelés során előfordulhat, hogy egynél többször próbálkozik az index létrehozásával. Emiatt ellenőrizze, hogy a létrehozni kívánt index már létezik-e, mielőtt megpróbálja létrehozni.

Adja hozzá az alábbi sorokat a következőhöz: `Main` .

```csharp
// Create the index
Console.WriteLine("Creating the index...");
SearchIndex demoIndex = CreateDemoIndex(indexClient);
```

Adja hozzá a következő using utasítást a egyértelműsítse-hivatkozás feloldásához.

```csharp
using Index = Azure.Search.Documents.Indexes.Models;
```

Az indexekkel kapcsolatos fogalmakról további információt a [create index (REST API)](/rest/api/searchservice/create-index)című témakörben talál.

### <a name="step-4-create-and-run-an-indexer"></a>4. lépés: indexelő létrehozása és futtatása

Eddig létrehozott egy adatforrást, egy képességcsoportot és egy indexet. Ez a három összetevő egy olyan [indexelő](search-indexer-overview.md) része lesz, amely az egyes részeket egyetlen többszakaszos műveletben egyesíti. A három rész egy indexelőben való egyesítéséhez mezőleképezéseket kell meghatároznia.

* A fieldMappings a rendszer az adatforrásból a készségkészlet, a leképezési forrás mezőinek feldolgozásával dolgozza fel az indexben lévő mezőkbe. Ha a mezők nevei és típusai mindkét végén azonosak, nincs szükség leképezésre.

* A outputFieldMappings a készségkészlet után dolgozzák fel, hivatkozva a sourceFieldNames, amelyek nem léteznek a dokumentum repedésének vagy dúsításának létrehozásakor. A targetFieldName egy index mezője.

A bemenetek kimenetekhez való csatlakoztatása mellett mező-hozzárendelések is használhatók az adatstruktúrák leállításához. További információ: [a dúsított mezők leképezése kereshető indexbe](cognitive-search-output-field-mapping.md).

```csharp
private static SearchIndexer CreateDemoIndexer(SearchIndexerClient indexerClient, SearchIndexerDataSourceConnection dataSource, SearchIndexerSkillset skillSet, SearchIndex index)
{
    IndexingParameters indexingParameters = new IndexingParameters()
    {
        MaxFailedItems = -1,
        MaxFailedItemsPerBatch = -1,
    };
    indexingParameters.Configuration.Add("dataToExtract", "contentAndMetadata");
    indexingParameters.Configuration.Add("imageAction", "generateNormalizedImages");

    SearchIndexer indexer = new SearchIndexer("demoindexer", dataSource.Name, index.Name)
    {
        Description = "Demo Indexer",
        SkillsetName = skillSet.Name,
        Parameters = indexingParameters
    };

    FieldMappingFunction mappingFunction = new FieldMappingFunction("base64Encode");
    mappingFunction.Parameters.Add("useHttpServerUtilityUrlTokenEncode", true);

    indexer.FieldMappings.Add(new FieldMapping("metadata_storage_path")
    {
        TargetFieldName = "id",
        MappingFunction = mappingFunction

    });
    indexer.FieldMappings.Add(new FieldMapping("content")
    {
        TargetFieldName = "content"
    });

    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/organizations/*")
    {
        TargetFieldName = "organizations"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/pages/*/keyPhrases/*")
    {
        TargetFieldName = "keyPhrases"
    });
    indexer.OutputFieldMappings.Add(new FieldMapping("/document/languageCode")
    {
        TargetFieldName = "languageCode"
    });

    try
    {
        indexerClient.GetIndexer(indexer.Name);
        indexerClient.DeleteIndexer(indexer.Name);
    }
    catch (RequestFailedException ex) when (ex.Status == 404)
    {
        //if the specified indexer not exist, 404 will be thrown.
    }

    try
    {
        indexerClient.CreateIndexer(indexer);
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to create the indexer\n Exception message: {0}\n", ex.Message);
        ExitProgram("Cannot continue without creating an indexer");
    }

    return indexer;
}
```

Adja hozzá az alábbi sorokat a következőhöz: `Main` .

```csharp
// Create the indexer, map fields, and execute transformations
Console.WriteLine("Creating the indexer and executing the pipeline...");
SearchIndexer demoIndexer = CreateDemoIndexer(indexerClient, dataSource, skillset, demoIndex);
```

Az indexelő feldolgozása várhatóan eltarthat egy ideig. Annak ellenére, hogy az adatkészlet kis méretű, az analitikai képességek számítási igénye nagy. Néhány képesség, például a képelemzés futásideje hosszú.

> [!TIP]
> Az indexelő létrehozása elindítja a folyamatot. Ha probléma lép fel az adatok elérésével, a bemenetek és kimenetek leképezésével vagy a műveletek sorrendjével kapcsolatban, az ebben a szakaszban jelenik meg.

### <a name="explore-creating-the-indexer"></a>Az indexelő létrehozásának megismerése

A kód a `"maxFailedItems"`  -1 értékre van kijelölve, amely arra utasítja az indexelési motort, hogy hagyja figyelmen kívül a hibákat az adatimportálás során Ez azért hasznos, mert az adatforrás kevés dokumentumot tartalmaz. Nagyobb méretű adatforrás esetén 0-nál nagyobb értéket kell megadnia.

Azt is figyelje `"dataToExtract"` meg, hogy a be van állítva `"contentAndMetadata"` . Ez az utasítást meghatározza, hogy az indexelő automatikusan kinyerje a tartalmat a különböző fájlformátumokból, beleértve az egyes fájlokra vonatkozó metaadatokat is.

Tartalom kinyerésekor az `imageAction` beállításával kinyerheti a szöveget az adatforrásban talált képekből. A `"imageAction"` konfiguráció beállítása `"generateNormalizedImages"` , az OCR-képesség és a szöveges egyesítési képesség kombinálva azt jelzi, hogy az indexelő Kinyeri a szöveget a képekből (például a "Leállítás" szót egy forgalom leállításakor), és beágyazza a tartalom mezőbe. Ez a működés mind a dokumentumokban beágyazott képekre (például egy PDF-fájlban található képre), mind az adatforrásban talált képekre (például egy JPG-fájlra) vonatkozik.

<a name="check-indexer-status"></a>

## <a name="4---monitor-indexing"></a>4 – indexelés figyelése

Az indexelő meghatározását követően az indexelő a kérés elküldésekor automatikusan lefut. Az indexelés a vártnál tovább is eltarthat attól függően, hogy mely kognitív képességeket adta meg. Ha szeretné megtudni, hogy az indexelő továbbra is fut-e, használja a `GetStatus` metódust.

```csharp
private static void CheckIndexerOverallStatus(SearchIndexerClient indexerClient, SearchIndexer indexer)
{
    try
    {
        var demoIndexerExecutionInfo = indexerClient.GetIndexerStatus(indexer.Name);

        switch (demoIndexerExecutionInfo.Value.Status)
        {
            case IndexerStatus.Error:
                ExitProgram("Indexer has error status. Check the Azure Portal to further understand the error.");
                break;
            case IndexerStatus.Running:
                Console.WriteLine("Indexer is running");
                break;
            case IndexerStatus.Unknown:
                Console.WriteLine("Indexer status is unknown");
                break;
            default:
                Console.WriteLine("No indexer information");
                break;
        }
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("Failed to get indexer overall status\n Exception message: {0}\n", ex.Message);
    }
}
```

`demoIndexerExecutionInfo` az indexelő aktuális állapotát és végrehajtási előzményeit jelöli.

A figyelmeztetések bizonyos forrásfájl- és képességkombinációk esetében gyakoriak, és nem mindig utalnak hibára. Ebben az oktatóanyagban a figyelmeztetések jóindulatúak (például nincs szöveges bemenet a JPEG-fájlokból).

Adja hozzá az alábbi sorokat a következőhöz: `Main` .

```csharp
// Check indexer overall status
Console.WriteLine("Check the indexer overall status...");
CheckIndexerOverallStatus(indexerClient, demoIndexer);
```

## <a name="5---search"></a>5 – keresés

Az Azure Cognitive Search oktatóanyag-konzol alkalmazásaiban általában egy 2 másodperces késleltetést adunk az eredményeket visszaadó lekérdezések futtatása előtt, de mivel a dúsítás több percet is igénybe vehet, a konzol alkalmazást lezárjuk, és más megközelítést használunk.

A legegyszerűbb lehetőség a portálon található [keresési böngésző](search-explorer.md) . Először futtasson egy üres lekérdezést, amely visszaadja az összes dokumentumot, vagy egy célzottabb keresést, amely a folyamat által létrehozott új mező tartalmát adja vissza. 

1. Azure Portal a keresés áttekintése lapon válassza az **indexek** lehetőséget.

1. Keresés **`demoindex`** a listában. 14 dokumentummal kell rendelkeznie. Ha a dokumentumok száma nulla, az indexelő vagy még fut, vagy még nincs frissítve a lap. 

1. Válassza az **`demoindex`** lehetőséget. A Search Explorer az első lap.

1. A tartalom kereshető, amint az első dokumentum betöltődik. A tartalom meglétének ellenőrzéséhez a **Keresés** gombra kattintva futtasson egy meghatározatlan lekérdezést. Ez a lekérdezés az összes aktuálisan indexelt dokumentumot adja vissza, így egy ötlet, hogy mit tartalmaz az index.

1. Ezután illessze be a következő karakterláncot a felügyelhető eredményekhez: `search=*&$select=id, languageCode, organizations`

<a name="reset"></a>

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszaiban a tervezési iteráció legalkalmasabb megközelítése az objektumok törlése az Azure Cognitive Search és a kód újraépítésének engedélyezése. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Az oktatóanyaghoz tartozó mintakód ellenőrzi a meglévő objektumokat, és törli őket, hogy újra lehessen futtatni a kódot. A portál segítségével indexeket, indexelő fájlokat, adatforrásokat és szakértelmével is törölhet.

## <a name="takeaways"></a>Legfontosabb ismeretek

Ez az oktatóanyag azt mutatja be, hogyan hozhatók létre egy bővített indexelési folyamat az összetevők létrehozásakor: adatforrás, készségkészlet, index és indexelő.

A [beépített készségek](cognitive-search-predefined-skills.md) a készségkészlet-definícióval és a képességek láncolásával együtt a bemeneteken és kimeneteken keresztül is elérhetők. Azt is megtanulta, hogy `outputFieldMappings` az indexelő definíciója szerint a folyamatból származó, az Azure Cognitive Search szolgáltatásban kereshető indexre való átirányításhoz szükséges.

Végül megismerte, hogyan tesztelheti az eredményeket, és hogyan állíthatja alaphelyzetbe a rendszert a későbbi futtatásokhoz. Megtanulta, hogy ha lekérdezéseket futtat az indexen, az a bővített indexelési folyamat által létrehozott kimenetet adja vissza. Emellett azt is megtanulta, hogyan ellenőrizheti az indexelő állapotát, illetve hogy melyik objektumokat kell törölnie a folyamat újrafuttatása előtt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, a projekt végén érdemes lehet eltávolítani a már nem szükséges erőforrásokat. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a minden erőforrás vagy erőforráscsoport hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri a mesterséges intelligencia-bővítési folyamat összes objektumát, ismerkedjen meg közelebbről a készségkészlet-definíciókkal és az egyéni ismeretekkel.

> [!div class="nextstepaction"]
> [Készségkészlet létrehozása](cognitive-search-defining-skillset.md)