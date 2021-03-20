---
title: Az adatbázis teljesítményének figyelése Intelligent Insights
description: A Azure SQL Database és az Azure SQL felügyelt példányának Intelligent Insights a beépített intelligenciával folyamatosan figyeli az adatbázis-használatot mesterséges intelligencia használatával, és felismeri a gyenge teljesítményt okozó zavaró eseményeket.
services: sql-database
ms.service: sql-db-mi
ms.subservice: performance
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 06/12/2020
ms.openlocfilehash: 61033e3eb8264c1e462faac3e4553a855a1d06c7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100592074"
---
# <a name="intelligent-insights-using-ai-to-monitor-and-troubleshoot-database-performance-preview"></a>Intelligent Insights AI használata az adatbázis teljesítményének figyelésére és hibakeresésére (előzetes verzió)
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

A Azure SQL Database és az Azure SQL felügyelt példányának Intelligent Insights segítségével megtudhatja, hogy mi történik az adatbázis teljesítményével.

A Intelligent Insights a beépített intelligenciával folyamatosan figyeli az adatbázis-használatot a mesterséges intelligencián, és felismeri a gyenge teljesítményt okozó zavaró eseményeket. Az észlelést követően a rendszer részletes elemzést végez, amely egy Intelligent Insights erőforrás-naplót (SQLInsights) hoz létre a probléma intelligens értékelésével. Ez az értékelés az adatbázis teljesítményével kapcsolatos probléma okának elemzését, és ahol lehetséges, a teljesítménnyel kapcsolatos javításokra vonatkozó javaslatokat tartalmaz.

## <a name="what-can-intelligent-insights-do-for-you"></a>Mit tehet Intelligent Insights

A Intelligent Insights az Azure beépített intelligenciának egyedülálló funkciója, amely a következő értéket biztosítja:

- Proaktív figyelés
- Testreszabott teljesítmény-felismerés
- Az adatbázis-teljesítmény romlásának korai észlelése
- Az észlelt problémák kiváltó okának elemzése
- Teljesítmény-javító javaslatok
- Több százezer adatbázisra kibővíthető képesség
- Pozitív hatással van a DevOps-erőforrásokra és a teljes tulajdonlási díjakra

## <a name="how-does-intelligent-insights-work"></a>Hogyan működik Intelligent Insights

Intelligent Insights elemzi az adatbázis teljesítményét, ha összehasonlítja az elmúlt órában az elmúlt hét napos alapkonfiguráció számítási feladatait. Az adatbázis-munkaterhelés olyan lekérdezésekből áll, amelyek úgy vannak meghatározva, hogy az adatbázis teljesítményének legjelentősebb részét, például a legtöbbször ismétlődő és legnagyobb lekérdezéseket. Mivel az egyes adatbázisok egyediek a szerkezettől, az adatoktól, a használattól és az alkalmazástól, az egyes generált számítási feladatok egyediek és egyediek a számítási feladatok esetében. Intelligent Insights a munkaterhelés alapkonfigurációtól függetlenül az abszolút működési küszöbértékeket is figyeli, és észleli a túlzott várakozási idő, a kritikus kivételek és a teljesítményre hatással lehet a lekérdezési parameterizations kapcsolatos problémákat.

Ha a teljesítmény romlása problémát észlelt több megfigyelt metrika alapján mesterséges intelligencia használatával, az elemzés elvégzése történik. A diagnosztikai naplót intelligens betekintéssel generáljuk, hogy mi történik az adatbázissal. A Intelligent Insights segítségével egyszerűen nyomon követheti az adatbázis teljesítményét a megoldás első megjelenése után. A rendszer minden észlelt problémát az életciklusa alapján követ nyomon a kezdeti probléma észlelése és a teljesítmény javításának ellenőrzése után.

![Adatbázis-teljesítmény elemzésének munkafolyamata](./media/intelligent-insights-overview/intelligent-insights-concept.png)

