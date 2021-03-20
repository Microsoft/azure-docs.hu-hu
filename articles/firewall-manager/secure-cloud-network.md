---
title: 'Oktatóanyag: virtuális központ biztonságossá tétele a Azure Firewall Managerrel'
description: Ebből az oktatóanyagból megtudhatja, hogyan védheti a virtuális központot Azure Firewall Managerrel a Azure Portal használatával.
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: tutorial
ms.date: 09/08/2020
ms.author: victorh
ms.openlocfilehash: 9d1e2d257074555e7a2e78930e1f9be6cd4d90fe
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "89536002"
---
# <a name="tutorial-secure-your-virtual-hub-using-azure-firewall-manager"></a>Oktatóanyag: virtuális központ biztonságossá tétele a Azure Firewall Managerrel

A Azure Firewall Manager használatával biztonságos virtuális hubokat hozhat létre, amelyekkel biztonságossá teheti a Felhőbeli hálózati forgalmat a magánhálózati IP-címekre, az Azure Pástire és az internetre. A tűzfal felé irányuló forgalom-útválasztás automatizált, így nem kell felhasználó által megadott útvonalakat (UDR-ket) létrehoznia.

![a felhőalapú hálózat védelme](media/secure-cloud-network/secure-cloud-network.png)

A Firewall Manager egy hub virtuális hálózati architektúrát is támogat. A biztonságos virtuális központ és a hub virtuális hálózati architektúrájának összevetéséhez tekintse meg [a mi a Azure Firewall Manager architektúrájának beállításait?](vhubs-and-vnets.md)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A küllős virtuális hálózat létrehozása
> * Biztonságos virtuális központ létrehozása
> * A hub és a küllős virtuális hálózatok összekötése
> * Forgalom irányítása a hubhoz
> * A kiszolgálók üzembe helyezése
> * Tűzfal-házirend létrehozása és a központ biztonságossá tétele
> * A tűzfal tesztelése

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-hub-and-spoke-architecture"></a>Sugaras architektúra létrehozása

Először is hozzon létre küllős virtuális hálózatokat, ahol elhelyezheti a kiszolgálókat.

### <a name="create-two-spoke-virtual-networks-and-subnets"></a>Két küllős virtuális hálózat és alhálózat létrehozása

A két virtuális hálózat mindegyike rendelkezik egy munkaterhelés-kiszolgálóval, és a tűzfal fogja védeni őket.

1. Az Azure Portal kezdőlapján válassza az **erőforrás létrehozása** lehetőséget.
2. A **hálózat** területen válassza a **virtuális hálózat** lehetőséget.
2. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **erőforráscsoport** területen válassza az **új létrehozása** lehetőséget, és írja be a név mezőbe az **FW-Manager** nevet, majd kattintson **az OK gombra**.
2. A **név** mezőbe írja be a **küllő-01** értéket.
3. A régió területen válassza az USA **keleti** **régióját**.
4. Válassza a **Tovább: IP-címek** lehetőséget.
1. A **címterület** mezőbe írja be a következőt: **10.1.0.0/16**.
3. Válassza az **alhálózat hozzáadása** lehetőséget.
4. Írja be a **munkaterhelés-01-SN** értéket.
5. **Alhálózati címtartomány** esetében írja be a következőt: **10.1.1.0/24**.
6. Válassza a **Hozzáadás** lehetőséget.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
2. Válassza a **Létrehozás** lehetőséget.

Ismételje meg ezt az eljárást egy másik hasonló virtuális hálózat létrehozásához:

Név: **küllős 02**<br>
Címterület: **10.2.0.0/16**<br>
Alhálózat neve: **munkaterhelés-02-SN**<br>
Alhálózati címtartomány: **10.2.1.0/24**

### <a name="create-the-secured-virtual-hub"></a>A biztonságos virtuális központ létrehozása

Hozza létre a biztonságos virtuális hubot a Firewall Manager használatával.

