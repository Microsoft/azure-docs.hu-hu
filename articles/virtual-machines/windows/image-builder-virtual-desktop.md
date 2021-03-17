---
title: Rendszerkép-készítő – Windows rendszerű virtuális asztali rendszerkép létrehozása
description: Hozzon létre egy Azure-beli virtuálisgép-rendszerképet a Windows virtuális asztalról az Azure rendszerkép-készítő használatával a PowerShellben.
author: danielsollondon
ms.author: danis
ms.reviewer: cynthn
ms.date: 01/27/2021
ms.topic: article
ms.service: virtual-machines-windows
ms.collection: windows
ms.subservice: imaging
ms.openlocfilehash: 01b253747791fc29abf4434bebfd85865099f9ee
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/16/2021
ms.locfileid: "103602018"
---
# <a name="create-a-windows-virtual-desktop-image-using-azure-vm-image-builder-and-powershell"></a>Windows rendszerű virtuális asztali rendszerkép létrehozása az Azure VM rendszerkép-készítő és a PowerShell használatával

Ez a cikk bemutatja, hogyan hozhat létre egy Windows rendszerű virtuális asztali rendszerképet ezekkel a testreszabásokkal:

* A [FsLogix](https://github.com/DeanCefola/Azure-WVD/blob/master/PowerShell/FSLogixSetup.ps1)telepítése.
* Windows rendszerű [virtuális asztali optimalizálási parancsfájl](https://github.com/The-Virtual-Desktop-Team/Virtual-Desktop-Optimization-Tool) futtatása a közösségi tárházból.
* Telepítse a [Microsoft Teams](https://docs.microsoft.com/azure/virtual-desktop/teams-on-wvd)programot.
* [Újraindítás](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-restart-customizer)
* [Windows Update](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-json?toc=%2Fazure%2Fvirtual-machines%2Fwindows%2Ftoc.json&bc=%2Fazure%2Fvirtual-machines%2Fwindows%2Fbreadcrumb%2Ftoc.json#windows-update-customizer) futtatása

Bemutatjuk, hogyan automatizálhatja ezt az Azure virtuálisgép-rendszerkép-készítővel, és hogyan terjesztheti a rendszerképet egy [megosztott](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)képkatalógusba, ahol más régiókba replikálhat, szabályozhatja a méretezést, és megoszthatja a rendszerképet a szervezeten belül és kívül is.


A rendszerkép-készítő konfiguráció üzembe helyezésének egyszerűbbé tétele érdekében a példa egy Azure Resource Manager sablont használ, amely a rendszerkép-készítő sablonnal van beágyazva. Ez számos más előnyt biztosít, például a változókat és a paraméterek bemeneteit. A paramétereket a parancssorból is átadhatja.

Ez a cikk egy másolási és beillesztési gyakorlat.

> [!NOTE]
> Az alkalmazások telepítéséhez szükséges parancsfájlok a [githubon](https://github.com/danielsollondon/azvmimagebuilder/tree/master/solutions/14_Building_Images_WVD)találhatók. Csak illusztrációs és tesztelési célokat szolgálnak, nem pedig éles számítási feladatokhoz. 

## <a name="tips-for-building-windows-images"></a>Tippek Windows-rendszerképek készítéséhez 

- VM-méret – az alapértelmezett virtuálisgép-méret a `Standard_D1_v2` , amely nem alkalmas a Windows számára. Használjon egy `Standard_D2_v2` vagy több.
- Ez a példa a [PowerShell-testreszabó parancsfájlokat](../linux/image-builder-json.md)használja. Ezeket a beállításokat kell használnia, vagy a Build nem válaszol.

    ```json
      "runElevated": true,
      "runAsSystem": true,
    ```

    Például:

    ```json
      {
          "type": "PowerShell",
          "name": "installFsLogix",
          "runElevated": true,
          "runAsSystem": true,
          "scriptUri": "https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/0_installConfFsLogix.ps1"
    ```
- A kód megjegyzése – a AIB-létrehozási napló (Testreszabás. log) rendkívül részletes, ha a parancsfájlokat a "Write-host" használatával adja meg, ezeket a rendszer elküldi a naplókba, és egyszerűbbé teszi a hibaelhárítást.

    ```PowerShell
     write-host 'AIB Customization: Starting OS Optimizations script'
    ```

- Kilépési kódok – a AIB minden parancsfájlból 0 kilépési kódot ad vissza. a nullától eltérő kilépési kódok miatt a AIB nem fogja tudni a testreszabást, és leállítja a buildet. Ha összetett parancsfájlokkal rendelkezik, adja hozzá a rendszerállapotot és a kilépési kódokat, ezek megjelennek a customization. log naplófájlban.

    ```PowerShell
     Write-Host "Exit code: " $LASTEXITCODE
    ```
- Teszt: tesztelje és tesztelje a kódot egy önálló virtuális gép előtt, és ügyeljen arra, hogy ne legyenek felhasználói kérések, a megfelelő jogosultságot használja.

- Hálózatkezelés – `Set-NetAdapterAdvancedProperty` . Ez az optimalizálási parancsfájlban van beállítva, de a AIB-Build nem sikerül, mert leválasztja a hálózatot, ez a Megjegyzés. Vizsgálat alatt áll.

## <a name="prerequisites"></a>Előfeltételek

Telepítenie kell a legújabb Azure PowerShell parancsmagokat, lásd [itt](https://docs.microsoft.com/powershell/azure/overview) a telepítés részleteit.

```PowerShell
# Register for Azure Image Builder Feature
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

Get-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages

# wait until RegistrationState is set to 'Registered'

# check you are registered for the providers, ensure RegistrationState is set to 'Registered'.
Get-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
Get-AzResourceProvider -ProviderNamespace Microsoft.Storage 
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault

# If they do not saw registered, run the commented out code below.

## Register-AzResourceProvider -ProviderNamespace Microsoft.VirtualMachineImages
## Register-AzResourceProvider -ProviderNamespace Microsoft.Storage
## Register-AzResourceProvider -ProviderNamespace Microsoft.Compute
## Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

## <a name="set-up-environment-and-variables"></a>Környezet és változók beállítása

```azurepowershell-interactive
# Step 1: Import module
Import-Module Az.Accounts

# Step 2: get existing context
$currentAzContext = Get-AzContext

# destination image resource group
$imageResourceGroup="wvdImageDemoRg"

# location (see possible locations in main docs)
$location="westus2"

# your subscription, this will get your current subscription
$subscriptionID=$currentAzContext.Subscription.Id

# image template name
$imageTemplateName="wvd10ImageTemplate01"

# distribution properties object name (runOutput), i.e. this gives you the properties of the managed image on completion
$runOutputName="sigOutput"

# create resource group
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="permissions-user-identity-and-role"></a>Engedélyek, felhasználói identitás és szerepkör 


 Hozzon létre egy felhasználói identitást.

```azurepowershell-interactive
# setup role def names, these need to be unique
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt
$idenityName="aibIdentity"+$timeInt

## Add AZ PS modules to support AzUserAssignedIdentity and Az AIB
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}

# create identity
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName

$idenityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).Id
$idenityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName).PrincipalId

```

Rendeljen engedélyeket az identitáshoz a rendszerképek terjesztéséhez. Ez a parancs letölti és frissíti a sablont a korábban megadott paraméterekkel.

```azurepowershell-interactive
$aibRoleImageCreationUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$aibRoleImageCreationPath = "aibRoleImageCreation.json"

# download config
Invoke-WebRequest -Uri $aibRoleImageCreationUrl -OutFile $aibRoleImageCreationPath -UseBasicParsing

((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $aibRoleImageCreationPath
((Get-Content -path $aibRoleImageCreationPath -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $aibRoleImageCreationPath

# create role definition
New-AzRoleDefinition -InputFile  ./aibRoleImageCreation.json

# grant role definition to image builder service principal
New-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
```

> [!NOTE] 
> Ha ezt a hibát látja: "New-AzRoleDefinition: a szerepkör-definíció túllépte a korlátot. Nem lehet több szerepkör-definíciót létrehozni. " a probléma megoldásához tekintse meg a következő cikket: https://docs.microsoft.com/azure/role-based-access-control/troubleshooting .



## <a name="create-the-shared-image-gallery"></a>A megosztott Képtár létrehozása 

Ha még nem rendelkezik megosztott rendszerkép-katalógussal, létre kell hoznia egyet.

```azurepowershell-interactive
$sigGalleryName= "myaibsig01"
$imageDefName ="win10wvd"

# create gallery
New-AzGallery -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup  -Location $location

# create gallery definition
New-AzGalleryImageDefinition -GalleryName $sigGalleryName -ResourceGroupName $imageResourceGroup -Location $location -Name $imageDefName -OsState generalized -OsType Windows -Publisher 'myCo' -Offer 'Windows' -Sku '10wvd'

```

## <a name="configure-the-image-template"></a>A rendszerkép-sablon konfigurálása

Ebben a példában egy sablon készen áll arra, hogy letöltse és frissítse a sablont a korábban megadott paraméterekkel, telepíti a FsLogix, az operációs rendszer optimalizálásait, a Microsoft Teams-t, és Windows Update a végén futtatja.

Ha megnyitja a sablont, amely a forrás tulajdonságban látható, akkor a használni kívánt rendszerkép jelenik meg, ebben a példában egy Win 10 több munkamenetet ábrázoló képet használ. 

### <a name="windows-10-images"></a>Windows 10 rendszerképek
Két fontos típust kell figyelembe vegye: többmunkamenetes és egy munkamenet.

A többmunkamenetes lemezképek a készletezett használatra szolgálnak. Íme egy példa a rendszerkép részleteire az Azure-ban:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "20h2-evd",
"version": "latest"
```

Az egyes munkamenet-lemezképek egyéni használatra szolgálnak. Íme egy példa a rendszerkép részleteire az Azure-ban:

```json
"publisher": "MicrosoftWindowsDesktop",
"offer": "Windows-10",
"sku": "19h2-ent",
"version": "latest"
```

Az elérhető Win10-lemezképeket is módosíthatja:

```azurepowershell-interactive
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsDesktop -Offer windows-10
```

## <a name="download-template-and-configure"></a>Sablon letöltése és konfigurálás

Most le kell töltenie a sablont, és konfigurálnia kell a használatára.

```azurepowershell-interactive
$templateUrl="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json"
$templateFilePath = "armTemplateWVD.json"

Invoke-WebRequest -Uri $templateUrl -OutFile $templateFilePath -UseBasicParsing

((Get-Content -path $templateFilePath -Raw) -replace '<subscriptionID>',$subscriptionID) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<rgName>',$imageResourceGroup) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<runOutputName>',$runOutputName) | Set-Content -Path $templateFilePath

((Get-Content -path $templateFilePath -Raw) -replace '<imageDefName>',$imageDefName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<sharedImageGalName>',$sigGalleryName) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<region1>',$location) | Set-Content -Path $templateFilePath
((Get-Content -path $templateFilePath -Raw) -replace '<imgBuilderId>',$idenityNameResourceId) | Set-Content -Path $templateFilePath

```

Nyugodtan tekintse meg a [sablont](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/14_Building_Images_WVD/armTemplateWVD.json), és minden kód megtekinthető.


## <a name="submit-the-template"></a>A sablon elküldése

A sablont el kell küldenie a szolgáltatásnak, ezzel letölti az összes függő összetevőt (például parancsfájlokat), érvényesítheti, ellenőrizheti az engedélyeket, és tárolhatja azokat az átmeneti erőforráscsoporthoz, az előre rögzített *IT_*.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName $imageResourceGroup -TemplateFile $templateFilePath -api-version "2020-02-14" -imageTemplateName $imageTemplateName -svclocation $location

# Optional - if you have any errors running the above, run:
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)
$getStatus.ProvisioningErrorCode 
$getStatus.ProvisioningErrorMessage
```
 
## <a name="build-the-image"></a>Rendszerkép létrehozása
```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName -NoWait
```

> [!NOTE]
> A parancs nem várja meg, amíg a rendszerkép-készítő szolgáltatás befejezi a rendszerkép összeállítását, az alábbi állapotot kérdezheti le.

```azurepowershell-interactive
$getStatus=$(Get-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName)

# this shows all the properties
$getStatus | Format-List -Property *

# these show the status the build
$getStatus.LastRunStatusRunState 
$getStatus.LastRunStatusMessage
$getStatus.LastRunStatusRunSubState
```
## <a name="create-a-vm"></a>Virtuális gép létrehozása
Most, hogy elkészült a létrehozás, létrehozhat egy virtuális gépet a rendszerképből, a példákat [itt](https://docs.microsoft.com/powershell/module/az.compute/new-azvm#examples)tekintheti meg.

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Először törölje az erőforráscsoport-sablont, ne csak törölje a teljes erőforráscsoportot, ellenkező esetben a AIB által használt átmeneti erőforráscsoport (*IT_*) nem lesz törölve.

Távolítsa el a rendszerkép sablonját.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name wvd10ImageTemplate
```

A szerepkör-hozzárendelés törlése.

```azurepowershell-interactive
Remove-AzRoleAssignment -ObjectId $idenityNamePrincipalId -RoleDefinitionName $imageRoleDefName -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## remove definitions
Remove-AzRoleDefinition -Name "$idenityNamePrincipalId" -Force -Scope "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"

## delete identity
Remove-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $idenityName -Force
```

Törölje az erőforráscsoportot.

```azurepowershell-interactive
Remove-AzResourceGroup $imageResourceGroup -Force
```

## <a name="next-steps"></a>Következő lépések

[A githubon](https://github.com/danielsollondon/azvmimagebuilder/tree/master/quickquickstarts)további példákat is kipróbálhat.
