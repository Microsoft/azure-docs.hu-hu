---
title: Virtuális hálózatok
description: Ismerje meg a hálózatkezelést, amely a Linux rendszerű virtuális gépek Azure-beli létrehozásához kapcsolódik.
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.workload: infrastructure-services
ms.topic: conceptual
ms.date: 07/17/2017
ms.author: cynthn
ms.openlocfilehash: 20cc67d5c6436d7c0f44071509e13af324a88eea
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/17/2021
ms.locfileid: "100578864"
---
# <a name="virtual-networks-and-virtual-machines-in-azure"></a>Virtuális hálózatok és virtuális gépek az Azure-ban 

Azure virtuális gép létrehozásakor létre kell hoznia egy [virtuális hálózatot](../virtual-network/virtual-networks-overview.md) (VNet), vagy egy meglévő VNetet kell használnia. Arról is döntenie kell, hogy a virtuális gépek milyen módon legyenek elérhetők a VNeten. Mindenképpen [készítsen tervet az erőforrások létrehozása előtt](../virtual-network/virtual-network-vnet-plan-design-arm.md), továbbá győződjön meg arról, hogy tisztában van a [hálózati erőforrások korlátaival](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).

Az alábbi ábrán a virtuális gépek web- és adatbázis-kiszolgálókként vannak ábrázolva. Az egyes virtuálisgép-csoportok a VNet különböző alhálózataihoz vannak rendelve.

![Azure virtuális hálózat](./media/virtual-machines-common-network-overview/vnetoverview.png)

A virtuális gép létrehozása előtt létrehozhat egy VNet, vagy létrehozhat egy virtuális gépet is. A virtuális géppel folytatott kommunikáció támogatásához az alábbi erőforrásokat hozza létre:

- Hálózati adapterek
- IP-címek
- Virtuális hálózat és alhálózatok

Ezen alapvető erőforrások mellett az alábbi választható erőforrások használatát is érdemes fontolóra venni:

- Network security groups (Hálózati biztonsági csoportok)
- Terheléselosztók 

## <a name="network-interfaces"></a>Hálózati adapterek

A [hálózati adapter (NIC)](../virtual-network/virtual-network-network-interface.md) a virtuális gép és a virtuális hálózat (VNet) közötti kapcsolat. Egy virtuális gépnek legalább egy hálózati adapterrel kell rendelkeznie, de a létrehozott virtuális gép méretétől függően több ilyennel is rendelkezhet. Ismerje meg, hogy hány hálózati adaptert támogat a virtuális gépek mérete. lásd: virtuálisgép- [méretek](sizes.md).

Létrehozható virtuális gép több NIC-vel, valamint lehetőség van NIC-k hozzáadására és eltávolítására a virtuális gép életciklusa során. Több NIC-vel lehetővé válik a virtuális gép különböző alhálózatokhoz való csatlakoztatása, illetve forgalom küldése vagy fogadása a legmegfelelőbb adapteren keresztül. Tetszőleges számú hálózati adapterrel rendelkező virtuális gépek lehetnek azonos rendelkezésreállási csoportban, legfeljebb a virtuális gép mérete által támogatott számban. 

A virtuális géphez csatlakoztatott hálózati adaptereknek a virtuális géppel megegyező helyen és előfizetésen belül kell lenniük. Az egyes hálózati adaptereket csatlakoztatni kell egy olyan virtuális hálózathoz, amely a hálózati adapterekkel megegyező Azure-helyen és -előfizetésen belül található. A létrehozás után módosíthatja, hogy a virtuális gép melyik alhálózathoz csatlakozzon, a virtuális hálózat módosítása azonban nem lehetséges. Minden virtuális géphez csatlakoztatott hálózati adapterhez egy MAC-cím van rendelve, amely a virtuális gép törléséig változatlan marad.

Ez a táblázat egy hálózati adapter létrehozásának lehetséges módszereit sorolja fel.

