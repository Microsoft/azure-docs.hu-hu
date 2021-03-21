---
title: Azure Service Fabric hálózatkezelés – ajánlott eljárások
description: Szabályok és kialakítási szempontok az Azure Service Fabric használatával történő hálózati kapcsolatok kezeléséhez.
author: chrpap
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: chrpap
ms.openlocfilehash: caba864e77822ccab649f694df7e63e0ee5d6e51
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732564"
---
# <a name="networking"></a>Hálózatkezelés

Az Azure Service Fabric-fürtök létrehozásakor és kezelésekor hálózati kapcsolatot biztosít a csomópontjai és alkalmazásai számára. A hálózati erőforrások közé tartozik az IP-címtartományok, a virtuális hálózatok, a terheléselosztó és a hálózati biztonsági csoportok. Ebben a cikkben az ezen erőforrásokkal kapcsolatos ajánlott eljárásokat ismerheti meg.

Tekintse át az Azure [Service Fabric hálózatkezelési mintáit](./service-fabric-patterns-networking.md) , amelyből megtudhatja, hogyan hozhat létre olyan fürtöket, amelyek a következő szolgáltatásokat használják: meglévő virtuális hálózat vagy alhálózat, statikus nyilvános IP-cím, csak belső terheléselosztó, belső és külső terheléselosztó.

## <a name="infrastructure-networking"></a>Infrastruktúra-hálózatkezelés
Maximalizálja a virtuális gép teljesítményét a gyorsított hálózatkezeléssel azáltal, hogy deklarálja a *enableAcceleratedNetworking* tulajdonságot a Resource Manager-sablonban, az alábbi kódrészlet egy virtuálisgép-méretezési csoport networkinterfaceconfigurations szakaszához, amely lehetővé teszi a gyorsított hálózatkezelést:

```json
"networkInterfaceConfigurations": [
  {
    "name": "[concat(variables('nicName'), '-0')]",
    "properties": {
      "enableAcceleratedNetworking": true,
      "ipConfigurations": [
        {
        <snip>
        }
      ],
      "primary": true
    }
  }
]
```
A Service Fabric-fürt Linux rendszeren is kiépíthető [gyorsított hálózatkezeléssel](../virtual-network/create-vm-accelerated-networking-cli.md), a [Windows pedig gyorsított hálózatkezeléssel](../virtual-network/create-vm-accelerated-networking-powershell.md).

A gyorsított hálózatkezelést az Azure Virtual Machine Series SKU-k támogatják: a D/DSv2, a D/DSv3, az E/ESv3, az F/FS, a FSv2 és az MS/MMS. A gyorsított hálózatkezelést sikeresen teszteltük a Standard_DS8_v3 01/23/2019 SKU használatával Service Fabric Windows-fürtön, illetve a 01/29/2019-es Standard_DS12_v2 a Service Fabric Linux-fürtön való használatával. Vegye figyelembe, hogy a gyorsított hálózatkezeléshez legalább 4 vCPU szükséges. 

Ha egy meglévő Service Fabric fürtön szeretné engedélyezni a gyorsított hálózatkezelést, először [egy virtuálisgép-méretezési csoport hozzáadásával kell kialakítania egy Service Fabric-fürtöt](./virtual-machine-scale-set-scale-node-type-scale-out.md)a következők elvégzéséhez:
1. NodeType kiépítése a gyorsított hálózatkezelés engedélyezésével
2. A szolgáltatások és az állapotuk áttelepítése a kiépített NodeType a gyorsított hálózatkezelés engedélyezésével

