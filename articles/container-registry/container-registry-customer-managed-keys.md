---
title: Beállításjegyzék titkosítása ügyfél által felügyelt kulccsal
description: Ismerje meg az Azure Container Registry titkosítását, valamint azt, hogyan titkosíthatja a prémium szintű beállításjegyzéket a Azure Key Vaultban tárolt ügyfél által felügyelt kulccsal.
ms.topic: article
ms.date: 03/03/2021
ms.custom: ''
ms.openlocfilehash: aad9419fdb139ff615bfe07075be78a2ca4ee4ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "102489072"
---
# <a name="encrypt-registry-using-a-customer-managed-key"></a>Beállításjegyzék titkosítása az ügyfél által felügyelt kulccsal

Ha lemezképeket és egyéb összetevőket tárol egy Azure Container registryben, az Azure automatikusan titkosítja a beállításjegyzék tartalmát a [szolgáltatás által felügyelt kulcsokkal](../security/fundamentals/encryption-models.md). Egy további titkosítási réteggel kiegészítheti az alapértelmezett titkosítást a Azure Key Vault (ügyfél által felügyelt kulcs) által létrehozott és kezelt kulcs használatával. Ez a cikk végigvezeti az Azure CLI, a Azure Portal vagy egy Resource Manager-sablon használatának lépésein.

Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás a [Azure Key Vault](../key-vault/general/overview.md)-integráción keresztül támogatott: 

* Létrehozhat saját titkosítási kulcsokat, és tárolhatja őket egy kulcstartóban, vagy használhatja a Azure Key Vault API-kat a kulcsok létrehozásához. 
* A Azure Key Vault használatával is naplózhatja a kulcshasználat.
* Azure Container Registry támogatja a beállításjegyzék-titkosítási kulcsok automatikus elforgatását, ha az új kulcs verziója elérhető a Azure Key Vaultban. Manuálisan is elforgathatja a beállításjegyzék titkosítási kulcsait.

Ez a funkció a **prémium** szintű Container Registry szolgáltatási szinten érhető el. További információ a beállításjegyzék szolgáltatási szintjeiről és korlátairól: [Azure Container Registry szolgáltatási szintek](container-registry-skus.md).


## <a name="things-to-know"></a>Tudnivalók

