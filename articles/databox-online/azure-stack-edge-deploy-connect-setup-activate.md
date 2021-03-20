---
title: Oktatóanyag a Azure Stack Edge Pro-eszköz csatlakoztatásához, konfigurálásához, aktiválásához Azure Portal-ben | Microsoft Docs
description: Az Azure Stack Edge Pro üzembe helyezésére vonatkozó oktatóanyag arra utasítja a fizikai eszköz csatlakoztatását, beállítását és aktiválását.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 03/28/2019
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: 8a143dadffb3f89ef67dc20a2038bb3c9bf5a0e4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "91743335"
---
# <a name="tutorial-connect-set-up-and-activate-azure-stack-edge-pro"></a>Oktatóanyag: a Azure Stack Edge Pro összekapcsolása, beállítása és aktiválása 

Ez az oktatóanyag azt ismerteti, hogyan lehet csatlakozni, beállítani és aktiválni a Azure Stack Edge Pro-eszközt a helyi webes felhasználói felület használatával.

A telepítés és az aktiválás folyamata körülbelül 20 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Kapcsolódás fizikai eszközhöz
> * A fizikai eszköz beállítása és aktiválása

## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge Pro-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge Pro telepítésének](azure-stack-edge-deploy-install.md)részletesen telepítette.
* Az Azure Stack Edge Pro-eszköz kezeléséhez létrehozott Azure Stack Edge-szolgáltatásból aktiválhatja az aktiválási kulcsot. További információért látogasson el a [felkészülés a Azure stack Edge Pro üzembe helyezéséhez](azure-stack-edge-deploy-prep.md)című témakörre.

## <a name="connect-to-the-local-web-ui-setup"></a>Kapcsolódás a helyi webes felhasználói felület telepítéséhez

1. Konfigurálja az Ethernet-adaptert a számítógépén az Azure Stack Edge Pro-eszközhöz a 192.168.100.5 és az alhálózat 255.255.255.0 statikus IP-címével való csatlakozáshoz.

2. Csatlakoztassa a számítógépet az eszközön a PORT 1-hez. A következő ábra alapján azonosíthatja eszközén a PORT 1-et.

    ![Egy bekábelezett eszköz hátlapja](./media/azure-stack-edge-deploy-install/backplane-cabled.png)

3. Nyissa meg egy böngészőablakot, és lépjen az eszköz helyi webes felhasználói felületére a `https://192.168.100.10` címen.  
    Ez a művelet eltarthat pár percig, miután bekapcsolta az eszközt.

    Megjelenik egy hiba vagy egy figyelmeztetés, amely azt jelzi, hogy probléma van a webhely biztonsági tanúsítványával.
   
    ![A webhely biztonsági tanúsítványának hibaüzenete](./media/azure-stack-edge-deploy-connect-setup-activate/image2.png)

4. Válassza **a folytatás ehhez a weboldalhoz** lehetőséget.  
    Ezek a lépések eltérők lehetnek, attól függően, hogy milyen böngészőt használ.

5. Jelentkezzen be az eszköz webes felhasználói felületére. Az alapértelmezett jelszó a *jelszó1*. 
   
    ![Azure Stack Edge Pro-eszköz bejelentkezési lapja](./media/azure-stack-edge-deploy-connect-setup-activate/image3.png)

6. A parancssorban módosítsa az eszköz rendszergazdai jelszavát.  
    Az új jelszó 8–16 karakterből állhat. A következő karakterek közül háromfélét tartalmaznia kell: kisbetűk, nagybetűk, számok és speciális karakterek.

Most már az eszköz irányítópultján van.

## <a name="set-up-and-activate-the-physical-device"></a>A fizikai eszköz beállítása és aktiválása
 
Az irányítópulton a fizikai eszköz a Azure Stack Edge szolgáltatással való konfigurálásához és regisztrálásához szükséges különféle beállítások láthatók. Az **eszköz neve**, a **hálózati beállítások**, a **webproxy beállításai** és az **időbeállítások** megadása nem kötelező. Az egyetlen szükséges beállítás a **felhő beállításai**.
   
![Helyi webes felhasználói felület "irányítópult" lapja](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-1.png)

