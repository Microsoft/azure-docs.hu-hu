---
title: Azure Monitor metrikai diagramok hibaelhárítása
description: A metrikai diagramok létrehozásával, testreszabásával vagy értelmezésével kapcsolatos hibák elhárítása
author: vgorbenko
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: vitalyg
ms.openlocfilehash: 6acab9618566766b12b4b236c75aa58386100513
ms.sourcegitcommit: edc7dc50c4f5550d9776a4c42167a872032a4151
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105961064"
---
# <a name="troubleshooting-metrics-charts"></a>Metrikadiagramok hibaelhárítása

Ez a cikk akkor használható, ha a diagramok létrehozásával, testreszabásával vagy értelmezésével kapcsolatos problémákba ütközik az Azure mérőszámok Explorerben. Ha még nem ismeri a metrikákat, ismerkedjen meg a mérőszámok [Explorerrel](metrics-getting-started.md) és [a metrikák Explorer speciális funkcióival](../essentials/metrics-charts.md). Megtekintheti a konfigurált metrikai diagramokra vonatkozó [példákat](../essentials/metric-chart-samples.md) is.

## <a name="chart-shows-no-data"></a>A diagram nem jelenít meg adattípust

Időnként előfordulhat, hogy a diagramok nem jelenítenek meg adatokat a megfelelő erőforrások és mérőszámok kiválasztása után. Ezt a viselkedést az alábbi okok közül több is okozhatja:

### <a name="microsoftinsights-resource-provider-isnt-registered-for-your-subscription"></a>A Microsoft.Insights erőforrás-szolgáltató nincs regisztrálva az előfizetéshez

A metrikák megtekintéséhez a *Microsoft.Insights* erőforrás-szolgáltatónak regisztrálva kell lennie az előfizetésében. Sok esetben a regisztrálás automatikus (azaz egy riasztási szabály konfigurálása után bármely erőforráshoz testreszabhatja a diagnosztikai beállításokat, vagy automatikus skálázási szabályt konfigurálhat). Ha a Microsoft. inbetekintés erőforrás-szolgáltató nincs regisztrálva, akkor manuálisan kell regisztrálnia az [Azure erőforrás-szolgáltatók és típusok](../../azure-resource-manager/management/resource-providers-and-types.md)című témakörben ismertetett lépések szerint.

**Megoldás:** Nyissa meg az **előfizetések**, az **erőforrás-szolgáltatók** fület, és ellenőrizze, hogy regisztrálva van-e a *Microsoft. ininsight* az előfizetéséhez

### <a name="you-dont-have-sufficient-access-rights-to-your-resource"></a>Nem rendelkezik megfelelő hozzáférési engedélyekkel az erőforráshoz

