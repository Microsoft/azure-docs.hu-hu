---
title: Gyakori kérdések
description: A Azure Container Instances szolgáltatással kapcsolatos gyakori kérdésekre adott válaszok
author: macolso
ms.author: macolso
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 2645c3f183e2217dff28a96c9c0d376eb82a6476
ms.sourcegitcommit: 87a6587e1a0e242c2cfbbc51103e19ec47b49910
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103573132"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Gyakori kérdések a Azure Container Instances

Ez a cikk a Azure Container Instancesával kapcsolatos gyakori kérdéseket tárgyalja.

## <a name="deployment"></a>Üzembe helyezés

### <a name="how-large-can-my-container-image-be"></a>Mekkora méretű lehet a tároló képe?

Azure Container Instances a telepíthető tároló lemezképének maximális mérete 15 GB. Előfordulhat, hogy a telepítés pillanatában a pontos rendelkezésre állástól függően nagyobb rendszerképeket telepíthet, de ez nem garantált.

A tároló lemezképének mérete befolyásolja, hogy mennyi ideig tart a üzembe helyezése, ezért a tárolók lemezképeit a lehető legkisebbre szeretné megőrizni.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Hogyan lehet felgyorsítani a tároló üzembe helyezését?

Mivel az üzembe helyezési idők egyik fő tényezője a képméret, a méret csökkentésének módját kell keresni. Távolítsa el a nem szükséges rétegeket, vagy csökkentse a képen látható rétegek méretét (egy világosabb alap operációsrendszer-rendszerkép kiválasztásával). Ha például Linux-tárolókat futtat, érdemes lehet a teljes Ubuntu-kiszolgáló helyett az Alpine-t használni alaprendszerképként. Hasonlóképpen, a Windows-tárolók esetén használjon Nano Server Base-rendszerképet, ha lehetséges. 

Azt is ellenőriznie kell, hogy az Azure Container images szolgáltatásban milyen előre gyorsítótárazott lemezképek vannak felsorolva a [gyorsítótárazott lemezképek](/rest/api/container-instances/location/listcachedimages) API-n keresztül. Előfordulhat, hogy ki lehet kapcsolni egy képréteget az egyik előre gyorsítótárazott rendszerképhez. 

A tároló indítási idejének csökkentésével kapcsolatos részletesebb [útmutatásért](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) lásd:.

### <a name="what-windows-base-os-images-are-supported"></a>Milyen Windows-alapú operációsrendszer-lemezképek támogatottak?

> [!NOTE]
> A visszamenőleges kompatibilitással kapcsolatos problémák miatt a 2020-es Windows-frissítések után a következő rendszerkép-verziók tartalmazzák az alaprendszerképben használt minimális verziószámot. A régebbi lemezkép-verziókat használó aktuális telepítések nem érintettek, de az új központi telepítéseknek meg kell felelniük a következő alapképeknek. 

