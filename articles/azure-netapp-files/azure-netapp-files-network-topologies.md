---
title: Útmutató a Azure NetApp Files Network Planning szolgáltatáshoz | Microsoft Docs
description: Útmutatást nyújt a hatékony hálózati architektúra kialakításához Azure NetApp Files használatával.
services: azure-netapp-files
documentationcenter: ''
author: ram-kakani
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: ramakk
ms.openlocfilehash: 96d8ba058a33d408ec2ee2a1adfba9011f393da9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96184484"
---
# <a name="guidelines-for-azure-netapp-files-network-planning"></a>Az Azure NetApp Files hálózattervezési irányelvei

A hálózati architektúra megtervezése az alkalmazás-infrastruktúra megtervezésének egyik kulcsfontosságú eleme. Ez a cikk segít megtervezni a számítási feladatok hatékony hálózati architektúráját, hogy kihasználhassa a Azure NetApp Files gazdag képességeit.

Azure NetApp Files kötetek úgy vannak kialakítva, hogy egy speciális célú alhálózatban legyenek, amelyet az Azure-Virtual Network [delegált](../virtual-network/virtual-network-manage-subnet.md) alhálózatnak nevezünk. Így a köteteket közvetlenül a VNet, az azonos régióban található, egymástól független virtuális hálózatok, illetve a helyszíni átjárón (ExpressRoute vagy VPN Gateway) Virtual Network keresztül is elérheti, ha szükséges. Az alhálózat Azure NetApp Files dedikált, és nincs kapcsolat a többi Azure-szolgáltatással vagy az internettel.

## <a name="considerations"></a>Megfontolandó szempontok  

Azure NetApp Files hálózat tervezésekor érdemes figyelembe vennie néhány megfontolandó szempontot.

### <a name="constraints"></a>Korlátozások

Az alábbi funkciók jelenleg nem támogatottak a Azure NetApp Files esetén: 

* A delegált alhálózatra alkalmazott hálózati biztonsági csoportok (NSG-EK)
* A delegált alhálózatra alkalmazott, felhasználó által megadott útvonalak (UDR-EK)
* Azure-szabályzatok (például egyéni elnevezési házirendek) a Azure NetApp Files felületen
* Terheléselosztó Azure NetApp Files forgalomhoz
* Azure Virtual WAN 
* Zóna redundáns Virtual Network átjárók (az átjárók az az paranccsal) 
* Aktív/aktív Virtual Network GWs 
* Kettős verem (IPv4 és IPv6) VNet

A következő hálózati korlátozások érvényesek Azure NetApp Filesre:

* A Azure NetApp Files (beleértve a társas virtuális hálózatok) VNet használt IP-címek száma nem haladhatja meg az 1000-ot. Dolgozunk a határérték növelésén, hogy megfeleljen az ügyfelek méretezési igényeinek. 
* Minden egyes Azure-Virtual Network (VNet) esetében csak egy alhálózat delegálható Azure NetApp Fileshoz.


### <a name="supported-network-topologies"></a>Támogatott hálózati topológiák

A következő táblázat a Azure NetApp Files által támogatott hálózati topológiákat ismerteti.  Emellett ismerteti a nem támogatott topológiák megkerülő megoldásait is. 

|    Topológiák    |    Támogatott    |     Áthidaló megoldás    |
|-------------------------------------------------------------------------------------------------------------------------------|--------------------|-----------------------------------------------------------------------------|
|    Kapcsolat a kötettel a helyi VNet    |    Yes    |         |
|    Kapcsolat a kötettel egy társ VNet (ugyanabban a régióban)    |    Yes    |         |
|    Kapcsolat a kötettel egy társ VNet (régió vagy globális társ)    |    No    |    Nincsenek    |
|    Kapcsolat egy kötettel a ExpressRoute-átjárón keresztül    |    Yes    |         |
|    Helyszíni kapcsolat a ExpressRoute-átjárón keresztüli küllős VNet, valamint az átjárók közötti VNet    |    Yes    |        |
|    Helyszíni kapcsolat a VPN-átjárón keresztül küllős VNet lévő kötetre    |    Yes    |         |
|    Helyszíni kapcsolat a VPN-átjárón keresztüli küllős VNet, illetve a VNet és az átjárók közötti adatforgalom    |    Yes    |         |


