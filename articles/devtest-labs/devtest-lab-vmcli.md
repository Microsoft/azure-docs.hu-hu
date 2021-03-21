---
title: Virtuális gépek létrehozása és kezelése a DevTest Labs szolgáltatásban az Azure CLI-vel
description: Ismerje meg, hogyan hozhat létre és kezelhet virtuális gépeket az Azure CLI-vel a Azure DevTest Labs használatával
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 22ee6bf607fe1b66cece0e7ddb25a2da2830258b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102201464"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Virtuális gépek létrehozása és kezelése az Azure CLI-vel a DevTest Labs használatával
Ez a rövid útmutató végigvezeti a fejlesztői gépek tesztkörnyezetben való létrehozásával, indításával, csatlakoztatásával, frissítésével és tisztításával. 

Előkészületek:

* Ha nem hoztak létre labort, [itt](devtest-lab-create-lab.md)találhat útmutatást.

* [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t. Az indításhoz futtassa az az login parancsot az Azure-beli kapcsolatok létrehozásához. 

## <a name="create-and-verify-the-virtual-machine"></a>A virtuális gép létrehozása és ellenőrzése 
A DevTest Labs-hez kapcsolódó parancsok végrehajtása előtt állítsa be a megfelelő Azure-környezetet a következő `az account set` paranccsal:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

A virtuális gép létrehozásához szükséges parancs a következő: `az lab vm create` . Az erőforráscsoport a laborhoz, a labor neve és a virtuális gép neve egyaránt szükséges. A többi argumentum a virtuális gép típusától függően változik.

A következő parancs egy Windows-alapú rendszerképet hoz létre az Azure Market Place-ből. A rendszerkép neve ugyanaz, mint amikor a Azure Portal használatával hoz létre virtuális gépet. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

A következő parancs egy virtuális gépet hoz létre a laborban elérhető egyéni rendszerkép alapján:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

A **rendszerkép típusú** argumentum a katalógusból a **Custom** **értékre** változott. A rendszerkép neve megegyezik azzal, amit látni fog, ha a Azure Portalban hozza létre a virtuális gépet.

A következő parancs létrehoz egy virtuális gépet egy, az SSH-hitelesítéssel rendelkező Piactéri rendszerképből:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

A képletek alapján is létrehozhat virtuális gépeket, ha a **rendszerkép-típus** paramétert **képletre** állítja. Ha ki kell választania egy adott virtuális hálózatot a virtuális géphez, használja a **vnet** és az **alhálózati** paramétereket. További információ: [az Lab VM Create](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="verify-that-the-vm-is-available"></a>Ellenőrizze, hogy a virtuális gép elérhető-e.
A `az lab vm show` parancs használatával ellenőrizze, hogy a virtuális gép elérhető-e, mielőtt elkezdi és csatlakozik hozzá. 

```azurecli
az lab vm show --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup --expand 'properties($expand=ComputeVm,NetworkInterface)' --query '{status: computeVm.statuses[0].displayStatus, fqdn: fqdn, ipAddress: networkInterface.publicIpAddress}'
```
```json
{
  "fqdn": "lisalabvm.southcentralus.cloudapp.azure.com",
  "ipAddress": "13.85.228.112",
  "status": "Provisioning succeeded"
}
```

## <a name="start-and-connect-to-the-virtual-machine"></a>A virtuális gép elindítása és kapcsolódás
A következő példában szereplő parancs egy virtuális gépet indít el:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Kapcsolódás virtuális géphez: [SSH](../virtual-machines/linux/mac-create-ssh-keys.md) vagy [Távoli asztal](../virtual-machines/windows/connect-logon.md).
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>A virtuális gép frissítése
A következő minta parancs az összetevőket egy virtuális gépre alkalmazza:

```azurecli
az lab vm apply-artifacts --lab-name  sampleLabName --name sampleVMName  --resource-group sampleResourceGroup  --artifacts @/artifacts.json
```

```json
[
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-java",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-install-nodejs",
    "parameters": []
  },
  {
    "artifactId": "/artifactSources/public repo/artifacts/linux-apt-package",
    "parameters": [
      {
        "name": "packages",
        "value": "abcd"
      },
      {
        "name": "update",
        "value": "true"
      },
      {
        "name": "options",
        "value": ""
      }
    ]
  } 
]
```

### <a name="list-artifacts-available-in-the-lab"></a>A laborban elérhető összetevők listázása

A laborban található virtuális gépen elérhető összetevők listázásához futtassa a következő parancsokat.

**Cloud Shell – PowerShell**: figyelje meg, hogy a kezdő ( \` ) a $-ban $Expand előtt (azaz "$Expand") használja:

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud SHELL – bash**: figyelje meg a perjel () karakter használatát a (z \\ ) parancsban. 

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(\$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

Példa a kimenetre: 

```json
[
  {
    "artifactId": "/subscriptions/<subscription ID>/resourceGroups/<resource group name>/providers/Microsoft.DevTestLab/labs/<lab name>/artifactSources/public repo/artifacts/windows-7zip",
    "status": "Succeeded"
  }
]
```

## <a name="stop-and-delete-the-virtual-machine"></a>A virtuális gép leállítása és törlése    
A következő minta parancs leállítja a virtuális gépet.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Virtuális gép törlése.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Következő lépések
Tekintse meg az alábbi tartalmat: [Azure DevTest Labs Azure CLI-dokumentációja](/cli/azure/lab). 
