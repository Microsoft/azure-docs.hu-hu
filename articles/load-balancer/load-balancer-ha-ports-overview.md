---
title: Magas rendelkezésre állású portok áttekintése az Azure-ban
titleSuffix: Azure Load Balancer
description: A magas rendelkezésre állású portok terheléselosztásának megismerése belső terheléselosztó esetén.
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/19/2019
ms.author: allensu
ms.openlocfilehash: 6f089af71e4d32023e9cebd6613872f7db0eed7a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94694959"
---
# <a name="high-availability-ports-overview"></a>Magas rendelkezésre állású portok – áttekintés

Az Azure standard Load Balancer lehetővé teszi, hogy minden porton egyidejűleg terheléselosztást hajtson **végre** **minden** porton, ha belső Load Balancert használ a ha portokon keresztül.

A magas rendelkezésre állású (HA) portok olyan terheléselosztási szabályok, amelyek egyszerű módszert biztosítanak a belső standard Load Balancer **összes** portjára **érkező folyamatok** terheléselosztásához. A terheléselosztási döntés egy folyamaton alapul. Ez a művelet a következő öt rekordos kapcsolaton alapul: forrás IP-címe, forrásport, cél IP-címe, célport és protokoll

A HA-portok terheléselosztási szabályai segítenek a kritikus forgatókönyvek, például a magas rendelkezésre állás és a virtuális hálózatokon belüli hálózati virtuális berendezések (NVA-EK) méretezése terén. A funkció akkor is segíthet, ha nagy számú portot kell terheléselosztással elválasztani. 

Ha az előtér-portok terheléselosztási szabályait úgy konfigurálja, hogy az előtérbeli és a háttérbeli portok **0-ra** és az **összes** protokollra legyenek beállítva. A belső terheléselosztó erőforrás ezután kiegyenlíti az összes TCP-és UDP-folyamatot, a portszámtól függetlenül.

## <a name="why-use-ha-ports"></a>Miért érdemes a HA portokat használni?

### <a name="network-virtual-appliances"></a><a name="nva"></a>Hálózati virtuális berendezések

A NVA segítségével az Azure-beli számítási feladatokat több típusú biztonsági fenyegetéssel is biztonságossá teheti. Ha NVA használ ezekben a forgatókönyvekben, megbízhatónak és magasan elérhetőnek kell lenniük, és igény szerint ki kell bővíteni őket.

Ezeket a célokat egyszerűen megteheti, ha NVA-példányokat ad hozzá a belső terheléselosztó háttér-készletéhez, és beállítja a HA ports Load-Balancer szabályt.

NVA esetén a HA-portok a következő előnyöket biztosítják:
- Gyors feladatátvétel biztosítása a példányok állapota alapján
- Nagyobb teljesítmény biztosítása a kibővített és az *n*-aktív példányok között
- *N*-aktív és aktív-passzív forgatókönyvek megadása
- Nincs szükség összetett megoldásokra, például Apache ZooKeeper csomópontokra a figyelési berendezések esetében

A következő ábrán a sugaras virtuális hálózatok üzemelő példánya látható. A küllők kényszeríti a forgalmat a hub virtuális hálózatra és a NVA keresztül a megbízható terület elhagyása előtt. A NVA egy belső standard Load Balancer mögött egy HA portok konfigurációval. Az összes forgalom feldolgozható és továbbítható ennek megfelelően. Ha a következő ábrán látható módon van konfigurálva, akkor egy HA-portok terheléselosztási szabálya emellett a bejövő és a kimenő forgalomhoz is biztosít flow-szimmetriát.

<a node="diagram"></a>
![Sugaras virtuális hálózat ábrája, NVA üzembe helyezése HA](./media/load-balancer-ha-ports-overview/nvaha.png)

>[!NOTE]
> Ha a NVA-t használja, erősítse meg a szolgáltatókat, hogy miként használják a leghatékonyabban a HA-portokat, és hogy milyen forgatókönyvek támogatottak.

### <a name="load-balancing-large-numbers-of-ports"></a>Nagy mennyiségű port terheléselosztása

Ha olyan alkalmazásokhoz is használhat, amelyek nagy mennyiségű port terheléselosztását igénylik. Ezeket a forgatókönyveket belső [standard Load Balancer](./load-balancer-overview.md) és ha portok használatával egyszerűsítheti le. Egyetlen terheléselosztási szabály több egyedi terheléselosztási szabályt cserél le, egyet mindegyik porthoz.

## <a name="region-availability"></a>Régiónkénti elérhetőség

A HA ports szolgáltatás az összes globális Azure-régióban elérhető.

## <a name="supported-configurations"></a>Támogatott konfigurációk

### <a name="a-single-non-floating-ip-non-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Egyetlen, nem lebegőpontos IP-cím (nem közvetlen kiszolgáló által visszaadott) HA-portok konfigurálása belső standard Load Balancer

