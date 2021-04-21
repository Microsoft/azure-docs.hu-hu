---
title: Oktatóanyag – Azure Key Vault használata virtuális géppel a .NET-| Microsoft Docs
description: Ebben az oktatóanyagban egy virtuális gépet fog konfigurálni egy ASP.NET fő alkalmazással, hogy beolvassa a key vault titkos kulcsát.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 03/17/2021
ms.author: mbaldwin
ms.custom: mvc, devx-track-csharp, devx-track-azurepowershell
ms.openlocfilehash: c08d0c210e992cba5bca2695fda0bcf08c4689dc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772090"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-net"></a>Oktatóanyag: Azure Key Vault használata virtuális géppel a .NET-en

Azure Key Vault segít megvédeni a titkos kulcsokat, például az API-kulcsokat, az alkalmazások, szolgáltatások és it-erőforrások eléréséhez szükséges adatbázis-kapcsolati sztringeket.

Ez az oktatóanyag bemutatja, hogyan olvashat be információkat egy konzolalkalmazással a Azure Key Vault. Az alkalmazás a virtuális gép által felügyelt identitással hitelesíti magát a Key Vault. 

Ez az oktatóanyag a következőket mutatja be:

> [!div class="checklist"]
> * Hozzon létre egy erőforráscsoportot.
> * Kulcstartó létrehozása.
> * Titkos kulcs hozzáadása a kulcstartóhoz.
> * Titkos kulcs lekérése a kulcstartóból.
> * Azure-beli virtuális gép létrehozása.
> * Felügyelt [identitás engedélyezése a](../../active-directory/managed-identities-azure-resources/overview.md) virtuális géphez.
> * Engedélyek hozzárendelése a virtuálisgép-identitáshoz.

Mielőtt hozzákezd, olvassa el [a Key Vault alapfogalmait.](basic-concepts.md) 

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Előfeltételek

