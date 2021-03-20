---
title: Több-bérlős adatbázis SaaS-alkalmazás üzembe helyezése
description: Üzembe helyezheti és felfedezheti a több-bérlős SaaS-alapú Wingtip-jegyeket, amelyek Azure SQL Database használatával mutatják be az SaaS-mintákat.
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 10/16/2018
ms.openlocfilehash: 4de7e428bff0feaafdec00b0c0014bbaf6acb917
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92790967"
---
# <a name="deploy-and-explore-a-sharded-multi-tenant-application"></a>Horizontálisan több-bérlős alkalmazás üzembe helyezése és megismerése
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben az oktatóanyagban egy Wingtip tickets nevű, több-bérlős SaaS-alkalmazást helyez üzembe és vizsgál. A Wingtip tickets alkalmazás az SaaS-forgatókönyvek megvalósítását megkönnyítő Azure SQL Database funkcióinak bemutatására szolgál.

A Wingtip tickets alkalmazás ezen implementációja egy több-bérlős adatbázis-mintát használ. A horizontális skálázás a bérlő azonosítója. A bérlői adatok a bérlői azonosító értékeinek megfelelően oszlanak meg egy adott adatbázison. 

Ez az adatbázis-minta lehetővé teszi, hogy egy vagy több bérlőt tároljon az egyes szegmensekben vagy adatbázisokban. Optimalizálhatja a legalacsonyabb költségeket azzal, hogy az egyes adatbázisokat több bérlő is megosztja. Vagy optimalizálhatja az elkülönítést úgy, hogy az egyes adatbázisok csak egy bérlőt tárolnak. Az optimalizálási választás az egyes bérlők esetében egymástól függetlenül is elvégezhető. Ön dönthet úgy, hogy a bérlőt először tárolja, vagy később is megváltoztathatja. Az alkalmazás úgy van kialakítva, hogy megfelelően működjön.

## <a name="app-deploys-quickly"></a>Az alkalmazás üzembe helyezése gyorsan

Az alkalmazás az Azure-felhőben fut, és Azure SQL Databaset használ. A következő üzembe helyezési szakasz a kék **üzembe helyezését ismerteti az Azure-** ban gomb. A gomb megnyomásakor az alkalmazás öt percen belül teljesen üzembe lesz helyezve az Azure-előfizetésben. Teljes hozzáféréssel rendelkezik az egyes alkalmazás-összetevőkkel való munkához.

Az alkalmazás három minta bérlőre vonatkozó adattal van üzembe helyezve. A bérlők együtt tárolódnak egy több-bérlős adatbázisban.

Az Wingtip-jegyek C#-és PowerShell-forráskódját bárki letöltheti a [GitHub-adattárból][link-github-wingtip-multitenantdb-55g].

## <a name="learn-in-this-tutorial"></a>Az oktatóanyag megismerése

> [!div class="checklist"]
> - A Wingtip tickets SaaS-alkalmazás üzembe helyezése.
> - Az alkalmazás forráskódjának és felügyeleti parancsfájljainak beszerzése.
> - Az alkalmazást alkotó kiszolgálók és adatbázisok ismertetése.
> - Hogyan vannak leképezve a bérlők az *adatkatalógushoz*.
> - Új bérlő kiépítése.
> - A bérlői tevékenység figyelése az alkalmazásban.

Ezen kezdeti üzembe helyezésre épülő kapcsolódó oktatóanyagok sorozata is elérhető. Az oktatóanyagok számos SaaS-tervezési és-felügyeleti mintát mutatnak be. Ha az oktatóanyagokon keresztül dolgozik, javasoljuk, hogy a különböző SaaS-minták megvalósítási módjának megismeréséhez lépjen be a megadott parancsfájlokba.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

- A legújabb Azure PowerShell telepítve van. Részletekért lásd: [a Azure PowerShell első lépései][link-azure-get-started-powershell-41q].

## <a name="deploy-the-wingtip-tickets-app"></a>A Wingtip tickets alkalmazás üzembe helyezése

### <a name="plan-the-names"></a>A nevek megtervezése

