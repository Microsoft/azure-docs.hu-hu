---
title: Az Azure rendszerkép-készítő használata & megosztott képkatalógus Linux rendszerű virtuális gépekhez (előzetes verzió)
description: Megtudhatja, hogyan használhatja az Azure rendszerkép-készítőt és az Azure CLI-t egy rendszerkép-verzió létrehozásához egy megosztott rendszerkép-gyűjteményben, majd a rendszerkép globális terjesztését.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: cf7f5ed4a27772a89d0356c60e6f7135786ca07d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "101695600"
---
# <a name="preview-create-a-linux-image-and-distribute-it-to-a-shared-image-gallery"></a>Előzetes verzió: Linux-rendszerkép létrehozása és terjesztése megosztott képgyűjteménybe 

Ebből a cikkből megtudhatja, hogyan használhatja az Azure rendszerkép-készítőt és az Azure CLI-t egy rendszerkép-verzió létrehozásához egy [megosztott rendszerkép](../shared-image-galleries.md)-katalógusban, majd a rendszerkép globális terjesztését. Ezt a [Azure PowerShell](../windows/image-builder-gallery.md)használatával is elvégezheti.


A rendszerkép konfigurálásához egy minta. JSON sablont fogunk használni. Az általunk használt. JSON fájl a következő: [helloImageTemplateforSIG.js](https://github.com/azure/azvmimagebuilder/blob/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json). 

A rendszerkép megosztott képtárba való terjesztéséhez a sablon a [sharedImage](image-builder-json.md#distribute-sharedimage) használja a `distribute` sablon szakaszának értékeként.

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

## <a name="set-variables-and-permissions"></a>Változók és engedélyek beállítása 

Többször is fogjuk használni az adatokat, így az adatok tárolására néhány változót fogunk létrehozni.

Az előzetes verzióban a Image Builder csak a forrásként kezelt lemezképtel azonos erőforráscsoporthoz tartozó egyéni lemezképek létrehozását támogatja. Az ebben a példában szereplő erőforráscsoport-nevet a forrásként kezelt képként megegyező erőforráscsoporthoz kell frissíteni.

```azurecli-interactive
# Resource group name - we are using ibLinuxGalleryRG in this example
sigResourceGroup=ibLinuxGalleryRG
# Datacenter location - we are using West US 2 in this example
location=westus2
# Additional region to replicate the image to - we are using East US in this example
additionalregion=eastus
# name of the shared image gallery - in this example we are using myGallery
sigName=myIbGallery
# name of the image definition to be created - in this example we are using myImageDef
imageDefName=myIbImageDef
# image distribution metadata reference name
runOutputName=aibLinuxSIG
```

Hozzon létre egy változót az előfizetés-AZONOSÍTÓhoz. Ezt a következővel érheti el: `az account show | grep id` .

```azurecli-interactive
subscriptionID=<Subscription ID>
```

Hozza létre az erőforráscsoportot.

```azurecli-interactive
az group create -n $sigResourceGroup -l $location
```

## <a name="create-a-user-assigned-identity-and-set-permissions-on-the-resource-group"></a>Felhasználó által hozzárendelt identitás létrehozása és engedélyek beállítása az erőforráscsoporthoz
A rendszerkép-szerkesztő a megadott [felhasználói identitást](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) használja a rendszerkép az Azure Shared rendszerkép-katalógusba (SIG) való behelyezéséhez. Ebben a példában egy olyan Azure-szerepkör-definíciót hoz létre, amely részletes műveleteket hajt végre a rendszerképnek a SIG-ba való terjesztéséhez. A szerepkör-definíció ezután a felhasználó-identitáshoz lesz rendelve.

```bash
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $sigResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $sigResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$sigResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# this command will download an Azure role definition template, and update the template with the parameters specified earlier.
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup
```


## <a name="create-an-image-definition-and-gallery"></a>Rendszerkép-definíció és-gyűjtemény létrehozása

Ha közös rendszerkép-katalógussal szeretné használni a képszerkesztőt, rendelkeznie kell egy meglévő képtárat és képdefiníciót tartalmazó képpel. A rendszerkép-szerkesztő nem hozza létre az Ön számára a rendszerkép-gyűjteményt és a rendszerkép definícióját.

Ha még nem rendelkezik a használni kívánt gyűjtemény-és képdefinícióval, először hozza létre őket. Először hozzon létre egy képtárat.

```azurecli-interactive
az sig create \
    -g $sigResourceGroup \
    --gallery-name $sigName
```

Ezután hozzon létre egy rendszerkép-definíciót.

```azurecli-interactive
az sig image-definition create \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --publisher myIbPublisher \
   --offer myOffer \
   --sku 18.04-LTS \
   --os-type Linux
```


## <a name="download-and-configure-the-json"></a>A. JSON letöltése és konfigurálása

Töltse le a. JSON sablont, és konfigurálja a változókkal.

```azurecli-interactive
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1_Creating_a_Custom_Linux_Shared_Image_Gallery_Image/helloImageTemplateforSIG.json -o helloImageTemplateforSIG.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateforSIG.json
sed -i -e "s/<rgName>/$sigResourceGroup/g" helloImageTemplateforSIG.json
sed -i -e "s/<imageDefName>/$imageDefName/g" helloImageTemplateforSIG.json
sed -i -e "s/<sharedImageGalName>/$sigName/g" helloImageTemplateforSIG.json
sed -i -e "s/<region1>/$location/g" helloImageTemplateforSIG.json
sed -i -e "s/<region2>/$additionalregion/g" helloImageTemplateforSIG.json
sed -i -e "s/<runOutputName>/$runOutputName/g" helloImageTemplateforSIG.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateforSIG.json
```

## <a name="create-the-image-version"></a>A rendszerkép verziójának létrehozása

A következő rész a rendszerkép verzióját fogja létrehozni a katalógusban. 

Küldje be a rendszerkép konfigurációját az Azure rendszerkép-szerkesztő szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $sigResourceGroup \
    --properties @helloImageTemplateforSIG.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Indítsa el a rendszerkép buildjét.

```azurecli-interactive
az resource invoke-action \
     --resource-group $sigResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateforSIG01 \
     --action Run 
```

A rendszerkép létrehozása és replikálása mindkét régióban eltarthat egy ideig. Várjon, amíg ez a rész be nem fejeződik a virtuális gép létrehozásához való továbblépés előtt.


## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozzon létre egy virtuális gépet az Azure-rendszerkép-szerkesztő által létrehozott rendszerkép-verzióból.

```azurecli-interactive
az vm create \
  --resource-group $sigResourceGroup \
  --name myAibGalleryVM \
  --admin-username aibuser \
  --location $location \
  --image "/subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup/providers/Microsoft.Compute/galleries/$sigName/images/$imageDefName/versions/latest" \
  --generate-ssh-keys
```

Csatlakozzon a virtuális géphez SSH-n keresztül.

```azurecli-interactive
ssh aibuser@<publicIpAddress>
```

A rendszerképet úgy kell megtekinteni, hogy az SSH-kapcsolatok létrehozása után *a nap egy üzenete* legyen.

```console
*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha most újra testre szeretné szabni a rendszerkép verzióját, hogy ugyanazon rendszerkép új verzióját hozza létre, ugorja át a következő lépéseket, és folytassa az [Azure rendszerkép-készítő használatával egy másik rendszerkép-verzió létrehozásához](image-builder-gallery-update-image-version.md).


Ezzel törli a létrehozott rendszerképet, valamint az összes többi erőforrás-fájlt is. Az erőforrások törlése előtt ellenőrizze, hogy befejeződött-e az üzemelő példány.

A rendszerkép-katalógus erőforrásainak törlésekor törölnie kell az összes rendszerkép-verziót, mielőtt törölné a létrehozásához használt képdefiníciót. A katalógus törléséhez először törölnie kell a gyűjtemény összes rendszerkép-definícióját.

Törölje a rendszerkép-szerkesztő sablonját.

```azurecli-interactive
az resource delete \
    --resource-group $sigResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateforSIG01
```

Engedélyek hozzárendelésének, szerepköreinek és identitásának törlése
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$sigResourceGroup

az role definition delete --name "$imageRoleDefName"

az identity delete --ids $imgBuilderId
```

Szerezze be a rendszerkép-szerkesztő által létrehozott rendszerkép verzióját, amely mindig a következővel kezdődik `0.` , majd törli a rendszerkép verzióját:

```azurecli-interactive
sigDefImgVersion=$(az sig image-version list \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID --query [].'name' -o json | grep 0. | tr -d '"')
az sig image-version delete \
   -g $sigResourceGroup \
   --gallery-image-version $sigDefImgVersion \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```   


Törölje a rendszerkép definícióját.

```azurecli-interactive
az sig image-definition delete \
   -g $sigResourceGroup \
   --gallery-name $sigName \
   --gallery-image-definition $imageDefName \
   --subscription $subscriptionID
```

Törölje a gyűjteményt.

```azurecli-interactive
az sig delete -r $sigName -g $sigResourceGroup
```

Törölje az erőforráscsoportot.

```azurecli-interactive
az group delete -n $sigResourceGroup -y
```

## <a name="next-steps"></a>Következő lépések

További információ az [Azure megosztott Képgalériákkal](../shared-image-galleries.md)kapcsolatban.
