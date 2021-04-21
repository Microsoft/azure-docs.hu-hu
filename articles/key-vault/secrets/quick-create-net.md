---
title: Rövid útmutató – Azure Key Vault .NET titkos kódtárának létrehozása (4-es verzió)
description: Megtudhatja, hogyan hozhat létre, olvashat be és törölhet titkos kódokat egy Azure Key Vaultból a .NET ügyféloldali kódtár használatával (4-es verzió)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: 6338b76e2f14e9f842b3395113badadcd37c8d32
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107814171"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Rövid útmutató: Azure Key Vault .NET-hez (SDK v4) készült titkos ügyféloldali kódtár létrehozása

A .NET-Azure Key Vault titkos ügyféloldali kódtárának első lépések. [Azure Key Vault](../general/overview.md) egy felhőszolgáltatás, amely biztonságos tárolót biztosít a titkos kulcsok számára. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, olvashat be és törölhet titkos kulcsokat egy Azure Key Vaultból a .NET ügyféloldali kódtár használatával

Key Vault ügyféloldali kódtár erőforrásai:

[API-referenciadokumentáció](/dotnet/api/azure.security.keyvault.secrets)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Package (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

További információ a titkos Key Vault és a titkos kulcsokról:
- [Key Vault áttekintés](../general/overview.md)
- [Titkos kulcsok áttekintése.](about-secrets.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK vagy újabb](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* Egy Key Vault – létrehozhat egyet az [Azure CLI Azure Portal](../general/quick-create-cli.md) [](../general/quick-create-powershell.md) vagy a Azure PowerShell [](../general/quick-create-portal.md)

Ez a rövid útmutató az és az `dotnet` Azure CLI-t használja

## <a name="setup"></a>Beállítás

Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual Studio vagy Visual Studio Code használatával is hitelesítik a hívásokat. További információ: Az ügyfél hitelesítése az Azure Identity ügyféloldali [kódtárával.](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.

    Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és adja meg a terminálon [https://aka.ms/devicelogin](https://aka.ms/devicelogin) megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="grant-access-to-your-key-vault"></a>Hozzáférés megadása a kulcstartóhoz

Hozzáférési szabályzat létrehozása a kulcstartóhoz, amely titkos engedélyeket ad a felhasználói fióknak

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
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

Telepítse a .NET-hez Azure Key Vault titkos ügyféloldali kódtárat a parancshéjból:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
```

Ebben a rövid útmutatóban az Azure SDK Azure Identityhez készült ügyféloldali kódtárát is telepítenie kell:

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

A Azure Key Vault .NET titkos ügyféloldali kódtára lehetővé teszi a titkos kulcsok kezelését. A [Példakódok](#code-examples) szakasz bemutatja, hogyan lehet ügyfelet létrehozni, titkos kódot beállítani, titkos kódot lekérni és titkos kódot törölni.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Direktíva hozzáadása

Adja hozzá a következő irányelveket a *Program.cs fájl tetejéhez:*

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó hitelesítést használ a key vaultban, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service virtuális géphez. További információ: Felügyelt identitás [áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a kulcstartó URI-nevére, "https:// \<your-key-vault-name\> .vault.azure.net" formátumban. Ebben a példában az Azure Identity [Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) ["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) osztályát használjuk, amely lehetővé teszi, hogy ugyanazt a kódot különböző környezetekben használjuk különböző identitás-beállításokkal. További információ a Key Vaultban való hitelesítésről: [Fejlesztői útmutató.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Titkos fájl mentése

Most, hogy hitelesítette a konzolalkalmazást, adjon hozzá egy titkos adatokat a kulcstartóhoz. Ehhez a feladathoz használja a [SetSecretAsync metódust.](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) A metódus első paramétere elfogadja a &mdash; mintában a "mySecret" titkos név nevét.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Ha a titkos kód neve létezik, a fenti kód létrehozza a titkos kód új verzióját.


### <a name="retrieve-a-secret"></a>Titkos adat lekérése

Most már lekérheti a korábban beállított értéket a [GetSecretAsync metódussal.](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync)

```csharp
var secret = await client.GetSecretAsync(secretName);
```

A titkos adatokat a rendszer a következőként `secret.Value` menti: .

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül töröljük a titkos adatokat a kulcstartóból a [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) és [a PurgeDeletedSecretAsync metódussal.](/dotnet/api/azure.security.keyvault.keys.keyclient)

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>Mintakód

Módosítsa úgy a .NET Core-konzolalkalmazást, hogy az Key Vault a következő lépések elvégzésével:

1. Cserélje le a *Program.cs fájlban a kódot* a következőre:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Secrets;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string secretName = "mySecret";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write("Input the value of your secret > ");
                var secretValue = Console.ReadLine();
    
                Console.Write($"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...");
                await client.SetSecretAsync(secretName, secretValue);
                Console.WriteLine(" done.");
    
                Console.WriteLine("Forgetting your secret.");
                secretValue = string.Empty;
                Console.WriteLine($"Your secret is '{secretValue}'.");
    
                Console.WriteLine($"Retrieving your secret from {keyVaultName}.");
                var secret = await client.GetSecretAsync(secretName);
                Console.WriteLine($"Your secret is '{secret.Value.Value}'.");
    
                Console.Write($"Deleting your secret from {keyVaultName} ...");
                DeleteSecretOperation operation = await client.StartDeleteSecretAsync(secretName);
                // You only need to wait for completion if you want to purge or recover the secret.
                await operation.WaitForCompletionAsync();
                Console.WriteLine(" done.");
                
                Console.Write($"Purging your secret from {keyVaultName} ...");
                await client.PurgeDeletedSecretAsync(secretName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Tesztelés és ellenőrzés

1. Futtassa az alábbi parancsot az alkalmazás futtatásához.

    ```dotnetcli
    dotnet run
    ```

1. Amikor a rendszer kéri, adjon meg egy titkos értéket. Például: mySecretPassword.

Megjelenik a következő kimenet egy változata:

```console
Input the value of your secret > mySecretPassword
Creating a secret in <your-unique-keyvault-name> called 'mySecret' with the value 'mySecretPassword' ... done.
Forgetting your secret.
Your secret is ''.
Retrieving your secret from <your-unique-keyvault-name>.
Your secret is 'mySecretPassword'.
Deleting your secret from <your-unique-keyvault-name> ... done.    
Purging your secret from <your-unique-keyvault-name> ... done.
```

## <a name="next-steps"></a>Következő lépések

Ha többet szeretne megtudni a Key Vault és az alkalmazásokkal való integrálásáról, tekintse meg a következő cikkeket:

- Áttekintés a [Azure Key Vault](../general/overview.md)
- Hozzáférési útmutató [Key Vault App Service alkalmazásból](../general/tutorial-net-create-vault-azure-web-app.md)
- [Hozzáférés-Key Vault virtuális gépről – oktatóanyag](../general/tutorial-net-virtual-machine.md)
- Lásd Azure Key Vault [fejlesztői útmutatót](../general/developers-guide.md)
- Tekintse át [a Key Vault biztonsági áttekintését](../general/security-features.md)
