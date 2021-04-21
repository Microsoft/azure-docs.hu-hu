---
title: Tárfiókkulcsok kezelése a Azure Key Vault és az Azure CLI használatával
description: A tárfiókkulcsok zökkenőmentes integrációt biztosítanak Azure Key Vault azure-tárfiókhoz való hozzáférés és kulcsalapú hozzáférés között.
ms.topic: tutorial
services: key-vault
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/18/2019
ms.custom: devx-track-azurecli
ms.openlocfilehash: 871c9f83d7bdc3f4c9e2f3b53c0b855483995152
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748609"
---
# <a name="manage-storage-account-keys-with-key-vault-and-the-azure-cli"></a>Tárfiókkulcsok kezelése a Key Vault és az Azure CLI használatával
> [!IMPORTANT]
> Javasoljuk, hogy az Azure Storage-integrációt Azure Active Directory (Azure AD), a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatásával. Az Azure AD-integráció elérhető az [Azure-blobok](../../storage/common/storage-auth-aad.md)és -üzenetsorok számára, és OAuth2 jogkivonat-alapú hozzáférést biztosít az Azure Storage-hoz (a Azure Key Vault). Az Azure AD lehetővé teszi az ügyfélalkalmazás hitelesítését egy alkalmazás vagy felhasználói identitás használatával a tárfiók hitelesítő adatai helyett. Azure [AD-beli](../../active-directory/managed-identities-azure-resources/index.yml) felügyelt identitást az Azure-on való futtatáskor használhat. A felügyelt identitások nem igényelnek ügyfél-hitelesítést, és nem kell az alkalmazásban vagy az alkalmazásban tárolni a hitelesítő adatokat. Az alábbi megoldást csak akkor használja, ha az Azure AD-hitelesítés nem lehetséges.

Az Azure Storage-fiók hitelesítő adatokat használ, amelyek egy fióknevet és egy kulcsot tartalmaznak. A rendszer automatikusan generálja a kulcsot, és nem titkosítási kulcsként, hanem jelszóként szolgál. Key Vault kezeli a tárfiókkulcsokat azáltal, hogy rendszeres időközönként újra létrehozza őket a tárfiókban, és közös hozzáférésű jogosultsági jogkivonatokat biztosít a tárfiókban található erőforrásokhoz való delegált hozzáféréshez.

A felügyelt tárfiók Key Vault funkcióval listálhatja (szinkronizálhatja) a kulcsokat egy Azure Storage-fiókkal, és rendszeres időközönként újragenerálhatja (elforgathatja) a kulcsokat. A tárfiókok és a klasszikus tárfiókok kulcsait is kezelheti.

A felügyelt tárfiókkulcs funkció használata során vegye figyelembe a következő pontokat:

- A hívónak adott válaszban a kulcsértékek soha nem térnek vissza.
- Csak Key Vault kell kezelnie a tárfiók kulcsait. Ne kezelje saját maga a kulcsokat, és ne zavarja a Key Vault folyamatait.
- Csak egyetlen Key Vault kell kezelnie a tárfiók kulcsait. Ne engedélyezze a kulcskezelést több objektumból.
- Kulcsok újragenerálása csak Key Vault használatával. Ne manuálisan újragenerálja a tárfiók kulcsait.

## <a name="service-principal-application-id"></a>Alkalmazáspéldány – Alkalmazásazonosító

