---
title: 'Oktatóanyag: NAT-átjáró integrálása belső terheléselosztó használatával – Azure Portal'
titleSuffix: Virtual Network NAT
description: Ebből az oktatóanyagból megtudhatja, hogyan integrálhat egy Virtual Network NAT-átjárót belső terheléselosztó használatával a Azure Portal segítségével.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 03/19/2021
ms.custom: template-tutorial
ms.openlocfilehash: f25266f5a969514ca515e8cbbec959404428d6fb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "104670844"
---
# <a name="tutorial-integrate-a-nat-gateway-with-an-internal-load-balancer-using-the-azure-portal"></a>Oktatóanyag: NAT-átjáró integrálása belső terheléselosztó használatával a Azure Portal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhat egy NAT-átjárót egy belső terheléselosztó használatával.

Alapértelmezés szerint az Azure standard Load Balancer biztonságos. A kimenő kapcsolat explicit módon van definiálva a kimenő SNAT (forrásoldali hálózati címfordítás) engedélyezésével. 

A SNAT engedélyezve van egy belső háttér-készlethez egy másik nyilvános terheléselosztó, hálózati útválasztás vagy egy virtuális gépen definiált nyilvános IP-cím használatával.

A NAT-átjáró integrációja a háttér-készletben lévő virtuális gépen definiált nyilvános terheléselosztó, hálózati útválasztás vagy nyilvános IP-cím központi telepítésének szükségességét váltja fel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Azure Load Balancer létrehozása
> * Hozzon létre két virtuális gépet a Azure Load Balancer háttér-készletéhez
> * NAT-átjáró létrehozása
> * A terheléselosztó backend-készletében lévő virtuális gépek kimenő kapcsolatának ellenőrzése

## <a name="prerequisites"></a>Előfeltételek

Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-the-virtual-network"></a>A virtuális hálózat létrehozása

Ebben a szakaszban létre fog hozni egy virtuális hálózatot és alhálózatot.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

2. Válassza a **Létrehozás** lehetőséget. 

3. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | Válassza az **Új létrehozása** lehetőséget. Adja meg a **TutorIntLBNAT-RG** értéket. </br> Válassza az **OK** lehetőséget. |
    | **Példány adatai** |                                                                 |
    | Name             | **MyVNet** megadása                                    |
    | Region           | Válassza ki az **USA keleti** régióját |

4. Válassza az **IP-címek** lapot, vagy válassza a **következő: IP-címek** gombot az oldal alján.

5. Az **IP-címek** lapon adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | IPv4-címtartomány | Adja meg a **10.1.0.0/16** értéket |

6. Az **alhálózat neve** alatt válassza ki az **alapértelmezett** szót.

7. Az **alhálózat szerkesztése** területen adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Alhálózat neve | **MyBackendSubnet** megadása |
    | Alhálózati címtartomány | Adja meg a **10.1.0.0/24** értéket |

8. Kattintson a **Mentés** gombra.

9. Válassza a **Biztonság** fület.

10. A **BastionHost** területen válassza az **Engedélyezés** lehetőséget. Adja meg a következő adatokat:

    | Beállítás            | Érték                      |
    |--------------------|----------------------------|
    | Bástya neve | **MyBastionHost** megadása |
    | AzureBastionSubnet címterület | Adja meg a **10.1.1.0/24** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név** mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |


11. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

12. Válassza a **Létrehozás** lehetőséget.

## <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Ebben a szakaszban egy standard Azure Load Balancer fog létrehozni. 

1. Válassza az **Erőforrás létrehozása** lehetőséget. 

2. A keresőmezőbe írja be a **Load Balancer** kifejezést. Válassza a **Load Balancer** elemet a keresési eredmények között.

3. A **terheléselosztó** lapon válassza a **Létrehozás** lehetőséget.
4. A **Load Balancer létrehozása** lapon adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | **Projekt részletei** |   |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza a **TutorIntLBNAT-RG** elemet.|
    | **Példány adatai** |    |
    | Name                   | **MyLoadBalancer** megadása                                   |
    | Region         | Válassza ki az USA **keleti** régióját.                                        |
    | Típus          | Válassza a **belső** lehetőséget.                                        |
    | Termékváltozat           | Hagyja meg az alapértelmezett **standard** értéket. |
    | **Virtuális hálózat konfigurálása** |    |
    | Virtuális hálózat | Válassza a **myVNet** lehetőséget. |
    | Alhálózat | Válassza a **myBackendSubnet** lehetőséget. |
    | IP-cím hozzárendelése | Válassza a **dinamikus** lehetőséget. |
    | A rendelkezésre állási zóna | Válassza ki a **felesleges zónát** a rugalmas Load Balancer létrehozásához. </br> A Zona Load Balancer létrehozásához jelöljön ki egy 1, 2 vagy 3 típusú zónát |
    

5. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás** elemet.

6. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget.

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
   
3. Hagyja meg a többi alapértelmezett értéket, majd válassza a **Hozzáadás** lehetőséget.

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
    | Üresjárati időkorlát (perc) | Adja meg a **15** percet. |
    | TCP alaphelyzetbe állítása | Válassza az **Engedélyezve** lehetőséget. |

4. Hagyja meg a többi alapértelmezett beállítást, majd kattintson az **OK gombra**.


## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a szakaszban két virtuális gépet hoz létre (**myVM1** és **myVM2**) két különböző zónában (**1. zóna** és **2. zóna**).

Ezek a virtuális gépek hozzáadódnak a korábban létrehozott terheléselosztó háttér-készletéből.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget. 
   
2. A **virtuális gép létrehozása** területen írja be vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **TutorIntLBNAT kiválasztása – RG** |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM1** megadása |
    | Region | Válassza ki az **USA keleti** régióját |
    | Rendelkezésre állási beállítások | **Rendelkezésre állási zónák** kiválasztása |
    | A rendelkezésre állási zóna | **1** . választás |
    | Kép | **Windows Server 2019 Datacenter** kiválasztása |
    | Azure Spot-példány | Hagyja meg az alapértelmezett értéket |
    | Méret | A virtuális gép méretének kiválasztása vagy az alapértelmezett beállítás megadása |
    | **Rendszergazdai fiók** |  |
    | Felhasználónév | Adjon meg egy felhasználónevet |
    | Jelszó | Adja meg a jelszót |
    | Jelszó megerősítése | Jelszó újbóli megadása |
    | **Bejövő portok szabályai** |  |
    | Nyilvános bejövő portok | Válassza a **nincs** lehetőséget |

3. Válassza a **hálózatkezelés** lapot, vagy válassza a **Tovább: lemezek**, majd a **Tovább: hálózatkezelés** lehetőséget.
  
4. A hálózatkezelés lapon válassza ki vagy írja be a következőket:

    | Beállítás | Érték |
    |-|-|
    | **Hálózati adapter** |  |
    | Virtuális hálózat | **myVNet** |
    | Alhálózat | **myBackendSubnet** |
    | Nyilvános IP-cím | Válassza a **Nincs** lehetőséget. |
    | NIC hálózati biztonsági csoport | Válassza a **speciális** lehetőséget|
    | Hálózati biztonsági csoport konfigurálása | Válassza az **Új létrehozása** lehetőséget. </br> A **hálózati biztonsági csoport létrehozása** területen adja meg  a myNSG **nevet**. </br> A **Bejövő szabályok** területen válassza **a + Bejövő szabály hozzáadása** elemet. </br> A  **célport tartománya** területen adja meg a **80** értéket. </br> A **Priority (prioritás**) területen adja meg a **100** értéket. </br> A **név** mezőben adja meg a **: myhttprule** </br> Válassza a **Hozzáadás** lehetőséget </br> Válassza az **OK** gombot. |
    | **Terheléselosztás**  |
    | Elhelyezi ezt a virtuális gépet egy meglévő terheléselosztási megoldás mögött? | Jelölje be a jelölőnégyzetet. |
    | **Terheléselosztási beállítások** |
    | Terheléselosztási beállítások | **Azure Load Balancer** kiválasztása |
    | Terheléselosztó kiválasztása | **MyLoadBalancer** kiválasztása  |
    | Háttérbeli készlet kiválasztása | **MyBackendPool** kiválasztása |
   
5. Válassza az **Áttekintés + létrehozás** lehetőséget. 
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

7. Az 1 – 7. lépés végrehajtásával hozzon létre egy virtuális gépet a következő értékekkel és az összes többi beállítással, amely ugyanaz, mint a **myVM1**:

    | Beállítás | VM 2 |
    | ------- | ----- |
    | Name |  **myVM2** |
    | A rendelkezésre állási zóna | **2** |
    | Hálózati biztonsági csoporttal | Meglévő **myNSG** kiválasztása| 