| Metódus | Leírás |
| ------ | ----------- |
| Azure Portal | Amikor virtuális gépet hoz létre az Azure Portalon, automatikusan létrejön egy hálózati adapter is (külön létrehozott hálózati adapter nem használható). A portál csak egy hálózati adapterrel hoz létre virtuális gépeket. Ha egynél több hálózati adapterrel rendelkező virtuális gépet kíván létrehozni, akkor más módszert kell alkalmaznia. |
| [Azure PowerShell](./windows/multiple-nics.md) | A [New-AzNetworkInterface](/powershell/module/az.network/new-aznetworkinterface) és a **-PublicIpAddressId** paraméter használatával adja meg a korábban létrehozott nyilvános IP-cím azonosítóját. |
| [Azure CLI](./linux/multiple-nics.md) | A korábban létrehozott nyilvános IP-cím azonosítójának megadásához használja az [az Network NIC Create](/cli/azure/network/nic) a **--Public-IP-cím** paramétert. |
| [Sablon](../virtual-network/template-samples.md) | Hálózati adapter sablon használatával történő üzembe helyezéséhez segítségképp használja a [nyilvános IP-címmel rendelkező virtuális hálózatban található hálózati adapter](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) sablonját. |

## <a name="ip-addresses"></a>IP-címek 

Az Azure-ban az alábbi [IP-cím](../virtual-network/public-ip-addresses.md)típusokat rendelheti egy hálózati adapterhez:

- **Nyilvános IP-címek** – Az internettel és a VNethez nem csatlakoztatott egyéb Azure-erőforrásokkal folytatott bejövő és kimenő (hálózati címfordítás (NAT) nélküli) kommunikációhoz használható. Nem kötelező nyilvános IP-címet rendelni egy hálózati adapterhez. A nyilvános IP-címekhez névleges díj tartozik, és az előfizetések maximális száma is felhasználható.
- **Magánhálózati IP-címek** – A VNeten és a helyszíni hálózaton belüli, illetve az internettel folytatott (hálózati címfordítást (NAT) használó) kommunikációhoz használható. Legalább egy magánhálózati IP-címet hozzá kell rendelni egy virtuális géphez. Az Azure-on belüli hálózati címfordítással kapcsolatos további információkért olvassa el a [az Azure kimenő kapcsolatait](../load-balancer/load-balancer-outbound-connections.md) ismertető cikket.

Nyilvános IP-címeket virtuális gépekhez vagy internetes elérésű terheléselosztókhoz rendelhet hozzá. Magánhálózati IP-címeket virtuális gépekhez vagy belső terheléselosztókhoz rendelhet hozzá. Az IP-címeket hálózati adapter használatával rendelheti hozzá a virtuális gépekhez.

Két módszer érhető el az IP-címek egy erőforráshoz történő lefoglalásához: a dinamikus vagy a statikus. Az alapértelmezett lefoglalási módszer a dinamikus, amelynek során az IP-cím nincs lefoglalva a létrehozása idején. Ehelyett az IP-címet akkor foglalja le a rendszer, amikor elindít egy leállított virtuális gépet, vagy létrehoz egy újat. Az IP-cím akkor szabadul fel, ha leállítja vagy törli a virtuális gépet. 

A lefoglalási módszert állíthatja statikusra is, hogy a virtuális géphez tartozó IP-címek változatlanok maradjanak. Ebben az esetben a rendszer azonnal kioszt egy IP-címet. Az IP-cím csak akkor szabadul fel, ha törli a virtuális gépet, vagy dinamikusra változtatja a lefoglalási módszert.
    
Ez a táblázat egy IP-cím létrehozásának lehetséges módszereit sorolja fel.

