---
title: Vész-helyreállítás az SaaS-alkalmazásokhoz földrajzi replikálással
description: Megtudhatja, hogyan használhatja a Azure SQL Database geo-replikákat a több-bérlős SaaS-alkalmazások helyreállításához leállás esetén
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: d2c1ddd1e0b5a080050e1ffeb28eded98dbfea3f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652109"
---
# <a name="disaster-recovery-for-a-multi-tenant-saas-application-using-database-geo-replication"></a>Vész-helyreállítás egy több-bérlős SaaS-alkalmazáshoz az adatbázis-geo-replikáció használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben az oktatóanyagban egy teljes vész-helyreállítási forgatókönyvet vizsgál egy több-bérlős SaaS-alkalmazáshoz, amely az adatbázis-bérlői modell használatával lett megvalósítva. Az alkalmazás áramszünet miatti védelméhez a [_geo-replikálás_](active-geo-replication-overview.md) segítségével hozzon létre replikákat a katalógushoz és a bérlői adatbázisokhoz egy másodlagos helyreállítási régióban. Leállás esetén gyorsan átadhatja ezeket a replikákat a normál üzleti műveletek folytatásához. Feladatátvételkor az eredeti régióban lévő adatbázisok a helyreállítási régióban lévő adatbázisok másodlagos replikái lesznek. Miután ezek a replikák ismét elérhetővé válnak, automatikusan felveszik a helyreállítási régióban lévő adatbázisok állapotát. A leállás feloldása után visszatérhet az eredeti éles régióban lévő adatbázisokhoz.

Ez az oktatóanyag a feladatátvételi és feladat-visszavételi munkafolyamatokat vizsgálja. A következőket fogja megtanulni:
> [!div class="checklist"]
> 
> * Adatbázis és rugalmas készlet konfigurációs adatainak szinkronizálása a bérlői katalógusba
> * Helyreállítási környezet beállítása egy másik régióban, amely az alkalmazást, a kiszolgálókat és a készleteket tartalmazza
> * _Geo-replikáció_ használata a katalógus és a bérlői adatbázisok replikálásához a helyreállítási régióban
> * Feladatátvétel az alkalmazás és a katalógus és a bérlő adatbázisai között a helyreállítási régióban 
> * Később, a leállás után visszaadja az alkalmazást, katalogizálja és bérlői adatbázisait az eredeti régióba
> * Frissítse a katalógust úgy, hogy minden bérlői adatbázis feladatait átadja az egyes bérlői adatbázisok elsődleges helyének nyomon követéséhez
> * Győződjön meg arról, hogy az alkalmazás és az elsődleges bérlői adatbázis mindig ugyanabban az Azure-régióban található, hogy csökkentse a késést  


Az oktatóanyag megkezdése előtt győződjön meg arról, hogy teljesülnek az alábbi előfeltételek:
* A Wingtip jegyek SaaS-adatbázisa egy bérlői alkalmazáson van üzembe helyezve. Ha kevesebb, mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip tickets SaaS-adatbázis üzembe helyezése és megismerése bérlői alkalmazásokban](saas-dbpertenant-get-started-deploy.md)  
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps)

## <a name="introduction-to-the-geo-replication-recovery-pattern"></a>Bevezetés a Geo-replikáció helyreállítási mintába

![Helyreállítási architektúra](./media/saas-dbpertenant-dr-geo-replication/recovery-architecture.png)

A vész-helyreállítási (DR) fontos szempont számos alkalmazás számára, legyen szó megfelelőségi okokról vagy üzletmenet folytonosságáról. Ha a szolgáltatás hosszabb ideig tartó, a jól előkészített DR-terv csökkentheti az üzleti fennakadást. A földrajzi replikálás használatával a legalacsonyabb RPO és RTO az adatbázis-replikák egy olyan helyreállítási régióban való fenntartásával, amely rövid időn belül feloldható.

A Geo-replikáción alapuló DR-terv három különálló részből áll:
* Beállítás – a helyreállítási környezet létrehozása és karbantartása
* Helyreállítás – az alkalmazás és az adatbázisok helyreállítási környezetbe való feladatátvétele leállás esetén,
* Visszaküldés – az alkalmazás és az adatbázisok feladatátvétele az eredeti régióba az alkalmazás feloldása után 

Minden résznek körültekintően kell megfontolnia, különösen, ha nagy léptékben működik. Összességében a tervnek számos célt kell végrehajtania:

