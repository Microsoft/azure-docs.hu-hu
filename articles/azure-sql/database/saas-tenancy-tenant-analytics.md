---
title: Több-bérlős elemzés kinyert adatszolgáltatásokkal
description: Több-bérlős elemzési lekérdezések több Azure SQL-adatbázisból kinyert adatok használatával egyetlen bérlős alkalmazásban.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 12/18/2018
ms.openlocfilehash: 98896b5b728a729a29f989b3b9a76f29131af8d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "93305966"
---
# <a name="cross-tenant-analytics-using-extracted-data---single-tenant-app"></a>Több-bérlős elemzés a kinyert adategységek használatával – egybérlős alkalmazás
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]
 
Ebben az oktatóanyagban egy teljes elemzési forgatókönyvet ismertetünk egyetlen bérlős implementációban. A forgatókönyv bemutatja, hogyan teheti lehetővé az elemzés a vállalatok számára az intelligens döntések meghozatalát. Az egyes bérlői adatbázisokból kinyert adatok felhasználásával az elemzéssel betekintést nyerhet a bérlők viselkedésére, beleértve a minta Wingtip tickets SaaS-alkalmazás használatát is. Ez a forgatókönyv három lépést foglal magában: 

1.  **Kinyerheti** az összes bérlői adatbázis adatait, és **betöltheti** azokat egy Analytics-tárolóba.
2.  **Alakítsa át a kinyert** adatelemzési folyamatokat.
3.  Az **üzleti intelligencia** eszközeivel hasznos elemzéseket készíthet, amelyek útmutatást nyújtanak a döntéshozatalhoz. 

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> - Hozza létre a bérlői elemzési tárolót az adatok a alkalmazásba való kinyeréséhez.
> - Rugalmas feladatok használatával kinyerheti az egyes bérlői adatbázisok adatait az Analytics-tárolóba.
> - Optimalizálja a kinyert adathalmazokat (egy csillag-sémába való átszervezést).
> - Az elemzési adatbázis lekérdezése.
> - Az adatvizualizációk Power BI használatával kiemelheti a bérlői adattrendeket, és javaslatokat tehet a tökéletesítésekhez.

![A diagram a cikkben használt architektúra áttekintését jeleníti meg.](./media/saas-tenancy-tenant-analytics/architectureOverview.png)

## <a name="offline-tenant-analytics-pattern"></a>Offline bérlői elemzési minta

A több-bérlős SaaS-alkalmazások általában nagy mennyiségű, a felhőben tárolt bérlői adattal rendelkeznek. Ezek az adatok széles körű elemzéseket biztosítanak az alkalmazás működéséről és használatáról, valamint a bérlők viselkedéséről. Ezek az ismeretek segítik a funkciók fejlesztését, a használhatóság javítását, valamint az alkalmazás és a platform egyéb befektetéseit.

Az összes bérlőre vonatkozó adatok elérése egyszerű, ha az összes adatok csak egy több-bérlős adatbázisban vannak. A hozzáférés azonban összetettebb, ha akár több ezer adatbázis között történik a skálázás. Az egyik módszer az összetettség és a tranzakciós adatokon alapuló elemzési lekérdezések hatásának minimalizálására, hogy az adatok egy célra tervezett elemzési adatbázisba vagy adattárházba legyenek kinyerve.

Ez az oktatóanyag egy teljes elemzési forgatókönyvet mutat be a Wingtip tickets SaaS-alkalmazáshoz. Első lépésként a *rugalmas feladatok* segítségével kinyerheti az egyes bérlői adatbázisok adatait, és betöltheti azokat átmeneti táblákba egy elemzési tárolóban. Az elemzési tár lehet egy SQL Database vagy egy dedikált SQL-készlet. Nagy mennyiségű Adatkiemelés esetén [Azure Data Factory](../../data-factory/introduction.md) ajánlott.