1. A Azure Portal kezdőlapon válassza a **minden szolgáltatás** lehetőséget.
2. A keresőmezőbe írja be a **Firewall Manager** kifejezést, majd válassza a **Firewall Manager** lehetőséget.
3. A **Firewall Manager** lapon válassza a **biztonságos virtuális hubok megtekintése** lehetőséget.
4. A **Firewall Managerben | Biztonságos virtuális hubok** lapon válassza az **új biztonságos virtuális központ létrehozása** lehetőséget.
5. Az **erőforráscsoport** területen válassza az **FW-Manager** lehetőséget.
7. A régió területen válassza az **USA keleti** **régiója** lehetőséget.
1. A **biztonságos virtuális központ neve** mezőbe írja be a következőt: **hub-01**.
2. A **hub címterület** mezőbe írja be a következőt: **10.0.0.0/16**.
3. Az új vWAN neve mezőbe írja be a következőt: **vWAN-01**.
4. Hagyja bejelölve a **VPN Gateway belefoglalása a megbízható biztonsági partnerek engedélyezése** jelölőnégyzetet.
5. Válassza a Next (tovább) lehetőséget **: Azure Firewall**.
6. Fogadja el az alapértelmezett **Azure Firewall** **engedélyezve** beállítást, majd válassza a **Tovább: megbízható biztonsági partner** lehetőséget.
7. Fogadja el az alapértelmezett **megbízható biztonsági partner** **letiltott** beállítást, majd kattintson a **Tovább gombra: felülvizsgálat + létrehozás**.
8. Válassza a **Létrehozás** lehetőséget. A telepítés körülbelül 30 percet vesz igénybe.

Most már lekérheti a tűzfal nyilvános IP-címét.

1. Az üzembe helyezés befejezése után a Azure Portal válassza a **minden szolgáltatás** lehetőséget.
1. Írja be a **Firewall Managert** , majd válassza a **Firewall Manager** lehetőséget.
2. Válassza a **biztonságos virtuális hubok** lehetőséget.
3. Válassza a **hub-01** elemet.
7. Válassza a **nyilvános IP-konfiguráció** lehetőséget.
8. Jegyezze fel a nyilvános IP-címet, amelyet később használni szeretne.

### <a name="connect-the-hub-and-spoke-virtual-networks"></a>A hub és a küllős virtuális hálózatok összekötése

Most már elvégezheti a hub és a küllős virtuális hálózatok összevonását.

1. Válassza ki az **FW-Manager** erőforráscsoportot, majd válassza ki a **Vwan-01** virtuális WAN elemet.
2. A **kapcsolat** területen válassza a **virtuális hálózati kapcsolatok** elemet.
3. Válassza a **kapcsolatok hozzáadása** lehetőséget.
4. A **kapcsolatok neve** mezőbe írja be a következőt: **hub-küllő-01**.
5. **Hubok** esetében válassza a **hub-01** elemet.
6. Az **erőforráscsoport** területen válassza az **FW-Manager** lehetőséget.
7. **Virtuális hálózat** esetén válassza a **küllő-01** elemet.
8. Válassza a **Létrehozás** lehetőséget.

Ismételje meg a **küllő-02** virtuális hálózat csatlakoztatását: kapcsolat neve- **hub-küllő-02**

## <a name="deploy-the-servers"></a>A kiszolgálók üzembe helyezése

1. A Azure Portal válassza az **erőforrás létrehozása** lehetőséget.
2. Válassza a **Windows Server 2016 Datacenter** elemet a **népszerű** listában.
3. Adja meg a következő értékeket a virtuális gép számára:

   |Beállítás  |Érték  |
   |---------|---------|
   |Erőforráscsoport     |**FW – kezelő**|
   |Virtuális gép neve     |**SRV-munkaterhelés-01**|
   |Region     |**USA USA keleti régiója)**|
   |Rendszergazda felhasználóneve     |adja meg a felhasználónevet|
   |Jelszó     |írjon be egy jelszót|

4. A **bejövő portszabályok** területen a **nyilvános bejövő portok** esetében válassza a **nincs** lehetőséget.
6. Fogadja el a többi alapértelmezett értéket, és válassza a **Tovább: lemezek** lehetőséget.
7. Fogadja el a lemez alapértelmezett értékeit, és válassza a **Tovább: hálózatkezelés** lehetőséget.
8. Válassza a **küllő-01** lehetőséget a virtuális hálózathoz, és válassza a **munkaterhelés-01-SN** lehetőséget az alhálózat számára.
9. A **nyilvános IP-címek** esetében válassza a **nincs** lehetőséget.
11. Fogadja el a többi alapértelmezett értéket, és válassza a **Tovább: kezelés** lehetőséget.
12. A rendszerindítási diagnosztika letiltásához válassza a **ki** lehetőséget. Fogadja el a többi alapértelmezett értéket, és válassza a **felülvizsgálat + létrehozás** lehetőséget.
13. Tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás** lehetőséget.

