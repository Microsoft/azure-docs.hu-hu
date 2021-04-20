---
title: Riasztási szabályok vagy műveletszabályok frissítése, ha a célerőforrásuk egy másik Azure-régióba kerül
description: Háttérinformációk és utasítások a riasztási szabályok vagy a műveletszabályok frissítéséhez, ha a célerőforrásuk egy másik Azure-régióba kerül.
author: harelbr
ms.author: harelbr
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 02/14/2021
ms.openlocfilehash: 727196f274db3abae75a38d3ecdf31a78dec0fab
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725943"
---
# <a name="how-to-update-alert-rules-or-action-rules-when-their-target-resource-moves-to-a-different-azure-region"></a>Riasztási szabályok vagy műveletszabályok frissítése, ha a célerőforrásuk egy másik Azure-régióba kerül

Ez a cikk [](./alerts-overview.md) ismerteti, [](./alerts-action-rules.md) hogy miért lehetnek hatással a meglévő riasztási szabályokra és műveletszabályokra más Azure-erőforrások régiók közötti áthelyezésekor, és hogy hogyan azonosíthatók és oldhatóak meg ezek a problémák. Tekintse meg az erőforrások [áthelyezésének](../../azure-resource-manager/management/move-resources-overview.md) fő dokumentációját, ahol további információt talál arról, hogy mikor hasznos az erőforrások áthelyezése a régiók között, valamint az áthelyezési folyamat tervezésének ellenőrzőlistája.

## <a name="why-the-problem-exists"></a>A probléma oka

A riasztási szabályok és a műveletszabályok más Azure-erőforrásokra hivatkoznak. Ilyenek például az [Azure-beli virtuális gépek, a](../../site-recovery/azure-to-azure-tutorial-migrate.md) [Azure SQL](../../azure-sql/database/move-resources-across-regions.md)és az [Azure Storage.](../../storage/common/storage-account-move.md) Amikor áthelyezi azokat az erőforrásokat, amelyekre a szabályok hivatkoznak, a szabályok valószínűleg nem fognak megfelelően működni, mert nem találják a hivatkozott erőforrásokat.

Két fő oka lehet annak, hogy a szabályok nem működnek tovább a célerőforrások áthelyezését követően:

- A szabály hatóköre kifejezetten a régi erőforrásra hivatkozik.
- A riasztási szabály metrikákon alapul.

## <a name="rule-scope-explicitly-refers-to-the-old-resource"></a>A szabály hatóköre explicit módon a régi erőforrásra hivatkozik

Amikor áthelyez egy erőforrást, annak erőforrás-azonosítója a legtöbb esetben megváltozik. A színfalak mögött a rendszer replikálja az erőforrást az új régióba, mielőtt törölve lenne a régi régióból. Ehhez a folyamathoz két erőforrásnak és így két különböző erőforrás-nak kell egyidejűleg léteznie egy rövid ideig. Mivel az erőforrás-azonosítóknak egyedinek kell lennie, a folyamat során új azonosítót kell létrehozni. 

**Hogyan befolyásolja az erőforrás áthelyezése a meglévő szabályokat?**

A riasztási szabályok és a műveletszabályok hatóköre kiterjed azokra az erőforrásokra, amelyekre vonatkoznak. A hatókör lehet egy teljes előfizetés, egy erőforráscsoport vagy egy vagy több meghatározott erőforrás.
Itt például egy olyan szabály van, amely egy hatókörrel rendelkezik két erőforrással (két virtuális gép):

![Több erőforrásra vonatkozó riasztási szabály](media/alerts-resource-move/multi-resource-alert-rule.png)

Ha a szabályhatókör kifejezetten megemlít egy erőforrást, és az erőforrás át lett helyezték és módosították az erőforrás-azonosítóját, akkor a szabály nem megfelelő vagy nem létező erőforrást keres, így sikertelen lesz.

**Hogyan oldható meg a probléma?**

Frissítse vagy hozza létre újra az érintett szabályt, hogy az új erőforrásra mutasson. A hatókör frissítésének folyamata a cikk későbbi része.

A probléma a következő szabálytípusokra vonatkozik:

