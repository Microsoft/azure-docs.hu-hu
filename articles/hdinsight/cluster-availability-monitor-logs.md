---
title: A fürt rendelkezésre állásának figyelése Azure Monitor naplókkal a HDInsight-ben
description: Megtudhatja, hogyan használhatja Azure Monitor naplókat a fürt állapotának és rendelkezésre állásának figyeléséhez.
ms.service: hdinsight
ms.topic: how-to
ms.date: 08/12/2020
ms.openlocfilehash: 299a17e23ca3eb2d954bae7335571ae1f645152e
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104867151"
---
# <a name="how-to-monitor-cluster-availability-with-azure-monitor-logs-in-hdinsight"></a>A fürt rendelkezésre állásának figyelése Azure Monitor naplókkal a HDInsight-ben

A HDInsight-fürtök közé tartoznak a Azure Monitor naplók integrációja, amely lekérdezhető mérőszámokat és naplókat, valamint konfigurálható riasztásokat biztosít. Ez a cikk bemutatja, hogyan használható a Azure Monitor a fürt figyelésére.

## <a name="azure-monitor-logs-integration"></a>Azure Monitor naplók integrációja

Azure Monitor naplók lehetővé teszik több erőforrás, például a HDInsight-fürtök által generált adatok gyűjtését és összesítését egy helyen egy egységes figyelési élmény eléréséhez.

Előfeltételként szükség lesz egy Log Analytics munkaterületre az összegyűjtött adatok tárolásához. Ha még nem hozott létre ilyet, kövesse az alábbi utasításokat: [log Analytics munkaterület létrehozása](../azure-monitor/logs/quick-create-workspace.md).

## <a name="enable-hdinsight-azure-monitor-logs-integration"></a>HDInsight-Azure Monitor naplók integrációjának engedélyezése

A portál HDInsight-fürterőforrás lapján válassza a **Azure monitor** lehetőséget. Ezután válassza az **Engedélyezés** lehetőséget, majd válassza ki a log Analytics munkaterületet a legördülő menüből.

:::image type="content" source="media/cluster-availability-monitor-logs/azure-portal-monitoring.png" alt-text="HDInsight Operations Management Suite":::

Alapértelmezés szerint a OMS-ügynököt az összes fürtcsomóponton telepíti az Edge-csomópontok kivételével. Mivel a OMS-ügynök nem lett telepítve a fürt peremhálózati csomópontjain, alapértelmezés szerint nincs Log Analytics az Edge-csomópontok telemetria.

## <a name="query-metrics-and-logs-tables"></a>Mérőszámok és naplók táblázatának lekérdezése

Azure Monitor naplózási integráció engedélyezése után (ez eltarthat néhány percig), navigáljon a **log Analytics munkaterület** -erőforráshoz, és válassza a **naplók** lehetőséget.

:::image type="content" source="media/cluster-availability-monitor-logs/hdinsight-portal-logs.png" alt-text="Log Analytics munkaterület naplófájljai":::

A naplók számos példa típusú lekérdezést listáznak, például:

| Lekérdezés neve                      | Leírás                                                               |
|---------------------------------|---------------------------------------------------------------------------|
| A számítógépek rendelkezésre állása ma    | A naplókat küldő számítógépek számának diagramja óránként                     |
| Szívverések listázása                 | Az összes számítógép szívverésének listázása az elmúlt órában                           |
| Az egyes számítógépek utolsó szívverése | Az egyes számítógépek által eljuttatott utolsó szívverés megjelenítése                             |
| Nem elérhető számítógépek           | Az összes olyan ismert számítógép listázása, amely nem küldött szívverést az elmúlt 5 órában |
| Rendelkezésre állási arány               | Az egyes csatlakoztatott számítógépek rendelkezésre állási arányának kiszámítása                |

