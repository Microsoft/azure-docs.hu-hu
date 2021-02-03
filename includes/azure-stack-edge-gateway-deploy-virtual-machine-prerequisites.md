---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/15/2021
ms.author: alkohli
ms.openlocfilehash: 8f3031669723cd61715c12a42f99869ac0eaf3bc
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99500436"
---
Mielőtt üzembe helyezi a virtuális gépeket az Azure Stack Edge-eszközön, konfigurálnia kell az ügyfelet, hogy Azure Resource Manageron keresztül kapcsolódjon az eszközhöz Azure PowerShellon keresztül. A részletes lépésekért lépjen a [kapcsolódás Azure Resource Managerhoz az Azure stack Edge-eszközön](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md).

Ellenőrizze, hogy a következő lépések használhatók-e az eszköz ügyfélről való eléréséhez. (Ezt a konfigurációt Azure Resource Managerhoz való csatlakozáskor tette meg. Most már csak azt ellenőrzi, hogy a konfiguráció sikeres volt-e.) 

1. Ellenőrizze, hogy a Azure Resource Manager kommunikáció működik-e. Adja meg a következőt:     

    ```powershell
    Add-AzureRmEnvironment -Name <Environment Name> -ARMEndpoint "https://management.<appliance name>.<DNSDomain>"
    ```

1. A hitelesítéshez hívja meg a helyi eszközök API-jait. Adja meg a következőt: 

    `login-AzureRMAccount -EnvironmentName <Environment Name>`

    Adja meg a felhasználónevet és a jelszót a *EdgeARMuser* Azure Resource Manager használatával történő kapcsolódáshoz.

1. Ha a Kubernetes-hez konfigurálta a **számítást** , kihagyhatja ezt a lépést. Győződjön meg arról, hogy engedélyezte a hálózati adaptert a számítási feladatokhoz. A helyi felhasználói felületen lépjen a **számítási** beállítások elemre. Válassza ki a virtuális kapcsoló létrehozásához használni kívánt hálózati adaptert. A létrehozott virtuális gépek az ehhez a porthoz és a társított hálózathoz csatolt virtuális kapcsolókhoz lesznek csatolva. Ügyeljen arra, hogy olyan hálózatot válasszon, amely megfelel a virtuális gép által használt IP-címnek.  

    ![A számítási beállítások engedélyezését bemutató képernyőkép.](../articles/databox-online/media/azure-stack-edge-gpu-deploy-virtual-machine-templates/enable-compute-setting.png)

    A hálózati adapteren engedélyezze a számítást. Azure Stack Edge létrehoz és felügyel egy, az adott hálózati adapternek megfelelő virtuális kapcsolót. Jelenleg ne adjon meg adott IP-címeket a Kubernetes. A számítás engedélyezése több percet is igénybe vehet.

    > [!NOTE]
    > Ha GPU-alapú virtuális gépeket hoz létre, válasszon ki egy, az internethez csatlakozó hálózati adaptert. Ez lehetővé teszi a GPU-bővítmények telepítését az eszközön.


