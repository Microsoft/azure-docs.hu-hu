---
title: Telemetria és naplózás térbeli elemzési tárolókhoz
titleSuffix: Azure Cognitive Services
description: A térbeli elemzés minden tárolóhoz közös konfigurációs keretrendszer-elemzéseket, naplózást és biztonsági beállításokat biztosít.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: 901e857a346b0955726c5755e23595efefbc2ca1
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589499"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetria és hibaelhárítás

A térbeli elemzés olyan funkciókat tartalmaz, amelyek figyelik a rendszer állapotát, és segítenek a problémák diagnosztizálásában.

## <a name="enable-visualizations"></a>Vizualizációk engedélyezése

Ha engedélyezni szeretné a AI-elemzések-események vizualizációját egy videókeretben, a Spatial Analysis művelet verzióját kell használnia `.debug` egy asztali gépen. [](spatial-analysis-operations.md) A vizualizáció nem lehetséges a Azure Stack Edge eszközökön. Négy hibakeresési művelet érhető el.

Ha az eszköz nem Azure Stack Edge eszköz, szerkessze [](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) az asztali gépek üzembe helyezési jegyzékfájlját úgy, hogy a megfelelő értéket használja a `DISPLAY` környezeti változóhoz. Meg kell `$DISPLAY` egyeznie a gazdagépen található változóval. Az üzembe helyezési jegyzék frissítése után újra üzembe kell helyeznie a tárolót.

Az üzembe helyezés befejezése után előfordulhat, hogy a fájlt a gazdagépről a tárolóba kell másolnia, majd újra kell `.Xauthority` indítania. Az alábbi mintában `peopleanalytics` a a gazdagépen található tároló neve.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Rendszer-állapot telemetria gyűjtése

A Telegraf egy nyílt forráskódú kép, amely együttműködik a térbeli elemzéssel, és elérhető a Microsoft Container Registry. A következő bemeneteket küldi a Azure Monitor. A telegraf modul a kívánt egyéni bemenetekkel és kimenetekkel építhető ki. A Spatial Analysis telegraf modulkonfigurációja az üzembehelyezés jegyzékfájljának része (a fenti hivatkozáson látható). Ez a modul nem kötelező, és eltávolítható a jegyzékből, ha nincs rá szüksége. 

Bemenetek: 
1. Térbeli elemzési metrikák
2. Lemezmetrikák
3. CPU-metrikák
4. Docker-metrikák
5. GPU-metrikák

Kimenetek:
1. Azure Monitor

A megadott Spatial Analysis telegraf modul közzéteszi a Spatial Analysis-tároló által kibocsátott összes telemetriai adatot a Azure Monitor. A [Azure Monitor](../../azure-monitor/overview.md) előfizetéshez való hozzáadásról Azure Monitor információkért tekintse meg a következőt: .

A Azure Monitor létre kell hoznia hitelesítő adatokat, amelyek lehetővé teszik, hogy a modul telemetriát küldjön. Az alábbi Azure Portal új szolgáltatásnév létrehozásához, vagy az alábbi Azure CLI-paranccsal hozhat létre egyet.

> [!NOTE] 
> Ehhez a parancshoz tulajdonosi jogosultság szükséges az előfizetésben. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

