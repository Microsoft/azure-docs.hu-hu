---
title: Azure-beli virtuálisgép-ajánlat (VM) létrehozása jóváhagyott alapból, Azure Marketplace
description: Megtudhatja, hogyan hozhat létre virtuálisgép-ajánlatot egy jóváhagyott alapból.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 04/16/2021
ms.openlocfilehash: 19ae4b929964aaeb971bef75a2a620e40e4667f5
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727167"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Virtuális gép létrehozása jóváhagyott alap használatával

Ez a cikk azt ismerteti, hogyan használható az Azure egy előre konfigurált, támogatott operációs rendszert tartalmazó virtuális gép (VM) létrehozására. Ha ez nem kompatibilis az Ön megoldásával, létrehozhat és konfigurálható egy helyszíni virtuális gép [egy](azure-vm-create-using-own-image.md) jóváhagyott operációs rendszer használatával.

> [!NOTE]
> Mielőtt elkezdené ezt az [](marketplace-virtual-machines.md#technical-requirements) eljárást, tekintse át az Azure-beli virtuális gépekre vonatkozó ajánlatok műszaki követelményeit, beleértve a virtuális merevlemezre (VHD) vonatkozó követelményeket.

## <a name="select-an-approved-base-image"></a>Jóváhagyott alapként kiválasztott rendszerkép

Válassza ki az alábbi Windows- vagy Linux-rendszerképek egyikét alapként.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview) [2014,](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview) [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Az Azure számos jóváhagyott Linux-disztribúciót kínál. Az aktuális listát az Azure által támogatott disztribúciók [linuxos listájában láthatja.](../virtual-machines/linux/endorsed-distros.md)

## <a name="create-vm-on-the-azure-portal"></a>Virtuális gép létrehozása a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com/).
2. Válassza a **Virtuális gépek** lehetőséget.
3. Válassza **a + Hozzáadás** lehetőséget a Virtuális gép létrehozása képernyő **megnyitásához.**
4. Válassza ki a rendszerképet  a legördülő listából, vagy válassza az Összes nyilvános és privát rendszerkép tallózása lehetőséget az összes elérhető virtuálisgép-rendszerkép kereséshez vagy tallózáshoz.
5. **2. generációs** virtuális gép létrehozásához kattintson a Speciális **lapra,** és válassza a **Gen 2** lehetőséget.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Válassza a Gen 1 vagy a Gen 2 lehetőséget.":::

6. Válassza ki az üzembe helyezni kívánt virtuális gép méretét.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Válasszon egy javasolt virtuálisgép-méretet a kiválasztott rendszerképhez.":::

7. Adja meg a virtuális gép létrehozásához szükséges egyéb adatokat.
8. Válassza **az Áttekintés + létrehozás lehetőséget** a választási lehetőségek áttekintéshez. Amikor **megjelenik az Ellenőrzésen** áteső üzenet, válassza a **Létrehozás lehetőséget.**

Az Azure elkezdi kiépítni a megadott virtuális gépet. A folyamat előrehaladását a bal **oldali Virtual Machines** lapfülre választva követheti nyomon. A létrehozása után a Virtuális gép állapota Fut **állapotúra változik.**

## <a name="configure-the-vm"></a>A virtuális gép konfigurálása

Ez a szakasz az Azure-beli virtuális gépek méretének, frissítésének és általánosizálásának a módszerét ismerteti. Ezek a lépések szükségesek a virtuális gép előkészítéséhez a virtuális gép Azure Marketplace.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

A Windows vagy [Linux](../virtual-machines/windows/connect-logon.md) rendszerű virtuális géphez való csatlakozáshoz tekintse meg a következő [dokumentációt.](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm)

### <a name="install-the-most-current-updates"></a>A legújabb frissítések telepítése

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>További biztonsági ellenőrzések végrehajtása

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfiguráció és ütemezett feladatok végrehajtása

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Következő lépések

- Javasolt következő lépés: [Tesztelje a virtuálisgép-rendszerképet,](azure-vm-image-test.md) hogy az megfeleljen a Azure Marketplace követelményeinek. Ez nem kötelező.
- Ha nem szeretné tesztelni a virtuálisgép-rendszerképet, jelentkezzen be a Partnerközpont [a](https://partner.microsoft.com/) rendszerkép közzétételéhez.
- Ha nehézségekbe ütközik az új Azure-alapú virtuális merevlemez létrehozása során, tekintse meg a virtuális gépekkel kapcsolatos gyakori [kérdéseket a Azure Marketplace.](azure-vm-create-faq.md)
