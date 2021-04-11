---
title: Térbeli elemzési tárolók telemetria és naplózása
titleSuffix: Azure Cognitive Services
description: A térbeli elemzés az egyes tárolókat közös konfigurációs keretrendszerbeli elemzési, naplózási és biztonsági beállításokkal biztosítja.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: a825b9e0abc4e33eb0f9033f46bb77c38559f740
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104722701"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetria és hibaelhárítás

A térbeli elemzés olyan funkciókat tartalmaz, amelyekkel monitorozható a rendszer állapota, és amelyek segítenek a hibák diagnosztizálásában.

## <a name="enable-visualizations"></a>Vizualizációk engedélyezése

Ha engedélyezni szeretné az AI-elemzések eseményeinek vizualizációját egy videó keretén belül, egy asztali gépen kell használnia `.debug` egy [térbeli elemzési művelet](spatial-analysis-operations.md) verzióját. A vizualizáció Azure Stack peremhálózati eszközökön nem lehetséges. Négy hibakeresési művelet érhető el.

Ha az eszköz nem Azure Stack peremhálózati eszköz, szerkessze az [asztali gépek](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) telepítési jegyzékfájlját, hogy a megfelelő értéket használja a `DISPLAY` környezeti változóhoz. Meg kell egyeznie a `$DISPLAY` gazdaszámítógépen lévő változóval. Az üzembe helyezési jegyzék frissítése után telepítse újra a tárolót.

Előfordulhat, hogy az üzembe helyezés befejezése után át kell másolnia a `.Xauthority` fájlt a gazdagépről a tárolóba, majd újra kell indítania. Az alábbi példában a a `peopleanalytics` gazdaszámítógépen lévő tároló neve.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Rendszerállapot-telemetria gyűjtése

A My Graf egy nyílt forráskódú rendszerkép, amely térbeli analízissel működik, és a Microsoft Container Registryban érhető el. A következő bemeneteket veszi át, és elküldi őket a Azure Monitornak. A Graf modul a kívánt egyéni bemenetekkel és kimenetekkel is felépíthető. A a térbeli elemzésben a (fent hivatkozott) üzembe helyezési jegyzékfájl része. Ez a modul nem kötelező, és eltávolítható a jegyzékfájlból, ha nincs rá szükség. 

Bemenetek 
1. Térbeli elemzési mérőszámok
2. Lemezmetrikák
3. CPU-metrikák
4. Docker-metrikák
5. GPU-metrikák

Kimenetek
1. Azure Monitor

A megadott térbeli analízis-kezelő modul a térbeli elemzési tároló által kibocsátott összes telemetria-adatmennyiséget Azure Monitor teszi közzé. Az előfizetéshez Azure Monitor hozzáadásával kapcsolatos információkért tekintse meg a [Azure monitor](../../azure-monitor/overview.md) .

A Azure Monitor beállítása után létre kell hoznia a hitelesítő adatokat, amelyek lehetővé teszik a modul számára a telemetria küldését. A Azure Portal használatával létrehozhat egy új szolgáltatásnevet, vagy az alábbi Azure CLI-paranccsal hozhat létre egyet.

> [!NOTE] 
> Ehhez a parancshoz tulajdonosi jogosultságok szükségesek az előfizetéshez. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/..."
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

