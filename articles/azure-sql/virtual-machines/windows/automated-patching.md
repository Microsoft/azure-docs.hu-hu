---
title: Automatikus javítás SQL Server virtuális gépekhez (Resource Manager) | Microsoft Docs
description: Ez a cikk az Azure-on futó SQL Server virtuális gépek automatizált javítási funkcióját ismerteti a Resource Manager használatával.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-resource-manager
ms.assetid: 58232e92-318f-456b-8f0a-2201a541e08d
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/07/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 429fe39f84a54c22fa97178b85f417d76dc84a8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359472"
---
# <a name="automated-patching-for-sql-server-on-azure-virtual-machines-resource-manager"></a>Az Azure Virtual Machines szolgáltatásban SQL Server automatikus javítása (Resource Manager)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Az automatikus javítás karbantartási időszakot hoz létre egy SQL Server rendszert futtató Azure-beli virtuális géphez. Az automatikus frissítéseket csak ebben a karbantartási időszakban lehet telepíteni. Az SQL Server esetén ez a korlátozás gondoskodik róla, hogy a rendszerfrissítésekre és az esetleges újraindításokra az adatbázis számára a lehető legkedvezőbb időpontban kerüljön sor. 

> [!IMPORTANT]
> A rendszer csak a **fontos** vagy **kritikus fontosságúként** megjelölt Windows-és SQL Server-frissítéseket telepíti. A más SQL Server frissítéseket, például a szervizcsomagokat és a nem **fontosként** vagy **kritikusként** megjelölt összesítő frissítéseket manuálisan kell telepíteni. 

Az automatikus javítás a [SQL Server infrastruktúra-kezelő (IaaS) ügynök bővítménytől](sql-server-iaas-agent-extension-automate-management.md)függ.

## <a name="prerequisites"></a>Előfeltételek
Az automatikus javítás használatához vegye figyelembe a következő előfeltételeket:

**Operációs rendszer**:

* Windows Server 2008 R2
* Windows Server 2012
* Windows Server 2012 R2
* Windows Server 2016
* Windows Server 2019

**SQL Server verziója**:

* SQL Server 2008 R2
* SQL Server 2012
* SQL Server 2014
* SQL Server 2016
* SQL Server 2017
* SQL Server 2019

**Azure PowerShell**:

* [Telepítse a legújabb Azure PowerShell-parancsokat](/powershell/azure/) , ha automatikus javítást szeretne konfigurálni a PowerShell-lel.

[!INCLUDE [updated-for-az.md](../../../../includes/updated-for-az.md)]

> [!NOTE]
> Az automatikus javítás a SQL Server IaaS-ügynök bővítményére támaszkodik. Az aktuális SQL-virtuálisgép-katalógus lemezképei alapértelmezés szerint a bővítményt adja hozzá. További információ: [SQL Server IaaS-ügynök bővítmény](sql-server-iaas-agent-extension-automate-management.md).
> 
> 

## <a name="settings"></a>Beállítások
Az alábbi táblázat az automatikus javításhoz konfigurálható beállításokat ismerteti. A tényleges konfigurációs lépések attól függően változnak, hogy az Azure Portal vagy az Azure Windows PowerShell-parancsokat használja-e.

| Beállítás | Lehetséges értékek | Leírás |
| --- | --- | --- |
| **Automatikus javítás** |Engedélyezés/letiltás (letiltva) |Engedélyezheti vagy letilthatja az Azure-beli virtuális gépek automatikus javítását. |
| **Karbantartási ütemezés** |Mindennap, hétfő, kedd, szerda, csütörtök, péntek, szombat, vasárnap |A virtuális géphez tartozó Windows-, SQL Server-és Microsoft-frissítések letöltésének és telepítésének ütemterve. |
| **Karbantartási kezdési óra** |0-24 |A virtuális gép frissítésének helyi indítási ideje. |
| **Karbantartási időszak időtartama** |30-180 |A frissítések letöltésének és telepítésének befejezéséhez szükséges percek száma. |
| **Javítás kategóriája** |Fontos | A letölteni és telepíteni kívánt Windows-frissítések kategóriája.|

## <a name="configure-in-the-azure-portal"></a>Konfigurálás a Azure Portal
A Azure Portal a kiépítés vagy a meglévő virtuális gépek automatikus javításának konfigurálására használható.