- Tevékenységnapló-riasztási szabályok
- Műveletszabályok
- Metrikákra vonatkozó riasztások – További információért tekintse meg a metrikákon alapuló [riasztási szabályok című következő szakaszt.](#alert-rules-based-on-metrics)

> [!NOTE]
> A naplókeresési riasztási szabályok és az intelligens érzékelő riasztási szabályai nem érintettek, mert a hatókörük munkaterület vagy Application Insights. Jelenleg egyik hatókör sem támogatja a régiók mozgatát.

## <a name="alert-rules-based-on-metrics"></a>Metrikákon alapuló riasztási szabályok

Az Azure-erőforrások által kibocsátott metrikák regionálisak. Amikor egy erőforrás új régióba kerül, elkezdi kibocsátni a metrikákat az új régióban. Ennek eredményeképpen a metrikákon alapuló riasztási szabályokat frissíteni vagy újra létre kell hozni, hogy a megfelelő régióban lévő aktuális metrikastreamre mutasson.

Ez a magyarázat a [metrikariasztás és](alerts-metric-overview.md) a rendelkezésre állási teszt riasztási [szabályaira is vonatkozik.](../app/monitor-web-app-availability.md)

Ha **a** hatókörben minden erőforrást áthelyeztek, nem kell újra létrehoznia a szabályt. Egyszerűen frissítheti a riasztási szabály bármely mezőjét, például a riasztási szabály leírását, és mentheti.
Ha **a hatókörben** lévő erőforrásoknak csak egy része lett áthelyezve, el kell távolítania az áthelyezve lévő erőforrásokat a meglévő szabályból, és létre kell hoznia egy új szabályt, amely csak az áthelyezott erőforrásokra vonatkozik.

## <a name="procedures-to-fix-problems"></a>A problémák megoldására vonatkozó eljárások

### <a name="identifying-rules-associated-with-a-moved-resource-from-the-azure-portal"></a>Az erőforrásból áthelyezott erőforráshoz társított szabályok Azure Portal

- **Riasztási szabályok esetén** – Lépjen a Riasztások > riasztási szabályok kezelése > az azt tartalmazó előfizetés és az áthelyezott erőforrás alapján szűrhet.
> [!NOTE]
> A tevékenységnapló-riasztási szabályok nem támogatják ezt a folyamatot. A tevékenységnapló-riasztási szabály hatóköre nem frissítható, és nem mutatatható egy másik előfizetés egyik erőforrására. Ehelyett létrehozhat egy új szabályt, amely lecseréli a régit.

- **Műveletszabályok esetén** – Lépjen a Riasztások lapra> Műveletek kezelése > (előzetes verzió) lehetőséget, > az azt tartalmazó előfizetésre és az áthelyezett erőforrásra szűrhet.

### <a name="change-scope-of-a-rule-from-the-azure-portal"></a>Szabály hatókörének módosítása a Azure Portal

1. Kattintson rá az előző lépésben azonosított szabály megnyitásához.
2. Az **Erőforrás alatt** kattintson a Szerkesztés **elemre,** és szükség szerint módosítsa a hatókört.
3. Szükség szerint módosítsa a szabály egyéb tulajdonságait.
4. Kattintson a **Mentés** gombra.

![Riasztási szabály hatókörének módosítása](media/alerts-resource-move/change-alert-rule-scope.png)

### <a name="change-the-scope-of-a-rule-using-azure-resource-manager-templates"></a>Szabály hatókörének módosítása sablonokkal Azure Resource Manager használatával

1. Szerezze Azure Resource Manager szabály sablonját.   Szabály sablonjának exportálása a Azure Portal:
   1. A portálon lépjen az Erőforráscsoportok szakaszra, és nyissa meg a szabályt tartalmazó erőforráscsoportot.
   2. Az Áttekintés szakaszban jelölje be a Rejtett típus **megjelenítése** jelölőnégyzetet, és szűrje a szabályt a megfelelő típus szerint.
   3. A részletek megtekintéséhez válassza ki a megfelelő szabályt.
   4. A **Beállítások alatt** válassza a Sablon **exportálása lehetőséget.**
2. Módosítsa a sablont. Szükség esetén ossza fel két szabályra (ez a metrikákra vonatkozó riasztások egyes esetekben releváns, ahogy azt fent említettük).
3. A sablon ismételt üzembe mentése.

### <a name="change-scope-of-a-rule-using-rest-api"></a>Szabály hatókörének módosítása a REST API

1. A meglévő szabály ([metrikariasztás,](/rest/api/monitor/metricalerts/get) [tevékenységnapló-riasztások ) lekérte](/rest/api/monitor/activitylogalerts/get)
2. A hatókör módosítása ([tevékenységnapló-riasztások](/rest/api/monitor/activitylogalerts/update))
3. A szabály ismételt üzembeése ([metrikariasztás,](/rest/api/monitor/metricalerts/createorupdate) [tevékenységnapló-riasztások](/rest/api/monitor/activitylogalerts/createorupdate))

### <a name="change-scope-of-a-rule-using-powershell"></a>Szabály hatókörének módosítása a PowerShell használatával

1. A meglévő szabály[(metrikariasztás,](/powershell/module/az.monitor/get-azmetricalertrulev2) [tevékenységnapló-riasztások,](/powershell/module/az.monitor/get-azactivitylogalert) [műveletszabályok) lekérte.](/powershell/module/az.alertsmanagement/get-azactionrule)
2. Módosítsa a hatókört. Szükség esetén ossza fel őket két szabályra (amelyek a metrikákra vonatkozó riasztások egyes esetekben relevánsak, a fent említettek szerint).
3. A szabály ([metrikariasztás,](/powershell/module/az.monitor/add-azmetricalertrulev2) [tevékenységnapló-riasztások,](/powershell/module/az.monitor/enable-azactivitylogalert) [műveletszabályok ) újratelepítése.](/powershell/module/az.alertsmanagement/set-azactionrule)

### <a name="change-the-scope-of-a-rule-using-azure-cli"></a>Szabály hatókörének módosítása az Azure CLI használatával

1.  A meglévő szabály[(metrikariasztás,](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-show) [tevékenységnapló-riasztások) lekérte.](/cli/azure/monitor/activity-log/alert#az-monitor-activity-log-alert-list)
2.  A szabály hatókörének közvetlen frissítése ([metrikariasztás,](/cli/azure/monitor/metrics/alert#az-monitor-metrics-alert-update) [tevékenységnapló-riasztások](/cli/azure/monitor/activity-log/alert/scope))
3.  Szükség esetén ossza fel őket két szabályra (amelyek a metrikákra vonatkozó riasztások egyes esetekben relevánsak, a fent említettek szerint).

## <a name="next-steps"></a>Következő lépések

További információ a [](alerts-troubleshoot.md)riasztási értesítésekkel, a [metrikák](alerts-troubleshoot-metric.md)riasztásával és a naplóriasztásokkal kapcsolatos egyéb problémák [megoldásával kapcsolatban.](alerts-troubleshoot-log.md)