1. A bal oldali panelen válassza az **eszköznév** lehetőséget, majd adjon meg egy rövid nevet az eszköznek.  
    A felhasználóbarát névnek 1 és 15 karakter közöttinek kell lennie, és betűket, számokat és kötőjeleket kell tartalmaznia.

    ![Helyi webes felhasználói felület "eszköznév" lapja](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-2.png)

2. Választható A bal oldali ablaktáblán válassza a **hálózati beállítások** lehetőséget, majd konfigurálja a beállításokat.  
    A fizikai eszközön hat hálózati adapter található. A PORT 1 és PORT 2 jelzésű portok 1 Gbps hálózati adapterek. A 3. port, a 4-es port, az 5. port és a 6-os PORT mind a 25 GB-os hálózati adapterek, amelyek 10 GB/s hálózati adapterként is használhatók. Az 1. PORT automatikusan csak felügyeleti portként van konfigurálva, a 2-es port pedig minden adatport. A **hálózati beállítások** lap az alábbi ábrán látható.
    
    ![Helyi webes felhasználói felület "hálózati beállítások" lapja](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-3.png)
   
    A hálózati beállítások konfigurálása közben figyeljen a következőkre:

   - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. A rendszer automatikusan hozzárendel egy IP-címet, egy alhálózatot, egy átjárót és egy DNS-t.
   - Ha a DHCP nincs engedélyezve, szükség esetén statikus IP-címeket is hozzárendelhet.
   - A hálózati adaptert IPv4-ként is konfigurálhatja.

     >[!NOTE] 
     > Azt javasoljuk, hogy a hálózati adapter helyi IP-címét ne módosítsa statikusról DHCP-re, hacsak nem rendelkezik másik IP-címmel az eszközhöz való csatlakozáshoz. Ha egy hálózati adaptert használ és DHCP-re vált, a DHCP-címeket nem lehet meghatározni. Ha DHCP-címre szeretne váltani, várjon, amíg az eszköz regisztrálja magát a szolgáltatásban, majd hajtsa végre a módosítást. Ezután megtekintheti az összes adapter IP-címeit az **eszköz tulajdonságai** között a szolgáltatás Azure Portal.

3. Választható A bal oldali ablaktáblán válassza a **webproxy beállításai** lehetőséget, majd konfigurálja a webproxy-kiszolgálót. Bár a webproxy konfigurálása nem kötelező, ha webproxyt használ, csak ezen a lapon konfigurálhatja.
   
   ![Helyi webes felhasználói felület "Webproxy-beállítások" lapja](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-4.png)
   
   A **webproxy beállításai** lapon tegye a következőket:
   
   a. A **webproxy URL-címe** mezőbe írja be az URL-címet a következő formátumban: `http://host-IP address or FQDN:Port number` . A HTTPS URL-címek nem támogatottak.

   b. A **Hitelesítés** területen válassza a **Nincs** vagy az **NTLM** lehetőséget. Ha engedélyezi a számítást, és IoT Edge modult használ a Azure Stack Edge Pro-eszközön, javasoljuk, hogy a webproxy-hitelesítést ne állítsa be a **none** értékre. Az **NTLM** nem támogatott.

   c. Ha hitelesítést használ, adjon meg egy felhasználónevet és egy jelszót.

   d. A konfigurált Webproxy-beállítások érvényesítéséhez és alkalmazásához válassza a **Beállítások alkalmazása** lehetőséget.

   > [!NOTE]
   > A proxy-Auto config (PAC) fájlok nem támogatottak. A PAC-fájlok azt határozzák meg, hogy a böngészők és más felhasználói ügynökök hogyan tudják automatikusan kiválasztani a megfelelő proxykiszolgálót (hozzáférési módszer) egy adott URL beolvasásához.
   > Azok a proxyk, amelyek megpróbálják megszakítani és beolvasni az összes forgalmat (majd újra aláírni a saját tanúsítványukkal) nem kompatibilisek, mert a proxy tanúsítványa nem megbízható.
   > Általában az átlátszó proxyk jól működnek Azure Stack Edge Pro-val.

