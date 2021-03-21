---
title: Oktatóanyag Azure Stack Edge mini R-eszköz hálózati beállításainak konfigurálásához Azure Portal
description: Az Azure Stack Edge mini R üzembe helyezésére vonatkozó oktatóanyag arra utasítja Önt, hogy konfigurálja a fizikai eszköz hálózati, számítási és WebProxy-beállításait.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Mini R so I can use it to transfer data to Azure.
ms.openlocfilehash: a202ee1d708feebc7643fa6f20aafa9d8603fc38
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "100546771"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-mini-r"></a>Oktatóanyag: a hálózat konfigurálása Azure Stack Edge mini R-hez

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja a hálózatot a Azure Stack Edge mini R-eszközhöz egy beépített GPU-val a helyi webes felhasználói felület használatával.

A kapcsolódási folyamat körülbelül 20 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Hálózat konfigurálása
> * Számítási hálózat engedélyezése
> * A webproxy konfigurálása


## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge mini R-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* Telepítette a fizikai eszközt a [Azure stack Edge mini R telepítésének](azure-stack-edge-gpu-deploy-install.md)részletesen.
* Csatlakoztatta az eszköz helyi webes FELÜLETéhez a [csatlakozás Azure stack Edge mini R](azure-stack-edge-mini-r-deploy-connect.md) -hez című rész részletesen


## <a name="configure-network"></a>Hálózat konfigurálása

A **Get Started** (első lépések) oldal megjeleníti azokat a beállításokat, amelyek szükségesek a fizikai eszköz konfigurálásához és regisztrálásához az Azure stack Edge szolgáltatással. 

Az eszközhöz tartozó hálózat konfigurálásához kövesse az alábbi lépéseket.

1. Az eszköz helyi webes FELÜLETén nyissa meg az **első lépések** lapot. 

