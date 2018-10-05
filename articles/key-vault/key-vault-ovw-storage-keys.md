---
ms.assetid: ''
title: Az Azure Key Vault-Tárfiókkulcsok
description: A tárfiókkulcsok egy Azure Key Vault és alapuló hozzáférés a kulcshoz seemless integrációjával nyújtson az Azure Storage-fiókot.
ms.topic: conceptual
services: key-vault
ms.service: key-vault
author: bryanla
ms.author: bryanla
manager: mbaldwin
ms.date: 10/03/2018
ms.openlocfilehash: 38717fed9f3877dfd0aa9819571ef0f32befc117
ms.sourcegitcommit: 4edf9354a00bb63082c3b844b979165b64f46286
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48785511"
---
# <a name="azure-key-vault-storage-account-keys"></a>Az Azure Key Vault-tárfiókkulcsok

Az Azure Key Vault tárfiókkulcsokat, mielőtt fejlesztők kellett a saját Azure Storage-fiók (ASA) kulcsok kezelése, és manuálisan vagy egy külső automation elforgatása őket. Key Vault Tárfiókkulcsokat megvalósított most [Key Vault titkos kódok](https://docs.microsoft.com/rest/api/keyvault/about-keys--secrets-and-certificates#BKMK_WorkingWithSecrets) hitelesítéséhez az Azure Storage-fiókban.

Az Azure Storage-fiók (ASA) fontos szolgáltatása, titkos Elforgatás kezeli. Azt is kiküszöböli az közvetlen kapcsolatba az ASA-kulccsal, azzal közös hozzáférésű Jogosultságkódok (SAS) módszerként.

Az Azure Storage-fiókokról további általános információkért lásd: [tudnivalók az Azure storage-fiókok](https://docs.microsoft.com/azure/storage/storage-create-storage-account).

## <a name="supporting-interfaces"></a>Adapterek támogatása

Teljes listája és a programozási és parancsfájl-kezelési felület mutató hivatkozásokat találhat az [Key Vault fejlesztői útmutató](key-vault-developers-guide.md#coding-with-key-vault).


## <a name="what-key-vault-manages"></a>A Key Vault kezeli

Felügyelt Tárfiókkulcsok használata esetén a Key Vault számos belső felügyeleti funkciót az Ön nevében hajt végre.

- Az Azure Key Vault kezeli a kulcsok az Azure Storage fiók (ASA).
    - Belsőleg az Azure Key Vault listázhatja az Azure Storage-fiók kulcsok (sync).
    - Az Azure Key Vault újragenerálása (rotálja) a kulcsokat rendszeres időközönként.
    - Kulcs értékeit a rendszer soha nem adja vissza a hívó adott válaszként.
    - Az Azure Key Vault kezeli a Storage-fiókok és a klasszikus Tárfiókok kulcsait.
- Az Azure Key Vault lehetővé teszi, a tár/objektum tulajdonosa, SAS (közös hozzáférésű Jogosultságkód, fiók vagy szolgáltatás SAS) definíciók létrehozásához.
    - SAS-definíciója használatával létrehozott SAS-értéke egy titkos kulcsot, adja vissza a REST-URI elérési útján. További információkért lásd: az SAS-definíciója műveletek a [Azure Key Vault REST API-referencia](/rest/api/keyvault).

## <a name="naming-guidance"></a>Elnevezési irányelvei

- A tárfiókok neve 3–24 karakter hosszúságú lehet, és csak számokból és kisbetűkből állhat.
- Egy SAS-definíció neve 1 – 102 karakter hosszúságú csak 0-9, a – z, A – Z kell lennie.

## <a name="developer-experience"></a>Fejlesztői élmény

### <a name="before-azure-key-vault-storage-keys"></a>Mielőtt az Azure Key Vault-Tárfiókkulcsok

A fejlesztők a tárfiókkulcs érheti el az alábbi eljárásokat kell használni az Azure storage eléréséhez.
1. Az Azure App Service-Alkalmazásbeállítások és a egy másik tárolási Store a kapcsolati karakterlánc vagy SAS-jogkivonatát.
1. Alkalmazás induláskor beolvassa a kapcsolati karakterlánc vagy SAS-jogkivonatát.
1. Hozzon létre [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) tárolási kommunikál.

```cs
// The Connection string is being fetched from App Service application settings
var connectionStringOrSasToken = CloudConfigurationManager.GetSetting("StorageConnectionString");
var storageAccount = CloudStorageAccount.Parse(connectionStringOrSasToken);
var blobClient = storageAccount.CreateCloudBlobClient();
 ```

### <a name="after-azure-key-vault-storage-keys"></a>Miután az Azure Key Vault-Tárfiókkulcsok

A fejlesztők hozzon létre egy [KeyVaultClient](https://docs.microsoft.com/dotnet/api/microsoft.azure.keyvault.keyvaultclient) és használja ki, hogy azok a SAS-jogkivonat lekérése. Ezt követően létre [CloudStorageAccount](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.cloudstorageaccount) adott tokenhez.

```cs
// Create KeyVaultClient with vault credentials
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(securityToken));

// Get a SAS token for our storage from Key Vault
var sasToken = await kv.GetSecretAsync("SecretUri");

// Create new storage credentials using the SAS token.
var accountSasCredential = new StorageCredentials(sasToken.Value);

// Use the storage credentials and the Blob storage endpoint to create a new Blob service client.
var accountWithSas = new CloudStorageAccount(accountSasCredential, new Uri ("https://myaccount.blob.core.windows.net/"), null, null, null);

var blobClientWithSas = accountWithSas.CreateCloudBlobClient();

// Use the blobClientWithSas
...

// If your SAS token is about to expire, get the SAS Token again from Key Vault and update it.
sasToken = await kv.GetSecretAsync("SecretUri");
accountSasCredential.UpdateSASToken(sasToken);
```

 ### <a name="developer-guidance"></a>Fejlesztői útmutatás

- Csak engedélyezze a Key Vault az ASA kulcsok kezeléséhez. Ne kísérelje meg saját maga kezelheti őket, a Key Vault folyamatok zavarja meg.
- Több Key Vault-objektum által felügyelendő ASA kulcsok nem teszik lehetővé.
- Ha manuálisan a az ASA-kulcsok újragenerálása van szüksége, azt javasoljuk, Key Vault-n keresztül újragenerálása.

## <a name="authorize-key-vault-to-access-to-your-storage-account"></a>A tárfiók eléréséhez a Key Vault engedélyezése

A Key Vault eléréséhez és a kezelése a tárfiók kulcsait, mielőtt engedélyeznie kell a hozzáférést a tárfiókhoz.  Számos alkalmazás, például a Key Vault integrálható az Azure AD identitás- és hozzáférés-kezelő szolgáltatásokra. 

Mivel a Key Vault egy Microsoft-alkalmazásba, előzetesen regisztrálva van az alkalmazás azonosítója alapján az összes Azure AD-bérlőt `cfa8b339-82a2-471a-a3c9-0fc0be7a4093`. Hasonlóan az Azure AD-ben regisztrált összes alkalmazás egy [szolgáltatásnév](/azure/active-directory/develop/app-objects-and-service-principals) objektum tartalmazza az alkalmazás identitását tulajdonságainál. Egyszerű szolgáltatás majd kaphatnak egy másik erőforrás hozzáférési szerepköralapú hozzáférés-vezérlés (RBAC) révén.  

Az Azure Key Vault alkalmazás engedélyekre van szüksége *lista* és *újragenerálása* kulcsokat a tárfiók számára. Ezeket az engedélyeket a beépített [tárolási fiók kulcs üzemeltető szolgáltatás](/azure/role-based-access-control/built-in-roles#storage-account-key-operator-service-role) RBAC szerepkör. A Key Vault egyszerű szolgáltatás hozzárendelése ehhez a szerepkörhöz, az alábbi lépéseket követve:

```powershell
# Get the resource ID of the Azure Storage Account you want Key Vault to manage
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Assign Storage Key Operator role to Azure Key Vault Identity
New-AzureRmRoleAssignment -ApplicationId “cfa8b339-82a2-471a-a3c9-0fc0be7a4093” -RoleDefinitionName 'Storage Account Key Operator Service Role' -Scope $storage.Id
```

> [!NOTE]
> Klasszikus fiók írja be a következőt, állítsa a szerepkör paramétert *"Klasszikus tárolási fiók kulcs operátora – szolgáltatási szerepkör."*

A sikeres szerepkör hozzárendelése után kell megjelennie a következőhöz hasonló kimenetet

```console
RoleAssignmentId   : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgSandbox/providers/Microsoft.Storage/storageAccounts/sabltest/providers/Microsoft.Authorization/roleAssignments/189cblll-12fb-406e-8699-4eef8b2b9ecz
Scope              : /subscriptions/03f0blll-ce69-483a-a092-d06ea46dfb8z/resourceGroups/rgSandbox/providers/Microsoft.Storage/storageAccounts/sabltest
DisplayName        : Azure Key Vault
SignInName         :
RoleDefinitionName : Storage Account Key Operator Service Role
RoleDefinitionId   : 81a9blll-bebf-436f-a333-f67b29880f1z
ObjectId           : c730c8da-blll-4032-8ad5-945e9dc8262z
ObjectType         : ServicePrincipal
CanDelegate        : False
```

## <a name="working-example"></a>Működő példát

A következő példa bemutatja, hogy a Key Vault létrehozása a felügyelt Azure Storage-fiókot és a társított SAS-definícióit.

### <a name="prerequisite"></a>Előfeltétel

Mielőtt elkezdené, győződjön meg arról, hogy [engedélyezze a Key Vault eléréséhez a tárfiókhoz](#authorize-key-vault-to-access-to-your-storage-account).

### <a name="setup"></a>Beállítás

```powershell
# This is the name of our Key Vault
$keyVaultName = "mykeyVault"

# Fetching all the storage account object, of the ASA we want to manage with KeyVault
$storage = Get-AzureRmStorageAccount -ResourceGroupName "mystorageResourceGroup" -StorageAccountName "mystorage"

# Get ObjectId of Azure KeyVault Identity service principal
$servicePrincipalId = $(Get-AzureRmADServicePrincipal -ServicePrincipalName cfa8b339-82a2-471a-a3c9-0fc0be7a4093).Id
```

Következő lépésként állítsa be a **fiókja** annak érdekében, hogy a Key Vault a tárolási engedélyek segítségével kezelheti. Az alábbi példában az Azure-fiók van _developer@contoso.com_.

```powershell
# Searching our Azure Active Directory for our account's ObjectId
$userPrincipalId = $(Get-AzureRmADUser -SearchString "developer@contoso.com").Id

# We use the ObjectId we found to setting permissions on the vault
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ObjectId $userPrincipalId -PermissionsToStorage all
```

### <a name="create-a-key-vault-managed-storage-account"></a>Key Vault létrehozása felügyelt Tárfiók

Most hozzon létre egy felügyelt Tárfiókot az Azure Key Vaultban, és használja a hozzáférési kulcs az Ön tárfiókjából az SAS-jogkivonatok létrehozásához.
- `-ActiveKeyName` használja a "2. kulcs: az SAS-jogkivonatokat hoz létre.
- `-AccountName` a felügyelt tárfiók azonosítására szolgál. Az alábbi egyszerű biztosítható, hogy a tárfiók nevét használjuk, de bármely név lehet.
- `-DisableAutoRegenerateKey` Adja meg a tárfiók kulcsaihoz generálja újra.

```powershell
# Adds your storage account to be managed by Key Vault and will use the access key, key2
Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $storage.StorageAccountName -AccountResourceId $storage.Id -ActiveKeyName key2 -DisableAutoRegenerateKey
```

### <a name="key-regeneration"></a>Kulcs újragenerálása

Ha azt szeretné, hogy a Key Vault rendszeresen újragenerálja a tárfiókkulcsok, beállíthat egy regenerációs időszakot. Az alábbi azt állítja a regenerációs időszakot 3 nap. 3 nap után a Key Vault "1. kulcs" újragenerálása és cseréje: key1"a"2. kulcs: az aktív kulcs.

```powershell
$regenPeriod = [System.Timespan]::FromDays(3)
$accountName = $storage.StorageAccountName

Add-AzureKeyVaultManagedStorageAccount -VaultName $keyVaultName -AccountName $accountName -AccountResourceId $storage.Id -ActiveKeyName key2 -RegenerationPeriod $regenPeriod
```

### <a name="set-sas-definitions"></a>SAS definíciók

A fiók SAS a blob service, különböző engedélyekkel rendelkező hozzáférést biztosít.
Állítsa be a SAS-definíciókat a Key Vaultban a felügyelt tárfiók.
- `-AccountName` a Key vaultban felügyelt tárfiók neve van.
- `-Name` a SAS-jogkivonatnak a storage-ban az azonosítója.
- `-ValidityPeriod` Beállítja a generált SAS-jogkivonat lejárati dátuma.

```powershell
$validityPeriod = [System.Timespan]::FromDays(1)
$readSasName = "readBlobSas"
$writeSasName = "writeBlobSas"

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service -VaultName $keyVaultName -AccountName $accountName -Name $readSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List

Set-AzureKeyVaultManagedStorageSasDefinition -Service Blob -ResourceType Container,Service,Object -VaultName $keyVaultName -AccountName $accountName -Name $writeSasName -Protocol HttpsOnly -ValidityPeriod $validityPeriod -Permission Read,List,Write
```

### <a name="get-sas-tokens"></a>SAS-tokenek beszerzése

A megfelelő SAS-tokeneket és a Storage-hívások. `-SecretName` a bemeneti segítségével jön létre a `AccountName` és `Name` végrehajtásakor, paraméterek [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition).

```powershell
$readSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$readSasName").SecretValueText
$writeSasToken = (Get-AzureKeyVaultSecret -VaultName $keyVaultName -SecretName "$accountName-$writeSasName").SecretValueText
```

### <a name="create-storage"></a>Tároló létrehozása

Figyelje meg, hogy az elérni próbált *$readSasToken* sikertelen lesz, azonban, hogy sikerült eléréséhez *$writeSasToken*.

```powershell
$context1 = New-AzureStorageContext -SasToken $readSasToken -StorageAccountName $storage.StorageAccountName
$context2 = New-AzureStorageContext -SasToken $writeSasToken -StorageAccountName $storage.StorageAccountName

# Ensure the txt file in command exists in local path mentioned
Set-AzureStorageBlobContent -Container containertest1 -File "./abc.txt" -Context $context1
Set-AzureStorageBlobContent -Container cont1-file "./file.txt" -Context $context2
```

Ön tudja hozzáférés a tároló blobtartalmát azzal az SAS-token, amely írási hozzáférése van.

### <a name="relevant-powershell-cmdlets"></a>Kapcsolódó Powershell-parancsmagok

- [Get-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/get-azurekeyvaultmanagedstorageaccount)
- [Add-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Add-AzureKeyVaultManagedStorageAccount)
- [Get-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Get-AzureKeyVaultManagedStorageSasDefinition)
- [Update-AzureKeyVaultManagedStorageAccountKey](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Update-AzureKeyVaultManagedStorageAccountKey)
- [Remove-AzureKeyVaultManagedStorageAccount](https://docs.microsoft.com/powershell/module/azurerm.keyvault/remove-azurekeyvaultmanagedstorageaccount)
- [Remove-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Remove-AzureKeyVaultManagedStorageSasDefinition)
- [Set-AzureKeyVaultManagedStorageSasDefinition](https://docs.microsoft.com/powershell/module/AzureRM.KeyVault/Set-AzureKeyVaultManagedStorageSasDefinition)

## <a name="storage-account-onboarding"></a>Storage-fiók bevezetése

Példa: tulajdonosként egy Key Vault objektumot ad hozzá egy tárolóobjektum fiók az Azure Key Vaultban való felvétele egy storage-fiókot.

Az előkészítés, során a Key Vault ellenőrizze, hogy az identitás az előkészítési fiók rendelkezik-e engedélyekkel kell *lista* , az *újragenerálása* tárkulcsok. Annak érdekében, hogy ezek az engedélyek ellenőrzéséhez a Key Vault olvas be egy OBO (a nevében-) token a hitelesítési szolgáltatást, állítsa be az Azure Resource Manager célközönség, és lehetővé teszi egy *lista* kulcs az Azure Storage szolgáltatás hívása. Ha a *lista* hívás sikertelen, az objektum-létrehozás sikertelen, és a egy HTTP-állapotkód: a Key Vault *tiltott*. Ilyen módon szereplő kulcsok a key vault-entitás tárolóval lettek gyorsítótárazva.

A Key Vault ellenőriznie kell, hogy rendelkezik-e az identitás *újragenerálása* engedélyek előtt is igénybe vehet a kulcsok újragenerálása tulajdonjogát. Annak ellenőrzése, hogy az identitás OBO jogkivonatot, valamint a Key Vault első fél azonosságát keresztül rendelkezik ezekkel az engedélyekkel:

- A Key Vault a tárfiók típusú erőforrást az RBAC-engedélyek listája.
- A Key Vault ellenőrzi a válasz reguláris kifejezéssel egyező műveleteket és a nem műveletek keresztül.

Jelenleg támogató példákat talál [Key Vault – felügyelt Storage-fiók kulcsok minták](https://github.com/Azure-Samples?utf8=%E2%9C%93&q=key+vault+storage&type=&language=).

Ha nem rendelkezik az identitás *újragenerálása* engedélyeket, vagy ha nincs a Key Vault első fél azonosságát *lista* vagy *újragenerálása* engedéllyel, akkor a Bevezetés kérelem meghiúsul, egy megfelelő hibakód, üzenet visszaadása.

A OBO token csak akkor fog működni, belső, natív ügyfélalkalmazások vagy a PowerShell vagy a parancssori felület használata esetén.

## <a name="other-applications"></a>Más alkalmazások

- SAS-jogkivonatok segítségével a Key Vault tárfiókkulcsokat, jön létre egy Azure storage-fiókot még nagyobb szabályozott hozzáférést biztosíthat. További információkért lásd: [a közös hozzáférésű jogosultságkódot](https://docs.microsoft.com/azure/storage/storage-dotnet-shared-access-signature-part-1).

## <a name="see-also"></a>Lásd még

- [A kulcsok, titkos kódok és tanúsítványok ismertetése](https://docs.microsoft.com/rest/api/keyvault/)
- [A Key Vault-csapat blogja](https://blogs.technet.microsoft.com/kv/)
