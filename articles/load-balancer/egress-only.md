---
title: Csak kifelé irányuló terheléselosztó konfigurációja
titleSuffix: Azure Load Balancer
description: Ebből a cikkből megtudhatja, hogyan hozható létre belső terheléselosztó kimenő NAT-vel
services: load-balancer
documentationcenter: na
author: asudbring
ms.custom: seodec18
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/07/2020
ms.author: allensu
ms.openlocfilehash: ee264a22de5ce094e8a4c1335ace77cbbba49270
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "94694993"
---
# <a name="outbound-only-load-balancer-configuration"></a>Csak kifelé irányuló terheléselosztó konfigurációja

Belső és külső standard Load balancerek együttes használata a belső terheléselosztó mögötti virtuális gépek kimenő kapcsolatának létrehozásához. 

Ez a konfiguráció egy belső terheléselosztási forgatókönyvhöz biztosít kimenő NAT-t, amely a háttér-készlet "csak kimenő" beállítását állítja elő.


<p align="center">
  <img src="./media/egress-only/load-balancer-egress-only.png" alt="Figure depicts a egress only load balancer configuration." width="500" title="Virtual Network NAT">
</p>


<!-- 
:::image type="content" source="./media/egress-only/load-balancer-egress-only.png" alt-text="Figure depicts a egress only load balancer configuration" border="true":::
-->


*Ábra: a kimenő forgalom csak a terheléselosztó konfigurálása*

A szükséges lépések a következők:

1. Hozzon létre egy megerősített gazdagéptel rendelkező virtuális hálózatot.
2. Hozzon létre egy virtuális gépet, amely csak privát IP-címmel rendelkezik.
3. Hozzon létre mind a belső, mind a nyilvános standard Load balancert.
4. Adja hozzá a háttér-készleteket a terheléselosztóhoz, és helyezze a virtuális gépet az egyes készletbe.
5. Kapcsolódjon a virtuális géphez a megerősített gazdagépen keresztül, és:
    1. Kimenő kapcsolat tesztelése, 
    2. Konfiguráljon egy kimenő szabályt a nyilvános terheléselosztó számára.
    3. A kimenő kapcsolat újratesztelése.

## <a name="create-virtual-network-and-virtual-machine"></a>Virtuális hálózat és virtuális gép létrehozása

Hozzon létre egy virtuális hálózatot két alhálózattal:

* Elsődleges alhálózat
* Megerősített alhálózat

Hozzon létre egy virtuális gépet az új virtuális hálózaton.

### <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

2. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | Válassza az **Új létrehozása** lehetőséget. </br> Adja meg a **myresourcegrouplb erőforráscsoportban**. </br> Válassza az **OK** lehetőséget. |
    | **Példány adatai** |                                                                 |
    | Name             | **MyVNet** megadása                                    |
    | Region           | Válassza az **USA 2. keleti** régióját |

3. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

4. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címtartomány | Adja meg a **10.1.0.0/16** értéket |

5. Az **alhálózat neve** alatt válassza ki az **alapértelmezett** szót.

6. Az **alhálózat szerkesztése** területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | **MyBackendSubnet** megadása |
    | Alhálózati címtartomány | Adja meg a **10.1.0.0/24** értéket |

7. Kattintson a **Mentés** gombra.

8. Válassza a **Biztonság** fület.

9. A **BastionHost** területen válassza az **Engedélyezés** lehetőséget. Adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bástya neve | **MyBastionHost** megadása |
    | AzureBastionSubnet címterület | Adja meg a **10.1.1.0/24** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név** mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |


8. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