### <a name="new-vms"></a>Új virtuális gépek
Ha új SQL Server virtuális gépet hoz létre a Resource Manager-alapú üzemi modellben, használja a Azure Portal az automatikus javítás konfigurálásához.

A **SQL Server beállítások** lapon válassza a **konfiguráció módosítása** az **Automatikus javítás** alatt lehetőséget. Az alábbi Azure Portal képernyőképen az **SQL-alapú automatikus javítás** panel látható.

![SQL-alapú automatikus javítás a Azure Portal](./media/automated-patching/azure-sql-arm-patching.png)

További információ: [SQL Server virtuális gép kiépítése az Azure](create-sql-vm-portal.md)-ban.

### <a name="existing-vms"></a>Meglévő virtuális gépek

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Meglévő SQL Server virtuális gépekhez nyissa meg az [SQL Virtual Machines-erőforrást](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource) , és válassza a **javítás** lehetőséget a **Beállítások** területen. 

![SQL automatikus javítás a meglévő virtuális gépekhez](./media/automated-patching/azure-sql-rm-patching-existing-vms.png)


Ha elkészült, kattintson az **SQL Server konfiguráció** panel alján található **OK** gombra a módosítások mentéséhez.

Ha első alkalommal engedélyezi az automatikus javítást, az Azure a háttérben konfigurálja a SQL Server IaaS-ügynököt. Ebben az időszakban előfordulhat, hogy a Azure Portal nem jelzi, hogy az automatikus javítás konfigurálva van. Várjon néhány percet, amíg az ügynök telepítve és konfigurálva van. Ezt követően a Azure Portal az új beállításokat tükrözi.

## <a name="configure-with-powershell"></a>Konfigurálás a PowerShell-lel
Az SQL-alapú virtuális gép üzembe helyezése után a PowerShell használatával konfigurálja az automatikus javítást.

A következő példában a PowerShell segítségével konfigurálhatja az automatikus javítást egy meglévő SQL Server VMon. A **New-AzVMSqlServerAutoPatchingConfig** parancs új karbantartási időszakot konfigurál az automatikus frissítésekhez.

```azurepowershell
$vmname = "vmname"
$resourcegroupname = "resourcegroupname"
$aps = New-AzVMSqlServerAutoPatchingConfig -Enable -DayOfWeek "Thursday" -MaintenanceWindowStartingHour 11 -MaintenanceWindowDuration 120  -PatchCategory "Important"
s
Set-AzVMSqlServerExtension -AutoPatchingSettings $aps -VMName $vmname -ResourceGroupName $resourcegroupname
```

> [!IMPORTANT]
> Ha a bővítmény még nincs telepítve, akkor a telepítés újraindítja SQL Server.

A jelen példa alapján a következő táblázat ismerteti a cél Azure-beli virtuális gép gyakorlati hatását:

| Paraméter | Hatás |
| --- | --- |
| **DayOfWeek** |A javítások minden csütörtökön telepítve vannak. |
| **MaintenanceWindowStartingHour** |A frissítés megkezdése 11:00 órakor. |
| **MaintenanceWindowsDuration** |A javításokat 120 percen belül kell telepíteni. A kezdési időpont alapján a befejezésük 1 – 16:00. |
| **PatchCategory** |A paraméter egyetlen lehetséges beállítása **fontos**. Ez telepíti a Windows Update fontos jelölését; nem telepít olyan SQL Server frissítéseket, amelyek nem szerepelnek ebben a kategóriában. |

A SQL Server IaaS-ügynök telepítése és konfigurálása több percet is igénybe vehet.

Az automatikus javítás letiltásához futtassa ugyanazt a parancsfájlt anélkül, hogy a **-enable** paramétert a **New-AzVMSqlServerAutoPatchingConfig**. A **-enable** paraméter hiánya jelzi, hogy a parancs letiltja a funkciót.

## <a name="next-steps"></a>Következő lépések
További információ az egyéb rendelkezésre álló automatizálási feladatokról: [SQL Server IaaS-ügynök bővítmény](sql-server-iaas-agent-extension-automate-management.md).

A SQL Server Azure-beli virtuális gépeken való futtatásával kapcsolatos további információkért lásd: [SQL Server az Azure Virtual Machines szolgáltatásban – áttekintés](sql-server-on-azure-vm-iaas-what-is-overview.md).

