---
title: Optimalizálja SQL Server-környezetét Azure Monitorkal | Microsoft Docs
description: A Azure Monitor használatával az SQL Health-ellenőrzési megoldással rendszeres időközönként felmérhetővé teheti a környezetek kockázatait és állapotát.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/05/2020
ms.openlocfilehash: 0f2319ea6ba314c08a67651667837f05df5765a1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101723231"
---
# <a name="optimize-your-sql-environment-with-the-sql-server-health-check-solution-in-azure-monitor"></a>Az SQL-környezet optimalizálása a SQL Server Health-ellenőrzési megoldással Azure Monitor

![SQL Health-ellenőrzési szimbólum](./media/sql-assessment/sql-assessment-symbol.png)

Az SQL Health-ellenőrzési megoldással rendszeres időközönként felméri a kiszolgálói környezetek kockázatait és állapotát. Ez a cikk segítséget nyújt a megoldás telepítéséhez, hogy kijavítsa a lehetséges problémákra vonatkozó lépéseket.

Ez a megoldás a telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját tartalmazza. A javaslatok 6 Kiemelt területre vannak kategorizálva, amelyek segítségével gyorsan megismerheti a kockázatokat, és elvégezheti a megfelelő lépéseket.

A javaslatok a Microsoft-mérnökök által több ezer ügyfél-látogatásból nyert ismeretekre és tapasztalatokra épülnek. Az egyes javaslatok útmutatást nyújtanak arról, hogy miért fontos a probléma, és hogyan valósítja meg a javasolt módosításokat.

Kiválaszthatja a szervezete számára legfontosabb fókusz területeket, és nyomon követheti az előrehaladást a kockázatmentes és kifogástalan állapotú környezetek futtatásának irányába.

A megoldás hozzáadása és az értékelés befejezése után a fókusz területekre vonatkozó összefoglaló információk az **SQL Health-ellenőrzés** irányítópulton jelennek meg a környezetében lévő infrastruktúrához. Az alábbi szakaszok azt ismertetik, hogyan használhatók az **SQL Health-ellenőrzési** irányítópulton található információk, ahol megtekintheti és elvégezheti az SQL Server-infrastruktúrával kapcsolatos ajánlott műveleteket.

![SQL Health-ellenőrzési csempe képe](./media/sql-assessment/sql-healthcheck-summary-tile.png)

