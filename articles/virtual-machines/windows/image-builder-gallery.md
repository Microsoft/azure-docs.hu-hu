---
title: Az Azure rendszerkép-készítő használata Windows rendszerű virtuális gépekhez készült rendszerkép-katalógussal (előzetes verzió)
description: Hozzon létre Azure Shared Gallery-rendszerképeket az Azure rendszerkép-készítő és a Azure PowerShell használatával.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: e8caf9f742217161c60ce90351989999f18adabb
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101694087"
---
# <a name="preview-create-a-windows-image-and-distribute-it-to-a-shared-image-gallery"></a>Előzetes verzió: Windows-rendszerkép létrehozása és terjesztése megosztott képgyűjteménybe 

Ebből a cikkből megtudhatja, hogyan használhatja az Azure-rendszerkép-szerkesztőt, Azure PowerShell és hogyan hozhat létre egy rendszerkép-verziót egy [megosztott rendszerkép](../shared-image-galleries.md)-katalógusban, majd globálisan terjesztheti a rendszerképet. Ezt az [Azure CLI](../linux/image-builder-gallery.md)használatával is elvégezheti.

A rendszerkép konfigurálásához egy. JSON sablont fogunk használni. Az általunk használt. JSON fájl a következő: [armTemplateWinSIG.js](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json). A sablon helyi verziójának letöltése és szerkesztése folyamatban van, ezért ez a cikk a helyi PowerShell-munkamenet használatával íródik.

