---
title: Titkosítás konfigurálása felügyelt HSM-Azure Key Vault ügyfél által kezelt kulcsokkal (előzetes verzió)
titleSuffix: Azure Storage
description: Megtudhatja, hogyan konfigurálhatja az Azure Storage-titkosítást a Managed HSM előzetes Azure Key Vault tárolt, ügyfél által kezelt kulcsokkal az Azure CLI használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/30/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9b40c934cb428a31a3feb77195518d5351818d7
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785360"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>Titkosítás konfigurálása felügyelt HSM-Azure Key Vault ügyfél által kezelt kulcsokkal (előzetes verzió)

Az Azure Storage egy tárfiókban tárolt összes adatot titkosítja. Alapértelmezés szerint az adatok titkosítása Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további szabályozása érdekében kezelheti a saját kulcsait. Az ügyfél által felügyelt kulcsokat a felügyelt Azure Key Vault (HSM) Key Vault (előzetes verzió) szolgáltatásban kell tárolni. A Azure Key Vault HSM egy FIPS 140-2 3. szint szerint ellenőrzött HSM.

Ez a cikk bemutatja, hogyan konfigurálhatja a titkosítást felügyelt HSM-ben tárolt, ügyfél által kezelt kulcsokkal az Azure CLI használatával. A kulcstartóban tárolt, ügyfél által kezelt kulcsokkal való titkosítás konfigurálásával kapcsolatos információkért lásd: Titkosítás konfigurálása a kulcstartóban tárolt, ügyfél által kezelt [kulcsokkal Azure Key Vault.](customer-managed-keys-configure-key-vault.md)

> [!IMPORTANT]
>
> A Managed HSM-Azure Key Vault ügyfél által kezelt kulcsokkal való titkosítás jelenleg előzetes **verzióban érhető el.** A [bétaverzióban,](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) előzetes verzióban vagy egyéb módon, általánosan elérhető Azure-funkciókra vonatkozó jogi feltételekért tekintse meg a kiegészítő használati feltételeket a Microsoft Azure előzetes verziókhoz.
>
> Azure Key Vault és Azure Key Vault Managed HSM ugyanazon API-kat és felügyeleti felületeket támogatja a konfiguráláshoz.

## <a name="assign-an-identity-to-the-storage-account"></a>Identitás hozzárendelése a tárfiókhoz

Először rendeljen hozzá egy rendszer által hozzárendelt felügyelt identitást a tárfiókhoz. Ezzel a felügyelt identitással adhat engedélyeket a tárfióknak a felügyelt HSM eléréséhez. A rendszer által hozzárendelt felügyelt identitásokkal kapcsolatos további információkért lásd: Mik azok az [Azure-erőforrások felügyelt identitások?](../../active-directory/managed-identities-azure-resources/overview.md).

Ha felügyelt identitást szeretne hozzárendelni az Azure CLI használatával, hívja az [az storage account update (az storage account update) hívást.](/cli/azure/storage/account#az_storage_account_update) Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Szerepkör hozzárendelése a tárfiókhoz a felügyelt HSM-hez való hozzáféréshez

Ezután rendelje hozzá a **Managed HSM Crypto Service Encryption** szerepkört a tárfiók felügyelt identitásához, hogy a tárfiók jogosultságokkal rendelkezik a felügyelt HSM-hez. A Microsoft azt javasolja, hogy a szerepkör-hozzárendelés hatókörét az egyes kulcsok szintjére kell hatókörbe tenni, hogy a lehető legkisebb jogosultságot adja meg a felügyelt identitásnak.

A tárfiók szerepkör-hozzárendelésének létrehozásához hívja meg [az az key vault role assignment create hívást.](/cli/azure/role/assignment#az_role_assignment_create) Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire.
  
```azurecli
storage_account_principal = $(az storage account show \
    --name <storage-account> \
    --resource-group <resource-group> \
    --query identity.principalId \
    --output tsv)

az keyvault role assignment create \
    --hsm-name <hsm-name> \
    --role "Managed HSM Crypto Service Encryption" \
    --assignee $storage_account_principal \
    --scope /keys/<key-name>
```

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Titkosítás konfigurálása kulccsal a felügyelt HSM-hez

Végül konfigurálja az Azure Storage-titkosítást az ügyfél által felügyelt kulcsokkal a felügyelt HSM-ban tárolt kulcs használatára. A támogatott kulcstípusok közé tartoznak a 2048-as, 3072-es és 4096-os RSA-HSM-kulcsok. A kulcsok felügyelt HSM-ben való létrehozásáról a [HSM-kulcs létrehozása cikkből olvashat.](../../key-vault/managed-hsm/key-management.md#create-an-hsm-key)

Telepítse az Azure CLI 2.12.0-s vagy újabbát, hogy a titkosítást úgy konfigurálja, hogy az ügyfél által felügyelt kulcsot használjon egy felügyelt HSM-ben. További információ: [Az Azure CLI telepítése.](/cli/azure/install-azure-cli)

Ha automatikusan frissítenie kell egy ügyfél által felügyelt kulcs kulcsverzióját, akkor ne használja a kulcsverziót, amikor ügyfél által kezelt kulcsokkal konfigurálja a titkosítást a tárfiókhoz. Hívja [meg az az storage account update](/cli/azure/storage/account#az_storage_account_update) parancsot a tárfiók titkosítási beállításainak frissítéséhez az alábbi példában látható módon. Adja hozzá a et, és állítsa be a következőre: , hogy engedélyezze az `--encryption-key-source parameter` `Microsoft.Keyvault` ügyfél által kezelt kulcsokat a fiókhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrzőket a saját értékeire.

```azurecli
hsmurl = $(az keyvault show \
    --hsm-name <hsm-name> \
    --query properties.hsmUri \
    --output tsv)

az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Az ügyfél által felügyelt kulcs verziójának manuális frissítéséhez adja meg a kulcsverziót a tárfiók titkosításának konfigurálásakor:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Amikor manuálisan frissíti a kulcsverziót, frissítenie kell a tárfiók titkosítási beállításait az új verzió használatára. Először a key vault URI-ját kell lekérdezni [az az keyvault show](/cli/azure/keyvault#az_keyvault_show)hívásával, a kulcsverzióhoz pedig az az [keyvault key list-versions hívásával.](/cli/azure/keyvault/key#az_keyvault_key_list_versions) Ezután hívja [meg az az storage account update](/cli/azure/storage/account#az_storage_account_update) parancsot a tárfiók titkosítási beállításainak a kulcs új verziójának használatára való frissítéséhez, ahogy az előző példában is látható.

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Felhasználó által kezelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
