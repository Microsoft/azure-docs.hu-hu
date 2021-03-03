---
title: Virtuálisgép-rendszerkép létrehozása és felhasználó által hozzárendelt felügyelt identitás használata az Azure Storage-ban tárolt fájlok eléréséhez (előzetes verzió)
description: Hozzon létre egy virtuálisgép-rendszerképet az Azure rendszerkép-készítővel, amely az Azure Storage-ban tárolt fájlokat felhasználó által hozzárendelt felügyelt identitás használatával érheti el.
author: cynthn
ms.author: cynthn
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: 9bcb7a94cdf1d5478db32a22ba6e612a90c53ed9
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695379"
---
# <a name="create-an-image-and-use-a-user-assigned-managed-identity-to-access-files-in-azure-storage"></a>Rendszerkép létrehozása és felhasználó által hozzárendelt felügyelt identitás használata az Azure Storage-beli fájlokhoz való hozzáféréshez 

Az Azure rendszerkép-szerkesztő támogatja a parancsfájlok használatát, illetve a fájlok több helyről történő másolását, például a GitHub és az Azure Storage stb. Ezeknek az adatoknak a használatához külsőleg elérhetőnek kell lenniük az Azure-rendszerkép-készítő számára, de az Azure Storage-blobokat SAS-jogkivonatok használatával lehet védelemmel ellátni.

Ez a cikk bemutatja, hogyan hozhat létre testreszabott rendszerképeket az Azure VM rendszerkép-készítő használatával, ahol a szolgáltatás egy [felhasználó által hozzárendelt felügyelt identitással](../../active-directory/managed-identities-azure-resources/overview.md) fér hozzá az Azure Storage-ban található fájlokhoz a rendszerkép testreszabásához, anélkül, hogy nyilvánosan elérhetővé kellene tennie a fájlokat, vagy sas-jogkivonatokat kell beállítania.

Az alábbi példában két erőforráscsoport jön létre, amelyeket az egyéni rendszerképhez fog használni, a másik pedig egy, a parancsfájlt tartalmazó Azure Storage-fiókot fog üzemeltetni. Ez valós helyzetet szimulál, ahol előfordulhat, hogy a rendszerkép-szerkesztőn kívül különböző tárolási fiókokban található összetevők vagy képfájlok is létrehozhatók. Létre kell hoznia egy felhasználó által hozzárendelt identitást, majd meg kell adnia az olvasási engedélyeket a parancsfájlhoz, de a fájlhoz nem fog nyilvános hozzáférést adni. Ezután a rendszerhéj-testreszabó használatával letöltheti és futtathatja a szkriptet a Storage-fiókból.


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


## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Többször is fogjuk használni az adatokat, így az adatok tárolására néhány változót fogunk létrehozni.


```console
# Image resource group name 
imageResourceGroup=aibmdimsi
# storage resource group
strResourceGroup=aibmdimsistor
# Location 
location=WestUS2
# name of the image to be created
imageName=aibCustLinuxImgMsi01
# image distribution metadata reference name
runOutputName=u1804ManImgMsiro
```

Hozzon létre egy változót az előfizetés-AZONOSÍTÓhoz. Ezt a következővel érheti el: `az account show | grep id` .

```console
subscriptionID=<Your subscription ID>
```

Hozza létre az erőforráscsoportot a rendszerképhez és a parancsfájl-tárolóhoz is.

```console
# create resource group for image template
az group create -n $imageResourceGroup -l $location
# create resource group for the script storage
az group create -n $strResourceGroup -l $location
```

Hozzon létre egy felhasználó által hozzárendelt identitást, és állítson be engedélyeket az erőforráscsoporthoz.

A rendszerkép-szerkesztő a megadott [felhasználói identitást](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) fogja használni a rendszerkép az erőforráscsoporthoz való beadásához. Ebben a példában egy Azure-szerepkör-definíciót hoz létre, amely a rendszerkép terjesztésének részletes műveleteit tartalmazni fogja. A szerepkör-definíció ezután a felhasználó-identitáshoz lesz rendelve.

