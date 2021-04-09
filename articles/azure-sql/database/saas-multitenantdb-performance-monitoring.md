---
title: Több-bérlős adatbázis teljesítményének figyelése
description: Több-bérlős SaaS-alkalmazásban lévő többbérlős Azure SQL Databaseek teljesítményének figyelése és kezelése
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
ms.openlocfilehash: d37bf2c84b74dba76e5d1921ed67072af7f6c328
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790899"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Több-bérlős SaaS-alkalmazásban lévő többbérlős Azure SQL Databaseek teljesítményének figyelése és kezelése
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben az oktatóanyagban az SaaS-alkalmazásokban használt fő teljesítmény-kezelési forgatókönyveket vizsgálja. Ha betöltési generátort használ a több-bérlős adatbázisok közötti tevékenységek szimulálása érdekében, a Azure SQL Database beépített figyelési és riasztási funkcióit mutatjuk be.

A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás egy több-bérlős adatmodellt használ, ahol a bérlői AZONOSÍTÓk az esetlegesen több adatbázis között vannak elosztva. Sok más SaaS-alkalmazáshoz hasonlóan a bérlői számítási feladatok várt mintája kiszámíthatatlan és szórványos. Ez a gyakorlatban azt jelenti, hogy a jegyeladásokra bármikor sor kerülhet. Ahhoz, hogy használhassa ezt a tipikus adatbázis-használati mintát, az adatbázisok fel-és leskálázásával optimalizálható a megoldás díja. Ilyen típusú mintázat esetén fontos az adatbázis-erőforrások használatának figyelése, hogy a terhelések ésszerűen egyensúlyban legyenek a potenciálisan több adatbázis között. Azt is biztosítania kell, hogy az egyes adatbázisok rendelkezzenek megfelelő erőforrásokkal, és ne verjék a [DTU](purchasing-models.md#dtu-based-purchasing-model) -korlátozásokat. Ez az oktatóanyag az adatbázisok figyelésének és kezelésének módjait mutatja be, valamint azt, hogyan végezheti el a javítási műveleteket a számítási feladatok változásaira reagálva.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> 
> * Több-bérlős adatbázis használatának szimulálása egy megadott Load Generator futtatásával
> * Figyelje meg az adatbázist úgy, ahogy válaszol a terhelés növekedésére
> * Az adatbázis vertikális felskálázása a megnövekedett adatbázis-terhelésre reagálva
> * Bérlő kiépítése egyetlen bérlős adatbázisba

Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás telepítve van. Ha kevesebb mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás üzembe helyezése és megismerése](./saas-multitenantdb-get-started-deploy.md) című részt
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>A SaaS teljesítmény-felügyeleti mintáinak bemutatása

Az adatbázisteljesítmény-kezelés a teljesítményadatok fordításából és elemzéséből, majd az adatokra való reagálásból áll. Ez tulajdonképpen a paraméterek módosítását jelenti, miáltal az alkalmazás válaszideje elfogadható szinten marad. 

### <a name="performance-management-strategies"></a>Teljesítménykezelési stratégiák

