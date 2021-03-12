---
title: Az Azure rendszerkép-szerkesztő használata a Linux rendszerű virtuális gépekhez, amelyek lehetővé teszik a meglévő Azure-VNET való hozzáférést (előzetes verzió)
description: Linuxos virtuálisgép-lemezképek létrehozása az Azure Image Builder használatával, amely lehetővé teszi egy meglévő Azure-VNET való hozzáférést
author: danielsollondon
ms.author: danis
ms.date: 03/02/2021
ms.topic: how-to
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.reviewer: danis
ms.openlocfilehash: 500ddec9b84f9d73db45ddb4b7f5a8486a48d3e5
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/10/2021
ms.locfileid: "102565308"
---
# <a name="use-azure-image-builder-for-linux-vms-allowing-access-to-an-existing-azure-vnet"></a>Az Azure-rendszerkép-készítő használata Linux rendszerű virtuális gépekhez, amely lehetővé teszi egy meglévő Azure-VNET való hozzáférést

Ez a cikk bemutatja, hogyan hozhat létre egy alapszintű, testreszabott Linux-rendszerképet az Azure-rendszerkép-készítővel, amely egy VNET meglévő erőforrásaihoz fér hozzá. A létrehozott Build virtuális gép üzembe helyezése az előfizetésben megadott új vagy meglévő VNET történik. Ha meglévő Azure-VNET használ, az Azure rendszerkép-készítő szolgáltatásnak nincs szüksége nyilvános hálózati kapcsolatra.

> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>A szolgáltatások regisztrálása

Először regisztrálnia kell az Azure rendszerkép-szerkesztő szolgáltatásban. A regisztráció a szolgáltatás számára engedélyezi az előkészítési erőforráscsoport létrehozását, kezelését és törlését. A szolgáltatás emellett jogosultságokkal is rendelkezik a rendszerkép létrehozásához szükséges csoport erőforrásainak hozzáadásához.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="set-variables-and-permissions"></a>Változók és engedélyek beállítása 

Az adatok többször is használhatók. Hozzon létre néhány változót az információk tárolásához.

```azurecli-interactive
# set your environment variables here!!!!

# destination image resource group
imageResourceGroup=aibImageRG01

# location (see possible locations in main docs)
location=WestUS2

# your subscription
# get the current subID : 'az account show | grep id'
subscriptionID=$(az account show | grep id | tr -d '",' | cut -c7-)

# name of the image to be created
imageName=aibCustomLinuxImg01

# image distribution metadata reference name
runOutputName=aibCustLinManImg01ro


# VNET properties (update to match your existing VNET, or leave as-is for demo)
# VNET name
vnetName=myexistingvnet01
# subnet name
subnetName=subnet01
# VNET resource group name
# NOTE! The VNET must always be in the same region as the AIB service region.
vnetRgName=existingVnetRG
# Existing Subnet NSG Name or the demo will create it
nsgName=aibdemoNsg
```

Hozza létre az erőforráscsoportot.

```azurecli-interactive
az group create -n $imageResourceGroup -l $location
```

## <a name="configure-networking"></a>Hálózatkezelés beállítása

Ha nem rendelkezik meglévő VNET\Subnet\NSG, a következő parancsfájl használatával hozzon létre egyet.

```bash

# Create a resource group

az group create -n $vnetRgName -l $location

# Create VNET

az network vnet create \
    --resource-group $vnetRgName \
    --name $vnetName --address-prefix 10.0.0.0/16 \
    --subnet-name $subnetName --subnet-prefix 10.0.0.0/24

# Create base NSG to simulate an existing NSG

az network nsg create -g $vnetRgName -n $nsgName

az network vnet subnet update \
    --resource-group $vnetRgName \
    --vnet-name $vnetName \
    --name $subnetName \
    --network-security-group $nsgName
    
#  NOTE! The VNET must always be in the same region as the Azure Image Builder service region.
```

### <a name="add-network-security-group-rule"></a>Hálózati biztonsági csoport hozzáadása szabály

Ez a szabály lehetővé teszi az Azure rendszerkép-készítő terheléselosztó és a proxy virtuális gép közötti kapcsolat használatát. A 60001-es port a Linux OSs és a 60000-es port a Windows OSs-hez készült. A proxy virtuális gép a 22-es porton keresztül csatlakozik a Build virtuális géphez a Windows OSs-hez készült Linux OSs vagy a 5986-es porthoz.

```azurecli-interactive
az network nsg rule create \
    --resource-group $vnetRgName \
    --nsg-name $nsgName \
    -n AzureImageBuilderNsgRule \
    --priority 400 \
    --source-address-prefixes AzureLoadBalancer \
    --destination-address-prefixes VirtualNetwork \
    --destination-port-ranges 60000-60001 --direction inbound \
    --access Allow --protocol Tcp \
    --description "Allow Image Builder Private Link Access to Proxy VM"
```

### <a name="disable-private-service-policy-on-subnet"></a>Privát szolgáltatási házirend letiltása az alhálózaton

```azurecli-interactive
az network vnet subnet update \
  --name $subnetName \
  --resource-group $vnetRgName \
  --vnet-name $vnetName \
  --disable-private-link-service-network-policies true 
```

A rendszerkép-készítő hálózatkezeléssel kapcsolatos további információkért lásd: az [Azure rendszerkép-készítő szolgáltatás hálózatkezelési beállításai](image-builder-networking.md).

## <a name="modify-the-example-template-and-create-role"></a>Példa sablon módosítása és szerepkör létrehozása

