---
title: Resource Manager sql-elemzésekhez használható sablonminták
description: Minta Azure Resource Manager SQL-elemzések üzembe helyezéséhez és konfigurálásához.
ms.topic: sample
author: bwren
ms.author: bwren
ms.date: 03/25/2021
ms.openlocfilehash: dafb7335ef211cb9173ec2fb4565243d603d35fe
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107730524"
---
# <a name="resource-manager-template-samples-for-sql-insights"></a>Resource Manager sql-elemzésekhez használható sablonminták
Ez a cikk [mintasablonokat Azure Resource Manager sql-elemzések](../../azure-resource-manager/templates/template-syntax.md) engedélyezéséhez az Azure-ban futó SQL monitorozásához.  Az [ÁLTALunk](sql-insights-overview.md) támogatott SQL-ajánlatokról és -verziókról az SQL-elemzések dokumentációjában talál további információt. Minden minta tartalmaz egy sablonfájlt és egy paraméterfájlt, amelyek mintaértékeket tartalmaznak, és meg kell adni a sablonnak.

[!INCLUDE [azure-monitor-samples](../../../includes/azure-monitor-resource-manager-samples.md)]


## <a name="create-a-sql-insights-monitoring-profile"></a>SQL Insights monitorozási profil létrehozása
A következő minta létrehoz egy SQL-elemzések figyelési profilját, amely tartalmazza a gyűjthető SQL-monitorozási adatokat, az adatgyűjtés gyakoriságát, és megadja a munkaterületet, amelyre a rendszer az adatokat küldi.


### <a name="template-file"></a>Sablonfájl

Tekintse meg [a sablonfájlt a Git Hubon.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.armtemplate)

### <a name="parameter-file"></a>Paraméterfájl

Tekintse meg [a paraméterfájlt a git hubon.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Create%20new%20profile/CreateNewProfile.parameters.json)


## <a name="add-a-monitoring-vm-to-a-sql-insights-monitoring-profile"></a>Monitorozási virtuális gép hozzáadása SQL Insights monitorozási profilhoz
Miután létrehozott egy figyelési profilt, olyan Azure-beli virtuális gépeket kell lefoglalnia, amelyek úgy lesznek konfigurálva, hogy távolról gyűjtsenek adatokat az adott virtuális gép konfigurációjában megadott SQL-erőforrásokból.  További részletekért tekintse meg az SQL-elemzések engedélyezése című dokumentációt.

A következő minta egy monitorozási virtuális gépet konfigurál a megadott SQL-erőforrások adatainak gyűjtésére.


### <a name="template-file"></a>Sablonfájl

Tekintse meg [a sablonfájlt a Git Hubon.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.armtemplate)

### <a name="parameter-file"></a>Paraméterfájl

Tekintse meg [a paraméterfájlt a git hubon.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/SQL/Add%20monitoring%20virtual%20machine/AddMonitoringVirtualMachine.parameters.json)


## <a name="create-an-alert-rule-for-sql-insights"></a>Riasztási szabály létrehozása SQL-elemzésekhez
A következő minta létrehoz egy riasztási szabályt, amely lefedi a megadott figyelési profil hatókörében található SQL-erőforrásokat.  Ez a riasztási szabály megjelenik az SQL Insights felhasználói felületén a riasztások felhasználói felületének helyi panelén.  

A paraméterfájl az SQL-elemzésekben megadott riasztássablonok egyikének értékeit tartalmazza. Módosíthatja, hogy riasztást adjon az SQL-hez gyűjtött egyéb adatokról.  A sablon nem határoz meg műveletcsoportot a riasztási szabályhoz.


#### <a name="template-file"></a>Sablonfájl

Tekintse meg [a sablonfájlt a git hubon.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/log-metric-noag.armtemplate)

### <a name="parameter-file"></a>Paraméterfájl

Tekintse meg [a paraméterfájlt a git hubon.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Workbooks/Workloads/Alerts/sql-cpu-utilization-percent.parameters.json)





## <a name="next-steps"></a>Következő lépések

* [Szerezze be a további mintasablonokat a Azure Monitor.](../resource-manager-samples.md)
* [További információ az SQL-elemzésekről.](sql-insights-overview.md)
