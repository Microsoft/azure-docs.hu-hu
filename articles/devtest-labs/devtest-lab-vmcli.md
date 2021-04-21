---
title: Virtuális gépek létrehozása és kezelése a DevTest Labsben az Azure CLI használatával
description: Megtudhatja, hogyan hozhat létre és Azure DevTest Labs virtuális gépeket az Azure CLI használatával a virtuális gépekkel
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 95e0add8ce14e47c609b1ae951673c261316293f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763540"
---
# <a name="create-and-manage-virtual-machines-with-devtest-labs-using-the-azure-cli"></a>Virtuális gépek létrehozása és kezelése a DevTest Labs használatával az Azure CLI használatával
Ez a rövid útmutató végigvezeti a tesztkörnyezetben található fejlesztői gépek létrehozásának, indításának, csatlakoztatásának, frissítésének és tisztításának folyamatán. 

Előkészületek:

* Ha még nem hozott létre tesztkörnyezetet, itt [talál](devtest-lab-create-lab.md)útmutatást.

* [Telepítse az Azure CLI-t.](/cli/azure/install-azure-cli) Először futtassa az az login futtatását az Azure-ral való kapcsolat létrehozásához. 

## <a name="create-and-verify-the-virtual-machine"></a>A virtuális gép létrehozása és ellenőrzése 
A DevTest Labshez kapcsolódó parancsok végrehajtása előtt állítsa be a megfelelő Azure-környezetet az `az account set` paranccsal:

```azurecli
az account set --subscription 11111111-1111-1111-1111-111111111111
```

A virtuális gép létrehozására használt parancs a következő: `az lab vm create` . A tesztkörnyezet erőforráscsoportja, a tesztkörnyezet neve és a virtuális gép neve is szükséges. A többi argumentum a virtuális gép típusától függően változik.

Az alábbi parancs egy Windows-alapú rendszerképet hoz létre az Azure Market Place-ről. A rendszerkép neve ugyanaz, mint amikor virtuális gépet hoz létre a Azure Portal. 

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "Visual Studio Community 2017 on Windows Server 2016 (x64)" --image-type gallery --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

A következő parancs létrehoz egy virtuális gépet a tesztkörnyezetben elérhető egyéni rendszerkép alapján:

```azurecli
az lab vm create --resource-group DtlResourceGroup --lab-name MyLab --name 'MyTestVm' --image "My Custom Image" --image-type custom --size 'Standard_D2s_v3' --admin-username 'AdminUser' --admin-password 'Password1!'
```

A **képtípus argumentuma** katalógusról **egyénire** **változott.** A rendszerkép neve megegyezik azzal, amit akkor lát, ha a virtuális gépet a Azure Portal.

A következő parancs létrehoz egy virtuális gépet egy marketplace-ről származó rendszerképből SSH-hitelesítéssel:

```azurecli
az lab vm create --lab-name sampleLabName --resource-group sampleLabResourceGroup --name sampleVMName --image "Ubuntu Server 16.04 LTS" --image-type gallery --size Standard_DS1_v2 --authentication-type  ssh --generate-ssh-keys --ip-configuration public 
```

A képletek alapján is létrehozhat virtuális gépeket, ha a **képtípus paramétert** a képletre **adja meg.** Ha egy adott virtuális hálózatot kell választania a virtuális géphez, használja a **vnet-name** és az **alhálózat paramétereit.** További információ: [az lab vm create.](/cli/azure/lab/vm#az_lab_vm_create)

## <a name="verify-that-the-vm-is-available"></a>Ellenőrizze, hogy a virtuális gép elérhető-e.
A paranccsal ellenőrizze, hogy a virtuális gép elérhető-e, mielőtt elindítja és `az lab vm show` csatlakozik hozzá. 

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

## <a name="start-and-connect-to-the-virtual-machine"></a>Indítsa el a virtuális gépet, és csatlakozzon a géphez
Az alábbi példaparancs elindít egy virtuális gépet:

```azurecli
az lab vm start --lab-name sampleLabName --name sampleVMName --resource-group sampleLabResourceGroup
```

Csatlakozás virtuális géphez: [SSH vagy](../virtual-machines/linux/mac-create-ssh-keys.md) [Távoli asztal.](../virtual-machines/windows/connect-logon.md)
```bash
ssh userName@ipAddressOrfqdn 
```

## <a name="update-the-virtual-machine"></a>A virtuális gép frissítése
A következő mintaparancs összetevőt alkalmaz egy virtuális gépre:

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

### <a name="list-artifacts-available-in-the-lab"></a>A tesztkörnyezetben elérhető összetevők felsorolása

A tesztkörnyezetben található virtuális gépeken elérhető összetevők listához futtassa a következő parancsokat.

**Cloud Shell – PowerShell:** figyelje meg a backtick ( ) használatát a $ előtt \` a $expand (pl. "$expand):

```azurecli-interactive
az lab vm show --resource-group <resourcegroupname> --lab-name <labname> --name <vmname> --expand "properties(`$expand=artifacts)" --query "artifacts[].{artifactId: artifactId, status: status}"
```

**Cloud Shell – Bash:** figyelje meg a perjel ( ) karaktert a $ előtt a \\ parancsban. 

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
Az alábbi mintaparancs leállít egy virtuális gépet.

```azurecli
az lab vm stop --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

Virtuális gép törlése.
```azurecli
az lab vm delete --lab-name sampleLabName --name sampleVMName --resource-group sampleResourceGroup
```

## <a name="next-steps"></a>Következő lépések
Tekintse meg a következő tartalmat: [Az Azure CLI dokumentációja a Azure DevTest Labs.](/cli/azure/lab) 
