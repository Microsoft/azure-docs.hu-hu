---
title: Rövid útmutató – privát hivatkozási szolgáltatás létrehozása a Azure Portal használatával
titlesuffix: Azure Private Link
description: Megtudhatja, hogyan hozhat létre privát hivatkozási szolgáltatást a rövid útmutató Azure Portal használatával
services: private-link
author: asudbring
ms.service: private-link
ms.topic: quickstart
ms.date: 01/18/2021
ms.author: allensu
ms.openlocfilehash: d394a475c5121607f70c03437382e104a5d0cbee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "98746407"
---
# <a name="quickstart-create-a-private-link-service-by-using-the-azure-portal"></a>Rövid útmutató: privát link szolgáltatás létrehozása a Azure Portal használatával

Ismerkedjen meg a szolgáltatásra hivatkozó privát link szolgáltatás létrehozásával.  Adja meg az Azure-standard Load Balancer mögött üzembe helyezett szolgáltatáshoz vagy erőforráshoz való magánhálózati hivatkozást.  A szolgáltatás felhasználói a virtuális hálózatról privát hozzáféréssel rendelkeznek.

## <a name="prerequisites"></a>Előfeltételek

* Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.

## <a name="create-an-internal-load-balancer"></a>Hozzon létre egy belső terheléselosztót

Ebben a szakaszban egy virtuális hálózatot és egy belső Azure Load Balancer hoz létre.

### <a name="virtual-network"></a>Virtuális hálózat

Ebben a szakaszban egy virtuális hálózatot és alhálózatot hoz létre a privát kapcsolati szolgáltatáshoz hozzáférő terheléselosztó üzemeltetéséhez.

1. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

2. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | **CreatePrivLinkService kiválasztása – RG** |
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
    | Alhálózat neve | **MySubnet** megadása |
    | Alhálózati címtartomány | Adja meg a **10.1.0.0/24** értéket |

7. Kattintson a **Mentés** gombra.

8. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

9. Válassza a **Létrehozás** lehetőséget.

### <a name="create-a-standard-load-balancer"></a>Standard terheléselosztó létrehozása

A portál használatával hozzon létre egy standard belső terheléselosztó. 

1. A képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózatkezelés**  >  **Load Balancer** elemet.

2. A **Load Balancer létrehozása** lap **alapok** lapján adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza az előző lépésben létrehozott **CreatePrivLinkService-RG** elemet.|
    | Name                   | **MyLoadBalancer** megadása                                   |
    | Region         | Válassza az **USA 2. keleti régiója** lehetőséget.                                        |
    | Típus          | Válassza a **belső** lehetőséget.                                        |
    | Termékváltozat           | **Standard** kiválasztása |
    | Virtuális hálózat | Válassza ki az előző lépésben létrehozott **myVNet** . |
    | Alhálózat  | Válassza ki az előző lépésben létrehozott **mySubnet** . |
    | IP-cím hozzárendelése | Válassza a **dinamikus** lehetőséget. |
    | A rendelkezésre állási zóna | **Zóna kiválasztása – redundáns** |

3. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás** elemet.

4. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget.   

## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a következőket konfigurálja:

* Egy háttérbeli címkészlet Load Balancer-beállításai.
* Egy állapot-mintavétel.
* Egy terheléselosztó-szabály.

### <a name="create-a-backend-pool"></a>Háttérkészlet létrehozása

A háttér-címkészlet tartalmazza a terheléselosztó számára csatlakoztatott virtuális (NIC) IP-címeit. 

Hozza létre a háttér-címkészlet **myBackendPool** , hogy a virtuális gépeket a terheléselosztási internetes forgalom számára is tartalmazza.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások** területen válassza a **háttér-készletek**, majd a **Hozzáadás** lehetőséget.

3. A **háttérbeli készlet hozzáadása** lapon a név mezőbe írja be a **myBackendPool** nevet a háttérbeli készlet neveként, majd válassza a **Hozzáadás** lehetőséget.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

A terheléselosztó az alkalmazás állapotát állapottal figyeli. 

Az állapot mintavétele a terheléselosztó által az állapot-ellenőrzésekre adott válasz alapján hozzáadja vagy eltávolítja a virtuális gépeket. 

Hozzon létre egy **myHealthProbe** nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások** területen válassza az **állapot**-mintavételek, majd a **Hozzáadás** lehetőséget.
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **myHealthProbe**. |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Port | Adja meg a **80** értéket.|
    | Időköz | A mintavételi kísérletek közötti időtartam másodpercben megadott számú **intervallumában** adja meg a **15** értéket. |
    | Nem kifogástalan állapot küszöbértéke | Válassza a **2** értéket a nem megfelelő **állapotú küszöbértékek** vagy egymást követő mintavételi hibák számának megadásához, amelyeknek a virtuális gép nem megfelelő állapotba kell kerülnie.|
    | | |

