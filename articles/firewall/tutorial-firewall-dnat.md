---
title: 'Oktatóanyag: a bejövő internetes forgalom szűrése Azure Firewall DNAT a portál használatával'
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és konfigurálhatja az Azure Firewall DNAT-ot az Azure Portalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 03/01/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: a1d3bdae1e870b094472a63d4b808d9df95c129d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101741905"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Oktatóanyag: a bejövő internetes forgalom szűrése Azure Firewall DNAT a Azure Portal használatával

Az Azure Firewall DNAT (Destination Network Address Translation, célhálózati címfordítás) funkciójának konfigurálásával lefordíthatja és szűrheti az alhálózatokra bejövő internetes forgalmat. A DNAT konfigurálásakor a NAT-szabálygyűjtemény művelete **DNAT** értékre van állítva. Ezután a NAT-szabálygyűjtemény minden szabálya használható a tűzfal nyilvános IP-címének és portjának egy magánhálózati IP-címhez és porthoz való lefordításához. A DNAT-szabályok implicit módon hozzáadnak egy kapcsolódó hálózati szabályt a lefordított adatforgalom engedélyezéséhez. Biztonsági okokból az ajánlott módszer egy adott internetes forrás hozzáadása, amely lehetővé teszi a DNAT hozzáférését a hálózathoz, és a helyettesítő karakterek használatának elkerülését. Az Azure Firewall szabályfeldolgozási logikájával kapcsolatos további információkért tekintse meg az [Azure Firewall szabályfeldolgozási logikájával](rule-processing.md) kapcsolatos cikket.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * DNAT-szabály konfigurálása
> * A tűzfal tesztelése

## <a name="prerequisites"></a>Előfeltételek

Ha még nincs Azure-előfizetése, kezdés előtt hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).



## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. A Azure Portal kezdőlapon válassza az **erőforráscsoportok**, majd a **Hozzáadás** lehetőséget.
4. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **Erőforráscsoport neve** mezőbe írja be a következőt: **RG-DNAT-Test**.
5. A **régió** területen válasszon ki egy régiót. Minden más létrehozott erőforrásnak ugyanabban a régióban kell lennie.
6. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

## <a name="set-up-the-network-environment"></a>A hálózati környezet beállítása

Ebben az oktatóanyagban két társított virtuális hálózatot hozunk létre:

- **VN-Hub** (központi virtuális hálózat) – ezen a virtuális hálózaton található a tűzfal.
- **VN-Spoke** (küllő virtuális hálózat) – ezen a virtuális hálózaton található a számítási feladat kiszolgálója.

Először hozza létre a virtuális hálózatokat, és társítsa őket.

### <a name="create-the-hub-vnet"></a>A központi virtuális hálózat létrehozása

