---
title: Azure-beli virtuális hálózatok társításának létrehozása, módosítása vagy törlése | Microsoft Docs
description: Virtuális hálózati társak létrehozása, módosítása vagy törlése. A virtuális hálózatok társításával azonos régióban és régiókban is csatlakoztathatók a virtuális hálózatok.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/01/2019
ms.author: altambaw
ms.openlocfilehash: dc8db3f1eccce2bb85f03d51fcfd1c4113823d49
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222666"
---
# <a name="create-change-or-delete-a-virtual-network-peering"></a>Virtuális hálózati társak létrehozása, módosítása vagy törlése

Megtudhatja, hogyan hozhat létre, módosíthat vagy törölhet virtuális hálózati társításokat. A virtuális hálózati társítás lehetővé teszi a virtuális hálózatok összekapcsolását ugyanabban a régióban és az egyes régiókban (más néven globális VNet-társítás) az Azure-beli gerinces hálózaton keresztül. A társítást követően a virtuális hálózatok továbbra is külön erőforrásként vannak kezelve. Ha még nem ismeri a virtuális hálózatok társítását, akkor további információt a [virtuális hálózat](virtual-network-peering-overview.md) összevonásának áttekintése című témakörben vagy egy [oktatóanyag](tutorial-connect-virtual-networks-portal.md)elvégzésével tudhat meg.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely szakaszának lépéseinek elvégzése előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free).
- Ha a portált használja, nyissa meg https://portal.azure.com , majd jelentkezzen be egy olyan fiókkal, amely rendelkezik a társításokkal való együttműködéshez [szükséges engedélyekkel](#permissions) .
- Ha a cikkben szereplő feladatok végrehajtásához PowerShell-parancsokat használ, futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy a PowerShellt a számítógépről futtatva. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor azt is futtatnia kell `Connect-AzAccount` egy olyan fiókkal, amely rendelkezik a [megfelelő engedélyekkel](#permissions) az Azure-hoz való kapcsolódáshoz.
- Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, akkor azt is futtatnia kell `az login` egy olyan fiókkal, amely rendelkezik a [megfelelő engedélyekkel](#permissions) a társítással való együttműködéshez az Azure-beli kapcsolatok létrehozásához.

A fiókkal, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy olyan [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyekben](#permissions)felsorolt megfelelő műveletekhez van rendelve.

## <a name="create-a-peering"></a>Peering létrehozása

A társítás létrehozása előtt Ismerkedjen meg a követelményekkel és a megkötésekkel és a [szükséges engedélyekkel](#permissions).

1. A Azure Portal tetején található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki. Ne válassza a **virtuális hálózatok (klasszikus)** lehetőséget, ha az megjelenik a listában, mert a klasszikus üzemi modellel központilag telepített virtuális hálózatról nem hozhat létre társítást.
2. Válassza ki azt a virtuális hálózatot a listában, amelyhez létre kívánja hozni a társítást.
3. A **Beállítások** **területen válassza a** társítások lehetőséget.
4. Válassza a **+ Hozzáadás** lehetőséget. 
5. <a name="add-peering"></a>Adja meg vagy válassza ki a következő beállítások értékeit:
    - **Név:** A társítás nevének egyedinek kell lennie a virtuális hálózaton belül.
    - **Virtuális hálózat telepítési modellje:** Válassza ki, hogy melyik üzembe helyezési modellt szeretné központilag telepíteni a társ-összevonással.
    - **Ismerem az erőforrás-azonosítót:** Ha olvasási hozzáféréssel rendelkezik ahhoz a virtuális hálózathoz, amelynek a társát szeretné használni, ne jelölje be ezt a jelölőnégyzetet. Ha nincs olvasási hozzáférése ahhoz a virtuális hálózathoz vagy előfizetéshez, amelyhez a társát szeretné használni, jelölje be ezt a jelölőnégyzetet. Adja meg annak a virtuális hálózatnak a teljes erőforrás-AZONOSÍTÓját, amelyet a jelölőnégyzet bejelölése után megjelenő **erőforrás-azonosító** mezőben jelölt ki. A megadott erőforrás-AZONOSÍTÓnak olyan virtuális hálózathoz kell tartoznia, amely ugyanabban a vagy [más](#requirements-and-constraints) Azure- [régióban](https://azure.microsoft.com/regions) található, mint ez a virtuális hálózat. A teljes erőforrás-azonosító a következőhöz hasonlóan néz ki: `/subscriptions/<Id>/resourceGroups/<resource-group-name>/providers/Microsoft.Network/virtualNetworks/<virtual-network-name>` . A virtuális hálózatok erőforrás-AZONOSÍTÓját a virtuális hálózat tulajdonságainak megtekintésével érheti el. A virtuális hálózatok tulajdonságainak megtekintéséről a [virtuális hálózatok kezelése](manage-virtual-network.md#view-virtual-networks-and-settings)című témakörben olvashat bővebben. Ha az előfizetés egy másik Azure Active Directory bérlőhöz van társítva, mint annak a virtuális hálózatnak az előfizetése, amelyhez a társítást hozza létre, először adjon [hozzá egy felhasználót](../active-directory/external-identities/add-users-administrator.md?toc=%2fazure%2fvirtual-network%2ftoc.json#add-guest-users-to-the-directory) az egyes bérlők számára a szemközti bérlőben.
    - **Előfizetés:** Válassza ki annak a virtuális hálózatnak az [előfizetését](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) , amelynek a társát szeretné használni. Egy vagy több előfizetés szerepel a listáján attól függően, hogy a fiók hány előfizetéshez rendelkezik olvasási hozzáféréssel. Ha bejelölte az **erőforrás-azonosító** jelölőnégyzetet, ez a beállítás nem érhető el.
    - **Virtuális hálózat:** Válassza ki azt a virtuális hálózatot, amelynek a társát szeretné használni. Kiválaszthat egy Azure-beli üzembe helyezési modellel létrehozott virtuális hálózatot is. Ha egy másik régióban lévő virtuális hálózatot szeretne kiválasztani, ki kell választania egy virtuális hálózatot egy [támogatott régióban](#cross-region). Olvasási hozzáféréssel kell rendelkeznie a virtuális hálózathoz ahhoz, hogy megjelenjen legyen a listában. Ha a virtuális hálózat szerepel a felsorolásban, de szürkén jelenik meg, annak oka az lehet, hogy a virtuális hálózat címterület átfedésben van a virtuális hálózathoz tartozó címterület területével. Ha a virtuális hálózati címtartomány átfedésben van, azok nem lehetnek egyenrangúak. Ha bejelölte az **erőforrás-azonosító** jelölőnégyzetet, ez a beállítás nem érhető el.
    - **Virtuális hálózati hozzáférés engedélyezése:** Válassza az **engedélyezve** (alapértelmezett) lehetőséget, ha engedélyezni szeretné a két virtuális hálózat közötti kommunikációt az alapértelmezett `VirtualNetwork` folyamaton keresztül. A virtuális hálózatok közötti kommunikáció engedélyezése lehetővé teszi, hogy a virtuális hálózathoz csatlakoztatott erőforrások ugyanazzal a sávszélességgel és késéssel kommunikáljanak egymással, mintha ugyanahhoz a virtuális hálózathoz csatlakoznak. A két virtuális hálózat erőforrásai közötti összes kommunikáció az Azure magánhálózati hálózatán keresztül történik. A hálózati biztonsági csoportok **VirtualNetwork** szolgáltatásának címkéje magában foglalja a virtuális hálózatot és a társ virtuális hálózatot, ha ez a beállítás **engedélyezve** van. (További információ a hálózati biztonsági csoport szolgáltatásának címkékről: [hálózati biztonsági csoportok áttekintése](./network-security-groups-overview.md#service-tags).) Válassza a **Letiltva** lehetőséget, ha nem szeretné, hogy a rendszer alapértelmezés szerint a virtuális hálózatra irányítsa a forgalmat. Ha egy virtuális hálózatot egy másik virtuális hálózattal társított, akkor a **Letiltva** lehetőséget választhatja, de alkalmanként le szeretné tiltani a két virtuális hálózat közötti alapértelmezett forgalmat. Előfordulhat, hogy az engedélyezés/letiltás sokkal kényelmesebb, mint a társítások törlése és újbóli létrehozása. Ha ez a beállítás le van tiltva, a forgalom alapértelmezés szerint nem áramlik a virtuális hálózatok között. a forgalom azonban továbbra is eltarthat, ha explicit módon engedélyezi egy [hálózati biztonsági csoport](./network-security-groups-overview.md) szabályát, amely tartalmazza a megfelelő IP-címeket vagy alkalmazás-biztonsági csoportokat.
      > [!WARNING]
      > A **virtuális hálózati hozzáférés engedélyezése** beállítás letiltása csak a **VirtualNetwork** szolgáltatási címke definícióját módosítja. Ez a beállítás leírásában leírtak szerint *nem* teljes mértékben akadályozza meg a forgalom áramlását a társ-kapcsolatok között.    
    - **Továbbított forgalom engedélyezése:** Jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné, hogy egy virtuális hálózat (amely nem a virtuális hálózatból származik) hálózati virtuális berendezése *továbbítsa* a forgalmat a virtuális hálózatra egy társon keresztül. Tegyük fel például, hogy három virtuális hálózat neve Spoke1, Spoke2 és hub. Az egyes küllős virtuális hálózatok és a hub virtuális hálózat között egy társítás létezik, de a küllős virtuális hálózatok között nem léteznek társítások. Egy hálózati virtuális berendezést helyeznek üzembe a központi virtuális hálózaton, és a felhasználó által megadott útvonalak minden olyan küllős virtuális hálózatra vonatkoznak, amely az alhálózatok közötti forgalmat a hálózati virtuális berendezésen keresztül irányítja. Ha ez a jelölőnégyzet nincs bejelölve az egyes küllős virtuális hálózatok és a hub virtuális hálózat között, a forgalom nem áramlik a küllős virtuális hálózatok között, mert a hub nem továbbítja a forgalmat a virtuális hálózatok között. Ha engedélyezi ezt a lehetőséget, a továbbított forgalom a társításon keresztül is lehetővé válik, nem hoz létre felhasználó által megadott útvonalakat vagy hálózati virtuális berendezéseket. A felhasználó által megadott útvonalak és hálózati virtuális berendezések külön jönnek létre. Ismerje meg a [felhasználó által megadott útvonalakat](virtual-networks-udr-overview.md#user-defined). Ezt a beállítást nem kell ellenőriznie, ha a forgalmat egy Azure-VPN Gateway keresztül továbbítják a virtuális hálózatok között.
    - **Átjáró továbbításának engedélyezése:** Jelölje be ezt a jelölőnégyzetet, ha rendelkezik a virtuális hálózathoz csatolt virtuális hálózati átjáróval, és engedélyezni szeretné, hogy a virtuális hálózatról érkező forgalom áthaladjon az átjárón keresztül. Ezt a virtuális hálózatot például egy virtuális hálózati átjárón keresztül lehet csatlakoztatni egy helyszíni hálózathoz. Az átjáró lehet ExpressRoute vagy VPN-átjáró. Ha bejelöli ezt a jelölőnégyzetet, a rendszer átirányítja a virtuális hálózatról a helyszíni hálózatra csatlakozó átjárón keresztül érkező forgalmat. Ha bejelöli ezt a jelölőnégyzetet, a társ virtuális hálózat nem rendelkezhet konfigurált átjáróval. A társ virtuális hálózatnak rendelkeznie kell a **távoli átjárók használata** jelölőnégyzettel, ha a másik virtuális hálózatról erre a virtuális hálózatra állítja be a társítást. Ha a jelölőnégyzet nincs bejelölve (alapértelmezés), a kihelyezett virtuális hálózatról érkező forgalom továbbra is erre a virtuális hálózatra kerül, de a virtuális hálózathoz csatlakoztatott virtuális hálózati átjárón keresztül nem végezhető el folyamat. Ha a társítás egy virtuális hálózat (Resource Manager) és egy virtuális hálózat (klasszikus) között van, az átjárónak a virtuális hálózaton (Resource Manager) kell lennie.

       A helyszíni hálózatra irányuló forgalom továbbítása mellett a VPN-átjárók továbbítják a hálózati forgalmat az átjárót tartalmazó virtuális hálózat között, anélkül, hogy a virtuális hálózatoknak egymáshoz kell csatlakozniuk. Ha VPN-átjárót használ a forgalom továbbítására, akkor lehet hasznos, ha VPN-átjárót szeretne használni egy központon belül (lásd: a **továbbított forgalom engedélyezése a továbbított forgalom engedélyezéséhez**) virtuális hálózat, hogy átirányítsa a forgalmat olyan küllős virtuális hálózatok között, amelyek nem csatlakoznak egymáshoz. Ha többet szeretne megtudni az átjárók átvitelének engedélyezéséről, tekintse meg a VPN Gateway áttelepítésre való [konfigurálásával foglalkozó témakört a virtuális hálózatban](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Ehhez a forgatókönyvhöz olyan felhasználó által definiált útvonalakat kell végrehajtani, amelyek a virtuális hálózati átjárót a következő ugrás típusaként határozzák meg. Ismerje meg a [felhasználó által megadott útvonalakat](virtual-networks-udr-overview.md#user-defined). Egy VPN-átjárót csak a felhasználó által megadott útvonalon lehet megadnia következő ugrási típusként, a felhasználó által megadott útvonalon nem adhat meg ExpressRoute-átjárót a következő ugrási típusként.

    - **Távoli átjárók használata:** Jelölje be ezt a jelölőnégyzetet, ha engedélyezni szeretné, hogy a virtuális hálózatról érkező forgalom olyan virtuális hálózati átjárón keresztül folyjon, amelyhez a társítást végzi. Például a társításhoz használt virtuális hálózat rendelkezik egy olyan VPN-átjáróval, amely lehetővé teszi a kommunikációt a helyszíni hálózattal.  Ha bejelöli ezt a jelölőnégyzetet, a virtuális hálózatról érkező forgalom áthaladhat a társ virtuális hálózathoz csatlakoztatott VPN-átjárón keresztül. Ha bejelöli ezt a jelölőnégyzetet, a társ virtuális hálózatnak hozzá kell rendelnie egy virtuális hálózati átjárót, és be kell jelölnie az **átjáró engedélyezése** jelölőnégyzetet. Ha a jelölőnégyzet nincs bejelölve (alapértelmezés), a kihelyezett virtuális hálózatról érkező forgalom továbbra is ebbe a virtuális hálózatba áramlik, de a virtuális hálózathoz csatlakoztatott virtuális hálózati átjárón keresztül nem végezhető el.
    
      A virtuális hálózatnak csak egy társítása lehet engedélyezve.

      Távoli átjárók nem használhatók, ha már van konfigurálva átjáró a virtuális hálózaton. Ha többet szeretne megtudni a tranzit átjárók használatáról, tekintse meg a [VPN Gateway konfigurálása a virtuális hálózatok közötti átjáráshoz](../vpn-gateway/vpn-gateway-peering-gateway-transit.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című témakört.
        
    > [!NOTE]
    > Ha Virtual Network-átjárót használ a helyszíni forgalom tranzitívnak való küldéséhez, a helyszíni VPN-eszközhöz tartozó VNet IP-címtartományt "érdekes" forgalomra kell beállítani. Ellenkező esetben a helyszíni erőforrások nem tudnak kommunikálni a megtekintett VNet lévő erőforrásokkal.

6. Kattintson az **OK** gombra, hogy hozzáadja a társítást a kiválasztott virtuális hálózathoz.

A különböző előfizetésekben és üzembe helyezési modellekben lévő virtuális hálózatok közötti összevonások megvalósításának lépésenkénti lépéseiért lásd a [következő lépéseket](#next-steps).

### <a name="commands"></a>Parancsok

- **Azure CLI**: [az Network vnet peering Create](/cli/azure/network/vnet/peering)
- **PowerShell**: [Add-AzVirtualNetworkPeering](/powershell/module/az.network/add-azvirtualnetworkpeering)

## <a name="view-or-change-peering-settings"></a>A peering beállításainak megtekintése vagy módosítása

A társítás módosítása előtt Ismerkedjen meg a követelményekkel és a megkötésekkel és a [szükséges engedélyekkel](#permissions).

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki. Ne válassza a **virtuális hálózatok (klasszikus)** lehetőséget, ha az megjelenik a listában, mert a klasszikus üzemi modellel központilag telepített virtuális hálózatról nem hozhat létre társítást.
2. Válassza ki azt a virtuális hálózatot a listában, amelynek a beállításait módosítani szeretné.
3. A **Beállítások** **területen válassza a** társítások lehetőséget.
4. Válassza ki a megtekinteni kívánt társítást, vagy módosítsa a beállításait.
5. Módosítsa a megfelelő beállítást. Olvassa el a társítás létrehozása című [cikk 5. lépésében](#add-peering) található egyes beállítások lehetőségeit.
6. Kattintson a **Mentés** gombra.

**Parancsok**

- **Azure CLI**: [az Network vnet](/cli/azure/network/vnet/peering) -társítási lista egy virtuális hálózathoz tartozó társítások listázásához [az az Network vnet peering show](/cli/azure/network/vnet/peering) , amely megjeleníti egy adott társítás beállításait, és [az az Network vnet peering Update paranccsal](/cli/azure/network/vnet/peering) megváltoztathatja a társ-beállítási beállításokat. |
- **PowerShell**: [Get-AzVirtualNetworkPeering](/powershell/module/az.network/get-azvirtualnetworkpeering) a beállítások módosításához és a [set-AzVirtualNetworkPeering](/powershell/module/az.network/set-azvirtualnetworkpeering) megtekintéséhez.

## <a name="delete-a-peering"></a>Peering törlése

A társítás törlése előtt győződjön meg arról, hogy a fiókja rendelkezik a [szükséges engedélyekkel](#permissions).

Ha töröl egy társítást, a virtuális hálózatról érkező forgalom már nem áramlik át a társ virtuális hálózatra. Ha a Resource Managerrel központilag telepített virtuális hálózatok egyenrangúak, minden virtuális hálózat egy másik virtuális hálózattal rendelkezik. Bár az egyik virtuális hálózatról való kivonás törlése letiltja a virtuális hálózatok közötti kommunikációt, nem törli a társat a másik virtuális hálózatról. A másik virtuális hálózatban található társ-összevonási állapot **megszakad**. A társítás addig nem hozható létre újra, amíg újra nem hozza létre a társítást az első virtuális hálózatban, és a virtuális hálózatok *egymáshoz* kapcsolási állapota is csatlakoztatva lesz.

Ha azt szeretné, hogy a virtuális hálózatok időnként kommunikáljanak, de nem mindig, hanem a társítás törlése helyett, akkor a **virtuális hálózati hozzáférés engedélyezése** beállítás **Letiltva** értékre állítható. Ebből a cikkből megtudhatja, hogyan tekintheti át a témakör a társ létrehozása című szakaszának 6. lépését. A hálózati hozzáférés letiltását és engedélyezését a társítások törléséhez és újbóli létrehozásához is használhatja.

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki. Ne válassza a **virtuális hálózatok (klasszikus)** lehetőséget, ha az megjelenik a listában, mert a klasszikus üzemi modellel központilag telepített virtuális hálózatról nem hozhat létre társítást.
2. Válassza ki azt a virtuális hálózatot a listából, amelynek a társítását törölni kívánja.
3. A **Beállítások** **területen válassza a** társítások lehetőséget.
4. A törölni kívánt társítás jobb oldalán válassza a **...** elemet, válassza a **Törlés** lehetőséget, majd az **Igen** gombot választva törölje a társítást az első virtuális hálózatról.
5. Az előző lépések végrehajtásával törölje a társítást a többi virtuális hálózatról a társításban.

**Parancsok**

- **Azure CLI**: [az Network vnet peering delete](/cli/azure/network/vnet/peering)
- **PowerShell**: [Remove-AzVirtualNetworkPeering](/powershell/module/az.network/remove-azvirtualnetworkpeering)

## <a name="requirements-and-constraints"></a>Követelmények és korlátozások

- <a name="cross-region"></a>A társ virtuális hálózatok ugyanabban a régióban vagy más régiókban is lehetnek. A különböző régiókban található egyenrangú virtuális hálózatokat *globális VNet*-társításnak is nevezzük. 
- Globális társítások létrehozásakor a kihelyezett virtuális hálózatok bármely Azure-beli nyilvános Felhőbeli régióban, illetve a kínai Felhőbeli régiókban vagy kormányzati Felhőbeli régiókban létezhetnek. A felhők között nem lehet egyenrangú. Az Azure-beli nyilvános felhőben lévő VNet például nem lehet egy Azure China Cloud-beli VNet.
- Az egyes virtuális hálózatok erőforrásai nem kommunikálhatnak a globálisan társított virtuális hálózat alapszintű, belső terheléselosztóinak előtérbeli IP-címével. Az alapszintű terheléselosztó csak ugyanabban a régióban támogatott. A standard terheléselosztó mindkettőhöz támogatott, a virtuális hálózatok közötti és a globális virtuális hálózatok közötti társviszonyhoz is. Az alapszintű Load balancert használó olyan szolgáltatások, amelyek nem fognak működni a globális VNet-társítással, itt vannak dokumentálva [.](virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)
- Távoli átjárókat használhat, vagy engedélyezheti az átjárók átvitelét a globálisan működő virtuális hálózatokban és a helyileg létrehozott virtuális hálózatokban.
- A virtuális hálózatok lehetnek azonos vagy eltérő előfizetésekben. Ha különböző előfizetésekben lévő egyenrangú virtuális hálózatokkal rendelkezik, mindkét előfizetés ugyanahhoz vagy különböző Azure Active Directory bérlőhöz is társítható. Ha még nem rendelkezik AD-Bérlővel, [létrehozhat egyet](../active-directory/develop/quickstart-create-new-tenant.md?toc=%2fazure%2fvirtual-network%2ftoc.json-a-new-azure-ad-tenant).
- A társ virtuális hálózatoknak nem átfedésben lévő IP-címekkel kell rendelkezniük.
- Ha a virtuális hálózat egy másik virtuális hálózattal van társítva, nem adhat hozzá címtartományt a virtuális hálózat címterület-tartományához, vagy törölheti a címtartományt. Címtartományok hozzáadásához vagy eltávolításához törölje a társítást, vegye fel vagy távolítsa el a címtartományt, majd hozza létre újra a társítást. Ha címtartományt szeretne hozzáadni a virtuális hálózatokból, vagy eltávolítja a címtartományt, tekintse meg a [virtuális hálózatok kezelése](manage-virtual-network.md)című témakört.
- A Resource Manageren keresztül központilag telepített két virtuális hálózatot, illetve a Resource Manageren keresztül üzembe helyezett virtuális hálózatokat a klasszikus üzemi modellen keresztül telepített virtuális hálózattal használhatja. A klasszikus üzembe helyezési modellel létrehozott két virtuális hálózat nem használható. Ha még nem ismeri az Azure üzembe helyezési modelljeit, olvassa el az [Azure Deployment models ismertetése](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json) című cikket. A [VPN Gateway](../vpn-gateway/design.md?toc=%2fazure%2fvirtual-network%2ftoc.json#V2V) használatával összeköthető két, a klasszikus üzembehelyezési modellel létrehozott virtuális hálózat.
- Két, Resource Managerrel létrehozott virtuális hálózat közötti társviszony létrehozása esetén mindkét érintett virtuális hálózathoz konfigurálni kell egy társítást. A következő típusok egyike jelenik meg a társítási állapothoz: 
  - *Kezdeményezve:* Amikor létrehozza a társítást a második virtuális hálózatra az első virtuális hálózatról, a rendszer *kezdeményezi* a társítási állapotot. 
  - *Csatlakoztatva:* Amikor létrehozza a társítást a második virtuális hálózatról az első virtuális hálózatra, a társítási állapota *csatlakoztatva* lesz. Ha megtekinti az első virtuális hálózat társítási állapotát, az állapota a *kezdeményezőtől* a *csatlakoztatott* állapotra módosult. Nem sikerült létrehozni a társítást, amíg a virtuális hálózati társítások társítási állapota is *csatlakoztatva* van.
- Ha a Resource Managerrel létrehozott virtuális hálózatot a klasszikus üzemi modellel létrehozott virtuális hálózattal hozta létre, akkor csak a Resource Manageren keresztül üzembe helyezett virtuális hálózatra vonatkozó társítást konfigurálhat. A virtuális hálózat (klasszikus) vagy a klasszikus üzemi modellben üzembe helyezett két virtuális hálózat között nem konfigurálható a társítás. A virtuális hálózat (Resource Manager) és a virtuális hálózat (klasszikus) közötti társítás létrehozásakor a rendszer *frissíti* a társítási állapotot, majd rövid idő alatt módosítja a *csatlakozást*.
- A rendszer két virtuális hálózat között létrehoz egy társítást. Önmagában nem tranzitívak. Ha a következőket hozza létre egymás között:
  - VirtualNetwork1 & VirtualNetwork2-VirtualNetwork1 & VirtualNetwork2
  - VirtualNetwork2 & VirtualNetwork3-VirtualNetwork2 & VirtualNetwork3


  A VirtualNetwork1 és a VirtualNetwork3 között nincs egymással való társítás a VirtualNetwork2-n keresztül. Ha létre szeretne hozni egy virtuális hálózatot a VirtualNetwork1 és a VirtualNetwork3 között, létre kell hoznia egy társítást a VirtualNetwork1 és a VirtualNetwork3 között. A VirtualNetwork1 és a VirtualNetwork3 között nincs egymással való társítás a VirtualNetwork2-n keresztül. Ha azt szeretné, hogy a VirtualNetwork1 és a VirtualNetwork3 közvetlenül kommunikáljanak, létre kell hoznia egy explicit társítást a VirtualNetwork1 és a VirtualNetwork3 között, vagy át kell haladnia egy NVA a hub-hálózaton.  
- Az alapértelmezett Azure-névfeloldás használatával nem oldhatók fel nevek a megosztott virtuális hálózatokban. Más virtuális hálózatok neveinek feloldásához Azure DNS kell használnia [a privát tartományokhoz](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy egy egyéni DNS-kiszolgálóhoz. A saját DNS-kiszolgáló beállításával kapcsolatos további információkért lásd: [névfeloldás a saját DNS-kiszolgáló használatával](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server).
- Az ugyanabban a régióban található, egymással azonos régióba tartozó virtuális hálózatok erőforrásai ugyanúgy kommunikálhatnak egymással, mint a virtuális hálózatban lévő azonos sávszélességgel és késéssel. Az egyes virtuális gépek mérete azonban a saját maximális hálózati sávszélességgel rendelkezik. A különböző virtuálisgép-méretek maximális hálózati sávszélességével kapcsolatos további információkért lásd: Windows vagy [Linux](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [rendszerű](../virtual-machines/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gépek méretei.
- Egy virtuális hálózat egy másik virtuális hálózatra is csatlakoztatható, és egy másik virtuális hálózathoz is csatlakozhat egy Azure virtuális hálózati átjáróval. Ha a virtuális hálózatok kapcsolaton és átjárón keresztül csatlakoznak, a virtuális hálózatok közötti forgalom az átjáró helyett a társítási konfiguráción keresztül folyik.
- A pont – hely VPN-ügyfeleket újra le kell tölteni a virtuális hálózati társítás sikeres konfigurálása után, hogy az új útvonalak le legyenek töltve az ügyfélre.
- Egy névleges díj vonatkozik a társhálózati viszonyt használó bejövő és kimenő forgalomra. További tájékoztatás a [díjszabási lapon](https://azure.microsoft.com/pricing/details/virtual-network) olvasható.

## <a name="permissions"></a>Engedélyek

A virtuális hálózati társítással való együttműködéshez használt fiókokat a következő szerepkörökhöz kell rendelni:

- [Hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor): a Resource Manageren keresztül üzembe helyezett virtuális hálózatok esetében.
- [Klasszikus hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#classic-network-contributor): a klasszikus üzemi modellel üzembe helyezett virtuális hálózatok esetében.

Ha a fiókja nincs hozzárendelve az egyik korábbi szerepkörhöz, hozzá kell rendelnie egy [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely a következő táblázat szükséges műveleteihez van rendelve:

| Művelet                                                          | Name |
|---                                                              |---   |
| Microsoft.Network/virtualNetworks/virtualNetworkPeerings/write  | Az A virtuális hálózatról a B virtuális hálózatra való társítás létrehozásához szükséges. A virtuális hálózatnak virtuális hálózatnak (Resource Manager) kell lennie.          |
| Microsoft. Network/virtualNetworks/peer/Action                   | A (z) B virtuális hálózatról (Resource Manager) az A virtuális hálózatra való társítás létrehozásához szükséges                                                       |
| Microsoft. ClassicNetwork/virtualNetworks/társ/művelet                   | A virtuális hálózat B (klasszikus) és a virtuális hálózat közötti társítás létrehozásához szükséges.                                                                |
| Microsoft. Network/virtualNetworks/virtualNetworkPeerings/READ   | Virtuális hálózati társak beolvasása   |
| Microsoft. Network/virtualNetworks/virtualNetworkPeerings/delete | Virtuális hálózati társak törlése |

## <a name="next-steps"></a>Következő lépések

- A virtuális hálózatok közötti társviszony az azonos vagy eltérő előfizetésekben lévő, azonos vagy eltérő üzembehelyezési modelleken keresztül létrehozott virtuális hálózatok között jön létre. Végezzen el egy oktatóanyagot a következő forgatókönyvek egyikéhez:

  |Azure üzembehelyezési modell             | Előfizetés  |
  |---------                          |---------|
  |Mindkét Resource Manager              |[Ugyanaz](tutorial-connect-virtual-networks-portal.md)|
  |                                   |[Különböző](create-peering-different-subscriptions.md)|
  |Egy Resource Manager, egy klasszikus  |[Ugyanaz](create-peering-different-deployment-models.md)|
  |                                   |[Különböző](create-peering-different-deployment-models-subscriptions.md)|

- Megtudhatja, hogyan hozhat létre [sugaras hálózati topológiát](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Virtuális hálózati társítás létrehozása a [PowerShell](powershell-samples.md) vagy az [Azure CLI](cli-samples.md) -szkriptek használatával vagy az Azure [Resource Manager-sablonok](template-samples.md) használatával
- [Azure Policy-definíciók](./policy-reference.md) létrehozása és társítása virtuális hálózatokhoz