Az adatbázis-teljesítménnyel kapcsolatos problémák méréséhez és észleléséhez használt mérőszámok lekérdezési időtartamon, időtúllépési kérelmeken, túlzott várakozási időpontokon és hibás kérelmeken alapulnak. A metrikákkal kapcsolatos további információkért lásd: [észlelési metrikák](#detection-metrics).

Az azonosított adatbázis-teljesítménycsökkenés a következő tulajdonságokkal rendelkező intelligens bejegyzéseket tartalmazó SQLInsights-naplóban van rögzítve:

| Tulajdonság | Részletek |
| :------------------- | ------------------- |
| Adatbázis-információk | Metaadatok egy olyan adatbázisról, amelyen a rendszer betekintést észlelt, például egy erőforrás-URI-t. |
| Megfigyelt időtartam tartománya | Az észlelt elemzés időszakának kezdési és befejezési időpontja. |
| Érintett mérőszámok | A betekintést kiváltó mérőszámok: <ul><li>A lekérdezés időtartama növekszik [másodperc].</li><li>Túlzott várakozás [másodperc].</li><li>Időtúllépési kérelmek [százalék].</li><li>Hiba miatti kérelmek [százalék].</li></ul>|
| Hatás értéke | A mért metrika értéke. |
| Érintett lekérdezések és hibakódok | Lekérdezési kivonat vagy hibakód. Ezek az érintett lekérdezések egyszerű összekapcsolására használhatók. A lekérdezés időtartamának növekedését, a várakozási időt, az időtúllépési számokat vagy a hibakódokat tartalmazó mérőszámok vannak megadva. |
| Észlelések | Az esemény során azonosított észlelés az adatbázisban. 15 észlelési minta van. További információ: [az adatbázis teljesítményével kapcsolatos hibák elhárítása Intelligent Insights](intelligent-insights-troubleshoot-performance.md). |
| Kiváltó okok elemzése | Az ember által olvasható formátumban azonosított probléma okának elemzése. Egyes bepillantások teljesítmény-javító javaslatot is tartalmazhatnak, ahol lehetséges. |
|||

Ha gyakorlati áttekintést kíván a Intelligent Insights és a Azure SQL Analytics használatáról, valamint a tipikus használati forgatókönyvekről, tekintse meg ezt a videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Get-Intelligent-Insights-for-Improving-Azure-SQL-Database-Performance/player]
>

Intelligent Insights az adatbázis-teljesítménnyel kapcsolatos problémák felderítésében és hibaelhárításában. Az adatbázis-teljesítménnyel kapcsolatos problémák elhárításához Intelligent Insights használatához tekintse meg [a Intelligent Insights teljesítménnyel kapcsolatos problémák elhárítása](intelligent-insights-troubleshoot-performance.md)című témakört.

## <a name="intelligent-insights-options"></a>Intelligent Insights beállítások

Az elérhető Intelligent Insights lehetőségek a következők:

| Intelligent Insights lehetőség | Azure SQL Database támogatás | Az Azure SQL felügyelt példányainak támogatása |
| :----------------------------- | ----- | ----- |
| **Intelligent Insights konfigurálása** – az adatbázisok Intelligent Insights elemzésének konfigurálása. | Igen | Yes |
| **A Azure SQL Analytics adatfolyam** -bepillantást nyerhet a Azure SQL Analytics. | Igen | Yes |
| Az **Azure Event Hubs** adatfolyam-bepillantást nyerhet a további egyéni integrációk Event Hubs. | Igen | Yes |
| Az **Azure Storage bepillantást nyerhet** az Azure Storage-ba, ahol további elemzést és hosszú távú archiválást végezhet. | Igen | Yes |

> [!NOTE]
> Az intelligens betekintő funkció az alábbi régiókban nem érhető el: Nyugat-Európa, Észak-Európa, USA 2. nyugati régiója és USA 1. keleti régiója.

## <a name="configure-the-export-of-the-intelligent-insights-log"></a>Az Intelligent Insights napló exportálásának konfigurálása

A Intelligent Insights kimenete az elemzéshez több célhelyre is továbbítható:

- A Log Analytics munkaterületre továbbított kimenet a Azure Portal felhasználói felületén keresztül is megtekinthető a [Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md) használatával. Ez az integrált Azure-megoldás, amely a legfontosabb módszer az ismeretek megtekintésére.
- Az Azure Event Hubsba továbbított kimenet használható az egyéni figyelési és riasztási forgatókönyvek fejlesztéséhez
- Az Azure Storage-ba továbbított kimenet használható az egyéni alkalmazások fejlesztéséhez, például az egyéni jelentéskészítéshez, a hosszú távú adatarchiváláshoz és így tovább.

Az Azure SQL Analytics, az Azure Event Hubs, az Azure Storage vagy a harmadik féltől származó termékek a használathoz való integrációját az adatbázis diagnosztikai Beállítások paneljén, a "SQLInsights" naplóban, majd a Intelligent Insights naplózási adatainak a következő célhelyek egyikére való továbbításával Intelligent Insights hajtják végre.

További információ a Intelligent Insights naplózásának engedélyezéséről és a metrika és az erőforrás-naplózási adatok egy felhasználható termékre való továbbításáról: [metrika és diagnosztika naplózása](metrics-diagnostic-telemetry-logging-streaming-export-configure.md).

### <a name="set-up-with-azure-sql-analytics"></a>Beállítás a Azure SQL Analytics

Azure SQL Analytics a megoldás grafikus felhasználói felületet, jelentéskészítési és riasztási képességeket biztosít az adatbázis-teljesítményhez az Intelligent Insights erőforrás-naplózási adatai segítségével.

