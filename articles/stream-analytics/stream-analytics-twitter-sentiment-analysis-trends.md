---
title: Közösségi médiatartalmak elemzése Azure Stream Analytics
description: Ez a cikk azt ismerteti, hogyan használható a közösségi média-elemzés Stream Analytics a Twitter Client API használatával. Lépésenkénti útmutató az események generálásához az élő irányítópulton lévő adatokhoz.
services: stream-analytics
author: enkrumah
ms.author: ebnkruma
ms.reviewer: jasonh
ms.service: stream-analytics
ms.topic: how-to
ms.date: 03/16/2021
ms.openlocfilehash: e2369e8f4349a3b4a83c1729c6414d319dd8419e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104589667"
---
# <a name="social-media-analysis-with-azure-stream-analytics"></a>Közösségi médiatartalmak elemzése Azure Stream Analytics

Ebből a cikkből megtudhatja, hogyan hozhat létre a közösségi média hangulati elemzési megoldását a valós idejű Twitter-események Azure Event Hubsba való bevezetésével. Azure Stream Analytics lekérdezést írhat az adatelemzéshez és az eredmények későbbi használatra történő tárolásához, vagy egy [Power bi](https://powerbi.com/) irányítópult létrehozásához, hogy valós időben szolgáltasson betekintést.

A közösségi média elemzési eszközei segítenek a szervezeteknek a trendek megismerésében. A Kiemelt témakörök olyan témák és hozzáállások, amelyek nagy mennyiségű bejegyzéssel rendelkeznek a közösségi médiában. A vélemény-és *adatbányászati* elemzések, valamint a közösségi média-elemzési eszközök segítségével határozzák meg a termékre vagy ötletre vonatkozó hozzáállást. 

A valós idejű Twitter trend elemzése nagyszerű példa egy elemzési eszközre, mert a hashtag-előfizetési modell lehetővé teszi, hogy bizonyos kulcsszavakat (hashtageket) Hallgasson meg, és fejlessze a hírcsatornát.

## <a name="scenario-social-media-sentiment-analysis-in-real-time"></a>Forgatókönyv: a közösségi média hangulatának elemzése valós időben

A legfrissebb hírekkel rendelkező vállalatok számára hasznos, hogy az olvasók számára azonnal releváns webhely-tartalmakat szerezzenek a versenytársakkal szemben. A vállalat a közösségi média-elemzést olyan témakörökre használja, amelyek az olvasóknak a Twitter-adatokat valós időben elemezve használják.

A Twitteren valós időben azonosított trendek felismeréséhez a vállalatnak valós idejű elemzésre van szüksége a tweetek mennyiségéről és a legfontosabb témakörökről.

## <a name="prerequisites"></a>Előfeltételek

Ebben a útmutatóban egy, a Twitterhez csatlakozó ügyfélalkalmazás és olyan tweetek megkeresi, amelyek bizonyos hashtagekkel rendelkeznek (amelyet beállíthat). Az alkalmazás futtatásához és a tweetek Azure streaming Analytics használatával történő elemzéséhez a következőkre van szükség:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).

