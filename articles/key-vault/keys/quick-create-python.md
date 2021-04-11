---
title: Gyors útmutató – Azure Key Vault Python ügyféloldali kódtár – kulcsok kezelése
description: Megtudhatja, hogyan hozhat létre, kérhet le és törölhet kulcsokat egy Azure Key vaultból a Python ügyféloldali kódtár használatával
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/03/2020
ms.service: key-vault
ms.subservice: keys
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: b2c69b4358b99bfd4fff2a3434ff8fe956a2a1f5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2021
ms.locfileid: "104801162"
---
# <a name="quickstart-azure-key-vault-keys-client-library-for-python"></a>Gyors útmutató: Azure Key Vault kulcsok ügyféloldali kódtára a Pythonhoz

Ismerkedés a Azure Key Vault a Pythonhoz készült ügyféloldali kódtáraval. Az alábbi lépéseket követve telepítse a csomagot, és próbálja ki az alapszintű feladatokhoz tartozó kódot. A titkosítási kulcsok tárolásához Key Vault használatával elkerülhető az ilyen kulcsok tárolása a kódban, ami növeli az alkalmazás biztonságát.

[API-referenciák dokumentációja](/python/api/overview/azure/keyvault-keys-readme)  |  [Könyvtár forráskódja](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/keyvault/azure-keyvault-keys)  |  [Csomag (Python-csomag indexe)](https://pypi.org/project/azure-keyvault-keys/)

## <a name="prerequisites"></a>Előfeltételek

- Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python 2.7 + vagy 3.6 +](/azure/developer/python/configure-local-development-environment)
- [Azure CLI](/cli/azure/install-azure-cli)

Ez a rövid útmutató azt feltételezi, hogy az [Azure CLI](/cli/azure/install-azure-cli) -t egy Linux-terminál ablakban futtatja.

## <a name="set-up-your-local-environment"></a>A helyi környezet beállítása

Ez a rövid útmutató az Azure Identity Library és az Azure CLI használatával hitelesíti a felhasználókat az Azure-szolgáltatásokban. A fejlesztők a Visual studiót vagy a Visual Studio Code-ot is használhatják a hívások hitelesítéséhez. További információ: [az ügyfél hitelesítése az Azure Identity Client Library](/python/api/overview/azure/identity-readme)használatával.

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

1. Futtassa a következő parancsot: `login`.

    ```azurecli-interactive
    az login
    ```

    Ha a parancssori felület megnyithatja az alapértelmezett böngészőt, akkor az egy Azure-beli bejelentkezési oldal betöltésével végezhető el.

    Ellenkező esetben nyisson meg egy böngészőt, [https://aka.ms/devicelogin](https://aka.ms/devicelogin) és adja meg a terminálon megjelenő engedélyezési kódot.

2. A böngészőben jelentkezzen be fiókja hitelesítő adataival.

### <a name="install-the-packages"></a>A csomagok telepítése

1. Egy terminálon vagy parancssorban hozzon létre egy megfelelő Project-mappát, majd hozzon létre és aktiváljan egy Python virtuális környezetet a [Python virtuális környezetek használata](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)című témakörben leírtak szerint.

1. Az Azure Active Directory Identity Library telepítése:

    ```terminal
    pip install azure.identity
    ```


1. Telepítse a Key Vault kulcs ügyféloldali függvénytárát:

    ```terminal
    pip install azure-keyvault-keys
    ```

### <a name="create-a-resource-group-and-key-vault"></a>Erőforráscsoport és kulcstartó létrehozása

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-python-qs-rg-kv-creation.md)]

### <a name="grant-access-to-your-key-vault"></a>Hozzáférés biztosítása a kulcstartóhoz

Hozzon létre egy olyan hozzáférési szabályzatot a kulcstartó számára, amely titkos jogosultságot biztosít a felhasználói fiókjához.

