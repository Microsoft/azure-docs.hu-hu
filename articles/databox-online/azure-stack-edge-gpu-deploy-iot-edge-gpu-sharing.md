---
title: IoT Edge munkaterhelés üzembe helyezése GPU-megosztással Azure Stack Edge Pro GPU-eszközön
description: Ismerteti, hogyan helyezhet üzembe GPU-val megosztott számítási feladatokat a Azure Stack Edge Pro GPU-eszközön IoT Edge használatával.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/12/2021
ms.author: alkohli
ms.openlocfilehash: b52d1e834772a2a6e0e000b3df15d8aa0fa866a9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "103565147"
---
# <a name="deploy-an-iot-edge-workload-using-gpu-sharing-on-your-azure-stack-edge-pro"></a>IoT Edge munkaterhelés üzembe helyezése GPU-megosztással a Azure Stack Edge Pro-ban

Ez a cikk azt ismerteti, hogyan oszthatják meg a GPU-ket a Azure Stack Edge Pro GPU-eszközön. Ez a módszer magában foglalja a többfolyamatos szolgáltatás (MPS) engedélyezését, majd a GPU munkaterhelések megadását egy IoT Edge központi telepítés segítségével. 

## <a name="prerequisites"></a>Előfeltételek

Mielőtt hozzákezd, győződjön meg az alábbiakról:

1. Olyan Azure Stack Edge Pro GPU-eszközhöz férhet hozzá, amely [aktiválva](azure-stack-edge-gpu-deploy-activate.md) van, és rendelkezik [számítási konfigurációval](azure-stack-edge-gpu-deploy-configure-compute.md). Rendelkezik a [KUBERNETES API-végponttal](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints) , és hozzáadta ezt a végpontot az `hosts` ügyfél azon fájljához, amely az eszközhöz fog hozzáférni.

1. Egy [támogatott operációs rendszerrel](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device)rendelkező ügyfélszoftverhez férhet hozzá. Windows-ügyfél használata esetén a rendszernek a PowerShell 5,0-es vagy újabb verzióját kell futtatnia az eszköz eléréséhez.

