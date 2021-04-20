---
title: 'Oktatóanyag: Aliasrekord létrehozása a tartomány csúcsneveinek támogatásához – Traffic Manager'
titleSuffix: Azure DNS
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhat Azure DNS-aliasrekordot, hogy támogassa a tartomány legfelső szintű nevének használatát a Traffic Managerrel.
services: dns
author: rohinkoul
ms.service: dns
ms.topic: tutorial
ms.date: 04/19/2021
ms.author: rohink
ms.openlocfilehash: e0101133c68142845a8ada50d9921d341cf10ad0
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107738791"
---
# <a name="tutorial-configure-an-alias-record-to-support-apex-domain-names-with-traffic-manager"></a>Oktatóanyag: aliasrekord konfigurálása a hogy, támogassa a legfelső szintű tartományneveket a Traffic Managerrel 

Létrehozhat egy aliasrekordot a legfelső szintű tartománynévhez egy Azure Traffic Manager-profilra való hivatkozáshoz. Például: contoso.com. Átirányítási szolgáltatás használata helyett konfigurálhatja az Azure DNS-t, hogy közvetlenül a zónájából hivatkozzon egy Traffic Manager-profilra. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Gazda-virtuálisgép és hálózati infrastruktúra létrehozása.
> * Traffic Manager-profil létrehozása.
> * Aliasrekord létrehozása.
> * Az aliasrekord tesztelése.

Ha nem rendelkezik Azure-előfizetéssel, [](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) kezdés előtt hozzon létre egy ingyenes fiókot.

## <a name="prerequisites"></a>Előfeltételek
Elérhetőnek kell lennie egy tartománynévnek, amelyet üzemeltethet az Azure DNS-ben a teszteléshez. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

A tartomány Azure DNS-ben való üzemeltetésére vonatkozó utasításokért lásd az [Oktatóanyag: Saját tartomány üzemeltetése az Azure DNS-ben](dns-delegate-domain-azure-dns.md) című szakaszt.

Az ebben az oktatóanyagban használt példatartománynév a contoso.com, de Ön használja a saját tartománynevét.

## <a name="create-the-network-infrastructure"></a>A hálózati infrastruktúra létrehozása

Először hozzon létre egy virtuális hálózatot és egy alhálózatot a webkiszolgálók elhelyezéséhez.

