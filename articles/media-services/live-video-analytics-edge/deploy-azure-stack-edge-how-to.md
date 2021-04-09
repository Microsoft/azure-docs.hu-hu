---
title: Élő video Analytics üzembe helyezése Azure Stack Edge-ben
description: Ez a cikk azokat a lépéseket sorolja fel, amelyek segítséget nyújtanak az élő videók elemzésének üzembe helyezéséhez az Azure Stack Edge-ben.
ms.topic: how-to
ms.date: 09/09/2020
ms.openlocfilehash: b5be85e93b81f5cf50284533f21e688384558494
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105561151"
---
# <a name="deploy-live-video-analytics-on-azure-stack-edge"></a>Élő video Analytics üzembe helyezése Azure Stack Edge-ben

Ez a cikk azokat a lépéseket sorolja fel, amelyek segítséget nyújtanak az élő videók elemzésének üzembe helyezéséhez az Azure Stack Edge-ben. Miután az eszközt beállította és aktiválta, készen áll az élő video Analytics üzembe helyezésére. 

Az élő videós elemzések esetében IoT Hubon keresztül fogjuk telepíteni, de az Azure Stack Edge-erőforrások elérhetővé teszik a Kubernetes API-t, amely lehetővé teszi az ügyfél számára, hogy további, nem IoT Hub, az élő videók elemzéséhez használható megoldásokat telepítsen. 

