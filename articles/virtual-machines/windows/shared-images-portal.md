---
title: Azure közös rendszerkép-katalógus létrehozása a portál használatával
description: Megtudhatja, hogyan hozhat létre és oszthat meg virtuálisgép-lemezképeket a Azure Portal használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 11/06/2019
ms.author: cynthn
ms.openlocfilehash: e28a1412fa417b9f15ab7a6f79305d07ca6d9fec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556094"
---
# <a name="create-an-azure-shared-image-gallery-using-the-portal"></a>Azure közös rendszerkép-katalógus létrehozása a portál használatával

A [megosztott képgyűjtemény](../shared-image-galleries.md) egyszerűbbé teszi a szervezeten belüli Egyéni rendszerképek megosztását. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni lemezképek olyan központi telepítési feladatok indítására használhatók, mint az alkalmazások, az alkalmazások konfigurációja és más operációsrendszer-konfigurációk. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását a szervezeten belül vagy régiókban, egy HRE-bérlőn belül. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. Több gyűjteményt is létrehozhat, hogy logikailag csoportosítsa a megosztott rendszerképeket. 

A gyűjtemény egy legfelső szintű erőforrás, amely teljes körű Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít. A lemezképek telepíthetők, és eldöntheti, hogy az egyes lemezkép-verziókat egy másik Azure-régióba replikálja-e. A katalógus csak felügyelt lemezképekkel működik.

A megosztott képkatalógus funkció több erőforrástípust is tartalmaz. Ebben a cikkben a következő lépéseket fogjuk használni vagy felépíteni:


[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../../includes/virtual-machines-shared-image-gallery-resources.md)]

<br>


A cikkben végzett munka során szükség esetén cserélje le az erőforráscsoportot és a virtuális gépek nevét.


[!INCLUDE [virtual-machines-common-shared-images-portal](../../../includes/virtual-machines-common-shared-images-portal.md)]
 
## <a name="create-vms"></a>Virtuális gépek létrehozása

Most már létrehozhat egy vagy több új virtuális gépet. Ez a példa egy *myVM* nevű virtuális gépet hoz létre a *MyResourceGroup* az *USA keleti* adatközpontjában.

1. Nyissa meg a rendszerkép definícióját. Az erőforrás-szűrőt használhatja az összes elérhető képdefiníció megjelenítéséhez.
1. A rendszerkép definíciójának lapján válassza a **virtuális gép létrehozása** elemet az oldal tetején található menüből.
1. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget, és írja be a *myResourceGroup* nevet.
1. A **virtuális gép neve** mezőbe írja be a következőt: *myVM*.
1. A régió területen válassza az *USA keleti* **régiója** lehetőséget.
1. A **rendelkezésre állási beállítások beállításnál** hagyja meg az alapértelmezett *infrastruktúra-redundancia* hiányát.
1. A **rendszerkép értékét** a rendszer automatikusan kitölti a `latest` rendszerkép verziójával, ha a rendszerkép definíciójának oldaláról indult.
1. A **méret** beállításnál válassza ki a virtuális gép méretét az elérhető méretek listájából, majd válassza a **kiválasztás** lehetőséget.
1. Ha a rendszerkép általánosítva van, a **rendszergazdai fiók** területen meg kell adnia egy felhasználónevet, például az *azureuser* -t és a jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](faq.md#what-are-the-password-requirements-when-creating-a-vm). Ha a rendszerkép speciális volt, a Felhasználónév és a jelszó mezők szürkén jelennek meg, mert a forrás virtuális gép felhasználóneve és jelszava van használatban.
1. Ha engedélyezni szeretné a virtuális gép távoli elérését, a **nyilvános bejövő portok** területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd az **RDP (3389)** lehetőséget a legördülő menüből. Ha nem szeretné engedélyezni a virtuális gép távoli elérését, hagyja a **nincs** kiválasztva lehetőséget a **nyilvános bejövő portokhoz**.
1. Ha elkészült, kattintson a lap alján található **felülvizsgálat + létrehozás** gombra.
1. Miután a virtuális gép áthaladt az ellenőrzésen, válassza a **Létrehozás** lehetőséget a lap alján a telepítés elindításához.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. Ehhez válassza ki a virtuális gép erőforráscsoportját, kattintson a **Törlés** elemre, majd erősítse meg a törölni kívánt erőforráscsoport nevét.

Ha törölni szeretné az egyes erőforrásokat, akkor azokat fordított sorrendben kell törölnie. A rendszerkép definíciójának törléséhez például törölnie kell az adott rendszerképből létrehozott összes rendszerkép-verziót.

## <a name="next-steps"></a>Következő lépések

Sablonok használatával is létrehozhat megosztott képgyűjteményi erőforrásokat. Több Azure Gyorsindítás-sablon is elérhető: 

- [Shared Image Gallery létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerképverzió létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerképverzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)

A megosztott képtárakkal kapcsolatos további információkért tekintse meg az [áttekintést](../shared-image-galleries.md). Ha problémákba ütközik, tekintse meg a [megosztott képtárak hibaelhárítása](../troubleshooting-shared-images.md)című témakört.