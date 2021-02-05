---
title: Oktatóanyag az Azure Stack Edge Pro R-eszköz hálózati beállításainak konfigurálásához Azure Portalban | Microsoft Docs
description: Az Azure Stack Edge Pro R üzembe helyezésére vonatkozó oktatóanyag arra utasítja Önt, hogy konfigurálja a fizikai eszköz hálózati, számítási és WebProxy-beállításait.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 02/04/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to connect and activate Azure Stack Edge Pro R so I can use it to transfer data to Azure.
ms.openlocfilehash: 11c1d76b5784587f234455f81595778897569eb1
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2021
ms.locfileid: "99594350"
---
# <a name="tutorial-configure-network-for-azure-stack-edge-pro-r"></a>Oktatóanyag: a hálózat konfigurálása Azure Stack Edge Pro R-hez

Ez az oktatóanyag azt ismerteti, hogyan konfigurálhatja a hálózatot a Azure Stack Edge Pro R-eszközhöz a helyi webes felhasználói felület használatával.

A kapcsolódási folyamat körülbelül 20 percet vesz igénybe.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
>
> * Előfeltételek
> * Hálózat konfigurálása
> * Számítási hálózat engedélyezése
> * A webproxy konfigurálása


## <a name="prerequisites"></a>Előfeltételek

Az Azure Stack Edge Pro R-eszköz konfigurálása és beállítása előtt győződjön meg az alábbiakról:

* A fizikai eszközt a [Azure stack Edge Pro R telepítésének](azure-stack-edge-gpu-deploy-install.md)részletesen telepítette.
* Az eszköz helyi webes felhasználói felületéhez kapcsolódott a [csatlakozás Azure stack Edge Pro R](azure-stack-edge-gpu-deploy-connect.md) -hez című rész részletesen


## <a name="configure-network"></a>Hálózat konfigurálása

A **Get Started** (első lépések) oldal megjeleníti azokat a beállításokat, amelyek szükségesek a fizikai eszköz konfigurálásához és regisztrálásához az Azure stack Edge szolgáltatással. 

Az eszközhöz tartozó hálózat konfigurálásához kövesse az alábbi lépéseket.

1. Az eszköz helyi webes FELÜLETén nyissa meg az **első lépések** lapot. 

2. A hálózat **csempén válassza** a **Konfigurálás** lehetőséget a **hálózat** lapra való ugráshoz. 
    
    <!--![Local web UI "Network settings" tile](./media/azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy/network-1.png)-->

    A fizikai eszközön négy hálózati adapter található. A PORT 1 és PORT 2 jelzésű portok 1 Gbps hálózati adapterek. A 3. és a 4. port mind a 10/25 GB-os hálózati adapter. Az 1. PORT automatikusan csak felügyeleti portként van konfigurálva, a 2-es port pedig minden adatport. A **hálózat** lap az alább látható.
    
    ![Helyi webes felhasználói felület "hálózati beállítások" lapja](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2.png)

   
