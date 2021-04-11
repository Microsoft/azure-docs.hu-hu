---
title: 'Oktatóanyag: nyilvános Load Balancer létrehozása IP-alapú háttérrendszer-Azure Portal'
titleSuffix: Azure Load Balancer
description: Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy nyilvános Load balancert egy IP-alapú háttér-készlettel.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: tutorial
ms.date: 3/31/2021
ms.custom: template-tutorial
ms.openlocfilehash: f8174d46d1674e0cf81e36e89f6fb6180091a9b9
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2021
ms.locfileid: "106123589"
---
# <a name="tutorial-create-a-public-load-balancer-with-an-ip-based-backend-using-the-azure-portal"></a>Oktatóanyag: nyilvános Load Balancer létrehozása IP-alapú háttérrel a Azure Portal használatával

Ebből az oktatóanyagból megtudhatja, hogyan hozhat létre egy nyilvános Load balancert egy IP-alapú háttér-készlettel. 

A Azure Load Balancer hagyományos telepítése a virtuális gépek hálózati adapterét használja. Az IP-alapú háttérrendszer használatával a virtuális gépek IP-cím alapján kerülnek a háttérbe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * NAT-átjáró létrehozása kimenő kapcsolathoz
> * Azure Load Balancer létrehozása
> * IP-alapú háttérrendszer-készlet létrehozása
> * Két virtuális gép létrehozása
> * A terheléselosztó tesztelése
## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkező Azure-fiók. [Hozzon létre egy fiókot ingyenesen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Ebben a szakaszban egy virtuális hálózatot fog létrehozni a terheléselosztó, a NAT-átjáró és a virtuális gépek számára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

1. Válassza ki a képernyő bal felső részén az **Erőforrás létrehozása > Hálózatkezelés > Virtuális hálózat** lehetőséget, vagy a keresőmezőben keressen rá a **virtuális hálózat** kifejezésre.

2. Válassza a **Létrehozás** lehetőséget. 

3. A **virtuális hálózat létrehozása** területen adja meg vagy válassza ki ezt az információt az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését                                  |
    | Erőforráscsoport   | **TutorPubLBIP kiválasztása – RG** |
    | **Példány adatai** |                                                                 |
    | Name             | **MyVNet** megadása                                    |
    | Region           | Válassza ki az USA **keleti** régióját |

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
    | AzureBastionSubnet címterület | Adja meg a **10.1.1.0/27** értéket |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> A **név** mezőbe írja be a következőt: **myBastionIP**. </br> Válassza az **OK** lehetőséget. |


11. Válassza a **felülvizsgálat + létrehozás** lapot, vagy kattintson a **felülvizsgálat + létrehozás** gombra.

12. Válassza a **Létrehozás** lehetőséget.
## <a name="create-nat-gateway"></a>NAT-átjáró létrehozása

Ebben a szakaszban létrehoz egy NAT-átjárót, és hozzárendeli azt a korábban létrehozott virtuális hálózat alhálózatához.

1. A képernyő bal felső részén válassza az **erőforrás létrehozása > hálózatkezelés > NAT-átjáró** vagy a **NAT-átjáró** keresése a keresőmezőbe elemet.

2. Válassza a **Létrehozás** lehetőséget. 