Az infrastruktúra horizontális felskálázása szükséges a gyorsított hálózatkezelés engedélyezéséhez egy meglévő fürtön, mert a gyorsított hálózatkezelés engedélyezése a leállást okozhatja, mivel a rendelkezésre állási csoportba tartozó összes virtuális gépet le kell állítani, és fel kell [szabadítani a gyorsított hálózatkezelés bármely meglévő hálózati adapterre való engedélyezése előtt](../virtual-network/create-vm-accelerated-networking-cli.md#enable-accelerated-networking-on-existing-vms).

## <a name="cluster-networking"></a>Fürthálózat

* Service Fabric fürtöket meglévő virtuális hálózatba lehet telepíteni a [Service Fabric hálózati mintákban](./service-fabric-patterns-networking.md)leírt lépések végrehajtásával.

* A hálózati biztonsági csoportok (NSG-EK) használata ajánlott a csomópont-típusoknál, hogy korlátozza a bejövő és kimenő forgalmat a fürtre. Győződjön meg arról, hogy a szükséges portok meg vannak nyitva a NSG. 

* A Service Fabric rendszerszolgáltatásokat tartalmazó elsődleges csomópont típusát nem kell a külső terheléselosztó használatával kitenni, és [belső](./service-fabric-patterns-networking.md#internal-only-load-balancer) terheléselosztó is elérhetővé teheti.

* Használjon [statikus nyilvános IP-címet](./service-fabric-patterns-networking.md#static-public-ip-address-1) a fürthöz.

## <a name="network-security-rules"></a>Hálózati biztonsági szabályok

Az alapvető szabályok itt az Azure által felügyelt Service Fabric-fürt biztonsági zárolásának minimális száma. Nem sikerült megnyitni a következő portokat, vagy az IP/URL-cím jóváhagyása megakadályozza a fürt megfelelő működését, és előfordulhat, hogy nem támogatott. Ezzel a szabállyal szigorúan szükséges az [operációs rendszer rendszerképének automatikus frissítése](../virtual-machine-scale-sets/virtual-machine-scale-sets-automatic-upgrade.md), ellenkező esetben további portokat kell megnyitnia.

### <a name="inbound"></a>Bejövő 
|Prioritás   |Name               |Port        |Protokoll  |Forrás             |Cél       |Művelet   
|---        |---                |---         |---       |---                |---               |---
|3900       |Azure              |19080       |TCP       |Internet           |VirtualNetwork    |Engedélyezés
|3910       |Ügyfél             |19000       |TCP       |Internet           |VirtualNetwork    |Engedélyezés
|3920       |Fürt            |1025-1027   |TCP       |VirtualNetwork     |VirtualNetwork    |Engedélyezés
|3930       |Rövid élettartamú          |49152-65534 |TCP       |VirtualNetwork     |VirtualNetwork    |Engedélyezés
|3940       |Alkalmazás        |20000-30000 |TCP       |VirtualNetwork     |VirtualNetwork    |Engedélyezés
|3950       |SMB                |445         |TCP       |VirtualNetwork     |VirtualNetwork    |Engedélyezés
|3960       |RDP                |3389-3488   |TCP       |Internet           |VirtualNetwork    |Megtagadás
|3970       |SSH                |22          |TCP       |Internet           |VirtualNetwork    |Megtagadás
|3980       |Egyéni végpont    |80          |TCP       |Internet           |VirtualNetwork    |Engedélyezés
|4100       |Bejövő forgalom blokkolása      |443         |Bármelyik       |Bármelyik                |Bármelyik               |Engedélyezés

További információ a bejövő biztonsági szabályokról:

* **Azure**-ban. Ezt a portot a Service Fabric Explorer használja a fürt tallózásához és kezeléséhez, és az Service Fabric erőforrás-szolgáltató is használja a fürt adatainak lekérdezéséhez az Azure felügyeleti portál való megjelenítéshez. Ha ez a port nem érhető el a Service Fabric erőforrás-szolgáltatótól, akkor megjelenik egy üzenet, például "csomópontok nem találhatók" vagy "UpgradeServiceNotReachable" a Azure Portal, és a csomópont és az alkalmazások listája üresen jelenik meg. Ez azt jelenti, hogy ha látni szeretné a fürtjét az Azure-felügyeleti portál, akkor a terheléselosztó számára közzé kell tenni egy nyilvános IP-címet, és a NSG engedélyeznie kell a bejövő 19080-forgalmat.  

* **Ügyfél**. Az ügyfél-kapcsolódási végpont API-k, például a REST, a PowerShell/parancssori felület. 

* **Fürt**. Csomópontok közötti kommunikációhoz használatos; Soha ne legyen letiltva.

* **Ideiglenes**. Service Fabric a portok egy részét használja az alkalmazás portjaiként, és a fennmaradók elérhetők az operációs rendszer számára. Ez a tartomány az operációs rendszerben található meglévő tartományhoz is leképezhető, így az összes célra használhatja a mintában megadott tartományokat. Győződjön meg arról, hogy a kezdő és a záró portok közötti különbség legalább 255. Előfordulhat, hogy ütközések merülhetnek fel, ha a különbség túl alacsony, mert ez a tartomány meg van osztva az operációs rendszerrel. A konfigurált Dinamikus porttartomány megjelenítéséhez futtassa a *következőt: netsh int IPv4 show Dynamic port TCP*. Ezek a portok nem szükségesek a Linux-fürtökhöz.

* **Alkalmazás**. Az alkalmazás portszámának elég nagynak kell lennie ahhoz, hogy le lehessen fedni az alkalmazások végponti követelményeit. Ez a tartomány nem lehet a számítógép Dinamikus porttartomány, azaz a konfigurációban beállított az ephemeralports-tartomány. Service Fabric ezeket a portokat használja, amikor új portokra van szükség, és gondoskodik a portok tűzfalának megnyitásáról a csomópontokon.

* **SMB**. Az SMB protokollt két forgatókönyv esetén a Lemezképtárolóba szolgáltatás használja. Ez a port szükséges ahhoz, hogy letöltse a csomagokat a Lemezképtárolóba a csomópontok között, valamint replikálja ezeket a replikák között. 

* **RDP**. Nem kötelező, ha az internetről vagy a VirtualNetwork Jumpbox-forgatókönyvek esetén RDP szükséges. 

* **SSH**-val. Nem kötelező, ha SSH szükséges az internetről vagy a VirtualNetwork Jumpbox-forgatókönyvekhez.

* **Egyéni végpont**. Egy példa arra, hogy az alkalmazás egy internetről elérhető végpontot engedélyezzen.

### <a name="outbound"></a>Kimenő

|Prioritás   |Name               |Port        |Protokoll  |Forrás             |Cél       |Művelet   
|---        |---                |---         |---       |---                |---               |---
|3900       |Network (Hálózat)            |Bármelyik         |TCP       |VirtualNetwork     |VirtualNetwork    |Engedélyezés
|3910       |Erőforrás-szolgáltató  |443         |TCP       |VirtualNetwork     |ServiceFabric     |Engedélyezés
|3920       |Frissítés            |443         |TCP       |VirtualNetwork     |Internet          |Engedélyezés
|3950       |Kimenő forgalom letiltása     |Bármelyik         |Bármelyik       |Bármelyik                |Bármelyik               |Megtagadás

További információ a kimenő biztonsági szabályokról:

* **Hálózat**. Kommunikációs csatorna alhálózatok és egy másik virtuális hálózat között.

* **Erőforrás-szolgáltató**. A UpgradeService által a Service Fabric erőforrás-szolgáltató által az összes ARM-telepítés végrehajtásához való kapcsolódás.

* **Frissítés**. A frissítési szolgáltatás a download.microsoft.com használatával kéri le a BITS szolgáltatást, ez a telepítéshez, a lemezképek újraindításához és a futásidejű frissítésekhez szükséges. A szolgáltatás dinamikus IP-címekkel működik. A "belső only" terheléselosztó forgatókönyvében egy további külső terheléselosztó hozzáadására van szükség egy olyan szabállyal, amely engedélyezi a kimenő forgalmat az 443-as porton. Ezt a portot a sikeres telepítés után is le lehet tiltani, de ebben az esetben a frissítési csomagot a csomópontok számára kell kiosztani, vagy a portot rövid ideig meg kell nyitni, azután manuális frissítésre van szükség.

A biztonsági zárolással kapcsolatos problémák nyomon követéséhez használja a Azure Firewallt a [NSG flow-napló](../network-watcher/network-watcher-nsg-flow-logging-overview.md) és a [Traffic Analytics](../network-watcher/traffic-analytics.md) használatával. Az ARM-sablon [Service Fabric a NSG-vel](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) jó példa az indításra. 


## <a name="application-networking"></a>Alkalmazás hálózatkezelése

* A Windows-tárolók számítási feladatainak futtatásához használja a [nyílt hálózati módot](./service-fabric-networking-modes.md#set-up-open-networking-mode) a szolgáltatások közötti kommunikáció megkönnyítéséhez.

* Használjon fordított proxyt, például [Traefik](https://docs.traefik.io/v1.6/configuration/backends/servicefabric/) vagy a [Service Fabric fordított proxyt](./service-fabric-reverseproxy.md) a gyakori alkalmazás-portok (például 80 vagy 443) elérhetővé tétele érdekében.

* Azon gapped gépeken üzemeltetett Windows-tárolók esetében, amelyek nem tudnak lekérni alaprétegeket az Azure Cloud Storage-ból, felülbírálják a külső réteg viselkedését a [--Allow-nem terjeszthető összetevők](/virtualization/windowscontainers/about/faq#how-do-i-make-my-container-images-available-on-air-gapped-machines) jelző használatával a Docker-démonban.

## <a name="next-steps"></a>Következő lépések

* Fürt létrehozása a Windows Servert futtató virtuális gépeken vagy számítógépeken: [Service Fabric Windows Server-fürt létrehozása](service-fabric-cluster-creation-for-windows-server.md)
* Fürt létrehozása virtuális gépeken vagy Linuxon futó számítógépeken: [Linux-fürt létrehozása](service-fabric-cluster-creation-via-portal.md)
* A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

[NSGSetup]: ./media/service-fabric-best-practices/service-fabric-nsg-rules.png
