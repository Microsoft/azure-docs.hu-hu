---
title: Virtuális gépek és méretezésikészlet-példányok üzembe helyezése dedikált gazdagépek számára a CLI használatával
description: Virtuális gépek és méretezésikészlet-példányok üzembe helyezése dedikált gazdagépek számára az Azure CLI használatával.
author: cynthn
ms.service: virtual-machines
ms.subservice: dedicated-hosts
ms.topic: how-to
ms.date: 11/12/2020
ms.author: cynthn
ms.openlocfilehash: adc09bf2572be563ff52cf9fa3d0dea51263d032
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774412"
---
# <a name="deploy-to-dedicated-hosts-using-the-azure-cli"></a>Üzembe helyezés dedikált gazdagépen az Azure CLI használatával
 

Ez a cikk bemutatja, [](../dedicated-hosts.md) hogyan hozhat létre egy dedikált Azure-gazdagépet a virtuális gépek (VM-ek) számára. 

Győződjön meg arról, hogy telepítette az Azure CLI 2.16.0-s vagy újabb verzióját, és bejelentkezett egy Azure-fiókba a `az login` használatával. 


## <a name="limitations"></a>Korlátozások

- A dedikált gazdagépekhez elérhető méretek és hardvertípusok régiónként változnak. További információért tekintse meg [a gazdagép díjszabását.](https://aka.ms/ADHPricing)

## <a name="create-resource-group"></a>Erőforráscsoport létrehozása 
Az Azure-erőforráscsoport olyan logikai tároló, amelybe a rendszer üzembe helyezi és kezeli az Azure-erőforrásokat. Hozza létre az erőforráscsoportot az az group create gombra. A következő példában létrehozunk egy *myDHResourceGroup* nevű erőforráscsoportot az *USA keleti régiója* helyen.

```azurecli-interactive
az group create --name myDHResourceGroup --location eastus 
```
 
## <a name="list-available-host-skus-in-a-region"></a>Egy régióban elérhető gazdagép-SKUS-k listái

Nem minden gazdagép termékkódja érhető el minden régióban és rendelkezésre állási zónában. 

A dedikált gazdagépek kiépítése előtt sorolja fel a gazdagépek rendelkezésre állását és az esetleges ajánlatkorlátozásokat. 

```azurecli-interactive
az vm list-skus -l eastus2  -r hostGroups/hosts  -o table  
```
 
## <a name="create-a-host-group"></a>Gazdagépcsoport létrehozása 

A **gazdagépcsoport** olyan erőforrás, amely dedikált gazdagépek gyűjteményét jelöli. Hozzon létre egy gazdagépcsoportot egy régióban és egy rendelkezésre állási zónában, majd adjon hozzá gazdagépeket. A magas rendelkezésre állás tervezésekor további lehetőségek is rendelkezésre állnak. A következő lehetőségek egyikét vagy mindkettőt használhatja a dedikált gazdagépekkel: 
- Több rendelkezésre állási zónára is kihathat. Ebben az esetben minden használni kívánt zónában gazdagépcsoportot kell használnia.
- Több tartalék tartományra is kihathat, amelyek fizikai állványra vannak leképezve. 
 
Mindkét esetben meg kell adnia a tartalék tartományok számát a gazdagépcsoporthoz. Ha nem szeretné átfogni a tartalék tartományokat a csoportban, használjon 1-es tartaléktartomány-számlálót. 

Dönthet úgy is, hogy a rendelkezésre állási zónákat és a tartalék tartományokat is használnia kell. 


Ebben a példában az [az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) használatával hozunk létre egy gazdagépcsoportot a rendelkezésre állási zónák és a tartalék tartományok használatával. 

```azurecli-interactive
az vm host group create \
   --name myHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 2 
``` 

Adja hozzá a paramétert, hogy a virtuális gépek és a méretezési csoport példányai automatikusan a gazdagépcsoport `--automatic-placement true` gazdagépeire kerülnek. További információ: Manuális [és automatikus elhelyezés. ](../dedicated-hosts.md#manual-vs-automatic-placement)


### <a name="other-examples"></a>További példák

Az az [vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) parancs használatával is létrehozhat egy gazdagépcsoportot az 1. rendelkezésre állási zónában (tartalék tartományok nélkül).

```azurecli-interactive
az vm host group create \
   --name myAZHostGroup \
   -g myDHResourceGroup \
   -z 1 \
   --platform-fault-domain-count 1 
```
 
A következőkben [az az vm host group create](/cli/azure/vm/host/group#az_vm_host_group_create) használatával hozunk létre egy gazdagépcsoportot csak tartalék tartományokkal (olyan régiókban használhatók, ahol a rendelkezésre állási zónák nem támogatottak). 

```azurecli-interactive
az vm host group create \
   --name myFDHostGroup \
   -g myDHResourceGroup \
   --platform-fault-domain-count 2 
```
 
## <a name="create-a-host"></a>Gazdagép létrehozása 

Most hozzunk létre egy dedikált gazdagépet a gazdagépcsoportban. A gazdagép neve mellett meg kell adnia a gazdagép termékváltozatát is. A gazdagép termékváltozata rögzíti a támogatott virtuálisgép-sorozatokat, valamint a dedikált gazdagép hardvergenerációját.  

A gazdagép-termékcsomagokkal és -díjszabással kapcsolatos további információkért lásd: [Azure Dedicated Host díjszabása.](https://aka.ms/ADHPricing)

Gazdagép [létrehozásához használja](/cli/azure/vm/host#az_vm_host_create) az az vm host create használhatja. Ha a gazdagépcsoport tartalék tartományszámát adja meg, a gazdagép tartalék tartományának megadására lesz szükség.  

```azurecli-interactive
az vm host create \
   --host-group myHostGroup \
   --name myHost \
   --sku DSv3-Type1 \
   --platform-fault-domain 1 \
   -g myDHResourceGroup
```


 
## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása 
Hozzon létre egy virtuális gépet egy dedikált gazdagépen [az az vm create használatával.](/cli/azure/vm#az_vm_create) Ha a gazdagépcsoport létrehozásakor rendelkezésre állási zónát adott meg, ugyanazt a zónát kell használnia a virtuális gép létrehozásakor.

```azurecli-interactive
az vm create \
   -n myVM \
   --image debian \
   --host-group myHostGroup \
   --generate-ssh-keys \
   --size Standard_D4s_v3 \
   -g myDHResourceGroup \
   --zone 1
```

Ha a virtuális gépet egy adott gazdagépen kell elhelyezésre használnia, a gazdagépcsoport megadása helyett használja a `--host` következőt: `--host-group` .
 
> [!WARNING]
> Ha olyan gazdagépen hoz létre virtuális gépet, amely nem rendelkezik elegendő erőforrással, a virtuális gép SIKERTELEN állapotban jön létre. 

## <a name="create-a-scale-set"></a>Méretezési csoport létrehozása 

Méretezési csoport üzembe helyezésekor meg kell adnia a gazdagépcsoportot.

```azurecli-interactive
az vmss create \
  --resource-group myResourceGroup \
  --name myScaleSet \
  --image UbuntuLTS \
  --upgrade-policy-mode automatic \
  --admin-username azureuser \
  --host-group myHostGroup \
  --generate-ssh-keys \
  --size Standard_D4s_v3 \
  -g myDHResourceGroup \
  --zone 1
```

Ha manuálisan szeretné kiválasztani, hogy melyik gazdagépre szeretné telepíteni a méretezési készletet, adja hozzá a nevet és `--host` a gazdagép nevét.


## <a name="check-the-status-of-the-host"></a>A gazdagép állapotának ellenőrzése

Az [az vm host get-instance-view](/cli/azure/vm/host#az_vm_host_get_instance_view)használatával ellenőrizheti a gazdagép állapotát és azt, hogy hány virtuális gépet helyezhet üzembe még a gazdagépen.

```azurecli-interactive
az vm host get-instance-view \
   -g myDHResourceGroup \
   --host-group myHostGroup \
   --name myHost
```
 A kimenet a következő módon fog kinézni:
 
```json
{
  "autoReplaceOnFailure": true,
  "hostId": "6de80643-0f45-4e94-9a4c-c49d5c777b62",
  "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/myDHResourceGroup/providers/Microsoft.Compute/hostGroups/myHostGroup/hosts/myHost",
  "instanceView": {
    "assetId": "12345678-1234-1234-abcd-abc123456789",
    "availableCapacity": {
      "allocatableVms": [
        {
          "count": 31.0,
          "vmSize": "Standard_D2s_v3"
        },
        {
          "count": 15.0,
          "vmSize": "Standard_D4s_v3"
        },
        {
          "count": 7.0,
          "vmSize": "Standard_D8s_v3"
        },
        {
          "count": 3.0,
          "vmSize": "Standard_D16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-8s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32-16s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D32s_v3"
        },
        {
          "count": 1.0,
          "vmSize": "Standard_D48s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-16s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64-32s_v3"
        },
        {
          "count": 0.0,
          "vmSize": "Standard_D64s_v3"
        }
      ]
    },
    "statuses": [
      {
        "code": "ProvisioningState/succeeded",
        "displayStatus": "Provisioning succeeded",
        "level": "Info",
        "message": null,
        "time": "2019-07-24T21:22:40.604754+00:00"
      },
      {
        "code": "HealthState/available",
        "displayStatus": "Host available",
        "level": "Info",
        "message": null,
        "time": null
      }
    ]
  },
  "licenseType": null,
  "location": "eastus2",
  "name": "myHost",
  "platformFaultDomain": 1,
  "provisioningState": "Succeeded",
  "provisioningTime": "2019-07-24T21:22:40.604754+00:00",
  "resourceGroup": "myDHResourceGroup",
  "sku": {
    "capacity": null,
    "name": "DSv3-Type1",
    "tier": null
  },
  "tags": null,
  "type": null,
  "virtualMachines": [
    {
      "id": "/subscriptions/10101010-1010-1010-1010-101010101010/resourceGroups/MYDHRESOURCEGROUP/providers/Microsoft.Compute/virtualMachines/MYVM",
      "resourceGroup": "MYDHRESOURCEGROUP"
    }
  ]
}

```
 
## <a name="export-as-a-template"></a>Exportálás sablonként 
Exportálhat egy sablont, ha most egy további fejlesztési környezetet szeretne létrehozni ugyanazokkal a paraméterekkel, vagy egy olyan éles környezetet, amely megfelel a sablonnak. Resource Manager JSON-sablonokat használ, amelyek meghatározzák a környezet összes paraméterét. A JSON-sablonra hivatkozva teljes környezeteket építhet ki. A JSON-sablonokat létrehozhatja manuálisan, vagy exportálhat egy meglévő környezetet a JSON-sablon létrehozásához. Az [az group export használatával](/cli/azure/group#az_group_export) exportálja az erőforráscsoportot.

```azurecli-interactive
az group export --name myDHResourceGroup > myDHResourceGroup.json 
```

Ez a parancs létrehozza `myDHResourceGroup.json` a fájlt az aktuális munkakönyvtárban. Amikor létrehoz egy környezetet ebből a sablonból, a rendszer az összes erőforrásnevet kéri. Ezeket a neveket feltöltheti a sablonfájlban, ha hozzáadja a `--include-parameter-default-value` paramétert a `az group export` parancshoz. Szerkessze a JSON-sablont az erőforrásnevek megadásához, vagy hozzon létre egy parameters.js, amely megadja az erőforrásneveket.
 
Környezet sablonból való létrehozásához használja [az az deployment group create et.](/cli/azure/deployment/group#az_deployment_group_create)

```azurecli-interactive
az deployment group create \ 
    --resource-group myNewResourceGroup \ 
    --template-file myDHResourceGroup.json 
```


## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása 

A dedikált gazdagépekért akkor is díjat számítunk fel, ha nincsenek virtuális gépek telepítve. A költségek csökkentése érdekében törölje az összes olyan gazdagépet, amely jelenleg nincs használatban.  

Csak akkor törölhet gazdagépeket, ha már nem használják azokat virtuális gépek. Törölje a virtuális gépeket az [az vm delete parancs használatával.](/cli/azure/vm#az_vm_delete)

```azurecli-interactive
az vm delete -n myVM -g myDHResourceGroup
```

A virtuális gépek törlése után az az vm host delete parancs használatával törölheti a [gazdagépet.](/cli/azure/vm/host#az_vm_host_delete)

```azurecli-interactive
az vm host delete -g myDHResourceGroup --host-group myHostGroup --name myHost 
```
 
Miután törölte az összes gazdagépet, törölheti a gazdagépcsoportot [az az vm host group delete parancs használatával.](/cli/azure/vm/host/group#az_vm_host_group_delete)  
 
```azurecli-interactive
az vm host group delete -g myDHResourceGroup --host-group myHostGroup  
```
 
A teljes erőforráscsoportot egyetlen paranccsal is törölheti. Ezzel törli a csoportban létrehozott összes erőforrást, beleértve az összes virtuális gép, gazdagép és gazdagépcsoportot is.
 
```azurecli-interactive
az group delete -n myDHResourceGroup 
```

## <a name="next-steps"></a>Következő lépések

- További információ: Dedikált [gazdagépek](../dedicated-hosts.md) áttekintése.

- Dedikált gazdagépeket is létrehozhat a [Azure Portal.](../dedicated-hosts-portal.md)

- Itt található egy [](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)mintasablon, amely zónákat és tartalék tartományokat is használ a maximális rugalmasság érdekében egy adott régióban.
