---
title: Ad hoc jelentéskészítési lekérdezések több adatbázis között
description: A több-bérlős alkalmazásokban például több Azure SQL-adatbázisban is futtathat ad hoc jelentéskészítési lekérdezéseket.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/30/2018
ms.openlocfilehash: 800592b7a8b263fea2883fdd3e030f78f72647dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96459919"
---
# <a name="run-ad-hoc-analytics-queries-across-multiple-databases-azure-sql-database"></a>Alkalmi elemzési lekérdezések futtatása több adatbázison keresztül (Azure SQL Database)
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben az oktatóanyagban elosztott lekérdezéseket futtat a bérlői adatbázisok teljes készletében az ad hoc interaktív jelentéskészítés engedélyezéséhez. Ezek a lekérdezések kinyerhetik a Wingtip tickets SaaS-alkalmazás napi működési adatokban kinyert elemzéseit. A kivonások elvégzéséhez egy további analitikai adatbázist kell üzembe helyeznie a katalógus-kiszolgálón, és rugalmas lekérdezést kell használnia az elosztott lekérdezések engedélyezéséhez.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> 
> * Ad hoc jelentési adatbázis üzembe helyezése
> * Elosztott lekérdezések futtatása az összes bérlői adatbázisban


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás telepítve van. Ha kevesebb mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése és megismerése](saas-multitenantdb-get-started-deploy.md) című részt
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps)
* SQL Server Management Studio (SSMS) telepítve van. A SSMS letöltéséhez és telepítéséhez lásd: [SQL Server Management Studio letöltése (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms).


## <a name="ad-hoc-reporting-pattern"></a>Alkalmi jelentéskészítési minta

![alkalmi jelentéskészítési minta](./media/saas-multitenantdb-adhoc-reporting/adhocreportingpattern_shardedmultitenantDB.png)

Az SaaS-alkalmazások a felhőben központilag tárolt bérlői adatmennyiséget elemezni tudják. Az elemzések feltárják az alkalmazás működésével és használatával kapcsolatos információkat. Ezek az ismeretek segítik a funkciók fejlesztését, a használhatóság javítását, valamint az alkalmazások és szolgáltatások egyéb befektetéseit.

Ezeknek az adatoknak egyetlen több bérlős adatbázisban történő elérése könnyű, de nem olyan egyszerű, ha méretezve akár több ezer adatbázis között vannak elosztva. Az egyik módszer a [rugalmas lekérdezés](elastic-query-overview.md)használata, amely lehetővé teszi a közös sémával rendelkező elosztott adatbázisok lekérdezését. Ezek az adatbázisok különböző erőforráscsoportok és előfizetések között terjeszthetők. Ugyanakkor az egyik gyakori bejelentkezésnek hozzáféréssel kell rendelkeznie az adatoknak az összes adatbázisból való kinyeréséhez. A rugalmas lekérdezés egyetlen *fő* adatbázist használ, amelyben külső táblák vannak meghatározva, amelyek az elosztott (bérlői) adatbázisokban lévő táblákat vagy nézeteket tükröznek. Az ebbe a központi adatbázisba küldött lekérdezések le lesznek fordítva elosztott lekérdezési terv készítéséhez, a lekérdezés részei pedig igény szerint le lesznek küldve a bérlői adatbázisokba. A rugalmas lekérdezés az összes bérlői adatbázis helyét határozza meg a katalógus-adatbázisban található szegmenses leképezés használatával. A telepítő és a lekérdezés a standard [Transact-SQL](/sql/t-sql/language-reference)használatával egyszerű, és támogatja az olyan eszközöktől származó ad hoc lekérdezéseket, mint a Power bi és az Excel.

A lekérdezéseknek a bérlői adatbázisokban való terjesztése révén a rugalmas lekérdezés azonnali betekintést nyújt az éles üzemi adataiba. Mivel azonban a rugalmas lekérdezés akár több adatbázisból is lekéri az adatok mennyiségét, a lekérdezés késése esetenként magasabb lehet, mint az egyetlen több-bérlős adatbázisba küldött egyenértékű lekérdezések esetében. Ügyeljen arra, hogy a visszaadott adatforgalom minimalizálására szolgáló lekérdezéseket tervezzen. A rugalmas lekérdezés többnyire kis mennyiségű valós idejű adat lekérdezésére alkalmas, a gyakran használt vagy összetett elemzési lekérdezések vagy jelentések kiépítése helyett. Ha a lekérdezések nem jól teljesítenek, tekintse meg a [végrehajtási tervet](/sql/relational-databases/performance/display-an-actual-execution-plan) , és tekintse meg, hogy a lekérdezés milyen része lett leküldve a távoli adatbázisra. És mérje fel, hogy mennyi adattal tért vissza a rendszer. Az összetett analitikus feldolgozást igénylő lekérdezések jobb kiszolgálása a kinyert bérlői adatoknak az elemzési lekérdezésekhez optimalizált adatbázisba való mentésekor lehet. Az SQL Database és az Azure szinapszis Analytics egy ilyen elemzési adatbázist üzemeltet.

Az elemzések mintáját a [bérlői elemzési oktatóanyag](saas-multitenantdb-tenant-analytics.md)ismerteti.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás forráskódjának és parancsfájljainak beszerzése

A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájljai és az alkalmazás forráskódja a [WingtipTicketsSaaS-MultitenantDB GitHub-](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) tárházban érhető el. Tekintse meg az [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets SaaS-parancsfájlok letöltésének és feloldásának lépéseihez.

## <a name="create-ticket-sales-data"></a>Ticket Sales-adatforgalom létrehozása

Ha több érdekes adathalmazon szeretne lekérdezéseket futtatni, a Ticket-Generator futtatásával hozzon létre jegyeladási adatforgalmat.

1. A *POWERSHELL ISE*-ben nyissa meg a... \\ A képzési modulok \\ operatív elemzési ad \\ hoc jelentéskészítés \\ *Demo-AdhocReporting.ps1* parancsfájlt, és a következő értékeket adja meg:
   * **$DemoScenario** = 1, **jegyek vásárlása minden helyszínen**.
2. Nyomja le az **F5** billentyűt a szkript futtatásához és a jegyek értékesítésének létrehozásához. A parancsfájl futása közben folytassa az oktatóanyag lépéseit. A jegyeket az *ad hoc elosztott lekérdezések futtatása* szakaszban kérdezi le, ezért várjon, amíg a Ticket Generator befejeződik.

## <a name="explore-the-tenant-tables"></a>A bérlői táblák megismerése 

A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazásban a bérlők egy hibrid bérlői felügyeleti modellben vannak tárolva, ahol a bérlői adategységeket egy több-bérlős adatbázisban vagy egyetlen bérlői adatbázisban tárolják, és a kettő között lehet áthelyezni. Az összes bérlői adatbázis lekérdezése esetén fontos, hogy a rugalmas lekérdezés kezelje az adatmennyiséget, mintha egy, a bérlő által elválasztott logikai adatbázis része legyen. 

Ennek a mintának az eléréséhez minden bérlői tábla tartalmaz egy *VenueId* oszlopot, amely meghatározza, hogy melyik bérlőhöz tartozik az adat. A *VenueId* a hely nevének kivonata alapján számítja ki a rendszer, de bármilyen módszert felhasználhat az oszlop egyedi értékének bevezetésére. Ez a megközelítés hasonló ahhoz, ahogy a bérlői kulcs a katalógusban való használatának kiszámításához szükséges. A *VenueId* tartalmazó táblákat a rugalmas lekérdezés használja a lekérdezések integrálással, és leküldi azokat a megfelelő távoli bérlői adatbázisba. Ez jelentősen csökkenti a visszaadott adat mennyiségét, és növeli a teljesítményt, különösen akkor, ha több bérlő is van, amelyek adatai egyetlen bérlői adatbázisban vannak tárolva.

## <a name="deploy-the-database-used-for-ad-hoc-distributed-queries"></a>Az ad hoc elosztott lekérdezésekhez használt adatbázis üzembe helyezése

Ez a gyakorlat telepíti a *adhocreporting* -adatbázist. Ez az a fő adatbázis, amely az összes bérlői adatbázis lekérdezéséhez használt sémát tartalmazza. A rendszer telepíti az adatbázist a meglévő Catalog kiszolgálóra, amely a minta alkalmazásban található összes felügyelettel kapcsolatos adatbázishoz használt kiszolgáló.

1. Megnyitás.. \\ . Képzési modulok \\ operatív elemzési ad \\ hoc jelentéskészítés \\ *Demo-AdhocReporting.ps1* a *PowerShell ISE* -ben, és állítsa be a következő értékeket:
   * **$DemoScenario** = 2, **ad hoc elemzési adatbázis üzembe helyezése**.

2. Nyomja le az **F5** billentyűt a szkript futtatásához és a *adhocreporting* -adatbázis létrehozásához.

A következő szakaszban sémát ad hozzá az adatbázishoz, hogy az elosztott lekérdezések futtatására is használható legyen.

## <a name="configure-the-head-database-for-running-distributed-queries"></a>A "Head" adatbázis konfigurálása elosztott lekérdezések futtatásához

Ez a gyakorlat hozzáadja a sémát (a külső adatforrást és a külső tábla definícióit) az ad hoc jelentési adatbázishoz, amely lehetővé teszi az összes bérlői adatbázis lekérdezését.

1. Nyissa meg SQL Server Management Studio, és kapcsolódjon az előző lépésben létrehozott ad hoc jelentési adatbázishoz. Az adatbázis neve *adhocreporting*.
2. Nyissa meg a. ..\Learning Modules\Operational Analytics\Adhoc Reporting \ *Initialize-AdhocReportingDB. SQL* MAPPÁT a SSMS-ben.
3. Tekintse át az SQL-parancsfájlt, és vegye figyelembe a következőket:

   A rugalmas lekérdezés adatbázis-hatókörű hitelesítő adatokat használ az egyes bérlői adatbázisok eléréséhez. Ezt a hitelesítő adatot az összes adatbázisban elérhetőnek kell lennie, és általában meg kell adni az ad hoc lekérdezések engedélyezéséhez szükséges minimális jogokat.

    ![hitelesítő adat létrehozása](./media/saas-multitenantdb-adhoc-reporting/create-credential.png)

   Ha a katalógus-adatbázist külső adatforrásként használja, a rendszer a lekérdezés futtatásakor a katalógusban regisztrált összes adatbázisra terjeszti a lekérdezéseket. Mivel a kiszolgálók nevei eltérőek az egyes központi telepítések esetében, ez az inicializálási parancsfájl lekéri a katalógus-adatbázis helyét az aktuális kiszolgáló (@) beolvasásával, @servername ahol a parancsfájlt végrehajtja.

    ![külső adatforrás létrehozása](./media/saas-multitenantdb-adhoc-reporting/create-external-data-source.png)

   A bérlői táblákra hivatkozó külső táblák eloszlással vannak definiálva **(VenueId)**. Ez egy adott *VenueId* egy lekérdezését irányítja a megfelelő adatbázishoz, és számos forgatókönyv esetén javítja a teljesítményt a következő szakaszban látható módon.

    ![külső táblák létrehozása](./media/saas-multitenantdb-adhoc-reporting/external-tables.png)

   A létrehozott és feltöltött helyi tábla *VenueTypes* . Ez a hivatkozási adattábla az összes bérlői adatbázisban közös, így helyi táblaként is képviselteti magát, és a közös adatokkal tölthető fel. Egyes lekérdezések esetében ez csökkentheti a bérlői adatbázisok és a *adhocreporting* -adatbázis között áthelyezett adatmennyiséget.

    ![tábla létrehozása](./media/saas-multitenantdb-adhoc-reporting/create-table.png)

   Ha ilyen módon tartalmaz hivatkozási táblákat, a bérlői adatbázisok frissítésekor mindenképpen frissítse a táblázat sémáját és az adatforrást.

4. Nyomja le az **F5** billentyűt a szkript futtatásához és a *adhocreporting* -adatbázis inicializálásához. 

Most már futtathatja az elosztott lekérdezéseket, és információkat gyűjthet az összes bérlőről.

## <a name="run-ad-hoc-distributed-queries"></a>Ad hoc elosztott lekérdezések futtatása

Most, hogy beállította a *adhocreporting* -adatbázist, ugorjon előre, és futtasson néhány elosztott lekérdezést. Adja meg a végrehajtási tervet, hogy jobban megértse a lekérdezés feldolgozásának helyét. 

A végrehajtási terv vizsgálatakor vigye a kurzort a terv ikonjaira a részletekért. 

1. A *SSMS* nyissa meg a... \\ Oktatási modulok \\ operatív elemzési ad \\ hoc jelentéskészítés \\ *demo-AdhocReportingQueries. SQL*.
2. Győződjön meg arról, hogy csatlakozik a **adhocreporting** -adatbázishoz.
3. Válassza a **lekérdezés** menüt, majd kattintson a **tényleges végrehajtási terv belefoglalása** elemre.
4. Jelölje ki a *jelenleg regisztrált helyszíneket?* lekérdezés, és nyomja le az **F5** billentyűt.

   A lekérdezés a teljes helyszín listáját adja vissza, amely azt szemlélteti, hogy milyen gyorsan és egyszerűen lehet lekérdezni az összes bérlőt, és visszaadni az egyes bérlők adatait.

   Vizsgálja meg a tervet, és tekintse meg, hogy a teljes díj a távoli lekérdezés, mert egyszerűen minden bérlői adatbázisra megyünk, és kiválasztja a helyszín információit.

   ![Válassza a * elemet a dbo. Helyszínek](./media/saas-multitenantdb-adhoc-reporting/query1-plan.png)

5. Válassza ki a következő lekérdezést, és nyomja le az **F5** billentyűt.

   Ez a lekérdezés a bérlői adatbázisokból és a helyi *VenueTypes* táblából (helyi, a *adhocreporting* -adatbázis egyik táblájából) csatlakozik az adatokhoz.

   Vizsgálja meg a tervet, és tekintse meg, hogy a legtöbb Cost a távoli lekérdezés, mert az egyes bérlők illetékességi adatait (dbo) kérdezi le. Helyszíneken), majd végezzen el egy gyors helyi csatlakozást a helyi *VenueTypes* táblával a rövid név megjelenítéséhez.

   ![Csatlakozás távoli és helyi adatszolgáltatásokhoz](./media/saas-multitenantdb-adhoc-reporting/query2-plan.png)

6. Most válassza ki, hogy *melyik napon volt a legtöbb eladott jegy?* lekérdezés, és nyomja le az **F5** billentyűt.

   Ez a lekérdezés egy kicsit összetettebb csatlakozást és összesítést végez. Fontos megjegyezni, hogy a feldolgozás nagy része távolról történik, és ismét csak a szükséges sorokat vesszük vissza, és csak egyetlen sort adunk vissza minden helyszín összesített jegyek eladásának napi száma alapján.

   ![lekérdezés](./media/saas-multitenantdb-adhoc-reporting/query3-plan.png)


## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> 
> * Elosztott lekérdezések futtatása az összes bérlői adatbázison
> * Helyezzen üzembe egy ad hoc jelentéskészítési adatbázist, és adjon hozzá sémát az elosztott lekérdezések futtatásához.

Most próbálja ki a [bérlői elemzési oktatóanyagot](saas-multitenantdb-tenant-analytics.md) , és ismerkedjen meg az adatok egy külön elemzési adatbázisba való kinyerésével az összetettebb analitikai feldolgozás érdekében.

## <a name="additional-resources"></a>További források

<!-- ??
* Additional [tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
-->

* [Rugalmas lekérdezés](elastic-query-overview.md)