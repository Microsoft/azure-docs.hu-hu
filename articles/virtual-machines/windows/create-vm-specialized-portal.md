---
title: Windows rendszerű virtuális gép létrehozása speciális virtuális merevlemezről a Azure Portal
description: Hozzon létre egy új Windows rendszerű virtuális gépet egy virtuális merevlemezen a Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/18/2019
ms.author: cynthn
ms.openlocfilehash: a93ab207960133ecbf5a68927c5d16a6daf94fd4
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102562214"
---
# <a name="create-a-vm-from-a-vhd-by-using-the-azure-portal"></a>Virtuális gép létrehozása virtuális merevlemezről a Azure Portal használatával

A virtuális gépek (VM-EK) több módon is létrehozhatók az Azure-ban: 

- Ha a virtuális merevlemezt (VHD) már használja, vagy ha a virtuális merevlemezt egy meglévő virtuális gépről kívánja átmásolni, hozzon létre egy új virtuális gépet úgy, hogy az új virtuális GÉPRE rendszerlemezként *csatolja* a VHD-t. 

- Létrehozhat egy új virtuális gépet egy olyan virtuális gép virtuális merevlemezéről, amely törölve lett. Ha például egy olyan Azure-beli virtuális gép van, amely nem megfelelően működik, törölheti a virtuális gépet, és használhatja a virtuális merevlemezt egy új virtuális gép létrehozásához. Felhasználhatja ugyanazt a VHD-t, vagy létrehozhat egy másolatot a virtuális merevlemezről egy pillanatkép létrehozásával, majd egy új felügyelt lemez létrehozásával a pillanatképből. Bár a pillanatkép létrehozása néhány további lépést is igénybe vehet, megőrzi az eredeti VHD-t, és tartalékot biztosít.

- Hozzon létre egy klasszikus virtuális gépet, és használja a VHD-t a Resource Manager-alapú üzemi modellt és a felügyelt lemezeket használó új virtuális gép létrehozásához. A legjobb eredmény érdekében **állítsa le** a klasszikus virtuális gépet a Azure Portal a pillanatkép létrehozása előtt.
 
