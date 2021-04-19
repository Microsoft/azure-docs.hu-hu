---
title: 'Oktatóanyag: Több rendelkezésre állási csoportból álló terheléselosztás létrehozása a háttérkészletben – Azure Portal'
titleSuffix: Azure Load Balancer
description: Ebben az oktatóanyagban üzembe helyezünk egy Azure Load Balancer egynél több rendelkezésre állási készletet a háttérkészletben.
author: asudbring
ms.author: allensu
ms.service: virtual-network
ms.subservice: nat
ms.topic: tutorial
ms.date: 04/16/2021
ms.custom: template-tutorial
ms.openlocfilehash: 21ff43217a7b2bd874a384f3b07a48d5223a1be2
ms.sourcegitcommit: 089c2bd1ac4861f43c4b89396d3d056a6eef4913
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2021
ms.locfileid: "107602348"
---
# <a name="tutorial-create-a-load-balancer-with-more-than-one-availability-set-in-the-backend-pool-using-the-azure-portal"></a>Oktatóanyag: Több rendelkezésre állási csoportból álló terheléselosztás létrehozása a háttérkészletben az Azure Portal

A magas rendelkezésre állású üzembe helyezés részeként a virtuális gépek gyakran több rendelkezésre állási csoportba vannak csoportosítva. 

Load Balancer több rendelkezésre állási készletet is támogat a háttérkészletben lévő virtuális gépekkel.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Virtuális hálózat létrehozása
> * NAT-átjáró létrehozása kimenő kapcsolathoz
> * Standard termékváltozat-Azure Load Balancer
> * Négy virtuális gép és két rendelkezésre állási csoport létrehozása
> * Rendelkezésre állási készletekben lévő virtuális gépek hozzáadása a terheléselosztási háttérkészlethez
> * A terheléselosztó tesztelése

## <a name="prerequisites"></a>Előfeltételek

- Aktív előfizetéssel rendelkezik egy Azure-fiók. [Hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)

## <a name="create-a-virtual-network"></a>Virtuális hálózat létrehozása

Ebben a szakaszban egy virtuális hálózatot fog létrehozni a terheléselosztáshoz és az oktatóanyagban használt egyéb erőforrásokhoz.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

2. A portál tetején található keresőmezőbe írja be a **Következőt: Virtuális hálózat.**

3. A keresési eredmények között válassza a **Virtuális hálózatok lehetőséget.**

4. Válassza **a + Létrehozás lehetőséget.**

5. A **Virtuális hálózat létrehozása** lap Alapvető beállítások **lapján** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ------|
    | **Projekt részletei** |   |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza az **Új létrehozása** lehetőséget. </br> A **Name (Név) szövegben adja meg a** **Következőt: <** |
    | **Példány adatai** |   |
    | Name | Adja meg a **myVNet** értéket. |
    | Region | Válassza az **(USA) USA 2. nyugati régiója** elemet. |

6. Válassza az **IP-címek** lapot, vagy a Lap alján található **Tovább: IP-címek** gombot.

7. Az **IP-címek lap** **Alhálózat neve részében válassza** az alapértelmezett **lehetőséget.**

8. Az **Alhálózat szerkesztése panel** Alhálózat neve részében **adja** meg a **myBackendSubnet nevet.**

9. Kattintson a **Mentés** gombra.

10. Válassza **a Biztonság** fület vagy a Lap alján található **Tovább:** Biztonság gombot.

11. A Biztonság **lap** **BastionHost területén válassza** az **Engedélyezés lehetőséget.**

12. Adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Bastion neve | Írja be a **MyBastionHost ált.** |
    | AzureBastionSubnet címtér | Írja be **a 10.1.1.0/27- et.** |
    | Nyilvános IP-cím | Válassza az **Új létrehozása** lehetőséget. </br> Írja be **a myBastionIP nevet** **a névbe.** |

13. Válassza az **Áttekintés + létrehozás** lapot, vagy az oldal alján található kék Áttekintés **+** létrehozás gombot.

14. Válassza a **Létrehozás** lehetőséget.

## <a name="create-nat-gateway"></a>NAT-átjáró létrehozása 