| Metódus | Leírás |
| ------ | ----------- |
| [Azure Portal](../virtual-network/virtual-network-deploy-static-pip-arm-portal.md) | Alapértelmezés szerint a nyilvános IP-címek dinamikusak, a hozzájuk rendelt cím pedig a virtuális gép leállításakor vagy törlésekor változhat. Ha biztosítani szeretné, hogy a virtuális gép ugyanazt a nyilvános IP-címet használja, hozzon létre egy statikus nyilvános IP-címet. Alapértelmezés szerint virtuális gép létrehozásakor a portál dinamikus magánhálózati IP-címet rendel egy hálózati adapterhez. Ezt az IP-címet a virtuális gép létrehozása után is módosíthatja statikusra.|
| [Azure PowerShell](../virtual-network/virtual-network-deploy-static-pip-arm-ps.md) | A [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) a **-AllocationMethod** paraméterrel dinamikus vagy statikusként használható. |
| [Azure CLI](../virtual-network/virtual-network-deploy-static-pip-arm-cli.md) | A Dynamic vagy Static beállításhoz használja az [az network public-ip create](/cli/azure/network/public-ip) parancsot az **--allocation-method** paraméterrel. |
| [Sablon](../virtual-network/template-samples.md) | Nyilvános IP-cím sablon használatával történő üzembe helyezéséhez segítségképp használja a [nyilvános IP-címmel rendelkező virtuális hálózatban található hálózati adapter](https://github.com/Azure/azure-quickstart-templates/tree/master/101-nic-publicip-dns-vnet) sablonját. |

A létrehozást követően a nyilvános IP-címet társíthatja egy virtuális géppel, ha hozzárendeli egy hálózati adapterhez.

## <a name="virtual-network-and-subnets"></a>Virtuális hálózat és alhálózatok

Az alhálózat egy IP-címtartományt jelent a VNeten belül. A VNetet a rendszerezés és a biztonság érdekében több alhálózatra lehet osztani. Egy virtuális gép minden hálózati adaptere egy VNet egyetlen alhálózatához van csatlakoztatva. Egy VNeten belül az (ugyanazon vagy különböző) alhálózatokhoz csatlakoztatott hálózati adapterek további konfigurálás nélkül is tudnak egymással kommunikálni.

Egy VNet beállításakor meg kell adni a topológiát, beleértve az elérhető címtereket és alhálózatokat is. Ha a VNetet más VNetekhez vagy helyszíni hálózatokhoz szeretné csatlakoztatni, akkor olyan címtartományokat kell választania, amelyek nincsenek átfedésben. Az IP-címek magánjellegűek, és nem érhetők el az internetről, ami csak a nem irányítható IP-címek, például a 10.0.0.0/8, a 172.16.0.0/12 vagy a 192.168.0.0/16 esetében igaz. Az Azure mostantól minden címtartományt a magánhálózati VNet IP-címtér részeként kezel, amely csak a VNeten belül, összekapcsolt VNetek között és a helyszíni helyről érhető el,. 

Ha olyan szervezetben dolgozik, amelyben valaki más felelős a belső hálózatokért, a címtér kiválasztása előtt egyeztessen az illetővel. Győződjön meg arról, hogy nincs átfedés, és tudassa velük a használni kívánt helyet, hogy ne próbálja ugyanazt az IP-címtartományt használni. 

Alapértelmezés szerint nincs biztonsági határ az alhálózatok között, hogy az ezekben lévő virtuális gépek kommunikálhassanak egymással. Beállíthat viszont hálózati biztonsági csoportokat (NSG), amelyek segítségével szabályozhatja az alhálózatokról és virtuális gépekről érkező, illetve az azokra irányuló forgalmat. 

Ez a táblázat egy VNet és alhálózatok létrehozásának lehetséges módszereit sorolja fel.    

| Metódus | Leírás |
| ------ | ----------- |
| [Azure Portal](../virtual-network/quick-create-portal.md) | Ha engedélyezi, hogy az Azure a virtuális gép létrehozáskor létrehozzon egy VNetet, akkor a név a VNetet tartalmazó erőforráscsoport nevéből és a **-vnet** elemből tevődik össze. A címtér a 10.0.0.0/24, a szükséges alhálózati név a **default**, az alhálózati címtartomány pedig a 10.0.0.0/24. |
| [Azure PowerShell](../virtual-network/quick-create-powershell.md) | A [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworkSubnetConfig) és a [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) használatával hozzon létre egy alhálózatot és egy VNet. Az [Add-AzVirtualNetworkSubnetConfig](/powershell/module/Az.Network/Add-AzVirtualNetworkSubnetConfig) használatával alhálózatot is hozzáadhat egy meglévő VNet. |
| [Azure CLI](../virtual-network/quick-create-cli.md) | Az alhálózat és a VNet egyidejűleg jön létre. Adjon egy **--subnet-name** paramétert az alhálózat nevével rendelkező [az network vnet create](/cli/azure/network/vnet) parancshoz. |
| Sablon | A VNet és alhálózatok létrehozásának legegyszerűbb módja egy meglévő sablon letöltése, például [Virtual Network két alhálózattal](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets), és az igényeinek megfelelően módosítható. |

## <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)

A [hálózati biztonsági csoport (NSG)](../virtual-network/virtual-network-vnet-plan-design-arm.md) tartalmazza a hozzáférés-vezérlési (ACL) szabályok listáját, amelyek megszabják, hogy milyen típusú hálózati forgalom juthat el az alhálózatokhoz, a hálózati adapterekhez vagy mindkettőhöz. Az NSG-ket alhálózatokhoz vagy alhálózathoz csatlakoztatott hálózati adapterekhez lehet hozzárendelni. Ha az NSG-t hozzárendelik egy alhálózathoz, az ACL-szabályok érvényesek lesznek az alhálózatban lévő összes virtuális gépre. Emellett egy adott hálózati adapterre irányuló forgalmat korlátozni is lehet azáltal, hogy egy NSG-t közvetlenül a hálózati adapterhez rendelnek.

Az NSG-k két szabálykészletet tartalmaznak: bejövőt és kimenőt. A szabály prioritásának az egyes készleten belül kell egyedinek lennie. Minden szabály rendelkezik protokolltulajdonságokkal, forrás- és célport-tartományokkal, címelőtagokkal, forgalomiránnyal, prioritással és hozzáférési típussal. 

Minden NSG tartalmaz egy alapértelmezett szabálykészletet. Az alapértelmezett szabályokat nem lehet törölni, de mivel a legalacsonyabb prioritást rendelték hozzájuk, a létrehozott szabályok felülbírálhatják azokat. 

Amikor hálózati adapterhez társít egy NSG-t, a rendszer az NSG hálózatelérési szabályait csak a hálózati adapterre alkalmazza. Ha egy több hálózati adapterrel rendelkező virtuális gépben a rendszer az NSG-t csak az egyik hálózati adapterre alkalmazza, az nem lesz hatással a többi hálózati adapter forgalmára. Különböző NSG-ket társíthat egy hálózati adapterhez (vagy virtuális géphez, az üzembe helyezési modelltől függően) és az alhálózathoz, amelyhez hozzá van kötve a hálózati adapter vagy a virtuális gép. A prioritást a forgalom iránya határozza meg.

A virtuális gépek és a VNet tervezésekor az NSG-re vonatkozóan is [készítsen tervet](../virtual-network/virtual-network-vnet-plan-design-arm.md).

Ez a táblázat egy hálózati biztonsági csoport létrehozásának lehetséges módszereit sorolja fel.

| Metódus | Leírás |
| ------ | ----------- |
| [Azure Portal](../virtual-network/tutorial-filter-network-traffic.md) | Amikor virtuális gépet hoz létre az Azure Portalon, automatikusan létrejön egy NSG is, amelyet a rendszer a portál által létrehozott hálózati adapterhez rendel. Az NSG neve a virtuális gép nevéből és az **-nsg** elemből tevődik össze. Az NSG egy 1000-es prioritású bejövő szabályt, egy RDP beállítású szolgáltatást, egy TCP beállítású protokollt, egy 3389 beállítású portot és egy Engedélyezés beállítású műveletet tartalmaz. Ha bármilyen egyéb bejövő forgalmat kíván engedélyezni a virtuális gépre, további szabályokat kell az NSG-hez adnia. |
| [Azure PowerShell](../virtual-network/tutorial-filter-network-traffic.md) | Használja a [New-AzNetworkSecurityRuleConfig](/powershell/module/az.network/new-aznetworksecurityruleconfig) , és adja meg a szükséges szabály adatait. A [New-AzNetworkSecurityGroup](/powershell/module/az.network/new-aznetworksecuritygroup) használatával hozza létre a NSG. A [set-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/set-azvirtualnetworksubnetconfig) használatával konfigurálja az alhálózat NSG. A [set-AzVirtualNetwork](/powershell/module/az.network/set-azvirtualnetwork) használatával adja hozzá a NSG a VNet. |
| [Azure CLI](../virtual-network/tutorial-filter-network-traffic-cli.md) | Az [az network nsg create](/cli/azure/network/nsg) parancs használatával hozhatja létre először az NSG-t. Az [az network nsg rule create](/cli/azure/network/nsg/rule) parancs használatával adhat szabályokat az NSG-hez. Az [az network vnet subnet update](/cli/azure/network/vnet/subnet) parancs használatával adhatja az NSG-t az alhálózathoz. |
| [Sablon](../virtual-network/template-samples.md) | Hálózati biztonsági csoport sablon használatával történő üzembe helyezéséhez segítségképp használja a [hálózati biztonsági csoport létrehozása](https://github.com/Azure/azure-quickstart-templates/tree/master/101-security-group-create) sablont. |

## <a name="load-balancers"></a>Terheléselosztók

Az [Azure Load Balancer](../load-balancer/load-balancer-overview.md) magas rendelkezésre állást és hálózati teljesítményt biztosít alkalmazásai számára. A Load Balancer konfigurálható a virtuális gépekre [beérkező internetes forgalom](../load-balancer/components.md#frontend-ip-configurations) vagy a [virtuális gépek közötti VNet-forgalom elosztására](../load-balancer/components.md#frontend-ip-configurations). A terheléselosztó a helyszíni számítógépek és virtuális gépek közötti forgalom elosztására is képes egy létesítmények közötti hálózatban, illetve továbbítani tudja a külső forgalmat egy adott virtuális gépre.

A terheléselosztó leképezi a terheléselosztó nyilvános IP-címe és portja, illetve a virtuális gép magánhálózati IP-címe és portja közötti bejövő és kimenő forgalmat.

Terheléselosztó létrehozásakor figyelembe kell vennie az alábbi konfigurációs elemeket is:

- **Előtér-IP-konfiguráció** – A terheléselosztó egy vagy több előtér-IP-címet tartalmazhat. Ezek az IP-címek szolgálnak a forgalom bemeneti pontjaként.
- **Háttérbeli címkészlet** – Ahhoz a hálózati adapterhez rendelt IP-címek, amelyen a terhelés eloszlik.
- **[Port továbbítása](../load-balancer/tutorial-load-balancer-port-forwarding-portal.md)** – meghatározza, hogy a bejövő forgalom hogyan áramlik át az ELŐTÉR-IP-címen, és a bejövő NAT-szabályokat használó háttérrendszer-IP-címhez legyen terjesztve.
- **Terheléselosztó szabályai** – Leképezi egy adott előtérbeli IP és port kombinációját háttérbeli IP-címek és portok kombinációjára. Egyetlen terheléselosztó több terheléselosztási szabállyal is rendelkezhet. Minden szabály egy virtuális gépekhez társított előtérbeli IP és port, illetve háttérbeli IP és port kombinációja.
- **[Mintavételezők](../load-balancer/load-balancer-custom-probe-overview.md)** – Figyelik a virtuális gépek állapotát. Ha egy mintavételező nem válaszol, a terheléselosztó nem küld új kapcsolatokat a nem megfelelő állapotú virtuális gépre. A meglévő kapcsolatok nem változnak, az újakat pedig a kifogástalan állapotú virtuális gépekre küldi.
- **[Kimenő szabályok](../load-balancer/load-balancer-outbound-connections.md#outboundrules)** – egy kimenő szabály konfigurálja a kimenő hálózati címfordítást (NAT) minden olyan virtuális gép vagy példány számára, amelyet a standard Load Balancer háttér-készlete azonosít a felületre.

Ez a táblázat egy internetkapcsolattal rendelkező terheléselosztó létrehozásának lehetséges módszereit sorolja fel.

| Metódus | Leírás |
| ------ | ----------- |
| Azure Portal |  [A virtuális gépekre az Azure Portal használatával lehet terheléselosztást alkalmazni az internetes forgalomra](../load-balancer/quickstart-load-balancer-standard-public-portal.md). |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-public-powershell.md) | A korábban létrehozott nyilvános IP-cím azonosítójának megadásához használja a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) és a **-PublicIpAddress** paramétert. A [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) használatával hozza létre a háttér-címkészlet konfigurációját. A [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) használatával hozza létre a létrehozott előtér-IP-konfigurációhoz társított bejövő NAT-szabályokat. A [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) használatával hozza létre a szükséges mintavételi teszteket. A Load Balancer konfigurációjának létrehozásához használja a [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) . A Load Balancer létrehozásához használja a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) .|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-public-cli.md) | Az első terheléselosztó konfigurációjának létrehozásához használja az [az network lb create](/cli/azure/network/lb) parancsot. A korábban létrehozott nyilvános IP-cím hozzáadásához használja az [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) parancsot. A háttércímkészlet konfigurációjának hozzáadásához használja az [az network lb address-pool create](/cli/azure/network/lb/address-pool) parancsot. NAT-szabályok hozzáadásához használja az [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) parancsot. A terheléselosztó szabályainak hozzáadásához használja az [az network lb rule create](/cli/azure/network/lb/rule) parancsot. A mintavételezők hozzáadásához használja az [az network lb probe create](/cli/azure/network/lb/probe) parancsot. |
| [Sablon](../load-balancer/quickstart-load-balancer-standard-public-template.md) | Használjon [3 virtuális gépet egy Load Balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/101-load-balancer-standard-create) útmutatóként a terheléselosztó sablon használatával történő üzembe helyezéséhez. |
    
