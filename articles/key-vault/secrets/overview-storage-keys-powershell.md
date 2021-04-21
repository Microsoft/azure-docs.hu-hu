---
title: Azure Key Vault tárfiók – PowerShell-verzió
description: A felügyelt tárfiók funkció zökkenőmentes integrációt biztosít a Azure Key Vault Azure Storage-fiók között.
ms.topic: tutorial
ms.service: key-vault
ms.subservice: secrets
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/10/2019
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: aa4daf7c8d951a7b42479533a3a5a50c06c5c144
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107748483"
---
# <a name="manage-storage-account-keys-with-key-vault-and-azure-powershell"></a>Tárfiókkulcsok kezelése Key Vault és Azure PowerShell
> [!IMPORTANT]
> Javasoljuk, hogy az Azure Storage-integrációt Azure Active Directory (Azure AD), a Microsoft felhőalapú identitás- és hozzáférés-kezelési szolgáltatásával. Az Azure AD-integráció az [Azure-blobok](../../storage/common/storage-auth-aad.md)és -üzenetsorok számára érhető el, és OAuth2 jogkivonat-alapú hozzáférést biztosít az Azure Storage-hoz (csakúgy, mint Azure Key Vault).
> Az Azure AD lehetővé teszi az ügyfélalkalmazás hitelesítését egy alkalmazás vagy felhasználói identitás használatával a tárfiók hitelesítő adatai helyett. Azure [AD-beli](../../active-directory/managed-identities-azure-resources/index.yml) felügyelt identitást az Azure-on való futtatáskor használhat. A felügyelt identitások miatt nincs szükség ügyfél-hitelesítésre és hitelesítő adatok tárolására az alkalmazásban vagy az alkalmazásban. Az alábbi megoldást csak akkor használja, ha az Azure AD-hitelesítés nem lehetséges.

Az Azure Storage-fiók egy fióknévből és egy kulcsból álló hitelesítő adatokat használ. A rendszer automatikusan generálja a kulcsot, és nem titkosítási kulcsként, hanem jelszóként szolgál. Key Vault úgy kezeli a tárfiókkulcsokat, hogy rendszeres időközönként újra létrehozza őket a tárfiókban, és közös hozzáférésű jogosultsági jogkivonatokat biztosít a tárfiókban található erőforrások delegált hozzáféréséhez.

A felügyelt Key Vault tárfiókkulcs funkcióval listálhatja (szinkronizálhatja) a kulcsokat egy Azure Storage-fiókkal, és rendszeres időközönként újragenerálhatja (elforgathatja) a kulcsokat. A tárfiókok és a klasszikus tárfiókok kulcsait egyaránt kezelheti.

A felügyelt tárfiókkulcs funkció használata során vegye figyelembe a következő pontokat:

- A hívók soha nem adnak vissza kulcsértékeket.
- Csak Key Vault kell kezelnie a tárfiók kulcsait. Ne saját maga kezelje a kulcsokat, és ne zavarja a Key Vault folyamatait.
- Csak egyetlen Key Vault kezelheti a tárfiók kulcsait. Ne engedélyezze a kulcskezelést több objektumból.
- Kulcsok újragenerálása csak Key Vault használatával. Ne manuálisan újragenerálja a tárfiók kulcsait.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="service-principal-application-id"></a>Alkalmazáspéldány – Alkalmazásazonosító

