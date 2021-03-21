---
title: A Storage-fiók kulcsainak kezelése a Azure Key Vault és az Azure CLI használatával
description: A Storage-fiókok kulcsai zökkenőmentes integrációt biztosítanak a Azure Key Vault és a kulcs-alapú Azure Storage-fiókhoz való hozzáférés között.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
manager: rkarlin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: e89716d0560cbf7960cb7bde67156c8df0045a31
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499220"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>A Storage-fiók kulcsainak kezelése a Key Vault és az Azure CLI használatával
> [!IMPORTANT]
> Javasoljuk, hogy az Azure Storage-integrációt Azure Active Directory (Azure AD), a Microsoft felhőalapú identitás-és hozzáférés-kezelési szolgáltatásával használja. Az Azure AD-integráció az [Azure-blobok és-várólisták](../../storage/common/storage-auth-aad.md)számára érhető el, és OAuth2 token-alapú hozzáférést biztosít az Azure Storage-hoz (akárcsak Azure Key Vault). Az Azure AD lehetővé teszi az ügyfélalkalmazás hitelesítését alkalmazás vagy felhasználói identitás használatával a Storage-fiók hitelesítő adatai helyett. Azure AD-beli [felügyelt identitást](../../active-directory/managed-identities-azure-resources/index.yml) használhat az Azure-ban való futtatáskor. A felügyelt identitások nem szükségesek az ügyfél-hitelesítéshez és a hitelesítő adatok tárolásához a vagy az alkalmazásban. Az alábbi megoldás csak akkor használható, ha az Azure AD-hitelesítés nem lehetséges.

Az Azure Storage-fiók a fiók nevét és kulcsát tartalmazó hitelesítő adatokat használ. A kulcs automatikusan jön létre, és jelszóként szolgál, nem pedig titkosítási kulcsként. Key Vault kezeli a Storage-fiókok kulcsait a Storage-fiókba való rendszeres újragenerálással, és megosztott hozzáférési aláírási jogkivonatokat biztosít a Storage-fiók erőforrásaihoz delegált hozzáféréshez.

A Key Vault felügyelt Storage-fiók kulcsa funkció használatával listázhatja (szinkronizálhatja) a kulcsokat egy Azure Storage-fiókkal, és rendszeresen újragenerálhatja (elforgathatja) a kulcsokat. A kulcsokat a Storage-fiókok és a klasszikus Storage-fiókok esetében is kezelheti.

A felügyelt Storage-fiók kulcsa funkció használata esetén vegye figyelembe a következő szempontokat:

- A rendszer soha nem adja vissza a kulcs értékeit a hívónak adott válaszként.
- Csak Key Vault kell kezelnie a Storage-fiók kulcsait. Ne kezelje a kulcsokat, és ne zavarja a Key Vault folyamatokat.
- Csak egyetlen Key Vault objektumnak kell kezelnie a Storage-fiók kulcsait. Ne engedélyezze a kulcskezelő szolgáltatás több objektumból való felügyeletét.
- Kulcsok újragenerálása csak Key Vault használatával. Ne végezze el manuálisan a Storage-fiók kulcsainak újragenerálása.

## <a name="service-principal-application-id"></a>Alkalmazáspéldány – Alkalmazásazonosító

