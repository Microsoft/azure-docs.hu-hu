---
title: Elemzési lekérdezések futtatása
description: Több-bérlős alkalmazások több Azure SQL Database adatbázisaiból kinyert adatok használatával több-bérlős elemzési lekérdezések.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 09/19/2018
ms.openlocfilehash: f12c823f609ac309d4b5ddbbaa7d5a076a7bb9ad
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96447291"
---
# <a name="cross-tenant-analytics-using-extracted-data---multi-tenant-app"></a>Több-bérlős elemzés a kinyert adatszolgáltatások használatával – több-bérlős alkalmazás
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Ebben az oktatóanyagban egy teljes elemzési forgatókönyvet ismertetünk a több-bérlős implementációban. A forgatókönyv bemutatja, hogyan teheti lehetővé az elemzés a vállalatok számára az intelligens döntések meghozatalát. A többrétegű adatbázisból kinyert adatok felhasználásával az elemzéssel betekintést nyerhet a bérlők viselkedésére, beleértve a minta Wingtip tickets SaaS-alkalmazás használatát is. Ez a forgatókönyv három lépést foglal magában: 

1.  **Adatok kinyerése** az egyes bérlői adatbázisokból egy Analytics-tárolóba.
2.  **Optimalizálja a kinyert** adatelemzési folyamatokat.
3.  Az **üzleti intelligencia** eszközeivel hasznos elemzéseket készíthet, amelyek útmutatást nyújtanak a döntéshozatalhoz. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Hozza létre a bérlői elemzési tárolót az adatok a alkalmazásba való kinyeréséhez.
> - Rugalmas feladatok használatával kinyerheti az egyes bérlői adatbázisok adatait az Analytics-tárolóba.
> - Optimalizálja a kinyert adathalmazokat (egy csillag-sémába való átszervezést).
> - Az elemzési adatbázis lekérdezése.
> - Az adatvizualizációk Power BI használatával kiemelheti a bérlői adattrendeket, és javaslatokat tehet a tökéletesítésekhez.

