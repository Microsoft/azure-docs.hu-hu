---
title: Munkafüzetek gyűjteménye Azure Security Center
description: Ismerje meg, hogyan hozhat létre részletes, interaktív jelentéseket a Azure Security Center-adatairól az integrált Azure Monitor munkafüzetek gyűjteményével
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107709"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Security Center-adatmennyiségek gazdag, interaktív jelentéseinek létrehozása

[Azure monitor munkafüzetek](../azure-monitor/visualize/workbooks-overview.md) rugalmas vásznon biztosítanak az adatelemzéshez, valamint a gazdag vizuális jelentések létrehozásához a Azure Portalon belül. Lehetővé teszik, hogy több adatforrásra koppintson az Azure-ból, és egyesítse azokat egységes interaktív környezetekben.

A munkafüzetek számos képességet biztosítanak az Azure-beli adatmegjelenítéshez. Az egyes vizualizációk típusaira vonatkozó részletes példákat a [vizualizációk példái és dokumentációja](../azure-monitor/visualize/workbooks-text-visualizations.md)tartalmazza. 

A Azure Security Centeron belül a beépített jelentések segítségével nyomon követheti a szervezet biztonsági állapotát. Az Security Center vagy más támogatott adatforrásokból származó adatok széles körének megjelenítéséhez egyéni jelentéseket is készíthet.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Biztonságos pontszám időbeli jelentés":::

## <a name="availability"></a>Rendelkezésre állás

| Szempont                          | Részletek                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Kiadás állapota:                  | Előnézet<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| Árképzési                        | Ingyenes                                                                                                                                         |
| Szükséges szerepkörök és engedélyek: | A munkafüzetek mentéséhez legalább munkafüzet közreműködői engedélyekkel kell rendelkeznie a cél erőforráscsoporthoz                                      |
| Felhők                         | ![Igen](./media/icons/yes-icon.png) Kereskedelmi felhők<br>![Igen](./media/icons/yes-icon.png) Nemzeti/szuverén (US Gov, kínai gov, other gov) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Munkafüzetek gyűjteménye Azure Security Center

Az integrált Azure-munkafüzetek funkcióinak köszönhetően a Azure Security Center egyszerűen létrehozhatja saját, egyéni, interaktív jelentéseit. A Security Center egy munkafüzet-katalógust is tartalmaz, amely a következő jelentésekkel készen áll a testreszabásra:

- **Biztonsági pontszám az idő múlásával** – nyomon követheti az előfizetések pontszámait és a javaslatok módosításait az erőforrásokra vonatkozóan
- **Rendszerfrissítések** – a hiányzó rendszerfrissítések megtekintése erőforrások, operációs rendszer, súlyosság és egyéb beállítások alapján
- **Sebezhetőségi felmérés eredményei** – megtekintheti az Azure-erőforrások sebezhetőségi vizsgálatának eredményeit

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Beépített munkafüzetek galériája Azure Security Center":::

Válasszon egyet a megadott jelentések közül, vagy hozza létre a sajátját.

> [!TIP]
> A **Szerkesztés** gombbal testreszabhatja a megadott jelentések bármelyikét. Ha végzett a szerkesztéssel, válassza a **Mentés** lehetőséget, és a módosítások egy új munkafüzetbe lesznek mentve.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="A megadott munkafüzetek szerkesztésével testreszabhatja az adott igényeket":::

### <a name="use-the-secure-score-over-time-report"></a>A "biztonságos pontszám az idő múlásával" jelentés használata

Ez a jelentés a Log Analytics munkaterületről származó biztonságos pontszám-adatok használatával működik. Ezeket az adatok exportálását a folyamatos exportálási eszközből kell exportálni, a [folyamatos exportálás konfigurálása a Azure Portal Security Center lapjain](continuous-export.md?tabs=azure-portal)leírtak szerint.

A folyamatos exportálás beállításakor állítsa az Exportálás gyakoriságát a **streaming Updates** és a **Snapshots** értékre.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="A biztonságos pontszámhoz az idő múlásával munkafüzetben mindkét lehetőséget ki kell választania a folyamatos exportálási konfiguráció exportálási gyakorisági beállításai között.":::

> [!NOTE]
> A pillanatképek hetente exportálhatók, ezért meg kell várnia legalább egy hetet az első pillanatkép exportálásához, mielőtt megtekinti az adatok ebben a jelentésben.

> [!TIP]
> A folyamatos exportálás konfigurálásához a szervezeten belül használja a következő témakörben ismertetett Azure Policy "DeployIfNotExist" szabályzatot [: a folyamatos exportálás konfigurálása nagy méretben](continuous-export.md?tabs=azure-policy).

A biztonságos pontszám az idő múlásával jelentés öt gráfot tartalmaz a kiválasztott munkaterületekre jelentett előfizetésekhez:


