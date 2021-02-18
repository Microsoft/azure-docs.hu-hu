---
title: Azure hálózati adapter létrehozása, módosítása vagy törlése
titlesuffix: Azure Virtual Network
description: Megtudhatja, mi a hálózati adapter, és hogyan hozhat létre, módosíthat beállításokat és törölhet egyet.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 1/22/2020
ms.author: kumud
ms.openlocfilehash: 74e09b4798a648b6a881fb05f1128831ad5f4aff
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100586437"
---
# <a name="create-change-or-delete-a-network-interface"></a>Hálózati adapter létrehozása, módosítása vagy törlése

Útmutató a hálózati adapterek létrehozásához, módosításához és törléséhez. A hálózati adapterek lehetővé teszik, hogy egy Azure-beli virtuális gép kommunikáljon az internettel, az Azure-ral és a helyszíni erőforrásokkal. Ha a Azure Portal használatával hoz létre virtuális gépet, a portál egy alapértelmezett beállításokkal rendelkező hálózati adaptert hoz létre. Ehelyett dönthet úgy, hogy az egyéni beállításokkal hálózati adaptereket hoz létre, és egy vagy több hálózati adaptert ad hozzá egy virtuális géphez a létrehozásakor. Előfordulhat, hogy módosítani szeretné egy meglévő hálózati adapter alapértelmezett hálózati adapterének beállításait is. Ez a cikk bemutatja, hogyan hozhat létre egyéni beállításokkal rendelkező hálózati adaptereket, hogyan módosíthatja a meglévő beállításokat, például a hálózati szűrő (hálózati biztonsági csoport) hozzárendelését, az alhálózat-hozzárendelést, a DNS-kiszolgáló beállításait és az IP-továbbítást, valamint törölhet egy hálózati adaptert.

