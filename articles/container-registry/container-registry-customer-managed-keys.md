---
title: Beállításjegyzék titkosítása ügyfél által felügyelt kulccsal
description: Ismerje meg az Azure Container Registry többi részében tárolt titkosítást, és hogyan titkosíthatja a Prémium beállításjegyzéket egy ügyfél által felügyelt kulccsal, amely a Azure Key Vault
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: 9ec32e32d187a3db07f023c78efbd301ef578cbc
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107817034"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Beállításjegyzék titkosítása ügyfél által felügyelt kulccsal

Amikor rendszerképeket és más összetevőket tárol egy Azure-beli tároló-beállításjegyzékben, az Azure automatikusan titkosítja a beállításjegyzék tartalmát a szolgáltatás által felügyelt [kulcsokkal.](../security/fundamentals/encryption-models.md) Az alapértelmezett titkosítást kiegészítheti egy további titkosítási réteggel egy olyan kulccsal, amely a Azure Key Vault (ügyfél által felügyelt kulcs) segítségével hozható létre és kezelhető. Ez a cikk végigvezeti az Azure CLI, a Azure Portal vagy egy Resource Manager lépéseit.

Az ügyfél által felügyelt kulcsokkal való kiszolgálóoldali titkosítás a következőkkel való integráción [keresztül Azure Key Vault:](../key-vault/general/overview.md) 

* Létrehozhatja saját titkosítási kulcsait, és kulcstartóban tárolhatja őket, vagy használhatja Azure Key Vault API-ját a kulcsok létrehozásához. 
* A Azure Key Vault a kulcshasználatot is naplólhatja.
* Azure Container Registry támogatja a beállításjegyzék titkosítási kulcsának automatikus rotációját, ha új kulcsverzió érhető el a Azure Key Vault. A beállításjegyzék titkosítási kulcsait manuálisan is elforgathatja.

Ez a szolgáltatás a **Prémium** tároló-beállításjegyzék szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási rétegeiről és korlátairól: Azure Container Registry [szolgáltatási szintek.](container-registry-skus.md)


## <a name="things-to-know"></a>Tudnivalók