2. Ha egy nulla napos frissítésre van szükség, ezt itt teheti meg, ha az adatportot vezetékes kapcsolatban konfigurálja. Az eszközhöz tartozó vezetékes kapcsolatok beállításával kapcsolatos további információkért lásd: [az eszköz csatlakoztatása](azure-stack-edge-mini-r-deploy-install.md#cable-the-device). A frissítés után eltávolíthatja a vezetékes kapcsolatát.

3. Tanúsítványok létrehozása Wi-Fi és az aláírási lánchoz. Az aláíró láncnak és a Wi-Fi tanúsítványnak DER formátumúnak kell lennie *. cer* kiterjesztéssel. Útmutatásért lásd: [tanúsítványok létrehozása](azure-stack-edge-gpu-manage-certificates.md).

4. A helyi webes KEZELŐFELÜLETen lépjen az első **lépések** elemre. A **Biztonság** csempén válassza a **tanúsítványok** lehetőséget, majd válassza a **Konfigurálás** lehetőséget. 

    [![Helyi webes felhasználói felület "tanúsítványok" lapja](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/get-started-1.png#lightbox)

    1. Válassza a **+ tanúsítvány hozzáadása** lehetőséget. 
    
        [![Helyi webes felhasználói felület "tanúsítványok" 1. oldal](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-1.png#lightbox)

    2. Töltse fel az aláíró láncot, és válassza az **alkalmaz** lehetőséget.

        ![Helyi webes felhasználói felület "tanúsítványok" 2. lapja](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-2.png)

    3. Ismételje meg az eljárást a Wi-Fi tanúsítvánnyal. 

        ![Helyi webes felhasználói felület "tanúsítványok" 3. oldala](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-4.png)

    4. Az új tanúsítványokat a **tanúsítványok** oldalon kell megjeleníteni. 
    
        [![Helyi webes felhasználói felület "tanúsítványok" – 4. oldal](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-cert-5.png#lightbox)

    5. **Lépjen vissza a** kezdéshez.

3. A **hálózat** csempén válassza a **Konfigurálás** lehetőséget.  
    
    A fizikai eszközön öt hálózati adapter található. A PORT 1 és PORT 2 jelzésű portok 1 Gbps hálózati adapterek. A 3. és a 4. port mind a 10 GB/s hálózati adapter. Az ötödik port a Wi-Fi port. 

    [![Helyi webes felhasználói felület "hálózati beállítások" 1. oldal](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png)](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-1.png#lightbox)    
    
    Válassza ki a Wi-Fi portot, és konfigurálja a portbeállítások beállításait. 
    
    > [!IMPORTANT]
    > Javasoljuk, hogy statikus IP-címet állítson be a Wi-Fi porthoz.  

    ![Helyi webes felhasználói felület "hálózati beállítások" 2. lapja](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-2.png)

    A **hálózati** oldal a Wi-Fi portbeállítások alkalmazása után frissül.

    ![Helyi webes felhasználói felület "hálózati beállítások" 3. lapja](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/configure-wifi-4.png)
   
4. Válassza a **Wi-Fi profil hozzáadása** lehetőséget, és töltse fel Wi-Fi-profilját. 

    ![Helyi webes felhasználói felület "Port WiFi hálózati beállításai" 1](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-1.png)
    
    A vezeték nélküli hálózati profil a vezeték nélküli hálózathoz való kapcsolódáshoz szükséges SSID-t (hálózati nevet), jelszót és biztonsági információt tartalmazza. A környezet Wi-Fi profilját a hálózati rendszergazdától kérheti le.

    ![Helyi webes felhasználói felület "Port WiFi hálózati beállításai" 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-2.png)

    A profil hozzáadása után az Wi-Fi profilok listája frissül, hogy tükrözze az új profilt. A profilnak le kell mutatnia a **kapcsolat állapotát** **leválasztva**. 

    ![Helyi webes felhasználói felület "Port WiFi hálózati beállításai" 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-3.png)


5. A vezeték nélküli hálózati profil sikeres betöltését követően kapcsolódjon ehhez a profilhoz. Válassza **a kapcsolódás Wi-Fi profilhoz** lehetőséget. 

    ![Helyi webes felhasználói felület "Port Wi-Fi hálózati beállítások" 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-4.png)

6. Válassza ki az előző lépésben hozzáadott Wi-Fi profilt, majd válassza az **alkalmaz** lehetőséget. 

    ![Helyi webes felhasználói felület "Port Wi-Fi hálózati beállítások" 5](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-5.png)

    A **kapcsolat állapotának** frissítenie kell a **Connected** értékre. A jel erőssége a jel minőségének jelzésére frissül. 

    ![Helyi webes felhasználói felület "Port Wi-Fi hálózati beállítások" 6](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/add-wifi-profile-6.png)

    > [!NOTE]
    > Nagy mennyiségű adatok átviteléhez ajánlott vezetékes kapcsolat használata a vezeték nélküli hálózat helyett. 

6. Az eszközön válassza le az 1. PORTOT a laptopról. 

7. A hálózati beállítások konfigurálása közben figyeljen a következőkre:

   - Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. A rendszer automatikusan hozzárendel egy IP-címet, egy alhálózatot, egy átjárót és egy DNS-t.
   - Ha a DHCP nincs engedélyezve, szükség esetén statikus IP-címeket is hozzárendelhet.
   - A hálózati adaptert IPv4-ként is konfigurálhatja.
   - A hálózati kártya (NIC) összevonása vagy a hivatkozás összesítése nem támogatott Azure Stack Edge esetén.
   - Bármely port sorozatszáma a csomópont sorozatszámának felel meg. A K sorozatú eszközök esetében csak egy sorozatszám jelenik meg.

     >[!NOTE] 
     > Azt javasoljuk, hogy a hálózati adapter helyi IP-címét ne módosítsa statikusról DHCP-re, hacsak nem rendelkezik másik IP-címmel az eszközhöz való csatlakozáshoz. Ha egy hálózati adaptert használ és DHCP-re vált, a DHCP-címeket nem lehet meghatározni. Ha DHCP-címre szeretne váltani, várjon, amíg az eszköz regisztrálja magát a szolgáltatásban, majd hajtsa végre a módosítást. Ezután megtekintheti az összes adapter IP-címeit az **eszköz tulajdonságai** között a szolgáltatás Azure Portal.

A hálózati beállítások konfigurálása és alkalmazása után válassza a **Tovább: számítás** lehetőséget a számítási hálózat konfigurálásához.

## <a name="enable-compute-network"></a>Számítási hálózat engedélyezése

Az alábbi lépéseket követve engedélyezheti a számítást és konfigurálhatja a számítási hálózatot. 


1. A **számítás** lapon válasszon ki egy hálózati adaptert, amelyet szeretne engedélyezni a számítási feladatokhoz. 

    ![Számítási oldal a helyi felhasználói felületen 2](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-1.png)

1. A **hálózati beállítások** párbeszédpanelen válassza az **Engedélyezés** lehetőséget. Ha engedélyezi a számítást, a rendszer létrehoz egy virtuális kapcsolót az eszközön az adott hálózati adapteren. A virtuális kapcsolót a számítási infrastruktúra használja az eszközön. 
    
1. **Kubernetes-csomópontok IP**-címeinek kiosztása. Ezek a statikus IP-címek a számítási virtuális géphez tartoznak.  

    *N*-csomópontos eszköz esetén legalább *n + 1* IPv4-cím (vagy több) összefüggő tartománya van megadva a számítási virtuális géphez a kezdő és záró IP-címek használatával. A megadott Azure Stack Edge egy 1 csomópontos eszköz, amely legalább 2 egybefüggő IPv4-címet biztosít.

    > [!IMPORTANT]
    > A Kubernetes on Azure Stack Edge a 172.27.0.0/16 alhálózatot használja a pod és a 172.28.0.0/16 alhálózat számára a szolgáltatáshoz. Győződjön meg arról, hogy ezek nincsenek használatban a hálózaton. Ha ezek az alhálózatok már használatban vannak a hálózaton, ezeket az alhálózatokat a `Set-HcsKubeClusterNetworkInfo` parancsmagnak az eszköz PowerShell-felületéről való futtatásával módosíthatja. További információkért lásd: a [Kubernetes Pod és a Service alhálózatok módosítása](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. **Kubernetes külső szolgáltatás IP**-címeinek kiosztása. Ezek a terheléselosztási IP-címek is. Ezek a folytonos IP-címek olyan szolgáltatásokhoz tartoznak, amelyeket a Kubernetes-fürtön kívül kíván tenni, és a statikus IP-címtartományt az elérhető szolgáltatások számától függően kell megadnia. 
    
    > [!IMPORTANT]
    > Javasoljuk, hogy legalább 1 IP-címet határozzon meg a Azure Stack Edge mini R hub szolgáltatáshoz a számítási modulok eléréséhez. Ezután további IP-címeket is megadhat más szolgáltatások/IoT Edge modulok számára (1/szolgáltatás/modul), amelyeket a fürtön kívülről kell elérni. A szolgáltatás IP-címei később is frissíthetők. 
    
1. Kattintson az **Alkalmaz** gombra.

    ![Számítási lap a helyi felhasználói felületen 3](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. A konfiguráció néhány percet vesz igénybe, és előfordulhat, hogy frissítenie kell a böngészőt. Láthatja, hogy a megadott port engedélyezve van a számítási feladatokhoz. 
 
    ![Számítási lap a helyi felhasználói felületen 4](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Válassza a **Tovább: webproxy** lehetőséget a webproxy konfigurálásához.  

  
## <a name="configure-web-proxy"></a>A webproxy konfigurálása

Ez egy opcionális konfiguráció.

> [!IMPORTANT]
> * Ha engedélyezi a számítást, és IoT Edge modult használ a Azure Stack Edge mini R-eszközön, javasoljuk, hogy a webproxy-hitelesítést ne a **none** értékre állítsa be. Az NTLM nem támogatott.
>* A proxy-Auto config (PAC) fájlok nem támogatottak. A PAC-fájlok azt határozzák meg, hogy a böngészők és más felhasználói ügynökök hogyan tudják automatikusan kiválasztani a megfelelő proxykiszolgálót (hozzáférési módszer) egy adott URL beolvasásához. Azok a proxyk, amelyek megpróbálják feltartóztatni és beolvasni az összes forgalmat (majd újra aláírni a saját tanúsítvánnyal), nem kompatibilisek, mert a proxy tanúsítványa nem megbízható. Általában az átlátszó proxyk jól működnek Azure Stack Edge mini R-vel. nem transzparens webproxyk nem támogatottak.


1. A **webproxy beállításai** oldalon hajtsa végre a következő lépéseket:

    1. A **webproxy URL-címe** mezőbe írja be az URL-címet a következő formátumban: `http://host-IP address or FQDN:Port number` . A HTTPS URL-címek nem támogatottak.

    2. A **Hitelesítés** területen válassza a **Nincs** vagy az **NTLM** lehetőséget. Ha engedélyezi a számítást, és IoT Edge modult használ a Azure Stack Edge mini R-eszközön, javasoljuk, hogy a webproxy-hitelesítést ne állítsa be a **none** értékre. Az **NTLM** nem támogatott.

    3. Ha hitelesítést használ, adjon meg egy felhasználónevet és egy jelszót.

    4. A konfigurált Webproxy-beállítások érvényesítéséhez és alkalmazásához kattintson az **alkalmaz** gombra.
    
   ![Helyi webes felhasználói felület "Webproxy-beállítások" lapja](./media/azure-stack-edge-mini-r-deploy-configure-network-compute-web-proxy/web-proxy-1.png)

2. A beállítások alkalmazása után kattintson a **Tovább gombra: eszköz**.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Hálózat konfigurálása
> * Számítási hálózat engedélyezése
> * A webproxy konfigurálása


Az Azure Stack Edge mini R-eszköz beállításával kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Eszközbeállítások megadása](./azure-stack-edge-mini-r-deploy-set-up-device-update-time.md)
