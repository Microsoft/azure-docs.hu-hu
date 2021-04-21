---
title: Windows rendszerű virtuális gép létrehozása az Azure Image Builder (előzetes verzió)
description: Hozzon létre egy Windows rendszerű virtuális gépet az Azure Image Builder.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: b93d236d0b716bfaf7dfb45b21c9524ece75fcae
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764566"
---
# <a name="preview-create-a-windows-vm-with-azure-image-builder"></a>Előzetes verzió: Windows rendszerű virtuális gép létrehozása az Azure Image Builder

Ez a cikk bemutatja, hogyan hozhat létre testreszabott Windows-rendszerképet a Azure VM Image Builder. A cikkben látható példa [testreszabók segítségével](../linux/image-builder-json.md#properties-customize) szabja testre a képet:
- PowerShell (ScriptUri) – töltsön le és futtatason egy [PowerShell-szkriptet.](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/testPsScript.ps1)
- Windows újraindítás – újraindítja a virtuális gépet.
- PowerShell (beágyazott) – egy adott parancs futtatása. Ebben a példában egy könyvtárat hoz létre a virtuális gépen a `mkdir c:\\buildActions` használatával.
- Fájl – másol egy fájlt a GitHubról a virtuális gépre. Ez a példa [index.md](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/exampleArtifacts/buildArtifacts/index.html) virtuális `c:\buildArtifacts\index.html` gépre másolja a következőt: .
- buildTimeoutInMinutes – Növelje a buildidőt a hosszabb ideig futó buildek érdekében. Az alapértelmezett érték 240 perc, a buildek pedig megnövelik a buildek idejét, így hosszabb ideig futó buildek futnak.
- vmProfile – vmSize és Network tulajdonságok megadása
- osDiskSizeGB – növelheti a kép méretét
- identity – identitás biztosítása az Azure Image Builder számára, amely a build során használható


Megadhatja a következőt `buildTimeoutInMinutes` is: . Az alapértelmezett érték 240 perc, és megnövelheti a buildidőt, így hosszabb ideig futó buildek futnak.

A rendszerkép konfigurálása egy .json-mintasablont használ. A használt .json-fájl itt található:helloImageTemplateWin.js[ a következőn:](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json). 


> [!IMPORTANT]
> Az Azure Image Builder jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).


## <a name="register-the-features"></a>A funkciók regisztrálása

Az Azure Image Builder előzetes verzióban való használatához regisztrálnia kell az új funkciót.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

Ellenőrizze a szolgáltatásregisztráció állapotát.

```azurecli-interactive
az feature show --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview | grep state
```

Ellenőrizze a regisztrációt.

```azurecli-interactive
az provider show -n Microsoft.VirtualMachineImages | grep registrationState
az provider show -n Microsoft.KeyVault | grep registrationState
az provider show -n Microsoft.Compute | grep registrationState
az provider show -n Microsoft.Storage | grep registrationState
az provider show -n Microsoft.Network | grep registrationState
```

Ha nem regisztráltat, futtassa a következőt:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
az provider register -n Microsoft.Network
```


## <a name="set-variables"></a>Változók beállítása

Néhány információt többször is felhasználunk, ezért létrehozunk néhány változót az információk tárolására.


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

Hozzon létre egy változót az előfizetés-azonosítóhoz. Ezt a használatával kaphatja `az account show | grep id` meg.

```azurecli-interactive
subscriptionID=<Your subscription ID>
```
## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Ez az erőforráscsoport tárolja a képkonfigurációs sablon összetevőt és a rendszerképet.


```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Felhasználó által hozzárendelt identitás létrehozása és engedélyek beállítása az erőforráscsoporton
Image Builder a megadott [felhasználói](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) identitással fogja az erőforráscsoportba bejektálni a rendszerképet. Ebben a példában létre fog hozni egy Azure-beli szerepkör-definíciót, amely a rendszerkép terjesztéséhez szükséges részletes műveletekkel rendelkezik. A szerepkör-definíció ezután hozzá lesz rendelve a felhasználói identitáshoz.

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



## <a name="download-the-image-configuration-template-example"></a>Példa képkonfigurációs sablon letöltése

