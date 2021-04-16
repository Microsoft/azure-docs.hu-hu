---
title: Rövid útmutató – Azure Key Vault Python ügyféloldali kódtárának létrehozása – titkos kulcsok kezelése
description: Megtudhatja, hogyan hozhat létre, olvashat be és törölhet titkos kódokat egy Azure-kulcstartóból a Python ügyféloldali kódtárával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: secrets
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurepowershell
ms.openlocfilehash: e9e27720c50de8653fa859e051c9cfb3af4509ff
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2021
ms.locfileid: "107377471"
---
# <a name="quickstart-azure-key-vault-secret-client-library-for-python"></a>Rövid útmutató: Azure Key Vault Python titkos ügyféloldali kódtárának létrehozása

A Pythonhoz Azure Key Vault titkos ügyféloldali kódtár első lépések. A csomag telepítéséhez kövesse az alábbi lépéseket, és próbálja ki az alapszintű feladatokhoz szükséges példakódot. A titkos Key Vault használatával elkerülheti, hogy titkos kódokat tároljon a kódban, ami növeli az alkalmazás biztonságát.

[API-referenciadokumentáció](/python/api/overview/azure/keyvault-secrets-readme)  |  [Kódtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-secrets)  |  [Csomag (Python-csomagindex)](https://pypi.org/project/azure-keyvault-secrets/)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyenesen.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Python 2.7+ vagy 3.6+](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Ez a rövid útmutató azt feltételezi, hogy [az Azure CLI-t](/cli/azure/install-azure-cli) egy Linux terminálablakban futtatja.


## <a name="set-up-your-local-environment"></a>A helyi környezet beállítása
Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual Studio vagy Visual Studio Code használatával is hitelesítik a hívásokat. További információ: Az ügyfél hitelesítése az [Azure Identity ügyféloldali kódtárával.](/python/api/overview/azure/identity-readme)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a CLI meg tudja nyitni az alapértelmezett böngészőt, akkor ezt fogja megtenni, és betölt egy Azure bejelentkezési oldalt.

    Ellenkező esetben nyisson meg egy böngészőoldalt a oldalon, és írja be a terminálban [https://aka.ms/devicelogin](https://aka.ms/devicelogin) megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="install-the-packages"></a>A csomagok telepítése

1. Egy terminálban vagy parancssorban hozzon létre egy megfelelő projektmappát, majd hozzon létre és aktivál egy Python virtuális környezetet a Python virtuális [környezetek használatával kapcsolatos leírás szerint.](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Telepítse a Azure Active Directory-identitástárat:

    ```terminal
    pip install azure-identity
    ```


1. Telepítse a Key Vault kódtárat:

    ```terminal
    pip install azure-keyvault-secrets
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

```bash
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>A mintakód létrehozása

A Azure Key Vault Python titkos ügyféloldali kódtára lehetővé teszi a titkos kulcsok kezelését. A következő kódminta bemutatja, hogyan hozhat létre ügyfelet, állíthat be titkos kódot, hogyan lehet titkos kódot lekérni és titkos kódot törölni.

Hozzon létre egy *kv_secrets.py nevű fájlt,* amely tartalmazza ezt a kódot.

```python
import os
import cmd
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = os.environ["KEY_VAULT_NAME"]
KVUri = f"https://{keyVaultName}.vault.azure.net"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)

secretName = input("Input a name for your secret > ")
secretValue = input("Input a value for your secret > ")

print(f"Creating a secret in {keyVaultName} called '{secretName}' with the value '{secretValue}' ...")

client.set_secret(secretName, secretValue)

print(" done.")

print(f"Retrieving your secret from {keyVaultName}.")

retrieved_secret = client.get_secret(secretName)

print(f"Your secret is '{retrieved_secret.value}'.")
print(f"Deleting your secret from {keyVaultName} ...")

poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()

print(" done.")
```

## <a name="run-the-code"></a>A kód futtatása

Győződjön meg arról, hogy az előző szakaszban található kód egy *kv_secrets.py nevű fájlban található.* Ezután futtassa a kódot a következő paranccsal:

```terminal
python kv_secrets.py
```

- Ha engedélyekkel kapcsolatos hibákba ütközik, ellenőrizze, hogy futtatta-e a [ `az keyvault set-policy` parancsot.](#grant-access-to-your-key-vault)
- A kód ugyanazon titkoskód-névvel való újrafuttatása a következő hibaüzenetet okozhatja: "(Conflict) Secret is currently in adeleted but recoverable state" (A titkos kód jelenleg törölt, de <name> helyreállítható állapotban van). Használjon másik titkos nevet.

## <a name="code-details"></a>Kód részletei

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó hitelesítést használ a key vaultban, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében felügyelt identitást kell hozzárendelni egy App Service virtuális géphez. További információ: Felügyelt identitás [áttekintése.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

Az alábbi példában a kulcstartó neve ki van bontva a kulcstartó URI-nevére, "https:// \<your-key-vault-name\> .vault.azure.net" formátumban. Ebben a példában a  ["DefaultAzureCredential()"](https://docs.microsoft.com/python/api/azure-identity/azure.identity.defaultazurecredential) osztályt használjuk, amely lehetővé teszi, hogy ugyanazt a kódot különböző környezetekben használja, különböző identitásbeállításokkal. További információ: Alapértelmezett [Azure-beli hitelesítőadat-hitelesítés.](https://docs.microsoft.com/python/api/overview/azure/identity-readme) 

```python
credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
```

### <a name="save-a-secret"></a>Titkos gombra

Miután beszerezta a kulcstartó ügyfélobjektumát, a következő metódussal tárolhat [titkos set_secret:](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#set-secret-name--value----kwargs-) 

```python
client.set_secret(secretName, secretValue)
```

A `set_secret` hívása hívást hoz létre az Azure REST API a kulcstartóhoz.

A kérés kezelésekor az Azure hitelesíti a hívó identitását (az egyszerű szolgáltatást) az ügyfélnek megadott hitelesítő objektum használatával.

### <a name="retrieve-a-secret"></a>Titkos adat lekérése

Titkos Key Vault a következő [get_secret:](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#get-secret-name--version-none----kwargs-)

```python
retrieved_secret = client.get_secret(secretName)
 ```

A titkos értéket a `retrieved_secret.value` tartalmazza.

A titkos adatokat az [az keyvault secret show Azure CLI-paranccsal is lekérheti.](/cli/azure/keyvault/secret?#az-keyvault-secret-show)

### <a name="delete-a-secret"></a>Titkos kulcs törlése

Titkos adatok törléséhez használja a [begin_delete_secret](/python/api/azure-keyvault-secrets/azure.keyvault.secrets.secretclient?#begin-delete-secret-name----kwargs-) metódust:

```python
poller = client.begin_delete_secret(secretName)
deleted_secret = poller.result()
```

A `begin_delete_secret` metódus aszinkron, és egy poller objektumot ad vissza. A poller metódusának `result` hívása megvárja annak befejezését.

A titkos kulcs el lett távolítva az [az keyvault secret show Azure CLI-paranccsal.](/cli/azure/keyvault/secret?#az-keyvault-secret-show)

A törlés után a titkos adatok egy ideig törölt, de helyreállítható állapotban maradnak. Ha újra futtatja a kódot, használjon másik titkos nevet.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha tanúsítványokkal és [](../certificates/quick-create-python.md) kulcsokkal is kísérletezni [szeretne,](../keys/quick-create-python.md)újra felhasználhatja Key Vault cikkben létrehozott tanúsítványokat.

Ellenkező esetben, ha végzett a cikkben létrehozott erőforrásokkal, a következő paranccsal törölheti az erőforráscsoportot és annak összes tartalmazott erőforrását:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure Key Vault áttekintése](../general/overview.md)
- [Biztonságos hozzáférés egy kulcstartóhoz](../general/secure-your-key-vault.md)
- [Azure Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Key Vault biztonsági áttekintése](../general/security-overview.md)
- [Hitelesítés Key Vault](../general/authentication.md)