* Annak elkerülése érdekében, hogy a teljesítmény manuális figyelése megtörténjen, a leghatékonyabb olyan riasztások beállítása, amelyek akkor lépnek érvénybe, **amikor az adatbázisok a normál tartományokból kóborak**.
* Egy adatbázis számítási méretének rövid távú ingadozására való reagáláshoz a **DTU szintje felfelé vagy lefelé is méretezhető**. Ha ez az ingadozás rendszeres vagy kiszámítható módon történik, **az adatbázis méretezése automatikusan ütemezhető**. Beállítható például a vertikális leskálázás, amikor előre láthatóan kevés lesz a számítási feladat, például éjjelente vagy a hétvégi napokon.
* A hosszú távú ingadozásokra vagy a bérlők változásaira való reagáláshoz az **egyes bérlők áthelyezhetők más adatbázisba**.
* Az *egyes* bérlők betöltésének rövid távú növekedésére való reagáláshoz az **egyes bérlők kivehetők egy adatbázisból, és egyedi számítási méretet rendelhetnek hozzájuk**. A terhelés csökkentése után a bérlő visszaküldhető a több-bérlős adatbázisba. Ha ezt előre ismeri, a bérlők áthelyezhetők a megelőző jelleggel, így biztosítva, hogy az adatbázis mindig rendelkezik a szükséges erőforrásokkal, és hogy elkerülje a több-bérlős adatbázisban lévő többi bérlőre gyakorolt hatást. Ha ez a szükséglet előre kiszámítható, például ha egy helyszín nagy mennyiségű növekedésre számít a jegyeladásokban egy népszerű esemény miatt, akkor ez a kezelési viselkedés integrálható az alkalmazásba.

