---
title: Felügyelt identitás engedélyezése tárolócsoportban
description: Megtudhatja, hogyan engedélyezheti a felügyelt identitásokat Azure Container Instances, amelyek más Azure-szolgáltatásokkal is hitelesíthetők
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: f8f3c646487d86f4e1bce13ccbf28992b8b1497a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107763988"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Felügyelt identitások használata az Azure Container Instancesszel

Az [Azure-erőforrások felügyelt](../active-directory/managed-identities-azure-resources/overview.md) identitásai használatával olyan kódot futtathat Azure Container Instances, amely más Azure-szolgáltatásokkal kommunikál – anélkül, hogy kódban őrizze meg a titkos kódokat és a hitelesítő adatokat. A szolgáltatás egy Azure Container Instances üzembe helyezést biztosít egy automatikusan felügyelt identitással a Azure Active Directory.

Ebben a cikkben további információt talál a felügyelt identitásokkal kapcsolatban a Azure Container Instances és:

> [!div class="checklist"]
> * Felhasználó vagy rendszer által hozzárendelt identitás engedélyezése tárolócsoportban
> * Hozzáférés megadása az identitás számára egy Azure Key Vaulthoz
> * Kulcstartó elérése futó tárolóból a felügyelt identitással

A példákat adaptálva engedélyezze és használja az identitásokat Azure Container Instances Azure-szolgáltatások eléréséhez. Ezek a példák interaktívak. A gyakorlatban azonban a tároló rendszerképe kód futtatásával fér hozzá az Azure-szolgáltatásokhoz.
 
> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt. A felügyelt identitások jelenleg a Azure Container Instances csak Linux-tárolók esetében támogatottak, Windows-tárolók esetében még nem.

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

A futó tárolókban található felügyelt identitással bármely [Azure AD-hitelesítést](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) támogató szolgáltatásban hitelesítheti magát anélkül, hogy a hitelesítő adatokat a tároló kódban kezelnie kell. Az AD-hitelesítést nem támogató szolgáltatások esetén a titkos kulcsokat tárolhatja egy Azure-kulcstartóban, és a felügyelt identitással elérheti a kulcstartót a hitelesítő adatok lekéréséhez. A felügyelt identitások használatával kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="enable-a-managed-identity"></a>Felügyelt identitás engedélyezése

 Tárolócsoport létrehozásakor engedélyezzen egy vagy több felügyelt identitást egy [ContainerGroupIdentity tulajdonság beállításával.](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) A felügyelt identitásokat a tárolócsoport futtatása után is engedélyezheti vagy frissítheti – bármelyik művelet újraindítja a tárolócsoportot. Egy új vagy meglévő tárolócsoport identitásának beállítását az Azure CLI, egy Resource Manager-sablon, egy YAML-fájl vagy egy másik Azure-eszköz használatával állíthatja be. 