4. Választható A bal oldali panelen válassza az **időbeállítások** lehetőséget, majd konfigurálja az adott eszközhöz tartozó időzónát és az elsődleges és másodlagos NTP-kiszolgálókat.  
    Az NTP-kiszolgálókra azért van szükség, mert az eszköznek szinkronizálnia kell az időt ahhoz, hogy hitelesíteni lehessen a felhőalapú szolgáltatóktól.
       
    Az **idő beállításai** lapon tegye a következőket:
    
    1. Az **időzóna** legördülő listában válassza ki azt az időzónát, amely megfelel az eszköz üzembe helyezésének földrajzi helyének.
        Az eszköz alapértelmezett időzónája a PST. Az eszköz minden ütemezett művelethez ezt az időzónát használja.

    2. Az **Elsődleges NTP-kiszolgáló** mezőbe írja be az eszköz elsődleges kiszolgálóját, vagy fogadja el a Time.Windows.com alapértelmezett értékét.  
        Győződjön meg arról, hogy a hálózat lehetővé teszi, hogy az NTP-forgalom áthaladjon az adatközpontból az internetre.

    3. Szükség esetén a **másodlagos NTP-kiszolgáló** mezőbe írja be az eszköz másodlagos kiszolgálóját.

    4. A beállított idő beállításainak ellenőrzéséhez és alkalmazásához válassza a **Beállítások alkalmazása** lehetőséget.

        ![Helyi webes felhasználói felület "Time Settings" lapja](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-5.png)

5. Választható A bal oldali ablaktáblán válassza a **tárolási beállítások** lehetőséget a tárterület rugalmasságának konfigurálásához az eszközön. Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Alapértelmezés szerint az eszközön lévő tárterület nem rugalmas, és adatvesztés történik, ha egy adatlemez meghibásodik az eszközön. Ha engedélyezi a rugalmas beállítást, a rendszer újrakonfigurálja az eszközön lévő tárolót, és az eszköz nem tudja elviselni az adatvesztés nélkül egy adatlemez meghibásodását. A tárterület rugalmasként való konfigurálása csökkenti az eszköz felhasználható kapacitását.

    > [!IMPORTANT] 
    > A rugalmasságot csak az eszköz aktiválása előtt lehet konfigurálni. 

    ![Helyi webes felhasználói felület "tárolási beállítások" lapja](./media/azure-stack-edge-deploy-connect-setup-activate/storage-settings.png)

6. A bal oldali ablaktáblán válassza a **felhő beállításai** lehetőséget, majd aktiválja az eszközt a Azure Portal Azure stack Edge szolgáltatásával.
    
    1. Az **aktiválási kulcs** mezőben adja meg azt az aktiválási kulcsot, amelyet a Azure stack Edge Pro [aktiválási kulcsának beszerzése](azure-stack-edge-deploy-prep.md#get-the-activation-key) című pontjában kapott.
    2. Kattintson az **Alkalmaz** gombra.
       
        ![Helyi webes felhasználói felület "Cloud Settings" (Felhőbeli beállítások) lapja](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-6.png)

    3. Először az eszköz aktiválva van. Az eszköz ekkor megkeresi a kritikus frissítéseket, és ha elérhető, a rendszer automatikusan alkalmazza a frissítéseket. Ekkor megjelenik egy értesítés erre a hatásra.

        A párbeszédpanelen egy helyreállítási kulcs is található, amelyet egy biztonságos helyen kell átmásolnia és menteni. Ez a kulcs az adatok helyreállításához használható abban az esetben, ha az eszköz nem tud elindulni.

        ![A helyi webes felhasználói felület "Cloud Settings" (Felhőbeli beállítások) lapja frissítve](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-7.png)

    4. Előfordulhat, hogy a frissítés sikeres befejeződése után néhány percet várnia kell. Az oldal frissül, és jelzi, hogy az eszköz aktiválása sikeres volt.

        ![Helyi webes felhasználói felület "Cloud Settings" (Felhőbeli beállítások) lap frissítve 2](./media/azure-stack-edge-deploy-connect-setup-activate/set-up-activate-8.png)

Az eszköz telepítése befejeződött. Mostantól megosztásokat is hozzáadhat az eszközhöz.

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Kapcsolódás fizikai eszközhöz
> * A fizikai eszköz beállítása és aktiválása

Ha szeretné megismerni, hogyan vihetők át adatok az Azure Stack Edge Pro-eszközével, tekintse meg a következőt:

> [!div class="nextstepaction"]
> [Adatok átvitele Azure stack Edge Pro-val](./azure-stack-edge-deploy-add-shares.md).