Az Azure AD-bérlő minden regisztrált alkalmazást biztosít egy [szolgáltatásnévvel.](../../active-directory/develop/developer-glossary.md#service-principal-object) A szolgáltatásnév az alkalmazásazonosítóként szolgál, amelyet a rendszer az engedélyezés beállításakor használ más Azure-erőforrások Azure RBAC-n keresztüli eléréséhez.

Key Vault egy Olyan Microsoft-alkalmazás, amely minden Azure AD-bérlőben előre regisztrálva van. Key Vault azonos alkalmazásazonosító alatt van regisztrálva az egyes Azure-felhőkben.

| Bérlők | Felhőbeli | Alkalmazásazonosító |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure – nyilvános | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Egyéb  | Bármelyik | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez először a következőket kell végrehajtania:

- [Telepítse az Azure CLI-t.](/cli/azure/install-azure-cli)
- [Kulcstartó létrehozása](quick-create-cli.md)
- [Hozzon létre egy Azure Storage-fiókot.](../../storage/common/storage-account-create.md?tabs=azure-cli) A tárfiók neve csak kisbetűket és számokat használhat. A név hossza 3–24 karakter hosszúságú lehet.
      
## <a name="manage-storage-account-keys"></a>Tárfiókkulcsok kezelése

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Hitelesítse az Azure CLI-munkamenetet [az az login](/powershell/module/az.accounts/connect-azaccount) parancsokkal.

```azurecli-interactive
az login
``` 

### <a name="give-key-vault-access-to-your-storage-account"></a>Hozzáférés Key Vault a tárfiókhoz

Az Azure CLI [az role assignment create parancsának](/cli/azure/role/assignment) használatával hozzáférést Key Vault a tárfiókhoz. Adja meg a parancsot a következő paraméterértékekkel:

- `--role`: Adja át a "Tárfiókkulcs-kezelői szolgáltatási szerepkör" Azure-szerepkört. Ez a szerepkör korlátozza a tárfiók hozzáférési hatókörét. Klasszikus tárfiókok ehelyett a "Klasszikus tárfiókkulcs-kezelői szolgáltatásszerepkével" ehelyett a "Klasszikus tárfiókkulcs-kezelői szerepkör" szerepkört adja át.
- `--assignee`: Adja meg a " " értéket, amely a nyilvános https://vault.azure.net Azure Key Vault ban található virtuális szolgáltatások URL-címe. (Az Azure Goverment-felhőhöz használja inkább az --asingee-object-id azonosítót, lásd: Egyszerű szolgáltatás [alkalmazásazonosítója.)](#service-principal-application-id)
- `--scope`: Adja meg a tárfiók erőforrás-azonosítóját, amely a következő formában van: `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Az előfizetés-azonosító megkereséhez használja az Azure CLI [az account list parancsot;](/cli/azure/account?#az-account-list) A tárfiók nevét és a tárfiók erőforráscsoportját az Azure CLI [az storage account list paranccsal találhatja](/cli/azure/storage/account?#az-storage-account-list) meg.

```azurecli-interactive
az role assignment create --role "Storage Account Key Operator Service Role" --assignee 'https://vault.azure.net' --scope "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```
### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Felhasználói fiók engedélyének adjuk a felügyelt tárfiókok számára

Az Azure CLI [az keyvault-set-policy](/cli/azure/keyvault?#az-keyvault-set-policy) parancsmaggal frissítheti az Key Vault hozzáférési szabályzatot, és tárfiók-engedélyeket adhat a felhasználói fiókjának.

```azurecli-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --storage-permissions get list delete set update regeneratekey getsas listsas deletesas setsas recover backup restore purge
```

Vegye figyelembe, hogy a tárfiókok engedélyei nem érhetők el a tárfiók "Hozzáférési szabályzatok" lapján a Azure Portal.
### <a name="create-a-key-vault-managed-storage-account"></a>Felügyelt Key Vault tárfiók létrehozása

 Hozzon létre Key Vault felügyelt tárfiókot az Azure CLI [az keyvault storage parancsával.](/cli/azure/keyvault/storage?#az-keyvault-storage-add) Állítsa be a 90 napos újratétes időszakot. A forgás ideje alatt a KeyVault újra létrehozza a nem aktív kulcsot, majd aktívként állítja be az újonnan létrehozott kulcsot. Az SAS-jogkivonatok egyszerre csak az egyik kulcs használatával kiállítására használható, ez az aktív kulcs. Adja meg a parancsot a következő paraméterértékekkel:

- `--vault-name`: Adja meg a kulcstartó nevét. A kulcstartó nevét az Azure CLI [az keyvault list paranccsal találhatja](/cli/azure/keyvault?#az-keyvault-list) meg.
- `-n`: Adja meg a tárfiók nevét. A tárfiók nevét az Azure CLI [az storage account list paranccsal találhatja](/cli/azure/storage/account?#az-storage-account-list) meg.
- `--resource-id`: Adja meg a tárfiók erőforrás-azonosítóját, amely a következő formában van: `/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>` . Az előfizetés-azonosító megkereséhez használja az Azure CLI [az account list parancsot;](/cli/azure/account?#az-account-list) A tárfiók nevét és a tárfiók erőforráscsoportját az Azure CLI [az storage account list paranccsal találhatja](/cli/azure/storage/account?#az-storage-account-list) meg.
   
 ```azurecli-interactive
az keyvault storage add --vault-name <YourKeyVaultName> -n <YourStorageAccountName> --active-key-name key1 --auto-regenerate-key --regeneration-period P90D --resource-id "/subscriptions/<subscriptionID>/resourceGroups/<StorageAccountResourceGroupName>/providers/Microsoft.Storage/storageAccounts/<YourStorageAccountName>"
 ```

## <a name="shared-access-signature-tokens"></a>Közös hozzáférésű jogosultságkivonatok

Arra is megkérheti a Key Vault, hogy hozzon létre közös hozzáférésű jogosultsági jogkivonatokat. A közös hozzáférésű jogosultság aláírása delegált hozzáférést biztosít a tárfiók erőforrásaihoz. A fiókkulcsok megosztása nélkül adhat hozzáférést az ügyfeleknek a tárfiókban található erőforrásokhoz. A közös hozzáférésű jogosultságok lehetővé teszi a tárerőforrások biztonságos megosztását a fiókkulcsok veszélyeztetése nélkül.

Az ebben a szakaszban található parancsok a következő műveleteket ják végre:

- Állítsa be a fiók közös hozzáférésű jogosultsága `<YourSASDefinitionName>` definícióját. A definíció egy felügyelt Key Vault van beállítva a `<YourStorageAccountName>` `<YourKeyVaultName>` kulcstartóban.
- Hozzon létre egy közös hozzáférésű jogosultsági jogkivonatot a blob-, fájl-, tábla- és üzenetsorszolgáltatásokhoz. A jogkivonat a Service, Container és Object erőforrástípusokhoz jön létre. A jogkivonat létrehozása az összes engedéllyel történik https-protokollon keresztül, valamint a megadott kezdési és befejezési dátumokkal.
- Állítson be Key Vault tároló közös hozzáférésű jogosultságának definícióját a tárolóban. A definíció tartalmazza a létrehozott közös hozzáférésű jogosultságkivonat sablon URI-ját. A definíció közös hozzáférésű jogosultságkódot tartalmaz, `account` és N napig érvényes.
- Ellenőrizze, hogy a közös hozzáférésű jogosultság jogosultsága titkos kulcsként lett-e mentve a kulcstartóban.

### <a name="create-a-shared-access-signature-token"></a>Közös hozzáférésű jogosultság jogosultsági jogkivonat létrehozása

Hozzon létre egy közös hozzáférésű jogosultságjel-definíciót az Azure CLI [az storage account generate-sas paranccsal.](/cli/azure/storage/account?#az-storage-account-generate-sas) Ehhez a művelethez a `storage` és `setsas` a engedélyre van szükség.


```azurecli-interactive
az storage account generate-sas --expiry 2020-01-01 --permissions rw --resource-types sco --services bfqt --https-only --account-name <YourStorageAccountName> --account-key 00000000
```
A művelet sikeres futtatása után másolja ki a kimenetet.

```console
"se=2020-01-01&sp=***"
```

Ez a kimenet lesz átadva a `--template-uri` paraméternek a következő lépésben.

### <a name="generate-a-shared-access-signature-definition"></a>Közös hozzáférésű jogosultság jogosultsága definíciójának létrehozása

Használja az Azure CLI [az keyvault storage sas-definition create](/cli/azure/keyvault/storage/sas-definition?#az-keyvault-storage-sas-definition-create) parancsot, és az előző lépés kimenetét átadva a paraméternek egy közös hozzáférésű jogosultsága `--template-uri` definíciójának létrehozásához.  A paraméterhez meg is használhatja a választott `-n` nevet.

```azurecli-interactive
az keyvault storage sas-definition create --vault-name <YourKeyVaultName> --account-name <YourStorageAccountName> -n <YourSASDefinitionName> --validity-period P2D --sas-type account --template-uri <OutputOfSasTokenCreationStep>
```

### <a name="verify-the-shared-access-signature-definition"></a>A közös hozzáférésű jogosultság jogosultságának definíciójának ellenőrzése

Az Azure CLI [az keyvault storage sas-definition show](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) parancsával ellenőrizheti, hogy a közös hozzáférésű jogosultság jogosultságának definíciója tárolva lett-e a kulcstartóban.

Most már használhatja [az az keyvault storage sas-definition show parancsot](/cli/azure/keyvault/storage/sas-definition?#az_keyvault_storage_sas_definition_show) és a tulajdonságot a titkos kulcs `id` tartalmának megtekintéséhez.

```azurecli-interactive
az keyvault storage sas-definition show --id https://<YourKeyVaultName>.vault.azure.net/storage/<YourStorageAccountName>/sas/<YourSASDefinitionName>
```

## <a name="next-steps"></a>Következő lépések

- További információ a [kulcsokról, titkos kulcsokról és tanúsítványokról.](/rest/api/keyvault/)
- Tekintse át a Azure Key Vault [blog cikkeit.](/archive/blogs/kv/)
- Lásd az [az keyvault storage referenciadokumentációt.](/cli/azure/keyvault/storage)
