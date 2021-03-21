---
title: Oktatóanyag a szűréshez, az adatok elemzése a Azure Stack Edge Pro GPU-val való számítással | Microsoft Docs
description: Megtudhatja, hogyan konfigurálhat számítási szerepkört Azure Stack Edge Pro GPU-ban, és hogyan alakíthatja át az adatokat az Azure-ba való küldés előtt.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/08/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to configure compute on Azure Stack Edge Pro so I can use it to transform the data before sending it to Azure.
ms.openlocfilehash: 750b7a8367d46434f48626268a0eb37c9edddfb1
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102633536"
---
# <a name="tutorial-configure-compute-on-azure-stack-edge-pro-gpu-device"></a>Oktatóanyag: számítás konfigurálása Azure Stack Edge Pro GPU-eszközön

<!--ALPA WILL VERIFY - [!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

Ez az oktatóanyag ismerteti, hogyan konfigurálhat számítási szerepkört, és hogyan hozhat létre Kubernetes-fürtöt az Azure Stack Edge Pro-eszközön. 

Ez az eljárás körülbelül 20 – 30 percet vesz igénybe.


Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Kubernetes-végpontok beolvasása

 
## <a name="prerequisites"></a>Előfeltételek

Mielőtt beállít egy számítási szerepkört a Azure Stack Edge Pro-eszközön, ügyeljen rá, hogy:

- Aktiválta Azure Stack Edge Pro-eszközét a [Azure stack Edge Pro aktiválása](azure-stack-edge-gpu-deploy-activate.md)című témakörben leírtak szerint.
- Győződjön meg arról, hogy követte a [számítási hálózat engedélyezése](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network) és a:
    - Hálózati adapter engedélyezése a számítási feladatokhoz.
    - Hozzárendelt Kubernetes Node IP-címek és Kubernetes külső szolgáltatás IP-címei.

## <a name="configure-compute"></a>Számítás konfigurálása

[!INCLUDE [configure-compute](../../includes/azure-stack-edge-gateway-configure-compute.md)]

## <a name="get-kubernetes-endpoints"></a>Kubernetes-végpontok beolvasása

Ha az ügyfelet a Kubernetes-fürt eléréséhez szeretné konfigurálni, szüksége lesz az Kubernetes-végpontra. Az alábbi lépéseket követve beolvashatja a Kubernetes API-végpontot az Azure Stack Edge Pro-eszköz helyi felhasználói felületéről.

1. Az eszköz helyi webes FELÜLETén nyissa meg az **eszközök** lapot.
2. Másolja a **KUBERNETES API Service** -végpontot az **eszköz végpontjai** alatt. Ez a végpont egy karakterlánc a következő formátumban: `https://compute.<device-name>.<DNS-domain>[Kubernetes-cluster-IP-address]` . 

    ![Eszköz lap a helyi felhasználói felületen](./media/azure-stack-edge-gpu-create-kubernetes-cluster/device-kubernetes-endpoint-1.png)

3. Mentse a végponti karakterláncot. Ezt a végpont-karakterláncot később fogja használni, amikor az ügyfelet a kubectl-on keresztül éri el a Kubernetes-fürthöz.

4. A helyi webes KEZELŐFELÜLETen a következőket teheti:

    - Nyissa meg a Kubernetes API-t, válassza a **Speciális beállítások** lehetőséget, majd töltse le a Kubernetes speciális konfigurációs fájlját. 

        ![Eszköz lap a helyi felhasználói felületen 1](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-1.png)

        Ha a Microsofttól kapott kulcsot (válassza a felhasználók rendelkezhetnek kulcsot), akkor ezt a konfigurációs fájlt használhatja.

        ![Eszköz lap a helyi felhasználói felületen 2](./media/azure-stack-edge-gpu-deploy-configure-compute/download-advanced-config-2.png)

    - Megtekintheti a **Kubernetes irányítópult** -végpontot is, és letöltheti a `aseuser` konfigurációs fájlt. 
    
        ![Eszköz lap a helyi felhasználói felületen 3](./media/azure-stack-edge-gpu-deploy-configure-compute/download-aseuser-config-1.png)

        Ezt a konfigurációs fájlt használhatja a Kubernetes-irányítópultba való bejelentkezéshez, vagy a Kubernetes-fürt esetleges problémáinak hibakereséséhez. További információ: hozzáférés a [Kubernetes-irányítópulthoz](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#access-dashboard). 


## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Számítás konfigurálása
> * Kubernetes-végpontok beolvasása


Az Azure Stack Edge Pro-eszköz felügyeletének megismeréséhez lásd:

> [!div class="nextstepaction"]
> [Helyi webes felhasználói felület használata Azure Stack Edge Pro felügyeletéhez](azure-stack-edge-manage-access-power-connectivity-mode.md)
