---
title: A valós idejű adatelemzések megjelenítéséhez használja a Azure Cosmos DB módosítási csatornát
description: Ez a cikk azt ismerteti, hogyan használható a hírcsatornák a kiskereskedelmi vállalatok számára a felhasználói minták megértéséhez, valós idejű adatelemzések és vizualizációk elvégzéséhez
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: how-to
ms.date: 05/28/2019
ms.author: sngun
ms.custom: devx-track-java
ms.openlocfilehash: e7b75c71d64054e38630677ecd38f8e3e2483c12
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97606334"
---
# <a name="use-azure-cosmos-db-change-feed-to-visualize-real-time-data-analytics"></a>A valós idejű adatelemzések megjelenítéséhez használja a Azure Cosmos DB módosítási csatornát
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

A Azure Cosmos DB változási hírcsatorna egy olyan mechanizmus, amely egy Azure Cosmos-tárolóból származó rekordok folyamatos és növekményes adatcsatornájának beolvasását végzi el a rekordok létrehozásakor vagy módosításakor. A hírcsatornák támogatásának módosításával megfigyelheti a tárolót a változásokhoz. Ezután a módosításuk sorrendjében felsorolja a módosított dokumentumokat. Ha többet szeretne megtudni a hírcsatornák változásáról, tekintse meg a következő témakört: [a Change Feeding cikk használata](change-feed.md) 

Ez a cikk azt ismerteti, hogyan használhatja az e-kereskedelmi vállalat a váltást a felhasználói minták megértéséhez, valós idejű adatelemzést és vizualizációt hajthat végre. Elemezni fogja az eseményeket, például az elemeket megtekintő felhasználót, egy elem hozzáadását a kosárhoz, vagy egy elem megvásárlását. Amikor az egyik esemény bekövetkezik, új rekord jön létre, és a változási hírcsatorna rögzíti a rekordot. A módosítási hírcsatorna ezt követően számos lépést indít el, ami a vállalat teljesítményének és tevékenységének elemzését szolgáló mérőszámok vizualizációját eredményezi. A megjeleníthető mérőszámok közé tartozhatnak a bevétel, az egyedi webhely látogatói, a legnépszerűbb elemek és a megtekintett, a kosárhoz és a vásárláshoz hozzáadott elemek átlagos ára. Ezek a minta mérőszámok segítséget nyújthatnak az e-kereskedelmi vállalatoknak a webhely népszerűségének kiértékelésében, a hirdetési és díjszabási stratégiák kidolgozásában, valamint a befektetett leltározással kapcsolatos döntések meghozatalában.

Az első lépések megkezdése előtt tekintse meg a megoldásról szóló videót:

> [!VIDEO https://www.youtube.com/embed/AYOiMkvxlzo]
>

## <a name="solution-components"></a>Megoldás-összetevők
A következő ábra a megoldásban részt vevő adatfolyamot és összetevőket mutatja be:

:::image type="content" source="./media/changefeed-ecommerce-solution/project-visual.png" alt-text="Projekt vizualizációja" border="false":::
 
1. **Adatgenerálás:** A adatszimulátor használatával olyan kiskereskedelmi adatforgalom hozhatók forgalomba, amelyek olyan eseményeket jelentenek, mint például egy felhasználó, egy elem hozzáadása a kosárhoz, és egy elem vásárlása. Az adatgenerátor használatával nagy mennyiségű mintaadatok hozhatók létre. A generált mintaadatok a következő formátumban tartalmazzák a dokumentumokat:
   
    ```json
    {
      "CartID": 2486,
      "Action": "Viewed",
      "Item": "Women's Denim Jacket",
      "Price": 31.99
    }
    ```

2. **Cosmos db:** A generált adattároló egy Azure Cosmos-tárolóban tárolódik.  

3. **Csatorna módosítása:** A változási hírcsatorna figyeli az Azure Cosmos-tároló módosításait. Minden alkalommal, amikor új dokumentumot adnak hozzá a gyűjteményhez (azaz ha egy esemény egy adott elem megtekintését, egy elem a kosárba való felvételét vagy egy elem megvásárlását), akkor a változási hírcsatorna egy [Azure-függvényt](../azure-functions/functions-overview.md)indít el.  

4. **Azure-függvény:** Az Azure-függvény feldolgozza az új adatokat, és elküldi azt egy [Azure Event hub](../event-hubs/event-hubs-about.md)-nak.  

5. **Event hub:** Az Azure Event hub tárolja ezeket az eseményeket, és elküldi azokat [Azure stream Analyticsba](../stream-analytics/stream-analytics-introduction.md) további elemzések elvégzéséhez.  

6. **Azure stream Analytics:** Azure Stream Analytics az események feldolgozására és valós idejű adatelemzésre szolgáló lekérdezéseket definiál. Ezt követően a rendszer elküldi ezeket az adatfájlokat a [Microsoft Power BInak](/power-bi/desktop-what-is-desktop).  

7. **Power bi:** A Power BI a Azure Stream Analytics által elküldett adatmegjelenítéshez használatos. Létrehozhat egy irányítópultot, amelyből megtudhatja, hogyan változnak a metrikák valós időben.  

## <a name="prerequisites"></a>Előfeltételek

* Microsoft .NET Framework 4.7.1 vagy újabb

* Microsoft .NET Core 2,1 (vagy újabb)

* Visual Studio Univerzális Windows-platform-fejlesztéssel, .NET Desktop-fejlesztéssel, valamint ASP.NET és webes fejlesztési számítási feladatokkal

* Előfizetés Microsoft Azure

* Microsoft Power BI fiók

* Töltse le a [Azure Cosmos db Change feed Lab](https://github.com/Azure-Samples/azure-cosmos-db-change-feed-dotnet-retail-sample) -t a githubról. 

## <a name="create-azure-resources"></a>Azure-erőforrások létrehozása 

Hozza létre az Azure-erőforrásokat – Azure Cosmos DB, a Storage-fiókot, az Event hub-t, Stream Analytics a megoldáshoz szükséges. Ezeket az erőforrásokat egy Azure Resource Manager sablonon keresztül fogja telepíteni. Az alábbi lépéseket követve telepítheti ezeket az erőforrásokat: 

1. Állítsa be a Windows PowerShell végrehajtási házirendjét a **korlátozás** nélküli értékre. Ehhez nyissa meg a **Windows PowerShellt rendszergazdaként** , és futtassa a következő parancsokat:

   ```powershell
   Get-ExecutionPolicy
   Set-ExecutionPolicy Unrestricted 
   ```

2. Az előző lépésben letöltött GitHub-tárházból lépjen a **Azure Resource Manager** mappára, és nyissa meg a fájl **parameters.js** nevű fájlt.  

3. Adja meg a cosmosdbaccount_name, eventhubnamespace_name, storageaccount_name és paraméterek értékét a fájl **parameters.js** . Az egyes erőforrásokhoz később megadott neveket kell használnia.  

4. A **Windows PowerShellben** navigáljon a **Azure Resource Manager** mappára, és futtassa a következő parancsot:

   ```powershell
   .\deploy.ps1
   ```
5. Ha a rendszer kéri, adja meg az Azure- **előfizetése azonosítóját**, a **changefeedlab** az erőforráscsoport nevét, és **run1** a telepítési név mezőben. Ha az erőforrások üzembe helyezése megkezdődött, akár 10 percet is igénybe vehet.

## <a name="create-a-database-and-the-collection"></a>Adatbázis és gyűjtemény létrehozása

Most hozzon létre egy gyűjteményt az e-kereskedelmi hely eseményeinek tárolására. Amikor egy felhasználó megtekint egy tételt, hozzáad egy tételt a kosárhoz, vagy megvásárol egy tételt, a gyűjtemény egy olyan rekordot fog kapni, amely tartalmazza a műveletet ("megtekintésre", "hozzáadva" vagy "megvásárolt"), az érintett elem nevét, az érintett elem árát, valamint az érintett felhasználói kosár AZONOSÍTÓjának számát.

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és keresse meg a sablon központi telepítése által létrehozott **Azure Cosmos db fiókot** .  

2. A **adatkezelő** ablaktáblán válassza az **új gyűjtemény** lehetőséget, és töltse ki az űrlapot a következő részletekkel:  

   * Az **adatbázis-azonosító** mezőben válassza az **új létrehozása** lehetőséget, majd adja meg a **changefeedlabdatabase**. Ne jelölje be a **kiépítési adatbázis átviteli sebessége** jelölőnégyzetet.  
   * A **gyűjtemény** azonosító mezőjébe írja be a következőt: **changefeedlabcollection**.  
   * A **Partition Key (partíció kulcsa** ) mezőbe írja be a következőt: **/cikkengedm**. Ez megkülönbözteti a kis-és nagybetűket, ezért ügyeljen arra, hogy megfelelően adja meg.  
   * Az **átviteli sebesség** mezőben adja meg a **10000** értéket.  
   * Kattintson az **OK** gombra.  

3. Ezután hozzon létre egy másik, **bérletek** nevű gyűjteményt a módosítási hírcsatorna feldolgozásához. A bérletek gyűjteménye összehangolja a változási csatornát több feldolgozón keresztül. A bérletek egy különálló bérlettel való tárolásához külön gyűjteményt kell használni.  

4. Térjen vissza a **adatkezelő** panelre, és válassza az **új gyűjtemény** lehetőséget, és töltse ki az űrlapot a következő részletekkel:

   * Az **adatbázis-azonosító** mezőben válassza a **meglévő használata** lehetőséget, majd adja meg a **changefeedlabdatabase**.  
   * A **Gyűjtemény azonosítója** mezőben adja meg a **bérletek** értéket.  
   * A **tárolási kapacitás** beállításnál válassza a **rögzített** lehetőséget.  
   * Hagyja meg az **átviteli sebesség** mezőt az alapértelmezett értékre.  
   * Kattintson az **OK** gombra.

## <a name="get-the-connection-string-and-keys"></a>A kapcsolatok karakterláncának és kulcsainak beolvasása

### <a name="get-the-azure-cosmos-db-connection-string"></a>A Azure Cosmos DBi kapcsolatok karakterláncának beolvasása

1. Nyissa meg a [Azure Portalt](https://portal.azure.com/) , és keresse meg a sablon központi telepítése által létrehozott **Azure Cosmos db fiókot** .  

2. Navigáljon a **kulcsok** panelre, másolja az elsődleges KAPCSOLATi karakterláncot, és másolja azt egy Jegyzettömbbe vagy egy másik dokumentumba, amely a laborban elérhető lesz. Címkézheti Cosmos DB a **kapcsolatok karakterláncát**. Később át kell másolnia a karakterláncot a kódra, ezért jegyezze fel, hogy hol tárolja.

### <a name="get-the-storage-account-key-and-connection-string"></a>A Storage-fiók kulcsának és a kapcsolatok karakterláncának beolvasása

Az Azure Storage-fiókok lehetővé teszik a felhasználók számára az adattárolást. Ebben a laborban egy Storage-fiókot fog használni az Azure-függvény által használt adat tárolására. Az Azure-függvény akkor aktiválódik, ha a gyűjtemény módosítása történik.

1. Térjen vissza az erőforráscsoporthoz, és nyissa meg a korábban létrehozott Storage-fiókot  

2. Válassza a **hozzáférési kulcsok** lehetőséget a bal oldali menüben.  

3. Másolja az 1. **kulcs** alatti értékeket egy Jegyzettömbre vagy egy olyan dokumentumra, amely a laborban lesz elérhető. A **kulcsot** **tárolási kulcsként** és a **kapcsolatok karakterláncának** kell megcímkézni **tárolási kapcsolatok karakterláncként**. Ezeket a sztringeket később át kell másolnia a kódra, ezért jegyezze fel, és jegyezze meg, hogy hol tárolja őket.  

### <a name="get-the-event-hub-namespace-connection-string"></a>Az Event hub névtér-kapcsolati karakterláncának beolvasása

Az Azure Event hub megkapja az adatesemények, a tárolók, a folyamatok és az adattovábbítást. Ebben a laborban az Azure Event hub minden alkalommal kap egy dokumentumot, amikor új esemény következik be (azaz egy felhasználó által megtekintett, egy felhasználó kosárba felvett vagy egy felhasználó által megvásárolt elem), majd továbbítja a dokumentumot Azure Stream Analytics.

1. Térjen vissza az erőforráscsoporthoz, és nyissa meg a prelab létrehozott és elnevezett **Event hub-névteret** .  

2. A bal oldali menüben válassza a **megosztott hozzáférési szabályzatok** lehetőséget.  

3. Válassza a **RootManageSharedAccessKey** lehetőséget. Másolja a **kapcsolati sztring elsődleges kulcsát** egy Jegyzettömbre vagy egy másik dokumentumra, amelyhez a laborban hozzá fog férni. Meg kell címkéznie az **Event hub névtér** kapcsolati karakterláncát. Később át kell másolnia a karakterláncot a kódra, ezért jegyezze fel, hogy hol tárolja.

## <a name="set-up-azure-function-to-read-the-change-feed"></a>Az Azure-függvény beállítása a változási csatorna olvasásához

Amikor új dokumentumot hoznak létre, vagy egy aktuális dokumentumot módosítanak egy Cosmos-tárolóban, a módosítási hírcsatorna automatikusan hozzáadja a módosított dokumentumot a gyűjtemény változásainak előzményeihez. Most létrehoz és futtat egy olyan Azure-függvényt, amely feldolgozza a változási csatornát. Ha a létrehozott gyűjteményben létrehoznak vagy módosítanak egy dokumentumot, az Azure-függvényt a változási csatorna indítja el. Ezután az Azure-függvény elküldi a módosított dokumentumot az Event hub-nak.

1. Térjen vissza az eszközön klónozott adattárhoz.  

2. Kattintson a jobb gombbal a **ChangeFeedLabSolution. SLN** nevű fájlra, majd válassza a **Megnyitás a Visual Studióval** lehetőséget.  

3. Navigáljon **local.settings.jsa** Visual Studióban. Ezután használja a korábban feljegyzett értékeket az üres értékek kitöltéséhez.  

4. Navigáljon a **ChangeFeedProcessor. cs**. A **Run** függvény paraméterei a következő műveleteket hajtják végre:  

   * Cserélje le a **gyűjtemény neve** szöveget a gyűjtemény nevével. Ha követte a korábbi utasításokat, a gyűjtemény neve changefeedlabcollection.  
   * Cserélje le a **bérletek gyűjteménye nevét** a bérletek gyűjteményének nevére. Ha követte a korábbi utasításokat, a bérletek gyűjteményének neve **bérletek**.  
   * Győződjön meg arról, hogy a Visual Studio tetején a zöld nyíl bal oldalán található indítási projekt mező a következőt mondja: **ChangeFeedFunction**.  
   * A program futtatásához kattintson a **Start**  gombra az oldal tetején.  
   * Ellenőrizheti, hogy a függvény fut-e, amikor a konzol alkalmazás a "feladat-gazdagép elindult".

## <a name="insert-data-into-azure-cosmos-db"></a>Adatbeszúrás Azure Cosmos DBba 

Ha szeretné megtudni, hogyan dolgozza fel a változás a hírcsatornában az új műveleteket egy e-kereskedelmi helyen, szimulálnia kell azokat az adatokat, amelyek a termékkatalógusban lévő elemeket jelenítik meg, és ezeket az elemeket felveszik a kosárba, és megvásárolják az elemeket a kosárban. Ezek az adatmennyiségek nem megfelelőek, és az e-kereskedelmi webhelyeken tárolt adatmennyiség replikálásának céljából.

1. Térjen vissza az adattárhoz a Fájlkezelőben, majd kattintson a jobb gombbal a **ChangeFeedFunction. SLN** elemre, és nyissa meg újra egy új Visual Studio-ablakban.  

2. Navigáljon a **App.config** fájlhoz. A `<appSettings>` blokkon belül adja hozzá a korábban lekért Azure Cosmos db-fiókhoz tartozó végpontot és egyedi **elsődleges kulcsot** .  

3. Adja hozzá a **gyűjtemény** és az **adatbázis** nevét. (Ezek a nevek csak akkor **changefeedlabcollection** és **changefeedlabdatabase** , ha úgy dönt, hogy másképpen nevezi el.)

   :::image type="content" source="./media/changefeed-ecommerce-solution/update-connection-string.png" alt-text="A kapcsolatok karakterláncának frissítése":::
 
4. Mentse a módosításokat az összes szerkesztett fájlon.  

5. Győződjön meg arról, hogy a Visual Studio tetején a zöld nyíl bal oldalán található **indítási projekt** mező a következőt mondja: **DataGenerator**. Ezután válassza a **Start** lehetőséget az oldal tetején a program futtatásához.  
 
6. Várjon, amíg a program futni próbál. A csillagok azt jelentik, hogy az adatforgalom bekerül! A program futásának fenntartása – fontos, hogy a rendszer sok adatgyűjtést gyűjtsön.  

7. Ha [Azure Portal](https://portal.azure.com/) , majd az erőforráscsoport Cosmos db fiókjához navigál, majd **adatkezelő**, akkor a rendszer a **changefeedlabcollection** importált véletlenszerű adatmennyiséget fogja látni.
 
   :::image type="content" source="./media/changefeed-ecommerce-solution/data-generated-in-portal.png" alt-text="A portálon létrehozott adatértékek":::

## <a name="set-up-a-stream-analytics-job"></a>Stream Analytics-feladatok beállítása

A Azure Stream Analytics egy teljes körűen felügyelt felhőalapú szolgáltatás, amely valós idejű feldolgozást biztosít a folyamatos átviteli sebességhez. Ebben a laborban a stream Analytics használatával dolgozza fel az Event hub új eseményeit (például egy elem megtekintett, kosárba való felvételével vagy megvásárlásával), ezeket az eseményeket valós idejű adatelemzésbe helyezheti el, majd elküldheti őket a vizualizációk Power BI.

1. A [Azure Portal](https://portal.azure.com/)navigáljon az erőforráscsoporthoz, majd a **streamjob1** (a prelab létrehozott stream Analytics-feladatokhoz).  

2. Válassza ki az alább látható **bemeneteket** .  

   :::image type="content" source="./media/changefeed-ecommerce-solution/create-input.png" alt-text="Bemenet létrehozása":::

3. Válassza a **+ stream-bemenet hozzáadása** elemet. Ezután válassza ki az **Event hub** elemet a legördülő menüből.  

4. Töltse ki az új beviteli űrlapot a következő részletekkel:

   * A **bemeneti** Alias mezőben adja meg a **bevitel** értéket.  
   * Válassza az **Event hub kiválasztása az előfizetések közül** lehetőséget.  
   * Állítsa az **előfizetés** mezőt az előfizetésre.  
   * Az **Event hub-névtér** mezőben adja meg az prelab során létrehozott Event hub-névtér nevét.  
   * Az **Event hub neve** mezőben válassza a **meglévő használata** lehetőséget, majd a legördülő menüben válassza az **Event-hub1** elemet.  
   * Az **Event hub-házirend** neve mező értékét állítsa az alapértelmezett értékre.  
   * Az **esemény szerializálási formátumának** meghagyása **JSON**-ként.  
   * Hagyja meg a **kódolás mezőt** **UTF-8** értékre.  
   * Az **esemény tömörítési típus** mezőjét állítsa **nincs** értékre.  
   * Válassza ki a **Mentés** gombot.

5. Váltson vissza a stream Analytics-feladatokhoz lapra, és válassza a **kimenetek** lehetőséget.  

6. Válassza a **+ Hozzáadás** lehetőséget. Ezután válassza a **Power bi** lehetőséget a legördülő menüből.  

7. Ha új Power BI kimenetet szeretne létrehozni az átlagos ár megjelenítéséhez, hajtsa végre a következő műveleteket:

   * A **kimeneti alias** mezőben adja meg a **averagePriceOutput**.  
   * Hagyja meg a **csoport munkaterület** mezőt úgy, hogy **engedélyezze a kapcsolódást a munkaterületek betöltéséhez**.  
   * Az **adatkészlet neve** mezőbe írja be a **averagePrice** nevet.  
   * A **tábla neve** mezőbe írja be a **averagePrice** nevet.  
   * Válassza az **Engedélyezés** gombot, majd kövesse az utasításokat a Power BIhoz való kapcsolódás engedélyezéséhez.  
   * Válassza ki a **Mentés** gombot.  

8. Ezután lépjen vissza a **streamjob1** , és válassza a **lekérdezés szerkesztése** lehetőséget.

   :::image type="content" source="./media/changefeed-ecommerce-solution/edit-query.png" alt-text="Lekérdezés szerkesztése":::
 
9. Illessze be a következő lekérdezést a lekérdezési ablakba. Az **átlagos ár** lekérdezés kiszámítja a felhasználók által megtekintett elemek átlagos árát, a felhasználók kosarahoz hozzáadott összes elem átlagos árát, valamint a felhasználók által megvásárolt összes elem átlagos árát. Ez a mérőszám segítséget nyújt az e-kereskedelmi vállalatoknak, hogy eldöntsék, milyen árakat kell értékesíteni a-ben, és hogy milyen leltárt kell a Ha például a megtekintett elemek átlagos díja jóval meghaladja a megvásárolt elemek átlagát, akkor a vállalat dönthet úgy, hogy olcsóbb elemeket ad hozzá a leltárhoz.

   ```sql
   /*AVERAGE PRICE*/      
   SELECT System.TimeStamp AS Time, Action, AVG(Price)  
    INTO averagePriceOutput  
    FROM input  
    GROUP BY Action, TumblingWindow(second,5) 
   ```
10. Ezután válassza a **Mentés** lehetőséget a bal felső sarokban.  

11. Most térjen vissza a **streamjob1** , és kattintson a lap tetején található **Start** gombra. A Azure Stream Analytics eltarthat néhány percig, de végül látni fogja, hogy a "Start" értékről a "Running" (indítás) lehetőségre változik.

## <a name="connect-to-power-bi"></a>Csatlakozás a Power BI-hoz

A Power BI egy üzleti elemzési eszközcsomag, mellyel adatokat elemezhet és megoszthatja a levont következtetéseket. Nagyszerű példa arra, hogy miként lehet stratégiailag megjeleníteni az elemzett adatmennyiséget.

1. Jelentkezzen be Power BI és navigáljon a **saját munkaterületre** a lap bal oldalán található menü megnyitásával.  

2. Válassza a **+ Létrehozás** lehetőséget a jobb felső sarokban, majd válassza az **irányítópult** lehetőséget az irányítópult létrehozásához.  

3. Kattintson a jobb felső sarokban található **+ csempe hozzáadása** lehetőségre.  

4. Válassza az **Egyéni adatfolyam-továbbítás** lehetőséget, majd kattintson a **tovább** gombra.  
 
5. Válassza ki a **averagePrice** az **adatkészletek** közül, majd kattintson a **tovább** gombra.  

6. A **vizualizáció típusa** mezőben válassza a **fürtözött sávdiagram** lehetőséget a legördülő menüből. A **tengely** területen adja hozzá a műveletet. A **Jelmagyarázat** kihagyása anélkül, hogy bármit adna hozzá. Ezután a következő, érték nevű szakaszban adja hozzá az **AVG** **értéket**. Válassza a **Next (tovább**), majd a diagram címet, és kattintson az **alkalmaz** gombra. Új diagramot kell látnia az irányítópulton.  

7. Most, ha további mérőszámokat szeretne megjeleníteni, térjen vissza a **streamjob1** , és hozzon létre három további kimenetet a következő mezőkkel.

   a. **Kimeneti alias:** IncomingRevenueOutput, adatkészlet neve: IncomingRevenue, tábla neve: incomingRevenue  
   b. **Kimeneti alias:** Top5Output, adatkészlet neve: Top5, tábla neve: Top5  
   c. **Kimeneti alias:** UniqueVisitorCountOutput, adatkészlet neve: UniqueVisitorCount, tábla neve: uniqueVisitorCount

   Ezután válassza a **lekérdezés szerkesztése** lehetőséget, és illessze **be a következő lekérdezéseket** az Ön által már írt módon.

   ```sql
    /*TOP 5*/
    WITH Counter AS
    (
    SELECT Item, Price, Action, COUNT(*) AS countEvents
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY Item, Price, Action, TumblingWindow(second,30)
    ), 
    top5 AS
    (
    SELECT DISTINCT
    CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
    FROM Counter
    GROUP BY TumblingWindow(second,30)
    ), 
    arrayselect AS 
    (
    SELECT arrayElement.ArrayValue
    FROM top5
    CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
    ) 
    SELECT arrayvalue.value.item, arrayvalue.value.price,   arrayvalue.value.countEvents
    INTO top5Output
    FROM arrayselect

    /*REVENUE*/
    SELECT System.TimeStamp AS Time, SUM(Price)
    INTO incomingRevenueOutput
    FROM input
    WHERE Action = 'Purchased'
    GROUP BY TumblingWindow(hour, 1)

    /*UNIQUE VISITORS*/
    SELECT System.TimeStamp AS Time, COUNT(DISTINCT CartID) as uniqueVisitors
    INTO uniqueVisitorCountOutput
    FROM input
    GROUP BY TumblingWindow(second, 5)
   ```
   
   Az első 5 lekérdezés kiszámítja az első 5 elemet, amelyet a megvásároltak száma szerint rangsorol. Ez a mérőszám segíthet az e-kereskedelmi cégeknek kiértékelni, hogy mely elemek a legnépszerűbbek, és befolyásolhatják a vállalat hirdetési, díjszabási és leltározási döntéseit.

   A BEVÉTELi lekérdezés kiszámítja a bevételt, és összesíti az egyes percenként vásárolt elemek árát. Ez a mérőszám segítséget nyújt az e-kereskedelmi vállalatok számára a pénzügyi teljesítmény kiértékelésében, valamint arról is, hogy milyen napszakok járulnak hozzá a legtöbb bevételhez. Ez hatással lehet a vállalati stratégia általános stratégiájára, különösen a marketingre.

   Az egyedülálló látogatói lekérdezés kiszámítja, hogy az egyedi bevásárlókocsi-AZONOSÍTÓk észlelésével hány egyedi látogató van a webhelyen 5 másodpercenként. Ez a mérőszám segítséget nyújt az e-kereskedelmi vállalatok számára a strategize és a további ügyfelek beszerzéséhez.

8. Most már hozzáadhat csempéket is ezekhez az adatkészletekhez.

   * A Top 5 esetében érdemes lehet egy fürtözött oszlopdiagram megadására, amelyben az elemek tengelyként és a darabszám értékként jelennek meg.  
   * Bevétel esetén érdemes lehet a diagramot a tengely és az érték összegével együtt elvégezni. A megjelenítendő időtartománynak a lehető legnagyobb lehetségesnek kell lennie ahhoz, hogy a lehető legtöbb információt lehessen szolgáltatni.  
   * Az egyedi látogatók számára érdemes lehet egy kártya vizualizációt készíteni, amelyben az egyedi látogatók száma érték.

   A minta irányítópult a következő diagramokat keresi:

   :::image type="content" source="./media/changefeed-ecommerce-solution/visualizations.png" alt-text="A képernyőfelvétel egy minta-irányítópultot mutat be, amely a megvásárolt elemek átlagos árát tartalmazza művelet, egyedi látogató, bevétel és 5 legfontosabb elem alapján.":::

## <a name="optional-visualize-with-an-e-commerce-site"></a>Nem kötelező: megjelenítés E-kereskedelmi webhellyel

Most bemutatjuk, hogyan használható az új adatelemzési eszköz egy valós e-kereskedelmi hellyel való kapcsolódáshoz. Az e-kereskedelmi webhely létrehozásához használjon egy Azure Cosmos-adatbázist a termékkategóriák (nők, férfiak, Unisex), a termékkatalógus és a legnépszerűbb elemek listájának tárolásához.

1. Térjen vissza a [Azure Portalra](https://portal.azure.com/), majd a **Cosmos db-fiókjába**, majd **adatkezelő**.  

   Adjon hozzá két gyűjteményt a **changefeedlabdatabase**  -  -**termékek** és- **Kategóriák** területen rögzített tárolókapacitással.

   Adjon hozzá egy másik gyűjteményt a **topItems** és a **/cikkengedm** nevű **changefeedlabdatabase** a partíció kulcsaként.

2. Válassza ki a **topItems** gyűjteményt, és a **skála és beállítások** területen állítsa be az **élettartamot** **30 másodpercre** , hogy a topItems 30 másodpercenként frissítsen.

   :::image type="content" source="./media/changefeed-ecommerce-solution/time-to-live.png" alt-text="Élettartam":::

3. Ahhoz, hogy a **topItems** -gyűjteményt a leggyakrabban megvásárolt elemekkel töltse fel, térjen vissza a **streamjob1** , és adjon hozzá egy új **kimenetet**. Válassza a **Cosmos db** lehetőséget.

4. Töltse ki a kötelező mezőket az alábbi képen látható módon.

   :::image type="content" source="./media/changefeed-ecommerce-solution/cosmos-output.png" alt-text="Cosmos-kimenet":::
 
5. Ha a labor előző részében a nem kötelező első 5 lekérdezést adta hozzá, folytassa a következő résszel: 5a. Ha nem, folytassa az 5b. résszel.

   5a. A **streamjob1** területen válassza a **lekérdezés szerkesztése** lehetőséget, és illessze be a következő lekérdezést a Azure stream Analytics lekérdezés-szerkesztőbe az első 5 lekérdezés alatt, de a többi lekérdezés fölé.

   ```sql
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```
   5b. A **streamjob1** területen válassza a **lekérdezés szerkesztése** lehetőséget, és illessze be a következő lekérdezést a Azure stream Analytics lekérdezési szerkesztőjébe az összes többi lekérdezés fölé.

   ```sql
   /*TOP 5*/
   WITH Counter AS
   (
   SELECT Item, Price, Action, COUNT(*) AS countEvents
   FROM input
   WHERE Action = 'Purchased'
   GROUP BY Item, Price, Action, TumblingWindow(second,30)
   ), 
   top5 AS
   (
   SELECT DISTINCT
   CollectTop(5)  OVER (ORDER BY countEvents) AS topEvent
   FROM Counter
   GROUP BY TumblingWindow(second,30)
   ), 
   arrayselect AS 
   (
   SELECT arrayElement.ArrayValue
   FROM top5
   CROSS APPLY GetArrayElements(top5.topevent) AS arrayElement
   ) 
   SELECT arrayvalue.value.item AS Item, arrayvalue.value.price, arrayvalue.value.countEvents
   INTO topItems
   FROM arrayselect
   ```

6. Nyissa meg a **EcommerceWebApp. SLN** fájlt, és navigáljon a **megoldáskezelő** **Web.config** fájljához.  

7. A `<appSettings>` blokkon belül adja hozzá az **URI** -t és az **elsődleges kulcsot** , amelyet korábban mentett, ahol az **URI itt** és **az elsődleges kulcs itt** látható. Ezután adja hozzá az **adatbázis nevét** és a **gyűjtemény nevét** a jelzett módon. (Ezek a nevek csak akkor **changefeedlabdatabase** és **changefeedlabcollection** , ha úgy dönt, hogy másképpen nevezi el.)

   Adja meg a **termékek gyűjteményének nevét**, a **Kategóriák gyűjteményének nevét** és a **legfontosabb elemek gyűjteményének nevét** a jelzett módon. (Ezeknek a névnek **termékeknek, kategóriáknak és topItems** kell lennie, kivéve, ha úgy döntött, hogy másképpen nevezi el.)  

8. Navigáljon a **EcommerceWebApp. SLN** **mappában található pénztár mappához** , és nyissa meg a következőt:. Ezután nyissa meg a **Web.config** fájlt a mappán belül.  

9. A `<appSettings>` blokkon belül adja hozzá azt az **URI** -t és **elsődleges kulcsot** , amelyet a jelzettnél korábban mentett. Ezután adja hozzá az **adatbázis nevét** és a **gyűjtemény nevét** a jelzett módon. (Ezek a nevek csak akkor **changefeedlabdatabase** és **changefeedlabcollection** , ha úgy dönt, hogy másképpen nevezi el.)  

10. Kattintson a **Start** gombra a lap tetején a program futtatásához.  

11. Mostantól az e-kereskedelmi webhelyen is játszhat. Amikor megtekint egy tételt, hozzáad egy tételt a kosárhoz, megváltoztatja egy elem mennyiségét a kosárban, vagy megvásárol egy tételt, ezek az események átkerülnek a Cosmos DB változási csatornán az Event hub, az ASA, majd a Power BI. Javasoljuk, hogy a DataGenerator futtatásával jelentős webes forgalmi adatokat hozzon létre, és reális készletet biztosítson a "forró termékek" számára az e-kereskedelmi webhelyen.

## <a name="delete-the-resources"></a>Erőforrások törlése

A laborban létrehozott erőforrások törléséhez keresse meg [Azure Portal](https://portal.azure.com/)az erőforráscsoportot, majd válassza az **erőforráscsoport törlése** elemet az oldal tetején található menüből, és kövesse a megadott utasításokat.

## <a name="next-steps"></a>Következő lépések 
  
* Ha többet szeretne megtudni a hírcsatornák változásáról, tekintse meg a [Azure Cosmos db](change-feed.md)