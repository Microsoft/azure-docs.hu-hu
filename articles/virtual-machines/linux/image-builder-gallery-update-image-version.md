---
title: Új virtuálisgép-rendszerkép verziójának létrehozása meglévő rendszerkép-verzióból az Azure rendszerkép-készítővel (előzetes verzió)
description: Hozzon létre egy új virtuálisgép-rendszerkép-verziót egy meglévő rendszerkép-verzióból a Linuxon futó Azure rendszerkép-készítő használatával.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2020
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 0887051ffa396f1eac8bc00dc2437b8e92bec45a
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695634"
---
# <a name="preview-create-a-new-vm-image-version-from-an-existing-image-version-using-azure-image-builder-in-linux"></a>Előzetes verzió: hozzon létre egy új virtuálisgép-rendszerkép verzióját egy meglévő rendszerkép-verzióból a Linuxon futó Azure rendszerkép-készítő használatával

Ebből a cikkből megtudhatja, hogyan készíthet meglévő rendszerképeket egy [megosztott rendszerkép](../shared-image-galleries.md)-katalógusban, hogyan frissítheti, és hogyan teheti közzé új rendszerképként a gyűjteményben.

A rendszerkép konfigurálásához egy minta. JSON sablont fogunk használni. Az általunk használt. JSON fájl a következő: [helloImageTemplateforSIGfromSIG.js](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json). 


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


## <a name="set-variables-and-permissions"></a>Változók és engedélyek beállítása

Ha létrehoz egy rendszerképet, [és eljuttat egy megosztott képtárat](image-builder-gallery.md) a megosztott képkatalógus létrehozásához, már létrehozott néhány változót. Ha nem, állítson be néhány változót, amelyet használni szeretne ehhez a példához.


```console
# Resource group name 
sigResourceGroup=ibLinuxGalleryRG
# Gallery location 
location=westus2
# Additional region to replicate the image version to 
additionalregion=eastus
# Name of the shared image gallery 
sigName=myIbGallery
# Name of the image definition to use
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibSIGLinuxUpdate
```

Hozzon létre egy változót az előfizetés-AZONOSÍTÓhoz. Ezt a következővel érheti el: `az account show | grep id` .

```console
subscriptionID=<Subscription ID>
```

Szerezze be a frissíteni kívánt rendszerkép verzióját.

```azurecli
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
Tekintse át a használni kívánt példát a. JSON fájl megnyitásához: [helloImageTemplateforSIGfromSIG.jsa](https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/2_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json) [rendszerkép-készítő sablon hivatkozásával](image-builder-json.md)együtt. 


Töltse le a. JSON-példát, és konfigurálja a változókat. 

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/8_Creating_a_Custom_Linux_Shared_Image_Gallery_Image_from_SIG/helloImageTemplateforSIGfromSIG.json -o helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<sigDefImgVersionId>%$sigDefImgVersionId%g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIGfromSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIGfromSIG.json
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Küldje be a rendszerkép konfigurációját a virtuálisgép-rendszerkép-szerkesztő szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIGfromSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIGfromSIG01
```

Indítsa el a rendszerkép buildjét.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIGfromSIG01 \
     --action Run 
```

Várjon, amíg a rendszerkép fel lett építve és replikációra kerül, mielőtt továbblép a következő lépésre.


## <a name="create-the-vm"></a>A virtuális gép létrehozása

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name aibImgVm001 \
  --admin-username azureuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Hozzon létre egy SSH-kapcsolatokat a virtuális géphez a virtuális gép nyilvános IP-címének használatával.

```console
ssh azureuser@<pubIp>
```

Az SSH-kapcsolatok létrehozása után a rendszerképet a "nap üzenete" értékre igazította.

```output
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

`exit`Az SSH-kapcsolatok bezárásához írja be a következőt:.

A katalógusban már elérhető rendszerképek is megtekinthetők.

```azurecli-interactive
az sig image-version list -g $sigResourceGroup -r $sigName -i $imageDefName -o table
```


## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a cikkben használt. JSON fájl összetevőiről, tekintse meg a [rendszerkép-szerkesztői sablon referenciáját](../linux/image-builder-json.md).
