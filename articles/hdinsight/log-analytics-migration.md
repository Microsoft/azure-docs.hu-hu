---
title: Log Analytics-adatok áttelepítése Azure HDInsight
description: Ismerje meg az új táblák Azure Monitor és az ajánlott eljárásokat.
ms.service: hdinsight
ms.topic: how-to
ms.author: ali
author: AliciaLiMicrosoft
ms.date: 04/19/2021
ms.openlocfilehash: 6659b515ee2d25a4b9136ccfac4cc3444e491438
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741668"
---
# <a name="log-analytics-migration-guide-for-azure-hdinsight-clusters"></a>Log Analytics migrálási útmutató Azure HDInsight fürtökhöz

 Azure HDInsight egy nagyvállalati használatra kész, felügyelt fürtszolgáltatás. Ez a szolgáltatás olyan nyílt forráskódú elemzési keretrendszereket futtat az Azure-Apache Spark, mint a Apache Spark, a Hadoop, a HBase és a Kafka. Azure HDInsight azure-szolgáltatásokkal integrálva lehetővé teszi az ügyfelek számára, hogy jobban kezel big data elemzési alkalmazásokat.

A Log Analytics biztosít egy eszközt a Azure Portal a naplólekérdezések szerkesztéséhez és futtatásához. A lekérdezések a naplók által gyűjtött Azure Monitor és interaktív módon elemzik az eredményeik. Az ügyfelek Log Analytics-lekérdezésekkel lekérheti a megadott feltételeknek megfelelő rekordokat. Lekérdezésekkel emellett trendeket azonosíthat, mintákat elemezhet, és betekintést nyerhet az adataikba.

Azure HDInsight Log Analytics 2017-es integrációjának engedélyezése. A HDInsight-ügyfelek gyorsan elfogadták ezt a funkciót a HDInsight-fürtök figyelése és a fürtök naplóinak lekérdezése érdekében. Bár a funkció bevezetésének száma megnövekedett, az ügyfelek visszajelzést adnak az integrációról:

- Az ügyfelek nem dönthetik el, hogy melyik naplókat tárolják és tárolják, költségessé válhatnak.
- A jelenlegi HDInsight-sémanaplók nem követik a konzisztens elnevezési konvenciókat, és egyes táblák ismétlődőek.
- Az ügyfelek egy előre elérhető irányítópultot szeretne, amely egyszerűen monitorja a HDInsight-fürtök KPI-it.
- Az egyszerű lekérdezések futtatásához az ügyfeleknek a Log Analyticsre kell ugrani.

## <a name="solution-overview"></a>Megoldás áttekintése

Az ügyfelek visszajelzéseit figyelembe véve a Azure HDInsight az integrációba fektetett a Azure Monitor. Ez az integráció a következőt teszi lehetővé:

- Új táblakészlet az ügyfelek Log Analytics-munkaterületén. Az új táblák egy új Log Analytics-folyamaton keresztül vannak kézbesítve.
- Nagyobb megbízhatóság
- Gyorsabb naplók kézbesítése
- Erőforrás-alapú táblacsoportozás és alapértelmezett lekérdezések

## <a name="benefits-of-the-new-azure-monitor-integration"></a>Az új integráció Azure Monitor előnyei

Ez a dokumentum a Azure Monitor változásait ismerteti, és ajánlott eljárásokat tartalmaz az új táblák használatával kapcsolatban.

**Újratervezett sémák:** Az új Azure Monitor integráció sémaformázása jobban szervezett és könnyen érthető. Az örökölt sémákban a lehető legnagyobb kétértelműség érdekében a kettő harmada kevesebb sémát tartalmaz.

**Szelektív naplózás (hamarosan elérhetővé válik):** A Log Analyticsben elérhetők naplók és metrikák. A költségek monitorozásának csökkentése érdekében kiadunk egy új szelektív naplózási funkciót. Ezzel a funkcióval be- és kikapcsolhatja a különböző naplókat és metrikaforrásokat. Ezzel a funkcióval csak azért kell fizetnie, amit használ.

**Naplók fürtportál-integrációja:** **A** Naplók panel új a HDInsight-fürtportálon. Bárki, aki hozzáfér a fürthöz, ezen a panelen lekérdezheti bármelyik táblát, amelybe a fürterőforrás rekordokat küld. A felhasználóknak már nincs szükségük a Log Analytics-munkaterülethez való hozzáférésre egy adott fürterőforrás rekordjainak eléréséhez.