Ez a táblázat egy belső terheléselosztó létrehozásának lehetséges módszereit sorolja fel.

| Metódus | Leírás |
| ------ | ----------- |
| Azure Portal | [A belső forgalom terhelését a Azure Portal terheléselosztási szolgáltatásával egyenlítheti](../load-balancer/quickstart-load-balancer-standard-internal-portal.md)ki. |
| [Azure PowerShell](../load-balancer/quickstart-load-balancer-standard-internal-powershell.md) | Ha privát IP-címet szeretne megadni a hálózati alhálózatban, használja a [New-AzLoadBalancerFrontendIpConfig](/powershell/module/az.network/new-azloadbalancerfrontendipconfig) és a **-privateipaddress tulajdonságot** paramétert. A [New-AzLoadBalancerBackendAddressPoolConfig](/powershell/module/az.network/new-azloadbalancerbackendaddresspoolconfig) használatával hozza létre a háttér-címkészlet konfigurációját. A [New-AzLoadBalancerInboundNatRuleConfig](/powershell/module/az.network/new-azloadbalancerinboundnatruleconfig) használatával hozza létre a létrehozott előtér-IP-konfigurációhoz társított bejövő NAT-szabályokat. A [New-AzLoadBalancerProbeConfig](/powershell/module/az.network/new-azloadbalancerprobeconfig) használatával hozza létre a szükséges mintavételi teszteket. A Load Balancer konfigurációjának létrehozásához használja a [New-AzLoadBalancerRuleConfig](/powershell/module/az.network/new-azloadbalancerruleconfig) . A Load Balancer létrehozásához használja a [New-AzLoadBalancer](/powershell/module/az.network/new-azloadbalancer) .|
| [Azure CLI](../load-balancer/quickstart-load-balancer-standard-internal-cli.md) | Az első terheléselosztó konfigurációjának létrehozásához használja az [az network lb create](/cli/azure/network/lb) parancsot. A magánhálózati IP-cím meghatározásához használja az [az network lb frontend-ip create](/cli/azure/network/lb/frontend-ip) parancsot a **--private-ip-address** paraméterrel. A háttércímkészlet konfigurációjának hozzáadásához használja az [az network lb address-pool create](/cli/azure/network/lb/address-pool) parancsot. NAT-szabályok hozzáadásához használja az [az network lb inbound-nat-rule create](/cli/azure/network/lb/inbound-nat-rule) parancsot. A terheléselosztó szabályainak hozzáadásához használja az [az network lb rule create](/cli/azure/network/lb/rule) parancsot. A mintavételezők hozzáadásához használja az [az network lb probe create](/cli/azure/network/lb/probe) parancsot.|
| [Sablon](../load-balancer/quickstart-load-balancer-standard-internal-template.md) | Használjon [2 virtuális gépet egy Load Balancer](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-internal-load-balancer) útmutatóként a terheléselosztó sablon használatával történő üzembe helyezéséhez. |

