---
title: GPU-kompatibilis tárolópéldány üzembe helyezése
description: Megtudhatja, hogyan helyezhet üzembe Azure Container Instances-példányokat nagy számítási igényű tárolóalkalmazások GPU-erőforrások használatával való futtatásához.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 6ffe4840d024c1e1f551966d05673c4ba83e1259
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764062"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>GPU-erőforrásokat tartalmazó tárolópéldányok üzembe helyezése

Bizonyos nagy számítási igényű számítási feladatok futtatásához a Azure Container Instances GPU-erőforrásokkal kell üzembe [helyeznie](container-instances-container-groups.md) a *tárolócsoportokat.* A csoportban található tárolópéldányok hozzáférhetnek egy vagy több NVIDIA Tesla GPU-hoz, miközben tárolók számítási feladatait futtatják(pl. CUDA és mélytanulásos alkalmazások).

Ez a cikk bemutatja, hogyan adhat hozzá GPU-erőforrásokat, amikor [YAML-fájl](container-instances-multi-container-yaml.md) vagy sablon használatával helyez [üzembe Resource Manager tárolócsoportot.](container-instances-multi-container-group.md) GPU-erőforrásokat is megadhat, amikor tárolópéldányt helyez üzembe a Azure Portal.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek.](#preview-limitations) Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Az előzetes verzió korlátozásai

Az előzetes verzióban az alábbi korlátozások érvényesek a GPU-erőforrások tárolócsoportokban való használata esetén. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

Idővel további régiók támogatása is elérhető lesz.

**Támogatott operációsrendszer-típusok:** csak Linux

**További korlátozások:** A GPU-erőforrások nem használhatók tárolócsoport virtuális hálózaton [való üzembe helyezésekor.](container-instances-vnet.md)

## <a name="about-gpu-resources"></a>A GPU-erőforrások

### <a name="count-and-sku"></a>Darabszám és termékváltozat

Ha GPU-kat használ egy tárolópéldányban, adjon meg egy *GPU-erőforrást* az alábbi információkkal:

* **Darabszám** – A GPU-k száma: **1,** **2** vagy **4**.
* **Termékváltozat** – A GPU termékváltozata: **K80,** **P100** vagy **V100**. Mindegyik termékváltozat az NVIDIA Tesla GPU-ra van leképezve az alábbi Azure GPU-kompatibilis virtuálisgép-családokkal:

  | Termékváltozat | Virtuálisgép-család |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | 100-as | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

GPU-erőforrások üzembe helyezésekor állítsa be a számítási feladatnak megfelelő CPU- és memória-erőforrásokat az előző táblázatban látható maximális értékekig. Ezek az értékek jelenleg nagyobbak, mint a GPU-erőforrások nélküli tárolócsoportokban elérhető CPU- és memória-erőforrások.  

> [!IMPORTANT]
> A [GPU-erőforrások alapértelmezett](container-instances-quotas.md) előfizetési korlátai (kvótái) termékváltozatonként eltérnek. A P100 és v100 SKU-k alapértelmezett processzorkorlátja kezdetben 0. Ha növelnie kell egy rendelkezésre álló régiót, küldjön egy Azure-támogatás [kérelmet.][azure-support]

### <a name="things-to-know"></a>Tudnivalók

* **Üzembe helyezési** idő – A GPU-erőforrásokat tartalmazó tárolócsoport létrehozása **akár 8–10 percet is igénybe vesz.** Ez a GPU-alapú virtuális gép Azure-ban való kiépítésének és konfigurálásának további ideje miatt van így. 

* **Díjszabás –** A GPU-erőforrások nélküli tárolócsoportokhoz hasonlóan az  Azure a GPU-erőforrásokat tartalmazó tárolócsoport időtartama alatt felhasznált erőforrásokért fizet. Az időtartamot a rendszer az első tároló rendszerképének lekért idejéből számítja ki, amíg a tárolócsoport le nem áll. Nem tartalmazza a tárolócsoport üzembe helyezésének idejét.

  Tekintse meg [a díjszabás részleteit.](https://azure.microsoft.com/pricing/details/container-instances/)

* **CUDA-illesztőprogramok** – A GPU-erőforrásokat tartalmazó tárolópéldányok előre ki vannak építve NVIDIA CUDA-illesztőprogramokkal és tároló-futtatásokkal, így használhatja a CUDA számítási feladatokhoz fejlesztett tároló-rendszerképeket.

  Ebben a szakaszban csak a CUDA 9.0-t támogatjuk. A Docker-fájlhoz például a következő alap rendszerképeket használhatja:
  * [nvidia/cuda:9.0-base-ubuntu16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-gpu-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML-példa

GPU-erőforrások hozzáadásának egyik módja egy tárolócsoport üzembe helyezése egy [YAML-fájllal.](container-instances-multi-container-yaml.md) Másolja a következő YAML-t egy *új, gpu-deploy-aci.yaml nevű fájlba,* majd mentse a fájlt. Ez a YAML létrehoz egy *gpucontainergroup* nevű tárolócsoportot, amely egy K80 GPU-val megadott tárolópéldányt határoz meg. A példány egy CUDA vektor összeadási mintaalkalmazást futtat. Az erőforrás-kérelmek elegendőek a számítási feladat futtatásához.

```YAML
additional_properties: {}
apiVersion: '2019-12-01'
name: gpucontainergroup
properties:
  containers:
  - name: gpucontainer
    properties:
      image: k8s-gcrio.azureedge.net/cuda-vector-add:v0.1
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
          gpu:
            count: 1
            sku: K80
  osType: Linux
  restartPolicy: OnFailure
```

Telepítse a tárolócsoportot [az az container create paranccsal,][az-container-create] és adja meg a YAML-fájl nevét a `--file` paraméterhez. Meg kell adnunk egy erőforráscsoport nevét és egy helyet a tárolócsoport számára, például *eastus,* amely támogatja a GPU-erőforrásokat.  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Az üzembe helyezés több percet is igénybe vehet. Ezután a tároló elindul, és futtat egy CUDA vektorkiadási műveletet. A napló [kimenetének megtekintéséhez][az-container-logs] futtassa az az container logs parancsot:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergroup --container-name gpucontainer
```

Kimenet:

```Console
[Vector addition of 50000 elements]
Copy input data from the host memory to the CUDA device
CUDA kernel launch with 196 blocks of 256 threads
Copy output data from the CUDA device to the host memory
Test PASSED
Done
```

## <a name="resource-manager-template-example"></a>Resource Manager példasablonra

A TÁROLÓcsoport GPU-erőforrásokkal való üzembe helyezésének egy másik módja egy Resource Manager [használata.](container-instances-multi-container-group.md) Először hozzon létre egy nevű fájlt, majd másolja bele a `gpudeploy.json` következő JSON-t. Ez a példa üzembe helyez egy tárolópéldányt egy 100-as v100 GPU-val, amely [egy TensorFlow](https://www.tensorflow.org/) betanítási feladatot futtat a MNIST-adathalmaz. Az erőforrás-kérelmek elegendőek a számítási feladat futtatásához.

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "containerGroupName": {
        "type": "string",
        "defaultValue": "gpucontainergrouprm",
        "metadata": {
          "description": "Container Group name."
        }
      }
    },
    "variables": {
      "containername": "gpucontainer",
      "containerimage": "mcr.microsoft.com/azuredocs/samples-tf-mnist-demo:gpu"
    },
    "resources": [
      {
        "name": "[parameters('containerGroupName')]",
        "type": "Microsoft.ContainerInstance/containerGroups",
        "apiVersion": "2019-12-01",
        "location": "[resourceGroup().location]",
        "properties": {
            "containers": [
            {
              "name": "[variables('containername')]",
              "properties": {
                "image": "[variables('containerimage')]",
                "resources": {
                  "requests": {
                    "cpu": 4.0,
                    "memoryInGb": 12.0,
                    "gpu": {
                        "count": 1,
                        "sku": "V100"
                  }
                }
              }
            }
          }
        ],
        "osType": "Linux",
        "restartPolicy": "OnFailure"
        }
      }
    ]
}
```

Telepítse a sablont az [az deployment group create paranccsal.][az-deployment-group-create] Meg kell adnunk egy olyan erőforráscsoport nevét, amely egy olyan régióban lett létrehozva, mint például az *eastus,* amely támogatja a GPU-erőforrásokat.

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

Az üzembe helyezés több percet is igénybe vehet. Ezután elindul a tároló, és futtatja a TensorFlow-feladatot. A napló [kimenetének megtekintéséhez][az-container-logs] futtassa az az container logs parancsot:

```azurecli
az container logs --resource-group myResourceGroup --name gpucontainergrouprm --container-name gpucontainer
```

Kimenet:

```Console
2018-10-25 18:31:10.155010: I tensorflow/core/platform/cpu_feature_guard.cc:137] Your CPU supports instructions that this TensorFlow binary was not compiled to use: SSE4.1 SSE4.2 AVX AVX2 FMA
2018-10-25 18:31:10.305937: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1030] Found device 0 with properties:
name: Tesla K80 major: 3 minor: 7 memoryClockRate(GHz): 0.8235
pciBusID: ccb6:00:00.0
totalMemory: 11.92GiB freeMemory: 11.85GiB
2018-10-25 18:31:10.305981: I tensorflow/core/common_runtime/gpu/gpu_device.cc:1120] Creating TensorFlow device (/device:GPU:0) -> (device: 0, name: Tesla K80, pci bus id: ccb6:00:00.0, compute capability: 3.7)
2018-10-25 18:31:14.941723: I tensorflow/stream_executor/dso_loader.cc:139] successfully opened CUDA library libcupti.so.8.0 locally
Successfully downloaded train-images-idx3-ubyte.gz 9912422 bytes.
Extracting /tmp/tensorflow/input_data/train-images-idx3-ubyte.gz
Successfully downloaded train-labels-idx1-ubyte.gz 28881 bytes.
Extracting /tmp/tensorflow/input_data/train-labels-idx1-ubyte.gz
Successfully downloaded t10k-images-idx3-ubyte.gz 1648877 bytes.
Extracting /tmp/tensorflow/input_data/t10k-images-idx3-ubyte.gz
Successfully downloaded t10k-labels-idx1-ubyte.gz 4542 bytes.
Extracting /tmp/tensorflow/input_data/t10k-labels-idx1-ubyte.gz
Accuracy at step 0: 0.097
Accuracy at step 10: 0.6993
Accuracy at step 20: 0.8208
Accuracy at step 30: 0.8594
...
Accuracy at step 990: 0.969
Adding run metadata for 999
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Mivel a GPU-erőforrások használata költséges lehet, győződjön meg arról, hogy a tárolók hosszú ideig nem futnak váratlanul. Figyelje a tárolókat a Azure Portal, vagy ellenőrizze egy tárolócsoport állapotát [az az container show paranccsal.][az-container-show] Például:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Ha végzett a létrehozott tárolópéldányokkal, törölje őket a következő parancsokkal:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Következő lépések

* További információ a tárolócsoport [YAML-fájllal](container-instances-multi-container-yaml.md) vagy [sablonnal Resource Manager üzembe helyezéséről.](container-instances-multi-container-group.md)
* További információ a [GPU-optimalizált virtuálisgép-méretekről az](../virtual-machines/sizes-gpu.md) Azure-ban.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az_container_create
[az-container-show]: /cli/azure/container#az_container_show
[az-container-logs]: /cli/azure/container#az_container_logs
[az-container-show]: /cli/azure/container#az_container_show
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