**Insights-fürtportál integrációja:** Az **Elemzések** panel a HDInsight-fürt portálján is új. Az új Azure Monitor integrációjának engedélyezése után kiválaszthatja az Elemzések panelt, és a fürt típusára vonatkozó, előre megadott naplók és metrikák irányítópultja automatikusan ki lesz töltve.  Ezeket az irányítópultokat a korábbi Azure-megoldásokhoz képest átalakítottuk. Ezek segítségével mély betekintést nyerhet a fürt teljesítményére és állapotába.

**Nagy léptékű elemzések:** Az új **At-Scale Insights-munkafüzetet** **a Azure Monitor** portálon használhatja a fürtök állapotának és teljesítményének különböző előfizetésekben való figyeléséhez.

## <a name="customer-scenarios"></a>Ügyfélforgatókönyvek

A következő szakaszok azt ismertetik, hogyan használhatják az ügyfelek az új Azure Monitor különböző forgatókönyvekben való integrációját. Az [Új integráció aktiválása Azure Monitor](#activate-a-new-azure-monitor-integration) szakasz ismerteti, hogyan aktiválhatja és használhatja az új Azure Monitor integrációt. A Klasszikus Azure Monitor áttelepítése az új [Azure Monitor-integráció](#migrate-to-the-new-azure-monitor-integration) szakasz további információkat tartalmaz a régi integrációtól Azure Monitor felhasználók számára.

> [!NOTE]
> Csak a 2020. szeptember végén és azt követően létrehozott fürtök jogosultak az új Azure Monitoring-integrációra.

## <a name="activate-a-new-azure-monitor-integration"></a>Új integrációs Azure Monitor aktiválása 

> [!NOTE]
> Az új integráció engedélyezése előtt létre kell hoznunk egy Log Analytics-munkaterületet egy olyan előfizetésben, amelyhez hozzáféréssel rendelkezik. A Log Analytics-munkaterületek létrehozásáról a Log [Analytics-munkaterület](../azure-monitor/learn/quick-create-workspace.md)létrehozása a Azure Portal.

Aktiválja az új integrációt a fürt portáloldalán, és görgessen lefelé a bal oldali menüben, amíg el nem éri a **Figyelés** szakaszt. A **Figyelés szakaszban** válassza az **Integráció monitorozása lehetőséget.** Ezután válassza az **Engedélyezés** lehetőséget, és kiválaszthatja azt a Log Analytics-munkaterületet, amelybe a naplókat el szeretné küldeni. A **munkaterület kiválasztása** után válassza a Mentés lehetőséget. 

### <a name="access-the-new-tables"></a>Az új táblák elérése

Az új táblákat kétféleképpen lehet elérni. 

### <a name="approach-1"></a>1. módszer:
Az új táblák elérésének első módja a Log Analytics-munkaterületen keresztüli hozzáférés. 

1. Lépjen az integráció engedélyezésekor kiválasztott Log Analytics-munkaterületre. 
2. Görgessen le a képernyő bal oldalán található menüben, és válassza a Naplók **lehetőséget.** Megjelenik egy Naplók lekérdezésszerkesztő a munkaterület összes táblájának listájával. 
3. Ha a táblák megoldás szerint **vannak** csoportosítva, az új HDI-táblák a Naplók kezelése **szakaszban** találhatóak. 
4. Ha a táblákat erőforrástípus szerint csoportosítja, a táblák a **HDInsight-fürtök** szakaszban jelennek meg, ahogy az alábbi képen látható. 

> [!NOTE]
> Ez a folyamat azt írja le, hogyan érhetők el a naplók a régi integrációban. Ehhez a felhasználónak hozzá kell férni a munkaterülethez.

### <a name="approach-2"></a>2. megközelítés:

Az új táblák elérésének második módja a Fürtportál elérése.
 
1. Lépjen a fürt portáloldalára, és görgessen lefelé a bal oldali menüben, amíg meg nem látja a **Figyelés** szakaszt. Ebben a szakaszban a Naplók **panelt láthatja.** 
2. Válassza **a Naplók** lehetőséget, és megjelenik egy Naplók lekérdezésszerkesztő. A szerkesztő a fürterőforráshoz társított összes naplót tartalmazza. Amikor engedélyezte az integrációt, a naplókat a Log Analytics-munkaterületre küldte. Ezek a naplók erőforrás-alapú hozzáférést (RBAC) biztosítanak. Az RBAC használatával azok a felhasználók, akik hozzáférnek a fürthöz, de nem férnek hozzá a munkaterülethez, láthatják a fürthöz társított naplókat.

Összehasonlításképpen az alábbi képernyőképeken az örökölt integrációs munkaterület nézet és az új integrációs munkaterület nézet látható:

**Örökölt integrációs munkaterület nézet**

  :::image type="content" source="./media/log-analytics-migration/legacy-integration-workspace-view.png"  lightbox="./media/log-analytics-migration/legacy-integration-workspace-view.png" alt-text="Képernyőkép az örökölt integrációs munkaterület nézetről." border="false":::

**Új integrációs munkaterület nézet**

  :::image type="content" source="./media/log-analytics-migration/new-integration-workspace-view.png" lightbox="./media/log-analytics-migration/new-integration-workspace-view.png" alt-text="Képernyőkép az új integrációs munkaterület nézetről." border="false":::

### <a name="use-the-new-tables"></a>Az új táblák használata

Ezek az integrációk segíthetnek az új táblák használatában:

#### <a name="default-queries-to-use-with-new-tables"></a>Alapértelmezett lekérdezések az új táblákhoz

A Naplók lekérdezésszerkesztőben állítsa a kapcsolót **Lekérdezések beállításra a** táblák listája felett. Győződjön meg arról, hogy  a lekérdezéseket erőforrástípus szerint csoportosítja, és hogy nincs szűrőkészlet a **HDInsight-fürtökön kívül más erőforrástípushoz.** Az alábbi kép bemutatja, hogyan  néznek ki az eredmények, ha erőforrástípus szerint vannak csoportosítva, és a **HDInsight-fürtökre vannak szűrve.** Csak válasszon ki egyet, és az megjelenik a Naplók lekérdezésszerkesztőben. Mindenképpen olvassa el a lekérdezésekben szereplő megjegyzéseket, mert némelyiknél meg kell adnia néhány információt, például a fürt nevét a lekérdezés sikeres futtatásához.

:::image type="content" source="./media/log-analytics-migration/default-query-results-grouped-resource-type.png" alt-text="Képernyőkép az alapértelmezett lekérdezési eredményekről csoportosított erőforrástípussal." border="true":::


#### <a name="create-your-own-queries"></a>Saját lekérdezések létrehozása

A Naplók lekérdezésszerkesztőben megadhatja a saját lekérdezéseit. A régi táblákon használt lekérdezések nem lesznek érvényesek az új táblákra, mivel számos új tábla új, finomított sémával is fel van használva. Az alapértelmezett lekérdezések remek hivatkozások az új táblák lekérdezésének alakítására.

#### <a name="insights"></a>Insights

Az elemzések az Azure-munkafüzetek használatával készült fürtspecifikus vizualizációs [irányítópultok.](../azure-monitor/platform/workbooks-overview.md) Ezek az irányítópultok részletes grafikonokat és vizualizációkat adnak a fürt futtatásáról. Az irányítópultok minden fürttípushoz, YARN-hoz, rendszermetrikákhoz és összetevőnaplókhoz tartalmaznak szakaszokat. A fürt irányítópultjának eléréséhez látogasson el a fürt oldalára a portálon, görgessen le a Figyelés szakaszhoz, és válassza az **Elemzések panelt.**  Az irányítópult automatikusan betöltődik, ha engedélyezte az új integrációt. A naplók lekérdezésekor hagyjon néhány másodpercet a gráfok betöltésére.

:::image type="content" source="./media/log-analytics-migration/visualization-dashboard.png" lightbox="./media/log-analytics-migration/visualization-dashboard.png" alt-text="A vizualizáció irányítópultját ábrázoló képernyőkép.":::

#### <a name="custom-azure-workbooks"></a>Egyéni Azure-munkafüzetek

Egyéni gráfokkal és vizualizációk használatával saját Azure-munkafüzeteket is létrehozhat. A fürt portáloldalán görgessen le a **Figyelés** szakaszhoz, és válassza a **Munkafüzetek** panelt a bal oldali menüben. Elkezdhet üres sablont használni, vagy használhatja a **HDInsight-fürtök szakaszban található sablonok** valamelyikét. Minden fürttípushoz van sablon. A sablonok akkor hasznosak, ha olyan konkrét testreszabásokat szeretne menteni, amelyekről az alapértelmezett HDInsight-elemzések nem nyújtanak információt. Ha úgy érzi, hogy hiányzik valami, küldjön kéréseket a HDInsight Insights új szolgáltatásaira.

#### <a name="at-scale-workbooks-for-new-azure-monitor-integrations"></a>Nagy léptékű munkafüzetek az új Azure Monitor integrációjához

Az új, nagy méretekben használható munkafüzet segítségével több fürtből álló figyelési élményt kap a fürtökhöz. A nagy léptékű munkafüzet bemutatja, hogy mely fürtökön van engedélyezve a monitorozási folyamat. A munkafüzet emellett egy egyszerű lehetőséget is biztosít több fürt állapotának egyszerre történő ellenőrzésére. A munkafüzet megtekintése:

1. A **kezdőlapról Azure Monitor** lapra Azure Portal lap
2. A **kezdőlapon Azure Monitor** insights **hub lehetőséget** az Elemzések **szakaszban.**
3. Az Elemzés **szakaszban válassza a HDInsight-fürtök** lehetőséget. 

   :::image type="content" source="./media/log-analytics-migration/at-scale-workbook.png" lightbox="./media/log-analytics-migration/at-scale-workbook.png" alt-text="Képernyőkép a nagy léptékű munkafüzetről." border="false":::

#### <a name="alerts"></a>Riasztások

A Naplólekérdezés szerkesztőben egyéni riasztásokat adhat hozzá a fürtökhöz és munkaterületekhez. A naplók lekérdezésszerkesztőjéhez  válassza a fürt vagy a munkaterület portáljának Naplók paneljét. Futtason egy lekérdezést, majd válassza az **Új riasztási** szabály lehetőséget az alábbi képernyőképen látható módon. További információkért olvassa el a [riasztások konfigurálását.](../azure-monitor/platform/alerts-log.md)

:::image type="content" source="./media/log-analytics-migration/new-rule-alert.png" alt-text="Az új szabályriasztás képernyőképe." border="false":::

## <a name="migrate-to-the-new-azure-monitor-integration"></a>Áttelepítés az új integrációs Azure Monitor be

Ha a klasszikus Azure Monitor integrációt használja, néhány módosítást kell végeznie az új táblaformátumokban, miután átváltott az új Azure Monitor integrációra.

Az új Azure Monitor engedélyezéséhez kövesse az Új integrációs szolgáltatás [aktiválása Azure Monitor lépéseit.](#activate-a-new-azure-monitor-integration)

### <a name="run-queries-in-log-analytics"></a>Lekérdezések futtatása a Log Analyticsben

Mivel az új táblaformátum eltér az előzőtől, a lekérdezéseket át kell dolgozni, hogy használjuk az új táblákat. Miután engedélyezi az új Azure Monitor integrációt, a táblákban és sémákban megkeresheti a régi lekérdezésekben használt mezőket.

A régi tábla [és](#appendix-table-mapping) az új tábla közötti leképezési táblázat segítségével gyorsan megtalálhatja az irányítópultok és lekérdezések áttelepítéséhez szükséges új mezőket.

**Alapértelmezett lekérdezések:** Alapértelmezett lekérdezéseket hoztunk létre, amelyek megmutatják, hogyan használhatók az új táblák a gyakori helyzetekben. Az alapértelmezett lekérdezések azt is megmutatják, hogy milyen információk érhetők el az egyes táblákban. Az alapértelmezett lekérdezéseket a cikk Új [](#default-queries-to-use-with-new-tables) táblákkal használható alapértelmezett lekérdezések szakaszában található utasításokat követve használhatja.

### <a name="update-dashboards-for-hdinsight-clusters"></a>Irányítópultok frissítése HDInsight-fürtökhöz

Ha több irányítópultot is felépített a HDInsight-fürtök figyelése érdekében, az új fürtök integrációjának engedélyezése után módosítania kell a tábla mögötti Azure Monitor. A tábla vagy a mező neve megváltozhat az új integrációban, de a régi integrációban található összes információ megtalálható benne.

Az [irányítópultok](#appendix-table-mapping) mögötti lekérdezés frissítéséhez tekintse meg a régi tábla/séma közötti leképezési táblát az új táblára/sémára.

#### <a name="out-of-box-dashboards"></a>Az irányítópultok már ki vannak vaiva 

A már nem szükséges irányítópultokat fürtszinten is továbbfejlesztettük. Minden diagram jobb felső részen található egy gomb, amely lehetővé teszi az információt előállító mögöttes lekérdezést. A gráf remek módja annak, hogy megismerje az új táblák hatékony lekérdezésének módját. A használatra elérhető irányítópultokat az Insights and At-scale workbooks [](#insights) for new Azure Monitor integrations (Elemzések és nagy léptékű munkafüzetek az új integrációkhoz) [Azure Monitor útmutatásai alapján használhatja.](#at-scale-workbooks-for-new-azure-monitor-integrations)

### <a name="use-an-hdinsight-at-scale-monitoring-dashboard"></a>HDInsight nagy léptékű monitorozási irányítópult használata

Ha a használatra elérhető monitorozási irányítópultot használja olyan HDInsight-fürtökhöz, mint a HDInsight Spark Monitoring és a HDInsight interaktív monitorozás, azon dolgozunk, hogy ugyanezeket a képességeket nyújtsuk a Azure Monitor portálon.

Látni fogja, hogy a HDInsight-fürtök beállítás a Azure Monitor.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-azure-monitor.png" lightbox="./media/log-analytics-migration/hdinsight-azure-monitor.png" alt-text="Képernyőkép a HDInsight beállításról a Azure Monitor." border="false":::

A Azure Monitor portál Insights Hubja lehetővé teszi több HDInsight-fürt egyetlen helyen történő monitorozását. A fürtök a számítási feladat típusa alapján vannak rendszerezve, így olyan típusokat láthat, mint a Spark, a HBase és a Hive. A több irányítópult helyett ebben a nézetben figyelheti az összes HDInsight-fürtöt.

> [!NOTE]
> További információt a jelen cikk Insights [and](#insights) [At-scale workbooks for new Azure Monitor integrations](#at-scale-workbooks-for-new-azure-monitor-integrations) (Elemzések és nagy léptékű munkafüzetek az új Azure Monitor-integrációkhoz) szakaszban talál.

## <a name="enable-both-integrations-to-accelerate-the-migration"></a>Mindkét integráció engedélyezése a migrálás felgyorsítása érdekében

Egyszerre aktiválhatja a klasszikus és az új Azure Monitor-integrációkat is egy olyan fürtön, amely jogosult mindkét integrációra az új integrációs Azure Monitor gyors áttelepítéséhez. Az új integráció a 2020. szeptember közepét követően létrehozott összes fürthöz elérhető.

Így egyszerűen összehasonlíthatja a használt lekérdezéseket.

### <a name="enabling-the-classic-integration"></a>A klasszikus integráció engedélyezése

Ha 2020 szeptemberének közepét követően létrehozott fürtöt használ, az új portálélményt a fürt portálján fogja látni. Az új folyamat engedélyezéséhez kövesse az Új folyamatintegráció [aktiválása Azure Monitor lépéseit.](#activate-a-new-azure-monitor-integration) A klasszikus integráció ezen a fürtön való aktiválásához kattintson a fürt portáloldalára. A **fürtportál** bal  oldalán található menü Figyelés szakaszában válassza az Integráció figyelése panelt. Válassza **a Azure Monitor HDInsight-fürtök integrációjához (klasszikus) lehetőséget.** Megjelenik egy oldalkörnyezet a klasszikus Azure Monitoring-integráció engedélyezéséhez és letiltásához használható váltógombkal. 

> [!NOTE]
> A klasszikus integrációból származó naplók és metrikák nem fognak látni a fürtportál Naplók és Elemzések oldalán. Ezen a helyeken csak az új integrációs naplók és metrikák lesznek jelen.

   :::image type="content" source="./media/log-analytics-migration/hdinsight-classic-integration.png" alt-text="Képernyőkép a klasszikus integráció elérésére mutató hivatkozásról." border="false":::

2021. július 1. után nem Azure Monitor új fürtök létrehozása klasszikus integrációval.

## <a name="release-and-support-timeline"></a>Kiadási és támogatási ütemterv

- A klasszikus Azure Monitoring-integráció 2021. október 15. után nem lesz elérhető. Ezt követően nem engedélyezheti a klasszikus Azure Monitoring-integrációt.
- A meglévő klasszikus Azure monitorozási integrációk továbbra is működni fognak. A klasszikus Azure Monitoring-integráció korlátozott támogatást nyújt. 
  - Miután az ügyfelek beküldik a támogatási jegyet, megvizsgálják a problémákat.
  - Ha a megoldáshoz rendszerkép-módosításra van szükség, az ügyfeleknek át kell lépnie az új integrációra.
  - Nem javítjuk ki a klasszikus Azure Monitoring integrációs fürtöt, kivéve a kritikus biztonsági problémákat.

## <a name="appendix-table-mapping"></a>Függelék: Táblaleképezés

Az alábbi diagramok a klasszikus Azure Monitoring-integrációból az újba való táblaleképezéseket mutatják be. A **Workload (Számítási** feladat) oszlop azt írja le, hogy az egyes tábla milyen számítási feladathoz van társítva. Az **Új tábla** sor az új tábla nevét jeleníti meg. A **Leírás** sor az ebben a táblában elérhető naplók/metrikák típusát ismerteti. A **Régi tábla** sor a klasszikus tábla integrációjának összes olyan tábláját tartalmazza Azure Monitor amelynek adatai most már szerepelnek az Új tábla sorban felsorolt **táblában.**

> [!NOTE]
> Egyes táblák újak, és nem a régi táblákon alapulnak.

## <a name="general-workload-tables"></a>Általános számítási feladatok táblái

| Új tábla | Részletek |
| --- | --- |
| HDInsightAmbariSystemMetrics | <ul><li>**Leírás:** Ez a tábla az Ambaritól gyűjtött rendszermetrikákat tartalmazza. A metrikák mostantól a fürt minden csomópontjéről (az élcsomópontok kivételével) jönnek létre, nem csak a két fejcsomópontból. Mostantól minden metrika egy oszlop, és minden metrikát rekordonként egyszer jelent a rendszer.</li><li>Régi **tábla:** metrics \_ cpu nice \_ \_ cl, metrics \_ cpu system \_ \_ cl, \_ metrics cpu user \_ \_ cl, metrics memory cache CL, metrics memory \_ swap \_ \_ \_ \_ \_ CL, \_ metrics memory \_ total \_ CLmetrics memory buffer CL, metrics \_ load \_ \_ \_ 1min CL, metrics load \_ cpu CL, metrics load \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ nodes CL, metrics load procs CL, metrics network in CL, metrics network out CL</li></ul>|
| HDInsightAmbariClusterAlerts | <ul><li>**Leírás:** Ez a táblázat a fürt minden csomópontjának Ambari-fürtriasztásokat tartalmaz (kivéve az élcsomópontokat). Minden riasztás egy rekord ebben a táblában.</li><li>**Régi tábla:** metrikák \_ \_ fürtriasztásai \_ CL</li></ul>|
| HDInsightSecurityLogs | <ul><li>**Leírás:** Ez a tábla az Ambari audit- és hitelesítési naplók rekordjait tartalmazza.</li><li>**Régi tábla:** log \_ ambari \_ audit \_ CL, log \_ auth \_ CL</li></ul>|
| HDInsightRangerAuditLogs | <ul><li>**Leírás:** Ez a tábla az ESP-fürtök Ranger-auditnaplójában található összes rekordot tartalmazza.</li><li>**Régi tábla:** Ranger \_ \_ auditnaplók \_ CL</li></ul>|
| HDInsightGatewayAuditLogs \_ CL | <ul><li>**Leírás:** Ez a tábla tartalmazza az átjárócsomópontok naplózási adatait. Ez ugyanaz a formátum, mint a Régi táblák oszlopban lévő tábla. **Ez továbbra is az Egyéni naplók szakaszban található.**</li><li>**Régi tábla: naplóátjáró** \_ \_ naplózása \_ CL</li></ul>|

## <a name="spark-workload"></a>Spark számítási feladat

> [!NOTE]
> A Spark-alkalmazásokkal kapcsolatos táblákat 11 új Spark-táblára cserélték (a HDInsightSparktól kezdve*), amelyek részletes információkat adnak a Spark számítási feladatairól.


| Új tábla | Részletek |
| --- | --- |
| HDInsightSparkLogs | <ul><li>**Leírás:** Ez a táblázat a Sparkkal és a kapcsolódó összetevővel kapcsolatos összes naplót tartalmazza: Livy és Jupyter.</li><li>**Régi tábla:** log \_ livy, \_ CL, log \_ jupyter \_ CL, log \_ spark \_ CL, log \_ sparkappsexecutors \_ CL, log \_ sparkappsdrivers \_ CL</li></ul>|
| HDInsightSparkApplicationEvents | <ul><li>**Leírás:** Ez a táblázat a Spark-alkalmazások eseményinformációját tartalmazza, beleértve a beküldési és befejezési időt, az alkalmazásazonosítót és az AppName adatokat. Ez akkor hasznos, ha nyomon követi, mikor indult és fejeződött be az alkalmazások. </li></ul>|
| HDInsightSparkBlockManagerEvents | <ul><li>**Leírás:** Ez a táblázat a Spark Block Managerével kapcsolatos eseményadatokat tartalmazza. Olyan információkat tartalmaz, mint a végrehajtó memóriahasználata.</li></ul>|
| HDInsightSparkEnvironmentEvents | <ul><li>**Leírás:** Ez a tábla az alkalmazás által futtatott környezettel, a Spark üzembe helyezési módjával, a főkiszolgálóval és a végrehajtóval kapcsolatos információkat tartalmazza.</li></ul>|
| HDInsightSparkExecutorEvents | <ul><li>**Leírás:** Ez a táblázat a Spark-végrehajtó alkalmazás által való használatáról tartalmaz eseményadatokat.</li></ul>|
| HDInsightSparkExtraEvents | <ul><li>**Leírás:** Ez a tábla olyan eseményinformációkat tartalmaz, amelyek nem illeszkednek más Spark-táblába. </li></ul>|
| HDInsightSparkJobEvents | <ul><li>**Leírás:** Ez a tábla információkat tartalmaz a Spark-feladatokról, beleértve a kezdési és a záró időpontukat, az eredményt és a kapcsolódó fázisokat.</li></ul>|
| HDInsightSparkSqlExecutionEvents | <ul><li>**Leírás:** Ez a tábla a Spark SQL-lekérdezések eseményinformációját tartalmazza, beleértve a tervinformációkat és a leírásukat, valamint a kezdési és a záró időpontokat.</li></ul>|
| HDInsightSparkStageEvents | <ul><li>**Leírás:** Ez a tábla a Spark-fázisok eseményinformációját tartalmazza, beleértve a kezdési és befejezési időt, a hiba állapotát és a részletes végrehajtási információkat.</li></ul>|
| HDInsightSparkStageTaskAccumulables | <ul><li>**Leírás:** Ez a táblázat fázisok és feladatok teljesítménymetrikákat tartalmaz.</li></ul>|
| HDInsightTaskEvents | <ul><li>**Leírás:** Ez a tábla a Spark-feladatok eseményinformációját tartalmazza, beleértve a kezdési és befejezési időt, a társított fázisokat, a végrehajtási állapotot és a feladattípust.</li></ul>|
| HDInsightJupyterNotebookEvents | <ul><li>**Leírás:** Ez a tábla Jupyter-notebookok eseményinformációit tartalmazza.</li></ul>|

## <a name="hadoopyarn-workload"></a>Hadoop/YARN számítási feladat

| Új tábla | Részletek |
| --- | --- |
| HDInsightHadoopAndYarnMetrics | <ul><li>**Leírás:** Ez a tábla a Hadoop- és YARN-keretrendszerek JMX-metrikákat tartalmaz. Ugyanazokkal a JMX-metrikákat tartalmazza, mint a régi Custom Logs-táblák, valamint további metrikákat is fontosnak tekintettünk. Hozzáadtunk idővonal-kiszolgáló, Node Manager és Feladatelőzmény-kiszolgáló metrikákat. Rekordonként egy metrikát tartalmaz.</li><li>Régi **tábla:** metrics \_ resourcemanager \_ clustermetrics \_ CL, metrics \_ resourcemanager \_ jvm \_ CL, \_ metrics resourcemanager \_ queue root \_ \_ CL, metrics \_ resourcemanager \_ queue root \_ \_ joblauncher \_ CL, metrics \_ resourcemanager \_ queue root \_ \_ \_ cl, metrics \_ resourcemanager \_ queue root \_ \_ thriftsvr \_ CL</li></ul>|
| HDInsightHadoopAndYarnLogs | <ul><li>**Leírás:** Ez a tábla a Hadoop- és YARN-keretrendszerek által létrehozott összes naplót tartalmazza.</li><li>**Régi tábla:** log \_ mrjobsummary \_ CL, log \_ resourcemanager \_ CL, log \_ timelineserver \_ CL, log \_ nodemanager \_ CL</li></ul>|

 
## <a name="hivellap-workload"></a>Hive/LLAP számítási feladat 

| Új tábla | Részletek |
| --- | --- |
| HDInsightHiveAndLLAPMetrics | <ul><li>**Leírás:** Ez a táblázat a Hive- és LLAP-keretrendszerek JMX-metrikákat tartalmaz. Ugyanazt a JMX-metrikát tartalmazza, mint a régi Custom Logs-táblák. Rekordonként egy metrikát tartalmaz.</li><li>Régi **tábla:** llap \_ metrics \_ hiveserver2 \_ CL, \_ \_ llap metrics hs2 \_ metrics \_ subsystemllap \_ \_ metrics jvm \_ CL, llap \_ metrics \_ llap \_ daemon \_ info \_ CL, llap \_ metrics \_ \_ allocator \_ info \_ CL, llap \_ metrics \_ deamon jvm CL, llap metrics \_ io CL, llap metrics executor metrics \_ CL, llap \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ \_ metrics metricssystem stats CL, llap metrics cache CL</li></ul>|
| HDInsightHiveAndLLAPLogs | <ul><li>**Leírás:** Ez a táblázat a Hive, LLAP és kapcsolódó összetevőik által létrehozott naplókat tartalmazza: WebHCat és Zeppelin.</li><li>**Régi tábla:** log \_ hivemetastore \_ CL log \_ hiveserver2 \_ CL, log \_ hiveserve2interactive \_ CL, log \_ webhcat \_ CL, log \_ zeppelin \_ zeppelin \_ CL</li></ul>|


## <a name="kafka-workload"></a>Kafka-számítási feladat

| Új tábla | Részletek |
| --- | --- |
| HDInsightKafkaMetrics | <ul><li>**Leírás:** Ez a tábla a Kafkában található JMX-metrikákat tartalmazza. Ugyanazokkal a JMX-metrikákat tartalmazza, mint a régi Custom Logs-táblák, és további metrikákat is fontosnak tekintettünk. Rekordonként egy metrikát tartalmaz.</li><li>**Régi tábla:** metrikák \_ kafka \_ CL</li></ul>|
| HDInsightKafkaLogs | <ul><li>**Leírás:** Ez a tábla a Kafka-közvetítők által létrehozott összes naplót tartalmazza.</li><li>**Régi tábla:** log \_ kafkaserver \_ CL, log \_ kafkacontroller \_ CL</li></ul>|

## <a name="hbase-workload"></a>HBase számítási feladat

| Új tábla | Részletek |
| --- | --- |
| HDInsightHBaseMetrics | <ul><li>**Leírás:** Ez a tábla A HBase-ben található JMX-metrikákat tartalmazza. A Régi séma oszlopban felsorolt táblák összes JMX-metrikát tartalmazza. A régi táblákkal ellentétben minden sor egy metrikát tartalmaz.</li><li>**Régi tábla:** metrics \_ regionserver \_ CL, metrics \_ regionserver \_ wal \_ CL, metrics \_ regionserver \_ \_ ipc CL, metrics \_ regionserver \_ os \_ CL, metrics regionserver replication CL, metrics restserver CL, metrics \_ \_ \_ \_ \_ \_ restserver \_ jvm CL, metrics \_ \_ hmaster \_ assignmentmanager CL, metrics \_ \_ hmaster \_ ipc \_ CL, metrics \_ hmaser \_ os \_ CL, metrics \_ hmaster balancer CL, metrics \_ \_ \_ hmaster \_ jvm CL, metrics \_ \_ hmaster \_ CL,metrics \_ hmaster \_ fs \_ CL</li></ul>|
| HDInsightHBaseLogs | <ul><li>**Leírás:** Ez a tábla a HBase naplóit és kapcsolódó összetevőit tartalmazza: Phoenix és HDFS.</li><li>**Régi tábla:** log \_ regionserver \_ CL, log \_ restserver \_ CL, log \_ phoenixserver \_ CL, log \_ hmaster \_ CL, log \_ hdfsnamenode \_ CL, log \_ garbage collector \_ \_ CL</li></ul>|

## <a name="storm-workload"></a>Storm számítási feladat

| Új tábla | Részletek |
| --- | --- |
| HDInsightStormMetrics | <ul><li>**Leírás:** Ez a tábla a régi táblák szakasz tábláival azonos JMX-metrikákat tartalmazza. A sorok rekordonként egy metrikát tartalmaznak.</li><li>**Régi tábla:** metrikák \_ stormnimbus \_ CL, \_ metrikák stormsupervisor \_ CL</li></ul>|
| HDInsightStormTopologyMetrics | <ul><li>**Leírás:** Ez a tábla a Storm topológiaszintű metrikákat tartalmazza. Ez ugyanaz az alakzat, mint a Régi táblák szakaszban felsorolt táblázat.</li><li>**Régi tábla:** metrikák \_ stormrest \_ CL</li></ul>|
| HDInsightStormLogs | <ul><li>**Leírás:** Ez a tábla tartalmazza a Storm által létrehozott összes naplót.</li><li>**Régi tábla:** \_ \_ naplófelügyeleti CL, log \_ nimbus \_ CL</li></ul>|

## <a name="oozie-workload"></a>Oozie számítási feladat

| Új tábla | Részletek |
| --- | --- |
| HDInsightOozieLogs | <ul><li>**Leírás:** Ez a táblázat az Oozie-keretrendszerből létrehozott összes naplót tartalmazza.</li><li>**Régi tábla:** Log \_ oozie \_ CL</li></ul>|

## <a name="next-steps"></a>Következő lépések
[HdInsight Azure Monitor fürtök figyelése naplók lekérdezése](hdinsight-hadoop-oms-log-analytics-use-queries.md)
