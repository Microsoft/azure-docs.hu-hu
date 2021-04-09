---
title: Optimalizálja Active Directory-környezetét Azure Monitorkal | Microsoft Docs
description: A Active Directory Health-ellenőrzési megoldással rendszeres időközönként felméri a környezetek kockázatait és állapotát.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/10/2019
ms.openlocfilehash: bff61ec9dfcb985ea0111ca58bfd58273e1fe432
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101723248"
---
# <a name="optimize-your-active-directory-environment-with-the-active-directory-health-check-solution-in-azure-monitor"></a>Az Active Directory környezet-optimalizálása az Azure Monitor Active Directory Health Check megoldásával

![AD Health-ellenőrzési szimbólum](./media/ad-assessment/ad-assessment-symbol.png)

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

A Active Directory Health-ellenőrzési megoldással rendszeres időközönként felméri a kiszolgálói környezetek kockázatait és állapotát. Ebből a cikkből megtudhatja, hogyan telepítheti és használhatja a megoldást, hogy a lehetséges problémákra vonatkozó korrekciós műveleteket hajtson végre.

Ez a megoldás a telepített kiszolgálói infrastruktúrára vonatkozó javaslatok rangsorolt listáját tartalmazza. A javaslatok négy kiemelt területre vannak kategorizálva, amelyek segítségével gyorsan megismerheti a kockázatokat, és elvégezheti a szükséges lépéseket.

A javaslatok a Microsoft-mérnökök által több ezer ügyfél-látogatásból nyert ismeretekre és tapasztalatokra épülnek. Az egyes javaslatok útmutatást nyújtanak arról, hogy miért fontos a probléma, és hogyan valósítja meg a javasolt módosításokat.

Kiválaszthatja a szervezete számára legfontosabb fókusz területeket, és nyomon követheti az előrehaladást a kockázatmentes és kifogástalan állapotú környezetek futtatásának irányába.

A megoldás hozzáadása és az ellenőrzések befejezése után a fókusz területekre vonatkozó összefoglaló információk az **ad Health-ellenőrzési** irányítópulton jelennek meg a környezetében lévő infrastruktúrához. Az alábbi szakaszok azt ismertetik, hogyan használhatók az **ad Health-ellenőrzési** irányítópulton található információk, ahol megtekintheti és elvégezheti a Active Directory kiszolgálói infrastruktúrához javasolt műveleteket.  

![AD Health-ellenőrzési csempe képe](./media/ad-assessment/ad-healthcheck-summary-tile.png)

![az AD Health-ellenőrzési irányítópult képe](./media/ad-assessment/ad-healthcheck-dashboard-01.png)

## <a name="prerequisites"></a>Előfeltételek

* A Active Directory Health ellenőrzési megoldáshoz a .NET-keretrendszer 4.6.2 vagy újabb verziójának támogatott verzióját kell telepítenie minden olyan számítógépen, amelyen telepítve van a Windows Log Analytics ügynöke (más néven a Microsoft monitoring Agent (MMA)).  Az ügynököt a System Center 2016-Operations Manager, Operations Manager 2012 R2 és Azure Monitor használja.
* A megoldás támogatja a Windows Server 2008 és a 2008 R2, a Windows Server 2012 és az 2012 R2, a Windows Server 2016 és a Windows Server 2019 rendszert futtató tartományvezérlőket.
* Log Analytics munkaterület, amely az Azure Portal Azure Marketplace-en adja hozzá a Active Directory Health-ellenőrzési megoldást. Nincs szükség további konfigurációra.

  > [!NOTE]
  > A megoldás hozzáadása után a AdvisorAssessment.exe fájl hozzá lesz adva a-ügynökökkel rendelkező kiszolgálókhoz. A rendszer beolvassa és elküldi a konfigurációs adatfeldolgozást a felhőben lévő Azure Monitorba. A felhőszolgáltatás egy logikát alkalmaz a kapott adatokon, és rögzíti azokat.
  >
  >

A tartományhoz tartozó tartományvezérlők állapot-ellenőrzésének elvégzéséhez az adott tartomány összes tartományvezérlőjén az alábbi támogatott módszerek egyikével kell ügynököt és kapcsolatot létesíteni Azure Monitor:

1. Telepítse a [Windows log Analytics Agent ügynököt,](../agents/agent-windows.md) ha a tartományvezérlőt még nem figyeli a System Center 2016-Operations Manager vagy a Operations Manager 2012 R2.
2. Ha a System Center 2016 – Operations Manager vagy Operations Manager 2012 R2 rendszerrel van figyelve, és a felügyeleti csoport nincs integrálva a Azure Monitorba, a tartományvezérlő több Azure Monitor-Operations Manager is lehet az adatok gyűjtésére és a szolgáltatásba való továbbítására, valamint a általi figyelésre.  
3. Ellenkező esetben, ha a Operations Manager felügyeleti csoport integrálva van a szolgáltatással, hozzá kell adnia a tartományvezérlőket az adatgyűjtéshez a szolgáltatáshoz az [ügynök által felügyelt számítógépek hozzáadása](../agents/om-agents.md#connecting-operations-manager-to-azure-monitor) után, miután engedélyezte a megoldást a munkaterületen.  

A tartományvezérlőn az ügynök, amely egy Operations Manager felügyeleti csoportnak jelent jelentést, adatokat gyűjt, továbbít a hozzárendelt felügyeleti kiszolgálóhoz, majd közvetlenül egy felügyeleti kiszolgálóról küldi el a Azure Monitor.  Az adatOperations Manager-adatbázisok nem kerülnek be az adatforrásba.  

## <a name="active-directory-health-check-data-collection-details"></a>Az Active Directory Health Check által végzett adatgyűjtés részletei

Active Directory állapot-ellenőrzési adatokat gyűjt a következő forrásokból az engedélyezett ügynök használatával:

- Regisztrációs adatbázis
- LDAP
- .NET-keretrendszer
- Eseménynapló
- Active Directory szolgáltatási felületek (ADSI)
- Windows PowerShell
- Fájladatok
- Windows Management Instrumentation (WMI)
- DCDIAG eszköz API
- Fájlreplikációs szolgáltatás (NTFRS) API
- Egyéni C#-kód

A rendszer a tartományvezérlőn gyűjti az adatokat, és hét naponta Azure Monitor továbbítja azokat.  

## <a name="understanding-how-recommendations-are-prioritized"></a>A javaslatok rangsorolásának ismertetése

Minden javaslat egy súlyozási értéket kap, amely a javaslat relatív fontosságát azonosítja. Csak a 10 legfontosabb javaslat jelenik meg.

### <a name="how-weights-are-calculated"></a>A súlyozás kiszámítása

A súlyozások három fő tényezőn alapuló összesített értékek:

* A probléma azonosításának *valószínűsége* problémákat okoz. A nagyobb valószínűség a javaslat nagyobb összesített pontszáma.
* A probléma *hatása* a szervezeten belül, ha problémát okoz. A nagyobb hatás a javaslat nagyobb összesített pontszáma.
* A javaslat megvalósításához szükséges *erőfeszítés* . A nagyobb erőfeszítés a javaslat kisebb összesített pontszámának felel meg.

Az egyes javaslatok súlyozása az egyes fókuszokhoz rendelkezésre álló teljes pontszám százalékában van kifejezve. Ha például egy javaslat a biztonsági és megfelelőségi területen 5%-os pontszámmal rendelkezik, az ajánlás megvalósítása 5%-kal növeli az általános biztonsági és megfelelőségi pontszámot.

### <a name="focus-areas"></a>Fókuszterületek

**Biztonság és megfelelőség** – ez a kiemelt terület a lehetséges biztonsági fenyegetésekkel és szabálysértésekkel, a vállalati házirendekkel, valamint a technikai, jogi és szabályozási megfelelőségi követelményekkel kapcsolatos javaslatokat mutatja be.

**Rendelkezésre állás és Üzletmenet-folytonosság** – ez a fókusz a szolgáltatás rendelkezésre állására, az infrastruktúra rugalmasságára és az üzleti védelemre vonatkozó javaslatokat mutatja be.

**Teljesítmény és méretezhetőség** – ez a fókusz a szervezet informatikai infrastruktúrájának növekedését segítő ajánlásokat mutat be, így biztosíthatja, hogy az informatikai környezet megfeleljen a jelenlegi teljesítménybeli követelményeknek, és képes legyen reagálni az infrastrukturális igények változására.

**Frissítés, áttelepítés és üzembe helyezés** – ez a fókusz olyan javaslatokat mutat be, amelyek segítséget nyújtanak a meglévő infrastruktúrához való Active Directory frissítéséhez, áttelepítéséhez és üzembe helyezéséhez.

### <a name="should-you-aim-to-score-100-in-every-focus-area"></a>Érdemes 100%-ra törekedni minden fókuszterületen?

Nem feltétlenül. A javaslatok a Microsoft-mérnökök által több ezer ügyfél-látogatáson keresztül szerzett ismeretekre és tapasztalatokra épülnek. Azonban két kiszolgálói infrastruktúra sem azonos, és a konkrét javaslatok többé vagy kevésbé fontosak lehetnek. Előfordulhat például, hogy bizonyos biztonsági javaslatok kevésbé fontosak, ha a virtuális gépek nem lesznek elérhetők az internethez. Egyes rendelkezésre állási javaslatok kevésbé fontosak lehetnek az alacsony prioritású ad hoc adatgyűjtést és jelentéskészítést biztosító szolgáltatások esetében. Az érett üzleti tevékenység szempontjából fontos problémák kevésbé fontosak az indításhoz. Érdemes lehet azonosítani, hogy mely Kiemelt területek legyenek a prioritásai, és hogyan változnak meg a pontszámok időbeli változása.

Minden javaslat tartalmaz útmutatást arról, hogy miért fontos. Ennek az útmutatónak a segítségével kiértékelheti, hogy az ajánlás alkalmazása megfelelő-e Önnek, az informatikai szolgáltatások természetétől és a szervezet üzleti igényeinek megfelelően.

## <a name="use-health-check-focus-area-recommendations"></a>Az állapot-ellenőrzési fókusz területtel kapcsolatos javaslatok használata

A telepítését követően megtekintheti a javaslatok összefoglalását a Azure Portal megoldás lapján található állapot-ellenőrzés csempével.

Tekintse meg az infrastruktúra összesített megfelelőségi értékeléseit, majd a részletes ajánlásokat.

### <a name="to-view-recommendations-for-a-focus-area-and-take-corrective-action"></a>A fókusz területre vonatkozó javaslatok megtekintése és a kijavítani kívánt művelet elvégzése

[!INCLUDE [azure-monitor-solutions-overview-page](../../../includes/azure-monitor-solutions-overview-page.md)]

1. Az **Áttekintés** lapon kattintson a **Active Directory állapot-ellenőrzési** csempére.

2. Az **Állapot ellenőrzése** oldalon tekintse át az összefoglaló információkat az egyik fókuszban lévő panelen, majd kattintson az egyikre a fókuszhoz tartozó javaslatok megtekintéséhez.

3. A fókusz oldalain megtekintheti a környezetére vonatkozó rangsorolt javaslatokat. Az **érintett objektumok** alatt található javaslatra kattintva megtekintheti a javaslat hátterének részleteit.

    ![Állapot-ellenőrzési javaslatok képe](./media/ad-assessment/ad-healthcheck-dashboard-02.png)

4. A **javasolt műveletekben** javasolt korrekciós műveleteket is végrehajthat. Az elem megcímzése után a későbbi felmérések rögzítik a javasolt műveleteket, és a megfelelőségi pontszám növekedni fog. A javított elemek **átadott objektumként** jelennek meg.

## <a name="ignore-recommendations"></a>Javaslatok figyelmen kívül hagyása

Ha olyan javaslatok vannak, amelyeket figyelmen kívül szeretne hagyni, létrehozhat egy szövegfájlt, amelyet Azure Monitor fog használni, hogy megakadályozza a javaslatok megjelenését az értékelés eredményeiben.

### <a name="to-identify-recommendations-that-you-will-ignore"></a>A figyelmen kívül hagyható javaslatok azonosítása

[!INCLUDE [azure-monitor-log-queries](../../../includes/azure-monitor-log-queries.md)]

A következő lekérdezéssel listázhatja azokat a javaslatokat, amelyek a környezetében lévő számítógépeken sikertelenek voltak.

```
ADAssessmentRecommendation | where RecommendationResult == "Failed" | sort by Computer asc | project Computer, RecommendationId, Recommendation
```

Az alábbi képernyőképen a log lekérdezés látható: <

![sikertelen javaslatok](media/ad-assessment/ad-failed-recommendations.png)

Válassza ki a figyelmen kívül hagyni kívánt ajánlásokat. A következő eljárásban a RecommendationId értékeit fogja használni.

### <a name="to-create-and-use-an-ignorerecommendationstxt-text-file"></a>IgnoreRecommendations.txt szövegfájl létrehozása és használata

1. Hozzon létre egy IgnoreRecommendations.txt nevű fájlt.

2. Illessze be vagy írja be az egyes RecommendationId minden olyan javaslathoz, amelyet szeretne figyelmen kívül hagyni Azure Monitor külön sorban, majd mentse és zárjuk be a fájlt.

3. Helyezze a fájlt a következő mappába az összes olyan számítógépen, amelyen Azure Monitor szeretné figyelmen kívül hagyni a javaslatokat.

   * A Microsoft monitoring agenttel rendelkező számítógépeken (közvetlenül vagy Operations Manageron keresztül csatlakoztatott) – *SYSTEMDRIVE*: \Program Files\Microsoft monitoring Agent\Agent
   * A Operations Manager 2012 R2 felügyeleti kiszolgálón – *rendszermeghajtó*: \Program Files\Microsoft System Center 2012 R2\Operations Manager\Server
   * A Operations Manager 2016 felügyeleti kiszolgálón – *rendszermeghajtó*: \Program Files\Microsoft System Center 2016 \ Operations Manager\Server

### <a name="to-verify-that-recommendations-are-ignored"></a>A javaslatok figyelmen kívül hagyása

A következő ütemezett állapot-ellenőrzés után, alapértelmezés szerint hét naponta, a megadott javaslatok *figyelmen kívül lesznek hagyva* , és nem jelennek meg az irányítópulton.

1. A következő naplók használatával listázhatja az összes figyelmen kívül hagyott javaslatot.

    ```
    ADAssessmentRecommendation | where RecommendationResult == "Ignored" | sort by Computer asc | project Computer, RecommendationId, Recommendation
    ```

2. Ha később úgy dönt, hogy szeretné megtekinteni a figyelmen kívül hagyott javaslatokat, távolítson el minden IgnoreRecommendations.txt fájlt, vagy távolítsa el a RecommendationIDs.

## <a name="ad-health-check-solutions-faq"></a>AD Health Check megoldás – gyakori kérdések

*Milyen ellenőrzéseket végeznek a AD Assessment megoldás?*

* A következő lekérdezés az összes jelenleg elvégzett ellenőrzés leírását tartalmazza:

```Kusto
ADAssessmentRecommendation
| distinct RecommendationId, FocusArea, ActionArea, Recommendation, Description
| sort by FocusArea,ActionArea, Recommendation
```
Az eredmények az Excel programba exportálhatók további áttekintés céljából.

*Milyen gyakran fut az állapot-ellenőrzési funkció?*

* Az ellenőrzési hét naponta fut.

*Van mód arra, hogy beállítsa, milyen gyakran fusson az állapot-ellenőrzési folyamat?*

* Jelenleg nem.

*Ha egy állapot-ellenőrzési megoldás hozzáadása után egy másik kiszolgáló is fel van derítve, a rendszer ellenőrzi*

* Igen, a felderítés után a rendszer a hét minden napján ellenőrzi, hogy be van-e jelölve.

*Ha a kiszolgáló leszerelése megtörtént, akkor mikor lesz eltávolítva az állapot-ellenőrzési rendszerből?*

* Ha egy kiszolgáló 3 hétig nem küldi el az adatküldést, a rendszer eltávolítja.

*Mi az adatgyűjtés folyamatának neve?*

* AdvisorAssessment.exe

*Mennyi időt vesz igénybe az adatok gyűjtése?*

* A kiszolgálón a tényleges adatgyűjtés körülbelül 1 órát vesz igénybe. A nagy számú Active Directory kiszolgálóval rendelkező kiszolgálókon hosszabb ideig tarthat.

*Be lehet állítani az adatok gyűjtésének módját?*

* Jelenleg nem.

*Miért csak az első 10 ajánlás jelenjen meg?*

* A feladatok teljes körű listájának megadása helyett javasoljuk, hogy először a rangsorolt javaslatok kezelésére koncentráljon. A megoldásuk után további javaslatok válnak elérhetővé. Ha szeretné megtekinteni a részletes listát, az összes javaslatot megtekintheti egy napló lekérdezés használatával.

*Van lehetőség arra, hogy figyelmen kívül hagyja a javaslatot?*

* Igen, lásd a fenti [javaslatok mellőzése](#ignore-recommendations) szakaszt.

## <a name="next-steps"></a>Következő lépések

[Azure monitor log-lekérdezések](../logs/log-query-overview.md) használatával megismerheti, hogyan elemezheti a részletes ad Health-ellenőrzési információkat és javaslatokat.

