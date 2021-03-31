---
title: 'SaaS-alkalmazások: a vész-helyreállítási geo-redundáns biztonsági mentések'
description: Megtudhatja, hogyan használhat Azure SQL Database geo-redundáns biztonsági mentést egy több-bérlős SaaS-alkalmazás helyreállításához leállás esetén
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/14/2019
ms.openlocfilehash: 3fe6095595f5270b18536e6ef46afe4a0a5b3268
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97607711"
---
# <a name="use-geo-restore-to-recover-a-multitenant-saas-application-from-database-backups"></a>Geo-visszaállítás használata több-bérlős SaaS-alkalmazás helyreállításához az adatbázis biztonsági másolatainak használatával
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ez az oktatóanyag egy teljes vész-helyreállítási forgatókönyvet mutat be egy több-bérlős SaaS-alkalmazáshoz, amely egy bérlői modellben van megvalósítva. A [geo-visszaállítással](recovery-using-backups.md) helyreállíthatja a katalógust és a bérlői adatbázisokat, így automatikusan megőrizheti a Geo-redundáns biztonsági mentéseket egy másodlagos helyreállítási régióban. A leállás feloldása után a [geo-replikációval](active-geo-replication-overview.md) szabadon hazautalhatnak a megváltozott adatbázisokat az eredeti régiójába.

![A diagram egy eredeti és helyreállítási régiót mutat be, amelyek közül mindkettő tartalmaz egy alkalmazást, egy katalógust, a kiszolgálók és készletek eredeti vagy tükrözött képét, a tárterület automatikus biztonsági mentését, a helyreállítási régiót, amely a biztonsági mentés földrajzi replikálását fogadja el, valamint a kiszolgáló és a készlet új bérlők számára.](./media/saas-dbpertenant-dr-geo-restore/geo-restore-architecture.png)

A Geo-visszaállítás a Azure SQL Database legalacsonyabb díjas helyreállítási megoldása. Azonban a Geo-redundáns biztonsági mentésből való visszaállítás akár egy óra adatvesztést eredményezhet. Az egyes adatbázisok méretétől függően jelentős időt is igénybe vehet. 

> [!NOTE]
> A legkevesebb lehetséges RPO és RTO rendelkező alkalmazásokat a Geo-visszaállítás helyett geo-replikáció használatával állíthatja helyre.

Ez az oktatóanyag a visszaállítási és a visszalépési munkafolyamatokat vizsgálja. Az alábbiak végrehajtásának módját ismerheti meg:
> [!div class="checklist"]
> 
> * Szinkronizálja az adatbázist és a rugalmas készlet konfigurációs adatait a bérlői katalógusba.
> * Hozzon létre egy tükrözési képkörnyezetet egy olyan helyreállítási régióban, amely alkalmazásokat, kiszolgálókat és készleteket tartalmaz.   
> * Állítsa helyre a katalógust és a bérlői adatbázisokat a Geo-visszaállítás használatával.
> * A földrajzi replikálás használatával szabadon hazautalhatnak a bérlői katalógust, és megváltoztathatja a bérlői adatbázisokat a leállás feloldása után.
> * Frissítse a katalógust úgy, hogy minden adatbázis helyreáll (vagy haza), hogy nyomon követhesse az egyes bérlői adatbázisok aktív példányának aktuális helyét.
> * Győződjön meg arról, hogy az alkalmazás és a bérlői adatbázis mindig ugyanabban az Azure-régióban található, hogy csökkentse a késést. 
 

