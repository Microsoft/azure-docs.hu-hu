---
title: Linuxos virtuális merevlemez letöltése az Azure-ból
description: Töltsön le egy linuxos virtuális merevlemezt az Azure CLI és a Azure Portal használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.date: 08/03/2020
ms.author: cynthn
ms.openlocfilehash: b3435d1dabf604cf7a1394c14ee62d65b923714b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565937"
---
# <a name="download-a-linux-vhd-from-azure"></a>Linuxos virtuális merevlemez letöltése az Azure-ból

Ebből a cikkből megtudhatja, hogyan tölthet le egy linuxos virtuális merevlemezt (VHD-fájlt) az Azure-ból a Azure Portal használatával. 

## <a name="stop-the-vm"></a>A virtuális gép leállítása

Egy virtuális merevlemez nem tölthető le az Azure-ból, ha egy futó virtuális géphez van csatlakoztatva. A virtuális merevlemez letöltéséhez le kell állítania a virtuális gépet. 

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
2.  A bal oldali menüben válassza a **Virtual Machines** lehetőséget.
3.  Válassza ki a virtuális gépet a listából.
4.  A virtuális gép lapján válassza a **Leállítás** lehetőséget.

    :::image type="content" source="./media/download-vhd/export-stop.PNG" alt-text="Megjeleníti a virtuális gép leállítására szolgáló menü gombot.":::

## <a name="generate-sas-url"></a>SAS URL-cím előállítása

A VHD-fájl letöltéséhez egy [közös hozzáférési aláírás (SAS)](../../storage/common/storage-sas-overview.md?toc=/azure/virtual-machines/windows/toc.json) URL-címét kell létrehoznia. Az URL-cím létrehozásakor a rendszer lejárati időt rendel az URL-címhez.

1. A virtuális gép oldalának menüjében válassza a **lemezek** lehetőséget.
2. Válassza ki a virtuális gép operációsrendszer-lemezét, majd válassza a **lemez exportálása** lehetőséget.
1. Ha szükséges, frissítse az **URL-cím érvényességét (másodpercben)** , hogy elegendő idő legyen a letöltés befejezésére. Az alapértelmezett érték 3600 másodperc (egy óra).
3. Válassza az **URL-cím előállítása** lehetőséget.
 
      
## <a name="download-vhd"></a>VHD letöltése

1.  A létrehozott URL-cím alatt válassza **a VHD-fájl letöltése** lehetőséget.

    :::image type="content" source="./media/download-vhd/export-download.PNG" alt-text="Megjeleníti a virtuális merevlemez letöltésére szolgáló gombot.":::

2.  Előfordulhat, hogy a letöltés indításához a böngésző **Mentés** elemét kell választania. A VHD-fájl alapértelmezett neve *ABCD*.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [tölthet fel és hozhat létre Linux rendszerű virtuális gépet egyéni lemezről az Azure CLI-vel](upload-vhd.md). 
- [Azure-lemezek kezelése az Azure CLI](tutorial-manage-disks.md)-vel.
