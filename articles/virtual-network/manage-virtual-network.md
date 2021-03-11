---
title: Azure-beli virtuális hálózat létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Hozzon létre és töröljön egy virtuális hálózatot, és módosítsa a beállításokat, például a DNS-kiszolgálókat és az IP-címtartományt egy meglévő virtuális hálózat számára.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/10/2019
ms.author: kumud
ms.openlocfilehash: 1ec4e26e266a1777857fe0e890b093f60a3597f0
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102612524"
---
# <a name="create-change-or-delete-a-virtual-network"></a>Virtuális hálózat létrehozása, módosítása vagy törlése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Megtudhatja, hogyan hozhat létre és törölhet virtuális hálózatokat, és hogyan módosíthatja a beállításokat, például a DNS-kiszolgálókat és az IP-címeket a meglévő virtuális hálózatok esetében. Ha még nem ismeri a virtuális hálózatokat, a [virtuális hálózatok áttekintésében](virtual-networks-overview.md) és az [oktatóanyag](quick-create-portal.md)elvégzésével többet is megtudhat. Egy virtuális hálózat alhálózatokat tartalmaz. Az alhálózatok létrehozásának, módosításának és törlésének megismeréséhez lásd: [alhálózatok kezelése](virtual-network-manage-subnet.md).

## <a name="before-you-begin"></a>Előkészületek

