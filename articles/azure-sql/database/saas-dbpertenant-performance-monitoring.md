---
title: 'SaaS-alkalmazás: számos adatbázis teljesítményének figyelése'
description: Azure SQL Database teljesítményének figyelése és kezelése több-bérlős SaaS-alkalmazásokban
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
ms.openlocfilehash: 21c0a7a3fe6d5be9d99ea53dbfa74cf72e163272
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92780665"
---
# <a name="monitor-and-manage-performance-of-azure-sql-database-in-a-multi-tenant-saas-app"></a>Azure SQL Database teljesítményének figyelése és kezelése több-bérlős SaaS-alkalmazásokban
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Ebben az oktatóanyagban az SaaS-alkalmazásokban használt fő teljesítmény-kezelési forgatókönyveket vizsgálja. A betöltési generátor használatával az összes bérlői adatbázison szimulálhatja a tevékenységeket, és a SQL Database és a rugalmas készletek beépített figyelési és riasztási funkcióit mutatjuk be.

A Wingtip tickets SaaS-adatbázis a bérlői alkalmazásokban egy egybérlős adatmodellt használ, ahol minden egyes helyszín (bérlő) saját adatbázissal rendelkezik. Sok más SaaS-alkalmazáshoz hasonlóan a bérlői számítási feladatok várt mintája kiszámíthatatlan és szórványos. Ez a gyakorlatban azt jelenti, hogy a jegyeladásokra bármikor sor kerülhet. A tipikus adatbázis-használati minta kihasználásához a bérlői adatbázisok rugalmas készletekre vannak telepítve. A rugalmas készletek optimalizálják a megoldások költségeit azáltal, hogy számos adatbázis között osztják meg az erőforrásokat. Ennél a típusú mintánál fontos az adatbázis és a készleterőforrások felhasználásának figyelése annak biztosítása érdekében, hogy a terhelések egyenletesen oszoljanak meg a készletek közt. Emellett azt is biztosítani kell, hogy az egyes adatbázisok elengedő mennyiségű erőforrással rendelkezzenek, és hogy a készletek ne érjék el a maximális [eDTU](purchasing-models.md#dtu-based-purchasing-model)-korlátot. Ez az oktatóanyag különböző módszereket ismertet az adatbázisok és készletek figyelésére és kezelésére, valamint a számítási feladatok változásaira adott korrekciós műveletek elvégzésére.

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> 
> * A bérlői adatbázisok használatának szimulálása egy adott terhelésgenerátor futtatásával
> * A bérlői adatbázisok megnövekedett terhelésre adott válaszának megfigyelése
> * A rugalmas készlet vertikális felskálázása az adatbázis megnövekedett terhelésére adott válaszul
> * Egy második rugalmas készlet kiépítése az adatbázis-tevékenységek terhelésének kiegyenlítésére


Az oktatóanyag teljesítéséhez meg kell felelnie az alábbi előfeltételeknek:

* A Wingtip jegyek SaaS-adatbázisa egy bérlői alkalmazáson van üzembe helyezve. Ha kevesebb, mint öt perc alatt kíván üzembe helyezni, tekintse meg [a Wingtip tickets SaaS-adatbázis üzembe helyezése és megismerése bérlői alkalmazásokban](./saas-dbpertenant-get-started-deploy.md)
* Az Azure PowerShell telepítve van. Részletes információk: [Ismerkedés az Azure PowerShell-lel](/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>A SaaS teljesítmény-felügyeleti mintáinak bemutatása

Az adatbázisteljesítmény-kezelés a teljesítményadatok fordításából és elemzéséből, majd az adatokra való reagálásból áll. Ez tulajdonképpen a paraméterek módosítását jelenti, miáltal az alkalmazás válaszideje elfogadható szinten marad. Több bérlő üzemeltetése esetén a rugalmas készletek költséghatékony megoldást biztosítanak a nem kiszámítható számítási feladatokkal rendelkező adatbázisok egy csoportjának erőforrásainak biztosításához és kezeléséhez. Bizonyos számításifeladat-mintáknál már akár két S3-adatbázist is előnyös lehet készletben kezelni.

![alkalmazás diagramja](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

A készleteket és a készletekben lévő adatbázisokat figyelni kell, hogy a teljesítmény elfogadható tartományokon belül maradjon. Állítsa be úgy a készlet konfigurációját, hogy megfeleljen az összes adatbázis összesített munkaterhelésének, így biztosítva, hogy a készlet Edtu megfelelőek legyenek a teljes munkaterhelés számára. Állítsa be az adatbázisonkénti minimális és maximális eDTU-értékeket az alkalmazás specifikus igényei szerint.

### <a name="performance-management-strategies"></a>Teljesítménykezelési stratégiák

* A teljesítmény manuális figyelése érdekében a leghatékonyabb olyan riasztások beállítása, amelyek akkor lépnek érvénybe, **amikor az adatbázisok vagy készletek elkóborolnak a normál tartományokból**.
* A készlet összesített számítási méretének rövid távú ingadozására való reagáláshoz a **készlet eDTU szintje felfelé vagy lefelé is méretezhető**. Ha az ingadozás rendszeres vagy kiszámítható, akkor **a készlet beállítható úgy, hogy a skálázás automatikusan ütemezve legyen**. Beállítható például a vertikális leskálázás, amikor előre láthatóan kevés lesz a számítási feladat, például éjjelente vagy a hétvégi napokon.
* A hosszabb távú ingadozásokra vagy az adatbázisok számának változására válaszul **az egyes adatbázisok áthelyezhetők másik készletekbe**.
* Az *egyes* adatbázisok egyéni adatbázis-terhelésének rövid távú növekedésére való reagáláshoz **létrehozhat egy készletet, és hozzárendelheti az egyes számítási méreteket**. A terhelés csökkenésével az adatbázis visszahelyezhető a készletbe. Ha ez előre ismert, az adatbázisok áthelyezhetők megelőző jelleggel, így biztosítva, hogy az adatbázis mindig rendelkezik a szükséges erőforrásokkal, és hogy elkerülje a készlet más adatbázisainak hatását. Ha ez a szükséglet előre kiszámítható, például ha egy helyszín nagy mennyiségű növekedésre számít a jegyeladásokban egy népszerű esemény miatt, akkor ez a kezelési viselkedés integrálható az alkalmazásba.

Az [Azure Portal](https://portal.azure.com) a legtöbb erőforráshoz beépített figyelési és riasztási lehetőségeket biztosít. A figyelés és a riasztás az adatbázisokon és a készleteken érhető el. Ez a beépített figyelési és riasztási erőforrás-specifikus megoldás, ezért érdemes kis mennyiségű erőforrást használni, de nem nagyon kényelmes, ha sok erőforrással dolgozik.

Nagy mennyiségű forgatókönyv esetén, ahol számos erőforrással dolgozik, [Azure monitor naplókat](./saas-dbpertenant-log-analytics.md) is használhat. Ez egy különálló Azure-szolgáltatás, amely a Log Analytics munkaterületen összegyűjtött, kibocsátott naplók elemzését teszi lehetővé. Azure Monitor naplók több szolgáltatásból is gyűjthetik a telemetria, és a riasztások lekérdezésére és beállítására használhatók.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>A Wingtip tickets SaaS-adatbázis beszerzése bérlői alkalmazás parancsfájljai alapján

A Wingtip jegyek SaaS több-bérlős adatbázis-parancsfájljai és az alkalmazás forráskódja a [WingtipTicketsSaaS-DbPerTenant GitHub-](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) tárházban érhető el. Tekintse meg az [általános útmutatót](saas-tenancy-wingtip-app-guidance-tips.md) a Wingtip tickets SaaS-parancsfájlok letöltésének és feloldásának lépéseihez.

## <a name="provision-additional-tenants"></a>További bérlők kiépítése

Noha a készletek használata már két S3-adatbázis esetén is költséghatékony lehet, minél több adatbázis található egy készletben, a költséghatékonyság mértéke annál jobban nő. Annak érdekében, hogy a teljesítményfigyelés és -kezelés nagy léptékben való használatát kellőképpen szemléltetni lehessen, jelen oktatóanyaghoz legalább 20 üzembe helyezett adatbázis szükséges.

Ha egy korábbi oktatóanyagban már kiépített bérlők kötegét, ugorjon a [használat szimulálása az összes bérlői adatbázison](#simulate-usage-on-all-tenant-databases) szakaszra.

1. A **POWERSHELL ISE**-ben nyissa meg a... \\ Tanulási modulok \\ teljesítményének figyelése és kezelése \\ *Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. **$DemoScenario**  =  **1** beállítása, **bérlők kötegének kiépítése**
1. A szkript futtatásához nyomja le az **F5** billentyűt.

A szkript kevesebb mint öt perc alatt 17 bérlőt helyez üzembe.

A *New-TenantBatch* parancsfájl a bérlők kötegét létrehozó [Resource Manager](../../azure-resource-manager/index.yml) -sablonok beágyazott vagy csatolt készletét használja, amely alapértelmezés szerint átmásolja a katalógus-kiszolgáló adatbázis- **basetenantdb** az új bérlői adatbázisok létrehozásához, majd regisztrálja ezeket a katalógusban, végül pedig a bérlő nevét és a hely típusát. Ez összhangban van azzal, ahogyan az alkalmazás kiépít egy új bérlőt. A *basetenantdb* végrehajtott módosítások minden olyan új bérlőre érvényesek, amelyet később kiépítenek. Tekintse meg a [séma-kezelési oktatóanyagot](saas-tenancy-schema-management.md) , amelyből megtudhatja, hogyan teheti meg a séma módosításait a *meglévő* bérlői adatbázisokra (beleértve a *basetenantdb*

## <a name="simulate-usage-on-all-tenant-databases"></a>Az összes bérlői adatbázis használatának szimulálása

A *Demo-PerformanceMonitoringAndManagement.ps1* parancsfájlt, amely az összes bérlői adatbázison futó munkaterhelést szimulál. A terhelés a rendelkezésre álló betöltési forgatókönyvek egyikével jön létre:

| Bemutató | Eset |
|:--|:--|
| 2 | Normál intenzitású terhelés előállítása (körülbelül 40 DTU) |
| 3 | Terhelés létrehozása adatbázisonkénti hosszabb és gyakoribb adatlöketekkel|
| 4 | Az adatbázis magasabb DTU-sorozatával generált terhelés (körülbelül 80 DTU)|
| 5 | Normál terhelés és nagy terhelés létrehozása egyetlen bérlőn (körülbelül 95 DTU)|
| 6 | Kiegyensúlyozatlan terhelés létrehozása több készlet számára|

A terhelésgenerátor egy *szintetikus* CPU-terhelést alkalmaz az összes bérlői adatbázison. A generátor minden bérlői adatbázis számára elindít egy feladatot, amely időközönként meghív egy, a terhelést létrehozó tárolt eljárást. A terhelések szintje (eDTU-ban mérve), időtartama és időköze minden adatbázis esetén más és más, ezzel szimulálva a kiszámíthatatlan bérlői aktivitást.

1. A **POWERSHELL ISE**-ben nyissa meg a... \\ Tanulási modulok \\ teljesítményének figyelése és kezelése \\ *Demo-PerformanceMonitoringAndManagement.ps1*. Tartsa ezt a szkriptet nyitva, mivel az oktatóanyag során több különböző forgatókönyvet is futtatnia kell majd.
1. A **$DemoScenario**  =  **2** beállítása, a *normál intenzitású terhelés létrehozása*.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.

A Wingtip jegyek SaaS-adatbázisa egy SaaS-alkalmazás, a SaaS-alkalmazások valós terhelése pedig általában szórványos és kiszámíthatatlan. Ennek szimulálására a terhelésgenerátor az összes bérlő között elosztott, véletlenszerű terhelést hoz létre. A terhelési minta megjelenése több percet is igénybe vehet, ezért futtassa a Load Generatort 3-5 percre, mielőtt megpróbálja figyelni a terhelést a következő fejezetekben.

> [!IMPORTANT]
> A terhelésgenerátor feladatok sorozataként fut a helyi PowerShell-munkamenetben. Hagyja nyitva a *Demo-PerformanceMonitoringAndManagement.ps1* lapot! Ha bezárja a lapot vagy felfüggeszti a gép működését, a terhelésgenerátor leáll. A Load Generator olyan *feladattípusban* marad, amelyben a generátor elindítása után kiépített új bérlők terhelését generálja. A *CTRL-C* billentyűkombinációval leállítja az új feladatok meghívását, és kilép a parancsfájlból. A Load Generator továbbra is futni fog, de csak a meglévő bérlők esetében.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Erőforrás-használat figyelése a Azure Portal használatával

Az alkalmazott terhelést eredményező erőforrás-használat figyeléséhez nyissa meg a portált a bérlői adatbázisokat tartalmazó készletben:

1. Nyissa meg a [Azure Portal](https://portal.azure.com) , és keresse meg a *tenants1-DPT- &lt; &gt; User* kiszolgálót.
1. Görgessen lefelé, keresse meg a rugalmas készleteket, és kattintson a **Pool1** készletre. Ez a készlet tartalmazza az összes eddig létrehozott bérlői adatbázist.

Figyelje meg a **rugalmas készlet figyelését** és a **rugalmas adatbázis-figyelési** diagramokat.

A készlet Erőforrás-kihasználtsága a készlet összes adatbázisának összesített adatbázis-kihasználtsága. Az adatbázis-diagram az öt legforróbb adatbázist mutatja:

![Adatbázisábra](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Mivel a készletben további adatbázisok találhatók az első öt felett, a készlet kihasználtsága olyan tevékenységet mutat be, amely nem szerepel az első öt adatbázis-diagramon. További részletekért kattintson az **adatbázis-Erőforrás kihasználtsága** lehetőségre:

![adatbázis-Erőforrás kihasználtsága](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Teljesítményriasztások beállítása a készletben

Állítson be egy riasztást a készleten, amely \> 75%-os kihasználtságot indít el a következőképpen:

1. Nyissa meg a *Pool1* (a *tenants1- \<user\> DPT-* Server kiszolgálón) a [Azure Portal](https://portal.azure.com).
1. Kattintson a **Riasztási szabályok** elemre, majd a **+ Riasztás hozzáadása** gombra:

   ![riasztás hozzáadása](./media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Adjon meg egy nevet, például: **Magas DTU**.
1. Állítsa be a következő értékeket:
   * **Metrika = eDTU százalékos értéke**
   * **Feltétel = nagyobb, mint**
   * **Küszöbérték = 75**
   * **Időszak = az elmúlt 30 percben**
1. Adjon hozzá egy e-mail-címet a *további rendszergazdai e-mailek (ek)* mezőbe, és kattintson **az OK** gombra.

   ![riasztás beállítása](./media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Foglalt készlet vertikális felskálázása

Ha egy készlet összesített terhelési szintje addig növekszik, hogy teljesen lefoglalja a készletet és 100%-os eDTU-használatot ér el, az hatással van az adatbázisok egyéni teljesítményére, és lelassíthatja a lekérdezések válaszidejét a készletben található összes adatbázisban.

**Rövid távú**, érdemes lehet a készletet a további erőforrások biztosítására, illetve az adatbázisok a készletből való eltávolítására (más készletekbe való áthelyezésre vagy a készletből egy önálló szolgáltatási rétegre).

Az adatbázis teljesítményének növelése érdekében érdemes **lehet a lekérdezések** optimalizálása vagy az indexelés használata. Az alkalmazás teljesítményingadozásokra való érzékenységétől függően az ajánlott eljárás a készlet vertikális felskálázása még a 100%-os eDTU-használat elérése előtt. Használjon olyan riasztást, amely előre figyelmezteti Önt.

Foglalt készletet a generátor által létrehozott terhelés növelésével szimulálhat. Az adatbázisok egyre gyakoribbak, és a készlet összesített terhelésének növelése az egyes adatbázisok követelményeinek módosítása nélkül. A készlet vertikális felskálázása könnyedén elvégezhető a portálon vagy a PowerShellben. A gyakorlat során a Portalt használjuk.

1. Állítsa be a *$DemoScenario*  =  **3** értéket, _és hozzon létre egy terhelést az adatbázison_ , hogy az összesíti a készlet összesített terhelésének intenzitását, és ne módosítsa az egyes adatbázisok által igényelt maximális terhelést.
1. Nyomja le az **F5** billentyűt, hogy az összes bérlői adatbázist érje terhelés.

1. Lépjen a Azure Portal **Pool1** .

Figyelje meg a készlet eDTU használatát a felső diagramon. Eltarthat néhány percig, amíg az új magasabb terhelés betöltődik, de a készletet gyorsan látnia kell a maximális kihasználtság eléréséhez, és mivel a terhelés az új mintának megfelelően növekszik, gyorsan túlterheli a készletet.

1. A készlet vertikális felskálázásához kattintson a **Pool1** lap tetején található **készlet konfigurálása** elemre.
1. Állítsa a **Pool eDTU** beállítást **100**-re. A készlet eDTU-értékének módosítása nem módosítja az adatbázisonkénti beállításokat (ami továbbra is adatbázisonként legfeljebb 50 eDTU). Az adatbázison belüli beállításokat a **készlet konfigurálása** lap jobb oldalán tekintheti meg.
1. Kattintson a **Save (Mentés** ) gombra a készlet skálázására irányuló kérelem elküldéséhez.

Lépjen vissza a **Pool1**  >  **áttekintésre** a figyelési diagramok megtekintéséhez. Figyelje meg, hogy a készlet több erőforrással való ellátása milyen hatással van (bár kevés adatbázissal és véletlenszerű terheléssel, hogy a rendszer nem mindig könnyen látható, amíg egy ideig nem fut). A diagramok megtekintése közben vegye figyelembe, hogy a felső diagramon látható 100% most 100 eDTU-t jelent, míg az alsó diagramon látható 100% továbbra is 50 eDTU-t, mivel az adatbázisonkénti maximum változatlanul 50 eDTU.

Az adatbázisok a folyamat során végig online állapotban maradnak, és teljes mértékben rendelkezésre állnak. Abban a pillanatban, hogy minden adatbázis készen áll a készlet új eDTU-értékével való engedélyezésre, minden aktív kapcsolat megszakad. Az alkalmazás kódját mindig úgy kell megírni, hogy újrapróbálja a megszakított kapcsolatokat, így újra csatlakozni fog az adatbázishoz a felskálázott készletben.

## <a name="load-balance-between-pools"></a>Terheléselosztás a készletek között

A készlet felskálázása mellett másik lehetőségként létrehozhat egy második készletet és áthelyezhet abba adatbázisokat, hogy kiegyenlítse a két készlet terhelését. Ehhez az új készletet ugyanazon a kiszolgálón kell létrehozni, amelyen az első is megtalálható.

1. A [Azure Portal](https://portal.azure.com)nyissa meg a **tenants1-DPT- &lt; User &gt;** kiszolgálót.
1. Kattintson az **+ új készlet** lehetőségre egy készlet az aktuális kiszolgálón való létrehozásához.
1. A **rugalmas készlet** sablonján:

   1. Állítsa a **nevet** a *pool2 értéket* értékre.
   1. A tarifacsomagnál hagyja meg a **Standard készlet** beállítást.
   1. Kattintson a **Készlet beállítása** elemre,
   1. Készlet **eDTU** beállítása *50 eDTU*.
   1. Az **adatbázisok hozzáadása** lehetőségre kattintva megtekintheti a kiszolgálón található adatbázisok listáját, amelyeket hozzáadhat a *pool2 értéket*-hez.
   1. Válasszon ki 10 adatbázist az új készletbe való áthelyezéshez, majd kattintson a **kiválasztás** elemre. Ha már futtatta a Load Generatort, a szolgáltatás már tudja, hogy a teljesítményadatok nagyobb készletet igényelnek, mint az alapértelmezett 50 eDTU-méret, és azt ajánljuk, hogy a 100 eDTU beállítással kezdjen.

      ![ajánlás](./media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Ebben az oktatóanyagban hagyja meg az alapértelmezett értéket a 50 Edtu, majd kattintson ismét a **kijelölés** gombra.
   1. Kattintson az **OK** gombra az új készlet létrehozásához és a kiválasztott adatbázisok áthelyezéséhez.

A készlet létrehozása és az adatbázisok áthelyezése néhány percet vesz igénybe. Az adatbázisok áthelyezve maradnak online állapotban, és az utolsó pillanatig teljes mértékben elérhetők, ekkor minden nyitott kapcsolat bezárult. Ha némi újrapróbálkozási logikával rendelkezik, az ügyfelek az új készletben fognak csatlakozni az adatbázishoz.

Tallózással keresse meg a **pool2 értéket** (a *tenants1- \<user\> DPT-* Server kiszolgálón) a készlet megnyitásához és a teljesítményének figyeléséhez. Ha nem látja, várjon, amíg befejeződik az új készlet üzembe helyezése.

Ekkor láthatja, hogy az erőforrás-használat a *Pool1* megszakadt, és a *pool2 értéket* már hasonlóan betöltve.

## <a name="manage-performance-of-an-individual-database"></a>Önálló adatbázis teljesítményének kezelése

Ha a készletben lévő egyes adatbázisok tartós magas terhelést tapasztalnak, a készlet konfigurációjától függően előfordulhat, hogy a készlet erőforrásai dominálnak, és más adatbázisokra is hatással vannak. Ha a tevékenység várhatóan egy ideig továbbra is fennáll, akkor az adatbázis átmenetileg kikerül a készletből. Ez lehetővé teszi, hogy az adatbázis a szükséges további erőforrásokkal rendelkezzen, és elkülönítse azt a többi adatbázistól.

Ez a gyakorlat a Contoso Concert Hall magas terhelésének a hatását szimulálja, amikor megkezdődik a jegyek árusítása egy népszerű koncertre.

1. A **POWERSHELL ISE**-ben nyissa meg a... \\ *Demo-PerformanceMonitoringAndManagement.ps1* szkript.
1. Állítsa be **$DemoScenario = 5, normál terhelés létrehozása, valamint egy adott bérlő nagy terhelését (körülbelül 95 DTU).**
1. Állítsa be a **$SingleTenantDatabaseName = contosoconcerthall** értéket.
1. Futtassa a szkriptet az **F5** billentyűvel.


1. A [Azure Portal](https://portal.azure.com)tallózással keresse meg a *tenants1-DPT- \<user\>* Server adatbázisainak listáját. 
1. Kattintson a **contosoconcerthall** -adatbázisra.
1. Kattintson arra a készletre, amelyben a **contosoconcerthall** található. Keresse meg a készletet a **rugalmas készlet** szakaszban.

1. Vizsgálja meg a **rugalmas készlet figyelési** diagramját, és keresse meg a megnövelt készlet eDTU használatát. Egy-két perc után jelentkezik a magas terhelés, aminek következtében a készlet eléri a 100%-os kihasználtságot.
2. Tekintse át a **rugalmas adatbázis figyelésének** megjelenítését, amely az elmúlt órában legforróbb adatbázisokat mutatja. A *contosoconcerthall* -adatbázisnak hamarosan meg kell jelennie az öt legforróbb adatbázis egyikének.
3. **Kattintson a rugalmas adatbázis-figyelési** **diagramra** , és megnyílik az **adatbázis-Erőforrás kihasználtsága** oldal, amelyen bármelyik adatbázist nyomon követheti. Ez lehetővé teszi a *contosoconcerthall* -adatbázis megjelenítésének elkülönítését.
4. Az adatbázisok listájában kattintson a **contosoconcerthall** elemre.
5. Kattintson a **díjszabási réteg (Scale DTU)** elemre a **teljesítmény konfigurálása** lap megnyitásához, ahol beállíthat egy különálló számítási méretet az adatbázis számára.
6. Kattintson a **Standard** lapra a Standard csomag skálázási beállításainak megnyitásához.
7. Csúsztassa a **DTU csúszkát** jobbra a **100** DTU kiválasztásához. Vegye figyelembe, hogy ez megfelel az **S3** szolgáltatási célnak.
8. Az **alkalmaz** gombra kattintva helyezze át az adatbázist a készletből, és végezze el a *szabványos S3* -adatbázis.
9. A skálázás befejezése után figyelje meg a contosoconcerthall-adatbázis és a Pool1 hatását a rugalmas készlet és az adatbázis-lapokon.

Ha a contosoconcerthall-adatbázis nagy terhelését a rendszer azonnal visszaküldi a készletbe, azzal csökkentheti a költségeit. Ha ez nem egyértelmű, akkor beállíthat egy riasztást az adatbázison, amely akkor aktiválódik, ha a DTU használata a készleten belüli maximális érték alá csökken. Egy adatbázis készletbe történő áthelyezésének menetét az 5. gyakorlat írja le.

## <a name="other-performance-management-patterns"></a>Egyéb teljesítmény-felügyeleti minták

**Megelőző skálázás** A fenti gyakorlat során, ahol megvizsgálta, hogyan méretezhető egy elkülönített adatbázis, megismerte, hogy melyik adatbázist kell megkeresnie. Ha a contoso Concert Hall felügyeletét értesítette a közelgő wingtips, az adatbázist kihelyezték a készlet megelőző jelleggel. Máskülönben valószínűleg egy riasztást kellett volna beállítani a készleten vagy az adatbázison ahhoz, hogy észre lehessen venni, mi történik. Nem jó, ha az ilyen eseményekről úgy szerez tudomást, hogy a készletben található többi bérlő csökkenő teljesítményről panaszkodik. Ha a bérlő meg tudja jósolni, hogy milyen hosszan lesz szüksége további erőforrásokra, beállítható egy Azure Automation-runbook, amely pontosan ütemezi az adatbázis kivételét, majd visszahelyezését a készletbe.

**Bérlők általi önkiszolgáló skálázás** Mivel a skálázási feladat könnyen meghívható a felügyeleti API-n keresztül, így a bérlői adatbázisok méretezésének lehetősége könnyen beépíthető a bérlőoldali alkalmazásba, és felkínálható az SaaS-szolgáltatás egy funkciójaként. Például a bérlők saját maguk adminisztrálhatják a vertikális fel- és leskálázást, ami előfordulhat, hogy közvetlen kapcsolatban áll a számlázásukkal.

**Készletek használati mintákhoz igazított, ütemezett vertikális fel- és leskálázása**

Ha az összesített bérlői használat kiszámítható mintákat követ, az Azure Automationnel ütemezni tudja a készletek vertikális fel- és leskálázását. Például egy készletet hétköznapokon este 6 után leskálázhat, reggel 6 előtt pedig felskálázhat, ha tudja, hogy a két időpont között csökken az erőforrásigény.



## <a name="next-steps"></a>Következő lépések

Ezen oktatóanyag segítségével megtanulhatja a következőket:

> [!div class="checklist"]
> * A bérlői adatbázisok használatának szimulálása egy adott terhelésgenerátor futtatásával
> * A bérlői adatbázisok megnövekedett terhelésre adott válaszának megfigyelése
> * A rugalmas készlet vertikális felskálázása az adatbázis megnövekedett terhelésére adott válaszul
> * Egy második rugalmas készlet kiépítése az adatbázis-tevékenységek terhelésének kiegyenlítésére

[Egyetlen bérlő visszaállítása – oktatóanyag](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>További források

* További [oktatóanyagok, amelyek az Wingtip tickets SaaS-adatbázisra épülnek a bérlői alkalmazások üzembe helyezése](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials) után
* [Rugalmas SQL-készletek](elastic-pool-overview.md)
* [Azure Automation](../../automation/automation-intro.md)
* [Naplók Azure monitor](./saas-dbpertenant-log-analytics.md) – Azure monitor naplók beállítása és használata – oktatóanyag