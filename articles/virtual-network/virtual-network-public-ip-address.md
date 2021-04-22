---
title: Nyilvános IP-címek kezelése | Microsoft Docs
titleSuffix: Azure Virtual Network
description: Nyilvános IP-címek kezelése.  Azt is megtudhatja, hogyan használhatók a nyilvános IP-címek saját konfigurálható beállításokkal.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
editor: ''
tags: azure-resource-manager
ms.assetid: bb71abaf-b2d9-4147-b607-38067a10caf6
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/06/2019
ms.author: kumud
ms.openlocfilehash: 3277d5836d85f1071b7aa169cc83896934a2f63d
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2021
ms.locfileid: "107872442"
---
# <a name="manage-public-ip-addresses"></a>Nyilvános IP-címek kezelése

Megismerhet egy nyilvános IP-címet, és megtudhatja, hogyan hozhat létre, módosíthat és törölhet egyet. A nyilvános IP-cím egy saját konfigurálható beállításokkal konfigurálható erőforrás. Ha nyilvános IP-címet rendel egy nyilvános IP-címeket támogató Azure-erőforráshoz, az lehetővé teszi a következőt:
- Bejövő kommunikáció az internetről az erőforrás felé, például Azure Virtual Machines (VM), Azure Application Gateways, Azure Load Balancer, Azure VPN Gateway stb. Bizonyos erőforrásokkal, például virtuális gépekkel akkor is kommunikálhat az internetről, ha egy virtuális géphez nincs hozzárendelve nyilvános IP-cím, amennyiben a virtuális gép egy terheléselosztási háttérkészlet része, és a terheléselosztáshoz egy nyilvános IP-cím van hozzárendelve. Annak megállapításához, hogy egy adott Azure-szolgáltatás erőforrása hozzárendelhető-e nyilvános IP-címhez, vagy egy másik Azure-erőforrás nyilvános IP-címével kommunikálható-e, tekintse meg a szolgáltatás dokumentációját.
- Kimenő internetkapcsolat kiszámítható IP-cím használatával. Egy virtuális gép például anélkül kommunikálhat kimenő internetkapcsolattal, hogy hozzá lenne rendelve nyilvános IP-cím, de a címe az Azure által alapértelmezés szerint kiszámíthatatlan nyilvános címekre lefordított hálózati cím. Ha nyilvános IP-címet rendel egy erőforráshoz, akkor tudni tudja, hogy melyik IP-címet használja a kimenő kapcsolat. Bár kiszámítható, a cím a választott hozzárendelési módszertől függően változhat. További információ: [Nyilvános IP-cím létrehozása.](#create-a-public-ip-address) Az Azure-erőforrások kimenő kapcsolataival kapcsolatos további információkért lásd: [A kimenő kapcsolatok megismerése.](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="before-you-begin"></a>Előkészületek

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Mielőtt végrehajtja a cikk bármely szakaszának lépéseit, a következő feladatokat kell elvégeznie:

- Ha még nem rendelkezik Azure-fiókkal, regisztráljon egy ingyenes [próbafiókra.](https://azure.microsoft.com/free)
- Ha a portált használja, nyissa meg a https://portal.azure.com webhelyet, és jelentkezzen be Azure-fiókjával.
- Ha PowerShell-parancsokat használ a cikkben található feladatok végrehajtásához, futtassa a parancsokat a [Azure Cloud Shell](https://shell.azure.com/powershell)vagy a PowerShell futtatásával a számítógépről. Az Azure Cloud Shell egy olyan ingyenes interaktív kezelőfelület, amelyet a jelen cikkben található lépések futtatására használhat. A fiókjával való használat érdekében a gyakran használt Azure-eszközök már előre telepítve és konfigurálva vannak rajta. Ehhez az oktatóanyaghoz a Azure PowerShell 1.0.0-s vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.
- Ha Azure parancssori felületi (CLI)-parancsokat használ a cikkben található feladatok [](https://shell.azure.com/bash)végrehajtásához, futtassa a parancsokat a Azure Cloud Shell-ban, vagy futtassa a parancssori felületet a számítógépről. Ehhez az oktatóanyaghoz az Azure CLI 2.0.31-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli). Ha az Azure CLI-t helyileg futtatja, a futtatásával kapcsolatot kell `az login` létesítenie az Azure-ral.

A fióknak, amelybe bejelentkezik, vagy amelyről [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) csatlakozik az Azure-hoz, hozzá kell [rendelnie](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a hálózati közreműködői szerepkörhöz vagy egy egyéni szerepkörhöz, amely az Engedélyek listában felsorolt megfelelő műveleteket [rendeli hozzá.](#permissions)

A nyilvános IP-címek névleges díjat számolnak fel. A díjszabás megtekintéséhez olvassa el az [IP-cím díjszabását.](https://azure.microsoft.com/pricing/details/ip-addresses)

## <a name="create-a-public-ip-address"></a>Hozzon létre egy nyilvános IP-címet

A nyilvános IP-címek Portál, PowerShell vagy parancssori felület használatával való létrehozására vonatkozó utasításokért tekintse meg a következő oldalakat:

 * [Nyilvános IP-címek létrehozása – Portal](./create-public-ip-portal.md?tabs=option-create-public-ip-standard-zones)
 * [Nyilvános IP-címek létrehozása – PowerShell](./create-public-ip-powershell.md?tabs=option-create-public-ip-standard-zones)
 * [Nyilvános IP-címek létrehozása – Azure CLI](./create-public-ip-cli.md?tabs=option-create-public-ip-standard-zones)

>[!NOTE]
>Bár a portál lehetővé teszi két nyilvános IP-cím-erőforrás (egy IPv4 és egy IPv6) létrehozására vonatkozó lehetőséget, a PowerShell és a PARANCSSORi felület parancsai létrehoznak egy erőforrást az egyik IP-verzióhoz vagy a másikhoz. Ha két nyilvános IP-cím-erőforrást szeretne, mindegyik IP-verzióhoz egyet, kétszer kell futtatnia a parancsot, és különböző neveket és IP-verziókat kell megadnia a nyilvános IP-cím-erőforrásokhoz.

A nyilvános IP-címek létrehozáskor megadott attribútumainak további részleteiért tekintse meg az alábbi táblázatot.

   |Beállítás|Kötelező?|Részletek|
   |---|---|---|
   |IP-verzió|Yes| Válassza az IPv4, az IPv6 vagy a Mindkettő lehetőséget. A Mindkettő lehetőség kiválasztásakor 2 nyilvános IP-cím lesz létrehozva – 1 IPv4-cím és 1 IPv6-cím. További információ az [Azure-beli virtuális gépeken használt IPv6-címekről.](../virtual-network/ipv6-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)|
   |Termékváltozat|Yes|A termékváltozatok bevezetése előtt létrehozott összes nyilvános IP-cím alapszintű **termékváltozat** nyilvános IP-cím. A termékváltozat a nyilvános IP-cím létrehozása után nem változtatható meg. Az önálló virtuális gépek, a rendelkezésre állási készleten belüli virtuális gépek vagy a virtuálisgép-méretezési készletek alapszintű vagy standard termékhasználatot használhatnak. A rendelkezésre állási készletekben, méretezési készletekben vagy önálló virtuális gépeken található virtuális gépek közötti termékkódok vegyesen nem használhatók. **Alapszintű** Termékváltozat: Ha olyan régióban hoz létre nyilvános IP-címet,  amely támogatja a rendelkezésre állási zónákat, a Rendelkezésre állási zóna alapértelmezett beállítása *Nincs.* Az alapszintű nyilvános IP-k nem támogatják a rendelkezésre állási zónákat. **Standard** Termékváltozat: Standard termékváltozatú nyilvános IP-cím társítható egy virtuális géphez vagy egy terheléselosztási előoldalhoz. Ha olyan régióban hoz létre nyilvános *IP-címet,* amely  támogatja a rendelkezésre állási zónákat, a Rendelkezésre állási zóna beállítás alapértelmezés szerint Zónaredundáns. A rendelkezésre állási zónákkal kapcsolatos további információkért lásd a rendelkezésre **állási zóna beállítását.** A standard termékváltozatra akkor van szükség, ha a címet egy standard terheléselosztáshoz társítja. További információ a standard terheléselosztásról: [Azure Load Balancer standard termékváltozata.](../load-balancer/load-balancer-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ha egy standard termékváltozatú nyilvános IP-címet hozzárendel egy virtuális gép hálózati adapteréhez, kifejezetten engedélyeznie kell a kívánt forgalmat egy [hálózati biztonsági csoporttal](./network-security-groups-overview.md#network-security-groups). Az erőforrással történő kommunikáció meghiúsul, amíg nem hoz létre és rendel hozzá egy hálózati biztonsági csoportot, és kifejezetten nem engedélyezi a kívánt forgalmat.|
   |Szint|Yes|Azt jelzi, hogy az IP-cím egy régióhoz van társítva (**Regionális**), vagy "anycast" több régióból (**Globális**). *Vegye figyelembe, hogy a "globális szintű" IP-cím a Standard IP-címek* előzetes verziója, és jelenleg csak a régiók közötti ip-címekhez Load Balancer.|
   |Name|Yes|A névnek egyedinek kell lennie a kiválasztott erőforráscsoporton belül.|
   |IP-cím hozzárendelése|Yes|**Dinamikus:** A dinamikus címeket csak akkor rendeli hozzá a rendszer, ha egy nyilvános IP-cím hozzá van rendelve egy Azure-erőforráshoz, és az erőforrás első alkalommal elindul. A dinamikus címek változhatnak, ha erőforráshoz, például virtuális géphez vannak rendelve, és a virtuális gép le van állítva (felszabadítva), majd újraindítják. A cím ugyanaz marad, ha egy virtuális gép újraindul vagy leáll (de nem szabadul fel). Dinamikus címek akkor szabadulnak fel, ha egy nyilvános IP-cím erőforrás nincs társítva egy olyan erőforrással, amelyhez társítva van. **Statikus:** A statikus címek a nyilvános IP-címek létrehozásakor vannak hozzárendelve. A statikus címek csak akkor szabadulnak fel, ha törölnek egy nyilvános IP-cím-erőforrást. Ha a cím nincs erőforráshoz társítva, a cím létrehozása után módosíthatja a hozzárendelési módszert. Ha a cím egy erőforráshoz van társítva, előfordulhat, hogy nem tudja módosítani a hozzárendelési módszert. Ha az *IP-verziónál az IPv6* lehetőséget **választja,** a hozzárendelési módszernek Dynamic (Dinamikus) értéknek kell lennie *az* alapszintű termékváltozathoz.  A standard termékváltozatú címek *statikusak* az IPv4 és az IPv6 esetén is. |
   |Üresjárati időkorlát (perc)|No|Hány percig lehet nyitva tartani egy TCP- vagy HTTP-kapcsolatot anélkül, hogy az ügyfeleknek életben tartás üzeneteket kellene küldeniük. Ha az IP-verzió beállításnál az IPv6 **lehetőséget** választja, ez az érték nem módosítható. |
   |DNS-névcímke|No|Egyedinek kell lennie abban az Azure-helyen, ahol a nevet létrehozza (az összes előfizetésben és az összes ügyfélben). Az Azure automatikusan regisztrálja a nevet és az IP-címet a DNS-ben, így Ön egy nevű erőforráshoz csatlakozhat. Az Azure hozzáfűz egy alapértelmezett alhálózatot( *location.cloudapp.azure.com* (ahol a hely a kiválasztott hely) a névhez a teljes DNS-név létrehozásához. Ha mindkét címverziót létrehozza, ugyanaz a DNS-név lesz hozzárendelve az IPv4- és az IPv6-címekhez is. Az Azure alapértelmezett DNS-e az IPv4 A és az IPv6 AAAA névrekordot is tartalmazza, és mindkét rekordra válaszol a DNS-név megkeresésekor. Az ügyfél választja ki, hogy melyik címmel (IPv4 vagy IPv6) kommunikáljon. Ahelyett vagy amellett, hogy az alapértelmezett utótaggal használná a DNS-név címkét, az Azure DNS szolgáltatással konfigurálhatja a DNS-nevet egy egyéni utótaggal, amelynek feloldása a nyilvános IP-címmé történik. További információt [az Azure DNS szolgáltatásnak nyilvános Azure IP-címmel történő használatával](../dns/dns-custom-domain.md?toc=%2fazure%2fvirtual-network%2ftoc.json#public-ip-address) foglalkozó témakörben tekinthet meg.|
   |Név (Csak akkor látható, ha mindkettő IP-verzióját **választja)**|Igen, ha a Mindkettő IP-verzió lehetőséget **választja**|A névnek eltérőnek kell lennie a listában az első **névhez** beírott névtől. Ha IPv4- és IPv6-címet is létrehoz, a portál két különálló nyilvános IP-címerőforrást hoz létre, amelyek mindegyik IP-címverziója hozzá van rendelve.|
   |IP-cím hozzárendelése (Csak akkor látható, ha a Mindkettő IP-verziója lehetőséget **választja)**|Igen, ha a Mindkettő IP-verzió lehetőséget **választja**|Ugyanazok a korlátozások, mint a fenti IP-cím-hozzárendelések|
   |Előfizetés|Yes|Ugyanabban az előfizetésben kell lennie, [mint](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) annak az erőforrásnak, amelyhez a nyilvános IP-címeket társítja.|
   |Erőforráscsoport|Yes|Létezhet ugyanabban vagy eltérő [erőforráscsoportban,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group) mint az az erőforrás, amelyhez a nyilvános IP-címeket társítja.|
   |Hely|Igen|A (más [](https://azure.microsoft.com/regions)néven régió) helyen kell lennie, mint annak az erőforrásnak, amelyhez a nyilvános IP-címeket társítja.|
   |A rendelkezésre állási zóna| No | Ez a beállítás csak akkor jelenik meg, ha egy támogatott helyet választ ki. A támogatott helyek listáját a rendelkezésre [állási zónák áttekintésében olvashatja el.](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Ha az Alapszintű **termékváltozatot** választotta, a *Nincs* automatikusan ki lesz választva. Ha egy adott zónát szeretne garantálni, választhat egy adott zónát. Egyik lehetőség sem zónaredundáns. Ha a **Standard** termékváltozatot választotta: A zónaredundáns automatikusan ki lesz választva, és az adatútvonal rugalmasan ellenáll a zónahibáknak. Ha egy adott zónát szeretne garantálni, amely nem rugalmas a zóna meghibásodása esetén, kiválaszthat egy adott zónát.

## <a name="view-modify-settings-for-or-delete-a-public-ip-address"></a>Nyilvános IP-cím megtekintése, beállításainak módosítása vagy törlése

   - **Nézet/Lista:** Nyilvános IP-cím beállításainak áttekintése, beleértve a termékváltozatot, a címet, a vonatkozó társításokat (pl. virtuális gép hálózati adaptere, Load Balancer előtere).
   - **Módosítás:** A beállítások módosításához használja a nyilvános [IP-cím](#create-a-public-ip-address)létrehozása 4. lépésében található információkat, például a tétlen időtúllépést, a DNS-névcímkét vagy a hozzárendelési módszert.  (A nyilvános IP-cím termékváltozatának Alapszintűről Standardra való frissítésének teljes folyamatát lásd: Azure nyilvános [IP-címek frissítése.)](./virtual-network-public-ip-address-upgrade.md)
   >[!WARNING]
   >Ha egy nyilvános IP-cím hozzárendelését statikusról dinamikusra szeretné módosítani, először el kell különítania a címet a megfelelő IP-konfigurációktól (lásd a **Törlés** szakaszt).  Azt is vegye figyelembe, hogy ha a hozzárendelési módszert statikusról dinamikusra módosítja, elveszíti a nyilvános IP-címhez rendelt IP-címet. Míg az Azure nyilvános DNS-kiszolgálói statikus vagy dinamikus címek és bármely DNS-névcímke között fenntartják a leképezést (ha definiált egyet), a dinamikus IP-címek megváltoznak, ha a virtuális gép a leállított (felszabadított) állapotban van. A cím módosításának megakadályozása érdekében rendeljen hozzá egy statikus IP-címet.
   
|Művelet|Azure Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|Nézet | Nyilvános **IP-cím** Áttekintés szakaszában |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) nyilvános IP-címobjektum lekéréséhez és beállításainak megtekintéséhez| [az network public-ip show](/cli/azure/network/public-ip#az_network_public_ip_show) to show settings|
|Lista | A Nyilvános **IP-címek kategóriában** |[Get-AzPublicIpAddress](/powershell/module/az.network/get-azpublicipaddress) egy vagy több nyilvános IP-címobjektum lekéréséhez és beállításainak megtekintéséhez|[az network public-ip list](/cli/azure/network/public-ip#az_network_public_ip_list) to list public IP addresses|
|Módosítás | A nem társított IP-címek esetében  válassza a Konfiguráció lehetőséget az üresjárati időtúllépés, a DNS-névcímke vagy az Alapszintű IP-cím statikusról dinamikusra való módosításához  |[Set-AzPublicIpAddress a](/powershell/module/az.network/set-azpublicipaddress) beállítások frissítéséhez |[az network public-ip update](/cli/azure/network/public-ip#az_network_public_ip_update) to update |

   - **Törlés:** A nyilvános IP-címek törlése megköveteli, hogy a nyilvános IP-objektum egyetlen IP-konfigurációhoz vagy virtuális hálózati adapterhez se legyen társítva. További részletekért tekintse meg az alábbi táblázatot.

|Erőforrás|Azure Portal|Azure PowerShell|Azure CLI|
|---|---|---|---|
|[Virtuális gép](./remove-public-ip-address-vm.md)|Válassza **a Társítás társításának törlése** lehetőséget az IP-címnek a hálózati adapter konfigurációjában való társításának törléséhez, majd válassza a Törlés **lehetőséget.**|[Set-AzPublicIpAddress](/powershell/module/az.network/set-azpublicipaddress) az IP-cím és a hálózati adapter konfigurációjának társításának visszavonásához; [Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) törléshez|[az network public-ip update --remove az](/cli/azure/network/public-ip#az_network_public_ip_update) IP-cím hálózati adapter-konfigurációból való társításának eltávolításához; [az network public-ip delete](/cli/azure/network/public-ip#az_network_public_ip_delete) to delete |
|Load Balancer előtere | Lépjen egy nem használt nyilvános  IP-címre, és válassza a Társítás lehetőséget, és válassza ki a megfelelő előtere IP-konfigurációval Load Balancer-t a lecserélni kívánt ip-címhez (a régi IP-cím a virtuális gépével azonos módszerrel törölhető)  | [A Set-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/set-azloadbalancerfrontendipconfig) parancs új előtérben található IP-konfigurációt társít a nyilvános Load Balancer; [Törölje a Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) parancsot; A a [Remove-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/remove-azloadbalancerfrontendipconfig) használatával is eltávolíthatja az előtérben található IP-konfigurációt, ha több is van |[az network lb frontend-ip update](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_update) to associate new Frontend IP config with Public Load Balancer; [Törölje a Remove-AzPublicIpAddress](/powershell/module/az.network/remove-azpublicipaddress) parancsot; az [az network lb frontend-ip delete](/cli/azure/network/lb/frontend-ip#az_network_lb_frontend_ip_delete) parancs használatával is eltávolíthatja az előtere IP-konfigurációt, ha több van|
|Firewall|N/A| [Felszabadítás() a](../firewall/firewall-faq.yml#how-can-i-stop-and-start-azure-firewall) tűzfal felszabadítása és az összes IP-konfiguráció eltávolítása | [az network firewall ip-config delete az](/cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_delete) IP eltávolításához (de először a PowerShellt kell használnia a felszabadításhoz)|

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

Ha nyilvános IP-címekkel használ virtuálisgép-méretezési készletet, nincsenek külön nyilvános IP-objektumok társítva az egyes virtuálisgép-példányokhoz. A példány IP-címeinek [](https://azure.microsoft.com/resources/templates/101-vmms-with-public-ip-prefix/)létrehozásához azonban használható egy nyilvános IP-előtagobjektum.

Egy virtuálisgép-méretezési készlet nyilvános IP-címeinek listához használhatja a PowerShellt ([Get-AzPublicIpAddress -VirtualMachineScaleSetName](/powershell/module/az.network/get-azpublicipaddress)) vagy a parancssori felületet[(az vmss list-instance-public-ips).](/cli/azure/vmss#az_vmss_list_instance_public_ips)

További információ: [Networking for Azure virtual machine scale sets (Hálózat az Azure-beli virtuálisgép-méretezési készletekhez).](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md#public-ipv4-per-virtual-machine)

## <a name="assign-a-public-ip-address"></a>Nyilvános IP-cím hozzárendelése

Megtudhatja, hogyan rendelhet hozzá nyilvános IP-címet a következő erőforrásokhoz:

- [Windows vagy](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) [Linux rendszerű](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) virtuális gép (létrehozáskor) vagy meglévő [virtuális gépre](virtual-network-network-interface-addresses.md#add-ip-addresses)
- [Nyilvános Load Balancer](../load-balancer/quickstart-load-balancer-standard-public-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Application Gateway](../application-gateway/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Hely–hely kapcsolat VPN Gateway](../vpn-gateway/tutorial-site-to-site-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- [Virtuálisgép-méretezési készlet](../virtual-machine-scale-sets/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

## <a name="permissions"></a>Engedélyek

A nyilvános IP-címeken való feladatok végrehajtásához [](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) [a](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) fiókját hozzá kell rendelni a hálózati közreműködői szerepkörhöz vagy egy egyéni szerepkörhöz, amely a következő táblázatban felsorolt megfelelő műveleteket rendeli hozzá:

| Művelet                                                             | Name                                                           |
| ---------                                                          | -------------                                                  |
| Microsoft.Network/publicIPAddresses/read                           | Nyilvános IP-cím olvasása                                          |
| Microsoft.Network/publicIPAddresses/write                          | Nyilvános IP-cím létrehozása vagy frissítése                           |
| Microsoft.Network/publicIPAddresses/delete                         | Nyilvános IP-cím törlése                                     |
| Microsoft.Network/publicIPAddresses/join/action                    | Nyilvános IP-cím társítása erőforráshoz                    |

## <a name="next-steps"></a>Következő lépések

- Nyilvános IP-cím létrehozása [PowerShell- vagy](powershell-samples.md) Azure CLI-mintaszkprogramokkal vagy Azure [Resource Manager sablonokkal](template-samples.md) [](cli-samples.md)
- Nyilvános [IP-címek Azure Policy és](./policy-reference.md) hozzárendelése
