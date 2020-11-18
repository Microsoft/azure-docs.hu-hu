---
title: 'Oktatóanyag: a bejövő internetes forgalom szűrése Azure Firewall DNAT a portál használatával'
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és konfigurálhatja az Azure Firewall DNAT-ot az Azure Portalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 08/28/2020
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: 71eda40abd38b4885b8e88085e338667b608902f
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655136"
---
# <a name="tutorial-filter-inbound-internet-traffic-with-azure-firewall-dnat-using-the-azure-portal"></a>Oktatóanyag: a bejövő internetes forgalom szűrése Azure Firewall DNAT a Azure Portal használatával

Az Azure Firewall DNAT (Destination Network Address Translation, célhálózati címfordítás) funkciójának konfigurálásával lefordíthatja és szűrheti az alhálózatokra bejövő internetes forgalmat. A DNAT konfigurálásakor a NAT-szabálygyűjtemény művelete **DNAT** értékre van állítva. A NAT-szabálygyűjtemény minden szabálya használható arra, hogy lefordítsa a tűzfal nyilvános IP-címét és portját egy magánhálózati IP-címre és portra. A DNAT-szabályok implicit módon hozzáadnak egy kapcsolódó hálózati szabályt a lefordított adatforgalom engedélyezéséhez. Ezt a viselkedést felülírhatja, ha explicit módon hozzáad egy hálózatiszabály-készletet, amely megtagadja azokat a szabályokat, amelyek a lefordított adatforgalomhoz tartoznak. Az Azure Firewall szabályfeldolgozási logikájával kapcsolatos további információkért tekintse meg az [Azure Firewall szabályfeldolgozási logikájával](rule-processing.md) kapcsolatos cikket.

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
3. Az **Erőforráscsoport neve** mezőbe írja be a következőt: **RG-DNAT-Test**.
4. Az **Előfizetés** beállításnál válassza ki az előfizetését.
5. Az **Erőforráscsoport helye** beállításnál válasszon ki egy helyet. Minden ezután létrehozott erőforrásnak ugyanezen a helyen kell lennie.
6. Kattintson a **Létrehozás** gombra.

## <a name="set-up-the-network-environment"></a>A hálózati környezet beállítása

Ebben az oktatóanyagban két társított virtuális hálózatot hozunk létre:

- **VN-Hub** (központi virtuális hálózat) – ezen a virtuális hálózaton található a tűzfal.
- **VN-Spoke** (küllő virtuális hálózat) – ezen a virtuális hálózaton található a számítási feladat kiszolgálója.

Először hozza létre a virtuális hálózatokat, és társítsa őket.

### <a name="create-the-hub-vnet"></a>A központi virtuális hálózat létrehozása