A szakasz lépéseiben olyan *felhasználói* értéket ad meg, amely az erőforrások nevének globálisan egyedivé tételét, valamint az alkalmazás központi telepítése által létrehozott összes erőforrást tartalmazó *erőforráscsoport* nevét tartalmazza. Egy *Ann Finley* nevű személy esetében a következőket javasoljuk:
- *User:* **AF1**  *(a monogramjuk és egy számjegy. Ha Másodszor telepíti az alkalmazást, használjon más értéket (például af2).)*
- *Erőforráscsoport:* **Wingtip-MT-AF1** *(Wingtip-MT) azt jelzi, hogy ez a több-bérlős, több-bérlős alkalmazás. a Felhasználónév hozzáfűzésével a AF1 összekapcsolja az erőforráscsoport nevét és a benne található erőforrások nevét.)*

Válassza ki a neveket, majd írja le őket. 

### <a name="steps"></a>Lépések

1. Kattintson a következő kék **üzembe helyezés az Azure-** ban gombra.
   - Ekkor megnyílik a Azure Portal a Wingtip tickets SaaS telepítési sablonnal.

     [![Az Azure-ban való üzembe helyezésre szolgáló gomb.][image-deploy-to-azure-blue-48d]][link-aka-ms-deploywtp-mtapp-52k]

1. Adja meg a központi telepítés szükséges paraméter-értékeit.

    > [!IMPORTANT]
    > Ebben a bemutatóban ne használjon már meglévő erőforráscsoportokat, kiszolgálókat vagy készleteket. Ehelyett válassza **az új erőforráscsoport létrehozása** lehetőséget. Ha végzett az alkalmazással, törölje a létrehozott erőforráscsoportot a kapcsolódó számlázások leállításához.
    > Ne használja az alkalmazást vagy az általa létrehozott erőforrásokat éles környezetben. A hitelesítés bizonyos szempontjai és a kiszolgáló tűzfal beállításai szándékosan nem biztonságosak az alkalmazásban a bemutató elősegítése érdekében.

    - **Erőforráscsoport** – válassza az **új létrehozása** lehetőséget, majd adja meg az erőforráscsoport **nevét** (kis-és nagybetűk megkülönböztetése).
        - Válasszon ki egy **helyet** a legördülő listából.
    - **Felhasználó** számára – javasoljuk, hogy válasszon egy rövid **felhasználói** értéket.

1. **Telepítse az alkalmazást**.

    - Kattintson ide a feltételek és kikötések elfogadásához.
    - Kattintson a **Purchase** (Vásárlás) gombra.

1. A telepítés állapotának figyeléséhez kattintson az **értesítések** elemre, amely a keresőmező jobb oldalán található harang ikon. A Wingtip alkalmazás üzembe helyezése körülbelül öt percet vesz igénybe.

   ![üzembe helyezés sikeres](./media/saas-multitenantdb-get-started-deploy/succeeded.png)

## <a name="download-and-unblock-the-management-scripts"></a>A felügyeleti parancsfájlok letöltése és feloldása

Az alkalmazás központi telepítése közben töltse le az alkalmazás forráskódját és a felügyeleti parancsfájlokat.

> [!NOTE]
> A Windows letilthatja a végrehajtható tartalmat (parancsfájlokat, DLL-eket), ha a ZIP-fájlokat külső forrásból tölti le, és kinyeri. A parancsfájlok zip-fájlból való kibontásakor a következő lépésekkel oldja fel a. zip fájlt a kicsomagolás előtt. A. zip fájl blokkolásának feloldásával gondoskodhat arról, hogy a parancsfájlok futtatása engedélyezett legyen.

1. Tallózással keresse meg [a WingtipTicketsSaaS-MultiTenantDb GitHub](https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDb)-tárházat.
2. Kattintson a **klónozás vagy a letöltés** elemre.
3. Kattintson a **zip letöltése** elemre, és mentse a fájlt.
4. Kattintson a jobb gombbal a **WingtipTicketsSaaS-MultiTenantDb-master.zip** fájlra, majd válassza a **Tulajdonságok** lehetőséget.
5. Az **általános** lapon válassza a **Tiltás feloldása** lehetőséget, majd kattintson az **alkalmaz** gombra.
6. Kattintson az **OK** gombra.
7. Bontsa ki a fájlokat.

