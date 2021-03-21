---
title: Virtuális hálózatok összekötése VNet-társítással – oktatóanyag – Azure Portal
description: Ebben az oktatóanyagban megismerheti, hogy kapcsolhat össze virtuális hálózatokat virtuális hálózatok közötti társviszony az Azure Portal használatával történő létesítésével.
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to connect two virtual networks so that virtual machines in one virtual network can communicate with virtual machines in the other virtual network.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 01/22/2020
ms.author: kumud
ms.custom: ''
ms.openlocfilehash: e95441aab6c8ce7de37ba5f6b08d5f7d54e13347
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "96017916"
---
# <a name="tutorial-connect-virtual-networks-with-virtual-network-peering-using-the-azure-portal"></a>Oktatóanyag: Virtuális hálózatok összekapcsolása virtuális hálózatok közötti társviszony az Azure Portal használatával történő létesítésével

A virtuális hálózatok közötti társviszony létesítésével virtuális hálózatokat kapcsolhat össze egymással. Ezek a virtuális hálózatok lehetnek azonos vagy eltérő régiókban (más néven globális virtuális társhálózatok létesítése). Ha a társviszony létrejött, a két virtuális hálózaton található erőforrások ugyanolyan késés és sávszélesség mellett kommunikálhatnak egymással, mintha ugyanazon a virtuális hálózaton lennének. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Két virtuális gép létrehozása
> * Két virtuális hálózat összekapcsolása virtuális hálózatok közötti társviszony létesítésével
> * Virtuális gép üzembe helyezése mindkét virtuális hálózaton
> * Virtuális gépek közötti kommunikáció

Igény szerint az oktatóanyagot az [Azure CLI](tutorial-connect-virtual-networks-cli.md) vagy az [Azure PowerShell](tutorial-connect-virtual-networks-powershell.md) használatával is elvégezheti.

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="log-in-to-azure"></a>Jelentkezzen be az Azure-ba

Jelentkezzen be az Azure Portalra a https://portal.azure.com címen.

## <a name="create-virtual-networks"></a>Virtuális hálózatok létrehozása

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
2. Válassza a **Hálózatkezelés**, majd a **Virtuális hálózat** elemet.
3. Az **alapvető** beállítások lapon adja meg vagy válassza ki az alábbi adatokat, és fogadja el a többi beállítás alapértelmezett beállításait:

    |Beállítás|Érték|
    |---|---|
    |Előfizetés| Válassza ki előfizetését.|
    |Erőforráscsoport| Válassza az **Új létrehozása** elemet, és adja meg a *myResourceGroup* nevet.|
    |Region| Válassza az **USA keleti régiója** lehetőséget.|
    |Name|myVirtualNetwork1|

4. Az **IP-címek** lapon adja meg a 10.0.0.0/16 értéket a **címterület** mezőben. Kattintson a lenti **alhálózat hozzáadása** gombra, és adja meg a *Subnet1* az alhálózat **neve** és a 10.0.0.0/24 **alhálózati címtartomány** mezőben.
5. Válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás** lehetőséget.
   
5. Végezze el ismét az 1–5. lépést az alábbi módosításokkal:

    |Beállítás|Érték|
    |---|---|
    |Név|myVirtualNetwork2|
    |Címtér|10.1.0.0/16|
    |Erőforráscsoport| Válassza a **meglévő használata** , majd a **myResourceGroup** lehetőséget.|
    |Alhálózat neve | Subnet2|
    |Alhálózat címtartománya|10.1.0.0/24|

## <a name="peer-virtual-networks"></a>Virtuális hálózatok közötti társviszony létesítése

1. Kezdje el begépelni a *MyVirtualNetwork1* kifejezést az Azure Portal tetején található keresőmezőbe. Amikor a **myVirtualNetwork1** elem megjelenik a keresési eredmények között, válassza ki.
2. Válassza a társítások lehetőséget, a **Beállítások** **területen, majd** válassza a **Hozzáadás** lehetőséget, ahogyan az a következő képen látható:

    ![Társviszony létrehozása](./media/tutorial-connect-virtual-networks-portal/create-peering.png)

