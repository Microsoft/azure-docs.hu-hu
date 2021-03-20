---
title: Több frontend – Azure Load Balancer
description: Ez a képzési terv az első lépéseket mutatja be a Azure Load Balancer több előtér-felületének áttekintésével
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.custom: seodec18
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2019
ms.author: allensu
ms.openlocfilehash: 5c2072d13cab9839a276c0437747d7075918e78a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "94696880"
---
# <a name="multiple-frontends-for-azure-load-balancer"></a>Több előtér Azure Load Balancer

Azure Load Balancer lehetővé teszi, hogy terheléselosztási szolgáltatásokat több porton, több IP-címen vagy mindkettőn. A nyilvános és a belső terheléselosztó-definíciók használatával terheléselosztási folyamatokat helyezhet át a virtuális gépek egy csoportján belül.

Ez a cikk ismerteti a képesség alapjait, a fontos fogalmakat és a megkötéseket. Ha csak egyetlen IP-címen kívánja közzétenni a szolgáltatásokat, a [nyilvános](./quickstart-load-balancer-standard-public-portal.md) vagy [belső](./quickstart-load-balancer-standard-internal-portal.md) terheléselosztó-konfigurációk egyszerűsített utasításait találhatja meg. Több előtér hozzáadása egyetlen előtér-konfigurációhoz. A cikkben szereplő fogalmak használatával bármikor kiterjesztheti az egyszerűsített konfigurációt.

Azure Load Balancer definiálásakor a rendszer egy előtér-és egy háttér-készlet konfigurációját is csatlakoztatja a szabályokhoz. A szabály által hivatkozott állapot-mintavételi módszer segítségével határozható meg, hogy a rendszer hogyan küldje el az új folyamatokat a háttér-készlet egyik csomópontjára. A frontend (más néven VIP) egy 3 rekordból áll, amely egy IP-cím (nyilvános vagy belső), egy átviteli protokoll (UDP vagy TCP) és egy portszám a terheléselosztási szabályból. A háttér-készlet a virtuális gépek IP-konfigurációinak gyűjteménye (a NIC-erőforrás része), amely a Load Balancer háttér-készletre hivatkozik.

A következő táblázat néhány példát tartalmaz a frontend-konfigurációkra:

| Előtér | IP-cím | protokoll | port |
| --- | --- | --- | --- |
| 1 |65.52.0.1 |TCP |80 |
| 2 |65.52.0.1 |TCP |*8080* |
| 3 |65.52.0.1 |*UDP* |80 |
| 4 |*65.52.0.2* |TCP |80 |

A táblázat négy különböző előtérbeli felületet mutat be. A #1, #2 és #3 frontendek egyetlen, több szabállyal rendelkező előtéri felületet is kapnak. Ugyanazt az IP-címet használja a rendszer, de a port vagy protokoll különbözik az egyes előtérben. A #1 és #4 előtérben például több előtér-felület található, ahol ugyanazt a frontend protokollt és portot használja a rendszer több előtér-felületre.

Azure Load Balancer rugalmasságot biztosít a terheléselosztási szabályok definiálásához. A szabály azt deklarálja, hogy a felület egy címe és portja a háttérbeli célcím és port számára van leképezve. Annak megadása, hogy a háttérbeli portok újra felhasználva vannak-e a szabályok között, a szabály típusától függ. Minden egyes szabályhoz konkrét követelmények vonatkoznak, amelyek befolyásolhatják a gazdagép konfigurációját és a mintavételi terveket. A szabályoknak két típusa van:

1. Az alapértelmezett szabály, amely nem rendelkezik háttér-port újrafelhasználásával
2. Az a lebegő IP-szabály, amelyben a háttérbeli portok újra fel lesznek használva

Azure Load Balancer lehetővé teszi mindkét szabálytípus összekeverését ugyanazon a terheléselosztó-konfiguráción. A terheléselosztó egyszerre használhatja őket egy adott virtuális géphez vagy bármely kombinációhoz, ha a szabály korlátai betartanak. A választott szabálytípus az alkalmazás követelményeitől és a konfiguráció támogatásának összetettségtől függ. Érdemes kiértékelni, hogy milyen típusú szabályok a legmegfelelőbbek a forgatókönyvhöz.

Ezeket a forgatókönyveket tovább vizsgálja az alapértelmezett viselkedéstől kezdve.

## <a name="rule-type-1-no-backend-port-reuse"></a>Szabály típusa #1: nincs háttér-port újrahasznosítása

![Több előtér-illusztráció zöld és lila felülettel](./media/load-balancer-multivip-overview/load-balancer-multivip.png)

Ebben az esetben a frontendek konfigurálása a következőképpen történik:

| Előtér | IP-cím | protokoll | port |
| --- | --- | --- | --- |
| ![zöld frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila felület](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

A DIP a bejövő folyamat célja. A háttér-készletben minden virtuális gép egy DIP-on lévő egyedi porton teszi elérhetővé a kívánt szolgáltatást. Ez a szolgáltatás egy szabály definícióján keresztül van társítva a frontendhez.

Két szabályt definiálunk:

| Szabály | Térkép előtér | Háttérbeli készlet |
| --- | --- | --- |
| 1 |![zöld frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![zöld háttérrendszer](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP1:80, ![zöld háttérrendszer](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) DIP2 EGYSÉGEK: 80 |
| 2 |![VIP](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![lila háttérrendszer](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP1:81, ![lila háttérrendszer](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) DIP2 EGYSÉGEK: 81 |

A Azure Load Balancer teljes leképezése mostantól a következő:

| Szabály | Előtérbeli IP-cím | protokoll | port | Cél | port |
| --- | --- | --- | --- | --- | --- |
| ![zöld szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |DIP IP-címe |80 |
| ![lila szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |DIP IP-címe |81 |

Minden szabálynak egy olyan folyamatot kell létrehoznia, amely a cél IP-cím és a célport egyedi kombinációjával rendelkezik. A folyamat rendeltetési portjának megváltoztatásával több szabály is továbbíthat folyamatokat ugyanazon a DIP-on különböző portokon.

Az állapot-mintavételek mindig egy virtuális gép bemerítésére vannak irányítva. Gondoskodnia kell arról, hogy a mintavétel a virtuális gép állapotát tükrözze.

## <a name="rule-type-2-backend-port-reuse-by-using-floating-ip"></a>Szabály típusa #2: háttérbeli port újrafelhasználása lebegőpontos IP használatával

Azure Load Balancer rugalmasságot biztosít a frontend-port több előtérben történő újrafelhasználásához, a használt szabálytípustől függetlenül. Emellett egyes alkalmazási forgatókönyvek előnyben részesítettek, vagy megkövetelik, hogy ugyanazt a portot több alkalmazás-példány is használja egyetlen virtuális gépen a háttér-készletben. A portok használatának gyakori példái közé tartozik a fürtözés a magas rendelkezésre álláshoz, a hálózati virtuális berendezések, valamint a több TLS-végpont ismételt titkosítás nélküli kimutatása.

Ha több szabályban szeretné felhasználni a háttér-portot, akkor a szabály definíciójában engedélyeznie kell a lebegőpontos IP-címet.

A "Floating IP" az Azure terminológiai része, amely a Direct Server Return (DSR) néven ismert részét képezi. A DSR két részből áll: egy folyamat topológiából és egy IP-cím-hozzárendelési sémából. A platform szintjén Azure Load Balancer mindig a DSR-folyamat topológiájában működik, függetlenül attól, hogy engedélyezve van-e a lebegőpontos IP-cím. Ez azt jelenti, hogy a folyamat kimenő részét mindig közvetlenül a forráshoz kell visszaírni a folyamatba.

Az alapértelmezett szabálytípus esetében az Azure egy hagyományos terheléselosztási IP-cím-hozzárendelési sémát tesz elérhetővé a könnyű használat érdekében. A lebegő IP-címek engedélyezése az IP-cím-hozzárendelési séma lehetővé teszi a további rugalmasságot az alább leírtak szerint.

A következő ábra szemlélteti ezt a konfigurációt:

![Több előtér-illusztráció zöld és lila felülettel a DSR-vel](./media/load-balancer-multivip-overview/load-balancer-multivip-dsr.png)

Ebben az esetben a háttér-készlet minden virtuális gépe három hálózati csatolóval rendelkezik:

* DIP: a virtuális GÉPHEZ társított virtuális hálózati adapter (az Azure NIC-erőforrás IP-konfigurációja)
* 1. frontend: a vendég operációs rendszeren belüli visszacsatolási felület, amely az 1. előtér IP-címével van konfigurálva
* 2. frontend: a vendég operációs rendszeren belüli, a 2. előtér IP-címével konfigurált visszacsatolási felület

A háttér-készletben lévő minden egyes virtuális géphez futtassa a következő parancsokat egy Windows-parancssorban.

A virtuális gépen található interfész-nevek listájának lekéréséhez írja be a következő parancsot:

```console
netsh interface show interface 
```

A VM NIC (Azure Managed) esetében írja be a következő parancsot:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled
```

(a InterfaceName helyére írja be az interfész nevét)

Az egyes hozzáadott visszacsatolási felületek esetében ismételje meg a következő parancsokat:

```console
netsh interface ipv4 set interface “interfacename” weakhostreceive=enabled 
```

(cserélje le a InterfaceName-t a visszacsatolási felület nevére)

```console
netsh interface ipv4 set interface “interfacename” weakhostsend=enabled 
```

(cserélje le a InterfaceName-t a visszacsatolási felület nevére)

> [!IMPORTANT]
> A visszacsatolási felületek konfigurációja a vendég operációs rendszeren belül történik. Ezt a konfigurációt az Azure nem végzi el vagy nem kezeli. Ezen konfiguráció nélkül a szabályok nem fognak működni. Az állapot-mintavételi definíciók a virtuális gép DIP felületét használják a DSR-felületet jelképező visszacsatolási interfész helyett. Ezért a szolgáltatásnak mintavételi válaszokat kell adnia egy olyan DIP-porton, amely tükrözi a DSR-felületet jelképező visszacsatolási felületen kínált szolgáltatás állapotát.


Tegyük fel, hogy ugyanazt a frontend-konfigurációt feltételezzük, mint az előző forgatókönyvben:

| Előtér | IP-cím | protokoll | port |
| --- | --- | --- | --- |
| ![zöld frontend](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |
| ![lila felület](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |*65.52.0.2* |TCP |80 |

Két szabályt definiálunk:

| Szabály | Előtér | Leképezés a háttérrendszer-készletre |
| --- | --- | --- |
| 1 |![zöld szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 |![zöld háttérrendszer](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) Frontend1:80 (a VM1 és a VM2) |
| 2 |![lila szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 |![lila háttérrendszer](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) Frontend2:80 (a VM1 és a VM2) |

A következő táblázat a terheléselosztó teljes leképezését tartalmazza:

| Szabály | Előtérbeli IP-cím | protokoll | port | Cél | port |
| --- | --- | --- | --- | --- | --- |
| ![zöld szabály](./media/load-balancer-multivip-overview/load-balancer-rule-green.png) 1 |65.52.0.1 |TCP |80 |ugyanaz, mint a frontend (65.52.0.1) |ugyanaz, mint a frontend (80) |
| ![lila szabály](./media/load-balancer-multivip-overview/load-balancer-rule-purple.png) 2 |65.52.0.2 |TCP |80 |ugyanaz, mint a frontend (65.52.0.2) |ugyanaz, mint a frontend (80) |

A bejövő folyamat célja a virtuális gép visszacsatolási felületének IP-címe. Minden szabálynak egy olyan folyamatot kell létrehoznia, amely a cél IP-cím és a célport egyedi kombinációjával rendelkezik. A folyamat cél IP-címének megváltoztatásával a portok újrafelhasználása ugyanazon a virtuális gépen lehetséges. A szolgáltatás a terheléselosztó számára elérhetővé válik úgy, hogy a megfelelő visszacsatolási felület IP-címéhez és portjához köti.

Figyelje meg, hogy ez a példa nem változtatja meg a célként megadott portot. Annak ellenére, hogy ez egy lebegő IP-forgatókönyv, Azure Load Balancer is támogatja a szabályok definiálását a háttérbeli célport újraírásához, és annak eltérővé tételéhez a frontend célport.

A lebegőpontos IP-szabály típusa több terheléselosztó-konfigurációs minta alapja. A jelenleg elérhető egyik példa a [több figyelővel rendelkező SQL-AlwaysOn](../azure-sql/virtual-machines/windows/availability-group-listener-powershell-configure.md) . Az idő múlásával több ilyen forgatókönyvet fogunk dokumentálni.

## <a name="limitations"></a>Korlátozások

* Több előtér-konfiguráció csak IaaS virtuális gépek esetén támogatott.
* A lebegőpontos IP-szabállyal az alkalmazásnak a kimenő SNAT-folyamatok elsődleges IP-konfigurációját kell használnia. Ha az alkalmazás a vendég operációs rendszerben a visszacsatolási felületen konfigurált előtérbeli IP-címhez van kötve, az Azure kimenő SNAT nem érhető el a kimenő folyamat újraírásához, és a folyamat meghiúsul.  Tekintse át a [kimenő forgatókönyveket](load-balancer-outbound-connections.md).
* A belső terheléselosztási forgatókönyvek esetében jelenleg nem támogatott a lebegőpontos IP-cím a másodlagos IP-konfigurációk esetében.
* A nyilvános IP-címek a számlázásra érvényesek. További információt az [IP-címek díjszabása](https://azure.microsoft.com/pricing/details/ip-addresses/) című témakörben talál.
* Az előfizetés korlátai érvényesek. További információ: [szolgáltatási korlátozások](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) a részletekért.

## <a name="next-steps"></a>Következő lépések

- Tekintse át a [kimenő kapcsolatokat](load-balancer-outbound-connections.md) , hogy megértse, milyen hatással van több előtér a kimenő kapcsolat viselkedésére.