Ha IP-címeket kell hozzáadnia, módosítania vagy eltávolítania egy hálózati adapterhez, tekintse meg az [IP-címek kezelése](virtual-network-network-interface-addresses.md)című témakört. Ha hálózati adaptereket kell hozzáadnia, vagy el kell távolítania a hálózati adaptereket a virtuális gépekről, tekintse meg a [hálózati adapterek hozzáadása vagy eltávolítása](virtual-network-network-interface-vm.md)című témakört.

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A cikk bármely szakaszának lépéseinek elvégzése előtt hajtsa végre a következő feladatokat:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon az [ingyenes próbaverziós fiókra](https://azure.microsoft.com/free).
- Ha a portált használja, nyissa meg https://portal.azure.com , majd jelentkezzen be az Azure-fiókjával.
- Ha a cikkben szereplő feladatok végrehajtásához PowerShell-parancsokat használ, futtassa a [Azure Cloud Shell](https://shell.azure.com/powershell)parancsait, vagy a PowerShellt a számítógépről futtatva. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell modul 1.0.0-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha az Azure parancssori felület (CLI) parancsait használja a jelen cikkben található feladatok elvégzéséhez, futtassa a [Azure Cloud Shell](https://shell.azure.com/bash)parancsait, vagy a CLI-t a számítógépről futtatva. Ehhez az oktatóanyaghoz az Azure CLI 2.0.28 verziójára vagy újabb verziójára van szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha helyileg futtatja az Azure CLI-t, akkor azt is futtatnia kell, `az login` hogy létre kell hoznia egy, az Azure-hoz való kapcsolódást.

A fiókkal, amelybe bejelentkezik, vagy az Azure-hoz csatlakozik, hozzá kell rendelnie a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy olyan [Egyéni szerepkörhöz](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , amely az [engedélyekben](#permissions)felsorolt megfelelő műveletekhez van rendelve.

## <a name="create-a-network-interface"></a>Hálózati adapter létrehozása

Amikor a Azure Portal használatával hoz létre virtuális gépet, a portál egy alapértelmezett beállításokkal rendelkező hálózati adaptert hoz létre. Ha inkább a hálózati adapter összes beállítását szeretné megadni, létrehozhat egy egyéni beállításokat tartalmazó hálózati adaptert, és csatlakoztathatja a hálózati adaptert egy virtuális géphez a virtuális gép létrehozásakor (a PowerShell vagy az Azure CLI használatával). Létrehozhat egy hálózati adaptert is, és hozzáadhatja azt egy meglévő virtuális géphez (a PowerShell vagy az Azure CLI használatával). Ha meg szeretné tudni, hogyan hozhat létre egy meglévő hálózati adapterrel rendelkező virtuális gépet, vagy hogyan adhat hozzá vagy távolíthat el hálózati adaptereket a meglévő virtuális gépekről, tekintse meg a [hálózati adapterek hozzáadása vagy eltávolítása](virtual-network-network-interface-vm.md)című témakört. Hálózati adapter létrehozása előtt egy meglévő [virtuális hálózattal](manage-virtual-network.md) kell rendelkeznie ugyanazon a helyen és előfizetésben, amelyhez hálózati adaptert hoz létre.

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a *hálózati adapterek* kifejezést. Ha a **hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza a **+ Hozzáadás** lehetőséget a **hálózati adapterek** területen.
3. Adja meg vagy válassza ki a következő beállítások értékeit, majd válassza a **Létrehozás** lehetőséget:

    |Beállítás|Kötelező?|Részletek|
    |---|---|---|
    |Name|Yes|A névnek egyedinek kell lennie a kiválasztott erőforráscsoporthoz. Az Azure-előfizetésben valószínűleg több hálózati adapterrel fog rendelkezni. A különböző hálózati adapterek felügyeletét megkönnyítő elnevezési konvenció létrehozásával kapcsolatos javaslatokért lásd: [elnevezési konvenciók](/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#resource-naming). A hálózati adapter létrehozása után a név nem módosítható.|
    |Virtuális hálózat|Yes|Válassza ki a virtuális hálózatot a hálózati adapterhez. Csak olyan hálózati adaptert rendelhet hozzá egy virtuális hálózathoz, amely ugyanabban az előfizetésben és helyen található, mint a hálózati adapter. A hálózati adapter létrehozása után nem módosíthatja azt a virtuális hálózatot, amelyhez hozzá van rendelve. Ahhoz a virtuális géphez, amelyhez a hálózati adaptert hozzá kívánja adni, ugyanazon a helyen és előfizetésen belül kell lennie, mint a hálózati adapternek.|
    |Alhálózat|Yes|Válasszon ki egy alhálózatot a kiválasztott virtuális hálózaton belül. Módosíthatja azt az alhálózatot, amelyhez a hálózati adapter hozzá lett rendelve.|
    |Magánhálózati IP-cím hozzárendelése|Yes| Ebben a beállításban az IPv4-címekhez tartozó hozzárendelési módszert választja. Válasszon a következő hozzárendelési módszerek közül: **dinamikus:** ha ezt a beállítást választja, az Azure automatikusan kiosztja a következő rendelkezésre álló címeket a kiválasztott alhálózat címterület területéről. **Statikus:** Ha ezt a beállítást választja, manuálisan kell hozzárendelni egy elérhető IP-címet a kiválasztott alhálózat címterület területéről. A statikus és a dinamikus címek addig nem változnak, amíg meg nem változtatja őket, vagy a hálózati adaptert törölték. A hozzárendelési módszer a hálózati adapter létrehozása után módosítható. Az Azure DHCP-kiszolgáló a virtuális gép operációs rendszerén belüli hálózati adapterhez rendeli ezt a címeket.|
    |Hálózati biztonsági csoporttal|No| Hagyja a **none** értéket, válasszon ki egy meglévő [hálózati biztonsági csoportot](./network-security-groups-overview.md), vagy [hozzon létre egy hálózati biztonsági csoportot](tutorial-filter-network-traffic.md). A hálózati biztonsági csoportok lehetővé teszik hálózati adapterek hálózati forgalmának szűrését. A hálózati adapterre nulla vagy egy hálózati biztonsági csoportot is alkalmazhat. Nulla vagy egy hálózati biztonsági csoport is alkalmazható arra az alhálózatra, amelyhez a hálózati adapter hozzá van rendelve. Ha hálózati biztonsági csoportot alkalmaz egy hálózati adapterre, és azt az alhálózatot, amelyhez a hálózati adapter hozzá van rendelve, időnként váratlan eredmények történnek. A hálózati adapterekre és alhálózatokra alkalmazott hálózati biztonsági csoportok hibakereséséhez lásd: [hálózati biztonsági csoportok hibáinak megoldása](diagnose-network-traffic-filter-problem.md).|
    |Előfizetés|Yes|Válasszon ki egy Azure- [előfizetést](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription). Az a virtuális gép, amelyhez csatlakoztatni kívánja a hálózati adaptert, és ahhoz a virtuális hálózathoz, amelyhez csatlakozni kíván, ugyanahhoz az előfizetéshez kell tartoznia.|
    |Magánhálózati IP-cím (IPv6)|No| Ha bejelöli ezt a jelölőnégyzetet, a hálózati adapterhez rendelt IPv4-cím mellett egy IPv6-cím is hozzá van rendelve a hálózati adapterhez. Tekintse meg a jelen cikk IPv6 című szakaszát, amely fontos információkat nyújt az IPv6 hálózati adapterekkel való használatáról. Az IPv6-cím hozzárendelési metódusa nem választható ki. Ha IPv6-cím hozzárendelését választja, akkor azt a rendszer a dinamikus metódussal rendeli hozzá.
    |IPv6-név (csak akkor jelenik meg, ha a **MAGÁNHÁLÓZATI IP-cím (IPv6)** jelölőnégyzet be van jelölve) |Igen, ha a **magánhálózati IP-cím (IPv6)** jelölőnégyzet be van jelölve.| Ezt a nevet a rendszer egy másodlagos IP-konfigurációhoz rendeli hozzá a hálózati adapterhez. További információ az IP-konfigurációkról: [hálózati adapter beállításainak megtekintése](#view-network-interface-settings).|
    |Erőforráscsoport|Yes|Válasszon ki egy meglévő [erőforráscsoportot](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) , vagy hozzon létre egyet. A hálózati adapterek ugyanabban vagy más erőforráscsoporthoz is létezhetnek, mint a virtuális gép, amelyhez csatlakoztatja, vagy a virtuális hálózathoz, amelyhez csatlakozik.|
    |Hely|Igen|Az a virtuális gép, amelyhez csatlakoztatni kívánja a hálózati adaptert és a virtuális hálózatot, ugyanazon a [helyen](https://azure.microsoft.com/regions)kell lennie, más néven régiónak.|

A portál nem biztosítja a nyilvános IP-cím hozzárendelését a hálózati adapterhez a létrehozásakor, bár a portál létrehoz egy nyilvános IP-címet, és hozzárendeli azt egy hálózati adapterhez, amikor virtuális gépet hoz létre a portál használatával. Ha meg szeretné tudni, hogyan adhat hozzá nyilvános IP-címet a hálózati adapterhez a létrehozása után, tekintse meg az [IP-címek kezelése](virtual-network-network-interface-addresses.md)című témakört. Ha nyilvános IP-címmel rendelkező hálózati adaptert szeretne létrehozni, akkor a parancssori felület vagy a PowerShell használatával kell létrehoznia a hálózati adaptert.

A portál nem biztosítja a hálózati adapter alkalmazás-biztonsági csoportokhoz való hozzárendelését hálózati adapter létrehozásakor, de az Azure CLI és a PowerShell használatával. Egy meglévő hálózati adaptert hozzárendelhet egy alkalmazás biztonsági csoportjához a portál használatával, ha a hálózati adapter egy virtuális géphez van csatlakoztatva. Ha szeretné megtudni, hogyan rendeljen hozzá egy hálózati adaptert egy alkalmazás biztonsági csoportjához, tekintse meg az [alkalmazás-biztonsági csoportok hozzáadása vagy eltávolítása](#add-to-or-remove-from-application-security-groups)című témakört.

>[!Note]
> Az Azure csak akkor rendel hozzá MAC-címeket a hálózati adapterhez, ha a hálózati adapter csatlakoztatva van egy virtuális géphez, és a virtuális gép első alkalommal indul el. Nem adhatja meg az Azure által a hálózati adapterhez hozzárendelt MAC-címeket. A MAC-cím továbbra is a hálózati adapterhez lesz rendelve, amíg a hálózati adaptert nem törlik, vagy az elsődleges hálózati adapter elsődleges IP-konfigurációjához rendelt magánhálózati IP-cím módosul. További információ az IP-címekről és az IP-konfigurációkról: [IP-címek kezelése](virtual-network-network-interface-addresses.md)

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az network nic create](/cli/azure/network/nic)|
|PowerShell|[Új – AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface)|

## <a name="view-network-interface-settings"></a>Hálózati adapter beállításainak megtekintése

A hálózati adapterek többségi beállításait a létrehozása után tekintheti meg és módosíthatja. A portál nem jeleníti meg a DNS-utótagot vagy az alkalmazás biztonsági csoportjának tagságát a hálózati adapterhez. Használhatja a PowerShell vagy az Azure CLI- [parancsokat](#view-settings-commands) a DNS-utótag és az alkalmazás biztonsági csoportjának tagságának megtekintéséhez.

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a *hálózati adapterek* kifejezést. Ha a **hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert, amelyről meg szeretné tekinteni vagy módosítani kívánja a beállításokat a listából.
3. A kiválasztott hálózati adapteren a következő elemek jelennek meg:
   - **Áttekintés:** Információt nyújt a hálózati adapterről, például a hozzá rendelt IP-címekről, a hálózati adapterhez hozzárendelt virtuális hálózatról/alhálózatról, valamint azt a virtuális gépet, amelyhez a hálózati adapter csatlakoztatva van (ha van ilyen). Az alábbi képen egy **mywebserver256** nevű hálózati adapter áttekintő beállításai láthatók: ![ hálózati adapter – áttekintés](./media/virtual-network-network-interface/nic-overview.png)

     A hálózati adaptert egy másik erőforráscsoporthoz vagy előfizetésbe helyezheti át az **erőforráscsoport** vagy az **előfizetés neve** melletti (**módosítás**) lehetőség kiválasztásával. Ha áthelyezi a hálózati adaptert egy új előfizetésre, a hálózati adapterhez kapcsolódó összes erőforrást át kell helyeznie. Ha a hálózati adapter egy virtuális géphez van csatlakoztatva, például át kell helyeznie a virtuális gépet és a virtuális gépekkel kapcsolatos egyéb erőforrásokat is. Hálózati adapter áthelyezéséhez tekintse meg az [erőforrás áthelyezése új erőforráscsoporthoz vagy előfizetésre](../azure-resource-manager/management/move-resource-group-and-subscription.md?toc=%2fazure%2fvirtual-network%2ftoc.json#use-the-portal)című témakört. A cikk felsorolja az előfeltételeket, valamint az erőforrások áthelyezését a Azure Portal, a PowerShell és az Azure CLI használatával.
   - **IP-konfigurációk:** Az IP-konfigurációhoz rendelt nyilvános és magánhálózati IPv4-és IPv6-címeket itt találja. Ha egy IPv6-cím IP-konfigurációhoz van rendelve, a cím nem jelenik meg. Az IP-konfigurációkkal és az IP-címek hozzáadásával és eltávolításával kapcsolatos további tudnivalókért tekintse meg az [IP-címek konfigurálása Azure-beli hálózati adapterhez](virtual-network-network-interface-addresses.md)című témakört. Ebben a szakaszban az IP-továbbítás és az alhálózat-hozzárendelés is be van állítva. További információ ezekről a beállításokról: az [IP-továbbítás engedélyezése vagy letiltása](#enable-or-disable-ip-forwarding) , valamint [alhálózat-hozzárendelés módosítása](#change-subnet-assignment).
   - **DNS-kiszolgálók:** Megadhatja, hogy a rendszer melyik DNS-kiszolgálót rendelje hozzá az Azure DHCP-kiszolgálók által hozzárendelt hálózati adapterekhez. A hálózati adapter örökölheti a beállítást a virtuális hálózatból, amelyhez a hálózati adapter hozzá van rendelve, vagy rendelkezik egy egyéni beállítással, amely felülbírálja a hozzárendelt virtuális hálózat beállítását. A megjelenített elemek módosításához tekintse meg a [DNS-kiszolgálók módosítása](#change-dns-servers)című témakört.
   - **Hálózati biztonsági csoport (NSG):** Megjeleníti, hogy mely NSG van társítva a hálózati adapterhez (ha van ilyen). A NSG bejövő és kimenő szabályokat tartalmaz a hálózati adapter hálózati forgalmának szűrésére. Ha egy NSG van társítva a hálózati adapterhez, megjelenik a társított NSG neve. A megjelenített elemek módosításához tekintse meg [a hálózati biztonsági csoport társítása vagy](#associate-or-dissociate-a-network-security-group)leválasztása című témakört.
   - **Tulajdonságok:** A hálózati adapterrel kapcsolatos legfontosabb beállításokat jeleníti meg, beleértve annak MAC-címe (üres, ha a hálózati adapter nincs csatlakoztatva virtuális géphez), és az előfizetése megtalálható a rendszerben.
   - **Érvényes biztonsági szabályok:**  A biztonsági szabályok akkor jelennek meg, ha a hálózati adapter egy futó virtuális géphez van csatlakoztatva, és egy NSG van társítva a hálózati adapterhez, a hozzárendelt alhálózathoz vagy mindkettőhöz. A megjelenített információkkal kapcsolatos további tudnivalókért tekintse meg az [érvényes biztonsági szabályok megtekintése](#view-effective-security-rules)című témakört. További információ a NSG: [hálózati biztonsági csoportok](./network-security-groups-overview.md).
   - **Érvényes útvonalak:** Az útvonalak akkor jelennek meg, ha a hálózati adapter egy futó virtuális géphez van csatlakoztatva. Az útvonalak az Azure alapértelmezett útvonalait, a felhasználó által megadott útvonalakat, valamint az alhálózathoz a hálózati adapterhez hozzárendelt BGP-útvonalak kombinációját jelentik. Ha többet szeretne megtudni a megjelenített tartalmakról, tekintse meg a [hatályos útvonalak megtekintése](#view-effective-routes)című témakört. Az Azure alapértelmezett útvonalakkal és a felhasználó által megadott útvonalakkal kapcsolatos további információkért lásd: az [Útválasztás áttekintése](virtual-networks-udr-overview.md).
Gyakori Azure Resource Manager beállítások: Ha többet szeretne megtudni a gyakori Azure Resource Manager beállításokról, tekintse meg a [műveletnapló](../azure-monitor/essentials/platform-logs-overview.md), a [hozzáférés-vezérlés (iam)](../role-based-access-control/overview.md), a [címkék](../azure-resource-manager/management/tag-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a [zárolások](../azure-resource-manager/management/lock-resources.md?toc=%2fazure%2fvirtual-network%2ftoc.json)és az [Automation szkriptek](../azure-resource-manager/templates/export-template-portal.md)című témakört.

<a name="view-settings-commands"></a>**Parancsok**

Ha egy IPv6-cím hozzá van rendelve egy hálózati adapterhez, a PowerShell kimenete azt a tényt adja vissza, hogy a cím hozzá van rendelve, de nem adja vissza a hozzárendelt cím értékét. Hasonlóképpen, a CLI azt a tényt adja vissza, amelyet a rendszer hozzárendel, de a kimenetében *Null* értéket ad vissza a címben.

|Eszköz|Parancs|
|---|---|
|parancssori felület|az az [Network NIC List](/cli/azure/network/nic) az előfizetésben található hálózati adapterek megtekintéséhez; [az Network NIC show](/cli/azure/network/nic) a hálózati adapter beállításainak megtekintéséhez|
|PowerShell|A [Get-AzNetworkInterface](/powershell/module/az.network/get-aznetworkinterface) segítségével megtekintheti a hálózati adaptereket az előfizetésben, vagy megtekintheti a hálózati adapterek beállításait.|

## <a name="change-dns-servers"></a>DNS-kiszolgálók módosítása

A DNS-kiszolgálót az Azure DHCP-kiszolgáló rendeli hozzá a virtuális gép operációs rendszerén belüli hálózati adapterhez. A hozzárendelt DNS-kiszolgáló, függetlenül attól, hogy a DNS-kiszolgáló beállítása egy hálózati adapterre vonatkozik-e. A hálózati adapterek névfeloldási beállításaival kapcsolatos további tudnivalókért lásd: [virtuális gépek](virtual-networks-name-resolution-for-vms-and-role-instances.md)névfeloldása. A hálózati adapter örökölheti a virtuális hálózat beállításait, vagy használhatja a saját egyedi beállításait is, amelyek felülbírálják a virtuális hálózat beállítását.

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a *hálózati adapterek* kifejezést. Ha a **hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert, amelyről a listából a DNS-kiszolgálót módosítani kívánja.
3. A **Beállítások** területen válassza a **DNS-kiszolgálók** lehetőséget.
4. Válasszon a következők közül:
   - **Öröklés a virtuális hálózattól**: ezt a beállítást választva örökli a virtuális hálózathoz definiált DNS-kiszolgáló beállítását, amelyhez a hálózati adapter hozzá van rendelve. A virtuális hálózat szintjén definiálva van egy egyéni DNS-kiszolgáló vagy az Azure által biztosított DNS-kiszolgáló. Az Azure által biztosított DNS-kiszolgáló fel tudja oldani az ugyanahhoz a virtuális hálózathoz rendelt erőforrások gazdagépeit. A különböző virtuális hálózatokhoz rendelt erőforrások feloldásához a teljes tartománynevet kell használni.
   - **Egyéni**: beállíthatja a saját DNS-kiszolgálóját a nevek feloldására több virtuális hálózat között. Adja meg a DNS-kiszolgálóként használni kívánt kiszolgáló IP-címét. A megadott DNS-kiszolgáló címe csak ehhez a hálózati adapterhez van hozzárendelve, és felülbírálja a virtuális hálózat DNS-beállításait, amelyhez a hálózati adapter hozzá van rendelve.
     >[!Note]
     >Ha a virtuális gép egy rendelkezésre állási csoport részét képező hálózati adaptert használ, a rendszer az összes olyan DNS-kiszolgálót örökli, amely a rendelkezésre állási csoport részét képező összes hálózati adapterhez meg van adva.
5. Kattintson a **Mentés** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az Network NIC Update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="enable-or-disable-ip-forwarding"></a>IP-továbbítás engedélyezése vagy letiltása

Az IP-továbbítás lehetővé teszi, hogy a virtuális gép hálózati adaptert csatoljon a következőhöz:
- A hálózati adapterhez rendelt IP-konfigurációkhoz rendelt IP-címek egyikére vonatkozó hálózati forgalom fogadása.
- A hálózati forgalmat a hálózati adapter IP-konfigurációinak egyikéhez hozzárendelt eltérő forrás IP-címmel küldje el.

A beállítást engedélyezni kell minden olyan virtuális géphez csatlakoztatott hálózati adapterhez, amely megkapja a virtuális gép számára szükséges forgalmat. A virtuális gépek továbbítják a forgalmat, függetlenül attól, hogy több hálózati adapterrel vagy egy hozzá csatlakoztatott egyetlen hálózati adapterrel rendelkezik. Míg az IP-továbbítás egy Azure-beállítás, a virtuális gépnek egy olyan alkalmazást is futtatnia kell, amely képes továbbítani a forgalmat, például a tűzfalat, a WAN-optimalizálást és a terheléselosztási alkalmazásokat. Ha egy virtuális gép hálózati alkalmazásokat futtat, a virtuális gépet gyakran hálózati virtuális berendezésnek nevezzük. Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances)-en megtekintheti, hogy készen áll-e a hálózati virtuális berendezések üzembe helyezésére. Az IP-továbbítás jellemzően felhasználó által megadott útvonalakkal használatos. A felhasználó által megadott útvonalakkal kapcsolatos további tudnivalókért tekintse meg a [felhasználó által megadott útvonalakat](virtual-networks-udr-overview.md).

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a *hálózati adapterek* kifejezést. Ha a **hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert, amelyen engedélyezni vagy le szeretné tiltani az IP-továbbítást.
3. Válassza az **IP-konfigurációk** lehetőséget a **Beállítások** szakaszban.
4. A beállítás módosításához válassza az **engedélyezve** vagy a **Letiltva** (alapértelmezett beállítás) lehetőséget.
5. Kattintson a **Mentés** gombra.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az Network NIC Update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="change-subnet-assignment"></a>Alhálózat-hozzárendelés módosítása

Módosíthatja az alhálózatot, de nem a virtuális hálózatot, mert a hálózati adapter hozzá van rendelve.

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a *hálózati adapterek* kifejezést. Ha a **hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert, amelyhez módosítani kívánja az alhálózat-hozzárendelést.
3. Válassza az **IP-konfigurációk** lehetőséget a **Beállítások** területen. Ha bármely felsorolt IP-konfigurációhoz magánhálózati IP-cím tartozik **(statikus)** , a következő lépések végrehajtásával módosítania kell az IP-cím hozzárendelési módszerét dinamikusra. A hálózati adapterhez tartozó alhálózat-hozzárendelés módosításához minden magánhálózati IP-címet hozzá kell rendelni a dinamikus hozzárendelési módszerhez. Ha a címek hozzá vannak rendelve a dinamikus metódushoz, folytassa az ötödik lépéssel. Ha bármely IPv4-cím hozzá van rendelve a statikus hozzárendelési módszerhez, hajtsa végre a következő lépéseket a hozzárendelési módszer dinamikusra váltásához:
   - Válassza ki azt az IP-konfigurációt, amelynél módosítani szeretné az IPv4-cím hozzárendelési módszerét az IP-konfigurációk listájáról.
   - A magánhálózati IP-cím- **hozzárendelési** módszernél válassza a **dinamikus** lehetőséget. Nem rendelhet hozzá IPv6-címeket a statikus hozzárendelési módszerhez.
   - Kattintson a **Mentés** gombra.
4. Válassza ki azt az alhálózatot, amelybe át szeretné helyezni a hálózati adaptert az **alhálózat** legördülő listából.
5. Kattintson a **Mentés** gombra. Az új alhálózathoz tartozó alhálózat-címtartomány új dinamikus címeket rendel hozzá. Miután hozzárendelte a hálózati adaptert egy új alhálózathoz, hozzárendelhet egy statikus IPv4-címeket az új alhálózat-címtartomány közül, ha Ön választja. A hálózati adapter IP-címeinek hozzáadásával, módosításával és eltávolításával kapcsolatos további tudnivalókért tekintse meg az [IP-címek kezelése](virtual-network-network-interface-addresses.md)című témakört.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az Network NIC IP-config Update](/cli/azure/network/nic/ip-config)|
|PowerShell|[Set-AzNetworkInterfaceIpConfig](/powershell/module/az.network/set-aznetworkinterfaceipconfig)|

## <a name="add-to-or-remove-from-application-security-groups"></a>Alkalmazás-biztonsági csoportok hozzáadása vagy eltávolítása

Ha a hálózati adapter egy virtuális géphez van csatlakoztatva, csak hálózati adaptert adhat hozzá, vagy távolíthat el hálózati adaptert egy alkalmazás biztonsági csoportjából a portál használatával. A PowerShell vagy az Azure CLI használatával hálózati adaptert adhat hozzá vagy távolíthat el egy alkalmazás biztonsági csoportjából, függetlenül attól, hogy a hálózati adapter csatlakoztatva van-e egy virtuális géphez, vagy sem. További információ az [alkalmazás biztonsági csoportjairól](./network-security-groups-overview.md#application-security-groups) és [az alkalmazás biztonsági csoportjainak létrehozásáról](manage-network-security-group.md).

1. A portál tetején található *erőforrások, szolgáltatások és dokumentumok keresése* mezőbe írja be annak a virtuális gépnek a nevét, amelyhez hozzá szeretné adni, vagy onnan eltávolítani kívánt hálózati adaptert. Ha a virtuális gép neve megjelenik a keresési eredmények között, válassza ki.
2. A **BEÁLLÍTÁSOK** területen válassza a **Hálózatkezelés** elemet.  Válassza az **alkalmazás biztonsági csoportok** lehetőséget, majd **konfigurálja az alkalmazás** biztonsági csoportjait, és válassza ki azokat az alkalmazás-biztonsági csoportokat, amelyekhez hozzá szeretné adni a hálózati adaptert, vagy törölje azokat a biztonsági csoportokat, amelyekről el szeretné távolítani a hálózati adaptert, majd válassza a **Mentés** lehetőséget. Ugyanahhoz az alkalmazás-biztonsági csoporthoz csak az azonos virtuális hálózatban található hálózati adapterek adhatók hozzá. Az alkalmazás biztonsági csoportjának ugyanabban a helyen kell lennie, mint a hálózati adapternek.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az Network NIC Update](/cli/azure/network/nic)|
|PowerShell|[Set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)|

## <a name="associate-or-dissociate-a-network-security-group"></a>Hálózati biztonsági csoport társítása vagy leválasztása

1. A portál felső részén található Keresés mezőben adja meg a *hálózati adapterek* kifejezést a keresőmezőbe. Ha a **hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki azt a hálózati adaptert a listában, amelyhez hálózati biztonsági csoportot kíván rendelni, vagy válasszon ki egy hálózati biztonsági csoportot a alkalmazásból.
3. A **Beállítások** területen válassza a **hálózati biztonsági csoport** lehetőséget.
4. Válassza a **Szerkesztés** elemet.
5. Válassza a **hálózati biztonsági csoport** lehetőséget, majd válassza ki azt a hálózati biztonsági csoportot, amelyet a hálózati adapterhez szeretne rendelni, vagy válassza a **nincs** lehetőséget a hálózati biztonsági csoport leválasztásához.
6. Kattintson a **Mentés** gombra.

**Parancsok**

- Azure CLI: [az Network NIC Update](/cli/azure/network/nic#az-network-nic-update)
- PowerShell: [set-AzNetworkInterface](/powershell/module/az.network/set-aznetworkinterface)

## <a name="delete-a-network-interface"></a>Hálózati adapter törlése

A hálózati adaptereket törölheti, ha az nincs virtuális géphez csatlakoztatva. Ha egy hálózati adapter egy virtuális géphez van csatlakoztatva, először a leállított (fel nem foglalt) állapotba kell helyeznie a virtuális gépet, majd le kell választania a hálózati adaptert a virtuális gépről. Ha hálózati adaptert szeretne leválasztani egy virtuális gépről, hajtsa végre a [hálózati adapter leválasztása virtuális gépről](virtual-network-network-interface-vm.md#remove-a-network-interface-from-a-vm)című témakör lépéseit. Nem lehet leválasztani egy hálózati adaptert egy virtuális gépről, ha az egyetlen hálózati adapter, amely a virtuális géphez van csatlakoztatva. A virtuális gépnek mindig rendelkeznie kell legalább egy hálózati adapterrel. A virtuális gép törlése leválasztja a hozzá csatlakoztatott összes hálózati adaptert, de nem törli a hálózati adaptereket.

1. A Azure Portal tetején található szöveges *keresési erőforrásokat* tartalmazó mezőbe írja be a *hálózati adapterek* kifejezést. Ha a **hálózati adapterek** megjelennek a keresési eredmények között, válassza ki.
2. Válassza ki a törölni kívánt listában a hálózati adaptert.
3. Az **Áttekintés** területen válassza a **Törlés** lehetőséget.
4. A hálózati adapter törlésének megerősítéséhez válassza az **Igen** lehetőséget.

Ha töröl egy hálózati adaptert, a hozzá rendelt MAC-és IP-címek jelennek meg.

**Parancsok**

|Eszköz|Parancs|
|---|---|
|parancssori felület|[az Network NIC delete](/cli/azure/network/nic)|
|PowerShell|[Remove-AzNetworkInterface](/powershell/module/az.network/remove-aznetworkinterface)|

## <a name="resolve-connectivity-issues"></a>Kapcsolódási problémák megoldása

Ha nem tud kommunikálni a virtuális gépről vagy a hálózatról, a hálózati biztonsági csoport biztonsági szabályait vagy útvonalait a hálózati adapterek esetében nem lehet végrehajtani, a probléma okozhatja a problémát. A probléma megoldásához a következő lehetőségek állnak rendelkezésre:

### <a name="view-effective-security-rules"></a>Érvényes biztonsági szabályok megtekintése

A virtuális géphez csatolt egyes hálózati adapterek érvényes biztonsági szabályai a hálózati biztonsági csoportban létrehozott szabályok és az [alapértelmezett biztonsági szabályok](./network-security-groups-overview.md#default-security-rules)kombinációi. A hálózati adapterek érvényes biztonsági szabályainak megismerése segíthet megállapítani, hogy miért nem tud kommunikálni a virtuális gépekkel vagy azokból. Megtekintheti a futó virtuális géphez csatlakoztatott bármely hálózati adapter érvényes szabályait.

1. A portál felső részén található keresőmezőbe írja be annak a virtuális gépnek a nevét, amelynek a hatályos biztonsági szabályait meg szeretné tekinteni. Ha nem ismeri a virtuális gép nevét, a keresőmezőbe írja be a *virtuális gépek* kifejezést. Ha a **virtuális gépek** megjelennek a keresési eredmények között, jelölje ki, majd válasszon ki egy virtuális gépet a listából.
2. Válassza a **hálózatkezelés** lehetőséget a **Beállítások** területen.
3. Válassza ki a hálózati adapter nevét.
4. A **támogatás + hibaelhárítás** területen válassza a **hatályos biztonsági szabályok** elemet.
5. Tekintse át az érvényes biztonsági szabályok listáját annak megállapításához, hogy a megfelelő szabályok léteznek-e a szükséges bejövő és kimenő kommunikációhoz. További információ arról, hogy mit lát a [hálózati biztonsági csoport áttekintése](./network-security-groups-overview.md)című listában.

Az Azure Network Watcher IP-forgalom ellenőrzése funkciójának segítségével meghatározhatja, hogy a biztonsági szabályok megakadályozzák-e a virtuális gépek és a végpontok közötti kommunikációt. További információt az [IP-folyamat ellenőrzése](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json)című témakörben talál.

**Parancsok**

- Azure CLI: [az Network NIC List-effektív-NSG](/cli/azure/network/nic#az-network-nic-list-effective-nsg)
- PowerShell: [Get-AzEffectiveNetworkSecurityGroup](/powershell/module/az.network/get-azeffectivenetworksecuritygroup)

### <a name="view-effective-routes"></a>Érvényes útvonalak megtekintése

A virtuális géphez csatlakoztatott hálózati adapterek érvényes útvonalai az alapértelmezett útvonalak, a létrehozott útvonalak és a helyi hálózatokról a BGP-n keresztül propagált útvonalak egy Azure-beli virtuális hálózati átjárón keresztül. A hálózati adapterek hatályos útvonalának megismerése segíthet megállapítani, hogy miért nem tud kommunikálni a virtuális gépekkel vagy azokból. Megtekintheti a futó virtuális géphez csatlakoztatott bármely hálózati adapter érvényes útvonalait.

1. A portál felső részén található keresőmezőbe írja be annak a virtuális gépnek a nevét, amelynek a hatályos biztonsági szabályait meg szeretné tekinteni. Ha nem ismeri a virtuális gép nevét, a keresőmezőbe írja be a *virtuális gépek* kifejezést. Ha a **virtuális gépek** megjelennek a keresési eredmények között, jelölje ki, majd válasszon ki egy virtuális gépet a listából.
2. Válassza a **hálózatkezelés** lehetőséget a **Beállítások** területen.
3. Válassza ki a hálózati adapter nevét.
4. Válassza ki a **hatályos útvonalakat** a **támogatás + hibaelhárítás** területen.
5. Tekintse át az érvényes útvonalak listáját annak megállapításához, hogy a megfelelő útvonalak léteznek-e a szükséges bejövő és kimenő kommunikációhoz. További információ arról, hogy mit láthat a listában az [Útválasztás áttekintése](virtual-networks-udr-overview.md)oldalon.

Az Azure Network Watcher következő ugrás funkciója segíthet megállapítani, hogy az útvonalak akadályozzák-e a virtuális gépek és a végpontok közötti kommunikációt. További információért lásd a [következő ugrást](../network-watcher/diagnose-vm-network-routing-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

**Parancsok**

- Azure CLI: [az Network NIC show-effektív-Route-Table](/cli/azure/network/nic#az-network-nic-show-effective-route-table)
- PowerShell: [Get-AzEffectiveRouteTable](/powershell/module/az.network/get-azeffectiveroutetable)

## <a name="permissions"></a>Engedélyek

A hálózati adapterekkel kapcsolatos feladatok elvégzéséhez a fiókját hozzá kell rendelni a [hálózati közreműködő](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) szerepkörhöz vagy egy [Egyéni](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szerepkörhöz, amely az alábbi táblázatban felsorolt megfelelő engedélyekhez van rendelve:

| Művelet                                                                     | Name                                                      |
| ---------                                                                  | -------------                                             |
| Microsoft. Network/networkInterfaces/READ                                   | Hálózati adapter lekérése                                     |
| Microsoft. Network/networkInterfaces/Write                                  | Hálózati adapter létrehozása vagy frissítése                        |
| Microsoft. Network/networkInterfaces/csatlakozás/művelet                            | Hálózati adapter csatlakoztatása egy virtuális géphez           |
| Microsoft. Network/networkInterfaces/delete                                 | Hálózati adapter törlése                                  |
| Microsoft. Network/networkInterfaces/joinViaPrivateIp/Action                | Erőforrás csatlakoztatása hálózati adapterhez Servi keresztül...     |
| Microsoft. Network/networkInterfaces/effectiveRouteTable/Action             | Hálózati adapter hatékony útválasztási táblázatának beolvasása               |
| Microsoft. Network/networkInterfaces/effectiveNetworkSecurityGroups/Action  | Hálózati adapter érvényes biztonsági csoportjainak beolvasása           |
| Microsoft. Network/networkInterfaces/loadBalancers/READ                     | Hálózati adapter terheléselosztásának beolvasása                      |
| Microsoft. Network/networkInterfaces/serviceAssociations/READ               | Szolgáltatás társításának beolvasása                                   |
| Microsoft. Network/networkInterfaces/serviceAssociations/Write              | Szolgáltatási társítás létrehozása vagy frissítése                    |
| Microsoft. Network/networkInterfaces/serviceAssociations/delete             | Szolgáltatási társítás törlése                                |
| Microsoft. Network/networkInterfaces/serviceAssociations/validate/Action    | Szolgáltatási társítás ellenőrzése                              |
| Microsoft. Network/networkInterfaces/ipconfigurations/READ                  | Hálózati adapter IP-konfigurációjának beolvasása                    |

## <a name="next-steps"></a>Következő lépések

- Több hálózati adapterrel rendelkező virtuális gép létrehozása az [Azure CLI](../virtual-machines/linux/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy a [PowerShell](../virtual-machines/windows/multiple-nics.md?toc=%2fazure%2fvirtual-network%2ftoc.json) használatával
- Egyetlen hálózati adaptert tartalmazó virtuális gép létrehozása több IPv4-címmel az [Azure CLI](virtual-network-multiple-ip-addresses-cli.md) vagy a [PowerShell](virtual-network-multiple-ip-addresses-powershell.md) használatával
- Egyetlen hálózati adaptert tartalmazó virtuális gép létrehozása magánhálózati IPv6-cím használatával (Azure Load Balancer mögött) az [Azure CLI](../load-balancer/load-balancer-ipv6-internet-cli.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a [PowerShell](../load-balancer/load-balancer-ipv6-internet-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json)vagy a [Azure Resource Manager sablonnal](../load-balancer/load-balancer-ipv6-internet-template.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Hálózati adapter létrehozása a [PowerShell](powershell-samples.md) vagy az [Azure CLI](cli-samples.md) minta parancsfájljaival vagy az Azure [Resource Manager-sablon](template-samples.md) használatával
- [Azure Policy-definíciók](./policy-reference.md) létrehozása és társítása virtuális hálózatokhoz