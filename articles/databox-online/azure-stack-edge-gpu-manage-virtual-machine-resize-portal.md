---
title: Virtuális gépek átméretezése Azure Stack Edge Pro GPU, Pro R, mini R használatával a Azure Portal
description: Megtudhatja, hogyan méretezheti át a Azure Stack Edge Pro GPU-n futó virtuális gépeket (VM), Azure Stack Edge Pro R-t, Azure Stack Edge mini R-t a Azure Portaln keresztül.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/30/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to resize VMs running on an Azure Stack Edge Pro device so that I can use it to run applications using Edge compute before sending it to Azure.
ms.openlocfilehash: bf2125a6e1d0b443202a036c52fdf845f79d11fa
ms.sourcegitcommit: 3ee3045f6106175e59d1bd279130f4933456d5ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106080300"
---
# <a name="use-the-azure-portal-to-resize-the-vms-on-your-azure-stack-edge-pro-gpu"></a>A Azure Portal használatával átméretezheti a virtuális gépeket a Azure Stack Edge Pro GPU-ban

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

Ez a cikk azt ismerteti, hogyan lehet átméretezni a Azure Stack Edge Pro GPU-eszközön üzembe helyezett virtuális gépeket (VM-ket).

       
## <a name="about-vm-sizing"></a>A virtuális gépek méretezése

A virtuális gép mérete határozza meg a virtuális gép számára elérhető számítási erőforrások (például a processzor, a GPU és a memória) mennyiségét. A virtuális gépeket az alkalmazás számítási feladatainak megfelelő virtuálisgép-mérettel kell létrehoznia. 

Annak ellenére, hogy az összes gép ugyanazon a hardveren fut, a gépi méretek eltérő korlátokkal rendelkeznek a lemezes hozzáféréshez. Ez segítséget nyújt a virtuális gépeken a teljes lemezes hozzáférés kezelésében. Ha a munkaterhelés növekszik, akkor átméretezheti a meglévő virtuális gépeket is.

További információ: támogatott virtuálisgép- [méretek az eszközhöz](azure-stack-edge-gpu-virtual-machine-sizes.md).


## <a name="prerequisites"></a>Előfeltételek

Mielőtt átméretezi az eszközön futó virtuális gépet a Azure Portalon keresztül, ügyeljen rá, hogy:

1. Legalább egy virtuális gép telepítve van az eszközön. A virtuális gép létrehozásához tekintse meg a [virtuális gép üzembe helyezése a Azure stack Edge Pro](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)-ban című témakör utasításait a Azure Portal használatával.

1. A virtuális gépnek **leállított** állapotban kell lennie. A virtuális gép leállításához lépjen a **Virtual machines > áttekintés** elemre, és válassza ki a leállítani kívánt virtuális gépet. Az Áttekintés lapon válassza a **Leállítás** lehetőséget, majd válassza az **Igen** lehetőséget, ha a rendszer megerősítést kér. A virtuális gép átméretezése előtt le kell állítania a virtuális gépet.

    ![Virtuális gép leállítása az Áttekintés lapról](./media/azure-stack-edge-gpu-manage-virtual-machine-network-interfaces-portal/stop-vm-2.png)


## <a name="resize-a-vm"></a>Virtuális gép átméretezése

Az eszközön üzembe helyezett virtuális gépek átméretezéséhez kövesse az alábbi lépéseket. 

1. Nyissa meg a leállított virtuális gépet, és nyissa meg az **Áttekintés** lapot. Válassza a **virtuális gép mérete (módosítás)** lehetőséget.
    
    ![Virtuálisgép-méret változásának kiválasztása az Áttekintés oldalon](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-1.png)

2. A **virtuális gép méretének módosítása** panelen a parancssorból válassza ki a **virtuális gép méretét** , majd kattintson a **módosítás** gombra.

    ![Új virtuális gép méretének kiválasztása](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-2.png)

3. Ekkor megjelenik egy értesítés arról, hogy a virtuális gép frissítése folyamatban van. A virtuális gép sikeres frissítése után az **áttekintő** oldal frissül, és megjeleníti az ÁTméretezett virtuális gépet.

    ![Átméretezett virtuális gép ](./media/azure-stack-edge-gpu-manage-virtual-machine-resize-portal/change-vm-size-3.png)


## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan helyezhet üzembe virtuális gépeket a Azure Stack Edge Pro-eszközön, tekintse meg [a virtuális gépek telepítése a Azure Portal segítségével](azure-stack-edge-gpu-deploy-virtual-machine-portal.md)című témakört.