Azure Container Instances a felügyelt Azure-identitások mindkét típusát támogatja: a felhasználó által hozzárendelt és a rendszer által hozzárendelt identitásokat. A tárolócsoporton engedélyezheti a rendszer által hozzárendelt identitást, egy vagy több felhasználó által hozzárendelt identitást vagy mindkét identitástípust. Ha nem ismeri az Azure-erőforrások felügyelt identitását, tekintse meg az [áttekintést.](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="use-a-managed-identity"></a>Felügyelt identitás használata

Felügyelt identitás használata esetén az identitásnak hozzáférést kell biztosítani egy vagy több Azure-szolgáltatási erőforráshoz (például webalkalmazáshoz, kulcstartóhoz vagy tárfiókhoz) az előfizetésben. A felügyelt identitások futó tárolókban való használata hasonló az Azure-beli virtuális gépeken használt identitáshoz. Tekintse meg [a](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)virtuális gépek jogkivonatok , Azure PowerShell az Azure CLI vagy az [Azure SDK-k használatával kapcsolatos útmutatót.](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md) [](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)

### <a name="limitations"></a>Korlátozások

* A virtuális hálózaton üzembe helyezett tárolócsoportban jelenleg nem használhat felügyelt identitást.
* Tárolócsoport létrehozásakor nem használhat felügyelt identitást egy rendszerkép le Azure Container Registry egy tárolócsoport létrehozásakor. Az identitás csak futó tárolón belül érhető el.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.49-es vagy újabb verziójára van szükség. Ha a Azure Cloud Shell, a legújabb verzió már telepítve van.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása

A cikkben látható példák egy felügyelt identitást Azure Container Instances egy Azure Key Vault titkos kulcsának eléréséhez. 

Először hozzon létre egy erőforráscsoportot *myResourceGroup* néven az *eastus* helyen az alábbi [az group create](/cli/azure/group#az_group_create) paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Kulcstartó létrehozásához használja az [az keyvault create](/cli/azure/keyvault#az_keyvault_create) parancsot. Mindenképpen egyedi kulcstartónevet adjon meg. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Titkos mintakulcs tárolása a key vaultban [az az keyvault secret set paranccsal:](/cli/azure/keyvault/secret#az_keyvault_secret_set)

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Folytassa az alábbi példákkal a kulcstartó felhasználó vagy rendszer által hozzárendelt felügyelt identitás használatával való eléréséhez a Azure Container Instances.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>1. példa: Felhasználó által hozzárendelt identitás használata az Azure Key Vault eléréséhez

### <a name="create-an-identity"></a>Identitás létrehozása

Először hozzon létre egy identitást az előfizetésében [az az identity create paranccsal.](/cli/azure/identity#az_identity_create) Használhatja ugyanazt az erőforráscsoportot, mint a kulcstartó létrehozásához, vagy használhat egy másikat is.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Ha az identitást a következő lépésekben használnia kell, használja [az az identity show](/cli/azure/identity#az_identity_show) parancsot az identitás szolgáltatásnév-azonosítójának és erőforrás-azonosítójának változókban való tárolására.

```azurecli-interactive
# Get service principal ID of the user-assigned identity
spID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query principalId --output tsv)

# Get resource ID of the user-assigned identity
resourceID=$(az identity show \
  --resource-group myResourceGroup \
  --name myACIId \
  --query id --output tsv)
```

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Felhasználó által hozzárendelt identitás hozzáférésének megadása a kulcstartóhoz

Futtassa a [következő az keyvault set-policy parancsot](/cli/azure/keyvault) egy hozzáférési szabályzat kulcstartón való beállítására. Az alábbi példa lehetővé teszi, hogy a felhasználó által hozzárendelt identitás titkos kulcsokat szerezze be a kulcstartóból:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Felhasználó által hozzárendelt identitás engedélyezése tárolócsoporton

Futtassa a következő [az container create parancsot](/cli/azure/container#az_container_create) egy tárolópéldány a Microsoft rendszerképe alapján való `azure-cli` létrehozásához. Ez a példa egy egytárolós csoportot biztosít, amely interaktív módon használható az Azure CLI futtatására más Azure-szolgáltatások eléréséhez. Ebben a szakaszban csak az alap operációs rendszert használjuk. Az Azure CLI tárolóban való használatára vonatkozó példáért lásd: Enable system-assigned identity on a container group (Rendszer által hozzárendelt [identitás engedélyezése tárolócsoporton).](#enable-system-assigned-identity-on-a-container-group) 

A `--assign-identity` paraméter átadja a felhasználó által hozzárendelt felügyelt identitást a csoportnak. A hosszan futó parancs futtatja a tárolót. Ez a példa ugyanazt az erőforráscsoportot használja, mint a kulcstartó létrehozásához, de megadhat egy másikat is.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Ellenőrizze az állapotát az [az container show paranccsal.](/cli/azure/container#az_container_show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A kimenet szakasza az alábbihoz hasonlóan néz ki, és azt mutatja, hogy az identitás be van állítva `identity` a tárolócsoportban. A alatt a következőben létrehozott identitás szolgáltatásnév `principalID` `userAssignedIdentities` Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "null",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "UserAssigned",
    "userAssignedIdentities": {
      "/subscriptions/xxxxxxxx-0903-4b79-a55a-xxxxxxxxxxxx/resourcegroups/danlep1018/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myACIId": {
        "clientId": "xxxxxxxx-5523-45fc-9f49-xxxxxxxxxxxx",
        "principalId": "xxxxxxxx-f25b-4895-b828-xxxxxxxxxxxx"
      }
    }
  },
[...]
```

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>Felhasználó által hozzárendelt identitás használata titkos kulcs lekért kulcstartóból

Most már használhatja a felügyelt identitást a futó tárolópéldányon belül a kulcstartó eléréséhez. Először indítson el egy Bash-rendszerhéjat a tárolóban:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Futtassa a következő parancsokat a tároló bash rendszerhéjában. Ha le kell szereznie egy hozzáférési jogkivonatot, Azure Active Directory kulcstartóban való hitelesítéshez használja, futtassa a következő parancsot:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Kimenet:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Ha a hozzáférési jogkivonatot egy változóban tárolja, hogy a következő parancsokban is használni tudja a hitelesítést, futtassa a következő parancsot:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Most használja a hozzáférési jogkivonatot a Key Vaultban való hitelesítéshez és egy titkos kulcs olvassa el. Mindenképpen helyettesítse be a kulcstartó nevét az URL-címben *(https: \/ /mykeyvault.vault.azure.net/...*):

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A válasz az alábbihoz hasonló, és a titkos adatokat mutatja. A kódban elemezné ezt a kimenetet a titkos kód beszerzéséhez. Ezt követően egy későbbi műveletben a titkos okkal férhet hozzá egy másik Azure-erőforráshoz.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>2. példa: Az Azure Key Vault elérése rendszer által hozzárendelt identitással

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Rendszer által hozzárendelt identitás engedélyezése tárolócsoporton

Az alábbi [az container create paranccsal](/cli/azure/container#az_container_create) hozzon létre egy tárolópéldányt a Microsoft rendszerképe `azure-cli` alapján. Ez a példa egy egytárolós csoportot biztosít, amely interaktívan használható az Azure CLI futtatására más Azure-szolgáltatások eléréséhez. 

A `--assign-identity` paraméter további érték nélkül lehetővé teszi a rendszer által hozzárendelt felügyelt identitást a csoporton. Az identitás hatóköre a tárolócsoport erőforráscsoportja. A hosszan futó parancs tartja futni a tárolót. Ez a példa ugyanazt az erőforráscsoportot használja, amelyet a kulcstartó létrehozásához használ, amely az identitás hatókörében van.

```azurecli-interactive
# Get the resource ID of the resource group
rgID=$(az group show --name myResourceGroup --query id --output tsv)

# Create container group with system-managed identity
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity --scope $rgID \
  --command-line "tail -f /dev/null"
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Ellenőrizze az állapotát az [az container show paranccsal.](/cli/azure/container#az_container_show)

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A kimenet szakasza az alábbihoz hasonló, és azt mutatja, hogy a rendszer által hozzárendelt identitás a következő `identity` Azure Active Directory:

```console
[...]
"identity": {
    "principalId": "xxxxxxxx-528d-7083-b74c-xxxxxxxxxxxx",
    "tenantId": "xxxxxxxx-f292-4e60-9122-xxxxxxxxxxxx",
    "type": "SystemAssigned",
    "userAssignedIdentities": null
},
[...]
```

Állítson be egy változót az `principalId` identitás értékére (a szolgáltatásnév-azonosítóra) a későbbi lépésekben való használathoz.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Tárolócsoport-hozzáférés megadása a kulcstartóhoz

Futtassa a [következő az keyvault set-policy parancsot](/cli/azure/keyvault) egy hozzáférési szabályzat kulcstartón való beállítására. Az alábbi példa lehetővé teszi, hogy a rendszer által felügyelt identitás lekért titkos kulcsokat a kulcstartóból:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>Titkos kulcs lekérte a Key Vaultból tárolócsoport-identitás használatával

Most már használhatja a felügyelt identitást a kulcstartó eléréséhez a futó tárolópéldányon belül. Először indítson el egy Bash-rendszerhéjat a tárolóban:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Futtassa a következő parancsokat a tároló bash rendszerhéjában. Először jelentkezzen be az Azure CLI-be a felügyelt identitással:

```bash
az login --identity
```

A futó tárolóból olvassa be a titkos adatokat a kulcstartóból:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

A titkos adatokat a program lekéri:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Felügyelt identitás engedélyezése Resource Manager használatával

Ha engedélyezni szeretné a felügyelt identitást egy tárolócsoportban egy Resource Manager [sablonnal,](container-instances-multi-container-group.md)állítsa be az objektum tulajdonságát `identity` egy `Microsoft.ContainerInstance/containerGroups` `ContainerGroupIdentity` objektummal. Az alábbi kódrészletek a különböző `identity` forgatókönyvekhez konfigurált tulajdonságot mutatják be. Lásd a [Resource Manager sablonreferenciáját.](/azure/templates/microsoft.containerinstance/containergroups) Adja meg legalább a `apiVersion` `2018-10-01` értéket.

### <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

A felhasználó által hozzárendelt identitás az űrlap erőforrás-azonosítója:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Egy vagy több felhasználó által hozzárendelt identitást engedélyezhet.

```json
"identity": {
    "type": "UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
```

### <a name="system-assigned-identity"></a>Rendszer által hozzárendelt identitás

```json
"identity": {
    "type": "SystemAssigned"
    }
```

### <a name="system--and-user-assigned-identities"></a>Rendszer- és felhasználó által hozzárendelt identitások

A tárolócsoporton engedélyezheti a rendszer által hozzárendelt identitást és egy vagy több felhasználó által hozzárendelt identitást is.

```json
"identity": {
    "type": "System Assigned, UserAssigned",
    "userAssignedIdentities": {
        "myResourceID1": {
            }
        }
    }
...
```

## <a name="enable-managed-identity-using-yaml-file"></a>Felügyelt identitás engedélyezése YAML-fájl használatával

Ha egy [YAML-fájllal](container-instances-multi-container-yaml.md)üzembe helyezett tárolócsoportban szeretné engedélyezni a felügyelt identitást, használja a következő YAML-t.
Adja meg a minimális `apiVersion` `2018-10-01` értéket.

### <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

A felhasználó által hozzárendelt identitás az űrlap erőforrás-azonosítója 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Egy vagy több felhasználó által hozzárendelt identitást engedélyezhet.

```YAML
identity:
  type: UserAssigned
  userAssignedIdentities:
    {'myResourceID1':{}}
```

### <a name="system-assigned-identity"></a>Rendszer által hozzárendelt identitás

```YAML
identity:
  type: SystemAssigned
```

### <a name="system--and-user-assigned-identities"></a>Rendszer- és felhasználó által hozzárendelt identitások

A tárolócsoporton engedélyezheti a rendszer által hozzárendelt identitást és egy vagy több felhasználó által hozzárendelt identitást is.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben a felügyelt identitásokkal és a Azure Container Instances a következővel:

> [!div class="checklist"]
> * Felhasználó vagy rendszer által hozzárendelt identitás engedélyezése egy tárolócsoportban
> * Hozzáférés megadása az identitás számára egy Azure Key Vaulthoz
> * Kulcstartó elérése futó tárolóból a felügyelt identitással

* További információ az [Azure-erőforrások felügyelt identitási szolgáltatásról.](../active-directory/managed-identities-azure-resources/index.yml)

* Tekintsen meg [egy Azure Go SDK-példát](https://medium.com/@samkreter/c98911206328) arra, hogyan használhat felügyelt identitást egy kulcstartó eléréséhez a Azure Container Instances.
