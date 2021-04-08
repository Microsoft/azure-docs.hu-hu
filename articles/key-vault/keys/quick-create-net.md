---
title: Gyors útmutató – Azure Key Vault kulcsok ügyféloldali kódtára a .NET-hez (4-es verzió)
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet kulcsokat az Azure Key vaultból a .NET ügyféloldali kódtár használatával (4-es verzió)
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/23/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-csharp
ms.openlocfilehash: cecf8330b7060a4cbc4691f64571a3c7865c575c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2021
ms.locfileid: "97935257"
---
# <a name="quickstart-azure-key-vault-key-client-library-for-net-sdk-v4"></a>Rövid útmutató: a .NET-hez készült ügyféloldali kódtár (SDK v4) Azure Key Vault

Ismerkedjen meg a .NET-hez készült Azure Key Vault Key ügyféloldali kódtáraval. A [Azure Key Vault](../general/overview.md) egy felhőalapú szolgáltatás, amely biztonságos tárolót biztosít a titkosítási kulcsokhoz. Biztonságosan tárolhatja a titkosítási kulcsokat, a jelszavakat, a tanúsítványokat és más titkokat. Az Azure-kulcstartók létrehozhatók és kezelhetők az Azure Portal segítségével is. Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre, kérhet le és törölhet kulcsokat egy Azure Key vaultból a .NET-kulcs ügyféloldali kódtár használatával

Key Vault legfontosabb ügyféloldali függvénytár-erőforrások:

[API-referenciák dokumentációja](/dotnet/api/azure.security.keyvault.keys)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/keyvault)  |  [Csomag (NuGet)](https://www.nuget.org/packages/Azure.Security.KeyVault.keys/)

A Key Vault és a kulcsokkal kapcsolatos további információkért lásd:
- [Key Vault áttekintése](../general/overview.md)
- [Kulcsok áttekintése](about-keys.md).

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

Hozzon létre egy hozzáférési szabályzatot a kulcstartó számára, amely a felhasználói fiók számára biztosít kulcsfontosságú engedélyeket

```console
az keyvault set-policy --name <your-key-vault-name> --upn user@domain.com --key-permissions delete get list create purge
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

A parancssorból telepítse a .NET-hez készült Azure Key Vault Key ügyféloldali kódtárat:

```dotnetcli
dotnet add package Azure.Security.KeyVault.Keys
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

A .NET-hez készült Azure Key Vault Key ügyféloldali kódtára lehetővé teszi a kulcsok kezelését. A [példák](#code-examples) az ügyfelek létrehozására, a kulcs megadására, a kulcs lekérésére és a kulcsok törlésére mutatnak.

## <a name="code-examples"></a>Kódpéldák

### <a name="add-directives"></a>Irányelvek hozzáadása

Adja hozzá a következő irányelveket a program elejéhez *. cs*:

```csharp
using System;
using Azure.Identity;
using Azure.Security.KeyVault.Keys;
```

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó a Key Vault hitelesítésére szolgál, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service vagy virtuális géphez kell rendelni, további információért lásd: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa az [Azure Identity Library](https://docs.microsoft.com/dotnet/api/overview/azure/identity-readme) ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) osztályát használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitás biztosításához. További információ a Key Vault hitelesítéséről: [fejlesztői útmutató](https://docs.microsoft.com/azure/key-vault/general/developers-guide#authenticate-to-key-vault-in-code).

```csharp
var keyVaultName = Environment.GetEnvironmentVariable("KEY_VAULT_NAME");
var kvUri = "https://" + keyVaultName + ".vault.azure.net";

var client = new KeyClient(new Uri(kvUri), new DefaultAzureCredential());
```

### <a name="save-a-key"></a>Kulcs mentése

Ehhez a feladathoz használja a [CreateKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.createkeyasync) metódust. A metódus paraméterei elfogadják a kulcs nevét és a [kulcs típusát](https://docs.microsoft.com/dotnet/api/azure.security.keyvault.keys.keytype).

```csharp
var key = await client.CreateKeyAsync("myKey", KeyType.Rsa);
```

> [!NOTE]
> Ha a kulcsnév létezik, a fenti kód a kulcs új verzióját fogja létrehozni.

### <a name="retrieve-a-key"></a>Kulcs lekérése

Most már lekérheti a korábban létrehozott kulcsot a [GetKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.getkeyasync) metódussal.

```csharp
var key = await client.GetKeyAsync("myKey");
```

### <a name="delete-a-key"></a>Kulcs törlése

Végül törölje és ürítse ki a kulcsot a Key vaultból a [StartDeleteKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.startdeletekeyasync) és a [PurgeDeletedKeyAsync](/dotnet/api/azure.security.keyvault.keys.keyclient.purgedeletedkeyasync) metódussal.

```csharp
var operation = await client.StartDeleteKeyAsync("myKey");

// You only need to wait for completion if you want to purge or recover the key.
await operation.WaitForCompletionAsync();

var key = operation.Value;
await client.PurgeDeletedKeyAsync("myKey");
```

## <a name="sample-code"></a>Mintakód

Módosítsa a .NET Core Console alkalmazást úgy, hogy az a következő lépések végrehajtásával működjön együtt a Key Vault:

- Cserélje a *program. cs* kódot a következő kódra:

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

1.  A projekt összeállításához hajtsa végre a következő parancsot

    ```dotnetcli
    dotnet build
    ```

1. Futtassa az alábbi parancsot az alkalmazás futtatásához.

    ```dotnetcli
    dotnet run
    ```

1. Ha a rendszer kéri, adjon meg egy titkos értéket. Például: mySecretPassword.

    Megjelenik a következő kimenet egy változata:

    ```console
    Creating a key in mykeyvault called 'myKey' ... done.
    Retrieving your key from mykeyvault.
    Your key version is '8532359bced24e4bb2525f2d2050738a'.
    Deleting your key from jl-kv ... done
    Purging your key from <your-unique-keyvault-name> ... done.   
    ```

## <a name="next-steps"></a>Következő lépések

Ebben a rövid útmutatóban létrehozott egy Key vaultot, tárolt egy kulcsot, és lekérte a kulcsot. 

Ha többet szeretne megtudni a Key Vaultről és az alkalmazásokkal való integrálásáról, tekintse meg a következő cikkeket:

- [A Azure Key Vault áttekintése](../general/overview.md)
- [A kulcsok áttekintésének](about-keys.md) beolvasása
- [Hozzáférési Key Vault megtekintése app Service alkalmazásról – oktatóanyag](../general/tutorial-net-create-vault-azure-web-app.md)
- [Hozzáférési Key Vault megtekintése a virtuális gépről – oktatóanyag](../general/tutorial-net-virtual-machine.md)
- Tekintse [meg a Azure Key Vault fejlesztői útmutatóját](../general/developers-guide.md)
- Tekintse át a [Key Vault biztonsági áttekintést](../general/security-overview.md)