Ebben a szakaszban egy NAT-átjárót fog létrehozni a virtuális gépek kimenő kapcsolatának létrehozásához.

1. A portál tetején található keresőmezőbe írja be a **NAT-átjárót.**

2. A **keresési eredmények között** válassza a NAT-átjárók lehetőséget.

3. Válassza **a + Létrehozás lehetőséget.**

4. A **Hálózati címfordítási** **(NAT-)** átjáró létrehozása lap Alapvető beállítások lapján adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |   |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza **a MertelBmultiAVS-rg lehetőséget.** |
    | **Példány adatai** |   |
    | NAT-átjáró neve | Írja be **a myNATgateway ált.** |
    | Region | Válassza az **(USA) USA 2. nyugati régiója** elemet. |
    | A rendelkezésre állási zóna | Válassza a **Nincs** lehetőséget. |
    | Üresjárati időkorlát (perc) | Adja meg **a 15-öt.** |

5. Válassza a **Kimenő IP-cím** lapot, vagy válassza a **Tovább: Kimenő IP-cím** gombot a lap alján.

6. A **Kimenő IP-cím lapon válassza a** Nyilvános **IP-címek** melletti Új nyilvános **IP-cím** létrehozása lehetőséget.

7. A Név szövegbe írja be a **myPublicIP-nat** **nevet.**

8. Válassza az **OK** lehetőséget.

9. Válassza az **Alhálózat** lapot, vagy válassza a **Tovább: Alhálózat** gombot a lap alján.

10. Válassza **a myVNet** elemet a virtuális hálózat alatti lekért **menüben.**

11. Jelölje be a **myBackendSubnet melletti jelölőnégyzetet.**

12. Válassza az **Áttekintés + létrehozás** lapot, vagy kattintson a lap alján található Kék Felülvizsgálat **+** létrehozás gombra.

13. Válassza a **Létrehozás** lehetőséget.

## <a name="create-load-balancer"></a>Terheléselosztó létrehozása

Ebben a szakaszban egy terheléselosztást fog létrehozni a virtuális gépekhez.

1. A portál tetején található keresőmezőbe írja be a **Terheléselosztást.**

2. A **keresési eredmények között válassza** a Terheléselosztások lehetőséget.

3. Válassza **a + Létrehozás lehetőséget.**

4. A **Terheléselosztás létrehozása** lap Alapvető beállítások **lapján** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |   |
    | Előfizetés | Válassza ki előfizetését. |
    | Erőforráscsoport | Válassza **a MajdbmultiAVS-rg lehetőséget.** |
    | **Példány adatai** |   |
    | Name | Adja meg **a myLoadBalancer adhatja meg a myLoadBalancer adhatja meg.** |
    | Region | Válassza az **(USA) USA 2. nyugati régiója** elemet. |
    | Típus | Hagyja meg az alapértelmezett **Nyilvános értéket.** |
    | Termékváltozat | Hagyja meg az alapértelmezett **Standard értéket.** |
    | Szint | Hagyja meg az alapértelmezett **Regionális értéket.** |
    | **Nyilvános IP-cím** |   |
    | Nyilvános IP-cím | Hagyja meg az Új **létrehozása beállítás alapértelmezett beállítását.** |
    | Nyilvános IP-cím | Írja be **a myPublicIP-lb fájlt.** |
    | A rendelkezésre állási zóna | Válassza a **Zónaredundáns lehetőséget.** |
    | A nyilvános IPv6-cím hozzáadása | Hagyja meg az alapértelmezett **Nem értéket.** |
    | Útválasztási beállítás | Hagyja meg az alapértelmezett **Microsoft-hálózatot.** |

5. Válassza az **Áttekintés + létrehozás** lapot, vagy kattintson az oldal alján található kék Felülvizsgálat **+** létrehozás gombra.

6. Válassza a **Létrehozás** lehetőséget.

### <a name="configure-load-balancer-settings"></a>Terheléselosztási beállítások konfigurálása

Ebben a szakaszban egy háttérkészletet fog létrehozni a **myLoadBalancer számára.**

