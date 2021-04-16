---
title: Linux rendszerű virtuális merevlemez letöltése az Azure-ból
description: Linux virtuális merevlemez letöltése az Azure CLI és a Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: 8def06990b72d6e08127e8c4f16e0dfd87905d4f
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2021
ms.locfileid: "107565185"
---
# <a name="download-a-linux-vhd-from-azure"></a>Linux rendszerű virtuális merevlemez letöltése az Azure-ból

Ebből a cikkből megtudhatja, hogyan tölthet le Linux rendszerű virtuális merevlemezt (VHD-fájlt) az Azure-ból a Azure Portal. 

## <a name="stop-the-vm"></a>A virtuális gép leállítása

A virtuális merevlemezek nem tölthetők le az Azure-ból, ha egy futó virtuális géphez vannak csatolva. Ha továbbra is futni szeretné a virtuális gépet, létrehozhat egy pillanatképet, [majd letöltheti a pillanatképet.](#alternative-snapshot-the-vm-disk)

A virtuális gép leállítása:

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  A bal oldali menüben válassza **a** Virtual Machines lehetőséget.
3.  Válassza ki a virtuális gépet a listából.
4.  A virtuális gép lapján válassza a Leállítás **lehetőséget.**

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Megjeleníti a virtuális gép leállítására a menügombot.":::

### <a name="alternative-snapshot-the-vm-disk"></a>Alternatív megoldás: A virtuálisgép-lemez pillanatképének készítése

Pillanatfelvétel készítése a lemezről a letöltéshez.

1. Válassza ki a virtuális gépet a [portálon.](https://portal.azure.com)
2. A **bal oldali menüben** válassza a Lemezek lehetőséget, majd válassza ki a pillanatképet készíteni kívánt lemezt. Megjelenik a lemez részletei.  
3. Az **oldal tetején** található menüben válassza a Pillanatkép létrehozása lehetőséget. Megnyílik **a Pillanatkép** létrehozása lap.
4. A **Név mezőbe** írja be a pillanatkép nevét. 
5. A **Pillanatkép típusa beállításhoz** válassza a **Teljes vagy** a **Növekményes lehetőséget.**
6. Ha végzett, válassza az Áttekintés **+ létrehozás lehetőséget.**

A pillanatkép hamarosan létrejön, és később egy másik virtuális gép letöltésére vagy létrehozására is használható.

> [!NOTE]
> Ha nem állítsa le először a virtuális gépet, a pillanatkép nem lesz tiszta. A pillanatkép ugyanabban az állapotban lesz, mintha a virtuális gép áramkimaradást vagy összeomlott volna a pillanatkép készítésekor.  Bár általában biztonságos, problémákat okozhat, ha az idő alatt futó alkalmazások nem voltak összeomlás-ellen védettek.
>  
> Ez a módszer csak olyan virtuális gépekhez ajánlott, amelyek egyetlen operációsrendszer-lemezzel vannak. Az egy vagy több adatlemezzel lévő virtuális gépeket le kell állítani a letöltés előtt, vagy mielőtt pillanatképet hoznak létre az operációsrendszer-lemezről és az egyes adatlemezekről.

## <a name="generate-sas-url"></a>SAS URL-cím létrehozása

A VHD-fájl letöltéséhez létre kell hoznia egy közös hozzáférésű jogosultság [signature (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL-címet. Az URL-cím generálása után lejárati idő lesz hozzárendelve az URL-címhez.

1. A virtuális gép oldalának menüjében válassza a **Lemezek lehetőséget.**
2. Válassza ki a virtuális gép operációsrendszer-lemezét, majd válassza a **Lemez exportálása lehetőséget.**
1. Ha szükséges, frissítse az URL-cím lejárati idejét **(másodpercben),** hogy elegendő időt adjon a letöltés befejezésére. Az alapértelmezett érték 3600 másodperc (egy óra).
3. Válassza az **URL-cím létrehozása lehetőséget.**
 
      
## <a name="download-vhd"></a>VHD letöltése

1.  A létrehozott URL-cím alatt válassza a **VhD-fájl letöltése lehetőséget.**

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Megjeleníti a VHD letöltésére használt gombot.":::

2.  Előfordulhat, hogy a letöltéshez **a böngészőben** a Mentés lehetőséget kell választania. A VHD-fájl alapértelmezett neve *abcd.*

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan tölthet fel és hozhat létre Linux rendszerű virtuális gépet [egyéni lemezről az Azure CLI használatával.](upload-vhd.md) 
- [Azure-lemezek kezelése az Azure CLI használatával.](tutorial-manage-disks.md)
