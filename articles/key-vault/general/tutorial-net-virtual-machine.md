---
title: Oktatóanyag – Azure Key Vault használata virtuális géppel a .NET-ben | Microsoft Docs
description: Ebben az oktatóanyagban egy ASP.NET Core-alkalmazással konfigurál egy virtuális gépet, hogy beolvassa a titkos kulcsot a kulcstartóból.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurecli
ms.openlocfilehash: a56c08e5bf6054d24af3ade571ec625969286a77
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2021
ms.locfileid: "102455644"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Oktatóanyag: Azure Key Vault használata virtuális géppel a .NET-ben

Azure Key Vault segítséget nyújt a titkok, például az API-kulcsok, az alkalmazások, szolgáltatások és informatikai erőforrások eléréséhez szükséges adatbázis-kapcsolati karakterláncok védelemmel való ellátásához.

Ebből az oktatóanyagból megtudhatja, hogyan szerezhet be egy konzolos alkalmazást a Azure Key Vault információk olvasásához. Az alkalmazás a virtuális gép felügyelt identitását használja a Key Vault való hitelesítéshez. 

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot.
> * Kulcstartó létrehozása.
> * Adjon hozzá egy titkos kulcsot a kulcstartóhoz.
> * Titkos kulcs lekérése a kulcstartóból.
> * Hozzon létre egy Azure-beli virtuális gépet.
> * [Felügyelt identitás](../../active-directory/managed-identities-azure-resources/overview.md) engedélyezése a virtuális géphez.
> * Rendeljen engedélyeket a virtuális gép identitásához.

Mielőtt elkezdené, olvassa el [Key Vault alapvető fogalmakat](basic-concepts.md). 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Windows, Mac és Linux rendszerekhez:
  * [Git](https://git-scm.com/downloads)
  * A [.net Core 3,1 SDK vagy újabb verzió](https://dotnet.microsoft.com/download/dotnet-core/3.1).
  * [Azure CLI](/cli/azure/install-azure-cli)-vel.

## <a name="create-resources-and-assign-permissions"></a>Erőforrások létrehozása és engedélyek kiosztása

Mielőtt elkezdené a kódolást, létre kell hoznia néhány erőforrást, be kell állítania egy titkos kulcsot a kulcstartóba, és hozzá kell rendelnie az engedélyeket.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ha az Azure CLI használatával szeretne bejelentkezni az Azure-ba, írja be a következőt:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>A Key Vault feltöltése titkos kulccsal

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Hozzon létre egy Windows vagy Linux rendszerű virtuális gépet az alábbi módszerek egyikével:

| Windows | Linux |
|--|--|
| [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure Portal](../../virtual-machines/windows/quick-create-portal.md) | [Azure Portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Identitás kiosztása a virtuális géphez
Hozzon létre egy rendszerhez rendelt identitást a virtuális géphez az az [VM Identity assign](/cli/azure/vm/identity#az-vm-identity-assign) paranccsal:

```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Jegyezze fel a rendszer által hozzárendelt identitást, amely a következő kódban látható. Az előző parancs kimenete a következő lesz: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Engedélyek kiosztása a virtuális gép identitásához
Rendelje hozzá a korábban létrehozott identitási engedélyeket a kulcstartóhoz az az Key [Vault set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) paranccsal:

```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions get list
```

## <a name="sign-in-to-the-virtual-machine"></a>Bejelentkezés a virtuális gépre

A virtuális gépre való bejelentkezéshez kövesse a [Kapcsolódás és bejelentkezés Azure-beli Windows rendszerű virtuális gépekre](../../virtual-machines/windows/connect-logon.md) című témakör utasításait, vagy [kapcsolódjon be, és jelentkezzen be egy Azure-beli linuxos virtuális gépre](../../virtual-machines/linux/login-using-aad.md).

## <a name="set-up-the-console-app"></a>A konzol alkalmazásának beállítása

Hozzon létre egy Console-alkalmazást, és telepítse a szükséges csomagokat a `dotnet` parancs használatával.

### <a name="install-net-core"></a>A .NET Core telepítése

A .NET Core telepítéséhez nyissa meg a [.net-letöltések](https://www.microsoft.com/net/download) lapot.

### <a name="create-and-run-a-sample-net-app"></a>Minta .NET-alkalmazás létrehozása és futtatása

Nyisson meg egy parancssort.

A következő parancsok futtatásával kinyomtathatja a konzolon a "Hello World" parancsot:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>A csomag telepítése

A konzol ablakban telepítse a Azure Key Vault Secrets ügyféloldali kódtárat a .NET-hez:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Ebben a rövid útmutatóban a következő Identity-csomagot kell telepíteni a Azure Key Vault hitelesítéséhez:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>A konzol alkalmazás szerkesztése

Nyissa meg a *program. cs* fájlt, és adja hozzá a következő csomagokat:

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Adja hozzá ezeket a sorokat, és frissítse az URI-t, hogy az tükrözze a `vaultUri` kulcstartót. Az alábbi kód az  ["DefaultAzureCredential ()"](/dotnet/api/azure.identity.defaultazurecredential) módszert használja a Key Vault hitelesítéséhez, amely tokent használ az alkalmazás által felügyelt identitástól a hitelesítéshez. Emellett exponenciális leállítási is használ az újrapróbálkozásokhoz a Key Vault szabályozása esetén.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";

            var kvUri = "https://<your-key-vault-name>.vault.azure.net";
            SecretClientOptions options = new SecretClientOptions()
            {
                Retry =
                {
                    Delay= TimeSpan.FromSeconds(2),
                    MaxDelay = TimeSpan.FromSeconds(16),
                    MaxRetries = 5,
                    Mode = RetryMode.Exponential
                 }
            };

            var client = new SecretClient(new Uri(kvUri), new DefaultAzureCredential(),options);

            Console.Write("Input the value of your secret > ");
            string secretValue = Console.ReadLine();

            Console.Write("Creating a secret in " + keyVaultName + " called '" + secretName + "' with the value '" + secretValue + "` ...");

            client.SetSecret(secretName, secretValue);

            Console.WriteLine(" done.");

            Console.WriteLine("Forgetting your secret.");
            secretValue = "";
            Console.WriteLine("Your secret is '" + secretValue + "'.");

            Console.WriteLine("Retrieving your secret from " + keyVaultName + ".");

            KeyVaultSecret secret = client.GetSecret(secretName);

            Console.WriteLine("Your secret is '" + secret.Value + "'.");

            Console.Write("Deleting your secret from " + keyVaultName + " ...");

            client.StartDeleteSecret(secretName);

            System.Threading.Thread.Sleep(5000);
            Console.WriteLine(" done.");

        }
    }
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha már nincs rájuk szükség, törölje a virtuális gépet és a kulcstartót.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Azure Key Vault REST API](/rest/api/keyvault/)
