---
title: Azure Data Box Gatewayhoz csatlakozhat, konfigurálhat és aktiválhat Azure Portal
description: Az Data Box Gateway üzembe helyezésének harmadik oktatóanyaga arra utasítja a virtuális eszköz csatlakoztatását, beállítását és aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: tutorial
ms.date: 03/18/2019
ms.author: alkohli
ms.openlocfilehash: cb6ef6ac25c4afa72160ba437e0ea3b5492cfd93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "96583451"
---
# <a name="tutorial-connect-set-up-activate-azure-data-box-gateway"></a>Oktatóanyag: Az Azure Data Box Gateway csatlakoztatása, konfigurálása és aktiválása

## <a name="introduction"></a>Bevezetés

Ez az oktatóanyag azt ismerteti, hogyan lehet csatlakozni, beállítani és aktiválni a Data Box Gateway eszközt a helyi webes felhasználói felület használatával. 

A telepítés és az aktiválási folyamat körülbelül 10 percet vesz igénybe. 

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Kapcsolódás virtuális eszközhöz
> * A virtuális eszköz beállítása és aktiválása

## <a name="prerequisites"></a>Előfeltételek

A Data Box Gateway konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* Létrehozta a virtuális eszközt, és egy csatlakoztatott URL-címet kapott a [Hyper-V Data Box Gateway](data-box-gateway-deploy-provision-hyperv.md) üzembe helyezése vagy [a VMware-Data Box Gateway kiépítése](data-box-gateway-deploy-provision-vmware.md)című részletesen.
* Az Data Box Gateway-szolgáltatás aktiválási kulcsa Data Box Gateway eszközök felügyeletére lett létrehozva. További információ: [felkészülés a Azure Data Box Gateway üzembe helyezésére](data-box-gateway-deploy-prep.md).


## <a name="connect-to-the-local-web-ui-setup"></a>Kapcsolódás a helyi webes felhasználói felület telepítéséhez 

1. Nyisson meg egy böngészőablakot, és nyissa meg az eszköz helyi webes FELÜLETét a következő címen:
   
   https: \/ /IP-Address-of-Network-Interface
   
   Használja az előző oktatóanyagban feljegyzett kapcsolatok URL-címét. Megjelenik egy hiba vagy egy figyelmeztetés, amely azt jelzi, hogy probléma van a webhely biztonsági tanúsítványával.

2. Válassza **a folytatás ehhez a weboldalhoz** lehetőséget. Ezek a lépések eltérők lehetnek, attól függően, hogy milyen böngészőt használ.
   
    ![A webhely biztonsági tanúsítványának hibaüzenete](./media/data-box-gateway-deploy-connect-setup-activate/image2.png)

3. Jelentkezzen be a virtuális eszköz webes felhasználói felületére. Az alapértelmezett jelszó a *jelszó1*. 
   
    ![Bejelentkezés a helyi webes felhasználói felületen](./media/data-box-gateway-deploy-connect-setup-activate/image3.png)

4. A parancssorban módosítsa az eszköz jelszavát. Az új jelszó 8–16 karakterből állhat. A következők közül hármat tartalmaznia kell: nagybetűk, kisbetűk, számok és speciális karakterek.

    ![Az eszköz jelszavának módosítása](./media/data-box-gateway-deploy-connect-setup-activate/image4.png)

Most már az eszköz **irányítópultján** van.

## <a name="set-up-and-activate-the-virtual-device"></a>A virtuális eszköz beállítása és aktiválása
 
Az irányítópulton a virtuális eszköznek a Data Box Gateway szolgáltatással való konfigurálásához és regisztrálásához szükséges különböző beállítások láthatók. Az **eszköz neve**, a **hálózati beállítások**, a **webproxy beállításai** és az **időbeállítások** megadása nem kötelező. Az egyetlen szükséges beállítás a **felhő beállításai**.
   
![Helyi webes felhasználói felület "irányítópult" lapja](./media/data-box-gateway-deploy-connect-setup-activate/image5.png)

1. A bal oldali ablaktáblában válassza az **eszköznév** lehetőséget, majd adjon meg egy rövid nevet az eszköznek. A rövid névnek 1 és 15 karakter közöttinek kell lennie, és betűt, számot és kötőjelet kell tartalmaznia. 

    ![Helyi webes felhasználói felület "eszköznév" lapja](./media/data-box-gateway-deploy-connect-setup-activate/image6.png)

2. Választható A bal oldali ablaktáblán válassza a **hálózati beállítások** lehetőséget, majd konfigurálja a beállításokat. A virtuális eszközön legalább egy hálózati adaptert látni fog, attól függően, hogy hányan konfigurálta az alapul szolgáló virtuális gépet. Egy olyan virtuális eszköz **hálózati beállítások** lapja, amelyen engedélyezve van egy hálózati adapter, az alább látható.
    
    ![Helyi webes felhasználói felület "hálózati beállítások" lapja](./media/data-box-gateway-deploy-connect-setup-activate/image7.png)
   
    A hálózati beállítások konfigurálásakor vegye figyelembe a következőket:

    - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. A rendszer automatikusan hozzárendel egy IP-címet, egy alhálózatot, egy átjárót és egy DNS-t.
    - Ha a DHCP nincs engedélyezve, szükség esetén statikus IP-címeket is hozzárendelhet.
    - A hálózati adaptert IPv4-ként is konfigurálhatja.

     >[!NOTE] 
     > Azt javasoljuk, hogy ne váltson a hálózati adapter helyi IP-címére statikusról DHCP-re, hacsak nem rendelkezik másik IP-címmel az eszközhöz való csatlakozáshoz. Ha egy hálózati adaptert használ és DHCP-re vált, a DHCP-címeket nem lehet meghatározni. Ha DHCP-címre szeretne váltani, várjon, amíg az eszköz regisztrálja magát a szolgáltatásban, majd hajtsa végre a módosítást. Ezután megtekintheti az összes adapter IP-címeit az **eszköz tulajdonságai** között a szolgáltatás Azure Portal.

