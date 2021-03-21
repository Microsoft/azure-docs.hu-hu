---
title: Windows rendszerű virtuális gép létrehozása az Azure rendszerkép-készítővel (előzetes verzió)
description: Hozzon létre egy Windows rendszerű virtuális gépet az Azure rendszerkép-szerkesztővel.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: 918cee723bfde69d08532aee6fe4f395dbddb4ee
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "101695447"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Előzetes verzió: Windows rendszerű virtuális gép létrehozása az Azure rendszerkép-készítővel

Ebből a cikkből megtudhatja, hogyan hozhat létre testreszabott Windows-rendszerképeket az Azure VM rendszerkép-készítő használatával. A cikkben szereplő példa [testreszabók](../linux/image-builder-json.md#properties-customize) használatával testreszabja a rendszerképet:
- PowerShell (ScriptUri) – [PowerShell-parancsfájl](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)letöltése és futtatása.
- Windows újraindítása – újraindítja a virtuális gépet.
- PowerShell (beágyazott) – adott parancs futtatása. Ebben a példában egy könyvtárat hoz létre a virtuális gépen a használatával `mkdir c:\\buildActions` .
- Fájl – másolja a fájlt a GitHubról a virtuális gépre. Ez a példa a [index.MD](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) másolja a `c:\buildArtifacts\index.html` virtuális gépre.
- buildTimeoutInMinutes – növelje a kiépítési időt, amely lehetővé teszi a már futó buildek használatát, az alapértelmezett érték 240 perc, és növelheti a felépítési időt, hogy a már futó buildek is lehetővé váljon.
- vmProfile – vmSize és hálózati tulajdonságok megadása
- osDiskSizeGB – növelheti a rendszerkép méretét
- identitás – identitás biztosítása az Azure rendszerkép-készítő számára a Build során való használatra


A is megadható `buildTimeoutInMinutes` . Az alapértelmezett érték 240 perc, és növelheti a felépítési időt, így a már futó buildek is elérhetővé válik.

A rendszerkép konfigurálásához egy minta. JSON sablont fogunk használni. Az általunk használt. JSON fájl a következő: [helloImageTemplateWin.js](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>A szolgáltatások regisztrálása

Ha az előzetes verzióban szeretné használni az Azure képszerkesztőt, regisztrálnia kell az új szolgáltatást.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

A szolgáltatás regisztrációjának állapotát vizsgálja meg.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Győződjön meg a regisztrációról.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Ha nem mondják a regisztrációt, futtassa a következőt:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables"></a>Változók beállítása

Többször is fogjuk használni az adatokat, így az adatok tárolására néhány változót fogunk létrehozni.


```azurecli-interactive
# Resource group name - we are using myImageBuilderRG in this example
imageResourceGroup=myWinImgBuilderRG
# Region location 
location=WestUS2
# Name for the image 
imageName=myWinBuilderImage
# Run output name
runOutputName=aibWindows
# name of the image to be created
imageName=aibWinImage
```

Hozzon létre egy változót az előfizetés-AZONOSÍTÓhoz. Ezt a következővel érheti el: `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Ez az erőforráscsoport a rendszerkép-konfigurációs sablon és a rendszerkép tárolására szolgál.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Felhasználó által hozzárendelt identitás létrehozása és engedélyek beállítása az erőforráscsoporthoz
A rendszerkép-szerkesztő a megadott [felhasználói identitást](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) fogja használni a rendszerkép az erőforráscsoporthoz való beadásához. Ebben a példában egy Azure-szerepkör-definíciót hoz létre, amely a rendszerkép terjesztésének részletes műveleteit tartalmazni fogja. A szerepkör-definíció ezután a felhasználó-identitáshoz lesz rendelve.

## <a name="create-user-assigned-managed-identity-and-grant-permissions"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása és engedélyek megadása 
```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```



## <a name="download-the-image-configuration-template-example"></a>Példa a rendszerkép konfigurációs sablonjának letöltésére

Egy paraméteres rendszerkép-konfigurációs sablon lett létrehozva a kipróbáláshoz. Töltse le a example. JSON fájlt, és konfigurálja a korábban beállított változókkal.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Ezt a példát módosíthatja a terminálon egy szövegszerkesztővel, például a paranccsal `vi` .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> A forrás rendszerkép esetében mindig [meg kell adnia egy verziót](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version), amelyet nem használhat `latest` .
> Ha hozzáadja vagy megváltoztatja azt az erőforráscsoportot, amelyben a rendszerkép el van terjesztve, meg kell adnia az [engedélyeket](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group) az erőforráscsoporthoz.
 
## <a name="create-the-image"></a>A rendszerkép létrehozása

A rendszerkép konfigurációjának elküldése a VM rendszerkép-készítő szolgáltatásnak

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Ha elkészült, a rendszer visszaküldi a sikert jelző üzenetet a-konzolra, és létrehoz egy `Image Builder Configuration Template` -t a alkalmazásban `$imageResourceGroup` . Ezt az erőforrást az erőforráscsoport Azure Portalban tekintheti meg, ha engedélyezi a "rejtett típusok megjelenítése" beállítását.

A háttérben a rendszerkép-szerkesztő létrehoz egy átmeneti erőforráscsoportot is az előfizetésében. Ez az erőforráscsoport a rendszerkép létrehozásához használatos. Ez a következő formátumban fog megjelenni: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Az átmeneti erőforráscsoportot nem szabad közvetlenül törölni. Először törölje a rendszerkép-sablon összetevőt, így az átmeneti erőforrás-csoport törölve lesz.

Ha a szolgáltatás hibát jelez a rendszerkép-konfigurációs sablon beküldésekor:
-  Tekintse át ezeket a [hibaelhárítási](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) lépéseket. 
- A Küldés megkísérlése előtt törölnie kell a sablont a következő kódrészlet használatával.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>A rendszerkép létrehozásának elindítása
A rendszerkép-létrehozási folyamat elindítása az [az Resource meghívása-Action](/cli/azure/resource#az-resource-invoke-action)paranccsal.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Várjon, amíg a Build befejeződik. Ez körülbelül 15 percet vesz igénybe.

Ha bármilyen hibát tapasztal, tekintse át ezeket a [hibaelhárítási](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors) lépéseket.


## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozza létre a virtuális gépet a létrehozott rendszerkép használatával. Cserélje le a- *\<password>* t a saját jelszavára a `aibuser` virtuális gépen.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>A Testreszabás ellenőrzése

Hozzon létre egy Távoli asztal-csatlakozást a virtuális géphez a virtuális gép létrehozásakor beállított Felhasználónév és jelszó használatával. A virtuális gépen nyisson meg egy parancssort, és írja be a következőt:

```console
dir c:\
```

A rendszerkép testreszabása során létrehozott két könyvtárat kell látnia:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha elkészült, törölje az erőforrásokat.

### <a name="delete-the-image-builder-template"></a>A rendszerkép-szerkesztő sablonjának törlése

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

### <a name="delete-the-role-assignment-role-definition-and-user-identity"></a>Törölje a szerepkör-hozzárendelést, a szerepkör-definíciót és a felhasználói identitást.
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

### <a name="delete-the-image-resource-group"></a>A rendszerkép-erőforráscsoport törlése

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a cikkben használt. JSON fájl összetevőiről, tekintse meg a [rendszerkép-szerkesztői sablon referenciáját](../linux/image-builder-json.md).