A parancsfájlok a *.. \\ WingtipTicketsSaaS-MultiTenantDb – fő \\ tanulási modulok \\* mappája.

## <a name="update-the-configuration-file-for-this-deployment"></a>A központi telepítés konfigurációs fájljának frissítése

A parancsfájlok futtatása előtt állítsa be az *erőforráscsoportot* és a *felhasználói* értékeket a **userconfig. psm1**. Állítsa be ezeket a változókat az üzembe helyezés során beállított értékekre.

1. Megnyitás.. \\ . Learning-modulok \\ *userconfig. Psm1* a *PowerShell ISE*-ben.
2. Frissítse a *ResourceGroupName* és a *nevet* az üzemelő példány megadott értékeivel (csak a 10. és a 11. sorban).
3. Mentse a módosításokat.

Az ebben a fájlban beállított értékeket az összes parancsfájl használja, ezért fontos, hogy pontosak legyenek. Ha újratelepíti az alkalmazást, különböző értékeket kell választania a felhasználói és az erőforráscsoport számára. Ezután frissítse a UserConfig. psm1 fájlt az új értékekkel.

## <a name="run-the-application"></a>Az alkalmazás futtatása

A Wingtip alkalmazásban a bérlők a helyszínek. A helyszín lehet a koncertterem, a sportcsarnok vagy bármely más, az eseményeket futtató hely. A helyszínek regisztrálva vannak a Wingtip ügyfélként, és minden egyes helyszínhez létrejön egy bérlői azonosító. Minden egyes helyszín felsorolja a közelgő eseményeit a Wingtip-ben, így a nyilvános jegyeket vásárolhat az eseményekhez.

Minden helyszín személyre szabott webalkalmazást kap az események listázásához és jegyek eladásához. Az egyes webalkalmazások függetlenek és elkülönítettek a többi bérlőtől. A Azure SQL Database belül az egyes bérlők összes adathalmaza egy több-bérlős, több-bérlős adatbázisban van tárolva, alapértelmezés szerint. Minden adat a bérlői azonosítóval van címkézve.

A központi **esemény központ** weboldala felsorolja az adott üzemelő példányban található bérlők hivatkozásait. A következő lépésekkel megtapasztalhatja az **Event hub** weboldalát és egy egyéni webalkalmazást:

1. Nyissa meg az **Events hubot** a böngészőben:
   - http://events.wingtip-mt.&lt; User &gt; . trafficmanager.net &nbsp; *( &lt; a felhasználót cserélje le &gt; az üzembe helyezés felhasználói értékére)*

     ![eseményközpont](./media/saas-multitenantdb-get-started-deploy/events-hub.png)

2. Kattintson a **Fabrikam Jazz Club** elemre az **eseményközpontban**.

   ![Események](./media/saas-multitenantdb-get-started-deploy/fabrikam.png)

### <a name="azure-traffic-manager"></a>Azure Traffic Manager

A bejövő kérések eloszlásának szabályozásához a Wingtip alkalmazás az [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)-t használja. Az egyes bérlők eseményeinek lapja tartalmazza a bérlő nevét az URL-címében. Minden URL-cím tartalmazza az adott felhasználói értéket is. Az egyes URL-címek a következő lépések alapján engedelmeskednek a megjelenített formátumnak:

- http://events.wingtip-mt.&lt; User &gt; . trafficmanager.net/*fabrikamjazzclub*

1. Az Events alkalmazás elemzi a bérlő nevét az URL-címről. A bérlő nevét az előző példában szereplő URL-cím *fabrikamjazzclub* .
2. Az alkalmazás ezután elkészíti a bérlő nevét egy olyan kulcs létrehozásához, amellyel hozzáfér egy katalógushoz a szegmenses [hozzárendelések kezelése](elastic-scale-shard-map-management.md)használatával.
3. Az alkalmazás megkeresi a kulcsot a katalógusban, és beolvassa a bérlő adatbázisának megfelelő helyét.
4. Az alkalmazás a Location info használatával megkeresi és hozzáfér a bérlő összes adatát tartalmazó adatbázishoz.

### <a name="events-hub"></a>Events hub

