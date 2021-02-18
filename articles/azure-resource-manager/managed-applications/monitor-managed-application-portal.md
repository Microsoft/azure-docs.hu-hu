---
title: Felügyelt alkalmazás figyelése Azure Portal használatával
description: Bemutatja, hogyan használható a Azure Portal a felügyelt alkalmazások rendelkezésre állásának és riasztásának figyelésére.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: a02062edd1a940bcc6588ab53457e0af91fedd9a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578273"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Felügyelt alkalmazás telepített példányának figyelése

Miután üzembe helyezett egy felügyelt alkalmazást az Azure-előfizetésében, érdemes ellenőriznie az alkalmazás állapotát. Ez a cikk a Azure Portal beállításait mutatja be az állapot ellenőrzéséhez. A felügyelt alkalmazásban található erőforrások rendelkezésre állását is figyelheti. Beállíthatja és megtekintheti a riasztásokat is.

## <a name="view-resource-health"></a>Erőforrás állapotának megtekintése

1. Válassza ki a felügyelt alkalmazás példányát.

   ![Felügyelt alkalmazás kiválasztása](./media/monitor-managed-application-portal/select-managed-application.png)

1. Válassza az **Erőforrás-állapot** lehetőséget.

   ![Erőforrás állapotának kiválasztása](./media/monitor-managed-application-portal/select-resource-health.png)

1. Megtekintheti a felügyelt alkalmazás erőforrásainak rendelkezésre állását.

   ![Erőforrás állapotának megtekintése](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Riasztások megtekintése

1. Válassza a **riasztások** lehetőséget.

   ![Riasztások kiválasztása](./media/monitor-managed-application-portal/select-alerts.png)

1. Ha a riasztási szabályok konfigurálva vannak, a kiváltott riasztásokra vonatkozó információk láthatók.

   ![Riasztások megtekintése](./media/monitor-managed-application-portal/view-alerts.png)

1. Riasztási szabályok hozzáadásához válassza az **+ új riasztási szabály** lehetőséget.

   ![Riasztás létrehozása](./media/monitor-managed-application-portal/create-new-alert.png)

Riasztásokat hozhat létre a felügyelt alkalmazás-példányhoz vagy a felügyelt alkalmazás erőforrásaihoz. A riasztások létrehozásával kapcsolatos információkért lásd: [a Microsoft Azure riasztások áttekintése](../../azure-monitor/alerts/alerts-overview.md).

## <a name="next-steps"></a>Következő lépések

* A felügyelt alkalmazások példáit lásd: [Sample projects for Azure Managed Applications](sample-projects.md).
* Felügyelt alkalmazás központi telepítéséhez lásd: a [Service Catalog alkalmazás központi telepítése Azure Portal használatával](deploy-service-catalog-quickstart.md).