1. Mentse a következő központi telepítést a `json` helyi rendszerre. Ennek a fájlnak az adatait fogja használni az IoT Edge központi telepítésének futtatásához. Ez az üzembe helyezés az NVIDIA által nyilvánosan elérhető, [egyszerű CUDA-tárolón](https://docs.nvidia.com/cuda/wsl-user-guide/index.html#running-simple-containers) alapul. 

    ```json
    {
        "modulesContent": {
            "$edgeAgent": {
                "properties.desired": {
                    "modules": {
                        "cuda-sample1": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        },
                        "cuda-sample2": {
                            "settings": {
                                "image": "nvidia/samples:nbody",
                                "createOptions": "{\"Entrypoint\":[\"/bin/sh\"],\"Cmd\":[\"-c\",\"/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done\"],\"HostConfig\":{\"IpcMode\":\"host\",\"PidMode\":\"host\"}}"
                            },
                            "type": "docker",
                            "version": "1.0",
                            "env": {
                                "NVIDIA_VISIBLE_DEVICES": {
                                    "value": "0"
                                }
                            },
                            "status": "running",
                            "restartPolicy": "never"
                        }
                    },
                    "runtime": {
                        "settings": {
                            "minDockerVersion": "v1.25"
                        },
                        "type": "docker"
                    },
                    "schemaVersion": "1.1",
                    "systemModules": {
                        "edgeAgent": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
                                "createOptions": ""
                            },
                            "type": "docker"
                        },
                        "edgeHub": {
                            "settings": {
                                "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
                                "createOptions": "{\"HostConfig\":{\"PortBindings\":{\"443/tcp\":[{\"HostPort\":\"443\"}],\"5671/tcp\":[{\"HostPort\":\"5671\"}],\"8883/tcp\":[{\"HostPort\":\"8883\"}]}}}"
                            },
                            "type": "docker",
                            "status": "running",
                            "restartPolicy": "always"
                        }
                    }
                }
            },
            "$edgeHub": {
                "properties.desired": {
                    "routes": {
                        "route": "FROM /messages/* INTO $upstream"
                    },
                    "schemaVersion": "1.1",
                    "storeAndForwardConfiguration": {
                        "timeToLiveSecs": 7200
                    }
                }
            },
            "cuda-sample1": {
                "properties.desired": {}
            },
            "cuda-sample2": {
                "properties.desired": {}
            }
        }
    }
    ```

## <a name="verify-gpu-driver-cuda-version"></a>A GPU-illesztőprogram, a CUDA-verzió ellenőrzése

Első lépésként ellenőrizze, hogy az eszközön a szükséges GPU-illesztőprogram és a CUDA-verziók futnak-e.

1. [Kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Futtassa az alábbi parancsot:

    `Get-HcsGpuNvidiaSmi`

1. Az NVIDIA SMI-kimenetben jegyezze fel a GPU-verziót és a CUDA-verziót az eszközön. Ha Azure Stack Edge 2102 szoftvert futtat, akkor ez a verzió a következő illesztőprogram-verziókhoz fog megfelelni:

    - GPU-illesztőprogram verziója: 460.32.03
    - CUDA-verzió: 11,2
    
    Íme egy példa a kimenetre:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Tue Feb 23 10:34:01 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 0000041F:00:00.0 Off |                    0 |
    | N/A   40C    P8    15W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>  
    ```

1. Tartsa nyitva ezt a munkamenetet, mert az NVIDIA SMI kimenetét fogja megtekinteni a cikk során.


## <a name="deploy-without-context-sharing"></a>Környezet-megosztás nélküli üzembe helyezés

Mostantól üzembe helyezhet egy alkalmazást az eszközön, ha a többfolyamatos szolgáltatás nem fut, és nincs kontextus-megosztás. Az üzembe helyezés az eszközön található névtér Azure Portalján keresztül történik `iotedge` .

### <a name="create-user-in-iot-edge-namespace"></a>Felhasználó létrehozása IoT Edge névtérben

Először létre kell hoznia egy felhasználót, aki csatlakozni fog a `iotedge` névtérhez. A IoT Edge modulok a iotedge névtérben vannak telepítve. További információ: Kubernetes- [névterek az eszközön](azure-stack-edge-gpu-kubernetes-rbac.md#namespaces-types).

Az alábbi lépéseket követve hozzon létre egy felhasználót, és adjon hozzáférést a felhasználónak a `iotedge` névtérhez. 

1. [Kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).

1. Hozzon létre egy új felhasználót a `iotedge` névtérben. Futtassa az alábbi parancsot:

    `New-HcsKubernetesUser -UserName <user name>`

    Íme egy példa a kimenetre:

    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName iotedgeuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: 
    ===========================//snipped //======================// snipped //=============================
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: iotedgeuser
      name: iotedgeuser@kubernetes
    current-context: iotedgeuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: iotedgeuser
      user:
        client-certificate-data: 
    ===========================//snipped //======================// snipped //=============================
        client-key-data: 
    ===========================//snipped //======================// snipped ============================
    PQotLS0tLUVORCBSU0EgUFJJVkFURSBLRVktLS0tLQo=
    ```

1. Az egyszerű szövegben megjelenített kimenet másolása Mentse a kimenetet *konfigurációs* fájlként (kiterjesztés nélkül) a `.kube` helyi gépen lévő felhasználói profil mappájába, például: `C:\Users\<username>\.kube` . 

1. Adja meg a létrehozott felhasználót, és nyissa meg a `iotedge` névteret. Futtassa az alábbi parancsot:

    `Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName <user name>`    

    Íme egy példa a kimenetre:

    ```python
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace iotedge -UserName iotedgeuser
    [10.100.10.10]: PS>    
    ```
Részletes útmutatásért lásd: [Kubernetes-fürt csatlakoztatása és kezelése a Azure stack Edge Pro GPU-eszközön a kubectl használatával](azure-stack-edge-gpu-create-kubernetes-cluster.md#configure-cluster-access-via-kubernetes-rbac).

### <a name="deploy-modules-via-portal"></a>Modulok üzembe helyezése a portálon keresztül

IoT Edge modulok üzembe helyezése a Azure Portal használatával. Az n-Body szimulációt futtató, nyilvánosan elérhető NVIDIA CUDA-minta modulokat üzembe helyezheti. További információ: [N-Body szimuláció](https://physics.princeton.edu//~fpretori/Nbody/intro.htm).

1. Győződjön meg arról, hogy az IoT Edge szolgáltatás fut az eszközön.

    ![IoT Edge a szolgáltatás fut.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-1.png)

1. Válassza ki a IoT Edge csempét a jobb oldali ablaktáblán. Válassza a **IoT Edge > tulajdonságok** lehetőséget. A jobb oldali ablaktáblában válassza ki az eszközhöz hozzárendelt IoT Hub erőforrást.

    ![Tulajdonságok megtekintése](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-2.png)

1. Az IoT Hub erőforrásban válassza az **automatikus eszközkezelés > IoT Edge** lehetőséget. A jobb oldali ablaktáblában válassza ki az eszközhöz hozzárendelt IoT Edge eszközt.

    ![Lépjen IoT Edge.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-3.png)

1. Válassza a **Set modules** (Modulok beállítása) lehetőséget.

    ![Válassza a modulok beállítása lehetőséget.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-4.png)

1. Válassza a **+ > + IoT Edge modul hozzáadása** lehetőséget.

    ![IoT Edge modul hozzáadása.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-5.png)

1. A **modul beállításai** lapon adja meg a **IoT Edge modul nevét** és a **rendszerkép URI-ját**. Állítsa be a **rendszerkép lekérési házirendjét** **a létrehozás** elemre.

    ![A modul beállításai.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-6.png)
1. A **környezeti változók** lapon a **0** értéket kell megadnia **NVIDIA_VISIBLE_DEVICES** .

    ![Környezeti változók.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-7.png)

1. A **tároló létrehozása beállítások** lapon adja meg a következő beállításokat:

    ```json
    {
        "Entrypoint": [
            "/bin/sh"
        ],
        "Cmd": [
            "-c",
            "/tmp/nbody -benchmark -i=1000; while true; do echo no-op; sleep 10000;done"
        ],
        "HostConfig": {
            "IpcMode": "host",
            "PidMode": "host"
        }
    }    
    ```
    A beállítások az alábbiak szerint jelennek meg:

    ![Tároló-létrehozási beállítások.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-8.png)

    Válassza a **Hozzáadás** lehetőséget.

1. A hozzáadott modulnak **futnia** kell. 

    ![Tekintse át és hozza létre az üzembe helyezést.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-9.png)


1. Ismételje meg az összes lépést az első modul hozzáadásakor követett modul hozzáadásához. Ebben a példában a modul nevét adja meg a következőként: `cuda-sample2` . 

    ![A második modul moduljának beállításai.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-12.png)

    Ugyanazt a környezeti változót használja, mint a modulok ugyanazt a GPU-t fogják megosztani.

    ![A második modul környezeti változója.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-13.png)

    Használja ugyanazokat a tároló-létrehozási beállításokat, amelyeket az első modulhoz megadott, majd válassza a **Hozzáadás** lehetőséget.

    ![A 2. modulok tároló-létrehozási beállításai.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-14.png)

1. A **modulok beállítása** lapon válassza a **felülvizsgálat + létrehozás** , majd a **Létrehozás** lehetőséget. 

    ![Tekintse át és hozza létre a második üzembe helyezést.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-15.png)

1. A modulok **futtatókörnyezeti állapota** mostantól **futként** jelenik meg.  

    ![második üzembe helyezés állapota.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/gpu-sharing-deploy-16.png)


### <a name="monitor-workload-deployment"></a>Munkaterhelés központi telepítésének figyelése

1. Nyisson meg egy új PowerShell-munkamenetet.

1. A névtérben futó hüvelyek listázása `iotedge` . Futtassa az alábbi parancsot:

    `kubectl get pods -n iotedge`   

    Íme egy példa a kimenetre:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-ssng8   2/2     Running   0          5s
    cuda-sample2-6db6d98689-d74kb   2/2     Running   0          4s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          6d21h
    edgehub-d6c764847-l8v4m         2/2     Running   0          24h
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          6d21h
    PS C:\WINDOWS\system32>   
    ```
    Két hüvely létezik, `cuda-sample1-97c494d7f-lnmns` és `cuda-sample2-d9f6c4688-2rld9` az eszközön fut.

1. Noha mindkét tároló futtatja az n-Body szimulációt, tekintse meg a GPU-kihasználtságot az NVIDIA SMI kimenetből. Nyissa meg az eszköz PowerShell-felületét, és futtassa a parancsot `Get-HcsGpuNvidiaSmi` .

    Íme egy példa a kimenetre, ha a tárolók az n-Body szimulációt is futtatják:

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:31:16 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   52C    P0    69W /  70W |    221MiB / 15109MiB |    100%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    188342      C   /tmp/nbody                        109MiB |
    |    0   N/A  N/A    188413      C   /tmp/nbody                        109MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
    Amint láthatja, két tároló fut a GPU 0 n törzsű szimulációján. Megtekintheti a megfelelő memóriahasználat is.

1. A szimuláció befejezése után az NVIDIA SMI kimenete azt mutatja, hogy az eszközön nem futnak folyamatok.

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Fri Mar  5 13:54:48 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   34C    P8     9W /  70W |      0MiB / 15109MiB |      0%      Default |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |  No running processes found                                                 |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>
    ```
 
1. Az n-Body szimuláció befejezése után tekintse meg a naplókat, és Ismerje meg a telepítés részleteit, valamint a szimuláció befejezéséhez szükséges időt. 

    Íme egy példa az első tárolóból származó kimenetre:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample1-869989578c-ssng8 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170171.531 ms
    = 98.590 billion interactions per second
    = 1971.801 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```
    Íme egy példa a második tárolóból származó kimenetre:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-d74kb cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 170054.969 ms
    = 98.658 billion interactions per second
    = 1973.152 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>
    ```

1. Állítsa le a modul üzembe helyezését. Az eszköz IoT Hub erőforrásában:
    1. Ugrás az **automatikus eszköz központi telepítésére > IoT Edge**. Válassza ki az eszközéhez tartozó IoT Edge eszközt.

    1. Lépjen a **modulok beállítása** elemre, és válassza ki a modult. 

        ![Válassza a modul beállítása lehetőséget.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-1.png)

    1. A **modulok** lapon válasszon ki egy modult.
    
        ![Válasszon ki egy modult.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-2.png)

    1.  A **modul beállításai** lapon állítsa le a **kívánt állapotot** leállítva értékre. Válassza a **Frissítés** lehetőséget.

        ![A modul beállításainak módosítása](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-3.png)

    1. Az eszközön üzembe helyezett második modul leállításához ismételje meg a lépéseket. Válassza az **Áttekintés + létrehozás**, majd a **Létrehozás** lehetőséget. Ekkor frissítenie kell az üzemelő példányt.

        ![Tekintse át és hozza létre a frissített telepítést.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-6.png)
 
    1. Többször is frissítheti a **modulok beállítása** lapot. amíg a modul **futtatókörnyezetének állapota** **Leállítva** állapotra mutat.

        ![Ellenőrizze a telepítési állapotot.](media/azure-stack-edge-gpu-deploy-iot-edge-gpu-sharing/stop-module-deployment-8.png) 
    

## <a name="deploy-with-context-sharing"></a>Üzembe helyezés környezeti megosztással

Most már üzembe helyezheti az n-Body szimulációt két CUDA-tárolón, ha az MPS fut az eszközön. Először is engedélyeznie kell az MPS szolgáltatást az eszközön.


1. [Kapcsolódjon az eszköz PowerShell-felületéhez](azure-stack-edge-gpu-connect-powershell-interface.md).

1. Az MPS az eszközön való engedélyezéséhez futtassa a `Start-HcsGpuMPS` parancsot.

    ```powershell
    [10.100.10.10]: PS>Start-HcsGpuMPS
    K8S-1HXQG13CL-1HXQG13:
    Set compute mode to EXCLUSIVE_PROCESS for GPU 0000191E:00:00.0.
    All done.
    Created nvidia-mps.service
    [10.100.10.10]: PS>    
    ```
1. Szerezze be az NVIDIA SMI kimenetét az eszköz PowerShell-felületéről. Láthatja, hogy a `nvidia-cuda-mps-server` folyamat vagy az MPS szolgáltatás fut az eszközön.

    Íme egy példa a kimenetre:

    ```yml
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:37:39 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   36C    P8     9W /  70W |     28MiB / 15109MiB |      0%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```

1. Telepítse a korábban leállított modulokat. Állítsa a **kívánt állapotot** a **set modulok** használatával történő futtatásra.

    Példa a kimenetre:

    ```yml
    PS C:\WINDOWS\system32> kubectl get pods -n iotedge --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1
    NAME                            READY   STATUS    RESTARTS   AGE
    cuda-sample1-869989578c-2zxh6   2/2     Running   0          44s
    cuda-sample2-6db6d98689-fn7mx   2/2     Running   0          44s
    edgeagent-79f988968b-7p2tv      2/2     Running   0          5d20h
    edgehub-d6c764847-l8v4m         2/2     Running   0          27m
    iotedged-55fdb7b5c6-l9zn8       1/1     Running   1          5d20h
    PS C:\WINDOWS\system32>
    ```
    Láthatja, hogy a modulok üzembe helyezése és futtatása az eszközön megtörténik.

1. A modulok telepítésekor az n-Body szimulálás is elindul mindkét tárolón. A következő példában látható a kimenet, amikor a szimuláció befejeződött az első tárolón:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge logs cuda-sample1-869989578c-2zxh6 cuda-sample1
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155256.062 ms
    = 108.062 billion interactions per second
    = 2161.232 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32> 
    ```
    A következő példában látható a kimenet, ha a szimuláció befejeződött a második tárolón:

    ```powershell
    PS C:\WINDOWS\system32> kubectl -n iotedge  --kubeconfig C:\GPU-sharing\kubeconfigs\configiotuser1 logs cuda-sample2-6db6d98689-fn7mx cuda-sample2
    Run "nbody -benchmark [-numbodies=<numBodies>]" to measure performance.
    ==============// snipped //===================//  snipped  //=============
    
    > Windowed mode
    > Simulation data stored in video memory
    > Single precision floating point simulation
    > 1 Devices used for simulation
    GPU Device 0: "Turing" with compute capability 7.5
    
    > Compute 7.5 CUDA device: [Tesla T4]
    40960 bodies, total time for 10000 iterations: 155366.359 ms
    = 107.985 billion interactions per second
    = 2159.697 single-precision GFLOP/s at 20 flops per interaction
    no-op
    PS C:\WINDOWS\system32>    
    ```      

1. Szerezze be az NVIDIA SMI kimenetét az eszköz PowerShell-felületéről, ha a tárolók az n-Body szimulációt is futtatják. Íme egy példa a kimenetre. Három folyamat áll rendelkezésre, a `nvidia-cuda-mps-server` folyamat (C típus) megfelel az MPS szolgáltatásnak, és a `/tmp/nbody` folyamatok (az M + C típus) megfelelnek a modulok által üzembe helyezett n-Body munkaterhelésnek. 

    ```powershell
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    K8S-1HXQG13CL-1HXQG13:
    
    Thu Mar  4 12:59:44 2021
    +-----------------------------------------------------------------------------+
    | NVIDIA-SMI 460.32.03    Driver Version: 460.32.03    CUDA Version: 11.2     |
    |-------------------------------+----------------------+----------------------+
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |
    |                               |                      |               MIG M. |
    |===============================+======================+======================|
    |   0  Tesla T4            On   | 00002C74:00:00.0 Off |                    0 |
    | N/A   54C    P0    69W /  70W |    242MiB / 15109MiB |    100%   E. Process |
    |                               |                      |                  N/A |
    +-------------------------------+----------------------+----------------------+
    
    +-----------------------------------------------------------------------------+
    | Processes:                                                                  |
    |  GPU   GI   CI        PID   Type   Process name                  GPU Memory |
    |        ID   ID                                                   Usage      |
    |=============================================================================|
    |    0   N/A  N/A     56832    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A     56900    M+C   /tmp/nbody                        107MiB |
    |    0   N/A  N/A    122792      C   nvidia-cuda-mps-server             25MiB |
    +-----------------------------------------------------------------------------+
    [10.100.10.10]: PS>Get-HcsGpuNvidiaSmi
    ```
    
## <a name="next-steps"></a>Következő lépések

- [Helyezzen üzembe egy megosztott GPU-Kubernetes számítási feladatot a Azure stack Edge Pro](azure-stack-edge-gpu-deploy-kubernetes-gpu-sharing.md)-ban.