### <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok

A Load balancerrel és a virtuálisgép-méretezési csoportokkal kapcsolatos további információkért lásd: [hálózatkezelés az Azure-beli virtuálisgép-méretezési csoportokhoz](../virtual-machine-scale-sets/virtual-machine-scale-sets-networking.md).

## <a name="vms"></a>Virtuális gépek

A virtuális gépeket létre lehet hozni ugyanabban a VNetben, és magánhálózati IP-címek használatával képesek csatlakozni egymáshoz. Akkor is képesek csatlakozni, ha külön alhálózatokon vannak – anélkül, hogy átjárót kellene konfigurálni vagy nyilvános IP-címeket kellene használni. Ha virtuális gépeket szeretne helyezni egy VNetbe, először hozza létre a VNetet, majd a virtuális gép létrehozásakor rendelje azt a VNethez és az alhálózathoz. A virtuális gépek az üzembe helyezés vagy az indítás során kérik le a hálózati beállításaikat.  

A virtuális gépek az üzembe helyezésükkor kapnak IP-címet. Ha több virtuális gépet helyez üzembe egy VNetben vagy alhálózatban, akkor a rendszerindításkor kapnak IP-címet. Statikus IP-címet is lefoglalhat egy virtuális géphez. Ha statikus IP-címet foglal le, érdemes egy adott alhálózatot használni, hogy elkerülje a statikus IP-címek véletlen újbóli használatát egy másik virtuális géphez.  

