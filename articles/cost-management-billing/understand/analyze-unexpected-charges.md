---
title: Váratlan Azure-költségek elemzése
description: Tudja meg, hogyan elemezheti az Azure-előfizetése kapcsán felmerülő váratlan költségeket.
author: bandersmsft
ms.reviewer: adwise
ms.service: cost-management-billing
ms.subservice: cost-management
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: banders
ms.custom: contperf-fy21q1
ms.openlocfilehash: 6d5198f47b6089730ff345eb12ab522fc5ce58a1
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100575387"
---
# <a name="analyze-unexpected-charges"></a>Váratlan költségek elemzése

A szervezete számára készített felhőalapú erőforrás-infrastruktúra valószínűleg összetett. Számos Azure-erőforrástípus különböző típusú költségekkel járhat. Előfordulhat, hogy az Azure-erőforrásokat a szervezet különböző csoportjai birtokolják, amelyek különböző erőforrásokra vonatkozó eltérő számlázási modelltípusokkal rendelkeznek. A költségek jobb megértése érdekében kezdje az elemzést a következő szakaszokban lévő stratégiákkal.

## <a name="review-invoice-for-resource-responsible-for-charge"></a>A díjért felelős erőforrás számlájának áttekintése

Az Azure-szolgáltatások megvásárlásának módja segít meghatározni az Ön számára elérhető módszertant és eszközöket a költséggel társított erőforrás azonosítása során. Annak megállapításához, hogy melyik módszertan vonatkozik Önre, először [határozza meg az Azure-ajánlat típusát](../costs/understand-cost-mgt-data.md#determine-your-offer-type). Ezután azonosítsa az ügyfélkategóriát a [támogatott Azure-ajánlatok](../costs/understand-cost-mgt-data.md#supported-microsoft-azure-offers) listájában.

A következő cikkek részletesen ismertetik, hogyan tekintheti át a számláját az ügyfél típusa alapján. Mindegyik cikkben útmutatást talál arról, hogyan tölthető le egy adott számlázási időszak használati és költségadatait tartalmazó CSV-fájl.

- [Használatalapú számla áttekintési folyamata](review-individual-bill.md#charges)
- [Nagyvállalati szerződéses számla áttekintési folyamata](review-enterprise-agreement-bill.md)
- [Microsoft-ügyfélszerződés áttekintési folyamata](review-customer-agreement-bill.md#analyze-your-azure-usage-charges)
- [Microsoft-partnerszerződés áttekintési folyamata](review-partner-agreement-bill.md#analyze-your-azure-usage-charges)

Az Azure-számla _mérőeszközök_ alapján összesíti a havi költségeket. A mérőeszközökkel az erőforrás kihasználtságát követjük nyomon időben, és ezekkel számítható ki a számla. Ha egyetlen Azure-erőforrást (például virtuális gépet) hoz létre, akkor egy vagy több mérőeszközpéldány jön létre az erőforráshoz.

A használati adatokat tartalmazó CSV-fájlt az elemezni kívánt számlán található _MeterName_ alapján szűrheti a mérőeszközre vonatkozó összes sorelem megtekintéséhez. A sorelemhez tartozó _InstanceID_ a díjat okozó tényleges Azure-erőforrásnak felel meg.

Ha azonosította a szóban forgó erőforrást, az Azure Cost Management költségelemzési eszközével tovább elemezheti az erőforrással kapcsolatos költségeket. A költségelemzéssel kapcsolatos további tudnivalókért lásd: [Költségelemzés indítása](../costs/quick-acm-cost-analysis.md).

## <a name="review-invoiced-charges-in-cost-analysis"></a>A számlázott díjtételek áttekintése a Költségelemzésben

A számlaadatok Azure Portalon való megtekintéséhez keresse meg a költségelemzést ahhoz a hatókörhöz, amely az elemezni kívánt számlához kapcsolódik. Válassza a **Számla részletei** nézetet. A száma részletei úgy jelenítik meg a díjtételeket, ahogy a számlán szerepelnek.

[![A számla részleteit bemutató példa](./media/analyze-unexpected-charges/invoice-details.png)](./media/analyze-unexpected-charges/invoice-details.png#lightbox)

A számlázási adatok megtekintésével azonosíthatja, hogy melyik szolgáltatás okozott váratlan költségeket, és megállapíthatja, hogy melyik erőforrások kapcsolódnak közvetlenül ehhez az erőforráshoz a Költségelemzésben. Ha például engedélyezni kívánja a Virtual Machines szolgáltatás díjait, navigáljon a **Halmozott költség** nézetre. A részletességet ezután állítsa **Napi** értékre, a díjakat szűréséhez adja meg a **Szolgáltatásnév: Virtual Machines** értéket, majd csoportosítsa a díjakat **Erőforrás** szerint.

[![A virtuális gépek halmozott költségeit bemutató példa](./media/analyze-unexpected-charges/virtual-machines.png)](./media/analyze-unexpected-charges/virtual-machines.png#lightbox)

## <a name="identify-spikes-in-cost-over-time"></a>Kiugró költségek azonosítása időben

Előfordulhat, hogy néha nem tudja, milyen költségek eredményezték a számlázott költségek változásait. A költségelemzéssel [láthatja a költségek napi és havi bontását](../costs/cost-analysis-common-uses.md#view-costs-per-day-or-by-month), így megértheti, hogy mi változott. A nézet létrehozása után csoportosíthatja a költségeket **szolgáltatás** vagy **erőforrás** alapján is a változások azonosításához. **Vonaldiagramra** is változtathatja a nézetet, hogy jobban láthassa az adatokat.

![A költségelemzésben a költségeket időben megjelenítő példa](./media/analyze-unexpected-charges/costs-over-time.png)

## <a name="determine-resource-pricing-and-billing-model"></a>Az erőforrás-díjszabás és a számlázási modell meghatározása

Egyetlen erőforrás több Azure-termék és -szolgáltatás díját is maga után vonhatja. [Azure-díjszabás termék alapján](https://azure.microsoft.com/pricing/#product-pricing) oldalon tudhat meg többet az egyes Azure-szolgáltatások díjszabásáról. Az Azure-ban létrehozott egyetlen virtuális gép használatának nyomon követéséhez például a következő mérőeszközök hozhatók létre. Előfordulhat, hogy mindegyiknek külön díjszabása van.

- Üzemóra
- IP-cím-szolgáltatásórák
- Bejövő adatforgalom
- Kimenő adatforgalom
- Standard szintű felügyelt lemez
- Standard felügyelt lemez műveletei
- Standard IO-lemez
- Standard IO – Blokkblobolvasási műveletek
- Standard IO – Blokkblobírási műveletek
- Szabványos IO – blokkblob-törlés

A virtuális gép létrehozásakor minden mérőeszköz elkezd használati adatokat szolgáltatni. Ezt a használatot és a mérőeszköz díjszabását az Azure-beli mérési rendszerben követjük nyomon. A számla összegének kiszámításához használt mérőeszközök a használati adatok CSV-fájljában láthatók.

## <a name="find-people-responsible-for-the-resource-and-engage"></a>Az erőforrásért felelős személyek megkeresése

Gyakran előfordul, hogy az adott erőforrásért felelős csapat tudatában van az adott erőforrással kapcsolatos változásoknak. A velük való kapcsolattartás hasznos lehet, mivel megállapíthatja, miért jelentkeznek díjak. Előfordulhat például, hogy a tulajdonos csapat nemrég hozta létre az erőforrást, frissítette a termékváltozatot (ami megváltoztatja az erőforrás díjszabását), vagy megnövelte az erőforrás terhelését a kód módosítása miatt. Az erőforrás tulajdonosának meghatározásához a következő részekben talál további technikákat.

### <a name="analyze-the-audit-logs-for-the-resource"></a>Az erőforrás auditnaplóinak elemzése

Amennyiben jogosult az erőforrás megtekintésére, rendelkeznie kell hozzáféréssel az auditnaplókhoz. Tekintse át a naplókat annak érdekében, hogy megtalálja, melyik felhasználó felelős az erőforrás legfrissebb módosításaiért. További információt az [Azure-tevékenységnapló eseményeinek megtekintését és lekérését](../../azure-monitor/essentials/activity-log.md#view-the-activity-log) ismertető szakaszban talál.

### <a name="analyze-user-permissions-to-the-resources-parent-scope"></a>A felhasználói engedélyek elemzése az erőforrás fölérendelt hatókörében

Az előfizetéshez vagy erőforráscsoporthoz írási hozzáféréssel rendelkező személyek általában birtokában vannak a létrehozott erőforrásokkal kapcsolatos információnak. El kell tudniuk magyarázni egy erőforrás célját, vagy tudniuk kell, hogy ki az, aki erre választ tud adni. Az előfizetési hatókör engedélyeivel rendelkező személyek azonosításához tekintse meg a [felhasználók Azure-erőforrásokhoz való hozzáférésének ellenőrzését](../../role-based-access-control/check-access.md) ismertető szakaszt. Hasonló módszert alkalmazhat erőforráscsoportok esetében.

## <a name="get-help-to-identify-charges"></a>Segítség kérése a díjak azonosításához

Ha már használta az előző stratégiákat, és még mindig nem érti, miért léptek fel költségek, vagy ha további segítségre van szüksége a számlázási problémákkal kapcsolatban, kérjük, [hozzon létre egy támogatási kérést](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>További lépések

- További információ: [A felhővel kapcsolatos befektetés optimalizálása az Azure Cost Management használatával](../costs/cost-mgt-best-practices.md).