1. Az **Events hub** felsorolja a katalógusban regisztrált összes bérlőt és azok helyszíneit.
2. Az **Events hub** kiterjesztett metaadatokat használ a katalógusban, hogy lekérje az egyes hozzárendelésekhez társított bérlő nevét az URL-címek létrehozásához.

Éles környezetben általában létrehoz egy CNAME DNS-rekordot, amely a [vállalati internetes tartományt](../../traffic-manager/traffic-manager-point-internet-domain.md) a Traffic Manager-profilra irányítja.

## <a name="start-generating-load-on-the-tenant-databases"></a>A bérlői adatbázisok terhelésének megkezdése

Most, hogy üzembe helyezte az alkalmazást, nézzük a munkát! A *demo-LoadGenerator PowerShell-* szkript elindítja az egyes bérlők számára futó számítási feladatokat. A sok SaaS-alkalmazás valós terhelése általában szórványos és kiszámíthatatlan. Az ilyen típusú terhelés szimulálása érdekében a generátor az összes bérlőre kiterjedő terhelést hoz létre. A terhelés véletlenszerű kitöréseket tartalmaz az egyes bérlők véletlenszerűen megjelenő időközönként. A terhelési minta megjelenése több percet vesz igénybe, így a generátor a terhelés monitorozása előtt legalább három-négy percig futtatható.

1. A *POWERSHELL ISE*-ben nyissa meg a... \\ Tanulási modulok \\ segédprogramjai \\ *Demo-LoadGenerator.ps1* szkript.
2. Nyomja le az **F5** billentyűt a szkript futtatásához és a terhelésgenerátor indításához (egyelőre nem módosítsa az alapértelmezett paraméterértékeket).

A *Demo-LoadGenerator.ps1* parancsfájl egy másik PowerShell-munkamenetet nyit meg, amelyen a Load Generator fut. A betöltési folyamat ebben a munkamenetben olyan előtéri feladatként fut, amely a háttérbeli terhelés-generálási feladatokat hívja meg, egyet az egyes bérlők számára.

Az előtérbeli feladat elindítása után a feladat-Meghívási állapotban marad. A feladat további háttérben futó feladatokat indít el minden olyan új bérlő esetében, amelyet később kiépítenek.

A PowerShell-munkamenet bezárása leállítja az összes feladatot.

Előfordulhat, hogy újra kell indítania a Load Generator-munkamenetet, hogy más paramétereket használjon. Ha igen, zárjuk be a PowerShell-létrehozási munkamenetet, majd futtassa újra a *Demo-LoadGenerator.ps1*.

## <a name="provision-a-new-tenant-into-the-sharded-database"></a>Új bérlő kiépítése a szilánkokra osztott adatbázisba

A kezdeti üzembe helyezés három minta bérlőt tartalmaz a *Tenants1* -adatbázisban. Hozzunk létre egy másik bérlőt, és figyeljük meg az üzembe helyezett alkalmazás hatásait. Ebben a lépésben egy kulcsot egy új bérlő létrehozásához kell megnyomnia:

1. Megnyitás.. \\ . A tanulási modulok \\ \\ a *PowerShell ISE*-ben Kiépítik és katalogizálják *Demo-ProvisionTenants.ps1* .
2. A szkript futtatásához nyomja le az **F5** billentyűt (nem **F8**) (most hagyja meg az alapértelmezett értékeket).

   > [!NOTE]
   > A PowerShell-szkripteket csak az **F5** billentyű lenyomásával, az **F8** billentyű lenyomásával futtatva futtassa a parancsfájl kiválasztott részét. Az **F8** -as probléma az, hogy a *$PSScriptRoot* változó nincs kiértékelve. Ezt a változót számos parancsfájl igényli a mappák navigálása, más parancsfájlok meghívása vagy importálási modulok esetén.

Az új Red Maple Racing-bérlő hozzá van adva a *Tenants1* -adatbázishoz, és regisztrálva van a katalógusban. Megnyílik az új bérlő jegy-értékesítési **eseményeinek** helye a böngészőben:

![Új bérlő](./media/saas-multitenantdb-get-started-deploy/red-maple-racing.png)

Frissítse az **Events hubot**, és az új bérlő most megjelenik a listában.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Új bérlő kiépítése a saját adatbázisában