|Graph  |Példa  |
|---------|---------|
|**Trendek az elmúlt héten és hónapban**<br>Ez a szakasz az előfizetések pontszámának aktuális pontszámának és általános trendjeinek figyelésére használható.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="A beépített jelentés biztonságos pontszámának trendjei":::|
|**Az összes kiválasztott előfizetés összesített pontszáma**<br>Vigye az egérmutatót a trendvonal bármely pontjára, hogy az összesített pontszámot a kiválasztott időtartomány bármely napján megtekintse.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Az összes kiválasztott előfizetés összesített pontszáma":::|
|**A legtöbb nem kifogástalan erőforrással kapcsolatos javaslatok**<br>Ez a táblázat segít a kiválasztott időszakon belül a legtöbb erőforrással megváltoztatott javaslatok osztályozásában.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="A legtöbb nem kifogástalan erőforrással kapcsolatos javaslatok":::|
|**Adott biztonsági vezérlők pontszámai**<br>Security Center biztonsági vezérlői a javaslatok logikai csoportjai. Ez a diagram az összes vezérlő heti pontszámát mutatja be.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="A biztonsági ellenőrzések eredményei a kiválasztott időszakban":::|
|**Erőforrások változásai**<br>Itt jelennek meg a legtöbb olyan erőforrás, amely módosított állapotot (kifogástalan, sérült vagy nem alkalmazható) tartalmaz a kiválasztott időszakban. Válasszon ki egy javaslatot a listából, és nyisson meg egy új táblázatot, amely felsorolja az adott erőforrásokat.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="A legtöbbet módosított állapotú erőforrásokkal kapcsolatos javaslatok":::|

### <a name="use-the-system-updates-report"></a>A System Updates jelentés használata

Ez a jelentés a "rendszerfrissítések telepítése a gépekre" biztonsági javaslaton alapul.

A jelentés segítségével azonosíthatja a kiemelkedő frissítésekkel rendelkező gépeket.

A kiválasztott előfizetések helyzetét a következőknek megfelelően tekintheti meg:

- A függőben lévő frissítésekkel rendelkező erőforrások listája
- Az erőforrásokból hiányzó frissítések listája

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="A Security Center rendszerfrissítési jelentése a hiányzó frissítések biztonsági javaslat alapján":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>A "sebezhetőségi felmérés eredményei" jelentés használata

Security Center tartalmazza a számítógépek, tárolók és SQL-kiszolgálók biztonsági réseit.

További információ a következő képolvasók használatáról:

- [A gépek ellenőrzése az integrált VA Scanner eszközzel](deploy-vulnerability-assessment-vm.md)
- [Biztonsági rések meglétének ellenőrzése a regisztrációs adatbázis rendszerképei kapcsán](defender-for-container-registries-usage.md)
- [Biztonsági rések ellenőrzése az SQL-erőforrásokban](defender-for-sql-on-machines-vulnerability-assessment.md)

Az egyes képolvasók megállapításait külön javaslatok jelentik:

- A virtuális gépek biztonsági réseit szervizelni kell
- A Azure Container Registry lemezképekben található biztonsági réseket szervizelni kell (Qualys-alapú)
- A sebezhetőségi felmérés eredményeit az SQL-adatbázisokban szervizelni kell
- A biztonsági rések felmérésének eredményeit a gépeken lévő SQL-kiszolgálókon szervizelni kell

Ez a jelentés összegyűjti ezeket az eredményeket, és a súlyosság, az erőforrás típusa és a kategória alapján rendezi őket.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Security Center sebezhetőségi felmérés eredményeinek jelentése":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Munkafüzetek importálása más munkafüzet-gyűjteményből

Ha más Azure-szolgáltatásokban is létrehozott munkafüzeteket, és át szeretné helyezni őket a Azure Security Center munkafüzetek gyűjteménybe:

1. Nyissa meg a cél munkafüzetet.

1. Az eszköztáron válassza a **Szerkesztés** lehetőséget.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Azure Monitor munkafüzet szerkesztése":::

1. Az eszköztárból válassza ki **</>** a speciális szerkesztő megadásához.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="A speciális szerkesztő elindítása a katalógus sablon JSON-kódjának lekéréséhez":::

1. Másolja a munkafüzet gyűjteményének JSON-sablonját.

1. Nyissa meg Security Center a munkafüzetek katalógusát, és a menüsávban válassza az **új** lehetőséget.
1. A **</>** gombra kattintva adja meg a speciális szerkesztő.
1. Illessze be a teljes dokumentumtár-sablon JSON-fájlját.
1. Kattintson az **Alkalmaz** gombra.
1. Az eszköztáron válassza a **Mentés másként** lehetőséget.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="A munkafüzet mentése a gyűjteménybe Security Center":::

1. Adja meg a munkafüzet mentéséhez szükséges adatokat:
   1. A munkafüzet neve
   2. A kívánt régió
   3. Előfizetés, erőforráscsoport és megosztás szükség szerint.

A mentett munkafüzetet a **legutóbb módosított munkafüzetek** kategóriában találja.


## <a name="next-steps"></a>Következő lépések

Ez a cikk Security Center integrált Azure Monitor munkafüzetek oldalát ismerteti beépített jelentésekkel, valamint a saját, egyéni, interaktív jelentések készítésének lehetőségét.

- További információ [Azure monitor munkafüzetekről](../azure-monitor/visualize/workbooks-overview.md)
- A beépített jelentések lekérik az adatait Security Center javaslataiból. Tudnivalók a biztonsági javaslatok számos biztonsági javaslatáról [– útmutató](recommendations-reference.md)