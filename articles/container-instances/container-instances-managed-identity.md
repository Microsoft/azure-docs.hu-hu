---
title: Felügyelt identitás engedélyezése a tároló csoportban
description: Megtudhatja, hogyan engedélyezheti a felügyelt identitást olyan Azure Container Instancesban, amelyek más Azure-szolgáltatásokkal is hitelesíthetők
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: a0d029e39122ca7bb858103f4d7f88e2536850d5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102198319"
---
# <a name="how-to-use-managed-identities-with-azure-container-instances"></a>Felügyelt identitások használata az Azure Container Instancesszel

[Felügyelt identitások használata az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) olyan Azure Container instances kód futtatásához, amely más Azure-szolgáltatásokkal is együttműködik – anélkül, hogy az összes titkot vagy hitelesítő adatot megtartja a kódban. A szolgáltatás Azure Container Instances központi telepítést biztosít automatikusan felügyelt identitással Azure Active Directoryban.

Ebben a cikkben további információt talál a felügyelt identitásokról Azure Container Instances és:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszerhez rendelt identitás engedélyezése egy tároló csoportban
> * Az identitás hozzáférésének biztosítása egy Azure Key vaulthoz
> * A felügyelt identitás használata egy futó tárolóból származó kulcstartó eléréséhez

A példákkal a többi Azure-szolgáltatás eléréséhez az identitásokat engedélyezheti és használhatja Azure Container Instancesban. Ezek a példák interaktívak. A gyakorlatban azonban a tároló lemezképei programkódot futtatnak az Azure-szolgáltatások eléréséhez.
 
> [!IMPORTANT]
> Ez a szolgáltatás jelenleg előzetes kiadásban elérhető. Az előzetes verziók azzal a feltétellel érhetők el, hogy Ön beleegyezik a [kiegészítő használati feltételekbe](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). A szolgáltatás néhány eleme megváltozhat a nyilvános rendelkezésre állás előtt. A Azure Container Instances felügyelt identitások jelenleg csak Linux-tárolókkal és a Windows-tárolókkal még nem támogatottak.

## <a name="why-use-a-managed-identity"></a>Miért érdemes felügyelt identitást használni?

Felügyelt identitást használhat egy futó tárolóban bármely olyan szolgáltatás hitelesítéséhez, [amely támogatja az Azure ad-hitelesítést](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) , és nem kezeli a hitelesítő adatokat a tároló kódjában. Az AD-hitelesítést nem támogató szolgáltatások esetében az Azure Key vaultban tárolhatja a titkos kulcsokat, és a felügyelt identitás használatával elérheti a Key vaultot a hitelesítő adatok lekéréséhez. A felügyelt identitás használatával kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md)

### <a name="enable-a-managed-identity"></a>Felügyelt identitás engedélyezése

 Amikor létrehoz egy tároló csoportot, egy vagy több felügyelt identitást egy [ContainerGroupIdentity](/rest/api/container-instances/containergroups/createorupdate#containergroupidentity) tulajdonság beállításával engedélyezhet. Emellett engedélyezheti vagy frissítheti a felügyelt identitásokat, miután egy tároló csoport fut – vagy a művelet hatására a tároló csoport újraindul. Az identitások új vagy meglévő YAML való beállításához használja az Azure CLI-t, egy Resource Manager-sablont, egy-fájlt vagy egy másik Azure-eszközt. 

Azure Container Instances a felügyelt Azure-identitások mindkét típusát támogatja: felhasználó által hozzárendelt és rendszerhez rendelt. Egy tároló csoporton engedélyezheti a rendszerhez rendelt identitást, egy vagy több felhasználó által hozzárendelt identitást vagy mindkét típusú identitást. Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintést](../active-directory/managed-identities-azure-resources/overview.md).

### <a name="use-a-managed-identity"></a>Felügyelt identitás használata

Felügyelt identitás használatához az identitásnak hozzáférést kell biztosítania egy vagy több Azure-szolgáltatási erőforráshoz (például egy webalkalmazáshoz, egy kulcstartóhoz vagy egy Storage-fiókhoz) az előfizetésben. Egy futó tárolóban felügyelt identitás használata hasonló az Azure-beli virtuális gépek identitásának használatához. Tekintse meg a virtuális gépekkel kapcsolatos útmutatót [token](../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md), [Azure POWERSHELL vagy Azure CLI](../active-directory/managed-identities-azure-resources/how-to-use-vm-sign-in.md)vagy az [Azure SDK](../active-directory/managed-identities-azure-resources/how-to-use-vm-sdk.md)-k használatával.

### <a name="limitations"></a>Korlátozások