9. Válassza a **Létrehozás** lehetőséget.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget. 
   
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **Myresourcegrouplb erőforráscsoportban** kiválasztása |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM** megadása |
    | Region | Válassza az **USA 2. keleti** régióját |
    | Rendelkezésre állási beállítások | Válassza az **infrastruktúra-redundancia nem szükséges** lehetőséget |
    | Kép | **Windows Server 2019 Datacenter** kiválasztása |
    | Azure Spot-példány | Válassza a **nem** lehetőséget |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |
    | **Bejövő portok szabályai** |  |
    | Nyilvános bejövő portok | Válassza ki a **kijelölt portok engedélyezése** lehetőséget |
    | Válassza ki a bejövő portokat | **RDP kiválasztása (3389)** |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-----|------------|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | **myVNet** |
    | Alhálózat | **myBackendSubnet** |
    | Nyilvános IP-cím | Válassza a **Nincs** lehetőséget. |
    | NIC hálózati biztonsági csoport | Válassza a **nincs** lehetőséget|
    | Elhelyezi ezt a virtuális gépet egy meglévő terheléselosztási megoldás mögött? | Válassza a **nem** lehetőséget |
   
5. Válassza a **kezelés** lapot, vagy válassza a **következő**  >  **kezelés** lehetőséget.

6. A **kezelés** lapon válassza ki vagy írja be a következőket:
    
    | Beállítás | Érték |
    |-|-|
    | **Figyelés** |  |
    | Rendszerindítási diagnosztika | Kijelölés **kikapcsolva** |
   
7. Válassza az **Áttekintés + létrehozás** lehetőséget. 
  
8. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

## <a name="create-load-balancers-and-test-connectivity"></a>Terheléselosztó létrehozása és a kapcsolat tesztelése

A létrehozásához használja a Azure Portal:

* Belső terheléselosztó
* Nyilvános Load Balancer
 
Adja hozzá a létrehozott virtuális gépet a háttérrendszer-készlethez.  Ezután beállíthat egy olyan konfigurációt, amely csak a virtuális gép kimenő kapcsolatait engedélyezi, majd teszteli az előtt és után.

### <a name="create-internal-load-balancer"></a>Belső terheléselosztó létrehozása

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer** elemet.

2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza ki az előző lépésben létrehozott **myresourcegrouplb erőforráscsoportban** .|
    | Name                   | **MyInternalLoadBalancer** megadása                                   |
    | Region         | Válassza az **USA 2. keleti régiója** lehetőséget.                                        |
    | Típus          | Válassza a **belső** lehetőséget.                                        |
    | Termékváltozat           | **Standard** kiválasztása |
    | Virtuális hálózat | Válassza ki az előző lépésben létrehozott **myVNet** . |
    | Alhálózat  | Válassza ki az előző lépésben létrehozott **myBackendSubnet** . |
    | IP-cím hozzárendelése | Válassza a **dinamikus** lehetőséget. |

3. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás** elemet.

4. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget.   

### <a name="create-public-load-balancer"></a>Nyilvános Load Balancer létrehozása

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer** elemet.

2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az **új létrehozása** elemet, és írja be a **myresourcegrouplb erőforráscsoportban** szöveget a szövegmezőbe.|
    | Name                   | **MyPublicLoadBalancer** megadása                                   |
    | Region         | Válassza az **USA 2. keleti régiója** lehetőséget.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | Termékváltozat           | **Standard** kiválasztása |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. |
    | Nyilvános IP-cím | Írja be a **myFrontendIP** szöveget a szövegmezőbe.|
    | A rendelkezésre állási zóna | **Zóna kiválasztása – redundáns** |
    | A nyilvános IPv6-cím hozzáadása | Válassza a **Nem** lehetőséget. |

3. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás** elemet.

4. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget.   
   
### <a name="create-internal-backend-address-pool"></a>Belső háttérbeli címkészlet létrehozása

A háttérbeli címkészlet **myInternalBackendPool** létrehozása:

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myInternalLoadBalancer** lehetőséget.

2. A **Beállítások** területen válassza a **háttér-készletek**, majd a **Hozzáadás** lehetőséget.

3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a **myInternalBackendPool** nevet a háttérbeli készlet neveként.
 
4. A **virtuális hálózat** területen válassza a **myVNet** lehetőséget.

5. A **virtuális gépek** területen válassza a **Hozzáadás** lehetőséget, majd válassza a **myVM** lehetőséget.
 
6. Válassza a **Hozzáadás** lehetőséget.

