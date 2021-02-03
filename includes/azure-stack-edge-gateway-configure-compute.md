---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: ea822acf5b7141d26a3776d7ed59630bf7ac9532
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2021
ms.locfileid: "99524927"
---
Ha az Azure Stack Edge Pro-ban szeretné beállítani a számítást, IoT Hub-erőforrást fog létrehozni a Azure Portal használatával.

1. Az Azure Stack Edge-erőforrás Azure Portal lépjen az **Áttekintés** elemre, és válassza a **IoT Edge** lehetőséget.

   ![Ismerkedés a számítási feladatokkal](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. A **IoT Edge szolgáltatás engedélyezése** lapon válassza a **Hozzáadás** lehetőséget.

   ![Számítás konfigurálása](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. Az **Edge-számítás konfigurálása panelen adja** meg a következő adatokat:
   
    |Mező  |Érték  |
    |---------|---------|
    |Előfizetés     |Válasszon egy előfizetést a IoT Hub erőforráshoz. Használhatja ugyanazt az előfizetést, mint amelyet az Azure Stack Edge-erőforrás használ.         |
    |Erőforráscsoport     |Válasszon ki egy erőforráscsoportot a IoT Hub erőforráshoz. Használhatja ugyanazt az erőforráscsoportot, mint amelyet az Azure Stack Edge-erőforrás használ.         |
    |IoT Hub     | Válasszon az **új** vagy a **meglévő** listából. <br> Alapértelmezés szerint IoT-erőforrás létrehozásához a rendszer Standard szintet (S1) használ. Ingyenes szintű IoT-erőforrás használatához hozzon létre egyet, majd válassza ki a létrehozott erőforrást. <br> A IoT Hub erőforrás minden esetben ugyanazt az előfizetést és erőforráscsoportot használja, amelyet az Azure Stack Edge-erőforrás használ.     |
    |Name     |Fogadja el az alapértelmezett nevet, vagy adja meg a IoT Hub erőforrás nevét.         |

   ![Ismerkedés a 2. számítási feladatokkal](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. A beállítások befejezését követően válassza a **felülvizsgálat + létrehozás** lehetőséget. Tekintse át a IoT Hub erőforrás beállításait, és válassza a **Létrehozás** lehetőséget.

   Egy IoT Hub erőforrás létrehozása több percet vesz igénybe. Az erőforrás létrehozása után az **Áttekintés** azt jelzi, hogy a IoT Edge szolgáltatás már fut.

   ![Ismerkedés a 3. számítási feladatokkal](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. A peremhálózati számítási szerepkör konfigurálásának megerősítéséhez lépjen **IoT Edge > tulajdonságai** elemre.

   ![Ismerkedés a 4. számítási feladatokkal](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Amikor a peremhálózati eszközön beállította a peremhálózat számítási szerepkört, két eszközt hoz létre: egy IoT eszközt és egy IoT Edge eszközt. Mindkét eszköz megtekinthető a IoT Hub erőforrásban. Ezen a IoT Edge eszközön a IoT Edge futtatókörnyezet is fut. Ezen a ponton csak a Linux platform érhető el a IoT Edge eszközhöz.

A számítási műveletek konfigurálása 20-30 percet is igénybe vehet, mivel a színfalak mögött a virtuális gépek és a Kubernetes-fürt is létrejön.

Miután sikeresen konfigurálta a számítást a Azure Portalban, egy Kubernetes-fürtöt és egy, az IoT-névtérhez társított alapértelmezett felhasználót (az Azure Stack Edge Pro által vezérelt rendszernévtér) létezik.
