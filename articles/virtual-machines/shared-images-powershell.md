---
title: Közös rendszerkép-katalógus létrehozása Azure PowerShell
description: Ismerje meg, hogyan hozhat létre egy megosztott képtárat az Azure-ban a Azure PowerShell használatával
author: cynthn
ms.service: virtual-machines
ms.subservice: shared-image-gallery
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/04/2020
ms.author: cynthn
ms.reviewer: akjosh
ms.openlocfilehash: 877439ddba6ffaf2fd060c29f28c25c18694605d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102555567"
---
# <a name="create-a-shared-image-gallery-with-azure-powershell"></a>Közös rendszerkép-katalógus létrehozása Azure PowerShell 

A [megosztott képgyűjtemény](./shared-image-galleries.md) egyszerűbbé teszi a szervezeten belüli Egyéni rendszerképek megosztását. Az egyéni rendszerképek olyanok, mint a piactérről beszerzett rendszerképek, de Ön hozza azokat létre. Az egyéni lemezképek olyan központi telepítési feladatok indítására használhatók, mint az alkalmazások, az alkalmazások konfigurációja és más operációsrendszer-konfigurációk. 

A megosztott képkatalógus lehetővé teszi az egyéni virtuálisgép-rendszerképek megosztását a szervezeten belül vagy régiókban, egy HRE-bérlőn belül. Válassza ki a megosztani kívánt képeket, mely régiókat szeretné elérhetővé tenni a alkalmazásban, és hogy kivel szeretné megosztani azokat. Több gyűjteményt is létrehozhat, hogy logikailag csoportosítsa a megosztott rendszerképeket. 

A gyűjtemény egy legfelső szintű erőforrás, amely teljes körű Azure szerepköralapú hozzáférés-vezérlést (Azure RBAC) biztosít. A lemezképek telepíthetők, és eldöntheti, hogy az egyes lemezkép-verziókat egy másik Azure-régióba replikálja-e. A katalógus csak felügyelt lemezképekkel működik.

A megosztott képkatalógus funkció több erőforrástípust is tartalmaz. 

[!INCLUDE [virtual-machines-shared-image-gallery-resources](../../includes/virtual-machines-shared-image-gallery-resources.md)]


[!INCLUDE [virtual-machines-common-shared-images-powershell](../../includes/virtual-machines-common-shared-images-powershell.md)]


## <a name="next-steps"></a>Következő lépések

Hozzon létre egy rendszerképet egy [virtuális](image-version-vm-powershell.md)gépről, egy [felügyelt rendszerképből](image-version-managed-image-powershell.md)vagy egy [másik gyűjteményből származó képpel](image-version-another-gallery-powershell.md).

Az [Azure rendszerkép-szerkesztő (előzetes verzió)](./image-builder-overview.md) segítségével automatizálhatja a rendszerkép-verziók létrehozását, és [egy meglévő rendszerkép-verzióból](./windows/image-builder-gallery-update-image-version.md)is frissítheti és létrehozhatja az új rendszerkép verzióját. 

Sablonok használatával is létrehozhat megosztott képgyűjteményi erőforrásokat. Több Azure Gyorsindítás-sablon is elérhető: 

- [Shared Image Gallery létrehozása](https://azure.microsoft.com/resources/templates/101-sig-create/)
- [Rendszerkép-definíció létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-definition-create/)
- [Rendszerképverzió létrehozása Shared Image Galleryben](https://azure.microsoft.com/resources/templates/101-sig-image-version-create/)
- [Virtuális gép létrehozása rendszerképverzióból](https://azure.microsoft.com/resources/templates/101-vm-from-sig/)