* Jelenleg csak akkor engedélyezheti az ügyfél által kezelt kulcsokat, ha létrehoz egy regisztrációs adatbázist. A kulcs engedélyezésekor konfigurál egy felhasználó *által* hozzárendelt felügyelt identitást a kulcstartó eléréséhez.
* Miután engedélyezte az ügyfél által felügyelt kulccsal való titkosítást egy regisztrációs adatbázisban, nem tilthatja le a titkosítást.  
* Azure Container Registry RSA- vagy RSA-HSM-kulcsokat támogatja. Az elliptikus görbe kulcsok jelenleg nem támogatottak.
* [A tartalom megbízhatósága](container-registry-content-trust.md) jelenleg nem támogatott az ügyfél által felügyelt kulccsal titkosított beállításjegyzékben.
* Az ügyfél által felügyelt kulccsal titkosított beállításjegyzékben a rendszer jelenleg csak 24 óráig őrzi ACR-feladatok naplóit. [](container-registry-tasks-overview.md) Ha hosszabb ideig meg kell őriznie a naplókat, tekintse meg a feladatfuttassa naplók exportálására és [tárolására vonatkozó útmutatót.](container-registry-tasks-logs.md#alternative-log-storage)


> [!IMPORTANT]
> Ha a beállításjegyzék titkosítási kulcsát egy olyan meglévő Azure-kulcstartóban tervezi tárolni, amely megtagadja a nyilvános hozzáférést, és csak a privát végpontot vagy a kiválasztott virtuális hálózatokat engedélyezi, további konfigurációs lépésekre van szükség. A [cikk Speciális forgatókönyv: Key Vault tűzfal](#advanced-scenario-key-vault-firewall) használata.

## <a name="automatic-or-manual-update-of-key-versions"></a>Kulcsverziók automatikus vagy manuális frissítése

A felhasználó által felügyelt kulccsal titkosított beállításjegyzék biztonságának fontos szempontja, hogy milyen gyakran frissíti (forgatja) a titkosítási kulcsot. Előfordulhat, hogy a szervezet megfelelőségi [](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) szabályzatai megkövetelik a Azure Key Vault tárolt kulcsverziók rendszeres frissítését, ha az ügyfél által felügyelt kulcsként használják. 

Amikor felhasználó által felügyelt kulccsal konfigurálja a beállításjegyzék-titkosítást, két lehetőség áll rendelkezésre a titkosításhoz használt kulcsverzió frissítésére:

* **Kulcsverzió** automatikus frissítése – Ha automatikusan frissíteni szeretné az ügyfél által felügyelt kulcsokat, ha az Azure Key Vault-ban új verzió érhető el, akkor a beállításjegyzék ügyfél által felügyelt kulccsal való titkosításának engedélyezésekor ne használja a kulcsverziót. Ha egy beállításjegyzéket nem verziószámmal titkosítottak, a Azure Container Registry rendszeresen ellenőrzi a kulcstartóban az új kulcsverziót, és 1 órán belül frissíti az ügyfél által felügyelt kulcsot. Azure Container Registry automatikusan a kulcs legújabb verzióját használja.

* **Kulcsverzió manuális** frissítése – Ha a kulcs egy adott verzióját szeretné használni a beállításjegyzék titkosításához, akkor ezt a kulcsverziót kell megadnia, amikor engedélyezi a beállításjegyzék ügyfél által felügyelt kulcsokkal való titkosítását. Ha egy beállításjegyzék egy adott kulcsverzióval van titkosítva, a Azure Container Registry ezt a verziót használja a titkosításhoz, amíg manuálisan nem váltja át manuálisan az ügyfél által felügyelt kulcsot.

Részletekért lásd a kulcsazonosító kulcsverzióval vagy anélkül való választását és a kulcsverzió frissítését a cikk későbbi, cikkében. [](#choose-key-id-with-or-without-key-version) [](#update-key-version)

## <a name="prerequisites"></a>Előfeltételek

A cikkben található Azure CLI-lépések használhatja az Azure CLI 2.2.0-s vagy újabb verzióját, vagy Azure Cloud Shell. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Felhasználó által felügyelt kulcs engedélyezése – CLI

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Szükség esetén futtassa [az az group create parancsot][az-group-create] egy erőforráscsoport létrehozásához a kulcstartó, a tároló-beállításjegyzék és az egyéb szükséges erőforrások létrehozásához.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt [felügyelt identitást az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) [az az identity create paranccsal.][az-identity-create] A regisztrációs adatbázis ezt az identitást fogja használni a Key Vault eléréséhez.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

A parancs kimenetében jegyezze fel a következő értékeket: `id` és `principalId` . Ezekre az értékekre a későbbi lépésekben szüksége lesz a kulcstartóhoz való beállításjegyzék-hozzáférés konfigurálásához.

```JSON
{
  "clientId": "xxxx2bac-xxxx-xxxx-xxxx-192cxxxx6273",
  "clientSecretUrl": "https://control-eastus.identity.azure.net/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myidentityname/credentials?tid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&oid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx&aid=xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourcegroups/myresourcegroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myresourcegroup",
  "location": "eastus",
  "name": "myidentityname",
  "principalId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "resourceGroup": "myresourcegroup",
  "tags": {},
  "tenantId": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Az egyszerűség kedvéért ezeket az értékeket környezeti változókban tárolja:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Hozzon létre egy kulcstartót [az az keyvault create][az-keyvault-create] használatával egy ügyfél által felügyelt kulcs tárolására a beállításjegyzék titkosításához. 

Alapértelmezés szerint a **soft delete** beállítás automatikusan engedélyezve van egy új kulcstartóban. A kulcs vagy kulcstartó véletlen törlése által okozott adatvesztés elkerülése érdekében engedélyezze a végleges törlés **elleni védelmi beállítást** is.

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

A későbbi lépésekben való használathoz szerezze be a kulcstartó erőforrás-azonosítóját:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Kulcstartó-hozzáférés engedélyezése

Konfigurálnia kell egy szabályzatot a kulcstartóhoz, hogy az identitás hozzáfér a kulcstartóhoz. A következő [az keyvault set-policy][az-keyvault-set-policy] parancsban adja át a korábban egy környezeti változóban tárolt felügyelt identitás egyszerű azonosítóját. Állítsa be a kulcsengedélyeket a **,** **unwrapKey**, és **wrapKey lekért kulcshoz.**  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Másik lehetőségként az [Azure RBAC használatával Key Vault,](../key-vault/general/rbac-guide.md) hogy engedélyeket rendeljen az identitáshoz a kulcstartó eléréséhez. Például rendelje hozzá az Key Vault Crypto Service Encryption szerepkört az [identitáshoz az az role assignment create paranccsal:](/cli/azure/role/assignment#az_role_assignment_create)

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Kulcs létrehozása és kulcsazonosító lekérte

Futtassa [az az keyvault key create parancsot][az-keyvault-key-create] a kulcstartóban való kulcs létrehozásához.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

Jegyezze fel a kulcs azonosítóját a parancs `kid` kimenetében. Ezt az azonosítót a következő lépésben használhatja:

```JSON
[...]
  "key": {
    "crv": null,
    "d": null,
    "dp": null,
    "dq": null,
    "e": "AQAB",
    "k": null,
    "keyOps": [
      "encrypt",
      "decrypt",
      "sign",
      "verify",
      "wrapKey",
      "unwrapKey"
    ],
    "kid": "https://mykeyvault.vault.azure.net/keys/mykey/<version>",
    "kty": "RSA",
[...]
```

### <a name="choose-key-id-with-or-without-key-version"></a>Kulcsazonosító kiválasztása kulcsverzióval vagy anélkül

Az egyszerűség kedvéért tárolja a kulcsazonosítóhoz választott formátumot a $keyID környezeti változóban. A kulcsazonosítót verzióval vagy verzió nélküli kulccsal is használhatja.

#### <a name="manual-key-rotation---key-id-with-version"></a>Manuális kulcsrotáció – kulcsazonosító verzióval

Ha egy beállításjegyzék ügyfél által kezelt kulccsal való titkosítására használatos, ez a kulcs csak manuális kulcsrotációt tesz lehetővé a Azure Container Registry.

Ez a példa a kulcs tulajdonságát `kid` tárolja:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Automatikus kulcsrotáció – a kulcsazonosító kihagyja a verziót 

Ha egy beállításjegyzék ügyfél által felügyelt kulccsal való titkosítására használatos, ez a kulcs lehetővé teszi az automatikus kulcsrotálást, ha a rendszer új kulcsverziót észlel a Azure Key Vault.

Ez a példa eltávolítja a verziót a kulcs `kid` tulajdonságból:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Beállításjegyzék létrehozása ügyfél által felügyelt kulccsal

Futtassa [az az acr create parancsot][az-acr-create] egy regisztrációs adatbázis létrehozásához a Prémium szolgáltatási szinten, és engedélyezze az ügyfél által felügyelt kulcsot. Adja át a korábban környezeti változókban tárolt felügyelt identitás azonosítóját és kulcsazonosítóját:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Titkosítási állapot megjelenítése

Az az acr encryption show paranccsal mutatható be, hogy engedélyezve van-e a beállításjegyzék-titkosítás az ügyfél által felügyelt [kulccsal:][az-acr-encryption-show]

```azurecli
az acr encryption show --name <registry-name>
```

A beállításjegyzék titkosításához használt kulcstól függően a kimenet a következőre hasonlít:

```console
{
  "keyVaultProperties": {
    "identity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "keyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
    "keyRotationEnabled": true,
    "lastKeyRotationTimestamp": xxxxxxxx
    "versionedKeyIdentifier": "https://myvault.vault.azure.net/keys/myresourcegroup/abcdefg123456789...",
  },
  "status": "enabled"
}
```

## <a name="enable-customer-managed-key---portal"></a>Felhasználó által kezelt kulcs engedélyezése – portál

### <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

Hozzon létre egy felhasználó által [hozzárendelt felügyelt identitást az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) a Azure Portal. A lépésekért [lásd: Felhasználó által hozzárendelt identitás létrehozása.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)

A későbbi lépésekben az identitás nevét kell használnia.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Felhasználó által hozzárendelt identitás létrehozása a Azure Portal":::

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A kulcstartók létrehozásához szükséges lépésekért lásd: Rövid útmutató: Kulcstartó létrehozása [a Azure Portal.](../key-vault/general/quick-create-portal.md)

Amikor ügyfél által felügyelt kulcshoz hoz létre kulcstartót, az Alapvető **beállítások** lapon engedélyezze a **Véglegesen kiürítés elleni védelem** beállítást. Ez a beállítás segít megelőzni a kulcs vagy kulcstartó véletlen törlése által okozott adatvesztést.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Kulcstartó létrehozása a Azure Portal":::

### <a name="enable-key-vault-access"></a>Kulcstartó-hozzáférés engedélyezése

Konfigurálnia kell egy szabályzatot a kulcstartóhoz, hogy az identitás hozzáfér a kulcstartóhoz.

1. Lépjen a kulcstartóhoz.
1. Válassza **a Beállítások Hozzáférési**  >  **szabályzatok > +Hozzáférési szabályzat hozzáadása lehetőséget.**
1. Válassza **a Kulcsengedélyek** lehetőséget, majd válassza **a Get**, **Unwrap Key**, és Wrap Key (Kulcs **beírása) lehetőséget.**
1. A **Rendszerbiztonsági tag kiválasztása** mezőben válassza ki a felhasználó által hozzárendelt felügyelt identitás erőforrásnevét.  
1. Válassza **a Hozzáadás,** majd a **Mentés lehetőséget.**

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Kulcstartó-hozzáférési szabályzat létrehozása":::

Másik lehetőségként az [Azure RBAC használatával Key Vault,](../key-vault/general/rbac-guide.md) hogy engedélyeket rendeljen az identitáshoz a kulcstartó eléréséhez. Rendelje hozzá például a Key Vault titkosítási szerepkört az identitáshoz.

1. Lépjen a kulcstartóhoz.
1. Válassza **a Hozzáférés-vezérlés (IAM)**  >  **+ Szerepkör-hozzárendelés** hozzáadása  >  **lehetőséget.**
1. A **Szerepkör-hozzárendelés hozzáadása ablakban:**
    1. Válassza **Key Vault titkosítási szolgáltatás titkosítása felhasználói szerepkört.** 
    1. Hozzáférés hozzárendelése **felhasználó által hozzárendelt felügyelt identitáshoz:**.
    1. Válassza ki a felhasználó által hozzárendelt felügyelt identitás erőforrásnevét, majd válassza a **Mentés lehetőséget.**

### <a name="create-key-optional"></a>Kulcs létrehozása (nem kötelező)

Ha szükséges, hozzon létre egy kulcsot a kulcstartóban a beállításjegyzék titkosításához. Ha egy adott kulcsverziót szeretne ügyfél által felügyelt kulcsként kiválasztani, kövesse az alábbi lépéseket. 

1. Lépjen a kulcstartóhoz.
1. Válassza a **Beállítási**  >  **kulcsok lehetőséget.**
1. Válassza **a +Generate/Import (+Generálás/importálás)** lehetőséget, és adjon meg egy egyedi nevet a kulcsnak.
1. Fogadja el a fennmaradó alapértelmezett értékeket, és válassza a **Létrehozás lehetőséget.**
1. A létrehozás után válassza ki a kulcsot, majd válassza ki az aktuális verziót. Másolja ki **a kulcsverzió** kulcsazonosítóját.

### <a name="create-azure-container-registry"></a>Azure Container Registry létrehozása

1. Válassza **az Erőforrás létrehozása**  >  **Tárolók Container Registry.**  >  
1. Az Alapvető **beállítások lapon** válasszon ki vagy hozzon létre egy erőforráscsoportot, és adja meg a beállításjegyzék nevét. A **Termékváltozat mezőben** válassza a **Prémium lehetőséget.**
1. A Titkosítás **lap** Felhasználó **által** kezelt kulcs lapján válassza az **Engedélyezve lehetőséget.**
1. Az **Identity (Identitás)** mezőben válassza ki a létrehozott felügyelt identitást.
1. A **Titkosítás részen** válasszon a következők közül:
    * Válassza **a Select (Key Vault)** lehetőséget, majd válasszon ki egy meglévő kulcstartót és kulcsot, vagy kattintson az **Új létrehozása gombra.** A kiválasztott kulcs nem verziószámú, és lehetővé teszi az automatikus kulcsrotálást.
    * Válassza az Enter key URI (Kulcs **URI-jának** megíratja) lehetőséget, és adjon meg egy kulcsazonosítót közvetlenül. Meg lehet adni egy verziószámos kulcs URI-ét (manuálisan rotált kulcshoz) vagy egy nem verziószámú kulcs URI-t (amely lehetővé teszi az automatikus kulcsrotálást). 
1. A Titkosítás **lapon** válassza a Felülvizsgálat **+ létrehozás lehetőséget.**
1. A **beállításjegyzék-példány** üzembe helyezéséhez válassza a Létrehozás lehetőséget.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Titkosított beállításjegyzék létrehozása a Azure Portal":::

A regisztrációs adatbázis titkosítási állapotának a portálon való megtekintése után keresse meg a regisztrációs adatbázist. A **Beállítások alatt** válassza a Titkosítás **lehetőséget.**

## <a name="enable-customer-managed-key---template"></a>Felhasználó által kezelt kulcs engedélyezése – sablon

A beállításjegyzék létrehozásához és az ügyfél által felügyelt kulccsal való titkosítás engedélyezéséhez Resource Manager sablont is használhat.

Az alábbi sablon egy új tároló-beállításjegyzéket és egy felhasználó által hozzárendelt felügyelt identitást hoz létre. Másolja az alábbi tartalmat egy új fájlba, és mentse egy fájlnévvel (például `CMKtemplate.json` : ).

```JSON
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "vault_name": {
      "defaultValue": "",
      "type": "String"
    },
    "registry_name": {
      "defaultValue": "",
      "type": "String"
    },
    "identity_name": {
      "defaultValue": "",
      "type": "String"
    },
    "kek_id": {
      "type": "String"
    }
  },
  "variables": {},
  "resources": [
    {
      "type": "Microsoft.ContainerRegistry/registries",
      "apiVersion": "2019-12-01-preview",
      "name": "[parameters('registry_name')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Premium",
        "tier": "Premium"
      },
      "identity": {
        "type": "UserAssigned",
        "userAssignedIdentities": {
          "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]": {}
        }
      },
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "adminUserEnabled": false,
        "encryption": {
          "status": "enabled",
          "keyVaultProperties": {
            "identity": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').clientId]",
            "KeyIdentifier": "[parameters('kek_id')]"
          }
        },
        "networkRuleSet": {
          "defaultAction": "Allow",
          "virtualNetworkRules": [],
          "ipRules": []
        },
        "policies": {
          "quarantinePolicy": {
            "status": "disabled"
          },
          "trustPolicy": {
            "type": "Notary",
            "status": "disabled"
          },
          "retentionPolicy": {
            "days": 7,
            "status": "disabled"
          }
        }
      }
    },
    {
      "type": "Microsoft.KeyVault/vaults/accessPolicies",
      "apiVersion": "2018-02-14",
      "name": "[concat(parameters('vault_name'), '/add')]",
      "dependsOn": [
        "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name'))]"
      ],
      "properties": {
        "accessPolicies": [
          {
            "tenantId": "[subscription().tenantId]",
            "objectId": "[reference(resourceId('Microsoft.ManagedIdentity/userAssignedIdentities', parameters('identity_name')), '2018-11-30').principalId]",
            "permissions": {
              "keys": [
                "get",
                "unwrapKey",
                "wrapKey"
              ]
            }
          }
        ]
      }
    },
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "apiVersion": "2018-11-30",
      "name": "[parameters('identity_name')]",
      "location": "[resourceGroup().location]"
    }
  ]
}
```

Kövesse az előző szakaszokban található lépéseket a következő erőforrások létrehozásához:

* Key Vault, név alapján azonosítva
* Kulcstartókulcs, azonosítóval azonosítva

Futtassa a következő [az deployment group create parancsot][az-deployment-group-create] a regisztrációs adatbázis létrehozásához az előző sablonfájl használatával. Ahol jelezve van, adjon meg egy új regisztrációs adatbázisnevet és felügyelt identitásnevet, valamint a létrehozott kulcstartó nevét és kulcsazonosítóját.

```bash
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file CMKtemplate.json \
  --parameters \
    registry_name=<registry-name> \
    identity_name=<managed-identity> \
    vault_name=<key-vault-name> \
    kek_id=<key-vault-key-id>
