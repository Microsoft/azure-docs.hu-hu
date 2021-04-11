---
title: 'Oktatóanyag: a Azure Firewall & konfigurálása a Azure Portal használatával'
description: Ebből az oktatóanyagból megtudhatja, hogyan helyezheti üzembe és konfigurálhatja az Azure Firewallt az Azure Portalon.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 04/08/2021
ms.author: victorh
ms.custom: mvc
ms.openlocfilehash: ac9eaea70de67e67ef690c280b30bae22517ac21
ms.sourcegitcommit: b28e9f4d34abcb6f5ccbf112206926d5434bd0da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2021
ms.locfileid: "107227993"
---
# <a name="tutorial-deploy-and-configure-azure-firewall-using-the-azure-portal"></a>Oktatóanyag: Az Azure Firewall üzembe helyezése és konfigurálása az Azure Portalon

A kimenő hálózati hozzáférés ellenőrzése az általános hálózati biztonsági terv fontos részét képezi. Előfordulhat például, hogy korlátozni szeretné a webhelyekhez való hozzáférést. Az is előfordulhat, hogy korlátozni szeretné az elérhető kimenő IP-címeket és portokat.

Az Azure-alhálózatok kimenő hálózati hozzáférése többek között az Azure Firewall használatával vezérelhető. Az Azure Firewall segítségével a következőket konfigurálhatja:

* Alkalmazásszabályokat, amelyek egy alhálózatról elérhető teljes tartományneveket (FQDN) határoznak meg.
* Hálózatszabályokat, amelyek forráscímet, protokollt, valamint célportot és célcímet határoznak meg.

A hálózati forgalmat a konfigurált tűzfalszabályok irányítják, ha alapértelmezett alhálózati átjáróként irányítja a tűzfalhoz a forgalmat.

Ebben az oktatóanyagban egy egyszerűsített VNet hoz létre két alhálózattal az egyszerű üzembe helyezéshez.

Éles környezetekben a [hub és a küllős modell](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) használata ajánlott, ahol a tűzfal a saját VNet van. A munkaterhelés-kiszolgálók egy vagy több alhálózattal azonos régióban lévő, egymással azonos régióba tartozó virtuális hálózatok találhatók.

* **AzureFirewallSubnet** – ezen az alhálózaton található a tűzfal.
* **Workload-SN** – ezen az alhálózaton található a számítási feladat kiszolgálója. Ennek az alhálózatnak a hálózati forgalma a tűzfalon halad át.

![Az oktatóanyag hálózati infrastruktúrája](media/tutorial-firewall-deploy-portal/tutorial-network.png)

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Tesztelési hálózati környezet beállítása
> * Tűzfal üzembe helyezése
> * Alapértelmezett útvonal létrehozása
> * Alkalmazás-szabály konfigurálása a www.google.com való hozzáférés engedélyezéséhez
> * Hálózatszabály konfigurálása külső DNS-kiszolgálókhoz való hozzáférés engedélyezéséhez
> * NAT-szabály konfigurálása a távoli asztal a tesztelési kiszolgálóhoz való engedélyezéséhez
> * A tűzfal tesztelése

Igény szerint az oktatóanyag az [Azure PowerShell](deploy-ps.md) használatával is elvégezhető.

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="set-up-the-network"></a>A hálózat beállítása

Először is hozzon létre egy erőforráscsoportot, amely a tűzfal üzembe helyezéséhez szükséges erőforrásokat tartalmazza. Ezután hozzon létre egy VNet, alhálózatot és egy teszt kiszolgálót.

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Az erőforráscsoport tartalmazza az oktatóanyag összes erőforrását.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. A Azure Portal menüben válassza az **erőforráscsoportok** lehetőséget, vagy keresse meg és válassza ki az *erőforráscsoportok* lehetőséget bármely oldalon. Ezután válassza a **Hozzáadás** elemet.
4. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **erőforráscsoport neve** mezőbe írja be a *test-FW-RG* értéket.
1. Az **Erőforráscsoport helye** beállításnál válasszon ki egy helyet. Az összes létrehozott erőforrásnak azonos helyen kell lennie.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

### <a name="create-a-vnet"></a>Virtuális hálózat létrehozása

Ez a VNet három alhálózattal fog rendelkezni.

