---
title: Azure Stack Edge Pro GPU számítási felügyelet | Microsoft Docs
description: Leírja, hogyan kezelheti a peremhálózati számítási beállításokat, például az triggert, a modulokat, a számítási konfiguráció megtekintését, a konfiguráció eltávolítását a Azure Stack Edge Pro GPU-n lévő Azure Portal.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 01/27/2021
ms.author: alkohli
ms.openlocfilehash: 4c4fbef807d31e03a79f80db7fd29580074fb8bd
ms.sourcegitcommit: 4e70fd4028ff44a676f698229cb6a3d555439014
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2021
ms.locfileid: "98955454"
---
# <a name="manage-compute-on-your-azure-stack-edge-pro-gpu"></a>Számítások kezelése a Azure Stack Edge Pro GPU-val

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez a cikk bemutatja, hogyan kezelheti a számításokat IoT Edge szolgáltatáson keresztül az Azure Stack Edge Pro GPU-eszközön. A számítást a Azure Portal vagy a helyi webes felületen keresztül kezelheti. A Azure Portal segítségével kezelheti a modulokat, az eseményindítókat és a IoT Edge konfigurációt, valamint a helyi webes felhasználói felületet a számítási hálózati beállítások kezeléséhez.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eseményindítók kezelése
> * IoT Edge konfiguráció kezelése


## <a name="manage-triggers"></a>Eseményindítók kezelése

Eseménynek nevezzük a felhőalapú környezetben vagy az eszközén bekövetkező történéseket, amelyekkel kapcsolatban érdemes lehet cselekedni. Egy fájl létrehozása egy megosztásban például eseménynek számít. A trigger az, ami az eseményt kiváltja. A Azure Stack Edge Pro esetében az eseményindítók a fájlok eseményeire vagy az ütemtervre is reagálnak.

- **Fájl**: ezek az eseményindítók olyan fájlok eseményeire reagálnak, mint például egy fájl létrehozása, egy fájl módosítása.
- **Ütemezve**: ezek az eseményindítók olyan ütemezésre reagálnak, amelyet a kezdési dátummal, a kezdési időponttal és az Ismétlési intervallummal lehet meghatározni.


### <a name="add-a-trigger"></a>Eseményindító hozzáadása

Trigger létrehozásához hajtsa végre az alábbi lépéseket a Azure Portal.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, majd lépjen a **IoT Edge** elemre. Lépjen az **Eseményindítók** elemre, és válassza a parancssáv **+ eseményindító hozzáadása** parancsát.

    ![Válassza az trigger hozzáadása lehetőséget](media/azure-stack-edge-j-series-manage-compute/add-trigger-1m.png)