Az oktatóanyag elkezdése előtt végezze el a következő előfeltételeket:
* Telepítse a Wingtip tickets SaaS-adatbázist egy bérlői alkalmazásban. Ha kevesebb, mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip tickets SaaS-adatbázis üzembe helyezését és felfedezését bérlői alkalmazásokban](saas-dbpertenant-get-started-deploy.md) 
* Az Azure PowerShell telepítése. Részletekért lásd: [a Azure PowerShell első lépései](/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-geo-restore-recovery-pattern"></a>Bevezetés a Geo-visszaállítás helyreállítási mintába

A vész-helyreállítási (DR) fontos szempont számos alkalmazás számára, legyen szó megfelelőségi okokról vagy üzletmenet folytonosságáról. Ha hosszabb a szolgáltatás, egy jól előkészített DR-csomaggal csökkentheti az üzleti fennakadást. A Geo-visszaállításon alapuló DR-tervnek számos célt kell végrehajtania:
 * A lehető leggyorsabban foglalja le az összes szükséges kapacitást a kiválasztott helyreállítási régióban, hogy az elérhető legyen a bérlői adatbázisok visszaállításához.
 * Hozzon létre egy tükrözési rendszerkép-helyreállítási környezetet, amely az eredeti készletet és az adatbázis konfigurációját tükrözi. 
 * A visszaállítási folyamat megszakításának engedélyezése a repülés közepére, ha az eredeti régió ismét online állapotba kerül.
 * A bérlői kiépítés gyors engedélyezése, hogy az új bérlői bevezetést a lehető leghamarabb újra lehessen indítani.
 * A bérlők prioritási sorrendben történő visszaállításához optimalizálni kell.
 * Legyen optimalizálva, hogy a lehető leghamarabb elérhetővé tegye a bérlők online állapotba lépéseit a gyakorlati lépések végrehajtásával.
 * Legyen rugalmas a hiba, az újraindítási és a idempotens.
 * Szabadon hazautalhatnak az adatbázisokat az eredeti régiójába, és minimális hatással van a bérlők számára a leállás feloldásakor.  

> [!NOTE]
> Az alkalmazást annak a régiónak a párosított régiójában kell helyreállítani, amelyben az alkalmazás telepítve van. További információ: [Azure párosított régiók](../../best-practices-availability-paired-regions.md).   

Ez az oktatóanyag a Azure SQL Database funkcióit és az Azure platformot használja az alábbi problémák megoldásához:

* [Azure Resource Manager sablonokat](../../azure-resource-manager/templates/quickstart-create-templates-use-the-portal.md), hogy a lehető leggyorsabban lefoglalja a szükséges kapacitást. Azure Resource Manager sablonokkal a helyreállítási régióban található eredeti kiszolgálók és rugalmas készletek tükörképét lehet kiépíteni. Az új bérlők üzembe helyezéséhez külön-kiszolgáló és-készlet is létrejön.
* [Elastic Database ügyféloldali kódtárat](elastic-database-client-library.md) (EDCL) a bérlői adatbázis-katalógus létrehozásához és karbantartásához. A kiterjesztett katalógus rendszeres időközönként frissített készletet és adatbázis-konfigurációs adatokat tartalmaz.
* A EDCL szegmensek közötti [felügyeleti helyreállítási funkciói](elastic-database-recovery-manager.md) a helyreállítás és a visszaállítás során a katalógusban található adatbázis-helyek bejegyzéseinek fenntartásához.  
* A [geo-visszaállítással](../../key-vault/general/disaster-recovery-guidance.md)helyreállíthatja a katalógust és a bérlői adatbázisokat, így automatikusan megőrizheti a Geo-redundáns biztonsági mentéseket. 
* A bérlői prioritású sorrendben eljuttatott [aszinkron visszaállítási műveleteket](../../azure-resource-manager/management/async-operations.md)a rendszer az egyes készletekhez várólistára helyezi, és a kötegekben dolgozza fel, hogy a készlet ne legyen túlterhelve. Ezek a műveletek a végrehajtás előtt vagy közben is megvonhatók, ha szükséges.   
* [Geo-replikáció](active-geo-replication-overview.md), hogy a leállás után szabadon hazautalhatnak az adatbázisokat az eredeti régióba. Földrajzi replikálás használata esetén nincs adatvesztés és a bérlő minimális hatása.
* Az [SQL Server DNS-aliasai](./dns-alias-overview.md)lehetővé teszik, hogy a katalógus szinkronizálási folyamata a helytől függetlenül csatlakozhasson az aktív katalógushoz.  

## <a name="get-the-disaster-recovery-scripts"></a>A vész-helyreállítási parancsfájlok beszerzése

Az oktatóanyagban használt DR szkriptek a [Wingtip tickets SaaS-adatbázisban találhatók a bérlői GitHub-tárházban](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant). Tekintse meg az Wingtip tickets felügyeleti parancsfájlok letöltéséhez és feloldásához szükséges lépéseket ismertető [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) .

> [!IMPORTANT]
> A Wingtip jegyek felügyeleti parancsfájljaihoz hasonlóan a DR szkriptek is minőségi minta, ezért nem használhatók éles környezetben.

## <a name="review-the-healthy-state-of-the-application"></a>Az alkalmazás kifogástalan állapotának áttekintése
A helyreállítási folyamat megkezdése előtt tekintse át az alkalmazás rendeltetésszerű kifogástalan állapotát.

1. A böngészőben nyissa meg a Wingtip tickets Events hubot ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net, cserélje le a &lt; felhasználót a &gt; telepítés felhasználói értékére).
    
   Görgessen a lap aljára, és figyelje meg a katalógus kiszolgálójának nevét és helyét a láblécben. A hely az a régió, amelyben üzembe helyezte az alkalmazást.    

   > [!TIP]
   > Vigye az egérmutatót a hely fölé a kijelző nagyításához.

   ![Az Events hub kifogástalan állapota az eredeti régióban](./media/saas-dbpertenant-dr-geo-restore/events-hub-original-region.png)