A felosztott több-bérlős modell lehetővé teszi, hogy új bérlőt építsen ki egy olyan adatbázisba, amely más bérlőket tartalmaz, vagy egy saját adatbázisba. A saját adatbázisában elkülönített bérlő a következő előnyöket nyújtja:

- A bérlői adatbázis teljesítménye úgy kezelhető, hogy nem kell más bérlők igényeihez kötni.
- Ha szükséges, az adatbázist visszaállíthatja egy korábbi időpontra, mert más bérlők nem lesznek érintettek.

Dönthet úgy, hogy ingyenes próbaverziós ügyfeleket vagy gazdaságbeli ügyfeleket helyez el több-bérlős adatbázisokba. Mindegyik prémium bérlőt saját dedikált adatbázisába helyezheti. Ha olyan sok adatbázist hoz létre, amely csak egy bérlőt tartalmaz, az erőforrások költségeinek optimalizálása érdekében a rugalmas készletekben együtt kezelheti őket.

Ezután kiépítünk egy másik bérlőt, ezúttal a saját adatbázisában:

1. A. \\ .. Tanulási modulok \\ kiépítése és katalogizálása \\ *Demo-ProvisionTenants.ps1*, *$TenantName* módosítása a **salsa Salix**, *$VenueType* a **Dance** és a *$Scenario* **2**.

2. A szkript ismételt futtatásához nyomja le az **F5** billentyűt.
    - Ez az **F5** billentyű lenyomásával az új bérlőt külön adatbázisban kell kiépíteni. Az adatbázis és a bérlő regisztrálva van a katalógusban. Ezután megnyílik a böngésző a bérlő események lapjára.

   ![Salix salsa-események lapja](./media/saas-multitenantdb-get-started-deploy/salix-salsa.png)

   - Görgessen le az oldal aljára. A szalagcímben megjelenik az adatbázis neve, amelyben a bérlői információk tárolódnak.

3. Frissítse az **Events hubot** , és a két új bérlő most megjelenik a listában.

## <a name="explore-the-servers-and-tenant-databases"></a>A kiszolgálók és a bérlői adatbázisok megismerése

Most nézzük meg az üzembe helyezett erőforrásokat:

1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg az erőforráscsoportok listáját. Nyissa meg az alkalmazás üzembe helyezésekor létrehozott erőforráscsoportot.

   ![erőforráscsoport](./media/saas-multitenantdb-get-started-deploy/resource-group.png)

2. Kattintson a **Catalog- &lt; MT &gt; felhasználói** kiszolgáló elemre. A katalógus-kiszolgáló két, *tenantcatalog* és *basetenantdb* nevű adatbázist tartalmaz. A *basetenantdb* -adatbázis egy üres sablon-adatbázis. A rendszer átmásolja egy új bérlői adatbázis létrehozására, akár több bérlőhöz, akár csak egy bérlőhöz használják.

   ![katalóguskiszolgáló elemre](./media/saas-multitenantdb-get-started-deploy/catalog-server.png)

3. Térjen vissza az erőforráscsoporthoz, és válassza ki azt a *tenants1-MT* kiszolgálót, amely a bérlői adatbázisokat tárolja.
    - A tenants1-adatbázis egy több-bérlős adatbázis, amelyben az eredeti három bérlőt, valamint az első hozzáadott bérlőt tárolja a rendszer. 50 DTU standard adatbázisként van konfigurálva.
    - A **salixsalsa** -adatbázis csak a Salix Salsa Dance-helyszínt tárolja. Alapértelmezés szerint a 50 DTU Standard Edition-adatbázisként van konfigurálva.

   ![bérlői kiszolgáló](./media/saas-multitenantdb-get-started-deploy/tenants-server.png)

## <a name="monitor-the-performance-of-the-database"></a>Az adatbázis teljesítményének figyelése

Ha a Load Generator több percig is fut, elegendő telemetria áll rendelkezésre a Azure Portal beépített adatbázis-figyelési képességeinek megkereséséhez.