3. A hálózati beállítások módosításához válasszon ki egy portot, és a megjelenő jobb oldali panelen módosítsa az IP-címet, az alhálózatot, az átjárót, az elsődleges DNS-t és a másodlagos DNS-t. 

    - Ha az 1. portot választja, láthatja, hogy az előre be van konfigurálva statikusként. 

        ![Helyi webes felhasználói felület – 1. port – hálózati beállítások](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-3.png)

    - Ha a 2-es portot, a 3. portot vagy a 4-es portot választja, a rendszer alapértelmezés szerint az összes portot DHCP-ként konfigurálja.

        ![Helyi webes felhasználói felület – 3. port – hálózati beállítások](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-4.png)

    A hálózati beállítások konfigurálása közben figyeljen a következőkre:

   * Ha a DHCP engedélyezve van a környezetben, a hálózati adapterek beállítása automatikusan megtörténik. A rendszer automatikusan hozzárendel egy IP-címet, egy alhálózatot, egy átjárót és egy DNS-t.
   * Ha a DHCP nincs engedélyezve, szükség esetén statikus IP-címeket is hozzárendelhet.
   * A hálózati adaptert IPv4-ként is konfigurálhatja.
   * A hálózati kártya (NIC) összevonása vagy a hivatkozás összesítése nem támogatott Azure Stack Edge esetén.
   * Bármely port sorozatszáma a csomópont sorozatszámának felel meg.
    <!--* On the 25-Gbps interfaces, you can set the RDMA (Remote Direct Access Memory) mode to iWarp or RoCE (RDMA over Converged Ethernet). Where low latencies are the primary requirement and scalability is not a concern, use RoCE. When latency is a key requirement, but ease-of-use and scalability are also high priorities, iWARP is the best candidate.-->
    Az eszköz hálózatának konfigurálása után az oldal frissítései az alább látható módon frissülnek.

    ![Helyi webes felhasználói felület "hálózati beállítások" 2. lapja](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/network-2a.png)<!--change-->


     >[!NOTE]
     >
     > * Azt javasoljuk, hogy a hálózati adapter helyi IP-címét ne módosítsa statikusról DHCP-re, hacsak nem rendelkezik másik IP-címmel az eszközhöz való csatlakozáshoz. Ha egy hálózati adaptert használ és DHCP-re vált, a DHCP-címeket nem lehet meghatározni. Ha egy DHCP-címről szeretne váltani, várjon, amíg az eszköz aktiválta a szolgáltatást, majd módosítsa a következőt:. Ezután megtekintheti az összes adapter IP-címeit az **eszköz tulajdonságai** között a szolgáltatás Azure Portal.


    A hálózati beállítások konfigurálása és alkalmazása után válassza a **Tovább: számítás** lehetőséget a számítási hálózat konfigurálásához.

## <a name="enable-compute-network"></a>Számítási hálózat engedélyezése

Az alábbi lépéseket követve engedélyezheti a számítást és konfigurálhatja a számítási hálózatot. 

1. A **számítás** lapon válasszon ki egy hálózati adaptert, amelyet szeretne engedélyezni a számítási feladatokhoz. 

    ![Számítási oldal a helyi felhasználói felületen 2](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-2.png)

1. A **hálózati beállítások** párbeszédpanelen válassza az **Engedélyezés** lehetőséget. Ha engedélyezi a számítást, a rendszer létrehoz egy virtuális kapcsolót az eszközön az adott hálózati adapteren. A virtuális kapcsolót a számítási infrastruktúra használja az eszközön. 
    