2. Válassza ki a contoso Concert Hall-bérlőt, és nyissa meg az esemény lapját.

   Figyelje meg a bérlő kiszolgálójának nevét a láblécben. A hely megegyezik a Catalog kiszolgáló helyével.

   ![Contoso Concert Hall – eredeti régió](./media/saas-dbpertenant-dr-geo-restore/contoso-original-location.png)   

3. A [Azure Portal](https://portal.azure.com)tekintse át, majd nyissa meg azt az erőforráscsoportot, amelyben üzembe helyezte az alkalmazást.

   Figyelje meg az erőforrásokat és a régiót, amelyben az App Service-összetevők és a SQL Database telepítve van.

## <a name="sync-the-tenant-configuration-into-the-catalog"></a>A bérlő konfigurációjának szinkronizálása a katalógusba

Ebben a feladatban elindít egy folyamatot, amely a kiszolgálók, a rugalmas készletek és az adatbázisok konfigurációját szinkronizálja a bérlői katalógusba. Ezt az információt később a rendszer a helyreállítási régióban a tükrözési képkörnyezet konfigurálásához használja.

> [!IMPORTANT]
> Az egyszerűség kedvéért a szinkronizálási folyamat és az egyéb hosszan futó helyreállítási és újratelepítési folyamatok a helyi PowerShell-feladatok vagy az ügyfél-felhasználói bejelentkezés alatt futó munkamenetek keretében valósulnak meg. A bejelentkezéskor kiállított hitelesítési tokenek több óra elteltével lejárnak, és a feladatok sikertelenek lesznek. Éles környezetben a hosszan futó folyamatokat olyan megbízható Azure-szolgáltatásként kell megvalósítani, amely egy egyszerű szolgáltatásnév keretében fut. Lásd: a [Azure PowerShell használata egy egyszerű szolgáltatásnév létrehozásához tanúsítvánnyal](../../active-directory/develop/howto-authenticate-service-principal-powershell.md). 

1. A PowerShell ISE-ben nyissa meg a. ..\Learning Modules\UserConfig.psm1 fájlt. Cserélje le a `<resourcegroup>` és `<user>` a értéket a 10-es és a 11-es vonalakra az alkalmazás üzembe helyezésekor használt értékkel. Mentse a fájlt.

2. A PowerShell ISE-ben nyissa meg a. ..\Learning Modules\Business folytonosságát és a katasztrófa Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 szkriptet.

    Ebben az oktatóanyagban futtatja az egyes forgatókönyveket ebben a PowerShell-parancsfájlban, ezért ne nyissa meg a fájlt.

3. Állítsa be a következőket:

    $DemoScenario = 1: indítson el egy háttérben futó feladatot, amely a bérlői kiszolgáló és a készlet konfigurációs adatait szinkronizálja a katalógusba.

4. A szinkronizálási parancsfájl futtatásához nyomja le az F5 billentyűt. 

    Ezt az információt később a rendszer a helyreállítási régióban található kiszolgálók, készletek és adatbázisok tükörképének létrehozásához használja.  

    ![Szinkronizálási folyamat](./media/saas-dbpertenant-dr-geo-restore/sync-process.png)

Hagyja meg a háttérben futó PowerShell-ablakot, és folytassa az oktatóanyag további részével.

> [!NOTE]
> A szinkronizálási folyamat egy DNS-alias használatával csatlakozik a katalógushoz. Az alias a visszaállítás és a visszaléptetés során módosul, hogy az aktív katalógusra mutasson. A szinkronizálási folyamat naprakészen tartja a katalógust a helyreállítási régióban végrehajtott adatbázis-vagy készlet-konfigurációval. A visszaléptetés során ezeket a módosításokat az eredeti régióban található egyenértékű erőforrásokra alkalmazza a rendszer.

## <a name="geo-restore-recovery-process-overview"></a>A helyreállítási folyamat földrajzi visszaállításának áttekintése

A Geo-visszaállítási helyreállítási folyamat telepíti az alkalmazást, és visszaállítja az adatbázisokat a biztonsági másolatokból a helyreállítási régióba.

A helyreállítási folyamat a következő műveleteket végzi el:

1. Letiltja az Azure Traffic Manager végpontot a webalkalmazáshoz az eredeti régióban. A végpont letiltása megakadályozza, hogy a felhasználók érvénytelen állapotban csatlakozzanak az alkalmazáshoz, ha az eredeti régió online állapotba kerül a helyreállítás során.

2. Kiépíti a helyreállítási katalógus kiszolgálóját a helyreállítási régióban, Geo-visszaállítja a katalógus-adatbázist, és frissíti a activecatalog aliast, hogy az a visszaállított katalógus-kiszolgálóra mutasson. A katalógus-alias módosítása biztosítja, hogy a katalógus szinkronizálási folyamata mindig szinkronban legyen az aktív katalógussal.

3. A helyreállítási katalógusban lévő összes meglévő bérlőt kapcsolat nélküli állapotban jelöli, hogy megakadályozza a bérlői adatbázisokhoz való hozzáférést a visszaállításuk előtt.

4. Kiépíti az alkalmazás egy példányát a helyreállítási régióban, és konfigurálja azt a visszaállított katalógus használatára a régióban. A minimális késleltetés érdekében a minta alkalmazás úgy van kialakítva, hogy mindig ugyanahhoz a régióhoz kapcsolódjon egy bérlői adatbázishoz.

5. Egy olyan kiszolgálót és rugalmas készletet hoz létre, amelyben új bérlők vannak kiépítve. Az erőforrások létrehozása biztosítja, hogy az új bérlők üzembe helyezése ne zavarja a meglévő bérlők helyreállítását.

6. Frissíti az új bérlői aliast, hogy a helyreállítási régióban lévő új bérlői adatbázisok kiszolgálójára mutasson. Az alias módosítása biztosítja, hogy az új bérlők adatbázisai a helyreállítási régióban legyenek kiépítve.
        
7. Kiépíti a kiszolgálókat és a rugalmas készleteket a helyreállítási régióban a bérlői adatbázisok visszaállításához. Ezek a kiszolgálók és készletek az eredeti régióban lévő konfiguráció tükrözési képe. A kiépítési készletek elöl foglalják az összes adatbázis helyreállításához szükséges kapacitást.

    A régiókban a leállás jelentős nyomást eredményezhet a párosított régióban elérhető erőforrásokra. Ha a DR-re vonatkozó geo-visszaállításra támaszkodik, akkor az erőforrások gyors megőrzése ajánlott. Vegye fontolóra a Geo-replikációt, ha kritikus fontosságú, hogy egy adott alkalmazás egy adott régióban legyen helyreállítva. 

8. Engedélyezi a webalkalmazás Traffic Manager végpontját a helyreállítási régióban. A végpont engedélyezése lehetővé teszi az alkalmazás számára új bérlők kiépítését. Ebben a szakaszban a meglévő bérlők továbbra is offline állapotban vannak.

9. Beküldi a kérelmek kötegeit az adatbázisok prioritási sorrendben történő visszaállításához. 

    * A kötegek úgy vannak rendszerezve, hogy az adatbázisok az összes készletben párhuzamosan legyenek visszaállítva.  

    * A visszaállítási kérelmeket aszinkron módon küldi el a rendszer, így azokat gyorsan elküldik, és az egyes készleteken való végrehajtásra várólistára kerülnek

    * Mivel a visszaállítási kérelmeket a rendszer párhuzamosan dolgozza fel az összes készletben, jobb megoldás a fontos bérlők elosztása számos különböző készleten keresztül. 

10. Figyeli a szolgáltatást, hogy meghatározza, mikor állítják vissza az adatbázisokat. A bérlői adatbázis visszaállítása után a katalógusban online állapotba kerül, és a bérlői adatbázis ROWVERSION összegét rögzíti a rendszer. 

    * A bérlői adatbázisokat az alkalmazás érheti el, amint online állapotban vannak a katalógusban.

    * A bérlői adatbázisban található ROWVERSION-értékek összege a katalógusban van tárolva. Ez az összeg ujjlenyomatként működik, amely lehetővé teszi a rehalasztott folyamat számára annak meghatározását, hogy az adatbázis frissítve lett-e a helyreállítási régióban.

## <a name="run-the-recovery-script"></a>A helyreállítási parancsfájl futtatása

> [!IMPORTANT]
> Ez az oktatóanyag visszaállítja az adatbázisokat a Geo-redundáns biztonsági mentésből. Habár ezek a biztonsági másolatok általában 10 percen belül elérhetők, akár egy óráig is eltarthat. A parancsfájl addig szünetel, amíg elérhetővé nem válik.

Képzelje el, hogy a régióban az alkalmazás központi telepítése történik, és futtassa a helyreállítási parancsfájlt:

1. A PowerShell ISE-ben a. ..\Learning Modules\Business folytonossága és a katasztrófa Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlban állítsa be a következő értéket:

    $DemoScenario = 2: állítsa helyre az alkalmazást egy helyreállítási régióban a Geo-redundáns biztonsági másolatokből való visszaállítással.

2. A parancsfájl futtatásához válassza az F5 lehetőséget.  

    * A parancsfájl egy új PowerShell-ablakban nyílik meg, majd elindítja a párhuzamosan futó PowerShell-feladatok készletét. Ezek a feladatok visszaállítják a kiszolgálókat, készleteket és adatbázisokat a helyreállítási régióba.

    * A helyreállítási régió az az Azure-régióhoz társított párosított régió, amelyben az alkalmazást üzembe helyezte. További információ: [Azure párosított régiók](../../best-practices-availability-paired-regions.md). 

3. Figyelje a helyreállítási folyamat állapotát a PowerShell-ablakban.

    ![Képernyőkép a PowerShell-ablakról, amelyen nyomon követheti a helyreállítási folyamat állapotát.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress.png)

> [!NOTE]
> A helyreállítási feladatok kódjának megismeréséhez tekintse át a PowerShell-szkripteket a. ..\Learning Modules\Business folytonossága és a katasztrófa-Recovery\DR-RestoreFromBackup\RecoveryJobs mappában.

## <a name="review-the-application-state-during-recovery"></a>Az alkalmazás állapotának áttekintése a helyreállítás során
Amíg az alkalmazás végpontja le van tiltva a Traffic Managerban, az alkalmazás nem érhető el. A katalógus vissza lett állítva, és az összes bérlő offline állapotban van megjelölve. Ekkor a helyreállítási régióban az alkalmazás-végpont engedélyezve lesz, és az alkalmazás ismét online állapotba kerül. Bár az alkalmazás elérhető, a bérlők offline állapotban jelennek meg az események központban, amíg az adatbázisok vissza nem állnak. Fontos, hogy megtervezze az alkalmazást az offline bérlői adatbázisok kezeléséhez.

* A katalógus-adatbázis helyreállítását, de a bérlők online állapotba való visszaállítását követően frissítse a Wingtip tickets Events hubot a böngészőben.

  * A láblécben figyelje meg, hogy a katalógus-kiszolgáló neve most már rendelkezik egy helyreállítási utótaggal, és a helyreállítási régióban található.

  * Figyelje meg, hogy a még nem visszaállított bérlők kapcsolat nélküli állapotban vannak megjelölve, és nem választhatók ki.   
 
    ![Helyreállítási folyamat](./media/saas-dbpertenant-dr-geo-restore/events-hub-tenants-offline-in-recovery-region.png)  

  * Ha közvetlenül a bérlő offline állapotban nyitja meg a bérlői események lapot, a lap egy bérlő offline értesítését jeleníti meg. Ha például a contoso Concert Hall offline állapotban van, próbálja meg megnyitni a következőt:. http://events.wingtip-dpt.&lt User &gt; . trafficmanager.net/contosoconcerthall.

    ![Az offline események lapot megjelenítő képernyőkép.](./media/saas-dbpertenant-dr-geo-restore/dr-in-progress-offline-contosoconcerthall.png)

## <a name="provision-a-new-tenant-in-the-recovery-region"></a>Új bérlő kiépítése a helyreállítási régióban
Még a bérlői adatbázisok visszaállítása előtt is kiépítheti az új bérlőket a helyreállítási régióban. A helyreállítási régióban kiépített új bérlői adatbázisok haza a helyreállított adatbázisokkal.   

1. A PowerShell ISE-ben a. ..\Learning Modules\Business folytonossága és a katasztrófa Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlban állítsa be a következő tulajdonságot:

    $DemoScenario = 3: új bérlő kiépítése a helyreállítási régióban.

2. A parancsfájl futtatásához válassza az F5 lehetőséget.

3. A galagonya Hall eseményei oldal megnyílik a böngészőben a kiépítés befejezésekor. 

    Figyelje meg, hogy a Hawthorn Hall adatbázisa a helyreállítási régióban található.

    ![A helyreállítási régióban kiépített Hawthorn Hall](./media/saas-dbpertenant-dr-geo-restore/hawthorn-hall-provisioned-in-recovery-region.png)

4. A böngészőben frissítse a Wingtip tickets Events hub oldalát, és tekintse meg a galagonya csarnokot is. 

    Ha a galagonya-csarnokot a többi bérlő visszaállítására való várakozás nélkül kiépített, akkor a többi bérlő továbbra is offline állapotba kerülhet.

## <a name="review-the-recovered-state-of-the-application"></a>Az alkalmazás helyreállított állapotának áttekintése

A helyreállítási folyamat befejeződése után az alkalmazás és az összes bérlő teljes mértékben működőképes a helyreállítási régióban. 

1. Miután a PowerShell-konzol ablakában megjelenik az összes bérlő, frissítse az Events hubot. 

    A bérlők mind online állapotban jelennek meg, beleértve az új bérlőt, a Hawthorn hallt is.

    ![Helyreállított és új bérlők az Events hub-ban](./media/saas-dbpertenant-dr-geo-restore/events-hub-with-hawthorn-hall.png)

2. Kattintson a contoso Concert Hall elemre, és nyissa meg az események lapját. 

    A láblécben figyelje meg, hogy az adatbázis a helyreállítási régióban található helyreállítási kiszolgálón található.

    ![Contoso a helyreállítási régióban](./media/saas-dbpertenant-dr-geo-restore/contoso-recovery-location.png)

3. A [Azure Portal](https://portal.azure.com)nyissa meg az erőforráscsoportok listáját.  

    Figyelje meg a telepített erőforráscsoportot, valamint a helyreállítási erőforráscsoportot a-Recovery utótaggal. A helyreállítási erőforráscsoport tartalmazza a helyreállítási folyamat során létrehozott összes erőforrást, valamint a leállás során létrehozott új erőforrásokat is. 

4. Nyissa meg a helyreállítási erőforráscsoportot, és figyelje meg a következő elemeket:

   * A katalógus és a tenants1-kiszolgálók helyreállítási verziói a-Recovery utótaggal. Ezen kiszolgálókon a visszaállított katalógus és bérlői adatbázisok mindegyike az eredeti régióban használt neveket tartalmazza.

   * A tenants2-DPT- &lt; User &gt; -Recovery SQL Server. Ez a kiszolgáló új bérlők kiépítési felállítására szolgál a leállás során.

   * Az App Service nevű Events-Wingtip-DPT- &lt; recoveryregion &gt; - &lt; felhasználó &gt; , amely az Events alkalmazás helyreállítási példánya.

     ![Contoso-erőforrások a helyreállítási régióban](./media/saas-dbpertenant-dr-geo-restore/resources-in-recovery-region.png) 
    
5. Nyissa meg a tenants2-DPT- &lt; User &gt; -Recovery SQL Servert. Figyelje meg, hogy az adatbázis hawthornhall és a rugalmas készlet Pool1 tartalmazza. A hawthornhall-adatbázis rugalmas adatbázisként van konfigurálva a Pool1 rugalmas készletben.

## <a name="change-the-tenant-data"></a>Bérlői adatértékek módosítása 
Ebben a feladatban frissíti a visszaállított bérlői adatbázisok egyikét. A rehazatérési folyamat átmásolja a visszaállított adatbázisokat, amelyek az eredeti régióra módosultak. 

1. A böngészőben keresse meg a contoso Concert Hall eseményeinek listáját, Görgesse végig az eseményeket, és figyelje meg az utolsó eseményt, amely súlyosan Strauss.

2. A PowerShell ISE-ben a. ..\Learning Modules\Business folytonossága és a katasztrófa Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlban állítsa be a következő értéket:

    $DemoScenario = 4: esemény törlése a helyreállítási régióban lévő bérlőből.

3. A szkript végrehajtásához válassza az F5 lehetőséget.

4. Frissítse a contoso Concert Events-események oldalát ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net/contosoconcerthall), és figyelje meg, hogy az esemény súlyos Strauss-hiánya hiányzik.

Az oktatóanyag ezen pontján visszaállította az alkalmazást, amely már fut a helyreállítási régióban. Kiépített egy új bérlőt a helyreállítási régióban, és módosította az egyik visszaállított bérlőt.  

> [!NOTE]
> A mintában szereplő egyéb oktatóanyagok nem úgy lettek kialakítva, hogy a helyreállítási állapotban fussanak az alkalmazással. Ha más oktatóanyagokat szeretne felfedezni, ügyeljen arra, hogy először szabadon hazautalhatnak az alkalmazást.

## <a name="repatriation-process-overview"></a>A rehazatérési folyamat áttekintése

A rehazatérési folyamat a leállás után visszaállít egy alkalmazást és annak adatbázisait az eredeti régiójába.

![Geo-visszaállítás visszautasítása](./media/saas-dbpertenant-dr-geo-restore/geo-restore-repatriation.png)   

A folyamat:

1. Leállítja az összes folyamatban lévő visszaállítási tevékenységet, és megszakítja az adatbázis-visszaállítási kérelmeket.

2. Újraaktiválja az eredeti régióbeli bérlői adatbázisokat, amelyek a leállás óta nem változtak. Ezek az adatbázisok a még nem visszaállított és a helyreállított, de utána nem módosított adatbázisokat tartalmazzák. Az újraaktivált adatbázisok pontosan a bérlők által utoljára elértek.

3. Kiépíti az új bérlő kiszolgálójának és rugalmas készletének tükrözési képét az eredeti régióban. A művelet befejezése után az új bérlői alias frissül, hogy erre a kiszolgálóra mutasson. Az alias frissítése azt eredményezi, hogy az új bérlő beléptetése az eredeti régióban történik a helyreállítási régió helyett.

3. A Geo-Replication használatával helyezi át a katalógust az eredeti régióba a helyreállítási régióból.

4. Frissíti a készlet konfigurációját az eredeti régióban, így összhangban van a helyreállítási régióban a leállás során végrehajtott változásokkal.

5. Létrehozza a szükséges kiszolgálókat és készleteket a leállás során létrehozott új adatbázisok üzemeltetéséhez.

6. A Geo-replikációval olyan visszaállított bérlői adatbázisokat szabadon hazautalhatnak, amelyek frissítve lettek a visszaállítás után, valamint az összes új bérlői adatbázis, amelyet a leállás során kiosztottak. 

7. A helyreállítási régióban létrehozott erőforrások tisztítása a visszaállítási folyamat során.

A haza bérlői adatbázisok számának korlátozásához az 1 – 3. lépést azonnal végrehajtjuk.  

A 4. lépés csak akkor hajtható végre, ha a helyreállítási régióban lévő katalógus módosult a leállás során. A katalógus akkor frissül, ha új bérlők jönnek létre, vagy ha a helyreállítási régióban bármely adatbázis vagy készlet konfigurációja módosul.

Fontos, hogy a 7. lépés a bérlők számára minimális fennakadást okoz, és ne vesszenek el az adatvesztés. A cél elérése érdekében a folyamat geo-replikálást használ.

Az egyes adatbázisok földrajzilag replikálása előtt a rendszer törli az eredeti régióban található megfelelő adatbázist. A helyreállítási régióban található adatbázist ezután földrajzilag replikálja a rendszer, és másodlagos replikát hoz létre az eredeti régióban. A replikáció befejezése után a bérlő offline állapotban van megjelölve a katalógusban, amely megszakítja a kapcsolatot az adatbázissal a helyreállítási régióban. Ezt követően a rendszer átadja az adatbázist, és feldolgozza a függőben lévő tranzakciókat a másodlagos módon, így az adatvesztés nem történik meg. 

Feladatátvételkor az adatbázis szerepkörei fordítottak. A másodlagos az eredeti régióban az elsődleges írható-olvasható adatbázis lesz, a helyreállítási régióban lévő adatbázis pedig csak olvasható másodlagos lesz. A katalógusban lévő bérlői bejegyzés frissül, hogy az eredeti régióban lévő adatbázisra hivatkozzon, és a bérlő online állapotban van megjelölve. Ezen a ponton befejeződött az adatbázis visszajuttatása. 

Az alkalmazásokat újra kell írni, hogy a kapcsolatok megszakadásakor az Újracsatlakozás automatikusan megtörténjen. Amikor a katalógus használatával közvetítik az újracsatlakozást, az eredeti régióban csatlakoznak a haza-adatbázishoz. Habár a rövid leválasztást gyakran nem észlelték, dönthet úgy, hogy a munkaidőn kívülről is szabadon hazautalhatnak az adatbázisokat.

Az adatbázis haza követően a helyreállítási régióban található másodlagos adatbázis törölhető. Az eredeti régióban található adatbázis újra támaszkodik a DR-védelem geo-visszaállítására.

A 8. lépésben a helyreállítási régióban lévő erőforrásokat, köztük a helyreállítási kiszolgálókat és a készleteket is törli a rendszer.

## <a name="run-the-repatriation-script"></a>A kivezetés parancsfájl futtatása
Képzelje el, hogy a leállás megoldódott, és futtatja a rehazatérési parancsfájlt.

Ha követte az oktatóanyagot, a szkript azonnal újraaktiválja a fabrikam jazz Clubot és a Som Dojot az eredeti régióban, mert változatlanok maradnak. Ezután repatriates az új bérlőt, a Hawthorn hallt és a contoso Concert hallt, mert módosították. A szkript a katalógust is repatriates, amely a Hawthorn Hall üzembe helyezésekor frissült.
  
1. A PowerShell ISE-ben a. ..\Learning Modules\Business folytonossága és a katasztrófa Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlban ellenőrizze, hogy a katalógus szinkronizálási folyamata továbbra is fut-e a PowerShell-példányában. Szükség esetén indítsa újra a beállítást:

    $DemoScenario = 1: a bérlői kiszolgáló, a készlet és az adatbázis-konfigurációs adatok szinkronizálásának megkezdése a katalógusba.

    A parancsfájl futtatásához válassza az F5 lehetőséget.

2.  Ezután állítsa be a következőt:

    $DemoScenario = 5: szabadon hazautalhatnak az alkalmazást az eredeti régiójába.

    Ha a helyreállítási parancsfájlt egy új PowerShell-ablakban szeretné futtatni, válassza az F5 lehetőséget. A rehazatérés több percet is igénybe vehet, és a PowerShell-ablakban figyelhető.

3. A parancsfájl futása közben frissítse az Events hub oldalt ( http://events.wingtip-dpt.&lt ; User &gt; . trafficmanager.net).

    Figyelje meg, hogy az összes bérlő online állapotban van, és elérhető a folyamat során.

4. Válassza ki a fabrikam jazz Clubot a megnyitásához. Ha nem módosította a bérlőt, figyelje meg a láblécet, hogy a kiszolgáló már visszaállt az eredeti kiszolgálóra.

5. Nyissa meg vagy frissítse a contoso Concert Events oldalát. Figyelje meg, hogy a láblécből kezdetben az adatbázis még mindig a-Recovery kiszolgálón van. 

6. Frissítse a contoso Concert Events-események oldalát a rehazatérési folyamat befejeződése után, és figyelje meg, hogy az adatbázis most már az eredeti régióban van.

7. Frissítse újra az események hubot, és nyissa meg a Hawthorn hallt. Figyelje meg, hogy az adatbázisa az eredeti régióban is található. 

## <a name="clean-up-recovery-region-resources-after-repatriation"></a>Helyreállítási régió erőforrásainak törlése a rehazatérés után
A rehazatérés befejezése után biztonságos törölni a helyreállítási régióban lévő erőforrásokat. 

> [!IMPORTANT]
> Ezeket az erőforrásokat azonnal törölje az összes számlázási művelet leállításához.

A visszaállítási folyamat létrehozza a helyreállítási erőforráscsoport összes helyreállítási erőforrását. A karbantartási folyamat törli ezt az erőforráscsoportot, és eltávolítja az erőforrásokra mutató összes hivatkozást a katalógusból. 

1. A PowerShell ISE-ben a. ..\Learning Modules\Business folytonossága és a katasztrófa Recovery\DR-RestoreFromBackup\Demo-RestoreFromBackup.ps1 parancsfájlban állítsa be a következőket:
    
    $DemoScenario = 6: elavult erőforrások törlése a helyreállítási régióból.

2. A parancsfájl futtatásához válassza az F5 lehetőséget.

A szkriptek tisztítása után az alkalmazás visszatér, ahol elindult. Ezen a ponton újra futtathatja a szkriptet, vagy más oktatóanyagokat is kipróbálhat.

## <a name="designing-the-application-to-ensure-that-the-app-and-the-database-are-co-located"></a>Az alkalmazás megtervezése annak biztosítására, hogy az alkalmazás és az adatbázis közös elhelyezésű legyen 
Az alkalmazás úgy van kialakítva, hogy mindig a bérlő adatbázisával azonos régióban lévő példányról kapcsolódjon. Ez a kialakítás csökkenti az alkalmazás és az adatbázis közötti késleltetést. Ez az optimalizálás feltételezi, hogy az alkalmazás és az adatbázis közötti interakció chattier, mint a felhasználó és az alkalmazás közötti interakció.  

Előfordulhat, hogy a bérlői adatbázisok a helyreállítás és az eredeti régiók között is elterjednek. Az alkalmazás minden adatbázisnál megkeresi a régiót, amelyben az adatbázis található, a DNS-címkeresés a bérlői kiszolgáló nevében. A kiszolgálónév egy alias. Az alias-kiszolgáló neve tartalmazza a régió nevét. Ha az alkalmazás nem ugyanabban a régióban található, mint az adatbázis, a a-kiszolgálóval megegyező régióban lévő példányra irányítja át. Az alkalmazás és az adatbázis közötti késleltetést az adott régióban lévő példányra irányítja át.  

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:
> [!div class="checklist"]
> 
> * A bérlői katalógus segítségével rendszeres időközönként frissítheti a konfigurációs adatokat, amelyek lehetővé teszik a tükrözési rendszerkép helyreállítási környezetének létrehozását egy másik régióban.
> * A Geo-visszaállítás használatával helyreállíthatja az adatbázisokat a helyreállítási régióban.
> * Frissítse a bérlői katalógust, hogy tükrözze a visszaállított bérlői adatbázis helyét. 
> * DNS-alias használatával engedélyezheti, hogy az alkalmazások újrakonfigurálás nélkül csatlakozzanak a bérlői katalógushoz.
> * A Geo-Replication használatával szabadon hazautalhatnak a helyreállított adatbázisokat az eredeti régióba egy kimaradás feloldása után.

Próbálja ki [egy több-bérlős SaaS-alkalmazás vész-helyreállítását az adatbázis geo-replikálási](./saas-dbpertenant-dr-geo-replication.md) oktatóanyagával, amelyből megtudhatja, hogyan használhatja a Geo-replikálást, hogy jelentősen csökkentse a nagyméretű több-bérlős alkalmazások helyreállításához szükséges időt.

## <a name="additional-resources"></a>További források

[További oktatóanyagok a Wingtip SaaS-alkalmazásra építve](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)