* Jelenleg nem használhat felügyelt identitást egy virtuális hálózatra központilag telepített tároló csoportba.
* Tároló-csoport létrehozásakor nem használhat felügyelt identitást Azure Container Registry rendszerkép lekérésére. Az identitás csak futó tárolón belül érhető el.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ehhez a cikkhez az Azure CLI 2.0.49 vagy újabb verziójára van szükség. Azure Cloud Shell használata esetén a legújabb verzió már telepítve van.

## <a name="create-an-azure-key-vault"></a>Azure Key Vault létrehozása

A cikkben szereplő példák felügyelt identitást használnak Azure Container Instances egy Azure Key Vault-titok eléréséhez. 

Először hozzon létre egy erőforráscsoportot *myResourceGroup* néven az *eastus* helyen az alábbi [az group create](/cli/azure/group#az-group-create) paranccsal:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

A Key Vault létrehozásához használja az az kulcstartó [létrehozása](/cli/azure/keyvault#az-keyvault-create) parancsot. Ügyeljen arra, hogy egyedi kulcstároló-nevet adjon meg. 

```azurecli-interactive
az keyvault create \
  --name mykeyvault \
  --resource-group myResourceGroup \ 
  --location eastus
```

Hozzon létre egy minta titkos kulcsot a kulcstartóban az az Key [Vault Secret set](/cli/azure/keyvault/secret#az-keyvault-secret-set) paranccsal:

```azurecli-interactive
az keyvault secret set \
  --name SampleSecret \
  --value "Hello Container Instances" \
  --description ACIsecret --vault-name mykeyvault
```

Folytassa a következő példákkal a Key Vault elérését egy felhasználó által hozzárendelt vagy rendszer által hozzárendelt felügyelt identitás használatával Azure Container Instancesban.

## <a name="example-1-use-a-user-assigned-identity-to-access-azure-key-vault"></a>1. példa: felhasználó által hozzárendelt identitás használata az Azure Key Vault eléréséhez

### <a name="create-an-identity"></a>Identitás létrehozása

Először hozzon létre egy identitást az előfizetésben az az [Identity Create](/cli/azure/identity#az-identity-create) paranccsal. Használhatja ugyanazt az erőforráscsoportot, amely a kulcstartó létrehozásához használatos, vagy egy másikat is használhat.

```azurecli-interactive
az identity create \
  --resource-group myResourceGroup \
  --name myACIId
```

Ha az identitást a következő lépésekben szeretné használni, használja az az [Identity show](/cli/azure/identity#az-identity-show) parancsot az identitás egyszerű szolgáltatásnév és erőforrás-azonosítójának a változókban való tárolásához.

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

### <a name="grant-user-assigned-identity-access-to-the-key-vault"></a>Felhasználó által hozzárendelt identitás elérésének engedélyezése a kulcstartóhoz

A Key vaultra vonatkozó hozzáférési szabályzat beállításához futtassa a következőt az kulcstartó [set-Policy](/cli/azure/keyvault) paranccsal. A következő példa lehetővé teszi, hogy a felhasználó által hozzárendelt identitás a Key vaultból kapjon titkos kódokat:

```azurecli-interactive
 az keyvault set-policy \
    --name mykeyvault \
    --resource-group myResourceGroup \
    --object-id $spID \
    --secret-permissions get
```

### <a name="enable-user-assigned-identity-on-a-container-group"></a>Felhasználó által hozzárendelt identitás engedélyezése egy tároló csoporton

A Microsoft rendszerképe alapján hozzon létre egy tároló példányt a következő az [Container Create](/cli/azure/container#az-container-create) paranccsal `azure-cli` . Ez a példa egy egytárolós csoportot biztosít, amelyet interaktív módon használhat az Azure CLI-vel más Azure-szolgáltatásokhoz való hozzáféréshez. Ebben a szakaszban csak az alap operációs rendszer van használatban. Ha például az Azure CLI-t a tárolóban szeretné használni, tekintse [meg a rendszer által hozzárendelt identitás engedélyezése egy tároló csoporton](#enable-system-assigned-identity-on-a-container-group)című témakört. 

A `--assign-identity` paraméter a felhasználó által hozzárendelt felügyelt identitást továbbítja a csoportnak. A hosszan futó parancs megtartja a tárolót. Ez a példa ugyanazt az erőforráscsoportot használja, amelyet a kulcstartó létrehozásához használtak, de megadhat egy másikat is.

```azurecli-interactive
az container create \
  --resource-group myResourceGroup \
  --name mycontainer \
  --image mcr.microsoft.com/azure-cli \
  --assign-identity $resourceID \
  --command-line "tail -f /dev/null"
```

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Az az [Container show](/cli/azure/container#az-container-show) paranccsal tekintse meg az állapotát.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A `identity` kimenetben található szakasz a következőhöz hasonlóan néz ki, ami azt mutatja, hogy az identitás be van állítva a Container csoportban. A `principalID` alatt a `userAssignedIdentities` Azure Active Directoryban létrehozott identitás egyszerű szolgáltatásnév:

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

### <a name="use-user-assigned-identity-to-get-secret-from-key-vault"></a>A Key vaultból a felhasználó által hozzárendelt identitás használatával szerezheti be a titkos kulcsot

Most már használhatja a felügyelt identitást a futó tároló példányán a kulcstartó eléréséhez. Először indítson el egy bash-rendszerhéjat a tárolóban:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Futtassa a következő parancsokat a tárolóban lévő bash-rendszerhéjban. A következő parancs futtatásával kérhet hozzáférési tokent a Key Vault hitelesítéséhez Azure Active Directory használatával:

```bash
curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true -s
```

Kimenet:

```bash
{"access_token":"xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSIsImtpZCI6Imk2bEdrM0ZaenhSY1ViMkMzbkVRN3N5SEpsWSJ9......xxxxxxxxxxxxxxxxx","refresh_token":"","expires_in":"28799","expires_on":"1539927532","not_before":"1539898432","resource":"https://vault.azure.net/","token_type":"Bearer"}
```

Ha a hozzáférési tokent egy változóban szeretné tárolni a következő parancsokban a hitelesítéshez, futtassa a következő parancsot:

```bash
token=$(curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true | jq -r '.access_token')

```

Most használja a hozzáférési tokent a Key Vault hitelesítéséhez, és olvassa el a titkos kulcsot. Ügyeljen arra, hogy a Key Vault nevét a következő URL-címben (*https: \/ /mykeyvault.Vault.Azure.net/..*.) cserélje le:

```bash
curl https://mykeyvault.vault.azure.net/secrets/SampleSecret/?api-version=2016-10-01 -H "Authorization: Bearer $token"
```

A válasz a következőhöz hasonlóan néz ki, amely a titkos kulcsot mutatja. A kódban elemezheti ezt a kimenetet a titok beszerzéséhez. Ezután egy későbbi művelet titkát használva férhet hozzá egy másik Azure-erőforráshoz.

```bash
{"value":"Hello Container Instances","contentType":"ACIsecret","id":"https://mykeyvault.vault.azure.net/secrets/SampleSecret/xxxxxxxxxxxxxxxxxxxx","attributes":{"enabled":true,"created":1539965967,"updated":1539965967,"recoveryLevel":"Purgeable"},"tags":{"file-encoding":"utf-8"}}
```

## <a name="example-2-use-a-system-assigned-identity-to-access-azure-key-vault"></a>2. példa: a rendszer által hozzárendelt identitás használata az Azure Key Vault eléréséhez

### <a name="enable-system-assigned-identity-on-a-container-group"></a>Rendszer által hozzárendelt identitás engedélyezése egy tároló csoporton

A Microsoft rendszerképe alapján hozzon létre egy tároló példányt a következő az [Container Create](/cli/azure/container#az-container-create) paranccsal `azure-cli` . Ez a példa egy egytárolós csoportot biztosít, amelyet interaktív módon használhat az Azure CLI-vel más Azure-szolgáltatásokhoz való hozzáféréshez. 

A `--assign-identity` további érték nélküli paraméter lehetővé teszi a rendszer által hozzárendelt felügyelt identitást a csoporton. Az identitás hatóköre a tároló csoport erőforráscsoporthoz tartozik. A hosszan futó parancs megtartja a tárolót. Ez a példa ugyanazt az erőforráscsoportot használja, amely a Key Vault létrehozásához használatos, amely az identitás hatókörében található.

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

Pár másodpercen belül az üzembe helyezés befejezéséről tájékoztató választ kell kapnia az Azure CLI-ről. Az az [Container show](/cli/azure/container#az-container-show) paranccsal tekintse meg az állapotát.

```azurecli-interactive
az container show \
  --resource-group myResourceGroup \
  --name mycontainer
```

A `identity` kimenetben található szakasz a következőhöz hasonlóan néz ki, amely azt mutatja, hogy a rendszerhez hozzárendelt identitást Azure Active Directory hozza létre:

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

Állítson be egy változót az `principalId` identitás (egyszerű szolgáltatásnév) értékére a későbbi lépésekben való használathoz.

```azurecli-interactive
spID=$(az container show \
  --resource-group myResourceGroup \
  --name mycontainer \
  --query identity.principalId --out tsv)
```

### <a name="grant-container-group-access-to-the-key-vault"></a>Tároló csoport hozzáférésének engedélyezése a kulcstartóhoz

A Key vaultra vonatkozó hozzáférési szabályzat beállításához futtassa a következőt az kulcstartó [set-Policy](/cli/azure/keyvault) paranccsal. A következő példa lehetővé teszi, hogy a rendszer által felügyelt identitás a Key vaultból beszerezze a titkos kulcsokat:

```azurecli-interactive
 az keyvault set-policy \
   --name mykeyvault \
   --resource-group myResourceGroup \
   --object-id $spID \
   --secret-permissions get
```

### <a name="use-container-group-identity-to-get-secret-from-key-vault"></a>A kulcstároló-identitás használata a Key vaultból való titkos kulcs beszerzéséhez

Mostantól a felügyelt identitás használatával férhet hozzá a kulcstárolóhoz a futó tároló példányán belül. Először indítson el egy bash-rendszerhéjat a tárolóban:

```azurecli-interactive
az container exec \
  --resource-group myResourceGroup \
  --name mycontainer \
  --exec-command "/bin/bash"
```

Futtassa a következő parancsokat a tárolóban lévő bash-rendszerhéjban. Először jelentkezzen be az Azure CLI-be a felügyelt identitás használatával:

```bash
az login --identity
```

A futó tárolóból kérje le a titkos kulcsot a Key vaultból:

```bash
az keyvault secret show \
  --name SampleSecret \
  --vault-name mykeyvault --query value
```

A titok értékét a rendszer lekéri:

```bash
"Hello Container Instances"
```

## <a name="enable-managed-identity-using-resource-manager-template"></a>Felügyelt identitás engedélyezése Resource Manager-sablon használatával

Ha egy [Resource Manager-sablonnal](container-instances-multi-container-group.md)szeretne felügyelt identitást engedélyezni egy tároló csoportban, állítsa az `identity` objektum tulajdonságát `Microsoft.ContainerInstance/containerGroups` `ContainerGroupIdentity` objektumra. A következő kódrészletek a `identity` különböző forgatókönyvekhez konfigurált tulajdonságot mutatják be. Tekintse meg a [Resource Manager-sablonok referenciáját](/azure/templates/microsoft.containerinstance/containergroups). Legalább a minimális `apiVersion` érték adható meg `2018-10-01` .

### <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

A felhasználó által hozzárendelt identitás az űrlap erőforrás-azonosítója:

```
"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}"
``` 

Egy vagy több felhasználó által hozzárendelt identitást is engedélyezhet.

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

### <a name="system--and-user-assigned-identities"></a>Rendszer-és felhasználó által hozzárendelt identitások

Egy tároló csoportban engedélyezheti a rendszerhez rendelt identitást és egy vagy több felhasználó által hozzárendelt identitást is.

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

## <a name="enable-managed-identity-using-yaml-file"></a>Felügyelt identitás engedélyezése a YAML-fájllal

Ahhoz, hogy egy [YAML-fájl](container-instances-multi-container-yaml.md)használatával üzembe helyezett tároló csoport felügyelt identitást engedélyezzen, a következő YAML kell megadnia.
Legalább a minimális `apiVersion` érték adható meg `2018-10-01` .

### <a name="user-assigned-identity"></a>Felhasználó által hozzárendelt identitás

A felhasználó által hozzárendelt identitás az űrlap erőforrás-azonosítója. 

```
'/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.ManagedIdentity/userAssignedIdentities/{identityName}'
```

Egy vagy több felhasználó által hozzárendelt identitást is engedélyezhet.

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

### <a name="system--and-user-assigned-identities"></a>Rendszer-és felhasználó által hozzárendelt identitások

Egy tároló csoportban engedélyezheti a rendszerhez rendelt identitást és egy vagy több felhasználó által hozzárendelt identitást is.

```YAML
identity:
  type: SystemAssigned, UserAssigned
  userAssignedIdentities:
   {'myResourceID1':{}}
```

## <a name="next-steps"></a>Következő lépések

Ebben a cikkben megtanulta a felügyelt identitásokat a Azure Container Instancesban, és a következőket:

> [!div class="checklist"]
> * Felhasználó által hozzárendelt vagy rendszerhez rendelt identitás engedélyezése egy tároló csoportban
> * Az identitás hozzáférésének biztosítása egy Azure Key vaulthoz
> * A felügyelt identitás használata egy futó tárolóból származó kulcstartó eléréséhez

* További információ az [Azure-erőforrások felügyelt identitásáról](../active-directory/managed-identities-azure-resources/index.yml).

* Tekintse meg az [Azure go SDK példáját](https://medium.com/@samkreter/c98911206328) , amely felügyelt identitás használatával fér hozzá egy kulcstartóhoz Azure Container Instancesról.
