---
title: Oktatóanyag – Azure DNS-aliasrekord létrehozása egy nyilvános Azure IP-címre való hivatkozáshoz.
description: Ez az oktatóanyag bemutatja, hogyan konfigurálhat egy Azure DNS-aliasrekordot egy nyilvános Azure IP-címre való hivatkozáshoz.
services: dns
author: vhorne
ms.service: dns
ms.topic: tutorial
ms.date: 9/25/2018
ms.author: victorh
ms.openlocfilehash: 7dcbfdaf00b0e628541cfd1a3b79df8cf8334ed3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536878"
---
# <a name="tutorial-configure-an-alias-record-to-refer-to-an-azure-public-ip-address"></a>Oktatóanyag: Tekintse meg az Azure nyilvános IP-cím egy aliast rekord konfigurálása 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Hálózati infrastruktúra létrehozása.
> * Webkiszolgálót futtató virtuális gép létrehozása.
> * Aliasrekord létrehozása.
> * Az aliasrekord tesztelése.


Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek
Elérhetőnek kell lennie egy tartománynévnek, amelyet üzemeltethet az Azure DNS-ben a teszteléshez. Teljes körű irányítással kell rendelkeznie a tartomány felett. A teljes körű irányításba beletartozik a tartomány névkiszolgálói (NS-) rekordjainak beállítására való képesség.

Üzemeltessen saját tartományt az Azure DNS-ben az utasításokért lásd: [oktatóanyag: Üzemeltessen saját tartományt az Azure DNS](dns-delegate-domain-azure-dns.md).

Az ebben az oktatóanyagban használt példatartománynév a contoso.com, de Ön használja a saját tartománynevét.

## <a name="create-the-network-infrastructure"></a>A hálózati infrastruktúra létrehozása
Először hozzon létre egy virtuális hálózatot és egy alhálózatot a webkiszolgálók elhelyezéséhez.
1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
2. Válassza a portál bal felső sarkában az **Erőforrás létrehozása** lehetőséget. Írja be a keresőmezőbe az *erőforráscsoport* kifejezést, és hozzon létre egy **RG-DNS-Alias-pip** nevű erőforráscsoportot.
3. Válassza az **Erőforrás létrehozása** > **Hálózat** > **Virtuális hálózat** lehetőséget.
4. Hozzon létre egy **VNet-Server** nevű virtuális hálózatot. Helyezze az **RG-DNS-Alias-pip** erőforráscsoportba, és adja az **SN-Web** nevet az alhálózatnak.

## <a name="create-a-web-server-virtual-machine"></a>Webkiszolgálót futtató virtuális gép létrehozása
1. Válassza az **Erőforrás létrehozása** > **Windows Server 2016-alapú virtuális gép** elemet.
2. Adja meg a **Web-01** nevet, és helyezze el a virtuális gépet az **RG-DNS-Alias-TM** erőforráscsoportban. Adjon meg egy felhasználónevet és egy jelszót, és válassza az **OK** lehetőséget.
3. A **Méret** mezőben válasszon ki egy 8 GB RAM-mal rendelkező SKU-t.
4. A **Beállítások** területen válassza a **VNet-Servers** virtuális hálózatot és az **SN-Web** alhálózatot. A nyilvános bejövő portokhoz válassza a **HTTP** > **HTTPS** > **RDP (3389)** elemet, majd válassza az **OK** elemet.
5. Az **Összefoglalás** lapon válassza a **Létrehozás** lehetőséget.

Ez az eljárás néhány percet vehet igénybe.

### <a name="install-iis"></a>Az IIS telepítése

Telepítse az IIS-t a **Web-01** hálózatra.

1. Csatlakozzon a **Web-01** virtuális géphez, és jelentkezzen be.
2. A **Kiszolgálókezelő** irányítópulton válassza a **Szerepkörök és szolgáltatások hozzáadása** elemet.
3. Kattintson háromszor a **Tovább** gombra. A **Kiszolgálói szerepkörök** lapon válassza a **Webkiszolgáló (IIS)** elemet.
4. Válassza a **Szolgáltatások hozzáadása**, majd a **Tovább** elemet.
5. Kattintson négyszer a **Tovább** gombra, majd válassza a **Telepítés** elemet. Ez az eljárás néhány percet vehet igénybe.
6. A telepítés befejezése után válassza a **Bezárás** elemet.
7. Nyisson meg egy webböngészőt. Nyissa meg a **localhost** címet, és győződjön meg róla, hogy az IIS alapértelmezett weblapja megjelenik.

## <a name="create-an-alias-record"></a>Aliasrekord létrehozása

Hozzon létre egy aliasrekordot, amely a nyilvános IP-címre mutat.

1. Válassza ki az Azure DNS-zónát a zóna megnyitásához.
2. Válassza a **Rekordhalmaz** elemet.
3. A **Név** szövegmezőbe írja be a **web01** nevet.
4. Hagyja meg a **Típus** mező értékét **A** rekordként.
5. Jelölje be az **Aliasrekord-halmaz** jelölőnégyzetet.
6. Kattintson az **Azure szolgáltatás kiválasztása** elemre, és válassza a **Web-01-ip** nyilvános IP-címet.

## <a name="test-the-alias-record"></a>Az aliasrekord tesztelése

1. Az **RG-DNS-Alias-pip** erőforráscsoportban válassza a **Web-01** virtuális gépet. Jegyezz fel a nyilvános IP-címet.
1. Egy webböngészőben nyissa meg a Web01-01 virtuális gép teljes tartománynevét. Például a **web01.contoso.com** nevet. Most megjelenik az IIS alapértelmezett weboldala.
2. Zárja be a webböngészőt.
3. Állítsa le a **Web-01** virtuális gépet, majd indítsa újra.
4. A virtuális gép újraindulása után jegyezze fel a virtuális gép új nyilvános IP-címét.
5. Nyisson meg egy új böngészőt. Nyissa meg újra a Web01-01 virtuális gép teljes tartománynevét. Például a **web01.contoso.com** nevet.

Ez az eljárás sikeres, mivel nem pedig standard A rekorddal, hanem egy aliasrekorddal mutatott a nyilvános IP-cím-erőforrásra.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs szüksége a jelen oktatóanyagban létrehozott erőforrásokra, törölheti az **RG-DNS-Alias-pip** erőforráscsoportot.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban létrehozott egy aliasrekordot egy nyilvános Azure IP-címre való hivatkozáshoz. Az Azure DNS és a webalkalmazások részletesebb megismeréséhez folytassa a webalkalmazásokról szóló oktatóanyaggal.

> [!div class="nextstepaction"]
> [Webalkalmazások DNS-rekordjainak létrehozása egyéni tartományban](./dns-web-sites-custom-domain.md)
