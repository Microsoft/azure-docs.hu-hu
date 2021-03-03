---
title: Az Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása az Azure CLI használatával
description: Az Azure virtuálisgép-rendszerkép-készítő szolgáltatás követelményeinek konfigurálása, beleértve az Azure CLI-vel való jogosultságokat és jogosultságokat
author: cynthn
ms.author: danis
ms.date: 03/02/2021
ms.topic: article
ms.service: virtual-machines
ms.subservice: image-builder
ms.collection: linux
ms.openlocfilehash: f9b60af2c9fe16f834ce3098266c03afe2b99667
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695430"
---
# <a name="configure-azure-image-builder-service-permissions-using-azure-cli"></a>Az Azure rendszerkép-készítő szolgáltatás engedélyeinek konfigurálása az Azure CLI használatával

Az Azure rendszerkép-készítő szolgáltatásnak a rendszerkép létrehozása előtt meg kell adni az engedélyek és a jogosultságok konfigurációját. Az alábbi részekből megtudhatja, hogyan konfigurálhat lehetséges forgatókönyveket az Azure CLI használatával.

> [!IMPORTANT]
> Az Azure rendszerkép-szerkesztő jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik. További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

## <a name="register-the-features"></a>A szolgáltatások regisztrálása

Először regisztrálnia kell az Azure rendszerkép-szerkesztő szolgáltatásban. A regisztráció a szolgáltatás számára engedélyezi az előkészítési erőforráscsoport létrehozását, kezelését és törlését. A szolgáltatás emellett jogosultságokkal is rendelkezik a rendszerkép létrehozásához szükséges csoport erőforrásainak hozzáadásához.

```azurecli-interactive
az feature register --namespace Microsoft.VirtualMachineImages --name VirtualMachineTemplatePreview
```

## <a name="create-an-azure-user-assigned-managed-identity"></a>Azure-felhasználó által hozzárendelt felügyelt identitás létrehozása

Az Azure rendszerkép-szerkesztőhöz [Azure-felhasználó által hozzárendelt felügyelt identitást](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)kell létrehoznia. Az Azure Image Builder a felhasználó által hozzárendelt felügyelt identitást használja a képek olvasásához, a képek írásához és az Azure Storage-fiókok eléréséhez. Az identitás engedélyt ad az előfizetésében meghatározott műveletek elvégzéséhez.

> [!NOTE]
> Korábban az Azure rendszerkép-készítő az Azure rendszerkép-készítő egyszerű szolgáltatásnevet (SPN) használta a rendszerkép-erőforráscsoportok engedélyeinek megadásához. Az egyszerű szolgáltatásnév használata elavulttá válik. Használja helyette a felhasználó által hozzárendelt felügyelt identitást.

Az alábbi példa bemutatja, hogyan hozhat létre egy Azure-felhasználóhoz rendelt felügyelt identitást. Cserélje le a helyőrző beállításait a változók beállításához.

| Beállítás | Leírás |
|---------|-------------|
| \<Resource group\> | Az erőforráscsoport, amelyben létre kell hozni a felhasználó által hozzárendelt felügyelt identitást. |

```azurecli-interactive
identityName="aibIdentity"
imageResourceGroup=<Resource group>

az identity create \
    --resource-group $imageResourceGroup \
    --name $identityName
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

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
# Resource group - For Preview, image builder will only support creating custom images in the same Resource Group as the source managed image.
imageResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleImageCreation.json -o aibRoleImageCreation.json

# Create a unique role name to avoid clashes in the same Azure Active Directory domain
imageRoleDefName="Azure Image Builder Image Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleImageCreation.json
sed -i -e "s/<rgName>/$imageResourceGroup/g" aibRoleImageCreation.json
sed -i -e "s/Azure Image Builder Service Image Creation Role/$imageRoleDefName/g" aibRoleImageCreation.json

# Create a custom role from the sample aibRoleImageCreation.json description file.
az role definition create --role-definition ./aibRoleImageCreation.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $imageRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$imageResourceGroup
```

### <a name="existing-vnet-azure-role-example"></a>Meglévő VNET Azure-szerepkör – példa

Az alábbi példa egy Azure-szerepkört hoz létre egy meglévő VNET-rendszerkép használatához és terjesztéséhez. Ezután adja meg az egyéni szerepkört a felhasználó által hozzárendelt felügyelt identitáshoz az Azure rendszerkép-szerkesztőben.

