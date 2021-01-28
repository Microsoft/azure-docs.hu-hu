---
title: Riasztási szabályok vagy műveleti szabályok frissítése, ha a célként megadott erőforrás egy másik Azure-régióba kerül át
description: Háttér és útmutatás a riasztási szabályok vagy műveleti szabályok frissítéséhez, ha a célként megadott erőforrás egy másik Azure-régióba kerül át.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 06/26/2020
ms.subservice: alerts
ms.openlocfilehash: 36cf5882913426062e281194b61a8c760141512a
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98944166"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Riasztási szabályok vagy műveleti szabályok frissítése, ha a célként megadott erőforrás egy másik Azure-régióba kerül át

Ez a cikk azt ismerteti, hogy a meglévő [riasztási szabályok](./alerts-overview.md) és [műveleti szabályok](./alerts-action-rules.md) miért befolyásolhatják a más Azure-erőforrások régiók közötti áthelyezését, valamint a problémák azonosítását és megoldását. A főbb [erőforrás-áthelyezési dokumentációban talál](../../azure-resource-manager/management/move-region.md) további információt arról, hogy az erőforrás hogyan mozdul el a régiók között, és hogyan lehet az áthelyezési folyamat megtervezéséhez szükséges ellenőrzőlista.

## <a name="why-the-problem-exists"></a>Miért létezik a probléma?

A riasztási szabályok és a műveleti szabályok más Azure-erőforrásokra hivatkoznak. Ilyenek például az Azure-beli [virtuális gépek](../../site-recovery/azure-to-azure-tutorial-migrate.md), az [Azure SQL](../../azure-sql/database/move-resources-across-regions.md)és az [Azure Storage](../../storage/common/storage-account-move.md). Ha áthelyezi a szabályok által hivatkozott erőforrásokat, a szabályok valószínűleg nem fognak megfelelően működni, mert nem találják az általuk hivatkozott erőforrásokat.

Két fő oka lehet annak, hogy a szabályok miért nem működnek a cél erőforrások áthelyezése után:

- A szabály hatóköre explicit módon hivatkozik a régi erőforrásra.
- A riasztási szabály mérőszámokon alapul.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>A szabály hatóköre explicit módon hivatkozik a régi erőforrásra

Amikor áthelyez egy erőforrást, az erőforrás-azonosítója a legtöbb esetben megváltozik. A rendszer a háttérben replikálja az erőforrást az új régióba, mielőtt törölné a régi régióból. Ehhez a folyamathoz két erőforrás szükséges, így két különböző erőforrás-azonosító is létezik egyszerre egy kis ideig. Mivel az erőforrás-azonosítóknak egyedieknek kell lenniük, új azonosítót kell létrehozni a folyamat során. 

**Hogyan befolyásolja az erőforrás áthelyezése a meglévő szabályokat?**

A riasztási szabályok és a műveleti szabályok olyan erőforrás-hatókörrel rendelkeznek, amelyre vonatkoznak. A hatókör lehet egy teljes előfizetés, egy erőforráscsoport vagy egy vagy több konkrét erőforrás.
Íme például egy olyan szabály, amelynek hatóköre két erőforrással rendelkezik (két virtuális gép):

![Több erőforrás riasztási szabálya](media/alerts-resource-move/multi-resource-alert-rule.png)

Ha a szabály hatóköre explicit módon megemlíti az erőforrást, és az erőforrás áthelyezte és módosította az erőforrás-AZONOSÍTÓját, akkor a szabály helytelen vagy nem létező erőforrást keres, és így sikertelen lesz.

**Hogyan lehet elhárítani a problémát?**

Frissítse vagy hozza létre újra az érintett szabályt, hogy az új erőforrásra mutasson. A hatókör frissítésének folyamata a cikk későbbi részében található.

A probléma a következő szabályok típusaira vonatkozik:

- Tevékenységnapló-riasztási szabályok
- Műveletszabályok
- Klasszikus riasztások
- Metrikai riasztások – további információkért tekintse meg a [metrikák alapján a következő szakasz riasztási szabályait](#alert-rules-based-on-metrics).

> [!NOTE]
> A naplózási keresési riasztási szabályokat és az intelligens detektor riasztási szabályait a rendszer nem érinti, mert a hatókörük munkaterület vagy Application Insights. Ezen hatókörök egyike sem támogatja a régió áthelyezését.

## <a name="alert-rules-based-on-metrics"></a>Riasztási szabályok mérőszámok alapján

A mérőszámok, amelyeket az Azure-erőforrások bocsátanak ki, regionálisak. Amikor egy erőforrás új régióba kerül, megkezdi a metrikák kibocsátását az új régióban. Ennek eredményeképpen minden mérőszámon alapuló riasztási szabályt frissíteni kell, vagy újból létre kell hozni, hogy az aktuális metrikai adatfolyamra mutassanak a megfelelő régióban.

Ez a magyarázat a [metrikus riasztási szabályokra](alerts-metric-overview.md) és a [rendelkezésre állási tesztekre vonatkozó riasztási szabályokra](../app/monitor-web-app-availability.md)is vonatkozik.

Ha a hatókör **összes** erőforrása át lett helyezve, nem kell újból létrehoznia a szabályt. Egyszerűen frissítheti a riasztási szabály bármely mezőjét, például a riasztási szabály leírását, és mentheti.
Ha a hatókörben lévő erőforrások **közül csak néhány** lett áthelyezve, el kell távolítania az áthelyezett erőforrásokat a meglévő szabályból, és létre kell hoznia egy új szabályt, amely csak az áthelyezett erőforrásokra vonatkozik.

## <a name="procedures-to-fix-problems"></a>Problémák elhárításának eljárásai

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Az áthelyezett erőforrásokhoz társított szabályok azonosítása a Azure Portal

- **Riasztási szabályok esetén** – navigáljon a riasztások > a riasztási szabályok kezelése > szűrés a tartalmazó előfizetés és az áthelyezett erőforrás alapján.
> [!NOTE]
> A műveletnapló riasztási szabályai nem támogatják ezt a folyamatot. A műveletnapló riasztási szabályának hatókörét nem lehet frissíteni, és egy másik előfizetésben lévő erőforrásra mutat. Ehelyett létrehozhat egy új szabályt, amely a régit váltja fel.

- **A műveleti szabályok esetében** – navigáljon a riasztások > a műveletek kezelése > műveleti szabályok (előzetes verzió) > a szűrést a tartalmazó előfizetés és az áthelyezett erőforrás alapján.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Szabály hatókörének módosítása a Azure Portal

1. A gombra kattintva nyissa meg az előző lépésben azonosított szabályt.
2. Az **erőforrás** területen kattintson a **Szerkesztés** elemre, és módosítsa a hatókört igény szerint.
3. Szükség szerint módosítsa a szabály egyéb tulajdonságait.
4. Kattintson a **Mentés** gombra.

![Riasztási szabály hatókörének módosítása](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Szabály hatókörének módosítása Azure Resource Manager sablonok használatával

1. Szerezze be a szabály Azure Resource Manager sablonját.  Szabály sablonjának exportálása a Azure Portalből:
   1. Navigáljon az erőforráscsoportok szakaszhoz a portálon, és nyissa meg a szabályt tartalmazó erőforráscsoportot.
   2. Az Áttekintés szakaszban jelölje be a **rejtett típus megjelenítése** jelölőnégyzetet, majd a megfelelő típusú szabály szerint szűrje a szűrőt.
   3. A részletek megtekintéséhez válassza ki a megfelelő szabályt.
   4. A **Beállítások** területen válassza a **sablon exportálása** lehetőséget.
2. Módosítsa a sablont. Szükség esetén két szabályra osztható (amelyek a fentiekben leírtak szerint a metrikus riasztások bizonyos eseteire vonatkoznak).
3. Telepítse újra a sablont.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Szabály hatókörének módosítása REST API használatával

1. Meglévő szabály beszerzése ([metrikus riasztások](/rest/api/monitor/metricalerts/get), [műveletnapló riasztásai](/rest/api/monitor/activitylogalerts/get))
2. Hatókör módosítása ([műveletnapló riasztásai](/rest/api/monitor/activitylogalerts/update))
3. A szabály újbóli üzembe helyezése ([metrikus riasztások](/rest/api/monitor/metricalerts/createorupdate), [műveletnapló riasztásai](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Szabály hatókörének módosítása a PowerShell használatával

1. A meglévő szabály beszerzése ([metrikai riasztások](/powershell/module/az.monitor/get-azmetricalertrulev2), [műveletnapló-riasztások](/powershell/module/az.monitor/get-azactivitylogalert), [műveleti szabályok](/powershell/module/az.alertsmanagement/get-azactionrule)).
2. Módosítsa a hatókört. Szükség esetén két szabályra osztható (amelyek a fentiekben leírtak szerint a metrikus riasztások bizonyos eseteire vonatkoznak).
3. A szabály újbóli üzembe helyezése ([metrikai riasztások](/powershell/module/az.monitor/add-azmetricalertrulev2), [műveletnapló-riasztások](/powershell/module/az.monitor/enable-azactivitylogalert), [műveleti szabályok](/powershell/module/az.alertsmanagement/set-azactionrule)).

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Szabály hatókörének módosítása az Azure CLI használatával

1.  A meglévő szabály beszerzése ([metrikus riasztások](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show), [műveletnapló riasztásai](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)).
2.  A szabály hatókörének frissítése közvetlenül ([metrikus riasztások](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update), [műveletnapló riasztásai](/cli/azure/monitor/activity-log/alert/scope))
3.  Szükség esetén két szabályra osztható (amelyek a fentiekben leírtak szerint a metrikus riasztások bizonyos eseteire vonatkoznak).

## <a name="next-steps"></a>Következő lépések

További információ a [Riasztási értesítések](alerts-troubleshoot.md), a [metrikus riasztások](alerts-troubleshoot-metric.md)és a [naplózási riasztások](alerts-troubleshoot-log.md)egyéb problémáinak kijavításáról. 