1. Keresse meg a **tenants1-MT &lt; felhasználói &gt;** kiszolgálót, és kattintson a **tenants1** elemre azon adatbázis erőforrás-kihasználtságának megtekintéséhez, amelyben négy bérlő található. Az egyes bérlők a Load generatortól származó, szórványosan nagy terhelést okoznak:

   ![tenants1 figyelése](./media/saas-multitenantdb-get-started-deploy/monitor-tenants1.png)

   A DTU-kihasználtsági diagram szépen szemlélteti, hogy a több-bérlős adatbázisok hogyan támogathatják előre nem kiszámítható számítási feladatokat számos bérlő között. Ebben az esetben a Load Generator nagyjából 30 DTU-t alkalmaz az egyes bérlők számára. Ez a terhelés egy 50 DTU-adatbázis 60%-os kihasználtságának felel meg. A 60%-ot meghaladó csúcsok az egyidejű több bérlőre alkalmazott terhelést eredményezik.

2. Keresse meg a **tenants1-MT &lt; felhasználói &gt;** kiszolgálót, és kattintson a **salixsalsa** -adatbázisra. Megtekintheti az erőforrás-használatot ezen az adatbázison, amely csak egy bérlőt tartalmaz.

   ![salixsalsa-adatbázis](./media/saas-multitenantdb-get-started-deploy/monitor-salix.png)

A Load Generator hasonló terhelést alkalmaz az egyes bérlők számára, függetlenül attól, hogy az egyes bérlők melyik adatbázissal rendelkeznek. A **salixsalsa** -adatbázis csak egy bérlője láthatja, hogy az adatbázis sokkal nagyobb terhelést tart fenn, mint az adatbázis több Bérlővel. 

### <a name="resource-allocations-vary-by-workload"></a>Az erőforrás-elosztások munkaterheléstől függően változnak

Előfordulhat, hogy egy több-bérlős adatbázisnak több erőforrásra van szüksége a jó teljesítményhez, mint az egyetlen bérlős adatbázis, de nem mindig. Az erőforrások optimális elosztása a rendszerbeli bérlők adott számítási feladatának jellemzőitől függ.

A Load Generator parancsfájl által generált munkaterhelések csak illusztrációs célokat szolgálnak.

## <a name="additional-resources"></a>További források

- A több-bérlős SaaS-alkalmazásokkal kapcsolatos további információkért lásd: [tervezési minták a több-bérlős SaaS-alkalmazásokhoz](saas-tenancy-app-design-patterns.md).

- A rugalmas készletekről további információt a következő témakörben talál:

  - [A rugalmas készletek segítségével több adatbázist kezelhet és méretezheti Azure SQL Database](elastic-pool-overview.md)
  - [Scaling out with Azure SQL Database (Horizontális felskálázás az Azure SQL Database segítségével)](elastic-scale-introduction.md)

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta az alábbiakat:

> [!div class="checklist"]
> - A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése.
> - Az alkalmazást használó kiszolgálók és adatbázisok.
> - A bérlők az *adatkatalógussal* vannak leképezve.
> - Új bérlők kiépítése egy több-bérlős adatbázisba és egy egybérlős adatbázisba.
> - A készlet kihasználtságának megtekintése a bérlői tevékenységek figyeléséhez.
> - Mintavételi erőforrások törlése a kapcsolódó számlázás leállításához.

Most próbálja ki a [létesítés és a katalógus oktatóanyagot](saas-multitenantdb-provision-and-catalog.md).


<!--  Link references.

A [series of related tutorials] is available that build upon this initial deployment.
[link-wtp-overivew-jumpto-saas-tutorials-97j]: saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials

-->

[link-aka-ms-deploywtp-mtapp-52k]: https://aka.ms/deploywtp-mtapp


[link-azure-get-started-powershell-41q]: /powershell/azure/get-started-azureps

[link-github-wingtip-multitenantdb-55g]: https://github.com/Microsoft/WingtipTicketsSaaS-MultiTenantDB/



<!--  Image references.

[image-deploy-to-azure-blue-48d]: https://aka.ms/deploywtp-mtapp "Button for Deploy to Azure."
-->

[image-deploy-to-azure-blue-48d]: media/saas-multitenantdb-get-started-deploy/deploy.png "Az Azure-ba való üzembe helyezéshez használt gomb."