3. Hagyja meg a többi alapértelmezett értéket, majd kattintson az **OK gombra**.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A bejövő forgalom és a háttérbeli IP-készlet előtérbeli IP-konfigurációját a forgalom fogadásához adja meg. A forrás-és a célport a szabályban van definiálva. 

Ebben a szakaszban egy terheléselosztó-szabályt fog létrehozni:

* Elnevezett **: myhttprule**.
* A **LoadBalancerFrontEnd** nevű előtérben.
* Figyelés a **80-es porton**.
* Az elosztott terhelésű forgalmat a 80-es **porton** található **myBackendPool** nevű háttérre irányítja.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások** területen válassza a **terheléselosztási szabályok** elemet, majd kattintson a **Hozzáadás** gombra.

3. Használja ezeket az értékeket a terheléselosztási szabály konfigurálásához:
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **: myhttprule**. |
    | IP-verzió | **IPv4** kiválasztása |
    | Előtérbeli IP-cím | **LoadBalancerFrontEnd** kiválasztása |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Port | Adja meg a **80** értéket.|
    | Háttérport | Adja meg a **80** értéket. |
    | A háttérkészlet | Válassza a **myBackendPool** lehetőséget.|
    | Állapotadat-mintavétel | Válassza a **myHealthProbe** lehetőséget. |
    | Üresjárati időkorlát (perc) | Mozgassa a csúszkát **15** percre. |
    | TCP alaphelyzetbe állítása | Válassza az **Engedélyezve** lehetőséget. |

4. Hagyja meg a többi alapértelmezett beállítást, majd kattintson az **OK gombra**.

## <a name="create-a-private-link-service"></a>Privát kapcsolati szolgáltatás létrehozása

Ebben a szakaszban egy, a standard Load Balancer mögötti privát kapcsolati szolgáltatást fog létrehozni.

1. A Azure Portal oldal bal felső részén válassza az **erőforrás létrehozása** lehetőséget.

2. A **Keresés a piactéren** mezőben keresse meg a **privát hivatkozást** .

3. Válassza a **Létrehozás** lehetőséget.

4. A **privát hivatkozás központjának** **Áttekintés** területén válassza a kék **create Private link Service** gombot.

5. Az **alapismeretek** lapon a **magánhálózati kapcsolat létrehozása** területen adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **CreatePrivLinkService-RG** elemet. |
    | **Példány adatai** |  |
    | Name | Adja meg a **myPrivateLinkService**. |
    | Region | Válassza az **USA 2. keleti régiója** lehetőséget. |

6. Válassza ki a **kimenő beállítások** lapot, vagy válassza a **következő: kimenő beállítások** elemet az oldal alján.

7. A **kimenő beállítások** lapon adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Terheléselosztóval | Válassza a **myLoadBalancer** lehetőséget. |
    | Terheléselosztó előtér-IP-címe | Válassza a **LoadBalancerFrontEnd (10.1.0.4)** lehetőséget. |
    | Forrás NAT-alhálózat | Válassza a **mySubnet (10.1.0.0/24)** lehetőséget. |
    | A TCP-proxy v2 engedélyezése | Hagyja meg az alapértelmezett **nem** értéket. </br> Ha az alkalmazás TCP-proxy v2-fejlécet vár, válassza az **Igen** lehetőséget. |
    | **Magánhálózati IP-cím beállításai** |  |
    | Hagyja meg az alapértelmezett beállításokat |  |

8. Válassza a **hozzáférési biztonság** lapot, vagy kattintson a Tovább gombra: a lap alján található **hozzáférés biztonság** elemre.

9. A **szerepköralapú hozzáférés-vezérlés** alapértelmezett értéke csak a **hozzáférési biztonság** lapon hagyható el.

10. Válassza a **címkék** lapot, vagy válassza a **következő: címkék** elemet az oldal alján.

11. Válassza a **felülvizsgálat + létrehozás** lapot, vagy válassza a **Tovább: felülvizsgálat + létrehozás** elemet az oldal alján.

12. Válassza a **Létrehozás** lehetőséget a **felülvizsgálat + létrehozás** lapon.

A magánhálózati kapcsolati szolgáltatás létrejött, és képes fogadni a forgalmat. Ha szeretné megtekinteni a forgalmi folyamatokat, konfigurálja az alkalmazást a standard Load Balancer mögött.


## <a name="create-private-endpoint"></a>Privát végpont létrehozása

Ebben a szakaszban a Private link Service-t egy privát végpontra képezi le. A virtuális hálózatok tartalmazzák a magánhálózati kapcsolat szolgáltatás privát végpontját. Ez a virtuális hálózat tartalmazza azokat az erőforrásokat, amelyek hozzáférnek a privát kapcsolati szolgáltatáshoz.

### <a name="create-private-endpoint-virtual-network"></a>Magánhálózati végpont virtuális hálózat létrehozása

1. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

2. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | **CreatePrivLinkService kiválasztása – RG** |
    | **Példány adatai** |                                                                 |
    | Name             | **MyVNetPE** megadása                                    |
    | Region           | Válassza az **USA 2. keleti** régióját |

3. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

4. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címtartomány | Adja meg a **11.1.0.0/16** értéket |

5. Az **alhálózat neve** alatt válassza ki az **alapértelmezett** szót.

6. Az **alhálózat szerkesztése** területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | **MySubnetPE** megadása |
    | Alhálózati címtartomány | Adja meg a **11.1.0.0/24** értéket |

7. Kattintson a **Mentés** gombra.

8. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

9. Válassza a **Létrehozás** lehetőséget.

### <a name="create-private-endpoint"></a>Privát végpont létrehozása

1. A portálon a képernyő bal felső részén válassza az **erőforrás létrehozása**  >  **hálózati**  >  **privát hivatkozás** lehetőséget, vagy a keresőmezőbe írja be a **privát hivatkozás** kifejezést.

2. Válassza a **Létrehozás** lehetőséget.

3. A **privát kapcsolat központban** válassza a bal oldali menüben a **privát végpontok** lehetőséget.

4. A **privát végpontok** területen válassza a **+ Hozzáadás** lehetőséget.

5. A **privát végpont létrehozása** elem **alapjai** lapon adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** | |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza a **CreatePrivLinkService-RG** elemet. Ezt az erőforráscsoportot az előző szakaszban hozta létre.|
    | **Példány adatai** |  |
    | Name  | Adja meg a **myPrivateEndpoint** nevet. |
    | Region | Válassza az **USA 2. keleti régiója** lehetőséget. |

6. Válassza ki az **erőforrás** lapot vagy a **következő: erőforrás** gombot az oldal alján.
    
7. Az **erőforrás** mezőben adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Kapcsolati módszer | Válassza a **Kapcsolódás egy Azure-erőforráshoz a címtárban** lehetőséget. |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforrás típusa | Válassza a **Microsoft. Network/privateLinkServices** lehetőséget. |
    | Erőforrás | Válassza a **myPrivateLinkService** lehetőséget. |

8. Válassza a **konfiguráció** lapot vagy a **következő: konfiguráció** gombot a képernyő alján.

9. A **konfiguráció** területen adja meg vagy válassza ki az alábbi adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Hálózat** |  |
    | Virtual Network | Válassza a **myVNetPE** lehetőséget. |
    | Alhálózat | Válassza a **mySubnetPE** lehetőséget. |

10. Válassza a **felülvizsgálat + létrehozás** lapot, vagy a képernyő alján található **felülvizsgálat + létrehozás** gombot.

11. Válassza a **Létrehozás** lehetőséget.

### <a name="ip-address-of-private-endpoint"></a>Magánhálózati végpont IP-címe

Ebben a szakaszban megtalálhatja a terheléselosztó és a magánhálózati kapcsolat szolgáltatásnak megfelelő privát végpont IP-címét.

1. A Azure Portal bal oldali oszlopában válassza az **erőforráscsoportok** lehetőséget.

2. Válassza ki a **CreatePrivLinkService-RG** erőforráscsoportot.

3. A **CreatePrivLinkService-RG** erőforráscsoporthoz válassza a **myPrivateEndpoint** lehetőséget.

4. A MyPrivateEndpoint **Áttekintés** lapján válassza ki a magánhálózati végponthoz társított hálózati adapter nevét.  A hálózati adapter neve a **myPrivateEndpoint. nic** előtaggal kezdődik.

5. A magánhálózati végpont hálózati adapterének **Áttekintés** lapján a végpont IP-címe a **magánhálózati IP-cím** mezőben jelenik meg.
    

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a Private link szolgáltatással, törölje az erőforráscsoportot az ebben a rövid útmutatóban használt erőforrások törléséhez.

1. Írja be a **CreatePrivLinkService-RG** kifejezést a portál tetején található keresőmezőbe, és válassza a **CreatePrivLinkService-RG** elemet a keresési eredmények közül.
1. Válassza az **Erőforráscsoport törlése** elemet.
1. **Írja be az ERŐFORRÁSCSOPORT neve mezőbe a** **CreatePrivLinkService-RG** értéket.
1. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban a következőket hajtja végre:

* Létrehozott egy virtuális hálózatot és belső Azure Load Balancer.
* Létrehozott egy privát kapcsolati szolgáltatást.
* Létrehozott egy virtuális hálózatot és egy privát végpontot a privát kapcsolat szolgáltatáshoz.

Ha többet szeretne megtudni az Azure Private-végpontról, folytassa a következővel:
> [!div class="nextstepaction"]
> [Rövid útmutató: privát végpont létrehozása a Azure Portal használatával](create-private-endpoint-portal.md)