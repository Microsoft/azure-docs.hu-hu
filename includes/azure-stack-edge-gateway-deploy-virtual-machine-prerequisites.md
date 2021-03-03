---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 71d5a910e36762d096763c4f45a13cbdad47414d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101730625"
---
Mielőtt üzembe helyezi a virtuális gépeket az Azure Stack Edge-eszközön, konfigurálnia kell az ügyfelet, hogy Azure Resource Manageron keresztül kapcsolódjon az eszközhöz Azure PowerShellon keresztül. Részletes útmutatásért lásd: [kapcsolódás Azure Resource Managerhoz az Azure stack Edge-eszközön](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Győződjön meg arról, hogy a következő lépésekkel fér hozzá az eszközhöz az ügyfélről. Ezt a konfigurációt már elvégezte a Azure Resource Managerhoz való csatlakozáskor, és most ellenőrzi, hogy a konfiguráció sikeres volt-e. 

1. A következő parancs futtatásával ellenőrizze, hogy a Azure Resource Manager kommunikáció működik-e:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. A helyi eszköz API-jai a hitelesítéshez való meghívásához írja be a következőt: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Azure Resource Manager használatával történő kapcsolódáshoz adja meg a Felhasználónév *EdgeARMuser* és a jelszavát.

1. Ha a Kubernetes-hez konfigurálta a számítást, kihagyhatja ezt a lépést. Ellenkező esetben ellenőrizze, hogy a hálózati adaptert engedélyezte-e a számításhoz a következő módon: 

   a. A helyi felhasználói felületen lépjen a **számítási** beállítások elemre.  
   b. Válassza ki a virtuális kapcsoló létrehozásához használni kívánt hálózati adaptert. A létrehozott virtuális gépeket egy ehhez a porthoz és a társított hálózathoz csatolt virtuális kapcsolóhoz csatolja a rendszer. Ügyeljen arra, hogy olyan hálózatot válasszon, amely megfelel a virtuális géphez használt IP-címnek.  

    ![Képernyőfelvétel a számítási konfiguráció hálózati beállításai panelről.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. A hálózati adapteren a **számítás engedélyezése** területen válassza az **Igen** lehetőséget. Azure Stack Edge létrehoz és felügyel egy virtuális kapcsolót, amely megfelel az adott hálózati adapternek. Jelenleg ne adjon meg adott IP-címeket a Kubernetes. A számítás engedélyezése több percet is igénybe vehet.

    > [!NOTE]
    > Ha GPU-alapú virtuális gépeket hoz létre, válasszon ki egy, az internethez csatlakozó hálózati adaptert. Ez lehetővé teszi a GPU-bővítmények telepítését az eszközön.


