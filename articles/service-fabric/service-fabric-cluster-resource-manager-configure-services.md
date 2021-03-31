---
title: Metrikák és elhelyezési beállítások megadása
description: Megtudhatja, hogyan leírhat egy Service Fabric szolgáltatást mérőszámok, elhelyezési megkötések és egyéb elhelyezési házirendek megadásával.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: d4dcd319000edb204ba188ed14b4c797dba5cd38
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "75610097"
---
# <a name="configuring-cluster-resource-manager-settings-for-service-fabric-services"></a>A fürterőforrás-kezelő beállításainak konfigurálása Service Fabric szolgáltatásokhoz
A Service Fabric fürterőforrás-kezelője lehetővé teszi az egyes nevesített szolgáltatásokra vonatkozó szabályok részletes szabályozását. Minden elnevezett szolgáltatás megadhatja a fürtben való felosztáshoz szükséges szabályokat. Az elnevezett szolgáltatások meghatározhatják a jelentéshez használni kívánt mérőszámok készletét is, beleértve a szolgáltatással kapcsolatos fontos adatokat is. A szolgáltatások konfigurálásának három különböző feladata van:

1. Elhelyezési megkötések konfigurálása
2. Metrikák konfigurálása
3. Speciális elhelyezési házirendek és egyéb szabályok konfigurálása (kevésbé gyakori)

## <a name="placement-constraints"></a>Elhelyezési megkötések
Az elhelyezési megkötések segítségével szabályozható, hogy a fürt mely csomópontjain fusson a szolgáltatás. Általában egy adott névvel ellátott Service-példány vagy egy adott típus összes szolgáltatása egy adott típusú csomóponton való futtatásra van korlátozva. Az elhelyezési megkötések bővíthetők. Meghatározhatja a tulajdonságok tetszőleges készletét, és kiválaszthatja őket a szolgáltatások létrehozásakor megkötésekkel. A szolgáltatás futása közben is módosíthatja a szolgáltatások elhelyezési korlátozásait. Ez lehetővé teszi, hogy válaszoljon a fürt változásaira vagy a szolgáltatás követelményeire. Egy adott csomópont tulajdonságainak frissítése dinamikusan is megoldható a fürtben. Az elhelyezési korlátozásokkal és azok konfigurálásával kapcsolatos további információkat ebben a [cikkben talál.](service-fabric-cluster-resource-manager-cluster-description.md#node-properties-and-placement-constraints)

## <a name="metrics"></a>Mérőszámok
A metrikák az adott névvel ellátott szolgáltatás által igényelt erőforrások összessége. A szolgáltatás metrikájának konfigurációja azt is tartalmazza, hogy az adott erőforrás mekkora részét használja az adott szolgáltatás állapot-nyilvántartó replikái vagy állapot nélküli példánya alapértelmezés szerint. A metrikák olyan súlyozást is tartalmaznak, amely azt jelzi, hogy milyen fontos az adott szolgáltatás mérőszáma, ha kompromisszumokra van szükség.

## <a name="advanced-placement-rules"></a>Speciális elhelyezési szabályok
Léteznek más típusú elhelyezési szabályok, amelyek kevésbé gyakori helyzetekben hasznosak. Néhány példa:
- A földrajzilag elosztott fürtöket segítő korlátozások
- Bizonyos alkalmazás-architektúrák

Az egyéb elhelyezési szabályok korrelációk vagy szabályzatok használatával konfigurálhatók.

## <a name="next-steps"></a>Következő lépések
- A metrikák azt jelentik, hogyan kezeli a Service Fabric fürterőforrás-kezelő a fürtben a felhasználást és a kapacitást. A metrikákkal és azok konfigurálásával kapcsolatos további tudnivalókért tekintse meg [ezt a cikket](service-fabric-cluster-resource-manager-metrics.md)
- Az affinitás egy olyan mód, amelyet konfigurálhat a szolgáltatásaihoz. Nem gyakori, de ha szüksége van rá, [itt](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) megismerheti
- Számos különböző elhelyezési szabály állítható be a szolgáltatásban további forgatókönyvek kezeléséhez. Ezekről a különböző elhelyezési házirendekről [itt](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) talál további információt
- Kezdje a kezdetektől, és [Ismerkedjen meg a Service Fabric fürterőforrás-kezelővel](service-fabric-cluster-resource-manager-introduction.md)
- Ha szeretné megtudni, hogy a fürterőforrás-kezelő hogyan kezeli és kiegyenlíti a fürt terhelését, tekintse meg a [terhelés kiegyensúlyozásáról](service-fabric-cluster-resource-manager-balancing.md) szóló cikket.
- A fürterőforrás-kezelő számos lehetőséget kínál a fürt leírására. Ha többet szeretne megtudni róluk, tekintse meg ezt a cikket a [Service Fabric-fürt leírását ismertető](service-fabric-cluster-resource-manager-cluster-description.md) cikkben.
