---
title: Gyors útmutató – Azure Key Vault Secrets ügyféloldali kódtára a .NET-hez (4-es verzió)
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kódokat egy Azure Key vaultból a .NET ügyféloldali kódtár használatával (4-es verzió)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: a82c2cdf7084b31eb6ba861e48ecffb81e6d1363
ms.sourcegitcommit: 6386854467e74d0745c281cc53621af3bb201920
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2021
ms.locfileid: "102453699"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-net-sdk-v4"></a>Rövid útmutató: Azure Key Vault a .NET-hez készült titkos ügyféloldali kódtár (SDK v4)

Ismerkedjen meg a .NET-hez készült Azure Key Vault Secret ügyféloldali kódtáraval. A [Azure Key Vault](../general/overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a titkok számára. Biztonságosan tárolhatja kulcsait, jelszavait, tanúsítványait és egyéb titkos adatait. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, kérhet le és törölhet titkos kódokat az Azure Key vaultban a .NET ügyféloldali kódtár használatával

Key Vault ügyféloldali kódtár erőforrásai:

[API-referenciák dokumentációja](/dotnet/api/azure.security.keyvault.secrets)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.Secrets/)

A Key Vault és a titkokkal kapcsolatos további információkért lásd:
- [Key Vault áttekintése](../general/overview.md)
- A [titkok áttekintése](about-secrets.md).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3,1 SDK vagy újabb](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* A Key Vault létrehozhat egyet [Azure Portal](../general/quick-create-portal.md) [Azure CLI](../general/quick-create-cli.md)használatával, vagy [Azure PowerShell](../general/quick-create-powershell.md)

Ez a rövid útmutató az `dotnet` Azure CLI-t használja

## <a name="setup"></a>Telepítés

Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual studiót vagy a Visual Studio Code-ot is használhatják a hívások hitelesítéséhez. További információ: [az ügyfél hitelesítése az Azure Identity Client Library](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)használatával.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

    Ellenkező esetben nyisson meg egy böngészőt, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) és adja meg a terminálon megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="grant-access-to-your-key-vault"></a>Hozzáférés biztosítása a kulcstartóhoz

Hozzon létre egy hozzáférési szabályzatot a kulcstartó számára, amely titkos engedélyeket biztosít a felhasználói fióknak

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set purge
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

A parancssorból telepítse a .NET-hez készült Azure Key Vault Secret ügyféloldali kódtárat:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Secrets
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

A .NET-hez készült Azure Key Vault Secret ügyféloldali kódtára lehetővé teszi a titkok kezelését. A [példák](#code-examples) az ügyfelek létrehozására, a titkos kulcs beolvasására és a titkos kód törlésére mutatnak.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a *program.cs* tetejéhez:

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=directives)]

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó a Key Vault hitelesítésére szolgál, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service vagy virtuális géphez kell rendelni, további információért lásd: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa az [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) osztályát használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitás biztosításához. További információ a Key Vault hitelesítéséről: [fejlesztői útmutató](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

[!code-csharp[](~/samples-key-vault-dotnet-quickstart/key-vault-console-app/Program.cs?name=authenticate)]

### <a name="save-a-secret"></a>Titkos kód mentése

Most, hogy a konzol alkalmazás hitelesítése megtörtént, adjon hozzá egy titkos kulcsot a kulcstartóhoz. Ehhez a feladathoz használja a [SetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.setsecretasync) metódust. A metódus első paramétere elfogadja a titkos &mdash; "keresési kifejezésként" nevét ebben a mintában.

```csharp
await client.SetSecretAsync(secretName, secretValue);
```

> [!NOTE]
> Ha a titkos név létezik, a fenti kód a titok új verzióját fogja létrehozni.


### <a name="retrieve-a-secret"></a>Titkos kód beolvasása

Most már lekérheti a korábban beállított értéket a [GetSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.getsecretasync) metódussal.

```csharp
var secret = await client.GetSecretAsync(secretName);
```

A titkos kód most már mentve van `secret.Value` .

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Végül törölje a titkos kulcsot a Key vaultból a [StartDeleteSecretAsync](/dotnet/api/azure.security.keyvault.secrets.secretclient.startdeletesecretasync) és a [PurgeDeletedSecretAsync](/dotnet/api/azure.security.keyvault.keys.keyclient) metódussal.

```csharp
var operation = await client.StartDeleteSecretAsync("mySecret");
// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

await client.PurgeDeletedKeyAsync("mySecret");
```

## <a name="sample-code"></a>Mintakód

Módosítsa a .NET Core Console alkalmazást úgy, hogy az a következő lépések végrehajtásával működjön együtt a Key Vault:

1. Cserélje le a *program.cs* található kódot a következő kódra:

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

1. Ha a rendszer kéri, adjon meg egy titkos értéket. Például: mySecretPassword.

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

Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, tekintse meg a következő cikkeket:

- [A Azure Key Vault áttekintése](../general/overview.md)
- [Hozzáférési Key Vault megtekintése app Service alkalmazásról – oktatóanyag](../general/tutorial-net-create-vault-azure-web-app.md)
- [Hozzáférési Key Vault megtekintése a virtuális gépről – oktatóanyag](../general/tutorial-net-virtual-machine.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)