#### <a name="windows-server-2016-base-images"></a>Windows Server 2016 alaplemezképek

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `sac2016` `10.0.14393.3506` vagy újabb
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016`  `10.0.14393.3506` vagy újabb

> [!NOTE]
> A Semi-Annual Channel 1709-es vagy 1803-es kiadásán alapuló Windows-rendszerképek nem támogatottak.

#### <a name="windows-server-2019-and-client-base-images"></a>Windows Server 2019 és ügyféloldali lemezképek

* [Nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809` `10.0.17763.1040` vagy újabb
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019` , `1809` `10.0.17763.1040` vagy újabb
* [Windows](https://hub.docker.com/_/microsoft-windows): `1809` `10.0.17763.1040` vagy újabb

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Milyen .NET-vagy .NET Core-lemezképfájlt használok a tárolóban? 

Használja a legkisebb rendszerképet, amely megfelel a követelményeinek. A Linux esetében használhat egy *Runtime-Alpine* .net Core-rendszerképet, amelyet a .net Core 2,1 kiadása óta támogattak. Windows esetén, ha a teljes .NET-keretrendszert használja, akkor Windows Server Core rendszerképet kell használnia (csak futásidejű rendszerkép, például  *4.7.2-windowsservercore-ltsc2016*). A csak futásidejű lemezképek kisebbek, de nem támogatják a .NET SDK-t igénylő munkaterheléseket.

> [!NOTE]
> Az ACI nem tudja lekérni a képeket a nem OCI-kompatibilis beállításjegyzékből.

### <a name="what-types-of-container-registries-are-compatible-with-aci"></a>Milyen típusú tároló-nyilvántartások kompatibilisek az ACI-val?

Az ACI támogatja az ACR és más külső gyártótól származó, például a DockerHub által készített képek lekérését. Az ACI támogatja az ACR-ből és más, harmadik féltől származó OCI kompatibilis tároló-beállításjegyzékből származó képek lekérését, például a DockerHub egy olyan végponttal, amely nyilvánosan elérhető az interneten.

## <a name="availability-and-quotas"></a>Rendelkezésre állás és kvóták

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Hány magot és memóriát kell lefoglalni a saját tárolók vagy a tároló csoport számára?

Ez valójában a számítási feladattól függ. Indítsa el a kis-és tesztelési teljesítményt a tárolók működésének megtekintéséhez. [Figyelje a CPU-és memória-erőforrások használatát](container-instances-monitor.md), majd vegyen fel magokat vagy memóriát a tárolóban üzembe helyezett folyamatok típusa alapján.

Győződjön meg arról, hogy az [Erőforrás rendelkezésre állását](container-instances-region-availability.md) is ellenőrizni kívánja-e azon régió esetében, amelynek a felső határait a CPU-magok és a rendelkezésre álló memória elérhetővé teszi. 

> [!NOTE]
> A szolgáltatás alapjául szolgáló infrastruktúra egy kis mennyiségű tároló-csoport erőforrásait használja. A tárolók a csoport számára lefoglalt összes erőforráshoz hozzáférhetnek. Ebből kifolyólag tervezzen egy kisebb erőforrás-puffert a csoportban lévő tárolók erőforrásainak kérésekor.

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Milyen mögöttes infrastruktúrát futtat az ACI?

Azure Container Instances célja, hogy kiszolgáló nélküli tárolók legyenek igény szerinti szolgáltatásként, ezért azt szeretnénk, ha a tárolók fejlesztésére kellene összpontosítania, és nem kell aggódnia az infrastruktúrával kapcsolatban! Azok számára, akik kíváncsiak vagy a teljesítmény összehasonlítását szeretnék végezni, az ACI különböző SKU-k készletén fut, elsősorban az F és a D sorozatból. Várjuk, hogy a jövőben is megváltozzon a szolgáltatás fejlesztése és optimalizálása. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Több ezer magot szeretnék üzembe helyezni az ACI-ban – megnövelhető a kvóta?
 
Igen (néha). Tekintse meg a [kvótákat és a korlátozásokat](container-instances-quotas.md) tartalmazó cikket a jelenlegi kvóták esetében, és hogy mely korlátokat lehet megnövelni a kérelem alapján

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Telepíthetek több mint 4 maggal és 16 GB RAM-mal?

Jelenleg nem. Jelenleg ezek a tároló csoportok maximális száma. Konkrét követelményekkel vagy kérésekkel forduljon az Azure ügyfélszolgálatához. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Mikor lesz az ACI egy adott régióban?

Az aktuális régió elérhetőségét [itt](container-instances-region-availability.md)tesszük közzé. Ha egy adott régióra vonatkozó követelményt tartalmaz, forduljon az Azure ügyfélszolgálatához.

## <a name="features-and-scenarios"></a>Funkciók és forgatókönyvek

### <a name="how-do-i-scale-a-container-group"></a>Hogyan méretezheti a tárolók csoportját?

Jelenleg a méretezés nem érhető el a tárolók és a tárolók csoportjai számára. Ha több példányt kell futtatnia, használja az API-t az automatizáláshoz, és hozzon létre több kérést a tároló csoport létrehozásához a szolgáltatáshoz. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Milyen funkciók érhetők el az egyéni VNet futó példányok számára?

A [tároló csoportokat üzembe helyezheti egy tetszőleges Azure-beli virtuális hálózatban](container-instances-vnet.md) , és delegálhatja a privát IP-címeket a VNet az Azure-erőforrások közötti forgalom irányításához. A hálózatkezelési forgatókönyvek és a Azure Container Instancesekkel kapcsolatos korlátozások: [virtuális hálózati forgatókönyvek és erőforrások](container-instances-virtual-network-concepts.md).

## <a name="pricing"></a>Díjszabás

### <a name="when-does-the-meter-start-running"></a>Mikor kezdődik a fogyasztásmérő futtatása?

A tároló csoport időtartamát a rendszer az első tároló rendszerképének (új központi telepítés esetén) vagy a tároló csoport újraindítása (ha már üzembe helyezett) újraindításának időpontja alapján számítja ki, amíg a tároló csoport le nem áll. A részletekért tekintse meg [Container instances díjszabását](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>A tárolók leállítása után nem kell fizetnem?

A mérőműszer leállítása a teljes tároló csoport leállítása után leáll. Ha a tároló csoport egyik tárolója fut, akkor az erőforrásokat abban az esetben tartjuk, ha újra el szeretné indítani a tárolókat. 

## <a name="next-steps"></a>Következő lépések

* [További](container-instances-overview.md) információ a Azure Container Instancesról.
* Azure Container Instances [gyakori problémáinak elhárítása](container-instances-troubleshooting.md) .
