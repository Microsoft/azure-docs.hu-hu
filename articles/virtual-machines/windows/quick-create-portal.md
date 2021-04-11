---
title: Rövid útmutató – Windows rendszerű virtuális gép létrehozása a Azure Portal
description: Ez a rövid útmutató a Windows rendszerű virtuális gépek az Azure Portallal történő létrehozását ismerteti.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.topic: quickstart
ms.workload: infrastructure
ms.date: 03/15/2021
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 0ba28d003f359af12de6242c6d2444fb8adab0d7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "103562755"
---
# <a name="quickstart-create-a-windows-virtual-machine-in-the-azure-portal"></a>Rövid útmutató: Windows rendszerű virtuális gép létrehozása az Azure Portalon

Az Azure-beli virtuális gépek (VM-ek) létrehozhatók az Azure Portal segítségével. Ez a módszer egy böngészőalapú felhasználói felületet biztosít a virtuális gépek és a társított erőforrások létrehozásához. Ez a rövid útmutató azt ismerteti, hogyan használható a Azure Portal egy virtuális gép (VM) üzembe helyezéséhez az Azure-ban, amely a Windows Server 2019-et futtatja. Ha látni szeretné a virtuális gépet feladatvégzés közben, hozzon létre RDP-kapcsolatot a virtuális géphez, és telepítse az IIS webkiszolgálót.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-virtual-machine"></a>Virtuális gép létrehozása

1. Írja be a **virtuális gépeket** a keresésbe.
1. A **szolgáltatások** területen válassza a **virtuális gépek** lehetőséget.
1. A **virtuális gépek** lapon válassza a **Hozzáadás** , majd a **virtuális gép** lehetőséget. 
1. Az **Alapok** fül **Projektadatok** részén győződjön meg arról, hogy a megfelelő előfizetés van kiválasztva, és válassza az **Új létrehozása** lehetőséget az Erőforráscsoport részen. A név mezőbe írja be a *myResourceGroup* nevet. 

    ![A Project Details (projekt részletei) szakasz képernyőképe, amelyen kiválaszthatja az Azure-előfizetést és a virtuális géphez tartozó erőforráscsoportot.](./media/quick-create-portal/project-details.png)

1. Alatt **Példány részletei** szakaszban írja be a *myVM* nevet a **Virtuális gép neve** mezőbe, és válassza *USA keleti régiója* lehetőséget a **Régió** részen. A **mérethez** válassza a *Windows Server 2019 Datacenter* elemet a **rendszerképhez** és *Standard_DS1_v2* . Hagyja meg a többi alapértelmezett értéket.

    ![Képernyőkép a példány részleteiről szakaszról, ahol megadhatja a virtuális gép nevét, és kiválaszthatja annak régióját, képét és méretét](./media/quick-create-portal/instance-details.png)

1. A **Rendszergazdai fiók**, területen adjon meg egy felhasználónevet (pl. *azureuser*) és egy jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](faq.md#what-are-the-password-requirements-when-creating-a-vm).

    ![Képernyőkép a rendszergazdai fiókról, ahol megadhatja a rendszergazda felhasználónevét és jelszavát](./media/quick-create-portal/administrator-account.png)

1. A **bejövő portszabályok** területen válassza a **kijelölt portok engedélyezése** lehetőséget, majd válassza az **RDP (3389)** és a **http (80)** elemet a legördülő menüből.

    ![Képernyőfelvétel a bejövő portszabályok szakaszról, ahol kiválaszthatja, hogy a bejövő kapcsolatok mely portokon engedélyezettek](./media/quick-create-portal/inbound-port-rules.png)

1. Hagyja meg a többi alapértelmezett beállítást, és kattintson a **Felülvizsgálat + létrehozás** gombra a lap alján.

    ![A lap alján található felülvizsgálat és Létrehozás gombra mutató képernyőkép](./media/quick-create-portal/review-create.png)

1. Az ellenőrzés futtatása után kattintson a lap alján található **Létrehozás** gombra.

1. Az üzembe helyezés befejezése után válassza **az Ugrás erőforráshoz** lehetőséget.

    ![Képernyőkép a következő lépésről, amely az erőforrást mutatja majd](./media/quick-create-portal/next-steps.png)

## <a name="connect-to-virtual-machine"></a>Csatlakozás virtuális géphez

Hozzon létre egy távoli asztali kapcsolatot a virtuális géppel. Ezek az utasítások ismertetik, hogyan csatlakozhat a virtuális gépéhez egy Windows rendszerű gépről. Mac rendszerben szüksége van egy RDP-kliensre, mint például a Mac App Store áruházban elérhető [távoli asztali ügyfélre](https://apps.apple.com/app/microsoft-remote-desktop/id1295203466?mt=12).

1. A virtuális gép áttekintés lapján kattintson a **kapcsolat** gombra, majd az **RDP** elemre. 

    ![Képernyőfelvétel a virtuális gép áttekintő oldaláról, amely a csatlakozási gomb helyét mutatja](./media/quick-create-portal/portal-quick-start-9.png)
    
2. Az **RDP-kapcsolat** lapon tartsa meg az alapértelmezett beállításokat az IP-cím, az 3389-as porton keresztül történő csatlakozáshoz, majd kattintson az **RDP-fájl letöltése** elemre.

2. Nyissa meg a letöltött RDP-fájlt, és kattintson a **Csatlakozás** gombra, amikor a rendszer erre kéri. 

3. A **Windows rendszerbiztonság** ablakban válassza a **További lehetőségek**, majd a **Másik fiók használata** elemet. Írja be a felhasználónevet **localhost** \\ *felhasználónévként*, adja meg a virtuális gép számára létrehozott jelszót, majd kattintson **az OK** gombra.

4. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. A kapcsolódás létrehozásához kattintson az **Igen** vagy a **Folytatás** gombra.

## <a name="install-web-server"></a>A webkiszolgáló telepítése

A virtuális gép működésének ellenőrzéséhez telepítse az IIS-webkiszolgálót. Nyisson meg egy PowerShell-parancssort a virtuális gépen, és futtassa a következő parancsot:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

Ha befejezte, zárja be a virtuális gép RDP-kapcsolatát.


## <a name="view-the-iis-welcome-page"></a>Az IIS kezdőlapjának megtekintése

A portálon válassza ki a virtuális gépet, és a virtuális gép áttekintésében vigye a kurzort az IP-cím fölé a **vágólapra Másolás** megjelenítéséhez. Másolja ki az IP-címet, és illessze be egy böngésző lapra. Ekkor megnyílik az alapértelmezett IIS-Kezdőlap, és a következőhöz hasonlóan kell kinéznie:

![Képernyőkép az IIS alapértelmezett webhelyéről egy böngészőben](./media/quick-create-powershell/default-iis-website.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölheti az erőforráscsoportot, a virtuális gépet és az összes kapcsolódó erőforrást. 

Lépjen a virtuális gép erőforráscsoporthoz, majd válassza az **erőforráscsoport törlése** elemet. Erősítse meg az erőforráscsoport nevét az erőforrások törlésének befejezéséhez.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban üzembe helyezett egy egyszerű virtuális gépet, nyitott egy hálózati portot a webes forgalomhoz, és telepített egy alapszintű webkiszolgálót. Ha bővebb információra van szüksége az Azure-alapú virtuális gépekkel kapcsolatban, lépjen tovább a Windows rendszerű virtuális gépekről szóló oktatóanyagra.

> [!div class="nextstepaction"]
> [Windowsos virtuális gépek az Azure-ban – oktatóanyagok](./tutorial-manage-vm.md)
