---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/04/2020
ms.author: alkohli
ms.openlocfilehash: 6e42fef2aa4415373ed0bac39284f36ba330a4d9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "101730647"
---
Kapcsolódjon a Windows rendszerű virtuális géphez a virtuális gép létrehozása során átadott IP-címen keresztül a RDP protokoll (RDP) használatával.

1. Az ügyfélszámítógépen nyissa meg az RDP-t. 
1. Lépjen a **Start menüre**, és írja be az **mstsc** parancsot.
1. A **Távoli asztali kapcsolat** ablaktáblán adja meg a virtuális gép IP-címét és a virtuálisgép-sablon paramétereinek fájljában használt hozzáférési hitelesítő adatokat, majd válassza a **Kapcsolódás** lehetőséget.

   ![Képernyőkép a Távoli asztali kapcsolat panelről, amely RDP-kapcsolaton keresztül csatlakozik a Windows rendszerű virtuális géphez.](media/azure-stack-edge-gateway-connect-vm-windows/connect-vm-rdp-1.png)

   > [!NOTE]
   > Előfordulhat, hogy jóvá kell hagynia a nem megbízható géphez való csatlakozást. 

Most bejelentkezett a készüléken futó virtuális gépre. 