![A diagram a cikkben használt architektúra áttekintését jeleníti meg.](./media/saas-multitenantdb-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Offline bérlői elemzési minta

Az Ön által fejlesztett SaaS-alkalmazások nagy mennyiségű, a felhőben tárolt információhoz férhetnek hozzá. Az adatok széles körű elemzéseket biztosítanak az alkalmazás működéséről és használatáról, valamint a bérlők viselkedéséről. Ezek az ismeretek segítik a funkciók fejlesztését, a használhatóság javítását, valamint az alkalmazás és a platform egyéb befektetéseit.

Az összes bérlőre vonatkozó adatok elérése egyszerű, ha az összes adatok csak egy több-bérlős adatbázisban vannak. A hozzáférés azonban összetettebb, ha több ezer adatbázis között oszlik meg. Az összetettség kinyerésének egyik módja az, hogy kinyeri az adatok elemzését egy elemzési adatbázisba vagy egy adattárházba. Ezután lekérdezheti az adattárházat, hogy bepillantást nyerjen az összes bérlő jegy-adatainak használatával.

Ez az oktatóanyag egy teljes elemzési forgatókönyvet mutat be ehhez a minta SaaS-alkalmazáshoz. Az első rugalmas feladatok az egyes bérlői adatbázisokból származó adatok kinyerésének a megadására szolgálnak. Az adatküldés egy Analytics-tárolóba történik. Az elemzési tár lehet egy SQL Database vagy egy Azure szinapszis Analytics. Nagy mennyiségű Adatkiemelés esetén [Azure Data Factory](../../data-factory/introduction.md) dicséretet biztosítunk.

Ezután az összesített adatokat a rendszer a [Star-Schema](https://www.wikipedia.org/wiki/Star_schema) táblákba gyűjti. A táblák egy központi tény tábla és a kapcsolódó dimenzió táblákból állnak:

- A Star-Schema központi tény táblázata a Ticket-adattartalomot tartalmazza.
- A dimenzió táblák a helyszínekkel, eseményekkel, ügyfelekkel és vásárlási dátumokkal kapcsolatos információkat tartalmaznak.

A központi és a dimenzió táblázatok együttesen teszik lehetővé a hatékony analitikai feldolgozást. Az oktatóanyagban használt csillag-séma az alábbi képen látható:
 
![Az adatbázisábra négy, központi adatbázis-objektumhoz csatlakozó adatbázis-objektumot mutat be.](./media/saas-multitenantdb-tenant-analytics/StarSchema.png)

Végül a Star-Schema táblákat kérdezi le. A lekérdezési eredmények vizuálisan jelennek meg, hogy kiemelje a bérlői viselkedést és az alkalmazás használatát. Ezzel a csillag-sémával olyan lekérdezéseket futtathat, amelyek segítenek a következőhöz hasonló elemek felderítésében:

- Ki vásárol jegyet és melyik helyszínről.
- Rejtett minták és trendek a következő területeken:
    - A jegyek eladása.
    - Az egyes helyszínek relatív népszerűsége.

Annak megértése, hogy az egyes bérlők milyen konzisztens módon használják a szolgáltatást, lehetővé teszi, hogy szolgáltatási terveket hozzon létre az igényeik kielégítésére. Ez az oktatóanyag a bérlői adatokból begyűjtött elemzések alapvető példáit mutatja be.

## <a name="setup"></a>Beállítás

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás telepítve van. Ha kevesebb mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése és megismerése](./saas-multitenantdb-get-started-deploy.md) című részt
- A Wingtip SaaS-parancsfájlok és az alkalmazás [forráskódja](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB) letölthető a githubról. Győződjön meg arról, hogy *feloldja a zip-fájl zárolását* a tartalom kicsomagolása előtt. Tekintse meg az [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets SaaS-parancsfájlok letöltésének és feloldásának lépéseihez.
- A Power BI Desktop telepítve van. [A Power BI Desktop letöltése](https://powerbi.microsoft.com/downloads/)
- További bérlők kötegét a [**bérlők kiépítése oktatóanyagban**](./saas-multitenantdb-provision-and-catalog.md)találhatja meg.
- A rendszer létrehozta A feladatok ügynökét és a Feladatkártya-adatbázist. Tekintse meg a megfelelő lépéseket a [**séma kezelése oktatóanyagban**](./saas-multitenantdb-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Adatgyűjtés a bemutatóhoz

Ebben az oktatóanyagban az elemzés a Ticket Sales adatain történik. Az aktuális lépésben a bérlők számára is létrehoz jegyet.  Az adatelemzéshez később kinyeri az adatgyűjtést. *Győződjön meg arról, hogy a korábban leírtaknak megfelelően kiépítte a bérlők kötegét, hogy jelentős mennyiségű adattal rendelkezzen*. Egy elég nagy mennyiségű adattal számos különböző jegy-vásárlási minta is elérhető.

1. A **POWERSHELL ISE**-ben nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*, és állítsa be a következő értéket:
    - **$DemoScenario**  =  **1** vásárlási jegyek minden helyszínen
2. Nyomja le az **F5** billentyűt a szkript futtatásához, és hozzon létre Ticket vásárlási előzményeket minden egyes helyszín eseményeihez.  A szkript több percet is igénybe vehet, hogy több tízezer jegyet lehessen készíteni.

### <a name="deploy-the-analytics-store"></a>Az Analytics-tároló üzembe helyezése
Az összes bérlői adattal együtt gyakran több tranzakciós szegmensben lévő adatbázis is található. A bérlői adatokat össze kell összesíteni a szilánkokra osztott adatbázisból egy Analytics-tárolóba. Az Összesítés lehetővé teszi az adatgyűjtés hatékony lekérdezését. Ebben az oktatóanyagban egy Azure SQL Database adatbázis tárolja az összesített adatokat.

A következő lépésekben telepíti a **tenantanalytics** nevű Analytics-tárolót. Az oktatóanyagban később feltöltött előre definiált táblákat is üzembe helyezhet:
1. A PowerShell ISE-ben nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Állítsa be az $DemoScenario változót a parancsfájlban, hogy az megfeleljen a választott elemzési tárolónak. A oszlopcentrikus nélküli adatbázis használata javasolt a tanulási célokra.
    - Ha oszlopcentrikus nélkül szeretné használni a SQL Databaset, állítsa be a **$DemoScenario**  =  **2**
    - Ha SQL Databaset szeretne használni a oszlopcentrikus , állítsa be a  =  **3** . $DemoScenario  
3. Nyomja le az **F5** billentyűt a bemutató parancsfájl futtatásához (amely meghívja az *Deploy-TenantAnalytics \<XX> . ps1* parancsfájlt), amely létrehozza a bérlői elemzési tárolót. 

Most, hogy telepítette az alkalmazást, és kitöltötte érdekes bérlői adatokkal, használja a [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) a **tenants1- \<User\> MT-** és a **Catalog \<User\> -MT-** kiszolgálók összekapcsolásához a login = *fejlesztői*, jelszó = *P \@ ssword1* használatával.

![architectureOverView](./media/saas-multitenantdb-tenant-analytics/ssmsSignIn.png)

A Object Explorer hajtsa végre a következő lépéseket:

1. Bontsa ki a *tenants1- \<User\> MT-* Server elemet.
2. Bontsa ki az adatbázisok csomópontot, és tekintse meg a több bérlőt tartalmazó *tenants1* -adatbázist.
3. Bontsa ki a *katalógus- \<User\> MT-* Server elemet.
4. Ellenőrizze, hogy megjelenik-e az Analytics-tároló és a jobaccount-adatbázis.

Az elemzési tár csomópontjának kibontásával tekintse meg a SSMS Object Explorer következő adatbázis-elemeit:

- A táblák **TicketsRawData** és **EventsRawData** a bérlői adatbázisokból származó nyers kinyert adatokkal rendelkeznek.
- A Star-Schema táblák a következők: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** és **dim_Dates**.
- A **sp_ShredRawExtractedData** tárolt eljárás a Star-Schema tábláknak a nyers adattáblákból való feltöltésére szolgál.

![A képernyőképen az Analytics-tároló csomópontja, például a táblák, a nézetek és a csomópontok Object Explorerja látható.](./media/saas-multitenantdb-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Adatbányászat 

### <a name="create-target-groups"></a>Célcsoportok létrehozása 

A folytatás előtt győződjön meg arról, hogy telepítette a projektfeladat-és jobaccount-adatbázist. A következő lépések során a rugalmas feladatok segítségével kinyerheti az adatok kinyerését a horizontálisan használt bérlők adatbázisából, és tárolhatja azokat az elemzési tárolóban. Ezután a második feladatsor felfordítja az adatokra, és a csillag-séma tábláiba tárolja azokat. Ez a két feladat két különböző célcsoporton fut, nevezetesen a **TenantGroup** és a **AnalyticsGroup**. A kinyerési művelet az összes bérlői adatbázist tartalmazó TenantGroup fut. Az aprítási feladatok a AnalyticsGroup futnak, amely csak az Analytics-tárolót tartalmazza. Hozza létre a célcsoportokat a következő lépések végrehajtásával:

1. A SSMS-ben kapcsolódjon a **jobaccount** adatbázishoz a Catalog-MT- \<User\> .
2. A SSMS nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics \ célcsoportok. SQL* 
3. Módosítsa a @User változót a parancsfájl tetején, és cserélje le a kifejezést `<User>` a Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezésekor használt felhasználói értékre.
4. Nyomja le az **F5** billentyűt a két célcsoportot létrehozó parancsfájl futtatásához.

### <a name="extract-raw-data-from-all-tenants"></a>Nyers adatok kinyerése az összes bérlőről

Előfordulhat, hogy a tranzakciók gyakrabban fordulnak elő a *Ticket és az ügyféladatok* esetében, mint az *esemény-és a helyszín* esetében. Ezért érdemes lehet külön és gyakrabban kinyerni a jegyeket és az ügyféladatokat, mint az Event és a helyszín adatok kinyerését. Ebben a szakaszban két különálló feladatot definiál és ütemezhet:

- A jegyek és az ügyféladatok kinyerése.
- Az esemény-és a helyszín adatok kinyerése.

Az egyes feladatok kinyerik az adataikat, és beolvasják azokat az Analytics-tárolóba. A kinyert adatok az Analytics Star-Schema-ben egy külön feladatsorból állnak.

1. A SSMS-ben kapcsolódjon a **jobaccount** adatbázishoz a Catalog-MT- \<User\> Server kiszolgálón.
2. A SSMS-ben nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.SQL*.
3. Módosítsa a @User szkriptet a parancsfájl tetején, és cserélje le a `<User>` nevet a Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezésekor használt felhasználónévre. 
4. Az **F5** billentyű lenyomásával futtathatja a parancsfájlt, amely létrehozza és futtatja a jegyeket és az ügyfelek adatait az egyes bérlői adatbázisokból. A művelet elmenti az adatok elemzését az Analytics-tárolóba.
5. A tenantanalytics-adatbázis TicketsRawData táblájának lekérdezése annak biztosítására, hogy a tábla az összes bérlőről származó jegyek adataival legyen feltöltve.

![A képernyőképen a ExtractTickets-adatbázis jelenik meg, amelyen az Object Explorer d b o van kiválasztva.](./media/saas-multitenantdb-tenant-analytics/ticketExtracts.png)

Ismételje meg az előző lépéseket, a 2. lépésben lecserélve a **\ExtractTickets.SQL** a **\ExtractVenuesEvents.SQL** elemre.

A feladatot sikeresen futtatva az Analytics-tárolóban található EventsRawData-tábla feltölti az összes bérlő új eseményeit és helyszíneit. 

## <a name="data-reorganization"></a>Adatátszervezés

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Kibontott adatok a Star-Schema táblák feltöltéséhez

A következő lépés a kinyert nyers adatok beillesztése az elemzési lekérdezésekhez optimalizált táblák halmazára. A rendszer csillag-sémát használ. A központi tábla az egyes Ticket Sales rekordokat tartalmazza. A dimenzió táblák a helyszínekkel, eseményekkel, ügyfelekkel és vásárlási dátumokkal kapcsolatos adatokkal vannak feltöltve. 

Az oktatóanyag ezen szakaszában definiálhat és futtathat egy olyan feladatot, amely egyesíti a kinyert nyers adatmennyiséget a Star-Schema táblákban található adatokkal. Az egyesítési feladatok befejezése után a rendszer törli a nyers adatokat, így a táblák készen állnak a következő bérlői adatkivonati feladatokkal való feltöltésre.

1. A SSMS-ben kapcsolódjon a **jobaccount** adatbázishoz a Catalog-MT- \<User\> .
2. A SSMS-ben nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.SQL*.
3. Nyomja le az **F5** billentyűt a szkript futtatásához egy olyan feladat definiálásához, amely meghívja az sp_ShredRawExtractedData tárolt eljárást az elemzési tárolóban.
4. A feladatok sikeres futtatásának engedélyezése elég idő.
    - Keresse meg jobs.jobs_execution tábla **életciklus** oszlopát a feladatok állapotára vonatkozóan. A folytatás előtt győződjön meg arról, hogy a művelet **sikeres** volt. A sikeres Futtatás az alábbi diagramhoz hasonló adattípust jelenít meg:

![A képernyőkép a sp_ShredRawExtractedData eljárás futtatásának sikeres eredményét jeleníti meg.](./media/saas-multitenantdb-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Adatelemzés

### <a name="visualize-tenant-data"></a>Bérlői adatmegjelenítés

A Star-Schema tábla adatai az elemzéshez szükséges összes Ticket Sales-adatforgalmat biztosítják. A nagyméretű adatkészletek trendjeinek könnyebb megjelenítéséhez grafikusan kell megjelenítenie.  Ebből a szakaszból megtudhatja, hogyan kezelheti és jelenítheti meg a kinyert és szervezett bérlői információkat a **Power bi** használatával.

A következő lépésekkel csatlakozhat a Power BIhoz, és importálhatja a korábban létrehozott nézeteket:

1. Indítsa el Power BI Desktopot.
2. A Kezdőlap menüszalagon válassza az **adatok lekérése** lehetőséget, és válassza a **továbbiak...** lehetőséget. a menüből.
3. Az **adatlekérdezés** ablakban válassza a Azure SQL Database lehetőséget.
4. Az adatbázis-bejelentkezés ablakban adja meg a kiszolgáló nevét (Catalog-MT- \<User\> . database.Windows.net). Válassza  az importálás **adatkapcsolati módra** lehetőséget, majd kattintson az OK gombra. 

    ![Képernyőfelvétel: SQL Server adatbázis párbeszédpanel, amelyen megadhatja a kiszolgálót és az adatbázist.](./media/saas-multitenantdb-tenant-analytics/powerBISignIn.PNG)

5. Válassza ki az **adatbázist** a bal oldali ablaktáblán, majd írja be a Felhasználónév = *fejlesztő* nevet, majd írja be a Password = *P \@ ssword1* értéket. Kattintson a **Csatlakozás** gombra.  

    ![Képernyőfelvétel: a SQL Server adatbázis párbeszédpanel, amelyen megadhatja a felhasználónevet és a jelszót.](./media/saas-multitenantdb-tenant-analytics/databaseSignIn.PNG)

6. A **navigátor** ablaktábla elemzési adatbázis területén válassza ki a csillag-séma táblákat: fact_Tickets, dim_Events, dim_Venues, dim_Customers és dim_Dates. Ezután válassza a **Betöltés** lehetőséget. 

Gratulálunk! Sikeresen betöltötte az adatPower BIba. Most már megkezdheti az érdekes vizualizációk megismerését, hogy könnyebben betekintést nyerjen a bérlők felé. A következő lépésekkel megtudhatja, hogyan teheti lehetővé az adatvezérelt ajánlásokat a Wingtip tickets Business csapat számára. A javaslatok segítségével optimalizálható az üzleti modell és a felhasználói élmény.

Első lépésként elemezze a jegyek értékesítési adatait, hogy megtekintse a különböző helyszíneken használatos változást. Válassza ki az alábbi beállításokat a Power BI az egyes helyszíneken eladott jegyek teljes számát ábrázoló sávdiagram ábrázolásához. A Ticket Generator véletlenszerű variációja miatt előfordulhat, hogy az eredmények eltérőek.
 
![Képernyőfelvétel: a Power B I vizualizáció és a jobb oldalon látható adatvizualizációk vezérlői.](./media/saas-multitenantdb-tenant-analytics/TotalTicketsByVenues.PNG)

Az előző ábra megerősíti, hogy az egyes helyszínek által eladott jegyek száma változó. A több jegyet értékesítő helyszínek sokkal nagyobb mértékben használják a szolgáltatást, mint a kevesebb jegyet értékesítő helyszíneken. Lehetőség van arra, hogy az erőforrás-kiosztást a különböző bérlői igényeknek megfelelően testre lehessen szabni.

Tovább elemezheti az adatelemzést, hogy megtudja, hogyan változnak a jegyek forgalma az idő múlásával. Válassza ki az alábbi beállításokat a Power BIban az egyes napokon a 60 napos időszakra eladott jegyek teljes számának ábrázolásához.
 
![Képernyőfelvétel: a Power B I vizualizáció a jegyek értékesítésének eloszlása és az értékesítés napján látható.](./media/saas-multitenantdb-tenant-analytics/SaleVersusDate.PNG)

Az előző diagramon látható, hogy egyes helyszíneken a Ticket Sales Spike szerepel. Ezek a tüskék erősítik azt a gondolatot, hogy egyes helyszínek aránytalanul nagy mennyiségű rendszererőforrást fogyasztanak. Eddig nem látható mintázat a tüskék előfordulásakor.

Ezt követően továbbra is meg kívánja vizsgálni ezeknek a maximális értékesítési napoknak a jelentőségét. Mikor fordulnak elő ezek a csúcsok a jegyek eladása után? A naponta eladott jegyek kiválasztásához válassza a következő lehetőségeket a Power BI.

![SaleDayDistribution](./media/saas-multitenantdb-tenant-analytics/SaleDistributionPerDay.PNG)

Az előző ábrán látható, hogy egyes helyszínek nagy mennyiségű jegyet árulnak az első napján. Amint a jegyek ezen a helyszíneken kerülnek értékesítésre, úgy tűnik, hogy egy őrült rohanás. Ez a feltört tevékenység néhány helyszín esetében hatással lehet a szolgáltatásra más bérlők esetében.

Az adat részletezésével megtekintheti, hogy ez az őrült rohanás igaz-e az ezen helyszínek által üzemeltetett összes esemény esetében. A korábbi mintaterületeken megfigyelte, hogy a contoso Concert Hall nagy mennyiségű jegyet árul, és a contoso egy adott napon belül is tartalmaz egy tüske-értékesítést. A Power BI lehetőségek közül választhat a contoso Concert-csarnok összesített jegyeladásának ábrázolásához, amely az egyes események értékesítési trendjeire koncentrál. Az összes esemény ugyanazt az eladási mintát követi?

![ContosoSales](./media/saas-multitenantdb-tenant-analytics/EventSaleTrends.PNG)

A contoso Concert Hall előző ábrázolása azt mutatja, hogy a Mad Rush nem minden eseménynél fordul elő. A szűrési beállításokkal megtekintheti az értékesítési trendeket más helyszíneken.

A jegyek értékesítési mintáinak betekintése Wingtip jegyeket eredményezhet üzleti modelljének optimalizálásához. Az összes bérlő egyforma kitöltése helyett előfordulhat, hogy a Wingtip különböző számítási méretekkel kell bevezetni a szolgáltatási szinteket. A naponta több jegyet értékesítő nagyobb helyszínek magasabb szintű szolgáltatói szerződéssel (SLA) is elérhetők. Ezeknek a helyszíneknek az adatbázisai a készletbe helyezhetők, és az adatbázison belüli erőforrások korlátai magasabbak. Az egyes szolgáltatási szintek óránkénti értékesítési kiosztással rendelkezhetnek, és a foglalást meghaladó költségekkel számoljuk el. Az időszakos értékesítési lehetőségekkel rendelkező nagyobb helyszínek a magasabb szinteken is hasznosak lehetnek, és a Wingtip jegyek hatékonyabban növelhetik a szolgáltatást.

Néhány Wingtip-jegy arra is panaszkodik, hogy az ügyfelek nem tudnak elegendő jegyet értékesíteni, hogy igazolják a szolgáltatás költségeit. Előfordulhat, hogy ezekben az elemzésekben lehetőség van a jegyek értékesítésének növelésére a helyszíneken. A magasabb eladások növelhetik a szolgáltatás észlelt értékét. Kattintson a jobb gombbal fact_Tickets és válassza az **új mérték** lehetőséget. Adja meg a következő kifejezést a **AverageTicketsSold** nevű új mértékhez:

```
AverageTicketsSold = DIVIDE(DIVIDE(COUNTROWS(fact_Tickets),DISTINCT(dim_Venues[VenueCapacity]))*100, COUNTROWS(dim_Events))
```

A következő vizualizációs beállítások kiválasztásával ábrázolhatja az egyes helyszíneken eladott jegyek százalékos arányát a relatív sikeresség megállapításához.

![analyticsViews](./media/saas-multitenantdb-tenant-analytics/AvgTicketsByVenues.PNG)

Az előző ábrán látható, hogy bár a legtöbb helyszín több mint 80%-át értékesíti a jegyeket, néhányan azért küzd, hogy több mint felet töltsön ki a helyükre. Az egyes helyszínekre eladott jegyek maximális vagy minimális százalékos arányának kiválasztásához az értékekkel is játszhat.

Korábban elmélyíti az elemzést, amely felderíti, hogy a jegyek értékesítései általában előre jelezhető mintákat követnek. Ez a felderítés lehetővé teheti, hogy a Wingtip jegyek a dinamikus díjszabásra vonatkozó javaslatokkal növelhetik a jegyek értékesítését. Ez a felderítés felfedi a lehetőséget, hogy gépi tanulási technikákat alkalmazzon az egyes eseményekhez tartozó jegyeladások előrejelzéséhez. Előrejelzéseket is el lehet végezni a jegyek értékesítésével járó kedvezményekre gyakorolt hatás tekintetében. Power BI Embedded integrálható egy eseménykezelő alkalmazásba. Az integráció segíthet az előre jelzett eladások megjelenítésében és a különböző kedvezmények hatásán. Az alkalmazás segíthet kialakítani az optimális kedvezményt, amelyet közvetlenül az elemzési kijelzőből alkalmazhat.

Megfigyelte a Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás bérlői adatainak tendenciáit. Más módokat is átadhat az alkalmazásnak az SaaS-alkalmazások gyártóinak üzleti döntéseiről. A szállítók jobban ellátják a bérlők igényeit. Remélhetőleg ez az oktatóanyag olyan eszközöket tartalmaz, amelyek szükségesek a bérlői adatok elemzéséhez, hogy a vállalatoknak adatvezérelt döntéseket lehessen hozni.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> - Bérlői elemzési adatbázis üzembe helyezése előre definiált Star Schema Tables-táblázatokkal
> - Rugalmas feladatok használata az adatok kinyeréséhez az összes bérlői adatbázisból
> - A kinyert adathalmazok egyesítése az elemzésre tervezett csillag-séma tábláiba
> - Elemzési adatbázis lekérdezése 
> - A bérlői adatvizualizációk trendjeinek megfigyelése Power BI használatával 

Gratulálunk!

## <a name="additional-resources"></a>További források

[Az Wingtip SaaS-alkalmazásra épülő további oktatóanyagok](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials). 
- [Rugalmas feladatok](./elastic-jobs-overview.md).
- [Több-bérlős elemzés a kinyert adategységek használatával – egybérlős alkalmazás](saas-tenancy-tenant-analytics.md)