3. A **hálózati címfordítási (NAT-) átjáró létrehozása** területen adja meg vagy válassza ki az alábbi adatokat az **alapok** lapon:

    | **Beállítás**          | **Érték**                                                           |
    |------------------|-----------------------------------------------------------------|
    | **Projekt részletei**  |                                                                 |
    | Előfizetés     | Válassza ki az Azure-előfizetését.                                  |
    | Erőforráscsoport   | Válassza az **új létrehozása** elemet, és írja be a **TutorPubLBIP-RG** karakterláncot a szövegmezőbe. </br> Válassza az **OK** lehetőséget. |
    | **Példány adatai** |                                                                 |
    | Name             | **MyNATgateway** megadása                                    |
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
## <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Ebben a szakaszban egy standard Azure Load Balancer fog létrehozni. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).
2. Válassza az **Erőforrás létrehozása** lehetőséget. 
3. A keresőmezőbe írja be a **Load Balancer** kifejezést. Válassza a **Load Balancer** elemet a keresési eredmények között.
4. A **terheléselosztó** lapon válassza a **Létrehozás** lehetőséget.
5. A **Load Balancer létrehozása** lapon adja meg a következő információkat, vagy válassza ki az alábbi adatokat: 

    | Beállítás                 | Érték                                              |
    | ---                     | ---                                                |
    | **Projekt részletei** |   |
    | Előfizetés               | Válassza ki előfizetését.    |    
    | Erőforráscsoport         | Válassza a **TutorPubLBIP-RG** elemet.|
    | **Példány adatai** |   |
    | Name                   | **MyLoadBalancer** megadása                                   |
    | Region         | Válassza ki az USA **keleti** régióját.                                        |
    | Típus          | Válassza a **Nyilvános** lehetőséget.                                        |
    | Termékváltozat           | Hagyja meg az alapértelmezett **standard** értéket. |
    | Szint          | Hagyja meg az alapértelmezett **területi** beállítást. |
    | **Nyilvános IP-cím** |   |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> Ha meglévő nyilvános IP-címmel szeretne használni, válassza a **meglévő használata** lehetőséget. |
    | Nyilvános IP-cím | Írja be a **myPublicIP-LB** karakterláncot a szövegmezőbe.|
    | A rendelkezésre állási zóna | Válassza ki a **felesleges zónát** a rugalmas Load Balancer létrehozásához. A Zona Load Balancer létrehozásához jelöljön ki egy 1, 2 vagy 3 típusú zónát |
    | A nyilvános IPv6-cím hozzáadása | Válassza a **Nem** lehetőséget. </br> További információ az IPv6-címekről és a Load balancerről: [Mi az az IPv6 for Azure Virtual Network?](../virtual-network/ipv6-overview.md)  |
    | Útválasztási beállítás | Hagyja meg az alapértelmezett **Microsoft-hálózatot**. </br> Az útválasztási beállításokkal kapcsolatos további információkért lásd: [Mi az útválasztási preferencia (előzetes verzió)?](../virtual-network/routing-preference-overview.md). |

6. Fogadja el az alapértelmezett értékeket a többi beállításnál, majd válassza a **felülvizsgálat + létrehozás** elemet.

7. A **felülvizsgálat + létrehozás** lapon válassza a **Létrehozás** lehetőséget.

## <a name="create-load-balancer-resources"></a>Terheléselosztó erőforrásainak létrehozása

Ebben a szakaszban a következőket konfigurálja:

* Egy háttérbeli címkészlet Load Balancer-beállításai.
* Egy állapot-mintavétel.
* Egy terheléselosztó-szabály.

### <a name="create-a-backend-pool"></a>Háttérkészlet létrehozása

A háttér-címkészlet tartalmazza a terheléselosztó számára csatlakoztatott virtuális (NIC) IP-címeit. 

Hozza létre a háttér-címkészlet **myBackendPool** , hogy a virtuális gépeket a terheléselosztási internetes forgalom számára is tartalmazza.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások** területen válassza a **háttér-készletek**, majd a **+ Hozzáadás** lehetőséget.

3. A **háttérrendszer-készlet hozzáadása** lapon adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **myBackendPool**. |
    | Virtuális hálózat | Válassza a **myVNet** lehetőséget. |
    | Háttérbeli készlet konfigurálása | Válassza ki az **IP-címet**. |
    | IP-verzió | Válassza az **IPv4** lehetőséget. |

4. Válassza a **Hozzáadás** lehetőséget.

### <a name="create-a-health-probe"></a>Állapotminta létrehozása

A terheléselosztó az alkalmazás állapotát állapottal figyeli. 

Az állapot mintavétele a terheléselosztó által az állapot-ellenőrzésekre adott válasz alapján hozzáadja vagy eltávolítja a virtuális gépeket. 