Létre lett hozva egy paraméteres rendszerképkonfigurációs sablon, hogy kipróbálja. Töltse le a .json példafájlt, és konfigurálja a korábban beállított változók használatával.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/0_Creating_a_Custom_Windows_Managed_Image/helloImageTemplateWin.json -o helloImageTemplateWin.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateWin.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateWin.json
sed -i -e "s/<region>/$location/g" helloImageTemplateWin.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateWin.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateWin.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateWin.json

```

Ezt a példát módosíthatja a terminálban egy szövegszerkesztővel, például a `vi` következővel: .

```azurecli-interactive
vi helloImageTemplateWin.json
```

> [!NOTE]
> A forrásként használt rendszerképhez mindig meg kell adnia egy [verziót,](../linux/image-builder-troubleshoot.md#build--step-failed-for-image-version)amely nem `latest` használható.
> Ha hozzáadja vagy módosítja azt az erőforráscsoportot, amelybe a rendszerképet terjeszti, akkor az engedélyeket az erőforráscsoporton kell beállítania. [](#create-a-user-assigned-identity-and-set-permissions-on-the-resource-group)
 
## <a name="create-the-image"></a>A rendszerkép létrehozása

A rendszerkép konfigurációjának elküldése a virtuális gép Image Builder szolgáltatásnak

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateWin.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateWin01
```

Ha elkészült, ez egy sikert visszakért üzenetet ad vissza a konzolra, és létrehoz egy et a `Image Builder Configuration Template` `$imageResourceGroup` -ban. Ezt az erőforrást az erőforráscsoportban láthatja a Azure Portal, ha engedélyezi a "Rejtett típusok megjelenítése" funkció engedélyezését.

A háttérben a Image Builder egy átmeneti erőforráscsoportot is létrehoz az előfizetésben. Ezt az erőforráscsoportot használja a rendszerkép-összeállításhoz. A következő formátumban lesz: `IT_<DestinationResourceGroup>_<TemplateName>`

> [!Note]
> Az átmeneti erőforráscsoportot nem törölheti közvetlenül. Először törölje a rendszerképsablon összetevőt. Ez az átmeneti erőforráscsoport törlését fogja okozhatni.

Ha a szolgáltatás hibát ad vissza a rendszerkép-konfigurációs sablon beküldési ideje alatt:
-  Tekintse át ezeket [a hibaelhárítási lépéseket.](../linux/image-builder-troubleshoot.md#troubleshoot-image-template-submission-errors) 
- Mielőtt újra próbálkozik a beküldési művelettel, törölnie kell a sablont az alábbi kódrészlet használatával.

```azurecli-interactive
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateLinux01
```

## <a name="start-the-image-build"></a>A rendszerkép-összeállítás elkezdődhet
Indítsa el a rendszerkép-építés folyamatát [az az resource invoke-action használatával.](/cli/azure/resource#az_resource_invoke_action)

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateWin01 \
     --action Run 
```

Várjon, amíg a build befejeződik. Ez körülbelül 15 percet is igénybe vehet.

Ha hibába ütközik, tekintse át ezeket [a hibaelhárítási lépéseket.](../linux/image-builder-troubleshoot.md#troubleshoot-common-build-errors)


## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozza létre a virtuális gépet a létrehozott rendszerkép használatával. Cserélje *\<password>* le a helyére a saját jelszavát a `aibuser` virtuális gépen.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgWinVm00 \
  --admin-username aibuser \
  --admin-password <password> \
  --image $imageName \
  --location $location
```

## <a name="verify-the-customization"></a>A testreszabás ellenőrzése

Hozzon létre Távoli asztal virtuális géphez a virtuális gép létrehozásakor beállított felhasználónévvel és jelszóval. A virtuális gépen belül nyisson meg egy parancssort, és írja be a következőt:

```console
dir c:\
```

A kép testreszabása során létrehozott két könyvtárnak kell látsza lennie:
- buildActions
- buildArtifacts

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha végzett, törölje az erőforrásokat.

### <a name="delete-the-image-builder-template"></a>A képszerkesztő sablon törlése

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

### <a name="delete-the-image-resource-group"></a>A rendszerkép erőforráscsoport törlése

```azurecli-interactive
az group delete -n $imageResourceGroup
```


## <a name="next-steps"></a>Következő lépések

A cikkben használt .json-fájl összetevőivel kapcsolatos további információkért tekintse meg a [Képszerkesztő sablonreferenciáját.](../linux/image-builder-json.md)
