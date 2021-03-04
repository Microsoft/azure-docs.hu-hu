---
title: Oktatóanyag – Blobok titkosítása és visszafejtése Azure Key Vault használatával
titleSuffix: Azure Storage
description: Megtudhatja, hogyan titkosíthatja és fejtheti vissza a blobokat az ügyféloldali titkosítással Azure Key Vault használatával.
services: storage
author: tamram
ms.service: storage
ms.topic: tutorial
ms.date: 02/18/2021
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: blobs
ms.custom: devx-track-csharp
ms.openlocfilehash: c2daed4a8df89ed176749900dc75eb231c00af87
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/04/2021
ms.locfileid: "102049270"
---
# <a name="tutorial---encrypt-and-decrypt-blobs-using-azure-key-vault"></a>Oktatóanyag – Blobok titkosítása és visszafejtése Azure Key Vault használatával

Ez az oktatóanyag azt ismerteti, hogyan használható az ügyféloldali tárolás titkosítása Azure Key Vault használatával. Bemutatja, hogyan titkosíthatja és fejtheti vissza a blobokat a konzolos alkalmazásokban ezekkel a technológiákkal.

**Az oktatóanyag áttekintésének várható időtartama:** 20 perc

További információ a Azure Key Vaultről: [Mi az Azure Key Vault?](../../key-vault/general/overview.md).

