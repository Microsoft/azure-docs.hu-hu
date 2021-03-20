---
title: Azure rövid útmutató – virtuális gép konfigurálása a kívánt állapot-konfigurációval | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan konfigurálhatja a virtuális gépet a kívánt állapot-konfigurációval.
services: automation
ms.subservice: dsc
keywords: dsc, konfigurálás, automatizálás
ms.date: 11/06/2018
ms.topic: quickstart
ms.custom: mvc
ms.openlocfilehash: e7fec2bee61844ac294e5463bd5bc88ec3fb5e98
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86186078"
---
# <a name="configure-a-vm-with-desired-state-configuration"></a>Virtuális gép konfigurálása a kívánt állapot-konfigurációval

A Azure Automation állapot-konfiguráció engedélyezésével felügyelheti és figyelheti a Windows-és Linux-kiszolgálók konfigurációit a kívánt állapot-konfiguráció (DSC) használatával. A kívánt konfigurációtól elsodródó konfigurációk azonosíthatók vagy automatikusan kijavíthatók. Ez a rövid útmutató a Linux rendszerű virtuális gépek engedélyezésével és a LAMP-verem üzembe helyezésével Azure Automation állapotának konfigurálásával végezhető el.

## <a name="prerequisites"></a>Előfeltételek

Ennek a rövid útmutatónak az elvégzéséhez a következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).
* Egy Azure Automation-fiókra. Azure Automation futtató fiók létrehozásával kapcsolatos információkért tekintse meg az [Azure-beli futtató fiókkal](./manage-runas-account.md) kapcsolatos részt.
* Red Hat Enterprise Linux, CentOS vagy Oracle Linux rendszert futtató Azure Resource Manager virtuális gép (klasszikus). A virtuális gépek létrehozásával kapcsolatos útmutatásért tekintse meg [az első Linux virtuális gép Azure Portallal való létrehozását](../virtual-machines/linux/quick-create-portal.md) bemutató cikket.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba
Jelentkezzen be az Azure-ba https://portal.azure.com .

## <a name="enable-a-virtual-machine"></a>Virtuális gép engedélyezése

Számos különböző módszerrel engedélyezheti a gép számára az állapot-konfigurálási funkciót. Ebből a rövid útmutatóból megtudhatja, hogyan engedélyezheti a virtuális gép szolgáltatását egy Automation-fiók használatával. A gépek állapot-konfigurálásának engedélyezéséhez további információkat tudhat meg a [számítógépek Azure Automation állapotának konfigurálásával történő felügyeletének engedélyezéséhez](./automation-dsc-onboarding.md).

1. Az Azure Portal bal oldali panelén válassza az **Automation-fiókok** elemet. Ha a bal oldali ablaktáblában nem látható, kattintson a **minden szolgáltatás** elemre, és keresse meg az eredményül kapott nézetet.
1. Válasszon egy Automation-fiókot a listában.
1. Az Automation-fiók bal oldali panelén válassza az **Állapotkonfiguráció (DSC)** elemet.
2. Kattintson a **Hozzáadás** elemre a virtuális gép kiválasztását lehetővé tevő oldal megnyitásához.
3. Keresse meg azt a virtuális gépet, amely számára engedélyezni kívánja a DSC-t. A keresőmező és a szűrési lehetőségek használatával kereshet meg egy adott virtuális gépet.
4. Kattintson a virtuális gépre, majd a **kapcsolat** lehetőségre.
5. Válassza ki a virtuális géphez megfelelő DSC-beállításokat. Ha már előkészített egy konfigurációt, megadhatja azt `Node Configuration Name` . Beállíthatja a [konfigurációs módot](/powershell/scripting/dsc/managing-nodes/metaConfig) a gép konfigurációs viselkedésének vezérléséhez.
6. Kattintson az **OK** gombra. Amíg a DSC-bővítmény telepítve van a virtuális gépen, az állapot a következőképpen jelenik meg: `Connecting` .

![Azure-beli virtuális gép engedélyezése DSC-hez](./media/automation-quickstart-dsc-configuration/dsc-onboard-azure-vm.png)

## <a name="import-modules"></a>Modulok importálása

