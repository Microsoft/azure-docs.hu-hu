---
title: Új rendszerkép-verzió létrehozása meglévő rendszerkép-verzióból az Azure rendszerkép-készítővel (előzetes verzió)
description: Hozzon létre egy új virtuálisgép-rendszerkép verzióját egy meglévő rendszerkép-verzióból a Windows Azure rendszerkép-készítő használatával.
author: cynthn
ms.author: cynthn
ms.date: 05/05/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subervice: image-builder
ms.colletion: windows
ms.openlocfilehash: d54c445e474f918f1eb37769bb2c1326ecb8c5ee
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2021
ms.locfileid: "101668039"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-windows"></a>Előzetes verzió: hozzon létre egy új virtuálisgép-rendszerkép verzióját egy meglévő rendszerkép-verzióból a Windows Azure rendszerkép-szerkesztő használatával

Ebből a cikkből megtudhatja, hogyan készíthet meglévő rendszerképeket egy [megosztott rendszerkép](../shared-image-galleries.md)-katalógusban, hogyan frissítheti, és hogyan teheti közzé új rendszerképként a gyűjteményben.

A rendszerkép konfigurálásához egy minta. JSON sablont fogunk használni. Az általunk használt. JSON fájl a következő: [helloImageTemplateforSIGfromWinSIG.js](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json). 

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
```

Ha nem mondják a regisztrációt, futtassa a következőt:

```azurecli-interactive
az provider register -n Microsoft.VirtualMachineImages
az provider register -n Microsoft.Compute
az provider register -n Microsoft.KeyVault
az provider register -n Microsoft.Storage
```


## <a name="set-variables-and-permissions"></a>Változók és engedélyek beállítása

Ha létrehoz egy rendszerképet, és elküld [egy megosztott képtárat](image-builder-gallery.md) a megosztott képkatalógus létrehozásához, már létrehozta a szükséges változókat. Ha nem, állítson be néhány változót, amelyet használni szeretne ehhez a példához.

Az előzetes verzióban a Image Builder csak a forrásként kezelt lemezképtel azonos erőforráscsoporthoz tartozó egyéni lemezképek létrehozását támogatja. Az ebben a példában szereplő erőforráscsoport-nevet a forrásként kezelt képként megegyező erőforráscsoporthoz kell frissíteni.

```azurecli-interactive
# Resource group name - we are using ibsigRG in this example
sigResourceGroup=myIBWinRG
# Datacenter location - we are using West US 2 in this example
location=westus
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=my22stSIG
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=winSvrimages
# image distribution metadata reference name
runOutputName=w2019SigRo
# User name and password for the VM
username="user name for the VM"
vmpassword="password for the VM"
```

Hozzon létre egy változót az előfizetés-AZONOSÍTÓhoz. Ezt a következővel érheti el: `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Szerezze be a frissíteni kívánt rendszerkép verzióját.

```azurecli-interactive
sigDefImgVersionId=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'id' -o json | grep 0. | tr -d '"' | tr -d '[:space:]')
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Felhasználó által hozzárendelt identitás létrehozása és engedélyek beállítása az erőforráscsoporthoz
Ahogy az előző példában beállította a felhasználói identitást, csak be kell szereznie az erőforrás-azonosítót, ezt a rendszer hozzáfűzi a sablonhoz.

```azurecli-interactive
#get identity used previously
imgBuilderId=$(az identity list -g $sigResourceGroup --query "[?contains(name, 'aibBuiUserId')].id" -o tsv)
```

Ha már rendelkezik saját közös rendszerkép-katalógussal, és nem követte az előző példát, akkor a rendszerkép-készítőhöz engedélyeket kell rendelnie az erőforráscsoport eléréséhez, így a katalógushoz is hozzáférhet. Tekintse át a [rendszerkép létrehozása és terjesztése megosztott rendszerkép-gyűjteményre](image-builder-gallery.md) című témakör lépéseit.


## <a name="modify-helloimage-example"></a>HelloImage módosítása – példa
Tekintse át a használni kívánt példát a. JSON fájl megnyitásához: [helloImageTemplateforSIGfromSIG.jsa](https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) [rendszerkép-készítő sablon hivatkozásával](../linux/image-builder-json.md)együtt. 


Töltse le a. JSON-példát, és konfigurálja a változókat. 

```azurecli-interactive
curl https://raw.githubusercontent.com/danielsollondon/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Win_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromWinSIG.json -o helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromWinSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromWinSIG.json
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Küldje be a rendszerkép konfigurációját a virtuálisgép-rendszerkép-szerkesztő szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromWinSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n imageTemplateforSIGfromWinSIG01
```

Indítsa el a rendszerkép buildjét.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n imageTemplateforSIGfromWinSIG01 \
     --action Run 
```

Várjon, amíg a rendszerkép fel lett építve és replikációra kerül, mielőtt továbblép a következő lépésre.


## <a name="create-the-vm"></a>A virtuális gép létrehozása

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgWinVm002 \
  --admin-username $username \
  --admin-password $vmpassword \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --location $location
```

## <a name="verify-the-customization"></a>A Testreszabás ellenőrzése
Hozzon létre egy Távoli asztal-csatlakozást a virtuális géphez a virtuális gép létrehozásakor beállított Felhasználónév és jelszó használatával. A virtuális gépen nyisson meg egy parancssort, és írja be a következőt:

```console
dir c:\
```

Ekkor két könyvtárat kell látnia:
- `buildActions` Ez az első rendszerkép-verzióban lett létrehozva.
- `buildActions2` Ez az első rendszerkép-verzió frissítésének részeként jött létre a második rendszerkép-verzió létrehozásához.


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a cikkben használt. JSON fájl összetevőiről, tekintse meg a [rendszerkép-szerkesztői sablon referenciáját](../linux/image-builder-json.md).