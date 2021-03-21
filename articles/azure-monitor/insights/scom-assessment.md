---
title: System Center Operations Manager értékelése Azure Monitor
description: A System Center Operations Manager Health Check megoldással rendszeres időközönként felmérhetővé teheti a környezetek kockázatait és állapotát.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/25/2018
ms.openlocfilehash: 35ae1e09fd0a06014a747cef99631a7bfe2dee1c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101731408"
---
# <a name="optimize-your-environment-with-the-system-center-operations-manager-health-check-preview-solution"></a>A környezet optimalizálása a System Center Operations Manager Health Check (előzetes verzió) megoldással

![System Center Operations Manager Health Check szimbólum](./media/scom-assessment/scom-assessment-symbol.png)

A System Center Operations Manager Health Check megoldással rendszeres időközönként felmérhetővé teheti a System Center Operations Manager felügyeleti csoport kockázatait és állapotát. Ebből a cikkből megtudhatja, hogyan telepítheti, konfigurálhatja és használhatja a megoldást úgy, hogy a lehetséges problémák megoldásához javítsa a megfelelő lépéseket.

Ez a megoldás a telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját tartalmazza. A javaslatok négy kiemelt területre vannak kategorizálva, amelyek segítségével gyorsan megismerheti a kockázatokat, és elvégezheti a megfelelő lépéseket.

A javaslatok a Microsoft-mérnökök által több ezer ügyfél-látogatásból nyert ismeretekre és tapasztalatokra épülnek. Az egyes javaslatok útmutatást nyújtanak arról, hogy miért fontos a probléma, és hogyan valósítja meg a javasolt módosításokat.

Kiválaszthatja a szervezete számára legfontosabb fókusz területeket, és nyomon követheti az előrehaladást a kockázatmentes és kifogástalan állapotú környezetek futtatásának irányába.

A megoldás hozzáadása és az értékelés elvégzése után a fókusz területekre vonatkozó összefoglaló információk az infrastruktúra **System Center Operations Manager Health Check** irányítópultján jelennek meg. Az alábbi szakaszok azt ismertetik, hogyan használhatók a **System Center Operations Manager Health Check** irányítópulton található információk, ahol megtekintheti és elvégezheti a Operations Manager-környezethez javasolt műveleteket.

![System Center Operations Manager megoldás csempe](./media/scom-assessment/log-analytics-scom-healthcheck-tile.png)

![System Center Operations Manager Health Check irányítópult](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-01.png)

## <a name="installing-and-configuring-the-solution"></a>A megoldás telepítése és konfigurálása

A megoldás a Microsoft System Center 2012 Operations Manager Service Pack 1, a Microsoft System Center 2012 R2 Operations Manager, a Microsoft System Center 2016 Operations Manager, a Microsoft System Center 2016 Operations Manager és a Microsoft System Center Operations Manager 1807 esetében működik. A .NET-keretrendszer 4.6.2 egy támogatott verzióját kell telepíteni az egyes felügyeleti kiszolgálókon.

A megoldás telepítésekor és konfigurálásakor vegye figyelembe az alábbi információkat.

- Az állapot-ellenőrzési megoldás Log Analyticsban való használata előtt telepítenie kell a megoldást. Telepítse a megoldást az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.SCOMAssessmentOMS?tab=Overview)-ről.

- Miután hozzáadta a megoldást a munkaterülethez, az irányítópulton lévő **System Center Operations Manager Health Check** csempén megjelenik egy további szükséges konfigurációs üzenet. Kattintson a csempére, és kövesse az oldalon említett konfigurációs lépéseket.

  ![Irányítópult-csempe System Center Operations Manager](./media/scom-assessment/scom-configrequired-tile.png)

> [!NOTE]
> System Center Operations Manager konfigurációját parancsfájl használatával hajthatja végre, a Log Analytics megoldás konfigurációs lapján említett lépéseket követve.

 Az értékelés Operations Manager operatív konzolon keresztüli konfigurálásához hajtsa végre az alábbi lépéseket a következő sorrendben:
1. [A futtató fiók beállítása System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
2. A System Center Operations Manager Health Check szabály konfigurálása

## <a name="system-center-operations-manager-health-check-data-collection-details"></a>A System Center Operations Manager állapot-ellenőrzés által végzett adatgyűjtés részletei

A System Center Operations Manager Health Check-megoldás az alábbi forrásokból gyűjt adatokat:

* Regisztrációs adatbázis
* Windows Management Instrumentation (WMI)
* Eseménynapló
* Fájladatok
* Közvetlenül a Operations Manager PowerShell-és SQL-lekérdezések használatával a megadott felügyeleti kiszolgálóról.  

Az adatok gyűjtése a felügyeleti kiszolgálón történik, és hét naponta Log Analytics továbbítva.  

## <a name="operations-manager-run-as-accounts-for-log-analytics"></a>Futtató Operations Manager-fiókok a Log Analyticshez

A Log Analytics a számítási feladatokhoz a felügyeleti csomagokra épít a szolgáltatások értékének megadása érdekében. Minden munkaterheléshez a felügyeleti csomagok más biztonsági környezetben, például egy tartományi felhasználói fiókban való futtatásához munkaterhelés-specifikus jogosultságok szükségesek. Operations Manager futtató fiók konfigurálása emelt szintű hitelesítő adatokkal. További információ: [futtató fiók létrehozása](/previous-versions/system-center/system-center-2012-R2/hh321655(v=sc.12)) a Operations Manager dokumentációjában.

Az alábbi információk segítségével állíthatja be System Center Operations Manager Health Check Operations Manager futtató fiókját.

### <a name="set-the-run-as-account"></a>A futtató fiók beállítása

A folytatás előtt a futtató fióknak a következő követelményeknek kell megfelelnie:

* Olyan tartományi felhasználói fiók, amely a helyi Rendszergazdák csoport tagja a Operations Manager szerepkör-felügyeleti kiszolgálót támogató összes kiszolgálón, SQL Server az operatív, adattárház-és ACS-adatbázist, jelentéskészítést, webkonzolt és átjáró kiszolgálót.
* A felügyelt felügyeleti csoport Operation Manager-rendszergazdai szerepköre
* Ha a fiók nem rendelkezik SQL sysadmin jogosultságokkal, akkor hajtsa végre a [parancsfájlt](#sql-script-to-grant-granular-permissions-to-the-run-as-account) , hogy részletes engedélyeket adjon a fióknak minden olyan SQL Server-példányhoz, amely a Operations Manager-adatbázisok egyikét vagy mindegyikét üzemelteti.

1. A Operations Manager-konzolon válassza a **felügyelet** navigáció gombot.
2. A **futtató konfiguráció** alatt kattintson a **fiókok** elemre.
3. A **futtató fiók létrehozása** varázslóban a **Bevezetés** lapon kattintson a **tovább** gombra.
4. Az **Általános tulajdonságok** lapon válassza a **Windows** lehetőséget a **futtató fiók típusa:** listában.
5. Írjon be egy megjelenítendő nevet a **megjelenítendő név** szövegmezőbe, és szükség esetén adjon meg egy leírást a **Leírás** mezőben, majd kattintson a **tovább** gombra.
6. A **terjesztés biztonsága** **lapon válassza a biztonságosabb lehetőséget**.
7. Kattintson a **Létrehozás** lehetőségre.  

Most, hogy létrejött a futtató fiók, meg kell céloznia a felügyeleti kiszolgálókat a felügyeleti csoportban, és egy előre definiált futtató profilhoz kell rendelni, hogy a munkafolyamatok a hitelesítő adatok használatával fussanak.  

1. A **futtató konfiguráció**, **fiókok** pontban, az eredmények ablaktábláján kattintson duplán a korábban létrehozott fiókra.
2. A **terjesztés** lapon kattintson a **Hozzáadás** elemre a **kijelölt számítógépek** mezőben, és adja hozzá a felügyeleti kiszolgálót a fiók terjesztéséhez.  Kattintson kétszer **az OK gombra** a módosítások mentéséhez.
3. A **futtató konfiguráció** alatt kattintson a **profilok** elemre.
4. Keresse meg a *SCOM-felmérési profilt*.
5. A profil nevének a következőkkel kell rendelkeznie: *Microsoft System Center Operations Manager Health Check futtató profil*.
6. Kattintson a jobb gombbal, és frissítse a tulajdonságait, és adja hozzá a korábban létrehozott futtató fiókot.

### <a name="sql-script-to-grant-granular-permissions-to-the-run-as-account"></a>Az SQL-szkript, amely részletes engedélyeket biztosít a futtató fiók számára

Futtassa a következő SQL-szkriptet a szükséges engedélyek megadásához a futtató fiókhoz az operatív, adattárház-és ACS-adatbázist üzemeltető Operations Manager által használt SQL Server példányon.

```
-- Replace <UserName> with the actual user name being used as Run As Account.
USE master

-- Create login for the user, comment this line if login is already created.
CREATE LOGIN [UserName] FROM WINDOWS


--GRANT permissions to user.
GRANT VIEW SERVER STATE TO [UserName]
GRANT VIEW ANY DEFINITION TO [UserName]
GRANT VIEW ANY DATABASE TO [UserName]

-- Add database user for all the databases on SQL Server Instance, this is required for connecting to individual databases.
-- NOTE: This command must be run anytime new databases are added to SQL Server instances.
EXEC sp_msforeachdb N'USE [?]; CREATE USER [UserName] FOR LOGIN [UserName];'

Use msdb
GRANT SELECT To [UserName]
Go

--Give SELECT permission on all Operations Manager related Databases

--Replace the Operations Manager database name with the one in your environment
Use [OperationsManager];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager DatawareHouse database name with the one in your environment
Use [OperationsManagerDW];
GRANT SELECT To [UserName]
GO

--Replace the Operations Manager Audit Collection database name with the one in your environment
Use [OperationsManagerAC];
GRANT SELECT To [UserName]
GO

--Give db_owner on [OperationsManager] DB
--Replace the Operations Manager database name with the one in your environment
USE [OperationsManager]
GO
ALTER ROLE [db_owner] ADD MEMBER [UserName]

```

### <a name="configure-the-health-check-rule"></a>Az állapot-ellenőrzési szabály konfigurálása

A System Center Operations Manager Health Check-megoldás felügyeleti csomagja tartalmazza a *Microsoft System Center Operations Manager Futtatás állapot-ellenőrzési szabálya* nevű szabályt. Ez a szabály felelős az állapot-ellenőrzés futtatásához. A szabály engedélyezéséhez és a gyakoriság konfigurálásához használja az alábbi eljárásokat.

Alapértelmezés szerint a Microsoft System Center Operations Manager Futtatás állapot-ellenőrzési szabálya le van tiltva. Az állapot-ellenőrzési szolgáltatás futtatásához engedélyeznie kell a szabályt egy felügyeleti kiszolgálón. Ehhez a következő lépések szükségesek.

#### <a name="enable-the-rule-for-a-specific-management-server"></a>A szabály engedélyezése egy adott felügyeleti kiszolgáló számára

1. Az Operations Manager operatív konzol **szerzői** műveletek munkaterületén keresse meg a **szabály panelen a** *Microsoft System Center Operations Manager Futtatás állapot-ellenőrzési szabályát* .
2. A keresési eredmények közül válassza ki azt, amely a következő szöveget tartalmazza *: felügyeleti kiszolgáló*.
3. Kattintson a jobb gombbal a szabályra, majd kattintson **a felülbírálások elemre** a  >  **következő osztály egy adott objektumához: felügyeleti kiszolgáló**.
4.  Az elérhető felügyeleti kiszolgálók listában válassza ki azt a felügyeleti kiszolgálót, ahol a szabályt futtatni kell.  Ennek a felügyeleti kiszolgálónak kell lennie, amelyet korábban konfigurált a futtató fiókhoz való hozzárendeléséhez.
5.  Győződjön meg arról, hogy az **engedélyezett** paraméterérték értéke **true (igaz** ) értékre módosul.<br><br> ![felülbírálási paraméter](./media/scom-assessment/rule.png)

    Ebben az ablakban még a következő eljárással konfigurálhatja a Futtatás gyakoriságát.

#### <a name="configure-the-run-frequency"></a>A futás gyakoriságának konfigurálása

Az értékelés úgy van konfigurálva, hogy alapértelmezés szerint 10 080 percenként (vagy hét napig) fusson. Az értéket felülbírálhatja 1440 perc (vagy egy nap) minimális értékre. Az érték azt jelzi, hogy az egymást követő értékelés futtatása esetén milyen minimális időtartam szükséges. Az intervallum felülbírálásához kövesse az alábbi lépéseket.

1. A Operations Manager-konzol **szerzői műveletek** munkaterületén keresse meg a **szabály szakaszban a** *Microsoft System Center Operations Manager Futtatás állapot-ellenőrzési szabályát* .
2. A keresési eredmények közül válassza ki azt, amely a következő szöveget tartalmazza *: felügyeleti kiszolgáló*.
3. Kattintson a jobb gombbal a szabályra, majd kattintson a **szabály felülbírálása** a  >  **következő osztály összes objektumához: felügyeleti kiszolgáló**.
4. Módosítsa az **intervallum** paraméter értékét a kívánt intervallum értékre. Az alábbi példában az érték 1440 percre van állítva (egy nap).<br><br> ![intervallum paraméter](./media/scom-assessment/interval.png)<br>  

    Ha az érték 1440 percnél kisebb, akkor a szabály egy nap elteltével fut. Ebben a példában a szabály figyelmen kívül hagyja az intervallum értékét, és egy nap gyakorisággal fut.


## <a name="understanding-how-recommendations-are-prioritized"></a>A javaslatok rangsorolásának ismertetése

Minden javaslat egy súlyozási értéket kap, amely a javaslat relatív fontosságát azonosítja. Csak a 10 legfontosabb javaslat jelenik meg.

### <a name="how-weights-are-calculated"></a>A súlyozás kiszámítása

A súlyozások három fő tényezőn alapuló összesített értékek:

- Az  észlelt probléma valószínűleg problémákat okoz. A nagyobb valószínűség a javaslat nagyobb összesített pontszáma.
- A probléma *hatása* a szervezeten belül, ha problémát okoz. A nagyobb hatás a javaslat nagyobb összesített pontszáma.
- A javaslat megvalósításához szükséges *erőfeszítés* . A nagyobb erőfeszítés a javaslat kisebb összesített pontszámának felel meg.

Az egyes javaslatok súlyozása az egyes fókuszokhoz rendelkezésre álló teljes pontszám százalékában van kifejezve. Ha például egy, a rendelkezésre állási és az üzletmenet-folytonossági területen található javaslat 5%-os pontszámmal rendelkezik, a javaslat megvalósítása a teljes rendelkezésre állási és üzletmenet-folytonossági pontszámot 5%-kal növeli.

### <a name="focus-areas"></a>Fókuszterületek

**Rendelkezésre állás és Üzletmenet-folytonosság** – ez a fókusz a szolgáltatás rendelkezésre állására, az infrastruktúra rugalmasságára és az üzleti védelemre vonatkozó javaslatokat mutatja be.

**Teljesítmény és méretezhetőség** – ez a fókusz a szervezet informatikai infrastruktúrájának növekedését segítő ajánlásokat mutat be, így biztosíthatja, hogy az informatikai környezet megfeleljen a jelenlegi teljesítménybeli követelményeknek, és képes legyen reagálni az infrastrukturális igények változására.

**Frissítés, áttelepítés és üzembe helyezés** – ez a fókusz olyan javaslatokat mutat be, amelyek segítik a SQL Server frissítését, áttelepítését és üzembe helyezését a meglévő infrastruktúrán.

**Műveletek és monitorozás** – ez a fókusz az informatikai műveletek egyszerűsítését, a megelőző karbantartás megvalósítását és a teljesítmény maximalizálása érdekében ajánlásokat mutat be.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Érdemes 100%-ra törekedni minden fókuszterületen?

Nem feltétlenül. A javaslatok a Microsoft-mérnökök által több ezer ügyfél-látogatáson keresztül szerzett ismeretekre és tapasztalatokra épülnek. Azonban két kiszolgálói infrastruktúra sem azonos, és a konkrét javaslatok többé vagy kevésbé fontosak lehetnek. Előfordulhat például, hogy bizonyos biztonsági javaslatok kevésbé fontosak, ha a virtuális gépek nem lesznek elérhetők az internethez. Egyes rendelkezésre állási javaslatok kevésbé fontosak lehetnek az alacsony prioritású ad hoc adatgyűjtést és jelentéskészítést biztosító szolgáltatások esetében. Az érett üzleti tevékenység szempontjából fontos problémák kevésbé fontosak az indításhoz. Érdemes lehet azonosítani, hogy mely Kiemelt területek legyenek a prioritásai, és hogyan változnak meg a pontszámok időbeli változása.

Minden javaslat tartalmaz útmutatást arról, hogy miért fontos. Ezzel az útmutatóval kiértékelheti, hogy az ajánlás alkalmazása megfelelő-e Önnek, az informatikai szolgáltatások természetétől és a szervezet üzleti igényeinek megfelelően.

## <a name="use-health-check-focus-area-recommendations"></a>Az állapot-ellenőrzési fókusz területtel kapcsolatos javaslatok használata

Mielőtt állapot-ellenőrzési megoldást szeretne használni a Log Analyticsban, telepítenie kell a megoldást. A megoldások telepítésével kapcsolatos további információkért lásd: [felügyeleti megoldás telepítése](./solutions.md). A telepítése után megtekintheti a javaslatok összefoglalását a Azure Portal munkaterületének **Áttekintés** lapján található System Center Operations Manager Health Check csempe használatával.

Tekintse meg az infrastruktúra összesített megfelelőségi értékeléseit, majd a részletes ajánlásokat.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>A fókusz területre vonatkozó javaslatok megtekintése és a kijavítani kívánt művelet elvégzése
1. Jelentkezzen be a Azure Portalba a következő címen: [https://portal.azure.com](https://portal.azure.com) .
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
3. A Log Analytics-előfizetések ablaktáblán válasszon ki egy munkaterületet, majd kattintson a **munkaterület-összefoglalás** menüpontra.  
4. Az **Áttekintés** lapon kattintson a **System Center Operations Manager Health Check** csempére.
5. A **System Center Operations Manager Health Check** oldalon tekintse át az összefoglaló információkat a fókuszban található egyik panelen, majd kattintson az egyikre a fókuszhoz tartozó javaslatok megtekintéséhez.
6. A fókusz oldalain megtekintheti a környezetére vonatkozó rangsorolt javaslatokat. Az **érintett objektumok** alatt található javaslatra kattintva megtekintheti a javaslat hátterének részleteit.<br><br> ![fókusz terület](./media/scom-assessment/log-analytics-scom-healthcheck-dashboard-02.png)<br>
7. A **javasolt műveletekben** javasolt korrekciós műveleteket is végrehajthat. Az elem megcímzése után a későbbi értékelések rögzítik a javasolt műveleteket, és a megfelelőségi pontszám növekedni fog. A javított elemek **átadott objektumként** jelennek meg.

## <a name="ignore-recommendations"></a>Javaslatok figyelmen kívül hagyása

Ha olyan javaslatok vannak, amelyeket figyelmen kívül szeretne hagyni, létrehozhat egy szövegfájlt, amelyet Log Analytics használ, hogy megakadályozza a javaslatok megjelenését az értékelés eredményei között.

### <a name="to-identify-recommendations-that-you-want-to-ignore"></a>A figyelmen kívül hagyni kívánt javaslatok azonosítása
1. A kiválasztott munkaterülethez tartozó Log Analytics munkaterület lapon kattintson a **naplóbeli keresés** menüelemre a Azure Portal.
2. A következő lekérdezéssel listázhatja azokat a javaslatokat, amelyek a környezetében lévő számítógépeken sikertelenek voltak.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Failed | select Computer, RecommendationId, Recommendation | sort Computer
    ```

    >[!NOTE]
    > Ha a munkaterülete frissítve lett az [új log Analytics lekérdezési nyelvre](../logs/log-query-overview.md), akkor a fenti lekérdezés a következőre módosul.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

    Az alábbi képernyőképen a napló keresési lekérdezése látható:<br><br> ![naplóbeli keresés](./media/scom-assessment/scom-log-search.png)<br>

3. Válassza ki a figyelmen kívül hagyni kívánt ajánlásokat. A következő eljárásban a RecommendationId értékeit fogja használni.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt szövegfájl létrehozása és használata

1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.
2. Illessze be vagy írja be az egyes RecommendationId minden olyan javaslathoz, amelyet szeretne figyelmen kívül hagyni Log Analytics külön sorban, majd mentse és zárjuk be a fájlt.
3. Helyezze a fájlt a következő mappába az összes olyan számítógépen, amelyen Log Analytics szeretné figyelmen kívül hagyni a javaslatokat.
4. A Operations Manager felügyeleti kiszolgálón – *rendszermeghajtó*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server.

### <a name="to-verify-that-recommendations-are-ignored"></a>A javaslatok figyelmen kívül hagyása

1. A következő ütemezett értékelés futtatása után, alapértelmezés szerint hét naponta, a megadott javaslatok figyelmen kívül lesznek hagyva, és nem jelennek meg az állapot-ellenőrzési irányítópulton.
2. A következő naplóbeli keresési lekérdezésekkel listázhatja az összes figyelmen kívül hagyott javaslatot.

    ```
    Type=SCOMAssessmentRecommendationRecommendationResult=Ignored | select  Computer, RecommendationId, Recommendation | sort  Computer
    ```

    >[!NOTE]
    > Ha a munkaterülete frissítve lett az [új log Analytics lekérdezési nyelvre](../logs/log-query-overview.md), akkor a fenti lekérdezés a következőre módosul.
    >
    > `SCOMAssessmentRecommendationRecommendation | where RecommendationResult == "Ignore" | sort by Computer asc | project Computer, RecommendationId, Recommendation`

3. Ha később úgy dönt, hogy szeretné megtekinteni a figyelmen kívül hagyott javaslatokat, távolítson el minden IgnoreRecommendations.txt fájlt, vagy távolítsa el a RecommendationIDs.

## <a name="system-center-operations-manager-health-check-solution-faq"></a>System Center Operations Manager Health Check megoldás – gyakori kérdések

*Felvettem az állapot-ellenőrzési megoldást az Log Analytics munkaterületre. A javaslatok azonban nem jelennek meg. miért ne?* A megoldás hozzáadása után a következő lépésekkel tekintheti meg a Log Analytics irányítópulton található javaslatokat.  

- [A futtató fiók beállítása System Center Operations Manager Health Check](#operations-manager-run-as-accounts-for-log-analytics)  
- [A System Center Operations Manager Health Check szabály konfigurálása](#configure-the-health-check-rule)


*Van mód arra, hogy beállítsa, milyen gyakran fusson az ellenőrzések?* Igen. Lásd: [a Futtatás gyakoriságának konfigurálása](#configure-the-run-frequency).

*Ha a System Center Operations Manager Health Check-megoldás hozzáadása után egy másik kiszolgáló is fel van derítve, a rendszer ellenőrzi?* Igen, a felderítés után a rendszer alapértelmezés szerint minden hét nap múlva bejelöli a következőt:.

*Mi az adatgyűjtés folyamatának neve?* AdvisorAssessment.exe

*Hol fut a AdvisorAssessment.exe folyamat?* AdvisorAssessment.exe a felügyeleti kiszolgáló HealthService folyamata alatt fut, ahol az állapot-ellenőrzési szabály engedélyezve van. Ezzel a folyamattal a teljes környezet felderítése a távoli adatgyűjtésen keresztül érhető el.

*Mennyi ideig tart az adatgyűjtés?* A kiszolgálón az adatgyűjtés körülbelül egy órát vesz igénybe. A sok Operations Manager példánnyal vagy adatbázissal rendelkező környezetekben hosszabb időt is igénybe vehet.

*Mi a teendő, ha az értékelés intervallumát kevesebb mint 1440 percre állítja be?* Az értékelés előre konfigurálva van, hogy naponta legfeljebb egyszer fusson. Ha az intervallum értéke 1440 percnél kisebb értékre van felülbírálva, akkor az értékelés az 1440 percet használja az intervallum értékeként.

*Honnan tudható, hogy vannak-e előfeltételi hibák?* Ha az állapot-ellenőrzés futott, és nem jelenik meg az eredmények, akkor valószínű, hogy az ellenőrzés egyes előfeltételei nem teljesülnek. Lekérdezéseket futtathat: `Operation Solution=SCOMAssessment` és `SCOMAssessmentRecommendation FocusArea=Prerequisites` a naplóbeli keresésben megtekintheti a sikertelen előfeltételeket.

*Az `Failed to connect to the SQL Instance (….).` előfeltételként szükséges hibákkal kapcsolatos üzenet jelenik meg. Mi a probléma?* AdvisorAssessment.exe az adatokat gyűjtő folyamat az HealthService folyamat alatt fut a felügyeleti kiszolgálón. Az állapot-ellenőrzési eljárás részeként a folyamat megkísérli a kapcsolódást ahhoz a SQL Serverhoz, ahol a Operations Manager-adatbázis megtalálható. Ez a hiba akkor fordulhat elő, ha a tűzfalszabályok letiltják a SQL Server-példányhoz való kapcsolódást.

*Milyen típusú adatokat gyűjt?* A rendszer a következő adattípusokat gyűjti össze:-WMI-adatok-beállításjegyzék-adatok-Eseménynapló-adatOperations Manager adatok a Windows PowerShell, az SQL-lekérdezések és a fájl-információ gyűjtő használatával.

*Miért van szükség egy futtató fiók konfigurálására?* A Operations Manager különböző SQL-lekérdezéseket futtat. Ahhoz, hogy futtatni lehessen őket, a szükséges engedélyekkel rendelkező futtató fiókot kell használnia. Emellett helyi rendszergazdai hitelesítő adatokra van szükség a WMI lekérdezéséhez.

*Miért csak az első 10 ajánlás jelenjen meg?* A feladatok teljes körű listájának megadása helyett javasoljuk, hogy először a rangsorolt javaslatok kezelésére koncentráljon. A megoldásuk után további javaslatok válnak elérhetővé. Ha szeretné megtekinteni a részletes listát, az összes javaslatot megtekintheti a naplóbeli keresés használatával.

*Van lehetőség arra, hogy figyelmen kívül hagyja a javaslatot?* Igen, tekintse meg a [javaslatok mellőzése](#ignore-recommendations)című témakört.


## <a name="next-steps"></a>Következő lépések

- A [naplókban](../logs/log-query-overview.md) megtudhatja, hogyan elemezheti a részletes System Center Operations Manager Health Checki információkat és javaslatokat.

