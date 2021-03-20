---
title: Kapcsolódjon a Linux rendszerű virtuális gépekhez Azure DevTest Labs
description: Megtudhatja, hogyan csatlakozhat a linuxos virtuális géphez egy laborban (Azure DevTest Labs)
ms.topic: how-to
ms.date: 07/17/2020
ms.openlocfilehash: 52fe245f85034a4c6300615ad8fb6040c1168298
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "86531652"
---
# <a name="connect-to-a-linux-vm-in-your-lab-azure-devtest-labs"></a>Kapcsolódás Linux rendszerű virtuális géphez a laborban (Azure DevTest Labs)
Ez a cikk bemutatja, hogyan csatlakozhat a Linux rendszerű virtuális géphez a laborban. 

## <a name="connect-to-a-linux-vm"></a>Csatlakozás Linux rendszerű virtuális géphez
1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
1. A keresési sávban keresse meg és válassza ki a **DevTest Labs** elemet. 

    :::image type="content" source="./media/connect-linux-virtual-machine/search-select.png" alt-text="DevTest Labs keresése és kiválasztása":::    
1. A Labs listából válassza ki a **labort**.

    :::image type="content" source="./media/connect-linux-virtual-machine/select-lab.png" alt-text="Válassza ki a labort":::            
1. A labor kezdőlapján válassza ki a Linux rendszerű virtuális GÉPET a **saját virtuális gépek** listájából. 

    :::image type="content" source="./media/connect-linux-virtual-machine/select-linux-vm.png" alt-text="Linux rendszerű virtuális gép kiválasztása":::        
5. Az **Áttekintés** lapon megtekintheti a virtuális gép teljes tartománynevét (FQDN) vagy IP-címét. A portot a következő képen látható módon is megtekintheti.

    :::image type="content" source="./media/connect-linux-virtual-machine/vm-overview.png" alt-text="A virtuális gép teljes tartományneve":::    

    Figyelje meg, hogy a **csatlakozási** gomb szürkén jelenik meg annak ellenére, hogy a virtuális gép elindult. Ez a kialakítás.
6.  Használja az SSH-t a linuxos virtuális géphez való kapcsolódáshoz. A következő példa a virtuális géphez a teljes tartománynevet `mydtl07172452621450000.eastus.cloudapp.azure.com` , a felhasználónevet és a portot is csatlakoztatja `vmuser` `51637` . Adja meg azt a jelszót, amellyel a felhasználó csatlakozhat a virtuális géphez. 

    ```bash
    ssh vmuser@mydtl07172452621450000.eastus.cloudapp.azure.com -p 51637
    ```

    A virtuális géphez való kapcsolódáshoz olyan eszközöket is használhat, mint a [Putty](https://www.putty.org/) vagy bármely más SSH-ügyfél. 

    Miután az SSH-val kapcsolódott, telepítheti és konfigurálhatja az asztali környezetet ([Xfce](https://www.xfce.org)) és a Távoli asztalt ([xrdp](http://xrdp.org)).  Részletes információkért lásd: [Távoli asztal telepítése és konfigurálása egy Linux rendszerű virtuális géphez való kapcsolódáshoz az Azure-ban](../virtual-machines/linux/use-remote-desktop.md). 

## <a name="next-steps"></a>Következő lépések
[Útmutató: Kapcsolódás Windows rendszerű virtuális géphez](connect-windows-virtual-machine.md)
