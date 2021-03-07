---
title: GPU-modul futtatása Microsoft Azure Stack Edge Pro GPU-eszközön | Microsoft Docs
description: Ismerteti, hogyan konfigurálhat és futtathat egy modult a GPU-n egy Azure Stack Edge Pro-eszközön a Azure Portal használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: dfae1a9b02db7e7b9577acdb47a1ba089f1609e8
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2021
ms.locfileid: "102439051"
---
# <a name="configure-and-run-a-module-on-gpu-on-azure-stack-edge-pro-device"></a>Modul konfigurálása és futtatása GPU-ban Azure Stack Edge Pro-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Az Azure Stack Edge Pro-eszköz egy vagy több grafikus feldolgozó egységet (GPU) tartalmaz. A GPU-k használata gyakori választás az MI-számítások esetén, mivel párhuzamos feldolgozási képességekkel rendelkeznek, és gyorsabban renderelik a képeket a processzoroknál (CPU-knál). Az Azure Stack Edge Pro-eszközön található GPU-val kapcsolatos további információkért látogasson el az [Edge Pro-eszköz technikai specifikációjának Azure stack](azure-stack-edge-gpu-technical-specifications-compliance.md).

Ez a cikk bemutatja, hogyan konfigurálhat és futtathat egy modult a GPU-ban az Azure Stack Edge Pro-eszközön. Ebben a cikkben az NVIDIA T4 GPU-hoz írt, nyilvánosan elérhető tároló modul **számjegyeit** fogjuk használni. Ezzel az eljárással konfigurálhatja az NVIDIA által a GPU-k által közzétett egyéb modulokat.


## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Hozzáférése van egy GPU-t támogató, 1 csomópontos Azure Stack Edge Pro-eszközhöz. Ezt az eszközt egy Azure-beli erőforrás aktiválta.  

## <a name="configure-module-to-use-gpu"></a>Modul konfigurálása GPU használatára

Egy modul konfigurálásához, hogy a GPU-t használja a Azure Stack Edge Pro-eszközön egy modul futtatásához.<!--Can it be simplified? "To configure a module to be run by the GPU on your Azure Stack Edge Pro device,"?--> kövesse az alábbi lépéseket.

1. A Azure Portal nyissa meg az eszközhöz hozzárendelt erőforrást.

2. Az **Áttekintés** területen válassza a **IoT Edge** lehetőséget.

    ![Modul konfigurálása a GPU 1 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-1.png)

3. A **IoT Edge szolgáltatás engedélyezése** lapon válassza a **Hozzáadás** lehetőséget.

   ![Modul konfigurálása a GPU 2 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-compute-2.png)

4. A **IoT Edge szolgáltatás létrehozása** területen adja meg IoT hub erőforrásának beállításait:

   |Mező   |Érték    |
   |--------|---------|
   |Előfizetés      | Az Azure Stack Edge-erőforrás által használt előfizetés. |
   |Erőforráscsoport    | Az Azure Stack peremhálózati erőforrás által használt erőforráscsoport. |
   |IoT Hub           | Válassza az **új létrehozása** lehetőséget, vagy **használja a meglévőt**. <br> Alapértelmezés szerint IoT-erőforrás létrehozásához a rendszer Standard szintet (S1) használ. Ingyenes szintű IoT-erőforrás használatához hozzon létre egyet, majd válassza ki a létrehozott erőforrást. <br> A IoT Hub erőforrás minden esetben ugyanazt az előfizetést és erőforráscsoportot használja, amelyet az Azure Stack Edge-erőforrás használ.     |
   |Name              | Ha nem szeretné használni az új IoT Hub erőforráshoz megadott alapértelmezett nevet, adjon meg egy másik nevet. |

   A beállítások befejezését követően válassza a **felülvizsgálat + létrehozás** lehetőséget. Tekintse át a IoT Hub erőforrás beállításait, és válassza a **Létrehozás** lehetőséget.

   ![Ismerkedés a 2. számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-3.png)

   Egy IoT Hub erőforrás létrehozása több percet vesz igénybe. Az erőforrás létrehozása után az **Áttekintés** azt jelzi, hogy a IoT Edge szolgáltatás már fut.

   ![Ismerkedés a 3. számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-4.png)

