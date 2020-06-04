---
title: Azure Stack Edge számítási felügyelet | Microsoft Docs
description: Leírja, hogyan kezelheti a peremhálózati számítási beállításokat, például az triggert, a modulokat, a számítási konfiguráció megtekintését, a konfiguráció eltávolítását az Azure Stack Edge-beli Azure Portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 05/20/2019
ms.author: alkohli
ms.openlocfilehash: 9d34bd1750b798407b493dcde01b04118d66a2d5
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/03/2020
ms.locfileid: "84339824"
---
# <a name="manage-compute-on-your-azure-stack-edge"></a>Számítások kezelése az Azure Stack Edge-ben

Ez a cikk bemutatja, hogyan kezelheti a számításokat az Azure Stack Edge-ben. A számítást a Azure Portal vagy a helyi webes felületen keresztül kezelheti. A Azure Portal segítségével kezelheti a modulokat, az eseményindítókat és a számítási konfigurációt, valamint a helyi webes felhasználói felületet a számítási beállítások kezeléséhez.

Ebben a cikkben az alábbiakkal ismerkedhet meg:

> [!div class="checklist"]
> * Eseményindítók kezelése
> * Számítási konfiguráció kezelése


## <a name="manage-triggers"></a>Eseményindítók kezelése

Az események olyan dolgok, amelyek az Ön felhőalapú környezetében vagy az eszközön történnek, amelyre szükség lehet. Ha például egy fájl egy megosztásban jön létre, akkor ez egy esemény. Az eseményindítók megemelik az eseményeket. Az Azure Stack Edge esetében az eseményindítók a fájlok eseményeire vagy az ütemtervre is reagálnak.

- **Fájl**: ezek az eseményindítók olyan fájlok eseményeire reagálnak, mint például egy fájl létrehozása, egy fájl módosítása.
- **Ütemezve**: ezek az eseményindítók olyan ütemezésre reagálnak, amelyet a kezdési dátummal, a kezdési időponttal és az Ismétlési intervallummal lehet meghatározni.


### <a name="add-a-trigger"></a>Eseményindító hozzáadása

Trigger létrehozásához hajtsa végre az alábbi lépéseket a Azure Portal.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, és válassza az **Edge számítási > trigger**lehetőséget. Válassza az **+ trigger hozzáadása** parancsot a parancssorban.

    ![Válassza az trigger hozzáadása lehetőséget](media/azure-stack-edge-manage-compute/add-trigger-1.png)

2. Az **trigger hozzáadása** panelen adjon meg egy egyedi nevet az trigger számára.
    
    <!--Trigger names can only contain numbers, lowercase letters, and hyphens. The share name must be between 3 and 63 characters long and begin with a letter or a number. Each hyphen must be preceded and followed by a non-hyphen character.-->

3. Válassza ki az trigger **típusát** . Válassza a **fájl** lehetőséget, ha az eseményindító válaszol egy fájl eseményre. Válassza az **ütemezett** lehetőséget, ha azt szeretné, hogy a trigger meghatározott időpontban induljon el, és fusson egy megadott ismétlési intervallumban. A választástól függően különböző beállítások jelennek meg.

    - **Fájl trigger** – válasszon egy csatlakoztatott megosztást a legördülő listából. Ha egy fájl eseményt indítanak ebben a megosztásban, az eseményindító egy Azure-függvényt hív meg.

        ![SMB-megosztás hozzáadása](media/azure-stack-edge-manage-compute/add-file-trigger.png)

    - **Ütemezett trigger** – Itt adhatja meg a kezdési dátumot és időpontot, valamint az Ismétlési időközt órában, percben vagy másodpercben. Adja meg a témakör nevét is. A témakör lehetővé teszi, hogy rugalmasan irányítsa át a triggert az eszközön üzembe helyezett modulra.

        Példa útvonal-karakterláncra: `"route3": "FROM /* WHERE topic = 'topicname' INTO BrokeredEndpoint("modules/modulename/inputs/input1")"` .

        ![NFS-megosztás hozzáadása](media/azure-stack-edge-manage-compute/add-scheduled-trigger.png)

4. A trigger létrehozásához válassza a **Hozzáadás** lehetőséget. Egy értesítés azt mutatja, hogy az trigger létrehozása folyamatban van. Az trigger létrehozása után a panel frissül, hogy tükrözze az új triggert.
 
    ![Frissített triggerek listája](media/azure-stack-edge-manage-compute/add-trigger-2.png)

### <a name="delete-a-trigger"></a>Trigger törlése

Az trigger törléséhez hajtsa végre az alábbi lépéseket a Azure Portal.

1. Az eseményindítók listájából válassza ki a törölni kívánt eseményindítót.

    ![Trigger kiválasztása](media/azure-stack-edge-manage-compute/add-trigger-1.png)

2. Kattintson a jobb gombbal, majd válassza a **Törlés**lehetőséget.

    ![Törlés kiválasztása](media/azure-stack-edge-manage-compute/add-trigger-1.png)

