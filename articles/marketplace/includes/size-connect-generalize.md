---
title: fájl belefoglalása
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: e119d40cd0b8f482d33c3c86c644cf6a0846390a
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727121"
---
## <a name="generalize-the-image"></a>A rendszerkép általánosizálása

A rendszerképnek Azure Marketplace általános módon újrahasználhatónak kell lennie. Ehhez az operációs rendszer virtuális merevlemezét általánosított művelettel kell elérni, amely eltávolítja az összes példányspecifikus azonosítót és szoftverillesztőt a virtuális gépről.

### <a name="for-windows"></a>Windows esetén

A Windows operációsrendszer-lemezek általánosított rendszerek a [sysprep eszközzel.](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) Ha később frissíti vagy újrakonfigurálja az operációs rendszert, újra kell futtatnia a sysprepet.

> [!WARNING]
> A sysprep futtatása után kapcsolja ki a virtuális gépet, amíg üzembe nem kerül, mert a frissítések automatikusan futhatnak. Ez a leállítás elkerüli, hogy a további frissítések példányspecifikus módosításokat eszközlének az operációs rendszeren vagy a telepített szolgáltatásokon. A sysprep futtatásával kapcsolatos további információkért lásd: [A virtuális merevlemezek általánosítatásának lépései.](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)

### <a name="for-linux"></a>Linux esetén

Az alábbi folyamat általánosít egy Linux rendszerű virtuális gépet, és külön virtuális gépként újra üzembe amint azt. Részletekért lásd: Rendszerkép létrehozása virtuális gépről vagy [virtuális merevlemezről.](../../virtual-machines/linux/capture-image.md) Ha eléri a "Virtuális gép létrehozása a rögzített rendszerképből" szakaszt, leállhat.

1. Távolítsa el az Azure Linux-ügynököt.
    1. Csatlakozzon a Linux rendszerű virtuális géphez egy SSH-ügyféllel.
    2. Az SSH ablakban írja be a következő parancsot: `sudo waagent –deprovision+user` .
    3. A folytatáshoz írja be az Y parancsot (a megerősítési lépés elkerülése érdekében hozzáadhatja a -force paramétert az előző parancshoz).
    4. Ha a parancs befejeződött, zárja be az SSH-ügyfelet **az Exit** (Kilépés) paranccsal.
2. Állítsa le a virtuális gépet.
    1. A Azure Portal válassza ki az erőforráscsoportot (RG), és foglalja le a virtuális gépet.
    2. A virtuális gép általánosított, és ezzel a virtuálisgép-lemezzel létrehozhat egy új virtuális gépet.

### <a name="capture-image"></a>Kép rögzítése

> [!NOTE]
> A SIG-t tartalmazó Azure-előfizetésnek a közzétevői fiókkal azonos bérlőn kell lennie a közzétételhez. Emellett a közzétevői fióknak legalább Közreműködői hozzáféréssel kell lennie a SIG-t tartalmazó előfizetéshez.

Ha a virtuális gép elkészült, rögzítheti egy Azure-beli megosztott rendszerkép-katalógusban. A rögzítéshez kövesse az alábbi lépéseket:

1. A [Azure Portal](https://ms.portal.azure.com/)a virtuális gép oldalára.
2. Válassza a **Rögzítés lehetőséget.**
3. A **Rendszerkép megosztása megosztott rendszerkép-katalógusban lehetőség alatt** válassza az Igen, ossza meg egy katalógusban rendszerképverzióként **lehetőséget.**
4. Az **Operációs rendszer állapota alatt válassza** az Általános lehetőséget.
5. Válasszon ki egy Cél rendszerkép-katalógust vagy **az Új létrehozása lehetőséget.**
6. Válasszon ki egy célként használt rendszerkép-definíciót, vagy **hozzon létre egy újat.**
7. Adja meg a **rendszerkép** verziószámát.
8. Válassza **az Áttekintés + létrehozás lehetőséget** a választási lehetőségek áttekintéshez.
9. Az ellenőrzés után válassza a Létrehozás **lehetőséget.**

Hozzáférés megadása:

1. Ugrás a Shared Image Gallery.
2. A **bal oldali panelen** válassza a Hozzáférés-vezérlés (IAM) lehetőséget.
3. Válassza **a Hozzáadás,** majd a **Szerepkör-hozzárendelés hozzáadása lehetőséget.**
4. Válasszon ki egy **szerepkört vagy** **tulajdonost.**
5. A **Hozzáférés hozzárendelése a szolgáltatáshoz** alatt **válassza a Felhasználó, csoport vagy szolgáltatásnév lehetőséget.**
6. Válassza ki a rendszerképet közzétevő személy Azure-e-mail-címét.
7. Kattintson a **Mentés** gombra.

:::image type="content" source="../media/create-vm/add-role-assignment.png" alt-text="Megjeleníti a szerepkör-hozzárendelés hozzáadása ablakot.":::

> [!NOTE]
> Nem kell SAS URI-okat létrehoznia, mivel most már közzétehet egy SIG-rendszerképet a Partnerközpont. Ha azonban továbbra is szüksége van a SAS URI létrehozásához szükséges lépésekre, tekintse meg a KÖVETKEZŐt: SAS URI létrehozása [virtuálisgép-rendszerképhez.](../azure-vm-get-sas-uri.md)
