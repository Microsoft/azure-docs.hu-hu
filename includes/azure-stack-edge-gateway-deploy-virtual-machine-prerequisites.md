---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: f166413507afb9aff814eaddaade099d2e34ae68
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2021
ms.locfileid: "106554408"
---
Mielőtt üzembe helyezi a virtuális gépeket az Azure Stack Edge-eszközön, konfigurálnia kell az ügyfelet, hogy Azure Resource Manageron keresztül kapcsolódjon az eszközhöz Azure PowerShellon keresztül. Részletes útmutatásért lásd: [kapcsolódás Azure Resource Managerhoz az Azure stack Edge-eszközön](../articles/databox-online/azure-stack-edge-gpu-connect-resource-manager.md).

Győződjön meg arról, hogy a következő lépésekkel fér hozzá az eszközhöz az ügyfélről. Ezt a konfigurációt már elvégezte a Azure Resource Managerhoz való csatlakozáskor, és most ellenőrzi, hogy a konfiguráció sikeres volt-e. 

1. A következő parancs futtatásával ellenőrizze, hogy a Azure Resource Manager kommunikáció működik-e:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. A helyi eszköz API-jai a hitelesítéshez való meghívásához írja be a következőt: 

    `login-AzureRMAccount -EnvironmentName <Environment Name> -TenantId c0257de7-538f-415c-993a-1b87a031879d`

    Azure Resource Manager használatával történő kapcsolódáshoz adja meg a Felhasználónév *EdgeArmUser* és a jelszavát.

1. Ha a Kubernetes-hez konfigurálta a számítást, kihagyhatja ezt a lépést. Ellenkező esetben ellenőrizze, hogy a hálózati adaptert engedélyezte-e a számításhoz a következő módon: 

   a. A helyi felhasználói felületen lépjen a **számítási** beállítások elemre.  
   b. Válassza ki a virtuális kapcsoló létrehozásához használni kívánt hálózati adaptert. A létrehozott virtuális gépeket egy ehhez a porthoz és a társított hálózathoz csatolt virtuális kapcsolóhoz csatolja a rendszer. Ügyeljen arra, hogy olyan hálózatot válasszon, amely megfelel a virtuális géphez használt IP-címnek.  

    ![Képernyőfelvétel a számítási konfiguráció hálózati beállításai panelről.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

   c. A hálózati adapteren a **számítás engedélyezése** területen válassza az **Igen** lehetőséget. Azure Stack Edge létrehoz és felügyel egy virtuális kapcsolót, amely megfelel az adott hálózati adapternek. Jelenleg ne adjon meg adott IP-címeket a Kubernetes. A számítás engedélyezése több percet is igénybe vehet.

    > [!NOTE]
    > Ha GPU-alapú virtuális gépeket hoz létre, válasszon ki egy, az internethez csatlakozó hálózati adaptert. Ez lehetővé teszi a GPU-bővítmények telepítését az eszközön.