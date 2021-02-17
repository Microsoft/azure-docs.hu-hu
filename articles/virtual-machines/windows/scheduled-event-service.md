---
title: Az Azure-beli virtuális gépek ütemezett eseményeinek monitorozása
description: Ismerje meg, hogyan figyelheti meg az Azure-beli virtuális gépeket az ütemezett eseményekhez.
author: mysarn
ms.service: virtual-machines
ms.subservice: monitoring
ms.date: 08/20/2019
ms.author: sarn
ms.topic: how-to
ms.openlocfilehash: a5e280fb562bf9bb36d0d729e5f494bd23776ec7
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100570130"
---
# <a name="monitor-scheduled-events-for-your-azure-vms"></a>Azure-beli virtuális gépek ütemezett eseményeinek monitorozása

A frissítések minden nap különböző Azure-részekre érvényesek, így a szolgáltatások biztonságban és naprakészen tarthatók. A tervezett frissítések mellett a nem tervezett események is előfordulhatnak. Ha például valamilyen hardveres romlást vagy hibát észlel, előfordulhat, hogy az Azure-szolgáltatásoknak nem tervezett karbantartást kell végezniük. Az élő áttelepítés, a memóriában lévő frissítések és a frissítések hatására általában szigorú sáv tart fenn, a legtöbb esetben ezek az események szinte transzparensek az ügyfeleknek, és a virtuális gépek lefagyása nem befolyásolja a legtöbb esetet. Egyes alkalmazások esetében azonban a virtuális gépek befagyasztásának néhány másodperce is hatással lehet. Fontos tudni, hogy a közelgő Azure-karbantartási feladatok a lehető legjobb élményt biztosítják az alkalmazások számára. A [Scheduled Events szolgáltatás](scheduled-events.md) egy programozott felületet biztosít a közelgő karbantartásról, és lehetővé teszi a karbantartás zökkenőmentes kezelését. 

Ebben a cikkben bemutatjuk, hogyan használhatók az ütemezett események olyan karbantartási eseményekről, amelyek hatással lehetnek a virtuális gépekre, és olyan alapszintű automatizálást készíthetnek, amely segíthet a monitorozásban és az elemzésben.


## <a name="routing-scheduled-events-to-log-analytics"></a>Ütemezett események útválasztása Log Analyticsba

Scheduled Events az [azure instance metadata Service](instance-metadata-service.md)részeként érhető el, amely minden Azure-beli virtuális gépen elérhető. Az ügyfelek írhatnak automatizálást a virtuális gépek végpontjának lekéréséhez, hogy megkeressék az ütemezett karbantartási értesítéseket, és csökkentsék a szükséges megoldásokat, például az állapot mentését és a virtuális gép elforgatásának elvégzését. Javasoljuk, hogy az automatizálás létrehozásával rögzítse a Scheduled Events, így az Azure karbantartási eseményeinek naplózási naplója is lehet. 

Ebből a cikkből megtudhatja, hogyan rögzítheti Log Analytics a karbantartási Scheduled Events. Ezután elindítunk néhány alapszintű értesítési műveletet, például e-mailek küldését a csapatnak, és bemutatjuk a virtuális gépeket érintő összes esemény előzményeit. Az események összesítéséhez és automatizálásához [log Analytics](../../azure-monitor/logs/quick-create-workspace.md)fogjuk használni, de a naplók összegyűjtéséhez és az automatizálás elindításához bármelyik figyelési megoldás használható.

![Az esemény életciklusát ábrázoló diagram](./media/notifications/events.png)

## <a name="prerequisites"></a>Előfeltételek

