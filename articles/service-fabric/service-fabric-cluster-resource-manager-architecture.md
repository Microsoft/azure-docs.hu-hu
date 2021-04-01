---
title: Resource Manager-architektúra
description: Az Azure Service Fabric fürterőforrás-kezelő szolgáltatással kapcsolatos és építészeti információk áttekintése.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0aff55810508fedcf354fba3d9fca9f7a402029b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94685834"
---
# <a name="cluster-resource-manager-architecture-overview"></a>A fürterőforrás-kezelő architektúrájának áttekintése
A Service Fabric fürterőforrás-kezelő egy központi szolgáltatás, amely a fürtben fut. Felügyeli a fürtben lévő szolgáltatások kívánt állapotát, különösen az erőforrás-felhasználás és az elhelyezési szabályok tekintetében. 

A fürt erőforrásainak kezeléséhez a Service Fabric fürterőforrás-kezelőnek több információval kell rendelkeznie:

- Jelenleg mely szolgáltatások léteznek
- Minden szolgáltatás aktuális (vagy alapértelmezett) erőforrás-felhasználása 
- A fennmaradó fürt kapacitása 
- A fürt csomópontjainak kapacitása 
- Az egyes csomópontokon felhasznált erőforrások mennyisége

Egy adott szolgáltatás erőforrás-felhasználása idővel változhat, és a szolgáltatások általában egynél több típusú erőforrással törődnek. A különböző szolgáltatások között valós fizikai és fizikai erőforrások is mérhetők. A szolgáltatások nyomon követhetik a fizikai mérőszámokat, például a memória és a lemez felhasználását. Gyakrabban a szolgáltatások a logikai metrikákkal kapcsolatosak – például "WorkQueueDepth" vagy "TotalRequests". A logikai és fizikai mérőszámok is használhatók ugyanabban a fürtben. A metrikák több szolgáltatás között is megoszthatók, vagy egy adott szolgáltatásra vonatkoznak.

## <a name="other-considerations"></a>További szempontok
A fürt tulajdonosai és üzemeltetői eltérhetnek a szolgáltatás és az alkalmazás szerzőktől, vagy legalább ugyanazok, akik különböző kalapokat viselnek. Az alkalmazás fejlesztése során néhány dolgot is tudnia kell, hogy mire van szüksége. Megbecsüli az általa felhasznált erőforrásokat, valamint a különböző szolgáltatások üzembe helyezésének módját. A webes szintet például az internetre kitett csomópontokon kell futtatni, míg az adatbázis-szolgáltatások nem. Egy másik példaként a webszolgáltatásokat valószínűleg a CPU és a hálózat korlátozza, míg az adatszintek szolgáltatásai többet törődnek a memória és a lemez használatával. Azonban az adott szolgáltatáshoz éles környezetben, vagy a szolgáltatásra való frissítést kezelő személy egy másik feladatot hajt végre, és különböző eszközöket igényel. 

A fürt és a szolgáltatások egyaránt dinamikusak:

- A fürt csomópontjainak száma növelhető és csökkenthető
- A különböző méretű és típusú csomópontok is elérhetők
- A szolgáltatások létrehozhatók, eltávolíthatók és módosíthatók a kívánt erőforrás-kiosztások és elhelyezési szabályok
- A frissítések és egyéb felügyeleti műveletek a fürtön keresztül is áthelyezhetők az infrastruktúra szintjén az alkalmazásban.
- A hibák bármikor megtörténhetnek.

## <a name="cluster-resource-manager-components-and-data-flow"></a>A fürterőforrás-kezelő összetevői és adatfolyama
A fürterőforrás-kezelőnek nyomon kell követnie az egyes szolgáltatások követelményeit, valamint az erőforrások felhasználását az adott szolgáltatásokon belüli egyes szolgáltatási objektumok alapján. A fürterőforrás-kezelő két fogalmi részből áll: az egyes csomópontokon futó ügynökökre és hibatűrő szolgáltatásokra. Az egyes csomópontokon lévő ügynökök nyomon követik a szolgáltatások betöltési jelentéseit, összesítik őket, és rendszeres időközönként bejelentik azokat. A fürterőforrás-kezelő szolgáltatás a helyi ügynököktől származó összes információt összesíti, és az aktuális konfiguráció alapján működik.

Nézzük meg a következő ábrát:

<center>

![A fürterőforrás-kezelő szolgáltatást megjelenítő diagram a helyi ügynököktől származó összes információt összesíti, és az aktuális konfiguráció alapján működik.][Image1]
</center>

A futtatókörnyezet során számos változás következik be. Tegyük fel például, hogy az egyes szolgáltatások által használt erőforrások mennyisége megváltozik, néhány szolgáltatás meghibásodik, és néhány csomópont csatlakozik a fürthöz, és elhagyja a fürtöt. A rendszer összesíti és rendszeres időközönként elküldi a csomópontok összes módosítását a fürterőforrás-kezelő szolgáltatásnak (1, 2), ahol az összesítésük újra, elemezve és tárolva van. A szolgáltatás néhány másodpercenként megtekinti a módosításokat, és meghatározza, hogy van-e szükség műveletekre (3). Láthatja például, hogy egyes üres csomópontok hozzá lettek adva a fürthöz. Ennek eredményeképpen úgy dönt, hogy egyes szolgáltatásokat áthelyez a csomópontokra. A fürterőforrás-kezelő azt is megfigyelheti, hogy egy adott csomópont túl van terhelve, vagy bizonyos szolgáltatások sikertelenek vagy törölve lettek, az erőforrások máshol való felszabadítása.

Nézzük meg az alábbi ábrát, és nézzük meg, mi történik tovább. Tegyük fel, hogy a fürterőforrás-kezelő meghatározza, hogy szükség van-e a módosításokra. Más rendszerszolgáltatásokkal (különösen a Feladatátvételi felügyelő) koordinálja a szükséges módosításokat. Ezután a rendszer elküldi a szükséges parancsokat a megfelelő csomópontoknak (4). Tegyük fel például, hogy a Resource Manager észrevette, hogy a Csomópont5 számítógépre túlterhelt, ezért úgy döntött, hogy a B szolgáltatást a Csomópont5 számítógépre-ről a Csomópont4-re helyezi át. Az újrakonfigurálás végén (5) a fürt így néz ki:

<center>

![Erőforrás-Balancer architektúrája][Image2]
</center>

## <a name="next-steps"></a>Következő lépések
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni róluk, tekintse meg ezt a cikket a [Service Fabric-fürt leírását ismertető](./service-fabric-cluster-resource-manager-cluster-description.md) cikkben.
- A fürterőforrás-kezelő elsődleges feladatai a fürt kiegyensúlyozására és az elhelyezési szabályok betartatására szolgálnak. A viselkedés konfigurálásával kapcsolatos további információkért lásd: [Service Fabric-fürt terheléselosztása](./service-fabric-cluster-resource-manager-balancing.md)

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
