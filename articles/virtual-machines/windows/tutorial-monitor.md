---
title: Oktatóanyag – Windows rendszerű virtuális gépek monitorozása az Azure-ban
description: Ebből az oktatóanyagból megtudhatja, hogyan figyelheti a Windows rendszerű virtuális gépeken futó teljesítmény-és felderített alkalmazás-összetevőket.
author: mgoedtel
manager: carmonm
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: tutorial
ms.workload: infrastructure
ms.date: 09/27/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 8c4345d16efe296413ea9d995c2307bba62d33f8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560701"
---
# <a name="tutorial-monitor-a-windows-virtual-machine-in-azure"></a>Oktatóanyag: Windowsos virtuális gép figyelése az Azure-ban

Az Azure monitoring ügynökök használatával gyűjti az Azure-beli virtuális gépekről származó rendszerindítási és teljesítményadatokat, tárolja ezeket az adatokat az Azure Storage-ban, és elérhetővé teszi a portálon, a Azure PowerShell modulon és az Azure CLI-n keresztül A speciális figyelés Azure Monitor for VMs a teljesítmény-metrikák gyűjtésével, a virtuális gépre telepített alkalmazás-összetevők felderítésével, valamint a teljesítmény-és a függőségi térképekkel együtt.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Rendszerindítási diagnosztika engedélyezése a virtuális gépen
> * Rendszerindítási diagnosztika megtekintése
> * Virtuálisgép-gazda metrikáinak megtekintése
> * Azure Monitor for VMs engedélyezése
> * VIRTUÁLIS gépek teljesítmény-metrikáinak megtekintése
> * Riasztás létrehozása

## <a name="launch-azure-cloud-shell"></a>Az Azure Cloud Shell elindítása

Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. 

