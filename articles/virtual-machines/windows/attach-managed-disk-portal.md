---
title: Felügyelt adatlemez csatolása Windows rendszerű virtuális géphez – Azure
description: Felügyelt adatlemez csatlakoztatása Windows rendszerű virtuális géphez a Azure Portal használatával.
author: roygara
ms.service: virtual-machines
ms.collection: windows
ms.topic: how-to
ms.date: 02/06/2020
ms.author: rogarana
ms.subservice: disks
ms.openlocfilehash: 8c64b0ff5b7a9abfa58ec17d0ebcabe05b0ed6e9
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550807"
---
# <a name="attach-a-managed-data-disk-to-a-windows-vm-by-using-the-azure-portal"></a>Felügyelt adatlemez csatolása Windows rendszerű virtuális géphez a Azure Portal használatával

Ez a cikk bemutatja, hogyan csatolhat új felügyelt adatlemezt egy Windows rendszerű virtuális géphez (VM) a Azure Portal használatával. A virtuális gép mérete határozza meg, hány adatlemezt tud csatlakoztatni. További információ: [virtuális gépek méretei](../sizes.md).


## <a name="add-a-data-disk"></a>Adatlemez hozzáadása

1. Adatlemez hozzáadásához nyissa meg a [Azure Portal](https://portal.azure.com) . Keresse meg és válassza ki a **virtuális gépeket**.
2. Válasszon ki egy virtuális gépet a listából.
3. A **virtuális gép** lapon válassza a **lemezek** lehetőséget.
4. A **lemezek** oldalon válassza az **adatlemez hozzáadása** elemet.
5. Az új lemez legördülő menüjében válassza a **lemez létrehozása** lehetőséget.
6. A **felügyelt lemez létrehozása** lapon írja be a lemez nevét, és szükség szerint módosítsa a többi beállítást. Ha elkészült, válassza a **Létrehozás** lehetőséget.
7. A **lemezek** lapon válassza a **Mentés** lehetőséget a virtuális gép új lemez-konfigurációjának mentéséhez.
8. Miután az Azure létrehozta a lemezt, és csatolja a virtuális géphez, az új lemez megjelenik a virtuális gép lemez-beállításaiban az **adatlemezek** területen.


## <a name="initialize-a-new-data-disk"></a>Új adatlemez inicializálása

1. Csatlakozzon a virtuális géphez.
1. Válassza ki a Windows **Start** menüt a futó virtuális gépen, és írja be a **diskmgmt. msc** kifejezést a keresőmezőbe. Megnyílik a **Lemezkezelés** konzol.
2. A Lemezkezelés felismeri, hogy új, nem inicializált lemezzel és a **lemez inicializálása** ablak jelenik meg.
3. Ellenőrizze, hogy az új lemez van-e kiválasztva, majd kattintson **az OK gombra** az inicializáláshoz.
4. Az új lemez nem **lefoglaltként** jelenik meg. Kattintson a jobb gombbal a lemezen bárhová, és válassza az **új egyszerű kötet** lehetőséget. Megnyílik az **új egyszerű kötet varázsló** ablak.
5. Folytassa a varázslót, és őrizze meg az összes alapértelmezett értéket, és ha elkészült, válassza a **Befejezés** gombot.
6. A **Lemezkezelés** ablak bezárásához.
7. Megjelenik egy előugró ablak, amely értesíti, hogy az új lemezt a használata előtt kell formázni. Válassza a **lemez formázása** lehetőséget.
8. Az **új lemez formázása** ablakban jelölje be a beállításokat, majd kattintson a **Start** gombra.
9. Megjelenik egy figyelmeztetés, amely értesíti, hogy a lemezek formázása minden adat törlődik. Válassza az **OK** lehetőséget.
10. Ha a formázás elkészült, kattintson **az OK gombra**.

## <a name="next-steps"></a>Következő lépések

- Az [adatlemezeket a PowerShell használatával is csatlakoztathatja](attach-disk-ps.md).
- Ha az alkalmazásnak a *D:* meghajtót kell használnia az adattároláshoz, [módosíthatja a Windows ideiglenes lemez meghajtóbetűjelét](change-drive-letter.md).