Állapotfigyelőt fog létrehozni a **HTTP** és a **80-as port figyelése érdekében.** Az állapotfigyelő mintavétel figyeli a háttérkészletben lévő virtuális gépek állapotát. 

Létre fog hozni egy terheléselosztási szabályt a **80-as porthoz,** letiltott kimenő SNAT-val. A korábban létrehozott NAT-átjáró kezeli a virtuális gépek kimenő kapcsolatát.

1. A portál tetején található keresőmezőbe írja be a **Következőt: Load Balancer.**

2. A **keresési eredmények között válassza a** Terheléselosztások lehetőséget.

3. Válassza **a myLoadBalancer lehetőséget.**

4. A **myLoadBalancer eszközben** válassza a **Háttérkészletek** lehetőséget a **Beállítások menüben.**

5. Válassza **a + Hozzáadás** a **háttérkészletben lehetőséget.**

6. A **Háttérkészlet hozzáadása mezőben** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be **a myBackendPool ált.** |
    | Virtuális hálózat | Válassza **a myVNet lehetőséget.** |
    | Háttérkészlet konfigurációja | Hagyja meg a hálózati adapter **alapértelmezett beállítását.** |
    | IP-verzió | Hagyja meg az **alapértelmezett IPv4 értéket.** |

7. Válassza a **Hozzáadás** lehetőséget.

8. Válassza **az Állapot-mintavételek lehetőséget.**

9. Válassza a **+ Hozzáadás** lehetőséget.