A Cloud Shell megnyitásához válassza a **Kipróbálás** lehetőséget egy kódblokk jobb felső sarkában. A Cloud Shell egy külön böngészőablakban is elindíthatja [https://shell.azure.com/powershell](https://shell.azure.com/powershell) . A **Copy** (másolás) gombra kattintva másolja és illessze be a kódot a Cloud Shellbe, majd nyomja le az Enter billentyűt a futtatáshoz.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

Ebben az oktatóanyagban az Azure-monitorozás és a frissítéskezelés konfigurálásához egy Azure-beli windowsos virtuális gép szükséges. Először a [Get-Credential](/powershell/module/microsoft.powershell.security/get-credential) paranccsal állítsa be a virtuális gép rendszergazdai felhasználónevét és jelszavát:

```azurepowershell-interactive
$cred = Get-Credential
```

Most hozza létre a virtuális gépet a [New-AzVM](/powershell/module/az.compute/new-azvm). Az alábbi példában egy *myVM* nevű virtuális gépet hozunk létre az *USA keleti régiója* helyen. Ha még nem létezik, létrejön a *myResourceGroupMonitorMonitor* nevű erőforráscsoport a támogató hálózati erőforrásokkal együtt:

```azurepowershell-interactive
New-AzVm `
    -ResourceGroupName "myResourceGroupMonitor" `
    -Name "myVM" `
    -Location "East US" `
    -Credential $cred
```

Az erőforrások és a virtuális gép létrehozása néhány percig tart.

## <a name="view-boot-diagnostics"></a>Rendszerindítási diagnosztika megtekintése

A Windows rendszerű virtuális gépek rendszerindítása után a rendszerindítási diagnosztikai ügynök rögzíti a képernyőkimenetet, amelyet hibaelhárítási célokra lehet felhasználni. Ez a képesség alapértelmezés szerint engedélyezve van. A rögzített képernyőképeket egy Azure Storage-fiók tárolja, amelyet a rendszer alapértelmezés szerint is létrehoz.

A rendszerindítási diagnosztika adatait a [Get-AzureRmVMBootDiagnosticsData](/powershell/module/az.compute/get-azvmbootdiagnosticsdata) paranccsal kérheti le. A következő példában a rendszerindítási diagnosztika a *c:\* meghajtó gyökérkönyvtárába töltődik le.

```powershell
Get-AzVMBootDiagnosticsData -ResourceGroupName "myResourceGroupMonitor" -Name "myVM" -Windows -LocalPath "c:\"
```

## <a name="view-host-metrics"></a>Gazdagép metrikáinak megtekintése

A Windows rendszerű virtuális gép egy dedikált gazdagéppel kommunikál az Azure-ban. A rendszer automatikusan összegyűjti a gazdagép metrikáit, amelyek megtekinthetők az Azure Portalon.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.
2. A virtuális gazdagép teljesítményének megtekintéséhez kattintson a **Metrikák** elemre a virtuális gép paneljén, majd válassza ki valamelyik gazdagépmetrikát a **Rendelkezésre álló metrikák** területen.

    ![Gazdagép metrikáinak megtekintése](./media/tutorial-monitoring/tutorial-monitor-host-metrics.png)

## <a name="enable-advanced-monitoring"></a>Speciális figyelés engedélyezése

Az Azure-beli virtuális gép monitorozásának engedélyezése Azure Monitor for VMs használatával:

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.

2. A virtuális gép lap **figyelés** területén válassza az eredmények **(előzetes verzió)** lehetőséget.

3. Az **áttekintések (előzetes verzió)** lapon válassza a **kipróbálás most** lehetőséget.

    ![Virtuális gép Azure Monitor for VMsának engedélyezése](../../azure-monitor/vm/media/vminsights-enable-portal/enable-vminsights-vm-portal.png)

4. Ha egy meglévő Log Analytics munkaterülettel rendelkezik ugyanabban az előfizetésben, akkor a **Azure monitor** megállapítások bevezetése lapon válassza ki azt a legördülő listából.  

    A lista előjelöli az alapértelmezett munkaterületet és helyet, ahol a virtuális gép üzembe lett helyezve az előfizetésben. 

    >[!NOTE]
    >Ha új Log Analytics munkaterületet szeretne létrehozni a figyelési adatok virtuális gépről való tárolásához, tekintse meg a [log Analytics munkaterület létrehozása](../../azure-monitor/logs/quick-create-workspace.md)című témakört. A munkaterületnek a [támogatott régiók](../../azure-monitor/vm/vminsights-configure-workspace.md#supported-regions)valamelyikéhez kell tartoznia.

Ha engedélyezte a figyelést, előfordulhat, hogy néhány percet várnia kell, amíg meg nem tekinti a virtuális gép teljesítmény-metrikáit.

![Azure Monitor for VMs figyelés telepítésének engedélyezése](../../azure-monitor/vm/media/vminsights-enable-portal/onboard-vminsights-vm-portal-status.png)

## <a name="view-vm-performance-metrics"></a>VIRTUÁLIS gépek teljesítmény-metrikáinak megtekintése

Azure Monitor for VMs olyan teljesítménymutatókat tartalmaz, amelyek több fő teljesítménymutatót (KPI-ket) céloznak meg, amelyek segítségével meghatározhatja, hogy a virtuális gép milyen jól van végrehajtva. A virtuális gépről való hozzáféréshez hajtsa végre az alábbi lépéseket.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.

2. A virtuális gép lap **figyelés** területén válassza az eredmények **(előzetes verzió)** lehetőséget.

3. Válassza ki a **Teljesítmény** fület.

Ez a lap nem csak a teljesítmény-kihasználtsági diagramokat tartalmazza, hanem egy táblázatot is, amely minden felderített logikai lemezhez, annak kapacitásához, kihasználtságához és teljes átlagához tartozik.

## <a name="create-alerts"></a>Riasztások létrehozása

Létrehozhat megadott teljesítménymetrikákon alapuló riasztásokat. A riasztások segítségével értesülhet például arról, ha az átlagos processzorhasználat meghalad egy bizonyos küszöbértéket vagy a rendelkezésre álló szabad lemezterület egy adott érték alá csökken. A riasztások megjeleníthetők az Azure Portalon vagy elküldhetők e-mailben. A létrehozott riasztásokra adott válaszként aktiválhatók Azure Automation-runbookok vagy Azure Logic Apps-alkalmazások.

A következő példában az átlagos processzorhasználat alapján hozunk létre riasztást.

1. Az Azure Portalon kattintson az **Erőforráscsoportok** lehetőségre, és válassza ki a **myResourceGroupMonitor**, majd a **myVM** elemet az erőforrások listájából.

2. Kattintson a **Riasztási szabályok** elemre a virtuális gép paneljén, majd a **Metrikariasztás hozzáadása** lehetőségre a riasztási panel felső részén.

3. Adjon meg egy **nevet** a riasztás számára, például *myAlertRule*

4. Ha szeretne riasztást aktiválni, amikor a processzorhasználat 5 percig meghaladja az 1,0 értéket, hagyja változatlanul az összes többi alapértelmezett beállítást.

5. E-mail-értesítés küldéséhez jelölje be az *E-mail küldése a tulajdonosoknak, közreműködőknek és olvasóknak* jelölőnégyzetet. Az alapértelmezett művelet az értesítés megjelenítése a portálon.

6. Kattintson az **OK** gombra.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban konfigurálta és megtekintette a virtuális gép teljesítményét. Megtanulta végrehajtani az alábbi műveleteket:

> [!div class="checklist"]
> * Erőforráscsoport és virtuális gép létrehozása
> * Rendszerindítási diagnosztika engedélyezése a virtuális gépen
> * Rendszerindítási diagnosztika megtekintése
> * Gazdagép metrikáinak megtekintése
> * Azure Monitor for VMs engedélyezése
> * Virtuálisgép-metrikák megtekintése
> * Riasztás létrehozása

Folytassa a következő oktatóanyaggal, amely az Azure Security Center használatát ismerteti.

> [!div class="nextstepaction"]
> [A virtuális gépek biztonságának kezelése](../../security/fundamentals/overview.md)
