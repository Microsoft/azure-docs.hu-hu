---
title: Rövid útmutató – Azure Key Vault .NET-hez (4-es verzió) elérhető tanúsítványok ügyféloldali kódtára
description: Megtudhatja, hogyan hozhat létre, olvashat be és törölhet tanúsítványokat egy Azure-kulcstartóból a .NET ügyféloldali kódtár használatával (4-es verzió)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: certificates
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 9188079a93c9c2688a310413401e90014b278bf8
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107813415"
---
# <a name="quickstart-azure-key-vault-certificate-client-library-for-net-sdk-v4"></a>Rövid útmutató: Azure Key Vault .NET-hez készült tanúsítvány ügyféloldali kódtárának létrehozása (SDK v4)

A .NET-hez Azure Key Vault tanúsítvány ügyféloldali kódtárának első lépések. [Azure Key Vault](../general/overview.md) egy felhőszolgáltatás, amely biztonságos tárolót biztosít a tanúsítványok számára. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, olvashat be és törölhet tanúsítványokat egy Azure Key Vaultból a .NET ügyféloldali kódtár használatával

Key Vault ügyféloldali kódtár erőforrásai:

[API-referenciadokumentáció](/dotnet/api/azure.security.keyvault.certificates)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Package (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Certificates/)

További információ a tanúsítványok Key Vault tanúsítványokról:
- [Key Vault áttekintés](../general/overview.md)
- [Tanúsítványok áttekintése.](about-certificates.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK vagy újabb](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* Egy Key Vault – létrehozhat egyet a [Azure Portal,](../general/quick-create-portal.md) [az Azure CLI](../general/quick-create-cli.md)vagy a [Azure PowerShell.](../general/quick-create-powershell.md)

Ez a rövid útmutató az és az `dotnet` Azure CLI-t használja

## <a name="setup"></a>Beállítás

Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual Studio vagy Visual Studio Code használatával is hitelesítik a hívásokat. További információ: Az ügyfél hitelesítése az Azure Identity ügyféloldali [kódtárával.](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.

    Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és írja be a terminálban [https://aka.ms/devicelogin](https://aka.ms/devicelogin) megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

#### <a name="grant-access-to-your-key-vault"></a>Hozzáférés megadása a kulcstartóhoz

Hozzáférési szabályzat létrehozása a kulcstartóhoz, amely tanúsítványengedélyeket ad a felhasználói fióknak

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --certificate-permissions delete get list create purge
```

### <a name="create-new-net-console-app"></a>Új .NET-konzolalkalmazás létrehozása

1. A parancshéjban futtassa a következő parancsot egy nevű projekt `key-vault-console-app` létrehozásához:

    ```dotnetcli
    dotnet new console --name key-vault-console-app
    ```

1. Váltsa át az újonnan *létrehozott key-vault-console-app* könyvtárat, és futtassa a következő parancsot a projekt felépítéséhez:

    ```dotnetcli
    dotnet build
    ```

    A build kimenete nem tartalmazhat figyelmeztetéseket vagy hibákat.
    
    ```console
    Build succeeded.
     0 Warning(s)
     0 Error(s)
    ```

### <a name="install-the-packages"></a>A csomagok telepítése

Telepítse a .NET-hez Azure Key Vault tanúsítvány ügyféloldali kódtárát a parancshéjból:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Certificates
```

Ehhez a rövid útmutatóhoz telepítenie kell az Azure SDK Azure Identityhez készült ügyféloldali kódtárát is:

```dotnetcli
dotnet add package Azure.Identity
```

#### <a name="set-environment-variables"></a>Környezeti változók beállítása

Ez az alkalmazás a kulcstartó nevét használja a nevű környezeti `KEY_VAULT_NAME` változóként.

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

A Azure Key Vault .NET-hez szükséges tanúsítvány ügyféloldali kódtára lehetővé teszi a tanúsítványok kezelését. A [Példakódok](#code-examples) szakasz bemutatja, hogyan lehet ügyfelet létrehozni, tanúsítványt beállítani, tanúsítványt lekérni és tanúsítványt törölni.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Direktíva hozzáadása

Adja hozzá a következő irányelveket a *Program.cs fájl tetejéhez:*

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Certificates;
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó hitelesítést használ a Key Vaultban, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service virtuális géphez kell hozzárendelni. További információ: Felügyelt identitások [áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a key vault URI-nevére, "https:// \<your-key-vault-name\> .vault.azure.net". Ez a példa az [Azure](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)Identity [Library "DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) osztályát használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben, különböző beállításokkal az identitások megadása érdekében. A Key Vaultban való hitelesítéssel kapcsolatos további információkért lásd: [Fejlesztői útmutató.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

```csharp
string keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new CertificateClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-certificate"></a>Tanúsítvány mentése

Ebben a példában az egyszerűség kedvéért használhat önaírt tanúsítványt az alapértelmezett kiállítási szabályzattal. Ehhez a feladathoz használja a [StartCreateCertificateAsync metódust.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startcreatecertificateasync) A metódus paraméterei egy tanúsítványnevet és a tanúsítvány-házirendet [fogadják el.](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.certificates.certificatepolicy)

```csharp
var operation = await client.StartCreateCertificateAsync("myCertificate", CertificatePolicy.Default);
var certificate = await operation.WaitForCompletionAsync();
```

> [!NOTE]
> Ha létezik tanúsítványnév, a fenti kód létrehozza a tanúsítvány új verzióját.

### <a name="retrieve-a-certificate"></a>Tanúsítvány lekérése

Most már lekérheti a korábban létrehozott tanúsítványt a [GetCertificateAsync metódussal.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.getcertificateasync)

```csharp
var certificate = await client.GetCertificateAsync("myCertificate");
```

### <a name="delete-a-certificate"></a>Tanúsítvány törlése

Végül töröljük és töröljük a tanúsítványt a kulcstartóból a [StartDeleteCertificateAsync](/dotnet/api/azure.security.keyvault.certificates.certificateclient.startdeletecertificateasync) és [a PurgeDeletedCertificateAsync metódussal.](/dotnet/api/azure.security.keyvault.certificates.certificateclient.purgedeletedcertificateasync)

```csharp
var operation = await client.StartDeleteCertificateAsync("myCertificate");

// You only need to wait for completion if you want to purge or recover the certificate.
await operation.WaitForCompletionAsync();

var certificate = operation.Value;
await client.PurgeDeletedCertificateAsync("myCertificate");
```

## <a name="sample-code"></a>Mintakód

Módosítsa úgy a .NET Core-konzolalkalmazást, hogy az Key Vault a következő lépések elvégzésével:

- Cserélje le a *Program.cs fájlban a kódot* a következőre:

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

Hajtsa végre a következő parancsot a projekt felépítéséhez

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

Ha többet szeretne megtudni a Key Vault és az alkalmazásokba való integrálásáról, tekintse meg a következő cikkeket:

- Olvassa el [a Azure Key Vault](../general/overview.md)
- Olvassa el a [tanúsítványok áttekintését](about-certificates.md)
- Hozzáférési útmutató [Key Vault App Service alkalmazásból](../general/tutorial-net-create-vault-azure-web-app.md)
- [Hozzáférés-Key Vault virtuális gépről – oktatóanyag](../general/tutorial-net-virtual-machine.md)
- Lásd [Azure Key Vault fejlesztői útmutatót](../general/developers-guide.md)
- A biztonsági [Key Vault áttekintése](../general/security-features.md)