```console
az keyvault set-policy --name <YourKeyVaultName> --upn user@domain.com --secret-permissions delete get list set
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
```cmd
export KEY_VAULT_NAME=<your-key-vault-name>
```

## <a name="create-the-sample-code"></a>A mintakód létrehozása

A Azure Key Vault a Pythonhoz készült ügyféloldali kódtár lehetővé teszi a titkosítási kulcsok kezelését. Az alábbi mintakód bemutatja, hogyan hozhat létre egy ügyfelet, hogyan állíthat be kulcsot, kérhet le egy kulcsot, és törölhet egy kulcsot.

Hozzon létre egy *kv_keys.* -es nevű fájlt, amely tartalmazza ezt a kódot.

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

Győződjön meg arról, hogy az előző szakaszban található kód egy *kv_keys.* a. file nevű fájlban van. Ezután futtassa a kódot a következő paranccsal:

```terminal
python kv_keys.py
```

- Ha az engedélyek hibába ütközik, győződjön meg arról, hogy a [ `az keyvault set-policy` parancsot](#grant-access-to-your-key-vault)futtatta.
- Ha újra futtatja a kódot ugyanazzal a kulcsnévvel, a következő hibaüzenet jelenhet meg: "(ütközés) a kulcs <name> jelenleg törölve, de helyreállítható állapotban van." Használjon másik kulcsnévt.

## <a name="code-details"></a>Kód részletei

### <a name="authenticate-and-create-a-client"></a>Ügyfél hitelesítése és létrehozása

Ebben a rövid útmutatóban a bejelentkezett felhasználó a Key Vault hitelesítésére szolgál, amely a helyi fejlesztés előnyben részesített módszere. Az Azure-ban üzembe helyezett alkalmazások esetében a felügyelt identitást App Service vagy virtuális géphez kell rendelni, további információért lásd: a [felügyelt identitás áttekintése](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

Az alábbi példában a kulcstartó neve a Key Vault URI-ra van kibontva, a "https:// \<your-key-vault-name\> . Vault.Azure.net" formátumban. Ez a példa a  ["DefaultAzureCredential ()"](/python/api/azure-identity/azure.identity.defaultazurecredential) osztályt használja, amely lehetővé teszi, hogy ugyanazt a kódot használja különböző környezetekben különböző beállításokkal az identitás biztosításához. További információ: az Azure-beli [hitelesítő adatok alapértelmezett hitelesítése](https://docs.microsoft.com/python/api/overview/azure/identity-readme). 


```python
credential = DefaultAzureCredential()
client = KeyClient(vault_url=KVUri, credential=credential)
```

## <a name="save-a-key"></a>Kulcs mentése

Miután megszerezte a Key Vault ügyfél-objektumát, a [create_rsa_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-rsa-key-name----kwargs-) metódus használatával tárolhat egy kulcsot: 

```python
rsa_key = client.create_rsa_key(keyName, size=2048)
```

Használhatja [create_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-key-name--key-type----kwargs-) vagy [create_ec_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#create-ec-key-name----kwargs-)is.

A metódus meghívása a `create` kulcstartó Azure REST API hívását hívja elő.

A kérelem kezelésekor az Azure hitelesíti a hívó identitását (az egyszerű szolgáltatásnév) az ügyfél számára megadott hitelesítőadat-objektum használatával.

## <a name="retrieve-a-key"></a>Kulcs lekérése

Key Vaultból származó kulcs beolvasásához használja a [get_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#get-key-name--version-none----kwargs-) metódust:

```python
retrieved_key = client.get_key(keyName)
 ```

Azt is ellenőrizheti, hogy a kulcsot beállította-e az Azure CLI-parancs az [kulcstartó Key show](/cli/azure/keyvault/key?#az-keyvault-key-show)paranccsal.

### <a name="delete-a-key"></a>Kulcs törlése

A kulcsok törléséhez használja a [begin_delete_key](/python/api/azure-keyvault-keys/azure.keyvault.keys.keyclient?#begin-delete-key-name----kwargs-) metódust:

```python
poller = client.begin_delete_key(keyName)
deleted_key = poller.result()
```

A `begin_delete_key` metódus aszinkron, és egy Poller objektumot ad vissza. A Poller metódusának meghívása `result` megvárja a befejezését.

Ellenőrizze, hogy a kulcs törölve lett-e az Azure CLI-parancs az [kulcstartó Key show](/cli/azure/keyvault/key?#az-keyvault-key-show)használatával.

A törlés után a kulcsok törölve maradnak, de a helyreállítható állapot egy ideig. Ha újra futtatja a kódot, adjon meg egy másik kulcsot.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha a [tanúsítványokkal](../certificates/quick-create-python.md) és a [titkokkal](../secrets/quick-create-python.md)is kísérletezni szeretne, a cikkben létrehozott Key Vault is felhasználhatja.

Ellenkező esetben, ha elkészült az ebben a cikkben létrehozott erőforrásokkal, a következő paranccsal törölheti az erőforráscsoportot és a benne foglalt összes erőforrást:

```azurecli
az group delete --resource-group KeyVault-PythonQS-rg
```

## <a name="next-steps"></a>Következő lépések

- [Az Azure Key Vault áttekintése](../general/overview.md)
- [Biztonságos hozzáférés egy kulcstartóhoz](../general/secure-your-key-vault.md)
- [Azure Key Vault fejlesztői útmutató](../general/developers-guide.md)
- [Key Vault biztonsági áttekintés](../general/security-overview.md)
- [Hitelesítés Key Vault](../general/authentication.md)
