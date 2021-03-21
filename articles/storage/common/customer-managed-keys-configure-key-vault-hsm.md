---
title: A titkosítás konfigurálása Azure Key Vault felügyelt HSM-ben (előzetes verzió) tárolt, ügyfél által felügyelt kulcsokkal
titleSuffix: Azure Storage
description: Megtudhatja, hogyan konfigurálhatja az Azure Storage-titkosítást Azure Key Vault felügyelt HSM-ben (előzetes verzió) tárolt, az Azure CLI használatával felügyelt kulcsokkal.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: f9be9272a898ad48f3553d4c5e48952e1fcdde81
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102218638"
---
# <a name="configure-encryption-with-customer-managed-keys-stored-in-azure-key-vault-managed-hsm-preview"></a>A titkosítás konfigurálása Azure Key Vault felügyelt HSM-ben (előzetes verzió) tárolt, ügyfél által felügyelt kulcsokkal

Az Azure Storage minden olyan adattárolót titkosít, amely egy Storage-fiókban található. Alapértelmezés szerint az adattitkosítás a Microsoft által kezelt kulcsokkal történik. A titkosítási kulcsok további vezérléséhez a saját kulcsait is kezelheti. Az ügyfél által felügyelt kulcsokat Azure Key Vault vagy Key Vault felügyelt hardveres biztonsági modellben (HSM) (előzetes verzió) kell tárolni. A Azure Key Vault felügyelt HSM egy FIPS 140-2 3. szintű hitelesített HSM.

Ez a cikk bemutatja, hogyan konfigurálhatja a titkosítást a felügyelt HSM-ben tárolt, ügyfél által felügyelt kulcsokkal az Azure CLI használatával. Ha meg szeretné tudni, hogyan konfigurálhatja a titkosítást a Key vaultban tárolt ügyfél által felügyelt kulcsokkal, tekintse meg a [titkosítás konfigurálása a Azure Key Vaultban tárolt ügyfél által felügyelt kulcsokkal című részt](customer-managed-keys-configure-key-vault.md).

> [!IMPORTANT]
>
> Azure Key Vault felügyelt HSM-ben tárolt, ügyfél által felügyelt kulcsokkal való titkosítás jelenleg **előzetes** verzióban érhető el. Tekintse meg az Azure-szolgáltatásokra vonatkozó, a bétaverzióban, az előzetes verzióban, vagy más módon még nem közzétett, általánosan elérhetővé vált jogi feltételekhez tartozó [Microsoft Azure előzetes verziójának kiegészítő használati feltételeit](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) .
>
> A Azure Key Vault és Azure Key Vault felügyelt HSM a konfigurációhoz ugyanazokat az API-kat és felügyeleti interfészeket támogatja.

## <a name="assign-an-identity-to-the-storage-account"></a>Identitás kiosztása a Storage-fiókhoz

Először rendeljen hozzá egy rendszerhez rendelt felügyelt identitást a Storage-fiókhoz. Ezt a felügyelt identitást fogja használni a Storage-fiók engedélyeinek megadásához a felügyelt HSM eléréséhez. A rendszerhez rendelt felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitásai?](../../active-directory/managed-identities-azure-resources/overview.md).

Felügyelt identitás az Azure CLI-vel való hozzárendeléséhez hívja [az az Storage Account Update](/cli/azure/storage/account#az-storage-account-update)lehetőséget. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire:

```azurecli
az storage account update \
    --name <storage-account> \
    --resource-group <resource_group> \
    --assign-identity
```

## <a name="assign-a-role-to-the-storage-account-for-access-to-the-managed-hsm"></a>Szerepkör társítása a Storage-fiókhoz a felügyelt HSM-hez való hozzáféréshez

Ezután rendelje hozzá a **felügyelt HSM titkosítási szolgáltatás titkosítási** szerepkörét a Storage-fiók felügyelt identitásához, hogy a Storage-fiók rendelkezik a felügyelt HSM-hez szükséges engedélyekkel. A Microsoft azt javasolja, hogy a szerepkör-hozzárendelést az egyéni kulcs szintjéhez rendelje, hogy a lehető legkevesebb jogosultságot adja a felügyelt identitásnak.

A Storage-fiók szerepkör-hozzárendelésének létrehozásához hívja az [az Key Vault szerepkör-hozzárendelés létrehozása](/cli/azure/role/assignment#az_role_assignment_create)lehetőséget. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.
  
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

## <a name="configure-encryption-with-a-key-in-the-managed-hsm"></a>Titkosítás konfigurálása kulccsal a felügyelt HSM-ben

Végül konfigurálja az Azure Storage-titkosítást az ügyfél által felügyelt kulcsokkal a felügyelt HSM-ben tárolt kulcsok használatára. A támogatott típusok közé tartoznak a 2048, 3072 és 4096 méretű RSA-HSM-kulcsok. Telepítse az Azure CLI-2.12.0 vagy újabb verzióját úgy, hogy a titkosítást a felügyelt HSM-ben ügyfél által felügyelt kulcs használatára konfigurálja. További információ: [Az Azure CLI telepítése](/cli/azure/install-azure-cli).

Az ügyfél által felügyelt kulcs verziószámának automatikus frissítéséhez hagyja ki a kulcs verzióját, ha a Storage-fiókhoz az ügyfél által felügyelt kulcsokkal konfigurálja a titkosítást. Az alábbi példában látható módon frissítse a Storage-fiók titkosítási beállításait az az [Storage Account Update](/cli/azure/storage/account#az_storage_account_update) paranccsal. Adja meg a `--encryption-key-source parameter` és a beállítását, hogy `Microsoft.Keyvault` engedélyezze az ügyfél által felügyelt kulcsokat a fiókhoz. Ne felejtse el lecserélni a zárójelben lévő helyőrző értékeket a saját értékeire.

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

Az ügyfél által felügyelt kulcs verziójának manuális frissítéséhez adja meg a kulcs verzióját a Storage-fiók titkosításának konfigurálásakor:

```azurecli-interactive
az storage account update
    --name <storage-account> \
    --resource-group <resource_group> \
    --encryption-key-name <key> \
    --encryption-key-version $key_version \
    --encryption-key-source Microsoft.Keyvault \
    --encryption-key-vault $hsmurl
```

Amikor manuálisan frissíti a kulcs verzióját, frissítenie kell a Storage-fiók titkosítási beállításait az új verzió használatára. Első lépésként a Key Vault URI-JÁT az [az kulcstartó show](/cli/azure/keyvault#az-keyvault-show)paranccsal hívhatja meg, és a kulcs verziószámát az az [kulcstartó Key List-Versions](/cli/azure/keyvault/key#az-keyvault-key-list-versions)paranccsal hívja meg. Ezt követően az az [Storage Account Update](/cli/azure/storage/account#az-storage-account-update) paranccsal frissítse a Storage-fiók titkosítási beállításait a kulcs új verziójának használatára az előző példában látható módon.

## <a name="next-steps"></a>Következő lépések

- [Inaktív adatok Azure Storage-titkosítása](storage-service-encryption.md)
- [Ügyfél által felügyelt kulcsok az Azure Storage-titkosításhoz](customer-managed-keys-overview.md)