- Létrehozhat egy Azure-beli virtuális gépet egy helyszíni virtuális merevlemezről a helyszíni VHD feltöltésével és egy új virtuális géphez való csatolásával. A PowerShell vagy egy másik eszköz használatával feltöltheti a virtuális merevlemezt egy Storage-fiókba, majd létrehoz egy felügyelt lemezt a VHD-ből. További információ: [speciális virtuális merevlemez feltöltése](create-vm-specialized.md#option-2-upload-a-specialized-vhd). 

> [!IMPORTANT]
> 
> Ha speciális lemezt használ egy új virtuális gép létrehozásához, az új virtuális gép megőrzi az eredeti virtuális gép számítógépnevét. A számítógépekre vonatkozó egyéb információk (például a CMID) is megmaradnak, és bizonyos esetekben az ismétlődő információk problémákat okozhatnak. A virtuális gépek másolásakor vegye figyelembe, hogy az alkalmazásai milyen típusú számítógép-specifikus információkat használnak.  
> Ezért ne használjon speciális lemezt, ha több virtuális gépet szeretne létrehozni. Ehelyett nagyobb telepítések esetén [hozzon létre egy rendszerképet](capture-image-resource.md) , majd [ezt a rendszerképet használva hozzon létre több virtuális](create-vm-generalized-managed.md)gépet.

Azt javasoljuk, hogy egyetlen pillanatképből vagy VHD-ből korlátozza az egyidejű központi telepítések számát 20 virtuális gépre. 

## <a name="copy-a-disk"></a>Lemez másolása

Hozzon létre egy pillanatképet, majd hozzon létre egy lemezt a pillanatképből. Ez a stratégia lehetővé teszi az eredeti VHD tartalékként való megőrzését:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden szolgáltatás** lehetőséget.
2. A **minden szolgáltatás** keresési mezőjébe írja be a **lemezek** elemet, majd válassza a **lemezek** lehetőséget az elérhető lemezek listájának megjelenítéséhez.
3. Válassza ki a használni kívánt lemezt. Megjelenik a **lemez lapja.**
4. A felső menüben válassza a **pillanatkép létrehozása** lehetőséget. 
5. Adja meg a pillanatkép **nevét** .
6. Válasszon **erőforráscsoportot** a pillanatképhez. Használhat meglévő erőforráscsoportot is, vagy létrehozhat egy újat.
7. A **fióktípus mezőben** válassza a **Standard (HDD)** vagy a **prémium (SSD)** tárolót.
8. Ha elkészült, válassza a **Létrehozás** lehetőséget a pillanatkép létrehozásához.
9. A pillanatkép létrehozása után válassza az **erőforrás létrehozása** lehetőséget a bal oldali menüben.
10. A keresőmezőbe írja be a **felügyelt lemez** kifejezést, majd válassza ki a **Managed Disks** elemet a listából.
11. A **Managed Disks** lapon válassza a **Létrehozás** lehetőséget.
12. Adja meg a lemez **nevét** .
13. Válasszon ki egy **erőforráscsoportot** a lemezhez. Használhat meglévő erőforráscsoportot is, vagy létrehozhat egy újat. Ez a kijelölés olyan erőforráscsoportként is használható, amelyben a virtuális gépet a lemezről hozza létre.
14. A **fióktípus mezőben** válassza a **Standard (HDD)** vagy a **prémium (SSD)** tárolót.
15. A **forrás típusa mezőben** ellenőrizze, hogy a **Pillanatkép** van-e kiválasztva.
16. A **forrás-pillanatkép** legördülő menüben válassza ki a használni kívánt pillanatképet.
17. Szükség szerint végezze el a szükséges módosításokat, majd válassza a **Létrehozás** lehetőséget a lemez létrehozásához.

## <a name="create-a-vm-from-a-disk"></a>Virtuális gép létrehozása lemezről

Miután a felügyelt lemez virtuális merevlemezét használni szeretné, a virtuális gépet a portálon is létrehozhatja:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **minden szolgáltatás** lehetőséget.
2. A **minden szolgáltatás** keresési mezőjébe írja be a **lemezek** elemet, majd válassza a **lemezek** lehetőséget az elérhető lemezek listájának megjelenítéséhez.
3. Válassza ki a használni kívánt lemezt. Megnyílik a **lemez lapja.**
4. Az **Áttekintés** lapon győződjön meg arról, hogy a **lemez állapota** nem **csatoltként** van felsorolva. Ha nem, akkor lehet, hogy le kell választania a lemezt a virtuális gépről, vagy törölnie kell a virtuális gépet, hogy felszabadítsa a lemezt.
4. A lap tetején található menüben válassza a **virtuális gép létrehozása** elemet.
5. Az új virtuális gép **alapjai** lapon adja meg a **virtuális gép nevét** , vagy válasszon ki egy meglévő **erőforráscsoportot** , vagy hozzon létre újat.
6. A méret lapon kattintson a méret **módosítása** **elemre** a **méret** lap eléréséhez.
7. Válasszon ki egy virtuálisgép-méretezési sort, majd válassza a **kiválasztás** lehetőséget.
8. A **hálózatkezelés** lapon engedélyezheti, hogy a portál minden új erőforrást hozzon létre, vagy kiválaszthat egy meglévő **virtuális hálózatot** és **hálózati biztonsági csoportot** is. A portál mindig létrehoz egy új hálózati adaptert és egy nyilvános IP-címet az új virtuális géphez. 
9. **A felügyeleti lapon** végezze el a figyelési beállítások módosítását.
10. A **vendég konfigurációja** lapon szükség szerint adja hozzá a kívánt bővítményeket.
11. Ha elkészült, válassza a **felülvizsgálat + létrehozás** elemet. 
12. Ha a virtuális gép konfigurációja ellenőrzi az ellenőrzést, válassza a **Létrehozás** lehetőséget a telepítés elindításához.


## <a name="next-steps"></a>Következő lépések

A PowerShell használatával is [feltöltheti a VHD-t az Azure-ba, és létrehozhat egy speciális virtuális gépet](create-vm-specialized.md).