1. A Azure Portal kezdőlapon válassza a **minden szolgáltatás** lehetőséget.
2. A **hálózat** területen válassza a **virtuális hálózatok** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
7. Az **erőforráscsoport** területen válassza a **RG-DNAT-test** elemet.
1. A **Név** mezőbe írja be a következőt: **VN-Hub**.
1. A **régió** mezőben válassza ki ugyanazt a régiót, amelyet korábban használt.
1. Válassza a **Tovább: IP-címek** lehetőséget.
1. **IPv4-címterület** esetén fogadja el az alapértelmezett **10.0.0.0/16** értéket.
1. Az **alhálózat neve** területen válassza az alapértelmezett lehetőséget.
1. Szerkessze az **alhálózat nevét** , és írja be a következőt: **AzureFirewallSubnet**.

     Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
     > [!NOTE]
     > A AzureFirewallSubnet-alhálózat mérete/26. További információ az alhálózat méretétől: [Azure Firewall GYIK](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

10. **Alhálózati címtartomány** esetében írja be a következőt: **10.0.1.0/26**.
11. Kattintson a **Mentés** gombra.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="create-a-spoke-vnet"></a>Küllő virtuális hálózat létrehozása

1. A Azure Portal kezdőlapon válassza a **minden szolgáltatás** lehetőséget.
2. A **hálózat** területen válassza a **virtuális hálózatok** lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
1. Az **erőforráscsoport** területen válassza a **RG-DNAT-test** elemet.
1. A **Név** mezőbe írja be a következőt: **VN-Spoke**.
1. A **régió** mezőben válassza ki ugyanazt a régiót, amelyet korábban használt.
1. Válassza a **Tovább: IP-címek** lehetőséget.
1. **IPv4-címterület** esetében szerkessze az alapértelmezett értéket, és írja be a **192.168.0.0/16** típust.
1. Válassza az **alhálózat hozzáadása** lehetőséget.
1. Az **alhálózat neveként** írja be a következőt: **SN-munkaterhelés**.
10. **Alhálózati címtartomány** esetében írja be a következőt: **192.168.1.0/24**.
11. Válassza a **Hozzáadás** lehetőséget.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="peer-the-vnets"></a>A virtuális hálózatok társítása

Most társítsa a két virtuális hálózatot.

1. Válassza ki a **vn-hub** virtuális hálózatot.
2. A **Beállítások** **területen válassza a** társítások lehetőséget.
3. Válassza a **Hozzáadás** lehetőséget.
4. A **virtuális hálózat** alatt, a társítási **hivatkozás neveként** írja be a következőt: **társ-HubSpoke**.
5. A **távoli virtuális hálózat** területen a társítási **hivatkozás neve** mezőbe írja be a következőt: **SpokeHub**. 
1. A virtuális hálózatnál válassza a **VN-Spoke** lehetőséget.
1. Fogadja el az összes többi alapértelmezett beállítást, majd kattintson a **Hozzáadás** gombra.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a számítási feladat futtatásához, és helyezze el az **SN-Workload** alhálózaton.

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.
2. A **népszerű** területen válassza a **Windows Server 2016 Datacenter** elemet.

**Alapvető beállítások**

1. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **erőforráscsoport** területen válassza a **RG-DNAT-test** elemet.
1. A **virtuális gép neve** mezőbe írja be az **SRV-munkaterhelés** nevet.
1. A **régió** mezőben válassza ki ugyanazt a helyet, amelyet korábban használt.
1. Adjon meg egy felhasználónevet és jelszót.
1. Válassza a **Tovább: lemezek** lehetőséget.

**Lemezek**
1. Válassza a **Tovább: Hálózatkezelés** lehetőséget.

**Hálózat**

1. **Virtuális hálózat** esetén válassza a **vn-küllő** elemet.
2. Az **Alhálózat** mezőnél válassza az **SN-Workload** lehetőséget.
3. A **nyilvános IP-címek** esetében válassza a **nincs** lehetőséget.
4. **Nyilvános bejövő portok** esetében válassza a **nincs** lehetőséget. 
2. Hagyja meg a többi alapértelmezett beállítást, és válassza a **Tovább: kezelés** lehetőséget.

**Felügyelet**

1. A **rendszerindítási diagnosztika** beállításnál válassza a **Letiltás** lehetőséget.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.

**Felülvizsgálat + létrehozás**

Tekintse át az összegzést, majd kattintson a **Létrehozás** gombra. Ez eltarthat néhány percig.

Az üzembe helyezés befejeztével jegyezze fel a virtuális gép magánhálózati IP-címét. Ezt később a tűzfal konfigurálása során használjuk majd. Válassza ki a virtuális gép nevét, és a **Beállítások** területen válassza a **hálózatkezelés** lehetőséget a magánhálózati IP-cím megkereséséhez.

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

1. A portál kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
1. Keresse meg a **tűzfal** elemet, majd válassza a **tűzfal** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget. 
1. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Érték  |
   |---------|---------|
   |Előfizetés     |\<your subscription\>|
   |Erőforráscsoport     |**RG-DNAT-test** kiválasztása |
   |Name     |**FW-DNAT-test**|
   |Region     |Válassza a korábban használt helyet|
   |Tűzfalak kezelése|**A tűzfalszabályok (klasszikus) használata a tűzfal kezeléséhez**|
   |Válasszon egy virtuális hálózatot     |**Meglévő használata**: VN-Hub|
   |Nyilvános IP-cím     |**Új hozzáadása**, név: **FW-pip**.|

5. Fogadja el a többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.
6. Tekintse át az összegzést, majd válassza a **Létrehozás** lehetőséget a tűzfal létrehozásához.

   Az üzembe helyezés néhány percet vesz igénybe.
7. Az üzembe helyezés befejeződése után nyissa meg a **RG-DNAT-test** erőforráscsoportot, és válassza ki az **FW-DNAT-test** tűzfalat.
8. Jegyezze fel a tűzfal magánhálózati és nyilvános IP-címeit. Ezeket később fogja használni az alapértelmezett útvonal-és NAT-szabály létrehozásakor.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

Az **SN-Workload** alhálózatot konfigurálja úgy, hogy a kimenő alapértelmezett útvonal áthaladjon a tűzfalon.

1. A Azure Portal kezdőlapon válassza a **minden szolgáltatás** lehetőséget.
2. A **hálózat** területen válassza az **útválasztási táblák** elemet.
3. Válassza a **Hozzáadás** lehetőséget.
5. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **erőforráscsoport** területen válassza a **RG-DNAT-test** elemet.
1. A **régió** mezőben válassza ki ugyanazt a régiót, amelyet korábban használt.
1. A **Név** mezőbe írja be a következőt: **RT-FWroute**.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Válassza az **Erőforrás megnyitása** lehetőséget.
1. Válassza ki az **alhálózatok** elemet, majd válassza a **hozzárendelés** lehetőséget.
1. **Virtuális hálózat** esetén válassza a **vn-küllő** elemet.
1. Az **Alhálózat** mezőnél válassza az **SN-Workload** lehetőséget.
1. Válassza az **OK** lehetőséget.
1. Válassza az **útvonalak** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
1. Az **Útvonal neve** mezőbe írja be következőt: **FW-DG**.
1. A **Címelőtag** mezőbe írja be a következőt: **0.0.0.0/0**.
1. A **Következő ugrás típusa** beállításnál válassza a **Virtuális berendezés** lehetőséget.

    Az Azure Firewall valójában egy felügyelt szolgáltatás, de ebben a helyzetben a virtuális berendezés beállítás is használható.
18. A **Következő ugrás címe** mezőbe írja be a tűzfal magánhálózati IP-címét, amelyet korábban feljegyzett.
19. Válassza az **OK** lehetőséget.

## <a name="configure-a-nat-rule"></a>NAT-szabály konfigurálása

1. Nyissa meg a **RG-DNAT-test** erőforráscsoportot, és válassza ki az **FW-DNAT-test** tűzfalat. 
2. Az **FW-DNAT-test** oldalon a **Beállítások** területen válassza a **szabályok (klasszikus)** lehetőséget. 
3. Válassza a **NAT-szabálygyűjtemény hozzáadása** lehetőséget. 
4. A **Név** mezőbe írja be a következőt: **RC-DNAT-01**. 
5. A **Prioritás** mezőbe írja be a következőt: **200**. 
6. A **Szabályok** területen a **Név** mezőbe írja be a következőt: **RL-01**.
7. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
1. A **forrás típusa** beállításnál válassza az **IP-cím** lehetőséget.
1. A **forrás** mezőbe írja be a * értéket. 
1. A **Célhelyek** mezőbe írja be a tűzfal nyilvános IP-címét. 
1. A **Célportok** mezőbe írja be a következőt: **3389**. 
1. A **Lefordított cím** mezőbe írja be az Srv-Workload virtuális gép magánhálózati IP-címét. 
1. A **Lefordított port** mezőben adja meg a **3389** értéket. 
1. Válassza a **Hozzáadás** lehetőséget. Ez eltarthat néhány percig.

## <a name="test-the-firewall"></a>A tűzfal tesztelése

1. Csatlakoztasson egy távoli asztalt a tűzfal nyilvános IP-címére. Ekkor az **Srv-Workload** virtuális gépre kell csatlakoznia.
2. Zárja be a távoli asztalt.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rá szükség, törölje az **RG-DNAT-Test** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * DNAT-szabály konfigurálása
> * A tűzfal tesztelése

A következő lépésben monitorozhatja az Azure Firewall naplóit.

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./firewall-diagnostics.md)
