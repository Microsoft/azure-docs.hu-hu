---
title: GPU-megosztás Azure Stack Edge Pro GPU-eszközön
description: Ismerteti a GPU-k Azure Stack Edge Pro GPU-eszközön való megosztásának módszereit.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/05/2021
ms.author: alkohli
ms.openlocfilehash: 6683e39cfa3601b1ae1fbbe02e69e4dc0a54e8e7
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565019"
---
# <a name="gpu-sharing-on-your-azure-stack-edge-pro-gpu-device"></a>GPU-megosztás az Azure Stack Edge Pro GPU-eszközön

A grafikus feldolgozó egység (GPU) egy speciális processzor, amely a grafikus megjelenítés felgyorsítására szolgál. A GPU-k egyszerre több adatot is feldolgozhatnak, így hasznosak lehetnek a gépi tanulás, a videoszerkesztő és a játékok alkalmazásai számára. A CPU az általános célú számításokon kívül a Azure Stack Edge Pro GPU-eszközök egy vagy két NVIDIA Tesla T4 GPU-t is tartalmazhatnak a nagy számítási igényű munkaterhelésekhez, például a hardveres gyorsított következtetésekhez. További információ: [NVIDIA Tesla T4 GPU](https://www.nvidia.com/data-center/tesla-t4/).


## <a name="about-gpu-sharing"></a>A GPU-megosztás ismertetése

Előfordulhat, hogy a gépi tanulás vagy más számítási feladatok nem igényelnek dedikált GPU-t. A GPU-k megoszthatók és megoszthatók a GPU-k a tárolók és a virtuális gépek munkaterhelései között, így a GPU kihasználtsága jelentősen befolyásolhatja a GPU teljesítménybeli előnyeit.  

## <a name="using-gpu-with-vms"></a>GPU használata virtuális gépekkel

A Azure Stack Edge Pro-eszközön a virtuális gépek számítási feladatainak üzembe helyezése során a GPU nem osztható meg. A GPU-t csak egy virtuális géphez lehet leképezni. Ez azt jelenti, hogy csak egy GPU-s virtuális gép használható egy olyan eszközön, amely két GPU-val felszerelt eszközön egy GPU-val és két virtuális géppel rendelkezik. Más tényezőket is figyelembe kell venni, amikor GPU virtuális gépeket használ egy olyan eszközön, amely Kubernetes van konfigurálva a tároló munkaterhelésekhez. További információ: [GPU virtuális gépek és Kubernetes](azure-stack-edge-gpu-deploy-gpu-virtual-machine.md#gpu-vms-and-kubernetes).


## <a name="using-gpu-with-containers"></a>GPU használata tárolókkal

Ha tároló munkaterheléseket helyez üzembe, a GPU több módon is megosztható a hardver és a szoftver rétegében. A Tesla T4 GPU-val a Azure Stack Edge Pro-eszközön korlátozott a szoftveres megosztás. Az eszközön a következő két módszer használható a GPU-szoftverek megosztására: 

- Az első módszer környezeti változók használatával határozza meg, hogy hány GPU-t lehet megosztani. Ha ezt a módszert használja, vegye figyelembe az alábbi figyelmeztetéseket:

    - Ezzel a módszerrel megadhat egy vagy több GPU-t is. Nem lehet megadni tört használatot.
    - Több modul is leképezhető egy GPU-ra, de ugyanazt a modult nem lehet egynél több GPU-hoz leképezni.
    - Az NVIDIA SMI kimenettel a GPU teljes kihasználtsága látható, beleértve a memória kihasználtságát is.
    
    További információ: [IoT Edge modul üzembe helyezése, amely a GPU](azure-stack-edge-gpu-configure-gpu-modules.md) -t használja az eszközön.

- A második módszer használatához engedélyeznie kell a többfolyamatos szolgáltatást az NVIDIA GPU-k számára. Az MPS egy futtatókörnyezeti szolgáltatás, amely lehetővé teszi, hogy a CUDA használatával egyszerre több folyamat fusson egyidejűleg egyetlen megosztott GPU-on. Az MPS lehetővé teszi a kernel és a memóriamásolási műveleteinek átfedését a GPU különböző folyamataiból a maximális kihasználtság eléréséhez. További információ: [többfolyamatos szolgáltatás](https://docs.nvidia.com/deploy/pdf/CUDA_Multi_Process_Service_Overview.pdf).

    Ha ezt a módszert használja, vegye figyelembe az alábbi figyelmeztetéseket:
    
    - Az MPS lehetővé teszi további jelzők megadását a GPU üzembe helyezésében.
    - Megadhatja a többrészes használatot az MPS-n keresztül, így korlátozva az eszközön telepített alkalmazások használatát. A `env` `deployment.yaml` következő paraméter hozzáadásával megadhatja a GPU százalékos arányát, amelyet az egyes alkalmazásokhoz használhat a szakasza alatt: 

    ```yml
    // Example: application wants to limit gpu percentage to 20%
    
        env:
              - name: CUDA_MPS_ACTIVE_THREAD_PERCENTAGE 
                value: "20"    
    ```

## <a name="gpu-utilization"></a>GPU-használat
 
Ha a GPU-t az eszközön üzembe helyezett, tárolóban lévő számítási feladatokhoz használja, használhatja az NVIDIA rendszerkezelési felületet (NVIDIA-SMI). Az NVIDIA-SMI egy parancssori segédprogram, amely segítséget nyújt az NVIDIA GPU-eszközök felügyeletéhez és figyeléséhez. További információ: az [NVIDIA rendszerkezelési felülete](https://developer.nvidia.com/nvidia-system-management-interface).

A GPU-használat megtekintéséhez először kapcsolódjon az eszköz PowerShell-felületéhez. Futtassa a `Get-HcsNvidiaSmi` parancsot, és tekintse meg az NVIDIA SMI kimenetét. Azt is megtekintheti, hogy a GPU-kihasználtság hogyan változik úgy, hogy engedélyezi az MPS-t, majd több számítási feladatot helyez üzembe az eszközön. További információt a [többfolyamatos szolgáltatás engedélyezése](azure-stack-edge-gpu-connect-powershell-interface.md#enable-multi-process-service-mps)című témakörben talál.


## <a name="next-steps"></a>Következő lépések

- GPU-megosztás a Kubernetes-környezetek [Azure stack Edge Pro platformon](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md).
- GPU-megosztás a IoT-környezetek [Azure stack Edge Pro platformon](azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing.md).