![SQL Health-ellenőrzési irányítópult képe](./media/sql-assessment/sql-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Előfeltételek

* Az SQL Health ellenőrzési megoldáshoz a .NET-keretrendszer 4.6.2 egy támogatott verzióját kell telepíteni minden olyan számítógépen, amelyen telepítve van a Microsoft monitoring Agent (MMA).  Az MMA-ügynököt a System Center 2016-Operations Manager és az Operations Manager 2012 R2, valamint a Azure Monitor használja.  
* A megoldás a 2012, 2014, 2016, 2017 és 2019 SQL Server verziót támogatja.
* Log Analytics munkaterület az SQL Health-ellenőrzési megoldás az Azure Marketplace-en való hozzáadásához a Azure Portalban. A megoldás telepítéséhez rendszergazdának vagy közreműködőnek kell lennie az Azure-előfizetésben.

  > [!NOTE]
  > A megoldás hozzáadása után a AdvisorAssessment.exe fájl hozzá lesz adva a-ügynökökkel rendelkező kiszolgálókhoz. A rendszer beolvassa és elküldi a konfigurációs adatfeldolgozást a felhőben lévő Azure Monitorba. A felhőszolgáltatás egy logikát alkalmaz a kapott adatokon, és rögzíti azokat.
  >
  >

A SQL Server-kiszolgálók állapot-ellenőrzésének végrehajtásához a következő támogatott módszerek egyikével kell ügynököt és kapcsolatot létesíteni a Azure Monitorhoz:

1. Telepítse a [Microsoft monitoring Agent (MMA)](../agents/agent-windows.md) programot, ha a kiszolgálót még nem figyeli a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2.
2. Ha a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2 rendszerrel van figyelve, és a felügyeleti csoport nincs integrálva a Azure Monitor-be, a kiszolgáló többplatformos lehet a Log Analytics, hogy adatokat gyűjtsön és továbbítson a szolgáltatásnak, és továbbra is a Operations Manager figyeli.  
3. Ellenkező esetben, ha a Operations Manager felügyeleti csoport integrálva van a szolgáltatással, hozzá kell adnia a tartományvezérlőket az adatgyűjtéshez a szolgáltatáshoz az [ügynök által felügyelt számítógépek hozzáadása](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) után, miután engedélyezte a megoldást a munkaterületen.  

A SQL Server ügynöke, amely egy Operations Manager felügyeleti csoportnak jelent jelentést, adatokat gyűjt, továbbít a hozzárendelt felügyeleti kiszolgálóhoz, majd közvetlenül egy felügyeleti kiszolgálóról küldi el a Azure Monitor.  Az adatOperations Manager-adatbázisok nem kerülnek be az adatforrásba.  

Ha a SQL Server Operations Manager figyeli, konfigurálnia kell egy Operations Manager futtató fiókot. További információért tekintse [meg az alábbi Azure Monitor Operations Manager futtató fiókokat](#operations-manager-run-as-accounts-for-log-analytics) .

## <a name="sql-health-check-data-collection-details"></a>Az SQL Health Check által végzett adatgyűjtés részletei
Az SQL Health-ellenőrzések a következő forrásokból gyűjtenek adatokat az Ön által engedélyezett ügynök használatával:

* Windows Management Instrumentation (WMI)
* Regisztrációs adatbázis
* Teljesítményszámlálók
* A dinamikus felügyeleti nézet eredményeinek SQL Server

Az adatok gyűjtése a SQL Server történik, és minden hét nap Log Analytics továbbítva.

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Futtató Operations Manager-fiókok a Log Analyticshez
Log Analytics az Operations Manager ügynök és a felügyeleti csoport használatával gyűjti és küldi az adatokat a Log Analytics szolgáltatásnak. A Log Analytics a felügyeleti csomagokon alapuló munkaterheléseket biztosít az értéknövelt szolgáltatások biztosításához. Minden munkaterheléshez a felügyeleti csomagok más biztonsági környezetben, például egy tartományi felhasználói fiókban való futtatásához munkaterhelés-specifikus jogosultságok szükségesek. A hitelesítő adatokat egy Operations Manager futtató fiók konfigurálásával kell megadnia.

Az alábbi információk segítségével állíthatja be az SQL Health-ellenőrzési Operations Manager futtató fiókot.

### <a name="set-the-run-as-account-for-sql-health-check"></a>Az SQL Health Check futtató fiókjának beállítása
 Ha már használja a SQL Server felügyeleti csomagot, akkor ezt a futtató konfigurációt kell használnia.

#### <a name="to-configure-the-sql-run-as-account-in-the-operations-console"></a>Az SQL futtató fiók konfigurálása az operatív konzolon
> [!NOTE]
> A felügyeleti csomagban található alapértelmezett munkafolyamatok a helyi rendszerfiók biztonsági környezetében futnak. Ha a Microsoft monitoring agentet közvetlenül a szolgáltatáshoz csatlakoztatja ahelyett, hogy közvetlenül egy Operations Manager felügyeleti csoportnak kellene bejelentkeznie, ugorja át az alábbi 1-5 lépéseket, és futtassa a T-SQL vagy a PowerShell-mintát, és adja meg az NT AUTHORITY\SYSTEM nevet a felhasználónévként.
>
>

1. A Operations Managerban nyissa meg az operatív konzolt, majd kattintson az **Adminisztráció** elemre.
2. A **futtató konfiguráció** alatt kattintson a **profilok** elemre, majd nyissa meg **SQL Assessment futtató profilt**.
3. A **Futtató fiókok** lapon kattintson **Hozzáadás** lehetőségre.
4. Válasszon olyan Windows rendszerű futtató fiókot, amely tartalmazza a SQL Serverhoz szükséges hitelesítő adatokat, vagy kattintson az **új** elemre egy létrehozásához.

   > [!NOTE]
   > A futtató fiók típusának Windowsnak kell lennie. A futtató fióknak a helyi Rendszergazdák csoport részét kell képeznie a SQL Server példányokat üzemeltető összes Windows-kiszolgálón.
   >
   >
5. Kattintson a **Mentés** gombra.
6. Módosítsa, majd hajtsa végre az alábbi T-SQL-mintát minden SQL Server példányon, hogy az állapot-ellenőrzés elvégzéséhez szükséges minimális engedélyeket adja meg a futtató fiók számára. Ezt azonban nem kell megtennie, ha egy futtató fiók már része a sysadmin (rendszergazda) kiszolgálói szerepkörnek SQL Server példányokon.

```
    ---
    -- Replace <UserName> with the actual user name being used as Run As Account.
    USE master

    -- Create login for the user, comment this line if login is already created.
    CREATE LOGIN [<UserName>] FROM WINDOWS

    -- Grant permissions to user.
    GRANT VIEW SERVER STATE TO [<UserName>]
    GRANT VIEW ANY DEFINITION TO [<UserName>]
    GRANT VIEW ANY DATABASE TO [<UserName>]

    -- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
    -- NOTE: This command must be run anytime new databases are added to SQL Server instances.
    EXEC sp_msforeachdb N'USE [?]; CREATE USER [<UserName>] FOR LOGIN [<UserName>];'

```

#### <a name="to-configure-the-sql-run-as-account-using-windows-powershell"></a>Az SQL futtató fiók konfigurálása a Windows PowerShell használatával
Nyisson meg egy PowerShell-ablakot, és futtassa a következő parancsfájlt, miután frissítette az adatait:

```
    import-module OperationsManager
    New-SCOMManagementGroupConnection "<your management group name>"

    $profile = Get-SCOMRunAsProfile -DisplayName "SQL Assessment Run As Profile"
    $account = Get-SCOMrunAsAccount | Where-Object {$_.Name -eq "<your run as account name>"}
    Set-SCOMRunAsProfile -Action "Add" -Profile $Profile -Account $Account
```

## <a name="understanding-how-recommendations-are-prioritized"></a>A javaslatok rangsorolásának ismertetése
Minden javaslat egy súlyozási értéket kap, amely a javaslat relatív fontosságát azonosítja. Csak a tíz legfontosabb javaslat jelenik meg.

### <a name="how-weights-are-calculated"></a>A súlyozás kiszámítása
A súlyozások három fő tényezőn alapuló összesített értékek:

* Az  észlelt probléma valószínűleg problémákat okoz. A nagyobb valószínűség a javaslat nagyobb összesített pontszáma.
* A probléma *hatása* a szervezeten belül, ha problémát okoz. A nagyobb hatás a javaslat nagyobb összesített pontszáma.
* A javaslat megvalósításához szükséges *erőfeszítés* . A nagyobb erőfeszítés a javaslat kisebb összesített pontszámának felel meg.

Az egyes javaslatok súlyozása az egyes fókuszokhoz rendelkezésre álló teljes pontszám százalékában van kifejezve. Ha például egy, a biztonsági és megfelelőségi területen található javaslat 5%-os pontszámmal rendelkezik, akkor a javaslat megvalósítása 5%-kal növeli az általános biztonsági és megfelelőségi pontszámot.

### <a name="focus-areas"></a>Fókuszterületek
**Biztonság és megfelelőség** – ez a kiemelt terület a lehetséges biztonsági fenyegetésekkel és szabálysértésekkel, a vállalati házirendekkel, valamint a technikai, jogi és szabályozási megfelelőségi követelményekkel kapcsolatos javaslatokat mutatja be.

**Rendelkezésre állás és Üzletmenet-folytonosság** – ez a fókusz a szolgáltatás rendelkezésre állására, az infrastruktúra rugalmasságára és az üzleti védelemre vonatkozó javaslatokat mutatja be.

**Teljesítmény és méretezhetőség** – ez a fókusz a szervezet informatikai infrastruktúrájának növekedését segítő ajánlásokat mutat be, így biztosíthatja, hogy az informatikai környezet megfeleljen a jelenlegi teljesítménybeli követelményeknek, és képes legyen reagálni az infrastrukturális igények változására.

**Frissítés, áttelepítés és üzembe helyezés** – ez a fókusz olyan javaslatokat mutat be, amelyek segítséget nyújtanak a meglévő infrastruktúrához való SQL Server frissítéséhez, áttelepítéséhez és üzembe helyezéséhez.

**Műveletek és monitorozás** – ez a fókusz az informatikai műveletek egyszerűsítését, a megelőző karbantartás megvalósítását és a teljesítmény maximalizálása érdekében ajánlásokat mutat be.

**Módosítási és konfigurálási felügyelet** – ez a fókusz terület a napi műveletek védelméhez nyújt javaslatokat, és gondoskodik arról, hogy a változások ne legyenek negatív hatással az infrastruktúrára, a változás-ellenőrzési eljárások létrehozására, valamint a rendszerkonfigurációk nyomon követésére és naplózására.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Érdemes 100%-ra törekedni minden fókuszterületen?
Nem feltétlenül. A javaslatok a Microsoft-mérnökök által több ezer ügyfél-látogatáson keresztül szerzett ismeretekre és tapasztalatokra épülnek. Azonban két kiszolgálói infrastruktúra sem azonos, és a konkrét javaslatok többé vagy kevésbé fontosak lehetnek. Előfordulhat például, hogy bizonyos biztonsági javaslatok kevésbé fontosak, ha a virtuális gépek nem lesznek elérhetők az internethez. Egyes rendelkezésre állási javaslatok kevésbé fontosak lehetnek az alacsony prioritású ad hoc adatgyűjtést és jelentéskészítést biztosító szolgáltatások esetében. Az érett üzleti tevékenység szempontjából fontos problémák kevésbé fontosak az indításhoz. Érdemes lehet azonosítani, hogy mely Kiemelt területek legyenek a prioritásai, és hogyan változnak meg a pontszámok időbeli változása.

Minden javaslat tartalmaz útmutatást arról, hogy miért fontos. Ennek az útmutatónak a segítségével kiértékelheti, hogy az ajánlás alkalmazása megfelelő-e Önnek, az informatikai szolgáltatások természetétől és a szervezet üzleti igényeinek megfelelően.

## <a name="use-health-check-focus-area-recommendations"></a>Az állapot-ellenőrzés fókuszterület-javaslatainak használata
Mielőtt felhasználja a felmérési megoldást a Azure Monitorban, telepítenie kell a megoldást.  A telepítését követően megtekintheti a javaslatok összefoglalását az Azure Portal Azure Monitor **Áttekintés** LAPJÁN található SQL Health ellenőrzés csempével.

Tekintse meg az infrastruktúra összesített megfelelőségi értékeléseit, majd a részletes ajánlásokat.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>A fókusz területre vonatkozó javaslatok megtekintése és a kijavítani kívánt művelet elvégzése
1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Monitorozás** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Monitorozás** lehetőséget.
3. **A menü áttekintések szakaszában válassza** a **továbbiak** lehetőséget.  
4. Az **Áttekintés** lapon kattintson az **SQL Health-ellenőrzési** csempére.
5. Az **Állapot ellenőrzése** oldalon tekintse át az összefoglaló információkat az egyik fókuszban lévő panelen, majd kattintson az egyikre a fókuszhoz tartozó javaslatok megtekintéséhez.
6. A fókusz oldalain megtekintheti a környezetére vonatkozó rangsorolt javaslatokat. Az **érintett objektumok** alatt található javaslatra kattintva megtekintheti a javaslat hátterének részleteit.<br><br> ![SQL Health-ellenőrzési javaslatok képe](./media/sql-assessment/sql-healthcheck-dashboard-02.png)<br>
7. A **javasolt műveletekben** javasolt korrekciós műveleteket is végrehajthat. Az elem megcímzése után a későbbi értékelések rögzítik a javasolt műveleteket, és a megfelelőségi pontszám növekedni fog. A javított elemek **átadott objektumként** jelennek meg.

## <a name="ignore-recommendations"></a>Javaslatok figyelmen kívül hagyása
Ha olyan javaslatok vannak, amelyeket figyelmen kívül szeretne hagyni, létrehozhat egy szövegfájlt, amelyet Azure Monitor fog használni, hogy megakadályozza a javaslatok megjelenését az értékelés eredményeiben.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>A figyelmen kívül hagyható javaslatok azonosítása
1. A Azure Monitor menüben kattintson a **naplók** elemre.
2. A következő lekérdezéssel listázhatja azokat a javaslatokat, amelyek a környezetében lévő számítógépeken sikertelenek voltak.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
    A napló lekérdezését bemutató képernyőkép:<br><br> ![sikertelen javaslatok](./media/sql-assessment/sql-assess-failed-recommendations.png)<br>

3. Válassza ki a figyelmen kívül hagyni kívánt ajánlásokat. A következő eljárásban a RecommendationId értékeit fogja használni.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt szövegfájl létrehozása és használata
1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.
2. Illessze be vagy írja be az egyes RecommendationId minden olyan javaslathoz, amelyet szeretne figyelmen kívül hagyni Azure Monitor külön sorban, majd mentse és zárjuk be a fájlt.
3. Helyezze a fájlt a következő mappába az összes olyan számítógépen, amelyen Azure Monitor szeretné figyelmen kívül hagyni a javaslatokat.
   * A Microsoft monitoring agenttel rendelkező számítógépeken (közvetlenül vagy Operations Manageron keresztül csatlakoztatott) – *SYSTEMDRIVE*: \Program Files\Microsoft monitoring Agent\Agent
   * A Operations Manager felügyeleti kiszolgálón – *rendszermeghajtó*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * A Operations Manager 2016 felügyeleti kiszolgálón – *rendszermeghajtó*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>A javaslatok figyelmen kívül hagyása
1. Miután a következő ütemezett értékelést futtatta, alapértelmezés szerint 7 naponta, a megadott javaslatok figyelmen kívül lesznek hagyva, és nem jelennek meg az értékelési irányítópulton.
2. A következő naplóbeli keresési lekérdezésekkel listázhatja az összes figyelmen kívül hagyott javaslatot.

    ```
    SQLAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```
3. Ha később úgy dönt, hogy szeretné megtekinteni a figyelmen kívül hagyott javaslatokat, távolítson el minden IgnoreRecommendations.txt fájlt, vagy távolítsa el a RecommendationIDs.

## <a name="sql-health-check-solution-faq"></a>Az SQL Health Check megoldás – gyakori kérdések

*Milyen ellenőrzéseket végeznek a SQL Assessment megoldás?*

* A következő lekérdezés az összes jelenleg elvégzett ellenőrzés leírását tartalmazza:

```Kusto
SQLAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Az eredmények az Excel programba exportálhatók további áttekintés céljából.


*Milyen gyakran fut az állapot-ellenőrzési funkció?*

* Az ellenőrzési hét naponta fut.

*Van mód arra, hogy beállítsa, milyen gyakran fusson az ellenőrzések?*

* Jelenleg nem.

*Ha a rendszer felderít egy másik kiszolgálót az SQL Health Check megoldás hozzáadása után, akkor a rendszer ellenőrzi?*

* Igen, a felderítés után a rendszer a hét minden napján ellenőrzi, hogy be van-e jelölve.

*Ha a kiszolgáló leszerelése megtörtént, akkor mikor lesz eltávolítva az állapot-ellenőrzési rendszerből?*

* Ha egy kiszolgáló 3 hétig nem küldi el az adatküldést, a rendszer eltávolítja.

*Mi az adatgyűjtés folyamatának neve?*

* AdvisorAssessment.exe

*Mennyi időt vesz igénybe az adatok gyűjtése?*

* A kiszolgálón a tényleges adatgyűjtés körülbelül 1 órát vesz igénybe. A nagy számú SQL-példánnyal vagy-adatbázissal rendelkező kiszolgálókon hosszabb időt is igénybe vehet.

*Milyen típusú adatokat gyűjt?*

* A rendszer a következő típusú adatokat gyűjti össze:
  * WMI
  * Regisztrációs adatbázis
  * Teljesítményszámlálók
  * SQL dinamikus felügyeleti nézetek (DMV).

*Be lehet állítani az adatok gyűjtésének módját?*

* Jelenleg nem.

*Miért van szükség egy futtató fiók konfigurálására?*

* SQL Server esetében néhány SQL-lekérdezés fut. Ahhoz, hogy futtatni lehessen őket, az SQL-alapú futtató fiókot kell használni.  Emellett a WMI lekérdezéséhez helyi rendszergazdai hitelesítő adatokra van szükség.

*Miért csak az első 10 ajánlás jelenjen meg?*

* A feladatok teljes körű listájának megadása helyett javasoljuk, hogy először a rangsorolt javaslatok kezelésére koncentráljon. A megoldásuk után további javaslatok válnak elérhetővé. Ha szeretné megtekinteni a részletes listát, az összes javaslatot megtekintheti az Log Analytics log-keresés használatával.

*Van lehetőség arra, hogy figyelmen kívül hagyja a javaslatot?*

* Igen, lásd a fenti [javaslatok mellőzése](#ignore-recommendations) szakaszt.

## <a name="next-steps"></a>Következő lépések
* A [lekérdezésekkel](../logs/log-query-overview.md) megtudhatja, hogyan elemezheti a részletes SQL Health-ellenőrzési információkat és javaslatokat.