Az Azure Stack Edge-eszköz , asztali gép , vagy [GPU-val](https://go.microsoft.com/fwlink/?linkid=2152189)üzemelő [Azure-beli](https://go.microsoft.com/fwlink/?linkid=2142179)virtuális gép üzembe helyezési jegyzékében keresse meg a [](https://go.microsoft.com/fwlink/?linkid=2152270) *telegraf* modult, és cserélje le a következő értékeket az előző lépésben lekért egyszerű szolgáltatás adataira, és újra üzembe helyezést.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

A telegraf modul üzembe helyezése után a jelentett metrikák a Azure Monitor szolgáltatáson  keresztül, vagy a IoT Hub-ban a Monitorozás Azure Portal.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor telemetriai jelentés készítése":::

### <a name="system-health-events"></a>Rendszer állapotesemények

| Esemény neve                  | Description    |
|-----------------------------|-------------------------------------------------------------------------------------------|
| archon_exit                 | Akkor lesz elküldve, amikor egy felhasználó a Spatial Analysis modul állapotát *futásról* leállított *állapotra módosítja.*  |
| archon_error                | Akkor lesz elküldve, amikor a tárolón belüli folyamatok bármelyike összeomlik. Ez egy kritikus hiba.      |
| InputRate (Bemeneti érték)                   | Az a sebesség, amellyel a gráf feldolgozza a videóbemenetet. 5 percenként jelentve.              |
| OutputRate (Kimeneti teljesítmény)                  | A gráf AI-elemzések kimenetének sebessége. 5 percenként jelentve.                |
| archon_allGraphsStarted     | Akkor lesz elküldve, ha az összes diagram indítása befejeződött.                                           |
| archon_configchange         | Akkor lesz elküldve, ha egy gráfkonfiguráció módosult.                                              |
| archon_graphCreationFailed  | Akkor lesz elküldve, ha a jelentett gráf `graphId` nem indul el.                           |
| archon_graphCreationSuccess | Akkor lesz elküldve, amikor a jelentett `graphId` gráf sikeresen elindul.                      |
| archon_graphCleanup         | Akkor lesz elküldve, amikor a jelentett gráf `graphId` megtisztítja és kilép.                      |
| archon_graphHeartbeat       | Egy képesség minden grafikonján percenként küldött szívverés.                                   |
| archon_apiKeyAuthFail       | Akkor van elküldve, Computer Vision erőforráskulcs több mint 24 órán keresztül nem tudja hitelesíteni a tárolót a következő okok miatt: Nincs kvóta, Érvénytelen, Offline. |
| VideoIngesterHeartbeat      | Óránként elküldve jelzi, hogy a videó streamelése a Videó forrásból történt az adott órában előforduló hibák számával együtt. Az egyes gráfok jelentése. |
| VideoIngesterState          | A *jelentések leálltak* vagy *elindítva* a videóstreameléshez. Az egyes gráfok jelentése.              |

##  <a name="troubleshooting-an-iot-edge-device"></a>Eszköz IoT Edge hibaelhárítása

A parancssori eszközzel ellenőrizheti a futó modulok `iotedge` állapotát és naplóit. Például:
* `iotedge list`: A futó modulok listáját jelenti. 
  A hibákat a további ellenőrzéshez `iotedge logs edgeAgent` használhatja. Ha `iotedge` elakad, megpróbálhatja újraindítani a `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` egy adott modul újraindításához 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Naplófájlok gyűjtése a diagnosztikai tárolóval

A térbeli elemzés olyan Docker-hibakeresési naplókat hoz létre, amelyek segítségével diagnosztizálhatja a futásidejű problémákat, vagy támogatási jegyeket is tartalmazhat. A Térbeli elemzés diagnosztikai modulja a Microsoft Container Registry letölthető. Az eszköz, asztali [](https://go.microsoft.com/fwlink/?linkid=2142179)gép vagy GPU-val Azure Stack Edge [Azure-beli](https://go.microsoft.com/fwlink/?linkid=2152189) virtuális gép jegyzékfájljában [keresse](https://go.microsoft.com/fwlink/?linkid=2152270)meg a *diagnosztikai modult.*

Az "env" szakaszban adja hozzá a következő konfigurációt:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

A távoli végpontra (például az Azure Blob Storage feltöltött naplók optimalizálása érdekében javasoljuk egy kis fájlméret fenntartását. A Docker-naplók ajánlott konfigurációját az alábbi példában láthatja.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>A naplózási szint konfigurálása

A naplószintű konfigurációval szabályozhatja a létrehozott naplók részletességét. A támogatott naplószintek a következőek: `none` , , , és `verbose` `info` `warning` `error` . A csomópontok és a platform alapértelmezett részletes naplózási szintje `info` a . 

A naplószintek globálisan módosíthatók, ha a környezeti változót az engedélyezett értékek `ARCHON_LOG_LEVEL` egyikére módosítja.
Az ikermodul IoT Edge dokumentumban is beállítható globálisan, az összes üzembe helyezett készséghez, vagy minden adott képességhez a és a értékeinek beállításával, az `platformLogLevel` `nodesLogLevel` alább látható módon.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodesLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Naplók gyűjtése

> [!NOTE]
> A modul nincs hatással a naplózás tartalmára, csak a meglévő naplók gyűjtésében, szűrésében és `diagnostics` feltöltésében segít.
> A modul csak akkor használható, ha a Docker API 1.40-es vagy újabb verzióját használja.

Az eszköz, asztali gép [vagy](https://go.microsoft.com/fwlink/?linkid=2142179)GPU-val Azure Stack Edge [Azure-beli](https://go.microsoft.com/fwlink/?linkid=2152189) virtuális gép üzembe helyezési jegyzékfájlmintája tartalmaz egy nevű modult, amely naplókat gyűjt és tölt [](https://go.microsoft.com/fwlink/?linkid=2152270) `diagnostics` fel. Ez a modul alapértelmezés szerint le van tiltva, és a IoT Edge modul konfigurációjában kell engedélyezni, amikor hozzá kell férni a naplókhoz. 

A gyűjtemény igény szerint, egy közvetlen metódussal IoT Edge vezérelhető, és képes naplókat küldeni egy `diagnostics` Azure Blob Storage.

### <a name="configure-diagnostics-upload-targets"></a>Diagnosztikai feltöltési célok konfigurálása

A IoT Edge válassza ki az eszközt, majd a **diagnosztikai modult.** Az Azure Stack Edge-eszköz , asztali gépek vagy [](https://go.microsoft.com/fwlink/?linkid=2152270)GPU-val üzemelő  [Azure-beli](https://go.microsoft.com/fwlink/?linkid=2142179)virtuális gépek üzembe helyezési jegyzékfájljában keresse meg a diagnosztika környezeti változók szakaszát , és adja hozzá a következő [](https://go.microsoft.com/fwlink/?linkid=2152189) `env` adatokat:

**Feltöltés konfigurálása Azure Blob Storage**

1. Ha még nem Azure Blob Storage, hozzon létre egy saját fiókfiókot.
2. Szerezze be **a tárfiók** kapcsolati sztringet a Azure Portal. Ez az Access Keys (Hozzáférési **kulcsok) alatt található.**
3. A térbeli elemzési naplók automatikusan fel lesznek töltve egy *rtcvlogs* nevű Blob Storage tárolóba a következő fájlnévformátummal: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Térbeli elemzési naplók feltöltése

A naplók igény szerint, a `getRTCVLogs` IoT Edge metódussal, a modulban vannak `diagnostics` feltöltve. 


1. A portálon IoT Hub válassza az **Edge-eszközök** lehetőséget, majd válassza ki az eszközt és a diagnosztikai modult. 
2. A modul részleteket tartalmazó oldalára kattintva kattintson a ***közvetlen metódus fülre.***
3. Írja `getRTCVLogs` be a metódus nevét, és egy json formátumú sztringet a hasznos adatokban. Megadhatja a `{}` et, amely egy üres hasznos tartalom. 
4. Állítsa be a kapcsolat és a metódus időtúllépését, majd kattintson a **Metódus meghívása elemre.**
5. Válassza ki a céltárolót, és készítsen egy hasznos JSON-sztringet a Naplózási szintaxis **szakaszban leírt paraméterekkel.** Kattintson **a Metódus meghívása** elemre a kérés végrehajtásához.

>[!NOTE]
> Ha a metódust üres hasznos adatokat használ, az eszközre telepített összes tároló `getRTCVLogs` listáját adja vissza. A metódus neve megkülönbözteti a kis- és nagybetűket. 501-es hibaüzenetet kap, ha helytelen metódusnevet ad meg.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="A getRTCVLogs metódus invoking (A getRTCVLogs metódusának invoking the getRTCVLogs) metódusa ":::
![getRTCVLogs Direct metódus lap](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Naplózási szintaxis

Az alábbi táblázat a naplók lekérdezéséhez használható paramétereket sorolja fel.

| Kulcsszó | Description | Alapértelmezett érték |
|--|--|--|
| StartTime | A kívánt naplók kezdési ideje, ezredmásodpercben (UTC). | `-1`– a tároló futásidejű futásának kezdete. Időtartományként használva az API az elmúlt egy óra `[-1.-1]` naplóit adja vissza.|
| EndTime | A kívánt naplók záró ideje, ezredmásodpercben (UTC). | `-1`, az aktuális időpont. Időtartomány használata esetén az API az elmúlt egy óra `[-1.-1]` naplóit adja vissza. |
| ContainerId (Tárolóazonosító) | Céltároló a naplók beolvasására.| `null`, ha nincs tárolóazonosító. Az API az összes rendelkezésre álló tárolóinformációt adja vissza, és az adatokat az adott adatokat adja vissza.|
| DoPost | Hajtsa végre a feltöltési műveletet. Ha ez a beállítás értéke , akkor végrehajtja a kért műveletet, és a feltöltési méretet a feltöltés `false` végrehajtása nélkül adja vissza. Ha a beállítása , akkor a kiválasztott naplók `true` aszinkron feltöltését kezdeményezi | `false`, ne töltse fel.|
| Szabályozás | Jelzi, hogy kötegenként hány sornyi naplót kell feltölteni | `1000`, Ezzel a paraméterrel módosíthatja az utólagos sebességet. |
| Szűrők | A feltöltésre használható naplók szűrése | `null`, a szűrők kulcs-érték párokként is megadva a Spatial Analysis-naplók szerkezete alapján: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Például: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

Az alábbi táblázat a lekérdezési válaszban található attribútumokat sorolja fel.

| Kulcsszó | Description|
|--|--|
|DoPost| Igaz *vagy* *hamis.* Jelzi, hogy a naplók fel vannak-e töltve. Ha úgy dönt, hogy nem tölt fel naplókat, az API a * adatokat szinkron módon adja **vissza** _. Ha naplók feltöltését választja, az API 200-as értéket ad vissza, ha a kérelem érvényes, és megkezdi a naplók _*_aszinkron feltöltését_**.|
|TimeFilter (Időszűrő)| A naplókra alkalmazott időszűrő.|
|ValueFilters (Értékszűrők)| A naplókra alkalmazott kulcsszavak szűrői. |
|Időbélyeg| Metódus végrehajtásának kezdési ideje. |
|ContainerId (Tárolóazonosító)| Céltároló azonosítója. |
|FetchCounter| Naplósorok teljes száma. |
|FetchSizeInByte| Naplóadatok teljes mennyisége bájtban. |
|MatchCounter| A naplósorok érvényes száma. |
|MatchSizeInByte| A naplóadatok érvényes mennyisége bájtban. |
|FilterCount (Szűrőszám)| A szűrő alkalmazása után a naplósorok teljes száma. |
|FilterSizeInByte| A szűrő alkalmazása után a naplóadatok teljes mennyisége bájtban. |
|FetchLogsDurationInMiliSec| A beolvasási művelet időtartama. |
|PaseLogsDurationInMiliSec| Szűrőművelet időtartama. |
|PostLogsDurationInMiliSec| Művelet utáni időtartam. |

#### <a name="example-request"></a>Példakérelem 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Példaválasz 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Ellenőrizze a beolvasási napló sorait, időit és méretét, ha ezek a beállítások megfelelőnek tűnikek, cserélje le a ***DoPostot*** a fájlra, és ez lekérte a naplókat ugyanazokkal a szűrőkkel a `true` célhelyre. 

A hibák elhárítása során a naplókat a Azure Blob Storage exportálhatja. 

## <a name="common-issues"></a>Gyakori problémák

Ha a modulnaplókban a következő üzenet jelenik meg, az azt jelentheti, hogy az Azure-előfizetését jóvá kell hagyni: 

"A tároló nem érvényes állapotban van. Az előfizetés érvényesítése "Eltérés" állapottal meghiúsult. Az API-kulcs nem az adott tárolótípushoz való."

További információ: [Jóváhagyás kérése a tároló futtatásához.](spatial-analysis-container.md#request-approval-to-run-the-container)

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Az Azure Stack Edge hibaelhárítása

A következő szakasz segítséget nyújt a hibakereséshez és az eszköz állapotának Azure Stack Edge ellenőrzéséhez.

### <a name="access-the-kubernetes-api-endpoint"></a>Hozzáférés a Kubernetes API-végponthoz. 

1. Az eszköz helyi felhasználói felületén válassza az Eszközök **lapot.** 
2. Az **Eszközvégpont alatt másolja** ki a Kubernetes API-szolgáltatásvégpontot. Ez a végpont egy sztring, a következő formátumban: `https://compute..[device-IP-address]` .
3. Mentse a végponti sztringet. Ezt később, a Kubernetes-fürt elérésére való `kubectl` konfiguráláskor fogja használni.

### <a name="connect-to-powershell-interface"></a>Csatlakozás PowerShell-felülethez

Távolról csatlakozzon egy Windows-ügyfélről. A Kubernetes-fürt létrehozása után ezen a fürtön keresztül kezelheti az alkalmazásokat. Csatlakoznia kell az eszköz PowerShell-felületéhez. Az ügyfél operációs rendszerétől függően az eszközhöz való távoli csatlakozáshoz szükséges eljárások eltérőek lehetnek. A következő lépések PowerShellt futtató Windows-ügyfél esetén vannak meg.

> [!TIP]
> * Mielőtt hozzákezd, győződjön meg arról, hogy a Windows-ügyfél Windows PowerShell 5.0-s vagy újabb verzióval fut.
> * A PowerShell Linux [rendszeren is elérhető.](/powershell/scripting/install/installing-powershell-core-on-linux)

1. Futta Windows PowerShell munkamenetet rendszergazdaként. 
    1. Győződjön meg arról, hogy a Rendszerfelügyeleti szolgáltatás fut az ügyfélen. A parancssorba írja be a következőt: `winrm quickconfig` .

2. Rendeljen hozzá egy változót az eszköz IP-címéhez. Például: `$ip = "<device-ip-address>"`.

3. A következő paranccsal adja hozzá az eszköz IP-címét az ügyfél megbízható gazdagépeinek listájához. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Indítson Windows PowerShell munkamenetet az eszközön. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Amikor a rendszer kéri, adja meg a jelszót. Használja ugyanazt a jelszót, mint a helyi webes felületre való bejelentkezéshez. A helyi webes felület alapértelmezett `Password1` jelszava. 

### <a name="access-the-kubernetes-cluster"></a>A Kubernetes-fürt elérése

A Kubernetes-fürt létrehozása után a parancssori eszközzel hozzáférhet `kubectl` a fürthöz.

1. Hozzon létre egy új névteret. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Hozzon létre egy felhasználót, és szerezze be a konfigurációs fájlt. Ez a parancs a Kubernetes-fürt konfigurációs információit fogja kihozni. Másolja ki ezt az információt, és mentse egy config nevű *fájlba.* Ne mentse a fájlt fájlkiterjesztésként.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Adja hozzá *a konfigurációs fájlt* a helyi gép felhasználói *profiljának .kube* mappájába.    

4. Társítsa a névteret a létrehozott felhasználóhoz.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Telepítse `kubectl` a-t a Windows-ügyfélen a következő paranccsal:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Adjon hozzá egy DNS-bejegyzést a gazdagépfájlhoz a rendszeren. 
    1. Futtassa rendszergazdaként a Jegyzettömböt, és nyissa meg a *következő* helyen található gazdagépfájlt: `C:\windows\system32\drivers\etc\hosts` . 
    2. Hozzon létre egy bejegyzést a gazdagépfájlban a helyi  felhasználói felület Eszköz lapján kapott eszköz IP-címével és DNS-tartományával. A használnia kell végpont a következő módon fog kinézni: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Ellenőrizze, hogy tud-e csatlakozni a Kubernetes-podhoz.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

A tárolónaplók lekért futtatásához futtassa a következő parancsot:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Hasznos parancsok

|Parancs  |Leírás  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Létrehoz egy Kubernetes konfigurációs fájlt. A parancs használata esetén másolja az adatokat egy config nevű *fájlba.* Ne mentse fájlkiterjesztéssel a fájlt.        |
| `Get-HcsApplianceInfo` | Az eszközzel kapcsolatos adatokat ad vissza. |
| `Enable-HcsSupportAccess` | Hozzáférési hitelesítő adatokat hoz létre egy támogatási munkamenet elindítani. |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>Támogatási jegy beterjesztése térbeli elemzéshez 

Ha további támogatásra van szüksége egy olyan probléma megoldásához, amely a Térbeli elemzés tárolóval van probléma, kövesse az alábbi lépéseket egy támogatási jegy kitöltéséhez és elküldéhez. Csapatunk további útmutatással fog beszélni. 

### <a name="fill-out-the-basics"></a>Az alapok kitöltése 
Hozzon létre egy új támogatási jegyet az [Új támogatási kérelem oldalon.](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) Kövesse az utasításokat a következő paraméterek kitöltése érdekében:

![A támogatás alapjai](./media/support-ticket-page-1-final.png)

1. A **Probléma típusaként adja** meg a következőt: `Technical` .
2. Válassza ki azt az előfizetést, amely a Spatial Analysis-tároló üzembe helyezéséhez van használva.
3. Válassza `My services` ki és válassza ki `Cognitive Services` szolgáltatásként.
4. Válassza ki azt az erőforrást, amely a Spatial Analysis-tároló üzembe helyezéséhez van használva.
5. Írjon egy rövid leírást, amely részletezi a problémát. 
6. Válassza `Spatial Analysis` a lehetőséget a probléma típusaként.
7. Válassza ki a megfelelő altípust a legördülő menüből.
8. Válassza **a Tovább: Megoldások** lehetőséget a következő oldalra lépéshez.

### <a name="recommended-solutions"></a>Javasolt megoldások
A következő szakasz a kiválasztott problématípushoz kínál ajánlott megoldásokat. Ezek a megoldások megoldják a leggyakoribb problémákat, de ha ez nem hasznos a megoldásához, válassza a **Tovább:** Részletek lehetőséget a következő lépéshez.

### <a name="details"></a>Részletek
Ezen az oldalon további részleteket adhat meg az Ön által megoldott problémáról. Mindenképpen a lehető legtitkosabb részleteket foglalja bele, mivel ez segít a mérnökeinknek a probléma jobb leszűkítésében. Adja meg a kívánt kapcsolatfelvételi módszert és a probléma súlyosságát, hogy megfelelően felvejük Önnel a kapcsolatot, majd válassza a **Tovább:** Áttekintés + létrehozás lehetőséget a következő lépésre való továbbléphez. 

### <a name="review-and-create"></a>Áttekintés és létrehozás 
Tekintse át a támogatási kérés részleteit, és győződjön meg arról, hogy minden pontos, és hatékonyan képviseli a problémát. Ha elkészült, kattintson a **Létrehozás** gombra, hogy elküldje a jegyet a csapatunknak! A jegy fogadása után egy visszaigazoló e-mailt fog kapni, és csapatunk a lehető leghamarabb vissza fog küldeni Önnek. A jegy állapotát a következő Azure Portal.

## <a name="next-steps"></a>Következő lépések

* [Emberek megszámlálása webalkalmazás üzembe helyezése](spatial-analysis-web-app.md)
* [Térbeli elemzési műveletek konfigurálása](./spatial-analysis-operations.md)
* [Kamera elhelyezési útmutatója](spatial-analysis-camera-placement.md)
* [Útmutató a zónák és sorok elhelyezéséhez](spatial-analysis-zone-line-placement.md)
