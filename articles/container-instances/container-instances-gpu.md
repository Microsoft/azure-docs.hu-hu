---
title: GPU-t használó Container-példány üzembe helyezése
description: Ismerje meg, hogyan helyezhet üzembe az Azure Container instances szolgáltatásban nagy számítási igényű tároló-alkalmazásokat GPU-erőforrásokkal.
ms.topic: article
ms.date: 07/22/2020
ms.openlocfilehash: 0d645d1fce24d1324e485d74e20bcf492d4444a7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2021
ms.locfileid: "93127008"
---
# <a name="deploy-container-instances-that-use-gpu-resources"></a>GPU-erőforrásokat használó tároló-példányok üzembe helyezése

Ha Azure Container Instanceson bizonyos számítási igényű munkaterheléseket szeretne futtatni, telepítse a [tároló-csoportokat](container-instances-container-groups.md) a *GPU-erőforrásokkal*. A csoport Container instances egy vagy több NVIDIA Tesla GPU-t is elérhet a tároló munkaterhelések, például a CUDA és a Deep learning alkalmazások futtatása közben.

Ez a cikk bemutatja, hogyan adhat hozzá GPU-erőforrásokat egy [YAML-fájl](container-instances-multi-container-yaml.md) vagy egy [Resource Manager-sablon](container-instances-multi-container-group.md)használatával. A GPU-erőforrásokat is megadhatja, ha a Azure Portal használatával telepít egy tároló-példányt.

> [!IMPORTANT]
> Ez a funkció jelenleg előzetes verzióban érhető el, és bizonyos [korlátozások érvényesek](#preview-limitations). Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe][terms-of-use]. A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt.

## <a name="preview-limitations"></a>Előzetes verzió korlátozásai

Az előzetes verzióban a következő korlátozások érvényesek, ha GPU-erőforrásokat használ a tároló-csoportokban. 

[!INCLUDE [container-instances-gpu-regions](../../includes/container-instances-gpu-regions.md)]

A további régiók támogatása az idő múlásával történik.

**Támogatott operációsrendszer-típusok**: csak Linux

**További korlátozások**: a GPU-erőforrások nem használhatók tároló-csoportok [virtuális hálózatra](container-instances-vnet.md)történő telepítésekor.

## <a name="about-gpu-resources"></a>Tudnivalók a GPU-erőforrásokról

### <a name="count-and-sku"></a>Darabszám és SKU

Ha a GPU-t egy Container-példányban szeretné használni, egy *GPU-erőforrást* kell megadnia a következő információkkal:

* **Darabszám** – a GPU-k száma: **1**, **2** vagy **4**.
* **SKU** – a GPU SKU: **K80**, **P100** vagy **V100**. Mindegyik SKU az NVIDIA Tesla GPU-ra mutat az alábbi Azure GPU-kompatibilis virtuálisgép-családokban:

  | Termékváltozat | VM-család |
  | --- | --- |
  | K80 | [NC](../virtual-machines/nc-series.md) |
  | P100 | [NCv2](../virtual-machines/ncv2-series.md) |
  | V100 | [NCv3](../virtual-machines/ncv3-series.md) |

[!INCLUDE [container-instances-gpu-limits](../../includes/container-instances-gpu-limits.md)]

A GPU-erőforrások üzembe helyezése során állítsa be a számítási feladatokhoz megfelelő CPU-és memória-erőforrásokat az előző táblázatban látható maximális értékig. Ezek az értékek jelenleg nagyobbak, mint a CPU-és memória-erőforrások, amelyek GPU-erőforrások nélküli tároló-csoportokban érhetők el.  

> [!IMPORTANT]
> A GPU-erőforrások alapértelmezett [előfizetési korlátai](container-instances-quotas.md) (kvótái) az SKU-ban különböznek. A P100 és az V100 SKU-ra vonatkozó alapértelmezett CPU-korlátok kezdetben 0 értékre vannak állítva. Egy elérhető régió növelésének igényléséhez küldjön egy [Azure-támogatási kérést][azure-support].

### <a name="things-to-know"></a>Tudnivalók

* **Üzembe helyezési idő** – a GPU-erőforrásokat tartalmazó tároló csoport létrehozása akár **8-10 percet** is igénybe vehet. Ezt a GPU virtuális gépek Azure-ban való üzembe helyezésének és konfigurálásának további ideje okozza. 

* **Díjszabás** – a GPU-erőforrások nélküli tároló-csoportokhoz hasonlóan a GPU-erőforrásokkal rendelkező tárolók *időtartama* alatt felhasznált erőforrások Azure-számlái. Az időtartam kiszámításának ideje az első tároló rendszerképének lekérése, amíg a tároló csoport leáll. Nem tartalmazza a tároló csoport központi telepítésének idejét.

  Tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/container-instances/).

