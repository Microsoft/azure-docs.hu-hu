---
title: Rövid útmutató – Azure Key Vault kulcsok ügyféloldali kódtára a .NET-hez (4-es verzió)
description: Megtudhatja, hogyan hozhat létre, olvashat be és törölhet kulcsokat egy Azure-kulcstartóból a .NET ügyféloldali kódtár használatával (4-es verzió)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: 5c1e4d64ba3359a07dddbbf89774e31815935230
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2021
ms.locfileid: "107818421"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Rövid útmutató: Azure Key Vault .NET-hez (SDK v4) készült fő ügyféloldali kódtár létrehozása

Első lépések a Azure Key Vault .NET-hez való ügyféloldali kódtárával. [Azure Key Vault](../general/overview.md) egy felhőszolgáltatás, amely biztonságos tárolót biztosít a titkosítási kulcsok számára. Biztonságosan tárolhat titkosítási kulcsokat, jelszavakat, tanúsítványokat és egyéb titkos kódokat. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, olvashat be és törölhet kulcsokat egy Azure Key Vaultból a .NET-kulcs ügyféloldali kódtárával

Key Vault ügyféloldali kódtár erőforrásainak létrehozása:

[API-referenciadokumentáció](/dotnet/api/azure.security.keyvault.keys)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Package (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

A kulcsokkal és kulcsokkal kapcsolatos Key Vault lásd:
- [Key Vault áttekintés](../general/overview.md)
- [Kulcsok áttekintése.](about-keys.md)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés – [hozzon létre egyet ingyenesen](https://azure.microsoft.com/free/dotnet)
* [.NET Core 3.1 SDK vagy újabb](https://dotnet.microsoft.com/download/dotnet-core)
* [Azure CLI](/cli/azure/install-azure-cli)
* Egy Key Vault – létrehozhat egyet a [Azure Portal,](../general/quick-create-portal.md) [az Azure CLI](../general/quick-create-cli.md)vagy a [Azure PowerShell.](../general/quick-create-powershell.md)

## <a name="setup"></a>Beállítás

Ez a rövid útmutató az Azure Identity Library használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual Studio vagy Visual Studio Code használatával is hitelesítik a hívásokat. További információ: Az ügyfél hitelesítése az Azure Identity ügyféloldali [kódtárával.](/dotnet/api/overview/azure/identity-readme?#authenticate-the-client&preserve-view=true)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
    ```azurecli-interactive
    az login
    ```
    # <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)
    
    ```azurepowershell-interactive
    Connect-AzAccount
    ```
    ---

    Ha az Azure CLI Azure PowerShell meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.

    Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és írja be a terminálban [https://aka.ms/devicelogin](https://aka.ms/devicelogin) megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

#### <a name="grant-access-to-your-key-vault"></a>Hozzáférés megadása a kulcstartóhoz

Hozzáférési szabályzat létrehozása a kulcstartóhoz, amely kulcsengedélyeket ad a felhasználói fióknak

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli-interactive
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell-interactive
Set-AzKeyVaultAccessPolicy -VaultName <your-key-vault-name> -UserPrincipalName user@domain.com -PermissionsToSecrets delete,get,list,set,purge
```
---

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

Telepítse a .NET-hez Azure Key Vault kulcs ügyféloldali kódtárát a parancshéjból:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
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
```azurepowershell
$Env:KEY_VAULT_NAME="<your-key-vault-name>"
```

macOS vagy Linux
```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="object-model"></a>Objektummodell

A Azure Key Vault .NET-hez való fő ügyféloldali kódtára lehetővé teszi a kulcsok kezelését. A [Példakódok](#code-examples) szakasz bemutatja, hogyan lehet ügyfelet létrehozni, kulcsot beállítani, kulcsot lekérni és kulcsokat törölni.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Direktíva hozzáadása

Adja hozzá a következő irányelveket a *Program.cs fájl tetejéhez:*

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó hitelesítést használ a Key Vaultban, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service virtuális géphez kell hozzárendelni. További információ: Felügyelt identitások [áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a key vault URI-nevére, "https:// \<your-key-vault-name\> .vault.azure.net". Ez a példa az [Azure](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme)Identity [Library "DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) osztályát használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben, különböző beállításokkal az identitások megadása érdekében. További információ a Key Vaultban való hitelesítésről: [Fejlesztői útmutató.](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code)

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Kulcs mentése

Ehhez a feladathoz használja a [CreateKeyAsync metódust.](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync) A metódus paraméterei elfogadják a kulcsnevet és a [kulcstípust.](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype)

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Ha a kulcsnév létezik, a fenti kód létrehozza a kulcs új verzióját.

### <a name="retrieve-a-key"></a>Kulcs lekérése

Most már lekérheti a korábban létrehozott kulcsot a [GetKeyAsync metódussal.](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync)

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Kulcs törlése

Végül töröljük és töröljük a kulcsot a kulcstartóból a [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) és [a PurgeDeletedKeyAsync metódussal.](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync)

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Mintakód

Módosítsa úgy a .NET Core-konzolalkalmazást, hogy az Key Vault a következő lépések elvégzésével:

- Cserélje le a *Program.cs fájlban a kódot* a következőre:

    ```csharp
    using System;
    using System.Threading.Tasks;
    using Azure.Identity;
    using Azure.Security.KeyVault.Keys;
    
    namespace key_vault_console_app
    {
        class Program
        {
            static async Task Main(string[] args)
            {
                const string keyName = "myKey";
                var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
                var kvUri = $"https://{keyVaultName}.vault.azure.net";
    
                var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
    
                Console.Write($"Creating a key in {keyVaultName} called '{keyName}' ...");
                var createdKey = await client.CreateKeyAsync(keyName, KeyType.Rsa);
                Console.WriteLine("done.");
    
                Console.WriteLine($"Retrieving your key from {keyVaultName}.");
                var key = await client.GetKeyAsync(keyName);
                Console.WriteLine($"Your key version is '{key.Value.Properties.Version}'.");
    
                Console.Write($"Deleting your key from {keyVaultName} ...");
                var deleteOperation = await client.StartDeleteKeyAsync(keyName);
                // You only need to wait for completion if you want to purge or recover the key.
                await deleteOperation.WaitForCompletionAsync();
                Console.WriteLine("done.");

                Console.Write($"Purging your key from {keyVaultName} ...");
                await client.PurgeDeletedKeyAsync(keyName);
                Console.WriteLine(" done.");
            }
        }
    }
    ```
### <a name="test-and-verify"></a>Tesztelés és ellenőrzés

1.  Hajtsa végre a következő parancsot a projekt felépítéséhez

    ```dotnetcli
    dotnet build
    ```

1. Az alkalmazás futtatásához futtassa a következő parancsot.

    ```dotnetcli
    dotnet run
    ```

1. Amikor a rendszer kéri, adjon meg egy titkos értéket. Például: mySecretPassword.

    Megjelenik a következő kimenet egy változata:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy kulcstartót, tárolt egy kulcsot, és lekérte azt. 

Ha többet szeretne megtudni a Key Vault és az alkalmazásokba való integrálásáról, tekintse meg a következő cikkeket:

- Olvassa el [a Azure Key Vault](../general/overview.md)
- Olvassa el a [kulcsok áttekintését](about-keys.md)
- Hozzáférési útmutató [Key Vault App Service alkalmazásból](../general/tutorial-net-create-vault-azure-web-app.md)
- [Hozzáférés-Key Vault virtuális gépről – oktatóanyag](../general/tutorial-net-virtual-machine.md)
- Lásd Azure Key Vault [fejlesztői útmutatót](../general/developers-guide.md)
- A biztonsági [Key Vault áttekintése](../general/security-features.md)
