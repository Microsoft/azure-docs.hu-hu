---
title: Az Azure Service Fabric hálózatkezelés bemutatása
description: Ismerje meg a hálózatokat, az átjárókat és az intelligens forgalom útválasztását Service Fabric Meshban.
author: georgewallace
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: ef8b40be366afe24b960f7419d84cc2a9976b592
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "99625701"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Service Fabric Mesh-alkalmazások hálózatkezelésének bemutatása

> [!IMPORTANT]
> Az Azure Service Fabric Mesh előzetes verziója ki lett vonva. Az új központi telepítések többé nem lesznek engedélyezve a Service Fabric Mesh API-n keresztül. A meglévő központi telepítések támogatása a 2021. április 28. után folytatódik.
> 
> Részletekért lásd: az [Azure Service Fabric Mesh előzetes verziójának nyugdíjazása](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Ez a cikk a terheléselosztó különböző típusait ismerteti, az átjárók hogyan kötik össze a hálózatot az alkalmazásaival más hálózatokkal, valamint azt, hogy a forgalom hogyan legyen átirányítva az alkalmazások szolgáltatásai között.

## <a name="layer-4-vs-layer-7-load-balancers"></a>4. réteg vs 7. rétegbeli terheléselosztó
A terheléselosztás az [OSI-modell](https://en.wikipedia.org/wiki/OSI_model) különböző rétegein végezhető el a hálózatkezeléshez, gyakran a 4. rétegben (L4) és a 7. rétegben (L7).  Jellemzően két típusú terheléselosztó létezik:

- Az L4 terheléselosztó a hálózatkezelési átviteli rétegen működik, amely a csomagok tartalmának figyelmen kívül hagyásával a csomagok szállításával foglalkozik. A terheléselosztó csak a csomagok fejléceit ellenőrzi, így az egyenlegező feltételek az IP-címekre és a portokra korlátozódnak. Egy ügyfél például TCP-kapcsolatban áll a terheléselosztó használatával. A terheléselosztó leállítja a kapcsolódást (közvetlenül a SYN-re válaszol), kiválasztja a hátteret, és új TCP-kapcsolódást végez a háttérrel (új SYN-t küld). Az L4 Load Balancer általában csak a L4 TCP/UDP kapcsolat vagy munkamenet szintjén működik. Így a terheléselosztás átirányítja a bájtok körét, és gondoskodik arról, hogy az azonos munkamenetből származó bájtok ugyanazon a háttérön legyenek. Az L4 Load Balancer nem ismeri az áthelyezett bájtok összes alkalmazási részletét. A bájtok bármely alkalmazás-protokoll lehetnek.

- Egy L7 terheléselosztó az alkalmazás rétegében működik, amely az egyes csomagok tartalmával foglalkozik. Megvizsgálja a csomagok tartalmát, mert megérti a protokollokat, például a HTTP-, HTTPS-vagy WebSocket-csomagokat. Ez biztosítja, hogy a terheléselosztó képes legyen speciális útválasztást végezni. Az ügyfél például egyetlen HTTP/2 TCP-kapcsolódást tesz lehetővé a terheléselosztó számára. A terheléselosztó ezután két háttérbeli kapcsolatot hajt végre. Amikor az ügyfél két HTTP/2 streamet küld a terheléselosztó felé, a streamet a rendszer a háttérbe küldi, a két stream pedig a két háttérbe kerül. Így a nagymértékben eltérő kérelmeket használó ügyfeleket még a háttérben is hatékonyan kiegyensúlyozottan fogja biztosítani. 

## <a name="networks-and-gateways"></a>Hálózatok és átjárók
A [Service Fabric erőforrás-modellben](service-fabric-mesh-service-fabric-resources.md)a hálózati erőforrás egy önállóan telepíthető erőforrás, amely független az alkalmazás-vagy szolgáltatási erőforrástól, amely függőségként hivatkozik rá. A rendszer az internetre nyitott alkalmazások hálózatának létrehozására szolgál. A különböző alkalmazásokból több szolgáltatás is lehet ugyanazon hálózat része. Ezt a magánhálózatot a Service Fabric hozza létre és kezeli, és nem Azure-beli virtuális hálózat (VNET). Az alkalmazások dinamikusan hozzáadhatók és eltávolíthatók a hálózati erőforrásból a VNET-kapcsolat engedélyezéséhez és letiltásához. 

Az átjáró két hálózat összeépítésére szolgál. Az átjáró erőforrása olyan [meghatalmazotti proxyt](https://www.envoyproxy.io/) helyez üzembe, amely L4 útválasztást biztosít bármely protokoll-és L7-útválasztáshoz a speciális http (S) alkalmazás-útválasztáshoz. Az átjáró egy külső hálózatról irányítja át a forgalmat a hálózatra, és meghatározza, hogy melyik szolgáltatás irányítja át a forgalmat.  A külső hálózat lehet egy nyílt hálózat (lényegében a nyilvános Internet) vagy egy Azure-beli virtuális hálózat, amely lehetővé teszi a más Azure-alkalmazásokhoz és-erőforrásokhoz való kapcsolódást. 

![Hálózat és átjáró][Image1]

A hálózati erőforrás létrehozásakor a rendszer `ingressConfig` egy nyilvános IP-címet rendel hozzá a hálózati erőforráshoz. A nyilvános IP-cím a hálózati erőforrás élettartamához lesz kötve.

Egy rácsvonal-alkalmazás létrehozásakor egy meglévő hálózati erőforrásra kell hivatkoznia. Új nyilvános portok adhatók hozzá, vagy a meglévő portok eltávolíthatók a bejövő konfigurációból. Egy hálózati erőforrás törlése sikertelen lesz, ha egy alkalmazás-erőforrás hivatkozik rá. Az alkalmazás törlésekor a hálózati erőforrás el lesz távolítva.

## <a name="next-steps"></a>Következő lépések 
Ha többet szeretne megtudni a Service Fabric Meshról, olvassa el az áttekintést:
- [Service Fabric Mesh – áttekintés](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png