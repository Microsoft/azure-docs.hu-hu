---
title: PowerShell – virtuálisgép-rendszerképek létrehozása a csomagoló használatával
description: Ismerje meg, hogyan hozhat létre lemezképeket az Azure-ban a csomagoló és a PowerShell használatával
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: windows
ms.topic: how-to
ms.workload: infrastructure
ms.date: 08/05/2020
ms.author: cynthn
ms.openlocfilehash: a2cd6152f9c8e75223fcf1aab77fee7700223979
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102561007"
---
# <a name="powershell-how-to-use-packer-to-create-virtual-machine-images-in-azure"></a>PowerShell: virtuálisgép-rendszerképek létrehozása a csomagoló használatával az Azure-ban
Az Azure-ban minden virtuális gép (VM) egy olyan rendszerképből jön létre, amely meghatározza a Windows-disztribúciót és az operációs rendszer verzióját. A képek tartalmazhatnak előre telepített alkalmazásokat és konfigurációkat is. Az Azure Marketplace számos első és harmadik féltől származó rendszerképet biztosít a leggyakoribb operációsrendszer-és alkalmazás-környezetekhez, vagy létrehozhat saját igényeire szabott egyéni rendszerképeket is. Ez a cikk részletesen ismerteti, hogyan lehet egyéni lemezképeket definiálni és létrehozni az Azure-ban a nyílt forráskódú eszköz [csomagoló](https://www.packer.io/) használatával.

Ez a cikk az 8/5/2020-es, a [csomagoló](https://www.packer.io/docs/install) 1.6.1-es verziójának használatával volt utoljára tesztelve.

> [!NOTE]
> Az Azure-ban már van egy szolgáltatás, egy Azure Image Builder (előzetes verzió), amellyel meghatározhatja és létrehozhatja saját egyéni rendszerképeit. Az Azure rendszerkép-szerkesztő a Csomagolón alapul, így a meglévő csomagoló rendszerhéj-szkripteket is használhatja. Az Azure rendszerkép-szerkesztő megkezdéséhez tekintse meg [a Windows rendszerű virtuális gép létrehozása az Azure rendszerkép-készítővel](image-builder.md)című témakört.

## <a name="create-azure-resource-group"></a>Azure-erőforráscsoport létrehozása
A kiépítési folyamat során a csomagoló ideiglenes Azure-erőforrásokat hoz létre, mivel létrehozza a forrás virtuális gépet. Ahhoz, hogy a forrás virtuális gép lemezképként használható legyen, meg kell határoznia egy erőforráscsoportot. Ez az erőforráscsoport tárolja a csomagoló-összeállítási folyamat kimenetét.

Hozzon létre egy erőforráscsoportot a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). A következő példában létrehozunk egy *myPackerGroup* nevű erőforráscsoportot a *eastus* helyen:

```azurepowershell
$rgName = "myPackerGroup"
$location = "East US"
New-AzResourceGroup -Name $rgName -Location $location
```

## <a name="create-azure-credentials"></a>Azure-beli hitelesítő adatok létrehozása
A csomagoló az Azure-ban egy egyszerű szolgáltatásnév használatával hitelesíti magát. Az Azure egyszerű szolgáltatás olyan biztonsági identitás, amely az alkalmazásokkal, szolgáltatásokkal és automatizálási eszközökkel, például a csomagoló eszközzel használható. Ön szabályozhatja és meghatározhatja az engedélyeket az Azure-ban az egyszerű szolgáltatás által elvégezhető műveletekhez.

Hozzon létre egy egyszerű szolgáltatásnevet a [New-AzADServicePrincipal](/powershell/module/az.resources/new-azadserviceprincipal). Az értéknek `-DisplayName` egyedinek kell lennie; szükség esetén cserélje le a értéket a saját értékére.  

```azurepowershell
$sp = New-AzADServicePrincipal -DisplayName "PackerSP$(Get-Random)"
$BSTR = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret)
$plainPassword = [System.Runtime.InteropServices.Marshal]::PtrToStringAuto($BSTR)
```

Ezután írja be a jelszót és az alkalmazás AZONOSÍTÓját.

```powershell
$plainPassword
$sp.ApplicationId
```


Az Azure-ban való hitelesítéshez az Azure-bérlőt és az előfizetési azonosítókat is be kell szereznie a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription)használatával:

```powershell
Get-AzSubscription
```


