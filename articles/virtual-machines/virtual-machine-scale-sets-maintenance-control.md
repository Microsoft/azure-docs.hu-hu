---
title: Az operációs rendszer rendszerképének frissítésével kapcsolatos karbantartási ellenőrzés áttekintése Azure-beli virtuálisgép-méretezési csoportokban
description: Megtudhatja, hogyan szabályozhatja, hogy az operációs rendszer rendszerképének automatikus frissítése hogyan történjen az Azure-beli virtuálisgép-méretezési csoportokban a karbantartási vezérlők használatával.
author: ju-shim
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/11/2020
ms.author: jushiman
ms.openlocfilehash: 55cbc5db534dd1b05f91a24e0104b1f2dc110547
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "90532638"
---
# <a name="preview-maintenance-control-for-azure-virtual-machine-scale-sets"></a>Előzetes verzió: Azure-beli virtuálisgép-méretezési csoportok karbantartási ellenőrzése 

A virtuálisgép-méretezési csoportok [automatikus operációsrendszer-rendszerkép-frissítéseinek](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md) kezelése karbantartási vezérlő használatával.

A karbantartási vezérléssel eldöntheti, hogy mikor alkalmazza a frissítéseket az operációsrendszer-lemezekre a virtuálisgép-méretezési csoportokban a könnyebb és kiszámítható felhasználói élmény használatával. 

A karbantartási konfigurációk az előfizetések és az erőforráscsoportok között működnek.

A teljes munkafolyamat ezekből a lépésekből áll: 
- Hozzon létre egy karbantartási konfigurációt.
- Virtuálisgép-méretezési csoport hozzárendelése karbantartási konfigurációhoz.
- Az operációs rendszer automatikus frissítéseinek engedélyezése.

> [!IMPORTANT]
> Az operációsrendszer-képek Azure-beli virtuálisgép-méretezési csoportokon történő frissítésének karbantartási ellenőrzése jelenleg nyilvános előzetes verzióban érhető el.
> Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk, és éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="limitations"></a>Korlátozások

- A virtuális gépeknek méretezési csoportba kell esniük.
- A felhasználónak **erőforrás-közreműködői** hozzáféréssel kell rendelkeznie.
- A karbantartási konfigurációban a karbantartási időtartamnak 5 óra vagy hosszabbnak kell lennie.
- A karbantartási konfigurációban az ismétlődést a "Day" értékre kell beállítani


## <a name="management-options"></a>Felügyeleti beállítások

A karbantartási konfigurációkat a következő lehetőségek bármelyikével hozhatja létre és kezelheti:

- [Azure PowerShell](virtual-machine-scale-sets-maintenance-control-powershell.md)


## <a name="next-steps"></a>Következő lépések

Megtudhatja, hogyan szabályozhatja, hogy az Azure-beli virtuálisgép-méretezési csoportokra hogyan kell karbantartani a karbantartást és a PowerShellt.

> [!div class="nextstepaction"]
> [Virtuálisgép-méretezési csoport karbantartási vezérlője a PowerShell használatával](virtual-machine-scale-sets-maintenance-control-powershell.md)
