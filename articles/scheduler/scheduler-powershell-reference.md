---
title: PowerShell-parancsmagok – dokumentáció
description: Tudnivalók az Azure Scheduler PowerShell-parancsmagjai
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/18/2016
ms.openlocfilehash: ccc9f709348d961e49bced00946658a6997837c0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "92368111"
---
# <a name="powershell-cmdlets-reference-for-azure-scheduler"></a>PowerShell-parancsmagok referenciája az Azure Scheduler szolgáltatáshoz

> [!IMPORTANT]
> [Azure Logic apps](../logic-apps/logic-apps-overview.md) az Azure Scheduler cseréje [folyamatban](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date)van. Ha továbbra is szeretne dolgozni a Feladatütemezőben beállított feladatokkal, akkor a lehető leghamarabb [telepítse át Azure Logic apps](../scheduler/migrate-from-scheduler-to-logic-apps.md) . 
>
> Az ütemező már nem érhető el a Azure Portalban, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagjai](scheduler-powershell-reference.md) jelenleg is elérhetők maradnak, így a feladatok és a feladatok gyűjteményei kezelhetők.

A Scheduler-feladatok és-webhelycsoportok létrehozására és kezelésére szolgáló parancsfájlok létrehozásához PowerShell-parancsmagokat használhat. Ez a cikk az Azure Scheduler fő PowerShell-parancsmagait sorolja fel a hivatkozási cikkeire mutató hivatkozásokkal. Az Azure-előfizetéshez Azure PowerShell telepítéséhez tekintse meg a [Azure PowerShell telepítése és konfigurálása](/powershell/azure/)című témakört. [Azure Resource Manager parancsmagokkal](/powershell/azure/)kapcsolatos további információkért lásd: [Azure PowerShell használata a Azure Resource Manager használatával](../azure-resource-manager/management/manage-resources-powershell.md).

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

| Parancsmag | Leírás |
|--------|-------------|
| [AzSchedulerJobCollection letiltása](/powershell/module/azurerm.scheduler/disable-azurermschedulerjobcollection) |A feladatütemezés letiltása. |
| [Enable-AzureRmSchedulerJobCollection](/powershell/module/azurerm.scheduler/enable-azurermschedulerjobcollection) |Lehetővé teszi a feladatok gyűjtését. |
| [Get-AzSchedulerJob](/powershell/module/azurerm.scheduler/get-azurermschedulerjob) |Ütemező feladatok beolvasása. |
| [Get-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/get-azurermschedulerjobcollection) |Beolvassa a feladatok gyűjteményeit. |
| [Get-AzSchedulerJobHistory](/powershell/module/azurerm.scheduler/get-azurermschedulerjobhistory) |Beolvassa a feladatok előzményeit. |
| [Új – AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/new-azurermschedulerhttpjob) |Létrehoz egy HTTP-feladatot. |
| [Új – AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/new-azurermschedulerjobcollection) |Létrehoz egy feladatot gyűjteményt. |
| [Új – AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebusqueuejob) | Létrehoz egy Service Bus üzenetsor-feladatot. |
| [Új – AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/new-azurermschedulerservicebustopicjob) |Létrehoz egy Service Bus témakör-feladatot. |
| [Új – AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/new-azurermschedulerstoragequeuejob) |Létrehoz egy tárolási várólista-feladatot. |
| [Remove-AzSchedulerJob](/powershell/module/azurerm.scheduler/remove-azurermschedulerjob) |Eltávolít egy Feladatütemező feladatot. |
| [Remove-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/remove-azurermschedulerjobcollection) |Eltávolít egy feladatot. |
| [Set-AzSchedulerHttpJob](/powershell/module/azurerm.scheduler/set-azurermschedulerhttpjob) |Módosítja egy ütemező HTTP-feladatot. |
| [Set-AzSchedulerJobCollection](/powershell/module/azurerm.scheduler/set-azurermschedulerjobcollection) |A feladatütemezés módosítása. |
| [Set-AzSchedulerServiceBusQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebusqueuejob) |Módosítja egy Service Bus üzenetsor-feladatot. |
| [Set-AzSchedulerServiceBusTopicJob](/powershell/module/azurerm.scheduler/set-azurermschedulerservicebustopicjob) |Módosítja egy Service Bus témakör-feladatot. |
| [Set-AzSchedulerStorageQueueJob](/powershell/module/azurerm.scheduler/set-azurermschedulerstoragequeuejob) |Módosít egy tárolási várólista-feladatot. |
||| 

További részletekért futtassa a következő parancsmagok bármelyikét: 

```text
Get-Help <cmdlet name> -Detailed
Get-Help <cmdlet name> -Examples
Get-Help <cmdlet name> -Full
```

## <a name="next-steps"></a>Következő lépések

* [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)