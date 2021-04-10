---
title: IoT Edge-modulok módosítása a FPGA-eszközön Azure Stack Edge Pro GPU-eszközön való futtatáshoz
description: Leírja, hogy milyen módosítások szükségesek ahhoz, hogy a meglévő FPGA-eszközök meglévő IoT Edge moduljai a Azure Stack Edge Pro GPU-eszközön fussanak.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 02/22/2021
ms.author: alkohli
ms.openlocfilehash: 1276a242efb1917a0c4a24aa73c3e0d11f81e158
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "105559162"
---
# <a name="run-existing-iot-edge-modules-from-azure-stack-edge-pro-fpga-devices-on-azure-stack-edge-pro-gpu-device"></a>Meglévő IoT Edge modulok futtatása Azure Stack Edge Pro FPGA-eszközökről Azure Stack Edge Pro GPU-eszközön

[!INCLUDE [applies-to-GPU-and-pro-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-sku.md)]

Ez a cikk részletesen ismerteti az Azure Stack Edge Pro-FPGA futó Docker-alapú IoT Edge modul módosításait, így az Azure Stack Edge Pro GPU-eszközön futó, Kubernetes-alapú IoT Edge platformon is futtatható. 

## <a name="about-iot-edge-implementation"></a>Tudnivalók a IoT Edge megvalósításáról 

A IoT Edge implementáció eltérő a Azure Stack Edge Pro FPGA-eszközökön és a Azure Stack Edge Pro GPU-eszközökön. A GPU-eszközök esetében a Kubernetes a IoT Edge üzemeltetési platformként szolgál. A FPGA-eszközökön lévő IoT Edge Docker-alapú platformot használ. A IoT Edge Docker-alapú alkalmazás modelljét a rendszer automatikusan lefordítja a Kubernetes natív alkalmazás modelljére. Előfordulhat azonban, hogy néhány módosításra továbbra is szükség van, mivel a Kubernetes-alkalmazás modelljének csak egy kis részhalmaza támogatott.

Ha a számítási feladatokat egy FPGA-eszközről egy GPU-eszközre telepíti át, akkor módosítania kell a meglévő IoT Edge modulokat, hogy azok sikeresen fussanak a Kubernetes platformon. Előfordulhat, hogy eltérő módon kell megadnia a tárterületet, a hálózatkezelést, az erőforrás-használatot és a webproxyra vonatkozó követelményeket. 

## <a name="storage"></a>Tárolás

A IoT Edge-modulok tárolásának megadásakor vegye figyelembe a következő információkat.

- A Kubernetes lévő tárolók tárolója a Volume mounts használatával van megadva.
- A Kubernetes-ben való üzembe helyezés nem rendelkezhet kötésekkel az állandó tárolók és a gazdagépek elérési útjának társításához.
    - Állandó tároláshoz használjon `Mounts` típust `volume` .
    - Gazdagép elérési útjaihoz használja `Mounts` a típust `bind` .
- A Kubernetes IoT Edge esetében a `Mounts` csak a címtárhoz való kötésre, a fájlra nem.

#### <a name="example---storage-via-volume-mounts"></a>Példa – tárolás a Volume mounts használatával 

A Docker IoT Edge esetében gazdaelérési út kötések használatával képezhetők le az eszközön lévő megosztások a tárolón belüli útvonalakra. Itt láthatók a FPGA-eszközökön használt tároló-létrehozási beállítások:

```
{
  "HostConfig": 
  {
   "Binds": 
    [
     "<Host storage path for Edge local share>:<Module storage path>"
    ]
   }
}
```
<!-- is this how it will look on GPU device?-->
A Kubernetes IoT Edge gazdagépének elérési útjai `Mounts` `bind` itt láthatók:
```
{
    "HostConfig": {
        "Mounts": [
            {
                "Target": "<Module storage path>",
                "Source": "<Host storage path>",
                "Type": "bind"
            }
        ]
    }
}
```


<!--following example is for persistent storage where we use mounts w/ type volume-->

A Kubernetes IoT Edge-t futtató GPU-eszközök esetében a kötet-csatlakoztatások a tárolók megadására szolgálnak. A tárterület megosztások használatával történő kiépítéséhez az érték a `Mounts.Source` GPU-eszközön kiépített SMB-vagy NFS-megosztás neve lenne. Az az az `/home/input` elérési út, ahol a kötet elérhető a tárolón belül. Itt láthatók a GPU-eszközökön használt tároló-létrehozási beállítások:

```
{
    "HostConfig": {
        "Mounts": [
        {
            "Target": "/home/input",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        },
        {
            "Target": "/home/output",
            "Source": "<nfs-or-smb-share-name-here>",
            "Type": "volume"
        }]
    }
}
```



## <a name="network"></a>Network (Hálózat)

A IoT Edge-modulok hálózatkezelésének megadásakor vegye figyelembe a következő információkat. 

- `HostPort` a specifikációnak a fürtön belül és kívül is elérhetőnek kell lennie a szolgáltatásban.
    - K8sExperimental-beállítások a szolgáltatás csak fürtre való kitettségének korlátozására.
- A többmodulos kommunikációhoz meg kell adni `HostPort` a specifikációt és a kapcsolatot a leképezett port használatával (és nem a tárolóban elérhető port használatával).
- A gazdagép hálózatkezelése együttműködik a `dnsPolicy = ClusterFirstWithHostNet` szolgáltatással, és az összes tárolónak (különösen `edgeHub` ) nem kell a gazdagép hálózatán is lennie. <!--Need further clarifications on this one-->
- Port-hozzárendelések hozzáadása a TCP-hez, az UDP ugyanabban a kérésben nem működik.

#### <a name="example---external-access-to-modules"></a>Példa – a modulokhoz való külső hozzáférés 

A portok kötéseit megadó IoT Edge modulok esetében az IP-címet az eszköz helyi felhasználói felületén megadott Kubernetes külső szolgáltatás IP-tartománya alapján rendeli hozzá a rendszer. A tároló-létrehozási beállítások nem változnak a Docker vs IoT Edge IoT Edge a következő példában látható módon történő Kubernetes.  

```json 
{
    "HostConfig": {
        "PortBindings": {
            "5000/tcp": [
                {
                    "HostPort": "5000"
                }
            ]
        }
    }
}
```

A modulhoz rendelt IP-cím lekéréséhez azonban használhatja a Kubernetes irányítópultot az [IP-cím lekérése szolgáltatásokhoz vagy modulokhoz](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules)című témakörben leírtak szerint. 