## <a name="define-packer-template"></a>Csomagoló sablon definiálása
Lemezképek létrehozásához JSON-fájlként hozzon létre egy sablont. A sablonban meg kell határoznia a tényleges felépítési folyamatot végző építőket és kiépítési folyamatokat. A csomagoló rendelkezik egy [Azure-előkészítővel](https://www.packer.io/docs/builders/azure.html) , amely lehetővé teszi az Azure-erőforrások, például az előző lépésben létrehozott egyszerű szolgáltatás hitelesítő adatainak definiálását.

Hozzon létre egy *windows.js* nevű fájlt, és illessze be a következő tartalmat. Adja meg a saját értékeit a következőkhöz:

| Paraméter                           | A beszerzés helye |
|-------------------------------------|----------------------------------------------------|
| *client_id*                         | Egyszerű szolgáltatás AZONOSÍTÓjának megtekintése `$sp.applicationId` |
| *client_secret*                     | Az automatikusan generált jelszó megtekintése a `$plainPassword` |
| *tenant_id*                         | Kimenet a `$sub.TenantId` parancsból |
| *subscription_id*                   | Kimenet a `$sub.SubscriptionId` parancsból |
| *managed_image_resource_group_name* | Az első lépésben létrehozott erőforráscsoport neve |
| *managed_image_name*                | A létrehozott felügyelt lemezkép neve |

```json
{
  "builders": [{
    "type": "azure-arm",

    "client_id": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "client_secret": "ppppppp-pppp-pppp-pppp-ppppppppppp",
    "tenant_id": "zzzzzzz-zzzz-zzzz-zzzz-zzzzzzzzzzzz",
    "subscription_id": "yyyyyyy-yyyy-yyyy-yyyy-yyyyyyyyyyy",

    "managed_image_resource_group_name": "myPackerGroup",
    "managed_image_name": "myPackerImage",

    "os_type": "Windows",
    "image_publisher": "MicrosoftWindowsServer",
    "image_offer": "WindowsServer",
    "image_sku": "2016-Datacenter",

    "communicator": "winrm",
    "winrm_use_ssl": true,
    "winrm_insecure": true,
    "winrm_timeout": "5m",
    "winrm_username": "packer",

    "azure_tags": {
        "dept": "Engineering",
        "task": "Image deployment"
    },

    "build_resource_group_name": "myPackerGroup",
    "vm_size": "Standard_D2_v2"
  }],
  "provisioners": [{
    "type": "powershell",
    "inline": [
      "Add-WindowsFeature Web-Server",
      "while ((Get-Service RdAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "while ((Get-Service WindowsAzureGuestAgent).Status -ne 'Running') { Start-Sleep -s 5 }",
      "& $env:SystemRoot\\System32\\Sysprep\\Sysprep.exe /oobe /generalize /quiet /quit",
      "while($true) { $imageState = Get-ItemProperty HKLM:\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Setup\\State | Select ImageState; if($imageState.ImageState -ne 'IMAGE_STATE_GENERALIZE_RESEAL_TO_OOBE') { Write-Output $imageState.ImageState; Start-Sleep -s 10  } else { break } }"
    ]
  }]
}
```

Ez a sablon egy Windows Server 2016 rendszerű virtuális gépet hoz létre, telepíti az IIS-t, majd a Sysprep használatával általánosítja a virtuális gépet. Az IIS-telepítés azt mutatja be, hogyan használható a PowerShell-kiépítés további parancsok futtatásához. A végleges csomagoló-rendszerkép tartalmazza a szükséges szoftverek telepítését és konfigurálását.

A Windows vendég ügynök részt vesz a Sysprep folyamatban. Az ügynököt teljesen telepíteni kell ahhoz, hogy a virtuális gép sysprep'ed. Annak érdekében, hogy ez igaz legyen, az összes ügynök-szolgáltatásnak futnia kell a sysprep.exe végrehajtása előtt. A fenti JSON-kódrészlet az egyik módszert mutatja be a PowerShell-kiépítő használatával. Ezt a kódrészletet csak akkor kell megadni, ha a virtuális gép az ügynök telepítésére van konfigurálva, ami az alapértelmezett.


## <a name="build-packer-image"></a>Csomagoló rendszerkép létrehozása
Ha még nincs telepítve a csomagoló a helyi gépen, [kövesse a csomagoló telepítési utasításait](https://learn.hashicorp.com/packer/getting-started/install).

A rendszerkép létrehozásához nyisson meg egy parancssort, és adja meg a csomagoló sablon fájlját a következőképpen:

```
./packer build windows.json
```

Az előző parancsok kimenetének példája a következő:

```bash
azure-arm output will be in this color.

==> azure-arm: Running builder ...
    azure-arm: Creating Azure Resource Manager (ARM) client ...
==> azure-arm: Creating resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Location          : ‘East US’
==> azure-arm:  -> Tags              :
==> azure-arm:  ->> task : Image deployment
==> azure-arm:  ->> dept : Engineering
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the certificate’s URL ...
==> azure-arm:  -> Key Vault Name        : ‘pkrkvpq0mthtbtt’
==> azure-arm:  -> Key Vault Secret Name : ‘packerKeyVaultSecret’
==> azure-arm:  -> Certificate URL       : ‘https://pkrkvpq0mthtbtt.vault.azure.net/secrets/packerKeyVaultSecret/8c7bd823e4fa44e1abb747636128adbb'
==> azure-arm: Setting the certificate’s URL ...
==> azure-arm: Validating deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Deploying deployment template ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> DeploymentName    : ‘pkrdppq0mthtbtt’
==> azure-arm: Getting the VM’s IP address ...
==> azure-arm:  -> ResourceGroupName   : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> PublicIPAddressName : ‘packerPublicIP’
==> azure-arm:  -> NicName             : ‘packerNic’
==> azure-arm:  -> Network Connection  : ‘PublicEndpoint’
==> azure-arm:  -> IP Address          : ‘40.76.55.35’
==> azure-arm: Waiting for WinRM to become available...
==> azure-arm: Connected to WinRM!
==> azure-arm: Provisioning with Powershell...
==> azure-arm: Provisioning with shell script: /var/folders/h1/ymh5bdx15wgdn5hvgj1wc0zh0000gn/T/packer-powershell-provisioner902510110
    azure-arm: #< CLIXML
    azure-arm:
    azure-arm: Success Restart Needed Exit Code      Feature Result
    azure-arm: ------- -------------- ---------      --------------
    azure-arm: True    No             Success        {Common HTTP Features, Default Document, D...
    azure-arm: <Objs Version=“1.1.0.1” xmlns=“http://schemas.microsoft.com/powershell/2004/04"><Obj S=“progress” RefId=“0"><TN RefId=“0”><T>System.Management.Automation.PSCustomObject</T><T>System.Object</T></TN><MS><I64 N=“SourceId”>1</I64><PR N=“Record”><AV>Preparing modules for first use.</AV><AI>0</AI><Nil /><PI>-1</PI><PC>-1</PC><T>Completed</T><SR>-1</SR><SD> </SD></PR></MS></Obj></Objs>
==> azure-arm: Querying the machine’s properties ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Managed OS Disk   : ‘/subscriptions/guid/resourceGroups/packer-Resource-Group-pq0mthtbtt/providers/Microsoft.Compute/disks/osdisk’
==> azure-arm: Powering off machine ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> ComputeName       : ‘pkrvmpq0mthtbtt’
==> azure-arm: Capturing image ...
==> azure-arm:  -> Compute ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
==> azure-arm:  -> Compute Name              : ‘pkrvmpq0mthtbtt’
==> azure-arm:  -> Compute Location          : ‘East US’
==> azure-arm:  -> Image ResourceGroupName   : ‘myResourceGroup’
==> azure-arm:  -> Image Name                : ‘myPackerImage’
==> azure-arm:  -> Image Location            : ‘eastus’
==> azure-arm: Deleting resource group ...
==> azure-arm:  -> ResourceGroupName : ‘packer-Resource-Group-pq0mthtbtt’
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


## <a name="create-a-vm-from-the-packer-image"></a>Virtuális gép létrehozása a csomagoló rendszerképből
Most létrehozhat egy virtuális gépet a rendszerképből a [New-AzVM](/powershell/module/az.compute/new-azvm)használatával. A támogató hálózati erőforrások akkor jönnek létre, ha még nem léteznek. Ha a rendszer kéri, adjon meg egy rendszergazdai felhasználónevet és jelszót, amelyet létre kell hozni a virtuális gépen. Az alábbi példa egy *myVM* nevű virtuális gépet hoz létre a *myPackerImage*:

```powershell
New-AzVm `
    -ResourceGroupName $rgName `
    -Name "myVM" `
    -Location $location `
    -VirtualNetworkName "myVnet" `
    -SubnetName "mySubnet" `
    -SecurityGroupName "myNetworkSecurityGroup" `
    -PublicIpAddressName "myPublicIpAddress" `
    -OpenPorts 80 `
    -Image "myPackerImage"
```

Ha virtuális gépeket szeretne létrehozni egy másik erőforráscsoporthoz vagy régióban, mint a csomagoló-rendszerkép, a rendszerkép neve helyett adja meg a rendszerkép AZONOSÍTÓját. A rendszerkép-azonosítót a [Get-AzImage](/powershell/module/az.compute/get-azimage)használatával szerezheti be.

A virtuális gép létrehozása néhány percet vesz igénybe a csomagoló rendszerképből.


## <a name="test-vm-and-webserver"></a>Virtuális gép és webkiszolgáló tesztelése
Szerezze be a virtuális gép nyilvános IP-címét a [Get-AzPublicIPAddress](/powershell/module/az.network/get-azpublicipaddress). A következő példa a korábban létrehozott *myPublicIP* IP-címét kéri le:

```powershell
Get-AzPublicIPAddress `
    -ResourceGroupName $rgName `
    -Name "myPublicIPAddress" | select "IpAddress"
```

Ha szeretné megtekinteni a virtuális gépet, amely tartalmazza az IIS telepítését a csomagoló-kihelyező szolgáltatásból, a műveletben adja meg a nyilvános IP-címet egy webböngészőben.

![Alapértelmezett IIS-webhely](./media/build-image-with-packer/iis.png) 


## <a name="next-steps"></a>Következő lépések
Az [Azure rendszerkép-készítővel](image-builder.md)meglévő csomagoló-kiépítési parancsfájlokat is használhat.