1. Jelentkezzen be az Azure Portalra a [https://portal.azure.com](https://portal.azure.com) webhelyen.
2. Válassza a portál bal felső sarkában az **Erőforrás létrehozása** lehetőséget. Írja be a keresőmezőbe az *erőforráscsoport* kifejezést, és hozzon létre egy **RG-DNS-Alias-TM** nevű erőforráscsoportot.
3. Válassza **az Erőforrás létrehozása** Hálózat  >  **virtuális**  >  **hálózat** lehetőséget.
4. Hozzon létre egy **VNet-Servers** nevű virtuális hálózatot. Helyezze az **RG-DNS-Alias-TM** erőforráscsoportba, és adja az **SN-Web** nevet az alhálózatnak.

## <a name="create-two-web-server-virtual-machines"></a>Két webkiszolgáló virtuális gép létrehozása

1. Válassza **az Erőforrás létrehozása** Windows Server  >  **2016 virtuális gép lehetőséget.**
2. Adja meg a **Web-01** nevet, és helyezze el a virtuális gépet az **RG-DNS-Alias-TM** erőforráscsoportban. Adjon meg egy felhasználónevet és egy jelszót, és válassza az **OK** lehetőséget.
3. A **Méret** mezőben válasszon ki egy 8 GB RAM-mal rendelkező SKU-t.
4. A **Beállítások** területen válassza a **VNet-Servers** virtuális hálózatot és az **SN-Web** alhálózatot.
5. Válassza a **Nyilvános IP-cím** elemet. A **Hozzárendelés** területen válassza a **Statikus** elemet, majd válassza az **OK** lehetőséget.
6. A nyilvános bejövő portok esetén válassza a **HTTP (80)** HTTPS  >  **(443)**  >  **RDP (3389)** lehetőséget, majd kattintson az **OK gombra.**
7. Az **Összefoglalás** lapon válassza a **Létrehozás** lehetőséget. Ez az eljárás néhány percet vehet igénybe.

Ugyanezt az eljárást megismételve hozzon létre egy másik virtuális gépet **Web-02** néven.

### <a name="add-a-dns-label"></a>Adja meg a DNS-címkét

A nyilvános IP-címeknek DNS-címkére van szükségük, hogy működjenek a Traffic Managerrel.
1. Az **RG-DNS-Alias-TM** erőforráscsoportban válassza a **Web-01-ip** nyilvános IP-címet.
2. A **Beállítások** területen válassza a **Konfiguráció** elemet.
3. A DNS-név címkéjének szövegmezőjébe írja be a **web01pip** nevet.
4. Kattintson a **Mentés** gombra.

Ismételje meg ezt az eljárást a **Web-02-ip** nyilvános IP-címnél, és DNS-név címkéjeként adja meg a **web02pip** értéket.

### <a name="install-iis"></a>Az IIS telepítése

Telepítse az IIS-t mind a **Web-01**, mind a **Web-02** virtuális gépen.

1. Csatlakozzon **a Web-01-hez,** és jelentkezzen be.
2. A **Kiszolgálókezelő** irányítópulton válassza a **Szerepkörök és szolgáltatások hozzáadása** elemet.
3. Kattintson háromszor a **Tovább** gombra. A **Kiszolgálói szerepkörök** lapon válassza a **Webkiszolgáló (IIS)** elemet.
4. Válassza a **Szolgáltatások hozzáadása**, majd a **Tovább** elemet.
5. Kattintson négyszer a **Tovább** gombra. Ezután válassza a **Telepítés lehetőséget.** Ez az eljárás néhány percet vehet igénybe.
6. A telepítés befejezése után válassza a **Bezárás** elemet.
7. Nyisson meg egy webböngészőt. Nyissa meg a **localhost** címet, és győződjön meg róla, hogy az IIS alapértelmezett weblapja megjelenik.

Ismételje meg az eljárást az IIS **Web-02** virtuális gépen való telepítéséhez.


## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

1. Nyissa meg az **RG-DNS-Alias-TM** erőforráscsoportot, és válassza a **Web-01-ip** nyilvános IP-címet. Jegyezze fel az IP-címet későbbi használatra. Ismételje meg ezt a lépést a **Web-02-ip** nyilvános IP-címhez.
1. Válassza **az Erőforrás létrehozása** Hálózat  >  **Traffic Manager**  >  **profilt.**
2. Adja meg a **TM-alias-test** nevet. Helyezze az **RG-DNS-Alias-TM** erőforráscsoportba.
3. Válassza a **Létrehozás** lehetőséget.
4. Az üzembe helyezés befejezése után válassza az **Erőforrás megnyitása** elemet.
5. A Traffic Manager-profillap **Beállítások** területén válassza a **Végpontok** elemet.
6. Válassza a **Hozzáadás** lehetőséget.
7. A **Típus** értékeként válassza a **Külső végpont** lehetőséget, és a **Név** mezőbe írja be az **EP-Web01** értéket.
8. A **Teljes tartománynév (FQDN) vagy IP-cím** szövegmezőbe írja be a **Web-01-ip** IP-címét, amelyet korábban feljegyzett.
9. Válassza ki ugyanazt a **helyet**, mint a többi erőforrásnál, majd válassza az **OK** elemet.

Ismételje meg ezt az eljárást a **Web-02** végpont hozzáadásához a korábban a **Web-02-ip** esetében lejegyzett IP-címmel.

## <a name="create-an-alias-record"></a>Aliasrekord létrehozása

Hozzon létre egy aliasrekordot, amely a Traffic Manager-profilra mutat.

1. Válassza ki az Azure DNS-zónát a zóna megnyitásához.
2. Válassza a **Rekordhalmaz** elemet.
3. Hagyja üresen a **Név** szövegmezőt, hogy a legfelső szintű tartománynevet jelölje. Például: contoso.com.
4. Hagyja meg a **Típus** mező értékét **A** rekordként.
5. Jelölje be az **Aliasrekord-halmaz** jelölőnégyzetet.
6. Kattintson az **Azure-szolgáltatás kiválasztása** elemre, és válassza a **TM-alias-test** Traffic Manager-profilt.

## <a name="test-the-alias-record"></a>Az aliasrekord tesztelése

1. Nyissa meg egy webböngészőben a legfelső szintű tartománynevet. Például: contoso.com. Megjelenik az IIS alapértelmezett webhelye. Zárja be a webböngészőt.
2. Állítsa le a **Web-01** virtuális gépet. Várjon néhány percet, hogy teljesen leálljon.
3. Nyisson meg egy új böngészőablakot, és keresse meg ismét a legfelső szintű tartománynevet.
4. Ismét megjelenik az IIS alapértelmezett webhelye, mert a Traffic Manager kezelte a helyzetet, és a **Web-02** virtuális gépre irányította a forgalmat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a jelen oktatóanyagban létrehozott erőforrásokra, törölheti az **RG-DNS-Alias-TM** erőforráscsoportot.

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy aliasrekordot, hogy a legfelső szintű tartománynevet használhassa egy Traffic Manager-profilra való hivatkozáshoz. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Elosztott terhelésű webalkalmazások üzemeltetése a zóna legfelső pontján](./dns-alias-appservice.md)