10. Az **Állapot-mintavétel hozzáadása mezőben** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be **a myHTTPProbe adatokat.** |
    | Protokoll | Válassza a **HTTP lehetőséget.** |
    | Port | Hagyja meg az alapértelmezett **80 értéket.** |
    | Elérési út | Hagyja meg az alapértelmezett **/** értéket. |
    | Időköz | Hagyja meg az alapértelmezett **5** másodpercet. |
    | Nem kifogástalan állapot küszöbértéke | Hagyja meg az alapértelmezett **2 egymást** követő hibát. |

11. Válassza a **Hozzáadás** lehetőséget.

12. Válassza **a Terheléselosztási szabályok lehetőséget.** 

13. Válassza a **+ Hozzáadás** lehetőséget.

14. Adja meg vagy válassza ki a következő információkat a **Terheléselosztási szabály hozzáadása alatt:**

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Írja be **a myHTTPRule adatokat.** |
    | IP-verzió | Hagyja meg az **alapértelmezett IPv4 értéket.** |
    | Előtere IP-címe | Válassza **a LoadBalancerFrontEnd lehetőséget.** |
    | Protokoll | Válassza a TCP alapértelmezett **beállítását.** |
    | Port | Adja meg **a 80-ast.** |
    | Háttérport | Adja meg **a 80-ast.** |
    | A háttérkészlet | Válassza **ki a myBackendPool készletet.** |
    | Állapotadat-mintavétel | Válassza **a myHTTPProbe lehetőséget.** |
    | Munkamenet-állandóság | Hagyja meg az alapértelmezett **Nincs értéket.** |
    | Üresjárati időkorlát (perc) | Állítsa a csúszkát **15-re.** |
    | TCP alaphelyzetbe állítása | Válassza az **Engedélyezve** lehetőséget. |
    | Nem fix IP-cím | Hagyja meg az alapértelmezett **Letiltva beállítást.** |
    | Kimenő forráshálózati címfordítás (SNAT) | Hagyja meg az **alapértelmezett (Ajánlott) Kimenő** szabályok használata beállítást, hogy hozzáférést biztosítson a háttérkészlet tagjai számára az internethez. |

5. Válassza a **Hozzáadás** lehetőséget.

## <a name="create-virtual-machines"></a>Virtuális gépek létrehozása

Ebben a szakaszban két rendelkezésre állási csoportot fog létrehozni csoportonként két virtuális gépből. Ezeket a gépeket a rendszer a terheléselosztási eszköz háttérkészletében fogja hozzáadni a létrehozás során. 

### <a name="create-first-set-of-vms"></a>Virtuális gépek első készletének létrehozása

1. A **portál bal felső** részén válassza az + Erőforrás létrehozása lehetőséget.

2. Az **Új mezőben** válassza **a Számítási** virtuális gép  >  **lehetőséget.**

3. A **Virtuális gép létrehozása** lap Alapvető beállítások **lapján** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |   |
    | Előfizetés | Az előfizetés kiválasztása |
    | Erőforráscsoport | Válassza **a MertelBmultiAVS-rg lehetőséget.** |
    | **Példány adatai** |   |
    | Virtuális gép neve | Adja meg **a myVM1 1-et.** |
    | Region | Válassza az **(USA) USA 2. nyugati régiója** elemet. |
    | Rendelkezésre állási beállítások | Válassza a **Rendelkezésre állási csoport** lehetőséget. |
    | Rendelkezésre állási csoport | Válassza az **Új létrehozása** lehetőséget. </br> Írja be **a myAvailabilitySet1 nevet** a **névbe.** </br> Válassza az **OK** lehetőséget. |
    | Kép | Válassza **a Windows Server 2019 Datacenter - Gen1 lehetőséget.** |
    | Azure Spot-példány | Ne jelölje be az alapértelmezett jelölőnégyzetet. |
    | Méret | Válassza ki a virtuális gép méretét. |
    | **Rendszergazdai fiók** |   |
    | Felhasználónév | Adjon meg egy felhasználónevet. |
    | Jelszó | Adjon meg egy jelszót. |
    | **Bejövőport-szabályok** |   |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |

4. Válassza **a Hálózat** lapfület, vagy kattintson a **Tovább:** Lemezek, majd a **Tovább:** Hálózatkezelés gombra a lap alján.

5. A **Hálózat lapon** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Hálózati adapter** |   |
    | Virtuális hálózat | Válassza **a myVNet lehetőséget.** |
    | Alhálózat | Válassza **a myBackendSubnet lehetőséget.** |
    | Nyilvános IP-cím | Válassza a **Nincs** lehetőséget. |
    | Hálózati adapter hálózati biztonsági csoportja | Válassza az **Advanced** (Speciális) lehetőséget. |
    | Hálózati biztonsági csoport konfigurálása | Válassza az **Új létrehozása** lehetőséget. </br> A **Név alatt** adja meg a **myNSG nevet.** </br> Válassza **a +Bejövő szabály hozzáadása lehetőséget** a Bejövő szabályok **között.** </br> A **Szolgáltatás mezőben** válassza a HTTP **lehetőséget.** </br> A Név alatt adja meg a **myHTTPrule** **nevet.** </br> Válassza a **Hozzáadás** lehetőséget. </br> Válassza az **OK** lehetőséget. | 
    | **Terheléselosztás** |   |
    | Ezt a virtuális gépet egy meglévő terheléselosztási megoldás mögé helyezze? | Jelölje be a jelölőnégyzetet. |
    | **Terheléselosztási beállítások** |   |
    | Terheléselosztási lehetőségek | Válassza **az Azure Load Balancer lehetőséget.** |
    | Terheléselosztás kiválasztása | Válassza **a myLoadBalancer lehetőséget.** |
    | Háttérkészlet kiválasztása | Válassza **a myBackendPool lehetőséget.** |

6. Válassza az **Áttekintés + létrehozás** lapot, vagy kattintson az oldal alján található kék Felülvizsgálat **+** létrehozás gombra.

7. Válassza a **Létrehozás** lehetőséget.

8. Ismételje meg az 1–7. lépést a készlet második virtuális gépének létrehozásához. Cserélje le a virtuális gép beállításait a következő információkra:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg **a myVM2-t.** |
    | Rendelkezésre állási csoport | Válassza **a myAvailabilitySet1 lehetőséget.** |
    | Virtual Network | Válassza **a myVNet lehetőséget.** |
    | Alhálózat | Válassza **a myBackendSubnet lehetőséget.** |
    | Nyilvános IP-cím | Válassza a **Nincs** lehetőséget. |
    | Hálózati biztonsági csoporttal | Válassza **a myNSG lehetőséget.** |
    | Terheléselosztási lehetőségek | Válassza **az Azure Load Balancer lehetőséget.** |
    | Terheléselosztás kiválasztása | Válassza **a myLoadBalancer lehetőséget.** |
    | Háttérkészlet kiválasztása | Válassza **a myBackendPool lehetőséget.** |

### <a name="create-second-set-of-vms"></a>Virtuális gépek második készletének létrehozása

1. A **portál bal felső** részén válassza az + Erőforrás létrehozása lehetőséget.

2. Az **Új mezőben** válassza **a Számítási** virtuális gép  >  **lehetőséget.**

3. A **Virtuális gép létrehozása** lap Alapvető beállítások **lapján** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Projekt részletei** |   |
    | Előfizetés | Az előfizetés kiválasztása |
    | Erőforráscsoport | Válassza **a MertelBmultiAVS-rg lehetőséget.** |
    | **Példány adatai** |   |
    | Virtuális gép neve | Írja be **a myVM3 adhatja meg a myVM3 adhatja meg.** |
    | Region | Válassza az **(USA) USA 2. nyugati régiója** elemet. |
    | Rendelkezésre állási beállítások | Válassza a **Rendelkezésre állási csoport** lehetőséget. |
    | Rendelkezésre állási csoport | Válassza az **Új létrehozása** lehetőséget. </br> Írja be **a myAvailabilitySet2 nevet** **a névbe.** </br> Válassza az **OK** lehetőséget. |
    | Kép | Válassza a **Windows Server 2019 Datacenter - Gen1 lehetőséget.** |
    | Azure Spot-példány | Ne jelölje be az alapértelmezett jelölőnégyzetet. |
    | Méret | Válassza ki a virtuális gép méretét. |
    | **Rendszergazdai fiók** |   |
    | Felhasználónév | Adjon meg egy felhasználónevet. |
    | Jelszó | Adjon meg egy jelszót. |
    | **Bejövőport-szabályok** |   |
    | Nyilvános bejövő portok | Válassza a **Nincs** lehetőséget. |

4. Válassza **a Hálózat** lapfület, vagy kattintson a **Tovább: Lemezek,** majd a **Tovább:** Hálózatkezelés gombra a lap alján.

5. A **Hálózat lapon** adja meg vagy válassza ki a következő adatokat:

    | Beállítás | Érték |
    | ------- | ----- |
    | **Hálózati adapter** |   |
    | Virtuális hálózat | Válassza **a myVNet lehetőséget.** |
    | Alhálózat | Válassza **a myBackendSubnet lehetőséget.** |
    | Nyilvános IP-cím | Válassza a **Nincs** lehetőséget. |
    | Hálózati adapter hálózati biztonsági csoportja | Válassza az **Advanced** (Speciális) lehetőséget. |
    | Hálózati biztonsági csoport konfigurálása | Válassza **a myNSG lehetőséget.** | 
    | **Terheléselosztás** |   |
    | Ezt a virtuális gépet egy meglévő terheléselosztási megoldás mögé helyezze? | Jelölje be a jelölőnégyzetet. |
    | **Terheléselosztási beállítások** |   |
    | Terheléselosztási lehetőségek | Válassza **az Azure Load Balancer lehetőséget.** |
    | Terheléselosztás kiválasztása | Válassza **a myLoadBalancer lehetőséget.** |
    | Háttérkészlet kiválasztása | Válassza **a myBackendPool lehetőséget.** |

6. Válassza az **Áttekintés + létrehozás** lapot, vagy kattintson az oldal alján található kék Felülvizsgálat **+** létrehozás gombra.

7. Válassza a **Létrehozás** lehetőséget.

8. Ismételje meg az 1–7. lépést a készlet második virtuális gépének létrehozásához. Cserélje le a virtuális gép beállításait a következő információkra:

    | Beállítás | Érték |
    | ------- | ----- |
    | Név | Adja meg **a myVM4-et.** |
    | Rendelkezésre állási csoport | Válassza **a myAvailabilitySet2 lehetőséget.** |
    | Virtual Network | Válassza **a myVNet lehetőséget.** |
    | Hálózati biztonsági csoporttal | Válassza **a myNSG lehetőséget.** |
    | Terheléselosztási lehetőségek | Válassza **az Azure Load Balancer lehetőséget.** |
    | Terheléselosztás kiválasztása | Válassza **a myLoadBalancer lehetőséget.** |
    | Háttérkészlet kiválasztása | Válassza **a myBackendPool lehetőséget.** |

## <a name="install-iis"></a>Az IIS telepítése

Ebben a szakaszban a korábban létrehozott Azure Bastion fogja használni a virtuális gépekhez való csatlakozáshoz és az IIS telepítéséhez.

1. A portál tetején található keresőmezőbe írja be a **Következőt: Virtuális gép.**

2. A **keresési eredmények között** válassza a Virtuális gépek lehetőséget.

3. Válassza **a myVM1 lehetőséget.**

4. A myVM1 **Áttekintés** lapján válassza a   >  **Bastion csatlakoztatása lehetőséget.**

5. Válassza **a Bastion használata lehetőséget.**

6. Adja meg **a virtuális** gép **létrehozásakor** létrehozott felhasználónevet és jelszót.

7. Válassza a **Kapcsolódás** lehetőséget.

7. A kiszolgáló asztalán lépjen a **Windows felügyeleti eszközök Windows PowerShell.**  >  

8. A PowerShell-ablakban futtassa a következő parancsokat a következők hez:

    * Az IIS-kiszolgáló telepítése
    * Az alapértelmezett iisstart.htm eltávolítása
    * Adjon hozzá egy iisstart.htm, amely megjeleníti a virtuális gép nevét:

   ```powershell
    # Install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
    Remove-Item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
    Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
8. Zárja be a Bastion-munkamenetet a **myVM1-hez.**

9. Ismételje meg az 1–8. lépést **a myVM2,** **myVM3** és **myVM4 esetén** is.

## <a name="test-the-load-balancer"></a>A terheléselosztó tesztelése

Ebben a szakaszban a terheléselosztás nyilvános IP-címét fogja felderítni. A terheléselosztási művelet tesztelésére az IP-címet fogja használni.

1. A portál tetején található keresőmezőbe írja be a Nyilvános **IP-címet.**

2. A **keresési eredmények között válassza a** Nyilvános IP-címek lehetőséget.

3. Válassza **a myPublicIP-lb lehetőséget.**

4. Jegyezze fel a nyilvános IP-címet a **myPublicIP-lb** Áttekintés lapján található **IP-cím** alatt: 

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/find-public-ip.png" alt-text="Keresse meg a terheléselosztás nyilvános IP-címét." border="true":::

5. Nyisson meg egy webböngészőt, és írja be a nyilvános IP-címet a címsorba:

    :::image type="content" source="./media/tutorial-multi-availability-sets-portal/verify-load-balancer.png" alt-text="A terheléseltöltő tesztelése webböngészővel." border="true":::

6. Válassza a frissítés lehetőséget a böngészőben a háttérkészlet többi virtuális gépe között kiegyensúlyozott forgalom kiválasztásához.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja az alkalmazás használatát, a következő lépésekkel törölje a terheléselosztást és a támogató erőforrásokat:

1. A portál tetején található keresőmezőbe írja be a következőt: **Erőforráscsoport.**

2. A **keresési eredmények között** válassza az Erőforráscsoportok lehetőséget.

3. Válassza **a MertelBmultiAVS-rg lehetőséget.**

4. A Lap **LapBmultiAVS-rg lapján válassza** az **Erőforráscsoport törlése lehetőséget.**

5. Írja **be a Következőt: ÉvABmultiAVS-rg,** **írja be AZ ERŐFORRÁSCSOPORT NEVÉT.**

6. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

Az oktatóanyag során az alábbi lépéseket fogja végrehajtani:

* Létrehozott egy virtuális hálózatot, és Azure Bastion gazdagépet.
* Létrehozott egy Azure-standard Load Balancer.
* Két rendelkezésre állási készletet hozott létre készletenként két virtuális gépből.
* Telepítette az IIS-t, és tesztelte a terheléseltöltőt.

A következő cikkből megtudhatja, hogyan hozhat létre régiók közötti Azure Load Balancer:
> [!div class="nextstepaction"]
> [Régiók közötti terheléselosztás létrehozása](tutorial-cross-region-portal.md)