3. Ha a rendszer megerősítést kér, kattintson az **Igen** gombra.

    ![Törlés megerősítése](media/azure-stack-edge-manage-compute/add-trigger-1.png)

Az eseményindítók listájának frissítése, hogy tükrözze a törlést.

## <a name="manage-compute-configuration"></a>Számítási konfiguráció kezelése

A Azure Portal használatával megtekintheti a számítási konfigurációt, eltávolíthatja a meglévő számítási konfigurációkat, vagy frissítheti a számítási konfigurációt, hogy szinkronizálja a IoT-eszköz hozzáférési kulcsait, és IoT Edge eszközét az Azure Stack Edge számára.

### <a name="view-compute-configuration"></a>Számítási konfiguráció megtekintése

Az eszköz számítási konfigurációjának megtekintéséhez hajtsa végre a következő lépéseket a Azure Portalban.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, és válassza az **Edge számítási > modulok**lehetőséget. Válassza ki a **számítás megjelenítése** elemet a parancssáv sávon.

    ![Számítási nézet kiválasztása](media/azure-stack-edge-manage-compute/view-compute-1.png)

2. Jegyezze fel az eszköz számítási konfigurációját. A számítás konfigurálásakor létrehozott egy IoT Hub erőforrást. A IoT Hub erőforrás alatt egy IoT-eszköz és egy IoT Edge-eszköz konfigurálva van. A IoT Edge eszközön csak a Linux-modulok futtathatók.

    ![Konfiguráció megtekintése](media/azure-stack-edge-manage-compute/view-compute-2.png)


### <a name="remove-compute-configuration"></a>Számítási konfiguráció eltávolítása

A Azure Portal az alábbi lépéseket végrehajtva távolítsa el az eszköz meglévő, peremhálózati számítási konfigurációját.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, és válassza az **Edge számítás > első lépések**lehetőséget. Válassza a parancssorból a **számítás eltávolítása** lehetőséget.

    ![Válassza a számítás eltávolítása lehetőséget.](media/azure-stack-edge-manage-compute/remove-compute-1.png)

2. Ha eltávolítja a számítási konfigurációt, újra kell konfigurálnia az eszközt abban az esetben, ha újra kell használnia a számítást. Ha a rendszer megerősítést kér, válassza az **Igen**lehetőséget.

    ![Válassza a számítás eltávolítása lehetőséget.](media/azure-stack-edge-manage-compute/remove-compute-2.png)

### <a name="sync-up-iot-device-and-iot-edge-device-access-keys"></a>IoT-eszköz szinkronizálása és IoT Edge eszköz-hozzáférési kulcsok

Ha az Azure Stack Edge-ben konfigurálja a számítást, létrejön egy IoT-eszköz és egy IoT Edge-eszköz. Ezeket az eszközöket a rendszer automatikusan hozzárendeli a szimmetrikus hozzáférési kulcsokhoz. Ajánlott biztonsági eljárásként ezeket a kulcsokat rendszeresen elforgatják a IoT Hub szolgáltatáson keresztül.

A kulcsok elforgatásához lépjen a létrehozott IoT Hub szolgáltatásra, és válassza ki a IoT eszközt vagy a IoT Edge eszközt. Minden eszközhöz tartozik egy elsődleges elérési kulcs és egy másodlagos elérési kulcs. Rendelje hozzá az elsődleges hozzáférési kulcsot a másodlagos elérési kulcshoz, majd az elsődleges hozzáférési kulcs újragenerálása.

Ha a IoT-eszköz és a IoT Edge eszköz kulcsait elforgatták, akkor frissítenie kell az Azure Stack Edge konfigurációját a legújabb hozzáférési kulcsok beszerzéséhez. A Sync segítségével az eszköz beolvassa a IoT-eszköz és IoT Edge eszköz legújabb kulcsait. Azure Stack Edge csak az elsődleges hozzáférési kulcsokat használja.

Az eszközhöz tartozó hozzáférési kulcsok szinkronizálásához hajtsa végre az alábbi lépéseket a Azure Portal.

1. A Azure Portal nyissa meg a Azure Stack Edge-erőforrást, és válassza az **Edge számítás > első lépések**lehetőséget. Válassza ki a parancssáv **konfigurációjának frissítése** elemet.

    ![Konfiguráció frissítése lehetőség kiválasztása](media/azure-stack-edge-manage-compute/refresh-configuration-1.png)

2. Ha a rendszer megerősítést kér, válassza az **Igen** lehetőséget.

     ![Ha a rendszer kéri, válassza az Igen lehetőséget](media/azure-stack-edge-manage-compute/refresh-configuration-2.png)

3. A szinkronizálást követően zárja be a párbeszédpanelt.

## <a name="next-steps"></a>Következő lépések

- Ismerje meg, hogyan [kezelheti az Edge számítási hálózatot Azure Portal használatával](azure-stack-edge-extend-compute-access-modules.md).
