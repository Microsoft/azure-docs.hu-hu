---
title: Ismerkedés a Azure Automation állapot-konfigurációval
description: Ez a cikk azt ismerteti, hogyan végezheti el a leggyakoribb feladatokat Azure Automation állapot-konfigurációban.
services: automation
ms.subservice: dsc
ms.date: 04/15/2019
ms.topic: conceptual
ms.openlocfilehash: 95072970004ae53663f130a78af36a84a6fab685
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99051498"
---
# <a name="get-started-with-azure-automation-state-configuration"></a>Ismerkedés a Azure Automation állapot-konfigurációval

Ez a cikk részletes útmutatót biztosít a leggyakoribb feladatok végrehajtásához Azure Automation állapot-konfigurációval, például a konfigurációk létrehozásával, importálásával és fordításával, a számítógépek kezelésének engedélyezésével és a jelentések megtekintésével kapcsolatban. Az áttekintő állapot konfigurációját lásd: az [állapot konfigurációjának áttekintése](automation-dsc-overview.md). A DSC-hez készült dokumentációhoz lásd: a [Windows PowerShell kívánt állapot-konfigurációjának áttekintése](/powershell/scripting/dsc/overview/overview).

Ha azt szeretné, hogy a jelen cikkben ismertetett lépések végrehajtása nélkül egy már beállított környezeti környezettel rendelkezik, használhatja a [Azure Automation felügyelt csomópont-sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/101-automation-configuration). Ez a sablon egy teljes állapot-konfigurációs (DSC) környezetet állít be, beleértve az állapot-konfiguráció (DSC) által felügyelt Azure-beli virtuális gépet.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő példák végrehajtásához a következők szükségesek:

- Egy Azure Automation-fiókra. Az Automation-fiók és a hozzá tartozó követelmények megismeréséhez tekintse meg az [Automation-fiók hitelesítésének áttekintése](./automation-security-overview.md)című témakört.
- Egy [támogatott operációs rendszert](automation-dsc-overview.md#operating-system-requirements)futtató Azure Resource Manager virtuális gép (klasszikus). A virtuális gépek létrehozásával kapcsolatos információkért tekintse meg a [Windows virtuális gép létrehozása az Azure Portallal](../virtual-machines/windows/quick-create-portal.md) című cikket.

## <a name="create-a-dsc-configuration"></a>DSC-konfiguráció létrehozása

Létre kell hoznia egy egyszerű [DSC-konfigurációt](/powershell/scripting/dsc/configurations/configurations) , amely a csomópontok hozzárendelésének módjától függően biztosítja a **webkiszolgáló Windows-** szolgáltatás (IIS) jelenlétét vagy hiányát.

1. Indítsa el a [VSCode](https://code.visualstudio.com/docs) (vagy bármely szövegszerkesztőt).
1. Írja be a következő szöveget:

    ```powershell
    configuration TestConfig
    {
        Node IsWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Present'
                Name                 = 'Web-Server'
                IncludeAllSubFeature = $true
            }
        }

        Node NotWebServer
        {
            WindowsFeature IIS
            {
                Ensure               = 'Absent'
                Name                 = 'Web-Server'
            }
        }
    }
    ```
1. Mentse a fájlt **TestConfig.ps1ként**.

Ez a konfiguráció egy erőforrást hív meg minden csomópont-blokkban, a [WindowsFeature-erőforrásban](/powershell/scripting/dsc/reference/resources/windows/windowsfeatureresource). Ez az erőforrás biztosítja a **webkiszolgáló** szolgáltatás jelenlétét vagy hiányát.

## <a name="import-a-configuration-into-azure-automation"></a>Konfiguráció importálása Azure Automationba

Ezután importálja a konfigurációt az Automation-fiókba.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon válassza az **állapot konfigurálása (DSC)** lehetőséget a **konfiguráció kezelése** területen.
1. Az állapot-konfiguráció (DSC) lapon kattintson a **konfigurációk** fülre, majd a **Hozzáadás** gombra.
1. A konfiguráció importálása panelen tallózással keresse meg a `TestConfig.ps1` fájlt a számítógépen.

   ![Képernyőkép a * * konfiguráció importálása * * panelről](./media/automation-dsc-getting-started/AddConfig.png)

1. Kattintson az **OK** gombra.

## <a name="view-a-configuration-in-azure-automation"></a>Konfiguráció megtekintése Azure Automation

A konfiguráció importálása után megtekintheti azt a Azure Portalban.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon válassza az  **állapot konfigurálása (DSC)** lehetőséget a **konfiguráció kezelése** területen.
1. Az állapot konfigurálása (DSC) lapon kattintson a **konfigurációk** fülre, majd a **TestConfig** elemre. Ez az előző eljárásban importált konfiguráció neve.
1. A TestConfig-konfiguráció ablaktáblán kattintson a **konfigurációs forrás megtekintése** elemre.

   ![Képernyőkép a TestConfig-konfiguráció panelről](./media/automation-dsc-getting-started/ViewConfigSource.png)

   Megnyílik egy TestConfig-konfigurációs forrás panel, amely megjeleníti a konfigurációhoz tartozó PowerShell-kódot.

## <a name="compile-a-configuration-in-azure-automation"></a>Konfiguráció fordítása Azure Automation

Ahhoz, hogy a kívánt állapotot egy csomópontra alkalmazza, meg kell adnia egy DSC-konfigurációt, amely meghatározza, hogy az állapotot egy vagy több csomópont-konfigurációban (MOF-dokumentum) kell lefordítani, és a Automation DSC lekérési kiszolgálóra helyezzük. A konfigurációk állapot-konfigurációban (DSC) való fordításának részletes ismertetését lásd: [konfigurációk fordítása Azure Automation állapot konfigurációjában](automation-dsc-compile.md).
A konfigurációk fordításával kapcsolatos további információkért lásd: [DSC-konfigurációk](/powershell/scripting/dsc/configurations/configurations).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció** felügyelete alatt.
1. Az állapot konfigurálása (DSC) lapon kattintson a **konfigurációk** fülre, majd a **TestConfig** elemre. Ez a korábban importált konfiguráció neve.
1. A TestConfig-konfiguráció ablaktáblán kattintson a **fordítás** elemre, majd az **Igen** gombra. Ez elindítja a fordítási feladatot.

   ![Képernyőkép a TestConfig-konfiguráció oldal kiemelésének fordítása gombról](./media/automation-dsc-getting-started/CompileConfig.png)

> [!NOTE]
> Ha Azure Automation konfigurációját fordítja le, a automatikusan telepíti az összes létrehozott csomópont-konfigurációs MOF-fájlt a lekérési kiszolgálóra.

## <a name="view-a-compilation-job"></a>Fordítási feladatok megtekintése

A fordítás elindítása után megtekintheti azt a **konfigurációs** lap **fordítási feladatok** csempén. A **fordítási feladatok** csempe a jelenleg futó, befejezett és sikertelen feladatokat jeleníti meg. Amikor megnyit egy fordítási feladatot tartalmazó ablaktáblát, megjeleníti az adott feladattal kapcsolatos információkat, beleértve az észlelt hibákat vagy figyelmeztetéseket, a konfigurációban használt bemeneti paramétereket és a fordítási naplókat.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció** felügyelete alatt.
1. Az állapot konfigurálása (DSC) lapon kattintson a **konfigurációk** fülre, majd a **TestConfig** elemre. Ez a korábban importált konfiguráció neve.
1. A **fordítási feladatok** területen válassza ki a megtekinteni kívánt fordítási feladatot. Megnyílik egy fordítási feladatok ablaktábla, amely a fordítási feladatok elindításának dátumával van megjelölve.

   ![A fordítási feladatok oldalának képernyőképe](./media/automation-dsc-getting-started/CompilationJob.png)

1. A feladattal kapcsolatos további részletek megtekintéséhez kattintson a fordítási feladatok ablaktábla bármelyik csempére.

## <a name="view-node-configurations"></a>Csomópont-konfigurációk megtekintése

A fordítási feladatok sikeres befejezése egy vagy több új csomópont-konfigurációt hoz létre. A csomópont-konfiguráció egy MOF-dokumentum, amely a lekérési kiszolgálóra van telepítve, és készen áll arra, hogy egy vagy több csomópont lekérje és alkalmazza őket. Az Automation-fiókban megtekintheti a csomópont-konfigurációkat az állapot-konfiguráció (DSC) lapon. A csomópont-konfigurációnak van egy neve az űrlappal `ConfigurationName.NodeName` .

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció** felügyelete alatt.
1. Az állapot-konfiguráció (DSC) lapon kattintson a **lefordított konfigurációk** fülre.

   ![Képernyőfelvétel a lefordított konfigurációk lapról](./media/automation-dsc-getting-started/NodeConfigs.png)

## <a name="enable-an-azure-resource-manager-vm-for-management-with-state-configuration"></a>Azure Resource Manager virtuális gép engedélyezése az állapot-konfigurációval való felügyelethez

Az állapot konfigurációjának használatával felügyelheti az Azure-beli virtuális gépeket (a klasszikus és a Resource Managert is), a helyszíni virtuális gépeket, a linuxos gépeket, az AWS-alapú virtuális gépeket és a helyszíni fizikai gépeket Ebből a cikkből megtudhatja, hogyan engedélyezheti csak Azure Resource Manager virtuális gépeket. A más típusú gépek engedélyezésével kapcsolatos további információkért lásd: a [gépek Azure Automation állapot-konfiguráció általi felügyeletének engedélyezése](automation-dsc-onboarding.md).

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció** felügyelete alatt.
1. Az állapot-konfiguráció (DSC) lapon válassza a **csomópontok** lapot, majd kattintson a **+ Hozzáadás** gombra.

   ![Képernyőkép a DSC-csomópontok oldaláról az Azure-beli virtuális gép hozzáadása gomb kiemelésével](./media/automation-dsc-getting-started/OnboardVM.png)

1. A Virtual Machines ablaktáblán válassza ki a virtuális gépet.
1. A virtuális gép részleteit tartalmazó ablaktáblán kattintson a **+ kapcsolat** lehetőségre.

   > [!IMPORTANT]
   > A virtuális gépnek egy [támogatott operációs rendszert](automation-dsc-overview.md#operating-system-requirements)futtató Azure Resource Manager virtuális gépnek kell lennie.

2. A regisztráció lapon válassza ki annak a csomópont-konfigurációnak a nevét, amelyre alkalmazni kívánja a virtuális gépet a **csomópont-konfiguráció neve** mezőben. Ezen a ponton a név megadása nem kötelező. A csomópont engedélyezése után módosíthatja a hozzárendelt csomópont-konfigurációt.

3. Szükség esetén ellenőrizze az **Újraindítás csomópontot**, majd kattintson **az OK** gombra.

   ![A regisztráció panel képernyőképe](./media/automation-dsc-getting-started/RegisterVM.png)

   A megadott csomópont-konfiguráció a virtuális gépre lesz alkalmazva, a **konfigurációs mód gyakorisága** által meghatározott értékkel megadott időközönként. A virtuális gép a **frissítési gyakoriság** értékében megadott időközönként ellenőrzi a csomópont-konfiguráció frissítéseit. További információ az értékek használatáról: [a helyi Configuration Manager konfigurálása](/powershell/scripting/dsc/managing-nodes/metaConfig).

Az Azure elindítja a virtuális gép engedélyezésének folyamatát. Ha elkészült, a virtuális gép megjelenik az Automation-fiók állapot-konfiguráció (DSC) lapján található **csomópontok** lapon.

## <a name="view-the-list-of-managed-nodes"></a>A felügyelt csomópontok listájának megtekintése

Az Automation-fiókban az állapot-konfiguráció (DSC) lap **csomópontok** lapján megtekintheti a felügyeletre engedélyezett összes gép listáját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció** felügyelete alatt.
1. Az állapot-konfiguráció (DSC) lapon kattintson a **csomópontok** fülre.

## <a name="view-reports-for-managed-nodes"></a>Felügyelt csomópontok jelentéseinek megtekintése

Minden alkalommal, amikor az állapot-konfiguráció konzisztencia-ellenőrzést végez egy felügyelt csomóponton, a csomópont visszaküld egy állapotjelentést a lekérési kiszolgálónak. Ezeket a jelentéseket az adott csomóponton található oldalon tekintheti meg.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció** felügyelete alatt.
1. Az állapot-konfiguráció (DSC) lapon kattintson a **csomópontok** fülre. Itt láthatja a konfiguráció állapotának áttekintését és az egyes csomópontok részleteit.

   ![Képernyőfelvétel a Node lapról](./media/automation-dsc-getting-started/NodesTab.png)

1. A **csomópontok** lapon kattintson a csomópont-rekordra a jelentéskészítés megnyitásához. Kattintson arra a jelentésre, amelyben további jelentéskészítési adatokat szeretne megjeleníteni.

   ![Képernyőfelvétel a jelentés panelről](./media/automation-dsc-getting-started/NodeReport.png)

Az egyes jelentésekhez tartozó panelen a következő állapotinformációkat tekintheti meg a megfelelő konzisztencia-ellenőrzéshez:

- A jelentés állapota Lehetséges értékek:
    * Megfelelő – a csomópont megfelel az ellenőrzési követelményeknek.
   * Sikertelen – a konfiguráció nem tudta végrehajtani az ellenőrzését.
   * Nem megfelelő – a csomópont állapotban van, `ApplyandMonitor` és a gép nem a kívánt állapotban van.
- A konzisztencia-ellenőrzés kezdési időpontja.
- A konzisztencia-ellenőrzés teljes futtatókörnyezete.
- A konzisztencia-ellenőrzés típusa
- Bármilyen hiba, beleértve a hibakódot és a hibaüzenetet.
- A konfigurációban használt összes DSC-erőforrás, valamint az egyes erőforrások állapota (azt határozza meg, hogy a csomópont az adott erőforrás kívánt állapotában van-e). Az egyes erőforrásokra kattintva részletesebb információkat kaphat az adott erőforrásról.
- A csomópont neve, IP-címe és konfigurációs módja.

A **nyers jelentés megtekintése** lehetőségre kattintva megtekintheti azokat a tényleges adatokat, amelyeket a csomópont küld a kiszolgálónak.
További információ az adatok használatáról: [DSC jelentéskészítő kiszolgáló használata](/powershell/scripting/dsc/pull-server/reportserver).

A csomópontok engedélyezése után is eltarthat egy ideig, mielőtt az első jelentés elérhetővé válik. Előfordulhat, hogy az első jelentéshez legfeljebb 30 percet kell várnia, miután engedélyezte a csomópontot.

## <a name="reassign-a-node-to-a-different-node-configuration"></a>Csomópont ismételt társítása egy másik csomópont-konfigurációhoz

Hozzárendelhet egy csomópontot úgy, hogy az eredetileg hozzárendelt eltérő csomópont-konfigurációt használjon.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció** felügyelete alatt.
1. Az állapot-konfiguráció (DSC) lapon kattintson a **csomópontok** fülre.
1. A **csomópontok** lapon kattintson az újból hozzárendelni kívánt csomópont nevére.
1. A csomóponthoz tartozó lapon kattintson a **csomópont-konfiguráció kiosztása** elemre.

    ![Képernyőkép – a csomópont részletei lap kiemelése a csomópontok hozzárendelésének beállítása gombra](./media/automation-dsc-getting-started/AssignNode.png)

1. A csomópont-hozzárendelés konfigurálása lapon válassza ki azt a csomópont-konfigurációt, amelyhez hozzá szeretné rendelni a csomópontot, majd kattintson az **OK** gombra.

    ![Képernyőkép a csomópont-hozzárendelés konfigurációjának oldaláról](./media/automation-dsc-getting-started/AssignNodeConfig.png)

## <a name="unregister-a-node"></a>Csomópont regisztrációjának törlése

Ha már nem szeretné, hogy egy csomópontot állapot-konfigurációval lehessen felügyelni, törölheti a regisztrációját.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A bal oldalon kattintson az **összes erőforrás** elemre, majd az Automation-fiók nevére.
1. Az Automation-fiók lapon kattintson az **állapot konfigurációja (DSC)** elemre a **konfiguráció** felügyelete alatt.
1. Az állapot-konfiguráció (DSC) lapon kattintson a **csomópontok** fülre.
1. A **csomópontok** lapon kattintson annak a csomópontnak a nevére, amelynek a regisztrációját törölni szeretné.
1. A csomópont ablaktábláján kattintson a **Regisztráció törlése** elemre.

    ![Képernyőkép a csomópont részletei oldalról a regisztráció törlése gomb kiemelésével](./media/automation-dsc-getting-started/UnregisterNode.png)

## <a name="next-steps"></a>Következő lépések

- Az áttekintést lásd: [Azure Automation állapot konfigurációjának áttekintése](automation-dsc-overview.md).
- A környezetben lévő virtuális gépek funkciójának engedélyezéséhez tekintse meg [Azure Automation állapot konfigurációjának engedélyezése](automation-dsc-onboarding.md)című témakört.
- A PowerShell DSC megismeréséhez tekintse meg a [Windows PowerShell a kívánt állapot konfigurációjának áttekintése](/powershell/scripting/dsc/overview/overview)című témakört.
- A díjszabással kapcsolatos információkért lásd: [Azure Automation állapot konfigurációjának díjszabása](https://azure.microsoft.com/pricing/details/automation/).
- A PowerShell-parancsmagok leírása: [az. Automation](/powershell/module/az.automation).