## <a name="virtual-network-for-azure-netapp-files-volumes"></a>Azure NetApp Files kötetek virtuális hálózata

Ez a szakasz a virtuális hálózatok tervezését segítő fogalmakat ismerteti.

### <a name="azure-virtual-networks"></a>Azure-beli virtuális hálózatok

Azure NetApp Files kötet kiépítés előtt létre kell hoznia egy Azure-beli virtuális hálózatot (VNet), vagy az előfizetésben már létezőt kell használnia. A VNet határozza meg a kötet hálózati határát.  A virtuális hálózatok létrehozásával kapcsolatos további információkért tekintse meg az [Azure Virtual Network dokumentációját](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Alhálózatok

Az alhálózatok a virtuális hálózatot különálló, a bennük található Azure-erőforrások által használható címekre osztják.  Azure NetApp Files kötetek a [meghatalmazott alhálózat](../virtual-network/virtual-network-manage-subnet.md)nevű speciális célú alhálózatban találhatók. 

Az alhálózati delegálás explicit jogosultságokat biztosít a Azure NetApp Files szolgáltatás számára, hogy a szolgáltatásra jellemző erőforrásokat hozzon létre az alhálózatban.  A szolgáltatás üzembe helyezése során egyedi azonosítót használ. Ebben az esetben a rendszer létrehoz egy hálózati adaptert, amely lehetővé teszi a Azure NetApp Fileshoz való kapcsolódást.

Ha új VNet használ, létrehozhat egy alhálózatot, és delegálhatja az alhálózatot úgy, hogy Azure NetApp Files az [alhálózat delegálása Azure NetApp Filesre](azure-netapp-files-delegate-subnet.md)című témakör útmutatását követve. Olyan meglévő üres alhálózatot is delegálhat, amely még nem lett delegálva más szolgáltatásokhoz.

Ha a VNet egy másik VNet van társítva, akkor nem lehet kibontani a VNet. Emiatt az új delegált alhálózatot a VNet belül kell létrehozni. Ha ki kell bővíteni a Címterület méretét, törölnie kell a VNet-társítást a Címterület bővítése előtt.

### <a name="udrs-and-nsgs"></a>UDR és NSG

A felhasználó által megadott útvonalak (UDR-EK) és hálózati biztonsági csoportok (NSG-EK) nem támogatottak a Azure NetApp Files delegált alhálózatokon. Azonban a UDR és a NSG más alhálózatokra is alkalmazhatja, akár a Azure NetApp Fileshoz delegált alhálózattal azonos VNet belül.

* Ezután a UDR megadhatja a többi alhálózatról a Azure NetApp Files delegált alhálózatra irányuló forgalmat. Ez segít annak biztosításában, hogy ez a forgalom a Azure NetApp Filesról a többi alhálózatra a rendszerútvonalak használatával legyen összehangolva.  
* A NSG ezt követően engedélyezi vagy megtagadja a forgalmat a Azure NetApp Files delegált alhálózaton. 

## <a name="azure-native-environments"></a>Azure-beli natív környezetek

Az alábbi ábra egy Azure-beli natív környezetet ábrázol:

![Azure – natív hálózatkezelési környezet](../media/azure-netapp-files/azure-netapp-files-network-azure-native-environment.png)

### <a name="local-vnet"></a>Helyi VNet

Alapszintű forgatókönyv egy Azure NetApp Files kötet létrehozása vagy összekötése egy virtuális gépről (VM) ugyanabban a VNet. A fenti ábrán a fenti diagramon a 2. kötet egy delegált alhálózatban jön létre, és az alapértelmezett alhálózatban az 1. virtuális gépre csatlakoztatható VNet.

### <a name="vnet-peering"></a>Virtuális hálózatok közötti társviszony

Ha ugyanabban a régióban további virtuális hálózatok van szükség, amelynek hozzá kell férnie egymás erőforrásaihoz, a virtuális hálózatok az Azure-infrastruktúrán keresztüli biztonságos kapcsolat engedélyezéséhez a [VNet](../virtual-network/virtual-network-peering-overview.md) -társítás használatával csatlakozhat. 

A fenti ábrán vegye fontolóra a 2. és a 3. VNet VNet. Ha a VM 1 virtuális GÉPHEZ 2 vagy 2. kötethez kell csatlakoznia, vagy ha a 2. VM 1. vagy 1. kötethez csatlakoznia kell, engedélyeznie kell a VNet-társítást a 2. és a 3. VNet között. 

Emellett Vegyünk például egy olyan forgatókönyvet, ahol a VNet 1 a VNet 2-vel van társítva, a 2. VNet pedig ugyanabban a régióban található VNet 3. Az 1. VNet erőforrásai a VNet 2 erőforrásaihoz kapcsolódhatnak, de nem tudnak csatlakozni a 3. VNet lévő erőforrásokhoz, kivéve, ha a VNet 1 és a VNet 3 nem található. 

A fenti ábrán bár a VM 3 képes csatlakozni az 1. kötethez, a VM 4 nem tud kapcsolódni a 2. kötethez.  Ennek az az oka, hogy a küllős virtuális hálózatok nem támogatottak, és az _átviteli útválasztás nem támogatott a VNet_-társítások esetében.

## <a name="hybrid-environments"></a>Hibrid környezetek

A következő ábra egy hibrid környezetet ábrázol: 

![Hibrid hálózati környezet](../media/azure-netapp-files/azure-netapp-files-network-hybrid-environment.png)

A hibrid forgatókönyvben a helyszíni adatközpontokból származó alkalmazásoknak hozzá kell férniük az Azure-ban található erőforrásokhoz.  Ez azt jelzi, hogy az adatközpontot ki szeretné-e terjeszteni az Azure-ra, vagy szeretné használni az Azure natív szolgáltatásait vagy a vész-helyreállítást. A helyek közötti VPN-en vagy ExpressRoute keresztül az Azure-beli erőforrásokhoz több helyszíni erőforrás összekapcsolásával kapcsolatos információkért tekintse meg [VPN Gateway tervezési lehetőségeket](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable) .

A hibrid küllős topológiában az Azure hub-VNet a helyszíni hálózathoz való kapcsolódás központi pontja. A küllők a központtal virtuális hálózatok, és a számítási feladatok elkülönítésére használhatók.

A konfigurációtól függően a helyszíni erőforrásokat a hub és a küllők erőforrásaihoz is csatlakoztathatja.

A fenti ábrán látható topológiában a helyszíni hálózat egy Azure-beli hub-VNet csatlakozik, és két küllős virtuális hálózatok található ugyanabban a régióban, mint a hub VNet.  Ebben az esetben a Azure NetApp Files kötetek által támogatott csatlakozási lehetőségek a következők:

* A helyszíni erőforrások 1. és 2. virtuálisgép-hálózata egy helyek közötti VPN-vagy ExpressRoute-áramköri kapcsolaton keresztül tud csatlakozni a központban található 1-es kötethez. 
* A helyszíni erőforrások 1. és 2. virtuálisgép-hálózata a 2. kötethez vagy a 3. kötethez kapcsolódhat helyek közötti VPN-és regionális vnet-társítás esetén.
* A központi VNet található VM 3 a küllős VNet 1 és a 3. kötet 2. kötetéhez tud csatlakozni.
* Az 1. és a küllős VNet 2. virtuális gép 4-es VM-VNet a hub VNet tud csatlakozni az 1. kötethez.
* A küllős VNet 1 virtuális gép 4-es verziójában nem lehet csatlakozni a 3. kötethez a küllős VNet 2. Emellett a küllős VNet2 5. virtuális gép nem tud csatlakozni a 2. kötethez a küllős VNet 1. Ez azért van így, mert a küllős virtuális hálózatok nem támogatottak, és az _átviteli útválasztás nem támogatott a VNet_-társítások esetében.
* A fenti architektúrában, ha van egy átjáró a küllős VNET, akkor a ANF-kötetnek az átjárón keresztül a hub-on keresztül való kapcsolata elvész. A megtervezéssel előnyben részesítette az átjárót a küllő VNet, így csak az átjárón keresztül csatlakozó gépek csatlakozhatnak a ANF-kötethez.

## <a name="next-steps"></a>Következő lépések

[Alhálózat delegálása az Azure NetApp Fileshoz](azure-netapp-files-delegate-subnet.md)