A következő táblázatban található információk segítségével konfigurálhat egy **SRV-munkaterhelés-02** nevű virtuális gépet. A többi konfiguráció ugyanaz, mint az **SRV-munkaterhelés-01** virtuális gép.

|Beállítás  |Érték  |
|---------|---------|
|Virtuális hálózat|**Küllős 02**|
|Alhálózat|**Munkaterhelés – 02 – SN**|

A kiszolgálók üzembe helyezése után válasszon ki egy kiszolgálói erőforrást, és a **hálózatban** jegyezze fel az egyes kiszolgálók magánhálózati IP-címét.

## <a name="create-a-firewall-policy-and-secure-your-hub"></a>Tűzfal-házirend létrehozása és a központ biztonságossá tétele

A tűzfalszabályok olyan szabályok gyűjteményeit határozzák meg, amelyek egy vagy több biztonságos virtuális hubhoz irányítják a forgalmat. Hozza létre a tűzfal-házirendet, majd gondoskodjon a központ biztonságossá tételéről.

1. A Firewall Managerben válassza a **Azure Firewall házirendek megtekintése** lehetőséget.
2. Válassza a **Azure Firewall házirend létrehozása** lehetőséget.
3. A szabályzat **részletei** területen, a **következő név** : **Policy-01** és régió esetén válassza az **USA keleti** **régiója** lehetőséget.
4. Válassza a **Tovább: DNS-beállítások (előzetes verzió)** lehetőséget.
1. Válassza a **Tovább: szabályok** lehetőséget.
2. A **szabályok** lapon válassza a **szabálygyűjtemény hozzáadása** elemet.
3. A **szabálygyűjtemény hozzáadása** lapon írja be az **app-RC-01** **nevet a név** mezőbe.
4. A **szabály gyűjtemény típusa** beállításnál válassza az **alkalmazás** lehetőséget.
5. A **priority**( **100**) mezőbe írja be a következőt:.
6. Győződjön meg arról, hogy a **szabályok gyűjtésének engedélyezése művelet** **engedélyezett**.
7. A szabály **neveként** írja be a következőt: **Allow-MSFT**.
8. A **forrás típusa** beállításnál válassza az **IP-cím** lehetőséget.
9. A **forrás** mezőbe írja be a következőt: **\*** .
10. A **protokoll** mezőben írja be a következőt: **http, HTTPS**.
11. Győződjön meg arról, hogy a **célként megadott típus** **FQDN**.
12. A **cél** mezőbe írja be a következőt: **\* . microsoft.com**.
13. Válassza a **Hozzáadás** lehetőséget.

Adjon hozzá egy DNAT-szabályt, hogy távoli asztalt csatlakoztasson az **SRV-munkaterhelés-01** virtuális géphez.

1. Válassza **a szabálygyűjtemény hozzáadása** lehetőséget.
2. A **név** mezőbe írja be a következőt: **DNAT-RDP**.
3. A **szabály gyűjtemény típusa** beállításnál válassza a **DNAT** lehetőséget.
4. A **priority**( **100**) mezőbe írja be a következőt:.
5. A szabály **neveként** írja be a következőt: **Allow-RDP**.
6. A **forrás típusa** beállításnál válassza az **IP-cím** lehetőséget.
7. A **forrás** mezőbe írja be a következőt: **\*** .
8. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
9. A **célport** mezőbe írja be a következőt: **3389**.
10. A **cél típusa** beállításnál válassza az **IP-cím** lehetőséget.
11. A **cél** mezőbe írja be a korábban feljegyzett tűzfal nyilvános IP-címét.
12. A **lefordított cím** mezőbe írja be a korábban feljegyzett **SRV-munkaterhelés-01** magánhálózati IP-címet.
13. A **Lefordított port** mezőben adja meg a **3389** értéket.
14. Válassza a **Hozzáadás** lehetőséget.

Adjon hozzá egy hálózati szabályt, hogy a Távoli asztalt a **SRV-munkaterhelés-01** -ről **SRV-munkaterhelés-02-** re lehessen kapcsolni.

