---
title: Azure-beli virtuálisgép-ajánlat (VM) létrehozása jóváhagyott alapból, Azure Marketplace
description: Megtudhatja, hogyan hozhat létre egy virtuális gép (VM) ajánlatot egy jóváhagyott alapból.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: how-to
author: emuench
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: 34d64d70c657712bb44aa6331b53f014349a0dda
ms.sourcegitcommit: b6f3ccaadf2f7eba4254a402e954adf430a90003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/20/2020
ms.locfileid: "92284321"
---
# <a name="how-to-create-a-virtual-machine-using-an-approved-base"></a>Virtuális gép létrehozása jóváhagyott alap használatával

Ez a cikk azt ismerteti, hogyan használható az Azure egy olyan virtuális gép (VM) létrehozásához, amely egy előre konfigurált, támogatott operációs rendszert tartalmaz. Ha ez nem kompatibilis a megoldással, [létrehozhat és konfigurálhat egy helyszíni virtuális gépet](azure-vm-create-using-own-image.md) egy jóváhagyott operációs rendszer használatával, majd beállíthatja és előkészítheti a feltöltéshez a [Windows VHD vagy VHDX felkészítése az Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md)-ba való feltöltésre című témakörben leírtak szerint.

> [!NOTE]
> Az eljárás megkezdése előtt tekintse át az Azure-beli virtuális gépekre vonatkozó [technikai követelményeket](marketplace-virtual-machines.md#technical-requirements) , beleértve a virtuális merevlemez (VHD) követelményeit.

## <a name="select-an-approved-base-image"></a>Jóváhagyott alaprendszerkép kiválasztása

Válassza ki az alábbi Windows-vagy Linux-rendszerképek egyikét a bázisként.

### <a name="windows"></a>Windows

- [Windows Server](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftwindowsserver.windowsserver?tab=Overview)
- SQL Server [2019](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2019-ws2019?tab=Overview), [2014](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2014sp3-ws2012r2?tab=Overview), [2012](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftsqlserver.sql2012sp4-ws2012r2?tab=Overview)

### <a name="linux"></a>Linux

Az Azure számos jóváhagyott Linux-disztribúciót kínál. Az aktuális listán tekintse [meg az Azure által támogatott disztribúciókkal](https://docs.microsoft.com/azure/virtual-machines/linux/endorsed-distros)foglalkozó Linux című témakört.

## <a name="create-vm-on-the-azure-portal"></a>Virtuális gép létrehozása a Azure Portal

1. Jelentkezzen be az [Azure Portalra](https://ms.portal.azure.com/).
2. Válassza a **Virtuális gépek** lehetőséget.
3. Válassza a **+ Hozzáadás** lehetőséget a **virtuális gép létrehozása** képernyő megnyitásához.
4. Válassza ki a lemezképet a legördülő listából, vagy válassza az **összes nyilvános és privát rendszerkép tallózása** lehetőséget a rendelkezésre álló virtuálisgép-lemezképek kereséséhez vagy tallózásához.
5. A 2. **generációs** virtuális gépek létrehozásához nyissa meg a **speciális** lapot, és válassza a **2. generációs** lehetőséget.

    :::image type="content" source="media/create-vm/vm-gen-option.png" alt-text="Válassza az 1. gen vagy a 2. lehetőséget.":::

6. Válassza ki a telepítendő virtuális gép méretét.

    :::image type="content" source="media/create-vm/create-virtual-machine-sizes.png" alt-text="Válassza az 1. gen vagy a 2. lehetőséget.":::

7. Adja meg a virtuális gép létrehozásához szükséges egyéb adatokat.
8. Válassza a **felülvizsgálat + létrehozás** lehetőséget a választási lehetőségek áttekintéséhez. Amikor megjelenik az **érvényesítési** üzenet, válassza a  **Létrehozás**lehetőséget.

Az Azure megkezdi a megadott virtuális gép üzembe helyezését. Az előrehaladás nyomon követéséhez válassza a bal oldali menü **Virtual Machines** lapját. A létrehozást követően a virtuális gép állapota **fut**értékre változik.


## <a name="configure-the-vm"></a>A virtuális gép konfigurálása

Ez a szakasz azt ismerteti, hogyan lehet méretezni, frissíteni és általánosítani egy Azure-beli virtuális gépet. Ezek a lépések szükségesek ahhoz, hogy előkészítse a virtuális gépet az Azure Marketplace-en való üzembe helyezéshez.

### <a name="connect-to-your-vm"></a>Csatlakozás a virtuális géphez

A Windows vagy [Linux](../virtual-machines/linux/ssh-from-windows.md#connect-to-your-vm) [rendszerű](../virtual-machines/windows/connect-logon.md) virtuális géphez való kapcsolódáshoz tekintse meg az alábbi dokumentációt.

### <a name="install-the-most-current-updates"></a>A legújabb frissítések telepítése

[!INCLUDE [Discussion of most current updates](includes/most-current-updates.md)]

### <a name="perform-additional-security-checks"></a>További biztonsági ellenőrzések végrehajtása

[!INCLUDE [Discussion of addition security checks](includes/additional-security-checks.md)]

### <a name="perform-custom-configuration-and-scheduled-tasks"></a>Egyéni konfiguráció és ütemezett feladatok végrehajtása

[!INCLUDE [Discussion of custom configuration and scheduled tasks](includes/custom-config.md)]

[!INCLUDE [Discussion of addition security checks](includes/size-connect-generalize.md)]

## <a name="next-steps"></a>Következő lépések

- Javasolt következő lépés: [a virtuálisgép-rendszerkép tesztelése](azure-vm-image-test.md) , hogy az megfeleljen az Azure Marketplace közzétételi követelményeinek. Ez nem kötelező.
- Ha nem teszteli a virtuálisgép-rendszerképet, folytassa [a sas URI előállításával](azure-vm-get-sas-uri.md).
- Ha nehézségekbe ütközött az új Azure-alapú virtuális merevlemez létrehozása során, tekintse meg [Az Azure Marketplace-hez készült virtuális gépekkel kapcsolatos gyakori kérdéseket](azure-vm-create-faq.md).