> [!TIP]
> A Kubernetes (K8s) API használata egyéni telepítéshez speciális eset. Azt javasoljuk, hogy az ügyfél az Kubernetes API használata helyett IoT Hub használatával hozzon létre Edge-modulokat és telepítsen minden Azure Stack Edge-erőforrást. Ebből a cikkből megtudhatja, hogyan helyezheti üzembe az élő videó elemzési modulját a IoT Hub használatával.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés, amelyhez [tulajdonosi jogosultságokkal](../../role-based-access-control/built-in-roles.md#owner)rendelkezik.
* [Azure stack Edge](../../databox-online/azure-stack-edge-gpu-deploy-prep.md) -erőforrás
   
* [Egy IoT Hub](../../iot-hub/iot-hub-create-through-portal.md)
* Az élő videó elemzési moduljának [egyszerű szolgáltatása](./create-custom-azure-resource-manager-role-how-to.md#create-service-principal) .

   Használja az alábbi régiók egyikét, ahol elérhető IoT Hub: USA 2. keleti régiója, az USA középső régiója, az USA északi középső régiója, az USA 2. nyugati régiója, az USA nyugati középső régiója, Kelet-Kanada, Egyesült Királyság déli régiója, Közép-Franciaország, Dél-Németország, Észak-Svájc, Nyugat-Svájc és Nyugat-Japán.
* Tárfiók

    Javasoljuk, hogy az általános célú v2-(GPv2-) tárolási fiókokat használja.  
    További információ az [általános célú v2-alapú Storage-fiókról](../../storage/common/storage-account-upgrade.md?tabs=azure-portal).
* A [Visual Studio Code](https://code.visualstudio.com/) a fejlesztői gépen. Győződjön meg arról, hogy rendelkezik az [Azure IoT Tools bővítménnyel](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).
* Győződjön meg arról, hogy a fejlesztői számítógép hálózata csatlakozik a speciális üzenetsor-kezelési protokoll engedélyezéséhez a 5671-es porton keresztül. Ez a telepítő lehetővé teszi az Azure IoT-eszközök számára az Azure IoT Hub való kommunikációt.

## <a name="configuring-azure-stack-edge-for-using-live-video-analytics"></a>Azure Stack Edge konfigurálása élő videó-elemzések használatához

Az Azure Stack Edge egy szolgáltatásként nyújtott hardveres megoldás, és egy hálózati adatátviteli képességekkel rendelkező AI-kompatibilis Edge számítástechnikai eszköz. További információ a [Azure stack Edge és a részletes beállítási utasításokról](../../databox-online/azure-stack-edge-deploy-prep.md). Az első lépésekhez kövesse az alábbi hivatkozások utasításait:

* [Azure Stack Edge/Data Box Gateway erőforrás létrehozása](../../databox-online/azure-stack-edge-deploy-prep.md)
* [Telepítés és beállítás](../../databox-online/azure-stack-edge-deploy-install.md)
* [Kapcsolatok és aktiválás](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md)
* [IoT Hub csatlakoztatása Azure Stack Edge-hez](../../databox-online/azure-stack-edge-gpu-deploy-configure-compute.md#configure-compute)
### <a name="enable-compute-prerequisites-on-the-azure-stack-edge-local-ui"></a>A számítási előfeltételek engedélyezése az Azure Stack Edge helyi felhasználói felületén

A folytatás előtt győződjön meg az alábbiakról:

* Aktiválta Azure Stack Edge-erőforrását.
* Hozzáférése van egy PowerShell 5,0 vagy újabb rendszert futtató Windows rendszerű ügyfélhez az Azure Stack Edge-erőforráshoz való hozzáféréshez.
* Kubernetes-fürt üzembe helyezéséhez konfigurálnia kell az Azure Stack Edge-erőforrást a [helyi webes felhasználói felületén](../../databox-online/azure-stack-edge-deploy-connect-setup-activate.md#connect-to-the-local-web-ui-setup)keresztül. 
    
    * A számítás engedélyezéséhez az eszköz helyi webes FELÜLETén nyissa meg a számítási lapot.
    
        * Válasszon ki egy hálózati adaptert, amelyet szeretne engedélyezni a számításhoz. Válassza az Engedélyezés lehetőséget. A számítási eredmények lehetővé teszik, hogy az eszközön egy virtuális kapcsolót hozzanak létre az adott hálózati adapteren.
        * Hagyja üresen a Kubernetes test Node IP-címei és a Kubernetes külső szolgáltatások IP-címei mezőt.
        * Alkalmazás kiválasztása – a művelet körülbelül 2 percet vesz igénybe.
        
        > [!div class="mx-imgBorder"]
        > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/azure-stack-edge-commercial.png" alt-text=" Az Azure Stack Edge helyi felhasználói felületének számítási előfeltételei":::

        * Ha a DNS nincs konfigurálva a Kubernetes API-hoz és Azure Stack Edge-erőforráshoz, akkor frissítheti az ablak gazdagépének fájlját.
        
            * Szövegszerkesztő megnyitása rendszergazdaként
            * A "C:\Windows\System32\drivers\etc\hosts" fájl megnyitása
            * Adja hozzá a Kubernetes API-eszköznév IPv4-nevét és állomásnevét a fájlhoz. (Ez az információ a Azure Stack Edge portál eszközök szakaszában található.)
            * Mentés és bezárás

### <a name="deploy-live-video-analytics-edge-module-using-azure-portal"></a>Élő videó Analytics Edge-modul üzembe helyezése Azure Portal használatával

Ebben az esetben csak az [élő videók elemzésének a IoT hub használatával történő üzembe helyezésével](deploy-iot-edge-device.md)fogunk konkrét lépéseket végrehajtani.

1. Ugorja át a felhasználó és csoport létrehozásának lépéseit, és lépjen az [élő videó Analytics Edge-modul üzembe helyezéséhez](deploy-iot-edge-device.md#deploy-live-video-analytics-edge-module) . Kövesse az itt említett lépéseket.
1. A tároló létrehozási beállításainál nem szükséges környezeti változókat beállítania. Tehát ugorja át ezt a lépést.
1. Nyissa meg a tároló létrehozása beállítások lapot.

   * Másolja és illessze be a következő JSON-t a mezőbe, hogy korlátozza a modul által létrehozott naplófájlok méretét.
    
      ```json
        "HostConfig": {
                    "LogConfig": {
                        "Type": "",
                        "Config": {
                        "max-size": "10m",
                        "max-file": "10"
                        }
                    },
                    "Binds": [
                        "/var/media/:/var/media/",
                        "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                    ]
                    }
      ```
    
      > [!NOTE]
      > Ha megosztott gRPC protokollt használ, használja a gazdagép IPC üzemmódját a megosztott memória eléréséhez az élő videó elemzése és a következtetési megoldások között.
   
      ```json
          "HostConfig": {
                        "LogConfig": {
                            "Type": "",
                            "Config": {
                            "max-size": "10m",
                            "max-file": "10"`
                            }
                        },
                        "Binds": [
                            "/var/media/:/var/media/",
                            "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                        ],
                        "IpcMode": "host",
                        "ShmSize": 1536870912
                    }
      ```

      > [!NOTE]
      > A JSON "kötések" szakasza 2 bejegyzést tartalmaz. A fenti kötési szakaszban említett könyvtárakat a rendszer automatikusan létrehozza a LVA.  
        Nyugodtan frissítheti a peremhálózati eszköz kötéseit, de ha igen, akkor győződjön meg arról, hogy ezek a címtárak léteznek az eszközön.
    
    * "/var/lib/azuremediaservices:/var/lib/azuremediaservices": ezzel a beállítással lehet az állandó alkalmazás-konfigurációs adatait a tárolóból kötni, és a peremhálózati eszközön tárolni.
    * "/var/Media:/var/media": ez köti össze az adathordozó-mappákat a peremhálózati eszköz és a tároló között. Ez a videó felvételek tárolására szolgál, ha olyan Media Graph-topológiát futtat, amely támogatja a videoklipek peremhálózati eszközön való tárolását.
        
1. Folytassa a doc lépéseit, és töltse ki a modul Twin beállításait.
1. Válassza a **tovább**: útvonalak szakaszt az útvonalak szakaszhoz. Útvonalak meghatározása.

    Tartsa meg az alapértelmezett útvonalakat, és válassza a Tovább: felülvizsgálat + létrehozás lehetőséget a felülvizsgálati szakasz folytatásához.
1. [Az üzemelő példány áttekintése és ellenőrzése](deploy-iot-edge-device.md#review-deployment)

#### <a name="optional-setup-docker-volume-mounts"></a>Választható Docker-kötet csatlakoztatásának beállítása

Ha szeretné megtekinteni a munkakönyvtárban lévő adatmegjelenítést, kövesse az alábbi lépéseket a Docker-kötet csatlakoztatásához a telepítés előtt. 

Ezek a lépések egy átjáró felhasználójának létrehozását és a fájlmegosztás beállítását fedik le az élő videó Analytics munkakönyvtárának és a Live Video Analytics Media mappa tartalmának megtekintéséhez.

> [!NOTE]
> A kötési csatlakoztatások támogatottak, de a Volume mounts lehetővé teszi az adatmegjelenítést, és szükség esetén távolról másolva. Használhatja a kötés és a kötet csatlakoztatását is, de nem mutathat ugyanarra a tároló elérési útra.

1. Nyissa meg Azure Portal, és lépjen az Azure Stack Edge-erőforráshoz.
1. Hozzon létre egy **átjáró-felhasználót** , amely hozzáférhet a megosztásokhoz.
    
    1. A bal oldali navigációs ablaktáblán kattintson a **Cloud Storage Gateway** elemre.
    1. A bal oldali navigációs ablaktáblán kattintson a **felhasználók** elemre.
    1. Kattintson az ion **+ felhasználó hozzáadása** lehetőségre a Felhasználónév és a jelszó beállításához. (Ajánlott: `lvauser` ).
    1. Kattintson a **Hozzáadás** gombra.
    
1. **Helyi megosztás** létrehozása az élő videók elemzésének megőrzéséhez.

    1. Kattintson a **Cloud Storage Gateway – >megosztások** lehetőségre.
    1. Kattintson a **+ megosztások hozzáadása** elemre.
    1. Adja meg a megosztás nevét. (Ajánlott: `lva` ).
    1. Tartsa meg a megosztás típusát SMB-ként.
    1. Győződjön **meg arról, hogy a megosztás használata Edge-számítással** jelölőnégyzet be van jelölve.
    1. Győződjön meg arról **, hogy az Edge helyi megosztás jelölőnégyzet be** van jelölve.
    1. A felhasználó adatai területen adjon hozzáférést a megosztáshoz a legutóbb létrehozott felhasználó számára.
    1. Kattintson a **Létrehozás** gombra.
        
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/local-share.png" alt-text="Helyi megosztás":::  

    > [!TIP]
    > Ha a Windows-ügyfelet csatlakoztatja a Azure Stack Edge-hez, a [jelen dokumentumban](../../databox-online/azure-stack-edge-deploy-add-shares.md#connect-to-an-smb-share)ismertetett lépéseket követve CSATLAKOZHAT az SMB-megosztásokhoz.    

1. Hozzon létre egy távoli megosztást a fájl-szinkronizálási tárolóhoz.

    1. Először hozzon létre egy blob Storage-fiókot ugyanabban a régióban a **Cloud Storage Gateway – >Storage-fiókok** elemre kattintva.
    1. Kattintson a **Cloud Storage Gateway – >megosztások** lehetőségre.
    1. Kattintson a **+ megosztások hozzáadása** elemre.
    1. Adja meg a megosztás nevét. (Ajánlott: adathordozó).
    1. Tartsa meg a megosztás típusát SMB-ként.
    1. Győződjön **meg arról, hogy a megosztás használata Edge-számítással** jelölőnégyzet be van jelölve.
    1. Győződjön meg arról, hogy az **Edge helyi megosztás beállítás** nincs bejelölve.
    1. Válassza ki a nemrég létrehozott Storage-fiókot.
    1. Adja meg a tároló nevét.
    1. Állítsa be a tároló típusát a blob blokkolására.
    1. A felhasználó adatai területen adjon hozzáférést a megosztáshoz a legutóbb létrehozott felhasználó számára.
    1. Kattintson a **Létrehozás** gombra.    
    
    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/remote-share.png" alt-text="Távoli megosztás":::
    
    
1. Frissítse az élő videó Analytics Edge-modul tároló-létrehozási beállításait (lásd a [modulok hozzáadása című dokumentum](deploy-iot-edge-device.md#add-modules)4. pontját) a mennyiségi csatlakoztatások használatához.

   ```json
      "createOptions": 
         {
             "HostConfig": 
             {
                 "Binds": 
                 [
                     "/var/lib/azuremediaservices:/var/lib/azuremediaservices"
                 ],
                 "Mounts": 
                 [
                     {
                         "Target": "/var/media",
                         "Source": "media",
                         "Type": "volume"
                     }
                 ]
             }
         }
    ```

### <a name="verify-that-the-module-is-running"></a>Annak ellenőrzése, hogy a modul fut-e

Az utolsó lépés az, hogy ellenőrizze, hogy a modul csatlakoztatva van-e, és hogy a várt módon fut-e. A modul futtatási ideje állapotának futnia kell a IoT Edge eszközön a IoT Hub erőforrásban.

Annak ellenőrzéséhez, hogy a modul fut-e, tegye a következőket:

1. A Azure Portal térjen vissza a Azure Stack Edge-erőforráshoz
1. Válassza ki a modulok csempét. Ekkor a modulok panelre kerül. A modulok listájában azonosítsa a telepített modult. A hozzáadott modul futásidejű állapotának futnia kell.

    > [!div class="mx-imgBorder"]
    > :::image type="content" source="./media/deploy-azure-stack-edge-how-to/iot-edge-custom-module.png" alt-text="Egyéni modul":::

### <a name="configure-the-azure-iot-tools-extension"></a>Az Azure IoT-eszközök bővítményének konfigurálása

Kövesse ezeket az utasításokat az IoT hubhoz való kapcsolódáshoz az Azure IoT Tools bővítmény használatával.

1. A Visual Studio Code-ban válassza a megtekintés > Explorer lehetőséget. Vagy válassza a CTRL + SHIFT + E billentyűkombinációt.
1. Az Explorer lap bal alsó sarkában válassza az Azure IoT Hub elemet.
1. A helyi menü megjelenítéséhez kattintson a további beállítások ikonra. Ezután válassza a IoT Hub a kapcsolatok karakterláncának beállítása lehetőséget.
1. Amikor megjelenik egy beviteli mező, adja meg IoT Hub kapcsolódási karakterláncát. 

   * A kapcsolati karakterlánc beszerzéséhez lépjen a IoT Hub Azure Portal majd kattintson a megosztott hozzáférési házirendek elemre a bal oldali navigációs panelen.
   * Kattintson a iothubowner gombra a közös elérésű kulcsok lekéréséhez.
   * Másolja a kapcsolatok karakterláncát – elsődleges kulcsot, és illessze be a VSCode található beviteli mezőbe.

   A következőhöz hasonló lesz a kapcsolatok karakterlánca:<br/>`HostName=xxx.azure-devices.net;SharedAccessKeyName=iothubowner;SharedAccessKey=xxx`
    
   Ha a kapcsolatok sikeresek, megjelenik az Edge-eszközök listája. Ekkor meg kell jelennie a Azure Stack Edge-nek. Mostantól kezelheti IoT Edge eszközeit, és a helyi menüben használhatja az Azure IoT Hubt. A peremhálózati eszközön üzembe helyezett modulok megtekintéséhez a Azure Stack eszköz alatt bontsa ki a modulok csomópontot.
    
## <a name="troubleshooting"></a>Hibaelhárítás

* **Kubernetes API-hozzáférés (kubectl)**

    * Kövesse a dokumentációt, és konfigurálja a gépet [a Kubernetes-fürthöz való hozzáféréshez](../../databox-online/azure-stack-edge-gpu-create-kubernetes-cluster.md).
    * Az összes telepített IoT Edge modul a `iotedge` névteret használja. Ügyeljen arra, hogy a kubectl használatakor is szerepeljen.  

* **Modulok naplói**

    Az `iotedge` eszköz nem érhető el a naplók beszerzéséhez. A naplók vagy a cső fájlba való megtekintéséhez [kubectl-naplókat](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs)  kell használnia. Példa: <br/>  `kubectl logs deployments/mediaedge -n iotedge --all-containers`  

* **Pod-és csomópont-metrikák**

    A [kubectl Top](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#top)  használatával megtekintheti a pod és a Node metrikákat.
    <br/>`kubectl top pods -n iotedge` 

* **Modul hálózatkezelése**   
Azure Stack Edge-modul felderítéséhez szükség van arra, hogy a modul rendelkezzen a createOptions-ben található gazda porttal. Ekkor a modul megcímezhető lesz `moduleName:hostport` .
    
    ```json
    "createOptions": {
        "HostConfig": {
            "PortBindings": {
                "8554/tcp": [ { "HostPort": "8554" } ]
            }
        }
    }
    ```
    
* **Kötet csatlakoztatása**

    Egy modul nem fog elindulni, ha a tároló egy meglévő és nem üres könyvtárba próbál kötetet csatlakoztatni.

* **Megosztott memória a gRPC használatakor**

    Az Azure Stack Edge-erőforrások megosztott memóriája a gazdagép IPC használatával bármely névtérben a hüvelyek között támogatott.
    Az Edge-modul megosztott memóriájának konfigurálása IoT Hub-n keresztül történő üzembe helyezéshez.
    
    ```
    ...
    "createOptions": {
        "HostConfig": {
            "IpcMode": "host"
        }
    ...
        
    //(Advanced) Configuring shared memory on a K8s Pod or Deployment manifest for deployment via K8s API
    spec:
        ...
        template:
        spec:
            hostIPC: true
        ...
    ```
    
* **Speciális Pod közös elhelyezés**

    Ha a K8s használatával helyez üzembe olyan egyéni következtetési megoldásokat, amelyek a gRPC-n keresztül kommunikálnak az élő videó-elemzéssel, gondoskodnia kell arról, hogy a hüvelyek az élő videó elemzési moduljaival megegyező csomópontokon legyenek telepítve.

    * **1. lehetőség** – csomópont-affinitás és beépített csomópont-címkék használata a közös elhelyezéshez.

    Jelenleg az egyéni konfiguráció NodeSelector úgy tűnik, hogy a felhasználók nem rendelkeznek hozzáféréssel a csomópontokon a címkék beállításához. Az ügyfél topológiája és elnevezési konvenciói alapján azonban lehetséges, hogy a [beépített csomópont-címkéket](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#built-in-node-labels)is használhatják. A nodeAffinity szakasz az élő videó elemzésével Azure Stack Edge-erőforrásokra hivatkozik, így a következtetések megadhatók a közös elhelyezés érdekében.
    * **2. lehetőség** – a pod affinitás használata a közös elhelyezéshez (ajánlott).
A Kubernetes támogatja a [Pod affinitást](https://kubernetes.io/docs/concepts/scheduling-eviction/assign-pod-node/#inter-pod-affinity-and-anti-affinity)  , amely az ugyanazon a csomóponton lévő hüvelyeket is ütemezhet. Az élő video Analytics modulra hivatkozó podAffinity-szakasz a következtetést kihasználva a következő helyen érhető el:.

    ```json   
    // Example Live Video Analytics module deployment match labels
    selector:
      matchLabels:
        net.azure-devices.edge.deviceid: dev-ase-1-edge
        net.azure-devices.edge.module: mediaedge
    
    // Example Inference deployment manifest pod affinity
    spec:
      affinity:
        podAntiAffinity:
          requiredDuringSchedulingIgnoredDuringExecution:
          - labelSelector:
              matchExpressions:
              - key: net.azure-devices.edge.module
                operator: In
                values:
                - mediaedge
            topologyKey: "kubernetes.io/hostname"
    ```
* **404 hibakód a modul használatakor `rtspsim`**  
A tároló pontosan egy mappából olvassa be a videókat a tárolón belül. Ha egy külső mappát a tároló képén belül már létezőhöz rendel, akkor a Docker elrejti a tároló rendszerképében lévő fájlokat.  
 
    Ha például nincsenek kötések, a tároló rendelkezhet a következő fájlokkal:  
    ```
    root@rtspsim# ls /live/mediaServer/media  
    /live/mediaServer/media/camera-300s.mkv  
    /live/mediaServer/media/win10.mkv  
    ```
     
    A gazdagép a következő fájlokkal rendelkezhet:
    ```    
    C:\MyTestVideos> dir
    Test1.mkv
    Test2.mkv
    ```
     
    Ha azonban a telepítési jegyzékfájlban a következő kötést adja hozzá, a Docker felülírja a/live/mediaServer/media tartalmát, hogy az megfeleljen a gazdagépen lévőknek.
    `C:\MyTestVideos:/live/mediaServer/media`
    
    ```
    root@rtspsim# ls /live/mediaServer/media
    /live/mediaServer/media/Test1.mkv
    /live/mediaServer/media/Test2.mkv
    ```

## <a name="next-steps"></a>Következő lépések

A modul segítségével közvetlen metódusok meghívásával elemezheti az élő videó streameket. [Hívja meg a közvetlen metódusokat](get-started-detect-motion-emit-events-quickstart.md#use-direct-method-calls) a modulon.