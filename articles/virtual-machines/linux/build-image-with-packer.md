---
title: Linuxos Azure-beli virtuálisgép-rendszerképek létrehozása a Packerrel
description: Megtudhatja, hogyan hozhat létre Rendszerképeket Linux rendszerű virtuális gépekről az Azure-ban a Packer használatával
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 05/07/2019
ms.author: cynthn
ms.collection: linux
ms.openlocfilehash: 1b40646109265b803945b43d7cc855688c5b47c5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764656"
---
# <a name="how-to-use-packer-to-create-linux-virtual-machine-images-in-azure"></a>Linux rendszerű virtuálisgép-rendszerképek létrehozása az Azure-ban a Packer használatával
Az Azure-ban minden virtuális gép (VM) a Linux-disztribúciót és az operációs rendszer verzióját meghatározó rendszerképből jön létre. A rendszerképek tartalmazhatnak előre telepített alkalmazásokat és konfigurációkat. A Azure Marketplace számos külső és külső rendszerképet biztosít a leggyakoribb disztribúciókhoz és alkalmazáskörnyezetekhez, de saját, az igényeinek megfelelő egyéni rendszerképeket is létrehozhat. Ez a cikk részletesen bemutatja, hogyan definiálhat és hozhat létre egyéni rendszerképeket az Azure-ban a nyílt forráskódú [Packer](https://www.packer.io/) eszközzel.

> [!NOTE]
> Az Azure most már rendelkezik egy Azure Image Builder (előzetes verzió) szolgáltatással, amely saját egyéni rendszerképeket definiál és hoz létre. Az Azure Image Builder Packerre épül, így meglévő Packer rendszerhéj-kiépítési szkripteket is használhat vele. Az Azure Image Builder első lépésekért [lásd: Linux](image-builder.md)rendszerű virtuális gép létrehozása az Azure Image Builder.


## <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása
A buildfolyamat során a Packer ideiglenes Azure-erőforrásokat hoz létre a forrás virtuális gép buildje során. A forrás virtuális gép rendszerképként való rögzítéséhez meg kell határoznia egy erőforráscsoportot. A Packer buildfolyamatának kimenete ebben az erőforráscsoportban van tárolva.

Hozzon létre egy erőforráscsoportot az [az group create](/cli/azure/group) paranccsal. A következő példában létrehozunk egy *myResourceGroup* nevű erőforráscsoportot az *eastus* helyen:

```azurecli
az group create -n myResourceGroup -l eastus
```


## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása
A Packer szolgáltatásnévvel hitelesíti magát az Azure-ral. Az Azure-szolgáltatásnév egy biztonsági identitás, amely olyan alkalmazásokkal, szolgáltatásokkal és automatizálási eszközökkel használható, mint a Packer. Ön határozza meg és határozza meg, hogy a szolgáltatásnév milyen műveleteket hajthat végre az Azure-ban.

Hozzon létre egy egyszerű szolgáltatást [az az ad sp create-for-rbac parancs segítségével,](/cli/azure/ad/sp) és a következő hitelesítő adatokat hozza létre, amelyekre a Packernek szüksége van:

```azurecli
az ad sp create-for-rbac --query "{ client_id: appId, client_secret: password, tenant_id: tenant }"
```

Az előző parancsok kimenete például a következő:

```output
{
    "client_id": "f5b6a5cf-fbdf-4a9f-b3b8-3c2cd00225a4",
    "client_secret": "0e760437-bf34-4aad-9f8d-870be799c55d",
    "tenant_id": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

Az Azure-beli hitelesítéshez az Azure-előfizetés azonosítóját is be kell szereznie [az az account show használatával:](/cli/azure/account)

```azurecli
az account show --query "{ subscription_id: id }"
```

A következő lépésben a két parancs kimenetét használja.


## <a name="define-packer-template"></a>Packer-sablon meghatározása
A rendszerképek létrehozásához hozzon létre egy sablont JSON-fájlként. A sablonban definiálhatja azokat a szerkesztőket és kiépítési adatokat, amelyek végrehajtják a tényleges buildfolyamatot. A Packer rendelkezik egy [Azure-kiépítési](https://www.packer.io/docs/builders/azure.html) szolgáltatással, amely lehetővé teszi Azure-erőforrások, például az előző lépésben létrehozott szolgáltatásnév-hitelesítő adatok megadását.

Hozzon létre egy nevű *ubuntu.js,* és illessze be a következő tartalmat. Adja meg a saját értékeit az alábbiakhoz:

| Paraméter                           | Hol szerezhető be |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | A create parancs kimenetének első `az ad sp` sora – *appId* |
| *client_secret*                     | A create parancs második kimeneti `az ad sp` sora – *jelszó* |
| *tenant_id*                         | A create parancs kimenetének harmadik `az ad sp` sora – *bérlő* |
| *subscription_id*                   | Parancs `az account show` kimenete |
| *managed_image_resource_group_name* | Az első lépésben létrehozott erőforráscsoport neve |
| *managed_image_name*                | A létrehozott felügyeltlemez-rendszerkép neve |


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

Ez a sablon egy Ubuntu 16.04 LTS-rendszerképet hoz létre, telepíti az NGINX-et, majd megszünteti a virtuális gépet.

> [!NOTE]
> Ha kibontja ezt a sablont a felhasználói hitelesítő adatok építéséhez, módosítsa a kiépítési parancsot, amely megszünteti az Azure-ügynök olvasását a `-deprovision` `deprovision+user` helyett.
> A `+user` jelző eltávolítja az összes felhasználói fiókot a forrás virtuális gépről.


## <a name="build-packer-image"></a>Packer-rendszerkép összeállítása
Ha még nincs telepítve a Packer a helyi gépen, kövesse [a Packer telepítési utasításait.](https://www.packer.io/docs/install)

A rendszerkép felépítéséhez adja meg a Packer-sablonfájlt az alábbiak szerint:

```bash
./packer build ubuntu.json
```

Az előző parancsok kimenete például a következő:

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

Eltarthat néhány percig, hogy a Packer felépítse a virtuális gépet, futtassa a kiépítéseket, és megtisztítsa az üzemelő példányokat.


## <a name="create-vm-from-azure-image"></a>Virtuális gép létrehozása az Azure-rendszerképből
Most már létrehozhat egy virtuális gépet a rendszerképből az [az vm create segítségével.](/cli/azure/vm) Adja meg a létrehozott rendszerképet a `--image` paraméterrel. Az alábbi példa létrehoz egy *myVM* nevű virtuális gépet a *myPackerImage rendszerből,* és SSH-kulcsokat hoz létre, ha még nem léteznek:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --name myVM \
    --image myPackerImage \
    --admin-username azureuser \
    --generate-ssh-keys
```

Ha a Packer-rendszerképtől eltérő erőforráscsoportban vagy régióban szeretne virtuális gépeket létrehozni, a rendszerkép neve helyett adja meg a rendszerkép azonosítóját. A képazonosítót az [az image show segítségével szerezheti be.](/cli/azure/image#az_image_show)

A virtuális gép létrehozása néhány percet vesz igénybe. A virtuális gép létrehozása után jegyezze fel az `publicIpAddress` Azure CLI által megjelenített üzenetet. Ez a cím az NGINX-webhely webböngészőn keresztüli elérésére használható.

Ahhoz, hogy a webes adatforgalom elérje a virtuális gépét, nyissa meg az internetről a 80-as portot az [az vm open-port](/cli/azure/vm) paranccsal:

```azurecli
az vm open-port \
    --resource-group myResourceGroup \
    --name myVM \
    --port 80
```

## <a name="test-vm-and-nginx"></a>Virtuális gép és NGINX tesztelése
Most nyisson meg egy webböngészőt, és írja be a `http://publicIpAddress` címet a címsorba. Adja meg a saját nyilvános IP-címét, amelyet a virtuális gép létrehozásakor kapott. Az alapértelmezett NGINX-oldal a következő példában látható:

![Alapértelmezett NGINX-webhely](./media/build-image-with-packer/nginx.png) 


## <a name="next-steps"></a>Következő lépések
Meglévő Packer kiépítési szkripteket is használhat az [Azure Image Builder.](image-builder.md)
