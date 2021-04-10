---
title: Linux Azure VM-rendszerképek létrehozása a Csomagolóval
description: Ismerje meg, hogyan hozhat létre az Azure-ban Linux rendszerű virtuális gépek képeit a csomagoló használatával
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: 4d85106cb78b5f4799a78d18463d83594f54dbac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "102556740"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Linuxos virtuálisgép-lemezképek létrehozása az Azure-ban a csomagoló használatával
Az Azure-ban minden virtuális gép (VM) egy olyan rendszerképből jön létre, amely meghatározza a Linux-disztribúciót és az operációs rendszer verzióját. A képek tartalmazhatnak előre telepített alkalmazásokat és konfigurációkat is. Az Azure piactér számos első és harmadik féltől származó rendszerképet biztosít a leggyakoribb disztribúciók és alkalmazási környezetek számára, vagy létrehozhat saját igényeire szabott egyéni rendszerképeket is. Ez a cikk részletesen ismerteti, hogyan lehet egyéni lemezképeket definiálni és létrehozni az Azure-ban a nyílt forráskódú eszköz- [csomagoló](https://www.packer.io/) használatával.

> [!NOTE]
> Az Azure-ban már van egy szolgáltatás, egy Azure Image Builder (előzetes verzió), amellyel meghatározhatja és létrehozhatja saját egyéni rendszerképeit. Az Azure rendszerkép-szerkesztő a Csomagolón alapul, így a meglévő csomagoló rendszerhéj-szkripteket is használhatja. Az Azure rendszerkép-szerkesztő megkezdéséhez lásd: [linuxos virtuális gép létrehozása az Azure rendszerkép-készítővel](image-builder.md).


## <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása
A kiépítési folyamat során a csomagoló ideiglenes Azure-erőforrásokat hoz létre, mivel létrehozza a forrás virtuális gépet. Ahhoz, hogy a forrás virtuális gép lemezképként használható legyen, meg kell határoznia egy erőforráscsoportot. Ez az erőforráscsoport tárolja a csomagoló-összeállítási folyamat kimenetét.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása
A csomagoló az Azure-ban egy egyszerű szolgáltatásnév használatával hitelesíti magát. Az Azure egyszerű szolgáltatás olyan biztonsági identitás, amely az alkalmazásokkal, szolgáltatásokkal és automatizálási eszközökkel, például a csomagoló eszközzel használható. Ön szabályozhatja és meghatározhatja az engedélyeket az Azure-ban az egyszerű szolgáltatás által elvégezhető műveletekhez.

Hozzon létre egy egyszerű szolgáltatást az [az ad SP Create-for-RBAC](/cli/azure/ad/sp) , és adja meg a csomagoló által igényelt hitelesítő adatokat:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Az előző parancsok kimenetének példája a következő:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Az Azure-ban való hitelesítéshez az Azure-előfizetés AZONOSÍTÓját is be kell szereznie az [az Account show](/cli/azure/account)paranccsal:

```azurecli
az account show --query "{ subscription_id: id }"
```

A következő lépésben a két parancs kimenetét kell használnia.


## <a name="define-packer-template"></a>Csomagoló sablon definiálása
Lemezképek létrehozásához JSON-fájlként hozzon létre egy sablont. A sablonban meg kell határoznia a tényleges felépítési folyamatot végző építőket és kiépítési folyamatokat. A csomagoló rendelkezik [Az Azure](https://www.packer.io/docs/builders/azure.html) -hoz, amely lehetővé teszi az Azure-erőforrások, például az előző lépésben létrehozott szolgáltatásnév hitelesítő adatok definiálását.

Hozzon létre egy *ubuntu.js* nevű fájlt, és illessze be a következő tartalmat. Adja meg a saját értékeit a következőkhöz:

| Paraméter                           | A beszerzés helye |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | A `az ad sp` create Command- *AppID* első kimenetének sora |
| *client_secret*                     | Kimenet második sora a `az ad sp` create Command- *Password* |
| *tenant_id*                         | Harmadik kimeneti vonal a `az ad sp` create parancsból – *bérlő* |
| *subscription_id*                   | Kimenet a `az account show` parancsból |
| *managed_image_resource_group_name* | Az első lépésben létrehozott erőforráscsoport neve |
| *managed_image_name*                | A létrehozott felügyelt lemezkép neve |


```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47",
    "subscription_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",

    "managed_image_resource_group_name": "myResourceGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Linux",
    "image_publisher": "Canonical",
    "image_offer": "UbuntuServer",
    "image_sku": "16.04-LTS",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "location": "East US",
    "vm_size": "Standard_DS2_v2"
  }],
  "provisioners": [{
    "execute_command": "chmod +x {{ .Path }}; {{ .Vars }} sudo -E sh '{{ .Path }}'",
    "inline": [
      "apt-get update",
      "apt-get upgrade -y",
      "apt-get -y install nginx",

      "/usr/sbin/waagent -force -deprovision+user && export HISTSIZE=0 && sync"
    ],
    "inline_shebang": "/bin/sh -x",
    "type": "shell"
  }]
}
```

Ez a sablon egy Ubuntu 16,04 LTS-rendszerképet hoz létre, telepíti az NGINX-et, majd kiosztja a virtuális gépet.

> [!NOTE]
> Ha kibontja ezt a sablont a felhasználói hitelesítő adatok kiépítéséhez, állítsa be úgy a kiépítési parancsot, hogy az Azure-ügynököt ne a helyett olvassa el `-deprovision` `deprovision+user` .
> A `+user` jelző eltávolítja az összes felhasználói fiókot a forrás virtuális gépről.


## <a name="build-packer-image"></a>Csomagoló rendszerkép létrehozása
Ha még nincs telepítve a csomagoló a helyi gépen, [kövesse a csomagoló telepítési utasításait](https://www.packer.io/docs/install).

Hozza létre a képet a csomagoló sablon fájljának megadásával a következőképpen:

```bash
./packer build ubuntu.json
```

Az előző parancsok kimenetének példája a következő:

```output
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> dept : Engineering
==> azure-arm:  ->> task : Image deployment
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> DeploymentName    : ‘pkrdpswtxmqm7ly’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.218.147’
==> azure-arm: Waiting for SSH to become available...
==> azure-arm: Connected to SSH!
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-shell868574263
    azure-arm: WARNING! The waagent service will be stopped.
    azure-arm: WARNING! Cached DHCP leases will be deleted.
    azure-arm: WARNING! root password will be disabled. You will not be able to login as root.
    azure-arm: WARNING! /etc/resolvconf/resolv.conf.d/tail and /etc/resolvconf/resolv.conf.d/original will be deleted.
    azure-arm: WARNING! packer account and entire home directory will be deleted.
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-swtxmqm7ly/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> ComputeName       : ‘pkrvmswtxmqm7ly’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm:  -> Compute Name              : ‘pkrvmswtxmqm7ly’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-swtxmqm7ly’
==> azure-arm: Deleting the temporary OS disk ...
==> azure-arm:  -> OS Disk : skipping, managed disk was used...
Build ‘azure-arm’ finished.

==> Builds finished. The artifacts of successful builds are:
--> azure-arm: Azure.ResourceManagement.VMImage:

ManagedImageResourceGroupName: myResourceGroup
ManagedImageName: myPackerImage
ManagedImageLocation: eastus
```

Eltarthat néhány percig, amíg a csomagoló létrehozza a virtuális gépet, futtathatja a kiépítő példányokat, és megtisztíthatja az üzembe helyezést.


## <a name="create-vm-from-azure-image"></a>Virtuális gép létrehozása az Azure-rendszerképből
Mostantól létrehozhat egy virtuális gépet a rendszerképből az [az VM Create](/cli/azure/vm)paranccsal. Határozza meg a paraméterrel létrehozott rendszerképet `--image` . Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre a *myPackerImage* -ből, és SSH-kulcsokat hoz létre, ha azok még nem léteznek:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ha virtuális gépeket szeretne létrehozni egy másik erőforráscsoporthoz vagy régióban, mint a csomagoló-rendszerkép, a rendszerkép neve helyett adja meg a rendszerkép AZONOSÍTÓját. A rendszerkép-azonosítót az [az rendszerkép show](/cli/azure/image#az-image-show)paranccsal szerezheti be.

A virtuális gép létrehozása néhány percet vesz igénybe. Miután létrejött a virtuális gép, jegyezze fel az `publicIpAddress` Azure CLI által megjelenített adatmennyiséget. Ez a címe az NGINX-webhely webböngészőből való elérésére szolgál.

Ahhoz, hogy a webes adatforgalom elérje a virtuális gépét, nyissa meg az internetről a 80-as portot az [az vm open-port](/cli/azure/vm) paranccsal:

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Virtuális gép és NGINX tesztelése
Most nyisson meg egy webböngészőt, és írja be a `http://publicIpAddress` címet a címsorba. Adja meg a saját nyilvános IP-címét, amelyet a virtuális gép létrehozásakor kapott. Az alapértelmezett NGINX-lap az alábbi példában látható módon jelenik meg:

![Alapértelmezett NGINX-webhely](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Következő lépések
Az [Azure rendszerkép-készítővel](image-builder.md)meglévő csomagoló-kiépítési parancsfájlokat is használhat.
