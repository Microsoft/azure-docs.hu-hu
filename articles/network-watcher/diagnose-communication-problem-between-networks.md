---
title: Oktatóanyag – kommunikációs problémák diagnosztizálása a hálózatok között a Azure Portal használatával
titleSuffix: Azure Network Watcher
description: Ebből az oktatóanyagból megtudhatja, hogyan diagnosztizálhatja a kommunikációs problémát egy helyszíni vagy más virtuális hálózathoz csatlakoztatott Azure-beli virtuális hálózat között egy Azure-beli virtuális hálózati átjárón keresztül, Network Watcher VPN-diagnosztika funkciójának használatával.
services: network-watcher
documentationcenter: na
author: damendo
Customer intent: I need to determine why resources in a virtual network can't communicate with resources in a different network.
ms.service: network-watcher
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/07/2021
ms.author: damendo
ms.custom: mvc
ms.openlocfilehash: 1a556ea66bb54f994349ce9ba4d12fb486827edf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98013294"
---
# <a name="tutorial-diagnose-a-communication-problem-between-networks-using-the-azure-portal"></a>Oktatóanyag: Hálózatok közötti kommunikációs probléma diagnosztizálása az Azure Portal használatával

A virtuális hálózati átjárók egy Azure Virtual Networköt kapcsolnak össze egy helyszíni vagy más virtuális hálózattal. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózati átjárókkal kapcsolatos problémák diagnosztizálása a Network Watcher VPN-diagnosztikai funkciójával
> * Átjárókapcsolattal kapcsolatos problémák diagnosztizálása
> * Átjárókkal kapcsolatos problémák megoldása

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek

A VPN-diagnosztika használatához rendelkeznie kell egy meglévő, futó VPN Gatewayjel. Ha nem rendelkezik meglévő VPN Gatewayjel, amelyet diagnosztizálhatna, egy [PowerShell-szkript](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) használatával üzembe helyezhet egyet. A PowerShell-szkriptet innen futtathatja:
- **Helyi PowerShell-telepítés**: a parancsfájlhoz a Azure PowerShell `Az` modul szükséges. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- **Az Azure Cloud Shell**: Az [Azure Cloud Shell](https://shell.azure.com/powershell) a PowerShell legfrissebb verziójával rendelkezik, amely telepítve és konfigurálva is van, és belépteti Önt az Azure-ba.

A szkript nagyjából egy óra alatt hozza létre a VPN Gatewayt. A további lépések azt feltételezik, hogy a diagnosztizálni kívánt átjáró megegyezik a szkript által üzembe helyezettel. Ha saját meglévő átjáróját diagnosztizálja, az eredmények eltérőek lehetnek.

## <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="enable-network-watcher"></a>A Network Watcher engedélyezése

Ha már engedélyezve van a Network Watcher az USA keleti régiójában, folytassa az [Átjáró diagnosztizálása](#diagnose-a-gateway) szakasszal.

1. A portálon válassza a **Minden szolgáltatás** lehetőséget. A **Szűrő** mezőbe írja be a *Network Watcher* kifejezést. Ha **Network Watcher** jelenik meg az eredmények között, válassza ki.
2. Válassza a **Régiók** lehetőséget a kibontáshoz, majd válassza a **...** jelet az **USA keleti régiója** melletti jobb oldalon, az alábbi ábrán látható módon:

    ![A Network Watcher engedélyezése](./media/diagnose-communication-problem-between-networks/enable-network-watcher.png)

3. Válassza a **Network Watcher engedélyezése** lehetőséget.

## <a name="diagnose-a-gateway"></a>Átjáró diagnosztizálása

1. Válassza a portál bal oldalán található **Minden szolgáltatás** lehetőséget.
2. Kezdje el begépelni a *Network Watcher* kifejezést a **Szűrő** mezőbe. Amikor a **Network Watcher** elem megjelenik a keresési eredmények között, válassza ki.
3. A **HÁLÓZATI DIAGNOSZTIKAI ESZKÖZÖK** területen válassza a **VPN-diagnosztika** lehetőséget.
4. Válassza a **Tárfiók** lehetőséget, majd válassza ki a tárfiókot, amelybe a diagnosztikai információkat írni szeretné.
5. A **Tárfiókok** listájából válassza ki a használni kívánt tárfiókot. Ha nem rendelkezik meglévő tárfiókkal, válassza a **+ Tárfiók** lehetőséget, adja meg vagy válassza ki a szükséges információkat, majd válassza a **Létrehozás** lehetőséget a tárfiók létrehozásához. Ha az [előfeltételek](#prerequisites) szakaszban leírt szkript használatával létrehozott egy VPN Gatewayt, érdemes ugyanabban az erőforráscsoportban (*TestRG1*) létrehozni a tárfiókot.
6. A **Tárolók** listájából válassza ki a használni kívánt tárolót, majd válassza a **Kiválasztás** lehetőséget. Ha nem rendelkezik tárolókkal, válassza a **+ Tároló** elemet, adjon meg egy nevet a tárolónak, majd válassza az **OK** lehetőséget.
7. Válasszon egy átjárót, majd válassza a **Hibaelhárítás megkezdése** elemet. Ahogyan az a következő képen is látható, a teszt egy **Vnet1GW** nevű átjárón lesz futtatva:

    ![VPN-diagnosztika](./media/diagnose-communication-problem-between-networks/vpn-diagnostics.png)

8. Amíg a teszt fut, a **Fut** érték látható a **HIBAELHÁRÍTÁSI ÁLLAPOT** oszlopban, ahol az előző képen még **Nem indult el** szerepelt. A teszt futtatása több percig is eltarthat.
9. Tekintse meg az elvégzett teszt állapotát. Az alábbi képen egy befejezett diagnosztikai teszt állapoteredményei láthatók:

    ![A képernyőkép a diagnosztikai tesztek állapotának eredményét jeleníti meg, a jelen példában nem kifogástalan állapotot, beleértve az összegzést és a részleteket is.](./media/diagnose-communication-problem-between-networks/status.png)

    Látható, hogy a **HIBAELHÁRÍTÁSI ÁLLAPOT****Nem kifogástalan**, illetve a probléma **Összegzése** és **Részletei** is láthatók az **Állapot** lapon.
10. A **Művelet** lap kiválasztásakor a VPN-diagnosztika további információkkal szolgál. Az alábbi képen látható példában a VPN-diagnosztikából megtudhatja, hogy ellenőriznie kell az összes kapcsolat állapotát:

    ![A képernyőképen a művelet lap jelenik meg, amely további információkat nyújt.](./media/diagnose-communication-problem-between-networks/action.png)

## <a name="diagnose-a-gateway-connection"></a>Átjárókapcsolat diagnosztizálása

Az átjárók átjárókapcsolatokkal csatlakoznak más hálózatokhoz. Az átjárónak és az átjárókapcsolatoknak egyaránt kifogástalannak kell lennie ahhoz hogy a virtuális hálózat és a csatlakoztatott hálózatok közötti kommunikáció sikeres legyen.

1. Hajtsa végre az [Átjáró diagnosztizálása](#diagnose-a-gateway) szakaszban leírt 7. lépést ismét, de ezúttal válasszon ki egy kapcsolatot. Az alábbi példában a **VNet1toSite1** nevű kapcsolatot teszteljük:

    ![A képernyőképen megtudhatja, hogyan indíthatja el a hibaelhárítást a kiválasztott kapcsolatok esetében.](./media/diagnose-communication-problem-between-networks/connection.png)

    A teszt több percet is igénybe vehet.
2. Amikor a kapcsolat tesztelése befejeződött, az alábbi képen láthatóhoz hasonló eredményeket fog kapni az **Állapot** és a **Művelet** lapon:

    ![A kapcsolat állapota](./media/diagnose-communication-problem-between-networks/connection-status.png)

    ![Kapcsolatokra vonatkozó műveletek](./media/diagnose-communication-problem-between-networks/connection-action.png)

    A VPN-diagnosztika megmutatja a hibát az **Állapot** lapon, és a **Művelet** lapon több javaslatot is tesz arra, hogy mi okozhatja a problémát.

    Ha a tesztelt átjáró az [Előfeltételek](#prerequisites) szakaszban leírt [szkript](../vpn-gateway/scripts/vpn-gateway-sample-site-to-site-powershell.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) által üzembe helyezett átjáró volt, akkor az **Állapot** lapon szereplő probléma és a **Műveletek** lapon szereplő első két elem pontosan megegyezik a problémával. A szkript konfigurál egy helyőrző IP-címet (23.99.221.164) a helyszíni VPN Gateway eszköz számára.

    A probléma megoldásához ellenőrizze, hogy a helyszíni VPN Gateway [megfelelően van-e konfigurálva](../vpn-gateway/vpn-gateway-about-vpn-devices.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json), és módosítsa a szkript által a helyi hálózati átjáró számára konfigurált IP-címet a helyszíni VPN Gateway tényleges nyilvános címére.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha csak az oktatóanyag elvégzése érdekében hozott létre egy VPN Gatewayt az [előfeltételek](#prerequisites) szakaszban leírt szkript használatával, és már nincs rá szükség, törölje az erőforráscsoportot és a benne lévő összes erőforrást:

1. Írja be a *TestRG1* nevet a portál tetején lévő **keresőmezőbe**. Amikor a **TestRG1** megjelenik a keresési eredmények között, válassza ki.
2. Válassza az **Erőforráscsoport törlése** elemet.
3. Írja be a *TestRG1* nevet az **ÍRJA BE AZ ERŐFORRÁSCSOPORT NEVÉT:** mezőbe, majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag a virtuális hálózati átjárókkal kapcsolatos problémák diagnosztizálását mutatta be. Érdemes naplózni a virtuális gépek bejövő és kimenő hálózati kommunikációját, hogy átnézhesse a naplókat rendellenességek után kutatva. Ennek megismeréséhez folytassa a következő oktatóanyaggal.

> [!div class="nextstepaction"]
> [Virtuális gép be- és kimenő forgalmának naplózása](network-watcher-nsg-flow-logging-portal.md)