Hozzon létre egy **myHealthProbe** nevű állapotmintát a virtuális gépek állapotának felügyeletéhez.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások** területen válassza az **állapot**-mintavételek, majd a **+ Hozzáadás** lehetőséget.
    
    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg a **myHealthProbe**. |
    | Protokoll | Válassza a **TCP** lehetőséget. |
    | Port | Adja meg a **80** értéket.|
    | Időköz | A mintavételi kísérletek közötti időtartam másodpercben megadott számú **intervallumában** adja meg a **15** értéket. |
    | Nem kifogástalan állapot küszöbértéke | Válassza a **2** lehetőséget. |
   
3. Hagyja meg a többi alapértelmezett értéket, majd válassza a **Hozzáadás** lehetőséget.

### <a name="create-a-load-balancer-rule"></a>Terheléselosztási szabály létrehozása

A terheléselosztási szabállyal azt lehet megadni, hogy a rendszer hogyan ossza el a forgalmat a virtuális gépek között. A bejövő forgalom és a háttérbeli IP-készlet előtérbeli IP-konfigurációját a forgalom fogadásához adja meg. A forrás-és a célport a szabályban van definiálva. 

Ebben a szakaszban egy terheléselosztó-szabályt fog létrehozni:

* Elnevezett **: myhttprule**.
* A **LoadBalancerFrontEnd** nevű előtérben.
* Figyelés a **80-es porton**.
* Az elosztott terhelésű forgalmat a 80-es **porton** található **myBackendPool** nevű háttérre irányítja.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza a **myLoadBalancer** lehetőséget.

2. A **Beállítások** területen válassza a **terheléselosztási szabályok**, majd a **+ Hozzáadás** lehetőséget.

3. Adja meg vagy válassza ki a Load Balancer szabály következő adatait:
    
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
    | Munkamenet-állandóság | Hagyja meg az alapértelmezett **beállítást**. |
    | Üresjárati időkorlát (perc) | Adja meg a **15** percet. |
    | TCP alaphelyzetbe állítása | Válassza az **Engedélyezve** lehetőséget. |
    | Nem fix IP-cím | Válassza a **Letiltva** lehetőséget. |
    | Kimenő forrás hálózati címfordítása (SNAT) | Válassza **a (javasolt) a kimenő szabályok használata lehetőséget, hogy a háttér-készlet tagjai hozzáférjenek az internethez.** |

4. Hagyja meg a többi alapértelmezett beállítást, majd kattintson a **Hozzáadás** gombra.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a szakaszban két virtuális gépet hoz létre (**myVM1** és **myVM2**) két különböző zónában (**1. zóna** és **2. zóna**).

Ezek a virtuális gépek hozzáadódnak a korábban létrehozott terheléselosztó háttér-készletéből.

1. A portál bal felső részén válassza az **erőforrás létrehozása**  >  **számítási**  >  **virtuális gép** lehetőséget. 
   
2. A **virtuális gép létrehozása** területen adja meg vagy válassza ki az értékeket az **alapok** lapon:

    | Beállítás | Érték                                          |
    |-----------------------|----------------------------------|
    | **Projekt részletei** |  |
    | Előfizetés | Válassza ki az Azure-előfizetését |
    | Erőforráscsoport | **TutorPubLBIP kiválasztása – RG** |
    | **Példány adatai** |  |
    | Virtuális gép neve | **MyVM1** megadása |
    | Region | Válassza ki az USA **keleti** régióját |
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
    | Hálózati biztonsági csoport konfigurálása | Válassza az **Új létrehozása** lehetőséget. </br> A **hálózati biztonsági csoport létrehozása** területen adja meg  a myNSG **nevet**. </br> A **Bejövő szabályok** területen válassza **a + Bejövő szabály hozzáadása** elemet. </br> A **szolgáltatás** alatt válassza a **http** lehetőséget. </br> A **Priority (prioritás**) mezőben adja meg a **100** értéket. </br> A **név** mezőben adja meg a **: myhttprule** </br> Válassza a **Hozzáadás** lehetőséget </br> Válassza az **OK** gombot. |
    | **Terheléselosztás**  |
    | Elhelyezi ezt a virtuális gépet egy meglévő terheléselosztási megoldás mögött? | Jelölje be a jelölőnégyzetet.|
    | **Terheléselosztási beállítások** |
    | Terheléselosztási beállítások | **Azure Load Balancer** kiválasztása |
    | Terheléselosztó kiválasztása | **MyLoadBalancer** kiválasztása  |
    | Háttérbeli készlet kiválasztása | **MyBackendPool** kiválasztása |
   
