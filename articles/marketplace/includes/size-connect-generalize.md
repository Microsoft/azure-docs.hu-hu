---
title: fájl belefoglalása
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 03/25/2021
ms.openlocfilehash: 8898a762e8a1e7a2d5c104f99d12032c676a5ca4
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629852"
---
## <a name="generalize-the-image"></a>A rendszerkép általánosítása

Az Azure Marketplace-en lévő összes lemezképet általános módon újrafelhasználhatónak kell lennie. Ennek eléréséhez általánosítva kell lennie az operációs rendszer virtuális merevlemezének, egy olyan műveletnek, amely eltávolítja az összes példány-specifikus azonosítót és a szoftver illesztőprogramjait egy virtuális gépről.

### <a name="for-windows"></a>Windows esetén

A Windows operációsrendszer-lemezek általánosítva vannak a [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) eszközzel. Ha később frissíti vagy újrakonfigurálja az operációs rendszert, újra kell futtatnia a Sysprep programot.

> [!WARNING]
> A Sysprep futtatása után kapcsolja ki a virtuális gépet az üzembe helyezésig, mert a frissítések automatikusan futtathatók. Ez a Leállítás megakadályozza, hogy a későbbi frissítések az operációs rendszer vagy a telepített szolgáltatások példány-specifikus módosításait is elvégezzék. A Sysprep futtatásával kapcsolatos további információkért tekintse [meg a virtuális merevlemez általánosítása című témakör lépéseit](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep).

### <a name="for-linux"></a>Linux esetén

A következő folyamat általánosít egy Linux rendszerű virtuális gépet, és újratelepíti külön virtuális gépre. Részletekért lásd: [virtuális gépek vagy VHD-k rendszerképének létrehozása](../../virtual-machines/linux/capture-image.md). Ha eléri a "virtuális gép létrehozása a rögzített lemezképből" szakaszt, akkor leállíthatja.

1. Távolítsa el az Azure Linux-ügynököt.
    1. Csatlakozhat a linuxos virtuális géphez egy SSH-ügyfél használatával.
    2. Az SSH ablakban adja meg a következő parancsot: `sudo waagent –deprovision+user` .
    3. A folytatáshoz írja be az Y betűt (a-Force paramétert hozzáadhatja az előző parancshoz, így elkerülhető a megerősítő lépés).
    4. A parancs befejezése után a **Kilépés** gombra kattintva zárja be az SSH-ügyfelet.
2. Állítsa le a virtuális gépet.
    1. A Azure Portal válassza ki az erőforráscsoportot (RG) és a virtuális gép lefoglalását.
    2. A virtuális gép már általánosítva van, és létrehozhat egy új virtuális gépet a virtuálisgép-lemez használatával.

### <a name="capture-image"></a>Rendszerkép rögzítése

Miután a virtuális gép elkészült, rögzítheti azt egy Azure-beli megosztott rendszerkép-katalógusban. A rögzítéshez kövesse az alábbi lépéseket:

1. [Azure Portalon](https://ms.portal.azure.com/)nyissa meg a virtuális gép lapját.
2. Válassza a **rögzítés** lehetőséget.
3. A **rendszerkép megosztása megosztott képtárban** területen válassza az **Igen, a képverzióként megoszthatja a gyűjteményt a** katalógusban.
4. Az **operációs rendszer állapota** területen válassza az általánosítva lehetőséget.
5. Válasszon ki egy cél képtárat, vagy **hozzon létre újat**.
6. Válassza ki a cél rendszerkép definícióját, vagy **hozzon létre újat**.
7. Adja meg a rendszerkép **verziószámát** .
8. Válassza a **felülvizsgálat + létrehozás** lehetőséget a választási lehetőségek áttekintéséhez.
9. Az érvényesítés után válassza a **Létrehozás** lehetőséget.

A SIG-t tartalmazó Azure-előfizetésnek ugyanannak a Bérlőnek kell lennie, mint a közzétevő fióknak a közzétételhez. Emellett a közzétevő fióknak tulajdonosi hozzáféréssel kell rendelkeznie a SIG-hoz. 

Hozzáférés biztosítása:

1. Nyissa meg a megosztott képtárat.
2. A bal oldali panelen válassza a **hozzáférés-vezérlés** (iam) lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget, majd **adja hozzá a szerepkör-hozzárendelést**.
4. Válasszon egy **szerepkört** vagy **tulajdonost**.
5. **A hozzáférés társítása lehetőségnél** válassza a **felhasználó, csoport vagy egyszerű szolgáltatásnév** lehetőséget.
6. Válassza ki a képet közzétevő személy Azure-beli e-mail-címét.
7. Kattintson a **Mentés** gombra.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Megjeleníti a szerepkör-hozzárendelés hozzáadása ablakot.":::

> [!NOTE]
> Nem kell SAS URI-ket létrehoznia, mivel most már közzétehet egy SIG-rendszerképet a partner Centerben. Ha azonban továbbra is szüksége van a SAS URI-létrehozási lépéseire, tekintse meg a következőt: [sas URI létrehozása virtuálisgép-rendszerképhez](../azure-vm-get-sas-uri.md).
