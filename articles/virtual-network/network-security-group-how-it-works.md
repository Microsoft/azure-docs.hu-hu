---
title: Hálózati biztonsági csoport – hogyan működik?
titlesuffix: Azure Virtual Network
description: Ismerje meg, hogy a hálózati biztonsági csoportok hogyan segíthetnek az Azure-erőforrások közötti hálózati forgalom szűrésében.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: NA
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/24/2020
ms.author: kumud
ms.reviewer: kumud
ms.openlocfilehash: 9510c4b0940a0a03ae9a232c3329817468aaf358
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/04/2021
ms.locfileid: "99537898"
---
# <a name="how-network-security-groups-filter-network-traffic"></a>Hálózati biztonsági csoportok szűrése hálózati forgalom alapján
<a name="network-security-groups"></a>

Egy Azure-beli hálózati biztonsági csoport használatával szűrheti az Azure-beli virtuális hálózatban lévő Azure-erőforrások felé irányuló és onnan érkező hálózati forgalmat. A hálózati biztonsági csoportok olyan [biztonsági szabályokat](./network-security-groups-overview.md#security-rules) tartalmaznak, amelyek engedélyezik vagy letiltják a különböző típusú Azure-erőforrások bejövő vagy kimenő hálózati forgalmát. Az egyes szabályokhoz meghatározhatja a forrást és a célt, valamint a használni kívánt portot és protokollt.

Az Azure-beli virtuális hálózatokban több Azure-szolgáltatásból is helyezhet üzembe erőforrásokat. A teljes listáért tekintse meg [a virtuális hálózatokban üzembe helyezhető szolgáltatásokat](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) ismertető témakört. A virtuális gépek mindegyik virtuális hálózatának [alhálózatához](virtual-network-manage-subnet.md#change-subnet-settings) és [hálózati adapteréhez](virtual-network-network-interface.md#associate-or-dissociate-a-network-security-group) nulla vagy egy hálózati biztonsági csoport rendelhető hozzá. Egy adott hálózati biztonsági csoport tetszőleges számú alhálózathoz és hálózati adapterhez rendelhető.

A következő képen különböző forgatókönyvek láthatók a hálózati biztonsági csoportok üzembe helyezésére az internetről bejövő és az internetre kimenő hálózati forgalom engedélyezéséhez a 80-as TCP-porton keresztül:

![NSG-feldolgozás](./media/network-security-group-how-it-works/network-security-group-interaction.png)

A fenti kép és a következő leírás mutatja be, hogy az Azure hogyan dolgozza fel a hálózati biztonsági csoportok bejövő és kimenő szabályait:

## <a name="inbound-traffic"></a>Bejövő forgalom

A bejövő forgalom esetében az Azure először a hálózati biztonsági csoport alhálózathoz rendelt szabályait dolgozza fel (ha van alhálózat), majd a hálózati biztonsági csoport hálózati adapterhez rendelt szabályait (ha van hálózati adapter).

- **VM1**: Az *NSG1* biztonsági szabályai lesznek feldolgozva, mivel ez a *Subnet1* alhálózathoz van rendelve, és a *VM1* a *Subnet1* alhálózatban található. Ha nem hozott létre egy szabályt, amely engedi a 80-as port bejövő forgalmát, a forgalmat az alapértelmezett [DenyAllInbound](./network-security-groups-overview.md#denyallinbound) biztonsági szabály letiltja, és azt az *NSG2* soha nem értékeli ki, mivel az *NSG2* a hálózati adapterhez van rendelve. Ha az *NSG1* rendelkezik olyan biztonsági szabállyal, amely engedélyezi a forgalmat a 80-as porton, a forgalmat ezután az *NSG2* feldolgozza. Ahhoz, hogy a 80-as porton engedélyezve legyen a virtuális gépre irányuló forgalom, az *NSG1* és az *NSG2* csoportnak egyaránt rendelkeznie kell olyan szabállyal, amely engedi az internetről beérkező forgalmat a 80-as porton.
- **VM2**: Az *NSG1* szabályai fel lesznek dolgozva, mivel a *VM2* is a *Subnet1* alhálózaton található. Mivel a *VM2* hálózati adapteréhez nincs hálózati biztonsági csoport rendelve, erre a gépre az *NSG1* által átengedett minden forgalom megérkezik, és az *NSG1* által letiltott minden forgalom blokkolva lesz. Ha egy alhálózathoz egy hálózati biztonsági csoport van rendelve, az adott alhálózaton az összes erőforrás számára le lesz tiltva vagy engedélyezve lesz a forgalom.
- **VM3**: Mivel a *Subnet2* alhálózathoz nincs hálózati biztonsági csoport rendelve, a forgalom engedélyezve lesz az alhálózaton, és az *NSG2* feldolgozza, mivel az *NSG2* hozzá van rendelve a *VM3* géphez csatlakoztatott hálózati adapterhez.
- **VM4**: A forgalom engedélyezve van a *VM4* gépen, mivel a *Subnet3* hálózathoz vagy a virtuális gép hálózati adapteréhez nincs hálózati biztonsági csoport rendelve. Ha egy alhálózathoz vagy hálózati adapterhez nincs hálózati biztonsági csoport rendelve, ezeken a teljes hálózati forgalom engedélyezve lesz.

## <a name="outbound-traffic"></a>Kimenő forgalom

A kimenő forgalom esetében az Azure először a hálózati biztonsági csoport hálózati adapterhez rendelt szabályait dolgozza fel (ha van hálózati adapter), majd a hálózati biztonsági csoport alhálózathoz rendelt szabályait (ha van alhálózat).

- **VM1**: Az *NSG2* biztonsági szabályai fel lesznek dolgozva. Ha nem hoz létre egy biztonsági szabályt, amely letiltja a 80-as port internetre irányuló kimenő forgalmát, a forgalmat az alapértelmezett [AllowInternetOutbound](./network-security-groups-overview.md#allowinternetoutbound) biztonsági szabály az *NSG1* és az *NSG2* csoporton egyaránt engedélyezi. Ha az *NSG2* rendelkezik olyan biztonsági szabállyal, amely letiltja a forgalmat a 80-as porton, a forgalom le lesz tiltva, és az *NSG1* soha nem értékeli ki. Ahhoz, hogy a 80-as porton le legyen tiltva a virtuális gépről érkező forgalom, legalább az egyik hálózati biztonsági csoportnak rendelkeznie kell olyan szabállyal, amely letiltja az internetre irányuló kimenő forgalmat a 80-as porton.
- **VM2**: A teljes forgalom áthaladhat a hálózati adapteren keresztül az alhálózatra, mivel a *VM2* géphez csatlakoztatott hálózati adapterhez nincs hálózati biztonsági csoport rendelve. Az *NSG1* szabályai fel lesznek dolgozva.
- **VM3**: Ha az *NSG2* rendelkezik olyan biztonsági szabállyal, amely letiltja a forgalmat a 80-as porton, a forgalom le lesz tiltva. Ha az *NSG2* rendelkezik olyan biztonsági szabállyal, amely engedélyezi a forgalmat a 80-as porton, a 80-as porton engedélyezve lesz az internetre irányuló kimenő forgalom, mivel a *Subnet2* hálózathoz nincs hálózati biztonsági csoport rendelve.
- **VM4**: A teljes hálózati forgalom engedélyezve van a *VM4* gépről, mivel a virtuális gép hálózati adapteréhez vagy a *Subnet3* alhálózathoz nincs hálózati biztonsági csoport rendelve.


## <a name="intra-subnet-traffic"></a>Intra-Subnet forgalom

Fontos megjegyezni, hogy az alhálózathoz társított NSG biztonsági szabályai befolyásolhatják a virtuális gépek közötti kapcsolatot. Ha például egy olyan szabályt ad hozzá a *NSG1* -hez, amely megtagadja az összes bejövő és kimenő forgalmat, a *VM1* és a *VM2* többé nem fog tudni kommunikálni egymással. Egy másik szabályt kifejezetten hozzá kell adni ahhoz, hogy ezt engedélyezzék. 

A hálózati adapterekhez rendelt összesített szabályokat könnyen megismerheti a hálózati adapter [érvényes biztonsági szabályainak](virtual-network-network-interface.md#view-effective-security-rules) megtekintésével. Emellett az Azure Network Watcher [IP-forgalom ellenőrzése](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md?toc=%2fazure%2fvirtual-network%2ftoc.json) szolgáltatásával is megállapíthatja, hogy a kommunikáció engedélyezett-e a hálózati adapterre/adapterről. Az IP flow ellenőrzi, hogy a kommunikáció engedélyezett vagy megtagadott-e, valamint hogy melyik hálózati biztonsági szabály engedélyezi vagy tiltja a forgalmat.

> [!NOTE]
> A hálózati biztonsági csoportok az alhálózatokhoz vagy a klasszikus üzemi modellben üzembe helyezett virtuális gépekhez és felhőalapú szolgáltatásokhoz, valamint a Resource Manager-alapú üzemi modellben lévő alhálózatokhoz vagy hálózati adapterekhez vannak társítva. Az Azure üzembehelyezési modellekkel kapcsolatos további információkért lásd: [Az Azure üzemi modelljeinek megismerése](../azure-resource-manager/management/deployment-models.md?toc=%2fazure%2fvirtual-network%2ftoc.json).

> [!TIP]
> Ha nincs konkrét oka a szolgáltatásra, javasoljuk, hogy rendeljen egy hálózati biztonsági csoportot egy alhálózathoz vagy egy hálózati adapterhez, de ne mindkettőt. Mivel az alhálózathoz rendelt hálózati biztonsági csoport szabályai ütközhetnek a hálózati adapterhez rendelt hálózati biztonsági csoport szabályaival, nem várt kommunikációs problémák merülhetnek fel, amelyek hibaelhárítást igényelnek.

## <a name="next-steps"></a>Következő lépések

* A virtuális hálózatokban üzembe helyezhető és hálózati biztonsági csoportokkal használható Azure-erőforrásokkal kapcsolatos információkért tekintse meg az [Azure-szolgáltatások virtuális hálózati integrációját](virtual-network-for-azure-services.md) ismertető cikket.
* Ha korábban még nem hozott létre hálózati biztonsági csoportot, ebben a rövid [oktatóanyagban](tutorial-filter-network-traffic.md) némi gyakorlatra tehet szert.
* Ha már ismeri a hálózati biztonsági csoportok működését, és kezelni szeretné őket, tekintse meg a [hálózati biztonsági csoportok kezelését](manage-network-security-group.md) bemutató témakört. 
* Ha kommunikációs problémákat tapasztal, és hibaelhárítást végezne a hálózati biztonsági csoportokon, tekintse meg [a virtuális gépek hálózatiforgalom-szűrési problémáinak diagnosztizálását](diagnose-network-traffic-filter-problem.md) ismertető rövid útmutatót. 
* Megtudhatja, hogyan engedélyezheti a [hálózati biztonsági csoport folyamatábráit](../network-watcher/network-watcher-nsg-flow-logging-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) a társított hálózati biztonsági csoporttal rendelkező erőforrások felé irányuló hálózati forgalom elemzéséhez.