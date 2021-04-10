---
title: GPU-modul üzembe helyezése az Azure Stack Edge Pro GPU-eszközön | Microsoft Docs
description: Ismerteti, hogyan engedélyezhető a számítási funkció, és hogyan végezhető el a Azure Stack Edge Pro-eszköz számítási felkészültsége a helyi felhasználói felületen keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: af44912edf3ce98ceb71bd34388543f7652c2181
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105568460"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-edge-pro-gpu-device"></a>GPU-t támogató IoT-modul üzembe helyezése Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ez a cikk azt ismerteti, hogyan helyezhet üzembe GPU-kompatibilis IoT Edge modult az Azure Stack Edge Pro GPU-eszközön. 

Ebben a cikkben az alábbiakkal ismerkedhet meg:
  - Készítse elő Azure Stack Edge Pro-t egy GPU-modul futtatásához.
  - Mintakód letöltése és telepítése git-tárházból.
  - Hozza létre a megoldást, és hozzon létre egy üzembe helyezési jegyzéket.
  - A megoldás üzembe helyezése Azure Stack Edge Pro-eszközön.
  - A modul kimenetének figyelése.


## <a name="about-sample-module"></a>A minta modul ismertetése

A cikkben található GPU-minta modul a PyTorch és a TensorFlow teljesítménytesztet tartalmazza a CPU-hoz a GPU-val szemben.

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg arról, hogy rendelkezik az alábbiakkal:

- Hozzáférése van egy GPU-t támogató, 1 csomópontos Azure Stack Edge Pro-eszközhöz. Ezt az eszközt egy Azure-beli erőforrás aktiválta. Lásd: [az eszköz aktiválása](azure-stack-edge-gpu-deploy-activate.md).
- Konfigurálta az eszközön a számítást. Kövesse az [oktatóanyag: számítás konfigurálása a Azure stack Edge Pro-eszközön](azure-stack-edge-gpu-deploy-configure-compute.md)című témakör lépéseit.
- Egy Azure Container Registry (ACR). Lépjen a **hozzáférési kulcsok** panelre, és jegyezze fel az ACR bejelentkezési kiszolgálóját, felhasználónevét és jelszavát. További információért látogasson el a rövid útmutató [: Private Container Registry létrehozása a Azure Portal használatával](../container-registry/container-registry-get-started-portal.md#create-a-container-registry).
- A következő fejlesztői erőforrások egy Windows-ügyfélen:
    - [Azure CLI 2,0 vagy újabb verzió](https://aka.ms/installazurecliwindows)
    - [Docker CE](https://store.docker.com/editions/community/docker-ce-desktop-windows). Előfordulhat, hogy létre kell hoznia egy fiókot a szoftver letöltéséhez és telepítéséhez.
    - [Visual Studio Code](https://code.visualstudio.com/)  
    - [Azure IoT Edge bővítmény a Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)-hoz.    
    - [Python-bővítmény a Visual Studio Code-hoz](https://marketplace.visualstudio.com/items?itemName=ms-python.python)    
    - [Python 3](https://www.python.org/)    
    - Pip a Python-csomagok telepítéséhez (jellemzően a Python-telepítés részeként)

## <a name="get-the-sample-code"></a>A mintakód letöltése

1. Nyissa meg az [Azure intelligens peremhálózati mintázatait az Azure-mintákban](https://github.com/azure-samples/azure-intelligent-edge-patterns). A kód klónozásához vagy letöltéséhez töltse le a zip-fájlt. 

    ![Zip-fájl letöltése](media/azure-stack-edge-gpu-deploy-sample-module/download-zip-file-1.png)

    Bontsa ki a fájlokat a zip-fájlból. A mintákat klónozással is elvégezheti.

    ```json
    git clone https://github.com/Azure-Samples/azure-intelligent-edge-patterns.git
    ```

## <a name="build-and-deploy-module"></a>Modul létrehozása és üzembe helyezése

1. Nyissa meg a **GpuReferenceModules** mappát a Visual Studio Code-ban.

    ![GPUReferenceModules megnyitása a VS Code-ban](media/azure-stack-edge-gpu-deploy-sample-module/open-folder-gpu-sample-1.png)

2. Nyissa *meg adeployment.template.jst* , és azonosítsa a tároló-beállításjegyzékre hivatkozó paramétereket. A következő fájlban CONTAINER_REGISTRY_USERNAME, CONTAINER_REGISTRY_PASSWORD és CONTAINER_REGISTRY_NAME használatos.

    ```json
        {
      "$schema-template": "2.0.0",
      "modulesContent": {
        "$edgeAgent": {
          "properties.desired": {
            "schemaVersion": "1.0",
            "runtime": {
              "type": "docker",
              "settings": {
                "minDockerVersion": "v1.25",
                "loggingOptions": "",
                "registryCredentials": {
                  "${CONTAINER_REGISTRY_NAME}":{
                  "username": "$CONTAINER_REGISTRY_USERNAME",
                  "password": "$CONTAINER_REGISTRY_PASSWORD",
                  "address": "${CONTAINER_REGISTRY_NAME}.azurecr.io"
                  }
                }
              }
            },
    ```
3. Hozzon létre egy új fájlt. Töltse ki a tároló beállításjegyzékének paramétereinek értékeit (a korábbi lépésben azonosított értékeket használja) a következőképpen: 

    ```json
    CONTAINER_REGISTRY_NAME=<YourContainerRegistryName>
    CONTAINER_REGISTRY_USERNAME=<YourContainerRegistryUserName>
    CONTAINER_REGISTRY_PASSWORD=<YourContainerRegistryPassword>
    ```
    Alább látható egy minta *. env* fájl:
    
    ![. Env fájl létrehozása és mentése](media/azure-stack-edge-gpu-deploy-sample-module/create-save-env-file-1.png)

4. Mentse a fájlt *. env* néven a **SampleSolution** mappában.

5. A Docker-ba való bejelentkezéshez írja be a következő parancsot a Visual Studio Code integrált terminálba. 

    ```json
    docker login -u <CONTAINER_REGISTRY_USERNAME> -p <CONTAINER_REGISTRY_PASSWORD> <CONTAINER_REGISTRY_NAME>
    ```
    Nyissa meg a tároló-beállításjegyzék **hozzáférési kulcsok** szakaszát a Azure Portal. Másolja ki és használja a beállításjegyzék nevét, jelszavát és bejelentkezési kiszolgálóját.

    ![Hozzáférési kulcsok a tároló beállításjegyzékében](media/azure-stack-edge-gpu-deploy-sample-module/container-registry-access-keys-1.png)

    A hitelesítő adatok megadása után a bejelentkezés sikeres lesz.

    ![Sikeres bejelentkezés](media/azure-stack-edge-gpu-deploy-sample-module/successful-sign-in-1.png)

6. Küldje le a rendszerképet az Azure Container registrybe. A VS Code Explorerben válassza ki és kattintson a jobb gombbal a **deployment.template.js** fájlra, majd válassza a **IoT Edge megoldás létrehozása és leküldése** lehetőséget. 

    ![IoT Edge-megoldás létrehozása és leküldése](media/azure-stack-edge-gpu-deploy-sample-module/build-push-iot-edge-solution-1.png)   

    Ha a Python és a Python bővítmény nincs telepítve, akkor ezek a megoldás létrehozásakor és leküldésekor települnek. Ez azonban a hosszú fordítási időt eredményezi. 

    Ha ez a lépés befejeződik, megjelenik a modul a tároló beállításjegyzékében.

    ![Modul a tároló beállításjegyzékében](media/azure-stack-edge-gpu-deploy-sample-module/module-container-registry-1.png)    


7. Központi telepítési jegyzékfájl létrehozásához kattintson a jobb gombbal a **deployment.template.js** elemre, majd válassza a **IoT Edge üzembe helyezési jegyzék** létrehozása lehetőséget. 

    ![IoT Edge üzembe helyezési jegyzék előállítása](media/azure-stack-edge-gpu-deploy-sample-module/generate-iot-edge-deployment-manifest-1.png)  

    Az értesítés azt az elérési utat értesíti, amelyen az üzembe helyezési jegyzék létrejött. A jegyzékfájl a `deployment.amd64.json` **konfigurációs** mappában létrehozott fájl. 

8. Válassza ki a **deployment.amd64.js** fájlt a **konfigurációs** mappában, majd válassza a **központi telepítés létrehozása egyetlen eszközhöz** lehetőséget. Ne használja a **deployment.template.js** fájlt. 

    ![Üzemelő példány létrehozása egyetlen eszközhöz](media/azure-stack-edge-gpu-deploy-sample-module/create-deployment-single-device-1.png)  

    A **kimenet** ablakban a telepítés sikerességét jelző üzenetnek kell megjelennie.

    ![Az üzembe helyezés sikeres volt a kimenetben](media/azure-stack-edge-gpu-deploy-sample-module/deployment-succeeded-output-1.png) 

## <a name="monitor-the-module"></a>A modul figyelése  

1. A VS Code parancskatalógusában futtassa az **Azure IoT Hub: Select IoT Hub** (Azure IoT Hub: IoT Hub kiválasztása) parancsot.

2. Válassza ki a konfigurálni kívánt IoT Edge-eszközt tartalmazó előfizetést és IoT Hubot. Ebben az esetben válassza ki az Azure Stack Edge Pro-eszköz üzembe helyezéséhez használt előfizetést, és válassza ki az Azure Stack Edge Pro-eszközhöz létrehozott IoT Edge eszközt. Ez akkor fordul elő, ha a számítást a korábbi lépések Azure Portalján keresztül konfigurálja.

3. A VS Code Explorerben bontsa ki az Azure IoT Hub szakaszt. Az **eszközök** területen az Azure stack Edge Pro-eszköznek megfelelő IoT Edge eszközt kell látnia. 

    1. Válassza ki az eszközt, kattintson a jobb gombbal, majd válassza a **figyelés beépített esemény végpontja** lehetőséget.
  
        ![Figyelés indítása](media/azure-stack-edge-gpu-deploy-sample-module/monitor-builtin-event-endpoint-1.png)  

    2. Nyissa meg az **eszközök > modulokat** , és ellenőrizze, hogy fut-e a **GPU-modul** .

        ![Modul IoT Hub](media/azure-stack-edge-gpu-deploy-sample-module/module-iot-hub-1.png)  

    3. A VS Code terminálnak a Azure Stack Edge Pro-eszköz figyelési kimenetének IoT Hubi eseményeit is meg kell jelenítenie.

        ![Figyelési kimenet](media/azure-stack-edge-gpu-deploy-sample-module/monitor-events-output-1.png) 

        Láthatja, hogy a GPU által az azonos műveletek végrehajtásához szükséges idő (5000-es ismétlés) nem sokkal kisebb, mint a CPU esetében.

## <a name="next-steps"></a>Következő lépések

- További információ arról, hogyan [konfigurálhatja a GPU-t modul használatára](./azure-stack-edge-gpu-configure-gpu-modules.md).