* Beállítás
    * Hozzon létre és őrizzen meg egy tükrözési környezetet a helyreállítási régióban. A rugalmas készletek létrehozása és az ebben a helyreállítási környezetben található adatbázisok replikálása a helyreállítási régióban foglalt kapacitást is fenntartja. A környezet fenntartása magában foglalja az új bérlői adatbázisok replikálását az üzembe helyezés során.  
* Helyreállítási
    * Ha a napi költségek csökkentése érdekében a méretezhető helyreállítási környezetet használják, a készleteket és az adatbázisokat fel kell mérni a helyreállítási régióban a teljes működési kapacitás megszerzéséhez.
     * Az új bérlői kiépítés engedélyezése a helyreállítási régióban a lehető leghamarabb  
     * A bérlők prioritási sorrendben való visszaállítására van optimalizálva
     * A lehető leggyorsabban optimalizálhatja a bérlők online állapotba lépéseit
     * Legyen rugalmas a meghibásodás, újraindítási és idempotens
     * Akkor lehet megszakítani a folyamatot, ha az eredeti régió újra elérhetővé vált.
* Hazaszállítás 
     * A helyreállítási régió adatbázisainak feladatátvétele az eredeti régióban lévő replikák számára minimális hatással van a bérlők számára: az adatvesztés és a minimálisan megengedett időtartam a bérlőn kívüli.

Ebben az oktatóanyagban ezek a kihívások a Azure SQL Database és az Azure platform funkcióinak használatával foglalkoznak:

* [Azure Resource Manager sablonokat](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), hogy a lehető leggyorsabban lefoglalja a szükséges kapacitást. Azure Resource Manager sablonokkal a helyreállítási régióban lévő üzemi kiszolgálók és rugalmas készletek tükörképét lehet kiépíteni.
* [Geo-replikáció](active-geo-replication-overview.md), aszinkron módon replikált írásvédett formátumú másodlagos zónák létrehozása az összes adatbázishoz. Egy leállás során a rendszer átadja a feladatátvételt a helyreállítási régióban lévő replikáknak.  A leállás feloldása után visszatérhet az eredeti régióban lévő adatbázisokhoz adatvesztés nélkül.
* A bérlői prioritási sorrendben eljuttatott [aszinkron](../../azure-resource-manager/management/async-operations.md) feladatátvételi műveletek a nagy számú adatbázis feladatátvételi idejének csökkentése érdekében.
* A szegmensek közötti [felügyeleti helyreállítási funkciók](elastic-database-recovery-manager.md)segítségével módosíthatja a katalógusban szereplő adatbázis-bejegyzéseket a helyreállítás és a visszaléptetés során. Ezek a funkciók lehetővé teszik, hogy az alkalmazás a helytől függetlenül csatlakozhasson a bérlői adatbázisokhoz, az alkalmazás újrakonfigurálása nélkül.
* [SQL Server DNS-aliasok](./dns-alias-overview.md), amelyek lehetővé teszik az új bérlők zökkenőmentes üzembe helyezését, függetlenül attól, hogy az alkalmazás melyik régión belül működik. A DNS-aliasok arra is szolgálnak, hogy a katalógus szinkronizálási folyamata a helytől függetlenül kapcsolódjon az aktív katalógushoz.

## <a name="get-the-disaster-recovery-scripts"></a>A vész-helyreállítási parancsfájlok beszerzése 

> [!IMPORTANT]
> A Wingtip jegyek felügyeleti parancsfájljaihoz hasonlóan a DR szkriptek is minőségi minta, ezért nem használhatók éles környezetben. 

Az oktatóanyagban és a Wingtip alkalmazás forráskódjában használt helyreállítási parancsfájlok elérhetők a [Wingtip tickets SaaS-adatbázisban a bérlői GitHub-tárházban](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant/). Tekintse meg az Wingtip tickets felügyeleti parancsfájlok letöltéséhez és feloldásához szükséges lépéseket ismertető [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) .

## <a name="tutorial-overview"></a>Az oktatóanyag áttekintése
Ebben az oktatóanyagban először a Geo-replikáció használatával hozza létre a Wingtip tickets-alkalmazás és az adatbázisainak replikáit egy másik régióban. Ezt követően átadja a feladatátvételt a régiónak, hogy szimulálja a helyreállítást a leállás miatt. Ha elkészült, az alkalmazás teljesen működőképes a helyreállítási régióban.