Ebben a példában létre kell hoznia egy [Windows rendszerű virtuális gépet egy rendelkezésre állási csoportba](tutorial-availability-sets.md). Scheduled Events a rendelkezésre állási csoport, a Cloud Service, a virtuálisgép-méretezési csoport vagy az önálló virtuális gépek bármelyikét érintő változásokkal kapcsolatos értesítéseket biztosítson. Egy olyan [szolgáltatást](https://github.com/microsoft/AzureScheduledEventsService) fogunk futtatni, amely egy gyűjtőként működő virtuális gép ütemezett eseményeit kérdezi le, hogy beszerezze az eseményeket a rendelkezésre állási csoport összes többi virtuális gépe számára.    

Ne törölje a csoport erőforráscsoportot az oktatóanyag végén.

[Létre kell hoznia egy log Analytics munkaterületet](../../azure-monitor/logs/quick-create-workspace.md) is, amelyet a rendelkezésre állási csoportba tartozó virtuális gépek adatainak összesítésére fogunk használni.

## <a name="set-up-the-environment"></a>A környezet beállítása

A rendelkezésre állási csoportnak két kezdeti virtuális géppel kell rendelkeznie. Most létre kell hozni egy harmadik virtuális gépet `myCollectorVM` ugyanabban a rendelkezésre állási csoportba. 

```azurepowershell-interactive
New-AzVm `
   -ResourceGroupName "myResourceGroupAvailability" `
   -Name "myCollectorVM" `
   -Location "East US" `
   -VirtualNetworkName "myVnet" `
   -SubnetName "mySubnet" `
   -SecurityGroupName "myNetworkSecurityGroup" `
   -OpenPorts 3389 `
   -PublicIpAddressName "myPublicIpAddress3" `
   -AvailabilitySetName "myAvailabilitySet" `
   -Credential $cred
```
 

Töltse le a projekt telepítési. zip fájlját a [githubról](https://github.com/microsoft/AzureScheduledEventsService/archive/master.zip).

Kapcsolódjon a **myCollectorVM** , és másolja a. zip fájlt a virtuális gépre, és bontsa ki az összes fájlt. Nyisson meg egy PowerShell-parancssort a virtuális gépen. Helyezze át a promptot a mappát tartalmazó mappába `SchService.ps1` , például: `PS C:\Users\azureuser\AzureScheduledEventsService-master\AzureScheduledEventsService-master\Powershell>` , és állítsa be a szolgáltatást.

```powershell
.\SchService.ps1 -Setup
```

Indítsa el a szolgáltatást.

```powershell
.\SchService.ps1 -Start
```

A szolgáltatás mostantól 10 másodpercenként megkezdi a lekérdezést minden ütemezett eseményre, és jóváhagyja az eseményeket a karbantartás meggyorsításához.  A befagyasztás, az újraindítás, az újbóli üzembe helyezés és a megelőzik az események ütemezett eseményeinek rögzítése.   Vegye figyelembe, hogy a parancsfájlt kiterjesztve az esemény jóváhagyása előtt bizonyos enyhítéseket is aktiválhat.

Ellenőrizze a szolgáltatás állapotát, és győződjön meg róla, hogy fut.

```powershell
.\SchService.ps1 -status  
```

Ennek vissza kell térnie `Running` .

A szolgáltatás mostantól 10 másodpercenként megkezdi a lekérdezést minden ütemezett eseményre, és jóváhagyja az eseményeket a karbantartás meggyorsításához.  A befagyasztás, az újraindítás, az újbóli üzembe helyezés és a megelőzik az események ütemezett eseményei által rögzített események. Kiterjesztheti a szkriptet bizonyos enyhítések elindításához az esemény jóváhagyása előtt.

Ha a fenti események bármelyikét rögzíti az Event Service szolgáltatás, akkor az az alkalmazás eseménynapló eseményének állapota, az esemény típusa, az erőforrások (a virtuális gépek nevei) és a NotBefore (a minimális értesítési időszak) bekerül a naplóba. Az alkalmazás-eseménynaplóban megtalálhatja az 1234 AZONOSÍTÓJÚ eseményeket.

A szolgáltatás beállítása és elindítása után a rendszer naplózza az eseményeket a Windows-alkalmazás naplófájljaiban.   A működés ellenőrzéséhez indítsa újra az egyik virtuális gépet a rendelkezésre állási csoportból, és meg kell jelennie az eseménynaplóban naplózott eseménynek a Windows-naplók > az alkalmazás naplójában, amely a virtuális gép újraindítását mutatja. 

![Képernyőkép az Eseménynaplóról.](./media/notifications/event-viewer.png)

Ha az események az ütemezett esemény szolgáltatásban vannak rögzítve, akkor az alkalmazás naplózva lesz az esemény állapota, az esemény típusa, az erőforrások (virtuális gép neve) és a NotBefore (a minimális értesítési időszak). Az alkalmazás-eseménynaplóban megtalálhatja az 1234 AZONOSÍTÓJÚ eseményeket.

> [!NOTE] 
> Ebben a példában a virtuális gépek egy rendelkezésre állási csoportba tartoznak, amely lehetővé tette, hogy egyetlen virtuális gépet jelöljön ki a gyűjtőnek, amely figyeli és irányítja az ütemezett eseményeket a log Analytics-beli működés területére. Ha önálló virtuális gépekkel rendelkezik, minden virtuális gépen futtathatja a szolgáltatást, majd egyenként összekapcsolhatja őket a log Analytics-munkaterülethez.
>
> A beállításhoz a Windowst választottuk, de a Linuxon hasonló megoldást is tervezhet.

Bármikor leállíthatja vagy eltávolíthatja az ütemezett esemény szolgáltatást a kapcsolók és a használatával `–stop` `–remove` .

## <a name="connect-to-the-workspace"></a>Kapcsolódás a munkaterülethez


Most szeretnénk csatlakozni egy Log Analytics munkaterülethez a gyűjtő virtuális géphez. Az Log Analytics munkaterület adattárként működik, és az Eseménynapló-gyűjteményt úgy konfigurálja, hogy rögzítse az alkalmazás naplóit a gyűjtő virtuális gépről. 

 Ahhoz, hogy a Scheduled Events az eseménynaplóba irányítsa, amelyet a szolgáltatás az alkalmazási naplóként fog menteni, a virtuális gépet a Log Analytics-munkaterülethez kell kötnie.  
 
1. Nyissa meg a létrehozott munkaterület oldalát.
1. **A kapcsolódás adatforráshoz** területen válassza az **Azure Virtual Machines (VM)** lehetőséget.

    ![Kapcsolódás virtuális géphez adatforrásként](./media/notifications/connect-to-data-source.png)

1. Keresse meg és válassza ki a **myCollectorVM**. 
1. A **myCollectorVM** új lapján válassza a **kapcsolat** lehetőséget.

Ekkor a rendszer telepíti a [Microsoft monitoring agentet](../extensions/oms-windows.md) a virtuális gépre. A virtuális gép a munkaterülethez való összekapcsolásához és a bővítmény telepítéséhez több percet is igénybe vehet. 

## <a name="configure-the-workspace"></a>A munkaterület konfigurálása

1. Nyissa meg a munkaterülethez tartozó lapot, és válassza a **Speciális beállítások** lehetőséget.
1. Válassza az **adatok** lehetőséget a bal oldali menüben, majd válassza a **Windows-eseménynaplók** lehetőséget.
1. A **következő eseménynaplók** beírásával kezdje el beírni az *alkalmazást* , majd válassza az **alkalmazás** elemet a listából.

    ![Speciális beállítások kiválasztása](./media/notifications/advanced.png)

1. Hagyja a **hiba**, a **Figyelmeztetés** és a kijelölt **információk** lehetőséget, majd válassza a **Mentés** lehetőséget a beállítások mentéséhez.


> [!NOTE]
> Némi késés lesz, és akár 10 percet is igénybe vehet, mielőtt a napló elérhetővé válik. 


## <a name="creating-an-alert-rule-with-azure-monitor"></a>Riasztási szabály létrehozása Azure Monitor 


Az események Log Analyticsba való leküldése után a következő [lekérdezés](../../azure-monitor/logs/log-analytics-tutorial.md) futtatásával megkeresheti az ütemezett eseményeket.

1. A lap tetején válassza a **naplók** lehetőséget, majd illessze be a következőt a szövegmezőbe:

    ```
    Event
    | where EventLog == "Application" and Source contains "AzureScheduledEvents" and RenderedDescription contains "Scheduled" and RenderedDescription contains "EventStatus" 
    | project TimeGenerated, RenderedDescription
    | extend ReqJson= parse_json(RenderedDescription)
    | extend EventId = ReqJson["EventId"]
    ,EventStatus = ReqJson["EventStatus"]
    ,EventType = ReqJson["EventType"]
    ,NotBefore = ReqJson["NotBefore"]
    ,ResourceType = ReqJson["ResourceType"]
    ,Resources = ReqJson["Resources"]
    | project-away RenderedDescription,ReqJson
    ```

1. Válassza a **Mentés** lehetőséget, majd írja be a nevet, hagyja beírni a lekérdezést, írja be a `ogQuery`  `VMLogs` **kategóriát**, majd válassza a **Mentés** lehetőséget. 

    ![A lekérdezés mentése](./media/notifications/save-query.png)

1. Válassza az **Új riasztási szabály** lehetőséget. 
1. A **szabály létrehozása** lapon hagyja meg `collectorworkspace` az **erőforrást**.
1. A **feltétel** területen válassza ki a bejegyzést, *amikor az ügyfél naplójának keresése történik <login undefined>*. Ekkor megnyílik a **jel logikai beállítása** lap.
1. A **küszöbérték** mezőben adja meg a *0* értéket, majd válassza a **kész** lehetőséget.
1. A **műveletek** területen válassza a **műveleti csoport létrehozása** lehetőséget. Ekkor megnyílik a **műveleti csoport hozzáadása** lap.
1. A **műveleti csoport neve** mezőbe írja be a következőt: *myActionGroup*.
1. A **rövid név** mezőbe írja be a következőt: *myActionGroup*.
1. Az **erőforráscsoport** területen válassza a **myResourceGroupAvailability** lehetőséget.
1. A műveletek területen a **művelet neve** mezőbe írja be az **e-mail** nevet, majd válassza az **E-mail/SMS/leküldés/hang** lehetőséget. Ekkor megnyílik az **e-mail/SMS/push/Voice** oldal.
1. Válassza az **e-mail** lehetőséget, írja be az e-mail címét, majd kattintson **az OK gombra**.
1. A **műveleti csoport hozzáadása** lapon kattintson az **OK gombra**. 
1. A **szabály létrehozása** lap **riasztás részletei** területén írja be a *myAlert* **nevet a riasztási szabály neveként**, majd írja be a **leíráshoz** az *e-mailes riasztási szabályt* .
1. Ha elkészült, válassza a **riasztási szabály létrehozása** lehetőséget.
1. Indítsa újra az egyik virtuális gépet a rendelkezésre állási csoportból. Néhány percen belül egy e-mailt kell kapnia arról, hogy a riasztás aktiválva lett.

A riasztási szabályok kezeléséhez nyissa meg az erőforráscsoportot, válassza a bal oldali menüben a **riasztások** elemet, majd a lap tetején válassza a **riasztási szabályok kezelése** lehetőséget.

     
## <a name="next-steps"></a>Következő lépések

További információ: [ütemezett események szolgáltatás](https://github.com/microsoft/AzureScheduledEventsService) lapja a githubon.