---
title: Windows rendszerű virtuális merevlemez letöltése az Azure-ból
description: Töltsön le egy Windows vhD-t a Azure Portal.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: disks
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 01/13/2019
ms.author: cynthn
ms.openlocfilehash: 32b9753b79273ce747d00cba077dd8a5ee6d724d
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565287"
---
# <a name="download-a-windows-vhd-from-azure"></a>Windows rendszerű virtuális merevlemez letöltése az Azure-ból

Ebből a cikkből megtudhatja, hogyan tölthet le Windows rendszerű virtuális merevlemezfájlt (VHD) az Azure-ból a Azure Portal.

## <a name="optional-generalize-the-vm"></a>Nem kötelező: A virtuális gép általánosizálása

Ha a VHD-t rendszerképként szeretné használni más virtuális gépek létrehozásához, a [Sysprep](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation) segítségével általánosíthatja az operációs rendszert. [](tutorial-custom-images.md) Ellenkező esetben minden létrehozni kívánt virtuális gépről másolatot kell készítsen a lemezről.

Ahhoz, hogy a VHD-t rendszerképként használva más virtuális gépeket hoz létre, általánosíthatja a virtuális gépet.

1. Ha még nem tette meg, jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2. [Csatlakozzon a virtuális géphez.](connect-logon.md) 
3. A virtuális gépen nyissa meg a parancssori ablakot rendszergazdaként.
4. Módosítsa a könyvtárat *a %windir%\system32\sysprep* könyvtárra, és futtassa a sysprep.exe.
5. A Rendszer-előkészítési eszköz párbeszédpanelen válassza az **Enter System Out-of-Box Experience (OOBE) lehetőséget,** és győződjön meg arról, hogy az **Általánosizálás** elem van kiválasztva.
6. A Leállítási beállítások között válassza a **Leállítás lehetőséget,** majd kattintson az **OK gombra.** 

Ha nem szeretné általánosítani a jelenlegi virtuális gépet, akkor is létrehozhat [](#alternative-snapshot-the-vm-disk)általános rendszerképet, ha először pillanatképet készít az operációsrendszer-lemezről, létrehoz egy új virtuális gépet a pillanatképből, majd általánosítja a másolatot.

## <a name="stop-the-vm"></a>A virtuális gép leállítása

A virtuális merevlemezek nem tölthetők le az Azure-ból, ha egy futó virtuális géphez vannak csatolva. Ha továbbra is futni szeretné a virtuális gépet, létrehozhat egy pillanatképet, [majd letöltheti a pillanatképet.](#alternative-snapshot-the-vm-disk)

1. A központi menüben kattintson a Azure Portal **elemre, Virtual Machines.**
1. Válassza ki a virtuális gépet a listából.
1. A virtuális gép panelen kattintson a Leállítás **gombra.**

### <a name="alternative-snapshot-the-vm-disk"></a>Alternatív megoldás: A virtuálisgép-lemez pillanatképének készítése

Pillanatfelvétel készítése a lemezről a letöltéshez.

1. Válassza ki a virtuális gépet a [portálon.](https://portal.azure.com)
2. A **bal oldali menüben** válassza a Lemezek lehetőséget, majd válassza ki a pillanatképet készíteni kívánt lemezt. Megjelenik a lemez részletei.  
3. Az **oldal tetején** található menüben válassza a Pillanatkép létrehozása lehetőséget. Megnyílik **a Pillanatkép** létrehozása lap.
4. A **Név mezőbe** írja be a pillanatkép nevét. 
5. A **Pillanatkép típusa beállításhoz** válassza a **Teljes vagy** a **Növekményes lehetőséget.**
6. Ha végzett, válassza a **Felülvizsgálat + létrehozás lehetőséget.**

A pillanatkép hamarosan létrejön, és később felhasználható egy másik virtuális gép letöltésére vagy létrehozására.

> [!NOTE]
> Ha nem állítja le először a virtuális gépet, a pillanatkép nem lesz tiszta. A pillanatkép ugyanabban az állapotban lesz, mintha a virtuális gép energiagazdálkodása vagy összeomlása történt volna a pillanatkép készítésekor.  Bár általában biztonságos, problémákat okozhat, ha az idő alatt futó alkalmazások nem voltak összeomlásbiztosak.
>  
> Ez a módszer csak olyan virtuális gépekhez ajánlott, amelyeken egyetlen operációsrendszer-lemez található. Az egy vagy több adatlemezzel lévő virtuális gépeket le kell állítani a letöltés előtt, vagy mielőtt pillanatképet hoznak létre az operációsrendszer-lemezről és az egyes adatlemezekről.

## <a name="generate-download-url"></a>Letöltési URL-cím létrehozása

A VHD-fájl letöltéséhez létre kell hoznia egy közös hozzáférésű jogosultság [jogosultsága (SAS) URL-címét.](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) Az URL-cím generálása után lejárati idő lesz hozzárendelve az URL-címhez.

1. A virtuális gép lapján kattintson a **bal oldali menüben** a Lemezek elemre.
1. Válassza ki a virtuális gép operációsrendszer-lemezét.
1. A lemez oldalán válassza a bal **oldali menü Lemez** exportálása parancsát.
1. Az URL-cím alapértelmezett lejárati ideje *3600* másodperc (egy óra). Előfordulhat, hogy ezt növelnie kell a Windows operációsrendszer-lemezek vagy a nagy méretű adatlemezek esetén. **36000** másodperc (10 óra) általában elegendő.
1. Kattintson az **URL-cím létrehozása elemre.**

> [!NOTE]
> A lejárati idő az alapértelmezett értékről megnövelve elegendő időt biztosít a nagyméretű VHD-fájl letöltéséhez a Windows Server operációs rendszer számára. A nagyméretű virtuális merevlemezek letöltése a kapcsolattól és a virtuális gép méretétől függően több órát is igénybe vehet. 
> 
> 

## <a name="download-vhd"></a>VHD letöltése

1. A létrehozott URL-cím alatt kattintson a VHD-fájl letöltése elemre.
1. Előfordulhat, hogy a **letöltéshez** a böngészőben a Mentés gombra kell kattintenie. A VHD-fájl alapértelmezett neve *abcd.*

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [tölthet fel VHD-fájlt az Azure-ba.](upload-generalized-managed.md) 
- [Felügyelt lemezek létrehozása nem felügyelt lemezekből egy tárfiókban.](attach-disk-ps.md)
- [Azure-lemezek kezelése a PowerShell használatával.](tutorial-manage-data-disk.md)