1. A Azure Portal kezdőlapon válassza a **minden szolgáltatás** lehetőséget.
2. A **hálózat** területen válassza a **virtuális hálózatok** lehetőséget.
3. Válassza a **Hozzáadás** elemet.
4. A **Név** mezőbe írja be a következőt: **VN-Hub**.
5. A **Címtér** mezőbe írja be a következőt: **10.0.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **AzureFirewallSubnet**.

     Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
     > [!NOTE]
     > A AzureFirewallSubnet-alhálózat mérete/26. További információ az alhálózat méretétől: [Azure Firewall GYIK](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

10. A **címtartomány** mezőbe írja be a következőt: **10.0.1.0/26**.
11. Használja a többi alapértelmezett beállítást, majd kattintson a **Létrehozás** gombra.

### <a name="create-a-spoke-vnet"></a>Küllő virtuális hálózat létrehozása

1. A Azure Portal kezdőlapon válassza a **minden szolgáltatás** lehetőséget.
2. A **hálózat** területen válassza a **virtuális hálózatok** lehetőséget.
3. Válassza a **Hozzáadás** elemet.
4. A **Név** mezőbe írja be a következőt: **VN-Spoke**.
5. A **Címtér** mezőbe írja be a következőt: **192.168.0.0/16**.
6. Az **Előfizetés** beállításnál válassza ki az előfizetését.
7. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
8. A **Hely** elemnél válassza a korábban használt helyet.
9. Az **Alhálózat** területen a **Név** mezőbe írja be a következőt: **SN-Workload**.

    Ezen az alhálózaton lesz a kiszolgáló.
10. A **Címtartomány** mezőbe írja be a következőt: **192.168.1.0/24**.
11. Használja a többi alapértelmezett beállítást, majd kattintson a **Létrehozás** gombra.

### <a name="peer-the-vnets"></a>A virtuális hálózatok társítása

Most társítsa a két virtuális hálózatot.

1. Válassza ki a **vn-hub** virtuális hálózatot.
2. A **Beállítások** **területen válassza a** társítások lehetőséget.
3. Válassza a **Hozzáadás** elemet.
4. Írja be a **társ-HubSpoke** **nevet a VN-Hubról a vn-küllőre**.
5. A virtuális hálózatnál válassza a **VN-Spoke** lehetőséget.
6. Írja be a **társ-SpokeHub** **nevet a VN-Spoke és a vn-hub között**.
7. A **VN-Spokeról a vn-hub-ra való továbbított forgalom engedélyezéséhez** válassza az **engedélyezve** lehetőséget.
8. Kattintson az **OK** gombra.

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a számítási feladat futtatásához, és helyezze el az **SN-Workload** alhálózaton.

1. Az Azure Portal menüjében válassza az **Erőforrás létrehozása** elemet.
2. A **népszerű** területen válassza a **Windows Server 2016 Datacenter** elemet.

**Alapbeállítások**

1. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
1. A **virtuális gép neve** mezőbe írja be az **SRV-munkaterhelés** nevet.
1. A **régió** mezőben válassza ki ugyanazt a helyet, amelyet korábban használt.
1. Adjon meg egy felhasználónevet és jelszót.
1. Válassza a **Tovább: lemezek** lehetőséget.

**Lemezek**
1. Válassza a **Tovább: Hálózatkezelés** lehetőséget.

**Hálózat**

1. **Virtuális hálózat** esetén válassza a **vn-küllő** elemet.
2. Az **Alhálózat** mezőnél válassza az **SN-Workload** lehetőséget.
3. A **nyilvános IP-cím** beállításnál válassza a **nincs lehetőséget**.
4. **Nyilvános bejövő portok** esetében válassza a **nincs** lehetőséget. 
2. Hagyja meg a többi alapértelmezett beállítást, és válassza a **Tovább: kezelés** lehetőséget.

**Felügyelet**

1. **Rendszerindítási diagnosztika** esetén válassza a **ki** lehetőséget.
1. Válassza a **felülvizsgálat + létrehozás** lehetőséget.

**Felülvizsgálat + létrehozás**

Tekintse át az összegzést, majd kattintson a **Létrehozás** gombra. Ez eltarthat néhány percig.

Az üzembe helyezés befejeztével jegyezze fel a virtuális gép magánhálózati IP-címét. Ezt később a tűzfal konfigurálása során használjuk majd. Válassza ki a virtuális gép nevét, és a **Beállítások** területen válassza a **hálózatkezelés** lehetőséget a magánhálózati IP-cím megkereséséhez.

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

1. A portál kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. Válassza a **hálózatkezelés** lehetőséget, majd a **Kiemelt** beállításnál válassza **az összes** megjelenítése lehetőséget.
3. Válassza a **tűzfal**, majd a **Létrehozás** lehetőséget. 
4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Érték  |
   |---------|---------|
   |Név     |FW-DNAT-test|
   |Előfizetés     |\<your subscription\>|
   |Erőforráscsoport     |**Meglévő használata**: RG-DNAT-Test |
   |Hely     |Válassza a korábban használt helyet|
   |Válasszon egy virtuális hálózatot     |**Meglévő használata**: VN-Hub|
   |Nyilvános IP-cím     |**Új létrehozása**. A nyilvános IP-címnek standard termékváltozat típusúnak kell lennie.|

5. Válassza a **Felülvizsgálat + létrehozás** lehetőséget.
6. Tekintse át az összegzést, majd válassza a **Létrehozás** lehetőséget a tűzfal létrehozásához.

   Az üzembe helyezés néhány percet vesz igénybe.
7. Az üzembe helyezés befejeződése után nyissa meg a **RG-DNAT-test** erőforráscsoportot, és válassza ki az **FW-DNAT-test** tűzfalat.
8. Jegyezze fel a magánhálózati IP-címet. Később, az alapértelmezett útvonal létrehozásakor szükség lesz rá.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

Az **SN-Workload** alhálózatot konfigurálja úgy, hogy a kimenő alapértelmezett útvonal áthaladjon a tűzfalon.

1. A Azure Portal kezdőlapon válassza a **minden szolgáltatás** lehetőséget.
2. A **hálózat** területen válassza az **útválasztási táblák** elemet.
3. Válassza a **Hozzáadás** elemet.
4. A **Név** mezőbe írja be a következőt: **RT-FWroute**.
5. Az **Előfizetés** beállításnál válassza ki az előfizetését.
6. Az **Erőforráscsoport** mezőben válassza a **Meglévő használata**, majd az **RG-DNAT-Test** lehetőséget.
7. A **Hely** elemnél válassza a korábban használt helyet.
8. Kattintson a **Létrehozás** gombra.
9. Válassza a **frissítés** lehetőséget, majd válassza ki az **RT-FWroute** útválasztási táblázatot.
10. Válassza ki az **alhálózatok** elemet, majd válassza a **hozzárendelés** lehetőséget.
11. Válassza a **virtuális hálózat** lehetőséget, majd válassza a **vn-küllő** elemet.
12. Az **Alhálózat** mezőnél válassza az **SN-Workload** lehetőséget.
13. Kattintson az **OK** gombra.
14. Válassza az **útvonalak** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
15. Az **Útvonal neve** mezőbe írja be következőt: **FW-DG**.
16. A **Címelőtag** mezőbe írja be a következőt: **0.0.0.0/0**.
17. A **Következő ugrás típusa** beállításnál válassza a **Virtuális berendezés** lehetőséget.

    Az Azure Firewall valójában egy felügyelt szolgáltatás, de ebben a helyzetben a virtuális berendezés beállítás is használható.
18. A **Következő ugrás címe** mezőbe írja be a tűzfal magánhálózati IP-címét, amelyet korábban feljegyzett.
19. Kattintson az **OK** gombra.

## <a name="configure-a-nat-rule"></a>NAT-szabály konfigurálása

1. Nyissa meg a **RG-DNAT-test** elemet, és válassza ki az **FW-DNAT-test** tűzfalat. 
2. Az **FW-DNAT-test** oldalon a **Beállítások** területen válassza a **szabályok** elemet. 
3. Válassza a **NAT-szabálygyűjtemény hozzáadása** lehetőséget. 
4. A **Név** mezőbe írja be a következőt: **RC-DNAT-01**. 
5. A **Prioritás** mezőbe írja be a következőt: **200**. 
6. A **Szabályok** területen a **Név** mezőbe írja be a következőt: **RL-01**.
7. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
8. A **Forráscímek** mezőbe írja be a következőt: *. 
9. A **Célcímek** mezőbe írja be a tűzfal nyilvános IP-címét. 
10. A **Célportok** mezőbe írja be a következőt: **3389**. 
11. A **Lefordított cím** mezőbe írja be az Srv-Workload virtuális gép magánhálózati IP-címét. 
12. A **Lefordított port** mezőben adja meg a **3389** értéket. 
13. Válassza a **Hozzáadás** elemet. 

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