```bash
# download the example and configure it with your vars

curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/quickquickstarts/1a_Creating_a_Custom_Linux_Image_on_Existing_VNET/existingVNETLinux.json -o existingVNETLinux.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" existingVNETLinux.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" existingVNETLinux.json
sed -i -e "s/<region>/$location/g" existingVNETLinux.json
sed -i -e "s/<imageName>/$imageName/g" existingVNETLinux.json
sed -i -e "s/<runOutputName>/$runOutputName/g" existingVNETLinux.json

sed -i -e "s/<vnetName>/$vnetName/g" existingVNETLinux.json
sed -i -e "s/<subnetName>/$subnetName/g" existingVNETLinux.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" existingVNETLinux.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json

sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json

```

## <a name="set-permissions-on-the-resource-group"></a>Az erőforráscsoport engedélyeinek beállítása

A rendszerkép-szerkesztő a megadott [felhasználói identitást](../../active-directory/managed-identities-azure-resources/qs-configure-cli-windows-vm.md#user-assigned-managed-identity) használja a rendszerkép az Azure Shared rendszerkép-katalógusba (SIG) való behelyezéséhez. Ebben a példában egy olyan Azure-szerepkör-definíciót hoz létre, amely részletes műveleteket hajt végre a rendszerképnek a SIG-ba való terjesztéséhez. A szerepkör-definíció ezután a felhasználó-identitáshoz lesz rendelve.

```bash
# create user assigned identity for image builder
idenityName=aibBuiUserId$(date +'%s')
az identity create -g $imageResourceGroup -n $idenityName

# get identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $idenityName | grep "clientId" | cut -c16- | tr -d '",')

# get the user identity URI, needed for the template
imgBuilderId=/subscriptions/$subscriptionID/resourcegroups/$imageResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/$idenityName

# update the template
sed -i -e "s%<imgBuilderId>%$imgBuilderId%g" existingVNETLinux.json

# make role name unique, to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# update the definitions
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json
```

A rendszerkép-készítő alacsonyabb részletességi és megnövekedett jogosultsági szint megadása helyett két szerepkört hozhat létre. Az egyik lehetővé teszi, hogy a Builder engedélyekkel hozzon létre egy rendszerképet, a másik lehetőség a Build virtuális gép és a terheléselosztó összekapcsolására a VNET.

```bash
# create role definitions
az role definition create --role-definition ./aibRoleImageCreation.json
az role definition create --role-definition ./aibRoleNetworking.json

# grant role definition to the user assigned identity
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName
```

Az engedélyekkel kapcsolatos további információkért lásd: az Azure [rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása az Azure CLI használatával](image-builder-permissions-cli.md) vagy az [Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása a PowerShell használatával](image-builder-permissions-powershell.md).

## <a name="create-the-image"></a>A rendszerkép létrehozása

Küldje be a rendszerkép konfigurációját az Azure rendszerkép-szerkesztő szolgáltatásba.

```azurecli-interactive
az resource create \
    --resource-group $imageResourceGroup \
    --properties @existingVNETLinux.json \
    --is-full-object \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01

# Wait approximately 1-3 mins (validation, permissions etc.)
```

Indítsa el a rendszerkép buildjét.

```azurecli-interactive
az resource invoke-action \
     --resource-group $imageResourceGroup \
     --resource-type  Microsoft.VirtualMachineImages/imageTemplates \
     -n existingVNETLinuxTemplate01 \
     --action Run 

# Wait approximately 15 mins
```

A rendszerkép létrehozása és replikálása mindkét régióban eltarthat egy ideig. Várjon, amíg ez a rész be nem fejeződik a virtuális gép létrehozásához való továbblépés előtt.


## <a name="create-the-vm"></a>A virtuális gép létrehozása

Hozzon létre egy virtuális gépet az Azure-rendszerkép-szerkesztő által létrehozott rendszerkép-verzióból.

```azurecli-interactive
az vm create \
  --resource-group $imageResourceGroup \
  --name aibImgVm0001 \
  --admin-username aibuser \
  --image $imageName \
  --location $location \
  --generate-ssh-keys
```

Csatlakozzon a virtuális géphez SSH-n keresztül.

```bash
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

Ha most szeretné újból testreszabni a rendszerkép verzióját, hogy ugyanazon rendszerkép új verzióját hozza létre, ugorja át a következő lépéseket, és folytassa az [Azure rendszerkép-készítő használatával egy másik rendszerkép-verzió létrehozásához](image-builder-gallery-update-image-version.md).


A következő törli a létrehozott rendszerképet, valamint az összes többi erőforrás-fájlt. Az erőforrások törlése előtt ellenőrizze, hogy befejeződött-e az üzemelő példány.

A rendszerkép-katalógus erőforrásainak törlésekor törölnie kell az összes rendszerkép-verziót, mielőtt törölné a létrehozásához használt képdefiníciót. A katalógus törléséhez először törölnie kell a gyűjtemény összes rendszerkép-definícióját.

Törölje a rendszerkép-szerkesztő sablonját.

```azurecli
az resource delete \
    --resource-group $imageResourceGroup \
    --resource-type Microsoft.VirtualMachineImages/imageTemplates \
    -n existingVNETLinuxTemplate01
```

Engedélyek hozzárendelésének, szerepköreinek és identitásának törlése
```azurecli-interactive
az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup

az role assignment delete \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$vnetRgName


az role definition delete --name "$imageRoleDefName"
az role definition delete --name "$netRoleDefName"

az identity delete --ids $imgBuilderId
```

Törölje az erőforráscsoportot.

```azurecli-interactive
az group delete -n $imageResourceGroup
```

Ha ehhez a rövid útmutatóhoz létrehozott egy VNET, akkor törölheti a VNET, ha már nincs használatban.

## <a name="next-steps"></a>Következő lépések

További információ az [Azure megosztott Képgalériákkal](../shared-image-galleries.md)kapcsolatban.