```

### <a name="show-encryption-status"></a>Titkosítási állapot megjelenítése

A beállításjegyzék-titkosítás állapotának az [az acr encryption show paranccsal mutatható][az-acr-encryption-show] be:

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>A beállításjegyzék használata

Miután engedélyezte az ügyfél által kezelt kulcsokat a beállításjegyzékben, végrehajthatja a beállításjegyzékben is ugyanazt a műveletet, mint az ügyfél által felügyelt kulccsal nem titkosított beállításjegyzékben. Például hitelesíthet a beállításjegyzékben, és lekultálhatja a Docker-rendszerképeket. Lásd a leküldéses és [leküldési parancsokat.](container-registry-get-started-docker-cli.md)

## <a name="rotate-key"></a>Kulcs elforgatása

Frissítse a kulcsverziót a Azure Key Vault, vagy hozzon létre egy új kulcsot, majd frissítse a beállításjegyzéket az adatok kulccsal való titkosításához. Ezeket a lépéseket az Azure CLI használatával vagy a portálon hajthatja végre.

Kulcs rotozásakor általában ugyanazt az identitást kell megadnia, mint a beállításjegyzék létrehozásakor. Szükség esetén konfigurálhatja az új, felhasználó által hozzárendelt identitást a kulcs eléréséhez, vagy engedélyezheti és megadhatja a beállításjegyzék rendszer által hozzárendelt identitását.

> [!NOTE]
> Győződjön meg arról, hogy a [kulcstartó eléréséhez](#enable-key-vault-access) szükséges hozzáférés be van állítva a kulcseléréshez konfigurált identitáshoz.

### <a name="update-key-version"></a>Kulcsverzió frissítése

Gyakori forgatókönyv a felhasználó által felügyelt kulcsként használt kulcs verziójának frissítése. A beállításjegyzék titkosításának konfigurálásától függően az ügyfél által felügyelt kulcs Azure Container Registry automatikusan frissül, vagy manuálisan kell frissíteni.

### <a name="azure-cli"></a>Azure CLI

A key vault-kulcsok létrehozásához vagy kezeléséhez használja az [az keyvault key][az-keyvault-key] commands parancsot. Új kulcsverzió létrehozásához futtassa az [az keyvault key create][az-keyvault-key-create] parancsot:

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

A következő lépés a beállításjegyzék titkosításának konfigurálásától függ:

* Ha a beállításjegyzék úgy van konfigurálva, hogy észlelje a kulcsverzió frissítéseit, az ügyfél által kezelt kulcs automatikusan frissül 1 órán belül.

* Ha a beállításjegyzék úgy van konfigurálva, hogy manuális frissítést igényel egy új kulcsverzióhoz, futtassa az [az acr encryption rotate-key][az-acr-encryption-rotate-key] parancsot az új kulcsazonosító és a konfigurálni kívánt identitás átadásával:

Az ügyfél által felügyelt kulcs verziójának manuális frissítése:

```azurecli
# Rotate key and use user-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity <principal-id-user-assigned-identity>