Ezt követően az összesített adatokat a rendszer a [Star-Schema](https://www.wikipedia.org/wiki/Star_schema) tábláiba alakítja át. A táblák egy központi tény tábla és a kapcsolódó dimenzió táblákból állnak.  Wingtip jegyek esetén:

- A Star-Schema központi tény táblázata a Ticket-adattartalomot tartalmazza.
- A dimenzió táblázatok a helyszíneket, az eseményeket, az ügyfeleket és a vásárlási dátumokat írják le.

A központi tény és a dimenzió táblázatok együttesen teszik lehetővé a hatékony analitikai feldolgozást. Az oktatóanyagban használt csillag-séma az alábbi képen látható:
 
![architectureOverView](./media/saas-tenancy-tenant-analytics/StarSchema.png)

Végül a **Power bi** használatával kérdezi le az elemzési tárolót, hogy kiemelje a bérlői viselkedést és a Wingtip tickets-alkalmazás használatát. A lekérdezéseket a következőket kell futtatnia:
 
- Az egyes helyszínek relatív népszerűségének megjelenítése
- Minták kiemelése a jegyek értékesítésében különböző eseményekhez
- A különböző helyszínek relatív sikerességének megjelenítése az esemény értékesítésével kapcsolatban

Annak megismerése, hogy az egyes bérlők hogyan használják a szolgáltatást, hogy megismerjék a szolgáltatással kapcsolatos bevételi lehetőségeket, és hogy a bérlők könnyebben tudják segíteni a szolgáltatást. Ez az oktatóanyag alapvető példákat tartalmaz a bérlői adatokból begyűjtött elemzések típusára.

## <a name="setup"></a>Beállítás

### <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- A Wingtip tickets SaaS-adatbázist bérlői alkalmazásként helyezi üzembe. Ha kevesebb, mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip SaaS-alkalmazás üzembe helyezése és megismerése](./saas-dbpertenant-get-started-deploy.md)
- A Wingtip jegyek SaaS-adatbázisa bérlői parancsfájlokban és az alkalmazás [forráskódja](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/) a githubról tölthető le. Lásd: letöltési utasítások. Győződjön meg arról, hogy *feloldja a zip-fájl zárolását* a tartalom kicsomagolása előtt. Tekintse meg az [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets SaaS-parancsfájlok letöltésének és feloldásának lépéseihez.
- A Power BI Desktop telepítve van. [A Power BI Desktop letöltése](https://powerbi.microsoft.com/downloads/)
- További bérlők kötegét a [**bérlők kiépítése oktatóanyagban**](./saas-dbpertenant-provision-and-catalog.md)találhatja meg.
- A rendszer létrehoz egy feladatot és egy feladatütemezés-adatbázist. Tekintse meg a megfelelő lépéseket a [**séma kezelése oktatóanyagban**](./saas-tenancy-schema-management.md#create-a-job-agent-database-and-new-job-agent).

### <a name="create-data-for-the-demo"></a>Adatgyűjtés a bemutatóhoz

Ebben az oktatóanyagban az elemzés a Ticket Sales adatain történik. Az aktuális lépésben a bérlők számára is létrehoz jegyet.  Az adatelemzéshez később kinyeri az adatgyűjtést. *Győződjön meg arról, hogy a korábban leírtaknak megfelelően kiépítte a bérlők kötegét, hogy jelentős mennyiségű adattal rendelkezzen*. Egy elég nagy mennyiségű adattal számos különböző jegy-vásárlási minta is elérhető.

1. A PowerShell ISE-ben nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1*, és állítsa be a következő értéket:
    - **$DemoScenario**  =  **1** vásárlási jegyek minden helyszínen
2. Nyomja le az **F5** billentyűt a szkript futtatásához, és hozzon létre Ticket vásárlási előzményeket minden egyes helyszín eseményeihez.  A szkript több percet is igénybe vehet, hogy több tízezer jegyet lehessen készíteni.

### <a name="deploy-the-analytics-store"></a>Az Analytics-tároló üzembe helyezése
Gyakran számos tranzakciós adatbázis van, amelyek együtt tartalmazzák az összes bérlői adattárolást. A bérlői adatokat a számos tranzakciós adatbázisból egy Analytics-tárolóba kell összevonnia. Az Összesítés lehetővé teszi az adatgyűjtés hatékony lekérdezését. Ebben az oktatóanyagban egy Azure SQL Database az összesített adatokat tárolja.

A következő lépésekben telepíti a **tenantanalytics** nevű Analytics-tárolót. Az oktatóanyagban később feltöltött előre definiált táblákat is üzembe helyezhet:
1. A PowerShell ISE-ben nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics\Demo-TenantAnalytics.ps1* 
2. Állítsa be az $DemoScenario változót a parancsfájlban, hogy az megfeleljen a választott elemzési tárolónak:
    - Ha SQL Databaset szeretne használni az oszlopok tárolása nélkül, állítsa be a **$DemoScenario**  =  **2**
    - Ha SQL Databaset szeretne használni az oszlopos tárolóval, állítsa be az **$DemoScenario**  =  **3**  
3. Nyomja le az **F5** billentyűt a bemutató parancsfájl futtatásához (amely meghívja az *Deploy-TenantAnalytics \<XX> . ps1* parancsfájlt), amely létrehozza a bérlői elemzési tárolót. 

Most, hogy telepítette az alkalmazást, és kitöltötte érdekes bérlői adatokkal, használja a [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) -t a **tenants1-DPT- &lt; User &gt;** és a **Catalog-DPT- &lt; User &gt;** kiszolgálók bejelentkezési = *fejlesztői*, jelszó = *P \@ ssword1* használatával való összekapcsolásához. További útmutatásért tekintse meg a [bevezető oktatóanyagot](./saas-dbpertenant-wingtip-app-overview.md) .

![A SQL Serverhoz való kapcsolódáshoz szükséges adatokat bemutató képernyőkép.](./media/saas-tenancy-tenant-analytics/ssmsSignIn.png)

A Object Explorer hajtsa végre a következő lépéseket:

1. Bontsa ki a *tenants1-DPT- &lt; User &gt;* kiszolgálót.
2. Bontsa ki az adatbázisok csomópontot, és tekintse meg a bérlői adatbázisok listáját.
3. Bontsa ki a *Catalog-DPT- &lt; User &gt;* kiszolgálót.
4. Ellenőrizze, hogy megjelenik-e az Analytics-tároló és a jobaccount-adatbázis.

Az elemzési tár csomópontjának kibontásával tekintse meg a SSMS Object Explorer következő adatbázis-elemeit:

- A táblák **TicketsRawData** és **EventsRawData** a bérlői adatbázisokból származó nyers kinyert adatokkal rendelkeznek.
- A Star-Schema táblák a következők: **fact_Tickets**, **dim_Customers**, **dim_Venues**, **dim_Events** és **dim_Dates**.
- A tárolt eljárás a Star-Schema táblák nyers adattáblákból való feltöltésére szolgál.

![Képernyőkép a SSMS Object Explorer látható adatbázis-elemekről.](./media/saas-tenancy-tenant-analytics/tenantAnalytics.png)

## <a name="data-extraction"></a>Adatbányászat 

### <a name="create-target-groups"></a>Célcsoportok létrehozása 

A folytatás előtt győződjön meg arról, hogy telepítette a projektfeladat-és jobaccount-adatbázist. A következő lépésekben a rugalmas feladatok az egyes bérlői adatbázisokból származó adatok kinyerésére és az adatoknak az elemzési tárolóban történő tárolására szolgálnak. Ezután a második feladatsor felfordítja az adatokra, és a csillag-séma tábláiba tárolja azokat. Ez a két feladat két különböző célcsoporton fut, nevezetesen a **TenantGroup** és a **AnalyticsGroup**. A kinyerési művelet az összes bérlői adatbázist tartalmazó TenantGroup fut. Az aprítási feladatok a AnalyticsGroup futnak, amely csak az Analytics-tárolót tartalmazza. Hozza létre a célcsoportokat a következő lépések végrehajtásával:

1. A SSMS-ben kapcsolódjon a **jobaccount** -adatbázishoz a Catalog-DPT-User-ben &lt; &gt; .
2. A SSMS nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics \ célcsoportok. SQL* 
3. Módosítsa a @User változót a parancsfájl tetején, `<User>` és cserélje le a értéket a Wingtip SaaS-alkalmazás üzembe helyezésekor használt felhasználói értékre.
4. Nyomja le az **F5** billentyűt a két célcsoportot létrehozó parancsfájl futtatásához.

### <a name="extract-raw-data-from-all-tenants"></a>Nyers adatok kinyerése az összes bérlőről

A nagy mennyiségű adatmódosítás gyakrabban fordulhat elő a *Ticket és az ügyféladatok* esetében, mint az *esemény-és a helyszín* esetében. Ezért érdemes lehet külön és gyakrabban kinyerni a jegyeket és az ügyféladatokat, mint az Event és a helyszín adatok kinyerését. Ebben a szakaszban két különálló feladatot definiál és ütemezhet:

- A jegyek és az ügyféladatok kinyerése.
- Az esemény-és a helyszín adatok kinyerése.

Az egyes feladatok kinyerik az adataikat, és beolvasják azokat az Analytics-tárolóba. A kinyert adatok az Analytics Star-Schema-ben egy külön feladatsorból állnak.

1. A SSMS-ben kapcsolódjon a **jobaccount** -adatbázishoz a Catalog-DPT- &lt; User &gt; kiszolgálón.
2. A SSMS-ben nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ExtractTickets.SQL*.
3. Módosítsa a @User szkript felső részén, és cserélje le a parancsot `<User>` a Wingtip SaaS-alkalmazás üzembe helyezésekor használt felhasználónévre. 
4. Az F5 billentyű lenyomásával futtathatja a parancsfájlt, amely létrehozza és futtatja a jegyeket és az ügyfelek adatait az egyes bérlői adatbázisokból. A művelet elmenti az adatok elemzését az Analytics-tárolóba.
5. A tenantanalytics-adatbázis TicketsRawData táblájának lekérdezése annak biztosítására, hogy a tábla az összes bérlőről származó jegyek adataival legyen feltöltve.

![A képernyőképen a ExtractTickets-adatbázis jelenik meg, amelyen az Object Explorer d b o van kiválasztva.](./media/saas-tenancy-tenant-analytics/ticketExtracts.png)

Ismételje meg az előző lépéseket, a 2. lépésben lecserélve a **\ExtractTickets.SQL** a **\ExtractVenuesEvents.SQL** elemre.

A feladatot sikeresen futtatva az Analytics-tárolóban található EventsRawData-tábla feltölti az összes bérlő új eseményeit és helyszíneit. 

## <a name="data-reorganization"></a>Adatátszervezés

### <a name="shred-extracted-data-to-populate-star-schema-tables"></a>Kibontott adatok a Star-Schema táblák feltöltéséhez

A következő lépés a kinyert nyers adatok beillesztése az elemzési lekérdezésekhez optimalizált táblák halmazára. A rendszer csillag-sémát használ. A központi tábla az egyes Ticket Sales rekordokat tartalmazza. A többi tábla a helyszínekről, eseményekről és ügyfelekről származó kapcsolódó adatokkal van feltöltve. És vannak idődimenziós táblázatok is. 

Az oktatóanyag ezen szakaszában definiálhat és futtathat egy olyan feladatot, amely egyesíti a kinyert nyers adatmennyiséget a Star-Schema táblákban található adatokkal. Az egyesítési feladatok befejezése után a rendszer törli a nyers adatokat, így a táblák készen állnak a következő bérlői adatkivonati feladatokkal való feltöltésre.

1. A SSMS-ben kapcsolódjon a **jobaccount** -adatbázishoz a Catalog-DPT-User-ben &lt; &gt; .
2. A SSMS-ben nyissa meg a *. ..\Learning Modules\Operational Analytics\Tenant Analytics\ShredRawExtractedData.SQL*.
3. Nyomja le az **F5** billentyűt a szkript futtatásához egy olyan feladat definiálásához, amely meghívja az sp_ShredRawExtractedData tárolt eljárást az elemzési tárolóban.
4. A feladatok sikeres futtatásának engedélyezése elég idő.
    - Keresse meg jobs.jobs_execution tábla **életciklus** oszlopát a feladatok állapotára vonatkozóan. A folytatás előtt győződjön meg arról, hogy a művelet **sikeres** volt. A sikeres Futtatás az alábbi diagramhoz hasonló adattípust jelenít meg:

![aprított](./media/saas-tenancy-tenant-analytics/shreddingJob.PNG)

## <a name="data-exploration"></a>Adatelemzés

### <a name="visualize-tenant-data"></a>Bérlői adatmegjelenítés

A Star-Schema tábla adatai az elemzéshez szükséges összes Ticket Sales-adatforgalmat biztosítják. A nagyméretű adatkészletek trendjeinek könnyebb megjelenítéséhez grafikusan kell megjelenítenie.  Ebből a szakaszból megtudhatja, hogyan kezelheti és jelenítheti meg a kinyert és szervezett bérlői információkat a **Power bi** használatával.

A következő lépésekkel csatlakozhat a Power BIhoz, és importálhatja a korábban létrehozott nézeteket:

1. Indítsa el Power BI Desktopot.
2. A Kezdőlap menüszalagon válassza az **adatok lekérése** lehetőséget, és válassza a **továbbiak...** lehetőséget. a menüből.
3. Az **adatlekérdezés** ablakban válassza a Azure SQL Database lehetőséget.
4. Az adatbázis-bejelentkezés ablakban adja meg a kiszolgáló nevét (Catalog-DPT- &lt; User &gt; . database.Windows.net). Válassza  az importálás **adatkapcsolati módra** lehetőséget, majd kattintson az OK gombra. 

    ![signinpowerbi](./media/saas-tenancy-tenant-analytics/powerBISignIn.PNG)

5. Válassza ki az **adatbázist** a bal oldali ablaktáblán, majd írja be a Felhasználónév = *fejlesztő* nevet, majd írja be a Password = *P \@ ssword1* értéket. Kattintson a **Csatlakozás** gombra.  

    ![Képernyőfelvétel: a SQL Server adatbázis párbeszédpanel, amelyen megadhatja a felhasználónevet és a jelszót.](./media/saas-tenancy-tenant-analytics/databaseSignIn.PNG)

6. A **navigátor** ablaktábla elemzési adatbázis területén válassza ki a csillag-séma táblákat: fact_Tickets, dim_Events, dim_Venues, dim_Customers és dim_Dates. Ezután válassza a **Betöltés** lehetőséget. 

Gratulálunk! Sikeresen betöltötte az adatPower BIba. Most már megkezdheti az érdekes vizualizációk megismerését, hogy könnyebben betekintést nyerjen a bérlők felé. A következő lépésekkel megtudhatja, hogyan teheti lehetővé az adatvezérelt ajánlásokat a Wingtip tickets Business csapat számára. A javaslatok segítségével optimalizálható az üzleti modell és a felhasználói élmény.

Első lépésként elemezze a jegyek értékesítési adatait, hogy megtekintse a különböző helyszíneken használatos változást. Válassza ki az alábbi beállításokat a Power BI az egyes helyszíneken eladott jegyek teljes számát ábrázoló sávdiagram ábrázolásához. A Ticket Generator véletlenszerű variációja miatt előfordulhat, hogy az eredmények eltérőek.
 
![Képernyőfelvétel: a Power B I vizualizáció és a jobb oldalon látható adatvizualizációk vezérlői.](./media/saas-tenancy-tenant-analytics/TotalTicketsByVenues.PNG)

Az előző ábra megerősíti, hogy az egyes helyszínek által eladott jegyek száma változó. A több jegyet értékesítő helyszínek sokkal nagyobb mértékben használják a szolgáltatást, mint a kevesebb jegyet értékesítő helyszíneken. Lehetőség van arra, hogy az erőforrás-kiosztást a különböző bérlői igényeknek megfelelően testre lehessen szabni.

Tovább elemezheti az adatelemzést, hogy megtudja, hogyan változnak a jegyek forgalma az idő múlásával. Válassza ki az alábbi beállításokat a Power BIban az egyes napokon a 60 napos időszakra eladott jegyek teljes számának ábrázolásához.
 
![Képernyőfelvétel: a Power B I vizualizáció a jegyek értékesítésének eloszlása és az értékesítés napján látható.](./media/saas-tenancy-tenant-analytics/SaleVersusDate.PNG)

Az előző diagramon látható, hogy egyes helyszíneken a Ticket Sales Spike szerepel. Ezek a tüskék erősítik azt a gondolatot, hogy egyes helyszínek aránytalanul nagy mennyiségű rendszererőforrást fogyasztanak. Eddig nem látható mintázat a tüskék előfordulásakor.

Ezt követően továbbra is meg kívánja vizsgálni ezeknek a maximális értékesítési napoknak a jelentőségét. Mikor fordulnak elő ezek a csúcsok a jegyek eladása után? A naponta eladott jegyek kiválasztásához válassza a következő lehetőségeket a Power BI.

![SaleDayDistribution](./media/saas-tenancy-tenant-analytics/SaleDistributionPerDay.PNG)

Az előző ábrán látható, hogy egyes helyszínek nagy mennyiségű jegyet árulnak az első napján. Amint a jegyek ezen a helyszíneken kerülnek értékesítésre, úgy tűnik, hogy egy őrült rohanás. Ez a feltört tevékenység néhány helyszín esetében hatással lehet a szolgáltatásra más bérlők esetében.

Az adat részletezésével megtekintheti, hogy ez az őrült rohanás igaz-e az ezen helyszínek által üzemeltetett összes esemény esetében. A korábbi mintaterületeken megfigyelte, hogy a contoso Concert Hall nagy mennyiségű jegyet árul, és a contoso egy adott napon belül is tartalmaz egy tüske-értékesítést. A Power BI lehetőségek közül választhat a contoso Concert-csarnok összesített jegyeladásának ábrázolásához, amely az egyes események értékesítési trendjeire koncentrál. Az összes esemény ugyanazt az eladási mintát követi?

![ContosoSales](./media/saas-tenancy-tenant-analytics/EventSaleTrends.PNG)

A contoso Concert Hall előző ábrázolása azt mutatja, hogy a Mad Rush nem minden eseménynél fordul elő. A szűrési beállításokkal megtekintheti az értékesítési trendeket más helyszíneken.

A jegyek értékesítési mintáinak betekintése Wingtip jegyeket eredményezhet üzleti modelljének optimalizálásához. Az összes bérlő egyforma kitöltése helyett előfordulhat, hogy a Wingtip különböző számítási méretekkel kell bevezetni a szolgáltatási szinteket. A naponta több jegyet értékesítő nagyobb helyszínek magasabb szintű szolgáltatói szerződéssel (SLA) is elérhetők. Ezeknek a helyszíneknek az adatbázisai a készletbe helyezhetők, és az adatbázison belüli erőforrások korlátai magasabbak. Az egyes szolgáltatási szintek óránkénti értékesítési kiosztással rendelkezhetnek, és a foglalást meghaladó költségekkel számoljuk el. Az időszakos értékesítési lehetőségekkel rendelkező nagyobb helyszínek a magasabb szinteken is hasznosak lehetnek, és a Wingtip jegyek hatékonyabban növelhetik a szolgáltatást.

Néhány Wingtip-jegy arra is panaszkodik, hogy az ügyfelek nem tudnak elegendő jegyet értékesíteni, hogy igazolják a szolgáltatás költségeit. Előfordulhat, hogy ezekben az adatokban lehetősége van arra, hogy növelje a jegyek értékesítését az elvégezhető helyszíneken. A magasabb eladások növelhetik a szolgáltatás észlelt értékét. Kattintson a jobb gombbal fact_Tickets és válassza az **új mérték** lehetőséget. Adja meg a következő kifejezést a **AverageTicketsSold** nevű új mértékhez:

```
AverageTicketsSold = AVERAGEX( SUMMARIZE( TableName, TableName[Venue Name] ), CALCULATE( SUM(TableName[Tickets Sold] ) ) )
```

A következő vizualizációs beállítások kiválasztásával ábrázolhatja az egyes helyszíneken eladott jegyek százalékos arányát a relatív sikeresség megállapításához.

![Képernyőfelvétel: a Power B I vizualizáció az egyes helyszíneken eladott jegyek átlagát mutatja.](./media/saas-tenancy-tenant-analytics/AvgTicketsByVenues.PNG)

Az előző ábrán látható, hogy bár a legtöbb helyszín több mint 80%-át értékesíti a jegyeket, néhányan azért küzd, hogy több mint felet töltsön ki a helyükre. Az egyes helyszínekre eladott jegyek maximális vagy minimális százalékos arányának kiválasztásához az értékekkel is játszhat.

Korábban elmélyíti az elemzést, amely felderíti, hogy a jegyek értékesítései általában előre jelezhető mintákat követnek. Ez a felderítés lehetővé teheti, hogy a Wingtip jegyek a dinamikus díjszabásra vonatkozó javaslatokkal növelhetik a jegyek értékesítését. Ez a felderítés felfedi a lehetőséget, hogy gépi tanulási technikákat alkalmazzon a jegyek értékesítésének előrejelzéséhez az egyes eseményekhez. Előrejelzéseket is el lehet végezni a jegyek értékesítésével járó kedvezményekre gyakorolt hatás tekintetében. Power BI Embedded integrálható egy eseménykezelő alkalmazásba. Az integráció segíthet az előre jelzett eladások megjelenítésében és a különböző kedvezmények hatásán. Az alkalmazás segíthet kialakítani az optimális kedvezményt, amelyet közvetlenül az elemzési kijelzőből alkalmazhat.

Megfigyelte a WingTip alkalmazásban a bérlői adatok tendenciáit. Más módokat is átadhat az alkalmazásnak az SaaS-alkalmazások gyártóinak üzleti döntéseiről. A szállítók jobban ellátják a bérlők igényeit. Remélhetőleg ez az oktatóanyag olyan eszközöket tartalmaz, amelyek szükségesek a bérlői adatok elemzéséhez, hogy a vállalatoknak adatvezérelt döntéseket lehessen hozni.

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

- [Az Wingtip SaaS-alkalmazásra épülő további oktatóanyagok](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials).
- [Rugalmas feladatok](./elastic-jobs-overview.md).
- [Több-bérlős elemzés a kinyert adatszolgáltatások használatával – több-bérlős alkalmazás](./saas-multitenantdb-tenant-analytics.md)
