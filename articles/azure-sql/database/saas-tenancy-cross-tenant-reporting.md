---
title: Lekérdezések jelentése több adatbázis között
description: Több-bérlős jelentéskészítés elosztott lekérdezések használatával.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewers: ''
ms.date: 01/25/2019
ms.openlocfilehash: 18a02b81e459217ccca53d48a08e35a706b071b0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92793262"
---
# <a name="cross-tenant-reporting-using-distributed-queries"></a>Több-bérlős jelentéskészítés elosztott lekérdezések használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben az oktatóanyagban elosztott lekérdezéseket futtat a bérlői adatbázisok teljes készletében a jelentéskészítéshez. Ezek a lekérdezések kinyerhetik a Wingtip jegyek SaaS-bérlői napi működési adatokban kinyert elemzéseket. Ehhez helyezzen üzembe egy további jelentési adatbázist a katalógus-kiszolgálón, és használjon rugalmas lekérdezést az elosztott lekérdezések engedélyezéséhez.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> 
> * Jelentéskészítési adatbázis üzembe helyezése
> * Elosztott lekérdezések futtatása az összes bérlői adatbázisban
> * Az egyes adatbázisok globális nézetei lehetővé teszik a különböző bérlők hatékony lekérdezését


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:


* A Wingtip jegyek SaaS-adatbázisa egy bérlői alkalmazáson van üzembe helyezve. Ha kevesebb, mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip tickets SaaS-adatbázis üzembe helyezése és megismerése bérlői alkalmazásokban](./saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) telepítve van. A SSMS letöltéséhez és telepítéséhez lásd: [SQL Server Management Studio letöltése (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="cross-tenant-reporting-pattern"></a>Több-bérlős jelentéskészítési minta

![több-bérlős elosztott lekérdezési minta](./media/saas-tenancy-cross-tenant-reporting/cross-tenant-distributed-query.png)

Az SaaS-alkalmazásokkal való egyik lehetőség a felhőben tárolt bérlői adatok nagy mennyiségének használata, amellyel betekintést nyerhet az alkalmazás működésére és használatára. Ezek az ismeretek segítik a funkciók fejlesztését, a használhatóság javítását, valamint az alkalmazások és szolgáltatások egyéb befektetéseit.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Az egyik módszer a [rugalmas lekérdezés](elastic-query-overview.md)használata, amely lehetővé teszi a közös sémával rendelkező elosztott adatbázisok lekérdezését. Ezek az adatbázisok különböző erőforráscsoportok és előfizetések között terjeszthetők, de közös bejelentkezési adatokat kell megosztaniuk. A rugalmas lekérdezés egyetlen *fő* adatbázist használ, amelyben külső táblák vannak meghatározva, amelyek az elosztott (bérlői) adatbázisokban lévő táblákat vagy nézeteket tükröznek. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. A rugalmas lekérdezés az összes bérlői adatbázis helyét határozza meg a katalógus-adatbázisban található szegmenses leképezés használatával. A fő adatbázis beállítása és lekérdezése egyszerű a standard [Transact-SQL](/sql/t-sql/language-reference)használatával, és támogatja az olyan eszközök lekérdezését, mint a Power bi és az Excel.

A lekérdezéseknek a bérlői adatbázisokban való terjesztése révén a rugalmas lekérdezés azonnali betekintést nyújt az éles üzemi adataiba. Mivel a rugalmas lekérdezés akár több adatbázisból is lekéri az adatok mennyiségét, a lekérdezési késés magasabb lehet, mint az egyetlen több-bérlős adatbázisba küldött egyenértékű lekérdezések száma. Tervezési lekérdezések a fő adatbázisba visszaadott adatforgalom minimalizálásához. A rugalmas lekérdezés többnyire kis mennyiségű valós idejű adat lekérdezésére alkalmas, a gyakran használt vagy összetett elemzési lekérdezések vagy jelentések kiépítése helyett. Ha a lekérdezések nem jól teljesítenek, tekintse meg a [végrehajtási tervet](/sql/relational-databases/performance/display-an-actual-execution-plan) , és figyelje meg, hogy a lekérdezés mely részét küldi le a rendszer a távoli adatbázisnak, és mennyi adat érkezik vissza. Az összetett összesítést vagy analitikus feldolgozást igénylő lekérdezések jobb kezelése a bérlői adatoknak az elemzési lekérdezésekhez optimalizált adatbázisba vagy adatraktárba való kinyerésével. Ezt a mintát a [bérlői elemzési oktatóanyag](saas-tenancy-tenant-analytics.md)ismerteti. 

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip tickets SaaS-adatbázis beszerzése bérlői alkalmazás parancsfájljai alapján

A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájljai és az alkalmazás forráskódja a [WingtipTicketsSaaS-DbPerTenant GitHub-](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) tárházban érhető el. Tekintse meg az [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets SaaS-parancsfájlok letöltésének és feloldásának lépéseihez.

## <a name="create-ticket-sales-data"></a>Ticket Sales-adatforgalom létrehozása

Ha több érdekes adathalmazon szeretne lekérdezéseket futtatni, a Ticket-Generator futtatásával hozzon létre jegyeladási adatforgalmat.

1. A *POWERSHELL ISE*-ben nyissa meg a... \\ A képzési modulok \\ operatív elemzési ad \\ hoc jelentéskészítési \\ *Demo-AdhocReporting.ps1* parancsfájlt, és a következő értéket adja meg:
   * **$DemoScenario** = 1, **jegyek vásárlása minden helyszínen**.
2. Nyomja le az **F5** billentyűt a szkript futtatásához és a jegyek értékesítésének létrehozásához. A parancsfájl futása közben folytassa az oktatóanyag lépéseit. A jegyeket az *ad hoc elosztott lekérdezések futtatása* szakaszban kérdezi le, ezért várjon, amíg a Ticket Generator befejeződik.

## <a name="explore-the-global-views"></a>A globális nézetek megismerése

A Wingtip jegyek SaaS-adatbázisa bérlői alkalmazásokban minden bérlő egy adatbázist kap. Így az adatbázis tábláiban található adategységek hatóköre egyetlen bérlő perspektívája. Az összes adatbázis lekérdezése során azonban fontos, hogy a rugalmas lekérdezés kezelje az adatkezelést, mintha egyetlen logikai adatbázis része legyen, amelyet a bérlő helyez el. 

Ennek a mintának a szimulálása érdekében "globális" nézetek kerülnek a bérlői adatbázisba, amely a bérlői azonosítót a globálisan lekérdezett táblákba adja. A *VenueEvents* nézet például egy számított *VenueId* hoz létre az *események* táblából kijelzett oszlopokhoz. Hasonlóképpen, a *VenueTicketPurchases* és a *VenueTickets* nézet egy számított *VenueId* oszlopot ad hozzá a saját tábláiból. Ezeket a nézeteket a rugalmas lekérdezés használja a lekérdezések integrálással, és leküldi azokat a megfelelő távoli bérlői adatbázisba, ha van *VenueId* -oszlop. Ez jelentősen csökkenti a visszaadott adat mennyiségét, és számos lekérdezés esetében jelentős növekedést eredményez a teljesítményben. Ezek a globális nézetek az összes bérlői adatbázisban előre létre lettek hozva.

1. Nyissa meg a SSMS, és [kapcsolódjon a tenants1- &lt; User &gt; kiszolgálóhoz](saas-tenancy-wingtip-app-guidance-tips.md#explore-database-schema-and-execute-sql-queries-using-ssms).
1. Bontsa ki az **adatbázisok** csomópontot, kattintson a jobb gombbal a _contosoconcerthall_ elemre, majd válassza az **Új lekérdezés** lehetőséget
1. Futtassa a következő lekérdezéseket az egybérlős táblák és a globális nézetek közötti különbség megismeréséhez:

   ```T-SQL
   -- The base Venue table, that has no VenueId associated.
   SELECT * FROM Venue

   -- Notice the plural name 'Venues'. This view projects a VenueId column.
   SELECT * FROM Venues

   -- The base Events table, which has no VenueId column.
   SELECT * FROM Events

   -- This view projects the VenueId retrieved from the Venues table.
   SELECT * FROM VenueEvents
   ```

Ezekben a nézetekben a *VenueId* a helyszín nevének kivonata alapján számítjuk ki, de bármilyen megközelítés használható egy egyedi érték bevezetésére. Ez a megközelítés hasonló ahhoz, ahogy a bérlői kulcs a katalógusban való használatának kiszámításához szükséges.

A *helyszínek* nézet definíciójának vizsgálata:

1. A **Object Explorer** bontsa ki a **contosoconcerthall**-  >  **nézetek** elemet:

   ![A képernyőkép a nézetek csomópont tartalmát jeleníti meg, beleértve a d b. helyszín négy típusát is.](./media/saas-tenancy-cross-tenant-reporting/views.png)

2. Kattintson a jobb gombbal a dbo elemre **. Helyszínek**.
3. Válassza ki a **parancsfájl nézetet az**  >    >  **Új lekérdezés-szerkesztő ablak** létrehozásához.

Parancsfájl a többi *helyszín* nézeteiből megtudhatja, hogyan adja hozzá a *VenueId*.

## <a name="deploy-the-database-used-for-distributed-queries"></a>Az elosztott lekérdezésekhez használt adatbázis üzembe helyezése

Ez a gyakorlat telepíti a _adhocreporting_ -adatbázist. Ez az a fő adatbázis, amely az összes bérlői adatbázis lekérdezéséhez használt sémát tartalmazza. A rendszer telepíti az adatbázist a meglévő Catalog kiszolgálóra, amely a minta alkalmazásban található összes felügyelettel kapcsolatos adatbázishoz használt kiszolgáló.

1. a *POWERSHELL ISE*-ben nyissa meg a... \\ Oktatási modulok \\ operatív elemzési ad \\ hoc jelentéskészítés \\ *Demo-AdhocReporting.ps1*. 

1. Állítsa be **$DemoScenario = 2**, _ad hoc jelentési adatbázis üzembe helyezése_.

1. Nyomja le az **F5** billentyűt a szkript futtatásához és a *adhocreporting* -adatbázis létrehozásához.

A következő szakaszban sémát ad hozzá az adatbázishoz, hogy az elosztott lekérdezések futtatására is használható legyen.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>A "Head" adatbázis konfigurálása elosztott lekérdezések futtatásához

Ez a gyakorlat hozzáadja a sémát (a külső adatforrást és a külső tábla definícióit) a _adhocreporting_ -adatbázishoz, így lehetővé teszi az összes bérlői adatbázis lekérdezését.

1. Nyissa meg SQL Server Management Studio, és kapcsolódjon az előző lépésben létrehozott ad hoc jelentési adatbázishoz. Az adatbázis neve *adhocreporting*.
2. Nyissa meg a. ..\Learning Modules\Operational Analytics\Adhoc Reporting \ _Initialize-AdhocReportingDB. SQL_ MAPPÁT a SSMS-ben.
3. Tekintse át az SQL-parancsfájlt, és jegyezze fel:

   A rugalmas lekérdezés adatbázis-hatókörű hitelesítő adatokat használ az egyes bérlői adatbázisok eléréséhez. A hitelesítő adatoknak az összes adatbázisban elérhetőnek kell lenniük, és általában a lekérdezések engedélyezéséhez szükséges minimális jogosultságokat kell megadni.

    ![hitelesítő adat létrehozása](./media/saas-tenancy-cross-tenant-reporting/create-credential.png)

   A katalógus-adatbázist külső adatforrásként a rendszer a lekérdezés futtatásakor a katalógusban regisztrált összes adatbázisra terjeszti a lekérdezéseket. Mivel a kiszolgálók nevei eltérőek az egyes központi telepítések esetében, ez a szkript beolvassa a katalógus-adatbázis helyét az aktuális kiszolgálóról (@ @servername ), ahol a parancsfájlt végrehajtja.

    ![külső adatforrás létrehozása](./media/saas-tenancy-cross-tenant-reporting/create-external-data-source.png)

   A külső táblák, amelyek az előző szakaszban ismertetett globális nézetekre hivatkoznak, és amelyek a következővel vannak meghatározva: **Distributed = VenueId**. Mivel minden egyes *VenueId* egy különálló adatbázishoz van hozzárendelve, ez számos forgatókönyv esetén javítja a teljesítményt, ahogy azt a következő szakaszban is látható.

    ![külső táblák létrehozása](./media/saas-tenancy-cross-tenant-reporting/external-tables.png)

   A létrehozott és feltöltött helyi tábla _VenueTypes_ . Ez a hivatkozási adattábla az összes bérlői adatbázisban közös, így helyi táblaként is képviselteti magát, és a közös adatokkal tölthető fel. Néhány lekérdezés esetében, ha a tábla a főadatbázisban van definiálva, csökkentheti a fő adatbázisba áthelyezni kívánt adatmennyiséget.

    ![tábla létrehozása](./media/saas-tenancy-cross-tenant-reporting/create-table.png)

   Ha ilyen módon tartalmaz hivatkozási táblákat, a bérlői adatbázisok frissítésekor mindenképpen frissítse a táblázat sémáját és az adatforrást.

4. Nyomja le az **F5** billentyűt a szkript futtatásához és a *adhocreporting* -adatbázis inicializálásához. 

Most már futtathatja az elosztott lekérdezéseket, és információkat gyűjthet az összes bérlőről.

## <a name="run-distributed-queries"></a>Elosztott lekérdezések futtatása

Most, hogy beállította a *adhocreporting* -adatbázist, ugorjon előre, és futtasson néhány elosztott lekérdezést. Adja meg a végrehajtási tervet, hogy jobban megértse a lekérdezés feldolgozásának helyét. 

A végrehajtási terv vizsgálatakor vigye a kurzort a terv ikonjaira a részletekért. 

Fontos megjegyezni, hogy ha a külső adatforrás meghatározásakor a **Distributed (VenueId)** beállítás van megadva, a teljesítmény számos forgatókönyv esetén javul. Mivel az egyes *VenueId* egy adott adatbázishoz vannak leképezve, a szűrés egyszerűen elvégezhető távolról, csak a szükséges adatértékek visszaadása.

1. Megnyitás.. \\ . Oktatási modulok \\ Operational Analytics ad \\ hoc jelentéskészítés \\ *demo-ADHOCREPORTINGQUERIES. SQL* a SSMS-ben.
2. Győződjön meg arról, hogy csatlakozik a **adhocreporting** -adatbázishoz.
3. Válassza a **lekérdezés** menüt, majd kattintson a **tényleges végrehajtási terv belefoglalása** elemre.
4. Jelölje ki a *jelenleg regisztrált helyszíneket?* lekérdezés, és nyomja le az **F5** billentyűt.

   A lekérdezés a teljes helyszín listát adja vissza, amely bemutatja, hogy milyen gyorsan és könnyen lehet lekérdezni az összes bérlőt, és visszaadni az egyes bérlők adatait.

   Vizsgálja meg a tervet, és ellenőrizze, hogy a teljes díj a távoli lekérdezésben van-e. Minden bérlői adatbázis távolról hajtja végre a lekérdezést, és visszaadja a hely adatait a fő adatbázisnak.

   ![Válassza a * elemet a dbo. Helyszínek](./media/saas-tenancy-cross-tenant-reporting/query1-plan.png)

5. Válassza ki a következő lekérdezést, és nyomja le az **F5** billentyűt.

   Ez a lekérdezés a bérlői adatbázisokból és a helyi *VenueTypes* táblából (helyi, a *adhocreporting* -adatbázis egyik táblájából) csatlakozik az adatokhoz.

   Vizsgálja meg a tervet, és tekintse meg, hogy a távoli lekérdezés a Cost nagy része. Minden bérlői adatbázis visszaadja a saját illetékességi adatait, és helyi illesztést végez a helyi *VenueTypes* táblával a felhasználóbarát név megjelenítéséhez.

   ![Csatlakozás távoli és helyi adatszolgáltatásokhoz](./media/saas-tenancy-cross-tenant-reporting/query2-plan.png)

6. Most válassza ki, hogy *melyik napon volt a legtöbb eladott jegy?* lekérdezés, és nyomja le az **F5** billentyűt.

   Ez a lekérdezés egy kicsit összetettebb csatlakozást és összesítést végez. A feldolgozás nagy része távolról történik.  A főadatbázisnak csak egyetlen sort kell visszaadnia, amely minden egyes helyszín napi jegyek eladásának számát tartalmazza.

   ![lekérdezés](./media/saas-tenancy-cross-tenant-reporting/query3-plan.png)


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> 
> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * Helyezzen üzembe egy jelentéskészítési adatbázist, és határozza meg az elosztott lekérdezések futtatásához szükséges sémát.


Most próbálja ki a [bérlői elemzési oktatóanyagot](saas-tenancy-tenant-analytics.md) , és ismerkedjen meg az adatok egy külön elemzési adatbázisba való kinyerésével az összetettebb analitikai feldolgozás érdekében.

## <a name="additional-resources"></a>További források

* További [oktatóanyagok, amelyek a Wingtip tickets SaaS-adatbázisra épülnek a bérlői alkalmazások esetében](./saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Rugalmas lekérdezés](elastic-query-overview.md)