5. Válassza az **Áttekintés + létrehozás** lehetőséget. 
  
6. Tekintse át a beállításokat, majd kattintson a **Létrehozás** gombra.

7. Az 1 – 6. lépés végrehajtásával hozzon létre egy virtuális gépet a következő értékekkel és az összes többi beállítással megegyező módon, mint a **myVM1**:

    | Beállítás | VM 2 |
    | ------- | ----- |
    | Name |  **myVM2** |
    | A rendelkezésre állási zóna | **2** |
    | Hálózati biztonsági csoporttal | Meglévő **myNSG** kiválasztása| 

## <a name="install-iis"></a>Az IIS telepítése

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd az erőforrások listából válassza ki a **myVM1** , amely a **TutorPubLBIP-RG** erőforráscsoporthoz található.

2. Az **Áttekintés** lapon válassza a **kapcsolat**, majd a **Bastion** lehetőséget.

3. Jelölje be a **Bastion gomb használata** jelölőnégyzetet.

4. Adja meg a virtuális gép létrehozásakor megadott felhasználónevet és jelszót.

5. Válassza a **Kapcsolódás** lehetőséget.

6. A kiszolgáló asztalán navigáljon a **Windows felügyeleti eszközök**  >  **Windows PowerShell** elemre.

7. A PowerShell ablakban futtassa a következő parancsokat:

    * Az IIS-kiszolgáló telepítése
    * Az alapértelmezett iisstart.htm fájl eltávolítása
    * Adjon hozzá egy új iisstart.htm fájlt, amely megjeleníti a virtuális gép nevét:

   ```powershell
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
    Remove-Item C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. A megerősített munkamenet lezárása a **myVM1**.

9. Az IIS és a frissített iisstart.htm fájl **myVM2** való telepítéséhez ismételje meg az 1 – 7. lépést.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

1. A terheléselosztó nyilvános IP-címének megkeresése az **Áttekintés** képernyőn. Válassza a **minden szolgáltatás** lehetőséget a bal oldali menüben, válassza a **minden erőforrás** lehetőséget, majd a **myPublicIP-LB** elemet.

2. Másolja a nyilvános IP-címet, majd illessze be a böngésző címsorába. Az IIS-webkiszolgáló alapértelmezett oldala jelenik meg a böngészőben.

   ![IIS-webkiszolgáló](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

Ha látni szeretné, hogy a terheléselosztó továbbítsa a forgalmat a myVM2, kényszerítse a webböngészőt az ügyfélgépről.
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, törölje a virtuális hálózatot, a virtuális gépet és a NAT-átjárót a következő lépésekkel:

1. A bal oldali menüben válassza az **erőforráscsoportok** lehetőséget.

2. Válassza ki a **TutorPubLBIP-RG** erőforráscsoportot.

3. Válassza az **Erőforráscsoport törlése** elemet.

4. Írja be a **TutorPubLBIP-RG** értéket, és válassza a **Törlés** lehetőséget.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban a következőket végezheti el:

* Virtuális hálózat létrehozva
* NAT-átjáró létrehozva
* Terheléselosztó létrehozva egy IP-alapú háttér-készlettel
* A terheléselosztó tesztelése

A következő cikkből megtudhatja, hogyan hozhat létre régiók közötti terheléselosztó-t:
> [!div class="nextstepaction"]
> [Régiók közötti Azure Load Balancer létrehozása a Azure Portal használatával](tutorial-cross-region-portal.md)
