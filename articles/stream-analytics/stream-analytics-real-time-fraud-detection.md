---
title: Oktatóanyag – a csalárd hívási adatmennyiség elemzése Azure Stream Analytics és az eredmények megjelenítése Power BI irányítópulton
description: Ez az oktatóanyag átfogóan bemutatja, hogyan használható az Azure Stream Analytics a csaló hívások elemzésére a telefonhívási streamekben.
author: enkrumah
ms.author: ebnkruma
ms.service: stream-analytics
ms.topic: tutorial
ms.custom: contperf-fy21q2
ms.date: 12/17/2020
ms.openlocfilehash: 1e26159c07ca551a78ee2f83a0ca64779c60f7b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98018870"
---
# <a name="tutorial-analyze-fraudulent-call-data-with-stream-analytics-and-visualize-results-in-power-bi-dashboard"></a>Oktatóanyag: a hamis hívási adatmennyiség elemzése Stream Analytics és az eredmények megjelenítése Power BI irányítópulton

Ez az oktatóanyag bemutatja, hogyan elemezheti a telefonhívási adataikat Azure Stream Analytics használatával. Az ügyfélalkalmazás által létrehozott telefonhívási adatok olyan csalárd hívásokat tartalmaznak, amelyeket a Stream Analytics feladatokkal szűrnek. Az oktatóanyag módszereit más típusú csalások észlelésére is használhatja, például a hitelkártya-csalások vagy a személyazonosság-lopás.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Minta telefonhívás-adathívások készítése és elküldése az Azure Event Hubsba.
> * Stream Analytics-feladat létrehozása.
> * Adja meg a feladatok bemenetét és kimenetét.
> * Lekérdezések definiálása a csalárd hívások szűréséhez.
> * Tesztelje és indítsa el a feladatot.
> * Az eredmények megjelenítése Power BIban.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt elkezdené, győződjön meg arról, hogy végrehajtotta a következő lépéseket:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Töltse le a [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) telefonhívás-eseménykészítő alkalmazást a Microsoft letöltőközpontjából, vagy szerezze be a forráskódot a [GitHubról](https://aka.ms/azure-stream-analytics-telcogenerator).
* Szüksége lesz egy Power BI-fiókra.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-an-azure-event-hub"></a>Azure Event Hub létrehozása

Mielőtt a Stream Analytics elemezni tudná a csaló hívások adatstreamjét, el kell küldenie az adatokat az Azure-ba. Ebben az oktatóanyagban az [Azure Event Hubs](../event-hubs/event-hubs-about.md) használatával során fog adatokat küldeni az Azure-ba.

Eseményközpont létrehozásához, majd az adatok az eseményközpontba küldéséhez hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. Válassza **az erőforrás létrehozása**  >  **eszközök internetes hálózata**  >  **Event Hubs** lehetőséget.

   ![Azure Event hub létrehozása a portálon](media/stream-analytics-real-time-fraud-detection/find-event-hub-resource.png)
3. Töltse ki a **névtér létrehozása** panelt a következő értékekkel:

   |**Beállítás**  |**Ajánlott érték** |**Leírás**  |
   |---------|---------|---------|
   |Name     | asaTutorialEventHub        |  Az eseményközpont névterének azonosítására szolgáló egyedi név.       |
   |Előfizetés     |   \<Your subscription\>      |   Válasszon ki egy Azure-előfizetést, ahol létre kívánja hozni az eseményközpontot.      |
   |Erőforráscsoport     |   MyASADemoRG      |  Válassza az **Új létrehozása** elemet, majd adja meg a fiók új erőforráscsoport-nevét.       |
   |Hely     |   USA 2. nyugati régiója      |    Az a hely, ahol az eseményközpont-névtér üzembe helyezhető.     |

4. A többi beállításnál használja az alapértelmezett beállításokat, majd válassza a **felülvizsgálat + létrehozás** lehetőséget. Ezután válassza a **Létrehozás** lehetőséget a telepítés elindításához.

   ![Event hub-névtér létrehozása Azure Portalban](media/stream-analytics-real-time-fraud-detection/create-event-hub-namespace.png)

5. Ha a névtér befejezte a telepítést, lépjen a **minden erőforrás** elemre, és keresse meg a *asaTutorialEventHub* az Azure-erőforrások listájában. A megnyitásához válassza a *asaTutorialEventHub* lehetőséget.

6. Ezután válassza a **+ Event hub** lehetőséget, és adjon meg egy **nevet** az Event hub számára. Állítsa a **partíciók darabszámát** 2 értékre.  A többi beállításnál használja az alapértelmezett beállításokat, majd válassza a **Létrehozás** lehetőséget. Várjon, amíg az üzembe helyezés sikeresen befejeződik.

   ![Event hub-konfiguráció a Azure Portal](media/stream-analytics-real-time-fraud-detection/create-event-hub-portal.png)

### <a name="grant-access-to-the-event-hub-and-get-a-connection-string"></a>Az eseményközponthoz való hozzáférés engedélyezése és kapcsolati sztring beszerzése

Ahhoz, hogy egy alkalmazás adatküldhető legyen az Azure Event Hubsba, az Event hub-nak rendelkeznie kell olyan házirenddel, amely lehetővé teszi a hozzáférést. A hozzáférési szabályzat egy kapcsolati sztringet hoz létre, amelyben megtalálhatók az engedélyezési információk.

1. Navigáljon az előző lépésben létrehozott *MyEventHub*. A **Beállítások** területen válassza a **Megosztott elérési szabályzatok**, majd a **+Hozzáadás** elemet.

2. Adja a szabályzatnak a **MyPolicy** nevet, és ellenőrizze, hogy **Kezelés** lehetőség be van-e jelölve. Ezután kattintson a **Létrehozás** elemre.

   ![Eseményközpont megosztott elérési házirendjének létrehozása](media/stream-analytics-real-time-fraud-detection/create-event-hub-access-policy.png)

3. A szabályzat létrehozása után válassza ki a szabályzat nevét a szabályzat megnyitásához. Keresse meg a **kapcsolatok karakterláncát – elsődleges kulcs**. A kapcsolódási karakterlánc mellett kattintson a **Másolás** gombra.

   ![A megosztott elérési szabályzat kapcsolati sztringjének mentése](media/stream-analytics-real-time-fraud-detection/save-connection-string.png)

4. Illessze be a kapcsolati sztringet egy szövegszerkesztőbe. A következő szakaszban szükség lesz erre a kapcsolati sztringre.

   A kapcsolati sztring a következőképpen néz ki:

   `Endpoint=sb://<Your event hub namespace>.servicebus.windows.net/;SharedAccessKeyName=<Your shared access policy name>;SharedAccessKey=<generated key>;EntityPath=<Your event hub name>`

   Figyelje meg, hogy a kapcsolati sztring több, pontosvesszővel elválasztott kulcs–érték párt tartalmaz: **Endpoint**, **SharedAccessKeyName**, **SharedAccessKey** és **EntityPath**.

## <a name="start-the-event-generator-application"></a>Az eseménylétrehozó alkalmazás elindítása

A TelcoGenerator alkalmazást úgy kell beállítania az indítása előtt, hogy a korábban beállított Azure Event Hubs-eseményközpontnak küldjön adatokat.

1. Bontsa ki a [TelcoGenerator.zip](https://download.microsoft.com/download/8/B/D/8BD50991-8D54-4F59-AB83-3354B69C8A7E/TelcoGenerator.zip) fájl tartalmát.
2. Nyissa meg a `TelcoGenerator\TelcoGenerator\telcodatagen.exe.config` fájlt egy tetszőleges szövegszerkesztőben, ahol több fájl is található `.config` , ezért győződjön meg arról, hogy a megfelelőt nyitja meg.

3. Frissítse a konfigurációs fájl `<appSettings>` elemét az alábbi részletekkel:

   * Állítsa az *EventHubName* kulcsot a kapcsolati sztringben található EntityPath értékére.
   * A *Microsoft.ServiceBus.ConnectionString* kulcs értékét állítsa a kapcsolati sztring értékére az EntityPath értéke nélkül. Ne felejtse el eltávolítani a pontosvesszőt, amely megelőzi a EntityPath értéket.

4. Mentse a fájlt.

5. Ezután nyisson meg egy parancsablakot, és lépjen arra a mappára, ahová kicsomagolta a TelcoGenerator alkalmazást. Ezután írja be a következő parancsot:

   ```cmd
   .\telcodatagen.exe 1000 0.2 2
   ```

   A paranccsal a következő paraméterek használhatók:
   * Óránkénti hívásadatrekordok száma.
   * Csalások valószínűségének százalékos aránya, amely azt adja meg, hogy az alkalmazás milyen gyakran szimuláljon csaló hívásokat. A 0,2-es érték azt jelenti, hogy nagyjából a hívások 20%-a fog csalónak tűnni.
   * Időtartam órában, amely azt adja meg, hogy hány óráig fusson az alkalmazás. Az alkalmazást bármikor leállíthatja, ha a parancssorban leállítja a folyamatot (**CTRL + C**).

   Néhány másodperc elteltével az alkalmazás elkezdi kijelezni a hívásrekordokat a képernyőn, miközben az eseményközpontba küldi őket. A telefonhívási adatok a következő mezőket tartalmazzák:

   |**Rekord**  |**Definíció**  |
   |---------|---------|
   |CallrecTime    |  A hívási kezdési idejét jelölő időbélyegző.       |
   |SwitchNum     |  A hívás csatlakozásához használt telefonkapcsoló. Ebben a példában a kapcsolók olyan karakterláncok, amelyek a származási országot/régiót (USA, Kína, Egyesült Királyság, Németország vagy Ausztrália) jelölik.       |
   |CallingNum     |  A hívó telefonszáma.       |
   |CallingIMSI     |  Az International Mobile Subscriber Identity (IMSI). Ez a hívó egyedi azonosítója.       |
   |CalledNum     |   A hívott fél telefonszáma.      |
   |CalledIMSI     |  International Mobile Subscriber Identity (IMSI). Ez a hívott fél egyedi azonosítója.       |

## <a name="create-a-stream-analytics-job"></a>Stream Analytics-feladat létrehozása

Most, hogy már rendelkezik a hívási események streamjével, létrehozhat egy olyan Stream Analytics-feladatot, amely beolvassa az adatokat az eseményközpontból.

1. Stream Analytics-feladat létrehozásához lépjen az [Azure Portalra](https://portal.azure.com/).

2. Válassza az **erőforrás létrehozása** lehetőséget, és keresse meg **stream Analytics feladatot**. Válassza a **stream Analytics feladatok** csempét, és válassza a * létrehozás * * lehetőséget.

3. Töltse ki az **új stream Analytics feladatok** űrlapot a következő értékekkel:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Feladat neve     |  ASATutorial       |   Az eseményközpont névterének azonosítására szolgáló egyedi név.      |
   |Előfizetés    |  \<Your subscription\>   |   Válassza ki, melyik Azure-előfizetésben kívánja létrehozni a feladatot.       |
   |Erőforráscsoport   |   MyASADemoRG      |   Válassza a **Meglévő használata** lehetőséget, és adjon meg egy új erőforráscsoport-nevet a fiókjának.      |
   |Hely   |    USA 2. nyugati régiója     |      A hely, ahol a feladat üzembe helyezhető. Ajánlott a feladatot és az eseményközpontot ugyanabba a régióba helyezni, az optimális teljesítmény érdekében, továbbá így elkerülheti a régiók közötti adatátvitel díját is.      |
   |Üzemeltetési környezet    | Felhőbeli        |     A Stream Analytics-feladatok a felhőben vagy a peremhálózaton is üzembe helyezhetők. A felhő lehetővé teszi az Azure-felhőbe való üzembe helyezést, az Edge pedig lehetővé teszi, hogy IoT Edge eszközre telepítsen.    |
   |Streamelési egységek     |    1       |      A Streamelési egységek azoknak a számítási erőforrásoknak felelnek meg, amelyek a feladat futtatásához szükségesek. Ez az érték alapértelmezés szerint 1. További információ a streamelési egységek skálázásáról: [A streamelési egységek ismertetése és módosítása](stream-analytics-streaming-unit-consumption.md).      |

4. A többi beállításnál használja az alapértelmezett beállításokat, válassza a **Létrehozás** lehetőséget, és várja meg, amíg a telepítés sikeres lesz.

   ![Azure Stream Analytics-feladat létrehozása](media/stream-analytics-real-time-fraud-detection/create-stream-analytics-job.png)

## <a name="configure-job-input"></a>Feladatbemenet konfigurálása

A következő lépés egy bemeneti forrás megadása, amelyből a feladat be tudja olvasni az adatokat az előző szakaszban létrehozott eseményközpontot használva.

1. A Azure Portal nyissa meg a **minden erőforrás** lapot, és keresse meg a *ASATutorial* stream Analytics feladatot.

2. A Stream Analytics-feladathoz tartozó **feladatok topológiája** szakaszban válassza a **bemenetek** lehetőséget.

3. Válassza a **+ Streambemenet hozzáadása**, majd az **Eseményközpont** lehetőséget. Töltse ki a bemeneti űrlapot a következő értékekkel:

   |**Beállítás**  |**Ajánlott érték**  |**Leírás**  |
   |---------|---------|---------|
   |Bemeneti alias     |  CallStream       |  Adjon meg egy rövid nevet a bemenet azonosításához. A bemeneti alias csak alfanumerikus karaktereket, kötőjeleket és aláhúzásjeleket tartalmazhat, és 3–63 karakter hosszúságúnak kell lennie.       |
   |Előfizetés    |   \<Your subscription\>      |   Jelölje ki azt az Azure-előfizetést, ahol létrehozta az eseményközpontot. Az eseményközpont ugyanabban az előfizetésben, mint a Stream Analytics-feladat, vagy egy másikban is.       |
   |Eseményközpont-névtér    |  asaTutorialEventHub       |  Válassza ki az előző szakaszban létrehozott eseményközpont-névteret. Az aktuális előfizetésben elérhető összes eseményközpont-névtér megjelenik a legördülő listában.       |
   |Eseményközpont neve    |   MyEventHub      |  Válassza ki az előző szakaszban létrehozott eseményközpontot. Az aktuális előfizetésben elérhető összes eseményközpont megjelenik a legördülő listában.       |
   |Eseményközpont szabályzatának neve   |  MyPolicy       |  Válassza ki az előző lépésben létrehozott megosztott elérési házirendet. Az aktuális előfizetésben elérhető összes eseményközpont-szabályzat megjelenik a legördülő listában.       |

4. A többi beállításnál hagyja meg az alapértelmezett beállításokat, majd válassza a **Mentés** lehetőséget.

   ![Azure Stream Analytics bemenet konfigurálása](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-input.png)

## <a name="configure-job-output"></a>Feladatkimenet konfigurálása

Az utolsó lépés egy kimeneti fogadó definiálása, ahol a feladatok írhatják az átalakított adatokat. Ebben az oktatóanyagban kimeneti adatokat hoz létre és vizualizál a Power BI használatával.

1. A Azure Portal nyissa meg az **összes erőforrást**, és válassza ki a *ASATutorial* stream Analytics feladatot.

2. A Stream Analytics feladatok **feladatok topológiája** szakaszában válassza a **kimenetek** lehetőséget.

3. Válassza a **+**  >  **Power bi** hozzáadása elemet. Ezután válassza az **Engedélyezés** lehetőséget, és kövesse az utasításokat a Power bi hitelesítéséhez.

:::image type="content" source="media/stream-analytics-real-time-fraud-detection/authorize-power-bi.png" alt-text="Power BI engedélyezése gomb":::

4. Töltse ki a kimenet űrlapot a következő részletekkel, majd válassza a **Mentés** lehetőséget:

   |**Beállítás**  |**Ajánlott érték**  |
   |---------|---------|
   |Kimeneti alias  |  MyPBIoutput  |
   |Csoport munkaterülete| Saját munkaterület |
   |Adatkészlet neve  |   ASAdataset  |
   |Table name (Táblázat neve) |  ASATable  |
   | Hitelesítési módszer | Felhasználói jogkivonat |

   ![Stream Analytics kimenet konfigurálása](media/stream-analytics-real-time-fraud-detection/configure-stream-analytics-output.png)

   Ez az oktatóanyag a *felhasználói jogkivonat* hitelesítési módját használja. A felügyelt identitás használatához lásd: [a felügyelt identitás használata a Azure stream Analytics-feladatok hitelesítéséhez Power bi](powerbi-output-managed-identity.md).

## <a name="create-queries-to-transform-real-time-data"></a>Lekérdezések létrehozása a valós idejű adatértékek átalakításához

Ezen a ponton egy Stream Analytics feladatsorral beolvasható egy bejövő adatfolyam. A következő lépés egy olyan lekérdezés létrehozása, amely valós időben elemzi az adatelemzést. A lekérdezések olyan SQL-szerű nyelvet használnak, amely a Stream Analyticsra vonatkozó egyes bővítményekkel rendelkezik.

Az oktatóanyag ezen szakaszában több lekérdezést is létrehozhat és tesztel, így megtanulhatja, hogyan alakíthat át egy bemeneti adatfolyamot elemzésre. 

Az itt létrehozott lekérdezések csak az átalakított adatképernyőt fogják megjeleníteni. Egy későbbi szakaszban az átalakított adatértékeket Power BIba írja.

Ha többet szeretne megtudni a nyelvről, tekintse meg a [Azure stream Analytics lekérdezés nyelvi referenciáját](/stream-analytics-query/stream-analytics-query-language-reference).

### <a name="test-using-a-pass-through-query"></a>Tesztelés átmenő lekérdezés használatával

Ha minden eseményt archiválni szeretne, használhat egy áteresztő lekérdezést, amely az esemény hasznos adataiban lévő összes mezőt beolvassa.

1. Navigáljon a Stream Analytics feladathoz a Azure Portal, és válassza a **lekérdezés** lehetőséget a *feladatok topológiája* alatt. 

2. A lekérdezési ablakban adja meg a következő lekérdezést:

   ```SQL
   SELECT 
       *
   FROM 
       CallStream
   ```

    >[!NOTE]
    >Az SQL-hez hasonlóan a kulcsszavak nem megkülönböztetik a kis-és nagybetűket, és a szóköz nem jelentős.

    Ebben a lekérdezésben az az `CallStream` alias, amelyet a bemenet létrehozásakor adott meg. Ha más aliast használt, használja helyette a nevet.

3. Válassza a **teszt lekérdezés** lehetőséget.

    A Stream Analyticsi feladattal futtatja a lekérdezést a bemeneti adatok alapján, és megjeleníti a kimenetet az ablak alján. Az eredmények azt jelzik, hogy az Event hub és a streaming Analytics-feladatok megfelelően vannak konfigurálva.

    :::image type="content" source="media/stream-analytics-real-time-fraud-detection/sample-output-passthrough.png" alt-text="Minta kimenete a teszt lekérdezésből":::

    A megjelenő rekordok pontos száma attól függ, hogy hány rekordot rögzít a rendszer a mintában.

### <a name="reduce-the-number-of-fields-using-a-column-projection"></a>Mezők számának csökkentése oszlop kivetítésével

Sok esetben az elemzéshez nem szükséges a bemeneti adatfolyam összes oszlopa. A lekérdezéssel a visszaadott mezők kisebb készletét lehet feltervezni, mint az áteresztő lekérdezésben.

Futtassa a következő lekérdezést, és figyelje meg a kimenetet.

```SQL
SELECT CallRecTime, SwitchNum, CallingIMSI, CallingNumCalledNum 
FROM 
    CallStream
```

### <a name="count-incoming-calls-by-region-tumbling-window-with-aggregation"></a>Bejövő hívások száma régiónként: kiesési ablak összesítéssel

Tegyük fel, hogy régiónként szeretné megszámolni a bejövő hívások számát. A folyamatos átviteli adatok esetében, ha olyan összesítő függvényeket kíván végrehajtani, mint például a számlálás, az adatfolyamot időbeli egységbe kell osztania, mivel maga az adatfolyam gyakorlatilag végtelen. Ezt egy streaming Analytics- [ablak függvény](stream-analytics-window-functions.md)használatával végezheti el. Ezután használhatja az adott ablakban található, egységként szolgáló adatmennyiséget.

Ehhez az átalakításhoz olyan időbeli Windows-sorozatot szeretne használni, amely nem fedi át az átfedést – minden ablaknak külön adatkészlete lesz, amelyet csoportosíthatjuk és összesíteni lehet. Az ilyen típusú ablakokat a rendszer *kieséses ablaknak* nevezzük. A kiesési ablakban a bejövő hívások száma látható `SwitchNum` , amely az országot vagy régiót jelöli, ahol a hívás származik.

1. Illessze be a következő lekérdezést a lekérdezés-szerkesztőbe:

    ```SQL
    SELECT 
        System.Timestamp as WindowEnd, SwitchNum, COUNT(*) as CallCount 
    FROM
        CallStream TIMESTAMP BY CallRecTime 
    GROUP BY TUMBLINGWINDOW(s, 5), SwitchNum
    ```

    Ez a lekérdezés a `Timestamp By` záradékban található kulcsszót használja `FROM` annak megadásához, hogy a bemeneti adatfolyam melyik timestamp mezőjében használja a kiesési időszakot. Ebben az esetben az ablak az egyes rekordok mezői szerint osztja szét az adatok szegmenseit `CallRecTime` . (Ha nincs megadva mező, az ablakos művelet azt az időpontot használja, amikor az egyes események megérkeznek az Event hub-ba. Tekintse meg a "megérkezési idő vs Application Time" kifejezést [stream Analytics lekérdezési nyelvi referenciában](/stream-analytics-query/stream-analytics-query-language-reference). 

    A vetítés magában foglalja `System.Timestamp` , amely az egyes ablak végének időbélyegét adja vissza. 

    Annak megadásához, hogy egy feltételt tartalmazó ablakot kíván használni, használja a [TUMBLINGWINDOW](/stream-analytics-query/tumbling-window-azure-stream-analytics) függvényt a `GROUP BY` záradékban. A függvényben meg kell adnia egy időegységet (bárhol a másodperctől egy napra) és egy ablak méretével (hány egység). Ebben a példában a kihelyező ablak 5 másodperces intervallumokból áll, így minden 5 másodpercenkénti hívás esetén az ország/régió alapján kell számolni.

2. Válassza a **teszt lekérdezés** lehetőséget. Az eredmények között figyelje meg, hogy a **WindowEnd** alatti időbélyegek 5 másodperces növekményekben vannak.

### <a name="detect-sim-fraud-using-a-self-join"></a>SIM-csalások észlelése Önillesztés használatával

Ebben a példában úgy gondolja, hogy a hamis használat olyan hívás, amely ugyanabból a felhasználóból származik, de egy másik helyen, 5 másodpercen belül. Például ugyanaz a felhasználó nem indíthat szabályosan hívásokat egyszerre az USA-ból és Ausztráliából.

Az ilyen esetek ellenőrzéséhez használhatja a folyamatos átviteli adatátvitelt úgy, hogy a streamet saját magához csatlakoztassa az `CallRecTime` érték alapján. Ezután megkeresheti azokat a hívási rekordokat, amelyekben az `CallingIMSI` érték (az eredeti szám) megegyezik, de a `SwitchNum` (származási ország/régió) értéke nem ugyanaz.

Ha adatfolyam-továbbítási adatokkal csatlakozik, az illesztésnek bizonyos korlátozásokat kell biztosítania, hogy az egyező sorok mennyi idő alatt legyenek elkülönítve. Amint azt korábban említettük, a folyamatos átviteli adattovábbítás gyakorlatilag végtelen. A kapcsolat időkorlátja a `ON` JOIN záradékában van megadva a `DATEDIFF` függvény használatával. Ebben az esetben az illesztés a hívási adatmennyiség 5 másodperces intervallumán alapul.

1. Illessze be a következő lekérdezést a lekérdezés-szerkesztőbe:

    ```SQL
    SELECT System.Timestamp AS WindowEnd, COUNT(*) AS FraudulentCalls
    INTO "MyPBIoutput"
    FROM "CallStream" CS1 TIMESTAMP BY CallRecTime
    JOIN "CallStream" CS2 TIMESTAMP BY CallRecTime
    ON CS1.CallingIMSI = CS2.CallingIMSI
    AND DATEDIFF(ss, CS1, CS2) BETWEEN 1 AND 5
    WHERE CS1.SwitchNum != CS2.SwitchNum
    GROUP BY TumblingWindow(Duration(second, 1))
    ```

    Ez a lekérdezés bármilyen SQL-csatlakoztatáshoz hasonló, kivéve a `DATEDIFF` JOIN függvényt. A jelen verziója a `DATEDIFF` streaming Analytics szolgáltatásra vonatkozik, és meg kell jelennie a `ON...BETWEEN` záradékban. A paraméterek egy időegység (a példában szereplő másodpercek) és az illesztés két forrásainak aliasai. Ez eltér a szabványos SQL- `DATEDIFF` függvénytől.

    A `WHERE` záradék tartalmazza azt a feltételt, amely a hamis hívást megjelöli: a kezdeményező kapcsolók nem egyeznek.

2. Válassza a **teszt lekérdezés** lehetőséget. Tekintse át a kimenetet, majd válassza a **lekérdezés mentése** lehetőséget.

## <a name="start-the-job-and-visualize-output"></a>A feladat elindítása és a kimenet vizualizációja

1. A feladatok elindításához navigáljon a feladatok **áttekintéséhez** , és válassza az **Indítás** lehetőséget.

2. Válassza a **Most** beállítást a feladatkimenet kezdési idejeként, majd válassza az **Indítás** lehetőséget. A feladat állapotát az értesítési sávban tekintheti meg.

3. Miután a feladat sikeresen végre lett hajtva, lépjen a [Power BI](https://powerbi.com/)-ba, és lépjen be munkahelyi vagy iskolai fiókjával. Ha a Stream Analytics-feladatlekérdezés eredményeket ad kimenetként, a korábban létrehozott *ASAdataset* adathalmaz az **Adatkészletek** lapon található.

4. A Power BI-munkaterületen válassza a **+ Létrehozás** lehetőséget egy új, *Fraudulent Calls* nevű irányítópult létrehozásához.

5. Az ablak tetején válassza a **Szerkesztés** lehetőséget, és **adja hozzá a csempét**. Ezután válassza az **Egyedi folyamatos átviteli adatok**, majd a **Tovább** lehetőséget. A **Saját adatkészletek** területen válassza az **ASAdataset** elemet. Válassza a **kártya** lehetőséget a **vizualizáció típusa** legördülő listából, és adjon meg **hamis hívásokat** a **mezőkhöz**. Kattintson a **Tovább** gombra, és nevezze el a csempét, majd kattintson az **Alkalmaz** elemre a csempe létrehozásához.

   ![Irányítópult-csempék létrehozása Power BI](media/stream-analytics-real-time-fraud-detection/create-power-bi-dashboard-tiles.png)

6. Hajtsa végre újra az 5. lépést az alábbi beállításokkal:
   * Amikor a Vizualizáció típusa részhez ér, válassza a Vonaldiagram lehetőséget.
   * Adjon hozzá egy tengelyt, és válassza ki **windowend** lehetőséget.
   * Adjon meg egy értéket, és válassza a **fraudulentcalls** lehetőséget.
   * A **Megjelenítendő időtartomány** beállításnál válassza ki az utolsó 10 percet.

7. A két csempe hozzáadása után az irányítópult az alábbi példában látható módon fog kinézni. Figyelje meg, hogy ha az Event hub Sender Application és a streaming Analytics alkalmazás fut, akkor az Power BI-irányítópult rendszeresen frissül, ahogy új adatként érkeznek.

   ![Eredmények megtekintése Power BI irányítópulton](media/stream-analytics-real-time-fraud-detection/power-bi-results-dashboard.png)

## <a name="embedding-your-power-bi-dashboard-in-a-web-application"></a>A Power BI-irányítópult beágyazása egy webalkalmazásba

Az oktatóanyag ezen részében egy minta [ASP.net](https://asp.net/) webalkalmazást fog használni, amelyet a Power bi csapata hozott létre az irányítópult beágyazásához. További információ az irányítópult beágyazásáról: [Beágyazás a Power BI-ba](/power-bi/developer/embedding).

Az alkalmazás beállításához nyissa meg a [PowerBI-Developer-Samples](https://github.com/Microsoft/PowerBI-Developer-Samples) GitHub-adattárat, és kövesse a **felhasználó tulajdonában lévő adat** szakasz utasításait (használja a Integration **-Web-App** alszakaszban található átirányítás és Kezdőlap URL-címeket). Mivel az irányítópult példáját használjuk, használja a [GitHub-tárházban](https://github.com/microsoft/PowerBI-Developer-Samples/tree/master/.NET%20Framework/Embed%20for%20your%20organization/)található integrat **-Web-App mintakód-** kódot.
Miután az alkalmazás fut a böngészőjében, az alábbi lépések végrehajtásával ágyazza be a weblapra a korábban létrehozott irányítópultot:

1. Válassza a **bejelentkezés Power bi** lehetőséget, amely hozzáférést biztosít az alkalmazásnak az Power bi-fiókban lévő irányítópultokhoz.

2. Kattintson az **Irányítópultok lekérése** gombra, amely megjeleníti a fiókjában található irányítópultokat egy táblában. Keresse meg a korábban létrehozott irányítópult nevét (**powerbi-embedded-dashboard**), és másolja be a megfelelő **EmbedUrl** értéket.

3. Végül illessze be az **EmbedUrl** értéket a megfelelő szövegmezőbe, és válassza az **Irányítópult beágyazása** lehetőséget. Az irányítópult most már megtekinthető egy webalkalmazásba beágyazva.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy Stream Analytics-feladatot, elemezte a bejövő adatokat, és megjelenítette az eredményeket egy Power BI-irányítópulton. A Stream Analytics-feladatokról a következő oktatóanyagban talál további információt:

> [!div class="nextstepaction"]
> [Azure Functions-függvények futtatása Stream Analytics-feladatokban](stream-analytics-with-azure-functions.md)