Az Azure AD-bérlő minden regisztrált alkalmazást biztosít egy [egyszerű szolgáltatással](../../active-directory/develop/developer-glossary.md#service-principal-object). Az egyszerű szolgáltatás az alkalmazás-AZONOSÍTÓként szolgál, amelyet az engedélyezési beállítások az Azure RBAC-n keresztül való hozzáféréshez használhatnak más Azure-erőforrásokhoz.

A Key Vault egy olyan Microsoft-alkalmazás, amely az összes Azure AD-bérlőben előre regisztrálva van. A Key Vault minden Azure-felhőben ugyanazzal az alkalmazás-AZONOSÍTÓval van regisztrálva.

| Bérlők | Felhőbeli | Alkalmazásazonosító |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure – nyilvános | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Egyéb  | Bármelyik | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Előfeltételek

Az útmutató elvégzéséhez először a következőket kell tennie:

- [Telepítse az Azure CLI](/cli/azure/install-azure-cli)-t.
- [Kulcstartó létrehozása](quick-create-cli.md)
- [Hozzon létre egy Azure Storage-fiókot](../../storage/common/storage-account-create.md?tabs=azure-cli). A Storage-fiók nevének csak kisbetűket és számokat kell használnia. A név hosszának 3 és 24 karakter közöttinek kell lennie.
      
## <a name="manage-storage-account-keys"></a>A Storage-fiók kulcsainak kezelése

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Hitelesítse Azure CLI-munkamenetét az az [login](/powershell/module/az.accounts/connect-azaccount) parancsok használatával.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Key Vault hozzáférés biztosítása a Storage-fiókhoz

A Storage-fiók eléréséhez használja az Azure CLI az [role hozzárendelés Create](/cli/azure/role/assignment) paranccsal Key Vault. Adja meg a parancsot a következő paraméter-értékekkel:

- `--role`: Adja át a "Storage-fiók kulcsát kezelő szolgáltatás szerepkör" Azure-szerepkört. Ez a szerepkör korlátozza a hozzáférési hatókört a Storage-fiókra. Klasszikus Storage-fiók esetén a "klasszikus Storage-fiók kulcs-kezelője" szerepkört adja át helyette.
- `--assignee`: Adja át a " https://vault.azure.net " értéket, amely az Azure nyilvános felhőben Key Vault URL-címe. (Az Azure kormányzati-felhőben használja a "--asingee-Object-id" helyet, lásd: [szolgáltatásnév alkalmazás azonosítója](#service-principal-application-id).)
- `--scope`: Adja meg a Storage-fiók erőforrás-AZONOSÍTÓját, amely az űrlapon található `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Az előfizetés AZONOSÍTÓjának megkereséséhez használja az Azure CLI az [Account List](/cli/azure/account?#az-account-list) parancsot; a Storage-fiók neve és a Storage-fiók erőforráscsoport megkereséséhez használja az Azure CLI az [Storage Account List](/cli/azure/storage/account?#az-storage-account-list) parancsot.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Felhasználói fiók engedélyezése a felügyelt Storage-fiókok számára

Az Azure CLI az kulcstartó [-set-Policy](/cli/azure/keyvault?#az-keyvault-set-policy) parancsmag használatával frissítse a Key Vault hozzáférési szabályzatot, és adja meg a felhasználói fiókhoz tartozó Storage-fiók engedélyeit.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Vegye figyelembe, hogy a Storage-fiókokra vonatkozó engedélyek nem érhetők el a Azure Portal "hozzáférési szabályzatok" lapján.
### <a name="create-a-key-vault-managed-storage-account"></a>Key Vault felügyelt Storage-fiók létrehozása

 Hozzon létre egy Key Vault felügyelt Storage-fiókot az Azure CLI az kulcstartó [Storage](/cli/azure/keyvault/storage?#az-keyvault-storage-add) paranccsal. Állítsa be a 90 napos újragenerálási időszakot. Az elforgatás ideje alatt a kulcstartó újra létrehozza a nem aktív kulcsot, majd az újonnan létrehozott kulcsot aktívként állítja be. A rendszer csak az egyik kulcsot használja az SAS-tokenek egyidejű kiküldésére, ez az aktív kulcs. Adja meg a parancsot a következő paraméter-értékekkel:

- `--vault-name`: Adja át a kulcstartó nevét. A Key Vault nevének megkereséséhez használja az Azure CLI az kulcstartó [List](/cli/azure/keyvault?#az-keyvault-list) parancsot.
- `-n`: Adja meg a Storage-fiók nevét. A Storage-fiók nevének megkereséséhez használja az Azure CLI az [Storage Account List](/cli/azure/storage/account?#az-storage-account-list) parancsot.
- `--resource-id`: Adja meg a Storage-fiók erőforrás-AZONOSÍTÓját, amely az űrlapon található `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Az előfizetés AZONOSÍTÓjának megkereséséhez használja az Azure CLI az [Account List](/cli/azure/account?#az-account-list) parancsot; a Storage-fiók neve és a Storage-fiók erőforráscsoport megkereséséhez használja az Azure CLI az [Storage Account List](/cli/azure/storage/account?#az-storage-account-list) parancsot.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Közös hozzáférésű aláírási jogkivonatok

Azt is megteheti, Key Vault hogy közös hozzáférésű aláírási jogkivonatokat állítson elő. A közös hozzáférésű aláírások delegált hozzáférést biztosítanak a Storage-fiók erőforrásaihoz. A fiók kulcsainak megosztása nélkül megadhatja az ügyfeleknek a Storage-fiók erőforrásaihoz való hozzáférést. A közös hozzáférésű aláírás biztonságos módot biztosít a tárolási erőforrások megosztására a fiók kulcsainak veszélyeztetése nélkül.

Az ebben a szakaszban szereplő parancsok a következő műveleteket hajtják végre:

- Fiók közös hozzáférésű aláírás-definíciójának beállítása `<YourSASDefinitionName>` . A definíció egy Key Vault felügyelt Storage-fiókban van beállítva a `<YourStorageAccountName>` kulcstartóban `<YourKeyVaultName>` .
- Hozzon létre egy fiók közös hozzáférési aláírási tokent a blob-, fájl-, tábla-és üzenetsor-szolgáltatásokhoz. A jogkivonat az erőforrástípusok szolgáltatás, a tároló és az objektum számára lett létrehozva. A jogkivonat minden engedélyekkel, HTTPS-kapcsolattal és a megadott kezdési és befejezési dátumokkal jön létre.
- Key Vault felügyelt tároló közös hozzáférésű aláírás-definíciójának beállítása a tárban. A definíció a megosztott hozzáférés-aláírási jogkivonat sablonjának URI-JÁT hozza létre. A definíció a közös hozzáférési aláírás típusát adja `account` meg, és N napig érvényes.
- Ellenőrizze, hogy a közös hozzáférésű aláírás mentve lett-e a Key vaultban titkos kulcsként.

### <a name="create-a-shared-access-signature-token"></a>Közös hozzáférésű aláírási jogkivonat létrehozása

Hozzon létre egy közös hozzáférésű aláírás-definíciót az Azure CLI az [Storage Account regenerált-sas](/cli/azure/storage/account?#az-storage-account-generate-sas) parancs használatával. Ehhez a művelethez a és az engedélyek szükségesek `storage` `setsas` .


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
A művelet sikeres futtatása után másolja ki a kimenetet.

```console
"se=2020-01-01&sp=***"
```

Ez a kimenet a `--template-uri` következő lépésben a paraméternek lesz átadva.

### <a name="generate-a-shared-access-signature-definition"></a>Közös hozzáférésű aláírás definíciójának létrehozása

A közös hozzáférésű aláírás definíciójának létrehozásához használja az Azure CLI az Key [Vault Storage sas-definition Create](/cli/azure/keyvault/storage/sas-definition?#az-keyvault-storage-sas-definition-create) parancsot, és adja át az előző lépés eredményét a `--template-uri` paraméternek.  Megadhatja az Ön által választott nevet a `-n` paraméternek.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>A közös hozzáférésű aláírás definíciójának ellenőrzése

Ellenőrizheti, hogy a közös hozzáférésű aláírás definíciója a kulcstartóban van-e tárolva az Azure CLI az Key [Vault Storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) parancs használatával.

Most már használhatja az az kulcstartó [Storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) parancsot és a `id` tulajdonságot a titkos kód tartalmának megtekintéséhez.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Következő lépések

- További információ a [kulcsokról, a titkokról és a tanúsítványokról](/rest/api/keyvault/).
- Tekintse át a [Azure Key Vault csapat blogján](/archive/blogs/kv/)található cikkeket.
- Lásd az az kulcstartó [Storage](/cli/azure/keyvault/storage) Reference dokumentációját.
