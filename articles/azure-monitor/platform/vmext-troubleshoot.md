---
title: Az Azure Log Analytics virtuálisgép-bővítmény hibáinak megoldása
description: Leírja a Windows és a Linux rendszerű Azure virtuális gépek Log Analytics virtuálisgép-bővítményével kapcsolatos leggyakoribb problémák tüneteit, okait és megoldásait.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/06/2019
ms.openlocfilehash: e16531484505f055c1383aff5adb40518719d98a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80054582"
---
# <a name="troubleshooting-the-log-analytics-vm-extension-in-azure-monitor"></a>A Log Analytics virtuális gépi bővítményének hibaelhárítása az Azure Monitorban
Ez a cikk segítséget nyújt a Windows-és Linux-alapú virtuális gépek Log Analytics virtuálisgép-bővítményével kapcsolatos hibaelhárítási hibák elhárításához Microsoft Azure rendszeren, és javaslatot tesz a lehetséges megoldásokra.

A bővítmény állapotának ellenőrzéséhez hajtsa végre a következő lépéseket a Azure Portal.

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com).
2. Az Azure Portalon kattintson a **Minden szolgáltatás** lehetőségre. Az erőforrások listájában írja be a következőt: **Virtual Machines**. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Virtuális gépek** lehetőséget.
3. A virtuális gépek listájában keresse meg és válassza ki azt.
3. A virtuális gépen kattintson a **bővítmények**lehetőségre.
4. A listából ellenőrizze, hogy az Log Analytics-bővítmény engedélyezve van-e, vagy sem.  A Linux esetében az ügynök **OMSAgentforLinux** és Windows rendszeren szerepel, az ügynök **MicrosoftMonitoringAgent**néven szerepel.

   ![Virtuálisgép-bővítmény nézet](./media/vmext-troubleshoot/log-analytics-vmview-extensions.png)

4. A részletek megtekintéséhez kattintson a bővítményre. 

   ![Virtuálisgép-bővítmény részletei](./media/vmext-troubleshoot/log-analytics-vmview-extensiondetails.png)

## <a name="troubleshooting-azure-windows-vm-extension"></a>Azure Windows VM-bővítmény hibaelhárítása

Ha a *Microsoft monitoring Agent* virtuálisgép-bővítménye nem telepíti vagy nem jelent jelentést, a probléma megoldásához hajtsa végre a következő lépéseket.

1. Ellenőrizze, hogy az Azure-beli virtuálisgép-ügynök telepítve van-e és megfelelően működik-e a [KB 2965986](https://support.microsoft.com/kb/2965986#mt1)-as lépésekkel.
   * A virtuálisgép-ügynök naplófájlját is ellenőrizheti`C:\WindowsAzure\logs\WaAppAgent.log`
   * Ha a napló nem létezik, a virtuálisgép-ügynök nincs telepítve.
   * [Az Azure VM-ügynök telepítése](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Tekintse át a Microsoft monitoring Agent virtuálisgép-bővítmény naplófájljait a`C:\Packages\Plugins\Microsoft.EnterpriseCloud.Monitoring.MicrosoftMonitoringAgent`
3. Győződjön meg arról, hogy a virtuális gép képes futtatni a PowerShell-parancsfájlokat
4. Győződjön meg arról, hogy a C:\Windows\temp engedélyei nem változtak
5. Tekintse meg a Microsoft monitoring Agent állapotát úgy, hogy beírja a következőt egy emelt szintű PowerShell-ablakban a virtuális gépen`(New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg').GetCloudWorkspaces() | Format-List`
6. Tekintse át a Microsoft monitoring Agent telepítési naplófájljait a fájlban.`C:\Windows\System32\config\systemprofile\AppData\Local\SCOM\Logs`

További információ: Windows- [bővítmények hibaelhárítása](../../virtual-machines/extensions/oms-windows.md).

## <a name="troubleshooting-linux-vm-extension"></a>Linuxos virtuálisgép-bővítmény hibaelhárítása
[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)] 
Ha a linuxos virtuálisgép-bővítmény *log Analytics ügynöke* nem telepíti vagy nem jelenti a telepítést, akkor a probléma megoldásához hajtsa végre a következő lépéseket.

1. Ha a bővítmény állapota *ismeretlen* , ellenőrizze, hogy az Azure VM-ügynök telepítve van-e és megfelelően működik-e a virtuálisgép-ügynök naplófájljának áttekintésével`/var/log/waagent.log`
   * Ha a napló nem létezik, a virtuálisgép-ügynök nincs telepítve.
   * [Az Azure-beli virtuálisgép-ügynök telepítése Linux rendszerű virtuális gépeken](../../azure-monitor/learn/quick-collect-azurevm.md#enable-the-log-analytics-vm-extension)
2. Egyéb nem kifogástalan állapotok esetén tekintse át a Linux rendszerű virtuális gépek bővítményének Log Analytics-ügynökének naplófájljait a következőben: `/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/extension.log` és`/var/log/azure/Microsoft.EnterpriseCloud.Monitoring.OmsAgentForLinux/*/CommandExecution.log`
3. Ha a bővítmény állapota Kifogástalan, de nem töltődik fel az adatai, tekintse át a Linux-naplófájlok Log Analytics ügynökét a következőben:`/var/opt/microsoft/omsagent/log/omsagent.log`

További információ: Linux- [bővítmények hibaelhárítása](../../virtual-machines/extensions/oms-linux.md).

## <a name="next-steps"></a>További lépések

Az Azure-on kívüli számítógépeken futó Linux Log Analytics ügynökkel kapcsolatos további hibaelhárítási útmutatót az [azure log Analytics Linux-ügynök hibaelhárítása](agent-linux-troubleshoot.md)című témakörben talál.  