A modulok DSC-erőforrásokat tartalmaznak, és számos megtalálható a [PowerShell-Galéria](https://www.powershellgallery.com). A konfigurációban használt összes erőforrást a fordítás előtt importálni kell az Automation-fiókba. Ebben az oktatóanyagban az **nx** nevű modulra van szükség.

1. Az Automation-fiók bal oldali ablaktábláján válassza a **modulok gyűjtemény** elemet a **megosztott erőforrások** területen.
1. Keresse meg az importálni kívánt modult a név egy részének beírásával: `nx` .
1. Kattintson az importálni kívánt modulra.
1. Kattintson az **Importálás** gombra.

![DSC-modul importálása](./media/automation-quickstart-dsc-configuration/dsc-import-module-nx.png)

## <a name="import-the-configuration"></a>A konfiguráció importálása

Ez a rövid útmutató a gépen Apache HTTP-kiszolgálót, MySQL-t és PHP-t konfiguráló DSC-konfigurációt használ. Lásd: [DSC-konfigurációk](/powershell/scripting/dsc/configurations/configurations).

Egy szövegszerkesztőben írja be a következőt, és mentse helyileg **AMPServer.ps1ként**.

```powershell-interactive
configuration LAMPServer {
   Import-DSCResource -module "nx"

   Node localhost {

        $requiredPackages = @("httpd","mod_ssl","php","php-mysql","mariadb","mariadb-server")
        $enabledServices = @("httpd","mariadb")

        #Ensure packages are installed
        ForEach ($package in $requiredPackages){
            nxPackage $Package{
                Ensure = "Present"
                Name = $Package
                PackageManager = "yum"
            }
        }

        #Ensure daemons are enabled
        ForEach ($service in $enabledServices){
            nxService $service{
                Enabled = $true
                Name = $service
                Controller = "SystemD"
                State = "running"
            }
        }
   }
}
```

A konfiguráció importálása:

1. Az Automation-fiók bal oldali panelén válassza az **Állapotkonfiguráció (DSC)** elemet, majd kattintson a **Konfigurációk** lapra.
2. Kattintson a **+ Hozzáadás** gombra.
3. Válassza ki az előző lépésben mentett konfigurációs fájlt.
4. Kattintson az **OK** gombra.

## <a name="compile-a-configuration"></a>Konfiguráció fordítása

Ahhoz, hogy hozzá lehessen rendelni egy csomópontot, le kell fordítania egy DSC-konfigurációt egy csomópont-konfigurációra (MOF-dokumentum). A fordítás érvényesíti a konfigurációt, és lehetővé teszi a paraméterértékek bevitelét. A konfiguráció fordításával kapcsolatos további tudnivalókért lásd: [konfigurációk fordítása az állapot-konfigurációban](automation-dsc-compile.md).

1. Az Automation-fiók bal oldali ablaktábláján válassza az **állapot konfigurálása (DSC)** lehetőséget, majd kattintson a **konfigurációk** fülre.
1. Válassza ki a konfigurációt `LAMPServer` .
1. A menüpontok között válassza a **fordítás** lehetőséget, majd kattintson az **Igen** gombra.
1. A konfiguráció nézetben megjelenik egy új fordítási feladatok várólistára helyezése. Amikor a feladat sikeresen elkészül, továbbléphet a következő lépésre. Ha bármilyen hiba merül fel, a részletekért kattintson a fordítási feladatra.

## <a name="assign-a-node-configuration"></a>Csomópont-konfiguráció hozzárendelése

Lefordított csomópont-konfigurációt hozzárendelhet egy DSC-csomóponthoz. A hozzárendelés alkalmazza a konfigurációt a gépre, és figyeli vagy automatikusan kijelöli az adott konfigurációtól való eltolódást.

1. Az Automation-fiók bal oldali ablaktábláján válassza az **állapot-konfiguráció (DSC)** elemet, majd kattintson a **csomópontok** fülre.
1. Válassza ki azt a csomópontot, amelyhez hozzá szeretné rendelni a konfigurációt.
1. Kattintson a **Csomópont-konfiguráció hozzárendelése** elemre
1. Válassza ki a csomópont `LAMPServer.localhost` -konfigurációt, és kattintson **az OK** gombra. Az állapot-konfiguráció most hozzárendeli a lefordított konfigurációt a csomóponthoz, és a csomópont állapota módosul `Pending` . A következő rendszeres vizsgálat során a csomópont lekéri a konfigurációt, alkalmazza azt és a jelentések állapotát. A csomópont beállításaitól függően akár 30 percet is igénybe vehet, amíg a csomópont beolvassa a konfigurációt. 
1. Az azonnali ellenőrzés kényszerítéséhez futtassa a következő parancsot helyileg a Linux rendszerű virtuális gépen: `sudo /opt/microsoft/dsc/Scripts/PerformRequiredConfigurationChecks.py`

![Csomópont-konfiguráció hozzárendelése](./media/automation-quickstart-dsc-configuration/dsc-assign-node-configuration.png)

## <a name="view-node-status"></a>Csomópont állapotának megtekintése

Az Automation-fiókban megtekintheti az állapot konfigurációjának összes felügyelt csomópontjának állapotát. Az adatok megjelenítéséhez válassza az **állapot konfigurálása (DSC)** lehetőséget, majd kattintson a **csomópontok** lapra. A megjelenítést állapot, csomópont-konfiguráció vagy név szerinti keresés alapján szűrheti.

![DSC-csomópont állapota](./media/automation-quickstart-dsc-configuration/dsc-node-status.png)

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban engedélyezte a Linux rendszerű virtuális gépet az állapot konfigurálásához, létrehozta a LAMP-verem konfigurációját, és telepítette a konfigurációt a virtuális gépre. Ha szeretné megtudni, hogyan használhatja Azure Automation állapot konfigurációját a folyamatos üzembe helyezés engedélyezéséhez, folytassa a cikket:

> [!div class="nextstepaction"]
> [Folyamatos üzembe helyezés beállítása a Chocolatey segítségével](./automation-dsc-cd-chocolatey.md)
