---
title: Az Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása a PowerShell használatával
description: Az Azure VM rendszerkép-készítő szolgáltatás követelményeinek konfigurálása, beleértve az engedélyeket és a jogosultságokat a PowerShell használatával
author: danielsollondon
ms.author: danis
ms.date: 05/06/2020
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: b476945677e10c4985d9e16689b109468c9f7c59
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101676191"
---
# <a name="configure-azure-image-builder-service-permissions-using-powershell"></a>Az Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása a PowerShell használatával

Az Azure rendszerkép-készítő szolgáltatásnak a rendszerkép létrehozása előtt meg kell adni az engedélyek és a jogosultságok konfigurációját. A következő részek részletesen ismertetik a lehetséges forgatókönyvek konfigurálásának módját a PowerShell használatával.

> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="register-the-features"></a>A szolgáltatások regisztrálása

Először regisztrálnia kell az Azure rendszerkép-szerkesztő szolgáltatásban. A regisztráció a szolgáltatás számára engedélyezi az előkészítési erőforráscsoport létrehozását, kezelését és törlését. A szolgáltatás emellett jogosultságokkal is rendelkezik a rendszerkép létrehozásához szükséges csoport erőforrásainak hozzáadásához.

```powershell-interactive
Register-AzProviderFeature -FeatureName VirtualMachineTemplatePreview -ProviderNamespace Microsoft.VirtualMachineImages
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Azure-felhasználó által hozzárendelt felügyelt identitás létrehozása

Az Azure rendszerkép-szerkesztőhöz [Azure-felhasználó által hozzárendelt felügyelt identitást](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)kell létrehoznia. Az Azure Image Builder a felhasználó által hozzárendelt felügyelt identitást használja a képek olvasásához, a képek írásához és az Azure Storage-fiókok eléréséhez. Az identitás engedélyt ad az előfizetésében meghatározott műveletek elvégzéséhez.

> [!NOTE]
> Korábban az Azure rendszerkép-készítő az Azure rendszerkép-készítő egyszerű szolgáltatásnevet (SPN) használta a rendszerkép-erőforráscsoportok engedélyeinek megadásához. Az egyszerű szolgáltatásnév használata elavulttá válik. Használja helyette a felhasználó által hozzárendelt felügyelt identitást.

Az alábbi példa bemutatja, hogyan hozhat létre egy Azure-felhasználóhoz rendelt felügyelt identitást. Cserélje le a helyőrző beállításait a változók beállításához.

| Beállítás | Leírás |
|---------|-------------|
| \<Resource group\> | Az erőforráscsoport, amelyben létre kell hozni a felhasználó által hozzárendelt felügyelt identitást. |

```powershell-interactive
## Add AZ PS module to support AzUserAssignedIdentity
Install-Module -Name Az.ManagedServiceIdentity

$parameters = @{
    Name = 'aibIdentity'
    ResourceGroupName = '<Resource group>'
}
# create identity
New-AzUserAssignedIdentity @parameters
```

Az Azure felhasználó által hozzárendelt identitásokkal kapcsolatos további információkért tekintse meg a személyazonosság létrehozásáról szóló [Azure felhasználó által hozzárendelt felügyelt identitás](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md) dokumentációját.

## <a name="allow-image-builder-to-distribute-images"></a>Lemezképek terjesztésének engedélyezése a képszerkesztő számára

Ahhoz, hogy az Azure Image Builder lemezképeket terjesszen (felügyelt lemezképek/megosztott képgyűjtemény), az Azure Image Builder szolgáltatásnak engedélyezettnek kell lennie a lemezképek ezen erőforráscsoportokba való beadásához. A szükséges engedélyek megadásához létre kell hoznia egy felhasználó által hozzárendelt felügyelt identitást, és biztosítania kell az IT-jogosultságokat azon az erőforráscsoporton, ahol a rendszerkép épül. Az Azure rendszerkép-készítőnek **nincs** engedélye az erőforrásokhoz való hozzáféréshez az előfizetésben található más erőforráscsoportok erőforrásaihoz. Olyan explicit műveleteket kell elvégeznie, amelyek lehetővé teszik a hozzáférést, hogy elkerülje a buildek meghibásodását.

A lemezképek terjesztéséhez nem kell megadnia a felhasználó által hozzárendelt felügyelt identitás közreműködői jogosultságait az erőforráscsoporthoz. A felhasználó által hozzárendelt felügyelt identitásnak azonban a következő Azure `Actions` -engedélyekkel kell rendelkeznie a terjesztési erőforráscsoporthoz:

```Actions
Microsoft.Compute/images/write
Microsoft.Compute/images/read
Microsoft.Compute/images/delete
```

Ha megosztott képtárat oszt ki, a következőkre is szüksége lesz:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
Microsoft.Compute/galleries/images/versions/write
```

## <a name="permission-to-customize-existing-images"></a>Engedély a meglévő lemezképek testreszabásához

Ahhoz, hogy az Azure Image Builder lemezképeket hozzon létre a forrásként szolgáló egyéni rendszerképekből (felügyelt lemezképek/megosztott képgyűjtemény), az Azure Image Builder szolgáltatásnak engedélyezni kell a lemezképek ezen erőforráscsoportokba való beolvasását. A szükséges engedélyek megadásához létre kell hoznia egy felhasználó által hozzárendelt felügyelt identitást, és biztosítania kell az IT-jogosultságokat azon az erőforráscsoporton, ahol a rendszerkép található.

Létrehozás meglévő egyéni rendszerképből:

```Actions
Microsoft.Compute/galleries/read
```

Build egy meglévő megosztott rendszerkép-katalógusból:

```Actions
Microsoft.Compute/galleries/read
Microsoft.Compute/galleries/images/read
Microsoft.Compute/galleries/images/versions/read
```

## <a name="permission-to-customize-images-on-your-vnets"></a>Engedélyek a virtuális hálózatok található rendszerképek testreszabásához

Az Azure-rendszerkép-szerkesztő képes az előfizetésében meglévő VNET üzembe helyezésére és használatára, így lehetővé téve a testreszabott erőforrások elérését.

Nem kell megadnia a felhasználó által hozzárendelt felügyelt identitás közreműködői jogosultságait az erőforráscsoporthoz, hogy a virtuális gépet egy meglévő VNET telepítse. A felhasználó által hozzárendelt felügyelt identitásnak azonban a következő Azure-engedélyekkel kell rendelkeznie a `Actions` VNET erőforráscsoporthoz:

```Actions
Microsoft.Network/virtualNetworks/read
Microsoft.Network/virtualNetworks/subnets/join/action
```

## <a name="create-an-azure-role-definition"></a>Azure-szerepkör definíciójának létrehozása

Az alábbi példák az előző szakaszokban leírt műveletekben létrehoznak egy Azure-szerepkör-definíciót. A példák az erőforráscsoport szintjén lesznek alkalmazva. Értékelje ki és tesztelje, hogy a példák elég részletességgel rendelkeznek-e az Ön igényeinek megfelelően. Előfordulhat, hogy egy adott megosztott képgyűjteményre kell szűkíteni.

A Képműveletek lehetővé teszik az olvasást és az írást. Döntse el, hogy mi a megfelelő a környezet számára. Létrehozhat például egy olyan szerepkört, amely lehetővé teszi, hogy az Azure Image Builder képeket Olvasson az erőforráscsoport *példája – RG-1* és képek írása az erőforráscsoport *példája – RG-2*.

### <a name="custom-image-azure-role-example"></a>Példa egyéni rendszerképre Azure-szerepkör

Az alábbi példa létrehoz egy Azure-szerepkört a forrásként szolgáló egyéni rendszerkép használatára és terjesztésére. Ezután adja meg az egyéni szerepkört a felhasználó által hozzárendelt felügyelt identitáshoz az Azure rendszerkép-szerkesztőben.

A példában szereplő értékek cseréjének egyszerűbbé tételéhez először állítsa be a következő változókat. Cserélje le a helyőrző beállításait a változók beállításához.

| Beállítás | Leírás |
|---------|-------------|
| \<Subscription ID\> | Az Azure-előfizetés azonosítója |
| \<Resource group\> | Az egyéni rendszerképhez tartozó erőforráscsoport |

```powershell-interactive
$sub_id = "<Subscription ID>"
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
$imageResourceGroup = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json"
$role_definition="aibRoleImageCreation.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
$timeInt=$(get-date -UFormat "%s")
$imageRoleDefName="Azure Image Builder Image Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<rgName>', $imageResourceGroup) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Image Creation Role', $imageRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleImageCreation.json description file. 
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $imageRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $imageResourceGroup
}

New-AzRoleAssignment @parameters
```

### <a name="existing-vnet-azure-role-example"></a>Meglévő VNET Azure-szerepkör – példa

Az alábbi példa egy Azure-szerepkört hoz létre egy meglévő VNET-rendszerkép használatához és terjesztéséhez. Ezután adja meg az egyéni szerepkört a felhasználó által hozzárendelt felügyelt identitáshoz az Azure rendszerkép-szerkesztőben.

A példában szereplő értékek cseréjének egyszerűbbé tételéhez először állítsa be a következő változókat. Cserélje le a helyőrző beállításait a változók beállításához.

| Beállítás | Leírás |
|---------|-------------|
| \<Subscription ID\> | Az Azure-előfizetés azonosítója |
| \<Resource group\> | VNET erőforráscsoport |

```powershell-interactive
$sub_id = "<Subscription ID>"
$res_group = "<Resource group>"
$identityName = "aibIdentity"

# Use a web request to download the sample JSON description
$sample_uri="https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json"
$role_definition="aibRoleNetworking.json"

Invoke-WebRequest -Uri $sample_uri -Outfile $role_definition -UseBasicParsing

# Create a unique role name to avoid clashes in the same AAD domain
$timeInt=$(get-date -UFormat "%s")
$networkRoleDefName="Azure Image Builder Network Def"+$timeInt

# Update the JSON definition placeholders with variable values
((Get-Content -path $role_definition -Raw) -replace '<subscriptionID>',$sub_id) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace '<vnetRgName>', $res_group) | Set-Content -Path $role_definition
((Get-Content -path $role_definition -Raw) -replace 'Azure Image Builder Service Networking Role',$networkRoleDefName) | Set-Content -Path $role_definition

# Create a custom role from the aibRoleNetworking.json description file
New-AzRoleDefinition -InputFile $role_definition

# Get the user-identity properties
$identityNameResourceId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).Id
$identityNamePrincipalId=$(Get-AzUserAssignedIdentity -ResourceGroupName $imageResourceGroup -Name $identityName).PrincipalId

# Assign the custom role to the user-assigned managed identity for Azure Image Builder
$parameters = @{
    ObjectId = $identityNamePrincipalId
    RoleDefinitionName = $networkRoleDefName
    Scope = '/subscriptions/' + $sub_id + '/resourceGroups/' + $res_group
}

New-AzRoleAssignment @parameters
```

## <a name="next-steps"></a>Következő lépések

További információ: az [Azure rendszerkép-készítő áttekintése](../image-builder-overview.md).