Vegyen fel Azure SQL Analyticseket a piactéren a Azure Portal irányítópultra, és hozzon létre egy munkaterületet: [configure Azure SQL Analytics](../../azure-monitor/insights/azure-sql.md#configuration)

Ha Intelligent Insightst szeretne használni a Azure SQL Analytics, konfigurálja Intelligent Insights naplózási adatokat az előző lépésben létrehozott Azure SQL Analytics munkaterületre, és tekintse meg a [metrikák és a diagnosztika naplózása](metrics-diagnostic-telemetry-logging-streaming-export-configure.md)című témakört.

Az alábbi példa egy Azure SQL Analyticson keresztül megtekintett Intelligent Insights mutat be:

![Intelligent Insights jelentés](./media/intelligent-insights-overview/intelligent-insights-azure-sql-analytics.png)

### <a name="set-up-with-event-hubs"></a>Beállítás a Event Hubs

Ahhoz, hogy a Intelligent Insightst Event Hubs használatával lehessen használni, a Intelligent Insights naplózási adatokat Event Hubsra kell továbbítani, a [metrikák és diagnosztika naplózása](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) és [Az Azure Diagnostics-naplók továbbítása a Event Hubsba](../../azure-monitor/essentials/resource-logs.md#send-to-azure-event-hubs)című témakörben talál.

Ha egyéni figyelést és riasztást szeretne beállítani a Event Hubs használatával, tekintse meg a [Mi a teendő a metrikákkal és a diagnosztikai naplókkal foglalkozó](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#what-to-do-with-metrics-and-resource-logs-in-event-hubs)témakört Event Hubs.

### <a name="set-up-with-azure-storage"></a>Beállítás az Azure Storage-ban

Ha Intelligent Insightst szeretne használni a Storage szolgáltatással, konfigurálja Intelligent Insights naplózási adatokat a tárolóba, lásd: [metrikák és diagnosztikai naplózás](metrics-diagnostic-telemetry-logging-streaming-export-configure.md) és [stream az Azure Storage-ba](metrics-diagnostic-telemetry-logging-streaming-export-configure.md#stream-into-azure-storage).

### <a name="custom-integrations-of-intelligent-insights-log"></a>Intelligent Insights napló egyéni integrációja

Ha a Intelligent Insightst külső gyártótól származó eszközökkel szeretné használni, vagy egyéni riasztási és figyelési fejlesztésre van használatban, tekintse meg [a Intelligent Insights adatbázis-teljesítmény diagnosztikai naplójának használata](intelligent-insights-use-diagnostics-log.md)című részt

## <a name="detection-metrics"></a>Észlelési metrikák

A Intelligent Insightst létrehozó modellek észleléséhez használt metrikák a figyelésen alapulnak:

- Lekérdezés időtartama
- Időtúllépési kérelmek
- Túlzott várakozási idő
- Hiba történt a kérelmeknél

A lekérdezés időtartama és az időtúllépési kérelmek elsődleges modellként használatosak az adatbázis-munkaterhelés teljesítményével kapcsolatos problémák észlelése érdekében. Ezeket azért használják, mert közvetlenül mérik, hogy mi történik a munkaterhelés esetében. A számítási feladatok teljesítményének összes lehetséges esetének észlelése érdekében a túlzott várakozási idő és a hibás kimenő kérelmek további modellként használatosak a munkaterhelés teljesítményét befolyásoló problémák jelzéséhez.

A rendszer automatikusan megtekinti a számítási feladatok változásait, és megváltoztatja az adatbázisra irányuló lekérdezési kérelmek számát, hogy dinamikusan meghatározza a normál és az általános adatbázis-teljesítmény küszöbértékeit.

Az összes mérőszámot egy tudományosan származtatott adatmodellben, amely az észlelt teljesítménnyel kapcsolatos problémákat kategorizálja, különböző kapcsolatokban tekinti át. Az intelligens betekintés által biztosított információk a következők:

- Az észlelt teljesítménnyel kapcsolatos probléma részletei.
- Az észlelt probléma kiváltó okának elemzése.
- Javaslatok a figyelt adatbázis teljesítményének növeléséhez, ahol lehetséges.

## <a name="query-duration"></a>Lekérdezés időtartama

A lekérdezés időtartamának romlási modellje elemzi az egyes lekérdezéseket, és észleli a lekérdezés fordításához és végrehajtásához szükséges időt a teljesítménnyel kapcsolatos alaptervhez képest.

Ha a beépített intelligencia jelentős növekedést észlel a lekérdezés fordítása vagy a lekérdezés végrehajtási ideje között, amely hatással van a számítási feladatok teljesítményére, a lekérdezések lekérdezési időtartamának romlási problémái lesznek megjelölve.

A Intelligent Insights diagnosztikai napló kimenetében a lekérdezés kivonata lecsökkentett teljesítményű. A lekérdezési kivonat azt jelzi, hogy a teljesítmény romlása kapcsolódott-e a lekérdezés fordításához vagy a végrehajtási idő növeléséhez, ami megnövelte a lekérdezés időtartamának időpontját.

## <a name="timeout-requests"></a>Időtúllépési kérelmek

Az időtúllépési kérelmek romlási modellje egyéni lekérdezéseket elemez, és észleli az időtúllépések növekedését a lekérdezés végrehajtási szintjén, valamint a kérelmek összesített időtúllépését az adatbázis szintjén, a teljesítmény-alapidőszakhoz képest.

Előfordulhat, hogy néhány lekérdezés időtúllépést okoz, még mielőtt elérné a végrehajtási szakaszt. A megszakított feldolgozók és a beérkező kérések, a beépített intelligenciával kapcsolatos intézkedések és az adatbázishoz tartozó összes olyan lekérdezés elemzése, amely elérte az adatbázist, függetlenül attól, hogy azok a végrehajtási fázisba kerültek-e.

Miután a futtatott lekérdezések időtúllépési száma vagy a megszakított kérelmek száma átlépi a rendszer által felügyelt küszöbértéket, a diagnosztikai naplót intelligens megállapítások töltik fel.

A generált megállapítások az időtúllépési kérelmek számát és az időtúllépési lekérdezések számát tartalmazzák. A teljesítmény romlásának jelzése a végrehajtási fázis időtúllépési növekedésével vagy az adatbázis teljes szintjének megadásával kapcsolatos. Ha az időtúllépések növekedése az adatbázis teljesítményének jelentős része, akkor ezek a lekérdezések időtúllépési teljesítménybeli romlási problémákként vannak megjelölve.

## <a name="excessive-wait-times"></a>Túlzott várakozási idő

A túlzott várakozási idő modell figyeli az egyes adatbázis-lekérdezéseket. Szokatlanul nagy lekérdezések várakozási statisztikáit észleli, amelyek átlépték a rendszer által felügyelt abszolút küszöbértékeket. A következő lekérdezés túlzott várakozási idejű mérőszámokat észlelt a, a [lekérdezési tároló várakozási statisztikái (sys.query_store_wait_stats)](/sql/relational-databases/system-catalog-views/sys-query-store-wait-stats-transact-sql)használatával:

- Erőforrás-korlátok elérése
- Rugalmas készlet erőforrás-korlátainak elérése
- A munkavégző vagy a munkamenet szála túl sok
- Túlzott adatbázis-zárolás
- Memóriaterhelés
- Egyéb várakozási statisztikák

Az erőforrás-korlátok vagy a rugalmas készlet erőforrás-korlátainak elérése azt jelzi, hogy az előfizetésben vagy a rugalmas készletben elérhető erőforrások felhasználása túllépte az abszolút küszöbértékeket. Ezek a statisztikák a munkaterhelés teljesítményének csökkenését jelzik. A munkavégző vagy a munkamenet-szálak túlzott száma azt jelzi, hogy a feldolgozói szálak vagy munkamenetek száma az abszolút küszöbértékeket eredményezte. Ezek a statisztikák a munkaterhelés teljesítményének csökkenését jelzik.

A túlzott adatbázis-zárolás olyan feltételt jelöl, amelyben az adatbázis Zárolások száma abszolút küszöbértéket eredményezett. Ez a stat a munkaterhelés teljesítményének csökkenését jelzi. A memória-nyomás olyan feltétel, amelyben a memóriát kérő szálak száma abszolút küszöbértéket eredményezett. Ez a stat a munkaterhelés teljesítményének csökkenését jelzi.

Az egyéb várakozási statisztikák észlelése azt a feltételt jelzi, hogy a lekérdezési tároló várakozási statisztikájában mért különféle mérőszámok abszolút küszöbértéket mutatnak. Ezek a statisztikák a munkaterhelés teljesítményének csökkenését jelzik.

Ha a rendszer túlzott várakozási időt észlel, a rendelkezésre álló adatoktól függően a Intelligent Insights diagnosztikai napló kimenete a teljesítmény, a lekérdezéseket a végrehajtásra vár, és a várakozási idő mérését kiváltó mérőszámok részletei.

## <a name="errored-requests"></a>Hibás kérelmek

A hibás kérelmek romlási modellje figyeli az egyes lekérdezéseket, és az alapidőszakhoz képest hibát okozó lekérdezések számának növekedését észleli. Ez a modell a beépített intelligencia által kezelt abszolút küszöbértékeket is figyeli. A rendszer automatikusan figyelembe veszi az adatbázisnak küldött lekérdezési kérések számát, valamint a figyelt időszakban felmerülő munkaterhelés-változásokhoz tartozó fiókokat.

Ha a hibás kérelmeknek a kérelmek teljes számához viszonyított megváltozása a számítási teljesítmény szempontjából jelentősnek számít, az érintett lekérdezések hibát jeleznek a teljesítmény romlásával kapcsolatban.

A Intelligent Insights napló a hibás kérelmek számát adja eredményül. Ez azt jelzi, hogy a teljesítmény romlása a hibás kérelmek növekedésével vagy a figyelt kritikus kivételek küszöbértékének és a teljesítmény romlásának mért idejével kapcsolatos-e.

Ha a figyelt kritikus kivételek bármelyike átlépi a rendszer által kezelt abszolút küszöbértékeket, akkor a kritikus kivétel részleteivel intelligens elemzés jön létre.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [figyelheti meg az adatbázisokat az SQL Analytics használatával](../../azure-monitor/insights/azure-sql.md).
- Ismerje meg, hogy miként lehet [elhárítani a Intelligent Insights teljesítményével kapcsolatos problémákat](intelligent-insights-troubleshoot-performance.md).