Az [Azure Portal](https://portal.azure.com) a legtöbb erőforráshoz beépített figyelési és riasztási lehetőségeket biztosít. SQL Database esetében a figyelés és a riasztás az adatbázisokon érhető el. Ez a beépített figyelési és riasztási erőforrás-specifikus megoldás, ezért érdemes kis mennyiségű erőforrást használni, de sok erőforrás használata esetén nem célszerű.

Nagy mennyiségű forgatókönyv esetén, ahol számos erőforrással dolgozik, [Azure monitor naplókat](https://azure.microsoft.com/services/log-analytics/) is használhat. Ez egy különálló Azure-szolgáltatás, amely a Log Analytics munkaterületen összegyűjtött, kibocsátott naplók elemzését teszi lehetővé. Azure Monitor naplók több szolgáltatásból is gyűjthetik a telemetria, és a riasztások lekérdezésére és beállítására használhatók.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>A Wingtip tickets SaaS több-bérlős adatbázis-alkalmazás forráskódjának és parancsfájljainak beszerzése

A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájljai és az alkalmazás forráskódja a [WingtipTicketsSaaS-MultitenantDB GitHub-](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) tárházban érhető el. Tekintse meg az [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets SaaS-parancsfájlok letöltésének és feloldásának lépéseihez.

## <a name="provision-additional-tenants"></a>További bérlők kiépítése

Ahhoz, hogy a teljesítmény monitorozása és kezelése nagy léptékben működjön, az oktatóanyaghoz több Bérlőnek kell lennie egy több-bérlős adatbázisban.

Ha egy korábbi oktatóanyagban már kiépített bérlők kötegét, ugorjon a [használat szimulálása az összes bérlői adatbázison](#simulate-usage-on-all-tenant-databases) szakaszra.

1. A **POWERSHELL ISE**-ben nyissa meg a... \\ Tanulási modulok \\ teljesítményének figyelése és kezelése \\ *Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. **$DemoScenario**  =  **1** beállítása, _bérlők kötegének kiépítése_
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A parancsfájl néhány perc alatt üzembe helyezi a több-bérlős adatbázisba 17 bérlőt. 

A *New-TenantBatch* szkript új bérlőket hoz létre a több-bérlős adatbázison belüli egyedi bérlői kulcsokkal, és inicializálja azokat a bérlői név és a hely típusával. Ez összhangban van azzal, ahogyan az alkalmazás kiépít egy új bérlőt. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Az összes bérlői adatbázis használatának szimulálása

A *Demo-PerformanceMonitoringAndManagement.ps1* parancsfájl a több-bérlős adatbázison futó számítási feladatok szimulálására szolgál. A terhelés a rendelkezésre álló betöltési forgatókönyvek egyikével jön létre:

| Bemutató | Eset |
|:--|:--|
| 2 | Normál intenzitású terhelés előállítása (körülbelül 30 DTU) |
| 3 | Terhelés kiváltása a bérlők száma alapján|
| 4 | Nagyobb terhelést eredményező DTU-törések (körülbelül 70 DTU)|
| 5 | Nagy intenzitású (körülbelül 90 DTU) létrehozása egyetlen bérlőn, valamint a normál fényerő terhelése az összes többi bérlőn |

A terhelésgenerátor egy *szintetikus* CPU-terhelést alkalmaz az összes bérlői adatbázison. A generátor minden bérlői adatbázis számára elindít egy feladatot, amely időközönként meghív egy, a terhelést létrehozó tárolt eljárást. A betöltési szintek (DTU), az időtartam és az intervallumok az összes adatbázisban változatosak, a nem kiszámítható bérlői tevékenységek szimulálása.

1. A **POWERSHELL ISE**-ben nyissa meg a... \\ Tanulási modulok \\ teljesítményének figyelése és kezelése \\ *Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. **$DemoScenario**  =  **2** beállítása, _normál intenzitású terhelés létrehozása_
1. Az **F5** billentyű lenyomásával alkalmazhatja a betöltést az összes bérlőre.

A Wingtip tickets SaaS több-bérlős adatbázisa egy SaaS-alkalmazás, és az SaaS-alkalmazások valós terhelése általában szórványos és kiszámíthatatlan. Ennek szimulálására a terhelésgenerátor az összes bérlő között elosztott, véletlenszerű terhelést hoz létre. A terhelési minta megjelenése több percet is igénybe vehet, ezért futtassa a Load Generatort 3-5 percre, mielőtt megpróbálja figyelni a terhelést a következő fejezetekben.

> [!IMPORTANT]
> A Load Generator egy új PowerShell-ablakban feladatok sorozata fut. Ha bezárta a munkamenetet, a Load Generator leáll. A Load Generator olyan *feladattípusban* marad, amelyben a generátor elindítása után kiépített új bérlők terhelését generálja. A *CTRL-C* billentyűkombinációval leállítja az új feladatok meghívását, és kilép a parancsfájlból. A Load Generator továbbra is futni fog, de csak a meglévő bérlők esetében.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Erőforrás-használat figyelése a Azure Portal használatával

Az alkalmazott terhelésből származó erőforrás-használat figyeléséhez nyissa meg a portált a több-bérlős adatbázishoz, a **tenants1**, amely tartalmazza a bérlőket:

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és keresse meg a *tenants1-MT- &lt; &gt; User* kiszolgálót.
1. Görgessen le és keresse meg az adatbázisokat, és kattintson a **tenants1** elemre. Ez a többvállalatos többbérlős adatbázis tartalmazza az eddig létrehozott bérlőket.

![Adatbázisábra](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Figyelje meg a **DTU** diagramot.

## <a name="set-performance-alerts-on-the-database"></a>Teljesítmény-riasztások beállítása az adatbázisban

Állítson be egy riasztást az adatbázison, amely \> 75%-os kihasználtságot indít el az alábbiak szerint:

1. Nyissa meg a *tenants1* -adatbázist (a *tenants1-MT- &lt; &gt; User* kiszolgálón) a [Azure Portal](https://portal.azure.com).
1. Kattintson a **Riasztási szabályok** elemre, majd a **+ Riasztás hozzáadása** gombra:

   ![riasztás hozzáadása](./media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Adjon meg egy nevet, például: **Magas DTU**.
1. Állítsa be a következő értékeket:
   * **Metrika = DTU százalék**
   * **Feltétel = nagyobb, mint**
   * **Küszöbérték = 75**.
   * **Időszak = az elmúlt 30 percben**
1. Adjon hozzá egy e-mail-címet a *további rendszergazdai e-mailek (ek)* mezőbe, és kattintson **az OK** gombra.

   ![riasztás beállítása](./media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Foglalt adatbázis vertikális felskálázása

Ha a terhelési szint növekszik egy adatbázison arra a pontra, amelyet az adatbázis lefoglalja, és eléri a 100%-os DTU-használatot, akkor az adatbázis teljesítménye is érintett, ami potenciálisan lelassítja a lekérdezési válaszidőt.

**Rövid távon** érdemes lehet felskálázást készíteni az adatbázisról, hogy további erőforrásokat biztosítson, vagy a bérlőket a több-bérlős adatbázisból távolítsa el (a több-bérlős adatbázisból egy önálló adatbázisba helyezi őket).

Az adatbázis teljesítményének növelése érdekében érdemes **lehet a lekérdezések** optimalizálása vagy az indexelés használata. Attól függően, hogy az alkalmazás mennyire érzékeny a teljesítményre, az ajánlott eljárás az adatbázis skálázása, mielőtt eléri a 100%-os DTU-használatot. Használjon olyan riasztást, amely előre figyelmezteti Önt.

Egy foglalt adatbázist szimulálhat úgy, hogy növeli a generátor által létrehozott terhelést. A bérlők gyakrabban törtek fel, a több-bérlős adatbázis terhelésének növelése pedig az egyes bérlők követelményeinek módosítása nélkül. Az adatbázis vertikális felskálázása egyszerűen elvégezhető a portálon vagy a PowerShellből. A gyakorlat során a Portalt használjuk.

1. Állítsa be a *$DemoScenario*  =  **3** értéket, _és hozzon létre egy terhelést az adatbázison_ , amely az egyes bérlők által megkövetelt maximális terhelés módosítása nélkül növelje az adatbázis összesített terhelésének intenzitását.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.
1. Nyissa meg a Azure Portal **tenants1** -adatbázisát.

Figyelje meg a nagyobb adatbázis-DTU használatát a felső diagramon. Eltarthat néhány percig, amíg az új magasabb terhelés betöltődik, de az adatbázisnak gyorsan kell megjelennie a maximális kihasználtság eléréséhez, és mivel a terhelés folyamatosan az új mintára kerül, gyorsan túlterheli az adatbázist.

1. Az adatbázis vertikális felskálázásához kattintson a **díjszabás (DTU skálázás)** elemre a beállítások panelen.
1. Módosítsa a **DTU** beállítást **100**-re. 
1. Kattintson az **alkalmaz** gombra az adatbázis skálázására irányuló kérelem elküldéséhez.

Lépjen vissza a **tenants1**  >  **áttekintésre** a figyelési diagramok megtekintéséhez. Figyelje meg, hogy az adatbázis további erőforrásokkal való ellátása milyen hatással van (bár néhány Bérlővel és véletlenszerű betöltéssel nem mindig könnyű megtekinteni, amíg egy ideig nem fut le). A diagramok megtekintése közben vegye figyelembe, hogy a felső diagramon a 100% már a 100 DTU jelöli, míg az alsó diagram 100%-a továbbra is 50 DTU.

Az adatbázisok a folyamat során végig online állapotban maradnak, és teljes mértékben rendelkezésre állnak. Az alkalmazás kódját mindig meg kell adni az eldobott kapcsolatok újrapróbálkozásához, így a rendszer újra csatlakozik az adatbázishoz.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Új bérlő kiépítése a saját adatbázisában 

A több-bérlős többbérlős modell lehetővé teszi, hogy új bérlőt építsen ki egy több-bérlős adatbázisban más bérlők mellett, vagy a bérlőt a saját adatbázisában is kiépítse. A bérlőnek a saját adatbázisában való kiépítés révén a különálló adatbázisban rejlő elkülönítés előnyeit élvezheti, így a bérlők teljesítménye a többitől függetlenül kezelhető, így a bérlők egymástól függetlenül, másoktól függetlenül is visszaállíthatók. Dönthet például úgy, hogy egy több-bérlős adatbázisban, és prémium szintű ügyfeleket helyez el az egyes adatbázisokban.  Ha elszigetelt egybérlős adatbázisokat hoz létre, azok továbbra is kezelhetők együtt egy rugalmas készletben az erőforrás-költségek optimalizálása érdekében.

Ha már kiépített egy új bérlőt a saját adatbázisában, ugorja át a következő néhány lépést.

1. A **POWERSHELL ISE**-ben nyissa meg a... \\ Tanulási modulok \\ ProvisionTenants \\ *Demo-ProvisionTenants.ps1*. 
1. Modify **$TenantName = "Salix salsa"** és **$VenueType = "Dance"**
1. **$Scenario**  =  **2** beállítása, _bérlő kiépítése egy új, egybérlős adatbázisban_
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A szkript külön adatbázisban fogja kiépíteni ezt a bérlőt, regisztrálja az adatbázist és a bérlőt a katalógusban, majd megnyitja a bérlő események lapját a böngészőben. Frissítse az Events hub oldalt, és látni fogja, hogy a "Salix salsa" a helyszínként lett hozzáadva.

## <a name="manage-performance-of-an-individual-database"></a>Önálló adatbázis teljesítményének kezelése

Ha a több-bérlős adatbázison belüli egyetlen bérlő tartósan magas terhelést tapasztal, akkor az adatbázis-erőforrások dominálnak, és más bérlők is befolyásolhatják ugyanabban az adatbázisban. Ha a tevékenység várhatóan egy ideig továbbra is fennáll, a bérlő átmenetileg kihelyezhető az adatbázisból és a saját egybérlős adatbázisba. Ez lehetővé teszi a bérlő számára, hogy a szükséges további erőforrásokkal rendelkezzen, és teljesen elkülönítse azt a többi bérlőtől.

Ez a gyakorlat szimulálja a Salix salsa hatását, amely nagy terhelést tapasztal, amikor a jegyek egy népszerű eseményre kerülnek.

1. Nyissa meg a.. \\ . *Demo-PerformanceMonitoringAndManagement.ps1* szkript.
1. Állítsa be **$DemoScenario = 5**, _normál terhelés létrehozása, valamint egy adott bérlő nagy terhelését (körülbelül 90 DTU)._
1. Set **$SingleTenantName = Salix salsa**
1. Futtassa a szkriptet az **F5** billentyűvel.

A figyelési diagramok megtekintéséhez lépjen a portálra, és navigáljon a **salixsalsa**  >  **áttekintéséhez** . 

## <a name="other-performance-management-patterns"></a>Egyéb teljesítmény-felügyeleti minták

**Bérlői önkiszolgáló skálázás**

Mivel a méretezés egy, a felügyeleti API-n keresztül könnyen meghívható feladat, egyszerűen létrehozható a bérlői adatbázisok méretezése a bérlői alkalmazásokban, és az SaaS-szolgáltatás szolgáltatásaként is használható. Például a bérlők saját maguk adminisztrálhatják a vertikális fel- és leskálázást, ami előfordulhat, hogy közvetlen kapcsolatban áll a számlázásukkal.

**Az adatbázisok méretezése a használati mintáknak megfelelően felfelé és lefelé**

Ha az összesített bérlői használat kiszámítható használati mintákat követ, a Azure Automation segítségével ütemezhet egy adatbázist egy ütemezett felfelé és lefelé. Tegyük fel például, hogy a 6. és a (z) időpontot lefelé kell méretezni a hétköznapokon, ha tudja, hogy az erőforrásokra vonatkozó követelmények csökkennek.

## <a name="next-steps"></a>Következő lépések

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * Több-bérlős adatbázis használatának szimulálása egy megadott Load Generator futtatásával
> * Figyelje meg az adatbázist úgy, ahogy válaszol a terhelés növekedésére
> * Az adatbázis vertikális felskálázása a megnövekedett adatbázis-terhelésre reagálva
> * Bérlő kiépítése egyetlen bérlős adatbázisba

## <a name="additional-resources"></a>További források

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure Automation](../../automation/automation-intro.md)