3. (Nem kötelező) konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, ha webproxyt használ, csak ezen a lapon konfigurálhatja.
   
   ![Helyi webes felhasználói felület "Webproxy-beállítások" lapja](./media/data-box-gateway-deploy-connect-setup-activate/image8.png)
   
   A **webproxy** lapon tegye a következőket:
   
   1. A **webproxy URL-címe** mezőbe írja be az URL-címet a következő formátumban: `http://&lt;host-IP address or FQDN&gt;:Port number` . A HTTPS URL-címek nem támogatottak.
   2. A **Hitelesítés** területen válassza a **Nincs** vagy az **NTLM** lehetőséget.
   3. Ha hitelesítést használ, adjon meg egy **felhasználónevet** és egy **jelszót**.
   4. A konfigurált Webproxy-beállítások érvényesítéséhez és alkalmazásához kattintson az **alkalmaz** gombra.

   > [!NOTE]
   > A proxy-Auto config (PAC) fájlok nem támogatottak. A PAC-fájlok azt határozzák meg, hogy a böngészők és más felhasználói ügynökök hogyan tudják automatikusan kiválasztani a megfelelő proxykiszolgálót (hozzáférési módszer) egy adott URL beolvasásához.
   > Azok a proxyk, amelyek megpróbálják megszakítani és beolvasni az összes forgalmat (majd újra aláírni a saját tanúsítványukkal) nem kompatibilisek, mert a proxy tanúsítványa nem megbízható.
   > Általában az átlátszó proxyk jól működnek Azure Data Box Gateway.

4. Választható A bal oldali panelen válassza az **időbeállítások** lehetőséget, majd konfigurálja az adott eszközhöz tartozó időzónát és az elsődleges és másodlagos NTP-kiszolgálókat. 

    Az NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt ahhoz, hogy hitelesíteni lehessen a felhőalapú szolgáltatóktól.
    
    ![Helyi webes felhasználói felület "Time Settings" lapja](./media/data-box-gateway-deploy-connect-setup-activate/image9.png)
    
    Az **idő beállításai** lapon tegye a következőket:
    
    1. Az **időzóna** legördülő listában válassza ki azt az időzónát, amely megfelel az eszköz központi telepítésének földrajzi helyének.
        Az eszköz alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    2. Adja meg az eszköz **Elsődleges NTP-kiszolgálóját** , vagy fogadja el az alapértelmezett értéket `time.windows.com` .   
        Győződjön meg arról, hogy a hálózat engedélyezi az adatközpont és az internet közötti NTP-adatforgalmat.

    3. Szükség esetén a **másodlagos NTP-kiszolgáló** mezőbe írja be az eszköz másodlagos kiszolgálóját.

    4. A beállított idő beállításainak ellenőrzéséhez és alkalmazásához válassza az **alkalmaz** lehetőséget.

6. A bal oldali ablaktáblán válassza a **felhő beállításai** lehetőséget, majd aktiválja az eszközt a Azure Portal Data Box Gateway szolgáltatásával.
    
    1. Az **aktiválási kulcs** mezőben adja meg a Data Box Gateway [aktiválási kulcsának lekérése](data-box-gateway-deploy-prep.md#get-the-activation-key) **aktiválási** kulcsát.

    2. Válassza az **aktiválás** lehetőséget.
       
         ![Helyi webes felhasználói felület "Cloud Settings" (Felhőbeli beállítások) lapja](./media/data-box-gateway-deploy-connect-setup-activate/image10a.png)
    
    3. Az eszköz aktiválva van, és ha elérhető, a kritikus frissítések automatikusan alkalmazva lesznek. Ekkor megjelenik egy értesítés erre a hatásra. A frissítési folyamat figyelése a Azure Portal használatával.

        ![Helyi webes felhasználói felület "felhő beállításai" 2. oldal](./media/data-box-gateway-deploy-connect-setup-activate/image12.png)
        
        **A párbeszédpanelen egy olyan helyreállítási kulcs is található, amelyet egy biztonságos helyre kell másolni és menteni. Ez a kulcs az adatok helyreállításához használható abban az esetben, ha az eszköz nem tud elindulni.**


    4. Lehet, hogy várnia kell néhány percet, amíg a frissítés sikeresen befejeződik. A frissítés befejezését követően jelentkezzen be az eszközön. A **Felhőbeli beállítások** oldal frissül, és jelzi, hogy az eszköz aktiválása sikeres volt.

        ![A helyi webes felhasználói felület "Cloud Settings" (Felhőbeli beállítások) lapja frissítve](./media/data-box-gateway-deploy-connect-setup-activate/image13.png)

Az eszköz telepítése befejeződött. Mostantól megosztásokat is hozzáadhat az eszközhöz.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Kapcsolódás virtuális eszközhöz
> * A virtuális eszköz beállítása és aktiválása

Ha szeretné megtudni, hogyan vihetők át az adatok a Data Box Gatewayával, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Adatok átvitele Data Box Gatewaysal](./data-box-gateway-deploy-add-shares.md).