Később, egy külön visszaléptetési lépésben a helyreállítási régióban lévő katalógus és bérlői adatbázisok feladatátvételét az eredeti régióba hajtja végre. Az alkalmazás és az adatbázisok elérhetők maradnak a teljes visszaigénylés során. Ha elkészült, az alkalmazás teljesen működőképes az eredeti régióban.

> [!Note]
> Az alkalmazást annak a régiónak a _párosított régiójában_ kell helyreállítani, amelyben az alkalmazás telepítve van. További információ: [Azure párosított régiók](../../best-practices-availability-paired-regions.md).

## <a name="review-the-healthy-state-of-the-application"></a>Az alkalmazás kifogástalan állapotának áttekintése

A helyreállítási folyamat megkezdése előtt tekintse át az alkalmazás rendeltetésszerű kifogástalan állapotát.
1. A böngészőben nyissa meg a Wingtip tickets Events hub ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net – &lt; felhasználó lecserélése &gt; a központi telepítés felhasználói értékére).
    * Görgessen a lap aljára, és figyelje meg a katalógus kiszolgálójának nevét és helyét a láblécben. A hely az a régió, amelyben üzembe helyezte az alkalmazást.
    *Tipp: vigye az egérmutatót a hely fölé a kijelző nagyításához.* 
     ![ Az Events hub kifogástalan állapota az eredeti régióban](./media/saas-dbpertenant-dr-geo-replication/events-hub-original-region.png)

2. Kattintson a contoso Concert Hall-bérlőre, és nyissa meg az esemény lapját.
    * Figyelje meg a bérlői kiszolgáló nevét a láblécben. A hely ugyanaz lesz, mint a Catalog kiszolgáló helye.

