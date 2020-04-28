---
title: Virtuális gép átméretezése laborban Azure DevTest Labs
description: Megtudhatja, hogyan módosíthatja a virtuális gép (VM) méretét a Azure DevTest Labs a processzor, a hálózat vagy a lemez teljesítményének igénye alapján.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 8460f09e-482f-48ba-a57a-c95fe8afa001
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/24/2020
ms.author: spelluru
ms.openlocfilehash: bf7c425766a97aaa1d143133f04502a0aa3c36cb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76756177"
---
# <a name="resize-a-vm-in-a-lab-in-azure-devtest-labs"></a>Virtuális gép átméretezése laborban Azure DevTest Labs
Az Azure Virtual Machines egyik fontos funkciója, hogy lehetővé teszi a virtuális gépek (VM-EK) méretének módosítását a CPU, a hálózat vagy a lemez teljesítményének megfelelően. Azure DevTest Labs támogatja ezt a funkciót a laborban lévő virtuális gépekhez. Az átméretezés funkció betartja a labor házirendet az engedélyezett virtuálisgép-méretekhez a laborban. Ez azt eredményezi, hogy a virtuális gép méretét csak a laborban engedélyezett méretekre lehet módosítani. 


## <a name="steps-to-resize-a-vm-in-a-lab"></a>A virtuális gépek tesztkörnyezetben való átméretezésének lépései 
A virtuális gépek Azure DevTest Labs tesztkörnyezetben való átméretezéséhez hajtsa végre a következő lépéseket: 

> [!NOTE]
> Ha távoli asztali munkameneten (RDP) keresztül csatlakozik a virtuális géphez, mentse a munkáját, és húzza le a virtuális gépet a méretezés előtt.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza a **minden szolgáltatás**lehetőséget, majd válassza ki a **DevTest Labs** elemet a listából.
3. A Labs listából válassza ki azt a labort, amely tartalmazza az átméretezni kívánt virtuális gépet.  
4. A bal oldali panelen válassza a **saját Virtual Machines**elemet. 
5. A virtuális gépek listájából válasszon ki egy virtuális gépet.
6. Ha a virtuális gép fut, válassza az eszköztár **Leállítás** elemét. Az **értesítések** ablakban tekintse meg a művelet állapotát. Várjon, amíg a virtuális gép leáll, majd az **értesítések** ablak bezárásához. 

    ![A virtuális gép leállítása](media/devtest-lab-resize-vm/stop-vm.png)
1. A virtuális gép virtuálisgép-lapján válassza a **méret** lehetőséget a bal oldali menü **Beállítások** területén.

    ![Méret menü](media/devtest-lab-resize-vm/size-menu.png)
1. A **méret kiválasztása** ablakban keresse meg és válassza ki a virtuális gép méretét, majd kattintson a **kiválasztás**gombra.     
1. Tekintse át az átméretezési művelet állapotát az **értesítések** ablakban.

    ![Állapot átméretezése](media/devtest-lab-resize-vm/resize-status.png)
10. Az átméretezési művelet sikeres elvégzése után lépjen be az **értesítések** ablakba. 
11. Válassza az **Áttekintés** lehetőséget a bal oldali menüben, majd kattintson az **Újraindítás** gombra az eszköztáron a virtuális gép újraindításához. 

## <a name="next-steps"></a>További lépések
Az Azure Virtual Machines által támogatott átméretezési funkcióval kapcsolatos részletes információkért lásd: [virtuális gépek átméretezése](https://azure.microsoft.com/blog/resize-virtual-machines/).


