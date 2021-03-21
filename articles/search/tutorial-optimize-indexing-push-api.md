---
title: C# oktatóanyag az indexelés optimalizálása a leküldéses API-val
titleSuffix: Azure Cognitive Search
description: Ismerje meg, hogyan indexelheti hatékonyan az információkat az Azure Cognitive Search leküldéses API-jával. Ez az oktatóanyag és mintakód a C# nyelven van.
manager: luisca
author: dereklegenzoff
ms.author: delegenz
ms.service: cognitive-search
ms.topic: tutorial
ms.date: 1/29/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: f97a99bf2d055805ee665ab51aff8cff12dc5a69
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "99094192"
---
# <a name="tutorial-optimize-indexing-with-the-push-api"></a>Oktatóanyag: indexelés optimalizálása a leküldéses API-val

Az Azure Cognitive Search az adatok keresési indexbe történő importálásának [két alapvető megközelítését](search-what-is-data-import.md) *támogatja: az* adatok programozott módon történő továbbítása az indexbe, vagy az [Azure Cognitive Search indexelő](search-indexer-overview.md) használata egy támogatott adatforráson az adatok *lekéréséhez* .

Ez az oktatóanyag azt ismerteti, hogyan lehet hatékonyan indexelni az információkat a [leküldéses modellel](search-what-is-data-import.md#pushing-data-to-an-index) a kérelmek kötegelt feldolgozásával, valamint egy exponenciális leállítási újrapróbálkozási stratégiájának használatával. [A minta alkalmazást letöltheti és futtathatja](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing). Ez a cikk ismerteti az alkalmazás legfontosabb szempontjait és azokat a tényezőket, amelyeket figyelembe kell venni az adatok indexelése során.

Ez az oktatóanyag a C# és a [.net SDK](/dotnet/api/overview/azure/search) használatával hajtja végre a következő feladatokat:

> [!div class="checklist"]
> * Index létrehozása
> * A leghatékonyabb méret meghatározásához tesztelje a különböző batch-méreteket
> * Kötegek indexelése aszinkron módon
> * Több szál használata az indexelési sebesség növeléséhez
> * Az exponenciális leállítási újrapróbálkozási stratégiájának használata a sikertelen dokumentumok újrapróbálkozásához

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Ehhez az oktatóanyaghoz a következő szolgáltatások és eszközök szükségesek.

+ A [Visual Studio](https://visualstudio.microsoft.com/downloads/)bármely kiadása. A mintakód és az utasítások tesztelése az ingyenes közösségi kiadásban történt.

+ [Hozzon létre egy Azure Cognitive Search szolgáltatást](search-create-service-portal.md) , vagy [keressen egy meglévő szolgáltatást](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) a jelenlegi előfizetése alatt.

<a name="get-service-info"></a>

## <a name="download-files"></a>Fájlok letöltése

Az oktatóanyag forráskódja az [Azure-Samples/Azure-Search-DotNet-Samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) GitHub-adattárban található [optimzize-adatindexelési/v11](https://github.com/Azure-Samples/azure-search-dotnet-samples/tree/master/optimize-data-indexing/v11) mappában található.

## <a name="key-considerations"></a>Fő szempontok

Amikor egy indexbe küldi az adatküldést, számos fontos szempontot kell figyelembe vennie az indexelési sebesség hatására. Ezekről a tényezőkről az [index nagyméretű adatkészletek című cikkben](search-howto-large-index.md)olvashat bővebben.

A következő hat kulcsfontosságú tényezőt kell figyelembe venni:

+ A **szolgáltatási réteg és a partíciók/replikák száma** – a partíciók hozzáadása és a réteg növelése egyaránt növeli az indexelési sebességet.
+ **Séma indexelése** – mezők hozzáadásával és további tulajdonságok hozzáadásával a mezőkhöz (például *kereshető*, *sokrétű* vagy *szűrhető*) az indexelési sebesség csökkentése érdekében.
+ **Köteg mérete** – az optimális köteg mérete az index sémája és az adatkészlet alapján változik.
+ **Szálak/feldolgozók száma** – egyetlen szál nem használja ki teljes mértékben az indexelési sebességeket
+ **Újrapróbálkozási stratégia** – exponenciális leállítási újrapróbálkozási stratégiát kell használni az indexelés optimalizálásához.
+ **Hálózati adatátviteli sebesség** – az adatátviteli sebesség korlátozó tényező lehet. Adatok indexelése az Azure-környezetből az adatátviteli sebesség növeléséhez.


## <a name="1---create-azure-cognitive-search-service"></a>1 – Azure Cognitive Search szolgáltatás létrehozása

Az oktatóanyag elvégzéséhez szüksége lesz egy Azure Cognitive Search szolgáltatásra, amelyet [a portálon hozhat létre](search-create-service-portal.md). Azt javasoljuk, hogy ugyanazt a szintet használja, amelyet éles környezetben szeretne használni, így az indexelési sebességet pontosan tesztelheti és optimalizálhatja.

### <a name="get-an-admin-api-key-and-url-for-azure-cognitive-search"></a>Rendszergazdai API-kulcs és URL-cím beszerzése az Azure Cognitive Search

Az API-hívásokhoz a szolgáltatás URL-címe és egy hozzáférési kulcs szükséges. A Search szolgáltatás mindkettővel jön létre, így ha az előfizetéshez hozzáadta az Azure Cognitive Searcht, kövesse az alábbi lépéseket a szükséges információk beszerzéséhez:

1. [Jelentkezzen be a Azure Portalba](https://portal.azure.com/), és a keresési szolgáltatás **Áttekintés** lapján töltse le az URL-címet. A végpontok például a következőképpen nézhetnek ki: `https://mydemo.search.windows.net`.

1. A **Beállítások**  >  **kulcsaiban** kérjen meg egy rendszergazdai kulcsot a szolgáltatásra vonatkozó összes jogosultsághoz. Az üzletmenet folytonossága érdekében két, egymással megváltoztathatatlan rendszergazdai kulcs áll rendelkezésre. Az objektumok hozzáadására, módosítására és törlésére vonatkozó kérésekhez használhatja az elsődleges vagy a másodlagos kulcsot is.

   ![HTTP-végpont és elérési kulcs beszerzése](media/search-get-started-rest/get-url-key.png "HTTP-végpont és elérési kulcs beszerzése")

## <a name="2---set-up-your-environment"></a>2 – a környezet beállítása

1. Indítsa el a Visual studiót, és nyissa meg a **OptimizeDataIndexing. SLN**.
1. Megoldáskezelő a kapcsolódási adatok megadásához nyissa meg a **appsettings.js** .

```json
{
  "SearchServiceUri": "https://{service-name}.search.windows.net",
  "SearchServiceAdminApiKey": "",
  "SearchIndexName": "optimize-indexing"
}
```

## <a name="3---explore-the-code"></a>3 – a kód megismerése

A *appsettings.js* frissítését követően a **OptimizeDataIndexing. SLN** programban a minta programnak készen kell állnia a létrehozásra és a futtatásra.

Ez a kód a [C#](search-get-started-dotnet.md)rövid útmutatóból származik. A .NET SDK-val kapcsolatos részletes információk a cikkben találhatók.

Ez az egyszerű C# kódon-konzol alkalmazás a következő feladatokat hajtja végre:

+ Létrehoz egy új indexet a C# Hotel osztály adatstruktúrája alapján (amely a címe osztályra is hivatkozik).
+ A leghatékonyabb méret megállapítása a különböző batch-méretekkel
+ Aszinkron módon indexeli az adathalmazokat
    + Több szál használata az indexelési sebesség növeléséhez
    + Az exponenciális leállítási újrapróbálkozási stratégiájának használata a sikertelen elemek újrapróbálkozásához

 A program futtatása előtt szánjon egy percet a minta kódjának és index-definícióinak tanulmányozására. A megfelelő kód több fájlban található:

  + A **Hotel. cs** és a **. cs** tartalmazza az indexet meghatározó sémát.
  + A **DataGenerator. cs** egy egyszerű osztályt tartalmaz, amely megkönnyíti a nagy mennyiségű szállodai érték létrehozását
  + A **ExponentialBackoff. cs** kódot tartalmaz az indexelési folyamat optimalizálásához az alább leírtak szerint
  + A **program. cs** olyan függvényeket tartalmaz, amelyek létrehozzák és törölik az Azure Cognitive Search indexet, indexelik az adatok kötegeit, és a különböző batch-méreteket tesztelik.

### <a name="creating-the-index"></a>Az index létrehozása

Ez a mintakód a .NET SDK használatával határozza meg és hozza létre az Azure Cognitive Search indexét. Kihasználja az `FieldBuilder` osztályt egy C# adatmodell-osztályból származó index-struktúra létrehozásához.

Az adatmodellt a Hotel osztály határozza meg, amely a címe osztályra mutató hivatkozásokat is tartalmaz. A FieldBuilder részletesen részletezi az indexek összetett adatstruktúrájának létrehozásához. A metaadatok címkéi az egyes mezők attribútumainak meghatározására szolgálnak, például hogy kereshetők vagy rendezve legyenek.

A ( **z). cs** fájl következő kódrészletei azt mutatják be, hogy egy adott mező és egy másik adatmodell-osztályra mutató hivatkozás is megadható.

```csharp
. . .
[SearchableField(IsSortable = true)]
public string HotelName { get; set; }
. . .
public Address Address { get; set; }
. . .
```

A **program. cs** fájlban az index egy névvel és egy, a metódus által generált mező-gyűjteménysel van definiálva, `FieldBuilder.Build(typeof(Hotel))` majd a következőképpen jön létre:

```csharp
private static async Task CreateIndexAsync(string indexName, SearchIndexClient indexClient)
{
    // Create a new search index structure that matches the properties of the Hotel class.
    // The Address class is referenced from the Hotel class. The FieldBuilder
    // will enumerate these to create a complex data structure for the index.
    FieldBuilder builder = new FieldBuilder();
    var definition = new SearchIndex(indexName, builder.Build(typeof(Hotel)));

    await indexClient.CreateIndexAsync(definition);
}
```

### <a name="generating-data"></a>Adatgenerálás

A **DataGenerator. cs** fájl egy egyszerű osztályt hoz létre a teszteléshez szükséges adatlétrehozáshoz. Ennek az osztálynak egyetlen célja, hogy megkönnyítse a nagy számú, egyedi AZONOSÍTÓval rendelkező dokumentum létrehozását az indexeléshez.

Az egyedi azonosítókkal rendelkező 100 000-Szállodák listájának lekéréséhez futtassa a következő kódrészleteket:

```csharp
long numDocuments = 100000;
DataGenerator dg = new DataGenerator();
List<Hotel> hotels = dg.GetHotels(numDocuments, "large");
```

Ebben a példában két különböző méretű Hotel érhető el: **kicsi** és  **nagy**.

Az index sémája jelentős hatással lehet az indexelési sebességre. Ennek a hatása miatt érdemes átkonvertálni ezt az osztályt a kívánt index sémának megfelelő adatlétrehozáshoz az oktatóanyag futtatása után.

## <a name="4---test-batch-sizes"></a>4 – batch-méretek tesztelése

Az Azure Cognitive Search a következő API-kat támogatja egy vagy több dokumentum indexbe való betöltéséhez:

+ [Dokumentumok hozzáadása, frissítése vagy törlése (REST API)](/rest/api/searchservice/AddUpdate-or-Delete-Documents)
+ [IndexDocumentsAction osztály](/dotnet/api/azure.search.documents.models.indexdocumentsaction) vagy [IndexDocumentsBatch osztály](/dotnet/api/azure.search.documents.models.indexdocumentsbatch)

A dokumentumok a kötegekben való indexelése jelentősen javítja az indexelési teljesítményt. Ezek a kötegek akár 1000-dokumentumok, akár 16 MB-onként is lehetnek.

Az adathalmazok optimális méretének meghatározása az indexelési sebesség optimalizálásának egyik fő összetevője. Az optimális batch-méretet befolyásoló két elsődleges tényező a következők:

+ Az index sémája
+ Az adataik mérete

Mivel az optimális batch-méret függ az indextől és az adataitól, a legjobb módszer a különböző batch-méretek tesztelése annak meghatározására, hogy mi eredményezheti a forgatókönyv leggyorsabb indexelési sebességét.

A következő függvény egy egyszerű megközelítést mutat be a Batch-méretek teszteléséhez.

```csharp
public static async Task TestBatchSizesAsync(SearchClient searchClient, int min = 100, int max = 1000, int step = 100, int numTries = 3)
{
    DataGenerator dg = new DataGenerator();

    Console.WriteLine("Batch Size \t Size in MB \t MB / Doc \t Time (ms) \t MB / Second");
    for (int numDocs = min; numDocs <= max; numDocs += step)
    {
        List<TimeSpan> durations = new List<TimeSpan>();
        double sizeInMb = 0.0;
        for (int x = 0; x < numTries; x++)
        {
            List<Hotel> hotels = dg.GetHotels(numDocs, "large");

            DateTime startTime = DateTime.Now;
            await UploadDocumentsAsync(searchClient, hotels).ConfigureAwait(false);
            DateTime endTime = DateTime.Now;
            durations.Add(endTime - startTime);

            sizeInMb = EstimateObjectSize(hotels);
        }

        var avgDuration = durations.Average(timeSpan => timeSpan.TotalMilliseconds);
        var avgDurationInSeconds = avgDuration / 1000;
        var mbPerSecond = sizeInMb / avgDurationInSeconds;

        Console.WriteLine("{0} \t\t {1} \t\t {2} \t\t {3} \t {4}", numDocs, Math.Round(sizeInMb, 3), Math.Round(sizeInMb / numDocs, 3), Math.Round(avgDuration, 3), Math.Round(mbPerSecond, 3));

        // Pausing 2 seconds to let the search service catch its breath
        Thread.Sleep(2000);
    }

    Console.WriteLine();
}
```

Mivel nem minden dokumentum azonos méretű (bár ebben a mintában vannak), a keresési szolgáltatásnak küldött adatok méretét becsüljük. Ezt az alábbi függvény használatával hajtja végre, amely először átalakítja az objektumot a JSON-be, majd a mérete bájtban van meghatározva. Ezzel a technikával meghatározhatja, hogy melyik batch-méretek a leghatékonyabbak a MB/s indexelési sebesség szempontjából.

```csharp
// Returns size of object in MB
public static double EstimateObjectSize(object data)
{
    // converting object to byte[] to determine the size of the data
    BinaryFormatter bf = new BinaryFormatter();
    MemoryStream ms = new MemoryStream();
    byte[] Array;

    // converting data to json for more accurate sizing
    var json = JsonSerializer.Serialize(data);
    bf.Serialize(ms, json);
    Array = ms.ToArray();

    // converting from bytes to megabytes
    double sizeInMb = (double)Array.Length / 1000000;

    return sizeInMb;
}
```

A függvénynek szüksége van egy `SearchClient` , valamint az egyes kötegek méretének tesztelésére irányuló próbálkozások számára. Mivel előfordulhat, hogy az egyes kötegek indexelési ideje változó lehet, a rendszer alapértelmezés szerint háromszor megpróbál minden köteget kipróbálni, hogy az eredmények statisztikailag jelentősebbek legyenek.

```csharp
await TestBatchSizesAsync(searchClient, numTries: 3);
```

A függvény futtatásakor a konzolon az alábbihoz hasonló kimenetnek kell megjelennie:

   ![A teszt batch-méret függvény kimenete](media/tutorial-optimize-data-indexing/test-batch-sizes.png "A teszt batch-méret függvény kimenete")

Határozza meg, hogy melyik batch-méret a leghatékonyabb, majd az oktatóanyag következő lépésében használja az adott batch-méretet. A különböző batch-méreteknél több MB/s méretű fennsík jelenhet meg.

## <a name="5---index-data"></a>5 – indexelt adatértékek

Most, hogy azonosította a használni kívánt batch-méretet, a következő lépés az adatindex megkezdése. Az adatindexek hatékony indexeléséhez a következő példa:

+ Több szálat/feldolgozót használ.
+ Egy exponenciális leállítási újrapróbálkozási stratégiát valósít meg.

### <a name="use-multiple-threadsworkers"></a>Több szál/feldolgozó használata

Ahhoz, hogy teljes mértékben ki tudja használni az Azure Cognitive Search indexelési sebességét, valószínűleg több szálat kell használnia, hogy a kötegelt indexelési kérelmeket egyidejűleg küldje el a szolgáltatásnak.  

A fent említett legfontosabb szempontok közül több a szálak optimális számát befolyásolja. Módosíthatja ezt a mintát és tesztelheti a különböző szálak számát, hogy meghatározza a forgatókönyv optimális szálak számát. Ha azonban egyszerre több szál fut, akkor a legtöbb hatékonysági előnyt ki kell használnia.

A keresési szolgáltatást elérve a kérések felfutásakor előfordulhat, hogy a kérést nem teljesen sikerült [http-állapotkódot](/rest/api/searchservice/http-status-codes) észlelni. Az indexelés során két gyakori HTTP-állapotkód van:

+ **503 a szolgáltatás nem érhető el** – ez a hiba azt jelenti, hogy a rendszer nagy terhelés alatt áll, és a kérés jelenleg nem dolgozható fel.
+ **207 többszörös állapot** – ez a hiba azt jelenti, hogy egyes dokumentumok sikeresek, de legalább egy sikertelen volt.

### <a name="implement-an-exponential-backoff-retry-strategy"></a>Exponenciális leállítási újrapróbálkozási stratégia implementálása

Ha hiba történik, a kérelmeket az [exponenciális leállítási újrapróbálkozási stratégiájának használatával újra](/dotnet/architecture/microservices/implement-resilient-applications/implement-retries-exponential-backoff)kell próbálkozni.

Az Azure Cognitive Search .NET SDK automatikusan újrapróbálkozik a 503s és más sikertelen kérelmekkel, de a 207s újrapróbálkozásához saját logikát kell megvalósítani. A nyílt forráskódú eszközök, például a [Polly](https://github.com/App-vNext/Polly) is használható az újrapróbálkozási stratégia megvalósításához.

Ebben a példában a saját exponenciális leállítási újrapróbálkozási stratégiát Implementáljuk. Ennek a stratégiának a megvalósításához néhány változót is meg kell határozni, beleértve az `maxRetryAttempts` és a `delay` sikertelen kérések kezdeti értékét:

```csharp
// Create batch of documents for indexing
var batch = IndexDocumentsBatch.Upload(hotels);

// Create an object to hold the result
IndexDocumentsResult result = null;

// Define parameters for exponential backoff
int attempts = 0;
TimeSpan delay = delay = TimeSpan.FromSeconds(2);
int maxRetryAttempts = 5;
```

Az indexelési művelet eredményeit a változó tárolja `IndexDocumentResult result` . Ez a változó azért fontos, mert lehetővé teszi annak ellenőrzését, hogy a Batch bármelyik dokumentuma meghiúsult-e az alább látható módon. Ha részleges hiba történt, egy új köteg jön létre a sikertelen dokumentumok azonosítója alapján.

`RequestFailedException` a kivételeket akkor is el kell látni, ha a kérést nem sikerült teljesen végrehajtani, és azt is újra kell próbálkozni.

```csharp
// Implement exponential backoff
do
{
    try
    {
        attempts++;
        result = await searchClient.IndexDocumentsAsync(batch).ConfigureAwait(false);

        var failedDocuments = result.Results.Where(r => r.Succeeded != true).ToList();

        // handle partial failure
        if (failedDocuments.Count > 0)
        {
            if (attempts == maxRetryAttempts)
            {
                Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
                break;
            }
            else
            {
                Console.WriteLine("[Batch starting at doc {0} had partial failure]", id);
                Console.WriteLine("[Retrying {0} failed documents] \n", failedDocuments.Count);

                // creating a batch of failed documents to retry
                var failedDocumentKeys = failedDocuments.Select(doc => doc.Key).ToList();
                hotels = hotels.Where(h => failedDocumentKeys.Contains(h.HotelId)).ToList();
                batch = IndexDocumentsBatch.Upload(hotels);

                Task.Delay(delay).Wait();
                delay = delay * 2;
                continue;
            }
        }

        return result;
    }
    catch (RequestFailedException ex)
    {
        Console.WriteLine("[Batch starting at doc {0} failed]", id);
        Console.WriteLine("[Retrying entire batch] \n");

        if (attempts == maxRetryAttempts)
        {
            Console.WriteLine("[MAX RETRIES HIT] - Giving up on the batch starting at {0}", id);
            break;
        }

        Task.Delay(delay).Wait();
        delay = delay * 2;
    }
} while (true);
```

Innen Becsomagoljuk az exponenciális leállítási kódot egy függvénybe, hogy könnyen meghívható legyen.

Ekkor létrejön egy másik függvény az aktív szálak kezeléséhez. Az egyszerűség kedvéért ez a függvény itt nem szerepel, de a [ExponentialBackoff. cs](https://github.com/Azure-Samples/azure-search-dotnet-samples/blob/master/optimize-data-indexing/v11/OptimizeDataIndexing/ExponentialBackoff.cs)-ben is megtalálható. A függvény hívható a következő paranccsal `hotels` , ahol a feltölteni kívánt adatok, `1000` a köteg mérete, és `8` az egyidejű szálak száma:

```csharp
await ExponentialBackoff.IndexData(indexClient, hotels, 1000, 8);
```

A függvény futtatásakor a következőhöz hasonló kimenetnek kell megjelennie:

![Az index adatfüggvényének kimenete](media/tutorial-optimize-data-indexing/index-data-start.png "Az index adatfüggvényének kimenete")

Ha a dokumentumok egy kötege meghibásodik, a rendszer kinyomtat egy hibaüzenetet, amely jelzi a hibát, és hogy a köteg újra próbálkozik:

```
[Batch starting at doc 6000 had partial failure]
[Retrying 560 failed documents]
```

A függvény futásának befejeződése után ellenőrizheti, hogy az összes dokumentum hozzá lett-e adva az indexhez.

## <a name="6---explore-index"></a>6 – az index megismerése

A kitöltött keresési indexet a program futtatása után, a programozott módon vagy a portálon található [**keresési böngésző**](search-explorer.md) használatával tekintheti meg.

### <a name="programatically"></a>Programozott módon

Két fő lehetőség áll rendelkezésre a dokumentumok számának ellenőrzéséhez egy indexben: a [dokumentumok száma API](/rest/api/searchservice/count-documents) és az [index-statisztika beolvasása API](/rest/api/searchservice/get-index-statistics). Ha a visszaadott dokumentumok száma alacsonyabb, mint az eredetileg várt érték, a két útvonal frissítésére is szükség lehet.

#### <a name="count-documents"></a>Dokumentumok számlálása

A dokumentumok száma művelet lekérdezi a dokumentumok számának számát egy keresési indexben:

```csharp
long indexDocCount = await searchClient.GetDocumentCountAsync();
```

#### <a name="get-index-statistics"></a>Index statisztikáinak beolvasása

Az index statisztikai lekérdezése művelet visszaadja az aktuális index bizonylatszámát, valamint a tárterület használatát. Az index statisztikája hosszabb időt vesz igénybe, mint a frissítendő dokumentumok száma.

```csharp
var indexStats = await indexClient.GetIndexStatisticsAsync(indexName);
```

### <a name="azure-portal"></a>Azure Portal

A Azure Portalban nyissa meg a keresési szolgáltatás **áttekintése** lapot, és keresse meg az **optimalizálás-indexelési** indexet az **indexek** listájában.

  ![Az Azure Cognitive Search indexek listája](media/tutorial-optimize-data-indexing/portal-output.png "Az Azure Cognitive Search indexek listája")

A *dokumentumok száma* és a *tárterület mérete* az [index statisztikai API beolvasása](/rest/api/searchservice/get-index-statistics) alapján történik, és a frissítés több percet is igénybe vehet.

## <a name="reset-and-rerun"></a>Alaphelyzetbe állítás és ismételt futtatás

A fejlesztés korai kísérleti szakaszaiban a tervezési iteráció legalkalmasabb megközelítése az objektumok törlése az Azure Cognitive Search és a kód újraépítésének engedélyezése. Az erőforrásnevek egyediek. Egy objektum törlése révén újból létrehozhatja azt ugyanazzal a névvel.

Az oktatóanyaghoz tartozó mintakód ellenőrzi a meglévő indexeket, és törli őket, hogy újra lehessen futtatni a kódot.

Az indexek törléséhez használhatja a portált is.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a saját előfizetésében dolgozik, a projekt végén érdemes lehet eltávolítani a már nem szükséges erőforrásokat. A továbbra is futó erőforrások költségekkel járhatnak. Az erőforrásokat törölheti egyesével, vagy az erőforráscsoport törlésével eltávolíthatja a benne lévő összes erőforrást is.

A bal oldali navigációs panelen a **minden erőforrás** vagy **erőforráscsoport** hivatkozás használatával megkeresheti és kezelheti az erőforrásokat a portálon.

## <a name="next-steps"></a>Következő lépések

Most, hogy már ismeri az adatfeldolgozási koncepciót, ismerkedjen meg közelebbről a Lucene lekérdezési architektúrával, és hogy a teljes szöveges keresés hogyan működik az Azure Cognitive Searchban.

> [!div class="nextstepaction"]
> [A teljes szöveges keresés működése az Azure Cognitive Searchben](search-lucene-query-architecture.md)