### <a name="create-public-backend-address-pool"></a>Nyilvános háttérrendszer-címkészlet létrehozása

A háttérbeli címkészlet **myPublicBackendPool** létrehozása:

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myPublicLoadBalancer** lehetőséget.

2. A **Beállítások** területen válassza a **háttér-készletek**, majd a **Hozzáadás** lehetőséget.

3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a **myPublicBackendPool** nevet a háttérbeli készlet neveként.

4. A **virtuális hálózat** területen válassza a **myVNet** lehetőséget.
 
5. A **virtuális gépek** területen válassza a **Hozzáadás** lehetőséget, majd válassza a **myVM** lehetőséget.
 
6. Válassza a **Hozzáadás** lehetőséget.

### <a name="test-connectivity-before-outbound-rule"></a>Kapcsolat tesztelése a Kimenő szabály előtt

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza ki a **myVM** , amely a **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz található.

2. Az **Áttekintés** lapon válassza a **kapcsolat**, majd a **Bastion** lehetőséget.

4. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

5. Válassza a **Kapcsolódás** lehetőséget.

6. Nyissa meg az Internet Explorert.

7. Írja be **https://whatsmyip.org** a címsorba.

8. A kapcsolatok sikertelenek lesznek. Alapértelmezés szerint a standard nyilvános Load Balancer [nem engedélyezi a kimenő forgalmat egy meghatározott kimenő szabály nélkül](load-balancer-overview.md#securebydefault).
 
### <a name="create-a-public-load-balancer-outbound-rule"></a>Nyilvános terheléselosztó kimenő szabályának létrehozása

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myPublicLoadBalancer** lehetőséget.

2. A **Beállítások** területen válassza a **Kimenő szabályok** elemet, majd kattintson a **Hozzáadás** gombra.

3. A kimenő szabályok konfigurálásához használja ezeket az értékeket:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **myOutboundRule**. |
    | Előtérbeli IP-cím | Válassza a **LoadBalancerFrontEnd** lehetőséget.|
    | Üresjárati időkorlát (perc) | Mozgassa a csúszkát **15 percre**.|
    | TCP alaphelyzetbe állítása | Válassza az **Engedélyezve** lehetőséget.|
    | A háttérkészlet | Válassza a **myPublicBackendPool** lehetőséget.| |
    | Port kiosztása – > a portok kiosztása | Válassza **a kimenő portok alapértelmezett számának használata** lehetőséget. |

4. Válassza a **Hozzáadás** lehetőséget.

### <a name="test-connectivity-after-outbound-rule"></a>Kapcsolat tesztelése a Kimenő szabály után

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza ki a **myVM** , amely a **myresourcegrouplb erőforráscsoportban** erőforráscsoporthoz található.

2. Az **Áttekintés** lapon válassza a **kapcsolat**, majd a **Bastion** lehetőséget.

4. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

5. Válassza a **Kapcsolódás** lehetőséget.

6. Nyissa meg az Internet Explorert.

7. Írja be **https://whatsmyip.org** a címsorba.

8. A csatlakoztatásnak sikeresnek kell lennie.

9. A megjelenített IP-címnek a **myPublicLoadBalancer** előtér-IP-címének kell lennie.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rá szükség, törölje az erőforráscsoportot, a terheléselosztó, a virtuális gép és az összes kapcsolódó erőforrást. 

Ehhez válassza ki az erőforráscsoport **myresourcegrouplb erőforráscsoportban** , majd válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozta a "kimenő forgalom" konfigurációt a nyilvános és belső terheléselosztó kombinációjával.  

Ez a konfiguráció lehetővé teszi a háttér-készletbe való bejövő belső forgalom terheléselosztását, miközben továbbra is megakadályozza a nyilvános bejövő kapcsolatokat.

- A [Azure Load Balancer](load-balancer-overview.md)megismerése.
- [Az Azure kimenő kapcsolatainak](load-balancer-outbound-connections.md)megismerése.
- Load Balancer – [Gyakori kérdések](load-balancer-faqs.md).
- Az [Azure Bastion](../bastion/bastion-overview.md) megismerése