A cikk bármely szakaszának lépéseinek elvégzése előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free).
- Ha a portált használja, nyissa meg https://portal.azure.com , majd jelentkezzen be az Azure-fiókjával.
- Ha a cikkben szereplő feladatok végrehajtásához PowerShell-parancsokat használ, futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy a PowerShellt a számítógépről futtatva. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31 vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, akkor azt is futtatnia kell, `az login` hogy létre kell hoznia egy, az Azure-hoz való kapcsolódást.
- A fiókkal, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy olyan [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyekben](#permissions)felsorolt megfelelő műveletekhez van rendelve.

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

1. Válassza **az + erőforrás létrehozása**  >  **hálózatkezelés**  >  **virtuális hálózat** lehetőséget.
2. Adja meg vagy válassza ki a következő beállítások értékeit, majd válassza a **Létrehozás** lehetőséget:
   - **Név**: a névnek egyedinek kell lennie abban az [erőforráscsoportban](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) , amelyet a virtuális hálózat létrehozásához választ. A virtuális hálózat létrehozása után a név nem módosítható. Az idő múlásával több virtuális hálózatot is létrehozhat. A névadási javaslatokért lásd: [elnevezési konvenciók](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources). Az elnevezési konvenciók segítségével könnyebben kezelhet több virtuális hálózatot.
   - **Címterület**: egy virtuális hálózat címterület egy vagy több nem átfedésben lévő címtartományból áll, amelyek CIDR-jelöléssel vannak megadva. Az Ön által definiált címtartomány lehet nyilvános vagy privát (RFC 1918). Függetlenül attól, hogy a címtartományt nyilvános vagy magánjellegűként határozza meg, a címtartomány csak a virtuális hálózatról, az összekapcsolt virtuális hálózatokról és a virtuális hálózathoz csatlakoztatott helyszíni hálózatokról érhető el. A következő címtartományok nem vehetők fel:
     - 224.0.0.0/4 (csoportos küldés)
     - 255.255.255.255/32 (szórásos)
     - 127.0.0.0/8 (visszacsatolási)
     - 169.254.0.0/16 (hivatkozás – helyi)
     - 168.63.129.16/32 (belső DNS, DHCP és Azure Load Balancer [Health](../load-balancer/load-balancer-custom-probe-overview.md#probesource)mintavétel)

     Bár csak egy címtartományt határozhat meg a virtuális hálózat portálon történő létrehozásakor, a virtuális hálózat létrehozása után további címtartományt is hozzáadhat a Címterület számára. Ha meg szeretné tudni, hogyan adhat hozzá címtartományt egy meglévő virtuális hálózathoz, tekintse meg a [Címtartomány hozzáadása vagy eltávolítása](#add-or-remove-an-address-range)című témakört.

     > [!WARNING]
     > Ha a virtuális hálózat olyan címtartományt tartalmaz, amelyek átfedésben vannak egy másik virtuális hálózattal vagy helyszíni hálózattal, a két hálózat nem csatlakoztatható. Címtartomány meghatározása előtt gondolja át, hogy szeretne-e a virtuális hálózatot más virtuális hálózatokhoz vagy helyi hálózatokhoz csatlakozni a jövőben. A Microsoft a szervezete tulajdonában lévő magánhálózati címtartomány vagy nyilvános címtartomány használatával javasolja a virtuális hálózati címtartományok konfigurálását.
     >

     - **Alhálózat neve**: az alhálózat nevének egyedinek kell lennie a virtuális hálózaton belül. Az alhálózat neve nem módosítható az alhálózat létrehozása után. A portálhoz egy virtuális hálózat létrehozásakor meg kell határoznia egy alhálózatot, bár a virtuális hálózatnak nincs szüksége alhálózatra. A portálon megadhat egy vagy több alhálózatot, amikor létrehoz egy virtuális hálózatot. A virtuális hálózat létrehozása után később további alhálózatokat adhat hozzá a virtuális hálózathoz. Ha alhálózatot szeretne hozzáadni egy virtuális hálózathoz, tekintse meg az [alhálózatok kezelése](virtual-network-manage-subnet.md)című témakört. Az Azure CLI vagy a PowerShell használatával több alhálózattal rendelkező virtuális hálózatot is létrehozhat.

       >[!TIP]
       >Előfordulhat, hogy a rendszergazdák különböző alhálózatokat hoznak létre az alhálózatok közötti forgalom-útválasztás szűrésére vagy szabályozására. Az alhálózatok meghatározása előtt gondolja át, hogyan szeretné szűrni és irányítani az alhálózatok közötti forgalmat. Ha többet szeretne megtudni az alhálózatok közötti adatforgalom szűréséről, tekintse meg a [hálózati biztonsági csoportok](./network-security-groups-overview.md)című témakört. Az Azure automatikusan irányítja az alhálózatok közötti forgalmat, de felülbírálhatja az Azure alapértelmezett útvonalait. Az Azure-beli alapértelmezett alhálózati forgalom útválasztásával kapcsolatos további tudnivalókért tekintse meg az [Útválasztás áttekintése](virtual-networks-udr-overview.md)című témakört.
       >

     - **Alhálózati címtartomány**: a tartománynak a virtuális hálózathoz megadott címtartomány közé kell esnie. A legkisebb megadható tartomány a/29, amely nyolc IP-címet biztosít az alhálózathoz. Az Azure fenntartja az egyes alhálózatok első és utolsó címeit a protokollok megfelelőségének megadásához. Három további cím van fenntartva az Azure-szolgáltatások használatára. Ennek eredményeképpen a/29 alhálózati címtartomány-tartománnyal rendelkező virtuális hálózatok csak három használható IP-címmel rendelkeznek. Ha virtuális hálózatot szeretne csatlakozni egy VPN-átjáróhoz, létre kell hoznia egy átjáró-alhálózatot. További információ [az átjáró-alhálózatokkal kapcsolatos adott címtartomány-megfontolásokról](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub). A címtartomány az alhálózat létrehozása után módosítható adott körülmények között. Az alhálózati címtartomány módosításáról az [alhálózatok kezelése](virtual-network-manage-subnet.md)című témakörben olvashat bővebben.
     - **Előfizetés**: válasszon ki egy [előfizetést](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Nem használhatja ugyanazt a virtuális hálózatot egynél több Azure-előfizetésben. Egy virtuális hálózatot azonban egyetlen előfizetésben is összekapcsolhat [a virtuális hálózatok](virtual-network-peering-overview.md)más előfizetésekben lévő virtuális hálózatokkal. A virtuális hálózathoz csatlakoztatott összes Azure-erőforrásnak ugyanabban az előfizetésben kell lennie, mint a virtuális hálózatnak.
     - **Erőforráscsoport**: válasszon ki egy meglévő [erőforráscsoportot](../azure-resource-manager/management/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-groups) , vagy hozzon létre egy újat. A virtuális hálózathoz csatlakozó Azure-erőforrások a virtuális hálózattal vagy más erőforráscsoport-csoporttal azonos erőforráscsoporthoz is lehetnek.
     - **Hely**: válasszon ki egy Azure- [helyet](https://azure.microsoft.com/regions/), más néven régiót. Egy virtuális hálózat csak egy Azure-helyen lehet. A virtuális hálózatokat azonban egy másik helyen lévő virtuális hálózathoz is kapcsolódhatja egy VPN-átjáró használatával. A virtuális hálózathoz csatlakoztatott összes Azure-erőforrásnak a virtuális hálózattal megegyező helyen kell lennie.

**Parancsok**

- Azure CLI: [az Network vnet Create](/cli/azure/network/vnet)
- PowerShell: [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork)

## <a name="view-virtual-networks-and-settings"></a>Virtuális hálózatok és beállítások megtekintése

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
2. A virtuális hálózatok listájából válassza ki azt a virtuális hálózatot, amelyre vonatkozóan meg szeretné tekinteni a beállításokat.
3. A következő beállítások szerepelnek a kiválasztott virtuális hálózatban:
   - **Áttekintés**: a virtuális hálózattal kapcsolatos információkat nyújt, beleértve a Címterület és a DNS-kiszolgálókat. Az alábbi képernyőfelvételen egy **MyVNet** nevű virtuális hálózat áttekintő beállításai láthatók:

     ![Hálózati adapter áttekintése](./media/manage-virtual-network/vnet-overview.png)

     A virtuális hálózatot másik előfizetéshez vagy erőforráscsoporthoz is áthelyezheti, ha kiválasztja az **erőforráscsoport** vagy az **előfizetés neve** melletti **módosítás** lehetőséget. A virtuális hálózatok áthelyezésével kapcsolatos további információkért lásd: [erőforrások áthelyezése másik erőforráscsoporthoz vagy előfizetésbe](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json). A cikk felsorolja az előfeltételeket, valamint az erőforrások áthelyezését a Azure Portal, a PowerShell és az Azure CLI használatával. A virtuális hálózathoz csatlakozó összes erőforrásnak a virtuális hálózattal kell lépnie.
   - **Címterület**: a virtuális hálózathoz rendelt címtartomány szerepel a felsorolásban. Ha meg szeretné tudni, hogyan adhat hozzá és távolíthat el címtartományt a címtartomány számára, hajtsa végre a [Címtartomány hozzáadása vagy eltávolítása](#add-or-remove-an-address-range)című témakör lépéseit.
   - **Csatlakoztatott eszközök**: a virtuális hálózathoz csatlakoztatott összes erőforrás szerepel a felsorolásban. Az előző képernyőképen három hálózati adapter és egy terheléselosztó csatlakozik a virtuális hálózathoz. A rendszer minden olyan új erőforrást listáz, amelyet létrehoz és a virtuális hálózathoz csatlakozik. Ha töröl egy olyan erőforrást, amely a virtuális hálózathoz csatlakozik, már nem jelenik meg a listában.
   - **Alhálózatok**: a virtuális hálózaton belül található alhálózatok listája látható. Az alhálózatok hozzáadásával és eltávolításával kapcsolatos további információkért lásd: [alhálózatok kezelése](virtual-network-manage-subnet.md).
   - **DNS-kiszolgálók**: megadhatja, hogy az Azure belső DNS-kiszolgálója vagy egy egyéni DNS-kiszolgáló névfeloldást biztosít-e a virtuális hálózathoz csatlakozó eszközök számára. Ha a Azure Portal használatával hoz létre virtuális hálózatot, alapértelmezés szerint az Azure DNS-kiszolgálóit a névfeloldáshoz használja a virtuális hálózaton belül. A DNS-kiszolgálók módosításához hajtsa végre a jelen cikk [DNS-kiszolgálók módosítása](#change-dns-servers) című részében ismertetett lépéseket.
   - **Társítások: Ha** vannak meglévő alhálózatok az előfizetésben, itt vannak felsorolva. Megtekintheti a meglévő társítások beállításait, vagy létrehozhat, módosíthat vagy törölhet társításokat. További információ a társításokról: [Virtual Network peering](virtual-network-peering-overview.md).
   - **Tulajdonságok**: megjeleníti a virtuális hálózat beállításait, beleértve a virtuális hálózat erőforrás-azonosítóját és az Azure-előfizetést is.
   - **Ábra**: a diagram a virtuális hálózathoz csatlakozó összes eszköz vizuális megjelenítését biztosítja. A diagram néhány kulcsfontosságú információt tartalmaz az eszközökről. Ha ebben a nézetben szeretne felügyelni egy eszközt, a diagramon válassza ki az eszközt.
   - **Általános Azure-beállítások**: az általános Azure-beállításokkal kapcsolatos további információkért tekintse meg a következő információkat:
     - [Tevékenységnapló](../azure-monitor/essentials/platform-logs-overview.md)
     - [Hozzáférés-vezérlés (IAM)](../role-based-access-control/overview.md)
     - [Címkék](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Zárolások](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
     - [Automation-szkript](../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)

**Parancsok**

- Azure CLI: [az Network vnet show](/cli/azure/network/vnet)
- PowerShell: [Get-AzVirtualNetwork](/powershell/module/az.network/get-azvirtualnetwork)

## <a name="add-or-remove-an-address-range"></a>Címtartomány hozzáadása vagy eltávolítása

Hozzáadhat és eltávolíthat egy virtuális hálózathoz tartozó címtartományt. Meg kell adni egy címtartományt a CIDR-jelölésben, és nem lehet átfedésben az azonos virtuális hálózaton belüli más címtartományok. Az Ön által meghatározott címtartományok lehetnek nyilvánosak vagy magánjellegűek (RFC 1918). Függetlenül attól, hogy a címtartományt nyilvános vagy magánjellegűként határozza meg, a címtartomány csak a virtuális hálózatról, az összekapcsolt virtuális hálózatokról és a virtuális hálózathoz csatlakoztatott helyszíni hálózatokról érhető el. 

A virtuális hálózat címtartomány csökkenthető, ha továbbra is a társított alhálózatok tartományait tartalmazza. Emellett kiterjesztheti a címtartományt is, például a/16 és a/8 változót. 

<!-- the above statement has been edited to reflect the most recent comments on the reopened issue: https://github.com/MicrosoftDocs/azure-docs/issues/20572 -->

A következő címtartományok nem vehetők fel:

- 224.0.0.0/4 (csoportos küldés)
- 255.255.255.255/32 (szórásos)
- 127.0.0.0/8 (visszacsatolási)
- 169.254.0.0/16 (hivatkozás – helyi)
- 168.63.129.16/32 (belső DNS, DHCP és Azure Load Balancer [Health](../load-balancer/load-balancer-custom-probe-overview.md#probesource)mintavétel)

Címtartomány hozzáadása vagy eltávolítása:

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
2. A virtuális hálózatok listájából válassza ki azt a virtuális hálózatot, amelyhez címtartományt szeretne hozzáadni vagy eltávolítani.
3. Válassza a **címterület** lehetőséget a **Beállítások** területen.
4. Hajtsa végre a következő lehetőségek egyikét:
    - **Címtartomány hozzáadása**: adja meg az új címtartományt. A címtartomány nem fedi át a virtuális hálózathoz definiált meglévő címtartományt.
    - **Címtartomány eltávolítása**: az eltávolítani kívánt címtartomány jobb oldalán válassza a **...**, majd az **Eltávolítás** lehetőséget. Ha egy alhálózat létezik a címtartományból, nem távolíthatja el a címtartományt. Címtartomány eltávolításához először törölnie kell a címtartomány meglévő alhálózatait (és az alhálózatokban található összes erőforrást).
5. Kattintson a **Mentés** gombra.

**Parancsok**

- Azure CLI: [az Network vnet Update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="change-dns-servers"></a>DNS-kiszolgálók módosítása

A virtuális hálózathoz csatlakozó virtuális gépek a virtuális hálózathoz megadott DNS-kiszolgálókkal regisztrálhatók. A névfeloldáshoz a megadott DNS-kiszolgálót is használják. A virtuális gépek minden hálózati adapteréhez (NIC) tartozhat a saját DNS-kiszolgáló beállításai. Ha a hálózati adapter saját DNS-kiszolgáló beállításaival rendelkezik, a DNS-kiszolgáló beállításai felülbírálják a virtuális hálózatra vonatkozó beállításokat. A hálózati ADAPTERek DNS-beállításaival kapcsolatos további tudnivalókért tekintse meg a [hálózati adapterrel kapcsolatos feladatokat és beállításokat](virtual-network-network-interface.md#change-dns-servers). Ha többet szeretne megtudni az Azure Cloud Services-beli virtuális gépek és szerepkör-példányok névfeloldásáról, tekintse meg a [virtuális gépek és a szerepkör-példányok](virtual-networks-name-resolution-for-vms-and-role-instances.md)névfeloldását ismertető témakört DNS-kiszolgáló hozzáadása, módosítása vagy eltávolítása:

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
2. A virtuális hálózatok listájából válassza ki azt a virtuális hálózatot, amelyre vonatkozóan módosítani kívánja a DNS-kiszolgálókat.
3. Válassza a **DNS-kiszolgálók** lehetőséget a **Beállítások** területen.
4. Válasszon az alábbi lehetőségek közül:
   - **Alapértelmezett (Azure által biztosított)**: az összes erőforrás neve és magánhálózati IP-címe automatikusan regisztrálva lesz a Azure DNS-kiszolgálókon. A neveket az ugyanahhoz a virtuális hálózathoz csatlakozó erőforrások között lehet feloldani. Ezzel a beállítással nem oldhatók fel a nevek a virtuális hálózatok között. A nevek virtuális hálózatokon keresztüli feloldásához egyéni DNS-kiszolgálót kell használnia.
   - **Egyéni**: hozzáadhat egy vagy több kiszolgálót, akár egy virtuális hálózat Azure-ra vonatkozó korlátját is. További információ a DNS-kiszolgáló korlátairól: [Azure-korlátok](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-networking-limits-classic). Az alábbi lehetőségek állnak rendelkezésére:
   - **Adja** meg a következő címeket: hozzáadja a kiszolgálót a virtuális hálózati DNS-kiszolgálók listájához. Ez a beállítás a DNS-kiszolgálót is regisztrálja az Azure-ban. Ha már regisztrált egy DNS-kiszolgálót az Azure-ban, akkor kiválaszthatja a listában a DNS-kiszolgálót.
   - Válasszon ki **egy címeket**: az eltávolítani kívánt kiszolgáló mellett válassza a **...**, majd az **Eltávolítás** lehetőséget. A kiszolgáló törlésével a kiszolgáló csak a virtuális hálózatok listájáról távolítható el. A DNS-kiszolgáló továbbra is regisztrálva lesz az Azure-ban a többi virtuális hálózat használatára.
   - **DNS-kiszolgáló címeinek átrendezése**: fontos ellenőrizni, hogy a DNS-kiszolgálókat a környezetének megfelelő sorrendben sorolja-e fel. A DNS-kiszolgálók listáját a rendszer a megadott sorrendben használja. Nem működnek ciklikus multiplexelés beállításként. Ha a lista első DNS-kiszolgálója elérhető, akkor az ügyfél ezt a DNS-kiszolgálót használja, függetlenül attól, hogy a DNS-kiszolgáló megfelelően működik-e. Távolítsa el az összes felsorolt DNS-kiszolgálót, majd adja őket vissza a kívánt sorrendben.
   - **Módosítsa a címeket**: jelölje ki a DNS-kiszolgálót a listában, majd adja meg az új címeket.
5. Kattintson a **Mentés** gombra.
6. Indítsa újra a virtuális hálózathoz csatlakozó virtuális gépeket, hogy azok hozzá legyenek rendelve az új DNS-kiszolgáló beállításaihoz. A virtuális gépek továbbra is a jelenlegi DNS-beállításokat használják, amíg újra nem indítják őket.

**Parancsok**

- Azure CLI: [az Network vnet Update](/cli/azure/network/vnet)
- PowerShell: [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork)

## <a name="delete-a-virtual-network"></a>Virtuális hálózat törlése

A virtuális hálózat csak akkor törölhető, ha nincs csatlakoztatva erőforrás. Ha vannak erőforrások csatlakoztatva a virtuális hálózaton található bármely alhálózathoz, először törölnie kell azokat az erőforrásokat, amelyek a virtuális hálózaton lévő összes alhálózathoz csatlakoznak. Az erőforrások törléséhez szükséges lépések az erőforrástól függően változnak. Az alhálózatokhoz csatlakozó erőforrások törlésével kapcsolatos információkért olvassa el a törölni kívánt erőforrástípusok dokumentációját. Virtuális hálózat törlése:

1. A portál felső részén található Keresés mezőben adja meg a *virtuális hálózatok* kifejezést a keresőmezőbe. Ha a **virtuális hálózatok** megjelennek a keresési eredmények között, válassza ki.
2. A virtuális hálózatok listájából válassza ki a törölni kívánt virtuális hálózatot.
3. A **Beállítások** területen ellenőrizze, hogy nincsenek-e csatlakoztatott eszközök a virtuális hálózathoz a **csatlakoztatott eszközök** kiválasztásával. Ha vannak csatlakoztatott eszközök, törölnie kell őket a virtuális hálózat törlése előtt. Ha nincsenek csatlakoztatott eszközök, válassza az **Áttekintés** lehetőséget.
4. Válassza a **Törlés** elemet.
5. A virtuális hálózat törlésének megerősítéséhez válassza az **Igen** lehetőséget.

**Parancsok**

- Azure CLI: [Azure hálózati vnet törlése](/cli/azure/network/vnet)
- PowerShell: [Remove-AzVirtualNetwork](/powershell/module/az.network/remove-azvirtualnetwork)

## <a name="permissions"></a>Engedélyek

Ha virtuális hálózatokon szeretné elvégezni a feladatokat, a fiókját hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő műveletekhez van rendelve:

| Művelet                                  |   Name                                |
|---------------------------------------- |   --------------------------------    |
|Microsoft. Network/virtualNetworks/READ   |   Virtuális hálózat beolvasása              |
|Microsoft. Network/virtualNetworks/Write  |   Virtuális hálózat létrehozása vagy frissítése  |
|Microsoft. Network/virtualNetworks/delete |   Virtuális hálózat törlése            |

## <a name="next-steps"></a>Következő lépések

- Virtuális hálózat létrehozása [PowerShell](powershell-samples.md) vagy [Azure CLI](cli-samples.md) parancsfájl használatával vagy Azure [Resource Manager-sablonok](template-samples.md) használatával
- [Azure Policy-definíciók](./policy-reference.md) létrehozása és társítása virtuális hálózatokhoz
