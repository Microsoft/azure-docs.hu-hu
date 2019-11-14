---
title: Oktatóanyag – Azure-beli virtuális gépek szabályozása az Azure CLI-vel
description: Ez az oktatóanyag bemutatja, hogyan kezelheti Azure-beli virtuális gépeit az RBAC, szabályzatok, zárolások és címkék alkalmazásával az Azure CLI-vel
services: virtual-machines-linux
documentationcenter: virtual-machines
author: tfitzmac
manager: gwallace
editor: tysonn
ms.service: virtual-machines-linux
ms.workload: infrastructure
ms.tgt_pltfrm: vm-linux
ms.topic: tutorial
ms.date: 09/30/2019
ms.author: tomfitz
ms.custom: mvc
ms.openlocfilehash: e9475f4226a65ad06d45170e0ab802c6cdb79d8f
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034543"
---
# <a name="tutorial-learn-about-linux-virtual-machine-governance-with-azure-cli"></a>Oktatóanyag: A Linux rendszerű virtuális gépek Azure CLI-vel történő vezérlése

[!INCLUDE [Resource Manager governance introduction](../../../includes/resource-manager-governance-intro.md)]

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

Ha az Azure CLI helyi telepítését és használatát választja, akkor ehhez az oktatóanyaghoz az Azure CLI 2.0.30-as vagy újabb verziójára lesz szüksége. A verzió azonosításához futtassa a következőt: `az --version`. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="understand-scope"></a>A hatókör bemutatása

[!INCLUDE [Resource Manager governance scope](../../../includes/resource-manager-governance-scope.md)]

Ebben az oktatóanyagban minden kezelési beállítást egy erőforráscsoportra alkalmaz, így egyszerűen eltávolíthatja a beállításokat, ha elkészült.

Hozzuk létre az erőforráscsoportot.

```azurecli-interactive
az group create --name myResourceGroup --location "East US"
```

Az erőforráscsoport jelenleg üres.

## <a name="role-based-access-control"></a>Szerepköralapú hozzáférés-vezérlés

Győződjön meg arról, hogy az intézmény felhasználói megfelelő hozzáférési szinttel rendelkeznek ezekhez az erőforrásokhoz. Nem ajánlott korlátlan hozzáférést nyújtania a felhasználóknak, de fontos biztosítania, hogy el tudják végezni a munkájukat. A [Szerepköralapú hozzáférés-vezérléssel](../../role-based-access-control/overview.md) kezelheti, hogy az egyes hatókörökben mely felhasználók hajthatnak végre adott műveleteket.

A szerepkör-hozzárendelések létrehozásához és eltávolításához a felhasználóknak `Microsoft.Authorization/roleAssignments/*` hozzáféréssel kell rendelkezniük. Ez a hozzáférés a tulajdonosi vagy a felhasználói hozzáférés rendszergazdájának szerepkörével adható meg.

A virtuálisgép-megoldások kezeléséhez három erőforrás-specifikus szerepkör létezik, amelyek biztosítják a leggyakrabban szükséges hozzáféréseket:

* [Virtuális gépek közreműködője](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor)
* [Hálózati közreműködő](../../role-based-access-control/built-in-roles.md#network-contributor)
* [Tárfiók-közreműködő](../../role-based-access-control/built-in-roles.md#storage-account-contributor)

Ahelyett, hogy szerepköröket rendelne az egyéni felhasználókhoz, gyakran célszerűbb egy azokat a felhasználókat tartalmazó Azure Active Directory-csoportot használni, akiknek hasonló műveleteket kell elvégezniük. Ezután rendelje hozzá a csoportot a megfelelő szerepkörhöz. Ehhez a cikkhez használhat egy meglévő csoportot a virtuális gép kezeléséhez, vagy a portálon [létrehozhat egy Azure Active Directory-csoportot](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Egy új csoport létrehozása vagy egy meglévő csoport megkeresése után az [az role assignment create](https://docs.microsoft.com/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) paranccsal rendelje hozzá az új Azure Active Directory-csoportot az erőforráscsoport Virtuális gépek közreműködője szerepköréhez.

```azurecli-interactive
adgroupId=$(az ad group show --group <your-group-name> --query objectId --output tsv)

az role assignment create --assignee-object-id $adgroupId --role "Virtual Machine Contributor" --resource-group myResourceGroup
```

Ha olyan hibaüzenetet kap, amely **\<GUID-> nem létezik a címtárban**, az új csoport nem lett propagálva az egész Azure Active Directory. Próbálja meg ismét futtatni a parancsot.

A folyamatot általában a *Hálózati közreműködő* és a *Tárfiók-közreműködő* szerepkörön is végre kell hajtani, hogy a felhasználók megkapják az üzembe helyezett erőforrások kezeléséhez szükséges jogosultságokat. Ebben a cikkben kihagyhatja ezeket a lépéseket.

## <a name="azure-policy"></a>Azure szabályzat

Az [Azure Policy](../../governance/policy/overview.md) segítségével ellenőrizheti, hogy az előfizetés összes erőforrása megfelel-e a vállalati szabványoknak. Az előfizetése már számos szabályzatdefinícióval rendelkezik. Az elérhető szabályzatdefiníciók megtekintéséhez használja az [az policy definition list](https://docs.microsoft.com/cli/azure/policy/definition?view=azure-cli-latest#az-policy-definition-list) parancsot:

```azurecli-interactive
az policy definition list --query "[].[displayName, policyType, name]" --output table
```

Itt láthatja a meglévő szabályzatdefiníciókat. A szabályzat típusa lehet **Beépített** vagy **Egyéni**. Keresse meg azokat a definíciókat, amelyek az Ön által hozzárendelni kívánt feltételt írják le. Ebben a cikkben olyan szabályzatokat rendel hozzá, amelyek:

* Korlátozzák az összes erőforrás helyét.
* Korlátozzák a virtuális gépek termékváltozatait.
* Naplózzák a felügyelt lemezeket nem használó virtuális gépeket.

A következő példában három olyan szabályzatdefiníciót fog lekérni, amely a megjelenítendő néven alapul. Az [az policy assignment create](https://docs.microsoft.com/cli/azure/policy/assignment?view=azure-cli-latest#az-policy-assignment-create) paranccsal rendelje hozzá ezeket a definíciókat az erőforráscsoporthoz. Egyes szabályzatoknál paraméterértékekkel határozhatja meg az engedélyezett értékeket.

```azurecli-interactive
# Get policy definitions for allowed locations, allowed SKUs, and auditing VMs that don't use managed disks
locationDefinition=$(az policy definition list --query "[?displayName=='Allowed locations'].name | [0]" --output tsv)
skuDefinition=$(az policy definition list --query "[?displayName=='Allowed virtual machine SKUs'].name | [0]" --output tsv)
auditDefinition=$(az policy definition list --query "[?displayName=='Audit VMs that do not use managed disks'].name | [0]" --output tsv)

# Assign policy for allowed locations
az policy assignment create --name "Set permitted locations" \
  --resource-group myResourceGroup \
  --policy $locationDefinition \
  --params '{ 
      "listOfAllowedLocations": {
        "value": [
          "eastus", 
          "eastus2"
        ]
      }
    }'

# Assign policy for allowed SKUs
az policy assignment create --name "Set permitted VM SKUs" \
  --resource-group myResourceGroup \
  --policy $skuDefinition \
  --params '{ 
      "listOfAllowedSKUs": {
        "value": [
          "Standard_DS1_v2", 
          "Standard_E2s_v2"
        ]
      }
    }'

# Assign policy for auditing unmanaged disks
az policy assignment create --name "Audit unmanaged disks" \
  --resource-group myResourceGroup \
  --policy $auditDefinition
```

Az előző példa azt feltételezi, hogy Ön már ismeri a szabályzat paramétereit. A paraméterek megtekintéséhez használja a következő kódot:

```azurecli-interactive
az policy definition show --name $locationDefinition --query parameters
```

## <a name="deploy-the-virtual-machine"></a>A virtuális gép üzembe helyezése

A szerepkörök és szabályzatok hozzárendelése megtörtént, így megkezdheti a megoldás üzembe helyezését. Az alapértelmezett méret a Standard_DS1_v2, amely az egyik engedélyezett termékváltozat. A parancs SSH-kulcsokat hoz létre, ha még nem léteznek az alapértelmezett helyen.

```azurecli-interactive
az vm create --resource-group myResourceGroup --name myVM --image UbuntuLTS --generate-ssh-keys
```

Az üzembe helyezés befejezése után további kezelési beállításokat alkalmazhat a megoldáson.

## <a name="lock-resources"></a>Erőforrások zárolása

Az [Erőforrás-zárolások](../../azure-resource-manager/resource-group-lock-resources.md) megakadályozzák, hogy a cég vagy intézmény felhasználói véletlenül töröljék vagy módosítsák a kritikus erőforrásokat. Az erőforrás-zárolások a szerepköralapú hozzáférés-vezérléssel szemben minden felhasználóra és szerepkörre érvényes korlátozásokat alkalmaznak. A zárolási szintet *CanNotDelete* (nem törölhető) vagy *ReadOnly* (csak olvasható) értékre állíthatja be.

A felügyeleti zárolások létrehozásához vagy törléséhez hozzáféréssel kell rendelkeznie a `Microsoft.Authorization/locks/*` műveletekhez. A beépített szerepkörök esetén ezek a műveletek csak a **Tulajdonosi** és a **Felhasználói hozzáférés rendszergazdájának** vannak engedélyezve.

A virtuális gép és a hálózati biztonsági csoport zárolásához használja az [az lock create](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest#az-resource-lock-create) parancsot:

```azurecli-interactive
# Add CanNotDelete lock to the VM
az lock create --name LockVM \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVM \
  --resource-type Microsoft.Compute/virtualMachines

# Add CanNotDelete lock to the network security group
az lock create --name LockNSG \
  --lock-type CanNotDelete \
  --resource-group myResourceGroup \
  --resource-name myVMNSG \
  --resource-type Microsoft.Network/networkSecurityGroups
```

A zárolások teszteléséhez futtassa az alábbi parancsot:

```azurecli-interactive 
az group delete --name myResourceGroup
```

Megjelenik egy hibaüzenet, amely szerint a törlési művelet nem hajtható végre egy zárolás miatt. Az erőforráscsoport csak akkor törölhető, ha külön eltávolítja a zárolásokat. Ezt a lépést [Az erőforrások eltávolítása](#clean-up-resources) című szakasz ismerteti.

## <a name="tag-resources"></a>Erőforrások címkézése

A [címkézéssel](../../azure-resource-manager/resource-group-using-tags.md) logikusan, kategóriák szerint rendszerezheti az Azure-erőforrásokat. Minden címke egy névből és egy értékből áll. Alkalmazhatja például a „Környezet” nevet és az „Éles” értéket az összes éles üzemben használt erőforrásra.

[!INCLUDE [Resource Manager governance tags CLI](../../../includes/resource-manager-governance-tags-cli.md)]

Egy virtuális gép címkézéséhez használja az [az resource tag](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) parancsot. Az erőforrás meglévő címkéit nem őrzi meg a rendszer.

```azurecli-interactive
az resource tag -n myVM \
  -g myResourceGroup \
  --tags Dept=IT Environment=Test Project=Documentation \
  --resource-type "Microsoft.Compute/virtualMachines"
```

### <a name="find-resources-by-tag"></a>Erőforrások keresése címke szerint

Az adott címkenévvel és -értékkel rendelkező erőforrások kereséséhez használja az [az resource list](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) parancsot:

```azurecli-interactive
az resource list --tag Environment=Test --query [].name
```

A visszaadott értékeket kezelési feladatokhoz, például az adott címkeértékkel rendelkező összes virtuális gép leállításához használhatja.

```azurecli-interactive
az vm stop --ids $(az resource list --tag Environment=Test --query "[?type=='Microsoft.Compute/virtualMachines'].id" --output tsv)
```

### <a name="view-costs-by-tag-values"></a>Költségek megtekintése címkeértékek szerint

[!INCLUDE [Resource Manager governance tags billing](../../../includes/resource-manager-governance-tags-billing.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A zárolt hálózati biztonsági csoport nem törölhető a zárolás eltávolításáig. A zárolás eltávolításához kérje le a zárolások azonosítóit, és adja meg őket az [az lock delete](https://docs.microsoft.com/cli/azure/resource/lock?view=azure-cli-latest#az-resource-lock-delete) parancsban:

```azurecli-interactive
vmlock=$(az lock show --name LockVM \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Compute/virtualMachines \
  --resource-name myVM --output tsv --query id)
nsglock=$(az lock show --name LockNSG \
  --resource-group myResourceGroup \
  --resource-type Microsoft.Network/networkSecurityGroups \
  --resource-name myVMNSG --output tsv --query id)
az lock delete --ids $vmlock $nsglock
```

Ha már nincs rá szükség, a [az group delete](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-delete) paranccsal eltávolítható az erőforráscsoport, a virtuális gép és az összes kapcsolódó erőforrás. Lépjen ki az SSH-munkamenetből a virtuális gépre, majd törölje az erőforrásokat a következő módon:

```azurecli-interactive 
az group delete --name myResourceGroup
```


## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban létrehozott egy egyéni virtuálisgép-rendszerképet. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Felhasználók hozzárendelése szerepkörhöz
> * Szabványokat kényszerítő szabályzatok alkalmazása
> * Kritikus erőforrások védelme zárolásokkal
> * Erőforrások címkézése számlázáshoz és felügyelethez

Folytassa a következő oktatóanyaggal, amelyből megtudhatja, hogyan azonosíthatja a módosításokat, és hogyan kezelheti a csomagok frissítéseit egy virtuális gépen.

> [!div class="nextstepaction"]
> [Virtuális gépek kezelése](tutorial-config-management.md)

