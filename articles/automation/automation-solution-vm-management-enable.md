---
title: Azure Automation Start/Stop VMs during off-hours engedélyezése
description: Ez a cikk azt ismerteti, hogyan engedélyezheti az Azure-beli virtuális gépek Start/Stop VMs during off-hours funkcióját.
services: automation
ms.subservice: process-automation
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 36f885416c5e9cb656d01a65b9c503f8897d2f9f
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593908"
---
# <a name="enable-startstop-vms-during-off-hours"></a>Virtuális gépek munkaidőn kívüli indításának és leállításának engedélyezése

A témakörben ismertetett lépések végrehajtásával engedélyezheti a virtuális gépek Start/Stop VMs during off-hours funkcióját egy új vagy meglévő Automation-fiók és a csatolt Log Analytics-munkaterület használatával. A telepítési folyamat befejezése után konfigurálja a változókat a funkció testreszabásához.

>[!NOTE]
>A szolgáltatás klasszikus virtuális gépekkel való használatához klasszikus futtató fiókra van szükség, amely alapértelmezés szerint nem jön létre. Lásd: [klasszikus futtató fiók létrehozása](automation-create-standalone-account.md#create-a-classic-run-as-account).
>

## <a name="create-resources-for-the-feature"></a>Erőforrások létrehozása a szolgáltatáshoz

1. Jelentkezzen be az Azure [Portalra](https://portal.azure.com).
2. Keresse meg és válassza ki az **Automation-fiókokat**.
3. Az Automation-fiókok lapon válassza ki az Automation-fiókját a listából.
4. Az Automation-fiókban válassza a **virtuális gép indítása/leállítása** **kapcsolódó erőforrások** lehetőséget. Innen kattintson **a további információ és a megoldás engedélyezése** lehetőségre. Ha már telepítette a szolgáltatást, kattintson a **megoldás kezelése** lehetőségre, és keresse meg azt a listában.

   ![Engedélyezés az Automation-fiókból](./media/automation-solution-vm-management/enable-from-automation-account.png)

   > [!NOTE]
   > Az erőforrást az **erőforrás létrehozása** lehetőségre kattintva is létrehozhatja a Azure Portal bárhonnan. A piactér lapon írjon be egy kulcsszót (például **Indítás** vagy **Indítás/Leállítás**). Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Azt is megteheti, hogy egy vagy több kulcsszót ír be a szolgáltatás teljes nevéből, majd lenyomja az **ENTER** billentyűt. A keresési eredmények közül válassza a **Start/Stop VMS During off-hours** lehetőséget.

5. A kiválasztott központi telepítés Start/Stop VMs during off-hours lapján tekintse át az összegzési információkat, majd kattintson a **Létrehozás** gombra.

   ![Azure Portal](media/automation-solution-vm-management/azure-portal-01.png)

## <a name="configure-the-feature"></a>A szolgáltatás konfigurálása

A létrehozott erőforrással megjelenik a megoldás hozzáadása lap. A rendszer arra kéri, hogy konfigurálja a szolgáltatást, mielőtt importálni tudja az Automation-előfizetésbe. Lásd: [Start/Stop VMS During off-hours konfigurálása](automation-solution-vm-management-config.md).

   ![Virtuálisgép-felügyelet – megoldás hozzáadása lap](media/automation-solution-vm-management/azure-portal-add-solution-01.png)

## <a name="select-a-log-analytics-workspace"></a>Log Analytics munkaterület kiválasztása

1. A megoldás hozzáadása lapon válassza a **munkaterület** lehetőséget. Válasszon ki egy Log Analytics munkaterületet, amely az Automation-fiók által használt Azure-előfizetéshez van csatolva. 

2. Ha nem rendelkezik munkaterülettel, válassza az **Új munkaterület létrehozása** lehetőséget. A Log Analytics munkaterület lapon hajtsa végre a következő lépéseket:

   - Adja meg az új Log Analytics munkaterület nevét, például **ContosoLAWorkspace**.
   - Válassza ki azt az **előfizetést** , amelyhez a legördülő listából választhatja ki a hivatkozást, ha az alapértelmezett beállítás nem megfelelő.
   - **Erőforráscsoport** esetén létrehozhat egy új erőforráscsoportot, vagy kiválaszthat egy meglévőt.
   - Válasszon egy **helyet**.
   - Válasszon **árképzési szintet**. Válassza a **GB-os (önálló)** lehetőséget. Azure Monitor naplók frissültek a [díjszabással](https://azure.microsoft.com/pricing/details/log-analytics/) , és a GB-onként az egyetlen lehetőség.

   > [!NOTE]
   > A szolgáltatások engedélyezésekor csak bizonyos régiók támogatottak Log Analytics munkaterület és egy Automation-fiók összekapcsolásához. A támogatott leképezési párok listáját lásd: [az Automation-fiók és a log Analytics munkaterület-hozzárendelési területe](how-to/region-mappings.md).

3. A szükséges információk megadása után a Log Analytics munkaterület lapon kattintson a **Létrehozás** gombra. A menü **értesítések** részén nyomon követheti a folyamat állapotát, amely visszaadja a megoldás hozzáadása lapra, ha elkészült.

## <a name="add-automation-account"></a>Automation-fiók hozzáadása

Nyissa meg újra a megoldás hozzáadása lapot, és válassza az **Automation-fiók** lehetőséget. Kiválaszthat egy meglévő Automation-fiókot, amely még nincs Log Analytics munkaterülethez társítva. Ha új Log Analytics munkaterületet hoz létre, létrehozhat egy új Automation-fiókot, amellyel társíthatja azt. Válasszon ki egy meglévő Automation-fiókot, vagy kattintson az Automation-fiók **létrehozása** lehetőségre, majd az Automation-fiók hozzáadása lapon adja meg az Automation-fiók nevét a **név** mezőben.

A rendszer a kijelölt Log Analytics munkaterület alapján automatikusan kitölti az összes többi beállítást. Ezek a beállítások nem módosíthatók. Az Azure-beli futtató fiók a szolgáltatáshoz tartozó runbookok alapértelmezett hitelesítési módszere. 

Miután rákattintott az **OK gombra**, a rendszer érvényesíti a konfigurációs beállításokat, és létrehozza az Automation-fiókot. Az **Értesítések** menüpont alatt nyomon követheti a folyamat előrehaladását.

## <a name="define-feature-parameters"></a>Szolgáltatás paramétereinek megadása

1. A megoldás hozzáadása lapon válassza a **Konfigurálás** lehetőséget. Megjelenik a paraméterek lap.

    ![Paraméterek lap a megoldáshoz](media/automation-solution-vm-management/azure-portal-add-solution-02.png)

2. A **cél ResourceGroup nevei** mező értékének megadása. A mező a szolgáltatás által felügyelni kívánt virtuális gépeket tartalmazó csoportok nevét határozza meg. Több nevet is megadhat, és vesszővel elválaszthatja a neveket (az értékek nem megkülönböztetik a kis-és nagybetűket). Helyettesítő karaktert is használhat, ha az előfizetés összes erőforráscsoportjában lévő virtuális gépeket szeretné megadni. Az értékeket a `External_Start_ResourceGroupNames` és változókban tárolja a rendszer `External_Stop_ResourceGroupNames` .

    > [!IMPORTANT]
    > A **célként megadott ResourceGroup-nevek** alapértelmezett értéke a **&ast;** . Ez a beállítás az előfizetésben lévő összes virtuális gépet célozza meg. Ha nem szeretné, hogy a szolgáltatás az előfizetésben lévő összes virtuális gépet megcélozza, meg kell adnia az erőforráscsoport-nevek listáját, mielőtt kiválasztja az ütemtervet.
  
3. A **virtuális gép kizárási listájának (karakterlánc)** mezőjének értékét kell megadnia. Ez az érték egy vagy több virtuális gép neve a cél erőforráscsoporthoz. Több nevet is megadhat, és vesszővel elválaszthatja a neveket (az értékek nem megkülönböztetik a kis-és nagybetűket). Helyettesítő karakter használata támogatott. Ez az érték a változóban van tárolva `External_ExcludeVMNames` .
  
4. Az **ütemterv** mezőben kiválaszthatja a szolgáltatás által a virtuális gépek felügyeletének ütemtervét. Válassza ki az ütemterv kezdési dátumát és időpontját, és hozzon létre egy ismétlődő napi ütemtervet a kiválasztott időponttól kezdve. Egy másik régió kiválasztása nem érhető el. Ha a szolgáltatás konfigurálása után szeretné beállítani az ütemtervet a megadott időzónára, tekintse meg [az indítási és leállítási ütemtervek módosítása](automation-solution-vm-management-config.md#modify-the-startup-and-shutdown-schedules)című témakört.

5. Ha e-mail-értesítéseket szeretne fogadni egy [műveleti csoportból](../azure-monitor/alerts/action-groups.md), fogadja el az alapértelmezett **Igen** értéket az  **e-mail értesítések** mezőben, és adjon meg egy érvényes e-mail-címet. Ha a **nem** lehetőséget választja, de az e-mail-értesítések fogadására később dönt, akkor frissítheti az érvényes e-mail-címekkel létrehozott műveleti csoportot, vesszővel elválasztva. 

6. Engedélyezze a következő riasztási szabályokat:

   - `AutoStop_VM_Child`
   - `Scheduled_StartStop_Parent`
   - `Sequenced_StartStop_Parent`

## <a name="create-alerts"></a>Riasztások létrehozása

A Start/Stop VMs during off-hours nem tartalmaz előre meghatározott riasztásokat. Tekintse át a [naplók létrehozása a Azure monitor](../azure-monitor/alerts/alerts-log.md) segítségével című témakört, amelyből megtudhatja, hogyan hozhat létre sikertelen riasztásokat a DevOps vagy működési folyamatainak és eljárásainak támogatására.

## <a name="deploy-the-feature"></a>A szolgáltatás üzembe helyezése

1. Miután konfigurálta a szolgáltatáshoz szükséges kezdeti beállításokat, kattintson az **OK** gombra a paraméterek lap bezárásához.

2. Kattintson a **Létrehozás** lehetőségre. Az összes beállítás ellenőrzése után a szolgáltatás telepíti az előfizetését. Ez a folyamat több másodpercig is eltarthat, és a menü **értesítések** részén nyomon követheti a folyamat állapotát.

    > [!NOTE]
    > Ha Azure Cloud Solution Provider (Azure CSP) előfizetéssel rendelkezik, az üzembe helyezés befejezése után az Automation-fiókjában lépjen a **változók** elemre a **megosztott erőforrások** területen, és állítsa a [External_EnableClassicVMs](automation-solution-vm-management.md#variables) változót **hamis** értékre. Ezzel leállítja a megoldást a klasszikus virtuálisgép-erőforrások keresésére.

## <a name="next-steps"></a>Következő lépések

* A szolgáltatás beállításához tekintse meg a [virtuális gépek leállítása/elindítása munkaidőn kívüli konfigurálását](automation-solution-vm-management-config.md)ismertető témakört.
* A szolgáltatási hibák megoldásához tekintse meg a [Start/Stop VMS During off-hours problémák elhárítása](troubleshoot/start-stop-vm.md)című témakört.