3. Adja meg vagy válassza ki az alábbi adatokat, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza az **OK** elemet:

    |Beállítás|Érték|
    |---|---|
    |A myVirtualNetwork1-ből távoli virtuális hálózatra irányuló társ neve|myVirtualNetwork1-myVirtualNetwork2 – az oldal első betöltésekor Itt láthatja a "távoli virtuális hálózat" kifejezést. A távoli virtuális hálózat kiválasztása után a "távoli virtuális hálózat" kifejezés a távoli virtuális hálózat nevével lesz lecserélve.|
    |Előfizetés| Válassza ki előfizetését.|
    |Virtuális hálózat|myVirtualNetwork2 – a *myVirtualNetwork2* virtuális hálózat kiválasztásához válassza a **virtuális hálózat** elemet, majd válassza a **myVirtualNetwork2 (myResourceGroup)** lehetőséget. Választhat egy az adott régióban vagy egy másik régióban lévő virtuális hálózatot is.|
    |A myVirtualNetwork2 és myVirtualNetwork1 közötti társítás neve|myVirtualNetwork2-myVirtualNetwork1|

    ![Társviszony beállításai](./media/tutorial-connect-virtual-networks-portal/peering-settings-bidirectional.png)

    A társítási **állapot** *csatlakoztatva* van, ahogy az a következő képen látható:

    ![Társviszony-létesítés állapota](./media/tutorial-connect-virtual-networks-portal/peering-status-connected.png)

    Ha nem látja az állapotot, frissítse a böngészőjét.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Hozzon létre egy virtuális gépet az egyes virtuális hálózatokon, hogy kommunikációt létesíthessen közöttük egy későbbi lépésben.

### <a name="create-the-first-vm"></a>Az első virtuális gép létrehozása

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
2. Válassza a **Számítás**, majd a **Windows Server 2016 Datacenter** elemet. Választhat másik operációs rendszert is, de a következő lépések itt azt feltételezik, hogy a **Windows Server 2016 Datacentert** választotta. 
3. Adja meg vagy válassza ki az alábbi adatokat az **Alapvető beállítások** területen, a többi beállítás esetében fogadja el az alapértelmezett értéket, majd válassza a **Létrehozás** elemet:

    |Beállítás|Érték|
    |---|---|
    |Erőforráscsoport| Válassza a **meglévő használata** , majd a **myResourceGroup** lehetőséget.|
    |Name|myVm1|
    |Hely| Válassza az **USA keleti régiója** lehetőséget.|
    |Felhasználónév| Adjon meg egy tetszőleges felhasználónevet.|
    |Jelszó| Adjon meg egy tetszőleges jelszót. A jelszónak legalább 12 karakter hosszúnak kell lennie, és meg kell felelnie a [meghatározott összetettségi követelményeknek](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm).|
   
4. Válasszon egy virtuálisgép-méretet a **méret** beállításhoz.
5. Válassza ki az alábbi értékeket a **hálózat** területen:

    |Beállítás|Érték|
    |---|---|
    |Virtuális hálózat| myVirtualNetwork1 – ha még nincs kiválasztva, válassza a **virtuális hálózat** elemet, majd válassza a **myVirtualNetwork1** lehetőséget.|
    |Alhálózat| Subnet1 – ha még nincs kiválasztva, válassza az **alhálózat** lehetőséget, majd válassza a **Subnet1** lehetőséget.|
   
6. Válassza a **Hálózat** lehetőséget. Válassza a **kiválasztott portok engedélyezése** lehetőséget a **nyilvános bejövő portok** beállításnál. Válassza az **RDP** lehetőséget a **bejövő portok kiválasztása** lehetőség alatt. 

7. A virtuális gép üzembe helyezésének elindításához kattintson a bal alsó sarokban található **felülvizsgálat + létrehozás** gombra.

### <a name="create-the-second-vm"></a>A második virtuális gép létrehozása

Végezze el ismét az 1–6. lépést az alábbi módosításokkal:

|Beállítás|Érték|
|---|---|
|Név | myVm2|
|Virtuális hálózat | myVirtualNetwork2|

A virtuális gépek létrehozása néhány percet vesz igénybe. Csak akkor folytassa a következő lépésekkel, ha már mindkét virtuális gép létrejött.

## <a name="communicate-between-vms"></a>Virtuális gépek közötti kommunikáció

1. Kezdje el begépelni a *myVm1* kifejezést a portál tetején található *Keresés* mezőbe. Amikor a **myVm1** elem megjelenik a keresési eredmények között, válassza ki.
2. Hozzon létre egy távoli asztali kapcsolatot a *myVm1* virtuális géppel a **Csatlakozás** lehetőség kiválasztásával az alábbi ábrán látható módon:

    ![Csatlakozás virtuális géphez](./media/tutorial-connect-virtual-networks-portal/connect-to-virtual-machine.png)  

3. Nyissa meg a letöltött RDP-fájlt a virtuális géphez való csatlakozáshoz. Ha a rendszer kéri, válassza a **Csatlakozás** lehetőséget.
4. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót (előfordulhat, hogy a virtuális gép létrehozásakor beírt hitelesítő adatok megadásához ki kell választania a **További lehetőségek**, majd a **Másik fiók használata** elemet), majd válassza az **OK** gombot.
5. A bejelentkezés során egy figyelmeztetés jelenhet meg a tanúsítvánnyal kapcsolatban. Válassza az **Igen** lehetőséget a csatlakozás folytatásához.
6. Egy későbbi lépésben pingeléssel fog kommunikálni a *myVm2* virtuális géppel a *myVm1* virtuális gépről. A pingelés az ICMP protokollt használja, amelyet a Windows tűzfal alapértelmezés szerint letilt. A *myVm1* virtuális gépen engedélyezze az ICMP-t a Windows tűzfalon, hogy egy később lépésben pingelhesse ezt a virtuális gépet a *myVm2* virtuális gépről a PowerShell használatával.

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```
    
    Habár ebben az oktatóanyagban pingelést használunk a virtuális gépek közötti kommunikációhoz, az éles környezetekben üzemelő példányok esetében nem ajánlott az ICMP engedélyezése a Windows tűzfalon.

7. A *myVm2* virtuális géphez való csatlakozáshoz adja meg a következő parancsot egy parancssorban a *myVm1* virtuális gépen:

    ```
    mstsc /v:10.1.0.4
    ```
    
8. Mivel engedélyezte a pingelést a *myVm1* virtuális gépen, most már pingelheti azt az IP-cím alapján:

    ```
    ping 10.0.0.4
    ```
    
9. Válassza le az RDP-munkameneteket a *myVm1* és *myVm2* virtuális gépről egyaránt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást: 

1. Írja be a *myResourceGroup* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **myResourceGroup** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *myResourceGroup* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerhette, hogyan kapcsolhat össze két, ugyanabban az Azure-régióban található virtuális hálózatot virtuális hálózatok közötti társviszony létesítésével. Más [támogatott régiókban](virtual-network-manage-peering.md#cross-region) és [különböző Azure-előfizetésekben](create-peering-different-subscriptions.md#portal) található virtuális hálózatok között is létesíthet társviszonyt, illetve a társviszony létesítésével [küllős hálózati kialakításokat](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke#virtual-network-peering) is létrehozhat. További információ a virtuális hálózatok közötti társviszony létesítéséről: [Virtuális hálózatok közötti társviszony létesítésének áttekintése](virtual-network-peering-overview.md) és[Virtuális hálózatok közötti társviszonyok kezelése](virtual-network-manage-peering.md).

Ha csatlakoztatni szeretné saját számítógépét egy virtuális hálózathoz VPN-en keresztül, és interakcióba szeretne lépni egy virtuális hálózaton vagy társviszonnyal összekapcsolt virtuális hálózatokon található erőforrásokkal, tekintse meg a [Számítógép csatlakoztatása egy virtuális hálózathoz](../vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című cikket.