* Egy [Twitter](https://twitter.com) -fiók.

* A TwitterClientCore alkalmazás, amely beolvassa a Twitter-hírcsatornát. Az alkalmazás beszerzéséhez töltse le a [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore).

* Telepítse a [a .net Core parancssori felülete](/dotnet/core/tools/?tabs=netcore2x) 2.1.0 verzióját.

Az alábbiakban a megoldás architektúráját fogjuk megvalósítani.

   ![A megoldás felépítéséhez használt különböző szolgáltatásokat és alkalmazásokat bemutató diagram.](./media/stream-analytics-twitter-sentiment-analysis-trends/solution-diagram.png "Megoldási diagram")

## <a name="create-an-event-hub-for-streaming-input"></a>Event hub létrehozása adatfolyam-bevitelhez

A minta alkalmazás eseményeket hoz létre, és leküldi őket egy Azure Event hub-ba. Az Azure Event Hubs a Stream Analytics esetében az események betöltésének előnyben részesített módszere. További információkért tekintse meg az [Azure Event Hubs dokumentációját](../event-hubs/event-hubs-about.md).

### <a name="create-an-event-hub-namespace-and-event-hub"></a>Event hub-névtér és Event hub létrehozása
Ebben a szakaszban létrehoz egy Event hub-névteret, és hozzáad egy Event hub-t a névtérhez. Az Event hub-névterek a kapcsolódó Event Bus-példányok logikai csoportosítására szolgálnak. 

1. Jelentkezzen be a Azure Portalba, és válassza az **erőforrás létrehozása** lehetőséget. Majd. Keresse meg **Event Hubs** , majd válassza a **Létrehozás** lehetőséget.

2. A **névtér létrehozása** lapon adja meg a névtér nevét. A névtérhez bármilyen nevet használhat, de a névnek érvényesnek kell lennie az URL-címhez, és egyedinek kell lennie az Azure-ban. 
    
3. Válassza ki az árképzési szintet és az előfizetést, majd hozzon létre vagy válasszon ki egy erőforráscsoportot. Ezután válasszon egy helyet, és válassza a **Létrehozás** lehetőséget. 
 
4. Ha a névtér befejezte a telepítést, navigáljon az erőforráscsoporthoz, és keresse meg az Event hub-névteret az Azure-erőforrások listájában. 

5. Az új névtérben válassza az **+ &nbsp; Event hub** elemet. 

6. Nevezze el az új Event hub *socialtwitter-eh* nevet. Más nevet is használhat. Ha így tesz, jegyezze fel, mert később szüksége lesz erre a névre. Az Event hub egyéb beállításait nem kell beállítania.
 
7. Válassza a **Létrehozás** lehetőséget.

### <a name="grant-access-to-the-event-hub"></a>Hozzáférés biztosítása az Event hub számára

Ahhoz, hogy egy folyamat adatküldést küldjön egy Event hubhoz, az Event hub-nak szüksége van egy olyan házirendre, amely engedélyezi a hozzáférést. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1.  Az Event hub-névtér bal oldalán lévő navigációs sávon válassza a **Event Hubs** elemet, amely az **entitások** szakaszban található. Ezután válassza ki az imént létrehozott Event hub-t.

2.  A bal oldali navigációs sávon válassza a **Beállítások** alatt található **megosztott elérési szabályzatok** elemet.

    >[!NOTE]
    >Az Event hub-névtérhez és az Event hub-hoz tartozó közös hozzáférési szabályzatok lehetőség van. Győződjön meg arról, hogy az Event hub környezetében dolgozik, nem pedig a teljes Event hub-névtérrel.

3.  A hozzáférési házirend lapon válassza a **+ Hozzáadás** lehetőséget. Ezután írja be a *socialtwitter-hozzáférés* **nevet a szabályzat neveként** , és jelölje be a **kezelés** jelölőnégyzetet.
 
4.  Válassza a **Létrehozás** lehetőséget.

5.  Miután telepítette a házirendet, válassza ki a szabályzatot a megosztott hozzáférési házirendek listájából.

6.  Keresse meg a **kapcsolódási karakterlánc elsődleges kulcsát** , és kattintson a kapcsolódási karakterlánc melletti Másolás gombra.
 
7.  Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. A következő szakaszhoz szükség van erre a kapcsolattípus-karakterláncra, miután elvégezte néhány kisebb módosítást.

   A következőhöz hasonló a kapcsolatok karakterlánca:
   
   ```
   Endpoint=sb://EVENTHUBS-NAMESPACE.servicebus.windows.net/;SharedAccessKeyName=socialtwitter-access;SharedAccessKey=Gw2NFZw6r...FxKbXaC2op6a0ZsPkI=;EntityPath=socialtwitter-eh
   ```

   Figyelje meg, hogy a kapcsolatok karakterlánca több kulcs-érték párokat tartalmaz, pontosvesszővel elválasztva: `Endpoint` ,, `SharedAccessKeyName` `SharedAccessKey` és `EntityPath` .  

   > [!NOTE]
   > A biztonság érdekében a példában szereplő, a kapcsolatok karakterláncának egyes részei el lettek távolítva.

## <a name="configure-and-start-the-twitter-client-application"></a>A Twitter-ügyfélalkalmazás konfigurálása és elindítása

Az ügyfélalkalmazás közvetlenül a Twitterről szerzi be a tweetek eseményeit. Ennek érdekében engedélyre van szüksége a Twitter streaming API-k meghívásához. Az engedély konfigurálásához létre kell hoznia egy alkalmazást a Twitteren, amely egyedi hitelesítő adatokat (például OAuth tokent) generál. Ezután beállíthatja, hogy az ügyfélalkalmazás ezeket a hitelesítő adatokat használja, amikor API-hívásokat végez. 

### <a name="create-a-twitter-application"></a>Twitter-alkalmazás létrehozása
Ha még nem rendelkezik olyan Twitter-alkalmazással, amelyet ehhez a útmutatóhoz használhat, létrehozhat egyet. Már rendelkeznie kell egy Twitter-fiókkal.

> [!NOTE]
> Az alkalmazások létrehozásához és a kulcsok, titkok és tokenek beszerzéséhez szükséges pontos folyamat a Twitteren változhat. Ha ezek az utasítások nem egyeznek meg a Twitter webhelyén láthatókkal, tekintse meg a Twitter fejlesztői dokumentációját.

1. A böngészőben nyissa meg a [Twitter fejlesztőknek](https://developer.twitter.com/en/apps), hozzon létre egy fejlesztői fiókot, és válassza **az alkalmazás létrehozása** lehetőséget. Előfordulhat, hogy megjelenik egy üzenet, amely azt jelzi, hogy egy Twitter fejlesztői fiókra van szüksége. Nyugodtan megteheti, és az alkalmazás jóváhagyását követően meg kell jelennie egy megerősítő e-mailben. Több napot is igénybe vehet egy fejlesztői fiók jóváhagyása.

   ![A képernyőképen az alkalmazás létrehozása gomb látható.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details.png "Twitter-alkalmazás részletei")

2. Az **Alkalmazás létrehozása** oldalon adja meg az új alkalmazás adatait, majd válassza a **Twitter-alkalmazás létrehozása** parancsot.

   ![Képernyőfelvétel: az alkalmazás részleteit tartalmazó ablaktábla, ahol megadhatja az alkalmazás értékeit.](./media/stream-analytics-twitter-sentiment-analysis-trends/provide-twitter-app-details-create.png "Twitter-alkalmazás részletei")

3. Az alkalmazás lapon válassza a **kulcsok és tokenek** fület, és másolja a **fogyasztói API-kulcs** és a **fogyasztói API titkos kulcsa** értékeit. A hozzáférési tokenek létrehozásához a hozzáférési jogkivonat **és a hozzáférési jogkivonat titka** területen válassza a **Létrehozás** lehetőséget. Másolja a **Hozzáférési token** és a **Hozzáférési token titkos kulcsa** mező értékeit.

   Mentse a Twitter-alkalmazáshoz lekért értékeket. Az értékeket később kell megadnia.

> [!NOTE]
> A Twitter-alkalmazás kulcsai és titkai biztosítják a Twitter-fiók elérését. Ezeket az adatokat bizalmasként kell kezelni, ugyanúgy, mint a Twitter-jelszót. Például ne ágyazza be ezeket az adatokat egy olyan alkalmazásba, amelyet másoknak adnak meg. 

### <a name="configure-the-client-application"></a>Az ügyfélalkalmazás konfigurálása

Létrehoztunk egy olyan ügyfélalkalmazás-alkalmazást, amely Twitter-adatokhoz csatlakozik a [Twitter streaming API](https://dev.twitter.com/streaming/overview) -kkal a témakörök adott készletével kapcsolatos Tweet-események összegyűjtéséhez.

Az alkalmazás futtatása előtt szükség van bizonyos információkra, például a Twitter-kulcsokra és az Event hub kapcsolati karakterláncára.

1. Győződjön meg arról, hogy letöltötte a [TwitterClientCore](https://github.com/Azure/azure-stream-analytics/tree/master/DataGenerators/TwitterClientCore) alkalmazást az előfeltételek részben leírtak szerint.

2. Szövegszerkesztő használatával nyissa meg a *App.config* fájlt. Hajtsa végre a következő módosításokat a `<appSettings>` elemen:

   * Állítsa be `oauth_consumer_key` a Twitter fogyasztói kulcsát (API-kulcs). 
   * Állítsa be `oauth_consumer_secret` a Twitter fogyasztói titkát (API titkos kulcs).
   * Állítsa be `oauth_token` a Twitter hozzáférési tokent.
   * Állítsa be `oauth_token_secret` a Twitter hozzáférési token titkát.
   * Állítsa be `EventHubNameConnectionString` a kapcsolódási karakterláncot.
   * Állítsa `EventHubName` az Event hub nevére (ez az entitás elérési útjának értéke).

3. Nyissa meg a parancssort, és navigáljon ahhoz a könyvtárhoz, ahol a TwitterClientCore-alkalmazás található. A parancs használatával hozza `dotnet build` létre a projektet. Ezután használja az parancsot az `dotnet run` alkalmazás futtatásához. Az alkalmazás Tweeteket küld az Event hub-nak.

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Most, hogy a tweet-események valós időben áramlanak a Twitterről, beállíthat egy Stream Analytics feladatot, amellyel valós időben elemezheti ezeket az eseményeket.

1. A Azure Portal navigáljon az erőforráscsoporthoz, és válassza a **+ Hozzáadás** lehetőséget. Ezután keresse meg **stream Analytics feladatot** , és válassza a **Létrehozás** lehetőséget.

2. Nevezze el a feladatot, `socialtwitter-sa-job` és adjon meg egy előfizetést, egy erőforráscsoportot és egy helyet.

    Érdemes a feladatot és az Event hub-t ugyanabban a régióban elhelyezni a legjobb teljesítmény érdekében, és így nem kell fizetnie a régiók közötti adatátvitel során.

3. Válassza a **Létrehozás** lehetőséget. Ezután navigáljon a feladatokhoz, amikor a telepítés befejeződött.

## <a name="specify-the-job-input"></a>Adja meg a feladatok bemenetét

1. A Stream Analyticsi feladatban válassza a bal oldali menü **bemenetek** elemét a **feladatok topológiája** alatt.

2. Válassza az **+ &nbsp; adatfolyam hozzáadása bemeneti**  >  **esemény hub** elemet. Töltse ki az **új beviteli** űrlapot a következő információkkal:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias| *TwitterStream* | Adja meg a bemenet aliasát. |
   |Előfizetés  | \<Your subscription\> |  Válassza ki a használni kívánt Azure-előfizetést. |
   |Event Hubs-névtér | *ASA – Twitter – eventhub* |
   |Eseményközpont neve | *socialtwitter – eh* | Válassza a *meglévő használata* lehetőséget. Ezután válassza ki a létrehozott Event hubot.|
   |Esemény tömörítési típusa| GZip | Az adattömörítési típus.|

   Hagyja meg a többi alapértelmezett értéket, majd válassza a **Mentés** lehetőséget.

## <a name="specify-the-job-query"></a>A feladatok lekérdezésének meghatározása

A Stream Analytics egy egyszerű, deklaratív lekérdezési modellt támogat, amely leírja a transzformációkat. Ha többet szeretne megtudni a nyelvről, tekintse meg a [Azure stream Analytics lekérdezés nyelvi referenciáját](/stream-analytics-query/stream-analytics-query-language-reference). Ez a útmutató segítséget nyújt a Twitter-alapú adatlekérdezések létrehozásához és teszteléséhez.

Ha össze szeretné hasonlítani a témakörök közötti említések számát, egy leválasztó ablak használatával lekérheti a témakörben szereplő, öt másodpercenként [megjelenő](/stream-analytics-query/tumbling-window-azure-stream-analytics) megjegyzések számát.

1. A feladatok **áttekintésében** válassza a lekérdezés **szerkesztése** lehetőséget a lekérdezési mező jobb felső sarkában. Az Azure felsorolja a feladathoz konfigurált bemeneteket és kimeneteket, és lehetővé teszi, hogy lekérdezést hozzon létre a bemeneti adatfolyamnak a kimenetre való elküldéséhez.

2. Módosítsa a lekérdezést a lekérdezés-szerkesztőben a következőre:

   ```sql
   SELECT *
   FROM TwitterStream
   ```

3. Az üzenetekből származó eseményeknek a lekérdezés alatti **bemeneti előnézet** ablakban kell megjelenniük. Győződjön meg arról, hogy a **nézet** **JSON**-ra van beállítva. Ha nem lát adatokat, győződjön meg arról, hogy az adat-előállító eseményeket küld az Event hub-nak, és hogy a bemenet tömörítési típusaként a **gzip** lehetőséget választotta.

4. Válassza a **lekérdezés tesztelése** lehetőséget, és figyelje meg az eredményeket a lekérdezés alatt a **teszt eredményei** ablakban.

5. Módosítsa a lekérdezést a Kódszerkesztő alkalmazásban a következőre, és válassza a **teszt lekérdezés** lehetőséget:

   ```sql
   SELECT System.Timestamp as Time, text
   FROM TwitterStream
   WHERE text LIKE '%Azure%'
   ```

6. Ez a lekérdezés az *Azure* kulcsszót tartalmazó összes tweetet adja vissza.

## <a name="create-an-output-sink"></a>Kimeneti fogadó létrehozása

Már definiált egy esemény-adatfolyamot, egy Event hub-bemenetet az események betöltéséhez, valamint egy lekérdezést, amely az adatfolyamon végzett átalakítást végzi. Az utolsó lépés a feladatokhoz tartozó kimeneti fogadó definiálása.  

Ebben a útmutatóban az összesített Tweet-eseményeket a feladatsorból az Azure Blob Storage-ba írhatja.  Az eredményeket az alkalmazás igényeitől függően az Azure SQL Database, az Azure Table Storage, a Event Hubs vagy a Power BI használatával is leküldheti.

## <a name="specify-the-job-output"></a>Adja meg a feladatok kimenetét

1. A bal oldali navigációs menü **feladatok topológiája** szakaszában válassza a **kimenetek** lehetőséget. 

2. A **kimenetek** lapon kattintson a **+ &nbsp; Hozzáadás** és a **blob Storage/Data Lake Storage Gen2** elemre:

   * **Kimeneti alias**: használja a nevet `TwitterStream-Output` . 
   * **Importálási beállítások**: válassza **a tároló kiválasztása az előfizetések** közül lehetőséget.
   * **Storage-fiók**. Válassza ki a tárfiókot.
   * **Tároló**. Válassza az **új létrehozása** elemet, és adja meg az értéket `socialtwitter` .
   
4. Kattintson a **Mentés** gombra.   

## <a name="start-the-job"></a>A feladat indítása

Meg van adva a feladatok bemenete, lekérdezése és kimenete. Készen áll arra, hogy elindítsa a Stream Analytics feladatot.

1. Győződjön meg arról, hogy a TwitterClientCore alkalmazás fut. 

2. A feladatok áttekintésében válassza az **Indítás** lehetőséget.

3. A **kezdési feladatok** lapon a **feladatok kimenetének kezdési idejéhez** válassza a **most** lehetőséget, majd kattintson a **Start** gombra.

## <a name="get-support"></a>Támogatás kérése
További segítségért próbálja ki a [Microsoft Q&a Azure stream Analytics kérdéseit](/answers/topics/azure-stream-analytics.html).

## <a name="next-steps"></a>Következő lépések
* [Bevezetés a Azure Stream Analyticsba](stream-analytics-introduction.md)
* [Get started using Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md) (Bevezetés az Azure Stream Analytics használatába)
* [Scale Azure Stream Analytics jobs (Azure Stream Analytics-feladatok méretezése)](stream-analytics-scale-jobs.md)
* [Azure Stream Analytics Query Language Reference (Referencia az Azure Stream Analytics lekérdezési nyelvhez)](/stream-analytics-query/stream-analytics-query-language-reference)
* [Az Azure Stream Analytics felügyeleti REST API referenciája](/rest/api/streamanalytics/)