Az Azure Storage ügyféloldali titkosításának áttekintését lásd: [ügyféloldali titkosítás és Azure Key Vault Microsoft Azure Storagehoz](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag teljesítéséhez a következőkre lesz szüksége:

* Azure Storage-fiók
* Visual Studio 2013 vagy újabb
* Azure PowerShell

## <a name="overview-of-client-side-encryption"></a>Ügyféloldali titkosítás áttekintése

Az Azure Storage ügyféloldali titkosításának áttekintését lásd: [ügyféloldali titkosítás és Azure Key Vault a Microsoft Azure Storage](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)

Íme egy rövid leírás arról, hogyan működik az ügyféloldali titkosítás:

1. Az Azure Storage ügyféloldali SDK létrehoz egy Content encryption Key (CEK) kulcsot, amely egy egyszer használatos szimmetrikus kulcs.
2. A vásárlói adatai titkosítva vannak a CEK használatával.
3. Ezután a CEK (titkosított) a kulcs titkosítási kulcs (KEK) használatával burkolta. A KEK-et egy kulcs-azonosító azonosítja, és lehet egy aszimmetrikus kulcspár vagy egy szimmetrikus kulcs, és kezelhető helyileg, vagy Azure Key Vault tárolható. Maga a Storage-ügyfél soha nem fér hozzá a KEK-hez. Csak a Key Vault által biztosított kulcs-körbefuttatási algoritmust hívja meg. Az ügyfelek dönthetnek úgy, hogy egyéni szolgáltatókat használnak a kulcsok becsomagolásához/kicsomagolásához, ha szeretnék.
4. Ezt követően a rendszer feltölti a titkosított fájlokat az Azure Storage szolgáltatásba.

## <a name="set-up-your-azure-key-vault"></a>A Azure Key Vault beállítása

Ennek az oktatóanyagnak a folytatásához a következő lépéseket kell végrehajtania, amelyek az oktatóanyag rövid útmutatójában [olvashatók: a titkos kód beállítása és lekérése Azure Key Vault egy .net-webalkalmazás használatával](../../key-vault/secrets/quick-create-net.md):

* Kulcstartó létrehozása.
* Adjon hozzá egy kulcsot vagy titkos kulcsot a kulcstartóhoz.
* Alkalmazás regisztrálása a Azure Active Directory.
* Engedélyezze az alkalmazásnak a kulcs vagy a titkos kód használatát.

Jegyezze fel azokat a ClientID és ClientSecret, amelyek az alkalmazások Azure Active Directory-vel való regisztrálása során jöttek létre.

Hozzon létre mindkét kulcsot a Key vaultban. Feltételezzük, hogy az oktatóanyag további részében a következő neveket használta: ContosoKeyVault és TestRSAKey1.

## <a name="create-a-console-application-with-packages-and-appsettings"></a>Konzolos alkalmazás létrehozása csomagokkal és AppSettings

Hozzon létre egy új Console-alkalmazást a Visual Studióban.

Adja hozzá a szükséges nuget-csomagokat a Package Manager konzolon.

```powershell
Install-Package Microsoft.Azure.ConfigurationManager
Install-Package Microsoft.Azure.Storage.Common
Install-Package Microsoft.Azure.Storage.Blob
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

Install-Package Microsoft.Azure.KeyVault
Install-Package Microsoft.Azure.KeyVault.Extensions
```

Adja hozzá a AppSettings a App.Confighoz.

```xml
<appSettings>
    <add key="accountName" value="myaccount"/>
    <add key="accountKey" value="theaccountkey"/>
    <add key="clientId" value="theclientid"/>
    <add key="clientSecret" value="theclientsecret"/>
    <add key="container" value="stuff"/>
</appSettings>
```

Adja hozzá a következő `using` irányelveket, és ügyeljen arra, hogy adjon hozzá egy hivatkozást System.Configszülő a projekthez.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Configuration;
using Microsoft.Azure;
using Microsoft.Azure.Storage;
using Microsoft.Azure.Storage.Auth;
using Microsoft.Azure.Storage.Blob;
using Microsoft.Azure.KeyVault;
using System.Threading;
using System.IO;
```
---

## <a name="add-a-method-to-get-a-token-to-your-console-application"></a>Metódus hozzáadása a konzol alkalmazáshoz való token beszerzéséhez

A következő módszert olyan Key Vault osztályok használják, amelyeknek hitelesítést kell végezniük a kulcstartóhoz való hozzáféréshez.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
private async static Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(
        CloudConfigurationManager.GetSetting("clientId"),
        CloudConfigurationManager.GetSetting("clientSecret"));
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
```
---

## <a name="access-azure-storage-and-key-vault-in-your-program"></a>Az Azure Storage és a Key Vault elérése a programban

A Main () metódusban adja hozzá a következő kódot.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
// This is standard code to interact with Blob storage.
StorageCredentials creds = new StorageCredentials(
    CloudConfigurationManager.GetSetting("accountName"),
    CloudConfigurationManager.GetSetting("accountKey")
);
CloudStorageAccount account = new CloudStorageAccount(creds, useHttps: true);
CloudBlobClient client = account.CreateCloudBlobClient();
CloudBlobContainer contain = client.GetContainerReference(CloudConfigurationManager.GetSetting("container"));
contain.CreateIfNotExists();

// The Resolver object is used to interact with Key Vault for Azure Storage.
// This is where the GetToken method from above is used.
KeyVaultKeyResolver cloudResolver = new KeyVaultKeyResolver(GetToken);
```
---

> [!NOTE]
> Key Vault Object models
> 
> Fontos tisztában lenni azzal, hogy valójában két Key Vault objektum-modellről van szó: az egyik a REST API (a kulcstartó névterén) alapul, a másik pedig az ügyféloldali titkosítás kiterjesztése.
> 
> A Key Vault ügyfél együttműködik a REST API, és megérti a JSON-webkulcsokat és titkos kódokat a Key Vaultban található két fajta dolog tekintetében.
> 
> A Key Vault bővítmények olyan osztályok, amelyek látszólag kifejezetten az Azure Storage-beli ügyféloldali titkosításhoz lettek létrehozva. A kulcsok (Rendszerállapotkulcsot) és osztályok illesztőfelületét tartalmazzák a kulcs feloldójának koncepciója alapján. A Rendszerállapotkulcsot két implementációja szükséges: a RSAKey és a SymmetricKey. Most pedig egybeesik a Key Vaultban található dolgokkal, de ezen a ponton független osztályok (így a Key Vault-ügyfél által lekért kulcs és titkos kód nem valósítja meg a Rendszerállapotkulcsot).
> 
> 

## <a name="encrypt-blob-and-upload"></a>BLOB titkosítása és feltöltés

Adja hozzá a következő kódot a blob titkosításához, majd töltse fel az Azure Storage-fiókjába. A használt **ResolveKeyAsync** metódus egy rendszerállapotkulcsot ad vissza.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
// Retrieve the key that you created previously.
// The IKey that is returned here is an RsaKey.
var rsa = cloudResolver.ResolveKeyAsync(
            "https://contosokeyvault.vault.azure.net/keys/TestRSAKey1", 
            CancellationToken.None).GetAwaiter().GetResult();

// Now you simply use the RSA key to encrypt by setting it in the BlobEncryptionPolicy.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(rsa, null);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

// Reference a block blob.
CloudBlockBlob blob = contain.GetBlockBlobReference("MyFile.txt");

// Upload using the UploadFromStream method.
using (var stream = System.IO.File.OpenRead(@"C:\Temp\MyFile.txt"))
    blob.UploadFromStream(stream, stream.Length, null, options, null);
```
---

> [!NOTE]
> Ha megtekinti a BlobEncryptionPolicy konstruktort, látni fogja, hogy el tud fogadni egy kulcsot és/vagy egy feloldót. Vegye figyelembe, hogy jelenleg nem használhat feloldót a titkosításhoz, mert jelenleg nem támogatja az alapértelmezett kulcsot.


## <a name="decrypt-blob-and-download"></a>BLOB visszafejtése és letöltése

A visszafejtési osztályok használata valóban hasznos, ha a feloldó osztályokat használja. A titkosításhoz használt kulcs azonosítója társítva van a blobhoz a metaadataiban, így nincs ok a kulcs lekérésére és a kulcs és a blob közötti társítás megjegyzésére. Csak meg kell győződnie arról, hogy a kulcs a Key Vaultban marad.   

Egy RSA-kulcs titkos kulcsa Key Vault marad, ezért a visszafejtéshez a CEK tartalmazó blob-metaadatok titkosított kulcsát a rendszer elküldi Key Vault a visszafejtéshez.

Adja hozzá a következőt az imént feltöltött blob visszafejtéséhez.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
// In this case, we will not pass a key and only pass the resolver because
// this policy will only be used for downloading / decrypting.
BlobEncryptionPolicy policy = new BlobEncryptionPolicy(null, cloudResolver);
BlobRequestOptions options = new BlobRequestOptions() { EncryptionPolicy = policy };

using (var np = File.Open(@"C:\data\MyFileDecrypted.txt", FileMode.Create))
    blob.DownloadToStream(np, null, options, null);
```
---

> [!NOTE]
> A kulcskezelő szolgáltatás egyszerűbben elvégezhető, többek között a következők: AggregateKeyResolver és CachingKeyResolver.

## <a name="use-key-vault-secrets"></a>Key Vault Secrets használata

Az ügyféloldali titkosítással való titkos kód használata a SymmetricKey osztályon keresztül történik, mivel a titkos kulcs lényegében szimmetrikus kulcs. De a fentiekben leírtak szerint a Key Vault titkos kulcsa nem pontosan a SymmetricKey mutat. Néhány Tudnivaló:

* A SymmetricKey lévő kulcsnak rögzített hosszúságú kell lennie: 128, 192, 256, 384 vagy 512 bit.
* A SymmetricKey lévő kulcsnak Base64 kódolású kell lennie.
* A SymmetricKey használni kívánt Key Vault titoknak az "Application/oktett-Stream" tartalomtípusnak kell lennie Key Vaultban.

Itt látható egy példa arra a PowerShell-re, amely egy titkos kulcsot hoz létre Key Vaultban, amelyet SymmetricKey használhat.
Vegye figyelembe, hogy a rögzített érték ($key) csak demonstrációs célra szolgál. A kulcsot a saját kódjában szeretné előállítani.

```powershell
// Here we are making a 128-bit key so we have 16 characters.
//     The characters are in the ASCII range of UTF8 so they are
//    each 1 byte. 16 x 8 = 128.
$key = "qwertyuiopasdfgh"
$b = [System.Text.Encoding]::UTF8.GetBytes($key)
$enc = [System.Convert]::ToBase64String($b)
$secretvalue = ConvertTo-SecureString $enc -AsPlainText -Force

// Substitute the VaultName and Name in this command.
$secret = Set-AzureKeyVaultSecret -VaultName 'ContosoKeyVault' -Name 'TestSecret2' -SecretValue $secretvalue -ContentType "application/octet-stream"
```

A konzol alkalmazásban ugyanazt a hívást használhatja, mint korábban a titkos kód SymmetricKey való lekéréséhez.

# <a name="net-v12"></a>[.NET V12](#tab/dotnet)

Jelenleg dolgozunk olyan kódrészletek létrehozásán, amelyek tükrözik az Azure Storage ügyféloldali kódtárainak 12. x verzióját. További információ: [Az Azure Storage V12 ügyféloldali kódtárainak bejelentése](https://techcommunity.microsoft.com/t5/azure-storage/announcing-the-azure-storage-v12-client-libraries/ba-p/1482394).

# <a name="net-v11"></a>[.NET-v11](#tab/dotnet11)

```csharp
SymmetricKey sec = (SymmetricKey) cloudResolver.ResolveKeyAsync(
    "https://contosokeyvault.vault.azure.net/secrets/TestSecret2/",
    CancellationToken.None).GetAwaiter().GetResult();
```
---

## <a name="next-steps"></a>Következő lépések

További információ a Microsoft Azure Storage C# használatával történő használatáról: [Microsoft Azure Storage ügyféloldali kódtára a .net-hez](/previous-versions/azure/dn261237(v=azure.100)).

További információ a blob REST APIről: [blob Service REST API](/rest/api/storageservices/Blob-Service-REST-API).

A Microsoft Azure Storage legfrissebb információit a [Microsoft Azure Storage csapat blogjában](/archive/blogs/windowsazurestorage/)találja.