A példában szereplő értékek cseréjének egyszerűbbé tételéhez először állítsa be a következő változókat. Cserélje le a helyőrző beállításait a változók beállításához.

| Beállítás | Leírás |
|---------|-------------|
| \<Subscription ID\> | Az Azure-előfizetés azonosítója |
| \<Resource group\> | VNET erőforráscsoport |

```azurecli-interactive
# Subscription ID - You can get this using `az account show | grep id` or from the Azure portal.
subscriptionID=<Subscription ID>
VnetResourceGroup=<Resource group>
identityName="aibIdentity"

# Use *cURL* to download the a sample JSON description 
curl https://raw.githubusercontent.com/azure/azvmimagebuilder/master/solutions/12_Creating_AIB_Security_Roles/aibRoleNetworking.json -o aibRoleNetworking.json

# Create a unique role name to avoid clashes in the same domain
netRoleDefName="Azure Image Builder Network Def"$(date +'%s')

# Update the JSON definition using stream editor
sed -i -e "s/<subscriptionID>/$subscriptionID/g" aibRoleNetworking.json
sed -i -e "s/<vnetRgName>/$vnetRgName/g" aibRoleNetworking.json
sed -i -e "s/Azure Image Builder Service Networking Role/$netRoleDefName/g" aibRoleNetworking.json

# Create a custom role from the aibRoleNetworking.json description file.
az role definition create --role-definition ./aibRoleNetworking.json

# Get the user-assigned managed identity id
imgBuilderCliId=$(az identity show -g $imageResourceGroup -n $identityName | grep "clientId" | cut -c16- | tr -d '",')

# Grant the custom role to the user-assigned managed identity for Azure Image Builder.
az role assignment create \
    --assignee $imgBuilderCliId \
    --role $netRoleDefName \
    --scope /subscriptions/$subscriptionID/resourceGroups/$VnetResourceGroup
```

## <a name="using-managed-identity-for-azure-storage-access"></a>Felügyelt identitás használata az Azure Storage-hozzáféréshez

Ha seemlessly szeretne hitelesíteni az Azure Storage-ban, és privát tárolókat használ, az Azure rendszerkép-készítőnek egy felhasználó által hozzárendelt felügyelt identitásra van szüksége. Az Azure rendszerkép-készítő az identitás használatával hitelesíti az Azure Storage-t.

> [!NOTE]
> Az Azure rendszerkép-szerkesztő csak az identitást használja a rendszerkép-sablon küldési idején. A Build virtuális gép nem fér hozzá az identitáshoz a rendszerkép létrehozása során.

A felhasználó által hozzárendelt felügyelt identitás létrehozásához használja az Azure CLI-t.

```azurecli
az role assignment create \
    --assignee <Image Builder client ID> \
    --role "Storage Blob Data Reader" \
    --scope /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Storage/storageAccounts/$scriptStorageAcc/blobServices/default/containers/<Storage account container>
```

A rendszerkép-szerkesztő sablonban meg kell adnia a felhasználó által hozzárendelt felügyelt identitást.

```json
    "type": "Microsoft.VirtualMachineImages/imageTemplates",
    "apiVersion": "2019-05-01-preview",
    "location": "<Region>",
    ..
    "identity": {
    "type": "UserAssigned",
          "userAssignedIdentities": {
            "<Image Builder ID>": {}     
        }
```

Cserélje le a következő helyőrző beállításokat:

| Beállítás | Leírás |
|---------|-------------|
| \<Region\> | Sablon régiója |
| \<Resource group\> | Erőforráscsoport |
| \<Storage account container\> | Tárfiók tárolójának neve |
| \<Subscription ID\> | Azure-előfizetés |

A felhasználó által hozzárendelt felügyelt identitással kapcsolatos további információkért tekintse meg az [Egyéni rendszerkép létrehozása, amely azure User-Assigned felügyelt identitást használ az Azure Storage-hoz való seemlessly eléréséhez](https://docs.microsoft.com/azure/virtual-machines/linux/image-builder-user-assigned-identity). A rövid útmutató végigvezeti a felhasználó által hozzárendelt felügyelt identitás létrehozásán és konfigurálásán egy Storage-fiók eléréséhez.

## <a name="next-steps"></a>Következő lépések

További információ: az [Azure rendszerkép-készítő áttekintése](../image-builder-overview.md).
