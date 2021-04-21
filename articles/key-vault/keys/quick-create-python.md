---
title: Rövid útmutató – Azure Key Vault Python ügyféloldali kódtárának létrehozása – kulcsok kezelése
description: Megtudhatja, hogyan hozhat létre, olvashat be és törölhet kulcsokat egy Azure-kulcstartóból a Python ügyféloldali kódtárával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: 2c15090551ad9d84282f65925ff9c2cfbab7e14f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773350"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Rövid útmutató: Azure Key Vault kulcsok ügyféloldali kódtára a Pythonhoz

A Pythonhoz készült Azure Key Vault első lépések. A csomag telepítéséhez kövesse az alábbi lépéseket, és próbálja ki az alapszintű feladatokhoz szükséges példakódot. A titkosítási Key Vault használatával elkerülheti, hogy ilyen kulcsokat tároljon a kódban, ami növeli az alkalmazás biztonságát.

[API-referenciadokumentáció](/python/api/overview/azure/keyvault-keys-readme)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Csomag (Python-csomagindex)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7+ vagy 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Ez a rövid útmutató azt feltételezi, hogy [az Azure CLI-t](/cli/azure/install-azure-cli) egy Linux terminálablakban futtatja.

## <a name="set-up-your-local-environment"></a>A helyi környezet beállítása

Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual Studio vagy Visual Studio Code használatával is hitelesítik a hívásokat. További információ: Az ügyfél hitelesítése az Azure Identity ügyféloldali [kódtárával.](/python/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.

    Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és adja meg a terminálon [https://aka.ms/devicelogin](https://aka.ms/devicelogin) megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="install-the-packages"></a>A csomagok telepítése

1. Egy terminálban vagy parancssorban hozzon létre egy megfelelő projektmappát, majd hozzon létre és aktivál egy Python virtuális környezetet a Python virtuális [környezetek használatával kapcsolatos leírás szerint.](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Telepítse a Azure Active Directory-identitástárat:

    ```terminal
    pip install azure.identity
    ```


1. Telepítse a Key Vault kulcs ügyféloldali kódtárát:

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Hozzáférés megadása a kulcstartóhoz

Hozzon létre egy hozzáférési szabályzatot a kulcstartóhoz, amely titkos kulcsot biztosít a felhasználói fióknak.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
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
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>A mintakód létrehozása

A Azure Key Vault Pythonhoz készült ügyféloldali kódtár lehetővé teszi a titkosítási kulcsok kezelését. A következő kódminta bemutatja, hogyan lehet ügyfelet létrehozni, beállítani egy kulcsot, lekérni egy kulcsot, és törölni egy kulcsot.

Hozzon létre egy *kv_keys.py nevű fájlt,* amely tartalmazza ezt a kódot.

```python
import os
from azure.keyvault.keys import KeyClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = "https://" + keyVaultName + ".vault.azure.net"

credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)

keyName = input("Input a name for your key > ")

print(f"Creating a key in {keyVaultName} called '{keyName}' ...")

rsa_key = client.create_rsa_key(keyName, size=2048)

print(" done.")

print(f"Retrieving your key from {keyVaultName}.")

retrieved_key = client.get_key(keyName)

print(f"Key with name '{retrieved_key.name}' was found.")
print(f"Deleting your key from {keyVaultName} ...")

poller = client.begin_delete_key(keyName)
deleted_key = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>A kód futtatása

Győződjön meg arról, hogy az előző szakaszban található kód egy *kv_keys.py nevű fájlban található.* Ezután futtassa a kódot a következő paranccsal:

```terminal
python kv_keys.py
```

- Ha engedélyekkel kapcsolatos hibákba ütközik, ellenőrizze, hogy futtatta-e a [ `az keyvault set-policy` parancsot.](#grant-access-to-your-key-vault)
- A kód ugyanazon kulcsnévvel való újrafuttatása a következő hibát okozhatja: "(Conflict) Key is currently in adeleted but recoverable state" (Az ütközési kulcs jelenleg <name> törölt, de helyreállítható állapotban van). Használjon másik kulcsnevet.

## <a name="code-details"></a>Kód részletei

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó hitelesítést használ a key vaultban, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service virtuális géphez. További információ: Felügyelt identitás [áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a kulcstartó URI-nevére, "https:// \<your-key-vault-name\> .vault.azure.net" formátumban. Ebben a példában a  ["DefaultAzureCredential()"](/python/api/azure-identity/azure.identity.defaultazurecredential) osztályt használjuk, amely lehetővé teszi, hogy ugyanazt a kódot különböző környezetekben használja, különböző identitásbeállításokkal. További információ: Alapértelmezett [Azure-beli hitelesítőadat-hitelesítés.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Kulcs mentése

Miután beszerezta a kulcstartó ügyfélobjektumát, a kulcstartót a következő [metódussal create_rsa_key:](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-) 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

A vagy a [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) is [create_ec_key.](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-)

A metódus `create` hívása hívást hoz létre az Azure REST API a kulcstartóhoz.

A kérés kezelésekor az Azure hitelesíti a hívó identitását (az egyszerű szolgáltatást) az ügyfélnek megadott hitelesítő objektum használatával.

## <a name="retrieve-a-key"></a>Kulcs lekérése

A kulcs beolvassa a Key Vault, használja a [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-) metódust:

```python
retrieved_key = client.get_key(keyName)
 ```

Azt is ellenőrizheti, hogy a kulcs be van-e állítva az [az keyvault key show Azure CLI-paranccsal.](/cli/azure/keyvault/key?#az_keyvault_key_show)

### <a name="delete-a-key"></a>Kulcs törlése

Kulcs törléséhez használja a [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-) metódust:

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

A `begin_delete_key` metódus aszinkron, és egy poller objektumot ad vissza. A poller metódusának `result` hívása megvárja annak befejezését.

A kulcs törlésének ellenőrzéséhez használja az [az keyvault key show Azure CLI-parancsot.](/cli/azure/keyvault/key?#az_keyvault_key_show)

A törlés után a kulcs egy ideig törölt, de helyreállítható állapotban marad. Ha újra futtatja a kódot, használjon egy másik kulcsnevet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a tanúsítványokkal [](../certificates/quick-create-python.md) és titkos kulcsokkal is kísérletezni [szeretne,](../secrets/quick-create-python.md)újra felhasználhatja Key Vault cikkben létrehozott tanúsítványokat.

Ellenkező esetben, ha végzett a cikkben létrehozott erőforrásokkal, a következő paranccsal törölheti az erőforráscsoportot és annak összes tartalmazott erőforrását:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure Key Vault áttekintése](../general/overview.md)
- [Biztonságos hozzáférés egy kulcstartóhoz](../general/security-overview.md)
- [Azure Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Key Vault biztonsági áttekintése](../general/security-overview.md)
- [Hitelesítés Key Vault](../general/authentication.md)