Ha létrehoz egy virtuális gépet, majd később áttelepítené egy VNetbe, akkor a művelet nem egyszerűen csak a konfiguráció módosításából áll. A virtuális gépet ismét üzembe kell helyeznie a VNetben. Az ismételt üzembe helyezés legegyszerűbb módja, ha törli a virtuális gépet, a hozzá csatolt lemezeket viszont nem, majd újra létrehozza a virtuális gépet a VNetben az eredeti lemezek használatával. 

Ez a táblázat virtuális gépek VNetben való létrehozásának lehetséges módszereit sorolja fel.

| Metódus | Leírás |
| ------ | ----------- |
| [Azure Portal](./windows/quick-create-portal.md) | A korábban említett hálózati beállítások használatával hoz létre virtuális gépet egyetlen hálózati adapterrel. Több hálózati adapterrel rendelkező virtuális gép létrehozásához más módszert kell alkalmaznia. |
| [Azure PowerShell](./windows/tutorial-manage-vm.md) | Az [Add-AzVMNetworkInterface](/powershell/module/az.compute/add-azvmnetworkinterface) használatával adja hozzá a korábban a virtuális gép konfigurációjához létrehozott hálózati adaptert. |
| [Azure CLI](./linux/create-cli-complete.md) | Hozzon létre és csatlakoztasson egy virtuális gépet egy olyan vnet, alhálózathoz és hálózati adapterhez, amely egyedi lépésként van felépítve. |
| [Sablon](./windows/ps-template.md) | Virtuális gép sablon használatával történő üzembe helyezéséhez segítségképp használja a [windowsos virtuális gépek leegyszerűsített üzembe helyezésére](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows) szolgáló sablont. |

## <a name="next-steps"></a>Következő lépések
A virtuális gépekhez készült Azure Virtual Networks szolgáltatás kezeléséhez szükséges virtuálisgép-specifikus lépésekért tekintse meg a [Windows](../virtual-machines/windows/tutorial-virtual-network.md) vagy [Linux](../virtual-machines/linux/tutorial-virtual-network.md) oktatóanyagokat.

A virtuális gépek terheléselosztásához és a Windows vagy [Linux](../virtual-machines/linux/tutorial-load-balancer.md) [rendszerhez](../virtual-machines/windows/tutorial-load-balancer.md) készült, magasan elérhető alkalmazások létrehozásához is vannak oktatóanyagok.

- Ismerje meg a [felhasználó által megadott útvonalak és az IP-továbbítás](../virtual-network/virtual-networks-udr-overview.md) konfigurálásának módját. 
- Ismerje meg a [virtuális hálózatok közötti kapcsolatok](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md) konfigurálásának módját.
- Ismerje meg az [útvonalak hibaelhárításának](../virtual-network/diagnose-network-routing-problem.md) módját.
- További információ a [virtuális gépek hálózati sávszélességéről](../virtual-network/virtual-machine-network-throughput.md).