## <a name="create-nat-gateway"></a>NAT-átjáró létrehozása

Ebben a szakaszban létrehoz egy NAT-átjárót, és hozzárendeli azt a korábban létrehozott virtuális hálózat alhálózatához.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása > hálózatkezelés > NAT-átjáró** vagy a **NAT-átjáró** keresése a keresőmezőbe elemet.

2. Válassza a **Létrehozás** lehetőséget. 

3. A **hálózati címfordítási (NAT-) átjáró létrehozása** területen adja meg vagy válassza ki az alábbi adatokat az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését.                                  |
    | Erőforráscsoport   | Válassza a **TutorIntLBNAT-RG** elemet. |
    | **Példány adatai** |                                                                 |
    | Name             | **MyNATGateway** megadása                                    |
    | Region           | Válassza ki az USA **keleti** régióját  |
    | Rendelkezésre állási zóna | Válassza a **Nincs** lehetőséget. |
    | Üresjárati időkorlát (perc) | Adja meg a **10** értéket. |

4. Válassza a **kimenő IP-cím** lapot, vagy válassza a **következő: kimenő IP-cím** gombot az oldal alján.

5. A **kimenő IP-cím** lapon adja meg vagy válassza ki a következő adatokat:

    | **Beállítás** | **Érték** |
    | ----------- | --------- |
    | Nyilvános IP-címek | Válassza **az új nyilvános IP-cím létrehozása** lehetőséget. </br> A **név** mezőben adja meg a **MyPublicIP-NAT** nevet. </br> Válassza az **OK** lehetőséget. |

6. Válassza az **alhálózat** fület, vagy válassza a **következő: alhálózat** gombot a lap alján.

7. Az **alhálózat** lapon válassza a **MyVNet** lehetőséget a **virtuális hálózat** legördülő menüben.

8. Jelölje be a **myBackendSubnet** melletti jelölőnégyzetet.

9. Válassza a **felülvizsgálat + létrehozás** fület, vagy kattintson a lap alján található kék **Áttekintés + létrehozás** gombra.

10. Válassza a **Létrehozás** lehetőséget.

## <a name="test-nat-gateway"></a>NAT-átjáró tesztelése

Ebben a szakaszban teszteljük a NAT-átjárót. Először a NAT-átjáró nyilvános IP-címét fogjuk felderíteni. Ezután csatlakozik a teszt virtuális géphez, és ellenőrizze a kimenő kapcsolatot a NAT-átjárón keresztül.
    
1. Keresse meg a NAT-átjáró nyilvános IP-címét az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd válassza a **myPublicIP** lehetőséget.

2. Jegyezze fel a nyilvános IP-címet:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-internal-portal/find-public-ip.png" alt-text="Képernyőfelvétel: a NAT-átjáró nyilvános IP-címének felderítése." border="true":::

3. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza ki a **myVM1** , amely a **TutorIntLBNAT-RG** erőforráscsoporthoz található.

4. Az **Áttekintés** lapon válassza a **kapcsolat**, majd a **Bastion** lehetőséget.

5. Válassza a kék **használat Bastion** gombot.

6. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

7. Nyissa meg az **Internet Explorert** a **myVM1**.

8. Írja be **https://whatsmyip.com** a címsorba.

9. Ellenőrizze, hogy a megjelenő IP-cím megegyezik-e az előző lépésben feljegyzett NAT-átjáró címével:

    :::image type="content" source="./media/tutorial-nat-gateway-load-balancer-internal-portal/my-ip.png" alt-text="Képernyőkép az Internet Explorer külső kimenő IP-címéről." border="true":::

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a virtuális hálózatot, a virtuális gépet és a NAT-átjárót a következő lépésekkel:

1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.

2. Válassza ki a **TutorIntLBNAT-RG** erőforráscsoportot.

3. Válassza az **Erőforráscsoport törlése** elemet.

4. Írja be a **TutorIntLBNAT-RG** értéket, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Az Azure Virtual Network NAT-ról további információt a következő témakörben talál:
> [!div class="nextstepaction"]
> [Virtual Network NAT áttekintése](nat-overview.md)