# Rotate key and use system-assigned identity
az acr encryption rotate-key \
  --name <registry-name> \
  --key-encryption-key <new-key-id> \
  --identity [system]
```

> [!TIP]
> A futtatásakor átadhat egy verzióval vagy egy nem `az acr encryption rotate-key` verziószámos kulcsazonosítót. Ha nem verziószámozható kulcsazonosítót használ, a beállításjegyzéket úgy konfigurálja, hogy automatikusan észlelje a későbbi kulcsverzió-frissítéseket.

### <a name="portal"></a>Portál

A beállításjegyzék Titkosítási **beállításaival** frissítheti az ügyfél által felügyelt kulcshoz használt kulcstartó-, kulcs- vagy identitásbeállításokat.

Például egy új kulcs konfiguráláshoz:

1. A portálon keresse meg a regisztrációs adatbázist.
1. A **Beállítások alatt** válassza a   >  **Titkosítási kulcs módosítása lehetőséget.**

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Kulcs elforgatása a Azure Portal":::
1. A **Titkosítás részen** válasszon a következők közül:
    * Válassza **a Select (Key Vault)** lehetőséget, majd válasszon ki egy meglévő kulcstartót és kulcsot, vagy kattintson az Create **new (Új létrehozása) gombra.** A kiválasztott kulcs nem verziószámú, és lehetővé teszi az automatikus kulcsrotálást.
    * Válassza az Enter key URI (Kulcs **URI-jának** enter) lehetőséget, és adjon meg közvetlenül egy kulcsazonosítót. Meg lehet adni egy verziószámos kulcs URI-ét (olyan kulcs esetén, amelyet manuálisan kell elforgatni) vagy egy nem verziószámú kulcs URI-ét (amely lehetővé teszi az automatikus kulcsrotálást).
1. Töltse ki a kulcsot, és válassza a **Mentés lehetőséget.**

## <a name="revoke-key"></a>Kulcs visszavonása

Vonja vissza az ügyfél által felügyelt titkosítási kulcsot a hozzáférési szabályzat vagy engedélyek kulcstartón való módosításával vagy törlésével. Például az [az keyvault delete-policy][az-keyvault-delete-policy] paranccsal módosíthatja a regisztrációs adatbázis által használt felügyelt identitás hozzáférési szabályzatát:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

A kulcs visszatartódása gyakorlatilag letiltja az összes beállításjegyzék-adathoz való hozzáférést, mivel a beállításjegyzék nem fér hozzá a titkosítási kulcshoz. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törölt kulcs vissza lett állva, a beállításjegyzék kiválasztja a kulcsot, így ismét hozzáférhet a titkosított beállításjegyzék adataihoz.

## <a name="advanced-scenario-key-vault-firewall"></a>Speciális forgatókönyv: Key Vault tűzfal használata

Érdemes lehet a titkosítási kulcsot egy meglévő, [Key Vault-tűzfallal](../key-vault/general/network-security.md)konfigurált Azure-kulcstartóval tárolni, amely megtagadja a nyilvános hozzáférést, és csak a privát végpontot vagy a kiválasztott virtuális hálózatokat engedélyezi. 

Ebben a forgatókönyvben először hozzon létre egy felhasználó által hozzárendelt identitást, kulcstartót és tároló-beállításjegyzéket ügyfél által felügyelt kulccsal titkosítva az [Azure CLI,](#enable-customer-managed-key---cli)a portál [vagy](#enable-customer-managed-key---portal)a sablon [használatával.](#enable-customer-managed-key---template) A részletes lépések a cikk korábbi szakaszaiban olvashatók.
   > [!NOTE]
   > Az új kulcstartó a tűzfalon kívül van üzembe helyezni. Ez csak ideiglenesen használatos az ügyfél által kezelt kulcs tárolására.

A beállításjegyzék létrehozása után folytassa a következő lépésekkel. A részleteket a következő szakaszok ismertetik.

1. Engedélyezze a beállításjegyzék rendszer által hozzárendelt identitását.
1. Adjon engedélyt a rendszer által hozzárendelt identitásnak a kulcstartóban található, a tűzfallal korlátozott Key Vault eléréséhez.
1. Győződjön meg arról, Key Vault tűzfal lehetővé teszi a megbízható szolgáltatások megkerülését. Az Azure Container Registry jelenleg csak a rendszer által felügyelt identitás használata esetén tudja megkerülni a tűzfalat. 
1. Váltsa át az ügyfél által felügyelt kulcsot úgy, hogy kiválaszt egyet a kulcstartóban, amely a Key Vault van korlátozva.
1. Ha már nincs rá szükség, törölheti a tűzfalon kívül létrehozott kulcstartót.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>1. lépés – A beállításjegyzék rendszer által hozzárendelt identitásának engedélyezése

1. A portálon keresse meg a regisztrációs adatbázist.
1. Válassza a **Beállítások**  >   **identitása lehetőséget.**
1. A **Rendszer által hozzárendelt alatt** állítsa az Állapot **beállítását** Be **állapotra.** Kattintson a **Mentés** gombra.
1. Másolja ki **az identitás objektumazonosítóját.**

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>2. lépés – Hozzáférés megadása a rendszer által hozzárendelt identitás számára a kulcstartóhoz

1. A portálon keresse meg a kulcstartót.
1. Válassza **a Beállítások Hozzáférési**  >  **szabályzatok > +Hozzáférési szabályzat hozzáadása lehetőséget.**
1. Válassza **a Kulcsengedélyek** lehetőséget, majd **válassza a Get**, Unwrap Key , és Wrap Key (Kulcs **beírása)** **lehetőséget.**
1. Válassza **a Rendszerbiztonsági tag** kiválasztása lehetőséget, és keresse meg a rendszer által hozzárendelt felügyelt identitás objektumazonosítóját vagy a regisztrációs adatbázis nevét.  
1. Válassza **a Hozzáadás,** majd a **Mentés lehetőséget.**

### <a name="step-3---enable-key-vault-bypass"></a>3. lépés – Kulcstartó megkerülő funkció engedélyezése

A tűzfallal konfigurált kulcstartók eléréséhez Key Vault beállításjegyzéknek meg kell kerülnie a tűzfalat. Győződjön meg arról, hogy a kulcstartó úgy van konfigurálva, hogy bármely megbízható [szolgáltatás számára engedélyezze a hozzáférést.](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services) Azure Container Registry a megbízható szolgáltatások egyike.

1. A portálon keresse meg a kulcstartót.
1. Válassza **a Beállítások** Hálózat  >  **lehetőséget.**
1. Erősítse meg, frissítse vagy adja hozzá a virtuális hálózati beállításokat. Részletes lépésekért lásd: [Tűzfalak Azure Key Vault virtuális](../key-vault/general/network-security.md)hálózatok konfigurálása.
1. Az Allow Microsoft Trusted Services to bypass this firewall (A Microsoft megbízható szolgáltatások **megkerülhetik ezt a tűzfalat)** beállításnál válassza az **Igen lehetőséget.** 

### <a name="step-4---rotate-the-customer-managed-key"></a>4. lépés – Az ügyfél által felügyelt kulcs forgása

Az előző lépések elvégzése után váltsa át az adatokat egy tűzfal mögötti kulcstartóban tárolt kulcsra.

1. A portálon keresse meg a regisztrációs adatbázist.
1. A **Beállítások alatt** válassza a   >  **Titkosítási kulcs módosítása lehetőséget.**
1. Az **Identity (Identitás)** mezőben válassza **a System Assigned (Rendszer által hozzárendelt) lehetőséget.**
1. Válassza **a Key Vault** lehetőséget, majd válassza ki a tűzfal mögötti kulcstartó nevét.
1. Válasszon ki egy meglévő kulcsot, vagy **hozzon létre egy újat.** A kiválasztott kulcs nem verziószámú, és lehetővé teszi az automatikus kulcsrotálást.
1. Töltse ki a kulcsot, és válassza a **Mentés lehetőséget.**

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="removing-managed-identity"></a>Felügyelt identitás eltávolítása


Ha egy felhasználó vagy rendszer által hozzárendelt felügyelt identitást próbál eltávolítani a titkosítás konfigurálásához használt beállításjegyzékből, a következőhöz hasonló hibaüzenet jelenhet meg:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
A titkosítási kulcsot nem fogja tudni módosítani (elforgatni). A megoldási lépések a titkosításhoz használt identitás típusától függnek.

**Felhasználó által hozzárendelt identitás**

Ha ez a probléma felhasználó által hozzárendelt identitással fordul elő, először a hibaüzenetben megjelenő GUID azonosítóval kell újra hozzárendelni az identitást. Például:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Ezután a kulcs módosítása és egy másik identitás hozzárendelése után eltávolíthatja az eredeti, felhasználó által hozzárendelt identitást.

**Rendszer által hozzárendelt identitás**

Ha a probléma egy rendszer által hozzárendelt identitással fordul elő, hozzon létre egy Azure-támogatás [jegyet](https://azure.microsoft.com/support/create-ticket/) az identitás visszaállításához.


## <a name="next-steps"></a>Következő lépések

* További információ az [Azure-beli titkosításról.](../security/fundamentals/encryption-atrest.md)
* További információ a hozzáférési szabályzatokkal és a kulcstartóhoz való [hozzáférés biztonságossá való elérésének mikéntjéből.](../key-vault/general/security-features.md)


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-show]: /cli/azure/feature#az_feature_show
[az-group-create]: /cli/azure/group#az_group_create
[az-identity-create]: /cli/azure/identity#az_identity_create
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-deployment-group-create]: /cli/azure/deployment/group#az_deployment_group_create
[az-keyvault-create]: /cli/azure/keyvault#az_keyvault_create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az_keyvault_key_create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az_keyvault_set_policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az_keyvault_delete_policy
[az-resource-show]: /cli/azure/resource#az_resource_show
[az-acr-create]: /cli/azure/acr#az_acr_create
[az-acr-show]: /cli/azure/acr#az_acr_show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az_acr_encryption_rotate_key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az_acr_encryption_show