2. Az **trigger hozzáadása** panelen adjon meg egy egyedi nevet az trigger számára.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Válassza ki az trigger **típusát** . Válassza a **fájl** lehetőséget, ha az eseményindító válaszol egy fájl eseményre. Válassza az **ütemezett** lehetőséget, ha azt szeretné, hogy a trigger meghatározott időpontban induljon el, és fusson egy megadott ismétlési intervallumban. A választástól függően különböző beállítások jelennek meg.

    - **Fájl trigger** – válasszon egy csatlakoztatott megosztást a legördülő listából. Ha egy fájl eseményt indítanak ebben a megosztásban, az eseményindító egy Azure-függvényt hív meg.

        ![SMB-megosztás hozzáadása](media/azure-stack-edge-j-series-manage-compute/add-file-trigger.png)

    - **Ütemezett trigger** – Itt adhatja meg a kezdési dátumot és időpontot, valamint az Ismétlési időközt órában, percben vagy másodpercben. Adja meg a témakör nevét is. A témakör lehetővé teszi, hogy rugalmasan irányítsa át a triggert az eszközön üzembe helyezett modulra.

        Példa útvonal-karakterláncra: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` .

        ![NFS-megosztás hozzáadása](media/azure-stack-edge-j-series-manage-compute/add-scheduled-trigger.png)

4. A trigger létrehozásához válassza a **Hozzáadás** lehetőséget. Egy értesítés azt mutatja, hogy az trigger létrehozása folyamatban van. Az trigger létrehozása után a panel frissül, hogy tükrözze az új triggert.
 
    ![Frissített triggerek listája](media/azure-stack-edge-j-series-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Trigger törlése

Az trigger törléséhez hajtsa végre az alábbi lépéseket a Azure Portal.

1. Az eseményindítók listájából válassza ki a törölni kívánt eseményindítót.

    ![Trigger kiválasztása](media/azure-stack-edge-j-series-manage-compute/delete-trigger-1.png)

2. Kattintson a jobb gombbal, majd válassza a **Törlés** lehetőséget.

    ![Törlés kiválasztása](media/azure-stack-edge-j-series-manage-compute/delete-trigger-2.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

    ![Törlés megerősítése](media/azure-stack-edge-j-series-manage-compute/delete-trigger-3.png)

Az eseményindítók listájának frissítése, hogy tükrözze a törlést.

## <a name="manage-iot-edge-configuration"></a>IoT Edge konfiguráció kezelése

A Azure Portal használatával megtekintheti a számítási konfigurációt, eltávolíthatja a meglévő számítási konfigurációkat, vagy frissítheti a számítási konfigurációt, hogy szinkronizálja a IoT-eszköz hozzáférési kulcsait, és IoT Edge eszközét az Azure Stack Edge Pro-hoz.

### <a name="view-iot-edge-configuration"></a>IoT Edge konfiguráció megtekintése

Az eszköz IoT Edge-konfigurációjának megtekintéséhez hajtsa végre a következő lépéseket a Azure Portal.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, majd lépjen a **IoT Edge** elemre. Ha IoT Edge szolgáltatás engedélyezve van az eszközön, az áttekintő oldal azt jelzi, hogy a IoT Edge szolgáltatás működik.

    ![Számítási nézet kiválasztása](media/azure-stack-edge-j-series-manage-compute/view-compute-1.png)

2. A **Tulajdonságok** elemre kattintva megtekintheti az eszköz IoT Edge konfigurációját. A számítás konfigurálásakor létrehozott egy IoT Hub erőforrást. A IoT Hub erőforrás alatt egy IoT-eszköz és egy IoT Edge-eszköz konfigurálva van. A IoT Edge eszközön csak a Linux-modulok futtathatók.

    ![Konfiguráció megtekintése](media/azure-stack-edge-j-series-manage-compute/view-compute-2.png)


### <a name="remove-iot-edge-service"></a>IoT Edge szolgáltatás eltávolítása

Az eszköz meglévő IoT Edge-konfigurációjának eltávolításához hajtsa végre az alábbi lépéseket a Azure Portal.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, majd lépjen a **IoT Edge** elemre. Nyissa meg az **áttekintést** , és válassza a parancssáv **Eltávolítás** elemét.

    ![Válassza a számítás eltávolítása lehetőséget.](media/azure-stack-edge-j-series-manage-compute/remove-compute-1.png)

2. Ha eltávolítja a IoT Edge szolgáltatást, a művelet visszafordíthatatlan, ezért nem vonható vissza. A létrehozott modulok és triggerek is törlődni fognak. Az eszközt újra kell konfigurálnia, ha IoT Edge újra kell használnia. Ha a rendszer megerősítést kér, kattintson **az OK gombra**.

    ![Válassza a 2. számítás törlése elemet.](media/azure-stack-edge-j-series-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>IoT-eszköz szinkronizálása és IoT Edge eszköz-hozzáférési kulcsok

Ha a Azure Stack Edge Pro-ban konfigurálja a számítást, egy IoT-eszköz és egy IoT Edge eszköz jön létre. Ezeket az eszközöket a rendszer automatikusan hozzárendeli a szimmetrikus hozzáférési kulcsokhoz. Ajánlott biztonsági eljárásként ezeket a kulcsokat rendszeresen elforgatják a IoT Hub szolgáltatáson keresztül.

A kulcsok elforgatásához lépjen a létrehozott IoT Hub szolgáltatásra, és válassza ki a IoT eszközt vagy a IoT Edge eszközt. Minden eszközhöz tartozik egy elsődleges elérési kulcs és egy másodlagos elérési kulcs. Rendelje hozzá az elsődleges hozzáférési kulcsot a másodlagos elérési kulcshoz, majd az elsődleges hozzáférési kulcs újragenerálása.

Ha elforgatták a IoT eszközt és IoT Edge eszköz kulcsait, akkor a legújabb hozzáférési kulcsok beszerzéséhez frissítenie kell a Azure Stack Edge Pro konfigurációját. A Sync segítségével az eszköz beolvassa a IoT-eszköz és IoT Edge eszköz legújabb kulcsait. Azure Stack Edge Pro csak az elsődleges hozzáférési kulcsokat használja.

Az eszközhöz tartozó hozzáférési kulcsok szinkronizálásához hajtsa végre az alábbi lépéseket a Azure Portal.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, majd lépjen a **IoT Edge számítás** elemre. Lépjen az **Áttekintés** elemre, és válassza a parancssáv **konfigurációjának frissítése** lehetőséget.

    ![Konfiguráció frissítése lehetőség kiválasztása](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-1.png)

2. Ha a rendszer megerősítést kér, válassza az **Igen** lehetőséget.

    ![Ha a rendszer kéri, válassza az Igen lehetőséget](media/azure-stack-edge-j-series-manage-compute/refresh-configuration-2.png)

3. A szinkronizálást követően zárja be a párbeszédpanelt.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan lehet [elhárítani a Azure stack Edge Pro](azure-stack-edge-gpu-troubleshoot.md)-t.