> [!NOTE]
> A AzureFirewallSubnet-alhálózat mérete/26. További információ az alhálózat méretétől: [Azure Firewall GYIK](firewall-faq.yml#why-does-azure-firewall-need-a--26-subnet-size).

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
1. Válassza a **hálózatkezelés**  >  **virtuális hálózat** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.
1. Az **Előfizetés** beállításnál válassza ki az előfizetését.
1. Az **erőforráscsoport** területen válassza a **test-FW-RG** elemet.
1. A **Név** mezőbe írja be a következőt: **Test-FW-VN**.
1. A **régió** mezőben válassza ki ugyanazt a helyet, amelyet korábban használt.
1. Válassza a **Tovább: IP-címek** lehetőséget.
1. **IPv4-címterület** esetén írja be a következőt: **10.0.0.0/16**.
1. Az **alhálózat** területen válassza az **alapértelmezett** lehetőséget.
1. Az **alhálózat neveként** írja be a következőt: **AzureFirewallSubnet**. Ezen az alhálózaton lesz a tűzfal. Az alhálózat neve **kizárólag** AzureFirewallSubnet lehet.
1. A **címtartomány** mezőbe írja be a következőt: **10.0.1.0/26**.
1. Kattintson a **Mentés** gombra.

   Ezután hozzon létre egy alhálózatot a munkaterhelés-kiszolgálóhoz.

1. Válassza az **alhálózat hozzáadása** lehetőséget.
4. Az **alhálózat neve** mezőbe írja be a **munkaterhelés-SN** értéket.
5. **Alhálózati címtartomány** esetében írja be a következőt: **10.0.2.0/24**.
6. Válassza a **Hozzáadás** lehetőséget.
7. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
8. Válassza a **Létrehozás** lehetőséget.

### <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása

Most hozza létre a munkaterhelés virtuális gépet, és helyezze a **munkaterhelés-SN** alhálózatba.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
2. Válassza a **Windows Server 2016 Datacenter** lehetőséget.
4. Adja meg a következő értékeket a virtuális gép számára:

   |Beállítás  |Érték  |
   |---------|---------|
   |Erőforráscsoport     |**Teszt – FW-RG**|
   |Virtuális gép neve     |**SRV-Work**|
   |Régió     |Ugyanaz, mint az előző|
   |Rendszerkép|Windows Server 2016 Datacenter|
   |Rendszergazda felhasználóneve     |Adja meg a felhasználónevet|
   |Jelszó     |Írjon be egy jelszót|

4. A **bejövő portszabályok**, **nyilvános bejövő portok** területen válassza a **nincs** lehetőséget.
6. Fogadja el a többi alapértelmezett értéket, és válassza a **Tovább: lemezek** lehetőséget.
7. Fogadja el a lemez alapértelmezett értékeit, és válassza a **Tovább: hálózatkezelés** lehetőséget.
8. Győződjön meg arról, hogy a **test-FW-vn** a virtuális hálózatra van kiválasztva, és az alhálózat **munkaterhelés-SN**.
9. A **nyilvános IP-címek** esetében válassza a **nincs** lehetőséget.
11. Fogadja el a többi alapértelmezett értéket, és válassza a **Tovább: kezelés** lehetőséget.
12. A rendszerindítási diagnosztika letiltásához válassza a **Letiltás** lehetőséget. Fogadja el a többi alapértelmezett értéket, és válassza a **felülvizsgálat + létrehozás** lehetőséget.
13. Tekintse át a beállításokat az összefoglalás lapon, majd válassza a **Létrehozás** lehetőséget.

## <a name="deploy-the-firewall"></a>A tűzfal üzembe helyezése

Helyezze üzembe a tűzfalat a virtuális hálózaton.

1. Az Azure Portal menüjében vagy a **Kezdőlapon** válassza az **Erőforrás létrehozása** elemet.
2. Írja be a **tűzfal** kifejezést a keresőmezőbe, majd nyomja le az **ENTER** billentyűt.
3. Válassza a **tűzfal** lehetőséget, majd válassza a **Létrehozás** lehetőséget.
4. A **Tűzfal létrehozása** oldalon konfigurálja a tűzfalat a következő táblázatban található értékekkel:

   |Beállítás  |Érték  |
   |---------|---------|
   |Előfizetés     |\<your subscription\>|
   |Erőforráscsoport     |**Teszt – FW-RG** |
   |Name     |**Test-FW01**|
   |Régió     |Válassza a korábban használt helyet|
   |Tűzfalak kezelése|**A tűzfalszabályok (klasszikus) használata a tűzfal kezeléséhez**|
   |Válasszon egy virtuális hálózatot     |**Meglévő használata**: **test-FW-vn**|
   |Nyilvános IP-cím     |**Új hozzáadása**<br>**Név**:  **FW-pip**|

5. Fogadja el a többi alapértelmezett értéket, majd válassza a **felülvizsgálat + létrehozás** lehetőséget.
6. Tekintse át az összegzést, majd válassza a **Létrehozás** lehetőséget a tűzfal létrehozásához.

   Az üzembe helyezés néhány percet vesz igénybe.
7. Az üzembe helyezés befejeződése után nyissa meg a **test-FW-RG** erőforráscsoportot, és válassza ki a **test-FW01** tűzfalat.
8. Jegyezze fel a tűzfal magán-és nyilvános IP-címeit. Ezeket a címeket később fogja használni.

## <a name="create-a-default-route"></a>Alapértelmezett útvonal létrehozása

A **Workload-SN** alhálózatot konfigurálja úgy, hogy a kimenő alapértelmezett útvonal áthaladjon a tűzfalon.

1. A Azure Portal menüben válassza a **minden szolgáltatás** lehetőséget, vagy keresse meg és válassza ki az *összes szolgáltatás* elemet bármely oldalon.
2. A **hálózat** területen válassza az **útválasztási táblák** elemet.
3. Válassza a **Hozzáadás** lehetőséget.
5. Az **Előfizetés** beállításnál válassza ki az előfizetését.
6. Az **erőforráscsoport** területen válassza a **test-FW-RG** elemet.
7. A **régió** mezőben válassza ki ugyanazt a helyet, amelyet korábban használt.
4. A **Név** mezőbe írja be a következőt: **Firewall-route**.
1. Válassza a **Felülvizsgálat és létrehozás** lehetőséget.
1. Válassza a **Létrehozás** lehetőséget.

Az üzembe helyezés befejezése után válassza **az Ugrás erőforráshoz** lehetőséget.

1. A tűzfal-útvonal lapon válassza az **alhálózatok** elemet, majd válassza a **hozzárendelés** lehetőséget.
1. Válassza a **Virtual Network**  >  **test-FW-vn** elemet.
1. **Alhálózat** esetében válassza a **munkaterhelés-SN** lehetőséget. Győződjön meg arról, hogy csak a **munkaterhelés-SN** alhálózatot választotta ehhez az útvonalhoz, ellenkező esetben a tűzfal nem fog megfelelően működni.

13. Válassza az **OK** lehetőséget.
14. Válassza az **útvonalak** lehetőséget, majd válassza a **Hozzáadás** lehetőséget.
15. Az **útvonal neve** mezőbe írja be a következőt: **FW-DG**.
16. A **Címelőtag** mezőbe írja be a következőt: **0.0.0.0/0**.
17. A **Következő ugrás típusa** beállításnál válassza a **Virtuális berendezés** lehetőséget.

    Az Azure Firewall valójában egy felügyelt szolgáltatás, de ebben a helyzetben a virtuális berendezés beállítás is használható.
18. A **Következő ugrás címe** mezőbe írja be a tűzfal magánhálózati IP-címét, amelyet korábban feljegyzett.
19. Válassza az **OK** lehetőséget.

## <a name="configure-an-application-rule"></a>Alkalmazásszabály konfigurálása

Ez az alkalmazási szabály, amely lehetővé teszi a kimenő hozzáférést a szolgáltatáshoz `www.google.com` .

1. Nyissa meg a **test-FW-RG** elemet, és válassza ki a **test-FW01** tűzfalat.
2. A **test-FW01** lapon a **Beállítások** területen válassza a **szabályok (klasszikus)** lehetőséget.
3. Válassza ki az **alkalmazás-szabály gyűjtemény** fület.
4. Válassza az **alkalmazás-szabály gyűjtemény hozzáadása** lehetőséget.
5. A **Név** mezőbe írja be a következőt: **App-Coll01**.
6. A **Prioritás** mezőbe írja be a következőt: **200**.
7. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.
8. A **szabályok**, **cél teljes tartománynevek** **neve** mezőbe írja be a következőt: **Allow-Google**.
9. A **forrás típusa** beállításnál válassza az **IP-cím** lehetőséget.
10. A **forrás** mezőbe írja be a következőt: **10.0.2.0/24**.
11. A **Protokoll:port** mezőbe írja be a következőt: **http, https**.
12. **Cél teljes tartománynevek** esetén írja be a következőt:**`www.google.com`**
13. Válassza a **Hozzáadás** lehetőséget.

Az Azure Firewall tartalmaz egy beépített szabálygyűjteményt az infrastruktúra alapértelmezés szerint engedélyezett teljes tartományneveiről. Ezek a teljes tartománynevek csak az adott platformra vonatkoznak, egyéb célra nem használhatók. További információ: [Infrastruktúra FQDN-jei](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Hálózatszabály konfigurálása

Ez az a hálózatszabály, amely lehetővé teszi a kimenő hozzáférést két IP-címhez az 53-as porton (DNS).

1. Válassza a **hálózati szabályok gyűjteménye** fület.
2. Válassza a **hálózati szabálygyűjtemény hozzáadása** lehetőséget.
3. A **Név** mezőbe írja be a következőt: **Net-Coll01**.
4. A **Prioritás** mezőbe írja be a következőt: **200**.
5. A **Művelet** beállításnál válassza az **Engedélyezés** lehetőséget.
6. A **szabályok**, **IP-címek**, **név** mezőben írja be a következőt: **Allow-DNS**.
7. A **Protokoll** beállításnál válassza az **UDP** lehetőséget.
9. A **forrás típusa** beállításnál válassza az **IP-cím** lehetőséget.
1. A **forrás** mezőbe írja be a következőt: **10.0.2.0/24**.
2. A **célhely típusa** beállításnál válassza az **IP-cím** lehetőséget.
3. A **cél címe** mezőbe írja be a következőt: **209.244.0.3, 209.244.0.4**

   Ezek a CenturyLink által működtetett nyilvános DNS-kiszolgálók.
1. A **Célportok** mezőbe írja be a következőt: **53**.
2. Válassza a **Hozzáadás** lehetőséget.

## <a name="configure-a-dnat-rule"></a>DNAT-szabály konfigurálása

Ez a szabály lehetővé teszi egy távoli asztal összekapcsolását a Srv-Work virtuális géppel a tűzfalon keresztül.

1. Válassza ki a **NAT-szabály gyűjtemény** fület.
2. Válassza a **NAT-szabálygyűjtemény hozzáadása** lehetőséget.
3. A **név** mezőbe írja be az **RDP** nevet.
4. A **Prioritás** mezőbe írja be a következőt: **200**.
5. A **szabályok** területen a **név** mezőbe írja be az **RDP-NAT** nevet.
6. A **Protokoll** beállításnál válassza a **TCP** lehetőséget.
7. A **forrás típusa** beállításnál válassza az **IP-cím** lehetőséget.
8. A **forrás** mezőbe írja be a következőt: **\*** .
9. A **cél címe** mezőbe írja be a tűzfal nyilvános IP-címét.
10. A **célport** mezőbe írja be a következőt: **3389**.
11. A **lefordított cím** mezőbe írja be az **SRV-Work** magánhálózati IP-címet.
12. A **Lefordított port** mezőben adja meg a **3389** értéket.
13. Válassza a **Hozzáadás** lehetőséget.


### <a name="change-the-primary-and-secondary-dns-address-for-the-srv-work-network-interface"></a>Módosítsa az **Srv-Work** hálózati adapter elsődleges és másodlagos DNS-címét.

Az oktatóanyag tesztelési célokra konfigurálja a kiszolgáló elsődleges és másodlagos DNS-címeit. Ez nem általános Azure Firewall követelmény.

1. A Azure Portal menüben válassza az **erőforráscsoportok** lehetőséget, vagy keresse meg és válassza ki az *erőforráscsoportok* lehetőséget bármely oldalon. Válassza ki a **test-FW-RG** erőforráscsoportot.
2. Válassza ki az **SRV-Work** virtuális gép hálózati adapterét.
3. A **Beállítások** területen válassza a **DNS-kiszolgálók** elemet.
4. A **DNS-kiszolgálók** területen válassza az **Egyéni** lehetőséget.
5. Írja be a **209.244.0.3** a címet a **DNS-kiszolgáló hozzáadása** szövegmezőbe, és **209.244.0.4** címet a következő szövegmezőbe.
6. Kattintson a **Mentés** gombra.
7. Indítsa újra az **Srv-Work** virtuális gépet.

## <a name="test-the-firewall"></a>A tűzfal tesztelése

Most tesztelje a tűzfalat, és ellenőrizze, hogy az a várt módon működik-e.

1. Csatlakoztasson egy távoli asztalt a tűzfal nyilvános IP-címéhez, és jelentkezzen be az **SRV-Work** virtuális gépre. 
3. Nyissa meg az Internet Explorert, és navigáljon a következő címre: `https://www.google.com`.
4.   >  Az Internet Explorer biztonsági riasztások ablakában kattintson az OK **Bezárás** gombra.

   Ekkor meg kell jelennie a Google kezdőlapjának.

5. Nyissa meg a következő címet: `https://www.microsoft.com`.

   A tűzfal blokkolja a hozzáférést.

Most ellenőrizte, hogy a tűzfalszabályok működnek-e:

* Az egyetlen engedélyezett FQDN-t el tudja érni, de másokat nem.
* Fel tudja oldani a DNS-neveket a konfigurált külső DNS-kiszolgálóval.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A tűzfalhoz kapcsolódó erőforrásokat a következő oktatóanyagban is használhatja, vagy ha már nincs rá szükség, törölje a **Test-FW-RG** erőforráscsoportot, és vele együtt a tűzfalhoz kapcsolódó összes erőforrást.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Az Azure Firewall naplóinak monitorozása](./firewall-diagnostics.md)