3. A [Azure Portal](https://portal.azure.com)nyissa meg azt az erőforráscsoportot, amelyben az alkalmazás telepítve van
    * Figyelje meg azt a régiót, amelyben a kiszolgálók telepítve vannak. 

## <a name="sync-tenant-configuration-into-catalog"></a>Bérlői konfiguráció szinkronizálása a katalógusba

Ebben a feladatban egy olyan folyamatot indít el, amely a kiszolgálók, a rugalmas készletek és az adatbázisok konfigurációját szinkronizálja a bérlői katalógusba. A folyamat naprakészen tartja ezt az információt a katalógusban.  A folyamat együttműködik az aktív katalógussal, akár az eredeti régióban, akár a helyreállítási régióban. A konfigurációs adatokat a rendszer a helyreállítási folyamat részeként használja annak biztosítására, hogy a helyreállítási környezet összhangban legyen az eredeti környezettel, majd később a visszaléptetés során, hogy az eredeti régió konzisztens legyen a helyreállítási környezetben végrehajtott változásokkal. A katalógus a bérlői erőforrások helyreállítási állapotának nyomon követésére is használható

> [!IMPORTANT]
> Az egyszerűség kedvéért a szinkronizálási folyamat és az egyéb hosszú ideig futó helyreállítási és újratelepítési folyamatok ezekben az oktatóanyagokban helyi PowerShell-feladatokként vagy az ügyfél felhasználói bejelentkezése alatt futó munkamenetekben valósulnak meg. A bejelentkezéskor kiállított hitelesítési jogkivonatok több óra elteltével lejárnak, és a feladatok sikertelenek lesznek. Éles környezetben a hosszan futó folyamatokat olyan megbízható Azure-szolgáltatásként kell megvalósítani, amely egy egyszerű szolgáltatásnév keretében fut. Lásd: a [Azure PowerShell használata egy egyszerű szolgáltatásnév létrehozásához tanúsítvánnyal](../../active-directory/develop/howto-authenticate-service-principal-powershell.md).

1. A _POWERSHELL ISE_-ben nyissa meg a. ..\Learning Modules\UserConfig.psm1 fájlt. Cserélje le a `<resourcegroup>` és `<user>` a értéket a 10-es és a 11-es vonalakra az alkalmazás üzembe helyezésekor használt értékkel.  Mentse a fájlt!

2. A *POWERSHELL ISE*-ben nyissa meg a. ..\Learning Modules\Business folytonosságát és a katasztrófa Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következőket:
    * **$DemoScenario = 1**, a bérlői kiszolgáló szinkronizálása és a készlet konfigurációs adatainak elindítása a katalógusba

3. Nyomja le az **F5** billentyűt a szinkronizálási parancsfájl futtatásához. A rendszer új PowerShell-munkamenetet nyit meg a bérlői erőforrások konfigurációjának szinkronizálásához.
![A bérlői erőforrások konfigurációjának szinkronizálására megnyitott új PowerShell-munkamenetet bemutató képernyőkép.](./media/saas-dbpertenant-dr-geo-replication/sync-process.png)

Hagyja a háttérben futó PowerShell-ablakot, és folytassa az oktatóanyag többi részével. 

> [!Note]
> A szinkronizálási folyamat egy DNS-alias használatával csatlakozik a katalógushoz. Ez az alias módosítva lesz a visszaállítás és a helyreállítás során, hogy az aktív katalógusra mutasson. A szinkronizálási folyamat naprakészen tartja a katalógust a helyreállítási régióban végrehajtott adatbázis-vagy készlet-konfigurációval.  A visszaléptetés során ezeket a módosításokat az eredeti régióban található egyenértékű erőforrásokra alkalmazza a rendszer.

## <a name="create-secondary-database-replicas-in-the-recovery-region"></a>Másodlagos adatbázis-replikák létrehozása a helyreállítási régióban

Ebben a feladatban egy olyan folyamatot indít el, amely egy duplikált alkalmazás-példányt telepít, és a katalógust és az összes bérlői adatbázist egy helyreállítási régióba replikálja.

> [!Note]
> Ez az oktatóanyag geo-replikációs védelmet biztosít a Wingtip tickets minta alkalmazáshoz. A földrajzi replikálást használó alkalmazások éles környezetben való üzembe helyezése esetén az egyes bérlők kezdettől kezdve egy földrajzilag replikált adatbázissal lesznek kiépítve. Lásd: [a magasan elérhető szolgáltatások tervezése Azure SQL Database használatával](designing-cloud-solutions-for-disaster-recovery.md#scenario-1-using-two-azure-regions-for-business-continuity-with-minimal-downtime)

1. A *POWERSHELL ISE*-ben nyissa meg a. ..\Learning Modules\Business folytonosságát és a katasztrófa Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő értékeket:
    * **$DemoScenario = 2**, tükrözött rendszerkép-helyreállítási környezet létrehozása és katalógus-és bérlői adatbázisok replikálása

2. A szkript futtatásához nyomja le az **F5** billentyűt. A rendszer új PowerShell-munkamenetet nyit meg a replikák létrehozásához.
![Szinkronizálási folyamat](./media/saas-dbpertenant-dr-geo-replication/replication-process.png)  

## <a name="review-the-normal-application-state"></a>A normál alkalmazás állapotának áttekintése

Ezen a ponton az alkalmazás általában az eredeti régióban fut, és most már a Geo-replikáció védi.  A csak olvasható másodlagos replikák léteznek a helyreállítási régióban az összes adatbázishoz. 

1. A Azure Portalban tekintse meg az erőforráscsoportot, és vegye figyelembe, hogy az erőforráscsoport a helyreállítási régióban helyreállítási utótaggal lett létrehozva. 

2. Fedezze fel a helyreállítási erőforráscsoport erőforrásait.  

3. Kattintson a contoso Concert Hall adatbázisára a _tenants1-DPT- &lt; User &gt; -Recovery_ Server kiszolgálón.  Kattintson Geo-Replication a bal oldalon. 

    ![Contoso Concert geo – replikálási hivatkozás](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication.png) 

Az Azure-régiók térképen jegyezze fel a földrajzi replikálási kapcsolatot az eredeti régióban lévő elsődleges régió és a helyreállítási régióban lévő másodlagos között.  

## <a name="fail-over-the-application-into-the-recovery-region"></a>Az alkalmazás feladatátvétele a helyreállítási régióban

### <a name="geo-replication-recovery-process-overview"></a>Geo-replikálás helyreállítási folyamatának áttekintése

A helyreállítási parancsfájl a következő feladatokat hajtja végre:

1. Letiltja a webalkalmazás Traffic Manager végpontját az eredeti régióban. A végpont letiltása megakadályozza, hogy a felhasználók érvénytelen állapotban csatlakozzanak az alkalmazáshoz, ha az eredeti régió online állapotba kerül a helyreállítás során.

1. A a helyreállítási régióban lévő katalógus-adatbázis kényszerített feladatátvételét használja az elsődleges adatbázis létrehozásához, és frissíti a _activecatalog_ aliast, hogy az a helyreállítási katalógus kiszolgálójára mutasson.

1. Frissíti a _newtenant_ -aliast, hogy az a helyreállítási régióban lévő bérlői kiszolgálóra mutasson. Az alias módosítása biztosítja, hogy az új bérlők adatbázisai a helyreállítási régióban legyenek kiépítve. 

1. A helyreállítási katalógusban lévő összes meglévő bérlőt kapcsolat nélküli állapotban jelöli meg, hogy megakadályozza a bérlői adatbázisokhoz való hozzáférést a feladatátvétel előtt.

1. Frissíti a helyreállítási régióban lévő összes rugalmas készlet és replikált önálló adatbázis konfigurációját, hogy tükrözze a konfigurációt az eredeti régióban. (Erre a feladatra csak akkor van szükség, ha a helyreállítási környezetben lévő készleteket vagy replikált adatbázisokat a normál műveletek során csökkentik a költségek csökkentése érdekében.

1. Engedélyezi a webalkalmazás Traffic Manager végpontját a helyreállítási régióban. A végpont engedélyezése lehetővé teszi az alkalmazás számára új bérlők kiépítését. Ebben a szakaszban a meglévő bérlők továbbra is offline állapotban vannak.

1. Beküldi a kérelmek kötegeit, hogy prioritási sorrendben kényszerítse a feladatátvételt az adatbázisokra.
    * A kötegek úgy vannak rendszerezve, hogy az adatbázisok feladatátvétele párhuzamosan történjen az összes készletben.
    * A feladatátvételi kérelmeket aszinkron műveletekkel küldi el a rendszer, így azok gyorsan elküldve lesznek, és egyszerre több kérelem is feldolgozható.

   > [!Note]
   > Leállás esetén az eredeti régió elsődleges adatbázisai offline állapotban vannak.  Kényszerített feladatátvétel a másodlagos megszakítással megszakítja a kapcsolódást az elsődleges kapcsolaton anélkül, hogy a fennmaradó várólistán lévő tranzakciókat kellene alkalmaznia. Ebben az oktatóanyagban a DR részletezési forgatókönyvben, ha a feladatátvétel időpontjában bármilyen frissítési tevékenység van, adatvesztés történhet. Később, amikor a helyreállítási régióban lévő adatbázisok feladatátvételét visszaadja az eredeti régióba, a rendszer egy normál feladatátvételt használ annak biztosítására, hogy ne legyen adatvesztés.

1. Figyeli a szolgáltatást annak megállapításához, hogy az adatbázisok feladatátvétele megtörtént-e. A bérlői adatbázis feladatátvétele után a frissíti a katalógust, hogy rögzítse a bérlői adatbázis helyreállítási állapotát, és a bérlőt online állapotba kell megjelölni.
    * A bérlői adatbázisokat az alkalmazás érheti el, amint online állapotban vannak a katalógusban.
    * A bérlői adatbázisban található ROWVERSION-értékek összege a katalógusban van tárolva. Ez az érték ujjlenyomatként működik, amely lehetővé teszi, hogy a helyreállítási folyamat megtudja, hogy az adatbázis frissítve lett-e a helyreállítási régióban.

### <a name="run-the-script-to-fail-over-to-the-recovery-region"></a>A parancsfájl futtatása a helyreállítási régióba való feladatátvételhez

Most képzelje el, hogy az alkalmazás üzembe helyezése és a helyreállítási parancsfájl futtatása a régióban leáll:

1. A *POWERSHELL ISE*-ben nyissa meg a. ..\Learning Modules\Business folytonosságát és a katasztrófa Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő értékeket:
    * **$DemoScenario = 3**, az alkalmazás helyreállítása helyreállítási régióban a replikák feladatátvételével

2. A szkript futtatásához nyomja le az **F5** billentyűt.  
    * A parancsfájl egy új PowerShell-ablakban nyílik meg, majd a párhuzamosan futó PowerShell-feladatok sorozatát indítja el. Ezek a feladatok feladatátvételt hajtanak végre a bérlői adatbázisokon a helyreállítási régióban.
    * A helyreállítási régió az az Azure-régióhoz társított _párosított régió_ , amelyben az alkalmazást üzembe helyezte. További információ: [Azure párosított régiók](../../best-practices-availability-paired-regions.md). 

3. Figyelje a helyreállítási folyamat állapotát a PowerShell-ablakban.
    ![feladatátvételi folyamat](./media/saas-dbpertenant-dr-geo-replication/failover-process.png)

> [!Note]
> A helyreállítási feladatok kódjának megismeréséhez tekintse át a PowerShell-szkripteket a. ..\Learning Modules\Business folytonossága és a katasztrófa-Recovery\DR-FailoverToReplica\RecoveryJobs mappában.

### <a name="review-the-application-state-during-recovery"></a>Az alkalmazás állapotának áttekintése a helyreállítás során

Amíg az alkalmazás végpontja le van tiltva a Traffic Managerban, az alkalmazás nem érhető el. Miután a katalógust átvette a helyreállítási régióba, és az összes olyan bérlő, amely offline állapotban van, az alkalmazás ismét online állapotba kerül. Bár az alkalmazás elérhető, az egyes bérlők offline állapotban jelennek meg az események központban, amíg az adatbázis feladatátvétele meghiúsul. Fontos, hogy megtervezze az alkalmazást az offline bérlői adatbázisok kezeléséhez.

1. A katalógus-adatbázis helyreállítása után azonnal frissítse a Wingtip tickets Events hubot a böngészőben.
   * A láblécben figyelje meg, hogy a katalógus-kiszolgáló neve most már rendelkezik egy _helyreállítási_ utótaggal, és a helyreállítási régióban található.
   * Figyelje meg, hogy a még nem visszaállított bérlők kapcsolat nélküli állapotban vannak megjelölve, és nem választhatók ki.  

     > [!Note]
     > Ha csak néhány adatbázist kíván helyreállítani, előfordulhat, hogy a helyreállítás befejeződése előtt nem tudja frissíteni a böngészőt, így előfordulhat, hogy nem látja a bérlőket offline állapotban. 

     ![Események hub offline](./media/saas-dbpertenant-dr-geo-replication/events-hub-offlinemode.png) 

   * Ha közvetlenül az offline bérlő eseményeinek lapját nyitja meg, akkor a "bérlő offline" értesítés jelenik meg. Ha például a contoso Concert Hall offline állapotban van, próbálja meg megnyitni a következőt: http://events.wingtip-dpt.&lt &gt; . felhasználó. Trafficmanager.net/contosoconcerthall ![ contoso offline oldal](./media/saas-dbpertenant-dr-geo-replication/dr-in-progress-offline-contosoconcerthall.png) 

### <a name="provision-a-new-tenant-in-the-recovery-region"></a>Új bérlő kiépítése a helyreállítási régióban
Még az összes meglévő bérlői adatbázis feladatátvétele előtt is kiépítheti az új bérlőket a helyreállítási régióban.  

1. A *POWERSHELL ISE*-ben nyissa meg a. ..\Learning Modules\Business folytonosságát és a katasztrófa Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlt, és állítsa be a következő tulajdonságot:
    * **$DemoScenario = 4**, új bérlő kiépítése a helyreállítási régióban

2. Nyomja le az **F5** billentyűt a szkript futtatásához és az új bérlő kiépítéséhez. 

3. A Hawthorn Hall eseményeinek lapja a böngészőben nyílik meg, amikor befejeződik. Figyelje meg, hogy a Hawthorn Hall-adatbázis a helyreállítási régióban lett kiépítve.
    ![A Hawthorn Hall eseményeinek lapja](./media/saas-dbpertenant-dr-geo-replication/hawthornhallevents.png) 

4. A böngészőben frissítse a Wingtip tickets Events hub oldalát, és tekintse meg a galagonya csarnokot is. 
    * Ha a galagonya-csarnokot a többi bérlő visszaállítására való várakozás nélkül kiépített, akkor a többi bérlő továbbra is offline állapotba kerülhet.


## <a name="review-the-recovered-state-of-the-application"></a>Az alkalmazás helyreállított állapotának áttekintése

A helyreállítási folyamat befejezése után az alkalmazás és az összes bérlő teljes mértékben működőképes a helyreállítási régióban. 

1. Ha a PowerShell-konzol ablakban megjelenik az összes bérlő helyreállítása, frissítse az Events hubot.  A bérlők mind online állapotban jelennek meg, beleértve az új bérlőt, a Hawthorn hallt is.

    ![helyreállított és új bérlők az Events hub-ban](./media/saas-dbpertenant-dr-geo-replication/events-hub-with-hawthorn-hall.png)

2. A [Azure Portal](https://portal.azure.com)nyissa meg az erőforráscsoportok listáját.  
    * Figyelje meg a telepített erőforráscsoportot, valamint a helyreállítási erőforráscsoportot a _-Recovery_ utótaggal.  A helyreállítási erőforráscsoport tartalmazza a helyreállítási folyamat során létrehozott összes erőforrást, valamint a leállás során létrehozott új erőforrásokat is.  

3. Nyissa meg a helyreállítási erőforráscsoportot, és figyelje meg a következő elemeket:
   * A katalógus és a tenants1-kiszolgálók helyreállítási verziói a _helyreállítási_ utótaggal.  Ezen kiszolgálókon a visszaállított katalógus és bérlői adatbázisok mindegyike az eredeti régióban használt neveket tartalmazza.

   * A _tenants2-DPT- &lt; User &gt; -Recovery_ SQL Server.  Ez a kiszolgáló új bérlők kiépítési felállítására szolgál a leállás során.
   * Az App Service nevű, _Events-Wingtip-DPT- &lt; recoveryregion &gt; - &lt; User&gt_;, amely az Events alkalmazás helyreállítási példánya. 

     ![Azure helyreállítási erőforrások](./media/saas-dbpertenant-dr-geo-replication/resources-in-recovery-region.png) 

4. Nyissa meg a _tenants2-DPT- &lt; User &gt; -Recovery_ SQL Servert.  Figyelje meg, hogy tartalmazza az adatbázis _hawthornhall_ és a _Pool1_ rugalmas készletét.  A _hawthornhall_ -adatbázis rugalmas adatbázisként van konfigurálva a _Pool1_ rugalmas készletben.

5. Térjen vissza az erőforráscsoporthoz, és kattintson a contoso Concert Hall adatbázisára a _tenants1-DPT- &lt; User &gt; -Recovery_ Server kiszolgálón. Kattintson Geo-Replication a bal oldalon.

    ![Contoso-adatbázis feladatátvétel után](./media/saas-dbpertenant-dr-geo-replication/contoso-geo-replication-after-failover.png)

## <a name="change-tenant-data"></a>Bérlői adatbázis módosítása 
Ebben a feladatban frissíti a bérlői adatbázisok egyikét. 

1. A böngészőben keresse meg a contoso Concert Hall eseményeinek listáját, és jegyezze fel az utolsó esemény nevét.
2. A *POWERSHELL ISE*-ben a. ..\Learning Modules\Business folytonossága és a katasztrófa Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 parancsfájlban állítsa be a következő értéket:
    * **$DemoScenario = 5** Esemény törlése a helyreállítási régióban lévő bérlőből
3. Nyomja le az **F5** billentyűt a szkript végrehajtásához
4. Frissítse a contoso koncert csarnok eseményeinek oldalát ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net/contosoconcerthall – az &lt; &gt; üzembe helyezés felhasználói értékével helyettesíti a felhasználót), és figyelje meg, hogy az utolsó eseményt törölték.

## <a name="repatriate-the-application-to-its-original-production-region"></a>Az alkalmazás szabadon hazautalhatnak az eredeti éles régióba

Ez a feladat repatriates az alkalmazást az eredeti régiójába. Valós esetben a rendszer a leállás feloldásakor kezdeményezi a kiesést.

### <a name="repatriation-process-overview"></a>A rehazatérési folyamat áttekintése

![Rehazatérési architektúra](./media/saas-dbpertenant-dr-geo-replication/repatriation-architecture.png)

A rehazatérési folyamat:
1. Megszakítja a függőben lévő vagy a fedélzeti adatbázis-visszaállítási kérelmeket.
2. Frissíti a _newtenant_ -aliast, hogy az a forrás régiójában lévő bérlők kiszolgálójára mutasson. Az alias módosítása biztosítja, hogy az új bérlők adatbázisait a rendszer kiépítse a forrás régióban.
3. A módosított bérlői összes adattal az eredeti régióba kerül
4. A bérlői adatbázisok prioritási sorrendben történő feladatátvétele.

A feladatátvétel hatékonyan áthelyezi az adatbázist az eredeti régióba. Ha az adatbázis feladatátvételt hajt végre, minden nyitott kapcsolat el lesz dobva, és az adatbázis néhány másodpercig nem érhető el. Az alkalmazásokat újra kell írni az újrapróbálkozási logikával.  Habár ez a rövid leválasztás gyakran nem fordul elő, dönthet úgy, hogy a munkaidőn kívülről szabadon hazautalhatnak az adatbázisokat. 


### <a name="run-the-repatriation-script"></a>A kivezetés parancsfájl futtatása
Most képzelje el, hogy a leállás megoldódott, és futtatja a rehazatérési parancsfájlt.

1. A *POWERSHELL ISE*-ben a. ..\Learning Modules\Business folytonossága és a katasztrófa Recovery\DR-FailoverToReplica\Demo-FailoverToReplica.ps1 szkript.

2. Ellenőrizze, hogy a katalógus szinkronizálási folyamata továbbra is fut-e a PowerShell-példányában.  Szükség esetén indítsa újra a beállítást:
    * **$DemoScenario = 1**, a bérlői kiszolgáló, a készlet és az adatbázis-konfigurációs adatok szinkronizálásának megkezdése a katalógusba
    * A szkript futtatásához nyomja le az **F5** billentyűt.

3.  Ezután állítsa be a következőt:
    * **$DemoScenario = 6**, szabadon hazautalhatnak az alkalmazást az eredeti régiójába
    * Nyomja le az **F5** billentyűt a helyreállítási parancsfájl új PowerShell-ablakban való futtatásához.  A rehazatérés több percet is igénybe vehet, és a PowerShell-ablakban figyelhető.
    ![Rehazatérési folyamat](./media/saas-dbpertenant-dr-geo-replication/repatriation-process.png)

4. A parancsfájl futása közben frissítse az Events hub oldalt ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net)
    * Figyelje meg, hogy az összes bérlő online állapotban van, és elérhető a folyamat során.

5. A visszatelepítési folyamat befejezése után frissítse az Events hubot, és nyissa meg a Hawthorn Hall Events (események) lapját. Figyelje meg, hogy ez az adatbázis az eredeti régióba lett haza.
    ![Events hub haza](./media/saas-dbpertenant-dr-geo-replication/events-hub-repatriated.png)


## <a name="designing-the-application-to-ensure-app-and-database-are-colocated"></a>Az alkalmazás megtervezése az alkalmazások és adatbázisok közös elhelyezésének biztosításához 
Az alkalmazás úgy van kialakítva, hogy mindig a bérlői adatbázissal azonos régióban lévő példányról csatlakozzon. Ez a kialakítás csökkenti az alkalmazás és az adatbázis közötti késleltetést. Ez az optimalizálás feltételezi, hogy az alkalmazás és az adatbázis közötti interakció chattier, mint a felhasználó és az alkalmazás közötti interakció.  

Előfordulhat, hogy a bérlői adatbázisok a helyreállítási és az eredeti régiókban is elterjedhetnek. Az alkalmazás minden adatbázisnál megkeresi a régiót, amelyben az adatbázis található, a DNS-címkeresés a bérlői kiszolgáló nevében. SQL Database a kiszolgálónév egy alias. Az alias-kiszolgáló neve tartalmazza a régió nevét. Ha az alkalmazás nem ugyanabban a régióban található, mint az adatbázis, a a-kiszolgálóval megegyező régióban lévő példányra irányítja át. Az alkalmazás és az adatbázis közötti késleltetést az adott régióban lévő példányra irányítja át. 

## <a name="next-steps"></a>Következő lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> 
> * Adatbázis és rugalmas készlet konfigurációs adatainak szinkronizálása a bérlői katalógusba
> * Helyreállítási környezet beállítása egy másik régióban, amely az alkalmazást, a kiszolgálókat és a készleteket tartalmazza
> * _Geo-replikáció_ használata a katalógus és a bérlői adatbázisok replikálásához a helyreállítási régióban
> * Feladatátvétel az alkalmazás és a katalógus és a bérlő adatbázisai között a helyreállítási régióban 
> * Az alkalmazás, a katalógus és a bérlő adatbázisainak visszautasítása az eredeti régióba a leállás után

További információt a technológiákról Azure SQL Database biztosít az üzletmenet folytonosságának biztosítása érdekében az üzletmenet- [folytonosság áttekintése](business-continuity-high-availability-disaster-recover-hadr-overview.md) dokumentációjában.

## <a name="additional-resources"></a>További források

* [További oktatóanyagok a Wingtip SaaS-alkalmazásra építve](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)