Az Azure-ban a metrikák elérését az [Azure szerepköralapú hozzáférés-vezérlés (Azure RBAC)](../../role-based-access-control/overview.md)vezérli. Az erőforrások metrikáinak megtekintéséhez a [figyelési olvasó](../../role-based-access-control/built-in-roles.md#monitoring-reader), a [figyelési közreműködő](../../role-based-access-control/built-in-roles.md#monitoring-contributor) vagy a [közreműködő](../../role-based-access-control/built-in-roles.md#contributor) szerepkörhöz kell tartoznia.

**Megoldás:** Győződjön meg arról, hogy rendelkezik megfelelő engedélyekkel ahhoz az erőforráshoz, amelynek a mérőszámait vizsgálja.

### <a name="your-resource-didnt-emit-metrics-during-the-selected-time-range"></a>Az erőforrás nem bocsátott ki metrikákat a kiválasztott időtartományban

Néhány erőforrás nem bocsát ki folyamatosan metrikákat. Az Azure például nem gyűjt metrikákat a leállított virtuális gépekről. Más erőforrások csak olyan esetben bocsátanak ki metrikákat, amikor valamilyen feltétel teljesül. A tranzakciók feldolgozási idejét megjelenítő metrikákhoz például legalább egy tranzakció szükséges. Ha nincsenek tranzakciók a kiválasztott időtartományban, akkor a diagram üres lesz. És bár az Azure-ban a legtöbb metrika gyűjtése percenként történik, egyes metrikáké ritkábban. Az adott metrika dokumentációjában talál további részleteket a megtekinteni kívánt metrikáról.

**Megoldás:** A diagram időpontjának módosítása szélesebb tartományra Az "elmúlt 30 nap" kezdettől kezdve nagyobb részletességgel (vagy az "automatikus időrészletesség" lehetőséggel) lehet kezdeni.

### <a name="you-picked-a-time-range-greater-than-30-days"></a>30 napnál nagyobb időtartományt választott

[Az Azure legtöbb mérőszáma 93 napig tárolódik](../essentials/data-platform-metrics.md#retention-of-metrics). Nem kérdezheti le azonban 30 napnál nagyobb időszak adatait egyetlen diagramban. Ez a korlátozás a [naplóalapú metrikákra](../app/pre-aggregated-metrics-log-metrics.md#log-based-metrics) nem vonatkozik.

**Megoldás:** Ha üres diagramot lát, vagy a diagram csak a metrikai adatok egy részét jeleníti meg, ellenőrizze, hogy az időválasztóban a kezdő és a záró dátumok közötti különbség nem haladja-e meg a 30 napos időközt. Miután kiválasztotta a 30 napos intervallumot [, a](metrics-charts.md#pan) diagramra kattintva megtekintheti a teljes megőrzés ablakát.

### <a name="all-metric-values-were-outside-of-the-locked-y-axis-range"></a>Minden metrikaérték az y tengely zárolt tartományán kívülre esett

A [diagram y tengelye határainak zárolásával](../essentials/metrics-charts.md#locking-the-range-of-the-y-axis) akaratlanul is azt állíthatja be, hogy a diagram megjelenítési területén ne jelenjen meg a diagramvonal. Ha az y tengely például a 0% és az 50% közötti tartományban van zárolva, és a metrika állandóan 100% értékű, akkor a vonal mindig a látható területen kívülre esik, így a diagram üresnek látszik.

**Megoldás:** Ellenőrizze, hogy a diagram y tengelyének határai nem zárolva vannak-e a metrikai értékek tartományán kívül. Ha az y tengely határai zárolva vannak, érdemes lehet ideiglenesen alaphelyzetbe állítani őket annak érdekében, hogy a metrikaértékek ne essenek a diagramtartományon kívül. Az y tengely tartományának zárolása nem ajánlott az **összeg**, **min** és **max** összesítésű diagramok automatikus részletessége esetén, mert az értékeik a böngészőablak átméretezésekor vagy az egyik képernyőfelbontásról egy másikra lépéskor a részletességgel együtt változnak. A részletesség módosításakor a diagram megjelenítési területe üres maradhat.

### <a name="you-are-looking-at-a-guest-os-metric-but-didnt-enable-azure-diagnostic-extension"></a>Egy vendég operációs rendszer metrikáját keresi, de nem engedélyezte az Azure diagnosztikai bővítményt

A **vendég operációs rendszerre** vonatkozó metrikák gyűjteményéhez konfigurálni kell az Azure Diagnostics bővítményt, vagy az erőforrás **Diagnosztikai beállítások** panelén kell azt engedélyezni.

**Megoldás:** Ha a Azure Diagnostics bővítmény engedélyezve van, de továbbra sem tudja megtekinteni a metrikákat, kövesse az [Azure Diagnostics bővítmény hibaelhárítási útmutatójában](../agents/diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal)ismertetett lépéseket. Lásd még: a [vendég operációs rendszer névterének és metrikáinak nem](#cannot-pick-guest-os-namespace-and-metrics) kiválasztására vonatkozó hibaelhárítási lépések

## <a name="error-retrieving-data-message-on-dashboard"></a>"Hiba történt az adatok beolvasása közben" üzenet az irányítópulton

Ez a probléma akkor fordulhat elő, amikor az irányítópult olyan metrikával jött létre, amely később elavult, és el lett távolítva az Azure-ból. Annak ellenőrzéséhez, hogy a helyzet van-e, nyissa meg az erőforrás **metrikák** lapját, és ellenőrizze az elérhető metrikákat a metrika-választóban. Ha nem jelenik meg metrika, akkor a metrika el lett távolítva az Azure-ból. Egy metrika elavulása rendszerint azt jelenti, hogy rendelkezésre áll egy új, jobb metrika, amely hasonlóan mutatja be az erőforrás állapotát.

**Megoldás:** Módosítsa a hibás csempét úgy, hogy kiválaszt egy alternatív metrikát a diagramhoz az irányítópulton. [Megtekintheti az Azure-szolgáltatásokhoz elérhető metrikák listáját](./metrics-supported.md).

## <a name="chart-shows-dashed-line"></a>A diagramban szaggatott vonal jelenik meg

Az Azure-metrikák diagramjai szaggatott vonal stílusát használják annak jelzésére, hogy a hiányzó érték (más néven "Null érték") két ismert időszelet adatpontja között van. Ha például az időválasztóban az "1 perc" idő részletességét adta meg, de a mérőszámot 07:26, 07:27, 07:29 vagy 07:30 értéken jelentették (jegyezze fel a második és harmadik adatpontok közötti percenkénti eltérést), akkor egy szaggatott vonal fogja összekapcsolni az 07:27 és az 07:29, és egy folytonos vonal fogja összekapcsolni az összes többi adatpontot. A szaggatott vonal nullára csökken, ha a metrika a **darabszámot** és a **Sum** összesítést használja. Az **AVG**, **min** vagy **Max** összesítések esetében a szaggatott vonal két legközelebbi ismert adatpontot kapcsol össze. Ha pedig az adatok a diagram jobb vagy bal szélén hiányoznak, akkor rendszer meghosszabbítja a szaggatott vonalat a hiányzó adatpont irányába.
  ![Képernyőfelvétel: a diagram jobb szélső vagy bal szélső oldalán lévő adat hiánya esetén a szaggatott vonal a hiányzó adatpont irányára bővül.](./media/metrics-troubleshoot/dashed-line.png)

**Megoldás:** Ez a viselkedés a tervezés szerint történik. Ez a hiányzó adatpontok azonosításához hasznos. A vonalas diagram kiváló választás a nagy sűrűségű mérőszámok trendjeinek megjelenítéséhez, de nehéz lehet értelmezni a ritka értékekkel rendelkező metrikákat, különösen akkor, ha fontosak az időgabona-vel kapcsolatos kapcsolódó értékek. A szaggatott vonal megkönnyíti az ilyen diagramok leolvasását, ha azonban a diagram még mindig nem egyértelmű, érdemes lehet más diagramtípussal megtekinteni a metrikákat. Az egyazon mérőszámhoz tartozó elszórt diagram például világosan mutatja, hogy az egyes időkeretek csak akkor jelennek meg, ha van érték, és az adatpontot teljesen kihagyja, ha az érték hiányzik: ![ képernyőkép, amely kiemeli a pontdiagram menüpontot.](./media/metrics-troubleshoot/scatter-plot.png)

   > [!NOTE]
   > Ha továbbra is vonaldiagramot szeretne használni a metrikához, akkor mozgassa az egérmutatót a diagram fölé, így az egérmutató helyén lévő adatpont kiemelése révén könnyebben felmérheti az időrészletességet.

## <a name="chart-shows-unexpected-drop-in-values"></a>A diagramban az értékek váratlan csökkenése látható

Sok esetben a metrikaértékek észlelt csökkenése a diagramon látható adatokkal kapcsolatos félreértésből ered. Az összegek vagy a számok csökkenése félrevezető lehet, ha a diagramon a legfrissebb percek láthatók, mert az Azure még nem kapta meg vagy nem dolgozta fel a legutóbbi metrikaadatpontokat. A szolgáltatástól függően a metrikák feldolgozásának késése néhány perces lehet. Az 1 vagy 5 perces részletességgel rendelkező legutóbbi időtartományt bemutató diagramok esetében az elmúlt néhány percben az érték egy cseppe ![ látható: képernyőfelvétel, amely az utolsó pár percben az érték csökkenését mutatja.](./media/metrics-troubleshoot/unexpected-dip.png)

**Megoldás:** Ez a viselkedés a tervezés szerint történik. Úgy gondoljuk, hogy az adatok lehető legkorábban történő megjelenítése akkor is előnyös, ha az adatok *részlegesek* vagy *nem teljesek*. Ennek köszönhetően hamarabb juthat fontos következtetésekre, és azonnal megkezdheti a vizsgálatot. Egy, a hibák számát megjelenítő metrika esetén például az X részleges érték elárulja, hogy egy adott percben legalább X hiba fordult elő. Azonnal megkezdheti a probléma vizsgálatát, és nem kell megvárnia, hogy pontosan hány hiba történt ebben a percben, ami talán nem is olyan fontos. A diagram a teljes adatkészlet fogadásakor frissül, de ekkor új nem teljes adatpontokat is jeleníthet meg a még frissebb percekből.

## <a name="cannot-pick-guest-os-namespace-and-metrics"></a>A vendég operációs rendszer névtere és mérőszámai nem választhatók ki

A virtuális gépeknek és virtuálisgép-méretezési csoportoknak két metrikakategóriája van: **Virtuálisgép-gazda** metrikák, amelyeket az Azure üzemeltetési környezete gyűjt, és a virtuális gépeken futó [figyelőügynök](../agents/agents-overview.md) által gyűjtött **Vendég operációs rendszer (klasszikus)** metrikák. A figyelőügynök az [Azure Diagnostics bővítmény](../agents/diagnostics-extension-overview.md) engedélyezésével telepíthető.

Alapértelmezés szerint a Vendég operációs rendszer típusú metrikákat az Azure-tárfiók tartalmazza, amelyet az erőforrás **Diagnosztikai beállítások** lapján választhat ki. Ha a vendég operációs rendszer metrikáit nem gyűjti a rendszer, vagy a metrikaböngésző nem tudja elérni őket, akkor csak a **Virtuálisgép-gazda** metrika névterét látja:

![metrika képe](./media/metrics-troubleshoot/vm.png)

**Megoldás:** Ha nem látja a **vendég operációs rendszer (klasszikus)** névterét és mérőszámait a metrikák Explorerben:

1. Győződjön meg róla, hogy az [Azure Diagnostics bővítmény](../agents/diagnostics-extension-overview.md) engedélyezve van és be van állítva a metrikák gyűjtésére.
    > [!WARNING]
    > A [Log Analytics ügynök](../agents/agents-overview.md#log-analytics-agent) (más néven Microsoft Monitoring Agent, vagy MMA) használatával a **Vendég operációs rendszer** nem küldhető tárfiókba.

1. Győződjön meg arról, hogy a **Microsoft. inbetekintés** erőforrás-szolgáltató [regisztrálva van az előfizetéséhez](#microsoftinsights-resource-provider-isnt-registered-for-your-subscription).

1. Győződjön meg arról, hogy a tárfiókot nem védi a tűzfal. Az Azure Portalnak hozzá kell férnie a tárfiókhoz, hogy lekérhesse a metrikai adatokat és összeállíthassa a diagramokat.

1. A [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) használatával ellenőrizheti, hogy a metrikák beáramlanak-e a Storage-fiókba. Ha a metrikák gyűjtése nem történik meg, kövesse az [Azure Diagnostics bővítmény hibaelhárítási útmutatóját](../agents/diagnostics-extension-troubleshooting.md#metric-data-doesnt-appear-in-the-azure-portal).

## <a name="next-steps"></a>Következő lépések

* [Ismerkedés a metrikus Explorer használatába](metrics-getting-started.md)
* [A metrika Explorer speciális funkcióinak megismerése](../essentials/metrics-charts.md)
* [Az Azure-szolgáltatásokhoz elérhető metrikák listájának megtekintése](./metrics-supported.md)
* [A konfigurált diagramok példáinak megtekintése](../essentials/metric-chart-samples.md)