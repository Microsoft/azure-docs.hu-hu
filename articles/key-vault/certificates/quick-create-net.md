---
title: Gyors útmutató – Azure Key Vault tanúsítványok .NET-hez készült ügyféloldali kódtára (4-es verzió)
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet tanúsítványokat egy Azure Key vaultból a .NET ügyféloldali kódtár használatával (4-es verzió)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 4dd216f4018feca8c3461104a5beb220d90fd743
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97932843"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-net-sdk-v4"></a>Gyors útmutató: Azure Key Vault tanúsítvány ügyféloldali kódtára a .NET-hez (SDK v4)

Ismerkedjen meg az Azure Key Vault Certificate .NET-hez készült ügyféloldali kódtáraval. A [Azure Key Vault](../general/overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a tanúsítványokhoz. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, kérhet le és törölhet tanúsítványokat egy Azure Key vaultból a .NET ügyféloldali kódtár használatával

Key Vault ügyféloldali kódtár erőforrásai:

[API-referenciák dokumentációja](/dotnet/api/azure.security.keyvault.certificates)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)

A Key Vault és a tanúsítványokról további információt a következő témakörben talál:
- [Key Vault áttekintése](../general/overview.md)
- A [tanúsítványok áttekintése](about-certificates.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3,1 SDK vagy újabb](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* A Key Vault a [Azure Portal](../general/quick-create-portal.md), az [Azure CLI](../general/quick-create-cli.md)vagy a [Azure PowerShell](../general/quick-create-powershell.md)használatával hozhat létre egyet.

Ez a rövid útmutató az `dotnet` Azure CLI-t használja

## <a name="setup"></a>Beállítás

Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual studiót vagy a Visual Studio Code-ot is használhatják a hívások hitelesítéséhez. További információ: [az ügyfél hitelesítése az Azure Identity Client Library](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)használatával.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

    Ellenkező esetben nyisson meg egy böngészőt, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) és adja meg a terminálon megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

#### <a name="grant-access-to-your-key-vault"></a>Hozzáférés biztosítása a kulcstartóhoz

Hozzon létre egy hozzáférési szabályzatot a kulcstartó számára, amely engedélyt ad a felhasználói fióknak.

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
```

### <a name="create-new-net-console-app"></a>Új .NET Console-alkalmazás létrehozása

1. A parancssorban futtassa a következő parancsot egy nevű projekt létrehozásához `key-vault-console-app` :

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Váltson az újonnan létrehozott *Key-Vault-Console-app* könyvtárra, és futtassa a következő parancsot a projekt felépítéséhez:

    ```dotnetcli
    dotnet build
    ```

    A Build kimenete nem tartalmazhat figyelmeztetést vagy hibát.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>A csomagok telepítése

A parancssorból telepítse a Azure Key Vault-tanúsítvány .NET-hez készült ügyféloldali kódtárat:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Certificates
```

Ehhez a rövid útmutatóhoz telepítenie kell az Azure SDK-ügyfél függvénytárát is az Azure Identity szolgáltatáshoz:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

Ez az alkalmazás a Key Vault nevét használja a nevű környezeti változóként `KEY_VAULT_NAME` .

Windows
```cmd
set KEY_VAULT_NAME=<your-key-vault-name>
````
Windows PowerShell
```powershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS vagy Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektummodell

A Azure Key Vault-tanúsítvány .NET-hez készült ügyféloldali kódtára lehetővé teszi a tanúsítványok kezelését. A [példák](#code-examples) az ügyfelek létrehozására, a tanúsítványok beállítására, a tanúsítványok lekérésére és a tanúsítványok törlésére mutatnak.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a program elejéhez *. cs*:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Certificates;
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó a Key Vault hitelesítésére szolgál, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service vagy virtuális géphez kell rendelni, további információért lásd: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa az [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) osztályát használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitás biztosításához. További információ a Key Vault hitelesítéséről: [fejlesztői útmutató](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-certificate"></a>Tanúsítvány mentése

Ebben a példában az egyszerűség kedvéért önaláírt tanúsítványt is használhat alapértelmezett kiállítási házirenddel. Ehhez a feladathoz használja a [StartCreateCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startcreatecertificateasync) metódust. A metódus paraméterei elfogadják a tanúsítvány nevét és a [tanúsítvány házirendjét](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificatepolicy).

```csharp
var operation = await client.StartCreateCertificateAsync("myCertificate", CertificatePolicy.Default);
var certificate = await operation.WaitForCompletionAsync();
```

> [!NOTE]
> Ha a tanúsítvány neve létezik, a fenti kód a tanúsítvány új verzióját fogja létrehozni.

### <a name="retrieve-a-certificate"></a>Tanúsítvány lekérése

Most már lekérheti a korábban létrehozott tanúsítványt a [GetCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.getcertificateasync) metódussal.

```csharp
var certificate = await client.GetCertificateAsync("myCertificate");
```

### <a name="delete-a-certificate"></a>Tanúsítvány törlése

Végül töröljük és kiürítjük a tanúsítványt a kulcstartóból a [StartDeleteCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startdeletecertificateasync) és a [PurgeDeletedCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.purgedeletedcertificateasync)  metódussal.

```csharp
var operation = await client.StartDeleteCertificateAsync("myCertificate");

// You only need to wait for completion if you want to purge or recover the certificate.
await operation.WaitForCompletionAsync();

var certificate = operation.Value;
await client.PurgeDeletedCertificateAsync("myCertificate");
```

## <a name="sample-code"></a>Mintakód

Módosítsa a .NET Core Console alkalmazást úgy, hogy az a következő lépések végrehajtásával működjön együtt a Key Vault:

- Cserélje a *program. cs* kódot a következő kódra:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Certificates;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string certificateName = "myCertificate";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a certificate in {keyVaultName} called '{certificateName}' ...");
                CertificateOperation operation = await client.StartCreateCertificateAsync(certificateName, CertificatePolicy.Default);
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
    
                Console.WriteLine($"Retrieving your certificate from {keyVaultName}.");
                var certificate = await client.GetCertificateAsync(certificateName);
                Console.WriteLine($"Your certificate version is '{certificate.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your certificate from {keyVaultName} ...");
                DeleteCertificateOperation deleteOperation = await client.StartDeleteCertificateAsync(certificateName);
                // You only need to wait for completion if you want to purge or recover the certificate.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine(" done.");

                Console.Write($"Purging your certificate from {keyVaultName} ...");
                await client.PurgeDeletedCertificateAsync(certificateName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Tesztelés és ellenőrzés

A projekt összeállításához hajtsa végre a következő parancsot

```dotnetcli
dotnet build
```

Megjelenik a következő kimenet egy változata:

```console
Creating a certificate in mykeyvault called 'myCertificate' ... done.
Retrieving your certificate from mykeyvault.
Your certificate version is '8532359bced24e4bb2525f2d2050738a'.
Deleting your certificate from mykeyvault ... done
Purging your certificate from mykeyvault ... done
```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy tanúsítványt, és lekérte a tanúsítványt. 

Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, tekintse meg a következő cikkeket:

- [A Azure Key Vault áttekintése](../general/overview.md)
- [A tanúsítványok áttekintésének](about-certificates.md) beolvasása
- [Hozzáférési Key Vault megtekintése app Service alkalmazásról – oktatóanyag](../general/tutorial-net-create-vault-azure-web-app.md)
- [Hozzáférési Key Vault megtekintése a virtuális gépről – oktatóanyag](../general/tutorial-net-virtual-machine.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)