A rendszerkép megosztott képtárba való terjesztéséhez a sablon a [sharedImage](../linux/image-builder-json.md#distribute-sharedimage) használja a `distribute` sablon szakaszának értékeként.

Az Azure rendszerkép-készítő automatikusan futtatja a Sysprep programot a rendszerkép általánosítása érdekében, ez egy általános Sysprep-parancs, amelyet szükség esetén [felül lehet bírálni](../linux/image-builder-troubleshoot.md#vms-created-from-aib-images-do-not-create-successfully) . 

Vegye figyelembe, hogy a réteg testreszabása hányszor történik. A Sysprep parancsot akár 8 alkalommal is futtathatja egyetlen Windows-rendszerképben. A Sysprep 8 alkalommal történő futtatása után újra létre kell hoznia a Windows-rendszerképet. További információ: [korlátozások a Sysprep futtatásának](/windows-hardware/manufacture/desktop/sysprep--generalize--a-windows-installation#limits-on-how-many-times-you-can-run-sysprep)hányszor. 

> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="register-the-features"></a>A szolgáltatások regisztrálása
Ha az előzetes verzióban szeretné használni az Azure képszerkesztőt, regisztrálnia kell az új szolgáltatást.

```powershell
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

A szolgáltatás regisztrációjának állapotát vizsgálja meg.

```powershell
Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

Várjon, `RegistrationState` amíg `Registered` meg nem történik a következő lépésre való áttérés.

Keresse meg a szolgáltató regisztrációját. Győződjön meg róla, hogy minden visszaadott érték `Registered` .

```powershell
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage | Format-table -Property ResourceTypes,RegistrationState 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | Format-table -Property ResourceTypes,RegistrationState
Get-AzResourceProvider -ProviderNamespace Microsoft.Network | Format-table -Property ResourceTypes,RegistrationState
```

Ha nem adnak vissza `Registered` , a következő paranccsal regisztrálhatók a szolgáltatók:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
Register-AzResourceProvider -ProviderNamespace Microsoft.Network
```

PowerShell-modulok telepítése:
```powerShell
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

## <a name="create-variables"></a>Változók létrehozása

Többször is fogjuk használni az adatokat, így az adatok tárolására néhány változót fogunk létrehozni. Cserélje le a változókat, például a és a értékeket a `username` `vmpassword` saját adataival.

```powershell
# Get existing context
$currentAzContext = Get-AzContext

# Get your current subscription ID. 
$subscriptionID=$currentAzContext.Subscription.Id

# Destination image resource group
$imageResourceGroup="aibwinsig"

# Location
$location="westus"

# Image distribution metadata reference name
$runOutputName="aibCustWinManImg02ro"

# Image template name
$imageTemplateName="helloImageTemplateWin02ps"

# Distribution properties object name (runOutput).
# This gives you the properties of the managed image on completion.
$runOutputName="winclientR01"

# Create a resource group for Image Template and Shared Image Gallery
New-AzResourceGroup `
   -Name $imageResourceGroup `
   -Location $location
```


## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Felhasználó által hozzárendelt identitás létrehozása és engedélyek beállítása az erőforráscsoporthoz
A rendszerkép-szerkesztő a megadott [felhasználói identitást](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md) használja a rendszerkép az Azure Shared rendszerkép-katalógusba (SIG) való behelyezéséhez. Ebben a példában egy olyan Azure-szerepkör-definíciót hoz létre, amely részletes műveleteket hajt végre a rendszerképnek a SIG-ba való terjesztéséhez. A szerepkör-definíció ezután a felhasználó-identitáshoz lesz rendelve.

```powershell
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$identityName="aibIdentity"+$timeInt

## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName

$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```


### <a name="assign-permissions-for-identity-to-distribute-images"></a>Az identitáshoz tartozó engedélyek kiosztása képek terjesztéséhez

Ez a parancs letölt egy Azure szerepkör-definíciós sablont, és frissíti a sablont a korábban megadott paraméterekkel.

```powershell
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

### NOTE: If you see this error: 'New-AzRoleDefinition: Role definition limit exceeded. No more role definitions can be created.' See this article to resolve:
https://docs.microsoft.com/azure/role-based-access-control/troubleshooting
```


## <a name="create-the-shared-image-gallery"></a>A megosztott Képtár létrehozása

Ha közös rendszerkép-katalógussal szeretné használni a képszerkesztőt, rendelkeznie kell egy meglévő képtárat és képdefiníciót tartalmazó képpel. A rendszerkép-szerkesztő nem hozza létre az Ön számára a rendszerkép-gyűjteményt és a rendszerkép definícióját.

Ha még nem rendelkezik a használni kívánt gyűjtemény-és képdefinícióval, először hozza létre őket. Először hozzon létre egy képtárat.

```powershell
# Image gallery name
$sigGalleryName= "myIBSIG"

# Image definition name
$imageDefName ="winSvrimage"

# additional replication region
$replRegion2="eastus"

# Create the gallery
New-AzGallery `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup  `
   -Location $location

# Create the image definition
New-AzGalleryImageDefinition `
   -GalleryName $sigGalleryName `
   -ResourceGroupName $imageResourceGroup `
   -Location $location `
   -Name $imageDefName `
   -OsState generalized `
   -OsType Windows `
   -Publisher 'myCompany' `
   -Offer 'WindowsServer' `
   -Sku 'WinSrv2019'
```



## <a name="download-and-configure-the-template"></a>A sablon letöltése és konfigurálása

Töltse le a. JSON sablont, és konfigurálja a változókkal.

```powershell

$templateFilePath = "armTemplateWinSIG.json"

Invoke-WebRequest `
   -Uri "https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Win_Shared_Image_Gallery_Image/armTemplateWinSIG.json" `
   -OutFile $templateFilePath `
   -UseBasicParsing

(Get-Content -path $templateFilePath -Raw ) `
   -replace '<subscriptionID>',$subscriptionID | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<rgName>',$imageResourceGroup | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<runOutputName>',$runOutputName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<imageDefName>',$imageDefName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<sharedImageGalName>',$sigGalleryName | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region1>',$location | Set-Content -Path $templateFilePath
(Get-Content -path $templateFilePath -Raw ) `
   -replace '<region2>',$replRegion2 | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$identityNameResourceId) | Set-Content -Path $templateFilePath
```


## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

A sablont el kell küldenie a szolgáltatásnak, ezzel letölti az összes függő összetevőt, például a parancsfájlokat, és tárolja azokat az átmeneti erőforráscsoporthoz, a *IT_* előtaggal.

```powershell
New-AzResourceGroupDeployment `
   -ResourceGroupName $imageResourceGroup `
   -TemplateFile $templateFilePath `
   -apiversion "2020-02-14" `
   -imageTemplateName $imageTemplateName `
   -svclocation $location
```

A "Run" meghívásához szükséges rendszerkép létrehozásához a sablonon.

```powershell
Invoke-AzResourceAction `
   -ResourceName $imageTemplateName `
   -ResourceGroupName $imageResourceGroup `
   -ResourceType Microsoft.VirtualMachineImages/imageTemplates `
   -ApiVersion "2020-02-14" `
   -Action Run
```

A rendszerkép létrehozása és replikálása mindkét régióban eltarthat egy ideig. Várjon, amíg ez a rész be nem fejeződik a virtuális gép létrehozásához való továbblépés előtt.

A rendszerkép-Build állapotának automatizálására vonatkozó beállításokkal kapcsolatos további információkért lásd a [readme] című témakört.
```powershell
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozzon létre egy virtuális gépet az Azure-rendszerkép-szerkesztő által létrehozott rendszerkép-verzióból.

Szerezze be a létrehozott rendszerkép-verziót.
```powershell
$imageVersion = Get-AzGalleryImageVersion `
   -ResourceGroupName $imageResourceGroup `
   -GalleryName $sigGalleryName `
   -GalleryImageDefinitionName $imageDefName
```

Hozza létre a virtuális gépet a második régióban, melyet a rendszerkép replikált.

```powershell
$vmResourceGroup = "myResourceGroup"
$vmName = "myVMfromImage"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzResourceGroup -Name $vmResourceGroup -Location $replRegion2

# Network pieces
$subnetConfig = New-AzVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24
$vnet = New-AzVirtualNetwork -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig
$pip = New-AzPublicIpAddress -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4
$nsgRuleRDP = New-AzNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow
$nsg = New-AzNetworkSecurityGroup -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP
$nic = New-AzNetworkInterface -Name myNic -ResourceGroupName $vmResourceGroup -Location $replRegion2 `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

# Create a virtual machine configuration using $imageVersion.Id to specify the shared image
$vmConfig = New-AzVMConfig -VMName $vmName -VMSize Standard_D1_v2 | `
Set-AzVMOperatingSystem -Windows -ComputerName $vmName -Credential $cred | `
Set-AzVMSourceImage -Id $imageVersion.Id | `
Add-AzVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $vmResourceGroup -Location $replRegion2 -VM $vmConfig
```

## <a name="verify-the-customization"></a>A Testreszabás ellenőrzése
Hozzon létre egy Távoli asztal-csatlakozást a virtuális géphez a virtuális gép létrehozásakor beállított Felhasználónév és jelszó használatával. A virtuális gépen nyisson meg egy parancssort, és írja be a következőt:

```console
dir c:\
```

Ekkor megjelenik egy nevű könyvtár `buildActions` , amely a rendszerkép testreszabása során jött létre.


## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha most újra testre szeretné szabni a rendszerkép verzióját, hogy ugyanazon rendszerkép új verzióját hozza létre, **hagyja ki ezt a lépést** , és folytassa az [Azure rendszerkép-készítő használatával egy másik rendszerkép-verzió létrehozásához](image-builder-gallery-update-image-version.md).


Ezzel törli a létrehozott rendszerképet, valamint az összes többi erőforrás-fájlt is. Az erőforrások törlése előtt ellenőrizze, hogy befejeződött-e az üzemelő példány.

Először törölje az erőforráscsoport-sablont, ellenkező esetben a AIB által használt átmeneti erőforráscsoport (*IT_*) nem lesz törölve.

A Képsablon ResourceID beolvasása. 

```powerShell
$resTemplateId = Get-AzResource -ResourceName $imageTemplateName -ResourceGroupName $imageResourceGroup -ResourceType Microsoft.VirtualMachineImages/imageTemplates -ApiVersion "2020-02-14"
```

Sablon törlése.

```powerShell
Remove-AzResource -ResourceId $resTemplateId.ResourceId -Force
```

Szerepkör-hozzárendelés törlése

```powerShell
Remove-AzRoleAssignment -ObjectId $identityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

definíciók eltávolítása

```powerShell
Remove-AzRoleDefinition -Name "$identityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

identitás törlése

```powerShell
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName -Force
```

törölje az erőforráscsoportot.

```powerShell
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

Ha szeretné megtudni, hogyan frissítheti a létrehozott rendszerkép-verziót, tekintse meg az [Azure rendszerkép-készítő használata egy másik rendszerkép-verzió létrehozásához](image-builder-gallery-update-image-version.md)című témakört.