Windows, Mac és Linux esetén:
  * [Git](https://git-scm.com/downloads)
  * A [.NET Core 3.1 SDK vagy újabb.](https://dotnet.microsoft.com/download/dotnet-core/3.1)
  * [Azure CLI vagy](/cli/azure/install-azure-cli) [Azure PowerShell](/powershell/azure/install-az-ps)

## <a name="create-resources-and-assign-permissions"></a>Erőforrások létrehozása és engedélyek hozzárendelése

A kódolás előtt létre kell hoznia néhány erőforrást, titkos kulcsot kell a kulcstartóba tennie, és engedélyeket kell hozzárendelni.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Az Azure-ba való bejelentkezéshez használja a következő parancsot:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az login
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Connect-AzAccount
```
---

## <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Kulcstartó feltöltése titkos kulccsal

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Virtuális gép létrehozása
Hozzon létre egy Windows vagy Linux rendszerű virtuális gépet az alábbi módszerek egyikével:

| Windows | Linux |
|--|--|
| [Azure CLI](../../virtual-machines/windows/quick-create-cli.md) | [Azure CLI](../../virtual-machines/linux/quick-create-cli.md) |  
| [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) | [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) |
| [Azure Portal](../../virtual-machines/windows/quick-create-portal.md) | [Azure Portal](../../virtual-machines/linux/quick-create-portal.md) |

## <a name="assign-an-identity-to-the-vm"></a>Identitás hozzárendelése a virtuális géphez
Hozzon létre egy rendszer által hozzárendelt identitást a virtuális géphez az alábbi példával:

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az vm identity assign --name <NameOfYourVirtualMachine> --resource-group <YourResourceGroupName>
```

Figyelje meg a rendszer által hozzárendelt identitást, amely az alábbi kódban látható. Az előző parancs kimenete a következő lesz:

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
$vm = Get-AzVM -Name <NameOfYourVirtualMachine>
Update-AzVM -ResourceGroupName <YourResourceGroupName> -VM $vm -IdentityType SystemAssigned
```

Figyelje meg az alábbi kódban megjelenő PrincipalId-t. Az előző parancs kimenete a következő lesz: 


```output
PrincipalId          TenantId             Type             UserAssignedIdentities
-----------          --------             ----             ----------------------
xxxxxxxx-xx-xxxxxx   xxxxxxxx-xxxx-xxxx   SystemAssigned
```
---

## <a name="assign-permissions-to-the-vm-identity"></a>Engedélyek hozzárendelése a virtuálisgép-identitáshoz
Rendelje hozzá a korábban létrehozott identitásengedélyeket a key vaulthoz az [az keyvault set-policy paranccsal:](/cli/azure/keyvault#az_keyvault_set_policy)

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)
```azurecli
az keyvault set-policy --name '<your-unique-key-vault-name>' --object-id <VMSystemAssignedIdentity> --secret-permissions  get list set delete
```
# <a name="azure-powershell"></a>[Azure PowerShell](#tab/azurepowershell)

```azurepowershell
Set-AzKeyVaultAccessPolicy -ResourceGroupName <YourResourceGroupName> -VaultName '<your-unique-key-vault-name>' -ObjectId '<VMSystemAssignedIdentity>' -PermissionsToSecrets  get,list,set,delete
```
---

## <a name="sign-in-to-the-virtual-machine"></a>Bejelentkezés a virtuális gépre

A virtuális gépre való bejelentkezéshez kövesse a Csatlakozás és bejelentkezés Egy [Azure Windows](../../virtual-machines/windows/connect-logon.md) rendszerű virtuális gépre vagy a Csatlakozás, majd egy Azure Linux rendszerű [virtuális gépre való bejelentkezés utasításait.](../../virtual-machines/linux/login-using-aad.md)

## <a name="set-up-the-console-app"></a>A konzolalkalmazás beállítása

Hozzon létre egy konzolalkalmazást, és telepítse a szükséges csomagokat az `dotnet` paranccsal.

### <a name="install-net-core"></a>A .NET Core telepítése

A .NET Core telepítéséhez kattintson a [.NET letöltési oldalára.](https://www.microsoft.com/net/download)

### <a name="create-and-run-a-sample-net-app"></a>Minta .NET-alkalmazás létrehozása és futtatása

Nyisson meg egy parancssort.

A "Hello World" a konzolon a következő parancsok futtatásával nyomtatható ki:

```console
dotnet new console -n keyvault-console-app
cd keyvault-console-app
dotnet run
```

### <a name="install-the-package"></a>A csomag telepítése

A konzolablakban telepítse a titkos Azure Key Vault .NET-hez ügyféloldali kódtárát:

```console
dotnet add package Azure.Security.KeyVault.Secrets
```

Ebben a rövid útmutatóban a következő identitáscsomagot kell telepítenie a hitelesítéshez a Azure Key Vault:

```console
dotnet add package Azure.Identity
```

## <a name="edit-the-console-app"></a>A konzolalkalmazás szerkesztése

Nyissa meg *a Program.cs fájlt,* és adja hozzá a következő csomagokat:

```csharp
using System;
using Azure.Core;
using Azure.Identity;
using Azure.Security.KeyVault.Secrets;
```

Adja hozzá ezeket a sorokat, és frissítse az URI-t úgy, hogy az `vaultUri` tükrözze a kulcstartó azonosítóját. Az alábbi kód a  ["DefaultAzureCredential()"](/dotnet/api/azure.identity.defaultazurecredential) használatával hitelesíti a Key Vaultot, amely az alkalmazás által felügyelt identitás jogkivonatát használja a hitelesítéshez. Emellett exponenciális leépítést is használ az újratitkozáshoz a Key Vault szabályozása esetén.

```csharp
  class Program
    {
        static void Main(string[] args)
        {
            string secretName = "mySecret";
            string keyVaultName = "<your-key-vault-name>";
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
