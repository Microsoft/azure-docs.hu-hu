---
title: GPU-modul üzembe helyezése az Microsoft Azure Stack Edge Pro-eszközön az Azure Marketplace-en | Microsoft Docs
description: Ismerteti, hogyan helyezhetők üzembe GPU-kompatibilis IoT-modulok a Azure Stack Edge Pro GPU-eszközön.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: e22014380d568b12e1e3bec751a75180d0760ab7
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2021
ms.locfileid: "105568402"
---
# <a name="deploy-a-gpu-enabled-iot-module-from-azure-marketplace-on-azure-stack-edge-pro-gpu-device"></a>GPU-t támogató IoT-modul üzembe helyezése az Azure Marketplace-en Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ez a cikk bemutatja, hogyan helyezhet üzembe egy grafikus processzorral (GPU) IoT Edge modult az Azure Marketplace-en az Azure Stack Edge Pro-eszközön. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:
  - Készítse elő Azure Stack Edge Pro-t egy GPU-modul futtatásához.
  - A GPU-t támogató IoT modul letöltése és üzembe helyezése az Azure Marketplace-en.
  - A modul kimenetének figyelése.

## <a name="about-sample-module"></a>A minta modul ismertetése

A cikkben található GPU-minta modul a PyTorch és a TensorFlow teljesítménytesztet tartalmazza a CPU-hoz a GPU-val szemben.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Hozzáférése van egy GPU-t támogató 1 csomópontos Azure Stack peremhálózati eszközhöz. Ezt az eszközt egy Azure-beli erőforrás aktiválta. 
- Konfigurálta az eszközön a számítást. Kövesse az [oktatóanyag: számítás konfigurálása az Azure stack Edge-eszközön](azure-stack-edge-gpu-deploy-configure-compute.md)című témakör lépéseit.
- A következő fejlesztői erőforrások egy Windows-ügyfélen:
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Azure IoT Edge bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)-hoz.   


## <a name="get-module-from-azure-marketplace"></a>Modul beszerzése az Azure Marketplace-ről

1. Böngésszen [az összes alkalmazásban az Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps).

    ![Alkalmazások tallózása az Azure Marketplace-en](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/browse-apps-marketplace-1.png)

2. Keressen rá a GPU-k **használatába**.

    ![Keresési GPU-minta modul](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/search-gpu-sample-module-1.png)

3. Válassza a **Letöltés most** lehetőséget.

    ![Minta modul beolvasása](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/get-sample-module-1.png)

4. A **tovább** gombra kattintva nyugtázza a szolgáltató használati feltételeit és adatvédelmi szabályzatát. 

    ![2. mintavételi modul beolvasása](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/terms-of-use-1.png)

5. Válassza ki azt az előfizetést, amelyet az Azure Stack Edge Pro-eszköz üzembe helyezéséhez használt.

    ![Előfizetés kiválasztása](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/select-subscription-1.png)

6. Adja meg annak a IoT Hub szolgáltatásnak a nevét, amelyet az Azure Stack Edge Pro-eszköz konfigurálásakor hozott létre. A IoT Hub szolgáltatás nevének megkereséséhez nyissa meg az eszközhöz társított Azure Stack Edge-erőforrást Azure Portal. 

    1. A bal oldali ablaktábla menüjében válassza az **Edge services > IoT Edge** lehetőséget. 

        ![Számítási konfiguráció megtekintése](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/view-config-1.png)

    1. Válassza a **Tulajdonságok** lehetőséget. 

        1. Jegyezze fel a IoT Hub szolgáltatást, amely akkor jött létre, amikor a Azure Stack Edge Pro-eszközön konfigurálta a számítást.
        2. Jegyezze fel annak a IoT Edge eszköznek a nevét, amelyet a számítás beállításakor hozott létre. Ezt a nevet fogja használni a következő lépésben.

        ![Peremhálózati számítási konfiguráció](media/azure-stack-edge-gpu-deploy-sample-module/view-compute-config-1.png)

10. Válassza **a telepítés eszközre** lehetőséget.

11. Adja meg a IoT Edge eszköz nevét, vagy válassza az **eszköz keresése** elemet a hub-ban regisztrált eszközök között.

    ![Eszköz keresése](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/find-device-1.png)

12. Válassza a **Létrehozás** lehetőséget a központi telepítési jegyzék konfigurálásának szabványos folyamatának folytatásához, beleértve a további modulok hozzáadását, ha szükséges. Az új modul adatai, például a rendszerkép URI-ja, a létrehozási beállítások és a kívánt tulajdonságok előre definiálva vannak, de módosíthatók.

    ![Kattintás a Létrehozás gombra](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/target-devices-iot-edge-module-1.png)


13. Ellenőrizze, hogy a modul telepítve van-e a IoT Hub a Azure Portalban. Válassza ki az eszközt, válassza a **modulok beállítása** elemet, és a modult a **IoT Edge modulok** szakaszban kell megadnia.

    ![Válassza a létrehozás 2](media/azure-stack-edge-gpu-deploy-sample-module-marketplace/running-module-iotres-1.png)

## <a name="monitor-the-module"></a>A modul figyelése  

1. A VS Code parancskatalógusában futtassa az **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub kiválasztása) parancsot.

2. Válassza ki a konfigurálni kívánt IoT Edge-eszközt tartalmazó előfizetést és IoT Hubot. Ebben az esetben válassza ki az Azure Stack Edge Pro-eszköz üzembe helyezéséhez használt előfizetést, és válassza ki az Azure Stack Edge Pro-eszközhöz létrehozott IoT Edge eszközt. Ez akkor fordul elő, ha a számítást a korábbi lépések Azure Portalján keresztül konfigurálja.

3. A VS Code Explorerben bontsa ki az Azure IoT Hub szakaszt. Az **eszközök** területen az Azure stack Edge Pro-eszköznek megfelelő IoT Edge eszközt kell látnia. 

    1. Válassza ki az eszközt, kattintson a jobb gombbal, majd válassza a **figyelés beépített esemény végpontja** lehetőséget.
  
        ![Figyelés indítása](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Nyissa meg az **eszközök > modulokat** , és ellenőrizze, hogy fut-e a **GPU-modul** .

    3. A VS Code terminálnak a Azure Stack Edge Pro-eszköz figyelési kimenetének IoT Hubi eseményeit is meg kell jelenítenie.

        ![Figyelési kimenet](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Láthatja, hogy a GPU által az azonos műveletek végrehajtásához szükséges idő (5000-es ismétlés) nem sokkal kisebb, mint a CPU esetében.

## <a name="next-steps"></a>Következő lépések

- További információ arról, hogyan [konfigurálhatja a GPU-t modul használatára](./azure-stack-edge-gpu-configure-gpu-modules.md).