1. **Kubernetes-csomópontok IP**-címeinek kiosztása. Ezek a statikus IP-címek a számítási virtuális géphez tartoznak.  

    *N*-csomópontos eszköz esetén legalább *n + 1* IPv4-cím (vagy több) összefüggő tartománya van megadva a számítási virtuális géphez a kezdő és záró IP-címek használatával. A megadott Azure Stack Edge egy 1 csomópontos eszköz, amely legalább 2 egybefüggő IPv4-címet biztosít. Ezeknek az IP-címnek ugyanabban a hálózaton kell lennie, ahol a számítást engedélyezte, és a virtuális kapcsoló létrejött.

    > [!IMPORTANT]
    > A Kubernetes on Azure Stack Edge a 172.27.0.0/16 alhálózatot használja a pod és a 172.28.0.0/16 alhálózat számára a szolgáltatáshoz. Győződjön meg arról, hogy ezek nincsenek használatban a hálózaton. Ha ezek az alhálózatok már használatban vannak a hálózaton, ezeket az alhálózatokat a `Set-HcsKubeClusterNetworkInfo` parancsmagnak az eszköz PowerShell-felületéről való futtatásával módosíthatja. További információkért lásd: a [Kubernetes Pod és a Service alhálózatok módosítása](azure-stack-edge-gpu-connect-powershell-interface.md#change-kubernetes-pod-and-service-subnets).


1. **Kubernetes külső szolgáltatás IP**-címeinek kiosztása. Ezek a terheléselosztási IP-címek is. Ezek a folytonos IP-címek olyan szolgáltatások, amelyeket a Kubernetes-fürtön kívül szeretne közzétenni, és a statikus IP-címtartományt az elérhető szolgáltatások számától függően kell megadnia. 
    
    > [!IMPORTANT]
    > Javasoljuk, hogy legalább 1 IP-címet határozzon meg a Azure Stack Edge Pro R hub szolgáltatáshoz a számítási modulok eléréséhez. Ezután további IP-címeket is megadhat más szolgáltatások/IoT Edge modulok számára (1/szolgáltatás/modul), amelyeket a fürtön kívülről kell elérni. A szolgáltatás IP-címei később is frissíthetők. 
    
1. Kattintson az **Alkalmaz** gombra.

    ![Számítási lap a helyi felhasználói felületen 3](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-3.png)

1. A konfiguráció néhány percet vesz igénybe, és előfordulhat, hogy frissítenie kell a böngészőt. Láthatja, hogy a megadott port engedélyezve van a számítási feladatokhoz. 
 
    ![Számítási lap a helyi felhasználói felületen 4](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/compute-network-4.png)

    Válassza a **Tovább: webproxy** lehetőséget a webproxy konfigurálásához.  

  
## <a name="configure-web-proxy"></a>A webproxy konfigurálása

Ez egy opcionális konfiguráció.

> [!IMPORTANT]
> * Ha engedélyezi a számítást, és a IoT Edge modult használja a Azure Stack Edge Pro R-eszközön, javasoljuk, hogy a webproxy-hitelesítést a **none** értékre állítsa be. Az NTLM nem támogatott.
>* A proxy-Auto config (PAC) fájlok nem támogatottak. A PAC-fájlok azt határozzák meg, hogy a böngészők és más felhasználói ügynökök hogyan tudják automatikusan kiválasztani a megfelelő proxykiszolgálót (hozzáférési módszer) egy adott URL beolvasásához. Azok a proxyk, amelyek megpróbálják feltartóztatni és beolvasni az összes forgalmat (majd újra aláírni a saját tanúsítvánnyal), nem kompatibilisek, mert a proxy tanúsítványa nem megbízható. Az átlátszó proxyk általában jól működnek Azure Stack Edge Pro R-vel. nem transzparens webproxyk nem támogatottak.


1. A **webproxy beállításai** oldalon hajtsa végre a következő lépéseket:

    1. A **webproxy URL-címe** mezőbe írja be az URL-címet a következő formátumban: `http://host-IP address or FQDN:Port number` . A HTTPS URL-címek nem támogatottak.

    2. A **hitelesítés** területen válassza a **nincs** vagy az **NTLM** lehetőséget. Ha engedélyezi a számítást, és a IoT Edge modult használja a Azure Stack Edge Pro R-eszközön, javasoljuk, hogy a webproxy-hitelesítést a **none** értékre állítsa be. Az **NTLM** nem támogatott.

    3. Ha hitelesítést használ, adjon meg egy felhasználónevet és egy jelszót.

    4. A konfigurált Webproxy-beállítások érvényesítéséhez és alkalmazásához kattintson az **alkalmaz** gombra.
    
   ![Helyi webes felhasználói felület "webproxy beállításai" 2. oldal](./media/azure-stack-edge-pro-r-deploy-configure-network-compute-web-proxy/web-proxy-2.png)

2. A beállítások alkalmazása után kattintson a **Tovább gombra: eszköz**.


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban megismerte a következőket:

> [!div class="checklist"]
> * Előfeltételek
> * Hálózat konfigurálása
> * Számítási hálózat engedélyezése
> * A webproxy konfigurálása


Az Azure Stack Edge Pro R-eszköz beállításával kapcsolatos további információkért lásd:

> [!div class="nextstepaction"]
> [Eszközbeállítások megadása](./azure-stack-edge-pro-r-deploy-set-up-device-update-time.md)
