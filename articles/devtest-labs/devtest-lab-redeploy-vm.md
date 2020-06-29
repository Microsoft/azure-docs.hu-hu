---
title: Virtuális gép újbóli üzembe helyezése laborban Azure DevTest Labsban | Microsoft Docs
description: Megtudhatja, hogyan telepítheti át a virtuális gépeket (az egyik Azure-csomópontról a másikra) Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da0edf13adaa0d7ecd84ee2c190f376c19b398db
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85480235"
---
# <a name="redeploy-a-vm-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép újbóli üzembe helyezése laborban Azure DevTest Labs
Ha nem tud csatlakozni egy virtuális géphez a laborban távoli asztali kapcsolaton keresztül, telepítse újra a virtuális gépet, és próbálkozzon újra a csatlakozással. Egy virtuális gép újratelepítésekor a DevTest Labs áthelyezi a virtuális gépet abból a csomópontból, amelyen az Azure-infrastruktúrán belüli új csomópontra fut. Ezután elindítja a virtuális gépet, miközben megőrzi az összes konfigurációs beállítást és a kapcsolódó erőforrásokat. Ez a szolgáltatás időt takarít meg a távoli asztali kapcsolat vagy az alkalmazás Windows-alapú virtuális gépekhez való hozzáférésének hibaelhárítása során. 

## <a name="steps-to-redeploy-a-vm-in-a-lab"></a>A virtuális gépek laboratóriumi üzembe helyezésének lépései 
Egy virtuális gép Azure DevTest Labs tesztkörnyezetben való újratelepítéséhez hajtsa végre a következő lépéseket: 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A Labs listából válassza ki azt a labort, amely tartalmazza az újratelepíteni kívánt virtuális gépet.  
4. A bal oldali panelen válassza a **saját Virtual Machines**elemet. 
5. A virtuális gépek listájából válasszon ki egy virtuális gépet.
6. A virtuális gép virtuálisgép-lapján válassza a bal oldali menü **műveletek** területén található **újratelepítés** lehetőséget.

    ![Ismételt üzembe helyezés](media/devtest-lab-redeploy-vm/redeploy.png)
7. Olvassa el az oldalon található információkat, majd kattintson az újbóli **üzembe helyezés** gombra. 9. Az ismételt üzembe helyezési művelet állapotát az **értesítések** ablakban tekintheti meg.

    ![Állapot újbóli üzembe helyezése](media/devtest-lab-redeploy-vm/redeploy-status.png)

## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan méretezheti át a virtuális gépeket a Azure DevTest Labsban: [virtuális gép](devtest-lab-resize-vm.md)átméretezése.