```console
# create user assigned identity for image builder to access the storage account where the script is located
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# download preconfigured role definition example
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# update the definition
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Azure Image Builder Service Image Creation Role" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

Hozza létre a tárolót, és másolja a minta parancsfájlt a GitHubról.

```azurecli-interactive
# script storage account
scriptStorageAcc=aibstorscript$(date +'%s')

# script container
scriptStorageAccContainer=scriptscont$(date +'%s')

# script url
scriptUrl=https://$scriptStorageAcc.blob.core.windows.net/$scriptStorageAccContainer/customizeScript.sh

# create storage account and blob in resource group
az storage account create -n $scriptStorageAcc -g $strResourceGroup -l $location --sku Standard_LRS

az storage container create -n $scriptStorageAccContainer --fail-on-exist --account-name $scriptStorageAcc

# copy in an example script from the GitHub repo 
az storage blob copy start \
    --destination-blob customizeScript.sh \
    --destination-container $scriptStorageAccContainer \
    --account-name $scriptStorageAcc \
    --source-uri https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/customizeScript.sh
```

Adja meg a rendszerkép-készítő engedélyt a rendszerkép-erőforráscsoport erőforrásainak létrehozásához. Az `--assignee` érték a felhasználó-azonosító azonosító.

```azurecli-interactive
az role assignment create \
    --assignee $imgBuilderCliId \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$strResourceGroup/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/$scriptStorageAccContainer 
```




## <a name="modify-the-example"></a>Példa módosítása

Töltse le a example. JSON fájlt, és konfigurálja a létrehozott változókkal.

```console
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/7_Creating_Custom_Image_using_MSI_to_Access_Storage/helloImageTemplateMsi.json -o helloImageTemplateMsi.json
sed -i -e "s/<subscriptionID>/$subscriptionID/g" helloImageTemplateMsi.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" helloImageTemplateMsi.json
sed -i -e "s/<region>/$location/g" helloImageTemplateMsi.json
sed -i -e "s/<imageName>/$imageName/g" helloImageTemplateMsi.json
sed -i -e "s%<scriptUrl>%$scriptUrl%g" helloImageTemplateMsi.json
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" helloImageTemplateMsi.json
sed -i -e "s%<runOutputName>%$runOutputName%g" helloImageTemplateMsi.json
```

## <a name="create-the-image"></a>A rendszerkép létrehozása

Küldje be a rendszerkép konfigurációját az Azure rendszerkép-szerkesztő szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @helloImageTemplateMsi.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
```

Indítsa el a rendszerkép buildjét.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n helloImageTemplateMsi01 \
     --action Run 
```

Várjon, amíg a Build befejeződik. Ez körülbelül 15 percet vesz igénybe.

## <a name="create-a-vm"></a>Virtuális gép létrehozása

Hozzon létre egy virtuális gépet a rendszerképből. 

```azurecli
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm00 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

A virtuális gép létrehozása után indítson el egy SSH-munkamenetet a virtuális géppel.

```console
ssh aibuser@<publicIp>
```

A rendszerképet úgy kell megtekinteni, hogy az SSH-kapcsolatok létrehozása után a nap egy üzenete legyen.

```output

*******************************************************
**            This VM was built from the:            **
**      !! AZURE VM IMAGE BUILDER Custom Image !!    **
**         You have just been Customized :-)         **
*******************************************************
```

## <a name="clean-up"></a>A fölöslegessé vált elemek eltávolítása

Ha elkészült, akkor törölheti az erőforrásokat, ha már nincs rájuk szükség.

```azurecli-interactive

az role definition delete --name "$imageRoleDefName"
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role "$imageRoleDefName" \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
az identity delete --ids $imgBuilderId
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n helloImageTemplateMsi01
az group delete -n $imageResourceGroup
az group delete -n $strResourceGroup
```

## <a name="next-steps"></a>Következő lépések

Ha bármilyen probléma merül fel az Azure rendszerkép-készítővel való együttműködés során, olvassa el a [hibaelhárítást](image-builder-troubleshoot.md?toc=%2fazure%2fvirtual-machines%context%2ftoc.json)ismertető témakört.