* Jelenleg csak a beállításjegyzék létrehozásakor engedélyezheti az ügyfél által felügyelt kulcsokat. A kulcs engedélyezésekor egy *felhasználóhoz rendelt* felügyelt identitást kell konfigurálni a kulcstartó eléréséhez.
* Miután engedélyezte a titkosítást egy ügyfél által felügyelt kulccsal a beállításjegyzékben, nem tilthatja le a titkosítást.  
* A Azure Container Registry csak RSA-vagy RSA-HSM-kulcsokat támogat. Az elliptikus görbe kulcsai jelenleg nem támogatottak.
* A [tartalom megbízhatósága](container-registry-content-trust.md) jelenleg nem támogatott az ügyfél által felügyelt kulccsal titkosított beállításjegyzékben.
* Az ügyfél által felügyelt kulccsal titkosított beállításjegyzékben az [ACR-feladatokhoz](container-registry-tasks-overview.md) tartozó naplókat jelenleg csak 24 óráig őrzi meg a rendszer. Ha hosszabb ideig kell megőriznie a naplókat, tekintse meg a [feladat-futtatási naplók exportálásával és tárolásával](container-registry-tasks-logs.md#alternative-log-storage)kapcsolatos útmutatót.


> [!IMPORTANT]
> Ha azt tervezi, hogy egy meglévő Azure Key vaultban tárolja a beállításjegyzék-titkosítási kulcsot, amely megtagadja a nyilvános hozzáférést, és csak privát végpontot vagy kiválasztott virtuális hálózatokat engedélyez, további konfigurációs lépésekre van szükség. Lásd a jelen cikk [speciális forgatókönyv: Key Vault tűzfal](#advanced-scenario-key-vault-firewall) című részében.

## <a name="automatic-or-manual-update-of-key-versions"></a>A főbb verziók automatikus vagy manuális frissítése

Az ügyfél által felügyelt kulccsal titkosított beállításjegyzék biztonságának fontos szempontja, hogy milyen gyakran frissíti (elforgatják) a titkosítási kulcsot. Előfordulhat, hogy a szervezete olyan megfelelőségi szabályzatokkal rendelkezik, amelyek az ügyfél által felügyelt kulcsokként való használat során rendszeresen frissítik a Azure Key Vault tárolt főbb [verzióit](../key-vault/general/about-keys-secrets-certificates.md#objects-identifiers-and-versioning) . 

Ha az ügyfél által felügyelt kulccsal konfigurálja a beállításjegyzék titkosítását, két lehetőség közül választhat a titkosításhoz használt kulcs verziójának frissítéséhez:

* **A kulcs verziójának automatikus** frissítése – az ügyfél által felügyelt kulcs automatikus frissítéséhez, ha a Azure Key Vaultben új verzió érhető el, hagyja ki a kulcs verzióját, ha az ügyfél által felügyelt kulccsal engedélyezi a beállításjegyzék titkosítását. Ha egy beállításjegyzék nem verziószámú kulccsal van titkosítva, Azure Container Registry rendszeresen ellenőrzi a kulcstartót egy új kulcs verziójában, és 1 órán belül frissíti az ügyfél által felügyelt kulcsot. A Azure Container Registry automatikusan a kulcs legújabb verzióját használja.

* **A kulcs verziójának manuális frissítése** – ha egy kulcs adott verzióját szeretné használni a beállításjegyzék-titkosításhoz, akkor a kulcs verzióját kell megadnia, ha az ügyfél által felügyelt kulccsal engedélyezi a beállításjegyzék titkosítását. Ha egy beállításjegyzék egy adott verziójú kulccsal van titkosítva, akkor a Azure Container Registry az adott verziót használja a titkosításhoz, amíg az ügyfél által felügyelt kulcs manuális elforgatása megtörténik.

Részletekért lásd a jelen cikk későbbi, a kulcs [azonosítójának és a](#choose-key-id-with-or-without-key-version) kulcs verziószámának megadása és a [kulcs verziójának frissítése](#update-key-version)című témakört.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő Azure CLI-lépések használatához az Azure CLI-es vagy újabb verziójára, vagy Azure Cloud Shellra van szükség. Ha telepíteni vagy frissíteni szeretne: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

## <a name="enable-customer-managed-key---cli"></a>Ügyfél által felügyelt kulcs engedélyezése – parancssori felület

### <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása

Szükség esetén futtassa az az [Group Create][az-group-create] parancsot egy erőforráscsoport létrehozásához a kulcstartó, a tároló-beállításjegyzék és az egyéb szükséges erőforrások létrehozásához.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt [felügyelt identitást az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) az az [Identity Create][az-identity-create] paranccsal. Ezt az identitást a beállításjegyzék fogja használni a Key Vault szolgáltatás eléréséhez.

```azurecli
az identity create \
  --resource-group <resource-group-name> \
  --name <managed-identity-name>
```

A parancs kimenetében jegyezze fel a következő értékeket: `id` és `principalId` . Ezekre az értékekre szüksége lesz a beállításjegyzék hozzáférésének a Key vaulthoz való konfigurálásának későbbi lépéseiben.

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

A kényelem érdekében tárolja ezeket az értékeket környezeti változókban:

```azurecli
identityID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'id' --output tsv)

identityPrincipalID=$(az identity show --resource-group <resource-group-name> --name <managed-identity-name> --query 'principalId' --output tsv)
```

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

Hozzon létre egy Key vaultot az az Key Vault [létrehozásával][az-keyvault-create] , amely az ügyfél által felügyelt kulcsot tárolja a beállításjegyzék titkosításához. 

Alapértelmezés szerint a rendszer automatikusan engedélyezi a **Soft delete** beállítást egy új kulcstartóban. Ha meg szeretné akadályozni, hogy a véletlen kulcs vagy a kulcstartó törlése okozza az adatvesztést, engedélyezze a **védelem kiürítésének** beállítását is.

```azurecli
az keyvault create --name <key-vault-name> \
  --resource-group <resource-group-name> \
  --enable-purge-protection
```

A későbbi lépésekben való használathoz szerezze be a Key Vault erőforrás-AZONOSÍTÓját:

```azurecli
keyvaultID=$(az keyvault show --resource-group <resource-group-name> --name <key-vault-name> --query 'id' --output tsv)
```

### <a name="enable-key-vault-access"></a>Key Vault-hozzáférés engedélyezése

Konfiguráljon egy házirendet a Key vaulthoz, hogy az identitás hozzáférhessen. A következő az a kulcstartó [set-Policy][az-keyvault-set-policy] paranccsal megadhatja a korábban létrehozott felügyelt identitás elsődleges azonosítóját, amelyet előzőleg egy környezeti változóban tároltak. Adja meg a **Get**, a **UnwrapKey** és a **wrapKey** kulcs engedélyeit.  

```azurecli
az keyvault set-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID \
  --key-permissions get unwrapKey wrapKey
```

Azt is megteheti, hogy a [Key Vault Azure RBAC](../key-vault/general/rbac-guide.md) használatával rendeli hozzá az identitáshoz a kulcstartó eléréséhez szükséges engedélyeket. Rendelje hozzá például a Key Vault titkosítási szolgáltatás titkosítási szerepkörét az identitáshoz az az [role hozzárendelés Create](/cli/azure/role/assignment#az-role-assignment-create) parancs használatával:

```azurecli 
az role assignment create --assignee $identityPrincipalID \
  --role "Key Vault Crypto Service Encryption User" \
  --scope $keyvaultID
```

### <a name="create-key-and-get-key-id"></a>Kulcs létrehozása és a kulcs AZONOSÍTÓjának beolvasása

Futtassa az az kulcstartó [kulcs létrehozása][az-keyvault-key-create] parancsot a Key vaultban lévő kulcs létrehozásához.

```azurecli
az keyvault key create \
  --name <key-name> \
  --vault-name <key-vault-name>
```

A parancs kimenetében jegyezze fel a kulcs AZONOSÍTÓját `kid` . Ezt az azonosítót a következő lépésben kell használni:

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

### <a name="choose-key-id-with-or-without-key-version"></a>Válassza a Key ID elemet a kulcs verziószámával vagy anélkül.

A kényelem érdekében a $keyID környezeti változóban tárolja a kulcs AZONOSÍTÓjának megfelelő formátumot. A kulcs AZONOSÍTÓjának verziója vagy verziószáma nélkül is használható.

#### <a name="manual-key-rotation---key-id-with-version"></a>Kézi kulcs elforgatása – kulcs azonosítója a verzióval

Ha az ügyfél által felügyelt kulccsal titkosít egy beállításjegyzéket, ez a kulcs csak a kézi kulcsok elforgatását engedélyezi Azure Container Registryban.

Ez a példa a kulcs `kid` tulajdonságát tárolja:

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)
```

#### <a name="automatic-key-rotation---key-id-omitting-version"></a>Automatikus kulcs elforgatása – a kulcs azonosítója a verzió kihagyása 

Ha ügyfél által felügyelt kulccsal titkosít egy beállításjegyzéket, ez a kulcs lehetővé teszi az automatikus kulcs elforgatását, amikor a rendszer új kulcs-verziót észlel Azure Key Vaultban.

Ez a példa a kulcs tulajdonságában lévő verziót távolítja el `kid` :

```azurecli
keyID=$(az keyvault key show \
  --name <keyname> \
  --vault-name <key-vault-name> \
  --query 'key.kid' --output tsv)

keyID=$(echo $keyID | sed -e "s/\/[^/]*$//")
```

### <a name="create-a-registry-with-customer-managed-key"></a>Beállításjegyzék létrehozása az ügyfél által felügyelt kulccsal

Futtassa az az [ACR Create][az-acr-create] parancsot egy beállításjegyzék létrehozásához a prémium szintű szolgáltatási szinten, és engedélyezze az ügyfél által felügyelt kulcsot. Adja át a felügyelt identitás AZONOSÍTÓját és a korábban a környezeti változókban tárolt kulcs AZONOSÍTÓját:

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --identity $identityID \
  --key-encryption-key $keyID \
  --sku Premium
```

### <a name="show-encryption-status"></a>Titkosítási állapot megjelenítése

Annak megjelenítéséhez, hogy engedélyezve van-e az ügyfél által felügyelt kulccsal rendelkező beállításjegyzék-titkosítás, futtassa az az [ACR encryption show][az-acr-encryption-show] parancsot:

```azurecli
az acr encryption show --name <registry-name>
```

A beállításjegyzék titkosításához használt kulcstól függően a kimenet a következőhöz hasonló:

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

## <a name="enable-customer-managed-key---portal"></a>Ügyfél által felügyelt kulcs engedélyezése – portál

### <a name="create-a-managed-identity"></a>Felügyelt identitás létrehozása

Hozzon létre egy felhasználó által hozzárendelt [felügyelt identitást az Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/overview.md) a Azure Portal. A lépéseket a [felhasználó által hozzárendelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity)című témakörben tekintheti meg.

Az identitás nevét a későbbi lépésekben használhatja.

:::image type="content" source="media/container-registry-customer-managed-keys/create-managed-identity.png" alt-text="Felhasználó által hozzárendelt identitás létrehozása a Azure Portalban":::

### <a name="create-a-key-vault"></a>Kulcstartó létrehozása

A kulcstartó létrehozásának lépéseiért lásd: gyors útmutató [: kulcstartó létrehozása a Azure Portal használatával](../key-vault/general/quick-create-portal.md).

Az ügyfél által felügyelt kulcs kulcstárolójának létrehozásakor az **alapok** lapon engedélyezze a **védelem kiürítése** beállítást. Ez a beállítás segít megakadályozni az adatvesztést a véletlen kulcs vagy a kulcstartó törlése miatt.

:::image type="content" source="media/container-registry-customer-managed-keys/create-key-vault.png" alt-text="Key Vault létrehozása a Azure Portalban":::

### <a name="enable-key-vault-access"></a>Key Vault-hozzáférés engedélyezése

Konfiguráljon egy házirendet a Key vaulthoz, hogy az identitás hozzáférhessen.

1. Navigáljon a kulcstartóhoz.
1. Válassza a **Beállítások**  >  **hozzáférési szabályzatok > + hozzáférési házirend hozzáadása** lehetőséget.
1. Válassza a **kulcs engedélyei** lehetőséget, majd a **beolvasás**, a **kicsomagolás** **és a** kicsomagolási kulcs elemet.
1. A **rendszerbiztonsági tag kiválasztása** területen válassza ki a felhasználó által hozzárendelt felügyelt identitás erőforrásának nevét.  
1. Válassza a **Hozzáadás**, majd a **Mentés** lehetőséget.

:::image type="content" source="media/container-registry-customer-managed-keys/add-key-vault-access-policy.png" alt-text="Key Vault hozzáférési szabályzat létrehozása":::

Azt is megteheti, hogy a [Key Vault Azure RBAC](../key-vault/general/rbac-guide.md) használatával rendeli hozzá az identitáshoz a kulcstartó eléréséhez szükséges engedélyeket. Rendelje hozzá például az Key Vault titkosítási szolgáltatás titkosítási szerepkörét az identitáshoz.

1. Navigáljon a kulcstartóhoz.
1. Válassza a **hozzáférés-vezérlés (iam)**  >  **+** Hozzáadás  >  **szerepkör-hozzárendelés** hozzáadása elemet.
1. A **szerepkör-hozzárendelés hozzáadása** ablakban:
    1. Válassza ki **Key Vault titkosítási szolgáltatás titkosítási felhasználói** szerepkörét. 
    1. Hozzáférés hozzárendelése a **felhasználóhoz rendelt felügyelt identitáshoz**.
    1. Válassza ki a felhasználó által hozzárendelt felügyelt identitás erőforrásának nevét, majd válassza a **Mentés** lehetőséget.

### <a name="create-key-optional"></a>Kulcs létrehozása (nem kötelező)

Opcionálisan létrehozhat egy kulcsot a Key vaultban a beállításjegyzék titkosításához. Kövesse az alábbi lépéseket, ha egy adott verziót szeretne ügyfél által felügyelt kulcsként kijelölni. 

1. Navigáljon a kulcstartóhoz.
1. Válassza a **Beállítások**  >  **kulcsok** elemet.
1. Válassza a **+ Létrehozás/importálás** lehetőséget, és adjon meg egy egyedi nevet a kulcshoz.
1. Fogadja el a többi alapértelmezett értéket, és válassza a **Létrehozás** lehetőséget.
1. A létrehozás után válassza ki a kulcsot, majd válassza ki az aktuális verziót. Másolja a **kulcs** verziószámát.

### <a name="create-azure-container-registry"></a>Azure Container Registry létrehozása

1. Válassza **az erőforrás létrehozása**  >  **tárolók**  >  **Container Registry** elemet.
1. Az **alapvető beállítások** lapon válasszon ki vagy hozzon létre egy erőforráscsoportot, és adjon meg egy beállításjegyzék-nevet. Az **SKU** területen válassza a **prémium** lehetőséget.
1. A **titkosítás** lap **ügyfél által felügyelt kulcs** területén válassza az **engedélyezve** lehetőséget.
1. Az **identitás** területen válassza ki a létrehozott felügyelt identitást.
1. A **titkosítás** területen válassza a következők egyikét:
    * Válassza **a kiválasztás Key Vault** lehetőséget, majd válasszon ki egy meglévő kulcstartót és kulcsot, vagy **hozzon létre újat**. A kiválasztott kulcs nem verziószámú, és lehetővé teszi az automatikus kulcs elforgatását.
    * Jelölje be a **kulcs URI**-azonosítójának megadása jelölőnégyzetet, és adjon meg közvetlenül egy kulcsot. Megadhat egy verziószámmal ellátott kulcs URI-JÁT (olyan kulcshoz, amelyet manuálisan kell elforgatni) vagy egy nem verziószámú kulcs URI-JÁT (ami lehetővé teszi az automatikus kulcs elforgatását). 
1. A **titkosítás** lapon válassza a **felülvizsgálat + létrehozás** elemet.
1. Válassza a **Létrehozás** lehetőséget a beállításjegyzék-példány telepítéséhez.

:::image type="content" source="media/container-registry-customer-managed-keys/create-encrypted-registry.png" alt-text="Titkosított beállításjegyzék létrehozása a Azure Portalban":::

Ha szeretné megtekinteni a beállításjegyzék titkosítási állapotát a portálon, keresse meg a beállításjegyzéket. A **Beállítások** területen válassza a  **titkosítás** lehetőséget.

## <a name="enable-customer-managed-key---template"></a>Ügyfél által felügyelt kulcs – sablon engedélyezése

Egy Resource Manager-sablonnal is létrehozhat egy beállításjegyzéket, és engedélyezheti a titkosítást az ügyfél által felügyelt kulccsal.

A következő sablon létrehoz egy új tároló-beállításjegyzéket és egy felhasználó által hozzárendelt felügyelt identitást. Másolja az alábbi tartalmat egy új fájlba, és mentse a fájlt egy olyan fájlnév használatával, mint például `CMKtemplate.json` .

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

Kövesse az előző szakaszokban ismertetett lépéseket a következő erőforrások létrehozásához:

* Key Vault, név alapján azonosítva
* Key Vault-kulcs, kulcs azonosítója alapján azonosítva

Az előző sablonfájl használatával hozza létre a beállításjegyzéket a következő az [Deployment Group Create][az-deployment-group-create] parancs futtatásával. Ha meg van jelölve, adja meg az új beállításjegyzék-nevet és a felügyelt identitás nevét, valamint a létrehozott kulcstároló nevét és AZONOSÍTÓját.

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

A beállításjegyzék-titkosítás állapotának megjelenítéséhez futtassa az az [ACR encryption show][az-acr-encryption-show] parancsot:

```azurecli
az acr encryption show --name <registry-name>
```

## <a name="use-the-registry"></a>A beállításjegyzék használata

Miután engedélyezte az ügyfél által felügyelt kulcsokat a beállításjegyzékben, ugyanazokat a beállításjegyzékbeli műveleteket hajthatja végre, amelyeket egy ügyfél által felügyelt kulccsal nem titkosított beállításjegyzékben hajt végre. Például a hitelesítést a beállításjegyzékben és a Docker-rendszerképek leküldésekor végezheti el. Lásd: példák a [leküldéses és a lekérési](container-registry-get-started-docker-cli.md)parancsokra.

## <a name="rotate-key"></a>Elforgatási kulcs

Frissítse a Azure Key Vaultban található kulcs verzióját, vagy hozzon létre egy új kulcsot, majd frissítse a beállításjegyzéket, hogy titkosítsa az adatait a kulcs használatával. Ezeket a lépéseket az Azure CLI használatával vagy a portálon végezheti el.

A kulcsok elforgatásakor általában ugyanazt az identitást kell megadnia, amelyet a beállításjegyzék létrehozásakor használ. Szükség esetén új, felhasználó által hozzárendelt identitást konfigurálhat a kulcs eléréséhez, vagy engedélyezheti és megadhatja a beállításjegyzék rendszer által hozzárendelt identitását.

> [!NOTE]
> Győződjön meg arról, hogy a szükséges [Key Vault-hozzáférés](#enable-key-vault-access) be van állítva a kulcs-hozzáféréshez konfigurált identitáshoz.

### <a name="update-key-version"></a>Kulcs verziójának frissítése

Gyakori forgatókönyv az ügyfél által felügyelt kulcsként használt kulcs verziójának frissítése. A beállításjegyzék-titkosítás konfigurálásának módjától függően a Azure Container Registry ügyfél által felügyelt kulcs automatikusan frissül, vagy manuálisan kell frissíteni.

### <a name="azure-cli"></a>Azure CLI

A Key Vault-kulcsok létrehozásához és kezeléséhez használja [az az kulcstartó Key][az-keyvault-key] parancsokat. Új kulcs verziójának létrehozásához futtassa az az [kulcstartó kulcs létrehozása][az-keyvault-key-create] parancsot:

```azurecli
# Create new version of existing key
az keyvault key create \
  –-name <key-name> \
  --vault-name <key-vault-name>
```

A következő lépés a beállításjegyzék titkosításának konfigurálási módjától függ:

* Ha a beállításjegyzék a kulcs verziófrissítésének észlelésére van konfigurálva, az ügyfél által felügyelt kulcs 1 órán belül automatikusan frissül.

* Ha a beállításjegyzék úgy van konfigurálva, hogy egy új kulcs manuális frissítését igényli, futtassa az az [ACR encryption forgatni-Key][az-acr-encryption-rotate-key] parancsot, és adja át az új kulcs azonosítóját és a konfigurálni kívánt identitást:

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
> Ha futtatja a parancsot `az acr encryption rotate-key` , a verziószámmal ellátott kulcs azonosítóját vagy a nem verziószámú kulcs azonosítóját is átadhatja. Ha nem verziószámmal ellátott kulcsot használ, a rendszer úgy konfigurálja a beállításjegyzéket, hogy automatikusan felderítse a verzió későbbi frissítéseit.

### <a name="portal"></a>Portál

A beállításjegyzék **titkosítási** beállításaival frissítheti az ügyfél által felügyelt kulcshoz használt Key Vault-, kulcs-vagy identitás-beállításokat.

Például egy új kulcs konfigurálásához:

1. A portálon navigáljon a beállításjegyzékhez.
1. A **Beállítások** területen válassza a **titkosítási**  >  **kulcs módosítása** elemet.

    :::image type="content" source="media/container-registry-customer-managed-keys/rotate-key.png" alt-text="Kulcs elforgatása a Azure Portalban":::
1. A **titkosítás** területen válasszon a következők közül:
    * Válassza **a kiválasztás Key Vault** lehetőséget, majd válasszon ki egy meglévő kulcstartót és kulcsot, vagy **hozzon létre újat**. A kiválasztott kulcs nem verziószámú, és lehetővé teszi az automatikus kulcs elforgatását.
    * Jelölje be a **kulcs URI**-azonosítójának megadása jelölőnégyzetet, és adjon meg közvetlenül egy kulcsot. Megadhat egy verziószámmal ellátott kulcs URI-JÁT (olyan kulcshoz, amelyet manuálisan kell elforgatni) vagy egy nem verziószámú kulcs URI-JÁT (ami lehetővé teszi az automatikus kulcs elforgatását).
1. Fejezze be a kulcs kijelölését, és válassza a **Mentés** lehetőséget.

## <a name="revoke-key"></a>Kulcs visszavonása

A Key Vault hozzáférési házirendjének vagy engedélyeinek módosításával vagy a kulcs törlésével vonja vissza az ügyfél által felügyelt titkosítási kulcsot. Például az az Key [Vault delete-Policy][az-keyvault-delete-policy] paranccsal módosíthatja a beállításjegyzék által használt felügyelt identitás hozzáférési házirendjét:

```azurecli
az keyvault delete-policy \
  --resource-group <resource-group-name> \
  --name <key-vault-name> \
  --object-id $identityPrincipalID
```

A kulcs visszavonása gyakorlatilag blokkolja az összes beállításjegyzék-adattal való hozzáférést, mivel a beállításjegyzék nem fér hozzá a titkosítási kulcshoz. Ha a kulcshoz való hozzáférés engedélyezve van, vagy a törölt kulcs vissza lett állítva, a beállításjegyzékben meg kell választania a kulcsot, hogy újra hozzáférhessen a titkosított beállításjegyzék-adataihoz.

## <a name="advanced-scenario-key-vault-firewall"></a>Speciális forgatókönyv: Key Vault tűzfal

Előfordulhat, hogy a titkosítási kulcsot egy [Key Vault tűzfallal](../key-vault/general/network-security.md)konfigurált, meglévő Azure Key Vault használatával szeretné tárolni, amely megtagadja a nyilvános hozzáférést, és csak a privát végpontot vagy a kiválasztott virtuális hálózatokat engedélyezi. 

Ebben az esetben először hozzon létre egy, az ügyfél által felügyelt kulccsal titkosított új, felhasználó által hozzárendelt identitást, kulcstartót és tároló-beállításjegyzéket az [Azure CLI](#enable-customer-managed-key---cli), [portál](#enable-customer-managed-key---portal)vagy [sablon](#enable-customer-managed-key---template)használatával. A részletes lépések a jelen cikk előző részében találhatók.
   > [!NOTE]
   > Az új kulcstartó üzembe helyezése a tűzfalon kívül történik. Csak ideiglenesen használatos az ügyfél által felügyelt kulcs tárolásához.

A beállításjegyzék létrehozása után folytassa a következő lépésekkel. A részletek a következő fejezetekben találhatók.

1. A beállításjegyzék rendszer által hozzárendelt identitásának engedélyezése.
1. Adja meg a rendszer által hozzárendelt identitási engedélyeket a kulcstartóban lévő kulcsok eléréséhez, amely a Key Vault tűzfallal van korlátozva.
1. Győződjön meg arról, hogy a Key Vault tűzfal engedélyezi a megbízható szolgáltatások megkerülését. Az Azure Container Registry jelenleg csak a rendszer által felügyelt identitás használata esetén tudja megkerülni a tűzfalat. 
1. Az ügyfél által felügyelt kulcs elforgatásához válasszon egyet a Key Vault tűzfallal korlátozott Key vaultban.
1. Ha már nincs rá szükség, a tűzfalon kívül létrehozott kulcstartót is törölheti.


### <a name="step-1---enable-registrys-system-assigned-identity"></a>1. lépés – a beállításjegyzék rendszer által hozzárendelt identitásának engedélyezése

1. A portálon navigáljon a beállításjegyzékhez.
1. Válassza a **Beállítások**  >   **identitás** lehetőséget.
1. A **rendszer által hozzárendelve** beállításnál állítsa be **a** következőt: **állapot** . Kattintson a **Mentés** gombra.
1. Másolja az identitás **objektum-azonosítóját** .

### <a name="step-2---grant-system-assigned-identity-access-to-your-key-vault"></a>2. lépés – a rendszerhez rendelt identitás hozzáférésének engedélyezése a kulcstartóhoz

1. A portálon navigáljon a kulcstartóhoz.
1. Válassza a **Beállítások**  >  **hozzáférési szabályzatok > + hozzáférési házirend hozzáadása** lehetőséget.
1. Válassza a **kulcs engedélyei** lehetőséget, majd a **beolvasás**, a **kicsomagolás** **és a** kicsomagolási kulcs elemet.
1. Válassza a **rendszerbiztonsági tag kiválasztása** lehetőséget, és keresse meg a rendszer által hozzárendelt felügyelt identitáshoz tartozó objektumazonosítót vagy a beállításjegyzék nevét.  
1. Válassza a **Hozzáadás**, majd a **Mentés** lehetőséget.

### <a name="step-3---enable-key-vault-bypass"></a>3. lépés – a Key Vault megkerülésének engedélyezése

Key Vault tűzfallal konfigurált kulcstartó eléréséhez a beállításjegyzéknek meg kell kerülnie a tűzfalat. Győződjön meg arról, hogy a Key Vault úgy van konfigurálva, hogy bármely [megbízható szolgáltatás](../key-vault/general/overview-vnet-service-endpoints.md#trusted-services)számára engedélyezze a hozzáférést. Azure Container Registry az egyik megbízható szolgáltatás.

1. A portálon navigáljon a kulcstartóhoz.
1. Válassza a **Beállítások**  >  **hálózatkezelés** lehetőséget.
1. Virtuális hálózati beállítások megerősítése, frissítése vagy hozzáadása. Részletes lépések: [Azure Key Vault tűzfalak és virtuális hálózatok konfigurálása](../key-vault/general/network-security.md).
1. A **tűzfal megkerülésének engedélyezése a Microsoft megbízható szolgáltatások számára** területen válassza az **Igen** lehetőséget. 

### <a name="step-4---rotate-the-customer-managed-key"></a>4. lépés – az ügyfél által felügyelt kulcs elforgatása

Az előző lépések elvégzése után forgassa el a tűzfal mögött található kulcstartóban tárolt kulcsot.

1. A portálon navigáljon a beállításjegyzékhez.
1. A **Beállítások** területen válassza a **titkosítási**  >  **kulcs módosítása** elemet.
1. Az **identitás** területen válassza ki a **rendszer által hozzárendelt** elemet.
1. Válassza a **kijelölés a következőből Key Vault** lehetőséget, majd válassza ki a tűzfal mögött található kulcstartó nevét.
1. Válasszon ki egy meglévő kulcsot, vagy **hozzon létre újat**. A kiválasztott kulcs nem verziószámú, és lehetővé teszi az automatikus kulcs elforgatását.
1. Fejezze be a kulcs kijelölését, és válassza a **Mentés** lehetőséget.

## <a name="troubleshoot"></a>Hibaelhárítás

### <a name="removing-managed-identity"></a>Felügyelt identitás eltávolítása


Ha egy, a titkosítás konfigurálására szolgáló beállításjegyzékből kísérli meg eltávolítani a felhasználó által hozzárendelt vagy rendszerhez rendelt felügyelt identitást, a következőhöz hasonló hibaüzenet jelenhet meg:
 
```
Azure resource '/subscriptions/xxxx/resourcegroups/myGroup/providers/Microsoft.ContainerRegistry/registries/myRegistry' does not have access to identity 'xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx' Try forcibly adding the identity to the registry <registry name>. For more information on bring your own key, please visit 'https://aka.ms/acr/cmk'.
```
 
A titkosítási kulcs nem módosítható (elforgatható). A megoldás lépései a titkosításhoz használt identitás típusától függenek.

**Felhasználó által hozzárendelt identitás**

Ha ez a probléma felhasználó által hozzárendelt identitással fordul elő, először újra hozzá kell rendelnie az identitást a hibaüzenetben megjelenő GUID azonosítóval. Például:

```azurecli
az acr identity assign -n myRegistry --identities xxxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx
```
        
Ezután a kulcs módosítása és egy másik identitás hozzárendelése után eltávolíthatja az eredeti, felhasználó által hozzárendelt identitást.

**Rendszer által hozzárendelt identitás**

Ha a probléma rendszerhez rendelt identitással fordul elő, [hozzon létre egy Azure-támogatási jegyet](https://azure.microsoft.com/support/create-ticket/) , amely segítséget nyújt az identitás helyreállításához.


## <a name="next-steps"></a>Következő lépések

* További információ [Az Azure-](../security/fundamentals/encryption-atrest.md)beli inaktív adatok titkosításáról.
* További információ a hozzáférési házirendekről és a [kulcstartóhoz való hozzáférés biztonságossá](../key-vault/general/secure-your-key-vault.md)tételéről.


<!-- LINKS - external -->

<!-- LINKS - internal -->

[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-show]: /cli/azure/feature#az-feature-show
[az-group-create]: /cli/azure/group#az-group-create
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-deployment-group-create]: /cli/azure/deployment/group#az-deployment-group-create
[az-keyvault-create]: /cli/azure/keyvault#az-keyvault-create
[az-keyvault-key-create]: /cli/azure/keyvault/key#az-keyvault-key-create
[az-keyvault-key]: /cli/azure/keyvault/key
[az-keyvault-set-policy]: /cli/azure/keyvault#az-keyvault-set-policy
[az-keyvault-delete-policy]: /cli/azure/keyvault#az-keyvault-delete-policy
[az-resource-show]: /cli/azure/resource#az-resource-show
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-encryption-rotate-key]: /cli/azure/acr/encryption#az-acr-encryption-rotate-key
[az-acr-encryption-show]: /cli/azure/acr/encryption#az-acr-encryption-show