A [Azure stack Edge-eszköz](https://go.microsoft.com/fwlink/?linkid=2142179), [asztali számítógép](https://go.microsoft.com/fwlink/?linkid=2152270)vagy Azure-beli virtuális gép üzembe helyezési jegyzékfájljában a [GPU-val](https://go.microsoft.com/fwlink/?linkid=2152189)keresse *meg a következő* értékeket, és cserélje le az alábbi értékeket az előző lépésben megadott egyszerű szolgáltatásnév és az újbóli üzembe helyezés elemre.

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

Miután telepítette a Service Graf modult, a jelentett metrikák a Azure Monitor szolgáltatáson keresztül érhetők el, vagy a Azure Portal IoT Hub a **figyelés** lehetőség kiválasztásával.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Azure Monitor telemetria jelentés":::

### <a name="system-health-events"></a>Rendszerállapot-események

| Esemény neve | Leírás|
|------|---------|
|archon_exit     |Akkor lett elindítva *, amikor a felhasználó a térbeli* elemzési modul állapotát *Leállítva* állapotra módosítja.  |
|archon_error     |A tárolóban lévő folyamatok összeomlásakor lett elküldve. Ez egy kritikus hiba.  |
|InputRate     |Az a sebesség, amellyel a gráf feldolgozza a videó bemenetét. 5 percenként jelentett jelentést. | 
|OutputRate     |Az a sebesség, amellyel a gráf kiadja az AI-bepillantást. 5 percenként jelentett jelentést. |
|archon_allGraphsStarted | Elküldése, ha az összes gráf befejeződik. |
|archon_configchange     | Egy gráf konfigurációjának módosításakor lett elküldve. |
|archon_graphCreationFailed     |A rendszer akkor továbbítja, ha a jelentett gráf `graphId` nem indul el. |
|archon_graphCreationSuccess     |Akkor lett elindítva, amikor a jelentett gráf `graphId` sikeresen elindul. |
|archon_graphCleanup     | Akkor lett elküldve, amikor a jelentésben a jelentés `graphId` megtisztítása megtörtént, és kilép. |
|archon_graphHeartbeat     |A szívverés minden percben elküldésre kerül a szaktudás minden gráfa esetében. |
|archon_apiKeyAuthFail |Akkor küldi el a rendszer, ha a Computer Vision erőforrás-kulcs 24 óránál hosszabb ideig nem tudja hitelesíteni a tárolót, a következő okok miatt: nem kvóta, érvénytelen, offline. |
|VideoIngesterHeartbeat     |Minden órában elküldjük, jelezve, hogy a videó stream a videó forrása, és az adott órában előforduló hibák száma. Minden gráf esetében jelenteni kell. |
|VideoIngesterState | A jelentések *leálltak* vagy *megkezdődött* a video streaming szolgáltatásban. Minden gráf esetében jelenteni kell. |

##  <a name="troubleshooting-an-iot-edge-device"></a>IoT Edge eszköz hibaelhárítása

A `iotedge` futó modulok állapotát és naplóit a parancssori eszközzel is megtekintheti. Például:
* `iotedge list`: A futó modulok listáját jelenti. 
  A következővel további hibákat is megtudhat `iotedge logs edgeAgent` . Ha `iotedge` elakad, próbálkozzon újra a használatával `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` adott modul újraindítása 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Naplófájlok gyűjtése a diagnosztikai tárolóval

A térbeli elemzés olyan Docker-hibakeresési naplókat hoz létre, amelyeket a futásidejű problémák diagnosztizálásához, vagy a támogatási jegyek belefoglalásához használhat. A térbeli elemzési diagnosztikai modul elérhető a Microsoft Container Registryban a letöltéshez. Az [Azure stack Edge-eszköz](https://go.microsoft.com/fwlink/?linkid=2142179), [asztali számítógép](https://go.microsoft.com/fwlink/?linkid=2152270)vagy [Azure virtuális gép](https://go.microsoft.com/fwlink/?linkid=2152189) JEGYZÉKFÁJL-telepítési fájljában, GPU-val keresse meg a *diagnosztikai* modult.

Az "env" szakaszban adja hozzá a következő konfigurációt:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Ha egy távoli végpontra (például Azure Blob Storage) feltöltött naplókat szeretne optimalizálni, javasoljuk, hogy kis méretű fájlméretet tartson fenn. Tekintse meg az alábbi példát az ajánlott Docker-naplók konfigurálásához.

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

A naplózási szintű konfiguráció lehetővé teszi a generált naplók részletességének szabályozását. A támogatott naplózási szintek a következők:,,, `none` `verbose` `info` `warning` és `error` . A csomópontok és a platform alapértelmezett naplójának részletes szintje a következő: `info` . 

A naplózási szintek globálisan módosíthatók úgy, `ARCHON_LOG_LEVEL` hogy a környezeti változót az egyik megengedett értékre állítja.
Azt is megteheti, hogy az IoT Edge modul Twin dokumentumán keresztül globálisan, az összes üzembe helyezett szaktudáshoz, vagy minden egyes képességhez az alábbi értékek megadásával állítja be az értékeket `platformLogLevel` `nodeLogLevel` .

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
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Naplók összegyűjtése

> [!NOTE]
> A `diagnostics` modul nem befolyásolja a naplózási tartalmat, csak a meglévő naplók összegyűjtését, szűrését és feltöltését segíti.
> A modul használatához a Docker API 1,40-es vagy újabb verziójára van szükség.

A [Azure stack Edge-eszköz](https://go.microsoft.com/fwlink/?linkid=2142179), [asztali számítógép](https://go.microsoft.com/fwlink/?linkid=2152270)vagy Azure-beli virtuális gép a [GPU-val való](https://go.microsoft.com/fwlink/?linkid=2152189) telepítési jegyzékfájlja tartalmaz egy nevű modult, `diagnostics` amely összegyűjti és feltölti a naplókat. Ez a modul alapértelmezés szerint le van tiltva, és a naplókhoz való hozzáféréshez a IoT Edge modul konfigurációján keresztül kell engedélyezni. 

A `diagnostics` gyűjtemény igény szerint felügyelhető, és egy IoT Edge Direct metódussal vezérelhető, és naplókat küldhet egy Azure-Blob Storageba.

### <a name="configure-diagnostics-upload-targets"></a>Diagnosztika-feltöltési célok konfigurálása

A IoT Edge portálon válassza ki az eszközt, majd a **diagnosztikai** modult. A [Azure stack Edge-eszközhöz](https://go.microsoft.com/fwlink/?linkid=2142179), [asztali gépekhez](https://go.microsoft.com/fwlink/?linkid=2152270)vagy Azure-beli [virtuális géphez a GPU-val](https://go.microsoft.com/fwlink/?linkid=2152189) a **környezeti változók** szakaszban, a `env` következő információk megadásával:

**Az Azure-ba való feltöltés konfigurálása Blob Storage**

1. Ha még nem tette meg, hozza létre saját Azure Blob Storage-fiókját.
2. Szerezze be a Storage-fiókhoz tartozó **kapcsolatok karakterláncát** a Azure Portal. A **hozzáférési kulcsokban** fog megjelenni.
3. A rendszer automatikusan feltölti a térbeli elemzési naplókat egy *rtcvlogs* nevű blob Storage tárolóba a következő fájlnév-formátummal: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Térbeli elemzési naplók feltöltése

A naplók feltöltése igény szerint történik a `getRTCVLogs` IoT Edge metódussal, a `diagnostics` modulban. 


1. Lépjen a IoT Hub-portál lapra, válassza az **Edge-eszközök** lehetőséget, majd válassza ki az eszközt és a diagnosztikai modult. 
2. Lépjen a modul részletek lapjára, és kattintson a ***közvetlen metódus*** fülre.
3. Írja be `getRTCVLogs` a metódus nevét, és egy JSON formátumú karakterláncot a hasznos adatok között. Megadhatja `{}` , amely üres adattartalom. 
4. Állítsa be a kapcsolat és a metódus időtúllépését, és kattintson a **metódus meghívása** elemre.
5. Válassza ki a tárolót, és hozzon létre egy hasznos adattartalom JSON-karakterláncot a **naplózási szintaxis** szakaszban leírt paraméterek használatával. A kérelem végrehajtásához kattintson a **metódus meghívása** elemre.

>[!NOTE]
> Ha a `getRTCVLogs` metódust üres adattartalommal hívja meg, az eszközön telepített összes tároló listáját fogja visszaadni. A metódus neve megkülönbözteti a kis-és nagybetűket. Ha a metódus neve nem megfelelő, akkor 501-as hibaüzenetet kap.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="A getRTCVLogs metódus meghívása ":::
![getRTCVLogs Direct metódus lapja](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Naplózási szintaxis

Az alábbi táblázat felsorolja a naplók lekérdezéséhez használható paramétereket.

| Kulcsszó | Leírás | Alapértelmezett érték |
|--|--|--|
| StartTime | A kívánt naplók kezdő időpontja ezredmásodpercben (UTC). | `-1`, a tároló futtatókörnyezetének kezdete. Amikor `[-1.-1]` időtartományként használja az API-t, az elmúlt egy órában visszaadja a naplókat.|
| EndTime | A kívánt naplók befejezési időpontja ezredmásodpercben (UTC). | `-1`, az aktuális idő. `[-1.-1]`Az időtartomány használatakor az API az elmúlt egy óra naplóit adja vissza. |
| ContainerId | A naplók beolvasására szolgáló cél tároló.| `null`, ha nincs tároló-azonosító. Az API az összes rendelkezésre álló tároló adatait adja vissza azonosítókkal.|
| DoPost | Hajtsa végre a feltöltési műveletet. Ha ez a értékre van állítva `false` , végrehajtja a kért műveletet, és visszaadja a feltöltési méretet a feltöltés végrehajtása nélkül. Ha a értékre `true` van állítva, a rendszer elindítja a kiválasztott naplók aszinkron feltöltését. | `false`, ne töltse fel.|
| Szabályozás | Adja meg, hogy hány sornyi feltöltési naplót kell feltölteni a kötegekben | `1000`, Ezzel a paraméterrel állíthatja be a post sebességet. |
| Szűrők | A feltöltendő naplók szűrése | `null`a szűrőket kulcs érték párokként lehet megadni a térbeli elemzési naplók szerkezete alapján: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Például: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

A következő táblázat a lekérdezési válasz attribútumait sorolja fel.

| Kulcsszó | Leírás|
|--|--|
|DoPost| *Igaz* vagy *hamis*. Azt jelzi, hogy a naplók feltöltése megtörtént-e. Ha úgy dönt, hogy nem tölt fel naplókat, az API a ***szinkron** _ adatokat adja vissza. Amikor úgy dönt, hogy feltölti a naplókat, az API a 200 értéket adja vissza, ha a kérelem érvényes, és elkezdi a naplók feltöltését. *_aszinkron módon_* *.|
|TimeFilter| A naplókra alkalmazott Időszűrő.|
|ValueFilters| A naplókra alkalmazott kulcsszavak szűrői. |
|Időbélyeg| Metódus végrehajtásának kezdési ideje. |
|ContainerId| Cél tároló azonosítója |
|FetchCounter| A naplósorok száma összesen |
|FetchSizeInByte| A naplózási adat teljes mennyisége bájtban megadva. |
|MatchCounter| A naplózási sorok érvényes száma. |
|MatchSizeInByte| A naplófájlok érvényes mennyisége bájtban megadva. |
|FilterCount| A naplózási sorok teljes száma a szűrő alkalmazása után. |
|FilterSizeInByte| A naplózási adat teljes mennyisége bájtban a szűrő alkalmazása után. |
|FetchLogsDurationInMiliSec| A művelet időtartamának beolvasása. |
|PaseLogsDurationInMiliSec| Szűrési művelet időtartama |
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

A beolvasási napló sorainak, idejének és méretének beolvasása, ha ezek a beállítások kitűnnek a ***DoPost*** értékre, `true` és a rendszer leküldi a naplókat ugyanazzal a szűrőkkel a célhelyekre. 

Hibaelhárítási problémák esetén a naplókat az Azure Blob Storage is exportálhatja. 

## <a name="common-issues"></a>Gyakori problémák

Ha a modul naplóiban a következő üzenet jelenik meg, az Azure-előfizetést jóvá kell hagynia: 

"A tároló érvénytelen állapotban van. Az előfizetés ellenőrzése nem sikerült, állapot: "eltérés". Az API-kulcs nem a megadott típusú tárolóhoz készült. "

További információ: [a kérelem jóváhagyása a tároló futtatásához](spatial-analysis-container.md#request-approval-to-run-the-container).

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Az Azure Stack Edge-eszköz hibaelhárítása

A következő szakasz segítséget nyújt az Azure Stack Edge-eszköz állapotának hibakereséséhez és ellenőrzéséhez.

### <a name="access-the-kubernetes-api-endpoint"></a>Hozzáférés a Kubernetes API-végponthoz. 

1. Az eszköz helyi felhasználói felületén nyissa meg az **eszközök** lapot. 
2. Másolja a Kubernetes API szolgáltatás végpontját az **eszközök végpontjai** területen. Ez a végpont egy karakterlánc a következő formátumban: `https://compute..[device-IP-address]` .
3. Mentse a végponti karakterláncot. Ezt később fogja használni `kubectl` , amikor a Kubernetes-fürthöz való hozzáférést konfigurálja.

### <a name="connect-to-powershell-interface"></a>Kapcsolódás a PowerShell-felülethez

Távolról, kapcsolódjon egy Windows-ügyfélről. A Kubernetes-fürt létrehozása után a fürtön keresztül felügyelheti az alkalmazásokat. Csatlakoznia kell az eszköz PowerShell-felületéhez. Az ügyfél operációs rendszertől függően előfordulhat, hogy az eszközhöz való távoli kapcsolódás eljárásai eltérőek lehetnek. A következő lépések a PowerShellt futtató Windows-ügyfelekre vonatkoznak.

> [!TIP]
> * Mielőtt elkezdené, győződjön meg arról, hogy a Windows-ügyfél a Windows PowerShell 5,0-es vagy újabb verzióját futtatja.
> * [A PowerShell Linuxon is elérhető](/powershell/scripting/install/installing-powershell-core-on-linux).

1. Futtasson egy Windows PowerShell-munkamenetet rendszergazdaként. 
    1. Győződjön meg arról, hogy a Windows távfelügyeleti szolgáltatása fut az ügyfélen. A parancssorba írja be a következőt: `winrm quickconfig` .

2. Rendeljen hozzá egy változót az eszköz IP-címéhez. Például: `$ip = "<device-ip-address>"`.

3. A következő parancs használatával adja hozzá az eszköz IP-címét az ügyfél megbízható gazdagépek listájához. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Indítsa el a Windows PowerShell-munkamenetet az eszközön. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Ha a rendszer kéri, adja meg a jelszót. Használja ugyanazt a jelszót, amelyet a helyi webes felületre való bejelentkezéshez használ. A helyi webes felület alapértelmezett jelszava `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>A Kubernetes-fürt elérése

A Kubernetes-fürt létrehozása után a `kubectl` parancssori eszköz használatával férhet hozzá a fürthöz.

1. Hozzon létre egy új névteret. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Hozzon létre egy felhasználót, és szerezzen be egy konfigurációs fájlt. Ez a parancs a Kubernetes-fürthöz tartozó konfigurációs adatokat fogja kiadni. Másolja ezt az információt, és mentse egy *config* nevű fájlba. Ne mentse a fájlt fájlkiterjesztésként.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Adja hozzá a *konfigurációs* fájlt a helyi számítógép felhasználói profiljának *. Kube* mappájához.    

4. Társítsa a névteret a létrehozott felhasználóval.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Telepítse a `kubectl` Windows-ügyfelet a következő parancs használatával:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Adjon hozzá egy DNS-bejegyzést a gazdagépek fájljához a rendszeren. 
    1. Futtassa rendszergazdaként a jegyzettömböt, és nyissa meg a következő helyen található *hosts* fájlt: `C:\windows\system32\drivers\etc\hosts` . 
    2. Hozzon létre egy bejegyzést a Hosts fájlban a helyi felhasználói felület **eszköz** lapjáról kapott eszköz IP-címmel és DNS-tartománnyal. A használni kívánt végpont a következőhöz hasonlóan fog kinézni: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Ellenőrizze, hogy tud-e csatlakozni a Kubernetes hüvelyéhez.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

A tároló-naplók beszerzéséhez futtassa a következő parancsot:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Hasznos parancsok

|Parancs  |Leírás  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Létrehoz egy Kubernetes-konfigurációs fájlt. A parancs használatakor másolja az adatokat egy *config* nevű fájlba. Ne mentse a fájlt kiterjesztéssel.        |
| `Get-HcsApplianceInfo` | Az eszközre vonatkozó adatokat ad vissza. |
| `Enable-HcsSupportAccess` | Hozzáférési hitelesítő adatokat hoz létre a támogatási munkamenet elindításához. |


## <a name="how-to-file-a-support-ticket-for-spatial-analysis"></a>Támogatási jegy benyújtása a térbeli elemzéshez 

Ha további támogatásra van szüksége a térbeli elemzési tárolóval kapcsolatos problémák megoldásához, kövesse az alábbi lépéseket a támogatási jegy kitöltéséhez és elküldéséhez. A csapatunk további útmutatást nyújt Önnek. 

### <a name="fill-out-the-basics"></a>Az alapok kitöltése 
Hozzon létre egy új támogatási jegyet az [új támogatási kérelem](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest) oldalon. A következő paraméterek kitöltéséhez kövesse az utasításokat:

![Támogatás alapjai](./media/support-ticket-page-1-final.png)

1. Állítsa be a **probléma típusát a következőre** : `Technical` .
2. Válassza ki azt az előfizetést, amelyet a térbeli elemzési tároló üzembe helyezéséhez használ.
3. Válassza ki `My services` és válassza ki a `Cognitive Services` szolgáltatást.
4. Válassza ki azt az erőforrást, amelyet a térbeli elemzési tároló üzembe helyezéséhez használ.
5. Írjon be egy rövid leírást, amely részletezi a problémával kapcsolatos problémát. 
6. Válassza `Spatial Analysis` a probléma típusa lehetőséget.
7. Válassza ki a megfelelő altípust a legördülő listából.
8. Válassza a **Next (tovább** ) lehetőséget: a következő lapra való áttéréshez szükséges megoldások.

### <a name="recommended-solutions"></a>Javasolt megoldások
A következő szakasz a kiválasztott probléma típusának ajánlott megoldásait fogja ajánlani. Ezek a megoldások a leggyakoribb problémák megoldására szolgálnak, de ha nem hasznosak a megoldásához, kattintson a **Tovább gombra: részletek** a következő lépéshez való ugráshoz.

### <a name="details"></a>Részletek
Ezen az oldalon további részleteket is megtudhat a megjelenő problémáról. Ügyeljen arra, hogy a lehető legrészletesebben vegye fel a problémát, mivel ez segít a mérnököknek a probléma jobb szűkítéséhez. Adja meg az előnyben részesített kapcsolattartási módszert, valamint a probléma súlyosságát, hogy kapcsolatba lépjen Önnel, és válassza a Next (tovább) gombot **: felülvizsgálat + létrehozás** lehetőségre a következő lépéshez való áttéréshez. 

### <a name="review-and-create"></a>Áttekintés és létrehozás 
Tekintse át a támogatási kérelmének részleteit, és győződjön meg róla, hogy minden rendben van, és hogy a probléma hatékonyan jelent meg. Ha elkészült, válassza a **Létrehozás** lehetőséget, hogy elküldje a jegyet a csapatnak! A jegy kézhezvétele után e-mailben értesítést kap, és a csapatunk a lehető leghamarabb vissza fog térni Önnek. A jegy állapotát a Azure Portal tekintheti meg.

## <a name="next-steps"></a>Következő lépések

* [Felhasználók üzembe helyezése webes alkalmazásokban](spatial-analysis-web-app.md)
* [Térbeli elemzési műveletek konfigurálása](./spatial-analysis-operations.md)
* [Kamera elhelyezése – útmutató](spatial-analysis-camera-placement.md)
* [A zóna és a vonal elhelyezési útmutatója](spatial-analysis-zone-line-placement.md)