Azt is megteheti, hogy az [eszköz PowerShell-felületéhez csatlakozik](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface) , és a `iotedge` lista paranccsal listázza az eszközön futó összes modult. A [parancs kimenete](azure-stack-edge-gpu-connect-powershell-interface.md#debug-kubernetes-issues-related-to-iot-edge) a modulhoz társított külső IP-címeket is megadja.


## <a name="resource-usage"></a>Erőforrás-használat 

A GPU-eszközök Kubernetes-alapú IoT Edge beállításakor a hardveres gyorsítás, a memória és a CPU-követelmények eltérően vannak megadva, mint a FPGA-eszközökön. 

#### <a name="compute-acceleration-usage"></a>Számítási gyorsítás használata

A modulok FPGA való üzembe helyezéséhez használja a tároló létrehozási beállításait. <!--with Device Bindings--> ahogy az a következő konfigurációban látható: <!--not sure where are device bindings in this config--> 

```json
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "microsoft.com/fpga_catapult": 2
        },
        "requests": {
        "microsoft.com/fpga_catapult": 2
        }
    }
    },
    "Env": [
    "WIRESERVER_ADDRESS=10.139.218.1"
    ]
}
``` 

    
<!--Note: The IP address assigned to your FPGA module's service can be used to send inferencing requests from outside the cluster OR your ML module can be used along with DBE Simple Module Flow by passing files to the module using an input share.-->
    
A GPU esetében az eszköz kötései helyett használjon erőforrás-kérelmekre vonatkozó specifikációkat, ahogy az a következő minimális konfigurációban látható. A katapult helyett NVIDIA-erőforrásokra van szüksége, és nem kell megadnia a értéket `wireserver` . 

```json     
{
    "HostConfig": {
    "Privileged": true,
    "PortBindings": {
        "50051/tcp": [
        {
            "HostPort": "50051"
        }
        ]
    }
    },
    "k8s-experimental": {
    "resources": {
        "limits": {
        "nvidia.com/gpu": 2
        }    
    }
}
```

#### <a name="memory-and-cpu-usage"></a>Memória-és CPU-használat
 
A memória-és CPU-használat beállításához használja a szakaszban található modulok processzorának korlátait `k8s-experimental` . <!--can we verify if this is how we set limits of memory and CPU-->

```json
    "k8s-experimental": {
    "resources": {
        "limits": {
            "memory": "128Mi",
            "cpu": "500m",
            "nvidia.com/gpu": 2
        },
        "requests": {
            "nvidia.com/gpu": 2
        }
}
```
A memória-és a CPU-specifikáció nem szükséges, de általában helyes gyakorlat. Ha `requests` nincs megadva, a rendszer a határértékekben beállított értékeket használja a minimálisan szükséges értékként. 

A modulok megosztott memóriájának használata más módszert is igényel. Használhatja például a gazdagép IPC üzemmódját a megosztott memória eléréséhez a Live Video Analytics és a következtetési megoldások között az [élő videó elemzése Azure stack Edge](../media-services/live-video-analytics-edge/deploy-azure-stack-edge-how-to.md#deploy-live-video-analytics-edge-module-using-azure-portal)-ben című témakörben leírtak szerint.


## <a name="web-proxy"></a>Webproxy 

A webproxy konfigurálásakor vegye figyelembe a következő információkat:

Ha a hálózaton konfigurálva van a webproxy, konfigurálja az alábbi környezeti változókat az üzemelő `edgeHub` példányhoz a Docker-alapú IoT Edge-telepítőn a FPGA-eszközökön:

- `https_proxy : <proxy URL>`
- `UpstreamProtocol : AmqpWs` (kivéve, ha a webproxy engedélyezi a `Amqp` forgalmat)

A GPU-eszközök Kubernetes-alapú IoT Edge beállításakor ezt a további változót az üzembe helyezés során kell konfigurálni:

- `no_proxy`: localhost

- A Kubernetes platformon IoT Edge proxy a 35000-es és a 35001-es portot használja. Győződjön meg arról, hogy a modul nem fut ezeken a portokon, vagy a port ütközéseket okozhat. 

## <a name="other-differences"></a>Egyéb különbségek

- **Központi telepítési stratégia**: Előfordulhat, hogy módosítania kell a modul frissítéseinek telepítési viselkedését. IoT Edge modulok alapértelmezett viselkedése a működés közbeni frissítés. Ez a viselkedés megakadályozza a frissített modul újraindítását, ha a modul olyan erőforrásokat használ, mint például a hardveres gyorsítás vagy a hálózati portok. Ez a viselkedés nem várt hatással lehet, kifejezetten a GPU-eszközök Kubernetes platformján lévő állandó kötetek kezelésekor. Az alapértelmezett viselkedés felülbírálásához megadhat egy `Recreate` `k8s-experimental` szakaszt a modul szakaszában.

    ```    
    {
      "k8s-experimental": {
        "strategy": {
          "type": "Recreate"
        }
      }
    }
    ```

- **Modulok neve**: a modul nevének követnie kell a Kubernetes elnevezési konvencióit. Előfordulhat, hogy át kell neveznie a IoT Edge-on futó modulokat a Docker-vel, ha a modulokat a Kubernetes-IoT Edgeba helyezi át. További információ a névadásról: [Kubernetes elnevezési konvenciók](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/).
- **Egyéb beállítások**: 
    - A FPGA-eszközökön futó egyes Docker-létrehozási beállítások nem fognak működni a GPU-eszközök Kubernetes-környezetében. Például:, like – BelépésiPont.<!--can we confirm what exactly is required here-->
    - A környezeti változókat, például a `:` szükséges értéket `__` .
    - Egy Kubernetes-Pod **tároló-létrehozási** állapota a IoT hub erőforrás egy moduljának **leállítási** állapotát eredményezi. Habár számos oka lehet annak, hogy a pod ebben az állapotban legyen, a gyakori ok az, amikor nagy méretű tárolót kell lehúzni egy alacsony hálózati sávszélességű kapcsolaton keresztül. Ha a pod ebben az állapotban van, a modul állapota **leállítási** néven jelenik meg az IOT hub-ban, bár a modul éppen elindul.


## <a name="next-steps"></a>Következő lépések

- További információ arról, hogyan [konfigurálhatja a GPU-t modul használatára](./azure-stack-edge-gpu-configure-gpu-modules.md).