Az Azure AD-bérlő minden regisztrált alkalmazást biztosít egy [szolgáltatásnévvel.](../../active-directory/develop/developer-glossary.md#service-principal-object) A szolgáltatásnév szolgál alkalmazásazonosítóként, amelyet a rendszer az engedélyezés beállítása során használ más Azure-erőforrások Azure RBAC-n keresztüli eléréséhez.

Key Vault egy Olyan Microsoft-alkalmazás, amely minden Azure AD-bérlőben előre regisztrálva van. Key Vault minden Azure-felhőben ugyanaz az alkalmazásazonosító van regisztrálva.

| Bérlők | Felhőbeli | Alkalmazásazonosító |
| --- | --- | --- |
| Azure AD | Azure Government | `7e7c393b-45d0-48b1-a35e-2905ddf8183c` |
| Azure AD | Azure – nyilvános | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |
| Egyéb  | Bármelyik | `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` |

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez először a következőket kell végrehajtania:

- [Telepítse a Azure PowerShell modult.](/powershell/azure/install-az-ps)
- [Kulcstartó létrehozása](quick-create-powershell.md)
- [Hozzon létre egy Azure Storage-fiókot.](../../storage/common/storage-account-create.md?tabs=azure-powershell) A tárfiók neve csak kisbetűket és számokat használhat. A név hossza 3–24 karakter hosszúságú lehet.


## <a name="manage-storage-account-keys"></a>Tárfiókkulcsok kezelése

### <a name="connect-to-your-azure-account"></a>Csatlakozás az Azure-fiókhoz

Hitelesítse a PowerShell-munkamenetet a [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) parancsmaggal.

```azurepowershell-interactive
Connect-AzAccount
```
Ha több Azure-előfizetéssel rendelkezik, a [Get-AzSubscription](/powershell/module/az.accounts/get-azsubscription) parancsmaggal listálhatja őket, és megadhatja a [Set-AzContext](/powershell/module/az.accounts/set-azcontext) parancsmaggal használni kívánt előfizetést.

```azurepowershell-interactive
Set-AzContext -SubscriptionId <subscriptionId>
```

### <a name="set-variables"></a>Változók beállítása

Először állítsa be a PowerShell-parancsmagok által használt változókat a következő lépésekben. Frissítse a "YourResourceGroupName", "YourStorageAccountName" és "YourKeyVaultName" helyőrzőket, és állítsa a $keyVaultSpAppId értéket a következőre (a fenti egyszerű szolgáltatásalkalmazás-azonosítóban `cfa8b339-82a2-471a-a3c9-0fc0be7a4093` megadottak szerint). [](#service-principal-application-id)

A [get-azcontext](/powershell/module/az.accounts/get-azcontext) Azure PowerShell [Get-AzStorageAccount](/powershell/module/az.storage/get-azstorageaccount) parancsmagokkal lekért felhasználói azonosítót és az Azure Storage-fiók környezetét is le fogjuk használni.

```azurepowershell-interactive
$resourceGroupName = <YourResourceGroupName>
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>
$keyVaultSpAppId = "cfa8b339-82a2-471a-a3c9-0fc0be7a4093"
$storageAccountKey = "key1" #(key1 or key2 are allowed)

# Get your User Id
$userId = (Get-AzContext).Account.Id

# Get a reference to your Azure storage account
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName

```
>[!Note]
> A klasszikus tárfiókok "elsődleges" és "másodlagos" $storageAccountKey <br>
> A Get-AzResource -Name "ClassicStorageAccountName" -ResourceGroupName $resourceGroupName" használata a Get-AzStorageAccount helyett klasszikus tárfiók esetén

### <a name="give-key-vault-access-to-your-storage-account"></a>Hozzáférés Key Vault a tárfiókhoz

Mielőtt Key Vault tárfiókkulcsait, engedélyeznie kell annak hozzáférését a tárfiókhoz. A Key Vault alkalmazásnak engedélyekkel  kell  rendelkeznie a tárfiók kulcsának listához és újragenerál használatához. Ezek az engedélyek az Azure beépített tárfiókkulcs-kezelői szolgáltatási [szerepkörével engedélyezhetők.](../../role-based-access-control/built-in-roles.md#storage-account-key-operator-service-role)

Rendelje hozzá ezt a Key Vault szolgáltatásnévhez, korlátozva a tárfiók hatókörét a Azure PowerShell [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment) parancsmag használatával.

```azurepowershell-interactive
# Assign Azure role "Storage Account Key Operator Service Role" to Key Vault, limiting the access scope to your storage account. For a classic storage account, use "Classic Storage Account Key Operator Service Role."
New-AzRoleAssignment -ApplicationId $keyVaultSpAppId -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storageAccount.Id
```

Sikeres szerepkör-hozzárendelés esetén az alábbi példához hasonló kimenetnek kell lennie:

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : storage account Key Operator Service Role
RoleDefinitionId   : 81a9662b-bebf-436f-a333-f67b29880f12
ObjectId           : 93c27d83-f79b-4cb2-8dd4-4aa716542e74
ObjectType         : ServicePrincipal
CanDelegate        : False
```

Ha Key Vault már hozzá lett adva a tárfiók szerepkörhöz, "A szerepkör-hozzárendelés *már létezik" üzenetet kapja.* hibát ad vissza. A szerepkör-hozzárendelést a tárfiók "Hozzáférés-vezérlés (IAM)" lapján is ellenőrizheti a Azure Portal.

### <a name="give-your-user-account-permission-to-managed-storage-accounts"></a>Felhasználói fiók engedélyének adjuk a felügyelt tárfiókok számára

A Azure PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) parancsmaggal frissítheti a Key Vault hozzáférési szabályzatot, és tárfiók-engedélyeket adhat a felhasználói fióknak.

```azurepowershell-interactive
# Give your user principal access to all storage account permissions, on your Key Vault instance

Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -UserPrincipalName $userId -PermissionsToStorage get, list, delete, set, update, regeneratekey, getsas, listsas, deletesas, setsas, recover, backup, restore, purge
```

Vegye figyelembe, hogy a tárfiókok engedélyei nem érhetők el a tárfiók "Hozzáférési szabályzatok" lapján a Azure Portal.

### <a name="add-a-managed-storage-account-to-your-key-vault-instance"></a>Felügyelt tárfiók hozzáadása a Key Vault példányhoz

Az Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) parancsmaggal hozzon létre egy felügyelt tárfiókot a Key Vault példányában. A  `-DisableAutoRegenerateKey` kapcsoló nem adja meg a TÁRfiók kulcsának újragenerálását.

```azurepowershell-interactive
# Add your storage account to your Key Vault's managed storage accounts

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -DisableAutoRegenerateKey
```

A tárfiók sikeres, kulcsgenerálás nélkül való összeadása után az alábbi példához hasonló kimenetnek kell lennie:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : False
Regeneration Period : 90.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

### <a name="enable-key-regeneration"></a>Kulcs-újragenerálás engedélyezése

Ha azt szeretné Key Vault rendszeresen újra létrehozza a tárfiók kulcsait, az Azure PowerShell [Add-AzKeyVaultManagedStorageAccount](/powershell/module/az.keyvault/add-azkeyvaultmanagedstorageaccount) parancsmaggal állíthat be egy új időszakot. Ebben a példában három napos újrarekedő időszakot mutatunk be. A forgás ideje alatt a Key Vault újra létrehozza a nem aktív kulcsot, majd aktívként állítja be az újonnan létrehozott kulcsot. Az SAS-jogkivonatok egyszerre csak az egyik kulcs használatával kiállítására használhatók. Ez az aktív kulcs.

```azurepowershell-interactive
$regenPeriod = [System.Timespan]::FromDays(3)

Add-AzKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storageAccountName -AccountResourceId $storageAccount.Id -ActiveKeyName $storageAccountKey -RegenerationPeriod $regenPeriod
```

A tárfiók kulcs-újragenerálással való sikeres létrehozása után az alábbi példához hasonló kimenetnek kell látszanunk:

```console
Id                  : https://kvcontoso.vault.azure.net:443/storage/sacontoso
Vault Name          : kvcontoso
AccountName         : sacontoso
Account Resource Id : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgContoso/providers/Microsoft.Storage/storageAccounts/sacontoso
Active Key Name     : key1
Auto Regenerate Key : True
Regeneration Period : 3.00:00:00
Enabled             : True
Created             : 11/19/2018 11:54:47 PM
Updated             : 11/19/2018 11:54:47 PM
Tags                :
```

## <a name="shared-access-signature-tokens"></a>Közös hozzáférésű jogosultságkivonatok

Arra is megkérheti a Key Vault, hogy hozzon létre közös hozzáférésű jogosultsági jogkivonatokat. A közös hozzáférésű jogosultság aláírása delegált hozzáférést biztosít a tárfiók erőforrásaihoz. A fiókkulcsok megosztása nélkül adhat hozzáférést az ügyfeleknek a tárfiókban található erőforrásokhoz. A közös hozzáférésű jogosultságok lehetővé teszi a tárerőforrások biztonságos megosztását a fiókkulcsok veszélyeztetése nélkül.

Az ebben a szakaszban található parancsok a következő műveleteket ják végre:

- Fiók közös hozzáférésű jogosultsága definíciójának beállítása.
- Hozzon létre egy közös hozzáférésű jogosultságkivonatot a blob-, fájl-, tábla- és üzenetsorszolgáltatásokhoz. A token a Service, Container és Object erőforrástípusokhoz jön létre. A jogkivonat létrehozása minden engedély esetén https protokollon keresztül, valamint a megadott kezdési és befejezési dátumokkal történik.
- Állítson Key Vault tároló közös hozzáférésű jogosultságának definícióját a tárolóban. A definíció tartalmazza a létrehozott közös hozzáférésű jogosultságkivonat sablon URI-ját. A definíció közös hozzáférésű jogosultságkódot tartalmaz, `account` és N napig érvényes.
- Ellenőrizze, hogy a közös hozzáférésű jogosultság jogosultsága titkos kulcsként lett-e mentve a kulcstartóban.
-
### <a name="set-variables"></a>Változók beállítása

Először állítsa be a PowerShell-parancsmagok által használt változókat a következő lépésekben. Ne feledjük a és a <YourStorageAccountName> <YourKeyVaultName> helyőrzők frissítését.

A [New-AzStorageContext](/powershell/module/az.storage/new-azstoragecontext) parancsmagok Azure PowerShell is használni fogjuk az Azure Storage-fiók környezetének leését.

```azurepowershell-interactive
$storageAccountName = <YourStorageAccountName>
$keyVaultName = <YourKeyVaultName>

$storageContext = New-AzStorageContext -StorageAccountName $storageAccountName -Protocol Https -StorageAccountKey Key1 #(or "Primary" for Classic Storage Account)
```

### <a name="create-a-shared-access-signature-token"></a>Közös hozzáférésű jogosultság jogosultsági jogkivonat létrehozása

Hozzon létre egy közös hozzáférésű jogosultságfeloldó definíciót Azure PowerShell [New-AzStorageAccountSASToken](/powershell/module/az.storage/new-azstorageaccountsastoken) parancsmagok használatával.

```azurepowershell-interactive
$start = [System.DateTime]::Now.AddDays(-1)
$end = [System.DateTime]::Now.AddMonths(1)

$sasToken = New-AzStorageAccountSasToken -Service blob,file,Table,Queue -ResourceType Service,Container,Object -Permission "racwdlup" -Protocol HttpsOnly -StartTime $start -ExpiryTime $end -Context $storageContext
```
A $sasToken ehhez hasonlóan fog kinézni.

```console
?sv=2018-11-09&sig=5GWqHFkEOtM7W9alOgoXSCOJO%2B55qJr4J7tHQjCId9S%3D&spr=https&st=2019-09-18T18%3A25%3A00Z&se=2019-10-19T18%3A25%3A00Z&srt=sco&ss=bfqt&sp=racupwdl
```

### <a name="generate-a-shared-access-signature-definition"></a>Közös hozzáférésű jogosultság jogosultsága definíciójának létrehozása

Közös hozzáférésű jogosultság Azure PowerShell a [Set-AzKeyVaultManagedStorageSasDefinition](/powershell/module/az.keyvault/set-azkeyvaultmanagedstoragesasdefinition) parancsmag használatával.  A paraméterhez meg lehet adni a választott `-Name` nevet.

```azurepowershell-interactive
Set-AzKeyVaultManagedStorageSasDefinition -AccountName $storageAccountName -VaultName $keyVaultName -Name <YourSASDefinitionName> -TemplateUri $sasToken -SasType 'account' -ValidityPeriod ([System.Timespan]::FromDays(30))
```

### <a name="verify-the-shared-access-signature-definition"></a>A közös hozzáférésű jogosultság jogosultsága definíciójának ellenőrzése

A [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) parancsmag használatával ellenőrizheti, hogy a közös hozzáférésű jogosultság jogosultságának definíciója el lett-e Azure PowerShell a kulcstartóban.

Először keresse meg a közös hozzáférésű jogosultság jogosultságának definícióját a kulcstartóban.

```azurepowershell-interactive
Get-AzKeyVaultSecret -VaultName <YourKeyVaultName>
```

Az SAS-definíciónak megfelelő titkos tulajdonságokkal rendelkezik:

```console
Vault Name   : <YourKeyVaultName>
Name         : <SecretName>
...
Content Type : application/vnd.ms-sastoken-storage
Tags         :
```

Most már használhatja a [Get-AzKeyVaultSecret](/powershell/module/az.keyvault/get-azkeyvaultsecret) parancsmagot a és paraméterekkel a titkos kulcs `VaultName` `Name` tartalmának megtekintéséhez.

```azurepowershell-interactive
$secret = Get-AzKeyVaultSecret -VaultName <YourKeyVaultName> -Name <SecretName>
$ssPtr = [System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($secret.SecretValue)
try {
   $secretValueText = [System.Runtime.InteropServices.Marshal]::PtrToStringBSTR($ssPtr)
} finally {
   [System.Runtime.InteropServices.Marshal]::ZeroFreeBSTR($ssPtr)
}
Write-Output $secretValueText
```

A parancs kimenete az SAS-definíciós sztringet fogja mutatni.

## <a name="next-steps"></a>Következő lépések

- [Felügyelt tárfiók kulcsminái](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=)
- [Key Vault PowerShell-referencia](/powershell/module/az.keyvault/#key_vault)