1. Válassza **a szabálygyűjtemény hozzáadása** lehetőséget.
2. A **név** mezőbe írja be a következőt: **vnet-RDP**.
3. A **szabály gyűjtemény típusa** beállításnál válassza a **hálózat** lehetőséget.
4. A **priority**( **100**) mezőbe írja be a következőt:.
5. A szabály **neveként** írja be a következőt: **Allow-vnet**.
6. A **forrás típusa** beállításnál válassza az **IP-cím** lehetőséget.
7. A **forrás** mezőbe írja be a következőt: **\*** .
8. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
9. A **célport** mezőbe írja be a következőt: **3389**.
9. A **cél típusa** beállításnál válassza az **IP-cím** lehetőséget.
10. A **cél** mezőbe írja be a korábban feljegyzett **SRV-munkaterhelés-02** magánhálózati IP-címet.
11. Válassza a **Hozzáadás** lehetőséget.
1. Válassza a **Tovább: fenyegetés intelligencia** lehetőséget.
2. Válassza a **Next (tovább): hubok** lehetőséget.
3. A **hubok** lapon válassza a **virtuális hubok hozzárendelése** lehetőséget.
4. Válassza a **hub-01** elemet, majd kattintson a **Hozzáadás** gombra.
5. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
6. Válassza a **Létrehozás** lehetőséget.

Ez körülbelül öt percet vesz igénybe.

## <a name="route-traffic-to-your-hub"></a>Forgalom irányítása a hubhoz

Most meg kell győződnie arról, hogy a hálózati forgalom a tűzfalon keresztül lesz átirányítva.

1. A Firewall Managerben válassza a **biztonságos virtuális hubok** lehetőséget.
2. Válassza a **hub-01** elemet.
3. A **Beállítások** területen válassza a **biztonsági konfiguráció** elemet.
4. Az **internetes forgalom** területen válassza a **Azure Firewall** lehetőséget.
5. A **privát forgalom** területen válassza a **Küldés Azure Firewall használatával** lehetőséget.
10. Győződjön meg arról, hogy a **sugaras** kapcsolatok **biztonságosként** jeleníti meg az **internetes forgalmat** .
11. Kattintson a **Mentés** gombra.


## <a name="test-your-firewall"></a>A tűzfal tesztelése

A tűzfalszabályok teszteléséhez egy távoli asztalt kell összekapcsolnia a tűzfal nyilvános IP-címével, amely az **SRV-munkaterhelés-01-** re van felhasználva. Innen egy böngészővel tesztelheti az alkalmazás szabályát, és összekapcsolhat egy távoli asztalt a **SRV-munkaterhelés-02** használatával a hálózati szabály teszteléséhez.

### <a name="test-the-application-rule"></a>Az alkalmazás szabályának tesztelése

A tűzfalszabályok tesztelésével ellenőrizze, hogy az a vártnak megfelelően működik-e.

1. Csatlakoztasson egy távoli asztalt a tűzfal nyilvános IP-címéhez, és jelentkezzen be.

3. Nyissa meg az Internet Explorert, és navigáljon a következő címre: https://www.microsoft.com.
4.   >  Az Internet Explorer biztonsági riasztások ablakában kattintson az OK **Bezárás** gombra.

   A Microsoft kezdőlapjának kell megjelennie.

5. Nyissa meg a következő címet: https://www.google.com.

   A tűzfal blokkolja a hozzáférést.

Most ellenőrizte, hogy a tűzfalszabály működik-e:

* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.

### <a name="test-the-network-rule"></a>A hálózati szabály tesztelése

Most tesztelje a hálózati szabályt.

- Nyisson meg egy távoli asztalt az **SRV-munkaterhelés-02** magánhálózati IP-címhez.

   A távoli asztalnak csatlakoznia kell az **SRV-munkaterhelés-02**-hez.

Most ellenőrizte, hogy a tűzfal hálózati szabálya működik-e:
* A távoli asztal egy másik virtuális hálózatban található kiszolgálóhoz is csatlakoztatható.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha végzett a tűzfal erőforrásainak tesztelésével, törölje a **FW-Manager** erőforráscsoportot az összes tűzfalhoz kapcsolódó erőforrás törléséhez.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [További információ a megbízható biztonsági partnerekről](trusted-security-partners.md)
