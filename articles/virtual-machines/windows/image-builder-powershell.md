---
title: Windows rendszerű virtuális gép létrehozása az Azure rendszerkép-készítővel a PowerShell használatával
description: Hozzon létre egy Windows rendszerű virtuális gépet az Azure rendszerkép-készítő PowerShell-modullal.
author: cynthn
ms.author: cynthn
ms.date: 06/17/2020
ms.topic: how-to
ms.service: virtual-machines-windows
ms.subservice: imaging
ms.openlocfilehash: c8a5e1b1324ca49d8b540998a82ebf125b3c5364
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2020
ms.locfileid: "84975860"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder-using-powershell"></a>Előzetes verzió: Windows rendszerű virtuális gép létrehozása az Azure rendszerkép-készítővel a PowerShell használatával

Ez a cikk bemutatja, hogyan hozhat létre testreszabott Windows-rendszerképeket az Azure VM rendszerkép-készítő PowerShell-moduljának használatával.

> [!CAUTION]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el. Ezt az előzetes verziót szolgáltatói szerződés nélkül biztosítjuk. Éles számítási feladatokhoz nem ajánlott. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: a [Microsoft Azure előzetes verziójának kiegészítő használati feltételei](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="prerequisites"></a>Előfeltételek

Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt hozzon létre egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

Ha a PowerShell helyi használatát választja, akkor ehhez a cikkhez telepítenie kell az az PowerShell-modult, és csatlakoznia kell az Azure-fiókjához a [AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) parancsmag használatával. Az az PowerShell-modul telepítésével kapcsolatos további információkért lásd: [Install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps).

> [!IMPORTANT]
> Az az **. ImageBuilder** és az **az. ManagedServiceIdentity** PowerShell-modulok előzetes verzióban találhatók, ezeket külön kell telepítenie a (z) `Install-Module` paraméterrel rendelkező parancsmaggal `AllowPrerelease` . Amint ezek a PowerShell-modulok általánosan elérhetővé váltak, az a PowerShell-modul kiadásainak és natív módon elérhetővé válnak a Azure Cloud Shellon belül.

```azurepowershell-interactive
'Az.ImageBuilder', 'Az.ManagedServiceIdentity' | ForEach-Object {Install-Module -Name $_ -AllowPrerelease}
```

[!INCLUDE [cloud-shell-try-it](../../../includes/cloud-shell-try-it.md)]

Ha több Azure-előfizetéssel rendelkezik, válassza ki a megfelelő előfizetést, amelyben az erőforrásokat számlázni kell. Válasszon ki egy adott előfizetést a [set-AzContext](https://docs.microsoft.com/powershell/module/az.accounts/set-azcontext) parancsmag használatával.

```azurepowershell-interactive
Set-AzContext -SubscriptionId 00000000-0000-0000-0000-000000000000
```

### <a name="register-features"></a>Szolgáltatások regisztrálása

Ha első alkalommal használja az Azure rendszerkép-készítőt az előzetes verzióban, regisztrálja az új **VirtualMachineTemplatePreview** funkciót.

```azurepowershell-interactive
Register-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

A szolgáltatás regisztrációjának állapotát vizsgálja meg.

> [!NOTE]
> A **RegistrationState** a `Registering` Módosítás előtt több percig is eltarthat `Registered` . A folytatás előtt várjon, amíg az állapot **regisztrálva** lesz.

```azurepowershell-interactive
Get-AzProviderFeature -ProviderNamespace Microsoft.VirtualMachineImages -FeatureName VirtualMachineTemplatePreview
```

Regisztrálja az alábbi erőforrás-szolgáltatókat az Azure-előfizetéséhez való használatra, ha még nincsenek regisztrálva.

- Microsoft.Compute
- Microsoft. kulcstartó
- Microsoft.Storage
- Microsoft. VirtualMachineImages

```azurepowershell-interactive
Get-AzResourceProvider -ProviderNamespace Microsoft.Compute, Microsoft.KeyVault, Microsoft.Storage, Microsoft.VirtualMachineImages |
  Where-Object RegistrationState -ne Registered |
    Register-AzResourceProvider
```

## <a name="define-variables"></a>Változók meghatározása

Az adatok többször is használhatók. Hozzon létre változókat az információk tárolásához.

```azurepowershell-interactive
# Destination image resource group name
$imageResourceGroup = 'myWinImgBuilderRG'

# Azure region
$location = 'WestUS2'

# Name of the image to be created
$imageTemplateName = 'myWinImage'

# Distribution properties of the managed image upon completion
$runOutputName = 'myDistResults'
```

Hozzon létre egy változót az Azure-előfizetési AZONOSÍTÓhoz. Annak megerősítéséhez, hogy a `subscriptionID` változó tartalmazza-e az előfizetés-azonosítóját, a következő példában futtathatja a második sort.

```azurepowershell-interactive
# Your Azure Subscription ID
$subscriptionID = (Get-AzContext).Subscription.Id
Write-Output $subscriptionID
```

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Hozzon létre egy [Azure-erőforráscsoportot](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) a [New-AzResourceGroup](https://docs.microsoft.com/powershell/module/az.resources/new-azresourcegroup) parancsmag használatával. Az erőforráscsoport olyan logikai tároló, amelyben a rendszer csoportként helyezi üzembe és kezeli az Azure-erőforrásokat.

A következő példa létrehoz egy erőforráscsoportot a változóban `$imageResourceGroup` megadott régióban található változó alapján `$location` . Ez az erőforráscsoport a rendszerkép-konfigurációs sablon és a rendszerkép tárolására szolgál.

```azurepowershell-interactive
New-AzResourceGroup -Name $imageResourceGroup -Location $location
```

## <a name="create-user-identity-and-set-role-permissions"></a>Felhasználói identitás létrehozása és szerepkör engedélyeinek beállítása

Az alábbi példa használatával biztosíthatja, hogy az Azure Image Builder engedélyeket hozzon létre lemezképek létrehozásához a megadott erőforráscsoporthoz. Ezen engedély nélkül a rendszerkép-létrehozási folyamat nem fejeződik be sikeresen.

Hozzon létre változókat a szerepkör-definíció és az identitás neve számára. Ezeknek az értékeknek egyedinek kell lenniük.

```azurepowershell-interactive
[int]$timeInt = $(Get-Date -UFormat '%s')
$imageRoleDefName = "Azure Image Builder Image Def $timeInt"
$identityName = "myIdentity$timeInt"
```

Hozzon létre egy felhasználói identitást.

```azurepowershell-interactive
New-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName
```

Tárolja az identitás-erőforrást és a résztvevő azonosítóit a változókban.

```azurepowershell-interactive
$identityNameResourceId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId = (Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId
```

### <a name="assign-permissions-for-identity-to-distribute-images"></a>Az identitáshoz tartozó engedélyek kiosztása képek terjesztéséhez

Töltse le a. JSON konfigurációs fájlt, és módosítsa a cikkben meghatározott beállítások alapján.

```azurepowershell-interactive
$myRoleImageCreationUrl = 'https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json'
$myRoleImageCreationPath = "$env:TEMP\myRoleImageCreation.json"

Invoke-WebRequest -Uri $myRoleImageCreationUrl -OutFile $myRoleImageCreationPath -UseBasicParsing

$Content = Get-Content -Path $myRoleImageCreationPath -Raw
$Content = $Content -replace '<subscriptionID>', $subscriptionID
$Content = $Content -replace '<rgName>', $imageResourceGroup
$Content = $Content -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName
$Content | Out-File -FilePath $myRoleImageCreationPath -Force
```

Hozza létre a szerepkör-definíciót.

```azurepowershell-interactive
New-AzRoleDefinition -InputFile $myRoleImageCreationPath
```

Adja meg a szerepkör-definíciót a rendszerkép-készítő egyszerű szolgáltatásnév számára.

```azurepowershell-interactive
$RoleAssignParams = @{
  ObjectId = $identityNamePrincipalId
  RoleDefinitionName = $imageRoleDefName
  Scope = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup"
}
New-AzRoleAssignment @RoleAssignParams
```

> [!NOTE]
> Ha a következő hibaüzenetet kapja: "_New-AzRoleDefinition: a szerepkör-definíció túllépte a korlátot. Nem hozhatók létre további szerepkör-definíciók._", lásd: az [Azure RBAC hibáinak megoldása](https://docs.microsoft.com/azure/role-based-access-control/troubleshooting).

## <a name="create-a-shared-image-gallery"></a>Shared Image Gallery létrehozása

Hozza létre a gyűjteményt.

```azurepowershell-interactive
$myGalleryName = 'myImageGallery'
$imageDefName = 'winSvrImages'

New-AzGallery -GalleryName $myGalleryName -ResourceGroupName $imageResourceGroup -Location $location
```

Hozzon létre egy gyűjtemény definícióját.

```azurepowershell-interactive
$GalleryParams = @{
  GalleryName = $myGalleryName
  ResourceGroupName = $imageResourceGroup
  Location = $location
  Name = $imageDefName
  OsState = 'generalized'
  OsType = 'Windows'
  Publisher = 'myCo'
  Offer = 'Windows'
  Sku = 'Win2019'
}
New-AzGalleryImageDefinition @GalleryParams
```

## <a name="create-an-image"></a>Rendszerkép létrehozása

Hozzon létre egy Azure rendszerkép-készítő forrásoldali objektumot. Tekintse [meg a Windows rendszerű virtuális gépek rendszerképeinek megkeresése az Azure Marketplace-](https://docs.microsoft.com/azure/virtual-machines/windows/cli-ps-findimage) en az érvényes paraméterérték Azure PowerShell.

```azurepowershell-interactive
$SrcObjParams = @{
  SourceTypePlatformImage = $true
  Publisher = 'MicrosoftWindowsServer'
  Offer = 'WindowsServer'
  Sku = '2019-Datacenter'
  Version = 'latest'
}
$srcPlatform = New-AzImageBuilderSourceObject @SrcObjParams
```

Hozzon létre egy Azure rendszerkép-készítő terjesztői objektumot.

```azurepowershell-interactive
$disObjParams = @{
  SharedImageDistributor = $true
  ArtifactTag = @{tag='dis-share'}
  GalleryImageId = "/subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup/providers/Microsoft.Compute/galleries/$myGalleryName/images/$imageDefName"
  ReplicationRegion = $location
  RunOutputName = $runOutputName
  ExcludeFromLatest = $false
}
$disSharedImg = New-AzImageBuilderDistributorObject @disObjParams
```

Hozzon létre egy Azure rendszerkép-készítő testreszabási objektumot.

```azurepowershell-interactive
$ImgCustomParams = @{
  PowerShellCustomizer = $true
  CustomizerName = 'settingUpMgmtAgtPath'
  RunElevated = $false
  Inline = @("mkdir c:\\buildActions", "echo Azure-Image-Builder-Was-Here  > c:\\buildActions\\buildActionsOutput.txt")
}
$Customizer = New-AzImageBuilderCustomizerObject @ImgCustomParams
```

Hozzon létre egy Azure rendszerkép-szerkesztői sablont.

```azurepowershell-interactive
$ImgTemplateParams = @{
  ImageTemplateName = $imageTemplateName
  ResourceGroupName = $imageResourceGroup
  Source = $srcPlatform
  Distribute = $disSharedImg
  Customize = $Customizer
  Location = $location
  UserAssignedIdentityId = $identityNameResourceId
}
New-AzImageBuilderTemplate @ImgTemplateParams
```

Ha elkészült, a rendszer egy üzenetet ad vissza, és létrehoz egy rendszerkép-készítő konfigurációs sablont a alkalmazásban `$imageResourceGroup` .

Annak megállapításához, hogy a sablon létrehozási folyamata sikeres volt-e, a következő példát használhatja.

```azurepowershell-interactive
Get-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup |
  Select-Object -Property Name, LastRunStatusRunState, LastRunStatusMessage, ProvisioningState
```

A háttérben a rendszerkép-készítő létrehoz egy átmeneti erőforráscsoportot is az előfizetésében. Ez az erőforráscsoport a rendszerkép létrehozásához használatos. Formátuma: `IT_<DestinationResourceGroup>_<TemplateName>` .

> [!WARNING]
> Ne törölje közvetlenül az előkészítési erőforráscsoportot. Törölje a rendszerkép-sablon összetevőt, ez az átmeneti erőforráscsoport törlését eredményezi.

Ha a szolgáltatás hibát jelez a rendszerkép-konfigurációs sablon beküldésekor:

- Lásd: az [Azure VM rendszerkép-build (AIB) hibáinak elhárítása](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#template-submission-errors--troubleshooting).
- Az újrapróbálkozás előtt törölje a sablont a következő példa használatával.

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup
```

## <a name="start-the-image-build"></a>A rendszerkép létrehozásának elindítása

Küldje be a rendszerkép konfigurációját a virtuálisgép-rendszerkép-szerkesztő szolgáltatásba.

```azurepowershell-interactive
Start-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

Várjon, amíg a rendszerkép-létrehozási folyamat befejeződik. Ez a lépés akár egy órát is igénybe vehet.

Ha hibát tapasztal, tekintse át az [Azure VM-rendszerkép build (AIB) hibáinak elhárítását ismertető témakört](https://github.com/danielsollondon/azvmimagebuilder/blob/master/troubleshootingaib.md#image-build-errors--troubleshooting).

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Tárolja a virtuális gép bejelentkezési hitelesítő adatait egy változóban. A jelszónak összetettnak kell lennie.

```azurepowershell-interactive
$Cred = Get-Credential
```

Hozza létre a virtuális gépet a létrehozott rendszerkép használatával.

```azurepowershell-interactive
$ArtifactId = (Get-AzImageBuilderRunOutput -ImageTemplateName $imageTemplateName -ResourceGroupName $imageResourceGroup).ArtifactId

New-AzVM -ResourceGroupName $imageResourceGroup -Image $ArtifactId -Name myWinVM01 -Credential $Cred
```

## <a name="verify-the-customization"></a>A Testreszabás ellenőrzése

Hozzon létre egy Távoli asztal-csatlakozást a virtuális géphez a virtuális gép létrehozásakor beállított Felhasználónév és jelszó használatával. A virtuális gépen nyissa meg a PowerShellt, és futtassa `Get-Content` az alábbi példában látható módon:

```azurepowershell-interactive
Get-Content -Path C:\buildActions\buildActionsOutput.txt
```

A kimenetnek a rendszerkép-testreszabási folyamat során létrehozott fájl tartalma alapján kell megjelennie.

```Output
Azure-Image-Builder-Was-Here
```

## <a name="clean-up-resources"></a>Erőforrások felszabadítása

Ha a cikkben létrehozott erőforrások nem szükségesek, az alábbi példák futtatásával törölheti őket.

### <a name="delete-the-image-builder-template"></a>A rendszerkép-szerkesztő sablonjának törlése

```azurepowershell-interactive
Remove-AzImageBuilderTemplate -ResourceGroupName $imageResourceGroup -Name $imageTemplateName
```

### <a name="delete-the-image-resource-group"></a>A rendszerkép-erőforráscsoport törlése

> [!CAUTION]
> A következő példa törli a megadott erőforráscsoportot és a benne található összes erőforrást.
> Ha a cikk hatókörén kívüli erőforrások szerepelnek a megadott erőforráscsoporthoz, akkor azokat is törli a rendszer.

```azurepowershell-interactive
Remove-AzResourceGroup -Name $imageResourceGroup
```

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a cikkben használt. JSON fájl összetevőiről, tekintse meg a [rendszerkép-szerkesztői sablon referenciáját](../linux/image-builder-json.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