5. A peremhálózati számítási szerepkör konfigurálásának megerősítéséhez válassza a **Tulajdonságok** lehetőséget.

   ![Ismerkedés a 4. számítási feladatokkal](./media/azure-stack-edge-j-series-deploy-configure-compute/configure-compute-5.png)

6. A **Tulajdonságok** területen válassza ki **IoT Edge eszköz** hivatkozását.

   ![Modul konfigurálása a GPU 6 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-2.png)

   A jobb oldali ablaktáblában megjelenik az Azure Stack Edge Pro-eszközhöz társított IoT Edge eszköz. Ez az eszköz megfelel a IoT Hub erőforrás létrehozásakor létrehozott IoT Edge eszköznek.
 
7. Válassza ki ezt az IoT Edge eszközt.

   ![Modul konfigurálása a GPU 7 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-3.png)

8. Válassza a **Set modules** (Modulok beállítása) lehetőséget.

   ![Modul konfigurálása a GPU 8 használatához](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-4.png)

9. Válassza a **+ Hozzáadás** , majd a **+ IoT Edge modul** lehetőséget. 

    ![Modul konfigurálása a GPU 9-es használatához](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-5.png)

10. A **IoT Edge modul hozzáadása** lapon:

    1. Adja meg a **rendszerkép URI-ját**. Itt fogja használni a nyilvánosan elérhető NVIDIA-modulok **számjegyeit** . 
    
    2. Állítsa az **Újraindítási szabályzatot** **mindig** értékre.
    
    3. Állítsa a **kívánt állapotot** a **futtatásra**.
    
    ![Modul konfigurálása a GPU 10 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-6.png)

11. A **környezeti változók** lapon adja meg a változó nevét és a hozzá tartozó értéket. 

    1. Ha azt szeretné, hogy az aktuális modul egy GPU-t használjon az eszközön, használja a NVIDIA_VISIBLE_DEVICES. 

    2. Állítsa az értéket 0 vagy 1 értékre. A 0 vagy az 1 érték biztosítja, hogy az eszköz legalább egy GPU-t használ ehhez a modulhoz. Ha az értéket 0, 1 értékre állítja, az azt jelenti, hogy az eszközön található GPU-k is ezt a modult használják.

       ![Modul konfigurálása a GPU 11 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-7.png)

       További információ az NVIDIA GPU-val használható környezeti változókról: [NVIDIA Container Runtime](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).

    > [!NOTE]
    > Egy GPU csak egy modulhoz rendelhető hozzá. Egy modul azonban a GPU-k egyikét is használhatja.

12. Adja meg a modul nevét. Ezen a ponton dönthet úgy, hogy a tároló létrehozása lehetőséget adja meg, és módosítja a modul különálló beállításait, vagy ha elkészült, válassza a **Hozzáadás** lehetőséget. 

    ![Modul konfigurálása a GPU 12 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-8.png)

13. Győződjön meg arról, hogy a modul fut, és válassza a **felülvizsgálat + létrehozás** elemet.

    ![Modul konfigurálása a GPU 13 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-9.png)

14. A **felülvizsgálat + létrehozás** lapon megjelenik a kiválasztott központi telepítési beállítások. Tekintse át a beállításokat, és válassza a **Létrehozás** lehetőséget.
    
    ![Modul konfigurálása a GPU 14 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-10.png)

15. Jegyezze fel a modul **futtatókörnyezeti állapotát** .
    
    ![Modul konfigurálása a GPU 15 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-11.png)

    A modul üzembe helyezése néhány percet vesz igénybe. Válassza a **frissítés** lehetőséget, és a **futásidejű állapot** frissítését **futtatni** kell.

    ![Modul konfigurálása a GPU 16 használatára](media/azure-stack-edge-j-series-configure-gpu-modules/configure-gpu-12.png)


## <a name="next-steps"></a>Következő lépések

- További információ [az NVIDIA GPU-val használható környezeti változókról](https://github.com/NVIDIA/nvidia-container-runtime#environment-variables-oci-spec).