Futtassa például a **rendelkezésre állási arány** mintájának lekérdezését a lekérdezés **futtatásának** kiválasztásával, ahogy az a fenti képernyőképen is látható. Ez a fürt egyes csomópontjainak rendelkezésre állási arányát fogja megjeleníteni százalékban. Ha több HDInsight-fürtön is engedélyezte a metrikák küldését ugyanarra a Log Analytics munkaterületre, akkor a megjelenő fürtökben megjelenik az összes csomópont (az Edge-csomópontok kivételével) rendelkezésre állási sebessége.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-availability-rate.png" alt-text="Log Analytics munkaterület &quot;rendelkezésre állási arány&quot; mintájának lekérdezése":::

> [!NOTE]  
> A rendelkezésre állási sebességet 24 órás időszakra mérjük, így a fürtnek legalább 24 órán át futnia kell, mielőtt a pontos rendelkezésre állási díjakat látni fogja.

Ezt a táblázatot a jobb felső sarokban található **rögzítés** gombra kattintva rögzítheti egy megosztott irányítópulton. Ha nem rendelkezik írható megosztott irányítópultokkal, megtekintheti, hogyan hozhat létre egyet itt: [irányítópultok létrehozása és megosztása a Azure Portal](../azure-portal/azure-portal-dashboards.md#publish-and-share-a-dashboard).

## <a name="azure-monitor-alerts"></a>Riasztások Azure Monitor

Beállíthat Azure Monitor riasztásokat is, amelyek akkor aktiválódnak, ha egy metrika értéke vagy egy lekérdezés eredményei megfelelnek bizonyos feltételeknek. Például hozzon létre egy riasztást, amely e-mailt küld, ha egy vagy több csomópont 5 órán belül nem küldött szívverést (azaz nem érhető el).

A **naplókból** futtassa a nem **elérhető számítógépek** minta lekérdezést úgy, hogy a lekérdezés **Futtatás** parancsát választja, az alább látható módon.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-unavailable-computers.png" alt-text="Log Analytics munkaterület &quot;nem elérhető számítógépek&quot; mintát naplóz":::

Ha az összes csomópont elérhető, a lekérdezésnek most nulla eredményt kell visszaadnia. Kattintson az **új riasztási szabály** elemre a lekérdezéshez tartozó riasztás konfigurálásának megkezdéséhez.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-logs-new-alert-rule.png" alt-text="Log Analytics munkaterület új riasztási szabálya":::

A riasztásnak három összetevője van: az *erőforrás* , amelyhez létre kell hozni a szabályt (ebben az esetben a log Analytics munkaterület), a riasztás aktiválásának *feltételét* , valamint azokat a *műveleti csoportokat* , amelyek meghatározzák, hogy mi fog történni a riasztás indításakor.
Az alább látható **feltétel címére** kattintva fejezze be a jel logikájának konfigurálását.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-condition-title.png" alt-text="Portál riasztás létrehozása szabály feltétele":::

Ekkor megnyílik a **jel logikájának konfigurálása**.

A **riasztási logika** szakasz a következőképpen állítható be:

*A következő alapján: eredmények száma, feltétel: nagyobb, mint, küszöbérték: 0.*

Mivel ez a lekérdezés csak a nem elérhető csomópontokat adja vissza eredményként, ha az eredmények száma egyre nagyobb nullánál, a riasztásnak tüzet kell mutatnia.

A **kiértékelt szakasz alapján** állítsa be az **időszakot** és a **gyakoriságot** attól függően, hogy milyen gyakran kívánja a nem elérhető csomópontok keresését.

Ennek a riasztásnak a kihasználása érdekében meg kell győződnie arról, hogy az **időtartam = Frequency.** Az időtartammal, gyakorisággal és egyéb riasztási paraméterekkel kapcsolatos további információkat [itt](../azure-monitor/alerts/alerts-unified-log.md#alert-logic-definition)talál.

Ha befejezte a jel logikájának konfigurálását, válassza a **kész** lehetőséget.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-configure-signal-logic.png" alt-text="Riasztási szabály konfigurálja a jel logikáját":::

Ha még nem rendelkezik meglévő műveleti csoporttal, kattintson az **új létrehozása** elemre a **műveleti csoportok** szakaszban.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-create-new-action-group.png" alt-text="Riasztási szabály új műveleti csoportot hoz létre":::

Ekkor megnyílik a **Hozzáadás műveleti csoport**. Válassza ki a **műveleti csoport nevét**, a **rövid nevet**, az **előfizetést** és az **erőforráscsoportot.** A **műveletek** szakaszban válassza ki a **művelet nevét** , és válassza az **E-mail/SMS/leküldés/hang** lehetőséget a **művelet típusaként.**

> [!NOTE]
> Több más művelet is elindítható egy e-mail/SMS/push/hang mellett, például egy Azure-függvény, a LogicApp, a webhook, a ITSM és az Automation Runbook mellett. [tudj meg többet.](../azure-monitor/alerts/action-groups.md#action-specific-information)

Ekkor megnyílik az **e-mail/SMS/leküldés/hang**. Válassza ki a címzett **nevét** , **jelölje be** az **e-mail** szövegmezőt, és írjon be egy e-mail-címet, amelyre a riasztást el szeretné juttatni. Válassza az **OK gombot**  **e-mailben, SMS-ben/leküldés/hangban**, majd a **műveleti csoport hozzáadása** elemnél a műveleti csoport konfigurálásának befejezéséhez.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-add-action-group.png" alt-text="Riasztási szabály – hozzáadási műveleti csoport létrehozása":::

Miután ezek a pengék bezárultak, a műveleti **csoportok** szakaszban szereplő műveleti csoportnak kell megjelennie. Végül fejezze be a **riasztás részletei** szakaszt a **riasztási szabály nevének** és **leírásának** beírásával és a **Súlyosság** kiválasztásával. A befejezéshez kattintson a **riasztási szabály létrehozása** elemre.

:::image type="content" source="media/cluster-availability-monitor-logs/portal-create-alert-rule-finish.png" alt-text="A portál riasztási szabályt hoz létre Befejezés":::

> [!TIP]
> A **Súlyosság** meghatározásának lehetősége egy hatékony eszköz, amely több riasztás létrehozásakor is használható. Létrehozhat például egy riasztást egy figyelmeztetés (1. pont) létrehozásához, ha egy fő csomópont leáll, és egy másik riasztás, amely kritikus (a 0. szint) állapotot eredményez abban a valószínűtlen eseményben, amelyet a főcsomópontok leállnak.

Ha a riasztás feltétele teljesül, a riasztás tüzet fog kapni, és e-mailben megkapja a riasztás részleteit, például a következőt:

:::image type="content" source="media/cluster-availability-monitor-logs/portal-oms-alert-email.png" alt-text="Azure Monitor riasztási e-mail példa":::

Megtekintheti az összes kilőtt riasztást, súlyosság szerint csoportosítva, a **log Analytics munkaterületen** lévő **riasztások** megadásával.

:::image type="content" source="media/cluster-availability-monitor-logs/hdi-portal-oms-alerts.png" alt-text="Log Analytics munkaterület riasztásai":::

A súlyossági csoportosítás (például az **1.,** a fenti Kiemelt) kiválasztásakor az adott súlyosságú riasztásokra vonatkozó rekordok jelennek meg, amelyek az alábbihoz hasonlóak:

:::image type="content" source="media/cluster-availability-monitor-logs/portal-oms-alerts-sev1.png" alt-text="Log Analytics munkaterület – egyetlen riasztás":::

## <a name="next-steps"></a>Következő lépések

* [Fürt rendelkezésre állása – Apache Ambari](./hdinsight-cluster-availability.md)
* [Azure Monitor naplók használata](hdinsight-hadoop-oms-log-analytics-tutorial.md)