Ez a konfiguráció egy alapszintű, HA portok konfigurálása. A HA-portok terheléselosztási szabályát egyetlen előtér-IP-címen is konfigurálhatja a következő módon:
1. Standard Load Balancer konfigurálásakor jelölje be a **Ha portok** jelölőnégyzetet az Load Balancer szabály konfigurációjában.
2. A **lebegőpontos IP** esetében válassza a **Letiltva** lehetőséget.

Ez a konfiguráció nem engedélyezi más terheléselosztási szabály konfigurálását az aktuális terheléselosztó-erőforráson. Ezen kívül lehetővé teszi a belső terheléselosztó erőforrás-konfigurációjának további konfigurálását is a háttérbeli példányok adott készlete számára.

Egy nyilvános standard Load Balancer azonban a háttérbeli példányokhoz is konfigurálható a HA-portok szabály mellett.

### <a name="a-single-floating-ip-direct-server-return-ha-ports-configuration-on-an-internal-standard-load-balancer"></a>Egyetlen, lebegőpontos IP-cím (közvetlen kiszolgáló visszaadása), HA-portok konfigurálása belső standard Load Balancer

Hasonlóképpen beállíthatja, hogy a terheléselosztó olyan terheléselosztási szabályt használjon, amely egyetlen előtér- **porttal** rendelkezik, ha a **lebegő IP-címet** **engedélyezve** értékre állítja. 

Ennek a konfigurációnak a használatával további lebegőpontos IP-terheléselosztási szabályokat és/vagy nyilvános Load balancert adhat hozzá. Azonban nem használhat nem lebegőpontos IP-címet, HA a port terheléselosztási konfigurációját ezen a konfiguráción felül.

### <a name="multiple-ha-ports-configurations-on-an-internal-standard-load-balancer"></a>Több HEKTÁRos portos konfiguráció egy belső standard Load Balancer

Ha a forgatókönyve megköveteli, hogy egynél több portot kell konfigurálnia ugyanahhoz a háttér-készlethez, a következőket teheti: 
- Több előtér-magánhálózati IP-cím konfigurálása egyetlen belső standard Load Balancer erőforráshoz.
- Több terheléselosztási szabályt is konfigurálhat, ahol minden egyes szabályhoz egyetlen egyedi előtér-IP-cím van kiválasztva.
- Válassza a **Ha portok** lehetőséget, majd állítsa be a **lebegőpontos IP-címet** **az összes** terheléselosztási szabályhoz.

### <a name="an-internal-load-balancer-with-ha-ports-and-a-public-load-balancer-on-the-same-back-end-instance"></a>Belső terheléselosztó a HA-portokkal és egy nyilvános terheléselosztó ugyanazon a háttér-példányon

*Egy nyilvános standard Load Balancer* erőforrást konfigurálhat a háttérbeli erőforrásokhoz, valamint egyetlen belső standard Load BALANCER, ha portokkal rendelkezik.

## <a name="limitations"></a>Korlátozások

- HA a portok terheléselosztási szabályai csak belső standard Load Balancer esetén érhetők el.
- A HA-portok terheléselosztási szabályának és a nem HEKTÁRos portok terheléselosztási szabályának a kombinációja, amely ugyanarra a háttér-ipconfiguration (ok) ra mutat, **nem** támogatott egyetlen ELŐTÉRI IP-konfigurációban, ha mindkettőn engedélyezve van a lebegőpontos IP-cím.
- A meglévő IP-töredékeket a HEKTÁRos portok terheléselosztási szabályai továbbítják az első csomaggal megegyező célra.  Az UDP-vagy TCP-csomagok IP-darabolása nem támogatott.
- A flow-szimmetria (elsősorban a NVA-forgatókönyvek esetében) a háttér-példány és egyetlen hálózati adapter (és egy IP-konfiguráció) esetében csak akkor támogatott, ha a fenti ábrán látható módon használja, és a HA portok terheléselosztási szabályait használja. Semmilyen más esetben nincs megadva. Ez azt jelenti, hogy két vagy több Load Balancer erőforrás és a hozzájuk tartozó szabályok független döntéseket hoznak, és soha nem koordinálják őket. Tekintse meg a [hálózati virtuális berendezések](#nva)leírását és ábráját. Ha több hálózati adaptert használ, vagy a NVA egy nyilvános és belső Load Balancer között használja, a flow-szimmetria nem érhető el.  Ezt megteheti, ha a forrás NAT'ing a bejövő forgalmat a készülék IP-címére, hogy a válaszok ugyanarra a NVA érkezzenek.  Javasoljuk azonban, hogy egyetlen hálózati adaptert használjon, és használja a fenti ábrán látható hivatkozási architektúrát.


## <a name="next-steps"></a>Következő lépések

- [Tudnivalók a standard Load Balancer](load-balancer-overview.md)