* A **CUDA-illesztőprogramok** – a GPU-erőforrásokkal rendelkező tároló-példányok az NVIDIA CUDA-illesztőprogramokkal és a tároló-futtatókörnyezetekkel vannak kiépítve, így a CUDA számítási feladatokhoz kifejlesztett tároló-lemezképeket is használhat.

  Ebben a szakaszban csak a CUDA 9,0-et támogatjuk. Használhatja például a következő alapképeket a Docker-fájlhoz:
  * [NVIDIA/CUDA: 9.0-Base-Ubuntu 16.04](https://hub.docker.com/r/nvidia/cuda/)
  * [tensorflow/tensorflow: 1.12.0-GPU-py3](https://hub.docker.com/r/tensorflow/tensorflow)
    
## <a name="yaml-example"></a>YAML példa

A GPU-erőforrások hozzáadásának egyik módja, ha egy [YAML-fájl](container-instances-multi-container-yaml.md)használatával helyez üzembe egy tároló csoportot. Másolja az alábbi YAML egy új, *GPU-Deploy-ACI. YAML* nevű fájlba, majd mentse a fájlt. Ez a YAML létrehoz egy *gpucontainergroup* nevű tároló csoportot, amely egy K80 GPU-val rendelkező Container-példányt ad meg. A példány egy példa CUDA vektoros hozzáadási alkalmazást futtat. Az erőforrás-kérelmek elegendőek a munkaterhelés futtatásához.

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

Telepítse a tároló csoportot az az [Container Create][az-container-create] paranccsal, és adja meg a paraméter YAML-fájljának nevét `--file` . Meg kell adnia egy erőforráscsoport nevét és egy olyan helyet a tároló csoport számára, amely támogatja a GPU-erőforrásokat támogató *eastus* .  

```azurecli
az container create --resource-group myResourceGroup --file gpu-deploy-aci.yaml --location eastus
```

Az üzembe helyezés több percet is igénybe vehet. Ezután a tároló elindítja és futtatja a CUDA vektorok hozzáadására szolgáló műveletet. Futtassa az az [Container logs][az-container-logs] parancsot a napló kimenetének megtekintéséhez:

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

## <a name="resource-manager-template-example"></a>Példa Resource Manager-sablonra

Egy másik lehetőség, hogy egy, a GPU-erőforrásokkal rendelkező tároló csoportot egy [Resource Manager-sablon](container-instances-multi-container-group.md)használatával telepítsen. Először hozzon létre egy nevű fájlt `gpudeploy.json` , majd másolja a következő JSON-t. Ez a példa egy V100 GPU-val rendelkező tároló-példányt helyez üzembe, amely [TensorFlow](https://www.tensorflow.org/) -betanítási feladatot futtat a MNIST-adatkészleten. Az erőforrás-kérelmek elegendőek a munkaterhelés futtatásához.

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

Telepítse a sablont az az [Deployment Group Create][az-deployment-group-create] paranccsal. Meg kell adnia egy olyan erőforráscsoport nevét, amely egy olyan régióban lett létrehozva, mint a GPU-erőforrásokat támogató *eastus* .

```azurecli-interactive
az deployment group create --resource-group myResourceGroup --template-file gpudeploy.json
```

Az üzembe helyezés több percet is igénybe vehet. Ezután elindul a tároló, és futtatja a TensorFlow feladatot. Futtassa az az [Container logs][az-container-logs] parancsot a napló kimenetének megtekintéséhez:

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

Mivel a GPU-erőforrások használata költséges lehet, gondoskodjon arról, hogy a tárolók ne fussanak váratlanul hosszú ideig. Figyelje a tárolókat a Azure Portalban, vagy ellenőrizze egy tároló csoport állapotát az az [Container show][az-container-show] paranccsal. Például:

```azurecli
az container show --resource-group myResourceGroup --name gpucontainergroup --output table
```

Ha elkészült a létrehozott tároló-példányokkal, törölje azokat a következő parancsokkal:

```azurecli
az container delete --resource-group myResourceGroup --name gpucontainergroup -y
az container delete --resource-group myResourceGroup --name gpucontainergrouprm -y
```

## <a name="next-steps"></a>Következő lépések

* További információ a Container Group [YAML-fájl](container-instances-multi-container-yaml.md) vagy [Resource Manager-sablon](container-instances-multi-container-group.md)használatával történő üzembe helyezéséről.
* További információ a GPU-ra optimalizált virtuálisgép- [méretekről](../virtual-machines/sizes-gpu.md) az Azure-ban.


<!-- IMAGES -->
[aci-vnet-01]: ./media/container-instances-vnet/aci-vnet-01.png

<!-- LINKS - External -->
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[azure-support]: https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
