---
title: Rövid útmutató Azure Stack Edge GPU konfigurálásához és üzembe helyezéséhez | Microsoft Docs
description: A Azure Stack Edge GPU üzembe helyezésének első lépései az eszköz fogadása után.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: quickstart
ms.date: 01/27/2021
ms.author: alkohli
ms.openlocfilehash: b6389152116a47bedbc9c2dc44231f3e1732fdc6
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055110"
---
# <a name="quickstart-get-started-with-azure-stack-edge-pro-with-gpu"></a>Gyors útmutató: az Azure Stack Edge Pro és a GPU használatának első lépései 

Ez a rövid útmutató részletesen ismerteti az Azure Stack Edge Pro GPU-eszköz telepítéséhez szükséges előfeltételeket és lépéseket. A gyorsútmutató lépései az Azure Portalon és az eszköz helyi webes felületén hajthatók végre. 

A teljes eljárásnak körülbelül 1,5 órán belül kell lennie. Részletes útmutatást az [oktatóanyag: Felkészülés a Azure stack Edge Pro GPU üzembe helyezése](azure-stack-edge-gpu-deploy-prep.md#deployment-configuration-checklist)című témakörben talál. 


## <a name="prerequisites"></a>Előfeltételek

A telepítése előtt győződjön meg arról, hogy az alábbi előfeltételek teljesülnek:

1. Az Azure Stack Edge Pro GPU eszközt a rendszer a webhelyre, a [kicsomagolt](azure-stack-edge-gpu-deploy-install.md#unpack-the-device) és a [rackhez csatlakoztatott](azure-stack-edge-gpu-deploy-install.md#rack-the-device)eszközre továbbítja. 
1. Konfigurálja úgy a hálózatot, hogy az eszköz el tudja érni a [felsorolt URL-mintákat és portokat](azure-stack-edge-gpu-system-requirements.md#networking-port-requirements). 
1. Tulajdonosi vagy közreműködői hozzáférése van az Azure-előfizetéshez.
1. A Azure Portal lépjen a **Home >-Előfizetések > az előfizetéses > erőforrás-szolgáltatók** elemre. Keresse meg `Microsoft.DataBoxEdge` és regisztrálja az erőforrás-szolgáltatót. `Microsoft.Devices`Ha a számítási feladatok üzembe helyezéséhez IoT hub erőforrást szeretne létrehozni, ismételje meg a műveletet.
1. Győződjön meg arról, hogy legalább 2 ingyenes, statikus, összefüggő IP-cím van a Kubernetes-csomópontokhoz, és legalább 1 statikus IP-címet IoT Edge szolgáltatáshoz. Minden egyes modulhoz vagy külső szolgáltatáshoz telepítenie kell egy további IP-címet.
1. Az eszközök konfigurálásához szükséges összes információt az [üzembe helyezési ellenőrzőlista](azure-stack-edge-gpu-deploy-checklist.md) tartalmazza. 


## <a name="deployment-steps"></a>A központi telepítés lépései

1. **Telepítés**: csatlakoztassa az 1. portot egy ügyfélhez egy fordított KÁBELLEL vagy USB Ethernet-adapteren keresztül. Csatlakoztasson legalább egy másik portot az adatokhoz, lehetőleg 25 GbE (a 3. PORTról a 6-os portra) az internetre legalább 1 GbE kapcsoló és SFP + Copper kábelek használatával. A biztosított tápkábelek csatlakoztatása a tápegységekhez és az energiaellátási kiosztások elkülönítése. Az eszköz bekapcsolásához nyomja meg a főkapcsoló gombot az előlapon.  

    Lásd: [Cavium FastlinQ 41000 Series együttműködési mátrix](https://www.marvell.com/documents/xalflardzafh32cfvi0z/) és [Mellanox Dual port 25G ConnectX-4 Channel hálózati adapter kompatibilis termékek](https://docs.mellanox.com/display/ConnectX4LxFirmwarev14271016/Firmware+Compatible+Products) a kompatibilis hálózati kábelek és kapcsolók beszerzéséhez.

    Itt látható az eszköz üzembe helyezéséhez szükséges minimális kábelezési konfiguráció:  ![ egy csatlakoztatott eszköz hátsó síkja](./media/azure-stack-edge-gpu-quickstart/backplane-min-cabling-1.png)

2. **Csatlakozás**: konfigurálja az IPv4-beállításokat a számítógépen lévő Ethernet-adapteren a **192.168.100.5** és az alhálózat **255.255.255.0** statikus IP-címével. Nyissa meg a böngészőt, és kapcsolódjon az eszköz helyi webes FELÜLETéhez a következő címen: https://192.168.100.10 . Ez eltarthat néhány percig. Ha megjelenik a biztonsági tanúsítvány figyelmeztetése, folytassa a weboldalt.

3. **Bejelentkezés**: Jelentkezzen be az eszközre az alapértelmezett jelszó *jelszó1*. Módosítsa az eszköz rendszergazdai jelszavát. A jelszónak 8 – 16 karakterből kell állnia, valamint a nagybetűs, a kisbetűs, a numerikus és a speciális karakterek közül 3.

4. **Hálózat konfigurálása**: fogadja el az alapértelmezett DHCP-konfigurációt a csatlakoztatott adatporthoz, ha DHCP-kiszolgálója van a hálózaton. Ha nem, adja meg az IP-címet, a DNS-kiszolgálót és az alapértelmezett átjárót. A [hálózati beállításokkal](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-network)kapcsolatos további információkért lásd:.

5. **Számítási hálózat konfigurálása**: hozzon létre egy virtuális kapcsolót egy port engedélyezésével az eszközön. Adjon meg 2 ingyenes, összefüggő statikus IP-címeket a Kubernetes-csomópontok számára abban a hálózatban, amelyet létrehozta a kapcsolót. Adjon meg legalább egy statikus IP-címet IoT Edge hub szolgáltatás számára a számítási modulok és az 1 statikus IP-cím eléréséhez minden olyan extra szolgáltatáshoz vagy tárolóhoz, amelyet a Kubernetes-fürtön kívül szeretne elérni. 

    A Kubernetes az összes tároló számítási feladat telepítéséhez szükséges. A [számítási hálózati beállításokkal](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#enable-compute-network)kapcsolatos további információkért tekintse meg a következőt:.

6. **Webproxy konfigurálása**: Ha a környezetben webproxyt használ, adja meg a webproxy-kiszolgáló IP-címét a ben `http://<web-proxy-server-FQDN>:<port-id>` . A hitelesítés beállítása **none** értékre. További információ a [webproxy beállításairól](azure-stack-edge-gpu-deploy-configure-network-compute-web-proxy.md#configure-web-proxy).

7. **Eszköz konfigurálása**: írja be az eszköz nevét és a DNS-tartományt, vagy fogadja el az alapértelmezett értékeket. 

8. A **frissítési kiszolgáló konfigurálása**: fogadja el az alapértelmezett Microsoft Update-kiszolgálót, vagy adja meg a Windows Server Update Services (WSUS) kiszolgálót és a kiszolgáló elérési útját. 

9. **Időbeállítások konfigurálása**: fogadja el az alapértelmezett időbeállításokat, vagy állítsa be az időzónát, az elsődleges NTP-kiszolgálót és a másodlagos NTP-kiszolgálót a helyi hálózaton vagy nyilvános kiszolgálóként.

10. **Tanúsítványok konfigurálása**: Ha módosította az eszköz nevét és/vagy a DNS-tartományt, akkor tanúsítványokat kell előállítania, vagy tanúsítványokat kell hozzáadnia az eszköz aktiválásához. 

    - A nem éles munkaterhelések teszteléséhez használja a [tanúsítványok előállítása lehetőséget](azure-stack-edge-gpu-deploy-configure-certificates.md#generate-device-certificates). 
    - Ha saját tanúsítványokat hoz létre, beleértve az aláíró lánc (oka) t, [adja hozzá a tanúsítványokat](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates) a megfelelő formátumhoz. Először töltse fel az aláíró láncot. Lásd: [tanúsítványok létrehozása](azure-stack-edge-gpu-create-certificates-tool.md) és [tanúsítványok feltöltése a helyi felhasználói felületen keresztül](azure-stack-edge-gpu-deploy-configure-certificates.md#bring-your-own-certificates).

11. **Aktiválás: az** aktiváló kulcs beszerzése 

    1. A Azure Portal nyissa meg a **Azure stack Edge-erőforrást > áttekintés > eszköz beállítása > aktiválja > kulcs létrehozását**. Másolja a kulcsot. 
    1. A helyi webes felhasználói felületen lépjen az első **lépések > aktiválás** elemre, és adja meg az aktiválási kulcsot. A kulcs alkalmazása után az eszköz eltarthat néhány percig az aktiváláshoz. Töltse le a `<device-serial-number>` . JSON fájlt, amikor a rendszer felszólítja, hogy biztonságosan tárolja a jövőbeli helyreállításhoz szükséges eszköz kulcsait. 

12. **Számítás konfigurálása**: a Azure Portal válassza az **Áttekintés > eszköz** lehetőséget. Ellenőrizze, hogy az eszköz **online állapotban** van-e. A bal oldali ablaktáblán válassza az **Edge számítási > első lépések > a peremhálózati számítási > számítási beállítások konfigurálása** lehetőséget. Adjon meg egy meglévő vagy új IoT Hub szolgáltatást, és várjon körülbelül 20 percet, amíg a számítási konfiguráció konfigurálva lenne. További információ az [oktatóanyagról: számítás konfigurálása Azure stack Edge Pro GPU-eszközön](azure-stack-edge-gpu-deploy-configure-compute.md)

Készen áll a számítási feladatok üzembe helyezésére az eszközön a [IoT Edge](azure-stack-edge-gpu-deploy-sample-module-marketplace.md)keresztül [, `kubectl` ](azure-stack-edge-gpu-create-kubernetes-cluster.md) az [Azure arc-kompatibilis Kubernetes](azure-stack-edge-gpu-deploy-arc-kubernetes-cluster.md)keresztül vagy az használatával. Ha a telepítés során problémák merülnek fel, lépjen az [eszközök problémáinak elhárítása](), a [problémák megoldása](azure-stack-edge-gpu-troubleshoot.md), a [tanúsítványok problémái](azure-stack-edge-gpu-certificate-troubleshooting.md)vagy a Kubernetes problémákra. 

## <a name="next-steps"></a>Következő lépések

[Azure Stack Edge Pro GPU telepítése](./azure-stack-edge-gpu-deploy-install.md)



